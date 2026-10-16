# 20 · Zameendar — Farm Strategy Sim (idea #77)

**Sixty months on sixteen acres.** A bilingual (English / اردو) turn-based strategy game grounded in Punjabi farming: Kharif and Rabi seasons, a warabandi canal share, soil health per plot, pests and weather, mandi prices with gluts and shocks, and the choice between a bank loan and the arhti's advance. Five years later you are graded on net worth *and* sustainability.

**Scale:** 3926 lines in one HTML file (247 CSS, 3570 JS) · 12 modules · seed data: 13 crops, 13 pests, 21 events, 6 challenges, 7 mechanic articles, 16 glossary terms, 5-step tour, 10 achievements.

## Getting started

- Open `index.html` in any modern browser. No build step, no server, no dependencies beyond Google Fonts (falls back to system fonts offline).
- Everything is in `localStorage` under the `zmd:` prefix: `zmd:auto` (current game), `zmd:slot1..3` (saves), `zmd:settings`, `zmd:lang`, `zmd:hall` (completed runs), `zmd:tourDone`.
- Reset: **Settings → Reset everything**, or clear site data. Export/import a single game from **Save / load**, or all data from **Settings**.
- Same seed = same weather and prices, so a class can play one seed and compare grades. Challenges use fixed seeds for exactly that.
- Programmatic access: `window.game` exposes the engine (`newGame`, `sow`, `endMonth`, `sell`, `grade`…) for tests and student scripts.

## The problem

Agriculture employs close to 40 % of Pakistan's workforce, yet the decisions that make or break a smallholding — rotation, water budgeting, when to sell, whom to borrow from — are learned by losing seasons. A simulation lets students and young farmers make a season of mistakes in minutes and *see* why monocropping, over-pumping and arhti advances quietly destroy a farm.

## What is built

| Area | Features |
|---|---|
| Farm (SVG) | 4×4 isometric grid of one-acre plots; soil-health tint; procedural vector sprites for 13 crops that change with growth stage; badges for ready / pest / thirsty / waterlogged; keyboard selection; grow-in, coin-burst, month-sweep and card slide-in motion |
| Turn engine | One month per turn, 60 turns; canal share with seasonal curve, difficulty and random factor; proportional water allocation, tubewell top-ups (diesel or solar), waterlogging; soil drain/recovery, monocropping penalty, rotation bonus; growth, ripeness, over-ripeness and field loss |
| Pests & weather | 13 seasonal pests with spread to neighbours, curative and preventive sprays; 21 events (heatwave, untimely rain, hail, flood, drought, desilting closure, frost, fog, locusts, support price, procurement stop, export ban/boom, fertiliser hike, import dump, diesel hike, labour shortage, mill delay, subsidy) with bilingual advisories |
| Market | Price index per crop (mean-reverting random walk, inflation drift, glut/lean seasonality, news shocks); mandi sale with 3 % arhti commission; storage cost and spoilage; sugarcane mill receivables paid two months later; 12-month sparklines and a 3-crop price-history chart |
| Finance | Bank loan (credit score, cap, 15 % APR) vs arhti advance (2 %/month implicit, forced sales, auto-covers shortfalls); crop insurance; solar tubewell; combine rental; monthly household/abiana overhead; income by crop and spending by category |
| Season planner | Pencil a crop per plot, see sowing cost vs cash, expected revenue and margin, a six-month water-need vs canal-share chart and a bilingual advisor (deficit months, cash gap, monocropping, perishable glut, rice overload); one click sows what is in window |
| Reports & print | Net worth, sustainability, water efficiency, diversity; cash line, soil bars, water pairs, price history, income/expense splits, yearly report cards, events log; a printable farm report with plot register, stock, receivables and debt |
| Scoring | Financial score against a benchmark (starting cash + Rs 90k/acre/year), sustainability = soil + water efficiency + diversity, letter grade; 10 achievements; yearly report card; final screen |
| Challenges & hall of fame | Six fixed-seed scenarios with objectives (drought soil, escape the arhti, rotation purist, water efficiency, mango orchard, crorepati); every finished run is recorded locally with grade, net worth and seed |
| Almanac | 13 crop cards with sprites and numbers, sowing calendar strip, 7 mechanic articles, 16-term glossary, full-text search — all bilingual and labelled as illustrative |
| Inbox | Every advisory, pest alert, market shock, payment and ready-crop notice filed with read state, type filters and an unread badge in the nav |
| Tutorial & tour | First-year contextual tips (welcome, water, harvest, market, rotation, arhti trap, year end) and a five-step spotlight tour of the farm screen |
| Settings & data | Language, compact money, water unit (acre-inch / m³), reduced motion, tips, confirmations, event cards, auto tour, sheet behaviour; export/import all data; reset; keyboard shortcuts sheet |
| i18n | Full English/Urdu dictionary (~430 keys), Noto Nastaliq Urdu, RTL layout in Urdu mode, bilingual log and inbox entries |

## Architecture (single file)

```
index.html
├─ <style>      tokens (olive / field / soil / sky / clay paper), motion kit, components,
│               game grid (HUD · farm · panel · ticker), SVG farm styles, tour, print, mobile sheet
├─ <body>
│  ├─ icon sprite (Lucide-style symbols used by the app + custom bug / warehouse / factory)
│  ├─ <header> brand, nav, save button, language toggle
│  ├─ <main #view> one renderer per route
│  └─ scrim, modal, event-card stack, fx layer, toasts, mobile FABs, mobile nav
└─ <script>
   ├─ utils         $, store (zmd: prefix), esc, money/water formatters, ic(), toast, motion helpers, seeded RNG
   ├─ settings      S (DEFAULT_SETTINGS), saveSettings
   ├─ i18n          L.en / L.ur, t(), tx(), month names
   ├─ data          CROPS, PESTS, DIFF, CANAL, RAIN, constants, EVENTS, CHALLENGES, TOUR, MECH, GLOSSARY
   ├─ engine        newGame · price/seasonal/mods · plot actions · endMonth (water → growth → finance → events → pests → market)
   │                · scoring (netWorth, sustainability, grade, achievements) · hall of fame · tutorial · persistence
   ├─ farm          isometric SVG (plotTile, cropSprite, badges), HUD, action panel, log, fx (coins, sweep, cards)
   ├─ market/bank   modal infra, renderMarket, renderBank, water modal, save/load modal, JSON export/import
   ├─ charts        sparkline, lineChart, barChart, pairBars, shareBars; renderReports, renderPrint, year/final modals
   ├─ almanac/new   renderAlmanac (search, calendar, glossary), renderAbout, renderNew, renderChallenges
   ├─ planner…      renderPlanner + advisor, renderInbox, renderSettings, tour, keyboard shortcuts
   └─ router        NAV, ROUTES, route(), refresh(), applyLang(), window.game API, boot
```

## Module map

| Module | Key functions | Storage |
|---|---|---|
| Farm | `renderFarm`, `refreshFarm`, `plotTile`, `cropSprite`, `hudHTML`, `panelHTML`, `fxCoins`, `fxSweep`, `showCards` | `zmd:auto` (via engine) |
| Engine | `newGame`, `sow`, `irrigate`, `fertilize`, `spray`, `harvest`, `fallow`, `endMonth`, `resolveWater`, `growPlot`, `rollEvents`, `rollPests`, `updateMarket`, `monthlyFinance`, `yearEnd`, `finishGame` | `zmd:auto` |
| Market | `renderMarket`, `sell`, `storageHTML`, `receivablesHTML`, `effectsHTML`, `drawPriceHistory` | — |
| Bank | `renderBank`, `takeLoan`, `repayLoan`, `takeAdvance`, `repayAdvance`, `buyInsurance`, `buySolar`, `setOption`, `openWaterModal` | — |
| Planner | `renderPlanner`, `planWater`, `planSummary`, `planAdvice`, `applyPlan` | `g.plan` |
| Reports & print | `renderReports`, `lineChart`, `barChart`, `pairBars`, `shareBars`, `renderPrint`, `openYearReport`, `openFinal` | `g.stats`, `g.years` |
| Almanac | `renderAlmanac`, `cropCard`, `sowingCalendar`, `mechCards`, `glossaryList`, `filterAlmanac` | — |
| Challenges & hall | `renderChallenges`, `challengeCard`, `hallTable`, `recordRun` | `zmd:hall` |
| Inbox | `notify`, `renderInbox`, `markRead`, `markAllRead`, `unreadCount` | `g.inbox` |
| Settings | `renderSettings`, `saveSettings`, `exportAll`, `importAll`, `resetAll`, `storageUsed` | `zmd:settings`, `zmd:lang` |
| Tour & shortcuts | `startTour`, `showTourStep`, `endTour`, `maybeStartTour`, `handleKeys`, `openShortcuts` | `zmd:tourDone` |
| Save / load | `saveSlot`, `loadSlot`, `adoptGame`, `slotMeta`, `openSaveModal`, `downloadJSON`, `importJSON` | `zmd:slot1..3` |

## Domain logic notes

- **Water.** `canal = CANAL[month] × difficulty × (1 + 0.15·N(0,1))`, modified by drought (×0.6), January desilting (×0.3) and floods (×1.8). Crops draw in proportion to need; `waterFactor = 0.25 + 0.75 × (need met)`. Irrigating a plot the canal already covers pushes it above 1.35× need → waterlogging (soil −8 per unit excess). Solar covers the first 60 acre-inches a month.
- **Soil.** Per-month drain per crop (rice/cane 1.0 … chickpea −1.5, dhaincha −5 plus +12 when ploughed in), −0.6 extra for monocropping, +1.5 for fallow, +3 when switching crop family, −2 per fertiliser dose (+15 % yield). `soilFactor = 0.55 + 0.45 × soil/100`.
- **Yield.** `base × soilFactor × waterFactor × (1 − damage) × fertiliser × orchardAge × (1 ± variance)`; ready crops lose 12 % a month (30 % for perishables) and are lost after three months.
- **Pests.** Seasonal probability × difficulty × 1.4 if monocropped × 0.3 if preventively sprayed; unsprayed damage = 50 % of the pest's rate in month one, 35 % after; 25 % spread to orthogonal neighbours with the same crop.
- **Prices.** `lvl += (trend − lvl)·0.1 + lvl·vol·N(0,1)` with `trend = 1.005^turn`; glut months ×0.85 (×0.7 for perishables), the two months before ×1.08; news multipliers stack with expiry turns.
- **Finance.** Bank 1.25 %/month on balance, credit −1/month above 70 % of the cap, +1 per mandi sale, +1 per Rs 1 lac repaid; arhti +2 %/month, 5 % commission, 5 % price discount, proceeds repay him first; negative cash at month end becomes arhti debt.
- **Score.** `fin = 50 + 50·log₂(netWorth / par)`, `par = startCash + 90,000 × 16 acres × 5 years`; `sust = 0.4·soil + 0.3·waterEff + 0.3·diversity`; `score = 0.6·fin + 0.4·sust`; A+ ≥ 90, A ≥ 80, B ≥ 65, C ≥ 50, D ≥ 35. A naive "sow anything in window, sell at harvest" policy lands a B/C; storing past the glut, rotating legumes and avoiding the arhti is what earns an A.

## Left for students

| Extension | Difficulty | Start here |
|---|---|---|
| Real mandi price feed (link to the Kisan Mandi Rate Board template or AMIS) with a "last synced" stamp and offline cache | ★★ | `updateMarket()` |
| Climate-scenario mode driven by PMD district series (flood years, droughts) selectable on the new-game screen | ★★ | `CANAL`, `RAIN`, `EVENTS` (TODO after the events array) |
| Livestock & dairy module: herd, fodder from maize/berseem plots, milk income, vaccination calendar | ★★★ | `livestockTick()` TODO at the end of the engine section |
| Multiplayer village market: shared prices and sell orders over WebSockets | ★★★ | `villageMarketSync()` TODO |
| Co-op / kameti financing as a third borrowing option (see projects/12) | ★★ | `kametiFinance` TODO, `renderBank()` |
| Sindhi and Punjabi (Shahmukhi) language packs | ★ | `L.sd`, `L.pa` next to the dictionary |
| Teacher mode with class codes and shared classroom leaderboards built on the local hall of fame | ★★★ | `recordRun()`, `renderChallenges()` |

Search the source for `TODO(student)` to find the exact hooks.

## Data / safety notes

Costs, yields, water needs, prices and event probabilities are rounded illustrative values for Punjab in 2026, chosen so the trade-offs feel right in play. They are not agronomic, financial or trading advice; replace them with Crop Reporting Service, PMD and AMIS data before any real-world use. All state stays on the player's device; nothing is sent anywhere.
