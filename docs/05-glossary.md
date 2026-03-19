# 📖 Glossary

Plain English definitions for terms you'll encounter in this guide and the AI agent world.

---

**Agent**
An AI that can take actions, not just answer questions. Unlike a chatbot that responds to one message at a time, an agent can execute multi-step tasks, use tools (search the web, write files, call APIs), and work autonomously toward a goal. Think of it as the difference between a calculator (responds to input) and an employee (figures out what needs to be done and does it).

**Orchestrator**
The "brain" or "CEO" agent in a multi-agent system. Its job is to understand the goal, break it into sub-tasks, delegate to specialized agents, and synthesize results. An orchestrator should never do low-level work itself — it plans and coordinates. In this setup, `gooper` (Claude Opus) is the orchestrator.

**Sub-Agent**
A specialized agent that handles a specific type of work, spawned and directed by an orchestrator. Sub-agents have narrower context and lower cost models — they do the actual work. Examples: a coding sub-agent that writes PRs, a research sub-agent that runs web searches, a summarization sub-agent that condenses long documents.

**Skill**
A pre-built behavior module that extends what an agent can do — like a plugin or a slash command. Skills are defined in markdown files (`SKILL.md`) and the agent auto-detects which one applies based on what you're asking. Examples: a `weather` skill that knows how to fetch forecasts, a `code-review` skill with a structured review checklist, a `discovery-pipeline` skill that runs a 6-phase idea evaluation.

**Memory (short-term vs long-term)**
Short-term memory is what the agent knows within a single session — everything in the current conversation. It disappears when the session ends. Long-term memory is information persisted to files that the agent reads at the start of new sessions. In OpenClaw: short-term = the active chat context; long-term = `MEMORY.md` (curated) + `memory/YYYY-MM-DD.md` (daily logs).

**Context Window**
The maximum amount of text an AI model can "see" at once — like its working memory. Claude's context window is large (~200K tokens, roughly 150K words), but it's not infinite. When context fills up, older information gets compressed or dropped. This is why long-running agent sessions can get "forgetful" — not a bug, just a physical limit.

**Prompt**
The text you send to an AI model to get a response. In everyday use: the message you type. In agent systems: prompts can be complex, structured, and automated. Good prompting is a skill — specificity, context, and format matter a lot.

**System Prompt**
A special prompt sent to the AI before the conversation starts — invisible to the user, defines the AI's behavior, persona, and constraints. This is how OpenClaw's `SOUL.md` and other config files become part of the agent's behavior. The system prompt is like a job description the AI was given before it started working.

**MCP (Model Context Protocol)**
An open standard (created by Anthropic, now adopted by OpenAI, Google, and others) that defines how AI models connect to external tools and data sources. Think of it as USB-C for AI: a standardized way to plug in capabilities. With MCP, you can give Claude access to your database, GitHub, calendar, or any custom tool — and it works the same way regardless of which tool.

**RAG (Retrieval-Augmented Generation)**
A technique for giving AI access to a large knowledge base without stuffing it all into the context window. The system stores documents in a searchable index (usually a vector database), retrieves the most relevant chunks when you ask a question, and feeds only those chunks to the AI. Common use case: "chat with your documents." Alternative approach: file-based context (what OpenClaw uses) — simpler, often works just as well for structured knowledge.

**Discovery Pipeline**
A structured 6-phase process for turning a raw idea into an implementation-ready spec: Capture → Problem Definition → Research → PRD → Tech Spec → Task Breakdown. Each phase builds on the last, with the agent facilitating research and structured thinking. Prevents "building the wrong thing" by forcing clarity before writing code.

**Dev Pipeline**
The automated process for turning a task spec into shipped code. In this setup: task file created → dev agent checks out git worktree → builds feature → runs auto-verification → creates PR → orchestrator reviews → human approves merge. The goal: human sets direction, agents do execution, human reviews results.

**Heartbeat**
A periodic check-in where the agent wakes up (every 30 minutes by default), reads a `HEARTBEAT.md` checklist, and decides if anything needs attention. If nothing needs attention, it goes back to sleep silently. If something matters (urgent email, upcoming meeting, failed build), it reaches out in Discord. Prevents both "always bothering you" and "going silent for hours."

**Cron**
Short for "cron job" — a task scheduled to run at specific times automatically. Named after the Unix `cron` daemon. Used for exact-timing tasks: daily briefings at 9am, weekly reports on Sunday, monthly cleanups. Unlike heartbeats (which drift and batch), cron jobs run precisely. Example: `0 2 * * *` = "run at 2:00 AM UTC every day."

**Worktree**
A git feature that lets you have multiple working directories from the same repository simultaneously. Each worktree is like a separate checkout of the repo — you can have one on `main` and another on `feature/pdf-upload` at the same time, without conflicts. Used in the dev pipeline so multiple agents can build different features in parallel without stepping on each other.

**PR (Pull Request)**
A proposal to merge code changes from one branch into another — a request to "pull" your changes into the main codebase. In the dev pipeline: when an agent finishes building a feature, it creates a PR so a human (or orchestrator) can review the code before it goes live. Standard practice in software development. GitHub and GitLab are the main platforms.

**VPS (Virtual Private Server)**
A virtual machine rented from a cloud provider — your own Linux server on the internet, available 24/7. Used to host OpenClaw so your agents run even when your laptop is off. Popular choices: Hetzner (cheap, European), DigitalOcean (easy), Vultr, AWS. A basic VPS for running agents costs ~$5–20/month. Alternative: run locally (no VPS needed, but agents stop when your machine sleeps).

---

*Missing a term? Open an issue or PR.*
