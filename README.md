# CEOCI stated-goal signal agent: team site

DRG's front end for the **CEO stated-goal signal agent** built for CEO Coaching International.
The agent finds CEOs who publicly state a numeric growth goal in news, press releases and interviews,
verifies the quote word for word, checks the company against the ICP with sources, scores it, and writes
the email and LinkedIn copy from verified facts only.

This branch (`staging`) holds the site. `main` intentionally has no site, so no production version exists.

## What the site does

- Team sign-in with a shared access code (stored in the browser after the first entry)
- Live signal list with status (Qualified, Review, Rejected), score and the reasons behind it
- ICP: private US/Canada companies with 50+ employees and sourced annual revenue of $10M–$200M
- **Financial snapshot** per company: revenue (with basis: company-stated, press-reported or third-party estimate), headcount now vs. earlier, funding and growth facts, each linked to its source. Private companies publish no P&L, so these are sourced estimates, never invented
- Challenge-style copy built from the CEO's own numbers (pace needed vs. recent pace)
- **Personalized assets:** a 3-slide PPT (goal, the math, who has done it) and a 1080×1080 image card, generated in the browser from the checked facts
- Revenue filter ($10M–$20M, $20M–$50M, $50M–$200M, outside band, not found)
- For each signal: the stated goal (from, to, by when) and pace, the exact quote, the CEO and LinkedIn,
  company size, ownership and HQ with sources, plus Email 1, the LinkedIn note and the LinkedIn DM with Copy buttons
- Filters by status and goal type, search, **Download all as CSV** and **CSV of this view** (32 columns)
- **Run agent now** (full news scan) and **Check article links** (up to 10 URLs) with live progress
  and the failure reason when a run stops
- If the same article is checked twice, only the newest result is shown

## Where it runs

- **GitHub Pages (from this `staging` branch, `/docs` folder):**
  `https://dollar-revenue-group.github.io/ceoci-signal-agent/`
  Every push to `staging` republishes it within a minute or two.
- **Copy served by n8n:** `https://n8n-bkfvq-u78038.vm.elestio.app/webhook/ceoci-agent/app`

## How it is wired

| Piece | Where |
|---|---|
| Agent | n8n workflow "CEOCI · Stated-goal signal agent" (folder `CEOCI_Signals_Jay`), weekdays 6:00 AM ET |
| Site API | n8n workflow "CEOCI · Agent site API" (same folder) |
| Signals | n8n data table `ceoci_goal_signals` |
| Run log | n8n data table `ceoci_agent_runs` |
| Site page (n8n copy) | n8n data table `ceoci_site_files` (row `index.html`) |

API endpoints (all under `https://n8n-bkfvq-u78038.vm.elestio.app/webhook/ceoci-agent/`).
Every call needs `?code=<team access code>`; a wrong code gets HTTP 401.

| Method | Path | Returns |
|---|---|---|
| GET | `prospects` | `{ ok, rows: [...] }` newest first |
| GET | `status` | `{ ok, runs: [...] }` last 8 runs started from the site |
| POST | `run` | `{ ok, run_id, kind }`, or `{ ok: false, reason: "already_running" }`. Add `&urls=<comma-separated, URL-encoded>` to check specific articles (max 10). |

## Changing the site

1. Edit `docs/index.html` on this branch and open a pull request into `staging`.
2. Merging into `staging` updates GitHub Pages automatically. To refresh the n8n copy too:
   ```sh
   N8N_PUBLISH_CODE=<admin code> sh scripts/publish-to-n8n.sh
   ```
   The admin code is not stored in this repo. Ask Jay.

## Optional: Netlify branch deploy (0 credits to create)

If you connect this repo to a Netlify project, `netlify.toml` cancels every production build,
so only the `staging` branch deploy is built (`staging--<site>.netlify.app`).
In Netlify: Project configuration → Build & deploy → Branches and deploy contexts →
Branch deploys: **Let me add individual branches** → `staging`.

## Good to know

- Scheduled weekday runs add signals but are not listed in the site's run status (only site-started runs are).
- Some news sites block automated reading. Those signals are checked against the news snippet only and go to Review.
- Rotate the team access code in the three "Code OK?" checks of the site API if it is shared outside the team.
