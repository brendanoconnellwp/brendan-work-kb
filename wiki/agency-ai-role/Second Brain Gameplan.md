# Second Brain Gameplan

> How to connect a personal knowledge base to your agency's operational reality so it actually compounds, instead of becoming a pile of smart but disconnected notes.

## The mental model

Think of the second brain as **three concentric layers** that feed each other:

```
       ┌──────────────────────────────────────────┐
       │  STRATEGY LAYER (the wiki you have now)  │
       │  Theory, frameworks, playbooks            │
       └──────────────────────────────────────────┘
                          ▲
                          │ informs / pulls from
                          ▼
       ┌──────────────────────────────────────────┐
       │  OPERATIONAL LAYER (agency-specific)      │
       │  Snapshot of people, projects, rituals    │
       │  Decisions, retros, current state         │
       └──────────────────────────────────────────┘
                          ▲
                          │ ingested from
                          ▼
       ┌──────────────────────────────────────────┐
       │  SIGNAL LAYER (Slack, Notion, Granola)    │
       │  Raw events: messages, transcripts, tasks │
       └──────────────────────────────────────────┘
```

The strategy layer is where you build your generic knowledge — articles on AI agents, RAG architecture, workflow audits, e-commerce patterns. What most people miss is the middle: **a grounded picture of the specific agency you're inside**. That's the operational layer — an ever-fresh snapshot of who does what, what's active, what decisions have been made.

The signal layer (Slack, Notion, your meeting notes) is where new information enters daily. The job of the second brain is to:
1. **Absorb** signals selectively — not all of it, just what changes the picture
2. **Update** the operational layer when reality shifts
3. **Pull from** the strategy layer when a decision needs framing
4. **Surface** the right context when you (or the team) are about to act

This is why disconnected notes don't compound — they have no path between the abstract and the concrete. Every framework should be one click from "where does this apply at this agency?", and every agency event should be one click from "what do we already know that's relevant?"

## What "connecting it together" looks like in practice

A worked example:

> The agency owner posts in Slack: *"We need an AI agent that reads call transcripts and updates our task database."*

Without the system, that's a message that scrolls away in 3 days.

With the system:
1. **Signal captured** — drop the quote into `raw/notes/` with a date and source.
2. **Operational layer updated** — added to "Stated demand" in your operational snapshot.
3. **Strategy layer pulled** — already covered in [[Proactive Agent Workflows]] and [[n8n for Agency Ops]]. New entry needed: "Call Transcript → Notion Task Pipeline" (specific to the agency's stack: meeting notes tool → Notion → Slack).
4. **Action surfaced** — when you next sync with the owner, you walk in with a one-pager: here's the architecture, here's the API surface area, here's what to build first, here's the cost.

That's the loop. Compounding happens because next month when the PM says "I'm spending 30 min/day chasing people about time tracking," you've already got the transcript-processing pattern, the Slack bot scaffolding, and the Notion API connection — you build the time-tracking nag in half the time.

## The gameplan — 5 moves

### 1. Lock in the operational snapshot as the spine

Your operational snapshot is the single source of "current state of this agency." Update it whenever something material changes — new project starts, someone joins or leaves, a tool changes. It's the document you re-read at the start of every working session to re-orient.

**Action:** Re-read it weekly. Update it any time a Slack scan turns up something new.

### 2. Set up a recurring Slack → KB scout

Scout Slack ad-hoc at first, then make it a weekly ritual. Every Monday morning, sweep `#general`, `#project-management`, and active project channels for the past week. Capture anything that:
- Reveals a new project, client, or team member
- Surfaces a stated AI need ("I wish we could just...")
- Shows a process pain point (scope creep, billing errors, asset QA misses)
- Mentions a tool or technique worth adding to the strategy layer

Then update the operational snapshot and relevant strategy articles.

**Action:** Start manual to calibrate, then automate with a scheduled agent or `/loop` job.

### 3. Build out per-project operational files

One file per active client is what takes the operational snapshot from a list to a network. Each one captures: stakeholders, stack, current state, open questions, links to Slack threads and PM tool pages. They don't need to be long — they need to exist so context has somewhere to land when a question comes up.

**Action:** Spin up one file per active project. Even a half-filled file is better than nothing.

### 4. Identify the highest-leverage first agent build

Three candidates typically surface in a small web agency, ranked by leverage × stated demand:

| Candidate | Leverage | Owner-stated? | Complexity |
|---|---|---|---|
| **Call transcript → task agent** | High — saves PM hours, captures asks that get lost | Often explicit | Medium (meeting API + Notion API + dedup logic) |
| **Quote-vs-actuals watcher** | High — overruns are the #1 margin killer; this flags at 50% | Latent (visible in PM behavior) | Low (time-tracking API + Notion + Slack alert) |
| **Daily standup synthesizer** | Medium — turns a thread of replies into a digest for the owner/PM | Latent | Low (Slack API only) |

Recommend starting with **#2 (budget watcher)** as the proof of concept: lowest complexity, most visible win to the PM (who runs ops), demonstrates the pattern without needing a transcript pipeline first. Then graduate to #1 once trust is built.

**Action:** Validate the candidate with the owner and PM before building. Write the spec first.

### 5. Connect strategy ↔ operational with explicit wikilinks

Every strategy article should answer "where does this apply at this specific agency?" Sweep the existing articles and add an "Applied" or "At this agency" section pointing to projects or Slack moments where the theory has bite. The operational snapshot should link back to relevant strategy — keep that bidirectional discipline.

**Action:** Do this densification pass after the operational files are populated. One session.

## What success looks like in 3 months

- The operational snapshot reflects current reality, kept fresh by weekly Slack scouts.
- One project file per active client, each linked to relevant strategy articles.
- One agent shipped (budget watcher or transcript-to-task) that the team uses unprompted.
- You can answer "what's the state of the fashion client project?" in 30 seconds by reading two files instead of scrolling Slack.
- The agency owner walks into client conversations with KB-sourced context — competitor data, relevant benchmarks, past decisions — rather than reconstructing from memory.

## The thing you can't shortcut

The KB only compounds if **the strategy articles get pulled into real decisions**. That's the test. If a month from now there's a discussion about plugin architecture and nobody opens [[WordPress Architecture Decision Framework]], the KB isn't working — it's just a library nobody visits.

The practice is: every time a question comes up at the agency, ask "is this in the wiki?" *before* answering from scratch. If yes, cite it. If no, write it after.

## Connections

- [[What the Agency AI Role Actually Is]] — the role this whole effort serves
- [[First 30 Days Plan]] — the operational snapshot starts getting built in week 1
- [[Knowledge Base Permissions and Classification]] — what to ingest vs. what to keep out
- [[Team RAG Architecture Overview]] — when/if this graduates from personal KB to team-shared
- [[Proactive Agent Workflows]] — patterns the first agent build will draw from
- [[Getting Agency Teams to Actually Use AI]] — adoption side of getting these tools used
- [[Notion vs Obsidian for the Company Brain]] — the architectural decision on where the canonical team brain lives

---
tags: [gameplan, second-brain, agency-ai-role, strategy]
date_added:: 2026-04-15
last_updated:: 2026-06-05
