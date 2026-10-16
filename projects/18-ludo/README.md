# 18 · Ludo (idea #66)

**The board game every Pakistani household already knows — rebuilt as a polished browser game.** Pass-and-play Ludo for 2–4 players with four AI levels (including an expectimax Expert), round-robin tournaments, a replay viewer, achievements, per-player statistics with charts, a guided tutorial, custom board colours, screen-reader move announcements and an Urdu toggle — in one HTML file.

**Scale:** 4,498 lines in one file (≈500 CSS, ≈3,870 JS) · 45 code sections making up 14 new modules on top of the original game · seed data: 3 recent results, 8 history games (26 player rows with dice-face counts), 2 unlocked achievements, a welcome notification and one sample replay generated on first open.

## Getting started

- Open `index.html` in any modern browser (Chrome, Edge, Firefox, Safari). No build step, no server, no dependencies beyond Google Fonts (falls back to system fonts offline).
- A spotlight tour runs on the first visit (Settings › Help › *Show the tour again* repeats it). *Guided first game* on the setup screen teaches the rules move by move.
- Everything is saved in `localStorage` under the `ludo:` prefix. Original keys are unchanged: `ludo:game` (auto-saved after every roll and move), `ludo:prefs` (seats, rules, theme, colours, accessibility, sound), `ludo:results` (recent games), `ludo:lang`. New keys: `ludo:history`, `ludo:replays`, `ludo:ach`, `ludo:inbox`, `ludo:tourney`, `ludo:arena`, `ludo:tutorial`, `ludo:toured`.
- Reset: Settings › *Reset everything*, or clear site data. Export/Import JSON in Settings moves every module between devices (the original `{prefs, game, results}` file still imports).
- Console API: `Ludo.startGame(seats)`, `Ludo.simulate(n, stopAtHuman)`, `Ludo.selfTest(n, levels?)`, `Ludo.forceWin()`, `Ludo.state`, plus `Ludo.playHeadless(seats)`, `Ludo.arena(levels, games)`, `Ludo.expertChoose`, `Ludo.tutorial()`, `Ludo.tour()`, `Ludo.tournament`, `Ludo.records`, `Ludo.achievements`, `Ludo.inbox`.

## The problem

Ludo apps on the Play Store are ad-heavy, online-only and rarely in Urdu. An FYP that ships a complete, offline, rules-correct Ludo with a readable engine, a measurable AI and proper record keeping is a strong base for the harder research pieces — networking, rating systems and stronger search — which are left as extension points.

## What is built

| Area | Features |
|---|---|
| Board | SVG 15×15 grid with correct geometry: four 6×6 bases with 4 slots, 52-square loop, coloured start squares with direction arrows, 4 star safe squares, coloured home columns, centre triangles that collect finished tokens; tokens stack and shrink when sharing a square |
| Rules | 2–4 players, red starts, clockwise; 6 to come out; 6 = extra roll; capture on non-safe squares; win order for all players; game ends when all but one finish. **Rule variants** (toggles, applied instantly): exact finish, three sixes forfeit, star squares safe, blocks, bonus roll on capture, bonus roll on finishing. **Assists:** auto-move single option, hints, quick dice, casual undo |
| AI | Easy / Medium / Hard heuristic scorer (unchanged) plus **Expert**: expectimax with one roll of lookahead over a static evaluation. Measured with the built-in arena: Expert beats Hard in 66 % of 180 head-to-head games; in 360 seat-rotated 4-player games Expert wins 29 % against 22 % for Hard |
| Tournament | Round-robin between the setup seats: head-to-head pairs (colours alternate per round) or full table (colours rotate), 1–3 rounds, standings with points / wins / tokens home / captures, live human matches, one-click simulation of AI-only matches, champion banner, printable standings |
| Replay viewer | Every finished game is saved with its move list; rebuild any position from the empty board, scrub, step, play at 0.5–4×, keyboard arrows / Home / End, per-move description, link to the report |
| Statistics | KPI tiles (games, dice rolled, captures, time), leaderboard table, hand-drawn SVG charts: wins by player, finishing-place distribution, captures made vs lost per game (player picker), dice fairness against the expected sixth; tooltips on every mark |
| AI arena | Pick a level (or Off) per seat and run 20–100 headless games; win share and average place per seat, stored for the next visit |
| Achievements | 12 achievements (first victory, hat-trick, untouchable, lucky streak, speedster, giant killer, full house, comeback, marathon, veteran, champion, scholar) with progress bars and unlock dates |
| Tutorial | Guided first game against an easy AI: scripted opening dice (a six, then a short move), coach card above the board, board highlights for safe squares and the home column, Scholar achievement on completion |
| Onboarding | Five-step spotlight tour of seats, rules, quick actions, navigation and settings; Esc / Skip / Back / Next |
| Colours | Classic / Dark / Pastel themes, token palettes (Standard, Sunset, Ocean, Garden, colour-blind friendly Okabe–Ito, Custom with four colour pickers), six board frames |
| Accessibility | Assertive live-region sentence for every roll, move, capture, finish and placing (toggle each); legal tokens are focusable (Tab + Enter); square numbers on the track; reduce-motion toggle on top of the OS setting; aria labels on all icon buttons |
| Inbox | Notifications for results, unlocked achievements, tournament matches and the tutorial; unread badge, mark read, clear; old notes re-translate with the language toggle |
| Match report | Printable report for any saved or current game: final standings, dice distribution chart, rules in force, tournament table, full move list |
| Play | Setup screen (seat type, name, AI level, rule toggles, recent games, quick cards), pass-and-play with "pass the device" toast, hints from the Expert AI, undo (casual mode), pause, keyboard (Space, 1–4, U, P, Esc), stats modal with log, win screen with podium, replay/report links and play again |
| Persistence | Auto-save after every step, resume from setup screen or `#game`, export/import JSON (v1 and v2), recent-results list |
| Sound | WebAudio-synthesised dice, hop, six, capture, finish, win and unlock; mute toggle; context created only after a user gesture |
| i18n | English/Urdu dictionary for every label, event, rule, tutorial step, tour step and announcement (Noto Nastaliq Urdu, RTL blocks) |

## Module map

| Module | Main functions / objects | Storage keys |
|---|---|---|
| Engine | `newGame`, `legalMoves`, `applyMove`, `afterMove`, `nextTurn`, `resolveRoll`, `isSafe`, `cloneForSearch` | `ludo:game` |
| Heuristic AI | `threats`, `threatProb`, `scoreMove`, `greedyMove`, `aiChoose` | — |
| Expert AI | `expertChoose`, `expectNextRoll`, `bestByEval`, `evalPosition`, `progressValue` | — |
| Rule variants | `RULE_VARIANTS`, `RULE_ASSISTS`, `rulesToggles`, `bindRules` | `ludo:prefs.rules` |
| Turn flow | `doRoll`, `passAfterRoll`, `performMove`, `afterStep`, `schedule`, `undo`, `togglePause` | `ludo:game` |
| Announcements | `Speak.say`, `Speak.roll`, `Speak.move`, `Speak.where` | `ludo:prefs.a11y` |
| Records | `Records.finish`, `recordResult`, `gameSummary`, `replayRecord`, `Records.ensureSample` | `ludo:results`, `ludo:history`, `ludo:replays` |
| Achievements | `ACHIEVEMENTS`, `Achievements.check`, `Achievements.unlock`, `Achievements.list` | `ludo:ach` |
| Inbox | `Inbox.add`, `Inbox.text`, `Inbox.markAll`, `renderInbox`, `renderBadge`, `fmtAgo` | `ludo:inbox` |
| Tournament | `tourneySchedule`, `Tourney.create / play / simulate / simulateAll / recordGame / standings`, `renderTournament` | `ludo:tourney` |
| Replay viewer | `positionAt`, `findRecord`, `moveText`, `Replay.seek / stepForward / play`, `renderReplayPage` | `ludo:replays` |
| Statistics | `playerRecords`, `diceTotals`, `chartWins`, `chartPlaces`, `chartTrend`, `chartDice`, `Stats.render` | `ludo:history` |
| AI arena | `Arena.run`, `arena`, `playHeadless`, `chartArena` | `ludo:arena` |
| Tutorial | `TUT_STEPS`, `Tutorial.start / scriptedDie / event / go / finish / render` | `ludo:tutorial`, `ludo:game.tutorial` |
| Onboarding tour | `TOUR_STEPS`, `Tour.start / go / show / place / end` | `ludo:toured` |
| Board colours | `PALETTES`, `FRAMES`, `applyPalette`, `shade`, `coloursHTML` | `ludo:prefs.palette / frame / custom` |
| Settings & data | `renderDrawer`, `bindSettings`, `setA11y`, `exportAll`, `importData`, `resetAll` | all `ludo:` keys |
| Match report | `renderReport`, `reportStandings`, `reportMoveList`, `reportRules`, `reportTourney` | `ludo:replays` |
| Self-test & API | `simulateTurn`, `checkInvariants`, `selfTest`, `window.Ludo` | — |

## Architecture (single file)

```
index.html
├─ <style>      tokens (felt / walnut / 4 colours + soft & deep tints, chart + place ramps), frame presets,
│               motion kit, controls, topbar + badge, setup + quick cards, game grid, board SVG classes,
│               tutorial highlights, dice cube, status bar, coach card, drawer / swatches / inbox, modal,
│               tour spotlight, KPI tiles + charts, achievements, tournament, replay, report, about,
│               responsive (1100 / 900 / 600), print
├─ <svg>        icon sprite (only the symbols used + custom skip-back / rewind / fast-forward / volume-x)
├─ <header>     brand, nav (Game · New game · Tournament · Stats · About), New game, Inbox, Urdu, Sound, Settings
├─ <main>       #v-setup · #v-game (4 panels + coach + board + status + meta) · #v-tournament · #v-stats
│               · #v-replay · #v-report · #v-about
├─ drawer (settings / inbox), modal, tour overlay, chart tooltip, confetti, toasts, two aria-live regions
└─ <script>
   ├─ utils         $, store, esc, ic, rollDie, toast, animateIn, countUp, drawPath, downloadJSON, bindTips
   ├─ i18n          L.en / L.ur, t(), RULES_EN / RULES_UR
   ├─ prefs + seed  DEFAULT_PREFS, loadPrefs, SEED_RESULTS, SEED_HISTORY_ROWS, seedStorage
   ├─ geometry      TRACK, HOMECOL, bases, slots, posPoint, cellKey, STACK
   ├─ engine        newGame, legalMoves, applyMove, afterMove, nextTurn, resolveRoll, cloneForSearch  (pure)
   ├─ AI            heuristic scorer · expert expectimax
   ├─ sound · board colours (palettes, frames) · board SVG (bases, track, centre, tokensMarkup)
   ├─ controller    state, animations (dice, hop, capture, confetti), rendering (tokens, panels, status, meta)
   ├─ turn flow     Speak (announcements) → doRoll → resolveRoll → performMove → afterStep → schedule
   ├─ records       results, history, replays · achievements · inbox
   ├─ modal · stats/win screens · setup · settings drawer · export/import
   ├─ tournament · replay viewer · statistics (charts, arena, page) · tutorial · tour · report · about
   ├─ topbar · keyboard · router (#setup #game #tournament #stats #replay #report #about)
   └─ headless play + window.Ludo (startGame, simulate, forceWin, selfTest, playHeadless, arena …) · init
```

The engine never touches the DOM: `legalMoves`, `applyMove`, `afterMove` and `nextTurn` operate on a plain `G` object. That is what makes `selfTest()`, the Expert search, tournament simulation, the AI arena and the replay rebuild possible without any UI.

## Domain logic notes

- **Positions.** Each token is `-1` (base), `0..50` (relative loop position from its own start), `51..55` (home column) or `56` (finished). Absolute loop square = `(OFFSET[colour] + rel) % 52` with offsets 0/13/26/39. Safe squares are the four start squares {0, 13, 26, 39} plus, unless the *star squares safe* variant is off, the stars {8, 21, 34, 47}.
- **Legal moves.** For each token: needs a 6 to leave base; `rel + die > 56` is illegal under exact finish (or clamps to 56 when overshoot is allowed); landing on a non-safe square with ≥2 opponent tokens of one colour is illegal when blocks are on; otherwise opponents there are captured.
- **Turn flow.** `resolveRoll` returns `forfeit` (third six, when that variant is on), `none` (no move — a six still re-rolls) or the move list. `afterMove` grants another roll on a six, a capture (variant) or a finish (variant), detects game over when `winOrder.length ≥ active − 1`, else `nextTurn` skips empty seats and finished players.
- **Heuristic AI.** `threats()` lists opponent tokens 1–6 squares behind a square that could still reach it; Hard multiplies that count/6 by the token's progress as expected loss.
- **Expert AI.** For each candidate move: apply it to a light copy (`cloneForSearch`), run `afterMove` to learn who rolls next, then average over the six die values. If the mover keeps the turn it answers with its own best evaluation; otherwise the opponent answers with the Hard heuristic. Leaves are scored by `evalPosition` = own progress (home column at a premium) − capture risk − ½ × leading opponent − ¼ × mean opponent, plus a placing bonus. Weights were chosen by arena runs (four variants, 360 four-player and 180 two-player games each).
- **Replays** store only `{p, d, i, from, to, cap}` per move; `positionAt(k)` replays the first k moves onto an empty board, so scrubbing is exact and storage stays small (≈8 games kept).
- **Tournaments.** Pairs: every pair once per round, win 3 / loss 0. Full table: everyone each round, colours rotate so a different player rolls first, 3 · 2 · 1 · 0 by place. Ties: wins, then tokens home, then captures.
- **Undo** stores a JSON snapshot before every human move (casual mode) — AI replies made since are rolled back too, as in a friendly table game.

## Testing

- `Ludo.selfTest(200)` plays 200 headless turns with Easy / Medium / Hard / Expert seats and checks invariants after every turn (4 tokens each, legal positions, no two colours on one unsafe square, captures = losses).
- `Ludo.startGame` with four AI seats + `Ludo.simulate(20000, false)` finishes a full game at every level, including Expert.
- `Ludo.arena(['expert', 'off', 'hard', 'off'], 100)` reproduces the strength figures above.

## Left for students

| Extension | Difficulty | Start here |
|---|---|---|
| Online multiplayer (WebRTC data channels or WebSocket rooms, state sync on join) | Hard | `performMove()` — broadcast `mv`, apply remote moves |
| Spectator mode (read-only board for a joining viewer) | Medium | `startGame()` + the networking layer |
| Monte-Carlo AI level (random rollouts per candidate, compare with Expert in the arena) | Hard | `aiChoose()` — copies come from `cloneForSearch()` |
| Elo ratings per player name with a leaderboard column | Medium | `recordResult()` |
| Knockout bracket tournament format with seeding and an SVG bracket | Medium | `createTournamentFromForm()` |
| Blunder check in replays (compare each move with the Expert, mark drops on the scrubber) | Medium | `Replay.drawControls()` |
| Token skins (pawn, coin, crown or uploaded image) with a picker | Easy | `buildBoard()` / `tokenSVG()` |
| Spoken announcements with `speechSynthesis` and an Urdu voice | Easy | `Speak.say()` |

Search the source for `TODO(student)` to find the exact hooks.

## Data / safety notes

No personal data leaves the device; names are local labels only. Dice use the browser's CSPRNG with rejection sampling, so results cannot be steered by the page; the only scripted dice are the two opening rolls of the tutorial. Seeded history, results and achievements make the setup and statistics screens look alive and are replaced as real games are played; the sample replay is a real headless game generated on first open. Nothing here is a gambling product — there is no stake, currency or payout logic, and students adding online play should keep it that way.
