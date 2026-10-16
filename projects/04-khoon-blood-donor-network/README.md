# 04 · Khoon — Blood Donor Network (idea #12)

**Find a compatible donor in minutes, not in a WhatsApp storm.** A crimson-and-cream single-file app: compatibility-aware donor search across nine Pakistani cities (with a haversine nearest sort), an emergency request board with a full lifecycle, pre-donation screening with deferral rules, donation-camp organiser, thalassemia rota, blood-component education, analytics, an in-app inbox, settings with backup, and an onboarding tour. English / اردو throughout.

**Scale:** 4,330 lines in one `index.html` · 13 routed modules (+ tour, shortcuts, modal/print helpers) · 258 seed records (75 donors, 9 live requests, 96 historic requests, 19 blood banks, 3 camps with 53 registrations, 4 rota patients, 2 inbox messages) · 477 bilingual strings.

## Getting started

Open `index.html` in any modern browser. Works offline from `file://`; only Google Fonts load from the network. Serve over HTTP/HTTPS (`npx serve .`) if you want the **Use my location** button to work — browsers block geolocation on `file://`, and the app falls back to the selected city or area centroid.

All data lives in `localStorage` under the `khoon:` prefix (`me`, `requests`, `camps`, `rota`, `screenings`, `inbox`, `settings`, `find`, `geo`, `tour`, `lang`, `reveals`). **Settings → Export backup** downloads everything as JSON; **Import backup** restores it; **Reset everything** clears the prefix and reloads. Turn **Show demo data** off to hide every seeded record once you connect a backend.

## The problem

When a patient needs blood in Pakistan, relatives broadcast on WhatsApp and hope. Thalassemia children need transfusions every few weeks for life. Donors do not know when they are eligible again, families do not know who has already been contacted, and nobody can see where demand outstrips supply.

## What is built

| Area | Features |
|---|---|
| Hero search | Blood-group chips (8 groups + Any), city select, jump to results; bell with unread count; shortcuts button; language toggle |
| Find donors | Area filter, "eligible now only", "include compatible groups" (ABO/Rh matrix), sort by availability, donation count or **nearest** (haversine from GPS or the chosen city/area centroid, distance pills), donor cards with eligibility countdown, badges, masked phone with reveal → call / WhatsApp |
| Requests | Post with validation; board sorted by status → urgency → recency; status filter and "show closed"; **lifecycle** open → donors contacted → arranged → fulfilled / expired with step indicator; **timeline** modal (status changes, responses, notes, first-response time); add notes; reopen; **auto-expire** 12 h after the needed-by date (inbox alert for own requests); matching eligible donors count; "I can donate" responses; WhatsApp share text |
| My donor card | Registration with validation (03xx-xxxxxxx, 18–60 y, ≥ 50 kg, consent, health notes), printable digital card, eligibility ring (90 d men / 120 d women), log a donation, donation history, badges, today's screening status, **printable donor certificate** with verification code |
| Screening | 12-question pre-donation questionnaire with hints + optional vitals (Hb, weight, systolic BP, age); rule engine → eligible / temporary deferral (earliest date) / permanent deferral, with advisories (aspirin → platelets only); interval question pre-filled from the donor card; screening log (last 20) and inbox note |
| Camps | Create camps (venue, city, date, hours, slots, organiser); upcoming/completed tabs; register donors (manual or "Register me") with slot capacity and duplicate-phone check; check-in list with checked-in / donated / deferred toggles; live results (units, slot fill, deferral rate, no-shows, units by group); close / reopen; printable camp sheet; delete |
| Thalassemia rota | Patients with monthly unit needs and transfusion day; month calendar coloured by coverage; assignment modal listing compatible donors in the city who will be eligible on that date (one donation per donor per month); "Assign me"; coverage bars for the next 3 months; inbox reminder when you are assigned |
| Components | Education cards for whole blood, red cells, plasma, platelets, cryoprecipitate (shelf life, interval, time, uses); three compatibility tables — red cells, **plasma** (ABO reversed, Rh ignored), **platelets** (ABO-identical preferred, acceptable-in-shortage grade) — with recipient-row highlight |
| Blood banks | 19 seed centres (government / private / NGO) with 24/7, apheresis and thalassemia tags, call and Maps links, city filter |
| Stats | Animated KPIs, donut + bars of donors by group, coverage by city (eligible / registered), red-cell matrix |
| Analytics | 90-day seeded history + live board: demand vs supply by group (grouped SVG bars), by city (table with ratio bars), time-to-first-response histogram, outcome by urgency, weekday × hour heatmap, KPIs (requests, fulfilment %, median response, peak hour, largest gap) |
| Inbox | In-app notifications with read state: welcome, critical requests in my city, eligibility reminder, responses to my requests, expiries, screening results, camp and rota confirmations; filters, mark read / all, clear |
| Settings | Language, demo-data toggle, default city, reminder toggles, JSON export / import, reset, storage usage, replay tour, shortcuts sheet |
| Tour & shortcuts | 5-step spotlight tour on first open (replayable); `1–9, 0` tabs, `L` language, `/` area search, `I` inbox, `?` sheet, `Esc` close |

## Architecture (single file)

```
index.html
├─ <style>         tokens · base · Urdu mode · hero · forms · tabs · cards · donors · requests + timeline
│                  · donor card + certificate · banks · stats/charts (bars, heatmap, hist) · camps · screening
│                  · rota calendar · components · inbox · settings · modal/toast/tour · responsive · print · motion kit
├─ <svg sprite>    56 icon symbols used via ic(name)
├─ <header.hero>   brand, bell, shortcuts, language, headline, search bar
├─ <nav.tabs>      13 tabs with live counters (open requests, unread inbox, upcoming camps)
├─ <main>          13 <section data-page> blocks toggled by go()
├─ overlays        #scrim/#modal, #tour spotlight, #toasts, #printArea
└─ <script>
   ├─ utils         store (get/set/remove/keys), esc, toast, animateIn, countUp, rng, time/format helpers, downloadText
   ├─ i18n          L.en / L.ur (477 keys each), t()
   ├─ domain        GROUPS, CAN_RECEIVE, PLASMA_ABO/plasmaOk, plateletGrade, INTERVAL, eligibleIn/eligibleOn,
   │                CITIES, CITY_GEO, AREA_GEO, haversine, DEFERRAL_RULES
   ├─ seed          SEED_DONORS (75), SEED_REQUESTS (9, with history), BANKS (19), SEED_HISTORY (96), SEED_CAMPS (3), SEED_ROTA (4)
   ├─ state         settings(), allDonors(), allRequests()/requests()/mutateRequest(), camps()/mutateCamp(), rota()/mutatePatient(),
   │                inbox()/notify()/updateBell(), request lifecycle (STATUS_FLOW, setStatus, expireRequests)
   ├─ modal/print   openModal(), closeModal(), printFragment()
   ├─ find          renderHeroSearch(), renderFind(), filterDonors(), sortDonors(), donorCard(), drawDonors(), revealContact(), requestLocation()
   ├─ requests      requestForm(), submitRequest(), requestCard(), lifecycleSteps(), renderRequests(), bindRequestActions(), openTimeline()
   ├─ register      renderRegister(), myCardHtml(), eligibilityRing(), registerForm(), saveProfile(), certificateHtml(), openCertificate()
   ├─ screening     evaluateScreening(), verdictHtml(), renderScreening(), saveScreening()
   ├─ camps         renderCamps(), openCampForm(), createCamp(), renderCampDetail(), registerForCamp(), campStats(), campSheetHtml()
   ├─ rota          rotaCalendar(), rotaCoverage(), renderRota(), openPatientForm(), rotaCandidates(), openAssign(), assignDonor()
   ├─ components    componentCard(), compatGrade(), compatTable(), renderComponents()
   ├─ banks/stats   renderBanks(), renderStats(), groupDonut(), cityCoverage()
   ├─ analytics     analyticsRequests(), demandSupplyByGroup(), groupedBarChart(), cityDemandTable(), responseHistogram(), weekHourHeatmap(), renderAnalytics()
   ├─ inbox         renderInbox(), markRead(), bootNotifications()
   ├─ settings      renderSettings(), exportData(), importData()
   ├─ tour/keys     startTour(), showTourStep(), openShortcuts(), keydown handler
   ├─ about         TODOS, renderAbout()
   └─ nav/boot      PAGES, renderTabs(), go(), applyLang(), boot sequence
```

## Module map

| Module | Route | Key functions | Storage keys |
|---|---|---|---|
| Find donors | `#find` | `renderFind`, `drawDonors`, `referencePoint`, `haversine`, `revealContact` | `find`, `geo`, `reveals` |
| Requests + lifecycle | `#requests` | `submitRequest`, `setStatus`, `expireRequests`, `openTimeline`, `mutateRequest` | `requests` |
| My donor card | `#register` | `renderRegister`, `saveProfile`, `openCertificate`, `certCode` | `me` |
| Screening | `#screening` | `evaluateScreening`, `saveScreening`, `DEFERRAL_RULES` | `screenings` |
| Camps | `#camps` | `createCamp`, `registerForCamp`, `campStats`, `campSheetHtml` | `camps` |
| Thalassemia rota | `#rota` | `rotaCandidates`, `assignDonor`, `rotaCoverage`, `eligibleOn` | `rota` |
| Components | `#components` | `plasmaOk`, `plateletGrade`, `compatTable` | — |
| Blood banks | `#banks` | `renderBanks` | — |
| Stats | `#stats` | `renderStats`, `groupDonut`, `cityCoverage` | — |
| Analytics | `#analytics` | `analyticsRequests`, `groupedBarChart`, `weekHourHeatmap`, `responseHistogram` | — (reads `requests`) |
| Inbox | `#inbox` | `notify`, `renderInbox`, `bootNotifications`, `updateBell` | `inbox` |
| Settings | `#settings` | `exportData`, `importData`, `saveSettings` | `settings`, `lang` (+ all keys for backup) |
| Tour / shortcuts | overlay | `startTour`, `showTourStep`, `openShortcuts` | `tour` |

## Domain logic

- **Red cells:** recipient → allowed donor groups follow ABO/Rh compatibility (`CAN_RECEIVE`). **Plasma:** antibodies live in plasma, so the ABO rule reverses (AB plasma is universal, O recipients accept all) and Rh is ignored (`PLASMA_ABO`). **Platelets:** ABO-identical preferred; other ABO acceptable in shortage; Rh-negative recipients should get Rh-negative units (`plateletGrade`). Tables are educational — the blood bank SOP is final.
- **Eligibility:** days since last donation ≥ 90 (men) or 120 (women) for whole blood; `eligibleOn(donor, date)` is used by the rota to check a future date.
- **Screening:** each triggered rule carries a deferral length (fever 14 d, tattoo/piercing 180 d, malaria-risk travel 90 d, dental 3 d, antibiotics 7 d, pregnancy/delivery 180 d, vaccine 14 d, surgery/transfusion 180 d; hepatitis/HIV/syphilis permanent; Hb < 12.5 g/dL 30 d; systolic BP outside 100–160 recheck in 7 d; under 18 until 18th birthday; over 60 permanent). Aspirin and poor sleep are advisories. The verdict is the longest deferral, or permanent if any permanent rule fires.
- **Request lifecycle:** `open → contacted → arranged → fulfilled`; anything not fulfilled 12 h after its needed-by date becomes `expired` on load. Every transition is appended to `history`; notes go to `notesLog`.
- **Nearest donors:** great-circle distance from the saved GPS fix or from the city/area centroid tables (`CITY_GEO`, `AREA_GEO`, approximate).
- **Analytics:** `SEED_HISTORY` is 96 deterministic closed requests over 90 days with weekday/hour weighting; live requests are merged on top. Supply is "eligible donors of that exact group right now".

## Left for students

| Extension | Difficulty | Start here |
|---|---|---|
| Backend + real-time sync (Firebase/Supabase/Node) so a request reaches every compatible donor instantly | ★★★ | `allRequests()` |
| OTP phone verification, CNIC duplicate prevention, poster-only status changes, "verified by blood bank" | ★★ | `saveProfile()` |
| Push/SMS to compatible eligible donors on post; escalate to blood banks after N hours | ★★ | `submitRequest()` |
| IP-based city fallback when GPS is denied; anonymised donor-density map | ★★ | `requestLocation()` |
| Privacy: call/SMS relay, quiet hours, max contacts per month, reveal audit | ★★ | `revealContact()` |
| Blood-bank stock levels per group and bank-side fulfilment confirmation | ★★ | `renderBanks()` |
| Camp slot times (20-minute windows) with SMS reminders the evening before | ★ | `bindCampDetail()` |
| Rota reminders two days before transfusion, with a decline flow that reopens the slot | ★ | `assignDonor()` |

Search the source for `TODO(student)` — there is one marker per row above.

## Data and safety notes

Donors, requests, camps, rota patients, response times and blood-bank phone numbers are illustrative seed data generated deterministically; verify every number before real use. Blood-group distribution follows approximate Pakistani frequencies (B+ 32 %, O+ 30 %, A+ 20 %, AB+ 8 %, negatives < 10 %). The screening module is a self-check that mirrors common donor-selection guidance; it never replaces the blood bank's own questionnaire and haemoglobin test. Nothing leaves the device until a backend is connected.
