---
aliases: [Value Stream Mapping for Creative Work]
---
# Value Stream Mapping for Creative Work

> The same technique Toyota used to eliminate waste on assembly lines works surprisingly well for figuring out why a landing page takes 3 weeks when it should take 3 days.

## Overview

Value Stream Mapping (VSM) is a visual tool that maps every step in a process, capturing both **active work time** (when someone is actually doing something) and **wait time** (when work is sitting in a queue). The gap between these two numbers is where your waste lives.

In creative/agency work, VSM was slow to catch on because people assumed "we're not a factory." But the insight transfers perfectly: creative work has suppliers (clients, strategists), inputs (briefs, assets), transformation steps (design, code), and customers (clients, users). The waste just looks different — it's not physical inventory, it's decisions stuck in someone's inbox.

## The Key Numbers

### Lead Time vs. Cycle Time
- **Lead time**: Total clock time from request to delivery (including all waits)
- **Cycle time**: Just the active work hours

```
Example: Landing Page Project
Lead time: 15 business days
Cycle time: 38 hours (~5 days of focused work)
Efficiency: 38/120 = 32%

Translation: The work is WAITING 68% of the time.
```

### Where Creative Teams Lose Time
| Waste Type | What It Looks Like | Typical Cost |
|---|---|---|
| **Approval queues** | Design sitting in "pending review" | 1-5 days per review cycle |
| **Handoff gaps** | Work finished by designer, not yet picked up by dev | 0.5-3 days per handoff |
| **Rework loops** | "Actually, can you change..." after approval | 20-40% of original effort |
| **Context switching** | People juggling 4+ projects | 20-30% productivity loss |
| **Unclear prioritization** | Work sitting in backlog, nobody knows what's next | Days to weeks |
| **Spec ambiguity** | Dev stops to ask designer questions | 4-8 hours/week per dev |

## Building Your First Agency VSM

### Step 1: Pick One Deliverable Type
Don't try to map everything. Choose the most common project type — "website landing page" or "campaign asset set." You need a repeatable process to map.

### Step 2: Get the Right People in the Room
You need one representative from each role that touches the work: PM, designer, developer, QA, whoever handles client review. 60-90 minutes together.

### Step 3: Map Current State (Left to Right)
For each step, capture:
- **What happens** (brief description)
- **Who does it** (role)
- **Cycle time** (active work hours)
- **Wait time** (hours/days before next step begins)
- **Rework %** (how often does this step need to be redone?)

```
BRIEF          WIREFRAME       DESIGN         CLIENT         DEV
RECEIVED       ────────►      ────────►      REVIEW         HANDOFF
                                              ────────►     ────────►
CT: 2h         CT: 4h          CT: 12h        CT: 1h         CT: 0.5h
WT: 1 day      WT: 2 days      WT: 0 days     WT: 3 days     WT: 1 day
Rework: 0%     Rework: 20%     Rework: 40%    Rework: -      Rework: -

BUILD          CODE            QA             CLIENT         DEPLOY
────────►      REVIEW          ────────►      REVIEW         ────────►
               ────────►                      ────────►
CT: 16h        CT: 2h          CT: 4h         CT: 1h         CT: 1h
WT: 0 days     WT: 1.5 days    WT: 0.5 days   WT: 4 days     WT: 0.5 days
Rework: 10%    Rework: 30%     Rework: -      Rework: -      Rework: -
```

### Step 4: Calculate Totals
- Total cycle time: ~42.5 hours
- Total wait time: ~13.5 days = 108 hours
- Total lead time: ~150.5 hours ≈ 19 business days
- **Efficiency: 28%**

### Step 5: Identify the Biggest Waste
In the example above:
1. **Client review wait times** (3 + 4 = 7 days) — you may not be able to fix this, but you can reduce the number of review cycles
2. **Design rework at 40%** — nearly half the time, design gets revised. Why? Are briefs unclear? Is the client changing their mind? Is the designer not aligned with the CD?
3. **Code review wait of 1.5 days** — a process fix (auto-assign reviewers, SLA) could cut this dramatically

### Step 6: Design Future State
For each major waste, ask: "What would it take to cut this in half?"

Then map the future state with those improvements. The gap between current and future state becomes your roadmap.

## The Non-Obvious Insights VSM Reveals

**1. Client wait time dominates everything**
Most agencies blame internal inefficiency, but 30-50% of total lead time is often spent waiting for client feedback. You can't eliminate this, but you can: batch review cycles, set deadlines, front-load decisions.

**2. Rework is the hidden multiplier**
A 40% rework rate on design doesn't just cost 40% more design time — it cascades. The dev who started building has to redo work. The PM has to re-plan. The timeline shifts. One upstream rework causes 2-3x downstream cost.

**3. The bottleneck isn't where you think**
Teams often blame the slowest worker. VSM usually reveals the bottleneck is a *queue*, not a person. The designer is fast — but work sits for 2 days before they get to it because they're on 5 projects.

**4. Small wait times add up**
A half-day wait at 8 handoff points = 4 days. Nobody notices any individual wait, but together they add a full week to every project.

## Connections

- [[Workflow Audit Deep Dive]] — VSM is Framework 1 in the comprehensive audit playbook
- [[Agency Workflow Audit Framework]] — the higher-level audit context
- [[SIPOC for Agency Pipelines]] — use SIPOC first to scope, then VSM to detail

## Sources

- [Value Stream Mapping for Marketing Teams — Agile Sherpas](https://www.agilesherpas.com/blog/value-stream-mapping)
- [Value Stream Mapping — IT Revolution](https://itrevolution.com/articles/value-stream-mapping/)
- [Value Stream Mapping for Software Delivery — DORA](https://dora.dev/guides/value-stream-management/)

---
tags: [vsm, methodology, creative-workflow, measurement]
date_added:: 2026-04-02
last_updated:: 2026-04-02
