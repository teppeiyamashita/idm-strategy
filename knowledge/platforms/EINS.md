# EINS – Enterprise Id‑management Networking Service   that collects employee and organization attributes and provides them to internal systems, while centrally managing Global ID issuance. [1](https://sonyjpn.sharepoint.com/sites/S099-070-eins/Documents_new/EINS_Overview(J).pdf?web=1)

---

### 2.2 Relationship to HR Systems

EINS **does not replace HR systems**.

Instead:
- HR systems (local or regional) remain the **systems of record**
- EINS aggregates identity data from those systems
- Each company is responsible for the **accuracy and timeliness** of submitted data [2](https://sonyjpn.sharepoint.com/sites/S099-070-eins/all_document/EINS_Guideline_for_companies(E).pdf?web=1)

---

## 3. Population Coverage

EINS explicitly supports **both employees and non‑employees**, but only as **identity records**, not accounts.

### Covered populations include:
- Permanent employees
- Fixed‑term / contract employees
- Expatriates
- Dispatch workers
- Contractors / outsourced workers

These populations are defined in EINS guidelines as **identity data to be included**, regardless of employment type [2](https://sonyjpn.sharepoint.com/sites/S099-070-eins/all_document/EINS_Guideline_for_companies(E).pdf?web=1)[3](https://sonyjpn.sharepoint.com/sites/S099-070-eins/all_document/EINS_Guideline_for_companies(J).pdf?web=1).

---

## 4. Identity Data Sources & Registration Models

EINS supports **three explicit data intake models**, all documented.

### 4.1 HR‑System‑Driven Registration (Primary)

- Employee and organization data is sent from company HR systems
- Global ID is issued based on received HR data
- This is the **default and preferred** model [1](https://sonyjpn.sharepoint.com/sites/S099-070-eins/Documents_new/EINS_Overview(J).pdf?web=1)

---

### 4.2 EINS Online Registration (Supplemental)

Used when:
- HR systems do not manage certain worker populations (e.g., contractors)

Mechanism:
- Authorized company representatives register individuals via EINS Online
- Registration requires organizational approval [1](https://sonyjpn.sharepoint.com/sites/S099-070-eins/Documents_new/EINS_Overview(J).pdf?web=1)

---

### 4.3 Email‑Based Registration (Exceptional)

Used when:
- A company is not fully onboarded to EINS

Mechanism:
- Registration forms submitted by email
- EINS team performs manual registration [1](https://sonyjpn.sharepoint.com/sites/S099-070-eins/Documents_new/EINS_Overview(J).pdf?web=1)

---

## 5. What EINS Explicitly Does NOT Do

This is critical for architectural clarity.

| Capability | Supported |
|----------|----------|
| Account creation (AD, Entra, email) | ❌ |
| Password management | ❌ |
| Authentication | ❌ |
| Authorization / access control | ❌ |
| Joiner‑Mover‑Leaver automation | ❌ |
| Access requests / approvals | ❌ |

EINS only **publishes identity attributes**; consuming systems decide how to use them.

---

## 6. Data Distribution & Interface Model

EINS provides **standardized, file‑based interfaces** to consuming systems.

### Key characteristics:
- Scheduled data publication (multiple times per day)
- Public vs non‑public attribute filtering
- Company, organization, personal, and relationship data files
- Secure transmission using PKI‑based encryption [4](https://sonyjpn.sharepoint.com/sites/S099-070-eins/all_document/EINS_Guideline_for_Standard_IF_(E).pdf?web=1)[5](https://sonyjpn.sharepoint.com/sites/S099-070-eins/Documents_new/EINS_Guideline_for_Standard_IF_(J).pdf?web=1)[6](https://sonyjpn.sharepoint.com/sites/S099-070-eins/Documents_new/EINS_IF_PKI_ClientUpdate_Guideline(J).pdf?web=1)

---

## 7. EINS Feature Table (Authoritative, No Inference)

> Format aligned with Passport feature table for direct comparison.

| Main Category | Capability / Function | Example (Concrete) |
|--------------|----------------------|-------------------|
| Identity Core & Global ID Management | Global ID issuance | Assigning a unique Global ID to a new employee based on HR data |
| Identity Core & Global ID Management | Global ID uniqueness enforcement | Preventing duplicate identity records across Sony Group companies |
| Identity Core & Global ID Management | Global ID lifecycle tracking | Maintaining historical GID records after employment ends |
| Person & Workforce Identity Data | Employee identity aggregation | Collecting employee attributes from company HR systems |
| Person & Workforce Identity Data | Non‑employee identity representation | Storing contractor identity records without provisioning accounts |
| Person & Workforce Identity Data | Employment type classification | Identifying permanent vs contract vs dispatched workers |
| Organization & Company Structure | Company master management | Maintaining Sony Group company codes |
| Organization & Company Structure | Organization hierarchy management | Managing division / department / section structures |
| Organization & Company Structure | Organization effective dating | Tracking org changes over time |
| Data Ingestion & Registration | HR‑based data ingestion | Receiving employee data from local HR systems |
| Data Ingestion & Registration | Online registration | Registering contractors via EINS Online |
| Data Ingestion & Registration | Email‑based registration | Manual registration for non‑onboarded companies |
| Data Validation & Quality Control | Data completeness validation | Rejecting records missing mandatory attributes |
| Data Validation & Quality Control | Duplicate person detection | Using attributes such as birthdate for identity checks |
| Data Validation & Quality Control | Company responsibility enforcement | Requiring each company to maintain data accuracy |
| Data Distribution & Interfaces | Standard interface publication | Providing identity data files to consuming systems |
| Data Distribution & Interfaces | Attribute exposure control | Filtering sensitive fields (e.g., birthday) |
| Data Distribution & Interfaces | Scheduled data refresh | Publishing updated identity data multiple times daily |
| Directory & Reference Usage | Identity reference directory | Applications using GID as a unique identity key |
| Directory & Reference Usage | Global directory lookup | Supporting Cross‑Page / global phonebook |
| Administration & Operations | Company onboarding management | Enabling new companies to participate in EINS |
| Administration & Operations | Interface certificate management | Managing PKI certificates for data transmission |
| Service Governance | Service availability management | Operating EINS as a shared enterprise service |
| Service Governance | Incident communication | Notifying applications of EINS transmission issues |
| Security & Compliance | Data minimization enforcement | Ensuring only approved attributes are shared |
| Security & Compliance | Internal‑only identity scope | Restricting EINS to Sony Group populations |

---

## 8. Relationship to Other Identity Systems

### EINS vs Passport (Conceptual)

| Dimension | EINS | Passport |
|--------|------|---------|
| Primary Role | Identity directory | IAM orchestration & governance |
| System of Identity Truth | ✅ | ❌ |
| Global ID Authority | ✅ | ❌ |
| Account Provisioning | ❌ | ✅ |
| Access Governance | ❌ | ✅ |
| Authentication | ❌ | ❌ (delegated) |

EINS intentionally avoids IAM responsibilities and focuses on **identity correctness and consistency**.

---

## 9. Architectural Implications

1. **EINS should remain stable and low‑change**
   - It is foundational infrastructure
   - Changes ripple widely

2. **IAM modernization should not overload EINS**
   - Passport / Entra should handle lifecycle and access
   - EINS should remain identity‑centric

3. **Clear separation of concerns is a strength**
   - EINS = Who is the person?
   - IAM = What can they access?

---

## 10. Recommended Usage of This Document

This document is suitable for:
- Identity architecture standards
- EINS vs Passport vs Entra comparisons
- Onboarding new architects or vendors
- Preventing scope creep into EINS

---

*End of document*
``
## System Context, Scope, and Feature Capabilities

---

## 1. Executive Summary

EINS (Enterprise Id‑management Networking Service) is **Sony Group’s global enterprise identity directory**.

Its primary purpose is to:
- **Collect employee and worker identity attributes** from group companies
- **Issue and manage a unique Global ID (GID)** for each individual
- **Distribute authoritative identity and organization data** to internal systems

EINS is **not** an IAM system and **does not perform account provisioning, authentication, or access governance**.

Architecturally:
- EINS is a **System of Identity Truth**
- It serves as a **read‑optimized, authoritative directory**
- It is intentionally **decoupled from access lifecycle automation**

---

## 2. System‑of‑Record vs System‑of‑Action

### 2.1 Identity Authority Model

| Aspect | Reality |
|------|--------|
| Identity Scope | Sony Group employees and workers |
| System Role | Enterprise identity directory |
| Identity Origination | Company HR systems and approved registrations |
| Global Identifier | **Global ID (GID)** issued by EINS |
| Account Provisioning | ❌ Not supported |
| Authentication | ❌ Not supported |
| Access Governance | ❌ Not supported |

EINS is explicitly described as:
