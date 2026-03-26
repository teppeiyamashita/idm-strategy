# IDM Web – Architecture Guide

---

## 1. System Overview

IDM Web follows the **ASP.NET MVC 4** architectural pattern with a layered design:

```
┌─────────────────────────────────────────────────────────────────┐
│                        Browser (User)                            │
└─────────────────────────────┬───────────────────────────────────┘
                              │ HTTP / Windows Auth
┌─────────────────────────────▼───────────────────────────────────┐
│                  ASP.NET MVC 4 (VB.NET)                          │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │                     Controllers                          │    │
│  │  HomeController │ UserController │ GroupController       │    │
│  │  AdminController │ RequestController │ ResourceController│    │
│  │  SharedMailboxController │ ComputerAccountController    │    │
│  └─────────────────────────────────────────────────────────┘    │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │                     Services                             │    │
│  │  UserService │ GroupService │ RequestService             │    │
│  │  AdminService │ SharedMailboxService │ ComputerService   │    │
│  │  CommonService │ SharedService │ HomeService             │    │
│  └─────────────────────────────────────────────────────────┘    │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │                    Data Access                           │    │
│  │  IDM_DBAccessor │ ADAccessor │ Account_MSTAccessor      │    │
│  │  SecurityGroup_MSTAccessor │ Define_MSTAccessor         │    │
│  │  ComputerAccount_MSTAccessor │ Request_LSTAccessor      │    │
│  └─────────────────────────────────────────────────────────┘    │
└──────────────┬──────────────────────────┬───────────────────────┘
               │                          │
┌──────────────▼─────────┐  ┌────────────▼───────────────────────┐
│    SQL Server (IDM_DB)  │  │  Active Directory (JP.SONY.COM)     │
│  - Account_MST          │  │  - User objects                     │
│  - SecurityGroup_MST    │  │  - Security groups                  │
│  - SharedMailbox_MST    │  │  - Shared accounts                  │
│  - Request_LST          │  │  - Computer accounts                │
│  - GHD_PersonalInfo_MST │  │  - Resource mailboxes               │
│  - EmployeeLeave_LST    │  └─────────────────────────────────────┘
│  - (100+ tables)        │
└─────────────────────────┘
```

---

## 2. Layer Descriptions

### 2.1 Presentation Layer (Views + ViewModels)

**Technology**: Razor MVC (`.vbhtml` templates), jQuery 3.2.1, KnockoutJS 3.4.2, jQuery UI 1.12.1

**Responsibilities**:
- Render HTML forms and result screens
- Client-side validation (jQuery Validation + Unobtrusive)
- Dynamic UI updates via KnockoutJS MVVM bindings
- AJAX calls for partial page refreshes

**Key ViewModels**:
| ViewModel | Purpose |
|-----------|---------|
| `UserViewModel` | User profile edit form (mail, display name, proxy addresses) |
| `GroupViewModel` | Group creation/edit with member list |
| `RequestListViewModel` | List of pending/completed requests |
| `RequestDetailViewModel` | Detailed request view with approval history |
| `ComputerViewModel` | Computer account creation/OU selection |
| `SharedMailboxViewModel` | Shared mailbox with member management |

**Views are organized** by controller under `IDM_Web/Views/{ControllerName}/`

---

### 2.2 Controller Layer

**Technology**: ASP.NET MVC 4 Controllers (VB.NET)

**Responsibilities**:
- Receive HTTP requests
- Load and validate ViewModels
- Delegate business logic to Services
- Return appropriate Views or redirects

**Authentication**: `[Authorize]` enforced globally via `FilterConfig.vb`. Windows identity is read from `HttpContext.User.Identity.Name`.

**Key Action Pattern**:
```vbnet
' GET: Display form
Function EditUser() As ActionResult
    Dim userInfo = UserService.GetUserInfo(userId)
    Dim viewModel = New UserViewModel(userInfo)
    Return View(viewModel)
End Function

' POST: Submit changes
<HttpPost>
Function EditUser(viewModel As UserViewModel) As ActionResult
    If ModelState.IsValid Then
        UserService.SaveRequest(viewModel)
        Return RedirectToAction("Complete")
    End If
    Return View(viewModel)
End Function
```

**Multi-button Forms** are handled via `AcceptParameterAttribute` (custom extension):
```vbnet
<AcceptParameter(Name:="Query", Value:="Search")>
Function SearchUser(model As SearchModel) As ActionResult
    ...
End Function
```

---

### 2.3 Service Layer

**Technology**: VB.NET classes in `IDM_Web/Services/`

**Responsibilities**:
- Implement business rules
- Coordinate data access
- Build and validate domain models
- Write requests to `Request_LST`

**Key Services**:

| Service | Key Responsibilities |
|---------|---------------------|
| `UserService` | User info retrieval, mail alias management, leave processing |
| `GroupService` | Group creation, member management, expiration logic |
| `RequestService` | Request tracking, status transitions, approval routing |
| `AdminService` | Admin-scoped user/group/request operations |
| `SharedMailboxService` | Mailbox provisioning, bulk member operations |
| `ComputerService` | AD computer operations, OU management |
| `CommonService` | Reusable utilities: employment type conversion, ad DN utilities |
| `SharedService` | Dropdown list builders (companies, OUs, mail domains, mailbox sizes) |

---

### 2.4 Data Access Layer

**Technology**: Entity Framework 6.2.0 + custom Accessor classes

**Responsibilities**:
- Map SQL Server tables to .NET entities
- Execute LINQ queries against IDM_DB
- Abstract AD DirectoryServices operations
- Cache frequently read configuration data

**Pattern**: Unit of Work via `IDM_DBAccessor` which wraps `DbContext`

```vbnet
Class IDM_DBAccessor
    Private _context As IDM_DBEntities
    
    Sub New()
        _context = New IDM_DBEntities()
    End Sub
    
    Function GetUsers() As IQueryable(Of Account_MST)
        Return _context.Account_MST
    End Function
End Class
```

**Active Directory Access** via `ADAccessor.vb`:
- Uses `System.DirectoryServices` for LDAP operations
- Service account credentials from `Web.config`
- Supports: Create user, Modify attributes, Reset password, Move OU, Add/Remove group members

---

### 2.5 Database Layer (IDM_DB)

**Technology**: Microsoft SQL Server

**Core Tables**:

```
Identity Tables:
  Account_MST            User account master
  Account_TRN            Account operation history
  MailAddress_MST        Email addresses
  Mailbox_MST            Mailbox configuration
  
Security Group Tables:
  SecurityGroup_MST      Group master
  SecurityGroup_TRN      Group operation history
  SecurityGroupMember_TMP Group members (temporary working)
  SecurityGroupInfo_View Materialized group view

Shared Resource Tables:
  SharedMailbox_MST      Shared mailbox master
  SharedMailboxMember_TRN Mailbox member history
  ResourceMailbox_MST    Meeting room/equipment
  
Computer Tables:
  ComputerAccount_MST    Computer account master
  ComputerAccount_TRN    Computer operation history
  
Request/Workflow Tables:
  Request_LST            All submitted requests
  RequestID_MST          Request ID generator
  
GHD Integration Tables (read-only):
  GHD_PersonalInfo_MST   Employee personal data
  GHD_OrganizationInfo_MST Organization hierarchy
  GHD_PersonalORGInfo_MST Employee-organization relationships
  GHD_LinkStatus_MST     Link status tracking
  
Configuration Tables:
  Define_MST             System configuration key-value store
  Company_MST            Company master
  OU_MST                 AD Organizational Unit definitions
  InternalPermission_MST Internal group permissions
  ExternalPermission_MST External group permissions
```

---

## 3. Request Workflow Engine

All identity changes flow through a **Request → Approval → Execution** workflow:

```
User submits form
      │
      ▼
Request_LST created (Status: WaitingForApproval or Processing)
      │
      ├── Requires Approval?
      │       │ YES
      │       ▼
      │   Approver receives notification
      │       │
      │       ├── Approve → Status: Processing
      │       └── Reject  → Status: Rejected
      │
      ▼
ADAccessor executes operation
      │
      ├── Success → Status: Completed
      └── Error   → Status: Error
```

**Function IDs** track the type of request (defined in `Constants.vb`):
- `010C10` = EditUser
- `030A10` = CreateInternalGroup
- `021A10` = CreateSharedMailbox
- `050A10` = CreateComputer
- (25+ total function IDs)

---

## 4. Authentication & Authorization Flow

```
Browser Request
      │
      ▼
IIS (Windows Authentication)
      │  Kerberos / NTLM
      ▼
HttpContext.User.Identity (WindowsIdentity)
      │
      ▼
HomeController.Index()
      ├── Read ManageCompanyCodeList from session
      ├── Determine role:
      │       ├── System Admin (DebugModeLoginUser match / admin list)
      │       ├── Company Admin (ManageCompanyCodeList > 0)
      │       └── General User
      └── Render menu based on role
```

**Session Data**:
- `UserInfo` object cached in `Session("UserInfo")`
- `ManageCompanyCodeList` controls which companies an admin can manage
- `DebugModeEnabled` in `Web.config` allows test user override

---

## 5. Configuration Management

All system configuration is stored in `Define_MST` table and accessed via `Define_MSTAccessor` with in-memory caching. This includes:
- Mail domain lists
- Mailbox size options
- AD OU paths
- Approval routing rules

`Web.config` stores:
- Database connection string
- AD service account credentials
- Debug mode settings
- Maintenance mode flag

---

## 6. Bundling & Front-End

**Technology**: Microsoft.AspNet.Web.Optimization

Bundles configured in `BundleConfig.vb`:
- `~/bundles/jquery` – jQuery 3.2.1
- `~/bundles/jqueryui` – jQuery UI 1.12.1
- `~/bundles/jqueryval` – jQuery Validation
- `~/bundles/knockout` – KnockoutJS 3.4.2
- `~/Content/css` – Application CSS

**JavaScript Pattern**:
- KnockoutJS for reactive form elements (member lists, dynamic fields)
- jQuery/AJAX for partial page requests (`SharedController` helper calls)
- jQuery UI for datepickers, autocomplete

---

## 7. Error Handling

- Global error handling via `FilterConfig.vb`
- `ErrorViewModel` passed to Error views
- AD operation failures logged and surface as `Status: Error` in `Request_LST`
- `MaintenanceMode` in `Web.config` redirects non-admin users to maintenance page

---

## 8. Related Documentation

- [SYSTEM_CONTEXT.md](./SYSTEM_CONTEXT.md) - System purpose and scope
- [PROJECT_STRUCTURE.md](./PROJECT_STRUCTURE.md) - Folder and file organization
- [FEATURES.md](./FEATURES.md) - Full feature reference
- [SETUP.md](./SETUP.md) - Development setup guide

---

*End of document*
