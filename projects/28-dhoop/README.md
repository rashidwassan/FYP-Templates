# 28 · Dhoop — Solar Battery Optimizer (idea #91)

**A day-ahead planner for Pakistani homes and small shops that already have rooftop solar, a hybrid inverter and a battery, in Urdu and English.** Dhoop forecasts tomorrow's solar from the sun's position, a clear-sky model and the clouds, then uses dynamic programming to decide hour by hour when the battery should charge, discharge, import or export, and when to run the water motor, washing machine, iron, e-bike charger and AC — minimising the bill plus battery wear while keeping a reserve for load-shedding. The home screen is a live, animated energy-flow diagram you can scrub through the day. Around it: a tariff and regime simulator (legacy net metering, net billing 2026, the proposed evening export price, no export, off-grid), a load-shedding planner, inverter-log analytics with a CSV column mapper, rainflow battery-health estimates, what-if sizing over 60 logged days and an experimental Web Bluetooth battery link. دھوپ اردو اور انگریزی دونوں میں ہے، اردو میں مکمل دائیں سے بائیں ترتیب کے ساتھ۔

**Scale:** 7,211 lines in one `index.html` (≈ 6,130 lines of JavaScript, ≈ 910 of CSS, a 107-icon sprite) · 15 routed modules plus the search palette, spotlight tour, shortcuts sheet, appliance editor, CSV mapper and three printable A4 reports · 891 bilingual strings · 5 bilingual guides (20 sections) and a 12-question quiz · seed data: 10 city presets with 70 days of offline weather (1,680 hourly values), 12 DISCOs, 3 battery chemistries, an 18-appliance library, a 14-appliance Lahore household, 10 load-shedding windows over the week, 60 days of hourly inverter logs (1,440 rows), 8 tariff slabs, 5 notifications and 3 activity-log events · 8 student TODOs.

## Getting started

- Open `index.html` in Chrome, Edge, Firefox or Safari. It works from `file://` and fully offline; only Google Fonts (Outfit, DM Mono, Noto Nastaliq Urdu) come from the network and fall back to system fonts.
- The first open lands on the **Control room** with a spotlight tour. Press play under the energy-flow diagram (or `Space`) to watch the day's plan, or drag the time bar. The language button (or `L`) switches every screen to Urdu, right to left; the moon button (or `T`) switches to the night-sky theme.
- **Live weather (optional):** Solar forecast → *Fetch live forecast* asks [Open-Meteo](https://open-meteo.com/) for 7 days of hourly cloud, temperature and shortwave radiation. It is never called on load; without a connection the seeded week is used and the failure is logged.
- **Web Bluetooth (experimental):** Battery link reads the standard GATT Battery Service (0x180F / 0x2A19). It needs Chrome or Edge on desktop or Android and a secure page — serve the folder (`npx serve .` or `python3 -m http.server`) and open `http://localhost:…`. Firefox and iPhone browsers do not support it; the simulated 16-cell BMS works everywhere.
- **Web Serial (student extension):** "Send to inverter" detects Web Serial (desktop Chrome/Edge, HTTPS) and lists the plan's battery time slots; writing them over Modbus is left as a TODO.
- **Where data lives:** `localStorage` under the `dhoop.` prefix — `settings`, `system`, `loads`, `tariff`, `outages`, `logs`, `weather` (live forecasts only), `inbox`, `activity`, `quiz`, `bms`, `meta`, `lang` and `noticed` (once-a-day notice guard).
- **Backup and reset:** Settings → Your data → Export JSON / Import JSON, *Demo data* off for an empty household to enter your own, and *Reset to demo*.

## The problem

Pakistan's rooftop-solar boom changed shape in 2026. NEPRA's Prosumer Regulations moved new users from net metering to **net billing**: every imported unit is paid at the full tariff, while surplus is bought at about **Rs 11 per unit instead of about Rs 26**, on five-year contracts ([Nukta](https://nukta.com/pakistan-solar-ends-net-metering)); existing users kept net metering after a February 2026 rollback ([Arab News](https://www.arabnews.com/node/2633632)). When a stored unit is worth four to five times an exported one, households buy batteries — roughly **6 GW of batteries were imported between January 2024 and August 2026** ([ProPakistani](https://propakistani.pk/2026/08/06/solar-owners-are-replacing-grid-power-with-batteries-catches-govt-off-guard/)). A time-of-use export price of **Rs 18–22 per unit for 5–10 pm has only been proposed** ([Business Recorder](https://www.brecorder.com/news/amp/40432839)), and IMF programme conditions limit new time-of-use tariffs ([Business Recorder](https://www.brecorder.com/news/40433394)).

Most hybrid inverters still run a fixed rule ("solar first, battery at night") that empties the battery before an 8 pm load-shedding slot and ignores forecasts, peak hours and battery wear. Owners have no simple way to see what the battery should do tomorrow, which appliances to move into the sunny hours, which regime they are better off in, or how fast the battery is ageing in a Multan summer. Dhoop answers those questions with transparent models a student can defend in a viva.

## What is built

| Area | Features |
|---|---|
| Control room | Animated energy-flow diagram (sun, home, battery, grid, optional generator around the hybrid inverter; line thickness = power, dash direction = flow, a sun arc from sunrise to sunset), landscape and portrait layouts drawn at the real width; 24-hour scrubber with play/pause, "now", a solar shading strip and outage marks; "at this hour" card with flows, the tariff in force and the current decision; today's load-shedding with the planned SOC at its start; KPI tiles (solar, self-sufficiency, saving vs naive and vs inverter default, net cost); the day-plan timeline with a moving cursor and the top decisions |
| System profile | City presets with coordinates and DISCO for Lahore, Karachi, Islamabad, Peshawar, Quetta, Multan, Faisalabad, Hyderabad, Sukkur, Gilgit; PV array (kWp, tilt, azimuth, temperature coefficient, NOCT, losses, inverter kW); battery (usable kWh, LFP / lead-acid / tubular with presets, min SOC, charge and discharge kW, round-trip efficiency, rated cycles, price, install date, SOC at midnight); grid (12 DISCOs, sanctioned load, five regimes, grid charging, export limit); generator (kW, L/h, fuel price); validation; engineering checks (DC/AC ratio, C-rate, chemistry minimum SOC, azimuth, tilt, sanctioned load, off-grid without generator) and derived numbers (wear cost formula, night backup hours, clear-sky yield, generator Rs/kWh, system cost) |
| Solar forecast | Spencer/NOAA sun position, sunrise/noon/sunset with refraction, Haurwitz clear sky with a per-city dust/smog factor, Kasten–Czeplak cloud attenuation, Erbs beam/diffuse split, isotropic plane-of-array transposition, NOCT cell temperature and temperature derating, inverter clipping, 15-minute sub-sampling; 7-day outlook vs clear sky; sun-path chart with both solstices, hour marks and the panel azimuth; irradiance components and PV output charts with hover tooltips; hourly table; Open-Meteo button with a last-synced stamp and offline fallback |
| Loads | 18-appliance library (watts, start surge, class); household list by class (fixed with hour toggles and duty cycle, shiftable with window, duration and usual start, comfort with window, hours wanted and usual hours, essential flag); stacked hourly profile; a drag- and arrow-key base-load editor; KPIs (daily kWh, flexible share, peak, essential load) |
| Day-ahead optimizer | DP over 1 % SOC levels × hourly or 15-minute steps for 24 or 48 h; import cost by hour, export value by regime, battery wear, unserved energy or generator fuel, reserve-SOC penalty at outage starts, charge/discharge/inverter/sanctioned-load/export limits, round-trip efficiency, terminal value of stored energy; shiftable and comfort loads placed by coordinate descent with overload pruning, re-running the DP; plain-language reasons ("Run the Water motor (1 hp) at 07:00 — 1.3 kW solar surplus", "Hold the battery at 100% from 13:00 so the 20:00 load-shedding is covered"), per-load savings; strategy table vs battery-only, inverter default (SBU) and naive backup; constraint audit chip; step table, CSV export, printable report; Web Serial detection for sending slots (TODO) |
| Tariff and regimes | All-in hourly import price ((rate + FPA) × (1 + GST + duty)), ToU or telescopic slabs (protected and unprotected), five regimes priced with the battery optimised under each over the 7 forecast days, monthly bill breakdown, unserved energy valued for off-grid, payback and NPV over the 5-year contract with escalation, degradation and discounting, cumulative discounted cash-flow chart, hourly buy/sell price chart, editable rates, printable comparison |
| Load-shedding | Weekly feeder grid (tap, drag or keyboard to paint hours) with presets; next-7-days table with energy and SOC needed for everything or essentials; suggested reserve SOC; reserve penalty; "what can I run" runtime calculator with running load, start surge vs inverter limits and whether it covers the next outage |
| Logs and analytics | 60 seeded days (monsoon clouds, soiling between rains, noisy household load, the inverter's default dispatch); CSV import with delimiter detection, a column mapper, W/kW, sign conventions (auto-detected battery sign), replace or merge, preview; KPIs over 7/30/60 days (self-consumption, self-sufficiency, export, savings); daily chart; hour × day heatmap for five metrics; plan-vs-actual replay of any logged day with perfect foresight; CSV export and a sample inverter CSV |
| Battery health | ASTM E1049 rainflow counting on the logged SOC, equivalent full cycles, throughput cycles, depth-of-discharge histogram, Wöhler-type cycle life by chemistry, calendar ageing with a +10 °C doubling rule using monthly city temperatures, SOH estimate and a 15-year projection with end of life at 80 %, rule-based recommendations, deepest-cycles table, printable report — labelled approximate |
| What-if sizing | Re-optimises every logged day for: today's system, +5 kWh battery, +2 kWp panels (clipped by the inverter), the other regime, and a custom mix (battery, panels, regime, inverter upgrade); 60-day cost, annual saving, extra cost, payback, imports, exports, curtailment, battery throughput, unserved energy; best-investment insight |
| Battery link | Feature detection (API, secure context, adapter); real GATT Battery Service read with notifications; vendor frame inspector that recognises JK RS-485, JK BLE and Daly headers (decoding is a TODO); a simulated 16-cell LFP BMS following today's plan (OCV curve, pack voltage, current, power, temperature, cell spread, cycles, SOC trend) that can feed the Control room |
| Learn | Five bilingual guides (net metering vs net billing, inverter priority modes SBU/SUB/SOL, battery safety, panel cleaning and soiling, reading your bill), printable; 12-question quiz with shuffled options, explanations, result ring, review and best score |
| Platform | Settings (language, day/night/device theme, three text sizes, reduce motion, planning parameters, demo data, storage used, JSON export/import/reset); notifications inbox and activity log grouped by day (CSV, print); search palette over pages, actions, appliances, guide sections and regimes; 8-step spotlight tour; keyboard shortcuts; skip link, labels, focus styles, `aria-live` announcements, keyboard-closable dialogs; print CSS that forces light colours |

## Module map

| Module | Key functions | Storage keys |
|---|---|---|
| State and seed | `seedApp`, `emptyApp`, `loadState`, `persist`, `notify`, `logEvent`, `demoLoads`, `demoOutages` | all `dhoop.*`, `dhoop.meta` |
| Solar model | `sunPosition`, `sunTimes`, `haurwitzGhi`, `cloudFactor`, `erbs`, `poaIrradiance`, `pvOutput`, `forecastSeries`, `weekOutlook`, `seedWeather`, `weatherFor`, `fetchLiveWeather`, `parseOpenMeteo` | `dhoop.weather` |
| Optimizer | `planContext`, `outageMask`, `buildProblem`, `stepFlows`, `moneyCost`, `reserveCost`, `moveCostTable`, `solveDP`, `evaluatePath`, `policyPath`, `sbuWant`, `backupWant`, `fixedProfile`, `flexVars`, `candidates`, `powerOk`, `optimisePlan`, `finishPlan`, `checkPlan`, `planReasons`, `actionBlocks` | — (cached in memory by `planKey`) |
| Plan service | `getPlan`, `scenarioPlan` (TODO), `planNotices`, `nowStep` | `dhoop.noticed` |
| Tariff | `importRate`, `exportRate`, `slabCharge`, `slabMarginal`, `monthlyBill`, `netUnits`, `monthBands`, `regimeMonth`, `investment`, `cashflows`, `npvOver`, `simplePayback`, `importNepraSchedule` (TODO) | `dhoop.tariff` |
| Analytics | `synthLogs`, `parseCsv`, `guessMapping`, `guessBattSign`, `parseStamp`, `csvToDays`, `sampleCsv`, `logKpis`, `replayDay`, `estimateSoiling` (TODO) | `dhoop.logs` |
| Battery health | `reversals`, `rainflow`, `cyclesToFailure`, `calendarLoss`, `batteryHealth`, `healthAdvice`, `fadeSpec` | — |
| Charts | `mountChart`, `xyChart`, `seriesSvg`, `gridLines`, `ganttSvg`, `heatmapSvg`, `sparkSvg`, `tipHtml`, `legendHtml`, `tableHtml` | — |
| Control room | `renderHome`, `flowSvg`, `updateFlow`, `setFlow`, `renderScrubber`, `setStep`, `togglePlay`, `updateNowCard`, `renderHomeKpis`, `renderOutageCard` | — |
| System profile | `renderSystem`, `readSystemForm`, `saveSystem`, `applyCityPreset`, `applyChemDefaults`, `systemChecks` | `dhoop.system` |
| Forecast view | `renderForecast`, `drawForecastCharts`, `sunPathSvg`, `fetchForecast`, `useSeedWeather` | `dhoop.weather` |
| Loads | `renderLoads`, `loadParts`, `renderBaseEditor`, `openLibrary`, `openItemEditor`, `saveItem`, `deleteItem`, `forecastLoadFromLogs` (TODO) | `dhoop.loads` |
| Day plan | `renderPlan`, `fillPlan`, `strategyTable`, `scheduleTable`, `exportPlanCsv`, `sendToInverter`, `pushScheduleToInverter` (TODO) | `dhoop.settings`, `dhoop.system` |
| Tariff view | `renderTariff`, `regimeMonths`, `regimeCard`, `paybackTable`, `renderBreakdown`, `tariffForm`, `saveTariff` | `dhoop.tariff` |
| Load-shedding | `renderOutage`, `renderWeekGrid`, `saveWeekGrid`, `applyPreset`, `upcomingOutages`, `outageNeed`, `socNeeded`, `renderRuntimeCalc` | `dhoop.outages`, `dhoop.settings` |
| Logs view | `renderLogs`, `drawLogCharts`, `renderReplay`, `openMapper`, `mapperPreview`, `importMapped`, `exportLogsCsv` | `dhoop.logs`, `dhoop.inbox` |
| Health view | `renderHealth`, `drawHealthCharts`, `cycleTable` | — |
| What-if | `wiScenarios`, `biggerBattery`, `simulateScenario`, `runWhatIf`, `bestInvestment`, `renderWhatIf` | — |
| Battery link | `renderBms`, `connectBle`, `setBleLevel`, `identifyBmsFrame`, `parseBmsFrame` (TODO), `startSim`, `simTick`, `ocv`, `renderSimCard` | `dhoop.bms`, `dhoop.settings.feedBms` |
| Learn | `renderLearn`, `renderGuide`, `startQuiz`, `renderQuiz`, `finishQuiz`, `renderQuizResult` | `dhoop.quiz` |
| Inbox, settings | `renderInbox`, `renderActivity`, `updateBadges`, `renderSettings`, `setPref`, `setDemo`, `exportData`, `importData`, `resetAll`, `scheduleReplan` (TODO) | `dhoop.inbox`, `dhoop.activity`, `dhoop.settings`, `dhoop.lang` |
| Search, tour, keys, print | `paletteIndex`, `openPalette`, `startTour`, `showTourStep`, `openShortcuts`, `onKey`, `printPlanReport`, `printBillReport`, `printHealthReport`, `printCurrent` | `dhoop.settings.tourDone` |
| Router and shell | `ROUTES`, `renderNav`, `renderRailFoot`, `route`, `renderRoute`, `applyPrefs`, `setLang`, `toggleTheme`, `boot` | URL hash |

## Architecture (single file)

```
index.html
├─ <head>        title "… | FYP Template", fonts (Outfit, DM Mono, Noto Nastaliq Urdu), inline SVG favicon
├─ <style>       tokens ("sunrise control room": warm off-white, saffron sun, deep teal battery, slate grid,
│                coral import; night theme = navy sky with stars), motion kit, left rail / top bar / phone tabs,
│                buttons, fields, switches, tiles, tables, sheet, palette, tour, toasts, chart tooltip,
│                energy-flow diagram, scrubber, chart classes, view styles, responsive (1100 / 760 / 420),
│                right-to-left rules, print (A4, light colours forced)
├─ <svg>         107-icon sprite generated from the icon names the code uses
├─ shell         rail (brand, grouped nav, sun clock), top bar (title, city · date · regime, search, inbox,
│                theme, language), view, phone tabs, modal, palette, tour, toasts, tooltip, live region, print area
└─ <script>
   ├─ utils          store (dhoop. prefix), esc, PKT dates, number formats, toast / announce, animateIn,
   │                 settle, countUp, drawPath, tile, modal / confirm, print, download, files, form, button,
   │                 range, card, note, list helpers
   ├─ i18n           STR [English, Urdu] in three groups → TXT.en / TXT.ur, t(), L()
   ├─ content        GUIDES (5 × 4 sections), QUIZ (12)
   ├─ catalog        CITIES (coordinates, DISCO, haze, 7-day weather seed, monthly temperatures), DISCOS,
   │                 CHEMS, REGIMES, APPLIANCES, LOAD_CLASSES
   ├─ solar          sun position, sun times, Haurwitz, Kasten–Czeplak, Erbs, POA, PV, weather, Open-Meteo
   ├─ optimizer      context, problem, flows and costs, DP, evaluator, baselines, loads, coordinate descent,
   │                 audit, reasons
   ├─ tariff         rates, slabs, bills, regimes month, payback, NPV
   ├─ analytics      synthetic logs, CSV parser and mapper, KPIs, replay, rainflow, ageing, advice
   ├─ state          defaults, seed, empty, load, persist, inbox, activity
   ├─ charts         measured-width SVG charts (ResizeObserver), tooltip, Gantt, heatmap, sparkline, legend, table
   ├─ plan service   cached plan, scenario stub, notices
   ├─ views          control room · day plan · forecast · system · loads · tariff · load-shedding · logs ·
   │                 health · what-if · battery link · learn · inbox · settings · about
   ├─ platform       search palette, tour, shortcuts, printable reports
   └─ router + boot  ROUTES, nav, prefs, language, theme, boot()
```

## Domain logic and algorithm notes

**Sun position** (local standard time, PKT = UTC+5). With fractional year γ = 2π/365 · (n − 1 + (h − 12)/24):

```
δ   = 0.006918 − 0.399912 cos γ + 0.070257 sin γ − 0.006758 cos 2γ + 0.000907 sin 2γ − 0.002697 cos 3γ + 0.00148 sin 3γ
EoT = 229.18 (0.000075 + 0.001868 cos γ − 0.032077 sin γ − 0.014615 cos 2γ − 0.040849 sin 2γ)   [minutes]
TST = 60 h + EoT + 4 λ − 60 · 5      ω = TST / 4 − 180°
cos Z = sin φ sin δ + cos φ cos δ cos ω          azimuth = atan2(sin ω, cos ω sin φ − tan δ cos φ) + 180°
sunrise/sunset: cos ωs = cos 90.833° / (cos φ cos δ) − tan φ tan δ,  noon = (720 − 4λ − EoT + 300) / 60
```

**Irradiance and PV.** Clear-sky GHI (Haurwitz) = 1098 cos Z · e^(−0.059 / cos Z) × haze (0.86 for Lahore in smog season … 0.97 for Quetta); clouds (Kasten–Czeplak) × (1 − 0.75 C^3.4), or Open-Meteo's measured shortwave radiation when fetched. Erbs: kt = GHI / (1367 (1 + 0.033 cos(2πn/365)) cos Z); kd = 1 − 0.09 kt (kt ≤ 0.22), 0.9511 − 0.1604 kt + 4.388 kt² − 16.638 kt³ + 12.336 kt⁴ (≤ 0.80), 0.165 otherwise; DHI = kd · GHI, DNI = (GHI − DHI) / max(cos Z, 0.065). Plane of array: POA = DNI · max(0, cos θ) + DHI (1 + cos β)/2 + 0.2 · GHI (1 − cos β)/2 with cos θ = cos Z cos β + sin Z sin β cos(γs − γp). Cell temperature Tc = Ta + POA/800 · (NOCT − 20); PV = kWp · POA/1000 · (1 + γT (Tc − 25)) · (1 − losses), clipped at the inverter rating; each step averages 15-minute samples.

**The day-ahead problem.** State i = battery SOC on a 101-level grid (1 % of usable capacity Eu, so one level = Eu/100 kWh); decision j = next level; ΔE = (j − i) · Eu/100.

```
charge   c = ΔE / ηc ≤ Pchg·Δt     (and ≤ PV − load when grid charging is off or the grid is down)
discharge d = −ΔE · ηd ≤ Pdis·Δt   and ≤ Pinv·Δt − PV                       (ηc = ηd = √RTE)
net = load + c − d − PV
net > 0: import = min(net, sanctioned·Δt) if grid up, unserved = rest
net < 0: export = min(−net, export limit·Δt) if the regime allows it and the grid is up, curtailed = rest
cost_k(i, j) = import·p_imp(k) − export·p_exp(k) + |ΔE|·w + unserved·VOLL (or generator Rs/kWh)
             + [an outage starts at k] · max(0, R − i)·Eu/100 · λ              (reserve R, penalty λ)
w = battery price / (rated cycles × Eu × 2)       (Rs 4.00 per kWh of throughput for the demo LFP pack)
V_K(i) = −(i − i0)·Eu/100 · max(0, ηd · min p_imp − w)                         (value of stored energy)
V_k(i) = min_j cost_k(i, j) + V_{k+1}(j)
```

Because flows depend only on j − i, each step's moves are priced once (`moveCostTable`), so a 24-step DP costs O(K · N · moves) ≈ 24 × 81 × 91 additions — about a millisecond. Ties within 1e-6 rupees are broken lexicographically by the larger sum of future SOC, so the battery fills before exporting when both cost the same. Export value is the hour's import price under legacy net metering (units cancel one for one), Rs 11 under net billing, Rs 20 in 17:00–22:00 under the evening-export proposal, and zero (export forbidden) under no-export and off-grid.

**Joint load placement** (coordinate descent around the DP):

```
place = usual times;  best = DP(load(place))
repeat up to 3 passes:
  for each flexible load v:
    for each candidate c (shiftable: every start in [earliest, latest − duration];
                          comfort: swap one chosen hour for one free hour in the window):
      if load(place with v = c) exceeds sanctioned + inverter kW (inverter only in an outage): prune
      elif DP(load(place with v = c)).cost < best.cost − 0.01: accept
  stop when a pass improves nothing
```

**Why the plan can never lose to the baselines.** The naive baseline (battery kept full as backup, discharged only during outages) and the inverter default (solar first, deficits from the battery, no grid charging) are walked step by step on the *same* SOC grid with the *same* `stepFlows`/`flowCost`, choosing the nearest feasible level to what the rule wants. Each is therefore a feasible path of the DP's graph, and the DP's minimum over that graph (verified against brute-force enumeration) cannot exceed either. Coordinate descent starts from the usual placement and only accepts improvements, so plan ≤ battery-only ≤ both baselines. `checkPlan` audits every step: SOC within [min, 100], charge/discharge/inverter/sanctioned/export limits, no grid during outages, no grid charging when disabled, and energy balance PV + discharge + import + unserved = load + charge + export + curtailed to 1e-6 kWh.

**Reasons.** Consecutive steps with the same battery action form blocks (charge from solar, charge from grid, discharge, backup during an outage, hold before an outage); exports, curtailment and unserved energy form windows. Each block or window and each flexible load becomes one sentence; a moved load also reports what it saves (the DP re-run with that load back at its usual time).

**Tariff.** All-in marginal import price = (energy rate + FPA) × (1 + GST + electricity duty), with ToU peak/off-peak or the marginal slab rate at the household's typical units. Monthly bill: units in peak and off-peak bands; net metering cancels exports against imports in the same band, then the other band, and buys any excess at the legacy rate; slabs are telescopic; fixed charges, FPA, duty, GST and TV fee follow. Regime comparison runs the battery DP for each forecast day under each regime (SOC carried over) and scales to 30 days. NPV = −capex + Σ_y S·(1 + e)^(y−1)·(1 − d)^(y−1) / (1 + r)^y over the contract years.

**Logs and CSV.** The delimiter is the one that splits the header most; quoted fields and doubled quotes are handled; headers are guessed by pattern; the battery sign is inferred from whether the battery column is positive while solar exceeds load; rows at any interval are averaged per hour. The plan-vs-actual replay prices the logged flows with the same tariff and wear as the DP's perfect-foresight optimum for that day.

**Battery health.** Rainflow (ASTM E1049-85, three-point method) on the hourly SOC reversals gives full and half cycles with depth d and mean. Damage = Σ n / N(d) with N(d) = N80 · (0.8/d)^k (k = 1.1 LFP, 1.4 tubular, 1.5 lead-acid); damage 1 ≙ 20 % capacity lost. Calendar loss = Σ_months cal25/12 · 2^((T_city + 3 − 25)/10). SOH = 100 − 20 · damage per day · days in service − calendar loss; the projection extends the same rates for 15 years. All of this is an educational approximation.

**What-if.** For each scenario and each of the last 60 logged days: logged load, logged PV scaled by the new kWp / old kWp and clipped by the (possibly upgraded) inverter, outage hours from the log, the battery DP for the day, SOC carried to the next day; money = imports − exports + wear + unserved. Annual saving = (base − scenario) × 365 / days; payback = extra cost / annual saving.

## Left for students

| Extension | Difficulty | Start here |
|---|---|---|
| Plan under forecast uncertainty: cloud scenarios, a two-stage stochastic or robust DP, and the value of the reserve | Hard | `scenarioPlan()` |
| Write the plan's battery time slots to the inverter over Web Serial + RS-485 Modbus RTU and read them back | Hard | `pushScheduleToInverter()` |
| Decode JK and Daly BMS frames (cell voltages, current, SOC, temperature) and stream them from the vendor's BLE service | Hard | `parseBmsFrame()` |
| Perez anisotropic sky model and a comparison with the isotropic model on logged data | Medium | `poaIrradiance()` |
| Learn the household's load profile from the logs (weekday × hour regression or k-nearest days by temperature) | Medium | `forecastLoadFromLogs()` |
| Estimate panel soiling from clear-day ratios to the clear-sky model and advise when a wash pays | Medium | `estimateSoiling()` |
| Re-plan hourly in the background with a service worker, periodic sync or push, and notify when advice changes | Medium | `scheduleReplan()` |
| Import an official NEPRA / DISCO tariff schedule with dated slab, ToU and FPA history | Easy | `importNepraSchedule()` |

Search the source for each function name; the About page lists the same eight with a one-line hint and a difficulty word.

## Data and safety notes

- **Everything is illustrative.** Tariffs, slab rates, FPA, taxes, buyback prices (Rs 26 legacy, Rs 11 net billing, Rs 20 evening proposal), appliance watts and surges, system and battery prices, cycle-life and ageing parameters, city weather seeds, haze factors, the feeder schedule and the demo household (the "Rana family, Johar Town") are made up for teaching. Every rate is labelled "illustrative — verify with NEPRA / your DISCO" in the app; check your own bill, contract and installer before deciding anything. The evening export price is a proposal, not policy.
- **Dhoop advises; it does not control anything.** It never writes to an inverter or BMS. Changing inverter or battery settings without the manufacturer's manual can damage equipment or void warranties.
- **Battery safety first.** The Learn guide covers ventilation, heat, fire, the BMS and acid. The ageing model is approximate and is not a warranty assessment.
- **Network and privacy.** No account, server or analytics. The only network calls are Google Fonts and, when you press the button, Open-Meteo (which receives your coordinates rounded to four decimals). Web Bluetooth asks for permission each time and reads only the Battery Level characteristic.
- **Browser limits stated in the UI:** Web Bluetooth (Chrome/Edge desktop or Android, HTTPS or localhost), Web Serial (desktop Chrome/Edge, HTTPS), the live forecast (internet), and that plans are only recomputed while the page is open.

## Verification

- **Harness** (`check.mjs`, 1366×850 and 390×844): 25 states — control room, scrubbed day plan, day plan, decisions, forecast, system profile, loads, appliance editor, tariff, load-shedding, logs, CSV mapper, battery health, what-if results, battery link with the simulator running, a guide, a quiz answer, control room in Urdu, the plan in the night theme, the onboarding tour, settings, inbox, about, tariff in Urdu and night, and the search palette — with no console, page or request errors and no horizontal overflow. Print layouts were checked with print media emulation in both themes.
- **Behaviour script** (Playwright, 141 checks, all passing): seed, no network on load, every `t()` key present and every string translated; scrubber and play; sun position (solstice declinations, noon azimuth, symmetric sunrise/sunset, Lahore day lengths), Haurwitz, Erbs closure, flat-panel POA = GHI, cloud attenuation, inverter clipping; live forecast through a local Open-Meteo fixture and the offline fallback; the plan's constraint audit, plan ≤ battery-only ≤ naive and inverter default, evaluated cost = DP value, loads inside their windows, reasons and reserve at the outage start; DP = brute force on 40 random problems; 24 random systems across all five regimes, 15-minute and 48-hour horizons, generators and random outages never violating a limit and never losing to either baseline; tariff slabs, net billing credit, net-metering netting, NPV and payback formulas and regime ordering; CSV parsing, mapping, sign detection, round trip and import through the mapper UI; replay optimum ≤ actual; the ASTM rainflow example; health, ageing and what-if monotonicity; outage grid, presets and runtime formula; appliance add/delete, base-load keyboard editing, system validation and presets; BMS header recognition and simulator; the 12/12 quiz; tour, Urdu, theme, shortcuts, search, inbox, JSON export/import, demo toggle and reset; and no overflow on 17 routes in both languages at both widths.

## Evaluation ideas for the FYP report

- **Savings:** run the plan-vs-actual replay over 60 real logged days and report the gap between what the inverter did and the optimum, split by sunny, cloudy and load-shedding days.
- **Baselines:** compare Dhoop, inverter default (SBU) and naive backup over a month under each regime; show why batteries pay under net billing but much less under legacy net metering.
- **Forecast accuracy:** compare the clear-sky + cloud model against measured PV (MAE, RMSE, bias by hour); test the effect of the haze factor and of live radiation data.
- **Robustness:** perturb the forecast by ±20 % and measure how much the deterministic plan loses against a re-plan with the true values (motivates the uncertainty TODO).
- **Algorithm:** confirm optimality against brute force on small cases, then plot runtime against SOC resolution (1 %, 2 %, 5 %) and step length (60 vs 15 minutes) and the cost lost by coarser grids.
- **Battery ageing:** compare rainflow equivalent cycles with throughput cycles and discuss how deep daily cycling and heat change the projected life for LFP vs tubular batteries.
- **Usability:** task-based tests in Urdu and English (find when to run the motor, set the reserve for tonight's outage, import a CSV) with a SUS questionnaire.
