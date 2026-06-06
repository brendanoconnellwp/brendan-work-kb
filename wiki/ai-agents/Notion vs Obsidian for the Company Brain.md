# Notion vs Obsidian for the Company Brain

> The architecture decision for a small agency's second brain: **Notion should become the canonical team-facing company brain; Obsidian/Markdown should remain the AI engineer's private agent workshop, research layer, and durable mirror.** Notion's new CLI, Markdown API, MCP, Workers, and Custom Agent tooling make this more viable than the original Obsidian-first plan — but not safe enough to use without schemas, permissions, review gates, and a local working layer.

## Overview

The original approach to an agency AI brain treated Obsidian as the likely mechanism: raw signals from Notion, Granola, Slack, and other tools would be ingested into a local Markdown wiki, synthesized by agents, and exposed back to the team through a Slack interface. That made sense when the main technical advantage was agent-friendliness — Markdown files are easy to read, diff, search, refactor, back up, and edit with coding agents.

Notion's new developer platform changes the calculation. It's no longer just a collaborative wiki/database product with an awkward block API. It now has an official CLI (`ntn`), hosted Notion Workers, Custom Agent tools, first-party MCP support, and Markdown content endpoints that let agents read and update pages as Notion-flavored Markdown. This narrows the gap between "agent-friendly Markdown" and "team-friendly Notion."

The result is not a full reversal into "everything in Notion." The right model is a **split-brain architecture** with clear roles:

```text
Obsidian / Markdown / AI agent = workshop
Notion / databases / comments / permissions = office
```

Agents can make a mess in the workshop: ingest, draft, compare, synthesize, test prompts, run local scripts, and produce opinionated recommendations. The team sees the reviewed output in the office: canonical project pages, meeting notes, tasks, decisions, status updates, SOPs, and AI review queues inside Notion.

## The bottom line

For a small web agency:

1. **Use Notion as the canonical team brain.** It is already the team's operational home, and it has the collaboration, permissions, databases, comments, templates, and nontechnical UX that Obsidian lacks.
2. **Keep Obsidian as the AI engineer's private layer.** It remains the better place for daily dumps, raw synthesis, research drafts, local experiments, Git/diff review, and durable backups.
3. **Build the first agent workflows into Notion review queues, not hidden Markdown notes.** The highest-value workflows — transcript → tasks, Notion → Harvest workplans, project status briefs — only become organizationally real when PMs and the team can inspect, edit, approve, and use them.
4. **Do not let agents write freely across Notion.** Use limited integration scopes, explicit database schemas, confidence/evidence fields, `AI-generated?` flags, confidentiality levels, and human approval gates.
5. **Treat Notion's newest agent features as promising but plan-gated.** Build on the stable surfaces first: Notion API, CLI, Markdown endpoints, and controlled integrations. Add Notion Workers/Custom Agents once you have a schema and governance model in place.

## Lock-in / portability

Agency owners often raise the lock-in concern: *"If we put everything into Obsidian, are we building a moat that makes it hard to leave if something better comes along?"*

The answer: Obsidian isn't the second brain. **A git repo of markdown files is the second brain.** Obsidian is just a convenient view layer over those files. The agent doesn't talk to Obsidian — it reads Markdown from a git repo, mirrored to the server via cron-pull.

This dissolves the lock-in concern:

| If "X" becomes the future brain... | What carries forward |
|---|---|
| A different Markdown editor (Logseq, Foam, Cursor, etc.) | Same files. Zero migration. |
| Notion (full migration) | Markdown API → bulk-create pages from `.md`. One-time scripted import. |
| A new RAG system (custom Vectorize, Pinecone, etc.) | Same files, indexed differently. |
| A new agent runtime | Same files, new reader. |
| Notion CLI / Workers | Same files, mirrored *into* Notion via `ntn` script. |

The substrate is portable by construction. The Obsidian UI is replaceable; the Markdown corpus is not.

### Practical wiring pattern

```text
AI engineer's laptop (Obsidian / Cursor / etc.)
       ↓  git push (manual or on-save hook)
Private git remote (GitHub or self-hosted)
       ↓  cron-pull every 5–10 min (systemd timer on VPS)
/home/agent/wiki-mirror/  ←  agent reads here via full-text search
```

The wiring is small and one-time. The agent reads a local clone, not the live Obsidian vault.

## What changed: Notion became more agent-friendly

### Notion CLI: `ntn`

Notion now documents an official CLI:

```bash
curl -fsSL https://ntn.dev | bash
```

The CLI can authenticate to a workspace, make Notion API requests, upload files, manage data sources, and create/deploy/manage Notion Workers. Coding agents and automation scripts can now interact with Notion through a first-party terminal workflow instead of bespoke curl wrappers.

Useful for: quick Notion API probes during workflow discovery, testing database schemas, uploading generated artifacts, deploying Workers, giving coding agents a more reliable Notion control surface.

### Markdown content endpoints

The most strategically important change. Notion now exposes enhanced Markdown operations:

| Operation | Endpoint / surface |
|---|---|
| Create page with Markdown | `POST /v1/pages` with a `markdown` body parameter |
| Read page as Markdown | `GET /v1/pages/:page_id/markdown` |
| Update page as Markdown | `PATCH /v1/pages/:page_id/markdown` |

Agents work naturally with Markdown. Historically, Notion forced them through a nested block-object model that was cumbersome to generate. The friction is now much lower — a transcript-processing agent can draft a meeting summary in Markdown and publish it into Notion without translating every paragraph into block JSON.

### Notion MCP

Notion MCP connects AI clients (Claude, ChatGPT, Cursor) to a Notion workspace. Supported tools include search, fetch, create pages, update pages/properties, and interact with workspace content.

Risk: Notion's docs warn that MCP tools act with the user's full Notion permissions. If an AI client is connected with broad access, it may surface NDA-sensitive material in responses. Introduce MCP with allowlists, limited scopes, and explicit review rules.

### Notion Workers

Notion Workers are hosted Node/TypeScript programs that extend Notion. Deploy with the Notion CLI, Notion hosts and runs them. They can sync external data, receive webhooks, call external APIs, use secrets/OAuth, and define tools that Notion Custom Agents call.

Candidate Workers for a web agency:

- `transcriptIngest` — receive/fetch transcript, create a Meeting record, queue extraction
- `extractMeetingActions` — convert transcript into proposed actions/decisions/risks with evidence quotes
- `createHarvestWorkplanDraft` — turn approved Notion scope/tasks into Harvest planning lines
- `lookupProjectContext` — fetch project/client metadata for a Custom Agent
- `createDevTicket` — turn an approved Notion request into a GitHub issue

### Agent tools and Custom Agents

Workers expose tools that Notion Custom Agents call — the bridge between fuzzy AI reasoning and deterministic business logic. The agent decides it needs project context; the Worker tool does the validated lookup or write.

Good tool descriptions are narrow and specific:
- `lookupProjectStatus`
- `findRecentClientDecisions`
- `createDraftHarvestLine`
- `checkDuplicateTask`

Bad tool descriptions are broad and dangerous:
- `runOperations`
- `manageProjects`
- `syncEverything`

This runtime shape suits Phase 3 internal-efficiency workflows — but only after Notion schemas and permissions are disciplined enough for tools to operate safely.

## Notion vs Obsidian: the practical comparison

### Notion wins for the team-facing brain

Strengths:
- Already adopted by most agencies
- Nontechnical teammates can use it without training
- Native databases for clients, projects, tasks, meetings, SOPs, and AI outputs
- Permissions, comments, mentions, assignments, views, templates
- Useful as an approval/review queue
- New CLI/API/MCP/Worker surfaces make automation viable

Weaknesses:
- Proprietary SaaS
- API/rate-limit/product-plan constraints
- Block/database model more complex than files
- Agent edits harder to review than Git diffs
- Workspace-wide AI access creates confidentiality risk

### Obsidian wins for the agent workshop

Strengths:
- Plain Markdown files
- Local-first and fast
- Easy for agents to read/write/search/refactor
- Git-friendly: diffs, branches, rollback, review
- No SaaS API limits
- Excellent for daily dumps, research, prompt iteration, and durable mirrors
- Lower blast radius: a bad agent edit corrupts a local draft, not a team's operational workspace

Weaknesses:
- Weak native collaboration for nontechnical teams
- No real workspace permissions model
- Databases require conventions/plugins, not first-class
- Not where the agency team already works

## The recommended architecture

### Layer 1 — Signal sources

Raw signals from the tools where work happens:
- Notion tasks/projects/pages/databases
- Granola (or equivalent) meeting transcripts
- Slack project channels
- Harvest time/budget data
- GitHub, Figma, Drive where relevant
- Client emails, gated by classification

### Layer 2 — Agent workshop: Obsidian / Markdown

The AI engineer's private working layer:
- Daily notes
- Transcript scratch analysis
- Research synthesis
- Prompt tests
- Architectural plans
- Local scripts
- Durable backup/mirror of canonical docs

The agent can be aggressive here because outputs are not automatically canonical.

### Layer 3 — Review and normalization

Before anything becomes team-facing:
- Source attached
- Evidence quote included
- Confidence scored
- Owner/due date marked as explicit vs inferred
- Confidentiality level assigned
- `AI-generated?` flag set
- Reviewer required for client-facing or operational writes

### Layer 4 — Canonical team brain: Notion

Notion becomes where the team actually works from:
- Clients database
- Projects database
- Meetings database
- Tasks / Workplans database
- Decisions database
- Risks / Blockers database
- SOPs database
- AI Outputs / Review Queue database

### Layer 5 — Team interfaces

- Notion pages/databases/views
- Slack agent bot
- Notion Custom Agents
- Automated review queues
- Occasional generated reports/briefs

Nobody should need to use Obsidian for the company brain to work.

## Proposed Notion schema

A Notion-first company brain needs schemas before content. Otherwise it becomes a nicer-looking junk drawer.

### Core databases

**Clients:** Name, Status, Confidentiality level, Primary PM, Account owner, Related projects, Last reviewed, Notes

**Projects:** Client, Status, Phase, PM, Design lead, Dev lead, Launch/milestone dates, Related meetings, Related decisions, Related risks, Related tasks, Confidentiality level, Last reviewed

**Meetings:** Title, Date/time, Client/project, Attendees, Transcript source, Summary, Extracted actions, Decisions, Risks/blockers, AI-generated?, Reviewed by, Review status

**Extracted Actions / AI Review Queue:** Title, Source meeting, Client/project, Proposed owner, Owner confidence, Due date, Due-date confidence, Evidence quote, Destination task, Status (proposed/approved/rejected/written), Rejection reason, AI-generated?, Confidentiality level

**Decisions:** Decision, Date decided, Client/project, Decider/participants, Evidence quote, Impact, Reversibility, Related tasks, Last reviewed

**Risks / Blockers:** Risk/blocker, Client/project, Severity, Owner, Status, Evidence/source, Mitigation, Last updated

**SOPs / How-To:** Area (PM/Design/Dev/Ops/AI), Owner, Last reviewed, Canonical?, Related tools, AI-safe?

## First workflow to build: transcript → task review

The cleanest flagship workflow. The Notion-first version should be review-centered, not auto-write-centered:

```text
Meeting transcript (Granola or equivalent)
  ↓
Hermes / local extractor / Notion Worker
  ↓
Meeting record in Notion
  ↓
Extracted Actions / Decisions / Risks records
  ↓
PM review queue
  ↓
Approved items become real Notion tasks/workplan items
```

Minimum viable version:
1. Fetch or upload one transcript
2. Create a Notion `Meeting` record
3. Extract actions, decisions, risks, blockers, open questions
4. For every item, include an evidence quote
5. Mark owner/due date as explicit or inferred
6. Put outputs into `AI Review Queue`
7. PM or owner reviews
8. Approved items become real tasks; rejected items teach the extraction prompt

Do **not** begin with autonomous Notion writes. Trust is the organizational value. A PM who trusts the review queue will eventually allow more automation. A PM who sees noisy auto-created tasks will turn the system off.

## Governance rules

1. **Confidentiality levels** — every source and output carries a field: Public / Internal / Client-confidential / NDA-restricted
2. **Human approval before writes** — required for: client-visible content, official tasks, canonical project status, high-sensitivity projects, triggering external coding agents
3. **Evidence by default** — every operational extraction includes source, evidence quote, timestamp, confidence, and inferred-vs-explicit marker
4. **Least-privilege integrations** — specific integration users, database/page allowlists, separate tokens per workflow, read-only where possible
5. **Auditability** — every AI-generated record identifies which tool, when, from what source, who reviewed it, approved/rejected/edited

## 30 / 60 / 90-day plan

**First 30 days:** Audit the existing Notion workspace. Identify canonical vs stale pages. Propose common metadata fields. Keep Obsidian as the AI engineer's private layer. Test CLI/API on a sandbox database only.

**60 days:** Build a sandbox `Meetings` database and `AI Review Queue`. Run transcript extraction on a small set of real meetings. Have the PM review outputs. Track rejection reasons and missing fields.

**90 days:** Move the review queue into a real project workflow. Add duplicate detection, confidence thresholds, weekly reporting. Decide whether a Notion Worker or agent-hosted service should own production.

## Decision table

| Question | Recommendation |
|---|---|
| Where should the team-facing brain live? | Notion |
| Where should the AI engineer's raw synthesis live? | Obsidian |
| Where should AI-generated drafts begin? | Obsidian or sandbox Notion review DB |
| Where should approved operational records live? | Notion |
| Should agents have broad Notion write access? | No |
| Should MCP be used? | Yes, with limited permissions and governance |
| Should Notion Workers be tested? | Yes, after schema design |
| Should the team adopt Obsidian? | No |

## Connections

- [[Second Brain Gameplan]] — the three-layer model this architecture realizes
- [[Notion Custom Agents]] — Notion-native agent runtime
- [[Knowledge Base Permissions and Classification]] — required before broad agent access
- [[Workflow-to-Agent Decision Framework]] — use before selecting the first production workflow
- [[Human-Agent Interface Patterns]] — approval gates and confidence thresholds

## Sources

- Notion CLI docs — https://developers.notion.com/cli/get-started/overview
- Notion Workers overview — https://developers.notion.com/workers/get-started/overview
- Working with Markdown content — https://developers.notion.com/guides/data-apis/working-with-markdown-content.md
- Notion MCP — https://developers.notion.com/guides/mcp/overview.md
- Notion releases — https://www.notion.com/releases

---
tags: [notion, obsidian, company-brain, ai-agents, mcp, workers, knowledge-base]
date_added:: 2026-05-16
last_updated:: 2026-06-05
