# EmDash CMS

> Cloudflare's Astro-native CMS: a serious early attempt to rebuild the useful parts of WordPress around TypeScript, serverless deployment, safer plugins, and AI-operable content workflows.

## Overview

EmDash is a new open-source CMS from Cloudflare, announced April 1, 2026 as a "spiritual successor to WordPress." The useful read is not "WordPress killer." It is more specific: **WordPress-style publishing and extensibility, rebuilt for Astro, TypeScript, Cloudflare, and agents.**

It keeps familiar CMS concepts — posts/pages, custom content types, taxonomies, menus, widgets, admin UI, media library, plugins — but changes the base architecture. EmDash runs as an Astro integration inside the site, stores content in SQL, serves edits at runtime through Astro Live Content Collections, and gives developers type-safe query functions instead of `WP_Query`.

As of the 2026-06-06 research pass, the docs are much more substantial than launch-day positioning: the repo has 73 MDX docs pages, the main package is at `0.17.2`, and the documentation covers setup, architecture, plugins, Cloudflare deployment, AI/MCP tooling, and WordPress migration.

## Key Concepts

### Astro-native, not headless

EmDash is explicitly **not** a separate headless CMS. It runs in the same Astro deployment as the site. The frontend and content engine are one project, not a SaaS CMS plus a frontend consuming an API.

That means:

- Developers write normal Astro pages/components.
- Editors use `/_emdash/admin`.
- Pages query content with `getEmDashCollection()` and `getEmDashEntry()`.
- Content updates are served at runtime, so they appear immediately without rebuild webhooks.

### Portable deployment

The cleanest path is Cloudflare Workers + D1 + R2, but the docs present it as cloud-portable:

- Database: SQLite, libSQL, Cloudflare D1, or PostgreSQL
- Media: local filesystem, Cloudflare R2, or S3-compatible storage
- Runtime: Cloudflare Workers or Node.js

On Cloudflare, D1/R2 bindings live in `wrangler.jsonc`, migrations run automatically on first request after deploy, and production should set `EMDASH_ENCRYPTION_KEY` for plugin secrets.

### Visual content modeling plus TypeScript

Collections and fields can be created in the admin panel. Non-developers can model content without editing code. Developers can then generate types:

```bash
npx emdash types
```

That gives autocomplete and type checking from content model to Astro templates. This is the core developer-experience promise: editor-friendly modeling without giving up typed code.

### Plugin model is the real wedge

EmDash's strongest architectural claim is plugin safety. WordPress plugins generally run in-process with broad database/filesystem/user access. EmDash splits plugins into two formats:

- **Sandboxed plugins** — recommended for third-party plugins; run in an isolated runtime with declared capabilities and resource enforcement.
- **Native plugins** — trusted first-party code; run in the host process and can do deeper things like React admin pages, Portable Text components, and page fragments.

That distinction matters. EmDash is not saying "plugins are safe because JavaScript." It is saying third-party extension code should have explicit capabilities instead of default total trust.

### AI/MCP is first-class

EmDash includes a built-in MCP server at:

```text
/_emdash/api/mcp
```

It is disabled by default and enabled in config:

```js
emdash({ mcp: true })
```

The MCP server exposes content operations to assistants like Claude, ChatGPT, Cursor, VS Code, and Windsurf. The reference says it has 45 tools across content, schema, media, search, taxonomies, menus, revisions, and settings.

This is the part that makes EmDash more than a modern CMS wrapper. It is designed so an AI assistant can safely draft, edit, compare, schedule, publish, manage taxonomies, update settings, and inspect schema through scoped auth and RBAC.

### WordPress migration exists, but it is not lift-and-shift

EmDash has a WordPress importer and concept mapping:

| WordPress | EmDash |
|---|---|
| Custom Post Types | Collections |
| `WP_Query` | `getEmDashCollection()` |
| `get_post()` | `getEmDashEntry()` |
| Categories/Tags | Taxonomies |
| `the_content()` | `<PortableText />` |
| Shortcodes/blocks | Portable Text custom blocks |
| `add_action/filter()` | Plugin hooks |
| `wp_options` | `ctx.kv` |
| `wp_postmeta` | Collection fields |
| Theme directory | `src/` |
| `functions.php` | `astro.config.mjs` + EmDash config |

The importer supports WXR uploads, WordPress.com import, and REST API probing. Gutenberg blocks convert to Portable Text; unknown blocks become `htmlBlock` for review. Custom fields and ACF are analyzed and type-inferred, though repeaters/flexible content may land as JSON unless explicitly remodeled.

Important caveat: EmDash does **not** run WordPress plugins or PHP. Migration means porting concepts and content, not moving an existing WordPress stack over unchanged.

## Why This Matters

For Brendan's world — WordPress, Astro, Cloudflare, agents, and agency work — EmDash sits right at the intersection.

The practical opportunity is not to pitch it as production-ready replacement for every WordPress site. The opportunity is to evaluate whether its architecture solves problems agencies actually have:

- Plugin sprawl and plugin security review
- Messy custom post/meta structures
- Desire for Astro/TypeScript instead of PHP theme work
- Cloudflare-native hosting/deployment
- Editorial workflows where AI assistants can operate through scoped tools
- Smaller client sites where the plugin ecosystem is not the main value

The short agency read: **EmDash is a strong candidate for pilots, internal tools, microsites, content sites, and selected WordPress migrations — not yet the default platform for complex client sites.**

## Evaluation Plan

A useful first pilot should be small and concrete:

1. Create a local EmDash project with `npm create emdash@latest`.
2. Test the setup wizard and passkey auth at `/_emdash/admin`.
3. Create a realistic content model: posts, pages, categories, maybe case studies or team members.
4. Generate TypeScript types and build a few Astro routes.
5. Deploy to Cloudflare Workers with D1 and R2.
6. Enable MCP locally and test assistant workflows against draft content only.
7. Run a WordPress WXR import from a simple site and inspect Gutenberg/ACF/media/redirect behavior.
8. Try one sandboxed plugin and one native plugin to understand the trust boundary.

If that goes well, the next useful test is not another demo. It is a real low-risk migration with messy enough content to expose importer and workflow limits.

## Open Questions

- How mature is the plugin marketplace beyond first-party/test plugins?
- How well does the WordPress importer handle real agency mess: ACF, custom Gutenberg blocks, SEO plugins, redirects, media libraries, and weird legacy content?
- How stable are APIs before 1.0?
- Does the MCP server feel safe in real editorial use, or is it too powerful without careful approval flows?
- What are the D1/Postgres limits for content-heavy sites?
- Will Cloudflare keep investing, or is this an architectural experiment?

## Connections

- [[Project Think Agents as Infrastructure]] — both frame Cloudflare infrastructure as the substrate for durable agent-era software.
- [[Cloudflare MCP Enterprise Reference Architecture]] — EmDash's built-in MCP server is a concrete CMS/content example of scoped tool access.
- [[Cloudflare AI Platform Unified Inference Layer]] — EmDash belongs in the same Cloudflare pattern: content, tools, and inference becoming agent-operable infrastructure.
- [[WordPress Agency Tech Stack]] — EmDash belongs in the "modern alternatives / pilot candidates" bucket, not as a default replacement yet.
- [[WordPress Architecture Decision Framework]] — useful comparison point when deciding WordPress vs Astro/static/headless/EmDash.
- [[Second Brain Gameplan]] — EmDash's MCP server is another example of content systems becoming agent-operable, not just human-operated.

## Sources

- Cloudflare launch post: https://blog.cloudflare.com/emdash-wordpress/
- EmDash GitHub repo: https://github.com/emdash-cms/emdash
- Official docs: https://docs.emdashcms.com/
- Docs source inspected: `emdash-cms/emdash/docs/src/content/docs`
- Practical third-party guide: https://emdashcms.dev/
- Research note: `raw/articles/2026-06-06-emdash-cms-research.md`

---
tags: [cloudflare, emdash, cms, wordpress, astro, mcp, ai-agents]
last_updated: 2026-06-09
