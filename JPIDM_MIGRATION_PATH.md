# JPIDM Migration Path

> This document covers the migration steps from JPIDM to the next-generation IDM platform.
> It assumes compliance with the provisioning architecture principles defined in [PROVISIONING_ARCHITECTURE_PRINCIPLES.md](PROVISIONING_ARCHITECTURE_PRINCIPLES.md).

---

## JPIDM Capabilities Requiring Replacement

| JPIDM Feature | Replacement Approach |
|--------------|----------------------|
| **FTE provisioning (EINS feed → AD)** | New IDM platform → SCIM API → Provisioning agent → AD |
| **Contingent worker provisioning** | Same as FTE (EINS Online Registration → new IDM platform) |
| **Group and mailbox management** | Provisioning API supports group operations; Exchange Online API for mailboxes |
| **Approval workflows** | New IDM platform must provide workflow engine |
| **Self-service portal** | New IDM platform must provide UI or integrate with ServiceNow/other portal |
| **Company affiliation attributes** | SCIM schema extensions support custom attributes |

---

## Technical Migration Steps (Phase 1 Transition)

1. **Deploy Microsoft Entra Connect Provisioning Agent** to Windows Server in Japan datacenter
2. **Configure API-driven inbound provisioning app** in Entra ID admin center
3. **Map EINS/GHD attributes** to SCIM schema in Entra provisioning app
4. **Pilot provisioning** to test population (e.g., 100 users) via SCIM API
5. **Cutover by population** (FTE → contingent workers → service accounts)
6. **Decommission JPIDM** after validation period
7. **Maintain provisioning agent** until Phase 2 (on-prem AD retirement)

---

## Document History

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | 2026-03-25 | EUSP Engineering Team | Split from PROVISIONING_ARCHITECTURE_PRINCIPLES.md |
