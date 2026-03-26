# IDM Web – Project Structure

---

## Directory Tree

```
c:\Git\JPIDM_Web\
├── IDM_Web.sln                          Visual Studio Solution
├── README.md                            Repository root readme
├── EINS.md                              EINS enterprise identity context
├── Passport.md                          Passport/SailPoint context
├── docs/                                Project documentation (this folder)
│   ├── INDEX.md
│   ├── SYSTEM_CONTEXT.md
│   ├── RELATIONSHIPS.md
│   ├── FEATURES.md
│   ├── README.md
│   ├── ARCHITECTURE.md
│   ├── PROJECT_STRUCTURE.md
│   ├── SETUP.md
│   ├── API.md
│   ├── CONFIGURATION.md
│   ├── CONTRIBUTING.md
│   └── TROUBLESHOOTING.md
│
├── IDM_Web/                             Main ASP.NET MVC Project
│   ├── Global.asax                      Application entry point (markup)
│   ├── Global.asax.vb                   Application startup code
│   ├── Web.config                       Application configuration
│   ├── Web.Debug.config                 Debug config transforms
│   ├── Web.Release.config               Release config transforms
│   ├── IDM_Web.vbproj                   VB.NET project file
│   ├── packages.config                  NuGet package references
│   ├── readme.txt                       Original readme
│   │
│   ├── App_Start/                       Application startup configuration
│   │   ├── BundleConfig.vb              CSS/JS bundle registration
│   │   ├── FilterConfig.vb              Global MVC filters
│   │   ├── RouteConfig.vb               MVC routing rules
│   │   └── WebApiConfig.vb              Web API routing
│   │
│   ├── Controllers/                     MVC Controllers (HTTP request handlers)
│   │   ├── HomeController.vb            Dashboard and menu
│   │   ├── UserController.vb            User profile management
│   │   ├── GroupController.vb           Security group lifecycle
│   │   ├── AdminController.vb           Administrative functions
│   │   ├── RequestController.vb         Request tracking and approvals
│   │   ├── ResourceController.vb        Meeting rooms and equipment
│   │   ├── SharedController.vb          Shared utilities and AJAX helpers
│   │   ├── SharedAccountController.vb   Shared service account management
│   │   ├── SharedMailboxController.vb   Shared mailbox management
│   │   ├── ComputerAccountController.vb Computer account management
│   │   └── MasterMaintenanceController.vb System master data maintenance
│   │
│   ├── Services/                        Business logic layer
│   │   ├── UserService.vb               User profile operations
│   │   ├── GroupService.vb              Group lifecycle operations
│   │   ├── SharedAccountService.vb      Shared account operations
│   │   ├── SharedMailboxService.vb      Shared mailbox operations
│   │   ├── ComputerService.vb           Computer account operations
│   │   ├── ResourceService.vb           Resource management
│   │   ├── RequestService.vb            Request workflow tracking
│   │   ├── AdminService.vb              Admin-scope operations
│   │   ├── HomeService.vb               Dashboard data
│   │   ├── SharedService.vb             Shared dropdown list builders
│   │   └── CommonService.vb             Common utility functions
│   │
│   ├── DataAccess/                      Data access layer (EF + AD)
│   │   ├── IDM_DBAccessor.vb            Database connection management
│   │   ├── ADAccessor.vb                Active Directory operations
│   │   ├── Account_MSTAccessor.vb       Account master queries
│   │   ├── SecurityGroup_MSTAccessor.vb Security group queries
│   │   ├── Request_LSTAccessor.vb       Request tracking queries
│   │   ├── ComputerAccount_MSTAccessor.vb Computer account queries
│   │   ├── Define_MSTAccessor.vb        Configuration key-value access
│   │   ├── IDM_DBEntities.vb            Entity Framework DbContext
│   │   └── [150+ entity classes]        EF entity models (one per DB table)
│   │
│   ├── Models/                          Domain models
│   │   ├── UserModel.vb                 UserInfo, GHDUserInfo, ApplicationLeaveInfoModel
│   │   ├── GroupModel.vb                GroupInfo, MemberInfo
│   │   ├── RequestModel.vb              Request, approval history models
│   │   ├── ResourceModel.vb             Meeting room, equipment models
│   │   ├── ComputerModel.vb             Computer account models
│   │   └── MemberInfo.vb                Shared member detail model
│   │
│   ├── ViewModels/                      UI-specific data models for views
│   │   ├── ViewModelBase.vb             Abstract base with common properties
│   │   ├── UserViewModel.vb             User edit form
│   │   ├── ManagerUserViewModel.vb      Manager user edit form
│   │   ├── GroupViewModel.vb            Group create/edit form
│   │   ├── RequestListViewModel.vb      Request list
│   │   ├── RequestDetailViewModel.vb    Request detail with history
│   │   ├── RequestComputerViewModel.vb  Computer request view
│   │   ├── RequestGroupViewModel.vb     Group request view
│   │   ├── ResourceViewModel.vb         Resource create/edit
│   │   ├── SearchResourceViewModel.vb   Resource search
│   │   ├── ComputerViewModel.vb         Computer create/edit
│   │   ├── LeaveApplicationViewModel.vb Employee leave application
│   │   ├── InitialPasswordListViewModel.vb New user password init
│   │   ├── SharedAccountViewModel.vb    Shared account create/edit
│   │   ├── SharedMailboxViewModel.vb    Shared mailbox create/edit
│   │   └── ErrorViewModel.vb            Error page model
│   │
│   ├── Views/                           Razor MVC templates (.vbhtml)
│   │   ├── Shared/                      Shared partial views and layouts
│   │   │   ├── _Layout.vbhtml           Master page layout
│   │   │   ├── _ViewStart.vbhtml        View initialization
│   │   │   └── Error.vbhtml             Error page
│   │   ├── Home/                        Dashboard views
│   │   │   ├── Index.vbhtml             Main menu
│   │   │   ├── Maintenance.vbhtml       Maintenance mode screen
│   │   │   ├── DisabledMenu.vbhtml      Restricted menu
│   │   │   └── OffshoreMenu.vbhtml      Region-specific menu
│   │   ├── User/                        User management views
│   │   ├── Group/                       Group management views (19 templates)
│   │   ├── Admin/                       Admin panel views
│   │   ├── Request/                     Request lifecycle views
│   │   ├── Resource/                    Resource management views
│   │   ├── SharedAccount/               Shared account views
│   │   ├── SharedMailbox/               Shared mailbox views
│   │   └── ComputerAccount/             Computer account views
│   │
│   ├── Constants/                       System-wide enumerations and codes
│   │   └── Constants.vb                 ApplyStatus, FunctionID, ResourceType enums
│   │
│   ├── Extensions/                      MVC extension methods
│   │   └── Extensions.vb                AcceptParameterAttribute, Html.SortDirection()
│   │
│   ├── Validators/                      Custom data annotation validators
│   │   ├── MailAddressValidator.vb       Email format and uniqueness validation
│   │   ├── DisplayNameValidator.vb       Display name validation
│   │   ├── DateRangeValidator.vb         Date range validation
│   │   ├── NumOfComValidator.vb          Computer name count validation
│   │   ├── SGAliasNameValidator.vb       Security group alias validation
│   │   └── NotDataValidator.vb           NOT NULL data validation
│   │
│   ├── Util/                            Utility classes
│   ├── OperateFile/                     File operation utilities (CSV handling)
│   ├── Scripts/                         JavaScript files (custom + vendor)
│   ├── Content/                         CSS stylesheets
│   ├── fonts/                           Web fonts
│   ├── bin/                             Compiled assemblies (do not edit)
│   └── obj/                             Build output (do not edit)
│
├── IDM_Web.Test/                        NUnit test project
│   ├── DataAccess/                      Data access tests
│   └── Services/                        Service layer tests
│
├── SharedMBTestProject/                 Shared mailbox test project
│
└── packages/                            NuGet package downloads
```

---

## Key Folder Descriptions

### `Controllers/`
HTTP request handlers. Each controller maps to a functional area (User, Group, Admin, etc.). Controllers are thin — they delegate all business logic to Services and return Views or ActionResults.

### `Services/`
The business logic layer. All domain rules, validation, data orchestration, and workflow logic lives here. Services call DataAccess classes to read/write data.

### `DataAccess/`
Two access mechanisms:
1. **Entity Framework** — `IDM_DBEntities` DbContext auto-generated from IDM_DB schema. 150+ entity classes.
2. **Custom Accessors** — `ADAccessor.vb` for Active Directory via `System.DirectoryServices`; accessor classes wrapping common EF queries.

### `Models/`
Domain model classes representing core business objects (UserInfo, GroupInfo, etc.). These are passed between services and controllers.

### `ViewModels/`
View-specific data models that combine domain data with UI state (dropdown lists, toggles, error messages). One ViewModel per View.

### `Views/`
Razor templates (`.vbhtml`) organized by controller. Each View folder mirrors a Controller folder. `Shared/` holds the master layout and shared partial views.

### `Constants/`
Single file `Constants.vb` defines system enumerations: `ApplyStatus` (request states), `FunctionID` (request type codes), `ResourceType` (room vs. equipment).

### `Validators/`
Custom `ValidationAttribute` subclasses for ASP.NET Model validation. Applied as data annotations on ViewModel properties.

### `Extensions/`
`AcceptParameterAttribute` — enables multi-button forms to route to different action methods based on which submit button was clicked.

### `App_Start/`
Runs at application startup:
- `RouteConfig.vb` — MVC URL routing
- `BundleConfig.vb` — CSS/JS bundles
- `WebApiConfig.vb` — Web API routes
- `FilterConfig.vb` — Global filters

---

## File Naming Conventions

| Type | Convention | Example |
|------|-----------|---------|
| Controllers | `{Area}Controller.vb` | `GroupController.vb` |
| Services | `{Area}Service.vb` | `GroupService.vb` |
| ViewModels | `{Area}ViewModel.vb` | `GroupViewModel.vb` |
| Views | `{ActionName}.vbhtml` | `CreateInternalGroup.vbhtml` |
| Accessors | `{Entity}Accessor.vb` | `Account_MSTAccessor.vb` |
| Entity classes | Match DB table name | `Account_MST.vb` |

---

## Important Configuration Files

| File | Purpose |
|------|---------|
| `Web.config` | DB connection, AD credentials, debug flags, maintenance mode |
| `Web.Debug.config` | Overrides for local development |
| `Web.Release.config` | Overrides for production deployment |
| `packages.config` | NuGet package versions |
| `App_Start/RouteConfig.vb` | URL routing definitions |

---

## Related Documentation

- [ARCHITECTURE.md](./ARCHITECTURE.md) - How components interact
- [SETUP.md](./SETUP.md) - Setting up a development environment
- [CONTRIBUTING.md](./CONTRIBUTING.md) - Adding new features

---

*End of document*
