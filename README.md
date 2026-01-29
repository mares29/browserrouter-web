# BrowserRouter Website

Marketing website for [BrowserRouter](https://github.com/mares29/browserrouter) — a macOS menu bar app that automatically routes links to your currently active browser.

## Tech Stack

- **Framework:** Astro 5.x (SSG)
- **Deployment:** Cloudflare Pages
- **Styling:** Custom CSS with design tokens
- **Content:** Astro Content Collections (Markdown)

## Development

```bash
# Install dependencies
npm install

# Start dev server
npm run dev

# Build for production
npm run build

# Preview production build
npm run preview

# Deploy to Cloudflare
npm run deploy
```

## Project Structure

```
src/
├── components/     # Astro components (Hero, Features, Install, etc.)
├── content/blog/   # Markdown blog posts
├── layouts/        # BaseLayout, BlogPost
├── pages/          # index.astro, blog/
└── styles/         # global.css (design tokens, typography)
public/             # Static assets (favicon, og-image)
```

## Pages

- `/` — Landing page with hero, features, and install guide
- `/blog` — Blog index and posts

## Design

- Light/dark mode via `prefers-color-scheme`
- Cormorant Garamond (headings) + Noto Sans JP (body)
- Accent color: `#C4A77D` (warm gold)
