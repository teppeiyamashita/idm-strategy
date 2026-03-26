# Passport (SailPoint) – Identity & Access Management
## System Context, Scope, and Feature Capabilities

---

## Ownership & Responsibility

| Aspect | Team/Location |
|--------|---------------|
| **Platform Ownership** | Cloud App Team (lead by Ramnath) |
| **Management Team** | US East Coast (Jorge) |
| **Operations Team** | SISC India (Sony India Software Center) |
| **Regional Scope** | Americas (AM), EMEA/EU, SM regions |

---

## 1. Executive Summary

Passport is Sony’s **SailPoint IdentityIQ–based Identity and Access Management (IAM) platform** used primarily in **AM, EU, and SM regions**. Its role varies significantly depending on worker type:

- **For Full‑Time Employees (FTEs)**  
  Passport is **not the system of entry**.  
  It acts as a **bridge and orchestration layer**, synchronizing identity data **from Workday to downstream systems** (notably on‑prem Active Directory and connected applications).

- **For Contingent Workers / 3rd Parties**  
  Passport **is the system of entry**.  
  Hiring managers or delegated admins manually create identities in Passport, which then **drives account creation** in Active Directory and selected downstream systems.

This distinction is critical for:
- Architecture clarity
- Modernization planning (Workday ↔ Entra direct provisioning)
- Proper comparison with **EINS** and **Microsoft Entra ID**

---

## 2. System‑of‑Record vs System‑of‑Action

### 2.1 Full‑Time Employees (FTE)

| Aspect | Reality |
|------|--------|
| Authoritative HR System | **Workday** |
| Identity Origination | Workday |
| Passport Role | Synchronization / orchestration |
| Employee Recognition | Passport recognizes employees **only when the record is received from Workday** |
| Account Creation Trigger | Downstream of Workday events |
| Architectural Position | **Middleman (Workday → AD / Apps)** |

Passport documentation explicitly states that it **only recognizes individuals as employees when the record is received from Workday**, and that its role is to propagate Workday changes across the network via SailPoint workflows.

In other words, for employees:
> Passport does **not** author identities; it **reacts** to Workday.

---

### 2.2 Contingent Workers / Contractors / 3rd Parties

| Aspect | Reality |
|------|--------|
| Authoritative Source | **Passport** |
| Identity Origination | Manual entry in Passport |
| Data Entry | Hiring manager or delegated admin |
| Account Creation Trigger | Passport |
| Target Systems | On‑prem AD, selected downstream apps |
| Architectural Position | **System of Entry + Orchestration** |

For non‑Workday populations, Passport is explicitly documented as the **manual registration point**, using the *Create Contingent Worker* flow. Once registered, Passport initiates provisioning actions.

---

## 3. Why Passport Documentation Often Appears Ambiguous

Many Passport overview documents use broad language such as:

> “Passport provides automation for employee and contingent worker onboarding (e.g. creation of network, email, O365 accounts).”

While factually correct, this wording **does not distinguish**:
- Who originates the identity
- Who is authoritative
- Whether Passport is reacting to or initiating provisioning

Architecturally, this has led to confusion.  
The **accurate model** is:

- **Employees** → *Workday‑driven, Passport‑orchestrated*  
- **Contingent workers** → *Passport‑driven end‑to‑end*

---

## 4. Passport Feature Table (Architecturally Precise)

> Format intentionally aligned with the EINS feature table for side‑by‑side comparison.

| Main Category | Capability / Function | Example (Concrete) |
|--------------|----------------------|-------------------|
| Identity Core & Account Management | Employee identity synchronization (Workday‑driven) | Receiving employee records from Workday and synchronizing attributes into on‑prem AD |
| Identity Core & Account Management | Contingent worker identity creation (Passport‑driven) | Hiring manager manually creates a contractor using the “Create Contingent Worker” form |
| Identity Core & Account Management | Identity correlation | Linking Global ID, network ID, and application accounts under one identity |
| Identity Core & Account Management | Account lifecycle management | Disabling AD and application access when a worker is terminated |
| Person & Workforce Identity Data | Employee vs non‑employee classification | Treating Workday‑fed identities as employees and manual entries as contingent workers |
| Person & Workforce Identity Data | Policy acceptance enforcement | Requiring contingent workers to accept corporate policies at first login |
| Access Provisioning & Deprovisioning | Employee provisioning (orchestrated) | Propagating Workday joiner events into AD and connected applications |
| Access Provisioning & Deprovisioning | Contingent worker provisioning (authoritative) | Creating an AD account for a contractor based on Passport entry |
| Access Provisioning & Deprovisioning | Joiner / Mover / Leaver workflows | Adjusting access when Workday job data changes |
| Access Provisioning & Deprovisioning | Group and entitlement management | Assigning security groups via SailPoint workflows |
| Access Request & Self‑Service | Self‑service access requests | User requests application access through Passport |
| Access Request & Self‑Service | Approval workflows | Manager or app owner approves access requests |
| Access Request & Self‑Service | Shared mailbox management | Requesting and managing shared mailboxes via Passport |
| Access Request & Self‑Service | Generic / service account management | Creating and assigning ownership of non‑human accounts |
| Password & Credential Management | Password change | User changes network password via Passport |
| Password & Credential Management | Password reset | Resetting password using security questions |
| Password & Credential Management | Account unlock | Unlocking a locked network account |
| Password & Credential Management | Password synchronization | Syncing password changes to AD, email, and M365 |
| Governance & Access Reviews | Access certification | Periodic manager review of user access |
| Governance & Access Reviews | Ownership attestation | Validating continued need for service or generic accounts |
| Governance & Access Reviews | Compliance reporting | Producing audit evidence for access reviews |
| Administration & Operations | Workflow configuration | Defining SailPoint joiner / mover / leaver workflows |
| Administration & Operations | Connector management | Operating AD, Exchange, and application connectors |
| Administration & Operations | Operational support | Handling Passport incidents and provisioning failures |
| Service Management & Platform Operations | Regional scope enforcement | Restricting Passport to AM / EU / SM companies |
| Service Management & Platform Operations | Platform availability | Operating Passport as a centralized IAM service |
| Security & Compliance Controls | Identity governance enforcement | Enforcing least‑privilege via access reviews |
| Security & Compliance Controls | Credential policy enforcement | Enforcing password complexity and rotation |
| Security & Compliance Controls | Authentication control limitations | Reliance on security questions for reset (non‑phishing‑resistant) |
| Security & Compliance Controls | Audit traceability | Tracking who approved, provisioned, or modified access |

---

## 5. Relationship to Other Identity Systems

### Passport vs EINS (at a glance)

| Dimension | Passport | EINS |
|--------|---------|------|
| Primary Role | IAM orchestration & governance | Enterprise identity directory |
| System of Entry (Employees) | ❌ Workday | ❌ Workday |
| System of Entry (Contingent) | ✅ Passport | ⚠️ EINS (ID registry only) |
| Account Provisioning | ✅ Yes | ❌ No |
| Global ID Authority | ❌ | ✅ |
| Governance & Reviews | ✅ | ❌ |

Passport is a **system of action**, while EINS is a **system of identity truth**.

---

## 6. Architectural Implications (Key Takeaways)

1. **Passport is removable from the employee provisioning path**  
   Because employees originate in Workday, direct **Workday → Entra ID** provisioning is architecturally feasible.

2. **Passport remains valuable for non‑employee identities**  
   Contingent workers, service accounts, and non‑human identities still require lifecycle governance.

3. **This distinction should be explicit in all future diagrams**  
   Any “Passport creates employee accounts” statement should be treated as **imprecise shorthand**, not architectural truth.

---

## 7. Recommended Usage of This Document

This MD is suitable for:
- Identity strategy decks
- EINS / Passport / Entra comparison
- Mid‑term modernization planning
- Architecture review boards
- Executive “what does Passport actually do?” conversations

---

*End of document*
``
