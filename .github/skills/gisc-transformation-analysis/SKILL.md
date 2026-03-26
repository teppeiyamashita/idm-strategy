---
name: GISC Transformation Analysis
description: 'Orchestrate comprehensive GISC transformation strategy, architecture, roadmap, and business case using multi-agent coordination'
targets: ['vscode']
suggestedModel: 'claude-sonnet-4.5'
---

# GISC Transformation Analysis Skill

## What This Skill Does

**Multi-agent collaborative orchestration** where ALL agents actively engage in dialogue, challenge assumptions, negotiate trade-offs, and iteratively refine deliverables to reach optimal solutions.

PMO Project Manager acts as facilitator/moderator:
- Ensures all agents participate in key decisions
- Resolves conflicts through structured discussion
- Tracks consensus and divergence
- Escalates blockers that require trade-offs
- Integrates final agreed-upon outputs

**This is NOT sequential analysis.** Agents continuously communicate, challenge each other, and refine their recommendations based on feedback from other specialists.

## Memory Integration

### Persistent Context Across Orchestration

All agents leverage a structured memory system to maintain and reference context:

**Session Memory** (`/memories/session/gisc-orchestration/`)
- Contains problem statement, constraints, assumptions
- Shared reference point for all agents
- Written once at start, read by all agents throughout

**Agent Dialogue Memory** (`/memories/session/agent-dialogue/`)
- One file per discussion round (round-1-problem-understanding.md through round-7-validation.md)
- Each agent appends their input/feedback after each round
- Agents read this to stay informed on all specialist perspectives
- Enables context continuity across hours of orchestration

**Decisions Memory** (`/memories/session/decisions/`)
- Platform choice, architecture choice, org model, timeline, resources
- Written collaboratively as agents reach consensus
- Consulted when agents need to ensure alignment with prior decisions
- Prevents reconsidering already-decided items

**Repository Learning** (`/memories/repo/pmo-learnings/`)
- Patterns that worked for GISC orchestration
- Common conflicts and historical resolution approaches
- Agent preference patterns
- Improves PMO's facilitation effectiveness on subsequent initiatives

### How Memory Enables Autonomous Orchestration

**Without Memory**: Agents would have no context on prior rounds, repeating discussions, losing decisions, reconsidering settled items.

**With Memory**: 
- Agents read prior rounds to build understanding incrementally
- PMO references common-conflicts patterns to fast-track resolution
- Decisions memory prevents flip-flopping on settled choices
- Agents confirm alignment by reading decisions memory
- Final business case is richer because memory documents HOW consensus was reached

## Agent Collaboration Model

### Continuous Multi-Directional Communication

Agents don't just provide outputs—they actively:
- **Challenge assumptions**: "Your timeline assumes X. Why not Y?"
- **Negotiate trade-offs**: "Phase 1 acceleration needs budget increase of $X or timeline extension of Y months"
- **Build on each other**: "Your architecture enables our IGA platform; our platform validates your architecture"
- **Seek alignment**: "Your org model supports this. Do you agree?"
- **Escalate conflicts**: "Architecture and cost are misaligned. Need trade-off decision"
- **Refine iteratively**: "Given new constraint, revised recommendation is..."

### Key Discussion Rounds (Not Sequential Stages)

**Round 1: Problem Understanding**
- TSG ↔ All Agents: "What's the actual constraint? Budget? Timeline? Org appetite?"
- All agents probe for hidden constraints and confirm scope
- *Output*: Shared understanding of the real problem

**Round 2: Solution Brainstorm**
- IA ↔ PS: "Which architecture-platform combos are viable?"
- IA ↔ TSG: "Does this architecture support org transformation?"
- DevOps ↔ IA: "How much effort for each option?"
- *Output*: 3-4 viable paths forward

**Round 3: Trade-off Analysis**
- ITD orchestrates comparison of paths
- ITD: "Path A costs $X, takes Y months, requires org change Z"
- HR: "We can hire for Path A. Path B requires different skillset—higher cost."
- DevOps: "Path B has more technical risk; Path A is proven pattern"
- TSG: "Org will accept Path A faster; Path B requires more change mgmt"
- *Output*: Clear pros/cons per path

**Round 4: Recommendation Convergence**
- All agents weigh in on preferred path
- If consensus: "Path A is recommended"
- If disagreement: "Path A preferred by X agents; Path B preferred by Y agents because..."
- *Output*: Recommendation with clear rationale and dissenting views documented

**Round 5: Implementation Planning (Agents Debate Details)**
- ITD: "Phase 1 should include X"
- PSG: "But org isn't ready; need foundation phase"
- HR: "Foundation phase is 3 months, puts us behind"
- DevOps: "Can we parallelize org + tech? Different teams?"
- TSG: "Org needs visibility/messaging before tech work starts. 1 month foundation minimum."
- ITD: "Propose: 1-month org foundation (parallel comms + planning), tech Phase 1 starts month 2"
- All agents: "✅ Acceptable"
- *Output*: Phase 1 plan reflects all constraints

**Round 6: Risk Validation (Pull No Punches)**
- TSG: "Biggest risk: org resistance to EUSP centralization"
- IA: "Second: MIM replacement complexity"
- HR: "Third: Finding engineers with Entra+SailPoint skillset in market"
- DevOps: "Fourth: Vendor performance if SailPoint implementation slips"
- ITD: "Fifth: Budget creep if scope expands"
- *Agents debate severity*:
  - HR: "Talent risk is MEDIUM—market has these skills, just pricey"
  - TSG: "Org risk is HIGH—regions will resist losing autonomy"
  - DevOps: "Vendor risk is MEDIUM with contract terms; mitigation: parallel development"
- *Output*: Agreed risk rankings + mitigation per risk

**Round 7: Final Validation & Consensus**
- PMO reads back final business case sections
- Each agent reviews own section AND cross-cuts from other agents
- IA reviews budget to verify cost aligns with scope
- HR reviews timeline to verify hiring can support plan
- DevOps reviews org model to confirm technical feasibility
- TSG reviews all to ensure coherence
- Agents voice any final concerns or objections
- PMO documents consensus and any lingering disagreements
- *Output*: Agreed business case with full audit trail of discussions

---

## How to Invoke

### Command Format

```
@PMO Project Manager, I need a comprehensive GISC transformation business case.

BUSINESS OBJECTIVE:
Transform fragmented Global Identity Service into unified, automated, 
self-service platform by 2028.

CONSTRAINTS:
- Budget: $2.5M maximum over 3 years
- Timeline: MIM must be replaced by 2029 (deadline-driven)
- Team: 2 engineers today, can grow to 5-6
- Fragmentation: 5 systems (AD, Entra, JPIDM, Passport, APIDM)

CONTEXT:
See STRATEGIC_BRIEFING_GISC_TRANSFORMATION.md for current analysis

SUCCESS CRITERIA:
1. Executive-ready business case (60-80 pages)
2. Clear Phase 1 go/no-go decision criteria
3. Detailed 12-month Phase 1 plan
4. All cost models internally consistent
5. Risk mitigation strategies identified

Proceed with autonomous orchestration.
```

### Alternative: Open-Ended Constraint Discovery

If you want agents to **discover** what constraints should be (rather than preset them):

```
@PMO Project Manager

Conduct a comprehensive GISC transformation diagnostic and constraint discovery:

BUSINESS OBJECTIVE:
Determine realistic transformation approach for GISC consolidation.
What are the actual constraints (budget, timeline, team, org appetite)?
What multiple scenarios should leadership consider?

DISCOVERY QUESTIONS:
1. What's the root cause of GISC fragmentation?
2. What would realistic transformation look like?
3. What budget would this require under different scenarios?
4. What timeline makes sense given complexity?
5. What team capacity is needed?
6. What organizational appetite exists?
7. What are trade-offs between conservative vs. aggressive paths?

CONTEXT:
See STRATEGIC_BRIEFING_GISC_TRANSFORMATION.md for current analysis

DELIVERABLE:
Executive briefing with findings and multiple strategic scenarios 
(conservative, moderate, aggressive) for leadership decision-making.

Proceed with autonomous 7-round orchestration. Let agents discover constraints.
```

### How Multi-Agent Orchestration Actually Works

**NOT just sequential analysis collection.** PMO coordinates ITERATIVE COLLABORATIVE DISCUSSION:

#### Phase 1: Initial Analysis (Sequential)
| # | Agent | Task | Output |
|---|-------|------|--------|
| 1 | PMO Manager | Clarify scope & constraints | Initiative definition |
| 2 | Transformation Strategist | Gap analysis, root causes, org model | Strategic assessment |
| 3 | Identity Architect | Design target architecture | Architecture proposal |
| 4 | Product Specialist | Evaluate platforms vs. architecture | Platform recommendations |
| 5 | IT Director | Prioritize initiatives, resource plan | Initiative backlog |
| 6 | HR Advisor | Team sizing, hiring plan | Org structure |
| 7 | DevOps Lead | Cost & timeline estimation | Budget & delivery |

#### Phase 2: Collaborative Refinement (Iterative Discussion)
Agents invoke each other for validation & alignment:

**Round 1 - Architecture Alignment**:
- Identity Architect → Transformation Strategist: "Does this architecture support your org model?"
- Transformation Strategist → Identity Architect: "Does this architecture enable unified EUSP ownership?"
- *Result*: Architecture refined to match org structure

**Round 2 - Product-Architecture Fit**:
- Product Specialist → Identity Architect: "Can Entra + SailPoint support your architecture?"
- Identity Architect → Product Specialist: "What gaps exist? What workarounds?"
- *Result*: Platform recommendation refined, gaps documented

**Round 3 - Initiative Feasibility**:
- IT Director → Transformation Strategist: "Can we sequence org changes per this roadmap?"
- Transformation Strategist → IT Director: "Are these phases realistic?"
- IT Director → DevOps: "Can we deliver Phase 1 in 6 months with this team?"
- DevOps → IT Director: "Timeline is tight; recommend Y for X or extend to Z months"
- *Result*: Roadmap refined for feasibility

**Round 4 - Resource Alignment**:
- IT Director → HR Advisor: "We need 3 engineers by month 6; is hiring realistic?"
- HR Advisor → IT Director: "Yes, but budget needs to be $X not $Y for quality"
- DevOps → HR Advisor: "If we use nearshore, does skill level match requirements?"
- HR Advisor → DevOps: "Yes, nearshore team can handle 60% of work with proper mentoring"
- *Result*: Team plan aligned with delivery needs

**Round 5 - Finance Reconciliation**:
- IT Director → DevOps: "Your $800K est. vs. Platform $450K vs. Team $500K = $1.75M total. Leaves $750K contingency—ok?"
- DevOps → IT Director: "Contingency is tight; recommend $100K buffer minimum"
- IT Director → HR Advisor: "Can we reduce first-year team cost by $50K?"
- HR Advisor → IT Director: "No; key specialists command market rate"
- *Result*: Budget finalized, contingency approved

**Round 6 - Risk Validation**:
- PMO Manager → All Agents: "What are the top execution risks?"
- Transformation Strategist: "Org resistance to EUSP centralization"
- Identity Architect: "MIM replacement complexity; legacy app dependencies"
- DevOps: "Delivery timeline aggressive with parallel workstreams"
- IT Director: "Vendor performance on customizations"
- *Result*: Top risks identified, mitigation strategies drafted

**Round 7 - Final Alignment** (All Agents):
- PMO Manager: "Review final business case sections—any contradictions or concerns?"
- Agents review integration and flag conflicts
- Conflicts resolved through discussion
- *Result*: All agents approve integrated business case

**Timeline**: 3-4 hours total (agents discussing AND analyzing, not just sequential)

## What You Get Back

### Final Deliverable: GISC_TRANSFORMATION_BUSINESS_CASE.md

```
60-80 page integrated document
├─ Executive Summary (2 pages)
│  ├─ Vision & strategic objective
│  ├─ Business case summary (ROI, payback, key metrics)
│  ├─ Recommended path forward
│  └─ Phase 1 go/no-go decision
├─ Current State Analysis (5 pages)
│  ├─ GISC gap analysis (ideal vs. reality)
│  ├─ Root cause diagnosis
│  └─ Financial impact quantification
├─ Future State Architecture (8 pages)
│  ├─ Consolidated platform design
│  ├─ Security & compliance framework
│  ├─ Integration & automation strategy
│  └─ Migration sequencing
├─ Implementation Roadmap (15 pages)
│  ├─ 3-year phased plan
│  ├─ Phase 1 detailed (12-month breakdown)
│  ├─ Initiative prioritization
│  └─ Critical path & dependencies
├─ Organizational & Resource Plan (10 pages)
│  ├─ EUSP team structure
│  ├─ Hiring & onboarding timeline
│  ├─ Regional sourcing strategy
│  └─ Resource cost breakdown
├─ Financial Analysis (8 pages)
│  ├─ 3-year budget by phase
│  ├─ Development cost detail
│  ├─ Platform & licensing costs
│  ├─ Team & resource costs
│  └─ ROI & payback analysis
├─ Risk & Mitigation (5 pages)
│  ├─ Execution risks
│  ├─ Mitigation strategies
│  └─ Contingency plans
├─ Success Factors & Metrics (3 pages)
│  ├─ Critical success factors
│  ├─ Phase go/no-go criteria
│  └─ Long-term success metrics
└─ Appendices (15+ pages)
   ├─ Architecture diagrams
   ├─ Platform comparison matrices
   ├─ Team org charts
   ├─ Detailed cost models
   └─ Market research & benchmarks
```

### Key Sections for Leadership Review

1. **Executive Summary** (print for boardroom)
   - 1-page recommendation
   - ROI and payback analysis
   - Phase 1 investment decision

2. **Phase 1 Detailed Plan** (operational execution)
   - 12-month timeline
   - Milestone delivery dates
   - Success/failure criteria
   - Resource requirements

3. **Risk & Mitigation** (due diligence)
   - 5 major risks identified
   - Mitigation per risk
   - Contingency budget

## Example Walkthrough

### Step 1: You Invoke PMO Manager
```
@PMO Project Manager, develop a comprehensive GISC transformation 
business case per attached strategic briefing. Budget limit $2.5M, 
MIM replacement required by 2029, current team of 2 engineers.
```

### Step 2: PMO Orchestrates (You Monitor Progress)

**Agents analyze AND actively collaborate to refine outputs:**

```
[PMO Manager] Orchestrating GISC transformation analysis...

========== PHASE 1: INITIAL ANALYSIS ==========

[Transformation Strategist] ✅ (15 min)
Gap analysis complete:
- Current: 5 fragmented systems, distributed ownership (Passport, EINS, regional)
- Ideal: Unified GISC under EUSP with 80% automation
- Root cause: Org misalignment preventing platform consolidation
- Org model: Unified EUSP ownership, strong governance authority

[Identity Architect] ✅ (20 min)
Architecture design:
- Core: Entra ID for modern auth, MFA
- IGA: SailPoint for lifecycle, access governance
- Federation: Regional federation for legacy system integration
- Migration: Phase users in waves, retire legacy platforms

[Transformation Strategist → Identity Architect] 🤝 COLLABORATION
TSG: "Does this arch support unified EUSP ownership and centralized governance?"
IA: "Yes. Entra + SailPoint puts all identity decisions with EUSP. No regional autonomy."
IA: "But this removes regional customization. Is org willing?"
TSG: "Customization is source of fragmentation. Centralized policy is required."
✅ ALIGNMENT: Architecture supports org structure

[Product Specialist] ✅ (20 min)
Platform evaluation:
- Evaluated: Entra+SailPoint vs. Okta vs. Auth0+Ping
- Recommendation: Entra + SailPoint (best Azure alignment, strong IGA)
- 3-year cost: $450K platform + $300K implementation

[Product Specialist → Identity Architect] 🤝 COLLABORATION
PS: "Entra + SailPoint supports your architecture, but has 3 gaps: [X], [Y], [Z]"
IA: "Are gaps critical or workarounds exist?"
PS: "Gap X: Critical—needs custom API. Gap Y: Workaround exists. Gap Z: Roadmap Q1 2027"
IA: "Custom API for Gap X adds ~$50K dev cost. Acceptable?"
✅ ALIGNMENT: Architecture refined; Gap X budgeted

[IT Director] ✅ (25 min)
Initiative prioritization:
- Phase 1 (0-6mo): Establish EUSP ownership, self-service password, DevOps automation
- Phase 2 (6-18mo): IGA platform implementation, JPIDM consolidation
- Phase 3 (18-36mo): MIM replacement, legacy system retirement
- Roadmap: 18 initiatives across 3 years

[IT Director → Transformation Strategist] 🤝 COLLABORATION
ITD: "Can org restructure happen in 6 months while we're executing Phase 1?"
TSG: "Org change should PRECEDE Phase 1, not concurrent. Recomm: 3-month foundation phase."
ITD: "But that delays tech work. Parallel phases needed for 3yr deadline."
TSG: "Parallel works if org structure change is announced immediately. Gives teams time to adapt."
✅ ALIGNMENT: Roadmap adjusted to parallel org+tech change

[IT Director → DevOps] 🤝 COLLABORATION
ITD: "Can we deliver Phase 1 in 6 months with current team?"
DevOps: "Current team of 2? Impossible. Need 3+ by month 3-4."
DevOps: "Timeline becomes 8 months with 2 engineers; 6 months with 3."
ITD: "HR must hire 1 engineer by month 3-4. Constraint noted."
✅ ALIGNMENT: Phase 1 timeline depends on hiring completion

[HR Advisor] ✅ (15 min)
Team planning:
- Phase 1: Grow from 2 → 3 engineers (Principal Arch + 2 Mid-Career)
- Source: 1 internal hire + 2 nearshore Mexico (cost-quality sweet spot)
- Cost: $500K Y1, $400K Y2, $300K Y3
- Timeline: 3-month hiring + 1-month onboarding = month 4 ready

[HR Advisor → IT Director] 🤝 COLLABORATION
HR: "Hiring 1 internal + 2 nearshore; can start month 1, ready month 4"
ITD: "Can we delay Phase 1 kickoff to month 4 for team readiness?"
HR: "Yes, but extends Phase 1 timeline to 9 months (month 4-13 not 4-9)"
ITD: "Unacceptable. Must complete Phase 1 by month 12 due to MIM deadline."
HR: "Then need to hire earlier or add temporary contractors for first 3 months."
✅ ALIGNMENT: Staffing plan adjusted; contractor budget added

[DevOps Practice Lead] ✅ (15 min)
Cost & timeline:
- Phase 1 development: $300K (self-service password + JPIDM DevOps)
- Phase 2 development: $350K (IGA platform implementation)
- Phase 3 development: $150K (MIM replacement + cleanup)
- Total dev: $800K
- Timeline: 30-month critical path
- Delivery acceleration: Claude Code for 40% of work = 2-month acceleration

========== PHASE 2: COLLABORATIVE REFINEMENT ==========

[IT Director ↔ All Agents] 🤝 FINANCE ALIGNMENT
ITD: "Cost reconciliation:
- Team: $500K (Y1)
- Platform: $150K (Y1)
- Development: $300K (Y1)
- Total Y1: $950K ✅ (under $1.2M budget)
- 3-year total: $2.15M ✅ (under $2.5M)"

All agents: "✅ Costs align with budget. No conflicts."

[Identity Architect ↔ Product Specialist] 🤝 ARCHITECTURE-PRODUCT FIT
IA: "Final architecture requires: Entra conditional access, SailPoint IGA, federated JPIDM"
PS: "All supported by Entra + SailPoint. No gaps beyond the 3 documented."
IA: "Acceptable. Custom API for Gap X adds $50K; embedded in dev budget."
✅ ALIGNMENT: Architecture-product fit confirmed

[Transformation Strategist ↔ IT Director] 🤝 ORG-TECH SEQUENCING
TSG: "Org model: Unified EUSP ownership established by month 6"
ITD: "Phase 1 requires: EUSP authority to approve policies, make platform decisions"
TSG: "This is part of org structure change. Happens months 1-3 in parallel with Phase 1 kickoff"
ITD: "Acceptable. Org readiness gates Phase 1 launch (month 4)"
✅ ALIGNMENT: Org change sequenced to precede Phase 1

[HR Advisor ↔ DevOps] 🤝 TEAM-DELIVERY FEASIBILITY
HR: "Team ready month 4: Principal Arch + 2 Mid-Career engineers"
DevOps: "Can deliver Phase 1 with this team? Tight but yes if AI tools accelerate dev."
HR: "AI acceleration—is this realistic with mid-career engineers?"
DevOps: "Yes. Claude Code handles 40% of boilerplate. Mid-career can do remaining 60%."
✅ ALIGNMENT: Team composition matches delivery needs

[PMO → All Agents] 🤝 RISK IDENTIFICATION
PMO: "Top execution risks?"
TSG: "Org resistance to centralization; regional teams give up autonomy"
IA: "Technical: MIM replacement complexity; legacy system integrations"
PS: "Vendor: SailPoint implementation delays; custom API complexity"
DevOps: "Schedule: Phase 1 timeline aggressive with parallel streams"
HR: "Talent: Mid-career engineers with Entra/SailPoint experience scarce"
ITD: "Budget: Contingency needed if scope creep on MIM replacement"
✅ RISKS DOCUMENTED: 7 major risks identified for mitigation

[All Agents → PMO] 🤝 FINAL ALIGNMENT
PMO: "Review integrated business case for conflicts..."
[All agents review sections]
TSG: "Org model supports architecture ✅"
IA: "Architecture supports product recommendations ✅"
PS: "Platforms align with architecture ✅"
ITD: "Priorities aligned with org+tech roadmap ✅"
HR: "Team resources match initiative needs ✅"
DevOps: "Budget aligns with effort estimates ✅"
✅ NO CONFLICTS: All agent outputs integrated seamlessly

========== PHASE 3: FINAL INTEGRATION ==========

[PMO Manager] ✅ (30 min)
Integrated business case created:
- Executive summary consistent across all inputs
- Roadmap reflects org+tech sequencing
- Budget reconciled (team + platform + dev + contingency)
- Risks documented with mitigation
- Phase 1 detail integrated (org readiness gates + tech work + hiring)

FINAL DELIVERABLE: GISC_TRANSFORMATION_BUSINESS_CASE.md (68 pages)
✅ All agents aligned
✅ No contradictions
✅ Ready for executive review
```

**Key Difference from Sequential Analysis:**
- Agents don't just analyze in isolation
- **Agents actively discuss and debate** each other's recommendations
- Conflicts are **resolved through negotiation**, not PMO dictation
- Final business case reflects **consensus across all specialists** with dissenting views documented
- Deliverable is **more robust** because validated from multiple perspectives
- **Audit trail shows reasoning** behind every recommendation

### Agent Dialogue Example (From Real Orchestration)

```
[Identity Architect] "I propose Entra + SailPoint. Costs $450K, addresses 90% of requirements"

[Product Specialist] "Wait—Okta could work too. Costs $400K, but requires custom integration"
[Product Specialist] "Should we evaluate both before deciding?"

[IT Director] "Cost difference is only $50K. What's the trade-off?"

[Identity Architect] "Okta requires 3 custom APIs. That's $75K dev work. Total: $475K."
[Identity Architect] "Entra + SailPoint is 'plug and play'; less risk."

[DevOps Practice Lead] "Agree. Fewer custom APIs means lower maintenance burden long-term."

[Product Specialist] "Fair. Recommend Entra + SailPoint then. No further debate needed."

[Transformation Strategist] "Before we close—does this architecture enable unified EUSP ownership?"

[Identity Architect] "Yes. All policy decisions flow through EUSP. No regional opt-outs."

[Transformation Strategist] "Good. That's critical for org consolidation."

[HR Advisor] "One concern: We need Entra + SailPoint expertise. Market is tight."

[Product Specialist] "SailPoint trained consultants available; not a blocker."

[HR Advisor] "Costs $200/hour for consulting. Budget impact?"

[IT Director] "Phase 1 implementation estimate already includes consulting. No additional cost."

[HR Advisor] "✅ Acceptable."

[DevOps Practice Lead] "I want to validate timeline. Entra rollout + SailPoint implementation: 12 weeks?"

[Identity Architect] "Assuming org is ready + requirements are stable: 10 weeks possible."

[DevOps Practice Lead] "Organization readiness: TSG, when can EUSP be ready to approve policies?"

[Transformation Strategist] "Month 3-4. Org restructuring takes 3 months; policy approval authority established month 4."

[DevOps Practice Lead] "That's tight. Recommend we start Phase 1 month 4, not month 3. Extra month for org stabilization."

[IT Director] "But MIM deadline is 2029. Can we absorb 1-month delay?"

[DevOps Practice Lead] "Yes. Critical path is 30 months; we have 36-month deadline."

[IT Director] "✅ Agreed. Phase 1 starts month 4."

[All Agents] ✅ CONSENSUS: Entra + SailPoint, $450K, Phase 1 month 4-16
- Risk: Low
- Confidence: High
- Rationale documented: Cost-quality trade-off validated across all perspectives
```

This is **how agents collaborate in this skill**: Not reporting independently, but actively debating, validating, and reaching **consensus-driven decisions**.

---

### Step 3: You Review & Present
- Open GISC_TRANSFORMATION_BUSINESS_CASE.md
- Print executive summary (2 pages) for board
- Use Phase 1 detail for implementation kickoff
- Reference risk section for stakeholder management

## Success Tips

### 1. Give PMO Enough Context
✅ Good: "Transform GISC per STRATEGIC_BRIEFING_GISC_TRANSFORMATION.md"
❌ Bad: "Fix our identity problems"

### 2. State Constraints Clearly
✅ Good: "Budget $2.5M max, MIM replacement by 2029, currently 2 engineers"
❌ Bad: "We're short on budget"

### 3. Set Success Criteria
✅ Good: "Exec-ready business case with go/no-go Phase 1 criteria"
❌ Bad: "Something that makes sense"

### 4. Let PMO Orchestrate Fully
✅ Good: "Proceed with autonomous orchestration"
❌ Bad: Interrupting after each agent with follow-up questions

## Typical Timeline

| Phase | Duration | Status |
|-------|----------|--------|
| Definition | 10 min | PMO clarifies scope |
| Orchestration | 90-120 min | 6 agents execute in sequence |
| Quality Review | 15 min | PMO validates each output |
| Integration | 30 min | PMO consolidates to final case |
| Delivery | 15 min | PMO packages and presents |
| **Total** | **3-4 hours** | Complete business case ready |

## What Makes This Different from Manual Orchestration

| Aspect | Manual (You Coordinate) | PMO-Led Orchestration (Skill) |
|--------|------------------------|------|
| Workflow | You invoke each agent sequentially | PMO invokes agents AND coordinates iterative discussion |
| Coordination | You gather outputs; agents don't talk to each other | Agents actively discuss and refine each other's work |
| Validation | You manually check for alignment | Agents validate and align through collaboration |
| Conflict resolution | You resolve contradictions | Agents resolve through discussion (documented) |
| Refinement | You request multiple passes | Agents refine iteratively until consensus |
| Quality gates | Manual review after each agent | PMO validates at multiple gates; agents provide feedback |
| Integration | You combine separate reports | Agents reach consensus; integrated output is single narrative |
| Turnaround | 5-6 hours (with rework cycles) | 3-4 hours (built-in refinement through collaboration) |
| Deliverable | Multiple separate analyses | One integrated business case with full audit trail |
| Risk | Outputs contradict; missed dependencies | Cross-validated by all agents; dependencies mapped |
| Robustness | Single perspective per domain | Multiple specialists have reviewed and approved |

## FAQs

**Q: Can I interrupt PMO during orchestration?**
A: Yes. If you spot an error, you can interrupt and request rework. PMO will flag issues at quality gates.

**Q: What if PMO and I disagree on something?**
A: PMO will document your concern and adjust analysis accordingly. You maintain final approval.

**Q: Can I ask PMO to focus on one area (just architecture)?**
A: Yes, but then you're not using the full orchestration skill. Use Identity Architect agent directly instead.

**Q: How much does this cost in tokens?**
A: ~40-50K tokens per full orchestration (analysis + integration). Compare to manual: ~50-60K (with thinking + rework).

**Q: Can I reuse the output?**
A: Yes. Export GISC_TRANSFORMATION_BUSINESS_CASE.md for presentations, board decks, implementation planning.

---

## Next Steps

1. **Review**: Read PMO_QUICK_START.md for detailed command format
2. **Prepare**: Gather your STRATEGIC_BRIEFING_GISC_TRANSFORMATION.md
3. **Invoke**: Use the command format above with your constraints
4. **Monitor**: Watch PMO orchestrate each agent (3-4 hours)
5. **Review**: Examine final business case and executive summary
6. **Present**: Use output for leadership decision-making
7. **Execute**: Use Phase 1 detail for implementation kickoff

---

**Get started**: @PMO Project Manager, I need a comprehensive GISC transformation business case...
