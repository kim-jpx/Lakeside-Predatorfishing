# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Lakeside Predatorfishing is a static single-page marketing site for a fishing guide service in Karasjok, Norway. It is hosted on GitHub Pages at `lakesidepredatorfishing.com` (see CNAME).

## Architecture

The entire site lives in a single `index.html` file (~2460 lines) containing:

- **Inline CSS** (lines 14–1696): All styles are embedded in a `<style>` block. No external stylesheets.
- **HTML body** (lines 1699–1920): Two horizontally-sliding pages managed by a `.page-slider` / `.page-track` system.
- **Inline JavaScript** (lines 1922–2458): All logic is in a single `<script>` block. No build tools, bundlers, or frameworks.

### Page Structure

1. **Landing page** (`#landing`): Hero with intro video overlay, arched SVG title, tagline, service cards (Day Trip / Private Trip / Seasonal Trip), CTA buttons, and bottom bar.
2. **Photo gallery page** (`.gallery-page`): Tile grid of photos with Facebook/WhatsApp social links.

Pages slide horizontally via `translateX` on `.page-track`, controlled by arrow buttons, dot indicators, keyboard arrows, and touch swipe.

### Key Patterns

- **Intro video overlay**: Full-screen video (`intro.webm`/`intro.mp4`) with poster fallback, skip button, and `sessionStorage`-based "already seen" logic.
- **Content switching**: Service cards toggle inline content slides (`content-default`, `content-guided`, `content-booking`, `content-lessons`) with slide animations. Clicking an active card returns to default.
- **i18n**: English/Swedish translations stored in a `translations` object. Language switcher buttons call `applyLanguage()` which updates all text, aria labels, meta tags, and `<html lang>`.
- **Visual effects**: Aurora gradient animation, grain overlay (SVG noise), and background image dimming — all CSS-driven.
- **Idle nudge**: After 12 seconds on the default view, service card icons get a bounce/shimmer animation to encourage interaction.

### Design Tokens (CSS Custom Properties)

```
--fjord-blue: #1F3A44    --moss-green: #5F7D6A
--mist-grey: #F2F5F4     --warm-rope: #C8A96A
--charcoal: #2A2E2F
```

Spacing scale: `--space-2xs` (0.25rem) through `--space-3xl` (4.5rem).
Fonts: **Teko** (headings), **Exo 2** (body) via Google Fonts.

## Development

No build step — open `index.html` directly in a browser or use any local HTTP server. All assets (images, videos) are in the repo root.

## Testing

Use the Playwright MCP tools (all pre-allowed in `.claude/settings.json`) for visual inspection. Start a local server with `npx http-server -p 8765` and navigate to `http://localhost:8765`.
