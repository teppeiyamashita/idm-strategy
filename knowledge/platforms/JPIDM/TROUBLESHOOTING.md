# IDM Web – Troubleshooting Guide

---

## 1. Application Startup Issues

### Application won't start / 403 Forbidden on login

**Symptoms**: Browser shows 403 or redirect loop on first load.

**Cause**: Windows Authentication not configured in IIS Express.

**Solution**:
1. Open `.vs\JPIDM_Web\config\applicationhost.config`
2. Find `<windowsAuthentication>` and set `enabled="true"`
3. Find `<anonymousAuthentication>` and set `enabled="false"`
4. Restart Visual Studio and try again

Or in Visual Studio:
- Select the IIS Express server profile in project properties
- Ensure Windows Authentication is enabled

---

### "Could not connect to SQL Server"

**Symptoms**: Application starts but crashes with SQL connection error.

**Solution**:
1. Verify SQL Server is running
2. Check connection string in `Web.config`:
   ```xml
   data source=YOUR_SERVER_NAME;initial catalog=IDM_DB;...
   ```
3. Ensure Windows auth has permission to `IDM_DB`
4. Test connection in SQL Server Management Studio (SSMS) with same credentials

---

### NuGet package restore fails

**Symptoms**: Build fails with missing assembly errors.

**Solution**:
```powershell
# Right-click solution in Solution Explorer
# Select "Restore NuGet Packages"

# Or via Package Manager Console
Update-Package -reinstall
```

If behind a corporate proxy:
1. Open `%APPDATA%\NuGet\NuGet.config`
2. Add proxy settings:
   ```xml
   <config>
     <add key="http_proxy" value="http://proxy.company.com:8080" />
   </config>
   ```

---

## 2. Authentication and User Issues

### "User not found" or blank username

**Symptoms**: After login, user shows as anonymous or "User Not Found" error.

**Cause**: `DebugModeEnabled` is `True` but `DebugModeLoginUser` is set to a user that doesn't exist in GHD tables.

**Solution**:
1. Check `Web.config` → `DebugModeLoginUser`
2. Ensure that account exists in `GHD_PersonalInfo_MST` or `Account_MST`
3. Alternatively, set `DebugModeEnabled = False` and log in with your own AD account

---

### Login redirects back to login page (loop)

**Symptoms**: Login page keeps redirecting.

**Cause**: IIS Express anonymous authentication is still enabled.

**Solution**: Disable anonymous auth (see Section 1 above).

---

### "Access Denied" on specific pages

**Symptoms**: Specific pages return 403 or redirect to Home.

**Cause**: User lacks required role (System Admin, Company Admin) or company scope.

**Solution**:
1. Check your user's `ManageCompanyCodeList` in session
2. Check `HomeController.Index` for role determination logic
3. Ensure the logged-in user exists in admin tables in IDM_DB

---

## 3. Database Issues

### EntityFramework model mismatch error

**Symptoms**: `InvalidOperationException: The model backing the 'IDM_DBEntities' context has changed since the database was created.`

**Cause**: IDM_DB schema was updated but EF model was not re-generated.

**Solution**:
1. Open `IDM_Web/DataAccess/IDM_DB.edmx` in Visual Studio
2. Right-click the design surface → **Update Model from Database**
3. Rebuild the project

---

### GHD tables empty (no employee data)

**Symptoms**: User search returns no results; employee fields are blank.

**Cause**: GHD integration tables not populated in development environment.

**Solution**:
1. Obtain a GHD data export from the production or test environment
2. Import into local IDM_DB:
   - `GHD_PersonalInfo_MST`
   - `GHD_OrganizationInfo_MST`
   - `GHD_PersonalORGInfo_MST`
3. Or ask the DBA team for a sanitized development data set

---

### Request_LST not updating

**Symptoms**: Submitted requests don't appear in request list.

**Cause**: Transaction not committed, or DB write failed silently.

**Solution**:
1. Check application event log / Visual Studio Output window for exceptions
2. Verify `Request_LST` table exists and user has INSERT permission
3. Check `RequestID_MST` table is populated (used for ID generation)

---

## 4. Active Directory Issues

### AD operations fail (group creation, account changes)

**Symptoms**: Form submits successfully but AD changes don't happen; request status stays at Error.

**Cause**: No AD connectivity, wrong service account, or insufficient permissions.

**Solution**:
1. Verify network connectivity to the DC server in `DebugModeGCConnectionString`
2. Test the service account credentials (`DebugModeAccountOperatorUser`):
   ```powershell
   Test-ADAuthentication -Username "DOMAIN\svc-account" -Password (Read-Host -AsSecureString)
   ```
3. Ensure the service account has:
   - Write permission to relevant OUs
   - Ability to create/modify group and user objects
4. Check `Request_LST.Status` for error details

---

### "Object reference not found" in ADAccessor

**Symptoms**: Exception from `ADAccessor.vb` with null reference.

**Cause**: DC server unreachable, or DN path mismatch.

**Solution**:
1. Ping the DC: `ping jpjdcads108`
2. Verify the LDAP/GC path format in `Web.config` matches actual AD structure
3. Test the path in LDP.exe (AD explorer tool)

---

## 5. UI Issues

### Dropdowns not loading (company, mail domain, mailbox size)

**Symptoms**: Dropdown appears empty or throws JavaScript error.

**Cause**: AJAX call to `SharedController` failed, or `Define_MST` table is empty.

**Solution**:
1. Open browser developer tools → Network tab
2. Find the AJAX call to `/Shared/CreateCompanyList` (or similar)
3. Check the response — if 500, check the IDM_DB `Define_MST` table has data
4. Populate `Define_MST` with basic configuration key-value pairs

---

### Member list not updating after add/remove

**Symptoms**: Added members don't appear, or removed members still show.

**Cause**: KnockoutJS observable not refreshed, or AJAX call failed.

**Solution**:
1. Check browser console for JavaScript errors
2. Verify the `GetGroupMembers` AJAX call returned successfully
3. Check `SecurityGroupMember_TMP` for the current UI state

---

### Bulk CSV upload not processing

**Symptoms**: Upload succeeds but members don't change.

**Cause**: CSV format mismatch, file encoding issue, or `OperateFile` processing error.

**Solution**:
1. Ensure CSV uses correct format (account name per row, UTF-8 encoding)
2. Check the Visual Studio output for exceptions from `OperateFile`
3. Verify the uploaded file size is within IIS limits (`maxRequestLength` in Web.config)

---

## 6. Maintenance Mode

### Application shows maintenance page unexpectedly

**Cause**: `MaintenanceMode` value in `Web.config` is set to `"Admin"` or another value.

**Solution**:
1. Check `Web.config`:
   ```xml
   <add key="MaintenanceMode" value="" />
   ```
2. Set to empty string `""` to allow all users
3. Recycle the application pool in IIS (or restart IIS Express)

---

## 7. Performance Issues

### Slow page loads

**Common causes and solutions**:

| Cause | Solution |
|-------|---------|
| Debug mode with bundles not minified | Expected in development; production builds minify assets |
| Large `SecurityGroupMember_TMP` table | Run cleanup jobs; truncate temp data |
| Slow AD operations | Check DC server response time; use GC instead of LDAP for reads |
| Define_MST not cached | `Define_MSTAccessor` caches on first load; check if cache is being invalidated |

---

## 8. Common Error Messages

| Error Message | Meaning | Fix |
|--------------|---------|-----|
| `The model backing 'IDM_DBEntities' has changed` | EF model out of sync with DB | Update EDMX model |
| `Access to the path is denied` | File permission error in OperateFile | Grant write access to app's temp folder |
| `DirectoryServicesCOMException` | AD operation failed | Check AD connectivity and service account permissions |
| `Object reference not set to an instance` | Null reference in service/controller | Check GHD data exists; check session is not expired |
| `A potentially dangerous Request.Form value was detected` | Unescaped HTML in form input | Use `<httpRuntime requestValidationMode="2.0"/>` if needed, or sanitize input |

---

## 9. Getting Help

1. **Check IDM_DB logs**: Look at `Request_LST.Status` and error fields
2. **Check Windows Event Log**: Application errors logged by IIS
3. **Check Visual Studio Output**: Runtime exceptions appear here in debug mode
4. **Enable detailed errors**: In `Web.config`, set `<customErrors mode="Off" />`
5. **Contact DBA**: For IDM_DB schema issues
6. **Contact AD admin**: For Active Directory connectivity issues

---

## Related Documentation

- [SETUP.md](./SETUP.md) - Initial setup steps (many issues prevented here)
- [CONFIGURATION.md](./CONFIGURATION.md) - All Web.config settings
- [ARCHITECTURE.md](./ARCHITECTURE.md) - Where to find code for debugging

---

*End of document*
