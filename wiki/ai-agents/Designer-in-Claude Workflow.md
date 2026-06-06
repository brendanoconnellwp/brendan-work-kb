# Designer-in-Claude Workflow

> A workflow that inverts who-writes-what in the design-to-dev pipeline: **designers prototype directly in Claude — animations, interactions, viewport-height layouts, smooth scroll — and dev WordPress-themes it after.** The handoff becomes a working static HTML/CSS file instead of a Figma spec the dev has to interpret.

## The shift

Today's typical handoff at a small web agency:

```text
Designer produces a Figma file with animations described in text or video
  ↓
Dev interprets the description into code — often imperfectly
  ↓
Iterations until the animation matches the designer's intent
```

The new proposed handoff:

```text
Designer prototypes the section in Claude — animation included, working code
  ↓
Dev converts the static HTML/CSS into a WordPress theme
  ↓
Mobile QA + production hardening loop in Claude using screenshots
```

The framing from an agency owner who proved this out: *"It's like when a designer has an idea, they have to explain it to the dev and then the dev writes the code and it's not perfect and whatever — I just feel like now the designer can just do it themselves. We just have to figure out the right workflow for that."*

## A proof case: prototyping a hero section

Here's a concrete walkthrough of the workflow working in practice. The agency owner built a full hero section — with no dev involvement until handoff:

| Step | What happened |
|---|---|
| 1 | Described the hero in Claude: viewport-height, parallax background extending into the section below, smooth-scroll feel, background color darkening with scroll progress. |
| 2 | Claude wrote it as static HTML/CSS, added a smooth-scroll library, added the parallax. |
| 3 | Asked Claude to copy a specific logo loading animation from a reference site — logo loads centered, moves up into position, rest of the page fades in. Claude wrote it. Took a couple tweaks to land it. |
| 4 | Sent the static build to the dev. The dev prompted Claude: *"make it a WordPress theme, these are the custom fields we'd need"* — Claude did it. |
| 5 | Owner installed MAMP locally, pulled the repo, QA'd on mobile by screenshotting into Claude: *"fix this on mobile"* — "it's keeping desktop as it should and making changes on mobile." |

The owner called this something the whole design team should adopt. "I've been doing this, I feel like it's something we really should have our designers do."

## Why this matters for each role

### For designers

Both common designer roles at a small agency benefit differently:

- **Design lead** (closer to execution) — prototype-in-Claude lets them test interaction patterns and animation choices against the actual brand tokens before any code lands. No waiting for a dev pass to know if the animation is buildable.
- **Design director** (closer to concept/brand) — Claude-as-prototype-partner is brainstorming with a working artifact, not just mockup stills. Faster iteration on the feeling of a layout.

### For devs

This is the thread where the dev's role visibly shifts. Today the front-end dev is often the bottleneck for animation translation. In the new model they're freed for harder work — QA, performance, hardening, accessibility — that designers can't (and shouldn't) be doing in Claude.

**Worth confirming with the dev before pitching this to the design team** — they need to see it as an upgrade of their role, not a threat to it.

### For the lead developer

This workflow depends on a reliable WordPress theming pattern. The dev's prompt-to-WP-theme works on a one-pager; on a real site, the conversion needs to encode agency WP conventions consistently — Sass partials, ACF field patterns, custom post types, security baseline. A custom WordPress skill or documented convention set is what makes the back half reliable.

## An insight from a freelance dev

One external developer working with an agency made an observation worth internalizing: *"For the stuff we do — because it's so custom — AI is still basically doing a lot of the initial stuff from scratch. But where AI really becomes valuable is in the QA."*

That's the bookend structure of this workflow:

- **AI strength #1 (designer-side):** Prototyping animations, interactions, and hero patterns from a description. *Front-loaded.*
- **AI strength #2 (dev-side):** QA on the built site — screenshot into Claude → fix mobile bug → refresh → loop. *Back-loaded.*

The middle (building a custom site from a designer's prototype into a maintainable production codebase) is still human dev work. You need both ends.

## Where the workflow breaks (and how to fix it)

### Problem 1: "Is the code any good?"

For a one-pager landing page: probably fine. For a real production site: needs convention enforcement. **Fix:** encode agency WordPress patterns into a reusable skill or prompt template so the WP theming step applies the right scaffold instead of Claude defaulting to generic patterns.

### Problem 2: Design system drift

If every designer prototypes in Claude independently, each section ends up using slightly different spacing, color tokens, and transition timing. **Fix:** a designer-side prompt or skill that encodes the agency's design system tokens — colors, typography scale, spacing rhythm, motion curves — so every prototype starts from the same foundation.

### Problem 3: Designer ↔ dev handoff QA

The prototype is static HTML/CSS. The dev has to translate it into ACF fields, custom post types, and dynamic content. **Fix:** standardize what the static prototype must include — markup conventions, class naming, CSS variable surface — so the dev's conversion is mechanical, not interpretive.

### Problem 4: Designers who aren't comfortable in code mode

Not every designer will want to work in Claude code output. **Fix:** start with one willing designer on one low-stakes project section. Document the friction points. Expand from there.

## Sequence to land this

1. **Don't pitch the whole design team yet.** Build the WordPress theming convention first so the back half is reliable.
2. **Pilot with one designer** on a low-stakes project section — a hero or a single component, not a whole site.
3. **Document the handoff template** the dev needs: markup conventions, class naming, what the prototype must include.
4. **Confirm with the dev** that their role shifts toward QA and hardening, not replacement.
5. **Then** propose the workflow as a standing agency pattern.

## Open questions

- Which designer goes first — the one who's more Claude-comfortable, or the most senior one whose buy-in validates it for the team?
- What's the right project type to pilot on? A single landing page or a hero section on a real project beats starting on a full site.
- Does this need a formal `design-prototype-guidelines.md` before piloting, or is a 1-pager brief enough to start?
- Tool choice: Claude Code vs. Claude.ai web vs. Cursor? Let the designer's comfort drive it.

## Connections

- [[Figma MCP Design-to-Code Pipeline]] — alternative pipeline (Figma-first); worth comparing approaches
- [[Claude-Powered Dev Workflows]] — Claude as dev partner reference
- [[WordPress Architecture Decision Framework]] — the WP convention layer this workflow depends on
- [[Agency Workflow Audit Framework]] — the broader handoff audit this sits inside

---
tags: [workflow, design, ai-assisted, agency-ai-role, designer-empowerment]
date_added:: 2026-05-26
last_updated:: 2026-06-05
