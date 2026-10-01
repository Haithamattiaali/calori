# Eater — journeys for WF-5 Plan a meal and confirm it, WF-7 Activity, WF-8 Reports and progress

Lens: **Eater** (blueprint §1.2). Dispatch: steps 2, 3 and 5 of `way/personas/_lens-brief.md` for WF-5, WF-7 and WF-8. Written 2026-10-01.
Research (step 1) and the experience (step 4) are in `way/personas/eater/research.md`. This file builds on them: findings are cited as `E1`…`E44`, experience requirements as `EX-01`…`EX-44`. Cycle-1 findings (C, F, P, R) are cited as corrected by `r1-refute-a.md` and `r1-refute-b.md`. Nothing refuted or doubtful is cited (not R34, nor the Cunningham detail of R42).
No new outside source was opened for this file. A claim that no FRD line, map line or E-finding supports is labelled `assumption`, and every one is listed in §8.

---

## 0 · How to read this file

- **Story ids** are `eater-5.n`, `eater-7.n` and `eater-8.n`. The journey number is the WF number.
- **Layers.** Every acceptance line is tagged. `/r` is runtime: observed in the served product, on the **iOS simulator** or through the **API over HTTP**. `/s` is system: components together, such as ledger → projection or import → dedupe. `/m` is module: one function's test, such as the planner's validator, the report projection or the dedupe rule. Every story has at least one `/r` line.
- **Trace.** Every story names its FRD lines (FR, AT, NFR, §) and the map line it serves (blueprint §1: a workflow, an interaction row or a done-when). Research ids show *why*, never *what*.
- **Shared.** A story that another persona's lens also covers carries **Shared: Eater · <persona>** and that lens's story id.
- **Synthetic.** Every person, food, number and date below is synthetic. Fixture nutrient values are calibration fixtures in the FRD's sense, not product data. Proposed English and Arabic button labels go to the string catalogue once (blueprint §1.4). They are listed in §6.
- **No internal ids on screen.** Quoted on-screen text never contains FR/AT numbers, error codes or field names. Codes appear only in API lines.

### 0.1 One name per thing

These are the only names this file uses for screens, states and actions. Every one is the map's word, a `way/vocabulary.md` (D2) word or the FRD's screen name. New names are marked *proposed* and listed in §6.

| kind | name | where it comes from |
|---|---|---|
| tabs | Today · Capture & Plan · My Units · Progress | map §1.4 |
| places | tabs as above; screens Analysis review · Meal planner · Meal review · Settings (sections Goals · Food rules · Activity · Units & language · Privacy · Export); inside tabs: the camera on Capture & Plan, the saved Plans list on Capture & Plan, the Activity sheet opened from Today's Activity row, the Day report opened from Today's remaining figure, and Progress sections Weight and Target history | `way/vocabulary.md` (D2) Places; "meal and day report", "target history" (map §1.3, §1.4); the Activity sheet and the saved Plans list are *proposed* (§6) |
| photo choice | "Plan a meal" · "Log what I ate" | FRD §2.4 |
| Meal planner actions | "Find counts" (*proposed*) · "Save plan" (*proposed*) | FRD §14 Meal planner |
| Plan confirmation actions | "Ate as planned" · "Change amounts" · "Not eaten" · Meal review: "Save consumed" · *proposed:* "Log leftovers" | FRD §2.5, §14 |
| Plan states (API `state`) | **Proposed** (`proposed`) · **Infeasible** (`infeasible`) → **Saved** (`saved`) → **Confirmed** (`confirmed`, as **Ate as planned** or **Changed**) · **Not eaten** (`not_eaten`). A Plan has no consumed calories until Confirmed. On screen the state word comes first, then one plain sentence, e.g. "Proposed — fits all your limits", "Infeasible — no plan fits these limits", "Saved — not counted until you confirm" | `way/vocabulary.md` (D2) States; FRD §14 "pending confirmation" = Saved |
| solver result (API `solution_status`, FRD §17 MealPlan) | `optimal` · `feasible` (time limit reached with a plan) · `infeasible` · `unknown` (time limit, no plan) — CP-SAT statuses (FRD §9.1, [S10]); the timeout outcome has no Plan state yet (§7, item 1) | FRD §9.1, §17 |
| a limit check on a Plan | "met" · "above your maximum" / "below your minimum" (a check result, not a Plan state) | FR-053, FR-055 |
| Entry states | Pending → Confirmed; Corrected; Voided → Restored | `way/vocabulary.md` (D2) |
| errors | `VALIDATION_ERROR` · `PLAN_INFEASIBLE` · `POLICY_FLOOR` · `STALE_REVISION` · `AI_UNAVAILABLE` · `RATE_LIMITED` · `CONSENT_REQUIRED` · `NOT_FOUND` | `way/vocabulary.md` (D2) Errors |
| limits | Calorie target (about, with a tolerance) · Calorie ceiling (strict) · Carbohydrate maximum · Protein minimum · Exclude · Must include · Available · Preference shares (basis: count · mass · calories) · Whole counts / Allow halves / Allow grams | FR-049–FR-051, FRD §9.1 |
| Activity | **Activity** (our record; Pending → Confirmed, Voided → Restored by analogy with Entry, §7, item 17) · Health workout (the HealthKit record it came from) · active energy · Activity mode: **Fixed** / **Activity-adjusted** · credit factor · credit cap | map §1.4; FRD §12.1, §12.2 |
| reports | meal report · Day report · period report (7 days · 28 days · Custom) | FR-069, FR-072 |
| Day states | Provisional (today) · Complete · Partial · Unlogged; Complete and Partial are the eater's own mark | `way/vocabulary.md` (D2); FR-073, FR-074, FR-060 |
| Target comparison | "Intake vs Target" (never "vs plan": the map's **Plan** is the meal Plan) | FR-072, FR-074; see §7, item 3 |
| weight | Weight (data: weight observation) · "Unusual — check" | FRD §17 WeightObservation |
| views and modes | Hide numbers · tracking-only · diary-day boundary · Kill switch (On/Off, platform admin) | map §1.6, §5; `way/vocabulary.md` (D2) |

### 0.2 Fixtures (all synthetic)

**Eaters** (from research.md Part 2 §2, extended):

| eater | language and digits | zone | diary-day boundary | Target | Activity mode |
|---|---|---|---|---|---|
| **Mona** | Arabic (Egyptian), Arabic-Indic digits | Africa/Cairo | 03:00 | 1,870 until 2026-09-14 · 1,750 from 2026-09-15 · 1,700 from 2026-10-01 (story 8.11 only) | Fixed |
| **Faisal** | Arabic (Gulf), Western digits | Asia/Riyadh | 05:00 with the Ramadan option on (fixture value; the default hours are a model decision, EX-20) | 1,900 | Fixed |
| **Sam** | English | Europe/London | 00:00 (fixture) | 1,870: resting estimate 1,779 × 1.2 + 200 kcal planned exercise = 2,334.8 maintenance, −20 % = 1,867.84, approved as 1,870 (FRD §11.3) | Fixed; Activity-adjusted in 7.18–7.19 |

Policy version 1, In effect (approver-10.48): calorie floor 1,200 kcal, hard stop 1,000 kcal, planner increments whole by default.

**Units** (one count each; source energy equals 4/4/9 energy in every fixture except story 8.4):

| Unit (English / Arabic) | kind and bread | Evidence | P g | C g | F g | kcal | carbohydrate share |
|---|---|---|---|---|---|---|---|
| bread bite 8 g / لقمة عيش | simple | Measured | 0.7 | 4.1 | 0.2 | 21.0 | 78.10 % |
| foul spoon / معلقة فول (20 g filling) | dipped: + 1 bread bite | Recipe-calculated | 2.2 | 6.6 | 1.2 | 46.0 (filling alone 25.0) | 57.39 % |
| cheese bite / قرصة جبنة (5.4 g cheese + 1.5 g oil + 8 g bread) | Composite, bread inside | Measured | 1.7 | 4.3 | 2.6 | 47.4 | 36.29 % |
| cheese without bread / جبنة من غير عيش (variant, FR-024) | Composite, no bread | Measured | 1.0 | 0.2 | 2.4 | 26.4 | 3.03 % |
| egg bite / لقمة بيض (12 g egg) | dipped: + 1 bread bite | Measured | 2.2 | 4.2 | 1.4 | 38.2 | 43.98 % |
| olive / زيتونة | simple | Measured | 0.0 | 0.2 | 0.5 | 5.3 | 15.09 % |
| tuna bite / لقمة تونة (6.8 g tuna + 8 g bread) | Composite, bread inside | Measured | 2.4 | 4.1 | 0.8 | 33.2 | 49.40 % |
| kabsa rice spoon / ملعقة رز كبسة (25 g) | Recipe | Recipe-calculated | 0.9 | 7.0 | 1.2 | 42.4 | 66.04 % |
| chicken piece / قطعة دجاج (60 g) | simple | Measured | 15.0 | 0.0 | 6.0 | 114.0 | 0.00 % |
| salad spoon / ملعقة سلطة (30 g) | simple | Estimated analogue (low 11.0 – high 24.0 kcal) | 0.3 | 1.5 | 1.0 | 16.2 | 37.04 % |
| laban cup / كوب لبن (Gulf: yogurt drink, F27) | simple | Label-verified | 8.0 | 11.0 | 5.0 | 121.0 | 36.36 % |
| grilled chicken bite (15 g) | dipped: + 1 bread bite | Estimated analogue | 5.2 | 4.1 | 1.4 | 49.8 | 32.93 % |
| hummus bite (15 g) | dipped: + 1 bread bite | Estimated analogue (low 45.0 – high 75.0 kcal) | 1.9 | 6.2 | 2.9 | 58.5 | 42.39 % |
| fries handful (30 g) | simple | Estimated analogue | 1.0 | 12.0 | 4.5 | 92.5 | 51.89 % |
| sandwich quarter (test fixture for AT-20) | simple | Label-verified | 12.0 | 18.775 | 14.1 | 250.0 | 30.04 % |

Mona owns the Egyptian Units, Faisal the kabsa Units and Sam the restaurant Units. Arithmetic was checked with exact fractions in this run. **Display rule used throughout:** kcal are stored unrounded. Today, reports and plan rows show whole kcal. The limits list on Meal planner shows the unrounded value to one decimal, or more decimals when needed to differ from its limit (story 5.15).

---

## 1 · Goals

- **G5 · A straight answer in counts I can eat, that keeps my limits, and a meal that counts once, when I say I ate it.** Map WF-5 and its done-when; FRD §2.4, §2.5, §9; the moment in research Part 2 §3 ("How much of this kabsa fits my 600?", at the table, before eating).
- **G7 · My food budget never grows quietly; Activity sits beside it and is honest about what it does not know.** Map WF-7, interaction row "HealthKit → app → API"; FRD §12; E18.
- **G8 · An honest picture of my Days and weeks, without scolding, that always matches my Entries.** Map WF-8, interaction row "Ledger → Eater"; FRD §13; E19, E24, EX-27, EX-42.

---

## 2 · Journey 5 — Plan a meal and confirm it

| step | what the eater does | stories |
|---|---|---|
| A · start | from a table photo or a list; unknown dishes; foods without a Unit; what is available to me | 5.1–5.4 |
| B · set limits | calorie target or ceiling; carbohydrate maximum; protein minimum; exclusions; must-include; preference shares and basis; increments; bread rule; invalid input | 5.5–5.13 |
| C · read the answer | counts inside every limit; unrounded checks; estimate and Evidence; count vs calorie shares; nudge a count; explanation; change limits | 5.14–5.20 |
| D · Infeasible | blocking limit and smallest changes; must-include and bread never dropped; several blockers; no answer in time | 5.21–5.24 |
| E · safety | Policy floor; over Target; tracking-only | 5.25–5.27 |
| F · keep and confirm | zero until confirmed; Ate as planned; retries; Change amounts; extra bites; leftovers; Not eaten; next-morning confirmation; correction; offline; Unit changed | 5.28–5.38 |
| G · when things fail | offline planning; AI off or AI limit reached | 5.39–5.40 |
| H · inclusion | Arabic and digits; one thumb, large text, VoiceOver; Hide numbers | 5.41–5.43 |

### A · Start a plan

#### eater-5.1 · Plan from a photo of the shared table
As the Eater, I photograph the tray and choose "Plan a meal", so that the dishes become foods I can choose from and nothing is counted as eaten. · Trace: FRD §2.4, FR-037, FR-038, FR-039 (intent "plan"), FR-045, AT-27; map WF-5, row "Eater → planner"; E6, E7, EX-04
- `/r` Given Faisal (Arabic) on the Capture & Plan camera with a photo of a kabsa tray shared by four, When the photo is taken, Then two buttons sit on the photo, «خطّط وجبة» ("Plan a meal") and «سجّل ما أكلت» ("Log what I ate"), and neither is preselected.
- `/r` Given he taps «خطّط وجبة», When the Analysis reaches Ready for review, Then Meal planner opens with the chips «رز كبسة · دجاج · سلطة · لبن» marked as available foods, and Today's remaining figure is unchanged.
- `/r` Given the same photo, When `POST /v1/analyses` returns, Then the Analysis has intent `plan`, state `ready_for_review` and `items[]` listed as available foods, and `GET /v1/reports/day` for the Day returns the same consumed kcal and `revision` as before the photo.
- `/s` Given hands and faces of the other diners in the photo, When the image is uploaded and analysed, Then the stored image has no EXIF metadata and the Analysis holds no attribute of any person (FR-038, FR-077).

#### eater-5.2 · Plan from a list of what is on the table
As the Eater, I build the list of available foods from my Units, a typed line or a Template, so that I can plan without a photo, or when the camera is not allowed. · Trace: FRD §2.4, FR-048, FR-015, FR-076; map WF-5; E2, E30, E42, EX-02, EX-10
- `/r` Given Mona with recent Units قرصة جبنة, معلقة فول and لقمة بيض, When she opens Capture & Plan → Meal planner → «أضف الأكل المتاح» ("Add available foods"), Then her recent Units are offered first as tiles with picture and name, and three taps add them as chips.
- `/r` Given she types «فول، جبنة، بيض» or "fool, cheese, egg", When the line is resolved, Then each word maps to her own Unit (معلقة فول, قرصة جبنة, لقمة بيض), and a word that matches nothing stays a chip reading «يحتاج وحدة» ("needs a unit").
- `/r` Given a Template «فطار البيت» ("home breakfast") with 4 Units, When she chooses «استخدم أكل قالب» ("Use a Template's foods"), Then its 4 Units become chips with no counts, and no Entry is created.
- `/r` Given camera access is denied, When she opens the Capture & Plan camera, Then the screen says in one line that the camera is off and offers "Choose foods" to plan from a list (FR-076: refusal keeps other functions).
- `/r` Given a list made only of Units, When `POST /v1/meal-plans` is called, Then it is accepted with no `POST /v1/analyses` in the request log (planning from a list makes no AI call).

#### eater-5.3 · An unknown dish: at most two questions, then leave it out or make a Unit
As the Eater, I answer at most two questions about a dish the app does not know, then leave it out or make my Unit, so that one strange dish never blocks the plan. · Trace: FRD §2.4 ("asks about unrecognized foods"), FR-035, FR-048; E12, EX-25
- `/r` Given Faisal's chips include «سمبوسة» with no matching Unit or Recipe, When Meal planner opens, Then the Analysis is in Needs answers, one question asks «أي سمبوسة؟ لحم · جبنة · غير ذلك» ("Which sambosa? meat · cheese · other"), and no more than two questions appear in the pass.
- `/r` Given a chip still has no counting unit after the questions, Then it reads «يحتاج وحدة» with «اصنع وحدتك» ("Make your unit") and «اتركه» ("Leave out"), and "Find counts" stays enabled for the other foods.
- `/r` Given he taps «اصنع وحدتك», goes to the Unit editor and comes back (back points right), Then Meal planner shows the same chips and limits as before.
- `/r` Given a plan request with the unresolved chip, When `POST /v1/meal-plans` returns, Then the chip is in `left_out[]` with reason `no_counting_unit`, and no row for it appears in the counts.

#### eater-5.4 · Say how much is available to me
As the Eater, I set how many pieces are available to me, so that the plan never gives me more than my share of the tray. · Trace: FR-049 (available-quantity limits), FR-037, FR-033; E6, E7
- `/r` Given the chip قطعة دجاج on Faisal's Meal planner, When he sets «المتاح: 3» ("Available: 3"), Then the chip shows "≤ 3" and no result has more than 3 chicken pieces.
- `/r` Given no availability is set for a chip from a photo, Then the chip reads «بدون حد» ("No limit set"), never a count guessed from the photo.
- `/r` Given chicken is Must include and Available is set to 0, Then the Available field shows «لا يمكن أن يكون صفرًا لطعام مطلوب» ("Can't be 0 for a must-include food"), "Find counts" is disabled, and the same request to `POST /v1/meal-plans` returns `VALIDATION_ERROR` naming `available`.
- `/m` Given Available 3 for the chicken Unit, When the planner's model is built, Then the count variable for chicken has upper bound 3.

### B · Set the limits

#### eater-5.5 · Calorie target or calorie ceiling, never confused
As the Eater, I choose "about 450" or "not above 500", so that the planner aims where I mean and treats a ceiling as strict. · Trace: FR-049, FRD §9.1 ("About 500" may use an explicit tolerance; "not above 500" is strict); EX-10
- `/r` Given Faisal's Target 1,900 and 1,260 kcal consumed today, When Meal planner opens, Then Calorie target reads «حوالي 640 — المتبقي اليوم» ("About 640 — what's left today") with its source, and no Calorie ceiling is set.
- `/r` Given Calorie ceiling 500, Then the field reads «لا يزيد عن 500 سعرة» ("Not above 500 kcal"); given Calorie target "about 450" with tolerance ±10 %, Then the field shows the tolerance as «405–495».
- `/r` Given Calorie ceiling 400 and Calorie target "about 450", Then the ceiling field says «الحد الأعلى أقل من الهدف — اخفض الهدف أو ارفع الحد» ("The ceiling is below the target — lower the target or raise the ceiling"), and "Find counts" is disabled.
- `/m` Given Calorie ceiling 500, When the validator checks a plan of 500.0 kcal and one of 500.04 kcal, Then the first passes and the second fails.

#### eater-5.6 · Carbohydrate maximum, on macro energy (4/4/9)
As the Eater, I set a carbohydrate maximum and see what it is a share of, so that "30 %" means the same thing in the plan and in my reports. · Trace: FR-049, FRD §9.1 (4C ≤ r × E_macro), §10.1 (denominator label; same convention in planner and reports)
- `/r` Given «حد الكربوهيدرات 30 ٪» ("Carbohydrate maximum 30 %") on Meal planner, Then the caption under the field reads «من الطاقة المحسوبة من المغذيات (4/4/9)» ("share of macro-derived energy (4/4/9)").
- `/m` Given 4 kabsa rice spoons + 2 chicken pieces (P 33.6, C 28.0, F 16.8 g; 397.6 kcal), When checked against 30 %, Then the share is 28.17 % and passes; 5 rice + 2 chicken (440.0 kcal) gives 31.82 % and fails.
- `/r` Given 0 or 130 typed in the field, Then «أدخل رقمًا من 1 إلى 100 ٪» ("Enter 1 to 100 %") appears beside it and no request is sent.

#### eater-5.7 · Protein minimum in grams or as a share
As the Eater, I set a protein minimum in grams or as a share, so that the meal keeps the protein I want. · Trace: FR-049, FR-005
- `/r` Given Faisal sets «أقل بروتين 30 غ» ("Protein minimum 30 g"), When counts are found, Then the limits list shows "Protein ≥ 30 g" with the plan's unrounded protein, and every result has P ≥ 30 g.
- `/r` Given a minimum of 25 % instead, Then the limits list reads "Protein ≥ 25 % of macro-derived energy (4/4/9)", and `POST /v1/meal-plans` checks 4P ≥ 0.25 × E_macro.
- `/r` Given −5 or 120 %, Then the field shows the fix beside it and "Find counts" is disabled.

#### eater-5.8 · Exclusions from my settings and for this meal
As the Eater, I keep my standing exclusions and add one for this meal, so that excluded food never appears in a plan. · Trace: FR-049, FR-008, FRD §2.1 (Settings: food rules), §19.3 (allergy absence cannot be certified from a photo)
- `/r` Given "olives" under Sam's Settings → Food rules exclusions and a photo whose chips include olives, When Meal planner opens, Then the olives chip reads "Excluded (your settings)" and no result contains olives.
- `/r` Given he adds "fries" under Exclude on Meal planner, Then the fries chip reads "Excluded for this meal" and Settings → Food rules still lists only "olives".
- `/r` Given an exclusion named "sesame" on a photo-based list, Then a note beside it reads "A photo can't show everything a dish contains — check the ingredients", and no result is labelled "sesame-free".
- `/r` Given every chip is excluded, Then Meal planner reads "Nothing left to plan — every food is excluded" with "Edit exclusions", and "Find counts" is disabled.

#### eater-5.9 · Must-include foods
As the Eater, I mark foods I will eat anyway, so that the plan is built around them, not without them. · Trace: FR-049, AT-19
- `/r` Given Sam marks "Must include: fries ≥ 1", When counts are found, Then every result has fries ≥ 1 counted at their full 92.5 kcal each.
- `/r` Given fries is both under Must include and under Exclude, Then the second field says "Fries can't be both required and excluded", and "Find counts" is disabled.
- `/r` Given `POST /v1/meal-plans` with the same food in `mandatory[]` and `exclusions[]`, Then it returns `VALIDATION_ERROR` naming both fields, and no Plan is created.

#### eater-5.10 · Preference shares, with the basis always visible
As the Eater, I say how I would like the meal split and by what (count, mass or calories), so that the plan's split means what I meant. · Trace: FR-050, AT-18
- `/r` Given Mona enters shares 30 / 20 / 5 / 40 / 5 for foul, cheese, olive, egg and tuna, Then a «الأساس» ("Basis") control shows Count · Mass · Calories with none selected, and "Find counts" stays disabled until she picks one.
- `/r` Given her shares add to 95, Then Meal planner reads «المجموع 95 ٪» ("Shares total 95 %") and offers «اجعلها 100 ٪» ("Scale to 100 %") or editing; nothing changes until she taps one.
- `/r` Given basis Count, When counts are found, Then the result header reads «النسب حسب العدد» ("Shares by count"), and `POST /v1/meal-plans` echoes `preference.basis: "count"`.
- `/m` Given basis Mass, When shares are computed, Then each row's mass is the expanded portion, bread included (a foul bite = 28 g).

#### eater-5.11 · Whole counts by default; halves or grams only when I allow them
As the Eater, I get whole bites and spoons unless I turn on halves or grams, so that the plan is something I can actually eat. · Trace: FR-051; map §1.6 Policy "planner increments" · **Shared: Eater · Nutrition approver** (approver-10.55)
- `/r` Given default settings, When counts are found, Then every count on Meal planner is a whole number and no "½" appears.
- `/r` Given Settings → Units & language → «اسمح بالأنصاف» ("Allow halves") on, Then counts may end in ½; given «اسمح بالجرامات» ("Allow grams") off, Then no gram field on the result can be edited.
- `/r` Given halves are off in the eater's settings, When `POST /v1/meal-plans` is called, Then every returned count is an integer.
- `/m` Given halves on, When the model is built, Then each count variable is an integer number of halves (CP-SAT integer modelling, FRD §9.1).

#### eater-5.12 · Bread with every dipped bite, counted before any check
As the Eater, I see each dipped bite's bread in the plan and in its numbers, so that "fits 500" includes the bread I really eat. · Trace: FR-052, FR-018, FR-019, FR-020, AT-04; FRD §2.4 ("bread with every bite"); E5
- `/r` Given Mona's foul spoon (dipped, 8 g bread rule) and cheese bite (bread inside), When counts are found, Then each foul row reads «فيها 8 غ عيش» ("Includes 8 g bread") and the cheese row reads «العيش جزء من اللقمة» ("Bread is part of the bite"), and 3 foul bites show 138 kcal, not 75.
- `/m` Given the validator receives a foul count of 3, Then it checks the expanded vector (46.0 kcal, P 2.2, C 6.6, F 1.2 per count), and a cheese bite count adds no second bread (47.4 kcal).
- `/r` Given a meat bite with the exception "5 g bread" (FR-020), Then its row reads «فيها 5 غ عيش».
- `/r` Given no Unit that puts two fillings on one bread bite, When `POST /v1/meal-plans` returns, Then for every dipped filling in `counts[]` the bread count in `components[]` equals the filling count.

#### eater-5.13 · Wrong limits are caught beside the field, in either digit system
As the Eater, I type limits in Arabic-Indic or Western digits and get told at once what is wrong, so that I never send a plan request that cannot mean anything. · Trace: FR-049, FRD §14.1 (both numeral systems, decimal input), §18.2; E41, EX-23, EX-39
- `/r` Given Mona types «٥٠٠» in Calorie ceiling, Then it is stored as 500 and shown as «٥٠٠»; typing "500" sends the same request.
- `/r` Given «٣٠٫٥» in Carbohydrate maximum, Then it is read as 30.5 %.
- `/r` Given −200 or «ابc» in Calorie ceiling, Then «أدخل رقمًا أكبر من صفر» ("Enter a number above 0") appears beside the field, "Find counts" is disabled and no request is sent.
- `/r` Given `POST /v1/meal-plans` with `carb_max_share: 1.3`, Then it returns `VALIDATION_ERROR` naming `carb_max_share`, and no Plan is created.

### C · Read the answer

#### eater-5.14 · A plan in counts I can eat, inside every limit
As the Eater, I tap "Find counts" and get counts with their numbers and every limit checked, so that I know before I reach for the tray. · Trace: FR-055, FR-053, FRD §9, §9.1, NFR-04; map WF-5 done-when ("cap 500 kcal + carbs ≤30 % → counts satisfying unrounded constraints"); E33, EX-11, EX-15
- `/r` Given Faisal's chips kabsa rice spoon, chicken piece (Available 3), salad spoon and laban cup, Calorie ceiling 500 and Carbohydrate maximum 30 %, When he taps «احسب الكميات» ("Find counts"), Then within 2 s Meal planner shows «مقترحة — ضمن كل حدودك» ("Proposed — fits all your limits"), a row per food used with count, component weights, kcal and P/C/F, the totals, and «احفظ الخطة» ("Save plan") as the main button (a Proposed Plan is Saved before it can be Confirmed).
- `/r` Given the same request to `POST /v1/meal-plans`, When it returns a Plan in state `proposed` and its counts are recomputed from the Unit vectors, Then E_source ≤ 500 and 4C ≤ 0.30 × E_macro on unrounded values. 4 rice + 2 chicken (397.6 kcal, 28.17 %) is a valid answer; 5 rice + 2 chicken (440.0 kcal, 31.82 %) is never returned.
- `/r` Given a result, Then the limits list shows each limit with its unrounded value and a word, for example "Calorie ceiling 500 kcal — 397.6 · met" and "Carbohydrate ≤ 30 % — 28.17 % · met" (EX-35: a word, not colour alone).
- `/s` Given 20 candidate Units with bounded counts, When the planner test harness runs 100 solves, Then p95 solve-and-validate time is ≤ 2 s (NFR-04).

#### eater-5.15 · Every limit is checked on unrounded numbers (AT-20)
As the Eater, I can trust that a plan marked as fitting really fits, so that a rounded "30.0 %" never hides 30.04 %. · Trace: FR-053, AT-20
- `/m` Given one sandwich quarter (P 12, C 18.775, F 14.1 g; 250.0 kcal) and Carbohydrate maximum 30.00 %, When validated, Then the share is 75.1 / 250.0 = 30.04 % and the check fails.
- `/r` Given that food as Must include 1 and Available 1 with Carbohydrate maximum 30 %, When `POST /v1/meal-plans` is called, Then the Plan's `state` is `infeasible` and `blocking[]` holds `carb_max_share` with `value` 0.3004 and `limit` 0.30.
- `/r` Given the same on Meal planner, Then the result reads "Infeasible — no plan fits these limits" with "Carbohydrate 30.04 % — above your 30 % maximum", never "30.0 %" beside "met".

#### eater-5.16 · An estimate is shown as an estimate, with its Evidence
As the Eater, I see where each number comes from and a low/high when a food is estimated, so that I never mistake a guess for a measurement. · Trace: FR-055 (estimate/range, evidence quality), FR-029, FR-033, FRD §9.1 (a ceiling is on the estimated value), §20.1; E8, EX-32
- `/r` Given a result of 4 rice + 2 chicken + 2 salad, Then each row carries its Evidence badge (Recipe-calculated, Measured, Estimated analogue), and the total reads "430 kcal · low 420 – high 446 (estimate, not a statistical interval)".
- `/m` Given salad low 11.0 and high 24.0 kcal per spoon, When the range is built, Then low = 430.0 − 2 × 5.2 = 419.6 and high = 430.0 + 2 × 7.8 = 445.6, and the Calorie ceiling is checked on 430.0, the estimate (FRD §9.1).
- `/r` Given a result whose rows are only Measured, Label-verified or Recipe-calculated, Then no range is shown.
- `/r` Given any result in English or Arabic, Then the words "exact" / «بالضبط» do not appear on Meal planner or in the API `explanation`.

#### eater-5.17 · Shares by count and by calories, reported apart (AT-18)
As the Eater, I see how my split by count turns out in calories, so that "40 % of bites" is never read as "40 % of calories". · Trace: FR-050, AT-18, FRD §10.2 (largest-remainder display)
- `/r` Given Mona's plan foul 6, cheese 4, olive 1, egg 8, tuna 1 with basis Count, Then two labelled rows show: «حسب العدد ٣٠٫٠ · ٢٠٫٠ · ٥٫٠ · ٤٠٫٠ · ٥٫٠ ٪» and «حسب السعرات ٣٤٫١ · ٢٣٫٤ · ٠٫٧ · ٣٧٫٧ · ٤٫١ ٪», and the total reads «٨١٠ سعرة».
- `/m` Given Calorie target about 809.7 with tolerance 0, shares by count 30 / 20 / 5 / 40 / 5 and no other limit, When solved, Then the counts are 6 / 4 / 1 / 8 / 1, the only answer with zero deviation.
- `/r` Given the same plan from `POST /v1/meal-plans`, Then `preference_report.count_shares` and `preference_report.calorie_shares` are separate arrays, and each displayed row adds to 100.0.

#### eater-5.18 · Nudge a count on the answer and see every limit checked again
As the Eater, I change a count on the result with a stepper, so that I can try "one more spoon" and see at once whether it still fits. · Trace: FR-053, FR-055, FRD §14 (Meal planner: counts); EX-13, EX-17
- `/r` Given the kabsa result rice 4 + chicken 2, When Faisal taps + on rice, Then within 300 ms the total reads 440 kcal, the limits list reads "Carbohydrate 31.82 % — above your 30 % maximum", and the header changes to «مقترحة — الكربوهيدرات أعلى من حدك» ("Proposed — carbohydrate above your maximum").
- `/r` When he taps − on rice, Then the header returns to «مقترحة — ضمن كل حدودك».
- `/r` Given he saves the plan with rice 5, When `POST /v1/meal-plans/{id}/validate` (*proposed*) is called with those counts, Then the server returns the same failing check, and the Saved Plan shows "Saved — carbohydrate above your maximum", never "fits all your limits".

#### eater-5.19 · The explanation repeats only verified numbers
As the Eater, I read a short explanation of why these counts were chosen, so that I understand the plan without the AI inventing numbers. · Trace: FRD §2.4 ("explains constraints"), §16.2 (AI may "explain a verified plan"), §15.2; E22
- `/s` Given a feasible plan with a generated `explanation`, When the planner test harness compares every number in the text with the validated `totals` and `checks`, Then every number is found; a text with any other number is dropped and the limits list is shown alone.
- `/r` Given the Kill switch for plan explanations is On, When counts are found, Then Meal planner shows the limits list with no generated text, and the header still reads "Proposed — fits all your limits".

#### eater-5.20 · Change a limit and find counts again
As the Eater, I change my mind about a limit and get a fresh answer, so that an old answer is never confirmed against new limits. · Trace: FR-049, FR-055; EX-17
- `/r` Given a result on Meal planner, When the Calorie ceiling is changed from 500 to 450, Then the result greys out with "Limits changed — Find counts again", and "Save plan" is disabled until counts are found again.
- `/r` Given the eater leaves Meal planner without "Save plan", Then the Plan stays Proposed and is not Saved: no Plan card appears on Today, and `GET /v1/meal-plans?state=saved` (*proposed*) does not list it.

### D · Infeasible: no plan fits

#### eater-5.21 · Infeasible: the blocking limit by name, and the smallest changes (AT-17)
As the Eater, I am told which limit blocks the plan and the smallest change that would fix it, so that I decide what to change and the bread never quietly disappears. · Trace: FR-054, AT-17, FRD §2.4 (substitution), FR-024; EX-23
- `/r` Given Mona's chips foul bite (57.39 %), cheese bite (36.29 %) and egg bite (43.98 %), each with its bread, Carbohydrate maximum 30 % and Calorie target about 300 (±10 %), When she taps «احسب الكميات», Then Meal planner reads «غير ممكنة — لا توجد خطة ضمن هذه الحدود» ("Infeasible — no plan fits these limits") and names the blocker: "Carbohydrate maximum 30 % — every food here is above it with its bread".
- `/r` Given the same result, Then two changes are offered: «ارفع الحد إلى ٣٦٫٣ ٪» ("Raise the maximum to 36.3 %") and, because she owns the Unit جبنة من غير عيش (3.03 %), «استخدم جبنة من غير عيش» ("Use your cheese without bread"). Nothing is applied until she taps one.
- `/r` Given she taps «ارفع الحد إلى ٣٦٫٣ ٪», When counts are found again, Then the result is 6 cheese bites (284 kcal, carbohydrate 36.29 %), the only food at or under 36.3 %.
- `/r` Given the response of `POST /v1/meal-plans`, Then the Plan's `state` is `infeasible`, `blocking[]` is `[carb_max_share]`, `changes[]` carries labels and new values, there are no `counts`, and every candidate's vector still includes its bread.
- `/r` Given any Infeasible result, Then no row shows "low-carb", and the egg bite reads «٤٤٫٠ ٪ — أعلى من الحد» ("44.0 % — above the maximum").
- `/r` Given that Infeasible Plan's id, When `POST /v1/consumption` is sent with it as `source_plan_id`, Then it returns `PLAN_INFEASIBLE` and no Entry is created.

#### eater-5.22 · Must-include foods and bread are never dropped to make it fit (AT-19)
As the Eater, I can trust that my required fries and my bread are always in the numbers, so that no side is "free". · Trace: AT-19, FR-049, FR-052, FR-054
- `/r` Given Sam's chips grilled chicken bite and hummus bite (each with bread) and fries handful, with Must include fries ≥ 1 and Calorie ceiling 500, When counts are found, Then the result includes fries ≥ 1, every chicken and hummus row reads "Includes 8 g bread", and the total equals the sum of the rows (for example 2 fries + 4 chicken + 1 hummus = 443 kcal).
- `/r` Given Calorie ceiling 80 instead, Then Meal planner reads "Infeasible — must-include fries (92.5 kcal) are above the 80 kcal ceiling" with the changes "Raise the ceiling to 93 kcal" and "Make fries optional".
- `/m` Given any candidate solution, When validated, Then it is rejected if a must-include count is below its minimum, if a dipped bite lacks its bread component, or if a row's kcal ≠ count × its Unit vector.

#### eater-5.23 · Several limits block together: each is named and each change is labelled
As the Eater, I see every limit that blocks the plan and one labelled change for each, so that I can pick the change that suits me. · Trace: FR-054, FR-049
- `/r` Given Faisal's Calorie ceiling 500, Protein minimum 60 g, chicken Available 3 and Carbohydrate maximum 30 %, When counts are found, Then «غير ممكنة — لا توجد خطة ضمن هذه الحدود» names three blockers together: protein minimum 60 g, calorie ceiling 500 kcal and chicken available 3.
- `/r` Given the same, Then three changes are offered, each enough alone: "Lower the protein minimum to 53.6 g", "Raise the ceiling to 584 kcal" and "Allow 4 chicken pieces".
- `/m` Given the fixture, When the planner computes the changes, Then the most protein under the other limits is 53.6 g (3 chicken + 2 salad + 1 laban = 495.4 kcal), the lowest ceiling reaching 60 g with ≤ 3 chicken is 584.0 kcal (3 chicken + 2 laban), and 4 chicken alone give 60 g at 456.0 kcal.
- `/r` Given `POST /v1/meal-plans`, Then `blocking[]` has three entries and `changes[]` has `new_value` 53.6, 584 and 4.

#### eater-5.24 · No answer in time is said plainly
As the Eater, I am told when the planner ran out of time, so that "no answer yet" is never shown as Infeasible or as a plan that fits. · Trace: NFR-04 ("explicit no-solution/timeout status"), FRD §9.1 (solver statuses [S10]), §18.2 ("never produces a fabricated success")
- `/r` Given the planner's time limit is reached after a plan was found, Then Meal planner reads "Proposed — best found in time; all your limits checked", and every limit in the list is verified.
- `/r` Given the time limit is reached with no plan found, Then no Plan is created, the screen reads "No answer in time — try fewer foods or fewer limits", shows no counts, and never reads "Infeasible".
- `/r` Given a test configuration that sets the solver time limit to 1 ms (`assumption`: a test-only setting), When `POST /v1/meal-plans` is called with 20 candidates, Then `solution_status` is `unknown` (no Plan) or `feasible` (a Plan in state `proposed`), never `infeasible`.
- `/r` Given a solve that is still running after 1 s, Then a progress indicator with "Cancel" shows, and Cancel returns to the limits with the list kept.

### E · Safety

#### eater-5.25 · The planner keeps to the Policy floor
As the Eater, I plan meals inside my approved Target, and a request for a starvation day gets a safe answer, so that the planner never helps me eat below a safe level. · Trace: map row "Eater → planner" ("floor policy respected"), map §1.6 Policy; FRD §19.3 ("Dangerous restriction requests require a safe response rather than a mathematically optimized starvation plan"), §3.3, §11.4; AT-17 ("nonempty meal"); R32, R33 · **Shared: Eater · Nutrition approver** (approver-10.48, approver-10.50)
- `/r` Given Mona's approved Target 1,750 and 1,350 kcal consumed, When Meal planner opens, Then Calorie target reads «حوالي ٤٠٠ — المتبقي اليوم» and Meal planner has no field that changes the Day's budget; after "Save plan", Today's Target still reads «١٬٧٥٠».
- `/r` Given she types in Capture & Plan «خطط يومي كله على ٧٠٠ سعرة» ("plan my whole day at 700 kcal"), When it is read as a plan request, Then no counts are found and the screen says «خطط اليوم الكامل لا تقل عن ١٬٠٠٠ سعرة، وهو الحد الأدنى المراجَع. تقدري تخطّطي وجبة واحدة أو تراجعي هدفك.» ("Whole-day plans stay at or above 1,000 kcal, the reviewed minimum. You can plan one meal or review your Target.") with «خطّط وجبة واحدة» and «راجع هدفي».
- `/r` Given `POST /v1/meal-plans` with `scope: "day"` (*proposed*) and `calorie_ceiling_kcal: 700`, Then it returns `POLICY_FLOOR` with the hard stop of the Policy version In effect (1,000 kcal), no Plan is created, and the response offers no option below 1,000.
- `/m` Given any plan request, When the model is built, Then it requires the sum of counts ≥ 1, so "eat nothing" is never a plan.

#### eater-5.26 · Over my Target today: no skipping, no scolding
As the Eater, I can still plan a meal after an over-Target day, so that the app never suggests skipping a meal to make up for it. · Trace: FRD §14.2 ("shall not recommend skipping meals to 'repay' an overage"), §11.4; FR-070; EX-42
- `/r` Given Mona has consumed 1,900 against her Target 1,750, When Meal planner opens, Then Calorie target is empty with «اليوم أعلى من هدفك بـ ١٥٠ سعرة. ضعي حدًا لهذه الوجبة إن أردتِ.» ("Today is 150 kcal over your Target. Set a limit for this meal if you like."); no negative target appears, and no red fill is used.
- `/r` Given any Meal planner result or message in English or Arabic, Then none of the string-catalogue words for "bad", "cheat", "failed", "skip" or "make up for" appears on Meal planner (catalogue check run against the served screens).

#### eater-5.27 · In tracking-only mode the planner helps without restricting
As the Eater in tracking-only mode, I can still get counts from what is on the table, so that I am helped without being given a restrictive plan. · Trace: FRD §11.4 ("do not generate restrictive plans"), §3.3, FR-008; map §5 WF-1 done-when (tracking-only); R37, R38; EX-44 · **Shared: Eater · Nutrition approver** (approver-10.53)
- `/r` Given an eater in tracking-only mode after a pregnancy answer, When Meal planner opens, Then Calorie target, Calorie ceiling and Carbohydrate maximum are absent; Available, Must include, Exclude, Preference shares and Protein minimum remain; and a Saved Plan can be Confirmed with "Ate as planned".
- `/r` Given the same account, When `POST /v1/meal-plans` is sent with `calorie_ceiling_kcal`, Then it returns `VALIDATION_ERROR` naming `calorie_ceiling_kcal` with the reason "tracking-only mode".
- `/r` Given the result, Then it shows food names and counts with neutral words and no Target status.

### F · Keep the plan and confirm what I ate

#### eater-5.28 · A Saved Plan counts zero until I confirm it
As the Eater, I save a Plan and see it waiting on Today, so that planning is never mistaken for eating. · Trace: FR-045, FRD §2.5, §17 MealPlan ("Has no consumed calories until committed"); map WF-5; `way/vocabulary.md` (Plan: "no consumed calories until Confirmed"); E19, EX-14
- `/r` Given Faisal taps «احفظ الخطة» ("Save plan") on the Proposed plan rice 4 + chicken 2 (397.6 kcal) at 21:30, Then Today shows a card «خطة · محفوظة — لا تُحسب حتى تؤكد» ("Plan · Saved — not counted until you confirm") with the three confirmation buttons, and the remaining figure still reads 640.
- `/r` Given the Saved Plan, When `GET /v1/reports/day` is called, Then consumed kcal and `revision` equal the values before saving, and `GET /v1/meal-plans/{id}` returns `state: "saved"`.
- `/r` Given another eater's token, When `GET /v1/meal-plans/{id}` is called with Faisal's Plan id, Then it returns `NOT_FOUND` (NFR-07).
- `/s` Given the Day's ledger is replayed from its events, Then the totals are the same with or without the Saved Plan (FR-042).

#### eater-5.29 · "Ate as planned" records the Plan, once (AT-21)
As the Eater, I tap "Ate as planned" after the meal, so that exactly what I planned is logged as one meal. · Trace: FRD §2.5, FR-045, FR-040, §18.1 (`source_plan_id`), AT-21; map WF-5 done-when ("Ate as planned records exactly one meal"); EX-09, EX-13
- `/r` Given the Saved Plan, When Faisal taps «أكلت كما في الخطة» at 22:15, Then two Entries (kabsa rice spoon × 4, chicken piece × 2) appear on Today as one meal, the remaining figure goes from 640 to 242, the meal report shows 398 kcal, and the card reads «مؤكدة · أكلت كما في الخطة» ("Confirmed · Ate as planned").
- `/r` Given the same, When `POST /v1/consumption` is sent with `source_plan_id` and a command id, Then the accepted Entries are Confirmed and their totals equal the Plan's (397.6 kcal), and `GET /v1/meal-plans/{id}` returns `state: "confirmed"`, `confirmed_as: "ate_as_planned"` and both `entry_ids`.
- `/r` Given the Undo banner «تراجع: خطة الكبسة (٤ ملاعق رز، قطعتا دجاج)», When Undo is tapped, Then both Entries are Voided, the remaining figure returns to 640, and the card reads «محفوظة — لا تُحسب حتى تؤكد» again (the Confirmed → Saved step on Undo is §7, item 2).

#### eater-5.30 · Confirming twice, retrying or a second device never adds a second meal (AT-21)
As the Eater, I can tap twice, lose the network or confirm on another device, so that the meal is still counted once. · Trace: AT-21, FR-043, FR-045, AT-10, AT-31, FRD §8.3, §18 ("A 409 conflict returns the current revision")
- `/r` Given the confirmation command for the Plan is delivered three times with the same command id, When `GET /v1/reports/day` is called, Then there is one meal of 397.6 kcal and the Day revision moved once.
- `/r` Given "Ate as planned" is double-tapped on the simulator, Then one meal is logged, and the button is disabled from the first touch.
- `/r` Given the Plan was Confirmed on the iPhone, When "Ate as planned" is tapped on an iPad that still shows it as Saved (different command id), Then the API returns 409 `STALE_REVISION` with the Plan's current state, and the iPad shows «تم تأكيدها الساعة 22:15 — لم يُضف شيء» ("Already confirmed at 22:15 — nothing added").
- `/m` Given a `source_plan_id` already linked to Confirmed Entries, When the ledger receives another consume command with that plan id, Then it rejects it, whatever the command id.

#### eater-5.31 · Change amounts: I log what I actually ate
As the Eater, I change the counts to what I really ate, so that my Day is true even when I did not follow the plan. · Trace: FRD §2.5, §14 Meal review (actual count steppers, plan comparison, Save consumed), FR-045; EX-17
- `/r` Given the Saved kabsa Plan, When Faisal taps «غيّر الكميات» ("Change amounts"), Then Meal review opens with steppers at 4 and 2 and the Plan's counts in a column beside them.
- `/r` When he sets rice to 3 and taps «احفظ ما أكلت» ("Save consumed"), Then Today shows kabsa rice spoon × 3 and chicken piece × 2 (355 kcal), the remaining figure reads 285, and the card reads «مؤكدة · بتغيير (رز 3 من 4)» ("Confirmed · Changed (rice 3 of 4)").
- `/r` Given he sets rice to 5 instead (440.0 kcal, carbohydrate 31.82 %), Then Meal review shows "Carbohydrate 31.82 % — above your 30 % maximum" as information, and "Save consumed" stays enabled: what was eaten can always be logged.
- `/r` Given the save, When `POST /v1/consumption` is inspected, Then it carries `source_plan_id` and the actual counts, `GET /v1/meal-plans/{id}` returns `confirmed_as: "changed"`, and a second save for the same plan id is rejected (5.30).

#### eater-5.32 · "Two extra egg bites": an adjustment before I confirm, an addition after
As the Eater, I say I ate two more, so that it changes the counts if I have not confirmed yet, and adds an Entry if I have. · Trace: FRD §2.5 ("becomes a count adjustment or a new consumption event, depending on the referenced meal"), §8.2, FR-039, FR-036; E42, E43, EX-40
- `/r` Given Mona's Saved Plan with egg bite × 8 and Meal review open, When she says or types «أكلت لقمتين بيض زيادة» ("I ate two extra egg bites"), Then a chip shows the transcript, the egg stepper moves from ٨ to ١٠, and nothing is committed until «احفظ ما أكلت».
- `/r` Given the Plan is already Confirmed with egg bite × 8, When she says the same on Today, Then a confirmation chip «أضف ٢ لقمة بيض للفطار» ("Add 2 egg bites to breakfast") logs a new Entry of 2 egg bites (76 kcal) in the same meal, and the 8-bite Entry is unchanged.
- `/r` Given she says «كانت ١٠ مش ٨» ("it was 10, not 8") instead, Then the Correction preview opens (WF-6) with old ٨, new ١٠ and delta +٧٦ — not a new Entry (AT-26).

#### eater-5.33 · A partial meal and its leftovers
As the Eater, I log the part I ate now and the leftovers when I eat them, so that the meal is complete without being counted twice. · Trace: FRD §14 Meal review states ("partial meal, leftovers"), FR-045, FR-043
- `/r` Given the Plan Confirmed as Changed with rice 3 of 4, Then the Plan card on Today reads «باقي: ملعقة رز ١» ("Leftovers: 1 rice spoon") with «سجّل الباقي» ("Log leftovers").
- `/r` When Faisal taps «سجّل الباقي» at 23:40, Then one Entry of kabsa rice spoon × 1 (42 kcal) is logged at 23:40 on the same Day (before the 05:00 boundary), and the card reads «مؤكدة · بتغيير · الباقي مسجّل» ("Confirmed · Changed · leftovers logged").
- `/r` Given «سجّل الباقي» is tapped twice or its command retried, Then one Entry is logged, and the button is gone afterwards.

#### eater-5.34 · Not eaten
As the Eater, I mark a Plan "Not eaten", so that it is closed and counts nothing. · Trace: FRD §2.5, FR-045; `way/vocabulary.md` (Plan: Not eaten)
- `/r` Given a Saved Plan, When «لم تُؤكل» ("Not eaten") is tapped, Then the card reads «لم تُؤكل» ("Not eaten") with Undo, no Entry is created, and Today's totals are unchanged.
- `/r` Given the same, When `GET /v1/meal-plans/{id}` is called, Then `state` is `not_eaten` and `entry_ids` is empty.
- `/r` Given Undo is tapped before the banner closes, Then the card reads «محفوظة — لا تُحسب حتى تؤكد» again with the three buttons (the Not eaten → Saved step on Undo is §7, item 2).

#### eater-5.35 · Confirm the next morning, onto the right Day
As the Eater, I confirm last night's Plan the next morning, so that it lands on the Day I ate it. · Trace: FR-047, FRD §8.1, FR-044, FR-040 (eating timestamp); E9, E11, E24; EX-07, EX-20
- `/r` Given Faisal's Plan Saved on Day 2027-02-10 (Ramadan, boundary 05:00) at 21:30 and not confirmed, When he opens Today on 2027-02-11 at 08:10, Then a card reads «من أمس: خطة الكبسة — محفوظة، لا تُحسب حتى تؤكد» ("From yesterday: kabsa plan — Saved, not counted until you confirm") with the three buttons.
- `/r` When he taps «أكلت كما في الخطة», Then the Entries land on Day 2027-02-10 with eating time 21:30 (editable before saving), the Day report for 2027-02-10 rises by 398 kcal, and Today (2027-02-11) is unchanged.
- `/r` Given a suhoor Plan Saved at 03:40 on 2027-02-11 by the clock, When it is confirmed, Then its Entries belong to Day 2027-02-10, the same Day as that evening's iftar.

#### eater-5.36 · Correcting a Confirmed Plan's meal keeps the link
As the Eater, I correct a count after confirming, so that the meal is fixed without running the Plan again. · Trace: FRD §2.6, FR-041, FR-046; WF-6
- `/r` Given the Plan Confirmed with rice × 4, When Faisal corrects the rice Entry to «3 مش 4» ("3, not 4"), Then the Correction preview shows old 4, new 3, meal −42 and Day −42; after confirming, the old Entry is Corrected, the card reads «مؤكدة · أكلت كما في الخطة · الرز صُحّح إلى 3» ("Confirmed · Ate as planned · rice corrected to 3"), and no new Plan execution happens.
- `/r` Given the Correction, When `POST /v1/consumption/{id}/corrections` returns, Then the response holds old, new and delta, and the replacing Entry keeps its `source_plan_id`.

#### eater-5.37 · Confirm while offline
As the Eater, I confirm a Plan with no signal, so that it is logged now and synced once later. · Trace: FRD §8.3, FR-043, FR-045, NFR-06, AT-31; E38; EX-12, EX-21
- `/r` Given airplane mode on the simulator, When Faisal taps «أكلت كما في الخطة», Then the two Entries appear marked «قيد المزامنة» ("Pending"), the remaining figure reads 242 with "incl. 398 Pending", and the card reads «مؤكدة · قيد المزامنة» ("Confirmed · Pending").
- `/r` Given the network returns, Then the command is accepted once, the Entries become Confirmed (the "Pending" mark disappears), and `GET /v1/reports/day` shows one meal of 397.6 kcal.
- `/r` Given the same Plan was Confirmed on another device while this phone was offline, When the phone reconnects, Then the server returns 409 `STALE_REVISION` with the current state, the phone shows «تم تأكيدها من جهاز آخر — لم يُضف شيء» ("Already confirmed on another device — nothing added"), and its Pending Entries are dropped from the outbox without ever being Confirmed.

#### eater-5.38 · A Unit changed after I planned
As the Eater, I recalibrate a Unit between planning and eating, so that confirming still logs exactly the numbers I was shown. · Trace: FR-014, FR-031, FRD §17 MealPlan (`selected_versions`); E15, E21; see §7, item 4
- `/r` Given a Plan Saved with kabsa rice spoon version 1 (25 g) and Faisal saves version 2 (28 g) before confirming, When the Plan card opens, Then it notes «تغيّرت ملعقة الرز بعد هذه الخطة (25 غ ← 28 غ)» ("Your rice spoon changed after this plan").
- `/r` When he taps «أكلت كما في الخطة», Then the Entries use version 1's values as shown on the Plan (`unit_version_id` = version 1), and "Change amounts" offers version 2 for new counts.

### G · When things fail

#### eater-5.39 · Planning with no network
As the Eater, I can prepare a plan offline and keep logging, so that a dead spot never loses my list or my meal. · Trace: FRD §8.3, §7.2, §15.1 (planner in the backend), NFR-06; E38; EX-21, EX-25
- `/r` Given airplane mode, When Meal planner opens, Then chips and limits can be edited, "Find counts" is disabled with the reason «يحتاج اتصالًا» ("Needs a connection"), and logging a recent Unit on Today still works.
- `/r` Given a table photo taken offline with "Plan a meal" chosen, Then its Analysis stays Pending in Capture & Plan; when the network returns it moves to Processing and then to planning, never to an Entry (FRD §7.2).
- `/r` Given a half-built list and limits, When the app is closed and reopened, Then Meal planner shows them unchanged.

#### eater-5.40 · AI off or my AI limit reached: planning from a list still works
As the Eater, I plan from my Units when photo reading is paused or my daily limit is used, so that the planner, which needs no AI, always works. · Trace: FRD §7.2, §16.4 (kill switch), §16.5, §18.2 (`AI_UNAVAILABLE`, `RATE_LIMITED`), AT-32, NFR-05; EX-22 · **Shared: Eater · Platform admin** (admin-10.30, admin-10.36)
- `/r` Given the Kill switch for meal photos is On, When Faisal takes a table photo and taps «خطّط وجبة», Then `POST /v1/analyses` returns `AI_UNAVAILABLE` and one line says «قراءة الصور متوقفة الآن. تقدر تخطّط من وحداتك.» ("Photo reading is paused. You can still plan from your Units.") with "Choose foods", and from that list "Find counts" works.
- `/r` Given his daily AI limit is reached, When `POST /v1/analyses` is called, Then it returns 429 `RATE_LIMITED` with `resets_at`, Meal planner shows that reset time in his local time, and `POST /v1/meal-plans` with Units is accepted.
- `/r` Given the Kill switch is On, When counts are found from Units, Then the Plan is Proposed with the full limits list and no generated explanation (5.19).

### H · Inclusion

#### eater-5.41 · The plan in Arabic, right to left, in my digits
As the Eater who reads Arabic, I read the plan right to left with my chosen digits, so that counts sit next to their food and numbers are never reversed. · Trace: FRD §14.1, map §0 line 5, §1.6 (numerals); E40, E41, EX-39
- `/r` Given Faisal in Arabic with Western digits, Then the rows on Meal planner read right to left («ملعقة رز كبسة · 4»), macro bars fill from the right, back points right, and 397.6 keeps its digit order.
- `/r` Given Mona with Arabic-Indic digits, Then counts, totals, steppers and shares use them («٦», «٨١٠ سعرة», «٣٤٫١ ٪»).
- `/r` Given an English Unit name inside an Arabic row ("cheese bite"), Then the count stays beside its food in both directions (the name is direction-isolated).

#### eater-5.42 · One thumb, large text and VoiceOver
As the Eater at the table, I use the planner with one thumb, at large text sizes, or by VoiceOver, so that it works with bread in my other hand and for every reader. · Trace: FRD §14.2, NFR-08; E34, E35, E36; EX-33, EX-36, EX-37, EX-38
- `/r` Given the smallest simulator (iPhone 17e), Then each count stepper and "Ate as planned" measure at least 44 × 44 pt and sit in the middle and lower band of the screen, with the tabs at the bottom edge.
- `/r` Given the largest accessibility text size in Arabic and in English, Then Meal planner and Meal review rows wrap with no clipping or overlap, and the limits list stays readable.
- `/r` Given VoiceOver, Then a row reads "Kabsa rice spoon, 4, 170 calories, recipe-calculated", reads "5" after +, and "Infeasible" is announced together with its blocking limit.

#### eater-5.43 · Hide numbers: a plan in counts only
As the Eater who hides numbers, I get counts and the Plan's state in words, so that I can plan without seeing calories. · Trace: map §1.6 ("hide numbers" view), FRD §11.4, §14.2; R37; EX-43; see §7, item 10
- `/r` Given Hide numbers is on, When counts are found, Then Meal planner shows food names, counts and "Proposed — fits all your limits" or "Infeasible — no plan fits these limits", with no kcal, macro grams or percentages.
- `/r` Given Hide numbers is on, Then the numeric limit fields are hidden, and the planner uses what is left of the approved Target as its hidden Calorie target (`POST /v1/meal-plans` shows `calorie_target_source: "remaining"`).

---

## 3 · Journey 7 — Activity

| step | what the eater does | stories |
|---|---|---|
| A · connect Apple Health | asked type by type when first needed; decline; "no data", never "denied"; limited history | 7.1–7.3 |
| B · import | on open and when allowed, with last sync; what each record keeps; weight; edits and deletions in Health; nothing to the AI | 7.4–7.8 |
| C · count once | two feeds; daily active energy vs its workouts; a manual entry that matches | 7.9–7.11 |
| D · by hand | add; gross to active; correct and Void; invalid input | 7.12–7.15 |
| E · Activity mode and budget | Fixed by default; food unchanged; switch to Activity-adjusted; its arithmetic; what each number means; coverage | 7.16–7.21 |
| F · time | late workouts, Ramadan nights, travel | 7.22 |
| G · inclusion and control | Arabic, VoiceOver, Hide numbers; turning access off later | 7.23–7.24 |

### A · Connect Apple Health

#### eater-7.1 · Health access is asked when I first need it, type by type
As the Eater, I am asked for each Health type only when I first add Activity, so that I share only what I choose and food logging never waits for it. · Trace: FR-062 ("after granular permission"), FR-076, FRD §3.2; map row 1 ("each Health type"); P30, R7, R22; EX-03, EX-26
- `/r` Given Sam has never connected Health, When he taps "Connect Apple Health" on Today's Activity row or in Settings → Activity, Then a sheet lists "Workouts", "Active energy" and "Body mass (weight)", each with one sentence on why and its own switch, before the system Health sheet appears.
- `/r` Given he allows Workouts and Active energy only, Then two Consents are Given (each with purpose, version, time and method), and Settings → Activity shows "Body mass — not shared".
- `/r` Given no Consent for Workouts, When `POST /v1/activity/import` is sent with a workout, Then it returns `CONSENT_REQUIRED` and nothing is stored.
- `/r` Given he declines everything, Then Today, logging and Progress work as before, and the Activity row offers "Add Activity by hand".
- `/r` Given a new eater logs a first food Entry, Then no Health prompt appears at any point of that log.

#### eater-7.2 · "No data", never "denied"
As the Eater, I see "no data" when Health sends nothing, so that the app never says I refused or that I did not move. · Trace: FR-067 ("Permission denial or missing wearable data is 'unknown', not proof of no exercise"), P30, R7; EX-27 · **Shared: Eater · Support agent** (support-9.19)
- `/r` Given Health returns no workouts and no active energy for Sam's Day (read access off or simply no data; the app cannot tell which), Then Today's Activity row reads "No Activity data from Apple Health" with "Check Health access", never "Permission denied" and never "0 kcal".
- `/r` Given he taps "Check Health access", Then a sheet says "If access is off in the Health app, turn it on there" with a button that opens the Health settings.
- `/r` Given the same Day, When `GET /v1/reports/day` is called, Then `activity_coverage.state` is `no_data` and `active_energy_kcal` is null, not 0.

#### eater-7.3 · A limited history window shows where my data starts
As the Eater who shared only recent Health history, I see where that history begins, so that older Days read as unknown, not as rest days. · Trace: P30 ("only a limited window of recent data"), FR-067
- `/r` Given Health shares data from 2026-09-15 only, When Progress → 28 days (2026-09-04 – 2026-10-01) opens, Then Activity for 09-04 to 09-14 reads "Unknown — before Health sharing starts" and no bar is drawn for those Days.
- `/r` Given the same, When `GET /v1/reports/period` is called, Then those Days have `activity_coverage.state: "no_data"`.

### B · Import

#### eater-7.4 · Imports run when I open the app, and I can see when they last ran
As the Eater, I see when Activity last synced, so that I know how fresh the numbers are. · Trace: FR-067 ("Surface data coverage and last sync time"), FRD §12.3 ("Refresh when allowed and at app foreground; display freshness"), P30
- `/r` Given Sam opens the app at 07:30, When the import finishes, Then Today's Activity row reads "Synced 07:30", and Today never waited for the import before showing.
- `/r` Given no import has succeeded for 26 hours, Then the row reads "Last synced yesterday 05:12" in quiet text, with no alert.
- `/r` Given an import, When `POST /v1/activity/import` returns, Then the body has `accepted`, `updated`, `duplicate` and `conflict` counts, and `GET /v1/reports/day` returns the same `last_sync_at`.
- `/s` Given background delivery is enabled for active energy (entitlement on), Then the app registers it at hourly frequency, the most HealthKit allows for that type (P30), and no screen string in the catalogue calls Activity data "live".

#### eater-7.5 · Each imported Activity keeps where it came from
As the Eater, I can see where each Activity came from, so that I can trust or question it. · Trace: FR-063
- `/r` Given Sam's imported Watch walk, When the Activity sheet opens from Today, Then it shows "Outdoor walk · 07:00–07:45 · 210 kcal active · Apple Watch via Apple Health".
- `/r` Given the same, When `GET /v1/activity?diary_day_id=…` (*proposed*) is called, Then the record holds `provider_record_id`, `origin`, `start`, `end`, `type`, `energy_basis: "active"`, `import_revision: 1` and `override: null`.

#### eater-7.6 · Weight from Health becomes my weight observations
As the Eater, I let Health bring in my weight, so that Progress shows it without typing. · Trace: FR-062 ("body-mass data"), FRD §17 WeightObservation, FR-072
- `/r` Given Body mass is shared and Health holds 81.3 kg at 2026-10-01 06:50, When Progress → Weight opens, Then 81.3 kg shows with the label "Apple Health"; with display unit lb it shows 179.2 lb.
- `/r` Given the same, When `GET /v1/reports/period` is called, Then the weight observation has `source: "healthkit"`, a UTC time and the capture zone.

#### eater-7.7 · Changed or deleted in Health: changed or removed here, once
As the Eater, I fix a workout in Health and the app follows, so that I never see the old and the new one together. · Trace: FR-063 (import revision), FR-064, FR-065
- `/r` Given Sam's Watch walk is edited in Health from 210 to 220 kcal, When the next import runs, Then the Activity sheet shows one walk of 220 kcal, and the import result reads `updated: 1`.
- `/r` Given the walk is deleted in Health, When the next import runs, Then it disappears from Today, and any Activity credit it gave is removed (7.19). Detecting deletions through HealthKit's change queries is `assumption`.

#### eater-7.8 · My Health data never goes to the AI
As the Eater, I can trust that my workouts and weight stay with Sips & Bytes, so that sharing Health never sends it to Google's AI. · Trace: map row "Eater → AI analyzer" ("Health data never sent"), R7, R1, FRD §16.3 step 2
- `/s` Given imported Activity and weight observations exist, When any `POST /v1/analyses` runs, Then the request captured by the model-adapter mock contains no Activity, active-energy or weight field.
- `/r` Given Settings → Activity, Then it reads "Health data stays in Sips & Bytes. It is not sent to the AI."

### C · Count each Activity once

#### eater-7.9 · One workout from two feeds counts once (AT-22)
As the Eater whose watch and running app both record the walk, I see one walk, so that the same effort is never counted twice. · Trace: AT-22, FR-064, FR-063; map WF-7 done-when ("one workout from Health + a matching manual entry → one contribution") · **Shared: Eater · Support agent** (support-9.19)
- `/r` Given Faisal's import at 2026-10-01 07:30 Asia/Riyadh holds the Watch walk 07:00–07:45 (210 kcal), the running app's copy 07:01–07:44 (205 kcal) and the Watch walk sent a second time, When the import runs, Then Today shows one walk of 210 kcal, and the import result is accepted 1, duplicate 2, conflict 0.
- `/r` Given that walk, When the Activity sheet opens, Then it reads «سُجّل أيضًا من: تطبيق الجري · محسوب مرة واحدة» ("Also recorded by: running app · counted once").
- `/m` Given two records of the same type whose intervals overlap, When deduplicated, Then one record is kept, the device-recorded one first. The overlap threshold and the source order are `assumption` (§8).

#### eater-7.10 · Daily active energy and its workouts are not added together
As the Eater, I see my day's active energy with the walk inside it, so that the walk is not added on top. · Trace: FR-064 ("Never sum a provider's active-energy daily aggregate and its included workouts"), FR-066
- `/r` Given Sam's Health active energy for the Day is 520 kcal including the 210 kcal walk, When the Activity sheet opens, Then it reads "Active energy 520 kcal" with the walk listed inside it, and 730 appears nowhere.
- `/m` Given a Day with a provider aggregate, When Activity is totalled, Then the total is the aggregate, and a workout adds only for time the aggregate does not cover.

#### eater-7.11 · A manual entry that matches an imported workout asks to link (AT-22)
As the Eater, I am asked whether my typed walk is the one Health already has, so that I never create a second walk by accident. · Trace: FR-065, AT-22
- `/r` Given the imported walk 07:00–07:45, When Faisal adds by hand «مشي 45 دقيقة · 200 سعرة» starting 07:00, Then a prompt asks «هل هو نفس المشي 7:00–7:45 من Apple Health؟» ("Is this the same walk as 7:00–7:45 from Apple Health?") with «اربطه» ("Link"), «استخدم أرقامي» ("Use my numbers") and «نشاط مختلف» ("Different activity").
- `/r` Given he taps «اربطه», Then Today shows one walk of 210 kcal; given «استخدم أرقامي», Then one walk of 200 kcal labelled «عدّلتها أنت» ("Edited by you").
- `/r` Given the manual request, When `POST /v1/activity` (*proposed*) returns, Then it reports `possible_duplicate` with the matching Activity id, and after linking `GET /v1/activity` lists one Activity for that time.

### D · By hand

#### eater-7.12 · Add an Activity by hand
As the Eater without a watch, I type my exercise, so that it shows beside my food. · Trace: FR-062 ("offer manual exercise entry and correction"), FR-063 (user override), FRD §8.3
- `/r` Given Mona has not connected Health, When she adds «مشاية ٣٠ دقيقة · ١٧٥ سعرة نشاط» ("treadmill 30 min, 175 kcal active") at 18:00, Then the Activity appears in Today's timeline at 18:00, and the food total is unchanged.
- `/r` Given airplane mode, When she adds it, Then it shows «قيد المزامنة» ("Pending") and becomes Confirmed once when the network returns.
- `/r` Given `POST /v1/activity` is sent three times with the same idempotency key, Then `GET /v1/activity` lists one Activity.
- `/r` Given −50 kcal sent to `POST /v1/activity`, Then it returns `VALIDATION_ERROR` naming `energy_kcal`.

#### eater-7.13 · Gross energy from a machine is converted before it counts
As the Eater, I enter the number on the treadmill, so that its resting part is taken out before it can raise my Target. · Trace: FRD §12.2 ("Manual activities reporting gross energy require conversion or confirmation before receiving net-exercise credit"), FR-063 (energy basis)
- `/r` Given Sam in Activity-adjusted mode with resting estimate 1,779 kcal/day, When he enters "Treadmill · 30 min · 175 kcal · total shown on the machine", Then the sheet reads "Counted as 138 kcal active (175 − 37 resting for 30 min)" with "Confirm", and the credit becomes 69 kcal (50 %).
- `/m` Given 175 kcal gross over 30 min and 1,779 kcal/day resting, Then active = 175 − 1,779 × 30 / 1,440 = 137.9375.
- `/r` Given he chooses "Not sure", Then the Activity is saved and shown with "No credit until the energy type is confirmed", and the Target today is unchanged.

#### eater-7.14 · Correct or Void an Activity without making a second one
As the Eater, I fix or remove an Activity, so that it is changed in place and never doubled. · Trace: FR-065 ("Corrections shall not create a second workout"), FR-041, FR-046
- `/r` Given Mona's treadmill Activity of 30 min, When she edits it to 40 min, Then one Activity shows 40 min, and its history reads «٣٠ ← ٤٠ دقيقة».
- `/r` Given she Voids it, Then it leaves Today with an Undo banner; Undo Restores it.
- `/r` Given `POST /v1/activity/{id}/void` (*proposed*) is retried, Then the Activity is removed once and `GET /v1/activity` matches Today.

#### eater-7.15 · Wrong Activity input is caught beside the field
As the Eater, I am told at once when a time or a number cannot be right, so that a slip never becomes data. · Trace: FR-062, FRD §18.2; care.md group 4; EX-23
- `/r` Given an end before the start, 0 minutes or −50 kcal, Then a message sits beside that field (for example "End is before start") and "Save" is disabled.
- `/r` Given 1,200 kcal for 20 minutes, Then "That's high for 20 minutes — check the number" offers "Keep" and "Edit", and does not block (the threshold is `assumption`).
- `/r` Given a start time later than now, Then "This starts in the future" appears beside the time.

### E · Activity mode and the food budget

#### eater-7.16 · Fixed mode by default: my food Target does not grow (AT-23)
As the Eater, I keep a fixed food Target that a workout does not raise, so that exercise is never "forced into" my calories. · Trace: FR-007 (mode "visible on the dashboard"), FRD §12.1, AT-23, FR-066; map WF-7 done-when ("in fixed mode the food target does not grow"); E18; EX-14
- `/r` Given Sam's Target 1,870 already includes 200 kcal planned exercise (FRD §11.3), Fixed mode and 1,200 kcal consumed, When a 200 kcal workout imports, Then Today still reads Target 1,870 and remaining 670, and the Activity row reads "200 kcal active · not added to your food Target (Fixed)".
- `/r` Given Today, Then the mode is shown as "Food Target: Fixed".
- `/r` Given the same, When `GET /v1/reports/day` is called before and after the import, Then `target_kcal` is 1,870 and `remaining_kcal` is 670 both times.

#### eater-7.17 · Exercise never changes what I ate (AT-24)
As the Eater, I add exercise and my food numbers stay as they were, so that what I ate is never edited by what I did. · Trace: FR-068, AT-24
- `/r` Given Sam's Day of 1,200 kcal, P 72 g, C 138 g, F 40 g, When "Cardio 175 kcal active" is added, Then the Day report still shows 1,200 kcal, P 72, C 138, F 40 and shares 24.0 / 46.0 / 30.0 %.
- `/r` Given Activity-adjusted mode, When the same Activity is added, Then only "Target today" and "Remaining" change; food kcal and macro grams in `GET /v1/reports/day` are identical.
- `/m` Given the Day projection, When an Activity event is applied, Then no food field of the projection changes.

#### eater-7.18 · Switching to Activity-adjusted: a base, a credit factor and a cap that I approve
As the Eater, I choose Activity-adjusted and approve its base, credit factor and cap, so that the budget grows only by rules I saw. · Trace: FRD §12.2, FR-007, FR-058 (effective-dated versions), FR-071; map §1.6 (activity mode)
- `/r` Given Sam in Fixed mode with Target 1,870 (all-in: 2,334.8 maintenance including 200 kcal exercise, −20 %), When he opens Settings → Activity → Activity mode and picks "Activity-adjusted", Then a preview shows "Base food Target 1,710 (maintenance without exercise 2,134.8, −20 %)", "Credit 50 % of eligible Activity" and "Cap 300 kcal a day", each editable, with "Approve".
- `/r` Given he taps "Approve", Then Today reads "Food Target: Activity-adjusted", and the Day report for 2026-09-30 still shows Target 1,870.
- `/r` Given he taps "Cancel" instead, Then nothing changes and Today reads "Food Target: Fixed".
- `/s` Given the approval, Then a new Target version stores the mode, base, credit factor, cap and effective date, and no calculation applies the all-in multiplier and Activity credit together (FRD §12.2).

#### eater-7.19 · The Activity-adjusted budget, step by step
As the Eater in Activity-adjusted mode, I see how a workout changes today's Target, so that every added calorie has a visible reason. · Trace: FRD §12.2 (`target_today = base_food_target + approved_activity_credit`; `remaining = target_today − consumed_source_energy`), FR-066, FR-064
- `/r` Given base 1,710, credit 50 %, cap 300 and 1,200 kcal consumed, When a 400 kcal workout imports, Then Today reads "Target today 1,910 (1,710 + 200 Activity credit)" and remaining 710.
- `/r` Given an 800 kcal workout instead, Then the credit is 300 with "Cap reached", Target today 2,010 and remaining 810.
- `/r` Given the 400 kcal workout arrives from two feeds, Then the credit is 200 once.
- `/r` Given Health has no data for the Day, Then the credit is 0, Target today 1,710, and the row reads "No Activity data — no credit".

#### eater-7.20 · What each number means
As the Eater, I can see food, active energy, total expenditure and the net figure side by side, so that I never take "food minus exercise" for my deficit. · Trace: FR-066 ("Explain that net intake is not maintenance or actual deficit"), FRD §11.3
- `/r` Given Sam's 1,200 kcal consumed, 520 kcal active energy and resting estimate 1,779, When the Activity sheet opens, Then it lists "Food 1,200", "Active energy 520", "Estimated total expenditure about 2,299 (resting estimate + active energy)", "Food minus active 680 — not your maintenance or your actual deficit" and "Remaining food Target 670 (Fixed)".
- `/r` Given Today, Then only the remaining figure and the Activity row show, and the full list is one tap away (EX-01, EX-11).
- `/r` Given `GET /v1/reports/day`, Then it returns `food_kcal`, `active_energy_kcal`, `estimated_total_expenditure_kcal`, `food_minus_active_kcal` and `remaining_kcal` as separate fields. The total-expenditure formula is `assumption` (§8).

#### eater-7.21 · Activity coverage and last sync, Day by Day
As the Eater, I can see for each Day where its Activity came from and how fresh it is, so that a missing day reads as unknown. · Trace: FR-067
- `/r` Given a Day with Health data, When the Activity sheet opens, Then it reads "Apple Health · data through 07:28 · synced 07:30"; a Day with only manual Activity reads "Entered by you · Apple Health not connected".
- `/r` Given `GET /v1/reports/day`, Then `activity_coverage` holds `source`, `last_sync_at` and `state` (`data`, `no_data` or `not_connected`).

### F · Time

#### eater-7.22 · A late workout lands on the right Day
As the Eater who walks after taraweeh or late at night, I see the walk on the Day I am living, so that the night is not split in two. · Trace: FRD §8.1 (diary-day boundary; travel must not duplicate or lose), FR-044; E9, E11, E20; EX-20
- `/r` Given Faisal's Ramadan boundary 05:00, When a walk from 23:30 to 00:10 (clock dates 2027-02-10 to 02-11) imports, Then it lands on Day 2027-02-10 with that Day's iftar and suhoor.
- `/r` Given a walk from 04:30 to 05:20, which crosses the boundary, Then it belongs to the Day in which it started (`assumption`; §7, item 7).
- `/r` Given Sam's workout recorded in Europe/London and his phone then set to Asia/Riyadh, When the next import runs, Then the workout keeps its UTC time and zone, and there is no second copy.

### G · Inclusion and control

#### eater-7.23 · Activity in Arabic, by VoiceOver, and with numbers hidden
As the Eater, I read Activity in Arabic, by VoiceOver or with numbers hidden, so that it works in the way I read. · Trace: FRD §14.1, §14.2, NFR-08; map §1.6; E41; EX-36, EX-39, EX-43
- `/r` Given Mona in Arabic with Arabic-Indic digits and her treadmill Activity (7.12), When the Activity sheet opens, Then it lays out right to left with «مشاية · ٣٠ دقيقة · ١٧٥ سعرة نشاط · ١٨:٠٠».
- `/r` Given VoiceOver on Sam's Today, Then the Activity row reads "Outdoor walk, 45 minutes, 210 calories active, counted once, not added to your food Target".
- `/r` Given Hide numbers is on, Then Activity rows show type and duration only, and an Activity credit is described as "Activity added to today's Target", with no number.

#### eater-7.24 · Turning Health access off later
As the Eater, I can stop sharing a Health type at any time, so that the app stops importing it and everything else keeps working. · Trace: FR-076 ("Refusal must preserve unaffected functions"), FR-067, P30, R3, R31 (withdrawing as easy as giving); WF-9
- `/r` Given Sam withdraws the Workouts Consent in Settings → Activity (the Consent becomes Withdrawn), Then workout imports stop at once, the Activity row reads "Workouts not shared", and food logging is unchanged. Whether earlier imported Activity is kept or deleted is open (§7, item 8).
- `/r` Given he turns access off in the Health app instead, Then the app shows "No new data from Apple Health since 07:30", never "denied" (it cannot tell, P30).

---

## 4 · Journey 8 — Reports and progress

| step | what the eater does | stories |
|---|---|---|
| A · after a meal, and the Day | meal report; Day report; shares to 100.0 %; label vs 4/4/9; unknown macros; over Target; Pending; reconcile; late correction | 8.1–8.9 |
| B · periods | 7 / 28 / custom; Target per Day; missing Days; marking a Day Complete; provisional today; intake vs Target; week start; wrong dates; Ramadan | 8.10–8.18 |
| C · weight and evidence | weight with source; manual and unusual weights; insufficient evidence (P1); no data | 8.19–8.22 |
| D · Target history | versions and assumptions | 8.23 |
| E · export | a period report as a file; offline and failure | 8.24–8.25 |
| F · empty, offline, slow, inclusion, modes | first use; offline and slow; Arabic and VoiceOver; Hide numbers; tracking-only | 8.26–8.30 |

### A · After a meal, and the Day

#### eater-8.1 · A meal report after every confirmed meal
As the Eater, I see what the meal added right after I confirm it, so that I know what that plate did to my Day. · Trace: FR-069, FRD §13.1, §10.1; map row "Ledger → Eater"; E19; EX-13
- `/r` Given Sam's Day before the meal is 720 kcal (P 30, C 105, F 20 g), When he confirms a meal of 480 kcal (P 42, C 33, F 20 g), Then the meal report on Today reads: Protein 42 g · 168 kcal · 35.0 %; Carbohydrate 33 g · 132 kcal · 27.5 %; Fat 20 g · 180 kcal · 37.5 %; and "Carbohydrate: within your 30 % maximum (share of macro-derived energy, 4/4/9)".
- `/r` Given the same, When `POST /v1/consumption` returns, Then the response holds meal totals 480 kcal and Day totals 1,200 kcal with the same grams.

#### eater-8.2 · The Day report: Target, consumed, remaining, macros, Evidence and coverage
As the Eater, I read the whole Day in one place, so that I know where I stand and how sure the numbers are. · Trace: FR-069, FR-070, FRD §13.1, §14 Today; map WF-8 done-when ("target, consumed, remaining, shares summing to 100.0 %, coverage")
- `/r` Given the Day after 8.1, When Day report opens from Today's remaining figure, Then it reads "Daily total 1,200 kcal · Target 1,870 · Remaining 670"; Protein 72 g · 288 kcal · 24.0 %; Carbohydrate 138 g · 552 kcal · 46.0 %; Fat 40 g · 360 kcal · 30.0 %; "Carbohydrate: above your 30 % target"; and "Macros known for all 1,200 kcal".
- `/r` Given the Day includes 2 hummus bites (Estimated analogue, low 45.0 – high 75.0 kcal each), Then each Entry shows its Evidence badge and the total shows "low 1,173 – high 1,233 (estimate)"; a Day with no estimated Entry shows no range.
- `/r` Given the same Day, When `GET /v1/reports/day` is called, Then it returns the same totals, shares, range, `revision` and target version.

#### eater-8.3 · Shares always add to 100.0 %, on one convention
As the Eater, I see shares that add up, so that a chart that says 99.9 % never makes me doubt the rest. · Trace: FRD §10.1, §10.2 (largest-remainder display; zero energy → "not applicable"); map WF-8 done-when
- `/m` Given P 20 g, C 25 g, F 10 g (80 / 100 / 90 kcal), When display shares are computed, Then they are 29.6 / 37.1 / 33.3 % (sum 100.0), while rounding each value alone would give 99.9; stored values stay unrounded.
- `/r` Given a Day with only black coffee and water (0 kcal), When Day report opens, Then shares read "Not applicable" and no macro chart is drawn.
- `/r` Given any Day report or meal report, Then the share heading reads "Share of macro-derived energy (4/4/9)".

#### eater-8.4 · When a label's calories differ from 4/4/9 (AT-15)
As the Eater, I keep the label's calories as the headline and see why the macro shares differ, so that I trust both. · Trace: AT-15, FR-030, FR-069, FRD §10.1, §10.2 (mismatch trigger); E15, E16
- `/r` Given a Label-verified snack Entry of 300 kcal with P 10, C 30, F 8 g (232 kcal by 4/4/9), When the meal report shows, Then the headline is 300 kcal, the shares read 17.3 / 51.7 / 31.0 % (sum 100.0) under "4/4/9", and a note reads "Label energy 300 kcal; the macros give 232 kcal by 4/4/9. Labels can use other energy factors."
- `/m` Given 300 vs 232 (68 kcal, 22.7 % of the source energy), Then the gap is material (> 10 % and > 10 kcal) and the note is shown; given 250 vs 232 (18 kcal, 7.2 %), Then no note. Measuring the percentage against the source energy is `assumption`.
- `/r` Given the same Entry, When `GET /v1/reports/day` is called, Then `source_kcal` is 300 and `macro_kcal` is 232; the label value is never changed.

#### eater-8.5 · Unknown macros stay unknown (AT-16)
As the Eater who logs a calorie-only food, I see that the Day's macros are incomplete, so that no grams are invented. · Trace: AT-16, FR-016, FR-070, FRD §10.2 ("Missing macros: mark unknown; display coverage")
- `/r` Given Sam's Day of 1,200 kcal with full macros, When he adds "office cake 150 kcal" (User-defined, calorie only), Then the Day reads 1,350 kcal, coverage reads "Macros known for 1,200 of 1,350 kcal", shares are labelled "of the 1,200 kcal with known macros", and the cake row shows no grams.
- `/r` Given the same, When `GET /v1/reports/day` is called, Then `macro_coverage` is `partial` and the cake's P/C/F are null, not 0.

#### eater-8.6 · Over my Target is a number, not a verdict
As the Eater, I see an over-Target Day as a plain number, so that the report informs without judging. · Trace: FRD §14.2 ("Avoid punitive red warnings for ordinary eating"), §11.4, FR-070; EX-35, EX-42
- `/r` Given Sam's Day of 2,000 kcal against 1,870, When Day report opens, Then it reads "Over by 130" in the same style as "Remaining" (no red fill, no warning icon), with words, not colour alone.
- `/r` Given Today, Day report and Progress in English and Arabic, Then none of the catalogue words for "bad", "cheat", "failed", "skip" or "make up for" appears.

#### eater-8.7 · Pending Entries in the Day
As the Eater offline, I see what is not synced yet, so that I know which part of the total is still Pending. · Trace: FRD §8.3 ("The client distinguishes pending from confirmed totals"), FR-070; EX-12; research.md §6, conflict 7
- `/r` Given Sam's Day of 1,200 kcal confirmed and a cheese bite (47.4 kcal) logged in airplane mode, When Today shows, Then it reads "1,247 eaten · 47 Pending" and "Remaining 623", and the cheese bite row is marked "Pending".
- `/r` Given the network returns, Then the cheese bite becomes Confirmed, "Pending" disappears, and `GET /v1/reports/day` returns 1,247.4 kcal.

#### eater-8.8 · Reports always reconcile with my Entries
As the Eater, I can add up the Entries I see and get the Day total, so that no number moves without a visible cause. · Trace: FR-070 ("Arithmetic must reconcile with the effective ledger"), FR-042, NFR-01, FRD §17.2; E15, E16, E19; EX-14 · **Shared: Eater · Platform admin** (admin-10.26) **· Nutrition approver** (approver-10.28, approver-10.58)
- `/r` Given any Day, When Day report opens, Then the Day total equals the sum of the Entries listed on Today and in the Day report, to the displayed precision.
- `/s` Given the Day's events replayed from scratch, Then the rebuilt projection equals the stored one, with zero discrepancy.
- `/r` Given a Registry version Rolled back (admin) or a newer Food version Approved, with the old one Superseded (approver), after the Day, When `GET /v1/reports/day` is called for that Day, Then totals and `revision` are unchanged.

#### eater-8.9 · A late correction changes its own Day, not today
As the Eater, I correct yesterday and only yesterday moves, so that today's report stays true. · Trace: FR-047, AT-14, FR-044; WF-6
- `/r` Given Mona corrects lunch on 2026-09-30 while on 2026-10-01, Then the Day report for 2026-09-30 and the 7-day view change, and today's Day report is unchanged.
- `/r` Given the correction, When the API response is read, Then the affected Day projections list only 2026-09-30.

### B · Periods

#### eater-8.10 · 7 days, 28 days, or my own dates
As the Eater, I look back over a week, four weeks or dates I pick, so that I see a pattern, not just a Day. · Trace: FR-072, FRD §14 Progress, §18 (`GET /v1/reports/period`); map WF-8
- `/r` Given Mona opens Progress, Then a control reads «٧ أيام · ٢٨ يومًا · مخصص» ("7 days · 28 days · Custom"), and the view has sections for intake per Day, intake vs Target, macro composition, Activity and Weight.
- `/r` Given `GET /v1/reports/period?from=2026-09-20&to=2026-09-26`, Then it returns 7 Day rows, each with target version and value, consumed, coverage state, Activity coverage and weights.
- `/r` Given "7 days" is chosen on 2026-10-01, Then the view covers the 7 Days ending on the selected Day, 2026-09-25 to 2026-10-01 (rolling; §7, item 11).

#### eater-8.11 · Each Day keeps the Target that applied that Day
As the Eater, I change my Target today and last month still reads as it was, so that the past is never rewritten. · Trace: FR-071, FR-058, FRD §17 GoalPlanVersion ("Past days retain their effective plan") · **Shared: Eater · Nutrition approver** (approver-10.58)
- `/r` Given Mona's Target was 1,870 until 2026-09-14 and 1,750 from 2026-09-15, When the 28-day view (2026-09-04 – 2026-10-01) opens, Then 09-04 to 09-14 show 1,870 and 09-15 onward show 1,750.
- `/r` Given she changes her Target to 1,700 on 2026-10-01, Then only 10-01 shows 1,700, 09-15 to 09-30 still show 1,750, and the API rows match the screen.
- `/s` Given a Policy version with a higher floor comes In effect after those Days (approver-10.58), When their projections are rebuilt, Then their target values are unchanged.

#### eater-8.12 · Missing Days are unknown, not zero (AT-25)
As the Eater who forgot two Days, I see them as unlogged, so that a gap is never shown as a low-calorie success. · Trace: FR-073, AT-25; map WF-8 done-when ("a week with 2 missing days shows coverage, not zeros"); E24; EX-27
- `/r` Given Mona's week 2026-09-20 to 09-26 with «١٬٦٥٠ · ١٬٧٢٠ · — · ١٬٥٨٠ · ١٬٩٠٠ · — · ١٬٦١٠» and Target 1,750, When the 7-day view for that week opens, Then it reads «٥ من ٧ أيام مسجلة» ("5 of 7 Days logged"), the two Days are marked «غير مسجل» ("Unlogged") with a pattern and a word, the average reads «١٬٦٩٢» over logged Days, and intake vs Target reads «−٢٩٠ على ٥ أيام مسجلة», not −3,790.
- `/r` Given the same period from `GET /v1/reports/period`, Then `days_logged` is 5, `days_unlogged` is 2, and `consumed_kcal` is null for the two Unlogged Days.
- `/m` Given the period projection, When averages and variance are computed, Then Unlogged Days are left out of both.

#### eater-8.13 · Complete, Partial, Unlogged — and marking a Day Complete
As the Eater, I mark a Day complete when I logged everything, so that reports can tell a full Day from a partial one. · Trace: FR-073, FR-060 ("self-marked complete diary days")
- `/r` Given Mona's Day report for 2026-09-29, When she taps «اليوم كامل» ("Mark Day complete"), Then a «كامل» ("Complete") badge appears with Undo, and the 28-day view shows that Day as Complete.
- `/r` Given Days with Entries she has not marked Complete, Then they read «جزئي» ("Partial"), and tapping «اليوم كامل» again on a Complete Day returns it to Partial; Days without Entries read «غير مسجل» ("Unlogged").
- `/r` Given `POST /v1/days/2026-09-29/complete` (*proposed*), Then `GET /v1/reports/period` returns `coverage: "complete"` for that Day; undoing returns `partial`.

#### eater-8.14 · Today is provisional
As the Eater, I see today marked as not finished, so that a half-eaten Day does not pull my week down. · Trace: FR-074 ("Current incomplete days must be labeled provisional")
- `/r` Given the 7-day view on 2026-10-01 with 900 kcal logged today, Then today's bar reads «مؤقت» ("Provisional") and is left out of the period average and intake vs Target until the Day ends or is marked Complete.
- `/r` Given `GET /v1/reports/period` including today, Then today's row has `provisional: true`.

#### eater-8.15 · Intake vs Target, never "fat lost"
As the Eater, I see how my intake compared with my Target, so that I get a fact, not a promise about my body. · Trace: FR-074 ("not an unsupported claim of fat gained/lost"), FR-059, FRD §1.3, §19.3; R6
- `/r` Given the week in 8.12, Then the summary reads «الأكل مقابل الهدف: −٢٩٠ سعرة على ٥ أيام مسجلة» ("Intake vs Target: −290 kcal over 5 logged Days"), and no kilograms, "fat", "burned" or projected weight appear beside it.
- `/r` Given any period view, Then the comparison is labelled "vs Target", never "vs plan".

#### eater-8.16 · My week starts where my region's week starts
As the Eater, I see weeks that start on my region's first day, so that "this week" matches my working week. · Trace: FR-072, FRD §1.3 ("daily/weekly reports"); E14; EX-05; research.md §6, conflict 4
- `/r` Given the device calendar's first weekday is Saturday, When the 28-day view opens, Then its week separators fall before Saturdays; given Monday (Sam, United Kingdom), Then before Mondays.
- `/r` Given Settings → Units & language → first day of week changed to Sunday, Then the separators move to Sundays, and Day values are unchanged.
*Note:* the first weekday for Egypt and Saudi Arabia comes from the device calendar; the values per country are `assumption`.

#### eater-8.17 · Wrong custom dates are caught
As the Eater, I am told at once when my custom dates cannot work, so that I never get an empty or misleading report. · Trace: FR-072, FRD §18.2; care.md group 4; EX-23
- `/r` Given Custom with an end before the start, Then "End date is before start date" appears beside the end date and the view does not load.
- `/r` Given an end after today, Then the picker stops at today; given more than 366 days, Then "Choose up to 366 days" (the limit is `assumption`).
- `/r` Given `GET /v1/reports/period?from=2026-09-26&to=2026-09-20`, Then it returns `VALIDATION_ERROR` naming `to`.

#### eater-8.18 · Iftar and suhoor in one Day's report
As the Eater in Ramadan, I see iftar, the late meal and suhoor in one Day, so that each Ramadan Day reads as one Day. · Trace: FRD §8.1 (custom boundary); E11, E12, E13; EX-20
- `/r` Given Faisal's Ramadan boundary 05:00 and Entries at 18:02 (3 dates and 1 laban cup), 22:15 (kabsa) and 03:40 by the clock on 2027-02-11 (suhoor), When the Day report for 2027-02-10 opens, Then it lists all three meals, and the 7-day view has one bar for that Day.

### C · Weight and evidence

#### eater-8.19 · My weight over the period, with its source
As the Eater, I see my weight observations over the period with where each came from, so that I read a trend, not one scale reading. · Trace: FR-072 (weight trend), FRD §17 WeightObservation ("not a guaranteed body-fat measure"), FR-062
- `/r` Given weights 82.0 (2026-09-10, by hand), 81.7 (09-17, Apple Health), 81.9 (09-24, Apple Health) and 81.3 (10-01, Apple Health), When Progress → Weight for 28 days opens, Then the four points show with their source labels in kg (or lb by setting).
- `/r` Given the Weight section, Then no body-fat or "fat lost" figure appears.

#### eater-8.20 · Add my weight by hand; an unusual reading is flagged, not deleted
As the Eater, I type my weight, and a reading that looks wrong is flagged for me to decide, so that one bad reading never bends the trend silently. · Trace: FRD §17 WeightObservation (`outlier_state`), §14 Progress ("outlier weights"), §14.1 (both numeral systems)
- `/r` Given Mona types «٨١٫٣ كجم», When saved, Then it is stored as 81.3 kg and shown as «٨١٫٣».
- `/r` Given 88.0 kg on 2026-09-20 between 81.7 (09-17) and 81.9 (09-24), Then the point reads «غير معتاد — راجعيه» ("Unusual — check") with «احتفظي به» ("Keep") and «استبعده من الاتجاه» ("Exclude from trend"); it stays in the list either way, and while flagged it is left out of the trend line. The rule for "unusual" is `assumption`.
- `/r` Given 0, a negative value or 500 kg, Then the field shows the fix beside it and nothing is saved; the same value sent to `POST /v1/weights` (*proposed*) returns `VALIDATION_ERROR` naming `kg`.

#### eater-8.21 · A trend statement only with enough evidence (P1)
As the Eater, I get a trend statement only when there is enough data, so that a few readings never become a claim. · Trace: FR-060 (P1: "proposed minimum 14 days, 10 self-marked complete diary days, and 4 weight observations. Otherwise show 'insufficient evidence'"), FR-059; R42 (the adaptive approach; its refuted detail is not used) · **P1**
- `/r` Given a 28-day period with 9 Complete Days and 4 weights, When the Weight section opens, Then it reads "Insufficient evidence for a trend: 9 of 10 Complete Days needed · 4 of 4 weights · 28 of 14 days", and no trend number is shown.
- `/r` Given at least 14 days, 10 Complete Days and 4 weights, Then a trend statement appears worded as an estimate with its range (FR-059), never as a promise.

#### eater-8.22 · No weight data, or Health access changed
As the Eater with no weights yet, or whose Health access changed, I see what is missing and what to do, so that an empty chart never looks like a broken app. · Trace: FRD §14 Progress ("Insufficient data … permission changes"), FR-067; EX-19, EX-27
- `/r` Given Body mass is not shared and no weight was typed, When Progress → Weight opens, Then it reads "No weight yet" with "Add weight" and "Connect Apple Health", never "denied".
- `/r` Given Health weights stopped arriving after 2026-09-24, Then it reads "No new weight from Apple Health since 24 Sept" in quiet text.

### D · Target history

#### eater-8.23 · My Target history
As the Eater, I see each Target I approved, when it applied and why, so that I understand every Day's comparison. · Trace: FRD §14 Progress ("target history"), FR-058, FR-071, FR-004, FR-059; map WF-8
- `/r` Given Mona's versions, When Progress → Target history opens, Then it lists 1,870 (2026-08-20 – 09-14, Fixed, "Estimated: resting energy, activity, −20 %"), 1,750 (09-15 – 09-30) and 1,700 (from 10-01), each with its source (estimated, typed by you, or clinician-provided).
- `/r` Given she taps a version, Then its assumptions show, with the projection worded as a range and "not a promise" (FR-059).
- `/r` Given `GET /v1/targets` (*proposed*), Then each version has `effective_from`, `effective_to`, `activity_mode` and `source`.

### E · Export

#### eater-8.24 · Export a period report as a file
As the Eater, I export a period report in a file my coach or spreadsheet can read, so that my data is mine to take. · Trace: FR-075 ("export of entries, portions, recipes, targets, and reports in machine-readable form"), FRD §23.3 ("Export … must never be paywalled"); C53; research.md §6, conflict 8
- `/r` Given the week 2026-09-20 – 09-26, When Mona taps «صدّري الفترة» ("Export period") → CSV, Then the share sheet offers a CSV with one row per Day: `date` (ISO), `target_version`, `target_kcal`, `consumed_kcal` (empty for Unlogged), `protein_g`, `carbohydrate_g`, `fat_g`, `coverage` (complete, partial or unlogged), `provisional`, `active_energy_kcal` (empty when no data) and `weight_kg`, in Western digits whatever her display setting.
- `/r` Given JSON is chosen instead, Then the file holds the same values as the CSV.
- `/r` Given `GET /v1/reports/period?from=2026-09-20&to=2026-09-26&format=csv` (*proposed* parameter), Then it returns `text/csv` whose rows equal the screen's values.
- `/r` Given any account, Then "Export period" shows no upgrade prompt or lock.

#### eater-8.25 · Export with no network, or when it fails
As the Eater, I am told why an export cannot be made now, so that I never get half a file. · Trace: FR-075, FRD §18.2; care.md group 4; EX-21
- `/r` Given airplane mode, Then "Export period" is disabled with the reason "Needs a connection".
- `/r` Given the server returns 503, Then "Couldn't make the export. Try again." appears, and no partial file is offered.

### F · Empty, offline, slow, inclusion and modes

#### eater-8.26 · Progress before my first Entry
As the Eater with no Entries yet, I see what Progress will show and how to start, so that an empty screen tells me what to do next. · Trace: FRD §14 Progress ("Insufficient data"); EX-19
- `/r` Given an eater with no Entries, When Progress opens, Then it reads "Your reports start with your first Entry" with "Log what you ate", which opens quick-add, and no chart of zeros is drawn.

#### eater-8.27 · Progress offline or slow
As the Eater, I see my last reports with a note when offline, and placeholders when slow, so that I am never left with a blank screen. · Trace: FRD §8.3, NFR-06; care.md group 4; EX-21
- `/r` Given airplane mode, When Progress opens, Then the last loaded period shows with "Offline — as of 07:45", and Pending Entries are counted and marked.
- `/r` Given a slow network, When a 28-day view loads, Then bar-shaped placeholders show within the first second and are replaced by the data, never a blank screen.

#### eater-8.28 · Reports in Arabic, right to left, by VoiceOver and at large text
As the Eater who reads Arabic or uses VoiceOver, I read my reports in my way, so that charts run in my direction and every number is spoken. · Trace: FRD §14.1, §14.2, NFR-08; E40, E41; EX-33, EX-36, EX-39
- `/r` Given Mona in Arabic, When the 7-day view opens, Then Days run right to left with the earliest on the right, bars and macro shares fill from the right, and digits keep their order («١٬٦٥٠»).
- `/r` Given VoiceOver, Then the chart's summary reads "7 days, 5 logged, average 1,692 calories, 2 unlogged", and each Day bar can be read on its own.
- `/r` Given the largest accessibility text size, Then Day report rows wrap with no clipping in English and Arabic.

#### eater-8.29 · Reports with numbers hidden
As the Eater who hides numbers, I still see coverage and what I ate, so that Progress stays useful without calories. · Trace: map §1.6 ("hide numbers" view); R37; EX-43
- `/r` Given Hide numbers is on, When Progress opens, Then it shows Complete / Partial / Unlogged and food names with counts, and no kcal, grams, percentages or weight numbers.
- `/r` Given Hide numbers is on, When "Export period" is used, Then the file still holds the numbers (it is the eater's own data), and the export screen says so before the file is made.

#### eater-8.30 · Reports in tracking-only mode
As the Eater in tracking-only mode, I get every report except a Target comparison, so that tracking-only is not a lesser app. · Trace: FRD §11.4, §3.3; map §5 WF-1 done-when; EX-44
- `/r` Given an eater in tracking-only mode with no Target, When the Day report opens, Then it shows consumed kcal, macros and coverage, and no "Remaining", "Over by" or intake vs Target.
- `/r` Given the same eater, Then the 7-day view, the 28-day view and "Export period" work, and `GET /v1/reports/period` has `target_kcal: null` with `mode: "tracking_only"`.

---

## 5 · Stories shared with another persona

| story | shared with | that lens's story | what is shared |
|---|---|---|---|
| eater-5.11 | Nutrition approver | approver-10.55 | planner increments: whole by default, halves only when the eater enables them |
| eater-5.25 | Nutrition approver | approver-10.48, approver-10.50 | the Policy floor and the 1,000 kcal hard stop the planner respects |
| eater-5.27 | Nutrition approver | approver-10.53 | tracking-only triggers and what the planner withholds |
| eater-5.40 | Platform admin | admin-10.30, admin-10.36 | AI kill switch and the AI limit never block planning from Units |
| eater-7.2 | Support agent | support-9.19 | "no data" is unknown, not proof of no exercise |
| eater-7.9 | Support agent | support-9.19 | the AT-22 import (accepted 1, duplicate 2, conflict 0, 07:30 Asia/Riyadh) |
| eater-8.8 | Platform admin · Nutrition approver | admin-10.26 · approver-10.28, approver-10.58 | rollbacks and new Food versions never change a past Day |
| eater-8.11 | Nutrition approver | approver-10.58 | a new Policy never rewrites a past Day's Target |

---

## 6 · Proposed names for the model phase to confirm

FRD §18 names `POST /v1/meal-plans`, `POST /v1/consumption`, `POST /v1/consumption/{id}/corrections`, `POST /v1/consumption/{id}/void`, `GET /v1/reports/day`, `GET /v1/reports/period` and `POST /v1/activity/import`. `way/vocabulary.md` (D2) fixes the states, the error codes and the places. This file adds no state and no error code. Everything below is *proposed*. The two new places need a dated delta before they are built (D2: "A word not here … is added by a dated delta first").

| kind | proposed name | used in |
|---|---|---|
| API | `GET /v1/meal-plans/{id}` · `GET /v1/meal-plans?state=` · `POST /v1/meal-plans/{id}/save` · `/validate` · `/not-eaten` | 5.18, 5.20, 5.28, 5.34 |
| API fields | Plan `state` (D2 Plan states) with `confirmed_as` (`ate_as_planned` · `changed`); `solution_status` (FRD §17); `scope: "day"`; `blocking[]`, `changes[]`, `left_out[]`, `preference_report`, `calorie_target_source` | 5.3, 5.14–5.25, 5.29, 5.31, 5.43 |
| API | `POST /v1/activity` · `GET /v1/activity?diary_day_id=` · `POST /v1/activity/{id}/corrections` · `/void` · `/restore` · `/link` | 7.5, 7.11–7.14 |
| API | `POST /v1/days/{diary_day_id}/complete` · `POST /v1/weights` · `GET /v1/targets` · `format=csv|json` on `GET /v1/reports/period` | 8.13, 8.20, 8.23, 8.24 |
| report fields | `activity_coverage {source, last_sync_at, state}` · `food_minus_active_kcal` · `estimated_total_expenditure_kcal` · `macro_coverage` · `provisional` · `days_logged` / `days_unlogged` | 7.2, 7.20, 7.21, 8.5, 8.12, 8.14 |
| errors | D2 codes only: `VALIDATION_ERROR` (naming the field) · `PLAN_INFEASIBLE` · `POLICY_FLOOR` · `STALE_REVISION` · `CONSENT_REQUIRED` · `NOT_FOUND` · `AI_UNAVAILABLE` · `RATE_LIMITED` | 5.4, 5.9, 5.13, 5.21, 5.25, 5.27, 5.28, 5.30, 5.37, 5.40, 7.1, 7.12, 8.17, 8.20 |
| places needing a delta | the **Activity sheet** opened from Today's Activity row · the **saved Plans list** in Capture & Plan | 7.x; 5.20 |
| places from the map, used as sections | the **Day report** opened from Today's remaining figure ("meal and day report", map §1.3) · Progress → **Weight** and **Target history** ("weight trend; target history", map §1.4 WF-8) | 8.x |
| labels for the string catalogue (EN / AR, one Arabic label per English word) | actions: Plan a meal / خطّط وجبة · Log what I ate / سجّل ما أكلت · Find counts / احسب الكميات · Save plan / احفظ الخطة · Ate as planned / أكلت كما في الخطة · Change amounts / غيّر الكميات · Not eaten / لم تُؤكل · Save consumed / احفظ ما أكلت · Log leftovers / سجّل الباقي · Mark Day complete / اليوم كامل — states: Proposed / مقترحة · Infeasible / غير ممكنة · Saved / محفوظة · Confirmed / مؤكدة · Changed / بتغيير · Pending / قيد المزامنة · Complete / كامل · Partial / جزئي · Unlogged / غير مسجل · Provisional / مؤقت | WF-5, WF-7, WF-8 |

---

## 7 · Conflicts for the model phase

Never for the owner. Items 11–13 restate research.md §6 conflicts 4, 7 and 8 where these journeys meet them.

1. **A planner timeout has no Plan state.** D2's Plan states are Proposed, Infeasible, Saved, Confirmed and Not eaten. NFR-04 asks for an "explicit no-solution/timeout status". This file creates no Plan when the time limit passes with no answer, and shows "No answer in time" (5.24). A timeout that found a plan is Proposed. If a state is wanted, it needs a dated delta.
2. **Undo back to Saved.** D2 lists no step from Confirmed or Not eaten back to Saved, but care.md group 4 asks that an action can be undone, and FR-046 gives Undo for recent mutations. This file Voids the Entries and returns the Plan to Saved on Undo (5.29, 5.34). The vocabulary needs that step, or a rule that the Plan stays Confirmed with its Entries Voided.
3. **"Plan" means two things in the FRD.** FR-072 "plan variance" and FR-074 "intake-versus-plan variance" mean the Target (GoalPlanVersion), while the map's **Plan** is the meal Plan. Screens here say "Intake vs Target" (8.15). Keep "plan" for the meal Plan only, in code and logs too.
4. **Which Unit version a Plan confirmation uses.** FRD §17 says the latest Saved Unit version is used for new logs, but a Plan stores `selected_versions` and showed numbers from them. This file confirms with the Plan's versions and notes the change (5.38). Decide, and say whether a Plan expires when its versions are superseded (MealPlan has `expiry`, with no value given).
5. **A whole-day plan request.** The FRD plans meals and asks for a safe response to dangerous restriction requests (§19.3). This file adds `scope: "day"` only to refuse plans below the hard stop with `POLICY_FLOOR` (5.25). Decide whether day-scope planning exists at all, and how a typed "plan my day at 700" is recognised (FR-039 intent "plan").
6. **What the planner aims at when only a ceiling is set.** With "not above 500" and no target, the FRD's objective ("minimize deviation from the requested calorie target") has no target. The default target is "what's left today" (5.5); the ceiling-only case is open: aim at the ceiling, at what is left, or at preference shares only.
7. **An Activity that crosses the diary-day boundary.** 7.22 assigns it to the Day it starts in (`assumption`). Food Entries use the eating time; an interval needs its own rule.
8. **Withdrawing Health Consent: keep or delete earlier imports?** FR-076 keeps unaffected functions; AT-29 propagates withdrawal to "media, queues, private cached analysis, and exports". Whether imported Activity and weights are deleted, kept or hidden is open between this lens, WF-9 and the Auditor (7.24).
9. **Which Activity earns credit in Activity-adjusted mode.** FRD §12.2 says "eligible net exercise" and a baseline that "excludes the selected exercise component". This file credits deduplicated workouts and confirmed manual Activity, not all-day active energy (`assumption`, 7.19). The approver's Policy may need a value.
10. **Hide numbers vs a planner built on numbers.** In Hide numbers the eater can neither see nor type a ceiling (5.43). This file uses what is left of the Target as a hidden target. Decide whether numeric limits are hidden, allowed, or the planner is offered at all in that view (R37).
11. **Rolling 7 days vs a calendar week** (research conflict 4). 8.10 uses rolling 7 Days; 8.16 uses the first weekday only for separators in the 28-day view. Decide whether a calendar "this week" exists and where the first weekday is stored.
12. **Pending in the headline** (research conflict 7). 8.7 counts Pending in "eaten" and in "Remaining" and shows how much is Pending. Confirm against the reconciliation tests (NFR-01), which compare Confirmed totals.
13. **Export digits and dates** (research conflict 8). 8.24 exports Western digits and ISO dates whatever the display setting.
14. **Tracking-only and the planner.** FRD §11.4 forbids "restrictive plans" but does not say whether a carbohydrate maximum or a protein minimum is restrictive. 5.27 removes calorie and carbohydrate limits and keeps the protein minimum. The approver's tracking-only Policy (approver-10.53) should own this list.
15. **Photo-based planning and the paid tier.** FRD §23.3 proposes "photo-based planning" as paid, while the map makes planning a P0 workflow. 5.40 keeps planning from Units always available. The free/paid split stays an open question for the owner (blueprint §1.7) and does not change these stories.
16. **Who sets "available to me" on a shared tray.** FR-037 makes the tray available food and FR-049 allows available-quantity limits. This file leaves availability unset unless the eater sets it (5.4) and never infers one person's share from the photo (FR-033). Suggesting a share (tray ÷ diners) would be new behaviour and needs a source.
17. **Activity has no states in D2.** This file gives Activity the Entry states by analogy (Pending → Confirmed, Voided → Restored) in 7.12 and 7.14. Add Activity to the states table, or name its own states.
18. **Partial as the default mark.** D2 says Complete and Partial are the eater's own mark. This file shows a Day with Entries and no mark as Partial, and "Mark Day complete" toggles Complete ↔ Partial (8.13). Confirm that an unmarked Day reads Partial.

---

## 8 · Assumptions in this file

- The calorie-target tolerance default (±10 % in 5.5) is a fixture value; the product default is to be chosen on the served screen (care.md group 3).
- The deduplication overlap threshold and source order (device first) in 7.9.
- Detecting Health deletions through HealthKit's change queries (7.7); P30 does not cover it.
- "Estimated total expenditure = resting estimate + active energy" (7.20); the FRD names the figure but not its formula.
- Credit eligibility: workouts and confirmed manual Activity only (7.19; §7, item 9).
- The "unusual weight" rule (8.20), the high-energy prompt threshold (7.15) and the 366-day custom limit (8.17).
- The first weekday per country (8.16); the device calendar decides.
- Measuring the energy-mismatch percentage against the source energy (8.4); FRD §10.2 gives the threshold, not its base.
- A test-only solver time limit (5.24).
- Faisal's 05:00 Ramadan boundary and Mona's 03:00 boundary are fixture values; the default hours are a model-phase decision (EX-20).
- An Activity crossing the boundary belongs to the Day it starts in (7.22).

---

## 9 · Coverage

### 9.1 FRD lines in this dispatch → stories

| line | stories |
|---|---|
| FR-045 planned meals and drafts contribute zero; confirmation linked, no duplicate | 5.1, 5.28, 5.29, 5.30, 5.31, 5.33, 5.34, 5.37 |
| FR-048 candidates from available foods, Units, Recipe variants, rules | 5.2, 5.3, 5.12 |
| FR-049 target, ceiling, carbohydrate maximum, protein minimum, exclusions, must-include, available quantity | 5.4, 5.5, 5.6, 5.7, 5.8, 5.9, 5.13, 5.23 |
| FR-050 preference shares by count, mass or calories; basis visible | 5.10, 5.17 |
| FR-051 whole counts by default; halves or grams only when enabled | 5.11 |
| FR-052 bread expanded before checking; no packing without approval | 5.12, 5.22 |
| FR-053 unrounded validation | 5.6, 5.14, 5.15, 5.18 |
| FR-054 infeasible: blocking constraints and smallest labelled changes | 5.21, 5.22, 5.23, 5.25 |
| FR-055 counts, weights, estimate/range, macros, target status, Evidence, confirmation control | 5.14, 5.16, 5.17, 5.18, 5.19 |
| FRD §2.4 Journey C (photo, "Plan a meal", chips, unknown foods, limits, substitution) | 5.1, 5.2, 5.3, 5.12, 5.19, 5.21 |
| FRD §2.5 Journey D (Ate as planned, Change amounts, Not eaten, extra bites, never both) | 5.28–5.34 |
| FRD §9 / §9.1 (OR-Tools, integer counts, 4C ≤ r × E_macro, about vs not above, solver verifies) | 5.5, 5.6, 5.11, 5.14, 5.16, 5.24 |
| FRD §14 Meal planner states (feasible, infeasible, uncertain estimate, pending confirmation) | 5.14, 5.21, 5.16, 5.28 |
| FRD §14 Meal review states (repeated confirmation, partial meal, leftovers, correction) | 5.30, 5.31, 5.33, 5.36 |
| FRD §19.3 dangerous restriction → safe response | 5.25 |
| NFR-04 planner ≤ 2 s; timeout status | 5.14, 5.24 |
| AT-17 | 5.21 (and 5.25 nonempty) |
| AT-18 | 5.17 |
| AT-19 | 5.22, 5.9 |
| AT-20 | 5.15, 5.18 |
| AT-21 | 5.29, 5.30 |
| FR-062 import with granular permission; manual entry and correction | 7.1, 7.4, 7.6, 7.12, 7.14 |
| FR-063 record id, origin, interval, type, basis, revision, override | 7.5, 7.7, 7.11, 7.13 |
| FR-064 deduplicate; never sum aggregate and its workouts | 7.9, 7.10, 7.19 |
| FR-065 manual matching import → link or replace; corrections never create a second | 7.11, 7.14 |
| FR-066 food, active, total expenditure, net, remaining; net is not deficit | 7.16, 7.19, 7.20 |
| FR-067 coverage and last sync; denial or missing is unknown | 7.2, 7.3, 7.4, 7.21, 7.24 |
| FR-068 food macros unchanged by exercise | 7.17 |
| FRD §12.1 Fixed mode | 7.16 |
| FRD §12.2 Activity-adjusted: base, credit factor, cap, gross conversion | 7.13, 7.18, 7.19 |
| FRD §12.3 refresh at foreground, display freshness | 7.4 |
| AT-22 | 7.9, 7.11 |
| AT-23 | 7.16 |
| AT-24 | 7.17 |
| FR-069 meal report and Day report after every confirmed Entry | 8.1, 8.2, 8.4 |
| FR-070 target, consumed, remaining/over, confidence, range; reconcile | 8.2, 8.5, 8.6, 8.7, 8.8 |
| FR-071 Target version effective each Day | 8.11, 7.18, 8.23 |
| FR-072 7-day, 28-day, custom: intake, vs Target, macros, Activity, weight | 8.10, 8.16, 8.17, 8.19 |
| FR-073 complete, partial, unlogged; missing ≠ zero | 8.12, 8.13 |
| FR-074 variance not fat; today provisional | 8.14, 8.15 |
| FR-075 machine-readable export of reports | 8.24, 8.25 |
| FR-060 (P1) insufficient evidence | 8.21, 8.13 |
| FRD §13.1 report example | 8.1, 8.2 |
| FRD §14 Progress states (insufficient data, partial period, outlier weights, permission changes) | 8.26, 8.12, 8.20, 8.22 |
| AT-15 | 8.4 |
| AT-25 | 8.12 |

### 9.2 Map lines → stories

| map line (blueprint §1) | stories |
|---|---|
| WF-5 done-when: cap 500 + carbs ≤ 30 % → counts satisfying unrounded limits, or infeasible naming the blocker; Ate as planned records exactly one meal | 5.14, 5.15, 5.21, 5.29, 5.30 |
| WF-7 done-when: one workout from Health + a matching manual entry → one contribution; in fixed mode the food Target does not grow | 7.9, 7.11, 7.16 |
| WF-8 done-when: Day report with Target, consumed, remaining, shares to 100.0 %, coverage; a week with 2 missing Days shows coverage, not zeros | 8.2, 8.3, 8.12 |
| row "Eater → planner" (solver verifies unrounded; infeasible explained; floor respected) | 5.14, 5.15, 5.21–5.25 |
| row "HealthKit → app → API" (dedupe; activity mode; "no data" never "denied") | 7.2, 7.9–7.11, 7.16–7.19 |
| row "Ledger → Eater" (target version per Day; 4/4/9 shares; coverage) | 8.1–8.3, 8.11, 8.12 |
| row 1 consents ("each Health type") | 7.1, 7.24 |
| §1.6 settings: Hide numbers, activity mode, diary-day boundary, numerals | 5.43, 7.23, 8.29; 7.18; 5.35, 7.22, 8.18; 5.13, 5.41, 8.28 |

### 9.3 The unhappy paths the lens brief names

| path | WF-5 | WF-7 | WF-8 |
|---|---|---|---|
| empty | 5.3, 5.8 | 7.2, 7.3 | 8.22, 8.26 |
| error | 5.24, 5.40 | 7.4 | 8.25 |
| offline | 5.37, 5.39 | 7.12 | 8.7, 8.25, 8.27 |
| permission denied | 5.2 (camera) | 7.1, 7.2, 7.24 | 8.22 |
| slow | 5.24 | 7.4 | 8.27 |
| invalid input | 5.4, 5.5, 5.6, 5.7, 5.9, 5.13 | 7.15 | 8.17, 8.20 |
| conflict | 5.30, 5.37, 5.38 | 7.7, 7.11 | 8.9, 8.11 |

### 9.4 Counts

WF-5: 43 stories · WF-7: 24 stories · WF-8: 30 stories · **97 stories**. Acceptance-line counts are in the hand-back.
