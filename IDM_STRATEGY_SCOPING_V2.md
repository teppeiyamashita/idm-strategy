# Identity Management (IDM) Strategy: Project Scoping Options — Version 2

## Purpose

This document is a revised version of the IDM Strategy Scoping document, enriched with authoritative current-state platform knowledge sourced from the Platform Expert knowledge base (`knowledge/platforms/`). Corrections and clarifications from the original version are annotated where relevant.

Our organisation manages approximately **110,000 identities** across multiple regions. Today these identities are managed by several independent platforms and teams worldwide, creating inconsistency and operational risk. This project seeks to establish a modern, sustainable IDM platform as the foundation for workforce identity management going forward.

> **Revision notes (v2):** Three significant factual corrections have been made based on authoritative platform documentation:
> 1. EINS is a **global** platform (not Japan-only). Its role is identity record authority and Global ID issuance — it does **not** provision accounts.
> 2. **MIM is backend sync infrastructure**, not the core engine of JPIDM. The core of JPIDM is an ASP.NET MVC 4 web portal backed by SQL Server.
> 3. **EINS Online Registration is the entry point for Japan contingent workers.** After EINS issues the Global ID, JPIDM receives the EINS feed and provisions the AD account. JPIDM is **not** the entry point — it is the provisioning layer downstream of EINS. IDM Web self-registration (IDM Web forms) covers shared/resource objects only, not people.

---

## Background & Drivers

| Driver | Detail |
|--------|--------|
| **Vendor dependency** | JPIDM operations are currently outsourced to **Avanade**. Removing this dependency will reduce cost (currently approximately ¥100 million/year) and increase EUSP control. |
| **JPIDM end-of-life** | JPIDM is built on ASP.NET MVC 4 (VB.NET) with MIM as backend sync infrastructure. MIM reaches end of support in **2029** (within 3 years). A replacement strategy must be in place before that date. The replacement scope is the entire JPIDM platform — web portal, AD operations layer, workflow engine, and MIM sync — not MIM alone. |
| **Platform fragmentation** | Workforce provisioning is handled differently across regions: JPIDM (JP), Passport (AM/EU/SM), and APIDM (AP, undocumented). There is no consistent, global provisioning pipeline. |
| **Global standardisation** | EUSP aims to establish a new platform as the **global standard** for workforce identity provisioning so that all regions and GISC-managed teams can provision employees through one consistent pipeline. |

---

## Current Platform Architecture (Authoritative Summary)

Understanding the existing platforms is essential before defining scope. The following is based on the Platform Expert knowledge base.

### EINS — Enterprise Id-management Networking Service

| Aspect | Detail |
|--------|--------|
| **Role** | Global identity directory and Global ID (GID) authority |
| **Scope** | **Global** (all Sony Group companies — not Japan-only) |
| **What it does** | Issues and maintains the canonical Global ID; aggregates workforce attributes from company HR systems; distributes identity data to downstream platforms via file-based interfaces |
| **What it does NOT do** | Account creation, provisioning, authentication, access control, Joiner-Mover-Leaver automation |
| **Population** | All identity types as records: FTE, fixed-term, expatriates, dispatch workers, contractors |
| **Entry model** | HR-system-driven (primary); EINS Online Registration (supplemental, for workers not in HR systems) |

> **Correction from v1:** EINS was labelled "(JP)" in the original document. EINS is a **global** platform serving all Sony Group companies.

### JPIDM — Japan Identity Management

| Aspect | Detail |
|--------|--------|
| **Role** | Japan self-service IDM portal and AD/Exchange orchestration |
| **Scope** | Japan only |
| **Technical stack** | ASP.NET MVC 4 (VB.NET); SQL Server (IDM_DB, 100+ tables); Entity Framework 6.2.0; MIM as backend sync infrastructure |
| **What it does** | User/group lifecycle in AD and Exchange; mailing list and security group management; company affiliation attributes; approval workflows (Request_LST engine) |
| **Upstream sources** | Reads identity/org data from EINS/GHD (GHD_PersonalInfo_MST, GHD_OrganizationInfo_MST tables) |
| **Downstream targets** | Writes to on-prem AD (JP.SONY.COM), Exchange |
| **Populations managed** | FTE (Japan); contingent workers (Japan — receives EINS feed, provisions AD account); service/shared accounts; computer accounts; resource accounts |

> **Correction from v1:** MIM was described as "the core engine of JPIDM." Per authoritative documentation, the JPIDM core is the **ASP.NET MVC 4 web portal + ADAccessor layer + SQL Server**. MIM is backend sync infrastructure. Replacing JPIDM means replacing the entire platform (portal, workflow engine, AD operations layer, MIM sync) — not only MIM.

### Passport — SailPoint IdentityIQ

| Aspect | Detail |
|--------|--------|
| **Role** | IAM orchestration and governance (access requests, certifications, provisioning) |
| **Scope** | Americas (AM), EMEA/EU, SM regions |
| **For FTEs** | **NOT the system of entry** — Workday is. Passport is a bridge/orchestration layer: Workday events trigger Passport, which provisions to AD and connected apps. |
| **For contingent workers** | **IS the system of entry** — hiring managers use the "Create Contingent Worker" form in Passport. Passport then provisions to AD. |
| **Downstream targets** | On-prem Active Directory, Exchange, connected applications |

---

## Identity Types in Scope for Decision

| Identity Type | Data Entry Point | Provisioning Flow | EUSP Owns Entry Point System? |
|--------------|-----------------|-------------------|:-----------------:|
| FTE — Japan | Castnet (HR) | Castnet → EINS → JPIDM → AD (JP) | ❌ (HR / Castnet team) |
| FTE — Asia-Pacific | HR system? | HR → APIDM → AD (AP)? | ❓ (Unknown) |
| FTE — AM / EU / SM | Workday (HR) | Workday → Passport → AD (AM/EU/SM) | ❌ (HRES / Workday team) |
| Contingent workers — Japan | EINS | EINS → JPIDM → AD (JP) | ❌ (EINS team) |
| Contingent workers — AM / EU / SM | Passport | Passport → AD (AM/EU/SM) | ❌ (Passport team) |
| Service / shared accounts — Japan | JPIDM | JPIDM → AD (JP) | ✅ |
| Service / shared accounts — AM / EU / SM | Passport | Passport → AD (AM/EU/SM) | ❌ (Passport team) |
| Cloud Admin / Service accounts | Entra ID Tenant | Entra ID | ✅ |

*APIDM is referenced for the AP region but is **not documented** in the authoritative knowledge base. Capabilities, ownership, and migration complexity are unknown and should be investigated before committing to scope.

---

## Pattern 1 — Focused Scope: EUSP-Managed Platforms Only

### Overview

Limit the project scope to **EUSP-managed platforms** — primarily JPIDM and APIDM. The immediate goal is to replace JPIDM (the entire platform, not MIM alone) and remove the Avanade operational dependency, while establishing a global standard provisioning pipeline for **full-time employees**.

### Goals

1. **Remove Avanade dependency** — Bring JPIDM operations fully in-house to EUSP, eliminating approximately ¥100 million/year in Avanade operational costs.
2. **Address JPIDM end-of-life** — Replace the JPIDM platform (ASP.NET MVC 4 portal, ADAccessor layer, MIM backend) before the MIM 2029 deadline. The replacement must cover all current JPIDM capabilities: FTE lifecycle, Japan contingent worker AD provisioning (via EINS feed), group/mailbox management, and approval workflows.
3. **Establish a global FTE provisioning standard** — Deploy the new platform as the single, authoritative provisioning pipeline for full-time employees across all regions, consuming identity data from EINS (Global ID authority) and HR systems.

### Provisioning Pipeline Target

```
EINS (Global ID authority)
     ▲
     │ (identity records from HR systems)
HR Systems (Workday, Castnet, ...)
          │
          ▼
   New IDM Platform  ◄─── EUSP-managed, global standard
          │
    ┌─────┴──────┐
    ▼            ▼
 Entra ID    On-premises Active Directory
```

> **Note on EINS integration:** EINS is the upstream source of Global IDs and identity data. The new platform must consume EINS-distributed data (via existing file-based interfaces) and must not attempt to replace EINS — it remains the global identity authority.

### In Scope

| Area | Detail |
|------|--------|
| **JPIDM full replacement** | Replace the entire JPIDM platform: web portal, AD operations layer (ADAccessor equivalent), approval workflow engine (Request_LST equivalent), MIM backend sync. This includes FTE lifecycle AND Japan contingent worker registration/provisioning. |
| **APIDM** | Migrate APIDM-managed accounts to the new platform. *Note: APIDM capabilities are undocumented — a discovery phase is required before estimating effort.* |
| **FTE account lifecycle** | Create, update, and deactivate full-time employee accounts automatically from HR/EINS data feed |
| **Japan contingent worker lifecycle** | JPIDM currently receives the EINS feed and provisions AD accounts for Japan contingent workers. The new platform must preserve this capability: consume the EINS feed and provision AD accounts for contingent workers registered via EINS Online Registration. |
| **Global provisioning standard** | New platform becomes the authoritative provisioning engine for FTEs (and JP contingent workers) into Entra ID and on-premises AD across all regions |
| **Avanade offboarding** | End Avanade contract and operational dependency |

### Out of Scope

| Area | Reason |
|------|--------|
| **Passport platform changes** | Managed by Ramnath's team; outside EUSP authority for this project. AM/EU/SM contingent worker provisioning continues unchanged. |
| **EINS platform changes** | EINS is managed outside EUSP at a global level. The new platform will *consume* EINS data but not replace it. |
| **EINS Online Registration** | The EINS identity registration process for Japan contingent workers (Global ID issuance) is outside EUSP scope and remains unchanged. Only the downstream AD provisioning step (currently JPIDM consuming the EINS feed) is in scope for replacement. |
| **Access certification / IGA** | Passport handles access reviews for AM/EU/SM. This project focuses on provisioning, not governance. |

### Stakeholders

- **Primary**: EUSP team
- **Secondary**: HR system owners (Workday, Castnet), EINS team (interface alignment), Regional IT leads for AD/Entra ID
- **Not involved**: Ramnath's team (Passport)

### Pros & Cons

| Pros | Cons |
|------|------|
| Clear, manageable scope — minimal stakeholders | Leaves AM/EU/SM platform fragmentation (Passport) unresolved |
| Directly addresses the 2029 MIM deadline | Two parallel provisioning paradigms remain (new platform + Passport) for AM/EU/SM |
| Removes Avanade cost dependency quickly | APIDM discovery gap — effort is currently unknown |
| Realistic and deliverable within a 3-year window | No unified governance layer for access certification (would require separate IGA initiative) |
| Keeps the project within EUSP authority — no need for cross-team governance | Japan contingent worker AD provisioning capability must be preserved in the replacement platform |
| EINS integration is well-understood (file-based interfaces already exist) | |

---

## Pattern 2 — Broad Scope: Unified Multi-Platform Approach

### Overview

Expand the project scope to **all identity management platforms**, consolidating JPIDM, APIDM, and Passport into a single, unified identity management solution. EINS remains unchanged as the global identity authority.

### Goals

1. **Platform consolidation** — Integrate JPIDM, APIDM, and Passport into one unified provisioning and governance platform.
2. **Address all identity types and regions** — Consistent lifecycle management for both FTE and contingent worker identities across all regions.
3. **Simplify the landscape** — Reduce the total number of independent provisioning platforms from three (JPIDM, APIDM, Passport) to one.

> **Architecture note (v2):** EINS is **excluded from consolidation** under both patterns. EINS is a global identity authority managing 100,000+ identity records across all Sony Group companies. It is structurally separate from provisioning platforms and is not owned by EUSP. Any attempt to replace EINS would require a separate, broader initiative beyond the scope of this project.

### Platforms Targeted

| Platform | Current Owner | Identity Types Covered | Documentation Status |
|----------|--------------|----------------------|----------------------|
| JPIDM | EUSP | FTE (JP), Contingent workers (JP — AD provisioning via EINS feed), Service/shared/computer accounts | ✅ Well documented |
| APIDM | EUSP (in transition) | FTE (AP) | ❌ **Undocumented — discovery required** |
| Passport | Non-EUSP (Ramnath's team) | Contingent workers (AM/EU/SM), FTE orchestration from Workday, Service/shared accounts (AM/EU/SM) | ✅ Documented |

> **Note on Passport for FTEs:** Passport does not originate FTE identities — Workday does. For FTEs in AM/EU/SM, consolidation may be achievable by routing the Workday → AD provisioning through the new platform directly, bypassing Passport. This could allow FTE provisioning consolidation without needing Passport to be replaced — only the contingent worker flow would require Passport integration/migration.

### Approach

A **feature analysis** must be conducted to map current capabilities and define the consolidation target. Key questions:
- Which JPIDM-specific capabilities (Japan contingent worker AD provisioning via EINS feed, computer account management, group management) are required in the consolidated platform?
- Can Workday → new platform → AD replace the Passport FTE flow, or does Passport governance (access certification) create a hard dependency?
- What are APIDM's actual capabilities? (Currently undocumented)
- Which platform serves as the consolidation target: Passport (SailPoint IIQ/ISP), Microsoft Entra ID Governance, a custom platform, or another IGA solution?

### In Scope

| Area | Detail |
|------|--------|
| **JPIDM & APIDM migration** | As per Pattern 1 |
| **Passport FTE flow replacement** | Route Workday → new platform → AD/Entra for AM/EU/SM FTE provisioning |
| **Passport contingent worker migration** | Work with Ramnath's team to migrate AM/EU/SM contingent worker registration to the new platform |
| **Full FTE + contingent worker lifecycle** | Unified provisioning and deprovisioning for all identity types across all regions |
| **Platform feature analysis** | Evaluate all three platforms and define the consolidation target before implementation begins |

### Out of Scope

- EINS (global identity authority — remains unchanged, consumed as upstream data source)
- Consumer identities (PlayStation Network, etc.)
- Application-specific identity stores

### Stakeholders

- **Primary**: EUSP team
- **Required partners**: Ramnath's team (Passport), EINS team (interface alignment), HR system owners, Regional IT leads
- **Governance**: Cross-team steering committee required

### Pros & Cons

| Pros | Cons |
|------|------|
| Single unified provisioning platform for all regions and identity types | Significantly more complex — more stakeholders, longer timeline |
| Eliminates parallel platform fragmentation entirely | Requires agreement from Ramnath's team to migrate Passport capabilities — political complexity |
| More cost-efficient long-term (one platform to operate) | Feature analysis phase adds time and cost before delivery starts |
| Addresses contingent worker consistency gap across regions | APIDM discovery adds unknown risk |
| Removes Passport licensing cost if fully replaced | Risk of in-flight scope changes if Passport team dependencies are unresolved |

---

## Pattern Comparison Summary

| Dimension | Pattern 1 — Focused | Pattern 2 — Broad |
|-----------|:-------------------:|:-----------------:|
| Platforms in scope | JPIDM, APIDM | JPIDM, APIDM, Passport |
| EINS | Unchanged (consumed as data source) | Unchanged (consumed as data source) |
| FTE provisioning regions | All (via new global standard) | All (same) |
| Contingent workers — Japan | ✅ Included (JPIDM replacement preserves AD provisioning via EINS feed) | ✅ Included |
| Contingent workers — AM/EU/SM | ❌ Remains with Passport | ✅ Migrated to new platform |
| Resolves MIM/JPIDM 2029 deadline | ✅ Yes | ✅ Yes |
| Removes Avanade dependency | ✅ Yes | ✅ Yes |
| Number of stakeholder teams | Low (EUSP + HR + EINS + Regional IT) | High (+ Ramnath's team, steering committee) |
| Project complexity | Medium | High |
| Delivery timeline | Shorter (estimated 2–3 years) | Longer (estimated 3–5 years) |
| Risk | Lower | Higher |
| Long-term platform fragmentation | Partially remains (Passport for AM/EU/SM) | Resolved |
| EUSP authority to execute | Full | Requires negotiation |

---

## Decision Guidance

### Choose Pattern 1 if:

- The primary near-term goal is addressing the **JPIDM end-of-life (MIM 2029 deadline) and Avanade dependency** within a realistic timeline.
- The project team wants a **focused and deliverable scope** without requiring alignment across multiple teams and leadership chains.
- Stakeholder engagement capacity is limited and a manageable project is a higher priority than a comprehensive one.
- AM/EU/SM contingent worker management (Passport) is considered a separate, later initiative.

### Choose Pattern 2 if:

- Leadership has appetite for a **comprehensive, multi-year transformation** that fully resolves regional platform fragmentation.
- There is organisational authority and executive support to bring **Ramnath's team** (Passport) into scope under a unified governance model.
- A full **feature analysis and platform consolidation study** can be funded and staffed before implementation begins.
- The organisation accepts a longer timeline and higher execution risk in exchange for a single operating platform across all regions.

---

## Recommended Next Steps

Regardless of which pattern is selected, the following actions apply:

1. **Confirm the selected pattern** with project sponsors and key stakeholders.
2. **APIDM discovery** — Commission a capability and ownership assessment for APIDM (AP region) before committing to migration scope. This is a gap in both patterns.
3. **Initiate JPIDM replacement platform evaluation** — assess candidate platforms (SailPoint IIQ/ISP, Microsoft Entra ID Governance, custom, or other) against current JPIDM functional requirements, including Japan contingent worker AD provisioning (EINS feed consumption) and AD/Exchange write operations.
4. **Map Avanade contract timeline** — align the Avanade offboarding plan with the new platform go-live.
5. **Define EINS integration interface** — confirm the file-based interfaces EINS currently publishes and ensure the new platform can consume them without modification to EINS.
6. **Establish the FTE provisioning pipeline design** — define the data flow from Workday/Castnet → EINS (Global ID) → New Platform → Entra ID / on-premises AD.
7. *(Pattern 2 only)* **Commission a cross-platform feature analysis** — map capabilities across JPIDM, Passport, and APIDM to inform consolidation target selection and FTE vs. contingent worker flow separation.

---

## Appendix: Platform Expert Consultation Notes

This revision was produced in consultation with the Platform Expert agent, which reviewed the following authoritative knowledge base documents:

| Document | Key Finding |
|----------|-------------|
| `knowledge/platforms/EINS.md` | EINS is global (not JP-only); is identity record authority only; does not provision accounts |
| `knowledge/platforms/JPIDM.md` | JPIDM is the provisioning system for Japan, including contingent workers |
| `knowledge/platforms/JPIDM/ARCHITECTURE.md` | JPIDM core = ASP.NET MVC 4 + SQL Server + ADAccessor; MIM is backend sync only |
| `knowledge/platforms/JPIDM/FEATURES.md` | IDM Web self-registration (IDM Web forms) is for shared/resource objects only — not people; Japan contingent worker AD accounts are provisioned by JPIDM via EINS feed, not via IDM Web forms; password reset handled outside JPIDM |
| `knowledge/platforms/JPIDM/SYSTEM_CONTEXT.md` | JPIDM reads from EINS/GHD tables; writes to JP.SONY.COM AD and Exchange |
| `knowledge/platforms/JPIDM/RELATIONSHIPS.md` | JPIDM does not replace EINS or Passport; each serves a distinct architectural role |
| `knowledge/platforms/Passport.md` | Passport is downstream of Workday for FTEs; is system of entry for contingent workers only |
| `knowledge/platforms/IDENTITY_PLATFORM_FEATURE_MAP.md` | EINS = identity truth; Passport = access governance; JPIDM = Japan action platform. Japan contingent workers: entry via EINS Online Registration → JPIDM receives EINS feed → provisions AD account. |
| `knowledge/Landscape.md` | Entra ID: 250K objects, 170K homed, 130K on-prem synced; JP 67K, AP 20K |

*Document prepared by EUSP — v2 revised with Platform Expert consultation. Original: IDM_STRATEGY_SCOPING.md*
