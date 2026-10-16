# FYP Templates

Single-file HTML/CSS/JS project templates for Final Year Projects by Pakistani university students. Each project solves a real local (or globally useful) problem, works offline from `file://`, ships with proper SVG iconography and fluid motion, and deliberately leaves a documented set of features for the student to build.

Start with [`IDEAS.md`](IDEAS.md) for the full backlog of 90 ideas, or open [`index.html`](index.html) for a visual hub with a guided tour of the repository. Projects are numbered in build order; the idea number from the backlog is shown alongside.

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

## For students: money, competitions, careers, tools

**Money and support**
- Ignite's NGIRI program funds prototypes with up to Rs 84,700 for equipment and Rs 12,100 for other costs; the national champion team wins Rs 400,000. Check whether your department is registered. ([ignite](https://ignite.org.pk/?p=2503))
- HEC's Innovator Seed Fund gave 28 university startups grants of up to Rs 10 million each in its 2022-23 round. Students and recent alumni can apply if their incubation centre endorses them and they register a company with SECP. ([hec](https://www.hec.gov.pk/english/news/news/Pages/28-University-Startups-Win-HEC-Innovator-Seed-Fund.aspx))
- Campus pitch funds: Rawalpindi Women University's 2025 pitch fest awarded Rs 9 million in seed funding; Punjab is setting up funding pools at each university. ([dawn](https://www.dawn.com/news/1960379))
- NIC Islamabad gives selected startups a year of free workspace and has run campus sessions on turning an FYP into a startup. ([startup.pk](https://www.startup.pk/nic-islamabad/), [bahria](https://archive.bahria.edu.pk/?p=14865))

**Competitions and recognition**
- P@SHA ICT Awards: the 2026 cycle closed on 10 August; winners go to APICTA in Jakarta in December 2026. Aim for the 2027 cycle. ([propakistani](https://propakistani.pk/2026/08/03/psha-opens-applications-for-22nd-ict-awards-winners-to-represent-pakistan-at-apicta-2026/))
- Microsoft Imagine Cup 2027: teams of up to four students, up to $5,000 in Azure credits, listed deadline 8 January 2027, world championship May 2027. ([imaginecup](https://imaginecup.microsoft.com/en-us/category/34))
- Ignite's national FYP championship has drawn winners from universities across Pakistan, not only the big-city ones. ([propakistani](https://propakistani.pk/2017/05/20/student-entrepreneurs-honored-national-competition-final-year-projects/))

**Jobs and careers**
- At IBA Karachi's June 2026 FYP exhibition several students received job offers and interview calls. ([iba](https://smcs.iba.edu.pk/iba-smcs-hosted-the-computer-science-final-year-project-fyp-exhibition-2026.php))
- 32.8 % of graduates aged 20–24 are unemployed (Gallup Pakistan on the 2024-25 Labour Force Survey); a strong FYP moves you up the queue. ([gallup](https://galluppakistandigitalanalytics.com/2026/07/24/in-pakistan-a-university-degree-can-mean-a-longer-wait-for-a-job-not-a-faster-one/))
- Employers say new graduates lack job skills; your FYP is the evidence that you are the exception. ([thenews](https://www.thenews.pk/tns/detail/1397528-the-point-of-education))
- AI is cutting demand for routine programming while opening roles in AI engineering, cybersecurity and cloud. A basic data-entry app no longer stands out. ([bloompakistan](https://bloompakistan.com/?p=163533))
- IT exports hit a record $4.6 billion in FY26 with a $10 billion target by FY29; freelancer earnings passed $1 billion with 2.37 million registered freelancers. A finished, deployed FYP is a portfolio piece clients can try. ([profit](https://profit.pakistantoday.com.pk/2026/07/18/pakistans-it-exports-hit-record-dollar46-billion-in-fy26-surpass-government-target), [outsourceaccelerator](https://news.outsourceaccelerator.com/pakistan-it-exports-record/))
- The National AI Policy's High-Tech Internship Program targets 20,000 placements a year plus 3,000 postgraduate scholarships. ([meatechwatch](https://meatechwatch.com/2025/10/03/pakistan-launches-national-ai-policy-2025-with-goal-to-train-one-million-by-2030/))

**Tools**
- GPU access is the biggest limit for AI research in Pakistan; prefer ideas that run with small models on free Colab or Kaggle, or with APIs. ([geo](https://www.geo.tv/latest/611676-pakistani-students-revolutionary-geogemma-project-wins-top-google-award))
- Under the August 2026 Pakistan–Google agreement students get a year of free Google AI Plus and Gemini; a payment method is required and it converts to paid unless cancelled. ([propakistani](https://propakistani.pk/2026/08/21/how-pakistani-students-can-get-free-gemini-subscription-for-1-year/))

## Contributing / PRs

Each project lands as its own pull request from a `claude/project-NN-slug` branch into `main`, containing only `projects/NN-slug/`.
