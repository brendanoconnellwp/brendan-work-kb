# Workflow-to-Agent Decision Framework

> A practical scoring rubric and decision tree for evaluating whether an agency workflow is a good candidate for AI agent automation — built for your first week on the ground.

## Overview

You're walking into a ~10-person web agency. Your job, in the words of the tweet that defined the role: find workflows where "if you threw compute (in the form of agents) at a task you could either execute it 100X faster or do it 100X more times than before." That framing is everything. You're not looking for workflows to make 20% more efficient — you're hunting for tasks where parallelization or speed-of-execution fundamentally changes what's possible.

The problem is that not every workflow deserves an agent. Roughly 40% of agentic AI deployments get canceled due to rising costs, unclear value, or poor risk controls. The difference between a successful agent deployment and a waste of everyone's time comes down to picking the right target. This framework gives you a structured way to evaluate any workflow you encounter during a [[Workflow Audit Deep Dive]] and decide: agent, tool, or leave it alone.

The core question isn't "can an AI do this?" (the answer is increasingly yes). It's "does throwing compute at this create 100X value, or does it create 100X problems?"

## Key Concepts

### The 100X Test: Two Modes of Agent Value

Every candidate workflow should pass at least one of these tests:

- **100X Faster**: The task is currently bottlenecked by human speed — reading, writing, analyzing, transforming. An agent doing it in seconds instead of hours unlocks real value. *Example: generating first-draft SEO content for 50 pages instead of 5.*
- **100X More**: The task is currently limited by human bandwidth — you can only do it for your top clients, or once a quarter. An agent lets you do it for every client, every week. *Example: running competitive audits across all client sites weekly instead of quarterly for one.*

If a workflow doesn't hit either threshold, it's probably a tool-assist situation (copilot, not agent) rather than a full automation target.

### The VFCDR Scoring Rubric

Score each candidate workflow on five dimensions (1-5 scale). This is adapted from the AI Value Rubric and tailored for agency context. Use it in your [[Agency Workflow Audit Framework]] sessions.

| Dimension                 | 1 (Low)                                  | 3 (Medium)                                   | 5 (High)                                         |
| ------------------------- | ---------------------------------------- | -------------------------------------------- | ------------------------------------------------ |
| **V — Volume**            | Done a few times/year                    | Weekly across clients                        | Daily or per-project                             |
| **F — Formalizability**   | Relies on gut feel, taste, relationships | Has rough guidelines but many judgment calls | Clear inputs, rules, and success criteria        |
| **C — Compute Leverage**  | Speed/scale wouldn't change the outcome  | Faster would be nice, more would help        | 100X faster or 100X more would be transformative |
| **D — Data Availability** | Data is in people's heads or scattered   | Data exists but needs assembly               | Structured, accessible, API-ready                |
| **R — Risk Tolerance**    | Errors are expensive or client-facing    | Errors are recoverable with some effort      | Errors are cheap, caught by human review         |

**Scoring thresholds:**
- **20-25**: Strong agent candidate. Build it.
- **15-19**: Promising but needs guardrails. Pilot with human-in-the-loop.
- **10-14**: Tool-assist territory. Use AI to augment, not replace.
- **5-9**: Leave it alone. Human judgment is the value here.

### Decision Tree: Week 1 Walkthrough

Use this when you're mapping workflows via [[SIPOC for Agency Pipelines]] or [[Value Stream Mapping for Creative Work]]:

```
START: Pick a workflow
  │
  ├─ Can you clearly define the inputs and outputs?
  │   ├─ NO → Document it better first. Come back later.
  │   └─ YES ↓
  │
  ├─ Does it happen at least weekly across the agency?
  │   ├─ NO → Low volume. Probably not worth the setup cost.
  │   └─ YES ↓
  │
  ├─ Would 100X speed OR 100X volume change the game?
  │   ├─ NO → Consider tool-assist (copilot mode), not full agent.
  │   └─ YES ↓
  │
  ├─ Can you get the data the agent needs programmatically?
  │   ├─ NO → Data pipeline problem. Solve that first.
  │   └─ YES ↓
  │
  ├─ Are errors cheap and catchable?
  │   ├─ NO → Add human review checkpoint. Then proceed.
  │   └─ YES → Strong candidate. Score it with VFCDR and prioritize.
```

### Agency Workflows: Good vs. Bad Candidates

**Strong candidates at a web agency:**
- SEO content brief generation (V:5, F:4, C:5, D:4, R:4 = 22)
- QA checklists and cross-browser testing triage (V:5, F:5, C:4, D:4, R:4 = 22)
- Competitive site audits across client portfolio (V:3, F:4, C:5, D:5, R:5 = 22)
- Status report compilation from PM tools (V:5, F:5, C:4, D:4, R:4 = 22)
- Content migration mapping (old URL → new URL) (V:3, F:5, C:5, D:4, R:4 = 21)

**Weak candidates (leave human):**
- Client relationship management and tone-setting (F:1)
- Creative direction and brand strategy (F:1, C:1)
- Scope negotiation and change order conversations (F:2, R:1)
- Design critique and aesthetic judgment (F:1)
- Hiring and team culture decisions (F:2, R:1)

The pattern: strong candidates have structured data, clear rules, and high repetition. Weak candidates depend on taste, relationships, or judgment that's hard to formalize.

### What Data Does the Agent Need?

Before building, map the agent's data requirements using the same lens as [[SIPOC for Agency Pipelines]]:

1. **Inputs**: What does the agent receive? (files, URLs, database queries, API responses)
2. **Context**: What background knowledge is needed? (client brand guidelines, past decisions, domain expertise)
3. **Tools**: What systems does it need access to? (CMS, PM tool, design tool APIs, analytics)
4. **Outputs**: What does it produce? (documents, data, actions in other systems)
5. **Feedback loop**: How does a human validate the output and correct errors?

If any of these are trapped in Slack conversations, people's heads, or unstructured docs — that's your bottleneck, not the agent itself.

### Failure Modes to Watch For

From Microsoft's taxonomy and real-world agency deployments:

- **Brittleness**: Agent works perfectly on the demo, breaks on real client data. Real inputs shift as policies update and catalogs evolve. *Mitigation: test with messiest real data from day one.*
- **Context loss at handoffs**: Agent completes a task but doesn't pass critical context to the human reviewer or next step. *Mitigation: design explicit handoff templates.*
- **Automation bias**: Team starts trusting the agent blindly, stops reviewing outputs. Quality degrades silently. *Mitigation: mandatory review checkpoints, rotating reviewers.*
- **Monolithic agent syndrome**: One agent doing research AND writing AND optimization produces mediocre everything. *Mitigation: specialized agents in sequence, each doing one thing well.*
- **Observability gaps**: When something goes wrong, nobody can figure out why. *Mitigation: log every agent step — inputs, outputs, confidence, cost.*
- **Scope creep**: "While we're at it, let's have the agent also..." kills more agent projects than technical failure. *Mitigation: one workflow, one agent, one success metric.*

## Connections

- [[Agency Workflow Audit Framework]] — Use VFCDR scoring during your audit sessions
- [[Workflow Audit Deep Dive]] — The practitioner playbook for actually running the audit
- [[SIPOC for Agency Pipelines]] — Map inputs/outputs before scoring
- [[Value Stream Mapping for Creative Work]] — Find the time sinks where 100X leverage lives
- [[AI Agent Landscape 2026]] — What's actually buildable today
- [[What the Agency AI Role Actually Is]] — The bigger picture: audit → quick wins → deep integration

## Open Questions

- **Where's the line between "tool-assist" and "full agent"?** The copilot-to-agent spectrum is blurry. When does a workflow deserve full autonomy vs. just better tooling?
- **How do you score "Formalizability" for creative-adjacent tasks?** Some agency work (like writing alt text or resizing images) feels creative but is actually highly formalizable. Need better heuristics here.
- **What's the minimum viable monitoring setup?** Full observability is ideal but expensive. For a 10-person agency, what's the minimum logging that catches failures without creating overhead?
- **How do multi-client agents work?** Can one agent handle the same workflow across clients with different brand guidelines, or does each client need its own instance?

## Sources

- [Should You Automate This? How to Choose the Right Workflows for AI Agents](https://www.salesforce.com/blog/how-to/agentic-automation/?bc=OTH) — Salesforce's framework for evaluating agentic automation candidates
- [The AI Value Rubric: A Structured Approach to Prioritizing AI Solutions](https://www.uxmatters.com/mt/archives/2025/12/the-ai-value-rubric-a-structured-approach-to-prioritizing-ai-solutions.php) — Five-dimension scoring framework (basis for VFCDR)
- [12 Failure Patterns of Agentic AI Systems](https://www.concentrix.com/insights/blog/12-failure-patterns-of-agentic-ai-systems/) — Comprehensive failure taxonomy
- [Taxonomy of Failure Modes in Agentic AI Systems](https://cdn-dynmedia-1.microsoft.com/is/content/microsoftcorp/microsoft/final/en-us/microsoft-brand/documents/Taxonomy-of-Failure-Mode-in-Agentic-AI-Systems-Whitepaper.pdf) — Microsoft whitepaper on agentic AI risks
- [Agent Failures in the Wild: Why Workflows Break After Go-Live](https://a21.ai/agent-failures-in-the-wild-why-workflows-break-after-go-live/) — Production failure patterns
- [SEO Agency AI Automation Case Study](https://eco-n-tech.com/projects/seo-agency-ai-automation/) — 10X productivity boost with human checkpoints
- [How to Use Process Mining to Identify Automation Opportunities](https://www.servicenow.com/community/process-mining-blog/how-to-use-process-mining-to-identify-automation-opportunities/ba-p/3061642) — Scoring criteria for automation candidates
- [AI Automation Agents: Complete Guide to Workflow Intelligence](https://latenode.com/blog/ai-agents-autonomous-systems/ai-agent-fundamentals-architecture/ai-automation-agents-in-2025-complete-guide-to-workflow-intelligence-9-implementation-strategies) — 40% cancellation stat, selection criteria
- Raw sources: `raw/articles/2026-04-15_salesforce-agentic-automation-choosing-workflows.md`, `raw/articles/2026-04-15_uxmatters-ai-value-rubric.md`, `raw/articles/2026-04-15_concentrix-failure-patterns-agentic-ai.md`, `raw/articles/2026-04-15_seo-agency-ai-automation-case-study.md`

---
tags: [ai-agents, decision-framework, workflow-automation, agency-ops, scoring-rubric]
date_added:: 2026-04-15
last_updated:: 2026-04-15
