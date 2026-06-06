---
aliases: [Team RAG Architecture Overview]
---
# Team RAG Architecture Overview

> A solo Obsidian wiki is a personal second brain. A team RAG is a shared brain. The architecture, tradeoffs, and decisions are fundamentally different.

## Overview

When you move from a solo knowledge system to a team RAG for ~10 distributed people across dev, design, PM, product, and ops roles, the problem shifts from "organize knowledge well" to "retrieve the right knowledge for the right person at the right time, without leaking." Five new concerns dominate:

1. **Ingestion** — content comes from everywhere (Google Drive, Slack, Notion, Figma, GitHub, transcripts, emails)
2. **Retrieval quality** — semantic search has to actually surface what people need
3. **Access control** — not everyone should see everything (see [[Knowledge Base Permissions and Classification]])
4. **Multi-user experience** — Slack bot? Web UI? IDE integration? All of the above?
5. **Infrastructure** — self-hosted vs managed, cost, operational burden

This article maps the decision space. Deeper dives in [[Team RAG Tooling Comparison]], [[Team RAG Access Control]], and [[Team RAG Ingestion Pipelines]].

## The Three Architectural Paths

### Path A: Managed SaaS (Fastest)
Use a fully managed RAG platform. Examples: Claude Projects (Team/Enterprise), Ragie, StackAI, Dify Cloud.

**How it works:** Connect your data sources, define teams/permissions in the platform, use their UI or API.

**Pros:**
- Live in days, not months
- Zero infrastructure management
- Built-in integrations for Google Drive, Slack, Notion
- Managed upgrades and improvements

**Cons:**
- Data sits on someone else's servers (check compliance needs)
- Monthly subscription costs ($20-200/user typical)
- Limited customization
- Vendor lock-in

**Best for:** A 10-person distributed team that needs this working next month, not next quarter. Start here unless you have strong reasons not to.

### Path B: Open-Source Framework + Self-Hosted (Balanced)
Build on an open-source RAG framework, self-host on your own infrastructure. Examples: LlamaIndex + Qdrant, LangChain + ChromaDB, RAGFlow standalone.

**How it works:** Developer sets up the stack, writes ingestion pipelines, integrates with team tools, deploys to your infrastructure.

**Pros:**
- Full data control — nothing leaves your servers
- Customizable retrieval and permissions
- One-time setup cost, lower ongoing cost
- Can integrate deeply with custom tools

**Cons:**
- Requires technical expertise (1 engineer, 2-4 weeks to MVP)
- You own operations, updates, monitoring
- Harder to get non-technical users onboarded

**Best for:** Teams with at least one engineer comfortable with Python and infrastructure. Agencies handling sensitive client data.

### Path C: Hybrid — Claude Projects as the Primary, Custom Where Needed
Use Claude Projects (Team plan) as the daily-use RAG for 90% of cases. Build custom RAG pipelines only for the specific sensitive/specialized use cases that need more control.

**How it works:** Claude Projects handles general team knowledge. A lightweight custom RAG handles transcripts, pricing, or other sensitive content tiers.

**Pros:**
- Fastest to value for the bulk of team use
- Custom only where it matters
- Natural fit for teams already using Claude
- Respects the [[Knowledge Base Permissions and Classification]] tiers — public/internal stuff in Claude Projects, confidential/restricted stuff in custom

**Cons:**
- Two systems to think about
- Unified search across both is harder

**Best for:** Teams already using Claude who want to move fast but have some tiered-access requirements.

## The Honest Recommendation for a 10-Person Team

**Start with Path C (Hybrid).** Claude Projects Team plan for 90% of knowledge, custom n8n + ChromaDB pipeline for transcripts and sensitive content. Here's why:

1. **Speed to value** — Claude Projects works today. Custom RAG takes weeks.
2. **Team adoption** — people already use Claude. Adoption friction is near-zero.
3. **Cost** — Team plan is ~$30/user/month vs. thousands for custom infrastructure + engineering time.
4. **Incremental ownership** — you can grow the custom piece as you identify specific gaps.
5. **Aligned with your role** — [[What the Agency AI Role Actually Is]] emphasizes quick wins + deep integration. Path C gives you both.

If after 3 months Claude Projects hits real limits (retrieval quality on specific domains, access control granularity, integration gaps), graduate to Path B for those specific needs.

## The Five Concerns Mapped to Paths

| Concern | Path A (Managed) | Path B (Self-Hosted) | Path C (Hybrid) |
|---|---|---|---|
| Ingestion | Built-in connectors | Custom pipelines | Claude handles most; custom for rest |
| Retrieval quality | Vendor-optimized | You tune | Claude for general, custom for specialized |
| Access control | Vendor features | You build | Two-tier — Claude for open, custom for sensitive |
| Multi-user UX | Polished web UI | You build | Claude UI + Slack/CLI for custom |
| Infrastructure | None | Full ownership | Minimal (just the custom piece) |

## Connections

- [[Team RAG Tooling Comparison]] — specific frameworks and vector databases
- [[Team RAG Access Control]] — RBAC, ABAC, ReBAC for multi-user RAG
- [[Team RAG Ingestion Pipelines]] — getting data from Slack, Drive, Notion, etc.
- [[Knowledge Base Permissions and Classification]] — the tiered access model applies here
- [[Cloudflare MCP Enterprise Reference Architecture]] — parallel governance design for agent-to-tool access; same "bake controls into the platform template" philosophy. Also worth considering Cloudflare Workers AI + Vectorize + D1 as a fourth stack option alongside the three paths here.
- [[What the Agency AI Role Actually Is]] — team RAG is a core deliverable of the role
- [[n8n for Agency Ops]] — the automation layer for ingestion and orchestration

## Open Questions

- At what team size does Path A break down? 25? 50? 100 users?
- How do you measure RAG quality — what's the minimum acceptable retrieval precision for team use?
- What's the right evaluation loop — who judges if answers are good enough?
- How do you handle stale content — auto-reindex? Manual refresh? Event-driven?

## Sources

- [Enterprise RAG Guide 2026 — Stack AI](https://www.stackai.com/blog/enterprise-rag-what-it-is-and-how-to-use-this-technology)
- [RAG Knowledge Base 2026 — Docsie](https://www.docsie.io/blog/articles/retrieval-augmented-generation-knowledge-base-2026/)
- [RAG in 2026: Real-World Implementations — Apex Logic](https://www.apex-logic.net/news/rag-in-2026-real-world-implementations-and-best-practices-beyond-the-hype)
- [Claude Projects RAG Feature](https://support.claude.com/en/articles/11473015-retrieval-augmented-generation-rag-for-projects)

---
tags: [rag, team-knowledge, architecture, agency, decision-framework]
date_added:: 2026-04-21
last_updated:: 2026-04-04
