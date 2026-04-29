# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

A static blogging website — pure HTML/CSS/JS with no build tools, no frameworks, no dependencies. Hosted on GitHub Pages via the `iamasx/my-blogs` repository. The `.nojekyll` file disables Jekyll processing.

## Architecture

**Homepage** (`index.html`) loads `assets/js/blogs.js` which sets `window.BLOG_POSTS` — an array of metadata objects. A script reads this array and renders blog cards dynamically. This uses a global variable pattern (not `fetch`) so the site works from `file://` protocol too.

**Blog posts** live at `posts/<slug>/index.html`. The directory-based structure gives clean URLs on GitHub Pages (e.g., `/posts/github-api-rate-limiting/`).

**Two types of blog posts exist:**
- **Bundled posts** — self-contained HTML files produced by an external tool (e.g., `posts/github-api-rate-limiting/`). These have their own inline CSS and use a `__bundler/manifest` + `__bundler/template` system. Do not modify bundled content — only the nav injection script at the top.
- **Template-based posts** — use `posts/_template.html` as a starting point and link to `../../assets/css/shared.css` and `../../assets/css/post.css`.

**Nav injection for bundled posts:** A MutationObserver waits for the bundler to render `.wrap`, then prepends a sticky `<nav>` with inline styles. This pattern avoids depending on external CSS.

## Adding a New Blog Post

1. Copy `posts/_template.html` to `posts/<slug>/index.html`
2. Replace placeholders: `POST_TITLE`, `POST_CATEGORY`, `POST_SUBTITLE`, `POST_DATE`, `POST_READING_TIME`
3. Add an entry to `assets/js/blogs.js`:
   ```js
   { slug: "my-post", title: "...", description: "...", date: "YYYY-MM-DD", readingTime: "X min", tags: ["..."] }
   ```

## CSS Architecture

- `assets/css/shared.css` — design tokens (CSS custom properties), reset, nav, footer, `.wrap` container
- `assets/css/post.css` — article/post-specific styles (headers, cards, code blocks, tables, alerts, collapsibles)
- Homepage styles are inline in `index.html` (animations, card grid, visual effects)

**Design tokens** — all colors, radii, and theming use CSS variables defined in `:root` of `shared.css`. Key values: `--bg: #0b0d10`, `--accent: #7dd3fc`, `--panel: #12161c`, `--ink: #e6edf3`. Always use these variables rather than hardcoding colors (exception: bundled posts and their nav injection which use inline styles).

**Responsive breakpoints:** 700px (tablet) and 560px (mobile).

## Post Styling Classes

When writing template-based posts, use these classes from `post.css`:
- `.post-header` with `.eyebrow`, `h1`, `.sub`, `.meta-row` for the title block
- `.card` for callout boxes
- `.alert-info`, `.alert-warn`, `.alert-good` for notices
- `<details>/<summary>` for collapsible sections
- Standard `<pre><code>`, `<table>`, `<ul>/<ol>` are all styled

## Deployment

No build step. Commit and push to `main` — GitHub Pages serves the site directly.
