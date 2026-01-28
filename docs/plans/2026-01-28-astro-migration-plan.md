# Astro Migration Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Migrate vanilla HTML landing page to Astro, add blog with 4 thought leadership posts.

**Architecture:** Static site generation with Astro content collections. Components extracted from monolithic HTML. Design tokens in global CSS. Cloudflare deployment unchanged.

**Tech Stack:** Astro 4.x, @astrojs/cloudflare adapter, TypeScript for config, Markdown for blog.

---

## Task 1: Initialize Astro Project

**Files:**
- Create: `package.json`
- Create: `astro.config.mjs`
- Create: `tsconfig.json`
- Create: `src/env.d.ts`
- Preserve: `public/favicon.svg`, `public/og-image.svg`
- Update: `wrangler.jsonc`

**Step 1: Initialize Astro**

```bash
npm create astro@latest . -- --template minimal --no-install --no-git
```

Select: Empty project, No TypeScript (we'll add tsconfig manually), No install yet.

**Step 2: Install dependencies**

```bash
npm install astro @astrojs/cloudflare
```

**Step 3: Configure Astro**

Replace `astro.config.mjs`:

```js
import { defineConfig } from 'astro/config';
import cloudflare from '@astrojs/cloudflare';

export default defineConfig({
  output: 'static',
  adapter: cloudflare(),
  site: 'https://browserrouter.app',
});
```

**Step 4: Move static assets**

```bash
mkdir -p public
mv favicon.svg public/
mv og-image.svg public/
```

**Step 5: Update wrangler.jsonc**

Change assets path to point to Astro output:

```jsonc
{
  "name": "browserrouter-web",
  "assets": {
    "directory": "./dist"
  }
}
```

**Step 6: Add scripts to package.json**

Ensure these scripts exist:

```json
{
  "scripts": {
    "dev": "astro dev",
    "build": "astro build",
    "preview": "astro preview",
    "deploy": "npm run build && wrangler deploy"
  }
}
```

**Step 7: Verify Astro runs**

```bash
npm run dev
```

Expected: Dev server starts at localhost:4321

**Step 8: Commit**

```bash
git add -A
git commit -m "feat: initialize Astro project with Cloudflare adapter"
```

---

## Task 2: Extract Design System

**Files:**
- Create: `src/styles/global.css`

**Step 1: Create global.css with design tokens**

```css
/* Reset */
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

/* Design Tokens */
:root {
  --white: #FDFCFB;
  --cream: #F8F6F3;
  --stone: #E8E4DE;
  --ink: #1A1A1A;
  --ink-light: #6B6B6B;
  --ink-faint: #A8A8A8;
  --accent: #C4A77D;
  --code-bg: #1A1A1A;
  --code-text: #F8F6F3;
}

@media (prefers-color-scheme: dark) {
  :root {
    --white: #141414;
    --cream: #1C1C1C;
    --stone: #2A2A2A;
    --ink: #E8E4DE;
    --ink-light: #A8A8A8;
    --ink-faint: #6B6B6B;
    --accent: #D4B896;
    --code-bg: #0A0A0A;
    --code-text: #E8E4DE;
  }
}

/* Base Typography */
html {
  scroll-behavior: smooth;
}

body {
  font-family: 'Noto Sans JP', sans-serif;
  font-weight: 300;
  background: var(--white);
  color: var(--ink);
  line-height: 1.8;
  letter-spacing: 0.02em;
}

::selection {
  background: var(--accent);
  color: var(--white);
}

/* Grain Texture Overlay */
body::before {
  content: '';
  position: fixed;
  inset: 0;
  background-image: url("data:image/svg+xml,%3Csvg viewBox='0 0 200 200' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='noise'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.65' numOctaves='3' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23noise)'/%3E%3C/svg%3E");
  opacity: 0.02;
  pointer-events: none;
  z-index: 1000;
}

/* Scroll Reveal Animation */
.reveal {
  opacity: 0;
  transform: translateY(30px);
  transition: all 1s ease-out;
}

.reveal.visible {
  opacity: 1;
  transform: translateY(0);
}
```

**Step 2: Commit**

```bash
git add src/styles/global.css
git commit -m "feat: extract design tokens to global.css"
```

---

## Task 3: Create BaseLayout

**Files:**
- Create: `src/layouts/BaseLayout.astro`

**Step 1: Create BaseLayout**

```astro
---
interface Props {
  title: string;
  description?: string;
  ogImage?: string;
}

const {
  title,
  description = "A macOS menu bar app that opens links in whichever browser you were last using.",
  ogImage = "/og-image.png"
} = Astro.props;

const canonicalURL = new URL(Astro.url.pathname, Astro.site);
---

<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>{title}</title>
  <meta name="description" content={description}>
  <link rel="canonical" href={canonicalURL}>

  <!-- Open Graph -->
  <meta property="og:type" content="website">
  <meta property="og:title" content={title}>
  <meta property="og:description" content={description}>
  <meta property="og:url" content={canonicalURL}>
  <meta property="og:image" content={new URL(ogImage, Astro.site)}>

  <!-- Twitter Card -->
  <meta name="twitter:card" content="summary_large_image">
  <meta name="twitter:title" content={title}>
  <meta name="twitter:description" content={description}>
  <meta name="twitter:image" content={new URL(ogImage, Astro.site)}>

  <!-- Favicon -->
  <link rel="icon" type="image/svg+xml" href="/favicon.svg">

  <!-- Fonts -->
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Cormorant+Garamond:ital,wght@0,300;0,400;0,500;1,300;1,400&family=Noto+Sans+JP:wght@200;300;400&display=swap" rel="stylesheet">
</head>
<body>
  <slot />

  <script>
    // Scroll reveal
    const observer = new IntersectionObserver((entries) => {
      entries.forEach(entry => {
        if (entry.isIntersecting) {
          entry.target.classList.add('visible');
        }
      });
    }, { threshold: 0.1 });

    document.querySelectorAll('.reveal').forEach(el => observer.observe(el));
  </script>
</body>
</html>

<style is:global>
  @import '../styles/global.css';
</style>
```

**Step 2: Commit**

```bash
git add src/layouts/BaseLayout.astro
git commit -m "feat: create BaseLayout with meta tags and fonts"
```

---

## Task 4: Create Header Component

**Files:**
- Create: `src/components/Header.astro`

**Step 1: Create Header**

```astro
---
interface Props {
  showBlogLink?: boolean;
}

const { showBlogLink = true } = Astro.props;
---

<nav>
  <a href="/" class="logo">BrowserRouter</a>
  <div class="nav-links">
    {showBlogLink && <a href="/blog" class="nav-link">Blog</a>}
    <a href="https://github.com/mares29/browserrouter" class="nav-link" rel="noopener">GitHub</a>
  </div>
</nav>

<span class="vertical-text vertical-left">ブラウザルーター</span>
<span class="vertical-text vertical-right">Open Source • MIT</span>

<style>
  nav {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    z-index: 100;
    padding: 2rem 4rem;
    display: flex;
    justify-content: space-between;
    align-items: center;
  }

  .logo {
    font-family: 'Cormorant Garamond', serif;
    font-size: 1.1rem;
    font-weight: 400;
    letter-spacing: 0.2em;
    text-transform: uppercase;
    color: var(--ink);
    text-decoration: none;
  }

  .nav-links {
    display: flex;
    gap: 0.5rem;
  }

  .nav-link {
    font-size: 0.75rem;
    letter-spacing: 0.15em;
    text-transform: uppercase;
    color: var(--ink-light);
    text-decoration: none;
    padding: 0.5rem 1rem;
    transition: color 0.3s;
  }

  .nav-link:hover {
    color: var(--ink);
  }

  .vertical-text {
    position: fixed;
    writing-mode: vertical-rl;
    font-size: 0.65rem;
    letter-spacing: 0.3em;
    color: var(--ink-faint);
    text-transform: uppercase;
  }

  .vertical-left {
    left: 2rem;
    top: 50%;
    transform: translateY(-50%);
  }

  .vertical-right {
    right: 2rem;
    top: 50%;
    transform: translateY(-50%) rotate(180deg);
  }

  @media (max-width: 800px) {
    nav {
      padding: 1.5rem;
    }

    .vertical-text {
      display: none;
    }
  }

  @media (max-width: 480px) {
    nav {
      padding: 1rem;
    }

    .logo {
      font-size: 0.95rem;
      letter-spacing: 0.15em;
    }

    .nav-link {
      font-size: 0.7rem;
      padding: 0.5rem 0.75rem;
    }
  }

  @media (max-width: 360px) {
    nav {
      padding: 0.75rem;
    }

    .logo {
      font-size: 0.85rem;
      letter-spacing: 0.1em;
    }
  }
</style>
```

**Step 2: Commit**

```bash
git add src/components/Header.astro
git commit -m "feat: create Header component with nav and vertical text"
```

---

## Task 5: Create Footer Component

**Files:**
- Create: `src/components/Footer.astro`

**Step 1: Create Footer**

```astro
<footer>
  <div class="footer-logo">BrowserRouter</div>
  <div class="footer-links">
    <a href="/blog">Blog</a>
    <a href="https://github.com/mares29/browserrouter" rel="noopener">GitHub</a>
    <a href="https://github.com/mares29/browserrouter/issues" rel="noopener">Issues</a>
    <a href="https://github.com/mares29" rel="noopener">@mares29</a>
  </div>
  <p class="footer-copy">Open Source • MIT License</p>
</footer>

<style>
  footer {
    padding: 6rem 2rem;
    text-align: center;
    border-top: 1px solid var(--stone);
  }

  .footer-logo {
    font-family: 'Cormorant Garamond', serif;
    font-size: 1.5rem;
    font-weight: 300;
    letter-spacing: 0.1em;
    margin-bottom: 2rem;
  }

  .footer-links {
    display: flex;
    justify-content: center;
    gap: 3rem;
    margin-bottom: 3rem;
  }

  .footer-links a {
    font-size: 0.75rem;
    letter-spacing: 0.15em;
    text-transform: uppercase;
    color: var(--ink-light);
    text-decoration: none;
    transition: color 0.3s;
  }

  .footer-links a:hover {
    color: var(--ink);
  }

  .footer-copy {
    font-size: 0.75rem;
    color: var(--ink-faint);
    letter-spacing: 0.1em;
  }

  @media (max-width: 800px) {
    .footer-links {
      gap: 2rem;
    }
  }

  @media (max-width: 480px) {
    footer {
      padding: 4rem 1.25rem;
    }

    .footer-logo {
      font-size: 1.25rem;
    }

    .footer-links {
      flex-wrap: wrap;
      gap: 1.25rem 2rem;
    }

    .footer-copy {
      font-size: 0.7rem;
    }
  }

  @media (max-width: 360px) {
    .footer-links {
      flex-direction: column;
      gap: 1rem;
    }
  }
</style>
```

**Step 2: Commit**

```bash
git add src/components/Footer.astro
git commit -m "feat: create Footer component"
```

---

## Task 6: Create Hero Component

**Files:**
- Create: `src/components/Hero.astro`

**Step 1: Create Hero**

```astro
<section class="hero">
  <div class="hero-symbol">◯</div>
  <h1>Links follow <em>your focus</em></h1>
  <p class="hero-text">
    Click a link in Slack. It opens in the browser you're already using—not your system default.
  </p>
  <div class="hero-actions">
    <a href="#install" class="link-minimal">Install</a>
    <a href="https://github.com/mares29/browserrouter" class="link-minimal" rel="noopener">Source</a>
  </div>
</section>

<style>
  .hero {
    min-height: 100vh;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    text-align: center;
    padding: 4rem 2rem;
    position: relative;
  }

  .hero::after {
    content: '';
    position: absolute;
    bottom: 4rem;
    left: 50%;
    width: 1px;
    height: 80px;
    background: var(--ink-faint);
    animation: extend 2s ease-out infinite;
  }

  @keyframes extend {
    0% { transform: scaleY(0); opacity: 0; }
    50% { transform: scaleY(1); opacity: 1; }
    100% { transform: scaleY(1); opacity: 0; transform-origin: bottom; }
  }

  .hero-symbol {
    font-size: 3rem;
    margin-bottom: 3rem;
    opacity: 0.3;
    animation: fade-in 1.5s ease-out;
  }

  h1 {
    font-family: 'Cormorant Garamond', serif;
    font-size: clamp(2.5rem, 6vw, 4.5rem);
    font-weight: 300;
    letter-spacing: 0.05em;
    margin-bottom: 1.5rem;
    animation: fade-in 1.5s ease-out 0.2s both;
  }

  h1 em {
    font-style: italic;
  }

  .hero-text {
    font-size: 0.95rem;
    color: var(--ink-light);
    max-width: 400px;
    margin-bottom: 3rem;
    animation: fade-in 1.5s ease-out 0.4s both;
  }

  @keyframes fade-in {
    from {
      opacity: 0;
      transform: translateY(20px);
    }
    to {
      opacity: 1;
      transform: translateY(0);
    }
  }

  .hero-actions {
    display: flex;
    gap: 2rem;
    animation: fade-in 1.5s ease-out 0.6s both;
  }

  .link-minimal {
    font-size: 0.8rem;
    letter-spacing: 0.15em;
    text-transform: uppercase;
    color: var(--ink);
    text-decoration: none;
    padding-bottom: 4px;
    border-bottom: 1px solid var(--ink-faint);
    transition: border-color 0.3s;
  }

  .link-minimal:hover {
    border-color: var(--ink);
  }

  @media (max-width: 480px) {
    .hero {
      padding: 3rem 1.25rem;
    }

    .hero-symbol {
      font-size: 2.5rem;
      margin-bottom: 2rem;
    }

    h1 {
      font-size: clamp(1.8rem, 8vw, 2.5rem);
    }

    .hero-text {
      font-size: 0.9rem;
      padding: 0 0.5rem;
    }

    .hero-actions {
      flex-wrap: wrap;
      justify-content: center;
      gap: 1.25rem;
    }

    .hero::after {
      bottom: 2rem;
      height: 50px;
    }
  }

  @media (max-width: 360px) {
    h1 {
      font-size: 1.6rem;
    }

    .hero-actions {
      gap: 1rem;
    }

    .link-minimal {
      font-size: 0.75rem;
    }
  }
</style>
```

**Step 2: Commit**

```bash
git add src/components/Hero.astro
git commit -m "feat: create Hero component with animations"
```

---

## Task 7: Create Section Component

**Files:**
- Create: `src/components/Section.astro`

**Step 1: Create Section wrapper**

```astro
---
interface Props {
  number: string;
  title: string;
  class?: string;
  id?: string;
}

const { number, title, class: className = '', id } = Astro.props;
---

<section class:list={['section', className, 'reveal']} id={id}>
  <p class="section-number">{number}</p>
  <h2>{title}</h2>
  <slot />
</section>

<style>
  .section {
    padding: 8rem 2rem;
    max-width: 900px;
    margin: 0 auto;
  }

  .section-number {
    font-family: 'Cormorant Garamond', serif;
    font-size: 0.9rem;
    color: var(--ink-faint);
    letter-spacing: 0.2em;
    margin-bottom: 1rem;
  }

  h2 {
    font-family: 'Cormorant Garamond', serif;
    font-size: 2rem;
    font-weight: 300;
    margin-bottom: 3rem;
  }

  @media (max-width: 480px) {
    .section {
      padding: 5rem 1.25rem;
    }

    h2 {
      font-size: 1.6rem;
      margin-bottom: 2rem;
    }
  }
</style>
```

**Step 2: Commit**

```bash
git add src/components/Section.astro
git commit -m "feat: create reusable Section component"
```

---

## Task 8: Create Landing Page Sections

**Files:**
- Create: `src/components/PrincipleSection.astro`
- Create: `src/components/FeaturesSection.astro`
- Create: `src/components/ProcessSection.astro`
- Create: `src/components/InstallSection.astro`

**Step 1: Create PrincipleSection**

```astro
<section class="principle reveal">
  <div class="principle-text">
    <p class="section-number">01</p>
    <h2>The problem with defaults</h2>
    <p>
      You use Chrome for work, Safari for personal browsing. A link in Slack? Opens in Safari. A link in Notes? Safari again. macOS only knows one default—it ignores context.
    </p>
    <br>
    <p>
      BrowserRouter watches which browser you last used. Click a link anywhere, and it opens there. No dialogs. No choosing. Just the right browser, every time.
    </p>
  </div>
  <div class="principle-visual">
    <div class="browser-line">
      <span class="icon">○</span>
      <span class="name">Chrome</span>
      <span class="status">—</span>
    </div>
    <div class="browser-line">
      <span class="icon">○</span>
      <span class="name">Safari</span>
      <span class="status">—</span>
    </div>
    <div class="browser-line active">
      <span class="icon">●</span>
      <span class="name">Arc</span>
      <span class="status">Active</span>
    </div>
    <div class="browser-line">
      <span class="icon">○</span>
      <span class="name">Firefox</span>
      <span class="status">—</span>
    </div>
  </div>
</section>

<script>
  // Rotate active browser
  const lines = document.querySelectorAll('.browser-line');
  let activeIdx = 2;

  setInterval(() => {
    lines.forEach(l => {
      l.classList.remove('active');
      const icon = l.querySelector('.icon');
      const status = l.querySelector('.status');
      if (icon) icon.textContent = '○';
      if (status) status.textContent = '—';
    });
    activeIdx = (activeIdx + 1) % lines.length;
    lines[activeIdx].classList.add('active');
    const activeIcon = lines[activeIdx].querySelector('.icon');
    const activeStatus = lines[activeIdx].querySelector('.status');
    if (activeIcon) activeIcon.textContent = '●';
    if (activeStatus) activeStatus.textContent = 'Active';
  }, 3000);
</script>

<style>
  .principle {
    padding: 8rem 2rem;
    max-width: 900px;
    margin: 0 auto;
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 4rem;
    align-items: start;
  }

  .section-number {
    font-family: 'Cormorant Garamond', serif;
    font-size: 0.9rem;
    color: var(--ink-faint);
    letter-spacing: 0.2em;
    margin-bottom: 1rem;
  }

  h2 {
    font-family: 'Cormorant Garamond', serif;
    font-size: 2rem;
    font-weight: 300;
    margin-bottom: 3rem;
  }

  .principle-text {
    font-size: 0.95rem;
    color: var(--ink-light);
    line-height: 2;
  }

  .principle-visual {
    display: flex;
    flex-direction: column;
    gap: 1.5rem;
  }

  .browser-line {
    display: flex;
    align-items: center;
    gap: 1rem;
    padding: 1rem 0;
    border-bottom: 1px solid var(--stone);
    opacity: 0.4;
    transition: opacity 0.5s;
  }

  .browser-line.active {
    opacity: 1;
  }

  .browser-line .icon {
    width: 24px;
    height: 24px;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 1rem;
  }

  .browser-line .name {
    flex: 1;
    font-size: 0.85rem;
    letter-spacing: 0.1em;
  }

  .browser-line .status {
    font-size: 0.7rem;
    color: var(--ink-faint);
    letter-spacing: 0.1em;
    text-transform: uppercase;
  }

  .browser-line.active .status {
    color: var(--accent);
  }

  @media (max-width: 800px) {
    .principle {
      grid-template-columns: 1fr;
      gap: 3rem;
    }
  }

  @media (max-width: 480px) {
    .principle {
      padding: 5rem 1.25rem;
    }

    h2 {
      font-size: 1.6rem;
      margin-bottom: 2rem;
    }

    .principle-text p {
      font-size: 0.9rem;
    }
  }
</style>
```

**Step 2: Create FeaturesSection**

```astro
<section class="features reveal">
  <p class="section-number">02</p>
  <h2>How it works</h2>
  <div class="features-list">
    <div class="feature-item">
      <span class="feature-num">一</span>
      <div class="feature-content">
        <h3>Zero friction</h3>
        <p>No popups asking "which browser?" Just click links like normal.</p>
      </div>
    </div>
    <div class="feature-item">
      <span class="feature-num">二</span>
      <div class="feature-content">
        <h3>Smart fallback</h3>
        <p>Browser closed? Opens in the next most recent one. All browsers closed? Uses your configured default.</p>
      </div>
    </div>
    <div class="feature-item">
      <span class="feature-num">三</span>
      <div class="feature-content">
        <h3>Your rules</h3>
        <p>Choose which browsers to track. Exclude the ones you don't want routing to.</p>
      </div>
    </div>
    <div class="feature-item">
      <span class="feature-num">四</span>
      <div class="feature-content">
        <h3>2MB, native Swift</h3>
        <p>No Electron. No background processes. Uses ~10MB RAM.</p>
      </div>
    </div>
  </div>
</section>

<style>
  .features {
    padding: 8rem 2rem;
    max-width: 900px;
    margin: 0 auto;
  }

  .section-number {
    font-family: 'Cormorant Garamond', serif;
    font-size: 0.9rem;
    color: var(--ink-faint);
    letter-spacing: 0.2em;
    margin-bottom: 1rem;
  }

  h2 {
    font-family: 'Cormorant Garamond', serif;
    font-size: 2rem;
    font-weight: 300;
    margin-bottom: 3rem;
  }

  .features-list {
    display: grid;
    gap: 3rem;
  }

  .feature-item {
    display: grid;
    grid-template-columns: 60px 1fr;
    gap: 2rem;
    align-items: start;
  }

  .feature-num {
    font-family: 'Cormorant Garamond', serif;
    font-size: 2rem;
    color: var(--ink-faint);
    text-align: right;
  }

  .feature-content h3 {
    font-weight: 400;
    font-size: 1rem;
    margin-bottom: 0.5rem;
    letter-spacing: 0.05em;
  }

  .feature-content p {
    font-size: 0.9rem;
    color: var(--ink-light);
  }

  @media (max-width: 800px) {
    .feature-item {
      grid-template-columns: 40px 1fr;
      gap: 1rem;
    }
  }

  @media (max-width: 480px) {
    .features {
      padding: 5rem 1.25rem;
    }

    h2 {
      font-size: 1.6rem;
      margin-bottom: 2rem;
    }

    .feature-item {
      grid-template-columns: 32px 1fr;
      gap: 0.75rem;
    }

    .feature-num {
      font-size: 1.5rem;
    }

    .feature-content h3 {
      font-size: 0.95rem;
    }

    .feature-content p {
      font-size: 0.85rem;
    }
  }
</style>
```

**Step 3: Create ProcessSection**

```astro
<div class="process">
  <div class="process-inner reveal">
    <p class="section-number">03</p>
    <h2>Setup takes 2 minutes</h2>
    <div class="process-steps">
      <div class="process-step">
        <div class="num">1</div>
        <h4>Build & install</h4>
        <p>Clone the repo, run the build script.</p>
        <div class="process-line"></div>
      </div>
      <div class="process-step">
        <div class="num">2</div>
        <h4>Set as default</h4>
        <p>Make BrowserRouter your default browser in System Settings.</p>
        <div class="process-line"></div>
      </div>
      <div class="process-step">
        <div class="num">3</div>
        <h4>Forget about it</h4>
        <p>Links now open in whichever browser you're using.</p>
      </div>
    </div>
  </div>
</div>

<style>
  .process {
    background: var(--cream);
    padding: 8rem 2rem;
    margin: 0 -100vw;
    padding-left: calc(50vw - 450px + 2rem);
    padding-right: calc(50vw - 450px + 2rem);
  }

  .process-inner {
    max-width: 900px;
    margin: 0 auto;
  }

  .section-number {
    font-family: 'Cormorant Garamond', serif;
    font-size: 0.9rem;
    color: var(--ink-faint);
    letter-spacing: 0.2em;
    margin-bottom: 1rem;
  }

  h2 {
    font-family: 'Cormorant Garamond', serif;
    font-size: 2rem;
    font-weight: 300;
    margin-bottom: 3rem;
  }

  .process-steps {
    display: flex;
    justify-content: space-between;
    gap: 2rem;
    margin-top: 4rem;
  }

  .process-step {
    flex: 1;
    text-align: center;
  }

  .process-step .num {
    font-family: 'Cormorant Garamond', serif;
    font-size: 3rem;
    font-weight: 300;
    color: var(--ink-faint);
    line-height: 1;
    margin-bottom: 1.5rem;
  }

  .process-step h4 {
    font-weight: 400;
    font-size: 0.9rem;
    letter-spacing: 0.1em;
    margin-bottom: 0.75rem;
  }

  .process-step p {
    font-size: 0.85rem;
    color: var(--ink-light);
  }

  .process-line {
    width: 60px;
    height: 1px;
    background: var(--stone);
    margin: 2.5rem auto 0;
  }

  @media (max-width: 800px) {
    .process {
      padding-left: 2rem;
      padding-right: 2rem;
    }

    .process-steps {
      flex-direction: column;
      align-items: center;
    }

    .process-step {
      max-width: 300px;
    }

    .process-line {
      width: 1px;
      height: 40px;
    }
  }

  @media (max-width: 480px) {
    .process {
      padding: 5rem 1.25rem;
    }

    h2 {
      font-size: 1.6rem;
      margin-bottom: 2rem;
    }

    .process-step .num {
      font-size: 2.5rem;
    }

    .process-step h4 {
      font-size: 0.85rem;
    }

    .process-step p {
      font-size: 0.8rem;
    }
  }
</style>
```

**Step 4: Create InstallSection**

```astro
<section class="install-section reveal" id="install">
  <p class="section-number">04</p>
  <h2>Get started</h2>
  <p class="requirement">macOS 13+ required</p>
  <div class="code-block">
    <span class="comment"># Clone</span>
    <br>
    <span class="prompt">$</span> git clone https://github.com/mares29/browserrouter.git
    <br><br>
    <span class="comment"># Build</span>
    <br>
    <span class="prompt">$</span> cd browserrouter
    <br>
    <span class="prompt">$</span> ./scripts/bundle.sh
    <br><br>
    <span class="comment"># Set as default browser in System Settings</span>
  </div>
</section>

<style>
  .install-section {
    padding: 8rem 2rem;
    max-width: 900px;
    margin: 0 auto;
    text-align: center;
  }

  .section-number {
    font-family: 'Cormorant Garamond', serif;
    font-size: 0.9rem;
    color: var(--ink-faint);
    letter-spacing: 0.2em;
    margin-bottom: 1rem;
  }

  h2 {
    font-family: 'Cormorant Garamond', serif;
    font-size: 2rem;
    font-weight: 300;
    margin-bottom: 3rem;
  }

  .requirement {
    color: var(--ink-light);
    font-size: 0.9rem;
  }

  .code-block {
    background: var(--code-bg);
    color: var(--code-text);
    padding: 3rem;
    margin-top: 3rem;
    text-align: left;
    font-family: 'SF Mono', 'Monaco', monospace;
    font-size: 0.85rem;
    line-height: 2.2;
    overflow-x: auto;
    -webkit-overflow-scrolling: touch;
  }

  .code-block .comment {
    color: var(--ink-faint);
  }

  .code-block .prompt {
    color: var(--accent);
  }

  @media (max-width: 480px) {
    .install-section {
      padding: 5rem 1.25rem;
    }

    h2 {
      font-size: 1.6rem;
      margin-bottom: 2rem;
    }

    .code-block {
      padding: 1.5rem 1rem;
      font-size: 0.75rem;
      line-height: 2;
      word-break: break-word;
      overflow-wrap: break-word;
    }
  }

  @media (max-width: 360px) {
    .code-block {
      font-size: 0.7rem;
      padding: 1.25rem 0.75rem;
    }
  }
</style>
```

**Step 5: Commit**

```bash
git add src/components/PrincipleSection.astro src/components/FeaturesSection.astro src/components/ProcessSection.astro src/components/InstallSection.astro
git commit -m "feat: create landing page section components"
```

---

## Task 9: Assemble Landing Page

**Files:**
- Create: `src/pages/index.astro`
- Delete: `index.html` (old file)

**Step 1: Create index.astro**

```astro
---
import BaseLayout from '../layouts/BaseLayout.astro';
import Header from '../components/Header.astro';
import Hero from '../components/Hero.astro';
import PrincipleSection from '../components/PrincipleSection.astro';
import FeaturesSection from '../components/FeaturesSection.astro';
import ProcessSection from '../components/ProcessSection.astro';
import InstallSection from '../components/InstallSection.astro';
import Footer from '../components/Footer.astro';
---

<BaseLayout title="BrowserRouter – Links open in your last-used browser">
  <Header />
  <Hero />
  <PrincipleSection />
  <FeaturesSection />
  <ProcessSection />
  <InstallSection />
  <Footer />
</BaseLayout>
```

**Step 2: Add structured data to BaseLayout**

Add this script block before closing `</head>` in `BaseLayout.astro` (only for homepage):

```astro
{Astro.url.pathname === '/' && (
  <script type="application/ld+json" set:html={JSON.stringify({
    "@context": "https://schema.org",
    "@graph": [
      {
        "@type": "WebSite",
        "@id": "https://browserrouter.app/#website",
        "url": "https://browserrouter.app/",
        "name": "BrowserRouter",
        "description": "Links open in your last-used browser",
        "publisher": { "@id": "https://browserrouter.app/#person" }
      },
      {
        "@type": "SoftwareApplication",
        "@id": "https://browserrouter.app/#software",
        "name": "BrowserRouter",
        "operatingSystem": "macOS 13+",
        "applicationCategory": "UtilitiesApplication",
        "offers": { "@type": "Offer", "price": "0", "priceCurrency": "USD" },
        "description": "A macOS menu bar app that opens links in whichever browser you were last using.",
        "downloadUrl": "https://github.com/mares29/browserrouter"
      },
      {
        "@type": "Person",
        "@id": "https://browserrouter.app/#person",
        "name": "mares29",
        "url": "https://github.com/mares29"
      }
    ]
  })} />
)}
```

**Step 3: Verify landing page works**

```bash
npm run dev
```

Open http://localhost:4321 - should match original design.

**Step 4: Delete old index.html**

```bash
rm index.html
```

**Step 5: Commit**

```bash
git add src/pages/index.astro src/layouts/BaseLayout.astro
git rm index.html
git commit -m "feat: assemble landing page from components, remove old HTML"
```

---

## Task 10: Set Up Blog Content Collection

**Files:**
- Create: `src/content/config.ts`
- Create: `src/content/blog/.gitkeep`

**Step 1: Create content config**

```ts
import { defineCollection, z } from 'astro:content';

const blog = defineCollection({
  type: 'content',
  schema: z.object({
    title: z.string(),
    date: z.date(),
    description: z.string(),
  }),
});

export const collections = { blog };
```

**Step 2: Create blog directory**

```bash
mkdir -p src/content/blog
touch src/content/blog/.gitkeep
```

**Step 3: Commit**

```bash
git add src/content/config.ts src/content/blog/.gitkeep
git commit -m "feat: configure blog content collection"
```

---

## Task 11: Create BlogPost Layout

**Files:**
- Create: `src/layouts/BlogPost.astro`

**Step 1: Create BlogPost layout**

```astro
---
import BaseLayout from './BaseLayout.astro';
import Header from '../components/Header.astro';
import Footer from '../components/Footer.astro';

interface Props {
  title: string;
  date: Date;
  description: string;
}

const { title, date, description } = Astro.props;

const formattedDate = date.toLocaleDateString('en-US', {
  year: 'numeric',
  month: 'long',
  day: 'numeric'
});
---

<BaseLayout title={`${title} – BrowserRouter`} description={description}>
  <Header />

  <article class="blog-post">
    <header class="post-header">
      <a href="/blog" class="back-link">← Blog</a>
      <h1>{title}</h1>
      <time datetime={date.toISOString()}>{formattedDate}</time>
    </header>

    <div class="post-content">
      <slot />
    </div>
  </article>

  <Footer />
</BaseLayout>

<style>
  .blog-post {
    max-width: 680px;
    margin: 0 auto;
    padding: 8rem 2rem 4rem;
  }

  .post-header {
    margin-bottom: 4rem;
  }

  .back-link {
    font-size: 0.8rem;
    letter-spacing: 0.1em;
    text-transform: uppercase;
    color: var(--ink-light);
    text-decoration: none;
    display: inline-block;
    margin-bottom: 2rem;
    transition: color 0.3s;
  }

  .back-link:hover {
    color: var(--ink);
  }

  h1 {
    font-family: 'Cormorant Garamond', serif;
    font-size: clamp(2rem, 5vw, 3rem);
    font-weight: 300;
    letter-spacing: 0.02em;
    line-height: 1.2;
    margin-bottom: 1rem;
  }

  time {
    font-size: 0.85rem;
    color: var(--ink-faint);
    letter-spacing: 0.05em;
  }

  .post-content {
    font-size: 1.05rem;
    line-height: 1.9;
    color: var(--ink-light);
  }

  .post-content :global(h2) {
    font-family: 'Cormorant Garamond', serif;
    font-size: 1.5rem;
    font-weight: 400;
    color: var(--ink);
    margin: 3rem 0 1.5rem;
  }

  .post-content :global(h3) {
    font-size: 1.1rem;
    font-weight: 400;
    color: var(--ink);
    margin: 2rem 0 1rem;
  }

  .post-content :global(p) {
    margin-bottom: 1.5rem;
  }

  .post-content :global(a) {
    color: var(--ink);
    text-decoration: underline;
    text-underline-offset: 3px;
  }

  .post-content :global(blockquote) {
    border-left: 2px solid var(--accent);
    padding-left: 1.5rem;
    margin: 2rem 0;
    font-style: italic;
    color: var(--ink);
  }

  .post-content :global(ul),
  .post-content :global(ol) {
    margin: 1.5rem 0;
    padding-left: 1.5rem;
  }

  .post-content :global(li) {
    margin-bottom: 0.5rem;
  }

  @media (max-width: 480px) {
    .blog-post {
      padding: 6rem 1.25rem 3rem;
    }

    .post-content {
      font-size: 1rem;
    }
  }
</style>
```

**Step 2: Commit**

```bash
git add src/layouts/BlogPost.astro
git commit -m "feat: create BlogPost layout with prose styling"
```

---

## Task 12: Create Blog Pages

**Files:**
- Create: `src/pages/blog/index.astro`
- Create: `src/pages/blog/[...slug].astro`

**Step 1: Create blog index**

```astro
---
import { getCollection } from 'astro:content';
import BaseLayout from '../../layouts/BaseLayout.astro';
import Header from '../../components/Header.astro';
import Footer from '../../components/Footer.astro';

const posts = (await getCollection('blog')).sort(
  (a, b) => b.data.date.valueOf() - a.data.date.valueOf()
);
---

<BaseLayout title="Blog – BrowserRouter" description="Thoughts on browsers, productivity, and building for macOS.">
  <Header />

  <main class="blog-index">
    <header class="blog-header">
      <h1>Blog</h1>
      <p>Thoughts on browsers, productivity, and building for macOS.</p>
    </header>

    <ul class="posts-list">
      {posts.map((post) => (
        <li>
          <a href={`/blog/${post.slug}/`}>
            <time datetime={post.data.date.toISOString()}>
              {post.data.date.toLocaleDateString('en-US', { year: 'numeric', month: 'short', day: 'numeric' })}
            </time>
            <h2>{post.data.title}</h2>
          </a>
        </li>
      ))}
    </ul>

    {posts.length === 0 && (
      <p class="no-posts">No posts yet. Check back soon.</p>
    )}
  </main>

  <Footer />
</BaseLayout>

<style>
  .blog-index {
    max-width: 680px;
    margin: 0 auto;
    padding: 8rem 2rem 4rem;
    min-height: 60vh;
  }

  .blog-header {
    margin-bottom: 4rem;
  }

  .blog-header h1 {
    font-family: 'Cormorant Garamond', serif;
    font-size: 2.5rem;
    font-weight: 300;
    letter-spacing: 0.02em;
    margin-bottom: 0.5rem;
  }

  .blog-header p {
    color: var(--ink-light);
    font-size: 0.95rem;
  }

  .posts-list {
    list-style: none;
  }

  .posts-list li {
    border-bottom: 1px solid var(--stone);
  }

  .posts-list a {
    display: block;
    padding: 2rem 0;
    text-decoration: none;
    transition: opacity 0.3s;
  }

  .posts-list a:hover {
    opacity: 0.7;
  }

  .posts-list time {
    font-size: 0.8rem;
    color: var(--ink-faint);
    letter-spacing: 0.05em;
    display: block;
    margin-bottom: 0.5rem;
  }

  .posts-list h2 {
    font-family: 'Cormorant Garamond', serif;
    font-size: 1.4rem;
    font-weight: 400;
    color: var(--ink);
  }

  .no-posts {
    color: var(--ink-faint);
    font-style: italic;
  }

  @media (max-width: 480px) {
    .blog-index {
      padding: 6rem 1.25rem 3rem;
    }

    .blog-header h1 {
      font-size: 2rem;
    }
  }
</style>
```

**Step 2: Create dynamic post route**

```astro
---
import { getCollection } from 'astro:content';
import BlogPost from '../../layouts/BlogPost.astro';

export async function getStaticPaths() {
  const posts = await getCollection('blog');
  return posts.map((post) => ({
    params: { slug: post.slug },
    props: post,
  }));
}

const post = Astro.props;
const { Content } = await post.render();
---

<BlogPost title={post.data.title} date={post.data.date} description={post.data.description}>
  <Content />
</BlogPost>
```

**Step 3: Commit**

```bash
git add src/pages/blog/index.astro src/pages/blog/\[...slug\].astro
git commit -m "feat: create blog index and dynamic post pages"
```

---

## Task 13: Add 4 Blog Posts

**Files:**
- Create: `src/content/blog/context-matters.md`
- Create: `src/content/blog/building-native.md`
- Create: `src/content/blog/invisible-software.md`
- Create: `src/content/blog/open-source-journey.md`

**Step 1: Create first post**

Create `src/content/blog/context-matters.md`:

```md
---
title: "Context Matters More Than Defaults"
date: 2026-01-28
description: "Why your operating system's concept of a 'default browser' is fundamentally broken."
---

Every operating system asks the same question during setup: which browser do you want as your default? It's a reasonable question with an unreasonable assumption—that you only ever want to use one browser.

## The multi-browser reality

Most of us live in at least two browsers. Chrome for work, with its profiles and extensions tied to our professional identity. Safari for personal browsing, where privacy matters more than compatibility. Maybe Firefox for development. Arc for that project you're exploring.

The operating system sees none of this nuance. Click a link in Slack, and it opens in Safari—even though you've spent the last hour in Chrome, working in that exact context.

## Context is invisible

The problem isn't that defaults exist. The problem is that defaults ignore context.

When you're deep in a work session, context isn't just which app you're using—it's the mental state you've built up. The tabs you have open. The frame of mind you're in. Breaking that context to manually choose a browser (or worse, having the wrong browser chosen for you) creates friction that accumulates throughout the day.

## What if software paid attention?

The best software disappears. It observes your patterns and acts accordingly, without asking permission for every decision.

That's the principle behind BrowserRouter. Not "which browser do you prefer?" but "which browser are you already using?" The answer changes throughout your day, and the software should respect that.

Sometimes the most powerful feature is simply paying attention.
```

**Step 2: Create second post**

Create `src/content/blog/building-native.md`:

```md
---
title: "Why Native Still Matters"
date: 2026-01-21
description: "In a world of Electron apps, there's still a case for building native software."
---

Every few months, someone publishes a thinkpiece about how native development is dead. Cross-platform frameworks have won. Why write Swift and Kotlin when you can write JavaScript once?

Having built BrowserRouter as a native Swift app, I have thoughts.

## The numbers that matter

BrowserRouter uses about 10MB of RAM. A comparable Electron app would use 150-300MB just to exist. That's not a theoretical concern—it's the difference between software that fades into the background and software that fights for resources.

The app is 2MB on disk. It launches instantly. It doesn't spin up a Chromium instance to display a menu bar icon.

## Native means invisible

The best menu bar apps are the ones you forget about. They sit there, doing their job, never demanding attention. Native apps make this possible in a way that frameworks struggle with.

System integration matters too. BrowserRouter needs to observe which app has focus, intercept URL handling, and communicate with the system's browser registration. These aren't web platform features. Fighting against the platform to access them is a losing battle.

## When Electron makes sense

Cross-platform frameworks aren't wrong—they're tradeoffs. If you need to ship on Windows, Mac, and Linux with a small team, Electron might be the right choice. If your app is essentially a web app that happens to need desktop presence, it makes sense.

But for a utility that lives in your menu bar and does one thing well? Native is still the answer.

## Craft as a statement

There's something else, harder to quantify: building native feels like craftsmanship. You're working with the materials the platform provides, not around them.

In a world of increasingly bloated software, small and fast is a feature worth pursuing.
```

**Step 3: Create third post**

Create `src/content/blog/invisible-software.md`:

```md
---
title: "The Best Software Is Invisible"
date: 2026-01-14
description: "On designing tools that solve problems without demanding attention."
---

There's a particular kind of software that I find most satisfying to use: software that solves a problem so well that you forget it exists.

## The attention economy of apps

Most software demands attention. Notifications. Badges. Onboarding flows. Feature announcements. The modern app wants to be seen, to justify its existence through engagement.

But engagement isn't value. Often, the most valuable software is the software you interact with least.

## Invisible by design

When I think about the tools I rely on most, they share a common trait: they work without my involvement. My password manager autofills credentials. My backup software runs in the background. My window manager responds to keyboard shortcuts I've built into muscle memory.

I don't "engage" with these tools. I don't check their dashboards or explore their features. They just work.

## The design principle

Building invisible software requires a specific mindset:

**Default to working.** Don't ask for configuration that you could infer. Don't require setup that you could automate.

**Stay quiet.** If something succeeded, the user doesn't need to know. If something failed, only speak up if there's action to take.

**Respect context.** The user is trying to do something else. Your software is a means to an end, not the end itself.

## The paradox of success

The better BrowserRouter works, the less people think about it. Links just open in the right place. That's the goal—to solve a problem so thoroughly that the problem feels like it never existed.

It's a strange metric for success: the best outcome is to be forgotten. But that's the nature of tools. They exist to enable, not to impress.
```

**Step 4: Create fourth post**

Create `src/content/blog/open-source-journey.md`:

```md
---
title: "Notes on Going Open Source"
date: 2026-01-07
description: "Reflections on releasing a personal project to the public."
---

BrowserRouter started as a script I wrote to solve my own problem. Somewhere along the way, it became an app with a landing page and a GitHub repository. Here's what I learned.

## Building for one

The best motivation for building software is solving your own problem. When you're the user, you know exactly what matters. No user research required—you have direct access to someone who will use this tool every day.

BrowserRouter exists because I got frustrated clicking links in Slack and watching them open in Safari while I was deep in a Chrome work session. That frustration was enough to write the first version in an afternoon.

## The moment of polish

There's a point in every project where "it works for me" isn't enough anymore. You start thinking about edge cases. You add error handling. You write documentation.

This is the moment where most personal projects die. The interesting problem is solved. What remains is the work of making it useful for others.

Pushing through that resistance is how side projects become real software.

## Open source as accountability

Making the code public changes your relationship with it. Suddenly there's a theoretical audience, even if no one's actually reading your code yet. You write better comments. You clean up that hacky workaround. You think about maintainability.

Open source isn't just about sharing—it's a forcing function for quality.

## What I'd tell myself

If I could go back to the beginning of this project, here's what I'd say:

**Ship earlier than you're comfortable with.** The feedback you get from having something real in the world is worth more than the polish you're adding in isolation.

**Documentation is marketing.** A good README does more for adoption than any feature.

**Solve one problem well.** Scope creep is the enemy of utility software. BrowserRouter does one thing. That focus is a feature.

The code is on GitHub. Maybe it'll be useful to someone else who's tired of fighting their operating system's defaults.
```

**Step 5: Verify blog works**

```bash
npm run dev
```

Visit http://localhost:4321/blog - should show all 4 posts.

**Step 6: Commit**

```bash
git add src/content/blog/
git commit -m "feat: add 4 blog posts"
```

---

## Task 14: Final Verification & Deploy

**Step 1: Build and preview**

```bash
npm run build && npm run preview
```

Verify in browser:
- Homepage renders correctly
- All sections visible
- Dark mode works
- Blog index shows 4 posts
- Individual posts render with prose styling

**Step 2: Deploy to Cloudflare**

```bash
npm run deploy
```

**Step 3: Verify production**

Visit https://browserrouter.app (or your deployment URL):
- Homepage works
- Blog works
- All links functional

**Step 4: Final commit if any cleanup needed**

```bash
git status
# If changes:
git add -A
git commit -m "chore: final cleanup after migration"
```

---

## Summary

| Task | Description |
|------|-------------|
| 1 | Initialize Astro project with Cloudflare adapter |
| 2 | Extract design tokens to global.css |
| 3 | Create BaseLayout with meta tags |
| 4 | Create Header component |
| 5 | Create Footer component |
| 6 | Create Hero component |
| 7 | Create Section component |
| 8 | Create landing page sections |
| 9 | Assemble landing page, delete old HTML |
| 10 | Set up blog content collection |
| 11 | Create BlogPost layout |
| 12 | Create blog pages (index + dynamic) |
| 13 | Add 4 blog posts |
| 14 | Final verification & deploy |
