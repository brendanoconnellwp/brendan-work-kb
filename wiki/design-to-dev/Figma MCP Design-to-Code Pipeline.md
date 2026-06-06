---
aliases: [Figma MCP Design-to-Code Pipeline]
---
# Figma MCP Design-to-Code Pipeline

> The Figma MCP integration turns design handoff from a lossy game of telephone into a structured data pipeline.

## Overview

As of early 2026, Figma's MCP (Model Context Protocol) server lets AI agents — particularly Claude Code — read live design data directly from Figma files. This isn't screenshot-based guessing. The agent accesses the actual design structure: variables, tokens, components, variants, auto layout rules, and layer hierarchy.

This fundamentally changes design-to-dev handoff. Instead of a designer exporting specs and a developer interpreting them, the AI reads the design as structured data and generates code that maps directly to the design intent.

## Three Workflows

### 1. Design → Code
The primary workflow. A developer gives Claude a Figma frame URL. Claude pulls structured context via MCP — layout rules, spacing, colors as variables, component structure — and generates production code. This is dramatically better than screenshot-based generation because it understands *intent*, not just pixels.

### 2. Code → Canvas
Reverse flow. Take a rendered interface from the browser and import it back into Figma as editable frames. Useful for: syncing after dev iterations, getting design feedback on implemented work, or documenting what was actually shipped.

### 3. Write → Canvas
Claude creates and edits Figma content directly — frames, components, variants, variables, auto layout. Useful for: rapid prototyping, generating variations, or building out design system components programmatically.

## Two MCP Server Options

| | Official Figma MCP | Framelink MCP |
|---|---|---|
| Cost | Requires Dev seat | Free |
| Tools | get_design_context, get_screenshot + 3 more | get_figma_data, download_figma_images |
| Code Connect | Yes — maps Figma components to code | No |
| Best for | Teams already on Figma Dev | Quick setup, any account |

## Making It Work in an Agency

The MCP is only as good as the Figma file structure. This is where the [[Agency Workflow Audit Framework]] matters — you need to establish standards *before* plugging in AI:

**Non-negotiable file hygiene:**
- Use Figma variables for all colors, spacing, typography (not raw values)
- Auto Layout on everything — direction, alignment, padding, gap map directly to CSS flex
- Components with proper variant states (hover, active, disabled, etc.)
- Clean, hierarchical layer naming — "Header/Nav/Logo" not "Frame 47"
- Export design tokens as JSON via plugins (Open Variable Visualizer)

**The uncomfortable truth:** Most agency Figma files aren't ready for MCP. The audit phase of [[What the Agency AI Role Actually Is]] will almost certainly involve cleaning up Figma practices before the MCP integration delivers real value.

## Connections

- [[What the Agency AI Role Actually Is]] — this is the technical centerpiece of the design-to-dev transformation
- [[Claude-Powered Dev Workflows]] — Claude Code is the consumer of MCP data
- [[Getting Agency Teams to Actually Use AI]] — designers need to buy into file hygiene standards

## Open Questions

- How much cleanup is realistic to ask of a design team mid-project vs. starting fresh?
- Does Code Connect (mapping Figma components to actual codebase components) work well enough for production use?
- What's the quality gap between MCP-generated code and hand-written code for complex interactive components?

## Sources

- [Structuring Figma Files for MCP](https://blog.logrocket.com/ux-design/design-to-code-with-figma-mcp/)
- [Figma × Claude Code Integration Guide](https://claudelab.net/en/articles/claude-code/figma-mcp-integration)
- [From Claude Code to Figma — Figma Blog](https://www.figma.com/blog/introducing-claude-code-to-figma/)
- [A Better Figma MCP](https://cianfrani.dev/posts/a-better-figma-mcp/)

---
tags: [figma, mcp, design-to-code, claude, tooling]
date_added:: 2026-04-02
last_updated:: 2026-04-02
