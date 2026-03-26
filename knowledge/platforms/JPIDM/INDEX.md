# IDM Web – Documentation Index

Welcome to the IDM Web documentation. Use this index to navigate all resources.

---

## Quick Navigation

### Understanding the System
| Document | Purpose |
|----------|---------|
| **[SYSTEM_CONTEXT.md](./SYSTEM_CONTEXT.md)** | Executive summary, enterprise role, system-of-record analysis, what system does NOT do |
| **[RELATIONSHIPS.md](./RELATIONSHIPS.md)** | How IDM Web relates to EINS, Passport, Active Directory, and GHD |
| **[FEATURES.md](./FEATURES.md)** | Authoritative feature matrix with concrete examples, FunctionID reference |

### Building and Developing
| Document | Purpose |
|----------|---------|
| **[SETUP.md](./SETUP.md)** | Prerequisites, database setup, Web.config, running the application |
| **[ARCHITECTURE.md](./ARCHITECTURE.md)** | System design, component layers, request workflow, data flow |
| **[PROJECT_STRUCTURE.md](./PROJECT_STRUCTURE.md)** | Full directory tree, folder descriptions, file naming conventions |
| **[CONTRIBUTING.md](./CONTRIBUTING.md)** | Code style, feature development checklist, PR process |

### Reference
| Document | Purpose |
|----------|---------|
| **[API.md](./API.md)** | MVC action routes, SharedController AJAX helpers, response conventions |
| **[CONFIGURATION.md](./CONFIGURATION.md)** | Web.config settings, connection strings, maintenance mode, AD config |
| **[TROUBLESHOOTING.md](./TROUBLESHOOTING.md)** | Common errors, SQL issues, AD connectivity, UI problems |

---

## Quick Links by Role

### New Developer
1. [SYSTEM_CONTEXT.md](./SYSTEM_CONTEXT.md) — Understand what this system does
2. [RELATIONSHIPS.md](./RELATIONSHIPS.md) — Understand how it fits in the enterprise
3. [SETUP.md](./SETUP.md) — Get it running locally
4. [ARCHITECTURE.md](./ARCHITECTURE.md) — Dive into the code structure
5. [PROJECT_STRUCTURE.md](./PROJECT_STRUCTURE.md) — Find your way around the files

### Making a Code Change
1. [CONTRIBUTING.md](./CONTRIBUTING.md) — Checklist and conventions
2. [ARCHITECTURE.md](./ARCHITECTURE.md) — Which layer to change
3. [PROJECT_STRUCTURE.md](./PROJECT_STRUCTURE.md) — Where to add files

### Architecture Review
1. [SYSTEM_CONTEXT.md](./SYSTEM_CONTEXT.md) — System purpose and scope
2. [RELATIONSHIPS.md](./RELATIONSHIPS.md) — Enterprise integration map
3. [FEATURES.md](./FEATURES.md) — Complete capability reference
4. [ARCHITECTURE.md](./ARCHITECTURE.md) — Technical design

### Deployment / Ops
1. [SETUP.md](./SETUP.md) — Deployment prerequisites
2. [CONFIGURATION.md](./CONFIGURATION.md) — All configuration options
3. [TROUBLESHOOTING.md](./TROUBLESHOOTING.md) — Known issues and fixes

---

## Document Descriptions

### [README.md](./README.md)
Project overview with key features, technology stack, and quick start guide.

### [SYSTEM_CONTEXT.md](./SYSTEM_CONTEXT.md)
**Enterprise-grade system context document** following EINS/Passport specification standards.

Covers:
- Executive summary and primary purpose
- System-of-record vs system-of-action analysis
- Population coverage (who IDM Web manages)
- Data ingestion models (GHD-driven, self-registration, request-approval)
- What IDM Web explicitly does NOT do
- Relationship to EINS, Passport, Active Directory, and GHD
- Architectural implications for modernization

### [RELATIONSHIPS.md](./RELATIONSHIPS.md)
**Side-by-side architectural comparison** of IDM Web vs related systems.

Covers:
- IDM Web vs EINS — identity directory vs self-service portal
- IDM Web vs Passport — Japan portal vs AM/EU/SM IAM governance
- IDM Web vs Active Directory — orchestration layer vs account store
- IDM Web vs GHD — consumer vs HR data source
- Common misconceptions and clarifications
- System interaction patterns

### [FEATURES.md](./FEATURES.md)
**Authoritative feature matrix** with concrete examples from the actual codebase.

Covers:
- User management features
- Security group lifecycle features
- Shared account/mailbox management
- Computer account management
- Resource management (rooms/equipment)
- Request and approval workflow
- Administrative features
- What IDM Web does NOT support
- Complete FunctionID reference
- Request status lifecycle

### [ARCHITECTURE.md](./ARCHITECTURE.md)
**Technical system design** including diagrams and component descriptions.

Covers:
- Full system architecture diagram
- Layer-by-layer descriptions (Presentation, Controller, Service, DataAccess, Database)
- Request workflow engine
- Authentication and authorization flow
- Configuration management approach
- Front-end bundling and JavaScript patterns

### [PROJECT_STRUCTURE.md](./PROJECT_STRUCTURE.md)
**Folder and file organization** reference.

Covers:
- Complete directory tree with descriptions
- Purpose of each folder
- Key file listings per folder
- Naming conventions
- Important configuration files

### [SETUP.md](./SETUP.md)
**Step-by-step setup guide** for local development.

Covers:
- Prerequisites
- Cloning and opening the solution
- Database configuration
- Web.config settings
- IIS Express Windows Authentication
- Running tests
- Development workflow tips

### [API.md](./API.md)
**Endpoint reference** for MVC actions and AJAX helpers.

Covers:
- SharedController AJAX endpoints (user lookup, group lookup, reference data)
- All MVC controller routes (GET + POST for each feature area)
- Request model structure
- Response conventions

### [CONFIGURATION.md](./CONFIGURATION.md)
**Complete configuration reference** for all settings.

Covers:
- All Web.config AppSettings with descriptions and examples
- Connection string format
- MaintenanceMode values and behavior
- Environment-specific transforms (Debug, Release)
- Define_MST runtime configuration
- AD connection settings
- Routing and bundle configuration

### [CONTRIBUTING.md](./CONTRIBUTING.md)
**Development guidelines** for code contributions.

Covers:
- Branching strategy
- VB.NET naming and style conventions
- Layer responsibility guide
- Feature development checklist
- Adding controllers, views, services
- Multi-button form pattern
- Writing unit tests
- PR process and commit message format
- Security best practices

### [TROUBLESHOOTING.md](./TROUBLESHOOTING.md)
**Diagnosis and resolution** for common issues.

Covers:
- Application startup failures (403, Windows Auth)
- SQL connection errors
- NuGet restore failures
- User authentication problems
- Database schema issues
- AD connectivity problems
- UI/JavaScript issues
- Bulk CSV upload problems
- Maintenance mode issues
- Common error message reference

---

## Related Resources

- [EINS.md](../EINS.md) — Enterprise Identity-management Networking Service specification
- [Passport.md](../Passport.md) — Passport/SailPoint IAM platform specification
- [README.md](../README.md) — Repository root

---

## Maintaining This Documentation

Update documentation when:
- A new controller/feature is added → update [FEATURES.md](./FEATURES.md), [API.md](./API.md)
- Architecture changes → update [ARCHITECTURE.md](./ARCHITECTURE.md)
- New config settings → update [CONFIGURATION.md](./CONFIGURATION.md)
- New error patterns found → update [TROUBLESHOOTING.md](./TROUBLESHOOTING.md)
- New folder/file added → update [PROJECT_STRUCTURE.md](./PROJECT_STRUCTURE.md)

---

*Last Updated: March 25, 2026*
