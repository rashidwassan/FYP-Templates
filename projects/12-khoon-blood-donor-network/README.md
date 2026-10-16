# 12 · Khoon — Blood Donor Network

**Find a compatible donor in minutes, not in a WhatsApp storm.** A crimson-and-cream single-file app: compatibility-aware donor search across nine Pakistani cities, an emergency request board, donor registration with eligibility tracking and badges, a blood bank directory and live stats. English / اردو.

Open `index.html`. Works offline; only Google Fonts load from the network.

## The problem

When a patient needs blood in Pakistan, relatives broadcast on WhatsApp and hope. Thalassemia children need transfusions every few weeks for life. There is no shared, compatibility-aware view of who can donate, where, and when they are eligible again.

## What is built

| Area | Features |
|---|---|
| Hero search | Blood-group chips (8 groups + Any), city select, jump to results |
| Find donors | Area filter, "eligible now only", "include compatible groups" (ABO/Rh matrix), sort by availability or donation count, donor cards with eligibility countdown, donation badges, masked phone with reveal → call / WhatsApp deep link, compatibility explainer |
| Requests | Emergency board sorted by status → urgency → recency, urgency colour rail, matching eligible donors in city, "I can donate" responses (requires a donor profile), WhatsApp share text, call, mark fulfilled / reopen, post a request with validation |
| My donor card | Registration with validation (03xx-xxxxxxx, age 18–60, weight ≥ 50 kg, consent, health notes), printable digital donor card, eligibility ring (90 days men / 120 days women), log a donation, donation history, badges (First Drop, Regular, Hero, Lifesaver) |
| Blood banks | 19 seed centres (government / private / NGO) with 24/7, apheresis and thalassemia-centre tags, call and Maps links, city filter |
| Stats | KPIs, donut + bars of donors by group, coverage by city (eligible / registered), full recipient × donor compatibility matrix |

## Architecture (single file)

```
index.html
├─ <style>         crimson/cream tokens, hero, tabs, donor/request cards, donor card, matrix, print
├─ <header.hero>   brand, headline, search bar
├─ <nav.tabs>      six tabs with live counters
├─ <main>          six <section data-page> blocks toggled by go()
└─ <script>
   ├─ utils         store, esc, toast, seeded RNG, time helpers, maskPhone, wa()
   ├─ i18n          L.en / L.ur
   ├─ domain        GROUPS, CAN_RECEIVE matrix, INTERVAL, eligibleIn(), CITIES/areas
   ├─ seed          SEED_DONORS (75, deterministic), SEED_REQUESTS, BANKS
   ├─ find          renderFind(), drawDonors()
   ├─ requests      renderRequests() (post, respond, share, fulfil)
   ├─ register      renderRegister() (form ↔ card), badges, donation log
   ├─ banks/stats   renderBanks(), renderStats()
   └─ nav           renderTabs(), go(), applyLang()
```

Domain rules: recipient → allowed donor groups follow red-cell ABO/Rh compatibility (`CAN_RECEIVE`). Eligibility = days since last donation ≥ 90 (men) or 120 (women); the blood bank's own screening is always final.

## Left for students

| Extension | Difficulty | Start here |
|---|---|---|
| Backend + real-time (Firebase/Supabase/Node) so requests reach every compatible donor instantly | ★★★ | `allDonors()`, `requests()` |
| OTP phone verification, duplicate prevention, "verified by blood bank" flow | ★★ | `#pSave` handler |
| Push/SMS notifications to compatible eligible donors; eligibility reminders | ★★ | `#qGo` handler |
| GPS nearest-donor sort and anonymised density map | ★★ | `drawDonors()` |
| Privacy: call/SMS relay, quiet hours, reveal audit | ★★ | `[data-reveal]` handler |
| Hospital integration: stock levels, auto-close after needed-by date | ★★ | `renderBanks()` |
| Recurring thalassemia donor rota linked to the Thalassemia Care Companion | ★★ | `renderRequests()` |

Search the source for `TODO(student)`.

## Data notes

Donors, requests and blood-bank phone numbers are illustrative seed data generated deterministically; verify every number before real use. Blood-group distribution follows approximate Pakistani frequencies (B+ 32 %, O+ 30 %, A+ 20 %, AB+ 8 %, negatives < 10 %).
