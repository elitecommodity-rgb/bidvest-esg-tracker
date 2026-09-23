# Bidvest ESG Tracker

Data capture and reporting tool scoped to 40 of Bidvest Catering Services'
ESG data points, across six categories: **Energy & climate**, **Water**,
**Waste & food waste**, **Packaging & sourcing**, **Health & safety**, and
**Food safety & customer** (see `checklist_items.json`). It is a sibling of
the full Bidvest ESG app (74 data points across Environmental, Social and
Governance), built on the same core principles as Waste Smart and sharing
the identical architecture: a single always-current capture record per data
point, a full history log, evidence files uploaded straight against the
record they support, unit/site administration, non-reporting alerts, and a
live summary dashboard.

Entries can be logged Daily, Weekly or Monthly — whatever cadence a site
actually captures at. Each data point's own official reporting frequency
(Monthly, Quarterly or Annual) defines the compliance period; daily and
weekly entries within that period automatically roll up (sum, average, or
latest — whichever fits the data point) so day-to-day capture adds up to
period compliance without extra steps.

## Local run

```
pip install -r requirements.txt
python3 app.py            # dev server on :5000
```

or production-style:

```
gunicorn -w 2 -b 0.0.0.0:8420 --timeout 30 app:app
```

## Logins

- One bootstrap admin account, set via env vars (`ADMIN_USERNAME` /
  `ADMIN_PASSWORD`, defaults `admin` / `bidvest-esg-tracker-admin` for local
  testing) — full access including Bidvest Admin (units, regions, users,
  alerts) and reports.
- Site/unit users are created by an admin in the Bidvest Admin tab and are
  assigned to a specific unit; they can only capture and view data for
  their own unit.

Every change and upload is attributed to the logged-in user.

## Data

SQLite at `data/bidvest_esg_tracker.db`, evidence files under
`data/uploads/<item_id>/`. On Render this needs the attached persistent
disk mounted at `/app/data` (see `render.yaml`) — without a paid plan and
disk, data resets on every restart/redeploy.

## API

- `POST /api/login`, `POST /api/logout`, `GET /api/session`
- `GET /api/meta`, `GET /api/checklist` — the 40 in-scope data points
- `GET /api/regions`, `POST /api/regions`
- `GET /api/units`, `POST /api/units`
- `GET /api/users`, `POST /api/users`
- `GET /api/status`, `POST /api/flags`
- `POST /api/entry` — log a Daily/Weekly/Monthly entry (with optional
  evidence upload)
- `GET /api/entries`, `GET /api/evidence`
- `GET /api/download/<id>`, `DELETE /api/evidence/<id>` (admin)
- `GET /api/alerts` — non-reporting site alerts
- `GET /api/summary` — category/status and frequency rollups
- `GET /api/reports/<pdf|docx|xlsx>` — scoped to any one of the six
  categories, or all of them together
- `GET /health`
