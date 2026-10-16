# 19 · Rickshaw Rush (idea #74)

**A Karachi-at-dusk endless rickshaw runner, drawn entirely with canvas vectors.** Three lanes, buses two lanes wide, cows that move for your horn, fares to deliver, fuel to find, a truck-art garage, a seeded daily challenge with ghost replay, daily goals, a trip log with charts and a printable driver report. English / اردو.

**Scale:** 4213 lines in one HTML file · 16 modules (engine, renderer, audio, HUD, menu, garage, leaderboard, daily + ghost, goals, inbox, trip log, report, settings, about, tour, test hooks) · 106 seed records (10 + 5 leaderboard rows, 14 sample runs, 12 goal templates, 14 billboards, 6 shop signs, 12 passenger names, 12 destinations, 10 achievements, 6 skins, 5 upgrades).

## Getting started

1. Open `index.html` in Chrome, Edge, Firefox or Safari. Everything works offline from `file://` (Google Fonts fall back to system fonts without network). No build step, no server.
2. Keyboard: `← →` or `A D` change lane, `↑` / `W` / `Space` honk, `P` / `Esc` pause, `?` opens the controls sheet. Touch: swipe or tap the left/right half, tap the top band to honk (switch to swipe-only in Settings).
3. Sound uses WebAudio and starts after the first key press or tap. Haptics use `navigator.vibrate` where supported.
4. All state lives in `localStorage` under the `rush:` prefix. Export or import a JSON backup from Settings; **Reset all data** wipes every key. Sample leaderboard rows and sample runs are flagged and can be hidden with the *Show sample data* switch.
5. The first open launches a seven-step onboarding tour over the menu; replay it from Settings.

## The problem

Casual mobile games made in Pakistan are rare, and most student game projects stall on asset production. This template shows that a complete, polished arcade game can be built with procedural vector art, a 120 Hz fixed-timestep loop and plain `<canvas>` — then wrapped in the retention features a real release needs (daily seed, goals, ghosts, stats), all in one file a student can read end to end.

## What is built

| Area | Features |
|---|---|
| Road & world | Pseudo-3D three-lane road with perspective projection, gentle curves, scrolling kerb stripes and dashes, parallax skyline (two procedural building layers, lit windows, rooftop billboards in Urdu/English, a dome and TV tower), roadside palms, lamp posts, flyover pillars, shops with signs, bus shelters |
| Day cycle & weather | Day → dusk → night palette interpolation, sun/moon arc, stars, night tint with glowing lights and headlight cone, rain events with streaks, wet-road sheen and reflected lights |
| Player | Procedural rickshaw (canopy, three wheels, fringe pom-poms, passenger silhouettes, number plate), six truck-art panel patterns, lane tween with lean, speed-breaker bob, shield bubble, nitro flames, horn waves, crash spin with slow motion |
| Traffic | Cars, two-lane buses (W-11), Suzuki pickups, weaving motorcycles, donkey carts, cows, potholes, speed breakers, construction barriers. Waves never block all three lanes; a sweeper removes walls formed by moving traffic; waves needing two lane changes get extra room |
| Pickups & power-ups | Named passengers at bus stops (max 3, fare paid after 250–520 m, destination shown), coin rows, fuel cans (more likely when low), Horn blast, Nitro (speed + invulnerability), Shield (duration upgradable) |
| Scoring | Distance, fares, coins, near-miss bonus, combo multiplier up to ×3, difficulty ramp over 5 km, fuel gauge game over |
| Garage | Five upgrades × five levels (engine, handling, tank, horn range, shield) with stat bars and live previews; six skins unlocked by distance or coins, rendered with the same `drawRickshaw()` |
| Daily challenge | Date-seeded run with three RNG streams (traffic, world, misc) so everyone meets the same traffic; seed code, best-of-day, copy-to-clipboard result, separate daily leaderboard |
| Ghost replay | Your best daily run is sampled every 0.25 s and replayed as a translucent ghost rickshaw; "Ghost beaten!" when you pass its final distance |
| Practice drive | No fuel drain, collisions become smashes, nothing is saved — for learning the obstacles |
| Daily goals | Three goals per day picked from 12 templates by date seed (per-run or cumulative metrics), progress bars on the menu, coin rewards, inbox notification on completion |
| Notifications inbox | Persistent in-app inbox with read state, unread badge on the menu, daily reminder, records, achievements, skins and goals; tapping a message opens the relevant screen |
| Trip log | Every run logged (score, distance, fares, cause, duration, max speed, rain, lane usage); hand-drawn SVG line, bar and donut charts plus lane-usage bars; sample runs seeded |
| Driver report | Print-friendly page (summary KPIs, today's goals, last 20 runs, top-10 board, achievements) with print CSS that hides everything else |
| Leaderboard & stats | Top-10 initials boards for endless and daily, 12 lifetime stats, 10 achievements |
| Settings | Sound + volume, haptics, reduce motion, screen shake, colour-blind markers, HUD size, touch scheme, sample-data toggle, language, controls sheet, tour replay, export/import/reset |
| Audio | WebAudio-synthesised two-stroke engine hum that pitch-shifts with speed, horn, coin, pickup, fare chime, near-miss whoosh, bump, crash, sputter |
| Engine quality | `requestAnimationFrame` render with 120 Hz fixed-step physics, pause on tab hide, DPR-aware resize with letterboxing, zero external assets, `window.game` test hooks with a headless `simulate()` stress check |

## Architecture (single file)

```
index.html
├─ <style>        design tokens, motion kit, HUD, truck-art panels, garage, tables, charts, tour, print CSS
├─ <svg sprite>   inline icon symbols (no emoji)
├─ #stage         letterboxed <canvas> + HTML HUD overlay + touch hint
├─ <section>s     menu · garage · board · daily · goals · inbox · trips · report · settings · about · pause · over · tour
└─ <script>
   ├─ 1 utils       $, store, esc, ic, toast, rng, hash, countUp, drawPath, colour helpers
   ├─ 2 i18n        L.en / L.ur, t()
   ├─ 3 data        UPG, SKINS, ACH, GOAL_POOL, SEED_BOARD(_D), SEED_RUNS, BILLBOARDS, names
   ├─ 4 profile     P (coins, upgrades, skins, stats, settings), BOARD, RUNS, INBOX
   ├─ 5 engine      resize/proj, TYPES, G run state, spawnScenery/spawnWave/sweep, collect/interact/honk, stepPlay/stepOver/stepWorld
   ├─ 6 render      palette, CITY, drawSky/drawCity/drawRoad, scenery, box3d + vehicles, rickshaw + patterns, ghost, lights/rain/floats
   ├─ 7 audio       audioInit, engineOn/engineTick, tone/noise, sfx
   ├─ 8 HUD         updateHUD (diffed DOM writes)
   ├─ 9 screens     renderMenu/Garage/Board/Daily/Settings/About, showOver, finishRun
   ├─ 10 modules    inbox, goals, trip log + SVG charts, report, ghost storage, tour
   └─ 11 input · loop · route · boot · window.game test hooks
```

## Module map

| Module | Key functions | Storage keys |
|---|---|---|
| Engine | `resize`, `proj`, `spawnWave`, `sweep`, `interact`, `collect`, `honk`, `stepPlay`, `stepOver`, `stepWorld` | — |
| Renderer | `render`, `drawSky`, `drawCity`, `drawRoad`, `drawScenery`, `drawObj`, `drawRickshaw`, `panelPattern`, `drawGhost`, `drawLights` | — |
| Audio | `audioInit`, `engineOn`, `engineTick`, `sfx` | `rush:profile` (settings.sound, vol) |
| Profile & garage | `loadProfile`, `saveP`, `renderGarage`, `buyUpgrade`, `chooseSkin`, `checkSkinUnlocks`, `unlockAch` | `rush:profile` |
| Leaderboards | `renderBoard`, `qualifies`, `saveInitials` | `rush:board`, `rush:boardDaily`, `rush:ini` |
| Daily challenge | `renderDaily`, `dailySeedCode`, `resetRun(daily)` | `rush:daily` |
| Ghost replay | `recordGhost`, `saveGhostIfBest`, `loadGhost`, `ghostPose`, `drawGhost` | `rush:ghost` |
| Daily goals | `goalsState`, `updateGoals`, `runSummary`, `renderGoals`, `renderGoalStrip` | `rush:goals` |
| Notifications | `notify`, `ensureInbox`, `ensureDailyNotice`, `renderInbox`, `updateInboxBadge` | `rush:inbox` |
| Trip log | `logRun`, `renderTrips`, `tripCharts`, `svgLine`, `svgBars`, `svgDonut` | `rush:runs` |
| Driver report | `renderReport`, print CSS | reads all of the above |
| Settings | `renderSettings`, `applyPrefs`, `setLang`, `exportBackup`, `importBackup`, `resetAll` | `rush:profile` + every key on export |
| Onboarding tour | `startTour`, `showTourStep`, `positionTour`, `endTour` | `rush:tourDone` |
| Test hooks | `window.game.simulate`, `demoTraffic`, `forceGameOver` | — |

## Domain logic notes

* **Projection.** An object `z` metres ahead of the player sits at camera distance `z + PD`; its scale is `s = CAM / (z + PD + CAM)`. Screen `y = horizonY + (H − horizonY)·s`, lane offset `x = xoff · roadHalf · s`, and the road curve adds `curve · W · (1 − s)²`. Everything — lanes, vehicles, scenery, the headlight cone, the ghost — goes through `proj()`.
* **Fair spawning.** Each wave blocks one or two lanes (a bus counts as two). The free lane is compared with the previous wave's free lanes; if reaching it needs two lane changes, the wave is pushed 45 m further out. Because vehicles move at different speeds they can still bunch up, so `sweep()` scans 15 m windows beyond 45 m and deletes one vehicle whenever all three lanes are covered.
* **Collision model.** Lateral overlap `|o.x − G.x| < (o.w + PW)/2` in lane units and longitudinal overlap `|o.z| < (len + PLEN)/2` in metres. A near miss is a crash obstacle that passes with lateral clearance under 0.16 lane units.
* **Determinism.** Traffic draws from `G.rs`, world events (scenery, weather, curves) from `G.rw`, and incidental values (fares, bike weaving) from `G.rm`. The traffic stream only advances per wave, so two players on the same seed meet the same waves regardless of frame rate or speed differences; this is also what makes the ghost's lane log line up with the road.
* **Fixed timestep.** The loop accumulates real time and runs `step(1/120)` up to 12 times per frame; slow motion scales the physics `dt` while the game-over timer uses real `dt`.
* **Goals.** Three templates are chosen with `rng(hash('goals-' + date))`. Per-run metrics keep the best single run; day metrics accumulate. Rewards are paid once and announced through the inbox.

## Left for students

| Extension | Difficulty | Start here |
|---|---|---|
| Sprite / art pipeline: real illustrations in a sprite atlas, same `proj()` placement | ★★ | `drawObj()`, `drawRickshaw()` |
| Online leaderboard and friends' ghosts (server stores runs + ghost samples) | ★★★ | `saveGhostIfBest()`, `drawGhost()`, `saveInitials()` |
| More cities as biomes (Lahore, Islamabad, Peshawar palettes and scenery) | ★★ | `SKY`, `CITY`, `spawnScenery()` |
| Level / mission mode with finite scripted routes | ★★ | `spawnWave()`, `finishRun()` |
| Urdu voice lines for passengers via WebAudio buffers | ★ | `collect()` case `'pax'`, `sfx()` |
| Low-end Android profiling and a quality setting | ★★ | `resize()`, `drawRain()`, `drawLights()` |
| Difficulty tuning dashboard with live sliders | ★★ | `spawnWave()`, `stepPlay()` |

Search the source for `TODO(student)` to find the exact hooks.

## Testing

`node check.mjs index.html[#route] <name> ["js"]` was run for 17 states (tour, start, garage, leaderboard, daily, goals, inbox, trips, report, settings, about, Urdu menu, run in progress, night + rain + colour-blind markers, daily run with ghost, practice drive, game over) at 1366×850 and 390×844 with no console errors and no horizontal overflow. `window.game.simulate(60, { randomLanes: true })` runs 7,200 fixed physics steps headlessly and reports crashes, lane changes, errors and step timing.

## Data and safety notes

Nothing leaves the device: there is no network code, analytics or account. Seed data is illustrative. The daily seed is derived from the local date, so players in different time zones may roll over at different hours; a server clock is the student extension.
