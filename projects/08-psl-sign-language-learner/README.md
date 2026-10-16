# 08 · Ishara — PSL Learner (idea #25)

> **Scale:** 4,067 lines in one `index.html` · 17 modules (6 original, 11 added) · 123 signs, 39 fingerspelling letters, 28 grammar sentences, 4 conversation scenarios (29 turns), 16 handshapes, 14 lessons, 12 badges.

**Learn Pakistan Sign Language with animated sign schematics, Urdu fingerspelling, a PSL grammar sentence builder, everyday conversations, spaced-repetition quizzes and webcam handshape practice.** Every sign is modelled as *handshape + location + movement* and drawn procedurally as an animated SVG figure, so the content is data, not video files. English / اردو throughout.

## Getting started

1. Open `index.html` in Chrome, Edge, Firefox or Safari. Lessons, fingerspelling, sentences, conversations, quiz, dictionary, progress, classroom and settings all work fully offline.
2. For **Practice** (camera and self-recording), serve the folder over HTTP (`python3 -m http.server` then `http://localhost:8000/`) because browsers block camera access on `file://`. TensorFlow.js and the MediaPipe Hands model (about 10 MB) load from a CDN on first use. Without network the page shows a fallback message and offers an "I did it" self-check instead.
3. To see a populated app straight away, go to **Settings → Load demo progress**. For a sample class, go to **Classroom → Teacher mode** (default PIN `1234`) and choose **Load demo class**.
4. All state lives in `localStorage` under the `psl:` prefix. Recorded clips are kept in IndexedDB (`ishara-clips`). **Settings → Export everything** downloads a full backup and **Import** restores it. **Settings → Delete all data** wipes the device. **Progress → Reset** clears learning progress only.

## The problem

Pakistan has an estimated one million deaf people, and very few hearing family members, teachers or doctors can sign. Existing PSL material is scattered across PDFs and videos. There is no learner-friendly, gamified, offline-capable introduction that also covers names (fingerspelling), grammar and real situations, and none that a teacher can run in a classroom without a server.

## What is built

| Area | Features |
|---|---|
| Lessons | 14 lessons (greetings, everyday basics, family, questions, people & places, feelings, time, numbers, health, food & drink, school, actions, colours, animals). Lessons unlock in order and show progress rings and a "continue" button. Each sign gets a stage with the animated schematic, handshape/location/movement chips, a bilingual description and Urdu text-to-speech (optionally automatic), plus a lesson-complete celebration with a quiz on that lesson |
| Daily goals | XP and new-sign targets per day (configurable), goal rings on the Learn page, streak and due-review pills, and an inbox entry plus confetti when the goal is reached |
| Sign schematics | Procedural hand-glyph renderer (16 handshapes as finger-state vectors), 16 body locations and 12 movement animations, for one- and two-handed signs. Glyphs are reused in chips, filters, tiles and letter cards |
| Urdu fingerspelling | Alphabet of 39 Urdu letters, each a handshape plus a small movement, every card flagged "needs verification". Includes a letter detail modal, "mark known", camera practice per letter and a spelling game with two modes ("read handshapes" picks the word, "spell with handshapes" taps tiles in order, with distractors). A spell-a-name tool plays a name letter by letter and queues the letters for camera practice |
| Sentence builder | 28 sentences across 7 simplified PSL grammar rules (time first, SOV, topic–comment, question sign last, yes/no, negation after, describer after noun), each with a non-manual-marker cue. Tiles move by drag-and-drop or tap (keyboard accessible), with feedback giving gloss, rule and face, a show-answer option, a rule filter, and per-sentence completion with XP |
| Conversations | 4 scenarios (doctor visit, market, class, visiting family) with 29 bilingual dialogue turns, each turn carrying its signs. Supports auto-play or step-through, opening any sign in the dictionary, and practising or quizzing the scenario's signs. Completing a scenario earns XP |
| Quiz | 10-question rounds, or due signs only. Four question types: sign → word, word → sign, handshape, location. SM-2-lite spaced repetition (1 → 3 → 7 → 14 → 30 days, shortened on a miss) and keyboard answers 1–4. The result screen lists missed signs and sends them straight to camera practice |
| Dictionary | 123 signs. Search in English or Urdu, plus filters by category, by handshape (16 glyph chips) and by state (learned / new / due). Signs show mastery dots and open a detail modal with the animated sign, "practise this", "mark learned", a video URL slot and a community correction form with a verified flag |
| Practice | Webcam with TensorFlow.js hand-pose detection. Draws the 21 landmarks live, classifies each finger as extended, curved or folded, matches it against the target handshape and fills a hold-to-pass bar (hold time is configurable). Also has a left-handed mirror, confetti and XP on pass, a practice queue fed from lessons, quizzes, letters, sentences, scenarios and assignments, and a no-camera self-check fallback |
| Practice analytics | Per-finger × target-state error heatmap built from every camera frame, with "weakest finger" called out. Also shows session history (sign, mode, pass/miss, time to pass), sessions per day for 14 days, pass rate and average time to pass |
| Self-review recording | MediaRecorder clips of up to 8 s while the camera runs, stored locally in IndexedDB (falls back to memory if storage is blocked). A playback modal shows the mirrored clip beside the schematic, and clips can be deleted. The button disables with an explanation when MediaRecorder is unsupported |
| Classroom | Teacher mode behind a PIN (changeable). Assignment builder adds a whole category or picks signs, with a due date and a minimum quiz score. The roster has local student codes. Student progress files import into a progress board with per-assignment completion. Exports a CSV roster, a printable board and a class-pack file that students import. The student view shows assignment status (in progress, done, overdue) with practise and quiz shortcuts |
| Progress | XP and levels, streak, signs learned, quiz accuracy, letters known and sentences done. Shows a 14-day XP chart against the goal, mastery rings per category, 12 badges, the activity log, JSON export, print and "export for teacher" |
| Settings | Language, large text, high contrast, reduce motion, left-handed, automatic Urdu speech, hold time, daily goals, student name and code, teacher PIN, export/import/demo/reset, and replay of tour and shortcuts |
| Notifications inbox | Bell with unread count. Entries cover badges, daily goal reached, due reviews, streak at risk (after 18:00), assignments due within 2 days and finished lessons. Entries are deduplicated by key, with mark-all-read and clear |
| Onboarding tour | 7 spotlight steps over the real UI that adapt to desktop tabs or the mobile bottom bar. Starts from a welcome banner on first open and can be replayed from Settings |
| Keyboard & a11y | `1–9` jump to sections, `1–4` answer, `/` search, `L` language, `N` inbox, `?` shortcuts, `Esc` closes. Also focus-visible outlines, aria labels on icon buttons, 40 px touch targets and a reduced-motion setting on top of `prefers-reduced-motion` |

## Module map

| Module | Main functions | Storage |
|---|---|---|
| Utils / i18n / settings | `store`, `esc`, `ic`, `toast`, `countUp`, `animateIn`, `confetti`, `download`, `heroIcon`, `t`, `applySettings`, `saveSettings` | `psl:lang`, `psl:settings` |
| Sign model & data | `SHAPES`, `LOC`, `CATS`, `RAW → SIGNS`, `descOf`, `edits`, `FS → LETTERS`, `SENTENCES`, `SCENARIOS` | `psl:edits` |
| Renderer | `handGlyph`, `moveClass`, `signSvg`, `shapeChip`, `miniGlyph`, `letterSvg`, `ring` | — |
| Progress model & badges | `P`, `learn`, `rate`, `addXP`, `streak`, `level`, `checkBadges`, `syncBadgesSilently`, `dueIds` | `psl:progress` |
| Daily goals | `todayXp`, `todaySigns`, `checkGoal`, `goalsCard` | `psl:settings` (targets) |
| Notifications inbox | `notify`, `openInbox`, `updateBell`, `bootNotifications` | `psl:inbox` |
| Lessons | `renderLearn`, `lessonCard`, `renderStage`, `finishLesson`, `openLesson`, `speak` | `psl:progress` |
| Quiz (SRS) | `startQuiz`, `makeQuestion`, `renderQuestion`, `answerQuestion`, `renderQuizResult`, `quizKey` | `psl:progress.srs` |
| Dictionary | `renderDict`, `dictMatches`, `openSign`, `saveSuggestion` | `psl:edits` |
| Practice (camera) | `toggleCam`, `loadScript`, `fingerStates`, `fingerGood`, `drawHand`, `loop`, `updateHold`, `onPass`, `pracTarget`, `letterTarget` | `psl:progress.cam` |
| Practice analytics | `recordFrame`, `endSession`, `heatmapHtml`, `worstFinger`, `sessionBars`, `sessionRows`, `analyticsHtml` | `psl:cam` |
| Self-review recording | `toggleRec`, `startRec`, `stopRec`, `saveClip`, `clipStore`, `playClip`, `deleteClip` | `psl:clips` + IndexedDB `ishara-clips` |
| Fingerspelling | `renderSpell`, `renderAlphabet`, `openLetter`, `spellOf`, `fsWords`, `startSpellGame`, `answerRead`, `wireSpellQuestion`, `renderNameSpeller`, `playName` | `psl:fs` |
| Sentence builder | `renderSentences`, `loadSentence`, `renderExercise`, `wireTiles`, `moveToken`, `checkSentence` | `psl:sent` |
| Conversations | `renderTalk`, `openScenario`, `renderDialogue`, `stepTurn`, `togglePlay` | `psl:conv` |
| Classroom | `renderClass`, `assignmentPct`, `teacherViewHtml`, `saveAssignment`, `syncAsgPicker`, `exportStudentFile`, `importAny`, `exportRosterCsv`, `loadDemoClass` | `psl:class` |
| Settings / tour / shortcuts / nav | `renderSettings`, `exportAll`, `importBackup`, `seedDemo`, `startTour`, `showTourStep`, `openShortcuts`, `onKey`, `go`, `renderNav`, `applyLang` | `psl:tour` |

## Architecture (single file)

```
index.html
├─ <style>        teal/sun/coral tokens (+ high-contrast, large-text, reduced-motion variants),
│                 Fredoka + Rubik + Noto Nastaliq Urdu, motion kit, schematic animations,
│                 learn · dictionary · quiz · practice/heatmap · fingerspelling · sentence tiles ·
│                 dialogue bubbles · progress · classroom · modal/sheet/tour, mobile + print rules
├─ <svg sprite>   67 inline icon symbols (no emoji)
├─ <header>       brand, notification bell, streak + XP chips, language toggle, section tabs
├─ <main>         eleven <section data-page> blocks
├─ bottom nav + "More" sheet (mobile), modal scrim, toasts, hidden file input
└─ <script>
   ├─ utils          store, esc, ic, toast, shuffle, countUp, animateIn, confetti, download
   ├─ i18n           TXT.en / TXT.ur (430 keys each)
   ├─ settings       SET (accessibility, practice, goals, profile)
   ├─ sign model     SHAPES, LOC, CATS, RAW → SIGNS, descOf() with local edits
   ├─ fingerspelling FS → LETTERS, FS_FOLD, FS_NAMES
   ├─ grammar        RULES, NMM, SENTENCES
   ├─ scenarios      SCENARIOS
   ├─ renderer       handGlyph(), signSvg(), shapeChip(), letterSvg(), ring()
   ├─ progress       P, learn(), rate(), addXP(), streak(), BADGES, checkBadges()
   ├─ goals          checkGoal(), goalsCard()
   ├─ inbox          notify(), openInbox(), bootNotifications()
   ├─ learn          LESSONS, renderLearn(), renderStage(), finishLesson()
   ├─ quiz           startQuiz(), makeQuestion(), renderQuestion()
   ├─ dictionary     renderDict(), openSign(), openModal()
   ├─ practice       loadScript(), toggleCam(), fingerStates(), loop(), onPass()
   ├─ analytics      CAM, recordFrame(), heatmapHtml(), analyticsHtml()
   ├─ recording      clipStore (IndexedDB), startRec(), saveClip(), playClip()
   ├─ spell          renderSpell(), spelling game, name speller
   ├─ sentences      renderSentences(), wireTiles(), checkSentence()
   ├─ talk           renderTalk(), renderDialogue(), togglePlay()
   ├─ progress page  renderProgress(), activityBars(), badgesHtml()
   ├─ classroom      renderClass(), teacher view, imports/exports
   ├─ settings       renderSettings(), exportAll(), seedDemo()
   ├─ tour           TOUR, startTour(), showTourStep()
   ├─ shortcuts      SHORTCUTS, onKey()
   ├─ about          TODOS, renderAbout()
   └─ nav / boot     PAGES, go(), renderNav(), applyLang(), syncBadgesSilently(), bootNotifications()
```

## Domain logic notes

### How handshape matching works
MediaPipe Hands returns 21 landmarks. For each of the four fingers, the ratio *distance(tip, wrist) / distance(MCP, wrist)* classifies the finger:
- extended above 1.65
- curved from 1.3 to 1.65
- folded below 1.3

The thumb counts as extended when its tip is clearly farther from the pinky base than its MCP joint is. The resulting 5-vector is compared with the target handshape. A curved target also accepts an extended finger. When four of the five fingers match for the configured hold time (default 1.2 s), the sign passes. Location and movement are not checked; that is a student extension.

### Practice analytics
Every camera frame adds one check per finger to a 3 × 5 matrix: the target state (folded, curved or extended) by finger. Each frame where the finger does not match also adds one error to the same cell. The heatmap shows errors ÷ checks for each cell, and greys out any cell with fewer than 10 frames. The "weakest finger" is the one with the highest error rate over at least 20 frames. A session runs from the moment a target appears until it passes, is skipped or the camera stops.

### Fingerspelling
A word is spelled by:
1. folding orthographic variants (آ → ا, ئ → ء, ك → ک …),
2. stripping diacritics,
3. mapping each letter to its handshape.

The game only uses words of 2–8 letters that can be fully spelled. Letters that share a handshape differ by a small movement (tap, twist, down…), which the camera cannot yet see.

### Sentence builder
Each sentence stores its PSL gloss order as sign ids, its grammar rule and a non-manual marker. The check compares the order exactly. Feedback teaches the rule and the facial expression, because in PSL the face carries question and negation meaning.

### Classroom without a server
Teacher and students exchange three small JSON files:
- **Class pack** (`app: ishara-classpack`): assignments, created by the teacher and imported by students.
- **Student file** (`app: ishara-student`): code, XP, learned count, quiz accuracy, streak and per-assignment completion, exported by a student and imported by the teacher.
- **Full backup** (`app: ishara`): every `psl:` key.

`importAny()` detects the file type from its `app` field.

## Left for students

| Extension | Difficulty | Start here |
|---|---|---|
| Real PSL video clips recorded with Deaf signers beside each schematic | ★★ | `openSign()` (video slot via `descOf().video`) |
| Full sign recognition: sequence model over landmarks checking location + movement | ★★★ | Practice section header above `CDN`, `loop()`, `fingerStates()` |
| Dynamic fingerspelling: detect the tap/twist that separates letters sharing a handshape | ★★★ | `letterTarget()` |
| Non-manual markers: face-landmark model checks eyebrows / head shake in the sentence builder | ★★★ | `checkSentence()` |
| Verification backend with Deaf-community reviewers; verified badges from the server | ★★ | `edits()` / `descOf()` |
| Class server and accounts replacing the JSON hand-off | ★★ | Classroom section header, `importAny()` |
| Speech-to-sign: Web Speech API → gloss → animated sign sequence | ★★ | `speak()` |
| Offline PWA caching the app shell and model weights | ★ | boot section (`applySettings()` call), new `sw.js` + `manifest.json` |

Search the source for `TODO(student)`; each row above has a marker at its start point.

## Content and data notes

- **All 123 sign descriptions and all 39 fingerspelling handshapes were written for this template as approximations.** They are flagged "needs verification" in lessons, the dictionary and every letter card. Check them against an authoritative PSL dictionary with Deaf community members before classroom use. The correction form and verified flag exist for exactly that workflow.
- The grammar rules are deliberate simplifications for beginners; PSL word order varies with context and signer.
- No data leaves the device. Recordings stay in the browser's IndexedDB and student progress moves only as files the user chooses to share. Clear any recordings before handing a shared school device to someone else.
