# 25 · FraudShield — Scam & Fraud Detection for Digital Payments (idea #90)

**Check a message, a link or a job offer before you act — and give analysts a lab to train scam classifiers and review suspicious transactions.** A dark security-console app in one HTML file: an explainable SMS / WhatsApp analyser for English, Urdu and Roman Urdu, naive Bayes and logistic regression written from scratch and trained in the browser, a link checker that decodes punycode and catches lookalike bank domains, a fake job-ad checker, an isolation-forest transaction lab with an analyst review queue and precision-at-k, complaint drafts for banks, wallets, the cybercrime portal and PTA, 12 awareness lessons and a 15-question quiz. English / اردو throughout.

**Scale:** 7,238 lines in one `index.html` (≈ 6,000 lines of JavaScript, ≈ 1,080 of CSS, 95 sprite icons) · 15 routed modules + tour, search palette, shortcuts sheet, detail drawer and 8 printable reports · seed data: 224 labelled messages (108 scam / 116 legitimate across English, Urdu and Roman Urdu), 12 scam patterns, 15 quiz questions, 21 trusted domains, 7 complaint contacts, 1,308 generated transactions over 70 accounts (42 seeded anomalies), plus demo records (8 analyses, 3 evidence items, 9 review labels, 4 notifications, 1 saved job ad, 1 saved complaint draft and a pre-filled complaint form) · 841 bilingual UI strings plus bilingual lesson, quiz, flag and checklist content · 7 student TODOs.

## Getting started

- Open `index.html` in any modern browser. It works offline from `file://`; the only network request is Google Fonts (Red Hat Display, Red Hat Mono, Noto Nastaliq Urdu), with system-font fallbacks.
- The first open loads illustrative demo data, trains both classifiers (≈ 0.1 s), generates and scores the transactions (≈ 0.3 s) and starts an 8-step spotlight tour. Replay it from **Settings → Replay the tour**.
- Try: **Home → Lucky draw (Roman Urdu)**, the **Link checker** example `xn--bl-3wc.com`, **Classifier lab → Train & evaluate**, **Transaction lab → Flagged → click a row**, then **Review queue** with the `F` / `L` / `E` / `S` / `Z` keys.
- **Where data lives:** `localStorage` under the `fs:` prefix — `settings`, `draft`, `history`, `evidence`, `evsel`, `links`, `trusted`, `dataset`, `mlopts`, `model` (only when a model file is imported), `jobs`, `jobdraft`, `jobchecks`, `txncfg`, `reviews`, `reportdraft`, `reports`, `lessons`, `quiz`, `inbox`, `log`, `seeded`. Transactions are regenerated deterministically from `txncfg.seed`, so they are not stored.
- **Backup / reset:** Settings → Export / Import (one JSON file) and Reset everything. Turn **Show demo data** off to hide every seeded record.
- **Console API:** `window.FraudShield` exposes `analyseMessage`, `analyseUrl`, `punyDecode`, `trainModels`, `crossValidate`, `predict`, `generateTransactions`, `scoreTransactions`, `precisionAtK` and more for experiments and tests.

## The problem

Pakistan has moved to digital payments fast (IDEAS.md quotes SBP: 92 % of retail payments digital in March 2026; reported cybercrime up 35 % in 2025 — verify the current figures). Scams moved with it: fake BISP / Ehsaas payment SMS, "your account is blocked" KYC calls, WhatsApp OTP theft, "sent by mistake" refund tricks, Telegram task jobs, lookalike bank links and SIM swaps. Victims rarely know the warning signs, banks see the fraud only after the money has left, and complaints are slowed by missing evidence. FraudShield is a teaching-grade prototype for both sides: a public desk that explains every red flag in the user's language, and an analyst console that shows how text classification and anomaly detection actually work, with honest evaluation against baselines.

## What is built

| Area | Features |
|---|---|
| Message analyser | Split view: sample picker (8), sender, channel (SMS / WhatsApp / call / email / social), text with live script detection (English / Urdu / Roman Urdu / mixed). Verdict gauge blending rule points with a classifier probability, three sub-scores, highlighted message (click a highlight to jump to its explanation), red-flag cards with evidence snippets and points, combination bonuses, classifier second opinion with top contributing tokens, links found (each run through the link checker), "what to do now" steps, save as evidence, draft complaint, add to training data as scam / legitimate, copy summary, print, history of recent checks |
| Rule engine | 16 bilingual pattern flags (OTP/PIN request, account threat, fee / transfer request, prize, congratulations, BISP / government, urgency, emotional pressure, secrecy, remote-access apps, wrong-transfer refund, investment, task jobs, brand mention, money, protective wording) + 8 context flags (personal mobile number, foreign number, organisation claim from a personal number, government message not from 8171, official short code / sender name, link, suspicious link, shouting) + 5 combinations; Urdu letter-variant normalisation and Roman Urdu spelling alternatives; suppression of matches inside "never share your OTP" wording |
| Classifier lab | Multinomial naive Bayes and L2 logistic regression (full-batch gradient descent) on unigrams + bigrams, optional hybrid rule-flag features; stratified seeded train/test split, majority-class baseline, accuracy / precision / recall / F1 / AUC, confusion matrices, live decision threshold, loss curve (train vs test), accuracy per script, misclassified test messages, 5-fold cross-validation; dataset browser with filters, search, pagination, add / delete / relabel (click a label) and a stale-model banner; explain tab with per-token contributions and the strongest global tokens; model export / import (JSON) and dataset CSV export; printable evaluation report |
| Link checker | Parses links without trusting the browser, decodes punycode (own RFC 3492 decoder), visual-skeleton homoglyph matching and Damerau–Levenshtein typosquat detection against an editable trusted-domain list, brand-in-subdomain and brand-in-path, shorteners, raw / encoded IPs, risky TLDs, fake `gov-pk`, `@` userinfo trick, `.apk` downloads, redirects, percent-encoding, bidi control characters, ports, many sub-domains, hyphens, phishing keywords; colour-coded anatomy showing the real owner domain; defanged copy; save as evidence; history |
| Fake job-ad checker | 13 weighted signals (fees, unrealistic pay with a salary parser for daily / weekly / monthly and lakh, task / MLM schemes, no interview, document requests, overseas visa charges, free-mail recruiter, chat-only contact, no registration / address, urgency, vague description, shouting, verifiable details), 8-step verification checklist saved per ad, 3 samples, saved ads |
| Transaction anomaly lab | Seeded generator (70 accounts with home city, rhythm, typical amount, devices and beneficiaries; 6 transaction types; legitimate oddities such as phone changes, travel and rent spikes) with injected account takeover, velocity bursts, SIM swap and "test charge then drain"; per-account features computed only from the past; isolation forest (100 trees, ψ = 256) + 7 explainable rules blended into one score; KPIs incl. AUC; precision-at-k chart for forest / rules / combined vs random baseline, against seeded truth or analyst labels (with table view); score histogram with threshold; hour × amount scatter with seeded anomalies ringed; fan-in beneficiaries (possible mules); flagged table; searchable, sortable, paginated explorer; tuning of blend, threshold and rule weights; detail drawer with features, rule hits, isolation-path attribution and account timeline |
| Analyst review queue | Queue of flagged transactions from the top score down (pending / reviewed / all), case card comparing amount, time, device and beneficiary with the account's normal behaviour, timeline, analyst note, fraud / legitimate / escalate / skip with keyboard shortcuts and undo, progress, analyst precision and agreement with seeded truth (lab only), printable review report |
| Analyst dashboard | KPIs (messages checked, model F1 vs baseline, flagged, review progress, P@20), verdict mix, script mix, most frequent red flags, precision-at-k, top flagged transactions (open the drawer), recent activity, printable summary |
| Evidence locker | Saved messages, links, job ads and transactions with notes, filters, selection for complaints, JSON export |
| Complaint generator | Bank / wallet, cybercrime and PTA templates in English or Urdu, addressee from an illustrative contacts directory, complainant and incident fields, CNIC masking, auto-written description from selected evidence, evidence list, live letter preview, copy / .txt download / print / saved drafts, next steps |
| Awareness | 12 scam patterns (summary, how it works, red flags, what to do, fictional example that opens in the analyser), read tracking, printable handout; 15-question quiz with explanations, mistakes review, best score and attempts |
| Platform | English / Urdu with RTL Nastaliq layout, dark / light / system theme, text size, reduced motion, demo-data toggle, JSON backup / import / reset, notifications inbox (filters, read state) and activity log (export, print), global search palette (Ctrl K or /), shortcuts sheet (?), 8-step onboarding tour, 8 printable reports with print CSS, aria-live announcements, labelled controls, focus styles, keyboard-closable modal and drawer, mobile tab bar and drawer navigation |

## Module map

| Module | Key functions | Storage keys (`fs:`) |
|---|---|---|
| Text pipeline | `normalizeWithMap`, `detectScript`, `extractUrls`, `mlTokens`, `displayToken`, `bidiText` | — |
| Rule engine | `findPatternFlags`, `applySuppression`, `contextFlags`, `comboHits`, `analyseMessage`, `adviceFor`, `highlightHTML` | — |
| Message analyser | `fullAnalysis`, `runAnalysis`, `recordHistory`, `addToDataset`, `saveMessageEvidence`, `ocrScreenshot` (stub) | `draft`, `history`, `evidence`, `dataset` |
| Classifiers | `allExamples`, `stratifiedSplit`, `buildVocab`, `trainNB`, `trainLR`, `docTokens`, `trainModels`, `predict`, `evaluateModel`, `confusion`, `aucScore`, `baselineFor`, `crossValidate`, `globalTopTokens`, `exportModelJSON`, `importModelJSON`, `embedMessage` (stub) | `dataset`, `mlopts`, `model` |
| Classifier lab UI | `retrain`, `relabel`, `evaluateTab`, `datasetTab`, `explainTab`, `exportTab` | as above |
| Link checker | `punyDecode`, `skeleton`, `damerau`, `registrable`, `parseLink`, `brandChecks`, `structureChecks`, `analyseUrl`, `lookupDomainReputation` (stub) | `links`, `trusted` |
| Job-ad checker | `parseSalary`, `scoreJob`, `runJobCheck`, `checklistHTML` | `jobs`, `jobdraft`, `jobchecks` |
| Transaction lab | `makeAccounts`, `normalTxns`, `fraudBurst`, `generateTransactions`, `computeFeatures`, `buildITree`, `buildForest`, `isoScore`, `ruleScore`, `scoreTransactions`, `precisionAtK`, `muleGraph` (starter), `scoreStream` (stub) | `txncfg` |
| Review queue | `reviewQueue`, `setReview`, `undoReview`, `reviewStats`, `decide`, `reviewKeys` | `reviews` |
| Evidence + complaints | `evidenceRow`, `autoDescription`, `evidenceLines`, `letterEN`, `letterUR`, `buildLetter`, `submitCommunityReport` (stub) | `evidence`, `evsel`, `reportdraft`, `reports` |
| Learn + quiz | `lessonGrid`, `lessonDetail`, `markLessonRead`, `startQuiz`, `quizQuestion`, `finishQuiz` | `lessons`, `quiz` |
| Dashboard | `dashKpis`, `flagFrequency`, `verdictBars`, `topFlaggedList`, `activityList` | reads everything |
| Inbox + log | `notify`, `logEvent`, `updateBell`, `noteRow`, `logTab` | `inbox`, `log` |
| Settings | `applyAppearance`, `setLang`, `bindSettings`, `exportAll`, `importAll`, `resetAll` | `settings` |
| Search, shortcuts, tour | `searchIndex`, `searchRank`, `openSearch`, `handleKeys`, `startTour`, `placeTour` | `settings.tourDone` |
| Print | `printReport` + 8 builders | — |
| Demo seed | `seedHistory`, `seedEvidence`, `seedReviews`, `seedOther`, `seedNotes` | `seeded` |

## Architecture (single file)

```
index.html
├─ <head>          fonts (Red Hat Display, Red Hat Mono, Noto Nastaliq Urdu), title "… | FYP Template"
├─ <style>         tokens (dark slate / signal amber / alert red / safe green + light theme) · motion kit ·
│                  base + Urdu RTL rules · shell (rail, topbar, tab bar) · panels with corner ticks · KPI tiles ·
│                  buttons, inputs, chips, badges, tabs, tables, bars · modal, drawer, toast, tooltip, tour,
│                  palette · home hero + radar · analyser (gauge, highlights, flag cards, token bars) ·
│                  classifier lab (metrics, confusion matrix, charts) · link anatomy · job checklist ·
│                  transaction lab · review queue · complaint letter · lessons + quiz · settings · print · responsive
├─ <svg sprite>    95 stroke icons (kit + custom: shield-alert, gauge, radar, split, network, sim, …)
├─ shell           rail nav · topbar (search, language, theme, bell, shortcuts) · main · tab bar · overlays
└─ <script>
   ├─ utils         store (fs: prefix), esc, ic, rng/hashStr/gauss/shuffle, dates, toast, announce, animateIn,
   │                countUp, drawPath, animateNumbers, bindTooltips, download, pickFile, copyText, bidiText
   ├─ i18n          tr / t / th + 841 [English, Urdu] pairs (core + modules)
   ├─ lexicon       FLAG_DEFS (16 bilingual pattern flags), CONTEXT_FLAGS (8), COMBOS (5), ROMAN_VARIANTS, ROMAN_MARKERS
   ├─ dataset       DATASET_SEED — 224 labelled synthetic messages (en / ur / ru)
   ├─ content       PATTERNS (12), QUIZ (15), TRUSTED_SEED (21), CONTACTS_SEED (7), MSG_SAMPLES (8)
   ├─ state         settings, coll (record lists with demo flag), logEvent, notify, typed accessors
   ├─ text          normalisation with index map, script detection, extractors, tokeniser
   ├─ rules         compiled flags, context flags, scoring, advice, highlight segments
   ├─ ml            dataset assembly, split, NB, LR, metrics, AUC, baseline, CV, explanations, export/import
   ├─ links         punycode, confusables skeleton, Damerau–Levenshtein, registrable domain, link findings
   ├─ jobs          JOB_FLAGS, regexes, salary parser, scoreJob, checklist, samples
   ├─ txn           generator, features, isolation forest, rules, blending, precision@k, mule fan-in
   ├─ ui core       caches (model, transactions), routes + router, nav, modal/drawer, appearance, gauge, SVG charts
   ├─ pages         home · analyser · classifier lab (2 parts) · links · jobs · transaction lab (2 parts) · review ·
   │                evidence · complaints · learn + quiz · dashboard · inbox + settings · search + shortcuts + tour ·
   │                about + print
   └─ boot          demo seeding, shell bindings, boot(), window.FraudShield
```

During development the file was assembled from numbered parts; the deliverable is the single `index.html`, which can be edited directly — no build step.

## Domain logic / algorithm notes

- **Normalisation with an index map.** Text is NFC-normalised; Arabic/Persian letter variants are unified (ي → ی, ك → ک, ه → ہ …), Arabic-Indic digits become ASCII, diacritics, tatweel, zero-width and bidi controls are dropped, Latin letters repeated 3+ times are collapsed. Every kept character remembers its original index, so a regex match on the normalised text can be highlighted exactly in what the user typed.
- **Script detection.** Arabic-script vs Latin letter counts decide Urdu / Latin / mixed; Latin text with ≥ 2 Roman Urdu marker words (≥ 10 % of words, e.g. *hai, ap, karen, bhejen, mubarak*) is Roman Urdu.
- **Rules.** Each flag's English, Urdu and Roman Urdu patterns are compiled into one Unicode regex with letter-aware boundaries (`(?<![\p{L}\p{M}\p{N}_])…`, because `\b` does not work for Urdu). Roman Urdu spellings are explicit alternatives (`inaa?m|enaam`, `bhejen|bhejein|bhejain`). Credential and secrecy matches that overlap protective wording ("code kisi ko na batain") are suppressed. Points per flag = weight + 15 % per extra match (max 2) (+ link score / 5 for a bad link); combinations add 10–12. Score = 100 · (1 − e^(−raw/45)); levels at 25 and 55 (shifted ±10 by the sensitivity setting).
- **Blended verdict.** Final = (1 − β) · rule score + β · 100 · p(scam) from the chosen classifier (β = 0.35 by default, adjustable).
- **Naive Bayes.** Multinomial with Laplace/Lidstone smoothing α (0.5): log P(t | c) = log((n_tc + α) / (N_c + α|V|)); probability from the log-odds; a token's contribution is tf · (log P(t | scam) − log P(t | legit)).
- **Logistic regression.** Binary bag-of-words, L2-normalised per message; full-batch gradient descent (lr 4, 500 epochs, L2 5·10⁻⁴), loss recorded every 10 epochs for train and test; contribution = w_j · x_j. Hybrid mode appends one `rule:<flag>` token per raised flag.
- **Evaluation.** Stratified seeded split (25 % test), majority-class baseline trained on the training split, accuracy / precision / recall / F1 for the scam class, rank-based AUC (Mann–Whitney), per-script accuracy, 5-fold stratified CV. Current numbers (seed 42, n_test = 56, baseline 51.8 %): naive Bayes 85.7 % / F1 0.862 / AUC 0.955, logistic regression 91.1 % / F1 0.906 / AUC 0.974 (hybrid); text-only NB 87.5 % / F1 0.877 and LR 82.1 % / F1 0.821; 5-fold CV NB 86.2 %, LR 91.6 % vs baseline 51.6 %.
- **Link checker.** Hosts are parsed with a regex (userinfo, host, port, path, query), converted to ASCII with `URL` and decoded label-by-label with an RFC 3492 punycode decoder. The registrable domain uses a mini public-suffix list (`com.pk`, `gov.pk`, `co.uk` …). Homoglyph = the decoded label's visual skeleton (Cyrillic/Greek look-alikes and 0→o, 1→l, rn→m, vv→w) equals a trusted brand label while the text differs; typosquat = optimal-string-alignment distance ≤ 1 (≤ 2 for labels of 8+ letters). Findings carry points (e.g. homoglyph 45, `@` trick 40, typosquat 40, brand in subdomain 32, raw IP 30); ≥ 45 is dangerous, ≥ 18 suspicious, a trusted match subtracts 25.
- **Job ads.** Regex signals plus a salary parser (field or text; per day × 26, per week × 4.3, lakh) — "unrealistic" means > Rs 150,000 / month for no-experience, part-time or data-entry work, > Rs 400,000 for anything, or ≥ Rs 3,000 a day for easy work. Verifiable details (registration, address, company-domain email) subtract 10.
- **Transactions.** Normal behaviour per account: amounts log-normal around the account's base, hours inside its window, mostly known beneficiaries and one device. Features use only the account's past: log amount, amount z-score with a shrinkage prior, hour rarity (share of past transactions within ± 1 h), velocity in 1 h / 24 h, new device, new beneficiary (money-out types), city change and −log(minutes since last).
- **Isolation forest.** For each of t trees, sample ψ points, split recursively on a random feature at a uniform random value until depth ceil(log₂ ψ) or one point; path length h(x) adds c(n) = 2H(n−1) − 2(n−1)/n for unsplit leaves; score s = 2^(−E[h(x)] / c(ψ)). Attribution in the drawer weights each feature by 1 / (depth + 1) along every path. On the default seed: mean score 0.635 for seeded anomalies vs 0.414 for normal transactions, AUC 0.991, P@20 95 % (rules alone AUC 0.907, P@20 90 %; combined P@20 100 %; base rate 3.2 %).
- **Rules and blending.** Velocity (≥ 3 in 1 h: 25, ≥ 5: 35), new device 20, odd hour 15, new beneficiary 14, amount z ≥ 2: 12 (≥ 3: 25), city change 10, takeover combo 15, each multiplied by a tunable weight and capped at 100. Combined = blend · min-max-scaled forest score + (1 − blend) · rule score; flagged ≥ threshold (40 by default → 45 flagged, 32 of them seeded anomalies).
- **Precision at k** = share of true anomalies (or analyst-confirmed fraud, skipping unreviewed and escalated items) among the top k by each scorer; the dashed line is the base rate.

## Left for students

| Extension | Difficulty | Start here |
|---|---|---|
| Cloud sync for an analyst team — authenticated API behind the `store` interface, per-record conflict resolution | Medium | `store` in the utils section |
| Multilingual transformer embeddings (ONNX in the browser) compared with bag-of-words per script | Hard | `embedMessage()` |
| Screenshot OCR in Urdu and English (Tesseract.js in a worker) feeding the analyser | Medium | `ocrScreenshot()` |
| Live domain age and blocklists through your own server (RDAP / WHOIS) | Medium | `lookupDomainReputation()` |
| Community scam-number database with moderation, de-duplication and "reported N times" | Medium | `submitCommunityReport()` |
| Streaming transaction scoring with sliding-window features and incremental trees | Hard | `scoreStream()` |
| Mule-network graph analytics (union-find over devices and beneficiaries, PageRank, SVG force layout) | Hard | `muleGraph()` |

Search the source for `TODO(student)` to find each stub; the About page lists the same items in both languages.

## Data and safety notes

- **Guidance, not a guarantee.** A low score does not prove a message or link is safe; a high score is not a legal finding. The app never contacts banks, PTA, the police or any portal, never opens the links it checks and sends nothing over the network.
- **Illustrative names and contacts.** Bank, wallet, telecom and government names, domains, short codes (e.g. 8171 for BISP), helplines (e.g. 1991) and portals are illustrative and labelled "verify on the official site" in the UI. Cybercrime responsibilities in Pakistan have moved between agencies (FIA / NCCIA); the complaint template tells users to confirm the current channel. The trusted-domain list is editable.
- **Synthetic data.** All 224 training messages, every phone number and link in them, all demo records and all 1,308 transactions (names drawn from common first and last names) are invented. Because the rule lexicon was written alongside the dataset, rules alone label all 224 seed messages correctly and the hybrid models are optimistic — collect a real, consented, anonymised dataset before claiming accuracy.
- **Privacy.** Everything stays in this browser's `localStorage`. The complaint form masks the CNIC by default and warns users to share it only with official bodies. Evidence notes and drafts are never uploaded.
- **Ground truth.** Precision-at-k against "seeded truth" and the review page's "agreement with seeded truth" exist only because the data is synthetic; in production only analyst labels are available.

## Evaluation ideas for the FYP report

1. **Ablation:** rules only vs text-only NB / LR vs hybrid, per script (English / Urdu / Roman Urdu), with 5-fold CV mean ± sd.
2. **Threshold analysis:** precision–recall trade-off as the decision threshold moves; pick an operating point for "warn" vs "block".
3. **Error analysis:** categorise the misclassified test messages (e.g. legitimate OTP alerts, family money requests, Roman Urdu spelling variants) and show how relabelling or new rules change them.
4. **Real-data study:** collect consented, anonymised scam and normal messages (remove numbers and names), double-label a subset and report Cohen's κ before training.
5. **Link checker benchmark:** run it on a list of known phishing domains and the top legitimate Pakistani domains; report detection rate and false alarms per finding type.
6. **Anomaly lab sensitivity:** vary trees (10–300), ψ (32–1024), anomaly rate and blend; report AUC and P@k, and compare with a z-score-only baseline.
7. **Analyst workflow:** time-to-decision and precision@k from analyst labels for 2–3 reviewers using the queue; compare with seeded truth.
8. **Awareness impact:** pre/post quiz scores for a small group of users (students, parents) before and after reading the 12 lessons.
9. **Performance:** training and scoring time on a low-end Android phone; size of the exported model.
