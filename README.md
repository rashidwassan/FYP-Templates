# FYP Templates

Single-file HTML/CSS/JS project templates for Final Year Projects by Pakistani university students. Each project solves a real local (or globally useful) problem, works offline from `file://`, ships with proper SVG iconography and fluid motion, and deliberately leaves a documented set of features for the student to build.

Start with [`IDEAS.md`](IDEAS.md) for the full backlog of 85 ideas. Projects are numbered in build order; the idea number from the backlog is shown alongside.

## Projects

| # | Idea | Project | Folder | One line |
|---|---|---|---|---|
| 01 | #1 | Kisan Mandi Rate Board | `projects/01-kisan-mandi-rate-board` | Daily crop rates across 18 mandis, Sell Advisor ranking mandis by net revenue, alerts, community reports |
| 02 | #4 | Crop Doctor | `projects/02-crop-doctor` | Offline leaf analyzer, trainable TensorFlow.js classifier, symptom wizard, bilingual treatment cards |
| 03 | #11 | Dengue Watch | `projects/03-dengue-watch` | Town-level outbreak dashboard, WHO 2009 triage, fever diary, breeding-site map, prevention streaks |
| 04 | #12 | Khoon | `projects/04-khoon-blood-donor-network` | Compatibility-aware donor search, emergency requests, donor card with eligibility, blood banks |
| 05 | #13 | Thalassemia Care Companion | `projects/05-thalassemia-care-companion` | Transfusion and ferritin tracking, next-transfusion predictor, chelation adherence, screening |
| 06 | #15 | Dawai | `projects/06-dawai-generic-finder` | Brand → generic finder, price-per-unit compare, medicine cabinet savings, interactions, reminders |
| 07 | #16 | LHW Field Visit Assistant | `projects/07-lhw-field-visit-assistant` | Offline maternal/child register, ANC schedule with auto flags, EPI card, growth, referral slips, reports |
| 08 | #25 | Ishara — PSL Learner | `projects/08-psl-sign-language-learner` | Pakistan Sign Language lessons with animated sign schematics, SRS quiz, dictionary, webcam handshape practice |
| 09 | #26 | Board Exam Revision Planner | `projects/09-board-exam-revision-planner` | Weightage-aware study planner, Pomodoro, spaced revision, past-paper tracking, readiness score |
| 10 | #27 | Virtual Science Lab | `projects/10-virtual-science-lab` | Canvas physics and chemistry simulations with worksheets for schools without labs |
| 11 | #39 | Sailab — Flood Preparedness | `projects/11-sailab-flood-preparedness` | River gauges, district risk map, evacuation planner, relief camps, missing-person board |
| 12 | #43 | Kameti Manager | `projects/12-kameti-committee-manager` | Rotating savings committee ledger: members, lottery order, payments matrix, agreement |
| 13 | #51 | Privacy-First Password Manager | `projects/13-password-manager` | WebCrypto vault, strength analysis, generator, encrypted export |
| 14 | #52 | Algorithm Visualizer | `projects/14-algorithm-visualizer` | Sorting, pathfinding and graph algorithms animated with step controls and code highlighting |
| 15 | #56 | ATS-Friendly Resume Builder | `projects/15-resume-builder` | Templates, job-description keyword match, PDF export |
| 16 | #59 | Mind Map & Flowchart Editor | `projects/16-mindmap-flowchart-editor` | Canvas nodes and connectors, auto-layout, undo/redo, export |
| 17 | #60 | Client-Side Image Toolkit | `projects/17-image-toolkit` | Compress, resize, convert, crop, strip EXIF without uploading |
| 18 | #66 | Ludo | `projects/18-ludo` | Classic Ludo with AI opponents and local multiplayer |
| 19 | #74 | Rickshaw Rush | `projects/19-rickshaw-rush` | Endless runner through Karachi traffic with passengers and upgrades |
| 20 | #77 | Zameendar | `projects/20-zameendar-farm-sim` | Farm strategy simulation: seasons, water, crops and market prices |

Projects 01–08 are built; 09 onward are in progress. Each folder has an `index.html` (the whole app) and a `README.md` (getting started, problem, features, architecture, domain logic, student extensions).

## Conventions

- **Single file, no build.** Vanilla JS, inline CSS, hand-drawn SVG charts. The only network dependencies are Google Fonts and, where noted, lazily loaded libraries (TensorFlow.js, Leaflet) that degrade gracefully offline.
- **Local-first.** State lives in `localStorage` under a per-project prefix; realistic seed data makes every app usable on first open.
- **Icons and motion.** Inline SVG icon sprites (no emoji chrome), page transitions, staggered lists, animated numbers and charts, all honouring `prefers-reduced-motion`.
- **Bilingual.** English with an Urdu toggle (Noto Nastaliq Urdu) wherever the audience is the Pakistani public.
- **Distinct design per project.** Different palette, typeface and layout pattern each time; shared only in a small internal skeleton (store, toast, router, chart helpers).
- **Student extension points.** `// TODO(student):` comments mark exact stub points; each app's About page and README list what is left to build and where to start.

## Running

Open any `projects/*/index.html` in Chrome, Edge, Firefox or Safari. For camera, geolocation and notification features serve the folder over HTTP (for example `python3 -m http.server`), since browsers restrict those on `file://`.

## Contributing / PRs

Each project lands as its own pull request from a `claude/project-NN-slug` branch into `main`, containing only `projects/NN-slug/`.
