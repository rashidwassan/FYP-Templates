# 26 · Karyana Khata — Shop Inventory & Udhaar Ledger (idea #46)

**The red cloth-bound khata of a Pakistani grocery store, with the arithmetic done exactly.** An Urdu-first, mobile-first, single-file app for small karyana and general stores: a POS-style quick sale with loose-weight selling and 58 mm receipts, inventory with moving-average cost and first-expiry-first-out batches, an udhaar ledger with credit limits, ageing and polite WhatsApp/SMS reminders, suppliers and purchases with reorder suggestions from sales velocity, a daily cash book with a counted day close, reports with month-end profit and loss, and an owner PIN with a sales-only helper mode. اردو اور English دونوں میں، مکمل دائیں سے بائیں ترتیب کے ساتھ۔

**Scale:** 6,817 lines in one `index.html` (≈ 5,600 lines of JavaScript, ≈ 1,040 of CSS) · 17 routed modules plus tour, search palette, PIN pad, shortcuts and print layouts · 827 bilingual strings · 95 sprite icons · seed of 126 items (35 sold loose), 25 customers, 6 suppliers and a 60-day simulated history: ≈ 2,320 bills with ≈ 5,050 lines, ≈ 500 udhaar entries, 116 purchases, ≈ 120 stock adjustments, ≈ 110 cash entries and 59 day closes · 8 student TODOs.

## Getting started

- Open `index.html` in Chrome, Edge, Firefox or Safari. It works from `file://` and offline; only the Google Fonts (Hanken Grotesk, Noto Nastaliq Urdu) come from the network and fall back to system fonts.
- The app opens in **Urdu, right to left**. The button at the top switches to English (shortcut `L`). A spotlight tour runs on the first open (replay it from Settings or About).
- **Owner PIN for the demo: `1234`.** The app starts in owner mode; use the lock button to hand the counter to a helper.
- The camera barcode scanner needs a browser with `BarcodeDetector` (Chrome on Android, ChromeOS, macOS) and a secure origin; serve the folder (`npx serve .`) for camera access. Everywhere else, type the digits or use a USB/Bluetooth scanner in the quick-sale search box (it types the code and presses Enter).
- **Where data lives:** `localStorage` under the `kk:` prefix — `meta`, `settings`, `items`, `customers`, `suppliers`, `sales` (packed as compact arrays), `ledger`, `supLedger`, `purchases`, `cash`, `adjust`, `closes`, `activity`, `read`, plus `cart`, `lang` and `role`.
- **Backup and reset:** Settings → JSON export / import (import re-runs the books check), five CSV exports (udhaar ledger, supplier ledger, sales lines, stock list, cash book; UTF-8 with BOM so Urdu names open correctly in Excel), "Demo shop data" off for an empty khata, and "Reset everything".

## The problem

A neighbourhood karyana store runs on a paper khata. Udhaar is written by hand and slowly turns into bad debt nobody chases politely; stock is judged by eye so fast movers run out while dead stock ties up money; loose atta, cheeni and daal are sold as "Rs 100 worth"; the drawer is counted at night with no idea why it is short; and month-end profit is a guess. Generic POS apps are English-first, assume barcodes on everything and ignore udhaar. Karyana Khata keeps the shopkeeper's habits — a ruled page, *diye* (gave) and *liye* (got) — and does the bookkeeping exactly.

## What is built

| Area | Features |
|---|---|
| Today (home) | Sales, drawer cash, udhaar given/recovered and gross profit for today with animated KPIs; one-tap actions (sell, got payment, gave udhaar, expense, stock in, close day); 7-day sales chart; recent bills; top unread alerts; receivables with ageing; live "books balance" badge |
| Quick sale (POS) | Search (English, Urdu or barcode) and a grid of the 24 most-sold items, category chips; loose items open a keypad **by weight or by rupees** ("Rs 100 of cheeni" becomes 588 g), quick chips (250 g … 5 kg, half dozen, tray); steppers; discount in Rs or % (helpers capped); round-off to the rupee; payment by cash (tendered and change with note shortcuts), JazzCash/Easypaisa (reference) or udhaar (customer picker, balance after bill, credit-limit block with owner approval, partial cash now); cart survives a reload; F9 / Ctrl+Enter |
| Receipts and bills | 58 mm thermal receipt (print CSS with an injected `@page`), 32-column plain-text receipt for sharing (Web Share, WhatsApp to the udhaar customer, copy), bill history with range/method filters and search, reprint, **void** with reason (stock returned, udhaar reversed, refund in today's cash book) |
| Stock | Units kg / litre / dozen (sold loose) and packet / piece; sell price, average cost, margin, value at cost and at retail; reorder level, purchase pack, supplier, EAN-13/8 barcode with check-digit validation and duplicate check; filters (low, out, expiry, not selling), category and sort; item page with reorder maths shown step by step, expiry batches, price history and stock movements; adjustments for damage, expiry, own use and physical count |
| Udhaar khata | 25 customers with credit limits, balance and limit bar, risk flags (over limit, near limit, 90+ days, no payment for 30 days); ageing buckets 0–30 / 31–60 / 61–90 / 90+ (FIFO); ruled khata page with gave/got/running balance; got payment (cash or wallet), cash loan or old balance; three polite reminder templates in Urdu and English opened as `wa.me` and `sms:` links; printable statement for any period; write-off as bad debt; new customers with an opening balance from the paper khata |
| Suppliers and purchases | Supplier khata (bills, payments by cash/bank/wallet, running payable), lead time and visit cycle, items supplied; purchase entry that raises stock, recomputes average cost and adds dated batches; partial payment; purchase history |
| Reorder | Items that will run short before the next visit, grouped by supplier: stock, daily velocity, days of cover, reorder point and suggested whole packs; create a purchase from the ticked lines, send the order on WhatsApp, or print a purchase order |
| Cash book | Day by day: opening (yesterday's counted cash), every drawer movement with a running balance (cash sales folded per hour), expenses (rent, bijli, gas, salary, transport, chai-pani, repairs), owner drawings, bank deposits, cash added; wallet and bank totals beside the drawer; **day close** with a denomination counter (Rs 5000 … Rs 1 coins), shortage/over check, lock after close, reopen of the latest close, printable day sheet |
| Reports | Sales by day, week and hour; payment mix; top items by sales, profit or frequency; margin by category and item; dead stock and slow movers; udhaar outstanding trend (60 days) and weekly gave/got; best customers with pay ratio; **month-end profit and loss** with print layout; every chart is hand-coded SVG |
| Roles and log | Owner PIN (salted SHA-256, lockout after 5 tries), "ask for PIN on open", helper mode limited to quick sale, today's bills and About; one-off owner approval for an over-limit helper sale; activity log of every change with owner/helper marking, filters and CSV |
| Platform | Alerts inbox (out of stock, low stock, expiry, over-limit and 90+ customers, unpaid suppliers, unclosed day, backup reminder) with read state and bell badge; global search palette (pages, items, customers, suppliers, bill numbers); keyboard shortcuts sheet; settings (shop profile, language, text size, paper/night theme, reduced motion, 1,250,000 or 12,50,000 grouping, rounding, helper discount cap, near-expiry window, PIN, demo data, backups, storage meter); books check modal; About with the student TODO panel; accessibility basics (labels, focus rings, aria-live totals and results, keyboard-closable sheets, skip link) |

## Module map

| Module | Route | Key functions | Storage keys |
|---|---|---|---|
| Today | `#home` | `renderHome`, `todayNumbers`, `quickActions`, `weekChartHtml` | reads all |
| Quick sale | `#sale`, `#sale/<customer>` | `renderSale`, `addToCart`, `openQtyPad`, `padQty`, `openDiscount`, `openCheckout`, `checkCredit`, `completeSale`, `postSale`, `billTotals`, `splitPayment` | `cart`, `sales`, `items`, `ledger` |
| Receipts and bills | `#bills` | `receiptHtml`, `receiptText`, `printReceipt`, `openReceipt`, `openVoid`, `voidSaleCore`, `renderBills` | `sales` |
| Stock | `#stock`, `#item/<id>` | `renderStock`, `renderItem`, `openItemForm`, `saveItem`, `openAdjust`, `postAdjust`, `openScanner`, `eanValid`, `stockOut`, `stockIn`, `addBatch`, `takeBatches` | `items`, `adjust` |
| Udhaar khata | `#udhaar`, `#cust/<id>` | `renderUdhaar`, `renderCustomer`, `openEntry`, `addLedger`, `ageing`, `riskOf`, `openReminder`, `reminderText`, `statementHtml`, `openWriteOff`, `openCustomerForm` | `customers`, `ledger` |
| Suppliers | `#suppliers`, `#sup/<id>` | `renderSuppliers`, `renderSupplier`, `openSupPay`, `addSupEntry`, `openSupplierForm` | `suppliers`, `supLedger` |
| Purchases | `#purchase`, `#purchase/<item or supplier>` | `renderPurchaseForm`, `draftTotals`, `savePurchase`, `postPurchase` | `purchases`, `items`, `supLedger` |
| Reorder | `#reorder` | `salesMatrix`, `velocity`, `reorderPlan`, `reorderList`, `renderReorder`, `orderText` | derived |
| Cash book | `#cash`, `#cash/<YYYY-MM-DD>` | `cashMoves`, `openingFor`, `daySummary`, `closeDayCore`, `openCloseDay`, `reopenDay`, `openCashEntry`, `addCash`, `daySheetHtml` | `cash`, `closes` |
| Reports | `#reports/<tab>` | `barChart`, `lineChart`, `hbars`, `itemStats`, `receivableTrend`, `monthPL`, `plTableHtml`, `repOverview` … `repPL` | derived |
| Alerts | `#alerts` | `computeAlerts`, `unreadAlerts`, `markRead`, `renderAlerts` | `read` |
| Activity log | `#activity` | `logAct`, `actText`, `renderActivity` | `activity` |
| Settings and backup | `#settings`, `#settings/audit` | `renderSettings`, `exportJson`, `importJson`, `CSV_EXPORTS`, `toggleDemo`, `resetAll`, `openAudit`, `auditBooks` | `settings`, all |
| Roles | top bar lock | `setPin`, `checkPin`, `openPin`, `requireOwner`, `setRole`, `switchToHelper`, `bootPinGate` | `settings`, `role` |
| Search, tour, keys, about | overlay, `#about` | `openPalette`, `searchAll`, `startTour`, `showTourStep`, `openShortcuts`, `onKey`, `renderAbout` | `settings` (`tourDone`), `lang` |

## Architecture (single file)

```
index.html
├─ <head>          title, meta, Google Fonts (Hanken Grotesk + Noto Nastaliq Urdu)
├─ <style>         tokens (binding red, ruled cream paper, ink blue, ledger green; night theme; text sizes)
│                  · base and Urdu/RTL rules · cloth-bound top bar, desktop spine and mobile bottom tab bar
│                  · buttons, inputs, chips, switches · slips, KPIs, lists, ruled ledger tables
│                  · POS tiles, cart, keypad, payment · 58 mm receipt · stock, udhaar, ageing, denominations
│                  · SVG charts · sheet/modal, toasts, tour, palette, PIN pad · motion kit · print CSS
├─ <svg sprite>    95 symbols (kit icons + cart, truck, receipt, scale, banknote, barcode, khata, cash-in/out …)
├─ shell           spine nav · top bar (search, alerts, owner/helper, language) · day bar · #view · cart bar · tab bar
├─ overlays        #modal sheet, #palette, #tour, #toasts, #live (aria-live), #printArea + injected @page rule
└─ <script>
   ├─ utils        store (kk:), esc, ic, dates (local day keys), money in paisa (amt/rs/parseRs, lakh grouping),
   │               units and quantities (qtyStr/parseQty/lineAmount), EAN check digit, SHA-256, toast, animateIn,
   │               countUp, drawPath, sheet/confirm, printDoc, downloads, CSV
   ├─ i18n         STR {key: [English, Urdu]}, tIn/t with {placeholders} and singular forms
   ├─ catalogue    CATS (14), SEED_SUPPLIERS (6), SEED_ITEMS (126), SEED_CUSTOMERS (25), expense/adjustment kinds
   ├─ state        S, COLLECTIONS, persist/touch (+ compact sales codec), memo cache, indexes, per-day index, activity
   ├─ seed         60-day deterministic simulation (mulberry32) through the real domain functions
   ├─ domain       average-cost pool, FEFO batches, sales and voids, udhaar ledger and ageing, suppliers and purchases,
   │               adjustments, cash book and day close, velocity and reorder plans, dead stock, frequent items
   ├─ audit        auditStock/Sales/Customers/Suppliers/Cash, computeAlerts
   ├─ views        home · POS · checkout/keypad/discount · receipts/bills · stock · item forms/scanner · udhaar ·
   │               reminders/statements/customers · suppliers · purchase/reorder · cash book · charts/report data ·
   │               report views · alerts/activity/search · settings/backup · roles/PIN · tour/shortcuts/about
   └─ boot         ROUTES, nav (spine + tab bar + "More" sheet), route guard for helper mode, applyPrefs, setLang, boot()
```

## Domain logic and algorithm notes

- **Exact money and quantities.** Every amount is an integer number of paisa; every quantity an integer of grams, millilitres or pieces (`UNITS`: kg and litre ×1000, dozen ×12). A line is `round(price × qty ÷ base)` in paisa; inputs are parsed from strings without floating point (`parseRs`). Bills may round to the nearest rupee and the round-off is shown on the receipt.
- **Moving-average cost without drift.** Each item keeps `poolP`, the total cost of what is on the shelf. Issuing `q` of `stock` units removes `round(poolP × q ÷ stock)`; issuing the last unit removes the remainder, so the pool reaches exactly zero with the stock. Purchases add their exact cost; voids put back the cost they took. Hence *stock value = opening + purchases − cost of sales − adjustments* holds to the paisa.
- **Expiry batches (FEFO).** `batches` is a list of `{qty, exp}` sorted by date; purchases add a batch, sales and write-offs consume the oldest first, and the item's `expiry` is the oldest batch still on the shelf. Near-expiry uses the settings window, shortened for short-life goods (bread, dahi).
- **Udhaar and ageing.** Debits (opening, goods on udhaar, cash loan, other) raise the balance; credits (payment, void, write-off) lower it; the cached balance is checked against the sum of entries. Ageing applies credits to the oldest debits first (FIFO) and buckets what is left by its own date: 0–30, 31–60, 61–90, 90+ days.
- **Reorder suggestions.** From 28 complete days of sales: daily mean *v* and standard deviation *σ*; supplier lead time *L* and visit cycle *R*; *z* = 1.65 (about 95% of days in stock). Safety stock `z·σ·√L`, reorder point `v·L + SS`, order-up-to level `T = v·(L+R) + z·σ·√(L+R)`, suggestion `ceil((T − stock) ÷ pack) × pack`.
- **Cash book.** Drawer cash only: cash sales (including the cash part of udhaar bills), cash payments received, minus cash loans, cash supplier payments, cash expenses, drawings, deposits and void refunds. Opening = the last counted close carried through unclosed days; closing records the expected cash, the counted cash and the difference; the next day opens with what was counted. A closed day locks new entries until the owner reopens it, and days close in order.
- **Profit and loss.** Net sales − cost of goods (average cost at the moment of sale) = gross profit; minus expenses, damage/expiry at cost, count differences and bad debts, plus or minus the cash over/short at day closes = net profit. Owner drawings and goods taken for own use are shown below the line, not as expenses.
- **Books check.** `auditBooks()` re-derives every item's stock and cost pool from opening + purchases − sales ± adjustments, checks batches, bill arithmetic and payment splits, customer and supplier balances, and recomputes every day close. It runs on Home, in Settings, and after an import.
- **Seed simulation.** A deterministic 60-day run (mulberry32) of a busy shop: weekday and payday traffic, morning and evening rush, item popularity, typical quantities per unit, supplier visits that reorder below level with slow cost inflation, a mid-period price revision, customers with prompt, monthly, slow and defaulting payment habits, rent/bijli/salary/daily expenses, damage, expiry, own use and a stock count, evening bank deposits and a counted close each night. It uses the same functions as the UI, so the demo books reconcile exactly and every seeded day closes with zero difference.
- **Storage.** Sales are about 80% of the bytes and every `file://` page shares one quota, so they are stored as compact arrays (`packSale` / `unpackSale`) — about half the size of plain objects.

## Left for students

| Extension | Difficulty | Start here |
|---|---|---|
| Cloud sync across devices (Firebase/Supabase) with per-device change logs and conflict merge by document id | Hard | `importJson()` |
| Scheduled polite reminders through the WhatsApp Business API from a small server | Medium | `openReminder()` |
| Print to a Bluetooth ESC/POS 58 mm printer with Web Bluetooth (Urdu as a raster image) | Medium | `printReceipt()` |
| Confirm JazzCash/Easypaisa payments through a merchant API or a Raast QR | Hard | `payBodyHtml()` |
| Seasonal demand forecast (Holt-Winters; Ramadan, Eid and payday weeks) with a backtest | Hard | `velocity()` |
| Urdu voice entry for udhaar with the Web Speech API | Medium | `openEntry()` |
| Offline-first PWA: IndexedDB storage behind a service worker | Medium | `persist()` |
| Import the paper khata from a photo (OCR names and balances, review, create customers) | Hard | `openCustomerForm()` |

Search the source for `TODO(student)` — there is one marker per row.

## Data and safety notes

- All shop data is **illustrative**: the shop, owner, customers, suppliers, phone numbers (`03xx-00001xx` style), prices, costs and the 60-day history are generated for this template. Item names are generic, not brands. Verify real prices, tax rules and contacts on official sources before relying on any figure.
- Nothing leaves the device. Reminders open the shopkeeper's own WhatsApp or SMS app with a prepared message; the app never sends messages, contacts customers or takes payments by itself.
- The owner PIN is a convenience lock for a shared counter, not strong security: anyone with the device and developer tools can read `localStorage`. Treat backups (JSON/CSV) as private financial records.
- Customer credit data is sensitive. If you extend this to a server, ask customers for consent, keep balances private and allow them to see and dispute their own statement.

## Evaluation ideas for the FYP report

- **Correctness:** run the books check after thousands of random operations (sales, voids, purchases, adjustments, payments) and report zero mismatches; compare integer-paisa arithmetic against a naive floating-point version and show the drift it avoids.
- **Reorder quality:** replay the seeded history with and without the suggestions and measure stock-out days, average stock value and service level for different *z* values.
- **Udhaar recovery:** compare days-to-recover and 90+ balances before and after reminders in a pilot shop; test which reminder tone works best.
- **Usability:** time a quick sale (loose and packed items) on a low-end Android phone against the paper khata; run a short SUS questionnaire with shopkeepers in Urdu; check text-size and night-theme readability.
- **Accessibility and localisation:** keyboard-only walkthrough, screen-reader announcements of cart totals, RTL layout review by native Urdu readers.
- **Performance and storage:** render time of each page with 60 days and with a simulated year of data; storage size with and without the compact sales codec.
