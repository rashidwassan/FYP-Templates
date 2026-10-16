# FYP Templates

Single-file HTML/CSS/JS project templates for Final Year Projects by Pakistani university students. Each project solves a real local problem, works offline from `file://`, and deliberately leaves a documented set of features for the student to build.

Start with [`IDEAS.md`](IDEAS.md) for the full backlog of 85 ideas (50 Pakistan-focused, 15 global, 20 games).

## Projects built

| # | Project | Folder | One line |
|---|---|---|---|
| 01 | Kisan Mandi Rate Board | `projects/01-kisan-mandi-rate-board` | Daily crop rates across 18 mandis, Sell Advisor ranking mandis by net revenue, alerts, community reports |
| 04 | Crop Doctor | `projects/04-crop-doctor` | Offline leaf analyzer, trainable TensorFlow.js classifier, symptom wizard, bilingual treatment cards |
| 11 | Dengue Watch | `projects/11-dengue-watch` | Town-level outbreak dashboard, WHO 2009 triage, fever diary, breeding-site map, prevention streaks |
| 12 | Khoon | `projects/12-khoon-blood-donor-network` | Compatibility-aware donor search, emergency requests, donor card with eligibility, blood banks |
| 13 | Thalassemia Care Companion | `projects/13-thalassemia-care-companion` | Transfusion and ferritin tracking, next-transfusion predictor, chelation adherence, screening |
| 15 | Dawai | `projects/15-dawai-generic-finder` | Brand → generic finder, price-per-unit compare, medicine cabinet savings, interactions, reminders |
| 16 | LHW Field Visit Assistant | `projects/16-lhw-field-visit-assistant` | Offline maternal/child register, ANC schedule with auto flags, EPI card, growth, referral slips, reports |

Each folder has an `index.html` (the whole app) and a `README.md` (problem, features, architecture, domain logic, student extensions).

## Conventions

- **Single file, no build.** Vanilla JS, inline CSS, hand-drawn SVG charts. The only network dependencies are Google Fonts and, where noted, lazily loaded libraries (TensorFlow.js in Crop Doctor, Leaflet in Dengue Watch) that degrade gracefully offline.
- **Local-first.** State lives in `localStorage` under a per-project prefix; realistic seed data makes every app usable on first open.
- **Bilingual.** English with an Urdu toggle (Noto Nastaliq Urdu) for key labels and all patient/farmer-facing text.
- **Distinct design per project.** Different palette, typeface and layout pattern each time; shared only in the small internal skeleton (store, toast, router, chart helpers).
- **Student extension points.** `// TODO(student):` comments mark exact stub points; each app's About page and README list what is left to build and where to start.

## Running

Open any `projects/*/index.html` in Chrome, Edge, Firefox or Safari. For camera, geolocation and notification features serve the folder over HTTP or HTTPS (for example `python3 -m http.server`), since browsers restrict those on `file://`.
