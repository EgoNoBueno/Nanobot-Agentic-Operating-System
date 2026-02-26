# LLM Provider Setup Guide

Configure nanobot to use different AI (artificial intelligence) models based on workflow type, cost, and capability requirements.

## Document Control
- **Owner:**
- **Version:** 1.0.0
- **Last Updated:** 2026-02-25
- **Status:** Active

## 1. Overview

Nanobot supports 100+ LLM (Large Language Model) models via unified configuration. Choose your provider based on:
- **Cost** - Qwen, DeepSeek cheaper; Opus more expensive but capable
- **Latency** - Local faster; cloud slightly slower
- **Capability** - Opus best for complex reasoning; Haiku fastest
- **Data residency** - Local Ollama for sensitive data

## 2. Supported Providers

| Provider | Models | Setup Difficulty | Cost | Latency |
|---|---|---|---|---|
| **OpenRouter** (Recommended) | 100+ (Claude, GPT, Qwen, etc.) | ⭐ Easy | $1-5/M | Medium |
| **Anthropic Direct** | Claude Opus, Sonnet, Haiku | ⭐ Easy | $3-15/M | Medium |
| **OpenAI Direct** | GPT-4, GPT-4o, GPT-3.5 | ⭐ Easy | $5-30/M | Fast |
| **Local (Ollama)** | Llama, Mistral, etc. | ⭐⭐ Moderate | $0 | Fast |
| **DeepSeek** | DeepSeek-Chat, Code | ⭐ Easy | $0.14/M | Medium |
| **Qwen (Alibaba)** | Qwen-Plus, -Turbo | ⭐ Easy | $0.008/M | Medium |
| **Gemini (Google)** | Gemini Pro, Ultra | ⭐ Easy | $0.5-10/M | Medium |
| **Cohere** | Command R, R+ | ⭐ Easy | $0.5-3/M | Medium |

## 3. Quick Setup (5 minutes)

### Option A: OpenRouter (All-in-One)
⏱️ ~5 minutes to complete  
OpenRouter gives you access to 100+ models with one API key. (An API key is like a password that lets nanobot connect to OpenRouter's servers.)

#### Before You Start
- ☐ You have a credit card (free tier gives small free allocation)
- ☐ Internet connection working
- ☐ Text editor to edit config file
- ☐ Terminal/shell access to nanobot

**Step 1: Get API Key**
⏱️ ~2 minutes

- Visit https://openrouter.ai/keys
- Sign up or log in
- Copy your API key (this is your unique password—keep it secret!)

**Step 2: Configure**
⏱️ ~1 minute

```json
{
  "providers": {
    "openrouter": {
      "apiKey": "sk-or-v1-YOUR_KEY_HERE"
    }
  },
  "agents": {
    "defaults": {
      "model": "anthropic/claude-opus-4-5",
      "provider": "openrouter"
    }
  }
}
```
Save to `~/.nanobot/config.json` (or set `OPENROUTER_API_KEY` environment variable—this is a way to store the API key so programs can read it)

**Step 3: Test**
```bash
# Start nanobot interactive mode and ask it a question
nanobot agent
ask a question
```

### Option B: Local Ollama
⏱️ ~10 minutes to complete  
Perfect for sensitive data, no API costs. (You run the AI on your own computer instead of using a cloud service.)

#### Before You Start
- ☐ Ollama installed on your local machine
- ☐ GPU or CPU with at least 4GB RAM (8GB recommended)
- ☐ 10GB+ disk space for models
- ☐ Network access from VPS to local machine

**Step 1: Install Ollama**
⏱️ ~2 minutes

- Download from https://ollama.ai
- Install on your machine

**Step 2: Pull a Model**
⏱️ ~2 minutes

```bash
# 'ollama pull' downloads an AI model to your machine (like downloading a file)
# 'mistral' is the name of the model
ollama pull mistral
```
✅ Expected: "pulling manifest" then "success"

**Step 3: Configure nanobot**
⏱️ ~1 minute**
```json
{
  "providers": {
    "ollama": {
      "baseUrl": "http://localhost:11434"
    }
  },
  "agents": {
    "defaults": {
      "model": "mistral",
      "provider": "ollama"
    }
  }
}
```

**Step 4: Start Ollama**
```bash
# ollama serve starts the AI server on your machine
# Keep this running in a terminal (it will listen for nanobot's requests)
ollama serve
```
(Keep this running in background)

**Step 5: Test**
```bash
nanobot agent
ask a question
```

### Option C: Direct Anthropic (Claude)
Best if using Claude exclusively.

**Step 1: Get API Key**
- Visit https://console.anthropic.com
- Create API key

**Step 2: Configure**
```json
{
  "providers": {
    "anthropic": {
      "apiKey": "sk-ant-YOUR_KEY_HERE"
    }
  },
  "agents": {
    "defaults": {
      "model": "claude-opus-4-5",
      "provider": "anthropic"
    }
  }
}
```

## 4. Multi-Provider Setup (Cost Optimization)

Use different models for different workflows:

```json
{
  "providers": {
    "openrouter": {
      "apiKey": "sk-or-v1-..."
    }
  },
  "agents": {
    "default": {
      "model": "openai/gpt-4-turbo",
      "provider": "openrouter"
    },
    "research": {
      "model": "anthropic/claude-opus-4-5",
      "provider": "openrouter"
    },
    "bookkeeping": {
      "model": "qwen/qwen-plus",
      "provider": "openrouter"
    },
    "fast-response": {
      "model": "anthropic/claude-haiku",
      "provider": "openrouter"
    }
  }
}
```

Then in Discord/Slack policy: Route `#bk-*` to bookkeeping agent, `#res-*` to research agent, etc.

## 5. Token Budgeting & Cost Control

Configure per-request and per-agent limits. (Tokens = words, roughly. This controls how long responses can be.)

```json
{
  "agents": {
    "defaults": {
      "maxInputTokens": 8000,
      "maxOutputTokens": 4096,
      "temperature": 0.1,
      "maxIterations": 40
    },
    "research": {
      "maxInputTokens": 32000,
      "maxOutputTokens": 8192,
      "temperature": 0.3,
      "maxIterations": 40
    }
  }
}
```

**What each means:**
- **maxInputTokens** = Maximum words/context to send to AI (8000 ≈ 6,000 words)
- **maxOutputTokens** = Maximum words in response (4096 ≈ 3,000 words)
- **temperature** = How "creative" the AI is (0.1 = logical, 1.0 = random)
- **maxIterations** = How many times AI can self-correct (higher = more thinking, more cost)

## 6. Prompt Caching (Cost Optimization)

Anthropic Claude supports prompt caching. (Caching = saving frequently-used context so you don't re-send it every time. This saves money!) Enable to reduce costs on repeated queries:

```json
{
  "providers": {
    "anthropic": {
      "apiKey": "sk-ant-...",
      "cacheControl": true
    }
  }
}
```

This re-uses cached prompts for 5 minutes, reducing cost by ~10%.

## 7. Environment Variable Override

Set a model for a single command:
```bash
NANOBOT_MODEL=openai/gpt-4 nanobot agent
```

## 8. Web Search API (Brave)

To enable web search, add Brave API key:

```json
{
  "braveApiKey": "YOUR_BRAVE_API_KEY"
}
```

Get key from https://api.search.brave.com/

## 9. Fallback & Degraded Mode

If primary provider fails, fall back to secondary:

```json
{
  "agents": {
    "defaults": {
      "fallbackChain": [
        "openrouter",
        "anthropic",
        "ollama"
      ]
    }
  }
}
```

Nanobot tries first provider, falls back to next on error.

## 10. Testing & Troubleshooting

**Test provider connection:**
```bash
nanobot doctor
```

Expected output:
```
Provider: openrouter ✓ Connected
Model: claude-opus-4-5 ✓ Available
Tokens available: Yes ✓
```

| Issue | Check |
|---|---|
| `401 Unauthorized` | Verify API key format and validity |
| `Model not found` | Confirm model name is correct for provider |
| `Rate limited` | Wait or upgrade API plan |
| `Timeout` | Check network connection or try different model |

## 10. Common Mistakes & Solutions

### ❌ Mistake 1: API Key Has Extra Spaces
**Problem:** `401 Unauthorized` or `Invalid API key`  
**Why:** Copy/paste sometimes includes leading/trailing whitespace  
**Fix:**
```bash
# Double-check the key has no spaces
echo "YOUR_API_KEY" | wc -c  # Count characters including any spaces
# Paste fresh key, remove any spaces, then test
nanobot doctor
```

### ❌ Mistake 2: Wrong Model Name for Provider
**Problem:** `Model not found` or `Unknown model`  
**Why:** Each provider uses different model naming (Claude 3 vs claude-3-opus vs claude-opus-4-5)  
**Fix:**
1. Check provider documentation for exact model names
2. For OpenRouter, browse https://openrouter.ai/models
3. For Ollama, run `ollama list` to see available models
4. Update config with correct name

### ❌ Mistake 3: Temperature & Token Settings Too High
**Problem:** Very expensive bills, slow responses, incoherent output  
**Why:** High temperature (>0.8) makes AI more random; large token limits are expensive  
**Fix:**
```json
{
  "agents": {
    "defaults": {
      "temperature": 0.1,        // Use 0.1-0.3 for factual tasks
      "maxOutputTokens": 1000    // Keep reasonable (500-2000)
    }
  }
}
```

### ❌ Mistake 4: Forgetting to Start Ollama (Local)
**Problem:** Nanobot runs but says "Ollama unreachable"  
**Why:** Ollama must be running as a background service  
**Fix:**
```bash
# Start Ollama in a new terminal
ollama serve

# In another terminal, verify it's running
curl http://localhost:11434/api/tags
# Should show your models
```

### ❌ Mistake 5: Setting Up Fallback Chain Backwards
**Problem:** Always uses expensive model even though cheap one is available  
**Why:** Fallback order matters; nanobot tries providers in config order  
**Fix:** Put cheap models first:
```json
{
  "fallbackProviders": [
    "openrouter:qwen",       // Try cheap first
    "openrouter:claude-opus" // Fall back to expensive
  ]
}
```

## 11. AOS Integration

Map channels to cost-tier models:

- `#bk-*` → Qwen (cheapest, bookkeeping is lower-risk)
- `#prd-*-analytics` → Claude Haiku (fast, moderate cost)
- `#prd-*-marketing` → Claude Opus (high quality, moderate cost)
- `#res-*` → Claude Opus (complex synthesis)
- `#res-* (escalated)` → Claude Opus (no cost cap)

Implement this via LLM policy document governing which agent routes to which provider.

## 12. Revision History

| Date | Version | Change |
|---|---|---|
| 2026-02-25 | 1.0.0 | Initial guide covering 8 major providers, multi-provider setup, cost controls, and caching |
