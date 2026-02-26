
# Getting Started With Nanobot

## Document Control
- **Owner:** Nanobot Community
- **Version:** 1.3.0
- **Last Updated:** February 26, 2026
- **Status:** Active
- **Purpose:** Quick-start orientation and documentation map for new users

---

## Who Is This For?

This documentation is for developers, operators, and teams who want to deploy, operate, and extend the Nanobot agentic framework for LLM-powered automation, research, and multi-channel communication. It is suitable for both beginners (getting started quickly) and advanced users (production, governance, and customization).

## What Nanobot Does

Nanobot is a lightweight (~4,000 lines), extensible agentic framework that:
- Routes to 100+ **LLM** (Large Language Model) models: Claude, GPT-4, local Ollama, Qwen, DeepSeek, etc.
- Integrates with 11 communication channels (Discord, Slack, Telegram, Feishu, DingTalk, WhatsApp, Email, QQ, Matrix, Mochat, CLI)
- Executes 14 built-in tools (web search, file ops, shell, GitHub, scheduling, MCP — Model Context Protocol tools for external integrations)
- Provides 9 pre-built skills (Obsidian, memory, summarization, cron, GitHub automation)
- Routes LLM calls across cost tiers (Tier A/B/C) with budget caps and escalation gates
- Enforces security with role-based access control, tool allowlisting, and audit logging

## About This Documentation

**Repository:** [HKUDS/nanobot](https://github.com/HKUDS/nanobot)  
**Nanobot Version:** v0.1.4 and later  
**Documentation Updated:** February 26, 2026

---

## How Nanobot Works

Nanobot is organized into three planes that work together on every request. Understanding the planes helps you know which documents to read and which settings to configure.

**Control Plane** — Multi-channel intake. Receives messages from Discord, Slack, Telegram, Email, and 7 other channels, then routes them into the agent loop. Configuration: [nanobot/README.md](../nanobot/README.md).

**Policy Plane** — Governance and routing. Selects the LLM tier for each request (Tier A = premium models like Claude Opus; Tier B = balanced; Tier C = cheapest/local), applies budget caps, enforces approval gates before tool execution, and logs decisions to the audit trail. Configuration: [Security & Governance Policies](Security-and-Governance-Policies.md) and [LLM Provider Setup Guide](LLM-Provider-Setup-Guide.md).

**Memory Plane** — Durable storage. Writes to an [Obsidian](https://obsidian.md) vault (a local markdown knowledge base) for audit trails, context recall, and long-term memory across sessions. Skills like `memory_consolidation` and `obsidian` operate in this plane. Configuration: [Skills & Tools Reference](Skills-and-Tools-Complete-Guide.md).

### Example: A Request in Action

1. Operator asks in Slack: *"Find competitor campaigns this week and propose a test"*
2. **Control Plane** receives the message via Slack integration
3. **Policy Plane** routes to Claude Opus ([Tier A](Glossary.md) — high-value task)
4. Agent executes: **web_search** → **summarize** → synthesizes findings
5. Posts response back to Slack with recommendation and source links
6. **Memory Plane** logs results to Obsidian for audit trail and replay

---

## Choose Your Deployment

### Option 1 — Simple Build: Local + Cloud LLM
- **Setup time:** 25-35 minutes
- **Ongoing cost:** $0-50/month (based on API usage; zero cost with local Ollama)
- **Best for:** First-time setup, prototyping, teams evaluating AOS
- **What it activates:** Control Plane (channels) + Policy Plane (LLM routing with cloud API)
- **What it doesn't include:** 24/7 persistent operation, local inference, full Memory Plane setup

**→ [AOS Startup: Simple Build](AOS-Startup-Simple-Build.md)**

### Option 2 — Advanced Build: VPS + Local Ollama
- **Setup time:** 60-90 minutes
- **Ongoing cost:** $20-200/month (VPS server; inference costs $0 — runs locally)
- **Best for:** Privacy, self-hosted inference, 24/7 continuous operation, production teams
- **What it activates:** All three planes — Control, Policy, and Memory — with persistent storage and full governance configuration

**→ [AOS Startup: Advanced Build](AOS-Startup-Advanced-Build.md)**

### Install (Both Options)

```bash
git clone https://github.com/HKUDS/nanobot.git
cd nanobot
pip install -e .
```

Or from PyPI:
```bash
pip install nanobot-ai
```

### What You Have After Setup

- A running Nanobot agent connected to your LLM provider
- One or more channels configured (CLI, Discord, Slack, Telegram, etc.)
- 14 built-in tools available: web search, file read/write, shell, GitHub, scheduling, MCP, subagents
- 9 pre-built skills ready to activate: Obsidian, memory consolidation, summarize, cron, GitHub automation
- Cost tier routing active: requests are routed to cheapest-capable LLM by default
- Role-based access control and audit logging enabled

### Ready to Go Further?

- Set up multi-team governance and approval workflows → [Security & Governance Policies](Security-and-Governance-Policies.md)
- Fine-tune LLM providers and cost tiers → [LLM Provider Setup Guide](LLM-Provider-Setup-Guide.md)
- Build custom skills and automate complex workflows → [Skills & Tools Reference](Skills-and-Tools-Complete-Guide.md)

---

## Documentation Map

### Learn the Architecture
- **[Master Index](Master-Index.md)** — Deep dive into the 3-plane architecture (Control, Policy, Memory), how governance works, and understand the full system

### Operate & Extend
- **[Skills & Tools Reference](Skills-and-Tools-Complete-Guide.md)** — Inventory of all 14 built-in tools and 9 pre-built skills; how to write custom skills
- **[Workflow Examples](Workflow-Examples-and-Recipes.md)** — Copy-paste ready workflows:
  - Daily knowledge consolidation  
  - Multi-channel content distribution  
  - Customer support automation  
  - Research agent  
  - GitHub automation

### Govern & Optimize
- **[Security & Governance Policies](Security-and-Governance-Policies.md)** — Role-based access, approval workflows, audit logging, cost allocation for multi-team deployments
- **[LLM Provider Setup Guide](LLM-Provider-Setup-Guide.md)** — Configure AI models (OpenRouter, Claude, OpenAI, local Ollama, Qwen, DeepSeek)
- **[Cost Calculator & Optimization](Cost-Calculator-and-Optimization.md)** — Estimate your bill, optimize with multi-provider routing (save 60-70%), self-hosted vs. cloud break-even analysis

### Troubleshoot & Recover
- **[Emergency Recovery & Troubleshooting](Emergency-Recovery-and-Troubleshooting.md)** — Diagnose and fix common deployment issues

### Reference
- **[Glossary](Glossary.md)** — Definitions for AOS-specific terms: planes, tiers, SOUL.md, SKILL.md, approval gates, and more

---

## Revision History

| Date | Version | Change |
|---|---|---|
| 2026-02-26 | 1.3.0 | Detailed pass: split "Who Is This For?" from capabilities into separate "What Nanobot Does" section; introduced Tier A/B/C cost routing in capabilities; expanded "How Nanobot Works" with per-plane config doc links, Obsidian explanation, Tier A link; merged redundant "Quick Start" + "Choose Your Path" into single "Choose Your Deployment" section with plane-activation context; moved install commands into deploy section; removed duplicate Ready for Production / Choose Your Path overlap; standardized em-dash spacing |
| 2026-02-26 | 1.2.0 | Rough first pass: fixed H1 to match filename; moved 3-plane architecture overview before Quick Start; removed orphaned bold tagline; added Glossary to Documentation Map |
| 2026-02-26 | 1.1.0 | Reviewed document structure; added Document Control header; fixed channel count (12→11); replaced negative "Limitations" with positive "Ready for Production?" section; reorganized Quick Start → Choose Your Path → Documentation Map flow for better readability; consolidated redundant sections; improved AOS alignment throughout |
| 2026-02-26 | 1.0.0 | Renamed from index.md and initial publication |
