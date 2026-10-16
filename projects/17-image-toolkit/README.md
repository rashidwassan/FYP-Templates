# 17 · Client-Side Image Toolkit (idea #60)

**Resize, crop, compress, convert, stamp and strip EXIF — without uploading a single pixel.** A pro-tool style photo utility that runs entirely in the browser: Pakistani ID-photo presets (passport 35 × 45 mm, CNIC / NADRA 1 × 1 inch), a portal **requirement checker** (NADRA, DGIP, HEC, NTS, visas, social), "fit under 200 KB" compression by binary search, an EXIF / GPS inspector with one-click metadata stripping, a **watermark stamp** for CNIC copies, **print sheets** of passport photos, before/after comparison, batch export to a ZIP written in plain JavaScript, a printable session report, an activity inbox and an onboarding tour.

**Scale:** 4519 lines in one HTML file · 19 modules (11 tool panels + export estimator, filmstrip, activity inbox, printable report, onboarding tour, keyboard shortcuts, settings/themes, sample generator) · 79 seed records (6 generated sample images with real EXIF / PNG metadata, 21 portal requirement rules, 12 resize presets, 7 crop aspects, 4 paper sizes, 6 stamp presets, 7 tour steps, 14 keyboard shortcuts, 2 hand-built EXIF blocks).

## Getting started

- Open `index.html` in any modern browser (Chrome / Edge / Firefox; Safari 17+). No build step, no server, no libraries. The only network request is Google Fonts (Urbanist) — the app falls back to system fonts offline.
- Everything works from `file://`. Serving over HTTP/HTTPS is only needed for the student extensions that require it (PWA share-target, FaceDetector API).
- Click **Try sample images** on the empty screen to generate six demo files on the fly: a 1600 × 1200 JPEG with a real EXIF block (camera, exposure, GPS at Minar-e-Pakistan), a sideways-stored portrait with EXIF **Orientation 6** (decodes upright), an 800 × 800 PNG with transparency and `tEXt` chunks, a 1240 × 1754 "scanned admission form", a few-colour chart PNG and a WebP product photo (when the browser encodes WebP).
- **Take the 1-minute tour** (empty screen, About drawer, or `ImageToolkit.startTour()`) spotlights the rail, preview, properties, export card, filmstrip, activity inbox and settings.
- Add your own images by drag-and-drop anywhere, the **Add images** button (multiple files), or **Ctrl / ⌘ + V** with an image on the clipboard. Press **?** for the shortcut sheet.
- **Where data lives:** only preferences are stored, in `localStorage` under the `imgkit:` prefix — `settings`, `pipeline`, `tool`, `stamp`, `sheet`, `requirements` (your custom rules), `reqId`, `activity` (file names and actions, never pixels), `tour`. Images live in memory and disappear when the tab is closed or refreshed — this is stated in the filmstrip and in Settings.
- **Reset / backup:** Settings → *Reset* clears every preference, custom rule and the activity log; *Export settings JSON* / *Import* moves them between devices. The filmstrip's *Clear* removes all images.
- Developer hook: `window.ImageToolkit` exposes `addFiles(files)`, `loadSamples()`, `runBatch({resize, stamp})`, `downloadZip()`, `makeZip(files)`, `exportBlob(canvas, type, quality)`, `parseJpeg / parsePng / parseWebp / parseGif(arrayBuffer)`, `quantize(canvas)`, `stampCanvas(canvas, stamp, name)`, `buildSheet(canvas, options)`, `checkCompliance(img, rule, bytes)`, `startTour()`, `logEvent()`, the live `state`, `pipe`, `settings` and `activity`.

## The problem

Students and small businesses routinely upload personal photos and scanned documents to random "compress image online" sites that keep copies. Admission portals, NADRA / Pak-Identity, passport and visa forms each demand a different photo size and a tight upload limit (often 200 KB) that phones never produce. CNIC copies handed to banks and telcos are reused for SIM fraud because nothing on the copy says what it was for. And every photo shared from a phone carries EXIF data — device model, exact timestamp and frequently GPS coordinates — that most people do not know exists. All of this is solvable on the device with Canvas 2D and a few thousand lines of JavaScript.

## What is built

| Area | Features |
|---|---|
| Input | Drag-and-drop (whole window), multi-file picker, clipboard paste. JPEG, PNG, WebP, GIF (first frame), BMP and AVIF when the browser can decode it. EXIF orientation is applied at decode (`createImageBitmap … imageOrientation: 'from-image'`, with an `<img>` fallback). Privacy warning (toast + inbox event) when a loaded file carries GPS or device identity. |
| Filmstrip | Thumbnails, original dimensions and byte size, current dimensions after edits, GPS flag, multi-select, remove, select all / none / clear (optional confirmation), totals, `[` / `]` navigation. |
| Resize | Pixels or percent, aspect lock, Fit / Fill (cover + centre crop) / Stretch, live result dimensions and mm / inch equivalents at the configured dpi. 12 presets: Passport 413 × 531, CNIC / NADRA 300 × 300, US visa 600 × 600, Gulf visa 472 × 709, WhatsApp DP, Instagram square & portrait, Facebook cover, YouTube thumbnail, LinkedIn banner, A4 @ 300 / 150 dpi. High-quality step-down resampling. |
| Crop | Draggable, resizable box with 8 handles, dimmed surround, rule-of-thirds grid, aspect presets (free, 1:1, 4:3, 3:4, 16:9, 35:45 passport, 2:3), numeric X / Y / W / H, centre and whole-image shortcuts. |
| Rotate | 90° left / right, flip horizontal / vertical, free −45°…45° straighten with live preview on an auto-expanded canvas; corners filled with a chosen colour or left transparent. |
| Compress | Quality slider with a debounced live output-size estimate and savings %, animated counters; **target-size mode** that binary-searches the quality to land under e.g. 200 KB and suggests a resize percentage when unreachable; PNG palette reduction to 256 colours by median cut. |
| Convert | JPEG ↔ PNG ↔ WebP (AVIF when the browser has an encoder; detected at start-up), transparency detection with background fill colour for alpha → JPEG (the preview flattens too), batch convert of all selected files to a ZIP. |
| Adjust | Brightness, contrast, saturation, grayscale, sepia, blur with live preview (`ctx.filter`, pixel-maths fallback), one-click auto-level (per-channel histogram stretch with 0.5 % clipping), RGB histogram drawn as SVG on a log scale with line draw-in. |
| Stamp | Text watermark with `{date}` / `{name}` tokens, six presets ("For bank use only · {date}", "For HEC verification only", …), size as % of width, opacity, angle, colour, soft shadow, 3 × 3 anchor grid or tiled pattern with spacing; live preview, Apply, optional "also stamp in Batch". Settings persist. |
| Metadata | JPEG APP segment walker (EXIF, XMP, ICC, IPTC, comments) and TIFF/IFD parser for camera, lens, date, exposure, aperture, ISO, focal length, orientation, software, artist, copyright and GPS (decimal degrees + Google Maps link). PNG chunk scan with `tEXt` key/value display and `eXIf`; WebP RIFF chunks (EXIF / XMP / ICCP); GIF comment / application extensions and frame count. Privacy badges, bytes of metadata, **Download clean copy**. Every export is re-encoded from pixels, so metadata is always removed — the export card says so. |
| Check | Library of 21 portal rules in six groups (identity, education, jobs & tests, visas, social, documents) with search; per-image compliance rows for dimensions, aspect ratio, file size (uses the live estimate), format and background; **Make it comply** sets resize / format / target size and resizes in one click; **Check N selected** encodes every selected image and lists pass / fail; add and delete your own rules (persisted). Rules are labelled illustrative. |
| Sheet | Print layout generator: 4R, 5R, A4 or Letter at 300 dpi, photo preset, copies (0 = fill), gap in mm, corner cut marks; live preview, **Add sheet to filmstrip** (as a PNG image you can export) or **Print now** via a print-only container. |
| Preview | Before/after comparison slider (pointer and keyboard) with the "before" letterboxed at its own aspect, zoom 25–400 % plus Fit, pixelated rendering above 100 %, pixel-peek tooltip with hex / RGB / alpha / coordinates, undo (6 steps) and reset per image. |
| Batch | Pipeline = optional Resize + optional Stamp + encode (format, quality or target size, quantize) over all selected images with a progress bar, per-file results, totals with animated counters, individual downloads or **one ZIP** (STORE method, CRC-32, UTF-8 names — no library). |
| Session report | `#report` route: printable page with KPIs (images, original total, estimated output, files with metadata), the export pipeline, a per-image table (thumbnail, type, original / current size, edits, metadata badges, estimated output), the last batch run and recent activity. Print CSS hides the app chrome. |
| Activity inbox | Every load, edit, export, batch, privacy warning and system event is logged with read state, grouped by day, filterable by type; unread badge on the bell; mark all read, clear; can be disabled in Settings. File names only, never pixels. |
| Tour & shortcuts | Seven-step spotlight tour (loads samples if needed, keyboard friendly, Esc cancels, completion persisted); 14 keyboard shortcuts (tools 1–0, Ctrl+Z, zoom, fit, compare, navigate, select, delete, save, activity, ?) with a shortcut sheet. |
| Settings | Light / dark / system theme, units (px / mm / inch) and dpi, default format and quality, file-name pattern with tokens, plain-name rule, remember-pipeline switch, default background, activity log toggle, confirm-before-clear, load-samples-on-open demo toggle, encoder availability, JSON export / import of everything except images, reset. |
| UX | Light grey workspace with white panels and a magenta accent (dark theme mirrors it), Urbanist type, SVG icon sprite (no emoji), motion kit (drop-zone pulse, filmstrip stagger, slider handle, animated counters, drawer slide, tour spotlight, toasts) under `prefers-reduced-motion`, keyboard-closable drawers, `aria-label`s on icon buttons, responsive to 390 px (rail becomes a horizontal icon bar, properties below the preview). |

## Architecture (single file)

```
index.html
├─ <style>      tokens (light + dark), motion kit, top bar (bell badge), buttons & inputs, empty state,
│               workspace grid (rail · centre · props / filmstrip), stage (frame, before/after, crop shade),
│               panel components (presets, format cards, sliders, anchor grid, check rows, sheet preview),
│               filmstrip, drawer, activity rows, report, tour spotlight, toasts, responsive & print rules
├─ <svg>        inline icon sprite + custom symbols (compress, flip, compare, archive, fit, keyboard, stamp, sheet, checklist)
├─ <header>     brand, privacy pill, Add images, Activity (#activity), Shortcuts (#shortcuts), Settings, About
├─ <main>
│   ├─ #empty       drop zone, Choose / Try samples / Take the tour, formats, feature tiles
│   ├─ #ws          .rail (11 tools) · .centre (.stagebar + .stage > .frame) · .props · .film
│   ├─ #reportView  printable session report (route #report)
│   └─ #printSheet  print-only container for sheets
├─ <aside.drawer> About / Settings / Activity / Shortcuts
├─ tour overlay   #tourShade (border-trick spotlight) + #tourCard
└─ <script>
   ├─ utils             $, store, esc, ic, toast, debounce, fmtBytes, countUp, drawPath, mkCanvas
   ├─ storage & settings FORMATS + encoder detection, settings, pipeline, stamp, sheet (all persisted)
   ├─ seed data         TOOLS, PRESETS, ASPECTS, PAPERS, REQUIREMENTS + REQ_GROUPS, TOUR_STEPS, SHORTCUTS, TODOS, FEATURES
   ├─ state             S (images in memory: orig / work / preview / history / meta / thumb / edits)
   ├─ canvas ops        decode, resample, resizeTo, cropCanvas, rotate90, flip, rotateFree, flatten, hasAlpha,
   │                    applyFilters (+ pixelAdjust), histogram, autoLevel, quantize (medianCut), stampCanvas, buildSheet
   ├─ metadata          parseJpeg → parseTiff, parsePng, parseWebp, parseGif, parseMetadata, gpsOf
   ├─ encoder           encode, prepareExport, exportBlob, findQuality, outName (pattern), saveBlob, fmtUnits, ratioText
   ├─ zip               CRC-32 table, localHeader, centralHeader, makeZip
   ├─ samples           drawPhoto / drawPortrait / drawLogo / drawScan / drawChart / drawProduct, spliceExif, splicePngText, loadSamples
   ├─ activity          logEvent, renderBadge, markAllRead
   ├─ input             importFile, privacyCheck, addFiles, bindInput (picker, drop, paste)
   ├─ render root       render, renderRail, applyTheme
   ├─ stage             renderStage, applyCmp, zoom helpers, undo / reset, compare handle, pixel peek
   ├─ crop overlay      initCrop, renderCropBox, cropDrag, setCropAspect, setCropField
   ├─ edits             commit, afterEdit, estimateNow → updateEstimateUI
   ├─ filmstrip         renderFilm, toggleSelected, activate, stepActive, removeImage, clearAll
   ├─ panel framework   slider, seg, renderPanel, exportCard, setFormat, downloadActive
   ├─ panels            panelResize · panelCrop · panelCompress · panelConvert · panelAdjust (+drawHist) · panelRotate ·
   │                    panelStamp · panelMetadata · panelCheck (+makeComply, checkAllSelected, custom rules) ·
   │                    panelSheet (+addCanvasAsImage, printCanvas) · panelBatch (+runBatch, downloadZip)
   ├─ report            renderReport, fillReportEstimates
   ├─ drawers           aboutHtml, settingsHtml + bindSettings (export / import / reset), activityHtml, shortcutsHtml, openDrawer
   ├─ tour              startTour, showTourStep, positionTour, endTour
   ├─ keyboard          handleKey
   └─ router & boot     #resize #crop #compress #convert #adjust #rotate #stamp #metadata #check #sheet #batch
                        #report · drawers #about #settings #activity #shortcuts
```

Data flow: each image keeps `orig` (decoded, orientation applied), `work` (committed edits) and an optional `preview` (uncommitted slider state). The stage draws `preview || work`; the export card, Compress panel, Check rows and stage pill share one debounced `estimateNow()` that encodes exactly what `exportBlob()` would produce.

## Module map

| Module | Entry points / main functions | Storage keys (`imgkit:` prefix) |
|---|---|---|
| Input & filmstrip | `addFiles`, `importFile`, `privacyCheck`, `renderFilm`, `activate`, `removeImage` | — (memory only) |
| Stage & preview | `renderStage`, `applyCmp`, `bindPixelPeek`, `zoomIn/Out/ToFit`, `undo`, `resetImage` | — |
| Resize | `panelResize`, `resizeTarget`, `applyPreset`, `applyResize`, `resizeTo`, `resample` | `pipeline.resize` |
| Crop | `panelCrop`, `initCrop`, `cropDrag`, `setCropAspect`, `applyCrop` | — |
| Compress | `panelCompress`, `findTargetQuality`, `findQuality`, `quantize` | `pipeline.quality/targetOn/targetKB/quantize` |
| Convert | `panelConvert`, `setFormat`, `prepareExport`, `flatten` | `pipeline.format/bg` |
| Adjust | `panelAdjust`, `applyFilters`, `autoLevel`, `drawHist` | — |
| Rotate | `panelRotate`, `rotate90`, `flip`, `rotateFree` | `pipeline.rotBg` |
| Stamp | `panelStamp`, `stampCanvas`, `drawAnchoredText`, `drawTiledText` | `stamp`, `pipeline.batchStamp` |
| Metadata | `panelMetadata`, `parseJpeg`, `parseTiff`, `parsePng`, `parseWebp`, `parseGif`, `gpsOf` | — |
| Check (requirements) | `panelCheck`, `checkCompliance`, `makeComply`, `checkAllSelected`, `addCustomReq` | `requirements`, `reqId` |
| Sheet | `panelSheet`, `buildSheet`, `drawCutMarks`, `addCanvasAsImage`, `printCanvas` | `sheet` |
| Batch & ZIP | `panelBatch`, `runBatch`, `batchCanvas`, `downloadZip`, `makeZip`, `crc32` | `pipeline.batchResize` |
| Export estimator | `exportCard`, `estimateNow`, `updateEstimateUI`, `exportBlob`, `outName`, `downloadActive` | `settings.pattern/suffix` |
| Session report | `renderReport`, `fillReportEstimates`, `reportRow` | — |
| Activity inbox | `logEvent`, `renderBadge`, `markAllRead`, `renderActivity` | `activity`, `settings.logActivity` |
| Onboarding tour | `startTour`, `showTourStep`, `positionTour`, `endTour` | `tour` |
| Keyboard shortcuts | `handleKey`, `shortcutsHtml` | — |
| Settings & theme | `settingsHtml`, `bindSettings`, `applyTheme`, `exportSettings`, `importSettings`, `resetSettings` | `settings`, `pipeline`, `tool` |
| Sample generator | `loadSamples`, `drawPhoto`…`drawProduct`, `spliceExif`, `splicePngText` | — |

## Algorithm notes

- **Step-down resampling.** Canvas `drawImage` uses bilinear sampling, which aliases when shrinking by more than 2×. `resample()` halves the image repeatedly (each pass is a clean 2 × 2 box filter) until it is within 2× of the target, then does one final smooth draw with `imageSmoothingQuality = 'high'`. Fill mode scales to cover, centre-crops, then resamples.
- **Target-size compression.** Encoded size is monotonic-ish in quality, so `findQuality()` binary-searches q ∈ [1, 100] with at most 7 `toBlob` calls and returns the highest quality that fits. If q = 1 still exceeds the limit, the UI computes √(target / size) and suggests a percent resize.
- **Median-cut quantization.** Up to 60 000 sampled opaque pixels start in one box; the box with the widest channel range is split at its median along that channel until 256 boxes exist; each box's mean colour is a palette entry. Pixels map to the nearest palette colour through an 18-bit colour cache. The browser still writes 32-bit PNG, but deflate shrinks dramatically (the sample logo drops from 409 KB to about 135 KB).
- **Auto-level.** Per-channel 256-bin histograms; the 0.5 % and 99.5 % percentiles become black and white points; a LUT stretches each channel.
- **Metadata parsing.** JPEG: walk markers from SOI until SOS; for APP1 "Exif\0\0" read the TIFF header (byte order, magic 42, IFD0 offset), then entries (tag, type, count, value/offset) with bounds checks; follow the ExifIFD (0x8769) and GPS (0x8825) pointers. PNG: walk chunks, read `tEXt` key/value pairs and `eXIf` TIFF. WebP: RIFF chunk list (`EXIF`, `XMP `, `ICCP`). GIF: skip image sub-blocks, collect comment and application extensions, count frames. GPS degrees/minutes/seconds become decimal degrees with hemisphere sign. The sample photo and portrait embed hand-built APP1 blocks (one with GPS, one with Orientation 6) so the parser and orientation handling can be demonstrated without a real camera file.
- **Metadata stripping** is a consequence of re-encoding: `canvas.toBlob()` writes only pixels (plus the JFIF/ICC headers Chromium adds). Orientation is already applied at decode, so no `Orientation` tag is needed in the output.
- **Compliance check.** Dimensions are compared exactly or as minimums, aspect within 1 %, file size against the live estimate, format against the rule's allow-list; background is a manual check. *Make it comply* writes the rule into the pipeline (fill-resize, allowed format, target KB) and applies the resize immediately.
- **Stamp.** Font size is a percentage of image width so the mark scales with resolution; tiled mode rotates the whole context and fills a disc of radius ½·diagonal with staggered rows so rotation never leaves gaps.
- **Print sheet.** Grid = floor((paper − 2·margin + gap) / (photo + gap)) per axis; photos are fill-resized once and blitted; cut marks are 14 px ticks offset 4 px outside each corner.
- **ZIP writer.** Local file header (0x04034b50, version 20, flag bit 11 for UTF-8 names, method 0 = STORE, DOS time/date, CRC-32, sizes, name) + raw bytes per file; central directory entries (0x02014b50) with offsets; end-of-central-directory record (0x06054b50). Verified with `unzip -t`.

## Left for students

| Extension | Difficulty | Start here |
|---|---|---|
| WASM codecs (mozjpeg, oxipng, avif) for 20–40 % smaller output and AVIF everywhere | ★★★ | `encode()`, `exportBlob()` |
| Background removal with a TF.js / MediaPipe segmentation model (ID-photo backdrop) | ★★★ | `panelAdjust()` → `#adBg` |
| Multi-image PDF export (hand-written PDF with DCTDecode image XObjects) | ★★ | `panelBatch()` → `#bzPdf` |
| PWA with `share_target` so phones can "Share to" the app | ★★ | `boot()` service-worker stub |
| OCR of scanned documents (Tesseract.js, eng + urd) | ★★ | `panelConvert()` → `#cvOcr` |
| Face-centred auto-crop for passport photos (FaceDetector / BlazeFace) | ★★★ | `panelCrop()` → `#cpFace` |
| Real indexed PNG writer (PLTE / tRNS / IDAT via CompressionStream) | ★★ | `quantize()` |
| Keep or write chosen EXIF fields (date, copyright) by rebuilding APP1 | ★★ | `exportBlob()`, see the splice in `loadSamples()` |

Search the source for `TODO(student)` to find the exact hooks; the About drawer lists the same items.

## Data & safety notes

- Nothing is uploaded: there is no network code besides the Google Fonts stylesheet. Processing uses Canvas 2D on the main thread; images above 64 MP are refused by Resize to avoid canvas limits, and very large batches briefly block the UI (a Worker + `OffscreenCanvas` is a natural next step).
- Images are never written to `localStorage`, IndexedDB or the cache; refreshing the page clears them, which is stated in the UI. The activity log stores file names and actions only and can be switched off.
- The sample EXIF blocks (device names, times, GPS) are synthetic. Real photos may contain more tags (maker notes, embedded thumbnails) than the inspector lists, but all of them are removed on export because only pixels are re-encoded.
- Portal rules in the Check library are **illustrative** and labelled as such — portals change their limits; the UI tells users to verify, and custom rules can be added for the exact requirement.
- Encoder availability differs by browser: AVIF encoding is detected at start-up and disabled when missing; WebP decoding/encoding is universal in current browsers.
- Preset photo dimensions follow the common 300 dpi conventions (35 × 45 mm → 413 × 531 px; 1 × 1 in → 300 × 300 px). Always check the specific portal's requirements — some also demand a plain background or a maximum file size, which the Crop, Convert, Compress and Check tools cover.
