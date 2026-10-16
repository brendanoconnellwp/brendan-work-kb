---
aliases: [Change Desk]
---
# Change Desk Plan-Approve-Build Pipeline

> Non-technical staff request website changes on a Notion card; AI plans, a human approves, AI builds and deploys a preview to a dev site. Live at about $0.20 and 4 minutes per change.

## Overview

Change Desk is an internal agency system that turns client website change requests into reviewed, deployable previews without a developer doing the first pass. It ties together agentic dev, WordPress, client change management and automation in one loop. Started Oct 2026; working end to end on two client WordPress sites, one of them bilingual (EN/FR via WPML).

The design principle: the AI does the labor, a person owns the decisions. Two human gates exist: approving the plan, and a developer taking the change live.

## Key Concepts

### The flow
1. A staff member fills in a card on a Notion board (Project = client site) and sets **AI → Plan it**.
2. A Notion automation fires a webhook to n8n. n8n looks up the site's repo in a data table (one row per site) and triggers the repo's `ai-plan` pipeline.
3. Claude writes a plan onto the card and sets AI to **Plan ready** (~1 min).
4. A person reads the plan and sets **Build it**: the human gate.
5. `ai-build` pipeline (~3 min): Claude makes the change on a fresh `change/...` branch, PHP lint runs, the theme folder deploys to the dev install, before/after checks run, and the card receives the preview link, cost and time. AI is set to **Preview ready**.
6. Going live stays with a developer via normal code review and a Git push deploy.

### Guardrails
- Deploys only to dev; production and staging are blocked by a deploy-target check script plus a protected-environments list in a per-site config file.
- Scope is the theme folder only. Never main/production branches, never databases or environment copies.
- Tooling lives on a separate `changedesk/tooling` branch per repo until reviewed and merged.

### Per-site setup
1. Copy the `.claude/` folder and pipeline file to the tooling branch; write a site guide and config.
2. Create a dev install copied from production; give the deploy user access to only that.
3. Set repo variables (Notion token, Anthropic key, environment name, theme path, deploy SSH key) and run the deploy pipeline once.
4. Add the site to the n8n data table and to the board's Project list.

### Gotchas
- **Bilingual sites:** changing English text via code drops the French WPML translation. Multilingual sites need an explicit translation step.
- **Don't poll from n8n.** A 2-minute check consumed 2,000 of 2,500 monthly executions in 3 days. Use Notion automation webhooks instead.
- **Plan accounts and keys up front:** per site and for company ownership. Personal API tokens expire and tie the system to one person; workspace-level tokens may need a higher plan tier.

## Connections

- [[Human-Agent Interface Patterns]]: approval gate between plan and build
- [[Workflow-to-Agent Decision Framework]]: why change requests are a strong automation candidate
- [[n8n for Agency Ops]]: webhook orchestration layer
- [[Claude Code for WordPress]]: the supervised dev loop this wraps in guardrails
- [[Notion Custom Agents]]: alternative native approach to the Notion side
- [[Getting Agency Teams to Actually Use AI]]: lets non-technical staff use AI through a tool they already know
- [[Secret Rotation Tools Doppler and Infisical]]: relevant to the shared-keys cleanup below

## Open Questions

- Move the board and n8n workflow to shared (company-owned) spaces.
- Merge tooling into main via dev review.
- Replace the borrowed Notion bot token with a dedicated one; move shared keys to workspace-level variables.
- How to handle translation automatically on WPML sites?
- Clean up old tokens and test branches/cards.

## Sources

- Personal project notes, Oct 16, 2026 (client names, repos and account identifiers deliberately omitted).

---
tags: [ai-agents, workflow, wordpress, n8n, notion, agency-ops, claude-code]
last_updated: 2026-10-16
