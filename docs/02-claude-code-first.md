# Claude Code: Your First AI Agent (10 Minutes)

[Claude Code](https://docs.anthropic.com/en/docs/claude-code/) is an AI agent that runs in your terminal. ([quickstart guide](https://docs.anthropic.com/en/docs/claude-code/quickstart)) Unlike ChatGPT, it doesn't just answer questions — it reads your files, writes code, runs commands, and builds things.

This is your first aha moment.

> 💬 **Community:** [@lukepierceops](https://x.com/lukepierceops/status/2032813461399392412) — "Automation consultants charge $15K for what Claude Code now does in 2 hours."

---

## What Makes Claude Code Different

When you ask ChatGPT to "write a Python script," it shows you the code. You copy it. You paste it. You run it. You hit an error. You go back to ChatGPT.

When you ask Claude Code to "write a Python script," it:
1. Creates the file
2. Runs it
3. Sees the error
4. Fixes it
5. Runs it again
6. Tells you it's done

It operates in your actual file system. It's an agent, not a chatbot.

> 💬 **Community:** [@nurijanian](https://x.com/nurijanian/status/2032124503330058696) — "PM who uses Claude Code/Cursor to build and execute research, strategy, and discovery — not just answer questions."

---

## Installation

```bash
# Install Claude Code globally
npm install -g @anthropic-ai/claude-code

# Verify installation
claude --version
```

### Set up your API key:
```bash
export ANTHROPIC_API_KEY=your-key-here

# Make it permanent (Mac/Linux):
echo 'export ANTHROPIC_API_KEY=your-key-here' >> ~/.zshrc  # or ~/.bashrc
source ~/.zshrc
```

Get your API key from the [Anthropic console](https://console.anthropic.com). If you don't have Node.js, install it via [FNM (Fast Node Manager)](https://github.com/Schniz/fnm) — fastest and cleanest way.

### First launch:
```bash
# Navigate to any project folder (or create one)
mkdir my-first-agent && cd my-first-agent

# Start Claude Code
claude
```

You'll see a prompt. You're in.

---

## Your First 5 Commands

Try these in order. Watch what happens.

**1. Ask it to create a file:**
```
> Create a simple Python script that shows today's date and weather summary placeholder
```
Claude Code will create `weather.py` in your folder. Actually create it.

**2. Ask it to read something:**
```
> Read the file you just created and explain what it does
```
It reads the actual file, not from memory.

**3. Ask it to fix something:**
```
> Add a function that calculates how many days until the end of the year
```
It edits the file. You can open it and see the changes.

**4. Ask it a multi-step task:**
```
> Create a new file called expenses.py that reads a list of expenses from a CSV,
> calculates totals by category, and outputs a summary. Include a sample CSV with test data.
```
Watch it think through the problem, create multiple files, and test them.

**5. Give it a real task:**
```
> Look at what files are in this folder and suggest what project this could become
```

---

## Understanding the Permission Prompts

When Claude Code wants to do something, it asks permission first (by default). See the [full permissions and security model](https://docs.anthropic.com/en/docs/claude-code/security) for details.

```
Claude wants to: run `python weather.py`
Allow? [y/n/always/never]:
```

- `y` — allow once
- `n` — deny
- `always` — always allow this type of command
- `never` — never allow this type of command

This is intentional. You're in control. (We'll cover the full permission model in the next chapter.)

---

## Useful Modes

```bash
# Interactive mode (default)
claude

# Run a one-shot task and exit
claude -p "Write a haiku about Python" --print

# Continue your last conversation
claude --continue

# Start with a specific file in context
claude --add-file mycode.py
```

---

## The CLAUDE.md File (Preview)

If you create a file called `CLAUDE.md` in your project folder, Claude Code reads it automatically at the start of every session. This is how persistent memory starts — but we'll build this properly in Stage 4.

Quick taste:
```bash
cat > CLAUDE.md << 'ENDMD'
# Project Context
This is my expense tracker project.
Language: Python.
Style: Simple, readable, no unnecessary dependencies.
ENDMD
```

Now every Claude Code session in this folder starts with that context.

> 💬 **Community:** [@cathrynlavery](https://x.com/cathrynlavery/status/2032923364424057007) — "Your AI skills shouldn't live in 3 different places." Use CLAUDE.md as your single source of project truth.

> 💬 **Community:** [@toddsaunders](https://x.com/toddsaunders/status/2031346598132011154) — Built a `/investor-pitch` Claude Code skill — a great example of how far custom skills can go once you have the pattern down.

---

## The Planning Mindset: Think Before You Act

One of the most valuable lessons from real Claude Code usage: **the more complex the task, the more time you should spend planning before letting the agent execute.**

Claude Code has a `plan` mode that changes how the agent works:

```bash
# Start an interactive session in plan mode
claude --plan
```

In plan mode, Claude Code:
1. Analyzes the task
2. Explains step-by-step what it intends to do
3. **Waits for your approval** before executing anything
4. You can refine, correct, or abort before any files are touched

**Why this matters:** A wrong plan caught in 2 minutes costs nothing. A wrong plan executed over 30 minutes might touch 40 files and create a mess that takes an hour to undo.

### The Rule of Thumb

| Task complexity | Recommended approach |
|----------------|---------------------|
| Small, clear task ("fix this bug") | Execute directly |
| Medium task ("add login feature") | Quick plan review, then execute |
| Large task ("refactor this module") | Thorough plan discussion, iterate until right, then execute |
| Architectural change | Extended planning session, possibly multiple iterations |

### Plan Mode vs. Messaging Setup

**Important caveat:** Plan mode exists in the Claude Code terminal. If you're using a messaging-based setup (Discord, Telegram bot), you typically only see the final output — not the agent's thinking process.

This is a fundamental tradeoff:
- **Terminal**: Full visibility into planning, reasoning, and intermediate steps. Best for complex or risky tasks.
- **Discord/Telegram**: Convenient, mobile-friendly, but less visibility. Better for well-defined, lower-risk tasks.

> 💡 **Practical tip from experience:** When you first try something new with an agent, always use the terminal with plan mode. Once you trust how the agent handles that type of task, you can move it to an automated/messaging setup.

---

## When Claude Code Shines

- Refactoring code you don't fully understand
- Writing scripts for one-off tasks
- Debugging errors (paste the error, it reads the code, it fixes it)
- Exploring unfamiliar codebases
- Building small tools quickly

## When It Struggles

- Very large codebases (context window limits)
- Tasks requiring real-time data without tools
- Things that need a browser UI (use browser tools for that)

---

## Next Step

You now have a working AI agent. It's stateless — each session starts fresh.

The next chapter covers permissions and security. Then we give it memory.
---

[← 💻 Terminal Basics](01-terminal-basics.md) · [📚 Table of Contents](../README.md) · [💰 Models & Costs →](02b-models-and-costs.md)
