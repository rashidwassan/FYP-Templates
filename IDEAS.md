# FYP Ideas Backlog — Single-File HTML Projects

Every idea below is scoped to be built as **one self-contained HTML file** (HTML + CSS + JS).
Ground rules for the build phase:

- Works offline: state lives in `localStorage`, data ships as seed/mock JSON inside the file.
- Live APIs (weather, AQI, exchange rates, maps) are optional add-ons, never a hard dependency.
- CDN libraries are allowed where they earn their place: Chart.js, Leaflet, TensorFlow.js, Tesseract.js, jsPDF.
- Urdu / English toggle wherever the audience is the general public.
- Proposed file naming once shortlisted: `projects/NN-short-slug.html` (e.g. `projects/08-bijli-bill-decoder.html`).

**Difficulty:** ★ easy · ★★ medium · ★★★ hard (for a single student over one semester).

**How to shortlist:** reply with the numbers (e.g. `1, 8, 13, 42, 66, 70`).

---

## Part A — Pakistan-focused, problem-solving (1–50)

### Agriculture & Rural

**1. Kisan Mandi Rate Board** ★★
Problem: Farmers sell to arhtis (commission agents) without knowing rates in nearby mandis and lose a big share of margin.
Build: Daily rates for wheat, cotton, rice, sugarcane, onion, tomato across major mandis; trend charts; "best mandi within X km" after transport cost; Urdu toggle.

**2. Warabandi Irrigation Planner** ★★
Problem: Canal turns are fixed but farmers over- or under-irrigate, wasting the country's scarcest resource.
Build: Crop, sowing date, soil type, acreage, canal turn → irrigation calendar with depth per watering, rainfall adjustment, water-saving tips (bed planting, laser levelling).

**3. Khad & Spray Dose Calculator** ★
Problem: Over-application of urea/DAP burns money and soil; product labels are in English.
Build: Land in acre/kanal/marla/ghunta, crop stage, soil-test values → bag counts, spray mixing ratios, cost in PKR, pre-harvest safety interval. Urdu-first UI.

**4. Crop Doctor (offline leaf-disease detector)** ★★★
Problem: One extension officer serves thousands of farmers; disease spreads before diagnosis.
Build: TensorFlow.js image model for cotton leaf curl, wheat rust, rice blast, citrus canker; camera input; treatment card in Urdu; works offline after first load.

**5. Maweshi Health & Milk Ledger** ★★
Problem: Smallholder dairy farmers lose animals to FMD/HS because vaccination dates are never tracked; milk income goes unrecorded.
Build: Animal profiles (tag, breed, age), vaccination/deworming reminders, daily milk yield charts, heat/pregnancy calendar, sale records.

**6. Pest & Locust Sighting Map** ★★
Problem: Locust and whitefly outbreaks are reported too late; villages have no early warning.
Build: Leaflet map, report form with photo + GPS, weekly heatmap, auto-generated alert text for the village WhatsApp group, seed data from past outbreaks.

**7. Solar Tube-well Sizing Tool** ★★
Problem: Diesel tube-wells are ruinously expensive; solar vendors oversell systems.
Build: Pump HP, bore depth, daily hours → panel count, inverter size, PKR cost, payback vs diesel/WAPDA, seasonal sun-hours by district.

### Energy, Water & Utilities

**8. Bijli Bill Decoder & Estimator** ★★
Problem: Slab tariffs, FPA, taxes and the protected/unprotected distinction make bills unpredictable; crossing 200 units can double a bill.
Build: Appliance-based unit estimator, slab-by-slab breakdown, "units left before next slab" alert, DISCO comparison, meter-reading photo log.

**9. Load-shedding, UPS & Solar Planner** ★★
Problem: Households buy the wrong-size UPS, batteries and solar systems, and misjudge net-metering returns.
Build: Outage schedule + appliances → battery Ah, inverter watts, solar kW, net-metering export estimate, payback years, 5-year cost comparison charts.

**10. Karachi Paani — Water Tanker Price Board & Pooling** ★★
Problem: Tanker operators charge arbitrary prices; neighbours cannot coordinate shared deliveries.
Build: Area-wise tanker price log, fair-price index, neighbour pooling scheduler, hydrant locator, complaint text templates.

### Health

**11. Dengue Watch** ★★
Problem: Seasonal dengue outbreaks in Lahore, Rawalpindi and Karachi; breeding sites go unreported.
Build: Symptom triage, breeding-site reporter on a map, weekly case heatmap (seed data), prevention checklist, fever-day tracker with warning-sign alerts.

**12. Khoon — Blood Donor Network** ★★
Problem: Families run WhatsApp campaigns to find blood in emergencies; thalassemia children need transfusions every few weeks.
Build: Donor registry (blood group, city, last donation, eligibility countdown), emergency request board, blood-bank directory, donor badges.

**13. Thalassemia Care Companion** ★★
Problem: Pakistan carries one of the world's largest thalassemia burdens; patients lack transfusion and chelation tracking.
Build: Transfusion log, Hb/ferritin trend charts, chelation reminders, next-transfusion predictor, pre-marital screening awareness module.

**14. Bachon ki Vaccination (EPI) Card** ★
Problem: Paper EPI cards get lost; missed doses are common.
Build: Child profile, Pakistan EPI schedule generated from DOB, due/overdue status, printable card (jsPDF), Urdu reminders, multi-child support.

**15. Dawai — Generic Medicine Finder & Price Compare** ★★
Problem: Brand medicines cost several times more than generics; patients don't know the alternatives.
Build: Searchable formulary (seed dataset), brand → generic equivalents, price comparison, counterfeit-spotting checklist, dose reminders.

**16. LHW Field Visit Assistant** ★★★
Problem: Lady Health Workers record maternal and child visits on paper; danger signs get missed.
Build: Offline household register, antenatal visit scheduler, EDD calculator, danger-sign checklist with referral prompts, monthly summary export.

**17. Hepatitis B/C Risk & Treatment Tracker** ★
Problem: Pakistan has one of the highest Hepatitis C rates worldwide; awareness is low.
Build: Risk questionnaire (barber blades, reused syringes, dental), screening advice, treatment-adherence tracker, myth-busting quiz in Urdu.

**18. Sukoon — Urdu Mental Health Toolkit** ★★
Problem: Stigma and a severe shortage of counsellors; young people have nowhere private to turn.
Build: Mood journal, guided breathing/CBT exercises in Urdu, PHQ-9/GAD-7 self-screeners, crisis helpline directory, data never leaves the device.

**19. Garmi Alert — Heatwave Safety Advisor** ★
Problem: The 2015 Karachi heatwave killed over a thousand people; outdoor workers still lack guidance.
Build: Heat-index calculator, risk by age/occupation, hourly "safe to work outside" guide, hydration planner, cooling-centre map.

**20. Smog Meter — Lahore Air Quality Dashboard** ★★
Problem: Winter smog shuts schools and fills hospitals; citizens don't know when to mask up.
Build: AQI dashboard (seed/API), advisory by sensitivity group, mask/indoor guidance, 7-day trend, pollution-source explainer.

### Education & Skills

**21. Merit Calculator Pakistan** ★
Problem: Every university uses a different aggregate formula; students misjudge their chances.
Build: MDCAT, ECAT, NUST NET, FAST, GIKI, NED formulas; marks → aggregates, past-merit comparison chart, counselling tips.

**22. Urdu Parho — Adult Literacy Trainer** ★★
Problem: Roughly 40% of adults cannot read; existing Urdu apps target children.
Build: Letters → joined forms → words → sentences, Nastaliq rendering, audio via Web Speech, canvas tracing, real-life texts (bills, signboards, CNIC).

**23. Wazifa — Scholarship Finder** ★
Problem: HEC, PEEF, Ehsaas and foreign scholarships are scattered across dozens of sites.
Build: Filter by degree, province, income, deadline; eligibility quiz; reminder calendar; application checklist.

**24. Out-of-School Children Survey Tool** ★★
Problem: Over 25 million children are out of school; NGOs survey on paper.
Build: Household survey form, offline save, GPS pin, reasons dashboard, CSV export, enrolment follow-up tracker.

**25. Pakistan Sign Language (PSL) Learner** ★★★
Problem: The deaf community is isolated; hearing families rarely learn PSL.
Build: Animated/video flashcards, category lessons, quizzes, optional webcam gesture practice via TensorFlow.js hand-pose.

**26. Board Exam Revision Planner** ★★
Problem: Matric/FSc students cram; chapter weightage from past papers is unknown to them.
Build: Syllabus per board, chapter weightage heatmap (seed), spaced-repetition schedule, daily plan, progress ring.

**27. Virtual Science Lab for Government Schools** ★★★
Problem: Most government schools have no functioning lab.
Build: Canvas simulations (pendulum, Ohm's law circuit, titration, projectile motion), Urdu/English labels, worksheet mode with auto-check.

**28. CSS / PPSC / FPSC Prep Companion** ★
Problem: Competitive-exam aspirants need structured offline MCQ practice.
Build: MCQ bank by subject, timed mocks, weak-area analytics, Pakistan Affairs flashcards, syllabus tracker.

### Governance, Legal & Civic

**29. Haq — Legal Rights & FIR Guide** ★
Problem: Citizens don't know how to file an FIR or their rights at a police station.
Build: Step-by-step FIR process, rights cards in Urdu, complaint letter generator, common PPC sections explained, helpline list.

**30. Shikayat — Citizen Complaint Tracker** ★★
Problem: Potholes, garbage and broken streetlights go unreported or get lost.
Build: Photo + GPS report, category, status timeline, ward-level heatmap, auto-generated complaint text for Citizen Portal/WhatsApp.

**31. Driving Theory Test & Challan Guide** ★
Problem: Road deaths are high; licence theory material is scattered.
Build: Traffic-sign quiz (Punjab/Sindh), rules explainer, e-challan fine lookup, licence process checklist, Urdu mode.

**32. Property Transfer Cost Calculator** ★★
Problem: DC rate vs FBR value, stamp duty, CVT and 236C/236K taxes confuse buyers and invite fraud.
Build: Province, city, area, filer status → full cost breakdown, document checklist, red-flag guide.

**33. Wirasat — Inheritance Share Calculator** ★★
Problem: Inheritance disputes and denial of women's shares are widespread.
Build: Select heirs → shares under Islamic law as applied in Pakistan, with explanations; property split in PKR; printable summary; FAQ.

**34. NADRA Document Helper** ★
Problem: People make repeated trips for CNIC, B-form and passport because of missing documents.
Build: Service selector → required documents, fee, processing time, printable checklist, appointment reminder, FAQ.

**35. FBR Income Tax & Filer Guide** ★★
Problem: Salaried people overpay or stay non-filers out of fear.
Build: Current tax slabs, salary → monthly tax, filer vs non-filer withholding comparison, return checklist, deadline reminders.

**36. Zakat, Ushr & Fitrana Calculator** ★
Problem: Nisab across gold, cash, stock and crops is routinely miscalculated.
Build: Asset entry, nisab from editable gold/silver price, ushr for crops, fitrana, Hijri date, Urdu/English.

### Housing & Construction

**37. Tameer — Grey Structure Cost Estimator** ★★
Problem: Families building a house are quoted wildly different costs and overcharged for materials.
Build: Plot size in marla/kanal, floors, quality tier → bricks, cement, steel, sand, labour quantities; PKR prices by city; stage-wise budget; export.

**38. Rent Agreement Generator & Tenant Rights** ★
Problem: Informal rentals lead to disputes; tenants don't know their rights.
Build: Guided form → bilingual agreement, stamp-paper value guide, rights and notice-period explainer, rent-receipt log.

### Disaster & Climate

**39. Sailab — Flood Preparedness & Relief Map** ★★★
Problem: The 2022 floods displaced 33 million people; warnings never reached many villages.
Build: River gauge dashboard (seed), district flood-risk map, evacuation checklist, relief-camp locator, missing-person board, family plan generator.

**40. Zalzala — Earthquake Readiness Audit** ★
Problem: Northern Pakistan is high-seismic; homes are never audited.
Build: Seismic zone map, home safety self-audit with score, drill planner, emergency bag checklist, aftershock guidance.

**41. Shajar — Tree Plantation Tracker** ★★
Problem: Plantation drives plant millions of trees but survival is never tracked.
Build: Log trees with GPS/photo, species, survival check-ins, ward leaderboard, urban-heat explainer, CSV export.

**42. Kabari Rates & Recycling Guide** ★
Problem: Recycling is informal; households don't know scrap values or how to segregate.
Build: Scrap price board (plastic, copper, paper, e-waste), segregation guide, kabari pickup scheduler, household waste footprint.

### Finance & Livelihood

**43. Kameti (Committee) Manager** ★★
Problem: Rotating savings committees run on memory and paper; disputes are common.
Build: Members, contribution, payout order (manual/lottery), payment ledger, reminders, trust score, printable statement.

**44. Remittance Rate Compare** ★
Problem: Overseas Pakistanis lose money to hidden fees and poor rates.
Build: Rate + fee comparison across channels (seed/API), per-corridor view (UAE, KSA, UK, US), alerts, Roshan Digital Account explainer.

**45. Mazdoor Connect — Skilled Worker Directory** ★★
Problem: Finding a trustworthy plumber, electrician or mason relies on word of mouth.
Build: Worker profiles, skills, area, city rate card, ratings, job request board, Urdu interface.

**46. Karyana Khata — Shop Inventory & Udhaar Ledger** ★★
Problem: Small shopkeepers run credit on paper and lose money.
Build: Products, stock, low-stock alerts, customer credit ledger, WhatsApp reminder text, daily sales summary.

**47. Freelancer Finance Tracker** ★★
Problem: Pakistan's large freelancer base struggles with USD→PKR tracking, platform fees and taxes.
Build: Invoice log, fee calculator (Upwork/Fiverr/Payoneer), PKR conversion, monthly income charts, PSEB and tax checklist.

**48. Fair Fare — Rickshaw & Taxi Fare Estimator** ★
Problem: Fare haggling exploits newcomers and women.
Build: Distance via map, current fuel price, vehicle type → fair fare range, shareable fare card, ride-hailing comparison.

**49. Mehangai Tracker — Household Price Index** ★★
Problem: Essential item prices change weekly; families cannot budget.
Build: Weekly price log for atta, ghee, dal, sugar, petrol by city; personal basket inflation %; budget planner; charts.

### Safety & Mobility

**50. Mahfooz — Women's Safety Companion** ★★
Problem: Harassment in public transport and streets; helplines are little known.
Build: One-tap SOS message with location, trusted contacts, helpline directory, incident log, area safety reviews.

### Alternates (swap in if any of 1–50 is dropped)

- **A1. Metro & BRT Route Planner** ★★ — Lahore Metro/Orange Line, Karachi BRT, Peshawar BRT: route finder with interchanges, fares, timings, nearest station.
- **A2. Roman Urdu ↔ Urdu Script Transliterator** ★★ — Rule-based conversion with Nastaliq preview; solves the "no Urdu keyboard" problem.
- **A3. Overseas Job Scam Checker** ★ — Red-flag quiz, licensed promoter checklist, document verification guide, complaint path.
- **A4. Northern Areas Trip Planner** ★★ — Hunza/Skardu/Swat itineraries, PKR budget, altitude-sickness advisor, packing list, offline maps.
- **A5. Namaz Times, Qibla & Masjid Finder** ★ — Offline prayer-time formulas, device-orientation Qibla, saved masjid list.
- **A6. Regional Language Dictionary** ★★ — Sindhi/Pashto/Punjabi/Balochi ↔ Urdu/English with audio.
- **A7. Urdu Screen Reader & TTS** ★★ — Paste or scan text → spoken Urdu for visually impaired users.
- **A8. Road Accident Blackspot Map** ★★ — Crowdsourced accident reports, hazard heatmap, safer-route tips.
- **A9. Missing Persons & Lost Items Board** ★ — Edhi-style public board with photo, last seen, contact.
- **A10. Urdu OCR Scanner** ★★★ — Tesseract.js Urdu model, camera capture, editable text, export.

---

## Part B — Global ideas (51–65)

**51. Privacy-First Password Manager** ★★★
WebCrypto AES-GCM vault in the browser, master password, strength meter, generator, encrypted export/import.

**52. Algorithm Visualizer** ★★
Sorting, pathfinding (A*, Dijkstra, BFS/DFS), tree and graph traversals animated with step controls and code highlighting.

**53. Local-First Markdown Notes** ★★
Editor with live preview, tags, full-text search, backlinks, export; no server at all.

**54. Kanban Project Board** ★★
Drag-and-drop columns, WIP limits, due dates, burndown chart, JSON import/export.

**55. Group Expense Splitter** ★
Uneven splits, multi-currency, simplified settlement graph, shareable summary.

**56. ATS-Friendly Resume Builder** ★★
Form → templates, keyword match score against a pasted job description, PDF export.

**57. Carbon Footprint Calculator** ★
Transport, electricity, diet, shopping → tonnes CO₂/year, country comparison, reduction plan.

**58. Typing Tutor & Analytics** ★
Lessons, WPM and accuracy over time, weak-key heatmap, code-typing mode.

**59. Mind Map & Flowchart Editor** ★★★
Canvas/SVG nodes and connectors, auto-layout, undo/redo, PNG/SVG export.

**60. Client-Side Image Toolkit** ★★
Compress, resize, convert, crop, strip EXIF; nothing leaves the browser.

**61. Phishing URL & Fake News Analyzer** ★★
Heuristics plus a small TensorFlow.js text model, red-flag explanations, education mode.

**62. Web Accessibility Checker** ★★
Paste HTML → contrast, alt text, heading order, ARIA issues with suggested fixes.

**63. Habit Tracker with Insights** ★
Streaks, heatmap calendar, habit correlations, reminders, data export.

**64. Developer Playground** ★
JSON formatter, regex tester, cron explainer, JWT decoder, text diff — one offline page.

**65. Meal Planner & Calorie Tracker** ★★
Seed food database, macros, weekly plan, grocery list, BMI/TDEE.

---

## Part C — Games (66–85)

**66. Ludo** ★★ — Pakistan's favourite; vs AI or local multiplayer, fair dice, animations.
**67. Carrom Board** ★★★ — Striker and coin physics, two-player, queen rules.
**68. Gully Cricket** ★★ — Timing-based batting, bowling variations, PSL-style tournament, scoreboard.
**69. Basant Kite Pecha** ★★ — Wind physics, mouse/touch control, cut opponent strings.
**70. Rang (Court Piece) Cards** ★★★ — Four-player trick-taking vs AI, trump selection, team scoring.
**71. Pitthu Garam (Seven Stones)** ★★ — Ball-throw physics, stack rebuild, dodge mechanics.
**72. Dama (Checkers)** ★★ — Local rules, minimax AI, hints.
**73. Lafz — Urdu Wordle** ★ — Daily Urdu word, Nastaliq on-screen keyboard, share grid.
**74. Rickshaw Rush** ★★ — Endless runner dodging Karachi traffic, pick up passengers, upgrades.
**75. Truck Art Runner** ★★ — Platformer with truck-art visuals, levels across Pakistani cities.
**76. Chai Dhaba Tycoon** ★★ — Idle/business sim: menu, staff, prices, customer flow.
**77. Zameendar — Farm Strategy Sim** ★★★ — Seasons, crop choice, water, market prices; educational.
**78. Pakistan Studies Quiz Show** ★ — Buzzer-style quiz, lifelines, leaderboard.
**79. Snakes & Ladders: Landmarks Edition** ★ — Board of Pakistani landmarks with fun facts.
**80. Memory Match: Culture Cards** ★ — Truck art, provinces, foods, festivals.
**81. Chess with AI** ★★★ — Full rules, minimax with alpha-beta pruning, PGN export.
**82. Sudoku Generator & Solver** ★★ — Difficulty levels, hints, pencil marks.
**83. Tetris** ★★ — Hold piece, ghost piece, levels, scoring.
**84. Tower Defense** ★★★ — Waves, towers, upgrades, map editor.
**85. 8-Ball Pool** ★★★ — Cue physics, collisions, two-player.
