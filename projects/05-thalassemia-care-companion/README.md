# 05 · Thalassemia Care Companion (idea #13)

**Scale:** ~4,980 lines in one `index.html` (≈4,500 lines of vanilla JS) · 16 screens + notifications inbox, guided tour and shortcuts · 435 seed records across 2 demo patients (plus 8 support programmes, 12 bilingual articles, 10 quiz questions, 8 reaction protocols, 9 lab tests with reference ranges) · 592 dictionary keys in English and Urdu.

**Every transfusion, every ferritin, every chelation dose — on the family's phone instead of a paper card.** A bilingual (English / اردو) companion app for β-thalassemia patients in Pakistan. It covers the transfusion log with a next-transfusion predictor, reaction log with protocol guidance, ferritin and MRI T2*/LIC iron tracking, a lab panel with a monitoring schedule, growth against approximate WHO medians, chelation adherence, a medicine inventory with a refill planner, treatment costs and support programmes, caregiver notes, carrier screening, an education library with a quiz and a clinician-friendly printable summary.

## Getting started

- Open `index.html` in any modern browser. No build step, no server, no dependencies beyond Google Fonts (DM Sans + Noto Nastaliq Urdu; falls back to system fonts offline).
- Browser notifications for the daily chelation reminder need the page to be served over HTTP(S) in some browsers (`npx serve .` or any static server). Everything else works from `file://`.
- All data lives in `localStorage` under the `thal:` prefix. Two fictional demo patients are seeded on first open. Data saved by v1 of this template keeps working: on load, the demo records for the new modules are added and nothing else changes.
- **Settings** has JSON backup export/import, demo reset, "clear all", text size, an animation switch and the guided tour. **About → Reset demo data** also restores the demo.
- Press the printer icon (or the **Print summary** tab, then Print) for an A4 clinical summary; choose "Save as PDF" in the print dialog.
- Keyboard: `1`–`9` jump to tabs, `n` opens notifications, `l` switches language, `?` shows the shortcuts sheet, `Esc` closes dialogs.

## The problem

Pakistan has one of the largest β-thalassemia burdens in the world: roughly 100,000 transfusion-dependent patients and a carrier rate of 5–8 %. Patients need a transfusion every 2–4 weeks for life plus daily iron chelation, regular liver/kidney/endocrine tests, yearly MRI iron scans and growth monitoring. Records are kept on paper cards that get lost. Chelation adherence is poor because of side effects, cost, stock-outs and simple forgetting. Families also carry heavy out-of-pocket costs without knowing which programmes can help, and pre-marital carrier screening is still uncommon despite provincial prevention laws.

## What is built

| Area | Features |
|---|---|
| Patient rail | Switch between patients, add / edit / delete (name, DOB, sex, diagnosis type, blood group, weight, target pre-Hb 9–10.5, chelator + mg/kg/day, centre, emergency contact, notes). Alert dots per patient. Deleting cascades to every per-patient collection |
| Overview | KPI cards (last pre-Hb, days since last transfusion, predicted next date, latest ferritin band, 30-day adherence); alerts from every module; today's dose tick; upcoming appointments; "due soon" panel (refills + overdue labs); treatment plan with latest growth and cardiac T2* stage |
| Transfusions | Log (date, mL or units, pre/post Hb, centre, notes); SVG Hb chart with target band, decline connectors and predicted point; next-transfusion predictor; annual requirement in mL/kg/year with hypersplenism flag; CSV export |
| **Reactions** (new) | Reaction log linked to a logged transfusion (type, severity, signs, action, outcome); 12-month count and reactions-per-transfusion rate; recurrent febrile/allergic warning; bars by type; bilingual protocol cards for 8 reaction types (typical signs, first steps, prevention), labelled illustrative |
| Iron & MRI | Ferritin log + trend chart (1000 / 2500 thresholds); chelator dose helper; daily reminder. **New:** MRI T2* tracker (cardiac T2*, liver T2*, LIC) with iron-overload staging pills, LIC estimated from liver T2* when not reported, twin trend charts with threshold lines, scan log |
| **Labs** (new) | 9 tests (ALT, AST, bilirubin, creatinine, calcium, vitamin D, TSH, zinc, fasting glucose) grouped liver / kidney / endocrine / other; approximate reference ranges (age-adjusted creatinine), low/high flags, sparkline per test, due status from typical intervals, tap-a-row trend chart with the reference band, full history |
| **Growth** (new) | Height/weight log; chart against approximate WHO median and 3rd-percentile curves (height/weight toggle); % of median, BMI, height velocity; flags for short stature, low weight and slow growth; optional sync of the patient's weight |
| Adherence | Daily tick with missed-dose reasons, 12-week calendar heatmap (tap to cycle), streak, 30-day and 12-week %, reason bars |
| **Medicines** (new) | Inventory (name, type, units/day, counted stock + date, pack size, price, free/pharmacy source); estimated units left, days of supply, run-out and refill-by dates; stock bar; "+1 pack" refill; monthly need and cost; 90-day refill planner |
| **Costs & support** (new) | Expense log (7 categories, covered-by-programme flag); this month / 6-month / covered / projected yearly KPIs; stacked monthly bars; category bars. Editable support-programme directory (Pakistan Bait-ul-Mal, PTPP, SBTA, Sehat Card, Fatimid, Sundas, TFP, KITCC) with bilingual offers/how-to-apply, province filter and per-patient status (not applied / applied / enrolled) |
| Appointments | Transfusion / clinic / lab / MRI with done toggle; one-click "use predicted transfusion date" |
| **Notes** (new) | Caregiver notes with tags (symptom, question, side effect, school, other), pinning, search, tag filter and an auto-collected "questions for the next visit" list |
| Print summary | A4 sheet: profile, last 6 transfusions, ferritin trend, adherence heatmap, growth, MRI stages, latest labs with flags, medicines, 12-month reactions, 6-month costs, pinned notes, predictor, alerts |
| Screening | Works without a patient: CBC → Mentzer and Shine & Lal indices; Punnett square with animated risk bar; pre-marital screening note |
| **Learn** (new) | 12 bilingual articles in 8 categories with search and category chips, article reader with key points; 10-question quiz with explanations, progress bar, best score and attempts saved |
| **Notifications inbox** (new) | Bell with unread badge; items derived from every patient's alerts, appointments in the next 2 days and refills due within 14 days; mark read / mark all; jump to the relevant tab |
| **Settings** (new) | Language, text size, animations switch, guided tour, keyboard-shortcut sheet, storage used, JSON backup export/import (validated), demo reset, clear all |
| **Guided tour** (new) | 5-step spotlight tour (rail, tabs, KPIs, inbox, language), started from Settings or About |
| i18n | Every user-facing string has an English and Urdu entry (592 keys); Nastaliq + RTL blocks for education, protocols, articles and quiz |
| UI kit | Inline SVG sprite (no emoji), page-enter / staggered / count-up / line-draw / bar-fill motion; respects `prefers-reduced-motion` and the in-app animation switch |

## Module map

| Module | Main functions | Storage keys (`thal:` prefix) |
|---|---|---|
| Patients | `renderRail`, `renderHead`, `patientForm`, `savePatient`, `deletePatient` | `patients`, `current` |
| Overview | `renderOverview`, `overviewKpis`, `duePanel`, `planPanel`, `renderTodayDose`, `alertsFor` | reads all |
| Transfusions | `renderTransfusions`, `predictor`, `annualReq`, `hbChart`, `exportCSV` | `tx` |
| Reactions | `renderReactions`, `rxForm`, `rxTable`, `rxProtocolCard`, `reactionStats` | `reactions` |
| Iron & MRI | `renderIron`, `ferritinPanel`, `mriPanel`, `mriStage`, `licFromT2`, `seriesChart`, `doseHelperPanel` | `fer`, `mri`, `rem` |
| Labs | `renderLabs`, `labSummary`, `labRange`, `labFlag`, `labLatestTable`, `sparkline` | `labs` |
| Growth | `renderGrowth`, `growthMedian`, `growthAssess`, `heightVelocity`, `growthFlags`, `growthChart` | `growth` |
| Adherence | `renderAdherence`, `adherenceStats`, `heatmap`, `reasonBars` | `doses` |
| Medicines | `renderMeds`, `medStatus`, `refillPlan`, `medForm`, `medRow` | `meds` |
| Costs & support | `renderCosts`, `costStats`, `monthBars`, `niceStep`, `programmeCard`, `programmeForm` | `costs`, `programmes`, `progStatus` |
| Appointments | `renderAppointments`, `apptRowFull` | `appts` |
| Notes | `renderNotes`, `noteCard`, `bindNotes` | `notes` |
| Print summary | `renderSummary`, `summaryTx`, `summaryLabs`, `summaryGrowthMri`, `summaryMedsRx` | reads all |
| Screening | `renderScreening`, `mentzer`, `punnett` | `screen` |
| Learn | `renderLearn`, `libGrid`, `libArticle`, `quizPanel`, `bindQuiz` | `quiz` |
| Notifications | `buildInbox`, `alertTab`, `inboxModal`, `markInboxRead`, `refreshInboxBadge` | `inboxRead` |
| Settings & backup | `renderSettings`, `applySettings`, `storageStats`, `exportBackup`, `importBackup` | `settings`, `lang` |
| Tour & shortcuts | `startTour`, `showTourStep`, `positionTour`, `shortcutsModal`, keydown handler | `tourDone` |
| Reminders | `checkReminders` | `rem`, `remFired` |
| Seed | `seedDemo`, `seedEpisodes`, `seedDoseLog`, `seedExtras`, `seedGrowthIron`, `seedLabsMeds`, `seedCareLog` | `seeded` (version 2) |

## Architecture (single file)

```
index.html
├─ <style>      tokens (lavender / teal), motion kit (+ data-motion / data-text overrides), rail + main layout,
│               components, tables, charts, heatmap, library / quiz / notes / medicine cards, modal, toasts, tour, print
├─ <svg>        inline icon sprite (only used symbols) — render with ic('name')
├─ <header>     brand, date, notifications bell (unread badge), print, language toggle
├─ <aside #rail>   patient cards + add button (horizontal strip on mobile)
├─ <main>       #phead (patient header) · #tabs (hash routes, wrap on desktop / scroll on mobile) · #view
└─ <script>
   ├─ utils         $, store (thal: prefix, keys/remove), esc, dates, numbers, toast, ic, animateIn, countUp, drawPath, downloadFile
   ├─ i18n          L.en / L.ur (592 keys each), t(), T() (Nastaliq-wrapped), nm(), urCls(), edu()
   ├─ domain        TYPES, GROUPS, CHELATORS, REASONS, APPT, CENTRES, UNIT_ML, TARGET_BAND
   ├─ reference     GROWTH_REF (approx. WHO medians), MRI_STAGES, LAB_TESTS, MED_TYPES, RX_TYPES (protocols),
   │                COST_CATS, PROGRAMMES_SEED, NOTE_TAGS, EDU_LIB (12 articles), QUIZ (10), TOUR_STEPS
   ├─ seed data     seedDemo() + seedExtras() — deterministic, relative to today; v1 → v2 upgrade path
   ├─ accessors     patients(), listOf(), setList(), removeById(), deletePatient(), txOf() … notesOf()
   ├─ computations  predictor, annualReq, adherenceStats, growth*, mriStage, licFromT2, labSummary, medStatus,
   │                refillPlan, reactionStats, costStats, alertsFor, buildInbox
   ├─ charts        hbChart, ferritinChart, seriesChart, growthChart, monthBars, sparkline, heatmap, hBars, doseBar
   ├─ views         overview · transfusions · reactions · iron · labs · growth · adherence · meds · costs ·
   │                appointments · notes · summary · screening · learn · settings · about
   ├─ modals        patientForm, requestBloodModal, medForm, programmeForm, inboxModal, shortcutsModal
   ├─ tour          startTour / showTourStep / positionTour / endTour (spotlight)
   ├─ reminders     checkReminders() — Notification API with toast fallback, re-checked every minute
   └─ router        hash routes for all 16 screens + keyboard shortcuts
```

## Domain logic notes

- **Hb decline rate** for consecutive episodes = (post-Hb of previous episode − pre-Hb of current) ÷ days between. The last three positive rates are averaged. **Predicted next transfusion** = last date + (last post-Hb − target pre-Hb) ÷ rate.
- **Annual requirement** = Σ mL in the last 365 days ÷ weight (1 unit = 250 mL, `UNIT_ML`). Above 200 mL/kg/year raises an informational hypersplenism / alloimmunisation flag.
- **Ferritin bands**: < 1000 low, 1000–2500 moderate, > 2500 high. **Adherence** counts unmarked past days as missed but ignores today until it is ticked.
- **MRI staging (approximate)**: cardiac T2* > 20 ms normal, 10–20 mild–moderate, < 10 severe. LIC < 3 mg/g acceptable, 3–7 mild, 7–15 moderate, > 15 severe. When only liver T2* is entered, LIC ≈ 0.0254 × (1000 ÷ T2*) + 0.202 (an R2*-based calibration).
- **Growth**: medians for ages 1–18 are interpolated linearly from a rounded WHO-derived table. The 3rd percentile is approximated as 91 % (height) / 76 % (weight) of the median. Height velocity uses the latest measurement and one at least 150 days earlier; under 4 cm/year at age 4–10 is flagged.
- **Labs**: a result outside the approximate range is flagged low/high (creatinine uses a lower paediatric range under 12 years). Each test has a typical interval: it is "due soon" after 80 % of the interval and "overdue" after it. ALT above twice the upper limit and high creatinine raise alerts.
- **Medicines**: units left = counted stock − units/day × days since the count. Run-out = today + floor(left ÷ units/day), refill-by = run-out − 5 days. Levels: out, urgent (≤ 5 days), soon (≤ 14), stocked.
- **Costs**: the projected yearly out-of-pocket figure is 2 × the last 6 months' out-of-pocket total.
- **Inbox**: built on demand from alerts, near appointments and upcoming refills. Item ids are stable per day so read-state survives re-renders, and read marks older than a week are pruned.
- **Screening**: Mentzer = MCV ÷ RBC (< 12.5 trait, > 13.5 iron deficiency); Shine & Lal = MCV² × MCH ÷ 100 (< 1530 suggests trait). HbA2 > 3.5 % on HPLC confirms β-trait. Inheritance uses an autosomal-recessive Punnett square.

## Left for students

| Extension | Difficulty | Start here |
|---|---|---|
| Cloud sync + doctor portal (Firebase/Supabase, per-centre patient list with alerts, conflict handling) | ★★ | `syncToCloud()` |
| Push notification backend (FCM / OneSignal / SMS) so reminders fire when the page is closed | ★★★ | `scheduleReminder()` in `checkReminders()` |
| Khoon donor-network API: post the request and show matched donors inline | ★★ | `requestBloodViaKhoon()` in `requestBloodModal()` |
| More languages (Sindhi, Pashto, Punjabi, Balochi) with clinician-reviewed education text | ★ | `L` dictionary |
| EMR export as HL7 FHIR bundles (Patient, Procedure, Observation for Hb / ferritin / labs) | ★★★ | `exportToEMR()` |
| Exact WHO z-scores from LMS tables instead of approximate medians | ★★ | `growthZScore()` → `growthAssess()` |
| Lab-report OCR (Tesseract.js) to pre-fill the Labs form after confirmation | ★★★ | `importLabReport()` |

Search the source for `TODO(student)` to find the exact hooks; the same list appears in the About screen.

## Data and safety notes

Seed patients, phone numbers and expenses are fictional. Programme entries name real organisations, but their offers, contacts and eligibility are illustrative placeholders that must be verified and can be edited in the app. Dose ranges, lab reference ranges, MRI staging cut-offs, growth medians and reaction protocols are approximate textbook values for demonstration. Every clinical screen says so and tells users to consult their clinician, and the printed summary asks the clinician to verify against centre records. The app never prescribes. Data never leaves the device until a student builds the sync layer.
