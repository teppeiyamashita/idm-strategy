# Identity Management (IDM) Strategy: Project Scoping Options

## Purpose

This document outlines two strategic options (patterns) for scoping the Identity Management modernisation project. The goal is to help stakeholders decide which approach best balances risk, complexity, cost, and timeline before formal project kick-off.

Our organisation manages approximately **110,000 identities** across multiple regions. Today these identities are managed by several independent platforms and teams worldwide, creating inconsistency and operational risk. This project seeks to establish a modern, sustainable IDM platform as the foundation for workforce identity management going forward.

---

## Background & Drivers

| Driver | Detail |
|--------|--------|
| **Vendor dependency** | JPIDM operations are currently outsourced to **Avanade**. Removing this dependency will reduce cost (currently approximately ¥100 million/year) and increase EUSP control. |
| **MIM end-of-support** | Microsoft Identity Manager (MIM), the core engine of JPIDM, reaches end of support in **2029** (within 3 years). A replacement strategy must be in place before that date. |
| **Platform fragmentation** | Workforce provisioning is handled differently across regions: JPIDM (JP), Passport (AM/EU), APIDM (AP). There is no consistent, global provisioning pipeline. |
| **Global standardisation** | EUSP aims to establish a new platform as the **global standard** for workforce identity provisioning so that all regions and GIC-managed teams can provision employees through one consistent pipeline. |

---

## Identity Types in Scope for Decision

| Identity Type | Entry Point | Current Platform | EUSP-Managed? |
|--------------|-------------|-----------------|:-------------:|
| Full-time employees (FTE) | HR systems (Workday, Castnet) | JPIDM / Passport / APIDM | ✅ Partial (JP/AP only; AM/EU via Passport) |
| Contingent workers | SailPoint / EINS (self-service portal) | SailPoint (AM/EU), EINS (JP) | ❌ No |
| Service accounts | JPIDM, SailPoint | Various | ✅ Partial (JP only via JPIDM) |
| Admin accounts | Entra ID Tenant | Entra ID | ✅ Yes |

---

## Pattern 1 — Focused Scope: EUSP-Managed Platforms Only

### Overview

Limit the project scope to **EUSP-managed platforms** — primarily JPIDM and APIDM. The immediate goal is to migrate JPIDM off MIM and Avanade dependency while simultaneously establishing a global standard provisioning pipeline for **full-time employees only**.

### Goals

1. **Remove Avanade dependency** — Bring JPIDM operations fully in-house to EUSP, eliminating approximately ¥100 million/year in Avanade operational costs.
2. **Address MIM end-of-support** — Replace Microsoft Identity Manager as the core JPIDM engine before the 2029 deadline.
3. **Establish a global FTE provisioning standard** — Deploy the new platform as the single, authoritative provisioning pipeline for full-time employees across all regions. All GIC-managed teams should be able to provision employees via this platform.

### Provisioning Pipeline Target

```
HR Systems (Workday, Castnet, ...)
          │
          ▼
   New IDM Platform  ◄─── EUSP-managed, global standard
          │
    ┌─────┴──────┐
    ▼            ▼
 Entra ID    On-premises Active Directory
```

### In Scope

| Area | Detail |
|------|--------|
| JPIDM migration | Replace MIM + custom .NET subsystem with the new platform |
| APIDM | Migrate APIDM-managed accounts onto the new platform |
| FTE account lifecycle | Create, update, and deactivate full-time employee accounts automatically from HR feed |
| Global provisioning standard | New platform becomes the authoritative source for provisioning FTEs into Entra ID and on-premises AD across all regions |
| Avanade offboarding | End Avanade contract and operational dependency |

### Out of Scope

| Area | Reason |
|------|--------|
| **Contingent worker provisioning pipeline** | Contingent worker identities do not originate from HR data. Their entry point remains SailPoint (AM/EU) and EINS (JP) as today. These flows will be left unchanged. |
| **SailPoint platform changes** | Managed by Ramnath's team; outside EUSP authority for this project. |
| **EINS platform changes** | Managed outside EUSP; contingent worker operations for Japan continue as-is. |
| **Passport platform** | Managed by a separate team (AM/EU); not within EUSP ownership at this stage. |
| **Contingent worker create/update/delete operations** | No changes to how contingent worker accounts enter Entra ID or on-premises AD. They continue flowing through SailPoint and EINS unchanged. |

### Stakeholders

- **Primary**: EUSP team
- **Secondary**: HR system owners (Workday, Castnet), Regional IT leads for AD/Entra ID
- **Not involved**: Ramnath's team (SailPoint/Passport), EINS team

### Pros & Cons

| Pros | Cons |
|------|------|
| Clear, manageable scope — minimal stakeholders | Leaves contingent worker and non-EUSP platform fragmentation unresolved |
| Directly addresses the 2029 MIM deadline | Ramnath's teams still operate separate platforms in parallel |
| Removes Avanade cost dependency quickly | No single global platform across all identity types |
| Realistic and deliverable within a 3-year window | Potential integration complexity if a broader scope is adopted later |
| Keeps the project within EUSP authority — no need for cross-team governance | |

---

## Pattern 2 — Broad Scope: Unified Multi-Platform Approach

### Overview

Expand the project scope to **all identity management platforms**, including those managed by non-EUSP teams, such as EINS and SailPoint (Passport). The goal is to integrate or replace three to four existing platforms with a single, unified identity management solution.

### Goals

1. **Platform consolidation** — Integrate JPIDM, APIDM, EINS, and SailPoint/Passport into one unified IDM platform.
2. **Address all identity types** — Consistent lifecycle management for both FTE and contingent worker identities across all regions.
3. **Simplify the landscape** — Reduce the total number of independent IDM platforms from four to one.

### Platforms Targeted

| Platform | Current Owner | Identity Types Covered |
|----------|--------------|----------------------|
| JPIDM | EUSP | FTE (JP), Service accounts |
| APIDM | EUSP (in transition; some accounts managed directly in on-premises AD) | FTE (AP) |
| EINS | Non-EUSP (JP, separate team) | Contingent workers (JP) |
| SailPoint / Passport | Non-EUSP (Ramnath's team) | Contingent workers (AM/EU), some FTE |

### Approach

A **feature analysis** must be conducted to map current capabilities across all four platforms and determine where each capability should reside in the consolidated solution. The target platform is not yet determined and could be one of:

- **SailPoint** (existing enterprise IGA tool, extended to cover provisioning)
- **Custom-developed solution** purpose-built for this organisation's requirements
- Another enterprise IGA/IDA platform (to be evaluated)

The analysis must clarify:
- Which features from JPIDM, EINS, and Passport are duplicated vs. unique
- Which platform can serve as the consolidation target
- What custom development, if any, is required to fill feature gaps

### In Scope

| Area | Detail |
|------|--------|
| JPIDM & APIDM migration | As per Pattern 1 |
| EINS integration/replacement | Bring contingent worker provisioning (JP) into scope |
| SailPoint / Passport integration | Work with Ramnath's team to align or consolidate AM/EU contingent worker flows |
| Full FTE + contingent worker lifecycle | Unified provisioning, update, and deprovisioning for all identity types |
| Platform feature analysis | Evaluate all four platforms and define the consolidation target |

### Out of Scope

- Consumer identities (PlayStation Network, etc.)
- Application-specific identity stores

### Stakeholders

- **Primary**: EUSP team
- **Required partners**: Ramnath's team (SailPoint/Passport), EINS team, HR system owners, Regional IT leads
- **Governance**: Cross-team steering committee required

### Pros & Cons

| Pros | Cons |
|------|------|
| Single unified platform for all identity types | Significantly more complex project with more stakeholders |
| Eliminates parallel platform fragmentation entirely | Requires agreement and co-operation from non-EUSP teams |
| More cost-efficient in the long run (one platform to operate) | Longer delivery timeline — higher risk of delay |
| Addresses contingent worker lifecycle management gap | Feature analysis phase adds time and cost before delivery starts |
| Reduces duplication of tooling and vendor contracts | Political complexity: ownership transitions from non-EUSP teams to EUSP |

---

## Pattern Comparison Summary

| Dimension | Pattern 1 — Focused | Pattern 2 — Broad |
|-----------|:-------------------:|:-----------------:|
| Platforms in scope | JPIDM, APIDM | JPIDM, APIDM, EINS, SailPoint/Passport |
| Identity types | FTE only | FTE + Contingent workers |
| Resolves MIM 2029 deadline | ✅ Yes | ✅ Yes |
| Removes Avanade dependency | ✅ Yes | ✅ Yes |
| Covers contingent workers | ❌ No | ✅ Yes |
| Number of stakeholder teams | Low (EUSP + HR + Regional IT) | High (+ Ramnath's team, EINS team, steering committee) |
| Project complexity | Low | High |
| Delivery timeline | Shorter | Longer |
| Risk | Lower | Higher |
| Long-term platform fragmentation | Partially remains | Resolved |
| EUSP authority to execute | Full | Requires negotiation |

---

## Decision Guidance

### Choose Pattern 1 if:

- The primary near-term goal is addressing the **MIM 2029 deadline and Avanade dependency** within a realistic timeline.
- The project team wants a **focused and deliverable scope** without requiring alignment across multiple teams and leadership chains.
- Stakeholder engagement capacity is limited and a manageable project is a higher priority than a comprehensive one.
- Contingent worker identity management is considered a separate, later initiative.

### Choose Pattern 2 if:

- Leadership has appetite for a **comprehensive, multi-year transformation** that fully resolves platform fragmentation.
- There is organisational authority and executive support to bring **non-EUSP teams** (Ramnath's team, EINS) into scope under a unified governance model.
- A full **feature analysis and platform consolidation study** can be funded and staffed before implementation begins.
- The organisation accepts a longer timeline and higher execution risk in exchange for a cleaner end-state.

---

## Recommended Next Steps

Regardless of which pattern is selected, the following actions apply:

1. **Confirm the selected pattern** with project sponsors and key stakeholders.
2. **Initiate MIM replacement evaluation** — assess candidate platforms (SailPoint, custom, or other) against JPIDM functional requirements.
3. **Map Avanade contract timeline** — align the Avanade offboarding plan with the new platform go-live.
4. **Establish the FTE provisioning pipeline design** — define the data flow from Workday/Castnet to Entra ID and on-premises AD.
5. *(Pattern 2 only)* **Commission a cross-platform feature analysis** — inventory capabilities across JPIDM, EINS, and SailPoint/Passport to inform consolidation target selection.

---

*Document prepared by EUSP. For questions or feedback, contact the EUSP Identity Management team.*
