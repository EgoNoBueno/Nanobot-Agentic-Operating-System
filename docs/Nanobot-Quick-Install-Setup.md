# Nanobot Quick Install & Setup

> **Start here** if you're new to nanobot. This guide gets you a working AI assistant in 5 minutes.

## Document Control
- **Owner:**
- **Version:** 1.0.0  
- **Last Updated:** 2026-02-25
- **Status:** Active

## 1. Objective
Get nanobot installed, configured with an LLM provider, and working end-to-end in minimal time.

## 2. Quick Prerequisites
- Python 3.11+ installed
- An LLM API key (or local Ollama running)
- Intending to use CLI, Discord, Slack, or another channel

## 3. Install (2 minutes)

### Option A: via pip (Recommended)
```bash
pip install nanobot-ai
```

### Option B: via uv (Fast)
```bash
uv tool install nanobot-ai
```

### Option C: From source
```bash
git clone https://github.com/HKUDS/nanobot.git
cd nanobot
pip install -e .
```

## 4. First Run: Interactive Setup (2 minutes)

```bash
nanobot onboard
```

This launches an interactive wizard. Answer these key questions:

| Question | Answer |  
|---|---|
| API key or local? | Paste your API key (e.g., OpenRouter, OpenAI, Anthropic) OR leave blank for local Ollama |
| Which channels? | Choose Discord, Slack, Telegram, Feishu, or CLI (local chat) |
| Model to start with? | anthropic/claude-opus-4-5 (via OpenRouter) OR llama2 (local) |

The wizard creates `~/.nanobot/config.json` with your choices.

## 5. Test It (1 minute)

### CLI Test (Easiest)
```bash
nanobot agent
```
Type a question. Hit Enter. You should get a response.

### Discord/Slack/Telegram Test
The wizard output shows your bot token and setup instructions for your chosen channel. Paste the token into Discord/Slack/etc. settings, then send a message in a channel the bot is in.

## 6. Next Steps

**For AOS-style governance:**
- See [LLM Provider Setup Guide](LLM-Provider-Setup-Guide.md) for multiple model routing
- See [Multi-Channel Integration Guide](Multi-Channel-Integration-Guide.md) for channel policies
- See [Tools & Skills Reference](Tools-and-Skills-Reference.md) for what nanobot can do

**For advanced (VPS + Ollama + Tailscale):**
- See [Nanobot Build Procedure](Nanobot-Build-Procedure.md)

## 7. Common Issues

| Symptom | Fix |
|---|---|
| `ModuleNotFoundError: nanobot` | Reinstall: `pip install nanobot-ai --force-reinstall` |
| API key rejected | Verify key format and expiry in your API provider dashboard |
| No response in CLI | Check internet connection and model availability |
| Discord bot offline | Paste correct bot token and check Message Content Intent is enabled |

## 8. What Nanobot Can Do (Out of the Box)

✅ **Web Search** - Real-time information via Brave API  
✅ **File Ops** - Read, write, edit files (workspace-scoped)  
✅ **Shell** - Execute commands (with allowlist)  
✅ **GitHub** - Search repos, manage PRs/issues  
✅ **Scheduling** - Run tasks on cron schedule  
✅ **Memory** - Remember conversation context  
✅ **MCP Tools** - Integrate external services  

See [Tools & Skills Reference](Tools-and-Skills-Reference.md) for details.

## 9. Revision History

| Date | Version | Change |
|---|---|---|
| 2026-02-25 | 1.0.0 | Initial quick-install guide for nanobot v0.1.4+ |
