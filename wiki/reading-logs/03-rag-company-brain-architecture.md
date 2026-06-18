---
tags: [reading-log, rag, knowledge-management, company-brain, retrieval]
date_added:: 2026-06-17
last_updated:: 2026-06-17
status:: complete
---

# Reading Log: RAG Architecture & Knowledge Management for Small Agencies (2025–2026)

*What actually works at 30-person agency scale — no hype, just patterns.*

---

## 1. Where RAG Is Now

RAG has moved from experimental to production workhorse. The key driver was compounding toolchain maturity, not a single breakthrough. By mid-2025, every major vector database solved the cold-start problem. Embedding models (Voyage-3, Cohere v3, GTE-Qwen2-1.5B) produce high-quality vectors at 512-1024 dimensions without the finicky tuning of 2023-2024.

**For a 30-person agency:** The sweet spot is **small, focused retrieval corpora** (10K-500K chunks). Marginal returns drop sharply past ~200K chunks for most agency use cases. You don't need a data lake — you need a curated knowledge base.

---

## 2. Vector Search vs. Knowledge Graphs vs. Hybrid

### Pure Vector Search
Works for: Simple Q&A over unstructured text. FAQs, documentation, wiki retrieval.
Fails at: Multi-hop reasoning ("Which client projects used that design system?"), temporal queries, entity relationships.

**Verdict:** Default starting point. Good for 70% of queries. But agencies deal in relationships (client→project→asset→decision). Pure vectors miss those links.

### Knowledge Graphs
Bring explicit relationship modeling. Can answer "Which designers worked on Acme Corp and what did they decide about colors?"
**Reality check:** Building and maintaining a KG is expensive. Schema design, entity extraction, ongoing curation. Tools have gotten better (Microsoft GraphRAG, LightRAG, Neo4j GraphRAG) but still require data discipline most agencies don't have.

**Sweet spot:** Don't build a full KG from scratch. Use LLM-powered entity extraction to build it automatically. Microsoft GraphRAG showed you can extract entities and relationships without manual schema. Cost: ~$0.50-$2 per document in LLM API calls. For 500 docs, that's $250-$1,000 one-time. Worth it.

### Hybrid — What Actually Works

| Layer | Technology | What It Handles |
|-------|-----------|-----------------|
| Level 1: Full-text + vector | BM25 + embedding hybrid | Simple Q&A, keyword search |
| Level 2: Graph overlay | Auto-extracted entity graph | Multi-hop questions, relationship queries |
| Level 3: Metadata filtering | Structured fields on vector store | Client/project/date/type filtering |

**The pragmatic hybrid stack:**
1. **Primary retrieval:** BM25 + vector hybrid via Reciprocal Rank Fusion. Catches both semantic matches and exact keywords (client names, project codes, dates).
2. **Graph augmentation:** Run entity extraction quarterly. Store in lightweight graph (Neo4j AuraDB free tier, or SQLite with recursive CTEs for simpler cases).
3. **Query router:** Small classifier decides whether query needs graph traversal or pure vector retrieval. This is the secret sauce — routing correctly doubles accuracy on complex questions.

---

## 3. Vector DB Showdown

### ChromaDB
- Embedded, no server needed
- Best for: Prototyping, single-user, local-first, <100K vectors
- Degrades past 200K
- **Skip for multi-user production.**

### Qdrant ✅ **Recommended**
- Client-server (Docker or cloud), easy self-host
- Best for: 100K-10M vectors, production multi-user
- **Best-in-class payload filtering** — arbitrary JSON, nested conditions
- Free tier up to 1GB
- **For agency scale:** The free tier handles any reasonable need. Filtering replaces a graph for many use cases — tag every chunk with `client`, `project`, `date`, `doc_type`. Filter-before-retrieval gives 80% of a graph's value with 20% of the complexity.

### pgvector
- Extension on existing Postgres
- Best for: Teams already on Postgres, <500K vectors
- Full SQL power — joins, subqueries, JSONB, full-text search
- HNSW support (Postgres 17) was a game-changer
- **Use if you're already on Supabase/Postgres and vectors stay under 500K.**

| Criterion | ChromaDB | Qdrant | pgvector |
|-----------|----------|--------|----------|
| Setup complexity | Minimal | Medium | Medium |
| Multi-user | ❌ | ✅ | ✅ |
| Metadata filtering | Basic | Advanced | Full SQL |
| Self-host | N/A | ✅ (single binary) | ✅ (Postgres) |
| Free tier for 100K vectors | ✅ | ✅ | ✅ |

---

## 4. Small-Team Patterns That Actually Work

### The "Two-Pizza" RAG

For a 30-person agency, the RAG system should be owned by 1-2 people. Must survive those people being out sick.

**Flat collection + rich metadata instead of complex indexing:**
1. Chunk everything into uniform pieces (512 tokens, 128-token overlap). Every chunk gets metadata: `{source_file, client, project, date, author, doc_type, tags[], version}`.
2. Store in Qdrant with payload indexing on `client`, `project`, `date`, `doc_type`.
3. Retrieve with pre-filtering: user selects `client=AcmeCorp` in the UI, then vector search runs only over that subset. Accuracy jumps 30-50%.
4. No graph needed until concrete multi-hop failures — and when you do, add lightweight Neo4j populated by automated weekly entity extraction.

### The "Half-Day RAG" Onboarding
Every new tool needs to prove value in half a day:
1. **Hour 1:** `docker run -p 6333:6333 qdrant/qdrant`. Install `qdrant-client` + `sentence-transformers`.
2. **Hour 2:** 50-line Python script: read markdown/PDFs, chunk, insert into Qdrant with metadata.
3. **Hour 3:** Use an LLM to answer questions with retrieved context as system prompt.
4. **Hour 4:** Evaluate on 10 real questions. If accuracy > 80%, viable. If not, data quality is the problem, not the stack.

### Operational Cadence
| Task | Frequency | Who | 
|------|-----------|-----|
| Ingest new docs | Daily (cron) | Automated |
| Re-embed changed docs | On change | Automated |
| Entity extraction | Weekly | 1 person, 30 min |
| Accuracy audit | Monthly | 1 person, 2 hours |
| Re-index | Quarterly | 1 person, 1 hour |

---

## 5. Notion + Obsidian as a Dual-Brain System

This is one of the most practical patterns for a small agency:

- **Notion = The Public Brain.** Structured, shared, accessible to everyone. Client deliverables, project plans, SOPs, meeting notes.
- **Obsidian = The Private Brain.** Personal vaults. Each team member's local notes, research, drafts. Contain the richest knowledge.

### Why This Works
| Notion | Obsidian |
|--------|----------|
| Structured databases | Markdown files |
| Shared team knowledge | Personal deep knowledge |
| API-accessible | Local file access |
| Good for "what we agreed" | Good for "what I know" |

### Implementation
1. **Notion ingestion:** Pull tagged pages daily. Chunk and embed into Qdrant with `source=notion` metadata.
2. **Obsidian ingestion:** Team members opt-in. Watch for changed `.md` files. Chunk and embed with `source=obsidian:<username>`. Only ingest notes tagged `#shared/rag`.
3. **Dual retrieval:** Retrieved from both corpora. Notion context marked as "team consensus," Obsidian as "personal notes." LLM weighs accordingly.
4. **Cross-linking:** When the system finds a concept in both, suggest creating a formal Notion page from the Obsidian note.

**Verdict:** Production-ready. ~80%+ coverage of institutional knowledge from 10 people's vaults + your Notion workspace.

---

## 6. Retrieval Strategies That Matter

### Multi-Hop Retrieval
Breaking a complex question into sub-questions. **The single biggest accuracy differentiator.** "Which clients have React projects started after the Tailwind migration memo?" requires 3 retrievals: find memo's date, find React projects, filter.

**Implementation:** Single LLM call to decompose into 2-4 sub-queries, run in parallel, merge results. Adds ~500ms and ~$0.01 per query. Worth it.

### Re-Ranking ✅ **Mandatory**
After initial retrieval (top-50), a cross-encoder re-ranks to top-5 most relevant. Adds 15-25% top-1 accuracy improvement. Cost: $0.001-$0.005/query.

**Implementation:** `rank_results = cross_encoder(query, initial_results[:50])` → take top 5. That's it.

### HyDE (Hypothetical Document Embeddings)
Generate a hypothetical ideal document that would answer the query, embed that, use its vector. **Niche tool** — useful when query-document mismatch exists. Use as **fallback strategy** when initial retrieval returns low similarity scores (< 0.7).

### The Agency Retrieval Pipeline

```
Query → [1] Router → Simple → [2a] BM25 + vector search
                    → Complex → [2b] Decompose → [2a] for each sub-query
                              → Merge results
                    → Low confidence? → [2c] HyDE fallback
              → [3] Re-rank (cross-encoder on top 50)
              → [4] LLM answer
```

Total latency: 1.5-3 seconds. Cost: $0.02-$0.08/query. Accuracy improvement over pure vector: ~35% on complex, ~15% on simple.

---

## 7. Fine-Tuning vs. RAG

The binary framing is outdated. The real question is **how much do you combine them?**

### The Three-Layer Model That Works
1. **Base model** (Claude 4 / GPT-5 / DeepSeek) — don't fine-tune this.
2. **RAG context** — injected at inference. Handles all facts, client info, project history.
3. **Lightweight fine-tune** (LoRA, 100-500 examples) — handles *style* and *behavior*, not knowledge.

### What This Means for an Agency
- **Do NOT fine-tune a base LLM.** ($500-$5,000+ per run, not justified.)
- **DO fine-tune a small embedding model** if your domain has specialized vocabulary. LoRA on `gte-small`, $10-$50.
- **DO fine-tune a small re-ranker** on your domain. LoRA on cross-encoder, $20-$100.
- **DO NOT fine-tune the generation model.** Use prompting + structured output.

---

## 8. The Stack for a 30-Person Agency

| Component | Choice | Why |
|-----------|--------|-----|
| Vector DB | Qdrant (free tier → $25/mo) | Best filtering, easy ops |
| Embedding | Voyage-3 or GTE-Qwen2-1.5B | Best quality/cost |
| Re-ranker | Cohere Rerank v3 or bge-reranker-v2-m3 | ~$0.001/query or free |
| LLM | Gemini 2.5 Flash / Claude 4 Haiku | ~$0.15/M tokens |
| Ingestion | Python + cron | Dead simple, no framework |
| Chunking | Semantic chunker (200-800 tokens, variable) | Better than fixed-size |
| Metadata | `client, project, date, doc_type, tags` | Enables filtering without graph |
| UI | Slack bot or Retool/Streamlit | Meets users where they are |
| Orchestration | Simple Python (no LangChain if avoidable) | Fewer deps, easier to debug |

### What to Skip
- LangChain/LlamaIndex for production (prototype with them, replace core with direct APIs)
- Self-hosted LLMs (GPU cost > API cost at agency scale)
- Full knowledge graphs (auto-extraction + Qdrant filtering covers 90%)
- ChromaDB for anything multi-user
- Fine-tuning the base model

### Build Plan (6 Weeks, 1 Person)
**Week 1:** Set up Qdrant + ingestion script for Notion. Index 50-100 docs. Test 10 queries.
**Week 2:** Add Obsidian vault ingestion. Implement BM25+vector hybrid. Add re-ranking.
**Week 3:** Build query router. Implement multi-hop decomposition. Add HyDE fallback.
**Week 4:** Build Slack bot UI. User testing with 5-10 team members.
**Week 5:** Accuracy audit on 50 real queries. Fix failure modes.
**Week 6:** Production hardening. Write 3-page "how to add content" guide.

**Total cost:** ~$200-$400/month. **Person-hours:** ~120 hours. **Result:** 80%+ institutional knowledge coverage, <2 hours/week maintenance.

---

## Key Takeaway

Start with the data, not the architecture. A simple vector search over well-tagged documents will outperform a sophisticated graph RAG over messy data every time. Get your ingestion and metadata right first. Everything else is optimization.