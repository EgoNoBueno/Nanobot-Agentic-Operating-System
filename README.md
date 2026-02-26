# Nanobot Agentic Operating System Documentation

**Comprehensive operational guides for the Nanobot agent orchestration framework.**

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![GitHub Pages](https://img.shields.io/badge/Docs-GitHub%20Pages-green)](https://egonobueño.github.io/Nanobot-Agentic-Operating-System/)
[![For Nanobot v0.1.4+](https://img.shields.io/badge/Nanobot-v0.1.4%2B-purple)](https://github.com/HKUDS/nanobot)
[![Last Updated](https://img.shields.io/badge/Updated-February%202026-blue)](docs/Master-Index.md)

## Quick Start – Choose Your Path

Pick the path that matches your goal:

### 🚀 **Quick Trial** (5-10 minutes)
Want to try nanobot immediately? Follow this fast path.
- Install nanobot with default settings
- Add one LLM provider (e.g., OpenRouter or local Ollama)
- Connect one chat platform (Discord or Slack)  
- Run a simple workflow
- **Result:** Working nanobot in under 15 minutes

[**Start Quick Trial** →](docs/Master-Index.md#21-quick-trial-5-10-minutes)

### 🏭 **Production Setup** (1-2 hours)
Ready to deploy a professional system with cost controls and governance?
- Understand the 3-plane architecture & RACI roles
- Plan monthly costs and optimize provider routing
- Full deployment (local + cloud LLM, or VPS + Ollama)
- Configure multi-channel communication (Discord, Slack, Telegram, Feishu, etc.)
- Install essential skills and verify system health
- **Result:** Production-grade nanobot ready for real workflows

[**Start Production Setup** →](docs/Master-Index.md#22-production-setup-1-2-hours)

### 👥 **Multi-Team Deployment** (2-4 hours+)
Running nanobot across multiple teams? Set up governance and cost controls.
- Implement role-based access control (Owner, Reviewer, Operator)
- Set up approval workflows and escalation rules
- Configure cost chargeback by team/project  
- Enable monthly security audits
- **Result:** Nanobot operates safely across teams with full transparency

[**Start Multi-Team Setup** →](docs/Master-Index.md#23-multi-team-deployment-2-4-hours--ongoing)

---

## About This Project

**Nanobot** is a Python agent orchestration framework that receives messages from any supported chat platform (Discord, Slack, Telegram, Feishu, DingTalk, Email, Matrix, etc.), routes them through policy constraints, executes tasks with governed access, and logs all activity for audit trails.

This documentation package provides:
- ✅ Step-by-step deployment procedures (local & cloud options)
- ✅ Multi-channel integration guides with platform-specific setup
- ✅ LLM provider configuration (Claude, GPT, local Ollama, DeepSeek, Qwen, etc.)
- ✅ Cost estimation & optimization strategies
- ✅ Real-world workflow examples (knowledge consolidation, customer support, research automation, GitHub automation)
- ✅ Governance policies and role-based access control patterns
- ✅ Security audit procedures & hardening guidelines
- ✅ Troubleshooting & recovery runbooks

## Documentation Structure

All documentation is available in two formats:

1. **Markdown files** — `/docs` folder in this repository
2. **Professional website** — [GitHub Pages](https://egonobueño.github.io/Nanobot-Agentic-Operating-System/) (with search, dark mode, mobile-friendly)

### Core Guides

| Guide | Purpose | Time |
|-------|---------|------|
| [Master Index](docs/Master-Index.md) | Architecture, capabilities, RACI roles, naming standards | 5 min |
| [Quick Install](docs/Nanobot-Quick-Install-Setup.md) | Fast install with one LLM provider | 5 min |
| [Build Procedure](docs/Nanobot-Build-Procedure.md) | Deployment options: local vs. VPS, simple vs. advanced | 30-60 min |
| [LLM Provider Setup](docs/LLM-Provider-Setup-Guide.md) | Configure Claude, GPT, Ollama, Qwen, DeepSeek, etc. | 15 min each |
| [Multi-Channel Integration](docs/Multi-Channel-Integration-Guide.md) | Set up Discord, Slack, Telegram, Feishu | 15-30 min each |
| [AOS Startup Procedure](docs/AOS-Startup-Procedure.md) | Launch and verify healthy system operation | 10-15 min |
| [Tools & Skills Reference](docs/Tools-and-Skills-Reference.md) | 14 built-in tools + 9 pre-built skills + custom extensions | Reference |
| [Essential Skills](docs/Essential-AOS-Skills.md) | 5 core skills every deployment needs | 30 min |
| [Workflow Examples](docs/Workflow-Examples-and-Recipes.md) | 6 real workflows ready to copy & customize | Reference |
| [Cost Calculator](docs/Cost-Calculator-and-Optimization.md) | Monthly cost estimation, provider routing, savings strategies | 15 min |
| [Governance Policies](docs/Governance-Policies-and-Config-Examples.md) | RBAC, approval workflows, escalation rules, cost chargeback | 30 min |
| [Security Runbook](docs/Security-Validation-Runbook.md) | Monthly security audit procedures | Monthly |

## Using This Documentation

### For Users
1. Start with the **Master Index** to understand the 3-plane architecture
2. Follow one of the three paths above (Quick Trial, Production, or Multi-Team)
3. Refer to specific guides as needed (LLM setup, channel integration, workflow examples)
4. Use cost calculator before scaling features
5. Run monthly security audits using the Security Runbook

### For Developers
- All guides include configuration examples (JSON, Python, YAML)
- Code snippets are tested against Nanobot v0.1.4+
- Contributing improvements? See [Contributing](#contributing) below

### For Organizations
- Governance Policies guide includes RBAC examples by org type (startup, scale-up, enterprise)
- Workflow Examples show how to implement knowledge management, customer support, and GitOps automation
- Cost calculator helps budget multi-team deployments

## Features Documented

### Core Framework
- 14 built-in tools (web search, file operations, shell execution, message delivery, scheduling, MCP integration, subagents, GitHub automation)
- 9 pre-built skills (Obsidian vault management, memory consolidation, content summarization, cron scheduling, weather, GitHub operations, skill marketplace integration, TMUX control, skill auto-generation)
- 100+ LLM models via unified provider routing
- 12+ communication channels with multi-channel simultaneous operation
- Cost controls: tiered model routing, budget caps, escalation gates
- Session continuity with automatic memory consolidation
- Structured audit logging for compliance

### Deployment Options
- **Local:** Laptop/desktop with cloud LLM provider
- **VPS:** Self-hosted Ollama for local inference + VPS runtime
- **Hybrid:** Mix of cloud providers and local inference

### Security & Compliance
- Channel-level access control and allowlisting
- Role-based permissions (Owner, Reviewer, Operator)
- Workspace binding to prevent filesystem escape
- Secret management via .env files (never committed)
- Monthly security validation procedures
- Audit trails with request IDs and cost tracking

## Getting Help

### I'm stuck on:
- **Installation?** → Start with [Quick Install](docs/Nanobot-Quick-Install-Setup.md)
- **LLM provider setup?** → See [LLM Provider Guide](docs/LLM-Provider-Setup-Guide.md)
- **Connecting Discord/Slack?** → See [Multi-Channel Integration](docs/Multi-Channel-Integration-Guide.md)
- **Understanding costs?** → See [Cost Calculator](docs/Cost-Calculator-and-Optimization.md)
- **Setting up governance?** → See [Governance Policies](docs/Governance-Policies-and-Config-Examples.md)

### Need to troubleshoot?
- Check [Security Validation Runbook](docs/Security-Validation-Runbook.md) for diagnostics
- Review [Workflow Examples](docs/Workflow-Examples-and-Recipes.md) for working configurations
- Search the [online documentation site](https://egonobueño.github.io/Nanobot-Agentic-Operating-System/) (Ctrl+K / Cmd+K to open search)

## Contributing

This documentation is maintained for the open-source Nanobot framework. We welcome:
- **Bug reports** in the documentation (links, outdated info, unclear instructions)
- **Improvements** to guides and examples
- **Additional workflows** that solve real problems
- **Translations** to other languages
- **Platform-specific guidance** for new channels

Please open an issue or pull request in this repository. Include:
- What guide/section needs improvement
- What's unclear or outdated
- Suggested fix or improvement

## Project Status

- ✅ **Complete:** Master Index, Quick Install, Build Procedure, LLM Provider Setup, Multi-Channel Integration, AOS Startup, Tools & Skills Reference, Essential Skills
- ✅ **Complete:** Cost Calculator, Workflow Examples, Governance Policies, Security Runbook
- ✅ **Complete:** Professional documentation site (search, dark mode, mobile-responsive)
- 🔄 **In Progress:** Translations (community-contributed)
- 📋 **Planned:** Video tutorials, interactive cost calculator

## License

This documentation is licensed under the **MIT License**. See [LICENSE](LICENSE) for details.

The Nanobot framework itself is maintained by [HKUDS/nanobot](https://github.com/HKUDS/nanobot).

## Related Resources

- **Official Nanobot Repository:** [github.com/HKUDS/nanobot](https://github.com/HKUDS/nanobot)
- **Nanobot Version:** v0.1.4 and later
- **Python Version:** 3.10+
- **Documentation Last Updated:** February 25, 2026

## Acknowledgments

This comprehensive documentation set was created to bridge the gap between Nanobot's powerful capabilities (14 tools, 9 skills, 100+ models, 12+ channels) and the documentation available to users. Thanks to anyone who uses, tests, and improves these guides.

---

**Ready to get started?** [Choose your path above](#quick-start--choose-your-path) or jump to the [Master Index](docs/Master-Index.md).
