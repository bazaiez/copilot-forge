# Agent Definition Template

Use this template when defining a new agent in the multi-agent system. This captures the agent's role, capabilities, system prompt, and integration points.

---

## Metadata

**Agent ID:** [e.g., agent-orchestrator, agent-dlp-investigator]
**Agent Name:** [Human-readable name]
**Version:** [e.g., 1.0]
**Created Date:** [YYYY-MM-DD]
**Last Updated:** [YYYY-MM-DD]
**Status:** [Design | Development | Testing | Production]

---

## Overview

### Purpose
[What is the primary purpose of this agent? What problem does it solve?]

### Role in Architecture
[How does this agent fit into the larger multi-agent system?]

---

## Agent Type and Classification

**Classification:** [Orchestrator | Investigator | Responder | Analyst | Other]

**Input Types:**
- [Input type 1: e.g., "User query in natural language"]
- [Input type 2: e.g., "Structured incident data from event system"]

**Output Types:**
- [Output type 1: e.g., "Investigation report in JSON"]
- [Output type 2: e.g., "Recommended actions with reasoning"]

---

## Core Responsibilities

[What specific tasks is this agent responsible for? Be concrete.]

1. [Responsibility 1]
2. [Responsibility 2]
3. [Responsibility 3]
4. [Responsibility 4]

---

## System Prompt

[The foundational system prompt that defines the agent's behavior, constraints, and approach.]

```
[Full system prompt text]
```

---

## Capabilities

### Primary Capabilities
[What can this agent do?]
- [Capability 1: Description and example]
- [Capability 2: Description and example]
- [Capability 3: Description and example]

### Secondary Capabilities
[Additional capabilities, if any]
- [Capability 1]
- [Capability 2]

### Constraints and Guardrails
[What should this agent NOT do? What are its limitations?]
- [Constraint 1: e.g., "Do not make changes to production data without explicit approval"]
- [Constraint 2: e.g., "Must always explain reasoning for recommendations"]

---

## Tools and Integrations

### Security Copilot Integration
**Plugin Used:** [e.g., "Purview Data Security Plugin"]
**Prompt Categories:** [What types of prompts does this agent use?]
- [Prompt category 1: e.g., "DLP incident response prompts"]
- [Prompt category 2: e.g., "Risk assessment prompts"]

### External APIs and Services
- [Service 1: Name, purpose, and integration points]
- [Service 2: Name, purpose, and integration points]

### Database or Data Store Access
- [Data store 1: What data is accessed? What permissions are required?]
- [Data store 2: What data is accessed? What permissions are required?]

### Claude Code Modules
[If using Claude Code or specialized modules]
- [Module 1: Purpose and invocation]
- [Module 2: Purpose and invocation]

---

## Coordination and Communication

### Upstream Dependencies
[What agents or systems must provide input to this agent?]
- [Agent/System 1: What data/triggers initiate this agent?]
- [Agent/System 2: What data/triggers initiate this agent?]

### Downstream Handoffs
[What agents or systems consume this agent's output?]
- [Agent/System 1: What does it do with the output?]
- [Agent/System 2: What does it do with the output?]

### Communication Protocol
[How does this agent communicate with other agents?]
- [Protocol description: e.g., "JSON messages via message queue"]
- [Message format and expected fields]

### Error Handling and Fallback
[How does this agent handle errors? What is the fallback behavior?]
- [Error scenario 1: What happens?]
- [Error scenario 2: What happens?]
- [Escalation path: When and how to escalate?]

---

## Configuration and Tuning

### Key Parameters
[What settings or parameters can be adjusted to tune agent behavior?]

| Parameter | Default Value | Range/Options | Impact |
|-----------|---------------|---------------|--------|
| [Parameter 1] | [Value] | [Range] | [What does changing this do?] |
| [Parameter 2] | [Value] | [Range] | [What does changing this do?] |

### Model Configuration
- **Model:** [e.g., "claude-opus-4"]
- **Temperature:** [e.g., 0.7 for balanced reasoning]
- **Max Tokens:** [e.g., 4000]
- **Context Window:** [e.g., "use last 5 exchanges"]

---

## Prompt Book or Library

[Which prompts does this agent primarily use?]

### Primary Prompts
- [Prompt ID: Brief description of when/how used]
- [Prompt ID: Brief description of when/how used]

### Decision Prompts
[Prompts used for decision-making or routing]
- [Prompt ID: Decision criteria]

### Fallback Prompts
[Prompts used when primary approach fails]
- [Prompt ID: When to use]

---

## State Management

### State Variables Tracked
[What state does this agent maintain?]
- [Variable 1: Type and purpose]
- [Variable 2: Type and purpose]

### Session Management
[How are sessions tracked? How long do they persist?]

### Memory and Context
[What context is retained across interactions? What is cleared?]

---

## Example Workflows

### Workflow 1: [Title]

**Trigger:** [What initiates this workflow?]

**Interaction Flow:**
1. Agent receives [input]
2. Agent queries [data/service]
3. Agent calls [prompt]
4. Agent evaluates [response]
5. Agent sends [output] to [next step]

**Expected Duration:** [e.g., "2-3 minutes"]

---

### Workflow 2: [Title]

[Similar structure to Workflow 1]

---

## Testing and Validation

### Unit Test Cases
[What should be tested to validate this agent?]

- Test Case 1: [Input] -> [Expected output]
- Test Case 2: [Input] -> [Expected output]
- Test Case 3: [Input] -> [Expected output]

### Integration Test Cases
[Tests for interaction with other agents/systems]

- Test Case 1: [Agent A sends X, Agent B should do Y]
- Test Case 2: [Error scenario: Agent A fails, Agent B should handle Z]

### Performance Benchmarks
[Expected performance metrics]

- Response Time: [e.g., "< 5 seconds for typical query"]
- Accuracy: [e.g., "90%+ accuracy on test set"]
- Throughput: [e.g., "20 concurrent conversations"]

---

## Monitoring and Observability

### Key Metrics to Track
- [Metric 1: e.g., "Query latency (p50, p99)"]
- [Metric 2: e.g., "Error rate"]
- [Metric 3: e.g., "Successful handoff rate to next agent"]

### Logging and Debugging
[What information should be logged?]
- [Log level 1: When and what to log]
- [Log level 2: When and what to log]

### Alerts and Thresholds
[When should alerts be triggered?]
- [Alert 1: Condition and action]
- [Alert 2: Condition and action]

---

## Deployment and Operations

### Deployment Requirements
[What is needed to deploy this agent?]
- [Requirement 1: e.g., "Security Copilot API access"]
- [Requirement 2: e.g., "Purview tenant configuration"]

### Configuration Checklist
[Steps to configure before deployment]
- [ ] [Configuration item 1]
- [ ] [Configuration item 2]
- [ ] [Configuration item 3]

### Rollback Procedure
[How to quickly revert to a previous version if issues arise]

---

## Known Limitations and Future Work

### Current Limitations
[What does this agent struggle with or not support?]
- [Limitation 1: Description and workaround]
- [Limitation 2: Description and workaround]

### Roadmap Items
[Planned improvements for future versions]
- [Item 1: e.g., "Support for real-time alerting"]
- [Item 2: e.g., "Multi-language support for prompts"]

---

## Related Content

### Related Agents
- [Agent ID: How do they interact?]
- [Agent ID: How do they interact?]

### Related Prompts
- [Prompt ID 1]
- [Prompt ID 2]

### Related Playbooks
- [Playbook title: How does this agent use it?]

### Documentation
- [Link to architecture document]
- [Link to API specification]

---

## Version History

| Version | Date | Author | Status | Changes |
|---------|------|--------|--------|---------|
| 1.0 | YYYY-MM-DD | [Name] | Design | Initial definition |

---

## Sign-Off

| Role | Name | Date | Approval |
|------|------|------|----------|
| Agent Designer | [Name] | YYYY-MM-DD | [ ] |
| Tech Lead | [Name] | YYYY-MM-DD | [ ] |
| Product Owner | [Name] | YYYY-MM-DD | [ ] |

---

## Notes for Reviewers

[Any context or questions for the review process?]
