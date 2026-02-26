# Multi-Channel Integration Guide

Connect nanobot to Discord, Slack, Telegram, Feishu, or other chat platforms.

## Document Control
- **Owner:**
- **Version:** 1.0.0
- **Last Updated:** 2026-02-25
- **Status:** Active

## 1. Overview

Nanobot works across 12+ chat platforms with unified governance. Same policy, security model, and memory apply regardless of which channel operators use.

**Supported Channels:**
Discord, Slack, Telegram, Feishu, DingTalk, WhatsApp, Email, QQ, Matrix/Element, Mochat

## 2. Discord Setup (5 minutes)

### Step 1: Create Bot in Developer Portal
1. Visit https://discord.com/developers/applications
2. Click **New Application**
3. Name: `My Nanobot` (or your preference)
4. Go to **Bot** tab → **Add Bot**
5. Copy bot **TOKEN** (keep secret!)

### Step  2: Configure Bot Settings
In Discord Developer Portal, under **Bot**:
- Toggle **Message Content Intent** ON (required for reading messages)
- Permissions: `Send Messages`, `Read Message History` (minimal)
- Intents: `Message Content`, `Direct Messages`

### Step 3: Invite Bot to Server
1. Go to **OAuth2** → **URL Generator**
2. Select Scopes: `bot`
3. Select Permissions: `Send Messages`, `Read Message History`
4. Copy generated URL
5. Open URL, select your Discord server, authorize

### Step 4: Configure nanobot
Add to `~/.nanobot/config.json`:
```json
{
  "channels": {
    "discord": {
      "enabled": true,
      "token": "YOUR_BOT_TOKEN",
      "allowFrom": ["YOUR_USER_ID_HERE"]
    }
  }
}
```

Get your Discord User ID: In Discord, enable Developer Mode (Settings → Advanced), right-click your name, **Copy User ID**.

### Step 5: Run nanobot
```bash
nanobot gateway
```

Should show: `Discord: Connected`

### Step 6: Test
Type in any channel the bot is in: `@MyNanobot hello`

Bot should respond.

## 3. Slack Setup (5 minutes)

### Step 1: Create App
1. Visit https://api.slack.com/apps
2. **Create New App** → **From scratch**
3. App Name: `Nanobot`
4. Pick your workspace

### Step 2: Configure Permissions
In **OAuth & Permissions**:
- **Scopes**:
  - `chat:write` (send messages)
  - `channels:read` (read channel list)
  - `users:read` (read user info)
  - `files:read` (read files)

### Step 3: Install App
Click **Install to Workspace** → Authorize

Copy **Bot User OAuth Token** (starts with `xoxb-`)

### Step 4: Configure nanobot
```json
{
  "channels": {
    "slack": {
      "enabled": true,
      "botToken": "xoxb-YOUR_TOKEN",
      "appToken": "xapp-YOUR_APP_TOKEN",
      "allowFrom": []
    }
  }
}
```

Get App Token: **App Level Tokens** tab, create token with `connections:write` scope.

### Step 5: Run & Test
```bash
nanobot gateway
```

In a Slack channel, mention the bot: `@Nanobot hello`

## 4. Telegram Setup (5 minutes)

### Step 1: Create Bot
1. Open Telegram, search `@BotFather`
2. Send `/newbot`
3. Follow prompts, get **TOKEN**

### Step 2: Configure nanobot
```json
{
  "channels": {
    "telegram": {
      "enabled": true,
      "token": "YOUR_BOT_TOKEN",
      "allowFrom": ["YOUR_USER_ID"]
    }
  }
}
```

Get your Telegram User ID: Search `@userinfobot`, send `/start`, note your ID.

### Step 3: Run & Test
```bash
nanobot gateway
```

Message your bot on Telegram.

## 5. Feishu Setup (5 minutes)

Feishu (飞书) is popular in China. Uses WebSocket—no public IP needed!

### Step 1: Create Bot App
1. Visit https://open.feishu.cn/app
2. **Create App** → **Bot**
3. Get **App ID** and **App Secret**

### Step 2: Configure Permissions
- Permissions: `im:message` (send/receive messages)
- Events: `im.message.receive_v1` (Long Connection mode recommended)

### Step 3: Configure nanobot
```json
{
  "channels": {
    "feishu": {
      "enabled": true,
      "appId": "cli_...",
      "appSecret": "...",
      "allowFrom": []
    }
  }
}
```

### Step 4: Run
```bash
nanobot gateway
```

Long Connection mode: Bot establishes WebSocket to Feishu, receives events immediately. No webhook/port forwarding needed!

## 6. Multi-Channel AOS Configuration

Enable multiple channels simultaneously with unified allowlist:

```json
{
  "channels": {
    "discord": {
      "enabled": true,
      "token": "...",
      "allowFrom": ["YOUR_DISCORD_ID"]
    },
    "slack": {
      "enabled": true,
      "botToken": "...",
      "allowFrom": []
    },
    "telegram": {
      "enabled": true,
      "token": "...",
      "allowFrom": ["YOUR_TELEGRAM_ID"]
    }
  }
}
```

Same sessions, memory, and tools work across all channels!

## 7. Channel-Level Access Control

Restrict certain channels to sensitive tools:

```json
{
  "channels": {
    "discord": {
      "allowed_tools": {
        "#ctl-nanobot-commands": ["shell", "mcp", "github"],
        "#prd-*": ["web", "file", "message"],
        "#bk-*": ["file", "message"],
        "default": ["web", "file", "message"]
      }
    }
  }
}
```

## 8. Multi-Channel Naming Convention

For consistency across platforms, use same channel naming:

| Workspace Domain | Discord | Slack | Telegram | Feishu |
|---|---|---|---|---|
| Product | `#prd-widget-marketing` | `#prd-widget-marketing` | `Prd Widget Marketing` | **Product** → **Widget Marketing** |
| Bookkeeping | `#bk-proj-reconcile` | `#bk-proj-reconcile` | `Bk Proj Reconcile` | **Bookkeeping** → **Proj Reconcile** |
| Research | `#res-ai-synthesis` | `#res-ai-synthesis` | `Res AI Synthesis` | **Research** → **AI Synthesis** |

Operators recognize the same workflow context across platforms.

## 9. Testing Channel Connectivity

```bash
nanobot doctor
```

Output shows which channels are connected:
```
Discord: Connected ✓
Slack: Connected ✓
Telegram: Connected ✓
Feishu: Connected ✓
```

## 10. Troubleshooting

| Symptom | Fix |
|---|---|
| Bot offline in one channel | Check token/credentials and re-authenticate |
| Bot doesn't respond to mentions | Verify Message Content Intent (Discord) or permissions (Slack) |
| Rate limited in Slack | Upgrade Slack plan or reduce message frequency |
| Feishu not receiving messages | Check Long Connection mode is enabled in Feishu app settings |

## 11. Security Best Practices

- **Never commit tokens** to version control; use `.env` files
- **Rotate tokens periodically** (quarterly minimum)
- **Use allowlists** to restrict who can invoke commands
- **Log all interactions** via Obsidian for audit
- **Monitor rate limits** to catch abuse early

## 12. Revision History

| Date | Version | Change |
|---|---|---|
| 2026-02-25 | 1.0.0 | Initial guide covering Discord, Slack, Telegram, Feishu; multi-channel setup; AOS naming conventions |
