# Essential AOS Skills

This document lists the 5 core skills (pre-built bundles) recommended for every Nanobot Agentic Operating System deployment. For complete reference including all 9 pre-built skills, 14 tools, and custom skill creation, see [Tools & Skills Reference](Tools-and-Skills-Reference.md).

> **Plain-English Note:** A skill is a pre-built bundle of tools configured for a specific business workflow. Install these five first, then add others as needed.

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

To enable nanobot ↔ Obsidian communication, set up the Local REST API plugin:

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

---

### 3. cron
**Purpose:** Schedule tasks to run on recurring schedules  
**Why Essential:** Enables proactive, always-on operations (daily journal, health checks, backups)  
**Install:** `nanobot skill add cron`  
**Examples:**
- `"Schedule a health check every hour"`
- `"Run daily journal summarizer at 23:50 every day"`
- `"Backup workspace every Sunday at 2 AM"`

---

### 4. summarize
**Purpose:** Condense long content while preserving key information  
**Why Essential:** Reduces token burn, improves clarity for reports and digests  
**Install:** Built-in  
**Usage Examples:**
- `"Summarize this 50-page PDF to 1 page"`
- `"Create a 3-sentence summary of today's Discord activity"`
- `"Condense research findings into key insights + citations"`

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
