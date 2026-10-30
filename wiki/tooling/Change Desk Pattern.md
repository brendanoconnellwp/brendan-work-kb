# Change Desk Pattern: Notion-Driven AI Website Changes

> A Notion-card pipeline where non-developers describe a website change in plain language, an AI plans and builds it on a dev/staging environment, and a human reviews and ships it — all without the AI making autonomous decisions or touching content, settings, or databases.

## Overview

Change Desk is a pattern for connecting a project-management board (Notion, Linear, Asana, etc.) to a CI/CD pipeline so that non-technical team members can request, review, and approve small website code changes without needing to open git repos or write code.

The core workflow:

1. A project manager or client writes a change request on a card in plain language.
2. Setting a card status triggers a CI pipeline that has the AI **plan** the change.
3. If the plan looks right, another trigger tells the AI to **build** it on a dev/staging environment.
4. Reviewers test the preview, give feedback via the same card, and the AI revises.
5. When approved, a final trigger **ships** the change to production through a normal PR/merge flow.

**The AI never decides to plan, build, or ship on its own.** Every step requires a human to set an explicit field on the card. The pattern is designed for safety-first: changes always go to dev first, and production deploys happen through standard git workflows that developers can see and audit.

## The card workflow

### Step 1 — Describe the request

A card is created on the board with:

- **Project/site** field — picks which repo and environment to target
- **Description** — plain language request: "Add a CTA section to the services page" or "Change the hero headline to match the new branding"

### Step 2 — Plan it

Someone sets the card status to **Plan it**. The system:

1. Reads the request and the site's project notes (AGENTS.md / context files).
2. Produces a plain-English plan describing what files would change and why.
3. Updates the card to either:

   - **Plan ready** — the plan is there to review.
   - **Needs info** — the card lists questions for the requester to clarify.
   - **Needs developer** — the request is risky, off-limits, or requires manual intervention, with an explanation.

### Step 3 — Build it (dev preview)

If the plan is approved, someone sets the card status to **Build it**. The system:

1. Checks out the repo, creates a change branch.
2. Makes the code changes based on the plan.
3. Builds/compiles assets if needed.
4. Deploys the change branch to a **dev/staging** environment.
5. Runs smoke checks (can the page load? does it return the right text?).
6. Updates the card to either:

   - **Preview ready** — with a preview link, testing instructions, and a draft message for the client.
   - **Failed** — if checks fail, with error info.

### Step 4 — Iterate with feedback

Reviewers write feedback at the bottom of the card. Setting **Build it** again revises the same change and updates the preview. This loop continues until the change is approved.

### Step 5 — Ship it

For sites configured for it, setting the status to **Ship it** does:

1. Verifies the dev branch still matches what was previewed.
2. Verifies the production branch is in a known state (no conflicting changes deployed).
3. Creates a pull request merging the change branch into the production branch.
4. Deploys **only the affected code** (typically a theme or specific directory).
5. Updates the card to **Live** with the production URL and any post-deploy steps (e.g. "sync ACF field groups", "update permalinks", "clear cache").

**Ship it refuses** if the branch changed after the preview, or if the production branch state is uncertain. It stops with "Needs developer" instead of overwriting anything.

## Architecture

```text
Notion card
  │  status field changes
  ▼
n8n webhook
  │
  ▼
CI pipeline (GitHub Actions / Bitbucket Pipelines)
  ├─ ai-plan:    card → plan
  ├─ ai-build:   card → change branch → dev/staging preview
  └─ ship:       approved card → merge → production deploy
```

### Key components

- **Trigger**: a Notion automation runs when a status field changes, calling an n8n webhook. The n8n workflow selects the right pipeline based on the card's project/site field.
- **Central orchestrator repo**: a single repo holds all scripts, site config (`sites/<site>/site.yml`), project context for the AI (`sites/<site>/AGENTS.md`), and CI pipeline definitions. Client site repos get nothing added except change branches.
- **Site config**: per-site YAML defines the repo, branch, dev/staging URL, build steps, and deployment method.
- **Bot account**: a bot user with scoped access:
  - Write access to site repos (for pushing change branches and creating PRs).
  - Deploy access to dev/staging and production environments.
- **Secrets**: stored only as secured CI/CD variables (AI API key, Notion token, bot token, deploy keys). Never in files, Notion, docs, or chat.

### Optional: laptop mode

A developer can run the same process manually with an AI agent on their own machine for debugging, rapid iteration, or sites not yet set up in the pipeline.

## Safety rules

1. **Dev first, always.** Changes go to a separate dev/staging environment before production. Live only gets code that passed its dev preview.
2. **Explicit human gate at every step.** The AI does not initiate planning, building, or shipping. A person changes a card field.
3. **The AI edits files only.** Separate scripts handle git operations, builds, and deployments. The AI never sees credentials or keys.
4. **Off-limits to the AI.** Content, plugins, settings, databases, ads/tracking, newsletter signups, redirects, and anything on the project's "never touch" list. Requests about these areas return "Needs developer."
5. **Production deploys are auditable.** Shipping merges through a normal PR, so developers see every production change and can review it.
6. **One deploy at a time per site.** Prevents race conditions. Developers can still deploy the usual way; they should pull the production branch first to avoid conflicts.
7. **Environment boundaries.** Never copy environment-to-environment in a way that overwrites the dev setup (plugin versions, configuration). Copying production content → dev for a fresh content baseline is fine; the reverse direction is not.

## Integration points

- **Notion** (or Linear, Asana, etc.): automation triggers when a status/select field changes → HTTP call to webhook.
- **n8n** (or Make, Zapier): receives webhook, maps fields, triggers CI pipeline via API.
- **CI/CD** (GitHub Actions, Bitbucket Pipelines, GitLab CI): runs AI planning, building, or shipping scripts.
- **Hosting** (WP Engine, Kinsta, Cloudflare Pages, Vercel, etc.): deploy via git push, API deploy hook, or SFTP.
- **AI**: called from CI scripts with the card description and project context as input. The AI generates a plan or code changes; the results are written to the repo, not executed directly by the agent.

## Cost tracking

Each card records the AI usage cost and elapsed time for planning, building, and shipping steps. This helps decide which changes justify automation and which are better done manually.

## When to use this pattern

Good candidates:

- Small, well-defined code changes to established sites (CSS tweaks, template adjustments, text updates, ACF field changes).
- Sites with a dev/staging environment and a repeatable deploy process.
- A PM or non-technical person who fields change requests and wants to unblock the team.
- A team that wants CI/CD but has resisted it because the current process is entirely manual.

Not a good fit:

- Greenfield builds or major architecture changes.
- Sites without a dev/staging environment.
- Changes that touch content, databases, plugins, or third-party integrations.
- Teams that don't have someone to review AI output and make go/no-go decisions.

## Connections

- [[n8n for Agency Ops]] — the automation layer that triggers pipelines from Notion status changes.
- [[Proactive Agent Workflows]] — the AI-driven planning and building steps fit the "bounded autonomous execution" pattern.
- [[Human-Agent Interface Patterns]] — the approval-gate workflow (plan → review → build → preview → ship) is a concrete example of the "human-approval workflow" pattern.
- [[WordPress Agency Tech Stack]] — the pattern is especially natural for WordPress sites with git-based deploy workflows.
- [[Workflow-to-Agent Decision Framework]] — use the scoring rubric to evaluate whether a given change request is a good candidate for this pipeline.
- [[Agent KPI Frameworks]] — track time and cost per card to quantify the automation value.

---
tags: [workflow, automation, ci-cd, notion, n8n, ai-agents, human-in-the-loop, deployment]
last_updated: 2026-10-30