# Brendan's Work KB

Personal knowledge base covering AI agents, dev workflows, WordPress development, and agency operations. Companion to [YouTube](https://www.youtube.com/@BrendanOConnellWP) and [brendan-oconnell.com](https://brendan-oconnell.com/).

## Directory structure

```
wiki/
  ai-agents/       # Agent patterns, evals, ecommerce AI, design workflows
  agency-ai-role/  # AI integrator methodology, second brain, onboarding frameworks
  cloudflare/      # Cloudflare AI platform, MCP, Workers
  digital-anchor/  # Digital Anchor positioning, buyer language, offer strategy
  design-to-dev/   # Design-to-code pipelines, workflow audits, SIPOC
  forecasts/       # AI scenario forecasts
  interests/       # Case studies and topics of personal interest
  team-adoption/   # Getting teams to actually use AI
  team-rag/        # RAG architecture, access control, ingestion pipelines
  tooling/         # n8n, cold email automation, workflow tools
  wordpress/       # WordPress AI dev patterns, Claude Code, Codex, Figma→WP
output/            # Generated artifacts, demos, session deliverables
raw/               # Source material (articles, notes, research) — local only, gitignored
```

## Protocols

**RESEARCH** — find high-quality sources, fetch and distill, save to `raw/articles/` with YAML frontmatter (title, source URL, date_fetched, topics).

**COMPILE** — create or update wiki articles from raw material. One concept per article. Use `[[wikilinks]]` to build connections. Always update `wiki/_meta/_index.md` after creating articles.

**QUERY** — consult the wiki first, do new research to fill gaps, file anything substantial back as a new article or update.

**OUTPUT** — save deliverables to `output/` with descriptive filenames.

## Article format

```markdown
# Title

> One-line summary.

## Overview
## Key Concepts
## Connections
## Open Questions
## Sources

---
tags: [tag1, tag2]
last_updated: YYYY-MM-DD
```

Write in a clear, engaging, slightly informal tone. Prioritize insight density over coverage. One concept per article, ~1500 words max before considering a split.

## Public repo hygiene

This repository is public and may be demoed in YouTube videos. Do not add private employer/client operational snapshots, sensitive project details, internal team names, credentials, or unpublished client information. Prefer public/generalizable agency patterns unless Brendan explicitly says the material is safe to publish.

## Tag conventions

Keep tags boring and reusable. Prefer existing canonical tags before creating variants:

- `ai-agents` not `agents`
- `ecommerce` not `e-commerce`
- `agency-ops` for agency operations/workflow material
- `workflow` for generic workflow patterns
- `code-mode` not `codemode`

Use specific tool/product tags (`cloudflare`, `wordpress`, `figma`, `mcp`, `n8n`, `notion`) when they are central to the article.
