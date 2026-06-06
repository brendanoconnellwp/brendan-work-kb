---
title: "Project Think Agents as Infrastructure"
source: "https://blog.cloudflare.com/project-think/"
tags: [cloudflare, agents-sdk, durable-objects, project-think, dynamic-workers, codemode]
date_added:: 2026-04-21
last_updated:: 2026-04-21
---

# Project Think: Agents as Infrastructure

> Cloudflare's April 2026 preview of the next-generation Agents SDK. Ships six new primitives (durable execution, sub-agents, persistent sessions, sandboxed code execution, an execution ladder, self-authored extensions) and an opinionated `Think` base class that wires them together. The real argument underneath: agents are 1:1, not 1:many, and that changes the scaling math — which is why serverless (not containers) is the right foundation.

## Overview

The thesis in one paragraph. Traditional applications serve many users from one instance. **Agents are one-to-one** — each agent is a unique instance, one user, one task. If a hundred million knowledge workers each run an agent at modest concurrency, you need capacity for tens of millions of simultaneous sessions. At current per-container costs, that's economically unsustainable. This breaks the standard "spin up a VM per user" architecture, and demands a foundation where idle agents cost zero. That foundation is Durable Objects.

Cloudflare frames the arc as **three waves of AI agents:**

1. **Chatbots** — stateless, reactive, fragile. Useful for answering questions, limited to answering questions.
2. **Coding agents** (Claude Code, Codex, Pi, OpenClaw) — stateful, tool-using, general-purpose. Proved the pattern: LLM + code execution + memory = something that looks like a personal assistant. But runs on your laptop, for one user, no durability.
3. **Agents as infrastructure** — durable, distributed, structurally safe, serverless. Survive failures, cost nothing when idle, enforce security through architecture rather than behavior. This is what Project Think is for.

This three-wave framing is useful for positioning conversations with an agency owner and clients — it gives language to why "just run Claude Code locally" isn't the endgame.

## The six new primitives

### 1. Durable execution with fibers
`runFiber()` is a checkpointable function invocation — registered in SQLite before execution, snapshotable at any point via `ctx.stash()`, and recoverable on restart via `onFiberRecovered`. An LLM call takes 30 seconds; a multi-turn loop can run much longer. At any point the environment can vanish (deploy, restart, resource limit) — fibers survive it. `keepAlive()` prevents eviction during active work; for longer jobs (video gen, CI), the agent starts the work, persists the job ID, hibernates, wakes on callback.

### 2. Sub-agents via Facets
Child Durable Objects colocated with the parent, each with isolated SQLite and typed RPC. Call them like functions: `const researcher = await this.subAgent(ResearchAgent, "research"); await researcher.search(task)`. RPC latency is a function call because they're on the same physical machine. Storage is isolated by default. TypeScript catches misuse at compile time. This is how you build orchestrator patterns without distributed-system tax.

### 3. Persistent sessions (Session API, experimental)
Messages stored as a tree, each with a `parent_id`. Enables: **forking** (explore an alternative path without losing the original), **non-destructive compaction** (summarize older messages, keep originals), **full-text search** across conversation history via SQLite FTS5. This is the storage layer underneath `Think`.

### 4. Sandboxed code execution: Dynamic Workers + codemode
The key insight: **models are better at writing code to use a system than at playing the tool-calling game.** The Cloudflare MCP server exposes just two tools (`search()`, `execute()`) consuming ~1,000 tokens, versus ~1.17M tokens for the naive tool-per-endpoint equivalent — a **99.9% token reduction**. This is [[Cloudflare MCP Enterprise Reference Architecture|Code Mode]] at the language level: instead of round-tripping each tool call through the model, the LLM writes one program that handles the whole task.

Dynamic Workers are the sandbox: fresh V8 isolate spun up at runtime in milliseconds, few megabytes of memory, ~100× faster and ~100× more memory-efficient than a container. Capability model: start with zero ambient authority (`globalOutbound: null`), grant explicit capabilities via bindings. "What do we want this thing to be able to do?" instead of "how do we stop it from doing too much?"

### 5. The execution ladder (Tier 0 → Tier 4)
Additive spectrum of compute environments:
- **Tier 0** — Workspace: durable virtual filesystem (SQLite + R2) with read/write/edit/grep/diff via `@cloudflare/shell`
- **Tier 1** — Dynamic Worker: LLM-generated JS in a sandboxed isolate, no network (`@cloudflare/codemode`)
- **Tier 2** — + npm: `@cloudflare/worker-bundler` fetches packages, bundles with esbuild, loads into the Dynamic Worker. The agent writes `import { z } from "zod"` and it just works.
- **Tier 3** — + headless browser via Cloudflare Browser Run. For when the service doesn't support agents via MCP/APIs yet.
- **Tier 4** — + Cloudflare Sandbox: full OS access with toolchains, repos, `git clone`, `npm test`, `cargo build`, synced bidirectionally with the Workspace.

**Design principle: the agent should be useful at Tier 0 alone.** Each tier is additive. Don't force users to stand up a full sandbox to get value.

### 6. Self-authored extensions
An agent writes its own tools at runtime. The agent generates a TypeScript extension, declares permissions (`network: ["api.github.com"], workspace: "read-write"`), `ExtensionManager` bundles it (with npm deps if needed), loads it into a Dynamic Worker, registers the new tools. The extension persists in DO storage and survives hibernation. "The next time the user asks about pull requests, the agent has a `github_create_pr` tool that didn't exist 30 seconds ago." Self-improvement loop through code — not fine-tuning, not RLHF.

## The economics that make it work

The unit-economics table is the most important single artifact in the post:

| | VMs/Containers | Durable Objects |
|---|---|---|
| Idle cost | Full compute cost, always | **Zero (hibernated)** |
| Scaling | Provision and manage capacity | Automatic, per-agent |
| State | External database required | Built-in SQLite |
| Recovery | You build it | Platform restarts, state survives |
| Identity/routing | Load balancers, sticky sessions | Built-in (name → agent) |
| **10,000 agents, 1% active** | **10,000 always-on instances** | **~100 active at any moment** |

That last row is the punchline. "One agent per customer" or "one agent per email thread" or "one agent per task" becomes economically reasonable because the marginal cost of spawning one is effectively zero.

## The Think base class

`Think` is the opinionated harness on top of the primitives — handles the full chat lifecycle: agentic loop, message persistence, streaming, tool execution, stream resumption, extensions. The minimal subclass is ~5 lines (just override `getModel()`). Lifecycle hooks: `beforeTurn → streamText → beforeToolCall/afterToolCall → onStepFinish → onChatResponse`. Context blocks in the system prompt ("MEMORY [42%, 462/1100 tokens]") let the model proactively persist facts across hibernation.

Works both as a standalone agent and as a sub-agent via `chat()` over RPC. Same WebSocket protocol as `@cloudflare/ai-chat`, so React clients using `useAgentChat` work unchanged.

## Why this matters for agencies

**The serverless-agents economic argument is the thing to internalize.** If the team builds agents per-client-engagement or per-workflow-instance (e.g. one agent per active PR, one agent per retainer client, one agent per call transcript), the DO-based model makes "lots of agents, mostly idle" financially viable. Containers/VMs don't.

For a call-transcript → task-extraction → reconciliation agent, the Project Think execution ladder is a natural fit. Tier 0 workspace holds the transcript + task DB state; Tier 1 codemode handles the reconciliation logic; Tier 2 npm for any third-party SDKs.

The self-authored extensions pattern also maps to a real agency need: a single base agent that can grow tool-specific extensions per client (Shopify, WP Engine, Harvest, Notion) without anyone hand-coding them.

**Caveat:** Project Think is preview. API surface "stable but will continue to evolve." Don't bet production revenue on it yet — but it's the right stack to prototype on.

## Connections

- [[Cloudflare AI Platform Unified Inference Layer]] — The model layer Think sits on top of (AI Gateway + Workers AI).
- [[Cloudflare MCP Enterprise Reference Architecture]] — Same Code Mode insight, applied at the MCP layer for enterprise governance.
- [[AI Agent Landscape 2026]] — Cloudflare's positioning vs. LangGraph, Vercel AI SDK, Mastra, AWS Bedrock Agents.
- [[Claude Code Agent Capabilities]] — The "coding agents" wave that Project Think explicitly builds on. Mentioned by name in the post.
- [[Proactive Agent Workflows]] — Persistent/scheduled/1:1 agent patterns map directly to DO + alarms + fibers.

## Open Questions

- What's the cold-start time for a hibernated DO when a message arrives? Does it feel synchronous to the user?
- Cost per DO-hour active vs. cost per sandboxed Dynamic Worker — where's the break-even vs. a VPS for an always-on agent?
- How do sub-agent Facets interact with deployment? Does each sub-agent class need its own wrangler config?
- Self-authored extensions: what prevents an agent from authoring an extension that then authors more extensions until it's written an operating system? (Answer probably: budget/quota caps, but not discussed.)
- Does the Think base class lock you into AI SDK `streamText`, or can you override the agentic loop entirely?

## Sources

- Raw the agency: `raw/Project Think building the next generation of AI agents on Cloudflare.md`
- Source: https://blog.cloudflare.com/project-think/ (2026-04-15, Agents Week)
- Related packages: `@cloudflare/think`, `@cloudflare/codemode`, `@cloudflare/shell`, `@cloudflare/worker-bundler`

---
tags: [cloudflare, agents-sdk, durable-objects, project-think, dynamic-workers, codemode]
last_updated:: 2026-04-21
