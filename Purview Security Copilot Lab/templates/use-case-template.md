# Use Case Template

Use this template when documenting a new use case. Copy it to the `use-cases/` directory and fill in all sections.

---

## Title
[Replace with use case title]

## Scenario Description
[Provide a 2-3 sentence description of the scenario. Who is involved? What problem are they solving? What is the business context?]

---

## Persona and Role
**Who:** [Job title and description]
**Goals:** [What does this person want to accomplish?]
**Pain Points:** [What challenges do they face currently?]
**Typical Tools:** [What tools do they currently use for similar tasks?]

---

## Key Objectives
[List 3-5 specific, measurable objectives this use case addresses. Example: "Identify and respond to DLP policy violations within 24 hours."]

1. [Objective 1]
2. [Objective 2]
3. [Objective 3]

---

## Prerequisites

### Data Sources Required
- [Data source 1 and required permissions]
- [Data source 2 and required permissions]

### Tools and Platforms
- [Required tool 1 and access level]
- [Required tool 2 and access level]

### Knowledge/Skills
- [Prerequisite knowledge 1]
- [Prerequisite knowledge 2]

### Access and Permissions
[What Purview roles or permissions are required? What scope of data access is needed?]

---

## Workflow Steps

[Provide a detailed step-by-step workflow. Number each step. For each step that involves a Security Copilot prompt, reference the prompt ID.]

1. **[Step Title]**
   - Description of the action
   - Related Prompt: [Prompt ID if applicable]
   - Expected input/output

2. **[Step Title]**
   - Description of the action
   - Related Prompt: [Prompt ID if applicable]
   - Expected input/output

3. **[Step Title]**
   - Description of the action
   - Related Prompt: [Prompt ID if applicable]
   - Expected input/output

[Continue as needed]

---

## Expected Outcomes

### Primary Outcomes
- [What is the user hoping to achieve? Be specific.]
- [What decisions can they make with this information?]

### Success Criteria
- [Measurable indicator 1: e.g., "Identify 95% of policy violations within 24 hours"]
- [Measurable indicator 2]
- [Measurable indicator 3]

### Example Output
[Describe or show an example of what success looks like. Include sample data if available.]

---

## Decision Tree / Branch Points

[If the workflow has decision points, map them out. Example:]

```
Start
├─ Is data classification correct?
│  ├─ Yes → Proceed to Step 5
│  └─ No → Return to Step 2, correct classification
├─ Is the policy violation intentional?
│  ├─ Yes → Document exception and proceed
│  └─ No → Initiate remediation
```

---

## Limitations and Workarounds

### Known Limitations
[What can Security Copilot NOT do in this workflow? What are the plugin limitations or gaps?]

- Limitation 1: [Description and impact]
- Limitation 2: [Description and impact]

### Workarounds
[How can users work around these limitations?]

- Workaround 1: [Alternative approach]
- Workaround 2: [Alternative approach]

### Manual Steps Required
[What steps still require human judgment or manual action?]

---

## Time Estimate
**Typical Duration:** [e.g., "15-30 minutes for initial investigation"]
[Include factors that might change the estimate]

---

## Related Content

### Prompts Used
- [Prompt ID 1]
- [Prompt ID 2]

### Related Playbooks
- [Playbook title and location]

### Related Use Cases
- [Related use case title]

### External References
- [Link to Purview documentation]
- [Link to relevant blog post or guide]

---

## Notes and Considerations

[Any other context that would help someone understand or implement this use case?]

- [Consideration 1]
- [Consideration 2]

---

## Version History

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | YYYY-MM-DD | [Name] | Initial creation |

---

## Feedback

[Any open questions or feedback for reviewers?]
