# AOS Startup Procedure: Advanced Build (VPS + Local Ollama)

**Robust deployment:** Run nanobot gateway on a rented cloud server with AI inference on your local computer. For privacy, self-hosting, and 24/7 operation.

## Document Control
- **Owner:**
- **Version:** 2.4.0
- **Last Updated:** 2026-02-26
- **Status:** Active
- **Applies To:** Path B (Advanced Build) only
- **Estimated Setup Time:** 60-90 minutes
- **Estimated Startup Time:** 10 minutes
- **Architecture:** VPS (gateway) ↔ Local Ollama (inference) via Tailscale tunnel

## For Complete Beginners: Advanced Build Overview

**What is Advanced Build?**
- You rent a small cloud server (called a VPS—Virtual Private Server)
- Nanobot gateway runs on that rented server
- The AI "brain" (Ollama) runs on your personal computer at home
- Your server and home computer talk to each other securely through Tailscale (an encrypted tunnel)
- You pay a monthly fee for the rented server (~$5-10/month) but NO per-query AI costs

**Why would you choose this?**
- ✅ You need 24/7 operation (the server is always on)
- ✅ You want zero cloud AI costs (run your own free AI locally)
- ✅ You want maximum privacy (AI inference stays on your computer)
- ✅ You're building a production system
- ✅ You're willing to spend 60-90 minutes setting up

**Who should NOT choose this?**
- ❌ You want the quickest possible setup (Simple Build is 25 minutes)
- ❌ You're just trying nanobot for the first time (start with Simple Build)
- ❌ You don't have a spare computer to run Ollama 24/7
- ❌ You're not comfortable with terminals and server concepts
- → For those cases, see [AOS-Startup-Simple-Build](AOS-Startup-Simple-Build.md)

---

## Cost Expectations

**To get started:**
- $5-10 for first month of VPS service
- $0 for everything else (Ollama is free, Tailscale is free for personal use)

**Per month (ongoing):**
- **VPS:** $5-10/month (whether you use it or not)
- **AI inference:** $0 (Ollama runs on your computer for free)
- **Electricity:** ~$10-20/month to run your home computer with Ollama 24/7

**Total:** ~$15-30/month (vs $0-100/month for Simple Build, depending on cloud API usage)

**Break-even calculation:** If you use nanobot heavily and pay $0.01 per 1000 tokens on cloud APIs, you'd pay $5-30/month for Simple Build. Advanced Build at $15-30/month becomes cheaper after 2-3 months of heavy use.

---

## Architecture Overview

```
┌─────────────────────────────────────────────────────────────────┐
│ Your Home/Office Network                                        │
├─────────────────────────────────────────────────────────────────┤
│ ┌────────────────┐              ┌──────────────────────────────┐ │
│ │  Your Discord  │              │  Your Computer               │ │
│ │  Slack Channel │              │  (Running Ollama)            │ │
│ │  etc.          │              │                              │ │
│ └────────┬───────┘              │  ┌──────────────────────┐    │ │
│          │ Messages             │  │  Ollama AI Inference │    │ │
│          │                      │  │  (Free, local)       │    │ │
└──────────┼──────────────────────│──┼─ Secure Tailscale ──┼────┘ │
           │                      │  │ Encrypted Tunnel    │      │
           │ ┌──────────────────┐ │  └──────────────────────┘    │
           └─┤ Tailscale Tunnel │ │                              │
             │ (Encrypted)      │ │                              │
             └────────┬─────────┘ └──────────────────────────────┘
                      │
           ┌──────────┴──────────┐
           │ Cloud VPS Server    │
           │ (Rented from        │
           │  DigitalOcean,      │  ┌─────────────────────────┐
           │  Linode, etc.)      │  │ Nanobot Gateway         │
           │                     │  │ Listening for messages  │
           │ Ubuntu 22.04        │  │ from Discord/Slack/etc. │
           │                     │  └─────────────────────────┘
           │                     │
           └─────────────────────┘
```

**How it works:**
1. You send a message in Discord/Slack
2. Nanobot gateway (on VPS) receives it
3. Gateway sends request to your local Ollama (via secure Tailscale tunnel)
4. Ollama generates response on your computer
5. Response goes back to VPS gateway
6. Gateway posts response back to Discord/Slack

---

# PART 1: SETUP (Do This First)

This section covers all prerequisites. Setting up Advanced Build takes about 60-90 minutes because you're configuring two machines (VPS + local).

---

## Step B1: Rent a Virtual Private Server (VPS)

A VPS is a rented computer in a data center. You control it via internet. Think of it like renting office space: you get your own space that you control.

### Choose a VPS Provider

Popular options (all offer similar features):

| Provider | Cost | Region | Best For |
|---|---|---|---|
| **DigitalOcean** | $5-6/month | Multiple (US, Europe, Asia) | Beginners (very easy interface) |
| **Linode** | $5-10/month | Multiple (US, Europe, Asia) | Reliability, same company as Akamai |
| **Hetzner** | €3-5/month | Europe | Europe-based, very cheap |
| **AWS EC2** | Pay-as-you-go | Everywhere | Complex, requires AWS account |

**Recommendation for beginners:** DigitalOcean. It has the easiest interface.

### Create a VPS with These Specs

**On your chosen provider:**

1. Click "Create" or "New Droplet" or "New Instance"
2. Select these settings:
   - **Operating System:** Ubuntu 22.04 LTS or newer (NOT Windows)
   - **CPU:** 1-2 vCPUs (virtual cores). One vCPU is one processor core on the rented server. For nanobot, 1 is enough; 2 is safer if you run other things too.
   - **RAM:** 1-2 GB (RAM is fast temporary memory; 2 GB gives more breathing room)
   - **Storage:** 10-20 GB SSD (storage to save files; nanobot logs take minimal space, so 10-20 is plenty)
   - **Region:** Choose closest to you (lower latency = faster responses)
   - **Authentication:** SSH Key (if you know what that is) OR Password (easier for beginners)

3. Click "Create" and wait 1-2 minutes for it to boot up

### What You Get After Creation

Your provider will email you (or show on screen):
- **VPS IP Address** (e.g., `203.0.113.10`)
- **Root Password** OR SSH Key (for access)
- **How to connect** (SSH command)

**Save these securely.**

---

## Step B2: Connect to Your VPS via SSH

**SSH** = Secure Shell, a secure way to remotely log into and control a server (your VPS) from your computer. It's like remote desktop but for servers, and it's encrypted (secure) so hackers can't see your commands.

### On Windows

1. Download PuTTY: https://www.putty.org/ (a program for SSH—remote terminal access)
2. Launch PuTTY
3. Under "Host Name," enter your VPS IP (e.g., `203.0.113.10`)
4. Click "Open"
5. When prompted:
   - Username: `root`
   - Password: Paste the VPS root password your provider gave you
6. ⚠️ **Important:** When pasting password, **nothing appears on screen** (no dots, no asterisks). This is normal. Just paste and press Enter.

✅ You should see a terminal prompt like: `root@ubuntu-server:~#`

### On macOS/Linux

Open Terminal and type:

```bash
ssh root@YOUR_VPS_IP
# ssh connects to your VPS server remotely
# root is the administrator account
# YOUR_VPS_IP is your VPS's IP address (e.g., ssh root@203.0.113.10)
```

When prompted for password, paste it (nothing appears—this is normal). Press Enter.

✅ You should see: `root@ubuntu-server:~#`

**Tip:** After connecting, you're typing commands on the VPS server, not your local computer. Everything you type runs on the VPS.

---

## Step B3: Update the VPS System

Always update packages on a new VPS (security patches):

```bash
apt update
apt upgrade -y
```

This takes 1-2 minutes. Let it run.

---

## Step B4: Install Python 3.11+ on the VPS

```bash
apt install -y python3.12 python3.12-venv python3-pip
```

Verify:
```bash
python3.12 --version
```

✅ Should print: `Python 3.12.X`

---

## Step B5: Install Nanobot on the VPS

```bash
pip install nanobot-ai
```

Verify:
```bash
nanobot --version
```

✅ Should print: `🐈 nanobot v0.1.4.post2` or similar (exact version may differ)

---

## Step B6: Prepare Ollama on Your Local Machine

**IMPORTANT:** This step happens on YOUR HOME COMPUTER, not the VPS. Close your VPS connection or open a new terminal.

### Install Ollama Locally

**On Windows:**
1. Go to https://ollama.ai
2. Click "Download for Windows"
3. Run the installer, click "Install"
4. Ollama starts automatically

**On macOS:**
1. Go to https://ollama.ai
2. Click "Download for macOS"
3. Run the installer (drag to Applications)
4. Launch from Applications, or it might start automatically

**On Linux:**
```bash
curl -fsSL https://ollama.ai/install.sh | sh
# After installation, start Ollama:
ollama serve
```

### Verify Ollama is Running

```bash
curl http://localhost:11434/api/tags
# Should return JSON with list of models (initially empty)
```

✅ You should see: `{"models":[]}`

### Pull an AI Model

Models are AI engines. Pick one based on disk space and internet speed:

**Quick & small (good for testing):**
```bash
ollama pull mistral
# ~7 GB download, ~3-5 minutes on typical home internet
```

**Better quality (medium size):**
```bash
ollama pull neural-chat
# ~10 GB download, ~5-10 minutes
```

**Large but powerful (requires 20+ GB free disk space):**
```bash
ollama pull dolphin-mixtral
# ~26 GB download, ~15-30 minutes or longer
```

**Important:** Check your hard drive has enough free space. Download time depends on your internet speed—pulling models on slow internet can take 30+ minutes. **Do not close your terminal or restart your computer** while a model is downloading. Let it run to completion.

Verify:
```bash
curl http://localhost:11434/api/tags
# Should now list your pulled model(s)
```

✅ You should see your model name in the list.

**Keep Ollama running.** It needs to stay on for the VPS to reach it later.

---

## Step B7: Install and Configure Tailscale on Both Machines

Tailscale creates a secure encrypted tunnel between your VPS and local Ollama machine. It's like a private VPN between just two computers.

### On Your Local Computer

**Windows:**
1. Go to https://tailscale.com/download
2. Download for Windows
3. Run installer, click "Install"
4. When prompted, click "Log in"
5. Browser opens—log in with Google, GitHub, Microsoft, or create Tailscale account
6. Approve the device
7. Close browser and installer
8. Right-click Tailscale icon (system tray, bottom right) → "Copy device IP"
9. **Note this IP** (e.g., `100.123.45.67`)

**macOS:**
1. Go to https://tailscale.com/download
2. Download for macOS
3. Drag to Applications, launch
4. When prompted, click "Log in"
5. Browser opens—log in
6. Approve the device
7. Click Tailscale menu (top right) → "Copy device IP"
8. **Note this IP**

**Linux:**
```bash
curl -fsSL https://tailscale.com/install.sh | sh
tailscale up
# Browser pops up—log in (same account as VPS below)
# Approve the device
# Tailscale prints your IP in terminal
# Note it (e.g., 100.123.45.67)
```

### On Your VPS (via SSH)

SSH back into your VPS (Step B2) and run:

```bash
curl -fsSL https://tailscale.com/install.sh | sh
tailscale up
# Tailscale prints a login URL (looks like: https://login.tailscale.com/a/...)
```

**Copy that URL and paste it in your browser on your LOCAL computer.**

On your browser:
1. Log in with the **SAME Tailscale account** as your local computer
2. Approve the VPS device
3. Your browser redirects—approve/confirm
4. Back in your SSH session, Tailscale prints your VPS Tailscale IP (e.g., `100.87.123.45`)

**Note this VPS IP.**

### Enable MagicDNS (So You Can Use Device Names)

1. Open https://login.tailscale.com/admin/dns
2. Scroll to "MagicDNS"
3. If not already enabled, click "Enable"

This lets you use device names instead of IPs (optional but convenient).

### Test the Tailscale Connection

From your VPS SSH session, verify it can reach your local Ollama:

```bash
curl http://100.123.45.67:11434/api/tags
# Replace 100.123.45.67 with YOUR LOCAL MACHINE'S Tailscale IP
# (the one you noted above)
```

✅ Should return JSON list of models (the model you pulled in Step B6)

**If this fails:**
- On local computer: Check Tailscale is still running (icon in system tray should show)
- Check Ollama is still running: `curl http://localhost:11434` on local computer
- Verify correct IP: On local computer, check Tailscale icon and copy the IP again
- Check firewall: Local computer might be blocking port 11434. This is a network security issue—you may need to open the port in your firewall settings.

---

## Step B8: Configure Nanobot on the VPS

**Back in your VPS SSH session:**

### Create Config Directory

```bash
mkdir -p ~/.nanobot
```

### Create Configuration File

```bash
nano ~/.nanobot/config.json
```

This opens a text editor. Paste this template:

```json
{
  "agents": {
    "defaults": {
      "model": "mistral",
      "provider": "custom"
    }
  },
  "providers": {
    "custom": {
      "apiBase": "http://100.123.45.67:11434/v1",
      "apiKey": "ollama"
    }
  },
  "channels": {
    "discord": {
      "enabled": false,
      "token": ""
    }
  }
}
```

**Replace these values:**
- `100.123.45.67` → **Your LOCAL machine's Tailscale IP** (from Step B7). Example: If your local Tailscale IP is `100.45.67.89`, change that line to `"apiBase": "http://100.45.67.89:11434/v1",`
- `mistral` → **Your model name** (the one you pulled in Step B6). Example: If you pulled `neural-chat`, change `"model": "mistral"` to `"model": "neural-chat"`
- Note the `/v1` at the end of the URL — Ollama exposes an OpenAI-compatible API at this path; do not omit it
- If using Discord, set `"enabled": true` and add your bot token (see the [channel integration guide](../nanobot/README.md))

**To save the file:**
- Press `Ctrl+O`
- Press `Enter`
- Press `Ctrl+X`

---

## Step B9 (Optional): Set Up Nanobot as a System Service

This makes nanobot start automatically if the VPS restarts.

### Create Service File

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
ExecStart=/usr/bin/python3.12 -m nanobot gateway
Restart=always
RestartSec=10
StandardOutput=journal
StandardError=journal

[Install]
WantedBy=multi-user.target
```

Save (Ctrl+O, Enter, Ctrl+X).

### Enable and Start the Service

```bash
sudo systemctl daemon-reload
sudo systemctl enable nanobot
sudo systemctl start nanobot
```

### Check Status

```bash
sudo systemctl status nanobot
```

✅ Should show: `active (running)`

---

# PART 2: STARTUP (Do This After Setup)

Now that everything is configured, here's how to start nanobot.

---

## Pre-Startup Checklist

**On your LOCAL computer:**
- ✅ Ollama is installed and running
- ✅ At least one model is pulled (`ollama list` shows models)
- ✅ Tailscale is installed and connected

**On your VPS:**
- ✅ Nanobot is installed
- ✅ Tailscale is installed and connected
- ✅ `~/.nanobot/config.json` has correct `providers.custom.apiBase` (Tailscale IP + port + `/v1`)
- ✅ Ollama endpoint accessible from VPS (Step 2 curl test passed)

---

## Step 1: Verify Ollama is Running on Your Local Computer

**On your local computer:**

```bash
curl http://localhost:11434/api/tags
# Should return your Ollama model list
```

✅ Should show: `{"models": [{"name": "mistral:latest", ...}]}`

If Ollama isn't running:
- **Windows/macOS:** Click Ollama icon to start it
- **Linux:** In a terminal, run `ollama serve`

---

## Step 2: Verify Ollama Accessibility from VPS

**In your VPS SSH session** (reconnect if needed):

```bash
curl http://100.123.45.67:11434/api/tags
# Replace 100.123.45.67 with YOUR LOCAL Tailscale IP
```

✅ Should return your model list

**If this fails:**
- Check Tailscale connection: `tailscale status` (on VPS)
- Check local machine Tailscale is running (system tray)
- Verify correct Tailscale IP (re-check on local computer)
- Restart both Tailscale connections

---

## Step 3: Start Nanobot on VPS

**In your VPS SSH session:**

### If You Set Up Systemd Service (Step B9)

```bash
sudo systemctl status nanobot
```

✅ Should show: `active (running)`

Nanobot is already running in the background!

### Or If Running Nanobot Directly

```bash
nanobot gateway
```

✅ Expected output:
```
🐈 Starting nanobot gateway on port 18790...
Warning: No channels enabled
✓ Heartbeat: every 1800s
```

(If you've enabled a channel like Discord, you'll see `✓ Channels enabled: discord` instead of the warning.)

Keep this terminal open—nanobot is running.

---

## Step 4: Test End-to-End Message Flow

**In your VPS SSH session** (reconnect if needed — see Step B2):

```bash
nanobot agent
```

You'll see:
```
🐈 Interactive mode (type exit or Ctrl+C to quit)

You: 
```

Type a test question:
```
You: What is 2+2?
```

✅ **Expected flow:**
1. Your message enters nanobot agent on the VPS
2. Agent calls Ollama via the Tailscale tunnel to your local computer
3. Ollama generates a response
4. Response is printed in your SSH session

You should see:
```
🐈 nanobot

2 + 2 equals 4.
```

**If you get no response or an error:**
- Check that Ollama is still running on your local computer
- Re-run the Tailscale connectivity test from Step 2 above
- Verify the `apiBase` URL (with `/v1`) in `~/.nanobot/config.json`

To exit:
```
exit
```

---

## Step 5: Test with Discord/Slack (Optional)

If you configured Discord/Slack in the config:

1. Send message to your Discord/Slack channel: `@BotName hello`
2. Bot should respond within 5 seconds

✅ Expected: Bot responds

---

## Validation: Advanced Startup Complete ✅

- ✅ Ollama running on local computer
- ✅ Ollama reachable from VPS via Tailscale (`curl` test succeeds)
- ✅ Nanobot gateway running on VPS (systemd service OR terminal window shows "Ready")
- ✅ End-to-end test succeeds (VPS gateway reaches Ollama, generates response)
- ✅ Optional: Discord/Slack responds

**Congratulations!** You have a working Advanced Build system.

---

## Cost & Performance Reference

| Component | Setup Cost | Ongoing Cost/Month | Performance | Best For |
|---|---|---|---|---|
| **VPS (gateway)** | $0 | $5–20 | Constant uptime | 24/7 operations |
| **Local Ollama (inference)** | GPU or CPU | $0 | 2–10s latency | Privacy-first, self-hosted |
| **Tailscale tunnel** | $0 | $0 (free tier) | <100ms latency | Secure remote connection |
| **Total (Advanced Build)** | Initial VPS setup | $5–20/month + local electricity (~$10–20) | 3–15s per query | Production, privacy, 24/7 |

**Comparison to Simple Build:** Simple Build costs $0.01–0.10 per message but requires manual operation. Advanced Build has a fixed monthly cost but runs 24/7 without intervention.

---

## Shutdown Procedure

### Stop Ollama (Local Computer)

Press `Ctrl+C` in your Ollama terminal (if running `ollama serve`).

Or close the Ollama application.

### Stop Nanobot (VPS)

**If using systemd service:**
```bash
sudo systemctl stop nanobot
```

**If running in terminal:**
```
Press Ctrl+C
```

---

## Health Monitoring

### Check Nanobot Service Status

```bash
sudo systemctl status nanobot
```

### View Recent Logs

```bash
sudo journalctl -u nanobot -n 50
# Shows last 50 log lines
```

### Restart Nanobot Service

```bash
sudo systemctl restart nanobot
```

---

## Still Completely Stuck? Troubleshooting

### Ollama Unreachable from VPS

```bash
# From VPS, test Ollama IP:
ping 100.123.45.67
# If "unreachable," Tailscale connection is broken

# Try reconnecting Tailscale on local computer:
# Close and reopen Tailscale app, check status
```

### Nanobot Won't Start

```bash
# Check error logs:
sudo journalctl -u nanobot -n 100

# Check config syntax:
cat ~/.nanobot/config.json
# Make sure JSON is valid (no missing commas, quotes)

# Restart:
sudo systemctl restart nanobot
```

### Gateway Can't Reach Ollama

```bash
# From VPS, test Ollama connection:
curl -v http://100.123.45.67:11434/api/tags

# If fails, check:
# 1. Your local Tailscale IP is correct in config
# 2. Ollama is still running on local computer
# 3. Local computer Tailscale is connected
```

---

## Frequently Asked Questions (Advanced Build)

**Q: Do I need to leave my home computer on 24/7?**  
A: Yes, if you want Ollama available 24/7. If you turn off your home computer, the VPS can't reach Ollama and nanobot will fail. For 24/7 operation without your computer on, add a cloud LLM provider as fallback (see [LLM Provider Setup Guide](LLM-Provider-Setup-Guide.md)).

**Q: What if my internet connection goes down?**  
A: The VPS can't reach your home computer. Nanobot will fail. You can configure a cloud LLM fallback to keep the system online during local internet outages.

**Q: How much internet bandwidth does this use?**  
A: Not much—typically 1-10 MB per day unless you ask very large questions. Tailscale is lightweight and encrypted.

**Q: Can I add Discord or Slack later?**  
A: Yes! Edit your config file, restart nanobot, and follow the [channel integration guide](../nanobot/README.md).

**Q: What if I want to go back to Simple Build?**  
A: You can export your config from the VPS, set up Advanced Build→Simple Build migration, and paste config into your local computer. See [LLM Provider Setup Guide](LLM-Provider-Setup-Guide.md) for multi-provider routing.

---

## Revision History

| Date | Version | Change |
|---|---|---|
| 2026-02-26 | 2.4.0 | Accuracy corrections: replaced fabricated config.json schema with correct nanobot structure (agents.defaults.model/provider, providers.custom.apiBase/apiKey). Added /v1 path to Ollama endpoint. Removed non-existent .env file step. Fixed gateway startup output. Replaced fabricated `nanobot agent --gateway` flag + SSH tunnel test with correct `nanobot agent` on VPS. Fixed --version format. |
| 2026-02-26 | 2.3.0 | Fixed broken Multi-Channel-Integration-Guide links → nanobot/README.md; fixed B9 ExecStart Python path to /usr/bin/python3.12; removed duplicate systemd section (wrong module path); fixed PuTTY step-3 numbering |
| 2026-02-26 | 2.2.0 | Split from AOS-Startup-Procedure.md. Focused entirely on Advanced Build (Path B). Added beginner-friendly language, cost expectations, architecture diagram, VPS provider comparison, troubleshooting. Removed all Simple Build content. |
| 2026-02-26 | 2.1.0 | Initial in parent document |
| 2026-02-25 | 2.0.0 | Initial version |
