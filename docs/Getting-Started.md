
# Nanobot Agentic Operating System (AOS)

## Document Control
- **Owner:** Nanobot Community
- **Version:** 1.1.0
- **Last Updated:** February 26, 2026
- **Status:** Active
- **Purpose:** Quick-start orientation and documentation map for new users

---

## Who Is This For?
This documentation is for developers, operators, and teams who want to deploy, operate, and extend the Nanobot agentic framework for LLM-powered automation, research, and multi-channel communication. It is suitable for both beginners (getting started quickly) and advanced users (production, governance, and customization).

**Production-ready documentation for deploying and operating the Nanobot AI agent framework.**

Nanobot is a lightweight (~4,000 lines), extensible agentic framework that:
- Routes to 100+ **LLM** (Large Language Model) models: Claude, GPT-4, local Ollama, Qwen, DeepSeek, etc.
- Integrates with 11 communication channels (Discord, Slack, Telegram, Feishu, DingTalk, WhatsApp, Email, QQ, Matrix, Mochat, CLI)
- Executes 14 built-in tools (web search, file ops, shell, GitHub, scheduling, MCP—Model Context Protocol tools for external integrations)
- Provides 9 pre-built skills (Obsidian, memory, summarization, cron, GitHub automation)
- Manages cost through tiered routing and budget controls
- Enforces security with role-based access control and audit logging

## About This Documentation

**Repository:** [HKUDS/nanobot](https://github.com/HKUDS/nanobot)  
**Nanobot Version:** v0.1.4 and later  
**Documentation Updated:** February 26, 2026

### Get the Code

```bash
git clone https://github.com/HKUDS/nanobot.git
cd nanobot
pip install -e .
```

Or install from PyPI:
```bash
pip install nanobot-ai
```

---


## Quick Start (25-35 Minutes)

Start here: **[AOS Startup Procedure: Simple Build](AOS-Startup-Simple-Build.md)**

The Simple Build is ideal for evaluation, prototyping, and basic use. Includes step-by-step installation, LLM provider setup, and channel integration.

### What You Get With Quick Start

If you follow the Quick Start guide, you will have:

- A working Nanobot agent connected to your chosen LLM provider (OpenAI, Anthropic, local Ollama, etc.)
- Basic channel integration (CLI, Discord, Slack, Telegram, Feishu, etc.)
- Access to core tools: web search, file operations, shell commands, GitHub integration, scheduling, memory, and MCP tools
- Ability to run and interact with the agent for simple tasks, chat, and automation
- Default security and cost controls

### Ready for Production?

The Quick Start provides the essentials. For production deployments, you'll also need:
- **Multi-team governance & approval gates** — See [Security & Governance Policies](Security-and-Governance-Policies.md)
- **Advanced cost optimization** — See [Cost Calculator & Optimization](Cost-Calculator-and-Optimization.md) for multi-provider routing (60-70% savings)
- **24/7 operations on your own hardware** — See [AOS Startup: Advanced Build](AOS-Startup-Advanced-Build.md) (self-hosted Ollama, zero cloud costs)
- **Custom skills and workflows** — See [Skills & Tools Reference](Skills-and-Tools-Complete-Guide.md) and [Workflow Examples](Workflow-Examples-and-Recipes.md)

---

## Choose Your Path

### Option 1 — Local + Cloud LLM (Easiest)
- **Setup time:** 25-35 minutes
- **Ongoing cost:** $0-50/month (based on API usage)
- **Best for:** Teams, quick experiments, cost-conscious organizations, prototyping
- **Deployment:** Runs on your laptop or CI/CD; LLM calls routed to OpenAI, Anthropic, etc.

**→ [AOS Startup: Simple Build](AOS-Startup-Simple-Build.md)**

### Option 2 — VPS + Local Ollama (Advanced)
- **Setup time:** 60-90 minutes
- **Ongoing cost:** $20-200/month (VPS only; inference is free)
- **Best for:** Privacy, self-hosted inference, 24/7 continuous operation, scale
- **Deployment:** Runs 24/7 on a dedicated VPS; local Ollama handles LLM inference (zero API calls = zero cloud costs)

**→ [AOS Startup: Advanced Build](AOS-Startup-Advanced-Build.md)**

---

## Documentation Map & Next Steps

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

---

## System Overview

### 3-Plane Architecture

**Control Plane:** Multi-channel intake (Discord, Slack, Telegram, etc.)  
**Policy Plane:** LLM routing, cost controls, approval gates  
**Memory Plane:** Obsidian vault for durable records and audit trails

### Example Workflow

1. Operator asks in Slack: *"Find competitor campaigns this week and propose a test"*
2. Nanobot receives message via Slack integration
3. Policy routes to Claude Opus (high-value task)
4. Nanobot executes: **web_search** → **summarize** → synthesizes findings
5. Posts back to Slack with recommendation and source links
6. Logs to Obsidian for audit trail and replay

---

## Revision History

| Date | Version | Change |
|---|---|---|
| 2026-02-26 | 1.1.0 | Reviewed document structure; added Document Control header; fixed channel count (12→11); replaced negative "Limitations" with positive "Ready for Production?" section; reorganized Quick Start → Choose Your Path → Documentation Map flow for better readability; consolidated redundant sections; improved AOS alignment throughout |
| 2026-02-26 | 1.0.0 | Renamed from index.md and initial publication |
