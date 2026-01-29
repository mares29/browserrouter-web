# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Commands

```bash
npm run dev      # Start dev server at localhost:4321
npm run build    # Build for production
npm run preview  # Preview production build locally
npm run deploy   # Build and deploy to Cloudflare Pages
```

## Architecture

Marketing website for BrowserRouter (macOS menu bar app). Built with Astro 5.x, deployed to Cloudflare Pages as static site.

**Key files:**
- `astro.config.mjs` — Static output mode, Cloudflare adapter, site URL
- `src/layouts/BaseLayout.astro` — Root layout with meta tags, OG/Twitter cards, JSON-LD schemas
- `src/styles/global.css` — Design tokens (CSS custom properties), light/dark mode via `prefers-color-scheme`
- `src/content/config.ts` — Blog collection schema (title, date, description)

**Design system:**
- Colors: `--white`, `--cream`, `--stone`, `--ink`, `--accent` (warm gold #C4A77D)
- Fonts: Cormorant Garamond (headings), Noto Sans JP (body)
- Animation: `.reveal` class with IntersectionObserver for scroll reveal

**Content:**
- Blog posts in `src/content/blog/` as Markdown with frontmatter (title, date, description)
- Dynamic routes via `src/pages/blog/[...slug].astro`
