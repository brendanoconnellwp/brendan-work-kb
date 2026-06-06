# Cold Email Workflow (n8n)

> A pattern for AI-assisted cold-email outreach built in n8n: from a seed (a comparable past project or target industry), research a prospect, find a contact, draft a personalized email, and log it — with one hard-won lesson about *where the draft has to land* for the feedback loop to work.

## The pipeline

Triggered by a seed — typically a comparable past project or a target industry. The flow then:

1. **Generate target companies** — use the seed to find comparable companies that would plausibly want similar work
2. **Dedupe against existing clients** — don't re-outreach companies you already work with
3. **Dedupe against your CRM** — skip anyone you've already contacted
4. **Find a contact** — research a relevant person (name, role, email)
5. **Research the company** — accumulate context for a non-generic hook
6. **Write the hook + body** — generate a personalized cold email
7. **Log to CRM** — company, contact, subject line, body
8. **Create a draft** — for a human to review and send

## The quality gate (manual, for now)

A human reviews each generated email before sending. Quality is mixed, and the failure mode is predictable: when the LLM has thin research on the target, the email reverts to template energy ("Hi, I'm a fan of your work, we're an agency that worked with [big name]…") — exactly the slop that gets ignored. When it has specific signals about the prospect's positioning, the email reads human.

The difference is almost entirely **how grounded the hook is in real research about the target.** A generic name-drop opener performs worse than a specific observation about the prospect's actual business. Invest in the research step, not the prose.

## The lesson: drafts must land where open-tracking lives

The non-obvious failure mode, and the most transferable insight here. If you run outreach through a tool that shows **open-tracking** (opened y/n, when, how many times, on what device) — e.g. Superhuman — then your drafts have to land *in that tool*, not in plain Gmail.

n8n's Gmail node creates a Gmail draft, and Gmail drafts are invisible to Superhuman even though Superhuman wraps Gmail. The result: you hand-copy each generated email from your CRM into the sending tool. That copy step is friction, and friction is what kills an outreach workflow — you stop running it regularly.

**The unblocker:** Superhuman released an MCP server (early May 2026) that supports drafting and sending. A workflow can now write *directly* into the sending tool as a draft, preserving open-tracking, and the copy step disappears.

## The maturity arc

```
Manual triage (copy-paste to send)
        ↓
Direct-to-sending-tool drafts (via MCP) — friction removed
        ↓
Cron-scheduled background runs — continuous outreach
        ↓
Closed feedback loop — open-rate data tunes hooks + follow-ups
```

Each step depends on the one before it. You can't close the feedback loop until draft → send → open-track all live in one system; you shouldn't go fully autonomous until the quality is consistently good and you trust the tracking.

## Why this is a good first automation

- **It already works in manual form.** If you've run the n8n version enough to grade outputs, migration risk is low — the work is re-wiring nodes, not inventing the workflow.
- **It exercises the right primitives.** A chat/Slack trigger, multiple MCP integrations (CRM, research APIs, the email tool), a generation step with a quality bar, and output logging — the same primitives nearly every other internal workflow needs. Build it once and it seeds the pattern.
- **The feedback loop is genuinely valuable.** Once open-rate data flows back, the agent can learn which hook styles get opens and implement follow-up logic (retry unopened contacts with a different angle, drop them after N days).

## Open questions

- **Seed automation.** Today a human provides the comparable seed. This could be automated from a portfolio database — pick the most relevant past project per target industry.
- **Quality bar for autonomy.** When is AI-drafted outreach good enough to send without a human gate? Probably never fully — but the gate can move from every-email to spot-check once quality is consistent.
- **Deliverability.** Volume cold email risks domain reputation. A cron-continuous version needs warmup, throttling, and bounce handling baked in.

## Connections

- [[n8n for Agency Ops]] — the automation platform this runs on
- [[Proactive Agent Workflows]] — the cron/continuous pattern this graduates into
- [[Human-Agent Interface Patterns]] — where the review gate sits and when it can loosen

---
tags: [n8n, automation, cold-email, outreach, mcp, workflow]
date_added:: 2026-05-12
last_updated:: 2026-06-05
