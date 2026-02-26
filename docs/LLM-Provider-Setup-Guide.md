# LLM Provider Setup Guide

Configure nanobot to use different AI (artificial intelligence) models based on workflow type, cost, and capability requirements.

## Document Control
- **Owner:**
- **Version:** 1.3.0
- **Last Updated:** 2026-02-26
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

> **Linux/Mac Terminal Tip:** To paste commands — middle-click (fastest), or `Ctrl+Shift+C` / `Ctrl+Shift+V`, or right-click → Paste.

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
⏱️ ~1 minute
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
⏱️ ~5 minutes to complete  
Best if you're using Claude exclusively and want to avoid OpenRouter as an intermediary. Gives access to Anthropic's prompt caching features (see §6).

#### Before You Start
- ☐ Anthropic account at https://console.anthropic.com
- ☐ Credit card (free tier credits available on signup)
- ☐ Text editor to edit config file

**Step 1: Get API Key**
⏱️ ~2 minutes

- Visit https://console.anthropic.com/settings/keys
- Click **Create Key**
- Copy the key (shown once—save it securely)

**Step 2: Configure**
⏱️ ~1 minute

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
Save to `~/.nanobot/config.json`

**Step 3: Test**
```bash
nanobot agent
ask a question
```
✅ Expected: Claude responds within a few seconds

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

Then configure channel routing in your governance policy to map channels to agents — see [Security & Governance Policies §5 (Provider Routing by Workflow)](Security-and-Governance-Policies.md) for full routing config examples.

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

This re-uses cached prompts for 5 minutes. Cached tokens are billed at ~10% of normal cost, so total bill savings depend on your cache hit rate (typically 50–80% on repeated prompts).

## 7. Environment Variable Override

**When to use:** Override the config-file model for a single command — useful for one-off tests, debugging a specific model, or CI/CD pipelines where you don't want to edit config files.

```bash
# Use a specific model for one command only
NANOBOT_MODEL=openai/gpt-4 nanobot agent

# Override provider too
NANOBOT_PROVIDER=anthropic NANOBOT_MODEL=claude-opus-4-5 nanobot agent
```

This does **not** change `config.json` — the override only applies to that command invocation.

## 8. Web Search API (Brave)

The `web_search` tool requires a Brave Search API key. This is separate from your LLM provider — it gives nanobot the ability to search the live web in addition to reasoning with an LLM.

**Get a key (~2 minutes):**
1. Visit https://api.search.brave.com/
2. Sign up → **Free tier** gives 2,000 queries/month at no cost
3. Copy your API key

**Add to config:**
```json
{
  "braveApiKey": "YOUR_BRAVE_API_KEY"
}
```

**Without this key:** `web_search` tool will be unavailable. All other tools and LLM providers remain functional.

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

## 10. Multi-Model Synergy & Advanced Agentic Workflows

Beyond simple failover, you can design workflows where **multiple specialized LLMs collaborate** to solve complex problems more effectively and cheaply than a single expensive model.

### Collaborative Roles & Synergy

When LLMs work together within the Nanobot AOS, they form a **Multi-Agent System**. By assigning specialized roles, each model handles what it does best:

- **The Architect (High Intelligence):** Premium model like **Claude 3.5 Sonnet** or **GPT-4o**. Handles high-level planning, complex reasoning, and final synthesis.
- **The Worker (Speed/Efficiency):** Faster, cheaper model like **Claude 3 Haiku** or **DeepSeek**. Handles repetitive tasks, data extraction, initial drafting, formatting.
- **The Critic/Judge (Quality Control):** Independent model (often different provider to avoid bias) that reviews outputs for errors or hallucinations.

### Synergy Schemes in Practice

| Scheme | Pattern | Benefit |
|---|---|---|
| **Draft-and-Polish** | Worker (Draft) → Architect (Review) | High-quality output at a fraction of the Architect's full cost |
| **Search-and-Filter** | Worker (Scout) → Architect (Analyst) | Worker narrows 50 web results to 5 most relevant, saving Architect's expensive context window |
| **Verification Loop** | Model A (Generator) ↔ Model B (Auditor) | Model B approves shell commands or code before execution for security |
| **Delegation Chain** | Architect (Plan) → Worker (Execute) → Architect (Verify) | Architect handles intelligence; Worker handles repetitive work; loop closes on verification |

### Implementation via Nanobot Skills

A Nanobot skill implements multi-model collaboration by calling the LLM multiple times within its `implementation.py`. Because Nanobot is **channel-agnostic**, this orchestration happens behind the scenes—users see only the final, verified result in Discord/Slack/Telegram/etc.

**Example: "Secure Coder" Skill**

1. **Step 1:** Skill sends request to **GPT-4o** → writes Python script (cost: ~$0.01)
2. **Step 2:** Skill sends output to local **Llama 3 (via Ollama)** with security review prompt (cost: $0.00)
3. **Step 3:** If no issues found, code saved to workspace. If issues found, loop back to GPT-4o with error report for rewrite.

**Cost Example:**
- Using GPT-4 alone for all steps: ~$0.15-0.30
- Using GPT-4o for planning + Haiku for drafting + local Llama for verification: ~$0.03-0.05 (80% savings)

### Setup via Skill Configuration

Multi-model workflows are implemented in a skill's `SKILL.md` instructions and `implementation.py`. The `SKILL.md` describes the workflow in natural language; the implementation script handles the LLM calls.

**SKILL.md excerpt (research-synthesis skill):**
```markdown
---
name: research-synthesis
description: "Multi-model research workflow: fast model scouts, premium model synthesizes"
tools-required:
  - web_search
  - summarize
models-required:
  - scout: qwen (cheap, fast — for initial search and filtering)
  - analyst: claude-opus (premium — for deep synthesis)
  - critic: ollama-mistral (local/free — for quality review)
---

## Workflow

1. Use the **scout model** (Qwen via OpenRouter) to run web_search on the topic and summarize
   each result in 1-2 sentences. Return the top 5 most relevant summaries.
2. Pass those 5 summaries to the **analyst model** (Claude Opus). Ask it to synthesize a
   detailed analysis with citations.
3. Pass the analyst's output to the **critic model** (Ollama Mistral) with the prompt:
   "Check this analysis for factual errors, missing citations, and unclear claims."
4. If the critic flags issues, loop back to step 2 with the critique appended.
5. Return the final verified synthesis to the user.
```

### Global Routing with Tiered Configuration

You can also define multi-model synergy at the **organization level** via `config.json`. Different channels or teams automatically route to different model combinations:

```json
{
  "llm_routing": {
    "rules": [
      {
        "channel": "#admin-*",
        "primary": "anthropic/claude-opus",
        "secondary": "openrouter/qwen",
        "verification": "local/ollama"
      },
      {
        "channel": "#general",
        "primary": "openrouter/qwen",
        "secondary": "local/ollama"
      },
      {
        "channel": "#research-*",
        "primary": "anthropic/claude-opus",
        "secondary": "anthropic/claude-haiku",
        "verification": "openrouter/deepseek"
      }
    ]
  }
}
```

**Result:** Different "brains" automatically handle different organizational responsibilities, optimizing both cost and quality.

## 11. Testing & Troubleshooting

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

## 12. Common Mistakes & Solutions

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

## 13. AOS Integration & Governance

LLM provider selections plug directly into the AOS 3-plane governance model. Once providers are configured here, you control *which* provider runs *where* through your governance policy — not by editing this file.

**Typical channel-to-model mapping:**

| Channel Pattern | Recommended Model | Reason |
|---|---|---|
| `#bk-*` | Qwen or DeepSeek (Tier C) | Routine bookkeeping; low risk; cheapest |
| `#prd-*-analytics` | Claude Haiku or GPT-4o-mini (Tier B) | Fast, moderate cost |
| `#prd-*-marketing` | Claude Sonnet (Tier B-A) | Quality writing; moderate cost |
| `#res-*` | Claude Opus or GPT-4o (Tier A) | Complex synthesis; accuracy matters |
| `#admin-*` | Claude Opus + local verification | High responsibility; use Verification Loop (§10) |
| `#general` | Qwen or local Ollama (Tier C/Free) | High volume; keep costs low |

**Governance setup steps:**
1. Configure all providers you'll use in `~/.nanobot/config.json` (this guide)
2. Define channel routing rules in your governance policy — see [Security & Governance Policies §5](Security-and-Governance-Policies.md)
3. Set per-team budget caps — see [Cost Calculator & Optimization](Cost-Calculator-and-Optimization.md)
4. Test with `nanobot doctor` to confirm all providers are reachable

Provider routing is **policy-plane configuration** — operations staff can adjust it without touching this guide or your provider credentials.

---

## 14. Related Documents

- [Cost Calculator & Optimization](Cost-Calculator-and-Optimization.md) — Per-provider cost matrix and monthly spend estimation
- [Security & Governance Policies](Security-and-Governance-Policies.md) — LLM provider routing, approval workflows, cost allocation
- [AOS Startup: Advanced Build](AOS-Startup-Advanced-Build.md) — Ollama setup and configuration
- [AOS Startup: Simple Build](AOS-Startup-Simple-Build.md) — Quick cloud-based setup
- [Master Index](Master-Index.md) — Complete system documentation

---

## 15. Revision History

| Date | Version | Change |
|---|---|---|
| 2026-02-26 | 1.3.0 | Review pass: completed Option C format, expanded §7 (env override) and §8 (Brave API), expanded §13 (AOS governance table), fixed §10 SKILL.md schema, clarified cost/M notation, converted copy/paste tip to inline callout |
| 2026-02-26 | 1.2.0 | Added "Multi-Model Synergy & Advanced Agentic Workflows" section covering collaborative LLM patterns, skill-based coordination, and global routing |
| 2026-02-26 | 1.1.0 | Updated cross-references to consolidated docs; fixed section numbering; added related documents section |
| 2026-02-25 | 1.0.0 | Initial guide covering 8 major providers, multi-provider setup, cost controls, and caching |
