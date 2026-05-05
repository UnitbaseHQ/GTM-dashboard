# GTM Dashboard

The public ABM coverage tracker for Unitbase. Live at **unitbase-crm.vercel.app**
(URL kept for backwards compatibility with the previous static deployment).

## How this works

The dashboard is a thin static frontend — single `index.html` with inline CSS
and JS. It does not compute anything; it just renders `public/dashboard.json`,
which is written here by the sibling agent system at
[github.com/UnitbaseHQ/unitbase-gtm](https://github.com/UnitbaseHQ/unitbase-gtm)
(the "operator-os" repo).

That repo's `dashboard-publisher` skill runs every weekday morning, computes
four datasets from the master Google Sheet, and pushes a fresh
`public/dashboard.json` here. Vercel watches `main` and auto-deploys.

## Sections

The left nav switches between four views. URL hashes are preserved so each is
shareable (e.g. `…/#coverage`).

- **Hot** (`#hot`) — Top 10 ICP-in companies by signal score, as cards. Each
  card shows the score, top contact (by org-role priority), latest signal, and
  contact count.
- **Funnel** (`#funnel`) — Pipeline counts across LinkedIn connection status
  (`none`, `requested`, `accepted`, `declined`) as horizontal bars, sorted by
  count desc, with the ICP-in total displayed as the hero number.
- **Coverage** (`#coverage`) — Companies × decision-maker personas table. Top
  50 by total contacts. Click any column header to sort. Cell shading: 0 is
  dim, 1–2 is normal, 3+ is bold + accent.
- **Activity** (`#activity`) — Weekly activity stat cards: connections
  requested, connections accepted, DMs sent, replies. Each compares this week
  to last week with a delta arrow.

## Data contract

`public/dashboard.json` shape (all optional except `generated_at`):

```
{
  "generated_at": "ISO timestamp",
  "stats": { "total_contacts": N, "total_companies": N, "icp_in_count": N },
  "hot_accounts": [
    { "company_name", "score", "last_signal_summary", "last_signal_at",
      "contact_count", "top_contact": { "name", "title" } }
  ],
  "funnel": { "none": N, "requested": N, "accepted": N, "declined": N, "total": N },
  "coverage_matrix": [
    { "company_name", "decision_maker", "champion", "influencer", "blocker", "total" }
  ],
  "weekly_activity": {
    "connections_requested": { "this_week": N, "last_week": N, "delta": N },
    "connections_accepted":  { "this_week": N, "last_week": N, "delta": N },
    "dms_sent":              { "this_week": N, "last_week": N, "delta": N },
    "replies":               { "this_week": N, "last_week": N, "delta": N }
  }
}
```

If the fetch fails or any section's data is empty, the dashboard shows a
graceful empty state for that section and "Last updated: Never" in the top bar.

## Repo layout

Everything served by Vercel lives in `public/`. `vercel.json` sets
`outputDirectory` to `public` so it becomes the served root — meaning
`public/dashboard.json` is reachable at `/dashboard.json` and
`public/index.html` at `/`.

```
public/
  index.html
  dashboard.json   ← written daily by operator-os
  favicon.svg
README.md
package.json
vercel.json
```

## Deploy

Vercel watches `main`. Every push auto-deploys.

## Local

```bash
npx serve public   # or: cd public && python3 -m http.server
```

Then open the printed URL.
