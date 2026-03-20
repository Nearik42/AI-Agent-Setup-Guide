# Agent Frameworks: Choosing the Right Tool

You've built a minimal agent from scratch. You understand the loop. Now let's talk about what frameworks actually give you — and which one you should use.

Honest answer first: most people add a framework too early. Your DIY agent from Stage 7 is good enough for a lot of use cases. Add a framework when you hit a wall, not because you read about it on Twitter.

---

## When You Actually Need a Framework

You need a framework when:
- You want **multiple agents** working together
- You need **production-grade reliability** (proper error handling, retries, monitoring)
- You want a **plugin/skills ecosystem** instead of writing everything from scratch
- You need **visual tooling** to debug what your agent is doing
- Your current setup has become **too complex to maintain**

You probably don't need one yet if:
- You have one agent doing one job
- It works and you don't need more features
- You're still learning how agents work

---

## The Decision Tree

```
What do you primarily need?
|
+-- Visual workflow builder, no code
|   --> n8n
|
+-- Python-centric, quick prototype
|   --> CrewAI
|
+-- Messaging-integrated personal agent (Discord/Telegram)
|   --> OpenClaw
|
+-- Custom orchestration logic, max control
|   --> LangGraph
|
+-- Enterprise-scale, conversation patterns
|   --> AutoGen / Microsoft Agent Framework
|
+-- I want to learn before picking
    --> DIY (previous chapter), then revisit
```

---

## The Frameworks

### OpenClaw

OpenClaw is a gateway + agent + skills architecture designed for personal AI assistants. It natively connects to Discord and Telegram, handles the scheduling/heartbeat pattern, and has a growing plugin ecosystem.

**Architecture**: One main agent (the "gateway") routes messages to specialized agents. Each agent has a persona, system prompt, and access to a set of skills. Skills are SKILL.md files that define reusable behaviors — the same pattern Claude Code uses.

**Best for**: Personal AI assistant setup. Running one or more agents that respond to Discord/Telegram messages, remember context across sessions, and perform scheduled tasks. Exactly the use case built in this guide.

**Not ideal for**: Pure Python codebases, enterprise teams, visual workflow building.

```bash
npm install -g openclaw
openclaw init
openclaw gateway start
```

### n8n

n8n is a visual workflow builder. You drag nodes onto a canvas, connect them, and define what happens when a trigger fires. Think Zapier but self-hosted, more powerful, and free.

**Architecture**: Trigger node (schedule, webhook, email) → processing nodes → action nodes. You can call the Claude API from a "HTTP Request" node and connect it to any service that has an n8n integration (hundreds exist).

**Best for**: Non-developers who want to connect services visually. "When I get an email, summarize it with Claude and post to Slack." "Every Monday morning, search for news about my competitors and send me a digest."

**Not ideal for**: Complex agent logic, multi-step reasoning, stateful agents that need deep memory.

```bash
docker run -it --rm -p 5678:5678 n8nio/n8n
```

### CrewAI

CrewAI is a Python framework for building role-based multi-agent systems. You define "crews" of agents, each with a role, goal, and tools. The crew works together to accomplish a task.

**Architecture**: You define agents (Researcher, Writer, Reviewer), assign them tools, and write a "task" for the crew to complete. CrewAI handles the coordination and output passing.

**Best for**: Quick multi-agent prototypes. If you want to see what a research + writing + review pipeline looks like in 50 lines of Python, CrewAI gets you there fastest.

**Not ideal for**: Production reliability, stateful long-running agents, messaging integrations (you'd need to add those yourself).

```bash
pip install crewai
```

### AutoGen / Microsoft Agent Framework

AutoGen (now rebranded as Microsoft Agent Framework) is a Python library for building conversation-based multi-agent systems. Agents talk to each other in a structured way until they solve a problem.

**Architecture**: Agents as conversation participants. A UserProxy agent represents the human. An AssistantAgent handles tasks. A GroupChat object coordinates who speaks when. It's conversation as orchestration.

**Best for**: Complex collaborative tasks where multiple agents need to critique, debate, and improve on each other's work. Research pipelines, code review loops, document drafting with multiple perspectives.

**Not ideal for**: Simple setups (overkill), real-time messaging integrations, non-Python teams.

```bash
pip install pyautogen
```

### LangGraph

LangGraph is a library for building stateful, graph-based agent workflows. You define nodes (agent steps) and edges (transitions between steps, including conditional logic). It gives you maximum control over the flow of execution.

**Architecture**: Directed graph where nodes are functions and edges define what happens next. You can have loops, conditional branching, parallel execution, and checkpointing. It's the most flexible option — and the most complex.

**Best for**: Custom orchestration logic where you need precise control. "Do research, then write, then if quality score < 7 revise, then publish." The conditional and loop patterns are hard to express in other frameworks.

**Not ideal for**: Quick prototypes, non-technical users, teams that don't want to think in graphs.

```bash
pip install langgraph
```

### DIY (Previous Chapter)

Your minimal Python agent from Stage 7. No dependencies beyond the Anthropic SDK. Full control, zero magic.

**Best for**: Learning, simple use cases, maximum customization.

---

## Framework Comparison Table

| Framework | Setup | Multi-Agent | Messaging | Code Required | Cost |
|-----------|-------|-------------|-----------|---------------|------|
| OpenClaw | Medium | Yes | Native | Low | Free (+ API costs) |
| n8n | Low | Limited | Via nodes | None | Free (self-hosted) |
| CrewAI | Low | Yes | Manual | Medium | Free |
| AutoGen | Medium | Yes | Manual | High | Free |
| LangGraph | High | Yes | Manual | High | Free |
| DIY | None | No | Manual | Full | Free |

---

## The "Build Your Own OpenClaw" Path

If you followed this guide from Stage 7, you basically built OpenClaw from scratch — just without the polish, plugin system, and Discord integration.

Here's what OpenClaw adds on top of your DIY agent:
1. **Gateway routing**: messages from Discord/Telegram go to the right agent automatically
2. **Skills system**: drop a SKILL.md in the right folder, agent learns the behavior
3. **Context engine**: smarter memory management (doesn't just append to a file)
4. **Plugin ecosystem**: 50+ community plugins for common integrations
5. **Multiple agent configs**: run different agents with different personas and permissions

When you install OpenClaw and configure it, you're essentially setting up the production version of what you built in Stage 7. The concepts are identical; the implementation is just more robust.

---

## My Recommendation

If you're using this guide to build a personal AI assistant:

1. **Start with the DIY agent** (Stage 7) — understand what you're building
2. **Add Telegram/Discord** — make it accessible
3. **Run it for 2-4 weeks** — find the pain points
4. **Then install OpenClaw** — let it replace the fragile parts with robust infrastructure

Don't install OpenClaw on Day 1. You won't understand what it's doing or why. You need the DIY experience first.

---

## Next Step

You understand the frameworks. Now let's build the final layer: multiple agents working together.
