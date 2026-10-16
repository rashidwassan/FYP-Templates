# 02 · Crop Doctor (idea #4)

**First-line leaf disease screening for Pakistani crops, on any phone, offline.** A bilingual (English / اردو) single-file app with a dependency-free colour analyzer, an optional in-browser trainable classifier, a symptom wizard and spoken treatment cards.

Open `index.html` in a modern browser. The baseline analyzer, symptom wizard, library and history work fully offline. The ML module and Google Fonts load from CDNs on demand and degrade gracefully when there is no network.

## The problem

One agriculture extension officer serves thousands of farmers. Cotton leaf curl, wheat rust and rice blast are usually diagnosed after they have spread across a field. A screening tool that works on a low-end Android phone, in Urdu, without signal, shortens that loop.

## What is built

| Area | Features |
|---|---|
| Image intake | Rear camera (getUserMedia), file upload, drag-and-drop, clipboard paste, synthetic leaf samples for demos; one shared "stage" component moves between the Scan and Train pages |
| Baseline analyzer | Background removal via border-colour reference, HSV classification of leaf pixels into healthy green / yellow / brown / white, animated health gauge, pixel breakdown bars, toggleable segmentation overlay, colour-cue matching against the knowledge base |
| ML second opinion | Lazy-loaded TensorFlow.js 4 + MobileNet v2 feature extractor + KNN head; add examples per class, predict, per-class counts, dataset persisted to localStorage, JSON export/import, dataset-collection protocol |
| Symptom wizard | Crop → affected part → symptom chips; weighted matching (primary ×2, secondary ×1, penalty for unmatched) against 19 diseases across 9 crops |
| Library | Filterable disease cards; detail modal with conditions, symptoms, treatment steps, Urdu treatment block with Web Speech playback, prevention, print layout |
| History | Saved scans with thumbnail, health index, colour match and ML prediction; JSON export |

## Architecture (single file)

```
index.html
├─ <style>            tokens (clinical green + dark scanner stage), components, modal, responsive
├─ <header>           brand, tab nav, language toggle
├─ <main>             six <section data-page> blocks toggled by go()
├─ #stage             shared camera/image/overlay element, mounted into the active page's slot
└─ <script>
   ├─ utils, i18n     store (namespaced localStorage), t(), nm()
   ├─ knowledge base  CROPS, SYMPTOMS (with affected parts), DISEASES (cues, treatments en/ur)
   ├─ stage/intake    camera lifecycle, loadImage(), synthLeaf()
   ├─ analyzer        analyze() → HSV segmentation, overlay mask, visualMatches()
   ├─ ML              loadML(), addExample(), predictML(), persistDataset()/restoreDataset()
   ├─ wizard          renderSymptoms() scoring
   ├─ library/modal   renderLibrary(), openDisease(), speakUrdu()
   └─ history/about   renderHistory(), TODOS
```

## How the analyzer works (for the report)

1. The image is downscaled to 320 px wide.
2. Pixels in a 4 % border ring are sampled and their per-channel median becomes the background reference; any pixel within an RGB distance of 42 is background. This is why the protocol asks for a plain background.
3. Remaining pixels are converted to HSV and bucketed: very dark → necrotic; low saturation and bright → white/pale; hue 66–170° → healthy green; 38–66° → yellow/chlorotic; < 38° or > 335° → brown/necrotic.
4. Health index = share of healthy-green pixels in the leaf area. Each disease carries colour-cue weights (yellow, brown, white); the weighted sum ranks visual matches. This is a screening heuristic, not a diagnosis, and the UI says so.

## Left for students

| Extension | Difficulty | Start here |
|---|---|---|
| Train a real CNN (PlantVillage + own field photos), export with `tensorflowjs_converter`, replace the KNN head | ★★★ | `ML`, `predictML()` |
| Proper leaf segmentation (GrabCut-style or tiny U-Net) for cluttered backgrounds | ★★★ | `analyze()` |
| Field dataset app: GPS, crop stage, consent, upload to a labelling backend | ★★ | `#saveBtn` handler |
| Agronomist review queue with verdicts fed back into training | ★★ | History + backend |
| Tehsil-level outbreak map from anonymised scans | ★★ | new page + Leaflet |
| Recorded Urdu/Sindhi/Punjabi audio for treatment cards | ★ | `speakUrdu()` |
| Weather-aware disease risk alerts | ★★ | new module |
| Offline PWA caching the app and model weights | ★ | `manifest.json`, `sw.js` |

Search the source for `TODO(student)`.

## Data and safety notes

Disease descriptions and treatments are summarised from standard extension guidance and simplified. Pesticide names are examples only; the label and the local extension office are the authority. The synthetic samples are drawn on a canvas and exist to demonstrate the pipeline without a dataset.
