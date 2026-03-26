# IDM Web – Identity Data Management Web Application

## System Context, Scope, and Feature Capabilities

---

## 1. Executive Summary

**IDM Web** (Identity Data Management Web) is Sony Japan's **self-service identity and access management portal** for managing employee accounts, security groups, shared resources, and identity-related workflows within the Sony Japan IT environment.

Its primary purpose is to:
- **Provide a self-service interface** for employees to manage their own identity attributes, mail settings, and group memberships
- **Enable administrators and managers** to perform account operations, approvals, and identity governance tasks
- **Orchestrate identity changes** across Active Directory, Exchange, and connected enterprise systems
- **Track and approve** identity-related requests through a structured workflow engine

Architecturally:
- IDM Web is a **system of action** — it reads identity truth from EINS/GHD and executes provisioning operations in AD/Exchange
- It is a **request and approval orchestration layer** sitting between end users and backend systems
- It is **not the system of record** for identity data (EINS/GHD are authoritative sources)

---

## 2. System‑of‑Record vs System‑of‑Action

### 2.1 Identity Authority Model

| Aspect | Reality |
|--------|---------|
| Identity Scope | Sony Japan employees, contractors, shared accounts, computer accounts |
| System Role | Self-service portal and orchestration layer |
| Identity Origination | EINS (via GHD) for employees; IDM Web for shared/resource accounts |
| User Authentication | Windows Integrated Authentication (Corporate AD) |
| Account Provisioning | ✅ Supported (creates/modifies AD objects) |
| Request Approval Workflows | ✅ Supported |
| Identity Truth Authority | ❌ Not authoritative (reads from EINS/GHD) |
| Global ID Authority | ❌ Not supported (EINS is authoritative) |
| IAM Governance Platform | ❌ Not supported (Passport/SailPoint handles governance) |

---

### 2.2 Relationship to HR and Identity Systems

IDM Web **does not replace EINS or Passport**:
- **EINS** remains the system of identity truth and Global ID authority
- **GHD** (Global HR Database) remains the source of employee/org data
- **Passport** handles IAM governance, access reviews, and certifications
- IDM Web **executes operations** based on user requests, driven by data from the above systems

---

## 3. Population Coverage

IDM Web manages identities for the following populations:

| Population | Direct Access | Admin Managed | Notes |
|-----------|---------------|---------------|-------|
| Regular employees | ✅ Self-service | ✅ Manager/Admin | Profile edits, mail settings |
| Managers | ✅ Self-service + team mgmt | ✅ Admin | Can edit subordinate profiles |
| Company Admins | ✅ Self-service + company scope | ✅ System Admin | Multi-company management |
| System Admins | ✅ Full access | — | All IDM functions |
| Shared accounts | ❌ Via admin/owner | ✅ | Service accounts, shared mailboxes |
| Computer accounts | ❌ Via admin | ✅ | AD computer objects |
| Resource accounts | ❌ Via admin | ✅ | Meeting rooms, equipment |
| External/contractor groups | ❌ Via admin | ✅ | External security groups |

---

## 4. Data Sources & Registration Models

### 4.1 GHD-Driven Identity (Primary for Employees)

- Employee and organization data is read from `GHD_PersonalInfo_MST`, `GHD_OrganizationInfo_MST`, `GHD_PersonalORGInfo_MST`
- Global IDs, employment types, and org hierarchy come from GHD/EINS
- This data is **read-only** in IDM Web — modifications flow back through EINS/Passport

---

### 4.2 IDM Web Self-Registration (For Shared/Resource Objects)

Used when:
- Creating shared mailboxes, shared service accounts, computer accounts, resource mailboxes

Mechanism:
- Authorized users (admins, designated owners) register objects via IDM Web forms
- Triggers provisioning workflows in Active Directory and Exchange

---

### 4.3 Request-Approval Workflow (For Governed Changes)

Used for:
- All changes to accounts, groups, and shared resources that require approval
- Approval routes to manager, company admin, or system admin depending on function

Function IDs embedded in `Request_LST` table track request type, status, and history.

---

## 5. What IDM Web Explicitly Does NOT Do

| Capability | Supported |
|-----------|-----------|
| Global ID issuance | ❌ (EINS authority) |
| Identity truth / system of record | ❌ (EINS/GHD authority) |
| IAM governance and access reviews | ❌ (Passport/SailPoint) |
| Authentication provider | ❌ (Corporate AD via Windows Auth) |
| HR system replacement | ❌ (GHD/Workday are HR sources) |
| Direct Workday integration | ❌ (via EINS/GHD intermediary) |
| Entra ID / cloud provisioning | ❌ (Passport or direct connectors) |
| Password policy enforcement | ❌ (enforced by Active Directory) |

---

## 6. Data Distribution & Interface Model

### Key characteristics
- Operations are synchronous (user submits → workflow approves → AD/Exchange changes execute)
- Requests are tracked in `Request_LST` with status: WaitingForApproval → Processing → Completed/Rejected/Canceled
- All AD operations use DirectoryServices with a service account operator
- Exchange integration is via AD ProxyAddresses management and mailbox database assignments
- GHD data is consumed read-only via Entity Framework

---

## 7. IDM Web Feature Table (Authoritative)

| Main Category | Capability / Function | Example (Concrete) |
|--------------|----------------------|-------------------|
| **User Account Management** | Self-service profile edit | Employee changes display name, mail settings, proxy addresses |
| **User Account Management** | Manager-initiated profile edit | Manager changes subordinate's display name or mail domain |
| **User Account Management** | Mail alias management | Adding/removing email aliases from user account |
| **User Account Management** | Leave/separation processing | Submitting employee leave application with alternate mail settings |
| **Security Group Management** | Internal group creation | Creating a new internal AD distribution/security group |
| **Security Group Management** | External group creation | Creating an external group with expiration date |
| **Security Group Management** | Group member management | Adding/removing up to 200 members via UI |
| **Security Group Management** | Bulk member changes | Upload CSV to add/remove members in batch |
| **Security Group Management** | Group expiration extension | Extending expiry date of an existing external group |
| **Security Group Management** | Group reactivation | Re-enabling an expired group |
| **Security Group Management** | Group deletion request | Submitting group deletion request with admin approval |
| **Security Group Management** | Member list export | Downloading group members as file |
| **Shared Account Management** | Shared account creation | Registering a new shared service account in AD |
| **Shared Account Management** | Shared account modification | Changing shared account properties |
| **Shared Account Management** | Shared account deletion | Requesting shared account removal |
| **Shared Mailbox Management** | Shared mailbox creation | Creating a shared mailbox for a team |
| **Shared Mailbox Management** | Shared mailbox member management | Adding/removing delegates to a shared mailbox |
| **Shared Mailbox Management** | Bulk mailbox member edit | Uploading member list for shared mailbox |
| **Shared Mailbox Management** | Member list export | Downloading mailbox members as file |
| **Computer Account Management** | Computer account creation | Registering a new computer in the correct AD OU |
| **Computer Account Management** | Computer OU change | Moving computer account to different organizational unit |
| **Computer Account Management** | Computer account reset | Resetting computer account password |
| **Resource Management** | Meeting room registration | Creating resource mailbox for a conference room |
| **Resource Management** | Equipment registration | Registering equipment/projector as resource |
| **Resource Management** | Resource modification | Editing room/equipment properties |
| **Resource Management** | Resource deletion | Requesting resource removal |
| **Request & Approval Workflow** | Request submission | Any identity change triggers a tracked request |
| **Request & Approval Workflow** | Request approval | Manager/admin approves or rejects submitted requests |
| **Request & Approval Workflow** | Request cancellation | Submitter cancels a pending request |
| **Request & Approval Workflow** | Request history | Viewing full request and approval history |
| **Administration** | User list management | Admin views and manages all users in company scope |
| **Administration** | Request administration | Admin reviews and processes all pending requests |
| **Administration** | Leave application management | Admin manages employee leave/separation records |
| **Administration** | Maintenance mode | Restricting system access to admins only |

---

## 8. Relationship to Other Identity Systems

### IDM Web vs EINS

| Dimension | IDM Web | EINS |
|-----------|---------|------|
| Primary Role | Self-service portal & orchestration | Enterprise identity directory |
| System of Identity Truth | ❌ | ✅ |
| Global ID Authority | ❌ | ✅ |
| Account Provisioning | ✅ | ❌ |
| Self-service UI | ✅ | ❌ |
| Request Workflows | ✅ | ❌ |
| Data Origination (Employees) | ❌ Reads from EINS/GHD | ✅ Collects from HR systems |

### IDM Web vs Passport (SailPoint)

| Dimension | IDM Web | Passport |
|-----------|---------|---------|
| Primary Role | Self-service portal (Japan) | IAM governance platform (AM/EU/SM) |
| Region | Japan | AM, EU, SM |
| Account Provisioning | ✅ (direct AD) | ✅ (via SailPoint workflows) |
| Access Governance | ❌ | ✅ |
| Access Certification | ❌ | ✅ |
| Contingent Worker AD Provisioning | ✅ (Japan — receives EINS feed; provisions AD account) | ✅ (AM/EU/SM) |
| Request/Approval Workflow | ✅ | ✅ |
| Compliance Reporting | ❌ | ✅ |

---

## 9. Architectural Implications

1. **IDM Web is a regional system (Japan)**
   - Handles Japan-specific AD forest (JP.SONY.COM)
   - Company code and domain name scoped to Japan Sony entities

2. **EINS is a prerequisite, not a replacement**
   - Employee data must exist in EINS/GHD before IDM Web can process changes
   - IDM Web reads GHD as a data dependency, not a data source of truth

3. **Passport and IDM Web are parallel, not replacement**
   - Passport covers AM/EU/SM regions with SailPoint governance
   - IDM Web covers Japan with custom ASP.NET MVC portal
   - Both ultimately provision into Active Directory

4. **Clear separation of concerns**
   - EINS = Who is the person?
   - GHD = What is their HR data?
   - IDM Web = What do they request to change?
   - AD/Exchange = Where do changes persist?
   - Passport = What access governance applies?

---

## 10. Recommended Usage of This Document

This document is suitable for:
- Architecture reviews comparing IDM Web with EINS and Passport
- Onboarding new developers to IDM Web project
- Clarifying Japan vs. global IAM system responsibilities
- Preventing scope creep into EINS or Passport functionality
- Identity modernization planning for Japan region

---

*End of document*
