# IDM Web – API Reference

---

## Overview

IDM Web uses both standard **ASP.NET MVC** controller actions and **ASP.NET Web API** endpoints.

- **MVC Actions** — Serve full-page HTML views; used for form submissions and page navigation
- **Web API / AJAX endpoints** — Return JSON; used for dynamic UI updates (dropdown loading, search, member lookups)
- **SharedController** — Acts as the main AJAX helper endpoint (not a formal REST API)

---

## Web API Routes

Configured in `App_Start/WebApiConfig.vb`:

```
GET/POST  api/{controller}/{id?}
```

> **Note**: OData query support (`QueryableAttribute`) is present in config but currently commented out (not active in production).

---

## SharedController – AJAX Helper Endpoints

`SharedController.vb` serves as an in-page AJAX data provider. All actions return HTML fragments or JSON for dynamic UI.

### User Lookup

| Method | Route | Description |
|--------|-------|-------------|
| GET | `/Shared/GetUserInfo` | Get user info by account name |
| GET | `/Shared/GetUserInfoByGID` | Get user info by Global ID |
| GET | `/Shared/SearchUser` | Search users by name/account |

**Example Request:**
```javascript
$.ajax({
    url: '/Shared/GetUserInfo',
    data: { accountName: 'JP-q\\9004074957' },
    success: function(data) { /* populate form */ }
});
```

**Example Response:**
```json
{
  "accountName": "9004074957",
  "displayName": "John Smith",
  "mailAddress": "john.smith@jp.sony.com",
  "companyCode": "JPSC"
}
```

---

### Group Lookup

| Method | Route | Description |
|--------|-------|-------------|
| GET | `/Shared/GetGroupInfo` | Get group details by name |
| GET | `/Shared/SearchGroup` | Search groups by prefix/name |
| GET | `/Shared/GetGroupMembers` | Get current group member list |

---

### Reference Data Lookups

| Method | Route | Description |
|--------|-------|-------------|
| GET | `/Shared/CreateCompanyList` | Get list of companies for dropdown |
| GET | `/Shared/CreateMailDomainList` | Get mail domains for company |
| GET | `/Shared/CreateMailboxSizeList` | Get mailbox size options |
| GET | `/Shared/GetOUInfo` | Get AD Organizational Unit details |
| GET | `/Shared/SearchOU` | Search OU by name/path |

---

### Resource Lookup

| Method | Route | Description |
|--------|-------|-------------|
| GET | `/Shared/SearchResource` | Search meeting rooms and equipment |

---

## MVC Action Endpoints (Form-Based)

These endpoints handle full HTML page requests and form submissions. They are not REST APIs but follow standard MVC conventions.

### Home

| Method | Route | Description |
|--------|-------|-------------|
| GET | `/Home/Index` | Main dashboard/menu |
| GET | `/Home/Maintenance` | Maintenance mode page |

---

### User Management

| Method | Route | Description |
|--------|-------|-------------|
| GET | `/User/EditUser` | User self-service edit form |
| POST | `/User/EditUser` | Submit user edit |
| GET | `/User/ConfirmEditUser` | Confirm user edit |
| POST | `/User/ConfirmEditUser` | Apply confirmed edit |
| GET | `/User/ManagerEditUser` | Manager edits team member |
| POST | `/User/ManagerEditUser` | Submit manager edit |
| GET | `/User/ApplyLeave` | Employee leave application |
| POST | `/User/ApplyLeave` | Submit leave application |

---

### Security Groups

| Method | Route | Description |
|--------|-------|-------------|
| GET | `/Group/CreateInternalGroup` | Internal group creation form |
| POST | `/Group/CreateInternalGroup` | Submit new internal group |
| GET | `/Group/CreateExternalGroup` | External group creation form |
| POST | `/Group/CreateExternalGroup` | Submit new external group |
| GET | `/Group/EditInternalGroup` | Edit internal group |
| POST | `/Group/EditInternalGroup` | Submit internal group changes |
| GET | `/Group/EditInternalGroupMembers` | Edit internal group members |
| POST | `/Group/EditInternalGroupMembers` | Submit member changes |
| GET | `/Group/EditMultiGroupMembers` | Bulk member upload form |
| POST | `/Group/EditMultiGroupMembers` | Process bulk member CSV |
| GET | `/Group/DeleteGroup` | Group deletion request form |
| POST | `/Group/DeleteGroup` | Submit deletion request |
| GET | `/Group/ExtensionGroup` | Group expiration extension form |
| POST | `/Group/ExtensionGroup` | Submit extension request |
| GET | `/Group/EnableGroup` | Reactivate expired group form |
| POST | `/Group/EnableGroup` | Submit reactivation |
| POST | `/Group/DownloadGroupMembers` | Download member list as file |

---

### Shared Mailboxes

| Method | Route | Description |
|--------|-------|-------------|
| GET | `/SharedMailbox/CreateSharedMailbox` | Create shared mailbox form |
| POST | `/SharedMailbox/CreateSharedMailbox` | Submit new shared mailbox |
| GET | `/SharedMailbox/EditSharedMailbox` | Edit shared mailbox |
| POST | `/SharedMailbox/EditSharedMailbox` | Submit mailbox changes |
| GET | `/SharedMailbox/EditSharedMailboxMembers` | Edit mailbox members |
| POST | `/SharedMailbox/EditSharedMailboxMembers` | Submit member changes |
| GET | `/SharedMailbox/EditMultiSharedMailboxMembers` | Bulk member upload form |
| POST | `/SharedMailbox/EditMultiSharedMailboxMembers` | Process bulk member CSV |
| POST | `/SharedMailbox/DownloadSharedMailboxMembers` | Download member list |

---

### Shared Accounts

| Method | Route | Description |
|--------|-------|-------------|
| GET | `/SharedAccount/CreateSharedAccount` | Create shared account form |
| POST | `/SharedAccount/CreateSharedAccount` | Submit new shared account |
| GET | `/SharedAccount/EditSharedAccount` | Edit shared account |
| POST | `/SharedAccount/EditSharedAccount` | Submit shared account changes |
| GET | `/SharedAccount/DeleteSharedAccount` | Delete shared account form |
| POST | `/SharedAccount/DeleteSharedAccount` | Submit deletion request |

---

### Computer Accounts

| Method | Route | Description |
|--------|-------|-------------|
| GET | `/ComputerAccount/CreateComputerAccount` | Computer creation form |
| POST | `/ComputerAccount/CreateComputerAccount` | Submit computer creation |
| GET | `/ComputerAccount/ConfirmCreate` | Confirm computer creation |
| POST | `/ComputerAccount/ConfirmCreate` | Execute computer creation |
| GET | `/ComputerAccount/EditComputerAccountOU` | Edit computer OU form |
| POST | `/ComputerAccount/EditComputerAccountOU` | Submit OU change |
| GET | `/ComputerAccount/ResetComputerAccount` | Reset computer form |
| POST | `/ComputerAccount/ResetComputerAccount` | Submit computer reset |

---

### Resources (Rooms & Equipment)

| Method | Route | Description |
|--------|-------|-------------|
| GET | `/Resource/CreateResource` | Create resource form |
| POST | `/Resource/CreateResource` | Submit new resource |
| GET | `/Resource/EditResource` | Edit resource form |
| POST | `/Resource/EditResource` | Submit resource changes |
| GET | `/Resource/DeleteResource` | Delete resource form |
| POST | `/Resource/DeleteResource` | Submit deletion request |
| GET | `/Resource/SearchResource` | Search resources |

---

### Requests & Approvals

| Method | Route | Description |
|--------|-------|-------------|
| GET | `/Request/RequestList` | View user's request history |
| GET | `/Request/RequestDetail/{id}` | View specific request detail |
| GET | `/Request/RequestApproval/{id}` | Approval form for approver |
| POST | `/Request/RequestApproval` | Submit approval/rejection |
| POST | `/Request/RequestCancel` | Cancel a pending request |

---

### Administration

| Method | Route | Description |
|--------|-------|-------------|
| GET | `/Admin/UserList` | User list (scoped by admin role) |
| GET | `/Admin/SearchRequestList` | Pending request queue |
| GET | `/Admin/LeaveApplicationListDetailManager` | Leave application management |
| GET | `/Admin/InitialPasswordList` | New user password init list |

---

## Request Model (Request_LST)

All form submissions create a record in `Request_LST` with these key fields:

| Field | Type | Description |
|-------|------|-------------|
| `RequestID` | string | Unique request identifier |
| `FunctionID` | string | Request type code (see FEATURES.md) |
| `Status` | string | Current status (WaitingForApproval, Completed, etc.) |
| `ApplicantAccountName` | string | Who submitted the request |
| `TargetAccountName` | string | Who/what is being changed |
| `ApproverAccountName` | string | Who must approve |
| `ApplyDate` | DateTime | When submitted |
| `ProcessDate` | DateTime | When processed |
| `RequestData` | string | Serialized request details |

---

## Response Conventions

**HTML Responses** (MVC Actions):
- `200 OK` with full HTML page
- `302 Found` redirect on success
- Error view on validation failure

**JSON Responses** (AJAX/SharedController):
- `200 OK` with JSON object
- `400 Bad Request` for invalid input
- `500 Internal Server Error` for unexpected failures

---

## Related Documentation

- [FEATURES.md](./FEATURES.md) - All features and FunctionID reference
- [ARCHITECTURE.md](./ARCHITECTURE.md) - System design
- [CONFIGURATION.md](./CONFIGURATION.md) - Route configuration

---

*End of document*
