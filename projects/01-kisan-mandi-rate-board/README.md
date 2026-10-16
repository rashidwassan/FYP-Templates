# 01 · Kisan Mandi Rate Board

**Know the rate before the arhti does.** A bilingual (English / اردو) dashboard of daily crop rates across 18 Pakistani mandis, with a Sell Advisor that ranks mandis by the money that actually reaches the farmer.

Open `index.html` in any modern browser. No build step, no server, no dependencies beyond Google Fonts (falls back to system fonts offline).

## The problem

Farmers usually sell to the nearest commission agent (arhti) without knowing what the same crop fetched that morning in Okara, Lahore or Karachi. That information gap, plus opaque deductions, costs smallholders a large share of their margin every season.

## What is built

| Area | Features |
|---|---|
| Rates board | 18 commodities × 18 mandis, category/region filters, search, sort, cards or table view, unit toggle (per 40 kg / kg / 100 kg), off-season handling, KPI strip (top gainer, top faller, most volatile) |
| Commodity drawer | 30-day multi-mandi SVG line chart with hover tooltips (compare up to 3 mandis), today's rates by mandi with 1-day and 7-day change, one-click alert creation, community reports |
| Sell Advisor | Picks the best mandi from your district: gross value − transport (vehicle capacity → trips × km × rate) − arhti commission − mandi fees. Shows net per maund, delta vs nearest mandi and a 7-day trend hint |
| Alerts | Above/below price targets per commodity and mandi (or any mandi), evaluated on every app open |
| Contribute | Farmers report observed rates; outlier warning when far from the modeled price; stored locally |
| i18n | Full English/Urdu dictionary, Noto Nastaliq Urdu, persisted language choice |

## Architecture (single file)

```
index.html
├─ <style>      design tokens (wheat / soil / leaf), components, responsive rules, print
├─ <header>     brand, nav, search, language toggle
├─ <aside>      filters (rates page only); becomes a bottom sheet on mobile
├─ <main #view> rendered by one function per route
└─ <script>
   ├─ utils        $, store (namespaced localStorage), esc, formatters, seeded RNG, haversine
   ├─ i18n         L.en / L.ur dictionaries, t(), name()
   ├─ seed data    COMMODITIES, MANDIS (lat/lng, multipliers), DISTRICTS, VEHICLES
   ├─ price engine series() → deterministic 30-day random walk per (commodity, mandi), re-seeded daily
   ├─ charts       sparkline(), lineChart() — hand-drawn SVG, no library
   ├─ views        renderRates, renderDrawer, renderAdvisor, renderAlerts, renderContribute, renderAbout
   └─ router       hash routes: #rates #advisor #alerts #contribute #about
```

Data flow: every view reads from `series(commodityId, mandiId)`. Replacing the simulation with a live feed means implementing one function (`fetchLiveRates`) and caching its result in the same shape.

## Data notes

Base prices are indicative 2026 values in PKR per 40 kg and exist only to make the demo realistic. Mandi multipliers encode the usual pattern (producing districts cheaper, metro consumer markets dearer). Distances use the haversine formula × 1.25 road factor. Nothing here should be used for real trading decisions until a live source is connected.

## Left for students

| Extension | Difficulty | Start here |
|---|---|---|
| Live data source (AMIS bulletins, scraping, own API) with "last synced" and offline fallback | ★★ | `fetchLiveRates()` |
| Price forecasting (moving average → ARIMA/Prophet → small TF.js model) drawn as a dashed projection | ★★★ | `forecast()` and `lineChart()` |
| Alert delivery by SMS/WhatsApp via a backend scheduler | ★★ | `checkAlertsOnLoad()` |
| Community report backend: OTP, photo of mandi slip, moderation, MAD outlier rejection | ★★ | `renderContribute()` |
| Leaflet map in the Sell Advisor, GPS "use my location", road distance from a routing API | ★★ | `renderAdvisor()` |
| Proper RTL layout for Urdu (`dir="rtl"`, logical CSS properties) | ★ | `applyLang()` |
| Installable offline PWA (manifest + service worker) | ★ | add `manifest.json`, `sw.js` |
| IVR/USSD/SMS access for feature phones | ★★★ | reuse the price engine server-side |

Search the source for `TODO(student)` to find the exact hooks.

## Evaluation ideas for the FYP report

Compare modeled vs. published rates once a live feed is connected; run usability sessions with 5–10 farmers in Urdu mode; measure the net-revenue uplift of the Sell Advisor against "sell at nearest mandi" over a season of real data.
