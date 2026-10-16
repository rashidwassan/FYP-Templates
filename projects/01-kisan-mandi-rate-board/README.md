# 01 · Kisan Mandi Rate Board (idea #1)

**Know the rate before the arhti does.** A bilingual (English / اردو) dashboard of daily crop rates across 18 Pakistani mandis, with a Sell Advisor that ranks mandis by the money that actually reaches the farmer, a cost-of-production calculator, a schematic price map, seasonal analytics, a farmer's store tracker, a buyer directory and SMS/print rate sheets.

**Scale:** 3,841 lines in one `index.html` · 13 routed modules + onboarding tour, global search and keyboard shortcuts · seed data: 18 commodities × 18 mandis (324 daily series), 59 districts, 4 vehicles, 18 twelve-month seasonal indices, 18 cost-of-production profiles, 30 buyers, 5 province outlines, 393 bilingual strings.

## Getting started

Open `index.html` in any modern browser. No build step, no server, no dependencies beyond Google Fonts (falls back to system fonts offline). Serve over HTTP only if you later add camera/GPS/notification features.

- **Where data lives:** `localStorage` under the `kmrb:` prefix (see *Settings → Your data* for the live key list). Export/import a JSON backup from the same page.
- **How to reset:** *Settings → Reset everything*, or clear site data in the browser.
- **Demo data:** sample alerts, reports and store holdings are seeded on first open so every module looks alive; switch them off in Settings.
- **First run:** a nine-step spotlight tour starts on the Rates page; restart it any time from Settings. Press `?` for keyboard shortcuts.

## The problem

Farmers usually sell to the nearest commission agent (arhti) without knowing what the same crop fetched that morning in Okara, Lahore or Karachi. That information gap, plus opaque deductions, costs smallholders a large share of their margin every season. Knowing the rate is only half of it: a farmer also needs to know what the crop cost to grow, when the seasonal peak comes, whom to sell to and how to get the rate to people without smartphones.

## What is built

| Area | Features |
|---|---|
| Rates board | 18 commodities × 18 mandis, category/region filters, search, sort, cards or table view, unit toggle (per 40 kg / kg / 100 kg), off-season handling, KPI strip (top gainer, top faller, most volatile, last-synced stamp), Refresh button that tries the live adapter and falls back to the simulated engine |
| Commodity drawer | 30-day multi-mandi SVG line chart (compare up to 3 mandis) with hover tooltips and a dashed moving-average overlay (window set in Settings), seasonal note (best month, months to peak, year-over-year), today's rates by mandi with 1-day/7-day change, one-click alert creation, community reports, cross-links to map, analytics, calculator and store |
| Sell Advisor | Picks the best mandi from your district: gross value − transport (vehicle capacity → trips × km × rate) − arhti commission − mandi fees. Net per maund, delta vs nearest mandi, 7-day trend hint, top-rated buyer at the winning mandi |
| Cost & profit calculator | Per-crop defaults for eight cost heads (seed, fertilizer, pesticide, water, labour, rent, harvest, transport) and expected yield → break-even price per maund, profit per acre, margin, verdict, cost-breakdown bars, profit by mandi, and what-if sliders for yield / price / costs (±30%). Profiles persist |
| Mandi price map | Hand-drawn schematic SVG of Pakistan's provinces; each mandi is a dot coloured and sized by today's price relative to the national average; hover tooltips, click opens the drawer; cheapest/dearest lists and province averages |
| Historical analytics | Seeded 12-month seasonal index per crop → monthly bars this year vs last year (future months flagged as projection), best/worst month, months to peak, YoY %, seasonal swing, 30-day average with moving-average overlay, volatility ranking, YoY table for all crops |
| My Store (portfolio) | Holdings (crop, maunds, valuation mandi or best mandi, cost basis, target value) → store value, 1-day/7-day change, gain vs cost, target status, sell-timing advice combining trend, seasonal upside and storage life |
| Buyer directory | 30 seeded arhtis / mills / exporters / wholesalers with commodities, payment terms, commission, rating, phone and notes; filter by mandi, commodity, type; sort; add your own buyers; rate and annotate any buyer |
| Rate Sheet | Plain-text SMS rate sheet (per-mandi codes or average + change, up to 4 mandis) with character/part counter, copy, `sms:` link and Web Share; printable daily sheet with print CSS |
| Alerts | Above/below price targets per commodity and mandi (or any mandi), evaluated on every app open and pushed to the Inbox |
| Contribute | Farmers report observed rates; outlier warning when far from the modeled price; stored locally |
| Inbox | Notifications with read state: triggered alerts, store targets reached, big daily movers, system notices; de-duplicated per day; unread badges on bell and nav |
| Settings | Language, Day/Night theme, price unit, show off-season crops, alert toast, demo data toggle, moving-average window, JSON export/import, reset, storage usage, restart tour, shortcuts sheet |
| Shell | Onboarding spotlight tour, global search (commodities, mandis, buyers, pages) with keyboard navigation, keyboard shortcuts, More menu / mobile sheet, About panel with module list and student TODOs |
| i18n | Full English/Urdu dictionary (393 keys each), Noto Nastaliq Urdu, persisted language choice |

## Module map

| Module (route) | Main functions | Storage keys |
|---|---|---|
| Rates `#rates` | `visibleCommodities`, `ratesKpis`, `rateCard`, `rateRow`, `ratesTable`, `renderRates`, `syncRates`, `lastSyncLabel` | `filters`, `lastSync` |
| Drawer | `openDrawer`, `renderDrawer`, `drawerHeader`, `drawerSeasonalNote`, `drawerTable`, `drawerReports`, `drawDrawerChart`, `bindDrawer` | `ma` |
| Sell Advisor `#advisor` | `advisorForm`, `advisorRows`, `advisorBest`, `advisorTable`, `renderAdvisor` | `advisor` |
| Calculator `#calculator` | `calcDefaults`, `calcLoad`, `calcCompute`, `calcForm`, `calcSliders`, `calcVerdict`, `calcBreakdown`, `calcMandiTable`, `calcResults`, `renderCalculator` | `calc` |
| Map `#map` | `project`, `ringPath`, `indexColor`, `mapDot`, `mapSvg`, `mapSide`, `renderMap`, `bindMap` | `mapC` |
| Analytics `#analytics` | `monthlySeries`, `seasonalProfile`, `yoyChange`, `movingAverage`, `volatilityRanking`, `analyticsCallouts`, `seasonalGrid`, `volatilityList`, `yoyTable`, `renderAnalytics` | `anC` |
| My Store `#portfolio` | `holdingValue`, `sellTiming`, `monthsToPeak`, `portfolioKpis`, `portfolioForm`, `timingBadge`, `holdingRow`, `renderPortfolio` | `portfolio` |
| Buyers `#buyers` | `allBuyers`, `starsHtml`, `buyerCard`, `buyersToolbar`, `filteredBuyers`, `addBuyerModal`, `rateBuyerModal`, `renderBuyers` | `buyers`, `buyerNotes`, `buyersFilter` |
| Rate Sheet `#sheet` | `sheetConfig`, `smsText`, `smsParts`, `printTable`, `sheetForm`, `renderSheet` | `sheet`, `settings.sheetMandis` |
| Alerts `#alerts` | `evalAlert`, `checkAlertsOnLoad`, `alertRow`, `renderAlerts` | `alerts` |
| Contribute `#contribute` | `reportRow`, `renderContribute` | `reports` |
| Inbox `#inbox` | `pushNotif`, `unreadCount`, `markRead`, `markAllRead`, `clearNotifs`, `updateBadges`, `syncNotifications`, `notifRow`, `renderInbox` | `notifs` |
| Settings `#settings` | `storageUsage`, `exportAll`, `importAll`, `resetAll`, `seedDemoData`, `removeDemoData`, `renderSettings`, `bindSettings`, `applyTheme` | `settings`, `demoSeeded`, `lang` |
| Tour / search / shortcuts | `startTour`, `showTourStep`, `positionTour`, `endTour`, `searchResults`, `renderSearch`, `pickSearch`, `showShortcuts`, `openModal`, `confirmModal` | `tourDone` |

## Architecture (single file)

```
index.html
├─ <style>      design tokens (wheat / soil / leaf) + night-theme overrides, components, map, tour, modal, print
├─ <svg>        inline icon sprite (24×24 stroke icons, used via ic(name))
├─ <header>     brand, primary nav + More menu, global search popover, bell with unread badge, language toggle
├─ <aside>      filters (rates page only); becomes a bottom sheet on mobile
├─ <main #view> rendered by one function per route
├─ overlays     drawer, More sheet (mobile), modal, tour spotlight + card, toasts
└─ <script>
   ├─ utils             $, store (namespaced localStorage with keys/remove), esc, formatters, seeded RNG, haversine, countUp, download/copy
   ├─ settings          SETTINGS_DEFAULT merged with kmrb:settings
   ├─ i18n              L.en / L.ur dictionaries (393 keys), t(), name()
   ├─ seed data         COMMODITIES (+ storage life), MANDIS, DISTRICTS, VEHICLES, SEASONAL, YEAR_LEVEL, COST_ITEMS/COST_PROFILES, BUYERS, PROVINCES
   ├─ price engine      series() → deterministic 30-day random walk per (commodity, mandi), re-seeded daily; summary(); syncRates() adapter
   ├─ analytics engine  monthlySeries(), seasonalProfile(), yoyChange(), movingAverage(), volatilityRanking(), sellTiming()
   ├─ charts            sparkline(), lineChart() (+ dashed series, null gaps), barChart() (grouped, projected fade), hbars()
   ├─ notifications     pushNotif(), syncNotifications(), badges
   ├─ views             rates, drawer, advisor, calculator, map, analytics, portfolio, buyers, sheet, alerts, contribute, inbox, demo data, settings, about
   ├─ shell             modal, onboarding tour, global search, keyboard shortcuts
   └─ nav / router      NAV_PRIMARY, NAV_MORE, ROUTES, route(), applyLang(), applyTheme(), init
```

Data flow: every view reads from `series(commodityId, mandiId)` and the seasonal index. Replacing the simulation with a live feed means implementing one function (`fetchLiveRates`) and caching its result in the same shape; `syncRates()` already records the source and time of the last refresh and the Rates page shows it.

## Domain logic notes

- **Price engine.** Target price = base × mandi multiplier × commodity override. A Mulberry32 PRNG seeded from `commodity|mandi|date` drives a 30-day walk with daily shocks of ± volatility and 8% mean reversion, rounded to Rs 5.
- **Seasonal index.** Twelve multipliers per crop (troughs at harvest). `monthlySeries(crop, year)` = national average × year level (2024 = 0.80, 2025 = 0.89, 2026 = 1.00) × index × ±3% noise; months after the current one are projections.
- **Sell timing.** Perishable (≤ 10 days) → sell now; falling 7-day trend with < 5% seasonal upside → sell soon; peak within storage life and ≥ 5% upside → hold; rising → can wait; otherwise neutral.
- **Break-even.** (Σ cost heads × cost factor) ÷ (yield × yield factor). Profit per acre = (price × price factor − break-even) × yield.
- **Sell Advisor.** Distance = haversine × 1.25 road factor; trips = ⌈qty × 40 kg ÷ vehicle capacity⌉; transport is free under 8 km.
- **Map.** Equirectangular projection (lng 60.5–77.5, lat 23.5–37.5); dot colour interpolates leaf → wheat → red across a 0.92–1.08 price index.

## Data notes

Base prices, cost profiles and buyer entries are indicative 2026 values in PKR that exist only to make the demo realistic. Mandi multipliers encode the usual pattern (producing districts cheaper, metro consumer markets dearer). Phone numbers are placeholders. Province outlines are hand-drawn approximations for orientation, not survey data. Nothing here should be used for real trading decisions until a live source is connected.

## Left for students

| Extension | Difficulty | Start here |
|---|---|---|
| Live data source (AMIS bulletins, scraping, own API) feeding the existing last-synced stamp | ★★ | `fetchLiveRates()`, `syncRates()` |
| Price forecasting (moving average → ARIMA/Prophet → small TF.js model) drawn as a dashed projection | ★★★ | `forecast()` and `drawDrawerChart()` |
| Alert delivery by SMS/WhatsApp via a backend scheduler | ★★ | `checkAlertsOnLoad()` |
| Community report backend: OTP, photo of mandi slip, moderation, MAD outlier rejection | ★★ | `renderContribute()` |
| Real tile map (Leaflet), GPS "use my location", road distance from a routing API | ★★ | `renderAdvisor()`, `renderMap()` |
| Proper RTL layout for Urdu (`dir="rtl"`, logical CSS properties) | ★ | `applyLang()` |
| Installable offline PWA (manifest + service worker) | ★ | add `manifest.json`, `sw.js` |
| SMS gateway / WhatsApp Business delivery of the rate sheet and IVR/USSD access for feature phones | ★★★ | `sheetForm()` → server-side reuse of `smsText()` |

Search the source for `TODO(student)` to find the exact hooks.

## Evaluation ideas for the FYP report

Compare modeled vs. published rates once a live feed is connected; validate the seasonal index against five years of AMIS monthly data; run usability sessions with 5–10 farmers in Urdu mode (tour, calculator, SMS sheet); measure the net-revenue uplift of the Sell Advisor against "sell at nearest mandi" over a season of real data.
