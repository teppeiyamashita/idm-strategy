# EUSP Engineering Team - Available Agents & Collaboration Guide

**Overview**: This document describes all available AI agents designed to support the EUSP Engineering Team's AI-driven IT operations efficiency initiative. Each agent brings specialized expertise to help teams make informed decisions and accelerate delivery.

---

## Available Agents

### 0. PMO Project Manager (Orchestrator) 🎯
**Specialty**: Multi-agent orchestration, project coordination, complex initiative management, business case development

**Use When**:
- You have a high-level business objective requiring multi-agent analysis and planning
- You need a complete business case, roadmap, and execution plan from a single task
- Coordinating work across multiple specialists (strategy, architecture, product, finance, people)
- Building comprehensive transformation initiatives (GISC, cloud migration, outsourcing decisions)
- Integrating outputs from multiple agents into cohesive deliverables

**Key Expertise**:
- Multi-agent workflow orchestration and sequencing
- Dependency management and critical path planning
- Business case development and ROI modeling
- Initiative decomposition and task allocation
- Quality gate review and deliverable integration
- Executive communication and reporting

**How to Use**:
Give PMO Manager ONE clear business objective. It autonomously orchestrates:
1. Transformation Strategist (strategy, org model)
2. Identity Architect (technical design)
3. Product Specialist - Identity (platform evaluation)
4. IT Director (prioritization, resource allocation)
5. Human Resources Advisor (team planning)
6. DevOps Practice Lead (cost/timeline estimation)

PMO Manager produces complete business case, roadmap, and Phase 1 execution plan (~3-4 hour turnaround).

**Example**: *"Develop a comprehensive GISC transformation business case including architecture, roadmap, team plan, and budget for executive review."*

---

### 0.5 Platform Expert
**Specialty**: Current-state knowledge of existing identity platforms — EINS, JPIDM, and Passport (SailPoint)

**Use When**:
- You need accurate, documented facts about what EINS, JPIDM, or Passport actually does today
- Comparing capabilities across existing platforms before designing a target architecture
- Understanding integration points, data flows, and platform boundaries
- Grounding strategy or architecture work in current-state reality
- Answering "what does the platform currently support?" questions

**Key Expertise**:
- EINS: Global ID authority, workforce attribute aggregation, population coverage, integration interfaces
- JPIDM: Japan self-service portal, AD/Exchange orchestration, technical stack (ASP.NET MVC 4 / VB.NET / SQL Server)
- Passport (SailPoint IdentityIQ): IAM orchestration for AM/EU/SM, FTE vs contingent worker flows, Workday integration
- Identity Landscape: Entra ID tenant composition (~250K objects), regional user distribution
- Cross-platform feature map and capability boundaries

**Scope Boundary**: Current-state only. Does not speculate about roadmaps or future capabilities.

**Knowledge Base**: `knowledge/platforms/` and `knowledge/Landscape.md`

**Typical Collaborators**: Identity Architect (feeds current-state into architecture design), Transformation Strategist (feeds current-state into gap analysis), Product Specialist - Identity (informs platform comparison), **Knowledge Manager** (maintains and updates the knowledge base itself)

---

### 0.6 Knowledge Manager
**Specialty**: CRUD operations on the knowledge base under `knowledge/`

**Use When**:
- Adding a new platform or system to the knowledge base
- Correcting a factual error in an existing knowledge file (e.g. wrong vendor name, outdated architecture)
- Updating platform documentation after a system change
- Checking what is and is not documented in the knowledge base
- Structuring new knowledge from raw notes, exports, or conversations into a proper knowledge file
- Keeping `IDENTITY_PLATFORM_FEATURE_MAP.md` in sync with individual platform files

**Key Expertise**:
- Knowledge base structure and file conventions
- Creating well-structured Markdown platform documentation
- Cross-file consistency (feature map vs. individual platform files)
- Identifying and flagging documentation gaps
- Safe deprecation/deletion of outdated content

**Scope**: `knowledge/` directory only. Does not modify strategy documents or agent files.

**Typical Collaborators**: Platform Expert (reads the knowledge base), all other agents (benefit from accurate knowledge)

---

### 1. Transformation Strategist
**Specialty**: Organizational transformation, systemic problem diagnosis, gap analysis, comprehensive change roadmaps

**Use When**:
- Analyzing complex organizational systems with interconnected problems
- Understanding root causes vs. symptoms
- Designing organizational restructuring alongside technical changes
- Planning multi-year transformation strategies
- Assessing feasibility of major structural changes (consolidation, ownership shifts)
- Sequencing initiatives where one change must precede another

**Key Expertise**:
- Gap analysis (ideal state vs. current state)
- Root cause analysis across organizational and technical systems
- Organizational structure and governance design
- Change management and sequencing strategies
- Parallel technical and organizational change planning
- Problem interdependency mapping

**Typical Collaborators**: Identity Architect (tech design), IT Director (resource/cost validation), DevOps Practice Lead (feasibility), HR Advisor (organizational change)

---

### 2. Identity Architect
**Specialty**: Identity and access management architecture, enterprise authentication, security design

**Use When**:
- Designing enterprise authentication strategies for multi-cloud environments
- Planning identity platform architecture and Zero Trust implementation
- Solving IAM governance and compliance challenges
- Designing API security and service-to-service authentication
- Implementing conditional access policies and access controls

**Key Expertise**:
- Zero Trust security principles and implementation
- Multi-tenancy and federation patterns
- RBAC and ABAC design
- Compliance frameworks (SOC 2, ISO 27001, HIPAA)
- Azure AD/Entra ID, Active Directory integration

**Typical Collaborators**: Product Specialist - Identity, IT Director, Transformation Strategist

---

### 3. Product Specialist - Identity
**Specialty**: Identity product research, vendor comparison, licensing, procurement

**Use When**:
- Evaluating identity platforms (Entra ID, Okta, SailPoint, Ping, Auth0, JumpCloud)
- Comparing licensing models and TCO across vendors
- Creating RFP requirements for identity solutions
- Assessing feature gaps and fit for specific use cases
- Negotiating vendor contracts and pricing

**Key Expertise**:
- Deep product knowledge: Entra ID, Okta, SailPoint, Ping Identity, JumpCloud, Auth0, Active Directory
- Feature comparison and strengths/weaknesses analysis
- Licensing models (per-user, per-identity, consumption-based)
- Integration capabilities and ecosystem breadth
- Implementation effort and time-to-value assessment

**Typical Collaborators**: Identity Architect, IT Director, Human Resources Advisor

---

### 4. IT Director
**Specialty**: IT service delivery strategy, cost-quality optimization, build-vs-buy decisions, resource sourcing

**Use When**:
- Deciding between custom development vs. commercial solutions
- Evaluating offshore/nearshore/onshore team composition
- Optimizing IT spending while maintaining service quality
- Planning geographic expansion of IT operations
- Creating financial business cases for IT investments
- Balancing competing priorities (cost, quality, time-to-market)

**Key Expertise**:
- Total cost of ownership (TCO) analysis and ROI calculation
- Build vs. Buy decision frameworks
- Global resource sourcing strategies (US, India, Mexico, Japan)
- Cost-quality trade-off analysis
- Service delivery management and SLA optimization
- Vendor management and partnership strategy

**Typical Collaborators**: DevOps Practice Lead, Human Resources Advisor, Product Specialist - Identity, PMO Project Manager

---

### 5. Human Resources Advisor
**Specialty**: IT job market research, talent sourcing, regional cost-quality analysis, skill availability

**Use When**:
- Determining optimal geographic team distribution
- Benchmarking compensation packages by region
- Identifying skill gaps and sourcing strategies
- Evaluating cost-quality trade-offs for IT roles
- Planning offshore/nearshore/onshore team expansion
- Forecasting talent availability for future roadmap
- Assessing team stability and retention risk

**Key Expertise**:
- Regional cost analysis: US Southwest, Mexico, Bangalore, Tokyo, Dalian
- Role-specific pricing (Junior/Mid/Senior Engineers, DevOps, QA, etc.)
- Market characteristics: talent quality, communication, timezone, specialization
- Sourcing models: FTE, contractor, agency, outsourcing partnerships
- Skill availability and scarcity analysis
- Retention strategies and competitive compensation

**Typical Collaborators**: IT Director, DevOps Practice Lead, PMO Project Manager

---

### 6. DevOps Practice Lead
**Specialty**: Custom solution development cost estimation, timeline planning, AI-assisted development, delivery optimization

**Use When**:
- Estimating development cost and timeline for custom solutions
- Planning team composition and resource allocation
- Assessing impact of AI-assisted development (GitHub Copilot, Claude Code)
- Forecasting total cost of ownership for 3-5 year horizon
- Planning phased delivery roadmap with budget allocation
- Evaluating build vs. buy with cost impact
- Identifying cost drivers and optimization opportunities
- Assessing technical debt and refactoring needs

**Key Expertise**:
- Feature complexity scoring and estimation models
- AI-assisted development productivity (30-50% time savings)
- Development cost and timeline forecasting
- Team composition and sizing for projects
- Technology stack impact on cost and timeline
- QA/testing cost estimation (50-85% of development)
- Multi-project and scaled delivery models
- Contingency and risk-adjusted planning

**Typical Collaborators**: IT Director, Human Resources Advisor, PMO Project Manager

---

## Typical Collaboration Workflows

### Workflow 0: PMO-Led Multi-Agent Orchestration (Recommended)
**Entry Point**: PMO Project Manager (receives ONE single user task)

**PMO Orchestrates Sequential Execution**:
1. **PMO Manager** → Clarify scope, define success criteria, establish timeline
2. **Transformation Strategist** → Gap analysis, root cause, org structure recommendations
3. **Identity Architect** → Target architecture design (depends on Strategist's org model)
4. **Product Specialist - Identity** → Platform evaluation and TCO (depends on Architecture)
5. **IT Director** → Prioritize initiatives, build-vs-buy, resource allocation (depends on all above)
6. **Human Resources Advisor** → Team planning, hiring strategy (depends on IT Director)
7. **DevOps Practice Lead** → Cost estimation, timeline, delivery acceleration (depends on IT Director)
8. **PMO Manager** → Integrate all outputs, create unified business case, executive summary

**Typical Use**: GISC Transformation, Cloud Migration, Outsourcing Decisions, Digital Transformation

**User Task Format**: @PMO Project Manager, develop a comprehensive business case for GISC transformation with scope [X], constraints [Y], timeline [Z]

**Deliverable**: 60-80 page integrated business case with executive summary, 3-year roadmap, resource plan, Phase 1 detail, all financial models aligned

**Timeline**: 3-4 hours from single user request to complete deliverable

---

### Workflow 1: Complex Organizational Transformation (Manual Step-by-Step)
Used when user wants to coordinate agents individually rather than through PMO orchestration

1. **Transformation Strategist** → Analyze gap, diagnose root causes, propose org structure changes
2. **Identity Architect** → Design target technical architecture
3. **IT Director** → Evaluate resource/cost implications, prioritize initiatives
4. **DevOps Practice Lead** → Estimate effort/cost/timeline for technical work
5. **Human Resources Advisor** → Plan org change, resource transitions
6. **User**: Synthesize into comprehensive transformation roadmap

### Workflow 1: New IT Solution Evaluation (Build vs. Buy)
1. **DevOps Practice Lead** → Estimate custom development cost, timeline, and effort
2. **Product Specialist - Identity** → Research commercial solutions, pricing, licensing
3. **IT Director** → Compare options, create business case, make build-vs-buy decision
4. **Collaborator**: Human Resources Advisor (if resource sourcing needed)

### Workflow 2: Identity Platform Migration Planning
1. **Identity Architect** → Design target identity architecture and strategy
2. **Product Specialist - Identity** → Evaluate platforms, recommend vendors
3. **IT Director** → Assess cost, resource impact, risk mitigation
4. **DevOps Practice Lead** → Estimate implementation cost and timeline
5. **Human Resources Advisor** → Plan resource acquisition if needed

### Workflow 3: Global Team Expansion & Resource Sourcing
1. **IT Director** → Define scope, budget, and quality requirements
2. **Human Resources Advisor** → Analyze markets, provide cost-quality recommendations
3. **DevOps Practice Lead** → Estimate project effort and resource needs
4. **Collaborator**: Product Specialist - Identity (if identity infrastructure development needed)

### Workflow 4: Custom Solution Development Planning
1. **IT Director** → Define business requirements and success criteria
2. **DevOps Practice Lead** → Estimate effort, cost, timeline, team composition
3. **Human Resources Advisor** → Source team members or allocate existing resources
4. **Identity Architect** → Handle any IAM architecture needs
5. **Product Specialist - Identity** → Evaluate identity components if needed

### Workflow 5: Cost Optimization Initiative
1. **IT Director** → Identify optimization opportunities
2. **DevOps Practice Lead** → Analyze custom solutions for cost reduction
3. **Product Specialist - Identity** → Evaluate licensing optimization for identity platforms
4. **Human Resources Advisor** → Assess team composition and sourcing efficiency
5. **Identity Architect** → Design efficient, compliant identity solutions

---

## Agent Selection Guide

### Question: "I have a complex business initiative requiring strategy, architecture, planning, and business case"
→ **PMO Project Manager** (orchestrates ALL agents; gives you complete business case output)

### Question: "What does EINS / JPIDM / Passport actually do today?"
→ **Platform Expert** (authoritative current-state platform knowledge)

### Question: "Add / update / fix something in the knowledge base?"
→ **Knowledge Manager** (CRUD operations on `knowledge/`)

### Question: "What is or isn't documented in our knowledge base?"
→ **Knowledge Manager** (reads and reports on knowledge base coverage)

### Question: "How do we solve this complex organizational & technical problem?"
→ **Transformation Strategist** (diagnoses root causes and org structure needs)

### Question: "How should we build our identity solution?"
→ **Identity Architect** (with Product Specialist - Identity for vendor evaluation)

### Question: "What will this project cost and take to deliver?"
→ **DevOps Practice Lead** (with IT Director for strategic context and approval)

### Question: "Should we build or buy identity platform?"
→ **IT Director** (with DevOps Practice Lead, Product Specialist - Identity for analysis)

### Question: "Which identity product fits our needs best?"
→ **Product Specialist - Identity** (with Identity Architect for technical validation)

### Question: "How do we staff this initiative cost-effectively?"
→ **IT Director** (with Human Resources Advisor for talent sourcing and regional analysis)

### Question: "Where should we source development talent?"
→ **Human Resources Advisor** (with IT Director for budget/timeline trade-offs)

### Question: "What's the total cost of ownership for a 3-year solution?"
→ **DevOps Practice Lead** + **IT Director** (complete cost modeling)

### Question: "How do we balance cost and quality in delivery?"
→ **IT Director** (with DevOps Practice Lead and Human Resources Advisor)

---

## Collaboration Best Practices

### 1. Frame Your Question Clearly
- Provide context: business objective, constraints, success criteria
- Specify the decision you're trying to make
- Share known constraints: budget, timeline, quality requirements

### 2. Engage Agents in Sequence
- Start with the primary agent for your domain
- Ask them to recommend other agents if needed
- They will suggest collaborators for complex decisions

### 3. Share Key Findings
- Communicate assumptions across agents
- Ensure consistency in cost estimates and timelines
- Validate calculations and recommendations across specialties

### 4. Build on Results
- Use one agent's output as input to the next
- Save detailed analyses for future reference
- Document decisions and rationale for auditability

### 5. Cross-Validate Complex Decisions
- For major decisions, get input from 2-3 agents
- Ensure different perspectives are captured
- Identify and reconcile any disagreements

---

## Key Decision Areas & Agent Involvement

| Decision | Primary Agent | Supporting Agents | Output |
|----------|---------------|-------------------|--------|
| Build vs. Buy | IT Director | DevOps, Product Specialist | Business case, ROI analysis |
| Architecture Design | Identity Architect | Product Specialist | Design document, standards |
| Platform Selection | Product Specialist | Identity Architect, IT Director | Vendor assessment, recommendation |
| Cost Estimation | DevOps Practice Lead | IT Director | Budget, timeline, risk assessment |
| Team Allocation | Human Resources Advisor | IT Director, DevOps | Sourcing plan, cost impact |
| Migration Planning | Identity Architect | DevOps, Product Specialist, IT Director | Migration roadmap, effort estimate |
| Quality Strategy | DevOps Practice Lead | Identity Architect | Testing plan, quality gates |

---

## Glossary of Agent Perspectives

- **Identity Architect**: "This is the secure, scalable way to solve the problem"
- **Product Specialist**: "Here's how commercial solutions address this, and their trade-offs"
- **IT Director**: "Here's how this contributes to business value while managing cost and risk"
- **Human Resources Advisor**: "Here's the talent market perspective and resource implications"
- **DevOps Practice Lead**: "Here's the effort required, timeline, and delivery approach"

---

## Getting Started

### For a Simple Question
Contact the single agent that best matches your question (see Agent Selection Guide above)

### For a Complex Decision
1. Start with the primary agent for your domain
2. Let them recommend collaborators
3. Engage those agents in sequence
4. Review recommendations with IT Director for final approval

### For Strategic Planning
Engage IT Director to orchestrate a multi-agent panel covering:
- Architecture perspective (Identity Architect)
- Product perspective (Product Specialist)
- Delivery perspective (DevOps Practice Lead)
- Resource perspective (Human Resources Advisor)

---

## Notes

- All agents follow the EUSP Engineering Team's principle of **AI-driven orchestration with strategic human leadership**
- Agents are trained on industry standards, best practices, and market intelligence
- Estimates and recommendations should be validated when making major commitment decisions
- Agents support the team's core mission: **transform EUSP IT operations through intelligent, efficient, AI-powered solutions**

---

*Created for EUSP Engineering Team's AI-driven IT operations efficiency initiative*
