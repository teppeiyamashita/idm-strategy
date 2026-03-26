# Identity Types — Canonical Reference

> Source: IDM_STRATEGY_SCOPING_V2.md (v2, Platform Expert reviewed)
> This table is the authoritative reference for identity type classification across all regions.

## Identity Types and Provisioning Flows

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

*APIDM is referenced for the AP region but is **not documented** in the authoritative knowledge base. Capabilities, ownership, and migration complexity are unknown and should be investigated before committing to scope.*

---

## Key Architectural Facts

- **EINS** is the global identity record authority. It is NOT a provisioning platform. It issues Global IDs and distributes identity data downstream.
- **JPIDM** is the Japan provisioning engine. For Japan contingent workers, EINS is the entry point (EINS Online Registration) — JPIDM receives the EINS feed and provisions the AD account.
- **Passport** (SailPoint IdentityIQ) is the entry point for contingent workers in AM/EU/SM regions (via "Create Contingent Worker" form).
- **EUSP owns** only the Japan service/shared account entry point (IDM Web → JPIDM) and cloud admin/service accounts (Entra ID).
- **EUSP does NOT own** the entry points for FTEs (HR teams own Castnet/Workday), Japan contingent workers (EINS team), or AM/EU/SM contingent workers (Passport team).
