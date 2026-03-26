# Identity Platform Feature Map
## JPIDM · Passport (SailPoint) · EINS

**Document Purpose:** Defines the functional scope and boundaries of each identity platform  
**Last Updated:** March 2026  
**Audience:** Identity architects, service owners, modernization planners

---

## Platform Summary

| | **EINS** | **Passport (SailPoint)** | **JPIDM** |
|--|---------|------------------------|----------|
| **Full Name** | Enterprise Id-management Networking Service | SailPoint IdentityIQ | Japan Identity Management |
| **Primary Role** | Global identity directory & Global ID authority | IAM orchestration & governance platform | Japan self-service IDM portal & AD orchestration |
| **Architecture Type** | System of Identity Truth | System of Action | System of Action (regional) |
| **Regional Scope** | Global (Sony Group) | Americas, EMEA, SM | Japan only |
| **Platform** | Custom enterprise service | SailPoint IdentityIQ | ASP.NET MVC 4 (VB.NET); SQL Server (IDM_DB); reads from EINS/GHD; writes to AD/Exchange |
| **Integration Target** | All consuming systems via file-based interfaces | On-prem AD, Exchange, connected apps | On-prem AD (JP.SONY.COM), Exchange |

---

## Core Role Summary

### EINS — *Who is this person?*
> EINS answers the identity question. It issues and maintains the canonical Global ID (GID), collects workforce attributes from company HR systems, and distributes authoritative identity data to downstream platforms. It does **not** provision accounts, manage access, or authenticate users.

### Passport — *What can this person access?*
> Passport orchestrates access lifecycle. For employees, it reacts to Workday events (Workday is the system of entry). For contingent workers, it **is** the system of entry. It provisions accounts in AD and connected applications, manages entitlements, and governs access reviews.

### JPIDM (IDM Web) — *Japan employees managing AD identity, groups, and shared objects*
> IDM Web is Sony Japan's self-service portal and AD orchestration layer. It reads identity truth from EINS/GHD and provides users, managers, and admins with self-service capabilities covering: security group management (internal + external with expiration), shared mailboxes, shared accounts, computer accounts, resource/room accounts, user profile edits, and request/approval workflows. It has **no access governance, no certification, and no compliance reporting** — those belong to Passport. It does **not** handle credentials — password reset and unlock are AD-native. JPIDM.md's reference to MIM reflects backend sync infrastructure only, not the IDM Web portal.

---

## Feature Map by Capability Domain

### 1. Identity & Global ID Management

| Capability | EINS | Passport | JPIDM |
|-----------|------|----------|-------|
| Global ID issuance | ✅ Authority | ❌ | ❌ |
| Global ID uniqueness enforcement | ✅ | ❌ | ❌ |
| Employee identity aggregation (from HR systems) | ✅ | ❌ | ❌ |
| Non-employee / contractor identity record | ✅ (record only) | ✅ (with provisioning) | ❌ |
| Identity correlation (GID ↔ network ID ↔ apps) | ✅ (source) | ✅ (orchestration) | ❌ |
| Employment type classification | ✅ | ✅ | ❌ |

---

### 2. Account Provisioning & Lifecycle

| Capability | EINS | Passport | JPIDM |
|-----------|------|----------|-------|
| Employee account creation | ❌ | ✅ (Workday-driven) | ❌ (reads from EINS/GHD; does not create employee AD accounts) |
| Contingent worker account creation | ❌ | ✅ (Passport-driven) | ✅ (Japan — JPIDM receives EINS feed after EINS Online Registration and provisions AD account; entry point is EINS, not IDM Web forms) |
| User profile / mail settings self-service | ❌ | ✅ | ✅ (display name, mail alias, mailbox size, mail domain) |
| Joiner / Mover / Leaver automation | ❌ | ✅ | ❌ (request-driven only; no automated JML triggers) |
| Account deprovisioning / termination | ❌ | ✅ | ✅ (leave/separation request workflow) |
| Shared / service account management | ❌ | ✅ | ✅ (create, edit, delete — SharedAccountController) |
| Computer account management | ❌ | ❌ | ✅ (create, OU-move, reset computer accounts in AD) |
| Resource / room account management | ❌ | ❌ | ✅ (register and manage meeting room / equipment mailboxes) |
| Company affiliation / org attribute management | ❌ | ❌ | ✅ (including 兼務者 multi-affiliation) |

---

### 3. Group & Entitlement Management

| Capability | EINS | Passport | JPIDM |
|-----------|------|----------|-------|
| Internal AD security group creation | ❌ | ✅ | ✅ (self-service, JP only) |
| External AD security group (with expiry) | ❌ | ✅ | ✅ (expiration date required) |
| Security group member add / remove | ❌ | ✅ | ✅ (up to 200 members via UI) |
| Bulk member management (CSV upload) | ❌ | ❌ | ✅ (batch add/remove via file upload) |
| Group expiration extension / reactivation | ❌ | ❌ | ✅ (extend or re-enable expired groups) |
| Group member list export | ❌ | ❌ | ✅ (downloadable member list) |
| Mailing list (ML/distribution group) creation | ❌ | ❌ | ✅ (self-service) |
| Shared mailbox management | ❌ | ✅ | ✅ (create, member edit, alias, forwarding — SharedMailboxController) |
| Request & approval workflows | ❌ | ✅ | ✅ (manager / company admin / system admin approval via RequestController) |
| Entitlement assignment via automated workflows | ❌ | ✅ | ❌ (request-driven, not automated event-driven) |
| Alt-recipient / mail forwarding for groups | ❌ | ❌ | ✅ |

---

### 4. Credential & Password Management

| Capability | EINS | Passport | JPIDM |
|-----------|------|----------|-------|
| Password reset (self-service) | ❌ | ✅ | ❌ (NOT IDM Web — authoritative FEATURES.md: "AD native / Passport") |
| Password change | ❌ | ✅ | ❌ (AD-native; IDM Web does not handle credentials) |
| Account unlock | ❌ | ✅ | ❌ (AD-native; IDM Web does not handle credentials) |
| Password synchronization (AD/M365) | ❌ | ✅ | ❌ |
| MFA management | ❌ | ⚠️ Partial (Thales, RSA) | ❌ |
| Phishing-resistant credential support | ❌ | ❌ | ❌ |

> **Correction:** JPIDM.md referenced MIM for password reset / unlock. The authoritative codebase FEATURES.md explicitly lists "Password reset portal" as **NOT** an IDM Web capability — it is handled by AD-native tools and/or Passport. MIM is backend sync infrastructure, not the IDM Web portal function.

> **Note:** Passport is in migration toward Entra ID SSPR for credential management.

---

### 5. Access Governance & Reviews

| Capability | EINS | Passport | JPIDM |
|-----------|------|----------|-------|
| Access certification / reviews | ❌ | ✅ | ❌ |
| Manager attestation | ❌ | ✅ | ❌ |
| Ownership attestation (service accounts) | ❌ | ✅ | ❌ |
| Compliance reporting | ❌ | ✅ | ❌ |
| Audit traceability (who approved/provisioned) | ❌ | ✅ | ✅ (full request + approval log in Request_LST table) |

---

### 6. Data Quality & Distribution

| Capability | EINS | Passport | JPIDM |
|-----------|------|----------|-------|
| Authoritative identity data publication | ✅ | ❌ | ❌ |
| Scheduled data distribution (file-based) | ✅ | ❌ | ❌ |
| Data completeness / duplicate validation | ✅ | ❌ | ❌ |
| Attribute exposure control (public/private) | ✅ | ❌ | ❌ |
| Organization hierarchy management | ✅ | ❌ | ❌ |
| Company master management | ✅ | ❌ | ❌ |
| PKI-secured data transmission | ✅ | ❌ | ❌ |

---

### 7. Authentication & Access Control

| Capability | EINS | Passport | JPIDM |
|-----------|------|----------|-------|
| Authentication | ❌ | ❌ (delegated to AD/Entra) | ❌ (delegated to AD) |
| SSO enablement | ❌ | ❌ | ❌ |
| Conditional access policies | ❌ | ❌ | ❌ |
| MFA enforcement | ❌ | ❌ | ❌ |

> **Note:** None of these platforms handle authentication directly. Authentication is owned by **Entra ID / Active Directory** across all regions.

---

### 8. Platform Operations & Governance

| Capability | EINS | Passport | JPIDM |
|-----------|------|----------|-------|
| Regional scope restriction | Global (Sony Group) | AM / EU / SM | JP only |
| Change management (CAB) | ❌ (documented) | ❌ (documented) | ✅ (confirmed) |
| Service availability management | ✅ | ✅ | ✅ |
| Company/tenant onboarding | ✅ | ✅ | ❌ |
| API/connector management | ❌ | ✅ (AD, Exchange, app connectors) | ✅ (ADAccessor → AD; Exchange via service account) |
| Admin-scoped user management | ❌ | ✅ | ✅ (company-scoped admin and system-wide admin views) |
| Maintenance mode / portal lockdown | ❌ | ❌ | ✅ (Web.config MaintenanceMode flag) |
| Platform availability SLA | ✅ | ✅ | ✅ |

---

## System-of-Record Summary

| Identity Population | System of Entry | Orchestration / Action | Directory of Truth |
|--------------------|-----------------|----------------------|-------------------|
| **FTE Employees** | Workday → EINS/GHD | Passport (AM/EU/SM), IDM Web (JP, profile/group changes only) | EINS (Global ID) |
| **Contingent Workers** | Passport (AM/EU/SM), EINS Online Registration (JP) | Passport (AM/EU/SM), JPIDM (JP — AD provisioning via EINS feed) | EINS (Global ID) |
| **Shared / Service Accounts** | IDM Web (JP), Passport (AM/EU) | IDM Web (JP AD objects), Passport (AM/EU) | ❌ No global authority |
| **Admin Accounts** | Entra ID (Tenant) | Entra ID | Entra ID |
| **Guest Accounts** | Azure AD B2B | Azure AD | Azure AD |

---

## Capability Gaps (Current State vs Ideal)

| Capability Needed | Where It's Missing | Impact |
|------------------|-------------------|--------|
| Unified credential management | JPIDM / Passport split | Inconsistent user experience; two migration tracks needed |
| Global non-human account governance | All platforms | No authoritative service account registry |
| Contingent worker lifecycle outside AM/EU/JP | No platform covers AP | AP accounts created ad-hoc or unknown |
| Self-service security group management (non-JP) | IDM Web is JP-only | AM/EU/AP require service requests for group changes |
| Access governance for JP users | IDM Web has no certification or compliance capability | No attestation or compliance reporting for JP entitlements |
| Password self-service for JP users | IDM Web explicitly does not handle credentials | JP users depend on AD-native reset or a separate MIM portal |
| Single provisioning source of truth | Passport + JPIDM + APIDM | Fragmented; EUSP has limited visibility |

---

## Architectural Boundaries (What Each Platform Must NOT Own)

| Platform | Must NOT Own |
|---------|-------------|
| **EINS** | Account provisioning, authentication, access governance — remains identity directory only |
| **Passport** | Global ID authority, identity truth (that is EINS) — Passport reacts, does not author |
| **IDM Web (JPIDM)** | Cross-region scope, access governance, compliance reporting, automated JML — JP portal scope only; credential management belongs to AD, not IDM Web |

---

## Migration Implications

| Platform | Modernization Direction |
|---------|------------------------|
| **EINS** | Remain stable; protect as foundational layer; direct Workday → Entra ID provisioning reduces dependency |
| **Passport** | Evaluate replacement path as Entra ID Lifecycle Workflows mature; contingent worker flows still require governance |
| **IDM Web (JPIDM)** | Modernize toward unified cloud-native platform (Entra ID / Lifecycle Workflows); security group and shared account self-service to be replaced; credential ops already delegated to AD-native; MIM backend infrastructure under review for decommission |

---

## Terms Reference

| Term | Definition |
|------|-----------|
| **GID / Global ID** | EINS-issued unique identifier for every Sony Group individual |
| **System of Entry** | The platform where an identity is first created |
| **System of Truth** | The authoritative source for identity data |
| **System of Action** | A platform that reacts to identity events to provision/govern access |
| **MIM** | Microsoft Identity Manager; backend sync infrastructure used by JPIDM (IDM Web) for AD/Exchange changes; end-of-life platform under review |
| **IDM Web** | The actual JPIDM application; ASP.NET MVC 4 (VB.NET) self-service portal fronting Active Directory for JP users |
| **SSPR** | Self-Service Password Reset; Entra ID feature replacing JPIDM/Passport credential ops |
| **Passport** | SailPoint IdentityIQ instance used in AM/EU/SM |
| **EINS Online** | Web registration portal for contractors when HR system not available |
| **CAB** | Change Advisory Board |

---

**Related Documents:**  
[EINS.md](EINS.md) · [Passport.md](Passport.md) · [JPIDM.md](JPIDM.md) · [GISC_Global_Identity_Service_Reformatted.md](GISC_Global_Identity_Service_Reformatted.md)
