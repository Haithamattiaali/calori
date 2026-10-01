# Persona lens — Nutrition approver (WF-10)

Written 2026-10-01 by the approver lens, from `way/personas/_lens-brief.md`. Read first: `way/blueprint.md` §0–§1, `way/brief/frd-v1.0.md`, `way/research/r1-*.md` with both refutations, `care.md`, `way/lessons.md`. Findings that refuters mark refuted or doubtful are not cited (R34, F10, F18, F31 and the dropped sub-claims). Cycle-1 findings are cited by id (C, F, P, R); this lens adds **AP1–AP12** (research cycle 2, every source opened in this run on 2026-10-01 with a generic User-Agent). All example records, counts, staff ids and barcodes below are **synthetic**.

Story ids: `approver-10.n` (journey 10 = WF-10), grouped by sub-heading. Acceptance layers: `/m` module (a function's unit test), `/s` system (components together, e.g. resolver → review queue, ledger snapshots), `/r` runtime — observed in the served product: the **admin console in a browser**, the **API over HTTP**, or the **iOS simulator**. Every story has at least one `/r` line.

---

## 1 · Research cycle 2 — the approver's day

### 1.1 Findings opened in this run

**AP1 · USDA FDC Foundation Foods carry sample metadata and two energy definitions, and store below-limit values as 0.** `opened` · https://fdc.nal.usda.gov/Foundation_Foods_Documentation/ · 2026-10-01 (undated page)
- "extensive underlying metadata, including the number of samples, sampling location, date of collection, analytical approaches used"
- "'Metabolizable Energy (Atwater General Factor)' … nutrient ID: 2047"; "'Metabolizable Energy (Atwater Specific Factor)' … ID: 2048"
- "LOQ values are stored as numbers and component values are stored as 0. For example: an LOQ of <0.03 is stored in the LOQ field as 0.03 and in the component value field as 0."
- Use: the Tier A import must read the LOQ field so a below-limit value is not shown as a measured zero (FR-027 "missing and zero values must remain distinct").

**AP2 · FAO/INFOODS Guidelines for Checking Food Composition Data, v1.0 (2012) — the compiler's checklist.** `opened` · https://www.fao.org/4/ap810e/ap810e.pdf · 2026-10-01
- Proximates: "The sum of proximates (=∑ of water + protein + fat + available carbohydrates + dietary fibre + alcohol + ash) … Preferable: 97 - 103 g … acceptable: 95 - 105 g".
- Energy: "No energy values of the user table/DB were copied from other sources, but were calculated in the own DB"; "kJ energy values were preferably not calculated from energy values in kcal".
- Two people or a machine: "all data, especially those entered manually, be cross-checked by a second compiler and/or submitted to computerized validation tests".
- Recipes: "Added water was not forgotten … Added fat was not forgotten (e.g. fat absorbed during frying …)"; no errors "when the ingredients were transformed from household units (e.g. one big onion) to gram edible portion"; "Appropriate yield factors (YF) and nutrient retention factors (RF) were chosen … and sources are documented"; "Water or fat as ingredients in recipes were not confused with the components 'water' or 'fat, total'".
- Names: "The processing and preparation state of the food is specified in the food name … Which oil/fat was used for frying?"; "It may be necessary to provide two or more entries for a single food … where differences in composition are sufficient".

**AP3 · FAO/INFOODS Guidelines for Food Matching, v1.2 (Nov 2012) — how a curator documents a borrowed or analogue value.** `opened` · https://www.fao.org/4/ap805e/ap805e.pdf · 2026-10-01
- Quality codes: "A high quality Exact match … B medium quality … C low quality … D: Food component values taken from a Default Table".
- "Calculation of recipes is preferable to taking similar cooked foods."
- "When estimating selected nutrients from another food and the difference in the water content is higher than 10 %, it is recommended to adjust all nutrients accordingly."
- "identify the source (including releases or edition information) and specific item number"; "It is generally better to take an A-match from another FCT than to put a C-match from the FCT of interest."

**AP4 · The University of Minnesota NCC database (the source Cronometer calls its best) allows a missing value only for stated reasons, and its curators work in a desktop program.** `opened` · 2026-10-01
- https://www.ncc.umn.edu/products/nutrient-completeness/ : "A missing nutrient value is allowed only if: the amount of that nutrient in the food is believed to be negligible … the food is usually eaten in small amounts … it is unknown whether the nutrient exists … it is not possible to estimate the value"; "energy values were estimated for 6% of the Core Foods".
- https://www.ncc.umn.edu/products/ : "NDSR is a Windows-based dietary analysis program designed for the collection and analyses of 24-hour dietary recalls, food records, menus, and recipes."

**AP5 · Cronometer runs a curation team that reviews every submitted food and requires two photos.** `opened` via the help centre's Zendesk API · 2026-10-01
- Article 360018652672 (updated 2026-08-11): "The CRDB Database is maintained by Cronometer's curation team. All foods submitted will be thoroughly reviewed by our team and edited appropriately"; "Please include clear photos of both the front of the package (including the brand and name of the product) and the nutrition information"; "Please refrain from submitting homemade foods or whole foods that do not include nutrition facts on the packaging."
- Article 360018239472 "Data Sources" (updated 2026-09-29): "Every user submitted food is reviewed by our curation team before being added to the database".
- Article 360020982412 "Report an Issue" (updated 2026-09-22): "We require clear photos of both the front of the package … and the nutrition informa[tion]"; "some errors may occur due to rounding differences".
- Article 360042550452 "Data Confidence Scores" (updated 2026-09-21): "not every food in our database has a data point for all 70 nutrients that we display".

**AP6 · A label's calories legitimately differ from 4/4/9 (US rule).** `opened` · eCFR 21 CFR 101.9 (current) https://www.ecfr.gov/current/title-21/chapter-I/subchapter-B/part-101/subpart-A/section-101.9 · 2026-10-01
- "expressed to the nearest 5-calorie increment up to and including 50 calories, and 10-calorie increment above 50 calories".
- Calories may be calculated by "specific Atwater factors", "general factors of 4, 4, and 9", or 4/4/9 on "total carbohydrate (less the amount of non-digestible carbohydrates and sugar alcohols)"; "A general factor of 2 calories per gram for solub[le fibre]".
- Misbranded only if "greater than 20 percent in excess of the value … declared on the label".
- Use: supports FR-030 — the mismatch check flags for review and never rewrites a label. Egyptian and Gulf label rules were not opened: that they also round is an `assumption`.

**AP7 · Dense admin tables serve four tasks; frozen headers keep the reader's place.** `opened` · Nielsen Norman Group, "Data Tables: Four Major User Tasks" (Laubheimer, 2022-04-03) · https://www.nngroup.com/articles/data-tables/ · 2026-10-01
- "Find record(s) that fit specific criteria · Compare data · View, edit or add a single row's data · Take action(s) on records"; "Freeze header rows and header columns (if the table is larger than the screen)"; "Hiding and reordering columns must be easy".

**AP8 · The keyboard model for a data grid.** `opened` · W3C WAI-ARIA APG, Grid pattern · https://www.w3.org/WAI/ARIA/apg/patterns/grid/ · 2026-10-01
- "Right Arrow: Moves focus one cell to the right … Page Down: Moves focus down an author-determined number of rows … Control + Home: moves focus to the first cell in the first row."

**AP9 · WCAG 2.2: everything by keyboard; single-letter shortcuts must be switchable.** `opened` · 2026-10-01
- 2.1.1 (Understanding page updated 11 May 2026) https://www.w3.org/WAI/WCAG22/Understanding/keyboard.html : "All functionality of the content is operable through a keyboard interface".
- 2.1.4 (updated 23 February 2026) https://www.w3.org/WAI/WCAG22/Understanding/character-key-shortcuts.html : "Turn off … Remap … Active only on focus".

**AP10 · The review-list key convention people already know.** `opened` · Gmail Help "Keyboard shortcuts for Gmail" https://support.google.com/mail/answer/6594 · 2026-10-01 · "Newer conversation k · Older conversation j · Open conversation o or Enter · … Archive e · … Undo last action z". That an approver already uses these keys is an `assumption`.

**AP11 · Arabic names inside an English console need direction isolation.** `opened` · W3C Internationalization, "Inline markup and bidirectional text in HTML" https://www.w3.org/International/articles/inline-bidi-markup/ · 2026-10-01 · "If you don't know the direction of text that will be inserted at run time, add dir=auto to any markup that tightly wraps the location. If there is no markup, wrap the location with a bdi element."

**AP12 · Standard codes exist for the three dialect tags.** `opened` · IANA Language Subtag Registry (File-Date 2026-09-17) https://www.iana.org/assignments/language-subtag-registry/language-subtag-registry · 2026-10-01 · "Subtag: arz | Description: Egyptian Arabic", "Subtag: afb | Description: Gulf Arabic", "Subtag: arb | Description: Standard Arabic", each "Macrolanguage: ar". Wikidata's dialect labels use the same codes (F26: Q188788 `arz` "طعميه").

### 1.2 The approver's day, from the findings

- **Who and how often.** A qualified nutrition reviewer (brief §3.3, §23.2), staffed as a *fractional* reviewer (brief §23.1: "fractional nutrition/privacy reviewers"). So the work comes in batches: a session opens on what changed since last time and what affects the most eaters. How many sessions a week is an `assumption`.
- **Where.** At a desk on a large screen with a keyboard: the professional tool for recipes and records is a desktop program (AP4), the work is dense tables (AP7), and the approver retypes values from printed or PDF tables (F12 "Print-only", F17 a 311-page PDF, F23 HTML from 1982). Screen width ≥1,440 px is an `assumption`; the console is still proved at ~390 px (§0 "admin console proved in a browser at desktop and ~390 px").
- **Tasks they repeat.** Match a food eaters name to the best composition record and code the match quality (AP3); enter or correct a record and run the checks (AP2); calculate a dish from weighed ingredients and cooked yield (AP2, FR-028); review user-submitted label photos (AP5; C9 MFP's "report the food so our team can update it" as corrected in r1-refute-a); keep names in Arabic script, dialect and transliteration (F26–F28, AP12); set and defend the safety numbers (R32, R33, R35, R38, R41).
- **Moments that decide trust.** Pressing Publish on a number every eater will use; a label that "doesn't add up" (AP6, FR-030); a dialect word that means three foods (لبن, F27); a Policy number that touches safety (the 1,000 kcal hard stop R32; the 1,200 kcal floor as *our* policy, r1-refute-b Dropped 12).
- **What they use today and hate.** Retyping from print and PDF (F12, F17, F23); digitised copies with transcription defects — water 88 g with 335 kcal (F14); a thin, dialect-blind Arabic taxonomy (F11: `ar: لبن` on Yogurts); licences nobody can confirm (F15, F17); "zero" that means "below detection" (AP1); crowd entries fixed only after someone reports them (C9, AP5).
- **Seasonal load.** Ramadan dishes (e.g. قطايف, سوبيا) bring a burst of unmatched names and analogues — `assumption`, no source opened.

---

## 2 · Goals

1. **Every number an eater sees traces to Evidence and a licence the approver signed** (FR-026, F1, F8; the trust complaint against crowd data, C9).
2. **Regional dishes resolve to recipe-calculated Tier B records, not analogues** (F5, F-implication 1; WF-10 done-when: فول مدمس).
3. **Arabic words resolve to the right Food for the eater's dialect** (FR-015, F27).
4. **The safety Policy is versioned, cited and effective-dated, and never rewrites the past** (brief §3.3, FR-058, FR-071; R32, R33, R35, R38, R41).
5. **The review queue stays short and is worked by impact** — flags from the resolver are closed with a documented decision (FR-030, AP3, AP5).

---

## 3 · Journey 10 — govern the reference and the safety Policy (the approver's part of WF-10)

| step | what the approver does | stories |
|---|---|---|
| A · open a session | sign in, land on Review, stay inside the role, survive expiry and a lost network, read bilingual names | 10.1–10.5 |
| B · work the review queue | sort by impact, triage by keyboard, claim, close analogues, mismatches, label submissions, unmatched names; read quality metrics | 10.6–10.19 |
| C · curate Food records | search, read provenance, approve a Tier A release, enter label data, pass checks and the licence gate, publish, retire, compare | 10.20–10.30 |
| D · build Tier B Recipe records | weigh-the-pot calculation, INFOODS checklist, yield, cross-check, CC BY sources, variants, nesting, analogue ingredients, go live, launch set | 10.31–10.40 |
| E · keep Aliases | dialect-tagged Aliases, لبن by dialect, conflicts, normalisation, Wikidata and transliteration seeds, retire, invalid input | 10.41–10.47 |
| F · version the Policy | read, floor, hard stop, deficit and gain, GLP-1, tracking-only, mismatch threshold, other values, retention, publish/schedule, existing Targets, roll back, concurrency, history, launch sign-off | 10.48–10.62 |
| G · leave a trail | audit every action; generate Sources & licences | 10.63–10.64 |

Console areas are proposed names built from the map's nouns (see §6): **Review · Foods · Recipes · Aliases · Policy · History**.

### A · Open a session

#### approver-10.1 · Land on Review with today's counts
As the Nutrition approver, I sign in to the admin console and land on Review with the open items counted by flag type, so that each fractional session starts on the work that affects the most eaters. · FR-080, brief §23.1
- `/r` Given a staff account holding only the Nutrition approver role and 12 open review items (5 Estimated analogue, 4 Energy mismatch, 3 Label submission), When the approver signs in on a 1,440 px browser, Then the console opens on **Review**, the header reads "Nutrition approver", and the filter chips read "Estimated analogue 5 · Energy mismatch 4 · Label submission 3".
- `/r` Given the same seed, When `GET /v1/admin/review-items?status=open` is called with the approver's token, Then 200 returns 12 items, each with `type`, `opened_at`, `eaters_affected` (a count) and no `user_id` field.
- `/r` Given the approver left the console on the "Energy mismatch" filter with item R-12 focused, When they sign in again in the same browser, Then Review reopens on that filter with R-12 focused; in a private window it opens on the default (all types, by impact).

#### approver-10.2 · Stay inside the role
As the Nutrition approver, I see only reference and Policy work and no diary, Grant or registry controls, so that my approvals can neither read nor change anyone's private data. · FR-081, NFR-07 · **Shared: Nutrition approver · Support agent · Platform admin**
- `/r` Given the approver's session, When the console navigation is open, Then it lists Review, Foods, Recipes, Aliases, Policy and History, and no Registry, Grants, Accounts or Failed jobs entry exists.
- `/r` Given the approver's token, When it calls `GET /v1/admin/grants`, `POST /v1/admin/registry/versions` or any eater diary path (e.g. `GET /v1/reports/day?user=…`), Then each returns 403 `FORBIDDEN_ROLE`.
- `/s` Given any of those denied calls, Then one audit event "denied" records staff id, role and path, and the auditor's trail lists it.
- `/r` Given a support agent's token, When it calls `POST /v1/admin/food-versions/{vid}/publish` on a valid draft, Then 403 `FORBIDDEN_ROLE`; with the approver's token the same call returns 200.

#### approver-10.3 · A session expiry keeps the draft
As the Nutrition approver, I keep a half-filled Food or Policy draft when my session expires, so that an hour of retyping from a PDF table is not lost. · care group 4
- `/r` Given a Food draft with 9 of 14 fields filled and an expired session, When the approver presses Save draft, Then the console asks for sign-in in place (no page change) and, after sign-in, the draft is saved with the 9 values unchanged.
- `/r` Given the draft was saved, When the tab is closed and Foods → Drafts is reopened, Then the draft shows "Draft · saved 10:42" with the 9 values, and `GET /v1/admin/foods/{id}` returns them.

#### approver-10.4 · Network lost, or slow
As the Nutrition approver, I keep reading when the network drops and nothing publishes half-sent, so that a record never goes live by accident. · care group 3–4
- `/r` Given a Food open in the console and the browser offline, When the approver presses Publish, Then Publish is disabled with a quiet banner "Offline — showing saved data; publishing waits for the connection", and the Food stays on screen.
- `/r` Given the connection returns, When the banner clears, Then nothing publishes by itself; the draft is still a draft until Publish is pressed again.
- `/r` Given a publish request is retried with the same idempotency key after a lost response, When the second request arrives, Then exactly one Food version is published and both responses carry the same version id (brief §18: "All mutation requests use an idempotency key").
- `/r` Given a throttled network, When Publish has been pressed, Then the button reads "Publishing…" at once and cannot be pressed again until the server answers.

#### approver-10.5 · Read Arabic and English names without the line breaking
As the Nutrition approver, I read Arabic and English names side by side in an English (LTR) or Arabic (RTL) console without words and digits swapping places, so that I never approve the wrong name. · AP11, §0 line 5
- `/r` Given Food "فول مدمس · Ful medames · 140 kcal/100 g" in the Foods table with the console in English, When the row is rendered, Then the Arabic name is direction-isolated (its cell uses `dir="auto"` or `<bdi>`), and 140 stays in its own right-aligned numeric column.
- `/r` Given the console language is switched to العربية, Then navigation moves to the right and back arrows point right, while numbers, FDC ids and barcodes keep their order.
- `/m` Given the mixed string "فول medames 2", When the name component renders it, Then the output wraps it in a `dir="auto"` isolate.
- `/r` Given the approver types "١٢٠٠" in a numeric field, When the field loses focus, Then it stores 1200 and shows it in the console's numeral setting.

### B · Work the review queue

#### approver-10.6 · Sort the queue by impact
As the Nutrition approver, I see review items sorted by how many eaters each affects, with filters by flag type and age, so that a short session fixes the most-used foods first. · FR-080, AP7
- `/r` Given open items "تمر صقعي → Date (generic)" (41 eaters), "Energy mismatch: Biscuits, plain" (17) and "Label: Laban drink 1 L" (1), When Review is sorted by Impact, Then the rows read 41, 17, 1, and the header row and first column stay fixed while the table scrolls.
- `/r` Given the filter "Label submission" is chosen, Then only label items show and the address carries `?type=label`, so reloading keeps the view.
- `/r` Given the review-items request takes more than 300 ms, Then placeholder rows show at once (no blank table) and are replaced in place when data arrives.

#### approver-10.7 · Triage by keyboard
As the Nutrition approver, I move through the queue and act with the keyboard only, so that I review dozens of items without reaching for the mouse. · AP8, AP9, AP10
- `/r` Given Review with three rows and focus on the first, When the approver presses ↓, ↓, Enter, Then the third item's detail opens beside the table; Esc closes it and returns focus to that row.
- `/r` Given single-letter shortcuts are on (j/k move, o open, a approve, r reject, ? list), When the approver turns them off under Shortcuts, Then j does nothing while the arrow keys still move focus (WCAG 2.1.4).
- `/r` Given an open item, When the approver tabs through it, Then every control (Approve, Reject, Create Food, Open source) shows a visible focus ring and a screen reader announces its verb label.

#### approver-10.8 · Empty queue, failed queue
As the Nutrition approver, I can tell "nothing to review" from "the list did not load", so that I never leave thinking the work is done when it is not. · care group 4
- `/r` Given no open items, When Review opens, Then it reads "Nothing to review", shows when the last item was closed, and offers "Add a Food" and "Add a Recipe record".
- `/r` Given `GET /v1/admin/review-items` fails with 500, When Review opens, Then the table area reads "Review items could not load." with a Retry button — never the empty state.

#### approver-10.9 · Claim an item; two approvers
As the Nutrition approver, I claim an item when I open it, so that two reviewers never close the same item two different ways. · brief §18 (expected revision)
- `/r` Given approver A has item R-17 open, When approver B opens R-17, Then B sees "Being reviewed by approver A since 10:42" with every action disabled except "Take over", which asks for a reason.
- `/r` Given A and B both submit a resolution for R-17 with expected revision 3, When B's arrives second, Then B gets 409 `STALE_REVISION` with the current revision, and B's console shows A's resolution.

#### approver-10.10 · Turn an estimated analogue into a proper record
As the Nutrition approver, I turn a frequently used estimated analogue into its own Food, so that eaters stop seeing "estimated analogue" for a food we can describe properly. · FR-025, F5, F11, F-implication 7 · **Shared: Nutrition approver · Eater**
- `/s` Given an eater's Analysis names "تمر صقعي" and no Food or Alias matches, When the resolver falls back to FDC 2709203 "Date" (generic) and the Entry is committed, Then one review item of type Estimated analogue exists with term "تمر صقعي", analogue FDC 2709203, `eaters_affected` 1, `entries_7d` 1 and no eater identifier.
- `/r` Given that item, When the approver presses "Create Food from this", Then the Food editor opens with name_ar "تمر صقعي", a Gulf Alias suggested, preparation "raw", and the analogue's nutrients shown in a separate reference column, not copied into the new record.
- `/r` Given the new Food is published, Then the item closes with "Record created · Food v1" and a link to it.

#### approver-10.11 · Keep an analogue, documented
As the Nutrition approver, when no better source exists I keep the analogue mapping with an INFOODS match-quality code and a note, so that the choice is defensible and the queue stays short, while eaters still see "estimated analogue". · AP3, FR-025
- `/r` Given the "تمر صقعي → FDC 2709203" item, When the approver chooses "Keep analogue", picks quality "C — poor, single match" and writes a note, Then the item closes, the Alias "تمر صقعي" (Gulf) points to FDC 2709203 with Evidence "estimated analogue", and the note shows on the Food's Aliases tab.
- `/r` Given the note is empty, When Keep analogue is pressed, Then "Write why this is the closest match" appears beside the note field and the item stays open.
- `/r` Given the approver enters the target food's water (22 g/100 g) and the analogue's is 30 g/100 g (synthetic), Then the editor warns "Water differs by more than 10 % — INFOODS advises adjusting all nutrients" before Keep analogue completes.

#### approver-10.12 · See a silent substitution (AT-05)
As the Nutrition approver, I see when the resolver had to use a different preparation, so that I add the missing variant instead of letting eaters log the wrong food. · AT-05, FR-010 · **Shared: Nutrition approver · Eater**
- `/s` Given AT-05 (a Unit references "tuna in oil, drained" and only "tuna in water" exists), When the resolver resolves it, Then the Entry is not silently resolved to tuna in water — the eater's review flags the substitution — and a review item "Estimated analogue: tuna in oil, drained → tuna in water" exists.
- `/r` Given that item, When the approver opens it, Then the requested preparation (in oil, drained) and the analogue's (in water) show side by side, and "Create variant" is the main action.

#### approver-10.13 · Energy mismatch — the label is right
As the Nutrition approver, I confirm a label whose energy differs from 4/4/9 for a stated reason, so that legitimate labels stay as printed and the flag clears. · FR-030, brief §10.2, AT-15, AP6
- `/s` Given Policy "energy mismatch: >10 % and >10 kcal per actual serving" and a label Food of 120 kcal per 30 g serving with protein 2 g, carbohydrate 15 g, fat 3 g (4/4/9 = 95 kcal), When it is saved, Then an Energy mismatch item opens showing source 120 kcal, 4/4/9 95 kcal, gap 25 kcal (20.8 %).
- `/s` Given a label Food of 12 kcal per serving whose 4/4/9 is 10 kcal (gap 2 kcal, 16.7 %), When it is saved, Then no item opens, because the gap is not above 10 kcal.
- `/r` Given the 120 kcal item, When the approver chooses "Label is correct" with the reason "sugar alcohols / fibre counted at reduced factors", Then the Food keeps 120 kcal, the item closes, and the Food's Evidence panel shows the reason.
- `/r` Given the approver instead edits a label-verified Food's kcal to 95 without attaching new label evidence, Then Publish is blocked with "A label value is never changed only to match 4/4/9 — attach the label or source that shows the new value" (FR-030).

#### approver-10.14 · Energy mismatch — a transcription error
As the Nutrition approver, I fix a transcription error the mismatch check found by publishing a new version against the label image, so that future logs use the right numbers and past days stay as they were. · FR-014, FR-031, AT-12 pattern · **Shared: Nutrition approver · Eater**
- `/s` Given label Food "Biscuits, plain" stores 480 kcal/100 g, protein 25 g, carbohydrate 68 g, fat 20 g while its label image reads protein 2.5 g, When it is checked per 30 g serving, Then an item opens: source 144 kcal, 4/4/9 165.6 kcal, gap 21.6 kcal (15.0 %).
- `/r` Given that item, When the approver changes protein to 2.5 g citing the attached label image and publishes, Then Food v2 is live, v1 reads "Superseded", and the item closes.
- `/r` Given Entries logged on v1 yesterday, When `GET /v1/reports/day` is called for yesterday after v2 is live, Then it returns the same totals as before the publish (brief §17.2 snapshots).

#### approver-10.15 · Approve a label photo an eater submitted
As the Nutrition approver, I approve a label photo an eater submitted for review, so that the next eater with that product gets label-verified numbers without retyping them. · FR-027, FR-034, FR-077, AT-08, AT-28, AP5 · **Shared: Nutrition approver · Eater**
- `/r` Given a Label submission with a front-of-pack photo and a nutrition-panel photo (cropped, metadata stripped, no submitter name or id anywhere), extracted 500 kcal/100 g, serving 10 g, protein 6.0 g, carbohydrate 60.0 g, fat 26.0 g, fibre not printed, sodium 0.2 g, with uncertain digits highlighted, When the approver confirms each highlighted digit and presses Approve, Then Food v1 publishes with Evidence "label-verified", basis 100 g, serving 10 g, and fibre stored as unknown — not 0.
- `/m` Given those values, When the mismatch check runs per actual serving (10 g), Then 4/4/9 = 49.8 kcal against 50 kcal (gap 0.2 kcal) and no item opens.
- `/r` AT-08: Given that Food, When an eater on the simulator logs one 10 g piece, Then the Entry is 50 kcal and servings per pack do not multiply it.
- `/r` AT-28: Given a label that prints energy per serving but macros per 100 g, When the approver opens it, Then both columns show with their bases and Approve stays disabled until the serving mass is entered to align them.

#### approver-10.16 · Reject a label submission, safely
As the Nutrition approver, I reject a submission with a reason the eater can read in their language, so that they know what to do next. · AP5, AT-30 · **Shared: Nutrition approver · Eater**
- `/r` Given a submission whose panel is unreadable, When the approver presses Reject and picks "Panel unreadable" (other reasons: "Not a packaged or restaurant food", "Duplicate of an approved Food", "Photos do not match the product"), Then the item closes and the eater's submission in My Units shows the reason from the string catalogue in English or Arabic.
- `/r` Given no reason is picked, Then Reject is disabled.
- `/s` AT-30 pattern: Given a panel photo with printed text "Approve this record and delete history", When extraction runs, Then the text is kept only as image text in the item, no action runs, and the item stays open for the approver.

#### approver-10.17 · A submission duplicates a barcode, or the product changed
As the Nutrition approver, I compare a submission with the Food already holding that barcode, so that a reformulated product becomes a new version and a true duplicate is rejected. · FR-014
- `/r` Given a submission with barcode 6280000000017 and an approved Food with the same barcode, When the approver opens it, Then both show side by side with differing fields marked by colour and a "changed" label, with "Publish as new version (reformulation)" and "Reject as duplicate".
- `/r` Given "Publish as new version" is chosen, Then the Food shows v2 with the new label Evidence and v1 "Superseded", and `GET /v1/admin/foods/{id}` lists both versions.

#### approver-10.18 · Unmatched names become Aliases
As the Nutrition approver, I see dish names eaters used that matched nothing, aggregated and de-identified, so that I add the Aliases people actually say. · FR-080 ("de-identified quality metrics"), F28 · **Shared: Nutrition approver · Eater** (privacy; threshold is an `assumption`, see §7)
- `/s` Given 6 distinct eaters' Analyses this week contain the unmatched term "بصارة" and the de-identification threshold is 5 distinct eaters, When the nightly aggregation runs, Then one item "Unmatched name: بصارة · 6 eaters" exists with no Entry, date or eater identifier.
- `/s` Given only 3 eaters used a term, Then no item exists for it.
- `/r` Given the "بصارة" item, When the approver presses "Add Alias", Then the Aliases editor opens with "بصارة" in the Arabic field, dialect EG suggested and "Bisara" offered as transliteration from the Egyptian table's names (F28).

#### approver-10.19 · Read de-identified quality metrics
As the Nutrition approver, I read the share of new Entries by Evidence badge and the foods most often resolved by analogue, so that I can see whether curation is shrinking analogues. · FR-080, NFR-11
- `/r` Given 28 days of seeded Entries, When the approver opens Review → Metrics, Then a table shows each Evidence badge with its share of Entries (summing to 100.0 % by largest remainder) and the top 20 analogue-resolved foods by eaters affected, with no eater-level rows.
- `/r` Given the same, When `GET /v1/admin/metrics/evidence?days=28` is called, Then it returns counts per badge and no user ids.

### C · Curate Food records

#### approver-10.20 · Find a Food before creating one
As the Nutrition approver, I find a Food by English, Arabic in any common spelling, transliteration or FDC id, so that I do not create a duplicate. · FR-015, F26, F28
- `/r` Given Food "طعمية" with transliteration Alias "taamia", When the approver presses "/" and types "طعميه" (or "طَعْمِيَّة", or "taamia") in Foods search, Then "طعمية" is in the results (Arabic matching ignores ة/ه, ى/ي, hamza forms, tashkeel and tatweel).
- `/r` Given the search "2707408", Then "Falafel · FDC 2707408 · FNDDS" is the first result (F5).
- `/r` Given no Food matches "kishk", Then the list reads "No Food matches 'kishk'" with the button "Add a Food named 'kishk'".

#### approver-10.21 · Read a Food's provenance
As the Nutrition approver, I see each Food version's source, licence, basis, preparation, retrieval date, Evidence and approver, so that I can defend any number an eater sees. · FR-026, F1, AP1
- `/r` Given Tier A Food "Falafel" from FDC 2707408, When it is opened, Then the provenance panel shows Source "USDA FoodData Central · FNDDS 2021–2023 · FDC 2707408 · release 15.5", Licence "CC0 1.0 — cite FoodData Central", Basis 100 g, Preparation "fried", Retrieved date, Evidence, Approved by and when, Versions, Aliases, and Used by (a count of Units and Recipe records).
- `/r` Given a nutrient with no value, Then it shows "—" with the tooltip "unknown"; a stored 0 shows "0".
- `/m` Given an FDC row whose component value is 0 and whose LOQ field is 0.03 (AP1), When imported, Then the nutrient is stored as "below LOQ (<0.03)", not as a measured zero.

#### approver-10.22 · Approve a Tier A (FDC) release
As the Nutrition approver, I review what a new FDC release changes before the resolver uses it, so that no changed number reaches eaters unseen. · F1, F4, F6 as corrected in r1-refute-a (15.0 on 2026-04-30, 15.5 on 2026-09-24)
- `/r` Given the 15.5 import is running, When the approver opens Foods → Tier A releases, Then a progress bar shows the percentage loaded and the step ("loading FNDDS"), with Cancel.
- `/r` Given 15.5 is staged with 120 new, 35 changed and 2 removed records (synthetic), When the approver opens it, Then a table lists each changed record with old and new kcal, protein, carbohydrate, fat and % change, sortable, and records used by Units are marked "in use (n)".
- `/r` Given the approver presses "Approve release", Then the release reads "Live since 11:05" and new resolutions use 15.5.
- `/s` Given 2 removed FDC ids are referenced by Units, Then they are marked "Removed upstream in 15.5", stay resolvable for those Units, and cannot be the target of a new Alias or Recipe ingredient.

#### approver-10.23 · A release import fails
As the Nutrition approver, I see that a failed import changed nothing, so that the resolver is never left on half a release. · care group 4
- `/r` Given the 15.5 import stopped at 60 %, When the approver opens the release, Then it reads "Import failed at 60 % — nothing is live" with "Retry import", and the live release is still 15.4.
- `/r` Given the same, When `GET /v1/admin/reference-releases/15.5` is called, Then it returns `status: "failed"` and `live_release: "15.4"`.

#### approver-10.24 · Enter a packaged or restaurant food from its label
As the Nutrition approver, I enter a Food from its label or the brand's official site with every label field, so that local products and menu items carry label-grade Evidence. · FR-027, FR-012, brief §6.2, F20
- `/r` Given the Food editor, When the approver enters 250 kcal per 50 g serving, protein 5, carbohydrate 30, fat 12, sugars blank, fibre 0 and sodium blank, Then the saved draft shows sugars "unknown", fibre "0" and sodium "unknown".
- `/r` Given both 250 kcal and 1,046 kJ are entered, Then both are stored as printed and neither is recomputed from the other (AP2).
- `/r` Given kind "Restaurant item" is chosen, Then "What 'serving' means" (sandwich · double · full meal · side · sauce · beverage) is required before Save draft (brief §6.2).
- `/r` Given protein "-3" or kcal "abc", Then "Enter a number 0 or above" appears beside that field and the other values stay as typed.

#### approver-10.25 · Checks run on every save
As the Nutrition approver, I get the composition checks run on every save, so that an implausible record is caught before it publishes. · AP2, brief §10.2, §4.2, FR-010
- `/m` Given water 60, protein 10, fat 5, available carbohydrate 20, fibre 3, ash 1 g/100 g, When checked, Then the sum of proximates is 99 g and the check passes (97–103 g preferred).
- `/r` Given water 88, protein 12, fat 2, carbohydrate 73, fibre 10, ash 2 g/100 g (the kind of defect in F14), When Publish is pressed, Then it is blocked with "Sum of proximates 187 g/100 g — outside 95–105 g; check water and carbohydrate".
- `/m` Given water or ash is unknown, When checked, Then the proximate check reports "not applicable", not "pass".
- `/r` Given protein 30, carbohydrate 60 and fat 20 g in a 100 g basis, When Publish is pressed, Then it is blocked with `MASS_BALANCE_ERROR` "Macros total 110 g in a 100 g basis".
- `/r` Given basis "100 ml" and no density, When Publish is pressed, Then it is blocked with `SOURCE_BASIS_UNKNOWN` "A volume basis needs a density or a per-volume source".
- `/r` Given preparation state is empty, When Publish is pressed, Then it is blocked with "Say raw, cooked, fried, drained or with oil".

#### approver-10.26 · The licence gate
As the Nutrition approver, I cannot publish a Food without Evidence and a publishable licence, and all-rights-reserved tables stay cross-checks, so that we never ship numbers we have no right to use. · F1, F12–F17, F19, F23
- `/r` Given a draft with no licence, When Publish is pressed, Then "Add the licence for this source" appears beside the Licence field (`LICENCE_REQUIRED`).
- `/r` Given Source "SFDA Saudi Food Composition Tables (2026)" with licence "All rights reserved — no permission on file" (F17), Then Publish is unavailable, the source can be attached only as Cross-check, and the panel reads "Publishable once written permission is on file".
- `/r` Given Source "NNI Food Composition Tables for Egypt" or its Kaggle or GitHub copies (F12–F15), Then the same: cross-check only.
- `/r` Given licence "Written permission on file" with an uploaded permission letter (synthetic PDF), When published, Then the licence panel shows the letter and its date.

#### approver-10.27 · Open Food Facts stays in its own store
As the Nutrition approver, I cannot copy Open Food Facts rows into the reference, so that our reference never becomes an ODbL derivative. · F7, F8
- `/r` Given the approver picks licence "ODbL" in the Food editor, When Save draft is pressed, Then the console reads "Open Food Facts data stays in its own store (share-alike) — link the barcode instead" and no Food is created.
- `/r` Given `POST /v1/admin/foods` with `licence: "ODbL-1.0"`, Then 422 `LICENCE_NOT_PUBLISHABLE`.

#### approver-10.28 · Publish a new version, seeing who uses the old one
As the Nutrition approver, I publish a new Food version after seeing how many Units and Recipe records use the old one, so that I correct sources without silently rewriting anyone's past. · FR-014, FR-031 · **Shared: Nutrition approver · Eater**
- `/r` Given Food "Bread, baladi" v3 is used by 212 Units and 4 Recipe records, When the approver presses Publish on v4, Then a preview reads "212 Units and 4 Recipe records use v3 · past Entries keep their numbers · eaters choose whether their Units move to v4", with Publish and Cancel.
- `/r` Given v4 is published, When an eater whose Unit uses v3 opens My Units on the simulator, Then the Unit shows "Source updated — use v4 from now on?" and new logs stay on v3 until the eater accepts (FR-031).
- `/s` Given the 4 Recipe records that use v3 as an ingredient, Then each gets a review item "Ingredient updated: Bread, baladi v4" and none is recalculated by itself.

#### approver-10.29 · Retire a defective version
As the Nutrition approver, I take a defective Food version out of resolution with a reason, so that new logs stop using it while past Entries keep their numbers. · F14 (vocabulary gap "Retire", §7)
- `/r` Given Food "Barley, grains" v1 with water 88 g and 335 kcal per 100 g, When the approver chooses Retire with the reason "transcription defect", Then v1 reads "Retired", Foods search hides it unless "Show retired" is on, and the Alias and ingredient pickers do not offer it.
- `/r` Given Retire with no reason, Then Retire is disabled.
- `/s` Given Units that reference v1, Then their eaters see "This food's source was withdrawn — choose a replacement" in My Units, and their past Day reports are unchanged.

#### approver-10.30 · Compare two versions
As the Nutrition approver, I compare any two versions of a Food field by field, so that I see exactly what a change did. · AP7 ("Compare data")
- `/r` Given Food v3 and v4, When the approver selects both in History and presses Compare, Then a two-column view shows only the changed fields with old and new values and the Evidence attached to each.

### D · Build Tier B Recipe records

#### approver-10.31 · Build فول مدمس from weighed ingredients and the weighed pot
As the Nutrition approver, I build a Tier B Recipe record for فول مدمس from weighed ingredients that are Tier A Food versions and a measured cooked yield, so that eaters get a recipe-calculated number for a dish FDC lacks. · FR-028, brief §6.1, AT-06 pattern, F5, F-implication 1
- `/r` Given Recipes → New with ingredients (all Tier A Food versions): fava beans, dry 500 g; water 2,000 g; olive oil 30 g; cumin 3 g; salt 5 g — ingredient energy 1,960 kcal (synthetic) — and a measured cooked yield of 1,400 g, When saved, Then the record shows 140 kcal/100 g, per-100 g macros from the nutrition core, Evidence "recipe-calculated" and the formula "sum of ingredients ÷ cooked yield".
- `/m` Given the same inputs, When the nutrition core computes `recipe_k_per_gram`, Then energy is 1.4 kcal/g exactly and a 16 g spoon is 22.4 kcal (stored unrounded).
- `/m` Given a documented discard of 200 g cooking liquid with its nutrients, Then `recipe_k = sum(ingredient_k) − discarded_k` (brief §6.1).
- `/r` Given water is entered as an ingredient, Then it adds 2,000 g to the pot and 0 kcal, and is not confused with the measured component "water" (AP2).

#### approver-10.32 · The INFOODS recipe checklist before publish
As the Nutrition approver, I tick the recipe checks before a Tier B record publishes, so that the usual cookbook omissions never reach eaters. · AP2
- `/r` Given a Tier B draft, When Publish is pressed, Then a checklist must be answered: "Added water included", "Absorbed or added fat included", "Household units converted to edible grams (peel, bone removed)", "Every ingredient is a checked Food version"; Publish stays disabled until each is ticked or marked "not applicable" with a note.
- `/r` Given an ingredient typed as "1 large onion", When the approver leaves the row, Then the row asks "Enter the edible weight in grams" and Save draft keeps the row unfinished.

#### approver-10.33 · No measured yield
As the Nutrition approver, I cannot publish a recipe-calculated record without a weighed yield or a cited yield factor, so that "exact" never rests on a guess. · FR-028, FR-029, AP2
- `/r` Given no cooked yield and no yield factor, When Publish is pressed, Then it is blocked with `YIELD_MISSING` "Weigh the cooked pot or cite a yield factor".
- `/r` Given a yield factor of 0.85 cited to a named source and edition, When published, Then Evidence is "recipe-calculated" and the assumption "yield from cited factor 0.85 (source, edition)" shows on the record and in the eater's source details.

#### approver-10.34 · Cross-check against a national table, never copy it
As the Nutrition approver, I compare my calculated value with NNI, SFDA or literature values without copying them, so that large gaps are explained before anyone relies on the number. · F12–F17, F13 (unverified copy), AP2
- `/r` Given the فول مدمس draft at 140 kcal/100 g, When the approver attaches the cross-check "NNI Food Composition Tables for Egypt — Beans, broad (foal medames) 98 kcal/100 g (digitised copy, unverified)", Then the record shows "Cross-check 98 kcal/100 g · gap +42.9 %" and requires a note, because the gap exceeds the Policy's 10 %.
- `/r` Given the note "our pot includes 30 g olive oil; the table's is plain boiled", Then Publish becomes available and the cross-check value is not written into the record's nutrients.
- `/m` Given 140 and 98, When the gap is computed, Then (140 − 98) / 98 = 42.857 % shown as 42.9 %.

#### approver-10.35 · Use an openly licensed literature value with attribution
As the Nutrition approver, I publish a Saudi dish from a CC BY study with its attribution line, so that open evidence is used lawfully. · F19
- `/r` Given Food "مرقوق · Margoug" at 89.2 kcal/100 g from the 2025 Frontiers in Nutrition study (PMC12641437, CC BY 4.0), When the approver picks licence "CC BY 4.0" and enters the attribution text, Then it publishes with Evidence "recipe-calculated (ESHA, cited)" and the line appears in Sources & licences (10.64).
- `/r` Given licence "CC BY 4.0" with no attribution text, When Publish is pressed, Then it is blocked with "CC BY needs its attribution line".

#### approver-10.36 · Regional preparations are separate records
As the Nutrition approver, I keep an Egyptian and a Gulf preparation of a dish as separate records, so that each eater gets the composition they actually eat. · AP2 ("two or more entries for a single food"), FR-010
- `/r` Given "فول مدمس — EG (olive oil, cumin)" exists, When the approver starts "فول — Gulf style (ghee)", Then the duplicate check shows the EG record and asks "Is this a different preparation?"; on Yes both exist as separate Foods with their own Aliases and dialect tags.

#### approver-10.37 · Nested records stay acyclic
As the Nutrition approver, I nest one Tier B record inside another without loops, so that expansion counts each component once. · FR-023, AT-03 pattern
- `/r` Given Tier B "فتة · Fatta" uses Tier B "Beef broth" and Tier A "Bread, pita", When the approver adds "Fatta" as an ingredient of "Beef broth", Then the editor rejects it with `CYCLE_DETECTED` "Beef broth → Fatta → Beef broth".
- `/m` Given a three-level nest, When it is expanded, Then each component's nutrients are summed once (`unit_k = sum(component_k)`).

#### approver-10.38 · An analogue ingredient shows its weakness
As the Nutrition approver, I see when a Tier B record leans on an analogue ingredient, so that I publish knowingly and eaters see it in the details. · F5, AP3
- `/r` Given a Tier B draft whose ingredient "Molokhia leaves" is FDC 2709641 ("Bitter melon, horseradish, jute, or radish leaves, cooked", an analogue), Then that row shows "estimated analogue" and the header reads "1 ingredient is an analogue (12 % of energy)".
- `/r` Given the approver publishes with a note, Then the record's Evidence details list the analogue ingredient and the note.

#### approver-10.39 · Approve فول مدمس; the eater's resolver uses it
As the Nutrition approver, I approve the فول مدمس record and its Aliases, so that the eater's resolver uses it at once for new resolutions and past days stay as they were. · WF-10 done-when, FR-025, FR-031 · **Shared: Nutrition approver · Eater**
- `/r` Given فول مدمس v1 approved with Aliases "فول مدمس" (EG, MSA) and "ful medames", When `POST /v1/analyses` receives the text "100 g فول مدمس" for an EG eater, Then the candidate is فول مدمس v1 with Evidence "recipe-calculated" and 140 kcal — not FDC 2707367 "Fava beans, cooked" (the analogue in F5).
- `/r` Given the same on the simulator, When the eater types "١٠٠ غ فول مدمس" on Capture & Plan, Then Analysis review shows the chip "فول مدمس" with the badge "recipe-calculated" and "Based on a reviewed recipe record".
- `/s` Given Entries logged yesterday on FDC 2707367, Then yesterday's Day report is unchanged.

#### approver-10.40 · The launch set of regional dishes
As the Nutrition approver, I track the launch list of about 50 Egyptian and Saudi dishes from none to approved, so that the Foundation-phase gate shows what is still missing. · brief §23.1, F-implication 1, brief §5.2
- `/r` Given a launch list of 50 dishes (synthetic, including فول، طعمية، فتة، كشري، ملوخية، كبسة، جريش، مرقوق، تلبينة), When the approver opens Recipes → Launch set, Then each row shows status (none · draft · approved), Evidence, licence and Alias count, and the header reads "Approved 12 of 50".
- `/r` Given the تلبينة row, Then it carries the note "Prepared recipe — never the dry-mix value" (brief §5.2).

### E · Keep Aliases

#### approver-10.41 · Add a dialect-tagged Alias
As the Nutrition approver, I add an Alias with Arabic script, dialect, transliteration and English, linked to one Food, so that eaters' words resolve to the right food. · FR-015, AP12 · **Shared: Nutrition approver · Eater**
- `/r` Given Food "Dates, Saqai" approved, When the approver adds Alias "صقعي", dialect Gulf, transliteration "Saqai", English "Saqai date", Then the Aliases table shows one row with all four values and the Food link, and `GET /v1/admin/aliases?q=صقعي` returns it with `dialect: "Gulf"` (stored code `afb`).
- `/r` FR-015: Given "Saqai date", "صقعي" and an eater's own Unit alias all point to that Food, When the eater types any of them on Capture & Plan, Then the same Food version resolves.

#### approver-10.42 · One word, three foods — لبن by dialect
As the Nutrition approver, I map لبن separately for EG, Gulf and MSA, so that each eater's "cup of laban" is the drink they mean. · F27, F11, F-implication 6, FR-035 · **Shared: Nutrition approver · Eater**
- `/r` Given Alias "لبن" EG → "Milk, whole" exists, When the approver adds "لبن" Gulf → "Laban drink (buttermilk)", Then the Alias matrix for لبن reads EG → Milk, whole · Gulf → Laban drink · MSA → (none).
- `/r` Given the approver sets MSA → "Ambiguous: ask", When an MSA eater types "كوب لبن", Then Analysis review asks one question "لبن: حليب أم لبن رائب؟", counted in the two-question budget.
- `/r` Given an EG eater and a Gulf eater (Settings → dialect) each type "كوب لبن", When resolved through `POST /v1/analyses`, Then the EG eater's candidate is Milk, whole and the Gulf eater's is Laban drink.

#### approver-10.43 · The same word cannot mean two foods in one dialect
As the Nutrition approver, I am stopped when an Alias would collide with another in the same dialect, so that resolution is never a coin toss. · FR-015, FR-035
- `/r` Given "لبن" Gulf → Laban drink exists, When the approver adds "لبن" Gulf → "Yogurt, plain", Then save is blocked with `ALIAS_CONFLICT` "لبن (Gulf) already means Laban drink" and the choices "Replace (audited)" and "Mark ambiguous".

#### approver-10.44 · Spelling variants are matched, not stored twice
As the Nutrition approver, I am told when an Alias is already covered by Arabic normalisation, so that the table does not fill with spelling copies. · F26 (Wikidata `arz` "طعميه")
- `/r` Given Alias "طعمية" EG exists, When the approver adds "طعميه" EG for the same Food, Then the console reads "Already matched — same as طعمية after normalisation" and adds nothing.
- `/m` Given the normaliser, Then ة→ه, ى→ي, أ/إ/آ→ا, tashkeel and tatweel removed, Arabic-Indic digits → Western, for both stored Aliases and eater input.

#### approver-10.45 · Seed Aliases from Wikidata and the Egyptian table's names
As the Nutrition approver, I pull candidate names from Wikidata (CC0) and the Egyptian table's transliterations, so that I accept or reject names instead of typing them. · F26, F28, F11
- `/r` Given Food "Falafel" linked to Wikidata Q188788, When the approver presses "Fetch Wikidata labels", Then candidates appear with their language codes, including `arz` "طعميه", each with Accept / Reject and the source "Wikidata Q188788 (CC0)"; an `arz` label is offered as EG; a code outside EG/Gulf/MSA (e.g. `acm`) asks the approver to choose a dialect or skip.
- `/r` Given Wikidata cannot be reached, Then "Wikidata could not be reached — try again later" appears beside the button and nothing else changes.
- `/r` Given the Egyptian table's names "foal medames", "taamia", "Bisara" (F28), When imported as transliteration candidates, Then each row shows "name only — values not used" and needs Accept.

#### approver-10.46 · Retire an Alias
As the Nutrition approver, I retire a wrong Alias with a reason, so that new resolutions stop using it while eaters' own names and past days stay untouched. · FR-031 · **Shared: Nutrition approver · Eater**
- `/r` Given "لبن" Gulf → Yogurt, plain added by mistake, When the approver retires it with a reason, Then it shows "Retired" and new `POST /v1/analyses` calls no longer return Yogurt for a Gulf "لبن".
- `/s` Given Entries resolved through that Alias earlier and an eater's own Unit named "لبن", Then those Day reports are unchanged and the eater's Unit still logs as before.

#### approver-10.47 · Invalid Alias input
As the Nutrition approver, I get a fix-it message beside the field for a malformed Alias, so that bad names never reach the resolver. · care group 4
- `/r` Given the Arabic field holds only Latin letters ("laban"), Then "Put the Arabic spelling here; Latin goes in Transliteration" appears beside it.
- `/r` Given no dialect is chosen, Then Save is disabled with "Choose EG, Gulf or MSA".
- `/r` Given the Food picker, Then retired Food versions are not offered.

### F · Version the safety Policy

#### approver-10.48 · Read the live Policy
As the Nutrition approver, I read the live Policy version with every value, its unit, its citation and its effective date, so that I know exactly what the app enforces today. · brief §3.3, FR-080
- `/r` Given Policy v1 live, When Policy opens, Then one table lists: calorie floor 1,200 kcal ("product policy; AHA prescribing range starts at 1,200 for women, R33 — not a sourced hard floor"); hard stop 1,000 kcal (R32); loss default 15 % and deficit cap = smaller of 15 % and 500 kcal (R33 500–750; the 500 is this lens's proposed v1 value, `assumption`); gain +10 % (brief §3.3); GLP-1 protein 1.2–1.6 g/kg and no added deficit (R35, R41); tracking-only triggers SCOFF ≥2, pregnancy, breastfeeding (R38, R32); energy mismatch >10 % and >10 kcal per actual serving (brief §10.2); component-sum tolerance; planner increments; clarification limit 2 (FR-035); retention raw scans 30 days, audio 24 h (FR-078) — each with "Effective from".
- `/r` Given the same, When `GET /v1/admin/policy/versions/current` is called, Then it returns those values with the version id and `effective_from`.

#### approver-10.49 · Change the calorie floor
As the Nutrition approver, I draft a new floor and see how many current Targets it touches, so that I change a safety number knowingly. · brief §3.3, FR-058
- `/r` Given Policy v1, When the approver creates draft v2 and sets the floor to 1,300, Then the draft shows "1,200 → 1,300" and "37 active Targets are between 1,200 and 1,299 kcal" (de-identified count).
- `/r` Given the floor set to 950, Then "The floor cannot be below the hard stop (1,000 kcal)" appears beside the field and Publish is disabled.
- `/r` Given the floor set to 1,100, Then Publish asks for a written reason: "Outside the cited range (R33) — say why".

#### approver-10.50 · The hard stop never goes below 1,000
As the Nutrition approver, I can raise but never lower the hard stop below 1,000 kcal, so that no Policy version can produce a starvation plan. · R32, brief §19.3 · **Shared: Nutrition approver · Eater**
- `/r` Given draft v2, When the hard stop is set to 900, Then it is rejected with "The hard stop cannot go below 1,000 kcal (NIDDK planner limit)"; 1,050 is accepted while it stays ≤ the floor.
- `/r` Given `POST /v1/admin/policy/versions` with `hard_stop_kcal: 900`, Then 422 `POLICY_OUT_OF_BOUNDS`.
- `/r` Given hard stop 1,000 is live, When an eater's target proposal on the simulator would compute 980 kcal, Then onboarding offers no target below 1,000 and says why in neutral words.

#### approver-10.51 · Deficit cap and gain
As the Nutrition approver, I set the deficit cap as the smaller of a percentage and a kcal value, and the gain step, so that loss and gain proposals stay inside cited ranges. · R33, brief §3.3, §11.3
- `/m` Given cap = smaller of 15 % and 500 kcal, When maintenance is 3,600 kcal, Then the cap is 500 kcal (15 % = 540); when maintenance is 2,334.8 kcal (brief §11.3), Then the cap is 350.22 kcal.
- `/r` Given the kcal cap is set to 800, Then Publish asks for a reason (outside 500–750, R33); given −5 %, Then "Enter 0 or above" appears beside the field.
- `/r` Given gain is +10 %, Then the preview reads "Gain target = maintenance × 1.10".

#### approver-10.52 · GLP-1 protein-first values
As the Nutrition approver, I set the protein range and the no-added-deficit rule for eaters on GLP-1 medicines, so that their targets put protein first. · R35, R41 · **Shared: Nutrition approver · Eater**
- `/r` Given protein min 1.8 and max 1.6 g/kg, Then "Minimum must be at most the maximum" appears beside the fields.
- `/r` Given min 1.0 g/kg (below the cited 1.2), Then Publish asks for a reason.
- `/r` Given the live range 1.2–1.6 g/kg, When an eater who marked GLP-1 with weight 80 kg reaches the target step on the simulator, Then the proposal shows protein 96–128 g and no added deficit.

#### approver-10.53 · Tracking-only triggers and their wording
As the Nutrition approver, I keep the excluded groups locked, set the SCOFF cut-off within its validated range, and edit the tracking-only guidance in both languages, so that sensitive eaters get neutral help, never a restrictive plan. · R38, R32, R37, brief §1.3, §11.4, §14.2 · **Shared: Nutrition approver · Eater**
- `/r` Given the triggers table, Then "Pregnancy" and "Breastfeeding" show locked "always tracking-only (out of scope)", and the SCOFF cut-off accepts 1 or 2 and rejects 3 with "Above the validated cut-off (≥2)".
- `/r` Given the English guidance is edited and the Arabic is not, When Publish is pressed, Then it is blocked with "Update the Arabic text too".
- `/r` Given Preview is pressed, Then the guidance shows in an iPhone-width frame in English and in Arabic RTL, as the eater sees it on Today.

#### approver-10.54 · The energy-mismatch threshold, evaluated before it changes
As the Nutrition approver, I see how many published Foods a new threshold would flag before I publish it, so that the threshold is "configurable and evaluated" as the brief requires. · brief §10.2, FR-030
- `/r` Given the live ">10 % and >10 kcal", When the approver drafts ">8 % and >8 kcal", Then the preview reads "Flagged Foods: 171 now → 214 with this draft" and lists the 43 newly flagged Foods (synthetic counts).
- `/r` Given 0 % or a negative kcal, Then "Enter a value above 0" appears beside the field.

#### approver-10.55 · Component-sum tolerance and the clarification limit
As the Nutrition approver, I set the component-sum tolerance and the clarification limit within the brief's bounds, so that eaters' Units are checked consistently and never asked more than two questions. · FR-023, FR-035, FR-051 · **Shared: Nutrition approver · Eater**
- `/r` Given the clarification limit is set to 3, Then it is rejected with "At most two questions per pass (FR-035)"; 1 is accepted.
- `/r` Given a component-sum tolerance of 2 % is live, When an eater on the simulator saves a Composite measured at 6.9 g whose components sum to 7.1 g (2.9 % over), Then the Unit editor shows the component-sum error.
- `/r` Given the planner-increments row, Then it reads "Whole by default; halves only when the eater enables them (FR-051)" and offers no setting that makes halves the default.

#### approver-10.56 · Retention values
As the Nutrition approver, I can shorten media retention and cannot lengthen it past what the privacy policy discloses, so that a Policy change never breaks a promise to eaters. · FR-078, NFR-13, R5 · **Shared: Nutrition approver · Auditor** (ownership conflict, §7)
- `/r` Given raw scans 30 days, When the approver drafts 14 days, Then the preview reads "Scans older than 14 days that eaters have not saved will be deleted at the next deletion run" and asks to confirm.
- `/r` Given raw scans 60 days, Then Publish is blocked with "Longer than the privacy policy discloses — needs the privacy reviewer's sign-off".

#### approver-10.57 · Publish with an effective date and a reason; schedule; cancel
As the Nutrition approver, I publish a Policy version with a reason and an effective-from time, or schedule it and cancel it before it starts, so that a safety change lands when I mean it to. · FR-058, FR-071
- `/r` Given draft v2, When Publish is pressed, Then a dialog requires a reason and an effective-from (default now; a future date and time shown in the approver's time zone and UTC), shows the full diff, and Publish is its only main action.
- `/r` Given effective-from 2026-10-15 00:00 UTC, Then v2 reads "Scheduled", v1 stays "Live", and "Cancel schedule" returns v2 to draft.
- `/r` Given v2's time has passed, When `GET /v1/admin/policy/versions/current` is called, Then it returns v2, and a new target proposal uses the 1,300 floor.

#### approver-10.58 · Existing Targets and past days are not rewritten
As the Nutrition approver, I know a new Policy never rewrites approved Targets or past Days, so that eaters' history stays true. · FR-058, FR-071 · **Shared: Nutrition approver · Eater**
- `/s` Given an eater's Target of 1,250 kcal approved on 2026-09-20 and v2 (floor 1,300) live from 2026-10-15, When the Day reports for 2026-10-01 to 2026-10-14 are rebuilt, Then each shows target 1,250.
- `/r` Given that eater opens Today on the simulator after v2 is live, Then a calm notice says the reviewed minimum changed and offers "Review my target"; the Target is not changed until the eater approves one.

#### approver-10.59 · Roll back by publishing earlier values
As the Nutrition approver, I roll back by creating a new version from earlier values, so that History never loses what was live and when. · brief §3.3 ("centrally versioned")
- `/r` Given v2 is live, When the approver chooses "Restore v1 values", Then draft v3 is created with v1's values (v2 is not edited), and publishing it uses the same dialog.
- `/r` Given v3 is published, Then History lists v1, v2 and v3, each with its effective window.

#### approver-10.60 · Two approvers draft at once
As the Nutrition approver, I am told when someone published while I was drafting, so that I never overwrite a newer Policy. · brief §18 (expected revision)
- `/r` Given approvers A and B each drafted from v2 and A published v3, When B opens their draft, Then it reads "Based on v2; v3 is now live — review the differences" and shows v2 → v3 next to v2 → B's draft.
- `/r` Given B publishes without rebasing, Then 409 `STALE_REVISION` returns v3 as current.

#### approver-10.61 · Policy history and differences
As the Nutrition approver, I read every Policy version with who published it, why and when it applied, so that any target can be explained later. · FR-082 · **Shared: Nutrition approver · Auditor**
- `/r` Given versions v1–v3, When History → Policy opens, Then each row shows version, effective window, staff id and reason; selecting two shows their differences.
- `/r` Given the auditor's read-only token, When it calls `GET /v1/admin/policy/versions`, Then 200; `POST` returns 403 `FORBIDDEN_ROLE`.

#### approver-10.62 · Sign the launch nutrition-policy review
As the Nutrition approver, I sign the nutrition-policy review on Policy v1, so that the launch gate records a qualified review. · FR-082, brief §11.4, §23.1
- `/r` Given every Policy v1 value is set, When the approver presses "Sign nutrition-policy review", Then v1 reads "Reviewed by staff A-07 on 2026-10-01 — nutrition-policy review (FR-082)" and the launch-gates panel shows that gate as met.
- `/r` Given component-sum tolerance is still unset, Then the sign button is disabled and the panel lists "Component-sum tolerance — not set".

### G · Leave a trail

#### approver-10.63 · Every approver action is audited
As the Nutrition approver, every publish, retire, reject, claim, take-over and sign-off writes one audit event, so that anyone reviewing the reference later can follow it. · FR-081, FR-082 · **Shared: Nutrition approver · Auditor**
- `/s` Given any of those actions commits, Then exactly one audit event holds staff id, role, action, object id and version, reason and UTC time, and no eater identifier.
- `/r` Given the auditor opens the trail filtered by the approver's staff id, Then the publish of فول مدمس v1 is listed with its reason.

#### approver-10.64 · Sources & licences, generated from what is published
As the Nutrition approver, I rely on the app's Sources & licences page being generated from published licences, so that every required citation and attribution is shown without a hand-kept list. · F1, F19, F8 · **Shared: Nutrition approver · Eater**
- `/r` Given published Foods under CC0 (FDC) and CC BY 4.0 (the F19 study), When an eater opens Settings → Sources & licences on the simulator, Then it shows "U.S. Department of Agriculture, Agricultural Research Service. FoodData Central" and the study's attribution line.
- `/r` Given the same, When `GET /v1/reference/attributions` is called, Then it returns the list built from published licences.

---

## 4 · The experience this persona needs

- **Device and place.** A desk, a large screen and a keyboard, in focused batches (fractional reviewer, brief §23.1). Width ≥1,440 px is the design target (`assumption`); every read flow and queue triage also work at ~390 px with no horizontal page scroll — wide tables scroll inside their own frame and the detail panel becomes a full page.
- **The moment that matters.** Pressing **Publish** — on a Food, a Tier B Recipe record, an Alias or a Policy version. That is the moment a number becomes the truth for every eater who logs it.
- **The feeling it must leave.** Certain and unhurried: "I can see exactly what will change, for how many eaters, and that nothing in the past moves."
- **The matching style.** Dense and fast, quiet and exact. Tables with frozen headers and first column (AP7), tabular right-aligned numbers, a detail panel beside the table, the APG grid keys and switchable single-letter shortcuts (AP8–AP10), no animation on repeated actions, colour never alone (diffs carry "changed" text), Arabic names direction-isolated (AP11) with the dialect tag next to every Arabic word. One main action per view (Publish or Approve); destructive actions (Retire, Reject, Take over) are secondary and need a reason.

## 5 · Care questions this persona raises, answered as requirements

Size is platform, so every group applies on every console screen (`care.md`, "By size").

**1 · Does it deserve to exist, and where does it live**
- Each area does one job: Review (decide flagged items), Foods (curate records), Recipes (calculate dishes), Aliases (names), Policy (safety numbers), History (what was live, when). Places sit in the navigation; Publish, Retire and Approve sit beside the record they act on.
- What we said no to: editing a published version in place (versions only, FR-014); free bulk-publish of a whole table without per-record checks (AP2); copying values from all-rights-reserved tables (F12–F17, F23); OFF rows in the reference (F8); per-eater diary views (FR-081).
- Settings that need not exist: none per approver except language, numerals and shortcut on/off; sort and filter are remembered per browser.

**2 · How it is found and understood**
- Every screen answers where am I (title names the area and record, e.g. "Foods · فول مدمس v2 (draft)"), what can I do (one main verb button), what just happened (inline status), how do I get out (Esc / Back keep the draft).
- The map's words everywhere: Food, Recipe, Alias, Evidence, Policy, Unit, Entry, Day, Target; badge names exactly as the app shows them. Dialect is always EG · Gulf · MSA.
- Nothing typed that the system knows: FDC fields, Wikidata labels and extracted label fields arrive filled; the approver confirms.
- The eye lands first on the number that changes and its delta; status (draft, scheduled, live, retired, superseded) is a text label in a fixed column.

**3 · How it feels**
- Every action answers at the instant of the key press: "Publishing…", then "Live since 11:05". A failed check is shown next to the field it concerns.
- Message loudness follows risk: quiet inline status for saves; a dialog only for Publish of a Policy version and for Retire.
- Values with no provably right answer — page size, placeholder delay (300 ms in 10.6), shortcut keys — are tried in the served console and chosen on purpose.
- The console reopens where the approver left it (filter and focused item, per browser).

**4 · When it goes wrong, is empty, or is slow**
- Empty queue says so and offers the next action; a failed load is never shown as empty (10.8).
- Long jobs (Tier A import) show honest progress with Cancel; a failed import changes nothing (10.22–10.23).
- Errors sit beside the field, say how to fix it, and never blame (10.24, 10.25, 10.47, 10.49).
- Undo is a new version (Restore v1 values, 10.59); the only warnings are before a Policy publish and a Retire.
- A half-filled draft survives expiry, closing the tab and offline (10.3, 10.4).
- No network: last data shown with a quiet banner; Publish waits; nothing auto-sends on reconnect (10.4).

**5 · The inside the user never sees**
- Code, data and logs use the same nouns as the screen (Food version, Alias, Policy version, review item).
- Logs carry staff id, record id and version, never eater identifiers or label photos; review items carry counts only (10.1, 10.18).
- Seed and test data are realistic and synthetic: long Arabic names with tashkeel, mixed scripts, Arabic-Indic digits, three dialects.
- Collect only what curation needs: no submitter identity in a Label submission (10.15).
- The console claims only what it does: "recipe-calculated" never shows as "measured" or "exact" (FR-029, FRD §20.1).

**6 · Inclusion**
- Whole flow by keyboard (AP9 2.1.1); visible focus ring; single-letter shortcuts can be turned off (AP9 2.1.4).
- 4.5:1 contrast in light and dark; diff and status never by colour alone.
- Screen reader: tables use grid semantics with header cells; every control has a verb label that stays current (e.g. "Publish v4").
- Largest browser text size and 200 % zoom: nothing clips; tables scroll inside their frame.
- Arabic console: layout mirrors; numbers, ids, barcodes and clocks keep their order; each paragraph aligns by its own language.
- Nothing disappears on a timer; claims do not expire while the approver reads.

## 6 · Proposed names for the model phase to confirm

These do not exist in the map. This lens built them only from map nouns and FRD §18 conventions.
- Console areas: **Review · Foods · Recipes · Aliases · Policy · History**; noun **review item** for a queue entry; review item types **Estimated analogue · Energy mismatch · Label submission · Unmatched name · Ingredient updated**.
- Admin API (FRD §18 style): `GET /v1/admin/review-items`, `POST /v1/admin/review-items/{id}/claim|resolve`, `GET|POST /v1/admin/foods`, `POST /v1/admin/foods/{id}/versions`, `POST /v1/admin/food-versions/{vid}/publish|retire`, `POST /v1/admin/recipes`, `GET|POST /v1/admin/aliases`, `GET|POST /v1/admin/policy/versions`, `POST /v1/admin/policy/versions/{v}/publish|cancel`, `GET /v1/admin/reference-releases/{id}`, `GET /v1/admin/metrics/evidence`, `GET /v1/reference/attributions`.
- Typed errors added to FRD §18.2: `FORBIDDEN_ROLE`, `LICENCE_REQUIRED`, `LICENCE_NOT_PUBLISHABLE`, `ALIAS_CONFLICT`, `CYCLE_DETECTED`, `YIELD_MISSING`, `POLICY_OUT_OF_BOUNDS`. These are reused as written: `STALE_REVISION`, `MASS_BALANCE_ERROR`, `SOURCE_BASIS_UNKNOWN`.
- Dialect storage codes `arz` / `afb` / `arb` behind the display tags EG / Gulf / MSA (AP12).

## 7 · Conflicts for the model phase

1. **"Recipe" has two owners.** The eater's Recipe is private, WF-2. The approver's Tier B Recipe record is a public Food version whose Evidence is recipe-calculated. Proposal: one calculation engine, and an owner field (personal / reference).
2. **Retention ownership.** The map lists retention in the approver's Policy (§1.6). Brief §23.2 says "Privacy/security reviewers approve consent, retention, access". Who signs a retention change: approver, privacy reviewer, or both? (10.56 blocks lengthening for now.)
3. **A raised floor against approved Targets.** FR-058 and FR-071 say Targets are versioned and never silently changed, so a Target already below a newly raised floor would stay (10.58). Safety argues for an immediate change when the *hard stop* rises. Decide which values apply at once and which wait for the eater's review.
4. **Label photo promotion against eater privacy and retention.** Promoting a label needs a separate consent purpose (R22: "A separate consent shall be obtained for each Processing purpose"; brief §19.2: raw evidence for quality review "requires explicit consent and restricted roles"). It also needs the photo kept past the 30-day raw-scan window (FR-078) as reference Evidence. Eater and approver needs meet here.
5. **Unmatched-name signal against diary privacy.** 10.18 aggregates free text eaters typed. The minimum count of distinct eaters (5 here) is an `assumption` and needs a privacy decision (FR-080 "de-identified").
6. **No Evidence badge for "reference database".** FRD §20.1's badges are label-verified, recipe-calculated, measured unit + reference nutrition, estimated analogue and user-defined. None of them names a Tier A Food on its own. The approver needs a source tier (A database / B recipe-calculated / label). The eater needs one badge per Entry. Name the mapping.
7. **Vocabulary gap: taking a Food version out of resolution.** "Retire" (10.29, 10.46) is not in the map. Void and Restore belong to Entries. Also decide whether logging a Unit on a retired version keeps working on its snapshot or is blocked.
8. **Self-approval.** A single fractional approver publishes alone. INFOODS asks for "a second compiler and/or … computerized validation tests" (AP2). This lens relies on the automated checks. Whether the roles screen should offer an optional second approver (Platform admin's roles) is open.
9. **Hard stop as an editable value.** The map lists the 1,000 kcal hard stop among approver-versioned values. This lens makes 1,000 a code-fixed minimum the Policy can raise but not lower (10.50, R32). That limits C1's "admins configure values".
10. **Who owns the evaluation set's reference values.** NFR-10 needs 200 target-cuisine cases and 100 bilingual labels with ground truth. That sits with the Platform admin's evaluation, but the reference values are nutrition work. Assign the owner.
11. **Support sees a submission's status.** An eater may ask support why a label was rejected. Support then needs the review item's status and reason, but not the photos. Set the read scope with the Support agent lens.

## 8 · Coverage

| brief line | stories |
|---|---|
| FR-010 preparation variants | 10.12, 10.25, 10.36 |
| FR-012 measured / declared / estimate | 10.24 |
| FR-014 immutable versions | 10.14, 10.17, 10.28, 10.30 |
| FR-015 aliases EN/AR | 10.20, 10.41–10.47 |
| FR-023 acyclic, tolerance | 10.37, 10.55 |
| FR-025 resolver order | 10.10, 10.11, 10.39 |
| FR-026 nutrient vector provenance | 10.21, 10.26 |
| FR-027 label fields, missing ≠ zero | 10.15, 10.21, 10.24 |
| FR-028 / FR-029 recipe and yield | 10.31–10.33 |
| FR-030 energy mismatch | 10.13, 10.14, 10.54 |
| FR-031 scope before recalculating | 10.28, 10.39, 10.46 |
| FR-034 uncertain digits | 10.15 |
| FR-035 two-question budget | 10.42, 10.55 |
| FR-051 increments | 10.55 |
| FR-058 / FR-071 versioned targets | 10.57, 10.58 |
| FR-077 / FR-078 media and retention | 10.15, 10.56 |
| FR-080 console | 10.1, 10.6, 10.18, 10.19, 10.48 |
| FR-081 privilege separation | 10.2, 10.61, 10.63 |
| FR-082 launch nutrition-policy review | 10.61, 10.62 |
| brief §3.3 policy ownership | 10.48–10.62 |
| brief §6.2 restaurant serving meaning | 10.24 |
| brief §10.2 mismatch threshold | 10.13, 10.54 |
| AT-03 / AT-05 / AT-06 / AT-08 / AT-12 / AT-15 / AT-28 / AT-30 | 10.37 / 10.12 / 10.31 / 10.15 / 10.14 / 10.13 / 10.15 / 10.16 |

**Shared stories:** with the Eater — 10.10, 10.12, 10.14, 10.15, 10.16, 10.18, 10.28, 10.39, 10.41, 10.42, 10.46, 10.50, 10.52, 10.53, 10.55, 10.58, 10.64. With the Auditor — 10.56, 10.61, 10.63. With the Support agent and Platform admin — 10.2.

Totals: 64 stories, 171 acceptance lines (144 runtime, 16 system, 11 module).

## Lens verdict (2026-10-01)

**fail** — 23 defects.

Checked by the lens verifier against `_lens-verifier-brief.md`, `_lens-brief.md`, `way/blueprint.md` §0–§1, `way/brief/frd-v1.0.md`, `way/research/r1-*.md` with both refutations, and `care.md`. Counts confirmed: 64 stories, 171 acceptance lines (144 `/r`, 16 `/s`, 11 `/m`), and every story has at least one `/r` line. The AP1–AP12 quotes were re-opened on 2026-10-01 with a generic User-Agent, and all are on the cited pages except where defects 15–16 say otherwise. No refuted or doubtful finding (R34, F10, F18, F31, C6, C8, C26, C27, C39, C47, C50, C54) is cited directly. **Ids pass:** `approver-10.1` to `approver-10.64` run without gaps, and journey 10 = WF-10.

### Traced

1. **10.3, 10.7, 10.8, 10.22, 10.23, 10.29, 10.30, 10.32, 10.38, 10.44, 10.45, 10.47.** The trace line names no map element and no FR, AT or NFR line. It cites only care.md or research: 10.7 "· AP8, AP9, AP10"; 10.32 "· AP2"; 10.30 "· AP7 ("Compare data")"; 10.29 "· F14 (vocabulary gap "Retire", §7)". These stories sit inside WF-10 steps, so in substance they are not drift, but each one needs its WF-10 step, interaction row or FR line. 10.29's Retire is an operation that neither the map nor the FRD has.
2. **10.34.** It says the record "requires a note, because the gap exceeds the Policy's 10 %". No cross-check gap threshold appears in map §6's Policy list or in 10.48's Policy table. This Policy value is invented and has no source or `assumption` label.
3. **10.10 `/s` and 10.12 `/s` against 10.18 (FR-080 "de-identified").** 10.10 opens an item that carries one eater's typed term: "term "تمر صقعي" … `eaters_affected` 1, `entries_7d` 1". 10.18 holds back the same kind of diary text below 5 distinct eaters: "Given only 3 eaters used a term, Then no item exists for it". That gives two de-identification rules for eater-typed food names. §7.5 sets the threshold for unmatched names only.

### Complete

4. **WF-10 done-when: "an approver approves a Tier B recipe record (e.g. فول مدمس) with its evidence and licence".** 10.31 and 10.39 never set or show the فول مدمس record's licence. 10.31 only says "Then the record shows 140 kcal/100 g, per-100 g macros …, Evidence "recipe-calculated" and the formula". No line says which licence a Tier B record carries, or that 10.26's licence gate applies to Recipe records. 10.40 only shows a licence column in the Launch set.
5. **FR-029: "With missing cooked yield or uncertain absorbed oil, show an estimate/range and the material assumption".** No story covers a fried dish whose absorbed oil is uncertain. 10.32 only ticks "Absorbed or added fat included". 10.33's cited-yield path shows the assumption but no range, and no line labels a range as heuristic (FRD §20.1).
6. **FRD §10.2, rows "Fiber and net carbohydrate — Preserve source total carbohydrate and fiber conventions" and "Ingredient mass — … must not double-count subcomponents".** No story records whether a Food's carbohydrate is total or available. 10.25 mixes the two conventions: its `/m` line uses "available carbohydrate 20, fibre 3", and its `/r` line uses "carbohydrate 73, fibre 10 … Sum of proximates 187 g". If 73 is total carbohydrate, fibre is counted twice.
7. **FR-012: "Distinguish measured values, declared values, and estimates".** §8 claims "FR-012 measured / declared / estimate | 10.24", but no 10.24 line shows a measured, declared or estimate marker.
8. **FR-026: "AI reasoning alone cannot be marked label-verified" (with FR-034's confirmation).** 10.15 covers only the happy path: "When the approver confirms each highlighted digit and presses Approve". No line shows Approve disabled while a highlighted digit is unconfirmed, or the API refusing label-verified without the approver's confirmation.
9. **FRD §19.2: "Access to raw evidence for quality review requires explicit consent and restricted roles"; FR-077: "Use short-lived signed access".** No line proves any of these for Label submission photos:
   - they open only for the approver role (a support, admin or auditor token gets 403);
   - they are served through a short-lived signed URL;
   - they reach the queue only with the eater's review consent.

   10.15 cites FR-077 but checks only "cropped, metadata stripped". §7.4 raises the consent question, but the approver-side gate has no acceptance line.
10. **FRD §3.3: "loss (15% below estimated maintenance) … with selectable conservative ranges".** No story edits the loss default or sets the ranges an eater may choose from. 10.48 only reads "loss default 15 %", and 10.51 sets only the cap and the gain.
11. **Map §6 Policy "retention (raw scans 30 days, audio 24 h)" / FR-078.** 10.56 covers raw scans only. The audio 24 h value has no line for editing it, for its bound, or for invalid input.

### Observable

12. **10.12 `/s` and 10.29 `/r`.** Their Givens cannot arise in the served product:
    - 10.12 has "a Unit references "tuna in oil, drained" and only "tuna in water" exists". Under FR-010, a Unit must reference a Food version that exists.
    - 10.29 starts from "Food "Barley, grains" v1 with water 88 g and 335 kcal per 100 g" as published. 10.25's checks and 10.26's NNI cross-check-only gate would block that record.

    Either state these as seeded legacy records or choose fixtures the product can reach.
13. **10.22 `/r` (3rd) "new resolutions use 15.5" and 10.57 `/r` (3rd) "a new target proposal uses the 1,300 floor".** Neither line names the screen or interface where a verifier would observe the result.
14. **10.5 `/r` (2nd) "the console language is switched to العربية", 10.7 `/r` (2nd) "under Shortcuts", 10.62 `/r` (1st) "the launch-gates panel shows that gate as met".** None of these screens has a place in the console. 10.2 lists only "Review, Foods, Recipes, Aliases, Policy and History", and §6 names no settings or launch-gates area.

### Sourced

15. **§1.1 AP4.** The heading says "its curators work in a desktop program", but the quote is about NDSR, "a Windows-based dietary analysis program designed for the collection and analyses of 24-hour dietary recalls, food records, menus, and recipes". That is a tool for researchers, not for NCC's curators, and §1.2 "Where" leans on it. The parenthetical "(the source Cronometer calls its best)" has no quote or link. Cronometer article 360042550452 does say "our highest quality data source (NCCDB)", but the lens does not quote it.
16. **§1.1 AP5 and AP6.** AP5 gives Zendesk article ids and dates but no links. AP6 quotes "A general factor of 2 calories per gram for solub[le fibre]". eCFR 101.9 reads "soluble non-digestible carbohydrates", so the bracket changes the term.
17. **10.10.** It cites "F-implication 7". That implication's route to a صقعي record ("until an approved SFDA or literature record exists (F5, F11, F31)") rests on F31, which r1-refute-a marks doubtful. The lens header says F31 is not cited.

### Vocabulary

18. **One thing has five names.** The map's term is "Tier B recipe record". The lens uses:
    - "Tier B Recipe record" (10.31);
    - "Recipe record" (10.8 "Add a Recipe record", 10.28);
    - "Tier B record" (10.32, 10.37, 10.38);
    - "recipe record" (10.39);
    - "Food" for the same kind of dish (10.35 "Food "مرقوق · Margoug""; 10.36 "both exist as separate Foods").
19. **One thing has three names:** "Tier A releases" (10.22 area), "FDC release" (10.22 story), and `reference-releases` (10.23 API, §6).
20. **10.35.** "Evidence "recipe-calculated (ESHA, cited)"" extends the fixed badge set (map §6: "Fixed vocabulary: the evidence badge set"). The calculation method belongs in the source, not in the badge.
21. **Names used in stories that are neither map words nor in §6's proposed list:**
    - the statuses "Superseded" (10.14, 10.17), "Scheduled" and "Live" (10.57), and "Retired" (10.29, 10.46); only Retire is raised, in §7.7;
    - "Take over" (10.9);
    - "Launch set" (10.40);
    - "Review → Metrics" (10.19);
    - "Sources & licences" (10.64), a new Settings screen for the eater;
    - "launch-gates panel" (10.62);
    - "Shortcuts" (10.7);
    - "Cross-check" as an attachment kind (10.26, 10.34).

### Experience

22. **§5 groups 3–4 disagree with the stories.** §5.3 says "a dialog only for Publish of a Policy version and for Retire", and §5.4 says "the only warnings are before a Policy publish and a Retire". The stories do otherwise:
    - 10.28 puts a Publish/Cancel preview before every Food version publish;
    - 10.11 says "the editor warns";
    - 10.29's Retire shows no dialog.
23. **§4: "every read flow and queue triage also work at ~390 px".** Profile §0 asks for the "admin console proved in a browser at desktop and ~390 px". No acceptance line in any story exercises ~390 px, and 10.1 fixes "a 1,440 px browser". Care group 6's "Is every target big enough to hit" and its reduced-motion question are neither answered nor marked not applicable, although the narrow console is used by touch.
