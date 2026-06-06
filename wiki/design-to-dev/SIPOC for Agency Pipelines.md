---
aliases: [SIPOC for Agency Pipelines]
---
# SIPOC for Agency Pipelines

> The 30-minute diagram that gives you the 30,000-foot view before you zoom in. Start every audit here.

## Overview

SIPOC (Suppliers, Inputs, Process, Outputs, Customers) is a high-level process mapping tool from Six Sigma. For agency work, it's the perfect first-meeting tool — simple enough to build collaboratively, structured enough to reveal gaps immediately.

Use SIPOC to **scope** the audit and align the team. Use [[Value Stream Mapping for Creative Work]] to **detail** the process after.

## Agency SIPOC Template

### Example: Website Build Pipeline

| Suppliers | Inputs | Process | Outputs | Customers |
|---|---|---|---|---|
| Client stakeholder | Business requirements, brand guidelines, content | **1. Discovery** | Project brief, site map, technical requirements | PM, designer, dev lead |
| Strategist / PM | Project brief, timeline, budget | **2. Design** | Wireframes, design comps, design system updates | Client (for approval), dev team |
| Design team | Approved designs, design tokens, asset exports | **3. Development** | Built pages, integrated CMS, staging environment | QA team, client (for review) |
| Dev team | Staging build, test plans | **4. QA & Review** | Bug reports, client feedback, approval | Dev team (for fixes), PM (for status) |
| Dev team, PM | Approved build, deployment checklist | **5. Launch** | Live website, documentation, handoff materials | Client, end users |

## How to Build One in 30 Minutes

**Prep:** Whiteboard or shared Miro/FigJam board. One person from each role in the room.

**Minute 0-5:** Start with the Process column. Ask: "In 4-5 steps, what are the major phases of a typical project?" Don't overthink it. Get the bones down.

**Minute 5-15:** Work the Outputs and Customers columns. For each process step: "What does this step produce? Who needs that output to do their work?"

**Minute 15-25:** Work the Suppliers and Inputs columns. For each step: "What does this step need to start? Who provides that?"

**Minute 25-30:** Sanity check. "Does this match reality? What did we miss?"

## What SIPOC Reveals Instantly

**1. Missing inputs**
If a step needs something that no supplier provides, you've found a gap. Common example: dev needs interaction specs but design only outputs static comps.

**2. Unclear customers**
If nobody knows who the "customer" of a step is, that step's quality criteria are undefined. Who decides if wireframes are "done"? The client? The CD? Both?

**3. Handoff ambiguity**
The border between two process steps is where handoffs live. If the output of Design doesn't match the input needed by Development, you've found the friction.

**4. Scope boundaries**
SIPOC shows you where the process starts and ends. This prevents scope creep in the audit itself — you know exactly what's in and out of bounds.

## SIPOC vs. VSM: When to Use Which

| | SIPOC | VSM |
|---|---|---|
| Detail level | High-level, 4-5 steps | Granular, every sub-step |
| Time to create | 30 minutes | 2-4 hours |
| Best for | Scoping, alignment, first meeting | Deep diagnosis, quantified waste |
| Data needed | None — built from team knowledge | Time data, tool metrics |
| Who's involved | Full team, quick session | Process owners, with data prep |

**The sequence:** SIPOC first (week 1) → VSM for the highest-priority pipeline (week 2-3) → Impact-Effort Matrix for findings (week 3-4).

## Connections

- [[Workflow Audit Deep Dive]] — SIPOC is Framework 2 in the comprehensive audit playbook
- [[Value Stream Mapping for Creative Work]] — the detailed mapping you do after SIPOC
- [[Agency Workflow Audit Framework]] — the higher-level audit context

## Sources

- [SIPOC Diagram — Asana](https://asana.com/resources/sipoc-diagram)
- [SIPOC — Go Lean Six Sigma](https://goleansixsigma.com/sipoc/)
- [SIPOC — iSixSigma](https://www.isixsigma.com/dictionary/suppliers-inputs-process-output-customers-sipoc/)

---
tags: [sipoc, methodology, process-mapping, framework]
date_added:: 2026-04-02
last_updated:: 2026-04-02
