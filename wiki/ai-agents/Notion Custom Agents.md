# Notion Custom Agents

> Notion's native automation primitive: agents that live inside the workspace, run on schedules or triggers, pull context from connected apps via MCP, and deliver outputs to Slack, databases, or Notion pages. The direct answer to "do we use an agent *inside* Notion?" — yes, this is what that means in practice, and it's a strong candidate for an agency's near-term internal-efficiency toolbox.

## What they are

Custom Agents are Notion-native automations with a specific shape:

- **Triggered or scheduled.** On-demand by a teammate, on a schedule, or in response to an event. Not real-time chat — async work, queued and delivered.
- **Notion-context-aware.** They read pages and databases natively without API plumbing on your end. The workspace is their default substrate.
- **App-connected via MCP.** External apps connect through MCP servers. Notion ships first-party connectors (Slack, Google Drive, GitHub, etc.) and accepts custom MCPs (which is how tools like a meeting-notes app or a time-tracking app would connect).
- **Output-flexible.** Write back to Notion (page, database row, comment), post to Slack, or emit a draft. The same agent can fan out to multiple destinations.
- **Model-agnostic.** Notion routes to whichever underlying model fits; not locked to one provider.
- **Built by anyone.** Prompt + trigger + tool config — no engineer required for most use cases.

## How a Custom Agent gets built

The Notion-suggested pattern is a five-step setup:

1. **Define the workflow** — sequence from trigger to output.
2. **Copy the starter prompt** — Notion provides a template. You swap in your context and tool references.
3. **Set the trigger** — on-demand, schedule, or event.
4. **Review the instructions** — confirm scope, source references, and what the output should look like.
5. **Configure tools and access** — grant the agent the right level of access to its sources.

The agent is then a first-class Notion object: editable, shareable, improvable by teammates. The collaborative-not-single-player framing matters — these are team primitives, not solo tools.

## Real-world scale (from Notion's own case studies)

- **Ramp** — 300+ custom agents. Their "Product Oracle" answers dozens of questions a day and is described as critical to product team operations.
- **Heidi** — getting back 60+ hours/month from custom agents handling busywork.
- **Braintrust** — competitive-intel agent posts daily updates from market sources, saves the marketing team 20 minutes a day.

Credibility-tiered numbers worth keeping in pocket for [[Luxury Brand AI ROI]]-style ROI conversations.

## Worked example shape: Support Insights Reporter

Notion's documented example is a "Support Insights Reporter" — useful as a template because the *shape* matches several common agency use cases:

- **Trigger:** Self-serve, employee runs on demand with their name.
- **Context pulled:** a support tool (via MCP) — closed conversations/tickets last 7 days for that person.
- **Synthesis:** summary stats, top themes, 3–7 ticket links with one-line context, recommendations.
- **Output:** New entry in a Notion "Summaries" database for review and sharing.

The MCP-pull → Notion-database-write pattern is the unit of work, and it generalizes.

## Where this lands for an agency

Several near-term internal-efficiency candidates map almost directly to the Custom Agent shape:

| Pain | Trigger | Source (MCP) | Synthesis | Output |
|---|---|---|---|---|
| **Manual task → time-tracking work plans** | On Notion task created/updated | Notion native | Convert task → budget line | Time-tracking entry (via MCP) |
| **Meeting transcript → tasks** | Transcript ready | Meeting-notes MCP | Extract tasks, route by project, assign owner | Notion task DB |
| **Project status briefs for client meetings** | Scheduled, day-of meeting | Notion (project hub) + Slack | Synthesize last 7 days of activity | Page in project hub or Slack message |

The transcript → tasks case is especially load-bearing — it's the canonical "where does the agent live?" question, and Custom Agents may be the runtime answer. Worth a real test on one meeting before committing.

## Caveats

- **NDA-sensitive content.** Custom Agents that read Slack or Notion broadly will hit confidential client material. Resolve a [[Knowledge Base Permissions and Classification]] model before any agent gets broad workspace read scope. Per-agent allowlists are likely the right pattern.
- **Model-agnostic ≠ uncontrolled.** Notion picks the model; you control the prompt and tools. Prompt-level guardrails ("if any page carries a confidentiality flag, exclude it from output") are the lever.
- **Model-agnostic also ≠ free.** Agents bill against Notion credits. The ROI math: `runs/month × minutes saved/run × loaded cost/minute − monthly credit cost`. Agents that aren't actually used are pure cost.
- **Async, not real-time.** Custom Agents aren't the answer to "a Slack bot with context that I can chat with during a meeting." That's a different surface.

## Open questions

- Does a given Notion plan include Custom Agents, or are they a paid add-on? Verify before committing.
- Do Custom Agents reliably handle multi-step routing logic, or degrade past simple "pull source → summarize → write back"? Ramp's 300-agent number suggests yes, but test before relying on it.
- Composability: can one Custom Agent trigger another? Matters for multi-step chains (transcript → tasks → budget line).
- Where do Custom Agent failures surface? A silently-failing agent is worse than no agent.

## Connections

- [[Workflow-to-Agent Decision Framework]] — score each candidate before building
- [[Knowledge Base Permissions and Classification]] — precondition for any agent with broad read scope
- [[Team RAG Architecture Overview]] — Custom Agents are one architectural option for the company brain; compare against custom-built alternatives
- [[Notion vs Obsidian for the Company Brain]] — where Custom Agents fit in the split-brain architecture

## Sources

- Notion's official "Getting Started with Custom Agents" walkthrough
- Notion customer stories: Ramp, Braintrust, Heidi

---
tags: [notion, ai-agents, automation, custom-agents, mcp]
date_added:: 2026-05-08
last_updated:: 2026-06-05
