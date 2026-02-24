# CLAUDE.md

This file provides guidance for AI assistants working on this codebase.

## Project Overview

**flor.in** is a personal blog built on [AstroPaper](https://github.com/satnaing/astro-paper), a minimal, responsive, accessible, and SEO-friendly Astro blog theme. The site is deployed at `https://flor.in/` and authored by Cristian.

- **Framework**: Astro 5 (static site generator)
- **Styling**: Tailwind CSS v4 (via `@tailwindcss/vite`)
- **Language**: TypeScript (strict mode)
- **Search**: Pagefind (client-side static search)
- **Content**: Markdown files in `src/data/blog/`

## Development Commands

```bash
# Install dependencies (use pnpm — NOT npm or yarn)
pnpm install

# Start dev server
pnpm dev

# Type-check, build, run pagefind indexing, and copy pagefind assets
pnpm build

# Preview production build
pnpm preview

# Check formatting (runs in CI)
pnpm format:check

# Auto-fix formatting
pnpm format

# Lint (runs in CI)
pnpm lint

# Sync Astro type definitions
pnpm sync
```

> **Important**: The `build` script runs `astro check && astro build && pagefind --site dist && cp -r dist/pagefind public/`. Do not skip any of these steps.

## CI Pipeline

CI runs on pull requests via `.github/workflows/ci.yml` using Node.js 20 and pnpm 10.11.1. The pipeline runs in order:
1. `pnpm lint` — must pass with zero errors
2. `pnpm format:check` — must pass with no formatting issues
3. `pnpm build` — must build successfully

All three checks must pass before merging.

## Project Structure

```
flor.in/
├── src/
│   ├── assets/
│   │   └── icons/          # SVG icons (imported as Astro components)
│   ├── components/         # Reusable Astro components
│   ├── content.config.ts   # Astro content collection schema (blog)
│   ├── config.ts           # Site-wide configuration (SITE object)
│   ├── constants.ts        # SOCIALS and SHARE_LINKS arrays
│   ├── data/
│   │   └── blog/           # Markdown blog posts live here
│   ├── layouts/
│   │   ├── Layout.astro    # Root HTML layout (SEO meta, fonts, theme)
│   │   ├── Main.astro      # Inner page layout wrapper
│   │   ├── PostDetails.astro # Full post layout with prev/next nav
│   │   └── AboutLayout.astro
│   ├── pages/              # File-based routing
│   │   ├── index.astro     # Home page
│   │   ├── about.md        # About page (Markdown)
│   │   ├── search.astro    # Pagefind search UI
│   │   ├── posts/          # Paginated posts listing
│   │   ├── tags/           # Tag pages
│   │   ├── archives/       # Archives (hidden when SITE.showArchives=false)
│   │   ├── rss.xml.ts      # RSS feed endpoint
│   │   ├── robots.txt.ts   # robots.txt endpoint
│   │   └── og.png.ts       # Site-level OG image endpoint
│   ├── scripts/
│   │   └── theme.ts        # Light/dark mode toggle logic
│   ├── styles/
│   │   ├── global.css      # Tailwind imports + CSS custom properties + utilities
│   │   └── typography.css  # Prose/article typography styles
│   └── utils/
│       ├── generateOgImages.ts  # OG image generation using satori + resvg
│       ├── getPath.ts           # Derives URL path from post id/filePath
│       ├── getPostsByGroupCondition.ts
│       ├── getPostsByTag.ts
│       ├── getSortedPosts.ts    # Filters (postFilter) + sorts by date
│       ├── getUniqueTags.ts
│       ├── loadGoogleFont.ts    # Font loader for OG image generation
│       ├── postFilter.ts        # Excludes drafts; respects scheduledPostMargin
│       ├── slugify.ts           # Hybrid slugifier (Latin vs non-Latin)
│       ├── og-templates/        # Satori JSX-object templates for OG images
│       └── transformers/        # Shiki code block transformers
├── public/                 # Static assets (favicon, OG image, pagefind index)
├── astro.config.ts         # Astro configuration
├── tsconfig.json           # TypeScript config (strict, path alias @/→src/)
├── eslint.config.js        # ESLint config (TypeScript + Astro plugins)
├── .prettierrc.mjs         # Prettier config
└── cz.yaml                 # Commitizen config (conventional commits)
```

## Site Configuration (`src/config.ts`)

All site-wide settings live in the `SITE` constant. Key fields:

| Field | Description |
|---|---|
| `website` | Production URL |
| `author` | Default post author |
| `lightAndDarkMode` | Enable/disable theme toggle (currently `false`) |
| `postPerIndex` | Posts shown on home page |
| `postPerPage` | Posts per page on `/posts/` |
| `scheduledPostMargin` | ms buffer for scheduled posts (15 min) |
| `showArchives` | Show/hide the `/archives` route |
| `showBackButton` | Show back button on post detail pages |
| `editPost.enabled` | Enable "Edit page" link on posts |
| `dynamicOgImage` | Generate per-post OG images via satori |
| `timezone` | IANA timezone for post dates (Europe/Bucharest) |

Social links and share links are configured in `src/constants.ts`.

## Content Collection & Blog Posts

Blog posts are Markdown files in `src/data/blog/`. The collection schema is defined in `src/content.config.ts`.

### Frontmatter Schema

```yaml
---
author: string          # defaults to SITE.author
pubDatetime: Date       # required — ISO 8601
modDatetime: Date       # optional — ISO 8601
title: string           # required
featured: boolean       # optional — appears in Featured section
draft: boolean          # optional — excluded from production builds
tags:
  - string              # defaults to ["others"]
ogImage: string|image   # optional — remote URL or local asset
description: string     # required — used in meta/cards
canonicalURL: string    # optional — overrides default canonical
hideEditPost: boolean   # optional — hides the "Edit" link for this post
timezone: string        # optional — per-post IANA timezone override
---
```

### Post Conventions
- Files starting with `_` are ignored by the glob loader
- Subdirectories inside `src/data/blog/` are supported; path segments become URL segments (slugified)
- Directories starting with `_` are excluded from the URL path
- `slug` in frontmatter is no longer used (Astro 5 content layer uses `id` derived from file path)
- Posts with `draft: true` are visible in dev but excluded in production
- Scheduled posts (future `pubDatetime`) are hidden until within `scheduledPostMargin` of publish time

### Slug/URL Generation

- Latin strings: `slugify(str, { lower: true })` — handles numbers/acronyms well
- Non-Latin strings: `lodash.kebabcase` — preserves non-Latin characters
- URL path: `getPath(id, filePath)` — strips `src/data/blog/`, slugifies each path segment, prefixes `/posts`

## TypeScript Path Aliases

```ts
import { SITE } from "@/config";        // resolves to src/config.ts
import Card from "@/components/Card.astro";  // resolves to src/components/Card.astro
```

The `@/` alias maps to `./src/` as configured in `tsconfig.json`.

## Styling Conventions

- **Tailwind CSS v4** via the Vite plugin (no `tailwind.config.*` file — configuration is done in CSS)
- Custom CSS variables defined in `src/styles/global.css`:
  - `--background`, `--foreground`, `--accent`, `--muted`, `--border`
  - Light/dark themes applied via `data-theme` attribute on `<html>`
- Custom utilities: `max-w-app` (max width), `app-layout` (centered layout wrapper)
- Typography for article content handled in `src/styles/typography.css`
- Font: **Google Sans Code** loaded via Astro's experimental font API

## Theme System

- Theme stored in `localStorage` under key `"theme"` (`"light"` or `"dark"`)
- Applied as `data-theme` attribute on `<html>`
- Inline script in `Layout.astro` sets the theme before render to prevent FOUC
- Full theme logic lives in `src/scripts/theme.ts`
- `SITE.lightAndDarkMode` controls whether the toggle button is shown

## Code Quality

### Linting Rules
- ESLint with `typescript-eslint` (recommended) + `eslint-plugin-astro` (recommended)
- **`no-console` is an error** — do not use `console.log` anywhere
- Ignores: `dist/**`, `.astro`, `public/pagefind/**`

### Formatting (Prettier)
- 2-space indentation, double quotes, trailing commas (ES5), LF line endings
- `prettier-plugin-astro` for `.astro` files
- `prettier-plugin-tailwindcss` for class sorting (using `src/styles/global.css` as stylesheet reference)

## Astro-Specific Patterns

- **View Transitions**: Enabled via `<ClientRouter />` in `Layout.astro`. Use `astro:after-swap` and `astro:before-swap` events to re-initialize client-side logic after navigation.
- **`is:inline`**: Used for scripts that must not be bundled (e.g., the progress bar in `PostDetails.astro` uses `is:inline data-astro-rerun`)
- **SVG Icons**: Imported from `src/assets/icons/` as Astro components (e.g., `import IconRss from "@/assets/icons/IconRss.svg"`)
- **Content API**: Use `getCollection("blog")` and `render(post)` from `astro:content`
- **OG Images**: Generated at build time by `src/pages/posts/[...slug]/index.png.ts` using satori + resvg; requires Google font loading

## Commit Conventions

This project uses [Conventional Commits](https://www.conventionalcommits.org/) via Commitizen (`cz.yaml`). Use the format:

```
<type>(<scope>): <description>
```

Types: `feat`, `fix`, `docs`, `style`, `refactor`, `test`, `chore`, `perf`, `ci`

## Docker

The project includes a two-stage `Dockerfile`:
1. Build stage: Node LTS + pnpm, runs `pnpm build`
2. Runtime stage: nginx serving from `/app/dist`

```bash
docker compose up
```

## Environment Variables

Defined in `astro.config.ts` using Astro's type-safe env schema:

| Variable | Type | Description |
|---|---|---|
| `PUBLIC_GOOGLE_SITE_VERIFICATION` | `string` (optional) | Google Search Console verification meta tag |

## Key Design Decisions

- **No test suite**: There are no automated tests. The CI validates via TypeScript type-checking (`astro check`), linting, formatting, and a successful production build.
- **Static output**: The site is fully static (no SSR). All pages are generated at build time.
- **Pagefind**: Full-text search is powered by Pagefind, which indexes the built HTML. The index is generated during `pnpm build` and copied to `public/pagefind/` for development use.
- **`no-console` rule**: Strictly enforced. Use Astro's built-in logging or remove debug statements before committing.
- **Tailwind v4**: No config file — all customization is in CSS. The `@theme inline` block in `global.css` maps CSS variables to Tailwind tokens.
