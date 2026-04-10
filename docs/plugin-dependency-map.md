# Cross-Plugin Dependency Map

**Last Updated:** April 2026
**Status:** [VALIDATED]
**Purpose:** Map each prompt book and investigation scenario to the Security Copilot plugins required for full functionality

---

## Why This Matters

Security Copilot uses a plugin architecture. Each plugin provides access to a specific data source. Prompts that reference data outside the enabled plugins will return incomplete results or fail silently. Before running a prompt, verify the required plugins are enabled in your tenant.

---

## Plugin Inventory

| Plugin Name | Data Source | Status | Required For |
|------------|------------|--------|-------------|
| **Microsoft Purview** | DLP alerts, IRM cases, MIP labels, audit logs, DSPM data | GA | All prompt books in this framework |
| **Microsoft Entra** | Sign-in logs, risky users, directory roles, group membership | GA | Identity-related investigation, sign-in anomalies |
| **Microsoft Defender for Endpoint** | Device activity, process execution, file events, network activity | GA | Endpoint DLP correlation, device forensics |
| **Microsoft Defender for Cloud Apps** | Cloud app usage, shadow IT, OAuth app consent, session controls | GA | Cloud exfiltration, shadow IT detection |
| **Microsoft Sentinel** | Custom detections, incidents, SOAR playbooks, threat intelligence | GA | SIEM correlation, custom detection rules |
| **Microsoft Intune** | Device compliance, app protection, configuration profiles | GA | Device posture validation |
| **Microsoft Defender XDR** | Cross-product incidents, unified alerts, automated investigation | GA | Incident correlation across Microsoft 365 |
| **Natural Language to KQL** | KQL query generation from natural language | GA | Advanced log queries |

---

## Prompt Book to Plugin Dependency Map

### Core Requirement: All Prompt Books

**Microsoft Purview plugin** is required for every prompt book in this framework. The 6 system capabilities all flow through this plugin:
- Get Data Risk Summary
- Get User Risk Summary
- Summarize Purview Alert
- Triage Purview Alerts
- Zoom Into Purview Data Risk
- Zoom Into Purview User Risk

### DLP Prompt Book (`prompts/dlp/prompt-book.md`)

| Section | Primary Plugin | Additional Plugins Needed | Notes |
|---------|---------------|--------------------------|-------|
| 1. Triage | Purview | None | Core DLP triage uses Purview plugin only |
| 2. Investigation | Purview | Entra (for user identity context) | User role and identity enrichment benefits from Entra |
| 3. Cross-Signal Investigation | Purview | Entra, Defender for Endpoint | Cross-signal requires IRM data sharing enabled + endpoint data |
| 4. False Positive Detection | Purview | None | Policy analysis uses Purview plugin only |
| 5. Reporting | Purview | None | Summary generation uses Purview plugin only |

### IRM Prompt Book (`prompts/irm/prompt-book.md`)

| Section | Primary Plugin | Additional Plugins Needed | Notes |
|---------|---------------|--------------------------|-------|
| 1. Triage | Purview | None | IRM triage uses Purview plugin only |
| 2. Investigation | Purview | Entra (sign-in patterns), Defender for Endpoint (device activity) | Deep investigation benefits from identity and device context |
| 3. Obfuscation Detection | Purview | Entra (log access patterns) | Detecting evidence hiding may need sign-in data |
| 4. Escalation Patterns | Purview | Entra, Defender for Endpoint | Multi-signal escalation detection |
| 5. Risk Assessment | Purview | None | Risk scoring uses Purview IRM data |

### Audit Prompt Book (`prompts/audit/prompt-book.md`)

| Section | Primary Plugin | Additional Plugins Needed | Notes |
|---------|---------------|--------------------------|-------|
| 1. Timeline Reconstruction | Purview | Entra (sign-in anomalies) | Sign-in patterns require Entra plugin |
| 2. Exfiltration Patterns | Purview | Defender for Endpoint (endpoint DLP), Defender for Cloud Apps (cloud uploads) | Full exfiltration detection spans multiple plugins |
| 3. User Risk Correlation | Purview | Entra (identity context) | Cross-signal requires multiple plugins |
| 4. Policy Violation Analysis | Purview | None | Policy analysis uses Purview plugin only |
| 5. Compliance Validation | Purview | None | Audit trail validation uses Purview plugin only |
| 6. Sign-In Anomaly Detection | **Entra** (primary) | Purview (for data access correlation) | This section primarily needs the Entra plugin |

### DSPM Prompt Book (`prompts/dspm/prompt-book.md`)

| Section | Primary Plugin | Additional Plugins Needed | Notes |
|---------|---------------|--------------------------|-------|
| 1. Posture Assessment | Purview | None | DSPM data flows through Purview plugin |
| 2. AI/Copilot Security | Purview | Defender for Cloud Apps (for third-party AI app monitoring) | [ASPIRATIONAL] — full AI data governance is emerging |
| 3. Remediation | Purview | None | Policy recommendations use Purview plugin only |

### MIP Prompt Book (`prompts/mip/prompt-book.md`)

| Section | Primary Plugin | Additional Plugins Needed | Notes |
|---------|---------------|--------------------------|-------|
| All sections | Purview | None | Label analysis uses Purview plugin only |

### Executive Prompt Book (`prompts/executive/prompt-book.md`)

| Section | Primary Plugin | Additional Plugins Needed | Notes |
|---------|---------------|--------------------------|-------|
| All sections | Purview | None | Executive synthesis uses Purview plugin only |

### Operations Prompt Book (`prompts/operations/prompt-book.md`)

| Section | Primary Plugin | Additional Plugins Needed | Notes |
|---------|---------------|--------------------------|-------|
| All sections | Purview | None | Configuration guidance uses Purview plugin only |

### DFIR Prompt Book (`prompts/dfir/prompt-book.md`)

| Phase | Primary Plugin | Additional Plugins Needed | Notes |
|-------|---------------|--------------------------|-------|
| 1. DLP Alert Triage | Purview | None | Standard triage |
| 2. Data Content Analysis | Purview | None | Purview data risk analysis |
| 3. User Behavioral Profile | Purview | Entra (identity context) | User profiling benefits from Entra |
| 4. IRM Signal Correlation | Purview | None | IRM data through Purview plugin |
| 5. Endpoint & Device Analysis | **Defender for Endpoint** (primary) | Purview | Endpoint forensics requires MDE plugin |
| 6. Identity & Access Analysis | **Entra** (primary) | Purview | Sign-in and identity analysis requires Entra |
| 7. Lateral Movement | Purview | Entra, Defender for Endpoint | Cross-signal lateral movement detection |
| 8. Third-Party & Cloud | **Defender for Cloud Apps** (primary) | Purview | Cloud app and shadow IT requires MDCA |
| 9. Evidence Packaging | Purview | All previously used plugins | Consolidation phase |
| 10. Post-Investigation | Purview | None | Reporting and lessons learned |

---

## Playbook to Plugin Dependency Map

| Playbook | Required Plugins | Optional Plugins |
|----------|-----------------|-----------------|
| `dlp-incident-investigation.md` | Purview | Entra, Defender for Endpoint |
| `irm-case-triage.md` | Purview | Entra |
| `data-leakage-response.md` | Purview | Entra, Defender for Endpoint, Defender for Cloud Apps |
| `post-incident-review.md` | Purview | None |

---

## Plugin Enablement Verification

Before running an investigation, verify plugin status with this SC prompt:

```
What plugins are currently enabled in my Security Copilot instance?
List the name and status of each plugin.
```

If a required plugin is not enabled, the prompt will either:
1. Return incomplete results (missing data from that source)
2. State it cannot access the requested data
3. Provide partial analysis based on available plugins only

**Important:** SC will NOT tell you that it failed to query a disabled plugin. It may simply omit data from that source without warning. Always verify plugin status before complex investigations.

---

## Plugin-Aware Prompt Guidance

When writing prompts that span multiple plugins, explicitly name the data sources so SC knows which plugins to invoke:

### Single-plugin prompt (Purview only):
```
Using Microsoft Purview, triage the most recent DLP alerts
and show me which should be prioritized today.
```

### Multi-plugin prompt (Purview + Entra):
```
Using Microsoft Purview and Microsoft Entra data, correlate
the DLP alert for user <user> with their recent sign-in patterns.
Show any sign-in anomalies (impossible travel, new locations)
that occurred within 24 hours of the DLP policy match.
```

### Multi-plugin prompt (Purview + Defender for Endpoint):
```
Using Microsoft Purview DLP data and Microsoft Defender for Endpoint
device activity, investigate whether user <user> copied sensitive files
to removable media. Show DLP policy matches alongside endpoint
file copy events.
```

---

**See also:**
- [audit-log-operations.md](reference/audit-log-operations.md) — Section 7 flags which operations require non-Purview plugins
- [sensitive-information-types.md](reference/sensitive-information-types.md) for SIT exact names
- [limitations-and-blind-spots.md](technical-deep-dives/limitations-and-blind-spots.md) for data access constraints
