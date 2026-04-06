# Playbook: Insider Risk Case Triage with Security Copilot

## Overview

This playbook guides analysts through Security Copilot-driven triage and assessment of Microsoft Purview Insider Risk Management (IRM) cases. It uses real SC prompts and the IRM system capabilities to evaluate risk indicators, user behavior, and data activities to determine appropriate case handling and escalation.

Use this playbook when:
- A new Insider Risk Management alert is generated
- A case is assigned for triage and disposition decision
- Multiple behavioral indicators need consolidation for risk assessment
- A case requires escalation decision or investigation prioritization
- An existing case needs re-evaluation based on new signals

Triage typically requires 45-60 minutes depending on case complexity and behavioral signal volume.

---

## Prerequisites

- Microsoft Purview Insider Risk Management plugin enabled in Security Copilot
- SC access with appropriate IRM RBAC roles (minimum: IRM Analyst)
- Access to IRM case management in Purview compliance portal
- DLP data sharing enabled (for cross-signal correlation with DLP alerts)
- Exchange Online, SharePoint, Teams audit logs enabled
- HR system integration configured (employment status, organizational changes)
- Azure AD user directory and manager hierarchy accessible
- IRM policies and risk indicator thresholds documented

---

## Trigger Conditions

IRM case triage is initiated by:

1. **New Alert Generation**: Automated policy match (data exfiltration, abnormal access, unusual behavior, etc.)
2. **Case Assignment**: Analyst assigned case from queue for triage disposition decision
3. **External Escalation**: Manager, HR, or legal escalates potential misconduct
4. **Correlation Alert**: Multiple risk indicators combine into single case
5. **Policy Tuning**: Cases analyzed for policy effectiveness assessment
6. **Follow-up Review**: Existing case requires re-evaluation

---

## Investigation Flow

Each step includes the exact SC prompt to use. Parameters marked with <angle brackets> should be replaced with actual values from your environment.

### Step 1: Case Intake and Initial Risk Screening

**Objective**: Establish basic facts about the case and perform initial risk assessment.

**SC Prompt 1**:
```
Show me the Insider Risk Management alerts for <user>
```

**What SC Returns**:
- List of all IRM alerts associated with the user
- Alert categories (data exfiltration, abnormal access, etc.)
- Alert timestamps and detection dates
- Summary of detected risk behaviors
- Count and trend of alerts over time

**Next Action**: Identify the specific alert triggering this case. Document:
- Case ID
- User UPN
- Alert type and policy matched
- Creation date
- Risk indicator category

---

### Step 2: Comprehensive Risk Summary

**Objective**: Get SC's consolidated assessment of the user's overall risk level.

**SC Prompt 2**:
```
Show me the risk associated with user <user>. Include risk score, active risk indicators, historical context, and trend analysis
```

**What SC Returns**:
- Current insider risk score for the user
- Active risk indicators and their confidence scores
- Behavioral signals and their severity
- Historical trend of risk (increasing, stable, decreasing)
- Count of active cases for this user
- Peer comparison (how user compares to similar users)

**Decision Point**:
- If risk score is critical (>80) → escalate immediately to incident response
- If risk score is elevated (60-80) → proceed to detailed behavioral analysis
- If risk score is moderate (40-60) → continue investigation for context
- If risk score is low (<40) → monitor, may close as low-risk

---

### Step 3: Data Exfiltration Indicators

**Objective**: Determine if user has engaged in data exfiltration activities.

**SC Prompt 3**:
```
List all data exfiltration activities involving <user>
```

**What SC Returns**:
- Timeline of exfiltration events (downloads, uploads, external sharing)
- Volume of data involved in exfiltration
- Target locations (external email, cloud storage, USB, etc.)
- Files or data classifications involved
- Frequency and pattern of exfiltration
- Comparison to user's baseline behavior

**Decision Point**:
- If recent exfiltration confirmed → high priority, proceed to Step 8 (escalation)
- If historical exfiltration only → continue investigation for context
- If no exfiltration detected → continue to Step 4
- If exfiltration involves sensitive data → potential breach scenario

---

### Step 4: Sequential Activity Analysis

**Objective**: Identify coordinated or suspicious sequences of activities.

**SC Prompt 4**:
```
Show me all sequential activities performed by <user> that match insider risk patterns. Include data access, file operations, communications, and unusual behaviors
```

**What SC Returns**:
- Sequences of related activities (e.g., access escalation → data download → external share)
- Timeline showing activity relationships
- Activities outside normal business hours
- Activities from unusual locations or devices
- Combinations of behaviors indicative of specific threats
- Confidence scores for pattern matching

**Decision Point**:
- If sequences suggest premeditated activity → high risk escalation
- If sequences are accidental or contextually normal → continue investigation
- If sequences include communication (emails, Teams) → proceed to Step 5
- If behavior is completely anomalous → immediate escalation

---

### Step 5: Unusual Behavior Detection

**Objective**: Identify deviations from user's normal operating patterns.

**SC Prompt 5**:
```
Did <user> engage in any unusual behavior? Include access pattern changes, time-of-day anomalies, location changes, privilege escalations, and behavioral deviations
```

**What SC Returns**:
- Specific behavioral deviations detected
- Confidence scores for each anomaly
- Comparison to user's baseline behavior
- Comparison to peer group behavior
- Timeframe of behavioral changes
- Associated activities during anomalies
- Explanatory context (if available from HR data)

**Decision Point**:
- If unusual behavior correlates with employment changes (notice given, promotion, etc.) → monitor closely
- If behavioral changes are unexplained → continue investigation
- If behavior includes privilege escalation → high priority escalation
- If behavior is consistent with user role → low risk finding

---

### Step 6: Recent Critical Actions

**Objective**: Identify key high-risk actions in the most recent period.

**SC Prompt 6**:
```
Show me the key actions performed by <user> in the last 10 days. Focus on data access, privilege changes, sharing activities, and unusual operations
```

**What SC Returns**:
- Chronological list of high-impact actions in past 10 days
- Data accessed and sensitive information involved
- Privilege or permission changes
- Sharing and collaboration activities
- System or service access changes
- Flagged or unusual actions
- Time-of-day analysis

**Decision Point**:
- If critical actions in past 10 days → high priority, continue to Step 8
- If activities are routine and normal → continue to Step 7
- If activities include escalation or unusual access → flag for investigation
- If activities include external sharing → proceed to cross-signal analysis

---

### Step 7: 30-Day Trend and Cumulative Context

**Objective**: Establish broader context and trend for the user's activities.

**SC Prompt 7**:
```
Summarize <user>'s last 30 days of activity. Include data access patterns, file operations, communications, collaboration, and any behavioral trends
```

**What SC Returns**:
- 30-day timeline of user activities
- Volume metrics (files accessed, data transferred, etc.)
- Trend analysis (increasing, stable, decreasing in activity)
- Collaboration and sharing patterns
- Communication volume and recipients
- Data classification patterns
- Anomalies or spikes in activity
- Contextual events (projects, role changes, etc.)

**Decision Point**:
- If 30-day trend is significantly elevated → escalation
- If activities are consistent with historical baseline → low risk
- If trend is increasing → monitor closely, may escalate if continues
- If trend includes concentration around specific data → investigate access patterns

---

### Step 8: Cross-Signal Correlation - DLP Alert Matching

**Objective**: Correlate IRM findings with DLP alerts for comprehensive data risk picture.

**SC Prompt 8**:
```
Show me all DLP alerts and data loss incidents associated with <user>. Include alert details, data classifications, policies matched, and remediation status
```

**What SC Returns**:
- List of DLP alerts triggered by user's activities
- Data classifications involved in DLP alerts
- DLP policy rules matched
- Alert severity and disposition
- Timeline correlation between IRM activities and DLP alerts
- Whether DLP incidents preceded or followed IRM alerts
- Evidence of data movement to external locations

**Decision Point**:
- If DLP alerts correlate with exfiltration activities → confirm organized threat
- If DLP and IRM signals align → escalate as confirmed incident
- If DLP is clean but IRM shows suspicious behavior → monitor for future data incidents
- If only DLP signals present → refer to DLP investigation playbook

---

### Step 9: Obfuscation and Evasion Activities

**Objective**: Identify if user engaged in activities to hide or obscure behavior.

**SC Prompt 9**:
```
Show me any obfuscation activities or evasion attempts by <user>. Include account manipulation, activity deletion, permission changes, shared account use, or other hiding techniques
```

**What SC Returns**:
- Evidence of activity deletion or log tampering
- Unusual permission or privilege changes
- Use of shared or generic accounts
- VPN or proxy use patterns
- Email forwarding to external accounts
- Drive mapping or alternate access methods
- Data classification label changes or removals
- Timing of deletions relative to suspicious activities

**Decision Point**:
- If obfuscation detected → **immediate escalation** (indicates malicious intent)
- If no obfuscation → behavior may be inadvertent or contextually legitimate
- If obfuscation precedes data activities → orchestrated threat
- If obfuscation follows incident → attempt to cover tracks

---

### Step 10: Investigation Summary and Disposition

**Objective**: Generate comprehensive summary and determine case disposition.

**SC Prompt 10**:
```
Generate a complete summary of this IRM case for <user> including: initial risk indicators, behavioral analysis, data exfiltration evidence, DLP correlation, unusual activities, 30-day trend, obfuscation indicators, overall risk assessment, and recommended disposition (false positive, low-risk monitoring, ongoing investigation, or immediate escalation)
```

**What SC Returns**:
- Executive summary of findings
- Key evidence supporting conclusions
- Risk assessment with confidence scores
- Behavioral narrative explaining detected patterns
- Data at risk assessment
- Recommended disposition with justification
- Suggested escalation path (if needed)
- Documentation for compliance and legal hold

**Case Disposition Decision**:
- **False Positive**: Close case with policy tuning recommendation
- **Low-Risk Monitoring**: Close with user on monitoring list, re-check quarterly
- **Ongoing Investigation**: Maintain active case with follow-up schedule
- **Escalation**: Hand off to incident response team immediately
- **HR Notification**: Escalate to HR if employment-related risk indicators present

---

## Embedded Experience Alternative

### Using "Summarize with Copilot" Button

If using the SC embedded experience in the IRM case dashboard:

1. Navigate to IRM case in Purview compliance portal
2. Click "Summarize with Copilot" button on the case detail page
3. SC provides inline summary of risk indicators and behavioral context
4. Review findings and follow decision points in Steps 2-4 above
5. If detailed investigation needed, export findings and continue with Step 5+

**Limitation**: Embedded experience provides initial summary only. Use this playbook's full prompt sequence for comprehensive case assessment.

---

## IRM Triage Agent Option

If your organization has deployed the Security Copilot IRM Triage Agent:

1. Route new alerts to the IRM Triage Agent for automated initial categorization
2. Agent classifies cases as false positive, low-risk, ongoing-investigation, or escalation
3. Review agent recommendations and override if needed
4. For cases recommended for escalation or investigation, proceed with manual triage using this playbook
5. Escalated cases are automatically prepared for incident response handoff

**Note**: The IRM Triage Agent is a preview feature. Check with your SC administrator for availability and configuration.

---

## Promptbook Version

Complete triage sequence formatted for SC promptbook deployment:

```
[PROMPTBOOK: Insider Risk Case Triage]

Step 1: Alert Intake
Prompt: "Show me the Insider Risk Management alerts for <user>"
Decision: If user has active alerts, proceed to Step 2. Else close as resolved.

Step 2: Risk Summary
Prompt: "Show me the risk associated with user <user>. Include risk score, active risk indicators, historical context, and trend analysis"
Decision: If critical risk (>80), escalate immediately. Else continue to Step 3.

Step 3: Exfiltration Analysis
Prompt: "List all data exfiltration activities involving <user>"
Decision: If exfiltration confirmed, escalate. Else continue to Step 4.

Step 4: Sequential Activities
Prompt: "Show me all sequential activities performed by <user> that match insider risk patterns"
Decision: If premeditated sequences detected, escalate. Else continue to Step 5.

Step 5: Unusual Behavior
Prompt: "Did <user> engage in any unusual behavior?"
Decision: If escalation detected, escalate. Else continue to Step 6.

Step 6: Last 10 Days
Prompt: "Show me the key actions performed by <user> in the last 10 days"
Decision: If critical actions, escalate. Else continue to Step 7.

Step 7: 30-Day Summary
Prompt: "Summarize <user>'s last 30 days of activity"
Decision: If trend elevated, flag for escalation. Else continue to Step 8.

Step 8: DLP Correlation
Prompt: "Show me all DLP alerts associated with <user>"
Decision: If DLP+IRM signals align, escalate as confirmed incident. Else continue to Step 9.

Step 9: Obfuscation Analysis
Prompt: "Show me any obfuscation activities or evasion attempts by <user>"
Decision: If obfuscation detected, escalate immediately. Else proceed to Step 10.

Step 10: Summary and Disposition
Prompt: "Generate a complete summary of this IRM case for <user> including: initial risk indicators, behavioral analysis, data exfiltration evidence, DLP correlation, unusual activities, 30-day trend, obfuscation indicators, overall risk assessment, and recommended disposition"
Decision: Execute recommended disposition. Close or escalate case.

[END PROMPTBOOK]
```

---

## Key Behavioral Indicators

**Critical Escalation Indicators** (Immediate Action):
- Obfuscation or evasion activities detected
- Concurrent data exfiltration and DLP violations
- Privilege escalation with data access pattern changes
- Multiple sequential high-risk activities in short timeframe
- Risk score > 80

**High Priority Indicators** (Escalate Within 24 Hours):
- Confirmed data exfiltration with external sharing
- Unusual behavior deviations from baseline
- Recent actions targeting sensitive data
- Multiple alerts within 72-hour window
- Risk score 60-80

**Standard Investigation Indicators** (Continue Assessment):
- Single behavioral anomaly without corroboration
- Data access without movement
- Activities explained by business context
- Risk score 40-60

**Low Risk Indicators** (Monitor/Close):
- Activities consistent with historical baseline
- Behavioral changes correlated with HR events
- No data movement detected
- Risk score < 40

---

## Metrics and Success Criteria

**Metrics to Track**:
- Mean time to disposition (should decrease with SC use)
- Escalation accuracy (% of escalated cases confirmed as actual incidents)
- False positive rate (should stabilize after policy tuning)
- Case closure rate (% of cases closed vs. ongoing)
- Cost per case (SC-assisted should be lower)

**Success Indicators**:
- All critical-risk cases escalated within 4 hours
- 85%+ of escalated cases confirmed as actual incidents
- Triage time reduced to 30-45 minutes average
- False positive rate reduced by 30%+ after policy tuning
- All obfuscation indicators detected and escalated

---

## Troubleshooting

**SC Prompt Returns Insufficient Data**:
- Ensure audit logs are enabled for Exchange, SharePoint, Teams
- Verify user has appropriate IRM analyst RBAC role
- Confirm user account is not deleted or disabled
- Check that IRM policies are enabled and configured

**Cross-Signal Analysis Not Available**:
- Verify DLP data sharing is enabled between IRM and DLP
- Confirm both IRM and DLP licenses are active
- Check that DLP policies are configured for the user's locations
- IRM and DLP may have different retention periods

**Behavioral Anomalies Not Detected**:
- Ensure user has sufficient activity history for baseline comparison
- Verify audit log retention is adequate (minimum 30-90 days)
- Check if user is new hire (less historical baseline available)
- Confirm peer comparison data is available for user's role

**Unable to Access HR Data for Context**:
- Verify HR system integration is configured
- Check employment status, organizational changes in Azure AD
- Consult with HR team for context on role changes or departures
- Use available audit and behavioral context as alternative

---

End of IRM Case Triage Playbook
