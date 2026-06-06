---
aliases: [Team RAG Access Control]
---
o# Team RAG Access Control

> RAG's greatest strength is finding semantically related information across vast document stores. When sensitive data is in the mix, that strength becomes a vulnerability. Here's how to lock it down properly.

## Overview

A team RAG for a ~10-person distributed agency touches pricing discussions, client strategy, salary information, HR matters, and internal creative work. You can't just throw everything into a vector database and trust role-based UI restrictions — the LLM will happily retrieve across permission boundaries if the retrieval layer doesn't enforce them.

This is the natural extension of [[Knowledge Base Permissions and Classification]] from solo/tagged files into a system where retrieval itself must respect permissions.

## The Core Problem

RAG systems excel at semantic search. When you ask "what's our pricing strategy for enterprise deals?", the vector database finds semantically similar chunks across all indexed content. Without access control at the retrieval layer:

- A junior dev's query surfaces confidential pricing
- An ops person sees HR salary discussions
- A contractor sees internal client strategy

**The isolation paradox:** aggressive partitioning (separate indexes per tier) breaks semantic value across tiers. Permissive access creates security risks. The solution is in-retrieval authorization.

## Three Authorization Models

### 1. RBAC (Role-Based Access Control)
Users have roles (admin, manager, member, contractor). Roles have permissions. Simplest model.

**Good for:** Clear role hierarchy, static permissions, small teams.
**Limitation:** Can't express "the creator of this doc can always edit it" or "managers see their team's docs."

### 2. ReBAC (Relationship-Based Access Control)
Permissions derived from relationships: "Alice created this doc → Alice is editor." "Bob manages the Design team → Bob reads all Design team docs."

**Good for:** Dynamic team structures, ownership-based access, cross-cutting relationships.
**Limitation:** More complex to implement and reason about.

### 3. ABAC (Attribute-Based Access Control)
Rules based on resource properties: "documents tagged `public` are visible to everyone," "documents with `classification: restricted` are visible only to owner."

**Good for:** Content classification, regulatory rules, property-driven policies.
**Limitation:** Can get complex with many attributes.

**The best approach is combining all three.** RBAC for the base layer, ReBAC for ownership and team relationships, ABAC for classification-based rules.

## Two Naive Approaches (Don't Do These)

### ❌ Post-Search Filtering
Retrieve top N results, iterate in application code, remove unauthorized documents.

**Why it fails:**
- Slow (network calls per document)
- Can return fewer than requested results if many are filtered out
- Scales poorly with many documents
- Auditing becomes a nightmare

### ❌ Separate Indexes Per Tier
Build one vector database per access tier. Query only the appropriate index for each user.

**Why it fails (for most teams):**
- Breaks semantic search across tiers (a public query can't benefit from internal knowledge even when it should)
- Multiplies infrastructure complexity
- Doesn't handle relationships ("user can access their own confidential docs but not others")

**Exception:** Separate indexes are appropriate if your tiers are genuinely disjoint (e.g., client-specific data that must never leak across clients). In that case, a separate index per client is the right call.

## The Right Approach: In-Retrieval Authorization

Push authorization logic into the retrieval query itself. The vector search and permission check happen in a single optimized operation.

### Implementation Patterns

**Pattern A: pgvector + Row-Level Security (Simplest)**
```sql
-- Vector search with built-in RLS
SELECT * FROM documents
WHERE user_can_access(current_user, document_id)
ORDER BY embedding <-> query_embedding
LIMIT 10;
```
If you already use PostgreSQL, this is the lowest-friction path. RLS handles auth, pgvector handles search.

**Pattern B: Qdrant + Metadata Filters**
Qdrant applies metadata filters *before* vector search. Tag each document with permission metadata on ingest:
```python
{
  "classification": "confidential",
  "visible_to_roles": ["leadership", "client_lead"],
  "owner_id": "user_123",
  "department": "design"
}
```
Query with filters that match the current user's context.

**Pattern C: Authorization Service (Most Flexible)**
Use a dedicated auth service like Oso Cloud, Cerbos, or OpenFGA. Your retrieval query calls the auth service to filter during or after vector search. Best for complex rules involving multiple relationships.

## Practical Recommendation for a 10-Person Agency

For your team size, the right approach is layered:

### Layer 1: Claude Projects (Path A/C)
Use Claude Projects Team plan for content that's safe for everyone on the team. This handles the "internal but not sensitive" tier.

### Layer 2: Custom RAG for Sensitive Content
Build a separate, smaller RAG system for confidential/restricted content using:
- **Vector DB:** pgvector (if you have PostgreSQL) or Qdrant
- **Auth model:** RBAC + ABAC hybrid
  - RBAC roles: admin, leadership, team-member, contractor
  - ABAC tags: public, internal, confidential, restricted
- **Access rules:** Hardcoded in the retrieval query, not in app code

### Layer 3: Audit Logging
Every query logs: who asked, what they asked, what was retrieved, what was returned. This is non-negotiable for a sensitive-data RAG.

## Mapping to the Four-Tier Classification

From [[Knowledge Base Permissions and Classification]]:

| Tier | RAG Handling |
|---|---|
| **Public** | Claude Projects (no auth needed) or public index |
| **Internal** | Claude Projects (Team plan scopes to team) |
| **Confidential** | Custom RAG with RBAC filter on role + ABAC on classification |
| **Restricted** | Separate index, single-user access, heavy audit logging |

## The Onboarding/Offboarding Problem

With 10 distributed people, staff turnover is real. Your RAG access control must handle:
- **New hires** — automatic role assignment, immediate access to appropriate content
- **Role changes** — promotion from team-member to leadership triggers new access
- **Departures** — immediate revocation of all access, audit trail of what they accessed before leaving

This is why authorization services like Oso or Cerbos are worth it even for small teams — they give you the audit trail and instant revocation that DIY solutions miss.

## Connections

- [[Team RAG Architecture Overview]] — access control is one of the five core concerns
- [[Team RAG Tooling Comparison]] — the vector DB choice affects auth implementation
- [[Knowledge Base Permissions and Classification]] — the tiered model this builds on
- [[Cloudflare MCP Enterprise Reference Architecture]] — same governance patterns applied at the agent-to-tool layer: per-group policies, DLP, central logging, identity via Access/SSO. Worth reading alongside to keep RAG and MCP governance designs consistent.
- [[What the Agency AI Role Actually Is]] — designing this is a core deliverable
- [[Getting Agency Teams to Actually Use AI]] — people won't contribute if they don't trust the controls

## Open Questions

- How do you handle "soft" permissions — content a manager *could* see but shouldn't by default?
- What's the right audit retention period for RAG query logs?
- How do you test access control without exposing real sensitive data to testers?
- When (if ever) should the LLM be allowed to "know" content exists that the user can't access?

## Sources

- [The Right Approach to Authorization in RAG — Oso](https://www.osohq.com/post/right-approach-to-authorization-in-rag)
- [RAG with Access Control — Pinecone](https://www.pinecone.io/learn/rag-access-control/)
- [RAG with Permissions — Supabase](https://supabase.com/docs/guides/ai/rag-with-permissions)
- [Secure RAG with Cerbos](https://www.cerbos.dev/blog/access-control-for-rag-llms)
- [Building a Permissions System for RAG — Paragon](https://www.useparagon.com/learn/ai-knowledge-chatbot-with-permissions-chapter-2/)

---
tags: [rag, access-control, security, multi-user, authorization]
date_added:: 2026-04-21
last_updated:: 2026-04-04
