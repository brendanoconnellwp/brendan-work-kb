# Support Ticket Orchestrator — Multi-Agent Template

> A composable, hot-swappable multi-agent system on Cloudflare Agents SDK. Built to be forked, customized, and demoed. Each sub-agent is a self-contained module you can swap out without touching the rest.

## Why This Pattern

Most multi-agent demos are two agents saying "hello" back and forth. This one solves a real problem every business has: something comes in (email, form, chat) and needs to be routed, researched, and responded to. The CF Agents SDK makes each sub-agent a durable, stateful, independent Durable Object — meaning each one has its own SQLite database, survives hibernation, and can be worked on independently.

The template is designed so that at a glance, anyone can see: "Oh, that's where I plug in my CRM. That's where I add my knowledge base. That's where I swap the email delivery."

## Architecture

```
                    ┌─────────────────────────────────┐
                    │       OrchestratorAgent          │
                    │  (Think base — LLM-powered)      │
                    │  Receives input, manages flow    │
                    └────┬──────┬──────┬──────┬───────┘
                         │      │      │      │
              ┌──────────┘      │      │      └──────────┐
              ▼                  ▼      ▼                  ▼
     ┌──────────────┐  ┌──────────────┐  ┌──────────────┐
     │ Classifier    │  │ CustomerLookup│  │ KnowledgeSearch│
     │ Agent         │  │ Agent        │  │ Agent         │
     │ (Facet/DO)    │  │ (Facet/DO)   │  │ (Facet/DO)    │
     └───────┬───────┘  └──────┬───────┘  └──────┬───────┘
             │                 │                  │
             ▼                 ▼                  ▼
     category, urgency   customer profile    relevant docs
             │                 │                  │
             └─────────────────┼──────────────────┘
                               ▼
                    ┌──────────────────┐
                    │   DraftAgent     │
                    │  (Facet/DO)      │
                    │  Synthesizes     │
                    │  and writes reply│
                    └────────┬─────────┘
                             │
                             ▼
                    ┌──────────────────┐
                    │  DeliveryAgent   │
                    │  Sends response  │
                    └──────────────────┘
```

## Flow

1. **Input arrives** — email webhook, API call, chat message, or cron poll
2. **OrchestratorAgent** receives it, spawns sub-agents
3. **ClassifierAgent** → urgency (p1-p5), category (billing/tech/sales), sentiment
4. **CustomerLookupAgent** → who is this? what plan? past tickets?
5. **KnowledgeSearchAgent** → what do we know about this problem?
6. **DraftAgent** → writes a reply using all context
7. **Orchestrator** presents to human for approval (or auto-sends for low-urgency)
8. **DeliveryAgent** → actually sends it (email, Slack, ticket system)

## The Hot-Swappable Components

Each sub-agent is designed with a clear interface and a default implementation. Forkers replace the implementation file — the interface stays the same.

### 1. Input Source (not an agent — a hook)

```
src/inputs/
  ├── email-webhook.ts     # Cloudflare Email Routing → Worker
  ├── api-endpoint.ts      # POST /ticket
  └── cron-poller.ts       # Polls a mailbox or API
```

**What to swap:** Wire up whichever input your business uses. The template ships with all three. Delete the ones you don't need.

### 2. ClassifierAgent

```
src/agents/classifier/
  ├── index.ts             # Agent class + interface (DOES NOT CHANGE)
  ├── prompts.ts           # System prompts for classification (SWAP THIS)
  └── criteria.ts          # Category + urgency definitions (SWAP THIS)
```

**Default:** Workers AI classifies into 4 categories (billing, technical, sales, other) and 3 urgency levels based on keywords + LLM judgment.

**To swap:** Edit `criteria.ts` to match your business categories. Edit `prompts.ts` to tune the LLM's classification style.

### 3. CustomerLookupAgent

```
src/agents/customer-lookup/
  ├── index.ts             # Agent class + interface (DOES NOT CHANGE)
  ├── adapters/            # (PICK ONE)
  │   ├── d1-default.ts    # Simple D1 table — works out of box
  │   ├── hubspot.ts       # HubSpot CRM API
  │   ├── salesforce.ts    # Salesforce API
  │   ├── teamwork.ts      # Teamwork.com API
  │   └── manual-prompt.ts # Just asks the LLM what it knows
  └── schema.ts            # Return type: CustomerProfile (SHARED)
```

**Default:** A D1 table with seed data. Deploy → it just works. Add a few example customers to the seed file and you're running.

**To swap:** Pick an adapter from the `adapters/` folder, wire up credentials in `wrangler.jsonc` secrets. The interface never changes — it always returns `{ name, email, plan, history, notes }`.

### 4. KnowledgeSearchAgent

```
src/agents/knowledge-search/
  ├── index.ts             # Agent class + interface (DOES NOT CHANGE)
  ├── adapters/            # (PICK ONE)
  │   ├── d1-faq.ts        # Simple D1 table of Q&A pairs
  │   ├── vectorize.ts     # Vectorize + Workers AI embeddings (RAG)
  │   ├── web-search.ts    # Searches your public docs site
  │   ├── files.ts         # Reads from R2 buckets (PDFs/docs)
  │   └── manual-knowledge.ts # LLM uses its own training data
  └── schema.ts            # Return type: KnowledgeResult[] (SHARED)
```

**Default:** D1 table with FAQ entries. Add your own Q&A pairs to the seed migration, and it works as a fast lookup.

**To swap:** Upgrade to `vectorize.ts` once you have real documentation — that's the production path. Or `web-search.ts` if your docs are public.

### 5. DraftAgent

```
src/agents/draft/
  ├── index.ts             # Agent class + interface (DOES NOT CHANGE)
  ├── prompts.ts           # Tone/persona templates (SWAP THIS)
  ├── reply-templates.ts   # Email templates per category (SWAP THIS)
  └── schemas.ts           # DraftOutput shape (SHARED)
```

**Default:** Professional-but-friendly tone. Three templates: billing, technical, general.

**To swap:** Edit `prompts.ts` to match your voice. Add reply templates for your categories.

### 6. DeliveryAgent

```
src/agents/delivery/
  ├── index.ts             # Agent class + interface (DOES NOT CHANGE)
  ├── adapters/            # (PICK ONE)
  │   ├── email-cloudflare.ts # Cloudflare Email Service (default)
  │   ├── slack.ts         # Posts to a Slack channel
  │   ├── discord.ts       # Posts to a Discord channel
  │   ├── teamwork-api.ts  # Creates a ticket in Teamwork
  │   └── print-json.ts    # Just outputs to console (for demo/debug)
  └── schema.ts            # DeliveryResult shape (SHARED)
```

**Default:** Outputs to console + optionally sends via Cloudflare Email Service (no extra API keys needed).

**To swap:** Pick the platform you actually use. The adapter folder makes it one-file change.

### 7. Human Approval Gate (optional, built-in)

```
src/orchestrator/
  ├── index.ts             # OrchestratorAgent (DOES NOT CHANGE much)
  ├── approval-gate.ts     # Pauses for human review (TUNE THRESHOLDS)
  └── routing.ts           # Which sub-agents to dispatch for what (TUNE)
```

**Default:** Auto-sends for low urgency, pauses for human approval on high urgency (P1-P2). Configurable in `approval-gate.ts`.

---

## Project Structure (Friendly Layout)

```
support-orchestrator/
├── src/
│   ├── orchestrator/
│   │   ├── index.ts           # OrchestratorAgent — the parent
│   │   ├── approval-gate.ts   # Human-in-the-loop config
│   │   └── routing.ts         # Which sub-agents for which flows
│   ├── agents/
│   │   ├── classifier/        # See above
│   │   ├── customer-lookup/   # See above
│   │   ├── knowledge-search/  # See above
│   │   ├── draft/             # See above
│   │   └── delivery/          # See above
│   ├── inputs/
│   │   ├── email-webhook.ts
│   │   ├── api-endpoint.ts
│   │   └── cron-poller.ts
│   ├── seed-data/
│   │   ├── customers.json     # Sample customers for D1 seed
│   │   ├── faq.json           # Sample knowledge base
│   │   └── sample-tickets.json # For testing
│   ├── app.tsx                # Chat UI (optional — for demos)
│   └── client.tsx
├── migrations/
│   ├── 001_seed_customers.sql
│   └── 002_seed_faq.sql
├── wrangler.jsonc
└── README.md                  # "How to fork this" guide
```

**Key design rule:** Every adapter folder has a `README.md` inside it explaining what each file does and what to change. When you open the project, you see the adapter folders first.

---

## Demo Mode vs Production Mode

The template ships with three run modes controlled by environment variables:

| Mode | What it does | How to use it |
|------|-------------|---------------|
| `DEMO=true` | Uses seed data + console output. No external APIs needed. Works instantly after deploy. | First deploy, tutorials, live demos |
| `LIVE=true` | Uses real adapters but still outputs to console. Test with real data before delivering. | Staging / testing |
| `PRODUCTION=true` | Full pipeline: real CRM, real knowledge base, real email delivery. | Production |

This means the YouTube demo video starts with `DEMO=true` and shows the whole thing working in 5 minutes. Then you swap to `LIVE` with your actual customer data. Then to `PRODUCTION` when ready.

---

## The Credibility Story (YouTube / GitHub)

The narrative that makes this work:

1. **"Every business has an input that needs processing"** — relatable starting point
2. **"Most multi-agent demos are fake. Here's one that solves a real problem."** — hooks the skeptical dev
3. **"Each sub-agent is its own Durable Object with its own SQLite database. They survive crashes. They cost $0 when idle."** — the technical differentiator
4. **"Swap the adapter files — not the logic. See the `adapters/` folder? That's the only thing you change."** — the hot-swappable sell
5. **"Deploy it right now with $DEMO=true and seed data. No API keys needed."** — immediate action

GitHub README should have a **quick-start section** that's literally:

```bash
npx create-cloudflare@latest --template brendanoconnellwp/support-orchestrator
npm run deploy
# Done. Open the URL.
```

Then a **"Make It Yours"** section with a table showing each adapter choice.

---

## Open Questions (for Brendan)

- Should there be a frontend at all, or is this purely API-driven with optional chat UI? The chat UI makes for better YouTube demos but adds complexity.
- The email input webhook is the most realistic for you (email = your ticket system). Should that be the *primary* demo flow, and the API/chat flows secondary?
- For the YouTube video: demo it against a real SaaS product you use (e.g., "pretend this is a Hacker News comment that needs a response") or keep it abstract?

---
tags: [cloudflare, agents, template, multi-agent, support, orchestrator, project]
last_updated: 2026-06-27