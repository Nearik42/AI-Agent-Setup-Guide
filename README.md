# 🤖 AI Setup Guide — From ChatGPT to Your Own Agent Army

> A practical guide for building your own AI agent setup — written for people who already use AI tools but want to go deeper.

## What is this?

This guide walks you through setting up a **personal AI agent system** — one that runs 24/7, has persistent memory, connects to Discord, and can automate real work. It's based on a real setup using [OpenClaw](https://openclaw.dev), Claude, and Discord as the command center.

This isn't theory. Every pattern here is in production.

## Who is this for?

✅ You already use ChatGPT, Claude, or Lovable
✅ You're business-minded and want to automate real tasks
✅ You're tech-curious and comfortable with a terminal (or willing to learn)
✅ You want more than a chatbot — you want a digital team member

❌ Not for: pure non-tech users who just need quick answers
❌ Not for: people who've never opened a terminal and don't want to

## Level Overview

| Level | What You'll Build | Time Investment |
|-------|------------------|-----------------|
| 🟢 **Basic** | One agent with Discord integration | ~2–3 hours |
| 🟡 **Intermediate** | Persistent memory + skill plugins + idea pipeline | ~1 day |
| 🔴 **Advanced** | Multi-agent system with specialized roles + dev pipeline | ~1 week |

## Table of Contents

1. [🧠 Mindset: Why Agents?](docs/00-mindset.md)
2. [🟢 Basic Setup: Your First AI Agent](docs/01-basic-setup.md)
3. [🟡 Intermediate: Memory, Skills & Discovery Pipeline](docs/02-intermediate.md)
4. [🔴 Advanced: Multi-Agent Architecture & Dev Pipeline](docs/03-advanced.md)
5. [🐦 Twitter/X Resource Index](docs/04-twitter-index.md)
6. [📖 Glossary](docs/05-glossary.md)

## The Real Setup (for reference)

This guide is based on a live setup with:

- **Framework:** OpenClaw (multi-agent, runs on VPS)
- **AI Model:** Claude (Opus for heavy thinking, Sonnet for everyday tasks)
- **Command Center:** Discord (channels per project, mobile access)
- **Agents:** `gooper` (orchestrator), `gooper-lite` (fast tasks), `dev` (coding), `analyst` (research)
- **Memory:** `MEMORY.md` (long-term) + daily logs in `memory/YYYY-MM-DD.md`
- **Skills:** discovery-pipeline, pm-orchestrator, coding-agent, weather, github, tavily
- **Projects:** MA Advisor (M&A due diligence tool), ExoExplorer, Gooper-1 (AutoResearch)

---

*Start with [Mindset](docs/00-mindset.md) if you're new. Jump to [Basic Setup](docs/01-basic-setup.md) if you're ready to build.*
