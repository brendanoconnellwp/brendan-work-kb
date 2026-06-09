---
aliases: [WordPress Agency Tech Stack]
---
# WordPress Agency Tech Stack

> The tools a high-end WordPress agency actually uses in 2026 — from local dev to production monitoring.

## Overview

A modern WordPress agency tech stack has more in common with a SaaS engineering team than with the "install themes and plugins" WordPress of 2015. Git-based workflows, CI/CD pipelines, staging environments, automated testing, and infrastructure-as-code are table stakes for agencies charging premium rates.

Understanding this stack is essential for [[What the Agency AI Role Actually Is]] — you need to know where AI tooling plugs into each layer.

## The Stack

### Local Development
| Tool | Purpose |
|---|---|
| **DevKinsta** / **Local by Flywheel** / **WordPress Studio** | Docker-based local WordPress environments |
| **WP-CLI** | Command-line automation — plugin installs, user management, database operations, migrations |
| **WP Migrate** | Move databases and media between environments without breaking serialized data |
| **Blueprints** | Reusable site recipes — PHP version, themes, plugins, settings. Instant consistent setup |

### Version Control & Code Review
- **GitHub / GitLab / Bitbucket** — repos, branches, PRs
- Branch strategy: `main` → `staging` → `feature-branches`
- PR-based code review enforced before merge
- **PHPCS** (PHP CodeSniffer) for WordPress coding standards
- **ESLint** for JavaScript

### CI/CD Pipeline
```
Push to branch → GitHub Actions triggers:
  1. Run PHPCS linting
  2. Run PHPUnit tests
  3. Compile SCSS/JS assets
  4. Deploy to staging (via Kinsta API)
  5. Run visual regression tests
  6. Post-deploy: cache purge, DB updates
```

This is where [[n8n for Agency Ops]] can add a layer — notifications to Slack, PM tool task updates, client preview link generation.

### Theme & Plugin Frameworks
| Framework | Use Case |
|---|---|
| **Sage + Bedrock** | Modern PHP — Blade templating, Composer dependencies, structured folders. The premium agency choice |
| **Underscores (_s)** | Lightweight starter theme for minimal builds |
| **Custom internal framework** | Mature agencies build proprietary: modular ACF blocks, Gutenberg components, pre-configured SEO/caching, headless-ready API layer |

### Design-to-Dev
| Tool | Role |
|---|---|
| **Figma** | Design, prototyping, component systems |
| **Figma MCP** | Direct design-to-code via [[Figma MCP Design-to-Code Pipeline]] |
| **Zeplin** | Dev-ready specs with spacing, colors, CSS snippets (for teams not yet on MCP) |

### Content Modeling
- **ACF Pro** — the backbone for custom content: flexible content, repeaters, options pages, field groups
- **Custom Post Types + Taxonomies** — structured content architecture
- **WPGraphQL** — API layer for headless builds, auto-exposes ACF fields

### Hosting & Infrastructure
| Component                     | Tool                                 |
| ----------------------------- | ------------------------------------ |
| **Managed WordPress hosting** | Kinsta / WP Engine / Pantheon        |
| **CDN**                       | Cloudflare (often bundled with host) |
| **Staging**                   | One-click staging environments       |
| **Edge caching**              | Host-level or Cloudflare             |
| **Headless frontend hosting** | Vercel / Netlify / Cloudflare Pages  |
| **Modern CMS pilots** | [[EmDash CMS]] for Astro/Cloudflare-native content sites where WordPress plugin compatibility is not the main value |

### Testing & QA
- **PHPUnit** — PHP unit testing
- **Jest** — JavaScript unit tests
- **Cypress** — end-to-end testing
- **Ghost Inspector / BrowserStack / LambdaTest** — visual regression, cross-browser testing
- **Query Monitor** — performance profiling in dev

### Operations & Client Management
| Function | Tools |
|---|---|
| Project management | Asana / ClickUp / Linear |
| Time tracking | Harvest / Toggl |
| Documentation | Notion / Confluence |
| Communication | Slack (with GitHub, deploy, and monitoring integrations) |
| Client reporting | Custom dashboards or tools like AgencyAnalytics |

## Where AI Plugs In (2026)

| Stack Layer | AI Tool | What It Does |
|---|---|---|
| Local dev | Claude Code + WordPress Studio | Generate plugins/themes from natural language |
| Design-to-dev | Figma MCP + Claude Code | Pull design data, generate WordPress components |
| Code review | Claude Code in CI/CD | Automated PR review, standards checking |
| Testing | Claude Code | Generate test cases from code |
| Content | Claude Cowork plugin | Generate block themes from descriptions |
| Deployment | n8n + Claude | Orchestrate multi-step deploy workflows with AI decision points |

## Connections

- [[WordPress Architecture Decision Framework]] — architecture choice determines which parts of this stack matter most
- [[Claude Code for WordPress]] — AI layer that sits on top of this stack
- [[Figma MCP Design-to-Code Pipeline]] — the design-to-dev segment
- [[n8n for Agency Ops]] — automation layer connecting these tools
- [[Agency Workflow Audit Framework]] — audit the stack to find automation opportunities

## Sources

- [Inside a Modern WordPress Agency Tech Stack — Kinsta](https://kinsta.com/blog/wordpress-agency-tech-stack/)
- [WordPress Automation Workflows for Agencies — Kinsta](https://kinsta.com/blog/wordpress-workflow-automation/)
- [Faster Local WordPress Dev Workflows for Agencies](https://wordpress.com/blog/2025/12/19/local-wordpress-dev-workflows-for-agencies/)
- [WordPress Development in 2026 — DeployHQ](https://www.deployhq.com/blog/wordpress-development-in-2025-from-full-site-editing-to-flawless-deployments)

---
tags: [wordpress, tech-stack, tooling, agency, devops]
date_added:: 2026-04-04
last_updated:: 2026-06-09
