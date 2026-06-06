---
aliases: [WordPress Architecture Decision Framework]
---
# WordPress Architecture Decision Framework

> Classic, block, headless, or hybrid? The answer depends on the client, the content, and the team — not on what's trendy.

## Overview

In 2026, WordPress sites can be built four fundamentally different ways. A high-end agency needs to pick the right architecture per project, not default to one approach. This decision has massive downstream effects on the [[Agency Workflow Audit Framework]] — it determines what the design-to-dev pipeline even looks like.

## The Four Architectures

### 1. Classic Theme (PHP Templates)
**What it is:** Traditional WordPress. PHP template files (header.php, page.php, single.php), template hierarchy, hooks and filters. The way WordPress has worked for 20 years.

**When to use it:**
- Complex portals with heavy PHP logic in views
- Advanced display conditions, custom query loops
- Client who'd break things with full site editing access
- Team deeply experienced in classic WordPress PHP
- Projects using page builders (Elementor, Divi) — still work better on classic structure
- Legacy sites being maintained or extended

**Design-to-dev implications:** Designer produces Figma comps → developer manually translates to PHP templates + CSS. Most handoff friction lives here. [[Figma MCP Design-to-Code Pipeline]] has limited utility because the output needs WordPress-specific PHP, not just HTML/CSS.

### 2. Block Theme (Full Site Editing / FSE)
**What it is:** WordPress's current direction. HTML-based templates, block markup, theme.json for global styles. Client edits headers, footers, templates directly in the editor.

**When to use it:**
- Corporate sites, blogs, portfolios
- Maximum performance (no page builder overhead)
- Future-proofing — this is where WordPress core investment goes
- Client wants self-service editing of layout, not just content
- New projects with no legacy constraints

**Design-to-dev implications:** theme.json maps closely to design tokens (colors, typography, spacing). This is the most natural fit for [[Figma MCP Design-to-Code Pipeline]] because design variables → theme.json → block patterns is a clean pipeline. Claude Code can generate block patterns and theme.json from design specs effectively.

### 3. Headless WordPress (API + Separate Frontend)
**What it is:** WordPress as a content API only. Frontend built in Next.js, Nuxt, Astro, etc. Content delivered via REST API or WPGraphQL.

**When to use it:**
- Performance-critical sites (sub-second loads, perfect Core Web Vitals)
- Multi-channel content delivery (web + app + kiosk + email)
- Team has strong React/Next.js skills
- Complex interactive frontends that exceed block editor capabilities
- Enterprise clients who need CDN-edge rendering

**The stack:**
| Layer | Technology |
|---|---|
| CMS Backend | WordPress on managed host (WP Engine, Kinsta), not public-facing |
| API | WPGraphQL (preferred) or REST API |
| Content Modeling | ACF Pro → fields exposed via WPGraphQL |
| Frontend | Next.js with ISR (Incremental Static Regeneration) |
| Hosting | Vercel / Netlify / Cloudflare Pages for frontend |

**Design-to-dev implications:** The richest pipeline. Figma → MCP → Claude Code → React components → Next.js pages. The frontend is pure modern web dev, so all the [[Claude-Powered Dev Workflows]] apply directly. ACF fields define the content model, WPGraphQL exposes it, Next.js consumes it.

**The tradeoff:** 2x the infrastructure (WordPress backend + frontend app). 8-12 week realistic timeline vs. 4-6 for traditional. Higher ongoing maintenance. But 80% of performance/flexibility gains at 30% of the cost of a fully custom CMS.

### 4. Hybrid (Best of Both)
**What it is:** Classic or block theme that adds selective headless capabilities. Use WordPress for most pages, but pull content via API for specific interactive sections or external apps.

**When to use it:**
- Mostly content-focused site with a few dynamic/interactive sections
- Team comfortable with WordPress PHP but wants modern JS for specific features
- Progressive modernization of an existing site
- Budget doesn't justify full headless rebuild

**Practical example:** Classic PHP theme with theme.json support (gets Gutenberg color palette and typography), REST API endpoints for a React-powered search or filtering component, static WordPress for everything else.

## The Decision Tree

```
Does the client need multi-channel content delivery?
├── YES → HEADLESS
└── NO → Does the site have complex interactive UI beyond what blocks handle?
    ├── YES → Is the budget there for headless infra?
    │   ├── YES → HEADLESS
    │   └── NO → HYBRID
    └── NO → Is there legacy classic theme code to maintain?
        ├── YES → CLASSIC (or hybrid migration path)
        └── NO → BLOCK THEME
```

## ACF Pro Across All Architectures

ACF Pro is the constant across all four architectures. It's how agencies model content beyond basic posts/pages:

| Architecture | ACF Role |
|---|---|
| Classic | PHP functions (get_field, the_field) in templates |
| Block | ACF blocks registered as Gutenberg blocks |
| Headless | Fields exposed via WPGraphQL or REST API, consumed by frontend |
| Hybrid | Mix of PHP access and API exposure |

**Key 2026 update:** WPGraphQL for ACF plugin automatically exposes ACF field groups in the GraphQL schema — no manual registration needed. Your ACF schema *becomes* your GraphQL schema.

## WordPress 7.0 (April 2026)

Arriving imminently — key changes for agencies:
- **PHP AI Client SDK in core** — infrastructure for AI plugins built into WordPress itself
- **Abilities API** — fine-grained capability tokens for frontend API access (replaces ad-hoc auth)
- Claude Code WordPress skills are production-ready for plugin and theme generation

## Connections

- [[WordPress Agency Tech Stack]] — the tools that support each architecture
- [[Claude Code for WordPress]] — how Claude fits into WP development specifically
- [[Figma MCP Design-to-Code Pipeline]] — block themes and headless are the best fit for MCP
- [[Agency Workflow Audit Framework]] — architecture choice determines what the pipeline looks like
- [[What the Agency AI Role Actually Is]] — recommending the right architecture per project is part of the role

## Open Questions

- How do you migrate a classic theme agency to block themes without disrupting active projects?
- What's the break-even point where headless infrastructure cost is justified by performance gains?
- How does WordPress 7.0's AI SDK change the plugin development workflow?
- Should ACF Pro be replaced by native WordPress custom fields now that core is catching up?

## Sources

- [Headless WordPress: Still Valid in 2026 — Contra Collective](https://contracollective.com/blog/headless-wordpress-valid-architecture-2026)
- [WordPress in 2026: Traditional, Headless, Static or Hybrid?](https://www.zebedeecreations.com/blog/wordpress-in-2026-traditional-headless-static-or-hybrid/)
- [Block Themes vs Classic Themes: FSE Guide 2026](https://wppoland.com/en/classic-vs-block-themes-fse-guide/)
- [Headless WordPress 2026 Build-to-Deploy Guide](https://forminit.com/blog/headless-wordpress-2026-guide/)
- [ACF + WPGraphQL for Headless WordPress](https://wpengine.com/resources/decode-2023-headless-acf-wpgraphql/)

---
tags: [wordpress, architecture, headless, block-theme, classic-theme, acf, decision-framework]
date_added:: 2026-04-04
last_updated:: 2026-04-04
