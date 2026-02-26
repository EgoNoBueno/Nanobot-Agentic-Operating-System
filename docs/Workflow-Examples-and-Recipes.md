# Workflow Examples & Recipes

Real-world nanobot workflows. Copy, customize, and deploy.

## Document Control
- **Owner:**
- **Version:** 1.0.0
- **Last Updated:** 2026-02-25
- **Status:** Active

---

## 1. Daily Knowledge Consolidation (Obsidian + Cron + Summarize)

**Goal:** Every night at 2am, summarize yesterday's Discord #prd channel into Obsidian vault.

**Tools used:** cron_schedule, file_read, file_write, shell_exec (python + summarize skill)

**Why useful:** Engineering teams generate insights constantly; capturing them prevents loss.

### Setup

**1.1 Config (in ~/.nanobot/config.json):**

```json
{
  "channels": {
    "discord": {
      "enabled": true,
      "token": "your-discord-token",
      "listen_channels": ["prd"]
    }
  },
  "skills": {
    "obsidian": {
      "enabled": true,
      "vault_path": "/path/to/obsidian/vault"
    },
    "summarize": {
      "enabled": true,
      "model": "qwen2:latest"
    },
    "cron": {
      "enabled": true
    }
  }
}
```

**1.2 Create Cron Job (in Discord):**

Send this message to trigger scheduled job creation:

```
@BotName cron create: nightly_prd_summary
  schedule: "0 2 * * *"
  action: "Summarize Discord #prd from yesterday and write to Obsidian daily-standup/{date}.md"
  provider: qwen2:latest
  output_format: markdown
```

**1.3 Check Job Created:**

```
@BotName cron list
```

Output:
```
✓ nightly_prd_summary (0 2 * * *) - Last run: 2026-02-25 02:01
```

### Execution (Automatic Every Night)

**What happens at 2am:**

1. Nanobot reads last 24h of #prd messages
2. Runs summarize skill: "Extract key decisions, blockers, and discussions"
3. Formats as Markdown with timestamps and attributions
4. Writes to: `Obsidian/01-Daily/2026-02-25.md`
5. Posts confirmation to #prd: "Summary written ✓"

### Sample Output

**File: `/Obsidian/01-Daily/2026-02-25.md`**

```markdown
# Daily Standup - Feb 25, 2026

## Key Decisions
- **Design System:** Approved new component library (Alex, 2:15pm)
- **Timeline:** Sprint ends Friday with demo Friday 4pm (Sara, 3:22pm)

## Blockers
- S3 integration failing with session token expiration (James, 1:45pm)
  - Action: James to investigate IAM policy with DevOps team
  
## Technical Discussions
- Python async patterns for high-concurrency services (3 messages)
- Caching strategy for API responses (2 threads)

## Attendees
- @alex, @sara, @james, @yuki

## Full Transcript
[link to Discord thread]
```

### Customization Ideas

**Variation 1: Weekly Executive Summary**

```
schedule: "0 8 * * 1"  // Monday 8am
format: executive_brief  // Shorter, business-focused
channels: ["prd", "exec", "strategy"]  // Multiple channels
```

**Variation 2: Per-Team Summaries**

```json
{
  "cron_jobs": [
    { "channel": "prd", "team": "engineering", "time": "8am" },
    { "channel": "marketing", "team": "marketing", "time": "9am" },
    { "channel": "sales", "team": "sales", "time": "10am" }
  ]
}
```

**Variation 3: Slack Instead of Obsidian**

```
action: "Summarize and post to Slack #daily-standup"
```

---

## 2. Multi-Channel Content Distribution

**Goal:** User writes article in Discord. Automatically posts to Discord, Slack, Telegram with platform-specific formatting.

**Tools used:** message_send (with channel routing), file_read, shell_exec (for formatting)

**Why useful:** Marketing teams write once, distribute everywhere. No copy-pasting errors.

### Setup

**2.1 Config:**

```json
{
  "channels": {
    "discord": { "enabled": true },
    "slack": { "enabled": true },
    "telegram": { "enabled": true }
  },
  "tools": {
    "message_send": {
      "enabled": true,
      "default_channel": "discord",
      "routing": {
        "twitter": "telegram",
        "blog": ["discord", "slack"],
        "announcement": ["discord", "slack", "telegram"]
      }
    }
  }
}
```

**2.2 Trigger in Discord:**

User post in #content-hub:

```
@BotName post:
Title: "5 Nanobot Best Practices"
Body: > Best practices for deploying nanobot at scale...
[full article text]

Distribute to: announcement
```

### Execution

**What Bot Does:**

1. **Format for Discord:**
   - Embed with title + body + author
   - Link to full document
   - Reactions for feedback

2. **Format for Slack:**
   - Slack blocks layout
   - Threading support
   - Mention relevant team members

3. **Format for Telegram:**
   - Plain text (Telegram markdown)
   - Link in thread

### Sample Output

**Discord:**
```
📰 5 Nanobot Best Practices
by @alice | #content-hub

Best practices for deploying nanobot at scale...
[Read Full Article](link)

👍 ❤️ 🔗
```

**Slack:**
```
5 Nanobot Best Practices
Posted by alice in #content-hub

Best practices for deploying nanobot at scale...
Read Full Article: [link]


Thread: 0 replies | 2 emoji reactions
```

**Telegram:**
```
📰 5 Nanobot Best Practices
@alice

Best practices for deploying nanobot...

Read: [link]
```

### Cost: ~$0.005 per distribution (Tier B)

---

## 3. Customer Support Bot (Multi-Channel)

**Goal:** Answer customer questions in Discord, Slack, Telegram. Route to human if confidence <70%.

**Tools used:** web_search, file_read (knowledge base), message_send, mcp_call (to ticket system)

**Why useful:** 24/7 support, reduce ticket volume by 50-60%, instant answers.

### Setup

**3.1 Config:**

```json
{
  "channels": {
    "discord": { "enabled": true, "channels": ["#support"] },
    "slack": { "enabled": true, "channels": ["#help"] },
    "telegram": { "enabled": true }
  },
  "tools": {
    "web_search": { "enabled": true, "api_key": "brave_key" },
    "file_read": { "enabled": true }
  },
  "skills": {
    "memory": {
      "enabled": true,
      "knowledge_base": "/path/to/support-docs"
    }
  },
  "routing": {
    "confidence_threshold": 0.70,
    "low_confidence_action": "escalate_to_human"
  }
}
```

**3.2 Knowledge Base File Structure:**

```
/knowledge-base/
├── faq.md (Common questions & answers)
├── troubleshooting.md (Error codes + fixes)
├── api-docs.md (API reference)
└── pricing.md (Pricing & billing)
```

### Execution

**User in Discord #support:**

```
@BotName: How do I enable multi-channel support?
```

**Bot Process:**

1. Search knowledge base for "multi-channel"
2. Search web if not found locally
3. Generate answer with confidence score
4. If confidence >70%: Post answer
5. If confidence <70%: Tag @support-team with context

**Sample Output:**

```
✓ How do I enable multi-channel support?

To enable multi-channel support, edit ~/.nanobot/config.json:

{
  "channels": {
    "discord": { "enabled": true },
    "slack": { "enabled": true }
  }
}

Then restart: nanobot gateway

📚 See: [Full Multi-Channel Guide](link)
👥 Still need help? Thread @alice, @bob, or @support-team
```

### Advanced: Escalation Logic

If bot unsure, creates MCP ticket:

```
🤔 I'm not confident in my answer (62% confidence).
Escalating to human team...

📋 Internal Ticket ID: SUP-2026-0225-001
👥 Assigned to: next available
⏱️ Est. response: 15 mins

Customer sees: "A human team member will respond shortly"
```

### Handle Volume

For 100 support questions/day:
- Auto-answer: 60-70 questions (~$2.25/month Tier C)
- Escalate: 30-40 questions (~30 mins human review)
- **ROI:** ~2 person-hours saved, net positive

---

## 4. Research Agent (Web Search + Synthesis)

**Goal:** User asks research question. Bot searches web, reads articles, synthesizes findings into report.

**Tools used:** web_search, file_read, file_write, shell_exec (for web scraping)

**Why useful:** Replace 2-3 hour research task with 2-minute bot task.

### Setup

**4.1 Config:**

```json
{
  "tools": {
    "web_search": {
      "enabled": true,
      "api_key": "brave_api_key",
      "max_results": 10
    },
    "file_read": { "enabled": true },
    "file_write": { "enabled": true }
  },
  "llm": {
    "model": "claude-opus",
    "tier": "a"
  }
}
```

### Trigger (in Discord #research)

```
@BotName research:
Question: "What are latest trends in AI agent frameworks (Feb 2026)?"
Format: report
Output: obsidian_file
Vault path: /obsidian/01-Research/{date}-{topic}.md
```

### Execution

**Bot Process (takes 2-3 min):**

1. Web search: "AI agent frameworks 2026 trends"
2. Read top 10 articles
3. Extract key points: frameworks, capabilities, costs, adoption
4. Synthesize into structured report
5. Write to Obsidian with citations

**Sample Output:**

**File: `/Obsidian/01-Research/2026-02-25-AI-Agent-Frameworks.md`**

```markdown
# AI Agent Frameworks - Trends Feb 2026

## Executive Summary
3 major frameworks dominating: Nanobot, AutoGPT-7, and Cursor-AI v2.
Common pattern: LLM routing + tool composition + memory consolidation.

## Framework Comparison

| Framework | LLM Support | Tools | Channels | Cost/mo |
|---|---|---|---|---|
| **Nanobot** | 100+ | 14 | 12+ | $0-200 |
| **AutoGPT-7** | OpenAI only | 30 | 5 | $50-500 |
| **Cursor-AI** | varies | 50+ | 3 | $20-300 |

## Key Trends

1. **Multi-provider routing** - Cost optimization (Trend strength: ++++)
2. **Memory consolidation** - Reduce context window costs (++++)
3. **Self-hosted inference** - Privacy + cost control (+++)
4. **Tool composition** - Complex workflows from simple primitives (++++)

## Detailed Analysis
[30-50 lines of synthesis]

## Sources
- [Article 1](url) - "2026 State of AI Agents"
- [Article 2](url) - "Framework Benchmark Report"
- [Article 3](url) - "Enterprise AI Agent Adoption"

Generated 2026-02-25 by Nanobot Research Agent
```

### Cost: ~$0.02 per research task (Tier A - Claude Opus)

**ROI:** Replaces 1-2 hours manual research at cost of $0.02 🎯

---

## 5. GitHub Automation & Daily Standup

**Goal:** Every morning, summarize overnight PRs/issues, post to Discord #engineering.

**Tools used:** github_search, github_pr_create, github_action_trigger, summarize skill, message_send, cron_schedule

**Why useful:** Engineering team aware of PRs needing review without checking GitHub.

### Setup

**5.1 Config:**

```json
{
  "tools": {
    "github": {
      "enabled": true,
      "token": "github-personal-access-token",
      "org": "your-org",
      "repos": ["nanobot", "cli-tools", "integrations"]
    }
  },
  "skills": {
    "cron": { "enabled": true },
    "summarize": { "enabled": true }
  },
  "channels": {
    "discord": { "enabled": true }
  }
}
```

**5.2 Create Cron Job:**

```
@BotName cron create: morning_github_standup
  schedule: "0 8 * * 1-5"  // Mon-Fri 8am
  action: "
    Get all open PRs and issues from last 24h across repos
    Summarize: PRs awaiting review, merged PRs, new critical issues
    Post to Discord #engineering with action items
  "
  provider: openrouter/qwen2:latest
```

### Execution

**Every Weekday 8am:**

Bot fetches from GitHub:
- 3 PRs opened overnight (with file diffs)
- 8 PRs merged yesterday
- 2 new critical issues
- 15 comments on open PRs

Generates summary:

**Discord #engineering:**

```
📊 GitHub Standup - Thursday Feb 25

## PRs Awaiting Review (3)
🔴 #456: Refactor auth middleware (alice) - 8 files, needs review
🟡 #457: Add caching layer (bob) - 3 files, 1 approval needed
🟡 #458: Update dependencies (dependabot) - automated

## Merged Yesterday (8)
✅ #445: Fix S3 upload bug
✅ #446: Add telemetry logging
✅ #447-452: Dependency updates

## Critical Issues (2)
🔴 #2034: Memory leak in connection pool (opened 3h ago)
🔴 #2035: API rate limiting broken

## Action Items
- @alice: Review #457 (3 mins)
- @james: Investigate #2034 (estimate 1h)
- @sara: Prioritize #2035 after standup

## Stats
- 8 PRs merged | 3 awaiting review | 14 commits
- Test coverage: 87% (↑ 1%)

[Full report](link to GitHub)
```

### Cost: ~$0.003 per run (Tier C - daily) = **$0.09/month**

---

## 6. Obsidian Sync + Weekly Review

**Goal:** Friday 5pm, auto-generate weekly review from Obsidian vault + Discord logs.

**Tools used:** file_read (Obsidian), shell_exec (Obsidian API), summarize skill, file_write

**Why useful:** Weekly reflection on team progress, decisions, and blockers.

### Setup

**6.1 Obsidian Vault Structure:**

```
/Vault/
├── 00-System/
│   └── weekly-review-template.md
├── 01-Daily/
│   ├── 2026-02-21.md
│   ├── 2026-02-22.md
│   └── ...
├── 02-Project/
│   ├── project-a.md
│   ├── project-b.md
│   └── ...
└── 03-Archive/
    └── weekly-2026-w08.md
```

**6.2 Cron Job:**

```
@BotName cron create: weekly_review
  schedule: "0 17 * * 5"  // Friday 5pm
  action: "
    1. Read daily notes from last 7 days
    2. Extract decisions, blockers, wins
    3. Summarize project updates
    4. Generate weekly review
    5. Save to 03-Archive/weekly-{date}.md
    6. Post summary to Discord #weekly-review
  "
```

### Sample Output

**Discord #weekly-review:**

```
📋 Weekly Review - Week of Feb 24

**Wins This Week**
✅ Launched multi-channel support (Major!)
✅ Cut API latency by 40% with caching
✅ Onboarded 3 new team members

**Blockers**
🔴 S3 integration still failing (blocker for 2 PRs)
🟡 API rate limits causing test flakiness

**Key Decisions**
- ✓ Approved new design system (for Q2 rollout)
- ✓ Migrated to OpenRouter for cost control (saving 70%)
- ✓ Delayed mobile app launch to May (more runway needed)

**Team Metrics**
- 23 PRs merged | 3 critical bugs fixed
- Code coverage: 87% (↑ 1%)
- Release readiness: 94%

**Next Week Priorities**
1. Fix S3 blocker (alice + james)
2. Complete onboarding (sara)
3. Q2 planning kickoff (friday afternoon)

[Full weekly review](link)
```

---

## Common Mistakes & Gotchas

### ❌ Mistake 1: Cron Schedule Syntax Wrong
**Problem:** Scheduled workflow never runs  
**Why:** Cron expression uses wrong format (off-by-one in day, wrong timezone)  
**Fix:**
- Correct format: `"0 2 * * *"` (minute=0, hour=2, day=any, month=any, day-of-week=any)
- Test BEFORE deploying: `nanobot cron validate "0 2 * * *"`
- Check timezone in config matches your office: `"timezone": "America/New_York"` (not UTC)
- Common mistake: `"2 0 * * *"` runs at 12am not 2am (hour comes AFTER minute)

### ❌ Mistake 2: API Rate Limits Hit, Workflow Stops Silently
**Problem:** Workflow runs once, then stops. No error messages.  
**Why:** Discord/Slack/web search API limits exceeded; nanobot backs off  
**Fix:**
1. Check logs: `nanobot logs --filter="rate_limit"`
2. Add backoff to config:
   ```json
   {
     "retry": {
       "maxAttempts": 3,
       "backoffMultiplier": 2.0,
       "initialDelaySeconds": 5
     }
   }
   ```
3. Stagger workflows: Don't run 5 at 2am (spread to 2am, 2:10am, 2:20am)
4. Upgrade API tier if hitting hard limits (Discord: Level 2+; OpenRouter: Tier B+)

### ❌ Mistake 3: Config Typo Silently Disables Entire Workflow
**Problem:** Feature doesn't work, but no error message  
**Why:** JSON syntax error or misspelled key name  
**Fix:**
1. **Validate JSON first:**
   ```bash
   python -m json.tool ~/.nanobot/config.json
   ```
   (If this fails, JSON is broken)
2. **Check exact key names:**
   - ❌ `"obsidian_path"` (underscore wrong)
   - ✅ `"vault_path"` (correct)
3. Always restart after config changes: `nanobot gateway` (stop and restart)
4. Check logs: `nanobot logs | tail -20`

### ❌ Mistake 4: Output File Path Doesn't Exist
**Problem:** Workflow runs but output file not written  
**Why:** Directory `/path/to/obsidian/vault/01-Daily/` doesn't exist  
**Fix:**
```bash
# Create directory structure first
mkdir -p /path/to/obsidian/vault/01-Daily/
mkdir -p /path/to/obsidian/vault/02-Projects/
mkdir -p /path/to/obsidian/vault/03-Archive/

# Then grant nanobot write permission
chmod 755 /path/to/obsidian/vault

# Test write
nanobot write test "/path/to/obsidian/vault/test.md"
```

### ❌ Mistake 5: Discord/Slack Bot Missing Permissions
**Problem:** Workflow tries to send message, fails silently or "Forbidden"  
**Why:** Bot doesn't have Send Messages / Read History permissions on channel  
**Fix:**
1. In Discord/Slack admin panel, grant bot these permissions:
   - ✅ Send Messages / Post Messages
   - ✅ Read Message History
   - ✅ Manage Messages (if deleting old summaries)
   - ✅ Embed Links (for formatted output)
2. Test bot can send: `@BotName test` in target channel
3. If "Missing Access" error: Bot likely removed from channel
   - Reinvite: `@BotName invite #channel-name`

### ❌ Mistake 6: Tokens/Secrets Hardcoded in Config
**Problem:** Config file with API keys committed to GitHub (security breach!)  
**Why:** Used hardcoded token instead of environment variable  
**Fix:**
1. **Immediately:** Revoke all tokens in API provider dashboard
2. **Remove from history:**
   ```bash
   git filter-branch --force --index-filter 'git rm --cached -r -f ~/.nanobot/config.json' -- --all
   ```
3. **Use environment variables instead:**
   ```json
   {
     "discord": {
       "token": "${DISCORD_TOKEN}"
     }
   }
   ```
4. **Set in `.env` file (added to `.gitignore`):**
   ```
   DISCORD_TOKEN=your_actual_token_here
   ```

---

## Mixing & Matching

All these recipes share common patterns. Combine them:

- **Daily Standup + Multi-Channel Distribution:** Write once, post everywhere
- **Research Agent + Obsidian:** Auto-capture findings
- **GitHub Standup + Cron:** Every morning, team has context
- **Customer Support + Memory:** Knowledge base grows with every ticket

**Cost for running all 6 workflows continuously:**
- ~$50-100/month combined (using Tier B+C routing)
- **Replaces:** ~10-15 person-hours/week work
- **ROI:** 20-30x annual payback

---

## Customization Template

Use this for building your own workflow:

```
Name: [workflow name]
Goal: [what problem does this solve]
Tools: [list tools used]

Setup:
1. [Config changes]
2. [File structures needed]
3. [Initial trigger/setup]

Execution:
- [Step 1]
- [Step 2]
- [Step 3]

Sample Output:
[Real example of what user sees]

Cost:
[Est. cost/month]

Variations:
- [Variation 1]
- [Variation 2]
```

---

## Revision History

| Date | Version | Change |
|---|---|---|
| 2026-02-25 | 1.0.0 | Initial workflow examples and recipes |

