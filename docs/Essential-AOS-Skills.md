# Essential AOS Skills

This document lists the 5 core skills (pre-built bundles) recommended for every Nanobot Agentic Operating System deployment. For complete reference including all 9 pre-built skills, 14 tools, and custom skill creation, see [Tools & Skills Reference](Tools-and-Skills-Reference.md).

> **Plain-English Note:** A skill is a pre-built bundle of tools configured for a specific business workflow. Think of skills as "ready-to-use packages" — install these five first, then add others as needed.

## Document Control
- **Owner:**
- **Version:** 2.0.0
- **Last Updated:** 2026-02-25
- **Status:** Active

## 1. The 5 Essential Skills

### 1. obsidian
**Purpose:** Read, write, and query Obsidian vault from nanobot  
**Why Essential:** Core memory plane; all AOS artifacts stored here for audit and recovery  
**Install:** Built-in or `nanobot skill add obsidian`  
**Config:**
```json
{
  "obsidianVaultPath": "~/Documents/ObsidianVault",
  "obsidianRestEndpoint": "http://localhost:18790",
  "obsidianRestToken": "token..."
}
```

#### Obsidian Local REST API Setup (Required)

**REST API** = a standard way for programs to talk to each other and exchange data. In this case, it lets nanobot read and write to Obsidian using HTTP requests (the same protocol your web browser uses).

To enable nanobot ↔ Obsidian communication, set up the Local REST API plugin.

**Step 1: Enable Community Plugins**
1. Open Obsidian → **Settings** → **Community Plugins**
2. Toggle **Turn on community plugins** (if not already on)
3. Search for **"Local REST API"** by Coremute
4. Click **Install**

**Step 2: Configure REST API**
1. Go to **Settings** → **Local REST API**
2. Enable the plugin
3. Note the **Ribbon Icon** appears in left sidebar (scroll down to see it)
4. Click the icon to see your API details:
   - **Listening on:** http://localhost:27123 (or another port)
   - **API Token:** (long hex string)

**Step 3: Get Your Token**
1. Click the REST API ribbon icon
2. Copy the access token shown

**Step 4: Update Nanobot Config**
```json
{
  "obsidian": {
    "vaultPath": "/Users/your-username/Documents/MyVault",
    "restEndpoint": "http://localhost:27123",
    "restToken": "<paste-token-here>"
  }
}
```

**Step 5: Test Connection**
```bash
# In nanobot terminal, verify Obsidian is reachable
nanobot test-vault --path /Users/your-username/Documents/MyVault

# Should return: ✅ Vault accessible
```

**Step 6: Verify with a Simple Command**
```
User: "@BotName read the file Getting-Started.md"
```

If successful, the bot returns the file contents. If it fails:
- Check Obsidian is running
- Confirm REST API plugin is enabled
- Verify vault path and token in config
- Check firewall isn't blocking localhost:27123

#### Storage & Permissions

**Vault Location Best Practices:**
- **Local machine:** SSD for speed (e.g., `~/Documents/MyVault`)
- **NOT in OneDrive/Dropbox initially** — File-locking can conflict with REST API
- **Network share:** Only if using NFS / SMB (not recommended for reliability)

**File Permissions:**
```bash
# Ensure nanobot process can read/write
chmod 755 ~/Documents/MyVault
chmod 644 ~/Documents/MyVault/*.md
```

**Usage Examples:**
- `"Read the Q1 roadmap from Strategy.md"`
- `"Write a research summary to 08-Research/AI-Safety/findings.md"`
- `"Query all notes tagged #decision"`

#### Common Mistakes & Solutions

### ❌ Mistake 1: REST API Plugin Not Installed
**Problem:** `Vault not reachable` error when nanobot tries to access Obsidian  
**Why:** REST API plugin isn't installed or enabled  
**Fix:**
1. Open Obsidian → Settings → Community Plugins
2. Search "Local REST API" by Coremute
3. Click **Install** then **Enable**
4. Restart Obsidian
5. Test: `nanobot test-vault`

### ❌ Mistake 2: Wrong Vault Path in Config
**Problem:** Obsidian vault path error, nanobot can't find files  
**Why:** Path is relative or incorrect; Windows vs Linux forward/backslash difference  
**Fix:**
- Use **absolute path**: `/Users/username/Documents/MyVault` (not `~/MyVault`)
- On Windows: Use forward slashes `/` or escaped backslashes `\\`
- Test path exists: `ls /Users/username/Documents/MyVault`

### ❌ Mistake 3: Using Wrong Port Number
**Problem:** Connection refused when nanobot tries to reach REST API  
**Why:** Default port changed, or using old hardcoded port  
**Fix:**
1. Open Obsidian REST API ribbon icon (left sidebar)
2. Check "Listening on: http://localhost:XXXX"
3. Copy exact port number
4. Update nanobot config with correct port
5. Restart

### ❌ Mistake 4: REST API Token Missing or Revoked
**Problem:** Unauthorized when accessing vault  
**Why:** Token expired, not copied correctly, or Obsidian restarted (new token)  
**Fix:**
1. Click REST API ribbon icon
2. Copy fresh token
3. Update nanobot config
4. Restart nanobot

### ❌ Mistake 5: Obsidian Locked/File Sync Conflict
**Problem:** Write operations fail, "file locked" error  
**Why:** OneDrive/Dropbox/iCloud is syncing; creates locks  
**Fix:**
1. Move vault out of cloud sync folder temporarily
2. Or: Disable cloud sync while nanobot is writing
3. Or: Use `.gitignore` to exclude `.obsidian` folder from sync
4. Test write: `nanobot write-test`

---

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
**Automatic** — No user interaction needed; agent manages conversation memory transparently.

#### Common Mistakes & Solutions

### ❌ Mistake 1: Memory Consolidation Not Running
**Problem:** Conversations get slow after 100+ messages  
**Why:** `consolidationInterval` set too high or memory not enabled  
**Fix:**
1. Check config: `consolidationInterval: 10` (consolidates every 10 messages)
2. If set to 0, consolidation is **disabled**; change to 10-20
3. Restart nanobot
4. Test: Watch for "Consolidating memory..." messages in logs

### ❌ Mistake 2: Context Window Always Hits Max Too Fast
**Problem:** Running out of tokens mid-conversation  
**Why:** `maxSessionTokens` set too low  
**Fix:**
- Increase from 32000 to 50000-80000 (if your LLM supports it)
- Check your LLM token limits: OpenRouter, Claude 3.5 Sonnet = 200k tokens
- Set `maxSessionTokens` to 80% of your model's limit

### ❌ Mistake 3: Memory Not Carrying Over Between Sessions
**Problem:** Each new conversation starts fresh, no history  
**Why:** Session persistence not enabled in config  
**Fix:**
1. Add to config: `"persistentSessions": true`
2. Restart nanobot
3. Sessions now auto-save to `~/.nanobot/sessions/`

---

### 3. cron
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
**Usage Examples:**
- `"Schedule a health check every hour"`
- `"Run daily journal summarizer at 23:50 every day"`
- `"Backup workspace every Sunday at 2 AM"`

#### Common Mistakes & Solutions

### ❌ Mistake 1: Cron Job Uses Wrong Timezone
**Problem:** Scheduled tasks run at unexpected times  
**Why:** Timezone mismatch (UTC vs local time)  
**Fix:**
1. Check config: `"timezone": "UTC"` (or your actual timezone)
2. If in US Eastern: Change to `"America/New_York"`
3. See full list: `nanobot config cron --list-timezones`
4. After change, test: `"Schedule test job for 30 seconds from now"`

### ❌ Mistake 2: Scheduled Task Fails Silently
**Problem:** Job doesn't run, no error messages  
**Why:** Task command syntax wrong, or job timeout too short  
**Fix:**
1. Check job definition: Simple commands work better (no complex pipes)
2. Increase `jobTimeout` from 1800 to 3600 seconds if jobs take long
3. Check logs: `nanobot logs --filter="cron"`
4. Test task manually first before scheduling

### ❌ Mistake 3: Too Many Jobs Scheduled, System Overload
**Problem:** System slow, jobs queuing up, some skipped  
**Why:** `maxConcurrentJobs` set too high, or scheduled jobs overlap  
**Fix:**
1. Reduce `maxConcurrentJobs` from 5 to 2-3
2. Space out job schedules (don't run 5 jobs at 23:50)
3. Example good spacing: 23:50, 00:10, 00:30 (not all together)
4. Check queue: `nanobot cron --status`

---

### 4. summarize
**Purpose:** Condense long content while preserving key information  
**Why Essential:** Reduces token burn, improves clarity for reports and digests  
**Install:** Built-in  
**Config:**
```json
{
  "summarize": {
    "targetLength": "concise",  // Options: "terse", "concise", "detailed"
    "includeMetadata": true,
    "preserveCitations": true
  }
}
```
**Usage Examples:**
- `"Summarize this 50-page PDF to 1 page"`
- `"Create a 3-sentence summary of today's Discord activity"`
- `"Condense research findings into key insights + citations"`

#### Common Mistakes & Solutions

### ❌ Mistake 1: Summary Is Too Short, Missing Key Details
**Problem:** Summary lost important context  
**Why:** `targetLength: "terse"` is too aggressive  
**Fix:**
1. Change to `"targetLength": "concise"` for better detail retention
2. Or specify exact length: `"Summarize to exactly 5 sentences"`
3. Always use `"preserveCitations": true` to keep important references

### ❌ Mistake 2: Summary Is Incoherent or Technical Jargon-Heavy
**Problem:** Summary doesn't explain concepts clearly  
**Why:** No simplification instruction given  
**Fix:**
1. Add instruction: `"Summarize in simple language for non-technical users"`
2. Or: `"Explain this like you're talking to a 10-year-old"`
3. Test with different personas to find clearest explanation

### ❌ Mistake 3: Losing Source Links and Citations
**Problem:** Summary has no references back to original  
**Why:** `preserveCitations: false` (or citations not in source)  
**Fix:**
1. Enable: `"preserveCitations": true`
2. Request: `"Summarize with at least 3 citations and page numbers"`
3. For Obsidian: `"Summarize and add wiki links to referenced notes"`

---

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
**Usage Examples:**
- `"Search our backend repo for TypeScript errors"`
- `"Create a PR to update dependencies in main branch"`
- `"Trigger the 'deploy' workflow"`

#### Common Mistakes & Solutions

### ❌ Mistake 1: GitHub Token Has Wrong Permissions
**Problem:** `Unauthorized: insufficient permissions` when trying to create PRs  
**Why:** Token created with read-only scope, not write  
**Fix:**
1. Go to GitHub → Settings → Developer settings → Personal access tokens
2. Create new token with these scopes:
   - ✅ `repo` (full repository access)
   - ✅ `workflow` (to trigger Actions)
   - ✅ `read:org` (to access org repos)
3. Copy exact token (don't add extra spaces)
4. Update config and restart nanobot

### ❌ Mistake 2: Workflows Not Triggering, or Wrong Workflow Selected
**Problem:** "Workflow not found" or workflow runs but nothing happens  
**Why:** Workflow filename wrong, or doesn't have `workflow_dispatch` trigger  
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
**Problem:** Token leaked to GitHub history  
**Why:** Pasted token in config file and committed it  
**Fix:**
1. **Immediately:** Regenerate token in GitHub settings (old one is compromised)
2. Remove old token from commit history: `git filter-branch --force --index-filter "git rm --cached --ignore-unmatch .env" -- --all`
3. Create `.env` file and add to `.gitignore`:
   ```bash
   echo "GITHUB_TOKEN=github_pat_..." >> .env
   echo ".env" >> .gitignore
   ```
4. Update nanobot to read from `.env` instead of config file

---

## 2. Installation Checklist

- [ ] **obsidian** - Configured with vault path and REST endpoint
- [ ] **memory** - Session consolidation enabled (default)
- [ ] **cron** - Installed and tested with one schedule
- [ ] **summarize** - Tested with a long document
- [ ] **github** - Token configured and tested with one search

## 3. Daily Journal Example (Using cron + obsidian + summarize)

Combine three essential skills to automate daily operational journaling:

**Step 1: Set up Archivist personality**

Add to `config.json` or `~/.nanobot/agents/primary/SOUL.md`:

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

**Step 2: Schedule the routine**

```
User: "@BotName schedule a task: Run the Archivist routine every day at 23:50"
```

**Step 3: Test manually**

```
User: "@BotName force-run Archivist routine for today"
```

**Expected:** Bot replies with success, entry appears in Obsidian vault.

---

## 4. Security Best Practices

**Tool Allowlisting:**
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

**Secret Management:**
- Never commit `.env` or config files with tokens
- Store API keys in environment variables: `export GITHUB_TOKEN=...`
- Use allowlists to restrict who can invoke sensitive skills

---

## 5. Complete Skill Catalog

This document covers the 5 essential skills. Four more are available:
- **weather** - Weather data retrieval
- **clawhub** - Skill marketplace integration
- **skill-creator** - Auto-generate skills from natural language
- **tmux** - Terminal multiplexer control

**See [Tools & Skills Reference](Tools-and-Skills-Reference.md) for:**
- All 14 built-in tools (web_search, file_*, shell, mcp, subagent_spawn, etc.)
- All 9 pre-built skills with full examples
- Cost/performance matrix
- Creating custom tools and skills
- Tool allowlisting patterns

---

## 6. Troubleshooting

| Issue | Fix |
|---|---|
| Skill not found | Confirm installed: `nanobot skill list` |
| Obsidian write fails | Verify vault path and REST endpoint in config |
| Cron task not running | Check time format (24-hour) and time zone |
| GitHub auth fails | Verify token format and repo permissions |

---

## 7. Revision History

| Date | Version | Change |
|---|---|---|
| 2026-02-25 | 2.0.0 | Major simplification: 5 essential skills only; removed outdated references; added cross-references to comprehensive Tools & Skills Reference; updated config examples and daily journal walkthrough |
| Previous | 1.x | Outdated skill inventory and setup instructions |
