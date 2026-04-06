# Backlog and Future Work

This document organizes work items by urgency and timeline. Items are prioritized based on impact, dependencies, and roadmap alignment.

---

## Immediate (Next 2 Weeks)

Work items that unblock other efforts or are critical for Phase 1-2 success.

1. **Create core architecture decision document**
   - Outline SC + Purview integration points
   - Identify agent topology options
   - Dependencies: None
   - Owner: [Architecture lead]

2. **Document initial use case catalog (5 scenarios)**
   - DLP incident investigation
   - IRM permission management
   - DSPM data discovery
   - Sensitive data remediation
   - Policy exception workflow
   - Dependencies: Architecture doc
   - Owner: [Product owner]

3. **Establish repository structure and conventions**
   - Directory layout for prompts, playbooks, use cases
   - Naming conventions
   - Template system
   - Dependencies: Architecture doc
   - Owner: [Repo maintainer]

4. **Build prompt template and documentation standard**
   - Metadata fields
   - Testing and validation approach
   - Quality checklist
   - Dependencies: None
   - Owner: [Content lead]

5. **Identify and access key data sources**
   - Purview DLP reports
   - IRM policy configurations
   - DSPM insights
   - Security Copilot test environment
   - Dependencies: Stakeholder approvals
   - Owner: [Data access lead]

6. **Create contributing guidelines and review process**
   - PR workflow
   - Quality standards
   - Review SLA
   - Dependencies: Repo structure
   - Owner: [Repo maintainer]

7. **Schedule SME interviews for prompt book validation**
   - DLP experts (3-5 interviews)
   - IRM architects (2-3 interviews)
   - DSPM analysts (2-3 interviews)
   - Dependencies: None
   - Owner: [Project coordinator]

---

## Short-Term (1-3 Months)

Core Phase 2-3 work: building content and validating against real SC.

1. **Build DLP prompt book (30-40 prompts)**
   - Incident detection and triage
   - Impact assessment
   - Policy analysis and remediation
   - Reporting and compliance
   - Dependencies: SME interviews complete
   - Effort: 40-60 hours
   - Owner: [DLP lead]

2. **Build IRM prompt book (20-30 prompts)**
   - Permission analysis and review
   - Role inheritance evaluation
   - Access request processing
   - Exception management
   - Dependencies: SME interviews complete
   - Effort: 30-40 hours
   - Owner: [IRM lead]

3. **Build DSPM prompt book (15-20 prompts)**
   - Data discovery and classification
   - Inventory analysis
   - Risk assessment
   - Remediation tracking
   - Dependencies: SME interviews complete
   - Effort: 20-30 hours
   - Owner: [DSPM lead]

4. **Develop 4 investigation playbooks**
   - "Investigate DLP Incident" playbook
   - "Review IRM Policy Compliance" playbook
   - "Assess DSPM Risk" playbook
   - "Respond to Sensitive Data Exposure" playbook
   - Dependencies: DLP, IRM, DSPM prompt books
   - Effort: 50-70 hours
   - Owner: [Playbook author]

5. **Create prompt taxonomy and discovery guide**
   - Categorize all prompts by workflow stage
   - Build decision tree for prompt selection
   - Create quick-reference guides
   - Dependencies: All prompt books complete
   - Effort: 15-20 hours
   - Owner: [Content lead]

6. **Test prompts against real Security Copilot (Phase 3)**
   - Execute each prompt against live SC
   - Document actual outputs and quirks
   - Validate against expected behavior
   - Dependencies: SC test environment, Phase 2 complete
   - Effort: 60-80 hours
   - Owner: [QA/Validation team]

7. **Gather and incorporate SME feedback**
   - Review sessions with 3-5 SMEs
   - Iterate on prompt quality
   - Validate playbooks with real analysts
   - Dependencies: All content created
   - Effort: 20-30 hours
   - Owner: [Product owner]

8. **Create internal documentation and training**
   - How to use the repo guide
   - Contributing guide (detailed version)
   - SC + Purview integration primer
   - Dependencies: Content stabilized
   - Effort: 15-20 hours
   - Owner: [Documentation lead]

9. **Document SC plugin limitations and gaps**
   - Identify what SC cannot do in Purview context
   - Document workarounds and manual steps
   - Create issues for SC product team
   - Dependencies: Phase 3 testing complete
   - Effort: 10-15 hours
   - Owner: [Validation team]

10. **Build output example library**
    - Capture real SC responses from testing
    - Screenshot and annotate key examples
    - Create pattern reference guide
    - Dependencies: Phase 3 testing
    - Effort: 15-20 hours
    - Owner: [Content lead]

---

## Medium-Term (3-6 Months)

Phase 4-5 work: agents, automation, and deeper integrations.

1. **Design multi-agent topology (Phase 4)**
   - Orchestrator agent (routing, context)
   - DLP Investigator agent (incident response)
   - Compliance Reviewer agent (policy validation)
   - Responder agent (optional, depends on scope)
   - Dependencies: Phase 3 complete
   - Effort: 30-40 hours
   - Owner: [Architecture lead]

2. **Develop agent system prompts and configurations**
   - Orchestrator system prompt
   - Investigator system prompt with DLP focus
   - Agent coordination protocol
   - Error handling and fallback logic
   - Dependencies: Agent topology design
   - Effort: 30-40 hours
   - Owner: [Agent lead]

3. **Build and test agent prototype locally**
   - Claude Code / Agent SDK setup
   - Agent communication framework
   - Test with 3-5 scenarios
   - Document learnings and constraints
   - Dependencies: Agent system prompts, Claude Code access
   - Effort: 40-60 hours
   - Owner: [Engineering lead]

4. **Create Claude Code configuration templates**
   - Orchestrator configuration
   - Agent environment setup
   - Prompt injection and safety guardrails
   - Dependencies: Agent prototype working
   - Effort: 15-20 hours
   - Owner: [Engineering lead]

5. **Map Purview API landscape and integrations**
   - Document available APIs (Data Classification, DLP, IRM, DSPM)
   - Identify Graph API integration points
   - Assess API maturity and limitations
   - Create integration design patterns
   - Dependencies: None
   - Effort: 20-30 hours
   - Owner: [Architecture lead]

6. **Design automation prototypes with Logic Apps**
   - Alert-triggered investigation workflow
   - Scheduled compliance review workflow
   - Exception request processing workflow
   - Dependencies: API landscape mapped
   - Effort: 30-40 hours
   - Owner: [Automation lead]

7. **Build MCP server for Purview API access**
   - Design MCP interface for Purview APIs
   - Implement core endpoints (DLP incidents, IRM policies, DSPM risks)
   - Document MCP server API
   - Build proof-of-concept
   - Dependencies: Purview APIs mapped
   - Effort: 50-70 hours
   - Owner: [Backend engineer]

8. **Test agent workflows end-to-end (Phase 4)**
   - Multi-agent investigation scenario
   - Multi-step decision workflow
   - Error recovery and escalation
   - Performance and latency
   - Dependencies: Agent prototype, Logic App prototypes
   - Effort: 30-40 hours
   - Owner: [QA/Testing team]

9. **Document cost and licensing implications**
   - SC pricing and token costs
   - Purview licensing requirements
   - Claude usage and pricing
   - Logic Apps and automation costs
   - Dependencies: Prototypes built
   - Effort: 10-15 hours
   - Owner: [Finance liaison]

10. **Gather customer feedback on framework**
    - Engage 2-3 internal customers
    - Run discovery workshops
    - Document feature requests
    - Dependencies: Phase 3 and early Phase 4 complete
    - Effort: 20-30 hours
    - Owner: [Product owner]

11. **Build video walkthroughs**
    - Using the prompt library
    - Running a DLP investigation playbook
    - Multi-agent orchestration in action
    - Agent API integration
    - Dependencies: Agents and agents operational
    - Effort: 30-40 hours
    - Owner: [Content/Video producer]

12. **Create feedback collection mechanism**
    - GitHub issue templates for feedback
    - Internal survey for usability feedback
    - Monthly feedback review cadence
    - Dependencies: Repo shared internally
    - Effort: 5-10 hours
    - Owner: [Product owner]

---

## Long-Term / Aspirational (6+ Months)

Strategic initiatives and customer-facing deliverables.

1. **Build customer-facing workshop kit**
   - Slide deck (overview, use cases, hands-on)
   - Lab environment setup guide
   - Attendee workbook
   - Facilitator guide
   - Dependencies: Framework mature, stable
   - Effort: 50-70 hours
   - Owner: [Customer success lead]

2. **Design feedback loop and continuous improvement process**
   - Quarterly review cycle for updates
   - Automated testing and validation pipeline
   - SC product alignment process
   - Owner escalation and governance
   - Dependencies: Phase 6 complete
   - Effort: 15-20 hours
   - Owner: [Product owner]

3. **Develop advanced agent capabilities**
   - Explain-ability and reasoning transparency
   - Confidence scoring and uncertainty handling
   - Multi-turn conversational workflows
   - Context and memory management
   - Dependencies: Basic agents mature
   - Effort: 60-80 hours
   - Owner: [Advanced engineering]

4. **Build integration marketplace**
   - Document partner and ISV integration patterns
   - Create reference implementations
   - Build certification program
   - Dependencies: Core framework mature
   - Effort: 40-60 hours
   - Owner: [Ecosystem lead]

5. **Extend to other Purview modules**
   - Compliance Manager prompts and playbooks
   - Audit and Activity Log prompts
   - Data Subject Request (DSR) workflows
   - Dependencies: DLP/IRM/DSPM mature
   - Effort: 80-100 hours
   - Owner: [Product team]

6. **Create open-source community edition**
   - Determine licensing and community governance
   - Build contribution workflow
   - Launch community forum
   - Dependencies: Framework proven and stable
   - Effort: 30-50 hours
   - Owner: [DevRel/Community lead]

---

## Backlog Notes

- **Dependencies:** Items are listed with dependencies; prioritize blocking items first
- **Effort Estimates:** Based on typical full-time contributor; adjust based on team capacity
- **Ownership:** Assign clear owners to each item to ensure accountability
- **Review Cadence:** Review backlog weekly during Phase 1-3, bi-weekly during Phase 4-5
- **Flexibility:** Backlog is subject to change based on SC product updates, customer feedback, and discovered blockers
