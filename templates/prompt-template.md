# Prompt Template

Use this template when documenting a new Security Copilot prompt. Copy it to the appropriate module folder (`prompts/dlp/`, `prompts/irm/`, or `prompts/dspm/`) and fill in all sections.

---

## Metadata

**Prompt ID:** [e.g., dlp-incident-response-001]
**Module:** [DLP | IRM | DSPM]
**Category:** [e.g., Incident Response, Risk Assessment, Compliance Check]
**Status:** [Tested | Theoretical]
**Last Updated:** [YYYY-MM-DD]
**Author:** [Name]

---

## Title
[Concise, descriptive title]

## Description
[2-3 sentence summary of what this prompt does and when you'd use it]

---

## Objective
[What specific question or task does this prompt accomplish?]

---

## Persona
[Who should use this prompt? Role and context]

Example: "Security analyst investigating a potential DLP incident"

---

## Prerequisites

### Required Data Access
[What data sources or Purview capabilities are needed?]
- [Data source 1]
- [Data source 2]

### Required Permissions
[What Purview roles are required?]

### Context or Information Needed
[What should the user know before running this prompt?]

---

## The Prompt

[Copy the exact prompt text here. This is what the user sends to Security Copilot.]

```
[Prompt text exactly as written]
```

---

## Expected Output Type
[What kind of response should the user expect?]

Examples:
- Structured list of incidents
- Risk assessment with scores
- Remediation recommendations
- Compliance gap analysis
- Yes/no determination

---

## Example Output

[If available, provide an example of what Security Copilot returns. Include a screenshot or transcript if helpful.]

### Input Context
[What data or situation was this prompt run against? Set the stage.]

### Sample Response
[The actual response from Security Copilot]

---

## Variations and Advanced Usage

### Variation 1: [Title]
[How to modify the prompt for a different scenario or more specific question]

```
[Modified prompt]
```

### Variation 2: [Title]
[How to modify the prompt for another scenario]

```
[Modified prompt]
```

---

## Tips for Use

- [Tip 1: How to phrase the request for best results]
- [Tip 2: What context to provide]
- [Tip 3: How to interpret the response]

---

## Limitations

### What This Prompt Cannot Do
[Be explicit about SC limitations or plugin gaps]

- Limitation 1: [Description]
- Limitation 2: [Description]

### Known Quirks
[Unusual behavior or inconsistencies observed]

- Quirk 1: [Description and workaround if available]
- Quirk 2: [Description and workaround if available]

---

## Workarounds and Follow-ups

[If the prompt has known limitations, how can users work around them?]

- Workaround 1: [Alternative approach or follow-up prompt]
- Workaround 2: [Alternative approach or follow-up prompt]

---

## Related Content

### Related Prompts
- [Prompt ID and brief description of relationship]
- [Prompt ID and brief description of relationship]

### Related Playbooks
- [Playbook title: when this prompt is used as part of a larger workflow]

### Related Use Cases
- [Use case title: scenario where this prompt applies]

---

## Effectiveness Metrics

[If tested, what was observed about effectiveness?]

### Performance Notes
- **Accuracy:** [e.g., "Correctly identified 9/10 test incidents"]
- **Response Time:** [e.g., "3-5 seconds for typical query"]
- **Confidence:** [How confident is the output? Is it always accurate?]

### Test Environment
[What configuration was used for testing?]
- [SC version]
- [Purview configuration]
- [Plugin version]

---

## Version History

| Version | Date | Status | Changes |
|---------|------|--------|---------|
| 1.0 | YYYY-MM-DD | Tested/Theoretical | Initial creation |

---

## Feedback and Notes

[Open questions for reviewers or future improvements]
