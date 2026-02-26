# LLM Provider Setup Guide

Configure nanobot to use different AI models based on workflow type, cost, and capability requirements.

## Document Control
- **Owner:**
- **Version:** 1.0.0
- **Last Updated:** 2026-02-25
- **Status:** Active

## 1. Overview

Nanobot supports 100+ LLM models via unified configuration. Choose your provider based on:
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
OpenRouter gives you access to 100+ models with one API key.

**Step 1: Get API Key**
- Visit https://openrouter.ai/keys
- Sign up or log in
- Copy your API key

**Step 2: Configure**
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
Save to `~/.nanobot/config.json` (or set `OPENROUTER_API_KEY` env var)

**Step 3: Test**
```bash
nanobot agent
ask a question
```

### Option B: Local Ollama
Perfect for sensitive data, no API costs.

**Step 1: Install Ollama**
- Download from https://ollama.ai
- Install on your machine

**Step 2: Pull a Model**
```bash
ollama pull mistral
```

**Step 3: Configure nanobot**
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

Configure per-request and per-agent limits:

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

## 6. Prompt Caching (Cost Optimization)

Anthropic Claude supports prompt caching. Enable to reduce costs on repeated queries:

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
