# JPIDM – Japan Identity Management System
## Authoritative System Description (Operational Scope Only)

---

## Ownership & Responsibility

| Aspect | Team/Vendor |
|--------|-------------|
| **Platform Ownership** | EUSP Japan Team |
| **Operations** | Avanade (outsourced) |
| **Cost** | ~¥100 million/year (Avanade operational costs) |

### Relationship to APIDM

JPIDM and **APIDM** (Asia-Pacific Identity Management) are **separate Microsoft Identity Manager (MIM) instances**:
- **JPIDM**: Japan region
- **APIDM**: Asia-Pacific region

> ⚠️ **APIDM is undocumented** in this knowledge base. Capabilities, technical stack, ownership, and migration complexity are unknown and require investigation.

---

## 1. Purpose of This Document

This document describes **JPIDM as it exists today**, based **only on authoritative SharePoint-hosted operational materials**.

Excluded by design:
- Strategy decks
- Architecture visions
- Roadmaps
- Problem statements
- Documents authored by Yamashita, Teppei

Where information is **not explicitly documented**, it is called out as such.

---

## 2. System Overview (What Is Explicitly Documented)

JPIDM is an **identity management system used in the Japan region**, providing a **web-based interface and backend services** for managing identity-related objects such as:

- Mailing lists / groups
- Group membership
- Company affiliation attributes
- Password reset and account unlock operations

JPIDM is accessed through a **dedicated IDM web portal** and is actively used by end users and administrators. [1](https://teams.microsoft.com/l/message/19:75f42e483fb74f0081c59f755db480e5@thread.v2/1727783413652?context=%7B%22contextType%22:%22chat%22%7D)

---

## 3. Confirmed Technical Foundation

### 3.1 Microsoft Identity Manager (MIM)

JPIDM uses **Microsoft Identity Manager (MIM)** to support credential-related operations.

Documented MIM capabilities include:
- Password reset
- Account unlock
- Security-question–based verification
- Network connectivity requirements (Sony network) [2](https://sonyjpn.sharepoint.com/sites/S099-070-eins/Documents_new/EINS_SLA.pdf?web=1)

This confirms:
- MIM is **in active operational use**
- JPIDM delegates authentication to Active Directory but manages resets via MIM

> ⚠️ **Architectural Note: JPIDM ≠ MIM**
>
> JPIDM **uses** MIM but is **architecturally independent** of it. The core JPIDM platform consists of:
> - ASP.NET MVC 4 web portal
> - SQL Server (IDM_DB)
> - ADAccessor layer (ADSI-based AD operations)
> - Request_LST workflow engine
>
> MIM is a **backend sync component** for specific credential operations (password reset, account unlock). **MIM decommissioning does not automatically require JPIDM decommissioning**. There are two architectural options:
> 1. **Decouple MIM**: Replace MIM-dependent features (password reset, unlock) with alternative implementations while retaining the JPIDM platform
> 2. **Replace entire platform**: Replace JPIDM + MIM together as part of a comprehensive replatforming
>
> MIM's 2029 end-of-support deadline is a forcing function requiring a decision between these two options, not an automatic mandate to replace JPIDM.

---

### 3.2 Active Directory and Exchange Integration

Production CAB records show that JPIDM:
- Uses **MIM synchronization**
- Integrates with **on‑premises Exchange**
- Performs changes that affect **Exchange Online objects via on‑prem Exchange**

Changes to JPIDM’s Exchange integration are treated as **CAB‑controlled production changes**, confirming JPIDM is **production‑critical infrastructure**. [3](https://outlook.office365.com/owa/?ItemID=AAMkADQyMmRkYTNmLTQ4MjgtNGY0OS1iOGRkLTc2Njk0NmE4OGY5NABGAAAAAAAcFvsZ%2b5dCT5PADYjpClo4BwAzpYgOvAwdQZKOQNabDJeKAAD5UTHZAAAzpYgOvAwdQZKOQNabDJeKAAD5UYmaAAA%3d&exvsurl=1&viewmodel=ReadMessageItem)

---

## 4. User‑Facing Capabilities (Documented)

### 4.1 Group and Mailing List Management

JPIDM provides **self‑service capabilities** for:

- Creating mailing lists
- Adding and removing members
- Upload‑based bulk member management
- Ownership‑based authorization (Owner / Alternative Admin)

These operations are performed via the **JPIDM web UI** and are limited to objects the user owns or administers. [1](https://teams.microsoft.com/l/message/19:75f42e483fb74f0081c59f755db480e5@thread.v2/1727783413652?context=%7B%22contextType%22:%22chat%22%7D)

---

### 4.2 Company Affiliation Management

JPIDM supports **company affiliation changes**, including scenarios where a user has **multiple affiliations (兼務者)**.

Users can:
- Select company from a dropdown
- Submit affiliation changes via IDM UI

This confirms JPIDM manages **organizational attributes**, not just credentials. 

---

### 4.3 Credential Operations

Via MIM integration, JPIDM supports:
- Password reset
- Account unlock
- Security-question–based identity verification

These functions are user‑initiated and documented in official MIM user guides. [2](https://sonyjpn.sharepoint.com/sites/S099-070-eins/Documents_new/EINS_SLA.pdf?web=1)

---

## 5. JPIDM Feature Table (Authoritative Only)

> Same structure as EINS / Passport tables.  
> Only features with direct documentation are included.

| Main Category | Capability / Function | Example (Concrete) |
|--------------|----------------------|-------------------|
| Identity Operations | Web-based IDM portal | Users access JPIDM via dedicated IDM web site [1](https://teams.microsoft.com/l/message/19:75f42e483fb74f0081c59f755db480e5@thread.v2/1727783413652?context=%7B%22contextType%22:%22chat%22%7D) |
| Group & ML Management | Mailing list creation | Creating a new ML via “グループ登録申請” [1](https://teams.microsoft.com/l/message/19:75f42e483fb74f0081c59f755db480e5@thread.v2/1727783413652?context=%7B%22contextType%22:%22chat%22%7D) |
| Group & ML Management | Member add/remove | Adding or removing ML members via UI or file upload [1](https://teams.microsoft.com/l/message/19:75f42e483fb74f0081c59f755db480e5@thread.v2/1727783413652?context=%7B%22contextType%22:%22chat%22%7D) |
| Group & ML Management | Ownership-based control | Only Owners / Alternative Admins can edit groups [1](https://teams.microsoft.com/l/message/19:75f42e483fb74f0081c59f755db480e5@thread.v2/1727783413652?context=%7B%22contextType%22:%22chat%22%7D) |
| Organizational Attributes | Company affiliation change | Changing company for兼務者 via IDM UI  |
| Credential Management | Password reset | Resetting Windows password using MIM portal [2](https://sonyjpn.sharepoint.com/sites/S099-070-eins/Documents_new/EINS_SLA.pdf?web=1) |
| Credential Management | Account unlock | Unlocking locked account after security answer verification [2](https://sonyjpn.sharepoint.com/sites/S099-070-eins/Documents_new/EINS_SLA.pdf?web=1) |
| Platform Integration | MIM integration | Using Microsoft Identity Manager for credential lifecycle [2](https://sonyjpn.sharepoint.com/sites/S099-070-eins/Documents_new/EINS_SLA.pdf?web=1) |
| Platform Integration | AD / Exchange linkage | Exchange object changes routed via on‑prem Exchange [3](https://outlook.office365.com/owa/?ItemID=AAMkADQyMmRkYTNmLTQ4MjgtNGY0OS1iOGRkLTc2Njk0NmE4OGY5NABGAAAAAAAcFvsZ%2b5dCT5PADYjpClo4BwAzpYgOvAwdQZKOQNabDJeKAAD5UTHZAAAzpYgOvAwdQZKOQNabDJeKAAD5UYmaAAA%3d&exvsurl=1&viewmodel=ReadMessageItem) |
| Operations & Governance | CAB‑controlled changes | JPIDM production changes require CAB approval [3](https://outlook.office365.com/owa/?ItemID=AAMkADQyMmRkYTNmLTQ4MjgtNGY0OS1iOGRkLTc2Njk0NmE4OGY5NABGAAAAAAAcFvsZ%2b5dCT5PADYjpClo4BwAzpYgOvAwdQZKOQNabDJeKAAD5UTHZAAAzpYgOvAwdQZKOQNabDJeKAAD5UYmaAAA%3d&exvsurl=1&viewmodel=ReadMessageItem) |

---

## 6. What Is NOT Documented (Important Gaps)

The following items are **not explicitly documented** in authoritative SharePoint sources:

- Joiner / mover / leaver automation logic
- Source‑of‑authority for identity data (HR vs EINS vs other)
- Scope of account provisioning (who/when/how)
- Coverage beyond JP region
- Relationship to Entra ID synchronization
- Non‑human or guest identity handling

These areas must **not be inferred** without additional system runbooks or vendor documentation.

---

## 7. Relationship to Other Identity Systems (Factual Only)

From available sources, we can state:

- JPIDM is **region‑specific (JP)**
- JPIDM uses **MIM** for credential operations
- JPIDM manages **identity-related objects in AD/Exchange**
- JPIDM provides **self‑service for certain identity changes**

Comparisons to Passport, EINS, or Entra ID **require separate documents** and should not be embedded here without mixing interpretation.

---

## 8. Intended Use of This Document

This document is suitable for:
- Identity architecture baseline (JP scope)
- Audit and compliance discussions
- Onboarding new engineers or vendors
- Avoiding incorrect assumptions about JPIDM scope

It is **not** a strategy or modernization document.

---

*End of authoritative JPIDM document*
``
