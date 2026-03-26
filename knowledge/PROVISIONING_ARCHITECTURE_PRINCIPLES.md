# Provisioning Architecture Principles for Next-Generation IDM Platform

## Document Purpose

This document establishes the **mandatory architectural principles** for provisioning in the next-generation Identity Management (IDM) platform that will replace JPIDM and potentially consolidate other on-premises provisioning systems across Sony Group companies.

These principles ensure:
- **Cloud-readiness** — the platform can transition from hybrid to cloud-native without re-engineering
- **Standards-based approach** — using SCIM standard for interoperability and vendor flexibility
- **Elimination of legacy dependencies** — no direct ADSI/LDAP writes to Active Directory
- **Future-proof architecture** — when on-premises AD is decommissioned, only the target endpoint changes

---

## Core Principle: API-Driven Provisioning is Mandatory

All account provisioning in the next-generation IDM platform **MUST** use **API-driven provisioning** via REST/SCIM protocols.

### What This Means

The new IDM platform will:
- Send provisioning requests as **SCIM-compliant REST API calls** to Microsoft Entra ID endpoints
- Use the **Microsoft Entra inbound provisioning API** (Graph API `/bulkUpload` endpoint)
- **NEVER** write directly to on-premises Active Directory via ADSI, LDAP, or other legacy protocols
- Operate as a SCIM client, sending bulk provisioning operations to Microsoft's cloud service

### What This Replaces

| Legacy Approach | Next-Generation Approach |
|----------------|--------------------------|
| Direct ADSI writes to on-prem AD (JPIDM model) | REST API calls to Microsoft Entra inbound provisioning endpoint |
| LDAP synchronization engines | SCIM standard provisioning |
| Microsoft Identity Manager (MIM) push-based sync | Microsoft Entra provisioning service orchestration |
| Custom AD PowerShell scripts | Standardized SCIM bulk operations |

---

## Microsoft Technology Reference

### Official Product Names and Features

| Component | Microsoft Product Name | Purpose |
|-----------|------------------------|---------|
| **Provisioning API** | **Microsoft Entra API-driven inbound provisioning** | SCIM-based REST API endpoint that accepts bulk provisioning requests from external systems |
| **On-premises connector** | **Microsoft Entra Connect Provisioning Agent** | Lightweight agent that bridges Entra ID provisioning service to on-premises Active Directory |
| **Provisioning service** | **Microsoft Entra provisioning service** | Cloud-based orchestration service that processes SCIM requests, applies attribute mappings, and executes provisioning to target systems |
| **API endpoint** | **Microsoft Graph `/bulkUpload` API** | The specific Graph API endpoint for sending SCIM bulk provisioning operations |

### Key Microsoft Learn Documentation

- **API-driven inbound provisioning concepts**  
  https://learn.microsoft.com/en-us/entra/identity/app-provisioning/inbound-provisioning-api-concepts

- **On-premises SCIM provisioning architecture**  
  https://learn.microsoft.com/en-us/entra/identity/app-provisioning/on-premises-scim-provisioning

- **Microsoft Entra Connect Provisioning Agent**  
  https://learn.microsoft.com/en-us/entra/identity/hybrid/cloud-sync/what-is-provisioning-agent

- **Graph API `/bulkUpload` specification**  
  https://learn.microsoft.com/en-us/graph/api/synchronization-synchronizationjob-post-bulkupload

### Licensing Requirements

- **Minimum**: Microsoft Entra ID P1 or Premium P2
- **Recommended**: Microsoft Entra ID Governance (higher API call limits: 6,000/day vs. 2,000/day)

---

## Phase 1 — Transitional: API-Driven to On-Premises AD

### Purpose

During the initial migration period, on-premises Active Directory remains the authoritative account store. The new IDM platform provisions accounts via the Entra inbound provisioning API, which writes to on-prem AD via the provisioning agent. Entra ID Connect then syncs AD up to Entra ID cloud (unchanged from today).

### Architecture Flow

```
┌─────────────────────────────────────────────────────────────────────────┐
│                            PHASE 1                                      │
│             API-Driven Provisioning → On-Premises AD                    │
└─────────────────────────────────────────────────────────────────────────┘

┌──────────────────────┐
│   New IDM Platform   │  ← Joiner/Mover/Leaver events from EINS/HR/etc.
│  (SailPoint / Entra  │
│  ID Governance / ?)  │
└──────────┬───────────┘
           │
           │ ① SCIM REST API calls
           │    (POST /bulkUpload)
           ▼
┌─────────────────────────────────────────────────────────┐
│   Microsoft Entra ID Inbound Provisioning API Endpoint  │
│              (Graph API /bulkUpload)                    │
│   • Receives SCIM bulk requests                         │
│   • Applies attribute mappings & scoping rules          │
│   • Orchestrates provisioning workflow                  │
└────────────────────────┬────────────────────────────────┘
                         │
                         │ ② SCIM provisioning instructions
                         ▼
           ┌──────────────────────────────────┐
           │  Microsoft Entra Connect         │
           │  Provisioning Agent              │  ← On-premises connector
           │  (Installed on Windows Server)   │
           └────────────┬─────────────────────┘
                        │
                        │ ③ Writes via LDAP (Microsoft-managed)
                        ▼
           ┌──────────────────────────────────┐
           │   On-Premises Active Directory   │
           │      (JP.SONY.COM, etc.)         │
           └────────────┬─────────────────────┘
                        │
                        │ ④ Entra ID Connect sync (unchanged)
                        ▼
           ┌──────────────────────────────────┐
           │   Microsoft Entra ID (Cloud)     │
           │   • Cloud identity records       │
           │   • Authentication source        │
           └──────────────────────────────────┘
```

### Key Characteristics

1. **IDM platform is cloud-native**: Sends REST API calls to Microsoft's cloud service
2. **Microsoft-managed AD writes**: The provisioning agent handles LDAP writes — not the IDM platform
3. **No ADSI dependency**: The new platform never touches ADSI or on-prem AD directly
4. **Preserves existing sync**: Entra ID Connect continues to sync on-prem AD → Entra ID (unchanged)
5. **Protocol remains constant**: The IDM platform only knows SCIM/REST — no on-prem awareness

---

## Phase 2 — Cloud-Centered: Entra ID Provisions to On-Premises AD

### Purpose

Entra ID becomes the **authoritative cloud identity store**. The provisioning direction reverses — instead of AD being the source that syncs up to Entra ID, Entra ID now writes **down** to on-premises AD via the provisioning agent. Entra ID Connect (AD→cloud sync) is replaced by cloud-to-AD provisioning.

The new IDM platform requires **zero changes** — it still sends the same SCIM calls to the same Entra inbound provisioning endpoint.

### Architecture Flow

```
┌─────────────────────────────────────────────────────────────────────────┐
│                            PHASE 2                                      │
│          Cloud-Centered: Entra ID → On-Premises AD                      │
└─────────────────────────────────────────────────────────────────────────┘

┌──────────────────────┐
│   New IDM Platform   │  ← Joiner/Mover/Leaver events from EINS/HR/etc.
│  (SailPoint / Entra  │
│  ID Governance / ?)  │
└──────────┬───────────┘
           │
           │ ① SCIM REST API calls
           │    (POST /bulkUpload)
           │    ** SAME PROTOCOL AS PHASE 1 **
           ▼
┌─────────────────────────────────────────────────────────┐
│   Microsoft Entra ID (Cloud) — Authoritative Store      │
│   • Receives SCIM bulk requests via inbound prov. API   │
│   • Cloud identity is now the source of truth           │
│   • Applies attribute mappings & lifecycle workflows    │
└────────────────────────┬────────────────────────────────┘
                         │
                         │ ② Entra ID provisions DOWN to on-prem AD
                         │    (cloud-to-on-premises provisioning)
                         ▼
           ┌──────────────────────────────────┐
           │  Microsoft Entra Connect         │
           │  Provisioning Agent              │  ← Still on-prem, now writes
           │  (Installed on Windows Server)   │     from Entra → AD direction
           └────────────┬─────────────────────┘
                        │
                        │ ③ Writes via LDAP (Microsoft-managed)
                        ▼
           ┌──────────────────────────────────┐
           │   On-Premises Active Directory   │
           │      (JP.SONY.COM, etc.)         │
           │   • Kept in sync FROM Entra ID   │
           │   • No longer the source of truth│
           └──────────────────────────────────┘

❌  Entra ID Connect (AD→cloud sync) — REPLACED by cloud-to-AD provisioning
✅  On-Premises AD still in use — but now as a downstream target, not source
```

### Key Characteristics

1. **Entra ID is authoritative**: Cloud identity is the master record — AD is a downstream replica
2. **Provisioning direction reversed**: Entra → AD (not AD → Entra via Entra ID Connect)
3. **IDM platform unchanged**: Same SCIM/REST calls to the same endpoint as Phase 1
4. **On-prem AD still running**: Applications and services dependent on AD continue to work
5. **Entra ID Connect retired**: AD sync replaced by Entra-managed write-back to AD
6. **Cloud-exit is now simple**: AD can be retired without touching the IDM platform

---

## Phase 3 — Cloud-Native: Direct Entra ID

### Purpose

When on-premises Active Directory is decommissioned, the IDM platform requires **no re-engineering**. Only the **target endpoint configuration changes** — the SCIM protocol remains identical to Phases 1 and 2.

### Architecture Flow

```
┌─────────────────────────────────────────────────────────────────────────┐
│                            PHASE 3                                      │
│               Cloud-Native: Direct Entra ID Only                        │
└─────────────────────────────────────────────────────────────────────────┘

┌──────────────────────┐
│   New IDM Platform   │  ← Joiner/Mover/Leaver events from EINS/HR/etc.
│  (SailPoint / Entra  │
│  ID Governance / ?)  │
└──────────┬───────────┘
           │
           │ ① SCIM REST API calls
           │    (POST /bulkUpload)
           │    ** SAME PROTOCOL AS PHASES 1 & 2 **
           ▼
┌─────────────────────────────────────────────────────────┐
│   Microsoft Entra ID Inbound Provisioning API Endpoint  │
│              (Graph API /bulkUpload)                    │
│   • Receives SCIM bulk requests                         │
│   • Applies attribute mappings & scoping rules          │
│   • Orchestrates provisioning workflow                  │
└────────────────────────┬────────────────────────────────┘
                         │
                         │ ② Direct provisioning to cloud
                         ▼
           ┌──────────────────────────────────┐
           │   Microsoft Entra ID (Cloud)     │
           │   • Cloud-native user accounts   │
           │   • No on-prem AD dependency     │
           │   • Direct authentication        │
           └──────────────────────────────────┘

❌  On-Premises Active Directory — DECOMMISSIONED
❌  Microsoft Entra Connect Provisioning Agent — REMOVED
❌  Entra ID Connect sync — NO LONGER NEEDED
```

### Key Characteristics

1. **Zero IDM platform changes**: The SCIM REST API calls are identical to Phases 1 and 2
2. **Configuration change only**: Admin re-points the provisioning app to target "cloud-only" instead of "on-premises AD"
3. **No on-prem components**: Provisioning agent is uninstalled; on-prem AD is retired
4. **Direct cloud authentication**: Users authenticate directly to Entra ID (no AD sync)
5. **Future-proof**: The IDM platform is unaware of the infrastructure change

---

## Strategic Rationale: Why API-Driven Provisioning Now?

### Problem: Legacy Provisioning is Not Cloud-Ready

JPIDM's current approach:
- Uses **ADSI (Active Directory Service Interfaces)** to write directly to on-prem AD
- Requires server infrastructure on the corporate network with AD access
- Cannot operate without on-premises AD connectivity
- Requires full platform re-engineering when migrating to cloud

**This architecture is incompatible with cloud-first strategy.**

### Solution: Protocol Decoupling via SCIM

By adopting API-driven provisioning now:

| Benefit | Description |
|---------|-------------|
| **Cloud-ready from day one** | IDM platform never depends on on-prem infrastructure |
| **Protocol stability** | SCIM/REST remains constant; only target endpoint changes |
| **No re-platforming** | Phase 1 → Phase 2 transition requires zero IDM code changes |
| **Standards-based** | SCIM is an open standard — reduces vendor lock-in |
| **Microsoft-managed AD writes** | Microsoft's provisioning agent handles LDAP complexity — not EUSP |
| **Incremental migration** | Can migrate regions/populations one at a time |

### Business Impact

- **Eliminate Avanade dependency** (¥100M/year operational cost, if full JPIDM replacement chosen)
- **Address MIM 2029 end-of-support** with either MIM decoupling or full platform replacement (see [JPIDM_MIM_DECOUPLING_OPTIONS.md](platforms/JPIDM_MIM_DECOUPLING_OPTIONS.md))
- **Future-proof architecture** — supports Sony's cloud-first strategy
- **Reduce technical debt** — standardizes on modern SCIM protocol

---

## Implications for Platform Selection

Any candidate platform for the next-generation IDM system **MUST** meet these requirements:

### Mandatory Capabilities

| Requirement | Acceptance Criteria |
|-------------|---------------------|
| **SCIM client support** | Platform must natively support outbound SCIM provisioning via REST API |
| **Entra ID inbound provisioning integration** | Platform must support Microsoft Graph `/bulkUpload` API endpoint |
| **Bulk operations** | Must support SCIM bulk request format (50 operations per API call) |
| **Attribute mapping** | Must allow configurable attribute transformations before sending to API |
| **Error handling** | Must query provisioning logs API and retry failed operations |
| **OAuth authentication** | Must support Microsoft Entra service principal authentication |

### Platform Features NOT Required (Replaced by Microsoft)

These capabilities were required in JPIDM but are **NOT** required in the new platform (if full replacement chosen) or alternative MIM-decoupling solution:
- ❌ ADSI libraries or LDAP write capability
- ❌ Direct Active Directory connectivity
- ❌ On-premises server infrastructure for provisioning
- ❌ Active Directory PowerShell module
- ❌ Microsoft Identity Manager (MIM) backend

> **Note:** If organization chooses MIM decoupling over full platform replacement, JPIDM's ASP.NET/ADSI components would remain but MIM would be replaced per these principles.

---

## Constraints and Non-Negotiables

### Architecture Constraints

| Constraint | Rationale |
|-----------|-----------|
| **MUST use SCIM REST API** | Only protocol supported by Microsoft Entra inbound provisioning |
| **MUST NOT use ADSI/LDAP** | Legacy approach incompatible with cloud migration strategy |
| **MUST support bulk operations** | Required for efficient provisioning at scale (~110,000 identities) |
| **MUST handle async processing** | Entra provisioning API is asynchronous; platform must poll for status |

### Transitional Period Constraints

| Constraint | Rationale |
|-----------|-----------|
| **MUST provision to on-prem AD** | Business operations require on-prem AD until 2029+ |
| **MUST use provisioning agent** | Only Microsoft-supported method for API → on-prem AD bridge |
| **MUST maintain Entra ID Connect** | Required for on-prem AD → Entra ID sync until decommission |
| **MUST support hybrid identities** | Users authenticate to on-prem AD (synced to Entra ID) |

### Security and Compliance Constraints

| Constraint | Rationale |
|-----------|-----------|
| **MUST use OAuth service principal** | Microsoft-recommended authentication for service accounts |
| **MUST implement retry logic** | Handle transient API failures and rate limiting (429 errors) |
| **MUST log provisioning events** | Audit trail required for compliance and troubleshooting |
| **MUST implement scoping rules** | Prevent accidental provisioning to wrong populations |

---

## Technical Specifications

### SCIM Bulk Request Format

The new IDM platform must construct SCIM bulk requests per the [SCIM RFC 7644 Bulk Operations](https://datatracker.ietf.org/doc/html/rfc7644#section-3.7) standard:

```json
POST https://graph.microsoft.com/v1.0/servicePrincipals/{id}/synchronization/jobs/{jobId}/bulkUpload

{
  "Operations": [
    {
      "method": "POST",
      "bulkId": "00aa00aa-bb11-cc22-dd33-44ee44ee44ee",
      "path": "/Users",
      "data": {
        "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
        "externalId": "G1234567",
        "userName": "john.doe@sony.com",
        "name": {
          "familyName": "Doe",
          "givenName": "John"
        },
        "active": true,
        "emails": [
          {
            "type": "work",
            "value": "john.doe@sony.com",
            "primary": true
          }
        ]
      }
    }
  ]
}
```

### API Rate Limits and Throttling

| Limit Type | Value | License Requirement |
|-----------|-------|---------------------|
| **API calls per 5-second window** | 40 | All licenses |
| **API calls per 24 hours** | 2,000 | Entra ID P1/P2 |
| **API calls per 24 hours** | 6,000 | Entra ID Governance |
| **Operations per bulk request** | 50 (recommended) | All licenses |

**Design Considerations:**
- Implement retry logic with exponential backoff for HTTP 429 (Too Many Requests)
- Batch operations into 50-operation chunks to maximize efficiency
- For 110,000 identities, plan provisioning cycles to stay within quota

---

## Relationship to Other Sony IDM Components

### EINS (Enterprise Id-management Networking Service)

- **Role**: Global ID authority and identity data aggregator
- **Relationship**: EINS remains the **authoritative source** for identity records
- **Integration**: New IDM platform consumes EINS data feeds (daily exports or API) and transforms to SCIM format
- **No change**: EINS does not provision accounts — it only issues Global IDs

### Passport (SailPoint IdentityIQ)

- **Role**: IAM platform for AM/EU/SM regions
- **Relationship**: Passport continues to provision in AM/EU/SM; new platform provisions in JP/AP regions
- **Future**: If new platform = SailPoint, Passport could be extended to JP/AP instead of replacing

### APIDM (Asia-Pacific Identity Management)

- **Status**: Undocumented; capabilities unknown
- **Relationship**: Candidate for replacement by new global platform
- **Action**: Investigate APIDM architecture and migration complexity before scoping

### Entra ID Connect (Azure AD Connect)

- **Role**: Syncs on-prem AD → Entra ID (password hash sync, seamless SSO, etc.)
- **Relationship**: Continues unchanged during Phase 1; retired in Phase 2
- **No change**: New IDM platform does not replace Entra ID Connect

---

## Governance and Decision Authority

| Decision Type | Authority | Rationale |
|--------------|-----------|-----------|
| **Architecture principles** | EUSP Engineering Team (Principal Architect) | Technical strategy and cloud-readiness |
| **Platform selection** | EUSP Engineering Team + Product Specialist | Vendor evaluation and procurement |
| **Implementation timeline** | EUSP Engineering Team + Project Manager | Resource availability and business impact |
| **Attribute mappings** | Identity Operations Team + Business stakeholders | Data governance and HR integration |
| **Security controls** | EUSP Security Engineer + InfoSec | OAuth scopes, audit logging, access control |

---

## Success Criteria

The provisioning architecture principles are successfully implemented when:

1. ✅ **Zero ADSI usage**: The new IDM platform does not contain ADSI libraries or LDAP write code
2. ✅ **SCIM-based provisioning**: All account creation/update/disable operations use Graph API `/bulkUpload`
3. ✅ **Hybrid deployment validated**: Provisioning agent successfully writes to on-prem AD in Phase 1
4. ✅ **Cloud-ready design**: Configuration change (not code change) enables Phase 2 cloud-native provisioning
5. ✅ **Avanade dependency eliminated**: EUSP team operates the platform without vendor reliance
6. ✅ **MIM 2029 deadline met**: Organization has either (a) decoupled MIM from JPIDM using API-driven alternatives, or (b) deployed full platform replacement before MIM end-of-support
7. ✅ **Feature parity**: All critical JPIDM capabilities (FTE/contingent/group mgmt) are replaced

---

## References and Further Reading

### Microsoft Documentation

- [API-driven inbound provisioning concepts](https://learn.microsoft.com/en-us/entra/identity/app-provisioning/inbound-provisioning-api-concepts)
- [Configure API-driven inbound provisioning app](https://learn.microsoft.com/en-us/entra/identity/app-provisioning/inbound-provisioning-api-configure-app)
- [On-premises SCIM provisioning](https://learn.microsoft.com/en-us/entra/identity/app-provisioning/on-premises-scim-provisioning)
- [Microsoft Graph bulkUpload API reference](https://learn.microsoft.com/en-us/graph/api/synchronization-synchronizationjob-post-bulkupload)
- [What is cloud sync?](https://learn.microsoft.com/en-us/entra/identity/hybrid/cloud-sync/what-is-cloud-sync)
- [SCIM protocol RFC 7644](https://datatracker.ietf.org/doc/html/rfc7644)

### Internal Sony Documentation

- [IDM Strategy Scoping V2](../IDM_STRATEGY_SCOPING_V2.md)
- [JPIDM Platform Documentation](platforms/JPIDM.md)
- [Platform Architecture Summary](PLATFORM_ARCHITECTURE_SUMMARY.md)
- [GISC Global Identity Service Strategy](../GISC_Global_Identity_Service.md)

---

## Document History

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | 2026-03-25 | EUSP Engineering Team | Initial release |

---

**Document Owner**: EUSP Engineering Team  
**Review Cycle**: Quarterly (or upon major Microsoft Entra feature releases)  
**Next Review Date**: 2026-06-25
