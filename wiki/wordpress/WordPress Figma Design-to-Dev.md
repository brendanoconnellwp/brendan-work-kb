---
aliases: [WordPress Figma Design-to-Dev]
---
# WordPress Figma Design-to-Dev

> The design-to-dev handoff is where WordPress agencies lose the most time. Here's how the pipeline works in 2026 — and where it still breaks.

## Overview

The Figma-to-WordPress pipeline has historically been the biggest bottleneck in agency workflows. A designer creates pixel-perfect Figma comps, then a developer spends days manually translating them into WordPress templates — losing fidelity along the way. In 2026, the pipeline is getting dramatically better, but it's not magic. The approach depends entirely on your [[WordPress Architecture Decision Framework]].

## The Pipeline by Architecture

### Block Theme Pipeline (Best MCP Fit)
```
Figma design
  → Design tokens in Figma variables (colors, typography, spacing)
  → Figma MCP reads structured data
  → Claude Code generates:
      - theme.json (maps directly from Figma variables)
      - Block patterns (layout structure)
      - Template parts (header, footer)
      - Custom block styles
  → Developer refines in WordPress editor
  → Client can edit via Site Editor
```

**Why it works well:** theme.json is essentially a design token file. Figma variables → theme.json is a near-1:1 mapping. Auto Layout in Figma → CSS flexbox/grid in block patterns. This is the cleanest pipeline.

### Headless Pipeline (Most Powerful)
```
Figma design
  → Figma MCP reads component structure
  → Claude Code generates:
      - React/Next.js components
      - GraphQL queries (matching ACF field structure)
      - Tailwind/CSS from design tokens
  → ACF fields defined in WordPress admin
  → WPGraphQL auto-exposes fields
  → Frontend consumes API
  → Developer integrates and deploys
```

**Why it works well:** The frontend is pure modern web dev — no WordPress template constraints. Claude Code generates React components as effectively as any other framework. The ACF → WPGraphQL → Next.js pipeline is clean and well-documented.

### Classic Theme Pipeline (Most Manual)
```
Figma design
  → Designer exports specs (or Zeplin generates them)
  → Developer manually builds:
      - PHP template files
      - SCSS/CSS from design specs
      - ACF field groups for dynamic content
      - Template parts and includes
  → Multiple rounds of pixel-matching
  → Client edits content only (not layout)
```

**Why it's harder:** WordPress PHP templates don't map cleanly from Figma data. Claude Code can generate PHP templates, but WordPress-specific functions (the_post(), get_field(), wp_nav_menu()) require WordPress context that MCP doesn't provide. More manual translation needed.

## The ACF Bridge

Across all architectures, ACF Pro is the bridge between design and content:

1. **Designer** creates a layout with placeholder content areas
2. **Developer** defines ACF field groups that match those content areas
3. **Content** flows from WordPress admin → ACF fields → templates/API → rendered page

The handoff artifact should include: which parts of the design are static (code) vs. dynamic (ACF fields). This is the spec that's most often missing and causes the most rework.

**Pro tip:** Name ACF field groups to match Figma component names. If Figma has a "Hero Section" component with "heading", "subtext", and "cta_url" layers, the ACF field group should use those exact names. Claude Code can generate the field registration from this naming convention.

## The Handoff Cost (Data)

From [[Workflow Audit Deep Dive]]:
- 66% of designers spend 4-8 hours/week explaining designs to developers
- 65% of developers spend 4-8 hours/week interpreting designs

For WordPress specifically, common rework triggers:
- Responsive behavior not specified in Figma
- Hover/active/focus states missing
- ACF field structure not discussed before design phase
- Typography doesn't match available web fonts
- Spacing system in Figma doesn't match theme.json scale

## Figma File Standards for WordPress

If you're the person running the [[Agency Workflow Audit Framework]], push for these Figma standards:

1. **Variables for everything** — colors, typography, spacing as Figma variables (maps to theme.json)
2. **Auto Layout always** — maps to flexbox/grid, critical for MCP
3. **Component variants** — match WordPress block style variations
4. **Layer naming = CSS class naming** — `hero-section/content/heading` not `Frame 47`
5. **Responsive breakpoints defined** — at minimum: mobile, tablet, desktop
6. **Content areas annotated** — mark what's static code vs. what's an ACF field
7. **Interaction states** — hover, active, focus, disabled for every interactive element

## Connections

- [[WordPress Architecture Decision Framework]] — architecture determines the pipeline
- [[Figma MCP Design-to-Code Pipeline]] — the generic MCP guide, WordPress-specific application here
- [[Claude Code for WordPress]] — the AI layer generating WordPress code from design data
- [[WordPress Agency Tech Stack]] — Figma and Zeplin in the design layer
- [[Workflow Audit Deep Dive]] — handoff friction data and diagnostic approach
- [[Getting Agency Teams to Actually Use AI]] — designers need to adopt file standards for this to work

## Sources

- [Figma to WordPress for Agencies](https://figma2wp.com/figma-to-wordpress-for-agencies/)
- [Figma to WordPress: ACF Integration](https://figma2wp.com/figma-to-wordpress-implementing-advanced-custom-fields-acf/)
- [10 Figma Things Every WordPress Developer Should Know](https://www.raycreations.net/figma-developer-handoff-guide/)
- [How to Create a WordPress Website from Figma: 2026 Guide](https://figmentor.io/blog/figma-to-wordpress-conversion-complete-guide/)

---
tags: [wordpress, figma, design-to-dev, acf, handoff]
date_added:: 2026-04-04
last_updated:: 2026-04-04
