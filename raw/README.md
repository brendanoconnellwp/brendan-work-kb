# raw/

Source material for the wiki. Everything here is gitignored — lives on disk locally, never pushed to GitHub. The compiled `wiki/` articles are the output; this is the input.

```
articles/   Fetched web articles, papers, blog posts (.md with YAML frontmatter)
notes/      Personal research jottings, half-formed thoughts, link dumps
videos/     Curated video resource lists
clippings/  Obsidian web clipper output, bookmarks
images/     Screenshots, diagrams, reference images
daily/      End-of-day dumps (YYYY-MM-DD.md) — stream of consciousness, never edited
```

## Naming convention

Articles: `YYYY-MM-DD_slug.md`

Frontmatter:
```yaml
---
title: "Article Title"
source: "https://original-url"
date_fetched: 2026-01-01
topics: [topic1, topic2]
---
```
