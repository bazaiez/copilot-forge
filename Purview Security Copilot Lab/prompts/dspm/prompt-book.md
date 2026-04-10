# DSPM Prompt Book: Security Copilot for Data Security Posture Management

**Validation Status:** 5 [VALIDATED] / 1 [PARTIALLY-VALIDATED] / 2 [ASPIRATIONAL]
**Required Plugin:** Microsoft Purview
**Last Validated:** April 2026

This prompt book provides Security Copilot users with practical prompts for assessing, analyzing, and remediating data security posture across Microsoft Purview. Prompts use the DSPM Posture Agent capabilities including natural language data discovery, risk assessments, and policy recommendations.

**Key Capabilities Used:**
- Get Data Risk Summary
- Analyze data classification coverage and sensitive data exposure
- Natural language data discovery and risk assessment
- One-click policy recommendations

**Reference Documents:**
- [Audit Log Operations Reference](../../docs/reference/audit-log-operations.md) — Exact operation names
- [Sensitive Information Types Reference](../../docs/reference/sensitive-information-types.md) — Exact SIT names
- [Plugin Dependency Map](../../docs/plugin-dependency-map.md) — Required plugins per section
- [Validation Matrix](../validation-matrix.md) — Testing status for each prompt
- [Error Recovery](../taxonomy.md#error-recovery-and-troubleshooting) — Recovery when SC returns unexpected results

---

## 1. POSTURE ASSESSMENT

Understanding organizational data risk and security gaps.

### 1.1 Executive Data Risk Dashboard [VALIDATED]

**Title:** Executive-Focused Data Risk Overview

**Purpose:** Obtain high-level summary of organizational data risk in format suitable for leadership reporting.

**When to Use:** Monthly/quarterly business reviews, executive briefings, board reporting, risk committee meetings.

**SC System Capability Used:** Get Data Risk Summary

**Exact Prompt:**
```
Analyze our current data security posture and provide a risk summary covering:
1. Overall data at risk score or percentage
2. Top 3-5 critical data risk categories
3. Number of sensitive file types exposed or unclassified
4. Comparison to previous month
5. One key recommendation for leadership focus
Format as a brief executive dashboard view.
```

**Expected Output:**
- Current overall risk level (High/Medium/Low)
- Data at risk percentage (% of sensitive data unprotected)
- Top risks (ranked list with file/user counts)
- Trend (Improving/Declining/Stable)
- Key single priority recommendation for leadership
- Business impact statement

**Chain-Next:** "What are the specific data exposure categories driving our top risks?"

**Limitations:**
- May oversimplify technical nuances
- Requires baseline historical data for trend comparison
- Does not provide remediation details
- Risk score varies by scanning frequency

**Tips:**
- Request monthly to build executive awareness
- Track quarterly trends to demonstrate improvement
- Quantify business impact in revenue/compliance terms
- Include benchmark comparisons if available

---

### 1.2 Sensitive Data Exposure and Classification Analysis [VALIDATED]

**Title:** Assess Coverage and Exposure of Sensitive Data Types

**Purpose:** Understand what sensitive data exists, how much is classified, and what sensitive data lacks protection.

**When to Use:** During posture assessment or when addressing compliance requirements; data inventory phase.

**SC System Capability Used:** Get Data Risk Summary

**Exact Prompt:**
```
Provide a detailed analysis of sensitive data exposure in our environment:
1. What types of sensitive information are present (PII, financial data, trade secrets, health data)?
2. What percentage of each type is classified vs. unclassified?
3. Which locations have the highest concentration of unprotected sensitive data (SharePoint, Teams, OneDrive)?
4. What is the exposure risk for unclassified sensitive data?
5. What percentage of users have regular access to sensitive data without DLP oversight?
```

**Expected Output:**
- Inventory of sensitive data types with counts
- Classification coverage by data type (% classified)
- High-risk locations (SharePoint, Teams, OneDrive, cloud apps)
- Unclassified sensitive data volume
- User access patterns to sensitive data
- Exposure risk assessment
- Gap analysis (protection coverage vs. actual data)

**Chain-Next:** "What label and retention policies should we apply to reduce exposure?"

**Limitations:**
- Scanning may miss unstructured data or cloud storage
- Classification accuracy depends on user application of labels
- Access patterns may be incomplete
- Real-time updates may lag behind actual data changes

**Tips:**
- Focus on highest-value data types first (customer PII, financial data)
- Look for data silos with poor visibility
- Assess whether users understand classification requirements
- Consider role-based access requirements

---

### 1.3 Oversharing Risk Assessment [VALIDATED]

**Title:** Identify Files and Folders with Excessive External or Broad Internal Sharing

**Purpose:** Find data that is shared more widely than business need, increasing exfiltration risk.

**When to Use:** During access control review; identifying principle of least privilege violations.

**SC System Capability Used:** Get Data Risk Summary

**Exact Prompt:**
```
Identify oversharing risks in our organization:
1. How many sensitive files are shared with "Everyone" or broad groups (>100 people)?
2. What sensitive data is shared externally beyond approved business partners?
3. Which users have shared sensitive data with external domains not in our approved partner list?
4. What percentage of shared files have retention policies allowing indefinite access?
5. Provide a prioritized list of oversharing incidents to remediate.
```

**Expected Output:**
- Count of excessively shared files
- Share scope (Everyone / > 100 people / external domains)
- Data sensitivity of overshared files
- External recipients and domains
- Users performing excessive sharing
- Remediation recommendations (retract sharing, limit scope, add retention)
- Risk impact assessment

**Chain-Next:** "Which overshared files should be remediated first based on sensitivity and exposure?"

**Limitations:**
- May flag legitimate broad-sharing scenarios
- Cannot determine business justification automatically
- User intent unclear from share settings alone
- Remediation requires user communication

**Tips:**
- Prioritize sensitive data (PII, financial) shared externally
- Verify business partner legitimacy before broad sharing
- Look for patterns (departments that overshare)
- Consider requiring approval for sharing sensitive data

---

### 1.4 Label Coverage and Retention Policy Effectiveness [VALIDATED]

**Title:** Assess Application and Effectiveness of Data Labels and Retention Policies

**Purpose:** Understand whether data classification and retention policies are being applied consistently.

**When to Use:** During policy effectiveness review or compliance audit preparation.

**SC System Capability Used:** Get Data Risk Summary

**Exact Prompt:**
```
Evaluate our data labeling and retention policy effectiveness:
1. What percentage of sensitive files have appropriate labels applied?
2. Which departments have poorest label coverage (>50% unlabeled sensitive files)?
3. Are retention policies being applied to sensitive data appropriately?
4. What percentage of data with retention policies exceeds recommended retention windows?
5. Which sites or teams have critical gaps in policy application?
```

**Expected Output:**
- Overall label coverage percentage
- Coverage by sensitivity level
- Departmental/team breakdown
- Retention policy application rate
- Files exceeding retention windows
- Gap analysis by department
- Policy effectiveness assessment
- Recommendations for improvement

**Chain-Next:** "What user training or policy changes would improve label coverage?"

**Limitations:**
- Label application depends on user behavior
- Automated labeling may not be enabled
- Retention policy effectiveness requires manual review
- Policy violations may have legitimate business reasons

**Tips:**
- Implement auto-labeling for sensitive data types
- Target training for departments with poor coverage
- Use retention policies to enforce data deletion
- Regular spot-checks to validate label accuracy

---

## 2. DSPM FOR AI AND COPILOT SECURITY

Managing data access risks from AI systems and Copilot interactions.

### 2.1 AI-Accessible Sensitive Data Assessment [ASPIRATIONAL]

**Title:** Identify Sensitive Data that AI Systems and Copilots Can Access

**Purpose:** Understand what sensitive data is exposed to AI systems through Copilot interactions and APIs.

**When to Use:** When implementing Microsoft Copilot or other AI agents; assessing AI security posture.

**SC System Capability Used:** Get Data Risk Summary

**Exact Prompt:**
```
Analyze data accessibility from AI and Copilot systems:
1. What sensitive data types (PII, financial data, trade secrets) are accessible to Microsoft Copilot in your environment?
2. Which users can access sensitive data through Copilot (permissions are inherited)?
3. What safeguards prevent Copilot from exfiltrating sensitive data to external systems?
4. Are there audit logs for sensitive data accessed through Copilot?
5. What data governance policies apply to Copilot interactions?
```

**Expected Output:**
- Sensitive data types Copilot can access
- User populations with Copilot access
- Data permissions inherited by Copilot
- Current safeguards (DLP, retention, encryption)
- Audit capability assessment
- Risk exposure summary
- Recommended controls

**Chain-Next:** "What DLP policies should apply specifically to Copilot interactions?"

**Limitations:**
- Copilot permissions tied to user permissions (difficult to restrict separately)
- Audit logging may not capture all Copilot interactions
- AI training data governance is complex and evolving
- Third-party AI systems have different data handling

**Confirmed Working Status**: Aspirational for current Security Copilot 2026 release. Copilot-specific data governance is emerging capability.

**Tips:**
- Use DLP to prevent sensitive data in Copilot prompts
- Restrict Copilot access for users handling PII/financial data
- Implement content disabling in Copilot for sensitive file types
- Monitor Copilot interaction logs for data exposure

---

### 2.2 Copilot Interaction Risk and Prompt Injection Prevention [ASPIRATIONAL]

**Title:** Assess Risks of Sensitive Data Exposure Through Copilot Prompts

**Purpose:** Understand how Copilot interactions expose data and implement controls to prevent unintended data sharing.

**When to Use:** During Copilot security assessment or after implementing Copilot in your organization.

**SC System Capability Used:** Get Data Risk Summary

**Exact Prompt:**
```
Evaluate Copilot interaction risks:
1. What DLP policies apply to Copilot prompts and responses?
2. Are there controls preventing users from pasting sensitive data into Copilot prompts?
3. How are Copilot interactions logged and can sensitive data in interactions be detected?
4. What data protection applies when Copilot references files or emails containing sensitive data?
5. Are there safeguards against prompt injection attacks using sensitive data?
```

**Expected Output:**
- DLP coverage of Copilot interactions
- Prompt content restrictions
- Logging and detection capabilities
- Data retention in Copilot logs
- Prompt injection risks
- Control recommendations
- Risk mitigation strategies

**Chain-Next:** "What user training is needed to prevent accidental sensitive data sharing through Copilot?"

**Limitations:**
- DLP application to Copilot still evolving
- Logs may not capture all interaction details
- User behavior difficult to control
- Prompt injection prevention still researched

**Confirmed Working Status**: Partially implemented. DLP for Copilot is new, logging not yet comprehensive.

**Tips:**
- Implement DLP policies that flag sensitive data in prompts
- Disable Copilot access for users handling highly sensitive data
- Train users on Copilot security risks
- Monitor logs for sensitive data exposure patterns

---

## 3. REMEDIATION & POLICY OPTIMIZATION

Addressing identified risks and implementing controls.

### 3.1 Prioritized Remediation Action Plan [PARTIALLY-VALIDATED]

**Title:** Create Prioritized List of Data Security Remediation Actions

**Purpose:** Translate findings into actionable remediation plan with clear priorities based on risk and effort.

**When to Use:** After posture assessment identifies gaps; creating remediation roadmap for 90-day execution.

**SC System Capability Used:** Get Data Risk Summary

**Exact Prompt:**
```
Based on our data security posture assessment, recommend prioritized remediation actions:
1. What are the top 5 highest-impact remediation actions we should take immediately?
2. For each action, estimate the implementation effort (high/medium/low) and expected risk reduction.
3. Which actions require user behavior change vs. technical implementation?
4. What policy changes should accompany technical controls?
5. What is the recommended 90-day execution roadmap?
```

**Expected Output:**
- Top 5 prioritized actions
- Risk reduction potential for each
- Implementation effort and timeline
- Owner (technical team, policy team, business owner)
- Prerequisites and dependencies
- Expected ROI for each action
- 90-day roadmap with sequencing
- Success metrics

**Chain-Next:** "Which actions should we automate and which require change management?"

**Limitations:**
- Effort estimates may not reflect your organization's capacity
- Prioritization depends on risk appetite and constraints
- Some actions require stakeholder buy-in
- Sequencing may need adjustment based on dependencies

**Tips:**
- Prioritize actions that reduce highest-risk exposure
- Sequence to build momentum (quick wins first)
- Automate technical controls where possible
- Plan change management for policy/process changes

---

### 3.2 DLP and Retention Policy Recommendations

**Title:** Recommend Specific Label, DLP, and Retention Policies to Address Gaps

**Purpose:** Translate data security findings into concrete policy recommendations implementable in Purview.

**When to Use:** During policy design or policy review; creating or updating DLP/retention policies.

**SC System Capability Used:** Get Data Risk Summary

**Exact Prompt:**
```
Based on our data security posture, recommend label, DLP, and retention policies:
1. What data classification labels should we create or modify to cover identified sensitive data types?
2. What DLP policies should be implemented for high-risk data (PII, financial data, trade secrets)?
3. For each policy, recommend whether it should be audit, monitor, or block mode initially?
4. What retention policies should we implement to limit data lifecycle (when should data be deleted)?
5. Which policies should be exceptions vs. strict enforcement based on business need?
```

**Expected Output:**
- Label recommendations (new labels, label hierarchy)
- DLP policy recommendations (policy rules with SIT/keywords)
- Policy action recommendations (block/monitor/audit)
- Retention policy recommendations (retention windows by data type)
- Exception criteria and approval process
- Phased implementation approach
- User communication plan

**Chain-Next:** "How should we enforce these policies without causing disruption?"

**Limitations:**
- Policies require careful design to avoid excessive false positives
- Business exceptions need clear approval criteria
- Policies require ongoing tuning
- Change management critical for adoption

**Tips:**
- Start policies in audit mode to validate accuracy
- Build exceptions for known legitimate business use
- Implement auto-labeling for classified data types
- Plan user training before enforcement

---

### 3.3 Access Control and Permission Remediation

**Title:** Recommend Access Control Changes to Reduce Data Exposure Risk

**Purpose:** Translate oversharing and excessive access findings into specific access control changes.

**When to Use:** During access control review; implementing principle of least privilege.

**SC System Capability Used:** Get Data Risk Summary

**Exact Prompt:**
```
Based on oversharing and access control findings, recommend access control changes:
1. What files or folders should have external sharing disabled immediately?
2. Which internal shares are too broad and should be restricted to role-based access?
3. What is the recommended access scope (user roles with legitimate data need)?
4. How should we handle inherited permissions that grant excessive access?
5. What approval process should govern sharing of sensitive data?
```

**Expected Output:**
- High-priority access remediation actions
- Files/folders to disable external sharing
- Access scope recommendations (by role/department)
- Inherited permission review findings
- Approval process recommendations
- Change communication plan
- Ongoing access review cadence

**Chain-Next:** "How should we automate enforcement of these access controls?"

**Limitations:**
- Access changes may disrupt business processes
- Identifying "excessive" access requires business input
- Inherited permissions difficult to trace
- Change management critical

**Tips:**
- Review access with business owners before making changes
- Implement role-based access where possible
- Set up regular access reviews (quarterly)
- Consider temporary access for projects
- Use retention to automatically delete old shared data

---

## DSPM Remediation Promptbook: Complete Sequential Assessment and Action Plan

This section provides a complete DSPM assessment workflow that CSAs can use as a structured posture review approach.

### Scenario
Organization conducts quarterly data security posture assessment. No major incidents occurred, but new Copilot implementation and recent growth in Teams/OneDrive usage raised governance questions. Goal: Assess current posture, identify top risks, create 90-day remediation plan.

### Step 1: Executive-Level Risk Overview
**Prompt:**
```
Analyze our current data security posture and provide a risk summary covering:
1. Overall data at risk score
2. Top 3-5 critical data risk categories
3. Number of sensitive file types exposed
4. Comparison to prior quarter
5. One key recommendation for leadership focus
```
**Output:** Overall risk: Medium (trending improved from High last quarter). Data at risk: 15% of sensitive files unprotected (down from 22%). Top risks: (1) Oversharing of customer PII via Teams (2) Unclassified financial documents, (3) External collaboration with non-vetted partners, (4) Copilot access to sensitive data, (5) Excessive on-premise data. Trend: Improving due to recent DLP policy deployment. Key priority: Reduce unprotected customer PII.

---

### Step 2: Detailed Sensitive Data Inventory
**Prompt:**
```
Provide detailed analysis of sensitive data exposure:
1. What types of sensitive information are present and in what volume?
2. What percentage of each type is classified?
3. Which locations have highest concentration of unprotected sensitive data?
4. What is the exposure risk for unclassified sensitive data?
5. What percentage of users have regular access without DLP oversight?
```
**Output:** Data types: Customer PII (250,000 records, 40% classified), Financial data (12,000 records, 60% classified), Trade secrets (3,500 files, 80% classified). High-risk locations: OneDrive (500,000 unclassified files), Teams channels (200,000 unclassified items), legacy file shares (1.2M files, minimal labeling). User access: 5,000 users with access to sensitive data, 60% use Copilot, DLP coverage: 85%.

---

### Step 3: Oversharing Risk Identification
**Prompt:**
```
Identify oversharing risks:
1. How many sensitive files are shared with "Everyone" or broad groups?
2. What sensitive data is shared externally beyond approved partners?
3. Which users are sharing sensitive data with non-approved external domains?
4. What percentage of shared files have indefinite access?
5. Provide prioritized list of oversharing incidents.
```
**Output:** Oversharing findings: (1) 150 sensitive files shared with "Everyone" (customer PII in Teams channels), (2) 45 external shares to 8 non-approved domains (likely shadow IT partners), (3) Top sharers: 10 users account for 60% of sensitive data shares, (4) 30% of shared sensitive files lack retention limits, (5) Remediation priority: Customer PII in Teams (immediate action), external partner shares (next 30 days).

---

### Step 4: Label Coverage and Policy Effectiveness
**Prompt:**
```
Evaluate labeling and retention policy effectiveness:
1. What percentage of sensitive files have appropriate labels?
2. Which departments have poorest label coverage?
3. Are retention policies being applied appropriately?
4. What percentage of data exceeds recommended retention?
5. Which sites have critical policy gaps?
```
**Output:** Label coverage: 48% of sensitive data labeled (below 70% target). Poorest coverage: Finance (25% labeled), HR (35% labeled), Sales (40% labeled). Retention policies: Applied to 55% of labeled data, but 20% of data exceeds recommended retention (should delete after 3 years, average 5.2 years on-site). Critical gaps: Legacy file shares (2% labeled/retention), OneDrive (10% coverage).

---

### Step 5: Copilot and AI Data Security
**Prompt:**
```
Analyze data accessibility from Copilot:
1. What sensitive data can Copilot access through user permissions?
2. Which users with Copilot access handle sensitive data?
3. What safeguards prevent Copilot exfiltration?
4. Are there audit logs for sensitive data accessed through Copilot?
5. What policies apply to Copilot interactions?
```
**Output:** Copilot access: 3,000 users enabled for Copilot, 1,500 have access to sensitive data (through inherited permissions). Sensitive data accessible: Customer PII files, financial documents, trade secret repositories. Safeguards: DLP policies partially cover Copilot (50% of policies), audit logging incomplete. Risk: High - sensitive data accessible to Copilot without full safeguards. Recommendation: Implement Copilot-specific DLP policies and restrict access for sensitive data handlers.

---

### Step 6: Compliance Gap Analysis
**Prompt:**
```
Assess compliance gaps against regulatory requirements:
1. What data protection requirements apply to our organization (GDPR, CCPA, HIPAA, SOC2)?
2. Are we meeting data classification requirements?
3. Are retention policies aligned with legal holds and compliance?
4. What gaps exist in access controls for regulated data?
5. What is our remediation priority for compliance gaps?
```
**Output:** Compliance requirements: GDPR (customer PII), CCPA (California customer data), SOC2 (vendor data handling). Gaps: (1) GDPR - only 40% of customer PII data labeled/protected, (2) CCPA - 15% of California customer records lack encryption and access controls, (3) SOC2 - vendor data shares not documented/approved, (4) Retention - 20% of data exceeds retention windows, (5) Access - 5,000 users with broad access to regulated data. Remediation priority: GDPR and CCPA customer data (0-30 days), SOC2 vendor controls (30-60 days).

---

### Step 7: 90-Day Remediation Roadmap
**Prompt:**
```
Based on findings, create 90-day remediation action plan:
1. What are top 5 highest-impact actions to take immediately?
2. For each, estimate effort (high/medium/low) and risk reduction?
3. Which actions require user behavior change vs. technical implementation?
4. What policy changes should accompany technical controls?
5. What is recommended 90-day execution roadmap?
```
**Output:** Top 5 Actions (90-day roadmap):
1. **Immediate (0-30 days)**: Disable "Everyone" sharing on 150 sensitive files, retract external shares to non-approved domains. Effort: Low (1-2 days). Risk reduction: High (eliminates 80% of high-risk exposure). Owner: SharePoint admin.

2. **Days 15-30**: Implement DLP policies for customer PII (block external sharing of labeled PII). Effort: Medium (1 week design, 1 week testing). Risk reduction: High. Owner: Security/DLP team. Change management: User notification, exceptions approval process.

3. **Days 30-60**: Auto-label sensitive data in Finance and HR (financial data, HR records). Effort: Medium (2 weeks configuration). Risk reduction: High (enables enforcement). Owner: Information governance. Change management: Training on labels.

4. **Days 45-75**: Implement Copilot-specific DLP policies; restrict Copilot access for sensitive data handlers (1,500 users). Effort: High (3 weeks policy design, testing, communication). Risk reduction: Medium. Owner: Security/Copilot team. Change management: Extensive user communication.

5. **Days 60-90**: Conduct access review for regulated data; remediate over-broad permissions. Effort: High (4 weeks with business owner reviews). Risk reduction: High. Owner: Identity/access governance. Change management: Work with business owners on access justification.

Execution sequencing: Actions 1 and 2 parallel (quick wins), Action 3 parallel with 2, Action 4 starts day 45, Action 5 starts day 60. Resource requirement: 2 FTE for 90 days, vendor support for migration/remediation.

---

### Step 8: DLP and Label Policy Recommendations
**Prompt:**
```
Recommend specific label, DLP, and retention policies:
1. What classification labels should we create?
2. What DLP policies should be implemented?
3. For each policy, should it be audit, monitor, or block mode?
4. What retention policies should apply?
5. Which policies should have exceptions?
```
**Output:** Recommended policies:
- **Labels to create**: "Customer PII", "Financial Sensitive", "Trade Secret", "Regulated Data"
- **DLP Policies**: (1) Customer PII Protection (block external sharing, monitor internal sharing to non-business groups), (2) Financial Data (block external, monitor broad internal), (3) Trade Secrets (block all external, audit internal). Initial mode: Audit for all (30 days testing), then Monitor (30 days adjustment), then Block (after 60 days).
- **Retention**: Customer PII (delete after 3 years per GDPR), Financial (7 years per regulatory), Trade Secrets (indefinite with access review annually).
- **Exceptions**: Customer PII (approved external vendors with Business Associate Agreements), Financial (CFO/audit team, temporary for external auditors).

---

### Step 9: Access Control Remediation Plan
**Prompt:**
```
Recommend access control changes to reduce exposure:
1. What files should have external sharing disabled?
2. Which internal shares are too broad?
3. What is recommended access scope by role?
4. How should we handle inherited permissions?
5. What approval process for sensitive data sharing?
```
**Output:** Access remediation:
- **Immediate actions**: Disable external sharing on 150 sensitive files (Teams channels with PII), disable "Everyone" access on Finance shares
- **30-day actions**: Review 5,000 users with broad access to sensitive data; baseline should be 1,500 users max (by role)
- **60-day actions**: Implement role-based access (Financial Analyst role → Finance shared folders only, Customer Service → Customer database only)
- **Ongoing**: Quarterly access review, temporary access for projects (45-day max with auto-expiration)
- **Approval process**: Sensitive data sharing requires manager approval (implemented via DLP incident notification); external sharing requires security team pre-approval.

---

### Step 10: Measurement and Success Criteria
**Prompt:**
```
Define success metrics and measurement approach:
1. What metrics demonstrate remediation progress?
2. How will we measure improvements to data security posture?
3. What targets should we set for each remediation action?
4. How often should we measure and report progress?
5. When should we complete this remediation roadmap?
```
**Output:** Success metrics:
- **Labeling**: Increase from 48% to 70% by end of Q2 (30-60 days), target 85% by end of year
- **DLP enforcement**: Deploy 3 policies with zero false positive rate in audit mode, move to monitor/block modes
- **Access control**: Reduce broad shares by 80% (150 sensitive files → 30), reduce user access to 1,500 (from 5,000)
- **Retention**: Move 100% of labeled data to compliant retention (from 55%)
- **Copilot security**: Restrict Copilot access for 1,500 sensitive data users, implement DLP coverage for 95% of Copilot policies
- **Compliance**: Close all GDPR/CCPA/SOC2 gaps identified
- **Measurement frequency**: Weekly status updates on immediate actions (0-30 days), bi-weekly for 30-60 day actions, monthly for longer timeline
- **Target completion**: 90 days for implementation, 120 days for stabilization and measurement

---

## Tips for Using DSPM Prompts Effectively

1. **Start with Executive Overview**: Always begin with executive risk assessment to align on priorities and business case.

2. **Inventory Drives Decisions**: Understand what data you have before designing policies; policies should match actual data present.

3. **Baseline Metrics**: Establish baseline metrics (% labeled, % protected, % shared) so you can measure progress over time.

4. **Incremental Implementation**: Don't try to implement all policies at once; start with audit mode, move to monitor, then block.

5. **User Communication**: Change management is critical; communicate policies, rationale, and exceptions clearly before enforcement.

6. **Automated Enforcement Where Possible**: Use auto-labeling, auto-retention, and auto-remediation to reduce manual effort.

7. **Regular Assessment Cadence**: Run posture assessment quarterly to track progress and identify emerging risks.

8. **Link to Incidents**: When DLP or IRM incidents occur, reference back to posture assessment to ensure policies address root causes.

---

**Document Version**: 2.0
**Last Updated**: April 2026
**Status**: Validated against Security Copilot with Purview plugin
**Note**: All prompts reflect confirmed Security Copilot capabilities as of April 2026. Some DSPM capabilities marked as "aspirational" are emerging in 2026 release.
