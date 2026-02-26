# Nanobot Agentic Operating System — Documentation

**Operational guides for the [Nanobot](https://github.com/HKUDS/nanobot) agent orchestration framework.**  
Deploy an AI agent that receives messages from any chat platform, executes governed tasks, and logs everything for audit.

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![GitHub Pages](https://img.shields.io/badge/Docs-GitHub%20Pages-green)](https://egonobueño.github.io/Nanobot-Agentic-Operating-System/)
[![For Nanobot v0.1.4+](https://img.shields.io/badge/Nanobot-v0.1.4%2B-purple)](https://github.com/HKUDS/nanobot)
[![Python 3.11+](https://img.shields.io/badge/Python-3.11%2B-yellow)](docs/AOS-Startup-Simple-Build.md)
[![Updated February 2026](https://img.shields.io/badge/Updated-February%202026-blue)](docs/Master-Index.md)

---

## Choose Your Starting Point

### ⚡ New to Nanobot?

[**Getting Started With Nanobot →**](docs/Getting-Started-With-Nanobot.md)

Explains what Nanobot is, what it can do, and which deployment path suits your situation. Read this first — it takes five minutes and will save you from picking the wrong setup path.

---

### 🖥️ Path A — Simple Build (25–35 min)

Run the gateway on your **personal computer** with a **cloud LLM** (OpenRouter, Claude, GPT-4).

**Best for:** First-time users, testing, light personal use.  
**Cost:** $0–20/month depending on API usage.

| Step | Guide | Time |
|------|-------|------|
| 1. Install and configure | [AOS Startup: Simple Build](docs/AOS-Startup-Simple-Build.md) | 25–35 min |
| 2. Choose your LLM provider | [LLM Provider Setup Guide](docs/LLM-Provider-Setup-Guide.md) | 10–15 min |
| 3. Add tools and automations | [Skills & Tools Complete Guide](docs/Skills-and-Tools-Complete-Guide.md) | Reference |

---

### 🏗️ Path B — Advanced Build (60–90 min)

Run the gateway on a **VPS** with **local Ollama** inference on your home computer, connected via Tailscale.

**Best for:** 24/7 operation, privacy-first, zero cloud AI costs.  
**Cost:** $15–30/month (VPS + electricity; no per-query AI charges).

| Step | Guide | Time |
|------|-------|------|
| 1. VPS + Ollama + Tailscale setup | [AOS Startup: Advanced Build](docs/AOS-Startup-Advanced-Build.md) | 60–90 min |
| 2. Choose your LLM provider | [LLM Provider Setup Guide](docs/LLM-Provider-Setup-Guide.md) | 10–15 min |
| 3. Add tools and automations | [Skills & Tools Complete Guide](docs/Skills-and-Tools-Complete-Guide.md) | Reference |

---

### 🏢 Path C — Multi-Team / Production

Deploy with governance, cost controls, and security auditing across teams.

**Best for:** Organizations, shared deployments, compliance requirements.  
**Additional setup time:** 1–3 hours beyond Path A or B.

| Step | Guide | Time |
|------|-------|------|
| 1. Complete Path A or B first | ↑ above | — |
| 2. Governance, RBAC, cost chargeback | [Security & Governance Policies](docs/Security-and-Governance-Policies.md) | 30–60 min |
| 3. Estimate and optimize costs | [Cost Calculator & Optimization](docs/Cost-Calculator-and-Optimization.md) | 15 min |
| 4. Review workflows to copy | [Workflow Examples & Recipes](docs/Workflow-Examples-and-Recipes.md) | Reference |

---

## All 11 Guides

| Guide | What It Covers |
|-------|---------------|
| [Getting Started With Nanobot](docs/Getting-Started-With-Nanobot.md) | What Nanobot is, capabilities by tier, which path to choose |
| [Master Index](docs/Master-Index.md) | Full architecture, 3-plane model, RACI roles, naming conventions, navigation map |
| [AOS Startup: Simple Build](docs/AOS-Startup-Simple-Build.md) | Local setup on Windows/Mac/Linux with cloud LLM — complete walkthrough for beginners |
| [AOS Startup: Advanced Build](docs/AOS-Startup-Advanced-Build.md) | VPS + local Ollama + Tailscale tunnel — 24/7 self-hosted deployment |
| [LLM Provider Setup Guide](docs/LLM-Provider-Setup-Guide.md) | Configure OpenRouter, Anthropic, OpenAI, Ollama, DeepSeek, Qwen, and multi-provider routing |
| [Skills & Tools Complete Guide](docs/Skills-and-Tools-Complete-Guide.md) | All built-in tools, pre-built skills, Clawhub marketplace, and custom skill development |
| [Workflow Examples & Recipes](docs/Workflow-Examples-and-Recipes.md) | 6 copy-ready workflows: knowledge consolidation, content syndication, GitHub automation, team reporting |
| [Cost Calculator & Optimization](docs/Cost-Calculator-and-Optimization.md) | Monthly spend estimation, multi-tier routing config, budget caps, break-even analysis |
| [Security & Governance Policies](docs/Security-and-Governance-Policies.md) | RBAC patterns, approval workflows, risk tiers, audit logging, monthly security validation runbook |
| [Emergency Recovery & Troubleshooting](docs/Emergency-Recovery-and-Troubleshooting.md) | Diagnose and recover from provider outages, config corruption, vault errors, and credential issues |
| [Glossary](docs/Glossary.md) | Definitions for all AOS terms: planes, tiers, roles, channels, skills, and config fields |

---

## What Nanobot Does

Nanobot is a Python agent orchestration framework ([HKUDS/nanobot](https://github.com/HKUDS/nanobot)). You connect it to chat platforms and LLM providers; it handles the rest.

```
User message (any channel)
    ↓
Nanobot Gateway  ←→  Policy Engine (RBAC, approval gates, cost limits)
    ↓
Tool Execution   ←→  LLM Provider (OpenRouter / Anthropic / local Ollama / ...)
    ↓
Response + Audit Log
```

**Channels supported:** Discord, Slack, Telegram, WhatsApp, Feishu, DingTalk, Matrix, MoChat, QQ, Email, CLI (11 total)

**LLM providers:** OpenRouter (100+ models), Anthropic, OpenAI, Ollama (local), DeepSeek, Qwen, Gemini, Cohere, and any OpenAI-compatible endpoint

**Built-in tools (14):** web search, file read/write, shell execution, message delivery, cron scheduling, MCP integration, GitHub automation, subagent spawning, and more

**Pre-built skills:** Obsidian vault management, memory consolidation, content summarization, weather, GitHub operations, TMUX control, skill auto-generation, and the Clawhub skill marketplace

**Cost model:**

| Tier | Models | Use For |
|------|--------|---------|
| **Tier A (Premium)** | Claude Opus, GPT-4 | High-value tasks requiring best quality |
| **Tier B (Balanced)** | Claude Haiku, GPT-4 Mini, Qwen-72B | General use, moderate reasoning |
| **Tier C (Budget)** | DeepSeek, Gemini, Cohere | Summarization, classification, routine tasks |
| **Tier D (Free)** | Local Ollama | Privacy-critical or high-volume — no API charges |

---

## Getting Help

| Problem | Go here |
|---------|---------|
| Not sure where to start | [Getting Started With Nanobot](docs/Getting-Started-With-Nanobot.md) |
| Installation issues | [AOS Startup: Simple Build](docs/AOS-Startup-Simple-Build.md) or [Advanced Build](docs/AOS-Startup-Advanced-Build.md) |
| LLM provider not connecting | [LLM Provider Setup Guide](docs/LLM-Provider-Setup-Guide.md) |
| Channel bot not responding | [Channel integration guide](nanobot/README.md) |
| Unexpected costs | [Cost Calculator & Optimization](docs/Cost-Calculator-and-Optimization.md) |
| Access control or governance | [Security & Governance Policies](docs/Security-and-Governance-Policies.md) |
| System failure or recovery | [Emergency Recovery & Troubleshooting](docs/Emergency-Recovery-and-Troubleshooting.md) |
| Unfamiliar term or acronym | [Glossary](docs/Glossary.md) |

---

## Contributing

Improvements are welcome. We're particularly interested in:

- **Broken or outdated content** — open an issue with the section and what changed
- **Additional workflow recipes** — real use cases that solve a concrete problem
- **Platform-specific guidance** — setup notes for channels or providers not covered
- **Clarifications** — anything that was harder to follow than it needed to be

Please open an issue or pull request. Describe what guide/section needs attention and what the correct information is.

---

## License & Credits

Documentation licensed under **[MIT](LICENSE)**.

Built for the [HKUDS/nanobot](https://github.com/HKUDS/nanobot) framework (Python 3.11+, Nanobot v0.1.4+).  
Maintained by [EgoNoBueno](https://github.com/EgoNoBueno).

---

*Browse the full docs online: [egonobueño.github.io/Nanobot-Agentic-Operating-System](https://egonobueño.github.io/Nanobot-Agentic-Operating-System/) — includes search (Ctrl+K / Cmd+K), dark mode, and mobile layout.*
