# Models & Costs: What You're Actually Paying For

The hidden surprise in AI agent setups: it's not the server that costs money — it's the model API calls. Understanding this upfront saves you from unexpected bills.

---

## Current Model Landscape (2026)

### Anthropic Claude

| Model | API ID | Speed | Best For | Cost (per 1M tokens in/out) |
|-------|--------|-------|---------|---------------------------|
| Claude Opus 4.5 | `claude-opus-4-5` | Slow | Complex reasoning, highest quality | ~$15 / $75 |
| Claude Sonnet 4.6 | `claude-sonnet-4-6` | Fast | Daily work, coding, agents | ~$3 / $15 |
| Claude Haiku 3.5 | `claude-haiku-3-5` | Very Fast | High-volume, routing, summaries | ~$0.80 / $4 |

**The recommendation**: Start with **Sonnet 4.6** — current sweet spot for agent work. Fast, highly capable, reasonable cost. Use Haiku 3.5 for routing/triage tasks. Opus 4.5 only for tasks that genuinely need maximum intelligence.

> ⚠️ **Model names change.** Always verify at [docs.anthropic.com/en/docs/models-overview](https://docs.anthropic.com/en/docs/models-overview) before hardcoding any model ID in production.

---

### OpenAI

| Model | Notes | Cost (per 1M tokens in/out) |
|-------|-------|---------------------------|
| GPT-5.4 | Current flagship, strongest reasoning + coding | ~$10 / $30 |
| o3 | Reasoning model, slower but exceptional for hard problems | ~$10 / $40 |
| o4-mini | Fast reasoning at lower cost | ~$1.10 / $4.40 |
| GPT-4.1 mini | Cheap, fast, good for routing | ~$0.40 / $1.60 |

**Note**: GPT-4.1 (non-mini) and o1 are largely superseded. For serious agent work in 2026, use GPT-5.4 or o3. o4-mini is excellent value for reasoning-intensive tasks that don't need full o3.

> 💬 **Community:** [@lamxnt](https://x.com/lamxnt/status/2030390230579281958) — "I have fully moved to Hermes Agent from @NousResearch. Main model is using Codex 5.3 with OAuth through OpenAI" — useful perspective on mixing local and cloud models.

---

### Google Gemini

| Model | Notes | Cost (per 1M tokens in/out) |
|-------|-------|---------------------------|
| Gemini 2.5 Pro | Massive 2M token context window | ~$3.50 / $10.50 |
| Gemini 2.0 Flash | Fast, cheap, good for high-volume | ~$0.10 / $0.40 |

**When to choose Gemini**: If you need to process huge documents (entire codebases, long video transcripts) in a single pass — the 2M token context window is unique.

---

## Subscription Plans vs. API

**This distinction trips up almost everyone.**

### API (Pay-as-you-go)
- Pay per token consumed
- No monthly commitment  
- Used by: Claude Code, automated agents, any API-based tool
- Good for: learning, building, variable usage

### Subscription Plans (Web Interface)
| Plan | Price | What you actually get |
|------|-------|----------------------|
| Claude.ai Pro | $20/mo | Higher limits in the web UI |
| Claude Max | $100/mo | 5x Pro limits in web UI |
| Claude Max (high) | $200/mo | 20x Pro limits in web UI |
| ChatGPT Plus | $20/mo | GPT-4o access in web UI |
| ChatGPT Pro | $200/mo | o1, o3, GPT-5.4 + generous limits in web UI |

**The critical distinction**: Subscription plans give you access to the *web interface*. They do **not** reduce your API costs when you're running agents programmatically. Building with Claude Code or running automated agents? You're using the API — subscriptions don't help.

**Except... there's a way.**

---

## Using Subscription Tokens in Agents

This is one of the most interesting cost-saving approaches in the agent community — and it's worth understanding clearly.

### The Concept

Your Claude Max or ChatGPT Pro subscription gives you a generous token budget for web-based usage. What if you could route your agent's calls through that subscription instead of paying API rates?

### OpenAI: Officially Supported ✅

OpenAI explicitly supports this. If you have a **ChatGPT Pro** subscription, you can:

1. Generate a **subscription-based API key** (different from the pay-per-token API key)
2. Use this key in your Claude Code or agent setup
3. Your agent calls are charged against your subscription, not your pay-per-token API balance

This means: a $200/month ChatGPT Pro subscription can power both your interactive ChatGPT use AND automated agent tasks, up to the subscription's rate limits.

Setup: go to [platform.openai.com/api-keys](https://platform.openai.com/api-keys) → create key associated with your Pro subscription.

### Anthropic/Claude: Gray Area ⚠️

Anthropic does **not** officially support using your Claude Max subscription token in external tools. However:

- **What exists**: Claude Code is Anthropic's own product, and when you use it with a Claude Max subscription, you're running Claude Code "on behalf of" your subscription. This is explicitly supported.
- **The gray area**: Some users extract the session auth token from their Claude.ai browser session and use it in API calls. This technically works, but violates Anthropic's terms of service for most use cases.
- **The legitimate path**: Use Claude Code's built-in Max subscription support — if you have Claude Max, Claude Code will use it. This is the intended workflow.

**Bottom line for Claude:**
- ✅ Claude Code + Claude Max subscription → explicitly supported, Claude Code is designed for this
- ❌ Manually extracting session tokens and using them in custom scripts → against ToS
- 🔄 This is an evolving area — Anthropic is likely to formalize more options here

### Practical Decision

If you're choosing between **Claude API pay-per-token** vs **subscription + Claude Code**:

| Approach | Monthly Cost | Token Budget | Good For |
|----------|-------------|-------------|---------|
| API pay-per-token | $20-150+ depending on use | Unlimited (pay more, get more) | Building custom agents, scripts |
| Claude Max ($100/mo) + Claude Code | $100 flat | High (subscription limits) | Interactive agent work, development |
| ChatGPT Pro ($200/mo) + API key | $200 flat | Generous limits | Heavy users wanting predictable cost |

For most people starting out: **pay-per-token API** with sensible context engineering is the most flexible. Move to subscription-based once you know your usage pattern.

---

## The Real Cost of Running Agents

This is where people get surprised. An always-on agent with a large context window can get expensive fast.

### Why context windows drive cost

Every time your agent responds, it sends:
- Your entire system prompt / CLAUDE.md
- The full conversation history
- Any files or data it has loaded

**Example cost calculation (Claude Sonnet 4.6):**
```
System prompt:  2,000 tokens
Conversation:  10,000 tokens  
Your question:    200 tokens
Total input:   12,200 tokens

Output:           800 tokens

Cost: (12,200 × $3 + 800 × $15) / 1,000,000
    = $0.0366 + $0.012
    = ~$0.049 per exchange
```

50 exchanges/day × $0.049 = ~$2.45/day = **~$74/month** from context alone.

That's for moderate use. Heavy users with large system prompts and hundreds of daily exchanges report $200-500+/month.

### Real-world usage reports (from the community)

| Usage Pattern | Monthly API Cost |
|--------------|-------------------|
| Experimenting, 10-20 queries/day | $5-25 |
| Regular use, small context | $20-60 |
| Always-on agent, medium context | $60-150 |
| Heavy automation, large context | $200-500+ |

These are API costs. Claude Code with Claude Max subscription changes these numbers significantly for interactive use.

---

## Context Engineering: Control Your Costs

The biggest lever you have.

### 1. Keep your CLAUDE.md lean

Every byte in your system prompt costs money on **every single API call**. 

- ✅ Good CLAUDE.md: 500-1,500 tokens — essential context only
- ❌ Expensive CLAUDE.md: 5,000-20,000 tokens — costs ~$30-120/month just for the system prompt at moderate use

**Tip**: Write your CLAUDE.md, then cut 50%. Then cut another 30%. The agent works better with focused, essential context than a wall of text.

### 2. Prompt caching

Anthropic's API automatically caches repeated identical prefixes. If your CLAUDE.md + system prompt stays the same across calls, subsequent calls pay a fraction (typically 10% of input cost) for the cached portion.

**How much this saves**: On a 5,000-token system prompt with 100 daily calls:
- Without caching: 100 × 5,000 × $3/1M = $1.50/day
- With caching: ~$0.15/day for cached tokens
- **Savings: ~$40/month from this one optimization**

This happens automatically with the Anthropic API. No setup required.

### 3. Model routing

Not every task needs the most expensive model:

```
Routing/classification → Haiku 3.5 (cheap, fast)
Regular coding/writing → Sonnet 4.6 (good balance)
Complex reasoning → Opus 4.5 (worth it for hard problems)
```

Using Haiku for 70% of calls and Sonnet for 30% can cut costs by 50-70% vs. using Sonnet for everything.

### 4. Session design

For always-on agents: design sessions to be focused and short, not one endless conversation. Compaction (automatic summarization of old turns) helps, but starting fresh regularly is cheaper.

### 5. Set hard spending limits

In [console.anthropic.com](https://console.anthropic.com) → Settings → Limits: set a monthly hard cap.

**Start with $30-50**. Raise it once you understand your actual usage pattern. An uncapped agent in a loop can burn through hundreds of dollars before you notice.

### 6. Keep agent docs current

> 💬 **Community:** [@AndrewYNg](https://x.com/AndrewYNg/status/2031051809499054099) — "Context Hub, an open tool that gives your coding agent the up-to-date API documentation it needs" — [chub.ai](https://chub.ai) helps agents use current API docs instead of hallucinating outdated ones.

---

## Local Models: The Free Alternative

For high-volume tasks where quality can be lower, local models cost nothing per call.

**[Ollama](https://ollama.ai)** — easiest to get started:
```bash
# Install (Mac/Linux)
curl -fsSL https://ollama.ai/install.sh | sh

# Download and run a model
ollama pull llama3.3
ollama run llama3.3
```

**[LM Studio](https://lmstudio.ai)** — GUI interface, no terminal required, great for beginners.

**What runs locally in 2026:**
| Model | RAM Needed | Quality | Best For |
|-------|-----------|---------|---------|
| Llama 3.3 70B | ~40GB | Strong | General tasks if you have the hardware |
| Qwen 2.5 Coder 32B | ~20GB | Excellent for code | Local coding agent |
| Mistral 7B | ~8GB | Decent | Simple tasks, routing |
| Llama 3.2 3B | ~4GB | Basic | Summarization, classification |

**The honest tradeoff:**
- Local: free per call, private, requires good hardware, lower quality
- API: costs money, fast, consistently high quality, no hardware requirements

Most serious setups use API models for the main agent work and local models for specific high-volume or privacy-sensitive sub-tasks.

---

## Quick Cost Estimator

| Your Situation | Recommended Model | Estimated Monthly API Cost |
|---------------|------------------|--------------------------|
| Just learning, occasional use | Sonnet 4.6 | $5-20 |
| Daily personal use, light context | Sonnet 4.6 | $20-60 |
| Always-on assistant, moderate use | Sonnet 4.6 + Haiku routing | $50-150 |
| Heavy automation, many tasks | Mixed routing | $100-300 |
| + Claude Max subscription | Claude Code on subscription | Subscription cost + overflow API |

*These numbers assume sensible context engineering. Ignoring context size can 3-5x these estimates.*

*Check current pricing at [anthropic.com/pricing](https://anthropic.com/pricing) and [openai.com/pricing](https://openai.com/pricing) — prices change regularly.*
