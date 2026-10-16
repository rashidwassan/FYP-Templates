# 02 · Crop Doctor (idea #4)

**First-line leaf disease screening for Pakistani crops, on any phone, offline.** A bilingual (English / اردو) single-file app with a dependency-free colour analyzer and image tools, an optional in-browser trainable classifier with an evaluation panel, a symptom wizard, a field log, a treatment planner with dosage calculator, weather-risk indices, a schematic outbreak map and spoken treatment cards.

**Scale:** 5,271 lines in one `index.html` (≈340 CSS, ≈270 HTML, ≈4,660 JS in 25 commented sections) · 11 pages · 20 modules · knowledge base of 34 disease/pest entries (8 insect pests with IPM calendars), 42 symptoms, 29 glossary terms, 16 illustrative products, 25 districts · 37 seeded demo records (4 fields, 15 scans, 14 weather days, 4 treatments).

## Getting started

Open `index.html` in a modern browser. Everything except the ML module and Google Fonts works fully offline from `file://`. Serve over HTTP (`npx serve .`) if you want the rear camera on a phone. All data lives in `localStorage` under the `cropdoc:` prefix; **Settings → Your data** exports a full backup, imports one, removes the demo records or resets the device. The onboarding tour runs once; replay it from Settings.

## The problem

One agriculture extension officer serves thousands of farmers. Cotton leaf curl, wheat rust and rice blast are usually diagnosed after they have spread across a field, sprays are mistimed or repeated with the same mode of action, and nothing is written down. A screening tool that works on a low-end Android phone, in Urdu, without signal, and that also keeps the field log, the spray calendar and the weather watch, shortens that loop.

## What is built

| Area | Features |
|---|---|
| Image intake | Rear camera, upload, drag-and-drop (multiple files start a batch), clipboard paste, deterministic synthetic leaf samples; one shared stage element moves between the Scan and Train pages |
| Image tools | Rotate 90°, drag-to-crop on the stage (mapped from the contained image back to pixel space), auto-contrast (1st–99th percentile stretch per channel), reset to original; every edit re-analyses |
| Baseline analyzer | Background removal via border-colour reference, HSV classification of leaf pixels into healthy green / yellow / brown / white, animated health gauge, pixel breakdown, segmentation overlay, colour-cue matching against the knowledge base |
| Batch analysis | Many images through the same `segment()` → results table (thumbnail, health, status, Y/B/W shares, top match), mean/flagged KPIs, save all to history, CSV export, demo batch of 8 synthetic leaves |
| ML second opinion | Lazy TensorFlow.js 4 + MobileNet v2 + KNN head; graceful offline fallback; dataset persisted and exported as a bundle (training set + test set + k) with legacy import |
| Evaluation panel | k slider (1–10), class-balance warnings (< 5 examples, > 3:1 imbalance), labelled held-out test set, KNN evaluation with confusion matrix and per-class precision/recall, offline baseline self-test on 24 synthetic leaves |
| Symptom wizard | Crop → affected part → symptom chips; weighted matching (primary ×2, secondary ×1, penalty for unmatched) over 34 diseases and pests |
| Library | Tabs for diseases, insect pests, glossary and IPM calendars; crop chips and free-text search; detail modal with conditions, symptoms, treatment (en/ur, spoken Urdu), prevention, IPM stages, matching products and "Plan a treatment" |
| Field log | Fields (name, crop, area in acre/kanal/hectare, sowing and harvest dates, district, notes), scans linked at save time, per-field health timeline chart and sparklines, linked treatments and crop-specific weather risk |
| Treatment planner | Spray schedule (upcoming / done / all, done / skip / postpone), warnings for pre-harvest interval vs expected harvest, re-entry time, repeated FRAC/IRAC group within 21 days and off-target products; dosage calculator (rate × area, water × area, tanks, per-tank, per-litre, % concentration) |
| Weather risk | Daily log (Tmin, Tmax, RH, leaf-wetness hours, rain) with 14 seeded days; rule-based 0–100 indices for yellow rust, rice blast, late blight (Smith period) and whitefly/CLCuV pressure; temperature and humidity charts, daily risk strips, rules explained in-app |
| Outbreak map | Hand-drawn schematic SVG outline with 25 district nodes sized and coloured by saved scans (all or one disease), click/keyboard selection, district ranking table |
| Reports | Printable extension-officer report per field or for all scans: metadata, recent scans, risk today, treatments, recommendations, signature lines; print CSS isolates the modal |
| Notifications | Inbox with unread badge: due/overdue sprays and high weather risk for crops you grow, deduplicated per day, read state persisted |
| Search & shortcuts | Global search (/, Ctrl+K) over diseases, pests, symptoms, glossary, fields, products and pages; keyboard sheet (?) with single-key page jumps |
| Settings & onboarding | Language, area unit, larger text, reduced motion, reminders, demo-data toggle, storage meter, export/import/reset; six-step spotlight tour |
| History | Saved scans with thumbnail, health, field/district, colour match and ML prediction; filter by field; JSON export |

## Module map

| Module (script section) | Key functions | Storage keys |
|---|---|---|
| 1 utils | `store`, `lineChart`, `sparkline`, `ringGauge`, `animateIn`, `drawPaths`, `countUp` | — |
| 2 i18n | `L`, `t()`, `nm()` | `lang` |
| 3 knowledge base | `CROPS`, `SYMPTOMS`, `DISEASES` (+`ipm`), `GLOSSARY`, `PRODUCTS`, `DISTRICTS`, `OUTLINE` | — |
| 4 settings + seed | `applySettings`, `toUnit/fromUnit`, `ensureSeed`, `seedFields/History/Weather/Plans`, `removeDemo` | `settings`, `seed_v` |
| 5 stage / intake | `mountStage`, `toggleCamera`, `snap`, `loadImage`, `synthLeafCanvas`, `thumbFromCanvas` | `crop` |
| 6 image tools | `rotateImage`, `toggleCrop`, `applyCrop`, `renderedImageRect`, `autoContrast`, `percentileBounds` | — |
| 7 analyzer | `rgb2hsv`, `borderReference`, `classifyPixel`, `segment`, `analyze`, `visualMatches` | — |
| 8 batch | `runBatch`, `batchDemo`, `renderBatch`, `exportBatchCsv`, `saveBatchToHistory` | `history` |
| 9 result + save | `renderResult`, `cuesFor`, `statusOf`, `renderFieldSelect`, `saveScan` | `history`, `field_sel` |
| 10 ML core | `loadML`, `addExample`, `predictML`, `persistDataset`, `restoreDataset`, `exportDataset`, `importDataset`, `renderTrain` | `knn`, `knn_k` |
| 11 ML evaluation | `addTestExample`, `balanceWarnings`, `confusionFromPairs`, `metricsFrom`, `evaluateKnn`, `evaluateBaseline`, `renderMLAdvanced` | `testset` |
| 12 symptom wizard | `scoreSymptoms`, `renderSymptoms`, `renderSymptomResults` | — |
| 13 library + modal | `renderLibrary`, `renderDiseaseGrid`, `renderGlossary`, `renderIpmCalendars`, `openDisease`, `speakUrdu`, `openModal` | — |
| 14 history | `renderHistory`, `healthPill` | `history` |
| 15 field log | `renderFields`, `fieldCard`, `renderFieldDetail`, `fieldTimeline`, `openFieldForm`, `saveField`, `deleteField` | `fields`, `field_open` |
| 16 planner + dosage | `planState`, `planWarnings`, `renderPlanner`, `savePlan`, `setPlanStatus`, `postponePlan`, `calcDose`, `renderDose` | `plans` |
| 17 weather risk | `tempScore`, `moistureScore`, `riskRust`, `riskBlast`, `riskLateBlight`, `riskWhitefly`, `riskAll`, `riskSeries`, `addWeatherDay` | `weather` |
| 18 outbreak map | `mapCounts`, `mapValue`, `heat`, `renderMap`, `renderMapSide` | reads `history`, `fields` |
| 19 report | `openReport` | reads all |
| 20 notifications | `pushNotification`, `refreshAlerts`, `renderBell`, `renderInbox`, `openDrawer` | `inbox` |
| 21 search | `searchIndex`, `openSearch` | — |
| 22 tour + shortcuts | `startTour`, `showTourStep`, `placeTip`, `endTour`, `openShortcuts` | `tour_done` |
| 23 settings page | `renderSettings`, `exportAll`, `importAll`, `resetAll`, `storageBytes` | all `cropdoc:*` |
| 24 about | `TODOS`, `renderAbout` | — |
| 25 nav / boot | `go`, `applyLang`, `boot` | `lang` |

## Architecture (single file)

```
index.html
├─ <style>            tokens (clinical green + dark scanner stage), header, forms, scan/result,
│                     tables, KPI tiles + charts, library, modal, fields, planner, weather,
│                     map, train/confusion, drawer, tour, report, icon + motion kit, responsive, print
├─ <svg> sprite       kit icons used + custom i-bug
├─ <header>           brand, 11-tab nav (wraps under 1400px), search, notifications bell, language
├─ <main>             sections: scan · symptoms · fields · planner · weather · map · train ·
│                     library · history · settings · about (toggled by go())
├─ #stage             shared camera/image/overlay/cropper element, mounted into the active slot
├─ modal · drawer · tour · toasts
└─ <script>           25 numbered sections, see Module map
```

## Domain logic notes (for the report)

**Analyzer.** The image is downscaled to 320 px; the per-channel median of a 4% border ring is the background reference (RGB distance < 42 = background). Remaining pixels go through HSV: very dark → necrotic; low saturation and bright → white; hue 66–170° → green; 38–66° → yellow; < 38° or > 335° → brown. Health index = green share of the leaf area. Each disease carries colour-cue weights (yellow, brown, white) and the weighted sum ranks visual matches. Auto-contrast stretches each channel between its 1st and 99th percentile; crop maps the drawn box from the `object-fit: contain` rendering back to image pixels.

**Weather indices (0–100; low < 35, moderate < 65, high ≥ 65).** Each is a trapezoid temperature suitability multiplied by a moisture score (humidity approaching a threshold or enough leaf-wetness hours). Yellow rust: mean 10–15°C, RH→80% or 6 h wet. Rice blast: mean 24–28°C, RH→90% or 10 h wet, +15% for nights ≤ 22°C. Late blight: Smith period, Tmin ≥ 10°C with ≥ 11 h above 90% RH on two consecutive days = 100, one day = 55. Whitefly/CLCuV: 7-day mean of hot (30–38°C) and dry-ish (RH 45–75%) days, heavy rain knocks a day to 30%. They flag favourable weather, not infection.

**Planner checks.** Pre-harvest interval: spray date + PHI must not pass the field's expected harvest. Re-entry: spray morning + REI hours. Resistance: the same FRAC/IRAC group on the same field within 21 days is flagged. Dosage: product = rate × acres, water = L/acre × acres, tanks = ceil(water / tank), per tank = rate × tank / water; 1% solution = 10 ml per litre.

**ML evaluation.** MobileNet activations of held-out images are stored as the test set; evaluation predicts each with the current KNN and k, builds the confusion matrix and per-class precision/recall. Class-balance warnings fire under 5 examples per class or above a 3:1 ratio. The offline self-test pushes 24 synthetic leaves through the colour analyzer and a 4-class rule so the panel can be demonstrated without a network.

## Left for students

| Extension | Difficulty | Start here |
|---|---|---|
| Train a real CNN (PlantVillage + own field photos), export with `tensorflowjs_converter`, replace the KNN head; the evaluation panel already scores it | ★★★ | `loadML()`, `predictML()` |
| Proper leaf segmentation (GrabCut-style or tiny U-Net) for cluttered backgrounds | ★★★ | `segment()` |
| Field dataset app: GPS, crop stage, consent, upload to a labelling backend | ★★ | `saveScan()` |
| Agronomist review queue with verdicts fed back into History and training | ★★ | `renderHistory()` |
| Real GIS outbreak map: district GeoJSON on Leaflet, anonymised scans aggregated week by week | ★★ | `renderMap()` |
| Recorded Urdu/Sindhi/Punjabi audio for treatment cards, TTS as fallback | ★ | `speakUrdu()` |
| Live weather feed (PMD / Open-Meteo) into the daily log, manual entry as fallback | ★★ | `addWeatherDay()` |
| Offline PWA caching the app, fonts and model weights | ★ | `boot()` |

Search the source for `TODO(student)`; each row above has a marker at that exact point.

## Data and safety notes

Disease, pest and IPM descriptions are summarised from standard extension guidance and simplified. Product names, rates, FRAC/IRAC groups, re-entry and pre-harvest intervals are illustrative examples for the planner; the label and the local extension office are the authority. Weather indices flag favourable conditions only. The district map is a hand-drawn schematic, not a survey. Synthetic leaves and all demo records are generated locally and flagged `demo`, so they can be removed from Settings without touching your own data.
