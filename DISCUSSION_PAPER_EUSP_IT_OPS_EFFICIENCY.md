**# Discussion Paper: EUSP IT Ops Efficiency Initiative
**Partner Outsourced Operations Transformation**

---

## Executive Summary

This discussion paper outlines a strategic approach to significantly reduce manual IT operations tasks across EUSP partner outsourced operations by analyzing real ticket distribution patterns from partner-managed environments and proposing targeted efficiency improvements. By focusing on high-volume, repeatable operations handled by partner teams, we can deliver measurable workload reduction, improved user experience, and better security compliance.

**Current State:** 7,784 tickets analyzed from partner outsourced ops operations (Work Orders)
**Analysis Period:** August 2025 – February 12, 2026 (6.5 months of core operational data)
**Subcategories:** 85 unique subcategories identified across 15 main categories
**Target:** Reduce manual partner L3 team intervention by enabling self-service, leveraging automation, and optimizing partner operational processes

---

## 1. Problem Statement

### Current Challenge
EUSP partners operating as outsourced ops teams are handling a significant volume of repetitive, manual tickets that could be addressed through:
- **Eliminate the Driver** (preventing the need entirely)
- **User Empowerment** (enabling users to solve problems independently)
- **Automation** (using existing or new tools to reduce partner team manual effort)

---

## 2. Priority Target: Partner Outsourced Ops

**High-Volume Operations (L3 Tickets via Helix Analysis):** The Top x operations target repetitive tasks across partner teams, identified through ticket volume analysis from the Helix dataset (7,784 tickets). High volume × low per-ticket cost = significant aggregate savings.

**High-Cashout Scoped Operations (via Ops Analysis):** Parallel initiatives like JPIDM Identity Management address critical systems with concentrated partner dependency and high annual costs through operational assessment. While narrower in scope, these represent substantial annual cashout that can be eliminated through DevOps maturity and FTE enablement.

---

## 3. Approach: Analysis Methodology

### Methodology
**L3 Operations Analysis (via Helix Ticket Analysis):**
- **Data Source:** Combined INCIDENT and WORK ORDER ticket analysis from partner outsourced operations
- **Sample Size:** 7,784 tickets handled by partner L3 teams (August 2025 – February 12, 2026)
- **Scope:** Partner-managed IT service delivery operations across EUSP customer base
- **Analysis Method:** Quantitative ticket volume and categorization from Helix dataset

**High-Cashout Scoped Operations (via Ops Analysis, e.g., JPIDM):**
- High-value, specialized systems with concentrated partner dependency and high annual costs
- Addressed through operational assessment and DevOps enablement planning
- Analysis Method: Operational review of systems, costs, and partner dependencies
- Complementary to L3 efficiency improvements; applies the same Build/Consume decision framework

### Key Assumptions
- Partner L3 team workload correlates directly with ticket volume from analyzed partner operations data
- Current ticket TAT (turnaround time) reflects typical duration of partner team task execution
- Ticket categorization from partner operations is reliable indicator of operation type
- Process improvements at ticket level and partner team efficiency directly translate to reduced manual workload
- Partner teams utilize standardized processes across EUSP customer base, making improvements scalable

---

## 4. Prioritization Principles

Operations will be evaluated and prioritized using the following framework:

| Principle | Rationale | Metric |
|-----------|-----------|--------|
| **Workload Impact** | Ticket volume + estimated TAT per ticket | Hours saved annually |
| **User Empowerment & Capability** | Enables end users to solve problems independently and accomplish more without GSD involvement | User empowerment adoption % + time-to-resolution improvement |
| **Security & Compliance** | Reduces manual errors, audit trail improvements | Risk reduction |
| **Effort to Address** | Resource investment vs. benefit | Implementation effort (hours) |

**Prioritization Formula:**
```
Score = (Volume × TAT × UX_Benefit) / Implementation_Effort
```

Operations with highest scores get priority in phased implementation.

---

## 5. High Volume Operations for Prioritization

Based on analysis of 7,784 tickets, the following operations represent the highest-impact opportunities:

### 1. **MFA Reset & Password Reset (Credential Management)**
- **Current Volume:** 1,167 tickets combined (15.0% of total)
  - MFA Reset: 1,040 tickets (13.4%)
  - Password Reset/Rotation: 120 tickets (1.5%)
  - Credential Management - Other: 7 tickets (0.1%)
- **Annual Hours Saved Potential:** 1,500+ hours (assuming 45 min/ticket)
- **Potential Solution:** 
  1. **Eliminate Driver:** Move to passwordless authentication (WHfB, Passkey) + Custom Password Scramble Opt-in to eliminate password/MFA reset needs entirely
  2. **User Empowerment:** Portal-based self-service (MFA Reset Self-Service + Self-Service Password Reset) for interim user management

### 2. **Access Control & Group Management**
- **Current Volume:** 607 combined (7.8% of total)
  - Group Membership: 284 tickets (3.6%)
  - Permission Management - Other: 148 tickets (1.9%)
  - Application Access & ACL: 101 tickets (1.3%)
  - Admin Role Assignment: 70 tickets (0.9%)
  - Dynamic Groups: 4 tickets (0.1%)
- **Annual Hours Saved Potential:** 900+ hours (assuming 1.5h/ticket average)
- **Potential Solution:** IGA package (Sailpoint) or open source (midPoint, OpenIAM) + custom built IGA solution for self-service/provisioning orchestration

### 3. **Account Management & User Lifecycle**
- **Current Volume:** 318 combined (4.1% of total)
  - Account Creation: 166 tickets (2.1%)
  - Account Management - Other: 57 tickets (0.7%)
  - Account Configuration: 27 tickets (0.3%)
  - Admin Account Lifecycle: 19 tickets (0.2%)
  - Attribute Management: 17 tickets (0.2%)
  - Out of Office/Presence Management: 15 tickets (0.2%)
  - Other account lifecycle: 17 tickets (0.2%)
- **Annual Hours Saved Potential:** 540+ hours (assuming 1.7h/ticket average)
- **Potential Solution:** IGA package (Sailpoint) or open source (midPoint, OpenIAM) + custom built IGA solution for self-service/provisioning orchestration

### 4. **Entra ID App Management**
- **Current Volume:** 615 tickets (7.9% of total)
  - App Registration and Config: 252 tickets (3.2%)
  - Application Services - Other: 180 tickets (2.3%)
  - Secrets/Keys/Certificates: 156 tickets (2.0%)
  - App Configuration Changes: 27 tickets (0.3%)
- **Annual Hours Saved Potential:** 750+ hours (assuming 1.3h/ticket average)
- **Potential Solution:** Empower app owners to self-serve app management (registration, configuration, secrets management) with IT oversight and governance guardrails


## 6. Solution Strategy Framework

### Part A: How to Address These Issues

The Efficiency Strategy Model

Apply solutions in priority order, evaluating each operation against these two strategic approaches:

```
┌─────────────────────────────────────────────────────────┐
│ 1. ELIMINATE THE DRIVER                                 │
│    Can we prevent the need for this entirely?            │
│    • Retire legacy services/architecture                 │
│    • Move to passwordless authentication                 │
│    • Modernize to eliminate root cause                   │
│    Example: Moving to Windows Hello eliminates MFA       │
│    password reset tickets; removing password auth        │
│    eliminates credential management operations           │
└─────────────────────────────────────────────────────────┘
                           ↓
┌─────────────────────────────────────────────────────────┐
│ 2. USER EMPOWERMENT                                     │
│    Can end users solve this independently?               │
│    • Portal-based request/action                         │
│    • Minimal GSD involvement                             │
│    • Governance controls + audit trails                  │
│    • AI-Powered Intelligent Assistants                   │
│      - Conversational interfaces for self-diagnosis      │
│      - Natural language support interactions             │
│      - Guided workflows with AI recommendations          │
│    Example: MFA reset, password reset, request access    │
│    With AI: Smart chatbot diagnoses issue, guides user   │
│    to self-resolution without GSD involvement            │
└─────────────────────────────────────────────────────────┘
```

---

### Part B: Picking the Right Solution (Implementation Tools & Platforms)

---

### Build vs. Buy/Consume Decision Framework

When addressing an operation (after deciding on Eliminate Driver vs. User Empowerment strategy), evaluate which tools and platforms to use:

**Available Platforms (BUY/CONSUME - Preferred if finantially justfied):**
- **SailPoint**
- **Microsoft 365**

**Cost Model:** Licensing (recurring) + Configuration (one-time)
**Timeline:** Weeks-Months
**Risk:** Medium (proven platforms, vendor support)

### Decision Framework: Build vs. Consume

#### **BUY/CONSUME (Licensed Platforms)** - *Recommended Default*
- ✓ Established platforms exist (SailPoint etc.)
- ✓ Standard IT ops operation (no unique requirements)
- ✓ Limited internal resources
- ✓ Need faster time-to-value (within 3-6 months)
- ✓ Vendor support/SLA required
- ✓ Predictable recurring costs preferred

#### **BUILD INTERNALLY (Custom Development)** - *Only When Justified*
- ✓ **Requires:** EUSP Engineering Team resources and ownership
- ✓ Unique requirements not met by existing platforms
- ✓ Significant competitive advantage achievable
- ✓ Volume justifies ongoing maintenance (800+ tickets/year)
- ✓ Sufficient internal DevOps resources available
- ✓ 5-year TCO favorable vs. licensing
- ✓ Integration complexity requires custom smart service solution

---

## 7. If Building Internally: Technology Stack & Implementation Approach

### Technology Stack Selection

#### **Option A: Low-Code/No-Code (LCNC)**
**Platforms:** Power Platform (Power Automate, Power Apps), OutSystems, Mendix

**Advantages:**
- Faster development (weeks vs. months)
- Lower operational overhead
- Smaller DevOps team required (1-2 engineers)
- Easier to modify/maintain

**Disadvantages:**
- Vendor lock-in risk
- Limited customization for complex scenarios
- Possible licensing costs still required
- Scalability constraints for multi-tenant ops

**Best For:** Simple operations with clear workflows (MFA reset, password reset, basic group requests)

**Estimated Effort:** 200-400 hours per operation

---

#### **Option B: Custom Development incl. Build on Top of Open Source**

**Advantages:**
- Cost effective
- Maximum flexibility and customization
- Optimal performance for high-volume operations
- Purpose-built for specific use cases

**Disadvantages:**
- Development timeline upfront
- Ongoing maintenance burden (2-3 AI equipped engineers)
- Security updates/compliance overhead

**Best For:** Complex, multi-step orchestrations or unique EUSP requirements

**Estimated Effort:** 1,000-2,500 hours per operation

---

## 8. Critical Organizational Dependencies

Success requires organizational support across five key function areas. 
### The AI Orchestration Model: A Couple of Humans + Many AI Agents

**Core Concept:** One Principal Architect and one Mid-Career Engineer orchestrate a team of specialized AI agents rather than hiring additional human developers.

- **Principal Architect:** Strategic direction, solution architecture, code review validation, quality gates, stakeholder management, technology roadmap
- **Mid-Career Engineer:** Hands-on implementation, AI agent configuration, integration development, operational support
- **AI Agent Team:** Code generation, test automation, documentation, architectural suggestions, code analysis, intelligent routing in production systems
- **Multiplier Effect:** Each AI agent handles specific tasks (writing API endpoints, generating unit tests, creating deployment scripts, suggesting optimizations). Engineers manage agent workflows, validate outputs, and maintain system integrity.
- **Scaling Without Hiring:** As workload increases, add or reconfigure AI agents. No need to hire additional engineers.

### Core Team Skillsets

**Principal Architect**
- AI orchestration & multi-agent system design
- Solution architecture & strategic prompting strategies
- Azure cloud & enterprise integration
- Strategic leadership (managing Principal Architect + Mid-Career engineer collaboration with AI agent teams)
- Data schema design, API contracts, integration architecture
- Technology roadmap and architectural governance

**Mid-Career Engineer**
- Full stack development (C#, Python, Node.js) with AI-assisted coding
- AI agent design & orchestration (Agent Framework, multi-agent systems)
- Power Platform (Power Automate, Power Apps) for user empowerment—leveraging AI agents for workflow design
- DevOps & infrastructure-as-code (ARM, Bicep)
- Production support & troubleshooting


---

### Engineering Discipline

- **Spec-Driven Development:** All features require written specifications (requirements, acceptance criteria, API contracts) before coding begins
- **Code Review & Standards:** Peer review (can include AI agents for automated code analysis/review); consistent coding standards and architecture patterns
- **Test-Driven Approach:** Unit tests, integration tests, UAT validation before deployment
- **Documentation:** Technical specs, API docs, deployment guides, runbooks
- **No Vibe Coding:** Decisions are data-driven and requirements-based, not intuition-based or ad-hoc

---

## 9. JPIDM Identity Management – Ongoing DevOps Transformation Initiative

### Overview
**JPIDM** is a hybrid identity lifecycle management system combining:
- **Microsoft Identity Manager (MIM)** as the core identity synchronization engine
- **Custom .NET-based subsystem** for extended identity management capabilities
- **Coverage:** 60,000 users across Japan operations
- **Sync Source:** Upstream HR database (EINS) feeds identity data into the system
- **Self-Service Capabilities:** User credential management, group membership management, etc.

---

**Concrete Application:** This ongoing project demonstrates how EUSP is reducing partner dependency and regaining operational control through DevOps maturity and FTE enablement—applying the Build/Consume framework to mission-critical systems.

### **9.1 Current Issues**

**Critical Challenges:**

1. **MIM Replacement Deadline**
   - Microsoft Identity Manager must be replaced by 2029
   - Currently central to identity synchronization
   - Risk of unsupported technology

2. **Subsystem Management Complexity**
   - Custom .NET-based solution developed by Hitachi long ago
   - Highly partner-dependent operations
   - FTE has limited deep knowledge of the solution

3. **DevOps Maturity Gap**
   - No established DevOps practices
   - No version control for custom code
   - Code changes without well-facilitated change management
   - High risk of configuration drift

#### **9.2 How Are We Addressing**

**Phase 1: Get FTE Back to Driver Seat** (Current Focus)

**Objectives:**
- Establish ownership and operational control with FTE team
- Build foundational DevOps capabilities and practice
- Create knowledge base and ops cadence

**Key Activities:**
1. **Adopt DevOps Practices**
   - Implement version control
   - Establish CI/CD pipeline
   - Set up issue management and tracking

2. **FTE Enablement**
   - Assigned Resources: Takayama-San, Unami-San
   - Regular coaching and knowledge transfer sessions
   - Hands-on experience with system operations
   - Documentation of tribal knowledge

3. **MIM Decommissioning Planning**
   - Assess current MIM dependencies
   - Identify replacement solutions
   - Create migration roadmap
   - Define transition milestones

**Phase 2: Solutioning** (Future)

**Objectives:**
- Design modern identity management architecture
- Select appropriate replacement solutions
- Establish scalable, maintainable solution

**Key Activities:**
1. **Requirements Definition**
   - Identity and document requirements
   - Consider global shared solution
   - Establish security and compliance requirements

2. **Architecture Design**
   - Evaluate modern identity platforms
   - Design integration architecture
   - Plan data migration strategy
   - Create implementation roadmap

3. **Implementation Planning**
   - Phased rollout approach
   - Risk mitigation strategies
   - Testing and validation plans
   - Rollback procedures

#### **9.3 Enablers**

**Coaching and Support:**
- Regular technical guidance
- Problem-solving support
- Progress reviews

**Tools and Resources:**
- AI agent aided spec and code development
- Version control systems (Git/GitHub)
- CI/CD platforms
- Issue tracking systems

**Success Metrics:**
- FTE confidence and capability levels
- Reduction in partner escalations
- System uptime and stability
- Code quality and test coverage
- Incident response time

---

## Next Steps and Timeline

### Q2 2026
- Complete Phase 1 for JPIDM (FTE enablement)
- Launch pilot for MFA self-service portal
- Establish baseline metrics for L3 ticket reduction

### Q3 2026
- Begin Phase 2 planning for JPIDM (Architecture design)
- Expand automation to groups management
- Review and optimize MFA self-service based on pilot results

### Q4 2026
- Complete requirements definition for MIM replacement
- Launch groups management self-service
- Begin app management automation pilot

### 2027
- Full implementation of MIM replacement solution
- Scale automation across all identified opportunities
- Achieve target cost reduction goals

---

---

## Appendix: Supporting Data

**Generated:** February 19, 2026 at 18:31:22
**Total Tickets:** 7,784

---


### Category Hierarchy with Subcategories

### 1. MESSAGING SERVICES (2,026 tickets - 26.0%)

```
├─ Distribution Lists                       (808 tickets, 10.4%)
├─ Email Services - Other                   (410 tickets, 5.3%)
├─ Exchange Configuration                   (339 tickets, 4.4%)
├─ Shared Mailbox                           (282 tickets, 3.6%)
├─ Application/Service Mailbox              (49 tickets, 0.6%)
├─ Mailbox Access & Permissions             (47 tickets, 0.6%)
├─ Room/Resource Mailbox                    (20 tickets, 0.3%)
├─ M365 Email Services                      (19 tickets, 0.2%)
├─ Email Configuration                      (16 tickets, 0.2%)
├─ Email Troubleshooting                    (14 tickets, 0.2%)
├─ Mailbox Migration/Transfer               (13 tickets, 0.2%)
└─ Mailbox Creation                         (9 tickets, 0.1%)
```

### 2. USER CREDENTIAL MANAGEMENT (1,167 tickets - 15.0%)

```
├─ MFA Reset                                (1,040 tickets, 13.4%)
├─ Password Reset/Rotation                  (120 tickets, 1.5%)
└─ Credential Management - Other            (7 tickets, 0.1%)
```

### 3. TEAMS & VOICE SERVICES (805 tickets - 10.3%)

```
├─ Voice/Phone Services                     (506 tickets, 6.5%)
├─ Teams Collaboration                      (195 tickets, 2.5%)
├─ Communication Services - Other           (102 tickets, 1.3%)
└─ Chat/Communication                       (2 tickets, 0.0%)
```

### 4. APP MANAGEMENT (615 tickets - 7.9%)

```
├─ App Registration and Config              (252 tickets, 3.2%)
├─ Application Services - Other             (180 tickets, 2.3%)
├─ Secrets/Keys/Certificates                (156 tickets, 2.0%)
└─ App Configuration Changes                (27 tickets, 0.3%)
```

### 5. ACCESS CONTROL & PERMISSIONS (607 tickets - 7.8%)

```
├─ Group Membership                         (284 tickets, 3.6%)
├─ Permission Management - Other            (148 tickets, 1.9%)
├─ Application Access & ACL                 (101 tickets, 1.3%)
├─ Admin Role Assignment                    (70 tickets, 0.9%)
└─ Dynamic Groups                           (4 tickets, 0.1%)
```

### 6. TENANT CONFIGURATION (350 tickets - 4.5%)

```
└─ Tenant Configuration                     (350 tickets, 4.5%)
```

### 7. SHAREPOINT & ONEDRIVE (319 tickets - 4.1%)

```
├─ Collaboration Services - Other           (234 tickets, 3.0%)
├─ SharePoint Access/Permissions            (42 tickets, 0.5%)
├─ Collaboration Services                   (32 tickets, 0.4%)
├─ Site Configuration                       (9 tickets, 0.1%)
├─ Document/File Management                 (1 tickets, 0.0%)
└─ OneDrive Provisioning & Restoration      (1 tickets, 0.0%)
```

### 8. USER ACCOUNT LIFECYCLE (318 tickets - 4.1%)

```
├─ Account Creation                         (166 tickets, 2.1%)
├─ Account Management - Other               (57 tickets, 0.7%)
├─ Account Configuration                    (27 tickets, 0.3%)
├─ Admin Account Lifecycle                  (19 tickets, 0.2%)
├─ Attribute Management                     (17 tickets, 0.2%)
├─ Out of Office/Presence Management        (15 tickets, 0.2%)
├─ Account Security/Unlock                  (9 tickets, 0.1%)
└─ New Hire/Starter Onboarding              (8 tickets, 0.1%)
```

### 9. ACTIVE DIRECTORY MANAGEMENT (181 tickets - 2.3%)

```
├─ AD User/Computer Management              (148 tickets, 1.9%)
└─ Directory Services - Other               (33 tickets, 0.4%)
```

### 10. INFORMATION REQUESTS (164 tickets - 2.1%)

```
├─ Data Services - Other                    (74 tickets, 1.0%)
├─ Data/Attribute Extract                   (65 tickets, 0.8%)
└─ Audit/Compliance Report                  (25 tickets, 0.3%)
```

### 11. POWER PLATFORM (89 tickets - 1.1%)

```
├─ Power Platform Configuration             (83 tickets, 1.1%)
└─ Automation Services - Other              (6 tickets, 0.1%)
```

### 12. TROUBLESHOOTING/ISSUES (64 tickets - 0.8%)

```
├─ Application Issues                       (51 tickets, 0.7%)
├─ System Outage/Downtime                   (10 tickets, 0.1%)
├─ Error/Exception Issues                   (2 tickets, 0.0%)
└─ Connectivity/Network Issues              (1 tickets, 0.0%)
```

### 13. SOFTWARE LICENSE/ENTITLEMENTS (57 tickets - 0.7%)

```
├─ Licensing - Other                        (40 tickets, 0.5%)
└─ License Assignment                       (17 tickets, 0.2%)
```

### 14. DATA MIGRATION (46 tickets - 0.6%)

```
├─ Transfer Services - Other                (27 tickets, 0.3%)
├─ Device Cleanup                           (13 tickets, 0.2%)
└─ Transfer Operations                      (6 tickets, 0.1%)
```

### 15. AUTHENTICATION & SECURITY (4 tickets - 0.1%)

```
└─ Security Services                        (4 tickets, 0.1%)
```

### 16. OTHER/MISCELLANEOUS (972 tickets - 12.5%)

```
├─ Miscellaneous                            (842 tickets, 10.8%)
└─ Monitoring/System Alerts                 (130 tickets, 1.7%)
```

## Summary Statistics

### Complexity Distribution

- **Simple**: 3,178 (40.8%)
- **Medium**: 2,762 (35.5%)
- **Complex**: 1,844 (23.7%)

### Automatable Potential

- **Possible**: 4,931 (63.3%)
- **Yes**: 2,853 (36.7%)

### Self-Service Readiness

- **High**: 1,388 (17.8%)
- **Medium**: 1,629 (20.9%)
- **Low**: 4,767 (61.2%)

---

### IDM Cost Comparison (100,000 Users)


#### ⚠️ DISCLAIMER

**This section is preliminary and contains rough estimates without any official quotes or commitments.** The analysis, projections, and recommendations presented herein are based on current data patterns and should be treated as discussion points for further evaluation. Actual implementation plans, timelines, and financial impacts require formal assessment, stakeholder validation, and official approval before proceeding.

---
| Cost Component | **SailPoint** | **Open Source + Customize** | **Custom Built** |
|---|---|---|---|
| **5-Year TCO** | **$6M–$18M+** | **$400K–$800K** | **$575K–$1.15M** |
| **Risk Level** | Low | Medium | Medium-High (requires skilled team) |
| **Compliance Readiness** | High | Medium | Depends on spec & implementation |
| **5-Year TCO (100K users)** | ~$6M–$18M+ | ~$400K–$800K | ~$575K–$1.15M |
| **Initial Cost** | $1.4M–$3.8M | $150K–$300K | $200K–$400K |
| - Licensing (Year 1) | $1M–$3M | — | — |
| - Implementation/services | $400K–$800K | $150K–$300K labor | — |
| - Development HC (Principal Architect + Mid-Career Engineer, 6-12mo) | — | — | $200K–$400K |
| **Recurring Cost (Annual)** | $1.2M–$3.5M | $50K–$100K | $75K–$150K |
| - Licensing (Year 2+) | $1M–$3M | — | — |
| - Support/maintenance | $200K–$500K | minimal | — |
| - Infrastructure/hosting | included | $50K–$100K | — |
| - Internal staffing (20% HC) | — | — | $25K–$75K |

**5-Year TCO Summary (100K users):**
- **SailPoint:** ~$6M–$18M+ (~$1–$3 per user per month; high licensing, vendor support included)
- **Open Source:** ~$400K–$800K (lowest cost, community support)
- **Custom Built:** ~$575K–$1.15M (mid-range, full internal control)

**Engineering HC Assumptions:**
- **Development HC (Initial):** 1 Principal Architect + 1 Mid-Career Engineer (AI-equipped), 6–12 months at full-time equivalent. Estimated blended rate: $200K–$400K total ($100K–$200K per role annually).
- **Internal Staffing (Recurring):** 20% HC allocation for ongoing maintenance, updates, and support (approximately 0.4 FTE annual post-launch, AI-equipped). Estimated at $25K–$75K annually depending on staffing model.
- **AI Augmentation:** Assumes engineers leverage AI agents for code generation, testing, documentation, and architectural support, enabling 2-person team to deliver 5–6 developer equivalents.

---