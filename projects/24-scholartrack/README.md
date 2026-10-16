# 24 · ScholarTrack — Scholarship Tracker & Application Manager (idea #89)

**Find the scholarships you actually qualify for, then run every application like a project.** ScholarTrack is a bilingual (English / Urdu) scholarship newspaper for Pakistani students: a 52-entry database of Pakistani, provincial, university and international awards, an eligibility quiz that explains every score, a kanban pipeline, document checklists linked to a shared vault with expiry reminders, an essay desk with limits, structure checks, versions and a paragraph bank, a referee tracker with polite templates, a deadline calendar with `.ics` export, statistics, a funding calculator and ten short guides. Editorial design: Fraunces headings, Public Sans body, navy, gold and paper cream.

**Scale:** about 6,550 lines in one `index.html` (about 5,370 lines of JavaScript, 1,020 of CSS, 99 inline SVG icons) · 240 named functions · 20 modules · 750 bilingual string pairs · seed records: 52 scholarships (18 Pakistani, 34 international or multi-country), 19 document types, 10 guides (40 sections, 50 checklist items), 17 city cost presets, and a demo student with 9 applications (68 checklist items), 12 vault documents, 4 essays with 5 saved versions, 6 bank paragraphs, 4 referees with 9 letter requests, 5 calendar events and 7 inbox notices · 8 student TODOs.

## Getting started

- Open `index.html` in any modern browser. It runs from `file://` with no build step and no server. The only network request is Google Fonts (Fraunces, Public Sans, Noto Nastaliq Urdu); system fonts are used offline.
- The first open starts a six-step spotlight tour. A demo student, Ayesha (MS in computer science, Punjab domicile), makes every screen useful immediately; hide her with **Settings > Show demo data**. Nothing is deleted.
- **Where data lives:** `localStorage` under the `st:` prefix: `settings`, `lang`, `profile` (quiz answers), `apps`, `vault`, `essays`, `bank`, `referees`, `events`, `inbox`, `activity`, `shortlist`, `compare`, `schEdits` (your links and notes on catalogue entries), `customSch` (scholarships you added), `funding`, `dbView`, `calKinds`, `guideChecks`, `guideBoth`, `seeded`.
- **Backup / reset:** Settings > Your data exports everything as JSON, imports a backup, clears quiz answers or resets everything (type `RESET`). From the console: `Object.keys(localStorage).filter(k => k.startsWith('st:')).forEach(k => localStorage.removeItem(k))`.
- **Calendar export:** Calendar > Export .ics downloads all upcoming deadlines, interviews, document expiries and referee due dates; each agenda row also has its own `.ics` button. Google Calendar, Outlook and phone calendars import the file.
- **Keyboard:** `1`–`0` jump to sections, `Ctrl K` or `/` search, `N` new application, `L` English/Urdu, `T` day/night edition, `I` inbox, `Alt` + arrows moves a focused pipeline card, `?` shows the sheet, `Esc` closes dialogs.

## The problem

Pakistani students lose scholarships for reasons that have nothing to do with merit: a domicile that took six weeks, an IELTS score that expired a month before the intake, a referee who was never reminded, an SOP that ran 80 words over the limit, or a deadline that was "sometime in November". Information is scattered across HEC notices, provincial endowment funds, university pages and foreign portals, and most students track it in WhatsApp chats and a notebook. ScholarTrack puts discovery, honest eligibility checking and the whole application workflow in one offline tool, in Urdu as well as English, and keeps telling the student to verify dates on the official site.

## What is built

| Area | Features |
|---|---|
| Front page | Lead story on the next deadline that needs action (stage, documents ready, letters pending, essay words), KPI column, this fortnight's agenda, reminders, best matches from the quiz, pipeline at a glance, windows opening soon, a featured guide |
| Scholarship database | 52 entries (HEC need-based, overseas and indigenous PhD, PM fee reimbursement, Benazir undergraduate, PEEF, CM Punjab Honhaar, Sindh / Balochistan / KP endowment funds, Bait-ul-Mal, Ihsan Trust, LUMS, NUST, Habib, IBA, GIKI, AKU, Fulbright, Humphrey, Global UGRAD, Chevening, Commonwealth, GREAT, British Council Women in STEM, Gates, Rhodes, Clarendon, DAAD, Erasmus Mundus and Erasmus+, Stipendium Hungaricum, Swedish Institute, Holland, Eiffel, Ireland, Türkiye Bursları, MEXT, JDS, CSC, Schwarzman, GKS, KAUST, IsDB, ADB–Japan, Aga Khan Foundation, Australia Awards, Knight-Hennessy); search, destination / level / field / region / basis filters, fully-funded, open-now, closing-soon, shortlist and eligible-only toggles, four sort orders, coverage icons, typical window with estimated deadline, match column, CSV export, detail drawer with match reasons, eligibility, coverage, documents, notes and an editable official-site link, user-added scholarships |
| Compare | Up to three scholarships side by side (country, levels, fields, basis, coverage, value, duration, window, deadline, eligibility, documents, your match, official link), printable |
| Eligibility quiz | Four steps (degree and destination, record, domicile and age, finances and commitments), validation, results with an animated score ring per scholarship, every rule that passed, warned or failed, eligible / all views, score distribution, profile summary |
| Application pipeline | Kanban with five stages (eligible, documents, submitted, interview, result), HTML5 drag and drop plus arrow buttons and `Alt` + arrow keys, deadline countdown chips, document and letter progress, priority edge, outcome recording (awarded / waitlisted / not selected with amount), filters, mobile stage tabs |
| Application detail | Stage stepper with dates, KPIs, key dates (deadline with typical-estimate reset, interview, intake month, priority), eligibility and coverage, tabs for documents, linked essays, referee letters and a timeline with notes |
| Documents | Per-application checklists (missing / in progress / ready / submitted / not needed, notes, add or remove items), a shared vault (type, title, issue and expiry dates with typical-validity suggestion, location, attested copies, status) that ticks matching checklist items when a document becomes ready, warnings when a document expires before a deadline or within six months of an intake, document reminders with a configurable lead time, printable checklists |
| Essays and SOPs | Essay cards with limit bars, structure and readability badges; editor with word or character limits, live meter and over-limit alert, eight-point structure checklist (automatic heuristics with manual override), Flesch reading ease, grade level, sentence length, long sentences, passive voice, filler words, clichés, repeated words, labelled versions with word-level diff and restore, paragraph bank (tags, insert at cursor, save selection to bank), outline starter, print layout |
| Referees | Referee cards with letter requests per application (to ask / asked / agreed / submitted / declined), due dates, reminder count and last reminder, request / reminder / thank-you templates in English and Urdu (Urdu verb forms follow the applicant's gender), copy and open-in-email, etiquette panel |
| Calendar | Month grid with typed event pills, agenda for the selected day and the next 60 days, kind filters, custom events, window planner (12-month strip of typical windows for the shortlist and pipeline), RFC 5545 `.ics` export of all or single events with alarms |
| Statistics | Applications, submitted, interviews, awards, success rate, funding won; pipeline funnel, outcomes donut, monthly submissions line chart, destinations, document readiness, value in play versus won |
| Funding calculator | Scholarship presets (coverage flags to tuition %, typical stipend, airfare, health, allowance), 17 city presets in local currency, editable costs and exchange rate, part-time income and family savings, verdict, monthly / programme / PKR totals, stacked income versus spending bars |
| Guides | Ten bilingual articles (SOP, domicile, attestation, income certificates, recommendation letters, English tests, interviews, a 12-month timeline, scams, after the award) with checklists that persist, a "both languages" view, print layout |
| Platform | Settings (language, day / night edition, text size, reduced motion, reminder lead times, demo data, JSON export / import / reset), six-step spotlight tour, inbox with deduplicated reminders and an activity log, printable portfolio report, global search palette, keyboard shortcuts, full Urdu RTL layout, aria-live announcements, labelled controls, focus trap in dialogs |

## Module map

| Module | Key functions | Storage |
|---|---|---|
| Utils and dialogs | `store`, `esc`, `ic`, `toast`, `announce`, `animateIn`, `animateMetrics`, `openModal`, `openDrawer`, `confirmBox`, `downloadText`, `pickFile` | — |
| i18n | `defineStrings`, `t`, `tIn`, `both`, `tr`, `fmtDate`, `relDays`, `fmtCompactPKR` | `st:lang` |
| Catalogue | `CATALOGUE`, `LEVELS`, `FIELDS`, `PROVINCES`, `COUNTRIES`, `COVER`, `DOC_TYPES`, `allScholarships`, `editScholarship`, `typicalDeadline`, `inWindow`, `windowText` | `st:schEdits`, `st:customSch` |
| State and seed | `settings`, `setSetting`, `list`, `saveList`, `mutateIn`, `vis`, `seedIfNeeded`, `checklistFor`, `logActivity`, `notify` | `st:settings`, `st:seeded`, `st:activity` |
| Matcher | `matchScholarship`, `matchAll`, `RULES` (`rLevel` … `rDestination`, `rExtras`), `cgpaToPct`, `pctToCgpa` | `st:profile` |
| Reminders | `reminders`, `appReminders`, `vaultReminders`, `refereeReminders`, `syncReminders` | `st:inbox` |
| Database and compare | `renderDatabase`, `filterScholarships`, `sortScholarships`, `dbRow`, `openScholarship`, `eligibilityList`, `toggleCompare`, `openCustomScholarship`, `exportScholarshipsCsv`, `renderCompare` | `st:dbView`, `st:shortlist`, `st:compare` |
| Quiz | `renderQuiz`, `renderQuizForm`, `readQuizStep`, `finishQuiz`, `renderQuizResults`, `matchCard`, `scoreRing` | `st:profile` |
| Pipeline | `renderPipeline`, `drawKanban`, `kanbanCard`, `bindDropZones`, `moveApp`, `openOutcome`, `openNewApplication`, `createApplication` | `st:apps` |
| Application detail | `renderApp`, `stageStepper`, `overviewTab`, `timelineTab`, `bindTimeline` | `st:apps` |
| Documents | `renderDocuments`, `checklistHtml`, `bindChecklist`, `docWarnings`, `vaultTab`, `openVaultDoc`, `saveVaultDoc` | `st:apps`, `st:vault` |
| Essays | `renderEssays`, `essayCard`, `countWords`, `readability`, `syllables`, `structureState`, `diffWords`, `openBankItem`, `openNewEssay` | `st:essays`, `st:bank` |
| Essay editor | `renderEssayEditor`, `meterHtml`, `structurePanel`, `readabilityPanel`, `versionsPanel`, `insertAtCursor`, `openDiff`, `restoreVersion` | `st:essays` |
| Referees | `renderReferees`, `refRequestRow`, `fillTemplate`, `urduForms`, `openRefTemplate`, `openLetterRequest` | `st:referees` |
| Calendar | `calendarEvents`, `monthGrid`, `agendaHtml`, `windowPlanner`, `buildIcs`, `foldLine`, `exportIcs` | `st:events`, `st:calKinds` |
| Statistics | `renderStats`, `stageReached`, `funnelChart`, `donut`, `hbars`, `lineChart` | — |
| Funding | `CITIES`, `RATES`, `fundingPreset`, `computeFunding`, `fundingResult`, `renderFunding` | `st:funding` |
| Guides | `GUIDES`, `renderGuides`, `renderGuide`, `guideCard` | `st:guideChecks`, `st:guideBoth` |
| Front page, inbox, report | `renderHome`, `leadStory`, `renderInbox`, `noticeRow`, `renderReport` | `st:inbox`, `st:activity` |
| Search, settings, tour, router | `openPalette`, `searchIndex`, `renderSettings`, `exportData`, `importData`, `startTour`, `onGlobalKey`, `renderAbout`, `route`, `refreshChrome`, `boot` | `st:settings` |

## Architecture (single file)

```
index.html
├─ <head>    Google Fonts link; <style>: tokens (day + night edition, text sizes), base, Urdu / RTL rules, motion kit,
│            buttons / chips / forms / switches, surfaces and KPIs, masthead + section nav, mobile bottom bar and drawers,
│            database table (card layout under 640 px), quiz, kanban, stepper and timeline, checklists, essays and editor,
│            referees, calendar and window planner, charts, funding, guides, front page, inbox / report / settings /
│            palette, modal / toast / tour, responsive (1180 / 980 / 860 / 640 px), print
├─ <body>    inline SVG sprite (generated from the icons the code uses), masthead (dateline, tools, crest), sticky
│            section nav, <main #view>, colophon, bottom bar, overlay root, tour layer, toasts, aria-live region
└─ <script>
   ├─ utils             store (st: prefix), esc, ic, hash + PRNG, dates, download / file / clipboard, toast, announce,
   │                    animateIn, countUp, drawPath, animateMetrics, modal / drawer / confirm, focus trap
   ├─ i18n              STR registry, t / tIn / both / tr, months and weekdays, date and money formatting
   ├─ catalogue         reference tables, 52 scholarships (sch helper), CAT_BY_ID
   ├─ guides            10 bilingual articles
   ├─ state             settings, vocabularies (stages, outcomes, statuses), collections, scholarships + edits,
   │                    windows and estimated deadlines, activity log, inbox
   ├─ seed              demo profile, applications, vault, essays, bank, referees, events, seedIfNeeded
   ├─ engine            eligibility rules and matcher, reminders engine
   ├─ database          filters, table, drawer, link edits, custom entries, CSV, compare
   ├─ quiz              stepper form, validation, results
   ├─ pipeline          kanban, drag and drop, outcomes, new application
   ├─ application       detail page, stepper, tabs, timeline
   ├─ documents         checklists, vault, reminders tab
   ├─ essays            text analysis, readability, structure, diff, list, bank, new essay
   ├─ editor            meter, side panels, versions, insert, settings
   ├─ referees          templates, request rows, cards, forms
   ├─ calendar          events, month grid, agenda, window planner, iCalendar builder
   ├─ statistics        charts and KPIs
   ├─ funding           city presets, calculator
   ├─ guides reader     list and article pages
   ├─ front page        lead story, KPIs, agenda, reminders, matches, glance, opening soon
   ├─ inbox + report    notices, activity log, printable portfolio
   ├─ search + settings palette, settings, backup / import / reset
   ├─ tour + about      spotlight tour, shortcuts, student TODO list
   └─ router + boot     nav chrome, theme and language, routes, boot, window.ScholarTrack test seam
```

Routes: `#home` · `#database` · `#compare` · `#quiz[/edit]` · `#pipeline` · `#app/<id>[/overview|docs|essays|refs|timeline]` · `#documents[/checklists|vault|reminders]` · `#essays` · `#essay/<id>` · `#referees` · `#calendar` · `#stats` · `#funding` · `#guides` · `#guide/<id>` · `#inbox[/activity]` · `#report` · `#settings` · `#about`.

The source lives in numbered parts (`00-head.html` … `99-tail.html`) assembled by a small `build.sh`; the shipped file is the concatenation, readable top to bottom.

## Domain logic / algorithm notes

- **Typical windows and estimated deadlines.** Each scholarship stores a typical window as `[open month, close month]` (it may wrap the year, e.g. Oct–Jan) or `null` for rolling schemes. `typicalDeadline` returns the last day of the closing month on or after today; `isOpenNow` checks the current month against the window. Every estimate is labelled "typical — verify on the official site", and the user can overwrite any application's deadline.
- **Eligibility matcher.** `matchScholarship` runs twelve weighted rules (level 30, field 15, domicile 15, less-developed district 15, income 15, academics 15, age 10, work 10, English 10, return bond 10, gender 10, destination 10) plus extras (special quotas, admission needed, loan not grant). Each returns pass / warn / fail with a sentence; "close" cases earn partial credit (income up to 10 % above the cap, CGPA within 0.1 or marks within 3 points, work within six months, English within half a band). Score = earned ÷ total weight × 100; any hard failure marks the scholarship ineligible and caps the score at 35. Results sort eligible first, then by score, then by approximate value. When a scholarship states only one scale, CGPA and percentage are converted with `percentage ≈ 20 × CGPA + 10` (labelled approximate).
- **Reminders engine.** `reminders()` derives notices from the data: deadlines within the lead time (default 14 days) for applications still being prepared, overdue estimates, missing documents when a deadline is within 30 days, interviews within 14 days, vault documents expiring within the lead time (default 60 days) or expired, a passport that expires within six months of any overseas intake (one consolidated notice), and referee letters due within 14 days. Each notice has a stable key (kind + record + date) so `syncReminders` pushes it to the inbox exactly once, in both languages.
- **Document vault linkage.** Checklists are built from each scholarship's document list and pre-marked ready from the vault. Saving a ready vault document marks matching missing items ready in every active application; moving an application to Submitted marks its ready items submitted. Expiry is suggested from typical validity (e.g. IELTS 24 months, income certificate 6 months, passport 10 years).
- **Readability.** Flesch reading ease = 206.835 − 1.015 × (words / sentences) − 84.6 × (syllables / words), with a vowel-group syllable heuristic; Flesch–Kincaid grade = 0.39 × words per sentence + 11.8 × syllables per word − 15.59. Also counted: sentences over 30 words, possible passive voice (`be` + past participle), filler words, clichés and the most repeated content words. Urdu text skips the formulas.
- **Structure checklist.** Eight regular-expression heuristics (hook, motivation, academic background, evidence with numbers, goals, programme fit, return plan, strong close) mark sections as detected; the writer's manual tick overrides the heuristic and is stored per essay.
- **Version diff.** `diffWords` tokenises both texts into words and whitespace and runs a longest-common-subsequence table (Uint16 rows) to produce equal / inserted / deleted runs; pairs above four million cells fall back to whole replacement. Restoring a version first saves the current text as a "Before restore" version.
- **iCalendar export.** `buildIcs` writes RFC 5545 all-day events (`DTSTART;VALUE=DATE`, exclusive `DTEND`), escapes commas, semicolons, backslashes and newlines, adds `VALARM` triggers (7 days before deadlines, 2 before interviews, 30 before expiries, 3 before referee due dates) and folds lines at 75 octets with a UTF-8-aware `foldLine`, so Urdu titles are never split inside a character.
- **Funding calculator.** Monthly net = stipend + part-time income − (rent + food + transport + health unless covered + other). Programme total = monthly net × months − uncovered tuition × months / 12 − one-off costs (set-up, return flights unless covered, minus any allowance); the PKR position adds family savings at an editable exchange rate. Presets map coverage flags to tuition % and a typical stipend converted from the award country's currency into the chosen city's currency.

## Left for students

| Extension | Difficulty | Start here |
|---|---|---|
| Cloud sync and accounts (authenticated backend, per-record timestamps, conflict resolution, offline cache) | Hard | `store` |
| Live scholarship feed (curated, versioned JSON maintained server-side, "changed since last visit" badges) | Hard | `allScholarships()` |
| Push, email and SMS reminders (service worker + Notification API or a server cron, quiet hours, opt-outs) | Medium | `syncReminders()` |
| AI feedback on essays through a server proxy, with personal details stripped and accept / reject suggestions | Medium | `readability()` |
| Read expiry dates with OCR (lazy-loaded Tesseract.js, on-device, pre-fills the vault form) | Hard | `openVaultDoc()` |
| Share the portfolio with a mentor (read-only expiring link, comments back into the inbox) | Medium | `renderReport()` |
| Essay reuse detector (5-word shingles, Jaccard similarity across essays and the bank) | Medium | `diffWords()` |
| Official grade conversion tables per university or HEC instead of the linear rule | Easy | `cgpaToPct()` |

Search the source for `TODO(student)`; each marker sits at the stub point with implementation notes, and the About page lists them in English and Urdu.

## Data and safety notes

- **Every scholarship window, eligibility rule, amount, duration and coverage flag is an illustrative "typical" value**, simplified for a student project. Programmes change their rules, quotas, deadlines and even their names every year. The interface labels these values "typical — verify on the official site" wherever they appear, and nothing in ScholarTrack should be treated as current fact.
- Official-site links point at the provider's main website where it is well known and are left empty otherwise; users can paste the exact page. Always reach scholarship portals by typing the official address rather than following links in messages.
- City living costs, typical stipends and exchange rates in the funding calculator are rough planning figures, editable on screen, not current prices.
- The demo student, her referees (example.edu.pk / example.com addresses), employer, essays and documents are fictional.
- The guides are general information, not legal or immigration advice; procedures for domicile, attestation and visas vary by district, province and country.
- All data stays in this browser. The vault stores only titles, dates and where a paper is kept — never upload CNIC or passport scans to a shared computer. Reminder templates open in the user's own email app; nothing is sent automatically.
- Need-based awards verify income: the quiz asks for household income only to filter typical caps, and the value never leaves the device.

## Evaluation ideas for the FYP report

- **Matcher validity:** have a scholarship counsellor label 20 real student profiles against 15 schemes (eligible / not); report precision, recall and agreement with the matcher, and which rules cause disagreements.
- **Deadline estimate error:** for one cycle, compare `typicalDeadline` with the published deadlines of 30 schemes and report the mean absolute error in days; motivate the live-feed extension.
- **Task-based usability:** time students on "find three fully funded master's awards you qualify for and add one to the pipeline", "find which document expires before your intake" and "send a reminder to a referee", with a paper notebook or spreadsheet as the baseline; add a System Usability Scale questionnaire and a separate Urdu-first group.
- **Readability and structure:** correlate the readability and structure scores of 30 anonymised SOPs with ratings from two teachers; report Spearman correlation and inter-rater agreement.
- **Reminder effectiveness:** in a two-month pilot, compare missed or last-day submissions between students using reminders and `.ics` export and those who are not.
- **Accessibility and performance:** keyboard-only walkthrough, screen-reader spot checks of the aria-live announcements, contrast checks in day and night editions, reduced-motion mode, and load and filter timings on a low-end Android phone at 390 px.
