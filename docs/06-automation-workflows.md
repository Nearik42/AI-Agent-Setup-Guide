# Automation & Workflows: Your Agent Works While You Sleep

> 💡 **You don't need to type any of this yourself.** Once Claude Code is running, paste this chapter's instructions and say *"set this up for me"* — it reads the guide and executes the commands on your behalf. The code blocks below are what Claude Code runs, not what you type.
You've got an agent that remembers things and responds to messages. Now let's make it proactive.

Automation means your agent does things on a schedule — without you asking. Morning briefings, weekly reports, code health checks. You set it up once, it runs forever.

---

## The Core Concept: Scheduled Tasks

There are two ways to schedule work:

**Cron**: A Unix system that runs commands at specific times. Set it once, it triggers automatically. Perfect for regular tasks.

**Heartbeats**: A lightweight loop where your agent checks for things to do periodically. More flexible, slightly less precise.

Which one? Cron for exact timing ("every day at 8am"), heartbeats for flexible checks ("every 30 minutes, see if anything needs attention").

---

## Cron: The Unix Scheduler

Cron syntax looks weird at first. It's five time fields followed by the command:

```
# ┌───── minute (0-59)
# │  ┌─── hour (0-23)
# │  │  ┌─ day of month (1-31)
# │  │  │  ┌ month (1-12)
# │  │  │  │  ┌ day of week (0-6, Sun=0)
# │  │  │  │  │
  0  8  *  *  *  command-to-run
```

The `*` means "every." So `0 8 * * *` means "at 8:00, every day of every month."

Edit your cron schedule:
```bash
crontab -e
```

This opens a text editor. Add your jobs, save, done.

---

## Morning Briefing: A Real Example

Here's a practical setup: every morning at 8am, your agent summarizes the news, checks your project status, and sends you a Telegram message.

**Step 1: Create your briefing prompt**

```bash
cat > ~/morning-brief-prompt.md << 'ENDMD'
You are my morning briefing assistant. Do the following:

1. Check what day it is and greet me appropriately
2. Read my current projects from ~/projects/CLAUDE.md
3. Give me 3 things I should focus on today based on the current sprint
4. Write a brief motivational line at the end

Keep it under 200 words. This is a morning message, not an essay.
ENDMD
```

**Step 2: Create a notification script**

```bash
cat > ~/scripts/telegram-notify.sh << 'ENDSH'
#!/bin/bash
# Reads from stdin, sends to Telegram
MESSAGE=$(cat)
curl -s -X POST "https://api.telegram.org/bot${TELEGRAM_TOKEN}/sendMessage" \
  -d chat_id="${TELEGRAM_CHAT_ID}" \
  -d text="${MESSAGE}" \
  -d parse_mode="Markdown" > /dev/null
ENDSH
chmod +x ~/scripts/telegram-notify.sh
```

**Step 3: Add to cron**

```bash
crontab -e
```

Add this line:
```
0 8 * * * source ~/.env && claude --print -p "$(cat ~/morning-brief-prompt.md)" | ~/scripts/telegram-notify.sh
```

That's it. Every morning at 8am, you get a briefing on your phone.

---

## The Skills Pattern: Reusable Agent Behaviors

A "skill" is a small markdown file that defines a specific behavior. Your agent reads it and knows how to perform that task.

Think of skills as job descriptions. You write them once, reference them forever.

**Example skill: Weekly Status Report**

```bash
mkdir -p ~/agent-skills
cat > ~/agent-skills/weekly-report.md << 'ENDMD'
# Weekly Status Report Skill

## Trigger
Run every Sunday at 6pm

## What to do
1. Read all project CLAUDE.md files in ~/projects/
2. For each project, summarize:
   - What was accomplished this week (check git log --since=7.days.ago)
   - What's blocked or pending
   - Next week's priority
3. Format as a clean summary
4. Send to Telegram

## Output format
## Weekly Status - [DATE]

**Project: [name]**
- Done: [list]
- Blocked: [list]
- Next: [priority]

Keep each project summary under 50 words.
ENDMD
```

**Cron for weekly report:**
```
0 18 * * 0 ANTHROPIC_API_KEY=your-key claude --print -p "$(cat ~/agent-skills/weekly-report.md)" | ~/scripts/telegram-notify.sh
```

Skills compound over time. You build a library of reliable behaviors your agent can perform on demand or on schedule.

---

## PM2 Scheduled Tasks

If you're already using PM2 to keep your bot running, you can also use it for scheduled tasks — no cron needed.

```javascript
// ecosystem.config.js
module.exports = {
  apps: [
    {
      name: 'agent-bot',
      script: 'agent-bot.js',
      watch: false
    },
    {
      name: 'morning-brief',
      script: 'scripts/morning-brief.js',
      cron_restart: '0 8 * * *',
      watch: false,
      autorestart: false  // Don't restart after one-shot tasks
    }
  ]
};
```

PM2 advantages: everything in one place, good logs, easy restart.
Cron advantages: simpler, no Node.js dependency, works even if PM2 is down.

For serious setups, use both: PM2 for the always-on bot, cron for scheduled tasks.

---

## n8n: Visual Automation for Non-Coders

If all this scripting feels like too much, n8n is worth knowing about. It's a visual workflow builder — like Zapier or Make.com, but self-hostable and free.

**What n8n does well:**
- Connect services visually (Telegram + Claude + Notion + Gmail)
- Triggers: webhooks, schedules, new emails
- No code required for basic workflows
- Self-hosted means no API fees beyond the services you use

**n8n vs. cron scripts:**

| Feature | Cron + Scripts | n8n |
|---------|---------------|-----|
| Setup complexity | Low (text files) | Medium (web UI) |
| Visibility | Terminal logs | Visual dashboard |
| Debugging | Manual | Point-and-click |
| Flexibility | Unlimited | Limited by nodes |
| Self-hostable | Yes (it's just cron) | Yes (Docker) |

**Install n8n:**
```bash
docker run -it --rm \
  --name n8n \
  -p 5678:5678 \
  -v ~/.n8n:/home/node/.n8n \
  n8nio/n8n
```

Open `http://localhost:5678` — you'll see a drag-and-drop workflow builder.

A simple n8n workflow for morning briefings: Schedule node > HTTP Request node (Claude API) > Telegram node. Five minutes to set up, no code.

The honest verdict: cron scripts are more powerful and more educational. n8n is faster to get started with if scripting isn't your thing. You can always migrate later.

---

## Building the Habits That Make Automation Work

Automation only helps if the inputs are good. A few habits that make your agent more effective:

**Keep your CLAUDE.md updated.** Your agent's knowledge is only as good as what you tell it. Spend 2 minutes after each work session updating your project file.

**Write tasks, not hopes.** "Refactor the auth module" is a task. "Make the code better" is not. Scheduled agents do exactly what you tell them — specificity matters.

**Start small, then expand.** One cron job that actually runs is worth ten clever automations you abandoned because they got complicated.

**Check the logs.** Things break. Cron jobs fail silently. Add a log to every script:
```bash
echo "$(date): morning brief sent" >> ~/logs/agent.log
```

---

## Next Step

Your agent now works on a schedule. It's proactive, not just reactive.

Before we add frameworks, let's understand how all of this works under the hood. Next chapter: build a minimal agent from scratch, so you understand what frameworks are actually doing.

---

[← 📱 Always-On Agent](05-always-on-agent.md) · [📚 Table of Contents](../README.md) · [🔧 Build Your Own Agent →](07-custom-agent-diy.md)
