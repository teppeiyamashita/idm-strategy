# JPIDM Feature Categorization

> Source: JPIDM knowledge base (Platform Expert analysis)
> Purpose: To inform replatforming scope — Sync Engine features will be replatformed per the Provisioning Architecture Principles (SCIM/API-driven); Self-Service features require a separate portal/workflow platform decision.

---

## Bucket 1: Sync Engine

*These are automated, background provisioning and synchronization capabilities. The replacement platform for these features is governed by [PROVISIONING_ARCHITECTURE_PRINCIPLES.md](PROVISIONING_ARCHITECTURE_PRINCIPLES.md) — all must use SCIM/REST API-driven provisioning, never ADSI.*

| Feature | Description | Upstream Trigger | Downstream Target |
|---------|-------------|-----------------|-------------------|
| GHD data import to IDM_DB | Synchronizes employee and organizational data from EINS/GHD into local IDM_DB tables (GHD_PersonalInfo_MST, GHD_OrganizationInfo_MST, GHD_PersonalORGInfo_MST) | EINS/GHD data updates | IDM_DB (read-only GHD tables) |
| MIM synchronization | Microsoft Identity Manager backend synchronization supporting credential operations | MIM sync schedule | Active Directory, Exchange |

> ⚠️ **Documentation Gap: Joiner/Mover/Leaver Automation**
>
> The following capabilities are **explicitly noted as undocumented** in the JPIDM knowledge base:
> - Automated AD account creation when new employees appear in EINS/GHD
> - Automated account modification when employees move organisations
> - Automated account disabling/deletion when employees leave
> - Automated mailbox provisioning for new hires
>
> Per `knowledge/platforms/JPIDM.md`, "Joiner / mover / leaver automation logic" is NOT documented. The existence of an "Initial Password List" admin feature suggests some automated account creation may occur, but the mechanism and trigger are unspecified.
>
> **Recommendation**: Survey the JPIDM operations team (Avanade) and review MIM runbooks to determine actual JML automation scope before finalising replatforming architecture.

---

## Bucket 2: Self-Service Capabilities

*These are interactive features accessed via the web portal by administrators or end users. These are NOT covered by the provisioning architecture principles — they require a separate UI/workflow platform decision.*

### Personal Account & Mail Settings

| Feature | Description | Who Uses It |
|---------|-------------|-------------|
| Self-service profile edit | Edit own display name, mail prefix, proxy addresses, mailbox size | Regular employees |
| Mail alias management | Add/remove email aliases (proxy addresses) from own account | Regular employees |
| Mail domain selection | Change primary email domain (jp.sony.com vs sony.com) | Regular employees |
| Mailbox size selection | Request mailbox size tier change (Standard/Large/Enterprise) | Regular employees |

### Password & Account Access

| Feature | Description | Who Uses It |
|---------|-------------|-------------|
| Password reset | Reset Windows password using MIM portal (self-service with security questions) | All users |
| Account unlock | Unlock locked account after failed login attempts | All users |

### Employee Lifecycle (Manager / HR)

| Feature | Description | Who Uses It |
|---------|-------------|-------------|
| Manager-initiated profile edit | Manager edits subordinate's profile attributes | Managers |
| Company affiliation change | Change company assignment for multi-company employees (兼務者) | Employees or Managers |
| Leave/separation request | Submit employee departure application with alternate mail forwarding | Regular employees |

### Group & Mailing List Management

| Feature | Description | Who Uses It |
|---------|-------------|-------------|
| Internal group creation | Create new internal AD security/distribution group | Authorised users, Group owners |
| External group creation | Create external group with mandatory expiration date | Authorised users, Group owners |
| Internal group member edit | Add/remove up to 200 members via UI list | Group owners, Alternative admins |
| External group member edit | Manage external group membership | Group owners, Alternative admins |
| Bulk member import | Upload CSV to batch-add or batch-remove group members | Group owners, Alternative admins |
| Group expiration extension | Extend expiry date of external security group | Group owners |
| Group reactivation | Re-enable an expired external group | Group owners, Admins |
| Group deletion request | Submit group deletion request (requires approval) | Group owners |
| Group member list export | Download group membership as CSV/file | Group owners, Alternative admins |
| Group mail forwarding | Configure alternate recipient (mail forwarding) for group | Group owners |

### Shared Service Accounts

| Feature | Description | Who Uses It |
|---------|-------------|-------------|
| Shared account creation | Register new shared service account in AD | Authorised admins, Resource owners |
| Shared account modification | Change shared account display name, description, properties | Authorised admins, Resource owners |
| Shared account deletion | Request shared account removal (requires approval) | Authorised admins, Resource owners |

### Shared Mailbox Management

| Feature | Description | Who Uses It |
|---------|-------------|-------------|
| Shared mailbox creation | Create new shared mailbox for team/department | Authorised admins, Mailbox owners |
| Shared mailbox member edit | Add/remove delegates to shared mailbox | Mailbox owners, Alternative admins |
| Bulk shared mailbox member edit | Upload CSV to batch-update mailbox members | Mailbox owners, Alternative admins |
| Shared mailbox member list export | Download mailbox delegation list as file | Mailbox owners, Alternative admins |
| Shared mailbox mail alias management | Add proxy addresses to shared mailbox | Mailbox owners, Admins |
| Shared mailbox forwarding | Set alternate recipient (mail forwarding) | Mailbox owners, Admins |

### Facility Resources (Meeting Rooms & Equipment)

| Feature | Description | Who Uses It |
|---------|-------------|-------------|
| Meeting room resource creation | Create resource mailbox for conference room | Facility admins, IT admins |
| Equipment resource creation | Register equipment (projector, etc.) as bookable resource | Facility admins, IT admins |
| Resource property edit | Change room capacity, equipment specs, contact info | Resource owners, Admins |
| Resource deletion | Request removal of decommissioned resource | Resource owners, Admins |

### Computer Account Management

| Feature | Description | Who Uses It |
|---------|-------------|-------------|
| Computer account creation | Register new computer in specific AD Organisational Unit | IT admins, Designated operators |
| Computer account OU move | Move computer object to different OU | IT admins, Designated operators |
| Computer account reset | Reset computer account password/status | IT admins, Designated operators |

### Request & Approval Workflow

| Feature | Description | Who Uses It |
|---------|-------------|-------------|
| Request submission | Any identity change creates tracked request in Request_LST | All users (automatic upon form submission) |
| Request approval (manager) | Manager approves subordinate's profile change request | Managers |
| Request approval (admin) | Admin approves governed operations (group deletion, shared resources) | Company admins, System admins |
| Request rejection | Approver rejects request with reason | Managers, Admins |
| Request cancellation | Submitter withdraws pending request before approval | Request submitters |
| Request status tracking | View current status (WaitingForApproval → Processing → Completed) | Request submitters, Approvers |
| Request history | View full approval and execution history for a request | All users (own requests), Admins (all requests) |

### Administration Features

| Feature | Description | Who Uses It |
|---------|-------------|-------------|
| System-wide user list | View and search all users across all companies | System admins |
| Company-scoped user list | View and manage users within assigned companies | Company admins |
| Admin user edit | Edit any user attributes across all companies | System admins |
| Company admin user edit | Edit users within assigned company scope | Company admins |
| Pending request administration | Review and process all pending approval requests | System admins, Company admins |
| Leave application management | Manage employee leave/separation workflows and records | HR admins, System admins |
| Initial password list | View new users requiring password initialisation | IT admins, System admins |
| Master maintenance | Configure system master data tables (companies, OUs, mail domains, etc.) | System admins |
| Maintenance mode | Restrict IDM Web access to admins only (emergency operations) | System admins |

---

## Replatforming Implications

| Bucket | Replatforming Guidance |
|--------|----------------------|
| **Sync Engine** | Replace with SCIM/REST API-driven provisioning per [PROVISIONING_ARCHITECTURE_PRINCIPLES.md](PROVISIONING_ARCHITECTURE_PRINCIPLES.md). No ADSI. JML automation scope must be confirmed with Avanade before finalising. |
| **Self-Service** | Requires a dedicated portal/workflow platform decision. Must support approval workflows, multi-company scoping, and group/resource management. Separate from the sync engine platform selection. |

---

## Critical Unknowns / Gaps

Per `knowledge/platforms/JPIDM.md`, the following are **explicitly not documented** in authoritative sources:

| Unknown | Impact |
|---------|--------|
| **JML automation logic** | Unclear if JPIDM auto-provisions AD accounts from EINS/GHD feed, or if admins do it manually via portal. Directly affects Sync Engine scope. |
| **Source-of-authority data flow** | Exact flow from HR → EINS → GHD → JPIDM not fully documented. |
| **Scope of automated provisioning** | Who gets accounts automatically vs. who requires manual provisioning is unknown. |
| **Entra ID Connect relationship** | How JPIDM-managed AD accounts sync to Entra ID is presumed (via Entra ID Connect) but not documented. |

### Discovery Actions Required

Before finalising replatforming scope:

1. Interview Avanade operations team to document actual MIM sync job behaviour
2. Review MIM runbooks to understand automated provisioning scope
3. Analyse IDM_DB `Request_LST` historical data to determine ratio of automated vs. manual operations
4. Survey JPIDM users to identify actively used portal features vs. legacy/unused
5. Investigate APIDM (Asia-Pacific) — separate instance with unknown capabilities

---

## References

- [knowledge/platforms/JPIDM.md](platforms/JPIDM.md)
- [knowledge/platforms/JPIDM/FEATURES.md](platforms/JPIDM/FEATURES.md)
- [knowledge/platforms/JPIDM/ARCHITECTURE.md](platforms/JPIDM/ARCHITECTURE.md)
- [knowledge/platforms/JPIDM/SYSTEM_CONTEXT.md](platforms/JPIDM/SYSTEM_CONTEXT.md)
- [knowledge/platforms/JPIDM/RELATIONSHIPS.md](platforms/JPIDM/RELATIONSHIPS.md)
- [PROVISIONING_ARCHITECTURE_PRINCIPLES.md](PROVISIONING_ARCHITECTURE_PRINCIPLES.md)

---

*Document prepared by Platform Expert — 2026-03-25*
