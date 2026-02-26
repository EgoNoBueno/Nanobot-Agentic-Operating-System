# Nanobot Build Procedure

Build and configure nanobot for deployment. For simple **quick-start in 5 minutes**, see [Nanobot Quick Install & Setup](Nanobot-Quick-Install-Setup.md).

## Document Control
- **Owner:**
- **Version:** 2.0.0
- **Last Updated:** 2026-02-25
- **Status:** Active

## Overview

Two deployment scenarios:

1. **Simple Build** (5 min) - Local pip install + cloud LLM (easiest)
2. **Advanced Build** (30 min) - VPS (Virtual Private Server) infrastructure + local Ollama inference

This document covers nanobot build/config steps. For VPS infrastructure (Tailscale, firewall, SSH keys), see your VPS provider's setup guide.

---

## How to Copy & Paste Commands in Linux

When you see a bash code block below, here are the best ways to copy and paste into your terminal:

**Method 1: Middle-click paste (Fastest - Linux/Mac)**
1. Highlight the command text with your mouse
2. **Middle-click** (or scroll wheel click) in the terminal
3. Command pastes immediately—no extra steps!

**Method 2: Keyboard shortcuts (Works everywhere)**
1. Highlight the command text
2. Press `Ctrl+Shift+C` to copy (note: Shift, not just Ctrl)
3. Click in terminal and press `Ctrl+Shift+V` to paste

**Method 3: Right-click menu (Your current method)**
1. Highlight the command text
2. Copy with `Ctrl+C` or right-click → Copy
3. Right-click in terminal and select Paste

**Recommendation:** Try middle-click paste if your terminal supports it—it's the fastest (one action instead of three). If that doesn't work, use `Ctrl+Shift+V` keyboard shortcuts.

---

## Path A: Simple Build (Local + Cloud LLM)

**For:** Personal/team use with OpenRouter, Anthropic, OpenAI, or compatible cloud provider.  
**Prerequisites:** Python ≥3.11, pip, internet connection, API key from your chosen LLM provider  
**Time:** ⏱️ 5 minutes  

### Before You Start
- ☐ You have Python 3.11 or higher installed (`python --version`)
- ☐ You have pip installed (`pip --version`)
- ☐ Internet connection working (test: `ping google.com` on Windows, or `ping -c 4 google.com` on macOS/Linux)
- ☐ You have an API key from OpenRouter, Anthropic, OpenAI, or similar
- ☐ You know which chat platform you'll use (Discord, Slack, etc.)

### Step 1: Install nanobot
⏱️ ~1 minute

```bash
# pip (Python package installer) downloads and installs nanobot
pip install nanobot-ai
```

Verify installation worked:
```bash
nanobot --version
# Should show: nanobot v0.1.4 or higher
```

### Step 2: Initialize Config
⏱️ ~2 minutes

```bash
nanobot onboard
```

Interactive wizard will ask several questions. (See [Nanobot Quick Install & Setup](Nanobot-Quick-Install-Setup.md) for details on each question.)

Config saved to:
- **Linux/Mac:** `~/.nanobot/config.json`
- **Windows:** `%APPDATA%\nanobot\config.json`

### Step 3: Test Installation
⏱️ ~1 minute

```bash
# nanobot test starts an interactive test session (type commands, see responses)
# This ensures everything is working before you deploy it
nanobot test
```

Should start interactive test mode:
```
Nanobot Test Mode (type 'exit' to quit)
Loading config: ✓
Starting LLM (Large Language Model) provider: openrouter ✓
Connected to: Discord ✓

nanobot> hello
[LLM response]

nanobot> web_search: python async patterns
[web search results]

nanobot> exit
```

### Validation Gate A - Simple Build Complete ✅
- ✅ `nanobot version` returns v0.1.4+
- ✅ `nanobot onboard` completes without errors
- ✅ Config file exists and is valid JSON
- ✅ `nanobot test` returns responses from LLM
- ✅ Web search, tool execution work in test mode

If any item shows ❌, see [Common Mistakes and Solutions](#common-mistakes-and-solutions) below.

### Deployment: Simple Build

Once validated, start the gateway:

```bash
nanobot gateway
```

Nanobot will now listen to Discord, Slack, or other configured channels and respond to messages.

**To run in background (Linux/Mac):**
```bash
# The & symbol means "run this command in the background"
# This lets you keep using the terminal while nanobot runs
nanobot gateway &
# Or use systemd (see Advanced Build)
```

**To run in background (Windows):**
Use Task Scheduler or Windows Service installer:
```bash
nanobot install-service
# Then: Net Start Nanobot
```

---

## Path B: Advanced Build (VPS + Local Ollama)

**For:** Self-hosted inference, privacy-critical workflows, cost optimization with local models.  
**Architecture:** VPS (Virtual Private Server) (gateway + routing) ↔️ Local machine (Ollama inference) via Tailscale-encrypted tunnel  
**Time:** ~30 minutes for build; ~15 minutes for startup  
**Prerequisites:** VPS access, local machine with Ollama, Tailscale account

### Prerequisites Checklist
- [ ] VPS (Virtual Private Server) deployed (Ubuntu 22.04+ recommended)
- [ ] SSH (Secure Shell) access to VPS
- [ ] Local machine with Ollama installed (Windows/Mac/Linux)
- [ ] Tailscale account (free tier OK)
- [ ] Discord/Slack token ready
- [ ] API keys for fallback provider (OpenRouter recommended)

---

## Phase A: VPS Preparation (15 min)

### Step 1: Connect to VPS
⏱️ ~1 minute

```bash
# ssh (Secure Shell) is how you remotely access a server
# Replace your-vps-user (e.g., 'ubuntu') and your-vps-ip (e.g., '192.168.1.100')
ssh your-vps-user@your-vps-ip
# Or configure SSH key for passwordless access
```

⚠️ **Linux Password Entry Note:** If prompted for a password, type it carefully. **On Linux/Mac, the terminal will NOT display any characters (no dots or asterisks) as you type your password.** This is normal and secure—just type and press Enter when done. The password is being entered; you just can't see it.

### Step 2: System Updates
⏱️ ~5 minutes

```bash
# apt is the package manager for Ubuntu (like 'store' for software)
# update checks for new versions of everything
# upgrade installs those new versions
# -y means "yes automatically, don't ask"
sudo apt update && sudo apt upgrade -y

# Install programs nanobot needs (Python, build tools, etc.)
# && means "if that worked, then run this next command"
sudo apt install -y python3.11 python3.11-venv python3-pip build-essential
```

⚠️ **Linux Password Entry Note:** You may be prompted for your sudo password. **The terminal will NOT display any characters (no dots or asterisks) as you type.** This is normal—just type your password and press Enter. The system is listening even though you can't see what you're typing.

### Step 3: Install Tailscale
⏱️ ~3 minutes

```bash
# curl -fsSL downloads and runs the Tailscale installer script
# | sh pipes (sends) the output to shell to execute it
curl -fsSL https://tailscale.com/install.sh | sh

# Start Tailscale (secure network tunnel)
# This creates an encrypted connection between your machines
sudo tailscale up
# Will print auth URL; open in browser; authenticate
```

After auth, note your VPS Tailscale IP (e.g., `100.87.123.45`). Print it:
```bash
# Show your Tailscale IP address (the address other machines will use to find you)
tailscale ip -4
```

### Step 4: Create nanobot User (Optional but Recommended)
⏱️ ~1 minute

```bash
# useradd -m creates a new user account named 'nanobot'
# -s /bin/bash sets bash as the default shell
sudo useradd -m -s /bin/bash nanobot

# su - nanobot "switches user" to nanobot (logs in as that user)
sudo su - nanobot
# Now running as nanobot user for isolation
```

⚠️ **Linux Password Entry Note:** If prompted for a password, **the terminal will NOT display any characters as you type.** This is normal—just type and press Enter. Do not assume the keyboard is broken if you don't see dots or asterisks.

### Validation: VPS Ready ✅
- ✅ SSH access works
- ✅ Python 3.11+ installed
- ✅ Tailscale running and connected
- ✅ Nanobot user created (optional)

---

## Phase B: Local Ollama Configuration (10 min)

**On your local machine** (where Ollama will run):

### Step 1: Install Ollama

Download from: https://ollama.ai

### Step 2: Set Ollama Listen Address

Allow remote connections via Tailscale. Edit Ollama systemd unit or set environment:

**Linux/Mac:**
```bash
export OLLAMA_HOST=0.0.0.0:11434
ollama serve
```

**Windows:**
```cmd
setx OLLAMA_HOST 0.0.0.0:11434
# Restart Ollama from system tray
```

### Step 3: Connect Tailscale Locally

```bash
tailscale up
# Note your local Tailscale IP (e.g., 100.123.45.67)
tailscale ip -4
```

### Step 4: Install Model

```bash
ollama pull qwen2:latest
# Or: ollama pull llama2, mistral, neural-chat, etc.
```

### Step 5: Test Local Ollama

```bash
curl http://localhost:11434/api/tags
# Should return JSON with model list
```

### Validation: Ollama Ready
- ✓ Ollama running
- ✓ Tailscale connected locally
- ✓ Model downloaded
- ✓ Local curl responds with model list

---

## Phase C: Tailscale Tunnel Validation (5 min)

**From VPS**, test reaching local Ollama through Tailscale:

```bash
# SSH into VPS
ssh your-vps-user@your-vps-ip

# Test connectivity (replace 100.123.45.67 with your local Tailscale IP)
curl http://100.123.45.67:11434/api/tags
# Should return JSON model list
```

**If failed:**
- Verify Ollama listening on `0.0.0.0:11434` (not just `localhost`)
- Check local firewall allows port 11434
- Verify both machines on same Tailscale network
- Restart Tailscale on both sides

### Validation: Tunnel Ready
- ✓ VPS can curl to local Ollama endpoint
- ✓ Response includes model names

---

## Phase D: Nanobot Installation (VPS)

**On VPS:**

### Step 1: Install nanobot

```bash
pip install nanobot-ai
nanobot version
# Should print: nanobot v0.1.4+
```

### Step 2: Create Config File

```bash
# Create config directory
mkdir -p ~/.nanobot
nano ~/.nanobot/config.json
```

Minimal config for ollama + discord:
```json
{
  "llm": {
    "provider": "ollama",
    "model": "qwen2:latest",
    "endpoint": "http://100.123.45.67:11434",
    "fallback_provider": "openrouter",
    "fallback_key": "sk-or-xxxxx"
  },
  "channels": {
    "discord": {
      "enabled": true,
      "token": "your-discord-bot-token"
    }
  },
  "skills": {
    "obsidian": {
      "enabled": true,
      "vault_path": "/path/to/obsidian/vault"
    }
  }
}
```

**Key values:**
- `endpoint`: Use local Tailscale IP (e.g., `http://100.123.45.67:11434`)
- `discord.token`: Get from Discord Developer Portal
- `vault_path`: Absolute path to Obsidian vault (if applicable)
- `fallback_key`: OpenRouter API key for fallback when Ollama down

### Step 3: Test Configuration

```bash
nanobot health
# Output should show: ollama ✓ connected, discord ✓ ready
```

### Validation: Nanobot Config Valid
- ✓ Config file parses (valid JSON)
- ✓ `nanobot health` shows all components connected
- ✓ Ollama endpoint reachable from VPS

---

## Phase E: Systemd Service (Optional but Recommended)

**To auto-start nanobot on VPS reboot:**

### Step 1: Create Service File

```bash
sudo nano /etc/systemd/system/nanobot.service
```

Paste:
```ini
[Unit]
Description=Nanobot Agentic Operating System
After=network.target

[Service]
Type=simple
User=nanobot
WorkingDirectory=/home/nanobot
ExecStart=/usr/bin/python3 -m nanobot.gateway
Restart=on-failure
RestartSec=10
Environment="PATH=/home/nanobot/.local/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin"

[Install]
WantedBy=multi-user.target
```

### Step 2: Enable & Start

```bash
sudo systemctl daemon-reload
sudo systemctl enable nanobot
sudo systemctl start nanobot
sudo systemctl status nanobot
# Should show: active (running)
```

### Step 3: View Logs

```bash
sudo journalctl -u nanobot -f
# Ctrl+C to exit
```

---

## Phase F: Startup Validation (Full End-to-End Test)

**Follow [AOS Startup: Advanced Build](AOS-Startup-Advanced-Build.md) for detailed startup testing.**

Quick version:
1. Local machine: Start Ollama (`ollama serve`)
2. VPS: Start nanobot (`nanobot gateway` or `systemctl start nanobot`)
3. Discord: Send test message `@BotName hello`
4. Expected: Response from local Ollama model (via Tailscale tunnel)

---

## Cost & Performance Reference

| Component | Resource Cost | Performance | Notes |
|---|---|---|---|
| **Local Inference** (Ollama) | 1x GPU/CPU | Latency 2-10s | Fine for async workflows; not real-time chat |
| **Cloud LLM** (OpenRouter) | $0.001-0.01 per msg | Latency <1s | Fast; pay per request; good for hybrid |
| **VPS** (minimal) | $5-20/month | Constant | Tailscale ingress is free; only covers VPS uptime |
| **Total (Advanced)** | VPS + Cloud fallback | Best UX | Ollama primary; cloud as fallback if local down |

---

## Post-Build Verification Checklist

After build completion:

- [ ] `nanobot version` returns v0.1.4+
- [ ] Config file exists at expected location
- [ ] `nanobot health` all components green
- [ ] Test message in Discord/Slack gets response
- [ ] Tool execution works (web_search, file operations)
- [ ] Obsidian write-back functional (if enabled)
- [ ] Systemd service auto-restarts on crash (advanced only)
- [ ] Cost tracking working (tokens logged, cost estimated)

---

## Troubleshooting Build Issues

| Problem | Symptom | Solution |
|---|---|---|
| Python version mismatch | `Error: nanobot-ai requires Python ≥3.11` | `python3 --version`; upgrade Python; use `python3.11` explicitly |
| Config parsing error | `Config validation failed: ...` | Use JSON validator (jsonlint); check quote marks and commas |
| Ollama unreachable | `Ollama connection refused` | Verify Ollama running; check endpoint URL; verify Tailscale tunnel |
| Discord token invalid | `Discord authentication failed` | Re-generate token in Developer Portal; verify it's bot token, not user token |
| Dependency conflict | `pip install nanobot-ai` fails | Clean environment: `pip install --upgrade pip setuptools wheel` then retry |

---

## Common Mistakes and Solutions

### ❌ Mistake 1: Using User Token Instead of Bot Token (Discord)
**Problem:** Discord authorization fails, bot won't authenticate  
**Why it happens:** Easy to copy the wrong token from Developer Portal  
**Fix:**
1. Go to https://discord.com/developers/applications
2. Select your app
3. Click **Bot** tab (not General or OAuth2)
4. Copy the **Token** there
5. ⚠️ Make sure it says "Bot" not "User"
6. Update config and restart

### ❌ Mistake 2: API Key Has Expired or Wrong Format
**Problem:** `Provider unreachable` or `Invalid API key` error  
**Why it happens:** Keys can expire, or pasted with extra spaces  
**Fix:**
1. Check your API key hasn't expired in provider dashboard
2. Copy fresh key (watch for leading/trailing spaces)
3. Test manually: `curl -H "Authorization: Bearer YOUR_KEY" https://api.openrouter.ai/api/v1/auth/key`
4. ✅ If it returns 200, key is valid

### ❌ Mistake 3: Python Version Too Old
**Problem:** `ModuleNotFoundError` or installation fails  
**Why it happens:** Nanobot requires Python 3.11+, older versions lack required features  
**Fix:**
```bash
# Check your Python version
python --version
# If < 3.11, install newer version
```

### ❌ Mistake 4: Firewall Blocking Outbound HTTPS (VPS)
**Problem:** `Connection timeout` when reaching cloud providers  
**Why it happens:** VPS firewalls sometimes block outbound port 443 by default  
**Fix:**
```bash
# Check firewall status
sudo ufw status
# Allow outbound HTTPS (port 443)
sudo ufw allow out 443
```

### ❌ Mistake 5: Config.json Syntax Invalid (JSON)
**Problem:** `Config validation failed` on startup  
**Why it happens:** Missing comma, unmatched braces, or quotes  
**Fix:**
```bash
# Validate JSON syntax
python -m json.tool ~/.nanobot/config.json
# If it shows error, find the line number and fix it
```

---

## Build Paths Decision Tree

```
Are you okay with API costs (OpenRouter/Anthropic/OpenAI)?
├─ YES → Use Simple Build (Path A)
│        5 minutes to deployment
│        Cost: $0.01-0.10 per conversation
│        Easiest for teams
│
└─ NO → Are you willing to manage VPS + Ollama?
         ├─ YES → Use Advanced Build (Path B)
         │        30 minutes setup
         │        Cost: $5-20/month VPS + cloud fallback
         │        Best for privacy/control
         │
         └─ NO → Use Simple Build anyway
                  OpenRouter is cheapest cloud option
                  Fallback to free Ollama if costs high
```

---

## Revision History

| Date | Version | Changes |
|---|---|---|
| 2026-02-25 | 2.0.0 | Complete rewrite: Split simple (Path A: pip install) vs. advanced (Path B: VPS+Ollama); removed npm/pnpm references; added systemd examples; added Tailscale tunnel validation; reduced from 796 to ~400 lines |
| Previous | 1.1.0 | Monolithic VPS-focused build procedure |
