# Models & Costs: What You're Actually Paying For

The hidden surprise in AI agent setups: it's not the server that costs money, it's the model API calls. Understanding this upfront saves you from unexpected bills.

---

## Current Model Landscape (2026)

### Anthropic Claude (recommended for this guide)

| Model | API Name | Speed | Intelligence | Cost (Input/Output per 1M tokens) |
|-------|----------|-------|-------------|-----------------------------------|
| Claude Opus 4.5 | `claude-opus-4-5` | Slow | Highest | ~$15 / $75 |
| Claude Sonnet 4.6 | `claude-sonnet-4-6` | Fast | Very High | ~$3 / $15 |
| Claude Haiku 3.5 | `claude-haiku-3-5` | Very Fast | Good | ~$0.80 / $4 |

**The practical recommendation**: Start with Sonnet 4.6. It's the current sweet spot — fast, very capable, and reasonable cost. Opus 4.5 is for complex reasoning tasks where you need maximum intelligence. Haiku 3.5 is for high-volume automation where cost matters more than quality.

> **Note on model names**: Anthropic updates model IDs regularly. Always check [docs.anthropic.com/en/docs/models-overview](https://docs.anthropic.com/en/docs/models-overview) for the latest. The names above reflect current models — they will change.

### OpenAI (alternative)

| Model | Notes | Cost |
|-------|-------|------|
| GPT-5.4 | Currently highest capability | ~$10 / $30 per 1M tokens |
| GPT-4.1 | Good balance | ~$2 / $8 per 1M tokens |
| GPT-4.1 mini | Fast, cheap | ~$0.40 / $1.60 per 1M tokens |

### Google Gemini (alternative)

| Model | Notes | Cost |
|-------|-------|------|
| Gemini 2.5 Pro | 2M token context window | ~$3.50 / $10.50 |
| Gemini 2.0 Flash | Fast, cheap | ~$0.10 / $0.40 |

---

## Subscription Plans vs. API

There are two ways to pay:

### API (Pay-as-you-go)
- Pay per token consumed
- No monthly commitment
- Good for: variable usage, learning, building

### Subscription Plans

| Plan | Price | What you get |
|------|-------|-------------|
| Claude.ai Pro | $20/mo | Usage via web/app, not API |
| Claude Max | $100-200/mo | Higher limits via web/app |
| ChatGPT Plus | $20/mo | GPT-4o access via web/app |
| ChatGPT Pro | $200/mo | o1 + higher limits via web/app |

**Important distinction**: Subscription plans give you access to the *web interface*. They do NOT reduce your API costs when building agents. If you're coding with Claude Code or running automated agents, you're using the API — subscriptions don't help here.

**Exception**: Claude Code has its own pricing model — see [claude.ai/code](https://claude.ai/code) for current subscription options that include Claude Code usage.

---

## The Real Cost of Running Agents

This is where people get surprised. An "always-on" agent with a large context window can get expensive fast.

### Why context windows matter for cost

Every time your agent responds, it sends:
- The full conversation history
- Your CLAUDE.md / system prompt
- Any files or data it has loaded

If your CLAUDE.md is 2,000 tokens, your conversation history is 10,000 tokens, and you ask a simple question — you're sending 12,000 tokens as input just for that one exchange.

**Example cost calculation:**
```
Simple question with context:
- Input: 12,000 tokens (history + system prompt + question)
- Output: 500 tokens (answer)
- Cost with Claude Sonnet 4.6: (12,000 × $3 + 500 × $15) / 1,000,000
- = $0.036 + $0.0075 = ~$0.044 per exchange

50 exchanges/day × $0.044 = ~$2.20/day = ~$66/month
```

That's for moderate use. An always-on agent with heavy use (hundreds of exchanges/day, large system prompts) can easily reach $200-500/month.

### Real-world usage reports

From Twitter/X users running always-on agents:
- Light use (10-20 queries/day, small context): $15-40/month
- Moderate use (50-100 queries/day, medium context): $50-150/month
- Heavy use (hundreds of queries, large context, complex tasks): $200-500+/month

---

## Context Engineering: How to Control Costs

The biggest lever: **control your context window size**.

### 1. Keep your CLAUDE.md lean
Every byte in your system prompt costs money on every single API call. A 10,000-token CLAUDE.md at 100 queries/day costs ~$9/day just for the system prompt.

**Good CLAUDE.md**: 500-1,500 tokens — essential info only
**Expensive CLAUDE.md**: 5,000-20,000 tokens — the agent "reads a book" every exchange

### 2. Use context compaction
Claude Code has automatic context compaction — when the conversation gets long, it summarizes old turns. Let this work. Don't fight it.

For always-on agents: design sessions to be short and focused, not one endless conversation.

### 3. Model routing
Use cheap models for simple tasks, expensive models for complex ones:
```
Routing question → Haiku 3.5 (cheap)
Writing code → Sonnet 4.6 (good balance)
Complex reasoning → Opus 4.5 (worth it)
```

### 4. Prompt caching
Anthropic's API supports prompt caching — repeated identical system prompts are cached and cost much less on subsequent calls. If your agent always starts with the same CLAUDE.md, you can save 50-90% on input costs.

### 5. Set hard limits
In [console.anthropic.com](https://console.anthropic.com): set a monthly spending cap. $50 is a good starting limit. Raise it once you know your usage pattern.

---

## Subscription Limits: What You Actually Get

If you pay for subscriptions (not API), here's what the limits look like in practice:

| Plan | Claude Code Access | Web Usage | API |
|------|-------------------|-----------|-----|
| Free | No | Limited | Separate |
| Claude.ai Pro ($20/mo) | Limited | 5x more than free | Separate |
| Claude Max ($100/mo) | More | 5x Pro | Separate |
| Claude Max ($200/mo) | Most | 20x Pro | Separate |

**GPT Pro ($200/mo)** is notably more generous for interactive use than Claude Max at the same price — you get o1, o3, GPT-4.5, Sora video, and more generous limits. But for API-based agent building, the comparison doesn't matter — you pay per token regardless.

---

## Local Models: The Free Alternative

For high-volume tasks where quality matters less than cost, local models are worth knowing about.

**Ollama** (recommended for beginners):
```bash
# Install Ollama
curl -fsSL https://ollama.ai/install.sh | sh

# Download and run a model
ollama pull llama3.2
ollama run llama3.2
```

**What you can run locally (2026):**
- **Llama 3.3 70B**: Strong general capability, needs ~40GB RAM
- **Llama 3.2 3B**: Fast, light, good for simple tasks, needs 4GB RAM
- **Mistral 7B**: Balanced, 8GB RAM
- **Qwen 2.5 Coder 32B**: Excellent for coding, 20GB RAM

**LM Studio**: GUI interface for local models, easier for non-coders

**The honest tradeoff:**
- Local models: free per call, private, slower, lower quality than frontier models
- API models: costs money, potentially private (check provider policies), fast, high quality

For most agent tasks, API models (especially Sonnet-tier) are worth the cost. Use local models for: high-volume low-stakes tasks, when privacy is critical, or when you hit cost limits.

---

## Quick Cost Estimator

Roughly what your setup will cost per month:

| Usage Pattern | Model Choice | Estimated Monthly Cost |
|---------------|-------------|----------------------|
| Learning/experimenting | Sonnet 4.6 | $5-20 |
| Daily personal agent (light) | Sonnet 4.6 | $20-60 |
| Always-on assistant (moderate) | Sonnet 4.6 + Haiku 3.5 routing | $40-120 |
| Heavy automation (many tasks) | Mixed routing | $100-300 |
| Enterprise/team | Depends on usage | $300+ |

These numbers assume sensible context engineering. Ignoring context size can easily 3-5x these estimates.

---

*Check current pricing at [anthropic.com/pricing](https://anthropic.com/pricing) — prices change as models improve.*
