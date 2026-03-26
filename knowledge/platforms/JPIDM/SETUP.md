# IDM Web – Setup Guide

---

## Prerequisites

| Requirement | Version | Notes |
|-------------|---------|-------|
| Visual Studio | 2019+ | Community edition or higher |
| .NET Framework | 4.7.2 | Target framework |
| SQL Server | 2016+ | Or SQL Server Express |
| IIS / IIS Express | — | For Windows Authentication |
| Active Directory access | — | For AD operations (optional in debug mode) |
| Git | — | For source control |

---

## 1. Clone the Repository

```powershell
git clone <repository-url>
cd JPIDM_Web
```

---

## 2. Open the Solution

```powershell
start IDM_Web.sln
```

Or open `IDM_Web.sln` directly from Visual Studio.

---

## 3. Restore NuGet Packages

Visual Studio restores packages automatically on build. To restore manually:

```powershell
# In Package Manager Console (Tools > NuGet Package Manager > Package Manager Console)
Update-Package -reinstall
```

Or right-click the solution → **Restore NuGet Packages**.

---

## 4. Database Setup

### 4a. Obtain or Create IDM_DB

You need access to a SQL Server instance with the `IDM_DB` database.

If you have a backup:
```sql
RESTORE DATABASE IDM_DB 
FROM DISK = 'C:\backups\IDM_DB.bak'
WITH MOVE 'IDM_DB' TO 'C:\SQLData\IDM_DB.mdf',
MOVE 'IDM_DB_log' TO 'C:\SQLData\IDM_DB_log.ldf'
```

### 4b. Verify Required Tables

IDM_DB requires 100+ tables including:
- `Account_MST`, `Account_TRN`
- `SecurityGroup_MST`, `SecurityGroup_TRN`
- `SharedMailbox_MST`, `ResourceMailbox_MST`
- `Request_LST`, `RequestID_MST`
- `GHD_PersonalInfo_MST`, `GHD_OrganizationInfo_MST`
- `Define_MST`, `Company_MST`, `OU_MST`

---

## 5. Configure Web.config

Edit `IDM_Web/Web.config`:

### 5a. Update Connection String

```xml
<connectionStrings>
  <add name="IDM_DBEntities"
       connectionString="metadata=res://*/DataAccess.IDM_DB.csdl|...;
       provider=System.Data.SqlClient;
       provider connection string=&quot;
       data source=YOUR_SQL_SERVER;
       initial catalog=IDM_DB;
       integrated security=True;
       MultipleActiveResultSets=True&quot;"
       providerName="System.Data.EntityClient" />
</connectionStrings>
```

Replace `YOUR_SQL_SERVER` with your SQL Server instance name or IP.

### 5b. Configure Debug Mode

```xml
<appSettings>
  <!-- Enable debug mode for local development -->
  <add key="DebugModeEnabled" value="True" />

  <!-- Set to your own Windows account (domain\username) -->
  <add key="DebugModeLoginUser" value="DOMAIN\your-username" />

  <!-- AD Global Catalog connection (for read operations) -->
  <add key="DebugModeGCConnectionString" value="GC://your-dc-server/DC=your,DC=domain,DC=com" />

  <!-- AD LDAP connection (for write operations) -->
  <add key="DebugModeLDAPConnectionString" value="LDAP://your-dc-server/DC=your,DC=domain,DC=com" />

  <!-- Service account for AD operations -->
  <add key="DebugModeAccountOperatorUser" value="DOMAIN\service-account" />
</appSettings>
```

> **Note**: Without AD access, AD-dependent features (account updates, group operations) will fail. The UI and request submission screens remain accessible.

### 5c. Maintenance Mode

```xml
<!-- Empty "" = All users can access -->
<!-- "Admin" = Admin users only -->
<!-- Other values = Maintenance mode message -->
<add key="MaintenanceMode" value="" />
```

---

## 6. Configure IIS Express for Windows Authentication

Windows Authentication is **required** for IDM Web. IIS Express config is at:
`.vs\JPIDM_Web\config\applicationhost.config`

Verify:
```xml
<windowsAuthentication enabled="true" />
<anonymousAuthentication enabled="false" />
```

Or in Visual Studio:
1. Right-click project → **Properties**
2. Go to **Web** tab
3. Under **Servers**, ensure IIS Express is selected
4. Open `launchSettings.json` or IIS Express settings and enable Windows Authentication

---

## 7. Build the Solution

```
Build → Build Solution (Ctrl+Shift+B)
```

Expected output:
```
Build succeeded.
0 Error(s)
```

If there are errors, check:
- NuGet packages restored
- SQL connection string valid
- .NET 4.7.2 framework installed

---

## 8. Run the Application

Press `F5` or click the green Run button in Visual Studio.

The application opens at: `http://localhost:{auto-assigned-port}/`

Default landing page is `Home/Index`.

---

## 9. Running Tests

### Unit Test Project

```powershell
# Via Visual Studio Test Explorer
# Test → Run All Tests

# Via command line (NUnit 3.0.0)
dotnet test IDM_Web.Test\IDM_Web.Test.vbproj
```

Test projects:
- `IDM_Web.Test/` — Unit tests for DataAccess and Services
- `SharedMBTestProject/` — Tests for shared mailbox features

---

## Development Workflow

### Typical Workflow

1. Check out a feature branch
2. Make changes in Services, Controllers, and/or Views
3. Build and test locally (`F5`)
4. Run unit tests
5. Submit PR for code review

### Adding a New Feature

1. **Add Constants** — Add new FunctionID in `Constants/Constants.vb` if needed
2. **Add Model** — Add domain model in `Models/` if new entity
3. **Add ViewModel** — Add ViewModel in `ViewModels/` for the UI form
4. **Add Validator** — Add validation attributes in `Validators/` if needed
5. **Add Service** — Implement business logic in `Services/`
6. **Add DataAccess** — Add DB or AD operations in `DataAccess/`
7. **Add Controller** — Handle HTTP request and wire up service
8. **Add Views** — Add `.vbhtml` Razor templates in matching `Views/` subfolder
9. **Register Routes** — Update `App_Start/RouteConfig.vb` if non-standard route

---

## Important Notes

### Debug Mode

When `DebugModeEnabled=True` in `Web.config`:
- The logged-in user is overridden by `DebugModeLoginUser`
- AD connection strings use the `DebugMode*` settings
- Useful for testing specific user roles (admin, manager, regular user)

### AD Operations

AD operations (creating accounts, modifying attributes) require:
- Network access to company AD (DC servers)
- Service account with appropriate permissions
- Without this, the UI works but AD writes fail silently or return errors

### GHD Data

GHD tables (`GHD_PersonalInfo_MST`, etc.) must contain employee data for user lookups to work. In development, populate these from a production data export or test data set.

---

## Common Setup Errors

| Error | Cause | Solution |
|-------|-------|---------|
| `403 Forbidden` on login | Windows Auth not enabled | Enable Windows Auth in IIS Express settings |
| SQL connection error | Wrong connection string | Update `Web.config` with correct SQL Server name |
| User not found | GHD tables empty | Import GHD test data into IDM_DB |
| AD operation fails | No AD connectivity | Use debug mode or mock AD for local dev |
| NuGet restore fails | Corporate proxy | Configure NuGet proxy settings |

See [TROUBLESHOOTING.md](./TROUBLESHOOTING.md) for more.

---

## Related Documentation

- [CONFIGURATION.md](./CONFIGURATION.md) — Full configuration reference
- [ARCHITECTURE.md](./ARCHITECTURE.md) — System design
- [CONTRIBUTING.md](./CONTRIBUTING.md) — Development guidelines
- [TROUBLESHOOTING.md](./TROUBLESHOOTING.md) — Common issues

---

*End of document*
