# 14 · Algorithm Visualizer (idea #52)

**See the algorithm move, not just its Big-O.** An offline, single-file visual lab for sorting, pathfinding, graph and data-structure algorithms with an IDE look: a canvas stage, a pseudo-code pane whose current line lights up in sync, and a transport bar that lets you play, pause, scrub, step backwards and race two algorithms on identical input.

## Getting started

1. Open `index.html` in Chrome, Edge, Firefox or Safari. Everything works from `file://` and offline; only Google Fonts (Manrope + Fira Code) load from the network and fall back to system fonts.
2. No build step and no server needed. There is no camera, GPS or notification feature, so serving over HTTP is optional.
3. Data lives in `localStorage` under the `algoviz:` prefix: `settings` (category, algorithm, sizes, seeds, speed), `graph` (your edited graph) and `quiz` (best score + last 20 attempts). Reset everything from the About panel (the `?` button) → **Reset saved data**.
4. Share a run: the URL hash carries the category, algorithm, size, seed and distribution (`#sorting/quick?size=60&seed=4242&dist=random&race=merge`). The share button copies it to the clipboard; opening it reproduces the exact input.
5. Keyboard: `Space` play / pause · `←` `→` step · `R` reset · `Esc` closes the About panel.

## The problem

Students memorise complexity tables without ever watching an algorithm run, so "n log n" stays an abstract label. Existing visualizers are online-only, English-heavy and closed: you cannot step backwards, compare two algorithms on the same data, or add your own algorithm. This template is a single HTML file that works offline on a lab PC or a phone, and whose architecture makes adding an algorithm a ten-line job.

## What is built

| Area | Features |
|---|---|
| Sorting | Bubble, insertion, selection, merge, quick (Lomuto), heap, counting, radix (LSD). Bar-chart canvas with compare / swap-write / pivot / sorted colour states, smooth bar tweening and a glow pulse on compared bars. Counters for comparisons, swaps, array writes, elapsed and total steps. Input: size 10–200, random / nearly sorted / reversed / few unique / custom comma list, deterministic seed, shuffle. Complexity badges (best / average / worst / space / stable). **Race mode**: two algorithms on split canvases over the same input, live counters per racer, code pane switchable between A and B, winner banner when the first finishes. |
| Pathfinding | Grid 15×25 → 30×50 (size slider), draw walls / mud (cost 5) / erase by dragging, drag S and E (the path recomputes live when a run has finished), maze generators (recursive division, random, clear), BFS, DFS, Dijkstra, A* (Manhattan / Euclidean), Greedy best-first, bidirectional BFS, diagonal-movement toggle without corner cutting, visited / frontier / path animation with a glowing path polyline, stats (visited, frontier, path length, path cost). |
| Graphs | Node-link canvas: click to add nodes, drag node → node to add an edge (weight from the "new weight" box), click an edge weight to edit or delete it, "drag = move" mode, pan by dragging empty space, auto-fit until you pan, delete node, directed toggle, presets (weighted graph, DAG, tree), persisted to localStorage. Algorithms: BFS, DFS (visiting order), Dijkstra (live distance table + distances drawn above nodes), Prim and Kruskal MST (union-find `parent[]` table), topological sort (Kahn, with cycle detection and in-degree table), connected components (coloured). Adjacency-list panel. |
| Data structures | Stack and queue with push / pop / enqueue / dequeue motion and overflow / underflow checks; binary search tree insert / search / delete (in-order successor) and in / pre / post-order traversal numbering; binary search with lo / mid / hi pointers and dead-zone dimming; min-heap insert (sift-up) and extract-min (sift-down) drawn as a gliding tree plus the array view. All driven by the same pseudo-code pane. |
| Learn | Complexity reference (sorting, graph & pathfinding, data-structure operations), a 10-question "which algorithm?" quiz with explanations, score, stored best and recent attempts, and a hoverable log-scale growth chart for n, log n, n log n, n², 2ⁿ. |
| Shell | Hash routing, share links, keyboard shortcuts, animated tab underline, collapsible code pane on phones, toasts, About panel with the extension pattern and the student TODO list, print CSS. |

## Architecture (single file)

```
index.html
├─ <style>        IDE tokens (#13111c / #1b1826, syntax palette), motion kit, components, responsive rules, print
├─ <svg sprite>   inline icon symbols (Lucide-style, no emoji)
├─ <header>       brand mark, category tabs + sliding ink bar, share, about
├─ <div.work>     <main #stage> (rendered per category)  ·  <aside #codepane> (title, badges, code, note, stats)
├─ <footer>       transport: reset · step back · play/pause · step forward · shuffle · race · scrubber · speed · size
└─ <script>
   ├─ utils        $, store (algoviz: prefix), esc, ic(), toast, rng(seed), fitCanvas (DPR aware), PQ (binary heap), motion helpers
   ├─ state        S (persisted settings), saveS()
   ├─ player       record(gen) → steps[]; P {tracks, idx, seek, play/pause, step} with apply/undo per track
   ├─ code pane    showCode(), hlLine() mini syntax highlighter
   ├─ transport    updateTransport(), slider / button wiring
   ├─ sorting      SORTS (8 generators + pseudo-code + complexity), makeInput(), sortModel/apply/undo, drawBars(), renderSorting() (incl. race)
   ├─ pathfinding  G grid, applyMaze(), nbrs(), PF_ALGOS (6 generators), pfModel/apply/undo, drawGrid(), pointer editing, renderPath()
   ├─ graphs       GR graph + presets, GR_ALGOS (7 generators), grModel/apply/undo, drawGraph(), grTable(), editor, renderGraph()
   ├─ data struct. mini auto-player, DS state, stack / queue / BST / binary search / heap operations as generators
   ├─ learn        complexity tables, QUIZ, growth chart (SVG with hover)
   └─ about/router TODOS, openAbout(), CATS, applyHash()/syncHash()/route(), keyboard shortcuts, boot
```

## The extension pattern (how every algorithm is wired)

Every algorithm is a **JavaScript generator** that works on its own copy of the data and `yield`s plain step objects:

```js
{ type: 'compare' | 'swap' | 'set' | 'visit' | 'frontier' | 'path' | 'edge' | 'dist' | 'uf' | 'order' | 'comp' | 'range' | 'pivot' | 'sorted' | 'line' | 'done',
  i, j,            // indices (sorting)         r, c, k   // cells (grid)        n, e, v, p, s   // graph node / edge / value / parent / state
  line,            // pseudo-code line to highlight
  note }           // one-sentence explanation shown under the code
```

`record(gen)` runs the generator to completion and stores the steps in an array (capped at 150 000). The player `P` keeps a *model* per track (the array, the grid overlay, the node/edge states) and two pure functions, `apply(model, step)` and `undo(model, step)`. Moving forward applies steps; moving backward undoes them (each `apply` stashes what it overwrote in `step.prev`), so scrubbing to any index is O(distance) and never re-runs the algorithm. The renderer draws only from the model plus the current step, which is why race mode is just two tracks sharing one index.

To add an algorithm, write a generator and a `code` array and register it in `SORTS`, `PF_ALGOS` or `GR_ALGOS`; dropdowns, race mode, counters and the Learn table pick it up automatically. The About panel shows a worked example.

## Domain / algorithm notes

- **Sorting input** values are integers 2–100 so that counting and radix sort have a bounded key space; custom lists accept any positive integers (first 200).
- **Step counts** are what race mode compares: one step per comparison, swap, write or marker. Counting sort therefore "wins" on comparisons but still pays for reads and writes.
- **Grid costs**: mud is weight 5, diagonal moves multiply by √2 and are blocked when either orthogonal neighbour is a wall. Manhattan is inadmissible with diagonals enabled — a good classroom discussion point.
- **Recursive division** places walls on odd rows/columns and gaps on even ones, which guarantees the maze stays connected.
- **Dijkstra / Prim** use a binary-heap priority queue with lazy deletion (stale entries are skipped when popped).
- **Kruskal** uses union-find without path compression on purpose so the `parent[]` table stays readable.
- **Topological sort** needs a directed graph; on an undirected graph it explains why and stops.
- **Growth chart** uses a log₁₀ y-axis (values below 1 are clamped to 1) so 2ⁿ at n = 32 fits on the same picture as log n.

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

Nothing leaves the browser: no network calls besides fonts, no analytics, no accounts. Saved settings, the user-drawn graph and quiz attempts are plain JSON in localStorage and can be wiped from the About panel. Step recording is capped at 150 000 steps per algorithm (bubble sort on 200 reversed values is about 40 000), so the page stays responsive even at the largest sizes.

## Evaluation ideas for the FYP report

Pre/post test on complexity questions with and without the visualizer; measure how many students can predict the next step after one session; compare race-mode counters against textbook complexities across input distributions; usability study of the pathfinding editor on phones.
