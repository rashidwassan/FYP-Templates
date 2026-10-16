# 06 · Dawai — Generic Medicine Finder (idea #15)

**Same molecule. A fraction of the price.** A search-first, bilingual (English / اردو) finder that maps any brand, molecule or everyday problem ("bukhar", "blood pressure") to the molecule behind it, lists every interchangeable brand sorted by price per tablet, and adds a household medicine cabinet with monthly cost, savings, interaction warnings and dose reminders.

## Getting started

- Open `index.html` in any modern browser. No build step, no server, no dependencies beyond Google Fonts (Public Sans + Noto Nastaliq Urdu; falls back to system fonts offline).
- Browser notifications for dose reminders need the page to be served over HTTP(S) in some browsers (`npx serve .` or any static server). Everything else works from `file://`.
- All state lives in `localStorage` under the `dawai:` prefix (`cabinet`, `extras`, `taken`, `pricelog`, `recent`, `checks`, `lang`). To reset, clear site data or run `Object.keys(localStorage).filter(k => k.startsWith('dawai:')).forEach(k => localStorage.removeItem(k))` in the console.
- "Load demo cabinet" on the My medicines page seeds a three-person household with six days of tick-off history so every screen has content.

## The problem

Branded medicines in Pakistan often cost 2–10× an interchangeable generic. Patients rarely know the molecule behind a brand, which brands are interchangeable at the same strength and form, or how to tell a counterfeit from the real pack, so they keep paying for a name. Pharmacists are the only people who routinely see this information, and they are not always asked.

## What is built

| Area | Features |
|---|---|
| Search-first landing | Large centred search box with instant autocomplete over brands, molecules and conditions (English, Roman Urdu and Urdu script, e.g. "bukhar", "بخار"), keyboard navigation, recent searches, popular-molecule chips, dataset stats, "illustrative data" ribbon, barcode-scan stub |
| Molecule page | Class, used-for and counselling point (en + ur), antibiotic / narrow-therapeutic-index flags, strength and form filters, brand table sorted by price per unit with the cheapest per strength-form group highlighted, savings % against a selectable reference brand (searched brand → originator → priciest), compare checkboxes (max 3) → side-by-side drawer, "Add" per brand, printable medicine card, quick facts rail |
| Local price log | Record the pack price you paid at a named pharmacy/city; the table shows average observed price, count and delta vs MRP (nine seeded observations + yours) |
| My medicines | Items grouped per person with dose per day → monthly cost, cheapest equivalent and monthly saving per item, one-click switch (confirmation for NTI drugs), KPI cards with total cost, potential savings ring + bar, "also taking" chips (iron, calcium, antacid, potassium, alcohol, contrast scan) and a 16-rule interaction check with severity and plain-language advice in English and Urdu |
| Reminders | Dose times per item, today's checklist with tick-off, day streak, 7-day adherence line chart, browser Notification permission with graceful fallback (fires while the tab is open) |
| Safety | 8-point counterfeit checklist with live verdict, "report a suspicious medicine" text generator with copy / WhatsApp / email, storage tips and an antibiotic-course message, all bilingual |
| About | Problem, savings and interaction methodology, disclaimers, data notes and the student TODO panel |
| i18n & motion | English/Urdu toggle (persisted), Nastaliq for Urdu, RTL blocks for patient-facing text; SVG icon sprite (no emoji), page-enter and staggered animations, animated counters, bars, ring and chart draw-in, all disabled under `prefers-reduced-motion` |

## Architecture (single file)

```
index.html
├─ <style>      tokens (mint / coral on white), icon + motion kit, search box, results grid,
│               responsive brand table (cards ≤760px), cabinet, reminders, safety, drawer/modal, print
├─ <svg>        inline icon sprite (Lucide-style symbols used by ic('name'))
├─ <header>     brand, text nav with underline indicator, compact search (hidden on landing), language toggle
├─ <main>       one <section data-page> per route; molecule page rendered into #molPage
└─ <script>
   ├─ utils          $, store (dawai: prefix), esc, toast, formatters, ic(), animateIn, countUp, drawPath
   ├─ i18n           L.en / L.ur, t(), nm(), bi() (bilingual block)
   ├─ seed data      MOLECULES (26, with en/ur uses + counselling), BRANDS (88 tuples → unit price), SEED_LOG
   ├─ interactions   GROUPS, EXTRAS, INTERACTIONS (16 rules), checkInteractions(items, extras) per person
   ├─ search         INDEX, suggest(q), attachSearch(input, list), recent searches
   ├─ molecule page  renderMolecule(id), compare drawer, openAdd(), openCard() (print)
   ├─ cabinet        renderCabinet(), cheapestEq(), loadDemo()
   ├─ reminders      scheduledToday(), adherence(), streak(), weekChart(), scheduleNotifs()
   ├─ safety         CHECKS, TIPS, ABX_MSG, report generator
   ├─ about          TODOS panel
   └─ router         hash routes: #search · #m/<moleculeId> · #cabinet · #reminders · #safety · #about
```

## Domain logic notes

- **Price per unit** = pack MRP ÷ units in the pack (tablet, capsule, 5 ml dose or sachet). **Savings %** = (reference unit price − brand unit price) ÷ reference unit price, computed only between brands of the same molecule, strength and form; other rows show "—". Reference = the brand you searched for, else the originator, else the priciest brand; changeable from a dropdown.
- **Cabinet**: monthly cost = unit price × doses/day × 30; potential saving = monthly cost − monthly cost of the cheapest equivalent (same strength and form). Narrow-therapeutic-index molecules (levothyroxine) carry a warning and a confirmation before switching.
- **Interaction engine**: every item contributes its molecule id and pharmacological groups (NSAID, PPI, STATIN, MACROLIDE, SU, ANTIPLATELET); "also taking" chips contribute tokens (iron, calcium, antacid, potassium, alcohol, contrast). A rule fires when one token from side A and one from side B are both present for the same person; `dup` rules fire on two distinct molecules from one group; paracetamol is summed as mg per unit × doses/day against the 4 g adult limit. Results are sorted high → moderate → low.
- **Adherence**: `taken[YYYY-MM-DD]` holds `itemId@HH:MM` keys; a day counts toward the streak when every scheduled dose was ticked. Notifications are plain `setTimeout`s for today's remaining doses while the tab is open.

## Left for students

| Extension | Difficulty | Start here |
|---|---|---|
| Import the full DRAP MRP list (CSV parser + schema validation, "data as of" badge) | ★★ | `importDrapMrp()` |
| Barcode / QR scan with the BarcodeDetector API, GTIN → brand mapping | ★★ | `scanBarcode()` |
| Pharmacy locator map (Leaflet + Geolocation) tied to price logs | ★★ | `findNearbyPharmacies()` |
| Crowd-sourced price backend with login, moderation and MAD outlier rejection | ★★★ | `syncPriceLog()` and `seenStats()` |
| Prescription OCR (Tesseract.js, Urdu + English) with fuzzy matching into the cabinet | ★★★ | `ocrPrescription()` |
| Bioequivalence / quality rating data per brand | ★★ | `qualityRating()` and the Quick facts card |
| Sindhi and Pashto translations, four-way language menu, full RTL layout | ★ | `L.sd` / `L.ps` next to `L.ur` |
| Background dose notifications via service worker / Push API, installable PWA | ★★ | `scheduleNotifs()` |

Search the source for `TODO(student)` to find each stub.

## Data and safety notes

- The dataset is **illustrative**: 26 molecules and 88 brands with plausible 2026 pack MRPs. Manufacturers are real Pakistani and multinational companies, but every price, pack size and even some brand–manufacturer pairings are seed values for demonstration and must be verified against the DRAP MRP list and the pack in hand before any real use.
- Dawai is not medical advice. Counselling points and interaction rules are simplified teaching content covering common pairs only; they are not a complete clinical screen. Never switch brands of narrow-therapeutic-index medicines without a doctor and a follow-up test, and always finish antibiotic courses.
- Price observations and the cabinet never leave the device; a shared backend is deliberately left as a student extension so that moderation and consent are designed in from the start.

## Evaluation ideas for the FYP report

Validate the seed prices against a scraped DRAP list and report the error; run usability sessions with 5–10 patients in Urdu mode at a pharmacy counter; measure the share of cabinets where the cheapest equivalent would save more than Rs 500 per month; have a pharmacist review the interaction rule set for false positives and misses.
