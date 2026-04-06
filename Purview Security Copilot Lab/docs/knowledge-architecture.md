# Knowledge Architecture

## Documentation Architecture

This repository serves as a structured knowledge base for using Security Copilot effectively in Purview data security contexts. The architecture distinguishes between stable product knowledge and fast-changing operational knowledge, organized to support multiple personas with different access patterns.

### Purpose and Structure

The documentation architecture is designed to:

1. **Enable Discoverability**: Teams working on different Purview products should quickly find relevant prompts, playbooks, and knowledge without digging through unrelated content
2. **Support Multiple Learning Styles**: Some users prefer step-by-step playbooks; others prefer prompt templates to adapt; others prefer reference documentation
3. **Track Evolution**: As Copilot behavior changes or prompt effectiveness evolves, documentation should reflect what works now, not what worked 3 months ago
4. **Reduce Duplication**: Shared knowledge (e.g., Purview data models, alert structures) should be documented once and referenced consistently
5. **Enable Contribution**: Teams should have clear conventions for adding prompts, playbooks, and knowledge without destabilizing existing content

### Top-Level Organization

```
/docs
  /reference                    # Stable product knowledge
  /prompts                      # Versioned prompt templates
  /playbooks                    # Investigation and operational workflows
  /knowledge                    # Extracted operational knowledge from PDFs and docs
  /templates                    # Reporting and summary templates
  /case-studies                 # Customer scenarios and outcomes
  README.md                     # Navigation and quick-start guide
  vision-and-scope.md           # Strategic context
  personas.md                   # User roles and use cases
  knowledge-architecture.md     # This file
```

---

## Knowledge Categories

### Stable Knowledge (Versioned Infrequently)

**Product Capabilities and Data Models**

Purview product structure, DLP policy components, IRM permission types, label hierarchies, audit event types, and API schemas. These change with major product releases (quarterly to semi-annually) but are stable within a release.

- **Location**: `/docs/reference/`
- **Update Cadence**: Quarterly product review; updated only when Purview releases new features or changes data structures
- **Format**: Structured reference documentation, data dictionaries, schema diagrams
- **Examples**:
  - DLP policy logic and rule evaluation order
  - IRM permission inheritance rules
  - Label taxonomy best practices
  - Audit event reference (event IDs, attributes, schema)
  - Data sources available to Security Copilot via Purview connectors

**Organizational Data**

Custom label taxonomy, DLP policy names and purposes, IRM template definitions, critical data asset inventory. These are organization-specific and should be extracted from customer engagements.

- **Location**: `/docs/reference/organizational-data/` (with placeholders for customer data, not actual customer data in the repo)
- **Update Cadence**: Annually or when organizational data classification changes
- **Format**: Example templates, anonymized samples
- **Examples**:
  - Customer label taxonomy (with definitions)
  - DLP policy inventory (by product line)
  - Critical data asset categories
  - Compliance mapping (which controls address which regulations)

### Fast-Changing Knowledge (Versioned Frequently)

**Prompt Effectiveness and Behavior**

How Security Copilot responds to specific prompts, which queries yield reliable results, which scenarios cause Copilot to hallucinate or give incomplete answers. This changes frequently as Copilot's underlying model is updated and as new plugins become available.

- **Location**: `/docs/prompts/` with version tracking
- **Update Cadence**: Weekly to monthly based on community testing
- **Format**: Versioned prompt templates with success rate, known limitations, and recent changes
- **Examples**:
  - "Alert Triage" prompt (v2.3): 90% accuracy on false positive detection
  - "Investigation Summary" prompt (v1.8): Works well with 50+ alerts; may miss patterns with <10 alerts
  - "Policy Tuning Recommendation" prompt (v2.1): Added constraint to focus on top 10 alert types

**Operational Insights and Heuristics**

Patterns observed in customer environments about false positive causes, common policy tuning mistakes, effective label coverage strategies, IRM permission misconfigurations. These are learned from live customer work and change as the security landscape evolves.

- **Location**: `/docs/playbooks/` and `/docs/knowledge/`
- **Update Cadence**: Monthly or on-demand when new patterns are identified
- **Format**: Observation + recommendation + evidence
- **Examples**:
  - "90% of false positives in DLP occur when legitimate internal teams discuss sensitive topics. Recommendation: add business process exceptions"
  - "IRM permission inheritance is commonly misunderstood; clarify to customers that 'Do Not Allow' is not the same as 'Do Not Forward'"
  - "Label adoption stalls when taxonomy has >15 labels; recommend consolidation to 8-10 core labels"

**Security Copilot Availability and Constraints**

Which plugins are available, what data sources Copilot can access via Purview connectors, known latency or timeout issues, rate limits, and when Copilot cannot reliably help.

- **Location**: `/docs/reference/copilot-constraints.md`
- **Update Cadence**: Monthly or when new plugins are released
- **Format**: Structured reference with date last verified
- **Examples**:
  - Audit logs available up to 90 days; older events require archival queries
  - IRM permission queries may time out with >100k objects; recommend filtering by date range
  - DLP policy evaluation is near-real-time but may have up to 15-minute latency
  - Real-time policy enforcement decisions are not available via Copilot

---

## Folder Structure for Local Knowledge Files

### Purpose

This structure allows teams to maintain a local repository of extracted knowledge from Purview documentation, customer engagements, and third-party sources without committing entire copyrighted PDFs to GitHub.

### Structure

```
/docs/knowledge/
  /extracts/
    /product-documentation/
      /dlp/
        dlp-policy-mechanics-summary.md     # Key points extracted from DLP docs
        dlp-rule-evaluation-flowchart.md
        dlp-common-false-positives.md
      /irm/
        irm-permission-inheritance.md
        irm-template-best-practices.md
      /dspm/
      /mip/
      /audit/
    /customer-learnings/
      /industry-financial-services/
        label-adoption-barriers.md
        common-dlp-exceptions-needed.md
      /industry-healthcare/
        hipaa-control-mapping.md
        phi-detection-patterns.md
    /published-research/
      /microsoft-security-blog/
        dlp-false-positive-reduction-study.md
  /source-index.md              # Master index of sources and extraction status
  /extraction-guidelines.md     # How to extract and structure knowledge
```

### Naming Conventions

- File names are kebab-case and descriptive: `label-adoption-barriers.md`, not `labels.md`
- Extracted content is identified by source (e.g., "Extracted from Microsoft Purview DLP documentation, January 2025")
- Each file begins with metadata: source URL, date extracted, extraction method, confidence level (e.g., "direct quote" vs "synthesized")
- Do not include verbatim chapters or large sections from copyrighted content; extract key concepts and fact-check them independently

### Example Extract Format

```markdown
# Extraction: IRM Permission Inheritance Rules

**Source**: Microsoft Purview IRM documentation, Azure Purview Portal
**Date Extracted**: 2025-02-15
**Confidence**: High (verified by multiple customer configurations)
**Extraction Method**: Key-point summary + customer validation

## Key Points

1. IRM permissions follow this evaluation order: [...]
2. "Do Not Allow" is not the same as "Forward Prohibited"
3. Expiration dates on permissions are not enforced in real-time; verification occurs at access time

## Customer Validation

Confirmed in 5+ customer environments; misconceptions documented in customer feedback.

## See Also

- IRM template configuration guide
- Common permission inheritance mistakes
```

---

## Tagging and Naming Conventions

### Prompt Naming

Prompts are named using a consistent pattern to enable discovery and version tracking:

```
[category]-[action]-[version].md

Examples:
- dlp-alert-triage-v2.3.md
- irm-permission-review-v1.1.md
- investigation-summary-v3.0.md
- executive-brief-v1.5.md
```

**Category**: Product domain or use case
- `dlp`, `irm`, `dspm`, `mip`, `audit`
- `investigation`, `reporting`, `compliance`, `operational`
- `demo`, `workshop`, `consulting`

**Action**: What the prompt is intended to accomplish
- `triage`, `enrich`, `summarize`, `recommend`, `correlate`, `analyze`, `explain`

**Version**: Semantic versioning (major.minor)
- Major version increments when prompt structure or input requirements change significantly
- Minor version increments when refinements are made to improve accuracy or add optional parameters

### Playbook Naming

Playbooks are named by their primary use case and include a version number and target persona:

```
[use-case]-playbook-[target-persona]-v[version].md

Examples:
- dlp-false-positive-analysis-analyst-v2.1.md
- irm-permission-remediation-admin-v1.0.md
- incident-investigation-csa-v3.2.md
- compliance-audit-response-analyst-v2.0.md
```

### Tags and Metadata

All prompt and playbook files include a metadata block at the top:

```yaml
---
title: Alert Triage for DLP Incidents
version: 2.3
category: DLP
action: Triage
target-personas:
  - SOC Analyst
  - Security Admin
use-cases:
  - UC-DLP-001
  - UC-DLP-002
success-rate: 0.92
last-validated: 2025-02-14
requires-copilot-plugins:
  - Purview Connector
  - Audit.Exchange Online
known-limitations:
  - High false positive rate with <10 alerts
  - May miss context if user history unavailable
status: Active
---
```

### Topic Tags

Use consistent tags to enable cross-reference and discovery:

**Product Tags**: `#dlp`, `#irm`, `#dspm`, `#mip`, `#audit`

**Activity Tags**: `#triage`, `#investigation`, `#reporting`, `#tuning`, `#compliance`, `#enablement`

**Persona Tags**: `#analyst`, `#admin`, `#csa`, `#compliance`, `#leadership`

**Quality Tags**: `#high-confidence`, `#tested`, `#experimental`, `#needs-validation`, `#deprecated`

---

## Method for Extracting Operational Knowledge from PDFs

### Overview

As the team works with customers and learns from real-world Purview deployments, knowledge should be captured in structured form and added to the repository. This process prevents knowledge loss and enables the broader team to benefit from frontline experience.

### Extraction Process

#### Step 1: Identify Knowledge to Extract

When working with customer PDFs, design documents, or learning materials, flag content that:

- Contradicts documented Purview behavior or CSA assumptions
- Reveals common customer mistakes or misconceptions
- Demonstrates effective tuning or configuration strategies
- Identifies gaps in public documentation
- Shows patterns in alert data or false positive causes

**Flag Process**: Create a brief note (5-10 lines) identifying:
- What you learned
- Why it matters
- Source document (keep confidential if needed; extract only the principle, not customer details)
- Confidence level (direct evidence vs. observed pattern)

#### Step 2: Synthesize into Structured Knowledge

Extract and synthesize the learning into a knowledge document (not a verbatim copy):

1. **Identify the Core Principle**: What is the essential finding? (E.g., "Label consolidation to 8-10 labels increases adoption vs. 15+ labels")
2. **Provide Evidence**: What is the evidence? (Customer feedback, customer data analysis, Microsoft documentation, etc.)
3. **Add Context**: When does this apply? Under what conditions is this knowledge valid?
4. **Suggest Remediation**: If it's a problem, what is the recommended solution?
5. **Connect to Prompts**: Which prompts might help operationalize this knowledge?

**Example Structure**:

```markdown
# Learning: Label Consolidation Improves Adoption

**Confidence Level**: High (observed in 8+ customer deployments)

## The Learning

Organizations with 8-10 core labels achieve 60%+ user adoption within 3 months.
Organizations with 15+ labels plateau at 30% adoption after 6 months.

## Evidence

- Customer A: Reduced from 18 labels to 8; adoption increased from 28% to 64%
- Customer B: Started with 10 labels; achieved 58% adoption in 2 months
- Customer C: 22 labels; adoption stalled at 22% despite 6-month rollout

## Why It Matters

Label adoption directly affects DLP and IRM effectiveness. Low adoption means
policies protect only a fraction of sensitive data.

## Recommendations

- Conduct label audit; consolidate overlapping categories
- Retain only labels with distinct business or compliance value
- Test with small user cohort before org-wide rollout

## Operationalization

- Use prompt "label-coverage-analysis-v2.1" to identify consolidation opportunities
- Use playbook "label-rollout-planning-admin-v1.0" for phased adoption
```

#### Step 3: Add to Repository

1. Determine location: Is this product knowledge (`/docs/reference/`) or operational insight (`/docs/knowledge/`)?
2. Create or update the relevant file
3. Add metadata and tags
4. Link to related prompts and playbooks
5. Update source index if extracting from external sources

#### Step 4: Validate and Version

1. Have another team member review for accuracy and clarity (peer review)
2. Validate against product documentation or other customers where possible
3. If it contradicts previous guidance, document the change and rationale
4. Increment version of related prompts if needed

### Tool Support

When extracting from long PDFs or unstructured documents:

1. Use Claude to summarize chapters or sections (e.g., "Summarize the key points of DLP policy evaluation order in 5 bullet points")
2. Use Claude to extract structured data from documents (e.g., "Extract the name, purpose, and threat level of each sensitivity label from the DLP policy guide")
3. Use Claude to identify contradictions between sources (e.g., "Compare the DLP rule evaluation order described in Microsoft documentation vs. this customer's experience; identify any contradictions")
4. Store extraction queries and results in a companion document for future reference

---

## Versioning Approach for Prompts, Playbooks, and Architectures

### Semantic Versioning

Use semantic versioning (major.minor.patch) for all prompt and playbook content:

**Major Version (X.0.0)**: Increment when:
- Prompt structure or input parameters change significantly
- Output format changes in a way that breaks downstream processing
- New Copilot plugins are required
- The target use case or persona changes

**Minor Version (1.X.0)**: Increment when:
- Refinements improve accuracy or reduce hallucination
- New optional parameters are added
- Known limitations are resolved
- Guidance is clarified or expanded

**Patch Version (1.1.X)**: Increment when:
- Typos or grammar errors are corrected
- Examples are updated
- Metadata is refined
- Links are fixed

### Versioning in Practice

**File Naming**: Include version in filename
```
dlp-alert-triage-v2.3.md  # version 2.3
```

**File Content**: Include version and changelog
```yaml
---
title: Alert Triage for DLP Incidents
version: 2.3
previous-version: 2.2
changelog:
  - "v2.3: Added guidance for handling multi-recipient alerts"
  - "v2.2: Fixed false positive heuristic for internal email domains"
  - "v2.1: Initial release"
---
```

**Git Commits**: Reference version in commit messages
```
git commit -m "Prompt: dlp-alert-triage v2.3 - improve multi-recipient handling"
```

### Backward Compatibility

When versioning prompts:

- **Major version changes**: Old version becomes deprecated but is not deleted; document why new version is recommended
- **Minor and patch changes**: No backward compatibility concerns; new version supersedes old

Example deprecation notice:

```markdown
## Deprecated: dlp-alert-triage-v1.x

This version is no longer maintained. Use dlp-alert-triage-v2.3 instead.

**Why**: Version 2.0 added support for multi-recipient alerts and significantly
improved accuracy on cross-domain false positives. Upgrade is recommended.
```

### Tracking Success Rate and Confidence

For each prompt or playbook, maintain a quality metric:

```yaml
---
success-rate: 0.92           # Percentage of times output was accurate/useful
confidence-level: High       # High / Medium / Low
last-validated: 2025-02-14   # When this metric was last verified
test-cases: 47               # Number of times this prompt was tested
---
```

Update these metrics monthly based on community feedback and testing.

---

## GitHub Repository Maintenance Guidelines

### Repository Structure

```
purview-security-copilot/
  /docs                    # Documentation and knowledge base
  /prompts                 # Prompt templates (may be symlink to /docs/prompts)
  /playbooks               # Playbook templates (may be symlink to /docs/playbooks)
  /examples                # Example scenarios, customer case studies
  /assets                  # Diagrams, flowcharts, imagery
  /scripts                 # Python/PowerShell helpers for prompt execution
  README.md                # Navigation and quick start
  CONTRIBUTING.md          # Contribution guidelines
  LICENSE                  # MIT or equivalent
  .gitignore               # Exclude large PDFs, customer data, secrets
```

### Contribution Guidelines

**Before Contributing**:

1. Review CONTRIBUTING.md and existing prompts/playbooks in similar category
2. Test your prompt or playbook with real scenarios (or documented examples)
3. Validate that it adds value and isn't a duplicate of existing content

**Creating a New Prompt**:

1. Choose appropriate category and version (e.g., `dlp-policy-tuning-v1.0.md`)
2. Include metadata block with title, version, target personas, use cases
3. Provide clear input instructions and expected outputs
4. Include at least one worked example
5. Document known limitations
6. Add success rate (start at "untested") and update after community validation

**Creating a New Playbook**:

1. Choose title reflecting use case and target persona (e.g., `dlp-false-positive-analysis-analyst-v1.0.md`)
2. Include step-by-step workflow with decision points
3. Reference specific prompts at each step
4. Provide realistic scenario
5. Include decision tree or flowchart where helpful

**Submitting a Pull Request**:

1. Branch from `main` with descriptive name (e.g., `prompt/dlp-policy-tuning-v1`)
2. Include updated content and metadata
3. Peer review by at least one other CSA or contributor
4. Update version numbers and changelog
5. Merge to `main` with squashed commit

### Maintenance Cadence

**Weekly**:
- Review and respond to issues and pull requests
- Log observations from customer interactions (candidate knowledge extractions)

**Monthly**:
- Validate prompt success rates and confidence levels based on community feedback
- Update known limitations in active prompts
- Increment version numbers for prompts with significant refinements
- Review deprecated prompts; archive if no longer relevant

**Quarterly**:
- Quarterly review of product documentation for changes to DLP, IRM, audit, etc.
- Extract new operational knowledge from customer learnings
- Assess repo structure and organization; refactor if needed
- Publish summary of updates and new content to CSA community

**Annually**:
- Full audit of all prompts and playbooks; archive deprecated or low-confidence content
- Assess whether prompts still align with latest Copilot capabilities and plugins
- Update vision and scope if significant changes to Copilot or Purview occur
- Plan next year's focus areas and priority use cases

### Issue Tracking

Issues should be tagged to enable prioritization:

**Issue Types**:
- `bug`: Prompt gives incorrect output or known limitation occurs unexpectedly
- `enhancement`: Refinement to existing prompt or playbook
- `new-content`: Request for new prompt or playbook
- `knowledge-gap`: Observation that documentation is missing or unclear

**Priority Tags**:
- `p0-critical`: Affects multiple customers or is critical to success
- `p1-high`: Affects several customers; improves quality or efficiency significantly
- `p2-medium`: Nice-to-have; moderate impact
- `p3-low`: Cosmetic or low priority

**Status Tags**:
- `waiting-for-review`: PR submitted, awaiting review
- `waiting-for-testing`: Waiting for community validation
- `in-progress`: Actively being worked on
- `blocked`: Blocked by another issue or decision

---

## Knowledge Refresh Cadence

### What Needs Frequent Refresh

**Copilot Behavior and Constraints** (monthly)
- Test active prompts to verify they still produce expected outputs
- Document any changes to Copilot's behavior or output format
- Update success rates and known limitations
- Test new Copilot features or plugins as they become available

**Operational Insights** (monthly)
- Synthesize learnings from customer interactions
- Add new patterns observed in alert data or false positives
- Update recommendations based on latest tuning advice

**Product Capabilities** (quarterly)
- Review Purview release notes for feature changes
- Update reference documentation for new DLP rule types, label features, audit events
- Document any changes to API schema or data model

### What Can Be Stable

**Strategic Vision and Scope** (annually)
- Review annually unless there is a major change to Copilot or Purview
- Update only if core business context or target use cases change

**Personas and Use Cases** (annually)
- Review annually to confirm they still reflect customer needs
- Update if new roles emerge or if priorities shift

**Knowledge Architecture and Processes** (as-needed)
- Review if repo structure becomes unwieldy or if new content types emerge
- Update contribution guidelines based on lessons learned

### Refresh Process

1. **Create a Refresh Ticket**: Start of each month (for monthly content) or quarter (for quarterly)
2. **Validate Active Content**: Test prompts, verify constraints, check for new Copilot features
3. **Document Changes**: Update version numbers, changelogs, and timestamps
4. **Capture New Learnings**: Extract and synthesize new operational knowledge
5. **Update Dependencies**: If a prompt changes significantly, update playbooks that reference it
6. **Communicate Updates**: Monthly summary to CSA community of what changed and why

---

## Summary

This knowledge architecture balances the need for stable, discoverable reference material with the reality that Copilot behavior and operational insights evolve frequently. By separating stable knowledge (product models, organizational data) from fast-changing knowledge (prompt effectiveness, operational patterns), the repository can remain useful and credible even as tools and tactics evolve.

The key to success is **discipline in versioning, tagging, and maintenance**. Without these practices, the repository will quickly become a collection of outdated prompts with no clear sense of what works and what does not.

