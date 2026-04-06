# Agent Topologies

This directory contains predefined multi-agent deployment patterns for different use cases and team sizes.

## Available Topologies

### Simple (3 Agents)
**Best for:** Quick prompt generation, internal CSA development, proof-of-concept

**Agents:** Orchestrator + Purview Knowledge + SC Prompt Engineer + Quality Reviewer

**Use when:**
- Single CSA working on prompt generation
- Internal tooling and rapid iteration
- Validating the multi-agent approach

### Standard (5 Agents)
**Best for:** Production prompt generation, customer workshops, playbook design

**Agents:** Orchestrator + Purview Knowledge + SC Prompt Engineer + Investigation Playbook + Quality Reviewer

**Use when:**
- Multi-CSA teams building customer deliverables
- Production deployments requiring structured playbooks
- Large customer engagements with multiple investigation types

### Full (6 Agents)
**Best for:** Enterprise deployments, high-volume customer enablement, content library building

**Agents:** All Standard agents + Customer Workshop Agent

**Use when:**
- Large CSA organizations running frequent customer workshops
- Building and maintaining a content marketplace
- Customer advisory boards and enablement programs

---

**Full details:** See [architecture.md](../architecture.md) Section 3 for complete topology diagrams and agent responsibilities.
