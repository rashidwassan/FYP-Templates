# 10 · Virtual Science Lab (idea #27)

**Fourteen interactive physics, chemistry and biology practicals for schools without laboratories.** Each experiment is a real simulation with adjustable parameters, live measurements, on-screen measuring tools, a graph, a readings table with a least-squares fit, bilingual theory with the model's simplifications, an auto-checked worksheet, a six-question quiz and a printable lab report. A teacher mode, glossary, printable lab manual, activity log and settings complete it. English / اردو. No libraries.

**Scale:** 7,836 lines in one `index.html` (one statement per line, functions under 60 lines) · 16 modules · 279 seed records (14 experiments, 62 worksheet questions, 84 quiz questions, 57 glossary terms, 53 demo-class submissions, 2 demo assignments, 7 tour steps) · 267 bilingual dictionary keys · 56 sprite icons · 8 student TODOs.

## Getting started

1. Open `index.html` in any modern browser. Everything runs offline from `file://` on a canvas; only the Google Fonts are fetched when online.
2. A short tour runs on first visit (restart it from Settings or About). Pick an experiment from the left rail (or the strip on phones), move the sliders, press **Run / Pause** and use the action buttons (Launch, Release, +50 g, Push through, +1 mL…).
3. Switch on the **Ruler**, **Protractor** or **Stopwatch** and drag their handles on the stage. Press **Take reading** (or `T`) to snapshot parameters and measurements into the **Data** tab, choose X/Y columns and a transform (x², 1/x, ln x) and read the slope, intercept and r².
4. Read **Theory** (both languages side by side, plus the model and its simplifications), answer the **Worksheet**, take the **Quiz**, then print or **Submit to teacher** from the **Lab report** tab.
5. Teachers open **Teacher mode** (set a 4-digit PIN on first use) to lock parameters, create assignment codes and read the class dashboard. **Load demo class** fills it with 12 students.
6. Routes are hash based: `#exp/<id>/<tab>`, `#glossary`, `#manual`, `#teacher`, `#log`, `#settings`. Keyboard: `Space` run/pause, `R` reset, `T` take reading, `1`–`7` tabs, `[` `]` previous/next experiment, `L` language, `?` shortcuts, `Esc` close.
7. All data lives in `localStorage` under the `vlab:` prefix. **Settings → Export all data (JSON)** backs it up, **Import JSON** restores it, and the two reset buttons clear worksheets/quizzes/readings or everything.

## The problem

Most government schools in Pakistan have no functioning science laboratory, so Matric and Intermediate students memorise practicals they have never performed. A phone or shared computer can run the core experiments and produce the measurements, graphs, data tables and reports that the syllabus expects, and a teacher on the same machine can set and check the work.

## What is built

| Experiment | Model | Students measure |
|---|---|---|
| Simple pendulum | θ'' = −(g/L) sin θ − bθ', four planets' gravity, zero-crossing period detection | Period vs length and gravity; derive g from the T²–L slope |
| Projectile motion | Integration with optional quadratic drag, auto-scaled field, velocity components | Range, maximum height, flight time vs angle |
| Ohm's law circuit | Single, series and parallel resistors, electron flow, bulb power glow | I = V/R, series and parallel totals, power |
| Waves & superposition | Travelling wave, two-wave interference with beats, standing wave with nodes | v = fλ, period, beat frequency, node spacing |
| Lenses & ray diagrams | Thin-lens formula, convex and concave, three principal rays | Image distance, magnification, nature |
| **Hooke's law** *(new)* | m x'' = mg − kx − bx' on an ideal spring, metre rule, elastic-limit flag | Extension vs load, k from the slope, T = 2π√(m/k), ½kx² |
| **Newton's second law** *(new)* | Cart + hanging mass over a pulley, kinetic friction μMg, timing gates at 1, 2, 3 m | a = (mg − μMg)/(M + m), a = 2x/t², tension |
| **Electromagnetic induction** *(new)* | Bar magnet through an N-turn coil, Φ(x) = BA/(1 + (x/w)²), ε = −N dΦ/dt, galvanometer | Peak EMF vs speed and turns, Lenz's law sign reversal |
| Acid–base titration | Strong acid/strong base pH, three indicators, hidden unknown samples | Equivalence volume, unknown concentration |
| Gas laws | PV = nRT with a piston, particle speeds ∝ √T, wall-hit counter | Boyle, Charles and Gay-Lussac relations |
| **Reaction rate** *(new)* | Second-order A + B with Arrhenius k = A·e^(−Ea/RT), catalyst lowers Ea, energy profile | Half-life vs T and concentration, ln k vs 1/T |
| **Electrolysis** *(new)* | Faraday's laws m = QM/(nF) for CuSO₄, AgNO₃ and water (2:1 gas), 60× clock | Mass vs charge, first and second laws |
| Osmosis | Water flux ∝ concentration difference across a semipermeable membrane | Direction of water movement, equilibrium |
| **Enzyme activity** *(new)* | Michaelis–Menten with Q10 = 2 below the optimum, Gaussian denaturation above it, pH optimum, irreversible denaturing | Optimum temperature and pH, rate vs substrate |

| Module | What it does |
|---|---|
| Measurement overlays | Draggable ruler (real units from each experiment's `scaleOf`), protractor with live angle, stopwatch on simulation time with laps |
| Readings + regression | "Take reading" snapshots every parameter and readout; table with delete, CSV export, column + transform pickers, slope/intercept/r², scatter plot with fitted line; "Fit line" on the live graph |
| Quiz bank | 84 multiple-choice questions (6 per experiment) with explanations, shuffle, best score and attempts |
| Lab report | Parameters, measurements, readings count and best-fit line, worksheet and quiz; print isolates the report; **Submit to teacher** |
| Teacher mode | PIN gate, per-experiment parameter locks (students see a lock), assignment codes that carry locks and a due date, class dashboard with animated KPIs, per-experiment bars, score histogram, submissions table, filters, CSV, demo class |
| Glossary | 57 bilingual terms with search, subject filter and "used in" links; Theory tab term chips open definitions |
| Lab manual | Choose experiments, fill name/class/school, print aim, apparatus, theory, model, procedure, a blank observation table and questions |
| Activity log | Opened experiments, readings, worksheet answers, quizzes, exports, prints, teacher and settings events, with filters and CSV |
| Settings | Language, text size, high contrast, reduce motion, grid, default speed, student name, assignment code, JSON export/import, resets, demo data, storage used |
| Onboarding + shortcuts | 7-step spotlight tour (desktop and phone targets), keyboard sheet |

## Module map

| Module | Main functions | Storage keys (`vlab:`) |
|---|---|---|
| Experiments registry | `EXPS[]` objects: `params, init, step, draw, readouts, graph, actions, scaleOf, theory, model, procedure, worksheet, terms` | — |
| Runner | `loadExp`, `paramsFor`, `enforceLocks`, `advance`, `frame`, `renderControls`, `renderActions`, `renderReads` | `cur`, `p:<id>` |
| Measurement tools | `drawRuler`, `drawProtractor`, `drawStopwatchBadge`, `onToolDown/Move/Up`, `renderTools`, `stopwatchAction` | `tools` |
| Graph | `drawGraph`, `graphBounds`, `drawAxes`, `plotLine`, `plotFit`, `toggleGraphFit`, `exportSeriesCsv` | — |
| Readings + regression | `takeReading`, `readingColumns`, `regressionPoints`, `readingsFit`, `renderData`, `drawRegression`, `exportReadingsCsv`, `linreg` | `readings:<id>` |
| Tabs | `renderTabs`, `setTab`, `renderTheory`, `renderAbout` | `tab` |
| Worksheet | `renderWS`, `bindWsQuestion`, `wsScore`, `scoreRing` | `ws:<id>` |
| Quiz | `QUIZ`, `newQuizDraft`, `renderQuiz`, `submitQuiz` | `quiz:<id>` |
| Lab report | `renderReport`, `paramText`, `submitReport`, `printSection` | `name`, `subs` |
| Glossary | `GLOSSARY`, `renderGlossary`, `glossMatches`, `openTerm` | — |
| Lab manual | `renderManual`, `manualArticleHtml`, `observationTableHtml`, `bindManual` | `manual` |
| Teacher mode | `renderTeacher`, `bindGate`, `renderLocksPanel`, `saveLocks`, `renderAssignPanel`, `createAssignment`, `renderDashboard`, `dashStats`, `loadDemoClass` | `teacher` (pin hash, locks, assignments), `subs` |
| Assignments (student side) | `activeAssignment`, `locksFor`, `applyAssignment`, `leaveAssignment`, `renderAssignBar` | `assign` |
| Activity log | `logEvent`, `renderLog`, `ago` | `log` |
| Settings + data | `applySettings`, `renderSettings`, `exportAll`, `importAll`, `resetData`, `refreshAll` | `settings`, `lang` |
| Tour, modal, keys, router | `startTour`, `maybeStartTour`, `placeTour`, `openModal`, `openKeys`, `onKey`, `route`, `showPage`, `renderSide`, `applyLang` | `tour` |

## Architecture (single file)

```
index.html
├─ <style>        tokens (graphite + acid), contrast/large-text/motion modes, motion kit, shell, lab, controls,
│                 tabs, data table, worksheet/quiz, report, pages, dashboard charts, modal, tour, mobile, print
├─ <svg sprite>   56 icon symbols (no emoji)
├─ <aside.side>   brand, experiments grouped by subject, page links, language/about
├─ <main>         phone page nav + experiment strip
│  ├─ #labView    head, assignment bar, stage canvas + transport, tabs
│  │              (Graph · Data · Theory · Worksheet · Quiz · Lab report · About), parameters, measurements, tools
│  └─ #pageView   glossary · lab manual · teacher mode · activity log · settings
├─ modal · tour spotlight · toasts
└─ <script>
   ├─ utils         store, esc, ic(), toast, download, seededRandom, linreg, printSection
   ├─ i18n          TXT.en / TXT.ur (267 keys each), t(), L()
   ├─ drawing       grid, line, arrow, label, dashed, circle, box, spring, heatColor, meter
   ├─ EXPS[]        14 experiments (physics 8 · chemistry 4 · biology 2)
   ├─ QUIZ, GLOSSARY
   ├─ runner        state, locks, paramsFor, loadExp, advance (240 Hz fixed step), frame, side panels
   ├─ tools         ruler, protractor, stopwatch, pointer dragging, tools panel
   ├─ graph + data  live graph with fit, readings table, transforms, regression scatter, CSV
   ├─ tabs          theory, worksheet, quiz, report + submit, about
   ├─ pages         glossary, lab manual, teacher mode (locks, assignments), dashboard, activity log, settings
   ├─ tour + keys   modal, shortcuts, spotlight tour
   └─ nav + boot    PAGES, route(), renderSide, applyLang, refreshAll, boot, window.VLAB test hook
```

Adding an experiment means pushing one object into `EXPS` (plus six questions in `QUIZ`); the runner, tools, data table, worksheet, quiz, report, manual, teacher locks and navigation pick it up automatically.

## Domain logic notes

- **Fixed-step integration.** Real elapsed time × speed is consumed in 1/240 s steps (semi-implicit Euler), so results do not depend on frame rate. The stopwatch accumulates the same simulated steps.
- **Each Theory tab states the model and its simplifications** (point masses, ideal springs, massless pulleys, thin lenses, ideal gas, full dissociation, 100 % current efficiency, analytic second-order kinetics, Michaelis–Menten with Q10 = 2).
- **Readings regression.** Ordinary least squares on transformed columns (`x²`, `1/x`, `ln x`), r² = 1 − SS_res/SS_tot. Example: pendulum T² against L gives slope 4π²/g.
- **Teacher locks.** Effective locks = the teacher's per-experiment locks plus those carried by the active assignment; locked values override saved parameters and survive action buttons.
- Verified headlessly: every experiment and every select option runs 1,200 steps with finite readouts and draws at 706 px and 362 px stage widths, and worksheet answers match the models (pendulum T = 2.01 s, half-life 29.9 s and 0.54 s with catalyst, Cu 0.636 g and Ag 2.16 g for 1930 C, Hooke 1.96 cm, Newton 1.18 m/s², EMF ∝ N, enzyme optimum 37 °C).

## Left for students

| Extension | Difficulty | Start here |
|---|---|---|
| Photosynthesis experiment (light intensity, CO₂ vs O₂ bubble rate) | ★★ | `TODO(student)` after `EXPS` |
| Cloud class sync so many phones feed one dashboard | ★★★ | `TODO(student)` below `renderDashboard()` |
| LMS export (xAPI statements to an LRS for Moodle / Google Classroom) | ★★ | `TODO(student)` in `submitReport()` |
| Uncertainty in the fit (standard error of slope/intercept, error bars) | ★★ | `TODO(student)` above `readingsFit()` |
| Canvas PNG snapshot embedded in the printed report | ★ | `TODO(student)` after `exportSeriesCsv()` |
| Accessibility: on-demand aria-live readouts and graph sonification | ★★ | `TODO(student)` in `renderReads()` |
| Sindhi, Pashto, Punjabi language packs | ★ | `TODO(student)` after `TXT` |
| Offline PWA for school tablets (manifest + service worker) | ★ | `TODO(student)` in `boot()` |

Search the source for `TODO(student)`; the same list appears in the About tab.

## Data and safety notes

- Simulation values come from the equations shown in each Theory tab and are meant for learning. The titration "unknown" samples are fixed (0.10, 0.15 and 0.08 M) so worksheet answers can be checked; the reaction-rate pre-exponential factor is chosen so the reaction takes about 30 s at 300 K.
- Teacher mode is local to one device. The PIN is stored as a simple hash and deters casual changes on a shared computer; it is not security. Submissions never leave the device unless exported.
- The demo class (12 students, 53 submissions) is generated with a fixed seed and marked `demo` so it can be replaced without touching real submissions.
