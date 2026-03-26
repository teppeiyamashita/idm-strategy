# IDM Web – Project Overview

---

## What Is IDM Web?

**IDM Web** (Identity Data Management Web) is Sony Japan's **self-service identity management portal**. It provides employees, managers, and administrators a web interface to manage AD accounts, security groups, shared mailboxes, computer accounts, and identity-related requests — all through a governed request-and-approval workflow.

> For deep architectural context, see [SYSTEM_CONTEXT.md](./SYSTEM_CONTEXT.md).

---

## Key Features

- **User Profile Management** — Employees edit own display name, mail settings, proxy addresses
- **Manager Controls** — Managers edit team member attributes and approve change requests
- **Security Group Lifecycle** — Create, edit, extend, enable, and delete security groups
- **Shared Mailbox Management** — Create shared mailboxes, manage delegates, bulk member changes
- **Computer Account Operations** — Register, move, and reset AD computer accounts
- **Resource Management** — Register and manage meeting rooms and equipment as resource mailboxes
- **Request & Approval Workflow** — All changes are tracked via a structured request lifecycle
- **Administrative Tools** — Admin panels for user lists, request queues, leave applications

---

## Technology Stack

| Component | Technology |
|-----------|-----------|
| Language | VB.NET |
| Framework | ASP.NET MVC 4 |
| Runtime | .NET Framework 4.7.2 |
| ORM | Entity Framework 6.2.0 |
| Database | Microsoft SQL Server (IDM_DB) |
| View Engine | Razor (`.vbhtml` templates) |
| JavaScript | jQuery 3.2.1, KnockoutJS 3.4.2, jQuery UI 1.12.1 |
| Authentication | Windows Integrated Authentication (AD) |
| Web API | Microsoft.AspNet.WebApi 4.0 |
| Unit Testing | NUnit 3.0.0 |

---

## Enterprise Integrations

| System | Role | Direction |
|--------|------|-----------|
| **EINS** | Identity truth & Global ID | Read (via GHD tables) |
| **GHD** (Global HR DB) | Employee & org master data | Read-only (IDM_DB tables) |
| **Active Directory** | Account/group persistent store | Read & Write (via DirectoryServices) |
| **Exchange Server** | Mail provisioning | Read & Write (via AD ProxyAddresses) |
| **Passport (SailPoint)** | Regional IAM (AM/EU/SM) | Parallel system (Japan equivalent) |

---

## Quick Start

### Prerequisites

- Visual Studio 2019 or later
- .NET Framework 4.7.2 SDK
- SQL Server (or access to IDM_DB)
- Windows Authentication enabled IIS
- AD service account with operator permissions

### Open and Build

```powershell
# Open solution
start IDM_Web.sln

# Restore NuGet packages (Visual Studio does this automatically)
# Or via Package Manager Console:
Install-Package EntityFramework -Version 6.2.0
```

### Configure

1. Edit [Web.config](../IDM_Web/Web.config) `connectionStrings` to point to your IDM_DB
2. Set `DebugModeEnabled` to `True` for local development
3. Set `DebugModeLoginUser` to your own AD account
4. Configure AD connection strings (`DebugModeGCConnectionString`, `DebugModeLDAPConnectionString`)

See [SETUP.md](./SETUP.md) for detailed setup steps.

### Run

- Press `F5` in Visual Studio (IIS Express)
- Navigate to `http://localhost:{port}/`

---

## Project Structure Summary

```
IDM_Web/
├── Controllers/       MVC controllers (request handling)
├── Services/          Business logic layer
├── DataAccess/        Entity Framework + AD integration
├── Models/            Domain models
├── ViewModels/        View-specific data models
├── Views/             Razor templates (.vbhtml)
├── Constants/         Enum and constant definitions
├── Validators/        Custom validation attributes
├── Extensions/        ASP.NET MVC extension methods
├── App_Start/         Startup configuration (routes, bundles)
├── Util/              Utility classes
└── Web.config         Application configuration
```

See [PROJECT_STRUCTURE.md](./PROJECT_STRUCTURE.md) for detailed descriptions.

---

## Documentation

| Document | Purpose |
|----------|---------|
| [INDEX.md](./INDEX.md) | Documentation navigation |
| [SYSTEM_CONTEXT.md](./SYSTEM_CONTEXT.md) | Enterprise role and scope |
| [RELATIONSHIPS.md](./RELATIONSHIPS.md) | EINS, Passport, AD comparisons |
| [FEATURES.md](./FEATURES.md) | Feature matrix with examples |
| [ARCHITECTURE.md](./ARCHITECTURE.md) | System design and components |
| [PROJECT_STRUCTURE.md](./PROJECT_STRUCTURE.md) | Folder and file organization |
| [SETUP.md](./SETUP.md) | Installation and development setup |
| [API.md](./API.md) | API endpoints and patterns |
| [CONFIGURATION.md](./CONFIGURATION.md) | Configuration reference |
| [CONTRIBUTING.md](./CONTRIBUTING.md) | Contribution guidelines |
| [TROUBLESHOOTING.md](./TROUBLESHOOTING.md) | Common issues and solutions |
