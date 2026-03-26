# GISC Global Identity Service
## Comprehensive Workforce Identity Management Service

**Document Type:** Service Description (Ideal State with Gap Analysis)  
**Last Updated:** March 2026  

---

## Document Approach

**This document defines the IDEAL future state of GISC Global Identity Service and systematically identifies gaps against that vision.**

**Structure:**
1. **Ideal Service Definition** — Sections 1-3 describe what GISC *should* deliver
2. **Current Implementation** — Sections 3-4 detail how services are *currently* implemented, including gaps
3. **Gap Consolidation** — Section 5 prioritizes and summarizes critical gaps
4. **Path Forward** — Section 6 recommends consolidation goals to close gaps

**Reading Guide:**
- Executive leaders: Read Executive Summary + Sections 5-6 (gaps + recommendations)
- Service architects: Read Sections 2-4 (ideal capabilities + current implementation gaps)
- Implementation teams: Focus on Section 4 (current state) and Section 5 (priority matrix)

---

## Executive Summary

GISC Global Identity Service delivers secure, seamless single sign-on (SSO) and unified account lifecycle management for Sony's global workforce. We manage platform operations, ensure compliance with CISD security standards, and provide application owners with secure access governance frameworks.

**Core Mission:** Enable frictionless identity access while maintaining enterprise-grade security and operational consistency across Asia, Americas, and EMEA regions.

---

## 1. SERVICE SCOPE

### In Scope ✓
- **Workforce Identity** — Sony employees, contingent workers, contractors, service accounts (API/automation), and privileged admin accounts requiring access to business applications and systems
- **Business Partner Identity** — Guest accounts using external identities from partner organizations

### Out of Scope ✗
- **Consumer Identity** — PlayStation network and external customer-facing accounts
- **Application-Specific Identity** — Application database or standalone IAM-managed accounts

---

## 2. SERVICE CAPABILITIES

### What We Deliver
| Capability | Description |
|-----------|-----------|
| **Single Sign-On (SSO)** | Seamless authentication for desktop and application access across enterprise |
| **Account Lifecycle Management** | Automated provisioning, updates, and deprovisioning for all account types |
| **Data Quality Assurance** | Verification mechanisms and compliance reports for user data owners |
| **Secure Credential Management** | Advanced issuance, recovery, and authentication method support |
| **Platform Security** | CISD-compliant access controls, regular security audits, automated backups |
| **Authentication Diversity** | CISD-compliant methods including passwordless and phishing-resistant credentials |
| **Entitlement Management** | Group-based access controls with self-service and attestation capabilities |
| **SSO Enablement** | Streamlined processes for application onboarding and integration guidance |
| **Privileged Access** | Role-based controls with JIT access for regional IT administrators |

---

## 3. CORE SERVICES & IMPLEMENTATION STATUS

### 4.1 Core Directory and Platform Security Management

**Purpose:** Ensure directory platforms (Active Directory, Entra ID) meet enterprise security and compliance standards.

**Current Capabilities:**
- ✅ Secure, recoverable directory platform architecture
- ✅ Regular security audits identifying and remediating vulnerabilities
- ✅ Compliance alignment with industry security standards
- ✅ Automated backup and recovery procedures

**Known Gaps:**
| Gap | Impact | Current State |
|-----|--------|---------------|
| **Entra ID Backup Strategy** | Limited recovery assurance for cloud identity data | Relying on Microsoft SLA; not operationalized internally |
| **Device/Application Object Protection** | Non-user objects may lack equivalent security | Entra ID-native objects lack equivalent protections to AD |

---

### 4.2 Account Lifecycle Management

**Purpose:** Maintain consistent provisioning, updates, and deprovisioning policies across all account types and regions.

**Current Capabilities:**
- ✅ Consistent policy framework for user provisioning/deprovisioning
- ✅ Self-service capabilities for account modifications (limited by region)

**Critical Gaps:**

#### Regular User Accounts (Fragmented Ownership)
| Region | Owner | Issues |
|--------|-------|--------|
| **Americas / EMEA** | Passport Team (non-EUSP) | External team; limited EUSP integration |
| **Japan (JP)** | EUSP JP (via JPIDM) | Regional silo; inconsistent with other regions |
| **Asia-Pacific (AP)** | Unknown/Multiple | No clear ownership; accounts via APIDM or on-prem AD |
| **Overall** | **FRAGMENTED** | No consistent ownership model; limited EUSP visibility |

#### Non-Human Accounts (AI Agents, Service Accounts)
- ❌ No centralized ownership
- ❌ Fragmented creation channels (service requests, JPIDM, Passport, others)
- ❌ No standardized lifecycle across regions

#### Contingent Worker Accounts
| Region | Owner | Status |
|--------|-------|--------|
| **JP** | EINS (non-EUSP) | Outside EUSP management |
| **AM/EU** | Passport (non-EUSP) | External control |
| **AP** | Unknown | No clear ownership |
| **Overall** | **Partially Managed** | Entirely outside EUSP; no unified process |

#### Guest Accounts
- ❌ No centralized lifecycle management across regions
- ⚠️ Any tenant user can independently invite guests
- ✅ JP only: Periodic sponsor reviews via EINS
- ❌ Other regions: No equivalent governance process

#### Root Cause Analysis
| Challenge | Systemic Issue |
|-----------|----------------|
| **Fragmented Ownership** | Responsibilities split across EUSP, Passport, EINS, APIDM, on-premises AD |
| **Inconsistent Standards** | No uniform de/provisioning policies across regions or account types |
| **Visibility Gaps** | EUSP has limited oversight of accounts managed by external partners |
| **Self-Service Limitations** | Only JP/EUSP have self-service; other regions require service requests |

---

### 4.3 Data Quality Intelligence

**Purpose:** Enable data owners to identify and remediate user data issues (e.g., manager assignments, organizational attributes).

**Current Capabilities:**
- ✅ Mechanisms for data owners to identify data quality issues
- ✅ Intelligence and compliance reports available

**Implementation Gaps:**
- ❌ No consistent data schema/format policy enforced on upstream data systems
- ❌ Limited proactive intelligence to assist data owners in quality improvement
- ⚠️ Data owners operate without clear quality standards or assistance tools

---

### 4.4 Entitlement Management & Access Governance

**Purpose:** Provide application owners with secure, auditable mechanisms to manage and verify user access.

**Current Capabilities:**
- ✅ Group-based entitlement mechanisms for application delivery
- ✅ Self-service group management (regional availability varies)
- ✅ Access attestation features for periodic verification

**Known Gaps:**
| Gap | Scope | Current State |
|-----|-------|---------------|
| **Regional Self-Service Disparity** | Group membership management | JP has self-service via JPIDM; AM/EU/AP require service requests |
| **Attestation Adoption** | Access review features | Available but not widely adopted by application owners |
| **Distributed Responsibility** | Entitlement operations | EUSP JP + Passport + others = inconsistent tooling |

---

### 4.5 Authentication Methods & Credential Management

**Purpose:** Provide CISD-compliant authentication methods, supporting secure credential issuance, recovery, and modern passwordless adoption.

**Current Capabilities:**
- ✅ CISD-compliant authentication methods and credential policies
- ✅ Advanced passwordless and phishing-resistant credential support
- ✅ Secure self-service password reset (Entra ID SSPR, JPIDM)
- ✅ Legacy credential management during transition

**Migration Status:**
| Component | Current Owner | Target State | Status |
|-----------|---------------|------------|--------|
| **JP Credentials** | EUSP JP (JPIDM) | Entra ID SSPR | **In Migration** |
| **AM/EU Credentials** | Passport | Entra ID SSPR | **In Migration** |
| **MFA Administration** | Passport (partial) | Unified platform | Uses Thales & RSA; distributed |
| **Self-Service Recovery** | Multiple teams | Centralized | Not yet available without GSD |

---

### 4.6 Access Control Management

**Purpose:** Enforce CISD-compliant access control policies using Entra ID Conditional Access as the central Policy Enforcement Point (PEP).

**Current Capabilities:**
- ✅ CISD-compliant access control policies enforced by default
- ✅ Entra ID Conditional Access as central enforcement engine

**Implementation Gaps:**
- ❌ No scalable MFA enforcement for on-premises Active Directory applications
- ❌ Inconsistent exception handling; legacy exceptions persist, rarely revisited post-onboarding
- ⚠️ Exception governance framework missing

**Impact:** Applications with access exceptions may not meet minimum CISD compliance requirements.

---

### 4.7 SSO Enablement & Application Integration

**Purpose:** Streamline application onboarding and provide clear guidance for SSO-ready development.

**Current Capabilities:**
- ✅ Streamlined SSO enablement process
- ✅ Self-service options for low-risk applications
- ✅ Clear review/approval processes for high-risk permissions
- ✅ Developer guidance documentation

**Process Challenges:**
| Gap | Severity | Root Cause |
|-----|----------|-----------|
| **Approval Process Ambiguity** | High | Security/privacy approvals (data location, network flow) unclear; conflated with SSO process |
| **Request Form Complexity** | Medium | Excel-based forms requiring manual processing |
| **Distributed Responsibilities** | High | JP managed by Application Auth Team + Dalian nearshore; inconsistent info repositories |

---

### 4.8 Privileged Access Management (PAM)

**Purpose:** Empower OpCo and regional IT administrators with secure, auditable privileged access to directory services.

**Current Capabilities:**
- ✅ Role-Based Access Control (RBAC) for directory administration
- ✅ Just-in-Time (JIT) access controls implemented
- ✅ Phishing-resistant credential requirements
- ✅ Periodic access reviews by privilege tier
- ✅ Comprehensive audit and compliance reporting

**Known Gaps:**
- ⚠️ Policy enforcement inconsistent across all risk tiers
- ⚠️ Some privileged access lacks JIT or periodic review controls

---

## 4. CURRENT STATE: IDENTITY TYPES & PROVISIONING

### What Exists Today

GISC manages five primary identity types across global operations, each with distinct entry points and provisioning paths:

| Identity Type | Description | Entry Point / Source System | Owner | Current Status |
|---------------|-------------|---------------------------|-------|--------|
| **FTE Employee** | Sony full-time employees | HR Database (Workday, Castnet) | HR Systems → EUSP | Primary (well-managed) |
| **Contingent Workers** | Contractors, temporary staff | SailPoint / EINS | SailPoint / EINS | Partially Managed |
| **Service Accounts** | API/automation service identities | JPIDM, SailPoint | Multiple (regional) | **Fragmented** |
| **Admin Accounts** | Elevated privileged accounts | Tenant (Entra ID) | IT Operations | Controlled |
| **Guest Accounts** | External partner/vendor access | Azure AD B2B invitation | End Users / IT | **Limited Governance** |

### Current Provisioning Flows

```
FTE Employee:
Workday/Castnet → HR DB → Okta/Entra ID → Applications

Contingent Workers:
SailPoint/EINS → Identity Store → Okta/Entra ID → Applications

Service Accounts:
JPIDM/SailPoint → Okta → Application APIs

Admin Accounts:
IT Request → Entra ID (Just-in-Time) → Directory Access

Guest Accounts:
B2B Invitation → Azure AD → Applications (no governance)
```

---

## 5. CRITICAL GAPS SUMMARY

### Priority Matrix

| Priority | Issue | Scope | Impact | Owner |
|----------|-------|-------|--------|-------|
| **🔴 CRITICAL** | Account lifecycle fragmentation | All regions | Inconsistent compliance, visibility gaps | Multiple teams |
| **🔴 CRITICAL** | Regular user account ownership ambiguity | AP region | Account management risk | Unknown |
| **🟠 HIGH** | SSO enablement delays | JP primarily | Slower application onboarding | App Auth Team + Dalian |
| **🟠 HIGH** | Entitlement self-service disparity | AM/EU/AP | Increased support requests, slow access provisioning | Passport |
| **🟠 HIGH** | MFA enforcement for on-prem apps | Integrated applications | Compliance gaps for legacy apps | Access Control team |
| **🟡 MEDIUM** | Guest account governance | AM/EU/AP | Uncontrolled guest access, compliance risk | Regional admins |
| **🟡 MEDIUM** | Data quality standards | All regions | Poor data integrity for user attributes | Data governance |
| **🟡 MEDIUM** | Attestation adoption | All regions | Limited access verification rigor | App owners |

---

## 6. RECOMMENDATIONS

### Consolidation Goals
1. **Establish single lifecycle ownership per account type** — Centralize under EUSP with clear regional roles
2. **Deploy self-service globally** — Extend JPIDM/Entra SSPR capabilities to all regions
3. **Unify MFA management** — Migrate all regions to Entra ID MFA; retire Thales/RSA
4. **Clarify SSO approval process** — Separate security/privacy review from SSO enablement; reduce friction
5. **Enforce exception governance** — Annual re-certification of all access exceptions
6. **Implement centralized guest governance** — Adopt JP's sponsor review model globally

---

## 7. TERMS & DEFINITIONS

| Term | Definition |
|------|-----------|
| **CISD** | Sony's Crown IT Security Division; defines security standards |
| **EUSP** | European User Service Platform (JP/EUSP operates JPIDM) |
| **SSPR** | Self-Service Password Reset; Entra ID feature |
| **JPIDM** | Japan Identity Delivery Management; regional platform |
| **Passport** | AM/EU identity management system (non-EUSP owned) |
| **APIDM** | Asia-Pacific Identity Delivery Management |
| **JIT** | Just-In-Time access; temporary elevated privileges |
| **RBAC** | Role-Based Access Control |
| **PEP** | Policy Enforcement Point; central access control decision engine |
| **PAM** | Privileged Access Management |

---

**Next Review:** Q2 2026  
**Document Owner:** GISC Service Management  
**Last Updated:** March 24, 2026
