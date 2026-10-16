# 15 · ATS-Friendly Resume Builder (idea #56)

**Scale:** 5,699 lines in one HTML file (about 4,950 lines of JavaScript) · 20 modules (8 new, 3 extended) · seed data: 5 sample profiles, 8 tracked applications, 34 interview questions, 6 role profiles with 89 skills and 74 learning resources.

**A resume the robots can read, and a job search that stays organised.** A single-file resume builder for fresh graduates: an accordion editor with a live A4 preview, six templates that are ATS-safe by construction, an ATS score that matches your resume against a pasted job description and flags Pakistani-CV habits (photo, CNIC, marital status), a rule-based bullet improver, a cover-letter generator and PDF / TXT / Markdown / JSON export. Around it sit the rest of the job search: an application tracker linked to resume versions, a skills-gap analyser with a learning plan, an interview bank with a STAR answer builder that drafts from your own bullets, a LinkedIn headline and About generator, a portfolio one-pager export and a bilingual / Urdu CV mode. English UI with Urdu CV output.

## Getting started

- Open `index.html` in any modern browser (Chrome/Edge recommended for the month pickers and `@page` printing). No build step, no server; the only external resource is Google Fonts (Lora, Karla and Noto Nastaliq Urdu), with system-font fallback offline.
- **Print / Save as PDF** uses the browser's print dialog: choose "Save as PDF", keep margins at *Default* (the file sets them with `@page`) and turn off headers/footers. The PDF file name is pre-filled as `Firstname_Lastname_Resume`. `Ctrl/Cmd + P` prints the resume, or the cover letter / application report when you are on those pages.
- All state lives in `localStorage` under the `cvb:` prefix: `cvb:resumes` (every version) and `cvb:ui` (zoom, open sections, pasted job description, module filters) are unchanged from the first version, so resumes saved earlier still load. New keys: `cvb:apps`, `cvb:star`, `cvb:questions`, `cvb:gap`, `cvb:linkedin`, `cvb:log`, `cvb:settings`.
- **Reset:** Settings › Reset all data (export a backup first with Settings › Export everything). The console one-liner still works: `Object.keys(localStorage).filter(k => k.startsWith('cvb:')).forEach(k => localStorage.removeItem(k))`.
- On first open two sample versions load (a fictional FAST-NUCES graduate) plus eight demo applications, so every screen has content. The **Sample** button or the version menu loads three more profiles (UET electrical engineer, IBA marketing graduate, NUST mobile developer). A **Take the tour** card at the top of the editor runs a 7-step spotlight tour.

## The problem

Graduates in Pakistan apply with designer templates full of two-column tables, icons and photos that applicant-tracking systems cannot parse. They add a CNIC number, father's name, date of birth and marital status, and they never tailor keywords to the job ad. The CV is filtered out before a human reads it. Then they lose track of which version went to which company, walk into interviews without prepared stories, and leave their LinkedIn headline as "Student at …". Career offices repeat the same advice every semester; this tool bakes it into one workflow.

## What is built

| Area | Features |
|---|---|
| Editor | Contact (photo and CNIC toggles **off by default** with an ATS note; Urdu name field in Urdu/bilingual mode), Summary with word count, Experience / Education / Projects / Certifications / Languages / Awards / Publications / Teaching / Custom section; bullet editor (Enter adds, Backspace on empty removes, drag or arrow keys reorder, sparkle button improves); Skills as chip groups; drag-and-drop reorder of sections, entries and bullets with keyboard fallback; show/hide sections; autosave; multiple versions with open / rename / duplicate / delete / new blank / import; first-run welcome card |
| Templates | Classic, Modern (main column + tinted sidebar, DOM stays linear), Compact, Academic CV, **Minimal** (hairline rules, quiet grey headings) and **Bold header** (accent band with white name, single column below). Four accents, font scale 85–115 %, A4 or US Letter. All: real `<h1>/<h2>`, no tables, no text in images |
| Bilingual / Urdu CV | Preview-bar language select: English, Bilingual (each heading followed by its Urdu twin in Nastaliq, Urdu name under the English one) or Urdu (RTL page, Urdu headings, Urdu name as the title). Content blocks get `dir="auto"` so English lines stay LTR and Urdu lines run RTL; contact items and dates are isolated LTR runs |
| Sample profiles | Five fictional graduates: FAST-NUCES software engineer, its data/ML variant, UET electrical engineer, IBA digital marketer, NUST Flutter developer. Picker can replace the current version or add a new one |
| Preview | Real 210 × 297 mm sheet scaled to fit, zoom, page counter and dashed page-break indicators; print CSS with `@page { size; margin }` driven by the template, everything but the page hidden |
| Export | Print / Save as PDF, plain-text `.txt`, copy as Markdown, JSON backup and import (validated, normalised), **portfolio one-pager** `.html`, **export everything** (all modules in one JSON) |
| ATS score | Pill in the top bar opens a drawer: score ring, structure vs keyword bars, verdict, job-description textarea with a sample ad, matched / missing keyword chips with one-click add, prioritised fix list, language-mode note, file-name and portal tips |
| Bullet improver | Weak-starter rewrite, verb upgrade table, metric placeholder, STAR template, remove first person, split long bullets, tidy casing; AI rewrite stub |
| Cover letter | Form + letter generated from the resume, editable body, live A4 letter, print and copy |
| **Application tracker** | Kanban board saved → applied → interview → offer with drag-and-drop and arrow buttons, closed (rejected / withdrawn) drawer, add/edit form (company, role, city, source, link, resume version sent, dates, salary, notes), detail modal with stage history, filter by resume version, KPIs (tracked, interviews, offers, interview rate, follow-ups due), **version performance table** (which resume version gets interviews), follow-up / interview / deadline reminders, printable application report |
| **Skills gap** | Six role profiles (frontend, backend, data, mobile, electrical/embedded, digital marketing). Proof-weighted match per role, per-skill status (proven in a bullet / only listed / missing) with where it was found, nice-to-have list, week-by-week learning plan with hours per week and finish date, plan items ticked off and added to the resume, SVG chart comparing every version for the selected role |
| **Interview prep + STAR** | 34 behavioural, HR and role-specific technical questions with tips, filters and search, own questions; STAR builder (Situation / Task / Action / Result) with live word count, spoken-time estimate, checks (number in result, "I" in action, placeholders left); bullet picker ranked by relevance that drafts all four parts from a resume bullet; confidence rating; timed 2-minute practice round with self-rating; export answers as text |
| **LinkedIn** | Five headline formulas with 220-character counter, editable chosen headline, About generator in three tones (professional, friendly, concise) with 2,600-character meter, top skills ranked by evidence in bullets, profile checklist (auto-detected + manual items), portfolio shortcut |
| **Portfolio one-pager** | Standalone responsive HTML (no scripts) from the resume: hero, contact links, about, project cards, experience, skills chips, education; section toggles, light/dark theme, sandboxed live preview, download or copy |
| **Settings** | Default template / paper / CV language for new resumes, follow-up reminder days, demo applications on/off, reduce motion, compact cards, export everything / import backup (merge or replace), load all samples, reset, storage meter per key, keyboard-shortcut sheet |
| **Activity + notifications** | Every meaningful action logged (versions, exports, stage changes, practice runs, milestones), unread badge on the bell, popover with the latest entries, full page with filters, search, mark-all-read and clear |
| **Onboarding tour** | Seven spotlight steps (editor, preview, templates, ATS score, export, module navigation, reminders) that skip hidden targets at phone width; arrow keys and Esc work |
| Motion & a11y | Page enter, staggered cards, animated KPIs and bars, chart bar growth, modal/drawer/tour transitions, toasts; all off under `prefers-reduced-motion` or the Settings toggle. Labels on every input, `aria-label` on icon buttons, Esc closes everything, `Alt + 1…9` jumps between modules, `?` opens shortcuts, touch targets ≥ 40 px |

## Module map

| Module | Main functions | Storage keys |
|---|---|---|
| Utilities | `store`, `esc`, `ic`, `toast`, `animateIn`, `countUp`, `isoDate`, `daysBetween`, `relTime` | — |
| Sample profiles | `SAMPLE_SWE/EE/MKT/MOBILE`, `withIds()`, `sampleResume()`, `sampleVariant()`, `blankResume()`, `SAMPLES` | `cvb:resumes` |
| State + versions | `changed()`, `saveNow()`, `switchResume()`, `renameVersion()`, `duplicateVersion()`, `deleteVersion()`, `newVersion()` | `cvb:resumes`, `cvb:ui` |
| Editor | `renderEditor()`, `sectionBody()`, `itemHTML()`, `bulletsHTML()`, `sortable()`, `editorAction()`, `openSamplePicker()`, `loadSample()` | `cvb:resumes`, `cvb:ui` |
| Content model | `blocks()` + `BLOCK_BUILDERS`, `secTitle()`, `urTitle()`, `resumeHTML()`, `modernHTML()`, `toTXT()`, `toMD()` | — |
| Preview + CV language | `renderPreview()`, `applyZoom()`, `renderPvbar()`, `setCvLang()`, `nameHTML()` | `cvb:resumes` (`lang`), `cvb:ui` |
| Top bar + navigation | `renderTopbar()`, `renderSubnav()`, `renderPill()`, `setTemplate()`, `togglePop()` | `cvb:ui` |
| Export / import | `runExport()`, `printPage()`, `download()`, `importResume()`, `exportDocx()` / `shareLink()` / `importJsonResume()` stubs | — |
| ATS engine + drawer | `extractKeywords()`, `keywordMatch()`, `analyse()` with `check*()` rules, `renderATS()`, `gotoSection()` | `cvb:ui` (`jd`) |
| Bullet improver | `pastOf()`, `verbSuggestion()`, `suggestBullet()`, `openImprover()`, `aiRewrite()` stub | — |
| Cover letter | `genLetter()`, `renderCover()`, `renderLetter()` | `cvb:resumes` (`cover`) |
| Activity + notifications | `logEvent()`, `renderBell()`, `renderBellPop()`, `renderActivity()`, `checkScoreMilestone()` | `cvb:log` |
| Settings + backup | `renderSettings()`, `exportEverything()`, `importEverything()`, `applyBackup()`, `resetAllData()`, `storageUsage()` | `cvb:settings` (+ all keys) |
| Application tracker | `seedApps()`, `renderTracker()`, `boardHTML()`, `bindBoardDnD()`, `setStage()`, `openAppForm()`, `openAppDetail()`, `versionStats()`, `followUpNotifications()`, `exportInterviewICS()` stub | `cvb:apps` |
| Skills gap | `ROLE_PROFILES`, `LEARN`, `ngramSet()`, `gapAnalysis()`, `learningPlan()`, `versionsChartSVG()`, `renderSkills()` | `cvb:gap`, `cvb:ui` |
| Interview prep + STAR | `QUESTION_BANK`, `rankBullets()`, `draftFromBullet()`, `answerMeters()`, `renderInterview()`, `startPractice()`, `exportAnswers()` | `cvb:star`, `cvb:questions`, `cvb:ui` |
| LinkedIn generator | `profileFacts()`, `topSkills()`, `achievements()`, `headlineVariants()`, `aboutText()`, `liChecklist()`, `renderLinkedIn()` | `cvb:linkedin` |
| Portfolio one-pager | `portfolioHTML()`, `portfolioCSS()`, `openPortfolioPreview()` | `cvb:ui` (`portfolio`) |
| Onboarding tour | `TOUR`, `startTour()`, `showTourStep()`, `placeTourCard()`, `endTour()`, `welcomeHTML()` | `cvb:settings` (`tourDone`) |
| About + router | `TODOS`, `MODULES`, `renderAbout()`, `route()`, `renderAll()`, `recruiterView()` stub | — |

## Architecture (single file)

```
index.html
├─ <style>      tokens (warm white / charcoal, Lora + Karla + Noto Nastaliq Urdu), motion kit, top bar, editor accordion,
│               A4 page + 6 template skins + Urdu/bilingual rules, ATS drawer, modal, cover letter, about,
│               sub-navigation, module pages (kpis, kanban, skills, STAR, LinkedIn, settings, activity), tour,
│               responsive, @media print (resume, letter, or application report)
├─ <svg>        inline icon sprite (73 symbols used by ic('name'))
├─ <header>     brand, version switcher, template segmented control, ATS pill, Export menu, Cover letter, bell, About
├─ <nav>        sub-navigation: Editor · Cover letter · Applications · Skills gap · Interview prep · LinkedIn · Settings · Activity · About
├─ .mobilebar   Editor | Preview toggle + template select (≤ 900 px, editor and cover pages)
├─ <main #view> #view-edit · #view-cover · #view-tracker · #view-skills · #view-interview · #view-linkedin
│               · #view-settings · #view-activity · #view-about
├─ <aside>      ATS drawer (#ats route) · modal · tour spotlight + card · toasts
└─ <script>
   ├─ utils          $, store (cvb: prefix), esc, ic(), toast, debounce, paths, dates, animateIn, countUp
   ├─ constants      TEMPLATES (6), ACCENTS, PAPERS, SECTIONS (+ Urdu titles), CV_LANGS, UR_HEADINGS, ITEM_DEFS,
   │                 STRONG / WEAK / UPGRADE / IRREG verb tables, STOP words, PERSONAL_RX, STD_HEADINGS
   ├─ sample profiles 5 data constants + withIds() factories, blankResume(), SAMPLES registry
   ├─ state          resumes[], ui, R (current), lang migration, changed() → save + preview + score
   ├─ editor         renderEditor(), sections, items, bullets, sortable(), editorAction(), sample picker
   ├─ content model  BLOCK_BUILDERS → blocks(r) → resumeHTML() · toTXT() · toMD() (portfolioHTML() reuses the model)
   ├─ preview        renderPreview() (page count, breaks, @page, dir="auto" in Urdu mode), zoom, language select
   ├─ top bar        sub-navigation, template switch, score pill, popovers, version actions
   ├─ export         export menu, print, downloads, JSON import/normalise, stubs
   ├─ ATS engine     keywords, eleven check*() rules, analyse(), drawer panels
   ├─ improver       pastOf(), suggestion helpers, suggestBullet(), openImprover()
   ├─ modals         modalOpen/Close, confirm, prompt, text fallback
   ├─ cover letter   genLetter(), renderCover(), renderLetter()
   ├─ activity       logEvent(), bell popover, activity page, score milestone
   ├─ settings       defaults, backup export/import/merge, reset, storage meter, shortcuts
   ├─ tracker        apps model + seed, board + DnD, stage changes, form, detail, version stats, reminders, report
   ├─ skills gap     role profiles, learning resources, n-gram evidence, plan, SVG chart
   ├─ interview      question bank, bullet ranking, STAR drafting, meters, practice timer
   ├─ linkedin       facts, top skills, headline formulas, About tones, checklist
   ├─ portfolio      standalone HTML generator + sandboxed preview
   ├─ tour           spotlight steps, placement, welcome card
   ├─ about          TODOS[], MODULES[], renderAbout()
   └─ router + init  #edit · #ats · #cover · #tracker · #skills · #interview · #linkedin · #settings · #activity · #about (· #r/<id> stub)
```

Data flow: every renderer reads the plain JSON resume `R`. `blocks(R)` shapes it once (title + para / entries / lines / inline / skills, with Urdu titles attached) and the HTML, TXT and Markdown renderers consume the same shape, so a new section type is one entry in `BLOCK_BUILDERS` plus a field schema in `ITEM_DEFS`. The job-search modules read the same model: the tracker links applications by resume id, the skills gap and LinkedIn generator read bullets and skills, and the STAR builder drafts from bullets.

## Domain logic notes

- **ATS-safe by construction.** Single linear DOM (Modern renders main content first, then the sidebar), semantic headings, `<ul>` bullets, no tables or images carrying text, links as visible text. The Bold header band is a CSS background behind real text. Print uses `@page { size: A4|Letter; margin: <template margin> }`, removes the preview scale and hides every other part of the app.
- **Keyword extraction.** Job description → tokens → stop-words removed → unigram and bigram frequencies; bigrams seen twice or more win over their words; weights `min(freq, 4)` (×1.5 for bigrams); top 40 kept; plural- and dot-insensitive matching against the plain-text export.
- **Score.** Ten structure checks worth 83 points (contact 10 · headline 5 · summary 8 · action verbs 12 · quantified bullets 10 · bullet length 6 · dates 6 · no photo/CNIC/age/marital status 12 · standard headings 6 · pages vs experience 8) plus keyword match 17 when a job description is pasted; otherwise scaled to 100.
- **Bilingual / Urdu mode.** Headings come from `SECTIONS[type].ur` (custom headings through `UR_HEADINGS`). Urdu mode sets `dir="rtl"` on the page, uses Karla first and Noto Nastaliq Urdu as fallback (so Latin keeps its face and Urdu glyphs render in Nastaliq), and gives paragraphs, bullets and entries `dir="auto"`; bullets use `margin-inline-start` so markers follow each line's own direction. Most ATS portals still expect English, and the drawer says so.
- **Skills gap.** Each role profile lists core skills (weight 1–3) and nice-to-haves with aliases. Evidence is looked up in uni/bi/trigram sets of five resume regions. A skill shown in an experience or project bullet counts fully; one only listed in Skills, the summary or certifications counts 70 %, nudging students to prove claims. Match % = weighted evidence ÷ core weight. The plan orders missing core skills by weight, then nice-to-haves, and packs resource hours into weeks at the chosen hours per week.
- **STAR drafting.** Questions carry keywords; bullets are ranked by stem overlap (first five letters) plus a bonus for numbers. A bullet is split at its first comma / semicolon / dash into action and result; a gerund result ("cutting time by 38 %") becomes "I cut time by 38 %". Situation and Task get bracketed prompts the student must fill. Meters assume 130 spoken words per minute; 120–300 words is the target.
- **Tracker reminders.** An application in *applied* for at least the configured number of days (default 10) raises one follow-up notification per day; interviews within three days and saved jobs closing within two days raise reminders too. Interview rate per version = applications that reached interview ÷ applications sent.
- **LinkedIn.** Top skills are ranked by how often each appears in bullets and stacks; achievements are bullets with numbers, ranked by count of numbers plus a bonus for %, Rs, users and installs. Headlines are cut to LinkedIn's 220 characters; the About meter uses its 2,600 limit and the note reminds that ~300 characters show before "see more".

## Left for students

| Extension | Difficulty | Start here |
|---|---|---|
| AI bullet rewriting with a bring-your-own API key (key stored locally, 3 variants as suggestion cards, rule engine as fallback) | Medium | `aiRewrite()` |
| DOCX export with real Word heading styles (generate `document.xml` from `blocks()`, zip it) | Hard | `exportDocx()` |
| LinkedIn data-export / JSON Resume import (map basics, work, education, skills) | Medium | `importJsonResume()` |
| Backend accounts and shareable read-only links (REST API, auth, signed URLs rendering `resumeHTML()`) | Hard | `shareLink()` |
| Recruiter view with highlights and comments stored per bullet id | Medium | `recruiterView()` and `route()` |
| Synonym-aware keyword matching (stemmer + tech-synonym table or small embeddings) | Medium | `keywordMatch()` |
| Calendar export for interview dates and deadlines (`.ics` VEVENT + Google Calendar link) | Easy | `exportInterviewICS()` |
| Voice practice: record answers with MediaRecorder, store in IndexedDB, play back, count filler words | Medium | `startPractice()` |

Search the source for `TODO(student)` to find the exact hooks.

## Data & safety notes

Nothing leaves the browser: resumes, applications, interview answers, LinkedIn drafts, the activity log and settings are stored only in `localStorage`; the portfolio preview runs in a sandboxed iframe with no scripts, and the AI, sharing, import and calendar stubs are deliberately not wired to any network service. The sample people, employers, applications and numbers are fictional and exist only to make the screens look real. Role profiles, weights and learning resources are hand-curated and illustrative, not labour-market data. The ATS checks encode widely published recruiter guidance; they approximate real parsers and should be validated against the portals a department's graduates actually use (Rozee.pk, LinkedIn Easy Apply, Workday, Greenhouse). Urdu CVs are for employers that ask for them; keep an English version for ATS portals.

## Evaluation ideas for the FYP report

Upload exported PDFs to two or three real ATS demo accounts and compare parsed fields with the JSON model; measure keyword-match uplift after using the fix list on 20 student CVs; run think-aloud sessions with final-year students on the bullet improver; A/B the Classic and Modern templates through a parsing API. For the new modules: track interview rate per resume version across a placement season, compare skills-gap results with what employers ask in interviews, and time students answering three behavioural questions before and after using the STAR builder.
