# AOS Startup Procedure: Simple Build (Local + Cloud LLM)

**Quick-start deployment:** Run nanobot on your personal computer with cloud-based AI. Fastest path from zero to working system.

## Document Control
- **Owner:**
- **Version:** 2.4.1
- **Last Updated:** 2026-02-26
- **Status:** Active
- **Applies To:** Path A (Simple Build) only
- **Estimated Setup Time:** 25-35 minutes
- **Estimated Startup Time:** 5 minutes

## For Complete Beginners: Simple Build Overview

**What is Simple Build?**
- You run nanobot on your regular computer (Windows, Mac, or Linux laptop/desktop)
- The AI "brain" (large language model) runs on someone else's server in the cloud (OpenRouter, Anthropic, OpenAI, etc.)
- You pay only for the AI responses you actually use (like paying per question)
- It's the fastest, cheapest way to get started

**Who should choose this path:**
- ✅ You want to try nanobot in 30 minutes
- ✅ You're willing to pay $1-20/month for API usage (if you use it heavily)
- ✅ You're on Windows, Mac, or Linux (no special hardware needed)
- ✅ You have a regular internet connection
- ✅ Privacy isn't your top concern (responses go to a cloud provider)

**Who should NOT choose this path:**
- ❌ You need 24/7 uptime (your computer needs to be on)
- ❌ You want zero API costs (this uses cloud LLM providers)
- ❌ All your data must stay on your network (no cloud access allowed)
- → For those cases, see [AOS-Startup-Advanced-Build](AOS-Startup-Advanced-Build.md)

---

## Minimum Viable System

**You only need THREE things to get a working system:**
1. Python 3.11 or higher (a programming language tool)
2. Nanobot (the AI agent software)
3. An API key (a secret token from OpenRouter, Anthropic, or OpenAI)

That's it. You don't need:
- ❌ Ubuntu or Linux (Windows or Mac works fine)
- ❌ Obsidian (note-taking app—useful but optional)
- ❌ A fancy VPS or server (your regular computer works)
- ❌ GPU or special hardware (any modern computer works)

Once you have those three things, you can test nanobot by typing in your computer's terminal. **That's your first working system.**

After that works, you can optionally add Discord, Slack, Obsidian, and other features—but they're not required.

---

## Cost Expectations

**To get started:** $0 (free downloads and installs)

**Per month (if you use it):**
- **Light use** (a few questions per day): $0-2/month
- **Moderate use** (questions throughout the day): $5-20/month
- **Heavy use** (24/7 or thousands of questions): $50-100+/month

**Why?** You only pay cloud LLM providers (OpenRouter, Anthropic, OpenAI) when nanobot actually asks them questions. It's like taking a taxi: you only pay per mile.

**How to avoid surprise bills:** When you get your API key (see Step A3 below), most providers let you set a monthly spending limit (e.g., "stop charging me after $10/month"). We recommend setting one while testing.

---

## How to Open a Terminal (Command Line)

**What is a terminal?** A terminal is a program where you type commands instead of clicking buttons. It sounds technical, but it's just another program—like opening Notepad or Word.

**Why do I need it?** Most of the setup steps require typing commands in a terminal.

### On Windows

1. Click Windows Start button (bottom left)
2. Type `PowerShell` in search
3. Click "Windows PowerShell" or "PowerShell"
4. A blue window appears—that's your terminal

You should see: `PS C:\Users\YourName>`

### On macOS

1. Press Command + Space
2. Type `Terminal`
3. Press Enter or click "Terminal"
4. A window appears—that's your terminal

You should see: `YourName@Computer ~ %`

### On Linux

1. Right-click on desktop (blank area)
2. Look for "Open Terminal" option, OR
3. Open Applications menu and search for "Terminal"

You should see: `username@computer:~$`

### Terminal Tips

- **Copy-paste:** Right-click and select "Paste" (or Ctrl+Shift+V on Linux)
- **Errors are OK:** If you make a typo, you'll see an error. Just re-type carefully
- **Clear screen:** Type `clear` and press Enter
- **Stop a command:** Press `Ctrl+C`

---

# PART 1: SETUP (Do This First)

This section covers all prerequisites and configuration. Everything is done on your local computer. Estimated time: **25-35 minutes**.

---

## Step A1: Verify Your Machine Meets Minimum Requirements

### Check Your Operating System

Open your terminal and type:

```bash
# On Windows (in PowerShell): 
Get-CimInstance CIM_OperatingSystem | Select-Object Caption, Version
# This asks Windows: "Tell me your version"

# On macOS (in Terminal):
sw_vers
# This asks macOS: "Tell me your version"

# On Linux (in Terminal):
cat /etc/os-release
# This reads a file that says your Linux version
```

You should see:
- **Windows:** "Windows 10" or "Windows 11" (or newer)
- **macOS:** "macOS Ventura" or newer
- **Linux:** "Ubuntu 22.04" or similar (any modern 2022+ distribution)

If your version is older, you can upgrade for free from your operating system's website.

### Check Your Computer's Memory (RAM)

RAM is temporary memory your computer uses right now (different from hard drive storage).

```bash
# Windows (PowerShell):
Get-CimInstance CIM_PhysicalMemory | Measure-Object -Property Capacity -Sum
# This shows how much RAM (working memory) you have

# Linux:
free -h
# The "-h" means "show in human-readable format" (like "GB" instead of bytes)

# macOS (the 'free' command is not available on macOS):
system_profiler SPHardwareDataType | grep "Memory:"
# Or check via Apple menu → About This Mac → Memory
```

You need:
- **Minimum:** At least **4 GB RAM**
- **Recommended:** **8 GB RAM** (so your OS + browser + nanobot all run smoothly)

**Alternative check:** On Windows, right-click "This PC" and click Properties. On Mac, click Apple menu → "About This Mac." It shows your RAM.

### Check Your Hard Drive Space

Hard drive space is permanent storage. If you run out, you can't install things.

```bash
# Windows (PowerShell):
Get-Volume
# Shows all your hard drives and free space

# macOS/Linux:
df -h
# The "-h" means "show in human-readable format"
```

You need:
- **For installation:** ~500 MB (that's tiny—like one video)
- **For nanobot logs and memories:** An additional 1–5 GB (grows slowly over months)

Most computers have 100+ GB free, so this shouldn't be an issue.

### Test Your Internet Connection

Nanobot needs internet every time you ask it a question (to reach cloud AI providers).

```bash
ping 8.8.8.8
# Sends a tiny message to Google's server to check if internet works
# On Windows: stops automatically after 4 packets
# On macOS/Linux: runs until you press Ctrl+C (or use: ping -c 4 8.8.8.8)
```

You should see:
```
Reply from 8.8.8.8: bytes=32 time=25ms TTL=117
Reply from 8.8.8.8: bytes=32 time=26ms TTL=117
Reply from 8.8.8.8: bytes=32 time=24ms TTL=117
Reply from 8.8.8.8: bytes=32 time=25ms TTL=117
```

**What this means:** Each line shows a successful message. The "time=25ms" is how fast (25 milliseconds—very fast!).

✅ If you see at least 4 successful lines, your internet is working. If you see "Timeout" or "Unreachable," your WiFi or cable connection isn't working.

**To stop the test:** Press `Ctrl+C`

---

## Step A2: Verify or Install Python 3.11+

**What is Python?** A programming language. Think of it as a translator: nanobot is written in Python code, so you need Python installed to run that code.

**Why Python 3.11+?** Nanobot uses features that only exist in Python 3.11 and newer. Older versions (3.10, 3.9, etc.) won't work. The Python team released these newer versions recently, so older computers might have outdated Python. We're checking to make sure you have the right version.

**Check if you already have Python:**

```bash
python --version
# Shows your Python version
```

**What you'll see:**
- ✅ If it shows "Python 3.11" or higher (like 3.12, 3.13), you're done! Skip to Step A3.
- ❌ If it shows "Python 3.10" or lower, or says "command not found," install or upgrade (see below).

### Install or Upgrade Python

**On Windows:**
1. Go to https://www.python.org/downloads/
2. Click "Download Python 3.12" (or latest 3.11+)
3. Run the installer
4. **IMPORTANT:** Check the box "Add Python to PATH" during installation (so your computer finds Python)
5. Click "Install Now"
6. Verify: Open terminal and type `python --version`

**On macOS:**
```bash
# Using Homebrew (if you have it installed):
brew install python@3.12

# Then verify:
python3 --version
```

Or download from https://www.python.org/downloads/ and run the installer.

**On Linux:**
```bash
# Ubuntu/Debian:
sudo apt update
sudo apt install python3.12

# Fedora/RHEL:
sudo dnf install python3.12

# Verify:
python3.12 --version
```

---

## Step A3: Obtain an LLM API Key

**What is an API Key?** A secret token (password-like string) that lets you use someone else's AI. Think of it like a library card: with your card, you can check out books. With an API key, you can "check out" AI responses.

**Why do you need this?** Nanobot can't think on its own. It needs to ask a cloud AI provider (Claude, GPT-4, etc.) to generate responses. Your API key proves you're allowed to ask.

**Choose ONE AI provider:**

If you've never done this, I recommend **OpenRouter** for beginners—it's easiest and supports many models.

### Option 1: OpenRouter (Recommended for Beginners)

1. Go to https://openrouter.ai
2. Click "Sign Up" (or "Sign In" if you have an account)
3. Create an account (email + password, takes 1 minute)
4. After signing up, you're logged in
5. Go to https://openrouter.ai/keys
6. You should see a "Create Key" button or form
7. Click "Create Key"
8. Copy the entire key (looks like: `sk-or-abc123xyz...`)
9. **Paste this key somewhere safe temporarily** (like a text editor on your desktop)—you'll need it in Step A5
10. **Optional but recommended:** On OpenRouter, set a monthly spending limit (e.g., "$10/month") to avoid surprise charges while testing

**Keep your key private:**
- Don't share it in chats, Discord messages, GitHub, Slack channels, or public code
- Only paste it into `~/.nanobot/config.json` in Step A5
- Don't paste it in Discord servers or forums—anyone with your key can use your API and incur charges
- If you accidentally share it, go back to OpenRouter immediately and delete/regenerate the key

### Option 2: Anthropic (If You Prefer Claude)

1. Go to https://console.anthropic.com
2. Sign up or log in
3. Go to "API Keys" (look in the left menu)
4. Click "Create Key"
5. Name: "nanobot" (or any name)
6. Copy the key (starts with "sk-ant-")
7. Paste it somewhere safe temporarily
8. Add a payment method to your account (required to use the API)

### Option 3: OpenAI (If You Prefer GPT-4)

1. Go to https://platform.openai.com
2. Sign up or log in
3. Go to "API Keys" (click your name in top right)
4. Click "Create new secret key"
5. Copy the key (starts with "sk-")
6. Paste it somewhere safe temporarily
7. **Important:** Add a payment method to your account (required before using the API)
8. **Recommended:** Set a monthly spending limit in account settings

---

## Step A4: Install Nanobot

Install nanobot using Python's package manager (pip):

```bash
pip install nanobot-ai
# This downloads and installs nanobot and everything it needs
```

Takes 1-2 minutes.

**Verify installation:**

```bash
nanobot --version
# Should print something like: 🐈 nanobot v0.1.4.post2
```

✅ You should see a version number (exact version may differ).

**If you get "command not found" error:**

Try using Python module directly:

```bash
python -m nanobot --version
```

If that works, you can reinstall:

```bash
pip install --force-reinstall nanobot-ai
```

---

## Step A5: Run Onboard and Configure Your API Key

Run the onboard command to create your config file and workspace:

```bash
nanobot onboard
```

Expected output:
```
✓ Created config at /home/you/.nanobot/config.json
✓ Created workspace at /home/you/.nanobot/workspace
  Created AGENTS.md
  Created memory/MEMORY.md
  Created memory/HISTORY.md

🐈 nanobot is ready!

Next steps:
  1. Add your API key to ~/.nanobot/config.json
     Get one at: https://openrouter.ai/keys
  2. Chat: nanobot agent -m "Hello!"
```

(On Windows, paths will look like `C:\Users\YourName\.nanobot\config.json`.)

**Now add your API key to the config file.** Open `~/.nanobot/config.json` in any text editor:

- **Windows (PowerShell):** `notepad $env:USERPROFILE\.nanobot\config.json`
- **macOS:** `open ~/.nanobot/config.json`
- **Linux:** `nano ~/.nanobot/config.json`

Find the `providers` section and add your key to the matching provider block. The file is valid JSON — don't add extra commas or remove brackets.

**For OpenRouter:**
```json
{
  "providers": {
    "openrouter": {
      "apiKey": "sk-or-YOUR_KEY_HERE"
    }
  }
}
```

**For Anthropic:**
```json
{
  "providers": {
    "anthropic": {
      "apiKey": "sk-ant-YOUR_KEY_HERE"
    }
  }
}
```

**For OpenAI:**
```json
{
  "providers": {
    "openai": {
      "apiKey": "sk-YOUR_KEY_HERE"
    }
  }
}
```

Save the file when done.

**If you're using an OpenAI key:** The default model in `config.json` is `anthropic/claude-opus-4-5`, which can't be served by OpenAI. Also find the `agents.defaults.model` field and change it to an OpenAI model — for example:
```json
{
  "agents": {
    "defaults": {
      "model": "gpt-4o"
    }
  }
}
```
OpenRouter and Anthropic users can leave the default model as-is.

✅ **Success:** Config file exists at `~/.nanobot/config.json` and contains your API key. You're ready for Part 2.

---

# PART 2: STARTUP (Do This After Setup)

Now that everything is configured, here's how to start nanobot and test it.

---

## Pre-Startup Checklist

Before you start, verify you have:
- ✅ Python 3.11+ installed (`python --version` works)
- ✅ Nanobot installed (`nanobot --version` works)
- ✅ API key added to `~/.nanobot/config.json`
- ✅ Internet connection working (`ping 8.8.8.8` succeeds)

---

## Step 1: Verify Connectivity

```bash
ping 8.8.8.8
# Should respond with pings (internet is up)
```

✅ You should see at least 4 successful replies.

---

## Step 2: Test with CLI (Terminal Chat—Your First Conversation!)

**This is the fastest way to verify nanobot works.** You don't need Discord, Slack, or a separate gateway for this.

Open a terminal and run:

```bash
nanobot agent
```

This starts an interactive chat. You'll see:
```
🐈 Interactive mode (type exit or Ctrl+C to quit)

You: 
```

Now type a simple question:

```
You: What is 2+2?
```

Press Enter and wait 3-5 seconds. Nanobot should respond:
```
🐈 nanobot

2 + 2 equals 4.
```

✅ **Success!** If you get a response, nanobot is working end-to-end.

Try more questions:
```
You: What is the capital of France?
You: Tell me a joke
You: What tools do you have?
```

**If you get no response:**
- ❌ Check internet: `ping 8.8.8.8`
- ❌ Verify your API key is correctly set in `~/.nanobot/config.json` (no extra spaces, valid JSON)
- ❌ Check API provider is active (log into OpenRouter/Anthropic/OpenAI and verify your account)
- ❌ Try waiting 10 seconds and asking again (APIs sometimes have brief delays)
- → If still broken, see "Still Completely Stuck? Nuclear Reset" section below

**To exit:**

Type `exit` or press `Ctrl+C`.

---

## Step 3: Start the Gateway for External Channels (Optional)

**Only needed if you've configured Discord, Telegram, Slack, or another external channel.**
For CLI-only use (Step 2 above), you can skip this step entirely.

The gateway manages background channel connections and heartbeat. Open a terminal and run:

```bash
nanobot gateway
```

Expected output:
```
🐈 Starting nanobot gateway on port 18790...
Warning: No channels enabled
✓ Heartbeat: every 1800s
```

(If you've configured a channel, you'll see `✓ Channels enabled: discord` or similar instead of the warning.)

✅ The terminal stays open and shows live activity. Keep it running while nanobot is in use.

**If the gateway fails to start:**
- ❌ Verify your API key is correctly set in `~/.nanobot/config.json`
- ❌ Verify API key hasn't expired (log into OpenRouter/Anthropic/OpenAI and check)
- ❌ Check internet: `ping 8.8.8.8`
- ❌ Verify config file exists: `cat ~/.nanobot/config.json` (should show valid JSON)
- → If still broken, see "Still Completely Stuck? Nuclear Reset" section below

---

## Step 4 (Optional): Test with Discord or Slack

Once CLI is working (Step 2), you can optionally add Discord/Slack. **This step is optional.** You already have a working system!

**Prerequisite:** `nanobot gateway` must be running (see Step 3) and your Discord/Slack bot token must be configured in `~/.nanobot/config.json`.

1. Go to your Discord/Slack channel where nanobot is a member
2. Send: `@BotName hello`
3. The bot should respond within 5 seconds

✅ Expected: "Hello! How can I help?" or similar

If the bot doesn't respond, see the [channel integration guide](../nanobot/README.md).

---

## Step 5 (Optional): Test with Obsidian

If you configured Obsidian vault access, test write-back:

In CLI (`nanobot agent`):
```
You: Write this to Obsidian: "Startup test at {{timestamp}}" to 00-System/startup-log.md
```

Check your Obsidian vault: Look in `00-System/startup-log.md`

✅ Expected: A new entry appears

If write fails, verify Obsidian vault path in `~/.nanobot/config.json` is correct.

---

## Validation: Simple Startup Complete ✅

- ✅ CLI test succeeds (`nanobot agent` responds to questions in terminal)
- ✅ Optional: Gateway starts without errors (`nanobot gateway`, if configured)
- ✅ Optional: Discord/Slack respond (if configured)
- ✅ Optional: Obsidian write succeeds (if configured)

**Congratulations!** You have a working Simple Build nanobot system. You can now:
- Ask questions in the terminal
- Add more channels (Discord, Slack, Telegram, etc.)
- Configure Obsidian for memory and audit logs
- Set up workflows and automations

---

## Shutdown Procedure

To stop nanobot cleanly, press `Ctrl+C` in the terminal where it's running. Nanobot finishes any in-progress message before exiting.

**If running `nanobot agent` interactively**, you can also type:
```
exit
```

**If running `nanobot gateway`**, `Ctrl+C` is the only option — it has no interactive prompt.

---

## Still Completely Stuck? Nuclear Reset Option

**Read this if:** You've tried everything and nanobot still won't work.

**What this does:** Completely removes nanobot and lets you start fresh (5 minutes).

### Step 1: Uninstall Nanobot

```bash
pip uninstall nanobot-ai
# Answer 'y' (yes) if it asks
```

### Step 2: Delete All Configuration Files

**On Windows (PowerShell):**
```powershell
Remove-Item $env:USERPROFILE\.nanobot -Recurse -Force
```

**On macOS/Linux:**
```bash
rm -rf ~/.nanobot
```

### Step 3: Clear Python Cache (Optional but Recommended)

```bash
pip cache purge
```

### Step 4: Start Fresh

```bash
pip install nanobot-ai
nanobot onboard
```

This is exactly the same as Step A4 and A5 above.

### Step 5: Retest

```bash
# Test CLI directly:
nanobot agent

# Or, if you use external channels, start the gateway:
nanobot gateway
```

---

## Frequently Asked Questions (Simple Build)

**Q: Do I need to leave my computer on 24/7?**  
A: No. Nanobot only runs when you start it. Close the terminal running `nanobot agent` or `nanobot gateway` and it stops.

**Q: How much will this cost me?**  
A: It depends on usage. Set a monthly spending limit on your API provider (OpenRouter, Anthropic, OpenAI) to cap costs at whatever you're comfortable with (e.g., $10/month).

**Q: Can I use both OpenRouter AND Anthropic?**  
A: Not simultaneously by default. You can add keys for multiple providers in `~/.nanobot/config.json` and nanobot will route automatically based on the model name. See the [LLM Provider Setup Guide](LLM-Provider-Setup-Guide.md) for details.

**Q: What if I want to move to Advanced Build later?**  
A: You can! Copy your `~/.nanobot/` directory and import it into a VPS. Both use the same config format.

**Q: Does nanobot work if I'm offline?**  
A: No. Simple Build requires internet to reach cloud LLM providers. For offline-capable systems, see [AOS-Startup-Advanced-Build](AOS-Startup-Advanced-Build.md).

**Q: How do I back up my nanobot configuration?**  
A: Copy the `~/.nanobot/` directory to a safe location. This contains all config, secrets, and logs.

---

## Revision History

| Date | Version | Change |
|---|---|---|
| 2026-02-26 | 2.4.1 | Additional accuracy fixes: corrected onboard output (✓ not 🐈 prefix); removed Rich markup tags from gateway output; fixed stale "setup wizard" reference in Step A3; added OpenAI model override note to Step A5; fixed 24/7 FAQ answer. |
| 2026-02-26 | 2.4.0 | Accuracy corrections: removed fabricated onboard wizard; replaced with actual `nanobot onboard` behavior + manual config.json setup. Fixed command architecture (nanobot agent is standalone; gateway not required for CLI). Fixed expected terminal output for gateway and agent. Removed all ~/.nanobot/.env references (config.json only). Fixed CLI prompt from >>> to You:. |
| 2026-02-26 | 2.3.0 | Fixed broken link: Multi-Channel-Integration-Guide.md → nanobot/README.md |
| 2026-02-26 | 2.2.0 | Split from AOS-Startup-Procedure.md. Focused entirely on Simple Build (Path A). Added beginner-friendly language, cost expectations, minimum viable system callout, terminal guide, and nuclear reset option. Removed all Advanced Build content. |
| 2026-02-25 | 2.1.0 | Initial comprehensive rewrite in parent document |
| 2026-02-25 | 2.0.0 | Initial version |
