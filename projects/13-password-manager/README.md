# 13 · Privacy-First Password Manager (idea #51)

**Your passwords never leave the device — and the app shows you exactly how they are protected.** A zero-knowledge, offline, single-file vault: PBKDF2 → AES-GCM encryption of the whole vault, five item types (logins, bank cards, secure notes, identities, Wi-Fi), live TOTP codes, an explainable strength analyzer, a cryptographically random generator, a health dashboard, rotation reminders with an inbox and activity log, a trash with restore, printable reports, a hands-on crypto lab, a command palette and an onboarding tour. English-only developer-grade tool.

**Scale:** ≈ 3,570 lines in one `index.html` (≈ 2,950 lines of JavaScript, ≈ 330 of CSS) · 18 modules · 26 seed items + 2 trashed items + 10 seed log events · 59 sprite icons · 7 student TODOs.

## Getting started

- Open `index.html` in any modern browser (Chrome, Edge, Firefox, Safari). It works from `file://` — WebCrypto is available there in Chromium and Firefox; if your browser refuses, serve the folder over HTTP (`npx serve .`) or `localhost`.
- No build step, no server, no dependencies beyond Google Fonts (Inter Tight + JetBrains Mono; falls back to system fonts offline). No crypto library: everything is `window.crypto.subtle`.
- **Demo:** click **Load demo vault** on the lock screen, then unlock with `demo-pass-1234`. 26 realistic items (HBL, JazzCash, Easypaisa, Meezan, Gmail, GitHub with a live TOTP secret, NADRA, FBR, two bank cards, two secure notes, a CNIC identity, two Wi-Fi networks…) include reused, weak, old, rotation-due, expired-card and no-2FA examples so Health and the Inbox have findings. The first unlock starts the onboarding tour.
- **Where data lives:** `localStorage["pfpm:vault"]` holds one record — `{format, version, kdf, iterations, salt, cipher, verifier, data:{iv, ct}, createdAt, savedAt}`. The encrypted `data` blob contains items, trash, the activity log and inbox notices. `localStorage["pfpm:prefs"]` holds non-secret preferences (auto-lock, clipboard timer, KDF iterations, accent, trash retention, tour state, failed-attempt timestamps, last export time).
- **Reset:** Settings → Danger zone → *Wipe vault* (type `DELETE`), or in the console: `Object.keys(localStorage).filter(k => k.startsWith('pfpm:')).forEach(k => localStorage.removeItem(k))`.
- Forgetting the master password means the data is unrecoverable — by design. Export an encrypted backup from Settings (the inbox reminds you after 30 days).

## The problem

People reuse one weak password across banking apps, JazzCash/Easypaisa, email and social accounts. Cloud password managers are paid, or ask users to trust a company with everything. This template is a vault that belongs to the user alone — one HTML file, no account, no network — and it doubles as a teaching artefact: every screen explains the cryptography and the maths behind its verdicts, and the crypto lab lets students run each primitive by hand.

## What is built

| Area | Features |
|---|---|
| Vault crypto | Master password → PBKDF2-SHA-256 (600,000 iterations, random 16-byte salt) → AES-GCM-256 key; whole vault JSON encrypted as one blob with a fresh 12-byte IV per save; verifier = AES-GCM encryption of a constant (no password hash stored); create (12+ chars, at least *Fair*), unlock, lock, change master password (re-encrypts with new salt), auto-lock after N idle minutes and on tab hide, clipboard cleared after 20 s, failed-attempt counter reported in the inbox, "How your vault is protected" explainer with the live parameters |
| Item types | **Login** (username, password, URL, TOTP), **Bank card** (holder, number with Luhn check and brand detection, expiry with expired/expiring alerts, CVV, PIN, stylised card face), **Secure note**, **Identity** (name, CNIC with format check, DOB → age, phone, email, address, passport), **Wi-Fi** (SSID, password, security, router, copyable `WIFI:` config string); every item has tags, favourite, colour, notes, custom fields with a secret flag, password history and rotation schedule |
| Entries UI | Three-pane layout (rail · list · detail) with type and tag views, search over title/username/URL/notes/tags, five sort orders, reveal/hide and copy per field (values looked up by key, never stored in the DOM), live TOTP code with 30-second ring, duplicate, favourite, move to trash, deep links `#all/<id>` |
| Generator | Random (8–64 chars, class toggles, exclude ambiguous, require each class) with unbiased `crypto.getRandomValues` sampling; passphrases from a 312-word list (separator, capitalisation, number suffix); pronounceable syllables; numeric PINs; entropy bits beside the analyzer's verdict; "use in new entry" and a dice button inside the editor |
| Strength analyzer | Entropy from character classes × length, then pattern penalties: common-password list (~240, Pakistani context), passphrase wordlist, l33t normalisation, keyboard walks, sequences, repeats, repeated separators, years, dates, long digit runs; crack time at 10¹⁰ guesses/s; verdict Very weak → Very strong with reasons; used in the editor, create/change-master forms, list dots, detail pane, Health and reports |
| Health | Weighted score ring (weak 45 % · reused 30 % · old/expired 15 % · missing 2FA 10 %), KPI tiles, strength-distribution bars, reused-password groups, "Items to fix" (weak, reused, old, rotation due, no 2FA, expired card) with deep links, k-anonymity breach check (SHA-1 locally, 5-char prefix shown, `checkRangeAPI()` stub explained in the UI) |
| Rotation & inbox | Per-item rotation interval (30/90/180/365 days) → due notices; rule-based inbox for rotation due, weak or reused passwords, old passwords, expiring/expired cards, trash purges, backup reminders and failed unlock attempts; read/unread/dismiss, filters by severity, unread badges in the rail and mobile bar |
| Activity log | Encrypted timeline of unlocks, failed attempts, locks, item changes, copies of secrets, trash/restore, imports/exports, master-password changes and settings; filter by kind; export as JSON (no secrets); printable |
| Trash | Soft delete with configurable retention (default 30 days), restore, delete forever, empty trash (typed confirmation), automatic purge on unlock, purge warnings in the inbox |
| Crypto lab | KDF benchmark (measures 100k PBKDF2 iterations, extrapolates unlock time, guesses/s and a 0.5-second recommendation), hash playground (SHA-1/256/512 + avalanche bit map), AES-GCM encrypt → flip a byte → decryption refused, TOTP walk-through showing key bytes, counter, HMAC, offset and truncation |
| Reports | Security audit, vault inventory and activity log as printable tables (print CSS isolates the chosen report; secrets never included) |
| Command palette & shortcuts | Ctrl K / ⌘ K quick open over items and actions with keyboard navigation; N, /, L, G-then-H/G/I/S and ? shortcuts; shortcuts sheet |
| Onboarding tour | Seven spotlight steps (rail, search, new item, detail, health, quick open, lock) on first unlock; replay from Settings; skips steps whose target is hidden on phones |
| Import / export | Encrypted export (.json = the vault record), import encrypted export (merge or replace; also "Restore" on the lock screen), CSV import in the browser-export shape with header aliasing and a quoted-field parser, plaintext CSV export behind a typed `EXPORT` confirmation |
| Settings | Auto-lock, lock-on-hide, clipboard seconds, trash retention, KDF iterations (min 600k, applied on master-password change), four accent colours, replay tour, shortcuts, change master password, backup/import, clear log, wipe vault (type `DELETE`) |
| About | Problem, threat model (protects / does not protect), how it works, code hygiene and the student TODO panel; also reachable from the lock screen |

## Architecture (single file)

```
index.html
├─ <style>      tokens (security console), icon + motion kit, buttons/inputs, lock card, 3-pane shell, list rows,
│               detail fields, meter, TOTP ring, card face, inbox/timeline, palette/tour, lab, report tables, print
├─ <svg>        inline icon sprite (59 Lucide-style symbols used by ic('name'))
├─ <main #lock> lock screen (create / unlock / demo / restore / about)
├─ <header .mobilebar> + <div #app>  rail · list pane · detail pane · content pane (tool pages)
└─ <script>
   ├─ utils            $, store (pfpm: prefix), esc, toast, ic(), animateIn, countUp, download, pickFile, debounce
   ├─ crypto           deriveKey (PBKDF2 → AES-GCM), encryptJSON/decryptJSON, digestHex, base32Decode, totpSteps/totp
   ├─ wordlists        COMMON (rank-ordered), WORDS (312), KNOWN_2FA
   ├─ analyzer         analyze() → {bits, score, verdict, crack, reasons}; find* pattern helpers; crackTime(); meter HTML
   ├─ generator        randInt (rejection sampling), genRandom, genPassphrase, genPronounceable, genPin
   ├─ vault state      prefs, session {key, data}, TYPES, normalizeEntry, createVault, unlockVault, saveVault,
   │                   changeMasterPassword, lock() (zeroes references), auto-lock timer, copyText (timed clear)
   ├─ log & inbox      logEvent, addNotice, refreshNotices (rules), unreadCount, markNotice, dismissNotice
   ├─ trash            softDelete, restoreFromTrash, purgeExpiredTrash
   ├─ item helpers     luhnValid, cardBrand, formatCardNumber, cardExpiryState, cnicValid, wifiString
   ├─ stubs            unlockWithPasskey, autofillBridge, syncVault, shareEntry, recoveryKit, checkRangeAPI
   ├─ demo data        demoLogins, demoOtherTypes, demoTrash, demoLog → demoData()
   ├─ modal            openModal / closeModal / confirmModal (typed confirmation), show/hide toggles
   ├─ lock screen      renderLock (create/unlock forms), loadDemo, enterApp, protectHTML
   ├─ tour             TOUR_STEPS, startTour, nextTourStep, positionTour, finishTour
   ├─ palette          paletteResults, openPalette, openShortcuts, handleGlobalKeys
   ├─ rail / list      renderRail, visibleEntries, rowHTML, renderList
   ├─ detail           renderDetail with per-type field builders, startTotp, secretByKey, handleDetailClick
   ├─ editor           openEditor (type selector, per-type fields, live meter, TOTP preview, Luhn info), saveEditor
   ├─ health           healthReport, renderHealth, bindBreachCheck
   ├─ reports          auditReportHTML, inventoryReportHTML, activityReportHTML, printReport
   ├─ generator page   renderGenerator, generatorOptionsHTML, bindGeneratorOptions
   ├─ crypto lab       runKdfBenchmark, updateHashPlayground, bindAesDemo, updateTotpWalkthrough
   ├─ inbox            renderInbox (notices + activity timeline), bindInbox
   ├─ trash page       renderTrash
   ├─ settings         renderSettings, openChangeMaster, applyAccent
   ├─ import/export    exportEncrypted, importEncrypted, parseCSV, importCSV, exportCSV
   ├─ about            TODOS, aboutHTML, renderAbout
   └─ router           #all · #fav · #type/<t> · #tag/<name> · (…/<id>) · #health · #generator · #inbox · #trash · #lab · #reports · #settings · #about
```

## Module map

| Module | Main functions | Storage |
|---|---|---|
| Vault crypto & session | `deriveKey`, `createVault`, `unlockVault`, `saveVault`, `changeMasterPassword`, `lock` | `pfpm:vault` (encrypted record) |
| Preferences | `prefs`, `savePrefs`, `applyAccent`, `renderSettings` | `pfpm:prefs` (plaintext, non-secret) |
| Items (5 types) | `normalizeEntry`, `openEditor`, `saveEditor`, `renderList`, `renderDetail`, `luhnValid`, `cardBrand`, `cnicValid` | `data.entries` inside the blob |
| TOTP | `base32Decode`, `totpSteps`, `startTotp` | — |
| Strength analyzer | `analyze`, `find*`, `crackTime` | in-memory cache (cleared on lock) |
| Generator | `generate`, `genRandom`, `genPassphrase`, `genPronounceable`, `genPin` | in-memory `gen` options |
| Health & breach check | `healthReport`, `renderHealth`, `bindBreachCheck`, `checkRangeAPI` (stub) | — |
| Rotation & inbox | `refreshNotices`, `addNotice`, `markNotice`, `dismissNotice`, `renderInbox` | `data.notices` |
| Activity log | `logEvent`, `activityListHTML`, log export | `data.log` (capped at 400) |
| Trash | `softDelete`, `restoreFromTrash`, `purgeExpiredTrash`, `renderTrash` | `data.trash`, `prefs.trashDays` |
| Crypto lab | `runKdfBenchmark`, `updateHashPlayground`, `bindAesDemo`, `updateTotpWalkthrough` | — |
| Reports | `auditReportHTML`, `inventoryReportHTML`, `activityReportHTML`, `printReport` | — |
| Command palette & shortcuts | `openPalette`, `paletteResults`, `openShortcuts`, `handleGlobalKeys` | — |
| Onboarding tour | `startTour`, `nextTourStep`, `positionTour`, `finishTour` | `prefs.tourDone` |
| Import / export | `exportEncrypted`, `importEncrypted`, `parseCSV`, `importCSV`, `exportCSV` | `prefs.lastExport` |
| Lock screen | `renderLock`, `loadDemo`, `enterApp`, `protectHTML` | `prefs.failedAttempts` |
| Demo data | `demoData` and helpers | written into the encrypted blob |
| Router & shell | `route`, `parseHash`, `renderRail`, `renderPage` | hash in the URL |

## Domain logic / algorithm notes

- **Key derivation and storage.** `deriveKey(password, salt, iterations)` imports the password as PBKDF2 material and derives a non-extractable AES-GCM-256 key. `createVault` makes a random salt, encrypts the verifier `{v:'pfpm-verifier-1'}` and the data and stores the record. `unlockVault` derives the key, decrypts the verifier (a clean "wrong password" signal — AES-GCM is authenticated, so a wrong key simply fails), then the data. Every `saveVault()` re-encrypts the full JSON with a new IV; the master password is never kept in memory after derivation. Failed attempts while locked are counted in plaintext prefs (timestamps only) and reported inside the vault at the next unlock.
- **TOTP (RFC 6238).** Base32 → bytes, counter = ⌊t / 30 s⌋ as a big-endian 8-byte buffer, HMAC-SHA-1 via `crypto.subtle.sign`, dynamic truncation (`offset = last nibble`, 31-bit integer mod 10⁶). `totpSteps` exposes every intermediate value for the crypto lab.
- **Strength.** Raw bits = length × log₂(alphabet size: 26/26/10/33 per class present). Pattern matches (dictionary words incl. l33t and capitalised forms, keyboard walks ≥ 4, sequences ≥ 3, repeats, years, dates, 7+ digit runs, repeated separators between words) are resolved greedily left-to-right, longest first; each covered span contributes its own small entropy (e.g. log₂(rank + 2) for a common password, log₂(312) per wordlist word, 7 bits for a year) instead of length × log₂(pool). Crack time = 2^(bits − 1) / 10¹⁰ s. Thresholds: < 30 Very weak, < 45 Weak, < 60 Fair, < 80 Strong, ≥ 80 Very strong.
- **Generator entropy.** Random: length × log₂(pool). Passphrase: words × log₂(312) (+1 bit/word for random caps, +6.6 bits for a 2-digit suffix). Pronounceable: sum of log₂(choices) per random decision. PIN: digits × 3.32. `randInt(n)` rejects values ≥ ⌊2³²/n⌋·n to avoid modulo bias.
- **Health score** = 100 × (1 − (0.45·weak + 0.30·reused + 0.15·(old + expired cards) + 0.10·noTOTP) / n), where n counts items that hold a password (logins and Wi-Fi), *weak* = analyzer score ≤ 1, *reused* = password shared by ≥ 2 items, *old* = password changed > 365 days ago, *noTOTP* = login without a secret whose URL/title matches a known-2FA service.
- **Inbox rules** run after every save: rotation due within 7 days (or overdue), weak password, old password (only when not already weak and not on a schedule), reused groups, expired/expiring cards (60 days), trash purging within 5 days, no encrypted export for 30 days, failed unlock attempts. Notices keep their read state while the condition holds and disappear when it is resolved; dismissed ones stay hidden.
- **Cards.** Luhn: double every second digit from the right, subtract 9 when > 9, valid when the sum mod 10 = 0. Brand from the first digits (4 Visa, 34/37 Amex, 51–55 or 2221–2720 Mastercard, 62 UnionPay, 22 PayPak approx.). Expiry `MM/YY` → last moment of that month.
- **k-anonymity.** SHA-1(password) → first 5 hex chars (16⁵ ≈ 1M buckets) would be sent; the 35-char suffix is matched locally against the returned range. The UI performs the local half and labels the network half as the student stub.

## Left for students

| Extension | Difficulty | Start here |
|---|---|---|
| Argon2id KDF via WASM (memory-hard; store KDF params in the record, migrate on unlock) | ★★★ | `deriveKey()` |
| WebAuthn / passkey unlock (PRF or largeBlob extension wraps the vault key) | ★★★ | `unlockWithPasskey()` |
| Browser-extension autofill (content script ↔ vault bridge, origin matching) | ★★ | `autofillBridge()` |
| End-to-end-encrypted sync between devices (push the encrypted record only, per-entry conflict resolution) | ★★ | `syncVault()` |
| Secure sharing with public-key crypto (ECDH key pairs, wrapped entry keys) | ★★★ | `shareEntry()` |
| Live HIBP range API check (fetch `range/{prefix}`, parse `SUFFIX:COUNT`, match locally) | ★ | `checkRangeAPI()` |
| Emergency access / recovery kit (printable recovery key, Shamir shares, waiting period) | ★★ | `recoveryKit()` |

Search the source for `TODO(student)` to find each stub. Lower-effort upgrades: swap `WORDS` for the 7,776-word EFF list (12.9 bits/word), add a larger common-password list, render the Wi-Fi config string as a QR code, add drag-to-reorder for custom fields.

## Data / safety notes

- **Threat model.** Protects against device theft with a locked vault, cloud/provider leaks (there is no cloud), casual DevTools snooping and tampering (authenticated encryption). Does **not** protect against malware or keyloggers on the device, malicious extensions, shoulder surfing while a secret is revealed, clipboard sniffers during the clear window, a weak master password, or a tampered copy of this file.
- **Hygiene in code.** Secrets are rendered through `esc()` or `textContent`; copy/reveal buttons look values up by key rather than storing them in attributes; password inputs use `autocomplete="off"`; `session.key`, `session.data` and the analyzer cache are nulled on lock; reports and the exported activity log never contain passwords, codes or card numbers; `<meta name="referrer" content="no-referrer">`; links open with `rel="noopener noreferrer"`; no analytics or third-party scripts. A strict Content-Security-Policy cannot be applied to an inline-script single file — a bundled build with script hashes would allow one.
- **Verified:** after loading the demo, every value in `localStorage` was scanned for the demo master password and every demo password, TOTP secret, card number, CNIC and recovery code — none appear. The demo data is fictional; phone numbers, CNIC, card numbers (Luhn-valid test numbers) and account numbers are placeholders.
- Preferences are plaintext because they are not secrets. The passphrase wordlist is small (≈ 8.3 bits/word) so passphrases from it are rated honestly by the analyzer; the Generator page says so.
