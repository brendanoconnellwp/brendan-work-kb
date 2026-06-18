---
tags: [reading-log, ai-agents, mcp, agent-patterns, production]
date_added:: 2026-06-17
last_updated:: 2026-06-17
status:: complete
---

# Reading Log: MCP, ACP, and Agent-to-Agent Communication Patterns (Mid-2026)

*A practical guide for production agent systems at agency scale.*

---

## 1. What Is MCP?

The **Model Context Protocol (MCP)** is an open standard (originally from Anthropic, November 2024) that defines how LLMs discover, authenticate to, and interact with external tools and data sources. Think of it as **USB-C for AI agents** — a single protocol that replaces the chaos of custom tool-calling integrations.

### Core Architecture

```
LLM Host (Claude, Cursor, etc.) ←→ MCP Client (SDK) ←→ MCP Server (Notion, GitHub, Figma, etc.)
```

**Key concepts:**
- **Resources** — Data the server exposes. Read-only by default. URI-addressable (`notion://page/abc123`).
- **Tools** — Executable functions the LLM can call. Each has a JSON Schema for parameters.
- **Prompts** — Pre-written templates the server offers to the host.
- **Transports** — Stdio (local dev), SSE/Streamable HTTP (production standard as of 2025-03-26 spec).

### The MCP Spec Evolution

| Version | Key Addition |
|---------|-------------|
| 2024-11-05 (initial) | Basic tools, resources, stdio transport |
| 2025-03-26 | Streamable HTTP transport, prompts, roots, sampling, notifications |
| 2026 (current) | Batch tool calls, streaming results, OAuth 2.0 device flow, SDK v1.8+ |

---

## 2. MCP vs. ACP — The Critical Distinction

This is where most people get confused. **They serve different layers of the stack.**

| | MCP | ACP |
|---|---|---|
| **Purpose** | LLM ↔ external tools/data | Agent ↔ Agent |
| **Origin** | Anthropic (Nov 2024) | Google (Dec 2024), multi-vendor |
| **Use case** | Giving an LLM access to APIs, files, databases | Enabling agents to delegate, share context, negotiate |
| **Scope** | Single agent + its tool ecosystem | Multi-agent systems, swarms, hierarchies |
| **Adoption** | Widespread — 500+ servers, every major AI IDE | Early — reference implementations |
| **Transport** | Stdio, SSE, Streamable HTTP | gRPC, WebRTC, HTTP/2 |

**For agencies:** MCP is table stakes. ACP is frontier work. If you don't speak MCP in 2026, you're wasting engineering time. ACP matters when you have 4+ cooperating agents.

---

## 3. The MCP Server Ecosystem (What Agencies Actually Use)

### Notion MCP Server
- **Tools:** Create/update/retrieve pages, query databases, search, append blocks
- **Production pattern:** Agent builds content briefs → searches existing notes → creates page → appends sections
- **Gotcha:** 3 req/s rate limit. Date formatting a common pitfall. Use queues for batch ops.

### Figma MCP Server
- **Tools:** Get file data (nodes, frames, components), export images, search nodes, get styles
- **Production pattern:** Agent reads design tokens → generates Tailwind CSS → creates GitHub issue
- **Gotcha:** Large files = 10MB+ JSON. Use depth/node_id filters aggressively. Community server quality varies.

### GitHub MCP Server
- **Tools:** Create/list issues, create/merge PRs, search code/files, manage actions
- **Production pattern:** PR triage → review diff → post comments → auto-approve trivial ones
- **Gotcha:** Use fine-grained PATs. Merge conflicts aren't handled — agent should detect and flag.

### Cloudflare MCP Server
- **Tools:** Deploy Workers, KV operations, D1 queries, DNS updates, AI model inference
- **Production pattern:** Agent builds and deploys serverless API in one conversation
- **Why it matters:** Collapses entire deploy pipeline into agent-accessible tools.

### Other Notable Servers
Slack, Linear, Postgres/SQLite, Filesystem, Puppeteer, Sentry, ClickUp, Jira, Supabase, Stripe, Airtable, Google Drive.

---

## 4. Production Patterns for Agency Agentic Workflows

### Pattern 1: Orchestrator with MCP Toolbelt
A single powerful LLM with 5-10 MCP servers. Simple, fast for 1-2 hop workflows. **Limitation:** Context bloat from tool definitions.

### Pattern 2: Router + Specialist Agents ✅ **Recommended for Article**
A lightweight router classifies intent → hands to specialist agents with purpose-scoped MCP servers.

```
Router Agent → Design Agent (Figma MCP only)
            → Dev Agent (GitHub + CF MCP only)
            → Content Agent (Notion + Google Docs MCP only)
            → QA Agent (Sentry + Playwright MCP only)
```

Eliminates context bloat. Each agent stays fast.

### Pattern 3: Human-in-the-Loop Gate
All destructive MCP tool calls require approval. Implement as a **gating layer** between LLM and MCP server. Sends Slack message, waits for confirmation, then forwards.

### Pattern 4: Event-Driven Pipeline
MCP servers push notifications on resource changes → agent wakes → calls tools. **Bleeding edge** — only ~30% of servers support push. Use webhooks as adapter.

---

## 5. The Mid-2026 Landscape

### Adoption
- 500+ public MCP servers (up from ~100 in March 2025)
- Every major LLM provider supports MCP client natively
- ~40% of AI-powered agencies use MCP in production
- Official servers from: Notion, GitHub, Cloudflare, Slack, Linear, Supabase, Stripe, Airtable, Google Drive

### What Changed Since 2025
1. Streamable HTTP is standard transport
2. OAuth 2.0 device flow for remote server auth
3. Rate limiting and retry baked into SDK
4. Tool batching (multiple tools in one round-trip)
5. MCP registries emerging (Smithery, Truffle — think "npm for MCP servers")
6. Self-hosting common at agencies (wrap internal APIs)

### Lingering Problems
| Problem | Severity | Workaround |
|---------|----------|-----------|
| Context bloat from tool definitions | High | Router pattern; only connect relevant servers |
| No built-in RBAC | Medium | Implement auth at server level |
| Inconsistent server quality | High | Vet servers; wrap community ones |
| Notification support spotty | Medium | Webhooks → MCP client adapter |
| No standard logging/tracing | Medium | Wrap with OpenTelemetry |

---

## 6. Recommendations for Article

### Build Now (Q3 2026)
1. **Standardize on MCP** for all tool integrations. Internal APIs (Harvest, ClickUp, custom tools) get MCP wrappers.
2. **Router + Specialists** as your architecture. Avoid monolithic orchestrator.
3. **Human-in-the-loop gate** for all writes. Client data is sacred.
4. **Adopt Streamable HTTP** transport for resilience.
5. **Build one differentiating MCP server** — design system MCP (auto-generate components from tokens) or client reporting MCP.

### Experiment With
- ACP for multi-agent workflows (when 4+ agents)
- MCP registries for server discovery
- Local-first MCP for client confidentiality

### Skip
- Custom function-calling wrappers (MCP makes these obsolete)
- Building your own agent framework (LangChain, CrewAI, Vellum support MCP natively)
- ACP for simple 2-agent workflows (use MCP relay instead)

---

## 7. Code: Spin Up a Local MCP Server

```python
# server.py — Custom agency time-tracking MCP server
from mcp.server import Server, NotificationOptions
from mcp.server.models import InitializationOptions
import mcp.server.stdio
import mcp.types as types
import httpx

server = Server("article-time-tracking")

@server.list_tools()
async def handle_list_tools() -> list[types.Tool]:
    return [
        types.Tool(
            name="get_client_hours",
            description="Get billable hours for a client this month",
            inputSchema={
                "type": "object",
                "properties": {
                    "client_id": {"type": "string"},
                    "month": {"type": "string", "pattern": "\\d{4}-\\d{2}"}
                },
                "required": ["client_id"]
            }
        )
    ]

@server.call_tool()
async def handle_call_tool(name: str, arguments: dict) -> list[types.TextContent]:
    if name == "get_client_hours":
        async with httpx.AsyncClient() as client:
            resp = await client.get("https://api.article.internal/tracking", params=arguments)
            return [types.TextContent(type="text", text=resp.text)]

async def main():
    async with mcp.server.stdio.stdio_server() as (read, write):
        await server.run(read, write, InitializationOptions(
            server_name="article-time-tracking",
            server_version="0.1.0",
            capabilities=server.get_capabilities(
                notification_options=NotificationOptions(),
                experimental_capabilities={}
            )
        ))

if __name__ == "__main__":
    import asyncio
    asyncio.run(main())
```

### MCP Config (Claude Desktop / Cursor)

```json
{
  "mcpServers": {
    "notion": {
      "command": "npx",
      "args": ["@notionhq/notion-mcp-server"],
      "env": { "NOTION_API_KEY": "${NOTION_TOKEN}" }
    },
    "github": {
      "command": "npx",
      "args": ["@modelcontextprotocol/server-github"],
      "env": { "GITHUB_TOKEN": "${GITHUB_PAT}" }
    },
    "cloudflare": {
      "command": "npx",
      "args": ["cloudflare-mcp-server"],
      "env": { "CLOUDFLARE_API_TOKEN": "${CF_TOKEN}", "CLOUDFLARE_ACCOUNT_ID": "${CF_ACCOUNT}" }
    }
  }
}
```

---

## Key Takeaway

The agencies winning with AI in 2026 aren't the ones with the smartest prompts — they're the ones with the best-integrated tool ecosystems. MCP is how you build that.