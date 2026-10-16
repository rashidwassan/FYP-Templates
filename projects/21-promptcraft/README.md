# 21 · PromptCraft — Prompt Engineering Game (idea #86)

**Learn to ask AI well by playing.** Six worlds of five levels each hide a rubric; an explainable offline judge scores your prompt check by check, and a simulated model visibly improves as your prompt does. Around the campaign: a Playground with `{{variables}}`, A/B tests and a token/cost estimator, a 34-template prompt library with shareable PNG cards, a live linter with squiggles and one-click fixes, 12 lessons with quizzes, a date-seeded daily challenge, local leaderboard, XP, streaks and 15 achievements. Everything works offline; Live AI is an opt-in bring-your-own-key mode. English only, playful arcade-notebook design with a mascot called Promptly.

**Scale:** ≈ 5,750 lines in one `index.html` (≈ 5,000 lines of JavaScript, ≈ 630 of CSS, 79 sprite icons) · 236 named functions · 18 modules · seed content: 30 levels (each with scenario, hidden rubric, starter, reference solution and simulator pack), 34 library templates, 12 lessons with 24 quiz questions, 15 achievements, 6 daily scenarios x 7 twists, 8 demo rivals, plus demo progress (3 played levels with 7 attempts, 2 lessons, 2 dailies, 2 Playground runs, 3 inbox notices) · 8 student TODOs.

## Getting started

- Open `index.html` in any modern browser. It runs from `file://` with no build step and no server. The only network requests are Google Fonts (Bricolage Grotesque, JetBrains Mono; system fonts are used offline) and, only if you switch Live AI on, the official SDK from jsDelivr.
- First open starts a 7-step spotlight tour on the world map. Demo progress (Clarity Cove partly played, two lessons, a short streak, rivals) makes every screen useful immediately; hide it with **Settings > Show demo data** (nothing is deleted).
- **Where data lives:** `localStorage` under the `pc:` prefix — `settings`, `profiles`, `active`, `prof:<playerId>` (all progress for one player), `inbox`, `pg` (Playground draft), `lint` (Linter draft), `live` (Live AI preferences, never the key), `draft:<levelId>` (unsent level prompts). The API key lives in `sessionStorage["pc:livekey"]` unless you tick "remember on this device".
- **Backup / reset:** Settings > Data exports all players and progress as JSON (never the key), imports a backup, or resets everything (type `RESET`). Console: `Object.keys(localStorage).filter(k => k.startsWith('pc:')).forEach(k => localStorage.removeItem(k))`.
- **Live AI (optional):** Settings > Live AI > paste a restricted key, choose a model, Test connection, then switch "Use Live AI" on. Levels and the Playground then stream real model output; the offline engine remains the fallback for every error.

## The problem

Students in Pakistan and everywhere else now use AI chatbots daily, but almost nobody is taught how to ask. Vague prompts waste time and tokens, outputs vary for reasons users cannot see, and "prompt engineering" advice is scattered in blog posts. PromptCraft makes the craft concrete and measurable: each level is a small, funny real-world brief (a chai-stall menu, a cat's resignation letter, a cricket commentary bot, a hackathon API), the judge shows exactly which elements your prompt is missing, and the model's output changes in front of you when you add them.

## What is built

| Area | Features |
|---|---|
| Campaign | 6 worlds (Clarity, Constraints, Examples / few-shot, Step-by-step reasoning, Roles and personas, Output formats and JSON) x 5 levels; SVG world map with a winding trail (wide 3x2 and narrow zigzag layouts), world pages with a level path and level cards, boss levels, unlock rules (next level at 1 star, next world after 3 passes), practice mode |
| Level play | Split editor / result layout: Promptly speech bubble with the scenario, task, chips naming the hidden check categories, squiggle editor, word / token / lint footer, Run judge (Ctrl Enter), hints that cost 5/10/15 coins, model answer for 25 coins, drafts autosaved; tabs for simulated model output (typewriter), judge report (animated score ring, stars, every check with partial credit and explanation) and attempts (sparkline, best prompt, share card); JSON/CSV validity chip |
| Offline judge | Rubric -> weighted checks: must-have elements, stated constraints, examples count, format request, role, audience, tone, reasoning cue, output-only, word budget, banned vague words, specificity score; 0–100 score and 1–3 stars; calibrated so every reference solution earns 3 stars and every starter at most 1 |
| Simulated model | Template-driven generator keyed off the same checks: row/table packs, text packs, reasoning packs (wrong, half-right, fully worked), JSON packs (bad keys and types, chatty fences, or clean JSON), daily "facts" packs; renders as table, CSV, JSON, numbered or bulleted list, letter or paragraph depending on what the prompt asked for; obeys stated item and word limits |
| Playground | System + user prompt, `{{variables}}` fill panel with highlighted preview, rubric picker (general quality, today's daily or any level), single or A/B mode judged by the same rubric with a winner banner, free-form simulator (lists, messages, steps, tables), token and cost estimator for three models, history with reload, share card, send to linter |
| Prompt library | 34 templates in 8 categories (Study, Writing, Coding, Career, Business, Data & JSON, Fun, Everyday), search, category chips, favourites, fork to Playground, shareable 1200x630 PNG cards drawn on canvas with copyable share text |
| Prompt linter | Live wavy underlines (error / warning / tip) mirrored behind the textarea; rules for missing task, missing audience, missing format, conflicting instructions (length, tone, mood, bullets, JSON-vs-explanation, impossible numeric limits), vague words, overlong prompt and sentences, filler politeness, shouting, many negatives, unclear opening reference, empty variables, missing role; one-click fixes, Fix all, lint score, three messy samples |
| Daily challenge | Date-seeded scenario x twist (same challenge for everyone on a date), countdown to midnight, 14-day completion strip, +30 coins on first completion |
| Learn | 12 short lessons with before/after prompts scored by the general rubric, "why it works" bullets, two-question checks with explanations, links to Playground, Linter and the matching world |
| Progress | XP (derived from records), levels, coins wallet, daily streak (current and best), 15 achievements, local multi-player profiles, leaderboard (players on this device + illustrative rivals) sortable by XP, stars or streak |
| Live AI adapter | Bring-your-own-key, lazy SDK import, Opus 5.5 / Sonnet 5.5 / Haiku 5.5 picker with prices, non-streaming and streaming calls, refusal handling, token counting, typed error chain, offline fallback, optional AI judge for levels and AI comparison for A/B runs |
| Platform | Settings (player name and colour, night/day theme, text size, reduced motion, typewriter, demo data, practice mode, Live AI, backup export/import/reset), onboarding spotlight tour, inbox with unread badge and activity log, printable progress report with print CSS, command palette / global search (Ctrl K), keyboard shortcuts sheet (?), aria-live result announcements, labelled controls, focus styles, keyboard-closable dialogs |

## Module map

| Module | Key functions | Storage |
|---|---|---|
| Analyzer | `analyzePrompt`, `instructionText`, `detectLimits`, `countExamples`, `specificityScore`, `hasFormat` | — |
| Linter | `lintPrompt`, `lintStructure`, `lintConflicts`, `lintVague`, `lintStyle`, `lintScore`, `squiggleHTML`, `mountEditor` | `pc:lint` |
| Judge | `buildChecks`, `matchPattern`, `evalPatternCheck`, `evalFeatureCheck`, `judgePrompt`, `failingChecks` | — |
| Simulated model | `simulateOutput`, `simRows`, `simText`, `simReason`, `simJson`, `simFacts`, `renderTableLike`, `outputValidity`, `simulateFree`, `freeMessage` | — |
| Campaign map | `drawWorldMap`, `smoothPath`, `worldUnlocked`, `levelUnlocked`, `nextLevel`, `renderMap`, `renderWorld`, `levelPathHTML` | `pc:prof:<id>.levels` |
| Level play | `renderPlay`, `runPlay`, `recordAttempt`, `renderPane`, `reportPaneHTML`, `buyHint`, `showAnswer`, `typeInto` | `pc:prof:<id>.levels`, `pc:draft:<levelId>` |
| Daily challenge | `dailyFor`, `dailyHeaderHTML`, `startCountdown`, `dailyRecord` | `pc:prof:<id>.daily` |
| Playground | `renderPlayground`, `runPlayground`, `runAB`, `renderVarsPanel`, `renderCostPanel`, `addHistory`, `streamInto` | `pc:pg`, `pc:prof:<id>.history`, `.abRuns` |
| Library & share cards | `renderLibrary`, `filteredTemplates`, `forkTemplate`, `renderShareCanvas`, `openShareCard` | `pc:prof:<id>.favs`, `.forks`, `.shares` |
| Learn | `renderLearn`, `beforeAfterHTML`, `bindLesson`, `finishLesson` | `pc:prof:<id>.lessons` |
| Economy & streaks | `computeXp`, `xpLevel`, `coinsEarned`, `wallet`, `spendCoins`, `streakInfo`, `markActiveDay` | `pc:prof:<id>.spent`, `.days`, `.runs` |
| Achievements | `ACHIEVEMENTS`, `checkAchievements` | `pc:prof:<id>.achievements` |
| Leaderboard & players | `leaderboardRows`, `renderRanks`, `createProfile`, `switchProfile`, `deleteProfile` | `pc:profiles`, `pc:active` |
| Live AI | `Live.getClient`, `liveParams`, `fallbackParams`, `liveCreate`, `liveStream`, `liveCountTokens`, `readMessage`, `describeLiveError`, `runLive`, `aiJudgeRequest`, `aiCompareRequest` | `pc:live`, `sessionStorage pc:livekey` |
| Inbox & activity | `addNotice`, `renderInbox`, `activityItems`, `unreadCount` | `pc:inbox` |
| Report | `renderReport`, `reportWorldRows` | — |
| Settings & backup | `renderSettings`, `exportData`, `importData`, `reloadState`, `applyAppearance` | `pc:settings` |
| Tour, palette, shortcuts, router | `startTour`, `positionTour`, `openPalette`, `paletteEntries`, `onGlobalKey`, `route`, `boot` | `pc:settings.tourDone` |

## Architecture (single file)

```
index.html
├─ <head>       Google Fonts link; <style> tokens (indigo night + day theme, text sizes), motion kit, base,
│               buttons/inputs/chips, shell (topbar, HUD, bottom nav, more sheet), mascot, world map, level path,
│               split play layout, squiggle editor, terminal, score ring, checks, playground, library, linter,
│               leaderboard, achievements, daily strip, learn, inbox/settings/report/about, modal/toast/tour/palette,
│               responsive (1020 / 760 px), print
├─ <body>       inline SVG sprite (generated from the icons the code uses), top bar, <main #view>, bottom nav,
│               more sheet, scrim, modal, tour layer, toasts, aria-live region
└─ <script>
   ├─ utils              $, esc, ic, todayKey, hashStr + mulberry32 (seeded RNG), debounce, download, pickFile,
   │                     copyText, announce, toast, animateIn, countUp, drawPath, animateBars
   ├─ storage/settings   store (pc: prefix), DEFAULT_SETTINGS, applyAppearance, demoOn
   ├─ modal              openModal, closeModal, confirmModal (typed confirmation)
   ├─ profiles/economy   initProfiles, createProfile, switchProfile, computeXp, xpLevel, coinsEarned, wallet,
   │                     spendCoins, streakInfo, markActiveDay, renderHud
   ├─ analyzer           VAGUE/ROLE/AUDIENCE/STEPS/TONE/TASK/LIMIT regexes, FORMAT_TESTS, analyzePrompt
   ├─ linter             lint rules + fixes, lintScore, squiggleHTML, mountEditor (reusable squiggle editor)
   ├─ campaign content   WORLDS, lv(), 30 levels (scenario, rubric, starter, solution, sim pack)
   ├─ judge              GENERAL_LEVEL, buildChecks, matchPattern, judgePrompt, failingChecks
   ├─ simulated model    on(), renderTableLike, simRows/simText/simReason/simJson, simulateOutput,
   │                     outputValidity, simulateFree (lists, messages, steps)
   ├─ content            TEMPLATES (34), fillVars, LESSONS (12), ACHIEVEMENTS (15), DAILY_SCENARIOS,
   │                     DAILY_TWISTS, dailyFor, simFacts, RIVALS, seedProgress, inbox (loadInbox, addNotice)
   ├─ Live AI            LIVE_MODELS, Live (key, lazy load, client, test seam), liveParams, liveCreate,
   │                     liveStream, liveCountTokens, describeLiveError, runLive, judge/compare requests, cost helpers
   ├─ mascot/map/world   mascot(expr), smoothPath, drawWorldMap, renderMap, renderWorld
   ├─ level play         renderPlay, onPlayEdit, runPlay, recordAttempt, panes, hints, model answer, live play, AI judge
   ├─ playground         renderPlayground, variables, cost, history, runPlayground, runAB, runLiveAB
   ├─ library            renderLibrary, tplCardHTML, forkTemplate, share canvas (wrapLines, drawStar, drawCardMascot)
   ├─ linter page        renderLinter, renderIssues, applyLintFix, fixAll
   ├─ ranks/learn/inbox  renderRanks (board, achievements, players), renderLearn, renderInbox
   ├─ report             renderReport (print CSS isolates the sheet)
   ├─ settings/about     renderSettings, Live AI settings, export/import/reset, STUDENT_TODOS, renderAbout
   ├─ tour/palette/keys  TOUR_STEPS, startTour, positionTour, openPalette, SHORTCUTS, onGlobalKey
   └─ shell/router/boot  NAV, renderShell, more sheet, ROUTES, route, onResize, boot, window.PromptCraft hooks
```

Routes: `#map` · `#world/<id>` · `#level/<world>-<n>` · `#daily` · `#playground` · `#library` · `#linter` · `#learn[/<lesson>]` · `#ranks` · `#inbox` · `#report` · `#settings` · `#about`.

## Domain logic / algorithm notes

- **Feature analysis.** `analyzePrompt` first blanks out example data (quoted strings, `[placeholders]` and lines labelled `Input:`, `Review:`, `Example 1:` ...) so that a review saying "food was good" is not punished as vague. It then detects: word/sentence counts, numbers, proper nouns, quotes, vague words, role (`You are...`, `Act as...`), audience (`for class 6 students`, `78 years old`, `to the warden`), reasoning cues (`step by step`, `show your working`, `verify`), tone words, delimiters, task verbs, constraint sentences, measurable limits (`under 50 words`, `exactly 3 lines`, number words normalised to digits), requested formats (table, JSON, CSV, list, numbered, headings, letter, poem, one line, label only), output-only phrases, worked examples (labelled examples, Input/Output pairs, `->` lines) and `{{variables}}`.
- **Specificity score** = 100 x (0.30·min(1, numbers/3) + 0.25·min(1, names/3) + 0.15·min(1, quotes) + 0.30·min(1, 3.5·share of words with 7+ letters)) − 6 per vague word, clamped to 0–100.
- **Judge.** Each level's rubric expands into checks with weights: must-have element 10, constraint 8, role 10, audience 8, tone 6, reasoning 12, examples 12, format 12, output-only 8, word budget 6, vague words 6, specificity 6. Element checks run case-insensitive regexes on the number-normalised prompt and can require several distinct matches (e.g. 3 menu items, all 4 suspects). Partial credit: matches/required, words inside the budget or a linear penalty outside it, vague words −34 % each, specificity relative to the target. Score = Σ(weight x partial) / Σweight x 100. Stars: 1 at 55, 2 at 75, 3 at 90 and every must-have passed.
- **Simulated model.** Every level ships a pack that ties pieces of output to check ids. Rows packs show real rows (with optional columns such as prices) only when the matching checks pass, otherwise generic filler rows; text packs choose a good or a bad sentence per check; reasoning packs return a confident wrong answer without a reasoning cue, a half-worked wrong answer when numbers are missing, and full steps plus a check line when both are present; JSON packs return badly keyed and typed JSON inside chatty fences until keys, types and "only JSON" are specified. A shared renderer turns the same rows into a table, CSV, JSON, numbered or bulleted list depending on what the prompt asked for, and honours stated item and word limits. Output is deterministic per prompt (seeded by a hash) so A/B runs are fair.
- **Linter.** Issues carry a severity, a character range (plus extra ranges for the other side of a conflict) and an optional fix function. The editor mirrors the text into a `<pre>` behind a transparent textarea and wraps issue ranges in `<mark>` with wavy underlines. Fixes either replace a range (with a fallback search if the text moved), insert a placeholder line, or remove a conflicting term together with its connector ("short but detailed" -> "short"). Lint score = 100 − 18·errors − 8·warnings − 2·tips.
- **Economy.** XP is derived, never stored: per level best score + 25 per star, 40 per lesson (+20 perfect), daily best/2 + 20, 50 per achievement, 2 per run (max 100). Level n starts at 25·(n−1)·(n+4) XP. Coins = 40 starting + 10 per star + 10 per lesson + 30 per daily pass + 20 per achievement − coins spent on hints. Streak = consecutive days with at least one judged prompt ending today or yesterday.
- **Daily challenge.** `dailyFor(date)` seeds mulberry32 with an FNV-1a hash of the date and picks one of 6 scenarios and one of 7 twists (children's audience, table, character limit, JSON only, exactly 4 bullets, formal tone, commentator persona); the rubric is the scenario's must-haves plus the twist's checks. All 42 combinations are calibrated so the generated model answer earns 3 stars.
- **Token and cost estimate.** Offline: about 4 characters per token, labelled as an estimate; cost = (input x input price + expected output x output price) / 1,000,000 for Opus 5.5 ($4 / $20), Sonnet 5.5 ($2 / $10) and Haiku 5.5 ($0.10 / $0.50). With Live AI on, "Count exactly" calls the token-counting endpoint.
- **Live AI contract.** The SDK is imported lazily from `https://cdn.jsdelivr.net/npm/@anthropic-ai/sdk@0.131.0/+esm` and constructed with `dangerouslyAllowBrowser: true`. Requests are `client.beta.messages.create|stream({ model, max_tokens: 16000, system, messages, output_config: { effort: 'low' }, ...fb })` where `fb = { betas: ['server-side-fallback-2026-07-01'], fallbacks: 'default' }` for Opus 5.5 and Sonnet 5.5 and `{}` for Haiku 5.5; no `thinking`, sampling parameters or assistant prefill. Streaming appends only `content_block_delta` / `text_delta` text, then reads `stream.finalMessage()`. `stop_reason === 'refusal'` is checked before content and shown as a friendly notice; only `text` blocks are read. Errors are mapped most-specific first (authentication, not found, rate limit, connection before generic API error) and always fall back to the offline engine with an inbox notice. The AI judge asks for a JSON verdict and treats the player's prompt as delimited data; stars stay with the offline judge so scores remain comparable.

## Left for students

| Extension | Difficulty | Start here |
|---|---|---|
| Server-side proxy for Live AI (hold the key, rate-limit per player, forward model/system/messages) | Hard | `Live.getClient()` |
| Semantic rubric matching with sentence embeddings and explained similarity scores | Hard | `matchPattern()` |
| Prompt-injection world: system prompts that must resist hostile user text, judged with attack strings | Hard | `WORLDS` |
| Online leaderboard with auth, server-side XP recomputation and weekly seasons | Medium | `leaderboardRows()` |
| Multi-turn Playground with chat history, bubbles and branch-and-compare | Medium | `buildMessages()` |
| Level editor and custom campaign packs (author rubric rows, preview the judge, import/export JSON) | Medium | `lv()` |
| Urdu interface (string dictionary, Noto Nastaliq Urdu, right-to-left layout, translated lessons) | Medium | `gameCardHTML()` |
| Spaced-repetition review of lesson questions (Leitner boxes, due reviews in the inbox) | Easy | `finishLesson()` |

Search the source for `TODO(student)`; each marker sits at the stub point with implementation notes.

## Data and safety notes

- Scenarios, names, phone numbers, prices, venues and rival players are fictional and illustrative. Model prices are as listed for these models at the time of writing; verify current pricing on the provider's official pages before budgeting.
- The judge measures the structure and specificity of a prompt, not the truth or safety of any model's answer. The simulated model is a template engine, labelled as such everywhere it appears.
- All data stays in the browser. Live AI is off by default; when enabled it sends the player's prompt (and, for the AI judge, the level brief) to the model provider using the player's own key. A key in a browser page is visible to anyone using the device: use a restricted key with a spend limit, prefer the session-only default, and move calls to a server for production (student TODO).
- Lesson 12 and the delimiter lesson teach prompt-injection awareness and fact-checking; the AI judge prompt wraps player text in tags and tells the model to treat it as data.

## Evaluation ideas for the FYP report

- **Learning gain:** pre/post test with the 24 lesson questions plus a "rewrite this prompt" task scored by the judge; compare players who completed two worlds with a control group who read a prompting guide.
- **Judge validity:** collect 100 player prompts, have two humans rate them 1–5, and report Spearman correlation with the judge score and inter-rater agreement; with Live AI on, report agreement (Cohen's kappa on stars) between the offline judge and the AI judge.
- **Calibration and ablation:** the shipped calibration (every solution = 3 stars, every starter <= 1 star, all 42 daily combinations) as a regression test; ablate each check type and measure how rankings change.
- **Behaviour analytics:** attempts per level, hint usage per world, score improvement between first and best attempt, which lint rules fire most and which fixes are accepted.
- **Usability and accessibility:** System Usability Scale with 10+ students, keyboard-only walkthrough, reduced-motion and day-theme contrast checks, phone (390 px) task completion times.
- **Cost awareness:** compare estimated tokens with exact counts from the token-counting endpoint across the 34 templates; report the error of the 4-characters-per-token heuristic.
