# Eater — journeys WF-2 Define a Unit and WF-4 Capture and analyse

Written 2026-10-01 by the eater lens; fix round 1 the same day (see the foot of the file). This file does steps 2, 3 and 5 of `way/personas/_lens-brief.md` (the journeys, the micro stories with acceptance and ids, the shared stories and the conflicts) for two workflows. It does not redo steps 1 and 4: research cycle 2 and the experience are in `way/personas/eater/research.md`, and this file cites that file's ids: findings **E1–E44** and experience requirements **EX-01–EX-44**.

**Read first:** `way/blueprint.md` §0–§1 (the map), `way/vocabulary.md` (delta D2: the one name for every state, error and place; binding), `way/brief/frd-v1.0.md` (binding: FR-009…FR-039, §2.2–§2.4, §4–§7, §14, §16–§18, AT-01…AT-08, AT-12, AT-13, AT-16, AT-26–AT-28, AT-30, AT-32), `way/research/r1-*.md` as corrected by `r1-refute-a.md` and `r1-refute-b.md`. No refuted or doubtful cycle-1 finding is cited. No outside source was opened for this file and nothing in it is new research. A choice made by this lens is marked `proposed`; anything reasoned rather than sourced is marked `assumption`.

## How to read this file

- **Ids.** `eater-2.n` = WF-2 Define a Unit; `eater-4.n` = WF-4 Capture and analyse. Each story has a trace line: the map or FRD line it serves first, then the research ids. Stories added in fix round 1 take the next free number and sit in the step they belong to.
- **Layers.** `/m` module: a pure function's test (nutrition core, quantity parser, validator). `/s` system: components together (API + resolver + ledger + the analyzer mock). `/r` runtime: observed in the served product, either the **iOS app on the simulator** (iPhone 17e is the smallest, iPhone 17 Pro Max the largest; P34) or the **API over HTTP**. Every story has at least one `/r` line.
- **Places** (vocabulary D2): tabs **Today · Capture & Plan · My Units · Progress**; screens **Analysis review**, **Unit editor**, **Meal planner**; **Settings** sections **Goals · Food rules · Activity · Units & language · Privacy · Export**. FRD words used as written, pending a dated delta (§5 conflict 10): the **quick-add control** (FRD §2.1), the capture **modes Meal · Unit · Label · Recipe** inside **Capture & Plan** (FRD §14), the **count stepper** (FRD §14), the **correction preview** (FRD §2.6) and **Source details** (FRD §14, FR-046).
- **States** (vocabulary D2). Analysis: Processing → Needs answers → Ready for review → Approved · Discarded · Failed, and Pending (captured offline, not sent). Unit, Composite and Recipe: Draft → Saved (version n) → Archived. Entry: Pending → Confirmed → Corrected · Voided → Restored. Consent: Given · Withdrawn. Label submission (approver lens): Proposed → In review → Approved · Rejected. "Draft" is used only for a Unit, a Composite or a Recipe, never for an Analysis. A Failed Analysis stays Failed; "Try again" creates a new Analysis from the same photo (admin-10.34).
- **Evidence badges** (map §1.4): label-verified · recipe-calculated · measured · estimated analogue · user-defined. **Value basis** of one amount (FR-012): measured · declared · estimated (§5 conflict 2).
- **AI in tests.** The Gemini adapter runs as its realistic mock until the owner's cutover (blueprint §0 line 6). Every AI acceptance line scripts the mock's output for its fixture, so the line is reproducible and can fail.
- **Copy.** Words in quotes are proposed screen copy. Arabic labels are fixed once in the string catalogue (EX-08). Copy never contains requirement ids, error codes or "we" (EX-23).
- **Interfaces.** FRD §18 endpoints are used as written. Endpoints and fields marked *proposed* are listed in §6. Error codes are only those in vocabulary D2.

## Fixtures (all synthetic)

These values are calibration fixtures in the FRD's sense (§5.2: "Their historical calorie estimates require source verification"). They are not nutrition data and must never become product data.

### The shared eater seed

The Saved Units of Mona, Faisal and Sam below carry **the same names and values as `wf3-wf6.md` §2.2**, and the kabsa Units carry the values of `wf5-wf7-wf8.md` §0.2. Where `wf5-wf7-wf8.md` gives other values for the same Unit, §5 conflict 15 lists them; this file follows `wf3-wf6.md`.

**Eaters**

| eater | language · digits | dialect | time zone | Day boundary | start state |
|---|---|---|---|---|---|
| **Mona** `eater-synth-mona` | Arabic · Arabic-Indic | EG | Africa/Cairo | 03:00 | the Saved Units below; Day Wed 30 Sep 2026 with 6 Entries, 1,098 kcal (`wf3-wf6.md` §2.3) |
| **Faisal** `eater-synth-faisal` | Arabic · Western | Gulf | Asia/Riyadh | 03:00 | the Saved Units below; Food rules: "Laban: unsweetened", "Ghee: 3 g per fried egg" |
| **Sam** `eater-synth-sam` | English · Western | not set | Europe/London | 00:00 | the Saved Units below |
| **Huda** `eater-synth-huda` | English · Western | not set | Africa/Cairo | 00:00 | fresh install, account made, no Units, no Food rules; Day 1 Oct 2026 holds one typed Entry "Bread, baladi 80 g · 200 kcal" (Day revision 1) |
| **Hala** | Arabic · Arabic-Indic | EG | Africa/Cairo | 00:00 | trial diary T1 of `wf1-wf9.md` (no account; Units «قرصة جبنة», «شاي بلبن», «لقمة عيش») |
| **Nadia** `eater-synth-nadia` | English · Western | not set | Europe/London | 00:00 | fresh account, no Units |
| **Khalid** `eater-synth-khalid` | Arabic · Western | Gulf | Asia/Riyadh | 00:00 | fresh account, no Units |

**Reference Foods** (Approved, in the test seed; values per 100 g unless stated)

| Food | kcal | protein g | carbohydrate g | fat g | note |
|---|---|---|---|---|---|
| White cheese | 231.5 | 33.3 | 9.3 | 7.4 | version 3 (version 4 is approved in 2.36) |
| Olive oil | 900 | 0 | 0 | 100 | |
| Ghee | 900 | 0 | 0 | 100 | |
| Bread, baladi | 250 | 8.75 | 50 | 1.25 | |
| Bread, shami | 260 | 9 | 52 | 1.5 | |
| Toast, white | 270 | 9 | 50 | 3.5 | |
| Barley flour | 350 | 10 | 75 | 2 | |
| Milk, whole | 60 | 3.2 | 4.6 | 3.2 | per 100 g and per 100 ml (approved density 1.00) |
| Sugar | 400 | 0 | 100 | 0 | |
| Honey | 304 | 0.3 | 82 | 0 | |
| Dates, Saqai | 300 | 2 | 75 | 0.4 | Aliases "Saqai date", «صقعي» (approver-10.41) |
| Dates, Sukkari | 300 | 2.5 | 72.5 | 0 | |
| Egg, boiled | 155 | 13 | 1 | 11 | |
| Tuna, canned in water, drained | 116 | 25.5 | 0 | 0.8 | no "in oil" record exists in the reference |
| Laban drink | 60.8 | 3.2 | 4.8 | 3.2 | per 100 ml only; Gulf Alias «لبن» points here (approver-10.42) |
| Yogurt, plain | 61 | 3.5 | 4.7 | 3.3 | per 100 g only, no density |
| Kabsa rice | 170 | 3.5 | 28 | 4.8 | the record the resolver offers as an analogue for kabsa rice on a plate |
| Molasses, sugarcane «عسل أسود» | 290 | unknown | 72 | 0 | label-verified; protein not printed |
| Brewed tea | 0 | 0 | 0 | 0 | per 100 ml |

EG Alias «لبن» → Milk, whole (approver-10.42). فول مدمس v1 is an Approved Tier B recipe record (approver-10.39).

**Own records** (each eater's private, label-verified Foods): Mona's "Tuna in oil, drained" from her can's label (200 kcal, protein 29 g, fat 8 g per 100 g) and "Milk, whole" from her carton's label (as the reference row); Faisal's and Sam's "Laban drink" from their bottles' labels (as the reference row, per 100 ml).

**Saved Units** (each eater's own; per one)

| owner | Unit · Arabic | amount kind · structure | definition | kcal | P / C / F g | Evidence |
|---|---|---|---|---|---|---|
| Mona | cheese spoon · معلقة جبنة | spoonful · Composite | White cheese 5.4 g + Olive oil 1.5 g = 6.9 g (AT-02) | 26.0 | 1.8 / 0.5 / 1.9 | measured |
| Mona | cheese bite · قرصة جبنة | bite · the with-bread variant of cheese spoon | cheese spoon + Bread, baladi 8 g (FRD §2.2) | 46.0 | 2.5 / 4.5 / 2.0 | measured |
| Mona | bread bite · لقمة عيش | bite · simple | version 1: 8 g Bread, baladi (version 2 in 2.44: 9 g) | 20.0 | 0.7 / 4.0 / 0.1 | measured |
| Mona | egg bite · لقمة بيض | bite · simple, eaten by dipping (2.22) | 12 g Egg, boiled | — | — | measured |
| Mona | meat bite · لقمة لحمة | bite · simple, eaten by dipping, its own bread 5 g (2.24) | — | — | — | measured |
| Mona | glass of milk · كوباية لبن | cup · simple | 250 ml Milk, whole (her label) | 150 | 8 / 11.5 / 8 | label-verified |
| Mona | my teaspoon · معلقتي الصغيرة | spoonful · simple | 3.75 g Sugar | 15.0 | 0 / 3.75 / 0 | measured |
| Mona | glass of milk tea · كوباية شاي بلبن | cup · Composite | Brewed tea 150 ml + Milk, whole 50 ml + 2 × my teaspoon | 60.0 | — | measured |
| Mona | talbina spoon · معلقة تلبينة | spoonful · of Recipe Talbina version 1 | 16 g; Talbina: Barley flour 40 g + Milk, whole 500 g + Sugar 10 g = 480 kcal, cooked yield 384 g (AT-06) | 20.0 | — | recipe-calculated |
| Mona | foul spoon · معلقة فول | spoonful | — | 30 | 2.0 / 4.0 / 0.7 | recipe-calculated |
| Mona | baladi loaf · رغيف بلدي | piece · simple | one loaf | 230 | — | measured |
| Mona | tuna spoon · معلقة تونة | spoonful · simple | 23 g Tuna in oil, drained (her label) (FRD §5.1, §24.1) | — | — | — |
| Mona | tuna bite · لقمة تونة | bite · Composite, bread inside | Tuna in oil, drained 6.8 g + Bread, baladi 8 g | — | — | — |
| Faisal | cheese bite · لقمة جبن | as Mona's cheese bite | | 46.0 | 2.5 / 4.5 / 2.0 | measured |
| Faisal | cup of laban · كوب لبن | cup · simple | 250 ml Laban drink (his label) | 152 | 8 / 12 / 8 | label-verified |
| Faisal | Sukkari date · تمرة سكري | piece · simple | 8.0 g edible, mean of a weighed sample (FR-013) | 24 | 0.2 / 5.8 / 0 | measured |
| Faisal | kabsa rice spoon · ملعقة رز كبسة | spoonful · of a Recipe | 25 g | 42.4 | 0.9 / 7.0 / 1.2 | recipe-calculated |
| Faisal | chicken piece · قطعة دجاج | piece · simple | 60 g | 114.0 | 15 / 0 / 6 | measured |
| Sam | cheese bite | as Mona's cheese bite | | 46.0 | 2.5 / 4.5 / 2.0 | measured |
| Sam | cup of laban | 250 ml Laban drink (his label) | | 152 | 8 / 12 / 8 | label-verified |

**Policy version 1, In effect** (approver-10.48, approver-10.55): component-sum tolerance 2 %, clarification limit 2, energy mismatch above 10 % and above 10 kcal per serving, raw scans kept 30 days unless saved, audio 24 h. **Registry** (admin lens): every task's Kill switch Off; image analyses hard limit 3 per eater per Day (the fixture of admin-10.40); upload bound 10 MB per image (*proposed* fixture value).

---

## 1 · Goals (the eater's, for these two workflows)

1. **Say once what my portion is, in my own words and amounts, and never explain it again.** FRD §1.1, §1.2 ("prefers 'six bites' to repeated gram entry"), §4.1; E2, E30.
2. **Every saved number can be reproduced and explained: parts, weights, sources.** FRD §2.2 ("not an unexplained aggregate"), FR-014, FR-026; E15, E16.
3. **A photo, a label or a sentence becomes something I approve, never food I did not eat.** FR-037, FR-039, FR-045, map §1.3 (Eater → AI analyzer row); E7, E19.
4. **Few questions and honest numbers: at most two questions, and ranges where the app cannot know.** FR-033, FR-035; E8, E17.
5. **It works in my language and dialect, by voice or thumb, with or without numbers on screen, and also when the AI or the network is down.** FR-036, FRD §7.2, AT-32, map §1.6 ("hide numbers"); E34, E42, E43.

---

## 2 · Journey 2 — Define a Unit (WF-2)

Map: "simple, composite (bread rules), or Recipe with weighed ingredients and weigh-the-pot cooked yield; by typing, scale photo or label photo; approve a version." Done-when: "'cheese bite' saved as 5.4 g cheese + 1.5 g oil + 8 g bread; a Recipe saved with a weighed cooked yield gives AT-06's numbers; My Units lists them; Today's total unchanged." Where: the kitchen counter with a scale, two hands likely (research §1, place 3, `assumption`), and the review one-handed. The moment: "It learned my bite and will never forget it" (research §3, WF-2).

| step | what the eater does | stories |
|---|---|---|
| A · start | open Create unit; avoid a second copy; keep half-built work | 2.1–2.3 |
| B · name it | choose the kind, name it by typing or voice, give it a picture | 2.4–2.6 |
| C · say which food | the exact food and preparation; no silent swap; where the numbers come from | 2.7–2.9 |
| D · measure it | grams, edible part, before/after, average piece, volume, ml on the scale, bad input | 2.10–2.16 |
| E · mixed portion (Composite) | parts, oil inside, sum check, no cycles, wet cereal | 2.17–2.21 |
| F · bread and preparation rules | dipped-bite rule, bread already inside, exceptions, variants, preparation defaults, eggs, precedence | 2.22–2.28 |
| G · Recipe | weigh ingredients and pot, prepared spoon, additions and discards, missing yield, cook again | 2.29–2.33 |
| H · numbers and sources | calorie-only, two energy values, a better source, restaurant serving | 2.34–2.37 |
| I · review and save | see everything, Save unit is not eating, find it in My Units | 2.38–2.40 |
| J · Aliases | Arabic, English, Latin letters, voice; my own name wins; collisions | 2.41–2.43 |
| K · versions | recalibrate, apply to chosen past Entries, archive, two devices | 2.44–2.47 |
| L · anywhere, anyone | offline, trial to account, Arabic and accessibility, model changes, hide numbers | 2.48–2.52 |

### A · Start

#### eater-2.1 · Create a Unit from My Units or from an empty Today
As the Eater, I tap "Create unit" in My Units, or "Make your first unit" on Today, so that I can say once what *my* bite is. · FRD §2.2, §14 (My Units "new unit"), map WF-2 · EX-03, EX-19, E2
- `/r` Given Huda has no Units, When she opens **My Units** on the iPhone 17e simulator, Then the screen reads "Make your first unit" with one button "Create unit" and shows no empty list or skeleton.
- `/r` Given Huda's **Today** shows no Unit tiles, When she taps "Make your first unit" there, Then the **Unit editor** opens at the same first step as from My Units: "What is it?".
- `/r` Given Huda has no Target, When the **Unit editor** opens, Then no step asks for a Target, a profile or Health access, and Save unit is reachable (FRD §3.2: "A user may create a food unit … before completing a weight-management plan").

#### eater-2.2 · Told when I already have it
As the Eater, when I start a Unit that matches one I already have, I am offered to recalibrate it, so that I don't end up with two "cheese bites" that log different numbers. · FRD §14 (My Units "duplicate candidate"), FR-014 · E15
- `/r` Given Mona's Saved cheese bite «قرصة جبنة» (version 1), When she types "cheese bite" or «قرصة جبنة» as the name of a new Unit in the **Unit editor**, Then a quiet note under the name reads "You already have cheese bite (version 1)" with "Recalibrate it" and "Keep both", and neither is pre-selected.
- `/r` Given she taps "Recalibrate it", When the editor switches, Then its header reads "cheese bite · new version 2" and every field holds version 1's values.
- `/r` Given she taps "Keep both" and leaves the same name, When she taps Save unit, Then the name field reads "Choose a different name" and nothing is saved; `POST /v1/units` with the name of one of her Saved Units returns 422 `VALIDATION_ERROR` naming the field `label`.

#### eater-2.3 · A half-built Unit survives
As the Eater, I can close the Unit editor or lose the app halfway, and my Draft is kept, so that a weighing session is never wasted. · vocabulary D2 (Unit "Draft"), map WF-2 · EX-25, care group 4
- `/r` Given the **Unit editor** holds "mixed peas spoon" with 2 of its 3 parts entered, When Mona closes the app from the app switcher and reopens it, Then **My Units** shows "Draft · mixed peas spoon", and tapping it reopens the editor at the same step with both parts.
- `/r` Given the same editor, When she taps Close, Then no "discard changes?" dialog appears and a quiet note reads "Draft kept in My Units".
- `/r` Given the Draft, When she taps "Delete draft", Then it disappears and a banner reads "Draft deleted · Undo"; Undo brings it back with both parts.
- `/r` Given a Draft exists only on the phone, When `GET /v1/units` (*proposed*) is called with her token, Then the response lists no Draft (a Unit reaches the server only at Save unit).

### B · Name it

#### eater-2.4 · Choose the kind, in my own words, fractions allowed
As the Eater, I say what kind of amount this is — bite, spoonful, sip, cup, piece, slice, handful or my own word — so that I log in the amounts I actually eat by. · FR-009 · E2, E30
- `/r` Given the **Unit editor** asks "What kind of amount?" in Arabic, When the list shows, Then it offers لقمة · معلقة · رشفة · كوب · قطعة · شريحة · حفنة · اسم خاص (bite · spoonful · sip · cup · piece · slice · handful · custom), each target at least 44×44 pt (E36).
- `/r` Given Mona chooses custom and names it «كمشة سوداني» (a handful of peanuts), When she saves it, Then **My Units** shows the tile «كمشة سوداني», and the `POST /v1/units` response holds `unit_kind: "custom"` and `label: "كمشة سوداني"`.
- `/r` Given a Recipe ingredient "baladi loaf" in the **Unit editor**, When Mona enters 1.5, «١٫٥» or «رغيف ونص», Then the ingredient reads 1.5 loaves (FR-009: "quantities may be fractional").
- `/m` Given the quantity parser, When it reads "1.5", "١٫٥", "½", «نص», «ونص» after a whole number n, and «ربع», Then it returns 1.5, 1.5, 0.5, 0.5, n + 0.5 and 0.25.

#### eater-2.5 · Name and describe it by voice or typing
As the Eater, I can say the Unit's name and description instead of typing, so that I can describe it with flour on my hands. · FRD §2.2 ("by typing or speaking"), FR-036 · EX-40
- `/r` Given Mona's AI Consent ("Send photos, voice and text to Google's AI (Gemini)") and Microphone Consent are Given and iOS allows the microphone, When she taps the microphone in the **Unit editor** and says «معلقة ملوخية من غير رز», Then the transcript appears as editable text, the name field proposes «معلقة ملوخية», and nothing is saved until she taps Save unit.
- `/r` Given iOS denies the microphone, When she taps the microphone, Then the field reads "Microphone is off. Type the name, or allow the microphone in iPhone Settings" and the keyboard opens.
- `/s` Given the voice recording, When the retention job runs 24 h after transcription, Then the audio object is gone and only the text remains (FR-078).

#### eater-2.6 · Give it a picture or the suggested icon
As the Eater, I give the Unit a photo or the icon the app suggests, so that I find it by sight on Today and in My Units. · FRD §2.2 ("The app suggests an icon"), §14.1 ("Show food pictures/icons"), FR-077 · E31
- `/r` Given Mona's cheese bite has no picture, When the **Unit editor** reaches "Picture", Then a cheese icon is pre-selected and "Use a photo" sits beside it.
- `/r` Given her Photos Consent is Given and she chooses "Use a photo", When the crop appears, Then the crop frame starts around the food, and after saving the cropped image is the tile on **My Units** and on **Today**'s recent Units.
- `/r` Given the saved picture, When `GET /v1/units/{id}/picture` (*proposed*) is called with Sam's token, Then 404 `NOT_FOUND` (vocabulary D2: never reveal another user's ids); with Mona's token it returns the image.
- `/s` Given the stored picture, When the test harness reads its bytes, Then it carries no EXIF, GPS or device metadata (FR-077).

### C · Say which food

#### eater-2.7 · Pin the exact food and preparation
As the Eater, I tie the Unit to one exact food and preparation — which bread, raw or cooked, drained or not, in oil or water — so that "a spoon of tuna" is my tuna. · FR-010, FRD §4.1
- `/r` Given the **Unit editor**'s food step, When Mona types «عيش» (bread), Then the list asks "Which bread?" and shows exactly Bread, baladi · Bread, shami · Toast, white, and Save unit stays disabled until one is chosen.
- `/r` Given she types «تونة», When the preparation step shows, Then it asks "In oil or in water?" and "Drained or not?", and choosing oil and drained selects her own record "Tuna in oil, drained (your label)".
- `/r` Given her tuna spoon is Saved, When `GET /v1/units/{id}` (*proposed*) is called, Then `food_version_id` names that one Food version and `preparation` holds medium "oil" and drained true, never only a food name.
- `/s` Given Units "rice, cooked spoon" and "rice, raw (for recipes)", When both are saved, Then each references a different Food version and neither replaces the other.

#### eater-2.8 · No silent substitution
As the Eater, when the food I named is missing, I see the stand-in and choose, so that tuna in oil is never quietly logged as tuna in water. · AT-05, FR-010, FR-024 · **Shared: Eater · Nutrition approver** (approver-10.12)
- `/r` AT-05: Given Faisal has no tuna record of his own and the reference holds only "Tuna, canned in water, drained", When he builds tuna spoon «ملعقة تونة» as "Tuna in oil, drained", 23 g, and reaches Review in the **Unit editor**, Then a line reads "Tuna, canned in water, drained — not the same as yours" with the badge "estimated analogue" and the choices "Use as an estimate" and "Read the label instead".
- `/r` Given he chooses "Use as an estimate", When he saves, Then the tile in **My Units** shows "estimated analogue", and the `POST /v1/units` response holds `evidence_status: "estimated analogue"` and both the requested and the used Food.
- `/s` Given that save, When the approver's Review is read, Then it holds one Estimated analogue flag "Tuna, canned — in oil, drained → in water" with counts only and no eater identifier (approver-10.12).

#### eater-2.9 · See where each number comes from, in the resolver's order
As the Eater, I open "Source details" on a Unit and see the source of every number, and the source is chosen in the FRD's order, so that I can trust it or fix it. · FR-025, FR-026, map §1.2 (resolver: "approved records → Tier A → recipe calculation → analogue"), FRD §14.1 ("Based on your saved recipe") · EX-14
- `/r` Given Mona's cheese bite version 1, When she opens it in **My Units** and taps "Source details", Then each part shows its source ("White cheese · reference version 3"; "Olive oil · reference"; "Bread, baladi · reference"), the serving basis "per 100 g", the date retrieved, the preparation state and the Evidence badge.
- `/m` FR-025: Given a test Food identity with matching candidates in all five tiers (the eater's own approved record, an approved label/manufacturer record, a Tier A record, a calculated recipe, an analogue), When the resolver runs five times, removing the winning tier each time, Then it picks them in exactly that order, and the analogue alone comes back marked "estimated analogue".
- `/r` FR-025: Given Faisal's own "Laban drink" label record and the reference Laban drink both match «كوب لبن», When `POST /v1/analyses` resolves Faisal's text «كوب لبن», Then the item's source is his own record; for Khalid (no own record) it is the reference Laban drink.
- `/r` FR-025 (recipe over analogue): Given Mona types «١٠٠ غ فول مدمس», When **Analysis review** opens, Then the chip's source is "فول مدمس · reviewed recipe record" with "recipe-calculated", not the Tier A analogue "Fava beans, cooked" (approver-10.39).
- `/r` Given a part whose values came only from an AI suggestion (no label photo confirmed), When `GET /v1/units/{id}` is called, Then that part's `evidence_status` is not `label-verified` (FR-026: "AI reasoning alone cannot be marked label-verified").

### D · Measure it

#### eater-2.10 · Type the weight and say how I got it
As the Eater, I type the weight from my kitchen scale and say whether I weighed it, read it on a pack or guessed, so that the Unit shows whether its amount is measured, declared or estimated. · FR-011 (direct mass), FR-012 · E2
- `/r` Given Huda's new bread bite in the **Unit editor**, When she types 8 and chooses "I weighed it", Then the amount reads "8 g · measured".
- `/r` Given she chooses "From the pack" instead, When the amount shows, Then it reads "8 g · declared"; with "My guess" it reads "8 g · estimated"; and the saved Unit's Evidence badge is "measured" only in the first case.
- `/r` Given Mona's interface in Arabic with Arabic-Indic digits, When she types ٨ and chooses "I weighed it" for a new Unit, Then the amount reads «٨ غ · موزون».
- `/m` Given 8 g of Bread, baladi, When the nutrition core computes it with FRD §6.1's formula, Then kcal = 20.0, protein 0.7 g, carbohydrate 4.0 g, fat 0.1 g, stored without rounding.

#### eater-2.11 · Count only the part I eat
As the Eater, I weigh dates whole and then their pits, so that the Unit counts only what I eat. · FRD §4.2 ("Record edible weight separately from peel, pit …"), FR-011
- `/r` Given Faisal builds Saqai date «تمرة صقعي» (kind piece) with the method "Weigh whole, then the pits", When he enters 5 dates, whole 60.0 g and pits 5.0 g, Then the editor reads "Edible 55.0 g · 11.0 g per date (average of 5) · 33 kcal".
- `/r` Given pits of 61 g, When the field loses focus, Then "Pits can't weigh more than the whole dates" shows beside it and Save unit is disabled.
- `/m` Given those inputs and Dates, Saqai, When the core runs, Then edible mass = 55.0 g, mean = 11.0 g, and one date = 33.0 kcal.

#### eater-2.12 · Weigh before and after
As the Eater, I weigh the honey jar before and after taking a spoon, so that I measure my spoon without dirtying a bowl. · FR-011 (before/after subtraction), FR-023 (negative residual) · E30
- `/r` Given the method "Before and after" for Mona's new honey spoon «معلقة عسل», When she enters before 412.6 g and after 398.4 g, Then the editor reads "14.2 g per spoon · measured · 43 kcal".
- `/r` Given before 398.4 g and after 412.6 g, When the second field loses focus, Then "After is heavier than before — check the order" shows with a "Swap" button and Save unit is disabled.
- `/r` Given the same reversed values sent to `POST /v1/units`, When the server validates, Then 422 `MASS_BALANCE_ERROR` and no Unit exists.
- `/r` Given she took 3 spoons between the two readings and enters count 3, When the editor recomputes, Then it reads "4.73 g per spoon (average of 3)".

#### eater-2.13 · Average a few pieces
As the Eater, I weigh several pieces together and save the average, so that small snacks are calibrated without weighing each one. · AT-01, FR-011, FR-013
- `/r` AT-01: Given the method "Average of several pieces" for Sam's new small biscuit, When he enters 7 pieces weighing 71.7 g after tare, Then the editor reads "10.24 g per piece · average of 7" and the saved Unit holds count 7.
- `/m` AT-01: Given 71.7 g and 7 pieces, When the core stores the mean, Then it is 10.242857 g unrounded, and only the display shows 10.24.
- `/r` FR-013: Given he also enters the single weights 9.8, 10.1, 10.6, 10.0, 10.4, 10.3 and 10.5 g, When the editor recomputes, Then it adds "pieces vary 9.8–10.6 g" and no screen says each piece weighs 10.24 g.
- `/r` Given single weights that add to 74.0 g against a total of 71.7 g, When he taps Save unit, Then "The single weights add to 74.0 g; the total says 71.7 g" shows with "Use the total" and "Use the single weights".

#### eater-2.14 · Measure by volume, never ml as grams
As the Eater, my cup of laban is defined by volume, and a millilitre is never treated as a gram, so that a drink is counted as a drink. · FR-011 (direct volume), FRD §4.2 ("Never convert milliliters to grams without an applicable density") · E4
- `/r` Given Faisal's Saved cup of laban (250 ml of his label's Laban drink, per 100 ml), When he opens it in **My Units**, Then it reads "250 ml · 152 kcal" and shows no gram figure.
- `/r` Given he builds a new Unit from Yogurt, plain (per 100 g, no density) and enters 250 ml, When the editor checks the basis, Then it reads "This food is listed per gram. Weigh one cup, or choose a food listed per ml", and Save unit is disabled — the ml/g ambiguity state of the Unit editor (FRD §14).
- `/r` Given the same Unit sent to `POST /v1/units`, When the server validates, Then 422 `SOURCE_BASIS_UNKNOWN`.
- `/m` Given a volume on a per-gram Food, When conversion is asked for, Then it runs only with an approved density for that Food version (Milk, whole has 1.00); there is no default of 1 g per ml.

#### eater-2.15 · My scale shows ml but I meant grams
As the Eater, when my scale photo shows "ml" and the Unit is in grams, I am asked which it is, so that an unclear reading is never called measured. · AT-07, FR-012, FRD §4.2 ("highlight a scale reading in 'ml' when the user requested grams") · EX-23
- `/r` AT-07: Given Mona builds yogurt cup «علبة زبادي» from Yogurt, plain (per 100 g) in grams, When her scale photo's display reads "180 ml", Then "ml" is highlighted in the **Unit editor**, the line reads "Your scale shows ml; this unit is in grams. Which is it?" with "It's grams" · "Weigh again", and the amount is not marked measured.
- `/r` Given she taps "It's grams", When the amount shows, Then it reads "180 g · declared" (her statement, not a measurement) and the Unit can be saved at 110 kcal.
- `/r` Given the same photo sent to `POST /v1/analyses` in scale capture, When the response returns, Then `measurement_basis` is not `measured`, `quantity_unit` is `ml`, and `required_questions` holds the basis question.

#### eater-2.16 · Impossible input is caught beside the field
As the Eater, I get a fix next to the field when I type something impossible, and obvious slips are fixed quietly, so that a typo never becomes a Unit. · FR-006 pattern ("Reject negative values and invalid units"), FRD §14.1 (Arabic decimal input) · EX-23, E41, care group 4
- `/r` Given a weight field in Mona's **Unit editor**, When she types «٨٫٥» (Arabic-Indic digits and the Arabic decimal mark), Then it is kept as 8.5 g and shown in her digits.
- `/r` Given "0", "-8" or "8..5", When the field loses focus, Then "Enter a weight above 0" or "Check the number" shows under it, Save unit is disabled, and the other fields keep their values.
- `/r` Given "8 g" typed with the unit letters, When the field loses focus, Then the letters are dropped and 8 stays.
- `/r` Given `POST /v1/units` with a part of −1.5 g, When the server validates, Then 422 `VALIDATION_ERROR` names that part's mass field.

### E · Mixed portion (Composite)

#### eater-2.17 · Build a Composite from its parts
As the Eater, I build a mixed spoon from its parts with each weight, so that rice, peas and meat are each counted once. · FR-017, AT-03, FRD §5.2 ("Mixed peas spoon")
- `/r` AT-03: Given "What is in it?" → "Several foods", When Mona adds Rice, cooked 15.1 g, Peas with sauce 14.4 g and Beef, cooked 8.6 g to mixed peas spoon, Then the **Unit editor** shows three lines and "Total 38.1 g".
- `/m` AT-03: Given the three parts, When the core sums each nutrient, Then each part counts once and the saved total mass is 38.1 g.
- `/r` Given it is saved, When `GET /v1/units/{id}` is called, Then `unit_kind` is "spoonful", `structure` is "composite", `components[]` holds 15.1, 14.4 and 8.6 g, and `total_mass_g` is 38.1.

#### eater-2.18 · The oil inside the cheese is not added twice
As the Eater, I weigh my cheese spoon with its oil and say how much is oil, so that the oil is counted once. · AT-02, FR-023, FRD §5.2 ("Cheese spoon = 6.9 g including 1.5 g oil")
- `/r` AT-02: Given Huda builds cheese spoon, When she enters total 6.9 g and "of which oil 1.5 g", Then the editor reads "White cheese 5.4 g · 12.5 kcal · Olive oil 1.5 g · 13.5 kcal · Total 6.9 g · 26 kcal".
- `/m` AT-02: Given 6.9 g with 1.5 g oil, When the core computes it, Then cheese = 5.4 g and kcal = 26.0 — never 29.5 (6.9 g of cheese plus the oil again) — and expanding it inside a with-bread variant adds no more oil.
- `/r` Given she also adds "Olive oil 1.5 g" as a separate part, When the line is added, Then the editor reads "Olive oil is already inside the 6.9 g" with "Remove this line".

#### eater-2.19 · Parts must add up to what I weighed
As the Eater, I am told when the parts don't add up to the total I weighed, so that a slip on the scale doesn't become a wrong Unit. · FR-023 (component sum against measured total, configurable tolerance), map §1.6 (Policy: component-sum tolerance) · **Shared: Eater · Nutrition approver** (approver-10.55)
- `/r` Given the Policy's component-sum tolerance is 2 % and mixed peas spoon was weighed at 38.1 g, When the parts add to 39.1 g, Then the **Unit editor** shows the component-sum error "Parts add to 39.1 g; you weighed 38.1 g (2.6 % more)" with "Fix a part" and "Use the parts' total", and Save unit is disabled until one is chosen.
- `/r` Given the parts add to 38.4 g (0.8 %), When she saves, Then no error shows, the Unit keeps both the weighed 38.1 g and the parts' 38.4 g, and its nutrients come from the parts.
- `/r` Given the 39.1 g against 38.1 g case sent to `POST /v1/units`, When the server validates, Then 422 `MASS_BALANCE_ERROR` with the measured total, the parts' sum and the tolerance.

#### eater-2.20 · A Composite cannot contain itself
As the Eater, I cannot put a Unit inside itself through another one, so that a Unit's numbers are always finite and clear. · FR-023 ("Reject cyclic composites")
- `/r` Given Mona's new Composite "foul plate" contains foul spoon × 6, When she edits foul spoon to add a part, Then "foul plate" is greyed out in the picker with "foul plate already contains foul spoon".
- `/r` Given the same change sent to `POST /v1/units/{id}/versions`, When the server validates, Then 422 `VALIDATION_ERROR` naming the cycle, and no new version exists.
- `/m` Given the graph A → B → C → A, When the cycle check runs, Then it is rejected; A → B, A → C, B → C passes.

#### eater-2.21 · Cereal and milk weighed together
As the Eater, I weigh my cereal and then the bowl with milk, so that the milk is counted once. · FRD §4.2 ("A combined wet cereal weight is not dry cereal weight plus milk a second time")
- `/r` Given Mona's new cereal bowl with the method "Weigh in steps", When she enters cereal 30 g and then "bowl with milk" 150 g, Then the editor reads "Cereal 30 g · Milk 120 g · Total 150 g".
- `/m` Given steps of 30 g and then 150 g in total, When the core computes it, Then milk = 120 g, total = 150 g, and milk is counted once.
- `/r` Given she also types Milk 150 g as a separate part, When the line is added, Then the component-sum error reads "Parts add to 180 g; you weighed 150 g".

### F · Bread and preparation rules

#### eater-2.22 · One bread bite with every dipped bite
As the Eater, I set once that every dipped bite comes with one bread bite, so that my egg bites include their bread without my adding it each time. · FR-018, AT-04, map §1.6 (User rules: accompaniment) · E5
- `/r` Given **Settings → Food rules**, When Mona sets "Each dipped bite includes 1 bread bite (8 g Bread, baladi)" and marks egg bite as "eaten by dipping", Then egg bite in **My Units** reads "Includes 8 g bread" (FRD §14.1), and cheese spoon still reads "without bread · 26 kcal" (its bread comes only through its with-bread variant, 2.25).
- `/r` AT-04: Given that rule, When she builds the Composite "breakfast plate" with egg bite × 3, Then its expansion shows "Bread, baladi 24 g (3 × 8 g) · 60 kcal" as its own line with its macros.
- `/m` AT-04: Given 3 dipped egg bites at 8 g, When the core expands them, Then bread = 24 g, counted once.
- `/s` Given the rule is saved, When `GET /v1/rules` (*proposed*) is read with Mona's token, Then it is a new rule version effective from now, and Entries logged before it keep their snapshots.

#### eater-2.23 · Bread already inside gets no more bread
As the Eater, a Unit that already contains bread gets no extra bread from the rule, so that a tuna bite or fatta is never double-counted. · FR-019, AT-04
- `/r` AT-04: Given the dipped-bite rule is on and Mona's tuna bite holds Bread, baladi 8 g inside, When she opens tuna bite in **My Units**, Then its expansion has exactly one Bread, baladi line (8 g) and reads "Bread already inside".
- `/r` Given she builds "fatta spoon" with bread inside and ticks "eaten by dipping", When the box is ticked, Then the editor reads "This already contains bread — the bread rule adds none", and the expansion adds 0 g.
- `/m` Given a Composite with any part in the bread group, When the accompaniment rule runs, Then it adds 0 g.

#### eater-2.24 · An exception: a meat bite with 5 g of bread
As the Eater, I give one Unit its own bread amount, so that my meat bite counts the smaller piece of bread I really use. · FR-020
- `/r` Given the household rule of 8 g and meat bite marked "eaten by dipping", When Mona sets meat bite → "Bread with each bite: 5 g", Then meat bite reads "Includes 5 g bread" and egg bite still reads 8 g in **My Units**.
- `/m` Given a Unit rule of 5 g and a household default of 8 g, When the core expands meat bite, Then 5 g is used.
- `/r` Given `GET /v1/units/{meat_bite}`, When it is read, Then the accompaniment shows Bread, baladi 5 g with the source "this unit's rule".

#### eater-2.25 · With bread or without, one filling
As the Eater, I keep one cheese filling and choose "with bread" or "without bread", so that a cheese spoon eaten alone carries no bread. · FR-024, FRD §24.1 ("retain without-bread base and with-bread variant")
- `/r` Given Mona's cheese spoon (6.9 g) and its with-bread variant, When she opens it in **My Units**, Then two lines show: "cheese spoon · without bread · 26 kcal" and "cheese bite · with bread · Includes 8 g bread · 46 kcal".
- `/r` Given she changes the with-bread variant's bread to 9 g, When she saves, Then only the variant gets a new version, and cheese spoon stays version 1.
- `/r` Given `GET /v1/units/{cheese_spoon}`, When it is read, Then both variants point at the same filling version.

#### eater-2.26 · How I make my tea, and my household defaults, as quantities
As the Eater, I store how I make tea as quantities and keep my household defaults as quantities, so that each glass counts the real milk and sugar every time. · FR-021, FRD §5.2 ("Tea with milk … Store actual milk quantity, not only the cup capacity"), map §1.6 (User rules: preparation defaults) · E3, E4
- `/r` Given Mona's glass of milk tea (a 200 ml glass), When she opens it in **My Units**, Then it reads "Brewed tea 150 ml · Milk, whole 50 ml · 2 × my teaspoon (Sugar 7.5 g) · 60 kcal", and the 200 ml shows as the glass's size, not as milk.
- `/m` Given that Unit, When the core computes it, Then kcal = 30.0 (milk) + 30.0 (sugar) = 60.0.
- `/r` Given Faisal's Food rules "Laban: unsweetened" and "Ghee: 3 g per fried egg", When he saves an "egg, boiled bite" and an "egg, fried bite", Then the boiled one shows no ghee and the fried one reads "Includes 3 g ghee" (only the matching rule applies).
- `/r` Given those rules, When `GET /v1/rules` (*proposed*) is called with Faisal's token, Then it returns them as quantities: added sugar 0 g for laban, ghee 3 g per fried egg.

#### eater-2.27 · Bread with whole eggs is asked, never guessed
As the Eater, when a portion has whole eggs eaten with bread, I am asked how many bread bites I used, or I pick a Composite I saved for it, so that bread is never guessed from the egg count. · FR-022 ("Use an approved serving template or ask for the missing bite count")
- `/r` Given Mona has a Saved Composite "two eggs breakfast" (Egg, boiled × 2 + bread bite × 5), When she builds a new Composite "eggs with bread" with Egg, boiled × 2 and taps Save unit, Then the editor asks "How many bread bites with the 2 eggs?" with an empty count stepper (not 2), and offers "Use two eggs breakfast (5 bread bites)".
- `/m` Given 2 eggs and no bite count, When the core expands the portion, Then bread is "missing", never 2 × 8 g.
- `/r` Given she enters 5, When the expansion updates, Then it reads "Bread, baladi 40 g (5 × 8 g)".

#### eater-2.28 · This time beats my variant; a spoon is not a bite
As the Eater, what I say for one Entry beats my variant, and my variant beats my household default, and my spoon of tuna is not my tuna bite, so that "no bread this time" works without changing my Units. · FRD §5.1, map §1.6 (precedence) · FRD §4.1 ("A spoon is not universally 15 g")
- `/r` Given the household default of 8 g bread and Mona's cheese bite (with bread), When she types «٣ قرص جبنة من غير عيش» in the quick-add control, Then **Analysis review** shows "cheese spoon × 3 · without bread (this time) · 78 kcal", and **My Units** still shows cheese bite with bread at 46 kcal.
- `/r` FRD §5.1: Given Mona's tuna spoon (23 g) and tuna bite (6.8 g of tuna), When she types «معلقة تونة», Then the chip in **Analysis review** is tuna spoon 23 g, never tuna bite.
- `/r` FRD §4.1: Given Mona's tuna spoon 23 g, talbina spoon 16 g and honey spoon 14.2 g, When she types only «معلقة» with no food, Then one question asks which spoon, and no 15 g "standard spoon" is offered.
- `/m` Given "spoon" and "bite" Units for the same food, When the resolver maps a word, Then it never maps one kind to the other.

### G · Recipe

#### eater-2.29 · Weigh the ingredients and the pot
As the Eater, I weigh what goes into the pot and then the cooked pot, so that a spoon of my talbina counts the real cooked dish. · FR-028, AT-06, map WF-2 ("weigh-the-pot cooked yield"), map §1.3 (cooked yield first-class) · C22, E24
- `/r` Given Huda opens **Unit editor** → "My Recipe" → "talbina", When she enters Barley flour 40 g, Milk, whole 500 g, Sugar 10 g, then "Weigh the pot": empty 1,216 g and with talbina 1,600 g, Then the Recipe reads "Cooked yield 384 g · 480 kcal · 1.25 kcal per g · recipe-calculated".
- `/r` AT-06: Given Huda's talbina version 1, When she saves "talbina spoon" = 16 g of it, Then **My Units** shows "talbina spoon · 20 kcal", and the `POST /v1/recipes` response holds `cooked_yield_g: 384` and `kcal: 480`.
- `/m` AT-06: Given 480 kcal and a yield of 384 g, When the core computes spoons, Then 16 g = 20 kcal, 15 spoons = 300 kcal and 18 spoons = 360 kcal exactly.
- `/r` Given Mona cooked talbina before in "big pot" (1,216 g empty), When the empty-pot field opens for a new Recipe, Then it offers "big pot · 1,216 g (last time)" as a choice.

#### eater-2.30 · A spoon of the cooked dish, not of the flour
As the Eater, my talbina spoon means 16 g of the cooked talbina, and water or cooked ingredients are not counted twice, so that the Recipe's numbers follow the dish. · FRD §5.2 ("Talbina spoon = 16 g … never 16 g dry barley flour"), FRD §6.1
- `/r` FRD §5.2: Given Mona's talbina spoon, When she opens "Source details", Then it reads "16 g of your cooked talbina (Recipe version 1)" and never "16 g barley flour".
- `/m` FRD §6.1: Given an ingredient already listed as cooked (Rice, cooked 200 g), When the Recipe is computed, Then no extra cooking-yield factor is applied to it.
- `/m` FRD §6.1: Given 500 g of water added, When the Recipe is computed, Then total kcal is unchanged and only the yield and kcal per g change.
- `/r` Given `POST /v1/recipes` with 500 g of water, When the response returns, Then `kcal` equals the total without water.

#### eater-2.31 · Oil added, fat poured off
As the Eater, I add the oil I cooked with and subtract the fat I poured off, so that the Recipe counts what is in the pot. · FR-028 ("Support cooking additions and known discarded liquid/fat"), FRD §6.1
- `/r` Given Mona's new Recipe "minced beef" with beef 500 g and oil 20 g, When she adds "Poured off: fat 30 g", Then the Recipe shows "− Fat poured off 30 g · −270 kcal" as its own line.
- `/m` Given recipe_k = Σ ingredient_k − discarded_k, When 30 g of fat is discarded, Then 270 kcal and 30 g of fat are subtracted.
- `/r` Given a discard larger than the fat that went in, When she taps Save unit, Then "More fat poured off than went in — check the weight" shows and Save unit is disabled.

#### eater-2.32 · No pot weight, or unknown oil: a range, not an exact figure
As the Eater, when I didn't weigh the pot or don't know how much oil the food soaked up, I see a range and the assumption, so that the app never claims exact calories it cannot know. · FR-029, FRD §14 (Unit editor "missing final yield"), FRD §20.1 · EX-32
- `/r` Given Mona's new Recipe "fried eggplant" with eggplant 400 g and frying oil 100 g, oil left in the pan unknown, When she saves it, Then the Recipe reads "kcal per 100 g: low–high range (heuristic) · assumes 0–100 g of oil soaked up" with "Weigh the oil left in the pan".
- `/r` Given Huda's talbina before the pot is weighed, When she reaches Review, Then the **Unit editor** shows the missing-final-yield state: the spoon's value is a heuristic low–high range, the badge is not "recipe-calculated", and Save unit is allowed with "Weigh the pot later".
- `/r` Given Mona later enters 70 g of oil left in the pan, When she saves, Then the range becomes one value and fried eggplant becomes version 2.
- `/r` Given `POST /v1/recipes` without `cooked_yield_g`, When the response returns, Then `assumptions[]` holds "cooked yield not weighed" and the per-gram value is a range, never one figure.

#### eater-2.33 · Cook it again, weigh the new pot, choose the spoon's version
As the Eater, I cook the same dish again and weigh the new pot, and I choose whether my spoon follows the new batch, so that this week's talbina uses this week's yield and nothing moves on its own. · FR-010 ("reference a specific … recipe version"), FR-014, FR-028 · E24 ("Most of my cooking isn't that consistent")
- `/r` Given Mona's talbina version 1 (yield 384 g), When she taps "Cook again" on it in **My Units**, Then the editor opens with the same ingredients and empty pot fields; a pot of 1,580 g gives a yield of 364 g and saves talbina version 2.
- `/r` Given talbina version 2 is saved, When the save completes, Then **My Units** asks "Use the new batch for talbina spoon from now on?" with "Use it" and "Keep version 1"; "Use it" saves talbina spoon version 2 (16 g of talbina version 2 · 21.1 kcal).
- `/r` Given talbina spoon × 15 was logged on 30 Sep on version 1, When Mona opens 30 Sep on **Today**, Then those Entries still read 300 kcal, and a log today reads 21.1 kcal per spoon.
- `/s` Given both versions, When `GET /v1/units/{talbina_spoon}` is read, Then version 1 references talbina version 1 and version 2 references talbina version 2, and the history lists both batches with their dates.

### H · Numbers and sources

#### eater-2.34 · Only the calories
As the Eater, I can save only the calories for something I know nothing else about, so that I can still log my aunt's basbousa. · FR-016, AT-16, FRD §18.1 ("Calorie-only custom items use an explicit user-override path") · EX-27
- `/r` Given "Custom" → "I only know the calories", When Mona saves "basbousa piece (aunt's)" with 250 kcal, Then the Unit reads "250 kcal · user-defined · protein, carbs and fat unknown" and no macro grams appear on any screen.
- `/r` AT-16: Given that Unit, When she logs 1 piece from **Today**, Then calories rise by 250 and the macro area reads "Macros incomplete — 1 entry without macros".
- `/r` AT-16: Given `GET /v1/reports/day` after the log, When it is read, Then macro coverage is incomplete and no protein, carbohydrate or fat value is reported as 0 for that Entry.
- `/m` Given a user-defined calorie value, When the core stores it, Then no macro is derived from the calories.

#### eater-2.35 · Two energy values, both kept
As the Eater, when a label's calories don't match its macros, I see both and why, and the label is never changed to fit, so that I can trust the printed number. · FR-030, AT-15 (headline part), FRD §10.1 · **Shared: Eater · Nutrition approver** (approver-10.13)
- `/r` Given a label for Sam's new oat biscuit of 120 kcal per 30 g serving with protein 2 g, carbohydrate 15 g and fat 3 g, When Sam saves the Unit and opens "Source details", Then the headline is 120 kcal and a line reads "From the macros: 95 kcal (4/4/9). Labels can use other factors, for example for fibre."
- `/m` Given 120 kcal and 95 kcal, When the Unit is stored, Then the source value stays 120 and the macro-derived 95 is stored apart.
- `/s` Given Sam has not submitted the label for review, When the approver's Review is read, Then no Energy mismatch flag names it (approver-10.13 raises it on a Label submission), and Sam's Unit keeps 120 kcal; if he submits it (4.37), the flag opens on the submission.

#### eater-2.36 · A better source arrives; I choose
As the Eater, when a reviewer improves a food my Unit uses, I choose whether new logs use it and whether any past Entries do, so that my history never changes behind my back. · FR-031, FR-014 · **Shared: Eater · Nutrition approver** (approver-10.28)
- `/r` Given Mona's cheese bite uses White cheese version 3 and the approver approves version 4, When Mona opens **My Units**, Then cheese bite reads "Source updated — use it from now on?" with "Use from now on" and "Keep current", and new logs stay on version 3 until she answers.
- `/r` Given she taps "Use from now on", When the Unit updates, Then cheese bite becomes version 2 and a second question "Also apply to past entries?" offers "No" (selected) and "Choose entries…".
- `/r` Given she keeps "No", When `GET /v1/reports/day` is called for 30 Sep, Then it returns the same totals (1,098 kcal) and revision as before.

#### eater-2.37 · A restaurant item says what its serving covers
As the Eater, when I save a restaurant item from its menu calories, I say what the serving covers, so that a double or a full meal is not counted twice. · FRD §6.2 ("The meaning of 'serving' is a required field") · E10, E28, F20
- `/r` Given Label mode on a menu photo (or typed) "Chicken kabsa — 1,250 kcal", When Faisal reaches Review in the **Unit editor**, Then "What does 1,250 kcal cover?" requires one of sandwich only · double serving · full meal with sides · side · sauce · drink, and Save unit stays disabled until he picks.
- `/r` Given "full meal with sides" and a later log of 0.5, When the Entry appears on **Today**, Then it is 625 kcal and no salad or sauce is added beside it.
- `/r` Given Faisal's Saved "double burger meal — 1,100 kcal, includes fries", When he adds fries in the same **Analysis review**, Then the fries chip reads "Already included in double burger meal?" before Approve is possible.
- `/m` Given a menu item marked "double serving", When it is logged × 1, Then the core does not multiply it by 2 again.

### I · Review and save

#### eater-2.38 · See the whole definition, then Save unit
As the Eater, before I save I see every part, its weight and its numbers, so that I know exactly what "my cheese bite" means. · FRD §2.2 ("The user sees all three, not an unexplained aggregate"), WF-2 done-when, AT-10 pattern · EX-09
- `/r` WF-2 done-when: Given Huda builds cheese bite in the **Unit editor**, When she reaches Review, Then she sees "White cheese 5.4 g · 12.5 kcal", "Olive oil 1.5 g · 13.5 kcal" and "Bread, baladi 8 g · 20 kcal", the total 46 kcal, protein 2.5 g, carbohydrate 4.5 g and fat 2.0 g, the badge "measured", and one main button "Save unit".
- `/r` Given she taps Save unit, When `POST /v1/units` answers, Then it returns an immutable unit version id with version 1 and its Evidence status, and the editor closes onto **My Units** with cheese bite first.
- `/r` Given a slow network, When she taps Save unit, Then the button reads "Saving…" at once and cannot be tapped again; a retried request with the same idempotency key leaves exactly one cheese bite (FRD §18).

#### eater-2.39 · Saving a Unit is not eating
As the Eater, saving a Unit adds nothing to my Day, so that calibrating at the counter never inflates what I ate. · FRD §2.2 ("Saving does not log consumption"), FRD §4.3, FR-045, WF-2 done-when
- `/r` WF-2 done-when: Given Huda's **Today** shows 200 kcal consumed at Day revision 1, When she saves cheese bite and talbina spoon, Then **Today** still shows 200 kcal, no Entry is added, and `GET /v1/reports/day` returns revision 1.
- `/r` Given the save is done, When the confirmation shows, Then it offers "Log it now" as a secondary button, and nothing is logged unless she taps it.
- `/s` Given the scale photo and the Unit-mode photo used for calibration, When the Day projection is rebuilt from the ledger, Then they contribute 0 kcal (FR-045).

#### eater-2.40 · Find my Units in My Units
As the Eater, I find my Units by picture, name or Alias, most recently used first, so that defining once pays off every day. · FRD §14 (My Units: recents, searchable, photos/icons, bread variants, version history), WF-2 done-when ("My Units lists them") · E31, EX-11
- `/r` Given Mona's Saved Units and Recipe talbina, When she opens **My Units**, Then "Recent" lists the last used first with picture, name and kcal per unit, Recipes have their own section, and searching «جبنة» finds cheese bite «قرصة جبنة» and cheese spoon «معلقة جبنة».
- `/r` Given talbina spoon is opened, When its page shows, Then it lists its versions with dates and its Evidence badge "recipe-calculated".
- `/r` Given `GET /v1/units?sort=recent` (*proposed*), When it is called with Mona's token, Then the same Units come back in the same order with their current version ids.
- `/r` Given the first open after install on a network slowed to 3 s, When **My Units** loads, Then placeholder tiles show within the first second, never a blank screen; offline, the saved Units show with the quiet note "Offline — showing saved units" (EX-21).

### J · Aliases

#### eater-2.41 · Other names: Arabic, English, Latin letters, spoken
As the Eater, I give a Unit other names — Arabic, English, the way I spell it in Latin letters, and what I say aloud — so that any of them finds it. · FR-015 · E42, E44 · **Shared: Eater · Nutrition approver** (approver-10.41, approver-10.44)
- `/r` Given Mona's foul spoon, When she adds the Aliases «معلقة فول», "foul spoon", "ful" and "fool spoon", Then typing any of them in the quick-add control resolves to foul spoon in **Analysis review**.
- `/r` FR-015: Given the approved Food Dates, Saqai with Aliases "Saqai date" and «صقعي», and Faisal's Saqai date Unit with his own Alias «صقعية», When he types "Saqai date", «صقعي» or «صقعية», Then each resolves to the same Food version, Dates, Saqai, through his Unit.
- `/m` Given the Arabic normaliser (approver-10.44), When it reads «طعميه» and «طعمية», or «١٨» and "18", Then each pair gives the same key.
- `/r` Given Mona records a spoken Alias «تلبينة» for talbina spoon, When the Alias is saved, Then it is stored as text and the recording follows the 24-hour rule.

#### eater-2.42 · My own name wins
As the Eater, my own Unit called "laban" always means my laban, whatever the dialect table says, so that the app never swaps my drink. · map §1.6 (User rules precedence; dialect "drives لبن/laban resolution"), FRD §5.1 · F27 · **Shared: Eater · Nutrition approver** (approver-10.42, approver-10.46)
- `/r` Given Sam's Saved cup of laban (yogurt drink) and his dialect set to EG in **Settings → Units & language** (where «لبن» resolves to Milk, whole), When he types "cup laban", Then **Analysis review** shows his cup of laban marked "Your unit", not Milk, whole.
- `/r` Given Nadia has no laban Unit and no dialect set, When she types "cup laban", Then one question asks "Laban: milk, or yogurt drink?" and it counts as one of the two questions.
- `/s` Given the approver retires the EG Alias «لبن» → Milk, whole, When Mona types «كوباية لبن», Then it resolves to her glass of milk Unit version 1, as before the retirement.

#### eater-2.43 · Two of my Units share a name
As the Eater, when one name fits two of my Units, I am asked which, so that the wrong one is never logged. · FR-015, FRD §18.2 (`UNIT_AMBIGUOUS`)
- `/r` Given Mona's cheese bite has the Alias "cheese", When she adds "cheese" to cheese spoon too, Then the editor reads "'cheese' already names cheese bite" with "Use it for both (I'll be asked)" and "Choose another name".
- `/r` Given both keep "cheese", When she types "3 cheese" in the quick-add control, Then **Analysis review** asks "cheese bite or cheese spoon?" and nothing is logged until she answers.
- `/r` Given the text "3 cheese" sent to `POST /v1/analyses`, When the response returns, Then `required_questions` names both Units; a consume command naming the Alias alone returns `UNIT_AMBIGUOUS`.

### K · Versions

#### eater-2.44 · Recalibrate: a new version, the past stays
As the Eater, when my bread bite is now 9 g, I recalibrate it and only future logs change, so that yesterday stays as it was. · FR-014, AT-12, map §1.3 (create or recalibrate a Unit) · E21, EX-14
- `/r` AT-12: Given Mona's bread bite version 1 = 8 g and 30 Sep's Entry bread bite × 6 (120 kcal), When she recalibrates to 9 g on 1 Oct and taps Save unit, Then **My Units** shows bread bite version 2 · 9 g, and 30 Sep on **Today** still shows 120 kcal for those 6.
- `/r` AT-12: Given version 2, When she taps bread bite on **Today**'s recent Units, Then the new Entry reads 9 g · 22.5 kcal — the recent tile uses version 2 (E21).
- `/r` Given `POST /v1/units/{id}/versions` with 9 g, When the response returns, Then it holds version 2, and `GET /v1/reports/day` for 30 Sep returns the same totals (1,098 kcal) and revision as before.
- `/r` Given bread bite is opened in **My Units**, When "Versions" shows, Then it lists version 1 · 8 g · measured and version 2 · 9 g · 2026-10-01 · measured, with what changed.

#### eater-2.45 · Apply the new weight to Entries I choose
As the Eater, I can apply the new weight to some past Entries, only after I see the difference and approve it, so that I fix one meal without rewriting last month. · AT-12 ("Selected-history correction works only after approval"), FR-014, FR-031, FRD §8.2 ("Apply that measurement to today's lunch")
- `/r` Given bread bite version 2 is saved, When Mona taps "Apply to past entries…", Then the Entries on version 1 are listed by Day and none is selected.
- `/r` Given she selects 30 Sep's lunch bread bite × 6, When the correction preview shows, Then it reads old 120 kcal · new 135 kcal · difference +15 kcal for the meal and the Day, and "Apply" is the only way to commit.
- `/r` Given she taps Apply, When **Today** for 30 Sep refreshes, Then that Entry is Corrected (old, new, difference shown), the Day reads 1,113 kcal, no other Day changes, and `POST /v1/consumption/{id}/corrections` was called once with its expected revision.
- `/s` Given she cancels the preview, When the ledger is read, Then no Correction exists.

#### eater-2.46 · Archive a Unit I no longer eat
As the Eater, I archive a Unit I no longer eat, so that it leaves my recents and my history keeps it. · FRD §14 (My Units "archived unit"), vocabulary D2 (Unit "Archived")
- `/r` Given Mona's Saved cereal bowl, When she taps Archive, Then it leaves Recent and search, and an "Archived (1)" row appears at the foot of **My Units**.
- `/r` Given past Entries of cereal bowl, When she opens those Days on **Today**, Then they are unchanged and "Source details" still names cereal bowl.
- `/r` Given `GET /v1/units`, When it is called without `include=archived` (*proposed*), Then cereal bowl is absent; with it, cereal bowl is listed as Archived.

#### eater-2.47 · Two phones change the same Unit
As the Eater, if I recalibrate the same Unit on two devices, I am shown the clash and choose, so that nothing is merged silently. · FRD §17.2 ("Cross-device edits carry an expected revision"), FRD §18.2 (`STALE_REVISION`)
- `/r` Given bread bite version 1 is open on two simulators signed in as Mona, When device A saves 9 g and device B then saves 8.5 g based on version 1, Then B's save returns 409 `STALE_REVISION` with the current version, and B's **Unit editor** reads "bread bite changed on another device to 9 g (version 2)" with "Keep 9 g" and "Save 8.5 g as version 3".
- `/r` Given she taps "Save 8.5 g as version 3", When **My Units** refreshes, Then version 3 is current and version 2 stays in the history.

### L · Anywhere, anyone

#### eater-2.48 · Build a Unit with no signal
As the Eater, I can build and save a Unit in a kitchen with no signal, so that the weighing is not wasted. · NFR-06 ("cached unit/diary access and queued commands"), FRD §8.3 · EX-21, E38
- `/r` Given airplane mode on device A, When Mona completes honey spoon (14.2 g) and taps Save unit, Then **My Units** shows it as a Draft reading "Saves when connected", and its kcal reads "calculated when connected" (the server resolves the numbers, FRD §18.1).
- `/r` Given the connection returns, When the queued Save runs, Then honey spoon becomes Saved version 1 with 43 kcal, and only one honey spoon exists — also when the app was closed before the sync.
- `/r` Given that while device A was offline Mona saved a different "honey spoon" on device B, When A's queued Save runs, Then it returns 422 `VALIDATION_ERROR` on `label`, and A's tile reads "Not saved — open to fix", opening the editor at the name field with every value kept.

#### eater-2.49 · Units from the trial come with me, once
As the Eater, Units I made before creating an account move to my account once, so that the trial was not wasted. · FR-001 ("safe migration without duplicate units or meals") · EX-03
- `/r` Given Hala's trial diary T1 with the Units «قرصة جبنة», «شاي بلبن» and «لقمة عيش», When she creates an account, Then **My Units** shows exactly those three Units, each version 1.
- `/r` Given account creation is retried after a dropped connection, When `GET /v1/units` is called with her token, Then it returns 3 Units, not 6.

#### eater-2.50 · The Unit editor in Arabic, at the largest text, with VoiceOver, one-handed, in sun or at night
As the Eater, I can review and save a Unit in Arabic, at the largest text size, with VoiceOver and with one thumb, readable in sun and at night, so that the editor works for me as I am. · NFR-08, FRD §14.1 (Arabic right-to-left, both numeral systems), FRD §14.2 ("sufficient contrast, large touch targets") · EX-33, EX-34, EX-36, EX-37, EX-39, E34–E37, E40, E41
- `/r` Given Mona's Arabic interface with Arabic-Indic digits, When the **Unit editor** shows cheese bite, Then the layout is mirrored, the back control points right, the weights read «٥٫٤ غ», «١٫٥ غ» and «٨ غ» with no digit reversed inside a number, and an English Alias "cheese bite" keeps its place in the Arabic row.
- `/r` Given the largest accessibility text size on the iPhone 17e, When the Review step shows, Then no line, count stepper or the Save unit button is cut off or overlapping.
- `/r` Given VoiceOver, When focus reaches the Review of cheese bite, Then it reads "cheese bite, 46 kilocalories, white cheese 5.4 grams, olive oil 1.5 grams, includes 8 grams bread, measured", and the main button reads "Save unit".
- `/r` Given Faisal's Arabic interface with Western digits, When his cheese bite «لقمة جبن» shows, Then the weights read 5.4, 1.5 and 8 — the numeral setting decides, not the language (E41).
- `/r` Given the iPhone 17 Pro Max, When the Review step shows, Then Save unit and the count steppers sit in the lower two-thirds of the screen, and every control measures at least 44×44 pt.
- `/r` Given light and dark appearance, When the Review step's text and kcal figures are measured on the served screen, Then contrast is at least 4.5:1, and the kcal figures use a heavier weight than body text (EX-34).
- `/r` Given a part line in the editor, When it is swiped to delete, Then the same action is also a visible "Remove" button on the line (EX-37).

#### eater-2.51 · Platform changes never move a saved Unit
As the Eater, nothing the platform changes behind the scenes moves my saved Units or past Days, so that "it was accurate, and now it isn't" never happens to me. · FRD §1.4 ("without silently rewriting previous days"), FR-014, FR-031 · E15, E16, EX-14 · **Shared: Eater · Platform admin** (admin-10.29)
- `/r` Given Mona's cheese bite version 1 at 46 kcal, When the admin moves a new Meal Registry version to Rollout and Mona then logs cheese bite, Then the Entry is 46 kcal, as before.
- `/s` Given any Registry change, When `GET /v1/units/{id}` and 30 Sep's `GET /v1/reports/day` are read, Then the version id and the totals are unchanged.
- `/r` Given cheese bite × 3 logged on 30 Sep and again on 1 Oct, When both Days are opened on **Today**, Then both Entries read 138 kcal with the same macros (E15).

#### eater-2.52 · "Hide numbers" in the Unit editor and My Units
As the Eater who has turned numbers off, I can still define and save my Units by weight and parts, without calorie or macro figures, so that calibrating never shows me numbers I find harmful. · map §1.6 ("'hide numbers' view", R37), FRD §14.2 · EX-43 (the same setting as eater-3.42)
- `/r` Given **Settings → Goals → "Hide numbers"** is on for Mona, When she builds and reviews a Unit in the **Unit editor**, Then parts and weights show (grams are measurements) and no kcal, macro grams or shares show anywhere on the Review step, and Save unit works.
- `/r` Given the same setting, When she opens **My Units** and "Source details", Then tiles and details show names, weights, versions and Evidence badges, and no kcal or macro figures.
- `/s` Given the same Unit saved with "Hide numbers" on and then off, When `GET /v1/units/{id}` is read, Then both responses are identical — the view hides, it never changes the Unit.
