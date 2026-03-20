---
title: "🔧 Build Your Own Agent"
nav_order: 11
---

# Build Your Own Agent: From Scratch

Before you pick a framework, build one yourself. Not because you'll use it in production — you probably won't. But because understanding how agents actually work makes you dramatically better at using them.

This chapter builds a minimal but real agent in ~100 lines of Python. It has memory, messaging integration, and scheduled checks. It's basically a tiny OpenClaw.

---

> 💬 **Community:** [@karpathy](https://x.com/karpathy/status/2030371219518931079) — "I packaged up the autoresearch project into a new self-contained minimal repo." Building from scratch is how you learn what frameworks are actually doing.

> 💬 **Community:** [@omarsar0](https://x.com/omarsar0/status/2030771811705872435) — "OpenDev is an 81-page paper covering scaffolding for terminal-based coding agents." The best reference for understanding agent architecture.

## What Frameworks Actually Do

When people say "I'm using CrewAI" or "I'm using OpenClaw," they mean they're using a framework that handles:

1. **The agent loop**: input → LLM → action → observe → repeat
2. **Tool management**: letting the LLM call functions (web search, file read, etc.)
3. **Memory**: loading and saving context between sessions
4. **Messaging**: connecting to Telegram, Discord, etc.
5. **Scheduling**: running tasks on a timer
6. **Error handling**: retrying, logging, graceful failure

That's it. Everything else is configuration and polish.

You can build all of this yourself. The question is whether you should. (Spoiler: for production, no. For learning, absolutely.)

---

## The Agent Loop

Every agent runs the same fundamental loop:

```
1. Get input (from user, or from a scheduled trigger)
2. Send input to LLM with current context
3. LLM returns either:
   a. A final response → send to user, done
   b. A tool call → execute the tool, add result to context, go to step 2
4. Repeat until done
```

That's it. The "magic" of agents is just this loop running repeatedly, with the LLM deciding when to use tools vs. when to give a final answer.

---

## Build It: A Minimal Agent in Python

```python
#!/usr/bin/env python3
"""
minimal_agent.py - A 100-line persistent agent with memory, tools, and messaging
"""

import os
import json
import anthropic
from datetime import datetime
from pathlib import Path

# ---- Config ----
MEMORY_FILE = Path("CLAUDE.md")
LOG_FILE = Path(f"memory/{datetime.now().strftime('%Y-%m-%d')}.md")
client = anthropic.Anthropic(api_key=os.environ["ANTHROPIC_API_KEY"])

# ---- Tools ----
def read_file(path: str) -> str:
    """Read a file and return its contents"""
    try:
        return Path(path).read_text()
    except FileNotFoundError:
        return f"Error: file {path} not found"

def write_file(path: str, content: str) -> str:
    """Write content to a file"""
    Path(path).parent.mkdir(parents=True, exist_ok=True)
    Path(path).write_text(content)
    return f"Wrote {len(content)} characters to {path}"

def run_command(cmd: str) -> str:
    """Run a shell command and return output"""
    import subprocess
    result = subprocess.run(cmd, shell=True, capture_output=True, text=True, timeout=30)
    return result.stdout + result.stderr

# Tool definitions for Claude
TOOLS = [
    {
        "name": "read_file",
        "description": "Read a file from disk",
        "input_schema": {
            "type": "object",
            "properties": {"path": {"type": "string", "description": "File path to read"}},
            "required": ["path"]
        }
    },
    {
        "name": "write_file",
        "description": "Write content to a file",
        "input_schema": {
            "type": "object",
            "properties": {
                "path": {"type": "string"},
                "content": {"type": "string"}
            },
            "required": ["path", "content"]
        }
    },
    {
        "name": "run_command",
        "description": "Run a shell command",
        "input_schema": {
            "type": "object",
            "properties": {"cmd": {"type": "string", "description": "Command to run"}},
            "required": ["cmd"]
        }
    }
]

TOOL_FUNCTIONS = {"read_file": read_file, "write_file": write_file, "run_command": run_command}

# ---- Memory ----
def load_memory() -> str:
    """Load the agent's persistent memory"""
    if MEMORY_FILE.exists():
        return MEMORY_FILE.read_text()
    return "# Agent Memory\nNo memory yet. This will be populated as we work together."

def save_log(user_input: str, response: str):
    """Append to today's log"""
    LOG_FILE.parent.mkdir(exist_ok=True)
    with open(LOG_FILE, "a") as f:
        f.write(f"\n## {datetime.now().strftime('%H:%M')}\n")
        f.write(f"**User**: {user_input}\n\n")
        f.write(f"**Agent**: {response}\n\n")

# ---- The Agent Loop ----
def run_agent(user_input: str) -> str:
    """Run the agent loop for a single user input"""
    memory = load_memory()
    system_prompt = f"""You are a helpful AI agent with persistent memory.

Here is your memory from previous sessions:
{memory}

You have tools to read/write files and run commands. Use them when needed.
After completing a task, update the memory file (CLAUDE.md) if anything important happened."""

    messages = [{"role": "user", "content": user_input}]

    # Agent loop
    while True:
        response = client.messages.create(
            model="claude-sonnet-4-6",
            max_tokens=4096,
            system=system_prompt,
            tools=TOOLS,
            messages=messages
        )

        # If the model wants to use tools
        if response.stop_reason == "tool_use":
            # Add assistant's response to messages
            messages.append({"role": "assistant", "content": response.content})

            # Execute each tool call
            tool_results = []
            for block in response.content:
                if block.type == "tool_use":
                    tool_fn = TOOL_FUNCTIONS.get(block.name)
                    if tool_fn:
                        result = tool_fn(**block.input)
                        tool_results.append({
                            "type": "tool_result",
                            "tool_use_id": block.id,
                            "content": str(result)
                        })

            # Add tool results and continue the loop
            messages.append({"role": "user", "content": tool_results})

        else:
            # Done — extract final text response
            final_text = ""
            for block in response.content:
                if hasattr(block, "text"):
                    final_text += block.text
            save_log(user_input, final_text)
            return final_text


# ---- Main ----
if __name__ == "__main__":
    print("Agent ready. Type your request (or 'quit' to exit).")
    while True:
        try:
            user_input = input("\n> ").strip()
            if user_input.lower() in ["quit", "exit"]:
                break
            if not user_input:
                continue
            response = run_agent(user_input)
            print(f"\nAgent: {response}")
        except KeyboardInterrupt:
            print("\nGoodbye!")
            break
```

Run it:
```bash
pip install anthropic
python minimal_agent.py
```

You now have a working agent with:
- Persistent memory (CLAUDE.md)
- Daily logs
- File read/write tools
- Command execution
- The full agent loop

---

## Adding Telegram Integration

Extend `minimal_agent.py` with a Telegram loop:

```python
import requests
import time

def send_telegram(text: str):
    TOKEN = os.environ["TELEGRAM_TOKEN"]
    CHAT_ID = os.environ["TELEGRAM_CHAT_ID"]
    requests.post(f"https://api.telegram.org/bot{TOKEN}/sendMessage",
                  json={"chat_id": CHAT_ID, "text": text})

def get_updates(offset: int = 0):
    TOKEN = os.environ["TELEGRAM_TOKEN"]
    r = requests.get(f"https://api.telegram.org/bot{TOKEN}/getUpdates",
                     params={"offset": offset, "timeout": 30})
    return r.json().get("result", [])

def telegram_bot_loop():
    print("Telegram bot running...")
    offset = 0
    while True:
        updates = get_updates(offset)
        for update in updates:
            offset = update["update_id"] + 1
            if "message" in update and "text" in update["message"]:
                user_text = update["message"]["text"]
                response = run_agent(user_text)
                send_telegram(response)
        time.sleep(1)

# Change the if __name__ block:
if __name__ == "__main__":
    if os.environ.get("TELEGRAM_TOKEN"):
        telegram_bot_loop()
    else:
        # Interactive terminal mode
        ...
```

---

## Adding Scheduled Checks

Add a background thread that runs tasks on a schedule:

```python
import threading
import schedule

def morning_brief():
    response = run_agent("Check my projects and give me a morning briefing. What should I focus on today?")
    send_telegram(response)

def start_scheduler():
    schedule.every().day.at("08:00").do(morning_brief)
    schedule.every().sunday.at("18:00").do(lambda: run_agent("Generate weekly status report and send to Telegram"))

    while True:
        schedule.run_pending()
        time.sleep(60)

# Start scheduler in background
threading.Thread(target=start_scheduler, daemon=True).start()
```

---

## What You Just Built vs. OpenClaw

Let's be honest about the gap:

| Feature | Your DIY Agent | OpenClaw |
|---------|---------------|---------|
| Agent loop | Yes | Yes |
| Tool use | Yes (3 tools) | Yes (100+ built-in) |
| Memory | CLAUDE.md | CLAUDE.md + vector search |
| Telegram | Yes | Yes (+ Discord, WhatsApp, SMS) |
| Scheduling | Yes (basic) | Yes (heartbeats + cron) |
| Multi-agent | No | Yes |
| Skills system | No (just prompt) | Yes (SKILL.md files) |
| Error recovery | No | Yes (retry logic, fallbacks) |
| Web UI | No | Yes |
| Plugin system | No | Yes |

Your DIY agent has the core concept right. Frameworks add reliability, features, and polish.

---

## When to DIY vs. Use a Framework

**DIY makes sense when:**
- You're learning how agents work
- Your use case is simple (one agent, one task, one channel)
- You want maximum control and minimum dependencies
- You're building something very custom

**Use a framework when:**
- You want multi-agent coordination
- You need production reliability (error handling, retries, monitoring)
- You want a built-in plugin/skill ecosystem
- You're managing multiple messaging channels

**The complexity threshold:** If your agent does more than 3 different things, or you find yourself re-implementing the same patterns, it's time for a framework.

---

## Next Step

You now understand how agents work at the code level. That knowledge makes you a much better user of any framework you pick.

Next chapter: a tour of the major frameworks — OpenClaw, n8n, CrewAI, AutoGen, LangGraph — and when to use each.

---

[← ⚙️ Automation & Workflows](06-automation-workflows.md) · [📚 Table of Contents](../README.md) · [🏗️ Agent Frameworks →](08-frameworks.md)
