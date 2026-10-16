# 05 · Thalassemia Care Companion (idea #13)

**Every transfusion, every ferritin, every chelation dose — on the family's phone instead of a paper card.** A bilingual (English / اردو) companion app for β-thalassemia patients in Pakistan: transfusion log with a next-transfusion predictor, iron and chelation tracking, a 12-week adherence calendar, carrier-screening tools for the public, and a clinician-friendly printable summary.

## Getting started

- Open `index.html` in any modern browser. No build step, no server, no dependencies beyond Google Fonts (DM Sans + Noto Nastaliq Urdu; falls back to system fonts offline).
- Browser notifications for the daily chelation reminder need the page to be served over HTTP(S) in some browsers (`npx serve .` or any static server); everything else works from `file://`.
- All data lives in `localStorage` under the `thal:` prefix. Two fictional demo patients are seeded on first open; **About → Reset demo data** restores them.
- Press the printer icon (or **Print summary** tab → Print) to produce an A4 clinical summary; use "Save as PDF" in the print dialog.

## The problem

Pakistan has one of the largest β-thalassemia burdens in the world: roughly 100,000 transfusion-dependent patients and a carrier rate of 5–8 %. Patients need a transfusion every 2–4 weeks for life plus daily iron chelation. Records are kept on paper cards that get lost, chelation adherence is poor (side effects, cost, stock-outs, simply forgetting), and pre-marital carrier screening is still uncommon despite provincial prevention laws.

## What is built

| Area | Features |
|---|---|
| Patient rail | Switch between patients, add / edit / delete (name, DOB, sex, diagnosis type, blood group, weight, target pre-Hb 9–10.5, chelator + mg/kg/day, centre, emergency contact, notes); alert dots per patient |
| Overview | KPI cards (last pre-Hb, days since last transfusion, predicted next date, latest ferritin with risk band, 30-day adherence), alert list (ferritin > 2500, pre-Hb < 7, overdue transfusion, adherence < 80 %, annual requirement > 200 mL/kg), today's dose tick, upcoming appointments, treatment plan, bilingual education |
| Transfusions | Log (date, mL or units, pre/post Hb, centre, reactions); SVG chart with pre/post points joined per episode, dashed decline connectors, shaded 9–10.5 target band, predicted point; next-transfusion predictor; annual requirement in mL/kg/year with hypersplenism flag; CSV export |
| Iron & chelation | Ferritin log + SVG trend chart with 1000 / 2500 ng/mL thresholds; chelator dose helper (deferasirox 14–28, deferiprone 75–99 in 3 doses, desferrioxamine 20–40 mg/kg/day) with range bar and "prescriber decides" disclaimer; daily reminder time with browser Notification permission (graceful fallback to toast) |
| Adherence | Daily tick with missed-dose reasons (side effects, forgot, out of stock, cost), 12-week calendar heatmap (tap to cycle), current streak, 30-day and 12-week %, reason breakdown bars, bilingual advice |
| Screening | Works without a patient: CBC → Mentzer index (MCV ÷ RBC, < 13 trait / > 13 iron deficiency) plus Shine & Lal index, HPLC/HbA2 confirmation note; Punnett square for any parent combination with animated risk bar; pre-marital screening note (Sindh 2013 / Punjab laws) |
| Appointments | Transfusion / clinic / lab / MRI with done toggle; one-click "use predicted transfusion date" |
| Request blood | Modal that builds a WhatsApp share text (initials, group, units, centre, date, contact) with copy + `wa.me` link; conceptually feeds the sibling **Khoon** donor network |
| Print summary | A4 clinical sheet: profile, last 6 transfusions, ferritin trend, adherence heatmap, predictor, active alerts |
| i18n | English UI with Urdu toggle for key labels and every patient-facing education text (Nastaliq, RTL blocks) |
| UI kit | Inline SVG icon sprite (no emoji), page-enter / staggered / count-up / line-draw motion under `prefers-reduced-motion` |

## Architecture (single file)

```
index.html
├─ <style>      tokens (lavender / teal), motion kit, rail + main layout, components, charts, heatmap, print
├─ <svg>        inline icon sprite — render with ic('name')
├─ <header>     brand, date, print, language toggle
├─ <aside #rail>   patient cards + add button (horizontal strip on mobile)
├─ <main>       #phead (patient header) · #tabs (hash routes) · #view
└─ <script>
   ├─ utils         $, store (thal: prefix), esc, dates, toast, ic, animateIn, countUp, drawPath
   ├─ i18n          L.en / L.ur, t(), T() (Nastaliq-wrapped), edu()
   ├─ domain        TYPES, GROUPS, CHELATORS (ranges), REASONS, APPT, CENTRES, UNIT_ML
   ├─ seed data     seedDemo() — two patients, ~2 years of transfusions, ferritin, 100 days of doses, appointments
   ├─ accessors     patients(), txOf(), ferOf(), apptOf(), dosesOf(), setDose(), current()
   ├─ computations  predictor(), annualReq(), ferBand(), adherenceStats(), alertsFor()
   ├─ charts        hbChart(), ferritinChart(), heatmap(), reasonBars(), doseBar() — hand-drawn SVG/CSS
   ├─ views         renderOverview / Transfusions / Iron / Adherence / Appointments / Summary / Screening / About
   ├─ modals        patientForm(), requestBloodModal()
   ├─ reminders     checkReminders() — Notification API with toast fallback, re-checked every minute
   └─ router        hash routes: #overview #transfusions #iron #adherence #appointments #summary #screening #about
```

## Domain logic notes

- **Hb decline rate** for consecutive episodes = (post-Hb of previous episode − pre-Hb of current) ÷ days between. The last three positive rates are averaged.
- **Predicted next transfusion** = last transfusion date + (last post-Hb − target pre-Hb) ÷ rate. A date in the past raises an "overdue" alert.
- **Annual requirement** = Σ volume (mL) over the last 365 days ÷ weight; 1 unit is counted as 250 mL packed red cells (`UNIT_ML`). > 200 mL/kg/year is shown as an informational hypersplenism / alloimmunisation flag.
- **Ferritin bands**: < 1000 low, 1000–2500 moderate, > 2500 high. **Adherence** counts unmarked past days as missed, but ignores today until it is ticked; the 30-day window shrinks to the days since the first record for new patients.
- **Mentzer index** = MCV ÷ RBC; < 12.5 suggests trait, > 13.5 suggests iron deficiency, in between is borderline; **Shine & Lal** = MCV² × MCH ÷ 100 (< 1530 suggests trait). Both are screening aids only — HbA2 > 3.5 % on HPLC/electrophoresis confirms β-trait.
- **Inheritance**: autosomal recessive Punnett square; carrier × carrier → 25 % major / 50 % carrier / 25 % unaffected per pregnancy.

## Left for students

| Extension | Difficulty | Start here |
|---|---|---|
| Cloud sync + doctor portal (Firebase/Supabase, per-centre patient list with alerts) | ★★ | `syncToCloud()` |
| MRI T2* liver / cardiac iron tracking charted beside ferritin, flag cardiac T2* < 20 ms | ★★ | `renderIron()` → `mriSection()` |
| WHO growth charts (height/weight-for-age percentiles) | ★★ | `growthChart()` hook in `renderOverview()` |
| Push notification backend (FCM / OneSignal / SMS) so reminders fire when the page is closed | ★★★ | `checkReminders()` |
| Khoon donor-network API: post the request and show matched donors inline | ★★ | `requestBloodViaKhoon()` in `requestBloodModal()` |
| More languages (Sindhi, Pashto, Punjabi, Balochi) with clinician-reviewed education text | ★ | `L` dictionary |
| EMR export as HL7 FHIR bundles | ★★★ | `exportToEMR()` |

Search the source for `TODO(student)` to find the exact hooks.

## Data and safety notes

Seed patients, centres and phone numbers are fictional or illustrative. Dose ranges, thresholds and indices are textbook values for demonstration; the app never prescribes — every clinical screen carries an "informational, prescriber decides" disclaimer, and the printed summary tells the clinician to verify against centre records. Data never leaves the device until a student builds the sync layer.
