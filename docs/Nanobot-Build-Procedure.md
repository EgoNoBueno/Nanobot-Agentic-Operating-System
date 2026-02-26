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
2. **Advanced Build** (30 min) - VPS infrastructure + local Ollama inference

This document covers nanobot build/config steps. For VPS infrastructure (Tailscale, firewall, SSH keys), see your VPS provider's setup guide.

---

## Path A: Simple Build (Local + Cloud LLM)

**For:** Personal/team use with OpenRouter, Anthropic, OpenAI, or compatible cloud provider.  
**Prerequisites:** Python ≥3.11, pip, internet connection  
**Time:** 5 minutes

### Step 1: Install nanobot

```bash
pip install nanobot-ai
```

Verifies:
```bash
nanobot version
# Output: nanobot v0.1.4+ (or higher)

nanobot health
# Output: Checking installation...
```

### Step 2: Initialize Config

```bash
nanobot init
```

Interactive prompt will ask:
- LLM provider? → Choose cloud provider (openrouter, anthropic, openai, qwen, deepseek)
- API key? → Paste from provider dashboard
- Discord token? → (or Slack, Telegram—choose your channel)
- Default channel? → e.g., #general or DM
- Obsidian vault path? → (optional; press Enter to skip)

Config saved to:
- **Linux/Mac:** `~/.nanobot/config.json`
- **Windows:** `%APPDATA%\nanobot\config.json`

### Step 3: Test Installation

```bash
nanobot test
```

Should start interactive test mode:
```
Nanobot Test Mode (type 'exit' to quit)
Loading config: ✓
Starting LLM provider: openrouter ✓
Connected to: Discord ✓

nanobot> hello
[LLM response]

nanobot> web_search: python async patterns
[web search results]

nanobot> exit
```

### Validation Gate A
- ✓ `nanobot version` returns v0.1.4+
- ✓ `nanobot init` completes without errors
- ✓ Config file exists and is valid JSON
- ✓ `nanobot test` returns responses from LLM
- ✓ Web search, tool execution work in test mode

### Deployment: Simple Build

Once validated, start the gateway:

```bash
nanobot gateway
```

Nanobot will now listen to Discord, Slack, or other configured channels and respond to messages.

**To run in background (Linux/Mac):**
```bash
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
**Architecture:** VPS (gateway + routing) ↔️ Local machine (Ollama inference) via Tailscale-encrypted tunnel  
**Time:** ~30 minutes for build; ~15 minutes for startup  
**Prerequisites:** VPS access, local machine with Ollama, Tailscale account

### Prerequisites Checklist
- [ ] VPS deployed (Ubuntu 22.04+ recommended)
- [ ] SSH access to VPS
- [ ] Local machine with Ollama installed (Windows/Mac/Linux)
- [ ] Tailscale account (free tier OK)
- [ ] Discord/Slack token ready
- [ ] API keys for fallback provider (OpenRouter recommended)

---

## Phase A: VPS Preparation (15 min)

### Step 1: Connect to VPS

```bash
ssh your-vps-user@your-vps-ip
# Or configure SSH key for passwordless access
```

### Step 2: System Updates

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y python3.11 python3.11-venv python3-pip build-essential
```

### Step 3: Install Tailscale

```bash
curl -fsSL https://tailscale.com/install.sh | sh
sudo tailscale up
# Will print auth URL; open in browser; authenticate
```

After auth, note your VPS Tailscale IP (e.g., `100.87.123.45`). Print it:
```bash
tailscale ip -4
```

### Step 4: Create nanobot User (Optional but Recommended)

```bash
sudo useradd -m -s /bin/bash nanobot
sudo su - nanobot
# Now running as nanobot user for isolation
```

### Validation: VPS Ready
- ✓ SSH access works
- ✓ Python 3.11+ installed
- ✓ Tailscale running and connected
- ✓ Nanobot user created (optional)

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

**Follow [AOS Startup Procedure](AOS-Startup-Procedure.md) (Path B section) for detailed startup testing.**

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
