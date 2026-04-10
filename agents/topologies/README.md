# Agent Topologies

Predefined deployment patterns for different use cases and team sizes.

## Available Topologies

### Starter (3 Agents)
**Best for:** Quick prompt generation, single CSA, proof-of-concept

**Agents:** Orchestrator + Knowledge Agent + Prompt Generator Agent

**Use when:**
- Single CSA generating prompts for a customer engagement
- Quick capability checks and prompt creation
- Internal tooling and rapid iteration

### Standard (5 Agents)
**Best for:** Production customer engagements, investigation workflows

**Agents:** Orchestrator + Knowledge + Prompt Generator + Investigation + Quality Reviewer

**Use when:**
- Active incident investigation with SC prompts
- Customer deliverables that need quality validation
- Multi-step workflows spanning triage, investigation, and reporting

### Full (6 Agents)
**Best for:** Enterprise deployments, customer workshops, complete content packages

**Agents:** All 6 — Orchestrator + Knowledge + Prompt Generator + Investigation + Demo Architect + Quality Reviewer

**Use when:**
- Preparing customer workshops and demos
- Large CSA teams building content libraries
- Complex scenarios requiring investigation + demo + reporting
- Customer advisory boards and enablement programs

---

## Agent Files

| Agent | File |
|---|---|
| Orchestrator | [orchestrator-agent.md](../orchestrator-agent.md) |
| Knowledge | [knowledge-agent.md](../knowledge-agent.md) |
| Prompt Generator | [prompt-generator-agent.md](../prompt-generator-agent.md) |
| Investigation | [investigation-agent.md](../investigation-agent.md) |
| Demo Architect | [demo-architect-agent.md](../demo-architect-agent.md) |
| Quality Reviewer | [quality-reviewer-agent.md](../quality-reviewer-agent.md) |

---

**Full architecture details:** See [architecture.md](../architecture.md) for the complete design document.
