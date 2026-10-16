# 10 · Virtual Science Lab (idea #27)

**Eight interactive physics, chemistry and biology practicals for schools without laboratories.** Each experiment is a real simulation with adjustable parameters, live measurements, a graph, bilingual theory, an auto-checked worksheet and a printable lab report. English / اردو. No libraries.

## Getting started

1. Open `index.html` in any modern browser. Everything runs offline on a canvas.
2. Pick an experiment from the left rail (or the horizontal bar on phones), move the sliders, press **Run / Pause** and use the action buttons (Launch, +1 mL, Auto…).
3. Read measurements in the right panel, inspect the **Graph** tab (CSV export), read **Theory**, answer the **Worksheet** (answers are checked instantly and saved) and print the **Lab report**.
4. Progress, parameters and worksheet answers live in `localStorage` under the `vlab:` prefix.

## The problem

Most government schools in Pakistan have no functioning science laboratory, so Matric and Intermediate students memorise practicals they have never performed. A phone or shared computer can run the core experiments and produce the measurements, graphs and reports that the syllabus expects.

## What is built

| Experiment | Physics / chemistry modelled | Students measure |
|---|---|---|
| Simple pendulum | θ'' = −(g/L) sin θ − bθ', Earth/Moon/Mars/Jupiter gravity, damping, zero-crossing period detection | Period vs length and gravity; derive g |
| Projectile motion | Numerical integration with optional quadratic drag, auto-scaled field, velocity vector decomposition | Range, maximum height, flight time vs angle |
| Ohm's law circuit | Single, series and parallel resistors, animated electron flow, bulb power glow, ammeter/voltmeter readouts, V–I graph | I = V/R, series and parallel totals, power |
| Waves & superposition | Travelling wave, two-wave interference with beats, standing wave with nodes | v = fλ, period, beat frequency |
| Lenses & ray diagrams | Thin-lens formula for convex and concave lenses, three principal rays, real/virtual image rendering | Image distance, magnification, nature |
| Acid–base titration | Strong acid/strong base pH calculation, three indicators with colour change, hidden unknown samples | Equivalence volume, unknown concentration |
| Gas laws | PV = nRT with a piston, particle speeds ∝ √T, wall-hit counter, P–V isotherm | Boyle, Charles and Gay-Lussac relations |
| Osmosis | Semipermeable membrane with water flux ∝ concentration difference, solute conservation, tonicity | Direction of water movement, equilibrium |

Cross-cutting: fixed-timestep runner (240 Hz) scaled by a speed slider, device-pixel-ratio canvas, graph-paper stage, hand-drawn graphs with markers and CSV export, worksheet scoring rings, printable report (print CSS isolates the report), Urdu for every title, intro, theory, label and question.

## Architecture (single file)

```
index.html
├─ <style>        graphite sidebar + acid accent tokens, lab layout, controls, tabs, worksheet, report, print
├─ <svg sprite>   icon symbols (no emoji)
├─ <aside.side>   brand, experiment list grouped by subject, language/about
├─ <main>         head, stage canvas + transport, tabs (Graph · Theory · Worksheet · Report · About), controls, measurements
└─ <script>
   ├─ utils         store, esc, ic(), toast, fmt, drawing helpers (grid, arrow, label, dashed)
   ├─ i18n          TXT.en / TXT.ur, L() for bilingual objects
   ├─ EXPS[]        one object per experiment: params, init, step, draw, readouts, graph, actions, theory, worksheet
   ├─ runner        resize(), loadExp(), frame() fixed-step loop, renderControls/Actions/Reads
   ├─ graph         drawGraph(), CSV export
   ├─ tabs          renderTheory(), renderWS() with auto-check, renderReport(), renderAbout()
   └─ nav           renderSide(), applyLang()
```

Adding an experiment means pushing one object into `EXPS`; the runner, worksheet, report and navigation pick it up automatically.

## Left for students

| Extension | Difficulty | Start here |
|---|---|---|
| More experiments (Hooke's law, induction, reaction rates, photosynthesis, enzymes) | ★★ | `EXPS` |
| Teacher mode: class codes, score dashboard, locked parameters for assessments | ★★★ | new page + backend |
| LMS export (SCORM/xAPI) for Moodle or Google Classroom | ★★ | `renderReport()` |
| Measurement overlays: draggable ruler, stopwatch, protractor | ★★ | `draw()` helpers |
| Accessibility: keyboard slider announcements, high contrast, graph sonification | ★★ | `renderControls()` |
| Sindhi, Pashto, Punjabi language packs | ★ | `TXT`, per-experiment strings |
| Offline PWA for school tablets | ★ | `manifest.json`, `sw.js` |

Search the source for `TODO(student)`.

## Notes

Simulation values are computed from the equations shown in each Theory tab and are meant for learning; the titration's "unknown" samples are fixed concentrations (0.10, 0.15 and 0.08 M) so worksheet answers can be checked.
