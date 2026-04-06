# Agent Scenario Examples

This directory contains concrete walkthroughs showing how the multi-agent system handles real investigation scenarios.

## Available Examples

### DLP Alert Investigation (Healthcare)
**Scenario:** CSA needs DLP investigation prompts for a healthcare customer dealing with PHI exfiltration alerts.

**Agent Flow:**
1. Orchestrator receives: "Generate DLP investigation prompts for healthcare customer with high-sensitivity patient data"
2. Purview Knowledge Agent confirms SC can triage DLP alerts and zoom into data risk
3. SC Prompt Engineer generates healthcare-specific prompts with HIPAA context
4. Investigation Playbook Agent structures prompts into incident response workflow
5. Quality Reviewer validates all prompts against real SC capabilities
6. Output: Promptbook sequence + investigation playbook + healthcare talking points

### IRM Case Triage (Financial Services)
**Scenario:** CSA needs to demonstrate IRM case management to a banking customer's security operations team.

**Agent Flow:**
1. Orchestrator receives: "Create IRM triage demo for financial services SOC team"
2. Purview Knowledge Agent confirms user risk summary and zoom capabilities
3. SC Prompt Engineer generates risk-scoring prompts with financial context
4. Workshop Agent creates safe demo script with sanitized data
5. Quality Reviewer validates demo prompts won't expose real data
6. Output: Workshop demo script + hands-on lab exercises + customer deck outline

### Cross-Signal Investigation (Technology)
**Scenario:** Investigating a departing employee at a tech company who triggered both DLP and IRM alerts.

**Agent Flow:**
1. Orchestrator receives: "Generate cross-signal investigation workflow for departing employee with DLP + IRM alerts"
2. Purview Knowledge Agent confirms cross-signal correlation requires data sharing enabled
3. SC Prompt Engineer generates combined DLP + IRM prompts
4. Investigation Playbook Agent creates timeline reconstruction workflow with escalation points
5. Quality Reviewer flags that audit log lag may affect timeline accuracy
6. Output: 10-step investigation chain + decision tree + escalation criteria

---

**See also:** [architecture.md](../architecture.md) Section 3 for topology details.
