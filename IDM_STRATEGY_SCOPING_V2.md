# Identity Management (IDM) Strategy: Project Scoping Options — Version 2

## Purpose

This document is a revised version of the IDM Strategy Scoping document, enriched with authoritative current-state platform knowledge sourced from the Platform Expert knowledge base (`knowledge/platforms/`). Corrections and clarifications from the original version are annotated where relevant.

Our organisation manages approximately **130,000 identities** across multiple regions. Today these identities are managed by several independent platforms and teams worldwide, creating inconsistency and operational risk. This project seeks to establish a modern, sustainable IDM platform as the foundation for workforce identity management going forward.

> **Revision notes (v2):** Three significant factual corrections have been made based on authoritative platform documentation:
> 1. EINS is a **global** platform (not Japan-only). Its role is identity record authority and Global ID issuance — it does **not** provision accounts.
> 2. **MIM is backend sync infrastructure**, not the core engine of JPIDM. The core of JPIDM is an ASP.NET MVC 4 web portal backed by SQL Server.
> 3. **EINS Online Registration is the entry point for Japan contingent workers.** After EINS issues the Global ID, JPIDM receives the EINS feed and provisions the AD account. JPIDM is **not** the entry point — it is the provisioning layer downstream of EINS. IDM Web self-registration (IDM Web forms) covers shared/resource objects only, not people.

---

## Background & Drivers

| Driver | Detail |
|--------|--------|
| **Vendor dependency** | EUSP operations are heavily dependent on external vendors: **Avanade** (JPIDM operations, ~¥100M/year) and **TCS** (partner L3 operations with 2,092 identity-related tickets/6.5 months: 1,167 credential management, 607 access control, 318 account lifecycle). This creates operational risk, limits EUSP's ability to innovate, and prevents direct control over service delivery. Removing these dependencies through DevOps maturity, FTE enablement, and self-service automation will reduce costs, increase operational control, and enable faster response to business needs. |
| **JPIDM end-of-life considerations** | JPIDM is built on ASP.NET MVC 4 (VB.NET) with MIM as backend sync infrastructure. MIM reaches end of support in **2029** (within 3 years). **Important: JPIDM uses MIM but is architecturally independent**. MIM decommissioning does not automatically require JPIDM replacement. Two options exist: (1) **Decouple MIM** — replace MIM-dependent features (password reset/unlock, MIM sync) with alternatives while retaining JPIDM platform; (2) **Replace entire platform** — modernize JPIDM + MIM together. The MIM 2029 deadline is a forcing function requiring a decision, not an automatic mandate to replace JPIDM. |
| **Platform fragmentation** | Workforce provisioning is handled differently across regions: JPIDM (JP), Passport (AM/EU/SM), and APIDM (AP, undocumented). There is no consistent, global provisioning pipeline. |
| **Global standardisation** | EUSP aims to establish a new platform as the **global standard** for workforce identity provisioning so that all regions and GISC-managed teams can provision employees through one consistent pipeline. |
| **Drive self-service** | Empower end users and managers with self-service capabilities to reduce operational burden on IT teams. Enable users to manage their own accounts, group memberships, and access requests without requiring manual intervention from support teams. |
| **AI Agent ready architecture** | Build a modern, API-first architecture that supports AI agent integration and automation. The platform must expose APIs compatible with AI agent frameworks (MCP, SCIM, REST) to enable intelligent automation, reduce manual operations, and prepare for future AI-driven identity management capabilities. API-driven design ensures extensibility and enables integration with emerging AI tools. |

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

> **Correction from v1:** MIM was described as "the core engine of JPIDM." Per authoritative documentation, the JPIDM core is the **ASP.NET MVC 4 web portal + ADAccessor layer + SQL Server**. MIM is backend sync infrastructure only.
>
> **JPIDM ≠ MIM**: JPIDM uses MIM as a backend component, but they are architecturally independent. **MIM decommissioning does not automatically require JPIDM decommissioning**. Organizations may choose to: (1) decouple MIM from JPIDM by replacing MIM-dependent features with alternatives, or (2) replace the entire platform. The MIM 2029 deadline forces a decision but does not mandate full platform replacement.

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

This pattern focuses on **EUSP-managed platforms** — primarily JPIDM and APIDM — with a strategy centered on **operational independence and incremental modernization**. Rather than treating platform replacement as a single disruptive project, EUSP will build an in-house AI DevOps capability to absorb Avanade's ¥100M/year operational role, then replatform JPIDM feature-by-feature as continuous Business As Usual (BAU) delivery. Each modernized feature will be designed for global scale from day one, progressively enabling consolidation of capabilities from other platforms like Passport and EINS. The outcome: by FY28, EUSP achieves full operational autonomy with a global-scale team managing all JPIDM features across all regions, without vendor dependency.

### Strategic Approach

This pattern prioritizes **operational independence and capability building** over rapid platform replacement. Rather than treating JPIDM modernization as a disruptive "lift-and-shift" project, EUSP will transform it into a continuous delivery model — **replatforming JPIDM as Business As Usual (BAU)** through feature-by-feature migration powered by an in-house AI DevOps team.

**The narrative:**

1. **Focus on JPIDM, eliminate Avanade dependency** — JPIDM is the operational bottleneck. Avanade's ¥100M/year contract isolates EUSP from the platform, preventing innovation and creating vendor lock-in. The first priority is to bring JPIDM operations in-house, starting immediately.

2. **Build in-house AI DevOps capability and practice** — Before attempting platform replacement, EUSP will establish a dedicated AI DevOps team with the skills, tools, and practices necessary to operate, maintain, and evolve JPIDM independently. This team will absorb all Avanade operational responsibilities — incident management, change requests, monitoring, deployment — while simultaneously building the foundation for continuous modernization.

3. **Replatform JPIDM as BAU: feature-by-feature migration** — Rather than a high-risk "big bang" replacement, JPIDM will be modernized incrementally. The AI DevOps team will migrate features one by one — groups management, mailbox provisioning, approval workflows — testing and validating each migration in production before proceeding. This approach reduces risk, maintains business continuity, and allows the team to learn and adapt as they build.

4. **Design replatformed features for global scale from day one** — Each modernized feature will serve all regions, not just Japan. For example, self-service Groups Management will become a global capability, available to all EUSP regions. Over time, this approach enables **consolidation of features from other platforms like Passport and EINS** — as JPIDM capabilities are rebuilt with global reach, redundant features in other systems can be retired, progressively simplifying the identity landscape.

5. **Leverage in-house sourcing (SISC) with FTE product management** — Team capacity will scale using **SISC (Sony India Software Centre)** engineers for AI DevOps execution, while **EUSP FTEs** provide product management, architectural oversight, and strategic direction. This model balances cost efficiency with retention of critical decision-making authority within EUSP.

**Outcome:** By FY28, EUSP will operate JPIDM with full autonomy, supported by a global-scale AI DevOps team capable of managing all platform features across all regions — without Avanade. The platform will be modernized incrementally, and EUSP will own the roadmap, the operations, and the cost structure.

### Goals

1. **Remove Avanade dependency** — Bring JPIDM operations fully in-house to EUSP, eliminating approximately ¥100 million/year in Avanade operational costs.
2. **Address JPIDM/MIM architecture decision** — MIM reaches end-of-support in 2029. Since JPIDM uses MIM for specific credential operations, a decision is required: (a) **Decouple MIM from JPIDM** by replacing password reset/unlock and MIM sync with alternative implementations (e.g., Azure AD SSPR, Entra provisioning), allowing JPIDM to continue; or (b) **Replace the entire JPIDM platform** (ASP.NET MVC 4 portal, ADAccessor layer, workflow engine, MIM backend) with a modern solution. Either option must preserve all current JPIDM capabilities: FTE lifecycle, Japan contingent worker AD provisioning (via EINS feed), group/mailbox management, and approval workflows.
3. **Establish a global FTE provisioning standard** — Deploy the new platform as the single, authoritative provisioning pipeline for full-time employees across all regions, consuming identity data from EINS (Global ID authority) and HR systems.


> **Note on EINS integration:** EINS is the upstream source of Global IDs and identity data. The new platform must consume EINS-distributed data (via existing file-based interfaces) and must not attempt to replace EINS — it remains the global identity authority.

### In Scope

| Area | Detail |
|------|--------|
| **JPIDM full replacement** | Replace the entire JPIDM platform: web portal, AD operations layer (ADAccessor equivalent), approval workflow engine (Request_LST equivalent), MIM backend sync. This includes FTE lifecycle AND Japan contingent worker registration/provisioning. **Note:** This is one option; alternatively, MIM can be decoupled from JPIDM without replacing the entire platform. |
| **APIDM (TBD)** | Migrate APIDM-managed accounts to the new platform. *Note: APIDM capabilities are undocumented — a discovery phase is required before estimating effort.* |
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

### Resourcing

#### Team Structure (Phased Build-Out)

**Year 1 (FY26)**
- **Minimum 2 FTE** dedicated to product management and AI DevOps for JPIDM
  - 1 Senior engineer/product manager
  - 1 Junior engineer
- **1/3 FTE Architect supervision** (shared resource providing oversight and architectural guidance)

**Year 3 (FY28) — Scaled Team**
- **5 FTE total** for full operational ownership and global-scale product management
  - 2 Senior engineers/product managers (+1 senior)
  - 3 Junior engineers (+2 junior)
- **1/3 FTE Architect supervision** (ongoing)
- **Capability**: Product management for all JPIDM features (groups management, mailbox management, FTE/contingent worker lifecycle, approval workflows) at global scale, serving ALL REGIONS

> **Note:** Team scaling assumes gradual replatforming approach with BAU (Business As Usual) AI DevOps operations running in parallel with platform modernization.

#### Sourcing Strategy
- **SISC** (Sony India Software Centre) — potential sourcing option for team members

#### Responsibilities
- Establish AI DevOps BAU (Business As Usual) operations
- Conduct research and proof-of-concept for new architecture
- Transition JPIDM operations from Avanade to EUSP
- Platform build, deployment, and operational ownership

#### Tooling & Licenses
- GitHub Enterprise (team collaboration and version control)
- GitHub Copilot licenses for the team (AI-assisted development)

### Pros & Cons

| Pros | Cons |
|------|------|
| Clear, manageable scope — minimal stakeholders | Leaves AM/EU/SM platform fragmentation (Passport) unresolved |
| Directly addresses the 2029 MIM deadline | Two parallel provisioning paradigms remain (chosen solution + Passport) for AM/EU/SM |
| Removes Avanade cost dependency quickly | APIDM discovery gap — effort is currently unknown |
| Realistic and deliverable within a 3-year window | No unified governance layer for access certification (would require separate IGA initiative) |
| Keeps the project within EUSP authority — no need for cross-team governance | Japan contingent worker AD provisioning capability must be preserved in the replacement platform |
| EINS integration is well-understood (file-based interfaces already exist) | |

### Expected Outcome

#### Financial Goals

| Fiscal Year | Avanade Cost Reduction Target | Cumulative Savings |
|-------------|------------------------------|--------------------|
| **FY26** (ends March 2027) | 20% reduction in Avanade payments | ¥20M/year |
| **FY27** (ends March 2028) | Additional 50% reduction | ¥70M/year (cumulative) |
| **FY28** (ends March 2029) | Full elimination (if replacement completed) | ¥100M/year (full savings) |

> **Note:** Cost reduction trajectory depends on architecture decision (MIM decoupling vs. full replacement). Full ¥100M savings realized only if Avanade contract fully terminated.

#### Team Build-Out (Product Management & AI DevOps)

| Fiscal Year | Capability Milestone | Operational Transition |
|-------------|---------------------|------------------------|
| **FY26** (ends March 2027) | Functions established; FTE team hired and trained | 20% of operations transitioned |
| **FY27** (ends March 2028) | Team operational; platform deployed | 70% of operations transitioned |
| **FY28** (ends March 2029) | Full operational lifecycle ownership | 100% (Avanade eliminated) |

> **Note:** Progressive transition from Avanade operations to EUSP in-house team enables cost reduction while building operational capability.

#### End of FY26 (March 2027)
- ✅ EUSP product management and AI DevOps functions established; FTE team hired and trained
- ✅ AI DevOps practice and platform readiness: CI/CD pipeline established (build, test, deploy automation)
- ✅ JPIDM/MIM architecture decision finalized (decouple vs. full replacement)
- ✅ Platform selection completed (if full replacement chosen)
- ✅ APIDM discovery completed; capabilities documented; migration plan defined
- ✅ Target architecture validated: SCIM/API-driven provisioning design approved
- ✅ Platform build/configuration started
- ✅ EINS interface design confirmed (file-based data consumption from EINS)

#### End of FY27 (March 2028)
- ✅ EUSP product management and AI DevOps team operational; 70% of JPIDM operations transitioned from Avanade
- ✅ Hypercare period initiated; EUSP operations team trained
- ✅ Platform deployment to production environment completed
- ✅ Japan FTE provisioning migrated: Castnet → EINS → New Platform/Modernized JPIDM → AD
- ✅ Japan contingent worker provisioning migrated: EINS feed → New Platform/Modernized JPIDM → AD
- ✅ If full replacement: Phase 1 JPIDM features migrated (group/mailbox management, workflows)
- ✅ If MIM decoupling: Alternative password reset/unlock solution deployed (Azure AD SSPR or equivalent)

#### End of FY28 (March 2029)
- ✅ EUSP product management and AI DevOps team owns full operational lifecycle (deploy, monitor, support, enhance)
- ✅ ¥100M/year operational savings realized (if Avanade terminated)
- ✅ EUSP owns and operates the solution with full visibility and control
- ✅ MIM 2029 end-of-support deadline met with zero technical debt
- ✅ If full replacement: JPIDM platform fully decommissioned; Avanade contract terminated
- ✅ If MIM decoupling: JPIDM continues with all MIM dependencies removed; MIM infrastructure decommissioned
- ✅ APIDM accounts migrated; AP region standardized on new platform/modernized JPIDM
- ✅ FTE provisioning standardized globally: Workday/Castnet → EINS → New Platform OR Modernized JPIDM → Entra ID / AD (JP + AP regions)
- ⚠️ AM/EU/SM contingent workers remain on Passport (out of scope)

---

## Pattern 2 — Broad Scope: Unified Multi-Platform Approach

### Overview

Expand the project scope to **all identity management platforms**, consolidating JPIDM, APIDM, and Passport into a single, unified identity management solution. EINS remains unchanged as the global identity authority.

### Goals

1. **Platform consolidation** — Integrate JPIDM, APIDM, and Passport into one unified provisioning and governance platform.
2. **Address all identity types and regions** — Consistent lifecycle management for both FTE and contingent worker identities across all regions.
3. **Simplify the landscape** — Reduce the total number of independent provisioning platforms from three (JPIDM, APIDM, Passport) to one.

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

### Expected Outcome

#### Financial Goals

| Fiscal Year | Avanade Cost Reduction Target | Cumulative Savings |
|-------------|------------------------------|--------------------|
| **FY26** (ends March 2027) | 20% reduction in Avanade payments | ¥20M/year |
| **FY27** (ends March 2028) | Additional 50% reduction | ¥70M/year (cumulative) |
| **FY28** (ends March 2029) | Full elimination (Avanade contract terminated) | ¥100M/year (full savings) |
| **FY29-FY30** | Additional Passport licensing savings (if fully replaced) | TBD (depends on Passport licensing costs) |

> **Note:** FY26-FY27 reductions assume phased transition of JPIDM operations to EUSP. FY28 achieves full Avanade elimination once JPIDM/MIM fully replaced.

#### Team Build-Out (Product Management & AI DevOps)

| Fiscal Year | Capability Milestone | Operational Transition |
|-------------|---------------------|------------------------|
| **FY26** (ends March 2027) | Functions established; FTE team hired and trained | 20% of operations transitioned |
| **FY27** (ends March 2028) | Team operational; platform deployed | 70% of operations transitioned |
| **FY28** (ends March 2029) | Full operational lifecycle ownership | 100% (Avanade eliminated) |
| **FY29-FY30** | Expanded scope (unified platform operations) | All regions/platforms under EUSP |

> **Note:** Progressive transition from Avanade operations to EUSP in-house team enables cost reduction while building operational capability.

#### End of FY26 (March 2027)
- ✅ EUSP product management and AI DevOps functions established; FTE team hired and trained
- ✅ AI DevOps practice and platform readiness: CI/CD pipeline established (build, test, deploy automation)
- ✅ JPIDM/MIM architecture decision finalized
- ✅ Unified platform selection completed (consolidation target identified)
- ✅ APIDM discovery completed; capabilities documented
- ✅ Cross-platform feature analysis completed (JPIDM, APIDM, Passport capabilities mapped)
- ✅ Passport integration/migration strategy agreed with Ramnath's team
- ✅ Steering committee established; cross-team governance model operational
- ✅ Target architecture validated: SCIM/API-driven provisioning + unified governance

#### End of FY27 (March 2028)
- ✅ EUSP product management and AI DevOps team operational; 70% of JPIDM operations transitioned from Avanade
- ✅ Unified platform deployed to production
- ✅ Japan region migrated: FTE + contingent worker provisioning live on new platform
- ✅ JPIDM decommissioned; MIM dependencies eliminated
- ✅ AP region (APIDM) migrated to new platform
- ✅ AM/EU/SM FTE provisioning migrated: Workday → EINS → New Platform → AD (bypassing Passport for FTE flow)
- ✅ Phase 1 governance capabilities deployed (access certification for migrated populations)

#### End of FY28 (March 2029)
- ✅ EUSP product management and AI DevOps team owns full operational lifecycle (deploy, monitor, support, enhance)
- ✅ MIM 2029 end-of-support deadline met
- ✅ Avanade contract terminated; full EUSP operational ownership
- ✅ AM/EU/SM contingent worker provisioning migration started (Passport → New Platform)
- ✅ Service account lifecycle standardized across JP/AP/AM/EU/SM
- ✅ Unified access governance operational for JP/AP/AM/EU/SM FTE populations

#### End of FY29 (March 2030)
- ✅ AM/EU/SM contingent worker provisioning fully migrated to new platform
- ✅ Passport FTE + contingent worker flows decommissioned
- ✅ Unified governance extended to all identity types (FTE, contingent, service accounts)

#### End of FY30 (March 2031)
- ✅ Passport platform fully decommissioned; licensing costs eliminated
- ✅ All three legacy platforms (JPIDM, APIDM, Passport) retired
- ✅ Single provisioning pipeline operational: Workday/Castnet → EINS → Unified Platform → Entra ID / AD (all regions)
- ✅ Contingent worker provisioning unified: Single entry process → Unified Platform → AD (all regions)
- ✅ Service account lifecycle: Centralized management across all regions
- ✅ Unified access governance and certification capability across all regions and identity types
- ✅ Total platform fragmentation resolved — one operating model, one team, one set of policies

> **Architecture note (v2):** EINS is **excluded from consolidation** under both patterns. EINS is a global identity authority managing 100,000+ identity records across all Sony Group companies. It is structurally separate from provisioning platforms and is not owned by EUSP. Any attempt to replace EINS would require a separate, broader initiative beyond the scope of this project.

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

- The primary near-term goal is addressing the **MIM 2029 end-of-support deadline and Avanade dependency** within a realistic timeline. The organization must decide whether to decouple MIM from JPIDM or replace the entire platform.
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
| `knowledge/platforms/JPIDM/ARCHITECTURE.md` | JPIDM core = ASP.NET MVC 4 + SQL Server + ADAccessor; MIM is backend sync component only; JPIDM ≠ MIM (architecturally independent) |
| `knowledge/platforms/JPIDM/FEATURES.md` | IDM Web self-registration (IDM Web forms) is for shared/resource objects only — not people; Japan contingent worker AD accounts are provisioned by JPIDM via EINS feed, not via IDM Web forms; password reset handled outside JPIDM |
| `knowledge/platforms/JPIDM/SYSTEM_CONTEXT.md` | JPIDM reads from EINS/GHD tables; writes to JP.SONY.COM AD and Exchange |
| `knowledge/platforms/JPIDM/RELATIONSHIPS.md` | JPIDM does not replace EINS or Passport; each serves a distinct architectural role |
| `knowledge/platforms/Passport.md` | Passport is downstream of Workday for FTEs; is system of entry for contingent workers only |
| `knowledge/platforms/IDENTITY_PLATFORM_FEATURE_MAP.md` | EINS = identity truth; Passport = access governance; JPIDM = Japan action platform. Japan contingent workers: entry via EINS Online Registration → JPIDM receives EINS feed → provisions AD account. |
| `knowledge/Landscape.md` | Entra ID: 250K objects, 170K homed, 130K on-prem synced; JP 67K, AP 20K |

*Document prepared by EUSP — v2 revised with Platform Expert consultation. Original: IDM_STRATEGY_SCOPING.md*
