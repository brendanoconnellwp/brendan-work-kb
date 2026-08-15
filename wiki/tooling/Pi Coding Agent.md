# Pi Coding Agent

> The #1 open-source AI coding agent (★87,502 on GitHub). TypeScript-based agent harness with multi-provider LLM support, a full coding agent CLI, terminal UI, and an extensive skill/extension ecosystem.

## Overview

Pi (`@earendil-works/pi`) is the most popular open-source coding agent—a full agent toolkit vs. a single CLI. It's designed to be **self-extensible**: Pi agents can install skills, load extensions, and modify their own capabilities at runtime.

Built by the team behind libgdx (Mario Zechner / badlogic). MIT licensed. TypeScript throughout.

## Architecture

Three core packages:

| Package | Purpose |
|---------|---------|
| `@earendil-works/pi-ai` | Unified multi-provider LLM API — OpenAI, Anthropic, Google, etc. |
| `@earendil-works/pi-agent-core` | Agent runtime with tool calling + state management |
| `@earendil-works/pi-coding-agent` | The interactive coding agent CLI (what most people mean by "Pi") |
| `@earendil-works/pi-telemetry` | Vendor-neutral telemetry contracts and reference adapter |

Plus a TUI library (`@earendil-works/pi-tui`) and a community-driven skill system (`badlogic/pi-skills`, ★2,363).

## Installation

```bash
npm install -g @earendil-works/pi-coding-agent
# or use the standalone binary from GitHub releases
```

## Key Features

**Agent loop:** Pi runs a reasoning → tool-calling → observation loop similar to Claude Code or Codex, but you control the model provider.

**Skill system:** Extend Pi with markdown skills (same format as Claude Code skills). Popular ones in `badlogic/pi-skills` include web research, Docker, database, deployment, and workflow patterns.

**Multi-model:** Pi can use different providers per task—cheap models for simple file ops, expensive models for architecture decisions.

**Permissions:** Pi doesn't include a built-in permission layer by default. For safety, containerize via Docker, a Gondolin extension (micro-VM isolation), or OpenShell sandbox.

**Session sharing:** Pi can publish coding sessions to Hugging Face for open-source training data via `badlogic/pi-share-hf`.

## Ecosystem and Key Extensions

| Project | Stars | What it adds |
|---------|-------|-------------|
| `can1357/oh-my-pi` | ★23,855 | Enhanced Pi experience—hash-anchored edits, optimized tool harness, LSP, Python, browser, subagents |
| `agegr/pi-web` | ★3,999 | Web UI for Pi |
| `badlogic/pi-skills` | ★2,363 | Community skill library |
| `huggingface/tau` | ★2,281 | Python port of Pi's minimalist coding agent |
| `GitJuhb/pi-multi-model-compare` | New | Convex-backed multi-model compare — parallel lanes, rubric judging, pick/prune/graft closeout |

## Using Pi with Convex

The "Convex Pi coding agent" pattern uses Convex (realtime backend platform) as the orchestration layer:

- **Multi-model comparison** — run 9+ model lanes in parallel on the same prompt, judge results via rubric, auto-closeout by grafting best ideas from losing lanes onto the winner. Configured via `~/.pi/agent/compare-models.json`.
- **Isolated agent VMs** — `agent-in-a-box` pattern spins up an isolated VM per conversation, each running a Pi agent orchestrated by Convex.
- **Behavior observability** — per-run evidence-backed behavior cards track recurring model strengths/failure modes.

**Install the multi-model extension:**
```bash
mkdir -p ~/.pi/agent/extensions/multi-model-compare
# copy index.ts, claude-cli-provider.ts, helper-lane-session.ts into it
```

**Usage:** `/cmp [prompt]` or the model-facing `convex_compare()` tool from within an agent session.

## When to Use Pi

- **You want an open-source alternative to Claude Code / Codex** — full control over providers, data, and costs
- **You need multi-model agent workflows** — run the same task across GPT, Claude, Gemini, and compare outputs
- **You're building agentic apps** — Pi is a harness you can embed, not just a CLI
- **You want session transparency** — inspect, share, and learn from every agent action

## Connections

- [[TanStack AI Agent Ecosystem]] — Pi can consume TanStack CLI MCP and Intent-generated skills
- [[RepoPrompt Context Engineering]] — curated context from RepoPrompt can drive Pi agent sessions
- [[Claude Code Agent Capabilities]] — direct comparison: Pi (open, multi-model) vs Claude Code (tight Anthropic integration)
- [[Composio Tool Integration Platform]] — composio tools can be surfaced as Pi tools via MCP
- [[Human-Agent Interface Patterns]] — Pi's session transparency makes it a good case study for agent observability

## Open Questions

- Pi vs Codex CLI for production WordPress/Agency workflows — worth a hands-on comparison
- Containerization patterns for Pi in agency client work — the Gondolin extension approach needs evaluation
- Pi skill format vs Claude Code AGENTS.md format — converging or diverging?

## Sources

- [Pi GitHub](https://github.com/earendil-works/pi)
- [Pi website](https://pi.dev)
- [Pi docs](https://pi.dev/docs/latest)
- [oh-my-pi](https://github.com/can1357/oh-my-pi)
- [pi-web](https://github.com/agegr/pi-web)
- [pi-multi-model-compare](https://github.com/GitJuhb/pi-multi-model-compare)
- [pi-skills](https://github.com/badlogic/pi-skills)

---
tags: [pi, coding-agent, ai-agents, tooling, typescript, open-source, convex]
last_updated: 2026-08-11