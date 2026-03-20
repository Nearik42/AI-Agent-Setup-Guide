# Where Does Your Agent Live? Deployment Options Explained

Before you write a single prompt, there's one decision that shapes everything: **where does your agent actually run?**

This isn't a technical detail — it affects your costs, your privacy, your uptime, and how much maintenance you'll do. Make this decision consciously.

---

## The Four Options

### Option 1: Your Laptop / Desktop (Start Here)

Your agent runs as a process on your machine. When you close the lid, it stops.

**How it works:**
- Install Claude Code, run it in your terminal
- Agent has access to your local files, your browser, your apps
- Costs: only API calls (no server fees)
- Privacy: your files never leave your machine

**When to use it:**
- You're just starting out and learning
- Your tasks are interactive (you're there watching)
- You don't need 24/7 availability

**When it breaks down:**
- You need the agent to run while you sleep
- You need it available on your phone
- You're traveling and don't want to keep your laptop on

**Setup time:** 30 minutes
**Monthly cost:** Just API calls (~$20-100/month depending on use)
**Technical level:** ⭐ Beginner

---

### Option 2: A VPS (Virtual Private Server)

A small rented server (€4-15/month) that runs 24/7 — even when your laptop is off.

> 💡 **The one-time investment:** Setting up a VPS sounds intimidating — SSH, server configuration, installing packages. Here's the honest truth: you do this once. After that, Claude Code runs on the server and you give it instructions in plain English from your phone or laptop. The setup takes 1-3 hours. The payoff is an agent available 24/7 that you control entirely with natural language.

**How it works:**
- Rent a server from Hetzner, DigitalOcean, or similar
- Install your agent there
- Connect via SSH or just let it run autonomously
- Your Telegram/Discord bot is always online

**Popular providers:**
| Provider | Cheapest Server | RAM | Good for |
|----------|----------------|-----|---------|
| [Hetzner](https://www.hetzner.com/cloud) CX22 | €4.15/mo | 4GB | Personal agents, always-on bots |
| [DigitalOcean](https://digitalocean.com) Droplet | $6/mo | 1GB | Beginners, simple bots |
| [Hetzner](https://www.hetzner.com/cloud) CX32 | €8.15/mo | 8GB | Multiple agents, more memory |
| Hetzner CCX23 (dedicated) | €22/mo | 8GB dedicated | Production workloads |

**When to use it:**
- You want your agent available 24/7
- You're connecting it to Telegram/Discord
- You want to run scheduled tasks (morning briefings, monitoring)
- You want isolation from your personal machine

**Setup time:** 1-3 hours (first time; includes SSH setup, Node.js, pm2)
**Monthly cost:** €4-15/mo server + API calls
**Technical level:** ⭐⭐ Intermediate

**Reality check:** This is what serious personal agent setups use. The Hetzner CX22 at €4.15/month is genuinely good enough for a personal AI assistant running Claude.

---

### Option 3: Cloud-Managed Agent Platforms

Services that run your agent for you — no server to manage. You pay a subscription, they handle the infrastructure.

**The options:**
- **Perplexity Computer** (Feb 2026): Cloud agent platform. Your agent runs on Perplexity's infrastructure, no server setup. Personal tier (Mac mini required for local features) and Enterprise tier. Coordinates 19+ AI models.
- **OpenClaw Cloud** (if/when available): The framework running as a managed service
- **n8n Cloud**: Visual workflow builder, fully hosted, $20-50/month
- **Zapier with AI**: Point-and-click automation, ~$50-100/month for meaningful AI use

**When to use it:**
- You don't want to manage a server
- You need enterprise-grade security and audit trails
- You want a polished UI rather than a terminal
- Budget is less of a concern than time

**When it's wrong:**
- Your data is sensitive (files, emails go through their servers)
- You want full customization
- Cost is a concern ($50-200/month vs. €4/month for a VPS)

**Monthly cost:** $20-200/month depending on tier
**Technical level:** ⭐ Beginner (but limited control)

---

### Option 4: Hybrid (Local + VPS)

The setup most experienced users end up with:
- **Local**: Interactive Claude Code sessions (coding, file work, exploration)
- **VPS**: Always-on agent for messaging, scheduled tasks, monitoring

This is what Erik's setup looks like: Claude Code runs locally for development, OpenClaw runs on a Hetzner VPS for the always-on Telegram/Discord agent.

**Monthly cost:** €4-15/mo server + local API calls
**Technical level:** ⭐⭐ Intermediate

---

## Decision Guide

**Answer these questions:**

**1. Do you need your agent running 24/7?**
- No → Start with local (Option 1)
- Yes → VPS (Option 2) or managed platform (Option 3)

**2. How sensitive is your data?**
- Very sensitive (medical, financial, confidential) → Local or VPS (you control it)
- Normal sensitivity → Managed platform is fine

**3. How technical are you?**
- Comfortable with terminal, SSH → VPS (best value)
- Prefer no terminal → Managed platform
- Want to learn → VPS (you learn more)

**4. What's your budget?**
- Minimize cost → Local + VPS (€4/month)
- Convenience over cost → Managed platform ($20-200/month)

---

## The Practical Path

For most people reading this guide:

**Week 1**: Run locally. Learn Claude Code on your own machine. No server costs, no setup complexity.

**Week 2-4**: If you want the agent always-on → rent a Hetzner CX22 (€4.15/month). This is the moment the agent becomes genuinely useful for daily life.

**Later**: If you want a more serious setup → add a second, more powerful server, or consider OpenClaw for multi-agent orchestration.

---

## A Note on Costs

All deployment options have API costs on top. The server/platform cost is just the hosting.

The real cost variable is **how much you use the AI model**. More on this in the next chapter: [Models & Costs](02b-models-and-costs.md).
