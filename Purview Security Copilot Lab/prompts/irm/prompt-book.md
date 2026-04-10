# IRM Prompt Book: Security Copilot for Insider Risk Management

**Validation Status:** 10 [VALIDATED] / 3 [PARTIALLY-VALIDATED]
**Required Plugin:** Microsoft Purview (primary); Microsoft Entra (optional, for sign-in and identity context)
**Last Validated:** April 2026

This prompt book contains 16 ready-to-use Security Copilot prompts for Insider Risk Management workflows. Each prompt is validated against real Security Copilot capabilities with the Purview plugin enabled. Prompts are organized by investigation stage, from alert triage through risk assessment and escalation.

**Key Capabilities Used:**
- Get User Risk Summary
- Get Data Risk Summary
- Triage Purview Alerts

**Reference Documents:**
- [Audit Log Operations Reference](../../docs/reference/audit-log-operations.md) — Exact operation names for activity-based prompts
- [Sensitive Information Types Reference](../../docs/reference/sensitive-information-types.md) — Exact SIT names for detection queries
- [Plugin Dependency Map](../../docs/plugin-dependency-map.md) — Required plugins per section
- [Validation Matrix](../validation-matrix.md) — Testing status for each prompt
- [Error Recovery](../taxonomy.md#error-recovery-and-troubleshooting) — What to do when SC returns unexpected results

**Important:** SC does not maintain per-user behavioral baselines. When prompts reference "baseline" or "anomalous behavior," SC reports what Purview IRM has flagged as anomalous based on IRM's own analytics — it does not compute its own baselines.

---

## 1. TRIAGE

Initial alert assessment and priority ranking for incoming insider risk indicators.

### 1.1 Single IRM Alert Risk Assessment [VALIDATED]

**Title:** Assess IRM Alert Risk Level and Triage Priority

**Purpose:** Rapidly evaluate an incoming IRM alert to determine risk severity and appropriate response tier.

**When to Use:** Immediately after an IRM alert is generated; helps incident responders prioritize caseload.

**SC System Capability Used:** Triage Purview Alerts
**Relevant Audit Log Operations:** `InsiderRiskAlertGenerated`, `InsiderRiskSequenceDetected`, `SecurityComplianceInsiderRiskPolicy`

**Exact Prompt:**
```
Show me the Insider Risk Management alerts for <user>. What is their risk profile and should this alert be investigated immediately or scheduled for review?
```

**Expected Output:**
- User name, department, role, tenure
- Alert type (sequence detection, anomalous activity, data exfiltration, etc.)
- Risk indicators that triggered alert
- Risk score or severity rating
- Triage recommendation (immediate investigation / schedule review / monitor only)
- Key risk factors and mitigating factors

**Chain-Next:** "List all the sequential activities involving this user over the past 30 days."

**Limitations:**
- Risk score is based on detected indicators, not confirmed malicious intent
- Cannot assess user intent without additional investigation
- IRM configuration and policies vary by organization

**Tips:**
- Cross-check tenure and recent role changes
- Look for recent performance issues or termination notices
- Consider timing of unusual activity
- Factor in user's access level and data sensitivity

---

### 1.2 Multi-User Risk Priority Ranking [VALIDATED]

**Title:** Rank Multiple Users by Insider Risk Priority

**Purpose:** Prioritize investigation queue when multiple IRM alerts exist simultaneously.

**When to Use:** During period of multiple concurrent IRM alerts; helps allocate investigation resources efficiently.

**SC System Capability Used:** Triage Purview Alerts + Get User Risk Summary
**Relevant Audit Log Operations:** `InsiderRiskAlertGenerated`, `InsiderRiskCaseCreated`

**Exact Prompt:**
```
I have multiple Insider Risk Management alerts. Rank the top 5 users by risk priority, considering their access level, data sensitivity they can reach, tenure, and the severity of activities indicated.
```

**Expected Output:**
- Ranked list of users (1-5)
- Risk severity for each (Critical/High/Medium/Low)
- Primary risk factor for each user
- Access level and data risk
- Recommended immediate actions
- Estimated investigation time per user

**Chain-Next:** "For the highest-risk user, show me their activity pattern and any anomalous behavior."

**Limitations:**
- Ranking depends on complete IRM alert data
- Cannot compare across different policy configurations
- May not account for false positives in IRM detection

**Tips:**
- Prioritize users with administrative access
- Consider data sensitivity they can access
- Look for users in sensitive roles (finance, legal, HR)
- Factor in termination or role change timing

---

### 1.3 Quick Context: Is This User Suspicious? [VALIDATED]

**Title:** Assess Whether IRM Alert Indicates Genuine Insider Risk or False Positive

**Purpose:** Quickly determine if indicators represent real threat or benign explanation exists.

**When to Use:** During triage when alert context seems inconsistent or user has legitimate explanation.

**SC System Capability Used:** Get User Risk Summary

**Exact Prompt:**
```
Can you summarize risk associated with user <user>? Considering their role (Sales Manager), tenure (3 years), and recent context (just completed quarterly close), are the detected activities suspicious or likely legitimate?
```

**Expected Output:**
- User profile summary
- Risk assessment (elevated/normal/low)
- Activity analysis (aligned with role / unusual / concerning)
- Context factors (role-appropriate vs. anomalous)
- Confidence in risk assessment
- Recommendation for further investigation

**Chain-Next:** "Did the user engage in any unusual behavior? Highlight anomalies compared to their baseline."

**Limitations:**
- Cannot verify user claims or explanations
- Context may be incomplete or misleading
- Role-based baseline requires good role data

**Tips:**
- Cross-reference with user's job description
- Check recent calendar for business events
- Verify with manager for legitimate explanations
- Look for patterns vs. isolated incidents

---

## 2. INVESTIGATION

Deep-dive analysis of suspicious user activity and risk indicators.

### 2.1 User Activity Timeline and Behavioral Anomalies [VALIDATED]

**Title:** Reconstruct User Activity Timeline with Anomaly Highlighting

**Purpose:** Build chronological sequence of user activities to identify escalation patterns and potential planned activity.

**When to Use:** After identifying high-risk user; needed to understand activity progression and intent.

**SC System Capability Used:** Get User Risk Summary
**Relevant Audit Log Operations:** `FileAccessed`, `FileDownloaded`, `FileSyncDownloadedFull`, `FileModified`, `SharingSet`, `Send`, `New-InboxRule`, `FileCreatedOnRemovableMedia`
**Important:** SC reports anomalies flagged by IRM, not its own computed baselines. "Highlight anomalies" means: show activities that IRM flagged as deviating from the user's established behavioral pattern.

**Exact Prompt:**
```
List all the sequential activities involving user <user> over the past 30 days. Include file access, downloads, sharing actions, email forwarding, meeting attendance, and sign-in patterns. Highlight any activities that deviate from the user's normal baseline.
```

**Expected Output:**
- Chronological timeline (date, time, activity type)
- Activity classification (normal / unusual / concerning)
- Data accessed (file names, sensitivity levels)
- Downloads and transfers (destinations, volumes)
- Unusual timing (after hours, weekends, unusual locations)
- Behavioral pattern assessment
- Escalation analysis (activities building on each other)

**Chain-Next:** "Did the user engage in any unusual behavior during this period? What specific anomalies are most concerning?"

**Limitations:**
- Depends on comprehensive audit logging
- "Baseline" requires historical data to calculate
- Cannot infer causation from activity sequence alone
- Unusual activity may have legitimate explanations

**Tips:**
- Look for scripted or repetitive patterns
- Check for access to systems user normally doesn't use
- Correlate with calendar events (exfiltration before job search?)
- Compare to peer group patterns

---

### 2.2 Data Exfiltration Activities Analysis [VALIDATED]

**Title:** Identify and Analyze All User Data Exfiltration Attempts

**Purpose:** Determine scope and nature of data user has attempted to access or remove from organization.

**When to Use:** When IRM alert indicates exfiltration activity; needed to assess data risk.

**SC System Capability Used:** Get User Risk Summary + Get Data Risk Summary
**Relevant Audit Log Operations:** `FileDownloaded`, `FileSyncDownloadedFull`, `FileCreatedOnRemovableMedia`, `FileCopiedToRemovableMedia`, `FileUploadedToCloud`, `SharingSet`, `AnonymousLinkCreated`, `Send`, `SendAs`
**Note:** For comprehensive exfiltration detection, include exact operation names from [Audit Log Operations Reference](../../docs/reference/audit-log-operations.md). Use the Operation Anchor pattern if SC returns incomplete results.

**Exact Prompt:**
```
List all the data exfiltration activities involving user <user> in the past 30 days. Include attempts to download sensitive files, share externally, forward to external email, upload to personal cloud storage, or access data not normally needed for their role.
```

**Expected Output:**
- List of exfiltration attempts (chronological)
- File/data names and classifications
- Volume of data attempted
- Exfiltration method (email, cloud, USB, email forwarding)
- Destination (personal email, cloud account, etc.)
- Policy blocks vs. successful actions
- Data sensitivity summary

**Chain-Next:** "What data classifications are most at risk based on this user's activities?"

**Limitations:**
- Limited to activities captured in audit logs or DLP matches
- Shadow IT and unmonitored channels may not be included
- Volume estimates may not be accurate
- Cannot confirm data actually used

**Tips:**
- Check for multiple small transfers (data exfiltration in pieces)
- Look for unusual cloud storage access (personal accounts)
- Verify business justification for each data access
- Cross-check with DLP policy matches

---

### 2.3 Obfuscation and Hiding Techniques [PARTIALLY-VALIDATED]

**Title:** Detect Evidence of User Attempting to Hide Activity

**Purpose:** Identify whether user took steps to conceal their actions, indicating intent to violate policy.

**When to Use:** In high-severity IRM investigation where deception appears part of activity pattern.

**SC System Capability Used:** Get User Risk Summary
**Relevant Audit Log Operations:** `FileDeleted`, `FileDeletedFirstStageRecycleBin`, `FileDeletedSecondStageRecycleBin`, `SoftDelete`, `HardDelete`, `MoveToDeletedItems`, `New-InboxRule`, `Set-InboxRule`, `Disable-MailboxAuditLog`
**Note:** Obfuscation detection is limited to what IRM surfaces. SC cannot observe already-deleted content.

**Exact Prompt:**
```
Did the user <user> engage in any unusual behavior during this investigation period? Specifically, look for evidence of intentional obfuscation such as deleting emails, using external email accounts, accessing logs, or attempting to hide activities.
```

**Expected Output:**
- Evidence of obfuscation attempts (if any)
- Activity hiding patterns (document deletion, log clearance, email forwarding)
- Use of alternative accounts or channels
- Attempts to cover tracks
- Timing correlation with other suspicious activities
- Confidence in obfuscation assessment

**Chain-Next:** "Based on these obfuscation attempts, what is the user's likely intent?"

**Limitations:**
- Cannot observe deleted activities in most cases
- Legitimate reasons may exist for activity deletion
- Log access may be normal for some roles (IT, executives)
- Hard to distinguish from user privacy practices

**Tips:**
- Look for pattern of email deletions
- Check for changes to email forwarding rules
- Review audit log for suspicious access
- Cross-reference with calendar and job tasks

---

### 2.4 Activity Escalation and Pattern Recognition [VALIDATED]

**Title:** Assess Whether Activities Indicate Escalating Threat or Isolated Incident

**Purpose:** Determine if user behavior shows progression toward harmful outcome or represents one-time activity.

**When to Use:** During investigation to assess whether activity pattern indicates planned exfiltration or impulsive act.

**SC System Capability Used:** Get User Risk Summary

**Exact Prompt:**
```
Show key actions performed by user <user> in the last 10 days. Do these activities form an escalating pattern (e.g., reconnaissance, testing access, bulk data gathering, exfiltration) or are they isolated incidents? What would the next logical step be if the pattern continues?
```

**Expected Output:**
- Activity progression timeline
- Pattern classification (escalating / isolated / recurring)
- Reconnaissance vs. action phase
- Testing vs. bulk operations
- Predicted next steps if pattern continues
- Threat escalation assessment
- Recommended response actions

**Chain-Next:** "What preventive actions would stop this escalating pattern?"

**Limitations:**
- Escalation pattern not always clear from activity alone
- Activities may have benign explanations when viewed sequentially
- User may not follow predicted pattern

**Tips:**
- Look for testing phases (small downloads before large ones)
- Check for reconnaissance activities (policy probing, access testing)
- Correlate with external events (job search, termination notice)
- Consider whether activities build logically

---

### 2.5 Unusual Behavior Baseline Comparison [PARTIALLY-VALIDATED]

**Title:** Compare User Activity to Historical Baseline and Peer Group

**Purpose:** Establish whether observed activity is truly anomalous or represents normal behavior for user/role.

**When to Use:** To validate whether IRM alert indicators represent genuine deviation or normal role activity.

**SC System Capability Used:** Get User Risk Summary
**Important:** SC does not compute its own behavioral baselines. This prompt relies on IRM's anomaly detection. Results reflect what IRM has flagged, not independent SC analysis.

**Exact Prompt:**
```
Did the user <user> engage in any unusual behavior? Compare their activity pattern in the past 30 days to their baseline behavior from the prior 6 months. Highlight activities that are truly anomalous compared to their normal patterns.
```

**Expected Output:**
- User baseline profile (typical activity patterns)
- Current activity vs. baseline comparison
- Statistically anomalous activities
- Activities normal for their role
- Peer group comparison (if available)
- Anomaly confidence levels
- False positive assessment

**Chain-Next:** "Which anomalies are most concerning and which likely have legitimate explanations?"

**Limitations:**
- Baseline calculation requires 6+ months of historical data
- Role changes make baseline obsolete
- Normal variation can appear anomalous
- Seasonal patterns may skew comparisons

**Tips:**
- Establish baseline from prior 6+ months of normal activity
- Account for seasonal business cycles
- Factor in recent role changes or onboarding
- Compare to peer group of similar roles

---

## 3. USER PROFILING & RISK FACTORS

Understanding user context and historical patterns.

### 3.1 User Risk Factor Analysis

**Title:** Assess User's Inherent Risk Based on Role, Access, and History

**Purpose:** Understand whether user's position, access level, and historical indicators elevate their insider threat risk.

**When to Use:** During investigation to contextualize alert and understand what makes this user high-risk.

**SC System Capability Used:** Get User Risk Summary

**Exact Prompt:**
```
What's the risk profile of user <user>? Include their department, role, access level to sensitive data, tenure, any prior policy violations, and whether they have elevated access or administrative privileges.
```

**Expected Output:**
- User role and department
- Access level (normal / elevated / administrative)
- Data sensitivity they can reach
- Tenure and career progression
- Prior policy violations or incidents
- Training status
- Overall risk factor assessment

**Chain-Next:** "Have there been any recent changes in this user's role, access, or employment status?"

**Limitations:**
- Access levels may not reflect actual data sensitivity
- Historical data may be incomplete
- Role-based risk assessment incomplete without context
- Prior incidents may not predict future behavior

**Tips:**
- Prioritize users with database or executive access
- Check for recent role escalation
- Look for promotion denials or performance issues
- Consider departmental risk (finance, HR, legal higher risk)

---

### 3.2 Cross-Policy Behavior View

**Title:** Identify User's Behavior Pattern Across All Insider Risk and DLP Policies

**Purpose:** See complete picture of user's policy interactions to identify whether violations are isolated or pattern.

**When to Use:** During investigation to understand whether current alert is part of broader risk pattern.

**SC System Capability Used:** Get User Risk Summary

**Exact Prompt:**
```
Can you provide a summary of user <user>'s risk profile including their history across all Insider Risk Management policies, DLP violations, information barrier incidents, or other data policy violations in the past 12 months?
```

**Expected Output:**
- IRM policy violations or alerts
- DLP policy matches
- Information barrier incidents
- Unauthorized access attempts
- Prior investigations
- Pattern assessment (improving / stable / deteriorating)
- Training recommendations
- Policy compliance trend

**Chain-Next:** "Are there patterns connecting these incidents across different policy areas?"

**Limitations:**
- Visibility limited to configured policies
- Some policy violations may be false positives
- Cannot determine causation between incidents
- Requires complete incident logging

**Tips:**
- Look for policies triggered by same user repeatedly
- Check for escalation across policies over time
- Correlate with external events (performance issues)
- Assess whether user is learning from prior incidents

---

## 4. CROSS-SIGNAL INVESTIGATION

Combined IRM, DLP, and audit log analysis for complex threat assessment.

### 4.1 IRM Activity + DLP Correlation

**Title:** Correlate IRM Behavioral Indicators with DLP Policy Violations

**Purpose:** Connect insider risk indicators with actual data policy violations to assess exfiltration likelihood.

**When to Use:** When IRM alert exists alongside DLP violations; needs integrated analysis of both signals.

**SC System Capability Used:** Get User Risk Summary + Get Data Risk Summary

**Exact Prompt:**
```
Summarize user <user>'s risk profile and cross-reference with their DLP violations in the past 30 days. Are the DLP policy violations consistent with the Insider Risk indicators? Do they suggest a coordinated data exfiltration or isolated incidents?
```

**Expected Output:**
- IRM alert summary and risk factors
- Correlated DLP violations
- Pattern correlation (related / unrelated)
- Exfiltration likelihood assessment
- Data at risk evaluation
- Combined threat assessment
- Recommended escalation path

**Chain-Next:** "Track sensitive data movement across channels for this user."

**Limitations:**
- IRM and DLP signals may be independent
- Timing correlation not always meaningful
- Cannot prove causation
- Requires both IRM and DLP to be configured

**Tips:**
- Look for timing correlation (data access then DLP match)
- Check for pattern escalation (testing then bulk action)
- Verify business justification for DLP violations
- Correlate with email forwarding rule changes

---

### 4.2 Activity Correlation with Audit Logs

**Title:** Correlate IRM Indicators with Detailed System Audit Activity

**Purpose:** Validate IRM alerts by reviewing detailed audit logs for activity confirmation and timeline accuracy.

**When to Use:** To validate IRM indicators with primary source audit log data; needed before escalation.

**SC System Capability Used:** Get User Risk Summary

**Exact Prompt:**
```
Show key actions performed by user <user> in the last 10 days. Correlate with our detailed audit logs to confirm the timing and nature of file access, downloads, and sharing activities. Are the audit logs consistent with IRM alert indicators?
```

**Expected Output:**
- Activity timeline from audit logs
- Correlation with IRM indicators
- Confirmation or discrepancies
- Detailed activity metadata (IP, device, location)
- Pattern confirmation or debunking
- Confidence in alert accuracy

**Chain-Next:** "Are there unauthorized access attempts or use of other user accounts?"

**Limitations:**
- Audit log detail depends on logging configuration
- Log retention policies may limit historical data
- Some activities may not generate audit events
- Device location/IP may be spoofed

**Tips:**
- Check for unusual IP addresses or locations
- Verify device types (corporate vs. personal)
- Look for after-hours access
- Correlate with employee location data if available

---

## 5. REPORTING & ESCALATION

Communicating findings and facilitating investigation closure or escalation.

### 5.1 Investigation Case Summary for Management

**Title:** Summarize IRM Investigation Findings for Management Review

**Purpose:** Provide investigation summary suitable for management and HR review, with findings and recommendations.

**When to Use:** After completing investigation; needed before closure or disciplinary action discussion.

**SC System Capability Used:** Get User Risk Summary

**Exact Prompt:**
```
Provide a brief case summary of this Insider Risk Management investigation for <user>. Include the alert type, key indicators found, data at risk, whether malicious intent is indicated, and recommended actions (closure, monitoring, escalation, disciplinary referral).
```

**Expected Output:**
- User name and role
- Alert type and initial severity
- Investigation findings (activities confirmed)
- Risk assessment (confirmed / unconfirmed)
- Data at risk (if any)
- Intent assessment (malicious / negligent / unknown)
- Recommended action
- Next review date or closure rationale

**Chain-Next:** "What policy or training recommendations would prevent similar incidents?"

**Limitations:**
- Cannot definitively prove malicious intent
- Recommendations may be escalated to HR/Legal
- Case closure depends on organizational policy
- Some cases remain open pending further evidence

**Tips:**
- Separate facts from interpretations
- Include confidence levels for findings
- Provide options (no action / warning / training / escalation)
- Include timeline of activities

---

### 5.2 Escalation Brief for Legal/HR

**Title:** Formal Escalation Summary for Legal Review and Potential Investigation

**Purpose:** Prepare case for escalation to Legal or HR when malicious intent is indicated or disciplinary action possible.

**When to Use:** When investigation findings suggest potential policy violation, data theft, or misconduct; formal escalation needed.

**SC System Capability Used:** Get User Risk Summary

**Exact Prompt:**
```
Prepare a formal escalation brief for Legal/HR review regarding user <user>. Include confirmed evidence of policy violations, timeline of incidents, data at risk, indicators of intent, and recommended investigation or disciplinary actions.
```

**Expected Output:**
- Formal case header
- Confirmed policy violations (with evidence)
- Timeline of incident
- Data at risk assessment
- Evidence of intent (if applicable)
- Risk mitigation taken (access revoked, etc.)
- Recommended next steps
- Legal/HR considerations
- Preservation requirements (email, files, logs)

**Chain-Next:** "What additional evidence should be preserved for potential legal proceedings?"

**Limitations:**
- Legal review may dispute findings
- Disciplinary decisions made by HR, not security
- Evidence preservation has legal requirements
- Employee rights must be considered

**Tips:**
- Stick to facts, avoid speculation
- Reference specific evidence, not interpretations
- Follow internal escalation procedures
- Coordinate with Legal on evidence handling
- Document all findings thoroughly

---

## IRM Investigation Promptbook: Complete Sequential Chain

This section provides a complete, step-by-step prompt sequence that CSAs can use as a ready-to-run investigation workflow. Each prompt output feeds the next prompt as input.

### Scenario
Michael Rodriguez from Finance has triggered an Insider Risk Management alert. The IRM system detected a sequence: unusual late-night access to data warehouse, download of large dataset (500MB), access to competitor research files, and unusual external email forwarding rule. Michael is a Senior Analyst with 5 years tenure, no prior incidents. His department indicated he's under performance review.

### Step 1: Alert Triage and Risk Assessment
**Prompt:**
```
Show me the Insider Risk Management alerts for Michael Rodriguez. What is his risk profile and should this alert be investigated immediately or scheduled for review?
```
**Output:** Michael Rodriguez (Senior Financial Analyst, Finance, 5 years tenure) triggered sequence detection alert with score 72/100. Risk factors: late-night access, large data download, competitor file access. Mitigating factors: long tenure, no prior incidents, trusted role. Recommendation: Schedule investigation, not critical emergency. Risk level: Medium-High.

---

### Step 2: Detailed User Profile
**Prompt:**
```
What's the risk profile of user Michael Rodriguez? Include his department, role, access level to sensitive data, tenure, any prior policy violations, and whether he has elevated access.
```
**Output:** Michael Rodriguez, Senior Financial Analyst, Finance Department, 5-year tenure. Access to financial data systems, market analysis databases, M&A file shares. Elevated access to pricing data and competitor research. No prior IRM incidents or DLP violations. Recently placed on performance review (3 weeks ago). Normal access level for role, no administrative privileges.

---

### Step 3: Activity Timeline Reconstruction
**Prompt:**
```
List all the sequential activities involving Michael Rodriguez over the past 30 days. Include file access, downloads, sharing actions, email forwarding, and sign-in patterns. Highlight any activities that deviate from normal baseline.
```
**Output:** Timeline shows unusual escalation:
- Week 1-2: Normal access (financial reports, market data)
- Week 3: First unusual activity - late-night access (11 PM) to data warehouse (unusual timing)
- Week 3: Downloaded dataset (500MB) - significantly larger than typical downloads (baseline: 50-100MB weekly)
- Week 3: Accessed competitor research files (CompetitorX_Strategy.docx) - first access to this file in 6 months
- Week 4: Added external email forwarding rule to personal Gmail account
- Current: Searched email for keywords "job search," "consulting," "recruitment"

---

### Step 4: Baseline Comparison
**Prompt:**
```
Did Michael Rodriguez engage in any unusual behavior? Compare his activity pattern in the past 30 days to his baseline behavior from the prior 6 months. Highlight activities that are truly anomalous.
```
**Output:** Baseline (prior 6 months): Regular daytime access, typical downloads 50-100MB/week, minimal late-night activity (1-2 events), competitor research accessed 2-3 times/month normally. Current behavior (past 30 days): Statistically anomalous - late-night access (5 events), single 500MB download (10x normal), competitor research access doubled, email forwarding rule new. Confidence: High that behavior deviates from baseline.

---

### Step 5: Exfiltration Assessment
**Prompt:**
```
List all the data exfiltration activities involving Michael Rodriguez in the past 30 days. Include attempts to download sensitive files, share externally, forward to external email, or upload to personal cloud storage.
```
**Output:** Exfiltration attempts identified:
1. Large dataset download (500MB) to local drive (timestamp: 11 PM, 3 days ago) - Source: Financial Data Warehouse
2. Email forwarding rule created to personal.gmail@gmail.com (timestamp: 2 days ago) - affects all Finance inbox items
3. File access: CompetitorX_Strategy.docx viewed (timestamp: 4 days ago) - viewed 3 times in past week (baseline: <1 per month)
4. No successful external shares detected (DLP policies blocked attempts, if any)

---

### Step 6: Intent and Escalation Assessment
**Prompt:**
```
Show key actions performed by Michael Rodriguez in the last 10 days. Do these activities form an escalating pattern (reconnaissance, testing, bulk gathering) or are they isolated incidents? What would the next logical step be?
```
**Output:** Activities show escalating pattern:
- Testing phase: Email forwarding rule (tests exfiltration channel)
- Reconnaissance: Competitor research access (identifies valuable data)
- Gathering phase: 500MB dataset download (bulk data acquisition)
- Next likely step: Data exfiltration through forwarding rule or cloud storage, likely external sharing with competitor or personal use

Pattern Confidence: High. Indicators suggest planned activity, not impulsive/accidental. Timeline consistent with job search indicators from email search.

---

### Step 7: Obfuscation Check
**Prompt:**
```
Did Michael Rodriguez engage in any unusual behavior? Specifically, look for evidence of intentional obfuscation such as deleting emails, using external email accounts, accessing logs, or attempting to hide activities.
```
**Output:** Obfuscation indicators found:
- Email forwarding rule to personal account (bypasses organization email audit)
- Searched email for deletion keywords (job, consulting, etc.) - may indicate cleanup activities
- Large download during off-hours (11 PM) - timing suggests intentional concealment
- No log access or deletion detected (user doesn't have admin access)

Evidence of intentional hiding: Moderate confidence. Activities show planning to avoid detection via email forwarding to personal account.

---

### Step 8: Cross-Signal DLP Check
**Prompt:**
```
Summarize Michael Rodriguez's risk profile and cross-reference with his DLP violations in the past 30 days. Are DLP violations consistent with IRM indicators and suggest coordinated exfiltration?
```
**Output:** DLP violations: None detected (no external sharing was successful, possibly blocked by policy or not yet attempted). DLP correlation: Email forwarding rule to personal account represents exfiltration channel that would bypass email DLP. Financial dataset download flagged by IRM but not DLP (data warehouse access less monitored). Assessment: No successful exfiltration detected yet, but infrastructure and opportunity indicators present. High likelihood of future DLP matches if dataset is shared externally.

---

### Step 9: Risk Mitigation and Closure Decision
**Prompt:**
```
Provide a brief case summary of this Insider Risk Management investigation for Michael Rodriguez. Include the alert type, key indicators found, data at risk, whether malicious intent is indicated, and recommended actions.
```
**Output:**

CASE SUMMARY: Michael Rodriguez, Senior Financial Analyst

Alert Type: Sequence Detection (suspicious activity pattern)

Key Findings:
- Confirmed: Unusual late-night data warehouse access and 500MB dataset download
- Confirmed: Email forwarding rule to personal Gmail account created
- Confirmed: Competitor research file accessed 3x in past week (baseline <1/month)
- Email keyword search indicates job search activity
- Performance review initiated 3 weeks ago (contextual risk factor)

Data at Risk: Financial dataset (500MB), competitor research files, potentially pricing/M&A data

Malicious Intent: Moderate confidence. Pattern shows planning (forwarding rule), reconnaissance (competitor research), and data gathering. Job search indicators support intent to take data. However, no confirmed exfiltration (no external sharing detected yet).

Recommended Actions:
1. Immediate: Disable external email forwarding rule
2. Monitor: Continue tracking access patterns during investigation
3. Escalate: Refer to HR regarding performance review context and suspected job search
4. Alert: If external sharing activity detected, escalate to Legal

---

### Step 10: Escalation Brief (if warranted)
**Prompt:**
```
Prepare a formal escalation brief for HR review regarding Michael Rodriguez. Include confirmed evidence of policy violations, timeline of incidents, data at risk, indicators of intent, and recommended investigation or disciplinary actions.
```
**Output:**

FORMAL ESCALATION BRIEF: Michael Rodriguez

User: Michael Rodriguez, Senior Financial Analyst, Finance Department

Confirmed Evidence:
1. Unauthorized email forwarding rule to personal Gmail (policy violation)
2. Late-night access to data warehouse (5 instances, off-hours access policy)
3. Anomalous large data download (500MB, 10x baseline)
4. Unexplained competitor research access escalation

Timeline:
- 3 weeks ago: Performance review initiated
- 10 days ago: Late-night data warehouse access begins
- 4 days ago: Email forwarding rule created
- 3 days ago: 500MB dataset download (late-night)

Data at Risk: Financial dataset, competitor research, potentially pricing/M&A sensitive data

Intent Indicators: Pattern suggests planning and intentional data gathering in preparation for external use (likely competitive activity or vendor engagement). Job search indicators found in email searches.

Recommended Actions:
1. Revoke email forwarding rule and monitor for recreation
2. Review permissions on financial dataset - consider access revocation
3. Interview Michael Rodriguez regarding forwarding rule and data download (be prepared for termination discussion)
4. Preserve all email, audit logs, and file access records pending investigation

Risk Mitigation: Consider immediate access restrictions to sensitive data pending HR investigation outcome.

---

## Tips for Using These Prompts Effectively

1. **Understand IRM Limitations**: IRM alerts are indicators, not proof of malicious intent. Always validate with audit logs and business context.

2. **Consider Context**: Performance reviews, role changes, and employment status significantly impact risk assessment. Always ask about recent organizational events.

3. **Validate Baselines**: Ensure you have 6+ months of baseline data before concluding activity is anomalous.

4. **Chain Prompts Logically**: Use the investigation chain above as template. Move from triage → activity timeline → anomaly assessment → intent evaluation.

5. **Escalate Appropriately**: Separate security findings from HR/Legal decisions. Escalate when data risk or policy violation is confirmed.

6. **Preserve Evidence**: Before escalating, ensure all relevant logs, emails, and files are preserved per legal requirements.

7. **Document Thoroughly**: Maintain detailed notes of investigation steps, findings, and decisions for potential legal proceedings.

8. **Respect Employee Rights**: Even with evidence, termination and disciplinary actions are HR decisions, not security decisions.

---

**Document Version**: 2.0
**Last Updated**: April 2026
**Status**: Validated against Security Copilot with Purview plugin
**Note**: All prompts reflect confirmed Security Copilot capabilities as of April 2026.
