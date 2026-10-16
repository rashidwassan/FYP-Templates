# 07 · LHW Field Visit Assistant (idea #16)

**The paper register, with a brain.** An offline-first visit planner for Pakistan's Lady Health Workers: ANC and postnatal schedules computed from the LMP, automatic danger-sign flags during a visit, a printable referral slip in English and Urdu, the Pakistan EPI immunization card, weight-for-age screening and a one-tap monthly report.

## Getting started

- Open `index.html` in any modern browser (Chrome on Android is the target). No build step, no server, no dependencies beyond Google Fonts (falls back to system fonts offline).
- Everything works from `file://`. Serve over HTTP/HTTPS only if you later add GPS, camera or the Web Speech API — browsers refuse those on `file://`.
- Data lives in `localStorage` under the `lhw:` prefix (`households`, `women`, `pregnancies`, `anc`, `pnc`, `children`, `profile`, `lang`, `lastSync`). Every record carries `id`, `createdAt`, `updatedAt` and `synced`.
- **Sync → Reset to seed data** restores the demo village; **Export JSON backup / Import JSON** moves data between phones.
- Set the LHW's name, code, catchment and referral facilities (BHU / RHC / DHQ, LHS phone) in **About → LHW profile**; they print on every referral slip and report.

## The problem

Pakistan's roughly 100,000 Lady Health Workers record maternal and child visits in paper registers. Danger signs are missed because nobody prompts for them, due dates are miscalculated, referrals are verbal and untraceable, immunization drop-outs are discovered late, and the monthly report is tallied by hand. Phones are everywhere; network is not.

## What is built

| Area | Features |
|---|---|
| Today | Planner of what is due today / this week and what is overdue across every household: ANC contacts, postnatal days 1·3·7·42, vaccines, monthly weighing, plus a "needs attention" strip for recent referrals and severely underweight children. One tap starts the right form. KPI counters, badges on the nav. |
| Households | Searchable list (head, mohalla, mother or child name) → detail with women, pregnancy status, children with immunization and growth chips. Register household, add woman, add child, register pregnancy (LMP → EDD live). Desktop: two-column list + detail; phone: list → detail with back button. |
| Pregnancy | Header with GA today, EDD, G/P, risk factors (age auto-detected). Timeline of WHO 2016 eight contacts plus the Pakistan MNCH four mandatory visits with done / due / overdue / missed status. ANC visit form with live assessment: BP, Hb, fundal height, fetal movement, nine danger signs (English + Urdu), TT, iron/folate, counselling checklist. Referral urgency with manual override, destination (BHU / RHC / DHQ), reason. Delivery outcome creates the child and starts the postnatal schedule. Postnatal form with mother + newborn danger signs. |
| Referral slip | Printable A5-style slip: patient, household, GA/EDD, risk factors, findings, danger signs in both languages, urgency, destination, LHW identity, signature boxes. Print CSS hides the chrome. SMS/share button (gateway is a student TODO). |
| Children | Pakistan EPI card by age group (birth, 6/10/14 wk, 9 mo, 15 mo) with due dates from DOB, given / due / overdue / upcoming, one-tap "Give today" with editable date and undo, coverage bar. Growth: weight-for-age SVG chart with WHO median and 80 % / 70 % screening lines, weight history, add weight (+ MUAC field). Newborn danger-sign check for babies under 2 months. |
| Report | Monthly counts (new registrations, ANC, PNC, referrals, deliveries, immunizations given, children weighed), six-month bar chart, line list, CSV export, print layout. |
| Sync | Queue of unsynced records by table, simulated "Sync now" with progress, last-sync stamp, JSON backup/import, reset. |
| i18n & UX | English UI with Urdu toggle; every danger sign, counselling message, risk factor and urgency label is always shown in both languages (Noto Nastaliq Urdu, RTL). Atkinson Hyperlegible for legibility in sunlight, 44–56 px tap targets, bottom tab bar on phones, SVG icon sprite (no emoji), motion kit under `prefers-reduced-motion`. |

## Architecture (single file)

```
index.html
├─ <style>      tokens (orange / deep teal / cream), motion kit, shell (rail → bottom tabs),
│               cards · buttons · chips · forms · checklists · split list+detail · timeline ·
│               EPI card · charts · referral slip · print rules
├─ <svg>        inline icon sprite (Lucide-style symbols, ic('name') renders <use>)
├─ <aside.rail> brand, nav, LHW profile, language toggle   ·  <header.topbar> phone header
├─ <main #view> rendered by one function per route
└─ <script>
   ├─ utils            $, store (namespaced localStorage), esc, ic, toast, animateIn/countUp/drawPath,
   │                   local-time date helpers (iso, parse, addDays, diffDays, relDays)
   ├─ i18n             L.en / L.ur UI dictionary, t(), bi(), both()
   ├─ reference data   CONTACTS, PNC_DAYS, PREG/PNC/NB danger signs, counselling, RISKS, URG, EPI, WFA medians
   ├─ store & seed     DB tables, put/remove/unsynced, seed() → one village, 3 households, 4 women,
   │                   3 pregnancies, 6 ANC + 4 PNC visits, 4 children
   ├─ clinical logic   ga(), schedule(), pncSchedule(), assessAnc(), assessSigns(), vaxGroups(),
   │                   median()/growthStatus(), planner()
   ├─ views            renderToday · renderHouseholds(+detail/form/sheets) · renderPregnancies
   │                   (+detail, ANC form, PNC form, delivery, slip) · renderChildren(+child, growthChart)
   │                   · renderReport(+barChart, CSV) · renderSync · renderAbout
   └─ router           #today · #households[/id|/new] · #pregnancy[/id[/visit|/pnc|/delivery|/slip/:visitId]]
                       · #children[/id] · #report · #sync · #about
```

## Clinical logic notes

- **Dating.** EDD = LMP + 280 days (Naegele). GA is computed in completed weeks + days for any date, so every visit, flag and contact window is relative to the LMP.
- **ANC schedule.** WHO 2016 contacts at 12, 20, 26, 30, 34, 36, 38, 40 weeks; the Pakistan MNCH four-visit minimum (< 16, 24–28, 32, 36 wk) is marked *mandatory*, with 32 wk added as its own row. Each contact's window is due − 7 d to due + 14 d: a visit inside the window covers it; past the window it is *overdue*, and *missed* once a later visit exists or six weeks have passed. The planner always offers the contact due now before chasing a missed one.
- **Visit flags.** BP ≥ 140/90 → pre-eclampsia alert (same-day); with headache / blurred vision / swelling / fits or ≥ 160/110 → emergency. Hb < 7 → severe anaemia (refer); 7–10.9 → anaemia (iron/folate + counselling). After 24 wk, fundal height outside ± 3 cm of GA → size–dates flag. After 28 wk, reduced or absent fetal movement → same-day referral. Any danger sign raises the urgency to its own level (emergency / same day / next clinic); default destination is DHQ / RHC / BHU respectively. The LHW can override urgency and destination.
- **Postnatal.** Visits at day 1, 3, 7 and 42 after delivery; six maternal and eight newborn danger signs; a baby not breastfeeding well is a same-day referral; a newborn under 2.5 kg is pre-ticked as "very small".
- **EPI.** Due date = DOB + 0 / 42 / 70 / 98 / 274 / 456 days. *Due* from the due date, *overdue* 14 days later.
- **Growth.** Weight ÷ WHO median weight-for-age (approximate medians by month, 0–24 months, boys and girls, linearly interpolated). < 80 % underweight, < 70 % severely underweight. This is a **simplified screening rule, not WHO z-scores** — the chart and labels say so, and z-scores are the first clinical TODO below.

## Left for students

| Extension | Difficulty | Start here |
|---|---|---|
| Real sync backend: PouchDB → CouchDB replication or a REST API with conflict resolution, retry queue and auto-sync on `navigator.onLine` | ★★★ | `syncNow()` |
| SMS referral to the Lady Health Supervisor / receiving facility through an SMS gateway, with delivery status on the visit | ★★ | `renderSlip()` → `#sSms` |
| GPS mapping of households and a Leaflet catchment map coloured by what is due, for route planning | ★★ | `renderHouseholdForm()` → `#fGps` |
| Supervisor (LHS) dashboard aggregating many LHWs: mandatory-visit coverage, referral follow-up, immunization drop-out, overdue map | ★★★ | `monthCounts()`, `planner()` |
| WHO z-score growth standards (LMS tables for weight-for-age, length-for-age, weight-for-length) with −2 / −3 SD curves | ★★ | `zScore()`, `growthStatus()`, `growthChart()` |
| MUAC-based malnutrition screening (SAM < 11.5 cm, MAM 11.5–12.4 cm) routed to the OTP site on the slip | ★ | `muacStatus()`, `renderChild()` |
| Urdu / Sindhi voice input for complaints and notes (Web Speech API `ur-PK`, or an offline Vosk model) | ★★ | `renderAncForm()` → `#vVoice` |
| DHIS2 export: map indicators to data elements and POST a `dataValueSet` for the period and org unit | ★★ | `renderReport()` → `#rDhis` |

Search the source for `TODO(student)` to find the exact hooks. Other good FYP additions: installable PWA (manifest + service worker), family-planning module, a tally of stock (iron/folate, ORS, contraceptives), and a photo of the paper register page attached to each sync batch.

## Data and safety notes

The seed village, names and readings are fictional. Thresholds follow WHO ANC (2016), WHO/IMNCI danger-sign lists, Pakistan's MNCH programme and the 2024 EPI card, but they are implemented as a decision aid for trained LHWs — not a diagnostic device and not a replacement for the official register or for clinical judgement at the facility. Growth screening uses percent-of-median, which is coarser than z-scores. All data stays on the device until a real sync backend is built; do not store real patient data in this template without your institution's approval.
