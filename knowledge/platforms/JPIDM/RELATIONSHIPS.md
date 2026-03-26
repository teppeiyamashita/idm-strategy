# IDM Web – System Relationships & Architectural Comparisons

---

## Overview

IDM Web operates within a broader Sony enterprise identity ecosystem. Understanding how it relates to EINS, Passport, Active Directory, and GHD is essential for architecture decisions, modernization planning, and scope governance.

---

## 1. IDM Web vs EINS

### Executive Comparison

| Dimension | IDM Web | EINS |
|-----------|---------|------|
| **Primary Role** | Self-service portal & AD orchestration | Enterprise identity directory |
| **Region** | Japan | Global (Sony Group) |
| **System of Identity Truth** | ❌ | ✅ |
| **Global ID Authority** | ❌ | ✅ |
| **Account Provisioning** | ✅ | ❌ |
| **Self-Service UI** | ✅ | ❌ |
| **Request Workflows** | ✅ | ❌ |
| **Data Origination (Employees)** | ❌ Reads from EINS/GHD | ✅ Collects from HR systems |
| **Population Coverage** | Japan Sony entities | All Sony Group companies |
| **Authentication** | ❌ (relies on Windows Auth) | ❌ |

### Integration Point

```
GHD/EINS
    │  (identity data feed into IDM_DB)
    ▼
IDM_DB (GHD_PersonalInfo_MST, GHD_OrganizationInfo_MST)
    │  (read by IDM Web services)
    ▼
IDM Web Portal (user requests changes)
    │  (approved changes pushed to AD)
    ▼
Active Directory (authoritative account store)
```

### Key Distinction

EINS answers **"Who is this person across Sony Group?"**
IDM Web answers **"What do Japan users need to change in AD today?"**

---

## 2. IDM Web vs Passport (SailPoint)

### Executive Comparison

| Dimension | IDM Web | Passport (SailPoint) |
|-----------|---------|---------------------|
| **Primary Role** | Japan self-service portal | AM/EU/SM IAM governance platform |
| **Technology** | ASP.NET MVC 4, VB.NET | SailPoint IdentityIQ |
| **Region** | Japan | AM, EU, SM |
| **Account Provisioning** | ✅ Direct to AD | ✅ Via SailPoint workflows |
| **Request/Approval Workflow** | ✅ Custom built | ✅ SailPoint native |
| **Access Governance** | ❌ | ✅ |
| **Access Certification** | ❌ | ✅ |
| **Compliance Reporting** | ❌ | ✅ |
| **Contingent Worker AD Provisioning** | ✅ For Japan (receives EINS feed; provisions AD account) | ✅ For AM/EU/SM |
| **Workday Integration** | ❌ | ✅ (via connector) |
| **Password Management** | ❌ AD-native | ✅ Password reset portal |
| **Shared Mailbox Management** | ✅ | ✅ |
| **Group Lifecycle** | ✅ With expiration | ✅ |

### Capability Overlap

| Capability | IDM Web | Passport | Authority |
|-----------|---------|---------|-----------|
| Security group creation | ✅ Japan | ✅ AM/EU/SM | Regional split |
| Shared mailbox creation | ✅ Japan | ✅ AM/EU/SM | Regional split |
| User profile editing | ✅ Japan | ✅ AM/EU/SM | Regional split |
| Access certification | ❌ | ✅ | Passport only |
| Access request workflow | ✅ | ✅ | Regional split |
| Compliance/audit trail | ❌ | ✅ | Passport only |
| Computer accounts | ✅ Japan | ❌ | IDM Web only |

### Key Distinction

Passport answers **"What access does this person have, and is it appropriate?"**
IDM Web answers **"Japan employees need to make these identity and group changes"**

---

## 3. IDM Web vs Active Directory

### Role Relationship

| Aspect | IDM Web | Active Directory |
|--------|---------|-----------------|
| Role | UI and workflow layer | Authoritative account/group store |
| Creates accounts | ✅ (via service account) | ❌ (storage only) |
| Enforces identity truth | ❌ | ❌ (AD is a target, not source) |
| Authentication | ❌ (delegates to AD) | ✅ |
| Group membership | ✅ (requests via UI) | ✅ (persistent enforcement) |
| Password management | ❌ (AD enforces policy) | ✅ |
| Computer objects | ✅ (create/move/reset via UI) | ✅ (persistent storage) |

### Integration Pattern

```
IDM Web (user request approved)
    │  System.DirectoryServices
    ▼
ADAccessor.vb
    │  Bind to LDAP/GC
    ▼
Active Directory (JP.SONY.COM)
    ├── User accounts
    ├── Security groups
    ├── Shared accounts
    ├── Computer accounts
    └── Resource mailboxes
```

---

## 4. IDM Web vs GHD (Global HR Database)

### Role Relationship

| Aspect | IDM Web | GHD |
|--------|---------|-----|
| Role | Reads employee data | Source of HR truth |
| Data direction | Consumer | Producer |
| Modification rights | Read-only | ❌ via IDM Web |
| Employee identity | Reads from GHD_PersonalInfo_MST | ✅ Authoritative |
| Org hierarchy | Reads from GHD_OrganizationInfo_MST | ✅ Authoritative |
| Link tracking | GHD_LinkStatus_MST (read) | ✅ Managed externally |

---

## 5. Common Confusion Points

### Misconception 1: "IDM Web creates employee identities"

**Reality**: IDM Web reads employee data from GHD/EINS. Employees must exist in GHD/EINS before appearing in IDM Web. IDM Web edits attributes of existing accounts, it does not originate identities.

**Why This Matters**: Onboarding delays are EINS/GHD issues, not IDM Web issues.

---

### Misconception 2: "IDM Web and Passport do the same thing"

**Reality**: IDM Web is the Japan-specific portal; Passport covers AM/EU/SM. They are parallel regional implementations, not alternatives.

**Why This Matters**: Modernization should not assume replacing one automatically replaces the other.

---

### Misconception 3: "IDM Web manages access governance"

**Reality**: IDM Web tracks request/approval status for identity changes. It does NOT perform periodic access reviews, certification campaigns, or compliance reporting. Those are Passport capabilities.

**Why This Matters**: Access governance requirements for Japan must be addressed separately.

---

### Misconception 4: "IDM Web is the system of record"

**Reality**: IDM Web is a system of action. It reads from EINS/GHD, executes operations in AD, and tracks change history. The authoritative sources remain EINS (identity), GHD (HR data), and AD (accounts).

---

## 6. System Interaction Patterns

### Pattern 1: Employee Profile Update

```
Employee (browser)
    │ POST /User/EditUser
    ▼
UserController.EditUser()
    │
    ├── UserService.GetUserInfo() ← reads Account_MST, GHD data
    │
    ├── UserService.SaveUserInfo() → Request_LST (status: WaitingForApproval)
    │
    └── Manager approves via RequestController.RequestApproval()
              │
              ▼
          ADAccessor (modify AD attributes)
              │
              ▼
          Active Directory (updated)
```

### Pattern 2: Security Group Creation

```
Admin/User (browser)
    │ POST /Group/CreateInternalGroup
    ▼
GroupController.CreateInternalGroup()
    │
    ├── GroupService.ValidateGroup()
    │
    ├── GroupService.CreateGroup() → Request_LST (status: Processing)
    │
    └── ADAccessor.CreateGroup() → Active Directory
```

### Pattern 3: Admin Leave Processing

```
Admin
    │ POST /Admin/LeaveApplicationListDetailManager
    ▼
AdminController.LeaveApplicationListDetailManager()
    │
    ├── AdminService.GetLeaveApplication() ← EmployeeLeave_LST
    │
    └── AdminService.ProcessLeave() → Account_MST (disable account)
              │
              ▼
          ADAccessor (disable AD user)
```

---

## 7. Modernization Considerations

| Question | Answer |
|----------|--------|
| Can IDM Web be replaced by direct Workday → Entra provisioning for FTEs? | Partially. For employee self-service edits, IDM Web adds value beyond basic provisioning. |
| Can Passport replace IDM Web for Japan? | Possibly in the long term, but Japan-specific features (computer accounts, regional AD ops) need evaluation. |
| What dependencies does IDM Web have on EINS? | Heavy — GHD data tables are the backbone of all employee look-ups. |
| Can IDM Web be migrated to Entra ID governance? | The governance layer (approvals, certifications) could route through Entra ID Governance, but AD LDAP operations need connector coverage. |

---

## 8. Related Documentation

- [SYSTEM_CONTEXT.md](./SYSTEM_CONTEXT.md) - IDM Web system purpose and scope
- [ARCHITECTURE.md](./ARCHITECTURE.md) - Internal system design
- [FEATURES.md](./FEATURES.md) - Detailed capability matrix

---

*End of document*
