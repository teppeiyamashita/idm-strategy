# IDM Web – Contribution Guide

---

## Getting Started

Before contributing, set up your development environment by following [SETUP.md](./SETUP.md).

---

## Branching Strategy

| Branch | Purpose |
|--------|---------|
| `main` / `master` | Production-ready code |
| `develop` | Integration branch for features |
| `feature/{description}` | New feature development |
| `bugfix/{description}` | Bug fix branches |
| `hotfix/{description}` | Urgent production fixes |

```bash
# Create a feature branch
git checkout develop
git pull origin develop
git checkout -b feature/add-bulk-group-import

# When done, push and open PR to develop
git push origin feature/add-bulk-group-import
```

---

## Code Style Guidelines

### VB.NET Conventions

Follow existing patterns in the codebase:

**1. Naming**
```vbnet
' PascalCase for Public functions and Properties
Public Function GetUserInfo(accountName As String) As UserInfo

' camelCase for local variables
Dim userInfo = New UserInfo()

' _camelCase or m_camelCase for private fields (choose one per file)
Private _dbAccessor As IDM_DBAccessor
```

**2. Disposal Pattern**
Classes that use `IDM_DBAccessor` or `DirectoryEntry` should implement `IDisposable`:
```vbnet
Public Class UserService
    Implements IDisposable

    Private _accessor As New IDM_DBAccessor()

    Protected Overridable Sub Dispose(disposing As Boolean)
        If disposing Then
            _accessor?.Dispose()
        End If
    End Sub

    Public Sub Dispose() Implements IDisposable.Dispose
        Dispose(True)
        GC.SuppressFinalize(Me)
    End Sub
End Class
```

**3. Error Handling**
Use structured error handling with meaningful messages:
```vbnet
Try
    ADAccessor.CreateGroup(groupName)
Catch ex As DirectoryServicesCOMException
    Logger.Error("AD group creation failed", ex)
    Throw New ApplicationException($"Failed to create group '{groupName}': {ex.Message}", ex)
End Try
```

**4. Constants vs Magic Strings**
Use `Constants.FunctionID` instead of hardcoded strings:
```vbnet
' Good
Dim functionId = FunctionID.CreateInternalGroup   ' = "030A10"

' Bad
Dim functionId = "030A10"
```

---

## Layer Responsibilities

Follow the existing layered architecture when adding features:

| Layer | Responsibility | Must NOT |
|-------|---------------|---------|
| Controller | Receive HTTP, build ViewModel, call Service, return View | Contain business logic |
| Service | Business rules, data orchestration, workflow logic | Access DB directly (use Accessor) |
| DataAccess | DB queries, AD operations | Contain business logic |
| ViewModel | UI data container | Contain logic |
| Model | Domain object | Know about UI |

---

## Adding a New Feature

### Checklist

- [ ] Add `FunctionID` constant in `Constants/Constants.vb` (if new request type)
- [ ] Add/update Model in `Models/` (if new domain entity)
- [ ] Add ViewModel in `ViewModels/` (for the UI form)
- [ ] Add Validator in `Validators/` (if new validation rule needed)
- [ ] Add Service method in `Services/{Area}Service.vb`
- [ ] Add DataAccess method in `DataAccess/{Entity}Accessor.vb`
- [ ] Add Controller action(s) in `Controllers/{Area}Controller.vb`
- [ ] Add View(s) in `Views/{ControllerName}/{ActionName}.vbhtml`
- [ ] Add Bundle registration in `BundleConfig.vb` (if new JS/CSS)
- [ ] Add unit tests in `IDM_Web.Test/`
- [ ] Update [FEATURES.md](./FEATURES.md) with the new capability

---

## Adding a New Controller

```vbnet
' IDM_Web/Controllers/MyFeatureController.vb
Public Class MyFeatureController
    Inherits Controller

    Private ReadOnly _service As New MyFeatureService()

    ' GET /MyFeature/Create
    Function Create() As ActionResult
        Dim viewModel = _service.GetCreateViewModel()
        Return View(viewModel)
    End Function

    ' POST /MyFeature/Create
    <HttpPost>
    Function Create(viewModel As MyFeatureViewModel) As ActionResult
        If Not ModelState.IsValid Then
            Return View(viewModel)
        End If
        _service.SubmitCreateRequest(viewModel)
        Return RedirectToAction("Complete", "Shared")
    End Function

End Class
```

---

## Adding a New View

Views use Razor syntax with `.vbhtml` extension:

```vbhtml
@* IDM_Web/Views/MyFeature/Create.vbhtml *@
@ModelType IDM_Web.ViewModels.MyFeatureViewModel

@Code
    ViewBag.Title = "Create My Feature"
    Layout = "~/Views/Shared/_Layout.vbhtml"
End Code

<h2>Create My Feature</h2>

@Using Html.BeginForm()
    @Html.AntiForgeryToken()
    @Html.ValidationSummary(True)

    @<div class="form-group">
        @Html.LabelFor(Function(m) m.Name)
        @Html.TextBoxFor(Function(m) m.Name, New With {.class = "form-control"})
        @Html.ValidationMessageFor(Function(m) m.Name)
    </div>

    @<button type="submit" name="Query" value="OK" class="btn btn-primary">Submit</button>
    @<button type="submit" name="Query" value="Cancel" class="btn btn-default">Cancel</button>
End Using
```

---

## Multi-Button Form Pattern

IDM Web uses `AcceptParameterAttribute` for multi-button forms:

```vbnet
' Controller - different methods for different submit buttons
<AcceptParameter(Name:="Query", Value:="Search")>
<HttpPost>
Function Index(viewModel As SearchViewModel) As ActionResult
    ' Handle Search button click
End Function

<AcceptParameter(Name:="Query", Value:="Cancel")>
<HttpPost>
Function Index(viewModel As SearchViewModel) As ActionResult
    Return RedirectToAction("Index", "Home")
End Function
```

```vbhtml
@* View - buttons use name="Query" to trigger routing *@
<button type="submit" name="Query" value="Search">Search</button>
<button type="submit" name="Query" value="Cancel">Cancel</button>
```

---

## Writing Unit Tests

Test project: `IDM_Web.Test/`

```vbnet
' IDM_Web.Test/Services/UserServiceTest.vb
<TestFixture>
Public Class UserServiceTest

    <Test>
    Public Sub GetUserInfo_ValidAccount_ReturnsUserInfo()
        ' Arrange
        Dim service = New UserService()
        Dim accountName = "test-account"

        ' Act
        Dim result = service.GetUserInfo(accountName)

        ' Assert
        Assert.IsNotNull(result)
        Assert.AreEqual(accountName, result.AccountName)
    End Sub

End Class
```

---

## Pull Request Process

1. **Create PR** from your feature branch to `develop`
2. **Title format**: `[Feature/Bugfix] Brief description`
3. **Description should include**:
   - What changed and why
   - How to test
   - Any configuration changes required
4. **Checklist**:
   - [ ] Builds without errors
   - [ ] Unit tests pass
   - [ ] No hardcoded credentials
   - [ ] Configuration changes documented
   - [ ] FEATURES.md updated (if new feature)

---

## Commit Message Format

```
{type}: {short description}

{optional body}

{optional footer}
```

Examples:
```
feat: add bulk CSV import for security group members

fix: correct mail domain validation for sony.com addresses

refactor: extract ADAccessor password reset into helper method

docs: update SETUP.md with IIS Express Windows Auth steps
```

Types: `feat`, `fix`, `refactor`, `test`, `docs`, `chore`

---

## Security Best Practices

- **No credentials in code** — Use `Web.config` AppSettings for service accounts
- **No SQL injection** — Use Entity Framework queries, not raw SQL
- **CSRF protection** — Include `@Html.AntiForgeryToken()` in all forms and `[ValidateAntiForgeryToken]` on POST actions
- **Input validation** — Use ViewModel data annotations and custom Validators
- **Role checks** — Verify company scope restrictions in Service layer for multi-company admins

---

## Related Documentation

- [SETUP.md](./SETUP.md) - Development environment setup
- [ARCHITECTURE.md](./ARCHITECTURE.md) - System design and layers
- [PROJECT_STRUCTURE.md](./PROJECT_STRUCTURE.md) - Where to find and add files
- [TROUBLESHOOTING.md](./TROUBLESHOOTING.md) - Common development issues

---

*End of document*
