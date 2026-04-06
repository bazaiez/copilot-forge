# Security Copilot for Purview Data Security Framework

A Microsoft-internal framework for Data Security CSAs, analysts, and administrators to generate and operationalize Security Copilot prompts, agents, and promptbooks for Purview data security investigations.

## What This Is

This repository contains a complete field-ready system for deploying Microsoft Security Copilot (SC) across Purview data security workflows. Instead of building prompts from scratch, Data Security CSAs and investigators can use this framework to:

- Copy pre-built, tested prompts for DLP triage, insider risk investigation, data posture assessment, and audit workflows
- Build custom promptbooks (sequential multi-prompt workflows) for specific scenarios
- Deploy production SC agents (DLP Triage, IRM Triage, DSPM Posture, Data Security Investigations)
- Run investigations using structured playbooks with clear steps and success criteria
- Share knowledge and patterns with customers and peer CSAs

The core purpose: **Help CSAs scale the use of Security Copilot for data security investigations with their customers.**

## Who This Is For

- **Data Security CSAs** designing and deploying SC + Purview solutions
- **SOC Analysts & Security Investigators** triaging incidents and managing cases
- **Compliance Officers & Data Security Admins** running data governance workflows
- **Security Leadership** evaluating SC's operational impact

## How to Get Started

**I want to...** | **Go Here**
---|---
Triage DLP alerts quickly | [DLP Prompt Book](prompts/dlp/prompt-book.md) → Use Triage Agents section
Investigate insider risk cases | [IRM Prompt Book](prompts/irm/prompt-book.md) → Follow IRM Triage playbook
Prepare a customer workshop demo | [Workshop Prompts](prompts/workshop/prompt-book.md)
Build a custom promptbook for my team | [USAGE-GUIDE.md](USAGE-GUIDE.md) → Building Your Own Promptbooks
Check what SC can actually do in Purview | [USAGE-GUIDE.md](USAGE-GUIDE.md) → Getting Started section
Understand product limitations | [USAGE-GUIDE.md](USAGE-GUIDE.md) → Known Limitations section
Learn the technical architecture | [ARCHITECTURE.md](ARCHITECTURE.md)
Deploy a DLP Triage Agent | [Agents Documentation](agents/README.md)

## What's Inside This Repository

```
├── README.md (you are here)
├── USAGE-GUIDE.md (practical how-to guide for using this framework)
├── ARCHITECTURE.md (design principles, agent patterns, system architecture)
├── CONTRIBUTING.md (how to add prompts, playbooks, or learnings)
│
├── prompts/
│   ├── dlp/prompt-book.md (Data Loss Prevention prompt workflows)
│   ├── irm/prompt-book.md (Insider Risk Management investigations)
│   ├── dspm/prompt-book.md (Data Security Posture Management)
│   ├── audit/prompt-book.md (Audit and forensics prompts)
│   ├── mip/prompt-book.md (Microsoft Information Protection)
│   ├── executive/prompt-book.md (Leadership reporting)
│   ├── workshop/prompt-book.md (Customer demo and training scenarios)
│   ├── operations/prompt-book.md (Daily operational hygiene)
│   ├── README.md (prompt design principles)
│   └── taxonomy.md (how prompts are organized and tagged)
│
├── playbooks/
│   ├── dlp-incident-investigation.md (step-by-step DLP incident response)
│   ├── irm-case-triage.md (IRM case classification workflow)
│   ├── data-leakage-response.md (cross-product leak response)
│   ├── post-incident-review.md (after-action analysis)
│   └── README.md (playbook structure and execution model)
│
├── agents/
│   ├── README.md (agent deployment and customization)
│   └── architecture.md (multi-agent orchestration patterns)
│
├── docs/
│   ├── vision-and-scope.md (what we're building and why)
│   ├── personas.md (detailed role definitions and workflows)
│   ├── knowledge-architecture.md (how knowledge flows through SC)
│   └── technical-deep-dives/
│       ├── Purview.md (Purview data flows and signal generation)
│       ├── security-copilot-mechanics.md (SC context, plugins, capabilities)
│       └── limitations-and-blind-spots.md (honest assessment of gaps)
│
├── use-cases/
│   ├── README.md (how to use the catalog)
│   └── catalog.md (mapped scenarios with success criteria)
│
├── templates/
│   └── (incident response templates, triage worksheets, report formats)
│
├── backlog/
│   └── (research areas and future development)
│
└── ROADMAP.md (planned features and enhancements)
```

## Prerequisites

Before using this framework, ensure you have:

### Access & Licensing
- **Security Copilot access** (assigned via Microsoft Security admin center)
- **Purview role assignment** (DLP Admin, IRM Admin, or equivalent)
- **SCU capacity** for your tenant (each prompt/agent call costs SCUs; plan accordingly)
- **Commercial cloud deployment** (SC is not available in sovereign/FedRAMP clouds; note: FedRAMP High commercial is GA)

### Configuration
- **Purview plugin enabled** in your SC instance (Sources > Plugins > Purview toggle)
- **Appropriate RBAC** in Purview for your role (alert access, user investigation rights, data classification visibility)
- **DLP active mode policies** (DLP Triage Agent does not work with simulation mode)

### Recommended
- SC experience or familiarity with copilot-driven investigation
- Understanding of your Purview policies (DLP rules, IRM settings, sensitivity labels)
- A test tenant or safe environment for experimenting with new prompts

## Quick Navigation by Role

| Role | Essential Reading | Then Use |
|------|------------------|----------|
| CSA / Pre-sales | [USAGE-GUIDE.md](USAGE-GUIDE.md) - For CSAs section; [workshop/prompt-book.md](prompts/workshop/prompt-book.md) | Workshop demo prompts and scenario templates |
| Analyst / Investigator | [USAGE-GUIDE.md](USAGE-GUIDE.md) - Daily Investigation Workflow section; [playbooks/README.md](playbooks/README.md) | DLP/IRM prompt books and incident playbooks |
| Security Admin | [USAGE-GUIDE.md](USAGE-GUIDE.md) - Using SC Agents section; [agents/README.md](agents/README.md) | DLP Triage Agent, IRM Triage Agent setup |
| Data Security Admin | [prompts/operations/prompt-book.md](prompts/operations/prompt-book.md); [prompts/mip/prompt-book.md](prompts/mip/prompt-book.md) | Policy hygiene, data governance workflows |
| Executive / CISO | [docs/vision-and-scope.md](docs/vision-and-scope.md); [prompts/executive/prompt-book.md](prompts/executive/prompt-book.md) | Program value, reporting templates |

## Core Capabilities at a Glance

This framework leverages these **Purview + SC native capabilities**:

**Triage & Alert Management:**
- DLP Triage Agent (auto-categorizes active mode DLP alerts)
- IRM Triage Agent (auto-categorizes IRM alerts)
- Get Data Risk Summary (MIP + DLP risk attributes)
- Get User Risk Summary (IRM user activities, exfiltration, sequences)

**Investigation:**
- Summarize Purview Alerts (DLP and IRM alerts with context)
- Zoom Into Data Risk (detailed MIP/DLP attributes)
- Zoom Into User Risk (activities, anomalies, behavioral patterns)

**Discovery & Posture:**
- DSPM Posture Agent (natural language data discovery)
- Data Security Investigations Agent (credential scanning)

**Reporting:**
- Executive summary generation
- Policy insights
- Activity explorer analysis (preview)

All prompts in this framework are designed to leverage these capabilities effectively.

## How to Contribute

See [CONTRIBUTING.md](CONTRIBUTING.md) for detailed guidance. In summary:

- **New prompts or prompt books**: Test in your environment, document with examples and output, explain use case
- **New playbooks**: Include step-by-step workflow, expected SC prompts, success criteria, common gotchas
- **Field learnings**: Document limitations found, workarounds, customer feedback
- **Bug reports or improvements**: File as issues with reproduction steps

All contributions must be honest about what works, what doesn't, and what the actual limitations are.

## Status & Disclaimer

**Internal Use Only | Work in Progress**

This framework represents collective field guidance and best practices developed by Microsoft Cloud Solution Architects and Security Copilot subject matter experts. It is:

- **Not official Microsoft product documentation**
- **Not covered by Microsoft support agreements**
- **Subject to change** as Security Copilot and Purview capabilities evolve
- **Your responsibility to validate** in your own environment before production use

Prompts, agents, playbooks, and limitations documented here reflect current (as of April 2026) product capabilities and may not reflect future releases.

---

**Next Steps:**
1. Read [USAGE-GUIDE.md](USAGE-GUIDE.md) for practical how-to instructions
2. Choose a prompt book for your use case
3. Review the relevant playbook if running an investigation
4. Test in your environment before customer engagement

**Questions?** Reach out to the Security Copilot Champions community or your CSA leadership.
