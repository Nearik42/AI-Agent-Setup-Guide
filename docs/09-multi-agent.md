# Multi-Agent Architecture: The Full System

> 💡 **You don't need to type any of this yourself.** Once Claude Code is running, paste this chapter's instructions and say *"set this up for me"* — it reads the guide and executes the commands on your behalf. The code blocks below are what Claude Code runs, not what you type.
One agent is useful. Multiple agents are powerful. This chapter covers why you'd want more than one, how to coordinate them, and what a real multi-agent setup looks like.

> 💬 **Community:** [@BoWang87](https://x.com/BoWang87/status/2032655917737771107) — "2026 is the year of agents. Here's the evidence from GitHub's fastest-growing AI repos." Multi-agent systems are the frontier everyone's racing toward.

---

## Why Multiple Agents?

Three reasons: specialization, parallel work, and cost optimization.

**Specialization**: A coding agent optimized for writing clean Python is better at that specific task than a generalist agent. Same with research, writing, review, customer support. Specialized agents have focused system prompts, relevant tools, and appropriate context — they're better at their job.

**Parallel work**: One agent does one thing at a time. Multiple agents can work simultaneously. Your orchestrator receives a request, splits it into subtasks, and dispatches them in parallel. Research happens while code is being written.

**Cost optimization**: Different tasks warrant different models. Routing a simple lookup to Claude Haiku (cheap, fast) vs. complex reasoning to Claude Opus (expensive, powerful) can cut your API costs by 80% without sacrificing quality. Your orchestrator makes that routing decision.

---

## The Supervisor-Worker Pattern

The most common multi-agent architecture:

```
You (Discord/Telegram)
     |
     v
+--------------------+
|   Orchestrator     |  <- Full model (Claude Opus/Sonnet)
|   (main-agent)         |    Reads all context, makes decisions
+--------+-----------+
         |
         | delegates to
  +------+------+----------+
  v             v          v
Research      Coding     Review
Agent         Agent      Agent
(Sonnet)      (Code)     (Haiku)
```

**The Orchestrator** is the brain. It:
- Receives your message
- Decides what needs to happen
- Delegates tasks to the right sub-agents
- Synthesizes their results
- Reports back to you

**Sub-agents** are specialists. They:
- Get a specific task from the orchestrator
- Execute it using their tools and context
- Return the result
- Don't talk to each other directly (all coordination goes through the orchestrator)

> 💬 **Community:** [@techNmak](https://x.com/techNmak/status/2030992194119864499) — "OpenClaw just got full observability. OpenClaw agents assemble context from: System prompts → History → Tool schemas." Observability matters when you have multiple agents; you need to see what each one knows.

---

## Permission Delegation Per Agent

Each agent should have only the permissions it needs.

**Research Agent**: Can search the web, read files. Cannot write files, cannot run code, cannot push to git. It just finds information.

**Coding Agent**: Can read and write files in `/workspace`, can run Python/Node commands, can run tests. Cannot access the web directly (to prevent prompt injection from malicious websites). Cannot push to git without a review step.

**Review Agent**: Can read files. Cannot modify anything. Its job is to evaluate, not change.

**Orchestrator**: Can read everything, can send messages, can delegate tasks. Should not directly modify code or push changes — it coordinates, others execute.

This is the principle of least privilege applied to your agent team.

---

## Real Example: "Build Me a Landing Page"

Here's what happens when you send this request to your multi-agent system:

**Step 1: Orchestrator receives the request**

You: "Build me a landing page for my expense tracker project."

Orchestrator reads your CLAUDE.md, sees it's a FastAPI project, decides on the plan:
- Research: find good landing page examples for dev tools
- Code: build the HTML/CSS/JS
- Review: check code quality and usability

**Step 2: Research Agent dispatches**

Orchestrator sends to Research Agent: "Find 3 examples of clean landing pages for developer tools. Extract: color schemes, headline patterns, CTA placement."

Research Agent searches the web, takes notes, returns findings.

**Step 3: Coding Agent builds**

Orchestrator sends to Coding Agent: "Build a landing page for an expense tracker. Here are the design references: [research output]. Requirements: hero section, feature list, pricing, CTA. Use Tailwind CSS."

Coding Agent writes the HTML, CSS, maybe some JavaScript. Creates files in `/workspace/landing/`.

**Step 4: Review Agent checks**

Orchestrator sends to Review Agent: "Review this landing page code. Check: does it load fast, is the HTML semantic, is there a mobile layout, are the CTAs clear?"

Review Agent reads the files, returns feedback.

**Step 5: Orchestrator decides**

If review is good: report back to you with the file location and a summary.
If review flags issues: send back to Coding Agent with the specific feedback. Loop until quality is satisfactory.

**Step 6: You get the result**

Orchestrator: "Your landing page is ready at `/workspace/landing/index.html`. Here's what was built: [summary]. The review flagged one thing: there's no mobile menu — I've asked the coding agent to add it, should be done in 2 minutes."

That's a multi-agent pipeline. You sent one message. Four agents did coordinated work.

---

## The Discovery Pipeline

This is the meta-pipeline — turning an idea into executable tasks.

```
You: "I want to build X"
         |
         v
    [Research Agent]
    Competitor analysis,
    user pain points,
    existing solutions
         |
         v
    [Orchestrator]
    Synthesizes research,
    drafts PRD
         |
         v
    [Spec Agent]
    Converts PRD to
    tech spec + task list
         |
         v
    [Coding Agent]
    Implements tasks
    one by one
         |
         v
    [Review Agent]
    Code review + QA
         |
         v
    [Orchestrator]
    Reports progress,
    asks for approvals
```

This is a real workflow. It's what sophisticated setups (including the one that runs this guide) actually use. An idea goes in; a working prototype comes out.

---

## The Dev Pipeline

For ongoing development work:

```
New code gets written
         |
         v
    [Review Agent]
    - Code quality check
    - Security scan
    - Test coverage check
         |
         v
    [Test Agent]
    - Run existing tests
    - Write new tests for
      uncovered code
         |
         v
    [Orchestrator]
    If all good: open PR
    If issues: fix loop
         |
         v
    [Git Agent]
    Opens PR with
    description and
    review summary
```

This pipeline can run automatically whenever code is written. Your coding agent writes code, the dev pipeline validates it, PRs get opened for your review.

---

## Cost Optimization in Practice

A real routing strategy for different task types:

```python
def route_to_model(task_type: str) -> str:
    """Route tasks to appropriate model tier"""
    cheap_tasks = ["summarize", "classify", "extract", "format", "route"]
    medium_tasks = ["research", "write", "review", "explain"]
    expensive_tasks = ["complex_reasoning", "architecture", "novel_problem"]

    if task_type in cheap_tasks:
        return "claude-haiku-3-5"      # ~20x cheaper than Opus
    elif task_type in medium_tasks:
        return "claude-sonnet-4-6"     # Good balance
    else:
        return "claude-opus-4-5"       # Best reasoning
```

Practical numbers: if you send 1000 requests per month and 70% are routing/summarization tasks that can use Haiku, you might cut costs from $60/month to $12/month with the same quality on the tasks that matter.

---

## When to Add Agents

The rule of thumb: **one agent doing more than 3 meaningfully different things = time to split.**

Signs you should add an agent:
- Your system prompt is longer than 500 words and covers completely different topics
- The same agent is doing both research AND coding AND review
- You're constantly giving the agent context that only applies to half its tasks
- Response quality has degraded because the agent is confused about its role

Signs you shouldn't add an agent yet:
- Your current agent is working fine
- The new "role" is just a different prompt, not a different tool set or permission set
- You're adding complexity for its own sake

Multi-agent setups are powerful but they're also harder to debug. One well-configured agent beats three poorly-configured ones.

---

## Next Step

You now have the complete picture — from zero to multi-agent system. The remaining chapters are reference material: a curated Twitter/X index of resources and a glossary of every term used in this guide.

Go build something.

---

[← 🔍 Framework Deep Dive](08c-framework-deep-dive.md) · [📚 Table of Contents](../README.md) · [🐦 Twitter/X Resources →](10-twitter-index.md)
