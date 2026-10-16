# 06 · Dawai — Generic Medicine Finder (idea #15)

**Scale:** 4,878 lines in one HTML file · 18 modules · seed data: 69 molecules, 220 brands, 51 drug-information sheets, 37 interaction rules, 29 price observations, 14 GTIN barcodes, 6 quiz questions (all illustrative).

**Same molecule. A fraction of the price.** A search-first, bilingual (English / اردو) finder that maps any brand, molecule or everyday problem ("bukhar", "blood pressure") to the molecule behind it, lists every interchangeable brand sorted by price per tablet, and then helps a household manage what it actually takes: a medicine cabinet with family profiles, prescriptions turned into course calendars, antibiotic course tracking, expiry and refill alerts, interaction warnings and dose reminders.

## Getting started

- Open `index.html` in any modern browser. No build step, no server, no dependencies beyond Google Fonts (Public Sans + Noto Nastaliq Urdu; falls back to system fonts offline).
- Browser notifications and the camera barcode scanner need a secure context in most browsers: serve the folder over HTTP(S) (`npx serve .`) or `localhost`. The camera scanner uses the `BarcodeDetector` API (Chrome on Android, macOS and ChromeOS); everywhere else the scanner dialog falls back to typing the barcode digits. Everything else works from `file://`.
- All state lives in `localStorage` under the `dawai:` prefix: `cabinet`, `extras`, `taken`, `pricelog`, `recent`, `checks`, `lang` (unchanged from v1, so old data still loads) plus `family`, `rx`, `abx`, `abxquiz`, `gtin`, `drap`, `settings`, `inbox`, `tour`, `demo`.
- **Settings → Demo data → Load demo** (or "Load demo household" on an empty cabinet) seeds a three-person household with profiles, stock and expiry data, a child's prescription that is already in reminders, three antibiotic courses (one active, one completed, one abandoned) and six days of tick-off history, so every screen has content.
- **Settings → Export everything** downloads a JSON backup; **Import backup** restores it; **Reset app** removes every `dawai:` key.
- Keyboard: `/` focuses search, `?` opens the shortcuts sheet, `g` then `s/m/p/r/f/t` jumps to a page, `Alt+L` toggles the language, `Esc` closes dialogs.

## The problem

Branded medicines in Pakistan often cost 2–10× an interchangeable generic. Patients rarely know the molecule behind a brand, which brands are interchangeable at the same strength and form, or how to tell a counterfeit from the real pack, so they keep paying for a name. Households also juggle several people's medicines, unfinished antibiotic courses and expired packs with no simple tool to see allergies, interactions and costs in one place.

## What is built

| Area | Features |
|---|---|
| Search-first landing | Large centred search with instant autocomplete over brands, molecules, conditions and therapeutic classes (English, Roman Urdu and Urdu script), keyboard navigation, recent searches, popular-molecule chips, dataset KPIs, browse-by-class grid, first-visit tour banner, "illustrative data" ribbon with an imported-data badge |
| Therapeutic classes | Nine classes (pain, infections, diabetes, heart, gut, allergy and chest, vitamins, nerves and mood, other) with a card per molecule showing brand count, cheapest unit price, AWaRe and NTI flags |
| Molecule page | Class, used-for and counselling point (en + ur), antibiotic / AWaRe / narrow-therapeutic-index flags, strength and form filters, brand table sorted by price per unit with the cheapest per strength-form group highlighted, savings % against a selectable reference brand, compare checkboxes (max 3) → side-by-side drawer, "Add" per brand, printable medicine card (now with missed-dose and storage advice), quick facts rail |
| Drug information | Six-section accordion per molecule — common side effects, contraindications, pregnancy, breastfeeding, storage, missed-dose advice — in English and Urdu, from 51 class-level sheets |
| Street prices | Record the pack price you paid at a named pharmacy/city; table shows average observed price and delta vs MRP; a bar chart per brand compares each pharmacy's average with the printed MRP line and summarises by city |
| My medicines · Cabinet | Items grouped per person with dose per day → monthly cost, cheapest equivalent and saving, one-click switch (confirmation for NTI drugs), course end dates, expiry flags, KPI cards with total cost and savings ring, "also taking" chips, 37-rule interaction check and per-person profile alerts, all bilingual |
| Family profiles | Name, cabinet person, age band, sex, pregnancy, breastfeeding, allergy families (9) and conditions (11), notes; alerts appear while adding a medicine, in the cabinet, on each profile card and in the inbox; "check a medicine" tool for any molecule |
| Stock & expiry | Expiry month, units left and opened date per pack; status pills (expired, expiring within 90 days, refill due, liquid opened over 4 weeks), four KPIs, sorted by urgency, disposal and liquid-storage guidance |
| Prescriptions | Digitiser form (patient, doctor, date, diagnosis) with brand search, units per dose, frequency, days and start date per line; live units/packs/cost and cost with cheapest equivalents; month course calendar with one colour per line; "Send to reminders" adds every line to the cabinet with dose times and end date; printable prescription sheet |
| Antibiotic stewardship | Course tracker fed by reminder tick-offs (doses taken vs expected, due today, late, completed, incomplete, stopped early), completion KPIs and streak, leftover-dose count, WHO AWaRe categories with the dataset's antibiotic list, five resistance facts and a six-question quiz with best score |
| Reminders | Day-aware dose checklist (only items active that day), tick-off, day streak, 7-day adherence line chart, per-item dose times, browser Notification permission with graceful fallback |
| Safety | 8-point counterfeit checklist with live verdict, suspicious-medicine report generator (copy / WhatsApp / email), storage tips and antibiotic message, links into stock and antibiotic tabs |
| Barcode scanner | Camera scan with `BarcodeDetector` where available, manual digits otherwise; illustrative GTIN table plus "link this code to a brand" learning, stored on device |
| DRAP-style CSV import | Paste or choose a CSV; RFC-4180 parser (quotes, escaped quotes, CRLF, BOM), header aliases, per-row validation (molecule, form, numbers, duplicates), preview table with rejected rows, apply / remove, "data as of" badge, sample CSV download; imported brands join search and molecule tables |
| Settings, inbox, tour | Language, text size, reduced motion, demo data load/clear, JSON export/import, reset, storage used, shortcuts; notifications inbox (expiry, refill, antibiotic end/late, high interactions, profile alerts) with unread badge and deep links; five-step onboarding tour |
| About | Problem, savings / interaction / profile methodology, disclaimers, data notes, in-app module map and the student TODO panel |
| i18n & motion | English/Urdu toggle (persisted) covering every new string, Nastaliq for Urdu, bilingual blocks for patient-facing text; SVG icon sprite (no emoji), page-enter and staggered animations, animated counters, bars, rings and chart draw-in, all disabled under `prefers-reduced-motion` or the Settings toggle |

## Module map

| Module | Main functions | Storage keys (`dawai:`) |
|---|---|---|
| Search & autocomplete | `buildIndex`, `suggest`, `attachSearch`, `pushRecent`, `openSuggestion` | `recent` |
| Landing & class browse | `renderLanding`, `biggestGap`, `renderWelcome`, `renderClass` | `tour` |
| Molecule page | `renderMolecule`, `brandTable`, `brandRow`, `factsCard`, `openCompare`, `openCard` | — |
| Drug information | `INFO_LIST`, `infoOf`, `infoCard` | — |
| Street prices | `allLog`, `seenStats`, `streetChart`, `streetCard`, `saveObservation` | `pricelog` |
| Cabinet | `renderCabinet`, `cabinetTab`, `personCard`, `itemRow`, `addItem`, `openAdd`, `switchItem` | `cabinet`, `extras` |
| Interaction engine | `GROUPS`, `INTERACTIONS`, `checkInteractions` | `extras` |
| Family profiles | `profileAlerts`, `personAlerts`, `familyTab`, `openProfileForm`, `saveProfile` | `family` |
| Stock & expiry | `expiryStatus`, `daysOfStock`, `stockIssues`, `stockTab`, `wireStockTab` | `cabinet` (expiry, qty, opened) |
| Prescriptions | `openRxForm`, `attachRxFind`, `rxCalc`, `rxTotals`, `rxCalendar`, `sendRx`, `printRx` | `rx` |
| Antibiotic stewardship | `addCourse`, `courseStats`, `completionStreak`, `abxTab`, `openCourseForm`, `stopCourse`, `wireQuiz` | `abx`, `abxquiz` |
| Reminders | `scheduledOn`, `adherence`, `streak`, `weekChart`, `scheduleNotifs` | `taken` |
| Safety | `renderSafety`, `wireSafety` | `checks` |
| Barcode scanner | `scanBarcode`, `startCamera`, `lookupGtin`, `scanResult` | `gtin` |
| CSV import | `parseCsv`, `csvColumns`, `importDrapMrp`, `applyCsv`, `rebuildDataset` | `drap`, `gtin` |
| Settings & data | `renderSettings`, `applySettings`, `exportAll`, `importAll`, `resetAll`, `loadDemo`, `clearDemo` | `settings`, `lang`, `demo` |
| Notifications inbox | `notify`, `scanAlerts`, `openInbox`, `renderInboxBadge` | `inbox` |
| Tour & shortcuts | `openTour`, `shortcutsSheet`, global `keydown` handler | `tour` |

## Architecture (single file)

```
index.html
├─ <style>      tokens (mint / coral / violet on white), icon + motion kit (+ data-motion / data-text settings),
│               search box, class grid, responsive brand table (cards ≤760px), drug-info accordion, street-price bars,
│               cabinet, family cards, stock rows, prescription cards + month calendar, antibiotic courses + quiz,
│               reminders, safety, settings / inbox / tour / scanner, drawer / modal / toasts, print
├─ <svg>        inline icon sprite (65 Lucide-style symbols used by ic('name'))
├─ <header>     brand, six-item nav, compact search, inbox bell with unread badge, settings, language toggle
├─ <main>       one <section data-page> per route (search, molecule, class, cabinet, rx, reminders, safety, settings, about)
└─ <script>
   ├─ utils            $, store (dawai: prefix), esc, toast, motion helpers, money + date helpers, download, copyText, fill
   ├─ i18n             L.en / L.ur (identical key sets), t(), nm(), bi()
   ├─ seed data        MOLECULES (69, class + info key + groups), BRAND_ROWS (220) → brandFromRow, GTIN_SEED, SEED_LOG
   ├─ drug info        INFO_LIST (51 sheets × 6 sections, en/ur)
   ├─ interactions     EXTRAS, INTERACTIONS (37 rules: pairs + duplicate therapy), checkInteractions()
   ├─ profile rules    ALLERGY_MAP, COND_MAP, pregnancy / breastfeeding / child / senior lists
   ├─ stewardship      AWARE_TEXT, ABX_FACTS, ABX_QUIZ; safety CHECKS, TIPS, ABX_MSG
   ├─ search           buildIndex, rebuildDataset, suggest, attachSearch; barcode scanner
   ├─ landing / class  renderLanding, renderClass
   ├─ molecule page    brand table, drug info, street prices, compare drawer, modal helpers, medicine card
   ├─ cabinet          addItem, profile engine, openAdd, demo household, cabinet / family / stock tabs
   ├─ prescriptions    rxCalc, form, calendar, send to reminders, print sheet; antibiotic courses + quiz
   ├─ reminders        scheduledOn, adherence, streak, weekChart, scheduleNotifs
   ├─ safety           checklist, report generator
   ├─ CSV import       parseCsv, importDrapMrp, sampleCsv
   ├─ settings         preferences, export / import / reset, CSV UI
   ├─ inbox / tour     notify, scanAlerts, openInbox, openTour
   ├─ about            TODOS, MODULES
   └─ router           hash routes: #search · #m/<molecule> · #c/<class> · #cabinet[/family|/stock] · #rx[/abx]
                       · #reminders · #safety · #settings · #about; keyboard shortcuts; init
```

## Domain logic notes

- **Price per unit** = pack MRP ÷ units in the pack (tablet, capsule, 5 ml dose, sachet, puff or ml). **Savings %** = (reference unit price − brand unit price) ÷ reference unit price, only between brands of the same molecule, strength and form; other rows show "—". Reference = the brand you searched for, else the originator, else the priciest brand.
- **Cabinet**: monthly cost = unit price × doses/day × 30 (items whose course ended cost nothing); potential saving = monthly cost − cost of the cheapest equivalent. NTI molecules (levothyroxine, warfarin, carbamazepine, valproate) need a confirmation before switching.
- **Interaction engine**: each item contributes its molecule id and pharmacological groups (NSAID, PPI, STATIN, MACROLIDE, FQ, SU, RAAS, ARB, ANTICOAG, SSRI, QT, SEDATING …); "also taking" chips contribute tokens (iron, calcium, antacid, potassium, alcohol, contrast). A pair rule fires when side A and side B are both present for one person *and* come from two different medicines (so a single QT-prolonging drug never warns against itself); `dup` rules fire on two distinct molecules from one group; paracetamol is summed as mg per unit × doses/day against the 4 g limit.
- **Profile alerts**: an allergy family maps to molecules at high (same family) or moderate (cross-reaction, e.g. penicillin ↔ cephalosporin, sulfa ↔ loop/thiazide/sulfonylurea) severity; each condition has avoid and caution lists; pregnancy, breastfeeding, child and senior age bands have their own lists. Results are sorted high → moderate.
- **Expiry**: a pack labelled `YYYY-MM` is usable to the last day of that month; ≤ 90 days left = expiring soon, ≤ 30 days raises an inbox alert. Days of stock = floor(units left ÷ doses per day); ≤ 7 = refill due. Opened liquids older than 28 days are flagged.
- **Prescriptions**: total units = units per dose × doses per day × days; packs = ceil(units ÷ pack size); cost = packs × MRP, repeated with the cheapest same-strength equivalent. Sending a prescription creates cabinet items with standard dose times (1×: 08:00, 2×: 08:00/20:00, 3×: 08:00/14:00/20:00, 4×: 06/12/18/22) and an `until` date.
- **Antibiotic courses**: expected doses = days × doses per day; taken = ticks in `taken` for the course's cabinet item between start and end. While running, "late" means more than one day's doses behind; after the end date ≥ 90 % taken = completed, otherwise incomplete; "stop early" records the stop and ends the cabinet item. The streak counts consecutive completed courses, newest first.
- **Adherence**: `taken[YYYY-MM-DD]` holds `itemId@HH:MM` keys. A day's schedule only includes items added on or before it whose course had not ended, so new prescriptions do not count as missed doses in the past. A day counts toward the streak when every scheduled dose was ticked.
- **CSV import**: a character-level parser handles quoted fields, doubled quotes, CRLF and a UTF-8 BOM; headers are matched through aliases (e.g. `generic`, `salt` → molecule; `pack size` → units); rows are rejected for unknown molecules or forms, non-positive numbers or duplicate brand ids. Accepted rows are stored as `drap.rows` and merged into `BRANDS` at start-up by `rebuildDataset()`.

## Left for students

| Extension | Difficulty | Start here |
|---|---|---|
| Import the full DRAP MRP list including new molecules (molecule_ur, class, uses columns; INFO defaults) | ★★ | `importDrapMrp()` |
| Barcode decoder fallback (ZXing-js) for browsers without `BarcodeDetector`, and real GTINs from surveyed cartons | ★★ | `scanBarcode()` |
| Pharmacy locator map (Leaflet + Geolocation) tied to price logs | ★★ | `findNearbyPharmacies()` |
| Crowd-sourced price backend with login, moderation and MAD outlier rejection | ★★★ | `syncPriceLog()` and `seenStats()` |
| Prescription OCR (Tesseract.js, Urdu + English) with fuzzy matching into the prescription form | ★★★ | `ocrPrescription()` |
| Bioequivalence / quality rating data per brand | ★★ | `qualityRating()` and the Quick facts card |
| Sindhi and Pashto translations, four-way language menu, full RTL layout | ★ | `L.sd` / `L.ps` next to `L.ur` |
| Background dose notifications via service worker / Push API, installable PWA | ★★ | `scheduleNotifs()` |

Search the source for `TODO(student)` to find each stub.

## Data and safety notes

- The dataset is **illustrative**: 69 molecules, 220 brands, 51 drug-information sheets, 37 interaction rules and 14 GTIN codes with plausible 2026 pack MRPs. Manufacturers are real Pakistani and multinational companies, but every price, pack size, barcode and even some brand–manufacturer pairings are seed values for demonstration and must be verified against the DRAP MRP list and the pack in hand before any real use. WHO AWaRe categories follow the published list for the molecules included.
- Dawai is not medical advice. Counselling points, drug-information sheets, profile rules and interaction rules are simplified teaching content shared per class and covering common situations only; they are not a complete clinical screen. Never switch brands of narrow-therapeutic-index medicines without a doctor and a follow-up test, and always finish antibiotic courses.
- Price observations, family profiles, prescriptions and the cabinet never leave the device; a shared backend is deliberately left as a student extension so that moderation and consent are designed in from the start.

## Evaluation ideas for the FYP report

Validate the seed prices against a scraped DRAP list and report the error; run usability sessions with 5–10 patients in Urdu mode at a pharmacy counter; measure the share of cabinets where the cheapest equivalent would save more than Rs 500 per month; have a pharmacist review the interaction and profile rule sets for false positives and misses; compare antibiotic-course completion rates for users who sent a prescription to reminders against those who did not.
