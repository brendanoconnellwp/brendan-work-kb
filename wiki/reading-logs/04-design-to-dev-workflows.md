---
tags: [reading-log, design-to-dev, figma, wordpress, workflows, agency-ai]
date_added:: 2026-06-17
last_updated:: 2026-06-17
status:: complete
---

# Reading Log: Agency AI Workflows — Design-to-Dev Pipeline (2025–2026)

*What's real, what's hype, and what WordPress agencies should actually build.*

---

## 1. Executive Summary

The biggest design-to-dev opportunity for agencies isn't full automation — it's **removing translation friction.** The real value is in:
- **Token automation** — automatically extracting design tokens from Figma and generating theme.json, CSS custom properties, or Tailwind config
- **Component generation via vision-capable LLMs** — Claude/GPT-5 reading designs and generating semantic HTML/CSS
- **Human-in-the-loop code review** — AI reviewing the output for consistency with the design system

Full "one-click design to production code" doesn't exist at production quality. Anyone selling it is lying. But the toolchain is good enough to eliminate 40-60% of the busywork in the handoff.

---

## 2. Figma MCP — The Real Breakthrough

Figma MCP lets LLMs read Figma node trees and variable collections directly. **This is real and working.** Reliability with organized files: ~9/10.

### What It Can Do (Production-Ready)
- Read node trees, frames, components, and their properties
- Extract fill colors, text styles, spacing, corner radii
- Get component hierarchy and instances
- Export nodes as PNG/SVG
- Download design system component definitions

### What It Cannot Do (Still Aspirational)
- Bidirectional sync (Figma → code → back to Figma)
- Auto-generate responsive variants from one frame
- Extract semantic intent ("this is a hero section") vs. just geometry

### The Gating Factor: Figma Variables

Figma MCP's output quality directly depends on whether the **design team uses Figma Variables**. If colors are hardcoded hex values, the MCP can still extract them, but if they're using variable aliases (`$color-primary`, `$space-md`), the output becomes immediately usable as design tokens. **Garbage in = garbage out.**

---

## 3. The Three Tiers of Design-to-Dev Automation

### Tier A: Spec Generation ✅ **Highest ROI, works today**
Take a Figma frame and generate a detailed implementation spec:
- Layout structure (flexbox/grid dimensions)
- Typography stack with sizes, weights, line-height
- Color assignments (mapped to design system tokens)
- Spacing and alignment
- Breakpoint behavior

**Time savings:** 30-60 minutes per section → 2-3 minutes.

### Tier B: Component Extraction ✅ **Medium effort, high ROI**
Generate semantic HTML/CSS for individual components from Figma frames. Best for:
- Marketing pages and static sections
- Hero layouts
- Card grids
- Simple forms

**Time savings:** 60% reduction in component build time.

### Tier C: Full Page Generation ⚠️ **Variable ROI**
One-shot full page generation. **Best for marketing pages, worst for complex functionality.** Fails on:
- Logic-dependent states (logged in, empty cart)
- Dynamic content from CMS
- Interaction patterns beyond basic hover/click
- Responsive breakpoints across complex layouts

---

## 4. Tools That Actually Work (Mid-2026)

### Tier 1: Production-Ready

| Tool | Best For | Limitation |
|------|----------|------------|
| **Pencil** | Best AI-native design tool. Real vector editing. | Requires running the Pencil app |
| **v0.dev** | Text-to-UI generation, React/Tailwind | React only, not WordPress-native |
| **Claude + MCP** | Most flexible. Best for WordPress. Reads Figma via MCP, generates any framework | Requires setup and prompt engineering |

### Tier 2: Promising but Limited

| Tool | Best For | Limitation |
|------|----------|------------|
| **Cursor with MCP** | Code generation inside an IDE | Needs manual context setup |
| **Locofy** | Figma → React/Next.js + Tailwind | WP output requires manual adaptation |
| **Anima** | Figma → HTML/CSS with basic responsiveness | Output quality variable |

### Tier 3: Aspirational

One-click Figma-to-WordPress tools don't exist at production quality. Anyone claiming otherwise hasn't shipped real client work.

---

## 5. Practical Patterns for WordPress Agencies

### Pattern 1: Design Token → theme.json Pipeline
```
Figma Variables → MCP Extraction → JSON transform → theme.json + CSS custom properties
```

WordPress's `theme.json` and `styles.createStyleVariation()` are perfectly suited for this. Extract color palette, font families, spacing scale, and corner radii — auto-generate the settings JSON.

**Implementation:** Weekly token sync cron job. When Figma variables change, regenerate theme.json and create a draft PR.

### Pattern 2: Block Pattern Generation from Figma Sections
```
Figma section frame → MCP read → Claude generates block markup + CSS → Register as pattern
```

WordPress block patterns (`wp_register_block_pattern`) are just PHP arrays with HTML. A section frame from Figma can be converted into a reusable pattern with consistent class names.

**Time savings:** 45 minutes per pattern → 5 minutes of review.

### Pattern 3: AI-Augmented CSS Audit & Refactoring
```
Figma MCP extracts actual design specs → Compare against current site CSS → Generate diff
```

For maintenance/refactoring clients: compare what the design says against what's in the actual CSS. Find drift. Suggest fixes.

### Pattern 4: Component-Level Code as Gutenberg Block Starting Point
```
Figma component → MCP → Claude generates block.json + edit.js + style.css skeleton
```

Not a full production block. A **starting point** that a human developer completes. Saves the boilerplate scaffold phase.

---

## 6. Designer-in-Claude Workflow Patterns

Ryan named this one on a call — designers prototype interactions in Claude, devs build the WordPress theme after.

### The Pattern
1. Designer describes interaction in plain language: "On hover, this card lifts 4px with a soft shadow, and the image inside zooms 5% over 400ms with ease-out."
2. Claude generates a working HTML/CSS prototype in real-time
3. Designer iterates: "Make the lift 2px, add a 1ms delay before the image starts zooming"
4. Final prototype = executable spec
5. Handoff to dev: "Here's the CSS. Here's the interaction spec. Don't re-derive it — build this."

**This works today.** It changes the handoff from subjective ("make it feel premium") to measurable ("easing = cubic-bezier(0.16, 1, 0.3, 1), duration = 400ms, transform = translateY(-4px) + shadow").

### WordPress-Specific Adaptation
1. Raw CSS from Claude goes into a `design-audit` folder or comment in the WP child theme
2. Dev adapts the CSS animation for Gutenberg's block wrapper selectors
3. Claude can help translate: "Rewrite this vanilla CSS to use WordPress block editor body classes"

---

## 7. What's Real vs. Still Aspirational

| Workflow | Reality Level | Notes |
|----------|--------------|-------|
| Token extraction from Figma | 9/10 real | Depends on Figma Variables usage |
| Spec generation from designs | 9/10 real | Best ROI. Use Claude + MCP |
| Component CSS generation | 8/10 real | Great starting point, needs human review |
| Full page generation | 6/10 | OK for marketing pages, bad for complex |
| Responsive breakdowns | 5/10 | AI struggles with breakpoint logic |
| One-click Figma→WordPress | 2/10 | Doesn't exist at production quality |
| Bidirectional sync (Figma↔code) | 1/10 | Aspirational |
| Automated ACF field groups from Figma | 2/10 | Manual mapping still required |

---

## 8. Recommended Stack for Article

### Immediate (1-2 Months)
- Adopt Figma Variables across design team
- Set up Figma MCP for token extraction
- Build prompt library: 5-10 prompts for common WP patterns
- Weekly token → theme.json sync cron job

### Medium-Term (3-6 Months)
- Create Article starter theme with AI-ready conventions
- Train designers on "Designer-in-Claude" interaction spec workflow
- Evaluate Pencil for initial design exploration
- Build block pattern generator from approved designs

### Long-Term (6-12 Months)
- AI review integrated into QA process
- Automated design drift detection
- Bidirectional tooling as ecosystem matures

---

## Key Takeaway

The design-to-dev pipeline in 2026 isn't about replacing developers. It's about **eliminating the translation step.** The single highest-ROI change: get your design team to use Figma Variables, set up MCP extraction, and auto-generate theme.json. That alone will save hours per project and prevent the "the colors don't match the design" conversation forever.