# 🔴 Advanced: Multi-Agent Architecture & Dev Pipeline

> Goal: Run a specialized team of agents that coordinate, delegate, and build software autonomously.

---

## Part 1: Multi-Agent Architecture

### The System

Instead of one agent doing everything, you build specialized agents with clear roles — like a real team.

```
Discord (#general, #idea-*, #projects)
       │
       ▼
┌─────────────────────────────────────┐
│         ORCHESTRATOR (gooper)        │
│    Claude Opus — Full Context        │
└──────────────┬──────────────────────┘
               │ delegates to
     ┌─────────┼──────────────┐
     ▼         ▼              ▼
┌─────────┐ ┌──────┐ ┌──────────┐
│gooper-  │ │ dev  │ │ analyst  │
│  lite   │ │agent │ │  agent   │
│(Sonnet) │ │(Code)│ │(Research)│
└─────────┘ └──────┘ └──────────┘
```

### Agent Roles

**gooper (Orchestrator)**
- Model: Claude Opus (most powerful, most expensive)
- Role: CEO/CTO brain. Plans, decides, delegates. Never writes code directly.
- When to use: project decisions, architecture choices, reviewing outputs, anything with high stakes
- Cost: ~$15/M input tokens, $75/M output

**gooper-lite (Fast Tasks)**
- Model: Claude Sonnet (fast, cost-effective)
- Role: Day-to-day ops. Discord management, quick research, status updates, routine tasks
- When to use: most normal interactions, brainstorming, quick questions
- Cost: ~$3/M input, $15/M output

**dev (Coding Agent)**
- Model: Claude Code (specialized for code)
- Role: Actually builds things. Reads codebases, writes code, creates PRs, runs tests.
- When to use: any coding task, always in a git worktree
- Cost: similar to Sonnet

**analyst (Research Agent)**
- Model: Claude Sonnet or Opus depending on depth needed
- Role: Deep research, competitive analysis, market research, data synthesis
- When to use: when research depth matters more than speed

### How They Communicate

**Discord Channels** — Primary communication surface. Each project has a channel. Agents post updates, ask questions, deliver results. You see everything.

**Shared Files** — Agents read/write to the same workspace files (`tasks/`, `research/`, `memory/`). File system = shared state.

**sessions_send** — Technical internal routing. For passing structured data between agent sessions (not for decisions — those go through Discord).

**Convention:** Any significant action = a Discord post. "Invisible work = doesn't exist." Everything important should be visible to the human.

### Cost Considerations

Rule of thumb: use the cheapest model that can do the job.

| Task | Recommended Model |
|------|------------------|
| Coding, file edits | Sonnet or Claude Code |
| Research, analysis | Sonnet |
| Architecture decisions | Opus |
| Reviewing important PRs | Opus |
| Casual chat, quick tasks | Sonnet |

Pro tip: Set up a routing rule in your orchestrator — "call out when I'm using the wrong model tier." One line in MEMORY.md can save you 10x on costs.

---

## Part 2: The Dev Pipeline

### How Code Gets Built

When you say "build feature X" in a project channel, here's what happens:

**Step 1: Task Creation**
gooper creates a structured task file in `tasks/`:
```markdown
# Task: Add PDF upload feature

## Goal
Allow users to upload PDF documents for analysis.

## Acceptance Criteria
- Upload endpoint accepts PDF up to 50MB
- Files stored in Supabase Storage
- Processing triggered automatically on upload
- Error handling for corrupted files

## Tech Notes
- Use existing Supabase client
- Add to /api/upload route
- Reference: docs/architecture.md
```

**Step 2: Dev Agent Picks Up**
The dev agent (Claude Code) reads the task, checks out a git worktree, and starts building:

```bash
# Claude Code runs in isolation
git worktree add ../feature-pdf-upload -b feature/pdf-upload
cd ../feature-pdf-upload
# ... builds the feature ...
```

Using worktrees means: main branch stays clean, multiple features can build in parallel, easy rollback.

**Step 3: Auto-Review**
When the dev agent finishes, it runs automated checks:
- TypeScript compilation: `tsc --noEmit`
- Tests: `npm test`
- Linting: `npm run lint`

If checks fail, it tries to fix. If it can't fix after 3 attempts, it flags for human review.

**Step 4: PR Created**
```bash
gh pr create --title "feat: PDF upload" --body "Implements task/pdf-upload.md"
```

PR description includes: what was built, how it was tested, link to task file, any caveats.

**Step 5: gooper Reviews**
The orchestrator reviews the PR — checks it matches the task spec, reviews code quality, approves or requests changes. Human gets final say on merge.

### Tools in the Dev Pipeline

- **Claude Code** — the coding agent itself (`npm install -g @anthropic-ai/claude-code`)
- **gh CLI** — GitHub CLI for PR creation, issue management
- **git worktrees** — parallel development without branch conflicts (`git worktree add`)
- **OpenClaw coding-agent skill** — orchestration layer that manages the above

---

## Part 3: Automation & Heartbeats

### What is a Heartbeat?

A heartbeat is a periodic check-in. Every 30 minutes (configurable), the agent wakes up, reads `HEARTBEAT.md`, and decides what to do.

`HEARTBEAT.md` is a small checklist of things to check:

```markdown
# HEARTBEAT.md

## Check on each heartbeat (rotate, 2-4x per day):
- [ ] Any urgent emails?
- [ ] Calendar events in next 2 hours?
- [ ] Any project PRs waiting for review?
- [ ] Running tasks still alive?

## Do not disturb after 23:00 or before 08:00 unless urgent.
```

If nothing needs attention: respond `HEARTBEAT_OK` and go back to sleep.
If something needs attention: proactively reach out in Discord.

### Cron Jobs

For exact timing (daily briefings, scheduled reports), use cron:

```bash
# Morning briefing at 9am Bangkok time (2am UTC)
0 2 * * * cd ~/workspace && openclaw run "Read TOOLS.md briefing config, fetch news, post to #daily-briefing"

# Weekly review every Sunday at 7pm
0 12 * * 0 cd ~/workspace && openclaw run "Generate weekly review from memory files, post to #weekly-review"
```

**Heartbeat vs Cron:**
- Use **heartbeat** when: checks can batch together, timing can drift, you want conversational context
- Use **cron** when: exact timing matters, task is isolated, you want it to run regardless of main session state

### Morning Briefing Pattern

A real example from the live setup:

```
Every day at 07:30 local time:
1. Fetch top news for the day's category (rotating: AI/Space/Gaming/Business/Science)
2. Check calendar for meetings in next 24h
3. Check any urgent GitHub notifications
4. Post 3-5 bullet summary to #daily-briefing
5. Suggest one project idea or insight for the day
```

The output in Discord looks like:
> 🌅 **Good morning! Thursday briefing:**
> 
> **Today's focus: Startups/Business**
> - Anthropic raised $4B at $60B valuation
> - YC W26 batch announced — 180 AI companies
> - Stripe adds AI payment routing
> 
> **Your day:**
> - No meetings scheduled
> - 2 PRs waiting review on MA Advisor
> 
> **Idea of the day:** What if MA Advisor had a "red flags auto-detect" feature that flags common deal-killers in uploaded docs?

---

*That's the full stack. Now go to [Twitter/X Resources →](04-twitter-index.md) for curated learning materials, or check the [Glossary →](05-glossary.md) for term definitions.*
