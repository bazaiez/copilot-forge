# 🎯 Agent Hub — Start Here

> **Just tell me what you need. I'll figure out the rest.**

---

## 🧭 How It Works

You describe what you need in plain language. The Orchestrator Agent analyzes your request, delegates to the right specialist agents, and delivers a complete answer. You don't need to know which agent to use.

---

## 💬 What Can I Ask?

### 🧪 "I have a question about Security Copilot"
> **Example:** "Can SC detect USB file transfers?"
> **What happens:** Knowledge Agent answers with capabilities, limitations, prerequisites, and example prompts.

### ⚡ "I need prompts for a customer use case"
> **Example:** "Generate prompts for a SOC team triaging 200 DLP alerts per day in financial services"
> **What happens:** Prompt Generator Agent creates complete, copy-paste-ready SC prompts from scratch.

### 🔍 "I have an incident to investigate"
> **Example:** "A departing employee downloaded 500 confidential files to USB last week"
> **What happens:** Investigation Agent builds a step-by-step playbook with SC prompts, decision trees, remediation, and escalation criteria.

### 🎬 "I need to prepare a customer demo"
> **Example:** "I have a healthcare customer meeting Friday — they want to see how SC handles PHI protection"
> **What happens:** Demo Architect Agent designs a complete demo package: scenarios, scripts, SC prompts, talking points, Q&A prep, and hands-on labs.

### 🎛️ "I have a complex scenario"
> **Example:** "Financial services customer with insider risk concerns, 500 alerts/day, 3-person SOC, needs demo + investigation playbook + executive reporting"
> **What happens:** Orchestrator splits the work across all agents and delivers a unified package.

### ✅ "Review my prompts"
> **Example:** "Check if these 5 prompts I wrote will actually work in SC"
> **What happens:** Quality Reviewer Agent validates against real capabilities, flags issues, and provides corrected versions.

---

## 🤖 The Agent Team

```
                    ┌─────────────────────┐
                    │   👤 You (user)     │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │  🎛️ Orchestrator    │ ← Receives ALL requests
                    │    (The Brain)      │   Plans, delegates, synthesizes
                    └──────────┬──────────┘
                               │
          ┌────────────────────┼────────────────────┐
          │                    │                     │
          ▼                    ▼                     ▼
 ┌─────────────────┐ ┌─────────────────┐ ┌───────────────────┐
 │ 🧪 Knowledge    │ │ ⚡ Prompt Gen   │ │ 🔍 Investigation  │
 │                 │ │                 │ │                   │
 │ Answers SC +    │ │ Crafts prompts  │ │ Builds incident   │
 │ Purview         │ │ from scratch    │ │ response          │
 │ questions       │ │ for any case    │ │ playbooks         │
 └─────────────────┘ └─────────────────┘ └───────────────────┘
          │                    │                     │
          ▼                    ▼                     ▼
 ┌─────────────────┐ ┌─────────────────┐
 │ 🎬 Demo         │ │ ✅ Quality      │
 │ Architect       │ │ Reviewer        │
 │                 │ │                 │
 │ Designs demos,  │ │ Validates ALL   │
 │ workshops,      │ │ outputs before  │
 │ materials       │ │ delivery        │
 └─────────────────┘ └─────────────────┘
```

---

## ⌨️ Quick Commands

| I want to... | Say this |
|---|---|
| ❓ Ask a question | "Can SC do [X]?" |
| ⚡ Generate prompts | "Generate prompts for [use case]" |
| 🔍 Investigate an incident | "Investigate: [what happened]" |
| 🎬 Prepare a demo | "Prepare a demo for [customer profile]" |
| 💡 Get creative ideas | "What use cases would work for [industry] customer?" |
| ✅ Review existing content | "Review these prompts: [content]" |
| 📦 Build a full package | "I need [prompts + demo + playbook] for [scenario]" |

---

## 📁 Agent Files

| Agent | File | What It Does |
|---|---|---|
| 🎛️ Orchestrator | [orchestrator-agent.md](orchestrator-agent.md) | Plans, delegates, synthesizes |
| 🧪 Knowledge | [knowledge-agent.md](knowledge-agent.md) | Answers capability questions |
| ⚡ Prompt Generator | [prompt-generator-agent.md](prompt-generator-agent.md) | Generates SC prompts from scratch |
| 🔍 Investigation | [investigation-agent.md](investigation-agent.md) | Builds incident response playbooks |
| 🎬 Demo Architect | [demo-architect-agent.md](demo-architect-agent.md) | Designs customer demos and workshops |
| ✅ Quality Reviewer | [quality-reviewer-agent.md](quality-reviewer-agent.md) | Validates all outputs |

---

## 🏅 Quality Guarantee

Every output from this system:

- ✅ Uses ONLY validated SC capabilities (no fictional features)
- ⚠️ Includes relevant limitations (no false promises)
- 🎯 Uses exact technical names (SITs, audit operations, policy names)
- 🏷️ Is marked [VALIDATED] or [ASPIRATIONAL] honestly
- 🔍 Passes the Quality Reviewer before customer delivery
- 📋 Is copy-paste ready for Security Copilot

---

**Author:** Bilel Azaiez — Microsoft CSA, with AI-assisted development
