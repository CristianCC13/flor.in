# AGENTS.md — flor.in

## Project
Personal portfolio & blog for Florin (founder of indexlab.ro).
Static Astro site deployed on Cloudflare Pages.

## Tech Stack
- **Astro 7** — static output
- **Tailwind CSS 4** — styling via `@tailwindcss/vite`
- **Cloudflare Pages** — hosting (static, free tier)
- **TypeScript** — strict mode

## Structure
```
src/
├── components/     — Reusable UI components
├── layouts/        — Layout.astro (base HTML shell)
├── pages/          — File-based routing
│   ├── index.astro           — Home
│   ├── about/index.astro     — About / CV
│   ├── projects/index.astro  — Projects showcase
│   ├── blog/index.astro      — Blog listing
│   ├── contact/index.astro   — Contact info
│   └── blog/                 — Blog posts
├── styles/         — global.css (Tailwind + theme tokens)
└── content/        — Future: content collections
```

## Commands
- `pnpm dev` — dev server
- `pnpm build` — production build → `dist/`
- `pnpm preview` — preview build locally

## Design
- Minimal, clean, monochrome (zinc palette)
- Max width: `max-w-2xl` for readability
- No hardcoded colors — use Tailwind theme tokens from `global.css`

## Deploy
- Push to `main` → Cloudflare Pages auto-build
- Build command: `pnpm build`
- Output dir: `dist`
- Custom domain: flor.in