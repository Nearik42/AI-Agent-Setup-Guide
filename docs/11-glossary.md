# Glossary: Plain English Definitions

Every technical term used in this guide, explained in 2-4 sentences without jargon.

---

## Agent

An AI system that can take actions in the world, not just answer questions. An agent reads files, runs code, searches the web, sends messages, and completes multi-step tasks autonomously. The key difference from a chatbot: it *does things* instead of just *saying things*.

---

## Orchestrator

The "brain" of a multi-agent system — the agent that receives a request, decides how to handle it, and delegates subtasks to other agents. An orchestrator doesn't usually do the detailed work itself; it plans, coordinates, and synthesizes results. Think of it as a manager who routes tasks to the right specialists.

---

## Sub-Agent

An agent that receives instructions from an orchestrator and executes a specific task. Sub-agents are specialists: one does research, another writes code, another reviews. They typically have limited permissions and focused context relevant only to their specialty.

---

## Skill

A small text file (usually SKILL.md) that teaches an agent how to handle a specific type of task. When the agent encounters a relevant request, it reads the skill file and follows its instructions. Skills are reusable, shareable, and composable — you build a library of them over time.

---

## Memory (Short-term vs. Long-term)

**Short-term memory** is what an agent remembers within a single conversation — the current context window. When the session ends, it's gone. **Long-term memory** is information that persists across sessions, stored in files (like CLAUDE.md) or a database. Without explicit long-term memory, every session starts completely fresh.

---

## Context Window

The maximum amount of text an AI model can "see" at once. If your conversation + files + instructions exceed the context window, the model can't see the oldest parts. Claude Sonnet's context window is 200,000 tokens (~150,000 words) — large, but it fills up in complex agent sessions. This is why memory management matters.

---

## Prompt

The text you send to an AI model as input. A prompt can be a question, a task description, a document to summarize, or complex instructions for how to behave. The quality of your prompts directly affects the quality of the output — this is "prompt engineering."

---

## System Prompt

A special prompt that defines how an agent behaves throughout a conversation. It's set by the developer (or you, in your config), not the user. The system prompt might say "you are a coding assistant who prefers Python, always writes tests, and asks for clarification before making big changes." Think of it as the agent's job description.

---

## CLAUDE.md

A special file that Claude Code (and Claude agents generally) reads automatically at the start of every session. It's your primary long-term memory mechanism — you write your project context, preferences, and history here, and the agent picks it up every time. See Stage 4 for how to use it effectively.

---

## Sandbox

An isolated environment where an agent runs with limited access to the rest of your system. If the agent goes rogue or makes a mistake in a sandbox, the damage is contained — it can't touch files outside the sandbox. Docker containers and VPS servers are common sandboxing approaches for agents.

---

## Permissions

Controls that define what an agent is allowed to do. Can it write files? Run shell commands? Access the internet? Push to git? Granting the minimum permissions needed for a task (and no more) is a security best practice called "least privilege."

---

## Least Privilege

The security principle of giving any system (agent, user, program) exactly the access it needs to do its job — nothing more. A research agent that only needs to search the web shouldn't also have write access to your codebase. Applying least privilege limits the blast radius if something goes wrong.

---

## MCP (Model Context Protocol)

A standard protocol that lets AI models connect to external tools and data sources in a consistent way. Instead of each agent building custom integrations for every service, MCP provides a common interface. A "MCP server" exposes tools (like web search, file access, database queries) that any compatible AI agent can use.

---

## RAG (Retrieval Augmented Generation)

A technique where an AI agent searches a knowledge base for relevant information before generating a response, rather than relying solely on what it was trained on. You store documents, notes, or data; the agent retrieves the relevant pieces when answering questions. It's how you give an agent access to information it wasn't trained on.

---

## Discovery Pipeline

A multi-agent workflow that takes an idea from concept to implementation-ready specification. Typically: idea capture → research (competitors, user needs) → PRD (product requirements document) → tech spec → task breakdown → handoff to coding agents. Referenced in Stage 9.

---

## Dev Pipeline

A multi-agent workflow for ongoing development work: code gets written → review agent checks quality → test agent runs/writes tests → if all good, PR gets opened automatically. Reduces the feedback loop between writing and reviewing code.

---

## Heartbeat

A regular check-in mechanism for an always-on agent. Every N minutes (or hours), the agent "wakes up," checks if anything needs attention (emails, calendar events, monitored conditions), and either acts or goes back to sleep. More flexible than cron because the agent decides what to do based on current state.

---

## Cron

A Unix scheduling system that runs commands at specified times. "Cron job" means a scheduled task. The syntax `0 8 * * *` means "at 8:00am every day." Cron is how you make your agent do things automatically on a schedule — morning briefings, weekly reports, nightly backups.

---

## VPS (Virtual Private Server)

A server you rent by the hour or month that runs in a data center. Unlike your laptop, it's always on, always connected, and isolated from your personal machine. Running your agents on a VPS means they work 24/7 even when your laptop is off, and any mistakes they make don't affect your personal files. Costs 5-20 EUR/month from providers like Hetzner or DigitalOcean.

---

## Docker

A tool that creates isolated, self-contained environments (containers) for running software. You can run your agent inside a Docker container so it can't access anything outside it. The container includes everything the agent needs (Node.js, your code, environment variables) and is easy to restart, copy, or throw away.

---

## API Key

A secret string of characters that authenticates you with an external service (like Anthropic, OpenAI, Telegram) and tracks your usage for billing. Treat API keys like passwords: never paste them in chat, never commit them to git, store them in `.env` files. Losing control of an API key means someone else can use your quota and you get the bill.

---

## Webhook

A way for a service to notify your application when something happens, rather than you constantly asking "anything new?" When a Telegram message arrives, Telegram sends an HTTP POST to your webhook URL, and your agent handles it immediately. More efficient than polling (repeatedly checking if anything happened).

---

## Token (Cost Unit)

The unit used to measure how much text an AI processes and to calculate costs. One token is roughly 4 characters or 0.75 words. "Claude Sonnet costs $3 per million input tokens" means every ~750,000 words you send costs $3. A typical agent session with 20 back-and-forth exchanges might use 5,000-20,000 tokens, costing fractions of a cent.

---

*See something missing or explained poorly? Open an issue on GitHub.*

---

[← 🐦 Twitter/X Resources](10-twitter-index.md) · [📚 Table of Contents](../README.md) · [💡 Use Cases →](12-use-cases.md)
