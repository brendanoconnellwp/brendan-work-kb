# Cloudflare Project Ideas

> A backlog of things worth building and demoing on Cloudflare Workers — for dev tutorials, agency client work, and everyday life. Generated from a 2026-06-27 research sweep of what the community is actually building.

## Why Now

Cloudflare's stack in 2026 is good and cheap (Workers, D1, R2, Vectorize, Workers AI, Durable Objects, Agents SDK, MCP) but there's a tutorial gap — most existing content is reference docs or 2023-era "hello world" proxies. The platform supports real full-stack apps and AI agents now, but almost nobody is showing the walkthroughs.

Community signals: Reddit threads about "what you guys build with Workers" are full of people building real things (Discord bots, search engines, status pages, secret-sharing tools, multi-agent systems) and the common thread is "nobody knows about this stuff."

## For Dev Tutorials (highest YouTube/demo impact)

### 1. "Build a Discord Bot on CF Workers in 15 Minutes"
The official discord/cloudflare-sample-app exists but nobody's made the polished tutorial. Show slash commands, D1 persistence, real-time interaction, and the $0 hosting story.

**Stack:** Workers (interactions endpoint), D1 (user data/scores), KV (rate limiting), Cron Triggers (scheduled messages)

### 2. "Your Own Perplexity Clone on the CF Free Tier"
A Reddit thread about this got big but there's no YouTube walkthrough. Full AI-powered search: pages → Workers AI embeddings → Vectorize similarity search → Workers AI LLM answer synthesis.

**Stack:** Workers, D1 (metadata+chat history), Vectorize (embeddings index), Workers AI (both embedding model + text generation), KV (cache), Browser Rendering (page scraping)

### 3. "Deploy a Remote MCP Server in 5 Minutes"
Cloudflare just made this trivially easy. This is *the* hot topic right now — agents talking to APIs via MCP is where the industry is moving.

**Stack:** Workers + MCP adapter, optionally: D1 for state, Stripe for pay-per-call, Workers AI for any AI-powered tools

### 4. "Run a Multi-Agent System on $0/Month"
The Cloudflare Agents SDK (Durable Objects per agent) is a legit differentiator vs. container-based agent orchestrators. Show agent-to-agent communication, persistent memory that hibernates when idle, sub-agent delegation.

**Stack:** Agents SDK (`cloudflare/agents`), Workers AI, D1, Durable Objects

### 5. "Serverless URL Shortener with Analytics"
Classic "hello world" project but nobody's shipping it with real analytics (geolocation, click tracking, user-agent parsing via Workers).

**Stack:** Workers (redirect handler + API), D1 (links + analytics), KV (hot/cache), Pages (admin dashboard), Workers Analytics Engine

## For Agency/SMB Clients

### 6. Multi-Client Uptime Watchdog
Cron trigger per client site. Check HTTP status, SSL expiry, WP plugin vulnerability alerts via WP APIs. AI-drafted fix message when something's red. Push alerts to Discord/Slack/email.

**Stack:** Cron Triggers, D1 (results history + client configs), Workers AI (draft fix messages), Email Service (alerts), Browser Rendering (visual checks)

### 7. Contact Form AI Gateway
Sits between WordPress (or any form) and CRM. Workers AI classifies inbound leads (lead / support / spam / quote request). Auto-responds to support-tier. Pushes qualified leads to CRM via webhook. Drafts quote estimates from rate cards for sales inquiries.

**Stack:** Workers (endpoint + AI call), Workers AI (classification + drafting), Queues (async processing), D1 (lead history + rate cards), Email Service (notifications)

### 8. Content Translation Edge Worker
For client sites with multilingual audiences. Intercepts requests with `?lang=es` or Accept-Language headers, translates rendered HTML body via Workers AI, caches translated version at the edge. No plugin, no database overhead, no monthly DeepL bill.

**Stack:** Workers (request interception), Workers AI (translation model), Cache API (per-language caching), KV (translation config per client site)

### 9. Client Monthly Report Generator
Cron job pulls from Cloudflare Analytics + GA4 + a D1 health database. Workers AI writes plain-English "what happened this month" (traffic changes, uptime, top pages, security blocks). Browser Rendering renders a branded PDF. Emails it to the client.

**Stack:** Cron Triggers, Workers AI (narrative generation), Browser Rendering (PDF from HTML), Email Service, D1 (historical health data + client configs)

## For Everyday Life

### 10. Personal RSS-to-Telegram Bridge
RSS polling cron → AI summarization → push to Telegram. The existing Telegram RSS bots are bare-bones. Add Workers AI for "tl;dr" filtering and smart dedup.

**Stack:** Cron Triggers, D1 (subscriptions + seen items), Workers AI (summarization), Telegram API via Worker (push)

### 11. Serverless File Share (Firefox Send Replacement)
R2 + Workers + auto-expiring links. Upload via Worker endpoint, get a one-time link. Files deleted automatically after download or expiry. Clean UI via Pages.

**Stack:** Workers (upload/download endpoints), R2 (file storage), KV (link metadata + expiry), Pages (UI), Cron Triggers (garbage collection)

### 12. Website Change Monitor
Cron trigger + Browser Rendering snapshot + AI diff. Watch competitor pricing pages, blog updates, or any site for changes. Push alerts via Discord or Telegram when content changes.

**Stack:** Cron Triggers, Browser Rendering (snapshots via Playwright), R2 (screenshot archive), Workers AI (diff analysis), D1 (change history + configs), Email/Telegram (alerts)

### 13. Smart Cache Warmer
Instead of warming every page on a site, use Analytics API (or own request logging) to learn which URLs actually get traffic. Durable Object stores the priority queue. Only warms popular pages.

**Stack:** Cron Triggers, Durable Objects (priority queue), D1 (traffic stats), Workers (warming requests)

## Community Trends (from 2025-2026 sweep)

What others are actually building on CF right now:

- **AI-powered search engines** — Workers + Vectorize + D1 + Workers AI. Several independent builders, one Reddit thread with huge engagement
- **StatusPage alternatives** — `krzko/statusflare` (GitHub) is a popular example
- **Secret-sharing CLIs** — Workers + KV with encrypted, auto-expiring links
- **Multi-agent systems** — Claude Code autonomously building agent systems on Workers
- **Discord bots** — Official template from Discord, plus community frameworks
- **Telegram bots** — Massive ecosystem. ChatGPT-Telegram-Workers (5k+ stars) is famously single-file, no-deps, deploy
- **Blogging platforms** — Full CMS on Workers + D1 + R2
- **MCP servers** — Paid MCP servers with Stripe, authenticated MCP, x402 pay-per-call
- **AI deception honeypots** — Zero-infrastructure honeypots pretending to be admin tools
- **Polling platforms** — Open-source polling with SvelteKit + Durable Objects

## Why CF for These (vs. Vercel/Railway/AWS)

- Free tier is genuinely usable: 100k requests/day, 1M Workers KV reads/day, D1 5GB, Vectorize indexes
- No cold start penalty for agents (Durable Objects hibernate at $0 when idle)
- Workers AI includes the GPU cost — no separate inference bill
- Global edge distribution is free
- The Agents SDK (Durable Objects) is a genuinely unique platform advantage — no other serverless provider offers persistent stateful agents

## Open Questions

- What's the best way to monetize these as tutorials? YouTube ad rev, paid Notion/PDF guides, or just as lead gen for Digital Anchor?
- Could the multi-client watchdog become a micro-SaaS on its own? StatusPage-for-WordPress is an underexplored niche.
- The Discord bot tutorial angle is well-trodden but nobody's shown it WITH AI (Workers AI for smart replies, D1 for user memory).

---
tags: [cloudflare, project-ideas, tutorials, agents, workers]
last_updated: 2026-06-27