# Background Drivers & Business Problems

> **Purpose:** Document the strategic context, business drivers, and operational problems that justify the Identity Management (IDM) transformation initiative.

---

## Executive Summary

EUSP manages approximately **110,000 identities** across multiple regions through fragmented platforms and heavy reliance on external vendors. This creates operational inefficiency, high costs, security risks, and limits EUSP's ability to deliver modern identity services. The IDM transformation addresses these systemic issues through platform consolidation, vendor dependency elimination, and modernization.

---

## Business Problems

### 1. Vendor Dependency & Operational Control

**Problem Statement:**
EUSP's identity operations are outsourced to external vendors with limited internal capability and control.

| Vendor | Scope | Annual Cost | Risk |
|--------|-------|-------------|------|
| **Avanade** | JPIDM operations (60,000 users, Japan) | ~¥100M/year | Single point of failure; limited innovation; knowledge retention risk |
| **TCS** | Partner L3 operations (IT service management) | TBD | High identity-related ticket volume (2,092/6.5 months: 1,167 credential mgmt, 607 access control, 318 account lifecycle); manual intervention dependency; limited scalability |

**Impact:**
- **Cost:** ~¥100M+/year in operational expenses with no internal capability build-up
- **Innovation velocity:** Vendor contract cycles slow down feature delivery and modernization
- **Knowledge loss:** Tribal knowledge resides with vendors; difficult to transfer
- **Service quality:** Indirect control limits service improvement and responsiveness
- **Security & compliance:** Vendor access to sensitive identity data creates audit complexity

**Evidence:**
- JPIDM operational costs: ¥100M/year (Avanade)
- Partner L3 identity-related operations: 2,092 tickets over 6.5 months (TCS operations analysis)
  - Credential management: 1,167 tickets (MFA reset: 1,040, password reset: 120)
  - Access control & permissions: 607 tickets (group membership, ACLs, admin roles)
  - Account lifecycle: 318 tickets (account creation, configuration, attributes)
- 26.9% of all partner tickets are identity-related operations
- Limited FTE operational knowledge: Dependency on vendor runbooks and tribal knowledge

---

### 2. Platform Fragmentation

**Problem Statement:**
Identity management is handled by **three independent platforms** across regions, creating inconsistency and operational complexity.

| Platform | Region | Technology Stack | Ownership | Status |
|----------|--------|------------------|-----------|--------|
| **JPIDM** | Japan (JP) | ASP.NET MVC 4 + MIM + SQL Server | EUSP (via Avanade) | End-of-life (MIM 2029) |
| **APIDM** | Asia-Pacific (AP) | MIM-based (undocumented) | EUSP | Unknown capabilities |
| **Passport** | AM/EU/SM | SailPoint IdentityIQ | Non-EUSP (Ramnath's team) | Active |

**Impact:**
- **Inconsistent user experience:** Different processes, portals, and capabilities per region
- **Operational complexity:** Separate teams, runbooks, and support models for each platform
- **Integration challenges:** No unified API layer; each platform has unique interfaces
- **Compliance risk:** Inconsistent security controls and audit trails across regions
- **Duplication of effort:** Similar capabilities implemented multiple times

**Evidence:**
- 3 separate provisioning platforms serving 110,000 identities
- No cross-platform visibility or unified reporting
- Different data models and attribute mappings per platform
- Manual coordination required for global operations

---

### 3. Technical Debt & End-of-Life Risk

**Problem Statement:**
JPIDM is built on **legacy technology with approaching end-of-life dates**, creating urgent modernization pressure.

| Component | Technology | Status | Deadline | Risk |
|-----------|------------|--------|----------|------|
| **MIM** | Microsoft Identity Manager | End-of-support | **2029** | Unsupported technology; security vulnerabilities |
| **JPIDM Portal** | ASP.NET MVC 4 (VB.NET) | Legacy | N/A | Recruitment challenges; limited ecosystem |
| **ADAccessor** | ADSI direct writes | Legacy pattern | N/A | Violates API-driven architecture principles |

**Impact:**
- **Security risk:** Running unsupported software after 2029 creates compliance issues
- **Maintenance complexity:** Limited developer pool for VB.NET and legacy MIM
- **Integration barriers:** ADSI pattern incompatible with modern cloud-first architecture
- **Innovation blocked:** Legacy stack prevents adoption of modern identity features

**Evidence:**
- MIM end-of-support: 2029 (3 years from now)
- JPIDM: ASP.NET MVC 4 + VB.NET (released 2012, legacy technology)
- ADAccessor: Direct ADSI writes (incompatible with SCIM/REST API-driven model)

---

### 4. Lack of Self-Service & Automation

**Problem Statement:**
End users and managers lack self-service capabilities, creating high operational burden on IT support teams.

**Manual Operations Requiring IT Intervention:**

| Operation Type | Monthly Tickets | Annual Impact | Root Cause |
|----------------|-----------------|---------------|------------|
| MFA Reset | 1,040 (13.4% of total) | 12,480 tickets | No self-service; requires L3 intervention |
| Password Reset | 120 (1.5% of total) | 1,440 tickets | Limited SSPR adoption; process barriers |
| Group Membership | 284 (3.6% of total) | 3,408 tickets | No delegated admin or self-service portal |
| Account Creation | 166 (2.1% of total) | 1,992 tickets | Manual provisioning; no automation |
| App Registration | 252 (3.2% of total) | 3,024 tickets | Centralized IT approval required |

**Impact:**
- **User productivity loss:** 1-3 day wait times for simple operations (password reset, group access)
- **IT operational burden:** ~7,784 tickets/6.5 months = 1,200 tickets/month requiring manual intervention
- **Cost:** Estimated 1.5 hours/ticket average × 1,200 tickets/month = 1,800 hours/month of L3 time
- **User satisfaction:** Poor experience due to delays and rigid processes

**Evidence:**
- Analysis of 7,784 tickets (August 2025 – February 2026) from partner L3 operations
- 15% of tickets are credential management (MFA/password reset)
- 7.8% are access control operations (groups, permissions)
- 4.1% are account lifecycle operations (creation, modification)

---

### 5. No AI Agent Ready Architecture

**Problem Statement:**
Current platforms use legacy protocols (ADSI, file-based feeds, custom scripts) that are incompatible with modern AI agent frameworks and automation tools.

**Current Architecture Limitations:**

| Platform | Integration Method | AI Agent Compatibility | Limitation |
|----------|-------------------|------------------------|------------|
| JPIDM | ADSI direct writes, file-based EINS feed | ❌ No | No REST API; no webhook events; no SCIM support |
| EINS | File-based data distribution | ❌ No | Batch processing; no real-time API |
| Passport | SailPoint proprietary API | ⚠️ Limited | Vendor-specific; not standards-based |

**Impact:**
- **Can't leverage AI agents:** No API surface for AI-driven automation (MCP, Agent Framework)
- **Manual operations remain:** No intelligent automation to reduce L3 ticket volume
- **Future innovation blocked:** Can't adopt emerging AI tools for identity management
- **Integration complexity:** Each platform requires custom integration code

**Modern Requirements:**
- **SCIM/REST APIs:** Standard protocols for provisioning and identity operations
- **Webhook/Event-driven:** Real-time notifications for identity lifecycle events
- **MCP compatibility:** Model Context Protocol for AI agent orchestration
- **API-first design:** All operations exposed via API for automation and integration

---

## Strategic Drivers for Transformation

### 1. Cost Reduction

**Target:** Eliminate ~¥100M/year Avanade operational costs through FTE enablement and DevOps maturity.

**Approach:**
- **FY26:** 20% reduction through team build-out and knowledge transfer
- **FY27:** 70% reduction through platform deployment and operational transition
- **FY28:** 100% elimination through full FTE ownership

---

### 2. Operational Control & Agility

**Target:** Bring identity operations in-house with EUSP FTE ownership.

**Approach:**
- Build product management and AI DevOps capability (FY26)
- Establish CI/CD pipeline and operational practices (FY26)
- Transition operations from vendors to EUSP (FY26-FY28)

---

### 3. Global Standardization

**Target:** Single provisioning pipeline for all regions and identity types.

**Approach:**
- Replace/consolidate JPIDM, APIDM, Passport (Pattern 1 or Pattern 2)
- SCIM/API-driven architecture per provisioning principles
- Unified governance and compliance model

---

### 4. User Empowerment & Self-Service

**Target:** Enable end users to manage own identities without IT intervention.

**Approach:**
- Self-service MFA reset, password reset, group management
- Delegated administration for managers and team leads
- AI-powered intelligent assistants for guided workflows

---

### 5. AI Agent Enabled Operations

**Target:** API-first architecture compatible with AI agent frameworks (MCP, Agent Framework).

**Approach:**
- SCIM/REST API exposure for all operations
- Webhook/event-driven architecture for real-time automation
- MCP compatibility for AI orchestration
- Reduce manual L3 operations through intelligent automation

---

## Success Metrics

| Metric | Baseline (Current) | Target (FY28) | Measurement |
|--------|-------------------|---------------|-------------|
| **Avanade cost** | ¥100M/year | ¥0 | Contract termination |
| **L3 manual tickets** | 1,200/month | <300/month | Ticket volume reduction (75%) |
| **Self-service adoption** | <10% | >70% | % of operations completed without IT |
| **Platform count** | 3 platforms | 1 platform (Pattern 2) or 2 (Pattern 1) | Platform consolidation |
| **MIM dependency** | 100% | 0% | MIM decommissioned by 2029 |
| **API coverage** | <20% | >95% | % of operations exposed via SCIM/REST API |
| **FTE operational ownership** | 0% (vendor-led) | 100% | EUSP owns full lifecycle |

---

## References

- **IDM Strategy Scoping Document:** [IDM_STRATEGY_SCOPING_V2.md](../IDM_STRATEGY_SCOPING_V2.md)
- **EUSP IT Ops Efficiency Discussion Paper:** [DISCUSSION_PAPER_EUSP_IT_OPS_EFFICIENCY.md](../DISCUSSION_PAPER_EUSP_IT_OPS_EFFICIENCY.md)
- **JPIDM Platform Documentation:** [knowledge/platforms/JPIDM.md](platforms/JPIDM.md)
- **Provisioning Architecture Principles:** [PROVISIONING_ARCHITECTURE_PRINCIPLES.md](PROVISIONING_ARCHITECTURE_PRINCIPLES.md)

---

**Document Owner:** EUSP Strategy Team  
**Last Updated:** March 26, 2026  
**Status:** Active - Strategic Planning
