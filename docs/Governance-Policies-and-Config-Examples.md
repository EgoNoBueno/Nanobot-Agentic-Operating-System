# Governance Policies & Config Examples

Enforce organizational guardrails, access control, and cost accountability. Templates and patterns for production deployments.

## Document Control
- **Owner:**
- **Version:** 1.0.0
- **Last Updated:** 2026-02-25
- **Status:** Active

---

## 1. Overview: The 3-Plane Governance Model

**Plane 1: Control** — Who can do what? (access control, tool allowlisting)  
**Plane 2: Policy** — How are decisions made? (routing rules, escalation, approval flows)  
**Plane 3: Memory** — What does the system know? (vault scope, retention policies)

All three planes are **channel-aware** (work in Discord, Slack, Telegram, etc.).

---

## 2. Access Control Patterns

### Pattern A: Role-Based Tool Allowlisting (RBAC)

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

**Goal:** Classify actions by reversibility. Actions with high impact require human approval.

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
Reason: Backlog/batch, low priority
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

## 7. Audit & Logging Policies

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

## 8. Cost Allocation & Chargeback

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

## 9. Sample Complete Config (All Planes)

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

## 10. Governance Checklist (Deployment)

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

## 11. Troubleshooting Governance Issues

| Problem | Symptom | Solution |
|---|---|---|
| User complains "tool not allowed" | Tool blocked for user | Check RBAC config; user in right team? Check channel policy |
| Cost running over budget | Monthly spend exceeds threshold | Review routing rules; are low-priority tasks using high tiers? |
| Approvals taking too long | Users waiting for action | Check approval timeout; add more approvers; use instant approval for low-risk |
| Audit logs missing | Cannot find historical actions | Check log_level and retention; increase verbosity for critical tools |
| Escalations not triggering | Cases not bubbling to humans | Verify escalation rules match actual thresholds; test with manual override |

---

## 12. Common Patterns by Organization Type

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

## Revision History

| Date | Version | Change |
|---|---|---|
| 2026-02-25 | 1.0.0 | Initial governance policies and config examples |

