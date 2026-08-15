# TanStack AI Agent Ecosystem

> TanStack's growing suite of AI agent infrastructure: a provider-agnostic AI SDK, agent-friendly CLI with MCP, a skill-shipping tool for library authors, and a durable execution engine.

## Overview

TanStack (Tanner Linsley's framework ecosystem) has expanded beyond React Query, Router, and Table into a full **AI agent toolchain**. Unlike vertical-specific tools, these are general-purpose building blocks that slot into any stack—React, Vue, Svelte, Solid, or server-only.

Four projects, worth understanding together:

| Project | Stars | What it does |
|---------|-------|-------------|
| **TanStack AI** (`@tanstack/ai`) | ★2,982 | Provider-agnostic TS SDK for streaming chat, tool calling, agents, structured output, multimodal, realtime voice |
| **TanStack CLI** (`@tanstack/cli`) | ★1,290 | Scaffolding + MCP server + Agent Skills installer |
| **TanStack Intent** (`@tanstack/intent`) | ★321 | CLI for library maintainers to ship Agent Skills alongside npm packages |
| **TanStack Workflow** (`@tanstack/workflow`) | ★195 | Type-safe durable execution for agents—resumable runs, append-only history |

Won **2026 JavaScript Open Source Award for AI Project of the Year**.

## TanStack AI SDK

The flagship. Think Vercel AI SDK but with TanStack's multiplatform DNA.

**Core concepts:**
- **Providers** — OpenAI, Anthropic, Gemini, plus any custom adapter
- **Activities** — composable building blocks: chat, image gen, audio, video, speech, transcription, summarization, realtime
- **Tools** — type-safe tool definitions via `toolDefinition()` shared between client/server
- **Code Mode** — LLM writes + executes TypeScript in an isolated sandbox for loops, branches, and parallel calls
- **Structured output** — Zod, ArkType, Valibot, or plain JSON Schema
- **Framework bindings** — React, Solid, Vue, Svelte, Preact + headless client

**Install:**
```bash
npm install @tanstack/ai @tanstack/ai-provider-openai
```

**Quick example (React):**
```tsx
import { useChat } from '@tanstack/ai-react'

function Chat() {
  const { messages, input, handleSubmit } = useChat({
    provider: 'openai',
    model: 'gpt-4',
  })
  return <div>...</div>
}
```

**When to use:** Building AI-native apps (chat UIs, agent dashboards, multimodal tools) where you want framework-native ergonomics and provider flexibility. If you're already in TanStack's ecosystem, this is the natural fit.

## TanStack CLI

Beyond scaffolding, the CLI includes an **MCP server** that exposes TanStack project operations to AI agents:

```bash
npx @tanstack/cli mcp
```

This means agents (Codex, Claude Code, Cursor, Pi) can interact with TanStack projects through MCP tools—create routes, manage queries, scaffold tables, etc.

Also handles **Agent Skills installation** — one-command setup of agent-optimized docs and rules into projects.

## TanStack Intent

The "for library authors" tool. It generates:
- **Agent Skills** — markdown skill files an agent can load (think CLAUDE.md / AGENTS.md style)
- **Agent metadata** — API surfaces, type signatures, common patterns
- **Validation** — checks that published skills match the actual library API

Publish alongside your npm package so agents can discover and use your library correctly without hallucinating APIs.

**When to use:** You maintain an open-source library and want AI agents to use it correctly. Or you're building internal packages and want your coding agents to stop guessing the API.

## TanStack Workflow

Durable execution for TypeScript agents. Key features:
- **Resumable runs** — survives process restarts
- **Append-only history** — full replay/audit trail
- **Compensable steps** — rollback on failure
- **Framework bindings** — React, Solid, Vue, Svelte

Competes with Temporal but is TypeScript-native and TanStack-integrated.

**When to use:** Agent workflows that need reliability—multi-step orchestrations, async approval flows, long-running data pipelines where a crash mid-way shouldn't lose progress.

## Connections

- [[Pi Coding Agent]] — Pi agents can use TanStack's MCP server and Intent-generated skills
- [[RepoPrompt Context Engineering]] — context curated in RepoPrompt can be fed into TanStack AI-powered agents
- [[Composio Tool Integration Platform]] — composio provides alternative tool integrations; TanStack provides the SDK layer
- [[Claude Code Agent Capabilities]] — Claude Code can consume TanStack Intent skills
- [[Human-Agent Interface Patterns]] — TanStack AI's framework bindings handle the human-facing side of agent interactions

## Open Questions

- TanStack AI vs Vercel AI SDK for production — TanStack claims better TypeScript DX and provider flexibility. Worth a head-to-head eval on your next AI UI project.
- How much overlap between TanStack Workflow and Cloudflare Workflows? Both do durable execution, different deployment models.

## Sources

- [TanStack AI GitHub](https://github.com/TanStack/ai)
- [TanStack CLI GitHub](https://github.com/TanStack/cli)
- [TanStack Intent GitHub](https://github.com/TanStack/intent)
- [TanStack Workflow GitHub](https://github.com/TanStack/workflow)
- [TanStack AI docs](https://tanstack.com/ai)

---
tags: [tanstack, ai-agents, tooling, typescript, sdk, mcp]
last_updated: 2026-08-11