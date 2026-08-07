# Cloudflare Computer — Agent Runtime (@cloudflare/computer)

> Cloudflare's open-source agent runtime that gives every agent its own virtual filesystem and execution environment, dynamically orchestrating between fast isolates and full Linux containers.

Launched August 3, 2026 during [[Agents Week 2026 Overview|Agents Week]].

## Overview

[@cloudflare/computer](https://github.com/cloudflare/computer) is an npm package that runs inside a **Durable Object**, providing an agent with:

- A **virtual filesystem** backed by SQLite in the Durable Object
- A **pluggable execution runtime** with three backends: container, isolate shell, isolate JavaScript
- A single `workspace.runtime.exec(source, { backend })` entry point

The core insight: **agents need a computer, not a container**. Most agent work (coding, text manipulation, document creation) can run in fast isolates. Containers are only needed for ~10% of work (full Linux binaries, `pandoc`, `ffmpeg`, network tools). The workspace abstraction lets you use both without managing infrastructure.

## Key Concepts

### Workspace
The central abstraction — a virtual filesystem (VFS) that lives in a Durable Object's SQLite. You populate it from cloud storage, git repos, or declaratively. Multiple backends see the exact same files.

### Three Execution Backends

| Backend | How it runs | Best for | Cost/Speed |
|---------|-------------|----------|------------|
| **Container** | `computerd` daemon in a sandbox container via FUSE mount + capnweb RPC | Full Linux userland, `pandoc`, `ffmpeg`, `npm install`, real binaries | Slower, more expensive |
| **Isolate Shell** | `just-bash` in a Dynamic Worker via Workers RPC | Quick shell commands, text processing, `curl` | Fast, cheap |
| **Isolate JavaScript** | ECMAScript module in a Dynamic Worker | Structured computation, `node:fs/promises`, `ws:git`, `ws:artifacts` | Fast, cheap |

A Workspace can register multiple backends under stable IDs. Backends connect lazily on first use.

### Why it matters for agents
- No container spinning overhead for simple operations
- Files persist in the DO regardless of which backend touched them last
- The **brain** (agent loop) is separate from the **hands** (sandbox execution) — the workspace is the shared state
- Scales to billions of concurrent agents because most work lands in isolates, not containers

## Examples (from the repo)

All at `examples/` in the GitHub repo:

### think — Chat agent with a computer
A [`@cloudflare/think`](https://www.npmjs.com/package/@cloudflare/think) agent with a terminal UI. The agent has workspace file + shell tools, with both the fast worker-shell backend and a container backend. You type, it uses its tools to read/write/execute in its workspace.

### tutorial — PDF recipe card agent
A single endpoint. POST a dish name → agent finds a recipe → writes a markdown card → runs `pandoc` in the container to produce a PDF → returns a signed link. The `write` tool runs on the host (DO), the `bash pandoc` runs in the container — same filesystem.

### artifacts — Generate + publish a Worker project
Creates a Worker project in a workspace, publishes it to Cloudflare Artifacts, returns a clone-ready URL. Demonstrates using the workspace as a build environment.

### assets — Text prompt → image → shareable link
Uses Workers AI (Flux) to generate an image, writes to the workspace, uploads to R2, returns a presigned URL via `@cloudflare/computer/assets`.

### container / worker-shell / worker-javascript
Three parallel examples showing the same HTTP surface (`write` / `read` / `exec`) on different backends. Container runs `computerd` inside a sandbox. worker-shell runs `just-bash` in a Dynamic Worker. worker-javascript evaluates an ES module in a Dynamic Worker.

### think-compare-runtimes
A web UI that runs the same agent task against container and worker runtimes side-by-side.

## What this means for you

### For Digital Anchor (client work)
- **White-label agent infrastructure**: Give each client's agent its own workspace on Cloudflare. No managing per-user containers.
- **SMB automation dashboards**: An agent that can edit files, run shell commands, and generate PDFs/reports from a single Durable Object — deploy per customer.
- **Support ticket triage** (mentioned in the blog): Agent has filesystem + git + shell tools + product-specific tools, all wired through a single workspace.

### For content / YouTube
- **Demo gold**: "Build an agent that has its own computer on Cloudflare" — set up the tutorial example live, show the workspace concept.
- **Compare with OpenAI Code Interpreter / Claude Code**: How Cloudflare's approach differs (serverless, edge, open-source, isolate-first).
- **Agents Week roundup**: This + other announcements as a cohesive video.

### For personal projects
- The `think` + workspace combo is essentially a self-hosted agent sandbox on Workers.
- Combine with Workers AI for a fully serverless coding agent pipeline.
- The artifact generation pattern (workspace → build → publish) maps directly to generating blog content, project scaffolding, or client deliverables.

### Key differentiator vs competitors
- **vs OpenAI Code Interpreter**: Runs on your Cloudflare account, not OpenAI's sandbox. Open-source. Cheaper because isolates handle the common case.
- **vs Claude Code / Codex**: Those give an agent a local or remote container. Cloudflare Computer runs at the edge, scales horizontally, and the VFS persists in the DO.
- **vs playground sandboxes**: Production-grade infrastructure, not a dev tool. Designed for "hundreds of millions of concurrent agents."

## Connections

- [[Project Think Agents as Infrastructure]] — related architecture for agent compute
- [[Support Ticket Orchestrator Template]] — could be powered by a workspace+agent combo
- [[EmDash CMS]] — workspace as a CMS-adjacent concept
- `[[Agents Week 2026]]` — broader context of Cloudflare's agent platform announcements

## Open Questions

- Pricing for the different backend types (isolate vs container execution)
- How `@cloudflare/computer` relates to or replaces `@cloudflare/think` tooling
- Whether the workspace can be mounted by external tools (e.g., VS Code over SSH)

## Sources

- Blog post: [Your agent needs a computer, not a container — introducing @cloudflare/computer](https://blog.cloudflare.com/cloudflare-computer/) (Aug 3, 2026)
- GitHub: [cloudflare/computer](https://github.com/cloudflare/computer) — README, examples, package docs
- [@cloudflare/think](https://www.npmjs.com/package/@cloudflare/think) — companion agent framework
- [just-bash](https://github.com/vercel-labs/just-bash) — shell backend used in worker-shell example
- [cloudflare/sandbox-sdk](https://github.com/cloudflare/sandbox-sdk) — precursor container bridge

---
tags: [cloudflare, agents, ai, computer-use, serverless, agents-week-2026, durable-objects]
last_updated: 2026-08-03