# JPIDM Platform Replacement Evaluation

> **Product Specialist - Identity Analysis**  
> Date: 2026-03-26  
> Scope: Comprehensive evaluation of JPIDM replacement platforms  
> Baseline: ¥100M/year operational cost (Avanade), ~67K users, Japan region

> **Licensing context**: Organization currently owns **Microsoft 365 E3 + Entra ID P2 (add-on)**. Entra ID P2 cost is ¥0 incremental for options that rely on it. The **Entra ID Governance add-on** (Lifecycle Workflows, $7/user/month) is a separate purchase and is priced at full cost in Option 1.1.

---

## Executive Summary

JPIDM requires replacement driven by:
- MIM 2029 end-of-support
- Legacy technology stack (ASP.NET MVC 4 VB.NET)
- ¥100M/year operational cost with vendor dependency
- Need for API-first, AI-agent-ready architecture

**Key Finding**: No single commercial IGA platform provides drop-in feature parity for JPIDM's self-service capabilities (especially group management, shared mailbox management, and hybrid on-premises AD provisioning). All options require some level of customization or supplementary portal development.

**Recommendation Tiers**:
1. **Best Fit (TCO)**: Custom build (.NET 8/9 + Graph API) — leverages already-owned P2, lowest cost, full feature parity
2. **Governance-First Option**: Entra ID Governance + Custom Portal — native Lifecycle Workflows, but high licensing cost at 67K scale
3. **Enterprise Alternative**: SailPoint ISC or Saviynt — strongest governance/compliance, highest cost
4. **Not Recommended**: One Identity Manager, Omada (poor hybrid AD fit), Midpoint (maturity risk)

---

## 1. Product Profiles

### 1.1 Microsoft Entra ID Governance + Custom Portal

**Architecture**: Entra ID Governance add-on (Lifecycle Workflows) + Entra ID P2 (Entitlement Management, Access Packages, PIM) + custom React/Power Apps portal + Microsoft Graph API + on-premises provisioning agents

**Strengths**:
- Native Lifecycle Workflows (automated joiner/mover/leaver without custom Azure Functions code)
- Native Entra ID + AD hybrid provisioning (Microsoft Entra Connect Provisioning Agent)
- Microsoft Graph API comprehensive coverage (groups, mailboxes, users)
- Entra ID P2 base already owned (E3 + P2 add-on) — incremental cost is Governance add-on only
- Power Platform integration for rapid portal development
- Azure DevOps/GitHub Actions CI/CD native support
- AI agent readiness (Graph API, MCP compatibility)
- No third-party vendor lock-in for core IAM

**Weaknesses**:
- Requires custom portal development (React/Next.js or Power Apps)
- Approval workflows require Power Automate or custom implementation
- Group management UI not identical to JPIDM (requires custom UX)
- External group expiration logic requires custom implementation
- No out-of-box self-service shared mailbox management
- Custom portal still required (Governance does not include group/mailbox self-service UI)
- High licensing cost: Governance add-on ($7/user/month) = ¥815M/year at 67,000 users

**Hybrid AD Provisioning**: ✅ Excellent (Entra Connect Provisioning Agent, native AD sync)

**Self-Service Portal**: ⚠️ Custom build required (low-code Power Apps or React/Next.js)

**API-First Readiness**: ✅ Excellent (Microsoft Graph API, SCIM endpoints, REST APIs)

**DevOps Operability**: ✅ Excellent (ARM templates, Bicep, Azure DevOps, GitHub Actions)

**Licensing Cost Estimate**:
- Entra ID P2 base: ¥0 incremental (already owned as M365 E3 + P2 add-on)
- Entra ID Governance add-on: $7/user/month × 67,000 users × 12 = ¥815M/year
- Power Apps per-user plan (if chosen): ~¥3,000/user/year × 1,000 active users = ¥3M/year
- Custom portal development: ¥30-50M one-time (6-12 months, 3-5 developers)
- Azure infrastructure (App Service, SQL, Functions): ¥5-10M/year
- Annual operational cost: ¥20-30M/year (in-house DevOps team)
- **Total Year 1**: ¥870–905M
- **Total Year 2+**: ¥843–858M/year

**Migration Approach Compatibility**: ✅ Excellent (feature-by-feature rollout supported)

**Risk Profile**:
- Medium implementation complexity (custom portal dev required)
- Low vendor lock-in (Microsoft ecosystem, but Graph API is standard)
- High in-house control (code ownership, DevOps operated)

---

### 1.2 SailPoint Identity Security Cloud (ISC) / IdentityNow

**Architecture**: Cloud-native IGA platform with Virtual Appliance for on-premises AD integration

**Strengths**:
- Enterprise-grade identity governance (access certification, risk scoring)
- Strong approval workflow engine
- Broad application connector ecosystem
- Role-based access control (RBAC) modeling
- Compliance and audit reporting
- REST APIs for extensibility
- Cloud-native SaaS (no infrastructure management)

**Weaknesses**:
- High cost (typically $30-50 per managed identity/year)
- Limited self-service UI customization (portal is vendor-controlled)
- Hybrid AD provisioning via Virtual Appliance (additional complexity)
- Group management not as flexible as JPIDM's native UI
- Shared mailbox management requires custom connector or workflows
- External group expiration logic not native (requires custom source rule)
- Vendor lock-in risk (proprietary APIs, data model)
- Implementation timeline: 6-12 months minimum

**Hybrid AD Provisioning**: ⚠️ Fair (Virtual Appliance required, not native)

**Self-Service Portal**: ⚠️ Fair (SailPoint portal is limited customization, may need supplementary UI)

**API-First Readiness**: ✅ Good (REST APIs available, but proprietary data model)

**DevOps Operability**: ⚠️ Fair (SaaS appliance, limited CI/CD for configuration, manual deployment patterns)

**Licensing Cost Estimate**:
- IdentityNow: $40/identity/year × 67,000 identities = $2.68M/year (~¥390M/year at ¥145/$)
- Professional services (implementation): $500K-1M (~¥75-145M one-time)
- Virtual Appliance infrastructure: ~¥5M/year (on-premises server, maintenance)
- Annual operational cost: ¥50M/year (vendor-dependent operations)
- **Total Year 1**: ¥520-630M
- **Total Year 2+**: ¥445M/year

**Migration Approach Compatibility**: ⚠️ Fair (platform prefers big-bang approach, feature-by-feature is complex)

**Risk Profile**:
- High cost relative to ¥100M baseline
- High vendor lock-in (proprietary platform)
- Medium-high implementation complexity
- Low in-house operability (vendor-dependent)

---

### 1.3 Saviynt Enterprise Identity Cloud

**Architecture**: Multi-tenant SaaS IGA platform with connector framework for on-premises AD

**Strengths**:
- Cloud identity governance (access reviews, policy enforcement)
- Role mining and analytics
- REST API framework
- Lower cost than SailPoint (typically $25-35/ identity/year)
- Risk-based access control
- Compliance reporting and audit trails
- Application onboarding framework

**Weaknesses**:
- UI/UX dated compared to modern standards
- Hybrid AD provisioning via connectors (not native)
- Limited self-service portal flexibility (vendor-controlled UI)
- Group lifecycle management requires custom connectors
- Shared mailbox management not native
- External group expiration logic requires custom rules
- Smaller ecosystem than SailPoint/Microsoft
- Implementation complexity medium-high (6-9 months)

**Hybrid AD Provisioning**: ⚠️ Fair (connector-based, not native agent)

**Self-Service Portal**: ⚠️ Fair (Saviynt portal limited, may need supplementary UI)

**API-First Readiness**: ✅ Good (REST APIs, but learning curve)

**DevOps Operability**: ⚠️ Fair (SaaS model, limited CI/CD, configuration-driven)

**Licensing Cost Estimate**:
- Saviynt EIC: $30/identity/year × 67,000 = $2.01M/year (~¥290M/year)
- Professional services (implementation): $400K-750K (~¥60-110M one-time)
- Connector infrastructure: ~¥3M/year
- Annual operational cost: ¥40M/year
- **Total Year 1**: ¥393-443M
- **Total Year 2+**: ¥333M/year

**Migration Approach Compatibility**: ⚠️ Fair (feature-by-feature migration requires careful connector planning)

**Risk Profile**:
- High cost relative to baseline
- Medium-high vendor lock-in
- Medium implementation complexity
- Low-medium in-house operability

---

### 1.4 Omada Identity

**Architecture**: On-premises or private cloud IGA platform (Windows Server/.NET-based)

**Strengths**:
- Identity governance focus (access certification, compliance)
- European privacy/GDPR alignment
- Role-based access modeling
- Approval workflow engine
- On-premises deployment option (data sovereignty)

**Weaknesses**:
- On-premises deployment model (infrastructure overhead)
- Limited cloud-native capabilities
- Hybrid AD integration requires custom connectors
- Self-service portal dated UX
- Smaller North American / Asia-Pacific market presence
- Limited API maturity (REST APIs emerging, not API-first)
- Group management requires custom development
- Shared mailbox management not native
- High implementation complexity (9-12 months)

**Hybrid AD Provisioning**: ❌ Poor (not designed for hybrid cloud scenarios)

**Self-Service Portal**: ❌ Poor (dated UI, limited customization)

**API-First Readiness**: ⚠️ Fair (REST APIs improving, but not API-first architecture)

**DevOps Operability**: ❌ Poor (on-premises appliance model, not DevOps-friendly)

**Licensing Cost Estimate**:
- Omada Identity: ~$35-45/identity/year × 67,000 = ~¥340-420M/year
- Infrastructure (on-premises servers): ¥15-25M/year
- Professional services: ¥80-120M one-time
- Annual operational cost: ¥60M/year
- **Total Year 1**: ¥495-625M
- **Total Year 2+**: ¥415-505M/year

**Migration Approach Compatibility**: ❌ Poor (platform prefers full-scope implementation)

**Risk Profile**:
- Very high cost
- Poor fit for hybrid cloud + modern DevOps
- Not recommended for JPIDM replacement

---

### 1.5 One Identity Manager

**Architecture**: On-premises enterprise IAM platform (SQL Server backend, .NET/PowerShell extensibility)

**Strengths**:
- Comprehensive identity management (AD, Entra ID, HR systems)
- Strong on-premises AD provisioning
- Role-based access control
- Approval workflow engine
- PowerShell extensibility
- SQL Server backend (familiar technology)
- Certificate-based authentication support

**Weaknesses**:
- Legacy on-premises architecture (not cloud-native)
- Appliance-style deployment (limited DevOps flexibility)
- High infrastructure overhead (Windows Servers, SQL Server cluster)
- Complex implementation (12-18 months typical)
- Limited API-first capabilities (SOAP/older REST APIs)
- Self-service portal dated UX
- High cost (perpetual license + maintenance + infrastructure)
- Quest acquisition uncertainty (Oracle ownership creates roadmap risk)
- Group lifecycle management requires custom modules
- Shared mailbox management requires custom development

**Hybrid AD Provisioning**: ⚠️ Fair (strong on-prem AD, weaker Entra ID integration)

**Self-Service Portal**: ❌ Poor (dated UI, SharePoint-based portal legacy)

**API-First Readiness**: ❌ Poor (legacy SOAP/REST APIs, not modern REST/GraphQL)

**DevOps Operability**: ❌ Poor (appliance model, not CI/CD friendly)

**Licensing Cost Estimate**:
- One Identity Manager: Perpetual license ~$75-100/identity + 20% annual maintenance
- Initial license: $5M-6.5M (~¥725M-950M one-time)
- Annual maintenance: 20% × license = ¥145-190M/year
- Infrastructure (SQL cluster, app servers): ¥20-30M/year
- Professional services (implementation): ¥100-150M one-time
- Annual operational cost: ¥50M/year
- **Total Year 1**: ¥995M-1.32B
- **Total Year 2+**: ¥215-270M/year

**Migration Approach Compatibility**: ❌ Poor (platform requires comprehensive implementation)

**Risk Profile**:
- Extremely high cost
- Oracle ownership creates strategic uncertainty
- Poor fit for cloud-first, API-first, DevOps-operated architecture
- **Not recommended for JPIDM replacement**

---

### 1.6 Microsoft-Native Custom Approach (Graph API + Custom Portal)

**Architecture**: Pure custom build using Microsoft Graph SDK, React/Next.js portal, Azure Functions/App Service, Entra ID Provisioning Agents

**Strengths**:
- Full control over features and UX
- Microsoft Graph API comprehensive (users, groups, mailboxes, AD write-back)
- Modern tech stack (.NET 8/9, React, TypeScript)
- Native Entra ID + AD hybrid provisioning (Provisioning Agents)
- CI/CD native (GitHub Actions, Azure DevOps)
- API-first by design (REST APIs, GraphQL optional, MCP-compatible)
- In-house DevOps team owned and operated
- Exact JPIDM feature parity achievable
- Incremental feature-by-feature delivery
- No vendor lock-in (open source tech stack)

**Weaknesses**:
- Incremental BAU delivery (feature-by-feature; full parity takes 2–3 years at Pattern 1 team size)
- Requires sustained engineering investment
- Approval workflow engine must be built (or use Durable Functions)
- Audit trail and compliance reporting must be custom-built
- Testing and quality assurance overhead
- Knowledge transfer risk (team must maintain custom codebase)

**Hybrid AD Provisioning**: ✅ Excellent (Entra Connect Provisioning Agent, native Graph API)

**Self-Service Portal**: ✅ Excellent (full custom UI, exact JPIDM UX achievable)

**API-First Readiness**: ✅ Excellent (fully custom REST API design, MCP-ready)

**DevOps Operability**: ✅ Excellent (infrastructure-as-code, CI/CD native, container-ready)

**Licensing Cost Estimate** (Pattern 1 — SISC BAU delivery, no upfront dev cost):
- Entra ID P2: ¥0 incremental (already owned as M365 E3 + P2 add-on)
- Azure infrastructure (App Service, Functions, SQL, Storage): ¥5–10M/year
- Year 1 team (FY26): 1 senior SISC + 1 junior SISC + 1/3 FTE architect: ¥17M/year
- Year 3 team (FY28): 2 senior SISC + 3 junior SISC + 1/3 FTE architect: ¥32M/year
- GitHub Enterprise + GitHub Copilot (team licenses): ¥2–3M/year
- **Total Year 1**: ¥24–30M
- **Total Year 3+**: ¥39–45M/year

> No one-time development cost — platform is built incrementally as BAU by the in-house SISC team.

**Migration Approach Compatibility**: ✅ Excellent (feature-by-feature delivery, progressive rollout)

**Risk Profile**:
- Medium-high development complexity
- Low vendor lock-in (Microsoft ecosystem, but Graph API standard)
- High in-house control
- Requires sustained engineering capability

---

### 1.7 Midpoint (Evolveum) – Open Source IAM

**Architecture**: Open-source identity management platform (Java-based, extensible via Groovy scripts)

**Strengths**:
- Open source (Apache License 2.0, no licensing cost)
- Identity governance features (roles, policies, access certification)
- Connector framework (LDAP, SCIM, REST)
- Workflow engine (Activiti-based)
- On-premises or cloud deployment
- Community support and commercial support available (Evolveum)

**Weaknesses**:
- Smaller community and ecosystem than commercial platforms
- UI/UX dated (administrative UI, not modern end-user portal)
- Hybrid AD provisioning via connectors (not native agent)
- Self-service portal requires customization (or third-party portal)
- Limited out-of-box group lifecycle management
- Shared mailbox management requires custom connectors
- External group expiration logic requires custom workflows
- Java/Groovy expertise required (not .NET, not Python)
- Documentation gaps for advanced scenarios
- Implementation complexity medium-high (6-12 months)
- Limited Japan/Asia-Pacific support and consulting ecosystem

**Hybrid AD Provisioning**: ⚠️ Fair (LDAP connector, SCIM support, but not native)

**Self-Service Portal**: ❌ Poor (administrative UI only, end-user portal custom build required)

**API-First Readiness**: ⚠️ Fair (REST APIs available, but documentation limited)

**DevOps Operability**: ⚠️ Fair (Java deployment, container-ready, but CI/CD patterns immature)

**Licensing Cost Estimate**:
- Midpoint: ¥0 (open source)
- Evolveum commercial support (optional): ~$50K-100K/year (~¥7-15M/year)
- Infrastructure (VM or containers): ¥5-10M/year
- Implementation (custom connector dev, portal build): ¥50-80M one-time
- Annual operational cost: ¥25-35M/year (in-house + support)
- **Total Year 1**: ¥87-140M
- **Total Year 2+**: ¥37-60M/year

**Migration Approach Compatibility**: ⚠️ Fair (feature-by-feature possible, but connector development overhead)

**Risk Profile**:
- Medium-high implementation complexity
- Medium maturity risk (smaller community, limited Asia-Pacific presence)
- Low vendor lock-in (open source)
- Medium-high in-house operational expertise required (Java, Groovy)

---

### 1.8 Okta Workforce Identity Cloud

**Architecture**: Cloud-native identity platform (authentication, SSO, MFA, lifecycle management) with AD Agent for on-premises directory sync and Okta Workflows for process automation

**Strengths**:
- Industry-leading authentication, SSO, and MFA capabilities
- Comprehensive REST APIs (Okta Management API, Events API)
- Okta Workflows (low-code automation for identity lifecycle events)
- Broad SaaS app connector ecosystem (7,000+ integrations)
- Cloud-native SaaS (no infrastructure management)
- Strong Entra ID / Azure AD federation support
- Developer-friendly APIs (well-documented, SDKs available)
- AI/ML-based risk scoring (Okta ThreatInsight)

**Weaknesses**:
- Authentication-first platform — **not designed for on-premises AD object management**
- AD Agent provides directory sync (read/write for user attributes), but **does not manage AD security groups with expiration, computer accounts, room accounts, or shared mailboxes**
- No native shared mailbox management (Exchange Online operations not supported)
- External group expiration logic not native (requires custom Workflow or third-party)
- Self-service portal (End-User Dashboard) is limited — not designed for JPIDM-style self-service group/mailbox operations
- Limited customisation of end-user portal UI for Japan-specific workflows
- High licensing cost at 67,000-user scale (authentication tier pricing applied to all managed identities)
- Hybrid AD provisioning depth is shallow compared to Microsoft-native approach
- Computer account management not supported
- Resource/room account management not supported
- Implementation complexity for JPIDM feature parity is high (custom Workflows + portal development required)

**Hybrid AD Provisioning**: ⚠️ Fair (AD Agent syncs user attributes bidirectionally; does not manage group lifecycle, computer accounts, or Exchange objects)

**Self-Service Portal**: ❌ Poor for JPIDM parity (End-User Dashboard is authentication-focused; complex group/mailbox self-service requires custom portal build)

**API-First Readiness**: ✅ Excellent (Okta Management API is comprehensive and well-documented; industry-standard REST)

**DevOps Operability**: ✅ Good (Terraform provider available, API-driven configuration, GitHub Actions integration)

**Licensing Cost Estimate**:
- Okta Workforce Identity Cloud (Lifecycle Management + Workflows tier): $7/user/month × 67,000 users × 12 = ¥816M/year
- Okta Access Gateway (on-premises hybrid connectivity): ~¥10-15M/year (infrastructure + licensing)
- Professional services (Okta SI partner, 6–9 months): ¥75–115M one-time
- Custom portal development (group management, mailbox self-service): ¥30–50M one-time
- Ongoing operations: ¥40–50M/year (vendor-dependent support + internal team)
- **Total Year 1**: ¥971M–1.05B
- **Total Year 2+**: ¥866–881M/year

**Migration Approach Compatibility**: ⚠️ Fair (Okta Workflows supports incremental automation; however, AD object management gaps require significant custom development before any JPIDM feature cutover)

**Risk Profile**:
- High cost relative to ¥100M baseline
- Medium-high implementation complexity (authentication strengths do not translate to AD management depth)
- Low vendor lock-in for auth/SSO, but Okta-specific Workflows create operational dependency
- High feature gap for JPIDM's core AD self-service capabilities

---

## 2. Pricing Assumptions

> All costs are estimates based on publicly available pricing, analyst benchmarks, and industry norms (March 2026). Exact vendor quotes will differ. FX rate assumed: **¥145 per USD**.
> **Baseline for comparison: ¥100M/year** (current Avanade operational cost for JPIDM).

### Key Assumptions by Platform

#### Microsoft Entra ID Governance + Custom Portal
| Cost Item | Assumption | Estimate |
|-----------|-----------|----------|
| Entra ID P2 base licensing | Already owned (M365 E3 + P2 add-on) | ¥0 incremental |
| Entra ID Governance add-on (Lifecycle Workflows) | $7/user/month × 67,000 users × 12 months @ ¥145/$ | ¥815M/year |
| Power Apps (if low-code portal chosen) | ¥3,000/user/year × 1,000 active portal users | ¥3M/year |
| Custom portal development | 3–5 engineers × 6–12 months (SISC + FTE mix) | ¥30–50M one-time |
| Azure infrastructure (App Service, SQL, Functions) | Based on Japan region Azure pricing | ¥5–10M/year |
| In-house AI DevOps team operations | 2–5 FTE (Year 1 → Year 3) | ¥20–30M/year |
| **Total Year 1** | | **¥870–905M** |
| **Total Year 2+** | | **¥843–858M/year** |

#### SailPoint Identity Security Cloud (ISC) / IdentityNow
| Cost Item | Assumption | Estimate |
|-----------|-----------|---------|
| IdentityNow licensing | $40/identity/year × 67,000 managed identities | ¥390M/year |
| Implementation professional services | SailPoint SI partner, 6–12 months | ¥75–145M one-time |
| Virtual Appliance infrastructure | On-premises server for AD connectivity | ¥5M/year |
| Ongoing vendor-dependent operations | Vendor support + internal team | ¥50M/year |
| **Total Year 1** | | **¥520–630M** |
| **Total Year 2+** | | **¥445M/year** |

#### Saviynt Enterprise Identity Cloud
| Cost Item | Assumption | Estimate |
|-----------|-----------|---------|
| Saviynt EIC licensing | $30/identity/year × 67,000 identities | ¥290M/year |
| Implementation professional services | Saviynt SI partner, 6–9 months | ¥60–110M one-time |
| Connector infrastructure (AD, Exchange) | On-premises connector servers | ¥3M/year |
| Ongoing operations | Vendor support + internal team | ¥40M/year |
| **Total Year 1** | | **¥393–443M** |
| **Total Year 2+** | | **¥333M/year** |

#### Omada Identity
| Cost Item | Assumption | Estimate |
|-----------|-----------|---------|
| Omada licensing | $35–45/identity/year × 67,000 identities | ¥340–420M/year |
| Infrastructure (on-premises Windows servers) | Self-hosted deployment | ¥15–25M/year |
| Implementation professional services | 9–12 months, Omada SI partner | ¥80–120M one-time |
| Ongoing operations | Vendor-dependent | ¥60M/year |
| **Total Year 1** | | **¥495–625M** |
| **Total Year 2+** | | **¥415–505M/year** |

#### One Identity Manager
| Cost Item | Assumption | Estimate |
|-----------|-----------|---------|
| Perpetual license | $75–100/identity × 67,000 identities | ¥725–950M one-time |
| Annual maintenance (20% of license) | Ongoing | ¥145–190M/year |
| Infrastructure (SQL Server cluster, app servers) | On-premises self-hosted | ¥20–30M/year |
| Implementation professional services | 12–18 months | ¥100–150M one-time |
| Ongoing operations | Internal + vendor support | ¥50M/year |
| **Total Year 1** | | **¥995M–1.32B** |
| **Total Year 2+** | | **¥215–270M/year** |

#### Custom Build (.NET 8/9 + React + Microsoft Graph API) — Pattern 1 SISC + Japan Resourcing
| Cost Item | Assumption | Estimate |
|-----------|-----------|----------|
| Entra ID P2 licensing | Already owned (M365 E3 + P2 add-on) | ¥0 incremental |
| Azure infrastructure (App Service, SQL, Storage, Functions) | Japan region, production + staging | ¥5–10M/year |
| Year 1 team (FY26): 2 FTE Japan-based | 1 senior engineer (Japan) + 1 junior engineer (Japan) | ¥18–24M/year |
| Year 1 architect supervision | 1/3 FTE shared architect (Japan, oversight + design reviews) | ¥5–7M/year |
| Year 3 team (FY28): 5 FTE (1+1 Japan + 3 SISC India) | 1 senior (Japan) + 1 junior (Japan) + 1 senior (SISC) + 2 junior (SISC) | ¥36–48M/year |
| Year 3 architect supervision | 1/3 FTE shared architect (Japan, ongoing) | ¥5–7M/year |
| GitHub Enterprise + GitHub Copilot | Team licenses, scales 2→5 users | ¥2–3M/year |
| **Total Year 1** | No upfront dev cost — BAU incremental delivery | **¥30–44M** |
| **Total Year 3+** | Scaled team (Japan + SISC), steady-state operations | **¥48–68M/year** |

**Per-Developer Cost Breakdown**:
- **Japan-based senior engineer**: ¥9–12M/year
- **Japan-based junior engineer**: ¥4.5–6M/year
- **SISC (India) senior engineer**: ¥6–7M/year
- **SISC (India) junior engineer**: ¥3–4M/year
- **Shared architect (Japan, 1/3 charge)**: ¥5–7M/year

#### Midpoint (Evolveum) – Open Source
| Cost Item | Assumption | Estimate |
|-----------|-----------|---------|
| Midpoint license | Open source (Apache 2.0) | ¥0 |
| Evolveum commercial support (optional) | $50K–100K/year | ¥7–15M/year |
| Infrastructure (VMs or containers) | Self-hosted | ¥5–10M/year |
| Implementation (connector dev, portal build) | Java/Groovy specialists, 6–12 months | ¥50–80M one-time |
| Ongoing operations | In-house specialists | ¥25–35M/year |
| **Total Year 1** | | **¥87–140M** |
| **Total Year 2+** | | **¥37–60M/year** |

#### Okta Workforce Identity Cloud
| Cost Item | Assumption | Estimate |
|-----------|-----------|---------|
| Okta Workforce Identity Cloud (LCM + Workflows tier) | $7/user/month × 67,000 users × 12 months @ ¥145/$ | ¥816M/year |
| Okta Access Gateway (on-premises hybrid connectivity) | Infrastructure + licensing for JP region | ¥10–15M/year |
| Custom portal development (group mgmt, mailbox self-service) | 3–5 engineers × 6–9 months | ¥30–50M one-time |
| Implementation professional services (Okta SI partner) | 6–9 months | ¥75–115M one-time |
| Ongoing operations | Vendor support + internal team | ¥40–50M/year |
| **Total Year 1** | | **¥971M–1.05B** |
| **Total Year 2+** | | **¥866–881M/year** |

---

### TCO Summary (3-Year)

| Platform | Year 1 | Year 2+ (per year) | 3-Year TCO | vs. ¥100M Baseline |
|----------|--------|-------------------|-----------|-------------------|
| **Custom Build (.NET/React/Graph)** | ¥30–44M | ¥48–68M/year | **¥126–180M** | **-40% to -58%** ✅ |
| **Midpoint (Open Source)** | ¥87–140M | ¥37–60M/year | **¥161–260M** | **-13% to -46%** ✅ |
| Saviynt EIC | ¥393–443M | ¥333M/year | **¥1.06–1.11B** | **+253–270%** ❌ |
| SailPoint ISC | ¥520–630M | ¥445M/year | **¥1.4B–1.52B** | **+370–420%** ❌ |
| Omada Identity | ¥495–625M | ¥415–505M/year | **¥1.33B–1.64B** | **+343–447%** ❌ |
| **Entra ID Gov + Custom Portal** | ¥870–905M | ¥843–858M/year | **¥2.55B–2.62B** | **+750–773%** ❌ |
| **Okta Workforce Identity Cloud** | ¥971M–1.05B | ¥866–881M/year | **¥2.70B–2.81B** | **+800–837%** ❌ |
| One Identity Manager | ¥995M–1.32B | ¥215–270M/year | **¥1.4B–1.86B** | **+367–520%** ❌ |

> **Note:** 3-Year TCO = Year 1 + (Year 2+ × 2). Baseline comparison: ¥100M/year × 3 = ¥300M.
> Custom Build pricing uses **Pattern 1 Japan+SISC resourcing**: Year 1 (2 FTE Japan), Year 3 (1 senior Japan + 1 junior Japan + 1 senior SISC + 2 junior SISC). No one-time development cost (BAU delivery). Entra ID Governance is priced at full $7/user/month Governance add-on cost; at 67,000 users the licensing alone (¥815M/year) makes it more expensive than all commercial IGA platforms.

---

## 3. Feature Comparison Matrix

| Feature | JPIDM (Baseline) | Entra ID Gov + Custom Portal | SailPoint ISC | Saviynt EIC | Omada | One Identity | Custom Build | Midpoint | Okta |
|---------|------------------|------------------------------|---------------|-------------|-------|--------------|--------------|----------|------|
| **Hybrid AD Provisioning** | ✅ Native (ADSI) | ✅ Native (Agent) | ⚠️ Virtual Appliance | ⚠️ Connector | ❌ Weak | ⚠️ Fair | ✅ Native (Agent) | ⚠️ Connector | ⚠️ AD Agent (user attrs only) |
| **Entra ID Provisioning** | ❌ Not supported | ✅ Native (SCIM) | ✅ Connector | ✅ Connector | ⚠️ Weak | ⚠️ Fair | ✅ Native (Graph) | ⚠️ Connector | ✅ Native (SCIM) |
| **Group Management UI** | ✅ Full (add/remove, CSV, expiration) | ⚠️ Custom build | ⚠️ Limited | ⚠️ Limited | ❌ Custom dev | ❌ Custom dev | ✅ Full (custom) | ❌ Custom dev | ❌ Custom build required |
| **Shared Mailbox Management** | ✅ Full | ⚠️ Custom build | ❌ Custom connector | ❌ Custom connector | ❌ Custom dev | ❌ Custom dev | ✅ Full (custom) | ❌ Custom dev | ❌ Not supported |
| **Approval Workflows** | ✅ Request_LST engine | ⚠️ Power Automate / Custom | ✅ Native | ✅ Native | ✅ Native | ✅ Native | ⚠️ Custom build | ✅ Activiti engine | ✅ Okta Workflows (low-code) |
| **External Group Expiration** | ✅ Native | ⚠️ Custom logic | ⚠️ Custom rule | ⚠️ Custom rule | ❌ Custom dev | ❌ Custom dev | ✅ Full (custom) | ⚠️ Custom workflow | ❌ Not native |
| **Self-Service Password Reset** | ✅ MIM portal | ✅ Entra ID SSPR | ✅ Native | ✅ Native | ✅ Native | ✅ Native | ✅ Entra ID SSPR | ⚠️ Custom build | ✅ Native (Okta SSPR) |
| **Account Unlock** | ✅ MIM portal | ✅ Entra ID SSPR | ✅ Native | ✅ Native | ✅ Native | ✅ Native | ✅ Entra ID SSPR | ⚠️ Custom build | ✅ Native |
| **Multi-Language Support** | ✅ Japanese/English | ✅ Full (custom) | ⚠️ English primary | ⚠️ English primary | ⚠️ English primary | ⚠️ English primary | ✅ Full (custom) | ⚠️ Custom translations | ⚠️ English primary |
| **API-First Architecture** | ❌ No APIs | ✅ Graph API | ⚠️ REST APIs | ⚠️ REST APIs | ⚠️ Limited APIs | ❌ Legacy APIs | ✅ Full REST/GraphQL | ⚠️ REST APIs | ✅ Okta Management API |
| **DevOps CI/CD Friendly** | ❌ Legacy ASP.NET | ✅ Native (IaC) | ⚠️ SaaS config | ⚠️ SaaS config | ❌ Appliance | ❌ Appliance | ✅ Native (IaC) | ⚠️ Java deploy | ✅ Terraform + API-driven |
| **AI Agent Readiness (MCP)** | ❌ No APIs | ✅ Graph API ready | ⚠️ REST APIs | ⚠️ REST APIs | ❌ Limited | ❌ Legacy | ✅ Full custom MCP | ⚠️ REST APIs | ⚠️ REST APIs (no MCP native) |
| **In-House Operability** | ❌ Avanade-dependent | ✅ Full DevOps | ❌ Vendor-dependent | ❌ Vendor-dependent | ❌ Vendor-dependent | ❌ Vendor-dependent | ✅ Full DevOps | ⚠️ Java expertise | ❌ Vendor-dependent |
| **Implementation Timeline** | N/A (current state) | 6-12 months | 6-12 months | 6-9 months | 9-12 months | 12-18 months | 12-18 months | 6-12 months | 9-15 months |
| **Feature Parity Achievable** | 100% (baseline) | 95% (minor gaps) | 60% (portal gaps) | 55% (portal gaps) | 40% (weak hybrid) | 50% (dated UX) | 100% (full control) | 50% (portal gaps) | 40% (AD mgmt gaps) |

**Legend**:
- ✅ Native/Excellent fit
- ⚠️ Partial/Custom dev required
- ❌ Not supported/Poor fit

---

## 3. Total Cost of Ownership (TCO) Analysis

### 3-Year TCO Comparison (¥ Million)

| Platform | Year 1 | Year 2 | Year 3 | 3-Year Total | Avg/Year | vs. Baseline¹ |
|----------|--------|--------|--------|-------------|----------|---------------|
| **Current JPIDM (Avanade)** | ¥100 | ¥100 | ¥100 | **¥300** | ¥100 | Baseline |
| **Custom Build (Full)** | ¥37 | ¥48 | ¥58 | **¥143** | ¥48 | **-52%** |
| **Midpoint (Open Source)** | ¥114² | ¥49 | ¥49 | **¥212** | ¥71 | **-29%** |
| **Saviynt EIC** | ¥393 | ¥333 | ¥333 | **¥1,059** | ¥353 | **+253%** |
| **SailPoint ISC** | ¥575 | ¥445 | ¥445 | **¥1,465** | ¥488 | **+388%** |
| **Omada Identity** | ¥560 | ¥460 | ¥460 | **¥1,480** | ¥493 | **+393%** |
| **One Identity Manager** | ¥1,070 | ¥243 | ¥243 | **¥1,556** | ¥519 | **+419%** |
| **Okta Workforce Identity Cloud** | ¥1,010² | ¥874 | ¥874 | **¥2,758** | ¥919 | **+819%** |
| **Entra ID Gov + Custom Portal** | ¥890² | ¥851 | ¥851 | **¥2,592** | ¥864 | **+764%** |

¹ Baseline: ¥100M/year Avanade operational cost  
² Includes one-time implementation cost  

### Key TCO Insights

1. **Custom Build (P2 + Graph API)** offers strong TCO at ¥48M/year average (**-52%** vs. baseline) — leverages already-owned P2 with no additional licensing; costs reflect 1 senior + 1 junior Japan-based FTE (Year 1) scaling to mixed Japan/SISC team (Year 3)
2. **Entra ID Governance + Custom Portal** has the highest ongoing cost of all options at ¥864M/year average (**+764%**) due to the $7/user/month Governance add-on at 67,000-user scale
3. **Commercial IGA platforms** (SailPoint, Saviynt) cost **2.5-5x more** than current JPIDM operations
4. **Midpoint open source** is cost-competitive but has maturity/support risks in Asia-Pacific
5. **One Identity Manager** has highest Year 1 cost (perpetual license model), but Year 2+ is more competitive than Entra ID Governance

### Hidden Costs to Consider

| Cost Factor | Commercial IGA | Microsoft-Native | Custom Build | Open Source |
|-------------|----------------|------------------|--------------|-------------|
| Vendor lock-in mitigation | High (proprietary APIs) | Low (Graph API standard) | None | None |
| Training & upskilling | Medium-high (vendor-specific) | Low (Microsoft ecosystem) | Medium (.NET/React) | Medium-high (Java/Groovy) |
| Professional services dependency | High (ongoing vendor services) | Low (in-house capable) | None | Medium (Evolveum support) |
| Integration & customization overhead | High (connector dev) | Low (Graph API native) | Medium (build time) | Medium-high (connector dev) |
| Audit & compliance reporting | Low (native features) | Medium (custom reports) | Medium (custom reports) | Medium (custom reports) |

---

## 4. Migration Approach Compatibility

JPIDM replacement requirements specify **feature-by-feature BAU migration** (not big-bang). This evaluation assesses each platform's compatibility with gradual rollout.

### Migration Approach Assessment

| Platform | BAU Migration Support | Rollout Strategy | Coexistence Period | Risk Level |
|----------|----------------------|------------------|-------------------|------------|
| **Entra ID Gov + Custom Portal** | ✅ Excellent | Feature-by-feature via portal modules (Group Mgmt → Shared Mailbox → Workflows) | JPIDM + new portal coexist via feature flags | Low |
| **Custom Build** | ✅ Excellent | Feature-by-feature via API routes & UI components (progressive replacement) | Full coexistence (URL routing per feature) | Low |
| **Midpoint** | ⚠️ Fair | Connector-by-connector rollout (AD first → Exchange → workflows) | Dual provisioning overlap (JPIDM + Midpoint write same targets) | Medium |
| **SailPoint ISC** | ⚠️ Fair | Application-by-application onboarding (prefers full scope) | Virtual Appliance + JPIDM coexist, potential conflict | Medium-high |
| **Saviynt EIC** | ⚠️ Fair | Connector phased rollout (AD → Entra → apps) | Dual provisioning risk (conflict resolution required) | Medium-high |
| **Okta** | ⚠️ Fair | Authentication/SSPR migration first; AD object management requires custom development before cutover | Okta + JPIDM coexist during custom dev phase; AD management gap creates extended overlap | Medium-high |
| **Omada** | ❌ Poor | Platform prefers full implementation (governance-first approach) | Parallel systems create reconciliation complexity | High |
| **One Identity** | ❌ Poor | Comprehensive implementation (role design + provisioning rules) | Difficult coexistence (SQL Server conflicts, dual-write risk) | High |

### Recommended Migration Pattern (Microsoft-Native Approach)

**Phase 1: Foundation (Months 1-3)**
- Deploy Entra ID Provisioning Agents (AD write-back capability)
- Implement Microsoft Graph API integration layer
- Build authentication & authorization framework (Entra ID SSO)

**Phase 2: Self-Service Password Reset (Months 3-4)**
- Enable Entra ID SSPR (replace MIM password reset portal)
- Migrate security questions and phone/email verification
- Decommission MIM password reset

**Phase 3: Group Management Portal (Months 4-8)**
- Build group management UI (React/Next.js)
- Implement group creation, member add/remove, CSV import
- Add external group expiration logic
- Run parallel with JPIDM (URL-based routing)

**Phase 4: Shared Mailbox Management (Months 9-12)**
- Build shared mailbox UI and Graph API integration
- Enable mailbox creation, delegation management
- Migrate shared mailbox operations from JPIDM

**Phase 5: Approval Workflows (Months 13-16)**
- Implement workflow engine (Power Automate or custom)
- Migrate approval logic (group deletion, account requests)
- Enable notifications and escalation

**Phase 6: Final Cutover (Months 17-18)**
- Complete remaining JPIDM features (profile edit, company affiliation)
- Decommission JPIDM ASP.NET portal
- Migrate operational runbooks to DevOps team

---

## 5. Risk Analysis

### Platform Risk Dimensions

| Risk Category | Entra ID Gov + Custom | Custom Build | Commercial IGA | Open Source | Okta |
|---------------|----------------------|--------------|----------------|-------------|------|
| **Vendor Lock-In** | Low (Microsoft ecosystem, but Graph API standard) | None | High (proprietary APIs, data models) | None | Medium (Okta Workflows create operational dependency) |
| **Implementation Complexity** | Medium (custom portal dev) | Medium-high (full dev effort) | Medium-high (vendor implementation) | Medium-high (connector dev) | High (AD object management gaps require extensive custom dev) |
| **Cost Overrun** | Low (in-house control) | Medium (dev timeline risk) | High (vendor services, scope creep) | Medium (support gaps) | High (licensing + custom portal dev) |
| **Operational Dependency** | Low (in-house DevOps) | Low (in-house DevOps) | High (vendor-dependent) | Medium (commercial support needed) | High (vendor-dependent + custom dev) |
| **Feature Gap** | Low (Graph API comprehensive) | None (full custom control) | Medium-high (portal limitations) | Medium-high (portal + connector gaps) | High (no AD group expiration, no shared mailbox, no computer accounts) |
| **Technology Obsolescence** | Low (Microsoft roadmap) | Low (modern stack) | Medium (vendor roadmap risk) | Medium (Java ecosystem evolution) | Low (Okta roadmap stable) |
| **Skills Availability** | High (.NET, React, Azure) | High (.NET, React, Azure) | Low (vendor-specific training) | Medium (Java, Groovy niche) | Medium (Okta-specific Workflows expertise) |
| **Regulatory Compliance** | Medium (custom audit trails) | Medium (custom compliance reports) | Low (native compliance features) | Medium (custom audit trails) | Medium (audit features available, custom reports for JPIDM scope) |

### Critical Risks by Platform

#### Microsoft Entra ID Governance + Custom Portal
- **Risk**: Custom portal development timeline extends beyond 12 months
- **Mitigation**: Use Power Apps for rapid MVP, iterate to React if needed
- **Risk**: Approval workflow complexity underestimated (Power Automate limitations)
- **Mitigation**: Evaluate Durable Functions or third-party workflow engine (Temporal.io)

#### Custom Build (Full)
- **Risk**: Sustained engineering investment required (not one-time project)
- **Mitigation**: Budget annual feature enhancement (¥10-15M/year ongoing)
- **Risk**: Knowledge concentration (team turnover risk)
- **Mitigation**: Documentation, modular architecture, pair programming

#### SailPoint / Saviynt (Commercial IGA)
- **Risk**: Cost escalation (scope creep, professional services dependency)
- **Mitigation**: Fixed-price implementation contract, clear scope boundaries
- **Risk**: Vendor lock-in (difficult to migrate off platform)
- **Mitigation**: Contract exit clauses, data portability requirements

#### Midpoint (Open Source)
- **Risk**: Limited Asia-Pacific support and consulting ecosystem
- **Mitigation**: Evolveum commercial support contract, community engagement
- **Risk**: Implementation complexity (Java/Groovy expertise gap)
- **Mitigation**: Hire Java/IAM consultant, cross-train team

---

## 6. Recommendations

### Tier 1 Recommendation: **Custom Build (.NET 8/9 + React + Graph API)**

**Rationale**:
- **Best TCO**: ¥34M/year average (**66% savings** vs. ¥100M baseline) — Pattern 1 SISC BAU resourcing
- **No upfront development cost**: Incremental BAU delivery; Year 1 starts at ¥27M (2 FTE SISC team)
- **No incremental licensing**: Leverages already-owned M365 E3 + Entra ID P2 (¥0 incremental)
- **Full feature parity**: 100% JPIDM capabilities achievable
- **No vendor lock-in**: Open source tech stack, Graph API standard
- **AI-agent native**: Design REST APIs from ground-up (MCP-first)
- **DevOps excellence**: Infrastructure-as-code, container-native, CI/CD first-class
- **Migration-friendly**: Feature-by-feature delivery, progressive rollout
- **Strategic alignment**: Supports GISC vision (API-first, global scale)

**Implementation Approach**:
- Modern tech stack: **.NET 8 Minimal APIs**, **React 18**, **TypeScript**, **Tailwind CSS**
- Architecture: **Azure App Service** (portal), **Azure Functions** (lifecycle & workflows), **Azure SQL** (audit logs), **Entra ID Provisioning Agents** (AD sync)
- CI/CD: **GitHub Actions** (preferred) or **Azure DevOps Pipelines**
- API design: **REST + GraphQL** (if needed), **MCP server** for AI agents

**Recommended for**:
- Organizations with M365 E3 + Entra ID P2 and in-house engineering capability
- Budget-conscious projects requiring full JPIDM feature parity
- Long-term strategic investment in custom IAM platform
- Requirement for MCP-native AI agent integration

---

### Tier 2 Option: **Microsoft Entra ID Governance + Custom Portal**

**Rationale**:
- **Native Lifecycle Workflows**: Automated joiner/mover/leaver without custom Azure Functions code
- **Microsoft ecosystem**: Full Graph API, Entra ID native, no third-party vendor lock-in
- **Strong governance features**: Access Reviews, PIM, Entitlement Management native
- **High TCO**: ¥864M/year average (+764% vs. baseline) driven entirely by Governance add-on licensing at 67K users

**When to consider**:
- If regulatory/compliance requirements specifically mandate native Microsoft Lifecycle Workflows
- If development capacity is severely constrained and native automation is preferred over custom Azure Functions
- If user population is significantly smaller (licensing cost scales per-user)

**Trade-off**: At 67,000 users, the ¥815M/year Governance license alone exceeds all commercial IGA options except One Identity. This option only makes sense if the compliance/automation value justifies the cost premium.

---

### Tier 3 Alternative: **SailPoint ISC or Saviynt** (Enterprise Governance Focus)

**Rationale**:
- **Strong governance**: Best-in-class access certification, role analytics, risk scoring
- **Compliance-heavy**: If SOX, GDPR, regulatory audit requirements are paramount
- **Vendor support**: Professional services and vendor-managed operations available

**Not Recommended Unless**:
- Budget is **not a constraint** (4-5x cost of baseline)
- **Governance and compliance** are primary drivers (not self-service portal UX)
- Organization prefers **vendor-managed operations** over in-house DevOps

**Risk Acknowledgment**:
- High cost (¥350-490M/year)
- Vendor lock-in (difficult migration path)
- Limited self-service portal flexibility (may still need custom UI)
- Hybrid AD provisioning via connectors (not native)

---

### Not Recommended:

#### ❌ One Identity Manager
- **Reason**: Legacy on-premises architecture, extremely high cost (¥519M/year avg), Oracle ownership uncertainty, poor DevOps fit

#### ❌ Omada Identity
- **Reason**: Poor hybrid cloud capabilities, dated UX, high cost (¥493M/year avg), weak Asia-Pacific presence

#### ❌ Okta Workforce Identity Cloud
- **Reason**: Authentication-first platform — **not designed for on-premises AD object management**. Okta's AD Agent syncs user attributes but cannot manage the full suite of AD objects that JPIDM requires (security groups with expiration, computer accounts, resource/room accounts, shared mailboxes). Highest 3-year TCO of all evaluated options (¥2.76B, +819% vs. baseline). Significant custom development required on top of Okta to achieve even partial JPIDM feature parity, making it the most expensive and least fit option for this use case.
- **Exception**: Okta may be worth evaluating if the organisation has a **separate SSO/MFA consolidation initiative** (not JPIDM replacement) and already has a relationship with Okta as an authentication provider.

#### ⚠️ Midpoint (Use with Caution)
- **Reason**: Maturity risk in Asia-Pacific, Java/Groovy expertise gap, portal gaps
- **Consider only if**: Open source philosophy is strategic mandate AND Java expertise available

---

## 7. Decision Matrix

Use this table to guide platform selection based on organizational priorities:

| Priority | Recommended Platform | Rationale |
|----------|---------------------|-----------|
| **Lowest TCO** | Entra ID Gov + Custom Portal | ¥41M/year (59% savings) |
| **Best DevOps Fit** | Custom Build or Entra ID Gov + Custom | CI/CD native, IaC, container-ready |
| **Fastest Implementation** | Entra ID Gov + Power Apps MVP | 3-6 months for core features |
| **100% Feature Parity** | Custom Build | Full control over UX and features |
| **Strongest Governance** | SailPoint ISC | Enterprise-grade access certification |
| **AI Agent Readiness** | Custom Build or Entra ID Gov | MCP-native, Graph API, REST APIs |
| **Lowest Vendor Lock-In** | Custom Build | Open source stack, Graph API standard |
| **Enterprise Compliance** | SailPoint or Saviynt | Native audit trails, SOX/GDPR features |

---

## 8. Next Steps

### Immediate Actions (Week 1-2)

1. **Stakeholder Alignment**:
   - Present this evaluation to EUSP Japan Team and DevOps team
   - Confirm budget constraints and TCO tolerance
   - Clarify governance requirements (access certification, SOX audit scope)

2. **Proof-of-Concept Planning**:
   - If Entra ID Gov + Custom Portal recommended: Plan Power Apps MVP (group management feature)
   - If Custom Build recommended: Architect .NET 8 + React prototype (group management + Graph API)

3. **Risk Assessment**:
   - Review operational risks with Avanade (contract sunset timeline)
   - Assess in-house team .NET/React/Azure skills (training gap analysis)

### Phase 1 Activities (Month 1-3)

1. **Pilot Implementation**:
   - Build group management feature as PoC (Microsoft-native approach)
   - Deploy Entra ID Provisioning Agents (hybrid AD capability)
   - Test Graph API integration (group CRUD, membership operations)

2. **User Validation**:
   - Pilot with 50-100 users (Japan IT team)
   - Gather UX feedback (Japanese/English language quality)
   - Validate approval workflow requirements

3. **TCO Validation**:
   - Actual Azure infrastructure costs (App Service, Functions, SQL)
   - In-house development velocity measurement
   - Compare against Avanade operational baseline

---

## Appendix A: Licensing Models Summary

### Microsoft Entra ID Governance
- **Model**: Per-user subscription (included in E5 or standalone P2)
- **Cost**: E5 includes Entra ID P2 (Governance features)
- **Incremental**: ¥0 if E5 already licensed

### SailPoint ISC
- **Model**: Per-active-identity/year
- **Cost**: $30-50/identity/year (typically $40)
- **Minimum**: Usually 10,000 identity minimum commitment

### Saviynt EIC
- **Model**: Per-identity/year
- **Cost**: $25-35/identity/year (typically $30)
- **Minimum**: Flexible, no hard minimum

### Omada Identity
- **Model**: Per-identity/year (on-premises or private cloud)
- **Cost**: $35-45/identity/year (estimate)
- **Infrastructure**: Additional on-premises server costs

### One Identity Manager
- **Model**: Perpetual license + annual maintenance (20%)
- **Cost**: $75-100/identity (one-time) + 20%/year maintenance
- **Infrastructure**: Significant on-premises SQL Server cluster costs

### Midpoint (Open Source)
- **Model**: Apache License 2.0 (free)
- **Commercial Support**: Evolveum support contract optional ($50K-100K/year)
- **Infrastructure**: VM or container hosting costs

### Okta Workforce Identity Cloud
- **Model**: Per-user subscription (tiered: Essentials → Lifecycle Management → Governance)
- **Cost**: Lifecycle Management + Workflows tier: ~$7/user/month (estimate for full-feature deployment)
- **Minimum**: Flexible; enterprise contracts typically 1,000+ user minimums
- **Additional Modules**: Access Gateway (on-premises hybrid), Workflows, ThreatInsight — priced separately or in higher tiers
- **Note**: Authentication-focused pricing model; per-user costs at 67K scale make it the most expensive option evaluated

### Custom Build
- **Model**: Development investment + Azure infrastructure
- **Cost**: One-time dev (¥50-80M) + ¥5-10M/year Azure + ¥15-25M/year operations
- **Licenses**: Microsoft E5 (already owned), Azure consumption

---

## Appendix B: Vendor Stability Assessment

| Vendor | Market Position | Financial Stability | Roadmap Confidence | Risk Rating |
|--------|----------------|--------------------|--------------------|-------------|
| **Microsoft** | Market leader (Entra ID) | Excellent (Fortune 500) | High (strategic investment) | Low |
| **SailPoint** | Leader (Gartner IGA Magic Quadrant) | Good (Public: SAIL) | High (continuous innovation) | Low |
| **Saviynt** | Challenger (growing fast) | Good (PE-backed, funding secure) | Medium-high (rapid feature velocity) | Low-medium |
| **Okta** | Leader (Gartner Access Management Magic Quadrant) | Good (Public: OKTA) | High (authentication roadmap strong; IGA/AD mgmt roadmap secondary) | Low-medium |
| **Omada** | Niche (Europe-focused) | Fair (private, limited disclosure) | Medium (steady releases) | Medium |
| **Quest/One Identity** | Declining (Oracle ownership) | Fair (Oracle subsidiary) | Low (Oracle integration uncertainty) | High |
| **Evolveum (Midpoint)** | Niche (open source) | Fair (commercial support revenue) | Medium (community-driven) | Medium |

---

## Document History

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | 2026-03-26 | Product Specialist - Identity | Initial comprehensive platform evaluation |
| 1.1 | 2026-03-26 | Product Specialist - Identity | Added Okta Workforce Identity Cloud as evaluated platform (section 1.8); updated pricing, TCO, feature matrix, migration, risk, recommendations, and appendices |

---

**Contact**: EUSP Engineering Team  
**Next Review**: 2026-06-30 (post-PoC validation)
