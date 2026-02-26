# Cost Calculator & Optimization Guide

Plan your nanobot deployment cost. Estimate monthly spend, optimize provider routing, and set budget controls.

## Document Control
- **Owner:**
- **Version:** 1.0.0
- **Last Updated:** 2026-02-25
- **Status:** Active

---

## 1. Provider Cost Matrix

| Provider | Model Example | Cost/1K Tokens | Latency | Best For | Notes |
|---|---|---|---|---|---|
| **OpenRouter** | GPT-4, Claude 3.5 | $0.002-0.03 | <2s | Cost-conscious mixed | Best value for quality; supports 100+ models |
| **Anthropic** | Claude 3 Opus | $0.015 | <3s | Premium tasks | Most capable; prompt caching reduces cost |
| **OpenAI** | GPT-4o | $0.005 | <2s | General purpose | Reliable; expensive for high volume |
| **Qwen** (via OpenRouter) | Qwen2-72B | $0.0005 | <4s | Budget tier | Cheapest option; good quality for cost |
| **DeepSeek** | DeepSeek-67B | $0.0003 | <5s | Ultra-budget | Lowest cost; slower |
| **Ollama** (local) | Qwen2, Llama2 | $0 | 2-10s | Privacy-critical | Free; needs local GPU/CPU |
| **Gemini** | Gemini Pro | $0.0005 | <2s | Google ecosystem | Billed per request |
| **Cohere** | Command R | $0.0003 | <3s | Specialized use | Command models; moderate cost |

**Cost Tier Summary:**
- **Tier A (Premium):** Claude Opus, GPT-4 → $0.015-0.03/1K tokens (use for high-value tasks)
- **Tier B (Balanced):** Claude Haiku, GPT-4 Mini, Qwen-72B → $0.0005-0.005/1K tokens
- **Tier C (Budget):** DeepSeek, Gemini, Cohere → $0.0001-0.0005/1K tokens
- **Tier D (Free):** Ollama local → $0 (infrastructure only)

---

## 2. Monthly Cost Calculator

### Step 1: Estimate Daily Token Usage

**Typical token consumption per interaction:**
- Simple query (web search): 500 tokens
- Moderate complexity (multi-step): 2,000 tokens
- Complex task (analysis + synthesis): 5,000 tokens

**Example team:**
- 5 users × 10 interactions/day = 50 total interactions/day
- Average 2,000 tokens per interaction = **100,000 tokens/day**

### Step 2: Calculate Monthly Cost

**Formula:**
```
(Daily tokens × 30 days) × (Cost per 1K tokens / 1000) = Monthly cost
```

**Examples (100,000 tokens/day):**

| Provider | Cost Per Month | Annual |
|---|---|---|
| OpenRouter (Qwen) | $1.50 | $18 |
| DeepSeek | $0.90 | $11 |
| Ollama (local GPU cost) | ~$50 | $600 |
| OpenRouter (Claude Haiku) | $15 | $180 |
| OpenAI (GPT-4o) | $15 | $180 |
| Claude Opus | $45 | $540 |

**Interactive Calculator:**

```python
# Paste into nanobot test mode or Python script
tokens_per_day = 100000
cost_per_1k = 0.0005  # DeepSeek: $0.0005/1K
monthly_cost = (tokens_per_day * 30 * cost_per_1k) / 1000
print(f"Monthly cost at {cost_per_1k}/1K tokens: ${monthly_cost:.2f}")
# Output: Monthly cost: $1.50
```

---

## 3. Multi-Provider Routing (Optimize Spend)

Route different tasks to different tiers based on complexity and value.

### Strategy: Hybrid Multi-Tier Routing

**Tier Logic:**
- **Tier A (Claude Opus)** ← 10% of tasks (complex analysis, creative work)
- **Tier B (Claude Haiku, Qwen)** ← 60% of tasks (general chat, moderate reasoning)
- **Tier C (DeepSeek, Budget Models)** ← 30% of tasks (summarization, classification, routine)

### Implementation: Config Example

```json
{
  "llm": {
    "routing": {
      "default": "tier_b",
      "rules": [
        {
          "channel": "#vip-*",
          "provider": "openrouter",
          "model": "claude-opus",
          "tier": "a"
        },
        {
          "channel": "#research-*",
          "provider": "openrouter",
          "model": "qwen2:72b",
          "tier": "b"
        },
        {
          "channel": "#bk-*",
          "provider": "openrouter",
          "model": "deepseek-67b",
          "tier": "c"
        },
        {
          "keyword": "urgent",
          "provider": "anthropic",
          "model": "claude-opus",
          "tier": "a"
        }
      ]
    }
  }
}
```

**Result:** 
- VIP channels always get Claude Opus (best quality)
- Research uses Qwen (good quality, cheaper)
- Backlog use DeepSeek (budget tier, adequate quality)
- Urgent keyword triggers Opus regardless of channel

**Estimated Cost Saving:** 60-70% vs. using Claude Opus for everything

---

## 4. Cost Per Workflow Examples

Real-world costs for common nanobot tasks:

### Example 1: Daily Knowledge Consolidation

**Workflow:** Cron job nightly; collect Discord messages from #prd channel; summarize + write to Obsidian

**Tokens consumed:**
- Read 50 messages: 10,000 tokens
- Summarization: 5,000 tokens
- Write Obsidian: 500 tokens
- **Total: ~15,000 tokens/day**

**Cost (using Tier C):**
```
15,000 tokens/day × 30 days × $0.0005/1K = $0.225/month
```

**ROI:** ~1 person-hour saved daily = Worth $500+ in labor, cost $0.23 🎯

### Example 2: Multi-Channel Content Posting

**Workflow:** User requests article post to Discord, Slack, Telegram simultaneously

**Tokens consumed:**
- Generate content: 3,000 tokens
- Format for channels: 1,500 tokens
- Post & confirm: 500 tokens
- **Total: ~5,000 tokens per post**

**Cost (using Tier B):**
```
5,000 tokens × $0.002/1K = $0.01 per post
```

**ROI:** Saves 15 mins manual cross-posting, cost $0.01 🎯

### Example 3: GitHub Automation (Daily)

**Workflow:** Cron job to scan issues, summarize PRs, post status to Discord

**Tokens consumed:**
- Fetch & parse 10 PRs: 8,000 tokens
- Summarize each: 4,000 tokens
- Generate status report: 3,000 tokens
- **Total: ~15,000 tokens/day**

**Cost (using Tier C):**
```
15,000 tokens/day × 30 days × $0.0005/1K = $0.225/month
```

**ROI:** Saves ~30 mins team review time daily, cost $0.225 🎯

### Example 4: Customer Support Bot (High Volume)

**Workflow:** 100 support questions/day across Discord, Slack, Telegram

**Tokens consumed per day:**
- Answer questions: 100 × 1,500 tokens = 150,000 tokens
- **Total: ~150,000 tokens/day**

**Cost Options:**
- Tier B (Qwen): 150,000 × 30 × $0.0005/1K = **$2.25/month**
- Tier A (Claude): 150,000 × 30 × $0.002/1K = **$9/month**

**ROI:** Deflects 50+ support tickets/month = Worth $1,000+ in labor🎯

---

## 5. Budget Caps & Token Limits

### Set Monthly Budget Cap

**Config example:**

```json
{
  "costs": {
    "monthly_budget": 100.00,
    "alert_at_percent": [50, 75, 90, 100],
    "action_on_exceed": "queue_lower_tier"
  }
}
```

**Behavior:**
- At 50%: Log warning message
- At 75%: Downgrade new requests to Tier C only
- At 90%: Downgrade to Tier C, alert ops team
- At 100%: All new requests → local Ollama (free)

### Per-Team Budget Allocation

For multi-team deployments:

```json
{
  "teams": {
    "prd": {
      "monthly_budget": 50,
      "tier": "b"
    },
    "research": {
      "monthly_budget": 30,
      "tier": "a"
    },
    "support": {
      "monthly_budget": 20,
      "tier": "c"
    }
  }
}
```

Each team gets its own pool. When exhausted:
- Auto-fallback to local Ollama (if available)
- Or queue requests for next billing cycle

### Token Usage Tracking

```bash
# View current month spend
nanobot cost report --this-month
# Output:
# Feb 2026: 3,000,000 tokens / $4.50 (Qwen tier)
# Budget remaining: $95.50 / 30% of quota

# View by channel
nanobot cost report --by-channel
# Output:
# #prd: 1,500,000 tokens / $2.25
# #research: 1,000,000 tokens / $1.50
# #support: 500,000 tokens / $0.75
```

---

## 6. Optimization Strategies

### Strategy 1: Batch Processing (Save 40%)

Instead of 50 real-time requests daily, batch into:
- Morning batch (8am): 30 requests
- Afternoon batch (2pm): 20 requests

**Token reduction:** Fewer context switches = shorter responses  
**Estimated savings:** 30-40%

### Strategy 2: Cache Repetitive Prompts (Save 20%)

If 30% of tasks use the same system prompt:

```json
{
  "cache": {
    "strategy": "claude_prompt_cache",
    "system_prompts": [
      {
        "name": "support_bot",
        "prompt": "You are a customer support agent...",
        "ttl_hours": 24
      }
    ]
  }
}
```

**Cost reduction:** Cached tokens cost 90% less (Anthropic)

### Strategy 3: Local Ollama for Off-Peak (Save 70%)

Run expensive tasks during off-peak hours on local GPU:

```json
{
  "scheduling": {
    "research_summaries": {
      "time": "02:00",
      "provider": "ollama",
      "model": "qwen2:72b"
    }
  }
}
```

**Cost reduction:** Free compute hours + cloud API for urgent tasks

### Strategy 4: Summarize Long Conversations (Save 30%)

Instead of feeding full history to LLM:

```bash
# Every 20 messages, auto-summarize conversation history
# Insert summary instead of full history
```

**Token reduction:** 20-30% fewer tokens per interaction

---

## 7. Break-Even Analysis: Cloud vs. Self-Hosted

### Scenario: Team of 10 people, 50 daily interactions

**Cloud (OpenRouter Qwen) approach:**
```
100,000 tokens/day × 30 × $0.0005/1K = $1.50/month
Team cost: $1.50 + ZenDesk/Slack = $500/month
```

**Self-Hosted (VPS + Ollama) approach:**
```
VPS: $20/month (4GB RAM, CPU)
GPU compute (rented): $200/month (NVIDIA A100 sharing)
Ollama updates: $0
Total: $220/month
```

**Break-even:** Self-hosted cheaper at >150k tokens/day consistently

**Recommendation:**
- **<50k tokens/day:** Cloud only (too cheap to self-host)
- **50k-200k tokens/day:** Hybrid (cloud + local batching)
- **>200k tokens/day:** Self-hosted (ROI in 2-3 months)

---

## 8. Cost Monitoring Checklist

Monthly review:

- [ ] Total tokens consumed vs. budget
- [ ] Cost per channel (is any team over-using?)
- [ ] Average tokens per interaction (trending up/down?)
- [ ] Tier distribution (are we using Tier A too often?)
- [ ] Cache hit rate (prompt caching effectiveness)
- [ ] Ollama uptime (if self-hosted; justify continued use?)

```bash
# Generate comprehensive monthly report
nanobot cost-report --detailed --month=feb
```

---

## 9. Cost Reduction Checklist

**Quick wins (easy, 10-30% savings):**
- [ ] Enable prompt caching (Anthropic)
- [ ] Switch to Qwen for routine tasks
- [ ] Batch non-urgent summarization to 2am window
- [ ] Add tier-based routing by channel

**Medium effort (30-50% savings):**
- [ ] Set up conversation summarization every 20 messages
- [ ] Implement local Ollama for off-peak batch jobs
- [ ] Review and reduce system prompt verbosity

**Major changes (50-70% savings):**
- [ ] Deploy self-hosted VPS + Ollama for high volume
- [ ] Migrate to on-device models (less privacy tradeoff)
- [ ] Implement aggressive batching (hourly vs. real-time)

---

## 10. Example Annual Budget Forecast

10-person team, 100,000 tokens/day:

| Approach | Monthly | Annual | Notes |
|---|---|---|---|
| **Cloud Only (Qwen)** | $1.50 | $18 | Cheapest; accept minimal latency |
| **Cloud + Caching** | $1.20 | $14 | With Anthropic prompt cache |
| **Hybrid (Cloud+Local)** | $40 | $480 | VPS $20 + part-time GPU $20 |
| **Full Self-Hosted** | $220 | $2,640 | VPS $20 + GPU $200 |

**Sweet spot for most teams:** Cloud (Tier C/B) + local batching = **$20-40/month**

---

## Revision History

| Date | Version | Change |
|---|---|---|
| 2026-02-25 | 1.0.0 | Initial cost calculator and optimization guide |

