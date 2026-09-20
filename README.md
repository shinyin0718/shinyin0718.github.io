# shinyin0718.github.io

Personal website built with [Astro](https://astro.build) and [Tailwind CSS](https://tailwindcss.com),
deployed to GitHub Pages at <https://shinyin0718.github.io>.

All content currently in the repo is clearly marked `PLACEHOLDER` — replace it with your own.

## Local development

```bash
npm install
npm run dev      # dev server on http://localhost:4321
npm run build    # static build into dist/
npm run preview  # serve the built site locally
```

Node 22 or newer is required (the deploy workflow uses Node 22).

## Project structure

```
public/                     static files served as-is (resume.pdf, images, favicon)
src/components/             SEO, header, footer, theme toggle, case study card
src/content/case-studies/   one markdown file per case study
src/content.config.ts       Zod schema for the case studies collection
src/data/cv.json            CV data rendered on /about
src/layouts/BaseLayout.astro  shared <head>, header, footer, dark mode bootstrap
src/pages/                  routes
src/styles/global.css       Tailwind import, colour palette, dark mode variant
```

## Adding a new case study

Create `src/content/case-studies/my-project.md`. The filename becomes the URL slug
(`/projects/my-project`). The frontmatter must match the schema in `src/content.config.ts`:

```md
---
title: 'My project'
summary: 'One line that shows on the cards.'
role: 'What you did'
timeframe: 'Jan 2026 — Mar 2026'
tools: ['Python', 'Postgres']
thumbnail: '/images/case-studies/my-project.png'
link: 'https://example.com'   # optional
featured: true                # optional, default false
order: 3                      # optional, controls sort order (ascending)
---

## Problem
## Approach
## Outcome
```

The body is regular markdown (`.mdx` also works if you want components). Put images in
`public/images/case-studies/` and reference them with an absolute path.

The home page shows the first three case studies by `order`; `/projects` shows all of them.

## Updating the CV

Edit `src/data/cv.json`. Its shape is:

- `name`, `tagline`, `location`, `summary`
- `experience[]` — `company`, `role`, `dates`, `bullets[]`
- `education[]` — `institution`, `qualification`, `dates`, `details`
- `skills` — `languages[]`, `tools[]`, `domains[]`

`/about` renders directly from this file, so no template changes are needed.

## Replacing the résumé and profile photo

- Résumé: overwrite `public/resume.pdf` (keep the filename; the "Download résumé"
  button on `/about` links to `/resume.pdf`).
- Profile photo: drop your image in `public/images/` and update the `src` on the
  `<img>` in `src/pages/index.astro`. Keep the `alt` text descriptive.
- Favicon and social preview: `public/favicon.svg` and `public/og-image.svg`.

## Theming

The palette is a neutral slate base with a single teal accent, defined as
`--color-accent-*` in the `@theme` block of `src/styles/global.css` (Tailwind v4's
CSS-first config). Change those nine values to re-skin the whole site.

Dark mode is class based: `.dark` on `<html>`, toggled by
`src/components/ThemeToggle.astro` and persisted in `localStorage`. An inline script in
`BaseLayout.astro` applies the stored (or system) preference before first paint.

## Deployment

`.github/workflows/deploy.yml` runs on every push to `main`: it installs dependencies,
runs `astro build`, uploads `dist/` with `actions/upload-pages-artifact`, and publishes it
with `actions/deploy-pages`.

One-time setup: in **Settings → Pages → Build and deployment**, set the source to
**GitHub Actions**. No CNAME file is used — the site stays on the `*.github.io` subdomain,
which is why `astro.config.mjs` needs no `base` path.
