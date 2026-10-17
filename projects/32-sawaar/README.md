# 32 · Sawaar — Crash Detection for Motorcyclists (idea #95)

**A night-HUD ride companion for motorcyclists in Pakistan, in Urdu and English.** Mount the phone, confirm your helmet (and your pillion's), and ride: a full-screen HUD shows speed, a g-force meter, lean angle and heading while a staged crash detector watches the motion sensors. After a hard impact, tumbling and stillness it starts a 30-second countdown with a siren, vibration and a huge "I am OK" button; if nobody answers it opens an alert screen with a Rescue 1122 call button and prefilled SMS and WhatsApp messages carrying your location and medical ID. The **Signal Lab** is the FYP evaluation chapter: labelled synthetic traces (including Pakistani speed breakers and potholes), a confusion matrix, precision / recall / F1, threshold sliders with a live ROC curve, and a recorder for your own traces with CSV export. Around it: ride coaching, trips, a printable medical card and helmet sticker with a QR code, maintenance and fuel log, a fleet dashboard and hazard pins. **Not a certified safety device.** سوار اردو اور انگریزی دونوں میں ہے، اردو میں مکمل دائیں سے بائیں ترتیب کے ساتھ۔

**Scale:** 9,096 lines in one `index.html` (≈ 7,520 lines of JavaScript including the 526-line shared QR encoder and an 889-key bilingual dictionary, ≈ 1,410 lines of CSS) · 13 routed modules plus the live ride HUD, crash alarm layer, search palette, spotlight tour, shortcuts sheet and five print layouts · 114 sprite icons · seed of 30 rides (3,510 track points, 279 km, 52 harsh events) around Lahore and Karachi, 10 fleet riders with 320 harsh events and 8 alerts, 18 hazard pins, 14 fuel fills, 9 maintenance items with 14 service records, 3 emergency contacts, 2 recorded traces (1,600 samples), 2 crash-log entries, 6 notifications and 10 activity-log events · a generated test set of 96 labelled traces (8 scenarios × 12, 24 s at 50 Hz) · 6 bilingual lessons and a 12-question quiz · 8 student TODOs.

## Getting started

- Open `index.html` in Chrome, Edge, Firefox or Safari. It works from `file://` and offline; only Google Fonts (Barlow, Barlow Condensed, Noto Nastaliq Urdu) come from the network and fall back to system fonts.
- **No phone needed to try it.** Press **Start ride**: with *Sensor source = Auto* the app tries the phone's motion sensors and, if none deliver data within 2.5 s (any desktop browser, headless Chromium), replays a simulated ride on a seeded Lahore or Karachi route through the same detector. The simulator pad on the HUD injects a speed breaker, pothole, hard brake, skid, crash or phone drop; *+10 s* fast-forwards. Choose *Crash test* in *Simulated route* to see the whole countdown.
- **On a real phone** serve the folder over HTTPS or `localhost` (`npx serve .`) — DeviceMotion, Geolocation and the Wake Lock need a secure context. iPhone asks for motion permission when you tap *Start ride*. **The screen must stay on:** browsers stop sensors (and the countdown) when the phone locks; Sawaar holds a Screen Wake Lock where supported (Chrome / Edge / Safari 16.4+).
- **Alerts are prepared, never sent automatically.** A web page cannot send SMS or WhatsApp messages or place calls; the alert screen opens your own apps with the text ready. Demo contacts use placeholder numbers and their buttons are disabled; add a real contact in *Medical ID* to see working `sms:` and `wa.me` links.
- Urdu: the `اردو` button in the top bar (or `u`). Night HUD / daylight: the sun / moon button (or `d`). A 7-step tour runs on first open (replay it from Settings or the More sheet). `?` lists all keyboard shortcuts.
- **Where data lives:** `localStorage` under the `swr.` prefix — `settings`, `engine` (live thresholds), `profile`, `contacts`, `bike`, `services`, `fuel`, `trips`, `hazards`, `riders`, `fleetEvents`, `fleetAlerts`, `recordings`, `crashLog`, `inbox`, `log`, `quiz`, `lab`, `meta`, plus `lang`.
- **Reset:** Settings → Your data → *Reset to demo*, *Demo data* off (empty app, keeps settings), *Erase everything*, or Export / Import JSON.

## The problem

Motorcycles are Pakistan's family car, delivery van and taxi, and they dominate crash statistics. In May 2026 alone Punjab recorded 43,160 road crashes, 434 deaths and over 23,000 serious injuries, with motorcycles involved in roughly 70–80 % of crashes ([SLD, June 2026](https://sld.com.pk/2026/06/12/road-safety-and-governance-in-pakistan-addressing-rising-road-accidents-through-effective-policy-reforms/)). Helmets are unaffordable for many families and pillion riders rarely wear one ([GlobalGiving: One helmet can save a life in Pakistan](https://www.globalgiving.org/projects/one-helmet-can-save-a-life-in-pakistan/reports/)); Islamabad now fines pillions without helmets ([The News](https://www.thenews.pk/latest/1326162-motorcyclists-pillion-riders-to-face-fine-for-not-wearing-helmet-in-islamabad)). A rider who crashes alone at night, on a ring road or an unlit street, may lie for a long time before anyone calls Rescue 1122. Phones already carry accelerometers, gyroscopes and GPS; Sawaar shows, honestly and in Urdu, how far a web app can go in detecting a crash and getting help — and where it stops.

## What is built

| Area | Features |
|---|---|
| Ride mode | Pre-ride checks: rider helmet (rides without it lose 20 points and ask for confirmation), pillion with / without helmet, dupatta / chaadar warning for pillions; sensor source (auto / simulator / phone sensors), road type with an illustrative limit, simulated route and speed (1–8x); feature-detected chips for motion, GPS, wake lock and secure context; DeviceMotion + DeviceOrientation with the iOS `requestPermission` prompt; `watchPosition` speed and distance (speed derived from successive fixes when the receiver gives none); Screen Wake Lock re-acquired on `visibilitychange`; full-screen night HUD: speed arc (red over the limit), friction-circle g-meter with trail and a high-pass impact bar with the threshold mark, lean gauge with max-lean markers, heading, clock, five detector stage lights, six trip stats, hazard warnings within 150 m, coaching messages; huge buttons: pause, SOS and End ride as press-and-hold, minimise to a banner; simulator pad and +10 s |
| Crash detection engine | Low-pass gravity estimate, high-pass "linear" acceleration, jerk, rotation rate, tilt between gravity before and after; streaming state machine riding → impact → tumble → stillness → confirmed with four rejection paths (parked phone drop with free fall, bump / speed breaker without tumble, still riding, moved on); 30-second countdown (15–60 s configurable) with a WebAudio hi-lo siren that gets louder in the last 10 s, vibration, ticks, aria-live announcements, a focused "I am OK" button (also `o`), "Get help now", mute; alert screen with Rescue 1122 `tel:`, "SMS all", per-contact SMS / WhatsApp / call, editable message in English or Urdu with a map link, accuracy, blood group and allergies; extra high-accuracy fix during the countdown; manual SOS; crash log; silent practice mode; after a false alarm, the last ~40 s of samples can be saved to the Lab as a labelled trace |
| Engine page | Live state, the state machine drawn with the current thresholds and numbered rejection paths, 11 threshold sliders that drive rides, countdown length, "copy from Lab", try any scenario on a fresh seed with its plot and decision log, practice countdown, alarm sound test, crash log, what it cannot do |
| Signal Lab | Seeded physics-lite trace synthesiser (8 labelled scenarios: normal riding, speed breakers single/double/triple, potholes with handlebar jolt, hard braking, phone dropped while parked, low-speed skid, high-speed impact, fall while stationary; phone on the mount or in a pocket; noise level); detector run on the whole set; precision, recall, F1, accuracy, false-alarm rate, mean time to alert; confusion matrix; parametric ROC with AUC over any of 7 thresholds, updated live as sliders move; per-scenario rates; trace viewer (impact g, rotation °/s and GPS speed lanes with thresholds, tumble and stillness windows, alert / reject markers) with a plain-language decision log; outcome filter (TP / FN / FP / TN); dataset regeneration (seed, size, noise, pocket share) and CSV export; recorder (phone sensors, the running ride, or the simulator) with labels, include toggles, CSV export / import; printable evaluation report |
| Ride coach | Explainable score per ride (harsh braking, harsh acceleration, sharp cornering, over the limit for 3 s, phone touched while moving, night riding, helmets); score trend with a 5-ride average; events per type; weekly summary vs the previous week with a tip for your most frequent event; 8 badges with progress; helmet streak; printable weekly summary |
| Trips | 30 seeded rides around Lahore and Karachi plus your own; filters, sort and search; route thumbnails; detail with a schematic canvas map coloured by speed, events and hazard pins, a speed / event timeline, score breakdown, helmet chips, GPX export, printable trip report; optional Leaflet street map when online |
| Medical ID and emergency card | Name, blood group, allergies, conditions, medication, notes, city, bike registration, organ donor; emergency contacts with Pakistani / international number validation; wallet card (front and back) and helmet sticker with a QR code from the shared offline encoder, readable by any camera as plain text; QR as PNG; print layout at card size |
| Safety and maintenance | Helmet streaks and a 35-day helmet calendar (rider and pillion), pillion helmet rate, helmet buying guide, dupatta / chaadar warning, night / rain / fog / smog tips; bike presets (CD 70, CG 125, YBR 125, GD 110S, Pridor, generic 70 cc, e-bike), odometer that grows with every ride, 8 service items by km or days with due / overdue bars and notifications, service history with cost; fuel log with full-to-full km per litre, Rs per km, 30-day spend and a chart against the bike's typical figure; kWh / Wh-per-km mode for e-bikes |
| Fleet dashboard | Fictional delivery fleet plus "You": score, km, rides, harsh events, helmet %, crashes, weekly score sparkline; sort; rider detail with trend and harsh-event charts and a WhatsApp coaching note template; harsh-event hotspot heatmap (16 × 11 grid, Lahore / Karachi, by event type) with the top three hotspots; alerts inbox with acknowledgement; printable monthly safety report with recommendations |
| Hazard pins | Potholes, open manholes, unmarked speed breakers, broken signals, standing water and other hazards placed by tapping a canvas map of your own routes; distance to your rides (point-to-segment), "still there" counts, mark fixed, filters, JSON export; the HUD warns within 150 m; local only |
| Learn | Six bilingual lessons (helmets, lane discipline, signs with drawn road signs, pillions and dupatta, night / rain / fog / smog, crash first aid with a 1122 call button), emergency numbers, a 12-question quiz with explanations, review and history |
| Platform | Settings (language, night HUD / daylight theme, three text sizes, reduce motion, alarm sound and volume, vibration, hazard warnings, sensor permission and location tests, demo data, storage used, JSON export / import, reset, erase); notifications inbox and activity log (CSV, print); search palette over pages, actions, trips, hazards, lesson points and fleet riders; 7-step spotlight tour; keyboard shortcuts; skip link, labels, focus styles, aria-live, keyboard-closable sheets |

## Module map

| Module | Key functions | Storage keys |
|---|---|---|
| State and seed | `loadState`, `persist`, `seedApp`, `emptyApp`, `setDemo`, `demoSeed`, `makeRoute`, `synthTrip`, `seedRiders`, `seedFleetEvents`, `seedHazards`, `seedFuel`, `seedServices`, `seedRecordings` | all `swr.*`, `swr.meta` |
| Signal chain | `LowPass3`, `SignalChain.push`, `SignalChain.leanStep`, `RollingStd`, `angleDeg`, `rng`, `gauss` | — |
| Crash engine | `CrashEngine.push`, `trackFreefall`, `snapGravity`, `watchImpact`, `watchTumble`, `watchStill`, `keptRiding`, `confirm`, `reject`, `ack`, `stage` | `swr.engine` |
| Trace synthesiser | `Synth` (`ride`, `idle`, `brake`, `pulse`, `speedBreaker`, `pothole`, `handle`, `freefall`, `impact`, `tumble`, `topple`, `lie`, `crashImpact`, `crashSkid`, `dropPhone`, `recover`), `BUILDERS`, `buildTrace` | — |
| Evaluation | `makeDataset`, `traceFeatures`, `runTrace`, `evaluate`, `metrics`, `rocCurve`, `traceToCsv`, `csvToCols` | `swr.lab` |
| Sensors and simulator | `detectSensors`, `askMotionPermission`, `startSensors`, `startGps`, `keepAwake`, `stopSensors`, `SimRide` (`fill`, `step`, `runEvent`, `inject`, `recover`, `pull`, `where`) | — |
| Ride session | `renderRide`, `startRide`, `chooseSource`, `checkSensorsAlive`, `rideLoop`, `advanceSim`, `ingest`, `onFix`, `checkHazards`, `coachStep`, `coachEvent`, `onRideTouch`, `onEngineEvent`, `endRide`, `buildTrip`, `fetchRoadSpeedLimit` (stub) | `swr.trips`, `swr.bike` |
| HUD | `hudHtml`, `speedArcSvg`, `gMeterSvg`, `leanSvg`, `stageLights`, `showHud`, `hudFrame`, `hudGauges`, `hudStats`, `bindHold`, `hudMsg`, `hudWarn` | — |
| Crash alarm and audio | `startCountdown`, `alarmTick`, `toAlert`, `imOk`, `openManualSos`, `alertMessage`, `smsHref`, `waHref`, `renderAlarm`, `offerFalseAlarmTrace`, `sendAlertViaServer` (stub), `listenForImOk` (stub); `startSiren`, `setSirenLevel`, `stopSiren`, `beep`, `chime` | `swr.crashLog` |
| Engine page | `renderEngine`, `machineSvg`, `tryHtml`, `crashLogHtml`, `engineLiveTick` | `swr.engine` |
| Signal Lab | `ensureDataset`, `runEvaluation`, `scheduleRoc`, `labEvalHtml`, `labTracesHtml`, `traceViewHtml`, `labDataHtml`, `datasetCsv`, `startRecording`, `recordSample`, `stopRecording`, `addRecording`, `saveBufferAsRecording`, `importRecordingCsv`, `printLabReport`, `trainLearnedClassifier` (stub); visuals `paramSliders`, `tracePlot`, `stageWindows`, `explainTrace`, `confusionHtml`, `rocSvg`, `perKindTable` | `swr.lab`, `swr.recordings` |
| Coach | `rideScore`, `scoreExplain`, `helmetStreak`, `badgeStatus`, `weekSummary`, `topEventKind`, `renderCoach`, `printWeekly` | (derived from `swr.trips`) |
| Trips and maps | `mapView`, `boundsOf`, `drawBase`, `drawRoute`, `drawPin`, `renderTrips`, `routeThumb`, `renderTripDetail`, `timelineSvg`, `tripGpx`, `printTrip`, `loadLeaflet`, `showStreetMap` | `swr.trips` |
| Medical ID | `renderMedical`, `qrPayload`, `walletFront`, `walletBack`, `stickerHtml`, `openContactForm`, `printCard`, `downloadQrPng`; shared `QR.encode`, `QR.toSvg`, `QR.toCanvas` | `swr.profile`, `swr.contacts` |
| Garage | `servicesStatus`, `checkServicesDue`, `helmetCalendar`, `openServiceDone`, `fuelStats`, `garageFuelHtml` | `swr.bike`, `swr.services`, `swr.fuel` |
| Fleet | `meAsRider`, `riderTable`, `hotspotGrid`, `heatCardHtml`, `topHotspots`, `fleetAlertsHtml`, `openRider`, `printFleetReport`, `syncFleet` (stub) | `swr.riders`, `swr.fleetEvents`, `swr.fleetAlerts` |
| Hazards | `distToRoutes`, `hazardBounds`, `drawHazardMap`, `onHazardMapClick`, `openHazardForm`, `shareHazardsCommunity` (stub) | `swr.hazards` |
| Learn, inbox, settings | `renderLearn`, `renderLesson`, `renderQuiz`, `finishQuiz`; `notify`, `logEvent`, `renderInbox`; `renderSettings`, `exportData`, `importData`, `resetDemo`, `wipeAll`, `connectHelmetSensor` (stub), `requestBackgroundMode` (stub) | `swr.quiz`, `swr.inbox`, `swr.log`, `swr.settings` |
| Search, tour, router | `openPalette`, `paletteIndex`, `startTour`, `showTourStep`, `openShortcuts`, `onKey`, `route`, `renderRoute`, `renderNav`, `applyPrefs`, `setLang`, `boot` | `swr.settings.tourDone`, `swr.lang` |

## Architecture (single file)

```
index.html
├─ <head>        title "… | FYP Template", fonts (Barlow, Barlow Condensed, Noto Nastaliq Urdu), favicon
├─ <style>       tokens (asphalt black, helmet yellow, road-marking white, crash red, calm cyan; concrete
│                daylight theme), motion kit, shell (slim instrument rail, top bar with lane marking, phone
│                tabs), buttons/fields/sliders, cards, sheets, palette, tour, toasts · HUD instruments, live HUD,
│                crash alarm layer, state machine, trace plots, confusion matrix, ROC, trips and maps, wallet
│                card and sticker, garage, fleet heatmap, lessons and quiz, RTL rules, print layouts
├─ <svg>         114-symbol icon sprite generated from the icons the code uses
├─ shell         rail, top bar (search, inbox, theme, language), banner, view, tabs; layers: #hud, #alarm,
│                modal sheet, palette, tour, toasts, live region, print area
└─ <script>
   ├─ utils          store (swr. prefix), esc, numbers/dates, haversine/bearing, phone helpers, toast,
   │                 vibrate guard, motion helpers, SVG line/bar/sparkline charts, modal, print, files
   ├─ i18n           STR [English, Urdu] in four groups (core + ride + alarm, engine + lab, modules,
   │                 platform), t(), tIn(), L()
   ├─ content        CITIES (schematic places and roads), ROAD_TYPES, BIKES, SERVICE_ITEMS, HAZARD_TYPES,
   │                 KIND_TEXT, SCENARIOS, COACH_W, HELPLINES, LESSONS (6), QUIZ (12), HELMET_GUIDE, BADGES
   ├─ state + seed   DEFAULT_SETTINGS, EMPTY, loadState, persist, notify, logEvent, demo seed generators
   ├─ QR encoder     shared kit encoder, pasted unchanged (ISO/IEC 18004, versions 1–40, ECC L–H)
   ├─ signal chain   rng, gauss, LowPass3, SignalChain, RollingStd
   ├─ crash engine   ENGINE_DEFAULTS, ENGINE_PARAMS, CrashEngine
   ├─ synthesiser    TRACE_KINDS, Synth
   ├─ evaluation     BUILDERS, buildTrace, makeDataset, traceFeatures, runTrace, evaluate, rocCurve, CSV
   ├─ sensors        SENS, LIVE, permission, motion, GPS, wake lock, SimRide
   ├─ audio          siren, beeps, chime
   ├─ views          ride page + session · HUD · crash alarm · lab visuals · engine page · Signal Lab ·
   │                 coach · trips + maps · medical ID · garage · fleet · hazards · learn · inbox ·
   │                 settings · search + tour + shortcuts · about
   └─ router + boot  ROUTES, rail/tabs/More sheet, banner, applyPrefs, setLang, resize, boot()
```

## Domain logic and algorithm notes

**Filters and features (per sample, any rate).** With the real sample interval Δt and cut-off f_c, the gravity low-pass is `g ← g + α(a − g)`, `α = Δt / (1/(2πf_c) + Δt)`, f_c = 0.6 Hz. Features: `acc = |a|/g₀` (1 g at rest), `lin = |a − g|/g₀` (the high-pass part, where impacts appear), `jerk = |Δlin|/Δt` (g/s), `rot = |ω|` (°/s). Tilt is the angle between the current gravity estimate and a snapshot taken 1.2 s before the candidate (before a slow topple began). Lean on the HUD is a complementary filter `φ ← 0.97(φ + ω_roll·Δt) + 0.03·φ_ref`, where `φ_ref = atan(v·ψ̇/g)` while moving (in a balanced turn the accelerometer alone cannot see the lean) and the gravity tilt when stopped.

**State machine (defaults in parentheses, all editable).**
```
riding:   if lin ≥ impactG (1.5 g) and jerk ≥ jerkG (10 g/s) → candidate
            moving = GPS speed ≥ minKmh (8 km/h) within the last preS (5 s), or speed unknown
            if not moving and a free fall (|a| < 0.4 g) of ≥ freefallMs (160 ms) ended < 1 s ago → reject "drop"
impact:   for tumbleS (3 s): track max rot and max tilt
            tumbled if rot ≥ rotDps (200 °/s) or tilt ≥ tiltDeg (45°); else reject "bump"
stillness: reject "riding" if speed > continueKmh (15 km/h) for 2 s
            confirm when σ(|a|) over stillS (5 s) < stillStdG (0.05 g) and speed ≤ 5 km/h
            reject "moved" if not confirmed within 20 s of the impact
confirmed: 30 s countdown → alert; "I am OK" → ack: back to riding after a 10 s cool-down
```
**Synthetic traces.** Riding: specific force along the bike's vertical `g/cos φ` plus longitudinal acceleration on the forward axis, engine and road vibration that grows with speed, gyro yaw `ψ̇ cos φ` and roll `dφ/dt`. Wheel strikes are half-sine pulses (front, then rear one wheelbase later: `Δt = 1.25 m / v`); free fall reads ≈ 0 g; impacts are half-sine pulses clipped at ±8 g per axis; tumbles rotate the gravity vector with Rodrigues' formula at the reported gyro rate (so rates and orientation agree); GPS is 1 Hz with lag `v_gps ← 0.35 v_gps + 0.65 v`. A pocket placement rotates every vector by a fixed random rotation, which the orientation-free features ignore. Ground truth: crash = skid, high-speed impact, fall while stationary.

**Metrics.** `precision = TP/(TP+FP)`, `recall = TP/(TP+FN)`, `F1 = 2PR/(P+R)`, `FPR = FP/(FP+TN)`; time to alert = confirm time − labelled event time. The ROC is parametric: one detector run per value of the swept threshold (others fixed), points sorted by FPR with (0,0) and (1,1) added, AUC by the trapezoid rule. Features are cached per trace, so a 13-point sweep only re-runs the state machine. Default set (seed 2026, 96 traces): **precision 1.00, recall 0.83, F1 0.91, AUC 0.978** over the impact threshold, mean time to alert 8.5 s; over six other seeds × 160 traces, impacts 120/120, speed breakers, potholes, braking and parked drops 0/120, skids 73 %, stationary falls 60 % (low-energy falls are the honest weak spot).

**Coaching.** Longitudinal acceleration is the 2-second slope of GPS speed (harsh braking ≤ −2.8 m/s², harsh acceleration ≥ 2.6 m/s²; GPS smoothing hides true peaks, so these sit below the usual 0.35 g telematics figures); lateral `a = v·ψ̇` (sharp cornering ≥ 3.6 m/s² above 15 km/h); over the limit = more than 10 % over the road-type limit for 3 s; touches are pointer events on the HUD while moving faster than 10 km/h. `score = 100 − Σ w·n·10/max(km, 2) − night 5 − no helmet 20 − pillion without helmet 10` with weights braking 4, acceleration 3, cornering 3, speeding 3, touches 5.

**Other calculations.** Haversine distance and initial bearing; hazard-to-route distance is point-to-segment on a local equirectangular projection; fuel uses the full-to-full method (`km/L = Δodometer / litres added`); maintenance due fraction = `(odometer − last) / interval` or days / interval; the hotspot grid bins harsh events over the schematic city bounds.

## Left for students

| Extension | Difficulty | Start here |
|---|---|---|
| Automatic SMS through a server gateway after the countdown, with delivery receipts and retries | Hard | `sendAlertViaServer()` |
| Learned classifier (logistic regression / decision tree on windowed features, k-fold CV) compared with the rule engine on the same ROC | Hard | `trainLearnedClassifier()` |
| Screen-off detection in a native wrapper (Capacitor or TWA with an Android foreground service) | Hard | `requestBackgroundMode()` |
| Helmet-mounted IMU over Web Bluetooth fused with the phone signals | Hard | `connectHelmetSensor()` |
| Fleet server sync (REST / WebSocket, rider consent, manager roles, offline queue) | Medium | `syncFleet()` |
| Real speed limits from OpenStreetMap `maxspeed` tags via Overpass, cached per road | Medium | `fetchRoadSpeedLimit()` |
| Community hazard map (grid-snapped for privacy, two reports to publish, expiry) | Hard | `shareHazardsCommunity()` |
| Hands-free "I am OK" in Urdu and English with the Web Speech API | Medium | `listenForImOk()` |

Search the source for `TODO(student)` to find each stub; the About page lists the same eight with a one-line hint each.

## Data and safety notes

- **Not a certified safety device.** Sawaar can miss crashes (especially slow, low-energy falls) and raise false alarms. It never contacts anyone automatically: a browser cannot send SMS or WhatsApp messages or place calls, and it cannot run with the screen off or the tab closed. These limits are stated on the Ride page, the Engine page, the alarm, Settings and About.
- **Illustrative data.** All riders, rides, hazards, contacts (placeholder numbers such as `0300-0000101`), fleet alerts, road-type speed limits, fines mentioned in lessons, service intervals, prices (petrol ≈ Rs 258–274/L, service costs) and typical fuel figures are made up or approximate. Verify limits and fines with the traffic police, intervals with your bike's manual and prices with current rates. The schematic city maps are approximate and not survey data. Synthetic sensor traces are a test bench, not crash data: validate on recorded rides before making any claim.
- **First aid** content is general information, not medical training; it follows widely taught guidance (call 1122, do not remove the helmet unless the airway is blocked, do not move the neck, direct pressure on bleeding). Take a recognised first-aid course.
- **Privacy.** Everything stays on the device. Medical details leave it only inside a message you choose to send or on a card you print. Network use: Google Fonts, and only on request Leaflet 1.9.4 from unpkg (with subresource integrity) and OpenStreetMap tiles, which reveal the area you view.
- **Testing a recorder safely.** Never stage a real fall; record speed breakers, potholes, braking, or drop the phone onto a cushion.

## Verification

- **Harness** (`check.mjs`, 1366×850 and 390×844): 32 states — ride page, riding HUD, crash countdown, alert screen, engine page, Lab evaluate / traces / dataset / recording, coach, trips, trip detail, medical ID, garage safety / maintenance / fuel, fleet, hazards, learn, first-aid lesson, quiz feedback, inbox, settings, about, Urdu ride page, Urdu Lab, Urdu alert, daylight coach, daylight HUD, tour, contact form sheet and search — with no console, page or request errors and no horizontal overflow; every screenshot was inspected.
- **Behaviour script** (Playwright, 117 checks, all passing): seed and storage prefix; tour; signal chain at rest; engine stage sequences; **high-speed impacts detected 25/25, speed breakers 0/25, parked drops 0/25, potholes and hard braking 0/25**; confusion-matrix arithmetic, F1, determinism, ROC ordering and monotonicity, CSV round trip; Lab sliders updating metrics live, applying thresholds to rides, ROC re-sweep, trace filter and decision log, dataset regeneration, recorder; simulated ride with distance, hazard warning, harsh-braking detection, pause; **simulated crash → countdown → real click on "I am OK" cancels, silences the siren, logs a false alarm, re-arms the engine and does not come back**; countdown timeout → alert with SMS / WhatsApp / 1122 links and an Urdu message; SOS and End ride press-and-hold (short press ignored); trip saved with odometer update; auto source falling back to the simulator; coach formula; trips filter, GPX, map; emergency QR decoded back with jsQR; contact validation; fuel km/L and Rs/km; maintenance reset; helmet calendar; fleet heatmap binning and alert acknowledgement; hazard placement by tapping the map; first aid and a 12/12 quiz; inbox and log; theme, Urdu RTL, JSON export / import / rejection, demo off / on; engine page; practice countdown and keyboard `o`; search and shortcuts; no overflow on 19 routes × 2 languages × 2 widths; no untranslated keys; no emoji; 8 TODOs; zero console errors.

## Evaluation ideas for the FYP report

- **Detector on real rides:** record 20+ rides (speed breakers, potholes, braking, parked drops onto a cushion) with the recorder, add them to the Lab set and report precision, recall, F1 and false alarms per 100 km; compare with the synthetic-only numbers.
- **Threshold study:** sweep each threshold, plot ROC curves, choose an operating point and justify it (a missed crash vs a false alarm that wakes a family at night).
- **Time to alert:** distribution of impact → countdown latency; trade-off between `stillS` and false alarms.
- **Phone placement:** mount vs pocket vs bag — feature robustness and recall.
- **"I am OK" usability:** time to cancel with gloves, at night, for Urdu-first and English-first riders; SUS questionnaire.
- **Coaching effect:** two-week before/after study of harsh events per 100 km and helmet streaks.
- **Battery and screen:** battery drain per hour with the wake lock on, on a low-end Android phone.
