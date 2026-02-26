# Security & Governance: Policies, Config & Validation

**Complete reference for governance frameworks, access control patterns, security validation procedures, and compliance auditing.**

Enforce organizational guardrails, access control, cost accountability, and security controls through governance policies and regular validation runbooks.

## Document Control
- **Owner:**
- **Version:** 2.1.0
- **Last Updated:** 2026-02-26
- **Status:** Active

---

## Quick Navigation

- **Part 1: Governance Model & Policies** — Access control, policy enforcement, cost routing
- **Part 2: Security Validation & Auditing** — Monthly validation procedures, compliance checks
- **Part 3: Common Mistakes & Solutions** — 12 governance mistakes + 6 security mistakes with fixes

---

# Part 1: Governance Model & Policies

## 1. Overview: The 3-Plane Governance Model

**Plane 1: Control** — Who can do what? (access control, tool allowlisting)  
**Plane 2: Policy** — How are decisions made? (routing rules, escalation, approval flows)  
**Plane 3: Memory** — What does the system know? (vault scope, retention policies)

All three planes are **channel-aware** (work in Discord, Slack, Telegram, etc.).

---

## 2. Access Control Patterns

### Pattern A: Role-Based Tool Allowlisting (RBAC)

RBAC = Role-Based Access Control. (Different people get different permissions based on their job role.)

**Use Case:** Different teams get different tools.

**Config example:**

```json
{
  "teams": {
    "engineering": {
      "channels": ["#dev", "#devops"],
      "allowed_tools": [
        "web_search",
        "github_*",
        "shell_exec",
        "file_*",
        "mcp_call",
        "cron_schedule"
      ],
      "forbidden_tools": ["shell_exec:rm", "shell_exec:sudo"],
      "llm_tier": "b"
    },
    "marketing": {
      "channels": ["#marketing", "#content-hub"],
      "allowed_tools": [
        "web_search",
        "file_read",
        "message_send",
        "obsidian"
      ],
      "forbidden_tools": ["shell_exec", "github_*"],
      "llm_tier": "c"
    },
    "executive": {
      "channels": ["#exec", "#leadership"],
      "allowed_tools": [
        "web_search",
        "file_read",
        "message_send",
        "summarize"
      ],
      "forbidden_tools": ["shell_exec", "cron", "mcp"],
      "llm_tier": "a",
      "escalation": "required"
    }
  }
}
```

**Behavior:**

| Team | Can use | Cannot use | LLM |
|---|---|---|---|
| **Engineering** | All tools except destructive shell | rm, sudo, reboot | Tier B |
| **Marketing** | Search, read, message, obsidian | Shell, GitHub, MCP | Tier C |
| **Executive** | Search, read, message, summarize | Shell, GitHub, cron, MCP | Tier A |

**Enforcement:**

If marketing user tries `@BotName shell_exec: ls /`:
```
❌ Tool 'shell_exec' not allowed for #marketing team.
   Allowed tools: web_search, file_read, message_send, obsidian
   Contact: @engineering-lead for access request
```

### Pattern B: Channel-Level Tool Restrictions

**Use Case:** Public channels more restricted than private.

```json
{
  "channels": {
    "#general": {
      "tools": ["web_search", "message_send"],
      "visibility": "public"
    },
    "#dev": {
      "tools": ["web_search", "shell_exec", "github_*", "mcp_call"],
      "visibility": "team"
    },
    "#devops-secrets": {
      "tools": ["shell_exec", "file_*", "mcp_call"],
      "visibility": "restricted",
      "requires_approval": true,
      "approval_group": "@devops-leads"
    }
  }
}
```

**Behavior:**

- `#general`: Only safe, read-only tools
- `#dev`: Dev-friendly tools, no approval needed
- `#devops-secrets`: Full power, but every action logged + approved

### Pattern C: Per-User Rate Limiting

**Use Case:** Prevent cost overruns and abuse.

```json
{
  "rate_limits": {
    "default_user": {
      "tokens_per_day": 50000,
      "requests_per_hour": 60,
      "web_search_per_day": 20
    },
    "power_users": {
      "tokens_per_day": 200000,
      "requests_per_hour": 300,
      "web_search_per_day": 100
    },
    "limited_users": {
      "tokens_per_day": 5000,
      "requests_per_hour": 10,
      "web_search_per_day": 5
    }
  },
  "team_quotas": {
    "engineering": {
      "daily_tokens": 500000,
      "monthly_budget": 100
    },
    "marketing": {
      "daily_tokens": 100000,
      "monthly_budget": 30
    }
  }
}
```

**Behavior:**

User exceeds daily limit:
```
⚠️ Daily token limit reached (50,000/50,000)
   Your limit resets tomorrow at 12:00am UTC
   Need more capacity? Request upgrade: https://...
```

Team exceeds budget:
```
💰 Team monthly budget exhausted ($100/$100)
   Remaining requests will use local Ollama (free)
   Budget resets in 5 days
```

---

## 3. Risk Tier Framework (Who Needs Approval?)

**Goal:** Classify actions by how easy they are to undo. (Reversibility = can you fix it if something goes wrong?)

If an action is hard to undo or could cause problems, it needs human approval first.

### Risk Tiers Overview

Risk is measured by **Magnitude of Irreversibility**. If an action is hard to undo or compromises core security, it's high-risk.

| Tier | Automation | Description | Examples | Approval? |
|------|-----------|-------------|----------|-----------|
| **Tier 0: Safe** | ✅ Autonomous | Read-only, internal logs, low-impact data | Checking weather, reading Obsidian notes, summarizing public PDFs | No |
| **Tier 1: Minor** | ✅ Autonomous | State changes with undo capability | Creating folders, moving temp files, adding Discord reactions | No (log only) |
| **Tier 2: Moderate** | ⚠️ Soft-Pause | Actions affecting external data or "shadow" systems | Sending email to known contact, deleting temp folder, running scheduled reports | Conditional* |
| **Tier 3: High** | 🛑 Hard-Pause | Actions affecting identity or security | Modifying Discord permissions, changing .env files, new SSH node access | **Yes (Approver role)** |
| **Tier 4: Critical** | 🔒 Locked | Destructive or system-wide changes | Deleting database, mass-sending external messages, changing Primary Operator ID | **Yes (MFA/Admin only)** |

\*Conditional = approved automatically if target is allowlisted; requires approval otherwise

### Implementation Example

```json
{
  "risk_tiers": {
    "tier_0": {
      "automation": "autonomous",
      "description": "Read-only, safe",
      "examples": ["web_search", "obsidian_read", "summarize"],
      "approval_required": false
    },
    "tier_1": {
      "automation": "autonomous", 
      "description": "Minor changes, recoverable",
      "examples": ["file_create", "folder_create", "message_react"],
      "approval_required": false,
      "audit_logging": true
    },
    "tier_2": {
      "automation": "soft_pause",
      "description": "External or shadow data impacts",
      "examples": ["message_send:email", "file_delete:temp", "cron:schedule"],
      "approval_required": "conditional",
      "auto_approve_if": "target_in_allowlist"
    },
    "tier_3": {
      "automation": "hard_pause",
      "description": "Security/identity impacts",
      "examples": ["permission_modify", "config_write:secrets", "node_authorize"],
      "approval_required": true,
      "approver_role": "AOS Approver",
      "timeout_seconds": 300
    },
    "tier_4": {
      "automation": "locked",
      "description": "Destructive, system-wide",
      "examples": ["database_truncate", "message_broadcast:external", "operator_change"],
      "approval_required": true,
      "approver_role": "Admin",
      "requires_mfa": true,
      "requires_human_review": true
    }
  }
}
```

### How Tiers Trigger Approval Flows

When user requests a **Tier 3** action:

```
User: @BotName ssh authorize new-node 192.168.1.50

🛑 HIGH-RISK ACTION: Node Authorization
   Requested by: @alice (engineering team)
   Action: Authorize new SSH access to 192.168.1.50
   
   Risk Level: Tier 3 (security impact)
   Reversibility: Revocable but requires cleanup
   
   ✋ Approval Required from: @AOS-Approver role
   Timeout: 5 minutes
   
[Review Details] [Approve] [Deny]
```

When user requests a **Tier 4** action:

```
User: @BotName database truncate production_logs

🔒 CRITICAL ACTION: Database Truncate
   Requested by: @alice
   Action: PERMANENTLY DELETE production_logs
   
   Risk Level: Tier 4 (destructive, irreversible)
   Data Loss: Irreversible
   
   ⛔ LOCKED: Only Admin role with MFA can approve
   Requires: Multi-factor authentication
   Approval Timeout: 1 hour (imminent action warning)
   
   WARNING: This action cannot be undone!
   All audit trails will be deleted.
   
[View Details] [Request Admin] [Cancel]
```

---

## 4. Channel Naming Convention & Governance

**Goal:** Channel names automatically enforce policy.

### Standard Naming Scheme

```
{scope}-{purpose}-{privacy}

Scope: prd, bk, res, ctl, fin, hr, lgl
Purpose: any lowercase string
Privacy: none (public), -private, -secret
```

**Examples:**

| Channel | Scope | Team | Tools Allowed | Privacy |
|---|---|---|---|---|
| `#prd-architecture` | prd | engineering | All dev tools | Public |
| `#prd-secrets` | prd | engineering | Full power | Restricted |
| `#bk-payroll` | bk | hr | Read-only files | Restricted |
| `#res-customer-data` | res | research | Web search, file read | Restricted |
| `#ctl-emergency` | ctl | ops | All tools, instant approval | Restricted |
| `#fin-quarterly` | fin | finance | Summarize, search | Private |

### Config-based Routing

```json
{
  "channel_policy": {
    "naming_scheme": "{scope}-{purpose}[-{privacy}]",
    "routing_rules": [
      {
        "pattern": "prd-*",
        "team": "engineering",
        "tools": "engineering_allowed",
        "tier": "b",
        "cost_center": "engineering"
      },
      {
        "pattern": "bk-*",
        "team": "operations",
        "tools": "operations_allowed",
        "tier": "c",
        "cost_center": "operations"
      },
      {
        "pattern": "*-secret",
        "requires_approval": true,
        "audit_level": "verbose",
        "approval_group": "@security-team"
      },
      {
        "pattern": "ctl-*",
        "tier": "a",
        "approval": "instant",
        "cost_center": "operations"
      }
    ]
  }
}
```

**Behavior:**

When user creates channel `#prd-auth-secret`:
```
✓ Channel created: #prd-auth-secret
  Automatically assigned:
  - Team: engineering
  - Tools allowed: {engineering_allowed}
  - Cost center: engineering
  - LLM tier: b
  - Approval required: Yes (contains 'secret')
  - Audit logging: Verbose

  Confirm policies? [Y/n]
```

---

## 5. Provider Routing by Workflow

**Goal:** Smart cost optimization. Route by channel/keyword/priority.

```json
{
  "llm_routing": {
    "rules": [
      {
        "channel": "#prd-*",
        "priority": "high",
        "provider": "openrouter",
        "model": "claude-opus",
        "tier": "a",
        "fallback": "openrouter-qwen"
      },
      {
        "channel": "#research-*",
        "priority": "medium",
        "provider": "openrouter",
        "model": "qwen2:72b",
        "tier": "b",
        "fallback": "openrouter-deepseek"
      },
      {
        "channel": "#bk-*",
        "priority": "low",
        "provider": "openrouter",
        "model": "deepseek-67b",
        "tier": "c",
        "fallback": "local-ollama"
      },
      {
        "keyword": "urgent",
        "priority": "critical",
        "provider": "anthropic",
        "model": "claude-opus",
        "tier": "a"
      },
      {
        "keyword": "batch",
        "time_window": "02:00-06:00",
        "provider": "local",
        "model": "ollama-qwen",
        "tier": "free"
      },
      {
        "time": "22:00-08:00",
        "default_tier": "c",
        "reason": "off-peak budget mode"
      }
    ]
  }
}
```

**Examples:**

```
User in #prd-architecture:
@BotName: Design async connection pooling

→ Route: claude-opus (Tier A, $0.015/1K tokens)
Reason: High-priority engineering channel
```

```
User in #bk-categorization:
@BotName: Classify these 100 support tickets

→ Route: deepseek-67b (Tier C, $0.0003/1K tokens)
Reason: Bookkeeping channel, low priority
Cost estimate: ~$0.03 instead of $0.30 if using Opus
```

```
User in #prd-emergency:
@BotName urgent: Production database down, need immediate diagnosis

→ Route: claude-opus (Tier A, instant)
Reason: Keyword "urgent"
Bypass tier restrictions
```

---

## 6. Approval Workflows for High-Risk Actions

**Goal:** Prevent costly or dangerous operations without human review.

```json
{
  "approval_required": {
    "rules": [
      {
        "tool": "shell_exec",
        "with_keywords": ["rm", "sudo", "chmod", "kill"],
        "approvers": ["@security-team"],
        "timeout": 300
      },
      {
        "tool": "mcp_call",
        "to_system": ["external_api", "database_write"],
        "approvers": ["@devops-leads"],
        "timeout": 600
      },
      {
        "action": "cost_exceeds",
        "threshold": 50,
        "approvers": ["@finance-team"],
        "timeout": 3600
      },
      {
        "channel": "*-secret",
        "any_tool": true,
        "approvers": ["@security-team"],
        "timeout": 300
      }
    ]
  }
}
```

**Example Flow:**

```
User in #prd-database:
@BotName shell_exec: rm -rf /data/cache/*

❌ APPROVAL REQUIRED
Safe mode blocked destructive command: rm

Description: Remove all cache data
Requested by: @alice
Approvers needed: @security-team, @devops-leads
Approval timeout: 5 minutes

Approving this will:
- Delete /data/cache
- Cannot be undone
- See audit trail: https://...

[View Details] [Approve] [Deny]
```

If approved:
```
✅ Approved by @eve (security-team)
✅ Approved by @frank (devops-leads)

Executing: rm -rf /data/cache/*

Command output:
[operation results]

Audit entry created: ID-2026-0225-0447
```

---

## 7. Escalation Workflows

**Goal:** Uncertain decisions bubble up to humans.

```json
{
  "escalation": {
    "rules": [
      {
        "confidence_below": 0.70,
        "escalate_to": "@support-team",
        "message": "Low confidence response; please review"
      },
      {
        "cost_estimate_exceeds": 100,
        "escalate_to": "@finance-team",
        "message": "High-cost operation; needs approval"
      },
      {
        "error_rate_above": 0.10,
        "escalate_to": "@devops-team",
        "message": "Tool failure rate elevated; investigate"
      },
      {
        "channel": "#legal-*",
        "any_question": true,
        "escalate_to": "@legal-team",
        "message": "All legal channel requests auto-escalate"
      }
    ]
  }
}
```

**Example:**

```
User in #support:
@BotName: What's our HIPAA compliance status?

🤔 Low confidence response (58%)
Escalating to human...

Conversation forwarded to: @support-team
Estimated response: 15 mins

Customer message:
"Our specialist team is reviewing your question.
You'll get a detailed response shortly."
```

---

## 8. Audit & Logging Policies

**Goal:** Track all actions for security and cost compliance.

```json
{
  "audit": {
    "log_level": {
      "default": "info",
      "*-secret": "verbose",
      "mcp_call": "verbose",
      "shell_exec": "verbose",
      "github_*": "info"
    },
    "retention": {
      "default": 90,
      "*-secret": 365,
      "security_events": 730
    },
    "fields": [
      "timestamp",
      "user_id",
      "team",
      "channel",
      "tool_used",
      "cost_incurred",
      "tokens_consumed",
      "success_or_error",
      "approval_chain"
    ],
    "export": {
      "frequency": "daily",
      "format": "json",
      "destination": "s3://audit-logs/"
    }
  }
}
```

**Usage:**

```bash
# View audit logs for a user
nanobot audit --user alice --since "7 days ago"

# View by team
nanobot audit --team engineering --since "this month"

# View security events
nanobot audit --event-type shell_exec,mcp_call --since "30 days ago"

# Export for compliance
nanobot audit export --format csv --destination ./audit-report.csv
```

---

## 9. Cost Allocation & Chargeback

**Goal:** Multi-team deployments need to track who spent what.

```json
{
  "cost_allocation": {
    "teams": {
      "engineering": {
        "cost_center": "eng-ops",
        "monthly_budget": 150,
        "alert_at": 100,
        "override_contact": "alice@company.com"
      },
      "marketing": {
        "cost_center": "mkt-ops",
        "monthly_budget": 50,
        "alert_at": 35,
        "override_contact": "bob@company.com"
      },
      "research": {
        "cost_center": "r-and-d",
        "monthly_budget": 200,
        "alert_at": 150,
        "override_contact": "carol@company.com"
      }
    },
    "reporting": {
      "frequency": "weekly",
      "recipients": ["cfo@company.com", "ops-team@company.com"],
      "format": "html"
    }
  }
}
```

**Weekly Report Example:**

```
Cost Allocation Report - Week of Feb 24, 2026

Engineering:
  Budget: $150/month | Spent this week: $32.50 (22%)
  YTD: $130 / Projected total: $520

Marketing:
  Budget: $50/month | Spent this week: $8.20 (16%)
  YTD: $30 / Projected total: $120

Research:
  Budget: $200/month | Spent this week: $45.80 (23%)
  YTD: $190 / Projected total: $760 (ESTIMATE EXCEEDS)

⚠️ Research team on track to exceed budget
   Contact: @carol | Recommend: Increase budget or reduce scope
```

---

## 10. Sample Complete Config (All Planes)

**Control Plane (Access):**

```json
{
  "access_control": {
    "teams": {
      "engineering": {
        "channels": ["#dev", "#devops", "#prd-*"],
        "allowed_tools": ["web_search", "shell_exec", "github_*", "file_*", "mcp_call"],
        "excluded_tools": ["shell_exec:rm", "shell_exec:sudo"]
      }
    }
  }
}
```

**Policy Plane (Routing & Approval):**

```json
{
  "routing": {
    "default": "tier_b",
    "rules": [
      { "channel": "#prd-*", "tier": "a", "provider": "anthropic" },
      { "channel": "#bk-*", "tier": "c", "provider": "openrouter-deepseek" }
    ]
  },
  "approval": {
    "rules": [
      { "tool": "shell_exec", "with_keywords": ["rm"], "required": true }
    ]
  }
}
```

**Memory Plane (Scope & Retention):**

```json
{
  "memory": {
    "vault_policies": {
      "#dev": { "path": "/vault/dev", "retention": 90 },
      "#secret": { "path": "/vault/secret", "retention": 730, "encrypted": true }
    }
  }
}
```

---

## 11. Governance Deployment Checklist

Before going live:

**Access Control:**
- [ ] Define teams and their allowed tools
- [ ] Set per-user rate limits
- [ ] Set team budgets
- [ ] Publish channel naming convention

**Routing & Cost:**
- [ ] Define tier assignments (A/B/C) by channel
- [ ] Set provider fallback chain
- [ ] Test multi-provider failover
- [ ] Validate cost estimates

**Approval & Escalation:**
- [ ] List high-risk tools requiring approval
- [ ] Define escalation rules (confidence, cost)
- [ ] Train approvers on response time
- [ ] Test approval workflow end-to-end

**Audit & Compliance:**
- [ ] Set log retention policy
- [ ] Configure audit export
- [ ] Set up weekly cost reports
- [ ] Define compliance requirements

**Enforcement:**
- [ ] Test access denied scenario
- [ ] Test approval workflow
- [ ] Test escalation triggers
- [ ] Monitor first week closely

---

# Part 2: Security Validation & Auditing

## 12. Security Validation Runbook

**Purpose:** Recurring security validation steps for the Nanobot Agentic Operating System.

### Validation Frequency and Ownership

- **Frequency:** Monthly (minimum), and after major config changes
- **Owner:** Security/Platform Owner
- **Operator:** Assigned Operator
- **Reviewer:** Assigned Reviewer

### Preconditions

- Access to gateway host
- Access to channel/platform settings (Discord, Slack, Telegram, etc.)
- Access to Nanobot config and logs
- Access to Obsidian operational records

### How to Copy & Paste Commands in Linux

When you encounter bash commands in validation steps:

**Quick reference:**
- **Fastest:** Highlight → middle-click in terminal
- **Reliable:** Highlight → `Ctrl+Shift+C` → `Ctrl+Shift+V` to paste
- **Fallback:** Highlight → `Ctrl+C` → right-click Paste

Middle-click paste is native to Linux/Mac terminals and requires no additional steps.

---

## 13. Validation Sequence

Execute in this order to ensure consistent evidence collection.

### Step 1: Gateway Exposure Check
- Verify gateway bind is loopback-only. (Loopback = only accessible from this computer)
- Confirm no unintended public bind (**0.0.0.0**—which means "accessible to the whole internet") is active.
- Validate remote access path is SSH (Secure Shell—encrypted remote access) tunnel or Tailscale network-based.
- Validate Ollama on local Windows host is reachable from VPS (Virtual Private Server) only via trusted Tailscale path and is not publicly exposed.

**Pass Criteria**
- Gateway is loopback-bound and remote access is controlled.

**Fail Criteria**
- Any direct public exposure without approved exception.

### Step 2: DM Policy and Allowlist Check

**DM** = Direct Message (private messages sent to the bot, not in public channels)
- Verify DM policy is pairing mode by default.
- Validate unknown sender behavior (no command execution before approval).
- Verify allowlist entries are current and minimal. (Allowlist = list of approved people/channels)

**Pass Criteria**
- DM pairing flow works and allowlist scope is least-privilege.

### Step 3: Trust Boundary Check
- Confirm no mutually untrusted operator groups share one gateway trust boundary.
- Validate separation model (separate gateway/host/OS user) where required.

**Pass Criteria**
- Trust boundaries are documented and enforced.

### Step 4: Sandbox Policy Check
- Verify non-main/shared channels run under sandbox profile where required.
- Confirm high-risk tools are not broadly exposed in shared contexts.

**Pass Criteria**
- Sandbox rules match policy for shared/non-main sessions.

### Step 5: Security Audit Execution
- Run `nanobot security audit --deep` (automated security check for vulnerabilities)
- Verify deployed Nanobot version is v0.1.4 or later. (Newer versions have security fixes)
- Review latest Nanobot security advisories and confirm no unpatched High/Critical findings apply to deployed version.
- Capture findings and classify severity. (Severity = how serious the issue is)
- Confirm critical findings have immediate mitigation plan.

**Pass Criteria**
- No unresolved critical findings.
- Deployed version is at or above the current patched minimum for published advisories.

### Step 6: Secrets and Token Hygiene
- Confirm tokens/secrets are in **.env** (configuration file with secrets—NOT shared in code) or approved secret store only.
- Confirm no secrets (API keys, passwords) are present in notes, docs, or source-controlled config.
- Validate token rotation readiness and last rotation date. (Rotation = changing tokens regularly, like changing passwords)

**Pass Criteria**
- Secret handling policy is fully compliant.

### Step 7: Logging and Evidence Integrity
- Confirm required telemetry fields exist (**request_id**, **project_id**, **workflow_domain**, status, timestamps, model/cost fields where applicable).
- Confirm incident and change records are persisted to Obsidian.

**Pass Criteria**
- Security-relevant logs are complete and traceable end-to-end.

---

## 14. Evidence to Capture

- Command output snippets (including security audit)
- Nanobot version output (**nanobot --version**) and advisory review date
- Config screenshots or sanitized config excerpts
- Allowlist review record
- Sandbox policy verification record
- Remediation tickets with owners and due dates

---

## 15. Remediation SLA

- **Critical:** immediate containment + fix plan same day
- **High:** remediation within 7 days
- **Medium:** remediation within 30 days
- **Low:** backlog with documented rationale

---

## 16. Runbook Output Record Template

```markdown
# Security Validation Report - <YYYY-MM-DD>
- Run ID:
- Owner:
- Operator:
- Reviewer:
- Environment:

## Results by Step
1. Gateway Exposure Check: Pass/Fail
2. DM Policy and Allowlist Check: Pass/Fail
3. Trust Boundary Check: Pass/Fail
4. Sandbox Policy Check: Pass/Fail
5. Security Audit Execution: Pass/Fail
6. Secrets and Token Hygiene: Pass/Fail
7. Logging and Evidence Integrity: Pass/Fail

## Findings
- Critical:
- High:
- Medium:
- Low:

## Remediation Plan
- Item:
  - Owner:
  - Due Date:
  - Status:

## Final Decision
- Overall Status: Pass / Conditional Pass / Fail
- Next Review Date:
```

---

## 17. Go/No-Go Rule

- **Go:** all critical controls pass and no unresolved critical findings.
- **No-Go:** any unresolved critical finding or trust-boundary violation.

---

# Part 3: Common Mistakes & Solutions

## 18. Governance Common Mistakes

### ❌ Mistake 1: RBAC Config Too Restrictive, Users Blocked
**Problem:** Team members can't use necessary tools; constant "not allowed" errors  
**Why:** `allowed_tools` list incomplete or too conservative  
**Fix:**
1. Audit actual tool usage: `nanobot logs --filter="tool_denied" | head -20`
2. See what users are trying to do
3. Update allowed_tools:
   ```json
   "engineering": {
     "allowed_tools": [
       "web_search",
       "github_*",        // GitHub operations
       "shell_exec",      // Command line
       "file_read",       // Read files
       "file_write",      // Write files
       "mcp_call",        // MCP integrations
       "cron_schedule"    // Scheduled tasks
     ]
   }
   ```
4. Restart: `nanobot gateway`

### ❌ Mistake 2: Tool Whitelist Misspelled or Mismatched
**Problem:** User loses access to tool that was supposedly allowed  
**Why:** Tool name in config doesn't match actual tool name  
**Fix:**
1. Check exact tool names: `nanobot tools list`
2. Common mistakes:
   ```json
   ❌ "github"          # Wrong - it's github_search, github_pr, etc.
   ✅ "github_*"       # Correct - wildcard matches all github tools
   
   ❌ "Web_Search"     # Wrong - lowercase, no underscore
   ✅ "web_search"     # Correct
   
   ❌ "file_ops"       # Wrong - not a real tool
   ✅ "file_read" and "file_write" # Correct - separate tools
   ```
3. Validate config: `nanobot config validate`

### ❌ Mistake 3: Approval Chain Never Completes - Bottleneck
**Problem:** Users waiting hours for approvals; productivity blocked  
**Why:** Too many approvers, or approvers offline/not checking  
**Fix:**
```json
❌ Too strict:
{
  "requires_approval": true,
  "approval_group": "@devops-leads",  // Only 1 person!
  "timeout": 86400  // 24 hours to approve
}

✅ Better:
{
  "requires_approval": true,
  "approval_group": ["@devops-lead", "@devops-oncall", "@devops-backup"],
  "approval_mode": "any",  // ANY one can approve
  "timeout": 900  // 15 minutes to request, auto-escalate
}
```

### ❌ Mistake 4: Cost Budget Not Enforced - Bills Skyrocketing
**Problem:** Monthly bill 5x expected; no alerts or controls  
**Why:** Budget limits set but not actually enforced; no alerts configured  
**Fix:**
```json
❌ Budget defined but no enforcement:
{
  "budget": {
    "monthly": 1000,
    "team_allocation": { "eng": 600, "marketing": 400 }
  }
  // No alerts, no hard limit!
}

✅ With enforcement:
{
  "budget": {
    "monthly": 1000,
    "alerts": {
      "at_50_percent": true,
      "at_75_percent": true,
      "at_90_percent": true,
      "channel": "#finance"
    },
    "enforcement": {
      "hard_limit": true,  
      "when_exceeded": "disable_high_tier_models"
    }
  }
}
```
2. Check spend daily: `nanobot budget status`
3. Set up alerts: `nanobot budget alert --set 75% --channel #finance`

### ❌ Mistake 5: Audit Logs Deleted, Can't Prove Compliance
**Problem:** Regulator asks "Who deleted that file?" - No logs!  
**Why:** Log retention too short; logs not being written  
**Fix:**
```json
❌ Audit logs might get deleted:
{
  "audit": {
    "enabled": true,
    "retention_days": 7  // Too short!
  }
}

✅ Enterprise-grade retention:
{
  "audit": {
    "enabled": true,
    "level": "verbose",
    "retention_days": 365,  // Full year
    "export_format": "json",
    "backup_location": "s3://compliance-bucket/nanobot-logs/",
    "tools_logged": ["shell_exec", "file_write", "mcp_call", "github_*"]
  }
}
```
2. Verify logs exist: `ls -lh ~/.nanobot/logs/`
3. Export to archive: `nanobot audit export --format=compliance --days=365`

### ❌ Mistake 6: Escalation Rules Never Trigger
**Problem:** High-risk/high-cost actions run without waiting for approval  
**Why:** Escalation threshold set too high, or action doesn't match rule  
**Fix:**
```json
❌ Escalation never triggers:
{
  "escalation": {
    "high_risk_threshold": 50000,  // Only escalates if >50k tokens
    "tools": ["shell_exec"]        // Only shell_exec
  }
  // User does web search (5k tokens) - not escalated
  // User runs github action (3k tokens) - not escalated
}

✅ Proper escalation:
{
  "escalation": {
    "high_risk_tools": ["shell_exec", "mcp_call", "file_write"],
    "high_cost_threshold": 5000,   // Escalate high-cost actions
    "approval_required_for": [
      "shell_exec:*",         // ALL shell commands need approval
      "mcp_call:database",    // Database MCP calls
      "file_write:config"     // Config file writes
    ]
  }
}
```
2. Test escalation manually: `/tool shell_exec test`
3. Check approval queue: `nanobot approvals list`

---

## 19. Security Common Mistakes

### ❌ Mistake 7: Security Audit Shows Findings But No Remediation Plan
**Problem:** Audit fails with "3 High severity findings"; validation stops  
**Why:** Findings documented, but no owner/timeline assigned  
**Fix:**
1. For each High/Critical finding, create remediation ticket immediately:
   ```markdown
   # Remediation: [Issue name]
   - Owner: @specific_person (not "team")
   - Due Date: [7 days from today for High]
   - Priority: High
   - Ticket: <link>
   ```
2. Don't mark validation as "Conditional Pass" without assigned owner + date
3. Follow up: Check status 3 days before due date

### ❌ Mistake 8: Token Rotation Never Happens - Old Tokens Still Active
**Problem:** Last rotation was 6 months ago; old Discord token still working  
**Why:** No automated reminder; no rotation policy enforced  
**Fix:**
1. **Set up rotation auto-reminders:**
   ```bash
   nanobot schedule "Rotate Discord token" cron "0 9 1 * *"  # 1st of month at 9am
   nanobot schedule "Rotate API keys" cron "0 9 15 * *"     # 15th of month
   ```
2. **Document last rotation date:**
   ```markdown
   - Discord token: Last rotated [DATE] - Due [DATE+90days]
   - OpenRouter API: Last rotated [DATE] - Due [DATE+90days]
   - GitHub token: Last rotated [DATE] - Due [DATE+90days]
   ```
3. **Enforce in config:**
   ```json
   {
     "secrets": {
       "rotation_policy": "every_90_days",
       "expiry_alert_days": 14
     }
   }
   ```

### ❌ Mistake 9: Sandbox Rules Documented But Not Enforced
**Problem:** Security says "shared channels run in sandbox"; in reality, they don't  
**Why:** Sandbox policy written but config doesn't enforce it  
**Fix:**
1. **Check actual config enforces sandbox:**
   ```json
   {
     "channels": {
       "#general": {
         "sandbox": true,
         "allowed_tools": ["web_search", "message_send"],
         "disallowed_tools": ["shell_exec", "file_write"]
       },
       "#devops": {
         "sandbox": false,
         "allowed_tools": "all"
       }
     }
   }
   ```
2. **Verify it's actually enforced:**
   ```bash
   nanobot config --validate
   # If validation passes, config is syntactically correct
   # But config enforcement = separate system check
   ```
3. **Test sandbox actually blocks:**
   ```
   /in #general shell_exec ls
   # Should get: "Blocked: shell_exec not allowed in #general (sandbox)"
   ```

### ❌ Mistake 10: Secrets in Git History - Can't Be Removed
**Problem:** Someone committed API key to GitHub 3 months ago; it's still in history  
**Why:** Used plain `git rm` instead of removing from history  
**Fix:**
1. **Remove the file from git history** using `git filter-repo`:
   ```bash
   # Install if needed:
   pip install git-filter-repo
   git filter-repo --path config.json --invert-paths --force
   ```
2. **Force push to remote:**
   ```bash
   git push origin --force --all
   ```
3. **Immediately revoke the compromised token in the service (GitHub, Discord, etc.)**
4. **Add to .gitignore to prevent future commits:**
   ```bash
   echo "config.json" >> .gitignore
   echo ".env" >> .gitignore
   echo "secrets/*" >> .gitignore
   git add .gitignore && git commit -m "Add secrets to gitignore"
   ```

### ❌ Mistake 11: Trust Boundary Violation - Multiple Untrusted Groups Share Gateway
**Problem:** Engineering team and Marketing team both use same gateway; engineer accesses marketing's Slack token  
**Why:** Didn't enforce separation; all users on same process  
**Fix:**
```yaml
❌ No trust boundary enforcement:
gateway:
  users: ["eng-team", "marketing-team"]  # Same process!

✅ Separate trust boundaries:
gateway_engineering:
  users: ["eng-team"]
  bind: "127.0.0.1:8000"

gateway_marketing:
  users: ["marketing-team"]
  bind: "127.0.0.1:8001"

# Even better: Separate hosts/VMs
```
2. **Implement:** Create separate gateway instances for each trust group
3. **Verify separation:**
   ```bash
   ps aux | grep nanobot
   # Should see multiple nanobot processes with different ports
   ```

### ❌ Mistake 12: Allowlist Has Old Users Who Left Company
**Problem:** Employee left 6 months ago; bot still accepts DMs from their old Discord account  
**Why:** Never removed from allowlist config  
**Fix:**
1. **Quarterly audit:**
   ```json
   {
     "dms": {
       "allowlist": [
         "alice (joined 2026-01-15)",
         "bob (joined 2025-09-01)",
         "dave (REVOKED 2026-02-01 - left company)"  // Mark as revoked
       ]
     }
   }
   ```
2. **Enforce allowlist strictly:**
   ```bash
   nanobot config dms.allowlist --validate
   # Check for stale/revoked entries
   ```
3. **When employee leaves:**
   - Remove from allowlist immediately
   - Revoke any API tokens they created
   - Check `~/.nanobot/logs/` for any emergency access patterns

---

## 20. Troubleshooting Governance Issues

| Problem | Symptom | Solution |
|---|---|---|
| User complains "tool not allowed" | Tool blocked for user | Check RBAC config; user in right team? Check channel policy |
| Cost running over budget | Monthly spend exceeds threshold | Review routing rules; are low-priority tasks using high tiers? |
| Approvals taking too long | Users waiting for action | Check approval timeout; add more approvers; use instant approval for low-risk |
| Audit logs missing | Cannot find historical actions | Check log_level and retention; increase verbosity for critical tools |
| Escalations not triggering | Cases not bubbling to humans | Verify escalation rules match actual thresholds; test with manual override |

---

## 21. Common Patterns by Organization Type

### Startup (Lean, Speed-Focused)

```json
{
  "teams": ["engineering"],
  "policies": "minimal",
  "approval": "self-approve",
  "audit": "basic",
  "budget": "flexible"
}
```

**Philosophy:** Move fast, minimal overhead.

### Scale-Up (Multiple Teams)

```json
{
  "teams": ["engineering", "marketing", "operations"],
  "policies": "channel-based",
  "approval": "for high-risk + high-cost",
  "audit": "weekly reports",
  "budget": "per-team allocation"
}
```

**Philosophy:** Balance autonomy + accountability.

### Enterprise (Compliance-Heavy)

```json
{
  "teams": ["engineering", "marketing", "operations", "legal", "finance"],
  "policies": "strict RBAC",
  "approval": "required for all risky actions",
  "audit": "verbose + compliance export",
  "budget": "strict cost centers + chargeback"
}
```

**Philosophy:** Governance by default; audit everything.

---

## 22. Revision History

| Date | Version | Change |
|---|---|---|
| 2026-02-26 | 2.1.0 | Fixed #bk-* routing description (backlog → bookkeeping); generalized Discord-specific precondition; replaced deprecated git filter-branch with git filter-repo in Mistake 10 |
| 2026-02-26 | 2.0.0 | Consolidated Security Validation Runbook and Governance Policies into single comprehensive guide |
| 2026-02-25 | 1.0.0 (Governance) | Initial governance policies and config examples |
| 2026-02-23 | 1.0.0 (Security) | Initial monthly security validation runbook |

---

## Related Documents
- [Master Index](Master-Index.md)
- [Skills & Tools Complete Guide](Skills-and-Tools-Complete-Guide.md)
- [Cost Calculator & Optimization](Cost-Calculator-and-Optimization.md)
