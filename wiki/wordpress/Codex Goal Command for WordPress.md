# Codex Goal Command for WordPress

> Codex's `/goal` command lets you front-load a tight spec, hand off the build, and walk away. The runtime keeps working until the spec's evidence requirements pass — turning AI-assisted dev from "supervise approve approve approve" into "write the spec, come back later." Highly relevant to how the lead developer could compress WordPress plugin and migration work.

## What `/goal` actually is

A normal Codex prompt says *"do this task once."* `/goal` says *"keep pursuing this objective until evidence says done."*

When you start a goal, Codex attaches a persisted objective to your thread. The runtime tracks the objective, the current status, time elapsed, and tokens spent. Each turn the runtime checks whether work remains — if yes, Codex gets a continuation prompt and picks the next action. The cycle repeats until completion criteria are met, the token budget runs dry, or you pause it.

Four states: `active`, `paused`, `complete`, `budget_limited`.

The load-bearing detail: Codex's continuation prompt template tells the model to **map every requirement to concrete evidence — files, command output, test results — and treat uncertainty as not-done**. That's what makes 28-minute unsupervised runs trustworthy. The runtime refuses to declare victory without proof.

It's a different shape from a Ralph-style outer loop (where you script the iteration) or a single long prompt (which drifts as the context window fills). The runtime owns continuation; the spec owns completion.

## The spec is the work

The temptation with `/goal` is to write one sentence and hope. For trivial tasks, fine. For WordPress, it falls over fast — capability checks, env gates, sanitization, hook timing, `wp-cli` running on host vs inside the wp-env Docker container. An agent that doesn't know about those produces code that *looks* correct and *behaves* badly.

The pattern that works: a "spec layer" upstream of `/goal`. In the source, this is a Codex Agent skill called `wp-spec-to-goal` that turns a vague paragraph into a Codex-ready bundle:

1. A scaffolded plugin folder (PSR-4 layout, `composer.json`, `.wp-env.json`, `AGENTS.md`, `.gitignore`, `package.json`) — only the parts that don't already exist.
2. A `goals/<slug>/` directory with three files: `GOAL.md`, `VERIFY.md`, `PROGRESS.md`.
3. A tailored `/goal` command to paste.

**Five minutes upfront buys 28 minutes of unsupervised execution.** That's the trade.

## The goal trio

Three files, three jobs, no moonlighting:

- **`GOAL.md`** — what must be true when work is done. Acceptance criteria with IDs (`AC-001.1`, `AC-001.2`). Definition of Done section that explicitly references the verification audit.
- **`VERIFY.md`** — how Codex proves it. The exact commands to run, smoke checks to perform, evidence format. WordPress-specific rule: every command routes through `npx wp-env run cli` rather than native `wp` or `composer` on the host (host commands hit a different PHP/MySQL environment and produce results that look right but lie).
- **`PROGRESS.md`** — what happened along the way. Skeleton at start; Codex fills it in. The completion audit table maps every AC to a real artifact (test output, screenshot, file).

Why three instead of one? Because `/goal` re-reads them every continuation turn. Mix the responsibilities and the audit gets confused. Keep them separate and Codex always knows what it's looking at.

The completion audit is the contract. Every AC gets a row pointing to a real file, command output, or screenshot. Anyone can re-run the commands afterward and confirm the markings. No theatre.

## When `/goal` earns its keep

Sweet spot: **bounded objectives with clear acceptance criteria**.

Reach for it on:
- Bug fixes with reproducible failures and regression tests
- Refactors with a "behavior preserved" success condition
- Single feature slices from a larger project — one user story at a time
- API integration work with a well-specified contract upfront

Hold back on:
- Vague objectives ("make the app better", "refactor everything") — no finish line means no audit
- Multi-feature builds that should be split into separate goals
- Anything where "done" can't be defined before starting

Framing that sticks: **`/goal` works best as an inner loop. The project manager role still belongs to you.** For larger work, split into multiple goals (`001-data-model`, `002-admin-ui`, `003-rest-api`) and run them sequentially. One coherent slice per goal.

Two practical caveats: (1) Plan Mode and `/goal` don't mix — exit Plan Mode before starting. (2) Skip the spec layer and you get a one-line-objective result. Garbage in, garbage out.

## What this means for agency dev work

A strong applied case is WordPress ACF (Advanced Custom Fields) migration work: bounded, repeatable, and evidence-checkable. The migration pattern looks like a perfect `/goal` candidate — each component conversion is a slice; each gets a GOAL.md + VERIFY.md; each gets walked away from.

The bigger pattern: **the part of dev that AI is getting good at is executing well-specified plans without supervision. The skill that pays off is writing the plan.** This aligns with a common agency view of the dev process — writing front-end code line-by-line isn't sustainable, but `/goal`-style autonomy is the shape that *does* maintain pixel-perfection (the spec defines what's acceptable, the verification proves it).

It also reframes the "supervised vs autonomous" debate. A 50-minute supervised Claude Code build vs a 28-minute `/goal` run isn't about model quality. It's about whether the spec is tight enough to trust the result without watching.

## Open questions

- Does `wp-spec-to-goal` (or a Claude-Code-equivalent skill) exist for the migration shapes you do most? If not, building a tailored `acf-migration-spec` skill could be a high-leverage internal build.
- How does this interact with your review process? If PRs currently get human review, would `/goal` runs go through the same gate, or a different one optimized for spec auditing rather than line-by-line review?
- Cost economics: at what spec-writing time does autonomous execution beat supervised? Worth measuring on a real task.

## Connections

- [[Claude Code for WordPress]] — the supervised-loop equivalent; this is the autonomous-loop sibling
- [[Workflow-to-Agent Decision Framework]] — the "bounded objective with clear acceptance criteria" rule maps directly to VFCDR scoring
- [[Claude Code Agent Capabilities]] — Claude Code has skills/subagents/hooks that could host an equivalent spec layer

## Sources

- `raw/How to Use the Codex Goal Command for WordPress Plugins.md` — Nathan Onn's full walkthrough (2026-05-07)
- Codex `/goal` documented in CLI 0.128.0 changelog (April 30, 2026)
- Reference repo: github.com/nathanonn/wp-login-for-ai

---
tags: [wordpress, codex, ai-agents, autonomous-dev, evidence-based-autonomy]
date_added:: 2026-05-08
last_updated:: 2026-05-08
