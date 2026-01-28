# Astro Migration Design

## Overview

Migrate vanilla HTML/CSS landing page to Astro. Add blog with 4 thought leadership posts.

## Goals

- Markdown content authoring
- Component-based development
- Future scalability for more pages
- Preserve aesthetic (colors, typography, minimalism)

## Project Structure

```
web/
├── astro.config.mjs
├── package.json
├── public/
│   ├── favicon.svg
│   └── og-image.svg
├── src/
│   ├── layouts/
│   │   ├── BaseLayout.astro
│   │   └── BlogPost.astro
│   ├── components/
│   │   ├── Header.astro
│   │   ├── Footer.astro
│   │   ├── Hero.astro
│   │   ├── Section.astro
│   │   └── FeatureCard.astro
│   ├── styles/
│   │   └── global.css
│   ├── content/
│   │   └── blog/
│   │       └── *.md
│   └── pages/
│       ├── index.astro
│       └── blog/
│           ├── index.astro
│           └── [...slug].astro
└── wrangler.jsonc
```

## Design System

Extract from current CSS into `global.css`:

```css
:root {
  /* Colors */
  --color-cream: #faf8f5;
  --color-stone: #e8e4df;
  --color-ink: #1a1a1a;
  --color-ink-light: #4a4a4a;
  --color-accent: #c9a227;

  /* Typography */
  --font-heading: 'Cormorant Garamond', serif;
  --font-body: 'Noto Sans JP', sans-serif;

  /* Spacing */
  --space-xs: 0.5rem;
  --space-sm: 1rem;
  --space-md: 2rem;
  --space-lg: 4rem;
  --space-xl: 8rem;
}

@media (prefers-color-scheme: dark) {
  :root {
    --color-cream: #1a1a1a;
    --color-ink: #faf8f5;
  }
}
```

Components use scoped `<style>` blocks referencing these variables.

## Components

| Component | Purpose | Props |
|-----------|---------|-------|
| `BaseLayout` | HTML shell, head, fonts, global CSS | `title`, `description`, `ogImage` |
| `BlogPost` | Extends Base, post header/footer | `title`, `date` |
| `Header` | Fixed navigation | — |
| `Footer` | Site footer | — |
| `Hero` | Landing hero with animation | — |
| `Section` | Numbered section wrapper | `number`, `title`, `subtitle` |
| `FeatureCard` | Feature grid item | `title`, `description` |

## Blog

Content collection with minimal schema:

```ts
const blog = defineCollection({
  type: 'content',
  schema: z.object({
    title: z.string(),
    date: z.date(),
    description: z.string(),
  }),
});
```

Features at launch:
- Posts with title, date, content
- Blog index listing all posts
- Prose typography for essays

## Build & Deployment

```js
// astro.config.mjs
export default defineConfig({
  output: 'static',
  adapter: cloudflare(),
  site: 'https://browserrouter.com',
});
```

Scripts:
- `npm run dev` — Local dev
- `npm run build` — Generate static site
- `npm run deploy` — Build + wrangler deploy

## Migration Steps

1. Initialize Astro in current directory
2. Extract CSS into `global.css`
3. Create components from `index.html` sections
4. Set up content collection
5. Add 4 blog posts
6. Test locally, deploy
