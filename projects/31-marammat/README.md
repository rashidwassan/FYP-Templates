# 31 · Marammat — Used Phone Health Certificate (idea #94)

**A used-phone health check that runs in the browser of the phone being tested, in Urdu and English, and ends with a certificate a buyer can verify.** Eleven guided steps use real browser APIs — full-screen pixel patterns, a touch-coverage grid, stereo speaker tones, a microphone spectrum and an automatic speaker→mic Goertzel loopback, camera sharpness by variance of the Laplacian, DeviceMotion tilt and compass tests, vibration, Battery Status, GPS fix time, storage and memory, a Web Worker CPU benchmark and a WebGL benchmark compared with a reference table — each with a manual fallback. A weighted engine turns the results into a grade (A–D) with reasons and a PKR value range. The repair shop signs the certificate with ECDSA P-256; the QR carries the signed hash and per-section digests, so the **Verify** screen shows which part of a document was edited. Around it: a repair estimator, repair-shop job cards with a QC re-test on the same engine, device passports keyed by a salted IMEI hash, and buyer-safety guides. مرمت اردو اور انگریزی دونوں میں ہے، اردو میں مکمل دائیں سے بائیں ترتیب کے ساتھ۔

**Scale:** 7,954 lines in one `index.html` (≈ 6,950 lines of JavaScript including the 527-line shared QR encoder, ≈ 820 of CSS, 118-icon sprite) · 12 routed modules (bench, 11-step diagnostic wizard, certificates, verify, valuation, repair estimator, repair shop with 5 tabs, device passport, buyer safety with 5 sections, inbox and log, settings, about) plus the full-screen test layer, search palette, tour, shortcuts sheet and print layouts · 41 weighted checks · 985 bilingual strings · seed of 9 signed certificates from 3 shops (1 expired, 1 revoked), 4 trusted shop keys, 12 job cards, 10 customers, 16 parts, 3 invoices, 27 phone models in the price/spec table, 26 TAC codes, 10 repair-part kinds × 5 tiers, a half-finished demo session, 7 notifications and 12 log events · 8 student TODOs.

## Getting started

- Open `index.html` in Chrome, Edge, Safari or Firefox. It works from `file://` and offline; only Google Fonts (Rethink Sans, Azeret Mono, Noto Nastaliq Urdu) come from the network and fall back to system fonts.
- **Run it on the phone you are testing.** Camera, microphone, motion sensors and WebCrypto signing need a secure page: host the folder on HTTPS (GitHub Pages, Netlify) or use `localhost`. Opening it as `http://192.168.x.x` on a phone is *not* secure — the app says so in a banner and those steps fall back to manual checks.
- Browser support differs and the UI states it per step: the Vibration API is missing on iPhone Safari; Battery Status only exists in Chromium browsers; `BarcodeDetector` (QR scanning) is in Chrome for Android but not iPhone Safari or Firefox — paste the bundle instead; iOS asks for motion permission when you press **Enable motion sensors**; the torch control exists only where the camera track exposes it. Keep the screen on while testing.
- First open seeds a fictional Lahore shop ("Bench 7 Mobile Lab") with a Galaxy A35 halfway through its checks, and starts an 8-step tour (replay from Settings, About or the More sheet). Press **New session** to test the phone in your hand.
- Where data lives: `localStorage` under the `mrmt.` prefix — `settings`, `session`, `certs`, `received`, `trust`, `shopKey`, `revoked`, `jobs`, `customers`, `parts`, `invoices`, `templates`, `buyer`, `inbox`, `log`, `meta`, `lang` (and `co2Repairs` for the CO2e slider).
- Reset: Settings → Your data → **Demo data** off (keeps your own records), **Export / Import JSON**, or **Reset to demo** (type RESET). The backup contains the shop's private signing key — keep it safe.

## The problem

Used and repaired phones are a large and growing market in Pakistan. Rising prices of new devices are pushing people back to repair, refurbishment and second-hand trading ([Express Tribune: rising costs revive the repair economy](https://tribune.com.pk/story/2627656/rising-costs-revive-repair-economy)), while the right-to-repair debate argues that phones should be repairable, with parts and software support available, to keep e-waste down ([Minute Mirror: the right to repair](https://minutemirror.com.pk/the-right-to-repair-pakistan-must-design-e-waste-out-of-our-future-614644/)). In that market a buyer usually has only the seller's word: copy screens sold as original, clone "flagships", patched "PTA approved" phones, account-locked or stolen handsets. Shops that do test phones hand over a paper slip that anyone can edit. Marammat turns the test into a repeatable, evidence-backed checklist that runs on the phone itself, and the result into a signed document that a buyer can check before paying. IMEI and PTA status still have to be verified on the official PTA DIRBS service ([dirbs.pta.gov.pk](https://dirbs.pta.gov.pk/) — verify the current method on pta.gov.pk); the app records what the tester saw and says plainly that a browser cannot query it.

## What is built

| Area | Features |
|---|---|
| Bench (home) | Device-under-test card with a progress ring, provisional grade and "next step"; quick actions; KPIs (certificates in 30 days, average score, open jobs, low-stock parts); live capability matrix of the 20 APIs this browser has (plus HTTPS); "what a browser cannot read" card; recent certificates; grades issued bar chart; jobs needing action |
| Diagnostic wizard | 11 steps, 41 weighted checks, each Pass / Minor / Fail / Skip with evidence and a mode tag (auto, manual, simulated); progress ring and step rail (horizontal chips on phones); API availability chips per step; results list redraws without stopping live streams; new session; QC sessions linked to a job card |
| 1 · Identify | Claimed brand, model, storage, RAM, colour, OS, customer; reference row for the model; what the browser reports (UA-CH model code, platform, CSS and panel pixels, pixel ratio, threads, device memory, touch points, colour gamut / HDR, secure context); automatic model-code match |
| 2 · Display | Nine full-screen patterns via the Fullscreen API (black, white, red, green, blue, 50 % grey, grey ramp, RGB ramps, 1-px checkerboard) with tap / Space / arrows and a fading HUD; per-pattern guidance; refresh rate measured from `requestAnimationFrame` intervals and compared with the model |
| 3 · Touch | Full-screen grid sized to the screen, coalesced pointer events with interpolation, edge cells outlined, live coverage HUD, input-to-next-frame latency; multi-touch pad drawing every finger; automatic skip without a touchscreen |
| 4 · Audio | Left / right tones through `StereoPannerNode` (ChannelMerger fallback) and a 100 Hz–12 kHz sweep with yes/no prompts; microphone level meter (RMS and peak dBFS with peak hold) and a log-frequency spectrum; voice detection auto-pass; 4-second record and playback with `MediaRecorder`; automatic loopback (1.0 / 2.75 / 4.1 kHz tones, Goertzel detection against a quiet window) that passes loopback and microphone; simulated path through an internal mixer; DSP self-test |
| 5 · Cameras | Permission, `enumerateDevices`, per-camera preview, rear / front from `facingMode` or label, capture at 480 px → variance of the Laplacian, mean brightness, clipped shadows / highlights, 32-bin histogram, colour cast; torch toggle via `applyConstraints` where supported; analyse a photo instead; sharp and blurred demo charts |
| 6 · Sensors | `DeviceMotionEvent.requestPermission` on iOS; accelerometer (|g| at rest), gyroscope (peak rotation), live x/y/z trace; tilt-the-ball game into four corners from `deviceorientation`; compass from `deviceorientationabsolute` / `webkitCompassHeading` with 12-sector coverage; pointer / keyboard simulator that never records a pass |
| 7 · Hardware | Vibration with a felt-it prompt; Battery Status level, charging, times; charge-state change test for the charging port; battery health and cycle count typed in from the phone with the source (stated plainly that browsers cannot read them); GPS time-to-fix and accuracy (position never stored); Network Information; manual SIM / Wi-Fi / Bluetooth / data ticks; storage quota and a rough disk size with a fake-capacity warning; device memory and threads |
| 8 · Performance | CPU benchmark in Web Workers from a Blob URL (FNV hashing, typed-array sort, matrix multiply), best of three single-thread runs then all threads in parallel, main-thread fallback; WebGL fill-rate benchmark with a heavy fragment shader and `readPixels` sync, unmasked renderer; spec table against the claimed model (threads, RAM, panel, refresh, GPU family, CPU and GPU scores, model code) with a clone verdict |
| 9 · Physical | Buttons, ports, SIM tray, lens glass, water-damage indicator and biometrics (OK / Issue / N/A); body grade A–D; screw check hint; up to six photos resized to ≈ 420 px JPEG data URLs with a SHA-256 evidence hash each |
| 10 · IMEI and PTA | Dial `*#06#` link; IMEI 1 and 2 with live Luhn analysis (expected check digit), TAC and reporting body, TAC brand vs claimed brand from an illustrative table, dual-SIM pairing note; box label match; account / activation-lock removal; PTA status as a manual field with the official method labelled "verify" and an `sms:8484` link |
| 11 · Grade and certificate | Seal with grade and score, counts, reasons; value range with a factor table and waterfall chart; box / charger / cable / warranty; unrun checks; suggested repairs → repair estimator; notes; issue a signed certificate |
| Certificates | List with filters (issued here, received, valid, expiring, expired, revoked) and KPIs; formal document (guilloche paper, copper seal, bilingual title, metadata grid, test table, QR, share code, value, signature, fine print); live verification banner; print A4 (one page), copy share bundle, download JSON, download QR PNG, WhatsApp summary, revoke (VOID stamp) |
| Verify | Paste, scan (BarcodeDetector camera loop), file (JSON or QR photo) or pick from records; accepts bundles, raw QR text and share codes; verdict banner (valid, expired, QR-only, tampered, forged, untrusted, revoked, invalid) with six checks; 10 section chips from the QR digests; field-level diff against the shop's own record; tamper lab to edit any of 26 fields and re-verify; save received certificates; trusted directory of shop keys (add key card, revoke / trust, delete, this shop's key card with QR) |
| Valuation | Model, storage, grade, PTA, battery slider, box, charger, warranty → range and factor breakdown; grade × PTA grouped bar chart; searchable price table of 27 models (illustrative PKR); negotiation notes; import from the bench session |
| Repair estimator | 10 part kinds × original / copy for the model's tier; parts + labour ranges, bench hours; value as-is vs after repair; verdict (repair pays / repair if keeping / replace); CO2e avoided; original-vs-copy notes; create a job card from the estimate |
| Repair shop | Kanban of job cards (intake → diagnosed → waiting for parts → repaired → QC re-test → delivered) with ageing chips; job detail with pipeline, parts from stock (reserves and returns stock, low-stock alerts), timeline, WhatsApp status message, printable job card, QC re-test that opens a linked diagnostic session, invoice; customers; parts inventory with margin, restock and add; invoices with print; bilingual WhatsApp templates per status |
| Device passport | Certificates and repairs grouped by PBKDF2 IMEI hash; score sparkline; lookup by IMEI; history timeline; export passport JSON; import verifies every certificate against trusted keys |
| Buyer safety | 12-item buyer checklist with progress; 7 scam patterns with warning signs and defences; data-wipe steps for Android, iPhone and the seller; battery safety; e-waste drop-off ideas (illustrative) and a CO2e calculator |
| Platform | Settings (language, bench / workshop theme, three text sizes, reduce motion, shop profile, certificate validity, signing key rotation, permission states, demo data, storage used, JSON export / import / reset); notifications inbox and activity log (CSV, print); search palette over pages, steps, actions, certificates, jobs, customers, parts and models; spotlight tour; keyboard shortcuts; skip link, labels, focus styles, `aria-live` results, keyboard-closable dialogs |

## Module map

| Module | Key functions | Storage keys |
|---|---|---|
| State and seed | `loadState`, `persist`, `newSession`, `setDemo`, `seedApp`, `demoSeed`, `seedKeys`, `seedCertificates`, `demoSession`, `makeImei` | all `mrmt.*`, `mrmt.meta` |
| Crypto | `generateShopKey`, `kidOf`, `signText`, `verifyText`, `canon`, `docHash`, `groupDigests`, `buildManifest`, `signCertificate`, `parsePayload`, `verifyCertificate`, `verdictOf`, `bundleOf`, `parseBundle`, `keyCardOf`, `parseKeyCard`, `imeiHash`, `shareCode` | `mrmt.shopKey`, `mrmt.trust` |
| Signal maths | `goertzelPower`, `toneSnrDb`, `loopbackVerdict`, `synthTone`, `rmsDb`, `peakDb`, `toGray`, `laplacianVariance`, `exposureStats`, `refreshFromIntervals`, `luhnCheckDigit`, `luhnValid`, `imeiInfo`, `imeiPairNote` | — |
| Benchmarks | `WORKER_SRC`, `runCpuBench`, `benchScore`, `runGpuBench`, `glProgram`, `compareSpecs`, `specVerdict`, `gpuFamily` | `mrmt.session.readings` |
| Grading and value | `STEPS`, `CHECKS`, `stepStatus`, `sessionProgress`, `computeGrade`, `gradeReasons`, `valueEstimate`, `repairEstimate`, `repairVerdict` | — |
| Wizard shell | `renderWizard`, `wizSide`, `checkRows`, `setCheck`, `setReading`, `refreshWizardChrome`, `startNewSession`, `FEATURES`, `apiAvailable`, `LIVE` | `mrmt.session` |
| Steps | `collectBrowserInfo`, `evaluateUaMatch` · `runDisplayPatterns`, `measureRefresh`, `openFs`, `closeFs` · `runTouchGrid`, `bindTouchPointers`, `finishTouch`, `bindMultiPad` · `playTone`, `startMic`, `drawSpectrum`, `recordMic`, `runLoopback`, `dspSelfTest` · `listCameras`, `openCamera`, `analyseFrame`, `toggleTorch`, `demoChart` · `startSensors`, `sensorFrame`, `evaluateSensors` · `runVibration`, `showBattery`, `watchCharger`, `saveHealth`, `runGps`, `showStorage` · `runCpu`, `runGpu`, `evaluatePerf` · `evaluatePhysical`, `addPhoto` · `evaluateImeiChecks` · `buildCertDoc`, `issueCertificate`, `ensureShopKey`, `sealSvg` | `mrmt.session` |
| Certificates | `renderCerts`, `certDocHtml`, `certState`, `certQr`, `renderCertDoc`, `bindCertDoc`, `revokeCert` | `mrmt.certs`, `mrmt.received`, `mrmt.revoked` |
| Verify | `parseVerifyInput`, `runVerify`, `verdictBanner`, `groupsHtml`, `diffHtml`, `drawTamperLab`, `startScan`, `verifyFile`, `tamperedSample`, `trustDirHtml`, `addKeyCard` | `mrmt.trust`, `mrmt.received` |
| Valuation | `renderValue`, `gradeChartSvg`, `priceTableHtml`, `valueFromSession`, `valueBreakdownHtml`, `waterfallSvg` | — |
| Repair estimator | `renderRepair`, `repairValues`, `repairResultHtml` | — |
| Repair shop | `renderShop`, `renderJobs`, `openJob`, `moveJob`, `addPartToJob`, `startQc`, `waJobLink`, `openJobForm`, `saveJobForm`, `renderCustomers`, `renderParts`, `restockPart`, `invoiceFromJob`, `createInvoice`, `showInvoice`, `renderTemplates` | `mrmt.jobs`, `mrmt.customers`, `mrmt.parts`, `mrmt.invoices`, `mrmt.templates` |
| Passport | `passports`, `renderPassport`, `findPassport`, `openPassport`, `exportPassport`, `importPassport` | `mrmt.received` |
| Buyer safety | `renderSafety`, `safetyChecklist`, `safetyScams`, `safetyWipe`, `safetyBattery`, `safetyGreen` | `mrmt.buyer`, `mrmt.co2Repairs` |
| Inbox, settings, platform | `notify`, `logEvent`, `refreshNotices`, `renderInbox`, `renderSettings`, `rotateKey`, `exportData`, `importData`, `resetAll`, `openPalette`, `startTour`, `openShortcuts`, `onKey` | `mrmt.inbox`, `mrmt.log`, `mrmt.settings`, `mrmt.lang` |
| Router and shell | `ROUTES`, `renderNav`, `route`, `renderRoute`, `applyPrefs`, `setLang`, `toggleTheme`, `boot` | URL hash |

## Architecture (single file)

```
index.html
├─ <head>        title, description, favicon, fonts (Rethink Sans, Azeret Mono, Noto Nastaliq Urdu)
├─ <style>       tokens (graphite / steel, electric cyan, solder copper, pass green, fail red; workshop light theme),
│                motion kit, shell (rail, top bar, phone tabs), buttons, fields, segmented controls, cards, tables,
│                sheets, palette, tour, wizard + instrument stage, full-screen test layer, certificate paper,
│                verify, kanban, passport, safety, print (A4 certificate, invoices, job cards, log)
├─ <svg>         118-symbol sprite generated from the icons the code uses
├─ shell         rail, top bar (search, inbox, theme, language), banner, view, tabs, full-screen layer,
│                modal sheet, palette, tour, toasts, live region, print area
└─ <script>
   ├─ utils          store (mrmt. prefix), esc, dates, PKR, toast, motion helpers, modal, print, files,
   │                 resizeImage, LIVE registry for streams and sensors, AudioContext
   ├─ i18n           STR [English, Urdu] + addStr() blocks per module, t(), L()
   ├─ reference data MODELS (27), TAC_TABLE, RBI, PARTS (10 × 5 tiers), CHECK_TO_PART, CO2, EWASTE
   ├─ QR encoder     shared kit encoder (pasted unchanged)
   ├─ state + seed   settings, sessions, notify/log, demo toggle, keys, signed certificates, jobs, parts
   ├─ crypto         ECDSA P-256, SHA-256, canonical JSON, manifest + digests, bundles, key cards, PBKDF2
   ├─ signal maths   Goertzel, SNR, loopback verdict, Laplacian variance, exposure, refresh, Luhn, IMEI
   ├─ benchmarks     Web Worker CPU, WebGL, spec comparison
   ├─ grading        steps, weighted checks, grade caps, valuation, repair estimate and verdict
   ├─ views          bench · wizard (identify, display, touch, audio, camera, sensors, hardware,
   │                 performance, physical, IMEI, summary) · certificates · verify · valuation ·
   │                 repair · shop (jobs, customers, parts, invoices, templates) · passport · safety ·
   │                 inbox · settings · search + tour + shortcuts · about
   └─ router + boot  ROUTES, nav, banner, prefs, theme, language, seed on first run, notices
```

## Domain logic and algorithm notes

- **Goertzel loopback.** For a block of N mic samples (Hann-windowed), with ω = 2πf/fs and c = 2cos ω: sₙ = xₙ + c·sₙ₋₁ − sₙ₋₂, power P(f) = (s₁² + s₂² − c·s₁·s₂)/N². The detector scores SNR = 10·log₁₀(P(f) / mean P(f ± 380, f ± 500 Hz)). Each tone (1.0, 2.75, 4.1 kHz) gets a 350 ms quiet window and a 650 ms tone window sampled every 30 ms; a tone is *heard* when ≥ 3 frames during the tone reach 12 dB and fewer than max(2, ⅓) quiet frames do. Two of three heard → loopback pass (and microphone pass); one → minor. Echo cancellation, noise suppression and AGC are switched off in `getUserMedia` so the tone is not removed. The simulated path mixes the same oscillators and white noise into an analyser to prove the detector without hardware.
- **Sharpness.** Frames are scaled to 480 px wide, converted to luma (0.299R + 0.587G + 0.114B) and filtered with the 4-neighbour Laplacian L = g(x±1) + g(y±1) − 4g; the variance of L is high for crisp edges and drops with blur, smudge or a failed focus motor. Pass ≥ 120, minor 50–120, fail < 50 (tuned for printed text at 20–30 cm). Exposure: mean luma, % below 12 and above 243, 32-bin histogram, per-channel cast.
- **Refresh and touch.** Refresh = 1000 / median(rAF interval), snapped to 30/48/60/75/90/100/120/144/165/240 Hz when within 8 %. Touch coverage = visited cells / all cells, with pointer paths interpolated every 12 px (and coalesced events) so fast swipes do not skip; latency = median(next frame time − event time).
- **IMEI.** Luhn: from the right, double every second digit of the 14-digit body (subtract 9 if > 9); check digit = (10 − sum mod 10) mod 10. TAC = first 8 digits, reporting body = first 2. Passport key = PBKDF2-SHA-256(IMEI, salt "marammat-passport-v1", 60,000 rounds, 128 bits). Certificates keep only `35xxxxxxxxx1234` and that hash.
- **Benchmarks.** Score per task = 1000 × base_ms / measured_ms; CPU score = geometric mean of hash, sort and matrix tasks; multi-thread score = sum over `hardwareConcurrency` (≤ 8) parallel workers. GPU score ≈ 1000 per 600 Mpix/s of a 48-iteration fragment shader. Ratios to the model's reference: ≥ 0.6 match, 0.35–0.6 check, < 0.35 mismatch. The spec verdict fails on two or more strong mismatches (GPU family, cores, panel resolution, CPU score, model code) → "possible clone", which collapses the valuation.
- **Grade.** score = Σ wᵢ·sᵢ / Σ wᵢ over run checks (pass 1, minor 0.5, fail 0); A ≥ 88, B ≥ 74, C ≥ 55, else D. Caps: dead pixels, touch coverage or charging failure → at best C; Luhn, PTA blocked, account lock or spec mismatch failure → D; body grade D → at best C; more than 30 % of the weight unverified → at best B. Every cap and failure becomes a reason.
- **Value.** mid = base(model, grade B, PTA, base storage) × grade (A 1.08, B 1, C 0.84, D 0.58) × (1 + 0.07 per storage step) × non-PTA factor (0.58–0.75 by model) or 0.3 if blocked × battery (≥ 90 1, ≥ 85 0.97, ≥ 80 0.93, else 0.86) × box 1.03 × charger 1.02 × warranty 1.04 × clone 0.18; range = mid ± 6 % (± 10 % when PTA is unchecked), rounded to Rs 500.
- **Repair verdict.** Cost = Σ (part range + labour) for the model's tier. Value as-is assumes grade D (screen / port faults) or C; after repair grade B, −8 % for a copy screen. Repair if value gained ≥ mid cost; "repair if you keep it" if cost ≤ 45 % of the repaired value; otherwise replace or sell for parts. CO2e avoided ≈ 55 kg (new phone) − parts − 0.4 kg transport (illustrative factors).
- **Signed certificate.** The document is canonical JSON (sorted keys). The QR payload is `MRMT1|serial|kid|docHash|digests|signature`, where `docHash` = base64url SHA-256 of the document, `digests` = 10 × 2-byte SHA-256 prefixes of the sections (id, dates, issuer, device, IMEI, tests, battery, grade, value, notes), `kid` = first 8 bytes of SHA-256 of the raw P-256 public key, and the ECDSA P-256 / SHA-256 signature covers everything before it. Verification: parse → find `kid` in the trusted directory → verify the signature over the manifest (proves the QR is genuine) → recompute the document hash and digests (proves the document matches and names the edited sections) → expiry and revocation. A forged QR fails the signature; an edited document fails the hash and lights the section; a self-signed impostor key is "unknown issuer". Share bundles are `MRMT1B.` + base64url(JSON {doc, qr}); share codes are 40 bits of the hash in Crockford base32.

## Left for students

| Extension | Difficulty | Start here |
|---|---|---|
| Keep the shop signing key non-extractable (IndexedDB) or behind a passkey / hardware token | Hard | `generateShopKey()` |
| Replace the public passport salt with a per-shop HMAC secret or server-side pepper | Medium | `imeiHash()` |
| Publish a signed revocation list that other devices fetch before verifying | Hard | `revokeCert()` |
| Crowd-source reference benchmark scores from known-good phones (medians and spreads) | Medium | `compareSpecs()` |
| Web NFC tap test and Web Bluetooth scan on Chrome for Android | Medium | `networkHtml()` |
| Camera-assisted stuck-pixel and burn-in detection with a second phone | Hard | `runDisplayPatterns()` |
| Live market prices from marketplace listings with a last-updated stamp | Medium | `valueEstimate()` |
| Sync job cards across counters and send status updates via an SMS / WhatsApp Business API | Medium | `waJobLink()` |

Each stub is a `// TODO(student):` comment in the named function; the About page lists the same eight with a hint.

## Data and safety notes

- **Not a certified lab.** Results depend on the tester, the browser and the room. A signature proves who issued a certificate and that it was not edited — not that the phone is still in that condition. Certificates expire after 30 days by default.
- **What a browser cannot read.** Battery health, cycle count, the IMEI and PTA status are typed in by the tester from the phone's settings, the box and the official PTA service, and every screen says so. Storage size (from the quota), device memory (rounded, capped at 8 GB) and the model code (only Chrome on Android) are hints. The app never contacts PTA, GSMA or any server.
- **Illustrative data.** Prices (PKR), the TAC table, reporting bodies, parts costs, labour, reference benchmark scores, CO2e factors, e-waste points and the shop policy text are illustrative for teaching — verify them before real use. The shop, customers, testers, placeholder phone numbers (`0300-0000xxx`) and IMEIs are fictional; seeded IMEIs are generated with valid Luhn digits from sample TACs.
- **Privacy.** Everything stays in `localStorage` on this device. Full IMEIs live only in the open session; certificates and job cards keep a masked IMEI and a slow salted hash (pseudonymous, not anonymous — IMEIs are guessable per TAC). GPS records only time-to-fix and accuracy. Photos are shrunk to ≈ 420 px and only their hashes enter certificates.
- **Keys.** The private signing key is stored as a JWK in this browser and in JSON backups; anyone with the device profile or the backup can sign as the shop. Rotating the key keeps the old public key trusted so earlier certificates still verify.
- **Battery safety.** The guide covers swollen batteries, sand trays at the bench and taping terminals; never puncture or bend a lithium cell.

## Verification

- **Harness** (`check.mjs`, 1366×850 and 390×844): 30+ states — bench (dark, light, Urdu), the tour, every wizard step (touch grid in full screen, simulated loopback results, camera demo chart, sensor simulator, hardware, performance, physical, IMEI in Urdu, summary in Urdu), certificates list, certificate document (English and Urdu), verify with a tampered sample, valuation, repair estimator, shop kanban, job sheet, parts, passport, safety, inbox, settings (light), about — with no console, page or request errors and no horizontal overflow. Print media renders the certificate and an invoice on one A4 page each.
- **Behaviour script** (`31-marammat-tests/behaviour.mjs`, Playwright with a fake camera and microphone, 218 checks, all passing): seed counts and namespaced storage; the tour; every seeded certificate verifies (valid / expired / revoked); the certificate QR decodes back to the signed payload with jsQR; **verify passes on the original and fails as "tampered" after editing each of 26 fields, with the right section flagged**; altered signature or manifest → forged; unknown key and impostor key → not valid; bundle and share-code round trips; canonical JSON; Verify screen banners, tampered sample, field diff, tamper lab edit and reset; key cards; revocation; Luhn, TAC, RBI, IMEI hash and masking; grade caps and valuation monotonicity; Goertzel and loopback detector; identify; nine full-screen patterns and refresh measurement; touch-grid coverage via real mouse drags; simulated and real (fake-mic) loopback, speaker prompt; Laplacian sharp vs blurred and a fake-camera capture; sensors via synthetic DeviceMotion / DeviceOrientation events (accel, gyro, tilt into four corners, compass sectors); GPS without storing the position; charge-state change; battery health thresholds; radios; CPU benchmark in Web Workers from `file://`; WebGL benchmark; spec verdicts; physical checklist; IMEI step; issuing a certificate that verifies and never contains the full IMEI; job card create → parts → repaired → invoice → QC re-test; WhatsApp link; passport grouping, lookup, export / import with verification; repair estimate arithmetic; no raw i18n keys on 32 routes in both languages; every string has Urdu; theme; search; shortcuts; export; demo toggle; no overflow at 390 px on 32 routes in both languages.

## Evaluation ideas for the FYP report

- **Agreement with a technician.** Test 30 used phones with Marammat and with an experienced repair technician; report per-check agreement (Cohen's κ) and where the app missed or over-flagged faults.
- **Loopback accuracy.** Run the automatic loopback on 20 phones in quiet and noisy rooms (measure dB SPL); plot detection rate against background noise and speaker volume; test phones with a known blocked grille.
- **Sharpness threshold.** Photograph a printed chart with good and deliberately smudged / defocused lenses; build an ROC curve for the variance-of-Laplacian threshold and compare 480 px against 960 px analysis.
- **Clone detection.** Collect benchmark and spec readings from genuine phones and from known clones or mislabelled models; report how often the spec verdict catches them and the false-alarm rate on throttled genuine phones.
- **Tamper study.** Ask participants to alter a printed certificate convincingly; measure how many forgeries Verify rejects (expected: all edits to the document, all altered QRs) and how long verification takes on a low-end Android phone.
- **Usability.** Time to complete a full session for shop staff vs first-time buyers, in Urdu and English (SUS questionnaire, task completion), and which steps people skip.
