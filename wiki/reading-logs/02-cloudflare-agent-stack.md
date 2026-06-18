---
tags: [reading-log, cloudflare, agents, durable-execution, production]
date_added:: 2026-06-17
last_updated:: 2026-06-17
status:: complete
---

# Reading Log: Cloudflare Agent AI Stack — Durable Execution for Agencies

*The "agents as infrastructure" thesis and what it means for production agent systems.*

---

## 1. The Thesis: Agents as Infrastructure

Cloudflare's April 2026 Agents Week articulated a fundamental shift: agents should not be ephemeral stateless RAG pipelines. They should be **first-class infrastructure primitives with durable identity.**

Every agent should have:
- **A persistent ID** — each user gets their own agent instance that maintains state across sessions
- **Transactional state management** — state survives crashes, supports exactly-once processing
- **Real-time bidirectional communication** — agents push messages, not just respond
- **Built-in retry and recovery** — failures are caught and replayed automatically

For Article building production systems for clients, this changes the question from "how do we call an LLM?" to **"how do we provision and manage stateful agent instances at scale?"**

---

## 2. Durable Objects — The Core Agent Primitive

Durable Objects (DOs) are single-writer, globally-unique actor instances. Each has:
- A unique identity (stable ID or name)
- Transactional embedded storage (`state.storage`) that persists across restarts
- Single-threaded execution (no race conditions)
- Global low-latency access

### The Agent Pattern

```typescript
export class ChatAgent extends DurableObject {
  async fetch(request: Request) {
    // Handle incoming messages
  }
  async alarm() {
    // Scheduled wake-up for proactive behavior
  }
  async webSocketMessage(ws: WebSocket, message: string) {
    // Real-time bidirectional messaging
  }
}
```

### Critical: Event Sourcing Approach

Don't just store latest state — **store the event log and reconstruct from it:**

```
state.storage.put("events", [
  { type: "message_received", timestamp, content, role },
  { type: "tool_called", timestamp, tool, input, output },
  { type: "llm_request", timestamp, request, response },
])
```

Enables debugging, audit trails, recovery, and observability.

---

## 3. Workers — The Orchestration Layer

Workers serve as stateless entry points that route requests to the correct DO agent:

```typescript
export default {
  async fetch(request, env) {
    const userId = extractUserId(request)
    const agentId = env.MY_AGENT.idFromName(userId)
    const agentStub = env.MY_AGENT.get(agentId)
    return agentStub.fetch(request)
  }
}
```

### Key Patterns for Agency Work

**Request Routing + Auth:** Worker handles auth, extracts user ID, routes to correct DO.

**Fan-Out / Fan-In:** Worker receives task → fans out to multiple specialized DO agents (research, writing, review) → collects results → returns composed response.

**WebSocket Upgrade:** Worker upgrades HTTP to WebSocket → routes to DO → full-duplex communication with user.

---

## 4. Workflows — Durable Execution for Long-Running Agents

Workflows solve the problem of multi-step agentic tasks failing mid-way:

- **Durable execution** — survive process crashes and worker restarts
- **Automatic retries** — configurable with exponential backoff
- **Step-level state tracking** — each step's output is persisted
- **Human-in-the-loop pauses** — workflows suspend and wait for approval
- **Built-in observability dashboard**

### The Agent Workflow Pattern

```typescript
export class ContentGenerationWorkflow extends Workflow {
  async run(event, step) {
    const researchResults = await step.do('research', async () => {
      return await callResearchAgent(event.payload.topic)
    })
    const draft = await step.do('generate-draft', async () => {
      return await callLLM(`Write about ${researchResults.summary}`)
    }, { retries: { limit: 3 } })
    const approved = await step.do('wait-for-approval', async () => {
      await notifyHuman(draft)
      return await sleepUntilApproval()
    })
    if (!approved) return { status: 'rejected' }
    return await step.do('publish', async () => {
      return await cms.publish(draft)
    })
  }
}
```

### DO vs. Workflows

| Aspect | Durable Object | Workflows |
|--------|---------------|-----------|
| State | Fine-grained, low-latency KV | Step-level, checkpointed |
| Real-time | Yes (WebSocket) | No (HTTP callback) |
| Duration | Long-lived (months+) | Finite (minutes-days) |
| Use case | Agent identity/state | Agent task execution |
| Communication | Bidirectional | Sequential steps |

**Practical architecture:** Use DOs for persistent identity and memory. Use Workflows for each task the agent executes. The DO spawns and monitors Workflows.

---

## 5. Queues — Async Messaging Between Agents

Cloudflare Queues provide at-least-once delivery between Workers and DOs. Use them as **mailboxes for agents:**

```
User → Worker → Queue → Research Agent DO (per topic)
                      → Writing Agent DO (per draft)
                      → Review Agent DO (per artifact)
               Result Queue → Composer Worker → User
```

### Key Features

- Batching — consume up to 100 messages at once (reduces LLM calls)
- Retry policies — dead-letter queues for failures
- Ordered delivery — per key (e.g., per user/session)
- Pull-based consumption — DOs batch-poll for work

### Pattern: Agent Mailboxes

```typescript
// Sending agent
await env.TASK_QUEUE.send({
  to: "research-agent",
  taskId: crypto.randomUUID(),
  payload: { topic: "..." },
  replyTo: "composer-queue"
})
```

---

## 6. Vectorize — Agent Memory and RAG

Vectorize serves three purposes for agents:

**1. Long-term Memory:** Store conversation embeddings for semantic recall across sessions.

**2. Knowledge Retrieval (RAG):** Embed client knowledge bases. Agent retrieves before each LLM call.

**3. Tool Selection:** Embed tool descriptions. Query to find the right tool — reduces tool-call tokens.

### Production RAG Pattern

```typescript
class MemoryAgent extends DurableObject {
  async searchMemory(query: string, limit = 5) {
    const queryEmbedding = await env.AI.run(
      '@cf/baai/bge-large-en-v1.5', { text: [query] }
    )
    const results = await env.VECTORIZE_INDEX.query(queryEmbedding.data[0], {
      topK: limit, returnMetadata: true
    })
    return results.matches
  }
}
```

---

## 7. AI Gateway — Cost Control That Changes Economics

AI Gateway is **the most important cost-control tool** for production agents:

- **Caching:** 90%+ cache hits for common queries
- **Rate limiting:** Per-user, per-model, per-endpoint
- **Fallback providers:** Auto-failover when primary is down
- **Logging:** Every LLM call logged with latency, tokens, cost
- **Prompt transformation:** Add system prompts, mask PII

### Code Mode's 94% Token Reduction

The biggest announcement from Agents Week: **Code Mode** — prompt compression that reduces token usage by 94% for structured agent tasks. Tool schemas become compact DSL instead of verbose descriptions. The LLM outputs structured code rather than reasoning.

**Impact:** A customer support agent costing $1,000/month → ~$60/month. Makes agent economics viable for high-volume, low-margin use cases.

### Cost Optimization Strategies

| Strategy | Savings | Implementation |
|----------|---------|---------------|
| AI Gateway Caching | 40-90% | Cache identical/semantic prompts |
| Code Mode | 94% tokens | Structured DSL for tool calls |
| Batch Processing | 50-70% | Batch queue messages before LLM calls |
| Context Window Management | 30-50% | Smart truncation + vector memory |
| Model Tiering | 60-80% | Cheap models for simple tasks |

---

## 8. Reference Architecture: Multi-Agent for Agencies

```
Components:
├── Entry Worker             HTTP/WebSocket entry point
├── Session Agent (DO)       Per-user agent identity
├── Memory Agent (DO)        Shared memory and RAG
├── Task Workflow            Durable execution of complex tasks
├── AI Gateway               Cost control and observability
├── Vectorize                Semantic memory index
├── Queues                   Inter-agent messaging
└── MCP Servers              Tool access layer
```

### Scaling: Multi-Agent Teams

```
Orchestrator Agent (DO)
├── Research Agent (DO)     searches knowledge base
├── Writing Agent (DO)      generates content
├── Review Agent (DO)       validates output
└── Escalation Agent         hands off to human
```

Each sub-agent is its own DO. Orchestration via:
1. Direct DO-to-DO calls (`env.RESEARCH_AGENT.get(id).fetch(request)`)
2. Queues — for async, fire-and-forget tasks
3. Workflows — for multi-step processes with human-in-the-loop

---

## 9. What to Build First at Article

1. **Customer Support Agent** — highest ROI for agency clients. Session Agent (DO) per user, knowledge base RAG via Vectorize, AI Gateway, human escalation via Workflows.

2. **Content Production Pipeline** — Research → Draft → Review → Publish (Workflow). Per-brand DO agents with brand voice memory. MCP servers for CMS, DAM, approval tools.

3. **Lead Qualification Bot** — Per-lead DO agent. CRM MCP integration. Scheduled follow-ups via DO alarm().

---

## Key Resources

- [Cloudflare Durable Objects](https://developers.cloudflare.com/durable-objects/)
- [Cloudflare Workflows](https://developers.cloudflare.com/workflows/)
- [Cloudflare Vectorize](https://developers.cloudflare.com/vectorize/)
- [Cloudflare AI Gateway](https://developers.cloudflare.com/ai-gateway/)
- [MCP Specification](https://modelcontextprotocol.io/)