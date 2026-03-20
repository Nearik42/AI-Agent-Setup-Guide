# Skills: Giving Your Agent Superpowers

A skill is a pre-built behavior module that tells your agent *how* to do something. Instead of explaining from scratch every time, you define the skill once — and the agent knows when and how to use it.

Think of it like training a new hire: you document the process once, they follow it every time.

---

## What Is a Skill?

A skill is typically a markdown file (often called `SKILL.md`) that contains:

- **What** the skill does
- **When** to use it (triggers)
- **How** to execute it (step-by-step)
- **What to output**

Your agent reads these files and knows to follow them when the situation calls for it.

**Example trigger:** User says "run a SWOT analysis" → agent loads the SWOT skill file → follows the defined process → delivers structured output.

---

## Why Skills Matter

Without skills: every interaction requires you to explain the process again, or the agent invents its own approach.

With skills: your agent has a consistent, documented playbook. Repeatable. Improvable. Shareable.

This is the difference between an agent that helps you occasionally and one that reliably handles workflows you've designed.

---

## Anatomy of a Skill File

````markdown
---
name: competitor-analysis
description: Run a structured competitive analysis for a product or business
triggers:
  - "analyze competitors"
  - "competitive analysis"
  - "who are my competitors"
---

# Competitor Analysis Skill

## When to use this
User asks for competitive analysis, market positioning, or competitor research.

## Process

### Step 1: Define the target
Ask the user: What product/business are we analyzing? Who do they consider competitors?

### Step 2: Research
Search for: [competitor name] pricing, features, reviews, funding, team size
Sources: company website, Crunchbase, G2/Capterra reviews, LinkedIn

### Step 3: Structure the output
For each competitor:
- Product overview (2-3 sentences)
- Pricing model
- Key strengths
- Known weaknesses
- Target customer

### Step 4: Synthesis
Create a comparison table. Identify gaps in the market.

## Output format
Structured markdown report with comparison table.
````

---

## Real Examples: PM & Discovery Skills

Two of the most powerful skills for product builders and entrepreneurs are built around product management frameworks.

### The Discovery Pipeline Skill

The Discovery Pipeline takes a raw idea through a structured 5-phase process:

```
Phase 1: Capture — raw idea, one paragraph
Phase 2: Problem Definition — who has this problem, how painful?
Phase 3: Research — market size, competitors, existing solutions
Phase 4: PRD — Product Requirements Document
Phase 5: Tech Spec — how to build it
```

**In practice:** You say "new idea: a tool that monitors all my websites for errors and sends alerts" → the agent runs you through all 5 phases systematically, ending with a full PRD and implementation plan.

This is not a chat session. It's a structured process your agent follows every time, producing consistent, high-quality outputs.

**See it in action:** The [PM Orchestrator skills collection](https://github.com/Nearik42/AI-Agent-Setup-Guide/tree/main/skills) includes SWOT analysis, Lean Canvas, Value Proposition, and more.

### Product Management Skills

PM skills handle specific frameworks on demand:

**SWOT Analysis:**
```
Trigger: "run a SWOT analysis for [thing]"
Output: Structured strengths/weaknesses/opportunities/threats with action recommendations
```

**Lean Canvas:**
```
Trigger: "create a lean canvas for [idea]"
Output: 9-field business model canvas (Ash Maurya format)
```

**Value Proposition:**
```
Trigger: "define the value prop for [product]"
Output: 6-part JTBD value proposition: Who / Why / What Before / How / What After / Alternatives
```

These aren't just prompts — they're documented processes your agent consistently follows, with structured outputs you can act on.

> 💬 **Community:** [@toddsaunders](https://x.com/toddsaunders/status/2031346598132011154) built `/investor-pitch` as a Claude Code skill that generates complete investor decks. Same pattern — a documented process your agent follows reliably.

---

## How to Create Your Own Skill

### Step 1: Document what you do manually

The best skills come from processes you already do. Write down the steps:

```markdown
When someone asks me to write a job description, I:
1. Ask what role and team
2. Research similar roles on LinkedIn
3. Write requirements section (must-have vs nice-to-have)
4. Write responsibilities section
5. Add culture/team context
6. Format in our standard template
```

### Step 2: Turn it into a skill file

```markdown
---
name: job-description-writer
description: Write structured job descriptions for any role
---

# Job Description Skill

## Process
1. Clarify: What role? What team? Seniority level?
2. Research: Search LinkedIn for 5 similar roles at comparable companies
3. Requirements: List 5-7 must-haves, 3-5 nice-to-haves
4. Responsibilities: 6-8 bullet points, outcome-focused
5. Culture section: 2-3 sentences about team/company
6. Format in standard template

## Output
Complete job description, ready to post.
```

### Step 3: Store it where your agent can find it

```
your-project/
├── CLAUDE.md          ← main context file
├── skills/
│   ├── job-description.md
│   ├── competitor-analysis.md
│   └── weekly-report.md
```

Tell your agent in `CLAUDE.md`:
```markdown
## Skills
I have skill files in /skills/. When a task matches a skill's description, 
load and follow that skill file rather than inventing your own approach.
```

---

## Skill Libraries and Communities

You don't have to build every skill from scratch. A growing ecosystem of shared skills exists:

- **Claude Code Skills** — community-built skills for Claude Code (search GitHub for `claude-code-skills`)
- **OpenClaw Skills** — the [clawhub.com](https://clawhub.com) marketplace has installable skills
- **PM Skills Collection** — product management frameworks (SWOT, Lean Canvas, PRD, etc.)

> 💬 **Community:** [@kloss_xyz](https://x.com/kloss_xyz/status/2033898090542076296) built a prompt that audits your OpenClaw setup against 21 layers required for a Jarvis-like system — scoring each layer. That's a skill in action.

> 💬 **Community:** [@AndrewYNg](https://x.com/AndrewYNg/status/2033577583200354812) proposed a "Stack Overflow for AI coding agents" — a place where agents share learnings with each other. Skills are moving in that direction.

---

## Skills vs. Prompts vs. Tools

People often confuse these three:

| | What it is | Lives where | Scope |
|---|---|---|---|
| **Prompt** | A one-time instruction | In your message | Single interaction |
| **Tool** | A function the agent can call (web search, bash, etc.) | In the framework config | Persistent capability |
| **Skill** | A documented process | In a file your agent reads | Repeatable workflow |

A good rule of thumb: if you find yourself giving the same instructions more than twice, it should be a skill.

---

## Getting Started

1. **Identify one recurring task** you do manually (writing something, researching something, reviewing something)
2. **Write out the steps** in plain English — don't overthink it
3. **Create a skills/ folder** in your project
4. **Add it to your CLAUDE.md** so the agent knows it exists
5. **Test it** — ask your agent to perform the task and see if it follows the skill

Your first skill doesn't have to be perfect. The point is to start documenting your processes so your agent can handle them reliably.
