# Nanobot Skills & Tools Complete Guide

**Complete, progressive reference** for all 14 built-in tools, 9 pre-built skills, essential 5-skill quickstart, and custom skill development.

## Document Control
- **Owner:**
- **Version:** 2.0.0
- **Last Updated:** 2026-02-26
- **Status:** Active
- **Scope:** Tools, pre-built skills, essential skills, custom development, troubleshooting

---

## Quick Navigation

**New to nanobot?** Start with [Essential 5-Skill Quickstart](#part-2-essential-5-skill-quickstart) → then explore [Pre-Built Skills](#part-1b-the-9-pre-built-skills)

**Building custom skills?** Go to [Custom Skill Development](#part-4-custom-skill-development)

**Troubleshooting?** Jump to [Troubleshooting & Common Mistakes](#part-5-troubleshooting-common-mistakes)

---

## Part 1: Tools & Built-In Skills Overview

### What Are Tools vs. Skills?

**Tools** = Single executable actions (web search, write file, run command, etc.)  
**Skills** = Pre-built, opinionated bundles of tools configured for specific workflows (obsidian integration, memory management, cron scheduling, etc.)

### Tool Matrix (All 14 Built-In Tools)

| Tool | Purpose | Risk | Cost | Notes |
|---|---|---|---|---|
| **web_search** | Real-time web search (Brave API) | Low | ~$0.01 | Requires Brave API key |
| **web_fetch** | Retrieve & parse full web pages | Low | Free | Rate-limited |
| **file_read** | Read files (workspace-scoped) | Low | Free | Sandbox-enforced |
| **file_write** | Create/overwrite files | Medium | Free | Audit-logged |
| **file_edit** | Edit specific lines in files | Medium | Free | Line-based; audit-logged |
| **file_list** | List directory contents | Low | Free | Workspace-visible only |
| **shell_exec** | Execute shell commands | High | Free | Operator-restricted; logged |
| **message_send** | Send alerts/notifications | Low | Free | Cross-channel possible |
| **mcp_call** | Call MCP server tools | High | Varies | Custom auth supported |
| **cron_schedule** | Schedule recurring tasks | Medium | Free | Cron syntax |
| **subagent_spawn** | Launch parallel agents | High | Varies | Own context/memory |
| **github_search** | Search GitHub repos | Low | Free | Public access |
| **github_pr_create** | Create pull requests | High | Free | Requires token |
| **github_action_trigger** | Trigger GitHub workflows | High | Free | Requires permissions |

---

## Part 1A: Tool Details & Configuration

### web_search
**Purpose:** Real-time search via Brave Search API  
**Required API Key:** Yes (Brave)  
**Cost:** ~$0.01-0.05 per search  
**Config:**
```json
{
  "braveApiKey": "YOUR_BRAVE_KEY"
}
```
**Usage:** `nanobot: "Find 3 articles about AI safety from this week"`

### web_fetch
**Purpose:** Download and parse web pages full-text  
**Cost:** None  
**Usage:** `nanobot: "Summarize this page: https://example.com/article"`

### file_read / file_write / file_edit / file_list
**Purpose:** Access workspace files  
**Workspace Scope:** All paths relative to `~/.nanobot/workspace/`  
**Security:** Cannot escape workspace root  
**Usage Examples:**
```
"Read the project README"
"Create a deployment checklist in deploy-checklist.md"
"Update line 10 in config.json to set debug=true"
"List all Python files in src/"
```

### shell_exec
**Purpose:** Execute OS commands  
**Risk Tier:** High  
**Config:**
```json
{
  "tools": {
    "shell": {
      "enabled": true,
      "allowFrom": ["USER_ID_1", "USER_ID_2"],
      "timeout": 30,
      "blocklist": ["rm -rf", "sudo dd"]
    }
  }
}
```

### message_send
**Purpose:** Send cross-channel alerts, escalations, notifications  
**Usage Examples:**
```
"Alert team in #incidents that model latency > 5s"
"Send daily summary to #ops-digest"
"Notify owner in #alerts about high spend"
```

### mcp_call
**Purpose:** Call externally hosted MCP (Model Context Protocol) tools  
**Examples of MCP Tools:**
- Filesystem operations on remote systems
- Database queries
- API calls to internal services
- Custom business logic tools
**Config:**
```json
{
  "mcpServers": {
    "mydb": {
      "command": "python",
      "args": ["/path/to/db_mcp_server.py"],
      "customHeaders": {
        "Authorization": "Bearer YOUR_TOKEN"
      }
    }
  }
}
```

### cron_schedule
**Purpose:** Schedule tasks to run on recurring schedule  
**Syntax:** Standard cron (minute hour day month weekday)  
**Examples:**
```
"0 9 * * 1-5" → Every weekday at 9 AM
"0 * * * *" → Every hour
"*/15 * * * *" → Every 15 minutes
"0 23 * * *" → Daily at 11 PM
```

### subagent_spawn
**Purpose:** Launch independent agents for parallel work  
**Use Cases:**
- Parallel research across multiple sources
- Delegated tasks (research, fix, monitor simultaneously)
- Complex multi-step workflows
- Fact-checking and verification
**Config:**
```json
{
  "orchestration": {
    "maxSpawnDepth": 2,
    "maxConcurrentAgents": 5,
    "costCeiling": 5.00,
    "subagentDefaults": {
      "model_fast": "qwen-2-72b",
      "model_smart": "claude-opus",
      "timeout_seconds": 300
    }
  }
}
```

### github_search, github_pr_create, github_action_trigger
**Purpose:** Automate GitHub workflows  
**Required:** GitHub personal access token  
**Config:**
```json
{
  "tools": {
    "github": {
      "token": "github_pat_...",
      "allowFrom": ["USER_ID"]
    }
  }
}
```

---

## Part 1B: The 9 Pre-Built Skills

### 1. obsidian
**Purpose:** Read, write, and query Obsidian vault from nanobot  
**Why Essential:** Core memory plane; all AOS artifacts stored here for audit and recovery  
**Install:** Built-in or `nanobot skill add obsidian`  
**Config:**
```json
{
  "obsidianVaultPath": "~/Documents/ObsidianVault",
  "obsidianRestEndpoint": "http://localhost:27123",
  "obsidianRestToken": "token..."
}
```

### 2. memory
**Purpose:** Automatic session management, memory consolidation, context optimization  
**Why Essential:** Enables long conversations without token explosion; automatic history summarization  
**Install:** Built-in  
**Config:**
```json
{
  "session": {
    "memoryWindow": 100,
    "maxSessionTokens": 32000,
    "consolidationInterval": 10
  }
}
```

### 3. summarize
**Purpose:** Condense long content while preserving key information  
**Why Essential:** Reduces token burn, improves clarity for reports and digests  
**Install:** Built-in  
**Config:**
```json
{
  "summarize": {
    "targetLength": "concise",
    "preserveCitations": true
  }
}
```

### 4. cron
**Purpose:** Schedule tasks to run on recurring schedules  
**Why Essential:** Enables proactive, always-on operations (daily journal, health checks, backups)  
**Install:** `nanobot skill add cron`  
**Config:**
```json
{
  "cron": {
    "timezone": "UTC",
    "maxConcurrentJobs": 3,
    "jobTimeout": 1800
  }
}
```

### 5. github
**Purpose:** GitHub automation (search repos, create PRs, trigger workflows)  
**Why Essential:** Enables GitOps automation from chat; CI/CD triggers from Discord/Slack  
**Install:** Built-in; requires token  
**Config:**
```json
{
  "github": {
    "token": "github_pat_...",
    "defaultOrg": "your-org",
    "allowFrom": ["OPERATOR_USER_ID"]
  }
}
```

### 6. weather
**Purpose:** Fetch current and forecast weather data  
**Install:** `nanobot skill add weather`  
**Cost:** Free (community weather service)  

### 7. clawhub
**Purpose:** Search and install public skills from ClawHub marketplace  
**Install:** `nanobot skill add clawhub`  
**Note:** Vet all skills before installing  

### 8. skill-creator
**Purpose:** Auto-generate new skills from natural language descriptions  
**Install:** `nanobot skill add skill-creator`  
**Usage:**
```
"Create a new skill: read financial PDFs and extract quarterly earnings"
"Generate a skill for checking DNS records"
```

### 9. tmux
**Purpose:** Control terminal multiplexer for advanced automation  
**Install:** `nanobot skill add tmux`  
**Config:**
```json
{
  "tmux": {
    "defaultSession": "main",
    "allowFrom": ["TRUSTED_USER_ID"]
  }
}
```

---

## Part 2: Essential 5-Skill Quickstart

**⭐ START HERE if you're new to nanobot.** These 5 skills cover 80% of deployments. Learn these first, add others as needed.

### Installation Checklist

- [ ] **obsidian** - Configured with vault path and REST endpoint
- [ ] **memory** - Session consolidation enabled (default)
- [ ] **cron** - Installed and tested with one schedule
- [ ] **summarize** - Tested with a long document
- [ ] **github** - Token configured and tested with one search

---

## Essential Skill 1: Obsidian

**Purpose:** Read, write, and query Obsidian vault; core memory plane for audit and recovery  

### Obsidian Local REST API Setup (Required)

**REST API** = a standard way for programs to exchange data via HTTP requests.

**Step 1: Enable Community Plugins**
1. Open Obsidian → **Settings** → **Community Plugins**
2. Toggle **Turn on community plugins** (if not already on)
3. Search for **"Local REST API"** by Coremute
4. Click **Install**

**Step 2: Configure REST API**
1. Go to **Settings** → **Local REST API**
2. Enable the plugin
3. Note the **Ribbon Icon** appears in left sidebar
4. Click the icon to see your API details:
   - **Listening on:** http://localhost:27123 (or another port)
   - **API Token:** (long hex string)

**Step 3: Update Nanobot Config**
```json
{
  "obsidian": {
    "vaultPath": "/Users/your-username/Documents/MyVault",
    "restEndpoint": "http://localhost:27123",
    "restToken": "<paste-token-here>"
  }
}
```

**Step 4: Test Connection**
```bash
nanobot test-vault --path /Users/your-username/Documents/MyVault
# Should return: ✅ Vault accessible
```

### Common Obsidian Mistakes & Solutions

### ❌ Mistake 1: REST API Plugin Not Installed
**Fix:**
1. Open Obsidian → Settings → Community Plugins
2. Search "Local REST API" by Coremute
3. Click **Install** then **Enable**
4. Restart Obsidian
5. Test: `nanobot test-vault`

### ❌ Mistake 2: Wrong Vault Path in Config
**Fix:**
- Use **absolute path**: `/Users/username/Documents/MyVault` (not `~/MyVault`)
- On Windows: Use forward slashes `/` or escaped backslashes `\\`
- Test path exists: `ls /Users/username/Documents/MyVault`

### ❌ Mistake 3: Using Wrong Port Number
**Fix:**
1. Open Obsidian REST API ribbon icon (left sidebar)
2. Check "Listening on: http://localhost:XXXX"
3. Copy exact port number
4. Update nanobot config with correct port
5. Restart

### ❌ Mistake 4: REST API Token Missing or Revoked
**Fix:**
1. Click REST API ribbon icon
2. Copy fresh token
3. Update nanobot config
4. Restart nanobot

### ❌ Mistake 5: Obsidian Locked/File Sync Conflict
**Fix:**
1. Move vault out of cloud sync folder temporarily, OR
2. Disable cloud sync while nanobot is writing, OR
3. Use `.gitignore` to exclude `.obsidian` folder from sync

---

## Essential Skill 2: Memory

**Purpose:** Automatic session management, memory consolidation, context optimization; enables long conversations without token explosion

### Configuration

```json
{
  "session": {
    "memoryWindow": 100,
    "maxSessionTokens": 32000,
    "consolidationInterval": 10
  }
}
```

### Common Memory Mistakes & Solutions

### ❌ Mistake 1: Memory Consolidation Not Running
**Fix:**
1. Check config: `consolidationInterval: 10` (consolidates every 10 messages)
2. If set to 0, consolidation is **disabled**; change to 10-20
3. Restart nanobot
4. Watch logs for "Consolidating memory..." messages

### ❌ Mistake 2: Context Window Always Hits Max Too Fast
**Fix:**
- Increase from 32000 to 50000-80000 (if your LLM supports it)
- Check your LLM token limits
- Set `maxSessionTokens` to 80% of model's limit

### ❌ Mistake 3: Memory Not Carrying Over Between Sessions
**Fix:**
1. Add to config: `"persistentSessions": true`
2. Restart nanobot
3. Sessions now auto-save to `~/.nanobot/sessions/`

---

## Essential Skill 3: Cron (Scheduling)

**Purpose:** Schedule tasks to run on recurring schedules; enables proactive, always-on operations

### Cron Syntax Cheat Sheet

```
"0 9 * * 1-5" → Every weekday at 9 AM
"0 * * * *" → Every hour
"*/15 * * * *" → Every 15 minutes
"0 23 * * *" → Daily at 11 PM
"0 0 * * 0" → Every Sunday at midnight
```

### Common Cron Mistakes & Solutions

### ❌ Mistake 1: Cron Job Uses Wrong Timezone
**Fix:**
1. Check config: `"timezone": "UTC"` (is this your actual timezone?)
2. If in US Eastern: Change to `"America/New_York"`
3. See full list: `nanobot config cron --list-timezones`
4. Test: `"Schedule test job for 30 seconds from now"`

### ❌ Mistake 2: Scheduled Task Fails Silently
**Fix:**
1. Check job definition: Simple commands work better
2. Increase `jobTimeout` from 1800 to 3600 seconds if jobs take long
3. Check logs: `nanobot logs --filter="cron"`
4. Test task manually first before scheduling

### ❌ Mistake 3: Too Many Jobs Scheduled, System Overload
**Fix:**
1. Reduce `maxConcurrentJobs` from 5 to 2-3
2. Space out job schedules (don't run 5 jobs at 23:50)
3. Example good spacing: 23:50, 00:10, 00:30
4. Check queue: `nanobot cron --status`

---

## Essential Skill 4: Summarize

**Purpose:** Condense long content while preserving key information; reduces token burn, improves clarity

### Configuration

```json
{
  "summarize": {
    "targetLength": "concise",  // Options: "terse", "concise", "detailed"
    "preserveCitations": true
  }
}
```

### Common Summarize Mistakes & Solutions

### ❌ Mistake 1: Summary Is Too Short, Missing Key Details
**Fix:**
1. Change to `"targetLength": "concise"` for better detail retention
2. Or specify exact length: `"Summarize to exactly 5 sentences"`
3. Always use `"preserveCitations": true` to keep references

### ❌ Mistake 2: Summary Is Incoherent or Jargon-Heavy
**Fix:**
1. Add instruction: `"Summarize in simple language for non-technical users"`
2. Or: `"Explain this like you're talking to a 10-year-old"`
3. Test with different personas to find clearest explanation

### ❌ Mistake 3: Losing Source Links and Citations
**Fix:**
1. Enable: `"preserveCitations": true`
2. Request: `"Summarize with at least 3 citations and page numbers"`
3. For Obsidian: `"Summarize and add wiki links to referenced notes"`

---

## Essential Skill 5: GitHub

**Purpose:** GitHub automation (search repos, create PRs, trigger workflows); enables GitOps from chat

### Configuration

```json
{
  "github": {
    "token": "github_pat_...",
    "defaultOrg": "your-org",
    "allowFrom": ["OPERATOR_USER_ID"]
  }
}
```

### Common GitHub Mistakes & Solutions

### ❌ Mistake 1: GitHub Token Has Wrong Permissions
**Fix:**
1. Go to GitHub → Settings → Developer settings → Personal access tokens
2. Create new token with these scopes:
   - ✅ `repo` (full repository access)
   - ✅ `workflow` (to trigger Actions)
   - ✅ `read:org` (to access org repos)
3. Copy exact token (no extra spaces)
4. Update config and restart nanobot

### ❌ Mistake 2: Workflows Not Triggering, or Wrong Workflow Selected
**Fix:**
1. Check file exists: `.github/workflows/deploy.yml` (exact case/spelling)
2. Add trigger at top of workflow file:
   ```yaml
   on:
     workflow_dispatch:
   ```
3. Commit & push to GitHub
4. Retry: `"Trigger the deploy workflow"`

### ❌ Mistake 3: Token Exposed or Accidentally Committed
**Fix:**
1. **Immediately:** Regenerate token in GitHub settings (old one compromised)
2. Remove old token from commit history: `git filter-branch --force --index-filter "git rm --cached --ignore-unmatch .env" -- --all`
3. Create `.env` file and add to `.gitignore`:
   ```bash
   echo "GITHUB_TOKEN=github_pat_..." >> .env
   echo ".env" >> .gitignore
   ```
4. Update nanobot to read from `.env` instead of config file

---

## Part 3: Daily Operations Example

### Daily Journal Using Cron + Obsidian + Summarize

Combine three essential skills to automate daily operational journaling:

**Archivist Personality (add to config or SOUL.md):**

```markdown
# AOS Archivist Identity

Every night at 23:50, run this routine:

1. **Analyze Discord history** from #aos-control and #general channels
2. **Extract key metadata:** milestones, technical hurdles, model performance notes
3. **Format for Obsidian** using structured template
4. **Write to vault** at 00-System/AOS-Journal/{{date}}.md
5. **Include metadata:**
   - Model used today
   - Context efficiency notes
   - 3-sentence summary

**Template:**
# AOS Operational Journal: {{date}}

## 🛠 Technical Milestones
* [List of tasks completed]

## 🧠 Model Context & Performance
* Model Used: {{primary_model}}
* Context Efficiency: [Memory retention rating]

## 📝 Summary
[3-sentence summary of the day]
```

---

## Part 4: Custom Skill Development

### Understanding Skills Architecture

A **Skill** is an extension that adds new capabilities to your nanobot deployment. Unlike pre-built skills, custom skills are:
- Created on-demand for your specific workflows
- Hot-reloaded without system restart
- Composable—built from existing tools
- Operator-injectable via chat interface

### Skill Types

| Skill Type | Best For | Complexity | Deploy Time |
|-----------|----------|-----------|-------------|
| **Prompt-Only** | Reasoning, summarization, content generation | Low | <5 min |
| **Tool-Based** | System integration, API calls, file operations | Medium | 15-30 min |
| **Orchestrated** | Multi-step workflows, delegated sub-tasks | High | 30+ min |

---

## Skill File Structure

```
~/.nanobot/skills/my-custom-skill/
├── SKILL.md          # Metadata + logic (required)
├── README.md         # Usage guide (optional)
├── requirements.txt  # Python dependencies (if tool-based)
└── implementation.py # Python code (if tool-based)
```

---

## SKILL.md Format

### Minimal Prompt-Only Skill Example

```markdown
---
name: standup
description: Summarizes recent work into a standup report
version: 1.0.0
category: reporting
user-invocable: true
---

# Purpose
Generate a concise standup summary from recent Obsidian logs.

# Trigger
User types: `/standup` or mentions "give me a standup"

# Workflow
1. Search Obsidian vault for files modified in last 24 hours from `07-Projects/` folder
2. Read the 3 most recent entries
3. Extract "Completed", "In Progress", "Blockers" sections
4. Format as brief standup (Yesterday → Today → Blockers)

# Output Format
**Yesterday:** [bullet list of completed items]
**Today:** [planned work]
**Blockers:** [any issues or blocked items]
```

### Tool-Based Skill (Python) Example

```markdown
---
name: health-check
description: Monitor system resources and connection health
version: 1.0.0
category: operations
type: tool-based
entry-point: implementation.py::run_health_check
user-invocable: true
tools-required: [shell_exec, message_send]
---

# Purpose
Check CPU, memory, disk, and network health. Alert if thresholds exceeded.

# Trigger
User types: `/health` or automatic daily at 09:00 UTC

# Metrics Checked
- CPU usage > 80% → WARN
- Memory usage > 85% → WARN
- Disk usage > 90% → CRITICAL
- Nanobot process running → OK/FAIL
- Connection to LLM provider → OK/FAIL
```

---

## Operator-Injected Skills

### Install from Repository

1. **Paste link in Discord:**
   ```
   @nanobot install skill: https://github.com/user/nanobot-skill-example
   ```

2. **The bot will:**
   - Analyze SKILL.md file
   - Check for security risks
   - Ask for confirmation

3. **Once approved:**
   - Clones to `~/.nanobot/skills/`
   - Hot-reloads gateway
   - Confirms: "Skill registered. Try `/example` to test."

### Create a New Skill via Chat

```
@nanobot create skill:
- Name: task-tracker
- Goal: Extract TODO items from Discord messages
- Action: Append them to Obsidian inbox.md
- Trigger: Any message starting with "TODO:"
```

---

## Skill Lifecycle Management

### Activation
```
/skill enable task-tracker
/skill list --enabled
```

### Testing
```
/skill test task-tracker --sample "TODO: Review PR #42"
```

### Monitoring
```
/skill stats --skill standup --days 7
```

### Updates
```
/skill reload task-tracker
```

### Deactivation
```
/skill disable task-tracker
/skill uninstall task-tracker
```

---

## Security & Permissions

### Permission Tiers

| Permission | Description | Risk Level |
|-----------|-------------|-----------|
| **read_obsidian** | Read vault files | Low |
| **write_obsidian** | Create/edit notes | Medium |
| **shell_exec** | Run system commands | High |
| **file_write** | Write files outside Obsidian | High |
| **mcp_call** | Access external APIs | High |
| **message_send** | Send external messages | Medium |

### Declaring Permissions

In your SKILL.md:

```markdown
---
name: advanced-task-processor
permissions:
  - read_obsidian
  - write_obsidian
  - shell_exec
  - mcp_call
---
```

---

## Tool Cost & Performance Reference

| Tool | Latency | Cost/Call | Context |
|---|---|---|---|
| web_search | 2-5s | ~$0.01 | 5-10KB |
| web_fetch | 1-3s | Free | 10-50KB |
| file_* | <100ms | Free | <1KB |
| shell_exec | 1-30s | Free | Varies |
| message_send | <100ms | Free | <1KB |
| mcp_call | Varies | Varies | Varies |
| cron_schedule | N/A | Free | None |
| subagent_spawn | High | Variable | +memory |
| github_* | 1-5s | Free | <5KB |

---

## Part 5: Troubleshooting & Common Mistakes

### Tool Allowlisting (AOS Security)

Restrict tools per channel:

```json
{
  "channels": {
    "discord": {
      "allowedTools": {
        "#ctl-nanobot": ["shell", "mcp", "github"],
        "#prd-*": ["web", "file", "message"],
        "#bk-*": ["file", "message", "obsidian"],
        "#res-*": ["web", "message", "obsidian"],
        "default": ["web", "file", "message"]
      }
    }
  }
}
```

---

## Common Tool Mistakes

### ❌ Mistake 1: Tool Allowlist Misspelled - Tool Blocked for All Users
**Problem:** "I configured web_search as allowed, but getting 'tool not allowed' error"  
**Fix:**
- Check exact tool names: `nanobot tools list`
- Common misspellings:
  ```json
  ❌ "allowed_tools": ["web search"]      # Wrong - has space
  ❌ "allowed_tools": ["web_Search"]      # Wrong - capital S
  ✅ "allowed_tools": ["web_search"]      # Correct - exact match
  ```
- Validate config: `nanobot config validate`
- Restart nanobot after config changes

### ❌ Mistake 2: Brave API Key Not Set, web_search Silently Fails
**Problem:** Users try `/web_search`, nothing happens; no error  
**Fix:**
1. Get API key from [Brave Search API](https://api.search.brave.com)
2. Add to config:
   ```json
   {
     "tools": {
       "web_search": {
         "enabled": true,
         "braveApiKey": "YOUR_KEY_HERE"
       }
     }
   }
   ```
3. Restart nanobot
4. Test: `nanobot test-tool web_search "test query"`

### ❌ Mistake 3: shell_exec Allowlist Is Empty - No One Can Run Commands
**Problem:** "Need to execute commands but keep getting 'not allowed' error"  
**Fix:**
```json
✅ With allowlist (specific users allowed):
{
  "tools": {
    "shell_exec": {
      "enabled": true,
      "allowFrom": [
        "DISCORD_USER_ID_1",
        "DISCORD_USER_ID_2",
        "@devops"
      ],
      "timeout": 30,
      "blocklist": ["rm -rf", "sudo dd"]
    }
  }
}
```

### ❌ Mistake 4: MCP Server Connections Fail - Auth Headers Missing
**Problem:** "Cannot connect to MCP server: mydb - Connection refused"  
**Fix:**
1. Verify MCP server running: `curl http://localhost:3000/health`
2. Add auth to config:
   ```json
   {
     "mcpServers": {
       "mydb": {
         "command": "python",
         "args": ["/path/to/db_mcp_server.py"],
         "customHeaders": {
           "Authorization": "Bearer YOUR_TOKEN",
           "X-API-Key": "your-api-key"
         }
       }
     }
   }
   ```
3. Restart nanobot
4. Test: `nanobot test-tool mcp_call mydb --query "SELECT 1"`

### ❌ Mistake 5: Cron Jobs Never Fire - Wrong Timezone or Syntax
**Problem:** Scheduled job set for "9 AM daily" but never runs; or runs at wrong time  
**Fix:**
1. Check timezone: UTC or your actual timezone?
2. Verify cron syntax: `nanobot cron validate "0 9 * * *"`
3. Common syntax mistakes:
   ```
   ❌ "9 * * * *"       # Runs every hour at minute 9 (not 9 AM!)
   ✅ "0 9 * * *"       # Runs at 9 AM
   
   ❌ "0 9 * * * *"     # Too many fields (cron has 5, not 6)
   ✅ "0 9 * * *"       # 5 fields: minute, hour, day, month, day-of-week
   ```
4. List cron jobs: `nanobot cron list`

### ❌ Mistake 6: Subagent Spawning Consumes Too Many Tokens - Cost Explosion
**Problem:** Spawned 5 agents, bill was 10x expected  
**Fix:**
```python
✅ Better - shared context, split specific tasks:
agents = await nanobot.spawn_subagents(
    count=3,
    shared_context=True,  # Agents share memory
    tasks=[
        "Research company A",
        "Research company B", 
        "Research company C"
    ]
)
```
2. Estimate cost: `nanobot estimate-cost --agents=5 --context=shared`
3. Use sparingly: Reserve for complex, high-value tasks
4. Monitor: `nanobot logs --filter="subagent" | grep "tokens_used"`

---

## Common Skill Mistakes

### ❌ Mistake 1: SKILL.md Metadata Syntax Wrong
**Problem:** Skill won't register; error: "Invalid YAML metadata"  
**Fix:**
- Field names must be lowercase with hyphens, not underscores:
  ```
  ❌ user_invocable: true       # Wrong
  ✅ user-invocable: true       # Correct
  ```
- No colons in values without quotes:
  ```
  ❌ description: Summarizes recent work
  ✅ description: "Summarizes recent work"
  ```
- Validate: `nanobot skill validate ~/.nanobot/skills/my-skill/SKILL.md`

### ❌ Mistake 2: Python Skill Missing Dependencies
**Problem:** Skill runs, then crashes: `ModuleNotFoundError: psutil`  
**Fix:**
1. Add to `requirements.txt`:
   ```
   psutil==6.0.0
   requests==2.31.0
   ```
2. Install: `pip install -r ~/.nanobot/skills/my-skill/requirements.txt`
3. Or let nanobot auto-install: Check config `"auto_install_deps": true`

### ❌ Mistake 3: Skill References Undefined Tool or Permission
**Problem:** Skill runs but action fails: "Tool not found: web_search"  
**Fix:**
```yaml
tools-required:
  - web_search
  - file_read
  - message_send
```
Check available: `nanobot tools list`

### ❌ Mistake 4: Skill Works Locally, But Fails in Discord/Slack
**Problem:** `/skill-name` works in CLI, fails in Discord  
**Fix:**
```python
✅ Returns Discord embed - looks professional
return {
    "type": "discord_embed",
    "title": "System Health",
    "fields": [
        {"name": "CPU", "value": "45%"},
        {"name": "Memory", "value": "60%"}
    ]
}
```

### ❌ Mistake 5: Hot Reload Doesn't Work - Changes Not Applied
**Problem:** Modified skill code, but old version still runs  
**Fix:**
1. After modifying code, reload:
   ```
   /skill reload my-skill
   ```
2. Verify reload: `nanobot skills --status` should show "Loaded: 2 minutes ago"
3. If still not working: Restart nanobot entirely
   ```bash
   systemctl stop nanobot
   nanobot gateway
   ```

### ❌ Mistake 6: Prompt Bloat = High Token Usage & Cost
**Problem:** Skill uses 5,000+ tokens per run  
**Fix:**
1. Trim system prompt (no unnecessary context)
2. Add token limit: `max_tokens: 500`
3. Test cost: `nanobot skill bench my-skill`

---

## Troubleshooting Reference Table

| Problem | Cause | Solution |
|---------|-------|----------|
| **Skill won't register** | Malformed SKILL.md metadata | Run `nanobot skill validate` |
| **Skill runs but no output** | Missing tool permissions | Add to `tools-required:` |
| **High token usage** | Inefficient prompting | Add context limits; break into sub-tasks |
| **Skill crashes silently** | Python dependency missing | Add to `requirements.txt` |
| **Changes don't take effect** | Skill not reloaded | Run `/skill reload skillname` |
| **Tool disabled error** | Not in allowlist | Check channel allowlist config |
| **Tool timeout** | Command too slow | Increase `timeout` or optimize |
| **MCP connection failed** | Server down or wrong auth | Verify server running; check headers |

---

## Best Practices

### Skill Naming
- **Action verbs:** `generate-`, `summarize-`, `fetch-`, `track-`
- **Domains:** `obsidian-`, `discord-`, `github-`, `slack-`
- **Examples:** `summarize-daily-logs`, `fetch-github-prs`, `track-costs`

### Documentation
Every skill should include:
- Purpose (one-line)
- Trigger (how it's invoked)
- Prerequisites
- Setup instructions
- Example usage
- Output description
- Troubleshooting
- Cost estimate (if applicable)

### Testing
```bash
nanobot skill test --skill my-skill --sample "test input" --verbose
nanobot skill validate --skill my-skill
nanobot skill inspect --skill my-skill --show-permissions
```

### Version Management
```markdown
---
version: 1.0.0  # Semantic versioning
compatibility: "nanobot >= 0.1.4"
changelog:
  1.0.0: Initial release
  0.9.0: Beta testing
---
```

---

## Real-World Examples

### Example 1: Obsidian Note Tagging (Prompt-Only)

```markdown
---
name: auto-tag
description: Tag Obsidian notes based on Discord channel
user-invocable: false
trigger: on_note_create
---

# When triggered
When a note is created from Discord message, automatically:
1. Extract the source channel name
2. Prepend tag: #channel-name
3. Add to daily journal entry
4. Link back to Discord message thread
```

### Example 2: Weekly Digest (Tool-Based)

```markdown
---
name: weekly-digest
description: Compile week's work into formatted report
type: tool-based
trigger: manual or cron:0 9 * * 1
permissions:
  - read_obsidian
  - write_obsidian
  - message_send
---

# Workflow
1. Search Obsidian for notes created/modified Mon-Fri
2. Group by project/domain
3. Count completions, blockers, metrics
4. Generate formatted report
5. Post to #weekly-digest
6. Archive report in Obsidian
```

---

## Revision History

| Date | Version | Change |
|---|---|---|
| 2026-02-26 | 2.0.0 | Consolidated Tools & Skills Reference, Essential AOS Skills, and Advanced Skill Development into single comprehensive guide with progressive learning path |
| 2026-02-25 | 1.0.0 | Original separate documents |
