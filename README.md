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
4. Triggers a Vercel production deploy

## Schedules

- **Daily at 00:00 UTC** — fetch new movies from the FilmyFly sitemap
- **Friday at 00:00 UTC** — refresh all download links (tokens expire ~8 days)

Both can also be triggered manually via `workflow_dispatch` with a `mode` input (`new-movies`, `refresh-links`, or `both`).

## Secrets (set in repo Settings → Secrets and variables → Actions)

| Secret name | Purpose |
|---|---|
| `PRIVATE_REPO_PAT` | GitHub PAT with `repo` scope on `hinduaichat/elora-tv` (used to checkout + push back) |
| `VERCEL_TOKEN` | Vercel access token (used to trigger production deploys) |
| `VERCEL_PROJECT_ID` | The Vercel project ID (e.g. `prj_xxxxxxxxxxxxx`) |
| `VERCEL_ORG_ID` | The Vercel team/org ID (e.g. `team_xxxxxxxxxxxxx`) |

No secrets are committed to this repo. All sensitive values are referenced via `${{ secrets.* }}` which GitHub masks in logs.

## Privacy & security

- This repo is **public** but contains **no source code**, **no movie data**, **no API keys**
- The actual website code, trace files, and catalog live in the private `hinduaichat/elora-tv` repo
- The workflow checks out the private repo only at runtime; no private data is ever committed here
- All credentials live in GitHub Actions secrets (encrypted at rest, masked in logs)

## License

Private project.
