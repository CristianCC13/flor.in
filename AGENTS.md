# AGENTS.md — flor.in

## Project
Personal portfolio & blog for Florin (founder of indexlab.ro).
Static Astro site built on Astro Keel theme, deployed on Cloudflare Pages.

## Tech Stack
- **Astro 7** — static output, zero JS
- **Astro Keel** — minimal editorial portfolio/blog theme
- **MDX** — content with code highlighting (Shiki dual-theme)
- **Sitemap + RSS** — auto-generated
- **Pagefind** — static search
- **TypeScript** — strict mode
- **Cloudflare Pages** — hosting (static, free tier)

## Structure
```
src/
├── components/     — Reusable UI components
├── layouts/        — BaseLayout.astro (base HTML shell)
├── pages/          — File-based routing
│   ├── index.astro           — Home
│   ├── about/index.astro     — About
│   ├── works/                — Projects/case studies
│   ├── blog/                 — Blog listing + posts
│   ├── search.astro          — Pagefind search
│   └── rss.xml.ts            — RSS feed
├── content/        — Content collections (works + blog)
├── styles/         — global.css (theme tokens, fonts)
├── consts.ts       — Site settings (title, nav, etc.)
└── lib/            — Utilities
```

## Commands
- `pnpm dev` — dev server
- `pnpm build` — production build → `dist/` (+ pagefind index)
- `pnpm preview` — preview build locally
- `pnpm check` — type check

## Customize
- Edit `src/consts.ts` for site title, description, nav items
- Edit `src/styles/global.css` — `--color-accent` is the single accent variable
- Add blog posts as `.mdx` or `.md` in `src/content/blog/`
- Add projects as `.mdx` or `.md` in `src/content/works/`

## Deploy
- Push to `main` → Cloudflare Pages auto-build
- Build command: `pnpm build`
- Output dir: `dist`
- Custom domain: flor.in