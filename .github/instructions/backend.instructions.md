---
applyTo: "backend/**/*"
---

# Backend Copilot Instructions — TAP API v2 (.NET 10 Isolated Azure Functions)

## Stack Overview

| Aspect | Value |
|--------|-------|
| **Runtime** | .NET 10 (`net10.0`) |
| **Azure Functions Version** | v4 |
| **Worker Model** | **Isolated Process** (not In-process) |
| **Output Type** | Executable (`.exe`) |
| **Logging** | Serilog → JSON console → Application Insights |
| **Serialization** | `System.Text.Json` for HTTP responses |
| **Auth** | JWT / Entra ID via middleware; Easy Auth at platform level |

---

## 1. Naming Conventions

| Element | Convention | Example |
|---------|-----------|---------|
| **Azure Function methods** | PascalCase | `GetTargetById`, `CreateNomination` |
| **Classes / interfaces** | PascalCase | `TapService`, `ITapService`, `TargetFunctions` |
| **Namespaces** | PascalCase, dot-separated | `TapApi.Services.Tap`, `TapApi.Infrastructure.Repositories` |
| **C# properties** | PascalCase | `public string UserId { get; set; }` |
| **JSON / URL fields** | `snake_case` (serialized) | `user_id`, `created_at`, `is_new_grad` |
| **Route parameters** | `snake_case` (URL segment) | `/v2/targets/{target_id}` |
| **Query parameters** | `snake_case` | `?limit=100&offset=0&is_new_grad=true` |
| **Service interfaces** | `I[Name]Service` | `ITapService`, `IGraphService` |
| **Repository interfaces** | `I[Name]Repository` | `IUnifiedTapRepository` |
| **Middleware classes** | `[Name]Middleware` | `RateLimitingMiddleware`, `CorrelationIdMiddleware` |
| **Database columns** | `snake_case` | `user_id`, `tap_issued_at` |
| **Private fields** | `_camelCase` | `_repository`, `_logger` |
| **Constants** | `PascalCase` (static readonly) | `JsonOptions`, `MaxPageSize` |

---

## 2. Project Layer Responsibilities

```
backend/src/
├── TapApi.Core/           # Domain models, exceptions, interfaces — no external dependencies
├── TapApi.Infrastructure/ # Data access, EF Core, repositories, correlation, utilities
├── TapApi.Services/       # Business logic and orchestration — depends on Core + Infrastructure
├── TapApi.Functions/      # Azure Function endpoints + middleware + DI wiring (Program.cs)
├── Sdk.Scim/              # SCIM 2.0 protocol SDK (standalone)
└── Sdk.Scim.AzureTable/   # Azure Table Storage provider for SCIM
```

**Rules:**
- `TapApi.Core` must never reference `TapApi.Infrastructure` or `TapApi.Services`.
- Services implement interfaces declared in `TapApi.Core`.
- Functions depend only on service/repository interfaces, never on concrete implementations.

---

## 3. Azure Function Trigger Standards

### 3.1 HTTP Trigger Rules

- **Authorization level** is always `AuthorizationLevel.Anonymous`; authentication is enforced by middleware and Easy Auth.
- **Route prefix** is `api` (set in `host.json`); omit the prefix when declaring routes — use `Route = "v2/..."`.
- Function class must inherit `TapApiFunctionBase` (defined in `ResponseHelper.cs`).
- Use `ExecuteAsync(req, async () => { ... })` to automatically handle `DomainException` and unhandled exceptions.
- Check `RequireRole(context, req)` first; return its result immediately if non-null.
- Prefer using the `ResponseHelper` static methods for JSON responses.
- Returning raw `HttpResponseData` is acceptable for response patterns not covered by `ResponseHelper` (for example: `202 Accepted`, `204 NoContent`, or when you must set special headers).
- When using `ResponseHelper`, **always** pass `GetCorrelationId()`.

### 3.2 Supported HTTP Methods

Use only the verbs appropriate for RESTful semantics:
- `GET` — read (list or single item)
- `POST` — create
- `PUT` / `PATCH` — update
- `DELETE` — delete

### 3.3 Pagination

All list endpoints must support OData v4 pagination:
- Query params: `limit` (default 100, max 500) and `offset` (default 0).
- Response shape: `{ "value": [...], "@odata.nextLink": "..." }` via `ResponseHelper.ODataAsync`.

### 3.4 JSON Serialization

Define `JsonOptions` at function-class level (or use the shared `ResponseHelper.JsonOptions`):

```csharp
private static readonly JsonSerializerOptions JsonOptions = new()
{
    PropertyNamingPolicy = JsonNamingPolicy.SnakeCaseLower,
    DefaultIgnoreCondition = JsonIgnoreCondition.Never, // ALWAYS include null fields
    Converters = { new JsonStringEnumConverter() }      // Enums as PascalCase strings
};
```

> **Warning:** Never set `DefaultIgnoreCondition = WhenWritingNull` — it breaks API parity with the Python reference.

---

## 4. Dependency Injection Rules

Registered in `Program.cs` via three private static methods:

| Method | What to register |
|--------|-----------------|
| `RegisterInfrastructure` | Singletons: correlation provider, SCIM auth options, cursor codec |
| `RegisterRepositories` | Repository implementations; check connection strings before registering storage clients |
| `RegisterApplicationServices` | Business services, external clients, SCIM services |

**Lifetime guidelines:**

| Lifetime | Use for |
|----------|---------|
| `Singleton` | Stateless, thread-safe services: config, cache, codec, HTTP clients |
| `Scoped` | Per-request state: repositories, business services, Graph clients |
| `Transient` | Rarely used; lightweight disposable helpers |

**Factory pattern** for optional services (example pattern from `Program.cs`):

```csharp
services.AddSingleton<IConfigRepository>(sp =>
{
    var connectionString = sp.GetRequiredService<IConfiguration>()["AzureWebJobsStorage"];
    if (string.IsNullOrEmpty(connectionString))
    {
        Log.Warning("Storage not configured; persistence disabled");
        return null!;
    }
    return new AzureTableConfigRepository(connectionString, "Configurations");
});
```

**Always register by interface**, not concrete type (except where no interface exists):

```csharp
// Correct
services.AddScoped<ITapService, TapService>();

// Wrong
services.AddScoped<TapService>();
```

---

## 5. Middleware Stack

Middleware is registered in `ConfigureFunctionsWorkerDefaults` inside `Program.cs`. **Order matters:**

```
1. ScimAuthenticationMiddleware  — SCIM-specific auth verification
2. ScimErrorHandlingMiddleware   — Format SCIM errors (RFC 7644)
3. CorrelationIdMiddleware       — Attach/propagate X-Correlation-ID
4. RateLimitingMiddleware        — Token-bucket throttling (partition by user/anonymous)
```

To add new cross-cutting middleware, insert it AFTER `RateLimitingMiddleware` unless it must run earlier.

All middleware must implement `IFunctionsWorkerMiddleware`:

```csharp
public async Task Invoke(FunctionContext context, FunctionExecutionDelegate next)
{
    var httpReq = await context.GetHttpRequestDataAsync();
    if (httpReq == null) { await next(context); return; } // Skip non-HTTP triggers

    // ... logic ...

    await next(context); // Always call next unless short-circuiting
}
```

---

## 6. Error Handling

All domain errors must be thrown as `DomainException` (or a subclass from `TapApi.Core.Exceptions`).

`TapApiFunctionBase.ExecuteAsync` automatically maps:
- `DomainException` → structured JSON with `error_code` + HTTP status from the exception
- Any other `Exception` → HTTP 500 with correlation ID for tracing

**Never** `try/catch` inside a Function method and return raw error strings. Use `DomainException` and let the base class handle it.

---

## 7. Golden Template — New HTTP Trigger Function

Use this as the starting point for every new Azure Function endpoint:

```csharp
using System.Net;
using Microsoft.Azure.Functions.Worker;
using Microsoft.Azure.Functions.Worker.Http;
using Microsoft.Extensions.Logging;
using TapApi.Core.Exceptions;
using TapApi.Core.Interfaces.Services;
using TapApi.Infrastructure.Correlation;

namespace TapApi.Functions.Functions;

/// <summary>
/// Azure Functions HTTP endpoints for [Domain] operations.
///
/// Routes:
///   GET  /api/v2/[domain]            — list [domain] with optional filters
///   GET  /api/v2/[domain]/{id}       — get [domain] by id
///   POST /api/v2/[domain]            — create [domain]
///   PATCH /api/v2/[domain]/{id}      — update [domain]
///   DELETE /api/v2/[domain]/{id}     — delete [domain]
/// </summary>
public class [Domain]Functions : TapApiFunctionBase
{
    private readonly I[Domain]Service _[domain]Service;

    public [Domain]Functions(
        I[Domain]Service [domain]Service,
        ICorrelationIdProvider correlationIdProvider,
        ILogger<[Domain]Functions> logger)
        : base(correlationIdProvider, logger)
    {
        _[domain]Service = [domain]Service ?? throw new ArgumentNullException(nameof([domain]Service));
    }

    /// <summary>
    /// GET /api/v2/[domain]/{id}
    /// Retrieve a single [domain] resource by its unique identifier.
    /// </summary>
    [Function("Get[Domain]ById")]
    public async Task<HttpResponseData> Get[Domain]ById(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get",
            Route = "v2/[domain]/{id}")] HttpRequestData req,
        string id,
        FunctionContext context)
    {
        var deny = RequireRole(context, req);
        if (deny != null) return deny;

        return await ExecuteAsync(req, async () =>
        {
            var result = await _[domain]Service.GetByIdAsync(id);
            if (result == null)
                return await ResponseHelper.NotFoundAsync(req,
                    new { error = "[Domain] not found", id },
                    GetCorrelationId());

            return await ResponseHelper.OkAsync(req, result, GetCorrelationId());
        });
    }

    /// <summary>
    /// GET /api/v2/[domain]
    /// List [domain] resources with optional filtering and OData v4 pagination.
    /// Query params:
    ///   - limit: max results (default 100)
    ///   - offset: skip first N results (default 0)
    /// </summary>
    [Function("Get[Domain]List")]
    public async Task<HttpResponseData> Get[Domain]List(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get",
            Route = "v2/[domain]")] HttpRequestData req,
        FunctionContext context)
    {
        var deny = RequireRole(context, req);
        if (deny != null) return deny;

        return await ExecuteAsync(req, async () =>
        {
            var qs = System.Web.HttpUtility.ParseQueryString(req.Url.Query);
            var limit = int.TryParse(qs["limit"], out var l) ? l : 100;
            var offset = int.TryParse(qs["offset"], out var o) ? o : 0;

            var (items, nextLink) = await _[domain]Service.ListAsync(limit, offset);

            return await ResponseHelper.ODataAsync(req,
                new PagedResult<[Domain]>
                {
                    Items = items,
                    NextContinuationToken = nextLink
                },
                req.Url.GetLeftPart(System.UriPartial.Path),
                GetCorrelationId());
        });
    }

    /// <summary>
    /// POST /api/v2/[domain]
    /// Create a new [domain] resource.
    /// </summary>
    [Function("Create[Domain]")]
    public async Task<HttpResponseData> Create[Domain](
        [HttpTrigger(AuthorizationLevel.Anonymous, "post",
            Route = "v2/[domain]")] HttpRequestData req,
        FunctionContext context)
    {
        var deny = RequireRole(context, req);
        if (deny != null) return deny;

        return await ExecuteAsync(req, async () =>
        {
            var body = await req.ReadFromJsonAsync<Create[Domain]Request>();
            if (body == null)
                throw new DomainException(
                    "Request body is required",
                    "INVALID_REQUEST",
                    400);

            var created = await _[domain]Service.CreateAsync(body);
            return await ResponseHelper.CreatedAsync(req, created, GetCorrelationId());
        });
    }
}
```

**Checklist before committing a new Function file:**

- [ ] Class inherits `TapApiFunctionBase`
- [ ] All injected dependencies use interfaces
- [ ] Every method calls `RequireRole` and `ExecuteAsync`
- [ ] `GetCorrelationId()` passed to all `ResponseHelper` calls
- [ ] `JsonOptions` uses `DefaultIgnoreCondition.Never`
- [ ] Route uses `snake_case` segments, no `api/` prefix
- [ ] XML `<summary>` doc on the class and every public method
- [ ] Registered in `Program.cs` → `RegisterApplicationServices`

---

## 8. Testing Conventions

- Unit tests in `backend/tests/TapApi.Tests.Unit/`
- Integration tests in `backend/tests/TapApi.Tests.Integration/`
- Test class naming: `[ClassName]Tests`
- Use `xUnit` + `Moq` (or `NSubstitute` if already used in the project)
- Mock all external dependencies; never hit real Azure services in unit tests
- Aim for ≥ 85% code coverage on service and function layers

---

## 9. Key Configuration Files

| File | Purpose |
|------|---------|
| `backend/src/TapApi.Functions/Program.cs` | DI registration and host configuration |
| `backend/src/TapApi.Functions/host.json` | Functions runtime (route prefix, log levels) |
| `backend/src/TapApi.Functions/appsettings.json` | Application config (DB, logging) |
| `backend/src/TapApi.Functions/local.settings.json` | Local dev secrets (not committed) |
