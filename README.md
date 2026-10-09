<div align="center">

# Elora Auto-Update (Public Runner)

</div>

Public GitHub Actions workflow runner for the **Elora Movie Site**.

## Why this repo is public

GitHub Actions is **free with unlimited minutes for public repositories**. Private repos are capped at 2000 min/month on the free plan. By moving the scheduled workflows here and pointing them at the private `hinduaichat/elora-tv` repo, we get **unlimited auto-updates forever** for $0.

## What this repo contains

Only **`.github/workflows/auto-update.yml`** — a single workflow file that:

1. Checks out the **private** `hinduaichat/elora-tv` repo using a PAT secret
2. Runs the Python scripts that live in the private repo:
   - `fetch_new_movies.py` — traces new movies that appeared in the FilmyFly sitemap
   - `refresh_all_links.py` — refreshes all fffast download tokens (they expire after ~8 days)
   - `generate_movies_data.py` — regenerates the catalog with sitemap_index
3. Commits + pushes the updated traces and catalog back to the private repo
4. The push to `main` triggers Vercel auto-deploy via the project's GitHub integration (configured in Vercel dashboard — no Vercel token needed here)

## Schedules (Nepal Time = UTC + 5:45, so 23:59 Nepal = 18:14 UTC)

- **Daily at 23:59 Nepal Time** (18:14 UTC) — fetch new movies from the FilmyFly sitemap
- **Friday at 23:59 Nepal Time** (18:14 UTC Friday) — refresh all download links (tokens expire ~8 days, so Friday refresh keeps them valid through next Saturday)

Both can also be triggered manually via `workflow_dispatch` with a `mode` input (`new-movies`, `refresh-links`, or `both`).

## Secrets (set in repo Settings → Secrets and variables → Actions)

| Secret name | Purpose |
|---|---|
| `PRIVATE_REPO_PAT` | GitHub PAT with `repo` scope on `hinduaichat/elora-tv` (used to checkout + push back) |

That's it — just ONE secret. Vercel deploys happen automatically via the private repo's GitHub integration with Vercel (configured in the Vercel dashboard), so no Vercel token is needed in this public repo.

## Privacy & security

- This repo is **public** but contains **no source code**, **no movie data**, **no API keys**, **no Vercel tokens**
- The actual website code, trace files, and catalog live in the private `hinduaichat/elora-tv` repo
- The workflow checks out the private repo only at runtime; no private data is ever committed here
- The PAT is referenced via `${{ secrets.PRIVATE_REPO_PAT }}` which GitHub masks in logs
- The git push command strips the token from any error messages via `sed`

## License

Private project.
