---
aliases: [Team RAG Ingestion Pipelines]
---
# Team RAG Ingestion Pipelines

> The knowledge base is only as good as what's in it. For a distributed team, content lives everywhere — and getting it into the RAG is usually harder than building the RAG itself.

## Overview

A distributed team of 10 across dev, design, PM, product, and ops produces content in many places simultaneously: Google Drive, Notion, Slack, Figma, GitHub, email, meeting transcripts, project management tools. The ingestion pipeline is how this scattered content becomes a unified retrievable knowledge base.

This is where [[n8n for Agency Ops]] becomes the backbone — it's the orchestration layer that connects everything.

## The Four Workflows Every Team RAG Needs

### 1. Source Connection
Establishing authenticated, reliable connections to every content source. For a 10-person agency:

| Source | Priority | Ingestion Method |
|---|---|---|
| Google Drive (docs, sheets, slides) | Critical | Drive API, scheduled sync |
| Notion (wikis, databases) | Critical | Notion API or export → sync |
| Slack (key channels only) | High | Slack API, selective channels |
| Figma (design files, comments) | Medium | Figma API for metadata, not raw files |
| GitHub (code, PRs, issues) | High | GitHub API, repo webhooks |
| Meeting transcripts | Critical | Fireflies/Otter/Grain → webhook → RAG |
| Project management (Linear/Asana) | Medium | API sync |
| Email (shared inboxes) | Low | IMAP or forwarding rules |

### 2. Ingestion & Preprocessing
Converting raw content into RAG-ready chunks. Steps:

1. **Fetch** — pull the raw content
2. **Normalize** — convert to consistent markdown/plain text
3. **Enrich** — add metadata: source, author, date, classification tier, tags
4. **Chunk** — split into retrievable pieces (typically 300-800 tokens with overlap)
5. **Embed** — generate vectors via embedding model
6. **Store** — insert into vector DB with metadata intact

Chunking strategy matters more than most people realize. Bad chunking = bad retrieval no matter how good your model is.

**Chunking rules for agency content:**
- **Docs/articles:** Split by heading hierarchy, respect section boundaries
- **Transcripts:** Split by speaker turns + time windows
- **Code:** Split by function/class, include file context
- **Slack:** Thread-based chunking — one thread = one chunk
- **Meeting notes:** Split by topic, include attendees/date metadata

### 3. Scheduled & Event-Driven Sync
Content changes constantly. Ingestion must stay current:

**Scheduled sync (daily/hourly):**
- Google Drive folder scans for new/updated files
- Notion page sync
- Project management snapshots

**Event-driven sync (real-time):**
- Slack messages (webhook → classify → ingest if relevant)
- GitHub PR merged → re-index affected docs
- New meeting transcript → auto-process and file
- Figma file published → metadata update

n8n handles both patterns natively. Scheduled triggers for polls, webhook triggers for events.

### 4. Classification & Access Tagging
Before content lands in the vector store, classify it for the [[Team RAG Access Control]] system:

```
New document arrives
  → Claude processes it:
     - Classify tier (public/internal/confidential/restricted)
     - Extract entities (projects, clients, people)
     - Generate summary
     - Tag with relevant topics
  → Metadata + content → vector DB
  → Notification: "New content indexed, flagged as [tier]"
```

This is where the transcript pipeline from [[Knowledge Base Permissions and Classification]] plugs in — same classification logic, just feeding a real RAG instead of folders.

## The n8n Ingestion Workflow (Template)

```
Trigger: [Drive upload / Slack message / Meeting ended / Schedule]
  ↓
Fetch content (API call)
  ↓
Normalize to markdown (text extraction, format cleanup)
  ↓
Claude classification node:
  - What tier? (public/internal/confidential/restricted)
  - What topics?
  - What entities?
  - Any sensitive content flags?
  ↓
Split into chunks (based on content type)
  ↓
Generate embeddings (OpenAI API call)
  ↓
Insert to vector DB with metadata:
  - source_url, source_type, author, date
  - classification_tier
  - allowed_roles, allowed_users
  - topics, entities
  - chunk_index, chunk_total
  ↓
Notify Slack channel: "📥 Indexed: [title] (tier: internal)"
  ↓
Log to audit database
```

## Common Ingestion Pitfalls

### 1. Ingesting Too Much
The temptation is to vacuum everything. Resist this.
- Slack has 90% noise (reactions, "thanks", "lol"), 10% signal
- Email often contains personal/confidential mixed with work
- Old docs may contradict current practices

**Fix:** Be selective. Whitelist specific Slack channels, Drive folders, Notion sections. Ingest with purpose.

### 2. Losing Context in Chunking
A chunk that says "yes, I think that's the right call" is useless without the question it answers.

**Fix:** Include surrounding context in each chunk. Thread-aware chunking for conversations. Include parent section for structured docs.

### 3. Stale Data Drift
Content changes but the RAG index doesn't. Users get outdated answers with confidence.

**Fix:** Event-driven re-indexing on source updates. Periodic full refresh (weekly). Timestamp every chunk.

### 4. Broken Classification
Auto-classification isn't perfect. Sensitive content leaks into public tier.

**Fix:** Conservative classification — when in doubt, flag as higher tier. Human review queue for edge cases. Regular audits of classification accuracy.

### 5. Ingestion Debt
Teams get the RAG working, fill it with initial content, then the ingestion pipeline breaks silently. Six months later, nobody trusts it because it's out of date.

**Fix:** Monitoring and alerts on ingestion failures. Daily health check dashboard. Ownership assigned — someone is responsible.

## Minimum Viable Ingestion for Week 1

Don't try to connect everything at once. Start with three sources that will deliver 80% of value:

1. **Google Drive** (or Notion, whichever is primary) — all docs, synced daily
2. **Meeting transcripts** — auto-processed from Fireflies/Otter/Grain
3. **Manual upload** — a simple web form for anything else

Add Slack, Figma, GitHub, and others in weeks 2-6 as you validate the core system.

## Connections

- [[Team RAG Architecture Overview]] — ingestion is one of the five core concerns
- [[Team RAG Tooling Comparison]] — vector DB + embeddings consume what ingestion produces
- [[Team RAG Access Control]] — ingestion is where classification tagging happens
- [[n8n for Agency Ops]] — the orchestration layer for all ingestion workflows
- [[Knowledge Base Permissions and Classification]] — the tiering logic applied during ingestion
- [[What the Agency AI Role Actually Is]] — designing ingestion is core to the role

## Open Questions

- How do you handle content that exists in multiple places (e.g., a doc in Drive and Notion)? De-dupe how?
- What's the right chunk overlap — 20%? 50%? Depends on content type?
- How do you handle multi-language content in international agencies?
- When should ingestion ignore content entirely (ephemeral Slack banter, draft docs)?

## Sources

- [Vertex AI RAG Engine Data Ingestion — Google Cloud](https://docs.cloud.google.com/vertex-ai/generative-ai/docs/rag-engine/use-data-ingestion)
- [Ragie: Context Engine for Agents](https://www.ragie.ai/)
- [Unified API for Enterprise Search](https://unified.to/blog/how_to_build_enterprise_search_across_google_drive_slack_notion_zendesk_and_other_platforms_with_a_unified_api)
- [Four Essential RAG Chatbot Workflows — AgentWiki](https://agentwiki.org/rag_chatbot_workflows)

---
tags: [rag, ingestion, pipelines, n8n, automation, team-knowledge]
date_added:: 2026-04-04
last_updated:: 2026-04-04
