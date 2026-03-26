# When to Use: Transformation Strategist vs. IT Director

## Quick Decision Tree

```
Does your situation involve:
├─ Complex organizational structure changes?        → Use Transformation Strategist
├─ Fragmented ownership that blocks solutions?      → Use Transformation Strategist
├─ Root cause analysis across multiple systems?     → Use Transformation Strategist
├─ 3-year transformation roadmap planning?          → Use Transformation Strategist
├─ "Why are we stuck?" diagnosis needed?            → Use Transformation Strategist
│
├─ Specific build vs. buy decision for one app?     → Use IT Director
├─ Resource sourcing for known project?             → Use IT Director
├─ Cost-quality trade-off for specific solution?    → Use IT Director
├─ Vendor negotiation & contract terms?             → Use IT Director
├─ ROI analysis for specific initiative?            → Use IT Director
└─ "How do we execute this?" direction needed?      → Use IT Director
```

---

## Comparison: Transformation Strategist vs. IT Director

| Aspect | Transformation Strategist | IT Director |
|--------|--------------------------|-------------|
| **Focus** | System-wide transformation | Specific decision-making |
| **Scope** | 3-year vision, interconnected changes | Individual initiatives, prioritization |
| **Depth** | Root cause analysis, structural change | Cost-quality trade-offs, resource allocation |
| **Expertise** | Org structure, systemic thinking, change mgmt | Business case, build-vs-buy, vendor management |
| **Questions Asked** | "What must change to solve the root cause?" | "How do we execute this efficiently?" |
| **Outputs** | Transformation roadmap, org design, sequencing | Prioritized backlog, resource plan, ROI |
| **Timeline** | Multi-year strategy | Quarterly/annual planning |
| **Who Leads Decision?** | Transformation Strategist analyzes; IT Director approves based on resource/cost | IT Director decides within Strategist's framework |

---

## Example Scenarios

### Scenario 1: GISC Identity Service Dysfunction

**Symptom**: 7,784 L3 tickets/year, high partner operations cost

**Question**: "Why is this happening?"

**Correct Agent**: **Transformation Strategist**
- Analyzes: Fragmented ownership, multiple platforms, manual processes
- Diagnoses: Root cause is organizational fragmentation + technology misalignment
- Recommends: EUSP consolidates ownership, implements unified platform, phases out manual processes
- Proposes: 3-year roadmap with organizational changes + technical changes in parallel

**IT Director's Role**: Once Strategist clarifies direction, IT Director executes specific decisions (SailPoint vs. Okta? Source team internally or hire/nearshore?)

---

### Scenario 2: Should We Build or Buy MFA Management?

**Symptom**: We need MFA self-service capability

**Question**: "What's the best way to implement this?"

**Correct Agent**: **IT Director**
- Evaluates: Entra ID SSPR (buy) vs. custom solution (build)
- Compares: Cost, timeline, maintenance burden, team capacity
- Recommends: Buy Entra ID feature + custom wrapper (hybrid approach)
- Decides: Approve budget, source resources

**Transformation Strategist's Role**: Only needed if MFA management is symptom of deeper systemic issue (e.g., "We have MFA management scattered across platforms because ownership is fragmented")

---

### Scenario 3: Why Do We Have Multiple Identity Platforms?

**Symptom**: AD, Entra ID, JPIDM, Passport, APIDM all doing similar functions

**Question**: "Why is this, and what do we do about it?"

**Correct Agent**: **Transformation Strategist**
- Diagnoses: Historical decisions, regional autonomy, vendor relationships, technology gaps, organizational misalignment
- Identifies: Consolidation opportunity, but requires org structure change
- Proposes: Unified EUSP ownership, migrate all users to Entra ID + regional federation, retire ad-hoc platforms
- Sequences: Must establish EUSP ownership before consolidation works

**IT Director's Role**: After consolidation is approved, decides which platform (Entra + SailPoint? Okta?) and allocates resources

---

## How They Collaborate on GISC Transformation

**Transformation Strategist**:
1. Analyzes GISC gap (ideal vs. reality)
2. Diagnoses root causes (fragmented ownership, MIM sunset, org misalignment)
3. Proposes 3-year transformation roadmap
4. Identifies organizational changes needed
5. Sequences technical changes to support org changes

**IT Director** (with input from other agents):
1. Reviews Strategist's roadmap
2. Prioritizes initiatives within resource constraints
3. Makes specific decisions (buy vs. build for each component)
4. Allocates budget and resources per phase
5. Manages vendor negotiations
6. Ensures ROI and business alignment

**Together**:
- Transformation Strategist provides the "what and why"
- IT Director provides the "how and when within constraints"
- Other agents provide detailed input (cost, architecture, resources)

---

## Key Insight: Diagnosis vs. Execution

**Transformation Strategist** = **Diagnosis Phase**
- What's broken?
- Why is it broken?
- What needs to change (org, tech, process)?
- What's the sequencing?
- What are the interdependencies?

**IT Director** = **Execution Phase**
- How do we execute within our constraints?
- Which specific tools/platforms?
- What's the resource plan?
- What's the ROI?
- How do we prioritize?

If you're stuck in execution (can't seem to move forward, initiatives seem disconnected), you probably need **Transformation Strategist** first to diagnose the root cause before IT Director can execute effectively.

---

## Questions to Ask Each

### Ask Transformation Strategist:
- "Why do we have multiple identity platforms?"
- "What organizational changes are needed to fix this?"
- "What must we do first before we can move forward?"
- "How are these problems interconnected?"
- "What's the root cause of high L3 operational costs?"
- "What would ideal GISC operating model look like?"

### Ask IT Director:
- "Which platform should we choose: SailPoint or Okta?"
- "Should we build or buy this capability?"
- "What's the ROI for this initiative?"
- "How much budget and resources do we need?"
- "What's the priority order of these initiatives?"
- "Can we negotiate better vendor terms?"

---

## For GISC Transformation Planning

**Use this sequence**:

1. **Start with Transformation Strategist**: "Analyze the GISC gap and develop a transformation strategy"
   - Output: Gap analysis, root causes, org structure changes, 3-year roadmap, sequencing

2. **Then involve IT Director**: "Given the Strategist's roadmap, what's our execution plan?"
   - Output: Prioritized initiatives, build-vs-buy decisions, resource allocation, budget

3. **Then other agents dive deeper**: "Estimate effort for Phase 1 technical work," "Evaluate SailPoint vs. Okta," etc.
   - Output: Detailed cost/schedule/resources for each initiative

**Don't start with IT Director if the real problem is systemic.** You'll optimize execution of the wrong strategy.

---

*The Transformation Strategist helps you figure out the right transformation. The IT Director helps you execute it.*
