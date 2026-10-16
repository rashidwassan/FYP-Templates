# 23 · CampusHub — Student Portal with Lost & Found (idea #88)

**A bilingual (English / اردو) university portal built around a lost and found that people can actually trust.** One HTML file gives a Pakistani campus a sidebar dashboard with a role switcher (student · society admin · staff moderator) and a hand-drawn SVG campus map: lost and found posts with photos and map pins, automatic lost-to-found matching, claims verified with questions whose answers are normalised and SHA-256 hashed, a witnessed handover log and a full status lifecycle — plus notices with read receipts, an events calendar with RSVP, waitlists and `.ics` export, a timetable with clash detection and a free-classroom finder, societies with join requests and roles, a ride-share board with women-only rides and contact reveal, an anonymous feedback box with an abuse/privacy filter, and role-based moderation with an audit log.

**Scale:** ≈ 7,120 lines in one `index.html` (≈ 6,050 lines of JavaScript, ≈ 920 of CSS, 109 sprite icons) · 13 routed modules (dashboard, lost & found with 5 views, notices, events, timetable with 3 views, societies, ride share, feedback, moderation with 4 tabs, reports, inbox, settings, about) plus global search, onboarding tour, shortcuts sheet and print layouts · 889 UI strings, every one in English and Urdu · 165 primary seed records (17 people, 22 lost/found posts, 3 handovers, 12 notices, 15 events, 32 course sections in 16 rooms, 10 societies, 10 rides, 10 feedback messages, 8 audit entries, 10 inbox messages) plus 117 read receipts, 32 memberships, RSVPs, claims and reports · 8 student TODOs.

## Getting started

- Open `index.html` in Chrome, Edge or Firefox. It runs from `file://` with no build step and no server; the only network request is Google Fonts (Onest + Noto Nastaliq Urdu), with system fonts as the offline fallback. Serve the folder over HTTP (`npx serve .`) if you want the phone camera to open directly from the photo picker.
- The first open (without a `#route`) starts a 7-step spotlight tour; replay it from Settings → Help or About.
- **Try the three roles** with the switcher at the top of the sidebar (or press `R`). Each role is a demo persona with its own inbox and permissions: *Ayesha Khan* (BS Computer Science student), *Hamza Raza* (President, Computing Society) and *Sadia Iqbal* (Student Affairs, staff moderator).
- **A complete lost and found walkthrough:** as Ayesha open Lost & found → Matches → *This is mine — claim* on the Casio calculator. The finder's demo answers are `Ayesha` and `cracked solar panel` (case, spaces and punctuation do not matter). Switch to Hamza (the finder), open the inbox notice, approve the claim, schedule the handover and confirm it with Ayesha's roll number `2022-CS-114`. The item becomes *Returned*, her lost post becomes *Resolved* and the Handover log gets a row. Other demo answers: Nike backpack `data structures` / `charger`; car key `Honda` / `Hunza`; student card `2025-ME-012`.
- **Where data lives:** `localStorage` keys prefixed `campushub:` (see the module map). Settings → Data → *Export backup* downloads everything as JSON; *Import backup* restores it; *Reset everything* clears the prefix and reseeds. *Show demo data* off hides every seeded record so you can start clean.
- **Keyboard:** `/` search · `1`–`8` modules · `N` lost post · `F` found post · `R` cycle role · `L` language · `D` dark theme · `I` inbox · `?` shortcut sheet · `Esc` closes dialogs, search and the tour.

## The problem

On most Pakistani campuses a lost calculator or student card ends up in a WhatsApp group screenshot and a drawer at the security office. Anyone can say "that's mine", nobody records who handed what to whom, and found items quietly pile up. The same students also chase notices across physical boards, miss events that clash with classes, wander between blocks looking for an empty room, negotiate rides in random groups, and keep complaints to themselves because they fear being identified. CampusHub puts these everyday problems in one portal with roles, and makes the lost and found trustworthy: posts are matched automatically, ownership is verified without revealing the answers, and every handover is witnessed and logged.

## What is built

| Area | Features |
|---|---|
| Shell | Sidebar dashboard (maroon · sand · teal, Onest + Noto Nastaliq Urdu), crest logo, persona card with a 3-way **role switcher**, grouped navigation with live badges, sticky top bar with global search, inbox bell, language toggle, shortcuts; off-canvas drawer on phones; full RTL mirroring in Urdu; light and dark themes; three text sizes |
| Hand-drawn campus map | 19 places (blocks, hostels, library, mosque, ground, gate, security office…) drawn as jittered double-stroke "sketch" outlines with hatching, roads, footpaths, trees, a pond and a compass; used for pins (lost / found / your pin / event venue), click-to-pick with nearest-building snapping, place filters, free-room badges and hotspot heat circles |
| Lost & found | Lost or found posts with title, 13 categories, colour, brand, date and time (no future dates), place + map pin, **photo resized on-device to a JPEG data URL** (drag-and-drop or camera); browse grid with search, type/category/status filters and sorting; map view with per-building counts; abuse and phone/email/CNIC checks on posts |
| Matching | Lost-to-found score from category (related categories get partial credit), title-weighted word similarity (stop-words, light stemming, synonyms such as *airpods = earbuds*, *navy = blue*, typo tolerance), pin distance and date gap (found-before-lost is impossible); *likely* and *possible* bands, a ring + per-signal bars explaining every score, inbox alerts for likely matches, "notify the owner" for finders and staff |
| Claims | Finder sets 1–3 questions with alternative answers (`black / dark grey`); answers are **normalised (case, punctuation, articles, diacritics, Urdu/Arabic digits) and SHA-256 hashed with the item id as salt** — WebCrypto with a pure-JS fallback; leak check stops a finder from putting the answer in the description; claimants answer + message + declaration; zero-match claims are not forwarded; 3 failed attempts lock claiming; finder or staff approve/reject with reasons; other pending claims auto-rejected |
| Handover & lifecycle | Schedule place/time/witness, then confirm with the claimant's roll number checked against the record; **handover log** (CSV + print); lifecycles *posted → claimed → verified → returned* (found) and *posted → match claimed → resolved* (lost); automatic expiry (lost 60 days; found 30 days, then the Security Office store) with history timelines |
| Notices | 8 categories, pinning, expiry chips (soon / today / expired) and "show expired", audiences (everyone, department, year, hostel residents) that decide who sees a notice, attachment **metadata** chips, unread dots and "mark all read", **per-user read receipts** with a read-by bar and receipt list for staff, bilingual posting form, extend/delete, inbox notification to the audience |
| Events | Month grid (Monday-first) and agenda views, type/host/"my RSVPs" filters, RSVP *going / interested / can't go*, capacity bars, **waitlist with automatic promotion** and notification, society or university host, venue on the map, attendee list for organisers, cancel with notifications, **`.ics` export** per event and for "my calendar" (UTC conversion from PKT, RFC 5545 escaping and 75-octet folding, 1-hour alarm), create form with **venue clash check** |
| Timetable | Catalogue of 18 courses / 32 sections with instructors and rooms; add/remove sections with **clash detection** (confirm-to-add, red split blocks), one-section-per-course rule, credit-hour total with illustrative limits, personal blocks, week grid, free gaps today, weekly `.ics` (16-week `RRULE`), printable timetable |
| Free-classroom finder | Rooms × slots matrix for any weekday, filters for room type and seats needed, "right now" (rolls to the next slot after hours and at weekends), free-room cards, buildings with free rooms highlighted on the map, utilisation % |
| Societies | Directory with categories and search, "my societies", detail page with banner, announcements (abuse-checked), upcoming events, **join requests** with motivation, approve/decline, **member roles** (president, VP, general secretary, treasurer, member) with a single-president rule (transfer demotes the old president to VP), remove member, leave; all decisions audited and notified |
| Ride share | Offers and requests, direction, area, days, time, vehicle, seats (icons), cost per seat with a **fair-share fuel-cost suggestion**, **women-only rides** (only women can post or request), seat requests with message, accept/decline by the driver, **contact numbers masked until accepted** then call / WhatsApp links, pause/delete, my rides |
| Anonymous feedback | Category + message with a live filter preview; **no user id stored**; random follow-up code (`FB-XXXX-XXXX`) kept only in this browser and stored as a hash; public wall with "me too" votes and topic filters; moderator replies and resolve; private follow-ups by code; code lookup from another device |
| Abuse / privacy filter | English, Roman Urdu and Urdu wordlists (mild / severe), leetspeak and repeated-letter normalisation, spaced-letter evasion (`s.t.u.p.i.d`), typo-tolerant matching, threat patterns, and personal-data detection (phone, email, CNIC, roll number, "Sir / Ms + Name"); used for feedback, posts, rides and announcements; redaction for publishing |
| Moderation | Role-scoped report queue (staff see everything; society admins see their society's events and announcements); reports from any post with reasons; **auto-hide after N distinct reporters**; dismiss / hide / remove / restore / warn author with a required reason; hidden-content list; feedback review (publish, publish redacted, reject, escalate safety issues, private reply, publish all clean); **audit log** of every privileged action with search, action filter, CSV and print; rules (thresholds, auto-publish, expiry days) and a filter tester |
| Reports | KPIs (posts, returned, return rate, median days to return), posts by category (grouped SVG bars), status donut, weekly posts line with draw-in, hotspot map; printable lost & found summary, handover log, timetable, event attendance sheet, audit log and feedback summary |
| Platform | Per-persona inbox with kinds, filters, read state and deep links; global search across posts, notices, events, societies, courses, rooms, rides, places and pages with keyboard navigation; settings (language, theme, text size, reduced motion, demo data, match alerts, profile incl. gender and hostel, JSON export/import/reset, storage use); onboarding tour; shortcuts sheet; focus-trapped modals; `aria-live` announcements; motion kit with `prefers-reduced-motion` and a manual override |

## Module map

| Module | Route | Key functions | Storage keys |
|---|---|---|---|
| State & seeding | — | `db`, `commit`, `ensureSeeded`, `visible`, `viewable`, `PERSONAS` | `seeded`, `role`, `lang`, `settings` |
| Dashboard | `#dashboard` | `renderDashboard`, `dashKpis`, `todayCard`, `attentionCard` | reads all |
| Campus map | (shared) | `campusMap`, `roughRect`, `roughEllipse`, `bindMap`, `nearestPlace`, `mapPoint` | — |
| Lost & found | `#lostfound/browse·map·matches·mine·log`, `#lostfound/<id>` | `renderLostFound`, `filteredItems`, `openItem`, `openItemForm`, `checkItemForm`, `saveItemForm` | `items`, `ui.lf` |
| Matching | (lost & found) | `matchScore`, `catScore`, `placeScore`, `dateScore`, `textScore`, `tokens`, `tokenSimilarity`, `matchPairs`, `notifyMatches` | — |
| Claims & handover | (lost & found) | `openClaimModal`, `submitClaim`, `recordClaim`, `decideClaim`, `openScheduleHandover`, `openConfirmHandover`, `completeHandover`, `expireItems` | `items`, `handovers` |
| Hashing | (shared) | `normaliseAnswer`, `splitAlternatives`, `sha256Hex`, `sha256Fallback`, `hashAnswer`, `randomToken` | — |
| Notices | `#notices`, `#notices/<id>` | `renderNotices`, `noticeList`, `audienceUsers`, `markRead`, `openReceipts`, `saveNotice` | `notices`, `reads` |
| Events | `#events`, `#events/<id>` | `renderEvents`, `monthView`, `agendaView`, `setRsvp`, `waitlist`, `eventIcs`, `icsFold`, `icsStamp`, `venueClash`, `saveEvent` | `events` |
| Timetable | `#timetable/week·courses·rooms` | `blocksFor`, `findClashes`, `clashesForSection`, `toggleSection`, `freeGaps`, `timetableIcs` | `timetable` |
| Free rooms | `#timetable/rooms` | `buildSections`, `roomBusy`, `freeRooms`, `currentSlot`, `roomMatrix` | — (derived) |
| Societies | `#societies`, `#societies/<id>` | `renderSocieties`, `renderSociety`, `openJoinForm`, `decideJoin`, `setMemberRole`, `postAnnouncement` | `societies` |
| Ride share | `#rides` | `renderRides`, `ridesFiltered`, `openSeatRequest`, `answerRideRequest`, `canSeeContact`, `suggestCost`, `saveRide` | `rides` |
| Feedback | `#feedback` | `submitFeedback`, `drawFeedbackWall`, `drawMyFeedback`, `replyFeedback`, `setFeedbackStatus` | `feedback`, `fbtokens` |
| Filter | (shared) | `screenText`, `wordLevel`, `joinSpacedLetters`, `highlightFlags`, `redactText` | — |
| Moderation | `#moderation/queue·feedback·audit·rules` | `reportContent`, `modAction`, `modQueue`, `decideFeedback`, `drawAudit`, `drawRules`, `audit` | `audit`, `settings` (+ `reports`/`mod` on each target) |
| Inbox | `#inbox` | `notify`, `renderInbox`, `openInboxItem`, `updateBell` | `inbox` |
| Reports | `#reports` | `lfStats`, `categoryChart`, `statusDonut`, `weeklyChart`, `hotspotMap`, `printReport` | — |
| Settings | `#settings` | `renderSettings`, `applySetting`, `applyTheme`, `exportData`, `importData`, `resetData` | `settings`, `users` |
| Search, tour, keys | overlay | `searchAll`, `drawSearch`, `startTour`, `showTourStep`, `onKey`, `openShortcuts` | `tour` |
| About | `#about` | `renderAbout`, `TODOS` | — |

## Architecture

```
index.html
├─ <style>        tokens (light + dark) · base · Urdu/RTL rules · sidebar shell · topbar · buttons/forms ·
│                 cards/chips/KPIs/bars · lists/tables · modal/toasts/search panel/tour · print area ·
│                 campus map · dashboard · lost & found · notices · calendar · timetable/matrix · societies ·
│                 rides · feedback/moderation · charts · motion kit · responsive (1100/900/640) · print
├─ <svg sprite>   109 stroke icons (kit + custom: smartphone, id-card, backpack, handshake, gavel, seat…)
├─ shell          skip link · aside (crest, persona + role switcher, nav) · topbar · <main#page> · modal ·
│                 tour · toasts · aria-live region · print area
└─ <script>
   ├─ utils        store (campushub: prefix), esc, toast, announce, animateIn/countUp/drawPath, rng, dates/times,
   │               phone helpers, downloads, resizeImage, CSV
   ├─ i18n         STR pairs [en, ur] added per module with addStrings(), t(), tx()
   ├─ campus map   PLACES, roads/paths/trees, rough shapes, campusMap(), bindMap(), nearestPlace()
   ├─ text         normaliseAnswer, SHA-256 (WebCrypto + pure-JS fallback), hashAnswer, tokens/stemming/synonyms,
   │               levenshtein, tokenSimilarity, abuse/PII screenText()
   ├─ seed         people, L&F posts with questions, handovers, notices + read receipts, events, slots/rooms/courses
   │               (buildSections()), timetables, societies, rides, feedback, audit, inbox
   ├─ state        settings, personas/roles, db()/commit() cache, ensureSeeded() (hashes demo answers), notify(),
   │               audit(), permissions (canManageSociety, canModerate…), viewable()
   ├─ ui           modal + focus trap, confirmBox, validate/setError/fail, chips, ring, page header, tabs, printDoc
   ├─ moderation core  TARGETS registry, reportContent (auto-hide), modAction, modQueue
   ├─ lost & found core · pages · detail · form · claims/handover
   ├─ notices · events · events detail/ics · timetable · free rooms · societies · society actions
   ├─ rides · ride actions · feedback · moderation UI · moderation tabs
   ├─ dashboard · inbox · reports · printable reports · settings · search · tour/shortcuts · about
   └─ router/boot  ROUTES, NAV_GROUPS, renderNav (badges), renderWho, switchRole, go(), rerender(), applyLang(), boot()
```

## Domain logic / algorithm notes

- **Answer verification.** `normaliseAnswer` applies NFKC, lower-case, Urdu/Arabic-Indic digits to ASCII, strips Latin diacritics and Arabic harakat, turns punctuation into spaces, drops articles (`the`, `a`, `my`, `is`…) and collapses spaces, so `"  The CRACKED Solar-Panel!! "` becomes `cracked solar panel`. Each accepted alternative is stored as `SHA-256("campushub|<itemId>|<normalised>")`; the item id is a per-item salt so equal answers on different items give different hashes. A claim records only which questions matched. Hashing in the browser does **not** stop an attacker who copies the data from guessing short answers offline — the attempt limit lives in the UI; a production system must verify on the server (student TODO).
- **Matching score** = 0.3 · category + 0.3 · text + 0.2 · place + 0.2 · date, then × 0.6 if categories are unrelated and × 0.5 if the item was "found" more than a day before it was "lost". Text = 0.6 · title similarity + 0.4 · full-text similarity, where similarity is a Dice overlap of stemmed, synonym-mapped tokens with partial credit for one-edit typos in words of 5+ letters. Place is 1 for the same building, otherwise falls linearly from 40 to 400 map units of pin distance. Date decays linearly over 14 days. ≥ 0.6 is *likely* (inbox alert), ≥ 0.4 *possible*.
- **Claim rules.** Zero correct answers → not forwarded, counts as a failed attempt; partial or full matches are forwarded with per-question ticks; three failed attempts lock claiming for that person on that item. Approving one claim rejects the other pending claims; rejecting the last pending claim reopens the post and the linked lost post. Handover requires the claimant's roll number (normalised compare) and a witness.
- **Expiry.** On load, open lost posts older than 60 days and open found posts older than 30 days expire (configurable in Moderation → Rules); found items "move to the Security Office store", logged as a system action.
- **Waitlist.** RSVP *going* on a full event becomes *waitlist* (ordered by timestamp). When a *going* person changes their answer, the earliest waitlisted person is promoted and notified. Seeded events carry a `base` count of registrations from the university system (labelled as seeded).
- **Calendar export.** Campus times are PKT (UTC+5, no DST) and are written as UTC `…Z` stamps; text is escaped per RFC 5545 and lines are folded at 75 octets using UTF-8 byte length (Urdu titles stay valid). Timetable export uses weekly `RRULE:FREQ=WEEKLY;COUNT=16` from the next matching weekday.
- **Clash detection.** Meetings clash when they share a weekday and `startA < endB && startB < endA`; detection is pairwise over the week (sections, labs spanning two slots and personal blocks). Seed sections get rooms from a greedy allocator that never double-books (`buildSections`), and the free-room finder tests the same overlap rule per room and slot.
- **Abuse and privacy filter.** Words are normalised (leetspeak, repeated letters, letters only), compared against mild/severe lists including Roman Urdu and Urdu terms, with one-edit tolerance for words of 6+ letters; spaced-letter evasions are joined first; threat patterns force *severe*; personal data (phone, email, CNIC, roll number, honorific + name) holds a message for review. Redaction masks words (`s*****`) and replaces personal data with `[removed]` / `[name removed]`.
- **Auto-hide.** When the number of distinct people with open reports on a post reaches the threshold (default 3), it is hidden, a system audit entry is written and staff are notified; *dismiss* or *restore* unhides it.
- **Fair-share ride cost** = (one-way km × 2 × fuel price ÷ km per litre) ÷ (seats + 1), rounded to PKR 10 — area distances, fuel price and mileage are illustrative constants.
- **Anonymity.** Feedback records contain no user id; the follow-up code is random (crypto), stored only as a SHA-256 hash in the record and in clear only in this browser's `fbtokens` list.

## Left for students

| Extension | Difficulty | Start here |
|---|---|---|
| Real sign-in (university SSO / OAuth) and server-side enforcement of every role check | Hard | `switchRole()` |
| Backend with real-time sync (Firebase / Supabase / Node + Postgres), localStorage as offline cache | Hard | `db()` |
| Photo similarity (average / difference hash, Hamming distance) as a fifth matching signal, weights tuned on labelled pairs | Medium | `matchScore()` |
| Web Push or email delivery for match alerts, claim decisions and waitlist promotions | Medium | `notify()` |
| Signed QR handover receipt that the owner scans on their own phone | Medium | `completeHandover()` |
| Room booking for study groups with department approval, shown as booked in the matrix | Medium | `bindRoomFinder()` |
| Route-aware ride matching on a real map (Leaflet + OSRM) with distance-based cost split | Hard | `saveRide()` |
| Trained abuse classifier (naive Bayes / logistic regression) evaluated against the wordlist | Medium | `screenText()` |

Search the source for `TODO(student)` — there is one marker per row, and the same list (in English and Urdu) is on the About page.

## Data and safety notes

- Everything is **illustrative demo data**: the campus layout, people, roll numbers, phone numbers (555 series), notices, deadlines, fees, scholarship rules, events, rooms, courses, ride prices and fuel constants. The UI labels these and asks users to verify official information with their university.
- Nothing leaves the device. Photos are resized locally and stored as data URLs in `localStorage` (about 5 MB per site) — the app warns when storage is full; a production system needs object storage.
- Contact numbers are masked until a ride request is accepted; finders and claimants are told to meet at the Security Office or another public place.
- The role switcher is a demo convenience and the permission checks run in the browser, so they protect nothing against a determined user; the hashed answers can be brute-forced offline if exported. Both are flagged as student TODOs.
- The language filter is a heuristic that will miss some abuse and occasionally flag innocent text; moderators make the final call and every decision is in the audit log. Safety-related feedback can be escalated to the relevant committee, and the app never contacts anyone by itself.

## Evaluation ideas for the FYP report

- **Matching quality:** label 50–100 lost/found pairs from a real campus WhatsApp group, report precision/recall at the *likely* and *possible* thresholds, and run an ablation of each signal (category, text, place, date).
- **Claim security:** measure how many guesses common answers take, compare 1, 2 and 3 questions, and discuss server-side rate limiting.
- **Usability:** System Usability Scale with 10–15 students per role, task times for "post a lost item", "claim an item" and "find a free room", English versus Urdu.
- **Moderation:** precision/recall of the filter on a hand-labelled set of English, Urdu and Roman Urdu messages; time-to-decision from the audit log.
- **Operations:** return rate and median days to return before and after a pilot; share of notices read within 48 hours from read receipts; room utilisation from the matrix.

## Verification

- Behaviour test (Playwright) drives every module's core flow through the UI and state — 79 checks: SHA-256 fallback equals WebCrypto, normalisation, filter levels, matching bands, wrong and right claims, finder approval, handover with roll check, posting with leak and phone checks, notices audience and read receipts, waitlist promotion, `.ics` stamps and folding, venue clash, timetable clashes and room double-booking, joins and the single-president rule, women-only and contact reveal, anonymous feedback and redaction, auto-hide and role-scoped queues, audit CSV, search, reports, theme, export, demo toggle, Urdu RTL on every page, tour, shortcuts, missing-translation detection and persistence — all pass with zero console errors.
- Harness screenshots at 1366 × 850 and 390 × 844 for 25+ states (every module, each role, Urdu, dark theme, tour, modals): no console errors and no horizontal overflow.
