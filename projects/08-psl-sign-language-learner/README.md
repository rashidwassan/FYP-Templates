# 08 · Ishara — PSL Learner (idea #25)

**Learn Pakistan Sign Language with animated sign schematics, spaced-repetition quizzes and webcam handshape practice.** Every sign is modelled as *handshape + location + movement* and drawn procedurally as an animated SVG figure, so the content is data, not video files. English / اردو.

## Getting started

1. Open `index.html` in Chrome, Edge, Firefox or Safari. Lessons, quiz, dictionary and progress work fully offline.
2. For **Practice** (camera), serve the folder over HTTP (`python3 -m http.server` then `http://localhost:8000/`) because browsers block camera access on `file://`. TensorFlow.js and the MediaPipe Hands model (about 10 MB) load from CDNs on first use; without network the page offers a "I did it" self-check instead.
3. Progress lives in `localStorage` under the `psl:` prefix. Export it from Progress → JSON; reset from Progress → Reset.

## The problem

Pakistan has an estimated one million deaf people and very few hearing family members, teachers or doctors who sign. Existing PSL material is scattered PDFs and videos. A learner-friendly, gamified, offline-capable introduction does not exist.

## What is built

| Area | Features |
|---|---|
| Lessons | 8 lessons (greetings, everyday basics, family, questions, people & places, feelings, time, numbers), 62 signs, unlock in order, progress rings, "continue" button, per-sign stage with animated schematic, handshape/location/movement chips, bilingual description, Urdu text-to-speech, lesson-complete celebration |
| Sign schematics | Procedural hand glyph renderer (13 handshapes as finger-state vectors), 16 body locations, 12 movement animations (forward, tap, circle, wave, apart/together…), one- and two-handed signs |
| Quiz | 10-question rounds, three question types (sign → word, word → sign, handshape), SM-2-lite spaced repetition (1 → 3 → 7 → 14 → 30 days, shortened on a miss), progress bar, XP, perfect-round badge |
| Dictionary | Search in English or Urdu, category filter, mastery dots, detail modal with animated sign, "practise this", mark learned, video URL slot, community correction form with verified flag (stored locally) |
| Practice | Webcam + TensorFlow.js hand-pose detection: 21 landmarks drawn live, per-finger extended/curved/folded classification, match against the target handshape, hold-to-pass bar, left-handed toggle, confetti and XP on pass, no-camera fallback |
| Progress | XP and levels, day streak, signs learned, quiz accuracy, mastery rings per category, 8 badges, activity log, JSON export |

## Architecture (single file)

```
index.html
├─ <style>        teal/sun/coral tokens, Fredoka + Rubik, motion kit, schematic animations
├─ <svg sprite>   inline icon symbols (no emoji)
├─ <header>       brand, tabs, streak + XP chips, language toggle
├─ <main>         six <section data-page> blocks
└─ <script>
   ├─ utils         store, esc, ic(), toast, countUp, animateIn, confetti
   ├─ i18n          TXT.en / TXT.ur
   ├─ sign model    SHAPES (finger-state vectors), LOC, RAW → SIGNS, descOf() with local edits
   ├─ renderer      handGlyph(), signSvg(), shapeChip()
   ├─ progress      P (xp, learned, srs, quiz, cam, days, log), learn(), rate(), streak(), badges
   ├─ learn         LESSONS, renderLearn(), openLesson()
   ├─ quiz          startQuiz(), renderQuiz()
   ├─ dictionary    renderDict(), openSign()
   ├─ practice      loadScript(), toggleCam(), fingerStates(), loop()
   └─ nav           go(), applyLang()
```

### How handshape matching works

MediaPipe Hands returns 21 landmarks. For each of the four fingers, the ratio *distance(tip, wrist) / distance(MCP, wrist)* classifies it as extended (> 1.65), curved (1.3–1.65) or folded (< 1.3); the thumb is extended when its tip is clearly farther from the pinky base than its MCP joint is. The resulting 5-vector is compared with the target handshape; four of five matching for 1.2 seconds passes. Location and movement are not checked — that is the student extension.

## Left for students

| Extension | Difficulty | Start here |
|---|---|---|
| Record a real PSL video dataset with Deaf signers and show clips beside each schematic | ★★ | `descOf()` → `video`, `openSign()` |
| Full sign recognition (sequence model over landmarks: location + movement) | ★★★ | `loop()`, `fingerStates()` |
| Urdu fingerspelling alphabet with a spelling game | ★★ | `RAW`, new lesson |
| Sentence builder teaching PSL grammar and non-manual markers | ★★★ | new page |
| Verification backend with Deaf-community reviewers; verified badges from the server | ★★ | `edits()` |
| Accounts, classrooms, teacher dashboards | ★★ | `P` model + backend |
| Offline PWA caching the app and model weights | ★ | `manifest.json`, `sw.js` |

Search the source for `TODO(student)`.

## Content note

The 62 seeded sign descriptions were written for this template as approximations and are flagged "needs verification" in the UI. They must be checked against an authoritative PSL dictionary with Deaf community members before classroom use; the correction form exists for exactly that workflow.
