# Vendor Management System — Perbadanan Pengurusan Astana Damansara

A single-page contractor governance portal for a Malaysian strata management corporation. Static build, no backend, no build step — it runs straight from GitHub Pages.

## Modules

| Module | What it does |
| --- | --- |
| **Vendor Directory & Compliance** | 12 seeded contractors with live compliance scoring against SSM, CIDB PPK and insurance expiry dates. Table and card views, search, status/trade/grade filters, sortable columns, per-vendor dossier drawer. |
| **Vendor Registration** | Enrolment form enforcing SSM registration, CIDB grade and PPK number, MOF (Treasury) field codes and Bumiputera status, and the mandatory RM 1,000,000 public liability / workmen's compensation limit. |
| **Schedule of Rates (SOR)** | 16 benchmark rate items (min / benchmark / max) traced to the tenders they came from. Searchable, filterable by trade, extendable with custom items. |
| **Rate Comparator & Estimator** | Multi-vendor tender modelling with contingency and preliminaries, variance against the SOR benchmark, and an auto-generated MC council recommendation memo. |

Compliance is computed against today's date, not stored: a contractor is flagged **Expired** if any of SSM, CIDB or insurance has lapsed, **Expiring Soon** if the earliest of those falls within 60 days, otherwise **Compliant**.

## Deploying to GitHub Pages

**Option A — Pages from a branch (simplest)**

1. Create a repository and push these files to `main`.
2. **Settings → Pages → Build and deployment → Source: Deploy from a branch**.
3. Branch `main`, folder `/ (root)`. Save.
4. The site goes live at `https://<username>.github.io/<repo>/` in a minute or two.

**Option B — Pages via Actions**

`.github/workflows/deploy-pages.yml` is included. Set **Settings → Pages → Source: GitHub Actions** and every push to `main` redeploys.

```bash
git init
git add .
git commit -m "Vendor Management System: initial static build"
git branch -M main
git remote add origin https://github.com/<username>/<repo>.git
git push -u origin main
```

## Running locally

Open `index.html` directly, or serve it to avoid any file:// quirks:

```bash
python3 -m http.server 8000   # then visit http://localhost:8000
```

## Notes on the build

- **Everything lives in `index.html`** — markup, Carbon-style CSS tokens, seed data and logic. One file to host, nothing to compile.
- **State persists per browser** via `localStorage` under the key `astana-vms-state-v1`. Registered vendors and custom rate items survive a reload on that device; they are not shared between users. **Reset Baseline** in the top bar restores the seeded data.
- **Two external CDN assets**: IBM Plex fonts (Google Fonts) and the Lucide icon library (jsDelivr). Both degrade gracefully — icon rendering is guarded, so a blocked CDN costs you glyphs, not function. To make the page fully self-contained, vendor those two files locally and repoint the `<link>` / `<script>` tags.
- **Compliance is scored against the live date.** `SYSTEM_DATE` reads the browser's current date (normalised to local midnight), and every displayed date — masthead, renewal notice, memo, dossier audit line, notice reference year — derives from it. Registration expiry defaults land 12 months out rather than on fixed dates.
- **The seeded expiry dates will drift.** The 12 demo contractors were calibrated around November 2026, which currently yields 8 compliant / 2 expiring / 2 expired. As real time moves on, more of them will age into Expired — expected for fixtures. Replace the seed data in `BASELINE_VENDORS` with live certificate dates when you put this into actual use.
- CSV exports (vendor roster, Schedule of Rates) are generated client-side as Blob downloads with a UTF-8 BOM, so `m²` and similar characters open correctly in Excel.

## Data caveat

The seeded contractors, registration numbers, licence dates, policy details and rates are **demonstration fixtures** for an internal management tool. Verify any figure against the actual SSM, CIDB and MOF certificates and the signed tender documents before relying on it for a procurement decision.
