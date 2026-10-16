# 18 · Ludo (idea #66)

**The board game every Pakistani household already knows — rebuilt as a polished browser game.** Pass-and-play Ludo for 2–4 players with three AI levels, a correct 15×15 board, animated dice and tokens, undo, stats, save/resume, three themes and an Urdu toggle — in one HTML file.

## Getting started

- Open `index.html` in any modern browser (Chrome, Edge, Firefox, Safari). No build step, no server, no dependencies beyond Google Fonts (falls back to system fonts offline).
- Everything is saved in `localStorage` under the `ludo:` prefix: `ludo:game` (auto-saved after every roll and move), `ludo:prefs` (seats, rules, theme, sound), `ludo:results` (recent games), `ludo:lang`.
- Reset: Settings → *Reset everything*, or clear site data. Export/Import JSON in Settings moves a game between devices.
- Console API for testing: `Ludo.selfTest(200)`, `Ludo.simulate(n)`, `Ludo.forceWin()`, `Ludo.state`.

## The problem

Ludo apps on the Play Store are ad-heavy, online-only and rarely in Urdu. An FYP that ships a complete, offline, rules-correct Ludo with a readable engine is a strong base for the harder research pieces — stronger AI, networking, accessibility — which are left as extension points.

## What is built

| Area | Features |
|---|---|
| Board | SVG 15×15 grid with correct geometry: four 6×6 bases with 4 slots, 52-square loop, coloured start squares with direction arrows, 4 star safe squares, coloured home columns, centre triangles that collect finished tokens; tokens stack and shrink when sharing a square |
| Rules | 2–4 players, red starts, clockwise; 6 to come out; 6 = extra roll; three 6s forfeit; exact finish (toggle); capture on non-safe squares = extra roll; 8 safe squares where colours coexist; two-token blocks (toggle); bonus roll on finishing (toggle); auto-move when exactly one legal move (toggle); automatic pass when no move; win order for all players; game ends when all but one finish |
| AI | Easy / Medium / Hard. Scores each legal move (finish +100, capture +60 + victim progress, escape danger +30, safe square +20, leave home +25, progress +½/square, landing in front of an opponent −25). Hard adds a one-ply expected-loss term (⅙ per threatening token × progress). Easy plays randomly 40 % of the time. 500–900 ms "thinking" delay with indicator |
| Dice | `crypto.getRandomValues` with rejection sampling; 3D CSS cube tumble; *Quick dice* skips the animation |
| Motion | Token hop square by square, capture flight back to base, pulsing legal-move rings and target markers, hint star, confetti on win, panel glow for the active player, drawer/modal/toast transitions; all respect `prefers-reduced-motion` |
| Play | Setup screen (seat type, name, AI level, rule toggles, recent games), pass-and-play with "pass the device" toast, hints, undo (casual mode), pause, keyboard (Space, 1–4, U, P, Esc), stats modal with log, win screen with podium and stats, play again |
| Persistence | Auto-save after every step, resume from setup screen or `#game`, export/import JSON, recent-results list |
| Sound | WebAudio-synthesised dice, hop, six, capture, finish and win; mute toggle; context created only after a user gesture |
| Themes / i18n | Classic (cream felt + walnut), Dark, Pastel; English/Urdu dictionary for all labels, events and rules (Noto Nastaliq Urdu, RTL blocks) |

## Architecture (single file)

```
index.html
├─ <style>      tokens (felt / walnut / 4 colours + soft & deep tints), motion kit, controls,
│               topbar, setup, game grid (corner panels + board), board SVG classes, dice cube,
│               status bar, drawer/modal/toast, about, responsive, print
├─ <svg>        icon sprite (only the symbols used + custom i-volume-x / i-keyboard)
├─ <header>     brand, nav (Game · New game · About), New game, Urdu, Sound, Settings
├─ <main>       #v-setup · #v-game (4 panels + board + status + meta) · #v-about
├─ drawer, modal, confetti canvas, toasts, aria-live region
└─ <script>
   ├─ utils        $, store, esc, ic, rollDie, toast, animateIn, countUp, announce
   ├─ i18n         L.en / L.ur, t(), RULES_EN / RULES_UR
   ├─ prefs        DEFAULT_PREFS, rules, seats, seeded results
   ├─ geometry     TRACK (52 cells), HOMECOL, bases, slots, posPoint(), cellKey(), STACK
   ├─ engine       newGame, legalMoves, applyMove, afterMove, nextTurn, resolveRoll   (pure)
   ├─ AI           threats, threatProb, scoreMove, aiChoose
   ├─ sound        Snd (WebAudio synth)
   ├─ board SVG    buildBoard, tokenSVG, setTok
   ├─ animations   animateDice, animateHop, animateCapture, confetti
   ├─ rendering    renderTokens, renderHL, buildPanels/renderPanels, renderStatus, renderMeta
   ├─ turn flow    doRoll → resolveRoll → performMove → afterStep → schedule (AI timers)
   ├─ undo/pause/game over · modals · stats · win screen
   ├─ setup · settings drawer · about · topbar · keyboard · router (#setup #game #about)
   └─ window.Ludo  startGame, simulate, forceWin, selfTest (headless 200-turn invariant check)
```

The engine never touches the DOM: `legalMoves`, `applyMove`, `afterMove` and `nextTurn` operate on a plain `G` object, which is what makes `selfTest()` and a future server-side authority trivial.

## Domain logic notes

- **Positions.** Each token is `-1` (base), `0..50` (relative loop position from its own start), `51..55` (home column) or `56` (finished). Absolute loop square = `(OFFSET[colour] + rel) % 52` with offsets 0/13/26/39, so the same code serves all four colours. Safe squares are absolute indices {0, 8, 13, 21, 26, 34, 39, 47}.
- **Legal moves.** For each token: needs a 6 to leave base; `rel + die > 56` is illegal under exact-finish (or clamps to 56 when overshoot is allowed); landing on a non-safe square with ≥2 opponent tokens of one colour is illegal when blocks are on; otherwise opponents there are captured.
- **Turn flow.** `resolveRoll` returns `forfeit` (third six), `none` (no move — a six still re-rolls) or the move list. `afterMove` grants another roll on a six, a capture or (optionally) a finish, detects game over when `winOrder.length ≥ active − 1`, else `nextTurn` skips empty seats and finished players.
- **AI.** `threats()` lists opponent tokens 1–6 squares behind a square that could still reach it without turning into their home column; the hard level multiplies that count/6 by the token's progress as expected loss.
- **Undo** stores a JSON snapshot before every human move (casual mode) — AI replies made since are rolled back too, as in a friendly table game.

## Left for students

| Extension | Difficulty | Start here |
|---|---|---|
| Online multiplayer (WebRTC data channels or WebSocket rooms, state sync on join) | ★★★ | `performMove()` — broadcast `mv`, apply remote moves |
| Stronger AI: expectimax over the six die outcomes, or Monte-Carlo rollouts per candidate move | ★★★ | `aiChoose()` — the engine is pure, so copies of `G` can be rolled out |
| Tournaments and leaderboards with accounts and Elo | ★★ | `recordResult()` |
| Custom boards and token skins with a picker in Settings | ★★ | `buildBoard()`, `tokenSVG()` |
| Screen-reader move announcements (sentence per hop/capture/finish, toggle) | ★ | `announce()` |
| Replay viewer with a scrubber | ★★ | `G.history` — rebuild from `newGame()` |
| Spectator mode (read-only board for a joining viewer) | ★★ | `startGame()` + the networking layer |

Search the source for `TODO(student)` to find the exact hooks.

## Data / safety notes

No personal data leaves the device; names are local labels only. Dice use the browser's CSPRNG, so results cannot be steered by the page. The recent-results list is seeded with three sample games so the setup screen looks alive; they are replaced as real games are played. Nothing here is a gambling product — there is no stake, currency or payout logic, and students adding online play should keep it that way.
