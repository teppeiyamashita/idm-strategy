# GISC Global Identity Service: Transformation Strategy Briefing

## Executive Summary

**Core Issue**: Fundamental gap between GISC's ideal unified identity service model and the fragmented, high-cost reality. The organization has multiple identity platforms, unclear ownership, manual L3 operations, and technical constraints (MIM sunset 2029).

**Transformation Scope**: Analyze root causes across organizational structure, technical architecture, and operational processes. Propose coherent 3-year transformation strategy to consolidate identity services, establish clear EUSP ownership, enable self-service, and reduce operational costs by 40%+.

**Lead Agent**: Transformation Strategist (with support from Identity Architect, IT Director, DevOps Practice Lead, HR Advisor)

---

## Part 1: The Gap Analysis

### Ideal GISC Operating Model

**What GISC Should Be**:
- **Single unified platform** providing SSO for all Sony workforce
- **Automated account lifecycle** (provisioning, deprovisioning, updates) across all account types
- **Self-service capabilities** for 80%+ of user-initiated operations
- **Clear accountability**: EUSP as unified global service owner
- **Secure by design**: Zero Trust, passwordless, MFA, conditional access policies
- **Scalable and efficient**: Minimal manual L3 partner operations required
- **Consolidated vendors**: Single or federated governance model

### Current Reality: The Fragmented System

**What GISC Actually Is**:

**Platform Fragmentation**:
- Microsoft Active Directory (on-premises, legacy, being retired)
- Azure Entra ID (cloud, modern, but incomplete)
- JPIDM (Japan-specific system: MIM + custom .NET subsystem, managing 60K users)
- Passport (AM/EU system, vendor-managed, some user/contingent worker functions)
- Regional variations (AP managed via APIDM or direct AD)

**Ownership Fragmentation**:
- **AM/EU**: Passport team (non-EUSP) owns regular accounts and contingent workers
- **JP**: EUSP manages via JPIDM, but custom subsystem is fragile
- **AP**: No clear ownership—accounts created via APIDM or on-premises AD
- **Overall**: EUSP visibility limited, responsibility split across multiple teams

**Account Type Fragmentation**:
- Regular employees: Owned by different teams per region
- Non-human/AI agents: No centralized lifecycle, fragmented creation channels
- Contingent workers: Managed by EINS (JP) and Passport (AM/EU), outside EUSP
- Guests: No centralized lifecycle management, any user can invite independently

**Capability Gaps**:
- **No unified self-service** (regional inconsistency, service requests required in many cases)
- **No account lifecycle automation** (manual process for provisioning/deprovisioning)
- **No identity governance** (no access reviews, no attestation, no automated decisions)
- **No unified authentication** (legacy password-based, MFA fragmented across systems)
- **No app owner self-service** (everything requires L3 intervention)
- **No unified entitlement management** (group management requires service requests by region)

**Technical Debt**:
- **MIM Sunset**: Microsoft retiring MIM by 2029—JPIDM's core engine has no replacement yet
- **Custom JPIDM Subsystem**: Non-standard .NET solution, limited FTE expertise, fragile
- **Limited DevOps**: JPIDM lacks version control, automated testing, CI/CD pipelines
- **Ad-hoc Integrations**: Multiple custom integrations, no standardized patterns
- **Data Quality**: No consistent upstream data schema, no data quality controls

### Evidence of Dysfunction: Ticket Analysis as Symptom

**7,784 partner L3 tickets analyzed (Aug 2025 - Feb 2026)**:
- **MFA & Password Reset**: 1,167 tickets (15.0%) → ~1,500 hours/year consumed by partner L3
- **Access Control & Group Management**: 607 tickets (7.8%) → ~900 hours/year
- **Account Lifecycle**: 318 tickets (4.1%) → ~540 hours/year
- **App Management**: 615 tickets (7.9%) → ~750 hours/year
- **Total**: 3,700+ hours/year of manual L3 operations that could be automated or eliminated

**What This Reveals**:
- Not broken individual operations, but systemic inability to automate
- Root causes: Scattered platforms, no unified capability, manual processes by design
- Cost driver: High partner L3 dependency for routine operations
- Opportunity: Strategic automation + self-service could eliminate 3,700+ hours/year

---

## Part 2: Root Cause Analysis

### Why Is It Fragmented?

**Historical Reasons**:
1. **Regional autonomy**: Different regions built their own solutions (predate current structure)
2. **Vendor relationships**: Passport manages AM/EU user lifecycle (contractual, long-standing)
3. **Technology gaps**: Legacy AD couldn't do what modern systems can (MFA, passwordless, governance)
4. **Organizational misalignment**: IT organization structure doesn't match service structure
5. **MIM limitations**: Custom JPIDM subsystem built to compensate for MIM gaps

**Organizational Misalignment**:
- No single "GISC service owner" with authority across regions
- Decisions require coordination across EUSP, Passport, EINS, regional teams
- Clear ownership of regular accounts unclear (Passport manages, EUSP doesn't see)
- No governance board for identity decisions across organization

### Core Problems (Not Just Symptoms)

**Problem 1: Distributed Ownership Blocks Consolidation**
- Can't unify account creation because different teams own different regions/types
- Can't standardize processes because each team has local requirements
- Can't invest in shared capability because funding approval scattered

**Problem 2: Legacy System Forces Manual Workarounds**
- MIM can't automate identity governance → custom JPIDM subsystem + manual reviews
- MIM can't support passwordless → custom MFA management + reset tickets
- MIM can't manage lifecycle → service requests required for provisioning

**Problem 3: Technology Misalignment with Business**
- Multiple platforms doing overlapping functions (AD, Entra, JPIDM all manage users)
- Organization structure doesn't match technology responsibilities
- No clear signal about "forward direction"—teams playing not-to-lose vs. playing-to-win

**Problem 4: Technical Debt Reinforces Status Quo**
- JPIDM custom subsystem fragile—expensive to change, risky to fix
- No DevOps practices—can't iterate, automate, or improve easily
- MIM sunset 2029 approaching but no clear replacement strategy agreed

---

## Part 3: Strategic Transformation Vision

### Transformation Objective

**By 2028** (1 year before MIM sunset):
- **Unified Global Identity Platform**: Single or federated service owned by EUSP
- **Automated Operations**: 80%+ of identity operations automated (account lifecycle, access management, app onboarding)
- **User Empowerment**: 60%+ user self-service adoption (password reset, MFA management, access requests)
- **Organizational Alignment**: Clear EUSP ownership across all regions and account types
- **Reduced Costs**: Partner L3 team manual workload reduced by 40%+ (→ 2,200 hours/year freed up)
- **Modern Architecture**: No MIM, no legacy ad-hoc systems, modern cloud-native identity platform

### What Must Change

**Organizational Changes Required**:
1. **Consolidate Ownership**: EUSP assumes authority for all identity operations globally
2. **Reorganize by Service**: Teams organized around GISC service, not region
3. **Establish Governance**: Identity governance board for cross-regional decisions
4. **Define Clear Accountability**: Who decides what? Who implements? Who supports?

**Technical Changes Required**:
1. **Choose Modern Platform**: SailPoint (enterprise IGA), Okta (modern cloud), or custom (if justified)
2. **Standardize on Entra ID**: Single modern cloud platform + regional federations as needed
3. **Implement Identity Governance**: Automated access reviews, certification, lifecycle orchestration
4. **Build Automation**: Workflow orchestration for provisioning, deprovisioning, role changes
5. **Achieve Passwordless-First**: Windows Hello, FIDO2, eliminate password resets

**Process Changes Required**:
1. **Self-Service Workflows**: Portal for password reset, MFA management, access requests, app onboarding
2. **Automated Provisioning**: New user → automatically create accounts, assign groups, provision to apps
3. **Automated Deprovisioning**: Termination → automatically revoke access, remove from groups
4. **Access Reviews**: Automated periodic reviews → certification → enforcement
5. **API-Driven**: App onboarding via self-service, no L3 involvement for standard apps

---

## Part 4: Three-Year Transformation Roadmap

### Phase 1: Stabilize & Establish Ownership (Months 1-6)

**Objectives**:
- Establish EUSP as unified global identity service owner
- Perform deep root cause analysis
- Win quick wins to build momentum
- Build DevOps capability for JPIDM

**Key Initiatives**:
1. **Organizational Restructuring**
   - Define EUSP identity service ownership across all regions
   - Move contingent worker and guest management to EUSP oversight
   - Establish identity governance board

2. **Quick Win: Self-Service Password Reset**
   - Implement Entra ID SSPR (Azure feature) + custom portal wrapper
   - Target: 50%+ adoption in 3 months
   - Impact: Eliminate 500+ MFA/password reset tickets/year

3. **JPIDM Modernization Kickstart**
   - Establish DevOps practices (version control, CI/CD, automated testing)
   - Document custom .NET subsystem architecture
   - Begin MIM replacement evaluation

4. **Deep Gap Analysis**
   - Map current state: All platforms, all teams, all processes
   - Root cause analysis: Why is ownership fragmented?
   - Modernization pathway: What's the ideal state?

**Expected Outcomes**:
- Clear ownership model established
- 500+ annual L3 tickets eliminated
- JPIDM operational stability improved
- Executive alignment on transformation vision

### Phase 2: Consolidate & Automate (Months 6-18)

**Objectives**:
- Implement unified identity governance platform (IGA)
- Consolidate JPIDM onto modern architecture
- Achieve significant automation of identity operations
- Begin regional standardization

**Key Initiatives**:
1. **Identity Governance Platform Implementation**
   - Evaluation: SailPoint (enterprise), Okta (modern), or power Platform/custom
   - Implementation: Access reviews, certifications, life cycle orchestration
   - Timeline: 6-9 months
   - Impact: Eliminate 300+ access management tickets/year

2. **JPIDM Replacement Planning**
   - Design modern architecture (cloud-native, API-first)
   - Parallel run strategy (old + new in parallel)
   - Data migration planning
   - Timeline: Begin design, pilot in 12-18 months

3. **Account Lifecycle Automation**
   - Build provisioning orchestration (new user → accounts + groups + apps)
   - Build deprovisioning orchestration (termination → revoke access)
   - Integrate with Entra ID, regional platforms
   - Target: 70%+ of account lifecycle automated

4. **Regional Standardization**
   - Consolidate regional platform variations
   - Establish unified account types (regular, contingent, guest, non-human)
   - Unified master data (user profiles, managers, organizational data)

**Expected Outcomes**:
- IGA platform operational
- 50%+ user adoption of self-service capabilities
- 40%+ of account lifecycle automated
- JPIDM modernization design complete
- 600+ annual L3 tickets eliminated

### Phase 3: Modernize & Transform (Months 18-36)

**Objectives**:
- Complete MIM sunset strategy (replace with modern platform)
- Achieve passwordless-first authentication
- Mature automation to 80%+
- Establish modern operating model

**Key Initiatives**:
1. **MIM Replacement Execution**
   - Deploy JPIDM replacement (modern platform)
   - Migrate 60K users from MIM to new architecture
   - Decomission MIM with confidence
   - Timeline: 12-18 months (overlapping with Phase 2)

2. **Passwordless-First Authentication**
   - Windows Hello for Business deployment (Windows devices)
   - FIDO2 keys and Passkeys (mobile + web)
   - Eliminate password resets entirely (0 tickets)
   - Phased rollout by region/department
   - Target: 80%+ workforce on passwordless by month 36

3. **Full Automation Maturity**
   - App onboarding: Developers self-register apps, automatic configuration
   - Access requests: No L3 approval needed for standard paths
   - Access reviews: Fully automated with AI-driven anomaly detection
   - Target: 80%+ of operations require no L3 intervention

4. **Establish Modern Operating Model**
   - L3 team redeployed to strategic work (architecture, platform improvement)
   - Self-service portal primary interface for users
   - AI-powered intelligent assistant for complex scenarios
   - Partner operations significantly streamlined

**Expected Outcomes**:
- MIM sunset complete (2029 compliance)
- Passwordless-first deployment mature
- 80%+ automation of identity operations
- 1,500+ annual L3 tickets eliminated (40% reduction achieved)
- Partner L3 team redeployed to higher-value work
- GISC operating as unified platform globally

---

## Part 5: Organizational & Resource Implications

### Organizational Changes Needed

**EUSP Identity Service Team Structure** (Target State):
- **Service Owner** (Principal): Overall GISC ownership, strategy, partnerships
- **Platform Architect**: Modern identity platform design, cloud architecture
- **Engineering Team** (2-3 FTE, 5-6 AI agents):
  - Core platform development and maintenance
  - Integration and workflow orchestration
  - Automation and AI-assisted operations
- **Regional Coordinators**: Ensure regional requirements understood, standardization enforced
- **Operations & Support**: Self-service portal, user support, incident response

**Ownership Transitions** (From Current):
- **Regular User Accounts**: Move from Passport → EUSP (AM/EU transition)
- **Contingent Workers**: Move from EINS → EUSP (JP transition, other regions already EUSP)
- **Guests**: Centralized management under EUSP (currently decentralized)
- **JPIDM**: Establish clear EUSP ownership with DevOps practices

### Resource Requirements

**Internal EUSP Team**:
- 1 Principal Architect (strategy, partnerships, vendors, executive alignment)
- 1 Mid-Career Engineer (platform development, integration, operations)
- 5-6 AI agents (GitHub Copilot, Claude Code) for development acceleration
- Regional coordinators (existing headcount redeployed)

**External Resources**:
- Vendor implementation partner (if buying SailPoint, Okta, or similar): 6-12 months
- Security consulting (if building custom identity governance): 3-6 months
- Change management support for organizational transitions

**Budget Estimate** (3-Year Program):
- Year 1: $1.2M (platform licenses, implementation, development, organizational change)
- Year 2: $600K (continued implementation, vendor support, development)
- Year 3: $400K (completion, optimization)
- **Total 3-Year**: $2.2M
- **Annual Recurring Year 4+**: $400-500K (platform licenses, support, infrastructure)

**ROI Analysis**:
- Current annual L3 cost (partner operations): ~$3M+ (estimated)
- Annual savings from 40% workload reduction: $1.2M+
- Payback period: 2-3 years
- Breakeven: Year 2 (cumulative investment recovered)

---

## Part 6: Critical Success Factors & Risks

### Success Factors

1. **Executive Alignment**: Leadership committed to unified ownership, not "my team's solution"
2. **EUSP Empowerment**: Authority and budget to execute globally (not asking for permission region-by-region)
3. **Vendor Partnership**: Choose platform partner willing to support multi-year modernization
4. **Team Stability**: Dedicated team with low turnover (not borrowed capacity)
5. **Patience with Phases**: Accept 3-year timeline, don't try to rush
6. **Measurement**: Track both metrics (automation %) and organizational metrics (ownership clarity)

### Key Risks & Mitigation

| Risk | Impact | Mitigation |
|------|--------|-----------|
| **MIM Sunset Deadline (2029)** | Must have replacement ready | Start replacement design in Phase 1, pilot Phase 2, production Phase 3 |
| **Regional Resistance** | Regions fight consolidation | Early engagement, understand local requirements, federated model with local customization |
| **Vendor Lock-in** | Choosing wrong platform costly | Evaluate 2-3 options, require API openness and standards |
| **Team Turnover** | Key personnel leave mid-program | Build documentation, use AI agents for codification, establish center of excellence |
| **Scope Creep** | Transformation expands beyond intended | Strict governance board, clear in/out of scope, phase discipline |
| **Vendor Delays** | Implementation takes longer than expected | Contract penalties, parallel in-house capability development, realistic timelines |

---

## Part 7: Questions for Strategic Discussion

**What the Transformation Strategist Should Address**:

1. **Organizational Structure**: What's the optimal EUSP structure for unified global identity ownership? How do we transition from current state?

2. **Ownership Consolidation**: How do we assume ownership of currently vendor-managed functions (Passport, EINS) in a way that works politically and operationally?

3. **Technology Consolidation**: Should we target single monolithic platform (Okta) or federated approach (Entra ID + regional consistency)? What's the financial and operational trade-off?

4. **JPIDM Future State**: Replace entire JPIDM with modern platform, or modernize custom subsystem and keep MIM as long as possible? Cost-benefit of each?

5. **Build vs. Buy for IGA**: SailPoint (proven, expensive), Okta (modern, scalable), or custom/open-source (cost-effective but maintenance burden)? Given our team capacity?

6. **Sequencing**: What must change organizationally before we can change technologically? Can org changes and tech changes happen in parallel or must one precede?

7. **Quick Wins**: What small wins in Phase 1 (MFA reset, single self-service portal) build momentum without blocking larger transformation?

8. **Governance**: How do we prevent re-fragmentation once consolidated? What governance model ensures unified decisions?

---

## Supporting Context

**See Also**:
- GISC Global Identity Service Service Description (for detailed capability inventory)
- Ticket Analysis (for evidence of operational gaps revealed through L3 workload)
- EUSP Engineering Team Structure Doc (for current capacity and capabilities)

---

*Ready for Transformation Strategist-led discussion. Initiate with: "Transformation Strategist, please analyze this GISC gap and develop a 3-year transformation roadmap addressing organizational structure, technical consolidation, and operational modernization."*
