# 07 · LHW Field Visit Assistant (idea #16)

**Scale:** ~5,030 lines in one `index.html` · 17 modules (7 core MNCH views + 10 programme/admin modules) · 90 seed records (77 rows across 13 tables, 4 LHW profiles, 9 supervisor summaries) · 573 bilingual UI strings.

**The paper register, with a brain.** An offline-first field tool for Pakistan's Lady Health Workers. It covers the MNCH round: ANC and postnatal schedules from the LMP, automatic danger-sign flags, printable bilingual referral slips, the Pakistan EPI card and growth screening. It also covers the wider LHW programme: family-planning counselling, MUAC nutrition screening, TB and malaria symptom screens, kit supplies, polio NID tallies, birth and death registration, a PIN-gated supervisor view and a monthly report with a DHIS2-style export.

## Getting started

- Open `index.html` in any modern browser (Chrome on Android is the target). There is no build step, no server and no dependency beyond Google Fonts, which fall back to system fonts offline.
- Everything works from `file://`. Serve over HTTP/HTTPS only if you later add GPS, camera or the Web Speech API, because browsers refuse those on `file://`.
- Data lives in `localStorage` under the `lhw:` prefix:
  - **Synced tables:** `households`, `women`, `pregnancies`, `anc`, `pnc`, `children`, `fp`, `nutrition`, `screenings`, `stock`, `bplans`, `nid`, `vitals`. Every record carries `id`, `createdAt`, `updatedAt` and `synced`.
  - **Device settings:** `profile`, `lhws`, `summaries`, `supplies`, `pin`, `lang`, `lastSync`, `seeded`, `seedVersion`.
- Data from the earlier MNCH-only version is kept. On first load, the programme tables are added beside it (`seedVersion` 2), and old MNCH-only JSON backups still import.
- **Sync → Reset to seed data** restores the demo village. **Export JSON backup / Import JSON** moves data between phones.
- **Supervisor mode** demo PIN: `1234`. You can change it once unlocked.
- Set the LHW's identity and referral facilities (BHU / RHC / DHQ / OTP site, LHS phone) in **About → LHW profile**. They print on every slip and report.

## The problem

Pakistan has roughly 100,000 Lady Health Workers, and they record maternal and child visits, family-planning clients, stock and campaign tallies in paper registers. This causes several problems:

- Danger signs are missed because nothing prompts for them.
- Due dates are miscalculated.
- Referrals are verbal and cannot be traced.
- Immunization drop-outs and malnourished children are found late.
- Kit items run out without warning.
- The monthly report is tallied by hand.

Phones are everywhere; network coverage is not.

## What is built

| Area | Features |
|---|---|
| Today | A planner of what is due today, due this week and overdue across every module: ANC contacts, PNC days 1, 3, 7 and 42, vaccines, monthly weighing, FP follow-ups, vitamin A / deworming / MNP doses, screening outcomes still pending, an NID campaign running today and vital events not yet reported. A "needs attention" strip shows recent referrals, severe underweight, SAM and stock-outs. It also has KPI counters, nav badges and quick actions. |
| Households | A searchable register. The detail view shows women (with pregnancy and FP status), children (with EPI, growth and MUAC chips) and a "Screen TB / malaria" shortcut. You can register households, women, children and pregnancies. Deleting a household cascades to every linked record. |
| Pregnancy | Header with GA, EDD and G/P; risk factors; the WHO 2016 eight-contact plus PK MNCH four-visit timeline; an ANC form with live assessment (BP, Hb, fundal height, fetal movement, nine danger signs) and an urgency override. The referral destination follows the urgency until the LHW picks one by hand. Iron-folate given at a visit is dispensed from stock. Recording the delivery creates the child and a birth or stillbirth record in the vital-events log. The PNC form checks the mother and the newborn. |
| **Birth-preparedness plan** | A per-pregnancy plan: place of delivery, skilled attendant, transport, money saved, blood donor, companion, plus four arrangement checks. It shows a live readiness bar (n/10) and a summary card on the pregnancy page, and it prints. |
| Referral slips | Printable A5-style slips in both languages, built by one shared `slipArticle()` and used for ANC/PNC, SAM → OTP and TB/malaria referrals. Share/SMS uses the Web Share API or an `sms:` link. |
| Children | The Pakistan EPI card (give today / undo / coverage bar), a weight-for-age SVG chart against the WHO median and the 80 % / 70 % lines, and the newborn danger-sign check. A MUAC typed beside a weight also creates a nutrition assessment. |
| **Family planning** | Eleven suitability questions drive a simplified MEC-style rating ("suitable", "caution" or "avoid", with reasons) for ten methods. Choosing a method records it and dispenses the resupply from stock. A follow-up schedule is computed per method (offsets, then a repeating interval). There are follow-up and stop actions, a counselling history and KPIs (users, follow-ups due, women with no method who are not pregnant). |
| **Nutrition** | MUAC classification (SAM, MAM or normal, with bilateral oedema counted as SAM), a live banner, and a SAM → OTP referral slip. A MUAC trend chart has shaded SAM/MAM bands. The IYCF counselling checklist changes with the child's age band (0–5, 6–8, 9–11, 12–23 months, 2–4 y). The vitamin A / deworming / MNP schedule shows per-child due status and dispenses from stock. |
| **TB & malaria screening** | Symptom checklists for any household member, in both languages. The rule-based result is "presumptive TB", "suspected malaria" or "severe malaria emergency", with urgency and destination. It produces a referral slip and tracks the facility outcome (pending, confirmed, ruled out, did not attend). When TB is confirmed, it prompts a household contact screen. You can filter the log by type or by pending outcome. |
| **Supplies** | Twelve kit items (ORS, zinc, iron-folate, contraceptives, vitamin A, albendazole, MNP and others) tracked by replaying stock movements (receipt, dispensing, physical count). Shows status (stock-out, low, OK), average monthly consumption, months of stock and expiry warnings, plus a six-month consumption bar chart. A printable request sheet tops each item up to two months of stock, with CSV export. ANC, FP and nutrition dispense from stock automatically. |
| **Polio NID tally** | Campaigns (name, dates, team, target, vaccine) with a large-button daily tally for houses visited, 0–11 m, 12–59 m, finger-marked, refusals and not available. It shows coverage of target, a by-day table, and a missed-children revisit list with covered ticks. You can close and reopen a campaign. |
| **Births & deaths** | A log of births, stillbirths and deaths with place, age at death and probable cause. A death is categorised as maternal, neonatal, child or adult, and maternal or neonatal deaths raise alerts. The registration pipeline (recorded → reported to LHS → registered at the Union Council) has dated steps, filters and KPIs. |
| **Supervisor mode** | Unlocked with a PIN (hashed and stored locally). Twelve monthly indicators per LHW, shown as traffic-light bars against illustrative targets: ANC4, SBA, PNC within 48 hours, fully immunized, Penta drop-out, weighing and others. It has a three-month selector, a composite-score ranking and LHW profile management (add, edit, remove, make active). The active LHW's figures are computed live; the others are synced demo summaries. |
| Report | MNCH and programme counts (17 indicators), a six-month bar chart, a line list across every module, CSV export, print, and a **DHIS2-style `dataValueSet` JSON** preview and download. |
| Sync | The queue of unsynced records across all 13 tables, a simulated "Sync now", JSON backup and import (programme tables optional, for older backups), and reset. |
| More / i18n / UX | A module hub with badges, so the phone keeps five daily tabs plus "More". The English UI has an Urdu toggle, with every new string in both languages; clinical content is always bilingual in Noto Nastaliq Urdu. Other details: Atkinson Hyperlegible, 44–56 px tap targets, a bottom tab bar on phones, KPI strips hidden on phone detail views, an SVG sprite with no emoji, and the motion kit under `prefers-reduced-motion`. |

## Architecture (single file)

```
index.html
├─ <style>      tokens (orange / deep teal / cream), motion kit, shell (grouped rail → bottom tabs + More),
│               cards · buttons · chips · forms · checklists · split list+detail · timeline · EPI card ·
│               charts · module tiles · FP suitability list · NID tally counters · PIN lock · indicator
│               table · registration steps · referral slip · print rules
├─ <svg>        inline icon sprite (Lucide-style, ic('name') renders <use>)
├─ <aside.rail> brand, grouped nav (Daily work / Programmes / Admin), LHW profile, language toggle
├─ <main #view> rendered by one function per route
└─ <script>
   ├─ utils            $, store, esc, ic, hashStr, toast, animateIn/countUp/drawPath/animateBars/bump,
   │                   local-time date helpers, lastMonths, download, csvCell
   ├─ i18n             L.en / L.ur (573 keys each), t(), bi(), both()
   ├─ clinical data    CONTACTS, PNC_DAYS, PREG/PNC/NB danger signs, counselling, RISKS, URG, EPI, WFA
   ├─ programme data   FP_METHODS/QUESTIONS/RULES, MUAC_CUTS, IYCF, SUPPLEMENTS, TB_SIGNS, MAL_SIGNS,
   │                   SUPPLY_ITEMS, BP_FIELDS/CHECKS, NID_METRICS, DEATH_CAUSES, INDICATORS, DHIS_MAP
   ├─ store & seed     13 tables, put/remove/unsynced, seed() (MNCH) + seedProgramme() (upgrade-safe),
   │                   seedStock(), seedLhws(), seedSummaries(), defaultSupplies()
   ├─ clinical logic   ga(), schedule(), pncSchedule(), assessAnc(), assessSigns(), vaxGroups(),
   │                   median()/growthStatus(), muacStatus(), zScore() stub
   ├─ programme logic  fpSuitability/fpSequence/fpStatus, suppStatus, screenResult, stockLevel/stockStatus/
   │                   dispense, deathCategory, monthCounts, indicatorsFor/indicatorCls/indicatorScore
   ├─ planner          plannerMnch() + plannerProgramme() → planner()
   ├─ shared UI        chip, kpi, checklist, segControl, destSelect, openSheet, slipArticle, shareSlip,
   │                   groupedBars
   ├─ views            renderToday · renderHouseholds(+form/detail/sheets) · renderPregnancies(+ANC, PNC,
   │                   delivery, birth plan, slip) · renderChildren(+growthChart) · renderFp · renderNutrition
   │                   (+muacChart, OTP slip) · renderScreening(+form, detail, slip) · renderSupplies
   │                   (+detail, request sheet) · renderNid · renderVitals · renderReport(+DHIS2 sheet) ·
   │                   renderSync · renderSupervisor(+lock, ranking, LHW sheet) · renderMore · renderAbout
   └─ router           #today · #households[/id|/new] · #pregnancy[/id[/visit|/pnc|/delivery|/plan|/slip/:vid]]
                       · #children[/id] · #fp[/womanId] · #nutrition[/childId[/slip/:id]]
                       · #screening[/id|/new[/hhId]|/slip/:id] · #supplies[/item|/request] · #nid[/id|/new]
                       · #vitals · #report · #sync · #supervisor · #more · #about
```

## Module map

| Module | Main functions | Storage keys |
|---|---|---|
| Today planner | `planner()`, `plannerMnch()`, `plannerProgramme()`, `renderToday()` | reads all tables |
| Households | `renderHouseholds()`, `renderHouseholdDetail()`, `deleteHousehold()`, `openWomanSheet()`, `openChildSheet()` | `lhw:households`, `lhw:women` |
| Pregnancy + ANC/PNC | `schedule()`, `assessAnc()`, `renderAncForm()`, `saveAncVisit()`, `renderPncForm()`, `saveDelivery()`, `renderSlip()` | `lhw:pregnancies`, `lhw:anc`, `lhw:pnc` |
| Birth-preparedness plan | `renderBirthPlan()`, `bplanScore()`, `bplanSummaryCard()` | `lhw:bplans` |
| Children (EPI, growth) | `vaxGroups()`, `growthStatus()`, `renderChild()`, `growthChart()`, `saveWeight()` | `lhw:children` |
| Family planning | `fpSuitability()`, `fpSequence()`, `fpStatus()`, `renderFp()`, `renderFpDetail()`, `saveFp()`, `fpFollowUpDone()` | `lhw:fp` (+ `lhw:stock`) |
| Nutrition | `muacStatus()`, `suppStatus()`, `iycfBand()`, `renderNutrition()`, `saveNutrition()`, `giveSupplement()`, `muacChart()` | `lhw:nutrition`, `lhw:children` (supplements) |
| TB & malaria | `screenResult()`, `renderScreening()`, `renderScreeningForm()`, `saveScreening()`, `renderScreeningSlip()` | `lhw:screenings` |
| Supplies | `stockLevel()`, `stockStatus()`, `amc()`, `dispense()`, `renderSupplies()`, `saveMovement()`, `requestLines()` | `lhw:stock`, `lhw:supplies` |
| Polio NID | `nidTotals()`, `renderNid()`, `renderNidCampaign()`, `openMissedSheet()` | `lhw:nid` |
| Births & deaths | `deathCategory()`, `nextVitalStep()`, `renderVitals()`, `openVitalSheet()` | `lhw:vitals` |
| Supervisor | `indicatorsFor()`, `indicatorCls()`, `indicatorScore()`, `lhwValues()`, `renderSupervisor()`, `switchLhw()` | `lhw:pin`, `lhw:lhws`, `lhw:summaries`, `lhw:profile` |
| Report + DHIS2 | `monthCounts()`, `lineList()`, `renderReport()`, `dhisPayload()`, `openDhisSheet()` | reads all tables |
| Sync | `unsynced()`, `syncNow()`, `exportBackup()`, `importBackup()` | all tables, `lhw:lastSync` |
| Settings / i18n | `applyLang()`, `saveProfile()`, `renderNav()`, `moduleBadges()` | `lhw:lang`, `lhw:profile` |

## Domain logic notes

> All thresholds, schedules, suitability rules, cut-offs and targets below are **illustrative training values**. The programme's official guidelines and clinical judgement at the facility override them.

- **Dating and ANC.**
  - EDD = LMP + 280 days. GA is computed in completed weeks and days for any date.
  - The WHO 2016 contacts fall at 12/20/26/30/34/36/38/40 weeks, and the PK MNCH four visits are marked *mandatory*.
  - Each contact's window runs from 7 days before to 14 days after it is due. After the window it is *overdue*, and it becomes *missed* once a later visit exists or six weeks have passed.
- **Visit flags.**
  - BP ≥ 140/90 raises a pre-eclampsia alert. With headache, vision change, swelling or fits, or BP ≥ 160/110, it is an emergency.
  - Hb < 7 g/dL needs referral; Hb 7–10.9 g/dL is anaemia.
  - After 24 weeks, fundal height more than ±3 cm from GA is flagged.
  - After 28 weeks, reduced fetal movement means same-day referral.
  - Urgency picks a default destination (BHU, RHC or DHQ). The LHW can override both.
- **EPI and growth.** Vaccine due date = DOB + 0 / 42 / 70 / 98 / 274 / 456 days, and a dose is overdue 14 days after that. Growth is screened as percent of the WHO median weight-for-age: < 80 % is underweight and < 70 % is severe. This is not a z-score.
- **MUAC (6–59 months).**
  - < 11.5 cm is SAM: refer to the OTP site.
  - 11.5–12.4 cm is MAM.
  - ≥ 12.5 cm is normal.
  - Bilateral pitting oedema is SAM whatever the tape shows.
- **Supplements.**
  - Vitamin A every 182 days from 6–59 months (100,000 IU under 12 months, 200,000 IU after).
  - Albendazole every 182 days from 12 months.
  - MNP every 60 days from 6–23 months.
  - A dose is *due* on its date and *overdue* 30 days later.
- **Family planning.** `FP_RULES` maps each method to the answers that make it "avoid" or "caution". This is a simplified version of the WHO MEC wheel: for example, COC is avoided while breastfeeding under 6 months, with hypertension, or for a smoker over 35. LAM requires breastfeeding. Follow-ups use fixed offsets (such as implant at 30 and 365 days) and then the method's repeat interval (DMPA every 91 days, COC every 90).
- **Screening.**
  - TB is *presumptive* if the person has a cough over 2 weeks, has blood in sputum, or has any two other symptoms. They are referred for a sputum test at the next clinic day.
  - Malaria is *suspected* with fever: same-day RDT.
  - Any severe sign (unable to drink, fits, severe pallor or dark urine, breathing difficulty) is an emergency referral to the DHQ.
- **Stock.** The level comes from replaying movements: a count sets it, a receipt adds, dispensing subtracts.
  - AMC is the average of the last three months' dispensing.
  - An item is *low* when it is below its minimum or under one month of stock.
  - The request quantity tops the item up to max(2 × minimum, 2 × AMC).
- **Indicators.**
  - ANC4, SBA and PNC48h use the deliveries in the month.
  - FIC uses children aged 12–23 months.
  - Drop-out = (Penta-1 − Penta-3) / Penta-1, counting children past the Penta-3 window.
  - The composite score is the mean attainment against targets.

## Left for students

| Extension | Difficulty | Start here |
|---|---|---|
| Real sync backend: PouchDB → CouchDB replication or a REST API with conflict resolution, retry queue and auto-sync on `navigator.onLine` | ★★★ | `syncNow()` |
| Supervisor data from the server: replace the seeded summaries for other LHWs with real monthly summaries pulled from the sync backend | ★★★ | `lhwValues()`, `seedSummaries()` |
| SMS referral through a gateway (Telenor/Jazz API behind your backend) with delivery status on the visit or screening | ★★ | `shareSlip()` |
| GPS mapping of households and a Leaflet catchment map coloured by what is due, for route planning | ★★ | `renderHouseholdForm()` → `#fGps` |
| WHO z-score growth standards (LMS tables for weight-for-age, length-for-age, weight-for-length) with −2 / −3 SD curves | ★★ | `zScore()`, `growthStatus()`, `growthChart()` |
| Verbal autopsy (WHO 2022) for maternal and neonatal deaths, with an LHS review step | ★★ | `renderVitals()`, `deathCategory()` |
| Urdu / Sindhi voice input for complaints and notes (Web Speech API `ur-PK`, or an offline Vosk model) | ★★ | `renderAncForm()` → `#vVoice` |
| DHIS2 submission: map the illustrative codes to real dataElement UIDs and POST the `dataValueSet` | ★★ | `openDhisSheet()` → `#dhPost`, `dhisPayload()` |

Search the source for `TODO(student)` to find the exact hooks; the About page lists the same eight. Other good FYP additions are an installable PWA (manifest + service worker) and a photo of the paper register page attached to each sync batch.

## Data and safety notes

The seed village, names, readings, stock levels, campaign tallies and other LHWs' summaries are all fictional. The rules draw on several references:

- WHO ANC (2016)
- WHO/IMNCI danger-sign lists
- Pakistan's MNCH programme and the 2024 EPI card
- CMAM MUAC cut-offs
- the WHO MEC wheel
- national TB and malaria screening cards

All of them are implemented as **simplified, illustrative training values** for a decision aid used by trained LHWs. This is not a diagnostic device, and it does not replace the programme's official guidelines, the official register or clinical judgement at the facility. Growth screening uses percent-of-median, which is coarser than z-scores. The supervisor PIN only gates a screen on a shared phone and is not real security. All data stays on the device until a real sync backend is built. Do not store real patient data in this template without your institution's approval.
