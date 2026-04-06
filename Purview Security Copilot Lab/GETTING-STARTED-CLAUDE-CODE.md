# Getting Started with Claude Code

How to use Claude Code to work with the multi-agent architecture in this repository.

## What Claude Code Does Here

When you open this repo in Claude Code, it automatically reads `CLAUDE.md` at session start. That file tells Claude:

- The 6 validated Purview plugin capabilities
- Known SC limitations and constraints
- Prompt format rules and taxonomy
- The repo structure and where to find content

This means Claude already understands the framework before you type anything.

## Choose Your Setup

### VS Code Extension (Recommended)

1. Open VS Code (version 1.98.0 or later)
2. Go to Extensions (`Ctrl+Shift+X`) and search **"Claude Code"**
3. Install the extension by Anthropic
4. Open this repo folder in VS Code
5. Open the Claude Code panel from the sidebar

### CLI

```powershell
# Windows
irm https://claude.ai/install.ps1 | iex

# macOS / Linux
curl -fsSL https://claude.ai/install.sh | bash
```

Then navigate to this repo and run:
```
claude
```

### Desktop App

1. Download from [claude.com/download](https://claude.com/download)
2. Open the app and go to the **Code** tab
3. Point it to this repo folder

## Account Requirement

Claude Code requires a **Pro, Max, Team, or Enterprise** plan. Free plans do not include Claude Code access.

## First Session

After opening the repo, Claude automatically loads `CLAUDE.md`. You can start working immediately.

**Example prompts to try:**

```
Generate a DLP alert triage promptbook for a financial services customer
```

```
Review the IRM prompt book and suggest improvements
```

```
Build a playbook for investigating data exfiltration via USB devices
```

```
What SC capabilities are available for insider risk investigations?
```

## Using the Agent Personas

The [agents/architecture.md](agents/architecture.md) defines 6 specialized agents. You can activate any persona by asking Claude to adopt that role.

### Orchestrator Agent
Routes your request to the right specialist. Use when you have a broad or multi-step task.

```
Act as the Orchestrator Agent. I need a complete investigation package
for DLP alerts in a healthcare environment. Route this through the
appropriate agents.
```

### Purview Knowledge Agent
Source of truth for what Security Copilot can actually do with Purview.

```
Act as the Purview Knowledge Agent. What are the exact capabilities
of the IRM Triage Agent? What are its limitations?
```

### SC Prompt Engineer Agent
Generates prompts using correct SC syntax and validated capabilities.

```
Act as the SC Prompt Engineer Agent. Generate a prompt chain for
triaging the top 10 DLP alerts by severity, then drilling into
the highest-risk user.
```

### Investigation Playbook Agent
Structures prompts into step-by-step investigation workflows.

```
Act as the Investigation Playbook Agent. Create a complete playbook
for responding to a sensitive data leak detected via DLP policy match.
```

### Quality Reviewer Agent
Validates prompts and playbooks against real SC capabilities.

```
Act as the Quality Reviewer Agent. Review prompts/dlp/prompt-book.md
and flag any prompts that reference capabilities not in the validated
Purview plugin list.
```

### Customer Workshop Agent
Prepares customer-facing materials, demos, and workshop content.

```
Act as the Customer Workshop Agent. Prepare a 2-hour hands-on workshop
agenda for a retail customer's SOC team on using SC for DLP investigations.
```

## Common Workflows

### Generate prompts for a customer engagement
```
I have a meeting with a financial services customer who wants to
deploy SC for insider risk investigations. Generate a set of
validated prompts they can use on day one.
```

### Validate existing content
```
Review all prompt books in prompts/ and flag any prompts marked
[ASPIRATIONAL] that could now be [VALIDATED] based on current
SC capabilities.
```

### Build a new playbook
```
Create a new playbook for investigating Communication Compliance
alerts. Follow the structure in playbooks/ and use the template
from templates/playbook-template.md.
```

### Chain multiple personas
```
Using the full agent topology:
1. Research what SC can do for DSPM (Knowledge Agent)
2. Generate prompts for data posture assessment (Prompt Engineer)
3. Structure into an investigation workflow (Playbook Agent)
4. Validate everything (Quality Reviewer)
```

## Tips

- **Be specific about the customer context** — industry, data types, and investigation triggers help Claude generate better prompts
- **Reference existing content** — point Claude to specific files like `prompts/dlp/prompt-book.md` for context
- **Use [VALIDATED] vs [ASPIRATIONAL]** — always ask Claude to mark prompts based on whether they match known-working SC capabilities
- **Check limitations** — ask Claude to list relevant limitations from `CLAUDE.md` for any generated content
- **Chain prompts** — SC promptbooks use `<parameterName>` format with no spaces for parameter passing between steps

## Useful Commands

| Command | What It Does |
|---------|-------------|
| `/init` | Creates a `CLAUDE.md` file if one doesn't exist |
| `/memory` | Opens your memory file for persistent notes |
| `/compact` | Compresses conversation context when it gets long |
| `/clear` | Starts a fresh conversation |
| `claude -c` | Resume the most recent conversation (CLI) |
| `claude -r` | Resume and print the full transcript (CLI) |

## How It All Connects

```
You (CSA) ──► Claude Code ──► Reads CLAUDE.md (capabilities, rules, structure)
                   │
                   ├──► agents/architecture.md (persona definitions)
                   ├──► prompts/ (existing prompt books to reference)
                   ├──► playbooks/ (investigation workflow patterns)
                   ├──► use-cases/catalog.md (22 mapped scenarios)
                   └──► docs/ (technical deep dives, limitations)
```

Claude uses this context to generate, validate, and package SC-ready outputs that follow the framework's standards.
