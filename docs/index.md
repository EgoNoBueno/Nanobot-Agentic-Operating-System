
# Nanobot Agentic Operating System (AOS)

## Who Is This For?
This documentation is for developers, operators, and teams who want to deploy, operate, and extend the Nanobot agentic framework for LLM-powered automation, research, and multi-channel communication. It is suitable for both beginners (getting started quickly) and advanced users (production, governance, and customization).

**Production-ready documentation for deploying and operating the Nanobot AI agent framework.**

Nanobot is a lightweight (~4,000 lines), extensible agentic framework that:
- Routes to 100+ **LLM** (Large Language Model) models: Claude, GPT-4, local Ollama, Qwen, DeepSeek, etc.
- Integrates with 12+ communication channels (Discord, Slack, Telegram, Feishu, etc.)
- Executes 14 built-in tools (web search, file ops, shell, GitHub, scheduling, MCP—Model Context Protocol tools for external integrations)
- Provides 9 pre-built skills (Obsidian, memory, summarization, cron, GitHub automation)
- Manages cost through tiered routing and budget controls
- Enforces security with role-based access control and audit logging

## About This Documentation

**Repository:** [HKUDS/nanobot](https://github.com/HKUDS/nanobot)  
**Nanobot Version:** v0.1.4 and later  
**Documentation Updated:** February 25, 2026

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


## Quick Start (5 Minutes)

Start here: **[Quick Install & Setup](Nanobot-Quick-Install-Setup.md)**

Most users will eventually want full AOS capabilities, allowing increased Autonomous Agent capabilities. The Quick Start is ideal for evaluation, prototyping, and basic use.

### What You Get With Quick Start

If you follow the Quick Start guide, you will have:

- A working Nanobot agent connected to your chosen LLM provider (OpenAI, Anthropic, local Ollama, etc.)
- Basic channel integration (CLI, Discord, Slack, Telegram, Feishu, etc.)
- Access to core tools: web search, file operations, shell commands, GitHub integration, scheduling, memory, and MCP tools
- Ability to run and interact with the agent for simple tasks, chat, and automation
- Default security and cost controls

**Limitations (AOS Standpoint):**
- Advanced governance features (multi-team policies, role-based access, audit logging, cost allocation) are not configured
- Custom channel policies, approval workflows, and escalation rules are not set up
- Multi-provider routing and advanced cost optimization require additional configuration
- No advanced skills or custom workflows beyond the built-in set
- Production deployment, backup, and recovery procedures are not established
- Security validation and compliance runbooks are not enabled by default


**Full AOS Capability:**

For production deployments, advanced governance, and full automation, continue beyond Quick Start:
- See the [Governance Policies and Config Examples](Governance-Policies-and-Config-Examples.md) for multi-team, security, and compliance features
- Use the [Multi-Channel Integration Guide](Multi-Channel-Integration-Guide.md) for advanced channel management and cross-platform workflows
- Follow the [Nanobot Build Procedure](Nanobot-Build-Procedure.md) for robust, scalable, and secure deployments

**Additional Capabilities with Multi-Channel Integration Guide:**
- Seamless integration with multiple communication platforms (Discord, Slack, Telegram, Feishu, DingTalk, WhatsApp, Email, QQ, Matrix, Mochat)
- Centralized management of channel policies, naming conventions, and access controls
- Ability to route tasks, messages, and workflows across channels for team collaboration
- Custom channel-specific behaviors, escalation rules, and approval workflows
- Enhanced notification, alerting, and cross-channel automation
- Improved audit logging and traceability for all channel interactions
- Support for multi-team deployments and channel isolation


## Getting Started

- **[Master Index](Master-Index.md)** — Overview of the entire system architecture
- **[Quick Install & Setup](Nanobot-Quick-Install-Setup.md)** — 5-minute quick start
- **[AOS Startup Procedure: Simple Build](AOS-Startup-Simple-Build.md)** — Deploy locally with cloud LLM (25-35 minutes)
- **[AOS Startup Procedure: Advanced Build](AOS-Startup-Advanced-Build.md)** — Deploy on VPS with local Ollama (60-90 minutes)

## Operating Nanobot

- **[Essential Skills](Essential-AOS-Skills.md)** — 5 core skills you need
- **[Tools & Skills Reference](Tools-and-Skills-Reference.md)** — Complete inventory of all capabilities
- **[Workflow Examples](Workflow-Examples-and-Recipes.md)** — Real-world workflows to copy & customize
  - Daily knowledge consolidation
  - Multi-channel content distribution
  - Customer support bot
  - Research agent
  - GitHub automation

## Cost & Governance

- **[Cost Calculator & Optimization](Cost-Calculator-and-Optimization.md)** — Estimate your bill, optimize routing
  - Provider cost matrix (cheapest to premium)
  - Monthly cost calculators with examples
  - Multi-provider routing for 60-70% savings
  - Break-even analysis (cloud vs. self-hosted)

- **[Governance Policies](Governance-Policies-and-Config-Examples.md)** — Multi-team deployments
  - Role-based access control
  - Channel naming conventions
  - Approval workflows
  - Escalation rules
  - Audit logging
  - Cost allocation & chargeback

## Security & Compliance

- **[Security Validation Runbook](Security-Validation-Runbook.md)** — Monthly audit procedures

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

### Common Use Cases

- **Product Marketing:** Auto-generate content, run competitor research, report KPI deltas
- **Customer Support:** 24/7 FAQ routing, knowledge base search, escalate complex cases
- **Research & Development:** Capture sources, synthesize findings with citations, stage approval gates
- **Operations/DevOps:** GitHub workflow automation, health monitoring, runbook execution
- **Bookkeeping:** Expense categorization, transaction reconciliation, month-end summaries

---

## Core Capabilities at a Glance

| Capability | Examples |
|---|---|
| **LLM Routing** | Claude, GPT-4, local Ollama, Qwen, DeepSeek, + 95+ more |
| **Channels** | Discord, Slack, Telegram, Feishu, DingTalk, WhatsApp, Email, QQ, Matrix, Mochat, CLI |
| **Tools** | Web search, file read/write/edit, shell commands, GitHub, scheduling, MCP, subagents |
| **Skills** | Obsidian, memory consolidation, summarize, cron, weather, GitHub, skill creator, TMUX |
| **Cost Control** | Tiered routing, budget caps, escalation gates, token tracking |
| **Security** | Role-based access, tool allowlisting, audit logging, workspace isolation |

---

## Choose Your Path

=== "Local + Cloud LLM (Easiest)"
    - **Setup:** 5-10 minutes
    - **Cost:** $0-50/month
    - **Best for:** Teams, quick experiments, cost-conscious orgs
    
    → [Quick Install & Setup](Nanobot-Quick-Install-Setup.md)

=== "VPS + Local Ollama (Advanced)"
    - **Setup:** 30-45 minutes
    - **Cost:** $20-200/month
    - **Best for:** Privacy, self-hosted inference, scale
    
    → [Build Procedure](Nanobot-Build-Procedure.md)

---

## Support & Community

- **GitHub:** [HKUDS/nanobot](https://github.com/HKUDS/nanobot)
- **Documentation Issues:** File on GitHub
- **Feature Requests:** Open a discussion

---

## Version

This documentation covers **Nanobot v0.1.4+** (released February 2026)

Last updated: **February 25, 2026**
