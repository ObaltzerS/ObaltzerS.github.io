# ObaltzerS.github.io

Personal portfolio for past, current, and planned projects.

**Stack:** Astro + Vue · builds to static files for GitHub Pages

## Develop

```sh
npm install
npm run dev
```

| Command | Action |
| --- | --- |
| `npm run dev` | Dev server at `localhost:4321` |
| `npm run build` | Production build → `./dist/` |
| `npm run preview` | Preview the build locally |

## Add a project

Edit [`src/data/projects.json`](src/data/projects.json):

```json
{
  "title": "My project",
  "description": "Short summary.",
  "status": "completed",
  "tags": ["Astro", "Vue"],
  "links": {
    "repo": "https://github.com/…",
    "demo": "https://…"
  },
  "date": "2026-07"
}
```

`status` must be one of: `completed` · `in-progress` · `planned`

## Project layout

```text
src/
  components/
    Hero.astro          # static intro
    About.astro         # static bio
    ProjectGrid.vue     # filterable project cards (Vue island)
  data/projects.json    # source of truth for projects
  layouts/Layout.astro
  pages/index.astro
  styles/global.css
```

Interactive filters live in Vue (`client:load`). Layout and copy stay in Astro.

## Deploy

Pushes to `main` build and publish via [`.github/workflows/deploy.yml`](.github/workflows/deploy.yml).  
Repo Settings → Pages → Source: **GitHub Actions**.
