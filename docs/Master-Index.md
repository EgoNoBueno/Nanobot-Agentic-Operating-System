# Nanobot Agentic Operating System Master Index

**Repository:** [HKUDS/nanobot](https://github.com/HKUDS/nanobot)  
**For Nanobot Version:** v0.1.4 and later  
**Last Updated:** February 25, 2026

## 1. Purpose
This document is the control index for the Nanobot documentation set. It explains how **multiple communication channels, LLM (Large Language Model) governance, and Obsidian memory work together as one coordinated system**—independent of which chat platform (Discord, Slack, Telegram, Feishu, etc.) you use.

### 1.1 Executive Summary (One-Page)
The Nanobot Agentic Operating System is a **three-plane, channel-agnostic operating model**:
- **Control Plane (Multi-Channel):** workflow intake via Discord, Slack, Telegram, Feishu, or other platforms; approvals and operator visibility.
- **Policy Plane (LLM Governance):** model routing (Claude, GPT, Qwen, Ollama, etc.), risk gates, budget controls, and cost optimization.
- **Memory Plane (Obsidian):** durable records, audit trails, recovery evidence, and long-term knowledge indexing.

**Nanobot** is the orchestration engine that powers the system. It listens to messages from any integrated channel, routes them through your policies, executes tasks (shell commands, file operations, web research, API calls, MCP (Model Context Protocol) tools, scheduled jobs), remembers what it learns, and sends results back to your chosen chat platform.

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
7. Nanobot writes structured artifact to Obsidian: **request_id** (unique identifier for this request), **project_id** (what project it belongs to), sources, reasoning, next steps.
8. Operator approves test → change logged in Obsidian for compliance/audit trail.


### 1.2 Nanobot Core Capabilities
Nanobot ships with these built-in features:

**LLM Provider Routing:** (Choose which AI model to use for each task)
- Cloud providers: OpenAI (GPT-4, etc.), Anthropic (Claude), Google Gemini, Cohere, Mistral, DeepSeek, Qwen, Moonshot, MiniMax, VolcEngine
- Local inference via vLLM or Ollama (free, runs on your computer)
- Custom provider support for proprietary models
- Prompt caching (Anthropic) to save on costs
- Automatic cost estimation and token budgeting

**Multi-Channel Integration:** (Listen on any chat platform)
- Discord, Slack, Telegram, Feishu, DingTalk, WhatsApp, Email, QQ, Matrix/Element, Mochat, CLI (local terminal)
- Control who can use nanobot in each channel
- Send and receive media and files
- Handle long messages automatically (Discord splitting, Slack threading, etc.)

**Tool System:** (Actions nanobot can perform)
- **Web Search & Fetch** - Find current information using Brave Search, download full pages
- **File Operations** - Read, write, edit, list files (safely sandboxed)
- **Shell Commands** - Run system commands (restricted, logged, operator-approved)
- **Message/Notifications** - Send alerts and updates across channels
- **Scheduling (Cron)** - Run tasks on a schedule (e.g., "every day at 9 AM")
- **MCP (Model Context Protocol)** - Connect to external databases, file systems, and APIs
- **Subagents** - Launch parallel workers to handle parts of complex tasks
- **GitHub Automation** - Search code repositories, create pull requests (PRs), manage issues
- **Custom Tools** - Build your own extensions

**Pre-Built Skills:** (Ready-to-use bundles)
- **Obsidian** - Read and write notes, search knowledge base
- **Summarize** - Condense long content while keeping the important parts
- **Memory** - Manage conversations, automatically summarize old history
- **Cron** - Schedule recurring tasks
- **Weather** - Fetch weather data
- **Skill Creator** - Write new skills from natural language descriptions
- **ClawHub** - Find and install community-created skills
- **TMUX** - Control terminal sessions for advanced automation

**Reliability & Optimization:**
- Session continuity with automatic memory consolidation
- Heartbeat system for connection health monitoring
- Retry/fallback strategies with degraded mode
- Tool-call validation and error handling
- Structured logging for debugging and audit

### 1.3 Cost Control Summary
Ways to control spending and prevent bill shock:
- **Tiered model routing** (Class A/B/C) - Use cheap models for simple tasks, expensive ones for complex work
- **Budget caps** - Set spending limits by request, channel, or project
- **Approval gates** - Require human sign-off before expensive operations
- **Smart context** - Automatically summarize old conversations to reduce token use
- **Provider choice** - Use budget-friendly models (Qwen, DeepSeek) for routine work; save Claude Opus for complex problems
- **Safety limits** - Automatically stop runaway requests and prevent unexpected charges
- **Weekly reviews** - Track spending patterns and watch for anomalies

### 1.4 Security & Compliance Baseline
How we keep your system safe:
- **Channel isolation** - Teams can't see each other's conversations
- **Role-based access** - Different users have different permissions (Operator, Reviewer, Owner)
- **Sandbox** - Nanobot can only access files in its workspace, can't escape to system files
- **Allowlist** - Only approved people can use dangerous tools (shell commands, APIs, GitHub automation)
- **Audit log** - Every action is recorded with who did it, when, and what it cost
- **Monthly security checks** - Run `nanobot security audit --deep` to verify everything is locked down
- **Separate sessions** - Each user gets their own isolated workspace
- **Secret protection** - API keys and tokens stored securely in `.env` files, never saved in code

### 1.5 Small Business & Enterprise Support Examples
Example ways this AOS supports organizations:
- **Bookkeeping:** Ingest expense receipts, reconcile transactions, categorize spending, publish month-end summaries to Obsidian— all with traceable **request_id** chains for audit.
- **Product Marketing:** Run product-scoped Slack/Discord/Telegram channels, auto-generate content drafts, run competitor research, report KPI deltas with cost-controlled model routing.
- **Research & Development:** Capture source links, synthesize findings with citations, evolve ideas through staged approval gates (hypothesis → design → test → post-mortem), all archived in Obsidian.
- **Customer Support:** Automate FAQ routing, search knowledge base, generate pre-approval responses, escalate complex cases to humans with full context.
- **Operations/DevOps:** Automate GitHub workflow triggers, monitor system health via cron jobs, log incidents with evidence, execute runbooks via shell tools.

## 2. Getting Started Roadmap

Choose your path based on your goal:

### 2.1 Quick Trial (5-10 minutes)
**Goal:** Try nanobot immediately with minimal setup.

1. **[Master Index](Master-Index.md)** (this file) - Understand what you're building (3 planes, capabilities, tools)
2. **[Nanobot Quick Install & Setup](Nanobot-Quick-Install-Setup.md)** - Install and test with default config
3. **[LLM Provider Setup](LLM-Provider-Setup-Guide.md)** - Add one LLM provider (e.g., OpenRouter or local Ollama)
4. **[Multi-Channel Integration](Multi-Channel-Integration-Guide.md)** - Connect one chat platform (Discord or Slack)
5. **[Workflow Examples](Workflow-Examples-and-Recipes.md)** - Copy and run a simple workflow

**Result:** Functional nanobot in <15 minutes. Explore, test, then decide to scale.

### 2.2 Production Setup (1-2 hours)
**Goal:** Deploy a professional system with cost controls, governance, and reliability.

1. **[Master Index](Master-Index.md)** - Understand 3-plane model, RACI roles, cost controls
2. **[Cost Calculator](Cost-Calculator-and-Optimization.md)** - Plan monthly spend, choose provider routing strategy
3. **[Nanobot Build Procedure](Nanobot-Build-Procedure.md)** - Choose Simple Build (local + cloud LLM) or Advanced Build (VPS + Ollama), deploy
4. **Choose your startup path:**
   - **Simple Build (25 min):** [AOS-Startup-Simple-Build](AOS-Startup-Simple-Build.md) - Run nanobot on your computer with cloud AI
   - **Advanced Build (60-90 min):** [AOS-Startup-Advanced-Build](AOS-Startup-Advanced-Build.md) - Run on VPS with local Ollama for 24/7 operation
5. **[LLM Provider Setup](LLM-Provider-Setup-Guide.md)** - Configure all providers you'll use (multi-provider routing)
6. **[Multi-Channel Integration](Multi-Channel-Integration-Guide.md)** - Set up all communication channels (Discord, Slack, Telegram, etc.)
7. **[Essential AOS Skills](Essential-AOS-Skills.md)** - Install core skills (obsidian, memory, summarize, github, cron)
8. **[Tools & Skills Reference](Tools-and-Skills-Reference.md)** - Learn what 14 tools and 9 skills you have available
9. **[Workflow Examples](Workflow-Examples-and-Recipes.md)** - Implement 1-2 real workflows for your use case
10. **[Governance Policies](Governance-Policies-and-Config-Examples.md)** - Set up access control, allowlists, cost caps (optional for solo users; required for teams)

**Result:** Production-grade nanobot with cost controls, memory/audit trails, and multi-channel support.

### 2.3 Multi-Team Deployment (2-4 hours + ongoing)
**Goal:** Deploy across teams with role-based access, approval workflows, and cost chargeback.

Follow **Production Setup** path above, then:

1. **[Governance Policies](Governance-Policies-and-Config-Examples.md)** - Configure RBAC (Owner, Reviewer, Operator roles), channel namespacing, approval gates, cost allocation
2. **[Cost Calculator](Cost-Calculator-and-Optimization.md)** - Set up cost chargeback by team/project
3. **[Security Validation Runbook](Security-Validation-Runbook.md)** - Run monthly security audit, validate allowlists and tool restrictions

**Result:** Nanobot operates safely across multiple teams with clear governance, spending transparency, and audit trails.

---

## 3. Core Documents (Reference)

Use these docs as references when following one of the three roadmaps above.

### Setup & Installation
- [Nanobot Quick Install & Setup](Nanobot-Quick-Install-Setup.md) - Install nanobot, configure LLM provider, verify setup
- [LLM Provider Setup Guide](LLM-Provider-Setup-Guide.md) - Configure Claude, GPT, local Ollama, Qwen, and other models
- [Multi-Channel Integration Guide](Multi-Channel-Integration-Guide.md) - Set up Discord, Slack, Telegram, Feishu, or other platforms

### Operations & Deployment
- [Nanobot Build Procedure](Nanobot-Build-Procedure.md) - Deploy framework (local or VPS)
- [AOS Startup: Simple Build](AOS-Startup-Simple-Build.md) - Run nanobot on your computer with cloud LLM (fast, cheapest to test)
- [AOS Startup: Advanced Build](AOS-Startup-Advanced-Build.md) - Run on VPS with local Ollama (24/7, zero per-query costs)

### Features & Extensibility
- [Tools & Skills Reference](Tools-and-Skills-Reference.md) - All built-in tools, pre-built skills, and how to create custom tools/skills
- [Essential AOS Skills](Essential-AOS-Skills.md) - Recommended pre-built skills for every deployment
- [Advanced Skill Development](Advanced-Skill-Development.md) - Create custom skills beyond pre-built capabilities; operator-injectable skills via Discord

### Workflows & Examples
- [Workflow Examples & Recipes](Workflow-Examples-and-Recipes.md) - Real-world workflows: knowledge consolidation, multi-channel posting, customer support bot, research agent, GitHub automation
- [Cost Calculator & Optimization Guide](Cost-Calculator-and-Optimization.md) - Monthly cost estimation, provider routing for cost savings, budget caps and tracking

### Governance & Policy
- [Governance Policies & Config Examples](Governance-Policies-and-Config-Examples.md) - Role-based access control, channel allowlisting, approval workflows, escalation rules, audit logging, multi-team cost allocation

### Security & Compliance
- [Nanobot Agentic Operating System Security Validation Runbook](Security-Validation-Runbook.md) - Monthly security audit, hardening steps, incident response
- [Emergency Recovery and Troubleshooting](Emergency-Recovery-and-Troubleshooting.md) - System diagnostics, recovery procedures, troubleshooting quick reference

## 4. Interoperability Summary

### 4.1 System Handshake (Channel-Independent)
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

### 4.2 Shared Control Principles
- **Channel agnostic** - Governance works the same regardless of communication platform.
- **Least privilege** - Bot and user permissions restricted to required channels/tools; operator role gates sensitive actions.
- **Context isolation** - Separate sessions by user; channel family prevents cross-project data leakage.
- **Structured logging** - All requests logged with consistent metadata for tracing, cost accounting, and compliance.
- **Model routing** - Cost-optimal model selection per workflow type; expensive models (Opus) reserved for high-complexity work.
- **Tool constraints** - Workspace-scoped filesystem, allowlist-gated shell/MCP/GitHub tools, sensitive action approvals.
- **Durable audit trail** - Obsidian artifacts provide replay, recovery, and compliance evidence.
- **Restore drills** - Periodic validation that artifacts can be replayed and systems recovered.

### 4.3 Security Baseline (Cross-Doc)
- **Configuration-driven access control** - Allowlist of users/channels per tool in `config.json` or environment.
- **Remote access** - VPS-based agent uses SSH + Tailscale tunnel OR public cloud API auth (no direct public exposure).
- **Session isolation** - Each user gets independent session; no cross-user context bleed.
- **Workspace binding** - Agent sandbox respects workspace root; cannot escape to parent directories or system files.
- **MCP security** - Custom auth headers supported for external tool connections; credentials stored in secrets, not code.
- **Tool opt-in** - Tools disabled by default; operator explicitly enables (web search, shell, MCP, etc.) per deployment.
- **Monthly audit** - Run `nanobot security audit --deep` to validate allowlists, disabled tools, workspace constraints, and access logs.
- **Incident response** - Failed security checks automatically escalate; sensitive operations require explicit approval.
- **Secret management** - API keys, model credentials, MCP auth stored in `.env`, never committed to version control.

## 5. Shared Data Contract (Cross-Doc)
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

## 6. Naming and Path Standards

### 6.1 Multi-Channel Naming Convention
Use consistent naming across all integrated channels (Discord, Slack, Telegram, Feishu, etc.) for easy operator recognition:
- **Product channels:** `prd-<project_id>-marketing`, `prd-<project_id>-sales`, `prd-<project_id>-analytics`
- **Bookkeeping channels:** `bk-<project_id>-inbox`, `bk-<project_id>-reconcile`, `bk-<project_id>-reporting`
- **Research channels:** `res-<project_id>-inbox`, `res-<topic>`, `res-syntheses`
- **Control/Admin channels:** `ctl-nanobot-commands`, `ctl-nanobot-alerts`, `ctl-nanobot-audit`

> **Note:** Exact channel naming syntax varies by platform (Discord uses `#`, Slack uses `#`, Telegram uses group names, Feishu uses channel topics, etc.), but adopt this semantic naming across all platforms.

### 6.2 Obsidian Vault Structure (v1)
- **02-Ideas/<project_id>/** - Proposals, hypotheses, early-stage concepts
- **08-Research/<year>/<project_id>/** - Research artifacts, syntheses, cited sources
- **07-Operations/Change-Records/<project_id>/** - Approved changes, executions, outcomes
- **07-Operations/Recovery-Drills/** - Drill evidence, restoration success logs
- **00-System/AOS-Journal/** - Daily operational journaling (if journaler skill is enabled)
- **00-System/Audit-Logs/** - Monthly security audit results and remediation

### 6.3 Web Research Tier Mapping (Channel → Model Class)
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

## 7. RACI Snapshot (Who Owns What)
| Area | Owner | Operator | Reviewer |
|---|---|---|---|
| Discord channel policy | Owner | Operator | Reviewer |
| LLM routing and budgets | Owner | Operator | Reviewer |
| Obsidian metadata and retention | Owner | Operator | Reviewer |
| Incident response execution | Owner | Operator | Reviewer |
| Recovery drill governance | Owner | Operator | Reviewer |

## 8. Review Cadence
- Daily: alerts, budget anomalies, failed runs.
- Weekly: KPI and cost review, blocked workflow review.
- Monthly: restore drill and policy adjustments.
- Quarterly: channel cleanup, model portfolio review, disaster drill.

## 9. Change Management Flow
1. Propose change in Discord change-review channel.
2. Assess risk, cost impact, and rollback plan.
3. Update affected docs (Discord/LLM/Obsidian/Procedure).
4. Execute controlled test.
5. Approve and promote to active.
6. Log outcome and references in Obsidian change records.

## 10. Interoperability Health Checklist
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

## 11. Common Mistakes & Misunderstandings

### ❌ Mistake 1: Thinking Nanobot Requires Discord (It Doesn't)
**Problem:** "We use Slack, not Discord. Can we still use nanobot?"  
**Why:** The documentation mentions Discord often, making it seem like Discord is required  
**Fix:**
- Nanobot works with **any** chat platform: Discord, Slack, Telegram, Feishu, WhatsApp, Email, CLI, etc.
- The choice of platform doesn't change nanobot's core behavior
- Same rules, same capabilities, same governance apply across all platforms
- See [Multi-Channel Integration Guide](Multi-Channel-Integration-Guide.md) for setup instructions for your chosen platform

### ❌ Mistake 2: Confusing "Control Plane" with "Discord Control Channel"
**Problem:** "What's the Control Plane? Is it a Discord channel?"  
**Why:** The term "plane" is architectural jargon that's not self-explanatory  
**Fix:**
- **Control Plane** = the system that routes user requests and manages approvals (exists *across* all channels)
- **Discord control channel** = just one example of where you might receive requests
- Think of it as layers: 
  - **Control** = intake + request routing (works in Discord, Slack, email, CLI, etc.)
  - **Policy** = which AI model to use + cost controls (same rules everywhere)
  - **Memory** = where records go (Obsidian vault, same for everyone)

### ❌ Mistake 3: Setting Up Channels with Wrong Naming Convention
**Problem:** Created channels named `#slack-research`, `#marketing_team`, `#ops`, etc., but system won't recognize them  
**Why:** Channel names don't follow the documented `project_id` pattern  
**Fix:**
- Use pattern: `#[workflow_domain]-[team_or_project]-[optional_detail]`
- Good examples:
  - `#prd-widget-marketing` (product team, research)
  - `#bk-finances` (backlog, financial tasks)
  - `#res-ai-safety` (research, AI safety topic)
  - `#ctl-nanobot` (control, nanobot operations)
- Avoid:
  - Single-word names like `#marketing`
  - Descriptive names like `#team-daily-standup`
  - Special characters or underscores

### ❌ Mistake 4: LLM Routing Not Working - Default Model Used Everywhere
**Problem:** Set up Tier A/B/C routing, but nanobot always uses the default model  
**Why:** Routing rules not actually configured in the active config file  
**Fix:**
1. Check your active config: `nanobot config --show`
2. Verify routing rules exist:
   ```json
   {
     "llm": {
       "routing": {
         "default": "tier_b",
         "rules": [
           { "channel": "#prd-*", "tier": "a" },
           { "channel": "#bk-*", "tier": "c" }
         ]
       }
     }
   }
   ```
3. Restart nanobot after config changes
4. Test: Send a message in `#prd-*` channel and check logs: `nanobot logs | grep "model used"`

### ❌ Mistake 5: Obsidian Vault Paths Don't Match Configuration
**Problem:** "Nanobot says it's writing to my vault, but I don't see files appearing"  
**Why:** Config has relative path (`~/vault`) but actual vault is at absolute path (`/Users/alice/Documents/My Vault`)  
**Fix:**
1. Find actual vault location: Go to Obsidian → Settings → About → Vault location
2. Copy the **exact full path** (including spaces if any)
3. Update config to absolute path:
   ```json
   ❌ "vault_path": "~/vault"
   ✅ "vault_path": "/Users/alice/Documents/My Vault"
   ```
4. On Windows: Use forward slashes `/` or escaped backslashes `\\`:
   ```json
   ✅ "vault_path": "C:/Users/alice/Documents/Nanobot-Vault"
   ✅ "vault_path": "C:\\Users\\alice\\Documents\\Nanobot-Vault"
   ```
5. Restart nanobot and test: `nanobot test-vault`

### ❌ Mistake 6: Budget Controls Set But No Alerts Configured
**Problem:** "We said no more than $100/month, but bill was $500. No one noticed!"  
**Why:** Budget limit set but no alert mechanism to notify when approaching limit  
**Fix:**
```json
❌ Budget with no alerts:
{
  "budget": {
    "monthly_limit": 100
  }
}

✅ Budget with alerts:
{
  "budget": {
    "monthly_limit": 100,
    "alerts": {
      "at_50_percent": { "channel": "#alerts", "message": "50% of budget used" },
      "at_75_percent": { "channel": "#alerts", "message": "75% of budget used" },
      "at_90_percent": { "channel": "#alerts", "message": "WARNING: 90% of budget used" }
    }
  }
}
```
2. Set up calendar reminders to check: `nanobot budget status` weekly
3. Review daily: `nanobot cost report --daily`

---

## 12. Current Status Notes
- Document orchestration links exist across Discord, LLM, and Obsidian guides.
- Discord and Obsidian path placeholders have been aligned to **project_id** and vault v1 structure.
- Remaining work is execution readiness (creating starter MOCs/notes and running first drill).

## 13. Revision History
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

