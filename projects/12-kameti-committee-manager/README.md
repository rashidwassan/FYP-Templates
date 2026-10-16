# 12 · Kameti Committee Manager (idea #43)

**Every rupee, every month, on record.** A bilingual (English / اردو) register for rotating savings committees (kameti / beesi / ROSCA): a multi-committee dashboard with a cash-flow calendar, a tap-to-update payments matrix with grace and penalty rules, lottery and need-based payout ordering, a default simulator, dispute log, receipts with a QR-style verification code, cross-committee trust scores, a kameti-vs-bank-vs-inflation planner, and printable agreements, statements and reports.

**Scale:** 3,971 lines in one `index.html` · 14 modules · seed data: 4 committees, 35 memberships (31 unique people), 171 payment cells, 17 payouts, 4 need requests, 3 disputes, 11 activity-log entries.

## Getting started

Open `index.html` in any modern browser. No build step, no server, no dependencies beyond Google Fonts (IBM Plex Sans / Mono and Noto Nastaliq Urdu; falls back to system fonts offline). Serving over HTTP is only needed if you later add camera uploads or notifications.

- **Where data lives:** `localStorage` under the `kameti:` prefix — `db` (committees), `settings`, `log`, `inbox`, `planner`, `lang`.
- **Backup / move devices:** Settings → *Export everything (JSON)*, then *Import JSON* on the other device. CSV export of any committee's matrix is on the Payments tab.
- **Reset:** Settings → *Reset demo data* restores the four seeded committees; *Erase all data* wipes every `kameti:` key.
- **Organizer lock:** Settings → *Set PIN* (4–6 digits, stored as a hash on the device); optional auto-lock after inactivity; `Shift+L` locks immediately.

## The problem

Millions of Pakistani households save through informal rotating committees. They run on memory, paper and WhatsApp, so disputes over who paid, who is next, late payments and "I gave you cash on the 4th" are common — and there is no legal protection when things go wrong. The organizer needs a register that every member can read, that prints, and that makes the rules (order, grace, fees, default handling) explicit before the first rupee changes hands.

## What is built

| Area | Features |
|---|---|
| Dashboard | KPIs across all committees (running, unique people, collected to date, outstanding, open disputes); committee cards with cycle ring, this-month progress and next payout; **cash-flow calendar** (collection days in, payout days out, month navigation, per-month totals, event list); six-month expected-vs-collected bar chart; recent activity |
| Overview | Pot hero with progress ring, recipient, due/overdue/grace pill and WhatsApp "remind unpaid"; KPI strip; collection-rate and cumulative in/out line charts; on-time rate per member (grace-aware); "needs attention" list; committee terms incl. grace days and fee cap |
| Members & order | Member table (masked CNIC, slots, turns, trust ring → profile, this-month status, statement); payout order with drag-and-drop and arrows, paid-out slots locked; **lottery draw** with animated shuffle; **need requests** (reason, urgency, amount, needed-by) with *Rank by need* proposing a new order for the unpaid slots |
| Payments | Cycle × member matrix: click cycles unpaid → paid → late → partial → waived, long-press/right-click opens the editor (amount, date, note, settle balance, receipt, raise dispute); paying after the grace period is recorded as late automatically; partial cells show balance; disputed cells carry a red dot; per-cycle stats, mark-all-paid, WhatsApp/SMS reminder sheet, CSV export |
| Schedule | Countdown to the next payout, timeline of every cycle with record/undo payout and payout acknowledgement; **default simulator** (shortfall per remaining cycle; split / organizer covers / swap turn to the end) |
| Disputes | Dispute log per committee: type, details, status (open → under review → resolved/rejected) with append-only history, filters, detail sheet, printable dispute report |
| People | Directory across committees keyed by phone number: trust score, outcome strip, sparkline trust trend, paid/received totals; profile sheet with trust-trend chart, outcome breakdown, committees and full payment history |
| Planner | What-if: monthly amount, months, your turn, bank rate, inflation → nominal pot, real value today, bank balance at end, real gain, advantage vs bank; cumulative position chart; "who benefits by turn" bar chart and table |
| Reports | Monthly collection report, full ledger, payout register, all member statements (page breaks), dispute log — previewed in-page and printed with dedicated print CSS |
| Receipts | Payment receipts and payout acknowledgements with receipt numbers and a deterministic **QR-style code drawn on canvas** from the receipt hash; print and share on WhatsApp |
| Agreement | Bilingual (English + Urdu) printable agreement generated from the terms, including grace, fee cap, need-based clause and signature lines; FAQ on how kametis work and their risks |
| Inbox & activity | Bell with unread badge; notifications for overdue cycles, payouts due within N days, stale disputes, committees starting next month or completed (deduplicated); append-only activity log of every mutation |
| Settings | Language, light/dark/system theme, reduce motion, compact numbers, default grace days, reminder lead, organizer PIN + auto-lock, storage usage, JSON export/import, demo reset, erase all, replay tour, keyboard shortcuts |
| Onboarding | Dismissible banner → six-step spotlight tour over the switcher, pot, tabs, inbox, settings and new-committee button |
| i18n & a11y | Full English/Urdu dictionary (`TXT.en` / `TXT.ur`), Nastaliq font with RTL layout, `aria-label`s on icon buttons, focus rings, keyboard-closable dialogs, shortcuts `1–9`, `I`, `N`, `L`, `Shift+L`, `Esc` |

## Module map

| Module | Key functions | Storage keys |
|---|---|---|
| Dashboard & calendar | `renderDashboard`, `dashboardTotals`, `committeeCard`, `renderCalendar`, `monthEvents`, `renderInflowChart` | `kameti:db` |
| Overview | `renderOverview`, `overviewCharts`, `ontimeBars`, `alertsCard`, `termsCard`, `monthStats` | `kameti:db` |
| Members & payout order | `renderMembers`, `memberRow`, `orderItem`, `wireOrderList`, `syncOrder`, `memberForm`, `lotteryModal`, `lotteryDraw`, `applyLottery` | `kameti:db` |
| Need-based payout | `needsCard`, `needForm`, `needScore`, `rankByNeed`, `needRankModal` | `kameti:db` (`committee.needs`) |
| Payments matrix | `renderPayments`, `matrixRow`, `wirePayments`, `setStatus`, `markAllPaid`, `cellEditor`, `lateFeeFor`, `paidAfterGrace`, `exportCsv` | `kameti:db` (`committee.payments`) |
| Receipts | `receiptModal`, `receiptData`, `receiptNo`, `drawQR`, `hash32`, `hashHex` | `kameti:db` (`payment.rcpt`) |
| Schedule & simulator | `renderSchedule`, `timelineItem`, `recordPayout`, `simulate`, `swapToEnd` | `kameti:db` (`committee.payouts`) |
| Disputes | `renderDisputes`, `disputeForm`, `disputeDetail`, `updateDispute`, `disputesFor`, `openDisputes` | `kameti:db` (`committee.disputes`) |
| Trust & people | `outcomes`, `scoreFrom`, `trustScore`, `trustHistory`, `allPeople`, `renderPeople`, `personCard`, `profileModal` | `kameti:db` (derived) |
| Planner | `renderPlanner`, `planMath`, `plannerResults` | `kameti:planner` |
| Reports & agreement | `renderReports`, `reportHTML`, `monthlyReport`, `ledgerReport`, `payoutRegister`, `statementsReport`, `disputeReport`, `agreementHTML`, `statementHTML`, `printEl` | `kameti:db` |
| Inbox & activity log | `scanAlerts`, `scanOverdue`, `scanPayouts`, `scanDisputes`, `notify`, `logEvent`, `renderDrawer`, `openNotification` | `kameti:inbox`, `kameti:log` |
| Settings & lock | `renderSettings`, `wireSettings`, `saveSettings`, `applyTheme`, `exportAll`, `importAll`, `resetDemo`, `eraseAll`, `pinSetup`, `lockApp`, `tryUnlock` | `kameti:settings`, `kameti:lang` |
| Onboarding tour | `renderBanner`, `startTour`, `showTourStep`, `endTour` | `kameti:settings` (`tourDone`, `bannerDismissed`) |

## Architecture (single file)

```
index.html
├─ <style>      tokens (forest / mint / gold) + dark-theme overrides, motion kit, components,
│               matrix, charts, calendar, drawer, lock screen, tour, print CSS
├─ <svg>        inline icon sprite (68 symbols, 24×24 stroke icons)
├─ <header>     brand, committee switcher, new committee, inbox bell, lock, settings, language
├─ <main>       #pot hero · #banner · #tabs · #view (one render function per route)
├─ overlays     #modal/#sheet, #drawer (inbox), #pop (cell editor), #lock, #tourHi/#tourCard, #toasts, #printRoot
└─ <script>
   ├─ 1. utils        $, store (namespaced localStorage), esc, rs/rsFull/kfmt, dates, hash32, seeded RNG
   ├─ 2. i18n         TXT.en / TXT.ur, t(), tf(), tEn()
   ├─ 3. settings     DEFAULT_SETTINGS, saveSettings, applyTheme
   ├─ 4. inbox + log  logEvent, notify, scanAlerts, renderBell
   ├─ 5. model        syncOrder, currentCycle, cycleState, lateFeeFor, monthStats, outcomes/trust, needScore,
   │                  rankByNeed, allPeople, monthEvents
   ├─ 6. seed data    demoData → seedOffice, seedMohalla, seedFamily, seedUni, seedLog
   ├─ 7. charts       lineChart, barChart, sparkline, hbars (hand-drawn SVG)
   ├─ 8. pot + banner renderPot, duePill, renderBanner
   ├─ 9. views        dashboard, overview, members, payments, schedule, disputes, people, planner,
   │                  reports, agreement, settings, about
   ├─ 10. modals      committee/member forms, lottery, need, dispute, receipt (canvas QR), statement,
   │                  profile, cell editor, reminders, PIN lock, tour, inbox drawer
   └─ 11. router      hash routes #dashboard … #about (+ #settings), tabs ink, shortcuts, init
```

Data flow: a committee is `{ members, order, payments, payouts, needs, disputes, terms }`. `order` has one entry per cycle (a member with two slots appears twice). `monthStats(c, i)` derives everything shown for a cycle from the `payments` map, so one tap on a cell updates the pot, KPIs, charts, calendar, trust scores, inbox and reports. `save()` persists and re-runs `scanAlerts()`.

## Domain logic notes

- **Cycle state:** `done` (payout recorded) · `current` (this month) · `pending` (past month, payout not recorded) · `upcoming`.
- **Grace & penalties:** a payment dated after `dueDate + graceDays` is recorded as *late* automatically (when a late-fee rule exists). Flat fees apply per late payment; percentage fees apply per week (or part) after the grace period, optionally capped by `feeCap`. Fees collected in a cycle are added to that cycle's payout.
- **Trust score (0–100):** every due cell of every membership with the same phone number is an outcome (on-time 1.0, late 0.5, partial 0.4, missed 0) with Bayesian smoothing (`k = 2`, prior 0.7), so new members start near 70. `trustHistory` replays outcomes in due-date order for the trend chart.
- **Need ranking:** `score = urgency × 2 + months waiting + trust ÷ 25`; members who already received the pot drop to the end; paid-out slots never move.
- **Default simulator:** shortfall per remaining cycle = expected − already paid; options are split (per other member), organizer fronts the total, or move the defaulter's remaining turns to the last cycles.
- **Planner:** real value = pot ÷ (1 + inflation/12)^turn; real cost = Σ contribution discounted monthly; bank path = annuity future value at the savings rate; *advantage vs bank* = pot compounded from the turn month to the end minus the bank-only balance. Early turns show positive advantage (interest-free loan), late turns negative (lending to the group).
- **Receipts:** receipt number `KM-<committee initials>-<cycle>-<member>`; the 25×25 QR-style pattern is seeded from an FNV-1a hash of `no|committee|member|amount|date|status`, so identical data draws an identical pattern. It is a visual verification code, not a scannable QR.
- **PIN:** stored as an FNV hash of a salted string — enough to keep casual eyes off a phone, not a cryptographic protection (see the student extension on sync and logins).

## Left for students

| Extension | Difficulty | Start here |
|---|---|---|
| Multi-device sync and member logins (Supabase / Firebase / own API) with offline cache and conflict resolution | ★★★ | `syncToBackend()` |
| OTP-confirmed payment acknowledgements so both sides agree a payment happened | ★★ | `requestAck()` |
| Raast / JazzCash / Easypaisa payment links with gateway callbacks that auto-fill the matrix | ★★★ | `reconcile()` |
| E-signatures on the agreement (SHA-256 hash, canvas or OTP signature per member) | ★★ | `signAgreement()` |
| Evidence uploads on disputes (resized receipt photos in the dispute history) | ★★ | `attachEvidence()` |
| Portable trust score as a signed, verifiable outcome history | ★★★ | `trustScore()` |
| SMS reminders from a server-side scheduler on the collection day | ★ | `sendSms()` |
| Live inflation and bank rates (SBP / PBS) in the planner with last-synced stamp and offline fallback | ★ | `fetchInflation()` |

Search the source for `TODO(student)` to find the exact hooks; the About tab lists the same eight extensions.

## Data & safety notes

All seed people, phone numbers and committees are fictional. Only the last four CNIC digits are ever stored and they are shown masked. A kameti has no statutory protection: the app records and prints, but the agreement's clauses, the dispute log and the receipts are what members rely on — keep signed paper copies with the organizer.
