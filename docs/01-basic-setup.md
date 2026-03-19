# 🟢 Basic Setup: Your First AI Agent

> Goal: Get one AI agent running, connected to Discord, responding to your messages.

---

## What is OpenClaw?

OpenClaw is an open-source multi-agent framework that runs on your machine or a VPS (cloud server). It connects AI models (like Claude) to your tools — Discord, GitHub, file systems, APIs — and keeps them running 24/7. Think of it as the "operating system" for your personal AI agents.

Key differentiators: file-based memory, Discord-native interface, plugin system (skills), multi-agent support out of the box.

---

## Prerequisites

Before you start, make sure you have:

- **Mac or Linux** (Windows works via WSL, but expect friction)
- **Node.js 20+** — install via [nvm](https://github.com/nvm-sh/nvm): `nvm install 20`
- **GitHub account** — for version control and skills
- **Discord account** — this will be your command center
- **Anthropic API key** — get one at [console.anthropic.com](https://console.anthropic.com)
  - Budget: ~$10–20/month for casual daily use with Claude Sonnet
  - ~$50–100/month for heavy use with Claude Opus

---

## Installation

### Step 1: Install OpenClaw

```bash
npm install -g openclaw
```

### Step 2: Initialize your workspace

```bash
mkdir ~/my-agent && cd ~/my-agent
openclaw init
```

This creates your workspace with starter config files.

### Step 3: Configure your API key

```bash
openclaw config set anthropic.apiKey YOUR_KEY_HERE
```

Or add it to the generated `.env` file in your workspace.

### Step 4: Start the gateway

```bash
openclaw gateway start
```

Your agent is now running. Open `http://localhost:18789` to see the dashboard.

---

## Key Config Files (Explained Simply)

OpenClaw uses markdown files to configure your agent's personality and behavior. This is intentional — it makes the system readable and editable.

### `SOUL.md` — Who your agent is
Defines personality, tone, and behavior rules. Think of it as the agent's character sheet.

```markdown
# SOUL.md
Be direct. Skip filler phrases like "Great question!". 
Have opinions. Be resourceful before asking.
```

### `USER.md` — Who you are
Context about you: timezone, goals, preferences, projects. The agent reads this every session to understand your world.

```markdown
# USER.md
Name: Alex
Timezone: Europe/Berlin
Current focus: Building a SaaS product for X
Interests: startups, climbing, sci-fi
```

### `AGENTS.md` — Workspace rules and memory system
Defines how the agent should behave in your workspace: memory conventions, communication rules, which channels mean what.

```markdown
# AGENTS.md
Every session: read SOUL.md, USER.md, and today's memory file.
Memory lives in memory/YYYY-MM-DD.md (daily) and MEMORY.md (long-term).
```

---

## Discord as Your Command Center

Discord isn't just chat — it's persistent history, mobile access, organized channels, and a reliable notification system. That's why it's the default interface.

### Why Discord?
- Messages persist (unlike CLI sessions)
- Access from phone, laptop, anywhere
- Channels = projects/topics (no clutter)
- Reactions + threads for organizing work
- Bots + webhooks = proper integration layer

### Setup Steps

1. Go to [discord.com/developers/applications](https://discord.com/developers/applications)
2. Create a new application, add a Bot
3. Enable **Message Content Intent**, **Server Members Intent**, **Presence Intent**
4. Copy your bot token
5. Configure in OpenClaw:
   ```bash
   openclaw config set plugins.entries.discord.config.token YOUR_BOT_TOKEN
   openclaw config set plugins.entries.discord.config.guildId YOUR_SERVER_ID
   ```
6. Invite the bot to your server (use the OAuth2 URL generator in the Developer Portal)
7. Restart: `openclaw gateway restart`

Your agent is now in your Discord server and will respond to messages.

---

## First Prompts to Try

Once your agent is running, try these in Discord:

1. **Get oriented:**
   > "Introduce yourself. What do you know about me so far?"

2. **Test memory:**
   > "My current project is X. Remember this for next session."

3. **Automate a task:**
   > "Every morning at 9am, give me a 3-bullet summary of what I should focus on today."

4. **File management:**
   > "Create a new project folder called 'my-startup' and set up a basic README."

5. **Research:**
   > "Research the top 3 competitors for [your project idea] and summarize in bullet points."

---

## Common Gotchas

**"My API key isn't working"**
→ Make sure you're using an Anthropic key (not OpenAI). Check you're on a paid plan (free tier has limits). Verify with `openclaw config get anthropic.apiKey`.

**"The bot isn't responding in Discord"**
→ Check that all 3 intents are enabled in the Developer Portal. Make sure your bot has Send Messages permission in the channel. Try `openclaw gateway restart`.

**"The gateway keeps stopping"**
→ Run it as a background service: `openclaw gateway start --daemon` or set up a systemd service.

**"I'm getting rate limit errors"**
→ You're on the free tier or hitting Claude's RPM limits. Upgrade your Anthropic plan or add a `--model claude-3-5-sonnet` flag for cheaper calls.

---

*Next up: [Intermediate Setup → Memory, Skills & Discovery Pipeline](02-intermediate.md)*
