---
aliases: [Claude Code for WordPress]
---
# Claude Code for WordPress

> Claude Code + WordPress Studio = describe what you want, get a working plugin or theme. The WordPress-specific AI workflow in 2026.

## Overview

WordPress development is one of the strongest use cases for [[Claude-Powered Dev Workflows]] because WordPress has well-defined patterns — hooks, filters, template hierarchies, REST API conventions, ACF field registration — that Claude Code can generate reliably. The combination of Claude Code with WordPress Studio (free local environment) means you can go from idea to working plugin in minutes.

As of WordPress 7.0 (April 2026), the PHP AI Client SDK is integrated into core, making AI-powered plugin development a first-class concern.

## The Setup

1. **Claude Code** — installed from anthropic.com, paid plan
2. **WordPress Studio** — free local environment from WordPress.com
3. Open terminal from WordPress Studio → type `claude` → trust the folder → start building

## What Claude Code Does Well for WordPress

### Plugin Development
- **Scaffold complete plugins** from natural language descriptions
- Generate proper WordPress plugin structure: main file headers, activation/deactivation hooks, admin pages
- Write hooks and filters that follow WordPress coding standards
- Build REST API endpoints with proper permission callbacks
- Generate ACF field group registrations
- Create custom post types and taxonomies with full admin UI

### Theme Development
- Generate block theme files: theme.json, block patterns, template parts
- Create classic theme templates following the template hierarchy
- Build ACF-powered flexible content layouts
- Generate Gutenberg custom blocks (both static and dynamic)

### The Claude Cowork Plugin
Anthropic's official WordPress integration. Describe the site you want → Claude generates a complete block theme → deploys to WordPress Studio. This is the "vibe code a whole site" workflow.

### Maintenance & Operations
- Write WP-CLI scripts for bulk operations
- Generate PHPUnit tests for plugins
- Create database migration scripts
- Build REST API clients for headless frontends
- Write PHPCS-compliant code by default when CLAUDE.md specifies standards

## CLAUDE.md for WordPress Projects

The key to consistent WordPress output is a project-level CLAUDE.md that defines your agency's standards:

```markdown
# WordPress Project Standards

## Architecture
- Theme type: [block/classic/headless]
- ACF Pro required: yes
- PHP version: 8.2+
- WordPress version: 7.0+

## Coding Standards
- Follow WordPress Coding Standards (PHPCS with WordPress-Extra ruleset)
- Use strict typing: declare(strict_types=1)
- Escape all output: esc_html(), esc_attr(), esc_url(), wp_kses_post()
- Sanitize all input: sanitize_text_field(), absint(), etc.
- Use nonces for all form submissions
- Use capabilities for all permission checks

## ACF Patterns
- Register field groups in PHP (not just GUI) for version control
- Use acf/init hook for registration
- Group fields by: [post-type]_[context] naming convention

## Block Development
- Use @wordpress/scripts for build toolchain
- Register blocks via block.json metadata
- Use render.php for dynamic blocks (not render_callback)

## Naming Conventions
- Plugin prefix: [agency-prefix]_
- Text domain: [project-slug]
- Function naming: [prefix]_[descriptive_name]
```

This CLAUDE.md means every developer on the team — and Claude itself — produces consistent, agency-standard WordPress code.

## WordPress-Specific Claude Skills

The open-source [claude-wordpress-skills](https://github.com/elvismdev/claude-wordpress-skills) repo provides professional skills for:
- Performance optimization
- Security auditing
- Gutenberg block development
- Theme/plugin best practices

These are reusable instruction sets that teach Claude how to do WordPress tasks correctly — the WordPress equivalent of the CLAUDE.md patterns.

## Where This Fits in the Agency Pipeline

```
Figma design
    → Figma MCP reads design data
        → Claude Code generates:
            - Block patterns (block theme) or
            - PHP templates (classic theme) or
            - React components (headless)
            - ACF field registrations
            - theme.json / design tokens
        → Developer reviews, refines, integrates
            → CI/CD deploys to staging
                → QA + client review
                    → Production
```

The developer's role shifts from writing WordPress code to **reviewing and directing** Claude's WordPress output. This is the same shift described in [[Claude-Powered Dev Workflows]] but with WordPress-specific patterns.

## Connections

- [[WordPress Architecture Decision Framework]] — architecture determines what Claude generates
- [[WordPress Agency Tech Stack]] — Claude plugs into the dev layer of the stack
- [[Claude-Powered Dev Workflows]] — WordPress is a specific instance of the broader dev workflow
- [[Figma MCP Design-to-Code Pipeline]] — the input layer for design-driven WordPress development
- [[Getting Agency Teams to Actually Use AI]] — WordPress devs may resist if they see Claude as replacing their craft

## Open Questions

- How well does Claude Code handle complex ACF flexible content layouts with nested repeaters?
- Can Claude reliably generate accessible, WCAG-compliant WordPress output?
- How does the Claude Cowork plugin compare to manual Claude Code workflows for theme generation?
- What's the right review depth for Claude-generated WordPress code — trust PHPCS or line-by-line?

## Sources

- [Build WordPress Plugins with AI — WordPress.com](https://wordpress.com/blog/2026/02/12/build-wordpress-plugins-with-ai-claude-code/)
- [Build WordPress Sites with AI: Claude Cowork Plugin](https://wordpress.com/blog/2026/02/13/new-plugin-and-skills-for-claude-cowork/)
- [claude-wordpress-skills — GitHub](https://github.com/elvismdev/claude-wordpress-skills)
- [AI WordPress Development: Automate Themes & Plugins](https://attowp.com/wordpress-development/ai-wordpress-development-automate-themes-plugins/)
- [Integrate Claude Code with WordPress — Seahawk](https://seahawkmedia.com/wordpress/how-to-integrate-claude-code-with-wordpress-guide/)

---
tags: [wordpress, claude-code, plugin-development, theme-development, ai-workflow]
date_added:: 2026-04-04
last_updated:: 2026-04-04
