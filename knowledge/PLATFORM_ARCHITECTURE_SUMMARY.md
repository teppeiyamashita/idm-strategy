# Platform Architecture — Authoritative Summary

> Source: IDM_STRATEGY_SCOPING_V2.md (v2, Platform Expert reviewed)
> This summary reflects corrections from the Platform Expert knowledge base. It supersedes any earlier informal summaries.

## EINS — Enterprise Id-management Networking Service

| Aspect | Detail |
|--------|--------|
| **Role** | Global identity directory and Global ID (GID) authority |
| **Scope** | **Global** (all Sony Group companies — not Japan-only) |
| **What it does** | Issues and maintains the canonical Global ID; aggregates workforce attributes from company HR systems; distributes identity data to downstream platforms via file-based interfaces |
| **What it does NOT do** | Account creation, provisioning, authentication, access control, Joiner-Mover-Leaver automation |
| **Population** | All identity types as records: FTE, fixed-term, expatriates, dispatch workers, contractors |
| **Entry model** | HR-system-driven (primary); EINS Online Registration (supplemental, for workers not in HR systems) |

> **Note:** EINS is a **global** platform serving all Sony Group companies — it is not Japan-only.

---

## JPIDM — Japan Identity Management

| Aspect | Detail |
|--------|--------|
| **Role** | Japan self-service IDM portal and AD/Exchange orchestration |
| **Scope** | Japan only |
| **Technical stack** | ASP.NET MVC 4 (VB.NET); SQL Server (IDM_DB, 100+ tables); Entity Framework 6.2.0; MIM as backend sync infrastructure |
| **What it does** | User/group lifecycle in AD and Exchange; mailing list and security group management; company affiliation attributes; approval workflows (Request_LST engine) |
| **Upstream sources** | Reads identity/org data from EINS/GHD (GHD_PersonalInfo_MST, GHD_OrganizationInfo_MST tables) |
| **Downstream targets** | Writes to on-prem AD (JP.SONY.COM), Exchange |
| **Populations managed** | FTE (Japan); contingent workers (Japan — receives EINS feed, provisions AD account); service/shared accounts; computer accounts; resource accounts |

> **Note:** The JPIDM core is the **ASP.NET MVC 4 web portal + ADAccessor layer + SQL Server**. MIM is backend sync infrastructure only. Replacing JPIDM means replacing the entire platform (portal, workflow engine, AD operations layer, MIM sync) — not MIM alone.

---

## Passport — SailPoint IdentityIQ

| Aspect | Detail |
|--------|--------|
| **Role** | IAM orchestration and governance (access requests, certifications, provisioning) |
| **Scope** | Americas (AM), EMEA/EU, SM regions |
| **For FTEs** | **NOT the system of entry** — Workday is. Passport is a bridge/orchestration layer: Workday events trigger Passport, which provisions to AD and connected apps. |
| **For contingent workers** | **IS the system of entry** — hiring managers use the "Create Contingent Worker" form in Passport. Passport then provisions to AD. |
| **Downstream targets** | On-prem Active Directory, Exchange, connected applications |
