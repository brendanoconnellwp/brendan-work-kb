# RepoPrompt Context Engineering

> Open-source native macOS app for context engineering — building dense, reviewable prompts for AI coding agents by curating files, CodeMaps, Git context, and repository structure.

## Overview

RepoPrompt CE is the community edition of RepoPrompt (Apache 2.0, free). It solves a specific problem: **agents are only as good as the context you give them.** Instead of dumping your whole codebase into a prompt, RepoPrompt lets you assemble the exact slice of context an agent needs.

A native macOS app (Swift/SwiftUI) with a bundled MCP server and CLI harness. Homebrew installable.

## Installation

```bash
brew tap repoprompt/repoprompt-ce
brew install --cask repoprompt-ce
```

Or build from source (requires macOS 26+ and Xcode 26).

## Key Concepts

### Context Engineering
Build focused, token-efficient prompts combining:
- **File trees** — repository structure overview
- **Selected file contents** — full files or line-range slices
- **CodeMaps** — high-level architectural maps of the codebase
- **Git diffs** — staged/unstaged changes as context

### Context Builder
An agent-driven mode: let an AI agent explore the repository, identify relevant files, and curate context within a token budget. The agent makes MCP calls, and RepoPrompt surfaces request-scoped progress to the client.

### Context Composer
A review surface where you:
- Inspect selected files and CodeMaps
- Configure prompt packaging and Git context inclusion
- Copy a model-ready prompt (or pipe it to an agent)

No blind handoffs — you see exactly what the agent will see before it acts.

### Agent Orchestration
Run and coordinate CLI-backed coding agents directly from the macOS app:
- **Worktree management** — app-managed git worktrees for isolated agent sessions
- **`.worktreeinclude`** — declare local files to copy into worktrees
- **Multi-root workspaces** — work across related repos, packages, and docs in one window

### MCP Server + CLI
RepoPrompt exposes its context capabilities as an MCP server, so MCP-compatible clients (Codex, Claude Code, Cursor, Pi) can query repository structure, inspect files, and curate context programmatically.

## How It Fits In Your Workflow

1. Open your project in RepoPrompt
2. Build context: pick relevant files, CodeMaps, Git state
3. Review and refine the assembled prompt
4. Hand off to your coding agent (Codex, Pi, Claude Code)
5. Agent works in an isolated worktree
6. Review the diff, iterate

You spend 2-3 minutes curating context, save 10-15 minutes of agent hallucinations and wrong-file edits.

## When to Use

- **Complex multi-file refactors** — agent needs to see the whole picture, not guess
- **Onboarding a new agent to an existing codebase** — provide architectural context upfront
- **Token budget management** — when you're hitting context limits, curate instead of truncate
- **Paired sessions** — review context with a teammate before letting an agent loose
- **Multi-repo work** — one workspace across interdependent packages

## Connections

- [[Pi Coding Agent]] — RepoPrompt can feed curated context into Pi sessions
- [[TanStack AI Agent Ecosystem]] — RepoPrompt's MCP server lets TanStack AI agents consume curated context
- [[Claude Code Agent Capabilities]] — direct integration: Claude Code can load RepoPrompt output
- [[Composio Tool Integration Platform]] — composio provides the tool integrations; RepoPrompt provides the context quality
- [[Second Brain Gameplan]] — RepoPrompt is the project-level analog of the second brain: curated context vs. curated knowledge

## Open Questions

- macOS-only limits team adoption — worth watching for Windows/Linux support
- How does it compare to Cursor's built-in context management?
- Token budget tuning for different models — does it optimize for Claude vs GPT vs Gemini?

## Sources

- [RepoPrompt CE GitHub](https://github.com/repoprompt/repoprompt-ce)
- [RepoPrompt website](https://repoprompt.com)

---
tags: [repoprompt, context-engineering, tooling, macos, mcp, coding-agents]
last_updated: 2026-08-11