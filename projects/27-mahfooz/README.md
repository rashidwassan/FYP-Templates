# 27 · Mahfooz — Women's Safety Companion (idea #50)

**A calm, private safety companion for women in Pakistan, in Urdu and English.** One large press-and-hold SOS control is always on screen; it opens a cancel countdown, finds your location and prepares SMS and WhatsApp alerts for your trusted circle, with a siren, a screen strobe and helpline buttons a tap away. Around it: a trusted circle with check-ins and a code word, safe journeys with an expected arrival time and an escalation ladder, a fake incoming call, an incident journal encrypted with a PIN (WebCrypto PBKDF2 + AES-GCM) that drafts police, workplace-committee and cyber-harassment complaints, a personal safety map, know-your-rights guides with a quiz, and a discreet mode with a working calculator disguise, quick exit and clear traces. Everything stays on the device. محفوظ اردو اور انگریزی دونوں میں ہے، اردو میں مکمل دائیں سے بائیں ترتیب کے ساتھ۔

**Scale:** 7,307 lines in one `index.html` (≈ 6,290 lines of JavaScript, ≈ 840 of CSS) · 13 routed modules plus the SOS layer, fake-call screen, calculator disguise, neutral page, search palette, tour, shortcuts sheet and print layouts · 991 bilingual strings · 6 bilingual rights guides and a 12-question quiz · 109 sprite icons · seed of 5 demo contacts, 14 map spots, 6 past journeys with 99 breadcrumbs, 3 safe places, 2 practice SOS runs, 2 check-ins, 6 encrypted journal entries (2 with evidence images), 6 notifications and 12 activity-log events · 8 student TODOs.

## Getting started

- Open `index.html` in Chrome, Edge, Firefox or Safari. It works from `file://` and offline; only Google Fonts (Albert Sans, Noto Nastaliq Urdu) come from the network and fall back to system fonts. Geolocation, notifications and the Wake Lock need a secure origin on some browsers: serve the folder (`npx serve .`) and open it on `localhost` or over HTTPS to test location on a phone.
- The app opens in English; the top-bar button (or `L`) switches everything, including alert messages, to Urdu with a right-to-left layout. A spotlight tour runs on the first open (replay it from Settings, About or the More sheet).
- **Demo journal PIN: `1357`.** The demo journal is encrypted at first start under that PIN. Use "Start my own journal" to replace it with an empty journal under your own PIN.
- **SOS:** press and hold the coral button for 3 seconds (on a keyboard: focus it with `S`, then hold Space or Enter). Try **Practice SOS** first: it is silent and sends nothing. Demo contacts have placeholder numbers and their send buttons are deliberately disabled; add a real contact to see the prepared `sms:` and `wa.me` links.
- **Safe journey:** "Simulate this journey" (on by default while demo data is on) uses made-up positions and a 10x clock (switchable to 1x or 60x) so you can watch the reminders, the overdue escalation and the breadcrumb log without travelling.
- **Discreet mode:** Settings → Discreet mode → Calculator disguise asks for a 4–12 digit PIN. Next time Mahfooz opens as a working calculator; type the PIN and press `=`. Quick exit: the Exit button in the top bar, or `Esc` twice. To return from the recipe page, hold its heading for 2 seconds or tap it five times.
- **Where data lives:** `localStorage` under the `mhf:` prefix — `settings`, `profile`, `contacts`, `plan`, `journeys`, `active`, `sosLog`, `spots`, `inbox`, `log`, `quiz`, `checkins`, `meta`, `lang`, `pinGuard` (wrong-PIN counter) and `vault` (the encrypted journal record).
- **Backup and reset:** Settings → Your data → Export JSON (the journal inside stays encrypted; contacts, journeys and places are readable), Import, "Demo data" off for an empty app (the journal is separate), and "Reset to demo". Clear traces (Settings or the More sheet) can also erase everything.

## The problem

Women in Pakistan meet harassment on the street, in rickshaws and ride-hailing cars, at work and online. In the moment, help depends on reaching the right person fast with a location; afterwards, a complaint to the police, an inquiry committee or the cybercrime authority depends on a clear, dated record that many people never manage to keep. Existing apps are often English-only, ask for accounts, send data to servers, or promise "automatic" alerts that a phone browser cannot deliver. Mahfooz keeps the essentials on the phone, in Urdu and English, and is honest about what a web page can and cannot do.

## What is built

| Area | Features |
|---|---|
| SOS | One large press-and-hold control, always reachable (raised in the phone tab bar, a pad at the foot of the desktop sidebar); a ring fills over 3 s and letting go early only shows a hint; keyboard hold with Space/Enter; cancel countdown (0/3/5/10 s, ticks or silent, vibration); location from the Geolocation API only when SOS starts (with accuracy, time, battery level from the Battery API, and a foreground `watchPosition` refresh while active); editable alert text from a bilingual template or your own with `{name} {where} {map} {time} {battery}`; per-contact SMS, WhatsApp and call buttons plus one "SMS all" link; WebAudio siren (sawtooth swept by a triangle LFO through a compressor) and a screen strobe kept under 3 flashes per second; `tel:` helplines (15, 1122, 1043, 1099, 1098, 0800-39393, 1991, 115, 130) labelled "verify locally"; minimise to a banner; Wake Lock; end flow that offers "I'm safe" messages to the people you alerted and a journal entry; practice mode; SOS centre page with history, what-it-cannot-do list and two-second siren/strobe tests |
| Trusted circle | Contacts with relation, priority order (move up), WhatsApp flag, include-in-SOS and share-journeys flags, notes, "tested" age; Pakistani mobile or international number validation; test message that marks a contact tested; demo contacts can never be messaged; "I'm safe" and "call me in N minutes" check-ins with history; code word sharing |
| Safety plan | Profile (name used in alerts, city, blood group, allergies, medicines, conditions), code word and its meaning, safe places, emergency notes, a credit-card-size wallet card printed in English and Urdu (the code word is never printed) |
| Safe journey | Destination, origin, mode (walking, rickshaw, ride-hailing, bus/metro, car), vehicle number, driver name and phone, trip ID, duration chips with a live ETA, contacts to share with; "share your trip" message; live card with a countdown ring and the escalation ladder; reminder at ETA, louder reminder after the grace period, prepared alert (with last known position) after twice the grace period, never sent automatically; +10/+30 min extensions; optional mid-journey "are you OK?" check-ins with a missed-check-in reminder; system notifications when the tab is hidden (if allowed); breadcrumb log while the page is open (real `watchPosition` fixes, or a simulated path in demo mode) drawn as an SVG trail with a scale bar; history with status, distance, timeline, GPX export and a printable journey report |
| Fake call | Caller presets (Ammi, Abbu, Bhai, Office, a friend) or a custom name; ring now or after 10 s / 30 s / 1 min / 5 min; three synthesised ringtones with preview; vibration; full-screen incoming call with accept/decline, then an in-call screen with a timer, scripted captions in Urdu or English and optional speech synthesis; banner with cancel while scheduled |
| Incident journal | PIN lock screen with a pad; PBKDF2-SHA-256 (310,000 rounds, random salt) → AES-GCM-256; entries with date and time, category, place, what happened, people involved, witnesses, evidence notes, up to 4 photos resized to JPEG data URLs on the device, where it was reported and the reference, severity and impact; filters, search, KPIs; the actual stored ciphertext shown on screen; wrong-PIN counter with a doubling wait after 5 tries; auto-lock after inactivity and when the page is hidden; change PIN (re-encrypts with a new salt); encrypted backup; forgot-PIN erase; start-your-own-journal replacing the demo |
| Complaint report | Police (FIR application to the SHO), workplace inquiry committee (under the 2010 Act, as amended) or cyber harassment; English or Urdu; complainant details, people complained against, relief sought (prefilled per type), chosen incidents as numbered paragraphs and a table, evidence list and a photo annex, declaration and signature block; print, copy text or download a standalone `.html`; clearly marked as an unencrypted draft and general information |
| Safety map | Spots marked unsafe, well lit, women-friendly or help point, with "when" (any time, daytime, after dark) and notes; offline schematic SVG (local projection, 500 m grid, distance rings, pan by drag, zoom by buttons or wheel, tap to add); type filters; list sorted by distance from your location or the home reference; nearest help point with directions; Leaflet 1.9.4 street map lazy-loaded with SRI only when you ask, falling back to the schematic when offline |
| Know your rights | Six bilingual guides: workplace harassment and inquiry committees, online and cyber harassment, public transport and ride safety, domestic violence support, reporting to the police (FIR), street harassment and stalking; "general information, not legal advice" on every page; where-to-get-help helplines per guide; printable guides |
| Rights quiz | 12 bilingual questions with explanations and links to the relevant guide, progress bar, result ring, review of missed answers, attempt history |
| Discreet mode | Working calculator disguise (precedence, percent, sign, delete, division by zero) that unlocks only with a PIN stored as a salted PBKDF2 hash; changes the tab title and icon; quick exit (button or double `Esc`) to a neutral recipe page, the calculator or a chosen website, which also locks the journal and strips the URL hash; clear traces (activity log and notifications, journeys and breadcrumbs, SOS history, check-ins, or everything) with honest notes about what a page cannot erase |
| Platform | Settings (language, light/night theme, three text sizes, reduce motion, vibration, SOS behaviour, location and simulation mode, grace period and check-in interval, notification permission, journal auto-lock, discreet mode, demo data, storage used, JSON export/import/reset); safety-readiness checklist on Home; notifications inbox and an activity log grouped by day (exportable, printable, never containing journal text); search palette over pages, actions, contacts, places, guide sections, journeys and (only while unlocked) journal entries; 8-step spotlight tour; keyboard shortcuts sheet; print layouts; skip link, labels, focus styles, `aria-live` announcements |

## Module map

| Module | Key functions | Storage keys |
|---|---|---|
| State and seed | `loadState`, `persist`, `seedApp`, `emptyApp`, `demoSeed`, `setDemo`, `simRoute` | all `mhf:*` keys, `mhf:meta` |
| SOS | `sosButtonHtml`, `bindSosButton`, `holdStart`/`holdEnd`, `startSos`, `sosCountTick`, `activateSos`, `fetchSosLocation`, `refreshSosMessage`, `markSosSent`, `endSos`, `renderSosPage` | `mhf:sosLog` |
| Messages | `sosMessage`, `journeyStartMessage`, `journeyOverdueMessage`, `smsHref`, `waHref`, `telHref`, `contactSendButtons`, `smsAllButton`, `sendAlertViaServer` (stub) | — |
| Audio and strobe | `siren`, `ringer`, `RINGTONES`, `chime`, `tick`, `strobe`, `stopAllSound` | — |
| Location | `getPosition`, `watchPosition`, `refreshGeoPermission`, `simPosition`, `mapLink`, `batteryLevel`, `keepAwake`, `haversine` | — |
| Trusted circle | `renderCircle`, `openContactForm`, `saveContactForm`, `moveContactUp`, `openTestMessage`, `openCheckin`, `openSendSheet` | `mhf:contacts`, `mhf:checkins` |
| Safety plan | `renderPlan`, `saveProfile`, `saveCodeWord`, `walletFront`, `walletBack`, `printWalletCard` | `mhf:profile`, `mhf:plan` |
| Safe journey | `startJourney`, `journeyTick`, `journeyNow`, `journeyStage`, `escalate`, `openStageSheet`, `openPreparedAlert`, `checkinTick`, `extendJourney`, `arriveJourney`, `addSimCrumb`, `pushCrumb`, `crumbSvg`, `exportGpx`, `printJourney` | `mhf:active`, `mhf:journeys` |
| Fake call | `scheduleFakeCall`, `ringFakeCall`, `acceptFakeCall`, `speakLine`, `endFakeCall`, `renderFakeCall` | `mhf:settings` (call fields) |
| Journal crypto | `deriveKey`, `encryptJSON`, `decryptJSON`, `createJournal`, `unlockJournal`, `saveJournal`, `lockJournal`, `changePin`, `startOwnJournal`, `guardFail`/`guardWait` | `mhf:vault`, `mhf:pinGuard` |
| Journal UI | `renderJournalLock`, `tryUnlock`, `renderJournalList`, `openEntryView`, `openEntryEditor`, `addPhotos`, `resizeImage`, `saveEntry`, `openChangePin`, `forgotPin` | in the encrypted `vault` |
| Complaint report | `openReportBuilder`, `defaultReportIds`, `reportHtml`, `downloadReport` | — |
| Safety map | `mapProjection`, `schematicSvg`, `bindSchematic`, `zoomSchematic`, `selectSpot`, `openSpotForm`, `nearestHelpHtml`, `loadLeaflet`, `showStreetMap` | `mhf:spots` |
| Rights and quiz | `renderRights`, `renderGuide`, `renderQuiz`, `finishQuiz` | `mhf:quiz` |
| Discreet mode | `calcPress`, `calcValue`, `showDisguise`, `unlockDisguise`, `quickExit`, `showNeutral`, `openClearTraces`, `clearTraces`, `wipeEverything`, `setDisguisePin`, `checkDisguisePin` | `mhf:settings` (disguise hash) |
| Inbox and log | `notify`, `logEvent`, `refreshNotices`, `renderInbox` | `mhf:inbox`, `mhf:log` |
| Settings and data | `renderSettings`, `exportData`, `importData`, `resetAll` | `mhf:settings`, `mhf:lang` |
| Search, tour, keys | `openPalette`, `paletteIndex`, `startTour`, `showTourStep`, `openShortcuts`, `onKey`, `onEscape` | `mhf:settings.tourDone` |
| Router and shell | `route`, `renderRoute`, `renderNav`, `renderBanner`, `applyPrefs`, `setLang`, `mountSosControls`, `boot` | URL hash |

## Architecture (single file)

```
index.html
├─ <head>        title "… | FYP Template", fonts (Albert Sans, Noto Nastaliq Urdu), favicon
├─ <style>       tokens (deep plum, soft lavender, calm teal; coral only for SOS), night theme, motion kit,
│                shell (plum side rail, top bar, phone tab bar, floating SOS), buttons/fields/switches,
│                cards, sheets, palette, tour, SOS layer, strobe, call screen, calculator, neutral page,
│                journal, map, guides, quiz, print layouts, RTL rules
├─ <svg>         109-symbol icon sprite generated from the icons the code uses
├─ shell         side rail, top bar (search, notifications, language, Exit), banner, view, tab bar,
│                overlays (SOS, strobe, call, disguise, neutral, modal sheet, palette, tour, toasts, live region)
└─ <script>
   ├─ utils          store (mhf: prefix), esc, dates and durations, haversine, phone helpers, toast,
   │                 animateIn/settle/countUp/drawPath/fillRing, modal sheet, print, download, resizeImage
   ├─ i18n           STR [English, Urdu] in four groups (core, safety, private, platform), t(), tIn(), L()
   ├─ content        HELPLINES, RELATIONS, MODES, SPOT_TYPES, CATEGORIES, REPORTED, GUIDES (6), QUIZ (12),
   │                 CALLERS (scripts), RECIPES (neutral page)
   ├─ state + seed   DEFAULT_SETTINGS, loadState, persist, logEvent, notify, setDemo, demo data, mock evidence
   ├─ crypto         PBKDF2 → AES-GCM journal vault, PIN guard, disguise PIN hash
   ├─ audio          siren, ringtones, chimes, ticks, strobe
   ├─ geo            getPosition / watchPosition (explicit actions only), simulation, map links, battery, wake lock
   ├─ messages       SOS / journey / check-in / code-word texts, sms:, wa.me, tel: builders
   ├─ views          home · SOS layer + SOS centre · circle + check-ins · plan + wallet card · journey + log ·
   │                 fake call · journal (lock, list, editor, report) · map (schematic, spots, Leaflet) ·
   │                 rights + quiz · discreet (calculator, quick exit, neutral page, clear traces) ·
   │                 inbox + log · settings · search + tour + shortcuts · about
   └─ router + boot  ROUTES, nav, banner, applyPrefs, setLang, auto-lock, timers, boot()
```

## Domain logic and algorithm notes

- **Press and hold.** Pointer events with pointer capture (and Space/Enter on the keyboard) start a `requestAnimationFrame` loop that fills a ring over 3,000 ms; `pointerup`, `pointercancel` and lost capture reset it. Only a completed hold starts the countdown, so a pocket tap or a short press does nothing.
- **Alert links.** `sms:` uses comma-separated numbers (`sms:/open?addresses=…&body=…` on iOS); WhatsApp uses `https://wa.me/<923xxxxxxxxx>?text=…` with `0300-…` converted to the `92` country code. The page records only that a link was opened; it cannot know whether a message was sent, and says so.
- **Location.** One high-accuracy fix with a 15 s timeout when SOS starts (cached up to 30 s), then a foreground `watchPosition` while SOS is active. The Permissions API is queried at boot without prompting, so the readiness checklist can show the state. Simulation mode (Settings) substitutes a drifting position near the demo home, clearly labelled.
- **Escalation ladder.** With grace g (default 10 min): stage 0 before ETA, stage 1 at ETA, stage 2 at ETA + g, stage 3 at ETA + 2g. Each step chimes louder, writes a notification and a log line, and opens a sheet; stage 3 prepares the overdue message with the last breadcrumb for the shared contacts. Extending the ETA recomputes the stage. Simulated journeys run on a virtual clock `virtBase + (now − realBase) × speed`, re-based whenever the speed changes.
- **Breadcrumbs.** Real fixes are kept when the position moved more than 15 m or a minute passed (capped at 600). Simulated fixes are interpolated every 30 s of journey time along a seeded, gently curved path. Distance is the sum of haversine segments; the trail is drawn on a local equirectangular projection (longitude scaled by cos φ) with an auto-chosen scale bar.
- **Journal encryption.** `deriveKey(pin, salt, 310000)` imports the PIN as PBKDF2 material and derives a non-extractable AES-GCM-256 key. The record stores the salt, iteration count, an encrypted verifier and the encrypted data; every save re-encrypts the whole JSON (entries and photo data URLs) with a fresh 12-byte IV. A wrong PIN fails the authenticated verifier decryption. After five failures the guard imposes 30 s, doubling each further failure, enforced before any key derivation. Only ciphertext is in `localStorage` (verified by the behaviour test).
- **Disguise PIN.** Stored as a 256-bit PBKDF2 hash (100,000 rounds, random salt). The calculator evaluates the entry first and only then checks a bare 4–12 digit number against the hash, so a wrong PIN just behaves like arithmetic.
- **Calculator.** Tokens `{n}` / `{o}`; a two-pass evaluation applies × and ÷ before + and −, with division by zero shown as `Error` and results rounded to 12 significant digits. No `eval`.
- **Schematic map.** The projection fits all spots and the reference point with padding; pins keep a constant on-screen size by scaling with the viewBox; taps are converted back to coordinates with the inverse projection to add a spot exactly where tapped.
- **Readiness score.** Eight checks: two real SOS contacts, a test message in the last 30 days, own name, location permission (or simulation), own journal PIN, a code word, a practice SOS in 90 days and a backup in 30 days.

## Left for students

| Extension | Difficulty | Start here |
|---|---|---|
| Automatic SMS alerts through a server gateway with delivery logging (user confirms first) | Hard | `sendAlertViaServer()` |
| Background location tracking with Capacitor or an Android foreground service | Hard | `watchPosition()` |
| Server-side journey watchdog that escalates even if the phone dies | Hard | `escalate()` |
| Shake (DeviceMotion) or hardware-button trigger for the SOS countdown | Medium | `enableShakeTrigger()` |
| Encrypted audio evidence recorded with MediaRecorder | Medium | `bindEntryEditor()` |
| Community safety map with anonymous, grid-snapped, moderated reports | Hard | `shareSpotsAnonymously()` |
| Offline map tiles with a service worker, installable PWA | Medium | `showStreetMap()` |
| Passkey (WebAuthn PRF) unlock for the journal, PIN as fallback | Hard | `unlockJournal()` |

Search the source for `TODO(student)` to find each stub; the About page lists the same eight with a one-line hint each.

## Data and safety notes

- **Mahfooz never contacts emergency services automatically.** A web page cannot send SMS or WhatsApp messages or place calls by itself: it opens your own app with the text ready and you press send. It cannot track location in the background; reminders, breadcrumbs and the fake call only run while the page is open and the screen is on. Automatic SMS, background location and server-side alerts need a native app or a server (student TODOs above).
- **Everything stays on the device.** No account, no server, no analytics. Network requests: Google Fonts, and only if you choose the street map, Leaflet 1.9.4 from unpkg (with subresource integrity) and OpenStreetMap tiles, which reveal the area you view to the tile server (the UI says so).
- **Illustrative content.** All demo names, numbers (placeholders such as `0300-0000101`), places, journeys and journal entries are made up; demo spots around Model Town and Gulberg, Lahore are not reports about real places. Helpline numbers and the summaries of the workplace harassment Act (2010, amended 2022), PECA 2016, provincial domestic violence laws, CrPC sections 154 and 22-A and PPC section 509 are general information, not legal advice; they change and differ by province, so verify them locally and with a lawyer or support organisation.
- **Security limits.** The journal is only as strong as its PIN (a 4-digit PIN has 10,000 possibilities; the UI recommends six or more digits or a passcode) and cannot protect against malware or someone watching you type. Exported reports and JSON backups are not encrypted (except the journal inside a backup); delete them from Downloads after use. Clearing traces cannot erase browser history, downloads or messages already sent.
- **Strobe.** The flash alternates at 220 ms per phase (under three flashes per second, the WCAG general-flash threshold) and slows to 1 Hz when reduced motion is on; Settings carries a photosensitivity warning and the strobe never starts automatically unless you switch that on.

## Verification

- **Harness** (`check.mjs`, 1366×850 and 390×844): 18 states — home, home in Urdu, SOS countdown, SOS active, active journey, incoming fake call, locked journal, unlocked journal, complaint report sheet, safety map with a selected spot, rights guide, quiz feedback, calculator disguise, onboarding tour, trusted circle in Urdu, settings, notifications and the quick-exit page — with no console, page or request errors and no horizontal overflow. Geolocation and WebAudio were stubbed in the harness actions.
- **Behaviour script** (Playwright, 169 checks, all passing): seed and boot (no location or audio requested on load); journal stores ciphertext only (no plaintext of any seeded or new entry and no image data in `localStorage`), a wrong PIN fails both through the app and through an independent WebCrypto decryption attempt, PIN change re-encrypts, five wrong PINs force a wait; complaint reports in all three types and both languages plus the downloaded file; a real 3-second press-and-hold through the mouse (a short press does nothing), cancel countdown, active SOS with the map link, `sms:` and `wa.me` links for a real contact and none for demo contacts, siren, strobe, minimise, end flow and practice mode; circle add/validate/reorder/test, check-ins, profile and code word (never on the wallet card); journey start, all three escalation stages, extension, breadcrumbs, archive and GPX; fake call schedule, cancel, ring, answer and end; map pins, filters, tap-to-add, zoom, Leaflet lazy load with SRI (served locally) and the offline fallback; guides and a 12/12 quiz; calculator arithmetic, PIN unlock, double-Esc quick exit, neutral-page return and clear traces; theme, text size, export/import, demo toggle, inbox, search, shortcuts and the tour; and no overflow on 15 routes in both languages at both widths.

## Evaluation ideas for the FYP report

- **Time to alert:** measure seconds from pocket to a sent SMS for 10 participants, with and without practice runs; compare with dialling 15.
- **False triggers:** count accidental SOS starts over a week of carrying the phone with the 3 s hold, with and without the cancel countdown.
- **Usability in Urdu and English:** SUS questionnaire and task completion (start a journey, schedule a fake call, write and export a journal entry) for Urdu-first and English-first users.
- **Escalation timing:** simulate 50 journeys at 60x with random arrival delays and verify each stage fires at ETA, ETA + g and ETA + 2g and never sends anything automatically.
- **Crypto checks:** show that `localStorage` holds no plaintext of any entry; time PBKDF2 at 310,000 rounds on a low-end Android phone; estimate PIN brute-force time for 4, 6 and 8 digits.
- **Disguise:** ask testers who do not know the app to identify what the calculator is; record whether anyone discovers the unlock.
- **Accessibility:** keyboard-only SOS (focus + hold Space), screen-reader announcements for the countdown and results, contrast in both themes.
