# Advanced Skill Development

**Purpose:** Create custom skills beyond pre-built capabilities. Build new commands, tools, and automations tailored to your deployment.

## 1. Understanding Skills Architecture

A **Skill** is an extension that adds new capabilities to your nanobot deployment. Unlike pre-built skills, custom skills are:
- Created on-demand for your specific workflows
- Hot-reloaded without system restart
- Composable—built from existing tools
- Operator-injectable via chat interface

### 1.1 Skill Types

| Skill Type | Best For | Complexity | Deploy Time |
|-----------|----------|-----------|-------------|
| **Prompt-Only** | Reasoning, summarization, content generation | Low | <5 min |
| **Tool-Based** | System integration, API calls, file operations | Medium | 15-30 min |
| **Orchestrated** | Multi-step workflows, delegated sub-tasks | High | 30+ min |

## 2. Skill File Structure

Every skill needs this directory structure:

```
~/.nanobot/skills/my-custom-skill/
├── SKILL.md          # Metadata + logic (required)
├── README.md         # Usage guide (optional)
├── requirements.txt  # Python dependencies (if tool-based)
└── implementation.py # Python code (if tool-based)
```

## 3. SKILL.md Format

The `SKILL.md` file defines your skill's metadata and behavior.

### 3.1 Minimal Prompt-Only Skill

**Example: Daily Standup Generator**

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

# Example Output
**Yesterday:** 
- Reviewed PR #123
- Updated cost calculator guide

**Today:**
- Finish skill development documentation
- Deploy to staging

**Blockers:**
- Waiting on LLM provider API key response
```

### 3.2 Tool-Based Skill (Python)

**Example: System Health Monitor**

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

# Output
Color-coded status board (Slack/Discord format)

# Configuration
threshold_cpu: 80
threshold_memory: 85
threshold_disk: 90
alert_channel: #alerts
```

**Associated `implementation.py`:**

```python
#!/usr/bin/env python3
"""Health check monitoring skill."""

import psutil
import json
from datetime import datetime

async def run_health_check(context):
    """Check system health and return status board."""
    
    metrics = {
        "timestamp": datetime.utcnow().isoformat(),
        "cpu_percent": psutil.cpu_percent(interval=1),
        "memory_percent": psutil.virtual_memory().percent,
        "disk_percent": psutil.disk_usage("/").percent,
        "nanobot_running": check_nanobot_process(),
        "llm_provider_healthy": await check_llm_connection(),
    }
    
    # Determine status
    status = "🟢 HEALTHY"
    if metrics["cpu_percent"] > 80 or metrics["memory_percent"] > 85:
        status = "🟡 WARNING"
    if metrics["disk_percent"] > 90:
        status = "🔴 CRITICAL"
    
    # Format for Discord/Slack
    message = format_status_board(metrics, status)
    
    return {
        "status": status,
        "metrics": metrics,
        "message": message
    }

def check_nanobot_process():
    """Check if nanobot is running."""
    for proc in psutil.process_iter(['name']):
        if 'nanobot' in proc.info['name'].lower():
            return True
    return False

async def check_llm_connection():
    """Verify LLM provider connectivity."""
    # Implementation depends on configured provider
    # (OpenAI, Anthropic, local Ollama, etc.)
    pass

def format_status_board(metrics, status):
    """Format metrics as readable board."""
    board = f"""
**System Health Report** {status}

**CPU:** {metrics['cpu_percent']}%
**Memory:** {metrics['memory_percent']}%
**Disk:** {metrics['disk_percent']}%

**Services:**
- Nanobot: {'✅ Running' if metrics['nanobot_running'] else '❌ Down'}
- LLM Provider: {'✅ Connected' if metrics['llm_provider_healthy'] else '❌ Error'}

*Updated: {metrics['timestamp']}*
    """
    return board
```

## 4. Operator-Injected Skills (Discord)

You can inject new skills directly through Discord without touching terminals.

### 4.1 From a Repository

If you find a skill on GitHub or a skill marketplace:

1. **Paste the link in Discord:**
   ```
   @nanobot install skill: https://github.com/user/nanobot-skill-example
   ```

2. **The bot will:**
   - Analyze the SKILL.md file
   - Check for security risks (shell_exec, file_write permissions)
   - Ask for confirmation: "Install 'Example Skill' from user/nanobot-skill-example?"

3. **Once approved:**
   - Clones to `~/.nanobot/skills/`
   - Hot-reloads the gateway
   - Confirms: "Skill registered. Try `/example` to test."

### 4.2 Create a New Skill via Chat

You can tell nanobot to write its own skill:

```
@nanobot create skill:
- Name: task-tracker
- Goal: Extract TODO items from my Discord messages
- Action: Append them to Obsidian inbox.md
- Trigger: Any message starting with "TODO:"
```

The bot will:
1. Draft the SKILL.md with appropriate metadata
2. Generate associated Python code (if tool-based)
3. Write files to the workspace
4. Ask for confirmation before activation
5. Test the new skill with a sample message

## 5. Skill Lifecycle Management

### 5.1 Activation

Once deployed, a skill is **inactive by default**. Enable it:

```
/skill enable task-tracker
/skill list --enabled
```

### 5.2 Testing

Before deploying to production:

```
/skill test task-tracker --sample "TODO: Review PR #42"
```

### 5.3 Monitoring

Track how often your skills are used:

```
/skill stats --skill standup --days 7
```

Returns: Usage count, average execution time, error rate, tokens burned.

### 5.4 Updates

When you update a SKILL.md or implementation.py:

```
/skill reload task-tracker
```

No restart needed—changes take effect immediately.

### 5.5 Deactivation

```
/skill disable task-tracker
/skill uninstall task-tracker  # Removes files from workspace
```

## 6. Security & Permissions

Skills are sandboxed by default. Access to dangerous operations requires explicit allowlisting.

### 6.1 Permission Tiers

| Permission | Description | Risk Level | Approval |
|-----------|-------------|-----------|----------|
| **read_obsidian** | Read vault files | Low | Auto-allow |
| **write_obsidian** | Create/edit notes | Medium | Operator approval |
| **shell_exec** | Run system commands | High | Config allowlist |
| **file_write** | Write files outside Obsidian | High | Config allowlist |
| **mcp_call** | Access external APIs | High | Config allowlist |
| **message_send** | Send external messages | Medium | Config allowlist |

### 6.2 Declaring Permissions

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

When someone tries to enable this skill, nanobot will warn:
```
⚠️ This skill requests:
- Shell execution (HIGH RISK)
- MCP integration (HIGH RISK)

Only enable if you trust the source and understand what it does.
```

### 6.3 For Shared Deployments

If running nanobot across teams, restrict skill creation:

```
# In nanobot.json
"skills": {
  "allow_operator_injection": true,      # Operators can create/inject
  "require_approval_for_dangerous": true, # Shell/file access needs approval
  "sandbox_shell_commands": true         # Isolate shell execution
}
```

## 7. Real-World Examples

### 7.1 Obsidian Note Tagging (Prompt-Only)

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

# Example
Discord: #prd-marketing channel
Message: "New campaign idea: seasonal push"
→ Obsidian note created with:
  - Title: "New campaign idea: seasonal push"
  - Tags: #prd-marketing
  - Link: discord://channel/prd-marketing/msg_id
```

### 7.2 Weekly Digest (Tool-Based)

```markdown
---
name: weekly-digest
description: Compile week's work into formatted report
version: 1.0.0
type: tool-based
entry-point: digest.py::generate_weekly_digest
trigger: manual or cron:0 9 * * 1  # Mon 9 AM
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
5. Post to #weekly-digest Discord channel
6. Archive report in Obsidian
```

### 7.3 Cost Tracker (Integrated with LLM Logging)

```markdown
---
name: cost-tracker
description: Monitor daily spend across LLM providers
type: tool-based
permissions:
  - read_obsidian
trigger: cron:0 20 * * *  # 8 PM daily
---

# Pulls from
- Nanobot's internal token ledger
- Configured LLM provider rates
- Project/channel cost breakdowns

# Outputs
- Daily cost report
- Weekly forecast
- Provider efficiency comparison
- Alert if approaching daily cap
```

## 8. Best Practices

### 8.1 Skill Naming

Use consistent patterns:
- **Action verbs:** `generate-`, `summarize-`, `fetch-`, `track-`
- **Domains:** `obsidian-`, `discord-`, `github-`, `slack-`
- **Examples:** `summarize-daily-logs`, `fetch-github-prs`, `track-costs`

### 8.2 Documentation

Every skill should include:

```markdown
# [Skill Name]

**Purpose:** One-line description

**Trigger:** How it's invoked (/command, automatic, manual)

**Prerequisites:** Required tools, LLM providers, channels

**Setup:** Step-by-step instructions

**Example Usage:** Copy-paste example

**Output:** What to expect

**Troubleshooting:** Common issues and fixes

**Cost:** Estimated tokens/month if applicable
```

### 8.3 Testing Before Deployment

```bash
# Test locally
nanobot skill test --skill my-skill --sample "test input" --verbose

# Check for errors
nanobot skill validate --skill my-skill

# Review permissions
nanobot skill inspect --skill my-skill --show-permissions
```

### 8.4 Version Management

Include version in SKILL.md metadata:

```markdown
---
version: 1.0.0  # Semantic versioning
compatibility: "nanobot >= 0.1.4"
changelog:
  1.0.0: Initial release
  0.9.0: Beta testing
---
```

## 9. Extending with MCP (Model Context Protocol)

Skills can integrate external tools via MCP—databases, file systems, APIs, etc.

### 9.1 Example: Database Query Skill

```markdown
---
name: query-database
type: tool-based
mcp-tools:
  - sqlite  # Built-in SQLite support
permissions:
  - mcp_call
---

# When user asks
"Show me all tasks from last 30 days"

# Skill processes
1. Parse natural language for query intent
2. Translate to SQL: SELECT * FROM tasks WHERE created >= date('now', '-30 days')
3. Execute via MCP sqlite provider
4. Format results as table or summary
5. Post to Discord
```

## 10. Troubleshooting

| Problem | Cause | Solution |
|---------|-------|----------|
| **Skill won't register** | Malformed SKILL.md metadata | Run `nanobot skill validate` to check syntax |
| **Skill runs but no output** | Missing tool permissions | Add to `permissions:` in SKILL.md |
| **High token usage** | Inefficient prompting | Add context limits or break into sub-tasks |
| **Skill crashes silently** | Python dependency missing | Add to `requirements.txt` and reinstall |
| **Changes don't take effect** | Skill not reloaded | Run `/skill reload skillname` |

## 11. See Also

- [Tools & Skills Reference](Tools-and-Skills-Reference.md) — Complete list of 14 built-in tools
- [Governance Policies](Governance-Policies-and-Config-Examples.md) — Skill allowlisting for teams
- [Workflow Examples](Workflow-Examples-and-Recipes.md) — Real patterns you can adapt
- [Cost Calculator](Cost-Calculator-and-Optimization.md) — Estimate skill execution costs
