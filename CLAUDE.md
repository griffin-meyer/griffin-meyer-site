# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

Single-page portfolio site for Griffin Meyer, NYC video producer & cinematographer. No build system, no package manager, no framework — pure HTML/CSS/JS.

## Development

To preview locally, serve the directory over HTTP (direct `file://` access works for most features but can have CORS issues with some browsers):

```bash
python3 -m http.server 8080
# or
npx serve .
```

There are no build steps, linters, or test commands.

## Architecture

Three files compose the entire site:

- **`base.css`** — CSS reset and global defaults (box-sizing, typography normalization, `prefers-reduced-motion`, focus styles, `.sr-only`)
- **`style.css`** — Design tokens as CSS custom properties (`:root`) + all component styles. Light and dark themes are defined here via `[data-theme="light"]` / `[data-theme="dark"]` selectors.
- **`index.html`** — Full page markup plus all JavaScript in a single `<script>` block at the bottom, organized as self-invoking IIFEs per feature.

## Key Conventions

### CSS Design Tokens

All spacing, color, typography, and shadow values are CSS custom properties defined in `style.css`. Use existing tokens rather than hard-coded values:
- Spacing: `--space-1` through `--space-32` (4px base grid)
- Type scale: `--text-xs` through `--text-hero` (all `clamp()`-based, fluid)
- Colors: `--color-bg`, `--color-surface`, `--color-text`, `--color-primary`, etc.
- Transitions: `--transition-interactive` (200ms), `--transition-slow` (500ms)

### Theme System

The theme is controlled by `data-theme` on `<html>`. JavaScript manages the toggle state and persists it in the `d` variable (initialized to `'light'`). Critically, **logo images are swapped imperatively via JS** (`updateLogos()`) because the `<picture>`/`<source>` media queries handle `prefers-color-scheme` only — they do not react to the `data-theme` attribute. Both `#nav-logo-img` and `#hero-logo-img` must be updated together.

### Video Cards

Each card has `data-video-url` set to a YouTube embed URL (`/embed/ID?autoplay=1`). The lightbox opens an `<iframe>` with that URL. When the page is embedded in another iframe (`window.self !== window.top`), clicking a card converts the embed URL to a `watch?v=` URL and opens it in a new tab — this works around YouTube's block on three-level iframe nesting.

### Video Grid Layout

The grid uses 12 columns. Three card variants control span:
- `.video-card--featured` → span 8 (first/hero card)
- `.video-card--standard` → span 4
- `.video-card--wide` → span 6

Breakpoints collapse these to span 6 (tablet) and span 12 (mobile).

### Scroll Reveal

Elements with `.reveal` start invisible (`opacity: 0; transform: translateY(24px)`). An `IntersectionObserver` adds `.is-visible` when they enter the viewport. Staggered delays use `.reveal-delay-1` through `.reveal-delay-4`.

### Contact Form

The form submission is simulated — `e.preventDefault()` is called, the form is hidden, and `#form-success` is shown. There is no backend endpoint.

### Adding Video Cards

Copy an existing `<div class="video-card ...">` block in the `#video-grid` section. Set `data-video-url` to `https://www.youtube.com/embed/VIDEO_ID?autoplay=1`, update the `<img src>` to the corresponding YouTube thumbnail (`https://img.youtube.com/vi/VIDEO_ID/maxresdefault.jpg`), and add the appropriate `data-category` value. If introducing a new category, add a corresponding `.filter-btn` with a matching `data-filter` attribute.
