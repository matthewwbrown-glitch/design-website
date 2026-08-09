# matthewbrowndesign.com — CLAUDE.md

Context for Claude Code (or any agent) working in this repo. This is Matthew Brown's personal portfolio site.

## What this is

A static HTML/CSS/JS site — **no framework, no build tool, no package manager**. Every page is hand-authored HTML that includes the same shared stylesheet and script. There is no `npm install`, no build step, and no `package.json`. You edit `.html`/`.css`/`.js` files directly and the result is exactly what ships.

## Deployment

- **Repo:** `https://github.com/matthewwbrown-glitch/design-website` (public), default branch `main`.
- **Hosting:** Cloudflare Pages, connected directly to this GitHub repo. Every push to `main` auto-builds and deploys — there's no manual deploy step and no separate staging environment. Treat commits to `main` as production.
- **Domain:** `matthewbrowndesign.com`, pointed at the Cloudflare Pages deployment.
- Because there's no build step, "does it work" checks should be done by opening the actual HTML files (or the live/preview URL) in a browser — there's no compile/lint step that will catch broken markup, bad paths, etc.

## Repo structure

```
site/
  index.html            Home (Product Design landing)
  about.html             About page
  visual-design.html    Visual Design landing
  case-studies.html      Password-gated private section (see below)
  projects/              15 individual project pages (2 templates, see below)
  assets/
    css/style.css        Single shared stylesheet — all design tokens as CSS custom properties
    js/main.js           Shared JS: mobile nav toggle, carousel, scroll-reveal
    images/<project>/    Per-project image folders
  favicon.ico, apple-touch-icon, etc.

../STYLE-GUIDE.md         Design tokens + page-template reference (read this before styling anything)
../content/
  site-text.md            Source copy scraped from the old live site
  projects/<slug>.md       Per-project extracted copy + image manifest (generated from live HTML)
  projects/<slug>/         A richer parallel staging area (case-study.md w/ frontmatter + images/) —
                            some projects have MORE images here than are live on the site (see Known gaps)
```

## Two page templates — don't mix them up

- **Product Design case study** (`workflow-designer`, `customer-search`, `hangry-app`, `mobile-form-design`, `super-wealth-tracker`, `loreal`): left-aligned `.case-study-header` + `.case-study-body` prose + a `.carousel` image component (autoplay, dots, arrows, keyboard + swipe nav).
- **Visual Design gallery** (`metro-trains`, `mastercard`, `moringa-project`, `glowpear`, `prancing-horse`, `blockbuster`, `hunting-the-bungle`, `wasp-bags`, `sfmrc`): centered `.hero` title, no narrative body, images laid out via `.visual-sequence`/`.visual-grid-row`/`.visual-hero` (orientation-aware: landscape images fill hero slots, portrait fills grid slots).

All design tokens (colors, spacing, type scale) live in `assets/css/style.css` as CSS custom properties — change them there, not per-page. Full reference in `../STYLE-GUIDE.md`.

## The password-gated "Case Studies" section

`case-studies.html` is a private section, separate from the 15 public project pages. It's a client-side-only gate: the page stores a SHA-256 hash of the password and compares against it via the Web Crypto API, unlocking `sessionStorage` for the rest of the browser session. **This is obscurity, not real security** — it's a static site with no backend, so the hash and gated content are both fully visible in page source/devtools to anyone who looks. Fine for keeping casual visitors out, not for anything actually sensitive. The password itself is intentionally not written into this file or committed anywhere in plaintext — ask Matthew if you need it.

## Known open items (as of 2026-08-08)

- Several project pages have thin/placeholder copy: Workflow Designer ("User interface validation", "Hi-fidelity design"), Customer Search (only 1 supporting image), Hangry App ("Personas" has no source copy), Super Wealth Tracker ("Brand identity and style guide" section, "Customer presentation pack"), L'Oréal (almost no narrative despite 13 images).
- `content/projects/<slug>/images/` (the parallel staging area) has more images than are live on the site for several projects — notably `glowpear` has a real `5.jpg` there that never made it into `site/assets/images/glowpear/`, and `moringa-project`/`prancing-horse` also have unused extra images staged.
- There's an unlinked 16th project, `content/projects/sketch-book-project/` (46 images, no case-study.md), not part of the current 15-project site — confirm with Matthew whether this should become a real page.
- `index.html`'s meta description still says "Melbourne-born, Seattle-based Product Designer" — stale location reference (About page's equivalent was already fixed to drop location).
- `style.css` has two separate, non-consolidated `@media (max-width: 480px)` blocks — cosmetic cleanup, not a bug.

## Working conventions

- No build tooling means no linter/formatter enforced — match existing style (2-space indent, class-based CSS, no inline styles).
- Image filenames are case-sensitive on the deployed host (Cloudflare Pages runs Linux) — always match `<img src>` casing to the actual file on disk exactly, even though it won't error locally on a Mac.
- Don't reintroduce the old duplicate carousel + static gallery pattern — the redundant static `.case-study-gallery` block was deliberately removed from the 6 product-design pages in favor of the carousel alone.
- Functional/interactive components (carousel, nav) should never be gated behind the scroll-reveal fade-in — only decorative content should use `.reveal`. This caused a real bug once (carousel appearing "broken" because it was invisible pre-scroll).
