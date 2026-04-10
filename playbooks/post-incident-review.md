# Playbook: Post-Incident Review

## Overview

This playbook guides teams through conducting a comprehensive post-incident review (PIR), also known as a postmortem or after-action review. It provides a structured approach to analyzing incident response effectiveness, identifying root causes, documenting lessons learned, and implementing improvements. Use this playbook when:

- A security incident has been contained and stabilized
- Sufficient time has passed to assess response actions objectively (typically 7-30 days)
- All evidence collection and initial investigation is substantially complete
- Leadership requires lessons learned and recommendations
- Process improvements need to be identified and tracked
- Team feedback on incident response needs to be captured

A comprehensive post-incident review typically requires 20-40 hours over a 2-4 week period across multiple participants.

---

## Prerequisites

- Incident response team and key stakeholders available
- Complete incident documentation and timeline
- All forensic analysis and investigation reports
- Evidence and audit log access
- Legal counsel review (especially for breaches)
- Security Copilot session for analysis support
- Executive sponsorship for improvement implementation
- Process improvement tracking system
- Team meeting facilitation capability

---

## Trigger Conditions

Post-incident review is conducted after:

1. **Major Incident Resolution**: Any incident classified as CRITICAL or HIGH severity
2. **Containment Completion**: Incident fully contained and response phase stabilized
3. **Investigation Completion**: Root cause analysis substantially completed
4. **Evidence Collection**: All relevant evidence and logs collected and preserved
5. **Stakeholder Decision**: Leadership decision to conduct formal review
6. **Time Passage**: Minimum 7 days post-incident (allows perspective and stabilization)
7. **Process Improvement Focus**: Identified need for security improvements

---

## Investigation Flow

### Step 1: Incident Summary Generation

**Objective**: Create a consolidated summary of the incident for PIR participants.

**Actions**:

1. Compile incident facts:
   - Incident ID and classification (severity, type)
   - Detection timestamp and method
   - Containment timeline and completion date
   - Key stakeholders and roles
   - Incident duration (discovery to stabilization)
   - Customer/regulatory notifications sent

2. Gather supporting documentation:
   - Initial incident report
   - Timeline reconstruction with all events
   - Investigation findings and analysis
   - Forensic reports (if applicable)
   - Containment actions taken
   - Notification letters sent
   - Response communication logs
   - Decision logs and approvals

3. Prepare executive summary:
   - Plain-language description of incident
   - Impact assessment summary
   - Response effectiveness assessment
   - Key metrics (detection lag, response time, scope)

4. Ask Security Copilot to generate summary:

**SC Prompt 1**:
```
Generate a consolidated incident summary for post-incident review:

Incident details:
- Incident ID: [INSERT_ID]
- Classification: [INSERT_SEVERITY_AND_TYPE]
- Detection date/time: [INSERT_TIMESTAMP]
- Detection method: [INSERT_METHOD]
- Containment complete date: [INSERT_DATE]
- Investigation complete date: [INSERT_DATE]
- Current status: [INSERT_STATUS]

Impact summary:
- Data affected: [INSERT_DATA_TYPE]
- Individuals impacted: [INSERT_COUNT]
- Geographic scope: [INSERT_SCOPE]
- Regulatory notification required: [INSERT_YES/NO]

Response summary:
- Incident response team: [INSERT_TEAM_COMPOSITION]
- Escalation path: [INSERT_ESCALATIONS]
- Key decisions: [INSERT_KEY_DECISIONS]
- Notifications sent: [INSERT_NOTIFICATIONS_SUMMARY]

Key metrics:
- Detection lag (incident to discovery): [INSERT_DURATION]
- Time to escalation: [INSERT_DURATION]
- Time to containment: [INSERT_DURATION]
- Total incident duration (discovery to resolution): [INSERT_DURATION]

Please generate:
1. Executive summary (3-5 paragraphs)
2. Incident timeline (key events with timestamps)
3. Impact summary
4. Response effectiveness summary
5. Stakeholders and roles
6. Current resolution status
```

**Decision Point**: Is all critical documentation available?
- If gaps exist → Assign documentation compilation as immediate task
- If complete → Proceed to Step 2

---

### Step 2: Timeline Reconstruction and Verification

**Objective**: Establish authoritative timeline with all significant events verified.

**Actions**:

1. Compile chronological events:
   - System/security events (log entries with timestamps)
   - Detection events (alerts, notifications, discovery)
   - Escalation events (decisions made, notifications sent)
   - Response actions (containment, investigation steps)
   - Stabilization milestones
   - Investigation completion

2. Verify timestamps and accuracy:
   - Cross-reference system logs with reported times
   - Identify any discrepancies in accounts of events
   - Establish true sequence when multiple events occurred
   - Note timezone considerations
   - Verify critical decision points and approvals

3. Identify detection lag:
   - Calculate time between incident occurrence and discovery
   - Assess whether earlier detection was possible
   - Identify monitoring/alerting gaps
   - Note false positives or missed signals

4. Assess response timeliness:
   - Time from discovery to escalation
   - Time from escalation to containment start
   - Time to key containment milestones
   - Overall incident duration
   - Comparison to SLA targets

5. Ask Security Copilot to reconstruct timeline:

**SC Prompt 2**:
```
Reconstruct and verify the incident timeline:

Reported events (with timestamps from incident documentation):
- [TIMESTAMP]: [EVENT_DESCRIPTION]
- [TIMESTAMP]: [EVENT_DESCRIPTION]
- [TIMESTAMP]: [EVENT_DESCRIPTION]

System events (from logs):
- [TIMESTAMP]: [LOG_EVENT]
- [TIMESTAMP]: [LOG_EVENT]
- [TIMESTAMP]: [LOG_EVENT]

Response actions and decisions:
- [TIMESTAMP]: [ACTION_DESCRIPTION]
- [TIMESTAMP]: [DECISION_AND_APPROVER]
- [TIMESTAMP]: [ACTION_TAKEN]

Key milestones:
- First indication of incident: [TIMESTAMP]
- Discovery timestamp: [TIMESTAMP]
- Escalation to leadership: [TIMESTAMP]
- Containment actions initiated: [TIMESTAMP]
- Containment completed: [TIMESTAMP]
- Investigation substantially completed: [TIMESTAMP]

Please provide:
1. Verified chronological timeline of all significant events
2. Notes on timestamp accuracy and confidence level
3. Detection lag assessment (time from incident to discovery)
4. Response timeliness assessment (discovery to key milestones)
5. Identification of any timeline gaps or discrepancies
6. Comparison of actual timeline to expected SLA timelines
```

**Decision Point**: Is the timeline accurate and complete?
- If gaps remain → Conduct targeted interviews to fill gaps
- If discrepancies exist → Note in PIR as lessons learned item
- If complete → Proceed to Step 3

---

### Step 3: Detection Effectiveness Assessment

**Objective**: Evaluate whether monitoring and alerting systems functioned as designed and identify detection gaps.

**Actions**:

1. Assess monitoring effectiveness:
   - **Detection mechanism used**: What alerting/monitoring caught the incident?
   - **Early indicators**: Were there earlier signals that could have triggered alerts?
   - **Sensitivity**: Did alert threshold sensitivity balance false positives/negatives appropriately?
   - **Blind spots**: Were there monitoring gaps that delayed detection?

2. Identify what signals existed pre-detection:
   - Activity logs that would have shown the incident (even if not alerted on)
   - Failed blocked attempts by DLP or endpoint controls
   - Anomalous activity that should have triggered alerts
   - Related incidents or similar activity from other users/systems

3. Assess alerting performance:
   - Did alerts fire as configured?
   - Were alerts received and acted upon?
   - Did alert content contain sufficient detail for quick response?
   - Were escalation rules functioning?
   - Were notification systems operational?

4. Identify process/procedural failures:
   - Were alerts reviewed/actioned timely?
   - Did monitoring personnel understand alert significance?
   - Were there communication barriers preventing escalation?
   - Were decision-makers notified appropriately?

5. Ask Security Copilot to assess detection effectiveness:

**SC Prompt 3**:
```
Assess the effectiveness of detection and monitoring systems:

Incident details:
- Incident type: [INSERT_TYPE]
- Detection method: [INSERT_HOW_DETECTED]
- Time of actual incident: [INSERT_TIMESTAMP]
- Detection/discovery time: [INSERT_TIMESTAMP]
- Detection lag: [INSERT_DURATION]

Monitoring systems in place:
- System 1: [INSERT_NAME]
- System 2: [INSERT_NAME]
- System 3: [INSERT_NAME]
- Alert thresholds: [INSERT_THRESHOLDS]

Pre-detection signals:
- Audit logs showing incident: [INSERT_YES/NO_DETAILS]
- Blocked attempts by DLP: [INSERT_YES/NO_DETAILS]
- Endpoint blocking: [INSERT_YES/NO_DETAILS]
- Related alerts not escalated: [INSERT_YES/NO_DETAILS]
- Anomalous activity visible in logs: [INSERT_YES/NO_DETAILS]

Alert/escalation performance:
- Did configured alerts fire: [INSERT_YES/NO]
- Were alerts received: [INSERT_YES/NO]
- Time to alert receipt: [INSERT_DURATION]
- Alert content quality: [INSERT_ASSESSMENT]
- Escalation rules fired correctly: [INSERT_YES/NO]

Personnel/process factors:
- Alert reviewed timely: [INSERT_YES/NO]
- Alert significance understood: [INSERT_YES/NO]
- Communication/escalation delays: [INSERT_YES/NO_DETAILS]
- Decision-maker notification lag: [INSERT_DURATION]

Please assess:
1. Did monitoring and alerting systems work as designed?
2. What pre-detection signals existed that could have enabled earlier discovery?
3. Could detection have been achieved earlier with existing tools?
4. What gaps in monitoring coverage contributed to detection lag?
5. Were process/procedural factors limiting detection effectiveness?
6. What is the earliest realistic detection time with current systems?
7. What improvements would enable earlier detection?
```

**Decision Point**: Were detection gaps system-based or process-based?
- If system gaps → Recommend monitoring control improvements
- If process gaps → Recommend procedural and training improvements
- If both → Address each category with specific improvements
- If detection was optimal → Document as positive finding

---

### Step 4: Response Evaluation

**Objective**: Assess how well incident response actions aligned with procedures and achieved objectives.

**Actions**:

1. Review response procedures and playbooks:
   - Did team follow established response procedures?
   - Were playbooks accurate and followed?
   - Were escalation procedures executed correctly?
   - Did decision authority have appropriate input?

2. Assess response actions:
   - Were containment actions appropriate and effective?
   - Did actions achieve objectives (stop damage, preserve evidence)?
   - Were actions timely and well-coordinated?
   - Were there unintended consequences from response actions?
   - Could response have been more effective?

3. Evaluate team performance:
   - Were needed roles filled?
   - Did team members understand their responsibilities?
   - Was communication effective?
   - Was decision-making timely and sound?
   - Were there personality/authority conflicts?

4. Assess communication:
   - **Internal communication**: Was status communicated appropriately within team and leadership?
   - **External communication**: Was customer/regulatory notification appropriate and timely?
   - **Media/public**: Was public communication handled appropriately (if needed)?
   - **Documentation**: Was activity logged for compliance/audit purposes?

5. Ask Security Copilot to evaluate response:

**SC Prompt 4**:
```
Evaluate the incident response actions and effectiveness:

Response procedures:
- Incident response plan/playbook: [INSERT_YES/NO]
- Playbook followed: [INSERT_YES/NO]
- Escalation procedures executed: [INSERT_YES/NO]
- Decision authority involved: [INSERT_YES/NO]

Containment actions taken:
- Action 1: [INSERT_ACTION_DESCRIPTION]
- Action 2: [INSERT_ACTION_DESCRIPTION]
- Action 3: [INSERT_ACTION_DESCRIPTION]
- Action effectiveness: [INSERT_ASSESSMENT]
- Unintended consequences: [INSERT_YES/NO_DETAILS]

Response team:
- Roles involved: [INSERT_ROLES]
- Roles missing: [INSERT_YES/NO_ROLES]
- Team coordination: [INSERT_ASSESSMENT]
- Decision-making timeliness: [INSERT_ASSESSMENT]
- Conflicts or issues: [INSERT_YES/NO_DETAILS]

Communication:
- Internal escalation: [INSERT_YES/NO_TIMELINE]
- Executive briefing: [INSERT_YES/NO_TIMELINE]
- Customer notification: [INSERT_YES/NO_TIMELINE]
- Regulatory notification: [INSERT_YES/NO_TIMELINE]
- Documentation completeness: [INSERT_ASSESSMENT]

Timing metrics:
- Time to first action: [INSERT_DURATION]
- Time to containment actions: [INSERT_DURATION]
- Time to stabilization: [INSERT_DURATION]
- SLA compliance: [INSERT_YES/NO]

Please assess:
1. Did response actions follow established procedures?
2. Were containment actions appropriate and effective?
3. Did response team have necessary skills and coordination?
4. Was communication effective across all stakeholders?
5. What response actions were most effective?
6. What could have improved response effectiveness?
7. Were there response gaps or delays?
```

**Decision Point**: Was response effective?
- If yes → Document positive findings and practices to continue
- If issues existed → Identify specific improvements needed
- If structural gaps → Recommend process/playbook improvements
- If training gaps → Recommend training or role-based coaching

---

### Step 5: Gap Identification

**Objective**: Systematically identify gaps between expected and actual incident handling.

**Actions**:

1. Compare to security standards and expectations:
   - **Detection gap**: Delay between incident and discovery
   - **Response gap**: Delay in escalation or initial response
   - **Containment gap**: Controls that should have prevented incident
   - **Monitoring gap**: Lack of visibility into critical assets/activities
   - **Procedure gap**: Unclear or missing response procedures
   - **Skill gap**: Team member lack of knowledge or training
   - **Communication gap**: Breakdown in information flow
   - **Authority gap**: Unclear decision-making authority

2. Categorize gaps:
   - **Technical gaps**: Technology/tool limitations or failures
   - **Process gaps**: Procedure or workflow deficiencies
   - **Training gaps**: Knowledge or skill deficiencies
   - **Organizational gaps**: Structure or communication issues
   - **Resource gaps**: Insufficient staffing or tools

3. Assess severity of each gap:
   - **Critical**: Gap directly enabled this incident or prevented early detection
   - **High**: Gap significantly impacted response effectiveness
   - **Medium**: Gap created inefficiency or delayed response
   - **Low**: Gap had minor impact but should be addressed

4. Ask Security Copilot to identify gaps:

**SC Prompt 5**:
```
Identify gaps between expected and actual incident handling:

Incident context:
- Type: [INSERT_TYPE]
- Severity: [INSERT_SEVERITY]
- Detection lag: [INSERT_DURATION]
- Response lag: [INSERT_DURATION]
- Expected detection lag: [INSERT_EXPECTED]
- Expected response lag: [INSERT_EXPECTED]

Established expectations:
- Detection capability: [INSERT_CAPABILITY]
- Response procedures: [INSERT_PROCEDURES_SUMMARY]
- Escalation authority: [INSERT_AUTHORITY_STRUCTURE]
- Team composition: [INSERT_TEAM_ROLES]
- Tools/systems available: [INSERT_TOOLS]

Actual findings:
- Detection method used: [INSERT_METHOD]
- Response procedures followed: [INSERT_YES/NO_DETAILS]
- Escalation effectiveness: [INSERT_ASSESSMENT]
- Team performance: [INSERT_ASSESSMENT]
- Tool/system limitations: [INSERT_YES/NO_DETAILS]

Detection gaps:
- [INSERT_GAP_DESCRIPTION]
- [INSERT_GAP_DESCRIPTION]

Response gaps:
- [INSERT_GAP_DESCRIPTION]
- [INSERT_GAP_DESCRIPTION]

Please identify:
1. Specific gaps between expected and actual performance
2. Category of each gap (technical, process, training, organizational)
3. Severity of each gap (critical, high, medium, low)
4. How each gap impacted this incident
5. Which gaps are most important to address
6. Gaps that indicate systemic vs. one-time issues
```

**Decision Point**: What are the priority gaps to address?
- If critical gaps → Must remediate before declaring process adequate
- If multiple medium gaps → Address through improvement program
- If few gaps → May indicate good baseline for incident handling
- Rank by impact and feasibility to address

---

### Step 6: Lessons Learned Extraction

**Objective**: Document key lessons from incident to inform future prevention and response.

**Actions**:

1. Identify what worked well:
   - Effective detection mechanisms
   - Good response coordination
   - Clear communication
   - Appropriate decision-making
   - Tool functionality
   - Team expertise and performance

2. Identify what did not work well:
   - Detection delays or failures
   - Response coordination issues
   - Communication breakdowns
   - Unclear authority or decision-making
   - Tool limitations
   - Knowledge or skill gaps

3. Identify systemic findings:
   - Patterns that contributed to incident
   - Root causes (not just surface causes)
   - Environmental factors (high load, staffing shortages)
   - Process or control weaknesses
   - Risk factors that enabled incident

4. Categorize lessons learned:
   - **Prevention**: How to prevent similar incidents
   - **Detection**: How to detect incidents earlier
   - **Response**: How to respond more effectively
   - **Recovery**: How to recover faster from incidents
   - **Resilience**: How to build more resilient systems

5. Ask Security Copilot to extract lessons:

**SC Prompt 6**:
```
Extract lessons learned from this incident:

Incident summary:
- Type: [INSERT_TYPE]
- Severity: [INSERT_SEVERITY]
- Root cause: [INSERT_ROOT_CAUSE]
- Impact: [INSERT_IMPACT_SUMMARY]

Response effectiveness:
- Detection effectiveness: [INSERT_ASSESSMENT]
- Response effectiveness: [INSERT_ASSESSMENT]
- Communication effectiveness: [INSERT_ASSESSMENT]
- Team performance: [INSERT_ASSESSMENT]

What worked well:
- [INSERT_SUCCESS_1]
- [INSERT_SUCCESS_2]
- [INSERT_SUCCESS_3]

What could be improved:
- [INSERT_GAP_1]
- [INSERT_GAP_2]
- [INSERT_GAP_3]

Systemic findings:
- Pattern that enabled incident: [INSERT_PATTERN]
- Environmental factor: [INSERT_FACTOR]
- Control weakness: [INSERT_WEAKNESS]
- Process deficiency: [INSERT_DEFICIENCY]

Prevention considerations:
- Could this incident have been prevented? [INSERT_YES/NO_HOW]
- What would have prevented it? [INSERT_PREVENTION_MEASURE]
- Feasibility of prevention: [INSERT_ASSESSMENT]

Detection improvements:
- Could detection have been faster? [INSERT_YES/NO_HOW]
- What would have enabled faster detection? [INSERT_IMPROVEMENT]
- Cost/benefit of detection improvement: [INSERT_ASSESSMENT]

Please extract:
1. Five key lessons learned (both positive and negative)
2. Root causes contributing to this incident
3. Preventive measures that would have stopped this incident
4. Detection improvements for earlier discovery
5. Response improvements for faster resolution
6. Systemic improvements for overall resilience
7. Lessons applicable to similar scenarios
```

**Decision Point**: What are the most important lessons?
- If prevention measure is feasible → Prioritize for immediate action
- If detection improvement possible → Implement with reasonable effort
- If systemic issue identified → Escalate for architectural change
- Document all lessons for organizational learning

---

### Step 7: Remediation Recommendations

**Objective**: Develop specific, actionable recommendations to prevent or better handle similar incidents.

**Actions**:

1. Develop prevention recommendations:
   - Technical controls (DLP policies, access controls, encryption)
   - Process improvements (procedures, workflow changes)
   - Architectural changes (system redesign, tool deployment)
   - Risk management (risk assessment, asset management)

2. Develop detection recommendations:
   - Monitoring/alerting rules
   - Baseline and anomaly detection
   - Log aggregation and analysis
   - Alert tuning and prioritization
   - Training for alert interpretation

3. Develop response recommendations:
   - Procedure/playbook improvements
   - Tool improvements or additions
   - Training and readiness programs
   - Communication improvements
   - Decision-making authority clarification

4. Develop resilience recommendations:
   - Backup and recovery improvements
   - Failover mechanisms
   - Business continuity planning
   - Third-party coordination

5. Rank recommendations by:
   - **Impact**: How much would this reduce future risk?
   - **Effort**: How much effort/cost is required?
   - **Timeline**: How quickly can this be implemented?
   - **Dependencies**: What must be done first?

6. Ask Security Copilot to recommend improvements:

**SC Prompt 7**:
```
Develop remediation recommendations for this incident:

Incident insights:
- Root cause: [INSERT_ROOT_CAUSE]
- Detection lag: [INSERT_DETECTION_LAG]
- Response gaps: [INSERT_GAPS]
- Control failures: [INSERT_CONTROL_FAILURES]

Prevention considerations:
- Could this be prevented? [INSERT_YES/NO]
- Prevention measures that would work: [INSERT_MEASURES]
- Cost/feasibility: [INSERT_ASSESSMENT]

Detection considerations:
- Earlier detection possible? [INSERT_YES/NO]
- Detection improvements available: [INSERT_IMPROVEMENTS]
- Tools/rules needed: [INSERT_TOOLS_RULES]

Response considerations:
- Response improvements available: [INSERT_IMPROVEMENTS]
- Procedure/playbook changes: [INSERT_CHANGES]
- Training needs: [INSERT_TRAINING]

Organizational context:
- Current security posture: [INSERT_MATURITY]
- Resource constraints: [INSERT_CONSTRAINTS]
- Strategic priorities: [INSERT_PRIORITIES]
- Other competing initiatives: [INSERT_INITIATIVES]

Please provide:
1. Top 5 remediation recommendations ranked by priority
2. Specific actions for each recommendation
3. Estimated effort and resources required
4. Implementation timeline (quick wins vs. long-term)
5. Success criteria for each recommendation
6. Dependencies or prerequisite work
7. Expected risk reduction from each recommendation
8. Business case for executive approval (if needed)
```

**Decision Point**: Which recommendations merit immediate implementation?
- If critical control gap → Urgent implementation required
- If quick win → Implement in next 30-60 days
- If high-effort improvement → Plan for next planning cycle
- If strategic initiative → Align with organizational roadmap

---

### Step 8: Report Generation for Leadership

**Objective**: Create comprehensive post-incident review report for distribution to leadership and stakeholders.

**Actions**:

1. Develop report structure:
   - Executive summary
   - Incident timeline and facts
   - Detection effectiveness assessment
   - Response effectiveness assessment
   - Gaps identified
   - Lessons learned
   - Remediation recommendations
   - Implementation plan and owners
   - Appendices (detailed evidence, logs, etc.)

2. Tailor for different audiences:
   - **Executive/Board level**: High-level summary, risk/impact focus
   - **Security team**: Detailed technical findings and recommendations
   - **Operations**: Operational impacts and resilience improvements
   - **Risk/Compliance**: Regulatory and risk implications

3. Include metrics and data:
   - Detection lag (in hours/days)
   - Response time metrics
   - Impact quantification
   - Cost of incident vs. prevention cost
   - Timeline comparison to SLAs

4. Ask Security Copilot to draft report:

**SC Prompt 8**:
```
Generate comprehensive post-incident review report:

Incident overview:
- ID: [INSERT_ID]
- Type: [INSERT_TYPE]
- Severity: [INSERT_SEVERITY]
- Detection date: [INSERT_DATE]
- Containment date: [INSERT_DATE]
- Status: [INSERT_STATUS]

Key findings:
- Root cause: [INSERT_ROOT_CAUSE]
- Detection lag: [INSERT_DURATION]
- Response effectiveness: [INSERT_ASSESSMENT]
- Primary gaps: [INSERT_GAPS]
- Key lessons: [INSERT_LESSONS]

Impact assessment:
- Data/individuals affected: [INSERT_COUNT]
- Business impact: [INSERT_IMPACT]
- Regulatory impact: [INSERT_IMPACT]

Response summary:
- Team involved: [INSERT_TEAM]
- Key decisions: [INSERT_DECISIONS]
- Actions taken: [INSERT_ACTIONS]
- Effectiveness: [INSERT_ASSESSMENT]

Recommendations (top 5):
1. [INSERT_RECOMMENDATION]
2. [INSERT_RECOMMENDATION]
3. [INSERT_RECOMMENDATION]
4. [INSERT_RECOMMENDATION]
5. [INSERT_RECOMMENDATION]

Implementation plan status:
- Owner: [INSERT_OWNER]
- Timeline: [INSERT_TIMELINE]
- Resources required: [INSERT_RESOURCES]

Please draft:
1. Executive summary (1 page)
2. Incident facts and timeline
3. Detection and response effectiveness assessment
4. Root cause analysis
5. Gaps identified
6. Lessons learned summary
7. Top remediation recommendations with business case
8. Implementation roadmap and owners
9. Success metrics for improvements
10. Appendices with detailed evidence
```

5. Incorporate stakeholder feedback:
   - Review draft with incident responders
   - Incorporate different perspectives
   - Ensure technical accuracy
   - Validate recommendations are actionable

6. Obtain approvals:
   - CISO/Security leadership approval
   - Executive sponsor approval
   - Legal review (if breach-related)
   - Compliance review (if regulatory-related)

---

## Post-Incident Review Meeting Structure

**Meeting 1: Kickoff and Context Setting** (1-2 hours)
- Participants: Incident response team leads, CISO, executive sponsor
- Agenda:
  - Incident overview and scope
  - PIR objectives and scope
  - Timeline and process
  - Confidentiality/privilege discussion (if applicable)
  - Roles and responsibilities
  - Questions and clarification

**Meeting 2: Technical Walkthrough** (2-3 hours)
- Participants: Technical investigators, responders, engineering
- Agenda:
  - Detailed incident timeline
  - Technical findings and root cause
  - Detection and monitoring review
  - System performance analysis
  - Evidence presentation

**Meeting 3: Response and Process Review** (2-3 hours)
- Participants: Incident command, responders, operations
- Agenda:
  - Response procedures and execution
  - Communication and escalation
  - Decision-making analysis
  - Team coordination and roles
  - Procedures/playbook effectiveness

**Meeting 4: Lessons Learned and Recommendations** (2-3 hours)
- Participants: All team, CISO, executive sponsor, subject matter experts
- Agenda:
  - Facilitated discussion of lessons learned
  - Gap analysis and prioritization
  - Remediation recommendations
  - Business case and resource planning
  - Accountability and implementation owners

**Meeting 5: Report Finalization and Approval** (1 hour)
- Participants: CISO, executive sponsor, legal (if needed)
- Agenda:
  - Report review and final comments
  - Recommendations prioritization
  - Approval and sign-off
  - Communication/distribution plan
  - Follow-up tracking process

---

## Output Artifacts

Post-incident review must produce the following artifacts:

1. **Final Incident Report** (for executive and compliance use):
   - Executive summary
   - Incident timeline and facts
   - Root cause analysis
   - Detection and response assessment
   - Gaps identified
   - Lessons learned
   - Recommendations with business case

2. **Detailed Technical Report** (for security team and auditors):
   - Complete timeline with timestamps and sources
   - Forensic analysis findings
   - System performance data
   - Evidence and logs
   - Technical assessment of controls
   - Root cause technical analysis

3. **Remediation Tracking Matrix** (for ongoing accountability):
   - Each recommendation with:
     - Specific action items
     - Owner and due date
     - Success criteria
     - Implementation status
     - Regular update schedule

4. **Lessons Learned Repository Entry**:
   - Incident case study for organizational learning
   - Key findings and insights
   - Scenario applicability
   - Recommendations and expected outcomes
   - Link to similar past incidents

5. **Metrics and Trending Report** (for security metrics):
   - Detection lag trend
   - Response time metrics
   - Incident frequency trend
   - Control effectiveness metrics
   - Improvement tracking

6. **Communication Materials**:
   - Executive briefing slides
   - All-hands communication (if appropriate)
   - Team-specific communication
   - Customer/stakeholder communication (if needed)

---

## Prompt Chain: Complete SC Interaction Sequence

Execute these SC prompts in order to conduct comprehensive post-incident review:

1. **SC Prompt 1** → Incident summary generation
2. **SC Prompt 2** → Timeline reconstruction and verification
3. **SC Prompt 3** → Detection effectiveness assessment
4. **SC Prompt 4** → Response evaluation
5. **SC Prompt 5** → Gap identification
6. **SC Prompt 6** → Lessons learned extraction
7. **SC Prompt 7** → Remediation recommendations
8. **SC Prompt 8** → Report generation

**Note**: Execute prompts sequentially. Each step builds on findings from previous steps. Iteratively refine findings as new information emerges during team discussion.

---

## Common Pitfalls

**1. Conducting PIR too early or too late**
- Too early: Investigation incomplete, team still stressed, decisions unstable
- Too late: Details forgotten, momentum lost, improvements delayed
- Mitigation: Schedule PIR 7-14 days post-incident for optimal timing

**2. Blaming individuals instead of analyzing systems**
- Focusing on "who made the mistake" rather than "what system failure enabled the mistake"
- Creating defensive atmosphere where team hides information
- Mitigation: Focus on process and system improvements, not individual blame

**3. Creating recommendations without business case**
- Recommending solutions without considering cost, effort, or feasibility
- Creating long lists of recommendations that never get implemented
- Mitigation: Rank recommendations by impact, effort, and feasibility

**4. Inadequate executive sponsor engagement**
- Executive sponsors view PIR as "security exercise" rather than business priority
- Recommendations don't get resources for implementation
- Mitigation: Engage executive sponsor early, frame in business/risk terms

**5. Poor timeline reconstruction**
- Inaccurate timestamps lead to incorrect conclusions about root cause
- Discrepancies between reported and actual timeline
- Mitigation: Use multiple evidence sources and verify all timestamps

**6. Insufficient gap analysis**
- Identifying surface-level gaps without understanding systemic issues
- Missing opportunities for comprehensive improvement
- Mitigation: Ask "why?" multiple times to get to root cause

**7. Not tracking remediation to completion**
- Recommendations don't get implemented
- Same issues reoccur in subsequent incidents
- Mitigation: Create formal tracking with owners and executive oversight

**8. Lack of organizational learning**
- Each incident treated as isolated rather than adding to organizational knowledge
- Similar incidents continue to occur
- Mitigation: Create lessons learned repository and reference in future incidents

---

## Quality Checklist

Before completing post-incident review, verify:

- [ ] All 8 SC prompts executed and analyzed
- [ ] Incident summary comprehensive and accurate
- [ ] Timeline reconstruction verified against multiple sources
- [ ] Detection effectiveness thoroughly assessed
- [ ] Response effectiveness thoroughly evaluated
- [ ] All significant gaps identified and categorized
- [ ] Lessons learned extracted and documented
- [ ] Remediation recommendations specific and actionable
- [ ] Business case developed for major recommendations
- [ ] Implementation ownership assigned
- [ ] Success metrics defined for each recommendation
- [ ] Final report drafted and reviewed for accuracy
- [ ] Executive sponsor engaged and supportive
- [ ] Legal review completed (if applicable)
- [ ] Team feedback incorporated
- [ ] Remediation tracking mechanism established
- [ ] Communication plan developed
- [ ] Lessons learned entered in organizational repository
- [ ] Follow-up review meeting scheduled (30-60 days)
- [ ] Audit trail complete for compliance

**Post-incident review is complete when all checkboxes verified and report approved by executive sponsor.**

