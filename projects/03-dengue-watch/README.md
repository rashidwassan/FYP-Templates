# 03 · Dengue Watch (idea #11)

**Surveillance, triage, vector control and prevention for dengue season, in one offline-first page.** Dark public-health dashboard for Lahore, Rawalpindi and Karachi: town-level case heat tiles with a weather-correlation panel, an anonymised case line list with epi curve and age/sex pyramid, larval-survey indices (HI / CI / BI), a WHO 2009 triage tool, a fever diary that detects the critical phase, an editable hospital bed and platelet board, a breeding-site reporter and campaign calendar on a map, a household risk quiz, a school module with lesson plans and quiz, a notifications inbox and settings with admin PIN and JSON export/import. English / اردو throughout.

**Scale:** 4,552 lines in one `index.html` (inline CSS + vanilla JS, no build step) · 15 routed modules plus the dashboard weather panel and onboarding tour · seed data per week: ~1,400 anonymised line-list cases for Lahore (≈2,900 across the three cities, always equal to the dashboard counts), 100 larval surveys, 21 hospitals, 24 campaign activities, 36 weeks of weather rows, 25 towns, 18 FAQ entries, 12 risk questions, 10 quiz questions, 3 lesson plans.

## Getting started

1. Open `index.html` in any modern browser. Everything except map tiles works offline from `file://`.
2. Serve over HTTP (`npx serve .` or `python -m http.server`) if you want the camera (`capture="environment"`) and GPS on the Report page; browsers block those on `file://`.
3. Leaflet + OpenStreetMap load lazily the first time the Report page opens; if the CDN is unreachable the map area shows a notice and reports still work from the list.
4. Data lives in `localStorage` under the `dengue:` prefix (see Settings → Storage for every key, record count and size). **Reset:** Settings → *Reset all data* (admin PIN if set), or clear site data in the browser.
5. Settings → *Show demo data* hides the seeded line list, surveys and campaigns so a team can start from a blank register; dashboard counts stay simulated and are labelled as such.

## The problem

Dengue returns to Punjab and Sindh every autumn. Breeding sites go unreported, families mistake the fever breaking for recovery when it is actually the start of the critical phase, and the wrong painkillers cause bleeding. District teams juggle case registers, larval surveys, fogging schedules and bed availability across separate spreadsheets, and public dashboards are not built for households or schools.

## What is built

| Area | Features |
|---|---|
| Dashboard | KPIs (cases this week, change, hotspot towns, open reports) with animated numbers, heat tiles per town with week-over-week change, 12-week bar + trend chart with tooltips, town detail with sparkline and deep links, city switcher, dismissible six-step onboarding tour |
| Weather panel | Seeded weekly rainfall / temperature / humidity per city, combo chart (cases bars, rainfall and temperature lines), Pearson correlation of rainfall vs cases at lags of 0–4 weeks with the strongest lag highlighted |
| Case line list | Anonymised records (age band, sex, town, onset date, severity DF / DWS / SD, outcome) generated to match dashboard counts, filters (town, severity, sex, period, source), KPIs incl. case fatality, stacked epidemic curve by onset week, age/sex pyramid, severity donut, outcome bars, paginated table, add-case form, CSV export |
| Vector surveillance | Larval survey form with live House / Container / Breteau Index preview and risk level, thresholds explainer, city-wide HI and BI trend by survey week, latest survey per town table, recent surveys list; 4 seeded surveys per town |
| Triage | WHO 2009 classification: probable dengue criteria, 7 warning signs, severe dengue signs, co-existing conditions → Group A / B / C with bilingual action lists, "return immediately if" list, critical-phase banner (fever days 3–7), platelet caveat, print, WhatsApp share, links to hospital board and diary |
| Fever diary | Multiple episodes, day strip (up to 14 days), phase indicator, daily temperature / platelets / haematocrit / fluids / urine / warning signs / notes, defervescence detection ("critical phase starts now"), red alert on any warning sign, dual-axis SVG chart with critical window shading, plain-text share with a doctor, print |
| Hospital board | 21 seeded facilities (govt / private / charity) with dengue beds, ICU beds, platelet units, phone and last-updated stamp; KPIs (free beds, free ICU, platelets, facilities ≥ 90 %), type filter and sort, occupancy bars, call link, admin-gated edit modal and "restore seeded values" |
| Report site | Breeding-site type and size, address, photo (resized to 320 px), GPS or map pin, status open/cleared, WhatsApp text with Google Maps link, markers coloured by status, toggleable campaign layer showing upcoming activities at town centres |
| Campaign planner | Month calendar with coloured dots per activity type (fogging, larviciding, awareness, school session, clean-up, survey), day panel, next-14-days list, KPIs, add/edit modal, done toggle, admin-gated deletion of seeded activities; editing a seed creates a user copy |
| Prevent | 8 weekly tasks (English + Urdu), completion ring, consecutive-week streak, configurable "dry day" with next-date banner, 8-week history, link to the risk check |
| Household risk check | 12 weighted yes / no / sometimes questions (reverse-scored where "No" is risky), live 0–100 score with band, tailored bilingual checklist of only the items that scored, saved history |
| School module | 3 lesson plans (grades 3–5, 6–8, 9–10: objectives, materials, timed activities, assessment, Urdu summaries) as printable accordions, 10-question quiz one question at a time with explanations, score card, attempts table, best / average, score distribution |
| Learn | 8 bilingual fact cards, editable local contacts, 18-entry FAQ with full-text search (English + Urdu, highlighted matches) and category chips |
| Notifications inbox | Alerts derived from visible data with stable ids and persistent read state: hotspot towns, weekly summary, open reports, campaigns today/tomorrow, high larval index per town, diary warning signs and critical-phase days, unfinished weekly checklist after the dry day, hospitals ≥ 90 % occupied; unread badge in rail, bottom nav and sheet |
| Settings | Language, city, demo-data switch, reduce-motion switch, replay tour, admin PIN (create / change / remove; hashed; unlocks for the session), JSON export of every `dengue:` key, import with validation, reset all, storage table |
| Shell | Grouped left rail (desktop), 4-item bottom nav + "More" sheet (mobile), hash router, bilingual dictionary `TXT.en` / `TXT.ur` (420 keys each), icon sprite, motion kit, print stylesheet, keyboard-closable modals and sheet |

## Module map

| Module | Main functions | Storage keys |
|---|---|---|
| utils / shell | `store`, `toast`, `animateIn`, `countUp`, `hash`, `rng`, `isoWeek`, `openModal`, `openSheet`, `requireAdmin`, `go`, `renderNav`, `applyLang` | `lang`, `city`, `settings`, `pin`, `tour` |
| seed data | `weekly`, `seasonal`, `weather`, `lineList`, `seedSurveys`, `indices`, `riskLevel`, `seedHospitals`, `hospitals`, `seedCampaigns`, `campaignsAll` | — (seeds are regenerated; see modules below for overrides) |
| dashboard + weather + tour | `renderDash`, `renderTiles`, `renderTownDetail`, `barChart`, `renderWeather`, `comboChart`, `renderTourCard`, `startTour` | `tour`, `reports` (read) |
| case line list | `renderCases`, `filteredCases`, `epiCurve`, `pyramid`, `donut`, `outcomeBars`, `caseTable`, `openCaseForm`, `exportCasesCsv` | `cases` |
| vector surveillance | `renderVector`, `surveyForm`, `readSurveyForm`, `surveyTrend`, `surveyList`, `lineChart` | `surveys` |
| triage | `renderTriageForm`, `triage`, `readTriageForm`, `runTriage` | — |
| fever diary | `renderDiary`, `diaryHeader`, `dayStrip`, `diaryForm`, `bindDiary`, `diarySummary`, `dualChart` | `episodes`, `ep` |
| hospital board | `renderHospitals`, `hospitalRow`, `openHospitalForm`, `saveHospitals` | `hospitals` |
| reports + map | `ensureMap`, `drawMarkers`, `drawCampaignMarkers`, `renderReport`, `submitReport`, `reportCard`, `renderRepList`, `resizeImage` | `reports`, `campLayer` |
| campaign planner | `renderCampaigns`, `renderCalendar`, `renderDayPanel`, `activityCard`, `bindActivityButtons`, `openCampaignForm` | `campaigns`, `campaigns:hidden`, `campaigns:done` |
| prevention | `renderPrevent`, `preventionStreak` | `prev:<ISO week>`, `dryday` |
| household risk | `renderRisk`, `riskPoints`, `riskBand`, `renderRiskChecklist` | `risk` |
| school | `renderSchool`, `renderLessons`, `renderQuiz`, `saveQuizAttempt`, `renderQuizResult`, `renderScores` | `quiz` |
| learn + FAQ | `renderLearn`, `renderFaq`, `highlight` | `contacts` |
| notifications | `buildNotifications`, `unreadCount`, `refreshBell`, `renderInbox` | `notif:read` |
| settings | `renderSettings`, `openPinForm`, `exportAll`, `importAll`, `saveSettings` | `settings`, `pin` (+ every key via export/import/reset) |
| about | `renderAbout`, `TODOS` | — |

## Architecture (single file)

```
index.html
├─ <style>        tokens, grouped rail (desktop) / bottom nav + sheet (mobile), cards, KPIs, tiles,
│                 charts, forms, tables, calendar, hospital rows, quiz, FAQ, inbox, modal, print, motion kit
├─ <svg sprite>   kit icons + custom bug / hospital / bed / spray symbols
├─ <aside.rail>   brand, grouped nav (Surveillance · Clinical · Community · More), city, language, bell
├─ <main>         fifteen <section data-page> blocks toggled by go()
│    dash · cases · vector · triage · diary · hospitals · report · campaigns · prevent · risk · school ·
│    learn · inbox · settings · about
└─ <script>
   ├─ utils         store (get/set/del/keys), toast, motion helpers, hash/rng, dates, pearson, csv
   ├─ settings      settings(), saveSettings(), admin PIN (pinHash, requireAdmin), modal + sheet
   ├─ i18n          TXT.en / TXT.ur (named TXT because Leaflet owns window.L), t(), nm(), pick()
   ├─ seed data     CITIES → towns, seasonal() curve, weekly(), weather(), lineList(), seedSurveys(),
   │                indices()/riskLevel(), HOSPITAL_SEED, CAMP_TYPES/seedCampaigns(), campaignsAll()
   ├─ dashboard     renderDash(), barChart(), attachBarTips(), renderWeather(), comboChart(), tour
   ├─ cases         caseFilter, renderCases(), epiCurve(), pyramid(), donut(), caseTable(), CSV
   ├─ vector        renderVector(), surveyForm() live preview, surveyTrend(), lineChart()
   ├─ triage        PROB / WARN / SEV / CO, triage(), runTriage()
   ├─ diary         renderDiary(), dualChart(), defervescence logic, diarySummary()
   ├─ hospitals     renderHospitals(), hospitalRow(), openHospitalForm() (admin)
   ├─ reports       ensureMap() lazy Leaflet, drawMarkers(), drawCampaignMarkers(), renderReport()
   ├─ campaigns     renderCampaigns(), renderCalendar(), openCampaignForm()
   ├─ prevention    TASKS, renderPrevent(), preventionStreak()
   ├─ risk          RISK_Q, renderRisk(), renderRiskChecklist()
   ├─ school        LESSONS, QUIZ, renderSchool() tabs
   ├─ learn         FACTS, FAQ, renderLearn(), renderFaq()
   ├─ inbox         buildNotifications(), renderInbox(), refreshBell()
   ├─ settings      renderSettings(), openPinForm(), exportAll(), importAll()
   └─ about / nav   TODOS, NAV_GROUPS, RENDER, go(), renderNav(), openMoreSheet(), applyLang()
```

## Domain logic notes

### Triage decision (WHO 2009)

```
severe sign present                         → Group C (emergency)
any warning sign                            → Group B (hospital today)
fever + ≥2 probable criteria + co-existing  → Group B
fever + ≥2 probable criteria                → Group A (home care, daily review)
otherwise                                   → dengue less likely
```

### Larval indices

`HI = houses positive / houses inspected × 100` · `CI = containers positive / containers inspected × 100` · `BI = containers positive / houses inspected × 100`. Risk level: **low** HI < 1 % and BI < 5 · **moderate** HI 1–5 % or BI 5–20 · **high** HI > 5 % or BI > 20. The trend chart aggregates all towns per ISO week (sum of positives / sum inspected) so small towns do not dominate.

### Household risk score

Each question has a weight (1–3). "Yes" scores the weight unless the question is reverse-scored (where "No" is the risky answer); "Sometimes" scores half. `score = round(points / 24 × 100)`; bands: < 25 low, 25–54 moderate, ≥ 55 high. The checklist shows only the action for every answer that scored.

### Weather correlation

Pearson r between rainfall in week *i* and cases in week *i + lag* for lag 0…4 on the 12 dashboard weeks; the largest |r| is highlighted. Twelve points is indicative only, which the panel says; forecasting is a student TODO.

### Seeding

Case counts are simulated: each town has a baseline multiplied by a seasonal Gaussian peaking around 15 October (day 288) with ±~30 % noise, re-seeded every ISO week. The line list is generated *from* those counts (one record per case with weighted age bands, 56 % male, 80 / 16 / 4 % severity split and recency-aware outcomes), so every chart agrees with the dashboard. Surveys, weather, hospitals and campaigns use the same seeded PRNG keyed by city and week.

## Left for students

| Extension | Difficulty | Start here |
|---|---|---|
| Real surveillance feed (provincial dashboards, DHIS2 exports) with "data as of" stamp | ★★ | `weekly()` |
| Line-list import (CSV/XLSX) with column mapping and de-duplication | ★★ | `exportCasesCsv()` |
| Report backend with photo upload, routing to TMA / DHO, inspector verification | ★★ | `renderReport()` HTML comment |
| Push / SMS delivery of inbox alerts, geofenced; mohalla prevention leaderboard | ★★ | `buildNotifications()` |
| Outbreak forecasting from real rainfall/temperature with a 2–4 week lag band | ★★★ | `weather()` |
| Offline map tiles (service worker) or schematic SVG town map fallback | ★★ | `ensureMap()` catch branch |
| Clinician ward dashboard importing fever-diary exports | ★★★ | `diarySummary()` |
| Live hospital bed-management API with "last synced" stamp | ★★ | `openHospitalForm()` |

Search the source for `TODO(student)`; the About page lists the same eight items.

## Data and safety notes

- The triage follows the WHO 2009 case classification and is a screening aid only. Helplines shown: Rescue 1122 and Sehat Tahaffuz 1166. Users should add their district health office in Learn → Local contacts.
- Hospital names are real public facilities but the bed, ICU, platelet figures and phone numbers are **illustrative placeholders** and are labelled as such on the page; replace them with the district control-room feed before any real use.
- The line list is anonymised by design (age band, sex, town, onset week only) and the add-case form says so. Nothing leaves the device: no analytics, no network calls except Google Fonts and the lazily loaded Leaflet/OSM tiles.
- The admin PIN is hashed (FNV-1a) and stored locally; it protects against casual edits on a shared device, not against a determined attacker with browser access.
