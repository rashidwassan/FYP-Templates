# 14 · Algorithm Visualizer (idea #52)

**See the algorithm move, not just its Big-O.** An offline, single-file visual lab for sorting, pathfinding, graph and data-structure algorithms with an IDE look: a canvas stage, a pseudo-code pane whose current line lights up in sync, and a transport bar that lets you play, pause, scrub, step backwards, race two algorithms on identical input, benchmark them without animation and print a lab report.

**Scale:** 5,900+ lines in one HTML file (one statement per line, functions under ~60 lines) · 19 modules · 151 seed records (8 sorting + 6 pathfinding + 7 graph algorithms, 5 data structures, 9 input distributions, 8 maps, 8 graph presets, 26 quiz questions, 31 glossary terms, 18 algorithm cards, 10 badges, 7 tour steps, 3 inbox messages, 5 saved runs).

## Getting started

1. Open `index.html` in Chrome, Edge, Firefox or Safari. Everything works from `file://` and offline; only Google Fonts (Manrope + Fira Code) load from the network and fall back to system fonts.
2. No build step and no server needed. There is no camera, GPS or notification feature, so serving over HTTP is optional.
3. A short onboarding tour starts on the first visit (replay it from Settings → Help or the About panel).
4. Data lives in `localStorage` under the `algoviz:` prefix (see the module map below). **Settings → Data & privacy** exports everything as one JSON file, imports it back on another machine, shows per-key storage use and has a two-click **Reset saved data**.
5. Share a run: the URL hash carries category, algorithm, size, seed and distribution (`#sorting/quick?size=60&seed=4242&dist=random&race=merge`). The share button copies it; the bookmark button saves it to your library with an editable name.
6. Keyboard: `Space` play / pause · `←` `→` step · `R` reset · `S` shuffle · `P` lab report · `B` save run · `I` inbox · `/` search · `1–6` labs · `?` shortcut sheet · `Esc` closes dialogs.

## The problem

Students memorise complexity tables without ever watching an algorithm run, so "n log n" stays an abstract label. Existing visualizers are online-only, English-heavy and closed: you cannot step backwards, compare two algorithms on the same data, measure them, or add your own algorithm. This template is a single HTML file that works offline on a lab PC or a phone, and whose architecture makes adding an algorithm a ten-line job.

## What is built

| Area | Features |
|---|---|
| Sorting | Bubble, insertion, selection, merge, quick (Lomuto), heap, counting, radix (LSD). Bar-chart canvas with compare / swap-write / pivot / sorted colour states, smooth bar tweening and a glow pulse on compared bars. Counters for comparisons, swaps, array writes, elapsed and total steps. Input: size 10–200, nine distributions (random, nearly sorted, reversed, few unique, already sorted, organ pipe, sawtooth, one swap away, custom comma list), deterministic seed, shuffle. Complexity badges. **Race mode**: two algorithms on split canvases over the same input, live counters per racer, code pane switchable between A and B, winner banner. |
| Pathfinding | Grid 15×25 → 30×50, draw walls / mud (cost 5) / erase by dragging, drag S and E (the path recomputes live when a run has finished), eight maps (recursive division, random scatter, two rooms, spiral, vertical bars, diagonal wall, crossroads, empty), BFS, DFS, Dijkstra, A* (Manhattan / Euclidean), greedy best-first, bidirectional BFS, diagonal moves without corner cutting, visited / frontier / path animation with a glowing path polyline, stats. |
| Graphs | Node-link canvas: click to add nodes, drag node → node to add a weighted edge, click an edge weight to edit or delete it, "drag = move" mode, pan, auto-fit until you pan, delete node, directed toggle, eight presets (weighted, DAG, tree, 3×3 mesh, complete K6, two islands, directed with a cycle, bipartite), persisted graph. Algorithms: BFS, DFS, Dijkstra (live distance table), Prim and Kruskal (union-find `parent[]` table), topological sort (Kahn, with cycle detection and in-degree table), connected components. Adjacency-list panel. |
| Data structures | Stack and queue with push / pop / enqueue / dequeue motion and overflow checks; BST insert / search / delete (in-order successor) and in / pre / post-order numbering; binary search with lo / mid / hi pointers; min-heap insert and extract-min drawn as a gliding tree plus the array view. |
| Compare (benchmark) | Counts comparisons, swaps + writes or total steps for any subset of the eight sorts across sizes 16–512 and any distribution, instantly and without animation. Log-scale SVG chart with dashed n log n and n² reference curves, results table with growth ratios classified as linear / n log n / quadratic, CSV download, printable report. |
| Learn | Complexity reference tables, 18 algorithm cards (inventor, idea, use, pitfall, "open in the lab"), a 31-term glossary with alphabet filter, a quiz drawing 10 of 26 questions with explanations, stored best score and attempts, printable certificate, and a hoverable log-scale growth chart. |
| Reports | Printable lab reports for sorting (incl. races), pathfinding, graphs, benchmarks and quiz results: KPIs, static SVG snapshot of the canvas, pseudo-code listing, first 30 steps of the trace, share link. Print CSS hides the app and prints only the report. |
| Inbox & activity | Bell with unread badge; completed runs, race winners, no-path warnings, quiz results, benchmark runs and badge unlocks arrive as messages with read state, delete, mark-all-read and deep links. Same drawer holds **Saved runs** (rename / open / remove) and **History** (last 40 runs opened). |
| Badges | Ten one-shot achievements (first race, 500 steps/s, every lab visited, perfect quiz, 50 cells drawn, 20-edge graph, three quiz attempts, benchmark run, eight saved runs, first report). |
| Settings | Bar labels, colour-blind safe palette (Okabe–Ito), reduced motion, text and pseudo-code size, canvas hints, autoplay, run toasts, aria-live step announcements, JSON export / import, storage usage, reset, badge gallery, help links. |
| Shell | Hash routing, global search across algorithms / structures / presets / maps / glossary / cards / pages / saved runs with keyboard navigation, onboarding tour with spotlight, shortcut sheet, animated tab underline, collapsible code pane on phones, toasts, About panel with the extension pattern and student TODO list. |

## Architecture (single file)

```
index.html
├─ <style>        IDE tokens (#13111c / #1b1826, syntax palette as CSS variables), motion kit, components, drawer, tour, report & print CSS
├─ <svg sprite>   inline icon symbols (Lucide-style, no emoji)
├─ <header>       brand mark, six category tabs + sliding ink bar, search box, save, share, inbox (badge), settings, about
├─ <div.work>     <main #stage> (rendered per category)  ·  <aside #codepane> (title, badges, code, note, stats)
├─ <footer>       transport: reset · step back · play/pause · step forward · shuffle · race · report · scrubber · speed · size
├─ overlays       drawer (inbox / saved / history), about, shortcut sheet, report modal, tour spotlight, aria-live region, toasts
└─ <script>
   ├─ utils            $, store (algoviz: prefix), esc, ic(), toast, rng(seed), timeAgo, downloadText, fitCanvas, PQ, motion helpers
   ├─ palette          PALETTES, COL, applyPalette()
   ├─ state            DEFAULTS, loadState() (deep-merged), S, saveS()
   ├─ player           record(gen) → steps[]; P {tracks, idx, seek, play/pause, step, loop}
   ├─ code pane        showCode(), hlLine(), announce()
   ├─ transport        updateTransport(), bindTransport(), resetCurrent(), shuffleCurrent()
   ├─ inbox/library    notify(), unlock() badges, markSeen(), addHistory(), saveCurrentRun(), runLabel()
   ├─ sorting          8 generators, SORTS, DISTS + inputFor(), sortModel/apply/undo, drawBars(), renderSorting()
   ├─ pathfinding      G grid, 7 map builders, nbrs(), 6 generators, pfModel/apply/undo, drawGrid(), grid editing, renderPath()
   ├─ graphs           GRAPH_PRESETS (8), 7 generators, grModel/apply/undo, drawGraph(), grTable(), editor, renderGraph()
   ├─ data structures  mini auto-player, stack / queue / BST / binary search / heap generators and renderers
   ├─ benchmark        benchCount(), runBenchmark(), growthLabel(), chart + table + CSV, renderBench()
   ├─ learn            tables, ALGO_CARDS, GLOSSARY, QUIZ (26), growth chart, renderLearn()
   ├─ reports          sortingReport(), pathReport(), graphReport(), benchReport(), quizReport(), openReport()
   ├─ settings         applyPrefs(), exportData(), importData(), wipeData(), renderSettings()
   ├─ drawer           openDrawer(), renderDrawer(), inbox / library / history lists
   ├─ search           searchIndex(), runSearch(), keyboard navigation
   ├─ tour             TOUR_STEPS, startTour(), positionTour()
   └─ about/router     TODOS, openAbout(), openSheet(), CATS, applyHash()/syncHash()/route(), onKey(), boot()
```

## Module map

| Module | Entry points | Storage keys |
|---|---|---|
| Player & transport | `record`, `P.load/seek/play`, `updateTransport`, `bindTransport` | `algoviz:settings` (speed) |
| Sorting lab | `renderSorting`, `SORTS`, `inputFor`, `drawBars`, `onSortingDone` | `settings.sort` |
| Pathfinding lab | `renderPath`, `PF_ALGOS`, `applyMaze`, `pfRebuild`, `bindGridEditing` | `settings.pf` |
| Graph lab | `renderGraph`, `GR_ALGOS`, `GRAPH_PRESETS`, `grRebuild`, `bindGraphEditing` | `settings.gr`, `algoviz:graph` |
| Data structures | `renderDS`, `mini`, `dsStack/dsQueue/dsBST/dsBSearch/dsHeap` | `settings.ds` |
| Compare (benchmark) | `renderBench`, `runBenchmark`, `benchCount`, `benchCsv` | `settings.bench` |
| Learn | `renderLearn`, `learnTable/learnCards/learnGlossary/learnQuiz/learnGrowth` | `settings.learn`, `algoviz:quiz` |
| Reports | `openReport`, `*Report()` builders, `barsSvg/gridSvg/graphSvg` | — |
| Inbox & badges | `notify`, `markRead`, `unlock`, `markSeen`, `renderInboxDot` | `algoviz:inbox`, `algoviz:achv`, `settings.seen` |
| Library & history | `saveCurrentRun`, `libraryList`, `addHistory` | `algoviz:library`, `algoviz:history` |
| Drawer | `openDrawer`, `renderDrawer`, `bindDrawer` | — |
| Search | `searchIndex`, `runSearch`, `openSearchResult` | — |
| Settings | `renderSettings`, `applyPrefs`, `exportData`, `importData`, `wipeData` | `settings.prefs`, all keys on export |
| Tour | `startTour`, `showTourStep`, `positionTour`, `endTour` | `algoviz:tour` |
| Shell | `route`, `applyHash`, `syncHash`, `onKey`, `openAbout`, `openSheet`, `boot` | `settings.cat` |

## The extension pattern (how every algorithm is wired)

Every algorithm is a **JavaScript generator** that works on its own copy of the data and `yield`s plain step objects:

```js
{ type: 'compare' | 'swap' | 'set' | 'visit' | 'frontier' | 'path' | 'edge' | 'dist' | 'uf' | 'order' | 'comp' | 'range' | 'pivot' | 'sorted' | 'line' | 'done',
  i, j,            // indices (sorting)         r, c, k   // cells (grid)        n, e, v, p, s   // graph node / edge / value / parent / state
  line,            // pseudo-code line to highlight
  note }           // one-sentence explanation shown under the code
```

`record(gen)` runs the generator to completion and stores the steps in an array (capped at 150 000). The player `P` keeps a *model* per track (the array, the grid overlay, the node/edge states) and two pure functions, `apply(model, step)` and `undo(model, step)`. Moving forward applies steps; moving backward undoes them (each `apply` stashes what it overwrote in `step.prev`), so scrubbing to any index is O(distance) and never re-runs the algorithm. The renderer draws only from the model plus the current step, which is why race mode is just two tracks sharing one index, the benchmark is just iterating generators and tallying step types, and a report is just the model drawn once as static SVG.

To add an algorithm, write a generator and a `code` array and register it in `SORTS`, `PF_ALGOS` or `GR_ALGOS`; dropdowns, race mode, the benchmark, search and the Learn table pick it up automatically. The About panel shows a worked example.

## Domain / algorithm notes

- **Sorting input** values are integers 2–100 so that counting and radix sort have a bounded key space; custom lists accept any positive integers (first 200).
- **Step counts** are what race mode compares: one step per comparison, swap, write or marker. Counting sort therefore "wins" on comparisons but still pays for reads and writes — switch the benchmark metric to see it.
- **Benchmark growth ratios** compare the two largest sizes: doubling n gives ×2 for linear, ≈×2.2 for n log n and ×4 for quadratic; the dashed reference curves are scaled to the mean value at the smallest size.
- **Grid costs**: mud is weight 5, diagonal moves multiply by √2 and are blocked when either orthogonal neighbour is a wall. Manhattan is inadmissible with diagonals enabled — a good classroom discussion point.
- **Recursive division** places walls on odd rows/columns and gaps on even ones, which guarantees the maze stays connected. The other maps are deterministic shapes seeded for door and gap positions; start and end cells are always cleared.
- **Dijkstra / Prim** use a binary-heap priority queue with lazy deletion (stale entries are skipped when popped).
- **Kruskal** uses union-find without path compression on purpose so the `parent[]` table stays readable.
- **Topological sort** needs a directed graph; on an undirected graph it explains why and stops. The "directed with a cycle" preset shows the cycle detection.
- **Growth chart** uses a log₁₀ y-axis (values below 1 are clamped to 1) so 2ⁿ at n = 32 fits on the same picture as log n.
- **Badges and inbox** are plain arrays in localStorage; `notify()` is the single entry point, so a backend could later mirror it.

## Left for students

| Extension | Difficulty | Start here |
|---|---|---|
| Dynamic-programming tables (LCS, 0/1 knapsack) as a filled grid of `set` steps | ★★ | new category next to `SORTS`; reuse `record()` + `P.load()` |
| String matching (KMP failure function, Rabin-Karp rolling hash) | ★★ | new generators + a text/pattern renderer |
| Sandbox that runs the user's own JavaScript with automatic compare/swap instrumentation (Proxy array in a Web Worker) | ★★★ | `runUserCode()` |
| Urdu step explanations and spoken narration (`speechSynthesis`, ur-PK voice) | ★★ | `narrate()`, step `note` templates |
| Auto-graded exercises ("predict the next swap", "which line runs next?") scored against the step list | ★★ | Learn tab, `P.tracks[0].steps` |
| GIF / video export of a run (`canvas.captureStream()` + `MediaRecorder`) | ★★ | `exportRun()` |
| More algorithms: Shell / Tim sort, Bellman-Ford, Floyd–Warshall, Jump Point Search, more maze generators | ★ | `SORTS`, `GR_ALGOS`, `PF_ALGOS`, `applyMaze()` |

Search the source for `TODO(student)` to find the exact hooks.

## Data / safety notes

Nothing leaves the browser: no network calls besides fonts, no analytics, no accounts. Saved settings, the user-drawn graph, inbox, library, history, badges and quiz attempts are plain JSON in localStorage; **Settings → Export JSON** writes them to one file (`{ app: 'algoviz', version: 2, data }`) and import validates that envelope before writing anything. Step recording is capped at 150 000 steps per algorithm (bubble sort on 200 reversed values is about 40 000), and the benchmark counts without storing steps, so the page stays responsive at every size.

## Evaluation ideas for the FYP report

Pre/post test on complexity questions with and without the visualizer; measure how many students can predict the next step after one session; compare benchmark growth ratios against textbook complexities across input distributions; usability study of the pathfinding editor on phones; analyse inbox/badge data as an engagement signal.
