# Persistent Memory: The Agent That Remembers You

Every Claude Code session starts fresh. But what if it could remember your projects, preferences, and decisions from previous sessions?

That's what this chapter is about.

---

## The Problem

Without memory:
- Every session: re-explain your project
- Every session: re-explain your preferences
- Every session: re-explain what you tried yesterday

With memory:
- Agent opens session, reads context, continues where you left off
- Knows your tech stack, your coding style, your current goals
- Remembers what worked and what didn't

---

## The CLAUDE.md Pattern

Claude Code automatically reads `CLAUDE.md` if it exists in the current folder. This is your project memory.

**Create your first one:**
```bash
cat > CLAUDE.md << 'ENDMD'
# About Me
- Name: [Your name]
- Location: [City, Timezone]
- Goal: Learning to build AI agents

# My Projects
- expense-tracker: Python script for tracking monthly expenses (in progress)
- ideas: list of project ideas in ideas.md

# Preferences
- Code style: Simple, readable, well-commented
- Language: Python preferred, Node.js fine
- No unnecessary dependencies

# Current Focus
Working on the expense tracker. Next step: add a web interface.

# What I've Tried
- Tried Flask for web UI, got confused with routing
- Decided to use FastAPI instead — cleaner syntax
ENDMD
```

Now every Claude Code session in this folder opens with this context. The agent knows who you are, what you're building, and where you left off.

---

## The Two-Layer Memory System

For more advanced setups, use two files:

### Layer 1: Long-term (CLAUDE.md)
Stable information that doesn't change often:
- Who you are, your goals
- Project descriptions
- Preferences and constraints
- Lessons learned

### Layer 2: Daily logs (memory/YYYY-MM-DD.md)
What happened today:
- Decisions made
- Problems encountered
- Next steps

```bash
mkdir memory
date_str=$(date +%Y-%m-%d)
cat > memory/$date_str.md << 'ENDMD'
# Today's Log

## What I did
- Set up Claude Code (finally!)
- Created the CLAUDE.md pattern
- Built first expense tracker prototype

## Decisions
- Using FastAPI instead of Flask (simpler routing)
- Storing data in SQLite (no server needed)

## Next session
- Add the web interface
- Test CSV import
ENDMD
```

---

## Telling Claude to Update Its Own Memory

The real power: instruct the agent to maintain its own memory.

Add to your CLAUDE.md:
```markdown
# Memory Instructions
At the end of each session:
1. Update this CLAUDE.md with any new decisions or context
2. Create/update memory/YYYY-MM-DD.md with what happened today
3. If something is important to remember long-term, add it to the "Lessons Learned" section
```

Now the agent maintains its own memory. Over time, it becomes more useful as it knows more about you.

---

## The Obsidian Pattern (Advanced)

Some people connect their agent to Obsidian (a note-taking app). The agent reads and writes Obsidian notes, treating your knowledge base as its memory. Referenced from: @om_patel5 and @micLivs on Twitter.

Tools like **napkin** make this easier — it's essentially Obsidian + an agent-optimized CLI. But CLAUDE.md is simpler and works fine for most people.

---

## Modular Memory Files

For complex projects, split memory across files:

```
.claude/
+-- CLAUDE.md          # Core project context (always loaded)
+-- rules/
    +-- coding.md      # Coding standards
    +-- decisions.md   # Architecture decisions
    +-- api.md         # API-specific rules
```

Claude Code automatically includes files in `.claude/rules/` based on what you're working on.

---

## What Good Memory Looks Like

After 2 weeks of using the memory pattern, your CLAUDE.md might look like:

```markdown
# Project: Expense Tracker
**Status**: Web interface working, now adding export features

## Tech Stack
- Backend: FastAPI (Python 3.11)
- Database: SQLite via SQLAlchemy
- Frontend: HTMX + Tailwind (no React — too heavy)

## Key Decisions
- SQLite because: no server setup, easy backup, fast enough for personal use
- HTMX because: decided against React on 2026-03-01 — overkill for this project
- File structure: decided to keep models and routes in same file until it grows

## Lessons Learned
- Always run `python -m pytest` before saying something is done
- The `category` field needs to handle emojis (broke once)
- Import expects ISO date format, not German format

## Current Sprint
- [ ] CSV export
- [ ] Budget alerts
- [ ] Mobile-friendly UI
```

This is a living document. The agent reads it, updates it, and becomes more useful every session.

---

## Next Step

You have a persistent agent that remembers your projects. But it only works when you open a terminal.

Next: make it available 24/7 via Telegram or Discord.
