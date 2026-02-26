# AOS Startup Procedure

Bring nanobot from fully powered down to fully operational. Choose your path based on your deployment scenario.

## Document Control
- **Owner:**
- **Version:** 2.0.0
- **Last Updated:** 2026-02-25
- **Status:** Active

## 1. Overview

Two startup scenarios:
- **Simple Startup** (10 min) - Local nanobot + cloud LLM (Large Language Model) API
- **Advanced Startup** (15 min) - VPS (Virtual Private Server) nanobot + local Ollama inference

Both assume initial setup is complete (config files, secrets stored, channels configured).

## 2. Assumptions

- Installation and hardening are complete
- Config files exist (`~/.nanobot/config.json` or `.env`)
- All required secrets (API keys, tokens) are stored securely
- Channels are already configured (Discord, Slack, etc.)
- Obsidian vault exists (if using obsidian skill)

## 3. Startup Checklist (High-Level)

Before you start, make sure you have:
- [ ] Internet connection (required for cloud LLM)
- [ ] Required services running (Ollama, Tailscale, VPS, etc. per your architecture)
- [ ] LLM (language model) provider accessibility confirmed
- [ ] Chat bot status verified (Discord/Slack/etc.)
- [ ] One quick test command ready to verify end-to-end flow
- [ ] Obsidian integration confirmed (if you're using it)

---

## Path A: Simple Startup (Local + Cloud API)

**For:** Using local nanobot with OpenRouter, Anthropic, OpenAI, or other cloud LLM provider.  
**Time:** ~10 minutes

### Step 1: Verify Connectivity
```bash
ping 8.8.8.8
# Should respond with pings (internet is up)
```

### Step 2: Start nanobot
```bash
nanobot gateway
```

Should show:
```
Starting Nanobot Gateway v0.1.4+
LLM Provider: openrouter ✓ Connected
Discord: Connected ✓
Slack: Connected ✓
Ready for messages
```

**If provider fails:**
- Check `.env` or `config.json` for correct API key format
- Verify key hasn't expired in your provider dashboard
- If key is fresh, wait 1 minute and retry (API sync delay)

### Step 3: Discord/Slack Test

Send a test message in any channel where bot is installed:
```
@BotName hello
```

Bot should respond within 5 seconds.

**If bot doesn't respond:**
- Verify bot is in the channel
- Check bot has `Send Messages` and `Read Message History` permissions
- Verify token in config matches what Discord/Slack shows
- Check logs: `nanobot logs --last 50` to see errors

### Step 4: Obsidian Write Test (Optional)

In Discord:
```
@BotName write to Obsidian: "Startup test at {{timestamp}}" to 00-System/startup-log.md
```

Check Obsidian vault. File should appear in `00-System/startup-log.md`.

### Validation Gate A
- ✓ nanobot gateway starts without errors
- ✓ LLM provider connected
- ✓ Bot responds to test message in Discord/Slack
- ✓ Obsidian write succeeds (if enabled)

---

## Path B: Advanced Startup (VPS + Local Ollama)

**For:** Using VPS-hosted nanobot with local Ollama inference.  
**Architecture:** VPS (gateway) ↔️ Local Windows/Mac (Ollama) via Tailscale  
**Time:** ~15 minutes

### Step 1: Start Local Inference Node

**On Windows/Mac** (where Ollama is installed):

```bash
ollama serve
```

Keep this running in a terminal. Ollama should listen on `localhost:11434`.

### Step 2: Verify Ollama is Running

```bash
curl http://localhost:11434/api/tags
# Should return JSON list of models
```

### Step 3: Connect Tailscale (Both Machines)

**On Local Machine:**
```bash
tailscale up
# Will print: Connected to tailnet as [device-name]
# Note your local Tailscale IP (e.g., 100.123.45.67)
```

**On VPS:**
```bash
tailscale up
# Note VPS Tailscale IP (e.g., 100.87.123.45)
```

Enable MagicDNS in Tailscale admin panel (recommended): https://login.tailscale.com/admin/dns

### Step 4: Test Tailscale Path (From VPS)

```bash
curl http://100.123.45.67:11434/api/tags
# Should return model list from local Ollama
```

If fails:
- Check local firewall allows port 11434 (scope to Tailscale interface only)
- Verify Ollama is running on local machine
- Restart Tailscale on both sides and re-authenticate

### Step 5: Start Nanobot on VPS

```bash
ssh your-vps-user@your-vps-ip
# Or use tailscale: ssh your-vps-name

# Start nanobot
nanobot gateway
```

Should show:
```
Starting Nanobot Gateway v0.1.4+
LLM Provider: ollama ✓ Connected to http://100.123.45.67:11434
Discord: Connected ✓
Ready for messages
```

### Step 6: Test End-to-End

From Discord/Slack, send:
```
@BotName What is 2+2?
```

Expected flow:
1. Discord receives message
2. Nanobot (VPS) forwards to Ollama (Local)
3. Ollama generates response
4. Response posted back to Discord

**If no response:**
- Check VPS logs: `journalctl -u nanobot -n 50` (if using systemd service)
- Verify Tailscale path again: `curl http://100.123.45.67:11434` from VPS
- Check Ollama logs on local machine for errors
- Verify nanobot config has correct Ollama endpoint

### Validation Gate B
- ✓ Ollama running locally and responding to curl
- ✓ Tailscale connected on both machines
- ✓ VPS can reach local Ollama via Tailscale IP
- ✓ Nanobot gateway starts on VPS
- ✓ End-to-end message → Ollama → response works
- ✓ Discord bot online and responding

---

## Common Issues & Fixes

| Symptom | Likely Cause | Check | Fix |
|---|---|---|---|
| "Provider disconnected" | API key invalid/expired | Verify in `.env` and provider dashboard | Rotate API key, update config, restart nanobot |
| Bot offline in Discord | Token wrong or permissions missing | Check developer portal settings | Verify token and Message Content Intent enabled |
| Ollama unreachable | Network/firewall issue | `curl http://Local-IP:11434` from VPS | Allow port 11434 on local firewall; check Tailscale route |
| No response to test message | Bot offline or wrong config | Check logs and provider status | See logs; verify config; restart gateway |

---

## Post-Startup Validation

### Quick Health Check
```bash
nanobot health
```

Expected output:
```
✓ Gateway running
✓ LLM provider connected
✓ Discord online
✓ Ollama reachable (if Path B)
✓ Memory consolidation healthy
```

### Detailed Logs
```bash
nanobot logs --follow
```

Watch for:
- Successful provider connections
- Message ingestion from channels
- Tool execution traces
- Error or warnings

---

## Startup Monitoring (Optional)

### Monitor Resource Usage
```bash
# On VPS or local machine
top
# Look for: nanobot process CPU/memory usage
# Should be low at idle (~1-5% CPU, <200MB RAM)
```

### Check Service Persistence (Advanced)
If using systemd service on VPS:
```bash
sudo systemctl status nanobot
sudo systemctl restart nanobot
# Service should restart cleanly without errors
```

---

## Shutdown Procedure (When Needed)

### Simple Path
```bash
# Gracefully stop nanobot (Ctrl+C in terminal)
Ctrl+C
# Nanobot will finish current message and shut down
```

### Advanced Path (VPS)
```bash
# Stop systemd service
sudo systemctl stop nanobot

# Or kill process
pkill -f nanobot
```

---

## Post-Startup Notes

After successful startup:
- Monitor logs for first hour for any warnings
- Test a real workflow (not just "hello")
- Check Obsidian write-back if applicable
- Verify cost tracking working (tokens logged, cost estimated)
- Document any customizations for recovery purposes

---

## Revision History

| Date | Version | Change |
|---|---|---|
| 2026-02-25 | 2.0.0 | Complete rewrite: Added simple startup path (local + cloud API); advanced startup path (VPS + Ollama); split scenario-specific instructions; added detailed troubleshooting; removed WSL2 references |
| Previous | 1.1.0 | WSL2-based startup procedure |
