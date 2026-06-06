---
title: "Cloudflare MCP Enterprise Reference Architecture"
source: "https://blog.cloudflare.com/enterprise-mcp/"
tags: [cloudflare, mcp, security, enterprise, governance, code-mode, ai-gateway]
date_added:: 2026-04-21
last_updated:: 2026-04-21
---

# Cloudflare MCP Enterprise Reference Architecture

> Cloudflare's own internal playbook for adopting MCP across product/sales/marketing/finance/eng without losing visibility, paying runaway token costs, or getting owned via supply-chain attacks. Four layers: remote MCP servers on Workers + Access for auth, MCP server portals for discovery and DLP, AI Gateway for model-layer cost control, Gateway for shadow-MCP detection. Headline numbers: 94% token reduction via Code Mode, and a complete DLP regex catalog for detecting unauthorized MCP traffic.

## Overview

As soon as MCP adoption moves past the engineering team, four problems start compounding:

1. **Supply-chain risk** — locally-installed MCP servers pull unvetted code from public repos. See the Docker "MCP horror stories" and OWASP MCP Top 10 on tool poisoning.
2. **Authorization sprawl** — every employee is choosing which MCP servers to run, which corporate resources they connect to, and how they're updated.
3. **Token cost explosion** — the "one tool per API endpoint" pattern floods the context window. 52 tools across 4 internal MCP servers = 9,400 tokens of tool definitions before any actual work.
4. **Shadow MCP** — employees using unauthorized remote MCP servers, invisible to IT.

Cloudflare's architecture addresses each directly using products they already sell — Access, AI Gateway, Gateway, WAF, Workers. The value of this the agency isn't the products; it's the **reference design and the Code Mode pattern**, both of which apply whether or not you're on Cloudflare.

## The four-layer architecture

### Layer 1 — Remote MCP servers with Cloudflare Access

Cloudflare banned locally-hosted MCP servers early. All internal MCPs are remote, deployed to Workers from a templated monorepo scaffold. Devs copy a template, write tool definitions, deploy — and inherit default-deny write controls, audit logging, CI/CD, and secrets management for free. **Governance is baked into the platform, not enforced on top of it.** That's what let adoption spread without breaking things.

Cloudflare Access sits in front as the OAuth provider — handling SSO, MFA, device posture, and contextual attributes (IP, location, device cert). Public-facing MCP servers (docs, Radar) skip auth; private ones require it.

### Layer 2 — MCP server portals for discovery and governance

Once you have dozens of remote MCP servers, discovery becomes the wall. MCP server portals are a unified endpoint: the employee connects their MCP client to the portal, and the portal surfaces every MCP server they're authorized to use. Admin gets:

- **Centralized logging** across all MCP activity
- **DLP guardrails** (e.g. block PII from reaching certain MCPs)
- **Per-group policies** — finance group sees read-only code-repo tools; engineers on corporate laptops see read/write tools
- **Consistent policy enforcement** across both Cloudflare-hosted and third-party MCP servers

Critically, all security and networking components run on the same physical machine — Access, portal, and remote MCP server traffic never leaves one box. That's the Cloudflare edge-compute advantage expressed as a security property.

### Layer 3 — Code Mode: the 94% token reduction

This is the single most important idea in the post. Traditional MCP = one tool per API endpoint. For a platform with thousands of endpoints, this is context-window suicide.

Code Mode replaces every tool with **two portal tools**: `portal_codemode_search` and `portal_codemode_execute`:

```javascript
// search: model writes JS to discover tools
async () => {
  const tools = await codemode.tools();
  return tools.filter(t => t.name.includes("jira") || t.name.includes("drive"))
              .map(t => ({ name: t.name, params: Object.keys(t.inputSchema.properties || {}) }));
}

// execute: model writes JS to chain tool calls
async () => {
  const tickets = await codemode.jira_search_jira_with_jql({ jql: 'project = BLOG AND status = "In Progress"', fields: ["summary"] });
  const doc = await codemode.google_workspace_drive_get_content({ fileId: "1aBcDeFgHiJk" });
  await codemode.jira_update_jira_ticket({ issueKey: tickets[0].key, fields: { description: tickets[0].description + "\n\n" + doc.content } });
  return { updated: tickets[0].key };
}
```

**Real numbers from Cloudflare's internal portal:** 52 tools across 4 MCP servers → 9,400 tokens of definitions → collapses to 2 portal tools consuming ~600 tokens. **94% reduction.** And the cost stays fixed as you connect more servers — it doesn't grow with tool count.

Why it works: the model discovers tools on demand (only the definitions it needs enter context), and chains calls in a single JS program (one tool invocation instead of N round-trips). Runs in a sandboxed Dynamic Worker. Pairs with the same Code Mode insight in [[Project Think]].

Activation is a URL query param: `?codemode=search_and_execute`.

### Layer 4 — AI Gateway for model-layer cost control

AI Gateway sits between the MCP client and the LLM. Two jobs: **switch between LLM providers** (prevent lock-in, route around outages — see [[Cloudflare AI Platform Unified Inference Layer]]) and **enforce per-employee token quotas** so nobody burns through $10K of context in a rogue agent loop.

## Shadow MCP detection with Cloudflare Gateway

The security flip side. How do you find employees using unauthorized remote MCP servers? Cloudflare Gateway (their secure web gateway) runs a multi-layer scan:

- **Hostname match** — `mcp.*` subdomains, known hosts like `mcp.stripe.com`
- **URI match** — `/mcp`, `/mcp/sse` paths
- **DLP body inspection via regex** — since MCP uses JSON-RPC, every request has a `"method"` field with values like `"tools/call"`, `"initialize"`, `"resources/read"`. The post publishes a ready-to-use regex catalog:

```javascript
const DLP_REGEX_PATTERNS = [
  { name: "MCP Initialize Method", regex: '"method"\\s{0,5}:\\s{0,5}"initialize"' },
  { name: "MCP Tools Call", regex: '"method"\\s{0,5}:\\s{0,5}"tools/call"' },
  { name: "MCP Tools List", regex: '"method"\\s{0,5}:\\s{0,5}"tools/list"' },
  { name: "MCP Resources Read", regex: '"method"\\s{0,5}:\\s{0,5}"resources/read"' },
  { name: "MCP Protocol Version", regex: '"protocolVersion"\\s{0,5}:\\s{0,5}"202[4-9]' },
  // + 5 more
];
```

Matching traffic can be blocked, redirected, or logged — the full DLP action set.

## Public-facing MCP: WAF + AI Security for Apps

For MCP servers your customers use (e.g. Cloudflare's own product-admin MCPs), every MCP server is an HTTP endpoint, so it goes behind the WAF. **AI Security for Apps** inspects inbound MCP traffic for prompt injection, sensitive data leakage, and topic classification. Same defensive posture as any other web API.

The related argument Cloudflare makes, worth adopting: **every organization should publish official, first-party MCP servers for their products.** The alternative is that your customers source unvetted MCPs from public repos, and your brand takes the hit when something goes wrong. Own the supply chain.

## Why this matters for agencies

**The governance pattern transfers even if the products don't.** The idea that "standing up a new governed MCP server should be minutes of scaffolding, with governance baked in" is the right North Star for any agency internal MCP deployment — Cloudflare or otherwise.

**Code Mode is the actionable takeaway today.** If the agency ever stands up internal MCP servers for Harvest / Notion / WP Engine / Slack / GitHub, do not expose tools one-per-endpoint. Use Code Mode from day one. The token savings compound directly into both cost and agent responsiveness.

**Shadow MCP detection is a 6-month-out concern**, but worth knowing the pattern exists. When an agency's AI adoption hits the point where an agency owner worries about "who's connecting what to what," the Gateway DLP regex catalog is a ready-made starting point.

**Governance parallel to [[Team RAG Access Control]].** The four-tier classification model in the RAG architecture and the per-group portal policies here solve the same problem at different layers. Design both consistently.

## Connections

- [[Project Think]] — Same Code Mode insight, applied at the in-agent tool-execution layer.
- [[Cloudflare AI Platform Unified Inference Layer]] — AI Gateway is the model-layer sibling to MCP server portals.
- [[Team RAG Access Control]] — Equivalent governance thinking for RAG; keep the two designs aligned.
- [[AI Agent Landscape 2026]] — MCP is becoming the agent-to-tool protocol; enterprise MCP is how you actually ship it.
- [[Knowledge Base Permissions and Classification]] — Same four-tier mindset applied differently.

## Open Questions

- How does Code Mode handle tools with side effects that shouldn't be chained (e.g. "send email" followed by a decision)? Is there a confirmation step, or does that belong upstream in the agent?
- Can MCP server portals front non-Cloudflare-hosted MCP servers (e.g. Stripe's hosted MCP), and does the portal still enforce DLP on that traffic?
- What's the right number of MCP server portals per org — one shared portal with group-based policies, or per-team portals? Cloudflare's post implies multiple with scoped policies.
- Are there prompt-injection guardrails in the MCP portal layer itself, or only in the WAF layer for public-facing MCP?

## Sources

- Raw the agency: `raw/Scaling MCP adoption Our reference architecture for simpler, safer and cheaper enterprise deployments of MCP.md`
- Source: https://blog.cloudflare.com/enterprise-mcp/ (2026-04-14, Agents Week)

---
tags: [cloudflare, mcp, security, enterprise, governance, code-mode, ai-gateway]
last_updated:: 2026-04-21
