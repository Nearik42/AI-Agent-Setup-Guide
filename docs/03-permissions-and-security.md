# Permissions & Security: Don't Give Your Agent Root

This chapter is boring but important. Skip it and you might accidentally let an agent delete important files or push secrets to GitHub.

See the [official Claude Code security docs](https://docs.anthropic.com/en/docs/claude-code/security) for the full reference.

---

## The Four Permission Modes

Claude Code has four ways to handle permissions:

### Mode 1: Ask Every Time (Default)
```bash
claude
```
Every file write, every command — Claude asks before doing it. Safest. Also slowest. Best for learning.

### Mode 2: Accept Edits
```bash
claude --permission-mode acceptEdits
```
Claude can edit files without asking, but still asks before running shell commands. Good balance for coding tasks.

### Mode 3: Bypass Permissions (With Warning)
```bash
claude --dangerously-skip-permissions
```
No permission checks. Claude just does whatever it decides. Fastest. Also most dangerous.
**Only use this in an isolated environment (see: sandboxing below).**

### Mode 4: Automation Mode (Headless)
```bash
claude --permission-mode bypassPermissions --print -p "your task"
```
For running Claude Code inside scripts and automation pipelines. No interactive prompts.

---

## What Each Mode Feels Like in Practice

Here's what actually happens in each mode during a real session:

### Scenario: "Refactor this Python file and add error handling"

| Step | Ask Every Time | Accept Edits | Bypass/Dangerously Skip |
|------|---------------|--------------|------------------------|
| Read `app.py` | ✅ asks → you approve | ✅ silent | ✅ silent |
| Analyze code | ✅ no approval needed | ✅ no approval needed | ✅ no approval needed |
| Write updated `app.py` | ✅ asks → you approve (~3 sec) | ✅ silent | ✅ silent |
| Run `python app.py` to test | ✅ asks → you approve (~3 sec) | ✅ asks → you approve | ✅ silent |
| Fix error, rewrite file | ✅ asks again (~3 sec) | ✅ silent | ✅ silent |
| Run tests `pytest` | ✅ asks again | ✅ asks again | ✅ silent |
| **Total human approvals needed** | **~6 interruptions** | **~2 interruptions** | **0 interruptions** |
| **Total extra time** | **+2-3 minutes** | **+30 seconds** | **None** |

### Scenario: "Build a complete web app with login, database, and deploy to Vercel"

| Mode | What happens | Risk |
|------|-------------|------|
| Ask Every Time | ~40+ approval prompts over 30 minutes. You become a rubber stamp. | Low but tedious |
| Accept Edits | 5-10 approval prompts for shell commands only. Comfortable. | Low |
| Bypass/Dangerously Skip | Zero prompts. 10 minutes. Done. | Medium — but manageable with Docker |

### The Real Cost of "Ask Every Time" for Automation

If you're running a background agent (overnight task, scheduled job), "ask every time" means the agent **stops and waits** until you approve. Your 2am scheduled job? It's been sitting idle since 2:00:03am, waiting for a keypress you'll never give.

For autonomous work: you need `bypassPermissions`. But that means you need a safety net.

> 💬 **Community:** [@Voxyz_ai](https://x.com/Voxyz_ai/status/2032821550651785611) — "The real unlock is trusting the agent to run unsupervised — trust isn't faith, it's config." The permission model is your config.

---

## What Can Go Wrong?

Real examples of what agents can do with too many permissions:

**Accidental deletion**: "Clean up unused files" deletes something important
**Git disasters**: "Push all changes" pushes to wrong branch, or with secrets in files
**Infinite loops**: Agent creates files, sees them as "new context," creates more files
**Cost explosions**: Agent calls an API in a loop — unexpected bill
**Secret exposure**: Agent reads `.env` file and includes contents in a commit message

The solution isn't to be paranoid — it's to be intentional about what permissions you grant.

---

## The Principle of Least Privilege

Give your agent exactly the permissions it needs for the task. Nothing more.

- **For learning/exploration**: Default mode (ask every time)
- **For a specific coding task**: `--permission-mode acceptEdits`
- **For automation**: `bypassPermissions` + Docker sandbox

---

## Sandboxing: Isolation for Autonomous Agents

When you want to run an agent autonomously (no babysitting), isolate it.

### Option 1: Separate Project Folder (Minimum)
Create a dedicated folder. The agent can only damage what's in that folder.

```bash
mkdir agent-workspace
cd agent-workspace
claude --dangerously-skip-permissions
```

### Option 2: Docker Container (Recommended for Automation)
The agent runs inside a container. Even if it goes rogue, it can't touch your main system.

```bash
docker run -it --rm \
  -e ANTHROPIC_API_KEY=$ANTHROPIC_API_KEY \
  -v $(pwd)/workspace:/workspace \
  -w /workspace \
  node:20 \
  bash -c "npm install -g @anthropic-ai/claude-code && claude --dangerously-skip-permissions"
```

### Option 3: VPS (For Production Agents)
Run your agent on a separate server. If something goes wrong, it's isolated from your main machine. This is what serious setups use. (We cover this in later stages.) Good options: [Hetzner Cloud](https://www.hetzner.com/cloud) or [DigitalOcean](https://digitalocean.com).

---

## Claude Code's Built-in Sandbox (macOS/Linux)

When sandboxing is enabled, Claude Code uses OS-level isolation:
- **Linux**: Bubblewrap — restricts filesystem and network access
- **macOS**: Apple Seatbelt — restricts what files/processes the agent can touch

This is automatic on newer Claude Code versions. You can verify:
```bash
claude --version  # Check if sandboxing is mentioned
```

---

## API Key Security Checklist

- [ ] API key is in `.env` file, not hardcoded
- [ ] `.env` is in `.gitignore`
- [ ] Never pasted key into a chat or email
- [ ] Set spending limits in [Anthropic console](https://console.anthropic.com)
- [ ] Created separate keys for different projects

Get or manage your API keys at [console.anthropic.com](https://console.anthropic.com).

---

## Permission Levels for Different Scenarios

| Scenario | Recommended Mode | Why |
|----------|-----------------|-----|
| Learning/experimenting | Default (ask every time) | Understand what it's doing |
| Coding a known project | acceptEdits | Fast but controlled |
| Quick one-shot task | bypassPermissions + small folder | Isolated scope |
| Background automation | bypassPermissions + Docker | Full isolation |
| Multi-agent orchestration | Per-agent policies | Each agent has minimum required access |

---

## The Orchestrator Pattern (Preview)

In advanced setups, an orchestrator agent decides what permissions sub-agents get:

```
Orchestrator (read-only + messaging):
+-- Research Agent (web search only, no file writes)
+-- Coding Agent (write to /workspace, no network)
+-- Review Agent (read files, no modifications)
```

This is the principle: trust flows down, but permissions don't automatically cascade. Each agent gets exactly what it needs. We build this in Stage 9.

---

## The Bottom Line

- **Default mode** for exploration: always safe
- **bypassPermissions** only with isolation: Docker, VPS, or dedicated folder
- **API keys**: `.env` file, always
- **Spending limits**: Set them in the [Anthropic console](https://console.anthropic.com) now

With that sorted, let's give your agent a memory.

---

## When Using Bypass/Dangerously Skip: A Security Checklist

Here's a real-world CLAUDE.md security policy that works in practice:

### 1. Constrain via CLAUDE.md — Your Most Powerful Tool

The agent reads this before every action. Use it to define what's off-limits:

```markdown
# Security Constraints

## What I am NOT allowed to do:
- Never commit or push to git without explicit permission
- Never send emails or messages without confirmation
- Never delete files — use trash/archive instead
- Never modify files outside /workspace
- Never access credentials files (.env, secrets/, credentials/)
- Never make API calls to payment services

## Allowed bash commands (whitelist approach):
- File operations: read, write, create in /workspace only
- Git: status, diff, log (read-only) — NOT push, commit
- Package managers: npm install, pip install
- Testing: pytest, npm test
- Build tools: npm run build, make

## If unsure: ask before acting
```

### 2. Docker Isolation — The Nuclear Option (Safest)

```bash
# Agent can only damage what's in /workspace
# Your main system is completely safe
docker run -it --rm \
  -e ANTHROPIC_API_KEY=$ANTHROPIC_API_KEY \
  -v $(pwd)/agent-workspace:/workspace \
  --network none \
  --memory="2g" \
  node:20 bash -c "npm install -g @anthropic-ai/claude-code && claude --dangerously-skip-permissions"
```

### 3. Network Restrictions — Prevent Data Exfiltration

```bash
# Allow only specific domains
# In CLAUDE.md:
# - Only make HTTP requests to: api.anthropic.com, api.github.com
# - No other network calls
```

Or at OS level: use a firewall rule to restrict the agent's process.

### 4. Git Safety — The Most Common Accident

```bash
# Add to your project's .claude/rules/git.md:
# NEVER run: git push, git commit without explicit user confirmation
# ALWAYS run: git diff --cached --name-only before any commit
# Check for .env files in staged changes

# Set up a pre-commit hook:
echo '#!/bin/bash
git diff --cached --name-only | grep -E "\.env|secrets|credentials" && echo "❌ Potential secret file detected!" && exit 1 || exit 0' > .git/hooks/pre-commit
chmod +x .git/hooks/pre-commit
```

### 5. Spending Limits — Set in Anthropic Console

- Go to [console.anthropic.com](https://console.anthropic.com) → Billing → Usage Limits
- Set monthly hard limit ($20-50 is reasonable for personal use)
- Enable email alerts at 80% usage

### 6. Separate API Keys per Agent — If One Leaks, Others Are Safe

- `ANTHROPIC_KEY_PERSONAL` — for personal coding
- `ANTHROPIC_KEY_AGENT_GOOPER` — for your always-on agent
- `ANTHROPIC_KEY_AUTOMATION` — for scheduled tasks

### 7. Secret Scanner — Before Any Commit

```bash
npx ai-secret-scan ./  # Scans for leaked API keys
# Or add to pre-push hook
```

### 8. The Orchestrator Permission Model (Advanced)

When you have multiple agents, the orchestrator decides what each sub-agent can do:

```
Orchestrator: read files + send messages only
├── Research Agent: web search only, cannot write files
├── Coding Agent: write to /workspace, cannot access network
└── Review Agent: read-only, cannot modify anything
```

This way, even if one agent is compromised or makes a mistake, the damage is contained.

---

[← 💰 Models & Costs](02b-models-and-costs.md) · [📚 Table of Contents](../README.md) · [🧠 Persistent Memory →](04-persistent-memory.md)


---

## ⚠️ The Real Danger: `rm -rf` and Destructive Commands

This is not hypothetical. AI agents running with unrestricted permissions have caused real data loss.

### What Can Go Wrong

When an agent has full system access and no guardrails, a single misunderstood instruction can trigger:

```bash
rm -rf /var/www/html        # Deletes entire website
rm -rf ~/projects           # Deletes all your work
rm -rf /                    # Deletes the entire filesystem (yes, agents have done this)
DROP TABLE users;           # Deletes your entire user database
git push --force origin main  # Overwrites production with broken code
```

The agent doesn't hesitate. It doesn't double-check. It executes.

### Real Incidents

**The "clean up unused files" disaster:**
A developer asked their agent to "clean up unused files in the project." The agent — running with `--dangerously-skip-permissions` on the production server — interpreted old log files, user uploads, and a backup directory as "unused." It deleted 40GB of user data in 90 seconds. No undo. No recycle bin. Gone.

**The "refactor the database" incident:**
An agent tasked with "removing duplicate entries from the database" wrote and executed a SQL script that was off by one in its logic. It deleted 60% of the production database before the developer noticed the CPU spike. Recovery took 6 hours and cost $4,000 in emergency consulting.

**The infinite loop bill:**
An agent was set up to "monitor the API and retry failed requests." A bug in the retry logic caused it to loop — making 50,000 API calls in 3 minutes and generating a $2,800 bill before the spending limit kicked in.

These are not edge cases. They are the predictable outcome of unrestricted agents.

---

## The Defense: Backups First, Always

Before you give any agent access to anything important:

### 1. VPS/Server Snapshots
Most VPS providers offer one-click snapshots:
- **Hetzner**: Server → Backups → Enable automatic backups (€0.80/month for a CX22)
- **DigitalOcean**: Droplet → Backups → Enable (20% of droplet cost)

Enable this **before** your first agent session on a server. A snapshot from 24 hours ago is the difference between "minor incident" and "days of recovery."

### 2. Database Backups
```bash
# Add to crontab — runs every night at 2am
0 2 * * * pg_dump mydb > /backups/mydb_$(date +%Y%m%d).sql
0 2 * * * mysqldump mydb > /backups/mydb_$(date +%Y%m%d).sql
```

### 3. Git Before Everything
Before any agent session that touches code:
```bash
git add -A && git commit -m "pre-agent snapshot $(date)"
```
Takes 5 seconds. Gives you a rollback point.

### 4. Never Run Agents as Root
```bash
# Bad: agent running as root
sudo claude --dangerously-skip-permissions

# Good: agent running as restricted user
useradd -m agentuser
su agentuser -c "claude --dangerously-skip-permissions"
```

Root access means `rm -rf /` works. Non-root access means it fails safely.

### 5. Explicitly Forbid Destructive Commands in CLAUDE.md
```markdown
## Commands I am NEVER allowed to run:
- rm -rf (any variant)
- DROP TABLE or DROP DATABASE
- git push --force
- Any command that deletes files without explicit confirmation
- sudo or su (no privilege escalation)

## Before deleting anything:
Always ask for confirmation first, even in bypass mode.
```

---

