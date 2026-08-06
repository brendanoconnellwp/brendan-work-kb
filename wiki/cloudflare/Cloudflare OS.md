# Cloudflare OS — Open-Source Agent Workspace

> Cloudflare's internal "operating system for AI productivity" — open-sourced August 2026. An agent workspace where every app is a sandboxed instance (a "gadget"), secured by capability-based Gatekeepers. Built by the Workers team on Workers itself.

## Overview

Cloudflare OS is an open-source platform originally built for internal use across Cloudflare's entire workforce — engineering, sales, everything in between. It provides three core capabilities:

1. **Agent chat UI** — preloaded with company-specific knowledge and context
2. **Gadgets** — sandboxed personal apps built by AI on demand
3. **Gatekeepers** — a capability-based security framework for controlling agent/app access to external resources

The key insight: this is not "a SaaS you use" — it's a platform you customize into "*Your Company* OS." The README is explicit: "the idea is not that your company uses Cloudflare OS, but rather that you make it *Your Company* OS."

## The OS Analogy

The "operating system" framing is literal, not just marketing:

| Normal OS | Cloudflare OS |
|-----------|---------------|
| kernel | `packages/workshop-backend` |
| device drivers | `packages/gatekeeper-*` |
| shell | `packages/workshop-frontend` |
| processes | gadgets |
| executables | blueprints |
| users | users |
| ACLs | shared permissions |
| ??? | agents |

The backend legitimately manages users, programs, devices, security — like a real kernel. Gatekeepers (wrapping external APIs) are device drivers. Gadgets (running in Dynamic Worker Facets) are processes.

## Gadgets — The Fundamental Unit

Gadgets are the paradigm shift. When you create a slide deck in Cloudflare OS, the system creates a **private instance** of the slide deck software just for you. This has two profound effects:

1. **Security** — The sandbox prevents any gadget from leaking data to an attacker. The gadget's server runs in a Dynamic Worker with internet access disabled (only communicating via Workers Bindings). The client runs in a sandboxed iframe with CSP and iframe sandbox restrictions.

2. **Customizability** — If the slide deck is missing a feature, ask the agent to add it. Because of the sandbox, it's safe.

This inverts the last 25 years of SaaS architecture. In the AI age, where any user can prompt an agent to add features, centralized software stops making sense.

### Blueprints

If you've built a Gadget worth sharing but don't want to share your live instance, you share a **Blueprint** — a copy of the code others can deploy as their own instance. Like mobile apps: every user runs their own copy of the software.

### Agent-Friendly API by Default

Every Gadget uses **Cap'n Web RPC** for client-server communication. This means:
- Low boilerplate — easy for agents to work with
- The server's API is automatically available for agents to call
- The built-in AI Agent harness uses [[Code Mode (Cloudflare)]] for tool calling

### Real-Time Multiplayer

Every Gadget is backed by a **Durable Object**, making real-time collaboration trivial. The coding agent implements it by default without being asked.

## Gatekeepers — Async-Approval Security

Gatekeepers are like supercharged MCP servers — a Worker per external service that:

- Exposes a clean Cap'n Web API wrapping the native service API
- Handles OAuth authorization
- Enforces narrow access to specific resources the user intended
- Logs every action
- Provides **async human-in-the-loop** approval

### The Async Approval Innovation

Traditional HITL requires the agent to *stop and wait* for approval — so users either deal with stuck agents or give in to `--dangerously-skip-permissions`. 

Gatekeepers solve this: when an action needs approval, the Gatekeeper **simulates the outcome locally**. The agent proceeds with simulated results, queues up more actions, and the user approves/rejects in bulk later. This is a genuine advancement in agent safety UX.

### Available Gatekeepers

GitHub, Google, Cloudflare API, Supabase, Notion, Confluence, Email Workers, Home Assistant, Slack, Spotify, ZoomInfo — each in `packages/gatekeeper-*` with setup instructions.

### Capability-Based Access

Agents and gadgets start with access to **nothing**. You must explicitly introduce them to each resource — paste a link, select from UI, or approve the agent's request. This differs from MCP servers where all your services are ambiently available.

## Built on Workers

Cloudflare OS runs on Cloudflare Workers, heavily using:
- **Durable Objects** — every workspace is a DO, every gadget backed by one
- **Dynamic Workers** — gadgets run in Dynamic Workers with restricted internet
- **Facets** — Gatekeepers install facets into each workspace
- **Code Mode** — agents write and execute code snippets as tool calls

Several of these runtime features were added *specifically* to support Cloudflare OS. Because it runs on `workerd` (the open-source Workers runtime), it can also run on your own servers.

This is **v2** — a complete rewrite from internal v1, putting what they learned on a new foundation. As of August 2026 it's early-access with rough edges.

## Getting Started

- **One-click deploy:** https://os.cloudflare.app/deploy
- **Starter repo (custom deploy):** https://github.com/cloudflare/cloudflare-os-starter
- **Run locally:** `pnpm run-local` (full stack on wrangler + workerd)
- **Source code:** https://github.com/cloudflare/cloudflare-os

## Connections

- Direct descendant of [[Project Think Agents as Infrastructure]] — serverless agents, not container-based
- Complements [[Cloudflare MCP Enterprise Reference Architecture]] — Gatekeepers are a successor/alternative to MCP's ambient-permission model
- Gadget architecture mirrors [[EmDash CMS]] plugins approach — agent-operable, sandboxed components
- The async-approval pattern is relevant to [[Human-Agent Interface Patterns]]
- Runs on the same [[Cloudflare AI Platform Unified Inference Layer]] under the hood

## Open Questions

- How does Cloudflare OS v2 compare to the v1 internal version? What was learned?
- The "not accepting contributions" policy — how will the community ecosystem evolve outside PRs?
- Will Gatekeeper-as-a-Service become a standalone product?
- How does the simulated-result approval model handle rollback when the user rejects?
- What's the pricing model for the services Gatekeepers connect to?

## Sources

- https://github.com/cloudflare/cloudflare-os — README (primary source)
- https://os.cloudflare.app — landing page
- https://github.com/cloudflare/cloudflare-os-starter — deployment starter
- https://x.com/KentonVarda — Cloudflare OS announcements on X
- API data: repo created 2026-04-15, 2,084+ stars, TypeScript, active development

---
tags: [cloudflare, agents, ai-agents, security, open-source, workers]
last_updated: 2026-08-05