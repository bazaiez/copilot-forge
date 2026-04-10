# Playbook Template

Use this template when documenting a Security Copilot investigation or response playbook. Copy it to the `playbooks/` directory and fill in all sections.

---

## Metadata

**Playbook ID:** [e.g., pb-dlp-incident-001]
**Title:** [e.g., "Investigate and Respond to DLP Policy Violation"]
**Scenario:** [Brief description of the scenario this playbook addresses]
**Status:** [Tested | Theoretical]
**Last Updated:** [YYYY-MM-DD]
**Author:** [Name]

---

## Overview

[2-3 sentence executive summary. What problem does this playbook solve? Who should use it?]

---

## Scenario Description

[Detailed description of the situation this playbook addresses. Include context, stakeholders, and why this matters.]

### Business Impact
[What is the potential impact if this incident is not handled properly?]

---

## Intended Audience

**Primary Persona:** [e.g., Security Analyst, Incident Responder]
**Skills Required:** [What background/knowledge is expected?]
**Difficulty Level:** [Beginner | Intermediate | Advanced]

---

## Prerequisites

### Data Access and Permissions
[What Purview permissions and data access are required?]

### Tools and Platforms
- [Tool 1 and required access level]
- [Tool 2 and required access level]

### Initial Information Needed
[What information should the user gather before starting?]

---

## Estimated Time
**Typical Duration:** [e.g., "20-45 minutes for complete investigation"]
**Factors that affect duration:**
- [Factor 1: how it impacts time]
- [Factor 2: how it impacts time]

---

## Step-by-Step Workflow

[Number each step. For each step involving a Security Copilot prompt, reference the prompt ID. Include expected inputs/outputs and decision points.]

### Step 1: [Action Title]

**Objective:** [What are we trying to accomplish?]

**Actions:**
1. [Specific action]
2. [Specific action]

**Prompt(s):** [Reference prompt ID(s) if applicable]

**Expected Output:** [What should the user see?]

**Success Indicator:** [How do they know this step is complete?]

---

### Step 2: [Action Title]

**Objective:** [What are we trying to accomplish?]

**Actions:**
1. [Specific action]
2. [Specific action]

**Prompt(s):** [Reference prompt ID(s) if applicable]

**Expected Output:** [What should the user see?]

**Success Indicator:** [How do they know this step is complete?]

---

[Continue with additional steps as needed]

---

## Decision Tree

[Map out decision points in the investigation. Use ASCII diagrams or structured format.]

```
START: Incident detected
├─ Is the incident policy violation confirmed?
│  ├─ YES → Proceed to Step 4 (Assess severity)
│  └─ NO → Proceed to Step 3 (Validate alert)
├─ Is the severity HIGH?
│  ├─ YES → Proceed to immediate containment
│  └─ NO → Proceed to notification
├─ Should an exception be granted?
│  ├─ YES → Document and close
│  └─ NO → Initiate remediation workflow
```

---

## Success Criteria

[What does a successful completion of this playbook look like?]

- Criterion 1: [Measurable indicator]
- Criterion 2: [Measurable indicator]
- Criterion 3: [Measurable indicator]

---

## Expected Outcomes

### Analyst's Perspective
[What should the analyst be able to conclude?]

### Business/Stakeholder Perspective
[What decisions can be made with the information gathered?]

### Artifacts Created
[What documentation or records should be created during this playbook?]
- [Artifact 1: e.g., "Incident investigation report"]
- [Artifact 2: e.g., "Remediation plan"]

---

## Escalation Paths

[When and how should this be escalated?]

### Level 1 Escalation
[When to escalate and to whom]

**Trigger:** [What indicates escalation is needed?]
**Escalate To:** [Role/team]
**Information to Include:** [What context should be provided?]

### Level 2 Escalation
[If Level 1 escalation occurs]

**Trigger:** [What indicates further escalation is needed?]
**Escalate To:** [Role/team]

---

## Limitations and Workarounds

### Known Limitations
[What cannot be done or what gaps exist?]

- Limitation 1: [Description and impact on playbook]
- Limitation 2: [Description and impact on playbook]

### Workarounds
[How to work around these limitations]

- Workaround 1: [Explanation]
- Workaround 2: [Explanation]

### Manual Steps
[What still requires human judgment or manual action?]

---

## Roles and Responsibilities

[If multiple people are involved, clarify roles]

| Role | Responsibilities |
|------|------------------|
| [Role 1] | [Responsibilities during this playbook] |
| [Role 2] | [Responsibilities during this playbook] |

---

## Related Content

### Prompts Used
[List all prompt IDs used in this playbook]
- [Prompt ID 1]
- [Prompt ID 2]

### Related Playbooks
[Other playbooks that might be relevant]
- [Related playbook title]

### Related Use Cases
[Use cases that align with this playbook]
- [Use case title]

### External References
- [Link to Purview documentation]
- [Link to compliance guidance]

---

## Examples and Case Studies

[Optional: Provide a real or realistic example of executing this playbook]

### Scenario
[Describe the specific incident or situation]

### Execution
[Walk through how the playbook would be executed step-by-step]

### Outcome
[What was the result?]

---

## Common Pitfalls and Best Practices

### Pitfalls to Avoid
- [Pitfall 1: What not to do and why]
- [Pitfall 2: What not to do and why]

### Best Practices
- [Best practice 1: How to succeed]
- [Best practice 2: How to succeed]

---

## Metrics and Effectiveness

[If the playbook has been tested, what was observed?]

### Performance Metrics
- **Success Rate:** [e.g., "95% of incidents resolved following this playbook"]
- **Average Duration:** [e.g., "35 minutes"]
- **Quality:** [How accurate/complete are the outcomes?]

### Test Environment
[What configuration was used for testing?]

---

## Feedback and Iteration

[Notes for future improvement]

- [Feedback item 1]
- [Feedback item 2]

---

## Version History

| Version | Date | Status | Changes |
|---------|------|--------|---------|
| 1.0 | YYYY-MM-DD | Tested/Theoretical | Initial creation |

---

## Quick Reference Checklist

[One-page checklist for quick reference while executing the playbook]

- [ ] Gather initial information (Step 1)
- [ ] Validate incident (Step 2)
- [ ] Assess severity (Step 3)
- [ ] [Continue for each step]
- [ ] Document findings
- [ ] Escalate if needed
- [ ] Close incident with documentation
