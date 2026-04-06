# DFIR Deep Investigation Prompt Book: DLP-Triggered Full-Spectrum Forensic Analysis

This prompt book contains **40 ready-to-use Security Copilot prompts** organized across **10 investigation phases** that guide a DFIR analyst from an initial DLP policy alert through a complete forensic investigation — correlating insider risk signals, device posture, identity risk, data exfiltration paths, and behavioral anomalies into a unified evidence chain.

**Design Philosophy:** Each phase builds on findings from the previous phase. Prompts are designed to be executed sequentially within a phase but phases can be parallelized when staffing allows. Every prompt includes explicit placeholders for evidence identifiers discovered in prior steps — this is an **investigation that compounds**, not a checklist.

**Estimated Duration:** 2-6 hours (full execution), 45-90 minutes (abbreviated fast-track)

**Prompt Count:** 40 prompts across 10 phases

**Status:** [ASPIRATIONAL] — Structured for SC + Purview plugin capabilities with investigative depth requiring analyst judgment at decision gates.

**Key Capabilities Used:**
- Summarize Purview Alert
- Triage Purview Alerts
- Get Data Risk Summary
- Get User Risk Summary
- Zoom Into Purview Data Risk
- Zoom Into Purview User Risk

**Prerequisite Permissions:**
- Security Copilot access with Purview plugin enabled
- Compliance Administrator or Security Reader role
- Insider Risk Management Analyst role (Phase 4+)
- Microsoft Entra ID sign-in logs access (Phase 6)
- Microsoft Defender for Endpoint access (Phase 5)

---

## Phase 1 — DLP Alert Triage & Initial Scoping

> **Objective:** Determine what fired, why it fired, and whether it warrants a full investigation or can be resolved as a known-good pattern. This phase produces the **Subject User**, **Subject Data**, and **Initial Severity** that feed every subsequent phase.

---

### 1.1 Alert Intake & Severity Classification

**Title:** Ingest DLP Alert and Classify Investigation Priority

**Purpose:** Pull the raw alert, extract core fields, and make the first triage decision — investigate, queue, or dismiss.

**When to Use:** The moment a DLP alert is assigned to you or appears in your queue.

**SC System Capability Used:** Summarize Purview Alert

**Exact Prompt:**
```
Summarize the DLP alert with ID <alertId>. For this alert, provide:
1. The exact DLP policy name and rule that triggered
2. The severity level assigned by the policy
3. The user (UPN) who performed the action
4. The timestamp and timezone of the triggering event
5. The workload where the violation occurred (Exchange, SharePoint, OneDrive, Teams, Endpoint)
6. The sensitive information types (SITs) detected and their confidence levels
7. The specific action that was taken or blocked (send, upload, copy, print, paste)
8. The match count — how many SIT instances were detected in the content
9. Whether this alert was auto-resolved, pending review, or escalated

Based on this information, assess the initial severity as Critical, High, Medium, or Low using these criteria:
- Critical: ≥50 SIT matches OR involves regulated data (PII, PHI, PCI) AND the action was NOT blocked
- High: 10-49 SIT matches OR involves regulated data AND the action was blocked but content was already shared
- Medium: <10 SIT matches with moderate confidence OR policy is in test mode
- Low: Single match with low confidence OR known false-positive pattern

State your confidence level in this severity assessment.
```

**Expected Output:**
- Structured alert summary with all 9 fields populated
- Initial severity classification with reasoning
- Confidence level (High/Medium/Low)
- Identification of the subject user and subject data for downstream investigation

**Chain-Next:** 1.2 (Policy Context) using the policy name extracted here

**Limitations:**
- SC cannot determine user intent from alert metadata alone
- Auto-resolved alerts may have limited detail
- Severity classification is heuristic — analyst must validate against organizational risk appetite

**Tips:**
- Copy the subject UPN and alert timestamp — you will reference these in every subsequent phase
- If multiple alerts fired within a short window for the same user, jump to 1.3 immediately

---

### 1.2 Policy Context & Rule Logic Assessment

**Title:** Evaluate the DLP Policy Design and Determine if the Alert is Structurally Valid

**Purpose:** Before investigating the user, investigate the policy. A misconfigured rule generates noise; a well-tuned rule generates signal. This prompt helps you distinguish between the two.

**When to Use:** Immediately after 1.1, before committing investigation resources.

**SC System Capability Used:** Zoom Into Purview Data Risk

**Exact Prompt:**
```
For the DLP policy "<policyName>" that triggered alert <alertId>:
1. Describe the policy's scope — which workloads, locations, and user groups does it cover?
2. What sensitive information types (SITs) does this policy detect, and at what confidence thresholds?
3. What are the configured actions (block, notify, override with justification, audit-only)?
4. Has this policy been recently modified? If so, when and what changed?
5. How many alerts has this specific policy generated in the last 30 days?
6. What is the false positive rate for this policy based on historical alert dispositions?
7. Are there any policy exceptions or exclusion groups that should have applied to user <userUPN> but did not?

Based on this analysis, assess whether this alert represents:
A) A genuine policy violation requiring investigation
B) A likely false positive due to policy misconfiguration or overly broad rules
C) A true positive in audit-only mode that needs policy enforcement escalation
```

**Expected Output:**
- Full policy configuration summary
- 30-day alert volume and false positive rate
- Assessment of alert validity (A, B, or C)
- Identification of any policy gaps or misconfigurations

**Chain-Next:** If A → proceed to 1.3. If B → document and close. If C → escalate to policy team AND proceed to 1.3.

**Limitations:**
- SC may not surface all historical policy modifications
- False positive rate depends on consistent analyst dispositions in the past

**Tips:**
- If the policy was modified in the last 7 days, treat the alert with higher scrutiny — new rules often over-fire
- Note the policy's action configuration — a "block with override" that was overridden is a very different signal than an "audit-only" match

---

### 1.3 Alert Clustering & Rapid Pattern Detection

**Title:** Identify Related DLP Alerts to Determine if This is an Isolated Event or Part of a Pattern

**Purpose:** A single DLP alert is a data point. Multiple correlated alerts are evidence of a pattern. This prompt scans for clustering before you invest hours in a single-alert investigation.

**When to Use:** After 1.1, especially when severity is Medium or above.

**SC System Capability Used:** Triage Purview Alerts

**Exact Prompt:**
```
Show me all DLP alerts for user <userUPN> in the last 30 days. For each alert provide:
1. Alert ID, timestamp, and severity
2. DLP policy name and rule triggered
3. Workload (Exchange, SharePoint, OneDrive, Teams, Endpoint)
4. Sensitive information types detected
5. Action taken (blocked, overridden, audit-only)
6. Alert disposition status (open, resolved, false positive)

After listing all alerts, analyze the pattern:
- Is the alert frequency increasing, stable, or decreasing over time?
- Are multiple policies firing for the same user, or is it the same policy repeatedly?
- Is the user triggering alerts across multiple workloads (cross-channel pattern)?
- Are the SITs consistent (same data type) or varied (broad data access)?
- Is there a time-of-day pattern (business hours vs. after hours)?

Classify this user's DLP alert pattern as:
A) Isolated incident — single or rare alert, no concerning pattern
B) Recurring negligence — repeated violations of the same type suggesting training gap
C) Escalating behavior — increasing frequency or expanding scope suggesting intentional action
D) Multi-channel exfiltration pattern — alerts across workloads suggesting coordinated data movement

State your confidence level and cite specific evidence for the classification.
```

**Expected Output:**
- Complete 30-day DLP alert history for the subject user
- Pattern analysis with temporal, workload, and SIT dimensions
- Behavioral classification (A/B/C/D) with confidence level
- Evidence citations for the classification

**Chain-Next:** If A → proceed to Phase 2 for single-alert deep dive. If B/C/D → proceed to Phase 2 AND immediately initiate Phase 4 (IRM correlation) in parallel.

**Limitations:**
- 30-day window may miss slow-burn patterns — extend to 90 days if organizational policy allows
- Alert disposition quality depends on prior analyst rigor

**Tips:**
- Classification C or D should trigger immediate escalation to your team lead before proceeding
- If you see cross-channel patterns (email + endpoint + cloud), this is a strong indicator of intentional data staging

---

### 1.4 Immediate Risk Signal Check

**Title:** Pull the User's Current Risk Score and Active Risk Indicators Before Deep Investigation

**Purpose:** Before spending hours investigating, check if the user is already flagged by other Purview signals. This 60-second check can instantly escalate or de-escalate your investigation.

**When to Use:** Immediately after 1.1, can be run in parallel with 1.2 and 1.3.

**SC System Capability Used:** Get User Risk Summary

**Exact Prompt:**
```
Get the user risk summary for <userUPN>. Specifically:
1. What is their current insider risk severity level?
2. Are there any active insider risk management cases or alerts for this user?
3. What risk indicators are currently active (data theft, data leak, security policy violation, patient data misuse, risky browsing)?
4. What is their cumulative risk score trend over the last 90 days — increasing, stable, or decreasing?
5. Are they flagged by any triggering events (resignation, termination, performance improvement plan, role change)?
6. What is their data access pattern — are they accessing more sensitive data than their peer group?

Highlight any risk indicators that correlate with the DLP alert from <alertId>.
```

**Expected Output:**
- Current IRM risk profile with severity level
- Active cases, alerts, and risk indicators
- 90-day risk trend
- HR triggering events if any
- Correlation between IRM signals and the DLP alert

**Chain-Next:** Phase 2 (always) + Phase 4 (if IRM risk is Medium or above)

**Limitations:**
- IRM data availability depends on licensing and policy configuration
- HR triggering events require HR connector integration
- Risk scores may lag real-time behavior by 24-48 hours

**Tips:**
- If a HR triggering event (resignation, PIP) is active, immediately classify this investigation as High priority regardless of DLP alert severity
- The absence of IRM signals does not mean the user is safe — it may mean IRM policies don't cover this scenario

---

## Phase 2 — Data & Content Deep Dive

> **Objective:** Understand exactly what data was involved — its sensitivity classification, business context, regulatory implications, and whether the content exposure represents an actual risk or a benign business process. This phase produces the **Data Impact Assessment** that determines investigation urgency.

---

### 2.1 Content Sensitivity & Classification Analysis

**Title:** Deep Dive into the Specific Content that Triggered the DLP Alert

**Purpose:** Move beyond the SIT match to understand the actual content — what was it, how sensitive is it really, and what is the business context.

**When to Use:** After Phase 1 confirms the alert warrants investigation.

**SC System Capability Used:** Zoom Into Purview Data Risk

**Exact Prompt:**
```
For DLP alert <alertId>, zoom into the data risk details:
1. What is the exact file name or email subject that triggered the alert?
2. What sensitivity label (if any) was applied to this content?
3. What sensitive information types were detected, with exact match counts per SIT?
4. What is the confidence level for each SIT detection (high, medium, low)?
5. Was the content classified correctly by the SIT engine, or are there indicators of false positive matches?
6. What is the source location of the content (mailbox, site, library, local drive, cloud app)?
7. Who is the content owner, and does it differ from the user who triggered the alert?
8. Has this specific file or content been involved in previous DLP alerts?

Based on this analysis, classify the data sensitivity impact as:
- Critical: Regulated data (PCI, PHI, PII under GDPR/CCPA) with high-confidence matches
- High: Confidential business data (trade secrets, M&A, financial projections) or moderate-confidence regulated data
- Medium: Internal-only data with sensitivity labels or low-confidence regulated data matches
- Low: Public or low-sensitivity data, likely false positive SIT matches

Provide your confidence level and explain any discrepancies between the sensitivity label and the SIT detections.
```

**Expected Output:**
- Detailed content identification (file, email, message)
- SIT match breakdown with confidence levels
- Sensitivity label vs. SIT detection comparison
- Content ownership and history
- Data sensitivity impact classification

**Chain-Next:** 2.2 for recipient/destination analysis

**Limitations:**
- SC cannot render or display the actual content for analyst review
- Content in encrypted or RMS-protected files may have limited SIT visibility
- Historical content alerts require sufficient retention policy

**Tips:**
- A mismatch between applied sensitivity label (e.g., "General") and SIT detection (e.g., "SSN - High Confidence x 200") is a strong indicator of either mislabeling or legitimate data aggregation
- If the content owner differs from the triggering user, this may indicate lateral data movement

---

### 2.2 Destination & Recipient Risk Analysis

**Title:** Analyze Where the Data Was Going and Who Would Have Received It

**Purpose:** The severity of a data leak is defined not just by what was shared, but by where it was going. An internal misroute is different from external exfiltration.

**When to Use:** After 2.1 confirms sensitive content was involved.

**SC System Capability Used:** Zoom Into Purview Data Risk

**Exact Prompt:**
```
For DLP alert <alertId>, analyze the data destination:
1. Was the content being sent externally or shared internally?
2. If external: What is the recipient domain? Is it a known partner, personal email (gmail, outlook.com, yahoo), competitor, or unknown entity?
3. If internal: Was the recipient authorized to access this sensitivity level? What is their role and clearance?
4. For file sharing: Was the sharing link scoped (specific people) or broad (anyone with the link, organization-wide)?
5. For endpoint actions: Was the content being copied to USB, printed, uploaded to a cloud app, or pasted into a browser?
6. Was the data transfer blocked, allowed with override, or allowed without restriction?
7. If override was used: What justification did the user provide?
8. Has this user sent data to this same destination/recipient before?

Assess the destination risk:
- Critical: Personal email domain, unknown external entity, competitor domain, or public sharing link
- High: USB copy, unauthorized cloud app upload, or broad internal sharing of regulated data
- Medium: Known partner without DLP-exempt status, or authorized internal recipient with oversharing scope
- Low: Authorized internal recipient with appropriate access, or blocked action with no data exposure

Cross-reference: Does the destination pattern match any known exfiltration techniques (slow drip to personal email, cloud staging, print-and-photograph)?
```

**Expected Output:**
- Complete destination analysis (external/internal, domain, recipient identity)
- Sharing scope and permission analysis
- Override justification review (if applicable)
- Destination risk classification
- Exfiltration technique pattern matching

**Chain-Next:** 2.3 for data lineage tracking

**Limitations:**
- SC cannot resolve recipient identity beyond what is in Purview/Exchange logs
- Personal device or offline exfiltration (photos of screen) are not detectable
- Partner domain classification depends on organizational allow-list configuration

**Tips:**
- Personal email domains (gmail, yahoo, hotmail, protonmail) as recipients for business data should ALWAYS be investigated further regardless of content sensitivity
- An override justification of "business need" with no specifics is a weak signal — dig deeper
- If the sharing link scope is "anyone with the link," treat as external exposure regardless of initial recipient

---

### 2.3 Data Lineage & Provenance Tracking

**Title:** Trace the Data's Origin and Movement Path to Understand the Full Exposure Chain

**Purpose:** Data rarely moves once. Trace the content backward to its origin and forward to all destinations to understand the complete exposure surface.

**When to Use:** When 2.1 or 2.2 indicate high-sensitivity data or suspicious destination patterns.

**SC System Capability Used:** Zoom Into Purview Data Risk

**Exact Prompt:**
```
For the content involved in DLP alert <alertId>, trace the complete data lineage:

BACKWARD (Origin):
1. Where did this file or content originate? (Which site, mailbox, database, or application?)
2. Who created it and when?
3. What sensitivity label was applied at creation?
4. Has the content been modified, copied, or downloaded since creation?
5. How many users have accessed this content in the last 90 days?

FORWARD (Exposure):
6. Besides the action in this alert, has this same content been shared, copied, or moved elsewhere?
7. Are there other DLP alerts for this same file or content hash?
8. Has this content been accessed from any unmanaged devices?
9. Has this content been synced to any personal OneDrive or downloaded locally?

MAP THE CHAIN:
10. Reconstruct the complete data movement timeline: creation → access → modification → sharing/exfiltration attempt

Assess the total exposure surface — how many copies of this content exist, in how many locations, accessible by how many users? Is the data still contained or has containment been lost?
```

**Expected Output:**
- Origin identification (creator, location, timestamp)
- Access history (who, when, from where)
- Forward movement map (all copies, shares, downloads)
- Complete data movement timeline
- Total exposure surface assessment
- Containment status (contained / partially exposed / fully exposed)

**Chain-Next:** Phase 3 (User Behavioral Deep Dive) with full data context

**Limitations:**
- Data lineage tracking depends on audit log retention (default 90 days, extended with E5)
- Cross-tenant data movement may not be visible
- Content hash tracking requires advanced DLP endpoint features

**Tips:**
- If you discover the content has been synced to a personal device, escalate immediately — containment may require device wipe authorization
- Multiple copies in multiple locations means containment is exponentially harder — quantify this for your incident commander

---

### 2.4 Regulatory Impact Quick Assessment

**Title:** Determine Regulatory and Compliance Exposure from the Data Involved

**Purpose:** Map the SIT detections to regulatory frameworks to determine if this incident triggers mandatory notification, reporting, or legal obligations.

**When to Use:** When Phase 2.1 identifies regulated data types (PII, PHI, PCI, financial data).

**SC System Capability Used:** Get Data Risk Summary

**Exact Prompt:**
```
Based on the sensitive information types detected in DLP alert <alertId>, assess the regulatory exposure:

1. Map each detected SIT to applicable regulations:
   - Social Security Numbers, Driver's License → CCPA, state breach notification laws
   - Credit Card Numbers, Bank Account Numbers → PCI DSS
   - Protected Health Information → HIPAA
   - EU Personal Data → GDPR
   - Financial Records → SOX, GLBA

2. For each applicable regulation:
   - What is the notification timeline requirement? (e.g., GDPR: 72 hours, HIPAA: 60 days)
   - What is the threshold for mandatory reporting? (e.g., number of records, type of data)
   - Does the data exposure in this alert meet or exceed that threshold?

3. Based on the match count from the alert (<matchCount> instances of <sitType>):
   - Is this a single-individual exposure or a bulk data event?
   - Does the volume suggest a database export, report, or individual record?

4. What is the regulatory risk level?
   - Critical: Mandatory breach notification likely triggered, legal team must be engaged immediately
   - High: Notification thresholds may be met depending on actual exposure confirmation
   - Medium: Regulatory data involved but exposure is contained and below notification thresholds
   - Low: Low-confidence matches or data types not covered by applicable regulations

Provide a recommendation on whether Legal and Privacy teams should be notified at this stage.
```

**Expected Output:**
- SIT-to-regulation mapping
- Notification timeline requirements
- Threshold analysis
- Regulatory risk classification
- Legal/Privacy notification recommendation

**Chain-Next:** Phase 3 (continue investigation) — flag regulatory findings for Phase 9 (Impact Assessment)

**Limitations:**
- SC cannot provide legal advice — regulatory assessment is indicative, not authoritative
- Jurisdiction-specific requirements (state-by-state breach laws) may need legal review
- Actual breach determination requires confirmation of unauthorized access, not just policy match

**Tips:**
- When in doubt about regulatory thresholds, err on the side of notifying Legal early — late notification is always worse than early notification
- Document this assessment as Exhibit A in your investigation file — it establishes the regulatory clock

---

## Phase 3 — User Behavioral Deep Dive

> **Objective:** Build a comprehensive behavioral profile of the subject user. Move beyond "what they did" to understand "who they are, what they normally do, and whether this event is anomalous." This phase produces the **User Risk Profile** that contextualizes all subsequent findings.

---

### 3.1 User Identity & Organizational Context

**Title:** Build the Subject User's Identity Profile and Organizational Position

**Purpose:** The same action by a departing executive and a new intern have vastly different risk implications. Establish who this user is before analyzing what they did.

**When to Use:** Beginning of Phase 3, after data context is established.

**SC System Capability Used:** Get User Risk Summary

**Exact Prompt:**
```
Build a comprehensive identity profile for <userUPN>:

ORGANIZATIONAL CONTEXT:
1. What is their job title, department, and reporting manager?
2. When did they join the organization (hire date)?
3. What is their current employment status — active, on leave, on notice period, terminated?
4. Have they had any recent role changes, transfers, or organizational moves in the last 90 days?
5. Are they in any privileged groups (executive leadership, IT admin, finance, legal, HR)?
6. What is their geographic location and primary office?

ACCESS CONTEXT:
7. What sensitivity levels of data are they authorized to access based on their role?
8. Do they have any elevated permissions (Global Admin, SharePoint Admin, Compliance Admin)?
9. Are they in any DLP policy exception or exclusion groups?
10. What applications and data sources are they licensed to use?

RISK CONTEXT:
11. What is their current insider risk severity level?
12. Are there any active HR signals (resignation, termination, PIP, performance review)?
13. Have they been the subject of any prior investigations?
14. What is their peer group for behavioral baselining?

Flag any risk amplifiers: notice period + sensitive data access is a different risk equation than a tenured employee in a routine role.
```

**Expected Output:**
- Complete identity profile with organizational position
- Access and permission inventory
- Risk context with HR signals
- Risk amplifier identification
- Peer group identification for behavioral comparison

**Chain-Next:** 3.2 for behavioral baseline comparison

**Limitations:**
- HR signals require HR connector; absence does not mean absence of risk
- Role-based access assessment may not capture informal data access (shared links, forwarded emails)
- Terminated users may have limited profile data

**Tips:**
- If the user is in their notice period and handling sensitive data, escalate to critical immediately
- Cross-reference their department with the data type — a finance user accessing financial data is expected; accessing HR data is not
- Document the peer group — you will need it for behavioral anomaly detection in 3.2

---

### 3.2 Behavioral Baseline & Anomaly Detection

**Title:** Compare the User's Recent Activity Against Their Historical Baseline and Peer Group

**Purpose:** Establish whether the DLP-triggering action was normal behavior for this user or a significant deviation.

**When to Use:** After 3.1 provides the user identity and peer group context.

**SC System Capability Used:** Zoom Into Purview User Risk

**Exact Prompt:**
```
Analyze the behavioral patterns for <userUPN> over the last 90 days and compare against their baseline:

ACTIVITY VOLUME:
1. What is their average daily/weekly file access, download, and sharing volume?
2. Has this volume changed significantly in the last 7, 14, or 30 days compared to the prior 60 days?
3. How does their activity volume compare to their peer group (<peerGroup>)?

DATA ACCESS PATTERNS:
4. What types of sensitive data do they typically access? (SIT categories, sensitivity label levels)
5. Have they accessed any new categories of sensitive data in the last 30 days that they hadn't accessed before?
6. Are they accessing data outside their normal scope (other departments' sites, other geographies' data)?

TEMPORAL PATTERNS:
7. What are their normal working hours based on historical activity?
8. Has there been after-hours or weekend activity that deviates from their pattern?
9. Is there a spike in activity just before or after the DLP alert timestamp?

SHARING PATTERNS:
10. What is their normal external sharing frequency and recipient list?
11. Have they shared with any new external recipients in the last 30 days?
12. Have they increased the volume or sensitivity level of externally shared content?

Identify the top 3 most anomalous behaviors relative to their baseline and peer group. For each anomaly, provide:
- The specific behavior
- How far it deviates from baseline (percentage or standard deviations)
- When it started
- Whether it correlates with the DLP alert timeline
```

**Expected Output:**
- 90-day behavioral profile with volume, access, temporal, and sharing dimensions
- Baseline deviation analysis with quantified anomalies
- Peer group comparison
- Top 3 ranked anomalies with correlation to DLP alert
- Behavioral change inflection point (if any)

**Chain-Next:** 3.3 for deep activity timeline reconstruction

**Limitations:**
- Behavioral baselines require 30+ days of historical data to be meaningful
- New hires (<30 days) will have insufficient baseline data
- Peer group comparison depends on how groups are defined in IRM policies

**Tips:**
- A user who suddenly starts downloading 10x their normal volume 2 weeks before their resignation date is a textbook exfiltration pattern
- Look for the inflection point — when did behavior change? This often correlates with a triggering event (resignation, bad review, job interview)
- After-hours + high-volume + new recipients = the classic trifecta

---

### 3.3 Full Activity Timeline Reconstruction

**Title:** Build a Minute-by-Minute Timeline of the User's Activities Surrounding the DLP Alert

**Purpose:** Reconstruct what the user was doing before, during, and after the DLP alert to understand the complete operational sequence.

**When to Use:** After 3.2 identifies behavioral anomalies or when alert severity is High/Critical.

**SC System Capability Used:** Zoom Into Purview User Risk

**Exact Prompt:**
```
Reconstruct a detailed activity timeline for <userUPN> for the 72-hour window centered on the DLP alert at <alertTimestamp> (36 hours before and 36 hours after):

For each activity, capture:
- Exact timestamp
- Activity type (file access, download, upload, share, email send, print, copy to USB, browse, sign-in)
- Target resource (file name, site, mailbox, application)
- Source context (device, IP address, location, application)

Organize the timeline and identify:

PRE-ALERT ACTIVITIES (36 hours before):
1. What was the user doing in the hours leading up to the alert?
2. Were there preparatory actions (bulk downloads, searches for specific content, new folder creation)?
3. Did they access the content multiple times before the triggering action?
4. Were there any failed access attempts or permission changes?

ALERT EVENT:
5. Exactly what happened at the alert timestamp — step by step?
6. Was this a single action or part of a rapid sequence?

POST-ALERT ACTIVITIES (36 hours after):
7. Did the user's behavior change after the alert (stop sharing, delete files, modify content)?
8. Were there any evidence of awareness of detection (clearing browser history, deleting emails, modifying files)?
9. Did they continue similar activities after the alert, suggesting they are unaware of being monitored?
10. Were there any communication activities (emails to personal accounts, Teams messages) that might indicate coordination?

Flag any sequences that match known exfiltration patterns:
- Stage → Compress → Exfiltrate
- Search → Download → Send
- Access → Screenshot/Print → Remove traces
- Reconnaissance → Collection → Exfiltration → Cover tracks
```

**Expected Output:**
- Complete 72-hour activity timeline with timestamps
- Pre-alert preparatory action identification
- Alert event sequence detail
- Post-alert behavioral changes
- Exfiltration pattern matching results
- Evidence of detection awareness or anti-forensic behavior

**Chain-Next:** Phase 4 (IRM Deep Correlation) with timeline context

**Limitations:**
- 72-hour window is a starting point — extend if patterns suggest longer preparation
- Endpoint activities require MDE integration
- Encrypted or offline activities will have gaps
- Some activities (viewing a screen, taking a photo) are undetectable

**Tips:**
- Post-alert behavioral changes are extremely telling — a user who immediately stops all sharing activity may be aware of monitoring
- The stage-compress-exfiltrate pattern often spans days — if you see file renaming or compression (zip, 7z) in the pre-alert window, expand your timeline
- Document this timeline as the core evidence artifact — it will be referenced by legal, HR, and management

---

### 3.4 Communication Pattern Analysis

**Title:** Analyze the User's Communication Patterns for Coordination or Data Exfiltration Indicators

**Purpose:** Exfiltration via email is obvious. Coordination via side channels, unusual recipient patterns, or encrypted messaging indicators is subtler but equally important.

**When to Use:** When Phase 3.3 reveals concerning patterns or when the DLP alert involved email/Teams.

**SC System Capability Used:** Zoom Into Purview User Risk

**Exact Prompt:**
```
Analyze the communication patterns for <userUPN> over the last 30 days:

EMAIL PATTERNS:
1. What is their normal daily email volume (sent/received)?
2. Have they sent emails to personal email addresses (gmail, yahoo, hotmail, protonmail, tutanota)?
3. Have they sent emails with attachments to external recipients outside their normal contact list?
4. Are there emails with unusually large attachments or many attachments?
5. Have they set up any new mail forwarding rules or inbox rules recently?
6. Have they used any mail delegation or send-as permissions?

TEAMS/COLLABORATION:
7. Have they shared files or links via Teams to unusual recipients or channels?
8. Have they created any new Teams, channels, or SharePoint sites recently?
9. Have they used Teams chat to share content that would normally go through email (bypassing email DLP)?

SEARCH BEHAVIOR:
10. What have they searched for in SharePoint, OneDrive, or Exchange in the last 30 days?
11. Are search patterns consistent with their role, or are they searching for data outside their scope?
12. Is there a pattern of searching → accessing → downloading that suggests targeted data collection?

Flag any indicators of:
- Data staging via personal email forwarding rules
- DLP evasion (using Teams when email is blocked, renaming file extensions)
- Communication with known-risk external entities
- Unusual encryption or password-protected file sharing
```

**Expected Output:**
- Email volume and pattern analysis
- External communication mapping
- Mail forwarding rule inventory
- Teams/collaboration anomaly detection
- Search behavior analysis
- DLP evasion indicator assessment

**Chain-Next:** Phase 4 (IRM correlation with communication evidence)

**Limitations:**
- Teams DLP coverage may differ from Exchange DLP coverage
- Personal device communications are not visible
- Encrypted email content may not be inspectable
- Search history retention depends on audit log configuration

**Tips:**
- Auto-forwarding rules to personal email are one of the most common and effective exfiltration techniques — always check
- If a user is searching for terms like "confidential," "merger," "salary," or specific project names outside their scope, this is targeted collection
- Password-protected zip files sent to personal email bypass most SIT detection — look for this pattern

---

## Phase 4 — Insider Risk Management Correlation

> **Objective:** Cross-reference DLP findings with IRM signals to build a composite risk picture. IRM captures behavioral sequences, risk indicators, and cumulative patterns that DLP alone cannot see. This phase produces the **Insider Threat Assessment**.

---

### 4.1 IRM Alert & Case Correlation

**Title:** Cross-Reference DLP Alert with Insider Risk Management Signals and Cases

**Purpose:** Determine if IRM has independently flagged this user and whether IRM signals corroborate or escalate the DLP findings.

**When to Use:** After Phase 1-3 establish the DLP alert context and user profile.

**SC System Capability Used:** Get User Risk Summary + Zoom Into Purview User Risk

**Exact Prompt:**
```
Perform a comprehensive IRM correlation for <userUPN>:

ACTIVE IRM STATUS:
1. Is this user currently in an active IRM case? If so, what is the case status, severity, and assigned analyst?
2. Are there any IRM alerts (not DLP alerts) currently open for this user?
3. What IRM policy templates have flagged this user (Data Theft, Data Leak, Security Policy Violation, Patient Data Misuse)?

RISK INDICATORS (ACTIVE):
4. List all currently active risk indicators for this user, including:
   - Data exfiltration indicators (downloads, USB copies, cloud uploads, email attachments)
   - Data obfuscation indicators (file renaming, compression, encryption before transfer)
   - Access anomaly indicators (first-time access, excessive access, off-hours access)
   - Security policy violation indicators (DLP override, sensitivity label downgrade, browser upload to unsanctioned sites)
   - Departing employee indicators (resignation, HR termination, access to sensitive data during notice period)

5. For each active indicator, provide:
   - When it was first triggered
   - How many times it has been triggered
   - Whether it is correlated with the DLP alert under investigation

SEQUENCE DETECTION:
6. Has IRM detected any risk activity sequences for this user? Sequences are multi-step patterns such as:
   - Download sensitive file → Rename file → Upload to personal cloud storage
   - Access confidential site → Download bulk files → Send email with attachment to personal address
   - Search for sensitive terms → Access multiple sensitive files → Print or copy to USB

7. How does the DLP alert fit within the IRM sequence timeline?

CUMULATIVE RISK:
8. What is the user's cumulative risk score, and how has it changed over the last 90 days?
9. What percentile does this user fall in relative to the organization?
10. What are the top contributing factors to their risk score?

Synthesize: Does the IRM data corroborate the DLP alert as part of a larger insider risk pattern, or does the DLP alert appear isolated from IRM signals?
```

**Expected Output:**
- Active IRM case and alert status
- Complete risk indicator inventory with timestamps
- Sequence detection results with DLP alert correlation
- Cumulative risk score with trend and percentile
- Synthesis: corroboration assessment

**Chain-Next:** 4.2 for exfiltration-specific deep dive

**Limitations:**
- IRM sequence detection depends on policy configuration and signal coverage
- Risk score may not reflect real-time behavior (24-48 hour lag)
- Not all DLP alerts will have IRM correlation — absence is not exculpatory

**Tips:**
- If IRM has independently detected sequences that include the DLP alert event, this is strong corroborating evidence
- A user in the 95th+ percentile for risk score warrants full investigation regardless of DLP alert severity
- Multiple active risk indicators from different categories (exfiltration + obfuscation + departing) is the strongest insider threat signal

---

### 4.2 Exfiltration Pathway Analysis

**Title:** Map All Potential Exfiltration Pathways This User Has Used or Attempted

**Purpose:** DLP caught one path. IRM may reveal others. Map the complete exfiltration surface.

**When to Use:** When 4.1 reveals active risk indicators or when DLP alert involves data leaving the organization.

**SC System Capability Used:** Zoom Into Purview User Risk

**Exact Prompt:**
```
For <userUPN>, analyze all potential data exfiltration pathways over the last 30 days:

EMAIL EXFILTRATION:
1. Emails with attachments sent to personal domains
2. Emails with attachments sent to external recipients not in their normal pattern
3. Emails forwarded to personal accounts (manual or via rules)
4. Volume and sensitivity of attached content

CLOUD EXFILTRATION:
5. File uploads to non-sanctioned cloud services (personal Dropbox, Google Drive, WeTransfer, etc.)
6. SharePoint/OneDrive sharing links created with "Anyone" permissions
7. OneDrive sync to unmanaged personal devices
8. Third-party app OAuth consents that grant data access

ENDPOINT EXFILTRATION:
9. Files copied to USB/removable media
10. Files copied to network shares outside their normal scope
11. Print jobs for sensitive content
12. Screen capture or clipboard activity on sensitive documents
13. File uploads via browser to non-corporate sites

COMMUNICATION CHANNEL EXFILTRATION:
14. Large file transfers via Teams DMs to unusual recipients
15. Content pasted into non-corporate messaging apps
16. Sensitive content in meeting chats or recordings

For each pathway used:
- Quantify the volume (number of events, total data size)
- Identify the data types involved
- Correlate timestamps with the DLP alert timeline
- Assess whether the pathway was blocked, detected-only, or undetected

Rank the exfiltration pathways by risk severity and data volume. Is there evidence of channel switching (user moves to a different pathway after being blocked on one)?
```

**Expected Output:**
- Multi-channel exfiltration pathway map
- Volume and data type quantification per pathway
- Timeline correlation with DLP alert
- Detection status per pathway (blocked/detected/undetected)
- Channel switching analysis
- Risk-ranked pathway summary

**Chain-Next:** 4.3 for departing employee risk assessment (if applicable) OR Phase 5 (Device Analysis)

**Limitations:**
- Endpoint exfiltration requires MDE/Endpoint DLP
- Browser-based uploads to unsanctioned sites require Cloud App Security / Defender for Cloud Apps
- Clipboard and screenshot monitoring requires advanced endpoint DLP policies

**Tips:**
- Channel switching is the single strongest indicator of intentional exfiltration — if a user gets blocked on email and immediately starts using USB, this is not accidental
- Browser uploads to personal cloud storage (drive.google.com, dropbox.com) often bypass email-focused DLP — check web activity logs
- Always check for OAuth app consents — a user can grant a personal app read access to their entire mailbox or OneDrive

---

### 4.3 Departing Employee & HR Trigger Analysis

**Title:** Assess Departing Employee Risk Indicators and HR-Correlated Behavioral Changes

**Purpose:** Departing employees represent the highest-risk insider threat vector. If a DLP alert coincides with a departure signal, the investigation priority and scope change fundamentally.

**When to Use:** When 3.1 or 4.1 revealed HR signals (resignation, termination, PIP), or when behavioral anomalies align with departure patterns.

**SC System Capability Used:** Get User Risk Summary

**Exact Prompt:**
```
Assess the departing employee risk for <userUPN>:

HR SIGNALS:
1. Has this user submitted a resignation? If so, when, and what is their last day?
2. Are they on a performance improvement plan (PIP)?
3. Have they been flagged for termination?
4. Have they had a recent negative performance review?
5. Has their manager changed, or have they been reorganized recently?

DEPARTURE BEHAVIORAL INDICATORS:
6. In the 30 days surrounding any HR trigger event:
   - Did file download volume spike compared to their 90-day baseline?
   - Did they access files or sites they hadn't accessed before?
   - Did they access HR-related content (benefits, severance, employment policies)?
   - Did they begin sharing content with personal accounts?
   - Did they access customer lists, pricing data, strategic plans, or IP documentation?

7. Are they exhibiting the classic "departing employee data collection" sequence:
   - Week 1-2 post-resignation: Reconnaissance (browsing, searching)
   - Week 2-3: Collection (downloading, bulk access)
   - Week 3-4+: Exfiltration (sending, copying, syncing)
   - Final days: Cleanup (deleting downloads, clearing history)

8. Have they accessed or downloaded any of the following high-value targets:
   - Customer/client lists
   - Source code repositories
   - Financial projections or M&A documents
   - Product roadmaps or strategic plans
   - Employee salary or benefits data
   - Proprietary methodologies or training materials

Map the DLP alert to the departure timeline. Does the alert occur during the collection or exfiltration phase?
```

**Expected Output:**
- HR signal verification with dates
- Departure behavioral indicator analysis
- Classic departure sequence phase mapping
- High-value target access assessment
- DLP alert positioning within departure timeline

**Chain-Next:** Phase 5 (Device Analysis) + escalate to Legal/HR if departure pattern confirmed

**Limitations:**
- HR signals require HR connector or manual input from HR team
- Not all departing employees are risks — analysis must be proportional
- PIP and performance data are highly sensitive — handle with confidentiality

**Tips:**
- The highest risk window is 14-30 days after resignation submission — this is when most data theft occurs
- If you see customer list access + personal email sharing in a departing employee, escalate to Legal immediately — this may involve non-compete or trade secret issues
- Document the timeline meticulously — this evidence may be used in legal proceedings

---

### 4.4 Peer & Accomplice Analysis

**Title:** Determine if the Subject User is Acting Alone or in Coordination with Others

**Purpose:** Insider threats sometimes involve collusion. Check whether related users exhibit similar patterns.

**When to Use:** When exfiltration evidence is strong and the question shifts from "if" to "who else."

**SC System Capability Used:** Get User Risk Summary + Triage Purview Alerts

**Exact Prompt:**
```
Investigate potential coordination for <userUPN>'s DLP activity:

DIRECT COLLABORATORS:
1. Who are the top 10 users that <userUPN> has shared files with or emailed in the last 30 days?
2. For each collaborator, do any have their own DLP alerts or IRM risk indicators?
3. Has any collaborator accessed the same sensitive content involved in alert <alertId>?

RECIPIENT ANALYSIS:
4. If data was shared externally, who received it?
5. Do any external recipients have connections to competitors or known-risk entities?
6. Is the same external recipient receiving data from multiple internal users?

PARALLEL PATTERNS:
7. Are any other users in the same department or team exhibiting similar behavioral anomalies (volume spikes, after-hours access, new external sharing)?
8. Have there been DLP alerts for the same policy from multiple users in a short timeframe?
9. Are any other users on the same team also departing or under HR action?

SHARED INFRASTRUCTURE:
10. Have any other users accessed the same file or content that triggered this alert?
11. Are there shared mailboxes, distribution lists, or Teams channels that could be used as coordination mechanisms?

Assess: Is there evidence of coordinated data exfiltration by multiple users, or does this appear to be an individual action? If coordination is suspected, identify the additional subjects.
```

**Expected Output:**
- Collaborator analysis with risk status
- External recipient risk assessment
- Parallel pattern detection across team/department
- Shared access analysis
- Coordination assessment with identified additional subjects (if any)

**Chain-Next:** Phase 5 (Device Analysis) — if new subjects identified, spawn parallel Phase 3 investigations for each

**Limitations:**
- Coordination via external channels (personal phone, in-person) is not detectable
- Peer analysis across large organizations may produce false correlations
- Privacy considerations — analyst must have justification for expanding scope to other users

**Tips:**
- Multiple departing employees from the same team all showing data access anomalies is a strong indicator of coordinated IP theft — this happens in competitive industries
- Check if external recipients are the same person receiving from multiple internal users — this is a collector pattern
- If you identify new subjects, document your justification for expanding the investigation scope before proceeding

---

## Phase 5 — Device & Endpoint Analysis

> **Objective:** Understand the device context of the DLP-triggering event and identify endpoint-level indicators that network and cloud logs cannot capture. This phase produces the **Device Risk Assessment**.

---

### 5.1 Device Compliance & Posture Assessment

**Title:** Assess the Device Used During the DLP Alert for Compliance and Risk Posture

**Purpose:** A DLP violation from a compliant, managed corporate device has different implications than one from a personal, unmanaged device.

**When to Use:** After Phase 3-4 establish user context, or immediately for Critical alerts.

**SC System Capability Used:** Zoom Into Purview User Risk

**Exact Prompt:**
```
Analyze the device context for the DLP alert <alertId> triggered by <userUPN>:

DEVICE IDENTIFICATION:
1. What device was used during the triggering event? (Device name, ID, type — laptop, desktop, mobile, tablet)
2. Is this a corporate-managed device or a personal/BYOD device?
3. What operating system and version is the device running?
4. Is the device enrolled in Microsoft Intune / Endpoint Manager?

COMPLIANCE STATUS:
5. Is the device currently compliant with organizational policies?
6. If non-compliant, what policies are violated? (OS patch level, encryption, antivirus, firewall)
7. Is BitLocker (Windows) or FileVault (Mac) encryption enabled?
8. Is Microsoft Defender for Endpoint installed and active?
9. When was the last compliance check?

DEVICE RISK INDICATORS:
10. Has this device been flagged by MDE for any security alerts (malware, suspicious process, vulnerability)?
11. Has this device been used to access sensitive data from an unusual network or location?
12. Are there any indication that this device has been compromised (lateral movement, C2 communication, credential theft)?
13. Have any other users signed into this device recently?

MULTI-DEVICE ANALYSIS:
14. How many devices does <userUPN> use to access corporate resources?
15. Have they recently started using a new device?
16. Is there evidence of the same data being accessed from multiple devices in sequence (device-hopping for exfiltration)?

Assess: Does the device posture increase or decrease the risk level of this DLP alert? Flag any device-level risks that compound the data risk.
```

**Expected Output:**
- Device identification and management status
- Compliance assessment with policy violations
- MDE security alert correlation
- Multi-device analysis
- Device risk impact on overall alert severity

**Chain-Next:** 5.2 for endpoint activity analysis

**Limitations:**
- Unmanaged/BYOD devices will have limited visibility
- MDE alerts require Defender for Endpoint P2 licensing
- Mobile device details may be limited to MDM-enrolled devices

**Tips:**
- A DLP alert from an unmanaged device is inherently higher risk — you cannot verify the data disposition
- If the device is non-compliant (e.g., no encryption), data at rest on that device is at risk even if the exfiltration attempt was blocked
- Device-hopping (access on corporate laptop → sync to personal laptop → exfiltrate from personal) is a sophisticated evasion technique

---

### 5.2 Endpoint Activity & Local Staging Analysis

**Title:** Analyze Endpoint-Level Activities for Local Data Staging and Exfiltration Indicators

**Purpose:** Before data leaves the organization, it is often staged locally. Endpoint signals reveal what cloud logs cannot — local file manipulation, USB activity, and application-level behavior.

**When to Use:** When device is corporate-managed and MDE is available, or when endpoint DLP is deployed.

**SC System Capability Used:** Zoom Into Purview User Risk

**Exact Prompt:**
```
Analyze endpoint-level activities for <userUPN> on device <deviceName> over the last 30 days:

LOCAL FILE OPERATIONS:
1. Have files been downloaded from SharePoint/OneDrive/Exchange to the local drive in bulk?
2. Have files been renamed, compressed (zip, 7z, rar), or encrypted locally?
3. Have files been moved to unusual local directories (temp folders, desktop, USB staging paths)?
4. Have new folders been created that might serve as collection staging areas?

REMOVABLE MEDIA:
5. Have any USB drives or external storage devices been connected?
6. If so, what was the device type, capacity, and when was it connected?
7. Were files copied to the removable media? If so, what files and what volume?
8. Is USB usage consistent with this user's normal pattern?

PRINT ACTIVITY:
9. Have sensitive documents been printed?
10. If so, which documents, how many pages, and to which printer?
11. Is the print volume or content type unusual for this user?

APPLICATION BEHAVIOR:
12. Have any non-standard applications been installed or used (compression tools, FTP clients, cloud sync apps, remote desktop, virtual machines)?
13. Have any browser uploads occurred to non-corporate sites?
14. Have screen capture tools or clipboard managers been used while sensitive content was open?

NETWORK INDICATORS:
15. Have there been connections to unusual IP addresses or domains from this endpoint?
16. Are there any DNS query anomalies suggesting C2 or data exfiltration via DNS tunneling?
17. Have any VPN or proxy connections been established outside corporate infrastructure?

Flag any indicators of the "local staging" exfiltration pattern: download → rename/compress → copy to USB or upload to personal cloud.
```

**Expected Output:**
- Local file operation analysis (downloads, renames, compression)
- Removable media inventory and copy activity
- Print activity assessment
- Non-standard application usage
- Network anomaly indicators
- Local staging pattern detection

**Chain-Next:** Phase 6 (Identity & Access Risk) with device context

**Limitations:**
- Endpoint visibility requires MDE + Endpoint DLP policies
- Encrypted container analysis (VeraCrypt, BitLocker-to-Go) is limited
- Personal cloud sync app detection depends on Cloud App Security policies
- Screen capture detection is limited to known capture tools

**Tips:**
- File compression + renaming just before USB copy or cloud upload is a strong exfiltration indicator
- Look for split archives (file.zip.001, .002, etc.) — this is used to bypass file size limits
- If you see virtual machine usage or remote desktop connections, the user may be creating an air-gapped exfiltration environment — escalate immediately

---

## Phase 6 — Identity & Access Risk Analysis

> **Objective:** Assess the authentication and access layer for suspicious indicators — risky sign-ins, conditional access gaps, privilege escalation, and credential compromise signals. A compromised identity changes the investigation from insider threat to external threat. This phase produces the **Identity Risk Assessment**.

---

### 6.1 Sign-In Risk & Authentication Analysis

**Title:** Analyze the User's Sign-In Patterns for Risk Indicators and Authentication Anomalies

**Purpose:** Determine if the account performing the DLP-triggering action was legitimately used by the employee or potentially compromised.

**When to Use:** Phase 6 entry point — especially critical when device or location analysis reveals anomalies.

**SC System Capability Used:** Get User Risk Summary

**Exact Prompt:**
```
Analyze the sign-in risk profile for <userUPN> over the last 30 days:

SIGN-IN RISK ASSESSMENT:
1. What is the user's current Entra ID sign-in risk level (High, Medium, Low, None)?
2. What is their user risk level (High, Medium, Low, None)?
3. Have there been any sign-ins flagged as risky? For each:
   - Timestamp
   - Risk level and risk detail (e.g., unfamiliar sign-in, atypical travel, anonymous IP, malware-linked IP)
   - Sign-in location (country, city, IP address)
   - Device and browser used
   - Whether the sign-in succeeded or was blocked
   - Whether MFA was required and completed

AUTHENTICATION PATTERNS:
4. What authentication methods does this user have registered? (Authenticator app, phone, FIDO2 key, SMS)
5. Have any new authentication methods been registered recently?
6. Have there been any MFA fatigue attacks (repeated push notifications) or MFA bypass attempts?
7. Has the user's password been changed recently? If so, was it initiated by the user or by an admin?

LOCATION & IP ANALYSIS:
8. What are the user's normal sign-in locations (country, city)?
9. Have there been sign-ins from unusual locations or impossible travel patterns?
10. Have there been sign-ins from anonymizing services (VPN, Tor) or known-malicious IP addresses?
11. Were there simultaneous sessions from different geographic locations?

SESSION ANALYSIS:
12. Were there any sign-in sessions active during the DLP alert that originated from an unusual source?
13. How many concurrent sessions did the user have at the time of the alert?
14. Are there any token theft indicators (sign-ins without a primary refresh token, cross-device session anomalies)?

Assess: Is there any evidence that the account was compromised, or do the sign-in patterns support legitimate user activity? State your confidence level.
```

**Expected Output:**
- Sign-in and user risk levels from Entra ID
- Risky sign-in event inventory with details
- Authentication method assessment
- Location and IP anomaly analysis
- Session analysis around DLP alert timestamp
- Account compromise assessment with confidence level

**Chain-Next:** 6.2 for conditional access evaluation

**Limitations:**
- Entra ID risk detection requires P2 licensing
- VPN usage may obscure true location
- Sophisticated attackers may use residential proxies that don't trigger anonymous IP detection
- Token theft may not be detected if the attacker replays tokens from a trusted location

**Tips:**
- If you see impossible travel (sign-ins from two countries within an hour), immediately investigate for account compromise before continuing the insider threat analysis
- New authentication method registration (especially phone number change) near the DLP alert timeline is a compromise indicator
- MFA fatigue (10+ push notifications in sequence) followed by a successful sign-in is a strong compromise signal

---

### 6.2 Conditional Access & Authorization Gap Analysis

**Title:** Evaluate Whether Conditional Access Policies Adequately Protected the Data in This Alert

**Purpose:** Determine if the DLP violation occurred because conditional access policies had gaps, were misconfigured, or were legitimately bypassed.

**When to Use:** After 6.1, especially when the sign-in or device context reveals non-standard access patterns.

**SC System Capability Used:** Get Data Risk Summary

**Exact Prompt:**
```
Evaluate the conditional access context for <userUPN>'s action that triggered DLP alert <alertId>:

CONDITIONAL ACCESS EVALUATION:
1. What conditional access policies were evaluated during the user's session?
2. Which policies were applied (enforced), and which were not applicable?
3. Were any policies in report-only mode that would have blocked the action if enforced?
4. Was the session subject to:
   - MFA requirement?
   - Compliant device requirement?
   - Approved app requirement?
   - Location-based restrictions?
   - Sign-in risk-based restrictions?
   - Session control (limited browser experience, app-enforced restrictions)?

ACCESS GAPS:
5. Could this DLP violation have been prevented by a conditional access policy that is not currently deployed?
6. Is the user accessing sensitive data from an unmanaged device that should be restricted?
7. Is the user accessing data from a location that should be blocked?
8. Are there app consent grants that bypass conditional access (legacy auth, direct API access)?

PRIVILEGE ASSESSMENT:
9. Does the user have any admin role assignments?
10. Have they been granted any new permissions in the last 30 days?
11. Are they a member of any privileged groups that grant elevated data access?
12. Have they activated any Privileged Identity Management (PIM) roles recently?

Identify the top 3 conditional access improvements that would reduce the risk of similar incidents.
```

**Expected Output:**
- Conditional access policy evaluation summary
- Policy gaps and report-only policy analysis
- Access restriction assessment
- Privilege and permission inventory
- Top 3 recommended conditional access improvements

**Chain-Next:** Phase 7 (Data Movement Mapping) with full identity context

**Limitations:**
- Conditional access evaluation details require Entra ID P1/P2
- Legacy authentication protocols may bypass conditional access entirely
- PIM role activation logs require Azure AD PIM licensing

**Tips:**
- Report-only conditional access policies that would have blocked the action represent immediate hardening opportunities — recommend switching to enforce
- Legacy authentication (SMTP, IMAP, POP) bypasses modern conditional access — check if the user has legacy auth activity
- A user with Global Admin + active PIM role accessing sensitive data is either a legitimate admin action or the most critical compromise scenario possible

---

## Phase 7 — Data Flow Mapping & Exfiltration Verification

> **Objective:** Synthesize findings from all prior phases to map the complete data flow from origin to final destination. Determine with confidence whether data exfiltration occurred, is occurring, or was attempted but prevented. This phase produces the **Exfiltration Determination**.

---

### 7.1 Complete Data Flow Reconstruction

**Title:** Map the End-to-End Data Flow from Origin to Every Known Destination

**Purpose:** Combine cloud logs, endpoint data, and communication analysis into a single data flow map that shows exactly where the sensitive data has traveled.

**When to Use:** After Phases 2-6 provide data, user, device, and identity context.

**SC System Capability Used:** Zoom Into Purview Data Risk + Zoom Into Purview User Risk

**Exact Prompt:**
```
Using all findings from this investigation, reconstruct the complete data flow for the sensitive content involved in DLP alert <alertId>:

DATA FLOW MAP (chronological):
1. ORIGIN: Where was the data created/stored originally? Who owns it?
2. ACCESS: When and how did <userUPN> first access this data? Was access authorized?
3. COLLECTION: Was the data downloaded, copied, or aggregated? What volume?
4. STAGING: Was the data staged locally (renamed, compressed, moved to a collection folder)?
5. EXFILTRATION: Was the data sent, shared, copied, or uploaded outside the organization?
6. DESTINATION: Where is the data now? (External recipient, USB device, personal cloud, printed copy)

For each step, provide:
- Timestamp
- Action type
- Source and destination
- Detection status (blocked, detected, undetected)
- Evidence source (DLP alert, audit log, MDE alert, IRM indicator)

CONTAINMENT STATUS:
7. Is the data still within organizational control?
8. How many copies exist outside the organization?
9. Can the external copies be recalled or revoked? (SharePoint sharing links can be revoked; email attachments cannot)
10. Is there evidence of further distribution by external recipients?

EXFILTRATION DETERMINATION:
Based on the complete data flow, classify this incident as:
A) No exfiltration — data movement was blocked or contained within the organization
B) Attempted exfiltration — user attempted to move data externally but was prevented
C) Partial exfiltration — some data left the organization but the full scope was prevented
D) Complete exfiltration — sensitive data successfully left organizational control
E) Ongoing exfiltration — data is still being moved and immediate containment is required

State your confidence level and cite the specific evidence chain supporting this determination.
```

**Expected Output:**
- Chronological data flow map with timestamps and evidence sources
- Containment status assessment
- External copy inventory
- Exfiltration determination (A/B/C/D/E) with evidence chain
- Confidence level

**Chain-Next:** 7.2 for DLP evasion technique analysis

**Limitations:**
- Complete data flow reconstruction requires data from multiple sources (Purview, MDE, Entra ID)
- Offline exfiltration (photos, handwritten notes) cannot be mapped
- External distribution by recipients is outside organizational visibility

**Tips:**
- Classification E (Ongoing) requires immediate escalation — do not continue investigation without initiating containment
- For classification C/D, immediately engage Legal and begin breach assessment
- This data flow map is the most important artifact in your investigation — it will be requested by legal, compliance, and executive leadership

---

### 7.2 DLP Evasion Technique Assessment

**Title:** Analyze Whether the User Employed Techniques to Evade DLP Detection

**Purpose:** Sophisticated insiders know about DLP. Evasion techniques indicate intent and premeditation.

**When to Use:** When exfiltration evidence exists or when the investigation suggests intentional behavior.

**SC System Capability Used:** Zoom Into Purview User Risk

**Exact Prompt:**
```
Analyze <userUPN>'s activities for indicators of deliberate DLP evasion:

FILE MANIPULATION EVASION:
1. Did the user rename files to remove sensitive keywords or change extensions?
2. Did the user split large files into smaller parts below detection thresholds?
3. Did the user compress or encrypt files before sharing (password-protected zips, encrypted archives)?
4. Did the user convert file formats (e.g., Excel to CSV, Word to plain text) to bypass content inspection?
5. Did the user embed sensitive data within images or non-standard file types (steganography indicators)?

CHANNEL SWITCHING EVASION:
6. After being blocked on one channel (e.g., email), did the user attempt the same data movement via another channel (e.g., Teams, USB, cloud upload)?
7. Did the user switch between workloads to find an unmonitored path?
8. Did the user use personal devices or unmanaged endpoints to bypass endpoint DLP?

POLICY EXPLOITATION:
9. Did the user exploit DLP override mechanisms (providing false justifications)?
10. Did the user add themselves to exception groups or request policy exceptions?
11. Did the user time their actions during known monitoring gaps (maintenance windows, off-hours with reduced SOC coverage)?
12. Did the user share data with an internal "relay" user who then shared it externally?

DETECTION COUNTER-MEASURES:
13. Did the user delete or modify files after the DLP alert fired?
14. Did the user clear browser history, recycle bin, or recent file lists?
15. Did the user remove auto-forwarding rules or sharing links after detection?
16. Did the user use any obfuscation tools or techniques?

Intent Assessment:
Based on the evasion indicators found, classify the user's intent level:
A) No evasion — actions are consistent with unintentional policy violation
B) Low evasion awareness — basic actions that incidentally avoid some detection (not necessarily deliberate)
C) Moderate deliberate evasion — clear attempts to work around specific controls
D) Sophisticated premeditated evasion — multi-technique approach indicating knowledge of security controls

Cite specific evidence for the intent classification.
```

**Expected Output:**
- File manipulation evasion indicator analysis
- Channel switching detection
- Policy exploitation assessment
- Detection counter-measure identification
- Intent classification (A/B/C/D) with evidence

**Chain-Next:** Phase 8 (Evidence Collection & Forensic Timeline) — intent classification informs investigation conclusions

**Limitations:**
- Sophisticated evasion may not leave detectable traces
- Encryption before transfer makes content inspection impossible
- Some evasion indicators have benign explanations (zip for convenience, not evasion)

**Tips:**
- Channel switching after being blocked is the clearest indicator of deliberate evasion — document the sequence and timestamps precisely
- False override justifications can be established by comparing the stated justification against the actual content and recipient
- If you reach Classification D, you are likely dealing with a premeditated insider threat — ensure Legal is engaged

---

## Phase 8 — Forensic Evidence Collection & Timeline

> **Objective:** Consolidate all findings into a forensic-grade evidence package. Build the definitive timeline, document the evidence chain, and preserve artifacts for potential legal proceedings. This phase produces the **Forensic Evidence Package**.

---

### 8.1 Master Timeline Construction

**Title:** Build the Definitive Forensic Timeline Integrating All Investigation Phases

**Purpose:** Merge all temporal data from Phases 1-7 into a single, authoritative timeline that tells the complete story of this incident.

**When to Use:** After Phases 1-7 are complete or substantially complete.

**SC System Capability Used:** Summarize Purview Alert + Zoom Into Purview User Risk + Zoom Into Purview Data Risk

**Exact Prompt:**
```
Build a comprehensive forensic master timeline for the investigation of <userUPN> related to DLP alert <alertId>. Integrate all findings into a single chronological narrative:

TIMELINE SCOPE: From the earliest relevant event (up to 90 days before the DLP alert) through the present.

For each event, capture:
- Exact timestamp (UTC and local time)
- Event type (HR action, access, download, share, sign-in, DLP alert, IRM indicator, device event)
- Event detail (what happened specifically)
- Source (which log, alert, or system provided this data)
- Significance (how this event relates to the investigation)
- Evidence reference (ID or link for the source record)

ORGANIZE THE TIMELINE INTO PHASES:
1. **Background/Context** — HR triggers, role changes, behavioral baseline shifts
2. **Reconnaissance** — Unusual searches, browsing, or access exploration
3. **Collection/Staging** — Downloads, copies, aggregation of sensitive data
4. **Exfiltration Attempts** — Sharing, sending, uploading, USB copying
5. **Detection & Response** — DLP alerts, blocks, the current investigation
6. **Post-Detection Activity** — User's behavior after alerts fired (evasion, continuation, or cessation)

NARRATIVE SUMMARY:
After the timeline, provide a 3-5 paragraph narrative that tells the story of this incident from the investigation's perspective:
- What happened and when
- What was the likely motivation (if determinable)
- What was the impact (data exposed, contained, or prevented)
- What is the confidence level of this reconstruction

Flag any gaps in the timeline where evidence is unavailable or ambiguous.
```

**Expected Output:**
- Complete chronological forensic timeline with all events
- Phase-organized view of the incident lifecycle
- Narrative summary of the incident story
- Evidence gaps identification
- Source references for every event

**Chain-Next:** 8.2 for evidence chain documentation

**Limitations:**
- Timeline completeness depends on log retention and coverage across systems
- Offline activities create unbridgeable gaps
- Timestamps across systems may have minor discrepancies (clock skew)

**Tips:**
- The narrative summary is what leadership and legal will read first — make it clear, factual, and free of speculation
- Flag evidence gaps explicitly — unstated gaps are worse than acknowledged ones
- If the timeline reveals a pattern you hadn't considered, loop back to relevant phases for additional analysis

---

### 8.2 Evidence Chain & Artifact Preservation

**Title:** Document the Evidence Chain and Identify All Artifacts Requiring Preservation

**Purpose:** Ensure all digital evidence is identified, documented, and preserved with chain-of-custody integrity for potential legal or disciplinary proceedings.

**When to Use:** When the investigation will be escalated to Legal, HR, or law enforcement.

**SC System Capability Used:** Get Data Risk Summary

**Exact Prompt:**
```
Document the complete evidence chain for the investigation of <userUPN> / DLP alert <alertId>:

EVIDENCE INVENTORY:
For each piece of evidence discovered during this investigation, document:
1. Evidence ID (assign sequential number)
2. Evidence type (alert, log entry, file, email, configuration, screenshot)
3. Source system (Purview DLP, Purview IRM, Entra ID, MDE, Exchange, SharePoint)
4. Original location (URL, path, or system reference)
5. Date/time of the evidenced event
6. Date/time evidence was collected/reviewed
7. Relevance (what does this evidence prove or indicate?)
8. Integrity (is this evidence tamper-evident, is it from an immutable log?)

PRESERVATION REQUIREMENTS:
9. Which logs have retention limits that require immediate preservation holds?
10. Are there mailbox or OneDrive litigation holds that should be placed?
11. Are there audit logs approaching their retention limit that need export?
12. Are there device forensic images that should be captured before device reuse or wiping?
13. Are there user accounts that should be preserved (not deleted) even after termination?

CHAIN OF CUSTODY:
14. Who has accessed or reviewed each piece of evidence?
15. Has any evidence been modified, exported, or copied during the investigation?
16. Are there any evidence integrity concerns?

GAPS & LIMITATIONS:
17. What evidence would strengthen the case but is unavailable?
18. What alternative explanations have not been fully ruled out?
19. What assumptions were made during the investigation?

Provide a summary evidence sufficiency assessment:
- Is there sufficient evidence for a formal HR action?
- Is there sufficient evidence for legal proceedings?
- What additional evidence would be needed to reach a higher standard of proof?
```

**Expected Output:**
- Numbered evidence inventory with full documentation
- Preservation action list with urgency
- Chain of custody documentation
- Evidence gaps and limitations
- Evidence sufficiency assessment for HR/Legal

**Chain-Next:** Phase 9 (Impact Assessment)

**Limitations:**
- SC cannot place litigation holds or export logs — analyst must perform these actions manually
- Evidence sufficiency standards vary by jurisdiction and organizational policy
- Some evidence may be privileged (attorney-client) and require legal review before sharing

**Tips:**
- Place litigation holds IMMEDIATELY — do not wait for the investigation to complete
- Audit logs in M365 have a maximum retention of 180 days (E5) or 90 days (E3) — if your investigation is approaching these limits, export now
- Device forensic images should be captured by qualified forensic personnel, not by the analyst conducting the investigation

---

## Phase 9 — Impact Assessment & Blast Radius

> **Objective:** Quantify the impact of this incident across data, regulatory, business, and reputational dimensions. This phase produces the **Impact Assessment** that drives executive communication and remediation priority.

---

### 9.1 Data Exposure Quantification

**Title:** Quantify the Total Data Exposure — Records, Individuals, and Sensitivity Levels

**Purpose:** Move from "some data was exposed" to precise numbers that legal, compliance, and leadership need for decision-making.

**When to Use:** After Phase 7 determines the exfiltration status and Phase 8 documents the evidence.

**SC System Capability Used:** Get Data Risk Summary + Zoom Into Purview Data Risk

**Exact Prompt:**
```
Quantify the total data exposure from this incident involving <userUPN> / DLP alert <alertId>:

DATA VOLUME:
1. How many files or documents were involved in total across all related alerts and activities?
2. What is the total data volume (MB/GB) that was exposed or exfiltrated?
3. How many unique sensitive information type instances were detected across all involved content?

INDIVIDUAL IMPACT:
4. How many unique individuals' data was potentially exposed? (Distinct SSNs, email addresses, names)
5. What categories of individuals are affected? (Customers, employees, patients, partners, public)
6. What types of personal data were exposed for each category? (PII, PHI, financial, credentials)

SENSITIVITY BREAKDOWN:
7. Break down the exposed data by sensitivity level:
   - Highly Confidential / Regulated: [count and types]
   - Confidential / Business Critical: [count and types]
   - Internal Only: [count and types]
   - General / Low Sensitivity: [count and types]

EXPOSURE STATUS:
8. For each data category, what is the current exposure status:
   - Contained (still within organizational control)
   - Revocable (shared externally but links can be revoked)
   - Exposed (externally accessible, cannot be recalled)
   - Unknown (insufficient evidence to determine status)

9. What is the worst-case vs. confirmed exposure scenario?
   - Worst case: All content reached external recipients and was forwarded
   - Confirmed: Only what we can verify left the organization

Provide the exposure numbers that will be needed for:
- Breach notification to affected individuals
- Regulatory reporting to supervisory authorities
- Executive communication to board/leadership
```

**Expected Output:**
- Total data volume and file count
- Unique individual count with categories
- Sensitivity level breakdown
- Exposure status per category
- Worst-case vs. confirmed exposure numbers
- Numbers formatted for regulatory reporting

**Chain-Next:** 9.2 for regulatory and business impact assessment

**Limitations:**
- Exact individual counts may require manual content review
- "Unknown" exposure status cannot be assumed as "not exposed" for regulatory purposes
- Worst-case numbers may be significantly higher than confirmed — communicate both

**Tips:**
- Regulators will ask for the number of affected individuals — have this number ready
- For GDPR, count EU data subjects specifically; for HIPAA, count patients; for PCI, count cardholders
- Conservative estimates protect the organization — do not undercount exposure for optimism

---

### 9.2 Regulatory, Business, & Reputational Impact Assessment

**Title:** Assess the Full Impact Across Regulatory, Business, Financial, and Reputational Dimensions

**Purpose:** Provide the comprehensive impact assessment that leadership, legal, and compliance need to make informed decisions about notification, remediation, and disclosure.

**When to Use:** After 9.1 quantifies the data exposure.

**SC System Capability Used:** Get Data Risk Summary

**Exact Prompt:**
```
Based on the data exposure quantification for <userUPN> / DLP alert <alertId>, assess the full impact:

REGULATORY IMPACT:
1. Which regulatory breach notification requirements are triggered?
   - GDPR (Article 33/34): Is notification to supervisory authority and data subjects required?
   - HIPAA: Is this a reportable breach to HHS?
   - PCI DSS: Is card brand notification required?
   - State breach laws: Which US states' notification requirements apply?
   - Industry-specific: Are there sector-specific reporting requirements?
2. What are the notification deadlines, and has the clock started?
3. What is the potential fine exposure per regulation?
4. Are there contractual notification obligations to clients or partners?

BUSINESS IMPACT:
5. Was intellectual property (trade secrets, source code, product roadmaps) exposed?
6. Was competitive intelligence (pricing, strategy, M&A plans) exposed?
7. Were customer relationships at risk (customer data shared with competitor)?
8. Is there operational disruption from containment actions (account suspension, device seizure)?
9. What is the estimated financial impact (regulatory fines + legal costs + remediation + business loss)?

REPUTATIONAL IMPACT:
10. Is this incident likely to become public? (Regulatory filing, media, affected individual notification)
11. What is the expected reputational impact if the incident becomes public?
12. Are there existing media, regulatory, or legal proceedings that this incident compounds?

IMPACT CLASSIFICATION:
- Critical: Mandatory regulatory notification, significant financial exposure, likely public disclosure
- High: Regulatory notification probable, material financial impact, potential public disclosure
- Medium: Regulatory notification uncertain, moderate financial impact, internal disclosure sufficient
- Low: No regulatory notification required, minimal financial impact, no public disclosure risk

Provide an executive-ready impact summary (5 sentences or less) suitable for immediate leadership communication.
```

**Expected Output:**
- Regulatory impact with notification requirements and deadlines
- Business impact assessment with financial estimate
- Reputational impact assessment
- Impact classification with rationale
- Executive-ready 5-sentence summary

**Chain-Next:** Phase 10 (Containment & Remediation Recommendations)

**Limitations:**
- Financial impact estimates are approximations — actual costs require legal and finance review
- Regulatory notification determination should be made by Legal, not by the investigation team alone
- Reputational impact assessment is inherently subjective

**Tips:**
- The executive summary will be forwarded widely — make it factual, proportional, and free of jargon
- If any regulatory notification deadline is within 72 hours, flag this as the #1 action item
- Always present worst-case and confirmed numbers side by side — never only present the optimistic scenario

---

## Phase 10 — Containment, Remediation & Recommendations

> **Objective:** Transition from investigation to action. Provide specific, prioritized containment actions, remediation steps, and long-term recommendations. This phase produces the **Action Plan**.

---

### 10.1 Immediate Containment Actions

**Title:** Generate Prioritized Immediate Containment Actions Based on Investigation Findings

**Purpose:** Stop the bleeding. Prevent further data exposure while the full remediation plan is developed.

**When to Use:** As soon as exfiltration is confirmed or ongoing (Phase 7 Classification C/D/E), or at investigation conclusion for all cases.

**SC System Capability Used:** Get User Risk Summary + Get Data Risk Summary

**Exact Prompt:**
```
Based on the complete investigation of <userUPN> / DLP alert <alertId>, generate prioritized immediate containment actions:

IDENTITY CONTAINMENT (0-1 hours):
1. Should the user's account be suspended, password reset forced, or sessions revoked?
2. Should MFA be reset and re-enrolled?
3. Should conditional access policies be tightened for this user?
4. Should app consent grants be revoked?
5. What is the risk of tipping off the user vs. the risk of continued access?

DATA CONTAINMENT (0-4 hours):
6. Which sharing links should be revoked immediately?
7. Should any SharePoint sites, OneDrive folders, or mailboxes be placed on litigation hold?
8. Should auto-forwarding rules be removed?
9. Should sensitivity labels be upgraded on exposed content?
10. Can any external shares be recalled or expired?

DEVICE CONTAINMENT (0-8 hours):
11. Should the device be quarantined, wiped, or isolated from the network?
12. Should USB access be disabled for this user/device?
13. Should a forensic image be captured before any device actions?

MONITORING ESCALATION (immediate):
14. Should enhanced monitoring be enabled for this user?
15. Should related users (from Phase 4.4) be placed under monitoring?
16. Should DLP policies be switched from audit-only to block mode for the affected policies?

For each action, provide:
- Priority (P0 immediate, P1 within hours, P2 within 24 hours)
- Risk of the action (user disruption, evidence destruction, legal exposure)
- Who should authorize (SOC, Security Manager, CISO, Legal, HR)
- Dependencies (must do X before Y)

Present as a prioritized action checklist.
```

**Expected Output:**
- Prioritized containment checklist organized by domain and urgency
- Risk assessment for each action
- Authorization requirements
- Dependencies and sequencing
- Immediate actions vs. actions requiring approval

**Chain-Next:** 10.2 for long-term remediation and policy recommendations

**Limitations:**
- SC cannot execute containment actions — analyst and authorized personnel must perform them
- Some containment actions (account suspension) may tip off the subject and trigger evidence destruction
- Device forensic image must be captured BEFORE any device containment actions

**Tips:**
- ALWAYS capture a forensic image before device wipe or quarantine
- Discuss user-facing containment actions (account suspension) with Legal and HR before execution — timing matters
- If exfiltration is ongoing (Phase 7 Classification E), identity containment is P0 — do it now, coordinate with Legal after

---

### 10.2 Long-Term Remediation & Policy Hardening

**Title:** Develop Long-Term Remediation and Security Posture Improvements Based on Investigation Learnings

**Purpose:** Ensure this incident drives lasting improvement — not just a one-time fix.

**When to Use:** After containment actions are in place.

**SC System Capability Used:** Get Data Risk Summary

**Exact Prompt:**
```
Based on the complete investigation and findings for <userUPN> / DLP alert <alertId>, recommend long-term remediation and policy improvements:

DLP POLICY IMPROVEMENTS:
1. Were there policy gaps that allowed this incident? What specific rules should be added or tightened?
2. Should any audit-only policies be moved to block/notify mode?
3. Are there SIT detection gaps (custom SITs needed for organization-specific data)?
4. Should endpoint DLP coverage be expanded to additional workloads?
5. Should DLP override justifications be tightened or require manager approval?

IRM POLICY IMPROVEMENTS:
6. Should IRM policies be tuned to detect the specific behavioral sequence observed?
7. Are there triggering event gaps (HR signals not flowing into IRM)?
8. Should sequence detection thresholds be adjusted?
9. Should new indicator groups be created for the patterns observed?

IDENTITY & ACCESS IMPROVEMENTS:
10. Should conditional access policies be strengthened based on the access patterns observed?
11. Are there excessive permissions that should be reduced (principle of least privilege)?
12. Should session controls be tightened for sensitive data access?
13. Should legacy authentication be fully blocked?

DATA GOVERNANCE IMPROVEMENTS:
14. Should sensitivity labeling requirements be tightened?
15. Are there data classification gaps that allowed sensitive data to be insufficiently protected?
16. Should sharing policies be more restrictive by default?

PROCESS IMPROVEMENTS:
17. Should incident response playbooks be updated based on lessons from this investigation?
18. Are there monitoring gaps that delayed detection?
19. Should analyst training be updated to cover the techniques observed?
20. Should security awareness training for end users be enhanced?

For each recommendation:
- Effort level (quick win, moderate, significant project)
- Impact level (high/medium/low risk reduction)
- Recommended timeline
- Owner/team responsible

Prioritize by impact-to-effort ratio — quick wins with high impact first.
```

**Expected Output:**
- DLP, IRM, Identity, Data Governance, and Process recommendations
- Each with effort, impact, timeline, and ownership
- Prioritized roadmap
- Quick wins highlighted

**Chain-Next:** 10.3 for executive investigation summary

**Limitations:**
- Policy changes should be tested in audit/simulation mode before enforcement
- Some recommendations may require additional licensing or tooling
- Organizational change management may slow implementation

**Tips:**
- Quick wins (enable a blocked policy, revoke excessive permissions) should be implemented within days, not weeks
- Use this investigation as the business case for recommendations you've wanted to make — real incidents are the strongest justification
- Track recommendation implementation — unaddressed findings from one investigation become risk acceptance for the next

---

### 10.3 Executive Investigation Summary

**Title:** Generate an Executive-Ready Investigation Summary with Findings, Impact, and Recommendations

**Purpose:** Produce the final deliverable — a concise, authoritative summary of the investigation suitable for CISO, legal, HR, and executive leadership.

**When to Use:** Upon investigation completion as the final prompt.

**SC System Capability Used:** Get Data Risk Summary + Get User Risk Summary

**Exact Prompt:**
```
Generate an executive investigation summary for the DFIR investigation of <userUPN> triggered by DLP alert <alertId>:

HEADER:
- Investigation ID
- Date range of investigation
- Investigating analyst
- Classification: [Confirmed Incident / Attempted Incident / False Positive / Inconclusive]
- Severity: [Critical / High / Medium / Low]
- Status: [Open / Contained / Remediated / Closed]

EXECUTIVE SUMMARY (5-7 sentences):
Concise narrative of what happened, who was involved, what data was affected, and what was done about it. Written for a non-technical executive audience.

KEY FINDINGS (bullet points):
1. What triggered the investigation
2. What the user did (factual, evidence-based)
3. What data was involved (types, volume, sensitivity)
4. Whether data left the organization
5. What the user's likely intent was (with confidence level)
6. Whether other users were involved

IMPACT ASSESSMENT:
- Data exposure: [X records / Y individuals / Z files]
- Regulatory: [notification required / not required / under review by Legal]
- Business: [IP loss / customer data / financial data / operational impact]
- Financial: [estimated total cost range]

ACTIONS TAKEN:
- Containment actions completed
- Evidence preserved
- Notifications issued

OPEN ITEMS:
- Pending actions requiring authorization
- Follow-up investigations needed
- Policy changes in progress

RECOMMENDATIONS (top 5):
Prioritized list of the most impactful security improvements

APPENDICES (references):
- Full timeline reference
- Evidence inventory reference
- Detailed findings from each phase
```

**Expected Output:**
- Complete executive investigation report
- Professional formatting suitable for CISO/Legal/Board presentation
- Clear classification and severity assignment
- Actionable recommendations with ownership
- Reference to detailed supporting artifacts

**Chain-Next:** Investigation complete. Archive investigation file. Schedule 30-day follow-up for recommendation implementation review.

**Limitations:**
- SC generates the structure and content based on what was investigated; analyst must review for accuracy, completeness, and tone
- Legal review of the summary is required before sharing with external parties
- Some findings may be attorney-client privileged — consult Legal before distribution

**Tips:**
- Have Legal review the executive summary before distribution
- The executive summary will likely be forwarded to people you don't expect — write accordingly
- Include confidence levels for all key findings — "confirmed" vs. "suspected" vs. "inconclusive"
- End with clear ownership for each recommendation — unowned actions don't get done

---

## Quick Reference: Investigation Phase Map

```
┌─────────────────────────────────────────────────────────────────┐
│                    DLP ALERT RECEIVED                           │
└─────────────────────┬───────────────────────────────────────────┘
                      │
          ┌───────────▼───────────┐
          │   PHASE 1: TRIAGE     │  ← 15-30 min
          │   (1.1-1.4)           │    Severity + Pattern + Risk
          └───────────┬───────────┘
                      │
        ┌─────────────┼─────────────┐
        │ ISOLATED    │  PATTERN    │
        ▼             ▼             ▼
  ┌───────────┐ ┌───────────┐ ┌───────────┐
  │ PHASE 2   │ │ PHASE 2   │ │ PHASE 4   │  ← Run 2+4
  │ Data Deep │ │ Data Deep │ │ IRM Check │    in parallel
  │ Dive      │ │ Dive      │ │           │    for patterns
  └─────┬─────┘ └─────┬─────┘ └─────┬─────┘
        │              │             │
        └──────────────┼─────────────┘
                       │
            ┌──────────▼──────────┐
            │   PHASE 3: USER     │  ← 30-60 min
            │   BEHAVIORAL        │    Identity + Baseline +
            │   (3.1-3.4)         │    Timeline + Comms
            └──────────┬──────────┘
                       │
            ┌──────────▼──────────┐
            │   PHASE 4: IRM      │  ← 30-45 min
            │   CORRELATION       │    Alerts + Exfil +
            │   (4.1-4.4)         │    Departure + Peers
            └──────────┬──────────┘
                       │
              ┌────────┼────────┐
              ▼                 ▼
      ┌──────────────┐ ┌──────────────┐
      │  PHASE 5:    │ │  PHASE 6:    │  ← Run 5+6
      │  DEVICE &    │ │  IDENTITY &  │    in parallel
      │  ENDPOINT    │ │  ACCESS      │
      └──────┬───────┘ └──────┬───────┘
             │                │
             └────────┬───────┘
                      │
           ┌──────────▼──────────┐
           │   PHASE 7: DATA     │  ← 30-45 min
           │   FLOW MAP &        │    Complete flow +
           │   EXFIL VERIFY      │    Evasion check
           └──────────┬──────────┘
                      │
           ┌──────────▼──────────┐
           │   PHASE 8: EVIDENCE │  ← 30-60 min
           │   & FORENSIC        │    Timeline + Chain
           │   TIMELINE          │    of custody
           └──────────┬──────────┘
                      │
           ┌──────────▼──────────┐
           │   PHASE 9: IMPACT   │  ← 30-45 min
           │   ASSESSMENT        │    Regulatory + Business
           └──────────┬──────────┘
                      │
           ┌──────────▼──────────┐
           │  PHASE 10: ACTION   │  ← 30-60 min
           │  Contain + Remediate│    Containment + Hardening
           │  + Executive Report │    + Exec Summary
           └─────────────────────┘
```

## Fast-Track Mode (45-90 minutes)

For time-critical investigations, execute this abbreviated sequence:

| Step | Prompts | Focus |
|------|---------|-------|
| 1 | 1.1 + 1.4 (parallel) | Alert intake + immediate risk check |
| 2 | 2.1 + 2.2 (parallel) | Content sensitivity + destination risk |
| 3 | 3.1 + 3.3 (parallel) | User identity + 72-hour timeline |
| 4 | 4.1 | IRM correlation |
| 5 | 6.1 | Sign-in risk check |
| 6 | 7.1 | Data flow determination |
| 7 | 10.1 + 10.3 (parallel) | Containment actions + executive summary |

## Parameter Reference

| Parameter | Description | Example |
|-----------|-------------|---------|
| `<alertId>` | DLP alert identifier from Purview | `DLP-2026-04-00847` |
| `<userUPN>` | User principal name (email) | `jsmith@contoso.com` |
| `<policyName>` | DLP policy that triggered | `Financial Data - External Sharing Block` |
| `<alertTimestamp>` | UTC timestamp of the alert | `2026-04-06T14:32:00Z` |
| `<deviceName>` | Device name from MDE/Intune | `LAPTOP-JS2847` |
| `<matchCount>` | SIT match count from alert | `247` |
| `<sitType>` | Sensitive information type | `U.S. Social Security Number` |
| `<peerGroup>` | IRM peer group for baseline | `Finance - Senior Analysts` |

---

**Version:** 1.0
**Last Updated:** 2026-04-06
**Status:** [ASPIRATIONAL]
**Author:** Security Copilot Lab
**Prompt Count:** 40 prompts across 10 phases
**Estimated Duration:** 2-6 hours (full) / 45-90 minutes (fast-track)
