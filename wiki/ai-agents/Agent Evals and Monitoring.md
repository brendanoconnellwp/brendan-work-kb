# Agent Evals and Monitoring

> How to know if your deployed AI agents are still working correctly — and catch it fast when they're not.

## Overview

You've built an agent that extracts tasks from meeting transcripts, or one that monitors time tracking and flags anomalies, or one that generates weekly client reports. It works great on Tuesday. By Friday, something's off — the outputs are subtly wrong, a model update changed the formatting, or the agent started hallucinating project names. You don't notice for two weeks because there's no alert, no test, no baseline to compare against.

This is the central problem of agent ops: **AI agents degrade silently.** Unlike traditional software that crashes loudly when broken, an agent with a bad output still returns a 200 status code. Infrastructure monitoring won't save you. You need a different kind of testing and monitoring — one designed for non-deterministic systems that can fail in ways you didn't predict.

The good news is that practical eval and monitoring patterns have matured significantly. You don't need an ML team or enterprise tooling. A small agency deploying agents via [[Claude Code Agent Capabilities]], [[n8n for Agency Ops]], or similar tools can set up meaningful quality gates with a few hours of work.

## Key Concepts

### What Is an Eval?

An eval is simply a test for an AI system: give the agent an input, then apply grading logic to its output. The twist compared to traditional unit tests is that you're testing a system whose outputs are non-deterministic — the same input can produce different (but equally valid) outputs.

Anthropic's engineering team recommends three grading approaches, in order of preference:

1. **Code-based graders** — Deterministic checks. Did the output contain the required fields? Is the JSON valid? Did the agent call the right tool? These are fast, reliable, and should be your first choice for anything with a clear right/wrong answer.
2. **LLM-as-judge** — Use a separate LLM call to assess quality on subjective dimensions (tone, completeness, reasoning quality). More flexible but slower and costlier. Tip: use PASS/FAIL rather than point scales — binary decisions force clarity about what "acceptable" means.
3. **Human graders** — Use primarily for calibration. Review a sample of outputs weekly to verify your automated graders are catching what matters.

### The Golden Dataset

Your eval suite runs against a "golden dataset" — a curated set of test inputs with expected outputs or grading criteria. Start small: **10-15 cases is enough to begin.** Cover these categories:

- **Happy path** — Common inputs the agent handles daily
- **Known failures** — Cases that broke in the past (these are gold)
- **Edge cases** — Unusual inputs, empty fields, unexpected formats
- **Out of scope** — Things the agent should refuse or escalate

For a transcript-to-tasks agent, this might be 5 real meeting transcripts with manually verified task lists, 3 transcripts with tricky cases (cross-talk, ambiguous assignments), and 2 adversarial inputs (a transcript that's actually a podcast, or a blank file).

Critical nuance: **grade outcomes, not paths.** Anthropic found that checking whether an agent used specific tool calls in a specific order is too rigid — agents regularly find valid approaches designers didn't anticipate. Check *what* was produced, not *how*.

### What Breaks When Models Update

Model updates are the most common source of agent regression, and they're uniquely dangerous because they happen without any code change on your side. Here's what typically goes wrong:

- **Output format changes** — A model that reliably returned JSON starts wrapping it in markdown code fences, or changes field names
- **Tone and style shifts** — Reports become more verbose or more terse
- **Edge case regression** — A case the old model handled correctly now fails
- **Token usage drift** — The same prompts consume more tokens (costs go up silently)
- **Instruction sensitivity changes** — Prompts that worked fine become over- or under-interpreted

The fix: **run your eval suite before and after any model update.** Teams with evals can validate a new model version in hours. Teams without evals spend weeks on manual testing and still miss things.

### Drift Detection

Research on "agent drift" (arXiv 2601.04170) identifies three types of degradation in production agents:

- **Semantic drift** — The agent gradually deviates from its original intent
- **Behavioral drift** — Unintended strategies emerge over extended use
- **Coordination drift** — In multi-agent systems, consensus breaks down

For a small agency, the practical concern is simpler: **does the agent's output quality today match what it was when you deployed it?** Detect this by:

1. **Sampling production outputs** — Don't eval every run; sample 5-10% and run automated graders on the sample
2. **Tracking key metrics over time** — Task extraction count per transcript, report length distribution, error/refusal rates
3. **Comparing against your golden baseline** — Run the full eval suite weekly on a schedule, not just on code changes

### Monitoring and Alerting for Small Teams

You don't need a dedicated observability platform on day one. Here's a practical stack that scales:

**Level 1 — Logs and eyeballs (week 1)**
Log every agent run: input summary, output, latency, token count, cost. Review a sample manually each day. This alone catches the biggest problems.

**Level 2 — Automated checks (week 2-3)**
Add code-based graders that run post-execution. For a task extraction agent: Does the output parse as valid JSON? Are there >0 tasks? Is each task under 200 characters? Alert (Slack, email) when checks fail.

**Level 3 — Scheduled evals and dashboards (month 2+)**
Run your golden dataset eval suite on a cron schedule. Track pass rates over time. Tools like **Promptfoo** (open-source, CLI-based, GitHub Actions integration) or **Langfuse** (open-source, self-hostable, free tier at 50k observations/month) are well-suited for small teams. **Braintrust** offers a generous free tier (1M events/month) if you prefer a hosted solution.

**What to alert on:**
- Eval pass rate drops below threshold (e.g., <90%)
- Latency P95 exceeds 2x baseline
- Token cost per run exceeds 1.5x baseline
- Any code-based grader failure (malformed output, missing fields)
- Agent refusal/error rate exceeds baseline

### Regression Testing in CI/CD

If your agent prompts and configs live in a git repo (they should), you can run evals in CI just like unit tests. The workflow:

1. Developer changes a prompt or tool configuration
2. CI triggers eval suite against golden dataset
3. Compare scores against the production baseline
4. Gate the merge on aggregate pass rate

Promptfoo and DeepEval both offer GitHub Actions integrations for this. Even a simple script that runs 15 test cases and checks pass/fail is vastly better than nothing.

The key insight from practitioners: **regression testing for LLM systems decays over time.** A test suite built three months ago may still pass while real-world performance degrades because production inputs have shifted. Refresh your golden dataset quarterly from recent production logs and incident reports.

## Connections

- [[AI Agent Landscape 2026]] — The broader ecosystem context for why agent monitoring matters as deployment scales
- [[Claude Code Agent Capabilities]] — The agent platform many of these evals would target; Claude's tool-use patterns inform what to test
- [[Proactive Agent Workflows]] — Proactive agents (scheduled, event-driven) are harder to monitor because there's no user in the loop to notice failures
- [[n8n for Agency Ops]] — n8n workflows can implement the monitoring layer: scheduled eval runs, Slack alerts on failures, logging to a database

## Open Questions

- **LLM-as-judge reliability** — How much can you trust an LLM to grade another LLM's output? Calibration against human review is essential, but how often?
- **Eval cost management** — Running LLM judges on production samples adds API costs. What's the right sampling rate for a 10-person agency?
- **Cross-model eval portability** — If you switch from Claude to GPT (or vice versa), do your evals still work, or are they implicitly tuned to one model's output patterns?
- **Compound agent evaluation** — When Agent A feeds into Agent B, how do you isolate which agent caused a quality drop?
- **Eval dataset contamination** — As models train on more web data, your "golden dataset" test cases might end up in training data, inflating scores

## Sources

- [Demystifying Evals for AI Agents — Anthropic Engineering](https://www.anthropic.com/engineering/demystifying-evals-for-ai-agents)
- [A Pragmatic Guide to LLM Evals for Devs — Pragmatic Engineer](https://newsletter.pragmaticengineer.com/p/evals)
- [Agent Drift: Quantifying Behavioral Degradation — arXiv](https://arxiv.org/abs/2601.04170)
- [Why LLM Observability Needs Evaluations — LangChain](https://www.langchain.com/articles/llm-monitoring-observability)
- [AI Agent Evaluation: A Practical Framework — Braintrust](https://www.braintrust.dev/articles/ai-agent-evaluation-framework)
- [LLM Regression Testing Tutorial — Evidently AI](https://www.evidentlyai.com/blog/llm-regression-testing-tutorial)
- [LLMOps for AI Agents in Production — OneReach](https://onereach.ai/blog/llmops-for-ai-agents-in-production/)
- [Best LLM Monitoring Tools 2026 — Braintrust](https://www.braintrust.dev/articles/best-llm-monitoring-tools-2026)
- [CI/CD for Evals: Running Prompt & Agent Regression Tests — Kinde](https://www.kinde.com/learn/ai-for-software-engineering/ai-devops/ci-cd-for-evals-running-prompt-and-agent-regression-tests-in-github-actions/)
- [Promptfoo — GitHub](https://github.com/promptfoo/promptfoo)

---
tags: [agent-evals, monitoring, drift-detection, regression-testing, observability, LLMOps]
date_added:: 2026-04-15
last_updated:: 2026-04-15
