# IDM Web – Configuration Reference

---

## 1. Web.config Settings

Location: `IDM_Web/Web.config`

---

### 1.1 Connection Strings

| Name | Description |
|------|-------------|
| `IDM_DBEntities` | Entity Framework connection to SQL Server IDM_DB |

```xml
<connectionStrings>
  <add name="IDM_DBEntities"
       connectionString="metadata=...;provider connection string=&quot;
       data source={SQL_SERVER};
       initial catalog=IDM_DB;
       integrated security=True;
       MultipleActiveResultSets=True&quot;"
       providerName="System.Data.EntityClient" />
</connectionStrings>
```

Replace `{SQL_SERVER}` with your server instance, e.g. `localhost` or `192.168.1.10\SQLEXPRESS`.

---

### 1.2 Application Settings

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| `webpages:Version` | string | `2.0.0.0` | ASP.NET Web Pages version |
| `PreserveLoginUrl` | bool | `true` | Preserve login URL after auth redirect |
| `ClientValidationEnabled` | bool | `true` | Enable client-side model validation |
| `UnobtrusiveJavaScriptEnabled` | bool | `true` | Enable unobtrusive jQuery validation |
| `DebugModeEnabled` | bool | `True` | Toggle debug/development mode |
| `DebugModeLoginUser` | string | — | Windows account to use in debug (e.g., `JP-q\9004074957`) |
| `DebugModeGCConnectionString` | string | — | AD Global Catalog connection string (e.g., `GC://jpjdcads108/DC=sony,DC=com`) |
| `DebugModeLDAPConnectionString` | string | — | AD LDAP connection string (e.g., `LDAP://jpjdcads108/DC=jp,DC=sony,DC=com`) |
| `DebugModeAccountOperatorUser` | string | — | AD service account for operations (e.g., `JP\9004014233-a`) |
| `MaintenanceMode` | string | `Admin` | Maintenance mode level (see below) |

---

### 1.3 MaintenanceMode Values

| Value | Effect |
|-------|--------|
| `""` (empty) | All users can access IDM Web normally |
| `"Admin"` | Only system admins can access; others see maintenance screen |
| Any other string | Displayed as maintenance message to all users |

---

### 1.4 Authentication Configuration

```xml
<authentication mode="Windows" />
<authorization>
  <deny users="?" />
</authorization>
```

- **Mode**: Windows Integrated Authentication
- **Anonymous access**: Denied globally
- All users must be authenticated via corporate AD before accessing any page

---

### 1.5 Compilation Settings

```xml
<compilation debug="true" strict="false" explicit="true" targetFramework="4.7.2">
```

| Setting | Value | Description |
|---------|-------|-------------|
| `debug` | `true` (Debug) / `false` (Release) | Changed via Web transform files |
| `strict` | `false` | VB.NET strict mode off |
| `explicit` | `true` | VB.NET explicit variable declaration on |
| `targetFramework` | `4.7.2` | .NET Framework version |

---

### 1.6 Session Configuration

Session state is configured in `Web.config`:
```xml
<sessionState timeout="30" />
```

Session timeout: **30 minutes** of inactivity.

Key session objects:
| Session Key | Type | Contents |
|-------------|------|---------|
| `UserInfo` | `UserInfo` | Logged-in user's account and mail info |
| `ManageCompanyCodeList` | `List(Of String)` | Companies this admin can manage |

---

## 2. Environment-Specific Configuration

### 2.1 Web.Debug.config (Development Overrides)

Applied when: `debug="true"` build (Visual Studio F5)

Typical overrides:
- `compilation debug="true"` — ensures debug symbols
- Connection string to local/dev SQL Server
- Debug mode enabled, test user configured

### 2.2 Web.Release.config (Production Overrides)

Applied when: publishing in Release mode

Typical overrides:
- `compilation debug="false"` — removes debug symbols
- Connection string to production SQL Server
- `DebugModeEnabled = False`
- `MaintenanceMode = ""` (or appropriate value)

---

## 3. Database Configuration (IDM_DB)

### 3.1 System Configuration via Define_MST

All runtime configuration is stored in `Define_MST` table and accessed through `Define_MSTAccessor` (with in-memory caching).

The `Define_MST` table contains key-value pairs for:
- Mail domain options per company
- Mailbox size tiers (Standard, Large, Enterprise labels)
- AD Organizational Unit paths
- Approval routing rules
- Company code → display name mappings
- System behavior flags

Example query:
```vbnet
Dim mailDomains = Define_MSTAccessor.GetValueList("MAIL_DOMAIN_{CompanyCode}")
```

### 3.2 Company Master (Company_MST)

Stores Sony Japan group company codes and display names. Used to populate company dropdowns and restrict admin scope.

### 3.3 OU Master (OU_MST)

Stores valid AD Organizational Unit paths. Used for computer account and group OU selection.

---

## 4. Active Directory Configuration

AD connections are configured per environment via `Web.config` AppSettings:

| Setting | Description | Example |
|---------|-------------|---------|
| `DebugModeGCConnectionString` | Global Catalog for read operations | `GC://dc-server/DC=domain,DC=com` |
| `DebugModeLDAPConnectionString` | LDAP for write operations in dev | `LDAP://dc-server/DC=jp,DC=domain,DC=com` |
| `DebugModeAccountOperatorUser` | Service account for AD operations in dev | `DOMAIN\svc-account` |

Production AD settings are stored separately (not in Debug-prefixed keys) and applied via `Web.Release.config` transforms.

> The `ADAccessor.vb` class reads these settings to establish `DirectoryEntry` connections.

---

## 5. Bundling Configuration

Bundles defined in `App_Start/BundleConfig.vb`:

| Bundle Path | Contents | Used For |
|-------------|----------|---------|
| `~/bundles/jquery` | `jquery-{version}.js` | Base jQuery |
| `~/bundles/jqueryui` | jQuery UI files | Datepickers, autocomplete |
| `~/bundles/jqueryval` | jQuery Validation + Unobtrusive | Form validation |
| `~/bundles/knockout` | `knockout-{version}.js` | KnockoutJS MVVM |
| `~/Content/css` | `Site.css` and other CSS | Application styles |

In `Release` mode, bundles are minified automatically.

---

## 6. Routing Configuration

Default route in `App_Start/RouteConfig.vb`:

```vbnet
routes.MapRoute(
    name:="Default",
    url:="{controller}/{action}/{id}",
    defaults:=New With {.controller = "Home", .action = "Index", .id = UrlParameter.Optional}
)
```

- **Default controller**: `Home`
- **Default action**: `Index`
- All non-API routes follow `/{Controller}/{Action}/{id?}` pattern

Web API route in `App_Start/WebApiConfig.vb`:

```vbnet
config.Routes.MapHttpRoute(
    name:="DefaultApi",
    routeTemplate:="api/{controller}/{id}",
    defaults:=New With {.id = RouteParameter.Optional}
)
```

---

## 7. Security Configuration

| Setting | Value | Notes |
|---------|-------|-------|
| Authentication | Windows Integrated | Corporate AD required |
| Authorization | Deny anonymous | All requests must be authenticated |
| Session | 30 min timeout | Configurable in Web.config |
| HTTPS | Configured at IIS/load balancer level | Not in Web.config |

---

## Related Documentation

- [SETUP.md](./SETUP.md) - Installing and running the application
- [ARCHITECTURE.md](./ARCHITECTURE.md) - How configuration is loaded
- [TROUBLESHOOTING.md](./TROUBLESHOOTING.md) - Configuration-related errors

---

*End of document*
