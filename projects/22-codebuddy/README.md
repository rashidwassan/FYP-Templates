# 22 · CodeBuddy — Code Mentor for Kids and Beginners (idea #87)

**A friendly robot called Buddy teaches children in Pakistan to code — in JavaScript or Python, in English or Urdu, online or offline.** One HTML file holds a kid-sized IDE (own syntax highlighter, auto-indent, bracket matching, find), a sandboxed JavaScript runner, full Python through Pyodide with an offline mini-Python fallback, turtle graphics, a step-through visualiser, blocks that turn into real code and back, plain-language error explanations with one-click fixes, 24 guided lessons and 5 mini projects whose goals are checked automatically, an offline mentor with an optional bring-your-own-key Live AI mode, badges, streaks, a local teacher view with class codes and printable certificates.

**Scale:** ≈ 9,650 lines in one `index.html` (≈ 8,640 lines of JavaScript, ≈ 840 of CSS) · 21 modules · content: 24 lessons + 5 mini projects written in both JavaScript and Python with 70 auto-checked goals, 40 bilingual error explanations, 17 mentor concepts, 17 badges, 502 UI strings in English and Urdu · seed records: 11 learners in 2 classes, 5 inbox messages, 4 activity-log entries, 3 playground programs, 1 half-finished lesson with a deliberate bug · 84 sprite icons · 8 student TODOs.

## Getting started

- Open `index.html` in Chrome, Edge or Firefox. It works from `file://` with no build step and no server. Fonts (Lexend, JetBrains Mono, Noto Nastaliq Urdu) come from Google Fonts and fall back to system fonts offline.
- First open loads **sample data** (Settings → Data → Sample data): the learner *Ayesha Khan* is on lesson 8, whose code contains a typo (`nam`) so you can see an error explained in English and Urdu and fixed with **Apply fix**. The onboarding tour starts automatically on the first visit without a hash; replay it from Settings → Help.
- **JavaScript** always runs in a sandboxed Web Worker. **Python** runs in Buddy's built-in **mini-Python** (instant, offline) by default; Settings → Running → *Download full Python* (or the engine chip in the status bar) lazy-loads Pyodide 0.29.5 from `https://cdn.jsdelivr.net/npm/pyodide@0.29.5/` (about 10 MB, once). If it cannot be downloaded the run falls back to mini-Python and an offline notice explains why.
- **Live AI** is off. To try it: Settings → Live AI → paste your own Claude API key, pick a model, *Test*, enable. The key stays in `sessionStorage` unless you tick *Remember on this device*.
- **Where data lives:** `localStorage` keys prefixed `cb:` (see the module map). **Reset:** Settings → Data → *Reset everything*, or in the console `Object.keys(localStorage).filter(k => k.startsWith('cb:')).forEach(k => localStorage.removeItem(k))`.
- Keyboard: `Ctrl+Enter` run · `Esc` stop/close · `F8` / `Shift+F8` step · `Ctrl+F` find · `Ctrl+/` comment · `Alt +`/`Alt -` code size · `Ctrl+K` search · `L` language · `1`–`7` pages · `?` shortcut sheet.

## The problem

Many Pakistani children meet programming in crowded computer labs with English-only tools, no internet at home and error messages that read like a wall (`TypeError: can only concatenate str (not "int") to str`). Teachers juggle forty pupils on a few shared PCs and cannot see who is stuck. CodeBuddy turns the computer into a patient tutor: every lesson has small goals that are checked automatically, every error is explained in plain English *and* Urdu with the line highlighted and a suggested fix, children can watch their program run one line at a time, younger pupils can start with blocks, and a teacher sees a whole class on one machine — all without an account or a server.

## What is built

| Area | Features |
|---|---|
| Editor | Transparent `<textarea>` over a highlighted layer driven by **its own tokenizer** (keywords, builtins, function calls, strings, f-strings/templates, numbers, comments, unterminated strings underlined); line numbers with current/error/step markers; auto-indent (after `{`, `:`; dedent after `return`/`pass`); auto-close of brackets and quotes with wrap-selection and pair delete; **bracket matching**; Tab/Shift+Tab block indent; `Ctrl+/` comment toggle; **find** bar with match count and next/previous; font size 12–24 px; autosave per learner, lesson and language; answers box for `ask()`/`input()` |
| JavaScript runner | Code compiled with `new Function` inside a **Blob-URL Web Worker**; `fetch`, `XMLHttpRequest`, `WebSocket`, `importScripts`, `indexedDB` locked; captured `console.*` (4,000-line cap) with Node-style value formatting; `ask()`/`prompt()` fed from the answers box; seedable `Math.random`; **time limit kills infinite loops** (worker terminated, the never-changing `while` line is guessed); runtime error lines mapped from the V8 stack, syntax-error lines from Buddy's parser; Stop button |
| Python runner | **Pyodide 0.29.5** loaded only on request in its own worker (stdout/stdin bridged, fresh globals each run, seed support, time limit by terminating the worker), traceback parsed into type/message/line; **offline notice** and automatic fallback to **mini-Python**; engine chip and settings (Auto / Mini / Full) |
| Turtle graphics | Same command set from both languages (`forward`, `left`, `circle`, `color`, `penup`, `goto`, `dot`, `write`, `begin_fill`…); commands replayed on a canvas with Python turtle's exact circle algorithm, fills, dots, text, grid paper, speed (slow → instant), replay, PNG export; Pyodide gets a real `turtle` module that forwards to the same replay |
| Step-through visualiser | Records up to 1,500 steps with the mini interpreter, then scrubs back and forth (buttons, slider, play/pause with speed); current line highlighted in the editor; **variables table** with added/changed rows flashing, globals vs. locals; **call stack**; **loop counters** with “round 3 of 5” progress; “Line 6 ran / next line” narration; output-so-far and partial turtle drawing; stale-code warning |
| Blocks ↔ code | Palette of 11 blocks (move, turn, circle, pen colour, pen up/down, repeat, if/else, set variable, change variable, print, ask); add by **drag-and-drop or tap a slot then tap a block**; reorder/delete; nested repeat/if/else; live **JavaScript or Python** generation; **code → blocks** for the supported subset (reports skipped lines); run blocks with turtle + console; send to the playground |
| Explain this error | 40-entry catalogue matching V8, CPython and mini-Python messages; English + Urdu title, meaning and fix; “did you mean …?” via edit distance; **one-click fixes** (rename typo, add colon, `=`→`==`, print brackets, close quote/bracket, indent/dedent, `const`→`let`, drop duplicate `let`, `str()` wrap, `int(input())`, `push`→`append`, add `global`); error line highlighted; error dictionary page with broken/fixed examples and “Try it” |
| Lessons | 24 lessons in 6 units (output, variables, decisions, loops, functions, lists), each in JavaScript **and** Python with starter code, reference solution, 2–3 goals checked automatically (output text/regex, line count, code pattern, max uses, turtle lines and turning, number of questions asked, colours used), progressive hints and a guarded “show solution” |
| Mini projects | Turtle-art rainbow flower, quiz app, number guessing, times-table trainer, simple calculator — steps, goals, starter + solution in both languages |
| Mentor | Offline rule-based Buddy: intents (hint, why/error, explain line N, concept, solution request, greeting, thanks) in English, Roman Urdu and Urdu; uses the last run, goal results, lesson hints, the error explainer and a **lint** pass (missing colon, `=` in conditions, capitalised builtins, curly quotes, unbalanced brackets, uncalled functions, never-ending `while`, `input()` used as a number); never pastes full solutions; SVG mascot with moods |
| Live AI (optional) | Bring-your-own-key Claude mentor with a kid-safe system prompt, streaming replies, refusal handling, token estimate and exact `countTokens` in the test, error chain with offline fallback (contract below) |
| Progress | KPIs, unit bars, 12-week heatmap, 3-week activity line, coding stats, 17 badges, current and best streak, recent wins, join/leave a class by code, printable progress report and **certificate** |
| Teacher view | Local classes with `CB-XXXX` codes, roster with lesson/unit/project progress, streaks and last activity, per-lesson completion bars, “needs a helping hand” list (idle 7+ days or many errors), add learners, open as learner, certificates, CSV export, printable class report, optional teacher PIN |
| Platform | English/Urdu (Nastaliq, RTL layout, code stays LTR), paper/chalkboard themes, large text, reduced motion, demo-data toggle, JSON export/import (API key excluded), reset, multiple learner profiles, inbox + activity log, `Ctrl+K` global search, shortcut sheet, onboarding spotlight tour, aria-live output and announcements, print CSS |

## Module map

| Module | Key functions | Storage keys |
|---|---|---|
| Lexer + highlighter | `scanCode`, `stringValue`, `tokenClasses`, `highlightHTML` | — |
| Parser (JS + Python → one AST) | `Parser`, `pyTokens`, `jsTokens`, `pyStatement`, `jsStatement`, `expr`, `templateParts`, `parseProgram` | — |
| Mini interpreter | `Machine` (`run`, `exec`, `eval`, `callUser`, `snapshot`), `pyBin`, `jsBin`, `installBuiltins`, `runMini`, `traceMini` | — |
| Editor | `CodeEditor` (`render`, `newline`, `typePair`, `matchBracket`, `shiftLines`, `toggleComment`, `setFind`, `findStep`) | `cb:settings.fontSize/autoClose` |
| Runners | `jsWorkerSource`, `runJsWorker`, `jsErrorLine`, `pyWorkerSource`, `loadPyodideEngine`, `runPyodide`, `runProgram` | `cb:settings.pyEngine/timeLimit/seed` |
| Goals | `checkGoal`, `checkTurtleGoal`, `evaluateGoals`, `printedText` | `cb:profiles[].done` |
| Turtle | `buildTurtleModel`, `TURTLE_OPS`, `turtleCircle`, `TurtleCanvas` | `cb:settings.turtleSpeed/turtleGrid` |
| Explain + lint | `explainError`, `findCatalogEntry`, `closestName`, `AUTOFIX.*`, `lintCode`, `guessLoopLine` | — |
| IDE | `openDoc`, `docMeta`, `renderRail`, `runCurrent`, `showRunResult`, `renderErrorCard`, `renderResultCard`, `recordRun` | `cb:code:<pid>` (code + answers per doc and language) |
| Playground | `newProgramModal`, `renameProgram`, `deleteProgram`, `ensurePlayProgram` | `cb:play:<pid>` |
| Visualiser | `startVisualiser`, `showStep`, `describeChanges`, `varsTableHTML`, `togglePlay` | `cb:profiles[].stats.steps` |
| Mentor | `offlineReply`, `detectIntent`, `describeLine`, `replyHint`, `mentorAsk`, `renderBuddyPane` | `cb:chat:<pid>` |
| Live AI | `loadAiSdk`, `aiClient`, `buildAiParams`, `buildAiMessages`, `aiCreate`, `aiStream`, `aiReadMessage`, `aiErrorInfo`, `aiCountTokens`, `aiTestConnection` | `cb:settings.ai`, key in `sessionStorage cb:aikey` (or `cb:aikey` when remembered) |
| Blocks | `blocksToCode`, `translateExpr`, `codeToBlocks`, `exprSource`, `insertBlock`, `dropOnSlot`, `runBlocks` | `cb:blocks:<pid>` |
| Projects + progress | `renderProjects`, `renderProgress`, `activityLineSVG`, `certificateHTML`, `progressReportHTML`, `profileModal` | `cb:profiles`, `cb:active` |
| Badges + streaks | `BADGES`, `awardBadges`, `markActivity`, `streak`, `bestStreak` | `cb:profiles[].badges/days/stats` |
| Teacher | `renderTeacher`, `classMembers`, `rosterTableHTML`, `lessonHeatSVG`, `needsHelp`, `classCSV`, `classReportHTML` | `cb:classes`, `cb:settings.teacherPin` |
| Inbox + log | `addInbox`, `visibleInbox`, `renderInbox`, `addLog`, `updateBell` | `cb:inbox`, `cb:log` |
| Search + error dictionary | `searchIndex`, `openSearch`, `renderErrorsPage`, `sampleExplanation` | — |
| Settings + data | `renderSettings`, `applyPrefs`, `setLanguage`, `exportData`, `importData`, `resetAll`, `toggleDemo` | `cb:settings`, `cb:seeded` |
| Tour, shortcuts, about | `startTour`, `positionTour`, `onGlobalKey`, `openShortcuts`, `renderAbout` | `cb:tour` |

## Architecture

```
index.html
├─ <style>      tokens (paper + chalkboard), Urdu/RTL rules, top bar, buttons/fields/chips, Buddy mascot,
│               IDE grid (rail · editor · side), editor layers + token colours, console, turtle card,
│               error/result cards, visualiser, chat, blocks, projects/progress/teacher, inbox/settings,
│               modal/toast/tour/search, certificate, motion kit, responsive (1240 / 920 / 420), print
├─ <svg>        84-symbol icon sprite (kit icons + bug, repeat, turtle, terminal, robot, wifi-off, puzzle, pen…)
├─ <header>     brand, nav, search, inbox, language, settings, learner avatar
├─ <main>       IDE section (rail, toolbar, find bar, editor, answers, status bar, Output/Steps/Buddy tabs)
│               + page sections: projects · blocks · progress · teacher · errors · inbox · settings · about
└─ <script>
   ├─ utils            $, store (cb: prefix), session, esc, ic, toast, modal, confirmModal, printHTML,
   │                   animateIn, countUp, growBars, drawPath, buddySVG (5 moods)
   ├─ i18n             t(), I18N.en / I18N.ur (502 keys each)
   ├─ content          UNITS, LESSONS (24), PROJECTS (5), GLOSSARY (17), ERROR_CATALOG (40)
   ├─ language core    lexer · parser (Python indent/dedent, JS ASI-lite) · mini interpreter (generators,
   │                   PyFloat, scopes, UnboundLocalError, recursion limit) · builtins · methods · run/trace
   ├─ editor           CodeEditor (render, keys, find)
   ├─ turtle           buildTurtleModel, TURTLE_OPS, TurtleCanvas
   ├─ runners          JS worker, Pyodide worker + turtle shim, runProgram, goals, loop-line guess
   ├─ explain + lint   explainError, AUTOFIX, lintCode
   ├─ state + seed     settings, profiles, progress, badges, streaks, inbox, classes, seedDemo
   ├─ IDE              documents, rail, toolbar, engine modal, more tools, run flow, cards, find
   ├─ visualiser       trace, controls, narration, tables
   ├─ mentor + AI      offline engine, chat pane, Live AI adapter
   ├─ blocks           model, generator, code→blocks, page UI with drag-and-drop/tap
   ├─ pages            projects, progress + certificate, teacher, errors dictionary, inbox, search, settings
   └─ shell            tour, shortcuts, about + TODO list, top bar, hash router (#learn/<id> · #project/<id> ·
                       #play/<id> · #projects · #blocks · #progress · #teacher · #errors · #inbox · #settings · #about)
```

## Domain logic / algorithm notes

- **One parser, two languages.** `scanCode` is a tolerant scanner shared by the highlighter and the parser. For Python, `pyTokens` turns lines into `nl`/`indent`/`dedent` tokens with a bracket stack (so `'(' was never closed`, `unmatched ')'` and `unindent does not match` come out exactly like CPython); for JavaScript, `jsTokens` remembers newlines for automatic-semicolon handling. A recursive-descent `Parser` builds one AST (`assign`, `if`, `while`, `for`, `foreach`, `func`, `return`, `bin`, `logic`, `call`, `member`, `index`, `slice`, `tpl`…). Error messages copy V8/CPython wording so one error catalogue serves every engine.
- **Mini interpreter as generators.** Every statement `yield`s before it runs, so the same `Machine` powers instant runs (`runMini` drives it to the end with a step cap and a time deadline checked every 512 steps) and the visualiser (`traceMini` stores a snapshot per step: frames with variables, active loops with counters, output length, turtle length). Python semantics that confuse beginners are modelled on purpose: `int`/`float` (a `PyFloat` wrapper so `10/2` prints `5.0`), floor division and modulo signs, str + int `TypeError`, comparisons between `str` and `int`, `UnboundLocalError` (functions pre-scan their assigned names), banker's rounding, recursion limit; JavaScript keeps loose/strict equality, `const`, block scoping and hoisted functions.
- **Sandbox and line mapping.** The worker receives the code as a message and compiles it with `new Function`, so syntax errors never escape as uncaught errors; V8 places the body after two header lines, so `<anonymous>:N` maps to line `N − 2`. A `setTimeout` on the main thread terminates the worker at the time limit; `guessLoopLine` then points at a `while` loop whose condition variables are never assigned in its body.
- **Goals.** Goals are data (`{k: 'out' | 'outRe' | 'lines' | 'code' | 'codeMax' | 'asks' | 'turtle' | 'colors'}`) checked against what the program printed (typed answers excluded), the comment-stripped code and turtle statistics. A lesson completes only when the run has no error and every goal passes; completion is stored per language.
- **Turtle.** Commands are recorded, not executed live, then `buildTurtleModel` replays them with Python turtle's rules (heading 0 = east, left = anticlockwise) and CPython's circle stepping (`steps = 1 + ⌊min(11 + |r|/6, 59)·|extent|/360⌋`), producing drawing ops and stats (lines, total turning, colours) used both for the canvas animation and for goals.
- **Explain this error.** `findCatalogEntry` matches `Type: message` against the catalogue; regex groups and a Levenshtein “did you mean” fill English and Urdu templates. Each fix in `AUTOFIX` is a small, conservative text transform of the error line (or the line that created an `input()` value) that returns `null` when it is not sure.
- **Blocks ↔ code.** Blocks form a tree; generation indents by depth, declares JavaScript variables with `let` on first use and adds `pass`/`from turtle import *` for Python. Expressions typed into blocks are translated token by token (`and`/`&&`, `True`/`true`…). Code → blocks walks the AST and maps recognised statements (turtle calls, print, assignments, `x += n`, `input()`/`ask()` with optional `int()`/`Number()`, counted loops, `if`/`else`), listing lines it skipped.
- **Streaks and badges.** Each run or mentor question adds to `days[YYYY-MM-DD]`; the current streak counts back from today (or yesterday), the best streak scans the sorted days. Badges are rules over the profile evaluated after every run.
- **Live AI adapter (exact contract).** The SDK loads lazily with `const { default: Anthropic } = await import('https://cdn.jsdelivr.net/npm/@anthropic-ai/sdk@0.131.0/+esm')` and `new Anthropic({ apiKey, dangerouslyAllowBrowser: true })`. Models: `claude-opus-5-5` (default, $4 / $20 per million input / output tokens), `claude-sonnet-5-5` ($2 / $10), `claude-haiku-5-5` ($0.10 / $0.50). Requests use `client.beta.messages.create` or `client.beta.messages.stream` with `{ model, max_tokens: 16000, system, messages, output_config: { effort: 'low' }, ...fb }`, where `fb = { betas: ['server-side-fallback-2026-07-01'], fallbacks: 'default' }` for Opus and Sonnet and `{}` for Haiku; no `thinking`, `temperature`, `top_p` or `top_k`, and the message list always ends with a user turn (no prefill). Streaming appends only `content_block_delta`/`text_delta` events and then awaits `finalMessage()`. `stop_reason === 'refusal'` is checked before reading content, and only `text` blocks are read. `client.messages.countTokens` gives exact input tokens in the settings test; elsewhere the UI shows a labelled ≈ 4-characters-per-token estimate. Errors are classified most-specific first: `AuthenticationError` → `NotFoundError` → `RateLimitError` → `APIConnectionError` → `APIError` (status + message); any failure falls back to the offline mentor.

## Left for students

| Extension | Difficulty | Start here |
|---|---|---|
| Server proxy for Live AI (key stays on a server; per-class rate limits and logs) | Medium | `aiClient()` |
| Cloud classes: sync profiles and progress to Firebase/Supabase so a teacher sees every pupil's device | Hard | `classMembers()` |
| Live `input()` instead of the answers box (SharedArrayBuffer + Atomics, cross-origin isolation) | Hard | `jsWorkerSource()` |
| Full-Python visualiser with `sys.settrace` inside Pyodide | Hard | `startVisualiser()` |
| Function and list blocks (define/call, append, for-each) translated both ways | Medium | `BLOCK_TYPES` |
| Read-aloud Buddy with the Web Speech API (Urdu voice when available) | Easy | `mentorAsk()` |
| Verifiable certificates (HMAC or QR code with a verification link) | Medium | `certificateHTML()` |
| Teacher lesson editor (JSON lessons and goals shared with a class code) | Medium | `LESSONS` |

Search the source for `TODO(student)` to find each stub. The same list appears on the About page.

## Data and safety notes

- **Illustrative data.** Sample learners, the two classes, school names, teachers and the teacher message are fictional and marked “sample” in the UI. Lesson content is original; school-syllabus alignment should be checked by a teacher. Certificates say “not an official qualification”.
- **Privacy.** Everything stays in this browser (`localStorage`); there is no account, analytics or tracking. Exports never include the API key. The teacher PIN is a convenience lock, not security.
- **Live AI.** Off by default. When enabled, the learner's code, last output and question are sent to the AI provider using the user's own key. The settings page warns that a key in a browser page is visible to anyone using the device and recommends a restricted key with a spend limit; production apps should proxy the call through a server (student TODO). The kid-safe system prompt keeps answers short, hint-first and on-topic, refuses to request personal details and points to a trusted adult when a child seems upset; refusals show a friendly notice.
- **Sandboxing.** JavaScript runs in a dedicated worker with network APIs removed and a hard time limit; it cannot touch the page. Pyodide is loaded from the pinned jsDelivr URL only after the user asks.
- **Limits.** Mini-Python and the visualiser cover the beginner subset used in the lessons (no classes, `try`, comprehensions, lambdas, tuples, `for…else`; JavaScript arrow functions are unsupported in the visualiser but run normally). Multiple `Turtle()` objects share one pen. `ask()`/`input()` read pre-typed answers rather than pausing the program.

## Evaluation ideas for the FYP report

- **Learning gains:** pre/post quiz on the six units with two groups (CodeBuddy vs. a plain online editor) in a Grade 6–8 class; report effect size.
- **Error recovery:** log time from first error to next successful run with the explanation card shown vs. hidden; compare English-only vs. bilingual explanations.
- **Explainer coverage:** collect 200 real beginner errors and measure how many map to a specific catalogue entry, how many fixes are correct, and false-fix rate.
- **Interpreter fidelity:** run the lesson programs and a corpus of beginner scripts through mini-Python and Pyodide and compare output, error type and line (the behaviour test already does this for every reference solution).
- **Visualiser usefulness:** think-aloud sessions asking children to predict variable values before and after stepping.
- **Mentor quality:** rate offline vs. Live AI replies for correctness, hint-not-answer behaviour and age-appropriateness; measure cost per learner-hour from token counts.
- **Teacher value:** interview teachers about the roster, “needs a helping hand” list and lesson completion bars after a two-week pilot.
- **Accessibility and performance:** Lighthouse accessibility audit, keyboard-only walkthrough, RTL review by Urdu readers, and run-time on low-end lab PCs.

## Verification

- Harness (`check.mjs`) on 23 states at 1366 × 850 and 390 × 844 — lesson with error card, turtle, steps, Buddy chat, Live-AI pane, blocks, projects, progress, teacher, error dictionary, inbox, settings, about, playground, search, find bar, engine modal, certificate modal, onboarding tour, Urdu IDE and progress, chalkboard theme, print emulation: no console/page errors, no horizontal overflow.
- Behaviour script: **64/64** checks — all 58 reference solutions (24 lessons + 5 projects × JavaScript and Python) pass their goals through the real runners while no starter code does; error explanations, line mapping, time limits and one-click fixes; turtle pixels; visualiser variables, stack and loops; blocks round trips; offline mentor intents (English and Urdu); the Live AI adapter against stubbed clients (exact params per model, no forbidden fields, text-only reading, streaming deltas, refusal, token count, error chain, no prefill); Pyodide offline fallback; editor features; progress, certificate, teacher, class codes, inbox, search, language, theme, export without key, demo toggle, tour and error dictionary. No network requests other than (stubbed) fonts.
