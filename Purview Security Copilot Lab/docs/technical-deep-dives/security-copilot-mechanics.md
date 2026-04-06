# Security Copilot Mechanics

**Last Updated:** April 2026
**Status:** [VALIDATED]

---

## Overview

Security Copilot (SC) is an AI-powered security assistant that processes natural language prompts to query, analyze, and summarize security data. This document covers the core mechanics of how SC processes prompts and interacts with the Purview plugin.

## Context and Session Management

### Session Context
- SC maintains context within a single session (conversation thread)
- Each new session starts without prior context
- Prompts in a session build on each other — later prompts can reference earlier outputs
- Context window is finite; long sessions may lose early context

### Plugin-Based Architecture
- SC uses plugins to connect to data sources
- The **Purview plugin** provides 6 system capabilities:
  1. **Get Data Risk Summary** — MIP + DLP risk attributes for data
  2. **Get User Risk Summary** — IRM user activities, exfiltration indicators, sequences
  3. **Summarize Purview Alert** — DLP and IRM alert details with context
  4. **Triage Purview Alerts** — Top/recent alerts ranked by risk
  5. **Zoom Into Purview Data Risk** — Detailed MIP/DLP attributes
  6. **Zoom Into Purview User Risk** — Activities, anomalies, behavioral patterns
- Plugins are enabled per-tenant in the Security Copilot admin center

## Prompt Processing

### How SC Interprets Prompts
1. SC receives a natural language prompt
2. It determines which plugin capability matches the intent
3. It calls the corresponding system capability with extracted parameters
4. Results are formatted into a natural language response
5. The response is added to session context for follow-up prompts

### Parameter Syntax
- Parameters use `<variable>` format (e.g., `<alertId>`, `<user>`, `<timeframe>`)
- SC infers parameters from prompt context when possible
- Explicit parameters produce more reliable results

### Prompt Patterns That Work
- **Question format:** "Show me...", "What is...", "List...", "Summarize..."
- **Chaining:** "Based on the above, show me..." (references prior output)
- **Comparison:** "Compare the risk of User A vs. User B"
- **Filtering:** "Show only high severity alerts from the past 24 hours"

## Custom Agents

SC supports custom agents through multiple creation methods:
- **Agent Builder** — Visual UI in Security Copilot admin center
- **YAML Manifest** — Declarative agent definition file
- **NL2Agent** — Natural language description converted to agent
- **MCP (Model Context Protocol)** — External tool integration

### Deployed Purview Agents (GA)
- **DLP Triage Agent** — Auto-categorizes active mode DLP alerts
- **IRM Triage Agent** — Auto-categorizes IRM alerts
- **DSPM Posture Agent** — Natural language data discovery queries
- **Data Security Investigations (DSI) Agent** — Credential scanning

## Promptbooks

Promptbooks are sequential multi-prompt workflows:
- Each step is a prompt that builds on previous outputs
- Steps can include `<parameter>` inputs from the user
- Promptbooks can be shared across the organization
- Output from one step feeds into the next automatically

## SCU (Security Copilot Units)

- Each prompt/agent call consumes SCUs
- SCU consumption varies by operation complexity
- Organizations must provision SCU capacity in their tenant
- Plan SCU budget based on expected prompt volume

## Limitations

- SC cannot access file contents — only metadata and classifications
- Audit log data may lag 24-48 hours
- IRM data sharing must be enabled for cross-signal correlation
- SC cannot take remediation actions directly — all actions are recommendations
- Custom SITs (Sensitive Information Types) may not be fully interpreted
- DLP Triage Agent requires active mode policies (not simulation mode)

---

**See also:** [Purview.md](Purview.md) for Purview-specific data flows and signal generation.
