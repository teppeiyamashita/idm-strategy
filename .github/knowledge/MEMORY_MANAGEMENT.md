# Memory Management Guide for Multi-Agent Orchestration

## Overview

All agents in the GISC transformation initiative leverage a structured memory system to maintain persistent context, avoid redundant discussion, and enable autonomous orchestration without losing information.

**Key Principle**: Memory is the connective tissue between rounds of agent dialogue. It prevents context loss and enables collaborative learning.

## Memory Architecture

### 1. Session Memory (`/memories/session/`)

**Lifespan**: Current orchestration only (cleared after task completion)

#### Problem Statement (`/memories/session/gisc-orchestration/problem-statement.md`)
- **Written by**: PMO at orchestration start
- **Read by**: All agents at start of each round
- **Contains**:
  - Initial objective statement
  - User-provided constraints (budget, timeline, org appetite)
  - Scope boundaries
  - Success criteria

**Agent Usage Pattern**:
```
@Agent reads: /memories/session/gisc-orchestration/problem-statement.md
Agent thinks: "What are the real constraints here? Budget is $2.5M and timeline is 3 years. 
Org has 2 existing engineers. MIM replacement is priority."
Agent comments: "Given these constraints, my recommendation is..."
```

#### Assumptions (`/memories/session/gisc-orchestration/assumptions.md`)
- **Written by**: Agents as they surface assumptions during Round 1
- **Read by**: All agents before Rounds 2-7
- **Contains**:
  - Shared assumptions (e.g., "SailPoint will handle IGA")
  - Questioned assumptions (e.g., "Can we really migrate 5000 staff in 6 months?")
  - Resolved assumptions (e.g., "No, phased migration over 12 months required")

**Agent Usage Pattern**:
```
@IA reads: /memories/session/gisc-orchestration/assumptions.md
IA notices: "We assumed SailPoint supports X, but HR flagged that's not proven"
IA statement: "Before finalizing architecture, we need vendor confirmation that SailPoint 
supports distributed provisioning to regional Active Directories"
PMO writes: "Assumption confirmed/flagged for vendor discussion in Round 2"
```

#### Constraints (`/memories/session/gisc-orchestration/constraints.md`)
- **Written by**: PMO + agents collaboratively during Round 1
- **Read by**: All agents before trade-off analysis (Round 3)
- **Contains**:
  - Hard constraints (budget $2.5M, timeline 3 years, MIM deadline)
  - Soft constraints (org change capacity, vendor maturity, team skillset)
  - Conflicting constraints (timeline vs cost vs quality)

**Agent Usage Pattern**:
```
@ITD reads: /memories/session/gisc-orchestration/constraints.md
ITD notes: "Constraint: $2.5M, 3-year roadmap, but MIM replacement is priority (Year 1-2)"
ITD statement: "Given MIM deadline, Phase 1 must fund MIM replacement. Other items shift right."
```

### 2. Agent Dialogue Memory (`/memories/session/agent-dialogue/`)

**Lifespan**: Current orchestration only (persists across all 7 rounds)

**Structure**: One markdown file per round
- `round-1-problem-understanding.md`
- `round-2-brainstorm.md`
- `round-3-tradeoffs.md`
- `round-4-consensus.md`
- `round-5-phase1-detail.md`
- `round-6-risks.md`
- `round-7-validation.md`

#### Round 1: Problem Understanding
- **What agents input**: "What's the real problem? What are we actually constrained by?"
- **Format**: Agent name + perspective
- **Example**:
```markdown
## Round 1: Problem Understanding

### Transformation Strategist
"Root problem: GISC fragmentation across 3 regions led to 7,784 manual identity tickets/year.
Real constraints: Regional autonomy expectations vs central MIM deadline.
Core trade-off: Consolidation (fast, disruptive) vs gradual evolution (slow, less risky)"

### Identity Architect
"Technical root cause: No unified architecture; each region has own MIM, own sync engine.
Constraint: MIM reaches EOL 2028; must replace by 2029.
Timing: Consolidation + new platform must happen in parallel to meet timeline."

### HR Advisor
"People constraint: Current team is 2 engineers. Cannot absorb consolidation + new platform.
Market reality: Finding Entra + SailPoint talent is expensive ($120-150K regional premium).
Budget implication: Team scaling is 40% of total transformation cost."
```

- **Agent Usage**: Agents read this before moving to Round 2. No need to re-explain constraints.

#### Round 2: Solution Brainstorm
- **What agents input**: "Given these constraints, what are the viable paths?"
- **Format**: Organized by proposed solution path
- **Example**:
```markdown
## Round 2: Brainstorm

### Path A: Entra + SailPoint Consolidation
**Proposed by**: Identity Architect
- Central Entra tenant for all regions
- SailPoint for IGA and provisioning
- 2-year implementation
- Cost: $450K platform + $300K implementation + $400K yr1 run = $1.15M
**Supports from**: DevOps (proven pattern), Product Specialist (strong market option)
**Questions from**: TSG ("Does single Entra work for regional governance?"), HR ("Talent hunt for SailPoint?")

### Path B: Two-Tenant Federated Entra
**Proposed by**: Identity Architect (alternative)
- West region Entra, East region Entra, federation between
- Okta for IGA (cheaper than SailPoint)
- 18 months implementation
- Cost: $350K platform + $250K implementation + $380K yr1 run = $980K
**Questions from**: IA ("Does federation meet SOC2 requirements?"), DevOps ("More complex monitoring")
**Supports from**: HR (Okta talent easier to find), ITD (lower cost)
```

- **Agent Usage**: Agents read this to understand all proposed options before Round 3 trade-off analysis.

#### Round 3: Trade-off Analysis
- **What agents input**: "Here's why my option is better OR acknowledges trade-offs"
- **Format**: Structured comparison with agent rationale
- **Example**:
```markdown
## Round 3: Trade-off Analysis

### Path A vs Path B Comparison

**Cost**: Path B wins ($980K vs $1.15M)
- ITD: "100K difference is real money"
- HR: "But Okta talent is easier to find; saves 20-30% on hiring premium"
- Net cost difference: ~$50K favors Path B

**Complexity**: Path A wins (single tenant simpler than federation)
- IA: "Single Entra = 50% less architecture burden"
- DevOps: "Federation adds ongoing sync complexity; ops risk for managed service"
- TSG: "Single Entra = simpler org messaging ('one identity system')"

**Timeline**: Path B wins (18 months vs 24 months)
- DevOps: "Federation implementation is faster because we're not rebuilding org"
- IA: "Yes, but MIM replacement is on critical path anyway"
- Net: Both deliver MIM replacement by MIM deadline (depends on Phase 1 overlap)

**Risk**: Path A rated lower risk
- PSG: "SailPoint more mature for enterprise IGA; Okta less proven at our scale"
- HR: "But Okta talent pool more stable; less vendor lock-in risk"
```

- **Agent Usage**: Agents read this to prepare for Round 4 recommendation decision. Data is fresh; no need to re-explain trade-offs.

#### Round 4: Consensus / Recommendation
- **What agents input**: "Here's my vote and rationale"
- **Format**: Agent vote + rationale + dissent (if any)
- **Example**:
```markdown
## Round 4: Recommendation Convergence

### Votes
- **Transformation Strategist**: Path A (simplicity + governance)
- **Identity Architect**: Path A (lower technical risk)
- **Product Specialist**: Path A (SailPoint proven at scale)
- **IT Director**: Path B (cost + timeline)
- **HR Advisor**: Path B (talent availability)
- **DevOps Practice Lead**: Path A (ops simplicity)

### Recommendation: Path A (Entra + SailPoint Consolidation)
**Rationale**: 4 agents support Path A. Cost difference ($50K) acceptable for reduced ops burden + lower risk.

**IT Director Dissent Summary**: 
"Path B saves $100K and shorter timeline. Cost-conscious org should prefer Path B. 
However, I accept Path A if org accepts $100K premium for risk reduction and ops simplicity."

**HR Advisor Dissent Summary**: 
"Okta talent easier to find, reducing hiring risk. However, I accept Path A if we raise 
SailPoint hiring budget to $150K regional premium."
```

- **Agent Usage**: Agents read this to confirm consensus or understand dissenting reasons. Enables quick alignment on why Path A won.

#### Round 5: Phase 1 Implementation Detail
- **What agents input**: "Here's what should be in Phase 1 given Path A"
- **Format**: Detailed phase breakdown with agent concerns
- **Example**:
```markdown
## Round 5: Phase 1 Implementation Detail

### Phase 1: Foundation (Months 1-6)
- Org governance design (TSG + ITD: "Establish regional council structure")
- MIM replacement planning (IA + PSG: "Select MIM replacement product")
- Entra infrastructure setup (IA + DevOps: "Design cloud infrastructure")
- SailPoint procurement (PSG: "Vendor selection, licensing deal")

### Phase 2: Pilot (Months 7-12)
- MIM replacement pilot (small region)
- Entra + SailPoint pilot (100 users)
- Integration testing
- Org messaging + training prep

**HR Concern**: "Phase 1-2 hiring must start month 2 (4-month ramp-up). 
Can we lock in SailPoint architect + Entra specialist by month 6?"
**Resolved**: "Yes, HR to begin hiring search month 2. Contractors fill gap if needed."

**DevOps Concern**: "Phase 1 focus on cloud infrastructure + SailPoint on-prem integration. 
Risk: Learning curve on SailPoint. Recommendation: Start SailPoint learning courses month 3."
**Resolved**: "Add $30K for training + external consultant Q3-Q4."
```

- **Agent Usage**: Agents read to confirm Phase 1 alignment before exec presentation. No surprises on scope.

#### Round 6: Risk Assessment
- **What agents input**: "Here are the top 5 risks I see"
- **Format**: Ranked risks with severity + mitigation
- **Example**:
```markdown
## Round 6: Risk Assessment

### Top 5 Risks (Consensus Ranking)

**Risk 1: Org Resistance to Centralized Identity (HIGH)**
- Owner: TSG
- Severity: HIGH (regions = 60% of headcount; trust in central IT is low)
- Mitigation: Executive sponsor + regional communication plan (adds 3 months Phase 0)

**Risk 2: MIM Replacement Complexity (HIGH)**
- Owner: IA
- Severity: HIGH (unknown factors in MIM migration from regional systems)
- Mitigation: Vendor pre-assessment; pilot with 1 region first

**Risk 3: Talent Acquisition Delay (MEDIUM)**
- Owner: HR
- Severity: MEDIUM (SailPoint + Entra talent tight market; 20-30% premium expected)
- Mitigation: Begin hiring month 2; explore international talent pools (Ireland + Canada)

**Risk 4: SailPoint Implementation Overrun (MEDIUM)**
- Owner: DevOps
- Severity: MEDIUM (SailPoint has learning curve; resource constraint if DevOps pulled elsewhere)
- Mitigation: Bring external integrator Q3-Q4 ($80K); protect team from BAU

**Risk 5: Budget Creep (MEDIUM)**
- Owner: ITD
- Severity: MEDIUM ($2.5M is tight; any overrun = phase delay)
- Mitigation: Monthly budget tracking; pre-approved contingency ($150K) for urgent items
```

- **Agent Usage**: Agents read to understand risk landscape. Phase 1 planning incorporates top 3 mitigations.

#### Round 7: Final Validation
- **What agents input**: "Final cross-check: Are we aligned? Does everything hold together?"
- **Format**: Agent validation statement + final concerns
- **Example**:
```markdown
## Round 7: Final Validation

### Transformation Strategist
"✅ Org transformation aligns with consolidation path. 
Regional councils structure proposed. Executive sponsorship plan solid.
Concern: Need 30-day org engagement campaign before Phase 1 kickoff."

### Identity Architect
"✅ Architecture holds together. MIM replacement + Entra + SailPoint are compatible.
Concern: MIM vendor not finalized. If chosen MIM doesn't integrate well with Entra, 
timeline could slip. Mitigated by Phase 1 vendor assessment task.
Final check: SailPoint supports regional AD sync? [Confirmed: Yes, distributed provisioning supported]"

### Product Specialist
"✅ SailPoint proven at scale. Licensing model supports our phased rollout.
Cost estimate validated with vendor. No surprises expected.
Concern: None. Proceeding with confidence."

### IT Director
"✅ Total cost $1.15M fits budget. Timeline realistic given MIM deadline.
Resource allocation aligns with HR plan.
Final decision: APPROVED. Escalate to executive steering committee."

### HR Advisor
"✅ Hiring plan is aggressive but achievable. 
Contingency: If SailPoint talent unavailable, switching to Path B (Okta) is still possible 
in Q1-Q2 (before Phase 1 ends). Cost difference allows pivot if needed.
Proceeding with Path A hiring; monitoring market."

### DevOps Practice Lead
"✅ Timeline, phasing, and technical approach realistic. 
Cloud infrastructure ready. Learning curve on SailPoint managed.
Concern: None. Ready for Phase 1."
```

- **Agent Usage**: Final check before business case production. Any last concerns surfaced here.

### 3. Decisions Memory (`/memories/session/decisions/`)

**Lifespan**: Current orchestration (referenced in final business case; archived after)

**Files**:
- `platform-choice.md` (Why Entra + SailPoint selected)
- `architecture-choice.md` (Single vs federated; consolidation approach)
- `org-model-choice.md` (Regional structure + central governance)
- `timeline-phases.md` (Phase 1-7 breakdown; critical path)
- `resource-plan.md` (Hiring, team structure, budget)
- `risk-mitigation-plan.md` (Top 5 risks + mitigations)

**Usage Pattern**:
```
Agent reads: /memories/session/decisions/platform-choice.md
Found: "Platform selected: Entra + SailPoint. Rationale: Lower ops risk, simpler architecture,
faster time to MIM replacement replacement. Cost premium $100K justified by risk reduction.
Dissenting view: IT Director preferred Okta (cost savings), but accepted trade-off."

Agent confirms: "Yes, architecture I proposed will work with Entra + SailPoint."
```

**Agent Usage**: During Phase 1 planning (Round 5), agents reference decisions to avoid 
reopening settled choices. Quick alignment on why decisions were made.

### 4. Repository Learning (`/memories/repo/pmo-learnings/`)

**Lifespan**: Persistent across all tasks

**Files**:
- `orchestration-patterns.md` (What worked for GISC-like initiatives)
- `common-conflicts.md` (Cost vs Timeline vs Quality trade-offs + historical resolution)
- `agent-preferences.md` (Which agents align by default; typical conflicts)

**Example Entry - orchestration-patterns.md**:
```markdown
## Pattern: Architecture + Hiring Conflicts

**When it happens**: Trade-off between "build simple fast" vs "build flexible slow"
**Typical agents involved**: Identity Architect (favors complexity for flexibility) 
vs DevOps (favors simplicity for ops) vs HR (favors simple for faster hiring)

**Resolution pattern**: 
1. Surface complexity cost upfront (Round 3)
2. Ask: "Does flexibility solve a real problem?" 
3. Default: Simpler architecture wins unless flexibility solves critical risk
4. Fallback: Phased simplicity (Phase 1 simple; Phase 2 add flexibility)

**Success rate**: 85% convergence in Round 4 using this pattern
```

**PMO Usage**: During Round 3-4, PMO references patterns to fast-track consensus on recurring conflicts.

**Agent Usage**: Rarely direct; PMO uses to facilitate. But agents can read to understand 
historical conflict patterns and pre-align.

## Agent Memory Access Rules

### What Each Agent Can Do

**All agents have**: `read`, `edit`, and `agent` tools

1. **Reading** (`read` tool):
   - Read any file in `/memories/session/` to understand context
   - Read any file in `/memories/repo/`  to reference historical patterns
   - Read outputs from prior agent inputs

2. **Writing** (`edit` tool):
   - Append their own perspective to current-round dialogue files
   - Update `/memories/session/decisions/` collaboratively during Round 4
   - Update `/memories/repo/pmo-learnings/` after task completion

3. **Calling Other Agents** (`agent` tool):
   - Request clarification from another agent on prior input
   - Example: "Identity Architect, I read your Round 2 proposal. Can you clarify how 
     federated Entra handles SOC2 compliance?"

### Memory Access Pattern During Orchestration

```
Round 1 Start:
  PMO writes: /memories/session/gisc-orchestration/problem-statement.md
  
Round 1 Mid:
  Agents read: problem-statement.md (all read once)
  Agents write: /memories/session/agent-dialogue/round-1-problem-understanding.md
  PMO consolidates: /memories/session/gisc-orchestration/assumptions.md and constraints.md

Round 2 Start:
  Agents read: round-1-problem-understanding.md + assumptions.md + constraints.md
  Agents write: /memories/session/agent-dialogue/round-2-brainstrom.md
  
Round 3:
  Agents read: round-2-brainstorm.md (all proposed options)
  Agents write: /memories/session/agent-dialogue/round-3-tradeoffs.md
  
Round 4:
  Agents read: round-3-tradeoffs.md (all analysis)
  Agents write+edit: /memories/session/decisions/ files (collaborative)
  Agents write: /memories/session/agent-dialogue/round-4-consensus.md

Rounds 5-7:
  Agents reference: /memories/session/decisions/ to avoid reopening settled choices
  Agents write: /memories/session/agent-dialogue/round-X-*.md
  
Completion:
  PMO consolidates: All memory files into final business case
  PMO updates: /memories/repo/pmo-learnings/ with new patterns + conflicts observed
```

## Best Practices

### For PMO

1. **Initialize memory at start**: Write problem-statement.md before invoking first round
2. **Consolidate after each round**: Update assumptions/constraints/decisions files
3. **Reference patterns**: Consult `/memories/repo/pmo-learnings/` before Round 3-4
4. **Archive at end**: Copy `/memories/session/` to project archive; keep learning in `/memories/repo/`

### For Agents

1. **Read before speaking**: Always read prior rounds before appending your input
2. **Reference decisions**: During Rounds 5-7, check `/memories/session/decisions/` for settled choices
3. **Acknowledge prior perspectives**: "I read TSG's concern about org resistance. Here's how Phase 1 addresses it..."
4. **Escalate conflicts**: If you disagree with a decision, write clearly to `/memories/session/agent-dialogue/` 
   so PMO can facilitate resolution in next round

### For Users (Those Invoking PMO)

1. **Provide clear problem statement**: More detail = better memory initialization
2. **Trust autonomous orchestration**: Let PMO read/write memory; you don't need to synthesize
3. **Review memory files**: Open `/memories/session/agent-dialogue/round-7-validation.md` 
   to understand full deliberation before accepting final output
4. **Preserve learning**: After task, PMO archives `/memories/repo/pmo-learnings/` 
   for improved orchestration on future initiatives

## Memory Clean-up

**After Task Completion**:
- Archive `/memories/session/` to project folder (document + date)
- Keep `/memories/repo/pmo-learnings/` intact for future reference
- Session files are not automatically cleared; user should manually delete if running same task again

**Example**:
```
/memories/repo/pmo-learnings/  (persist)
/memories/session/gisc-orchestration-20260324/  (archived to project)
  └── All round files, decisions, assumptions
```
