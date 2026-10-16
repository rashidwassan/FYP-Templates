# 09 · Board Exam Revision Planner (idea #26)

**Study the chapters that carry the marks, and come back to them before you forget.** A bilingual (English / اردو) revision planner for Matric (9–10) and FSc (11–12) students of Pakistani boards: per-board chapter weightage heatmaps from a seeded past-paper bank, an interleaving week planner, Pomodoro timer, spaced-repetition reviews, flashcards, a mock-exam generator, a datesheet with exam-day checklist, printable reports and a readiness score.

**Scale:** 4,409 lines in one `index.html` (376 CSS, ~3,900 JS) · 11 routed modules + tour, search and inbox engine · seed data: 44 subject syllabi / 395 chapters, 22 boards in 6 provinces, a deterministic bank of 5 tagged papers per subject per board (4,840 papers), 56 flashcards, 10 checklist items, 12 stream combinations, plus a demo profile with 18 sessions, 5 papers, 6 reviews and 1 marked mock.

## Getting started

Open `index.html` in any modern browser (Chrome, Edge, Firefox, Safari). No build step, no server, no dependencies beyond Google Fonts (Plus Jakarta Sans + Noto Nastaliq Urdu; system fonts are used offline).

- Browser notifications from the Pomodoro timer need the page served over HTTP(S) (`npx serve .`) or a browser that allows them from `file://`.
- All data lives in `localStorage` under `berp:*` keys. **Settings → Data** exports a JSON backup, imports one, resets to the demo profile or starts blank.
- First open loads a demo profile (12th class, Pre-Medical, BISE Lahore, exam in March 2027) and starts a six-step onboarding tour. Replay it from Settings.

## The problem

Matric and FSc students cram without a plan, do not know which chapters carry the most marks in their board's papers, and rarely revisit what they studied. Marks are lost on high-weightage chapters that were studied once, early, and forgotten.

## What is built

| Area | Features |
|---|---|
| Plan | Interleaved week grid (at most 2 sessions of one subject per day), priority = weightage × difficulty × (1 − mastery/5) × weak-paper boost, last 7 days reserved for revision, drag-and-drop between days, custom sessions, computed start times, `.ics` export, printable week, KPIs with animated numbers, 14-day streak dots |
| Today / timer | Pomodoro timer that survives route changes (focus blocks + breaks, beep, Web Notification), rating modal updates mastery and schedules a review |
| Subjects | Readiness rings per subject, what-if slider (hours/day → projected readiness and coverage), per-board weightage heatmaps, chapter editor (weightage, difficulty, mastery, reset to bank default) |
| Revise | SM-2-lite queue: ladder +1, +3, +7, +14, +30 days; ratings 1–2 reset, 3 shortens, 4 keeps, 5 lengthens; overdue highlighting; upcoming list |
| Cards | Leitner flashcards per chapter (boxes 1–5, due in 1, 2, 4, 7, 15 days), flip card with keyboard support, practice all due, add/delete cards; a run of 5+ cards rates the chapter review from recall |
| Mock | Mock-paper generator (board / half / quick patterns for FSc and Matric): MCQ and short questions allocated proportionally to weightage, long questions from distinct chapters, countdown timer, self-marking (correct/wrong, zero/half/full or exact marks), per-chapter result, mastery update, "log as paper attempt", history, printable paper |
| Papers | Past-paper attempt log with weak-chapter tags, SVG score trend with tooltips, weak-chapter frequency, one-click extra sessions, seeded paper bank matrix (year × chapter marks) for any of the 22 boards |
| Exams | Editable datesheet per subject (header countdown and planner follow the first paper), gap days, final-week focus chapters, exam-day checklist with custom items and tips |
| Reports | Printable weekly report (minutes-per-day bar chart, sessions by status, minutes by subject, most-studied chapters) and a one-page guardian summary with remarks and signature lines |
| Inbox | In-app notifications with read state (reviews due, exam milestones, paper within 3 days, streaks, unfinished sessions, session/paper/mock/practice events) and a full activity log |
| Settings | Study profile (name, board grouped by province, class, stream, hours, days off, Pomodoro, start times), language, light/dark/system theme, timer sound, animations toggle, keyboard-shortcut sheet, export/import/reset/blank |
| Global | Hash routing, onboarding spotlight tour, global search (`/`) over pages, chapters and cards, keyboard shortcuts, English/Urdu dictionary (339 keys each), print CSS, zero-overflow layouts at 1366 and 390 px |

## Module map

| Module | Key functions | Storage keys |
|---|---|---|
| Utils and i18n | `store`, `esc`, `ic`, `hashStr`, `mulberry32`, date helpers, `toast`, `countUp`, `ringSet`, `growBars`, `t`, `tf` | `lang` |
| Syllabus and boards | `SUBJ`, `STREAMS`, `COMBOS`, `BOARDS`, `subjects()`, `chapters()`, `chById()` | – |
| Paper bank | `paperTotal`, `distributeMarks`, `paperBank`, `bankWeight`, `fetchPaperBank` (stub) | – (derived from hashes) |
| State | `saveAll`, `W`, `D`, `M`, `setMastery`, `seedDemo`, `seedDemoExtras`, `seedBlank`, `importBackup` | `settings`, `prefs`, `mastery`, `overrides` |
| Planner | `priority`, `buildDays`, `buildQueues`, `fillStudyDays`, `fillRevisionDays`, `generatePlan`, `sessionTimes`, `addSession`, `moveSession`, `completeSession`, `exportIcs` | `plan`, `log` |
| Spaced repetition | `scheduleReview`, `rateReview`, `dueReviews`, `overdueCount` | `reviews` |
| Readiness and stats | `subjReadiness`, `overallReadiness`, `projection`, `streak`, `minutesBySubject`, `weekStats`, `focusChapters` | – |
| Timer | `timerStart`, `timerResume`, `timerPause`, `timerEnd`, `renderTimer`, `beep`, `notify` | – |
| Plan view | `renderPlan`, `renderWeek`, `dayHtml`, `sessHtml`, `renderTodayList`, `openSession`, `openRating`, `openAddSession` | `plan` |
| Subjects view | `renderSubjects`, `tileHtml`, `whatIfHtml`, `openChapter`, `heatColor` | `overrides`, `mastery` |
| Revise view | `renderRevise`, `reviewRowHtml` | `reviews` |
| Flashcards | `renderCards`, `deckListHtml`, `startPractice`, `answerCard`, `finishPractice`, `bindAddCard` | `cards` |
| Mock exam | `MOCK_PATTERNS`, `allocateByWeight`, `generateMock`, `finalizeMock`, `logMockAsPaper`, `renderMock`, `mockTick`, `bindMarking` | `mocks`, `papers` |
| Papers view | `renderPapers`, `addPaperFromForm`, `weakFreqHtml`, `bankMatrixHtml`, `scoreChart` | `papers` |
| Exams | `ensureDatesheet`, `firstExamDate`, `defaultChecklist`, `renderExams`, `bindExams` | `datesheet`, `checklist`, `settings.examDate` |
| Reports | `barChart`, `weeklyReportHtml`, `guardianReportHtml`, `renderReports` | `settings.guardianNote` |
| Inbox | `pushInbox`, `dailyNotices`, `inboxUnread`, `renderInbox`, `activityHtml` | `inbox`, `log` |
| Settings | `renderSettings`, `saveProfile`, `bindPrefs`, `bindData`, `applyTheme`, `applyMotion` | `settings`, `prefs` |
| Tour, search, router | `tourStart`, `tourShow`, `tourEnd`, `openSearch`, `searchItems`, `onKeydown`, `route`, `applyLang`, `init` | `tourDone`, `lang` |

## Architecture (single file)

```
index.html
├─ <style>      tokens (indigo/violet on warm off-white + dark theme), motion kit, app bar, week grid,
│               timer, heatmap, flashcards, mock, datesheet, inbox, reports, search/tour, modal, print
├─ <svg>        icon sprite (Lucide-style symbols used by ic(name))
├─ <header>     brand, exam countdown (→ #exams), inbox bell with badge, language toggle, nav, search
├─ <main #view> rendered by one function per route
└─ <script>
   ├─ utils            $, store (namespaced localStorage), esc, hashStr/mulberry32, dates, toast, motion helpers
   ├─ i18n             TXT.en / TXT.ur, t(), T(), tf()
   ├─ syllabus seed    SUBJ (44 subjects), STREAMS, COMBOS, BOARDS (22, by province)
   ├─ paper bank       deterministic 5-year tagged papers per subject × board → bankWeight()
   ├─ flashcard seed   CARD_SEED
   ├─ state & seed     settings/prefs/mastery/... loaders, saveAll, seedDemo, seedBlank
   ├─ planner          priority, buildDays, buildQueues, fillStudyDays, fillRevisionDays, generatePlan
   ├─ spaced repetition, readiness & statistics, datesheet/checklist defaults, inbox engine
   ├─ modal · Pomodoro timer · session/rating/add-session modals
   ├─ views            renderPlan, renderSubjects, renderRevise, renderCards, renderMock, renderPapers,
   │                   renderExams, renderReports, renderInbox, renderSettings, renderAbout
   ├─ exports          exportIcs
   ├─ tour · search · shortcuts · theme
   └─ router           NAV, ROUTES, route(), applyLang(), init()
```

## Domain logic notes

- **Weightage per board.** For every subject and board the bank generates five papers (2021–2025). Each paper splits the theory total (85 for FSc science, 60 for Matric science, 100/75 otherwise) across chapters as `base × province emphasis (0.85–1.15) × yearly jitter (0.8–1.2)`, both derived from string hashes, then fixes rounding so marks add up. The heatmap shows the mean share; an edited chapter overrides it. Changing the board in Settings changes the heatmap.
- **Planner.** Slots per day = floor(capacity / (2 × Pomodoro)). Each chapter receives `round(slots × priority / Σpriority)` sessions (at least one), queued per subject round-robin; each slot takes the subject with the most remaining demand, capped at two per subject per day. The first session of a paper-flagged weak chapter is tagged *extra*. Completed and custom sessions are kept when regenerating.
- **Mastery (0–5)** moves 50 % towards a session's confidence rating, 30 % towards a review rating, 30 % towards a mock chapter score and 20 % towards a flashcard run. Readiness = Σ(weightage × mastery/5) / Σweightage.
- **What-if.** Minutes needed = Σ(5 − mastery) × difficulty × 25; coverage = available / needed; projected readiness fills 90 % of the remaining gap at full coverage.
- **Spaced repetition.** Ladder [1, 3, 7, 14, 30] days with multipliers 0.8 / 1.1 / 1.3 for ratings 3 / 4 / 5; ratings ≤ 2 reset the step. A flashcard run of 5+ cards from one chapter converts recall ratio to a rating of 1–5 and rates the review.
- **Mock papers.** Question counts per chapter follow largest-remainder allocation of the weightages, shuffled with a seeded PRNG (the seed is stored, so a paper can be regenerated). Weak = chapters with ≥ 4 marks and < 50 % scored.

## Left for students

| Extension | Difficulty | Start here |
|---|---|---|
| Real past-paper database (board papers tagged per question → chapter, weightage = marks share over 5–10 years) | ★★ | `fetchPaperBank()` |
| OCR / import a paper PDF (Tesseract.js) and auto-tag questions to chapters | ★★★ | `renderPapers()` |
| Cloud sync + study groups with a weekly minutes leaderboard (Supabase/Firebase) | ★★ | `renderSettings()` |
| Push notification backend (service worker + Web Push) for session and review reminders | ★★ | `timerStart()` |
| ML weightage and difficulty estimation from paper history and confidence ratings | ★★★ | `estimateWeightagesML()` |
| Live guardian link: publish the guardian summary to a backend with a read-only share code | ★★ | `renderReports()` |
| Real question bank for mocks: actual questions with answer keys, auto-marked MCQs, model answers | ★★★ | `generateMock()` |
| Two-way calendar sync (Google Calendar API) instead of the one-shot `.ics` | ★★ | `exportIcs()` |

Search the source for `TODO(student)` to find the exact hooks. The About page lists the same eight items.

## Data and safety notes

Chapter lists follow Punjab Textbook Board editions; Sindh, KP, Balochistan, Federal and AJK boards share the chapter structure with minor differences. Weightages, difficulties and the paper bank are **illustrative** and deterministic: they exist so the demo is realistic and reproducible, not as a statement about any board's actual papers. Edit them per chapter or replace them with a real database before relying on the heatmap. Everything stays in the student's browser; nothing is uploaded.

## Evaluation ideas for the FYP report

Compare the seeded weightages with tagged real papers of one board over five years; run a two-week usability study with 8–10 students in Urdu mode; measure whether the planner's interleaving and spaced reviews improve mock scores against a self-made timetable.
