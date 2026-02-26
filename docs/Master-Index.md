# Nanobot Agentic Operating System Master Index

**Repository:** [HKUDS/nanobot](https://github.com/HKUDS/nanobot)  
**For Nanobot Version:** v0.1.4 and later  
**Last Updated:** February 25, 2026

## 1. Purpose
This document is the control index for the Nanobot operational document set. It shows how **multiple communication channels, LLM governance, and Obsidian memory work together as one coordinated system**—independent of which chat platform (Discord, Slack, Telegram, Feishu, etc.) operators use.

### 1.1 Executive Summary (One-Page)
The Nanobot Agentic Operating System is a **three-plane, channel-agnostic operating model**:
- **Control Plane (Multi-Channel):** workflow intake via Discord, Slack, Telegram, Feishu, or other platforms; approvals and operator visibility.
- **Policy Plane (LLM Governance):** model routing (Claude, GPT, Qwen, Ollama, etc.), risk gates, budget controls, and cost optimization.
- **Memory Plane (Obsidian):** durable records, audit trails, recovery evidence, and long-term knowledge indexing.

**Nanobot** is the agent orchestration runtime: it receives messages from any integrated channel, routes them through policy constraints, executes tasks (shell commands, file operations, web research, API calls, MCP tools, scheduled jobs), manages memory, and returns results back to the operator's chosen platform.

**How it works in practice:**
1. Operator sends request in a policy-approved channel (e.g., Discord **#prd-widget-marketing**, Slack **#prd-widget-marketing**, Telegram group, or any supported platform).
2. Nanobot receives the message via channel integration.
3. LLM policy routes to appropriate model class (Claude, GPT, local Ollama, etc.) with cost/security constraints.
4. Nanobot executes tasks: web search (Brave API), file read/write/edit, shell commands, GitHub automation, weather data, MCP tool calls, cron tasks, or spawns parallel subagents.
5. Results are logged with structured metadata (**request_id**, **project_id**, tokens, cost, timing).
6. Response is posted back to the requesting channel with citations, confidence notes, and references.
7. Structured artifacts are written to Obsidian for replay, audit, and recovery.

#### Example Scenario: Product Campaign Research to Action (Channel-Independent)
1. Operator asks in **Slack #prd-widget-marketing:** "Find 3 competitor campaigns this week and propose one test we can run."
2. Nanobot receives message via Slack channel integration.
3. Policy routes to model trained on current data (e.g., Claude Opus via OpenRouter).
4. Nanobot invokes: **web_search** tool (Brave API) → retrieves competitor campaign data with cost/context controls.
5. Web results → **summarize** skill → synthesizes findings with citations.
6. Nanobot posts back to Slack with recommendation, source links, and confidence assessment.
7. Nanobot writes structured artifact to Obsidian: **request_id**, **project_id**, sources, reasoning, next steps.
8. Operator approves test → change logged in Obsidian for compliance/audit trail.


### 1.2 Nanobot Core Capabilities
Nanobot ships with these built-in features:

**LLM Provider Routing:**
- OpenAI (GPT-4, etc.), Anthropic (Claude), Google Gemini, Cohere, Mistral, DeepSeek, Qwen, Moonshot, MiniMax, VolcEngine
- Local inference via vLLM or Ollama
- Custom provider support for proprietary models
- Prompt caching (Anthropic) for cost optimization
- Token budgeting and cost estimation

**Multi-Channel Integration:**
- Discord, Slack, Telegram, Feishu, DingTalk, WhatsApp, Email, QQ, Matrix/Element, Mochat, CLI (local)
- Channel-level access control and allowlists
- Media/file support across platforms
- Long message handling (Discord splitting, Slack threading, etc.)

**Tool System (What Nanobot Can Execute):**
- **Web Search & Fetch** - Real-time search via Brave API, page retrieval
- **File Operations** - Read, write, edit, list files (workspace-scoped)
- **Shell Commands** - Execute system commands (operator-restricted)
- **Message/Notifications** - Send alerts, escalations, cross-channel notifications
- **Scheduling (Cron)** - Run tasks on recurring schedules
- **MCP (Model Context Protocol)** - Integrate external tools/services (filesystem, web, databases, APIs)
- **Subagents** - Spawn parallel agent instances for complex/delegated work
- **GitHub Automation** - Search repos, create/update PRs, manage issues, trigger workflows
- **Custom Tools** - Extend with user-defined tool implementations

**Pre-Built Skills:**
- **Obsidian** - Read/write vault entries, query knowledge base
- **Summarize** - Content condensation with context preservation
- **Memory** - Session management, conversation consolidation
- **Cron** - Scheduled task definitions and execution
- **Weather** - Weather data retrieval
- **Skill Creator** - Auto-generate new skills from natural language
- **ClawHub** - Search public skill marketplace and install skills
- **TMUX** - Terminal multiplexer control for advanced automation

**Reliability & Optimization:**
- Session continuity with automatic memory consolidation
- Heartbeat system for connection health monitoring
- Retry/fallback strategies with degraded mode
- Tool-call validation and error handling
- Structured logging for debugging and audit

### 1.3 Cost Control Summary
Primary cost controls used by this AOS:
- **Tiered model routing** (Class A/B/C) by workflow type and risk level.
- **Budget caps** at request, workflow/channel, and project levels.
- **Escalation gates** - Approval required for high-cost model classes or complexity triggers.
- **Context optimization** - Memory summarization, prompt caching, and efficient retrieval to limit token burn.
- **Provider selection** - Route to cheaper models (Qwen, DeepSeek) for routine tasks; reserve expensive models (Claude Opus) for complex work.
- **Fallback/degraded mode** - Stop runaway retries and implement cost circuit-breakers.
- **Weekly KPI review** - Track cost per successful workflow, escalation rate, and model utilization.

### 1.4 Security & Compliance Baseline
Primary security & compliance protocols used by this AOS:
- **Channel isolation** by project/workflow to prevent context bleed between teams.
- **Role-based access control** - Operator, Reviewer, Owner roles with specific permissions.
- **Workspace binding** - Agent sandbox respects workspace directory boundaries; no filesystem escape.
- **Allowlist enforcement** - Only specified users/channels can invoke sensitive tools (shell, MCP, GitHub automation).
- **Audit trail** - All requests logged with **request_id**, operator identity, timestamp, tool usage, and cost.
- **Monthly security audit** - Run `nanobot security audit --deep` to validate config, allowlists, and tool constraints.
- **Trust boundaries** - Split execution contexts when operators are mutually untrusted; separate sessions per user.
- **Secret management** - API keys, tokens, MCP auth headers stored in `.env` files, never committed.

### 1.5 Small Business & Enterprise Support Examples
Example ways this AOS supports organizations:
- **Bookkeeping:** Ingest expense receipts, reconcile transactions, categorize spending, publish month-end summaries to Obsidian— all with traceable **request_id** chains for audit.
- **Product Marketing:** Run product-scoped Slack/Discord/Telegram channels, auto-generate content drafts, run competitor research, report KPI deltas with cost-controlled model routing.
- **Research & Development:** Capture source links, synthesize findings with citations, evolve ideas through staged approval gates (hypothesis → design → test → post-mortem), all archived in Obsidian.
- **Customer Support:** Automate FAQ routing, search knowledge base, generate pre-approval responses, escalate complex cases to humans with full context.
- **Operations/DevOps:** Automate GitHub workflow triggers, monitor system health via cron jobs, log incidents with evidence, execute runbooks via shell tools.

## 2. Core Documents

### Setup & Installation
- [Nanobot Quick Install & Setup](Nanobot-Quick-Install-Setup.md) ⭐ **Start here** - Install nanobot, configure LLM provider, verify setup
- [LLM Provider Setup Guide](LLM-Provider-Setup-Guide.md) - Configure Claude, GPT, local Ollama, Qwen, and other models
- [Multi-Channel Integration Guide](Multi-Channel-Integration-Guide.md) - Set up Discord, Slack, Telegram, Feishu, or other platforms

### Operations & Deployment
- [Nanobot Build Procedure](Nanobot-Build-Procedure.md) - Deploy to local or VPS (simple and advanced paths)
- [AOS Startup Procedure](AOS-Startup-Procedure.md) - Bring system from power-down to fully operational

### Features & Extensibility
- [Tools & Skills Reference](Tools-and-Skills-Reference.md) - All built-in tools, pre-built skills, and how to create custom tools/skills
- [Essential AOS Skills](Essential-AOS-Skills.md) - Recommended pre-built skills for every deployment

### Workflows & Examples
- [Workflow Examples & Recipes](Workflow-Examples-and-Recipes.md) - Real-world workflows: knowledge consolidation, multi-channel posting, customer support bot, research agent, GitHub automation
- [Cost Calculator & Optimization Guide](Cost-Calculator-and-Optimization.md) - Monthly cost estimation, provider routing for cost savings, budget caps and tracking

### Governance & Policy
- [Governance Policies & Config Examples](Governance-Policies-and-Config-Examples.md) - Role-based access control, channel allowlisting, approval workflows, escalation rules, audit logging, multi-team cost allocation

### Security & Compliance
- [Nanobot Agentic Operating System Security Validation Runbook](Security-Validation-Runbook.md) - Monthly security audit, hardening steps, incident response

## 3. Interoperability Summary

### 3.1 System Handshake (Channel-Independent)
1. **Operator sends request** in any integrated channel (Discord, Slack, Telegram, Feishu, etc.) using chat interface.
2. **Channel Integration** receives message and forwards to Nanobot via message bus event (InboundMessage).
3. **Agent Loop** receives event; builds context: conversation history, user permissions, LLM policy, available tools.
4. **LLM Routing** applies policy: selects model (Claude, GPT, local Ollama, etc.), enforces cost/risk constraints, prepares context.
5. **Tool Execution** loop (up to 40 iterations):
   - LLM returns tool calls (web search, file read, shell command, MCP call, spawn subagent, etc.)
   - Each tool executes; results logged with **request_id**, tokens, latency, cost
   - Tool outputs returned to LLM for next iteration
   - LLM returns final response or tool error handling
6. **Response Composition** - Nanobot formats response with citations, confidence, metadata, next steps
7. **Memory Logging** - Structured metadata written to Obsidian: **request_id**, **project_id**, **workflow_domain**, tokens, cost, tools used
8. **Channel Output** - Response posted back to original channel (Discord, Slack, Telegram, etc.) with reference links

### 3.2 Shared Control Principles
- **Channel agnostic** - Governance works the same regardless of communication platform.
- **Least privilege** - Bot and user permissions restricted to required channels/tools; operator role gates sensitive actions.
- **Context isolation** - Separate sessions by user; channel family prevents cross-project data leakage.
- **Structured logging** - All requests logged with consistent metadata for tracing, cost accounting, and compliance.
- **Model routing** - Cost-optimal model selection per workflow type; expensive models (Opus) reserved for high-complexity work.
- **Tool constraints** - Workspace-scoped filesystem, allowlist-gated shell/MCP/GitHub tools, sensitive action approvals.
- **Durable audit trail** - Obsidian artifacts provide replay, recovery, and compliance evidence.
- **Restore drills** - Periodic validation that artifacts can be replayed and systems recovered.

### 3.3 Security Baseline (Cross-Doc)
- **Configuration-driven access control** - Allowlist of users/channels per tool in `config.json` or environment.
- **Remote access** - VPS-based agent uses SSH + Tailscale tunnel OR public cloud API auth (no direct public exposure).
- **Session isolation** - Each user gets independent session; no cross-user context bleed.
- **Workspace binding** - Agent sandbox respects workspace root; cannot escape to parent directories or system files.
- **MCP security** - Custom auth headers supported for external tool connections; credentials stored in secrets, not code.
- **Tool opt-in** - Tools disabled by default; operator explicitly enables (web search, shell, MCP, etc.) per deployment.
- **Monthly audit** - Run `nanobot security audit --deep` to validate allowlists, disabled tools, workspace constraints, and access logs.
- **Incident response** - Failed security checks automatically escalate; sensitive operations require explicit approval.
- **Secret management** - API keys, model credentials, MCP auth stored in `.env`, never committed to version control.

## 4. Shared Data Contract (Cross-Doc)
Use these fields consistently across Discord logs, LLM telemetry, and Obsidian notes:
- **request_id**
- **project_id**
- **workflow_domain**
- **owner** or **requester**
- **status**
- **timestamp_utc**
- **model_class** and **model_name** (when applicable)
- **input_tokens** / **output_tokens** / **estimated_cost** (LLM executions)
- **reference** (link to note/runbook/output)

> **Plain-English Note:** Using the same core fields across systems makes tracing and recovery much faster.

## 5. Naming and Path Standards

### 5.1 Multi-Channel Naming Convention
Use consistent naming across all integrated channels (Discord, Slack, Telegram, Feishu, etc.) for easy operator recognition:
- **Product channels:** `prd-<project_id>-marketing`, `prd-<project_id>-sales`, `prd-<project_id>-analytics`
- **Bookkeeping channels:** `bk-<project_id>-inbox`, `bk-<project_id>-reconcile`, `bk-<project_id>-reporting`
- **Research channels:** `res-<project_id>-inbox`, `res-<topic>`, `res-syntheses`
- **Control/Admin channels:** `ctl-nanobot-commands`, `ctl-nanobot-alerts`, `ctl-nanobot-audit`

> **Note:** Exact channel naming syntax varies by platform (Discord uses `#`, Slack uses `#`, Telegram uses group names, Feishu uses channel topics, etc.), but adopt this semantic naming across all platforms.

### 5.2 Obsidian Vault Structure (v1)
- **02-Ideas/<project_id>/** - Proposals, hypotheses, early-stage concepts
- **08-Research/<year>/<project_id>/** - Research artifacts, syntheses, cited sources
- **07-Operations/Change-Records/<project_id>/** - Approved changes, executions, outcomes
- **07-Operations/Recovery-Drills/** - Drill evidence, restoration success logs
- **00-System/AOS-Journal/** - Daily operational journaling (if journaler skill is enabled)
- **00-System/Audit-Logs/** - Monthly security audit results and remediation

### 5.3 Web Research Tier Mapping (Channel → Model Class)
When enabling web search (Brave API), use these tier guidelines to balance cost and depth:

| Channel Family | Research Depth | Cost Tier | Model Tier | Notes |
|---|---|---|---|---|
| `#bk-*` | Metadata only | R1 | A | Snippet-level financial data, no full-page retrieval |
| `#prd-*-analytics` | Bounded data | R1 | A | Industry metrics, light competitive analysis |
| `#prd-*-marketing` | Full-page | R2 | B | Content research, campaign analysis, limited depth |
| `#res-*` | Deep research | R2 | B | Multi-source synthesis, citations required |
| `#res-* (escalated)` | Exhaustive | R3 | C | Comprehensive analysis; requires Owner approval |
| `#ctl-*` | Policy-limited | Restricted | A only | Control channel; internet access optional |

> **Plain-English Note:** R1 = cheap/shallow, R2 = moderate cost/depth, R3 = expensive/comprehensive. Model tier (A/B/C) controls which LLM is used; escalate to higher-cost models only for complex work.

## 6. RACI Snapshot (Who Owns What)
| Area | Owner | Operator | Reviewer |
|---|---|---|---|
| Discord channel policy | Owner | Operator | Reviewer |
| LLM routing and budgets | Owner | Operator | Reviewer |
| Obsidian metadata and retention | Owner | Operator | Reviewer |
| Incident response execution | Owner | Operator | Reviewer |
| Recovery drill governance | Owner | Operator | Reviewer |

## 7. Review Cadence
- Daily: alerts, budget anomalies, failed runs.
- Weekly: KPI and cost review, blocked workflow review.
- Monthly: restore drill and policy adjustments.
- Quarterly: channel cleanup, model portfolio review, disaster drill.

## 8. Change Management Flow
1. Propose change in Discord change-review channel.
2. Assess risk, cost impact, and rollback plan.
3. Update affected docs (Discord/LLM/Obsidian/Procedure).
4. Execute controlled test.
5. Approve and promote to active.
6. Log outcome and references in Obsidian change records.

## 9. Interoperability Health Checklist
- [ ] Channel naming follows the **project_id** pattern.
- [ ] Each active channel family maps to an Obsidian path.
- [ ] Each workflow domain maps to a default LLM class.
- [ ] Escalation and fallback are configured and tested.
- [ ] Required shared metadata fields appear in logs/notes.
- [ ] Monthly restore drill completed with evidence.
- [ ] Gateway loopback and remote-access hardening are validated.
- [ ] DM pairing policy and allowlists are active and tested.
- [ ] Trust-boundary and sandbox policies are validated for shared channels.
- [ ] Monthly security audit findings are logged and remediated.

## 10. Current Status Notes
- Document orchestration links exist across Discord, LLM, and Obsidian guides.
- Discord and Obsidian path placeholders have been aligned to **project_id** and vault v1 structure.
- Remaining work is execution readiness (creating starter MOCs/notes and running first drill).

## 11. Revision History
| Date | Version | Change |
|---|---|---|
| 2026-02-25 | 2.0.0 | **Major update:** Made AOS system channel-agnostic (Discord/Slack/Telegram/Feishu/etc.); documented all actual nanobot capabilities (LLM routing, tools, skills, MCP, web search, subagents, cron); updated Core Documents section with new guides; expanded security baseline to cover all deployment scenarios; clarified 3-plane governance model applies to any channel |
| 2026-02-24 | 1.1.10 | Added Discord Messaging Setup Guide to core docs for onboarding field collection and allowlist formatting |
| 2026-02-24 | 1.1.9 | Added Nanobot Onboarding Guide link for manual-mode local-gateway onboarding standard |
| 2026-02-23 | 1.1.8 | Added AOS Project Startup Guide for standardized new-project onboarding |
| 2026-02-23 | 1.1.7 | Renamed document title to "Nanobot Agentic Operating System Master Index" for project-name consistency |
| 2026-02-23 | 1.1.6 | Added explicit Nanobot orchestration-runtime role statement to Executive Summary |
| 2026-02-23 | 1.1.5 | Added concrete "How it works in practice" scenario for product campaign research-to-action flow |
| 2026-02-23 | 1.1.4 | Added executive summary with operating model, cost controls, security protocol overview, and small-business support examples |
| 2026-02-23 | 1.1.3 | Added compact R1/R2/R3 internet research tier mapping table for operator quick reference |
| 2026-02-23 | 1.1.2 | Added Master Index reference to LLM Internet Research Policy controls |
| 2026-02-23 | 1.1.1 | Added Security Validation Runbook to core document set |
| 2026-02-23 | 1.1.0 | Security Hardening v1: added cross-doc security baseline and enforcement checklist items |
| 2026-02-23 | 1.0.0 | Initial master index: interoperability map, shared contract, standards, and review cadence |

