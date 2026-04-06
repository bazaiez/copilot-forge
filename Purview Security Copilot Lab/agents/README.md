# Multi-Agent Architecture for Security Copilot & Purview Data Security

## Overview

This directory contains the design and implementation guidance for a **multi-agent system** built on Claude AI for investigating Microsoft Purview Data Security incidents. The architecture uses specialized agents coordinated by an orchestrator to handle complex DLP alerts, insider risk cases, and compliance investigations.

## Why Multi-Agent?

Purview Data Security investigations span multiple investigation domains:
- **DLP investigations**: Policy analysis, false positive detection, data flow mapping
- **Insider Risk Management**: Behavioral analysis, risk scoring, context evaluation
- **Audit log correlation**: Timeline reconstruction, activity attribution, pattern detection
- **Knowledge synthesis**: Retrieving best practices, configuration guidance, compliance mapping
- **Reporting**: Translating findings into executive summaries or detailed incident reports

A single agent becomes bottlenecked trying to be expert at all of these. **Multi-agent design** enables:
- **Specialization**: Each agent masters one domain
- **Parallelization**: Multiple investigations can run concurrently
- **Quality control**: Dedicated review agent validates findings
- **Auditability**: Clear decision trails for each component

## What's in This Directory

| File | Purpose |
|------|---------|
| `architecture.md` | Complete multi-agent design document with agent definitions, topologies, and implementation guide |
| `examples/` | Concrete scenario walkthroughs showing agent interactions |
| `topologies/` | Predefined 3-agent, 5-agent, and 6-agent deployment patterns |

## Quick Start

1. **Read `architecture.md`** for the complete design (includes agent definitions, topologies, and Claude implementation guide)
2. **Choose a topology** based on your use case:
   - **3-agent (Simple)**: Quick prompt generation (Orchestrator + Knowledge + Prompt Engineer + Quality Reviewer)
   - **5-agent (Standard)**: Production prompt generation with playbook design
   - **6-agent (Full)**: Enterprise deployments with customer workshop support
3. **Review `examples/`** for concrete scenario walkthroughs
4. **Deploy with Claude Code** for single-file agents or **Claude Agent SDK** for microservices

## Key Principles

- **Clear boundaries**: Each agent owns specific decision types
- **Human control**: Final decisions on severity, escalation, and policy changes stay with humans
- **Validation**: Quality agent reviews all high-stakes outputs before delivery
- **Auditability**: All handoffs are documented with reasoning and confidence levels
- **Extensibility**: New agents can be added without redesigning the core system

## Implementation Paths

### Claude Code
Simple, monolithic implementation in VS Code. Each agent is a Claude Code instance with a system prompt. Good for small teams, rapid prototyping.

### Claude Agent SDK
Production-grade microservices architecture. Each agent is an independent process with structured communication. Good for large-scale investigations, API integrations, SLAs.

### Hybrid (Claude Desktop + MCP)
Claude Desktop with local MCP servers for Purview data access. Ideal for investigators who want AI assistance within their normal workflow.

## Human-in-the-Loop Guardrails

This system enforces human approval on:
- **Alert dismissal** (cannot auto-close DLP/IRM alerts)
- **Case severity classification** (humans set final severity)
- **Policy changes** (investigation may recommend policy, humans approve)
- **External communications** (reports reviewed before distribution)
- **Legal/HR escalations** (flagged but not auto-escalated)

See `architecture.md` Section 6 for the complete control boundary table.

## Prompt Books & Knowledge Bases

Agents reference **prompt books** (collections of proven prompts and patterns) for:
- **DLP investigations**: Alert anatomy, policy intent, false positive patterns
- **IRM investigations**: Case triage, behavioral indicators, risk scoring
- **Audit analysis**: Query optimization, timeline reconstruction, attribution logic

These prompt books are living documents—improve them as you encounter new patterns.

## Next Steps

1. Read the full `architecture.md` document
2. Review the system prompt examples for your target topology
3. Start with the 3-agent topology for validation
4. Add specialized agents as you scale
5. Establish feedback loops to improve prompts over time

---

**Version**: 1.0
**Last Updated**: April 2026
**Maintained By**: Security Copilot Champions (CSA Team)
**Claude Models**: Claude 3.5 Sonnet and above
**Status**: Production-Ready
