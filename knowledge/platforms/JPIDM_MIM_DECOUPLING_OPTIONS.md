# JPIDM & MIM: Architectural Independence and Decommissioning Options

> **Purpose:** To clarify that JPIDM and MIM are architecturally independent, and that MIM's 2029 end-of-support does not automatically mandate JPIDM replacement.

---

## Key Principle: JPIDM ≠ MIM

**JPIDM uses MIM, but is not dependent on MIM architecturally.**

JPIDM is composed of:
- **ASP.NET MVC 4 web portal** (user interface, request submission)
- **SQL Server (IDM_DB)** (100+ tables for identity records, group membership, request tracking)
- **ADAccessor layer** (ADSI-based direct writes to Active Directory)
- **Request_LST workflow engine** (approval routing and request lifecycle management)
- **MIM component** (backend sync for credential operations: password reset, account unlock, MIM sync jobs)

MIM is **one component among many**, not the core platform. JPIDM can theoretically continue to operate without MIM if the MIM-dependent features are replaced with alternative implementations.

---

## MIM's Role in JPIDM

MIM currently provides:

| Feature | MIM Dependency |
|---------|----------------|
| Password reset (self-service) | MIM Portal for security-question–based reset |
| Account unlock | MIM backend operation |
| Backend sync jobs | MIM synchronization service (sync scope requires investigation) |

All other JPIDM capabilities (group management, mailbox provisioning, approval workflows, AD account operations, etc.) are **independent of MIM** and run through the ASP.NET MVC portal + ADAccessor layer.

---

## Two Strategic Options for MIM 2029 End-of-Support

Organizations facing MIM's 2029 end-of-support deadline have **two architectural options**:

### Option 1: Decouple MIM from JPIDM

**Approach:** Replace MIM-dependent features with alternative implementations; retain the JPIDM platform.

**What changes:**
- Replace MIM Portal password reset with **Azure AD Self-Service Password Reset (SSPR)** or equivalent
- Replace MIM account unlock with native AD unlock capability or Azure AD–based unlock
- Replace MIM sync jobs with **Microsoft Entra Provisioning Agents** or alternative sync mechanism
- Remove MIM infrastructure (servers, licenses, operational overhead)

**What stays:**
- JPIDM web portal (ASP.NET MVC 4)
- SQL Server (IDM_DB)
- ADAccessor layer (AD operations)
- Request_LST workflow engine
- All self-service capabilities (group management, mailbox management, approval workflows)

**Outcome:**
- ✅ MIM 2029 deadline met (MIM decommissioned)
- ✅ JPIDM platform continues to operate
- ✅ Potentially lower cost than full platform replacement
- ✅ Avanade contract renegotiation opportunity (reduced scope)
- ⚠️ ASP.NET MVC 4 / VB.NET technical debt remains
- ⚠️ ADSI-based provisioning (ADAccessor) remains (violates API-driven architecture principles)

---

### Option 2: Replace Entire JPIDM Platform

**Approach:** Replace JPIDM + MIM together as part of a comprehensive modernization.

**What changes:**
- Replace ASP.NET MVC 4 portal with modern web UI
- Replace SQL Server (IDM_DB) with new data model
- Replace ADAccessor (ADSI) with **SCIM/REST API-driven provisioning** (per [PROVISIONING_ARCHITECTURE_PRINCIPLES.md](../PROVISIONING_ARCHITECTURE_PRINCIPLES.md))
- Replace Request_LST workflow engine with modern approval/orchestration capability
- Retire MIM infrastructure

**What stays:**
- Functional capabilities (all JPIDM features must be preserved in new platform)

**Outcome:**
- ✅ MIM 2029 deadline met (MIM decommissioned)
- ✅ JPIDM technical debt eliminated (ASP.NET MVC 4, VB.NET, ADSI)
- ✅ API-driven architecture principles met (SCIM/REST to Entra ID)
- ✅ Opportunity to consolidate with other platforms (APIDM, Passport)
- ✅ Avanade contract eliminated (EUSP takes ownership)
- ⚠️ Higher cost and complexity
- ⚠️ Longer implementation timeline
- ⚠️ Higher migration risk (all features must be rebuilt/replaced)

---

## Decision Factors

| Factor | Favors Option 1 (Decouple MIM) | Favors Option 2 (Replace Platform) |
|--------|-------------------------------|-------------------------------------|
| **Timeline** | Shorter (1-2 years) | Longer (3-5 years) |
| **Cost** | Lower (MIM replacement only) | Higher (full platform rebuild) |
| **Technical debt resolution** | Partial (MIM removed, ASP.NET/ADSI remains) | Complete (all legacy components replaced) |
| **Alignment with architecture principles** | Partial (ADSI violation remains) | Full (SCIM/REST API-driven) |
| **Risk** | Lower (fewer moving parts) | Higher (complete replatforming) |
| **Strategic value** | Tactical fix | Strategic modernization |
| **Avanade dependency** | Reduced but not eliminated | Eliminated entirely |
| **Global consolidation opportunity** | Limited | Enables JPIDM/APIDM/Passport unification |

---

## Recommendation Criteria

**Choose Option 1 (Decouple MIM) if:**
- Timeline is critical (must meet 2029 deadline with minimal disruption)
- Budget is constrained
- JPIDM platform is otherwise fit for purpose
- Risk tolerance is low
- Organization prefers incremental change over transformational change

**Choose Option 2 (Replace Platform) if:**
- Strategic modernization is prioritized over tactical fixes
- Budget supports comprehensive replatforming
- Organization wants to eliminate Avanade dependency entirely
- Global platform consolidation (JPIDM + APIDM + Passport) is a priority
- Compliance with API-driven architecture principles is mandatory
- Willing to accept higher implementation risk for long-term strategic value

---

## Conclusion

**MIM decommissioning does NOT automatically mandate JPIDM decommissioning.**

The MIM 2029 end-of-support deadline is a **forcing function requiring a decision**, not an automatic mandate to replace JPIDM. Organizations must evaluate both options based on timeline, budget, technical debt tolerance, strategic goals, and risk appetite.

Both options are architecturally viable. The decision should be made based on organizational priorities, not MIM's end-of-life date alone.
