# 29 · Zer-e-Zameen — Village Groundwater Twin (idea #92)

**A digital twin of a village aquifer for farmers, water committees, agriculture extension staff and hydrology students, in Urdu and English.** An animated geological cross-section and a depth-to-water map show the water table under the fields moving from 2021 to 2036. Underneath is a real 2-D finite-difference groundwater model (implicit monthly steps solved by successive over-relaxation, canal leakage, evaporation, no-flow and constant-head edges, a water balance every month), calibrated against five years of well readings, driven by FAO-56 crop water demand with Hargreaves ET0, and used by a ten-year scenario lab, a fair quota allocator with a credits ledger, and a full-screen village meeting mode with voting and printable minutes. زیرِ زمین اردو اور انگریزی دونوں میں ہے، اردو میں مکمل دائیں سے بائیں ترتیب کے ساتھ۔

**Scale:** 7,889 lines in one `index.html` (≈ 7,050 lines of JavaScript including ≈ 1,200 lines of bilingual strings and content, ≈ 710 of CSS) · 14 routed modules plus the full-screen meeting stage, search palette, spotlight tour, shortcuts sheet and three print layouts · 938 bilingual strings · 6 bilingual guides and a 12-question quiz · 86 sprite icons · seed of 2 fictional villages on 40 × 30 grids (2 × 1,200 cells, 6 aquifer zones), 120 fields (5,060 acres), 141 tubewells and 4 piezometers, 28 farmers, 1,149 monthly depth readings (5 years × 20 monitored wells, with noise and deliberate outliers), 12 credit trades, 2 past meetings, 10 crops, 6 district climate normals · 8 student TODOs.

## Getting started

- Open `index.html` in Chrome, Edge, Firefox or Safari. It works from `file://` and fully offline; only Google Fonts (Bitter, Mulish, Noto Nastaliq Urdu) come from the network and fall back to system fonts.
- On first open the model runs for 180 months (history November 2021 to October 2026, then a business-as-usual projection to 2036). This takes about a second; a skeleton shows meanwhile and the chip in the top bar reports the largest water-balance error.
- A spotlight tour runs on the first visit (replay it from Settings, About or the More sheet). The top-bar button (or `L`) switches to Urdu with a right-to-left layout; `T` toggles the dark "deep soil" theme.
- **Optional network features, only on request:** the street map lazy-loads Leaflet 1.9.4 from unpkg and OpenStreetMap tiles; if that fails (offline, blocked) it falls back to the offline schematic. "Use my location" needs Geolocation permission and, in most browsers, a secure page (`https://` or `localhost`): serve the folder with `npx serve .` to try it on a phone. Fullscreen for the meeting mode is requested when available and skipped silently otherwise.
- **Where data lives:** `localStorage` under the `zez.` prefix — `settings`, `villages` (grids, zones, fields, wells, farmers, calibration, solver), `cur`, `readings`, `ledger`, `meetings`, `scen` (scenario levers and results), `calibLog`, `quota`, `quiz`, `inbox`, `log`, `meta`, `lang`.
- **Backup and reset:** Settings → Export JSON / Import JSON; "Demo villages" off leaves one blank village to build your own; "Reset everything" restores the demo villages.

## The problem

Groundwater carries much of Pakistan's irrigation, and pumping has exploded:

- Punjab's farm tubewells grew from about **334,000 in 1994 to over 1.2 million in 2024** ([Dawn](https://www.dawn.com/news/amp/1910479)).
- The **Agricultural Census 2024 counted 1,832,232 tubewells and lift pumps, 912,327 of them solar** ([The Friday Times](https://www.thefridaytimes.com/07-Sep-2026/2050-begins-tubewell)).
- Solar pumping, with almost free extra hours, is **linked to falling water tables**, a link the power minister has disputed ([Arab News](https://www.arabnews.com/node/2617737/amp)); **Gallup Pakistan recommends quotas or permits in stressed zones** ([Gallup Pakistan](https://galluppakistandigitalanalytics.com/2026/01/28/tube-wells-from-solution-to-new-challenge-in-pakistans-agriculture/)).

Rules like quotas only work if the people who must follow them share a picture of their own aquifer: where the water comes from, where it goes, which wells will dry first and what each option costs. Zer-e-Zameen builds that picture for one village, in Urdu and English, honest about being a simplified, illustrative model.

## What is built

| Area | Features |
|---|---|
| Village twin (home) | Animated geological cross-section (topsoil, clay, sand, saturated zone, aquifer base, canals, houses, crops by season, monsoon rain) with tubewells as pipes and pump intakes coloured safe / at risk / dry; plan-view heatmap of depth to water (or change since 2021) with marching-squares contours and pulsing dry wells; a month slider from 2021 to 2036 with play/pause; KPIs (mean depth with 12-month change, wells at risk, wells dry, pumping, crop income and pumping cost, water-balance error); insights (fitted trend, mean monsoon rebound, five wells closest to drying with the month they go dry, recharge against pumping); printable village report |
| Village setup | 40 × 30 grid of 100 m cells; tools to paint land use (field, abadi, canal, distributary, pond, road, outside model), aquifer zones and field parcels with 1–3 cell brushes and undo; place, edit, move and delete tubewells (owner, power electric / diesel / solar, horsepower, discharge in L/s or cusec, pump setting, bore depth, year, monitored) and piezometers; field forms (owner, rabi and kharif crops, irrigation method, canal command, serving well); zone table (Sy, T, base, initial depth → initial head); canal allowance, watercourse efficiency, stage, bed and conductance; rain-year factors, pond seepage, ET extinction depth, unconfined T; no-flow / constant-head edges with a regional trend; farmers; new, duplicate, export and import villages |
| Readings | Monthly depth-to-water per monitored well; m / ft toggle; per-well chart with readings, simulated depth and monsoon bands; outlier detection by a moving-median residual and median absolute deviation (modified z-score, limit configurable) with include / exclude; KPIs (latest, 12-month change, least-squares trend, monsoon rebound); pre-monsoon (May–June) against post-monsoon (September–October) table and an all-wells rebound chart; add / edit / delete with validation; CSV import with header mapping, unit detection, de-duplication and row validation; CSV export; notices for outliers and readings below the pump intake |
| Aquifer model | `Sy ∂h/∂t = ∂/∂x(T ∂h/∂x) + ∂/∂y(T ∂h/∂y) + R − Q` on 1,200 block-centred cells, backward-Euler monthly steps, SOR with ω and tolerance controls; unconfined T = K × saturated thickness (lagged); canal and distributary cells as river-type head-dependent leakage; groundwater ET with an extinction depth; constant-head edges with a regional trend; wells stop when the water table reaches 1 m above the pump intake; plan layers (depth, head, change since 2021, pumping drawdown against a no-pumping counterfactual run); draw a section line by dragging, then play the months along it; cell inspector; water-balance table per water year (rain, canal seepage, watercourse losses, return flow, ponds, edge inflow, storage | pumping, ET, edge outflow, canal drain, storage) with closure error; closure and iteration charts; ω comparison (Gauss–Seidel to 1.9); verification panel (zero stress, closed box, symmetric cone, Theis benchmark, village balance); tubewell table with margin, hours and first dry month |
| Calibration | RMSE, bias, MAE, R² against observed depth; observed-vs-simulated scatter with a 1:1 line; error per well; residual time series per well; manual Sy / T multiplier trials; auto-calibrate: a 7 × 7 grid search (49 five-year runs) chunked across animation frames with a progress bar and a live RMSE surface; apply best; calibration history. The seeded readings come from a hidden "true" run (Sy × 0.8, T × 1.25 in Nooranwala), so students can see the search recover it |
| Crop water | Monthly Tmax, Tmin, Ra (from latitude and day of year), Hargreaves ET0, rain and USDA-SCS effective rain for 6 district normals; formulas; crop library of 10 crops with FAO-56 stage lengths, Kc curves, sowing dates, yields, prices and Ky (editable, persisted, resettable); village groundwater demand by month stacked by crop; field table (ETc, effective rain, canal, net, gross, groundwater m³); pumping hours per well per month with capacity flags |
| Scenario lab | Ten-year projections from today's heads with the same rain sequence: business as usual, rice → maize (50 %), solar adoption (60 % of diesel and grid wells), per-acre quotas (800 mm/yr), turn rotation (rest 1 day in 4), laser levelling / AWD / drip (80 % of flood fields), 12 recharge ponds, drought years 3–5, and "my mix" with six sliders and a drought switch; small multiples on a shared scale with the business-as-usual line; ranking table (depth 2036, change vs usual, dry wells, pumping, energy cost, crop income, net) with a transparent 0–100 score, sortable; dry-well and energy-by-source charts; send the top options to the meeting |
| Budget and quotas | Hand-drawn Sankey of the aquifer budget for any water year with a numbers list in Mm³ and mm; sustainable yield (pumping that would have kept storage level), overdraft and fall; fair quota allocator (acreage / crop-need weight, equity floor, reserve) per farmer in m³ and acre-inches with recent use and credits; "test this allocation in the model" (ten years with per-well caps); tradable-credits ledger with validation against available credits, CSV export and a printable quota sheet |
| Village meeting | Meeting form (title, date, place, chair, people present, agenda, options), vote tally with ± buttons; full-screen presentation with eight slides (title with cross-section, where we stand, five-year history, where our water goes, ten years ahead, options with outcomes, vote, decision) captioned in Urdu with English underneath; arrow keys and Esc; closes the vote and records the decision; minutes list and printable minutes with signatures |
| Map | Offline schematic of the grid with coordinates at the corners, north arrow and 1 km scale; set the centre by typing, by Geolocation or by clicking the Leaflet map; lazy Leaflet 1.9.4 with the village rectangle and well markers |
| Learn | Six bilingual guides (what an aquifer is, why water tables fall, saline intrusion, recharge, alternate wetting and drying, laser levelling) with the other language one tap away, and a 12-question quiz with explanations and score history |
| Platform | Settings (language, theme, text size, reduced motion, depth and discharge units, risk margin, outlier limit, illustrative energy prices, demo data, JSON export / import / reset), spotlight tour, notifications inbox and activity log, global search (pages, actions, villages, wells, fields, farmers, guides, crops), keyboard shortcuts, aria-live announcements, focus-trapped keyboard-closable modals, print CSS for three reports |

## Module map

| Module | Key functions | Storage keys |
|---|---|---|
| Reference data | `DISTRICTS`, `CROPS`, `METHODS`, `PUMP_TYPES`, `CELL_TYPES` | – |
| Hydrology | `extraterrestrialRa`, `hargreavesET0`, `effectiveRain`, `climateTable`, `cropDaily`, `cropMonthly`, `fieldMonthlyCoeffs`, `fieldNeedMm`, `canalMmAtField`, `wellHoursCap`, `pumpingEnergy` | `settings.crops` |
| Aquifer model | `initialDepth`, `buildSpec`, `conductances`, `solveStep`, `headDependent`, `budgetStep`, `testSpec` | `villages[].solver`, `villages[].calib` |
| Simulation | `stressContext`, `monthStress`, `naturalRecharge`, `fieldDemand`, `wellPumping`, `returnFlowAndLedger`, `closeWaterYear`, `simulate` | – |
| Verification | `verifyZeroStress`, `verifyClosedBox`, `verifySymmetricCone`, `wellFunction`, `verifyTheis`, `verifyVillageBalance` | – |
| Run manager | `ensureRun`, `mergeRuns`, `markStale`, `wellStatus`, `firstDryMonth`, `twinKpis`, `afterRun`, `outlierScores`, `observations`, `calibStats`, `runScenario`, `runScenarios` | `inbox` |
| Seed | `genVillage`, `genGrid`, `genFields`, `genWells`, `seedReadings`, `seedLedger`, `seedMeeting`, `blankVillage` | `meta` |
| Charts and maps | `lineChart`, `barChart`, `scatterChart`, `hoverLayer`, `sankeyChart`, `paintHeat`, `marchingSquares`, `contourSvg`, `paintPlan`, `crossSectionSvg` | – |
| Twin | `renderHome`, `updateTwin`, `startPlay`, `planLayer`, `homeInsights`, `printVillageReport` | `villages[].section` |
| Setup | `renderSetup`, `paintEditor`, `paintAt`, `undoEdit`, `openWellForm`, `openFieldForm`, `openFarmerForm`, `panelZones`, `panelBounds`, `importVillage` | `villages`, `cur` |
| Readings | `renderReadings`, `readingsChart`, `monsoonRows`, `toggleReading`, `openReadingForm`, `importReadingsCsv`, `exportReadingsCsv` | `readings`, `settings.units` |
| Model page | `renderModel`, `updateModelView`, `bindSectionDraw`, `ensureNoPumpRun`, `yearBudgets`, `compareOmega`, `runVerification`, `wellTable` | `villages[].solver` |
| Calibration | `renderCalib`, `autoCalibrate`, `rmseGridHtml`, `applyCalibration`, `errorStats` | `villages[].calib`, `calibLog` |
| Crop water | `demandTable`, `renderCrops`, `hoursTable`, `openCropForm`, `applyCropOverrides` | `settings.crops` |
| Scenario lab | `renderScenarios`, `runLab`, `smallMultiple`, `rankRows`, `sendToMeeting` | `scen` |
| Budget and quotas | `renderBudget`, `sustainableYield`, `allocate`, `wellQuotas`, `testAllocation`, `renderLedger`, `openTradeForm`, `printQuotaSheet` | `quota`, `ledger` |
| Meeting | `openMeeting`, `renderMeeting`, `closeVote`, `startStage`, `renderStage`, `stageSlide`, `printMinutes` | `meetings` |
| Map | `villageBounds`, `schematicSvg`, `loadLeaflet`, `mountLeaflet`, `locateMe` | `villages[].lat/lng` |
| Learn | `renderLearn`, `renderGuide`, `renderQuiz`, `finishQuiz` | `quiz` |
| Platform | `renderInbox`, `renderSettings`, `exportAll`, `importAll`, `resetAll`, `openPalette`, `startTour`, `onKey`, `renderAbout` | `settings`, `inbox`, `log`, `lang` |

## Architecture

```
index.html
├─ head: fonts (Bitter, Mulish, Noto Nastaliq Urdu), CSS tokens (soil, clay, wheat, aquifer blues, leaf; dark "deep soil")
├─ CSS: motion kit, shell (top bar with village switcher, strata nav, phone tabs), cards with core-sample strips,
│        KPIs, forms, tables, overlays, twin (cross-section, plan), editor, charts, meeting stage, print, responsive
├─ inline SVG sprite (86 icons, generated from the icons the code uses)
├─ shell: top bar, nav, view, tabs, stage, modal, palette, tour, tooltip, toasts, live region, print area
└─ script
   ├─ utils: storage (zez.), escaping, statistics (median, MAD, regression), units and rupees (lakh / crore),
   │         toasts, motion helpers, tooltips, modal + focus trap, menus, print, download, CSV
   ├─ i18n: STR [English, Urdu] pairs, t(), TXT.en / TXT.ur views; two string tables; learn content
   ├─ reference data · seeded village generator · application state, notices and demo toggle
   ├─ hydrology (Ra, Hargreaves, effective rain, Kc curves, demand, energy)
   ├─ aquifer model (spec, conductances, SOR, head-dependent terms, budget) · simulation loop · verification
   ├─ run manager (cache, merge, well status, outliers, observations, calibration stats, scenarios)
   ├─ charts (line, bar, scatter, Sankey, hover layer) · heatmap + marching squares · cross-section
   ├─ views: twin · setup (editor, panel, forms) · readings (+ io) · model (+ tools) · calibration · crops ·
   │         scenarios · budget (+ ledger) · meeting (+ stage) · map · learn · inbox · settings ·
   │         search, tour and shortcuts · about and student stubs · reports
   └─ router, navigation, preferences, village switcher, boot
```

## Domain logic and algorithm notes

**Groundwater flow.** For each active cell *i* with area *A*, storage coefficient *Sc = Sy·A/Δt* and face conductances *C = T_face* (harmonic mean of the two cells' transmissivities on square cells), backward Euler gives

```
(Sc + Σ C_k + C_riv + e) h_i − Σ C_k h_k = Sc h_i(old) + R_i A − Q_i + C_riv H_canal + e (z_s − d_ext)
```

where the canal term uses `C_riv (H_canal − h)` while the head is above the canal bed and the constant `C_riv (H_canal − z_bed)` once the water table is disconnected (MODFLOW river style), and the evaporation term *e = ET_max / d_ext* applies only while the water table is within the extinction depth. Both are re-linearised at every iterate. SOR sweeps rows: `h ← h + ω (rhs/diag − h)` until the largest change is below the tolerance (default 10⁻⁵ m, ω = 1.8, ≈ 70 iterations per month). Unconfined transmissivity is `K × (h − base)` from the start of the step (floored at 10 % of its initial value).

**Water balance.** Per step: IN = rain + watercourse + return + ponds + canal leakage in + edge inflow + storage decrease; OUT = pumping + ET + canal drain + edge outflow + storage increase; closure = 100 (IN − OUT) / ((IN + OUT)/2). The seeded villages close at about 10⁻⁴ % every month.

**Stresses each month.** Rain recharge = 0.5 × (P − 25 mm) on fields (other land uses have their own share); watercourse losses (1 − efficiency of the canal water at the field) seep under commanded fields; ponds seep 10 mm/day in July–September; for every field, ETc = Kc × ET0, net = max(0, ETc − Peff), rice adds 2.5 mm/day of ponding percolation and 150 mm of land preparation (cut 40 % by AWD); gross = net / application efficiency + ponding water; canal water at the field = allowance (cusec per 1,000 acres) × month supply share × watercourse efficiency; the rest is groundwater demand, scaled by the pump's "appetite" (diesel is rationed to 80 %, solar pumps 25 % more because the extra hours are free). Each well pumps `min(demand, discharge × hours available, quota left)`, nothing if its water level is within 1 m of the pump intake. Applied water not consumed returns 70 % to the aquifer. Crop income uses FAO-33: `Ya/Ym = 1 − Ky (1 − supplied / required)`.

**Hargreaves ET0** = 0.0023 (T_mean + 17.8) √(T_max − T_min) × 0.408 Ra, with Ra from FAO-56 eq. 21 (solar declination, inverse Earth–Sun distance, sunset hour angle). **Effective rain** (USDA-SCS): P(125 − 0.2P)/125 for P ≤ 250 mm. **Pumping energy:** hydraulic kWh = V × H / 367 with H = depth to water + 3 m; electric and solar divide by 0.45 wire-to-water; diesel by 0.6 then × 0.3 L/kWh.

**Outliers.** For each well, residual = reading − median of up to six neighbouring readings; z = (residual − median residual) / (1.4826 × MAD); |z| > 3.5 is flagged and, by default, left out of calibration.

**Calibration** grid search: for Sy × {0.6 … 1.4} and T × {0.5 … 2} (7 × 7), run the five-year history (tolerance 10⁻⁴ m), compare simulated and observed depth for every non-outlier reading, keep the pair with the lowest RMSE. Each run yields to the browser so the progress bar and RMSE surface update live.

**Verification.** Zero stress on a flat, patchy aquifer for 24 months: max head change 10⁻¹³ m. One well in a uniform 31 × 31 aquifer: drawdown symmetric to 10⁻⁸ m and monotonic. A closed box with a mound conserves storage. Drawdown against Theis `s = Q W(u) / 4πT`, `u = r² S / 4Tt` at 200–900 m after 180 days: within 1.5 %.

**Contours** are traced by marching squares on a lightly smoothed copy of the field (16-case table, saddle cases split), drawn with non-scaling strokes over a bilinear-smoothed canvas heatmap. **Sankey** bands are cubic Béziers of constant thickness from stacked inflow nodes to one aquifer node to stacked outflow nodes.

**Quotas.** Pool = sustainable yield × (1 − reserve). Share_f = pool × (w × acres_f / Σacres + (1 − w) × need_f / Σneed); farmers under the floor (floor × the equal per-acre share) are lifted and the difference is taken pro rata from those above it. Credits = quota − recent use; a trade may not exceed the seller's credits.

## Left for students

| Extension | Difficulty | Start here |
|---|---|---|
| Steady-state spin-up for initial heads (solve with Sy/Δt = 0 under average stresses) | Medium | `initialDepth()` |
| Capture zones by backward particle tracking (Pollock's method) | Hard | `traceCaptureZone()` |
| Gradient calibration (Levenberg–Marquardt on zone-wise Sy and T, with parameter correlation) | Hard | `calibrateGaussNewton()` |
| Uncertainty bands: Monte Carlo runs in a Web Worker, 10–90 % bands on the small multiples | Hard | `ensembleBands()` |
| Observed monthly rainfall from the Open-Meteo archive or PMD instead of typed rain factors | Medium | `fetchObservedRain()` |
| Saline front: track the fresh–brackish interface and warn before it reaches a pump | Hard | `salinityFront()` |
| Readings by SMS or WhatsApp through a gateway, de-duplicated and validated | Medium | `syncFromSmsGateway()` |
| Real ground elevation from an SRTM or Copernicus DEM tile resampled to the grid | Hard | `importElevation()` |

Each is marked `// TODO(student):` at the exact stub, listed in the About panel, and reachable from a button in the app that explains it is not built yet.

## Data and safety notes

- **Both villages, their farmers, wells, readings, trades and meetings are fictional.** Placing a village on a real map does not describe that place.
- Climate normals, aquifer properties, crop coefficients, yields, prices, energy tariffs, canal allowances and supply shares are **illustrative teaching values**: verify with PMD, the Punjab Irrigation and Agriculture departments, PARC, FAO-56 and your DISCO bill before using them for any decision. The UI labels them so.
- The model is one layer with 100 m cells and monthly steps: it shows trends and compares options; it cannot design a well, predict a specific well's level precisely, or represent vertical flow and salinity (see the student TODOs).
- Pumping follows simple rules (demand, hours, appetite, quotas); real farmers respond to prices, credit, power cuts and neighbours.
- Quotas and credits here are a discussion aid: they create no legal rights or permits.
- Nothing leaves the browser unless you load the street map (unpkg.com and openstreetmap.org) or allow Geolocation.

## Evaluation ideas for the FYP report

1. **Verification:** report the five built-in checks (zero stress, closed box, symmetric cone, Theis error by distance, monthly closure) and how they change with ω, tolerance and time step; plot Theis error against Δt.
2. **Solver study:** iterations and run time against ω (the built-in comparison table) and grid size; compare with a direct banded solver.
3. **Calibration identifiability:** draw the RMSE surface for both villages, test recovery of the hidden truth with more noise or fewer wells, and discuss Sy–T trade-offs.
4. **Outlier detection:** inject known errors into the CSV and measure precision and recall of the MAD flag at different limits.
5. **Scenario sensitivity:** vary each lever (rice share, solar share, quota, ponds) and plot depth in 2036, dry wells and net income; discuss the irrigation-efficiency paradox the model shows (laser and drip save energy but little water).
6. **Usability with a water committee:** time-to-answer tasks in Urdu and English ("which wells dry first?", "what does a quota cost us?"), SUS questionnaire after a meeting-mode session, and accessibility checks (keyboard only, screen reader, reduced motion).
