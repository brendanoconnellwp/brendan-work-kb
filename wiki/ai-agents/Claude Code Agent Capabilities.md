# Claude Code Agent Capabilities

> Everything Claude Code can do as an autonomous agent — scheduled tasks, hooks, subagents, teams, and the SDK.

## Overview

Claude Code has evolved from an interactive coding assistant into a full agent platform. You can schedule tasks that run while you sleep, define custom subagents for specialized work, orchestrate teams of Claude instances, and hook into every lifecycle event. This article covers what's available today and how to set it up.

## Three Tiers of Scheduling

### /loop — Session-Scoped Polling
The simplest tier. Runs while your Claude Code session is active.

- `/loop 5m check if the deployment finished` — polls every 5 minutes
- `/loop 20m /review-pr 1234` — re-runs a skill on a schedule
- Default interval: 10 minutes. Supports `s`, `m`, `h`, `d` units
- Max 50 tasks per session. Auto-expires after 7 days.
- Also supports one-shot reminders: `remind me at 3pm to push the release branch`

**Best for:** Monitoring during active work — CI builds, deployments, PR status.

### Desktop Scheduled Tasks — Persistent
Created via the Schedule sidebar in Claude Code Desktop, or by asking Claude.

- Options: Manual, Hourly, Daily, Weekdays, Weekly, or custom cron
- Each task fires in its own session. Can use worktree isolation.
- Missed runs: one catch-up run on wake for the most recently missed time
- Permission modes configurable per task — use "Run now" first to pre-approve tools

**Best for:** Daily routines — morning code review, dependency audit, standup prep, overnight research.

### /schedule — Cloud Tasks
Run on Anthropic's infrastructure. Your machine can be off.

- Minimum interval: 1 hour
- Works against a fresh clone of your repo
- Fully autonomous — no permission prompts

**Best for:** Truly hands-off automation — competitor tracking, continuous codebase maintenance, security audits.

## Hooks System

24 lifecycle events you can hook into, configured in `settings.json`:

**Key hooks:**
- **PreToolUse / PostToolUse** — validate or react to tool calls (e.g., auto-format after every file edit)
- **Notification** — desktop alerts when Claude needs input
- **Stop** — verify task completion before Claude stops working
- **SessionStart** (with `compact` matcher) — re-inject context after context compaction
- **TaskCreated / TaskCompleted** — quality gates

**Four handler types:** `command` (shell), `http` (POST to endpoint), `prompt` (single LLM call), `agent` (multi-turn verification).

**Practical example — auto-format after edits:**
```json
{
  "hooks": {
    "PostToolUse": [{
      "matcher": "Edit|Write",
      "hooks": [{
        "type": "command",
        "command": "jq -r '.tool_input.file_path' | xargs npx prettier --write"
      }]
    }]
  }
}
```

## Custom Subagents

Define as markdown files in `.claude/agents/` (project) or `~/.claude/agents/` (global):

```yaml
---
description: "Security reviewer for code audits"
tools: [Read, Glob, Grep]
model: claude-sonnet-4-20250514
isolation: worktree
---
You are a security-focused code reviewer. Analyze for vulnerabilities...
```

Key options: restrict tools, route to cheaper models (Haiku for simple tasks), isolate in worktrees, attach specific MCP servers.

## Agent Teams (Experimental)

Enable with `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1`.

One lead session coordinates teammates. Each teammate is a full Claude Code instance with its own context. They share a task list and message each other directly.

```
Create an agent team to review PR #142. Spawn three reviewers:
- Security implications
- Performance impact
- Test coverage
```

Start with 3-5 teammates. Token costs scale linearly. Requires v2.1.32+.

## Claude Agent SDK

The full Claude Code runtime as a library (`@anthropic-ai/claude-agent-sdk` for TS, `claude-agent-sdk` for Python):

```python
from claude_agent_sdk import query, ClaudeAgentOptions

async for message in query(
    prompt="Find and fix the bug in auth.py",
    options=ClaudeAgentOptions(allowed_tools=["Read", "Edit", "Bash"]),
):
    print(message)
```

Use cases: CI/CD pipelines, production automation, custom agent apps, batch processing. Supports session resumption, MCP server configuration, and all built-in tools.

## Git Worktrees for Parallel Execution

`claude --worktree feature-auth` (or `-w`) creates an isolated worktree with its own branch. The power pattern: run 10-15 parallel Claude sessions, each in its own worktree, each working independently on different tasks.

## MCP Integrations

500+ community MCP servers available. Claude Code can connect to Slack, Notion, GitHub, Supabase, Cloudflare, Playwright (browser automation), Google Calendar, Gmail, Asana, Context7 (live docs), and more. These become tools that agents and scheduled tasks can use.

## What to Set Up Today

1. **Notification hook** — know when Claude needs you
2. **PostToolUse auto-format hook** — consistent code style
3. **`/loop` during active work** — monitor deployments, CI, PRs
4. **Desktop scheduled tasks** — daily code review, dependency audit
5. **Custom subagents** in `.claude/agents/` for your common workflows
6. **Worktrees** for parallel Claude sessions without conflicts

## Connections

- [[AI Agent Landscape 2026]] — where Claude Code fits in the broader agent ecosystem
- [[Proactive Agent Workflows]] — specific workflows you can build with these capabilities
- [[Claude-Powered Dev Workflows]] — Claude Code as an agentic dev partner
- [[Designer-in-Claude Workflow]] — adjacent pattern where non-developers use Claude as the prototyping surface before dev cleanup
- [[Codex Goal Command for WordPress]] — parallel autonomous-coding workflow for WordPress-specific tasks
- [[n8n for Agency Ops]] — complements Claude Code for non-code automation

## Open Questions

- When will Agent Teams move out of experimental?
- Will cloud scheduled tasks support more MCP integrations beyond GitHub?
- How will the 3-15% tool failure rate affect long-running scheduled tasks?

## Sources

- Claude Code official documentation (code.claude.com)
- See `raw/articles/2026-04-09_claude-code-agent-capabilities-deep-dive.md` for full source list

---
tags: [claude-code, ai-agents, scheduling, hooks, sdk, subagents]
date_added:: 2026-04-09
last_updated:: 2026-04-09
