# HyperFrames — Video from HTML, Driven by Agents

> Build short-form videos as HTML compositions using GSAP timelines, then render them to MP4 — all from an agent prompt.

## Overview

HyperFrames (by HeyGen) renders video from HTML. You write a composition as an HTML file with `data-*` timing attributes and a GSAP timeline, then the CLI renders it to MP4. The agent handles the full pipeline: capture the source material, write the storyboard, build frames, assemble, and render.

The pipeline: `npx hyperframes init` → capture assets → storyboard → build frame HTML → `npx hyperframes render` → MP4.

## Key Concepts

- **Compositions** — HTML files with `<template>…</template>` wrapping. Each frame is a sub-composition referenced by `data-composition-src`. GSAP timelines drive motion, registered at `window.__timelines["frame-id"]`.
- **Capture** — `npx hyperframes capture <url>` downloads screenshots, videos, fonts, and design tokens from a live site.
- **Frame presets** — shipped design systems (`broadside`, `cobalt-grid`, `code-editorial`, etc.) that get remixed onto captured brand tokens.
- **Workflow skills** — `/product-launch-video`, `/faceless-explainer`, `/motion-graphics`, `/general-video` route the brief through the pipeline.
- **Render** — `npx hyperframes render --skill=<workflow> --quality high` produces MP4. Uses headless Chrome + GPU for frame capture.

## Agent Workflow

1. `npx hyperframes init <project> --non-interactive --example=blank --skill=<workflow>`
2. Capture assets: `npx hyperframes capture <url> -o capture`
3. Choose a frame preset and remix brand tokens: `build-frame.mjs --preset <name>`
4. Write `STORYBOARD.md` (per-frame beats, VO, timing) and `SCRIPT.md` (locked narration)
5. Generate audio: `audio.mjs --script SCRIPT.md --storyboard STORYBOARD.md --provider kokoro`
6. Build per-frame HTML — either manually or by dispatching sub-agents per frame
7. Assemble: `assemble-index.mjs` stitches frames + audio into `index.html`
8. Validate: `npx hyperframes lint` → `npx hyperframes check`
9. Render: `npx hyperframes render --skill=<workflow> --quality high`

## Pitfalls

- **ID prefixes that start with digits** break `querySelectorAll` — subagents often use `#01-hook-the` selectors that fail at runtime. Fix with `[id='01-hook-the']` or `getElementById`.
- **SVG assets from captures** can be stubs (`<use xlink:href="#copy"/>`). Verify SVG files actually contain paths before referencing them.
- **Subagents time out** easily on frame-building (600s limit). For simple frames, writing the HTML directly is faster than dispatching.
- **Captured video paths** may differ between `capture/assets/videos/` and `assets/`. The assemble-index hoists approved frame-videos.
- **No HeyGen credential** means no BGM and limited TTS. Kokoro TTS works offline for voice.

## Connections

- [[Claude Code for WordPress]] — same agent-loop pattern, different output format
- [[Claude-Powered Dev Workflows]] — Claude Code builds the compositions, HyperFrames renders them
- [[Second Brain Gameplan]] — this note lives in the operational layer

## Open Questions

- Can HyperFrames do >30s narrative pieces well, or is it best for short promos?
- How does the quality compare to CapCut / Premiere for client-facing delivery?
- Is there a way to feed custom music (not HeyGen catalog) for BGM?

## Sources

- HyperFrames docs: https://hyperframes.heygen.com
- CLI: `npx hyperframes --help`

---
tags: [tooling, ai-agents, video, workflow]
last_updated: 2026-07-28