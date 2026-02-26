# AOS Startup Procedure

Bring nanobot from fully powered down to fully operational. Choose your path based on your deployment scenario.

## Document Control
- **Owner:**
- **Version:** 2.0.0
- **Last Updated:** 2026-02-25
- **Status:** Active

## 1. Overview

New users of the Nanobot Agentic Operating System can choose between two primary installation pathways: the **Simple Build** and the **Advanced Build**.



The **Simple Build** is designed for a quick start, typically taking about 5 minutes, and involves a local `pip` installation on your own computer combined with a cloud-based LLM provider like OpenRouter, Anthropic, or OpenAI. This is the ideal choice for individuals or teams who want to get up and running immediately with minimal technical overhead and low initial costs.


The **Advanced Build** is a more robust deployment that takes approximately 30–45 minutes and utilizes a Virtual Private Server (VPS) for the system gateway and local inference via Ollama. Users would choose this pathway if they prioritize complete data privacy, require a self-hosted infrastructure, or want to avoid ongoing per-token cloud costs by running their own local models. While it requires more initial effort to configure components like Tailscale-encrypted tunnels and VPS firewalls, it provides significantly more control and long-term scalability for production environments.

Two startup scenarios:
- **Simple Startup** (10 min) - Local nanobot + cloud LLM (Large Language Model) API

For the **Simple Build** pathway, the Nanobot Agentic Operating System (AOS) is designed to be ultra-lightweight, allowing it to run on almost any modern consumer hardware.

Because the "Simple Build" offloads the heavy AI processing (inference) to cloud providers like OpenRouter or Anthropic, your local machine only needs enough resources to run the Python-based orchestration engine.

### 1. Operating System

Nanobot is cross-platform and supports the following:

* **Windows:** 10 or 11 (standard installations or via WSL2 (Windows Subsystem For Linux 2. 2 Means Linux can utilize the systems GPU)).
* **macOS:** Ventura (13.0) or newer (supports both Intel and Apple Silicon/M-series chips).
* **Linux:** Any modern distribution (Ubuntu 22.04+, Debian, Fedora, etc.).

### 2. Hardware Requirements

Since the "Simple Build" does **not** run models locally, the hardware requirements are very low:

* **Processor (CPU):** Any modern dual-core processor (Intel Core i3/i5, AMD Ryzen 3/5, or Apple M1/M2/M3).
* **Memory (RAM):** * **Minimum:** 4 GB RAM.
* **Recommended:** 8 GB RAM (to comfortably run your OS, a web browser, and the Nanobot gateway simultaneously).
* *Note: Nanobot itself typically uses less than 200MB of RAM while idle.*


* **Storage:** * **Installation:** ~500 MB of free space for the Nanobot software and Python dependencies.
* **Workspace/Memory:** An additional 1–5 GB is recommended for your **Obsidian Vault** and local audit logs, which grow over time as the agent "remembers" interactions.


* **Internet Connection:** A stable, always-on broadband connection is **required** because this build relies on cloud APIs to function.

### 3. Software Prerequisites

In addition to the hardware, you must have:

* **Python 3.11 or higher:** This is the most critical requirement.
* **An LLM API Key:** (e.g., OpenRouter, Anthropic, or OpenAI) to power the "brain" of the system.

- **Advanced Startup** (15 min) - VPS (Virtual Private Server) nanobot + local Ollama inference.

For the **Advanced Build** pathway, the Nanobot Agentic Operating System (AOS) uses a distributed architecture to maximize privacy and control while optimizing costs. This setup separates the "brain" (the LLM) from the "nervous system" (the gateway and tools).

### 1. Operating System

The Advanced Build requires two distinct environments:

* **VPS (Virtual Private Server):** A clean installation of **Ubuntu 22.04 LTS or newer** is highly recommended for the gateway.
* **Local Machine:** Windows (10/11), macOS (Ventura+), or any modern Linux distribution to host the local inference engine.

### 2. Hardware Requirements

Because this path runs the AI models on your own hardware, the requirements are higher than the Simple Build:

**The Gateway (VPS):**

* **Processor (CPU):** 1–2 vCPUs.
* **Memory (RAM):** 1–2 GB RAM (Nanobot is lightweight; most of the VPS resources are for the OS and network handling).
* **Storage:** 10–20 GB SSD.

**The Inference Engine (Local Machine):**

* **Processor (CPU):** Modern multi-core CPU (Intel i7/i9, AMD Ryzen 7/9, or Apple M1/M2/M3 Max).
* **Graphics (GPU):** Highly recommended for speed. NVIDIA GPU with **8GB+ VRAM** (e.g., RTX 3060 or better) or Apple Silicon with **16GB+ Unified Memory**.
* **Memory (RAM):** 16 GB minimum (32 GB+ recommended for running larger models like Llama 3-70B).
* **Storage:** 40 GB+ of fast SSD space to store multiple local models (typical models are 4GB–20GB each).

### 3. Software & Network Prerequisites

This build relies on secure "tunnels" to connect your cloud VPS to your home/office computer:

* **Tailscale:** Used to create a secure, encrypted private network between your VPS and local machine without opening risky firewall ports.
* **Ollama:** The local inference engine that actually runs the AI models.
* **Python 3.11+:** Required on the VPS to run the Nanobot core.
* **Cloud Fallback Key:** (Optional but recommended) An API key from OpenRouter or Anthropic to ensure the system stays online if your local machine loses power or internet.



## 2. Assumptions

**IMPORTANT:** Both the **Simple Build** and **Advanced Build** pathways assume you are starting brand new with **NO prior setup completed**. This means:

- Nanobot is **not yet installed**
- No config files exist yet (`~/.nanobot/config.json` or `.env`)
- You have **not yet obtained** API keys, tokens, or secrets
- Channels (Discord, Slack, etc.) are **not yet configured** with nanobot
- Obsidian vault does **not yet exist** (if you plan to use it)

This guide walks you through every step, from zero to fully operational.

---

## How to Copy & Paste Commands in Linux

When following startup steps with bash commands:

**Recommended methods (in order):**
1. **Middle-click paste:** Select command → middle-click in terminal (fastest)
2. **Keyboard:** Select → `Ctrl+Shift+C` → `Ctrl+Shift+V` to paste
3. **Right-click:** Select → `Ctrl+C` → right-click → Paste

Most Linux/Mac terminals support middle-click for instant pasting.

---

# PART 1: SETUP (Do This First)

The following sections walk you through all setup steps BEFORE you start nanobot. Choose your path based on which deployment you want.

---

## Path A: Simple Build Setup (Local + Cloud API)

This section covers all prerequisites and configuration needed before you can start the nanobot gateway. Everything is done on your local computer. Estimated time: **25-35 minutes** for first-time setup.

### Step A1: Verify Your Machine Meets Minimum Requirements

**Check your operating system:**
```bash
# On Windows (in PowerShell): 
Get-CimInstance CIM_OperatingSystem | Select-Object Caption, Version

# On macOS (in Terminal):
sw_vers

# On Linux (in Terminal):
cat /etc/os-release
```

Make sure you meet these minimums:
- **Windows:** Version 10 or 11
- **macOS:** Ventura (13.0) or newer  
- **Linux:** Ubuntu 22.04+, Debian, Fedora (any modern distribution)

**Check your hardware resources:**
```bash
# Check RAM available
# Windows (PowerShell):
Get-CimInstance CIM_PhysicalMemory | Measure-Object -Property Capacity -Sum

# macOS/Linux:
free -h
```

You need at least **4 GB RAM** (8 GB recommended). Nanobot itself uses less than 200 MB, but your OS and other applications need space.

**Check available disk space:**
```bash
# Windows (PowerShell):
Get-Volume

# macOS/Linux:
df -h
```

You need at least **500 MB free** for installation plus an additional **1-5 GB** for logs and vault growth.

**Verify internet connection:**
```bash
ping 8.8.8.8
```

✅ Should show successful pings. This confirms you have stable internet (required for cloud LLM providers).

### Step A2: Verify or Install Python 3.11+

Python is the runtime that nanobot uses. Check if you already have it:

```bash
python --version
```

**If Python is already installed:**
- ✅ If version is 3.11 or higher, you can skip to Step A3
- ❌ If version is 3.10 or lower, you must upgrade (see below)

**If Python is NOT installed or you need to upgrade:**

**On Windows:**
1. Go to https://www.python.org/downloads/
2. Click the **Download Python 3.12** button (or latest 3.11+)
3. Run the installer
4. **IMPORTANT:** Check the box "Add Python to PATH" during installation
5. Click "Install Now"
6. Verify installation:
   ```bash
   python --version
   ```

**On macOS:**
```bash
# Using brew (if you have it installed):
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

### Step A3: Obtain an LLM API Key

You need an API key from one of these providers to power nanobot's "brain":

**Option 1: OpenRouter (Recommended for beginners)**
- Supports multiple models (Claude, GPT-4, etc.)
- Easy to set up, good pricing
- Steps:
  1. Go to https://openrouter.ai
  2. Click "Sign Up" (or "Sign In" if you have an account)
  3. Go to https://openrouter.ai/keys
  4. Click "Create Key"
  5. Copy the key (starts with "sk-or-")
  6. **Store this key somewhere safe** (you'll need it soon)
  7. Optional: Set a usage limit on this page to avoid surprise charges

**Option 2: Anthropic (If you prefer Claude)**
- Steps:
  1. Go to https://console.anthropic.com
  2. Sign up or log in
  3. Go to "API Keys"
  4. Click "Create Key"
  5. Name: "nanobot" or similar
  6. Copy the key (starts with "sk-ant-")
  7. Store it safely

**Option 3: OpenAI (If you prefer GPT-4)**
- Steps:
  1. Go to https://platform.openai.com
  2. Sign up or log in
  3. Go to "API Keys"
  4. Click "Create new secret key"
  5. Copy the key (starts with "sk-")
  6. Store it safely
  7. Add a payment method to your account (required for API usage)

**Important:** Your API key is like a password. Treat it as a secret:
- ❌ Never share it in public chat, emails, or version control
- ❌ Never paste it into untrusted websites
- ✅ Only paste it into nanobot's setup wizard

### Step A4: Install Nanobot

Now install the nanobot package using Python's package manager (pip):

```bash
pip install nanobot-ai
```

This downloads and installs nanobot and all its dependencies. It takes 1-2 minutes.

**Verify installation:**
```bash
nanobot --version
```

✅ Should print something like: `nanobot version 0.1.4`

**If you get "command not found" error:**

This usually means nanobot isn't in your system PATH. Try:

```bash
# Use the Python module directly:
python -m nanobot --version

# Or reinstall:
pip install --force-reinstall nanobot-ai
```

### Step A5: Run the Interactive Setup Wizard

This wizard creates your nanobot configuration file and asks a few key questions:

```bash
nanobot onboard
```

You'll be asked these questions:

**Question 1: "Do you want to use a cloud LLM or local inference?"**
- Answer: Type your API key (e.g., `sk-or-...` from Step A3) and press Enter
- OR: Press Enter with nothing to use local Ollama (left blank)
- For this path, paste your API key

**Question 2: "Which channel would you like to use?"**
- Answer: Type one of: `discord`, `slack`, `telegram`, `feishu`, or `cli`
- You can add more channels later
- For easiest testing, choose `cli` (command-line chat on your computer)
- For Discord, see Step A6 below

**Question 3: "Which model would you like to start with?"**
- Answer: Press Enter to accept the default, or type a specific model name
- Examples: `anthropic/claude-opus-4-5` (via OpenRouter), `gpt-4` (via OpenAI), `gpt-4o-mini` (cheaper option)

**Question 4: "Do you want to enable the Obsidian vault feature?"**
- Answer: `yes` if you use Obsidian and want nanobot to read/write notes
- Answer: `no` for now if you don't use Obsidian (you can enable it later)
- If yes, provide the path to your Obsidian vault (e.g., `/Users/yourname/Documents/ObsidianVault`)

The wizard then creates:
- `~/.nanobot/config.json` - Your configuration file
- `~/.nanobot/.env` - Your secrets (API key, tokens)

✅ **Success:** The wizard prints "Configuration saved!" and you're ready for Step A6.

### Step A6: Configure Discord (If You Chose Discord)

If you chose Discord in Step A5, you need to:

1. **Create a Discord Application:**
   - Go to https://discord.com/developers/applications
   - Click "New Application"
   - Name: "nanobot" (or any name you like)
   - Click "Create"

2. **Enable necessary permissions:**
   - Go to "OAuth2" → "Scopes"
   - Check: `bot`
   - Go to "OAuth2" → "Permissions"
   - Check:
     - `Read Messages/View Channels`
     - `Send Messages`
     - `Read Message History`
     - `Embed Links`
     - `Mention @everyone, @here, and All Roles`

3. **Generate the bot token:**
   - Go to "Bot"
   - Click "Reset Token" (if you need a new one, otherwise it's already shown)
   - Copy the token (starts with something like `OTk1...`)
   - Click the copy icon (don't manually select it)

4. **Add the token to nanobot:**
   - Edit `~/.nanobot/.env` (or `.nanobot/config.json` depending on your setup)
   - Find the line: `DISCORD_TOKEN=`
   - Paste your token: `DISCORD_TOKEN=OTk1...`
   - Save the file

5. **Invite the bot to your Discord server:**
   - Go back to https://discord.com/developers/applications
   - Click on your "nanobot" application
   - Go to "OAuth2" → "URL Generator"
   - Under "Scopes," check `bot`
   - Under "Permissions," check the same permissions as step 2
   - Copy the generated URL from the bottom
   - Paste it in your browser
   - Select the Discord server you want to add the bot to
   - Click "Authorize"

6. **Test the connection:**
   - Go to any Discord channel where the bot is now a member
   - Type: `@nanobot hello`
   - The bot should respond within 5 seconds

### Step A7 (Optional): Configure Obsidian Vault

If you want nanobot to read and write to your Obsidian notes:

1. **Open your Obsidian vault location:**
   - In Obsidian: Click the vault icon (top left) → "Open folder as vault" or check Settings → About → Vault location
   - Note the path (e.g., `/Users/yourname/Documents/MyVault`)

2. **Update nanobot config:**
   - Edit `~/.nanobot/config.json`
   - Find: `"obsidian_vault_path": ""`
   - Add your path: `"obsidian_vault_path": "/Users/yourname/Documents/MyVault"`
   - Save

3. **Create the system folder:**
   - In Obsidian, create a new folder: `00-System`
   - Create a note inside: `startup-log.md`
   - This is where nanobot will write test messages

---

## Path B: Advanced Build Setup (VPS + Local Ollama)

This section covers setup for a distributed deployment: nanobot gateway on a VPS, AI inference on your local machine via Ollama, connected securely with Tailscale. Estimated time: **45-90 minutes** for first-time setup.

### Step B1: Rent a Virtual Private Server (VPS)

You need a cloud server to run the nanobot gateway. Popular VPS providers:

**Recommended providers:**
- **DigitalOcean** (https://www.digitalocean.com/) - $4-6/month, beginner-friendly
- **Linode** (https://www.linode.com/) - $5-10/month, reliable
- **Hetzner** (https://www.hetzner.com/) - €3-5/month, Europe-based, affordable
- **AWS EC2** (https://aws.amazon.com/ec2/) - Pay-as-you-go, pricing varies

**Create a VPS with these specs:**
- **OS:** Ubuntu 22.04 LTS (or newer)
- **CPU:** 1-2 vCPUs
- **RAM:** 1-2 GB
- **Storage:** 10-20 GB SSD
- **Region:** Choose closest to you for lower latency

**After creation, you'll receive:**
- VPS IP address (e.g., `192.168.1.1`)
- Root password OR SSH key
- SSH connection command (e.g., `ssh root@192.168.1.1`)

Store these securely.

### Step B2: Connect to Your VPS via SSH

SSH (Secure Shell) is how you access your VPS from your local computer.

**On Windows:**
- Download PuTTY: https://www.putty.org/
- Launch PuTTY
- Under "Host Name," enter your VPS IP (e.g., `192.168.1.1`)
- Click "Open"
- When prompted, type `root` as username
- Paste the password your VPS provider gave you

**On macOS/Linux:**
```bash
ssh root@YOUR_VPS_IP
# Replace YOUR_VPS_IP with your actual IP (e.g., 192.168.1.1)
# When prompted, paste your VPS root password
```

⚠️ **Linux Password Note:** When pasting your VPS password, **nothing will appear on screen—no dots, no asterisks.** This is normal. Just paste and press Enter.

✅ You should now see a prompt like: `root@ubuntu-server:~#`

### Step B3: Update the VPS System

Always update packages on a new VPS:

```bash
apt update
apt upgrade -y
```

This takes 1-2 minutes and ensures all security patches are installed.

### Step B4: Install Python 3.11+ on the VPS

```bash
apt install -y python3.12 python3.12-venv python3-pip
```

Verify:
```bash
python3.12 --version
```

### Step B5: Install Nanobot on the VPS

```bash
pip install nanobot-ai
```

Verify:
```bash
python -m nanobot --version
```

### Step B6: Prepare Ollama on Your Local Machine

**On your local computer** (NOT the VPS), install Ollama:

**On Windows:**
1. Go to https://ollama.ai
2. Click "Download for Windows"
3. Run the installer
4. Click "Install"
5. Ollama starts automatically

**On macOS:**
1. Go to https://ollama.ai
2. Click "Download for macOS"
3. Run the installer (drag to Applications)
4. Start Ollama from Applications

**On Linux:**
```bash
curl -fsSL https://ollama.ai/install.sh | sh
# Start Ollama:
ollama serve
```

**Verify Ollama is running:**
```bash
curl http://localhost:11434/api/tags
```

✅ Should return a JSON list of models.

**Pull a model** (models are AI engines—pick one):

```bash
# Quick and small (good for testing):
ollama pull mistral

# Or if you have more disk space (better quality):
ollama pull neural-chat
ollama pull dolphin-mixtral
```

Each model is 4-50 GB, so pick based on your available disk space.

### Step B7: Install and Configure Tailscale on Both Machines

Tailscale creates a secure tunnel between your VPS and local Ollama machine, so they can talk securely without opening risky firewall ports.

**On your local computer:**

**Windows:**
1. Go to https://tailscale.com/download
2. Download for Windows
3. Run installer, click "Install"
4. When prompted, click "Log in"
5. Browser opens—log in with Google, GitHub, Microsoft, or create a Tailscale account
6. Approve the device
7. Close browser and installer
8. Right-click Tailscale icon (system tray) → "Copy device IP"
9. **Note this IP** (e.g., `100.123.45.67`)

**macOS:**
1. Go to https://tailscale.com/download
2. Download for macOS
3. Drag to Applications, launch
4. When prompted, click "Log in"
5. Browser opens—log in
6. Approve the device
7. Click Tailscale menu → "Copy device IP"
8. **Note this IP**

**Linux:**
```bash
curl -fsSL https://tailscale.com/install.sh | sh
tailscale up
# Browser pops up—log in
# Browser redirects—approve device
# In terminal, Tailscale prints your IP
# Note: e.g., 100.123.45.67
```

**On your VPS** (via SSH):

```bash
apt install tailscale
tailscale up
# Tailscale prints a login URL
# Open that URL in your local browser
# Log in with same Tailscale account
# Approve the VPS device
# Back in SSH, Tailscale prints the VPS IP
# Note this IP (e.g., 100.87.123.45)
```

**Enable MagicDNS** (so you can use device names instead of IPs):
1. Open https://login.tailscale.com/admin/dns
2. Scroll to "MagicDNS"
3. Click "Enable" (if not already enabled)

**Test the Tailscale connection:**

From your VPS, verify you can reach local Ollama:
```bash
curl http://100.123.45.67:11434/api/tags
# Replace 100.123.45.67 with your LOCAL machine's Tailscale IP
# Should return JSON list of models
```

✅ If you see the model list, Tailscale is working!

### Step B8: Configure Nanobot on the VPS

**On the VPS:**

```bash
# Set up config directory:
mkdir -p ~/.nanobot

# Create config file:
nano ~/.nanobot/config.json
```

Paste this template, then customize:

```json
{
  "gateway": {
    "listen_address": "0.0.0.0",
    "listen_port": 8000
  },
  "llm": {
    "provider": "ollama",
    "endpoint": "http://100.123.45.67:11434",
    "model": "mistral"
  },
  "channels": {
    "discord": {
      "enabled": true,
      "token": "YOUR_DISCORD_TOKEN_HERE"
    }
  },
  "obsidian": {
    "enabled": false,
    "vault_path": ""
  }
}
```

**Replace:**
- `100.123.45.67` with your LOCAL machine's Tailscale IP
- `mistral` with whichever model you pulled
- `YOUR_DISCORD_TOKEN_HERE` with your Discord bot token (if using Discord, see Step A6 for how to get this)

**Save the file:**
- In `nano`: Press Ctrl+O, Enter, then Ctrl+X

**Create .env file for secrets:**

```bash
nano ~/.nanobot/.env
```

Add:
```
DISCORD_TOKEN=YOUR_TOKEN_HERE
OLLAMA_ENDPOINT=http://100.123.45.67:11434
```

Save (Ctrl+O, Enter, Ctrl+X).

### Step B9: Set Up Nanobot as a System Service (Optional but Recommended)

This makes nanobot start automatically if the VPS restarts:

**Create a systemd service file:**

```bash
sudo nano /etc/systemd/system/nanobot.service
```

Paste:

```ini
[Unit]
Description=Nanobot Gateway Service
After=network.target

[Service]
Type=simple
User=root
WorkingDirectory=/root
ExecStart=/usr/local/bin/python3.12 -m nanobot gateway
Restart=always
RestartSec=10
StandardOutput=journal
StandardError=journal

[Install]
WantedBy=multi-user.target
```

Save (Ctrl+O, Enter, Ctrl+X).

**Enable the service:**

```bash
sudo systemctl daemon-reload
sudo systemctl enable nanobot
sudo systemctl start nanobot
```

**Check status:**

```bash
sudo systemctl status nanobot
```

✅ Should show "active (running)"

---

# PART 2: STARTUP (Do This After Setup)

Now that everything is configured, here's how to start nanobot.

---

## Path A: Simple Startup (Local + Cloud API)

**Prerequisites:** You have completed all steps in "Path A: Simple Build Setup" above.

**For:** Using local nanobot with OpenRouter, Anthropic, OpenAI, or other cloud LLM provider.  
**Time:** ⏱️ ~5 minutes per startup  

### Pre-Startup Checklist
- ✓ Python 3.11+ installed (`python --version` works)
- ✓ Nanobot installed (`nanobot --version` works)
- ✓ API key stored in `~/.nanobot/.env` 
- ✓ Internet connection working
- ✓ Discord token configured (if using Discord)
- ✓ Obsidian vault path configured (if using)

### Step 1: Verify Connectivity
⏱️ ~1 minute

```bash
ping 8.8.8.8
# Should respond with pings (internet is up)
```
✅ Expected: At least 4 successful ping responses

### Step 2: Start Nanobot
⏱️ ~2 minutes

Open a terminal and run:

```bash
nanobot gateway
```

Expected output:
```
Starting Nanobot Gateway v0.1.4+
LLM Provider: openrouter ✓ Connected
Discord: Connected ✓
Slack: Connected ✓
Ready for messages
```
```bash
nanobot gateway
```

Expected output:
```
Starting Nanobot Gateway v0.1.4+
LLM Provider: openrouter ✓ Connected
Discord: Connected ✓
Slack: Connected ✓
Ready for messages
```

**If the gateway fails to start:**
- ❌ Check `.env` or `config.json` for correct API key format
- ❌ Verify API key hasn't expired in your provider dashboard
- ❌ If key is fresh, wait 1 minute and retry (API sync delay might affect connectivity)
- ❌ Check internet connection: `ping 8.8.8.8` again
- ❌ Verify file permissions: `ls -la ~/.nanobot/` (config files should exist)

### Step 3: Test with Discord/Slack (If Configured)
⏱️ ~2 minutes

If you configured Discord or Slack in setup:

1. Go to any Discord channel (or Slack channel) where the bot is a member
2. Send a test message:
   ```
   @BotName hello
   ```
3. The bot should respond within 5 seconds

✅ Expected: "Hello! How can I help?" or similar response

**If bot doesn't respond:**
- ❌ Verify bot is in the channel (right-click channel → view members)
- ❌ Check bot has required permissions: 
   - `Send Messages`
   - `Read Message History`
   - `Embed Links`
- ❌ Verify Discord token in `.nanobot/.env` matches your bot token (double-check spelling)
- ❌ Check logs: `nanobot logs --last 50` to see specific errors
- ❌ Restart nanobot (Ctrl+C to stop, then `nanobot gateway` to restart)

### Step 4: Test with Obsidian (If Configured)
⏱️ ~1 minute

If you configured Obsidian in setup, test write-back:

In Discord or Slack:
```
@BotName write to Obsidian: "Startup test at {{timestamp}}" to 00-System/startup-log.md
```

Check your Obsidian vault: Look in `00-System/startup-log.md`

✅ Expected: A new entry appears in the file

**If write fails:**
- ❌ Check Obsidian vault path in `~/.nanobot/config.json` is correct
- ❌ Verify vault folder exists on disk
- ❌ Check file permissions: `ls -la ~/path/to/vault/`
- ❌ Verify `00-System/` folder exists in vault (create it if not)

### Validation Gate A: Simple Startup Complete
- ✓ Nanobot gateway starts without errors
- ✓ LLM provider connected (shows in startup message)
- ✓ Bot responds to test message in Discord/Slack (if configured)
- ✓ Obsidian write succeeds (if enabled)

---

## Path B: Advanced Startup (VPS + Local Ollama)

**Prerequisites:** You have completed all steps in "Path B: Advanced Build Setup" above.

**For:** Using VPS-hosted nanobot with local Ollama inference.  
**Architecture:** VPS (gateway) ↔️ Local Ollama (inference) via Tailscale  
**Time:** ⏱️ ~5-10 minutes per startup  

### Pre-Startup Checklist
On your **local machine**:
- ✓ Ollama is installed
- ✓ At least one model is pulled (`ollama list` shows models)
- ✓ Tailscale is installed and connected

On your **VPS**:
- ✓ Nanobot is installed
- ✓ Tailscale is installed and connected
- ✓ Config file points to correct Ollama IP/port
- ✓ Discord token configured (if using Discord)

### Step 1: Start Ollama on Your Local Machine
⏱️ ~1 minute

**On Windows/macOS:**
Ollama likely auto-starts. To verify it's running:
```bash
curl http://localhost:11434/api/tags
# Should return JSON with your models
```

**On Linux:**
```bash
ollama serve
# Keep this terminal open
```

### Step 2: Verify Ollama Accessibility from VPS
⏱️ ~1 minute

**From your VPS** (via SSH), test that it can reach your local Ollama:

```bash
curl http://YOURLOCALTAILSCALEIP:11434/api/tags
# Replace YOURLOCALTAILSCALEIP with your actual local Tailscale IP
# Example: curl http://100.123.45.67:11434/api/tags
```

✅ Expected: Returns JSON list of models

**If this fails:**
- ❌ Check local machine Tailscale is still connected: `tailscale status` on local machine
- ❌ Check local Ollama is still running: `curl http://localhost:11434` on local machine
- ❌ Verify correct IP: On local machine, run `tailscale status` and copy the IP from left column
- ❌ Check firewall: On local machine, make sure port 11434 isn't blocked
- ❌ Restart both Tailscale connections and try again

### Step 3: Start Nanobot on VPS
⏱️ ~2 minutes

**Via SSH on your VPS:**

```bash
nanobot gateway
```

Expected output:
```
Starting Nanobot Gateway v0.1.4+
LLM Provider: ollama ✓ Connected to http://100.123.45.67:11434
Discord: Connected ✓
Slack: Connected ✓
Ready for messages
```

**If nanobot fails to start:**
- ❌ Check config file: `cat ~/.nanobot/config.json` (verify Ollama endpoint is correct)
- ❌ Test Ollama path: `curl http://LOCALIP:11434` from VPS again
- ❌ Check API key in `.env` (if using cloud fallback)
- ❌ Verify permissions: `ls -la ~/.nanobot/`

### Step 4: Test End-to-End Message Flow
⏱️ ~2 minutes

Send a test message from Discord/Slack:

```
@BotName What is 2+2?
```

**Expected flow:**
1. Your chat platform receives the message
2. Nanobot (on VPS) sees it
3. Nanobot forwards to Ollama (on local machine via Tailscale)
4. Ollama generates response
5. Response comes back to Discord/Slack within a few seconds

✅ Expected response: "2+2 equals 4" or similar (answers vary by model)

**If no response:**
- ❌ Check Ollama is still running on local machine
- ❌ Verify Tailscale path to Ollama: `curl http://LOCALIP:11434` from VPS
- ❌ Check VPS logs to see where it fails: On VPS, press Ctrl+C to stop nanobot, then review the logs that print
- ❌ Verify Discord/Slack token in VPS config is correct
- ❌ Test CLIchannel instead: On VPS, can you do `echo "hello" | nanobot agent`?

### Step 5: Verify Systemd Service (If Configured)
⏱️ ~1 minute

If you set up nanobot as a systemd service:

```bash
sudo systemctl status nanobot
```

✅ Expected: Shows "active (running)" with no errors

**If service is dead:**
- ❌ Check why: `sudo journalctl -u nanobot -n 50`
- ❌ Restart: `sudo systemctl restart nanobot`
- ❌ Check status of dependent services: Is Tailscale still running? Is Ollama still running?

### Validation Gate B: Advanced Startup Complete
- ✓ Ollama is running on local machine
- ✓ Ollama is reachable from VPS via Tailscale
- ✓ Nanobot gateway starts on VPS without errors
- ✓ End-to-end message → Ollama → response flow works
- ✓ Discord/Slack bot is online and responding
- ✓ (Optional) Systemd service is running (if configured)

---

## Common Issues & Fixes

| Symptom | Likely Cause | Check | Fix |
|---|---|---|---|
| "Provider disconnected" (Path A) | API key invalid/expired | Check provider dashboard | Rotate/refresh API key, update `.env`, restart nanobot |
| "Cannot reach Ollama" (Path B) | Network/Tailscale issue | `curl http://LOCAL-IP:11434` from VPS | Restart Tailscale on both, verify correct IP, check firewall |
| Bot offline in Discord | Token wrong or permissions missing | Check Discord developer portal | Verify bot token, enable Message Content Intent, check permissions |
| No response to test message | Config issue or provider down | Check logs: `nanobot logs --last 50` | Verify config, check provider status page, restart nanobot |
| VPS can't reach internet | Network configuration | `ping 8.8.8.8` on VPS | Check VPS network settings, verify security group/firewall rules |
| Ollama model slow | GPU not being used | Check Ollama logs | Install GPU drivers, configure GPU in Ollama, restart Ollama |

---

## Startup Monitoring & Health Checks

### Quick Health Check (Path A or B)
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

### Follow Live Logs
```bash
nanobot logs --follow
```

Watch for:
- ✓ Successful provider connections at startup
- ✓ Message ingestion from Discord/Slack
- ✓ Tool execution traces
- ❌ Any errors or warnings (fix these if present)

### Monitor System Resources (VPS or Local Machine)
```bash
top
# Look for nanobot process
# CPU usage should be low at idle (~1-5%)
# Memory usage should be <500MB at idle
```

### Check Service Status (Path B with systemd)
```bash
sudo systemctl status nanobot
# Should show "active (running)"

# View recent logs:
sudo journalctl -u nanobot -n 20

# Restart if needed:
sudo systemctl restart nanobot
```

---

## Graceful Shutdown Procedure

### Simple Shutdown (Path A)

In the terminal where nanobot is running:
```bash
Ctrl+C
# Nanobot will finish current message and shut down gracefully
```

### Advanced Shutdown (Path B - VPS)

**Option 1: Graceful via SSH**
```bash
# SSH into VPS
ssh your-vps-user@your-vps-ip

# Kill nanobot gracefully (if running in terminal)
Ctrl+C

# Or if running as systemd service:
sudo systemctl stop nanobot
```

**Option 2: Restart the service**
```bash
sudo systemctl restart nanobot
```

---

## Post-Startup Maintenance

After first successful startup, monitor for 30 minutes:

**Check for:**
- ✅ No errors in logs
- ✅ Messages are being processed
- ✅ Response times are reasonable (< 30 seconds for cloud, < 10 seconds for local)
- ✅ CPU/memory stay stable (not continuously climbing)
- ✅ Files are being written to Obsidian (if configured)

**Document for future reference:**
- API costs per 1000 tokens (if Path A)
- Model performance characteristics observed
- Any custom channel configurations
- Backup location of `~/.nanobot/config.json` and `.env`

---

## Next Steps

**After successful startup:**

1. **Explore nanobot capabilities:** Try different commands to test what it can do
2. **Configure additional channels:** Add Slack, Telegram, or other platforms
3. **Set up automations:** Create scheduled tasks or workflows
4. **Review governance:** Set up cost limits, approval workflows, audit logging
5. **Integrate with tools:** Connect databases, APIs, or file systems
6. **Customize skills:** Create custom agents or tools for your use case

See the other documentation guides for:
- [LLM Provider Setup Guide](LLM-Provider-Setup-Guide.md) - Multiple model routing
- [Multi-Channel Integration Guide](Multi-Channel-Integration-Guide.md) - Channel policies
- [Tools & Skills Reference](Tools-and-Skills-Reference.md) - What nanobot can do
- [Security Validation Runbook](Security-Validation-Runbook.md) - Monthly security checks
- [Emergency Recovery Guide](Emergency-Recovery-and-Troubleshooting.md) - Troubleshooting common issues

---

## Frequently Asked Questions

**Q: Can I run both Path A and Path B at the same time?**  
A: Yes, they run independently on different machines, so you can have both running simultaneously.

**Q: How do I stop nanobot without losing current work?**  
A: Press Ctrl+C in the terminal. Nanobot will finish processing current messages before shutting down gracefully.

**Q: Can I move my nanobot from Path A to Path B later?**  
A: Yes! Export your config and secrets from Path A, then import them into the VPS in Path B. Both use the same config format.

**Q: What happens to my data when nanobot shuts down?**  
A: All config and state is saved to disk (`~/.nanobot/`). When you restart, everything is restored from disk.

**Q: How do I back up my nanobot configuration?**  
A: Copy the `~/.nanobot/` directory to a safe location. This contains all config, secrets, and logs.

---
# Service should restart cleanly without errors
```

---

## Revision History

| Date | Version | Change |
|---|---|---|
| 2026-02-26 | 2.1.0 | **MAJOR REWRITE:** Complete restructure for brand-new users. Split into PART 1 (Setup) and PART 2 (Startup). Added comprehensive Path A setup sections (Python verification/installation, LLM API key acquisition, nanobot installation, interactive setup wizard, Discord configuration, Obsidian vault setup). Added comprehensive Path B setup sections (VPS provisioning, SSH connection, system updates, Python installation, Ollama installation and configuration, Tailscale setup on both machines with MagicDNS, nanobot configuration on VPS, systemd service setup). Updated startup sections to reflect that setup is complete. Added detailed troubleshooting for all common failure scenarios, health checks, shutdown procedures, post-startup maintenance, next steps, and comprehensive FAQ. Total: 1000+ new lines added for complete coverage. |
| 2026-02-25 | 2.0.0 | Initial version with simple and advanced startup paths |
