# Prompt Validation Matrix

**Last Updated:** April 2026
**Status:** Active tracking document
**Purpose:** Systematic tracking of prompt testing status, SC behavior, and version compatibility

---

## How to Use This Matrix

1. Before using a prompt in production, check its validation status below
2. After testing a prompt, update the matrix with your results
3. Re-validate prompts quarterly or after SC updates
4. Flag any prompt that behaves differently than documented

## Validation Status Key

| Status | Meaning |
|--------|---------|
| **VALIDATED** | Tested against live SC with Purview plugin; returns expected output |
| **PARTIALLY-VALIDATED** | Tested but output is incomplete or inconsistent across sessions |
| **ASPIRATIONAL** | Not yet tested; designed based on documented capabilities |
| **EXPERIMENTAL** | Generated for undocumented scenario; requires testing before production use |
| **DEPRECATED** | Previously worked but no longer produces reliable output |
| **BLOCKED** | Requires plugin or feature not yet available |

---

## DLP Prompt Book (`prompts/dlp/prompt-book.md`)

| Prompt ID | Prompt Title | Status | Last Tested | SC Behavior Notes |
|-----------|-------------|--------|-------------|-------------------|
| DLP-1.1 | Single DLP Alert Severity Classification | VALIDATED | Apr 2026 | Top 5 high severity alerts reliably returned |
| DLP-1.2 | Batch Triage and Priority Ranking | VALIDATED | Apr 2026 | Uses Triage Purview Alerts capability |
| DLP-1.3 | Legitimate Business Exception | VALIDATED | Apr 2026 | Requires analyst context about role/business |
| DLP-1.4 | Filter Alerts by Policy and Pattern | PARTIALLY-VALIDATED | Apr 2026 | Filtering by specific policy name may not work consistently |
| DLP-2.1 | DLP Alert Summary and Context | VALIDATED | Apr 2026 | Core Summarize Purview Alert capability |
| DLP-2.2 | User Risk Profile and Historical Pattern | VALIDATED | Apr 2026 | Uses Get User Risk Summary |
| DLP-2.3 | Data Exfiltration Activity Correlation | VALIDATED | Apr 2026 | Uses Get Data Risk Summary + Get User Risk Summary |
| DLP-2.4 | Sequential Activity Timeline | VALIDATED | Apr 2026 | Uses Get User Risk Summary |
| DLP-2.5 | Policy Match Analysis and Confidence | VALIDATED | Apr 2026 | Uses Summarize Purview Alert |
| DLP-3.1 | DLP Alert + Insider Risk Context | VALIDATED | Apr 2026 | Requires IRM data sharing enabled |
| DLP-3.2 | Data Movement Across Channels | PARTIALLY-VALIDATED | Apr 2026 | Channel-level detail may vary by tenant |
| DLP-4.1 | Policy Effectiveness and FP Analysis | VALIDATED | Apr 2026 | Uses Triage Purview Alerts |
| DLP-4.2 | False Positive Reduction | PARTIALLY-VALIDATED | Apr 2026 | Recommendations are generic without specific policy details |
| DLP-5.1 | Incident Summary for Leadership | VALIDATED | Apr 2026 | SC synthesis capability |
| DLP-5.2 | Post-Incident Policy Recommendations | PARTIALLY-VALIDATED | Apr 2026 | Advisory quality depends on incident complexity |
| DLP-5.3 | Compliance Documentation | VALIDATED | Apr 2026 | SC generates audit-ready format |

## IRM Prompt Book (`prompts/irm/prompt-book.md`)

| Prompt ID | Prompt Title | Status | Last Tested | SC Behavior Notes |
|-----------|-------------|--------|-------------|-------------------|
| IRM-1.1 | Single IRM Alert Risk Assessment | VALIDATED | Apr 2026 | Uses Triage Purview Alerts |
| IRM-1.2 | Multi-User Risk Priority Ranking | VALIDATED | Apr 2026 | Uses Triage + Get User Risk Summary |
| IRM-1.3 | Quick Context: Is This User Suspicious | VALIDATED | Apr 2026 | Uses Get User Risk Summary |
| IRM-2.1 | User Activity Timeline | VALIDATED | Apr 2026 | Uses Get User Risk Summary; baselines come from IRM, not SC |
| IRM-2.2 | Data Exfiltration Activities | VALIDATED | Apr 2026 | Uses Get User Risk Summary + Get Data Risk Summary |
| IRM-2.3 | Obfuscation and Hiding Techniques | PARTIALLY-VALIDATED | Apr 2026 | Obfuscation detection limited to what IRM surfaces |
| IRM-2.4 | Activity Escalation Pattern | VALIDATED | Apr 2026 | Uses Get User Risk Summary |
| IRM-2.5 | Unusual Behavior Baseline Comparison | PARTIALLY-VALIDATED | Apr 2026 | SC reports IRM anomaly indicators; does not compute own baseline |
| IRM-3.1 | Risk Score Decomposition | VALIDATED | Apr 2026 | Uses Zoom Into Purview User Risk |
| IRM-3.2 | Multi-User Risk Comparison | PARTIALLY-VALIDATED | Apr 2026 | Cross-user comparison may require separate queries |
| IRM-4.1 | Executive Risk Report | VALIDATED | Apr 2026 | SC synthesis capability |

## Audit Prompt Book (`prompts/audit/prompt-book.md`)

| Prompt ID | Prompt Title | Status | Last Tested | SC Behavior Notes |
|-----------|-------------|--------|-------------|-------------------|
| AUD-1 | Incident Timeline Reconstruction | PARTIALLY-VALIDATED | Apr 2026 | Audit log lag (24-48hrs) affects completeness |
| AUD-2 | Data Exfiltration Patterns | PARTIALLY-VALIDATED | Apr 2026 | Broad scope may return generic results |
| AUD-3 | User Risk Signal Correlation | VALIDATED | Apr 2026 | Cross-signal when IRM data sharing enabled |
| AUD-4 | Policy Violation Root Cause Analysis | VALIDATED | Apr 2026 | Uses Triage + Zoom Into Data Risk |
| AUD-5 | Compliance Audit Trail Validation | ASPIRATIONAL | — | Full compliance validation exceeds single-prompt scope |
| AUD-6 | Anomalous Sign-In Detection | ASPIRATIONAL | — | Requires Entra plugin (not Purview alone) |
| AUD-7 | Data Flow Mapping | PARTIALLY-VALIDATED | Apr 2026 | Scope-dependent; best with specific user/data |
| AUD-8 | User Access Review | ASPIRATIONAL | — | Access review data may not be fully available via SC |
| AUD-9 | Training Gap Analysis | ASPIRATIONAL | — | Derived from violation patterns; training data not in SC |
| AUD-10 | eDiscovery and Regulatory Response | ASPIRATIONAL | — | eDiscovery capability coverage is limited |

## DSPM Prompt Book (`prompts/dspm/prompt-book.md`)

| Prompt ID | Prompt Title | Status | Last Tested | SC Behavior Notes |
|-----------|-------------|--------|-------------|-------------------|
| DSPM-1.1 | Executive Data Risk Dashboard | VALIDATED | Apr 2026 | Uses Get Data Risk Summary |
| DSPM-1.2 | Sensitive Data Exposure Analysis | VALIDATED | Apr 2026 | Uses Get Data Risk Summary |
| DSPM-1.3 | Oversharing Risk Assessment | VALIDATED | Apr 2026 | Uses Get Data Risk Summary |
| DSPM-1.4 | Label Coverage and Retention | VALIDATED | Apr 2026 | Uses Get Data Risk Summary |
| DSPM-2.1 | AI-Accessible Sensitive Data | ASPIRATIONAL | — | Copilot-specific data governance is emerging |
| DSPM-2.2 | Copilot Interaction Risk | ASPIRATIONAL | — | DLP for Copilot interactions is new |
| DSPM-3.1 | Prioritized Remediation Plan | PARTIALLY-VALIDATED | Apr 2026 | SC provides recommendations; remediation is manual |

## MIP Prompt Book (`prompts/mip/prompt-book.md`)

| Prompt ID | Prompt Title | Status | Last Tested | SC Behavior Notes |
|-----------|-------------|--------|-------------|-------------------|
| MIP-1 | Label Coverage Analysis | VALIDATED | Apr 2026 | SC reports label statistics from Purview |
| MIP-2 | Mislabeled Content Detection | PARTIALLY-VALIDATED | Apr 2026 | Detection depends on available metadata |
| MIP-3 | Auto-Labeling Effectiveness | PARTIALLY-VALIDATED | Apr 2026 | Rule-level metrics may not be fully exposed |
| MIP-4 | Regulatory Label Validation | ASPIRATIONAL | — | Full compliance mapping exceeds SC scope |
| MIP-5 | Label Taxonomy Optimization | PARTIALLY-VALIDATED | Apr 2026 | SC provides usage-based recommendations |
| MIP-6 | Labeled Data Access Analytics | ASPIRATIONAL | — | Cross-system label access visibility limited |
| MIP-7 | Policy Enforcement Review | PARTIALLY-VALIDATED | Apr 2026 | Enforcement data available; depth varies |
| MIP-8 | Protection Settings Review | VALIDATED | Apr 2026 | SC can review protection configurations |
| MIP-9 | Email Labeling Compliance | ASPIRATIONAL | — | Email-specific label metrics limited |
| MIP-10 | Label Program KPIs | PARTIALLY-VALIDATED | Apr 2026 | KPI generation from aggregate data |

## Executive Prompt Book (`prompts/executive/prompt-book.md`)

| Prompt ID | Prompt Title | Status | Last Tested | SC Behavior Notes |
|-----------|-------------|--------|-------------|-------------------|
| EXEC-1 | Monthly Risk Summary | VALIDATED | Apr 2026 | SC synthesis capability |
| EXEC-2 | Incident Impact Brief | VALIDATED | Apr 2026 | SC synthesis capability |
| EXEC-3 | Compliance Audit Readiness | VALIDATED | Apr 2026 | SC synthesis capability |
| EXEC-4 | Breach Notification | VALIDATED | Apr 2026 | SC synthesis capability |
| EXEC-5 | Quarterly Board Metrics | VALIDATED | Apr 2026 | SC synthesis capability |
| EXEC-6 | Technical Briefing CISO/CTO | VALIDATED | Apr 2026 | SC synthesis capability |
| EXEC-7 | Customer-Facing Posture | VALIDATED | Apr 2026 | SC synthesis capability |
| EXEC-8 | Risk Remediation Roadmap | ASPIRATIONAL | — | Roadmap generation requires organizational context |

## Workshop Prompt Book (`prompts/workshop/prompt-book.md`)

| Prompt ID | Prompt Title | Status | Last Tested | SC Behavior Notes |
|-----------|-------------|--------|-------------|-------------------|
| WS-1 | One Click Data Risk Summary | VALIDATED | Apr 2026 | Reliable demo prompt |
| WS-2 | High-Risk User Access | VALIDATED | Apr 2026 | Reliable demo prompt |
| WS-3 | Breach Investigation Walkthrough | VALIDATED | Apr 2026 | Works with realistic scenario |
| WS-4 | Label Taxonomy Design | VALIDATED | Apr 2026 | SC provides good design guidance |
| WS-5 | Custom Talking Points | VALIDATED | Apr 2026 | SC adapts to audience |
| WS-6+ | Remaining workshop prompts | VALIDATED | Apr 2026 | All workshop prompts verified for demos |

## DFIR Prompt Book (`prompts/dfir/prompt-book.md`)

| Prompt ID | Prompt Title | Status | Last Tested | SC Behavior Notes |
|-----------|-------------|--------|-------------|-------------------|
| DFIR-1.1 | Alert Intake & Severity | ASPIRATIONAL | — | Structured but untested as a chain |
| DFIR-1.2 | Policy Context Assessment | ASPIRATIONAL | — | Full policy history may not be surfaced |
| DFIR-1.3 | Alert Clustering | ASPIRATIONAL | — | 30-day user alert query may return partial |
| DFIR-1.4 | Immediate Risk Signal Check | ASPIRATIONAL | — | Uses Get User Risk Summary |
| DFIR-2.x | Data Content Analysis | ASPIRATIONAL | — | SC cannot access file contents |
| DFIR-3.x | User Behavioral Profile | ASPIRATIONAL | — | Deep profiling requires multiple queries |
| DFIR-4.x | IRM Signal Correlation | ASPIRATIONAL | — | Requires IRM data sharing |
| DFIR-5.x | Endpoint Analysis | ASPIRATIONAL | — | Requires Defender for Endpoint plugin |
| DFIR-6.x | Identity Analysis | ASPIRATIONAL | — | Requires Entra plugin |
| DFIR-7.x-10.x | Advanced phases | ASPIRATIONAL | — | Multi-plugin cross-correlation untested |

## Operations Prompt Book (`prompts/operations/prompt-book.md`)

| Prompt ID | Prompt Title | Status | Last Tested | SC Behavior Notes |
|-----------|-------------|--------|-------------|-------------------|
| OPS-1 | Label Configuration Guidance | VALIDATED | Apr 2026 | SC provides configuration guidance |
| OPS-2 | DLP Policy Configuration | VALIDATED | Apr 2026 | SC provides policy design guidance |
| OPS-3 | Auto-Labeling Rule Setup | VALIDATED | Apr 2026 | SC provides rule design guidance |
| OPS-4 | Policy Health Check | VALIDATED | Apr 2026 | SC can review policies |
| OPS-5+ | Remaining operations | VALIDATED | Apr 2026 | Configuration guidance is reliable SC capability |

---

## Validation Process

### How to Validate a Prompt

1. Open Security Copilot with the Purview plugin enabled
2. Replace all `<parameters>` with real values from your environment
3. Execute the prompt and capture the full response
4. Compare the response to the "Expected Output" section in the prompt book
5. Score the output:
   - **Match (VALIDATED):** Output contains all expected fields and is accurate
   - **Partial (PARTIALLY-VALIDATED):** Output contains some expected fields; missing or inaccurate data
   - **Fail (update to ASPIRATIONAL or DEPRECATED):** Output is incorrect, empty, or unrelated
6. Document any SC behavior differences in the "SC Behavior Notes" column
7. Update validation date

### Re-Validation Triggers

- SC model update or version change
- Purview plugin update
- New SC capability announcement
- Prompt modification
- Quarterly scheduled review
- User report of unexpected behavior

---

**See also:**
- [audit-log-operations.md](reference/audit-log-operations.md) for exact operation names to improve prompt accuracy
- [sensitive-information-types.md](reference/sensitive-information-types.md) for exact SIT names
- [plugin-dependency-map.md](plugin-dependency-map.md) for required plugins per prompt
