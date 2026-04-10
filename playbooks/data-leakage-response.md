# Playbook: Data Leakage Response

## Overview

This playbook provides a structured approach to responding to confirmed or suspected data leakage incidents in Microsoft Purview environments. It guides incident responders through initial detection assessment, impact quantification, containment planning, and stakeholder communication. Use this playbook when:

- A data breach is confirmed or strongly suspected
- Sensitive data is found in unauthorized locations
- External parties report possession of confidential information
- Monitoring detects large-scale suspicious data movement
- Customer or regulatory notification requirements are triggered
- Post-breach forensics and remediation are required

Data leakage response requires coordinated effort across security, legal, and business teams. Initial response phase typically requires 4-8 hours, with broader response continuing over days/weeks.

---

## Prerequisites

- Microsoft Purview DLP, IRM, and eDiscovery roles
- Access to exchange, SharePoint, Teams, and OneDrive audit logs
- Incident response team and escalation authority
- Legal and compliance counsel availability
- Communications and public relations support
- HR liaison for employee-related incidents
- Customer notification authority
- Forensics and evidence preservation capabilities
- Regulatory breach notification requirements documented

---

## Trigger Conditions

Data leakage response is initiated by:

1. **Confirmed Breach**: Third-party or customer notification of data in unauthorized possession
2. **Suspicious Detection**: Large-scale data movement to external locations detected
3. **Credential Compromise**: Unauthorized access with data exfiltration indicators
4. **Insider Incident**: Terminating employee or reported misconduct with data theft indicators
5. **Physical Loss**: Laptop, phone, or storage device with unencrypted data reported missing
6. **Supply Chain Incident**: Third-party vendor or partner breach affecting organization's data
7. **Public Disclosure**: Data posted to public website or social media

---

## Investigation Flow

### Step 1: Initial Detection and Scope Assessment

**Objective**: Establish facts of the incident and determine initial scope of leakage.

**Actions**:

1. Document the detection:
   - How incident was discovered (internal monitoring, external notification, customer report, etc.)
   - Date and time of discovery
   - Who reported/discovered incident
   - Initial indication of what data may be involved
   - Initial assessment of leak severity

2. Gather available evidence:
   - Screenshots or copies of evidence (public postings, breach notifications)
   - Third-party notification details and sources
   - Customer complaint or inquiry details
   - Internal alert or monitoring details triggering response
   - Any communications related to incident

3. Perform immediate scope assessment:
   - Affected data type (customer data, employee data, financial, technical, etc.)
   - Estimated volume of records involved
   - Estimated number of individuals impacted
   - Regulatory frameworks potentially applicable (GDPR, HIPAA, PCI-DSS, CCPA, etc.)
   - Geographic scope of potential exposure

4. Ask Security Copilot for incident assessment:

**SC Prompt 1**:
```
Perform initial assessment of this suspected data leakage incident:

Detection details:
- Detection method: [INSERT_METHOD]
- Date/time discovered: [INSERT_TIMESTAMP]
- Reported by: [INSERT_REPORTER]
- Evidence source: [INSERT_SOURCE]

Initial observations:
- Data type involved: [INSERT_TYPE]
- Estimated volume: [INSERT_VOLUME]
- Estimated individuals impacted: [INSERT_COUNT]
- Exposure location: [INSERT_LOCATION]

Please provide:
1. Plain-language summary of the incident
2. Initial breach severity assessment: low/medium/high/critical
3. Regulatory frameworks that may be triggered
4. Critical information needed in next 4 hours
5. Recommended immediate containment actions (at overview level)
6. Stakeholder notification requirements
```

**Decision Point**: Is this a confirmed breach or unconfirmed suspicion?
- If confirmed → Escalate immediately, activate incident response team
- If strong indicators → Escalate, initiate forensics to confirm
- If weak indicators → Investigate further before broad escalation

---

### Step 2: Data Classification of Leaked Content

**Objective**: Determine what specific data classifications were leaked and their sensitivity.

**Actions**:

1. Identify leaked data content:
   - **For confirmed breaches**: Analyze actual leaked data
   - **For suspected breaches**: Estimate likely content based on access patterns
   - Data types included (financial records, health information, personal identifiers, credentials, etc.)
   - Records by classification level (public, internal, confidential, restricted)
   - Geographic origin of data (US, EU, APAC, etc.)

2. Classify by sensitivity:
   - **Highly Sensitive**: PII (SSN, DOB), PHI, payment card data, credentials, trade secrets
   - **Sensitive**: Customer records, employee information (non-PII), internal financial data
   - **Standard**: General business communications, product documentation
   - **Low**: Public or already-disclosed information

3. Identify regulatory data:
   - GDPR personal data (any EU resident identifiers)
   - HIPAA protected health information (if healthcare organization)
   - PCI-DSS cardholder data (credit card information)
   - CCPA personal information (California residents)
   - Industry-specific (SOX for financial companies, etc.)

4. Ask Security Copilot to assess data sensitivity:

**SC Prompt 2**:
```
Classify the leaked data in this breach incident:

Leaked data details:
- Data type: [INSERT_TYPE]
- Sample content categories: [INSERT_CATEGORIES]
- Estimated records: [INSERT_COUNT]
- Data format: [INSERT_FORMAT]
- Time period covered: [INSERT_DATES]

Data classifications present:
- Highly sensitive data: [INSERT_YES/NO_TYPES]
- Sensitive data: [INSERT_YES/NO_TYPES]
- Standard data: [INSERT_YES/NO_TYPES]
- Public/already-disclosed: [INSERT_YES/NO]

Regulatory applicability:
- GDPR personal data involved: [INSERT_YES/NO_COUNT]
- HIPAA health information: [INSERT_YES/NO_COUNT]
- PCI-DSS cardholder data: [INSERT_YES/NO_COUNT]
- CCPA personal information: [INSERT_YES/NO_COUNT]
- Industry-specific regulations: [INSERT_YES/NO_TYPES]

Please analyze:
1. What is the actual sensitivity and criticality of leaked data?
2. Which regulatory breach notification requirements apply?
3. What is the business and reputational impact?
4. Are there particular individuals or entities at risk?
5. What notification obligations are triggered?
6. Timeline for compliance with notification requirements
```

**Decision Point**: What regulatory frameworks apply?
- If any regulatory data → Notify legal/compliance immediately
- If GDPR data → 72-hour notification clock starts
- If HIPAA → Follow breach notification rules (60 days)
- If PCI-DSS → Breach notification rules apply
- Document applicable deadlines

---

### Step 3: Leakage Vector Identification

**Objective**: Determine how data left the organization (email, USB, cloud upload, print, etc.).

**Actions**:

1. Analyze data movement patterns:
   - **Email exfiltration**: Identify emails sent with attachments, recipients, external domains
   - **Cloud upload**: Identify cloud services data was uploaded to (OneDrive, personal Google Drive, Dropbox, AWS, etc.)
   - **USB/removable media**: Identify if data was copied to removable storage
   - **Printing**: Identify if data was printed and physically removed
   - **Screenshots/photos**: If digital data captured and re-shared
   - **Database export**: If data exported from operational systems
   - **API/bulk download**: If systematic data download through API or bulk methods

2. Establish timeline of exfiltration:
   - First indication of data leaving organization
   - Peak exfiltration activity timeframe
   - Last confirmed data movement
   - Method consistency (single vector or multiple vectors used)

3. Identify user involvement:
   - Single user vs. multiple users
   - User actions vs. system/automation
   - Authorized users acting inappropriately vs. compromise scenario
   - Correlation with employment events (termination, resignation, etc.)

4. Ask Security Copilot to analyze leakage vector:

**SC Prompt 3**:
```
Analyze how data was leaked from the organization:

Leakage evidence:
- First indication of leakage: [INSERT_DATE_AND_EVIDENCE]
- Data was found at: [INSERT_LOCATION]
- Suspected exfiltration method: [INSERT_METHOD]
- Time period of likely exfiltration: [INSERT_DATES]

Evidence of exfiltration method:
- Email evidence: [INSERT_YES/NO_DETAILS]
- Cloud upload evidence: [INSERT_YES/NO_DETAILS]
- USB/removable media: [INSERT_YES/NO_DETAILS]
- Database export: [INSERT_YES/NO_DETAILS]
- API/bulk download: [INSERT_YES/NO_DETAILS]
- Multiple methods: [INSERT_YES/NO_DETAILS]

User involvement:
- User involved: [INSERT_UPN]
- User's role: [INSERT_ROLE]
- User action (intentional): [INSERT_YES/NO]
- Credential compromise (unauthorized): [INSERT_YES/NO]
- Timing correlation (termination, etc.): [INSERT_YES/NO_DETAILS]

Please analyze:
1. What is the most likely exfiltration vector?
2. How does this vector align with available evidence?
3. What other vectors should be investigated?
4. What is the timeline of exfiltration?
5. Is evidence consistent with intentional misconduct or compromise?
6. What additional logs/evidence should be collected?
```

**Decision Point**: What is the likely exfiltration vector?
- If email → Examine message trace, recipients, external domains
- If cloud → Identify service, account, sharing settings
- If USB/physical → Examine file access logs, timing
- If credential compromise → Activate incident response for account takeover
- If multiple vectors → May indicate sustained access, increase severity

---

### Step 4: User and Access Review

**Objective**: Identify who had access to leaked data and assess authorization appropriateness.

**Actions**:

1. Identify data owners and authorized accessors:
   - Business owner of leaked data
   - Teams/departments with legitimate access
   - Individuals with specific access to this dataset
   - Access grants and approval documentation
   - Time period of access authorization

2. Review user access patterns:
   - For exfiltration user (if identified):
     - Historical data access patterns before exfiltration
     - Access volume and scope during exfiltration period
     - Access outside normal work hours or locations
     - Access to broader data scope than role typically requires

   - For unauthorized access (if compromise):
     - Account login patterns and locations
     - Credential usage anomalies
     - API keys or tokens used
     - Mailbox delegation or shared access grants

3. Assess authorization appropriateness:
   - Did user have legitimate business reason to access this data?
   - Did user have authorization to access this specific data classification?
   - Did user's role justify access scope?
   - Were there approval processes that should have restricted access?

4. Ask Security Copilot to review access:

**SC Prompt 4**:
```
Review user access for this data leakage incident:

Leaked data context:
- Data owner: [INSERT_OWNER]
- Data classification: [INSERT_CLASSIFICATION]
- Data volume: [INSERT_VOLUME]

Exfiltration user (if identified):
- User: [INSERT_UPN]
- Role: [INSERT_ROLE]
- Access history: [INSERT_SUMMARY]
- Access volume during exfiltration: [INSERT_VOLUME]
- Off-hours/unusual location access: [INSERT_YES/NO]
- Access to broader scope than role requires: [INSERT_YES/NO]

Authorization context:
- User's legitimate data access scope: [INSERT_SCOPE]
- Approvals for access level: [INSERT_DETAILS]
- Access reviews/certifications: [INSERT_YES/NO_DATES]
- Data access restrictions (labels, DLP): [INSERT_DETAILS]

Unauthorized access indicators (if compromise):
- Unusual login locations: [INSERT_YES/NO]
- Credential anomalies: [INSERT_YES/NO]
- Mailbox delegation changes: [INSERT_YES/NO]
- API key usage: [INSERT_YES/NO]

Please assess:
1. Did the user have appropriate authorization for this data?
2. Was the access scope appropriate for their role?
3. Are there indicators of intentional misconduct vs. authorized access?
4. If compromise, what is the scope of potential unauthorized access?
5. What access restrictions/controls failed to prevent this exfiltration?
```

**Decision Point**: Was this authorized or unauthorized access?
- If authorized access misused → Insider threat case, potential discipline
- If unauthorized (compromised account) → Account compromise incident response
- If access that should have been restricted → Control failure requiring remediation
- If authorization ambiguity → Interview data owner to determine

---

### Step 5: Timeline Reconstruction

**Objective**: Establish detailed chronological sequence of events from initial compromise to discovery.

**Actions**:

1. Identify key timeline events:
   - First indication of unauthorized access or data movement
   - Peak exfiltration activity
   - Detection by monitoring/alerts
   - Discovery by external party or customer
   - Notification to incident response
   - Current time

2. Correlate with organizational events:
   - User employment events (hiring, termination, resignation notice, suspension)
   - Organizational changes (restructuring, acquisition)
   - System changes (policy deployment, security control implementation)
   - Known security incidents or breaches
   - Public events or announcements

3. Assess exposure duration:
   - Time between first exfiltration and discovery
   - Time data was accessible to unauthorized party
   - Ability to retrieve or suppress leaked data
   - Third-party download or access history

4. Ask Security Copilot to reconstruct timeline:

**SC Prompt 5**:
```
Reconstruct the incident timeline:

Timeline of events:
- [DATE/TIME]: [EVENT_AND_EVIDENCE]
- [DATE/TIME]: [EVENT_AND_EVIDENCE]
- [DATE/TIME]: [EVENT_AND_EVIDENCE]
- [DATE/TIME]: [EVENT_AND_EVIDENCE]
- [DATE/TIME]: [EVENT_AND_EVIDENCE]
- Current time: [TIMESTAMP]

Exposure duration:
- First exfiltration: [DATE]
- Last confirmed access: [DATE]
- Discovery date: [DATE]
- Total exposure: [DURATION]
- Estimated data accessibility to unauthorized party: [DURATION]

Organizational context:
- User employment events: [INSERT_EVENTS_DATES]
- System/policy changes: [INSERT_EVENTS_DATES]
- Related security incidents: [INSERT_YES/NO_DETAILS]
- Public announcements: [INSERT_YES/NO_DETAILS]

Please analyze:
1. What is the chronological sequence of events?
2. How long was data exposed before discovery?
3. Are there organizational correlations that explain timing?
4. What is the exposure window for third-party access?
5. Could data have been accessed or distributed further?
6. What is the latest point unauthorized access could have occurred?
```

**Decision Point**: How long was data exposed?
- If brief exposure (hours) → Limit damage, focus on containment
- If extended exposure (days/weeks) → Broader potential impact, likely multiple access points
- If exposure still ongoing → Containment actions must be immediate and decisive
- If time lag between exfil and discovery → May affect breach notification timeline

---

### Step 6: Impact Quantification

**Objective**: Measure business and regulatory impact of the breach.

**Actions**:

1. Quantify individuals affected:
   - Total individuals with data in breach
   - By nationality/geography (affects GDPR, CCPA, etc.)
   - By data type (customers, employees, partners)
   - Sensitivity of individual impact (financial risk, identity theft, health privacy)

2. Assess business impact:
   - Revenue impact (customer loss, contract penalties)
   - Operational impact (business interruption, system unavailability)
   - Reputational impact (brand damage, customer trust)
   - Competitive impact (trade secrets, product information exposed)
   - Financial impact (investigation costs, notification costs, legal costs, fines)

3. Regulatory impact:
   - Breach notification obligations and timeline
   - Regulatory investigation likelihood
   - Potential fines (GDPR up to 4% of revenue, others vary)
   - Compliance remediation requirements
   - Customer contract breach implications

4. Ask Security Copilot to quantify impact:

**SC Prompt 6**:
```
Quantify the impact of this data leakage incident:

Individuals affected:
- Total individuals: [INSERT_COUNT]
- US residents: [INSERT_COUNT]
- EU residents (GDPR): [INSERT_COUNT]
- California residents (CCPA): [INSERT_COUNT]
- Other jurisdiction: [INSERT_COUNT]

Data exposed by type:
- Customer data: [INSERT_COUNT_AND_TYPES]
- Employee data: [INSERT_COUNT_AND_TYPES]
- Financial data: [INSERT_COUNT_AND_TYPES]
- Health information: [INSERT_COUNT_AND_TYPES]
- Credentials/secrets: [INSERT_YES/NO]

Business impact assessment:
- Type of business: [INSERT_TYPE]
- Customer base sensitivity: [INSERT_ASSESSMENT]
- Competitive sensitivity of data: [INSERT_ASSESSMENT]
- Operational dependencies: [INSERT_ASSESSMENT]

Regulatory framework:
- GDPR personal data: [INSERT_YES/NO_COUNT]
- HIPAA protected health: [INSERT_YES/NO_COUNT]
- PCI-DSS cardholder data: [INSERT_YES/NO_COUNT]
- CCPA personal info: [INSERT_YES/NO_COUNT]
- State privacy laws: [INSERT_YES/NO_TYPES]

Please analyze:
1. Total number of individuals materially impacted
2. Estimated financial impact (notification, investigation, remediation)
3. Estimated regulatory fine exposure
4. Reputational/brand impact assessment
5. Customer notification obligations and timeline
6. Regulatory notification obligations and timeline
7. Recommended disclosure scope (external communication strategy)
```

**Decision Point**: What is the overall impact magnitude?
- If localized impact (small number, single jurisdiction) → Manage notification
- If broad impact (multiple jurisdictions, regulatory data) → Escalate to executive/legal
- If critical impact (large volume, trade secrets, many jurisdictions) → Public disclosure/press likely
- If employee impact → Coordinate with HR for employee notification

---

### Step 7: Containment Actions

**Objective**: Implement immediate actions to stop ongoing leakage and prevent further exposure.

**Note**: This section documents non-SC containment actions that incident response team must execute.

**Actions**:

1. **Immediate containment** (first 4 hours):
   - If account compromise suspected:
     - Force password reset for compromised account
     - Revoke active sessions and tokens
     - Review and revoke mailbox delegations
     - Revoke API keys and application access
     - Enable enhanced security (MFA, conditional access)
     - Monitor account for continued unauthorized access

   - If insider threat:
     - Notify manager and HR of suspension
     - Disable user account and access tokens
     - Revoke mailbox access and shared access
     - Retrieve company devices (laptop, phone)
     - Disable VPN and credential access
     - Restrict access to sensitive systems

   - If cloud exposure:
     - Identify compromised cloud account
     - Force password reset for cloud service
     - Revoke sharing permissions on exposed data
     - Contact cloud provider for evidence preservation
     - Request removal of publicly-shared content
     - Monitor cloud service for continued unauthorized access

   - If physical exposure:
     - File theft report with law enforcement
     - Contact device manufacturer for security wipe
     - Disable credentials/credentials on lost device
     - Monitor compromised device for connection attempts
     - Notify customers if applicable

2. **Short-term containment** (4-24 hours):
   - Implement or enhance data loss prevention:
     - Deploy DLP rules for exposed data types
     - Increase monitoring on similar data assets
     - Restrict external sharing temporarily for sensitive classifications
   - Credential rotation for users with access to exposed data
   - Audit and restrict access to similar data repositories
   - Implement additional authentication controls
   - Enhanced monitoring/alerting for related data/users

3. **Long-term remediation** (ongoing):
   - Policy and procedure improvements
   - Security control implementation
   - Training and awareness programs
   - Access review and recertification
   - Vendor/third-party security assessments
   - Insurance claim filing and management

---

### Step 8: Evidence Preservation

**Objective**: Preserve forensic evidence for investigation and potential legal proceedings.

**Actions**:

1. **Preserve digital evidence**:
   - Collect and preserve audit logs:
     - Exchange message trace logs (email access, forwarding rules)
     - SharePoint site and file activity logs
     - OneDrive access and sharing logs
     - Teams chat and file activity logs
     - Azure AD sign-in logs and risk detections
   - Preserve user account artifacts:
     - Mailbox forwarding rules and delegates
     - Calendar events and sharing
     - Application permissions and consents
     - API keys and tokens (hashed)
   - Preserve exfiltrated data evidence:
     - Screenshots of public postings
     - Copies of data found in unauthorized locations
     - Cloud service access logs
     - Third-party communications about breach

2. **Establish evidence chain of custody**:
   - Document who handled evidence and when
   - Maintain hash values for data integrity
   - Secure storage of sensitive evidence
   - Access controls on evidence repository
   - Retention policy aligned with legal holds

3. **Coordinate with legal**:
   - Ensure all evidence is legally preserved
   - Understand privilege implications
   - Communicate with counsel about investigation scope
   - Document privilege assertions if needed
   - Maintain attorney-client communication channels

4. **Forensic analysis**:
   - Work with forensics team for:
     - System image collection (if physical compromise)
     - Memory dump analysis (if malware suspected)
     - File system analysis (if data recovery needed)
     - Timeline reconstruction
     - User activity analysis

---

### Step 9: Stakeholder Notification Draft

**Objective**: Prepare notification communications for affected parties.

**Actions**:

1. Identify stakeholders requiring notification:
   - **Immediate escalation**: CISO, CEO, Chief Legal Officer
   - **Customer notification**: Affected customers (regulatory requirement)
   - **Employee notification**: Potentially affected employees
   - **Regulatory bodies**: As required by law (GDPR, state AGs, FBI, etc.)
   - **Media**: If public disclosure appropriate
   - **Business partners**: If their data involved
   - **Credit monitoring services**: If financial data exposed

2. Develop notification messages:
   - **Executive briefing**: Facts, impact, response plan
   - **Customer notification template**: Breach notice with recommended actions
   - **Employee notification**: If employee data/privacy affected
   - **Regulatory notification**: Formal breach notification as required
   - **Media statement**: Public communication if disclosure needed

3. Ask Security Copilot to draft notifications:

**SC Prompt 7**:
```
Draft stakeholder notifications for this data breach:

Incident summary:
- Breach discovery: [INSERT_DATE]
- Data exposed: [INSERT_TYPES]
- Individuals affected: [INSERT_COUNT]
- Geographic scope: [INSERT_SCOPE]
- Notification obligations: [INSERT_OBLIGATIONS]

Affected stakeholders:
- Customers: [INSERT_COUNT]
- Employees: [INSERT_YES/NO]
- Partners: [INSERT_YES/NO]
- Regulators: [INSERT_YES/NO]

Recommended response approach:
- Containment status: [INSERT_STATUS]
- Investigation status: [INSERT_STATUS]
- Remediation planned: [INSERT_SUMMARY]

Please draft:
1. Executive briefing summary (for leadership)
2. Customer notification letter (formal breach notice)
3. Employee notification (if applicable)
4. Regulatory notification template (as required)
5. FAQ for internal use
6. Recommended media response (if public disclosure needed)
7. Suggested customer support talking points
```

**Decision Point**: What notification strategy applies?
- If internal only → Executive briefing and employee notification
- If customer notification required → Formal breach letters required
- If regulatory notification required → Follow regulatory notification requirements
- If public disclosure → Coordinate with PR/media team
- Document all notification approvals and send times

---

### Step 10: Post-Incident Review Preparation

**Objective**: Establish framework for post-incident analysis and lessons learned.

**Actions**:

1. Establish post-incident review (PIR) schedule:
   - Initial PIR: 7 days post-incident (tactical response review)
   - Comprehensive PIR: 30 days post-incident (root cause analysis)
   - Policy review: 60 days post-incident (control effectiveness)

2. Identify review participants:
   - Incident response team
   - Security team leads
   - Data owner and business leadership
   - IT operations (for system/control issues)
   - Legal and compliance
   - HR (if employee-related)

3. Prepare review topics:
   - Timeline accuracy and completeness
   - Detection effectiveness (what worked, what didn't)
   - Response timeliness and effectiveness
   - Communication effectiveness (internal and external)
   - Control failures (what allowed this to happen)
   - Lessons learned and improvements
   - Prevention recommendations
   - Readiness improvements

4. Ask Security Copilot to outline PIR structure:

**SC Prompt 8**:
```
Outline the post-incident review structure for this data breach:

Incident summary:
- Breach type: [INSERT_TYPE]
- Root cause (preliminary): [INSERT_PRELIMINARY_CAUSE]
- Key control failures (if any): [INSERT_FAILURES]
- Detection method: [INSERT_METHOD]
- Detection lag: [INSERT_DURATION]

Response assessment:
- Containment effectiveness: [INSERT_ASSESSMENT]
- Notification timeliness: [INSERT_ASSESSMENT]
- Communication effectiveness: [INSERT_ASSESSMENT]
- Investigation thoroughness: [INSERT_ASSESSMENT]

Please outline:
1. Post-incident review schedule and milestones
2. Participants required for effective PIR
3. Key questions to be answered in PIR
4. Root cause analysis framework
5. Control failure analysis approach
6. Lessons learned extraction process
7. Recommended improvements (top 5 priority items)
8. Metrics for success (how will we know we've improved?)
```

5. Prepare tracking for remediation items:
   - Create action items tracker for improvements
   - Assign owners and deadlines
   - Track progress and completion
   - Link to policy updates and control implementations

---

## Response Timeline and Escalation

**Immediate** (0-2 hours):
- Activate incident response team
- Escalate to CISO and executive leadership
- Secure initial evidence
- Begin containment assessment
- Start SC analysis

**Early response** (2-8 hours):
- Complete initial scope assessment
- Identify containment actions and begin implementation
- Notify legal and compliance
- Begin customer impact analysis
- Start evidence preservation

**Active response** (8-24 hours):
- Containment actions completed
- Data classification completed
- Impact quantification completed
- Notification strategy approved
- Customer notification begins (if required)
- Regulatory notification (within required timelines)

**Ongoing** (24 hours +):
- Investigation and forensics continue
- Evidence collection and preservation ongoing
- Stakeholder communication ongoing
- Remediation and control improvements begin
- Post-incident review preparation

---

## Output Artifacts

Data leakage response must produce the following artifacts:

1. **Incident Report** (stored in incident management system):
   - Incident timeline and facts
   - Data impact assessment
   - Root cause analysis
   - Containment actions taken
   - Response timeline and decisions
   - Notification letters sent

2. **Impact Assessment** (for executive/legal):
   - Summary of affected individuals
   - Data types involved
   - Regulatory framework applicability
   - Estimated financial impact
   - Recommended notification strategy

3. **Evidence Package** (for investigation/legal):
   - Audit logs and preserved evidence
   - Chain of custody documentation
   - Screenshots of public disclosures
   - User access and activity records
   - Timeline reconstruction
   - Forensic analysis results (if applicable)

4. **Customer Notification Materials**:
   - Breach notification letters sent
   - Notification recipient list
   - Send dates and delivery confirmation
   - FAQ and support materials
   - Credit monitoring offer documentation

5. **Regulatory Notification Documentation**:
   - Regulatory notifications sent
   - Regulatory agency responses
   - Compliance timeline tracking
   - Investigation cooperation documentation

6. **Post-Incident Review Plan**:
   - PIR schedule and participants
   - Root cause analysis findings
   - Control failure analysis
   - Lessons learned summary
   - Remediation items and status

---

## Prompt Chain: Complete SC Interaction Sequence

Execute these SC prompts in order to conduct data leakage response:

1. **SC Prompt 1** → Initial incident assessment
2. **SC Prompt 2** → Data classification and regulatory framework
3. **SC Prompt 3** → Leakage vector analysis
4. **SC Prompt 4** → User access review
5. **SC Prompt 5** → Timeline reconstruction
6. **SC Prompt 6** → Impact quantification
7. **SC Prompt 7** → Stakeholder notification drafting
8. **SC Prompt 8** → Post-incident review planning

**Note**: Execute prompts sequentially. Later prompts may require refinement of earlier findings if significant new information emerges.

---

## Common Pitfalls

**1. Delayed escalation to leadership**
- Treating breach as security issue when it's business-critical
- Failure to brief executive team within first 4 hours
- Mitigation: Escalate immediately upon confirmation

**2. Incomplete evidence preservation**
- Failing to preserve logs before systems rotate them off
- Not understanding data retention policies
- Losing forensic chain of custody
- Mitigation: Involve legal counsel early, engage forensics team

**3. Premature public disclosure**
- Making public statements before facts confirmed
- Communicating before legal review
- Creating liability through admissions
- Mitigation: All external communications reviewed by legal counsel

**4. Inadequate customer notification**
- Missing required regulatory notification timeframes
- Insufficient notification content
- Not offering appropriate remediation (credit monitoring)
- Mitigation: Work with legal to ensure compliance

**5. False assumptions about scope**
- Assuming only identified data was exposed
- Not assessing broader system compromise
- Underestimating potential third-party distribution
- Mitigation: Assume broader scope, investigate thoroughly

**6. Insufficient user/access review**
- Not understanding how data was accessed
- Missing multiple users involved
- Failing to assess authorization appropriateness
- Mitigation: Deep review of access logs in Step 4

**7. Poor timeline reconstruction**
- Missing key events or timeline gaps
- Incorrectly estimating exposure duration
- Not correlating with organizational events
- Mitigation: Use multiple evidence sources for timeline

**8. Weak post-incident follow-up**
- Not conducting real post-incident review
- Failing to implement improvements
- Not tracking remediation to completion
- Mitigation: Schedule PIR immediately, assign ownership

---

## Quality Checklist

Before considering response phase complete, verify:

- [ ] All 8 SC prompts executed and analyzed
- [ ] Executive team briefed within 4 hours
- [ ] Initial scope and severity assessed
- [ ] Data classification completed
- [ ] Regulatory frameworks identified
- [ ] Leakage vector identified or narrowed
- [ ] User access review completed
- [ ] Timeline reconstructed with key events
- [ ] Impact quantified (individuals, regulatory, financial)
- [ ] Containment actions identified and begun
- [ ] Evidence preservation initiated
- [ ] Legal counsel briefed and involved
- [ ] Notification strategy approved
- [ ] Customer notification in progress (if required)
- [ ] Regulatory notification in progress (if required)
- [ ] Investigation plan established
- [ ] Post-incident review scheduled
- [ ] Remediation tracking established
- [ ] Audit trail documented for all decisions
- [ ] All action items assigned with ownership and deadlines

**Response phase is operationally ready when all critical items checked.**

