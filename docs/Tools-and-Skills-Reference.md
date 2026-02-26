# Tools & Skills Reference

Complete reference for all built-in tools and pre-built skills in nanobot, plus how to create custom tools/skills.

## Document Control
- **Owner:**
- **Version:** 1.0.0
- **Last Updated:** 2026-02-25
- **Status:** Active

## 1. Tools Overview

**Tools** are the executable actions nanobot can perform. All tools are opt-in and can be allowlisted (approved) per channel.

**What's a Tool?** Like an extension or app—it's a single action the bot can do (search the web, write a file, run a command, etc.)

### Quick Tool Matrix

| Tool | Purpose | Risk Tier | Notes |
|---|---|---|---|
| **web_search** | Real-time web search (Brave API) | Low | Requires Brave API key |
| **web_fetch** | Retrieve & parse full web pages | Low | Limited by rate limits & context |
| **file_read** | Read files (workspace-scoped) | Low | Sandbox-enforced; no parent dir escape |
| **file_write** | Create/overwrite files | Medium | Audit-logged; idempotent support |
| **file_edit** | Edit specific lines in files | Medium | Line-based for precision; audit-logged |
| **file_list** | List directory contents | Low | Only workspace-visible directories |
| **shell_exec** | Execute shell commands | High | Operator-restricted; all commands logged |
| **message_send** | Send alerts/notifications | Low | Cross-channel notifications possible |
| **mcp_call** | Call MCP server tools | High | Custom auth headers supported |
| **cron_schedule** | Schedule recurring tasks | Medium | Cron syntax; can be expensive at scale |
| **subagent_spawn** | Launch parallel agents | High | Spawned agents have own context/memory |
| **github_search** | Search GitHub repos | Low | No auth needed; public access |
| **github_pr_create** | Create pull requests | High | Requires GitHub token; write access |
| **github_action_trigger** | Trigger workflows | High | Requires org/repo permissions |

## 2. Tool Details & Configuration

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
**Required API Key:** No  
**Cost:** None  
**Config:** Built-in, no config needed  
**Usage:** `nanobot: "Summarize this page: https://example.com/article"`

### file_read / file_write / file_edit / file_list
**Purpose:** Access workspace files  
**Workspace Scope:** All paths are relative to agent workspace (e.g., `~/.nanobot/workspace/`)  
**Security:** Cannot escape workspace root (e.g., cannot read `/etc/passwd`)  
**Config:** No special config; enabled by default with workspace binding  
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
**Restrictions:**
- Only operators with explicit allowlist can invoke
- All commands logged with timestamp, operator, exit code, output
- Dangerous commands (rm -rf /, sudo) can be blocklisted
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
**Usage:** `nanobot: "Deploy the latest build: make deploy"`

### message_send
**Purpose:** Send cross-channel alerts, escalations, notifications  
**Config:** Built-in  
**Usage Examples:**
```
"Alert team in #incidents that model latency > 5s"
"Send daily summary to #ops-digest"
"Notify owner in #alerts about high spend ($50/day)"
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
**Docs:** https://modelcontextprotocol.io

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
**Config:** No special config; enabled via Cron skill  
**Usage:** `nanobot: "Schedule a daily digest at 23:50 every day"`

### subagent_spawn
**Purpose:** Launch independent agents for parallel work  
**Use Cases:** 
- Parallel research across multiple sources
- Delegated tasks (e.g., "research this, fix that, monitor that")
- Complex multi-step workflows
- Fact-checking and verification sub-processes
**Config:** No special config; built-in  
**Usage:**
```
"Spawn 3 agents to each research a different competitor, then synthesize findings"
"Spawn agent to monitor GitHub repo for issues while you work on design doc"
```

#### Sub-Agent Orchestration (Advanced Pattern)

Modern deployments use **Agent Orchestration** to decompose complex tasks into specialized sub-agents:

**Architecture:**

| Component | Role | Model Priority | Focus |
|-----------|------|---|---|
| **Manager Agent** | Primary orchestrator | Fast (e.g., Sonnet) | Planning, coordination, synthesis |
| **Fact-Checker Sub-Agent** | Verification & auditing | Smart (e.g., local 70B) | Technical accuracy, hallucination detection |
| **Researcher Sub-Agent** | Information gathering | Fast (e.g., 8B) | Web search, data collection |

**Example Workflow:**

```
User: "Research competitors and tell me 3 novel features we could implement"

1. Manager Agent receives request
2. Manager spawns Researcher: "Find top 5 competitors in our space"
3. Manager spawns Analyzer: "Extract novel features from their product"
4. Manager spawns Fact-Checker: "Verify these features are real and current"
5. Manager synthesizes: "Here are 3 novel features we don't have yet"
```

**Configuration:**

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

**Safety Controls:**

- **maxSpawnDepth:** Prevents infinite recursion (agents spawning agents spawning agents...)
- **maxConcurrentAgents:** Limits parallel spawns to prevent resource exhaustion
- **costCeiling:** Daily budget cap for sub-agent API calls
- **timeout_seconds:** Hard kill timeout for stuck sub-agents

**Manual Invocation (Discord/Slack):**

```
@BotName spawn fact-checker to verify: "The RTX 4090 has 24GB VRAM"

→ Creates isolated agent with:
   - Access to web search + knowledge base
   - Specific verification prompt
   - Confidence scoring
   - Source citations

Result: 
✅ VERIFIED (98% confidence)
Source: NVIDIA official specs
Details: RTX 4090 has 24GB GDDR6X memory
```

**Cost Optimization Tip:**

Pair expensive orchestration with cheap models:
- Use **Tier C model (fast, cheap)** for initial research
- Use **Tier A model (slow, powerful)** for final synthesis/verification only
- Example: 3 parallel Tier C searches → 1 Tier A synthesis = $0.05 total vs. $0.45 for direct Tier A

**See Also:** [Advanced Skill Development](Advanced-Skill-Development.md) for custom orchestration patterns.

### github_search / github_pr_create / github_action_trigger
**Purpose:** Automate GitHub workflows  
**Required:** GitHub personal access token (for create/trigger)  
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
**Usage Examples:**
```
"Search nanobot repo for issues tagged 'bug'"
"Create a PR to add docstrings to parser.py"
"Trigger the 'deploy' workflow in main branch"
```

## 3. Skills Overview

**Skills** are pre-built, opinionated bundles of tools configured for specific business functions. (Think of a skill as a "ready-to-use package" that combines multiple tools to solve a specific problem.)

### Built-In Skills

#### 1. obsidian
**Purpose:** Read, write, and query Obsidian vault from nanobot  
**Install:** `nanobot skill add obsidian` (or included by default)  
**Config:**
```json
{
  "obsidianVaultPath": "~/Documents/ObsidianVault",
  "obsidianRestEndpoint": "http://localhost:27123",
  "obsidianRestToken": "token..."
}
```
**Usage:**
```
"Read the project roadmap from Roadmap.md"
"Write a research summary to 08-Research/AI-Safety/2026-02.md"
"Query all notes tagged with #decision"
```

#### 2. memory
**Purpose:** Manage conversation sessions and memory consolidation  
**Install:** Built-in  
**Features:**
- Automatic memory consolidation for long conversations
- Session isolation per user
- Token budget enforcement
- Retention policies
**Usage:** Automatic; configure via:
```json
{
  "session": {
    "memoryWindow": 100,
    "maxSessionTokens": 32000
  }
}
```

#### 3. summarize
**Purpose:** Condense long content while preserving key information  
**Install:** Built-in  
**Config:** No special config  
**Usage:**
```
"Summarize this 50-page PDF to 1 page"
"Create a 3-sentence summary of today's Discord activity"
"Condense this research into key findings + citations"
```

#### 4. cron (Scheduler)
**Purpose:** Run tasks on recurring schedules  
**Install:** `nanobot skill add cron`  
**Config:**
```json
{
  "cron": {
    "enabled": true,
    "allowedJobs": ["daily_digest", "backup", "health_check"]
  }
}
```
**Usage Examples:**
```
"Schedule a health check every hour"
"Run a daily journal summarizer at 23:50"
"Backup workspace every Sunday at 2 AM"
```

#### 5. weather
**Purpose:** Fetch current and forecast weather data  
**Install:** `nanobot skill add weather`  
**Config:** No API key needed (free service)  
**Usage:**
```
"What's the weather in San Francisco?"
"Get 5-day forecast for New York"
```

#### 6. clawhub
**Purpose:** Search and install public skills from ClawHub marketplace  
**Install:** `nanobot skill add clawhub`  
**Usage:**
```
"Search ClawHub for 'email' skills"
"Install the 'LinkedIn Poster' skill from ClawHub"
```
**Note:** ClawHub is a skill marketplace; vet all skills before installing.

#### 7. skill-creator
**Purpose:** Auto-generate new skills from natural language descriptions  
**Install:** `nanobot skill add skill-creator`  
**Usage:**
```
"Create a new skill: read financial PDFs and extract quarterly earnings"
"Generate a skill for checking DNS records"
```

#### 8. tmux
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
**Usage:** `"Start a tmux session 'dev' and run my build script"`

#### 9. github
**Purpose:** GitHub automation (search, create PRs, trigger workflows)  
**Install:** Built-in  
**Config:**
```json
{
  "github": {
    "token": "github_pat_...",
    "defaultOrg": "your-org"
  }
}
```
**Usage:**
```
"Search our 'frontend' repo for TypeScript issues"
"Create a PR to update dependencies"
"Trigger 'test' workflow"
```

## 4. Custom Tools

Add custom tool implementations to extend nanobot:

**Example: Custom Weather Tool**
```python
# ~/.nanobot/tools/custom_weather.py
from nanobot.agent.tools import BaseTool

class CustomWeatherTool(BaseTool):
    name = "custom_weather"
    description = "Fetch weather from internal weather service"
    
    async def execute(self, location: str) -> dict:
        # Your custom logic here
        return {"temperature": 72, "condition": "sunny"}
```

**Register in config:**
```json
{
  "customTools": [
    "~/.nanobot/tools/custom_weather"
  ]
}
```

## 5. Custom Skills

Create a new skill by bundling tools with business logic:

**Example: Bookkeeping Reconciliation Skill**
```python
# ~/.nanobot/skills/bk_reconcile.py
from nanobot.agent.skills import BaseSkill

class BookkeepingReconcileSkill(BaseSkill):
    id = "bk_reconcile"
    description = "Reconcile transactions against ledger"
    
    async def execute(self, csv_file: str, ledger_date: str) -> dict:
        # 1. Read CSV via file_read tool
        # 2. Parse transactions
        # 3. Query ledger (via MCP or database tool)
        # 4. identify discrepancies
        # 5. Write summary to Obsidian
        return {"status": "reconciled", "discrepancies": [...]}
```

**Install:**
```bash
nanobot skill add ~/.nanobot/skills/bk_reconcile
```

## 6. Tool Allowlisting (AOS Security)

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

## 7. Tool Cost & Performance Reference

| Tool | Latency | Cost/Call | Context Impact |
|---|---|---|---|
| web_search | 2-5s | ~$0.01 | 5-10KB |
| web_fetch | 1-3s | None | 10-50KB |
| file_* | <100ms | None | <1KB |
| shell_exec | 1-30s | None | Varies |
| message_send | <100ms | None | <1KB |
| mcp_call | Varies | Varies | Depends |
| cron_schedule | N/A | None | None |
| subagent_spawn | High | +cost per agent | +memory |
| github_* | 1-5s | None | <5KB |

## 8. Common Mistakes & Solutions

### ❌ Mistake 1: Tool Allowlist Misspelled - Tool Blocked for All Users
**Problem:** "I configured web_search as allowed, but getting 'tool not allowed' error"  
**Why:** Tool name in allowlist doesn't match actual tool name; uses underscore instead of exact match  
**Fix:**
- Check exact tool names: `nanobot tools list`
- Common misspellings:
  ```json
  ❌ "allowed_tools": ["web search"]      # Wrong - has space
  ❌ "allowed_tools": ["web_Search"]      # Wrong - capital S
  ✅ "allowed_tools": ["web_search"]      # Correct - exact match
  
  ❌ "allowed_tools": ["github"]          # Too broad
  ✅ "allowed_tools": ["github_search", "github_pr_create"]  # Explicit
  ```
- Validate config: `nanobot config validate`
- Restart nanobot after config changes

### ❌ Mistake 2: Brave API Key Not Set, web_search Silently Fails
**Problem:** Users try `/web_search`, nothing happens; no error  
**Why:** Brave API key not configured, tool disabled but no error shown  
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
5. Verify logs: `nanobot logs | grep "brave" | tail -5`

### ❌ Mistake 3: shell_exec Allowlist Is Empty - No One Can Run Commands
**Problem:** "Need to execute commands but keep getting 'not allowed' error"  
**Why:** Didn't configure `allowFrom` list; defaults to empty (blocks everyone)  
**Fix:**
```json
❌ No allowlist (everyone blocked):
{
  "tools": {
    "shell_exec": {
      "enabled": true
      // Missing allowFrom!
    }
  }
}

✅ With allowlist (specific users allowed):
{
  "tools": {
    "shell_exec": {
      "enabled": true,
      "allowFrom": [
        "DISCORD_USER_ID_1",
        "DISCORD_USER_ID_2",
        "@devops"  // Role-based
      ],
      "timeout": 30,
      "blocklist": ["rm -rf", "sudo dd"]
    }
  }
}
```
2. Get user IDs: In Discord, @mention user and note the ID that appears
3. Restart nanobot
4. Test with allowed user: Have them try `/shell_exec ls`

### ❌ Mistake 4: MCP Server Connections Fail - Auth Headers Missing
**Problem:** "Cannot connect to MCP server: mydb - Connection refused"  
**Why:** MCP server requires authentication but config has no auth headers  
**Fix:**
1. Verify MCP server is running: `curl http://localhost:3000/health` (replace port/URL)
2. Check if it requires auth: Check MCP server documentation for required headers
3. Add auth to config:
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
4. Restart nanobot
5. Test: `nanobot test-tool mcp_call mydb --query "SELECT 1"`

### ❌ Mistake 5: Cron Jobs Never Fire - Wrong Timezone or Syntax
**Problem:** Scheduled job set for "9 AM daily" but never runs; or runs at wrong time  
**Why:** Cron syntax wrong, or timezone mismatch  
**Fix:**
1. Check timezone in config:
   ```json
   {
     "cron": {
       "timezone": "UTC"  // ← Is this your actual timezone?
     }
   }
   ```
2. Verify cron syntax: `nanobot cron validate "0 9 * * *"`
3. Common syntax mistakes:
   ```
   ❌ "9 * * * *"       # Runs every hour at minute 9 (not 9 AM!)
   ✅ "0 9 * * *"       # Runs at 9 AM (hour=9, minute=0)
   
   ❌ "0 9 * * * *"     # Too many fields (cron has 5, not 6)
   ✅ "0 9 * * *"       # 5 fields: minute, hour, day, month, day-of-week
   
   ❌ "at 9am tomorrow"  # Natural language doesn't work
   ✅ "0 9 * * *"       # Must be cron syntax
   ```
4. List cron jobs: `nanobot cron list` - check next run time
5. If timezone wrong: Change to your zone (`America/New_York`, `Europe/London`, etc.)

### ❌ Mistake 6: Subagent Spawning Consumes Too Many Tokens - Cost Explosion
**Problem:** Spawned 5 agents for parallel research, bill was 10x expected  
**Why:** Each agent runs full model reasoning + context; not sharing memory efficiently  
**Fix:**
```python
❌ Inefficient - each agent pays full context cost:
agents = await nanobot.spawn_subagents(
    count=5,
    task="Research competitor X",
    # Each agent gets full context = 5x cost
)

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
2. Estimate cost before spawning: `nanobot estimate-cost --agents=5 --context=shared`
3. Use sparingly: Reserve for complex, high-value tasks (not routine work)
4. Monitor: `nanobot logs --filter="subagent" | grep "tokens_used"`

---

## 9. Troubleshooting Tools

**Tool disabled error:**
```
"web_search" tool not available in #bk-finances
```
Fix: Add `web` to allowed tools for that channel.

**Tool timeout:**
```
shell_exec timed out after 30s
```
Fix: Increase `timeout` config or optimize command.

**MCP connection failed:**
```
Cannot connect to MCP server: mydb
```
Fix: Verify MCP server is running and auth headers are correct.

## 10. Revision History

| Date | Version | Change |
|---|---|---|
| 2026-02-25 | 1.0.0 | Initial comprehensive reference for 14 tools and 9 pre-built skills |
