# GTM Dashboard

The public ABM coverage tracker for Unitbase. Live at **unitbase-crm.vercel.app**
(URL kept for backwards compatibility with the previous static deployment).

## How this works

The dashboard is a thin static frontend. It does not compute anything — it just
renders `public/dashboard.json`, which is written here by the sibling agent
system at [github.com/UnitbaseHQ/unitbase-gtm](https://github.com/UnitbaseHQ/unitbase-gtm)
(the "operator-os" repo).

That repo's `dashboard-publisher` skill runs every weekday morning, computes the
four datasets (hot accounts, funnel, coverage matrix, weekly activity) from the
master Google Sheet, and pushes a fresh `public/dashboard.json` here.

## Deploy

Vercel watches `main`. Every push auto-deploys.

## Local

```bash
npx serve .   # or any static server
```

Then open the printed URL.
