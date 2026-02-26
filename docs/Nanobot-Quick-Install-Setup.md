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

## 7. Common Mistakes & Solutions

### ❌ Mistake 1: Python Version Too Old
**Problem:** `nanobot onboard` fails with "Python 3.11 required"  
**Why:** System Python is 3.10 or older; nanobot needs modern features  
**Fix:**
1. Check your Python version: `python --version`
2. If it's 3.10 or lower:
   - **Windows:** Download Python 3.12 from python.org, install it
   - **Mac:** `brew install python@3.12`
   - **Linux:** `apt install python3.12`
3. Use the new version explicitly:
   ```bash
   python3.12 -m pip install nanobot-ai
   python3.12 -m nanobot onboard
   ```
4. Verify: `python3.12 -c "import sys; print(sys.version)"`

### ❌ Mistake 2: API Key Copied Wrong (Extra Spaces or Newline)
**Problem:** `Invalid API key` error immediately after pasting  
**Why:** Pasting from email/Slack adds trailing whitespace  
**Fix:**
1. Copy the actual API key again, carefully
2. **Do NOT paste** from notification emails (they often wrap the key)
3. Go directly to provider dashboard: GitHub → Anthropic Console → OpenRouter
4. Copy from the official source
5. Double-check: Key should be **no spaces** at start or end
6. Paste: `echo "sk_..." | wc -c` to verify length (should be 40-80 characters, not longer)

### ❌ Mistake 3: Forgot to Start Ollama (If Using Local)
**Problem:** "Local model 'llama2' not found" when using Ollama  
**Why:** Didn't start Ollama server; it's not running in background  
**Fix:**
1. **Start Ollama first:**
   ```bash
   ollama serve
   ```
   (You'll see: `Listening on 127.0.0.1:11434`)
2. **In another terminal**, run nanobot:
   ```bash
   nanobot onboard
   ```
3. When asked "API key or local?", leave **blank** (hit Enter)
4. Verify Ollama is still running: `curl http://localhost:11434`

### ❌ Mistake 4: pip Install Failed, Old Version Still Active
**Problem:** `pip install nanobot-ai` succeeds, but `nanobot --version` shows old version  
**Why:** Old installation cached or multiple Python installations  
**Fix:**
1. **Clean install:**
   ```bash
   pip uninstall nanobot-ai
   pip cache purge
   pip install nanobot-ai --force-reinstall
   ```
2. **Verify new version:**
   ```bash
   nanobot --version
   ```
3. **If still wrong**, check where nanobot is installed:
   ```bash
   which nanobot
   ```
   (Should show something like `/usr/local/bin/nanobot` or `~/.local/bin/nanobot`)

### ❌ Mistake 5: Interactive Wizard Didn't Create Config File
**Problem:** After running `nanobot onboard`, no `~/.nanobot/config.json` exists  
**Why:** Wizard crashed or you cancelled it mid-setup  
**Fix:**
1. Check if file exists:
   ```bash
   cat ~/.nanobot/config.json
   ```
   (If not found, file doesn't exist yet)
2. **Rerun wizard:** `nanobot onboard`
3. **Complete all questions** without canceling
4. Verify created: `ls -la ~/.nanobot/`
5. If still failing, create manually:
   ```bash
   mkdir -p ~/.nanobot
   cat > ~/.nanobot/config.json << 'EOF'
   {
     "agent": {
       "name": "nanobot",
       "model": "gpt-4o"
     },
     "providers": {
       "openai": {
         "apiKey": "sk_...",
         "enabled": true
       }
     },
     "channels": {
       "cli": { "enabled": true }
     }
   }
   EOF
   ```

---

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
