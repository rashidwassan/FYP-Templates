# 15 · ATS-Friendly Resume Builder (idea #56)

**A resume the robots can read.** A single-file resume builder for fresh graduates: an accordion editor with a live A4 preview, four templates that are ATS-safe by construction, an ATS score that matches your resume against a pasted job description and flags Pakistani-CV habits (photo, CNIC, marital status), a rule-based bullet improver, a cover-letter generator and PDF / TXT / Markdown / JSON export. English UI, global tool, Pakistani context notes.

## Getting started

- Open `index.html` in any modern browser (Chrome/Edge recommended for the month pickers and `@page` printing). No build step, no server; the only external resource is Google Fonts (Lora + Karla), with system-font fallback offline.
- **Print / Save as PDF** uses the browser's print dialog: choose "Save as PDF", keep margins at *Default* (the file sets them with `@page`) and turn off headers/footers. The PDF file name is pre-filled as `Firstname_Lastname_Resume`.
- All state lives in `localStorage` under the `cvb:` prefix (`cvb:resumes` — every version — and `cvb:ui` — zoom, open sections, pasted job description). To reset, clear site data or run `Object.keys(localStorage).filter(k => k.startsWith('cvb:')).forEach(k => localStorage.removeItem(k))` in the console and reload.
- On first open two sample versions load (a fictional FAST-NUCES graduate: FYP, internship, TA role) so every screen has content. The **Sample** button in the editor reloads it; **Export › Save JSON** / **Load JSON** moves resumes between devices.

## The problem

Graduates in Pakistan apply with designer templates full of two-column tables, icons and photos that applicant-tracking systems cannot parse. They add a CNIC number, father's name, date of birth and marital status, and they never tailor keywords to the job ad. The CV is filtered out before a human reads it. Career offices repeat the same advice every semester; this tool bakes it into the editor.

## What is built

| Area | Features |
|---|---|
| Editor | Contact (photo and CNIC toggles **off by default** with an ATS note), Summary with word count, Experience / Education / Projects / Certifications / Languages / Awards / Publications / Teaching / Custom section; bullet editor (Enter adds, Backspace on empty removes, drag or arrow keys reorder, sparkle button improves); Skills as four chip groups (Enter/comma adds); drag-and-drop reorder of sections, entries and bullets with keyboard fallback and move buttons on touch; show/hide sections; autosave; multiple resume versions with open / rename / duplicate / delete / new blank / import |
| Templates | Classic (single column, Lora headings), Modern (main column + tinted sidebar for contact and skills, DOM stays linear: main then side), Compact (dense one-pager), Academic CV (Lora, publications and teaching auto-shown). Four accent colours, font-size scale 85–115 %, A4 or US Letter. All templates: real `<h1>/<h2>` headings, no tables for content, no text in images, `break-inside: avoid` on entries |
| Preview | Real 210 × 297 mm sheet scaled to fit, zoom −/+/100 %/fit, page counter and dashed page-break indicators; print CSS with `@page { size; margin }` driven by the template's margin, the preview scale removed and everything but the page hidden |
| Export | Print / Save as PDF, plain-text `.txt` for portal forms, copy as Markdown (clipboard with fallback dialog), JSON backup and import (validated and normalised), suggested file name |
| ATS score | Pill in the top bar (0–100, animated ring) opens a drawer: big score ring, structure vs keyword bars, verdict, job-description textarea with a sample ad, matched / missing keyword chips with one-click **add to skills**, prioritised fix list with points and "Go to section" buttons, passing checks, file-name and portal tips |
| Bullet improver | Rule-based suggestions with one-click apply: weak-starter rewrite ("Responsible for managing…" → "Managed…", iterated so "Worked on…" does not survive), verb upgrade table, add-a-metric placeholder, STAR template, remove first person, split long bullets, tidy casing; AI rewrite stub |
| Cover letter | Job title / company / hiring manager / source form, letter generated from the resume (degree, top skills, latest role's bullets, FYP, matched JD keywords), editable body, live A4 letter preview, print and copy |
| Motion & a11y | Accordion expand/collapse (grid-rows transition), preview fade on template switch, score ring and bar animation, chip pop-in, toasts, hover lift; all off under `prefers-reduced-motion`. Labels on every input, `aria-label` on icon buttons, Esc closes modal/drawer/menus, focus styles, touch targets ≥ 40 px |

## Architecture (single file)

```
index.html
├─ <style>      tokens (warm white / charcoal, Lora + Karla), motion kit, top bar, editor accordion,
│               A4 page + 4 template skins, ATS drawer, modal, cover letter, about, responsive, @media print
├─ <svg>        inline icon sprite (Lucide-style symbols used by ic('name'))
├─ <header>     brand, version switcher (popover), template segmented control, ATS pill, Export menu, Cover letter, About
├─ .mobilebar   Editor | Preview toggle + template select (≤ 900 px)
├─ <main #view> #view-edit (editor + preview) · #view-cover · #view-about
├─ <aside>      ATS drawer (#ats route)
└─ <script>
   ├─ utils         $, store (cvb: prefix), esc, ic(), toast, debounce, get/setPath, fmtD, animateIn, countUp
   ├─ constants     TEMPLATES, ACCENTS, PAPERS, SECTIONS, ITEM_DEFS (field schema per section), STRONG verbs,
   │                WEAK starters, UPGRADE map, IRREG past tenses, STOP words, PERSONAL_RX, STD_HEADINGS
   ├─ sample data   sampleResume(), sampleVariant(), blankResume()
   ├─ state         resumes[], ui, R (current), changed() → saveSoon + previewSoon + scoreSoon
   ├─ editor        renderEditor(), sectionBody(), itemHTML(), bulletsHTML(), sortable() (DnD + arrow keys),
   │                delegated input/click/keydown handlers, editorAction()
   ├─ content model blocks(R) → blockHTML() / resumeHTML() · toTXT() · toMD()
   ├─ preview       renderPreview() (page count, break markers, @page style), applyZoom(), renderPvbar()
   ├─ top bar       renderTopbar(), renderPill(), setTemplate(), versions popover, export menu, JSON import
   ├─ ATS engine    extractKeywords(), keywordMatch(), analyse() → checks[], renderATS(), gotoSection()
   ├─ improver      pastOf(), suggestBullet(), openImprover(), aiRewrite() stub
   ├─ cover letter  genLetter(), renderCover(), renderLetter()
   ├─ about         TODOS[], renderAbout()
   └─ router        hash routes: #edit · #ats · #cover · #about (· #r/<id> stub)
```

Data flow: every renderer reads the plain JSON resume `R`. `blocks(R)` shapes it once (title + para / entries / lines / inline / skills) and the HTML, TXT and Markdown renderers consume the same shape, so a new section type is one `case` in `blocks()` plus a field schema in `ITEM_DEFS`.

## Domain logic notes

- **ATS-safe by construction.** Single linear DOM (Modern renders main content first, then the sidebar), semantic headings, `<ul>` bullets, no tables or images carrying text, links as visible text. Print uses `@page { size: A4|Letter; margin: <template margin> }` with the page padding removed, so every printed page keeps the same margins; the on-screen sheet emulates them with padding and shows approximate break lines at multiples of the page height.
- **Keyword extraction.** Job description → lowercase → tokens (letters, digits, `+ # . -`, split on `/`) → stop-words removed → unigram and bigram frequencies. Bigrams are kept when they occur ≥ 2 times and their words are discounted from the unigram counts; weights are `min(freq, 4)` (×1.5 for bigrams); the top 40 are kept. Matching normalises tokens (dots removed, trailing *s* stripped) against the resume's plain-text export, so "APIs" matches "API" and "Node.js" matches "nodejs". Match % = matched weight ÷ total weight.
- **Score.** Eleven checks worth 100 points: contact 10 · headline 5 · summary length 8 · strong action verbs 12 (−2 per weak starter) · quantified bullets 10 (full marks at ≥ 60 %) · bullet length 6 · dates 6 · no photo/CNIC/age/marital status 12 · standard headings and no empty sections 6 · page count vs years of experience 8 (< 5 years → one page) · keyword match 17. Without a job description the keyword check is skipped and the rest is scaled to 100. Fixes are sorted by points lost.
- **Bullet improver.** Weak phrases (`responsible for`, `worked on`, `helped to`, …) are stripped; a following gerund or infinitive is turned into past tense (`managing → Managed`, `to design → Designed`) with a small irregular table, otherwise a replacement verb is prefixed. The pass repeats once so the result cannot itself start weakly. Metric placeholders depend on the verb family (reduce → "by X %", people → "used by N+").
- **Cover letter.** Pure template fill: degree and institute from the first education entry, non-soft skills, the latest role's first two bullets lower-cased into sentences, the first project, and matched JD keywords if an analysis exists. Brackets mark the sentences the student must write.

## Left for students

| Extension | Difficulty | Start here |
|---|---|---|
| AI bullet rewriting with a bring-your-own API key (store the key locally, 3 variants as suggestion cards, rule engine as fallback) | ★★ | `aiRewrite()` |
| DOCX export with real Word heading styles (generate `document.xml` from `blocks()`, zip it) | ★★★ | `exportDocx()` |
| LinkedIn data-export / JSON Resume import (map basics, work, education, skills) | ★★ | `importJsonResume()` |
| Backend accounts and shareable read-only links (REST API, auth, signed URLs rendering `resumeHTML()`) | ★★★ | `shareLink()` |
| Recruiter view with highlights and comments stored per bullet id | ★★ | `recruiterView()` and `route()` |
| Urdu / bilingual CV mode (heading dictionary, `dir="rtl"`, Noto Nastaliq Urdu) | ★★ | `resumeHTML()` |
| Synonym-aware keyword matching (stemmer + tech-synonym table or small embeddings) | ★★ | `keywordMatch()` |

Search the source for `TODO(student)` to find the exact hooks.

## Data & safety notes

Nothing leaves the browser: resumes, versions and the pasted job description are stored only in `localStorage`; the AI, sharing and import stubs are deliberately not wired to any network service. The sample person, employers and numbers are fictional and exist only to make the preview look real. The ATS checks encode widely published recruiter guidance (one page under five years of experience, no personal identifiers, standard headings); they approximate real parsers and should be validated against the portals a department's graduates actually use (Rozee.pk, LinkedIn Easy Apply, Workday, Greenhouse).

## Evaluation ideas for the FYP report

Upload exported PDFs to two or three real ATS demo accounts and compare parsed fields with the JSON model; measure keyword-match uplift after using the fix list on 20 student CVs; run think-aloud sessions with final-year students on the bullet improver; A/B the Classic and Modern templates through a parsing API.
