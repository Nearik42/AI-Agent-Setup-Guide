# 🟡 Intermediate: Memory, Skills & Discovery Pipeline

> Goal: Make your agent actually remember things, extend it with skill plugins, and run your first idea through the full discovery pipeline.

---

## Part 1: The Memory System

### The Problem

By default, AI has no memory between sessions. You tell it your project context, it helps you, session ends — next time it's a blank slate again. This is the #1 frustration with AI tools.

### The Solution: Two-Layer Memory

OpenClaw uses a file-based memory system. The agent reads specific markdown files at session start, giving it continuity across sessions.

**Layer 1: Daily Logs** — `memory/YYYY-MM-DD.md`
Raw notes from each session. What happened, what was decided, what's in progress.

```markdown
# memory/2026-03-19.md

## Today
- Researched 3 competitors for MA Advisor
- Decided to use Next.js for frontend
- Erik asked to revisit pricing model next session
- TODO: follow up on API rate limits question
```

**Layer 2: Long-Term Memory** — `MEMORY.md`
Curated, distilled knowledge. The important stuff that should persist forever — decisions, context, preferences.

```markdown
# MEMORY.md

## Projects
- MA Advisor: Due diligence SaaS for M&A professionals. Stack: Next.js + Supabase. Status: MVP in progress.
- ExoExplorer: Space data visualization tool.

## Preferences
- Code reviews: be direct, don't sugarcoat issues
- Morning briefings: 3 bullets max, only what matters today

## Key Decisions
- 2026-03-01: Chose Anthropic over OpenAI for API (better tool use, reasoning)
```

### How It Works

The agent's startup sequence reads these files automatically. You configure this in `AGENTS.md`:

```markdown
Every session:
1. Read SOUL.md
2. Read USER.md  
3. Read memory/YYYY-MM-DD.md (today + yesterday)
4. In main session: also read MEMORY.md
```

### What to Put in MEMORY.md

- **Decisions made** (and why) — prevents re-litigating the same choices
- **Project context** — current status, key constraints, goals
- **Your preferences** — how you want things done, tone, format
- **Lessons learned** — what failed, what works
- **Important relationships** — who's involved, what matters to them

The goal: if you woke up with amnesia, MEMORY.md should get you back to 80% context in 5 minutes.

---

## Part 2: The Skills System

### What Are Skills?

Skills are pre-built behavior modules — like plugins or slash commands for your agent. Each skill is a folder with a `SKILL.md` that tells the agent how to handle specific tasks.

The agent auto-detects which skill applies based on what you're asking. You don't have to invoke them manually.

### Examples of Available Skills

| Skill | What it does |
|-------|-------------|
| `weather` | Fetch current weather and forecasts |
| `github` | Interact with repos, PRs, issues via `gh` CLI |
| `code-review` | Systematic code reviews with severity levels |
| `tavily` | AI-optimized web search |
| `deep-research` | Multi-step research pipeline (~2 min, comprehensive) |
| `coding-agent` | Delegate coding tasks to Claude Code or Codex |
| `discovery-pipeline` | Full idea-to-spec pipeline (see Part 3) |

### How to Install Skills

```bash
# Via clawhub (skill marketplace)
clawhub install weather
clawhub install github
clawhub install tavily

# Skills live in your workspace's skills/ folder
ls skills/
```

### How to Create Custom Skills

Every skill is just a folder:

```
skills/
  my-skill/
    SKILL.md        ← instructions for the agent
    scripts/        ← optional helper scripts
    references/     ← optional reference docs
```

`SKILL.md` structure:
```markdown
## Description
Brief description that triggers auto-detection.

## Instructions
Step-by-step instructions for the agent.

## Examples
Concrete examples of when/how to use this.
```

Good skill candidates: anything you do repeatedly with specific rules (e.g., "how I format meeting notes", "how I write commit messages", "my code review checklist").

---

## Part 3: The Discovery Pipeline (Idea → Implementation)

This is one of the most powerful patterns in the setup. It takes a raw idea from your brain to an implementation-ready task list — with research and product thinking baked in.

### The 6 Phases

**Phase 0: Capture**
Drop a raw idea into your `#idea-[name]` Discord channel. No structure needed yet. Just brain dump.

> "I want to build a tool that helps M&A advisors do due diligence faster. Currently it takes weeks of manual research."

**Phase 1: Problem Definition**
The agent helps you crystallize the actual problem. Who has it? How painful is it? Is it worth solving?

Output: clear problem statement, target user, success criteria.

**Phase 2: Research**
The agent runs deep research using web search and MCP research tools. Competitors, market size, existing solutions, user pain points from Reddit/forums.

Output: research summary, competitive landscape, validated (or invalidated) assumptions.

**Phase 3: PRD (Product Requirements)**
Structured product requirements document. What are we building exactly? What's in scope? What's not?

Output: PRD with user stories, features, constraints, success metrics.

**Phase 4: Tech Spec**
Architecture decisions, stack choices, API design, file structure. How are we building it?

Output: tech spec with ADRs (Architecture Decision Records), dependency matrix.

**Phase 5: Task Breakdown → Handoff**
Break the spec into concrete tasks with effort estimates, dependencies, and risk flags. Ready to hand to a dev agent.

Output: task list in `tasks/` folder, ready for `coding-agent` skill.

### Real Example: MA Advisor

Here's how the MA Advisor (Due Diligence Tool) went through the pipeline:

1. **Capture:** "I want to automate M&A due diligence — currently takes weeks, lots of repetitive research"

2. **Problem Definition:** Target = boutique M&A advisory firms (5–50 people), pain = junior analysts spend 60% of time on research that could be automated, success = reduce initial diligence from 3 weeks to 3 days

3. **Research:** Found 3 competitors (Kira Systems, Luminance, eBrevia) — all enterprise-priced ($50K+/year). Gap: no tool for boutique firms at $500–2K/month.

4. **PRD:** Core features = document upload + AI extraction, automated financial analysis, comparable company research, risk flagging. Non-scope: deal management, CRM.

5. **Tech Spec:** Next.js + Supabase + Anthropic API + LlamaIndex for document parsing. VPS deployment.

6. **Tasks:** 23 tasks broken down, 4 sprints, first sprint = auth + document upload + basic extraction.

Total time from idea to ready-to-build: ~4 hours of back-and-forth with the agent.

---

*Next up: [Advanced — Multi-Agent Architecture & Dev Pipeline →](03-advanced.md)*
