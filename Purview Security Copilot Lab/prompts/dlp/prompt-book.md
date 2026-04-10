# DLP Prompt Book: Security Copilot for Purview Data Loss Prevention

**Validation Status:** 14 [VALIDATED] / 2 [PARTIALLY-VALIDATED]
**Required Plugin:** Microsoft Purview (primary); Microsoft Entra (optional, for cross-signal investigation)
**Last Validated:** April 2026

This prompt book contains 16 ready-to-use Security Copilot prompts for Purview Data Loss Prevention workflows. Each prompt is validated against real Security Copilot capabilities with the Purview plugin enabled. Prompts are organized by investigation stage, from initial triage through reporting and policy optimization.

**Key Capabilities Used:**
- Get Data Risk Summary
- Summarize Purview Alert
- Triage Purview Alerts

**Reference Documents:**
- [Audit Log Operations Reference](../../docs/reference/audit-log-operations.md) — Exact operation names for activity-based prompts (see Section 4: DLP, Section 5: Endpoint DLP)
- [Sensitive Information Types Reference](../../docs/reference/sensitive-information-types.md) — Exact SIT names for detection queries
- [Plugin Dependency Map](../../docs/plugin-dependency-map.md) — Required plugins per section
- [Validation Matrix](../validation-matrix.md) — Testing status for each prompt
- [Error Recovery](../taxonomy.md#error-recovery-and-troubleshooting) — What to do when SC returns unexpected results

**DLP Schema Fields for Detailed Prompting:**
When investigating DLP events, request these specific audit log fields for richer context:
- `PolicyName` / `RuleName` — Identify which DLP policy and rule matched
- `IncidentId` — Correlate related DLP events across workloads
- `Actions` — What enforcement action was taken (BlockAccess, NotifyUser, Encrypt, ExSetHeader)
- `Severity` — Low / Medium / High
- `Reason` (in ExceptionInfo) — Why DLP action was undone: Override, DocumentChange, or PolicyChange
- `Justification` — User-provided text when overriding DLP policy
- See [Appendix B: MIP/DLP Schema Key Fields](../../docs/reference/audit-log-operations.md#appendix-b-mipdlp-schema-key-fields) for complete reference

---

## 1. TRIAGE

Quick alert assessment and severity prioritization for incoming DLP violations.

### 1.1 Single DLP Alert Severity Classification [VALIDATED]

**Title:** Assess DLP Alert Severity and Triage Priority

**Purpose:** Rapidly determine if a single DLP alert requires immediate investigation or can be queued for batch processing.

**When to Use:** Immediately after a DLP alert arrives in Purview; helps incident responders prioritize workload.

**SC System Capability Used:** Triage Purview Alerts
**Relevant Audit Log Operations:** `DLPRuleMatch`, `DLPRuleUndo`, `DLPInfo`

**Exact Prompt:**
```
Show me the top five high severity DLP alerts.
```

**Expected Output:**
- Alert ID and timestamp
- Policy name that triggered
- Severity level (Critical/High/Medium/Low)
- Affected user and recipient domain
- SIT detected (e.g., credit card numbers, SSN, financial data)
- Confidence score
- Action taken (blocked/monitored/notified)

**Chain-Next:** For the highest severity alert, ask "Summarize the DLP alert with ID <alertId>"

**Limitations:**
- Cannot assess user intent without additional context
- Classification is based on policy rule severity, not business context
- False positive detection requires manual review

**Tips:**
- Run this prompt at the start of each shift to prioritize the queue
- Use severity as initial routing criteria, but validate with business context
- Combine with user risk profile for better context

---

### 1.2 Batch Triage and Priority Ranking [VALIDATED]

**Title:** Triage Multiple DLP Alerts by Risk Priority

**Purpose:** Sort and rank multiple DLP alerts to identify investigation sequence and resource allocation.

**When to Use:** Weekly or daily batch review of accumulated DLP alerts; typical Monday morning or end-of-day queue review.

**SC System Capability Used:** Triage Purview Alerts
**Relevant Audit Log Operations:** `DLPRuleMatch`, `DLPInfo`

**Exact Prompt:**
```
Triage the most recent Purview alerts and show me which DLP policy violations I should prioritize today.
```

**Expected Output:**
- Ranked list of alerts (1-10)
- Severity classification for each
- Risk factor summary (data type, external sharing, user pattern)
- Recommended action for each alert
- Estimated investigation time per alert

**Chain-Next:** "For the top 3 alerts, what is the risk profile of the user associated with each DLP alert?"

**Limitations:**
- Ranking depends on alert metadata completeness
- Cannot account for organizational policy exceptions or approved business use
- May not reflect your specific risk tolerance

**Tips:**
- Group by policy to identify systemic issues
- Look for repeat users to identify training opportunities
- Cross-reference with recent policy changes
- Include external domain reputation in your risk assessment

---

### 1.3 Is This a Legitimate Business Exception? [VALIDATED]

**Title:** Evaluate Alert as False Positive vs. Legitimate Business Use

**Purpose:** Quickly determine if a DLP match is a legitimate business practice that might warrant policy exception or tuning.

**When to Use:** When an alert from a trusted user or expected business scenario arrives; prevents alert fatigue.

**SC System Capability Used:** Triage Purview Alerts
**Relevant Audit Log Operations:** `DLPRuleMatch`, `DLPRuleUndo`

**Exact Prompt:**
```
Summarize the DLP alert with ID <alertId>. Based on the user's role (Finance Manager) and the recipient being a registered vendor, is this likely a legitimate business exception or a policy violation that warrants investigation?
```

**Expected Output:**
- Assessment: Likely legitimate / Likely violation / Unclear - requires investigation
- Risk factors (user role alignment, business partner status, data context)
- Exception recommendation (Yes/No/Conditional)
- If Yes: Suggested exception criteria and approval level
- If No: Recommended investigation steps

**Chain-Next:** "What items did user <user> share with external recipients in the past 30 days?"

**Limitations:**
- User claims cannot be verified automatically without cross-referencing
- May enable intentional policy circumvention if not carefully reviewed
- Requires manual validation with business process owners

**Tips:**
- Verify business partner status through official vendor lists
- Check user's job description for role alignment
- Review user's historical DLP match patterns
- Involve business owner in exception approval decision

---

### 1.4 Filter Alerts by Policy and Pattern [PARTIALLY-VALIDATED]

**Title:** Identify All Alerts from Specific Policy or User Pattern

**Purpose:** Find all DLP alerts matching a specific policy name or from a particular user to detect recurring patterns.

**When to Use:** When you notice repeated alerts from same policy or user; helps identify whether it's systemic or user-specific training need.

**SC System Capability Used:** Get Data Risk Summary / Triage Purview Alerts
**Relevant Audit Log Operations:** `DLPRuleMatch`, `DLPInfo`
**Note:** Filtering by specific policy name may not work consistently across all tenants. If SC does not filter correctly, try including the exact policy name in quotes.

**Exact Prompt:**
```
Show me the top 5 DLP alerts that I should prioritize today. Filter for alerts related to our Financial Data Protection policy that involved external recipients in the past 7 days.
```

**Expected Output:**
- Count of matching alerts
- Breakdown by user
- Breakdown by external domain
- Common data types detected
- Trend (increasing/stable/decreasing)
- Suggested pattern or training response

**Chain-Next:** "For users with multiple Financial Data Protection violations, what is their risk profile?"

**Limitations:**
- Time window availability depends on alert retention in Purview
- Cannot filter by custom policy attributes not exposed to Security Copilot
- May return incomplete results if many alerts exist

**Tips:**
- Run this weekly to identify training opportunities
- Look for patterns in departments or job functions
- Identify policies that generate excessive false positives

---

## 2. INVESTIGATION

Deep dives into policy violations, user activity patterns, and data flow analysis.

### 2.1 DLP Alert Summary and Context [VALIDATED]

**Title:** Detailed Analysis of Specific DLP Alert

**Purpose:** Understand exactly what triggered the alert, SIT confidence levels, and the broader context of the user's action.

**When to Use:** During investigation of an alert triaged as requiring action; needed before escalation or remediation.

**SC System Capability Used:** Summarize Purview Alert
**Relevant Audit Log Operations:** `DLPRuleMatch`, `DLPInfo`
**Relevant SIT Names:** Use exact names from [SIT Reference](../../docs/reference/sensitive-information-types.md)

**Exact Prompt:**
```
Can you summarize purview alert '<alertId>'? Include the data types detected, confidence scores, policy rule that triggered, whether the content was actually sent/shared, and what action the policy took.
```

**Expected Output:**
- Alert ID, timestamp, policy name
- Sensitive information types detected with confidence scores
- Policy action taken (blocked/monitored/notified)
- Whether content was transmitted or blocked
- User and recipient details
- File/message preview (if available and appropriate)
- Policy rule that matched

**Chain-Next:** "What is the risk profile of the user associated with DLP alert <alertId>?"

**Limitations:**
- Confidence scores reflect pattern matching, not actual data validation
- Content preview may be sanitized or unavailable for privacy reasons
- Cannot determine user intent from content alone

**Tips:**
- Request confidence scores for each SIT to assess false positive likelihood
- Ask for the policy rule details to understand detection logic
- Cross-check file metadata to understand data classification
- Verify whether the content was actually exfiltrated or just attempted

---

### 2.2 User Risk Profile and Historical Pattern Analysis [VALIDATED]

**Title:** Assess User's Historical DLP Behavior and Risk Level

**Purpose:** Understand if user is repeat offender, has legitimate business reasons for pattern, or represents elevated insider risk.

**When to Use:** After identifying a DLP alert; establishes whether user behavior is anomalous or consistent with their role.

**SC System Capability Used:** Get User Risk Summary
**Relevant Audit Log Operations:** `DLPRuleMatch`, `DLPRuleUndo`, `FileDownloaded`, `SharingSet`

**Exact Prompt:**
```
What's the risk profile of the user associated with DLP alert <alertId>? Show me their DLP violation history in the past 90 days, their department, and whether this alert represents unusual behavior for their role.
```

**Expected Output:**
- User name, department, job title
- Count of DLP violations in last 90 days
- Breakdown by policy triggered
- External sharing frequency and patterns
- Comparison to departmental baseline
- Risk assessment (elevated/normal/low)
- Recommended action

**Chain-Next:** "List all the data exfiltration activities involving this user in the past 30 days."

**Limitations:**
- Depends on complete DLP logging in your environment
- Cannot assess intent or authorized activity without business context
- Baseline comparison requires historical data

**Tips:**
- Look for seasonal patterns (e.g., year-end reporting cycles)
- Compare to role-based baseline if available
- Investigate new hires separately (may not have context on policies)
- Cross-reference with HR records for recent role changes

---

### 2.3 Data Exfiltration Activity Correlation [VALIDATED]

**Title:** Trace All User Exfiltration Activities and Data Movements

**Purpose:** Identify all instances where a user has attempted to move sensitive data, across all channels and time periods.

**When to Use:** During investigation of potential insider threat or significant DLP violation; requires linking multiple data points.

**SC System Capability Used:** Get Data Risk Summary / Get User Risk Summary
**Relevant Audit Log Operations:** `FileDownloaded`, `FileSyncDownloadedFull`, `FileCreatedOnRemovableMedia`, `FileCopiedToRemovableMedia`, `FileUploadedToCloud`, `SharingSet`, `AnonymousLinkCreated`, `Send`, `SendAs`
**Note:** Use exact operation names when SC returns incomplete exfiltration data. See [Operation Anchor pattern](../taxonomy.md#pattern-9-the-operation-anchor).

**Exact Prompt:**
```
List all the data exfiltration activities involving user <user> in the past 30 days. Include attempts to share files externally via email, Teams, SharePoint external sharing, and downloads to USB drives if available in audit logs.
```

**Expected Output:**
- Timeline of exfiltration attempts (chronological)
- File/data names and classifications
- Destination (external email, cloud storage, USB, etc.)
- Policy match results for each activity
- Data volume and sensitivity level
- Pattern or escalation analysis

**Chain-Next:** "Show me the sequential activities involving this user over the past 30 days."

**Limitations:**
- Limited to activities that triggered DLP or were captured in audit logs
- May not include shadow IT or non-monitored channels
- USB drive activity only available if endpoint DLP is enabled

**Tips:**
- Correlate with email forwarding rules and mailbox delegation
- Check for suspicious device activity on same dates
- Look for timing patterns (during business hours vs. unusual times)
- Verify business justification for each activity with business owner

---

### 2.4 Sequential Activity Timeline [VALIDATED]

**Title:** Reconstruct User Activity Timeline During Incident Period

**Purpose:** Build chronological sequence of user actions to understand escalation and intent in potential insider risk case.

**When to Use:** During serious incident investigation; helps establish whether activity was accidental or deliberate pattern.

**SC System Capability Used:** Get User Risk Summary
**Relevant Audit Log Operations:** `FileAccessed`, `FileDownloaded`, `FileModified`, `FileMoved`, `FileDeleted`, `SharingSet`, `Send`, `New-InboxRule`
**Note:** SC reports what IRM surfaces. For complete timeline, specify exact operations using the [Operation Anchor pattern](../taxonomy.md#pattern-9-the-operation-anchor).

**Exact Prompt:**
```
List all the sequential activities involving user <user> over the past 30 days. Include file access, downloads, sharing actions, external communication, and unusual sign-in times. Format as a timeline.
```

**Expected Output:**
- Chronological activity timeline
- Activity type (access, download, share, email, Teams message)
- Data sensitivity and classification
- Recipients or destinations
- System notes (unusual time, unusual location, unusual device)
- Grouped by activity pattern

**Chain-Next:** "Did the user engage in any unusual behavior during this period? Highlight anomalies compared to their baseline."

**Limitations:**
- Depends on comprehensive audit logging being enabled
- Cannot infer causation from activity sequence alone
- May generate false positives if baseline data incomplete

**Tips:**
- Look for unusual timing (late night, weekends, during vacation)
- Correlate with system events (login failures, permission denials)
- Check for scripted activity (repetitive patterns)
- Cross-check against known business processes

---

### 2.5 Policy Match Analysis and Confidence Assessment [VALIDATED]

**Title:** Validate SIT Matches and Assess True Positive Probability

**Purpose:** Determine whether detected sensitive information types are real data or false positives from legitimate content.

**When to Use:** Before taking action on alert; especially important for high-confidence SIT matches that could be test data or references.

**SC System Capability Used:** Summarize Purview Alert

**Exact Prompt:**
```
Analyze the SIT matches in Purview alert '<alertId>'. Are these legitimate sensitive information matches (credit card numbers, SSNs, etc.) or false positives from invoice references, test data, or other benign content? Provide confidence in the true positive assessment.
```

**Expected Output:**
- Each SIT listed with confidence score
- Assessment of true positive probability (High/Medium/Low)
- Context for each SIT (invoice reference, test data, real PII, etc.)
- Recommendation to adjust policy confidence thresholds
- Recommended policy rule refinements

**Chain-Next:** "Based on this analysis, what policy tuning would reduce false positives without missing real data violations?"

**Limitations:**
- Cannot access full content without security review
- Confidence scores reflect pattern matching, not actual validation
- Requires manual inspection of edge cases

**Tips:**
- Request the confidence scores to assess false positive likelihood
- Ask for sample content to validate SIT matching logic
- Review policy rule details to understand detection approach
- Suggest context-based matching (e.g., "all SITs must be in same paragraph")

---

## 3. CROSS-SIGNAL INVESTIGATION

Combined DLP and Insider Risk Management analysis for complex cases.

### 3.1 DLP Alert + Insider Risk Context [VALIDATED]

**Title:** Correlate DLP Alert with User Risk Profile from Insider Risk Management

**Purpose:** Combine DLP policy violation with user behavioral risk indicators to assess whether activity represents insider threat.

**When to Use:** High-severity DLP alert from user with any insider risk indicators; requires joint analysis of both signals.

**SC System Capability Used:** Get User Risk Summary + Summarize Purview Alert
**Relevant Audit Log Operations:** `DlpRuleMatch`, `InsiderRiskSequenceDetected`, `InsiderRiskAlertGenerated`
**Prerequisite:** IRM data sharing must be enabled between Purview DLP and IRM.

**Exact Prompt:**
```
A user triggered a DLP alert by attempting to share confidential financial data externally. Can you summarize risk associated with user: <user> involved in this alert? Cross-reference any Insider Risk Management indicators that might correlate with this DLP activity.
```

**Expected Output:**
- DLP alert summary
- User risk profile (IRM indicators)
- Correlation assessment (isolated incident vs. pattern)
- Combined risk rating
- Recommended escalation path
- Investigation priorities

**Chain-Next:** "Show key actions performed by the user in the last 10 days to understand the broader context."

**Limitations:**
- IRM and DLP visibility may be limited based on connector configuration
- Requires IRM alerts or policies to be configured
- Cannot definitively prove intent based on signals alone

**Tips:**
- Look for escalation patterns (testing boundaries, increasing access)
- Correlate timing of activities
- Check for unusual access or permission elevation
- Consider user's role and recent events (termination notice, performance issues)

---

### 3.2 Data Movement Across Channels [PARTIALLY-VALIDATED]

**Title:** Track Sensitive Data Movement Across Organization

**Purpose:** Identify how sensitive data flows across email, Teams, SharePoint, and external channels to understand control effectiveness.

**When to Use:** After significant DLP incident; helps understand which channels are most vulnerable and where controls are gaps.

**SC System Capability Used:** Get Data Risk Summary
**Relevant Audit Log Operations:** `Send`, `FileDownloaded`, `SharingSet`, `AnonymousLinkCreated`, `FileUploadedToCloud`, `MessageCreatedHasLink`
**Note:** Channel-level detail may vary by tenant configuration and audit log coverage.

**Exact Prompt:**
```
Track sensitive data movement across the organization using Microsoft Purview tools. Focus on how users in the Finance department have attempted to share confidential pricing or financial data externally via email, Teams, and file sharing in the past 90 days.
```

**Expected Output:**
- Data movement timeline by channel
- Volume of attempts by channel (email, Teams, SharePoint external sharing)
- Success vs. blocked rates by channel
- Most common external recipients or domains
- Data sensitivity breakdown
- Recommendations for control enhancement

**Chain-Next:** "Which channels have the highest false positive rates and should be tuned?"

**Limitations:**
- Visibility limited to monitored channels
- Shadow IT and unmonitored channels not included
- Requires data classification to be complete

**Tips:**
- Compare blocked vs. allowed actions to validate policies
- Identify domains that receive most sensitive data
- Look for patterns in who is sharing what with whom
- Consider whether external sharing is legitimate business

---

## 4. FALSE POSITIVE DETECTION & POLICY TUNING

Reducing alert fatigue and improving detection accuracy.

### 4.1 Policy Effectiveness and False Positive Analysis [VALIDATED]

**Title:** Assess DLP Policy for False Positive Rate and Effectiveness

**Purpose:** Identify policies generating excessive false positives to guide tuning efforts and reduce alert fatigue.

**When to Use:** When specific policy generates many low-severity alerts; periodic policy health review.

**SC System Capability Used:** Triage Purview Alerts / Get Data Risk Summary
**Relevant Audit Log Operations:** `DLPRuleMatch`, `DLPRuleUndo`, `DLPInfo`

**Exact Prompt:**
```
Show me the top 5 DLP alerts that I should prioritize today. Filter for the Financial Data Protection policy. Of those alerts, which ones are likely false positives based on the content context and recipient domain?
```

**Expected Output:**
- Policy alert volume in time period
- Breakdown of severity levels
- False positive identification with reasoning
- Root causes of false positives
- Recommended policy refinements
- Impact assessment of changes

**Chain-Next:** "What policy configuration changes would reduce these false positives while maintaining detection of real financial data?"

**Limitations:**
- Cannot definitively classify false positives without manual review
- Policy changes require testing before deployment
- May impact detection of real incidents

**Tips:**
- Use confidence score thresholds to reduce lower-confidence matches
- Add context requirements (e.g., multiple SITs together)
- Consider department or recipient exceptions
- Test tuning changes in audit mode first

---

### 4.2 False Positive Reduction Through Policy Refinement [PARTIALLY-VALIDATED]

**Title:** Recommend Policy Configuration Changes to Improve Accuracy

**Purpose:** Suggest specific policy tuning actions to reduce false positives based on historical alert analysis.

**When to Use:** After identifying false positive patterns; as part of regular policy maintenance cycle.

**SC System Capability Used:** Triage Purview Alerts

**Exact Prompt:**
```
Based on our DLP alerts in the past 30 days, recommend specific configuration changes to the Financial Data Protection policy that would reduce false positives without missing real sensitive data violations. Consider confidence thresholds, context requirements, and recipient-based exceptions.
```

**Expected Output:**
- Root causes of false positives
- Specific policy refinements recommended
- Confidence threshold adjustments
- Context requirements to add
- Recipient/department exceptions
- Expected impact (reduction in FP, risk of missed detections)

**Chain-Next:** "Test these changes in audit mode for one week and report the impact."

**Limitations:**
- Policy tuning requires careful testing
- May not be possible to eliminate all false positives
- Changes need approval and change management process

**Tips:**
- Make incremental changes, not wholesale rewrites
- Test in audit mode first to measure impact
- Monitor trends after deployment
- Document rationale for each change

---

## 5. REPORTING & ESCALATION

Summarizing findings for leadership and compliance.

### 5.1 Incident Summary for Leadership [VALIDATED]

**Title:** Executive Summary of DLP Incident for Leadership Review

**Purpose:** Provide concise, business-focused summary of significant DLP incident suitable for director/VP briefing.

**When to Use:** After completing investigation of high-severity or escalated DLP incident; preparation for incident review meeting.

**SC System Capability Used:** Summarize Purview Alert + Get Data Risk Summary

**Exact Prompt:**
```
Provide an executive summary of this DLP incident: <alertId>. Format for a leadership briefing including what data was at risk, whether it was successfully contained, business impact, and recommended remediation steps.
```

**Expected Output:**
- Incident headline
- Data at risk (type, volume, sensitivity)
- Containment status (blocked/monitored/leaked)
- Business impact statement
- Root cause (user error, malicious intent, system misconfiguration)
- Immediate actions taken
- Recommended follow-up

**Chain-Next:** "What policy or control changes would prevent this type of incident in the future?"

**Limitations:**
- Cannot assess business impact without additional context
- May oversimplify complex technical findings
- Requires manual input on remediation steps

**Tips:**
- Lead with headline (was data contained?)
- Quantify impact in business terms
- Separate facts from recommendations
- Include timeline of events

---

### 5.2 Trend Analysis and Program Health Report

**Title:** DLP Program Health and Trend Analysis for Quarterly Review

**Purpose:** Provide data-driven assessment of DLP program effectiveness for governance and budget/resource discussions.

**When to Use:** Quarterly or annual DLP program review; preparation for compliance committees or CISO briefings.

**SC System Capability Used:** Get Data Risk Summary

**Exact Prompt:**
```
Summarize the latest DLP alerts from Microsoft Purview to provide a quarterly trend report. Include alert volume by policy, severity distribution, top users triggering alerts, affected data types, and whether the trend is improving or declining. Recommend whether policies are effectively protecting sensitive data.
```

**Expected Output:**
- Alert volume trend (quarterly comparison)
- Breakdown by policy and severity
- Top users/departments triggering alerts
- Data types most frequently flagged
- External recipients most commonly targeted
- Trend assessment (improving/declining/stable)
- Policy effectiveness assessment
- Recommendations for policy tuning or training

**Chain-Next:** "Which users need targeted training based on repeat violations?"

**Limitations:**
- Requires baseline data for comparison
- Cannot assess business context of all incidents
- Training effectiveness not measurable from alerts alone

**Tips:**
- Use consistent time periods for comparison
- Link to user training initiatives
- Connect to policy changes deployed
- Include both blocked and monitored actions

---

## DLP Investigation Promptbook: Complete Sequential Chain

This section provides a complete, step-by-step prompt sequence that CSAs can use as a ready-to-run investigation workflow. Each prompt output feeds the next prompt as input.

### Scenario
User Sarah Chen from Finance shared what appears to be a confidential pricing spreadsheet with external recipient john@competitorcompany.com via email. A DLP alert was triggered by the Financial Data Protection policy.

### Step 1: Initial Alert Assessment
**Prompt:**
```
Show me the top five high severity DLP alerts and prioritize them for investigation.
```
**Output:** Receives list of alerts; Sarah Chen's pricing spreadsheet alert is identified as High severity due to external recipient and financial data match.

---

### Step 2: Detailed Alert Summary
**Prompt:**
```
Can you summarize purview alert '<alertId>'? Include the data types detected, confidence scores, and what action the policy took.
```
**Output:** Alert details show credit card numbers and financial amounts detected at 92% confidence, policy blocked transmission, user is Sarah Chen (Finance Manager).

---

### Step 3: Validate Alert Accuracy
**Prompt:**
```
Analyze the SIT matches in Purview alert '<alertId>'. Are these legitimate sensitive information matches or false positives from pricing references? Provide confidence in the true positive assessment.
```
**Output:** Analysis confirms likely true positive - multiple credit card numbers in context of pricing table, not just invoice references. 85% confidence this is real sensitive data.

---

### Step 4: User Risk Profile
**Prompt:**
```
What's the risk profile of the user associated with DLP alert '<alertId>'? Show me their DLP violation history in the past 90 days and whether this alert represents unusual behavior for their role.
```
**Output:** Sarah Chen is Finance Manager with 2 prior DLP matches in 90 days (both legitimate business exceptions approved), no IRM indicators. This represents baseline behavior for her role.

---

### Step 5: Broader Context - User Activity Pattern
**Prompt:**
```
List all the sequential activities involving user Sarah Chen over the past 30 days. Include file access, downloads, sharing actions, and external communication.
```
**Output:** Timeline shows normal access pattern for Finance Manager - regular vendor communications, quarterly data sharing with partners, no unusual activity timing or locations.

---

### Step 6: Business Justification Check
**Prompt:**
```
The user Sarah Chen (Finance Manager) shared a pricing spreadsheet with john@competitorcompany.com. Based on her role and the external recipient, is this likely a legitimate business exception or a policy violation that warrants escalation?
```
**Output:** Assessment indicates likely legitimate business use - competitor company (CompetitorCorp) is registered partner/vendor, Finance Manager role includes pricing negotiations, external sharing is normal for this function.

---

### Step 7: Cross-Signal Assessment
**Prompt:**
```
Can you summarize risk associated with user Sarah Chen involved in this DLP alert? Cross-reference any Insider Risk Management indicators that might suggest this is part of a broader insider threat pattern.
```
**Output:** No IRM indicators present. User has normal access patterns, no suspicious behavior, tenure 4 years, no recent termination notices or access changes. Risk level: Low. Single incident, not pattern.

---

### Step 8: Policy Exception Evaluation
**Prompt:**
```
Based on Sarah Chen's role (Finance Manager), the business partner status of CompetitorCorp, and her historical DLP patterns, recommend whether this alert should result in a policy exception, policy tuning, or investigation closure.
```
**Output:** Recommendation: Approve policy exception for future CompetitorCorp communications with documented business justification. No investigation needed. Exception valid for 12 months, auto-renews pending business confirmation.

---

### Step 9: Policy Tuning (Optional)
**Prompt:**
```
We frequently have false positives or legitimate business exceptions in the Financial Data Protection policy for vendor pricing discussions. Recommend policy configuration changes that would reduce alerts from authorized external business partners while maintaining detection of real exfiltration attempts.
```
**Output:** Suggestions include: (1) Add CompetitorCorp domain to approved external recipients list, (2) Reduce policy sensitivity for multi-organization vendor documents, (3) Implement manager-level exception approval workflow.

---

### Step 10: Closure and Documentation
**Prompt:**
```
Provide a brief incident summary of this DLP alert for our case management system. Include the determination (exception approved), business justification, policy changes made, and recommended follow-up actions.
```
**Output:** Formatted summary with incident ID, determination, justification, approvers, exception period, policy changes, and next review date.

---

## Tips for Using These Prompts Effectively

1. **Validate Results**: Always cross-reference Security Copilot's analysis with your own policy knowledge and business context. Copilot is a thinking partner, not the final authority.

2. **Customize for Your Organization**: Replace generic policy names, data types, and domains with your actual DLP configuration.

3. **Provide Complete Context**: The quality of Copilot's response depends on specific details - user names, policy names, time ranges, and data types matter significantly.

4. **Iterate with Follow-ups**: Ask clarifying questions to deepen analysis. For example, "Are there other users sharing data with this same external domain?"

5. **Chain Prompts Strategically**: Use outputs from one prompt as inputs to the next. The investigation chain above shows how prompts build on each other.

6. **Use Output as Starting Point**: Analysis and recommendations should be reviewed, validated, and refined before action.

7. **Test Policy Changes**: Always validate tuning recommendations in audit mode before deploying to enforcement mode.

8. **Maintain Consistency**: Use similar prompt structures across your team to ensure consistent terminology and approach.

---

**Document Version**: 2.0
**Last Updated**: April 2026
**Status**: Validated against Security Copilot with Purview plugin
**Note**: All prompts reflect confirmed Security Copilot capabilities as of April 2026.
