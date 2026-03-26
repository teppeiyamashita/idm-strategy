---
description: 'Guidelines for modular UX testing'
applyTo: '.github/skills/ux-test-*/**'
---

# UX Testing Best Practices for Onboarding Manager

## Core Principles

### 1. Modularity
- Each test is **independent** - can run in any order
- Each skill encapsulates one complete workflow
- Skills compose cleanly with minimal coupling

### 2. Reusability
- Common patterns (element discovery, verification) shared across skills
- `browser-use` skill as single source of truth for interactions
- Config and test-users shared across all modules

### 3. State Management
- **Setup**: Verify preconditions (env, user state, prerequisites)
- **Execute**: Perform test actions via browser-use commands
- **Verify**: Assert expected outcomes with eval/state checks
- **Artifact**: Capture screenshots at key milestones
- **Cleanup**: Reset state if needed for next test

### 4. Evidence Capture
- Screenshot on success at final milestone
- Screenshot on failure for diagnostics
- Structured logs to `tests/artifacts/reports/`
- Timestamped for traceability

### 5. Error Handling
- Retry failed interactions up to 3 times
- Capture screenshot on first failure
- Log detailed error with context
- Continue with next test or halt if critical

---

## Test Module Template

Every UX test skill follows this structure:

```markdown
---
name: ux-test-{module-name}
description: '{Clear description of what this test validates} Use when testing {specific workflow or feature}. Prerequisite: {dependencies}.'
uses: browser-use
depends-on: [list of prerequisite skills, if any]
---

# UX Test: {Module Name}

**Goal**: [One-sentence test objective]

**Prerequisite(s)**: [What must be true before this test runs]

## Setup
- [Verify environment]
- [Check user state]
- [Load configuration]

## Test Steps

### Step 1: [Action]
- `browser-use [command]`
- Expected: [what should happen]

### Step 2: [Action]
- [More steps...]

## Verification

- ✅ [Expected outcome 1]
- ✅ [Expected outcome 2]
- Screenshot: `tests/artifacts/screenshots/module-result.png`

## Return Value

```json
{
  "success": true,
  "key_data": "value",
  "metadata": {}
}
```

---
```

---

## browser-use Command Patterns

All skills use these **standardized patterns** for consistency:

### Element Discovery
```bash
# Get clickable elements
browser-use state

# Extract info via JavaScript
browser-use eval "document.querySelector('selector').textContent"
```

### Interaction
```bash
# Click by index from state
browser-use click 5

# Type text
browser-use input 3 "search text"

# Select dropdown
browser-use select 7 "option value"
```

### Verification
```bash
# Wait for appearance
browser-use wait selector "div[role=dialog]"
browser-use wait text "Success"

# Screenshot at milestone
browser-use screenshot tests/artifacts/screenshots/milestone-name.png

# Evaluate assertion
browser-use eval "document.querySelectorAll('tr').length === 10"
```

### Navigation
```bash
# Open URL
browser-use --headed open http://localhost:5173

# Scroll
browser-use scroll down --amount 300

# Go back
browser-use back
```

---

## Configuration File Format

`tests/config/ux-test-config.json`:
```json
{
  "environment": {
    "frontend_url": "http://localhost:5173",
    "backend_url": "http://localhost:7071",
    "browser_mode": "headless"
  },
  "timeouts": {
    "page_load": 10000,
    "modal_open": 5000,
    "api_response": 3000
  },
  "test_modules": {
    "initialization": { "enabled": true, "priority": 1, "required": true },
    "tap_issuance": { "enabled": true, "priority": 2, "required": false }
  },
  "artifacts": {
    "screenshots_dir": "tests/artifacts/screenshots",
    "reports_dir": "tests/artifacts/reports",
    "save_on_failure": true
  }
}
```

---

## Test User Format

`tests/config/test-users.json`:
```json
{
  "test_users": [
    {
      "id": "admin-001",
      "name": "Teppei Yamashita",
      "email": "teppei.yamashita@domain.com",
      "role": "admin",
      "mfa_enabled": false
    }
  ]
}
```

---

## Artifact Management

```
tests/artifacts/
├── .gitignore              # Ignores *.png, *.log, *.json
├── screenshots/
│   ├── init-dashboard.png
│   ├── tap-issued.png
│   └── pagination-page2.png
├── logs/
│   └── test-run-20260322.log
└── reports/
    └── ux-test-20260322.json
```

All generated files **ignored by git** (.gitignore configured)
Directories **preserved with .gitkeep** for CI/CD

---

## Error Recovery Strategy

| Error Type | Retry Count | Action on Failure |
|-----------|------------|-------------------|
| Network timeout | 3 | Retry, then skip |
| Element not found | 3 | Screenshot + log, then fail |
| Assertion failed | 0 | Screenshot + log, then fail |
| Critical (auth) | 0 | Halt test suite |

---

## Naming Conventions

- **Skill names**: `ux-test-{module}` (lowercase, hyphens)
- **Screenshots**: `{module}-{milestone}.png` (lowercase)
- **Reports**: `ux-test-{YYYYMMDD}.json`
- **Log statements**: Include `[MODULE-NAME]` prefix

Example: `[TAP-ISSUANCE] Modal opened successfully`

---
