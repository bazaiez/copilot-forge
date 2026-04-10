# Playbook: DLP Incident Investigation with Security Copilot

## Overview

This playbook provides a structured Security Copilot-driven investigation of Data Loss Prevention (DLP) incidents in Microsoft Purview. It guides analysts through alert triage, contextual analysis, insider risk correlation, and investigation documentation using actual SC prompts and system capabilities.

Use this playbook when:
- An automated DLP alert is triggered with medium or higher severity
- A user reports a potential data leak
- A DLP policy violation is detected during audit review
- A manager escalates suspicious data sharing activity

Investigation typically requires 30-90 minutes depending on alert severity and the number of correlated events.

---

## Prerequisites

- Microsoft Purview DLP plugin enabled in Security Copilot
- SC access with appropriate Purview RBAC roles
- DLP policy configuration and definitions available
- IRM data sharing enabled (for cross-signal user risk analysis)
- Exchange Online, SharePoint, Teams audit logs enabled
- Azure AD user information accessible
- DLP policy documentation available for reference

---

## Trigger Conditions

DLP investigation is initiated by:

1. **Automated Alert**: DLP policy match with Medium+ severity
2. **User-Reported Incident**: Employee or manager reports potential data leak
3. **Manual Discovery**: Analyst finds suspicious pattern during policy review
4. **Escalation**: Alert escalated by automated rules or previous SC analysis
5. **Correlation**: Multiple related DLP matches within short timeframe
6. **Third-Party Notification**: Customer or legal team reports exposure

---

## Investigation Flow

Each step includes the exact SC prompt to use. Parameters marked with <angle brackets> should be replaced with actual values from your environment.

### Step 1: Alert Triage and Initial Assessment

**Objective**: Understand what triggered the alert and establish baseline facts.

**SC Prompt 1**:
```
Show me the top 5 high severity DLP alerts from the past 24 hours
```

**What SC Returns**: List of recent high-severity DLP alerts with basic metadata.

**Next Action**: Select the specific alert that triggered this investigation. Document:
- Alert ID
- User involved (UPN)
- Timestamp
- Policy matched
- Data classification detected

---

### Step 2: Alert Summary and Initial Analysis

**Objective**: Get SC's analysis of the specific alert.

**SC Prompt 2**:
```
Summarize the DLP alert with ID <alertId>
```

**What SC Returns**:
- Plain-language summary of what triggered the alert
- Data classifications detected (PII, Confidential, etc.)
- Policy rule that matched
- Initial risk assessment
- Key context and observations

**Decision Point**:
- If the alert appears to be a true positive → continue to Step 3
- If evidence suggests false positive → document and consider closure
- If severity is critical → prepare for expedited escalation path

---

### Step 3: User Context and Organizational Role

**Objective**: Understand who performed the action and their normal operating context.

**SC Prompt 3**:
```
Get user risk summary for <user>
```

**What SC Returns**:
- User's organizational role and department
- Historical risk indicators (if IRM data sharing enabled)
- Prior DLP or security incidents involving this user
- Manager and organizational reporting structure

**Decision Point**:
- If user has no prior incidents → continue standard investigation
- If user has history of violations → escalate priority
- If user has high insider risk score → proceed to Step 8 (IRM correlation)

---

### Step 4: Data Risk Assessment

**Objective**: Understand the scope and sensitivity of data involved in the incident.

**SC Prompt 4**:
```
Zoom into data risk for the file in alert <alertId>
```

**What SC Returns**:
- Data classification and sensitivity level
- Ownership and access controls
- Data residency and regulatory implications
- Related data risk context
- Similar data at risk

**Decision Point**:
- If data is highly sensitive (PII, PHI, Confidential) → High priority escalation
- If data is classified but not sensitive → Continue investigation
- If data classification is unclear → Consult data classification policy

---

### Step 5: Action Details and Data Movement

**Objective**: Establish what actions were performed on the data.

**SC Prompt 5**:
```
What actions were performed on the file in alert <alertId>? Include copy, move, send, and share activities
```

**What SC Returns**:
- Timeline of actions performed by the user
- Destination of data movement (external, cloud, etc.)
- Whether actions involved sharing with external parties
- Copy or duplication activities
- Send or email activities

**Decision Point**:
- If data was sent externally → high risk, proceed to escalation
- If data was copied internally → assess business context in Step 6
- If actions are minimal/isolated → standard disposition path

---

### Step 6: Cross-Signal Analysis - Insider Risk Correlation

**Objective**: Correlate DLP findings with user behavior indicators.

**SC Prompt 6**:
```
Show me the insider risk level and indicators associated with <user>
```

**What SC Returns**:
- Insider risk score for the user (if enabled)
- Key risk indicators triggered in IRM
- Behavioral signals (unusual access patterns, data hoarding, exfiltration indicators)
- Timeline of activities triggering IRM policies
- Confidence scores for each indicator

**Decision Point**:
- If insider risk score is elevated → escalate to incident response team
- If insider risk is low but DLP signal is strong → DLP-focused investigation
- If both DLP and IRM signals are strong → potential organized exfiltration → immediate escalation

---

### Step 7: Historical Behavior and Pattern Analysis

**Objective**: Determine if this incident is part of a pattern of activity.

**SC Prompt 7**:
```
List all exfiltration activities involving <user> in the past 30 days
```

**What SC Returns**:
- Timeline of exfiltration activities (if IRM data sharing enabled)
- Unusual data access patterns
- Downloads or transfers of sensitive data
- External sharing or forwarding activities
- Frequency and trend of suspicious activities

**Decision Point**:
- If this is an isolated incident → proceed to reporting (Step 9)
- If there's a pattern of exfiltration → escalate as ongoing threat
- If activities are increasing in frequency → immediate escalation

---

### Step 8: Sequential Activities and Timeline

**Objective**: Establish a chronological narrative of related activities.

**SC Prompt 8**:
```
Show me all sequential activities performed by <user> around the time of alert <alertId> (within 1 hour before/after)
```

**What SC Returns**:
- Chronological timeline of all user activities
- Related data access, file operations, and communications
- Context for why data may have been accessed
- Related email or Teams communication
- File operations before and after the incident

**Decision Point**:
- If activities suggest accidental/legitimate business need → document as false positive or policy tuning
- If activities show deliberate data exfiltration → continue to escalation
- If timeline is unclear → request additional audit data

---

### Step 9: Investigation Summary and Disposition

**Objective**: Generate a management-ready summary and determine next steps.

**SC Prompt 9**:
```
Generate a summary of this DLP investigation for the alert <alertId> including: initial alert summary, user context, data sensitivity, actions performed, insider risk correlation, and recommended disposition (false positive, policy tuning, monitoring, escalation)
```

**What SC Returns**:
- Executive summary of findings
- Key evidence supporting conclusions
- Risk assessment and impact statement
- Recommended disposition
- Suggested escalation path (if needed)
- Documentation for compliance record

**Decision Point**:
- **False Positive**: Close alert with policy tuning recommendation
- **Policy Tuning Needed**: Document pattern and recommend rule adjustment
- **Monitoring**: Close with flagged user for enhanced monitoring
- **Escalation**: Hand off to incident response team with full SC analysis

---

## Embedded Experience Alternative

### Using "Summarize with Copilot" Button

If using the SC embedded experience in the DLP incident dashboard:

1. Navigate to DLP alert in Purview compliance portal
2. Click "Summarize with Copilot" button on the alert detail page
3. SC provides inline summary without manual prompt entry
4. Review findings and follow decision points in Steps 2-4 above
5. If detailed investigation needed, export findings and continue with Step 5+

**Limitation**: Embedded experience provides initial summary only. Use this playbook's full prompt sequence for complete investigation.

---

## DLP Triage Agent Option

If your organization has deployed the Security Copilot DLP Triage Agent:

1. Route high-volume alerts to the DLP Triage Agent for automated initial categorization
2. Agent classifies alerts as false positive, policy tuning, monitoring, or escalation
3. Review agent recommendations and override if needed
4. For alerts recommended for escalation or monitoring, proceed with manual investigation using this playbook
5. Escalated cases are automatically enriched with triage agent reasoning

**Note**: The DLP Triage Agent is a preview feature. Check with your SC administrator for availability and configuration.

---

## Promptbook Version

Complete investigation sequence formatted for SC promptbook deployment:

```
[PROMPTBOOK: DLP Incident Investigation]

Step 1: Initial Alert Triage
Prompt: "Show me the top 5 high severity DLP alerts from the past 24 hours"
Decision: If alert of interest found, proceed to Step 2. Else close investigation.

Step 2: Alert Summary
Prompt: "Summarize the DLP alert with ID <alertId>"
Decision: If true positive signal strong, proceed to Step 3. Else document false positive.

Step 3: User Context
Prompt: "Get user risk summary for <user>"
Decision: If high-risk user, proceed to Step 6. Else continue to Step 4.

Step 4: Data Risk
Prompt: "Zoom into data risk for the file in alert <alertId>"
Decision: If data highly sensitive, increase priority. Continue to Step 5.

Step 5: Action Details
Prompt: "What actions were performed on the file in alert <alertId>? Include copy, move, send, and share activities"
Decision: If external transfer, escalate. Continue to Step 6 for correlation.

Step 6: Insider Risk Correlation
Prompt: "Show me the insider risk level and indicators associated with <user>"
Decision: If elevated IRM risk, escalate immediately. Else continue to Step 7.

Step 7: Historical Exfiltration
Prompt: "List all exfiltration activities involving <user> in the past 30 days"
Decision: If pattern found, escalate. Else continue to Step 8.

Step 8: Sequential Activities
Prompt: "Show me all sequential activities performed by <user> around the time of alert <alertId> (within 1 hour before/after)"
Decision: If deliberate exfiltration evident, escalate. Else proceed to Step 9.

Step 9: Summary and Disposition
Prompt: "Generate a summary of this DLP investigation for the alert <alertId> including: initial alert summary, user context, data sensitivity, actions performed, insider risk correlation, and recommended disposition (false positive, policy tuning, monitoring, escalation)"
Decision: Execute recommended disposition. Close or escalate case.

[END PROMPTBOOK]
```

---

## Key Metrics and Success Criteria

**Metrics to Track**:
- Mean time to disposition (should decrease with SC use)
- False positive rate (should stabilize after policy tuning)
- Escalation rate (should identify true threats)
- Cost per investigation (SC-assisted should be lower)

**Success Indicators**:
- All high-priority alerts disposed within 24 hours
- Root cause identified for 90%+ of true positives
- Policy tuning recommendations reduce false positive rate by 20%+
- Escalated cases confirmed as actual incidents

---

## Troubleshooting

**SC Prompt Returns Insufficient Data**:
- Ensure audit logs are enabled for SharePoint, Exchange, Teams
- Verify user has appropriate DLP incident manager RBAC role
- Check that alert ID is correctly formatted
- Confirm Purview plugin is enabled in SC configuration

**Cross-Signal Analysis Not Available**:
- Verify IRM data sharing is enabled in your organization
- Confirm both DLP and IRM licenses are active
- Check that user exists in Azure AD
- IRM may have different retention periods than DLP

**Unable to Access Specific File or User Data**:
- Verify RBAC permissions in Purview
- Confirm data is within your organization's retention period
- Check if user account still exists (if user was terminated)
- Consult with your Purview administrator for access issues

---

End of DLP Investigation Playbook
