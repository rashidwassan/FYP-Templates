# 16 · Nuqta — Mind Map & Flowchart Editor (idea #59)

**Scale:** about 4,870 lines in one `index.html` (≈4,380 lines of JavaScript), 30 modules (17 of them new or rebuilt in this expansion), 10 templates seeding 122 nodes, 118 connectors, 2 sticky notes and 2 comments, plus 30 node icons and 6 themes.

**An offline, single-file diagram editor.** Infinite SVG canvas, mind-map mode with keyboard-driven branching and animated auto-layout, flowchart mode with nine shapes and a Sugiyama-style layered layout (cycle removal, dummy nodes, barycentric crossing reduction, lane routing), sticky notes, threaded comments, image nodes, node icons, an outline panel synced with the canvas, find & replace, named version history, branch-by-branch presentation mode, Mermaid import/export, undo/redo, multiple documents and export to SVG, PNG, JSON, Markdown or Mermaid. No libraries.

## Getting started

1. Open `index.html`. A sample "FYP plan" mind map loads on first run with a welcome card; **Take the tour** walks through the editor in seven steps (replay it from Settings or Help).
2. Double-click the canvas to add a node; double-click a node to edit it. Select a node and press **Tab** for a child or **Enter** for a sibling. Press **L** to auto-layout, **F** to fit and **P** to present.
3. Switch to **Flowchart** in the top bar, pick shapes from the left palette, and drag from a node (connect tool or a port) to another node to link them. Double-click a connector to label it.
4. **S** drops a sticky note, **I** adds an image node (or drop image files on the canvas), **Shift+C** comments on the selected node, **O** opens the side panel (Outline, Comments, History, Activity), **Ctrl+F** opens find & replace.
5. **Documents** (folder icon) holds ten templates, saved documents, JSON and Mermaid import. **Export** (download icon) offers SVG, PNG, JSON, Markdown, Mermaid and print. **Settings** (gear) has themes, layout spacing, auto-versioning, backup/restore and reset.
6. Hash shortcuts open panels directly, e.g. `index.html#docs`, `#export`, `#settings`, `#mermaid`, `#present`, `#outline`, `#history`, `#tour`.

Everything lives in `localStorage` under the `nuqta:` prefix and works from `file://` with no network. To start over use **Settings → Reset all data**, or clear the site data in the browser.

## The problem

Students plan FYPs, write-ups and system designs in paid or online-only tools. A lightweight editor that works offline on any laptop, exports clean vectors for reports and Mermaid for Markdown/GitHub, and is small enough to read and extend is itself a good systems project: it touches graph algorithms, geometry, rendering, state management, parsing and UX.

## What is built

| Area | Features |
|---|---|
| Canvas | Infinite SVG scene with pan (space-drag / hand tool / middle mouse), cursor-centred wheel zoom, pinch zoom on touch, dot grid with configurable size, snap-to-grid, minimap (nodes and notes) with viewport, fit-to-screen, zoom controls, position readout, right-click context menu, drag-and-drop of images, `.json` and `.mmd` files |
| Nodes | 9 shapes, 8 colours, font size, width, inline text editing with word-wrap, **30 icons from the sprite**, **image nodes** (downscaled to 320 px and stored as data URLs), URL badge, notes, resize handle, collapse/expand branches, duplicate, copy/paste, nudge with arrows, marquee and shift multi-select, align in a row |
| Connectors | Curved, orthogonal and straight paths, arrowheads none/end/both, dashed style, labels placed at the path midpoint, reverse direction, drag-to-connect from ports, drag into empty space to create a connected child, **lane routing** for edges that skip layers |
| Mind-map mode | Tab/Enter branching with inherited colours, tree auto-layout growing right or **balanced left/right**, animated moves (keyed DOM patching with CSS transitions), collapse counters, Markdown outline export |
| Flowchart mode | Shape palette, **layered layout** top-to-bottom or left-to-right: DFS cycle removal, longest-path layering, dummy nodes, barycentre sweeps that keep the ordering with the fewest crossings, alternating up/down coordinate balancing |
| Sticky notes & comments | Free-floating coloured sticky notes (drag, resize, edit, convert to node); threaded comments per node with author, time, resolve/reopen, badge on the node and on the side-panel button |
| Outline panel | Editable tree synced with the canvas both ways: click to select and centre, double-click or Enter to rename, Alt+Right/Left to indent/outdent, drag rows to re-parent (with cycle protection), add/delete, expand/collapse, arrow-key focus moves the canvas selection |
| Find & replace | Searches node text, notes, connector labels and sticky notes; match case, whole word, include notes; highlights matches on the canvas, steps through them (expanding collapsed branches), replace one or all in a single undo step |
| Version history | Named snapshots per document (manual or every 10/25/50 changes), thumbnails, size diff against now, restore (the current state is saved first) and delete; up to 30 versions per document |
| Presentation | Full-canvas mode that zooms branch by branch with eased view tweens: overview, each root with its branches, then each branch subtree (flowcharts step node by node with their successors); dims everything else; arrows/space/click/Home/End/Esc, progress bar, present from a chosen node |
| Mermaid | Export `flowchart TD/LR` (shapes, labels, dashed, two-way) or `mindmap`; import flowchart/graph/mindmap with shapes, `A & B` groups, chained links, `-- text -->`, `-.->`, `==>`, comments and ignored styles; live parse preview; round-trip tested on all templates |
| Keyboard | Alt+arrows move between nodes (tree-aware in mind maps, nearest-in-direction in flowcharts), `[` `]` cycle nodes, Home jumps to the root, announcements in an `aria-live` region, structural keys only act on the canvas so Tab/Enter still work on buttons |
| History & docs | 100-step undo/redo snapshots (including notes and comments), autosave with status, pending saves flushed on document switch and page hide, multiple documents with SVG thumbnails, duplicate/delete, JSON import/export, **ten templates** |
| Export | SVG (selectable text, icon symbols inlined), 2x PNG via canvas, JSON, Markdown outline with sticky notes, Mermaid (copy or `.mmd`), print/PDF stylesheet |
| Settings & data | Six themes (light, dark, sepia, ocean, forest, high contrast), grid size, minimap, hints, animations, full screen when presenting, mind-map style, flowchart direction, layer/node spacing, author name, auto-version interval; storage usage, full backup download, restore, reset |
| Onboarding & activity | First-run welcome card, seven-step spotlight tour that skips targets hidden at the current screen size, device-wide activity log (opens, imports, versions, comments, replacements, exports) |
| UX | Tooltips, quick find in the toolbar, shortcut sheet, responsive layout with a scrollable toolbar, slide-over side panel and bottom-sheet inspector on phones, reduced-motion support |

## Module map

| Module | Main functions | Storage keys |
|---|---|---|
| Helpers | `store`, `esc`, `ic`, `toast`, `announce`, `debounce` | prefix `nuqta:` |
| Settings & themes | `saveSettings`, `applySettings`, `setTheme`, `THEMES` | `settings`, `snap`, `theme` (legacy keys kept) |
| Model | `newDoc`, `normalizeDoc`, `mkNode`, `mkEdge`, `mkSticky`, `mkComment`, `childMap`, `hiddenIds`, `descendants` | — |
| History & persistence | `snapshot`, `restore`, `undo`, `redo`, `save`, `flushSave`, `flushPending`, `loadDoc`, `openNew`, `activateDoc` | `docs`, `doc:<id>`, `cur` |
| Geometry | `anchor`, `edgePath`, `orthoPath`, `lanePath`, `lanePort`, `pathMid`, `shapePath`, `wrapText`, `bounds` | — |
| Render | `render`, `renderNodes`, `nodeMarkup`, `renderEdges`, `setEdgeGeometry`, `renderStickies`, `renderMinimap`, `applyView`, `animateView`, `viewFor`, `fit`, `zoomAt` | — |
| Tree layout | `treeLayout`, `subtreeHeight`, `placeSubtree`, `placeBalanced` | — |
| Layered layout | `acyclicEdges`, `assignLayers`, `buildLayerGraph`, `groupLayers`, `countCrossings`, `barycenterSort`, `orderLayers`, `assignCoordinates`, `applyCoordinates`, `layeredLayout`, `autoLayout` | — |
| Interactions | `onPointerDown/Move/Up`, `pressNode`, `pressSticky`, `beginMove`, `endMarquee`, `endConnect`, `hitTarget`, `startEdit`, `endEdit` | — |
| Keyboard & navigation | `onKeyDown`, `modKeys`, `structureKeys`, `toolKeys`, `navigate`, `treeNeighbor`, `spatialNeighbor`, `stepSelection`, `selectNode` | — |
| Node operations | `addNodeAt`, `addChild`, `addSibling`, `deleteSel`, `duplicate`, `copySel`, `pasteSel`, `alignSelected`, `toggleCollapse` | — |
| Inspector | `renderInspector`, `edgePanel`, `stickyPanel`, `documentPanel`, `nodePanel`, `iconGrid`, `imageField` | — |
| Sticky notes | `addStickyAt`, `stickyToNode`, `stickyMarkup` | inside `doc:<id>` |
| Comments | `addComment`, `toggleComment`, `deleteComment`, `commentThread`, `openCommentBox` | inside `doc:<id>` |
| Image nodes | `shrinkImage`, `addImageNode`, `pickImageFor` | inside `doc:<id>` |
| Side panel | `toggleSide`, `setSideTab`, `renderSide`, `updateBadge` | `settings.sideOpen`, `settings.sideTab`, `cmtAll` |
| Outline | `outlineView`, `outlineRow`, `outlineRename`, `reparent`, `indent`, `outdent`, `focusRow` | — |
| Version history | `saveVersion`, `listVersions`, `restoreVersion`, `deleteVersion`, `versionDiff`, `historyView` | `ver:<id>` |
| Activity log | `log`, `activityView` | `log` |
| Find & replace | `openFind`, `computeHits`, `gotoHit`, `revealNode`, `replaceCurrent`, `replaceAll` | — |
| Documents & templates | `TEMPLATES`, `branchTree`, `linkAll`, `openTemplate`, `openDocs`, `importJsonText`, `thumb` | `docs`, `doc:<id>` |
| Mermaid | `toMermaid`, `mermaidMindmap`, `parseMermaid`, `parseFlowchart`, `parseMindmap`, `mmNode`, `mmEdgeOp`, `importMermaid`, `openMermaidImport` | — |
| Export | `svgString`, `exportSVG`, `exportPNG`, `markdownOutline`, `exportMD`, `exportJSON`, `openExport` | — |
| Presentation | `mindSteps`, `flowSteps`, `startPresent`, `gotoStep`, `endPresent`, `presentKeys`, `presentFrom` | `settings.fullscreen` |
| Popovers | `openPop`, `closePop`, theme menu, context menu | — |
| Settings & data | `openSettings`, `bindSettings`, `storageSummary`, `exportBackup`, `importBackup`, `resetAll` | all of the above |
| Onboarding | `startTour`, `showTourStep`, `placeTourCard`, `endTour`, `showWelcome` | `tourDone` |
| Help | `openHelp`, `SHORTCUTS`, `TODOS` | — |
| Router & boot | `ROUTES`, `route`, `boot` | — |
| Public API | `window.nuqta` | — |

## Architecture (single file)

```
index.html
├─ <style>        tokens + 6 themes, motion kit, toolbar, side panel, canvas (nodes, edges, stickies),
│                 find bar, presentation bar, inspector, modals, popovers, tour, mobile sheet, print
├─ <svg sprite>   icon symbols (UI and node icons, no emoji)
├─ .app           toolbar · palette | side panel (outline/comments/history/activity) |
│                 canvas (#board > #world > stickies/edges/nodes/overlay, find bar, welcome, minimap,
│                 zoom, presentation bar) | inspector · status bar
└─ <script>
   ├─ helpers            store, esc, ic, toast, announce, debounce
   ├─ settings & themes  defaults merged with saved settings and legacy keys
   ├─ model              doc {nodes, edges, stickies, comments, view}, factories, normalizeDoc()
   ├─ history            snapshot()/restore(), undo/redo, debounced save() with flush
   ├─ geometry           anchor, edgePath (curve/ortho/straight/lane), pathMid, shapePath, wrapText
   ├─ render             keyed patching with per-element signatures; minimap; view tweening
   ├─ layout             tree (right / balanced) and layered (Sugiyama-style) layouts
   ├─ interactions       pointer state machine: move · marquee · pan · resize · connect; drop files
   ├─ keyboard           shortcuts, node navigation, structural keys scoped to the canvas
   ├─ inspector          node / connector / sticky / document panels, icons, images, comments
   ├─ side panel         outline, comments, version history, activity log
   ├─ find & replace     search fields, hits, navigation, replace one/all
   ├─ documents          10 templates, gallery with thumbnails, JSON import
   ├─ mermaid            export (flowchart, mindmap) and import parser
   ├─ export             SVG, PNG, JSON, Markdown, print
   ├─ presentation       step builders, eased zooms, dimming, keyboard and click control
   ├─ popovers           theme menu, context menu
   ├─ settings           themes, canvas, layout, collaboration, backup/restore/reset
   ├─ onboarding         welcome card and spotlight tour
   ├─ help               shortcut sheet, about, student extensions
   └─ router · boot · window.nuqta
```

## Domain logic notes

- **Document model.** `{id, name, mode, nodes, edges, stickies, comments, view, updatedAt}`. Nodes keep the original fields and gain optional `icon` and `image`; edges may carry a `lane` written by the layered layout. Documents saved by the first version are upgraded on load by `normalizeDoc()`, and the original keys (`docs`, `doc:<id>`, `cur`, `snap`, `theme`) are unchanged.
- **Rendering.** Elements are kept in id-keyed maps. Each node and edge stores a signature of its visible properties, so a render only rewrites the markup that changed and otherwise touches `transform` and `d`, which CSS transitions animate. Label positions are computed from the path string rather than `getTotalLength()`, which would read the half-transitioned path. A 320-node, 359-edge flowchart lays out in about 60 ms and re-renders in about 35 ms in headless Chromium.
- **Layered layout.** (1) DFS reverses back edges so the graph is acyclic; (2) longest-path layering; (3) edges that skip layers are split into chains of small dummy items; (4) up to eight down/up barycentre sweeps reorder each layer, keeping the ordering with the fewest crossings counted between adjacent layers; (5) layers are packed and then balanced by alternating passes that pull each item toward the mean centre of its predecessors or successors; (6) the dummy positions become a `lane`, and orthogonal edges route through it while both end nodes keep their laid-out boxes.
- **Mermaid.** A small scanner reads node references (`id`, then the first matching shape delimiter), `&` groups and edge operators (plain, `|label|` and `-- text -->` forms). Shapes map both ways: rect `[]`, rounded `()`, terminal `([])`, decision `{}`, input/output `[//]`, database `[()]`, circle `(())`, note `>]`. Mind maps use indentation; several roots are exported under a synthetic root because Mermaid allows one.
- **Presentation.** Steps are computed from the visible tree (mind maps) or from reading order and successors (flowcharts). Each step dims everything outside its focus set and tweens the view with an ease-in-out cubic to frame the step. Direct view changes (fit, zoom, pan, opening a document) cancel a running tween.
- **Versions vs undo.** Undo is an in-memory stack of 100 JSON snapshots for the session. Versions are named snapshots stored per document in `ver:<id>` (30 kept) and survive reloads; restoring first saves the current state as "Before restore", so restoring can itself be undone.

## Public API (tests and extensions)

`window.nuqta` keeps the original members (`doc`, `autoLayout`, `fit`, `openNew`, `loadDoc`, `TEMPLATES`, `setTool`, `snapshot`, `render`) and adds `view`, `selection`, `settings`, `undo`, `redo`, `addNode`, `addEdge`, `addChild`, `select`, `navigate`, `openTemplate`, `layered`, `countCrossings`, `toMermaid`, `parseMermaid`, `importMermaid`, `replaceAll`, `openFind`, `addComment`, `addSticky`, `saveVersion`, `listVersions`, `restoreVersion`, `startPresent`, `endPresent`, `next`, `presentation`, `toggleSide`, `setSideTab`, `setTheme`, `openSettings`, `openDocs`, `openExport`, `openHelp`, `startTour`, `svgString`, `markdownOutline` and `log`.

## Left for students

| Extension | Difficulty | Start here |
|---|---|---|
| Real-time collaboration: WebSocket/WebRTC transport, CRDT merge (Yjs/Automerge), presence cursors | ★★★ | `snapshot()`, `save()` |
| Cloud documents: accounts, cloud version history, read-only share links | ★★ | `loadDoc()` |
| Obstacle-aware routing: A* on a grid around nodes, separate parallel edges | ★★★ | `edgePath()`, `lanePath()` |
| draw.io interop: import/export mxGraph XML | ★★ | Mermaid section, `buildFromParsed()` |
| AI outline to mind map with a bring-your-own API key | ★★ | `TEMPLATES` builders, `branchTree()` |
| Slides export: presentation steps to PDF/PPTX with speaker notes from node notes | ★★ | `mindSteps()`, `flowSteps()`, `svgString()` |
| Attachments in IndexedDB instead of data URLs in localStorage | ★★ | `shrinkImage()`, `addImageNode()` |
| Touch gestures: two-finger pan, long-press context menu, haptics | ★ | touch pinch handlers |

Search the source for `TODO(student)`; each item above has one marker at the exact place to start, and the Help sheet lists the same items.

## Data and safety notes

- Data never leaves the device: documents, versions, settings, activity log and the tour flag are stored in `localStorage` under `nuqta:`. Browsers allow roughly 5 MB per site; Settings shows current usage, and the save indicator turns red ("Storage full") if a write fails. Images are downscaled before embedding to save space.
- **Back up everything** in Settings downloads all documents, versions, settings and the activity log as one JSON file; **Restore backup** merges it back.
- Comment authors are a free-text name from Settings; there is no authentication. Node links open in a new tab with `rel="noopener"`, and all user text is escaped before it is placed in markup.
- Mermaid import supports the flowchart/graph and mindmap subsets described above; styling, subgraphs and other diagram types are ignored rather than guessed.
