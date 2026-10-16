# 11 · Dengue Watch

**Surveillance, triage and prevention for dengue season, in one offline-first page.** Dark public-health dashboard for Lahore, Rawalpindi and Karachi with town-level case heat tiles, a WHO 2009 triage tool, a fever diary that detects the critical phase, a breeding-site reporter on a map, and a weekly prevention checklist. English / اردو.

Open `index.html`. Everything except map tiles works offline; Leaflet and OpenStreetMap load only when the Report page is opened.

## The problem

Dengue returns to Punjab and Sindh every autumn. Breeding sites go unreported, families mistake the fever breaking for recovery when it is actually the start of the critical phase, and the wrong painkillers cause bleeding. Public dashboards exist but are not built for households.

## What is built

| Area | Features |
|---|---|
| Dashboard | KPIs (cases this week, change, hotspot towns, open reports), heat tiles per town with week-over-week change, 12-week bar + trend chart with tooltips, town detail with sparkline, city switcher |
| Triage | WHO 2009 classification: probable dengue criteria, 7 warning signs, severe dengue signs, co-existing conditions → Group A / B / C with bilingual action lists, "return immediately if" list, critical-phase banner (fever days 3–7), platelet caveat, print and WhatsApp share |
| Fever diary | Multiple episodes, day strip (up to 14 days), phase indicator, daily temperature / platelets / haematocrit / fluids / urine / warning signs / notes, defervescence detection ("critical phase starts now"), red alert on any warning sign, dual-axis SVG chart with critical window shading |
| Report site | Breeding-site type and size, address, photo (resized to 320 px), GPS or map pin, status open/cleared, WhatsApp text with Google Maps link, markers coloured by status |
| Prevent | 8 weekly tasks (English + Urdu), completion ring, consecutive-week streak, configurable "dry day", 8-week history |
| Learn | 8 bilingual fact cards (daytime biting, clean water, critical phase, paracetamol only, platelets, serotypes, fluids, helplines) plus editable local contacts |

## Architecture (single file)

```
index.html
├─ <style>        dark tokens, left rail (desktop) / bottom nav (mobile), cards, tiles, forms, charts
├─ <aside.rail>   brand, nav, city selector, language toggle
├─ <main>         seven <section data-page> blocks toggled by go()
└─ <script>
   ├─ utils        store, seeded RNG, ISO week helpers
   ├─ i18n         L.en / L.ur
   ├─ seed data    CITIES → towns (lat/lng, baseline), seasonal() curve, weekly() generator
   ├─ dashboard    renderDash(), barChart()
   ├─ triage       PROB / WARN / SEV / CO lists, triage() decision function, ADVICE, runTriage()
   ├─ diary        renderDiary(), dualChart(), defervescence logic
   ├─ reports      ensureMap() lazy Leaflet, renderReport(), renderRepList(), drawMarkers()
   ├─ prevention   TASKS, renderPrevent() with streak computation
   └─ learn/about  FACTS, TODOS
```

### Triage decision (for the report)

```
severe sign present                         → Group C (emergency)
any warning sign, or probable dengue with
a co-existing condition / social risk       → Group B (hospital today)
fever + ≥2 probable criteria                → Group A (home care, daily review)
otherwise                                   → dengue less likely
```

The case counts are simulated: each town has a baseline multiplied by a seasonal Gaussian peaking around 15 October (day 288) with ±~30 % noise, re-seeded every ISO week so the dashboard changes weekly but stays stable within a week.

## Left for students

| Extension | Difficulty | Start here |
|---|---|---|
| Real surveillance feed (provincial dengue dashboards, DHIS2 exports, hospital line lists) | ★★ | `weekly()` |
| Report backend with photo upload, routing to TMA / District Health Office, inspector verification | ★★ | `#rGo` handler in `renderReport()` |
| Hotspot SMS/push alerts and geofenced breeding-site warnings | ★★ | new module + backend |
| Outbreak forecasting from rainfall/temperature with a 2–4 week lag | ★★★ | `barChart()` for the forecast band |
| Offline map tiles or schematic SVG town map fallback | ★★ | `ensureMap()` catch branch |
| Clinician ward dashboard importing diary exports | ★★★ | new page |
| Mohalla prevention leaderboard with verified badges | ★ | `renderPrevent()` |

Search the source for `TODO(student)`.

## Safety note

The triage follows the WHO 2009 case classification and is a screening aid only. Helplines shown: Rescue 1122 and Sehat Tahaffuz 1166. Users should add their district health office in Learn → Local contacts.
