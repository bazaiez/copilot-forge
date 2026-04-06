# Security Copilot Investigation Playbooks for Microsoft Purview

## Overview

This directory contains four comprehensive investigation and response playbooks for Security Copilot users managing Microsoft Purview security incidents. These playbooks are designed for security analysts, CSAs, and incident responders who need structured guidance for conducting investigations and managing security incidents.

All playbooks follow a consistent structure:
- Clear prerequisites and trigger conditions
- Step-by-step investigation flow with decision points
- Integrated Security Copilot prompts at each step
- Severity classification frameworks
- Escalation criteria and procedures
- Output artifacts and documentation templates
- Common pitfalls to avoid
- Quality checklists for verification

---

## Playbooks Included

### 1. dlp-incident-investigation.md
**Playbook: DLP Incident Investigation**

Use this playbook when investigating Data Loss Prevention (DLP) alerts or incidents.

**When to use:**
- An automated DLP alert is triggered
- A user reports a potential data leak
- A policy violation is detected during audit review
- Manager escalates suspicious data access or transfer activity

**Investigation phases:**
1. Initial alert assessment
2. Policy and rule context review
3. User context and history
4. Data sensitivity assessment
5. Scope determination
6. Impact analysis
7. Response recommendation
8. Documentation

**Key features:**
- 8 integrated Security Copilot prompts
- Severity classification decision tree
- Impact vs. exposure assessment
- False positive evaluation guidance
- Manager notification criteria
- Quality checklist (20+ verification points)

**Typical timeline:** 30-90 minutes depending on alert severity

---

### 2. irm-case-triage.md
**Playbook: Insider Risk Case Triage**

Use this playbook when triaging Insider Risk Management (IRM) alerts and cases.

**When to use:**
- A new IRM alert is generated
- A case is assigned for triage and disposition
- Multiple risk indicators need consolidation
- Escalation decision or investigation prioritization is needed
- An existing case requires re-evaluation

**Investigation phases:**
1. Alert intake and initial assessment
2. Risk indicator review
3. User context and employment status
4. Activity timeline analysis
5. Behavioral pattern assessment
6. Correlation with other signals (DLP, audit)
7. Severity classification
8. Action recommendation and disposition
9. Case documentation

**Key features:**
- 9 integrated Security Copilot prompts
- Risk indicator assessment framework
- Employment factor evaluation
- Baseline behavior comparison
- Severity classification matrix
- Escalation decision tree
- Disposition categories (dismiss, monitor, restrict, investigate, escalate)
- Quality checklist (20+ verification points)

**Typical timeline:** 45-60 minutes depending on case complexity

---

### 3. data-leakage-response.md
**Playbook: Data Leakage Response**

Use this playbook when responding to confirmed or suspected data breaches.

**When to use:**
- A data breach is confirmed or strongly suspected
- Sensitive data is found in unauthorized locations
- External parties report possession of confidential information
- Monitoring detects large-scale suspicious data movement
- Customer or regulatory notification requirements are triggered
- Post-breach forensics and remediation are required

**Response phases:**
1. Initial detection and scope assessment
2. Data classification of leaked content
3. Leakage vector identification
4. User and access review
5. Timeline reconstruction
6. Impact quantification
7. Containment actions planning
8. Evidence preservation
9. Stakeholder notification drafting
10. Post-incident review preparation

**Key features:**
- 8 integrated Security Copilot prompts
- Regulatory framework identification (GDPR, HIPAA, PCI-DSS, CCPA)
- Exposure vector analysis (email, cloud, USB, physical)
- Impact and damage assessment
- Breach notification requirements
- Evidence preservation guidance
- Stakeholder communication templates
- Timeline and escalation procedures

**Typical timeline:** 4-8 hours for initial response phase

---

### 4. post-incident-review.md
**Playbook: Post-Incident Review**

Use this playbook when conducting comprehensive post-incident reviews (postmortems) after security incidents.

**When to use:**
- A security incident has been contained and stabilized
- Sufficient time has passed to assess response objectively (7-30 days)
- All evidence collection and investigation is substantially complete
- Leadership requires lessons learned and recommendations
- Process improvements need to be identified and tracked
- Team feedback on incident response needs to be captured

**Review phases:**
1. Incident summary generation
2. Timeline reconstruction and verification
3. Detection effectiveness assessment
4. Response evaluation
5. Gap identification
6. Lessons learned extraction
7. Remediation recommendations
8. Report generation for leadership

**Key features:**
- 8 integrated Security Copilot prompts
- Detection effectiveness assessment
- Response procedure evaluation
- Systemic gap identification
- Lessons learned framework
- Remediation recommendation ranking
- Multi-level report generation
- PIR meeting structure and agenda
- Quality checklist (20+ verification points)

**Typical timeline:** 20-40 hours over 2-4 weeks

---

## How to Use These Playbooks

### Quick Start for Analysts

1. **Identify the incident type:**
   - DLP alert → Use dlp-incident-investigation.md
   - IRM alert/case → Use irm-case-triage.md
   - Confirmed breach → Use data-leakage-response.md
   - Post-incident → Use post-incident-review.md

2. **Review prerequisites:**
   - Ensure you have required access and tools
   - Confirm Security Copilot session is active
   - Gather necessary data sources

3. **Follow the investigation flow:**
   - Execute each step in order
   - Run the provided Security Copilot prompts at each step
   - Document findings and decision points
   - Answer decision point questions honestly

4. **Complete the quality checklist:**
   - Verify all steps completed
   - Confirm all SC prompts executed
   - Check decision documentation
   - Ensure appropriate approvals obtained

### For Security Copilot Integration

**Copy/paste the SC prompts exactly as written**, customizing the bracketed fields with your incident details. Example:

```
Replace: [INSERT_UPN] with actual user email
Replace: [INSERT_SEVERITY] with Low/Medium/High/Critical
Replace: [INSERT_TIMESTAMP] with actual date/time
```

**Maintain the prompt structure** - the structured format helps Security Copilot provide consistent, relevant analysis.

### For Escalation and Handoff

**Include specific section references** when escalating:
- Reference the playbook (e.g., "DLP Incident Investigation Step 6 - Impact Analysis")
- Reference the decision point (e.g., "Severity Classification Decision Tree")
- Include SC prompt numbers (e.g., "SC Prompt 3 findings indicate...")

### For Management and Audit

**Maintain documentation trail:**
- Keep copies of all SC prompts and responses
- Document all decision points with rationale
- Store evidence package per playbook specifications
- Track actions and follow-ups via quality checklists

---

## Key Concepts Across All Playbooks

### Security Copilot Prompts

Each playbook includes numbered SC prompts (SC Prompt 1, SC Prompt 2, etc.) that should be executed in sequence. These prompts:
- Provide structured analysis at each investigation step
- Synthesize findings from multiple sources
- Recommend next actions and decision criteria
- Help identify gaps and risks
- Support escalation justification

**Note:** Security Copilot analysis is advisory. The analyst/investigator maintains responsibility for all decisions and escalations.

### Decision Points

Each playbook includes decision points where analyst judgment is required:
- Evaluate whether conditions for the decision are met
- Choose the appropriate path forward
- Document the decision rationale
- Proceed to the next appropriate step

### Severity Classifications

Each playbook includes severity classification frameworks (Low, Medium, High, Critical, and sometimes Dismiss or Monitor). These provide:
- Consistent baseline for alert assessment
- Escalation trigger criteria
- Resource allocation guidance
- SLA and timeline requirements

### Quality Checklists

Each playbook ends with a quality checklist (15-20+ items) to verify:
- All investigation steps completed
- All SC prompts executed
- Critical decision points evaluated
- Appropriate approvals obtained
- Documentation complete

**Investigation/review is not complete until all checklist items are verified.**

### Escalation Criteria

Each playbook specifies when escalation is required:
- To manager/supervisor (alerting of incident)
- To CISO/Security leadership (significant incidents)
- To legal counsel (breach/regulatory implications)
- To law enforcement (criminal activity)
- To executive team (high-impact incidents)

---

## Customization and Refinement

### Adapting Prompts for Your Organization

The SC prompts are designed as templates. Customize them to include:
- Your organization's data classification framework
- Your specific regulatory obligations
- Your internal policy names and numbers
- Your escalation authority structure
- Your incident management system names

### Integrating with Existing Tools

These playbooks are designed to work alongside:
- Microsoft Purview DLP incident management
- Microsoft Purview Insider Risk Management
- Azure Active Directory logs
- Exchange, SharePoint, and Teams audit logs
- Email message trace tools
- File activity monitoring tools
- Your incident management system
- Your case management system

### Training and Onboarding

Use these playbooks to:
- Train new security analysts on incident investigation procedures
- Create consistent investigation methodology
- Establish baselines for investigation quality
- Provide hands-on learning with Security Copilot
- Build organizational incident response maturity

---

## Document Maintenance and Updates

### Version Control

These playbooks should be maintained in version control with:
- Version numbers and dates
- Change logs documenting updates
- Review cycles (recommend annual review minimum)
- Approval process for modifications

### Regular Review Schedule

Recommend reviewing playbooks:
- **Quarterly**: After major incidents to incorporate lessons learned
- **Annually**: For completeness and relevance check
- **Ad hoc**: When organizational changes affect incident response
- **Post-incident**: If any incident revealed playbook gaps

### Feedback and Improvement

After using these playbooks, provide feedback on:
- Steps that were unclear or difficult
- SC prompts that could be improved
- Decision points that need clarification
- Additional guidance that would be helpful
- Changes in organizational procedures or tools

---

## Troubleshooting and Common Questions

### Q: What if a playbook step doesn't apply to my situation?
**A:** Skip the non-applicable step but document why. Continue with remaining steps. Note in the quality checklist.

### Q: Should I run all SC prompts or can I skip some?
**A:** Run all prompts in sequence for comprehensive analysis. The prompts build on each other. If time-constrained, run at least prompts covering your major decision points.

### Q: What if Security Copilot recommendations conflict with my analysis?
**A:** Investigate the conflict. Document both perspectives. Make your own judgment but escalate disagreement to your supervisor. SC analysis is advisory, not authoritative.

### Q: Can I use these playbooks without Security Copilot?
**A:** Yes, but with reduced effectiveness. The SC prompts provide valuable analytical support. You can use the playbook structure and decision trees manually, but you'll need to manually gather and analyze the information that SC would synthesize.

### Q: How do I customize these for my organization?
**A:** Create a configuration file that maps:
- Your policy names to playbook references
- Your escalation authority structure
- Your data classification framework
- Your regulatory obligations
- Your internal tool names
Then customize prompt templates to reference your configuration.

### Q: What if I'm running out of time to complete the full playbook?
**A:** Complete the minimum viable investigation:
1. Follow Steps 1-3 (initial assessment and user context)
2. Execute SC Prompts 1-3
3. Make escalation decision
4. Complete remaining steps in priority order
5. Document time constraints in audit trail

---

## Related Resources

### Microsoft Purview Documentation
- [DLP Policy Documentation](https://docs.microsoft.com/purview)
- [Insider Risk Management](https://docs.microsoft.com/purview)
- [eDiscovery and Advanced Audit](https://docs.microsoft.com/purview)

### Incident Response Resources
- NIST Cybersecurity Framework
- SANS Incident Response Procedures
- CIS Controls and Benchmarks
- CISA Incident Response Guidance

### Security Copilot Documentation
- [Security Copilot User Guide]
- [Security Copilot Prompt Best Practices]
- [Integration with Microsoft Purview]

---

## Support and Questions

For questions about these playbooks:
- Review the relevant playbook's "Common Pitfalls" section
- Consult the decision trees and criteria
- Escalate to your CISO or Security leadership
- Engage Security Copilot for analytical support
- Document lessons learned for continuous improvement

---

## Document Information

**Created:** 2026
**For:** Microsoft Purview Security Copilot practitioners
**Authors:** CSA / Security Copilot Champion
**Classification:** Internal Use - Security Sensitive

**Playbooks Included:**
1. dlp-incident-investigation.md
2. irm-case-triage.md
3. data-leakage-response.md
4. post-incident-review.md

**Total Investigation Time:** Variable (30 min to 40+ hours depending on playbook and complexity)
**Required Tools:** Microsoft Purview, Security Copilot, Audit Logs
**Audience:** Security Analysts, Incident Responders, CSAs

---

## Change Log

| Version | Date | Changes | Author |
|---------|------|---------|--------|
| 1.0 | 2026 | Initial creation - 4 playbooks | CSA Champion |

