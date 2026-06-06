---
aliases: [Agency Workflow Audit Framework]
---
# Agency Workflow Audit Framework

> Before you can improve a pipeline, you have to see it clearly. Most agencies have never mapped their design-to-dev workflow end-to-end.

## Overview

A workflow audit is the first thing you do in the role. It's not glamorous, but it's where you earn credibility and find the actual leverage points. The goal: map how work flows from client brief to shipped deliverable, identify where time and quality leak, and prioritize what to automate or improve.

## The Audit Process

### Step 1: Shadow & Interview (Week 1)
Spend time with each role — don't just ask "what's your workflow," *watch them work*. People often don't know their own inefficiencies because they've normalized them.

**Questions for each role:**
- Walk me through a typical project from your perspective
- Where do you wait for other people?
- What do you do more than once that feels redundant?
- Where do things fall through the cracks?
- What information do you wish you had earlier?

### Step 2: Map the Pipeline (Week 2)
Document the actual flow (not the ideal flow):

```
Brief → Discovery → Design
  → Concept → Iteration → Approval → Handoff
    → Dev Setup → Build → Review → QA → Deploy → Delivery
```

At each transition point, note:
- **What artifact moves between roles** (Figma link? PDF? Slack message? Nothing?)
- **How long the handoff takes** (minutes? days?)
- **What information is lost** in the handoff
- **Who is blocked** and for how long

### Step 3: Identify Leakage Points (Week 3)
Common agency leaks:

| Leak | Symptom | Root Cause |
|---|---|---|
| Design-to-dev handoff | Devs rebuild instead of translate | Figma files not structured for code extraction |
| Spec ambiguity | Back-and-forth in Slack about details | No single source of truth for requirements |
| Status tracking | PMs spend hours compiling updates | No automated rollup from actual work tools |
| Scope creep | Projects consistently over budget | Change requests not tracked or priced |
| Review bottlenecks | PRs sit for days | No routing, no SLAs, no automation |
| Duplicated work | Same component built differently by different devs | No shared component library or design system |

### Step 4: Prioritize by Impact × Ease (Week 4)
Plot each issue on a 2x2:
- **High impact, easy fix** → Do immediately (n8n automations, Claude Code setup)
- **High impact, hard fix** → Plan for Phase 3 (Figma MCP pipeline, process redesign)
- **Low impact, easy fix** → Do when convenient
- **Low impact, hard fix** → Skip

### Step 5: Present & Align
Share findings with leadership and the team. Frame it as:
- "Here's what I found"
- "Here's what it's costing us" (in hours, dollars, or quality)
- "Here's what I recommend, in what order"
- "Here's what success looks like in 30/60/90 days"

## The Spec-Driven Development Angle

A growing trend in 2026: **Spec-Driven Development (SDD)**. Instead of ad hoc prompts to AI tools, teams write structured specifications that drive what AI agents produce. This requires:
- Standardized brief templates
- Design tokens and component specs exported as structured data
- Clear acceptance criteria per deliverable

SDD is where the audit leads — once you see the chaos, the fix is almost always "add structure to the handoff artifacts."

## Traceability

One audit insight that surprises agencies: **decision traceability is terrible.** When an auditor (or just a confused PM) asks "why was it built this way?", the answer is buried across Slack threads, Figma comments, and someone's memory.

Building audit trails — even simple ones like decision logs in the wiki or structured commit messages — makes the whole operation more resilient.

## Connections

- [[What the Agency AI Role Actually Is]] — the audit is Phase 1 of the role
- [[Workflow Audit Deep Dive]] — the expanded practitioner-level version of this guide
- [[SIPOC for Agency Pipelines]] — start every audit here before going deep
- [[Value Stream Mapping for Creative Work]] — the detailed diagnostic tool for measuring waste
- [[Figma MCP Design-to-Code Pipeline]] — audit will likely reveal Figma file structure as a bottleneck
- [[n8n for Agency Ops]] — audit findings directly inform which automations to build
- [[Getting Agency Teams to Actually Use AI]] — the audit is also your listening tour for adoption barriers

## Open Questions

- How do you audit without disrupting active projects?
- What's the right level of detail — too granular wastes time, too high-level misses real issues?
- How do you handle findings that implicate specific people's work habits?
- Should the audit be a one-time thing or an ongoing practice?

## Sources

- [Auditing Your Pipeline: Compliance and Accountability](https://dohost.us/index.php/2026/03/30/auditing-your-pipeline-ensuring-compliance-and-accountability-for-client-work/)
- [6 Structural Shifts Redefining Agency Architecture](https://www.aaaa.org/blog/decisions-2026-dc-next-agency-now-6-structural-shifts-redefining-your-agencys-operating-architecture/)
- [Agentic Workflows — McKinsey](https://medium.com/quantumblack/agentic-workflows-for-software-development-dc8e64f4a79d)

---
tags: [workflow-audit, agency-ops, design-to-dev, process]
date_added:: 2026-04-02
last_updated:: 2026-04-02
