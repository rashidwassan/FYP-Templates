# 16 · Nuqta — Mind Map & Flowchart Editor (idea #59)

**An offline, single-file diagram editor.** Infinite SVG canvas, mind-map mode with keyboard-driven branching and animated auto-layout, flowchart mode with nine shapes and orthogonal/curved/straight connectors, undo/redo, multiple documents with thumbnails, templates, and export to SVG, PNG, JSON or a Markdown outline. No libraries.

## Getting started

1. Open `index.html`. A sample "FYP plan" mind map loads on first run.
2. Double-click the canvas to add a node; double-click a node to edit it. Select a node and press **Tab** for a child or **Enter** for a sibling. Press **L** to auto-layout and **F** to fit.
3. Switch to **Flowchart** in the top bar, pick shapes from the left palette, and drag from a node (connect tool or a port) to another node to link them. Double-click a connector to label it.
4. Open **Documents** (folder icon) for templates, saved documents and JSON import; **Export** (download icon) for SVG/PNG/JSON/Markdown. Everything autosaves to `localStorage` under the `nuqta:` prefix. Press **?** (help icon) for all shortcuts.

## The problem

Students plan FYPs, write-ups and system designs in paid or online-only tools. A lightweight editor that works offline on any laptop, exports clean vectors for reports, and is small enough to read and extend is itself a good systems project: it touches geometry, rendering, state management and UX.

## What is built

| Area | Features |
|---|---|
| Canvas | Infinite SVG scene with pan (space-drag / hand tool / middle mouse), cursor-centred wheel zoom, pinch zoom on touch, dot grid, snap-to-grid, minimap with viewport, fit-to-screen, zoom controls, position readout |
| Nodes | 9 shapes (process, rounded, terminal, decision, input/output, document, database, circle, note), 8 colours, font size, width, inline text editing with word-wrap, URL badge, notes, resize handle, collapse/expand branches, duplicate, copy/paste, nudge with arrows, marquee and shift multi-select, align selected |
| Connectors | Curved, orthogonal and straight paths, arrowheads none/end/both, dashed style, labels, reverse direction, drag-to-connect from ports, drag into empty space to create a connected child |
| Mind-map mode | Tab/Enter branching with inherited colours, tree auto-layout that animates node moves (keyed DOM patching with CSS transitions), collapse counters, Markdown outline export |
| Flowchart mode | Shape palette, layered auto-layout (longest-path layering), templates for a login flow and an org chart |
| History & docs | 100-step undo/redo snapshots, autosave with status, multiple documents with SVG thumbnails, duplicate/delete, JSON import/export, four templates |
| Export | SVG (text stays selectable), 2× PNG via canvas, JSON, Markdown outline |
| UX | Light/dark theme, tooltips, find-node search that selects and centres, shortcut sheet, responsive layout with bottom-sheet inspector on phones |

## Architecture (single file)

```
index.html
├─ <style>        navy toolbar + dotted canvas tokens, node/edge SVG styles, inspector, modals, mobile sheet
├─ <svg sprite>   icon symbols (no emoji)
├─ .app           toolbar · palette | canvas (#board > #world > edges/nodes/overlay) | inspector · status bar
└─ <script>
   ├─ model         doc {nodes, edges, view}, mkNode/mkEdge, children(), hiddenIds()
   ├─ history       snapshot()/restore(), undo/redo, debounced save(), loadDoc()/openNew()
   ├─ geometry      toWorld/toScreen, anchor(), edgePath() (curve/ortho/straight), shapePath(), wrapText()
   ├─ render        keyed patching of <g.node>/<g.edge> so transforms animate; minimap; fit(); zoomAt()
   ├─ layout        autoLayout(): tree layout for mind maps, layered layout for flowcharts
   ├─ interactions  pointer state machine: move · marquee · pan · resize · connect; inline editor; keyboard map
   ├─ inspector     node / connector / document panels
   ├─ documents     templates, gallery with thumbnails, import
   └─ export        svgString(), PNG via Image→canvas, Markdown outline
```

Public hook for tests and extensions: `window.nuqta` exposes `doc`, `autoLayout`, `fit`, `openNew`, `loadDoc`, `TEMPLATES`, `setTool`, `snapshot`, `render`.

## Left for students

| Extension | Difficulty | Start here |
|---|---|---|
| Real-time collaboration (WebSocket/WebRTC + CRDT), presence, comments | ★★★ | `snapshot()`, `save()` |
| Cloud documents, accounts, version history, share links | ★★ | `loadDoc()` / `openDocs()` |
| Proper layered graph layout (Sugiyama/dagre) and port-aware routing | ★★★ | `autoLayout()`, `edgePath()` |
| Image and attachment nodes | ★★ | `mkNode()`, `render()` |
| Mermaid / draw.io import-export | ★★ | export modal |
| AI outline → mind map via bring-your-own API key | ★★ | `openNew()` builders |
| Presentation mode with zoom animations | ★★ | `fit()`, `view` |
| Full keyboard navigation and screen-reader graph description | ★★ | keyboard handler |

Search the source for `TODO(student)`.
