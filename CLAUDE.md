# Brendan's Work KB

Personal knowledge base covering AI agents, dev workflows, WordPress development, and agency operations. Companion to [YouTube](https://www.youtube.com/@BrendanOConnellWP) and [brendan-oconnell.com](https://brendan-oconnell.com/).

## Directory structure

```
wiki/
  ai-agents/       # Agent patterns, evals, ecommerce AI, design workflows
  agency-ai-role/  # AI integrator methodology, second brain, onboarding frameworks
  cloudflare/      # Cloudflare AI platform, MCP, Workers
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
