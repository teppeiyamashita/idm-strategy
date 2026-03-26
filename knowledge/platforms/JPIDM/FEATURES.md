# IDM Web – Feature Reference

---

## Overview

This document is an authoritative reference for all features in IDM Web. Each capability is grounded in concrete system behavior based on the actual codebase.

---

## Feature Table (Authoritative)

### Main Category: User Account Management

| Capability | Concrete Example | Code Location |
|-----------|-----------------|---------------|
| Self-service profile edit | Employee changes display name, mail prefix, or mailbox size | `UserController.EditUser` |
| Manager-initiated edit | Manager changes subordinate's display name before manager confirms | `UserController.ManagerEditUser` |
| Mail alias management | Adding or removing proxy email addresses from an account | `UserService.SaveMailAddresses` |
| Mailbox size selection | Selecting Standard/Large/Enterprise mailbox tier | `UserViewModel.MailboxSizeList` |
| Mail domain selection | Changing between `jp.sony.com` / `sony.com` domains | `SharedService.CreateMailDomainList` |
| Leave/separation request | Submitting employee leave application with alternate mail handling | `UserController.ApplyLeave` |
| Admin user edit | System admin edits any user attributes across all companies | `UserController.EditUserForAdmin` |
| Company admin user edit | Company-scoped admin edits users in their company | `UserController.EditUserForCompanyAdmin` |
| Sony.com address preservation | Preserving `@sony.com` address when mail domain changes | `UserController.ConfirmManagerEditUser` |

---

### Main Category: Security Group Management

| Capability | Concrete Example | Code Location |
|-----------|-----------------|---------------|
| Internal group creation | Creating a new internal AD security group | `GroupController.CreateInternalGroup` |
| External group creation | Creating an external group with mandatory expiration date | `GroupController.CreateExternalGroup` |
| Internal group member edit | Adding/removing up to 200 members via UI list | `GroupController.EditInternalGroupMembers` |
| External group member edit | Managing external group membership | `GroupController.EditExternalMembers` |
| Bulk member import | Upload CSV to batch-add or batch-remove members | `GroupController.EditMultiGroupMembers` |
| Group expiration extension | Extending the expiry date of an external group | `GroupController.ExtensionGroup` |
| Group reactivation | Re-enabling an expired external security group | `GroupController.EnableGroup` |
| Group deletion request | Submitting deletion request with admin approval required | `GroupController.DeleteGroup` |
| Group member list export | Downloading group membership as a downloadable file | `GroupController.DownloadGroupMembers` |
| Alt-recipient (forwarding) | Configuring mail forwarding for a group | `GroupService.SetAltRecipient` |
| Group display name parts | Managing separate display name components for a group | `GroupViewModel.DisplayNamePartsList` |

---

### Main Category: Shared Account Management

| Capability | Concrete Example | Code Location |
|-----------|-----------------|---------------|
| Shared account creation | Registering a new shared service account in AD | `SharedAccountController.CreateSharedAccount` |
| Shared account property edit | Changing display name or description of a service account | `SharedAccountController.EditSharedAccount` |
| Shared account deletion | Requesting shared account removal with approval workflow | `SharedAccountController.DeleteSharedAccount` |
| Owner validation | Ensuring account ownership is valid before changes | `SharedAccountService.ValidateOwner` |

---

### Main Category: Shared Mailbox Management

| Capability | Concrete Example | Code Location |
|-----------|-----------------|---------------|
| Shared mailbox creation | Creating a new shared mailbox for a team | `SharedMailboxController.CreateSharedMailbox` |
| Shared mailbox member edit | Adding/removing delegates from a shared mailbox | `SharedMailboxController.EditSharedMailboxMembers` |
| Bulk member edit | Upload CSV to add/remove mailbox members in batch | `SharedMailboxController.EditMultiSharedMailboxMembers` |
| Member list export | Downloading mailbox member list as a file | `SharedMailboxController.DownloadSharedMailboxMembers` |
| Mail alias management | Adding proxy addresses to a shared mailbox | `SharedMailboxService.SaveMailAliases` |
| Mailbox forwarding | Setting alternate recipient (mail forwarding) | `SharedMailboxService.SetForwardTo` |

---

### Main Category: Computer Account Management

| Capability | Concrete Example | Code Location |
|-----------|-----------------|---------------|
| Computer account creation | Registering a new computer in a specific AD OU | `ComputerAccountController.CreateComputerAccount` |
| Computer account OU move | Moving a computer to a different Organizational Unit | `ComputerAccountController.EditComputerAccountOU` |
| Computer account reset | Resetting computer account password/status | `ComputerAccountController.ResetComputerAccount` |
| Computer confirmation workflow | Confirmation screen before executing AD operation | `ComputerAccountController.ConfirmCreate` |
| OU search and selection | Searching/browsing AD OUs for computer placement | `SharedController.GetOUInfo` |

---

### Main Category: Resource Management

| Capability | Concrete Example | Code Location |
|-----------|-----------------|---------------|
| Meeting room registration | Creating a resource mailbox for Conference Room A | `ResourceController.CreateResource` |
| Equipment registration | Registering a projector as a bookable resource | `ResourceController.CreateResource` (ResourceType=1) |
| Resource property edit | Changing room capacity or contact details | `ResourceController.EditResource` |
| Resource deletion | Requesting removal of a decommissioned resource | `ResourceController.DeleteResource` |
| Resource search | Finding resources by name, type, or company | `ResourceController.SearchResource` |

---

### Main Category: Request & Approval Workflow

| Capability | Concrete Example | Code Location |
|-----------|-----------------|---------------|
| Request submission | Any identity change creates a tracked `Request_LST` record | All controllers |
| Request approval (manager) | Manager approves user profile change request | `RequestController.RequestApproval` |
| Request approval (admin) | Admin approves group deletion request | `RequestController.RequestApproval` |
| Request rejection | Approver rejects request with reason | `RequestController.RequestApproval` |
| Request cancellation | Submitter withdraws a pending request | `RequestController.RequestCancel` |
| Request status tracking | Viewing current status (WaitingForApproval → Completed) | `RequestController.RequestList` |
| Request history | Viewing full approval history for a request | `RequestController.RequestDetail` |
| Function-based routing | Request detail view routes by FunctionID to correct sub-view | `RequestController.RequestDetail` logic |

---

### Main Category: Administration

| Capability | Concrete Example | Code Location |
|-----------|-----------------|---------------|
| Company-scoped user list | Company admin views all users within their companies | `AdminController.UserList` |
| System-wide user list | System admin views all users across all companies | `AdminController.UserList` |
| Pending request administration | Admin reviews and processes all pending approval requests | `AdminController.SearchRequestList` |
| Leave application management | Admin manages employee leave/separation workflows | `AdminController.LeaveApplicationListDetailManager` |
| Initial password list | Admin views new users requiring password initialization | `AdminController.InitialPasswordList` |
| Master maintenance | System admin configures master data tables | `MasterMaintenanceController` |
| Maintenance mode | Restricting IDM Web access to admins only | `HomeController.Index` + Web.config `MaintenanceMode` |

---

## What IDM Web Does NOT Do

| Capability | Not Supported | System That Does It |
|-----------|---------------|---------------------|
| Global ID issuance | ❌ | EINS |
| Identity system of record | ❌ | EINS / GHD |
| IAM access governance | ❌ | Passport (SailPoint) |
| Access certification / reviews | ❌ | Passport (SailPoint) |
| Compliance reporting | ❌ | Passport (SailPoint) |
| Authentication provider | ❌ | Active Directory (Windows Auth) |
| Password reset portal | ❌ | AD native / Passport |
| Workday integration | ❌ | Passport / EINS |
| Entra ID / cloud provisioning | ❌ | Azure AD Connect / Passport |
| HR data origination | ❌ | GHD / Workday |
| Password policy enforcement | ❌ | Active Directory |
| Single Sign-On (SSO) | ❌ | Corp AD federation / Entra ID |

---

## Request Types (Function IDs)

All requests are categorized by a Function ID stored in `Request_LST.FunctionID`:

| Function ID | Operation |
|------------|-----------|
| 010C10 | Edit User |
| 010C20 | Edit User (Admin) |
| 010C40 | Edit User (Company Admin) |
| 020A10 | Create Shared Account |
| 020C10 | Edit Shared Account |
| 020D10 | Delete Shared Account |
| 021A10 | Create Shared Mailbox |
| 021C10 | Edit Shared Mailbox |
| 022C10 | Edit Shared Mailbox Members |
| 023C10 | Bulk Edit Shared Mailbox Members |
| 024C10 | Download Shared Mailbox Members |
| 030A10 | Create Internal Group |
| 030C10 | Edit Internal Group |
| 031C10 | Edit Internal Group Members |
| 032C10 | Enable Expired Group |
| 033C10 | Extend Group Expiration |
| 035C10 | Bulk Edit Group Members |
| 036C10 | Download Group Members |
| 040A10 | Create External Group |
| 040C10 | Edit External Group |
| 050A10 | Create Computer Account |
| 050C10 | Edit Computer Account |
| 050R10 | Reset Computer Account |
| 070A10 | Create Resource |
| 070C10 | Edit Resource |
| 070D10 | Delete Resource |

---

## Request Status Flow

| Status | Description |
|--------|-------------|
| `WaitingForApproval` | Request submitted, awaiting approver action |
| `WaitingForProcess` | Approved, awaiting system execution |
| `Processing` | System is executing the change |
| `Completed` | Change successfully applied in AD/Exchange |
| `Rejected` | Request rejected by approver |
| `Canceled` | Request withdrawn by submitter |
| `Error` | System error occurred during execution |
| `BatchProcessing` | Batch job is processing |
| `BatchCompleted` | Batch processing completed successfully |
| `BatchAbend` | Batch processing ended with error |

---

## Related Documentation

- [SYSTEM_CONTEXT.md](./SYSTEM_CONTEXT.md) - System purpose and scope
- [RELATIONSHIPS.md](./RELATIONSHIPS.md) - How IDM Web relates to EINS, Passport, AD
- [ARCHITECTURE.md](./ARCHITECTURE.md) - System design

---

*End of document*
