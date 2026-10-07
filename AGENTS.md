# AGENTS.md — Working Agreement for AI Coding Agents

Scope: this repository (Kadena Community Edition website). Purpose: keep the site's structure, visual style, and conventions stable — regardless of who or what edits it.

For colors, typography, surfaces, and interaction/animation rules, the source of truth is [kdace-style-guide.md](./kdace-style-guide.md). Read it before touching presentation. This document covers everything that guide does not.

## 1. What this project is

- Hand-written, dependency-free static site: plain HTML + Tailwind CSS loaded via the Play CDN + vanilla JS in one inline `<script>` at the end of each page.
- No build step, no `package.json`, no framework, no linter, no tests. Do not introduce any of these unless explicitly asked.
- Public pages (root level): `index.html`, `miners.html`, `developers.html`, `dao.html`, `wallets.html`, `ecosystem.html`. Supporting files: `robots.txt`, `sitemap.xml`, `.nojekyll`, `google683312d1b7002f8b.html` (Google verification — do not touch), `img/`, `pdf/`.
- Deployment: GitHub Actions, `.github/workflows/publish.yaml` (manual trigger) publishes ONLY: root `*.html`, `img/`, `pdf/`, `sitemap.xml`, `robots.txt`, `.nojekyll`. Anything not in that list (e.g. `AGENTS.md`, this kind of documentation, `opencode.json`) is not shipped — that is intentional, but never reference unpublished files from the HTML.

## 2. Shared page chrome — invariants

All six pages share the same skeleton. Any change to navbar, footer, warning modal, theme or helper logic must be replicated in **all six pages**, byte-for-byte where applicable:

- **Navbar**: fixed, glassmorphic (`bg-white/95 dark:bg-surface-dark/95 backdrop-blur-md`), brand logo links to `/` (pair: `img/kadenace_dark.svg` light mode / `img/kadenace_light.svg` dark mode via `block dark:hidden` / `hidden dark:block`), theme-toggle button, hamburger; links in this exact order: Miners, Developers, DAO, Ecosystem, Wallets, Explorer. The current page's link carries `aria-current="page"`. The Explorer link is `target="_blank"` **and** carries class `external-link`.
- **Head order**: charset/viewport → `<title>` → favicon → description/keywords/author → canonical → OpenGraph → Twitter → `BreadcrumbList` JSON-LD → Google Fonts (Inter + JetBrains Mono) preconnect/link → Tailwind CDN + inline `tailwind.config` (tokens exactly as in style guide §3) → small shared `<style>` (body transition, `.aspect-video`).
- **Domains (deliberate mix, keep as is)**: canonical, `og:url`, `twitter:url` and sitemap/robots use `https://kda-chain.org/...`; `og:image` and `twitter:image` use absolute `https://kda-community.github.io/...` URLs. Every page gets its own OG banner from `img/og_card/`.
- **Dark mode**: Tailwind class strategy; shipping default is `<html class="dark">`. Every style that differs between modes needs its `dark:` counterpart. Preference persists in `localStorage["color-theme"]` — never hardcode a theme deeper than the default class on `<html>`.
- **Selection colors** vary per page family (standard: green/black; DAO: orange/black; Developers: blue/white) — see style guide §4. Preserve each page's existing setting.
- **Warning modal ("Leaving Website")**: triggered by a single delegated listener — it intercepts clicks on anchors inside `.project-card` / `.miner-card` / `.wallet-card` / `.group`, anywhere inside `<footer>`, elements with class `external-link`, or `#setup-guide-btn`, that point to another hostname (or to `/docs/...`). Internal `#...` anchors and `javascript:` hrefs are skipped. New external CTAs must therefore live inside one of those containers (cards' link rows already qualify) or explicitly carry `external-link`.
- **Footer**: identical social links (GitHub, Medium, X, Telegram, Discord, Bluesky) and the copyright line. The year is injected at runtime into `#current-year` — never hardcode years in the footer.
- **Body wrapper**: `bg-surface-light dark:bg-surface-dark bg-grid text-text-mainLight dark:text-text-mainDark font-sans antialiased selection:... overflow-x-hidden flex flex-col min-h-screen`, plus the fixed vignette overlay `<div class="fixed inset-0 pointer-events-none vignette z-0">` immediately inside `<body>`.
- **Script sections** carry banner comments (`// --- UNIVERSAL MODAL LOGIC ---`, `// --- 2. PROJECT FILTER LOGIC ---`, `// --- 3. DARK MODE LOGIC ---`, `// --- 4. BURGER MENU ---`). Keep that convention and numbering if sections grow. The only sanctioned inline JS in markup is the filter pills' `onclick="filterProjects('...')"` — everything else rides the delegated/global listeners.

## 3. Project cards (ecosystem.html; analogous grids in miners.html / wallets.html)

- One wrapper `<div>` per project. The safest procedure is: duplicate the freshest, most representative card, then edit only the differentiated values — never re-type the boilerplate.
- Wrapper signature (ecosystem.html): `project-card group h-full flex flex-col relative p-6 bg-white dark:bg-surface-card border border-gray-200 dark:border-gray-800 rounded-2xl hover:border-brand dark:hover:border-brand transition-all duration-300 hover:-translate-y-1 shadow-sm`. The border-accent (`brand`) switches to `dev` on developers.html and varies per page — keep the **page's** accent.
- `data-category`: space-separated tokens. Only tokens already covered by that page's filter pills are valid. Note `filterProjects()` matches with substring containment (`categories.includes(category)`), so keep tokens whole-word distinct (e.g. don't add `dexes` next to `dex`).
- **Icon tile**: `w-14 h-14 rounded-xl bg-gray-100 dark:bg-gray-800 p-2 flex items-center justify-center border border-gray-200 dark:border-gray-700 overflow-hidden` holding `<img src="img/ecosystem/ecosystem-<ShortName>.svg" onerror="this.src='https://placehold.co/100x100?text=<2-LETTER-INITIALS>'" alt="<Official Name>" class="w-full h-full object-contain">`. Logos are 135.466mm viewBox squares exported from the shared Inkscape sheet.
- **Tags**: pill = `px-2 py-1 text-xs font-mono rounded` + the established color pair, exactly: Infra=slate, Bridge=indigo, Protocol=indigo, DEX=blue, Community=orange, Mining=yellow, Game=red, NFT=pink, Launchpad=fuchsia, Oracle=teal, Exchange=green, Explorer=sky, Tools=cyan, DeFi=purple (e.g. Exchange: `bg-green-100 text-green-800 dark:bg-green-900/30 dark:text-green-300 border border-transparent dark:border-green-800/50`). Do not invent new pill colors. Multiple pills stack inside `<div class="flex flex-col items-end gap-1">`.
- **Copy**: `<h3 class="text-xl font-bold mb-2">` = the project's official casing exactly as their brand spells it (AnonKyc, Gate.io, KADPAD, KittyKad, CoinMetro...). Description: `<p class="text-sm text-gray-600 dark:text-gray-400 mb-6 line-clamp-2">` with ONE neutral, factual sentence, realistically ≤ ~100 characters; no exclamation marks, no buzzwords; if the project self-describes, paraphrase their positioning tightly.
- **Link row**: `<div class="flex items-center space-x-3 text-gray-400">` with X first (`aria-label="<Name> on X"`, filled-X path SVG), then website (`aria-label="<Name> Website"`, outlined globe SVG). Both `target="_blank"`, no extra classes beyond `hover:text-brand transition-colors`. Repositories/web-only projects substitute the code-branch icon (see the Kia card) with `aria-label="Website"`. Do not add new social icons ad hoc — the paths live in the footer if ever needed.
- **Whitespace/layout**: exactly one blank line between sibling cards; new cards go beside their category neighbors; do not silently reshuffle or re-wrap existing cards.

## 4. Naming conventions

- Page-scoped imagery: `img/<page>/ecosystem-<ShortProjectName>.svg` style — kebab-case prefix + the project's shortened name in its branded casing (`ecosystem-anonKyc.svg`, `ecosystem-kadpad.svg`, `ecosystem-marmaladeNg.svg`).
- Global imagery stays flat in `img/`; OG banners in `img/og_card/` sized to match the existing set.
- No spaces, no mixed separators beyond the established pattern; remember hosting is case-sensitive (Linux) — paths in HTML must match filenames exactly, including casing.

## 5. SEO & metadata upkeep

- Material content changes to a page ⇒ bump that page's `<lastmod>` in `sitemap.xml` (ISO `YYYY-MM-DD`). Dates in the repo are newer-than-they-look because of the fictional 2026 timeline — always check the current value before assuming.
- Structural changes (new/removed/renamed pages) ⇒ update `sitemap.xml` and, if discovery paths change, `robots.txt`. Priorities: homepage `1.0`, all others `0.8`; `changefreq weekly` — keep uniform.
- Never regenerate the whole sitemap by hand casually; edit surgically and keep the existing alphabetical-ish ordering.
- `robots.txt` gains the Algolia crawler-verification line at deploy time (added by the workflow) — do not add or remove it manually.

## 6. Process & git discipline

- Formatting: 4-space indentation, double-quoted attributes, keep orphans' neighbor formatting (multi-attribute wraps exactly where the file wraps them); prefer the smallest diff that achieves the goal.
- Stay in precedent: before adding markup, look at how the nearest sibling card/section does it and imitate 1:1.
- Icons: inline SVG paths only (24×24 viewBox, StrokeHeroicons-style for outline glyphs); no icon fonts, no CDN icon packs.
- Git: never stage/commit/push unless the operator explicitly asks. When a commit is requested, imperative concise subjects matching history style (`Add Oberpool and BallenasDNNS cards to ecosystem and miners pages`, `fix(seo): ...`). Machine-local config stays untracked: `opencode.json` is deliberately in `.gitignore` — do not add it, do not commit it.
- Leave `google683312d1b7002f8b.html`, `.nojekyll`, workflow YAML, and service-worker-less assumptions untouched unless the task says otherwise.

## 7. Verification (no CI tests exist)

1. Serve locally (`python3 -m http.server 8000`) and open the changed page(s): check desktop + narrow viewport, light + dark.
2. Exercise affected behaviors: filter pills, theme toggle, burger, warning modal, card hovers, new link targets.
3. `grep -ri '<removed-name>'` across the repo to prove no dangling references/assets (case-insensitive on NAMES, case-sensitive mindset for PATHS).
4. `git diff` review: only intended hunks; `git status` shows only intended modifications, nothing staged/committed unless asked.
