# 13 · Privacy-First Password Manager (idea #51)

**Your passwords never leave the device — and the app shows you exactly how they are protected.** A zero-knowledge, offline, single-file vault: PBKDF2 → AES-GCM encryption of the whole vault, TOTP codes, an explainable strength analyzer, a cryptographically random generator, a health dashboard with reused/weak/old findings and a k-anonymity breach-check UI. English-only developer-grade tool.

## Getting started

- Open `index.html` in any modern browser (Chrome, Edge, Firefox, Safari). It works from `file://` — WebCrypto is available there in Chromium and Firefox; if your browser refuses, serve the folder over HTTP (`npx serve .`) or `localhost`.
- No build step, no server, no dependencies beyond Google Fonts (Inter Tight + JetBrains Mono; falls back to system fonts offline). No crypto library: everything is `window.crypto.subtle`.
- **Demo:** click **Load demo vault** on the lock screen, then unlock with `demo-pass-1234`. Twelve realistic entries (HBL, JazzCash, Easypaisa, Gmail, university LMS, Netflix, GitHub with a live TOTP secret, home Wi-Fi, Facebook, Instagram, NADRA, Daraz) include reused, weak, old and no-2FA examples so the Health page has findings.
- **Where data lives:** `localStorage["pfpm:vault"]` holds one record — `{format, version, kdf, iterations, salt, cipher, verifier, data:{iv, ct}, createdAt, savedAt}`. Nothing in it is plaintext. `localStorage["pfpm:prefs"]` holds non-secret preferences (auto-lock minutes, clipboard timer, KDF iterations, accent).
- **Reset:** Settings → Danger zone → *Wipe vault* (type `DELETE`), or in the console: `Object.keys(localStorage).filter(k => k.startsWith('pfpm:')).forEach(k => localStorage.removeItem(k))`.
- Forgetting the master password means the data is unrecoverable — by design. Export an encrypted backup from Settings.

## The problem

People reuse one weak password across banking apps, JazzCash/Easypaisa, email and social accounts. Cloud password managers are paid, or ask users to trust a company with everything. This template is a vault that belongs to the user alone — one HTML file, no account, no network — and it doubles as a teaching artefact: every screen explains the cryptography and the maths behind its verdicts.

## What is built

| Area | Features |
|---|---|
| Vault crypto | Master password → PBKDF2-SHA-256 (600,000 iterations, random 16-byte salt) → AES-GCM-256 key; whole vault JSON encrypted as one blob with a fresh 12-byte IV per save; verifier = AES-GCM encryption of a constant (no password hash stored); create (12+ chars, at least *Fair*), unlock, lock, change master password (re-encrypts with new salt), auto-lock after N idle minutes and on tab hide, clipboard cleared after 20 s, "How your vault is protected" explainer with the live parameters |
| Entries | Title, username, password, URL, notes, tags, favourite, colour, created/updated/password-changed timestamps, password history (kept automatically on change), custom fields with a secret flag, TOTP secret (Base32) → live 6-digit HMAC-SHA-1 code with 30-second countdown ring and copy; add / edit / delete / duplicate, search over title/username/URL/tags, four sort orders, tag and favourites views, reveal/hide per field, deep links `#all/<id>` |
| Generator | Random (8–64 chars, class toggles, exclude ambiguous, require each class) with unbiased `crypto.getRandomValues` sampling; passphrases from a ~300-word embedded list (separator, capitalisation, number suffix); pronounceable syllables; entropy bits shown next to the analyzer's verdict; one click to open a new entry with the result; dice button inside the editor |
| Strength analyzer | Entropy from character classes × length, then pattern penalties: common-password list (~230, with Pakistani context), passphrase wordlist, l33t normalisation, keyboard walks, sequences, repeats, years, dates, long digit runs; crack time at 10¹⁰ guesses/s; verdict Very weak → Very strong with the reasons listed; used live in the editor, create-vault and change-master forms, the list dots, the detail pane and the Health page |
| Health | Weighted score ring (weak 45 % · reused 30 % · older than a year 15 % · missing 2FA 10 %), KPI tiles, strength-distribution bars, reused-password groups, "Items to fix" with deep links, breach check that computes SHA-1 locally, shows the 5-character prefix that *would* be sent and explains k-anonymity; the network call is a stub (`checkRangeAPI()` returns `null`) with clear UI messaging |
| Import / export | Encrypted export (.json = the vault record), import encrypted export (merge or replace; also "Restore" on the lock screen), CSV import in the common browser-export shape with header aliasing and a quoted-field parser, plaintext CSV export behind a typed `EXPORT` confirmation |
| Settings | Auto-lock minutes, lock-on-hide toggle, clipboard seconds, KDF iterations (min 600k, applied on master-password change), four accent colours, wipe vault (type `DELETE`) |
| About | Problem, threat model (what it protects against / what it does not), how-it-works, code-hygiene notes and the student TODO panel; also reachable from the lock screen |
| UI kit | Security-console theme (near-black, electric cyan), SVG icon sprite (no emoji), unlock transition, staggered lists, copy feedback, TOTP ring, animated meters/bars/rings and counters, all disabled under `prefers-reduced-motion`; three-pane desktop layout, list ↔ detail with back button and a slide-in rail on mobile |

## Architecture (single file)

```
index.html
├─ <style>      tokens (security console), icon + motion kit, buttons/inputs, lock card, 3-pane shell,
│               list rows, detail fields, meter, TOTP ring, health rings/bars, modal, toasts, responsive, print
├─ <svg>        inline icon sprite (50 Lucide-style symbols used by ic('name'))
├─ <main #lock> lock screen (create / unlock / demo / restore / about)
├─ <header .mobilebar> + <div #app>  rail · list pane · detail pane · content pane (health/generator/settings/about)
└─ <script>
   ├─ utils          $, store (pfpm: prefix), esc, toast, ic(), animateIn, countUp, download, pickFile
   ├─ crypto         deriveKey (PBKDF2 → AES-GCM), encryptJSON/decryptJSON, sha1Hex, base32Decode, totp()
   ├─ wordlists      COMMON (rank-ordered), WORDS (passphrases), KNOWN_2FA
   ├─ analyzer       analyze() → {bits, score, verdict, crack, reasons}; crackTime(); meter/strength/reasons HTML
   ├─ generator      randInt (rejection sampling), genRandom, genPassphrase, genPronounceable
   ├─ vault state    prefs, session {key, data}, createVault, unlockVault, saveVault, changeMasterPassword,
   │                 lock() (zeroes references), auto-lock timer, visibilitychange, copyText (timed clear)
   ├─ stubs          unlockWithPasskey, autofillBridge, syncVault, shareEntry, recoveryKit, checkRangeAPI
   ├─ demo data      demoEntries() — 12 entries with history, custom fields and a TOTP secret
   ├─ modal          openModal / closeModal / confirmModal (typed confirmation), show/hide toggles, Esc
   ├─ lock screen    renderLock, loadDemo, enterApp, protectHTML
   ├─ rail / list    renderRail, visibleEntries, renderList (stagger on view change)
   ├─ detail         renderDetail, startTotp/stopTotp, copy-by-key, reveal-by-key, fav/edit/dup/del
   ├─ editor         openEditor (live meter, generator, TOTP preview, custom fields, tag chips, colour)
   ├─ health         healthReport, renderHealth (ring, KPIs, distribution, groups, fixes, breach check)
   ├─ generator page renderGenerator (three modes, live options)
   ├─ settings       renderSettings, openChangeMaster, applyAccent
   ├─ import/export  exportEncrypted, importEncrypted, parseCSV, importCSV, exportCSV
   ├─ about          TODOS, aboutHTML, renderAbout
   └─ router         hash routes: #all · #fav · #tag/<name> · (…/<id>) · #health · #generator · #settings · #about
```

## Domain logic / algorithm notes

- **Key derivation and storage.** `deriveKey(password, salt, iterations)` imports the password as PBKDF2 material and derives a non-extractable AES-GCM-256 key. `createVault` makes a random salt, encrypts the verifier `{v:'pfpm-verifier-1'}` and the data `{entries, createdAt}` and stores the record. `unlockVault` derives the key, decrypts the verifier (a clean "wrong password" signal — AES-GCM is authenticated, so a wrong key simply fails), then the data. Every `saveVault()` re-encrypts the full JSON with a new IV; the master password is never kept in memory after derivation.
- **TOTP (RFC 6238).** Base32 → bytes, counter = ⌊t / 30 s⌋ as a big-endian 8-byte buffer, HMAC-SHA-1 via `crypto.subtle.sign`, dynamic truncation (`offset = last nibble`, 31-bit integer mod 10⁶). The ring's `stroke-dashoffset` follows the seconds remaining; the code recomputes only when the counter changes.
- **Strength.** Raw bits = length × log₂(alphabet size: 26/26/10/33 per class present). Pattern matches (dictionary words incl. l33t and capitalised forms, keyboard walks ≥ 4, sequences ≥ 3, repeats, years, dates, 7+ digit runs) are resolved greedily left-to-right, longest first; each covered span contributes its own small entropy (e.g. log₂(rank + 2) for a common password, log₂(300) per wordlist word, 7 bits for a year) instead of length × log₂(pool). Crack time = 2^(bits − 1) / 10¹⁰ s. Thresholds: < 30 Very weak, < 45 Weak, < 60 Fair, < 80 Strong, ≥ 80 Very strong.
- **Generator entropy.** Random: length × log₂(pool). Passphrase: words × log₂(300) (+1 bit/word for random caps, +6.6 bits for a 2-digit suffix). Pronounceable: sum of log₂(choices) per random decision. `randInt(n)` rejects values ≥ ⌊2³²/n⌋·n to avoid modulo bias.
- **Health score** = 100 × (1 − (0.45·weak + 0.30·reused + 0.15·old + 0.10·noTOTP) / n), where *weak* = analyzer score ≤ 1, *reused* = password shared by ≥ 2 entries, *old* = password changed > 365 days ago, *noTOTP* = no secret and the URL/title matches a known-2FA service list.
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

Search the source for `TODO(student)` to find each stub. Lower-effort upgrades: swap `WORDS` for the 7,776-word EFF list (12.9 bits/word), add a larger common-password list, add drag-to-reorder for custom fields.

## Data / safety notes

- **Threat model.** Protects against device theft with a locked vault, cloud/provider leaks (there is no cloud), casual DevTools snooping and tampering (authenticated encryption). Does **not** protect against malware or keyloggers on the device, malicious extensions, shoulder surfing while a secret is revealed, clipboard sniffers during the clear window, a weak master password, or a tampered copy of this file.
- **Hygiene in code.** Secrets are rendered through `esc()` or `textContent`; copy/reveal buttons look values up by key rather than storing them in attributes; password inputs use `autocomplete="off"`; `session.key`, `session.data` and the analyzer cache are nulled on lock; `<meta name="referrer" content="no-referrer">`; links open with `rel="noopener noreferrer"`; no analytics or third-party scripts. A strict Content-Security-Policy cannot be applied to an inline-script single file — a bundled build with script hashes would allow one.
- **Verified:** after loading the demo, every value in `localStorage` was scanned for the demo master password and every demo entry password/username/TOTP secret — none appear (see the harness check in the project notes). The demo data is fictional; the phone numbers, CNIC and account numbers are placeholders.
- Preferences are plaintext because they are not secrets. The passphrase wordlist is small (≈ 8.2 bits/word) so passphrases from it are rated honestly by the analyzer; the About page says so.
