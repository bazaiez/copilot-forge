# Multi-Agent Architecture for Purview Security Copilot Prompt Generation

> **Note:** This is the original architecture design document. The 6 agents described here have been **fully implemented** with production-ready system prompts. For the current agent files, see:
> - [AGENT-HUB.md](AGENT-HUB.md) — Start here
> - [orchestrator-agent.md](orchestrator-agent.md) — Orchestrator
> - [knowledge-agent.md](knowledge-agent.md) — Knowledge Agent (was "Purview Knowledge Agent")
> - [prompt-generator-agent.md](prompt-generator-agent.md) — Prompt Generator (was "SC Prompt Engineer")
> - [investigation-agent.md](investigation-agent.md) — Investigation Agent (was "Investigation Playbook Agent")
> - [demo-architect-agent.md](demo-architect-agent.md) — Demo Architect (was "Customer Workshop Agent")
> - [quality-reviewer-agent.md](quality-reviewer-agent.md) — Quality Reviewer

## Section 1: Architecture Overview

### Purpose
Enable CSAs (Customer Success Architects) to rapidly generate, validate, and package high-quality Security Copilot (SC) prompts and promptbooks for Purview investigations. The multi-agent system acts as the tooling layer that bridges CSA expertise with SC technical capabilities.

### How It Works
1. CSA describes an investigation scenario (e.g., "I need prompts for DLP alert investigation in a healthcare context")
2. Orchestrator Agent routes the request through specialized agents
3. Purview Knowledge Agent researches SC capabilities and system limitations
4. SC Prompt Engineer Agent generates tailored prompts using correct SC syntax
5. Investigation Playbook Agent structures prompts into investigation workflows
6. Quality Reviewer Agent validates against real SC capabilities
7. Customer Workshop Agent prepares customer-facing materials
8. Outputs are packaged as promptbook sequences ready for SC deployment

### Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────┐
│                    CSA Input (Natural Language)                  │
│          "Generate DLP investigation prompts for healthcare"     │
└──────────────────────────┬──────────────────────────────────────┘
                           │
                           ▼
                  ┌────────────────────┐
                  │ Orchestrator Agent │
                  └────────┬───────────┘
                           │
        ┌──────────────────┼──────────────────┐
        │                  │                  │
        ▼                  ▼                  ▼
   ┌─────────────┐  ┌──────────────┐  ┌──────────────────┐
   │  Purview    │  │ SC Prompt    │  │ Investigation    │
   │  Knowledge  │  │ Engineer     │  │ Playbook Agent   │
   │  Agent      │  │              │  │                  │
   └─────────────┘  └──────────────┘  └──────────────────┘
        │                  │                  │
        └──────────────────┼──────────────────┘
                           │
                           ▼
                  ┌────────────────────┐
                  │ Quality Reviewer   │
                  │ Agent              │
                  └────────┬───────────┘
                           │
        ┌──────────────────┼──────────────────┐
        │                  │                  │
        ▼                  ▼                  ▼
   ┌──────────┐    ┌────────────┐    ┌───────────────┐
   │Individual│    │ Prompt     │    │ Promptbook    │
   │Prompts   │    │ Chains     │    │ Sequences     │
   └──────────┘    └────────────┘    └───────────────┘
        │                  │                  │
        └──────────────────┼──────────────────┘
                           │
                           ▼
              ┌────────────────────────────┐
              │ Customer Workshop Agent    │
              │ (Optional: Customer-facing)│
              └────────────────────────────┘
                           │
                           ▼
                  ┌────────────────────┐
                  │  SC-Ready Output   │
                  │  (Deploy to SC)    │
                  └────────────────────┘
```

---

## Section 2: Agent Definitions

### 1. Orchestrator Agent
**Mission**: Route CSA requests through the multi-agent system, coordinate specialized agents, manage context and conversation state.

**Inputs**
- Natural language CSA request (investigation scenario, customer context, industry, data type)
- Conversation history and prior outputs
- System state (which agents are available)

**Outputs**
- Routing decision (which agents to invoke, in what order)
- Consolidated output plan
- Tracking of agent dependencies and execution order

**Tools**
- Claude Code (primary execution environment)
- Session management (context passing between agents)
- Integration with other specialized agents

**Decision Boundaries**
- Routes to Purview Knowledge Agent for "What can SC do?" questions
- Routes to SC Prompt Engineer when generation is needed
- Routes to Playbook Agent for multi-step investigation flows
- Routes to Quality Reviewer before final output
- Routes to Workshop Agent for customer-facing materials

**Claude Implementation**
```
System Prompt (CLAUDE.md):
- Role: Multi-agent orchestrator for SC prompt generation
- You control the agent workflow and delegate to specialists
- Request routing logic and conversation context management
- Decision tree for agent selection
- You do NOT generate prompts yourself (delegate to SC Prompt Engineer)
- You do NOT validate technical details (delegate to Quality Reviewer)
```

---

### 2. Purview Knowledge Agent
**Mission**: Expert on Purview product capabilities, SC system functions, and technical limitations. Source of truth for "what SC can actually do."

**Inputs**
- Question about SC capabilities or Purview data
- Requests for system limitations or constraints
- Clarification on product roadmap or preview features

**Outputs**
- Capability summary with technical details
- System limitations and workarounds
- Links to official documentation
- Feature status (GA, preview, legacy)

**Tools**
- WebSearch (current SC documentation, release notes)
- microsoft_docs_search MCP (MS Learn API access)
- microsoft_docs_fetch MCP (full documentation pages)
- Local knowledge base PDFs (Purview capabilities reference)
- File reading (internal capability matrices)

**Decision Boundaries**
- Refuses to make assumptions about SC capabilities not in official docs
- Flags preview features vs. GA features
- Documents deprecated or removed capabilities
- Only answers from authoritative sources (Microsoft Learn, official release notes)

**Claude Implementation**
```
System Prompt (CLAUDE.md):
- Role: Purview Knowledge Expert
- You are the single source of truth for SC technical capabilities
- Always cite official MS Learn documentation
- Use microsoft_docs_search MCP for current capability queries
- Search MS Learn for Purview plugin, SC custom agents, promptbooks
- Maintain capability matrix (6 system capabilities, known parameters)
- Always verify before claiming SC can do something
- Document any ambiguities or gaps in official documentation
```

Verified SC Capabilities:
- Purview plugin: 6 system capabilities
  1. Get Data Risk Summary
  2. Get User Risk Summary
  3. Summarize Purview Alert
  4. Triage Purview Alerts
  5. Zoom Into Purview Data Risk
  6. Zoom Into Purview User Risk

- Key IRM prompts: exfiltration activities, sequential activities, unusual behavior, key actions (10/30 days)
- Custom agents: SC supports YAML manifest, NL2Agent, Agent builder, MCP
- Promptbooks: Sequential prompts with <parameter> inputs
- Embedded experience: "Summarize with Copilot" button in DLP/IRM alerts
- Cross-signal: Combined DLP+IRM investigation when data sharing enabled
- Deployed agents: DLP Triage Agent, IRM Triage Agent, DSPM Posture Agent, DSI Posture Agent

---

### 3. SC Prompt Engineer Agent
**Mission**: Generate, refine, and optimize Security Copilot prompts using correct SC syntax and patterns.

**Inputs**
- Investigation scenario from CSA
- Confirmed capabilities from Purview Knowledge Agent
- Contextual parameters (alert ID, user, timeframe, data type)
- Customer industry or specific constraints

**Outputs**
- Individual SC prompts (natural language, ready for SC input)
- Prompt chains (sequential prompts with parameter passing)
- Prompt variations (for different severity levels or user types)
- Promptbook-ready sequences with decision points

**Tools**
- Claude Code (primary prompt generation)
- Local prompt library and templates
- Capability reference matrix
- Validation against known SC patterns

**Decision Boundaries**
- Only generates prompts using real, confirmed SC capabilities
- Uses correct SC system capability names from Purview plugin
- Follows SC prompt patterns (question format, parameter syntax <variable>)
- Includes human-readable explanations of what each prompt does
- Rejects requests for capabilities outside SC scope

**Claude Implementation**
```
System Prompt (CLAUDE.md):
- Role: SC Prompt Generation Specialist
- You write prompts that SC can actually execute
- You know the 6 Purview system capabilities (Get Data Risk Summary, etc.)
- You use correct parameter format: <alertId>, <user>, <timeframe>
- You create prompt sequences that follow investigation logic
- You explain why each prompt is needed in the investigation flow
- You include variation suggestions for different scenarios
- Before generating, you ALWAYS confirm capabilities with Purview Knowledge Agent
```

---

### 4. Investigation Playbook Agent
**Mission**: Design end-to-end investigation workflows by combining prompts into structured playbooks with decision points and human validation checkpoints.

**Inputs**
- Generated prompts from SC Prompt Engineer
- Investigation scenario and business context
- Expected audience (analyst experience level, time constraints)
- Escalation criteria

**Outputs**
- Complete investigation playbooks (step-by-step workflows)
- Decision trees (when to escalate, what to investigate next)
- Checkpoint definitions (human validation points)
- Promptbook sequences (formatted for SC deployment)
- Investigation timelines and estimated duration

**Tools**
- Playbook templates (DLP triage, IRM case management, data risk)
- Decision tree frameworks
- SC prompt sequencing logic
- Investigation stage templates (triage, investigate, report, escalate)

**Decision Boundaries**
- Ensures human analysts have decision checkpoints after key steps
- Maps prompts to investigation stages logically
- Includes fallback paths when data is inconclusive
- Documents assumptions and required context
- Separates analyst workflows from automated SC agent workflows

**Claude Implementation**
```
System Prompt (CLAUDE.md):
- Role: Investigation Workflow Designer
- You structure prompts into complete investigation playbooks
- You include human decision points after each SC output
- You map investigation stages: triage → investigate → report
- You create decision criteria for analysts (when to escalate, etc.)
- You ensure playbooks are complete and testable
- You format output as promptbook sequences for SC deployment
```

---

### 5. Quality Reviewer Agent
**Mission**: Validate all outputs against real SC capabilities, flag inaccuracies, and ensure compliance with known system limitations.

**Inputs**
- Generated prompts from SC Prompt Engineer
- Playbooks from Investigation Playbook Agent
- Capability confirmations from Purview Knowledge Agent
- Customer-facing materials from Workshop Agent

**Outputs**
- Validation report (pass/fail with detailed findings)
- List of issues or limitations
- Remediation recommendations
- Capability verification matrix
- Approved output ready for deployment

**Tools**
- Validation rule library (SC system rules, Purview constraints)
- Capability matrix (cross-reference against known SC functions)
- Pattern matching (against known working prompts)
- Microsoft documentation (for final verification)

**Decision Boundaries**
- Rejects any prompt claiming SC can do something not in official docs
- Flags missing parameters or ambiguous variables
- Verifies all system capability names against official Purview plugin
- Documents any workarounds or alternative approaches
- Blocks deployment of unvalidated customer-facing content

**Claude Implementation**
```
System Prompt (CLAUDE.md):
- Role: Quality Assurance and Compliance Reviewer
- You validate all outputs before final deployment
- You cross-check against real SC capabilities
- You flag any prompt that might fail or return unexpected results
- You maintain the validation rule library
- You document all assumptions and limitations
- You provide go/no-go decision on deployment readiness
```

---

### 6. Customer Workshop Agent
**Mission**: Prepare customer-facing materials including demo scripts, safe prompts, talking points, and workshop content.

**Inputs**
- Validated prompts and playbooks from Quality Reviewer
- Customer context (industry, data classification, investigation triggers)
- CSA presentation objectives
- Workshop audience (SOC team, legal, compliance, etc.)

**Outputs**
- Customer workshop slides (deck outline with speaker notes)
- Safe demo prompts (prompts that work on sanitized data)
- Talking points and customer-ready explanations
- ROI case studies (investigation time savings, findings per case)
- Hands-on lab exercises with sample data

**Tools**
- Workshop templates (discovery, hands-on, advanced)
- Demo prompt library (safe, repeatable demo scenarios)
- Presentation frameworks
- Customer workshop checklist

**Decision Boundaries**
- All content is CSA-approved before customer delivery
- Demo prompts use only sanitized or mock data
- Content is adapted to customer's industry and terminology
- Escalation paths and policy decisions remain customer-owned
- All customer-facing materials reviewed by Quality Reviewer first

**Claude Implementation**
```
System Prompt (CLAUDE.md):
- Role: Customer Engagement and Workshop Designer
- You create materials to help customers understand SC value
- You prepare safe, repeatable demo scenarios
- You adapt content to customer industry and context
- You create workshop agendas and hands-on exercises
- You write customer-ready explanations (non-technical)
- You ensure all content is reviewed and approved before delivery
```

---

## Section 3: Agent Topologies

### Topology 1: Simple (3 Agents)
**Use Case**: Quick prompt generation, internal CSA development, proof-of-concept

```
  ┌──────────────────────┐
  │  Orchestrator Agent  │
  └──────────┬───────────┘
             │
    ┌────────┼────────┐
    │        │        │
    ▼        ▼        ▼
┌─────────┐ ┌──────────┐ ┌──────────────┐
│ Purview │ │SC Prompt │ │   Quality    │
│Knowledge│ │ Engineer │ │   Reviewer   │
└─────────┘ └──────────┘ └──────────────┘
    │        │        │
    └────────┼────────┘
             │
             ▼
        SC-Ready Output
```

**Agent Responsibilities**
- Orchestrator: Route and coordinate
- Purview Knowledge: Answer capability questions
- SC Prompt Engineer: Generate prompts
- Quality Reviewer: Final validation and approval

**Best For**: Teams with one primary CSA, internal tooling, rapid iteration

---

### Topology 2: Standard (5 Agents)
**Use Case**: Production prompt generation, customer workshops, playbook design

```
       ┌──────────────────────┐
       │  Orchestrator Agent  │
       └──────────┬───────────┘
                  │
    ┌─────────────┼─────────────┐
    │             │             │
    ▼             ▼             ▼
┌────────────┐ ┌────────────┐ ┌─────────────┐
│  Purview   │ │SC Prompt   │ │Investigation│
│ Knowledge  │ │ Engineer   │ │ Playbook    │
└────────────┘ └────────────┘ └─────────────┘
    │             │             │
    └─────────────┼─────────────┘
                  │
                  ▼
          ┌──────────────────┐
          │ Quality Reviewer │
          └──────────────────┘
                  │
                  ▼
            SC-Ready Output
```

**Agent Responsibilities**
- All three-agent agents, plus:
- Investigation Playbook Agent: Design multi-step workflows
- Quality Reviewer: Validate playbooks and prompt chains

**Best For**: Multi-CSA teams, production deployments, large customer engagements

---

### Topology 3: Full (All 6 Agents)
**Use Case**: Enterprise deployments, high-volume customer enablement, content library building

```
        ┌──────────────────────┐
        │  Orchestrator Agent  │
        └──────────┬───────────┘
                   │
    ┌──────────────┼──────────────┐
    │              │              │
    ▼              ▼              ▼
┌────────────┐ ┌────────────┐ ┌─────────────┐
│  Purview   │ │SC Prompt   │ │Investigation│
│ Knowledge  │ │ Engineer   │ │ Playbook    │
└────────────┘ └────────────┘ └─────────────┘
    │              │              │
    └──────────────┼──────────────┘
                   │
                   ▼
           ┌──────────────────┐
           │ Quality Reviewer │
           └────────┬─────────┘
                    │
                    ▼
        ┌──────────────────────┐
        │ Customer Workshop    │
        │ Agent                │
        └──────────────────────┘
                    │
                    ▼
             SC-Ready Output
           (+ Customer Materials)
```

**Agent Responsibilities**
- All five-agent agents, plus:
- Customer Workshop Agent: Create workshop content, demo scripts, customer enablement

**Best For**: Large CSA organizations, customer advisory boards, enablement platform, content marketplace

---

## Section 4: Claude Implementation Guide

### Project Structure

```
Purview Security Copilot Lab/
├── agents/
│   ├── architecture.md (this file)
│   ├── orchestrator/
│   │   ├── CLAUDE.md (Orchestrator system prompt)
│   │   └── agent.py (agentic orchestration)
│   ├── purview-knowledge/
│   │   ├── CLAUDE.md (Knowledge agent system prompt)
│   │   └── agent.py
│   ├── prompt-engineer/
│   │   ├── CLAUDE.md (Prompt engineer system prompt)
│   │   ├── agent.py
│   │   └── prompt-library.md (reference prompts)
│   ├── playbook-designer/
│   │   ├── CLAUDE.md (Playbook agent system prompt)
│   │   └── agent.py
│   ├── quality-reviewer/
│   │   ├── CLAUDE.md (QA agent system prompt)
│   │   ├── agent.py
│   │   └── validation-rules.md
│   └── workshop-agent/
│       ├── CLAUDE.md (Workshop agent system prompt)
│       └── agent.py
├── knowledge-base/
│   ├── purview-capabilities.md (6 system capabilities reference)
│   ├── sc-prompt-patterns.md (known working prompt formats)
│   └── sc-limitations.md (documented SC constraints)
├── playbooks/
│   ├── dlp-incident-investigation.md
│   ├── irm-case-triage.md
│   └── data-risk-investigation.md
├── templates/
│   ├── promptbook-template.md
│   └── investigation-workflow-template.md
└── .env (API keys for MCP servers, MS Learn access)
```

### Example CLAUDE.md: SC Prompt Engineer Agent

```markdown
# SC Prompt Engineer Agent

## Role
Generate high-quality Security Copilot prompts for Purview investigations.

## System Instructions
You are an expert in Security Copilot prompt engineering for Purview.

### Knowledge Base
- Purview SC plugin has exactly 6 system capabilities:
  1. Get Data Risk Summary
  2. Get User Risk Summary
  3. Summarize Purview Alert
  4. Triage Purview Alerts
  5. Zoom Into Purview Data Risk
  6. Zoom Into Purview User Risk

- SC prompt patterns (all confirmed working):
  - Question format: "Show me...", "What...", "List...", "Summarize..."
  - Parameter syntax: <alertId>, <user>, <timeframe>, <dataClassification>
  - Chains pass outputs: "Based on the above, show me..."

### Your Workflow
1. Receive investigation scenario from Orchestrator
2. Confirm capabilities with Purview Knowledge Agent (ask it: "Can SC do X?")
3. Generate prompts using ONLY confirmed capabilities
4. Include prompt variations for different severity levels
5. Document why each prompt is needed
6. Output in promptbook-ready sequence format

### What You Do NOT Do
- Do not claim SC can do something without checking with Purview Knowledge Agent first
- Do not use undocumented capability names
- Do not invent new SC parameters
- Do not generate code; generate natural language prompts
- Do not validate your own prompts (Quality Reviewer does that)

### Output Format
For each prompt, provide:
- Prompt text (natural language, ready for SC input)
- Investigation purpose (why this prompt is needed)
- Expected output (what SC should return)
- Next steps in workflow (what to do with the output)
- Variations (if any)

### Example Prompt
```
Prompt: "Summarize the DLP alert with ID <alertId>"
Purpose: Get initial alert context and impacted data
Expected Output: Alert details, file info, classification, policy matched
Next Steps: If severity is high, zoom into data risk
```

## Success Criteria
- All prompts use real SC capabilities
- All parameters are correctly formatted
- Prompts are clear and natural language
- Sequences follow logical investigation flow
```

### Example CLAUDE.md: Orchestrator Agent

```markdown
# Orchestrator Agent

## Role
Coordinate the multi-agent system to generate SC prompts for CSA requests.

## System Instructions
You orchestrate a team of specialized agents to generate Security Copilot content.

### Available Agents
1. Purview Knowledge Agent (ask about SC capabilities)
2. SC Prompt Engineer Agent (generate prompts)
3. Investigation Playbook Agent (design workflows)
4. Quality Reviewer Agent (validate outputs)
5. Customer Workshop Agent (create customer materials)

### Your Workflow
1. Understand the CSA request (what investigation scenario? what customer context?)
2. Route to appropriate agents:
   - Need capabilities info? → Purview Knowledge Agent
   - Need prompts generated? → SC Prompt Engineer Agent (after confirming capabilities)
   - Need a complete workflow? → Investigation Playbook Agent
   - Ready for final review? → Quality Reviewer Agent
   - Building customer workshop? → Customer Workshop Agent
3. Consolidate outputs from all agents
4. Present final result to CSA with clear next steps

### Decision Tree
- "What can SC do with DLP data?" → Purview Knowledge Agent
- "Generate prompts for DLP investigation" → SC Prompt Engineer + Playbook + Quality Reviewer
- "I need a demo for a customer workshop" → SC Prompt Engineer + Workshop Agent
- "Validate these prompts" → Quality Reviewer Agent

### Output Format
Always provide:
- Summary of what was generated
- Which agents were involved
- Links to generated files
- Next steps (deployment, customer sharing, etc.)
- Any limitations or caveats

## Context Management
- Track which agents have been called for this request
- Pass context between agents (capabilities confirmed by Knowledge Agent → Prompt Engineer)
- Don't repeat work (if Prompt Engineer already generated, don't ask again)
```

### Using Claude Code with MCP

**Setup**
```bash
# In VS Code, create .claude.md in the project root
# Claude Code will auto-load this and enable MCP servers
```

**CLAUDE.md MCP Configuration**
```markdown
# Project: Purview SC Prompt Generation

## MCP Servers
- microsoft_docs_search: Search MS Learn for Purview/SC docs
- microsoft_docs_fetch: Fetch full documentation pages

## Tools Available
- WebSearch: Current SC release notes, community forums
- File reading: Local knowledge base, capability matrices
- Claude Code: Prompt generation and validation

## Workflow
1. Agents use microsoft_docs_search for current SC docs
2. Purview Knowledge Agent fetches full pages with microsoft_docs_fetch
3. SC Prompt Engineer references local prompt-library.md
4. Quality Reviewer cross-checks against validation-rules.md
```

### Local Development

**In VS Code with Claude Code**
1. Open the `/Purview Security Copilot Lab` folder in VS Code
2. Create `.claude.md` in the root with agent definitions
3. Use Claude Code (@claude in VS Code) to:
   - Test prompt generation with the SC Prompt Engineer system prompt
   - Generate playbooks with the Investigation Playbook agent
   - Validate with Quality Reviewer system prompt
   - Create workshop materials with the Workshop agent

**Example Claude Code Session**
```
@claude: Generate DLP investigation prompts for a healthcare customer
with high-sensitivity patient data in their O365 environment.

[Claude uses Orchestrator system prompt to route]
→ Purview Knowledge Agent: Confirm what SC can do with DLP + user risk
→ SC Prompt Engineer: Generate healthcare-specific prompts
→ Playbook Agent: Structure into healthcare incident response workflow
→ Quality Reviewer: Validate all prompts
→ Outputs: prompt sequence + playbook + healthcare talking points
```

---

## Section 5: How This Connects to SC Custom Agents

### The Pipeline: Claude Agents → SC Agents

**Claude Agents (This System)**
- Generate candidate prompts and workflows
- Validate against documented SC capabilities
- Create promptbook sequences
- Design agent manifests

**Security Copilot Custom Agents**
- Can be deployed from outputs of this system
- Use YAML manifests generated by Claude agents
- Execute promptbook sequences generated by Investigation Playbook Agent
- Pull from SC plugin's 6 system capabilities

### Deployment Output Formats

#### Format 1: SC Promptbook Sequence
```
[PROMPTBOOK: DLP Investigation]

Step 1: "Show me the top 5 high severity DLP alerts from the past 24 hours"
Decision: If any critical, proceed to Step 2. Otherwise, monitoring complete.

Step 2: "Summarize the DLP alert with ID <alertId>"
Decision: If sensitive data confirmed, proceed to Step 3.

Step 3: "Who is the user related to alert <alertId>?"
Decision: If user is known insider risk, jump to Step 7.

...continue...
```

#### Format 2: SC Custom Agent Manifest (YAML)
```yaml
name: DLP-Investigation-Agent
description: Automated DLP alert investigation with insider risk correlation
capabilities:
  - Get Data Risk Summary
  - Get User Risk Summary
  - Summarize Purview Alert
  - Triage Purview Alerts
  - Zoom Into Purview Data Risk
  - Zoom Into Purview User Risk
prompts:
  - triage: "Show me the top 5 high severity DLP alerts..."
  - investigate: "Summarize the DLP alert with ID <alertId>..."
  - correlate: "Show me the risk associated with <user>..."
triggers:
  - high-severity DLP alert
  - repeat offender user
```

#### Format 3: Prompt Chain (Sequential Execution)
```
[PROMPT CHAIN: DLP Alert Investigation]

Primary Prompt: "Summarize DLP alert <alertId>"
├─ If file is PII → Secondary: "Zoom into data risk for <fileId>"
├─ If sensitive role → Secondary: "Get user risk summary for <user>"
└─ If cross-boundary → Secondary: "Show me DLP alerts for <user>"

Final Output: Investigation summary + risk assessment + escalation recommendation
```

### CSA Workflow: Generate → Validate → Deploy

1. **Generate Phase** (Claude agents)
   - CSA describes investigation scenario
   - Orchestrator Agent coordinates multi-agent generation
   - SC Prompt Engineer generates candidate prompts
   - Investigation Playbook Agent creates workflow sequences

2. **Validate Phase** (Claude agents)
   - Quality Reviewer cross-checks against real SC capabilities
   - Ensures all prompts use correct system capability names
   - Verifies parameter formats and data requirements

3. **Deploy Phase** (CSA manual)
   - CSA approves final prompts
   - Deploys promptbook sequence to SC customers
   - Optionally creates custom SC agent manifest from YAML output
   - Conducts customer workshop using Workshop Agent materials

---

## Section 6: Human Control Boundaries

### What Stays Human-Controlled

**Final Prompt Approval**
- All generated prompts require CSA review before customer delivery
- CSA validates that prompts match customer's investigation needs
- CSA confirms prompts align with customer's RBAC and data access

**Customer-Facing Content Review**
- Workshop slides, demo scripts, talking points reviewed by CSA
- Customer success manager approves messaging and timelines
- Any customer promises (investigation time, coverage) reviewed by leadership

**Security Copilot Custom Agent Deployment**
- CSA decides which agents to deploy to which customers
- CSA handles SC agent registration and RBAC configuration
- CSA performs customer testing before production deployment
- Escalation decisions and investigation policies remain customer-owned

**Policy Decisions**
- Alert thresholds (what triggers investigation) → Customer
- Escalation criteria (when to involve security team) → Customer
- Data retention and evidence handling → Customer compliance/legal
- Investigation reporting and communication → Customer IR policy

### What Can Be Automated

**Prompt Generation and Variation**
- SC Prompt Engineer Agent generates multiple prompt variations
- Automated for different severity levels, user roles, data types
- CSA reviews and selects variations before delivery

**Playbook Sequencing**
- Investigation Playbook Agent designs multi-step workflows
- Automated based on investigation type (DLP, IRM, data risk)
- CSA adds decision criteria and escalation checkpoints

**Quality Validation**
- Quality Reviewer Agent validates all outputs against SC capabilities
- Automated cross-check against documented system limitations
- Flags issues for CSA manual review

**Workshop Content Generation**
- Workshop Agent creates slide outlines, talking points, demo scripts
- Automated based on customer industry and investigation scenario
- CSA customizes messaging and adds customer-specific context

**Capability Lookups**
- Purview Knowledge Agent searches MS Learn for current SC capabilities
- Automated research into system limitations and workarounds
- CSA uses findings to inform prompt generation strategy

### Human Decision Points in Workflow

```
CSA Request
    │
    ▼
Orchestrator Routes to Agents
    │
    ▼
Agents Generate Prompts/Playbooks/Content
    │
    ▼
[HUMAN DECISION: Approved?]
    ├─ No → Return to agents for revision
    └─ Yes ↓
    ▼
Quality Reviewer Validates
    │
    ▼
[HUMAN DECISION: Ready for customer?]
    ├─ No → Flag issues, return to agents
    └─ Yes ↓
    ▼
CSA Conducts Customer Workshop (CSA-led, not automated)
    │
    ▼
[HUMAN DECISION: Deploy to customer SC?]
    ├─ No → Archive for future use
    └─ Yes ↓
    ▼
Customer SC Agent Deployment (CSA configures, not automated)
    │
    ▼
Customer Uses SC Agent (Customer-owned investigation decisions)
```

---

## Appendix: Agent Communication Protocol

### Inter-Agent Message Format

When one agent needs to invoke another:

```
[TO: <Agent Name>]
[CONTEXT: <Brief description of why we need this agent>]
[REQUEST: <Specific question or task>]

Example:
[TO: Purview Knowledge Agent]
[CONTEXT: Generating DLP investigation prompts for a healthcare customer]
[REQUEST: Can Security Copilot correlate DLP alerts with IRM user risk
          when data sharing is enabled between Purview and IRM?]
```

### Agent Response Format

```
[FROM: <Agent Name>]
[STATUS: <Confirmed|Warning|Unable to answer>]
[ANSWER: <Direct answer to the request>]
[SOURCE: <Citation to official docs>]
[NEXT STEPS: <Recommended actions>]

Example:
[FROM: Purview Knowledge Agent]
[STATUS: Confirmed]
[ANSWER: Yes. The 6 Purview SC system capabilities include Get User Risk Summary,
         which pulls IRM data when data sharing is enabled between products.]
[SOURCE: MS Learn - "Security Copilot with Purview" documentation]
[NEXT STEPS: SC Prompt Engineer can now generate cross-signal prompts that
            reference both DLP alerts and IRM user risk]
```

---

## Section 7: Adaptive Reasoning Protocol for Undocumented Cases

### Problem Statement

Not every investigation scenario is documented in the prompt books. Analysts encounter novel cases including Communication Compliance violations, eDiscovery searches, new SIT types, cross-tenant data flows, or activity types they do not recognize. Without guidance, agents either refuse the request or produce unreliable output.

### Design Goal

Enable agents to handle novel, undocumented investigation scenarios by systematically decomposing them into known capability patterns, generating best-effort prompts clearly marked for testing, and documenting new patterns for future reuse.

### Adaptive Reasoning Workflow

```
Analyst Request (novel/undocumented scenario)
    │
    ▼
Orchestrator: Does this match any existing prompt book?
    ├─ Yes → Route to standard workflow
    └─ No ↓
    ▼
Orchestrator: Activate Adaptive Reasoning Protocol
    │
    ├─ 1. Route to Purview Knowledge Agent:
    │     "What Purview data sources are relevant to this scenario?"
    │     "Which of the 6 system capabilities could surface useful data?"
    │     "What audit log operation names correspond to this activity?"
    │
    ├─ 2. Route to SC Prompt Engineer with enhanced instructions:
    │     "Generate a best-effort prompt chain using known capability
    │      patterns. Mark all output as [EXPERIMENTAL]."
    │     "Use Operation Anchor pattern with exact audit log operation names."
    │     "Reference the audit-log-operations.md for operation names."
    │
    ├─ 3. Route to Quality Reviewer with adaptive rules:
    │     "Instead of rejecting unknown capabilities, classify prompts as:
    │      [EXPERIMENTAL - HIGH CONFIDENCE]: Uses validated capabilities in new combination
    │      [EXPERIMENTAL - MEDIUM CONFIDENCE]: Uses validated capabilities for untested scenario
    │      [EXPERIMENTAL - LOW CONFIDENCE]: Relies on assumed/unverified capability
    │      Provide specific rationale for confidence level."
    │
    └─ 4. Package output with testing instructions:
          "These prompts are untested. Before production use:
           1. Execute each prompt in a test environment
           2. Verify output matches expected fields
           3. Document any SC behavior differences
           4. Update validation-matrix.md with results
           5. If validated, move prompt to appropriate prompt book"
```

### Orchestrator Fallback Route

Add to Orchestrator Agent system prompt:

```
### Fallback: Novel Investigation Scenarios

If a CSA request does not match any existing prompt book domain
(DLP, IRM, DSPM, MIP, Audit, Executive, Operations, Workshop):

1. DO NOT refuse the request.
2. Acknowledge that this is an undocumented scenario.
3. Activate the Adaptive Reasoning Protocol:
   a. Ask Purview Knowledge Agent: "Which Purview data sources and
      SC capabilities are most likely relevant to [scenario]?"
   b. Ask SC Prompt Engineer: "Using only confirmed capabilities,
      generate a best-effort prompt chain for [scenario].
      Use the Operation Anchor pattern from taxonomy.md.
      Mark all output as [EXPERIMENTAL]."
   c. Ask Quality Reviewer: "Classify each prompt's confidence
      level using the experimental rating scale.
      Do NOT reject prompts solely because the scenario is novel."
4. Package the output with clear testing instructions.
5. Log the scenario in the backlog for future prompt book expansion.
```

### SC Prompt Engineer: Adaptive Generation Protocol

Add to SC Prompt Engineer system prompt:

```
### Adaptive Generation: Undocumented Scenarios

When generating prompts for scenarios not covered by existing prompt books:

1. Decompose the scenario:
   a. What Purview data source is involved?
      (DLP alerts, IRM cases, audit logs, MIP labels, DSPM data,
       Communication Compliance, eDiscovery, Activity Explorer)
   b. Which of the 6 system capabilities could surface relevant data?
   c. What exact Unified Audit Log operation names correspond to
      the activities in question?
      (Reference: docs/reference/audit-log-operations.md)
   d. What Sensitive Information Types are relevant?
      (Reference: docs/reference/sensitive-information-types.md)
   e. Are additional SC plugins required beyond Purview?
      (Reference: docs/plugin-dependency-map.md)

2. Generate prompts using the closest matching validated pattern:
   - Use the Operation Anchor pattern for activity-based queries
   - Use the SIT Anchor pattern for data-type queries
   - Use the Correlator pattern for cross-signal investigations
   - Use the Investigator pattern for deep-dive scenarios

3. Apply prompt hardening for novel scenarios:
   - Include exact operation names to prevent SC misinterpretation
   - Include exact SIT names to prevent detection mismatches
   - Explicitly name the Purview data source to query
   - Request structured output (table or timeline) to validate completeness
   - Ask SC to state which data sources it used for each finding
   - Include a confidence request: "State your confidence level for each finding"

4. Mark every generated prompt with:
   [EXPERIMENTAL - <CONFIDENCE_LEVEL>]
   Confidence levels:
   - HIGH: Uses validated capabilities in a new combination
   - MEDIUM: Uses validated capabilities for an untested scenario type
   - LOW: Relies on capability that may not work as expected

5. Include testing instructions with each prompt:
   "Test this prompt in your SC environment before production use.
    Verify that SC returns [expected fields]. If SC returns unexpected
    results, try [fallback prompt]."
```

### Quality Reviewer: Adaptive Validation Rules

Add to Quality Reviewer system prompt:

```
### Adaptive Validation: Experimental Prompts

When reviewing prompts generated via the Adaptive Reasoning Protocol:

1. DO NOT reject prompts solely because the scenario is undocumented.

2. Instead, classify each prompt using the experimental confidence scale:
   [EXPERIMENTAL - HIGH CONFIDENCE]
   - Uses only the 6 validated Purview capabilities
   - Combines them in a new way but each capability is confirmed working
   - Operation names and SIT names are from the reference documents

   [EXPERIMENTAL - MEDIUM CONFIDENCE]
   - Uses validated capabilities but for a scenario type not yet tested
   - Some operation names may not produce expected results
   - Output format may differ from what's documented

   [EXPERIMENTAL - LOW CONFIDENCE]
   - Relies on capability behavior that is assumed but not documented
   - Requires plugins beyond Purview that may not be enabled
   - Data availability is uncertain for this scenario type

3. For each experimental prompt, provide:
   - Rationale for confidence classification
   - Specific risk factors (what could go wrong)
   - Suggested fallback prompt if the experimental prompt fails
   - Testing priority (test this before/after other prompts)

4. Track experimental prompts separately:
   - Log in prompts/validation-matrix.md as EXPERIMENTAL status
   - Flag for re-review after first successful test
   - Promote to appropriate prompt book once validated
```

### Coverage Areas for Adaptive Reasoning

The following Purview areas are NOT covered by existing prompt books and should be handled through the Adaptive Reasoning Protocol:

| Area | Current Status | Likely Capability Mapping |
|------|---------------|--------------------------|
| **Communication Compliance** | No prompt book | Summarize Purview Alert, audit log operations: `SupervisoryReviewPolicyMatch`, `SupervisoryReviewTaggedItem` |
| **eDiscovery** | No prompt book | Audit log operations: `SearchCreated`, `SearchExported`, `CaseCreated`, `HoldCreated` |
| **Information Barriers** | No prompt book | Audit log operations for communication events across barrier groups |
| **Records Management** | No prompt book | Retention label operations, disposition review events |
| **Data Lifecycle Management** | No prompt book | Retention policy application, auto-delete events |
| **Activity Explorer deep analysis** | Minimal coverage | Operation-anchored queries for specific activity types |
| **Cross-tenant investigations** | No prompt book | Requires tenant-specific context; multi-session approach |
| **Custom SIT tuning** | No prompt book | SIT Anchor pattern with custom SIT names from customer portal |
| **Adaptive Protection** | No prompt book | IRM + DLP integration operations; dynamic risk level changes |

### Feedback Loop: Experimental to Validated

```
1. Agent generates [EXPERIMENTAL] prompt via Adaptive Reasoning
2. CSA tests prompt in live SC environment
3. CSA documents results in validation-matrix.md
4. If validated:
   a. Remove [EXPERIMENTAL] tag → add [VALIDATED]
   b. Move prompt to appropriate prompt book (or create new prompt book)
   c. Update audit-log-operations.md if new operations discovered
   d. Update plugin-dependency-map.md if new plugin dependencies identified
5. If not validated:
   a. Document failure mode and SC behavior
   b. Generate revised prompt with fallback approach
   c. Re-test and iterate
6. Patterns that recur 3+ times → create dedicated prompt book for that area
```

---

End of Architecture Document
