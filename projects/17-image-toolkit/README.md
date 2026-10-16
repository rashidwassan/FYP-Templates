# 17 · Client-Side Image Toolkit (idea #60)

**Resize, crop, compress, convert and strip EXIF — without uploading a single pixel.** A pro-tool style photo utility that runs entirely in the browser: Pakistani ID-photo presets (passport 35 × 45 mm, CNIC / NADRA 1 × 1 inch), "fit under 200 KB" compression by binary search, an EXIF / GPS inspector with one-click metadata stripping, before/after comparison, and batch export to a ZIP file written in plain JavaScript.

## Getting started

- Open `index.html` in any modern browser (Chrome / Edge / Firefox; Safari 17+). No build step, no server, no libraries. The only network request is Google Fonts (Urbanist) — the app falls back to system fonts offline.
- Everything works from `file://`. Serving over HTTP/HTTPS is only needed for the student extensions that require it (PWA share-target, FaceDetector API).
- Click **Try sample images** on the empty screen to generate three demo files on the fly: a 1600 × 1200 JPEG with a real EXIF block (camera, exposure, GPS at Minar-e-Pakistan), an 800 × 800 PNG with transparency, and a 1240 × 1754 "scanned admission form".
- Add your own images by drag-and-drop anywhere, the **Add images** button (multiple files), or **Ctrl / ⌘ + V** with an image on the clipboard.
- **Where data lives:** only preferences are stored, in `localStorage` under the `imgkit:` prefix (`settings`, `pipeline`, `tool`). Images live in memory and disappear when the tab is closed or refreshed — this is stated in the filmstrip and in Settings.
- **Reset:** Settings → *Reset* clears the preferences; the filmstrip's *Clear* removes all images. Settings can be exported / imported as JSON.
- Developer hook: `window.ImageToolkit` exposes `addFiles(files)`, `loadSamples()`, `runBatch({resize})`, `downloadZip()`, `makeZip(files)`, `exportBlob(canvas, type, quality)`, `parseExif(arrayBuffer)`, `quantize(canvas)` and the live `state`.

## The problem

Students and small businesses routinely upload personal photos and scanned documents to random "compress image online" sites that keep copies. Admission portals, NADRA / Pak-Identity, passport and visa forms each demand a different photo size and a tight upload limit (often 200 KB) that phones never produce. And every photo shared from a phone carries EXIF data — device model, exact timestamp and frequently GPS coordinates — that most people do not know exists. All of this is solvable on the device with Canvas 2D and a few hundred lines of JavaScript.

## What is built

| Area | Features |
|---|---|
| Input | Drag-and-drop (whole window), multi-file picker, clipboard paste. JPEG, PNG, WebP, GIF (first frame), BMP and AVIF when the browser can decode it. EXIF orientation is applied at decode (`createImageBitmap … imageOrientation: 'from-image'`, with an `<img>` fallback). Filmstrip with thumbnails, original dimensions, byte size, multi-select, remove, totals. |
| Resize | Pixels or percent, aspect lock, Fit / Fill (cover + centre crop) / Stretch, live result dimensions. Presets: Passport 413 × 531 (35 × 45 mm @ 300 dpi), CNIC / NADRA 300 × 300 (1 × 1 in), US visa 600 × 600, WhatsApp DP, Instagram square & portrait, Facebook cover, YouTube thumbnail, A4 @ 300 / 150 dpi. High-quality step-down resampling. |
| Crop | Draggable, resizable box with 8 handles, dimmed outside area, rule-of-thirds grid, aspect presets (free, 1:1, 4:3, 3:4, 16:9, 35:45 passport, 2:3), numeric X / Y / W / H, centre and whole-image shortcuts. |
| Rotate | 90° left / right, flip horizontal / vertical, free −45°…45° straighten with live preview on an auto-expanded canvas; corners filled with a chosen colour or left transparent. |
| Compress | Quality slider with a debounced live output-size estimate and savings %, animated counters; **target-size mode** that binary-searches the quality to land under e.g. 200 KB and explains what to do when the target is unreachable; PNG palette reduction to 256 colours by median cut. |
| Convert | JPEG ↔ PNG ↔ WebP (AVIF when the browser has an encoder; detected at start-up), transparency detection with background fill colour for alpha → JPEG, batch convert of all selected files to a ZIP. |
| Adjust | Brightness, contrast, saturation, grayscale, sepia, blur with live preview (`ctx.filter`, pixel-maths fallback), one-click auto-level (per-channel histogram stretch with 0.5 % clipping), RGB histogram drawn as SVG with log scale and line draw-in. |
| Metadata | JPEG APP segment walker (EXIF, XMP, ICC, IPTC, comments) and TIFF/IFD parser for camera make & model, lens, date, exposure, aperture, ISO, focal length, orientation, software, copyright and GPS (decimal degrees with a Google Maps link). PNG chunk scan (`tEXt`, `iTXt`, `zTXt`, `eXIf`, `iCCP`, `tIME`). Privacy badges, bytes of metadata, and a **Download clean copy** button. Every export is re-encoded from pixels, so metadata is always removed — the export card says so. |
| Preview | Before/after comparison slider (pointer and keyboard), zoom 25–400 % plus Fit, pixelated rendering above 100 %, pixel-peek tooltip with hex / RGB / alpha / coordinates, undo (6 steps) and reset to original per image. |
| Batch | Run Resize + encode (current format, quality or target size, quantize) over all selected images with a progress bar, per-file results, total before / after / saved with animated counters, individual downloads or **one ZIP** (STORE method, CRC-32, UTF-8 names — no library). |
| Settings | Default format and quality, remember-last-pipeline switch, dimension suffix in file names, default background colour, encoder availability, JSON export / import, reset. |
| UX | Light grey workspace with white panels and a magenta accent, Urbanist type, SVG icon sprite (no emoji), motion kit (drop-zone pulse, filmstrip stagger, slider handle, animated counters, drawer slide, toasts) under `prefers-reduced-motion`, keyboard-closable drawers, `aria-label`s on icon buttons, responsive to 390 px (rail becomes a horizontal icon bar, properties below the preview). |

## Architecture (single file)

```
index.html
├─ <style>      tokens (grey / white / charcoal / magenta), motion kit, top bar, buttons & inputs,
│               empty state (drop zone + feature tiles), workspace grid (rail · centre · props / filmstrip),
│               stage (checkerboard frame, before/after clip, handle, crop box + shade, pixel peek),
│               properties panel components, filmstrip, drawer, toasts, responsive & print rules
├─ <svg>        inline icon sprite (Lucide-style) + custom symbols (compress, flip-h/v, compare, archive, fit)
├─ <header>     brand mark, privacy pill, Add images, Settings (#settings), About (#about)
├─ <main>
│   ├─ #empty   drop zone, Choose / Try samples, supported formats, feature tiles
│   └─ #ws      .rail (tools) · .centre (.stagebar + .stage > .frame) · .props · .film
├─ <aside.drawer> About (problem, built list, student TODOs) / Settings
└─ <script>
   ├─ utils            $, store (namespaced localStorage), esc, ic, toast, debounce, fmtBytes, countUp, drawPath
   ├─ settings/pipe    FORMATS + encoder detection, settings & pipeline persistence (never images)
   ├─ state            S (images in memory: orig / work / preview / history / meta / thumb), TOOLS
   ├─ canvas ops       decode, resample (step-down), resizeTo (fit/fill/stretch), cropCanvas, rotate90, flip,
   │                   rotateFree, flatten, hasAlpha, applyFilters (+ pixelAdjust fallback), histogram,
   │                   autoLevel, quantize (median cut)
   ├─ metadata         parseExif (APPn walker) → parseTiff (IFD0 / Exif / GPS), parsePng (chunks), gpsOf
   ├─ encoder          encode (toBlob), prepareExport, exportBlob, findQuality (binary search), outName, saveBlob
   ├─ zip              CRC-32 table, makeZip (local headers, central directory, EOCD)
   ├─ samples          drawPhoto / drawLogo / drawScan, EXIF APP1 splice, loadSamples
   ├─ input            addFiles, file picker, drag-and-drop, paste
   ├─ stage            renderStage, zoom, applyCmp (slider), pixel peek, crop overlay (drag / handles / aspect)
   ├─ edits            commit / afterEdit (history), estimateNow (debounced) → updateEstimateUI
   ├─ filmstrip        renderFilm, activate, removeImage
   ├─ panels           panelResize · panelCrop · panelCompress · panelConvert · panelAdjust (+drawHist) ·
   │                   panelRotate · panelMetadata · panelBatch, exportCard shared by all tools
   ├─ batch            runBatch (progress), downloadZip
   ├─ drawers          TODOS, aboutHtml, settingsHtml + bindSettings, openDrawer / closeDrawer
   └─ router & boot    hash routes #resize #crop #compress #convert #adjust #rotate #metadata #batch #about #settings
```

Data flow: each image keeps `orig` (decoded, orientation applied), `work` (committed edits) and an optional `preview` (uncommitted slider state). The stage draws `preview || work`; the export card, Compress panel and stage pill share one debounced `estimateNow()` that encodes exactly what `exportBlob()` would produce.

## Algorithm notes

- **Step-down resampling.** Canvas `drawImage` uses bilinear sampling, which aliases when shrinking by more than 2×. `resample()` halves the image repeatedly (each pass is a clean 2 × 2 box filter) until it is within 2× of the target, then does one final smooth draw with `imageSmoothingQuality = 'high'`. Fill mode scales to cover, centre-crops, then resamples.
- **Target-size compression.** Encoded size is monotonic-ish in quality, so `findQuality()` binary-searches q ∈ [1, 100] with at most 7 `toBlob` calls and returns the highest quality that fits. If q = 1 still exceeds the limit, the UI computes the scale factor √(target / size) and suggests a percent resize.
- **Median-cut quantization.** Up to 60 000 sampled opaque pixels start in one box; the box with the widest channel range is split at its median along that channel until 256 boxes exist; each box's mean colour is a palette entry. Pixels are mapped to the nearest palette colour with a 18-bit colour cache. The browser still writes 32-bit PNG, but the deflate stage shrinks dramatically (the sample logo drops from 409 KB to about 135 KB).
- **Auto-level.** Per-channel 256-bin histograms; the 0.5 % and 99.5 % percentiles become black and white points; a LUT stretches each channel.
- **EXIF parsing.** Walk JPEG markers from SOI until SOS; for APP1 "Exif\0\0" read the TIFF header (byte order, magic 42, IFD0 offset), then entries (tag, type, count, value/offset) with bounds checks; follow the ExifIFD (0x8769) and GPS (0x8825) pointers. Rationals become numbers; GPS degrees/minutes/seconds become decimal degrees with hemisphere sign. The sample photo embeds a hand-built APP1 block so the parser can be demonstrated without a real camera file.
- **Metadata stripping** is a consequence of re-encoding: `canvas.toBlob()` writes only pixels (plus JFIF/ICC headers Chromium adds). Orientation is already applied at decode, so no `Orientation` tag is needed in the output.
- **ZIP writer.** For each file: a local file header (signature 0x04034b50, version 20, flag bit 11 for UTF-8 names, method 0 = STORE, DOS time/date, CRC-32, sizes, name) followed by the raw bytes; then central directory entries (0x02014b50) with offsets; then the end-of-central-directory record (0x06054b50). Verified with `unzip -t`.

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

- Nothing is uploaded: there is no network code besides the Google Fonts stylesheet. Processing uses Canvas 2D on the main thread; images above ~64 MP are refused by Resize to avoid canvas limits, and very large batches will briefly block the UI (a Worker + `OffscreenCanvas` is a natural next step).
- Images are never written to `localStorage`, IndexedDB or the cache; refreshing the page clears them, which is stated in the UI.
- The sample EXIF block (device name, time, GPS) is synthetic. Real photos may contain more tags (maker notes, embedded thumbnails) than the inspector lists, but all of them are removed on export because only pixels are re-encoded.
- Encoder availability differs by browser: AVIF encoding is detected at start-up and disabled when missing; WebP decoding/encoding is universal in current browsers.
- Preset photo dimensions follow the common 300 dpi conventions (35 × 45 mm → 413 × 531 px; 1 × 1 in → 300 × 300 px). Always check the specific portal's requirements — some also demand a plain background or a maximum file size, which the Crop, Convert and Compress tools cover.
