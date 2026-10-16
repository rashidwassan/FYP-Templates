# 11 · Sailab — Flood Preparedness & Relief Map (idea #39)

**Know the river before it knows you.** A map-first, bilingual (English / اردو) flood app for Pakistan: district alert levels computed from upstream river gauges and rainfall, an FFD-style gauge dashboard, a family evacuation planner with routes, relief-camp finder with family registration, camp supplies and volunteers, historical flood comparison, a reunification board for missing and found people, a notifications inbox, printable reports and a safety guide with an SOS generator.

**Scale:** ~4,020 lines in one HTML file · 12 modules (plus search, tour and reports) · 159 seed records (13 gauge stations, 32 districts, 40 relief camps, 8 inventory items, 12 volunteers, 8 reunification posts, 8 helplines, 8 historical floods, 15 guide cards, 15 go-bag items).

## Getting started

1. Open `index.html` in any modern browser. No build step, no server, no dependencies beyond Google Fonts.
2. The map loads Leaflet 1.9.4 and OpenStreetMap tiles lazily from the network. When they cannot load (offline, blocked CDN, or `index.html?nomap=1`) the app falls back to a hand-drawn SVG schematic of the Indus basin with the same markers, routes and interactions.
3. Serve over HTTP (`python -m http.server`) if you want the **GPS** button in the SOS generator and clipboard copy to work in every browser; everything else works from `file://`.
4. Data lives in `localStorage` under the `sailab:` prefix (see the module map). **Settings → Export my data** writes one JSON file; **Import** restores it; **Reset demo data** clears everything and restores the seed scenario.
5. First open starts a five-step tour; replay it from Settings. Keyboard: `/` search, `1–9` pages, `Esc` close, `?` settings.

## The problem

The 2022 floods affected 33 million people. Warnings issued from Islamabad did not reach villages in time or in their language, families did not know where the relief camps were or what to carry, camp managers had no shared picture of stock and occupancy, and relatives separated during evacuation had no channel to find each other.

## What is built

| Module | Features |
|---|---|
| Alerts (default) | 32 flood-prone districts with lat/lng, computed risk score, four alert levels, bilingual advisory, local flood mechanism note, 7-day rain mini-bars; map markers coloured by level and sized by risk, pulsing for Severe; filter by province and level; "my district" selector drives the whole app; print situation report |
| Gauges | 13 FFD stations (Indus, Jhelum, Chenab, Panjnad) with realistic per-station flood-limit thresholds, seeded 14-day inflow/outflow, animated gauge bars with L/M/H/VH/EH ticks, sparklines, SVG line chart with threshold bands, draw-in animation and hover tooltip, trend vs yesterday, cusecs ↔ m³/s units |
| Plan | Household profile (age groups, pregnancy, disability, livestock, vehicle, documents, meeting point, contacts) → prioritised checklist shifted by alert level, go-bag with tick-off and progress ring, "when to leave" rule, evacuation route to a chosen camp with ETA by foot / tractor-trolley / car / boat drawn on the map, three nearest camps by haversine, printable family plan card |
| Camps | 40 schools, colleges, stadiums and high-ground sites with capacity, seeded occupancy, facilities, needs chips, contact, Google Maps directions; local occupancy/needs updates; **register a displaced family** (adds to occupancy); nearest-first list with search and province filter; printable camp list |
| Supplies | Per-camp stock of 8 items vs daily needs of the people sheltering → days-of-supply or coverage with shortage flags; receive / dispatch movements with a log; cross-camp shortage list; KPI tiles; volunteer roster (skills, phone, camp assignment) with add/assign; map coloured by water days; printable shortage report and camp register |
| Missing | Post missing or found people (photo resized to 240 px), search and status filter, match suggestions by name bigram similarity + age proximity + location, WhatsApp share text, mark reunited with a celebration; 8 seed posts including two matching pairs |
| History | Today vs indicative 2010 / 2014 / 2022 peaks per station (animated grouped bars with threshold lines, % of 2010 record), daily rainfall chart for my district, wettest-district ranking, timeline of eight major floods with the lesson each taught |
| Guide | Bilingual before / during / after cards (moving water, electrical hazards, snakes, water purification, diarrhoea, malaria/dengue, skin infections, safe return), editable helplines with call links, SOS text generator with headcount, needs chips, landmark and GPS link → copy / WhatsApp / SMS |
| Inbox | Notifications derived from the scenario (station category crossings, my district level, severe districts, camps near capacity, reunification matches) with read state and badge; activity log of everything the user does (plan saved, stock logged, family registered, post added, reunion, exports, settings) |
| Settings | Language, flow units, text size, reduce motion, alert toasts, default sheet height, my district, export / import / reset, replay tour, keyboard shortcuts |
| About | Problem, risk formula, data disclaimer, module list, student TODO panel |
| Search · Tour · Reports | Global search across districts, stations, camps, posts, guide and pages; five-step spotlight onboarding; print-ready situation report, family plan card, camp list, shortage report, camp register |

## Architecture (single file)

```
index.html
├─ <style>        tokens (slate / river blue / amber / red), motion kit, map stage, floating panel ↔ bottom sheet,
│                 components, per-module styles, modal / search / tour / more-menu, print CSS
├─ <svg>          icon sprite (86 symbols, all used by the app)
├─ <header>       brand, 9 tabs, search, my-district select, inbox bell with badge, settings, language
├─ #stage         #map (Leaflet or SVG schematic) · map status + legend · .panel (right panel / bottom sheet)
├─ <nav>          mobile bottom bar (4 tabs + More)
└─ <script>
   1. utils            $, store (namespaced localStorage), esc, hash/rng, haversine, countUp, drawPath, animateBars
   2. i18n             TXT.en / TXT.ur, t(), name(), bi()
   3. seed data        STATIONS, DISTRICTS, CAMPS, ITEMS, VOLUNTEERS_SEED, GUIDE, HELPLINES_SEED, GOBAG, MISSING_SEED, FLOODS
   4. flood engine     series(), gcat(), gaugeFrac(), rain7(), risk(), trend(), settings + flow units, campData(),
                       inventorySeed() / stockOf() / supplyStatus() / shortages(), logActivity(), buildNotifications()
   5. map adapter      MapCtl: init → Leaflet (lazy) or initSvg; setMarkers, setRoute, focus, fit, refit, pads()
   6. view helpers     setHead, badges, sparkline, rainBars, modal, celebrate, scrollToCard
   7–17. modules       renderAlerts · renderGauges · renderPlan · renderCamps · renderSupplies · renderMissing ·
                       renderHistory · renderGuide · renderInbox · renderSettings · renderAbout
   18. reports         printReport('sitrep' | 'plan' | 'camps' | 'supplies' | 'register')
   19. search / tour   searchIndex(), openSearch(), TOUR_STEPS, startTour(), openMoreMenu()
   20. sheet / router  setSheet + drag, NAV, route(), applyLang(), keyboard shortcuts, boot()
```

Hash routes: `#alerts[/district]` `#gauges[/station]` `#plan` `#camps[/camp]` `#supplies[/camp]` `#missing` `#history[/station]` `#guide[/before|during|after|sos]` `#inbox[/notifs|activity]` `#settings` `#about`. Add `?nomap=1` before the hash to force the schematic map.

## Module map

| Module | Main functions | localStorage keys (`sailab:` prefix) |
|---|---|---|
| Alerts | `risk`, `R`, `filteredDistricts`, `districtCard`, `renderAlerts`, `toggleDistrict`, `setMyDistrict` | `district`, `ui` |
| Gauges | `series`, `gcat`, `gaugeFrac`, `trend`, `flowChart`, `stationRow`, `renderGauges`, `selectStation` | `ui`, `settings` (units) |
| Plan | `getPlan`, `buildChecklist`, `leaveRule`, `routeEstimate`, `planRoute`, `renderPlan`, `savePlanFromForm` | `plan`, `gobag` |
| Camps | `campData`, `campRow`, `openCampUpdate`, `saveCampUpdate`, `openRegistration`, `renderCamps` | `campUpdates`, `registrations` |
| Supplies | `inventorySeed`, `stockOf`, `supplyStatus`, `shortages`, `renderSupplies`, `applyStockMove`, `openVolunteerForm` | `inventory`, `stockLog`, `volunteers` |
| Missing | `getPosts`, `dice`, `matches`, `postCard`, `renderMissing`, `openPostForm`, `markReunited` | `missing` |
| History | `peaksChart`, `rainChart`, `timelineHtml`, `rainRanking`, `renderHistory` | `ui` |
| Guide | `sosText`, `guideCards`, `helplineRows`, `renderGuide`, `requestGps` | `helplines` |
| Inbox | `buildNotifications`, `unreadCount`, `updateInboxBadge`, `logActivity`, `renderInbox` | `inboxRead`, `activity` |
| Settings | `saveSettings`, `applySettings`, `renderSettings`, `exportData`, `importData`, `resetDemo` | `settings`, `lang`, `sheet`, `tourDone` |
| Reports | `printReport`, `sitrepReport`, `planReport`, `campsReport`, `suppliesReport`, `registerReport` | — |
| Search / Tour | `searchIndex`, `runSearch`, `openSearch`, `startTour`, `renderTourStep`, `openMoreMenu` | `tourDone` |
| Map | `MapCtl.init / initLeaflet / initSvg / setMarkers / setRoute / focus / fit / refit` | — |

## Domain logic notes

- **Gauge series.** Each station has a flood-wave shape (`base + peak · exp(-((day − tPeak)/w)²)`) with ±2.5 % seeded noise, rounded to 500 cusecs. Dams release `0.85·base + 0.6·(inflow − base)`; barrages pass 94 % one day later. The series are deterministic per station id and marked "simulated" everywhere.
- **FFD categories.** Discharge is compared with per-station limits (e.g. Guddu 200k / 350k / 500k / 700k / 900k cusecs) → Normal, Low, Medium, High, Very High, Exceptionally High. `gaugeFrac()` places the current flow on that ladder as 0–1 (0.2 per band).
- **District risk.** `risk = 100 × (0.55·gauge + 0.30·rain + 0.15·vulnerability)`; rain-driven hill-torrent districts use `0.70·rain + 0.30·vulnerability`. Rain is the seeded 7-day total scaled by 250 mm. Levels: ≥ 70 Severe, ≥ 50 Warning, ≥ 30 Watch. At first open "my district" is Dadu (Warning, 62) with Sukkur at High flood upstream.
- **When to leave.** Severe → leave now; Warning → move vulnerable members and livestock now, others ready within 6 hours; Watch → prepare; Normal → review. Checklist priorities shift one step with the level.
- **Route estimate.** Haversine distance × 1.3 road factor; speeds 4 / 15 / 40 / 6 km/h for foot / tractor-trolley / car / boat. Drawn as a dashed line on both map modes. Real routing is a student TODO.
- **Supplies.** Daily items: `days = stock / (occupancy × per-person rate)` (water 4 L, food 1 ration, ORS 0.15, chlorine 0.2). Stock items: `coverage = stock × people-per-unit / occupancy` (tents 6, nets 2, blankets 1, hygiene kits 5). Flags: < 1 day / < 40 % red, < 2 days / < 60 % amber. Seeded shortages line up with each camp's "needs" chips.
- **Matching.** `0.5 × Dice(name bigrams) + 0.3 × age proximity (±8 yrs) + 0.2 × location (same district 1, same province 0.4)`; halved when genders conflict; shown when ≥ 0.45.
- **Notifications** are rebuilt from the scenario with stable ids, so read state survives reloads; the activity log is appended by every user action.

## Left for students

| Extension | Difficulty | Start here |
|---|---|---|
| Live FFD / PMD / WAPDA gauge and rainfall feeds with last-synced stamp and offline cache | ★★ | `fetchLiveGauges()` |
| NDMA / PDMA alert integration and SMS cell broadcast to users of a district | ★★★ | `pushAlerts()` |
| Satellite inundation layers (Sentinel-1 / Copernicus EMS) on the map with a date slider | ★★★ | `MapCtl.addOverlay()` |
| Offline map tiles and installable PWA (service worker, manifest) | ★★ | `MapCtl.init()` |
| Relief coordination backend: sync stock, registrations and volunteers with OTP-verified reporters | ★★ | `syncRelief()` |
| Verified reunification backend with photo matching and privacy purge | ★★★ | `syncMissing()` |
| SMS / USSD access for feature phones ("SAILAB DADU" → level, advisory, nearest camp) | ★★ | `sosViaSms()` |
| Real road routing (OSRM / GraphHopper) avoiding reported flooded roads | ★★ | `routeEstimate()` |

Search the source for `TODO(student)` to find the exact hooks.

## Data / safety notes

All gauge series, rainfall, camp occupancy, stock, volunteers, contacts and posts are simulated seed data for demonstration; phone numbers are placeholders. FFD thresholds and historical peaks are approximate published values, labelled indicative in the UI. Helplines ship with common national numbers but must be verified per province before use. Nothing in this template replaces official NDMA / PDMA warnings. Reunification posts contain personal data and stay on the device; a real deployment needs consent, moderation and deletion rules.

## Evaluation ideas for the FYP report

Compare computed district levels with PDMA advisories for a past monsoon once live gauges are connected; run usability sessions with households in Urdu mode on low-end phones (bottom sheet, text size, SOS flow); measure how quickly camp managers can log stock and registrations versus paper registers.
