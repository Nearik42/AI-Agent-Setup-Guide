# Permissions & Security: Don't Give Your Agent Root

This chapter is boring but important. Skip it and you might accidentally let an agent delete important files or push secrets to GitHub.

---

## The Four Permission Modes

Claude Code has four ways to handle permissions:

### Mode 1: Ask Every Time (Default)
```bash
claude
```
Every file write, every command — Claude asks before doing it. Safest. Also slowest. Best for learning.

### Mode 2: Accept Edits
```bash
claude --permission-mode acceptEdits
```
Claude can edit files without asking, but still asks before running shell commands. Good balance for coding tasks.

### Mode 3: Bypass Permissions (With Warning)
```bash
claude --dangerously-skip-permissions
```
No permission checks. Claude just does whatever it decides. Fastest. Also most dangerous.
**Only use this in an isolated environment (see: sandboxing below).**

### Mode 4: Automation Mode (Headless)
```bash
claude --permission-mode bypassPermissions --print -p "your task"
```
For running Claude Code inside scripts and automation pipelines. No interactive prompts.

---

## What Can Go Wrong?

Real examples of what agents can do with too many permissions:

**Accidental deletion**: "Clean up unused files" deletes something important
**Git disasters**: "Push all changes" pushes to wrong branch, or with secrets in files
**Infinite loops**: Agent creates files, sees them as "new context," creates more files
**Cost explosions**: Agent calls an API in a loop — unexpected bill
**Secret exposure**: Agent reads `.env` file and includes contents in a commit message

The solution isn't to be paranoid — it's to be intentional about what permissions you grant.

---

## The Principle of Least Privilege

Give your agent exactly the permissions it needs for the task. Nothing more.

- **For learning/exploration**: Default mode (ask every time)
- **For a specific coding task**: `--permission-mode acceptEdits`
- **For automation**: `bypassPermissions` + Docker sandbox

---

## Sandboxing: Isolation for Autonomous Agents

When you want to run an agent autonomously (no babysitting), isolate it.

### Option 1: Separate Project Folder (Minimum)
Create a dedicated folder. The agent can only damage what's in that folder.

```bash
mkdir agent-workspace
cd agent-workspace
claude --dangerously-skip-permissions
```

### Option 2: Docker Container (Recommended for Automation)
The agent runs inside a container. Even if it goes rogue, it can't touch your main system.

```bash
docker run -it --rm \
  -e ANTHROPIC_API_KEY=$ANTHROPIC_API_KEY \
  -v $(pwd)/workspace:/workspace \
  -w /workspace \
  node:20 \
  bash -c "npm install -g @anthropic-ai/claude-code && claude --dangerously-skip-permissions"
```

### Option 3: VPS (For Production Agents)
Run your agent on a separate server. If something goes wrong, it's isolated from your main machine. This is what serious setups use. (We cover this in later stages.)

---

## Claude Code's Built-in Sandbox (macOS/Linux)

When sandboxing is enabled, Claude Code uses OS-level isolation:
- **Linux**: Bubblewrap — restricts filesystem and network access
- **macOS**: Apple Seatbelt — restricts what files/processes the agent can touch

This is automatic on newer Claude Code versions. You can verify:
```bash
claude --version  # Check if sandboxing is mentioned
```

---

## API Key Security Checklist

- [ ] API key is in `.env` file, not hardcoded
- [ ] `.env` is in `.gitignore`
- [ ] Never pasted key into a chat or email
- [ ] Set spending limits in Anthropic console
- [ ] Created separate keys for different projects

---

## Permission Levels for Different Scenarios

| Scenario | Recommended Mode | Why |
|----------|-----------------|-----|
| Learning/experimenting | Default (ask every time) | Understand what it's doing |
| Coding a known project | acceptEdits | Fast but controlled |
| Quick one-shot task | bypassPermissions + small folder | Isolated scope |
| Background automation | bypassPermissions + Docker | Full isolation |
| Multi-agent orchestration | Per-agent policies | Each agent has minimum required access |

---

## The Orchestrator Pattern (Preview)

In advanced setups, an orchestrator agent decides what permissions sub-agents get:

```
Orchestrator (read-only + messaging):
+-- Research Agent (web search only, no file writes)
+-- Coding Agent (write to /workspace, no network)
+-- Review Agent (read files, no modifications)
```

This is the principle: trust flows down, but permissions don't automatically cascade. Each agent gets exactly what it needs. We build this in Stage 9.

---

## The Bottom Line

- **Default mode** for exploration: always safe
- **bypassPermissions** only with isolation: Docker, VPS, or dedicated folder
- **API keys**: `.env` file, always
- **Spending limits**: Set them in the Anthropic console now

With that sorted, let's give your agent a memory.
