# Eater — journeys WF-3 Log what I ate and WF-6 Correct history

Lens: **Eater** (blueprint §1.2). Dispatch: steps 2, 3 and 5 of `way/personas/_lens-brief.md` (journeys, micro stories with acceptance, ids, conflicts) for **WF-3** and **WF-6**. Written 2026-10-01.

Builds on `way/personas/eater/research.md`: its findings **E1–E44** and experience requirements **EX-01–EX-44** are cited by id and are not redone here. Also read: `way/blueprint.md` §0–§1 (with delta D2), `way/vocabulary.md` (D2: the one name for every state, error and place), `way/brief/frd-v1.0.md`, `way/research/r1-*.md` with both refutations, care.md, `way/lessons.md`. No finding the refuters mark refuted, doubtful or unverified is cited (C26, C27, C46, C54 and the dropped parts of C4, C5, C12, C15 are left out).

## How to read this file

- **Ids.** `eater-3.n` for WF-3, `eater-6.n` for WF-6.
- **A story** is "As the Eater, I …, so that …", then its trace (a map line, an FRD line — FR, AT, NFR or § — and the research ids that shaped it), a **Shared** marker when another persona has the same story, then its acceptance.
- **Layers.** Every acceptance line is tagged: `/m` module (one function's test: the nutrition core, the count parser, the Day assigner), `/s` system (components together: client outbox + API + ledger + Day projection, HealthKit writer), `/r` runtime — observed in the served product: the **iOS simulator** (smallest iPhone 16e/17e and largest Pro Max, P34; screenshots kept by XCUITest, P36), the **API over HTTP**, or the simulator's **Health** and **Shortcuts** apps. Every story has at least one `/r` line.
- **Places** are the map's and D2's: tabs **Today · Capture & Plan · My Units · Progress**; screens **Analysis review**, **Unit editor**, **Meal planner**, **Meal review**, **Settings** (Goals, Food rules, Activity, Units & language, Privacy, Export). FRD names used as written: the **quick-add** control (FRD §2.1), the **count stepper** (FRD §14), the **correction preview** (FRD §2.6), the Day's **timeline** (FR-046), the **meal report** and **day report** (map; FR-069). Three names this file needs that no list holds yet are marked *(proposed)* and listed in Conflicts item 15: **Day picker**, **Entry details**, the **Templates** list in My Units.
- **States** are D2's: Entry **Pending → Confirmed**, **Corrected**, **Voided → Restored**; Day **Provisional · Complete · Partial · Unlogged**; Analysis **Processing → Needs answers → Ready for review → Approved · Discarded · Failed**, **Pending** offline; Unit **Draft → Saved (version n) → Archived**; Plan **Saved → Confirmed · Not eaten**; Food **Approved → Superseded · Retired**; Consent **Given · Withdrawn**; Kill switch **On/Off**.
- **API.** FRD §18 endpoints as written: `POST /v1/consumption`, `POST /v1/consumption/{id}/corrections` (quantity, Unit or diary Day), `POST /v1/consumption/{id}/void`, `GET /v1/reports/day`, `GET /v1/reports/period`, `POST /v1/analyses`, `POST /v1/units/{id}/versions`. *(Proposed)*, not in FRD §18: `POST /v1/consumption/{id}/restore`, `GET /v1/consumption/{id}/history`, `/v1/templates`, `POST /v1/days`, `PATCH /v1/me/settings`. Errors only from D2: `UNIT_NOT_FOUND`, `UNIT_AMBIGUOUS`, `STALE_REVISION`, `AI_UNAVAILABLE`, `RATE_LIMITED`, `VALIDATION_ERROR`, `NOT_FOUND`, `CONSENT_REQUIRED`. Error codes never appear on an eater's screen.
- **Arabic.** Arabic text in acceptance is the eater's own data (Unit names, typed or spoken words) and digits. Button and label wording in Arabic comes from the string catalogue (one Arabic label per English word, D2) and is referred to here by its English word, e.g. "the Log button in Arabic".
- **Synthetic.** Every eater, Unit, number and Day below is synthetic. Nutrition values are calibration fixtures in the FRD's sense (§5.2, §21), not verified product labels.

---

## 1 · Goals (high)

What the eater wants from WF-3 and WF-6, each tied to the map and to the research.

| # | goal | map / FRD | research |
|---|---|---|---|
| G1 | Log a habitual food in ≤2 taps, a repeat log in ≤10 s median, with one thumb | WF-3 done-when; brief §1.5; FRD §14.1 | E25, E32, E34, E35; EX-02 |
| G2 | Every total is the sum of Entries I can see, and the same Unit gives the same number every time | FR-042, NFR-01; FRD §2.3, §18.1 | E15, E16, E18, E19; EX-14 |
| G3 | Repeat whole meals and Days, not only yesterday's | map interaction row "copy a meal or day, log a Template"; WF-3 | E31, E32; C5, C23; EX-02, EX-41 |
| G4 | The Day follows my night and Ramadan, and travel neither doubles nor loses a meal | FRD §8.1, FR-044 | E9–E13, E20; EX-07, EX-20 |
| G5 | Logging works with no signal and with no AI | FRD §7.2, §8.3, NFR-05, NFR-06, AT-32 | E30, E38; C15, C35; EX-21, EX-22 |
| G6 | My words count — Arabic dialect, both digit forms, mixed language — and nothing is logged on a guess | FR-036, FR-039, FRD §14.1 | E30, E42, E43, E44; F27; EX-39, EX-40 |
| G7 | Fixing a mistake replaces, never adds; the past changes only where I change it | FRD §2.6, §8.2, FR-041, FR-047, FR-014, FR-031 | E16, E21, E29; EX-14, EX-24 |
| G8 | Apple Health holds the same diary as the app | map row "Ledger → HealthKit"; WF-3 done-when | P29, P30, C12, C56 |

The moment and the feeling come from the research (research.md Part 2 §3): **WF-3** — "tapping 3 cheese bites with the left thumb between two bites of bread", leaving *"Done before the tea cooled, and the total is true."* **WF-6** — "the spoon was 18 g, not 15", that evening or the next morning, leaving *"The past is safe; nothing moved that I didn't move."*

---

## 2 · Fixtures (synthetic)

### 2.1 Eaters

| eater | language · digits | dialect | time zone | diary-day boundary | Target |
|---|---|---|---|---|---|
| **Mona** | Arabic · Arabic-Indic | EG | Africa/Cairo | 03:00 | 1,870 kcal |
| **Faisal** | Arabic · Western | Gulf | Asia/Riyadh | 03:00; "Ramadan days" option at 12:00 (hour is an `assumption`, EX-20) | 1,870 kcal |
| **Sam** | English · Western | — | Europe/London | 00:00 | 1,870 kcal, carbohydrate ≤30 % |

The three are the research's synthetic eaters (research.md Part 2 §2). 1,870 kcal is FRD §11.3's example Target.

### 2.2 Units and Foods (each eater's own Saved Units unless marked Food)

| Unit · Arabic name | definition | per one | P / C / F g | Evidence |
|---|---|---|---|---|
| cheese bite · قرصة جبنة (Mona), لقمة جبن (Faisal) | 5.4 g cheese + 1.5 g oil + 8 g bread (FRD §2.2, WF-2 done-when) | 46 kcal | 2.5 / 4.5 / 2.0 | measured |
| bread bite · لقمة عيش | version 1: 8 g; version 2: 9 g (AT-12) | 20 → 22.5 kcal | — | measured |
| meat bite | its own bread exception, 5 g (FR-020) | — | — | measured |
| cup of laban (yogurt drink) · كوب لبن (Faisal, Sam) | one cup, unsweetened (FR-021) | 152 kcal | 8 / 12 / 8 | label-verified |
| glass of milk · كوباية لبن (Mona; Egyptian لبن = milk, F27) | one glass | 150 kcal | 8 / 11.5 / 8 | label-verified |
| glass of milk tea · كوباية شاي بلبن (Mona) | 50 ml full-fat milk + 2 calibrated teaspoons sugar (FRD §5.2) | 60 kcal | 1.6 / 10.0 / 1.6 | measured |
| talbina spoon · معلقة تلبينة | 16 g prepared; Recipe 480 kcal over a 384 g cooked yield (AT-06) | 20 kcal | 0.6 / 3.2 / 0.5 | recipe-calculated |
| foul spoon · معلقة فول | — | 30 kcal | 2.0 / 4.0 / 0.7 | recipe-calculated |
| foul spoon with oil · معلقة فول بالزيت | — | 45 kcal | 2.0 / 4.0 / 2.4 | recipe-calculated |
| baladi loaf · رغيف بلدي | one loaf | 230 kcal | 8 / 46 / 1.5 | measured |
| molokhia plate · طبق ملوخية بالرز | one plate | 360 kcal | — | recipe-calculated |
| Sukkari date · تمرة سكري | mean of a weighed sample (FR-013) | 24 kcal | 0.2 / 5.8 / 0 | measured |
| cup of gahwa · فنجان قهوة | Saudi coffee, no milk or sugar | 3 kcal | 0 / 0 / 0 | measured |
| tuna spoon (Sam) | 23 g (FRD §24.1); **Archived** | — | — | measured |
| Food: plain biscuits | label 500 kcal/100 g (AT-08) | 5 kcal/g | — | label-verified |
| Food: date-filled biscuit | label 200 kcal per piece | 200 kcal (248 by 4/4/9) | 5 / 30 / 12 | label-verified |
| calorie-only Entry: kunafa slice | 350 kcal, macros unknown (FR-016) | 350 kcal | unknown | user-defined |

### 2.3 Days

- **Sam, Thu 1 Oct** (Provisional): Breakfast 08:41 — 3 cheese bites (138 kcal) + 1 cup of laban (152 kcal) = **290 kcal**, P 15.5 g, C 25.5 g, F 14.0 g; **1,580 kcal left**.
- **Mona, Wed 30 Sep**: Breakfast 08:40 — 3 cheese bites (138) + 1 glass of milk tea (60) + 4 foul spoons (120) = 318; Lunch 15:45 — 6 bread bites (120) + 1 molokhia plate (360) = 480; Dinner 21:10 — 15 talbina spoons (300). Day **1,098 kcal**, **772 kcal left**.
- **Faisal, Mon 8 Feb 2027** (first expected day of Ramadan, E13): iftar 18:02, a family meal 21:30, suhoor 03:40 on 9 Feb.

---

## 3 · Journey WF-3 · Log what I ate

Map: "tap a recent Unit, copy a meal or day, log a Template, speak or type, Siri or a widget; one-tap with Undo; offline outbox; meal and day report; written to Apple Health." Done-when: a recent Unit logs in ≤2 taps from Today; "copy yesterday's breakfast" logs one meal; meal report and day report appear and reconcile; Undo removes exactly one Entry; offline logs sync once; the Entry appears in Apple Health and disappears on Void.

| step | what the eater does | stories |
|---|---|---|
| A · see the Day | open Today: which Day, its zone, what remains; the empty Day | 3.1–3.2 |
| B · tap a recent Unit | two taps; the count; Undo; one-tap opt-in; same number every time; an Archived or foreign Unit | 3.3–3.8 |
| C · say or type it | a sentence becomes chips; Arabic, dialect, mixed language; voice; ambiguous words; intent; AI off | 3.9–3.14 |
| D · calories only | a calorie-only Entry | 3.15 |
| E · repeat | copy a meal; a copy uses today's Units; copy a Day; save a Template; log a Template | 3.16–3.20 |
| F · Siri and the widget | Siri and Shortcuts; the widget; one idempotent command for every surface | 3.21–3.23 |
| G · near-duplicate | a quiet note, never a discard | 3.24 |
| H · offline and slow | Pending; sync once; never wait | 3.25–3.27 |
| I · the Day | late night; Ramadan; changing the boundary; travel and clock changes; Start new day; a past Day from memory | 3.28–3.33 |
| J · reports | meal and day report; shares to 100.0 %; coverage; Target and remaining; reconciliation | 3.34–3.38 |
| K · Apple Health | write each Confirmed Entry; ask at the moment of use | 3.39–3.40 |
| L · for every eater | one thumb, large text, VoiceOver, Arabic; hide numbers; only eating changes the Day | 3.41–3.43 |

### A · See the Day

#### eater-3.1 · Read the Day and what remains at a glance
As the Eater, I open Today and see which Day I am on, its time zone when it differs from my phone's, and one large "remaining" figure above my Entries, so that I know at a glance how much remains on this Day. · FR-044, FR-070, FRD §14 Today ("selected diary date; intake/target/remaining"), map WF-3 · EX-01, EX-07, EX-11, EX-18, E19
- `/r` Given Sam's Thu 1 Oct (290 kcal, Target 1,870), When Today opens on the iPhone 16e simulator, Then the header reads "Thu 1 Oct", the largest number reads "1,580" labelled "kcal remaining", the line under it reads "290 consumed of 1,870", and the timeline lists "3 cheese bites" and "1 cup of laban", each food name before its kcal.
- `/r` Given Faisal's Day Thu 1 Oct was captured in Asia/Riyadh and the simulator's zone is then set to Europe/London, When Today opens, Then the header reads "Thu 1 Oct · Riyadh time"; with the device back in Asia/Riyadh the header names no zone.
- `/r` Given Sam was viewing Tue 29 Sep on Today two minutes ago, When the app is killed and relaunched, Then Today reopens on Tue 29 Sep with "Back to today" shown, and the first screenshot already shows that Day's Entries — never the empty Today.
- `/s` Given Sam's Thu 1 Oct, When `GET /v1/reports/day?diary_day_id=2026-10-01` is called with his token, Then it returns consumed_kcal 290, target_kcal 1870 and remaining_kcal 1580 — the figures Today shows.

#### eater-3.2 · An empty Today says what to do next, even before a Target
As the Eater, I see on an empty Day what to do next, with the buttons to do it, even before I have set a Target, so that my first log needs no setup. · FRD §3.2, FR-001, FRD §14 Today ("Empty"), map WF-1 ("Tracking works before a target exists") · EX-03, EX-19, E17
- `/r` Given a new eater with no Units, no Entries and no Target, When Today opens, Then it shows two buttons, "Log what you ate" (opens quick-add) and "Make your first unit" (opens the Unit editor), and no "kcal remaining" figure or "0 remaining" appears.
- `/r` Given that eater logs a 350 kcal calorie-only Entry (eater-3.15), When Today returns, Then it reads "350 consumed · No Target yet" with a "Set a Target" link to Settings → Goals, and still no "remaining" figure.
- `/r` Given the device language is Arabic, When the empty Today opens, Then the layout is mirrored and both buttons show their Arabic catalogue labels in the same places, reachable with one thumb.

### B · Tap a recent Unit

#### eater-3.3 · Log a recent Unit in two taps
As the Eater, I tap a recent Unit on Today and then Log, with the count I used last time already filled in, so that a habitual food is recorded before the tea cools. · WF-3 done-when ("≤2 taps from Today"), FRD §14.1 ("persist the last used unit"), FR-040, FRD §18.1, NFR-02, brief §1.5 · EX-02, EX-10, EX-13, E3, E25, E32, C34
- `/r` Given Sam's recent Units on Today include "cheese bite", last logged ×3, When Sam taps the "cheese bite" tile and then Log on the count stepper that opens at 3, Then one Entry "3 cheese bites · 138 kcal" appears in the timeline and "kcal remaining" drops by 138; the recorded walk counts two taps.
- `/r` Given that walk repeated 20 times on the iPhone 16e simulator, When the time from the Log tap to the new Entry being drawn is measured, Then the 95th percentile is ≤300 ms and no spinner or screen transition appears (NFR-02).
- `/r` Given the API is online, When Log sends `POST /v1/consumption` with {command_id (a UUID), diary_day_id "2026-10-01", eaten_at, items:[{unit_version_id of cheese bite version 1, count 3}], intent "consume"}, Then the response carries entry_id, meal totals 138 kcal, day totals and a day_revision one higher, within 2 s p95 (NFR-02).
- `/s` Given the accepted Entry, When it is read from the ledger, Then it holds entry_id, user_id, eaten_at in UTC, time zone "Europe/London", diary_day_id, the component snapshot (5.4 g cheese, 1.5 g oil, 8 g bread per bite), quantity 3, the source versions and the command_id (FR-040).

#### eater-3.4 · Set the count with one thumb, in my digits
As the Eater, I change the count with large − and + buttons or type it, including halves and Arabic-Indic digits, so that I can log "two and a half" with the hand that is not holding bread. · FR-009, FRD §14.1 ("both Arabic-Indic and Western numerals, decimal input"), NFR-08 · EX-17, EX-37, EX-39, E5, E34, E35, E36, E41
- `/r` Given the count stepper for "cheese bite" open at 3, When the eater taps − once and + twice, Then the count reads 4 and the button reads "Log 4 cheese bites"; − and + each measure at least 44×44 pt and sit in the middle band of the screen.
- `/r` Given Mona's numerals are Arabic-Indic, When she types «٢٫٥» in the count field for «قرصة جبنة», Then the field shows «٢٫٥», the Log button (in Arabic) names «٢٫٥», and the Entry stores quantity 2.5; typing "2.5" on a Western keypad stores the same 2.5.
- `/r` Given the count field is emptied or set to 0, When the eater looks at Log, Then Log is disabled and "Enter how many" shows beside the field; the keypad offers no minus sign.
- `/r` Given Sam's Day, When a request over HTTP to `POST /v1/consumption` carries count 0 or −2, Then it returns `VALIDATION_ERROR` naming the count, and the Day's revision is unchanged.

#### eater-3.5 · Undo exactly what I logged
As the Eater, I tap Undo on the banner that names what I just logged, so that a slip of the thumb ("4, not 3") is reversed without touching anything else. · WF-3 done-when ("Undo removes exactly one entry"), FR-046 ("Undo for recent supported mutations"), map WF-3 ("one-tap with Undo") · EX-13, EX-24, EX-38, C10
- `/r` Given Sam's Day holds "1 cup of laban" and he then logs "4 cheese bites", When the banner "Logged 4 cheese bites · Undo" shows and he taps Undo, Then the 4-cheese-bite Entry leaves the timeline, "1 cup of laban" stays, and "kcal remaining" rises by exactly 184.
- `/r` Given that Undo was sent online, When `GET /v1/consumption/{id}/history` *(proposed)* is called for that Entry, Then it lists "Confirmed" then "Voided · reason undo", and `GET /v1/reports/day` no longer counts the 184 kcal.
- `/r` Given VoiceOver is on, When the banner appears, Then VoiceOver announces "Logged 4 cheese bites. Undo, button" and the banner stays until the eater acts; with VoiceOver off its length is a value chosen on the served screen (care group 3; `assumption` until then).
- `/r` Given the banner has closed, When the eater opens that Entry's Entry details *(proposed)*, Then "Undo" is still offered for this most recent change (FR-046).

#### eater-3.6 · One-tap logging, only if I turn it on
As the Eater, I can turn on one-tap logging so that a tap on a recent Unit, or a command naming only unambiguous Saved Units, logs at once with a visible Undo, so that the fastest path is mine to choose and never a surprise. · FRD §2.3 ("opt-in one-tap logging with a visible Undo"), map §6 ("one-tap logging on/off") · EX-05, EX-13
- `/r` Given a new install, When Settings → Food rules is opened, Then "One-tap logging" is off, and a tile tap on Today opens the count stepper (eater-3.3).
- `/r` Given one-tap logging is on, When Sam taps the "cheese bite" tile once, Then "3 cheese bites" (the last count) appears in the timeline at once with the banner "Logged 3 cheese bites · Undo"; the recorded walk counts one tap.
- `/r` Given one-tap logging is on, When Sam types "three cheese bites and a cup of laban" in quick-add, Then both Entries log at once with one Undo banner naming both, because both words match exactly one Saved Unit each.
- `/r` Given one-tap logging is on, When the typed words match two Saved Units ("foul spoon" and "foul spoon with oil"), Then nothing is logged until Sam picks one chip (eater-3.12).

#### eater-3.7 · The same Unit gives the same number on every Day
As the Eater, I get identical calories for the same Unit version on any Day, computed by the server from my Saved Unit — never from an AI re-estimate or a number my phone sends — so that my totals cannot drift. · FRD §2.3 ("No additional AI nutrition estimate"), FRD §18.1 ("The client cannot submit its own aggregate calories"), FRD §16.5, FR-040 · EX-14, E15, E16, E22 · **Shared: Eater · Platform admin**
- `/r` Given "cheese bite" version 1, When Sam logs 3 on Tue 29 Sep and 3 on Thu 1 Oct, Then both Entries in the timeline read "138 kcal", and their Entry details read P 7.5 g, C 13.5 g, F 6.0 g.
- `/r` Given Sam's Day, When a request over HTTP to `POST /v1/consumption` for 3 cheese bites also carries "kcal": 10, Then the accepted Entry reads 138 kcal: the client's number is ignored, never stored.
- `/s` Given the analyzer adapter's call counter, When a recent-Unit tap, a Template log and a copied meal are committed, Then the counter is unchanged (FRD §16.5: "A confirmed repeated unit uses no new nutrition inference").
- `/s` Given the Platform admin moves the text-intent Registry version to Rollout, When Sam's Days 29 Sep and 1 Oct are read again with `GET /v1/reports/day`, Then every Entry and total is identical (FR-031; the admin lens's rollout story).

#### eater-3.8 · An Archived or foreign Unit never logs silently
As the Eater, I am told plainly when a Unit I logged offline was Archived on my other iPhone, and nobody else's Unit can ever be logged into my Day, so that nothing is counted against a definition I removed or do not own. · FRD §7.1 ("validate existence and ownership of every referenced unit"), FRD §18.2, NFR-07, FRD §14 My Units ("archived unit") · EX-23
- `/r` Given Sam Archived "tuna spoon" on iPhone B, When iPhone A syncs, Then the "tuna spoon" tile leaves A's recent Units on Today and My Units lists it under "Archived".
- `/r` Given iPhone A logged "2 tuna spoons" offline before that sync, When the command reaches the server, Then the Entry is kept as eaten and its Entry details note "This unit is Archived" with a "Unarchive unit" button — nothing is dropped silently (a proposal; Conflicts item 16).
- `/r` Given Mona's token, When a request over HTTP to `POST /v1/consumption` names Sam's unit_version_id, Then it returns `UNIT_NOT_FOUND` exactly as for an id that never existed, and no Entry is created (NFR-07).

### C · Say or type it

#### eater-3.9 · A typed sentence becomes my Units, with their parts shown
As the Eater, I type "three cheese bites and a cup of laban" in quick-add and see my Saved Units as chips, each with its count and what it includes, so that I confirm a breakfast in one step and see exactly what will be counted. · FRD §2.3 ("resolves the saved units, displays the expanded ingredients and count, and accepts a quick confirmation"), FR-036, FR-039, FR-045, FRD §16.3 · EX-09, EX-40, E23, E31
- `/r` Given Sam's Saved Units "cheese bite" and "cup of laban", When he types "three cheese bites and a cup of laban" in quick-add, Then the Analysis shows Ready for review as two chips — "3 × cheese bite · includes 5.4 g cheese, 1.5 g oil, 8 g bread each" and "1 × cup of laban" — with "290 kcal" and the button "Log 2 items", and the timeline is unchanged.
- `/r` Given those chips, When Sam lowers the first chip's count to 2 and taps "Log 2 items", Then two Entries appear (2 cheese bites 92 kcal, 1 cup of laban 152 kcal) and the banner reads "Logged 2 cheese bites · 1 cup of laban · Undo".
- `/s` Given that path, When it commits, Then `POST /v1/analyses` returned intent "consume" with Unit ids and counts only (no nutrient numbers), the server checked both Unit ids belong to Sam (FRD §7.1), and the Entries were committed through the same `POST /v1/consumption` service as a tile tap (FRD §16.3 step 7).
- `/r` Given Sam closes quick-add while the chips are Ready for review, When Today and `GET /v1/reports/day` are read, Then the Analysis is Discarded and the Day total is still 290 kcal (FR-045).

#### eater-3.10 · Arabic, dialect counts and mixed language land on my Units
As the Eater, I type or say my food the way I talk — «تلات قرص جبنة», «معلقتين», «نص رغيف», "ضيف ٣ cheese bites و cup laban" — so that my words and either digit form land on my own Units, and my dialect decides what «لبن» means. · FR-036, FR-015, FRD §5.1 (rule precedence), FRD §14.1 ("spoken fractions, and local food names"), map row "alias by dialect" · E30, E42, E43, E44, F27, EX-39, EX-40, research conflict 3 · **Shared: Eater · Nutrition approver**
- `/r` Given Mona's Saved Units «قرصة جبنة», «معلقة فول» and «رغيف بلدي», When she types «تلات قرص جبنة ومعلقتين فول ونص رغيف» in quick-add, Then the chips read «٣ × قرصة جبنة», «٢ × معلقة فول» and «٠٫٥ × رغيف بلدي», in Arabic-Indic digits, laid out right to left.
- `/r` Given Faisal's Saved Units «لقمة جبن» (English Alias "cheese bite") and «كوب لبن» (Alias "cup laban"), When he types "ضيف ٣ cheese bites و cup laban", Then the chips read "3 × لقمة جبن" and "1 × كوب لبن" in Western digits, each count kept beside its food name.
- `/r` Given Mona's Saved Unit «كوباية لبن» (milk) and Faisal's «كوب لبن» (yogurt drink), When each types their own word, Then each chip names that eater's own Unit; given a fixture eater with dialect Gulf and no laban Unit types «كوب لبن», Then the chip shows the Food the Nutrition approver's Gulf Alias gives (a yogurt drink, F27) with its Evidence badge, and waits for a tap — the eater's own Unit always wins over a dialect Alias (FRD §5.1).
- `/m` Given the count parser, When it reads "18", «١٨», "three", «تلات», «ثلاث», «معلقتين», «رغيفين», «نص» and «ربع», Then it returns 18, 18, 3, 3, 3, 2, 2, 0.5 and 0.25.
- `/r` Given Mona added the Latin-script Alias "ful" to «معلقة فول», When she types "2 ful", Then the chip reads «٢ × معلقة فول» (E44).

#### eater-3.11 · Speak it, see the words, then log
As the Eater, I tap the microphone in quick-add and say my food, then see the editable words and the chips before anything is logged, so that a misheard word never becomes food I did not eat. · FR-036 ("visible transcription … allow replay or text editing before an uncertain entry commits"), FR-076, FR-078, FRD §14.1, map row "Eater → app: consents (mic; sending voice to Google's AI)" · EX-26, EX-40, E43, P15, P28
- `/r` Given the microphone has never been asked for, When Mona first taps the microphone in quick-add, Then one plain sentence (in Arabic) says why it is needed and that audio is deleted within 24 hours, and only then the iOS microphone prompt appears.
- `/r` Given the microphone is allowed and the Consent for Google's AI is Given, When Mona says «تلات قرص جبنة وكوباية شاي بلبن», Then the words appear editable in the quick-add field and the chips «٣ × قرصة جبنة» and «١ × كوباية شاي بلبن» appear below; nothing is in the timeline until she taps Log.
- `/r` Given the words came back as «تلات» but she meant two, When she edits the field to «اتنين قرص جبنة», Then the chip changes to «٢ × قرصة جبنة» before anything is logged.
- `/r` Given the microphone permission is denied in iOS, When quick-add opens, Then the microphone shows "Voice is off · Turn on in Settings", and typing, recent Units and Templates log as before (FRD §3.2).

#### eater-3.12 · Ambiguous or unknown words wait for my choice
As the Eater, I am asked to choose when a word matches two of my Units or none, so that the app never guesses which food I ate. · FR-035 (≤2 questions; "High-impact ambiguity must not be silently resolved"), FRD §2.3 ("unambiguous approved units"), FRD §14.1 ("Voice must not translate a requested food into a different food silently"), FRD §7.1 · EX-24
- `/r` Given Mona's Saved Units «معلقة فول» and «معلقة فول بالزيت», When she types «٣ معالق فول», Then the Analysis Needs answers: one chip asks which of the two Units she means and Log stays disabled until she picks one.
- `/r` Given Sam has no kunafa Unit, When he types "2 kunafa and 3 cheese bites", Then the kunafa chip reads "kunafa · not one of your units" with "Find the food" (Capture & Plan, WF-4), "Make a unit" (Unit editor) and "Calories only" (eater-3.15), while "3 × cheese bite" can be logged on its own.
- `/r` Given an Analysis with three unclear words, When the chips appear, Then at most two questions are asked in this pass and the third word offers manual entry (FR-035).
- `/s` Given Sam's Saved Units, When `POST /v1/analyses` is called with "2 kunafa", Then the Analysis returns required_questions for the unmatched item, the response for a two-Unit match carries `UNIT_AMBIGUOUS`, and no Entry is committed (FRD §7.1).

#### eater-3.13 · My words decide whether anything is logged
As the Eater, I can type "calculate and save my bite", "how many calories in 2 cheese bites?", "plan my dinner", "how much is left?", "add another 3 spoons", "18 not 15" or "start a new day" in the same quick-add box, so that each does what I meant and only eating, correcting or removing ever changes my Day. · FR-039 (estimate, calibrate, plan, consume, correct, remove, report, start a new day), AT-13, FRD §4.3, FRD §8.2, FR-045 · EX-14
- `/r` Given Sam's Day at 290 kcal, When he types "calculate and save my bite" with a photo of a cheese bite on the scale, Then the Unit editor in My Units opens with a Unit Draft; after "Save unit", Today still reads 290 consumed and `GET /v1/reports/day` returns consumed_kcal 290 (AT-13).
- `/r` Given that Day, When he types "how many calories in 2 cheese bites?", Then quick-add answers "92 kcal" with no Log button pressed and the timeline unchanged; When he types "how much is left?", Then the day report opens at "1,580 kcal remaining" and the Day's revision is unchanged.
- `/r` Given that Day, When he types "plan my dinner, 600 kcal", Then the Meal planner in Capture & Plan opens with a 600 kcal cap and the Day total is unchanged (WF-5).
- `/r` Given Mona's Dinner holds «١٥ معلقة تلبينة», When she types «زوّد ٣ معالق تلبينة», Then a new chip «٣ × معلقة تلبينة · ٦٠» appears as an addition; When instead she types «١٨ مش ١٥», Then the correction preview opens (eater-6.5) and no consumption chip appears (FRD §8.2).
- `/r` Given any Day, When "start a new day" (or «ابدأ يوم جديد») is typed, Then the Start new day confirmation opens (eater-3.32) and nothing is deleted.

#### eater-3.14 · AI off, used up or not allowed: my Units still log
As the Eater, I can still log from my recent Units, Templates and typed Unit names when sentence understanding is unavailable — Kill switch On, daily AI quota used up, or my Consent for Google's AI Withdrawn — and one line tells me why, so that logging never stops. · FRD §7.2, AT-32, NFR-05, FRD §16.4 ("A kill switch must preserve manual and cached logging"), FRD §16.5, FR-076, D2 Registry ("Kill switch … fail fast with AI_UNAVAILABLE") · EX-22, R3, research conflict 9 · **Shared: Eater · Platform admin**
- `/r` Given the text-intent Kill switch is On, When Sam types "3 cheese bites" in quick-add, Then one line reads "Sentences can't be read right now — pick from your units", the recent Units whose names match the typed words ("cheese bite") are listed with count steppers, and tapping "cheese bite" and then Log records 3 cheese bites.
- `/r` Given Sam's daily AI quota is used up (`RATE_LIMITED` with a reset time), When he types a sentence, Then the line names the reset time ("Sentences are back at 00:00"), and recent Units, Templates and copied meals still log; a recent-Unit tap never uses the quota (FRD §16.5).
- `/r` Given Mona's Consent for Google's AI is Withdrawn, When she opens quick-add, Then the microphone and sentence reading are off with "Off by your choice · Settings → Privacy", and recent Units, Templates, copy and Calories only log as usual; a request over HTTP to `POST /v1/analyses` with her token returns `CONSENT_REQUIRED`.
- `/s` Given the analyzer adapter mock times out on every call, When 50 recent-Unit logs are made, Then all 50 are Confirmed and none waited on the analyzer (NFR-05).

### D · Calories only

#### eater-3.15 · A calorie-only Entry when I only have a number
As the Eater, I add "kunafa slice, 350 kcal" as a calorie-only Entry from quick-add, so that a food I cannot describe still counts — without the app making up its macros. · FR-016 ("label it user-defined and leave unknown macros unknown"), AT-16, FRD §18.1 ("Calorie-only custom items use an explicit user-override path"), FRD §20.1 · EX-27
- `/r` Given Sam's Day at 290 kcal with full macros, When he opens quick-add → "Calories only", enters "kunafa slice" and 350, and taps Log, Then the timeline shows "kunafa slice · 350 kcal · user-defined", Today reads "640 consumed", and the macro bars read "Macros known for 45 % of kcal" (AT-16).
- `/r` Given that Entry, When its Entry details open, Then protein, carbohydrate and fat read "Unknown" — never 0 g — and no grams are shown.
- `/r` Given the calories field is empty, negative or not a number, When Log is viewed, Then Log is disabled with "Enter the calories" beside the field.
- `/s` Given the kunafa slice, When the calorie-only command is sent to `POST /v1/consumption`, Then the stored Entry's Evidence is user-defined, its macros are null (not 0), and `GET /v1/reports/day` returns macros_complete false.

### E · Repeat a meal, a Day or a Template

#### eater-3.16 · Copy a meal from any past Day
As the Eater, I copy a meal from yesterday or any earlier Day into today, so that a repeated breakfast costs one action and I am not limited to yesterday. · WF-3 done-when ("copy yesterday's breakfast logs one meal"), map row "consume: copy a meal or day" · C5, E31, E32, EX-02, EX-41
- `/r` Given Mona's Wed 30 Sep Breakfast (3 cheese bites, 1 glass of milk tea, 4 foul spoons; 318 kcal), When on Thu 1 Oct she opens quick-add → "Copy from another Day", picks 30 Sep → Breakfast and taps Log, Then today's Breakfast gains exactly those three Entries (318 kcal) as one meal, and the meal report and day report appear (eater-3.34).
- `/r` Given Sam moves the Day picker *(proposed)* on Today to Tue 8 Sep, When he opens that Day's Lunch menu → "Copy to today" and taps Log in quick-add, Then the Lunch's Entries are logged into Thu 1 Oct — copying is not limited to yesterday (E32).
- `/s` Given the copy, When the ledger is read, Then each copied Entry is new (own entry_id, the copy's command_id, diary_day_id 2026-10-01, eaten_at at the time of the copy) and the 30 Sep Entries are unchanged.
- `/r` Given the copy request is resent after a lost response with the same command_id, When Today and `GET /v1/reports/day` are read, Then the meal appears once (AT-10).

#### eater-3.17 · A copy uses my Units as they are now, and says what changed
As the Eater, I see when a Unit in the meal I copy has changed since that Day, so that the copy uses my current definition and I know why its number differs from last time. · FR-014, FRD §17 EatingUnitVersion ("Latest approved version is used for new logs only") · E15, E21, EX-14
- `/r` Given Sam's "bread bite" went from version 1 (8 g, 20 kcal) to version 2 (9 g, 22.5 kcal) on 30 Sep, and his 29 Sep Breakfast holds "6 bread bites" (120 kcal on version 1), When he copies that Breakfast to 1 Oct, Then quick-add notes before Log "bread bite changed since 29 Sep: 8 g → 9 g", the new Entry reads "6 bread bites · 135 kcal", and 29 Sep still reads 120 kcal.
- `/r` Given the copied meal holds "2 tuna spoons" and "tuna spoon" is Archived, When quick-add lists the meal's items, Then that line reads "tuna spoon is Archived — skipped" with "Unarchive unit", and the other items can still be logged.
- `/r` Given the source meal holds an Entry with Evidence "estimated analogue", When it is copied, Then the copy keeps the badge "estimated analogue" — a copy never upgrades Evidence.

#### eater-3.18 · Copy a whole Day
As the Eater, I copy a whole earlier Day's food into today and can leave a meal out, so that a routine Ramadan day or work day is logged at once. · map row "copy a meal or day", FR-045 · C23 ("not including imported activity"), E11, E12, EX-02, EX-41
- `/r` Given Faisal's Mon 8 Feb 2027 holds food Entries in three meals, an Activity "walk 30 min" and one Voided Entry, When on Tue 9 Feb he picks 8 Feb in the Day picker → "Copy this Day" and taps Log, Then today receives every Confirmed food Entry of that Day under the same meal names, and neither the Activity nor the Voided Entry is copied.
- `/r` Given "Copy this Day", When quick-add lists the Day's meals, Then it shows each meal with its kcal and a tick per meal, all ticked; unticking one meal leaves it out of the copy.
- `/r` Given Faisal copies the same Day again two minutes later, When he taps Log, Then the near-duplicate note appears (eater-3.24) and nothing is thrown away.

#### eater-3.19 · Save a meal as a Template
As the Eater, I save a meal I eat often as a named Template, so that next time it is one tap from quick-add. · map §1 ("save meal Templates", vocabulary "Template (a saved meal)"), FRD §2.1 ("recent units, or a meal template"), FR-045 · C5, C45, EX-02
- `/r` Given Mona's Wed 30 Sep Breakfast, When she opens the Breakfast menu → "Save as Template", names it «فطار عادي» and taps Save, Then the Templates list *(proposed)* in My Units shows «فطار عادي · ٣ عناصر · ٣١٨» and quick-add shows it under Templates.
- `/r` Given the Template was saved, When Today and `GET /v1/reports/day` for 1 Oct are read, Then nothing was logged by saving.
- `/r` Given the name is empty or every item was removed, When Save is viewed, Then Save is disabled with "Give it a name" or "Add at least one item" beside the field; given the name «فطار عادي» already exists, Then "A Template with this name exists — choose another name" appears and nothing is overwritten.
- `/s` Given «فطار عادي», When it is saved through `POST /v1/templates` *(proposed)* and read back, Then the Template stores Unit ids and counts — no Unit versions and no calories — so its numbers come from the Units when it is logged.

#### eater-3.20 · Log a Template, changing counts first if I want
As the Eater, I log a Template from quick-add and can change a count or leave an item out before Log, so that "usual breakfast, but two cheese bites today" is still two taps. · FRD §2.1, map WF-3 ("log a Template"), FR-014 · C5, EX-02, EX-17
- `/r` Given the Template «فطار عادي», When Mona taps it in quick-add, Then its three items appear with count steppers at 3, 1 and 4 and a Log button naming 3 items; tapping Log adds three Entries (318 kcal) to Breakfast — two taps in the recorded walk.
- `/r` Given those steppers, When she lowers the cheese bites to 2 and unticks the tea, Then Log names 2 items and logs «٢ × قرصة جبنة» and «٤ × معلقة فول» (212 kcal), and the Template in My Units still reads 3, 1 and 4.
- `/r` Given one-tap logging is on, When she taps the Template, Then all three items log at once with an Undo banner naming «فطار عادي».
- `/r` Given she deletes the Template in My Units → Templates, When the banner offers Undo and she ignores it, Then the Template is gone and no Entry on any Day changed (a Template is not part of the ledger).

### F · Siri and the widget

#### eater-3.21 · Log with Siri or the Shortcuts action
As the Eater, I say "Log cheese bite in Sips & Bytes" or run the "Log a Unit" action, so that I can log without opening the app, through the same command the app uses. · map WF-3 ("Siri or a widget"), map row "one idempotent consume command for every surface", FR-040 · P22, P23, C55, EX-13
- `/r` Given Sam's Unit "cheese bite" (last count 3) and the App Shortcut phrase "Log cheese bite in Sips & Bytes" (the app name plus one parameter, the Unit, P22), When the "Log a Unit" App Intent runs from the simulator's Shortcuts app with Unit = cheese bite and no count, Then it logs 3 and the result snippet reads "Logged 3 cheese bites · 1,442 kcal remaining" with an Undo button (UndoableIntent and SnippetIntent, iOS 26, P23).
- `/r` Given the same action, When it runs with Count = 2, Then the snippet reads "Logged 2 cheese bites", and Today lists the Entry when the app is opened.
- `/r` Given that snippet, When Undo is tapped on it, Then the Entry is Voided (reason undo) and Today no longer lists it.
- `/r` Given the simulator is offline, When the intent runs, Then the snippet reads "Logged 3 cheese bites · waiting to send", and Today shows the Entry as Pending (eater-3.25).
- Note: no source confirms App Shortcut phrases with Siri in Arabic (P22, r1-refute-b open point 3) — `assumption`. Arabic acceptance for this path runs through the Shortcuts action; typed and tapped logging never depend on Siri.

#### eater-3.22 · Log from the widget, discreetly
As the Eater, I tap a recent Unit on the Home Screen widget, so that a glass of tea is logged without opening the app — and a locked phone shows nothing of my diary. · map WF-3 ("Siri or a widget"), map row "one idempotent consume command", map §6 ("hide numbers") · P24, C55, EX-43, research Part 2 §4 ("Discreet")
- `/r` Given the Sips & Bytes widget on the simulator's Home Screen shows Sam's three most recent Units, When he taps "cup of laban", Then the app does not open, the widget shows "Logged 1 cup of laban · Undo", and Today, once opened, lists the Entry.
- `/r` Given the simulator is locked, When the Lock Screen widget's button is tapped, Then nothing is logged until the device is unlocked (P24), and the Lock Screen widget shows no food names or kcal — only "Sips & Bytes · Log" (C55: "an innocuous summary").
- `/r` Given Settings → Privacy → "Show kcal remaining on widgets" is turned on, When the Home Screen widget refreshes after that log, Then it shows "1,428 kcal remaining"; given Settings → Goals → "Hide numbers" is on, Then the widget shows no numbers at all.
- `/r` Given the widget shows "Logged 1 cup of laban · Undo", When Undo is tapped on it, Then the Entry is Voided and the widget returns to its tiles.

#### eater-3.23 · Every surface sends one idempotent command
As the Eater, I can trust that a tap, the chips, a Template, a copy, Siri and the widget all record food the same way, so that a retry or a double delivery never doubles my food. · map row "one idempotent consume command for every surface", FR-040, FR-043 ("Retried commands and duplicate delivery must not add food twice"), AT-10, FRD §18, NFR-06
- `/r` Given one consumption command for 3 cheese bites with one command_id sent three times over HTTP to `POST /v1/consumption` (AT-10), When each response is read, Then all three carry the same entry_id and day_revision, and `GET /v1/reports/day` shows one new Entry and one 138 kcal addition.
- `/s` Given the tile, the quick-add chips, a Template, a copy, the Siri intent and the widget intent, When each logs once in a system test, Then each sends exactly one `POST /v1/consumption` with a fresh command_id and intent "consume", through the same validation and snapshot path.
- `/s` Given the app is killed after sending a command but before the response arrives, When it relaunches and the outbox resends with the same command_id, Then no second Entry is created (NFR-06 session recovery).

### G · Near-duplicate

#### eater-3.24 · A near-duplicate gets a quiet note, never a discard
As the Eater, I see a quiet note when I log the same thing again within minutes, with "Keep both" and "Undo this one", so that a real second helping is never thrown away and a double tap is easy to fix. · FR-043 ("Near-duplicate human commands should show a warning rather than being automatically discarded") · EX-24, EX-35
- `/r` Given Sam logged "3 cheese bites" at 08:41, When he logs "3 cheese bites" again at 08:43, Then both Entries are in the timeline and the banner reads "You logged 3 cheese bites at 08:41 · Keep both · Undo this one" — no dialog.
- `/r` Given that note, When he taps "Undo this one", Then only the 08:43 Entry is Voided; When instead he taps "Keep both" or ignores the note, Then both stay counted and Today reads 276 kcal for the two.
- `/s` Given two commands with different command_ids and the same items 2 minutes apart, When both reach `POST /v1/consumption`, Then both are Confirmed; the note's window (10 minutes here) is a value chosen in the served product (`assumption`; Conflicts item 5).
- `/r` Given 3 cheese bites logged at 08:41, When the same is logged at 12:30, Then no note appears.

### H · Offline and slow

#### eater-3.25 · Log with no signal; Pending is shown, not hidden
As the Eater, I keep logging with no signal and see each new Entry marked Pending and the Pending kcal shown apart from the Confirmed figure, so that I know what the server has accepted without being interrupted. · FRD §8.3 ("The client distinguishes pending from confirmed totals"), FRD §7.2, NFR-06, D2 Entry ("the day total shows Pending separately"), FRD §14 Today ("offline pending") · EX-12, EX-21, E19, E29, E38, C15, C35
- `/r` Given Mona online with Breakfast 318 kcal Confirmed on Thu 1 Oct, When the simulator goes offline and she logs «١ × كوباية شاي بلبن» and «٢ × تمرة سكري», Then both Entries show Pending with a clock symbol and the word, the headline reads «١٬٤٤٤» kcal remaining with the Pending figure «١٠٨» beside it and the Confirmed figure «١٬٥٥٢» under it (labels in Arabic), and no alert appears.
- `/r` Given offline, When she opens My Units, the Templates list and any of the last 30 Days, Then each opens from the device with a quiet "Offline — showing saved data" line (NFR-06: at least 30 days cached).
- `/s` Given the two offline logs, When the outbox is read, Then each command is stored durably with its UUID command_id and expected revision, survives an app kill, and is listed once (FRD §8.3).
- `/r` Given offline, When she types a sentence in quick-add, Then the line "Sentences need a connection — pick from your units" appears, her matching recent Units are offered, and nothing is logged from the sentence later on its own (FRD §7.2).

#### eater-3.26 · Back online: each Pending Entry is Confirmed once
As the Eater, I come back online and each Pending Entry becomes Confirmed exactly once, so that logging offline never doubles my food. · FRD §8.3 ("the server accepts an unprocessed command once"), AT-10, NFR-06, FR-042, FR-043, FRD §18.2 ("bounded retries with the same command ID")
- `/r` Given Mona's two Pending Entries, When the simulator reconnects, Then both lose the Pending mark, the headline reads «١٬٤٤٤» with no Pending figure, and `GET /v1/reports/day` for 2026-10-01 returns consumed_kcal 426.
- `/r` Given the app is killed while the first command's response is in flight, When it relaunches online, Then the outbox resends with the same command_id and the Day has exactly two new Entries (426 kcal in total).
- `/s` Given the server answers 503 to a queued command, When the client retries with the same command_id, Then one Entry results; after the retry budget is spent the Entry stays Pending with "Will retry" — never dropped, never shown as Confirmed.
- `/s` Given iPhone A and iPhone B each logged food offline on the same Day with the same expected day revision, When both sync, Then both consume commands are Confirmed and the Day equals the sum of both (additions do not conflict; edits do, eater-6.22; Conflicts item 3).

#### eater-3.27 · A slow network never makes me wait
As the Eater, I see my Entry at the instant of the tap even when the server is slow, so that a weak signal never blocks the next bite. · NFR-02 ("Local feedback ≤300 ms p95; online commit acknowledgment ≤2 s p95"), FRD §8.3 · EX-12, EX-16
- `/r` Given the API is throttled to answer after 6 s, When Sam taps Log on 3 cheese bites, Then the Entry appears within 300 ms marked Pending, turns Confirmed when the answer arrives, and no spinner covers Today.
- `/r` Given that slow commit, When Sam logs a second Unit meanwhile, Then it also appears at once and both turn Confirmed in the order sent.
- `/s` Given the documented test profile, When 100 online commits are timed at the API, Then acknowledgment is ≤2 s p95.

### I · The Day: time zone, boundary, Start new day

#### eater-3.28 · A late dinner lands on the Day I am still living
As the Eater, I set my Day to end at 03:00 so that a sandwich at 00:20 counts on the Day I am still living, and Today says which Day it is. · FRD §8.1 ("A custom boundary supports … late-night eating"), FR-044, FRD §17 UserProfile (diary_boundary) · E9, E10, E20, EX-07, EX-20
- `/r` Given Mona's boundary is 03:00 (Africa/Cairo), When she logs «١ × رغيف بلدي» at 00:20 local time on 1 Oct, Then the Entry is on Wed 30 Sep, and Today's header at 00:25 names Wednesday 30 September with "Day ends 03:00" (in Arabic).
- `/r` Given 03:01 has passed, When Today is opened, Then it shows Thu 1 Oct (empty) and 30 Sep still holds the 00:20 Entry.
- `/m` Given eaten_at 00:20 local on 1 Oct in Africa/Cairo, When the Day assigner runs with boundary 03:00, Then diary_day_id is 2026-09-30; with boundary 00:00 it is 2026-10-01.
- `/r` Given Settings → Units & language, When "My day ends at" is opened, Then it shows the boundary with a time picker and the line "Changes apply from your next Day; past Entries stay where they are".

#### eater-3.29 · Ramadan nights stay on one Day
As the Eater, I switch on a Ramadan option so that iftar, the late family meal and suhoor fall on one Day, under my own meal names. · FRD §8.1, FR-044, map §6 ("diary-day boundary") · E1, E11, E12, E13, EX-05, EX-20, research conflict 2
- `/r` Given Faisal turns on Settings → Units & language → "Ramadan days" (boundary 12:00; the hour is an `assumption` for the model phase), When he logs iftar at 18:02 on Mon 8 Feb 2027, the family meal at 21:30 and suhoor at 03:40 on 9 Feb, Then all three meals are on Day Mon 8 Feb and its day report sums them.
- `/r` Given those meals, When Today shows them, Then they carry Faisal's meal names (iftar and suhoor in Arabic, and the name he typed for the 21:30 meal), not fixed Breakfast/Lunch/Dinner slots.
- `/r` Given "Ramadan days" is on, When Faisal turns it off on 10 Mar 2027, Then new Entries follow his 03:00 boundary from the next Day, and February's Days keep their Entries.

#### eater-3.30 · Changing the boundary never moves my past
As the Eater, I can change my boundary at any time and it applies only from my next Day, so that last week's totals never shift. · FRD §8.1 ("A manual day switch is not an instruction to reinterpret all past events"; "stable diary-day assignment"), FR-047 · EX-14, research conflict 1
- `/r` Given Sam's boundary is 00:00 and his Entry at 01:10 on Wed 30 Sep is on Day 30 Sep, When he changes the boundary to 03:00 on 1 Oct, Then the 01:10 Entry stays on Day 30 Sep and Progress shows Tue 29 Sep and Wed 30 Sep with unchanged totals.
- `/s` Given the change, When the ledger is read, Then no Entry's diary_day_id changed, no Correction was written, and the new boundary carries an effective-from Day (2026-10-02).
- `/r` Given he wants that 01:10 Entry on 29 Sep, When he moves it (eater-6.17), Then it is one visible Correction of one Entry, never a side effect of the boundary.

#### eater-3.31 · Travel and clock changes neither duplicate nor lose a meal
As the Eater, I fly from Riyadh to London and my Entries stay on their Days with their own local times, so that travel never creates or swallows a meal. · FRD §8.1 ("Travel and daylight-saving changes must not duplicate or lose meals"; "absolute UTC timestamp, capture time zone"), FR-040, FR-044 · E20, EX-07
- `/r` Given Faisal logged lunch at 13:00 Asia/Riyadh on Thu 1 Oct and the simulator's zone then changes to Europe/London, When he logs dinner at 20:00 London time, Then Day 1 Oct lists both in time order, lunch labelled "13:00 Riyadh time" and dinner "20:00".
- `/m` Given two Entries at 01:30 local in Europe/London on the night the clocks go back (the hour repeats), When the Day assigner stores them, Then they keep two distinct UTC eaten_at values and sit on the same Day in UTC order.
- `/s` Given the zone change, When `GET /v1/reports/day` for 2026-10-01 is read before and after, Then the Entry count and totals are identical.
- `/r` Given the device zone changed, When Faisal logs next, Then the Entry takes the device's current zone without a question, unlike a zone frozen at sign-up (E20).

#### eater-3.32 · "Start new day" never deletes
As the Eater, I can tap or say "Start new day" after a late night, so that new Entries go to the next Day while every earlier Day stays exactly as it was. · FR-044 ("creates or selects a diary day; it never deletes previous days. The selected day and time zone remain visible"), FR-039, AT-14, FRD §8.1 · EX-07
- `/r` Given Mona at 02:00 on 1 Oct is still on Day Wed 30 Sep (boundary 03:00, 1,098 kcal), When she taps the Day picker → "Start new day" and confirms, Then Today shows Thu 1 Oct, empty, with Wed 30 Sep and its 1,098 kcal one tap away in the Day picker, and nothing on 30 Sep changed.
- `/r` Given the same Day, When she types «ابدأ يوم جديد» in quick-add instead, Then the same confirmation opens; nothing is deleted, cleared or reset.
- `/s` Given Mona's Days, When `POST /v1/days` *(proposed)* for 2026-10-01 is sent twice, Then one Day exists, and `GET /v1/reports/day` for 2026-09-30 returns the same totals and revision as before.
- `/r` Given she started 1 Oct early, When 03:00 passes, Then Today stays on Thu 1 Oct and no extra Day is created.

#### eater-3.33 · Log onto a past Day from memory
As the Eater, I pick yesterday in the Day picker and log the dinner I forgot, so that a missed meal is recorded on its Day and today stays untouched. · FR-047, FR-044, FRD §14 Today ("Selected diary date") · E24, E33, EX-07
- `/r` Given Sam's Thu 1 Oct at 290 kcal, When he picks Wed 30 Sep in the Day picker and logs "1 cup of laban" at 21:00, Then 30 Sep's day report rises by 152 and Thu 1 Oct still reads 290 consumed.
- `/r` Given a past Day is selected, When Today is viewed, Then the header names that Day with "Back to today", so a log never lands on another Day unnoticed.
- `/s` Given that log, When its command is read in the outbox, Then it carries diary_day_id 2026-09-30 and eaten_at 21:00 local on 30 Sep (the time can be changed before Log), and `GET /v1/reports/period` for 24–30 Sep includes the 152 kcal.

### J · Meal report and day report

#### eater-3.34 · A meal report and a day report after every entry
As the Eater, I see right after each log what was added and where my Day now stands — calories, macro grams, 4/4/9 kcal and shares, Target and remaining — so that the reports I used to ask a chatbot for are always there and always the same. · FR-069, FR-070, FRD §13.1, FRD §10.1, FRD §10.3, map row "Ledger → Eater: meal and day report" · EX-11, E22, E23
- `/r` Given Sam's Day (Provisional) at 720 kcal (P 30 g, C 105 g, F 20 g) with Target 1,870 and carbohydrate ≤30 %, When he logs a lunch resolving to 480 kcal (P 42 g, C 33 g, F 20 g), Then the meal report on Today reads "Lunch added: 480 kcal" with Protein 42 g · 168 kcal · 35.0 %, Carbohydrate 33 g · 132 kcal · 27.5 %, Fat 20 g · 180 kcal · 37.5 %, and "Carbohydrate: within the 30 % maximum".
- `/r` Given that log, When the day report under the meal report is read, Then it reads "Today: 1,200 kcal · Target 1,870 · Remaining 670" with Protein 72 g · 288 kcal · 24.0 %, Carbohydrate 138 g · 552 kcal · 46.0 %, Fat 40 g · 360 kcal · 30.0 %, and "Carbohydrate: above the 30 % maximum" in neutral wording (FRD §13.1).
- `/r` Given those share columns, When they are read, Then the heading names their basis "share of macro-derived energy (4/4/9)" (FRD §10.1).
- `/r` Given that lunch, When the `POST /v1/consumption` response is read over HTTP, Then its meal totals and day totals carry the same figures as the two reports.

#### eater-3.35 · Shares add to 100.0 % and the headline keeps the source's calories
As the Eater, I see macro shares that always add to exactly 100.0 % and a calorie headline that keeps the label's or record's own number even when the macros say otherwise, so that I can trust both. · FRD §10.1 ("The calorie headline remains source energy"), FRD §10.2 (largest remainder; zero-energy "not applicable"; precision), AT-15, FR-030, FR-069
- `/m` Given P 9 g, C 9 g, F 4 g (36, 36 and 36 kcal), When the share calculator in the nutrition core rounds for display, Then it returns 33.4 %, 33.3 % and 33.3 % (largest remainder; ties broken protein, carbohydrate, fat — a proposal), summing to 100.0 %, where plain rounding would give 99.9 %.
- `/r` Given Sam logs one "date-filled biscuit" (label 200 kcal; P 5 g, C 30 g, F 12 g = 248 kcal by 4/4/9), When the meal report shows, Then the headline reads 200 kcal, the shares read 8.1 %, 48.4 % and 43.5 % (100.0 %), and a line reads "Macros give 248 kcal (4/4/9); the label's 200 kcal is kept" (AT-15; 24 % and 48 kcal exceed the Policy's >10 % and >10 kcal).
- `/r` Given a Day whose only Entry is "1 cup of gahwa" (3 kcal; 0 g protein, carbohydrate and fat), When the day report shows, Then the shares read "Not applicable" — never 0 % and never an error.
- `/s` Given the stored nutrient values of these Entries, When the reports are rendered, Then the stored values remain unrounded decimals; only the displayed fields are rounded.

#### eater-3.36 · Missing macros show coverage, never invented grams
As the Eater, I see how much of my calories has known macros when an Entry has calories only, so that my macro split is never shown as complete when it is not. · AT-16, FR-016, FRD §10.2 ("Missing macros: Mark unknown; display coverage"), FRD §14 Today ("missing macros") · EX-27
- `/r` Given Sam's Day of 290 kcal with known macros plus the 350 kcal "kunafa slice" (user-defined), When the day report shows, Then it reads "640 kcal" and "Macros known for 290 of 640 kcal (45 %)", and the shares 21.4 %, 35.2 % and 43.4 % are labelled "of Entries with known macros".
- `/r` Given that Day, When the protein bar on Today is read, Then it shows "15.5 g + unknown", never a figure that seems to include the kunafa.
- `/s` Given that Day, When `GET /v1/reports/day` is called, Then it returns consumed_kcal 640, macro_coverage_kcal 290 and macros_complete false, and attributes no protein to the kunafa Entry.

#### eater-3.37 · Target, remaining and over — plainly, without judgment
As the Eater, I see Target, consumed and remaining (or over) with the Evidence behind them and a range when estimates are included, in calm words, so that an ordinary over-target Day never feels like a failure. · FR-070 ("target, consumed, remaining/over, source confidence, and estimated range"), FR-071, FRD §11.4, FRD §14.2, FRD §20.1, FRD §14 Today ("over target") · EX-35, EX-42
- `/r` Given Sam's Day at 1,990 kcal with Target 1,870, When Today shows, Then the headline reads "120 kcal over" in the same neutral colour as "remaining", carries the word "over", and no red, alert, "bad" or advice to skip a meal appears.
- `/r` Given that Day includes an "estimated analogue" Entry of 450 kcal with a range of 380–520, When the day report opens, Then it reads "1,990 kcal · heuristic range 1,920–2,060", labelled a heuristic low/high, not a confidence interval.
- `/r` Given that Day, When the Evidence line of the day report is read, Then it counts Entries per badge, e.g. "measured 4 · recipe-calculated 1 · estimated analogue 1".
- `/s` Given Sam's Target changed from 1,870 to 1,800 on 1 Oct, When `GET /v1/reports/day` for 2026-09-30 is read, Then it still compares against 1,870 (FR-071).

#### eater-3.38 · Every figure adds up to Entries I can see
As the Eater, I can tap any figure and see the Entries it adds up, so that a total never moves without a visible Entry, Correction, Void or Restore. · FR-042 ("one transaction. Replaying the ledger shall reproduce the totals"), NFR-01, FRD §17 DiaryDayProjection, map row "Ledger → Eater" · E18, E19, EX-14 · **Shared: Eater · Support agent**
- `/r` Given Sam's Day at 1,200 kcal, When he taps the consumed figure on Today, Then the list of Entries shows each one's kcal and the sum line reads 1,200 kcal.
- `/s` Given that Day's accepted events (creates, a Correction, a Void, a Restore), When the Day projection is rebuilt from the ledger alone, Then consumed kcal, macro grams, coverage and revision equal the stored projection exactly.
- `/s` Given a create and its Day-projection update, When the projection write is forced to fail, Then neither is stored (one transaction) and the client keeps the Entry Pending.
- `/r` Given an Active Grant for Sam, When the Support agent opens this Day's day report in the admin console, Then its figures equal the eater's own (support lens, Grant diary read).

### K · Apple Health

#### eater-3.39 · Each Confirmed Entry appears in Apple Health as a food
As the Eater, I find what I log in Apple Health as one food with its energy and macros, and it disappears when I Void it, so that my other health apps see the same diary. · map row "Ledger → HealthKit: write the entry as a food correlation … delete on void", WF-3 done-when ("appears in Apple Health and disappears on void"), FR-016 · P29, C12, C56
- `/r` Given Sam turned on Settings → Activity → "Write meals to Apple Health" and allowed Dietary Energy, Protein, Carbohydrates and Total Fat in the iOS sheet, When "3 cheese bites" turns Confirmed, Then the simulator's Health app → Browse → Nutrition lists 138 kcal Dietary Energy from "Sips & Bytes" at the Entry's eaten time, with protein 7.5 g, carbohydrates 13.5 g and fat 6.0 g at the same time.
- `/s` Given that write, When the HealthKit store is queried in a system test, Then it holds one food correlation with the four samples (P29), and the Entry stores the correlation and sample ids.
- `/r` Given the calorie-only "kunafa slice", When it is written, Then Health shows 350 kcal and no protein, carbohydrate or fat sample — unknown is never written as 0 g.
- `/r` Given an Entry is Pending offline, When Health is viewed, Then it has no sample yet; after the Entry turns Confirmed the sample appears once (Conflicts item 4).
- `/r` Given that Entry is in Health, When Sam taps Undo on it or Voids it, Then the correlation disappears from the Health app.

#### eater-3.40 · Health is asked once, when it matters; saying no keeps logging
As the Eater, I'm offered "Write meals to Apple Health" as a quiet card after my first Confirmed Entry, so that I decide when it means something, and logging works the same if I say no. · FR-076 ("Refusal must preserve unaffected functions"), FRD §3.2 ("Goals, permissions, and health connections are separate choices"), map §6 ("Health write on/off"), map row Consent ("each Health type") · EX-26, P30, R7
- `/r` Given Sam never decided on Health, When his first Entry turns Confirmed, Then Today shows a card "Add your meals to Apple Health?" with "Turn on" and "Not now" inside the timeline (no pop-up), and "Turn on" opens the iOS sheet listing only the four nutrition types to write.
- `/r` Given Sam taps "Not now" or denies every type in the iOS sheet, When he logs 3 cheese bites, Then the Entry is logged as usual, nothing is written to Health, and Settings → Activity reads "Write meals to Apple Health: Off".
- `/r` Given Sam turns writing off later, When he logs, Then no new samples are written and his earlier samples stay in Health (`assumption`; Conflicts item 4).
- `/s` Given "Write meals to Apple Health" is on, When 20 Entries are written, Then the recorded requests to the API and to the analyzer adapter hold no value read from Health (R7; map rule "Health data never sent").

### L · For every eater

#### eater-3.41 · Logging works one-handed, at the largest text, with VoiceOver and in Arabic
As the Eater, I can log with one thumb, at the largest text size, with VoiceOver, with a keyboard and in mirrored Arabic, so that the fastest path is the same for everyone. · NFR-08 ("Screen-reader and text-scaling flows pass on every P0 journey"), FRD §14.1 (right-to-left, both numeral systems), FRD §14.2 · EX-33, EX-34, EX-36, EX-37, EX-38, EX-39, E34–E37, E40, E41
- `/r` Given the largest accessibility text size on the iPhone 16e simulator, When Today, the count stepper, the quick-add chips and the meal and day reports are opened in English and in Arabic, Then no text clips or overlaps in the screenshots and Log is reachable (by scrolling if needed) and never covered by the keyboard.
- `/r` Given VoiceOver, When focus reaches the "cheese bite" tile, Then it reads "cheese bite, last 3, includes 8 g bread each, button"; after Undo the "kcal remaining" figure is read with its new value.
- `/r` Given Arabic, When Today is shown, Then the layout is mirrored, back points right, macro bars fill from the right, «١٬٥٨٠» keeps its digit order, and a row mixing an English Unit name with an Arabic count keeps the count beside its food.
- `/r` Given light and dark appearance, When the "kcal remaining" figure and Entry text are measured on the served screen, Then contrast is at least 4.5:1 and the figure uses a heavier weight than body text.
- `/r` Given Full Keyboard Access, When the eater tabs through Today and the count stepper, Then every control shows a focus ring and can be used; given Reduce Motion, When a log, Undo or count change happens, Then nothing slides or bounces.

#### eater-3.42 · "Hide numbers" keeps logging
As the Eater, I can hide calorie and macro numbers and still log my Units, so that tracking helps me without numbers I find harmful. · map §6 ("'hide numbers' view", R37), FRD §11.4, FRD §14.2 · EX-43
- `/r` Given Settings → Goals → "Hide numbers" is on, When Mona logs «٣ × قرصة جبنة» from Today, Then the timeline shows «٣ قرصة جبنة» with no kcal, Today shows no "remaining" figure, and the meal and day reports show food names and coverage words only.
- `/r` Given that view, When the widget and the Siri snippet show a log, Then neither shows a number.
- `/s` Given that view, When `GET /v1/reports/day` is read with "Hide numbers" on and then off, Then both return the same Entries and totals — the view hides, it never changes the ledger.

#### eater-3.43 · Only eating changes my Day
As the Eater, I can save a Unit, keep a Plan, take a photo or drop an Analysis without anything being added to my Day, and confirming a Plan twice still adds one meal, so that my total holds only what I ate. · FR-045 ("Planned meals, calibration photos, and abandoned drafts shall contribute zero … prevent duplicate execution"), AT-13, AT-21, FRD §4.3, FR-039 ("Only authorized consume/correct/remove commands affect the ledger")
- `/r` Given Sam's Day at 290 kcal, When he saves a Unit from a scale photo, keeps a 600 kcal Plan Saved in Capture & Plan, and closes an Analysis so it is Discarded, Then Today still reads 290 consumed and `GET /v1/reports/day` returns consumed_kcal 290.
- `/r` Given a Saved Plan is Confirmed with "Ate as planned" and the confirmation is resent with the same command_id, When Today is read, Then the Plan's meal appears once (AT-21; the Plan flow itself is WF-5).
- `/s` Given a Saved Plan, When its consume command is read at `POST /v1/consumption`, Then it carries source_plan_id (FRD §18.1), and a second confirmation of the same Plan adds no Entry.

---

## 4 · Journey WF-6 · Correct history

Map: "correct, void, restore, move day; scope this entry or future default." Interaction row: "Eater → ledger: correct / void / restore / move day — Entry (supersedes) — expected revision; scope choice: this entry vs future default — effective entry replaced"; and "Ledger → HealthKit: rewrite on correction, delete on void". Done-when: "18 not 15" shows old, new and delta and replaces the effective Entry; yesterday's correction leaves today untouched.

| step | what the eater does | stories |
|---|---|---|
| A · find the Entry | open any Entry; say or type the correction | 6.1–6.2 |
| B · preview and replace | old, new, meal and Day change; replace, never add; Arabic voice; "add another" is not a correction; grams or the Unit; refusals; Undo | 6.3–6.9 |
| C · choose the scope | this Entry only or also my future default; chosen past Entries; every quick path logs the new version; a better source | 6.10–6.13 |
| D · Void and Restore | Void with Undo; see and Restore; "remove the laban" | 6.14–6.16 |
| E · move to another Day | 6.17 |
| F · late edits stay on their Day | yesterday after Start new day; period views and old Targets; the Day and zone in the preview | 6.18–6.20 |
| G · offline and two devices | correct offline; two iPhones changed one Entry; a Void meets a Correction | 6.21–6.23 |
| H · Apple Health follows | 6.24 |
| I · history, access | the Entry history replays to the Day; VoiceOver, large text, Arabic | 6.25–6.26 |

### A · Find the Entry

#### eater-6.1 · Open any Entry and see what it is made of and what happened to it
As the Eater, I open an Entry from any Day's timeline and see its Unit and version, its parts, its Evidence and source, and its history, with Correct and Void beside it, so that I can check a number before I change it. · FR-046 ("itemized timeline, source details, correction history"), FR-026, FRD §14.1 ("Includes 8 g bread", "Based on your saved recipe") · EX-04, EX-37
- `/r` Given Mona's Wed 30 Sep, When she taps «١٥ معلقة تلبينة» in Dinner, Then Entry details *(proposed)* show «معلقة تلبينة», version 1 · 16 g, the Evidence badge recipe-calculated, «٣٠٠» kcal, the time «٢١:١٠», one history row "Confirmed 21:10", and Correct and Void as visible buttons (labels in Arabic).
- `/r` Given Sam's "3 cheese bites" Entry, When its Entry details open, Then they read "Includes 5.4 g cheese · 1.5 g oil · 8 g bread each" and "Based on your saved unit, version 1".
- `/r` Given an Entry row in the timeline, When the eater swipes it, Then Correct and Void appear, and the same two actions are also buttons in Entry details (EX-37).
- `/r` Given that Entry, When `GET /v1/consumption/{id}/history` *(proposed)* is called over HTTP, Then it returns each event with operation, event_time, quantity and snapshot reference.

#### eater-6.2 · Say or type the correction; the app finds the Entry
As the Eater, I type or say "18 not 15" or "those biscuits were 10 g, not 25 g", so that the app opens the affected Entry instead of making me hunt for it. · FRD §2.6 ("opens the affected entry"), FR-039 (correct), FR-035, AT-26
- `/r` Given Mona's selected Day is Wed 30 Sep and its only Entry with 15 is «١٥ معلقة تلبينة», When she types "18 not 15" in quick-add, Then the correction preview for that Entry opens.
- `/r` Given a second Entry «١٥ معلقة فول» is added to 30 Sep, When she types «١٨ مش ١٥», Then one question lists both Entries and asks which she means, and nothing changes until she picks.
- `/r` Given no Entry on the selected Day has 15, When she types "18 not 15", Then the line "No entry with 15 on Wed 30 Sep — choose one" appears above that Day's Entries, and no food is added.
- `/r` Given Sam's Entry "plain biscuits · 25 g · 125 kcal" on 30 Sep is in view, When he types "those biscuits were 10 g, not 25 g", Then the correction preview for that Entry opens at 25 g → 10 g.

### B · Preview and replace

#### eater-6.3 · The preview shows old, new, the meal's change and the Day's change
As the Eater, I see before confirming the old and new quantity and calories, the meal difference and the Day difference, and what the correction applies to, so that I know exactly what it will do. · FRD §2.6 ("The correction preview shows old and new quantities, meal difference, daily difference, and whether the correction applies only to this entry or also creates a future default"), AT-06, FRD §18 (corrections return "old/new/delta and affected day projections") · EX-14, EX-35
- `/r` Given «١٥ معلقة تلبينة» (300 kcal) in Dinner on 30 Sep (Day 1,098 kcal, Target 1,870), When the correction to 18 is previewed, Then the correction preview shows old «١٥ · ٣٠٠», new «١٨ · ٣٦٠», Dinner 300 → 360 (60 more), Day 1,098 → 1,158 (60 more; 772 → 712 remaining), and "Applies to: this Entry only" (a change of count has no future default; a change of weight offers one, eater-6.10) (AT-06: 18 spoons = 360 kcal).
- `/r` Given those change lines, When they are read, Then each carries a word ("more" or "less") and a sign, not colour alone.
- `/r` Given the preview, When Mona taps Cancel or swipes it away, Then nothing changes and `GET /v1/reports/day` for 2026-09-30 returns the same revision.
- `/s` Given the previewed correction, When it is sent to `POST /v1/consumption/{id}/corrections`, Then the response's old, new and delta and the 30 Sep Day projection equal the preview's figures.

#### eater-6.4 · Confirming replaces the Entry; it never adds food
As the Eater, I confirm a correction and the Entry is replaced, so that "two, not three" leaves two in my Day — not five. · AT-11, FR-041 ("effective-entry projection with an auditable event history"), FR-042, FRD §2.6 ("Confirmation replaces the effective entry; it is not another positive food addition"), FRD §8.2
- `/r` Given Sam's Entry "3 foul spoons" (90 kcal), When he corrects it to 2 and confirms, Then the timeline lists "2 foul spoons · 60 kcal" once, the Day falls by 30 kcal, and no "3 foul spoons" Entry remains (AT-11).
- `/r` Given that Entry, When its Entry details open, Then the history reads "3 · Corrected" and "2 · Confirmed" with their times — the prior value is kept.
- `/s` Given that correction, When the ledger is read, Then a correction event whose supersedes field names the earlier event is appended, the effective-Entry projection holds one Entry of 2, and replaying the events gives the same Day total (FR-041, FR-042).
- `/r` Given that correction, When it is resent over HTTP with the same command_id, Then the Day falls by 30 once, not 60.

#### eater-6.5 · Arabic voice «١٨ مش ١٥» does what typed "18 not 15" does
As the Eater, I say «التلبينة كانت ١٨ مش ١٥» (or type «١٨ مو ١٥» in Gulf Arabic), so that my spoken Arabic correction does exactly what the typed English one does — and the number comes from my Unit, never from an AI re-think. · AT-26 ("Arabic '18, not 15' and mixed English-Arabic food names are transcribed and resolved as correction, not new consumption"), WF-4 done-when, FR-036, FR-039, FRD §16.2, FRD §16.5 · EX-39, E15, E29, E43, P15
- `/r` Given Mona's Entry «١٥ معلقة تلبينة», the microphone allowed and the Consent for Google's AI Given, When she says «التلبينة كانت ١٨ مش ١٥», Then the words appear editable in quick-add and the correction preview opens at 15 → 18 with 60 more kcal — no consumption chip appears (AT-26).
- `/r` Given Faisal has the same Entry, When he types «١٨ مو ١٥» (the Gulf negation, as Saudi reviewers write it, E15), Then the same correction preview opens.
- `/s` Given the words "18" or «١٨», When `POST /v1/analyses` parses them, Then both give quantity 18 (E43), the Analysis returns intent "correct" with the Entry reference and the quantity only, and the 360 kcal comes from the Entry's snapshot (20 kcal per spoon × 18) with no nutrition inference call.
- `/r` Given the words came back as «٨ مش ١٥», When the preview opens, Then the words stay editable and fixing them to «١٨» updates the preview before Confirm (FR-036).

#### eater-6.6 · "Add another 3" is new food; "make it 18" is a correction
As the Eater, I can say "add another 3 spoons" and get a new addition, or "make it 18" and get a correction, so that more food and a fixed number are never confused. · FRD §8.2 ("'Make that 18 spoons, not 15' replaces a quantity. 'Add another 3 spoons' adds consumption … A repeated photo does not prove repeated consumption"), FR-039
- `/r` Given Mona's «١٥ معلقة تلبينة», When she types «زوّد ٣ معالق تلبينة» and logs the chip, Then the timeline shows two Entries, 15 and 3, and the Day reads 1,158 kcal; the 15-spoon Entry is unchanged.
- `/r` Given the same Entry, When she types «خليها ١٨» instead, Then the correction preview opens at 15 → 18.
- `/r` Given Sam sends yesterday's plate photo again with "log this", When the Analysis is Ready for review, Then nothing is logged until he approves in Analysis review, and Analysis review notes that the same photo was used on Wed 30 Sep (the note is a proposal) — a repeated photo is not proof of eating.

#### eater-6.7 · Correct the grams, or the Unit I picked
As the Eater, I can correct the grams of a weighed food or swap a Unit picked by mistake, so that "the biscuits were 10 g" and "it was the foul with oil" are each one correction. · FRD §2.6 (biscuits example), FRD §18 (corrections "Replace quantity, unit, or diary day"), AT-08, FR-041
- `/r` Given Sam's Entry "plain biscuits · 25 g · 125 kcal" (label 500 kcal/100 g, label-verified), When he confirms 10 g in the correction preview, Then the preview had shown 125 → 50 kcal (75 less), and the timeline now reads "plain biscuits · 10 g · 50 kcal".
- `/r` Given Mona's «٤ معالق فول» (120 kcal) at Breakfast on 30 Sep, When she taps "Change unit" in the correction preview and picks «معلقة فول بالزيت», Then the preview shows 120 → 180 (60 more), and after Confirm one Entry «٤ معلقة فول بالزيت» remains.
- `/s` Given the Unit swap, When `GET /v1/consumption/{id}/history` *(proposed)* is read, Then the correction event names the new unit_version_id and the earlier snapshot stays in the history.

#### eater-6.8 · A correction that cannot work says so beside the number
As the Eater, I'm told beside the number when a correction makes no sense, so that I fix it instead of guessing. · FR-041, FRD §18 ("corrections also include an expected entry version"), FRD §18.2 · EX-23, care group 4
- `/r` Given the correction preview, When the new quantity is 0, Then Confirm is disabled and the line reads "To remove this entry, use Void" with a Void button.
- `/r` Given the correction preview, When the new quantity equals the old one (15 → 15), Then Confirm is disabled with "No change".
- `/r` Given the Entry is Voided, When "18 not 15" points at it, Then the line reads "This entry is Voided — Restore it first" with Restore; a request over HTTP to correct a Voided Entry returns `VALIDATION_ERROR` and changes nothing.
- `/r` Given «١٥ معلقة تلبينة», When a request over HTTP to `POST /v1/consumption/{id}/corrections` has no expected entry version, Then it returns `VALIDATION_ERROR` and the Entry is unchanged.

#### eater-6.9 · Undo a correction
As the Eater, I tap Undo after a correction and get the earlier quantity back, with both steps kept in the history, so that a mistaken fix is as easy to reverse as a mistaken log. · FR-046 ("Undo for recent supported mutations") · EX-24
- `/r` Given Mona confirmed 15 → 18, When she taps Undo on the banner naming the correction, Then the Entry reads 15 again, the Day reads 1,098, and the history lists 15 Corrected, 18 Corrected, 15 Confirmed with their times.
- `/s` Given that Undo, When the Entry history is read over `GET /v1/consumption/{id}/history` *(proposed)*, Then the Undo is a new correction event back to the earlier snapshot — append-only; no earlier event is removed.
- `/r` Given the banner has closed, When Entry details open, Then "Undo" is still offered for this most recent change.

### C · Choose the scope

#### eater-6.10 · This Entry only, or also my future default
As the Eater, when I correct a weight ("my bread bite is 9 g now"), I choose whether it applies to this Entry only or also makes a new version of my Unit for future logs, so that one odd portion never changes my definition and a real change never rewrites my past. · FRD §2.6, FRD §8.2 ("'I changed my spoon weight' creates a future unit version"), FR-014, AT-12, FRD §5.1, FR-020, FRD §18 (`POST /v1/units/{id}/versions`: "prior logs unchanged")
- `/r` Given Mona's Unit «لقمة عيش» version 1 (8 g, 20 kcal) and Entries «٦ لقم عيش» today (Thu 1 Oct, 120 kcal) and on Wed 30 Sep (120 kcal), When she says «لقمة العيش بقت ٩ جرام» and the correction preview opens, Then it offers "This Entry only" and "This Entry and future logs", neither chosen, and Confirm stays disabled until she picks one.
- `/r` Given she picks "This Entry only", When she confirms, Then today's Entry reads 6 × 9 g · 135 kcal (15 more) and My Units still shows «لقمة عيش» at 8 g, version 1.
- `/r` Given she picks "This Entry and future logs", When she confirms, Then My Units shows «لقمة عيش» at 9 g, version 2, today's Entry reads 135 kcal, the 30 Sep Entry still reads 120 kcal on version 1, and her next «لقمة عيش» log uses 9 g (AT-12).
- `/r` Given the future-logs choice, When the preview is read before Confirm, Then it names the Units whose bread follows this rule ("cheese bite: future logs include 9 g bread") and those keeping their own exception ("meat bite: stays 5 g", FR-020).
- `/s` Given the future-logs choice, When it is confirmed, Then `POST /v1/units/{id}/versions` has created version 2 and every earlier Entry keeps unit_version_id version 1.

#### eater-6.11 · Apply a new measurement to past Entries I choose — only when I approve
As the Eater, I can also apply the new measurement to past Entries I pick, seeing each Day's change first, and nothing is recalculated unless I confirm, so that "apply that to yesterday's lunch" is possible and never automatic. · AT-12 ("Selected-history correction works only after approval"), FRD §8.2 ("'Apply that measurement to today's lunch' explicitly corrects selected entries"), FR-014, FR-031, FR-047
- `/r` Given version 2 of «لقمة عيش» exists, When Mona opens "Apply to past entries…" on the Unit in My Units, Then the Entries on version 1 are listed by Day with none ticked, and ticking one shows its Day's change (e.g. Wed 30 Sep: «٦ لقم عيش», 15 more kcal).
- `/r` Given that list, When she ticks only 30 Sep's Lunch Entry and confirms, Then 30 Sep's day report reads 1,113 kcal, Tue 29 Sep is unchanged, and the changed Entry's history names the 9 g measurement.
- `/r` Given that list, When she closes it without confirming, Then no past Entry changed and every Day's revision is the same.
- `/s` Given the confirmed batch, When the ledger is read, Then one correction event is written per ticked Entry and each affected Day's revision rises once.

#### eater-6.12 · After a new default, every quick path logs the new version
As the Eater, I tap my recent tile, a Template, Siri or the widget after changing a Unit and get the new version, so that the old value never sneaks back from a shortcut. · FR-014, FRD §17 EatingUnitVersion ("Latest approved version is used for new logs only"), map row "one idempotent consume command for every surface" · E21, EX-14
- `/r` Given «لقمة عيش» is version 2, When Mona logs it from the recent tile, from the widget and from the Shortcuts action, and logs the Template «فطار عادي» whose cheese bites take bread by rule, Then every new Entry's details read 9 g bread.
- `/r` Given 29 Sep's Breakfast logged on version 1, When she copies it to today, Then the copy uses version 2 and says so (eater-3.17).
- `/s` Given iPhone B, offline, logs «لقمة عيش» naming version 1 after iPhone A created version 2, When B syncs, Then the Entry is stored on version 1 as sent and its details offer "Use version 2" as a one-tap correction (a proposal; Conflicts item 6).

#### eater-6.13 · A better source never rewrites my past by itself
As the Eater, I keep my past Days exactly as they were when the reference data behind a Unit improves, and I choose whether to apply it to Entries I pick, so that nothing moves that I did not move. · FR-031 ("require explicit scope selection before recalculating historical entries"), FRD §1.4, FRD §17.2 ("Log events capture nutrient snapshots"), D2 Food states (Approved → Superseded) · E16, EX-14 · **Shared: Eater · Nutrition approver · Platform admin**
- `/r` Given the Nutrition approver approves a new version of the Food behind Mona's «معلقة فول» (the old one becomes Superseded; approver-10.28), When Mona opens Progress for the week and the Day Wed 30 Sep, Then every total is unchanged.
- `/r` Given that approval, When she opens «معلقة فول» in My Units, Then a quiet line says a newer source is available and past Entries are unchanged, with "Use for future logs" and "Apply to past entries…" (eater-6.11).
- `/s` Given the Platform admin moves a Registry version to Rollout and then to Rolled back, When the ledger and Day projections are compared before and after, Then they are identical.

### D · Void and Restore

#### eater-6.14 · Void an Entry, with Undo instead of a warning
As the Eater, I Void an Entry with one action and get Undo instead of "Are you sure?", so that removing a mistake is quick and reversible. · FR-041, FRD §18 (void: "Retrying does not subtract twice"), map row "delete on void" · EX-15, EX-24, C10
- `/r` Given Sam's Entry "1 cup of laban" (152 kcal), When he taps Void in its Entry details, Then the Entry leaves the timeline with no dialog, the banner reads "Voided 1 cup of laban · Undo", and "kcal remaining" rises by 152.
- `/r` Given that Void, When it is resent over HTTP with the same command_id, Then the Day rises by 152 once, not 304.
- `/r` Given any Entry, When its Entry details open, Then Correct is the first button and Void is styled as secondary — Void is never the main action.
- `/s` Given that Void, When the ledger is read, Then a void event is appended and the Entry's earlier events stay in its history.

#### eater-6.15 · See Voided Entries and Restore one
As the Eater, I can see what I Voided on a Day and Restore it, so that nothing I removed is lost for good — unlike a tracker whose undo lasts about 30 seconds (C10, weak: from a competitor blog). · FR-041 ("create, correct, void, restore"), FR-046, map vocabulary "Restore"
- `/r` Given 30 Sep has one Voided Entry, When the Day's timeline is viewed, Then a collapsed line reads "Voided (1)"; opening it shows "1 cup of laban · Voided 08:52" with Restore.
- `/r` Given that line, When Restore is tapped, Then the Entry returns to Breakfast with its 152 kcal snapshot and the Day rises by 152; resending `POST /v1/consumption/{id}/restore` *(proposed)* with the same command_id leaves it counted once.
- `/r` Given that Restored Entry, When its Entry details open, Then its history reads Confirmed, Voided, Restored with their times.

#### eater-6.16 · "Remove the laban" opens a Void for me to confirm
As the Eater, I can type «احذف اللبن» or "remove the laban", so that the app finds the Entry and I confirm the Void myself. · FR-039 (remove: "Only authorized consume/correct/remove commands affect the ledger"), FRD §16.2 ("It may not … delete history")
- `/r` Given Faisal's Day has one «كوب لبن» Entry, When he types «احذف اللبن», Then Entry details open on that Entry showing 152 kcal less for the meal and for the Day if Voided, and nothing changes until he taps Void.
- `/r` Given two «كوب لبن» Entries that Day, When he types «احذف اللبن», Then one question asks which one.
- `/s` Given the Analysis returns intent "remove", When the ledger is read before Faisal taps Void, Then the analyzer has changed nothing; only the app's void command, sent after Faisal's tap, changes the ledger.

### E · Move to another Day

#### eater-6.17 · Move an Entry to the Day it belongs to
As the Eater, I move an Entry to another Day, see both Days' change first, and both reports update, so that a meal logged on the wrong Day is fixed in one step. · FR-041 ("move-to-day"), FRD §18 (corrections replace "diary day"), FR-047, FR-071, FRD §8.1
- `/r` Given Sam's "1 baladi loaf" (230 kcal) at 00:20 sits on Thu 1 Oct (his boundary is 00:00), When he chooses "Move to another Day" → Wed 30 Sep in Entry details, Then the preview reads "Wed 30 Sep: 230 more · Thu 1 Oct: 230 less", and after Confirm 30 Sep's day report includes it and 1 Oct's does not.
- `/r` Given the move, When the Entry is opened on Day 30 Sep, Then it keeps its eaten time and shows "00:20 (1 Oct)" on Day 30 Sep, unless Sam also edits the time.
- `/s` Given the move, When the ledger is read, Then one correction event changes diary_day_id, each Day's revision rises once, and each day report uses its own Target version (FR-071).
- `/r` Given "Move to another Day", When its Day picker opens, Then Days after the current Day are not offered (a proposal; Conflicts item 9).

### F · Late edits stay on their Day

#### eater-6.18 · Correct yesterday after starting a new day; today stays apart
As the Eater, I correct yesterday's lunch after I have started today, and only yesterday changes, so that a late fix never leaks into today. · AT-14 ("User starts a new day, then corrects yesterday's lunch. Yesterday updates; new day remains separate"), FR-044, FR-047, WF-6 done-when
- `/r` Given Mona used Start new day at 02:00 and Today (Thu 1 Oct) shows 318 kcal, When she corrects Wed 30 Sep's Lunch «٦ لقم عيش» to 4 (120 → 80 kcal), Then 30 Sep's day report reads 1,058 kcal and Today still reads 318 kcal with the same "remaining" figure (AT-14).
- `/r` Given that correction, When its correction preview opens, Then its Day line names Wednesday 30 September, not today.
- `/s` Given that correction, When `GET /v1/reports/day` for 2026-10-01 is read before and after it, Then consumed and revision are unchanged; for 2026-09-30 the revision is one higher.

#### eater-6.19 · Late edits reach the period views; old Targets stay
As the Eater, I see a late correction in last week's view and an old Target unchanged, so that my history is both corrected and honest. · FR-047 ("update the relevant historical day and cumulative report"), FR-071, FR-072, FR-061 ("Never retroactively alter historical targets")
- `/r` Given Progress → 7 days (24–30 Sep) shows Wed 30 Sep at 1,098 kcal, When the 15 → 18 talbina correction is confirmed, Then the view shows 30 Sep at 1,158 and the week's total 60 kcal higher.
- `/r` Given Mona's Target was 1,900 until 15 Sep and 1,870 after, When she corrects an Entry on 10 Sep, Then 10 Sep's day report compares against 1,900.
- `/s` Given that correction, When `GET /v1/reports/period` for 2026-09-24 to 2026-09-30 is called, Then it returns the corrected 30 Sep and each Day's effective Target.

#### eater-6.20 · The preview names the Day and its time zone
As the Eater, I see in the correction preview which Day and time zone the Entry belongs to, so that travel or a late boundary never makes me fix the wrong Day. · FRD §8.1, FR-044 ("The selected day and time zone remain visible") · EX-07
- `/r` Given Faisal's lunch on Thu 1 Oct was captured in Asia/Riyadh and his device is now in Europe/London, When he opens the correction preview for it, Then it reads "Thu 1 Oct · 13:00 Riyadh time" and its change lines name that Day.
- `/r` Given Mona's 00:20 Entry belongs to Wed 30 Sep by her 03:00 boundary, When she opens its correction preview, Then it names Wednesday 30 September and shows the time as 00:20 on 1 Oct.

### G · Offline and two devices

#### eater-6.21 · Correct with no signal; it waits as Pending and applies once
As the Eater, I correct or Void an Entry with no signal and see the change marked Pending, so that a fix made offline is applied exactly once when the signal returns. · FRD §8.3 ("Each command has a UUID and expected revision"), NFR-06, AT-10, FRD §18
- `/r` Given Mona is offline, When she corrects «١٥ معلقة تلبينة» to 18, Then the Entry reads «١٨» with the Pending mark, and the Day shows the 60 kcal change as Pending beside the Confirmed figure, which still counts 15.
- `/r` Given the simulator reconnects, When the outbox sends the correction with expected entry version 1, Then the Entry reads 18 with no mark and `GET /v1/reports/day` for 2026-09-30 returns 1,158 once.
- `/s` Given that correction, When the app is killed during the send and resends with the same command_id, Then the correction is applied once (60 more, not 120).

#### eater-6.22 · Two iPhones changed one Entry: I choose, it never counts twice
As the Eater, when my two iPhones changed the same Entry while offline, I see both values side by side and choose, so that calories are never added twice and nothing is overwritten behind my back. · AT-31 ("Two devices edit the same entry offline. Server detects stale revision and offers reconciliation; calories are not added twice"), FRD §8.3, FRD §17.2, FRD §18.2 ("A 409 conflict returns the current revision") · **Shared: Eater · Support agent**
- `/s` Given «١٥ معلقة تلبينة» at revision 1, iPhone A corrects it to 18 offline and iPhone B corrects it to 12 offline, When A syncs and then B, Then A's correction is Confirmed (revision 2) and B's returns 409 `STALE_REVISION` with revision 2 (18 spoons).
- `/r` Given B received that 409, When Mona opens Today on B, Then the Entry says it changed on another device, and the choice shows "On your other iPhone at 21:40: 18 spoons · 360 kcal" and "On this iPhone: 12 spoons · 240 kcal", with "Keep 18" and "Use 12".
- `/r` Given that choice, When she picks "Use 12", Then a correction with expected revision 2 is sent, both iPhones show 12 (240 kcal), and Wed 30 Sep reads 1,038 kcal — the Entry counted once.
- `/r` Given she has not chosen yet, When Today on either iPhone is read, Then the Day counts the Confirmed 18 and the Entry is marked "Needs your choice"; the Support agent's Jobs → Sync row shows the conflict without food, quantities or kcal (support lens).
- `/s` Given both iPhones also logged new food offline, When they sync, Then those consume commands are Confirmed without a conflict (eater-3.26).

#### eater-6.23 · A Void meets a Correction
As the Eater, when one iPhone Voided an Entry and the other corrected it, I'm asked which I meant, so that the Entry neither comes back nor vanishes without me. · AT-31, FR-041, FRD §18.2
- `/s` Given iPhone A Voids «٤ معالق فول» (revision 1 → 2) and iPhone B, offline, corrects it to 3 with expected revision 1, When B syncs, Then 409 `STALE_REVISION` returns with the current state Voided.
- `/r` Given that 409, When Mona opens Today on B, Then the Entry says it was Voided on her other iPhone, with "Keep it Voided" and "Restore with 3"; picking "Restore with 3" sends a Restore and then a correction to 3, and Wed 30 Sep counts 3 spoons once (1,068 kcal).

### H · Apple Health follows

#### eater-6.24 · Apple Health follows every Correction, Void, Restore and move
As the Eater, I see Apple Health match my diary after I correct, Void, Restore or move an Entry, so that Health never keeps a number I fixed. · map row "Ledger → HealthKit: rewrite on correction, delete on void", WF-3 done-when, FR-047 · P29 ("Correlations are immutable", r1-refute-b), EX-26
- `/r` Given "Write meals to Apple Health" is on and «١٥ معلقة تلبينة» was written as 300 kcal at 21:10 on 30 Sep, When the correction to 18 turns Confirmed, Then the simulator's Health app shows 360 kcal at 21:10 on 30 Sep from "Sips & Bytes" and no 300 kcal sample remains.
- `/s` Given that correction, When the HealthKit store is queried in a system test, Then the old food correlation was deleted and a new one written (correlations cannot be edited, P29), and the Entry's stored sample ids were replaced.
- `/r` Given that Entry, When it is Voided, Then its correlation disappears from the Health app; When it is then Restored, Then one correlation with the restored values reappears.
- `/r` Given an Entry in Health, When it is moved to another Day without changing its eaten time, Then the Health app shows it unchanged (Health has no Days); When its eaten time is changed, Then the sample appears at the new time and not at the old one.
- `/r` Given "Write meals to Apple Health" is off or iOS denied it, When Entries are corrected, Voided or Restored, Then the ledger and Today change as usual and nothing is written to Health.

### I · History and access

#### eater-6.25 · The Entry history is mine to read, and replays to the Day
As the Eater, I can read every change to an Entry — what, when and from which device — and trust that the Day can be rebuilt from it, so that "zero unexplained discrepancies" is something I can see. · FR-041 ("auditable event history"), FR-042, FR-046, NFR-01, brief §1.5, FRD §19.2 · EX-28, EX-29 · **Shared: Eater · Support agent**
- `/r` Given an Entry that was logged, corrected, Voided and Restored, When its Entry details → History opens, Then it lists, with times and the device: "15 spoons · 300 kcal · Confirmed 21:10 · Corrected 21:40" and "18 spoons · 360 kcal · Confirmed 21:40 · Voided 22:05 · Restored 22:06".
- `/s` Given only the 30 Sep events, When the Day is replayed in a test, Then its totals equal `GET /v1/reports/day` for 2026-09-30 exactly.
- `/s` Given these four commands, When the operational logs they wrote are searched, Then they hold request ids, Entry ids, operation names and validation codes, and no food names, quantities or kcal (FRD §19.2).
- `/r` Given an Active Grant for Mona, When the Support agent opens this Day (read-only) in the admin console, Then they see the same history, and their correct, void and restore calls are refused (support lens).

#### eater-6.26 · Correcting works with VoiceOver, large text and in Arabic
As the Eater, I can correct an Entry with VoiceOver, at the largest text size and in Arabic, so that fixing history is as accessible as logging. · NFR-08, FRD §14.1, FRD §14.2 · EX-33, EX-35, EX-36, EX-39, E40, E41
- `/r` Given VoiceOver, When the correction preview opens, Then it reads "Talbina spoon. Old: 15, 300 kcal. New: 18, 360 kcal. Dinner: 60 more. Day: 60 more. Applies to this Entry only. Confirm, button."
- `/r` Given the largest text size in Arabic, When the correction preview opens, Then the old, new and change lines wrap without clipping, the arrow runs from the old value toward the new one in reading direction (leftward), and every number keeps its digit order.
- `/r` Given Mona's numerals are set to Western, When the correction preview opens in Arabic, Then every number in it shows Western digits inside the Arabic layout (EX-39).

---

## 5 · Stories shared with another persona

| story | shared with | why |
|---|---|---|
| eater-3.7 | Platform admin | a Registry rollout never changes a Saved Unit's numbers or a past Day (admin lens: "rollouts and rollbacks … leave existing Entries, Units and Days exactly as they were") |
| eater-3.10 | Nutrition approver | «لبن» resolves by the approver's dialect-tagged Alias unless the eater's own Unit matches (approver-10.42) |
| eater-3.14 | Platform admin | Kill switch On and per-user AI quota must never stop recent-Unit, Template or copy logging (admin lens: "AI being off never blocking food logging"; quota answer) |
| eater-3.38 | Support agent | a Support agent's Grant read shows the same day report figures as the eater's |
| eater-6.13 | Nutrition approver · Platform admin | a new approved Food version (approver-10.28) or a Registry change never rewrites past Entries (FR-031) |
| eater-6.22 | Support agent | the Support agent sees the `STALE_REVISION` row and waits for the eater's choice; only the eater resolves it |
| eater-6.25 | Support agent | the Entry history is what a Support agent reads inside an Active Grant, read-only |

---

## 6 · Conflicts for the model phase

Tensions with other personas or inside the model, for the model phase — never for the owner.

1. **"Meal" has no word in the vocabulary** (research conflict 2). FR-069's meal report, "copy a meal", Template-from-a-meal (3.16–3.20) and Ramadan names (3.29) all need it. Proposal: each Entry carries a meal name (default by time of day, editable, custom names allowed); a meal = the Entries with one name on one Day; the "addition" report of FR-069 = the Entries of one command. Name it once, in both languages, by a delta.
2. **The headline when some Entries are Pending** (research conflict 7; D2 "the day total shows Pending separately"). This file shows "remaining" including Pending, with the Pending kcal and the Confirmed figure beside it (3.25, 6.21). Confirm, or show Confirmed first.
3. **`expected_day_revision` on a consume command** (FRD §18.1). If it were enforced, two iPhones' offline additions would conflict. This file accepts consume commands whatever the Day's revision (additions commute) and enforces the expected **entry** version only on correct, void, restore and move (3.26, 6.22).
4. **Apple Health: when, and from which device.** The map writes "Ledger → HealthKit", so this file writes only Confirmed Entries (Pending Entries are not in Health, 3.39). Open: which iPhone rewrites a correlation another iPhone wrote; whether a sample the eater deleted in the Health app is re-written on a correction; whether turning writing off removes earlier samples (3.40, `assumption`).
5. **The near-duplicate window** (3.24) is not among the map's Policy values (§6). Is it a Nutrition approver Policy value or a fixed product value? 10 minutes here is an `assumption`.
6. **Latest version vs "the same number as last time."** Copies and Templates log the latest Unit version (FRD §17, E21) while the eater also trusts "same food, same number" (E15). This file uses the latest version with a visible "changed since" note (3.17). Also: an offline command naming a Superseded Unit version is stored as sent with a one-tap update (6.12) — confirm, or have the server map it to the latest.
7. **Siri carries one parameter** (P22): "three cheese bites and a cup of laban" cannot be one Siri phrase; Siri logs one Unit at its last count (3.21); Arabic Siri phrases are unverified. Decide whether a Siri phrase may hand a free sentence to quick-add.
8. **An unanswered device conflict** (6.22, eater vs Support agent): this file keeps the Confirmed value until the eater chooses, with no automatic merge; the Support agent only sees the row. Decide whether the choice ever expires.
9. **Moving an Entry** (6.17): this file keeps eaten_at unless the eater edits it, and does not offer Days after the current Day.
10. **Start new day vs the automatic boundary** (3.32): a manual start creates the next Day early; the automatic boundary then creates no second Day for the same date.
11. **Who assigns the Day.** The consume command carries diary_day_id (FRD §18.1), so the device assigns it. This file has the server store it as sent, so a boundary setting that syncs late between two iPhones cannot move an Entry; set a validation rule (e.g. diary_day_id within one Day of eaten_at's local date).
12. **Retired or Superseded reference data** (6.13; approver lens conflict 7): this file only shows a quiet notice and a scoped "Apply to past entries…". Decide whether a Retired (defective) Food version warrants a stronger notice to affected eaters.
13. **Undo's reach.** (a) Undo of a command still in the outbox: removed from the outbox (it never reached the ledger) or sent and then Voided? (b) Undo after a multi-item log (chips, copy, Template) reverses every Entry of that command — this file reads the done-when "Undo removes exactly one entry" as "exactly what was logged" (3.5, 3.9, 3.20).
14. **Entry history vs the Audit trail.** D2 makes "Audit trail" the only name for "who did what". The eater's per-Entry ledger history (FR-041, FR-046) is a different record; this file calls it the Entry's "history". Confirm the two names.
15. **Names this file needs that no list holds** (D2: added by a dated delta first): **Day picker** (FRD §14's "selected diary date" control), **Entry details** (FR-046's "source details, correction history"), the **Templates** list in My Units, **Unarchive** (taking a Unit out of Archived; "Restore" stays an Entry word), and Settings placements: diary-day boundary and "Ramadan days" in Units & language; One-tap logging in Food rules; Hide numbers in Goals; "Write meals to Apple Health" in Activity; "Show kcal remaining on widgets" in Privacy. Endpoints *(proposed)*: restore, history, templates, days, settings.
16. **An Archived Unit logged offline** (3.8): this file keeps the Entry (the food was eaten) and offers "Unarchive unit"; the alternative is to refuse it and ask the eater to log again.
17. **AI quota vs free repeat logging** (research conflict 9): enforced here by 3.14 — a recent-Unit, Template or copy log never uses the quota.

## 7 · Assumptions this file leaves open

- The Undo banner's length without VoiceOver; the near-duplicate window (10 minutes); the "Ramadan days" boundary hour (12:00); Sam's 00:00 and Mona's 03:00 boundaries are fixture choices, not defaults (EX-20).
- That the count parser in the nutrition core normalises number words and digits after the AI parse and for the offline Unit-name match (3.10, 3.14).
- App Shortcut phrases with Siri in Arabic (P22).
- HealthKit behaviour across two iPhones and after the eater deletes a sample in the Health app (Conflicts item 4).
- The widget shows three recent Units.

---

## 8 · Coverage: every dispatched FRD line → stories

| line | what it requires | stories |
|---|---|---|
| FR-014 | editing a Unit creates a version; logs keep theirs unless the eater corrects selected Entries | 3.17, 6.10, 6.11, 6.12 |
| FR-031 | source improvements need explicit scope before recalculating history | 3.7, 6.11, 6.13 |
| FR-039 | intents: estimate, calibrate, plan, consume, correct, remove, report, start a new day; only consume/correct/remove touch the ledger | 3.13, 3.43, 6.2, 6.5, 6.6, 6.16, 3.32 |
| FR-040 | immutable consumption event with entry id, user, eaten time, zone, Day, snapshot, quantity, sources, idempotency key | 3.3, 3.23, 3.31 |
| FR-041 | create, correct, void, restore, move-to-day via an effective-Entry projection with history | 6.4, 6.7, 6.14, 6.15, 6.17, 6.25 |
| FR-042 | Entry, Day revision and projection in one transaction; replay reproduces totals | 3.38, 6.4, 6.25 |
| FR-043 | retries never add twice; near-duplicates warn, never discard | 3.23, 3.24, 3.26 |
| FR-044 | Start new day never deletes; selected Day and zone visible | 3.1, 3.28, 3.31, 3.32, 3.33, 6.18, 6.20 |
| FR-045 | Plans, calibration photos, abandoned drafts add zero; Plan confirmation once | 3.9, 3.13, 3.43 |
| FR-046 | timeline, source details, correction history, Undo | 3.5, 6.1, 6.9, 6.15, 6.25 |
| FR-047 | late edits update the historical Day and cumulative report | 3.33, 6.11, 6.17, 6.18, 6.19 |
| FR-069 | addition/meal report and day report after every entry, kcal, grams, 4/4/9 kcal, % | 3.34, 3.35 |
| FR-070 | target, consumed, remaining/over, source confidence, range; reconciles with the ledger | 3.1, 3.34, 3.36, 3.37, 3.38 |
| FRD §2.3 | saved Units resolved and expanded; quick confirmation; opt-in one-tap with Undo; no new AI estimate | 3.6, 3.7, 3.9, 3.10 |
| FRD §2.6 | correction preview: old, new, meal and Day difference, scope; replaces, never adds | 6.2, 6.3, 6.4, 6.7, 6.10 |
| FRD §8.1 | configured boundary, custom boundary, UTC + capture zone, stable Day; travel and daylight saving; manual switch reinterprets nothing | 3.28, 3.29, 3.30, 3.31, 3.32, 6.17, 6.20 |
| FRD §8.2 | replace vs add; future Unit version; explicit correction of selected Entries; repeated photo | 3.13, 6.6, 6.10, 6.11 |
| FRD §8.3 | durable outbox; UUID and expected revision; accepted once or conflict; Pending vs Confirmed | 3.25, 3.26, 3.27, 6.21, 6.22 |
| AT-10 | one command delivered three times → one Entry | 3.16, 3.23, 3.26, 6.21 |
| AT-11 | three spoons → two: total includes two, audit keeps three | 6.4 |
| AT-12 | 8 g → 9 g: yesterday stays, future uses 9 g, selected history only after approval | 3.17, 6.10, 6.11 |
| AT-13 | "calculate and save my bite" saves a Unit; Day unchanged | 3.13, 3.43 |
| AT-14 | Start new day, then correct yesterday's lunch: yesterday updates, new Day separate | 3.32, 6.18 |
| AT-15 | source kcal ≠ 4/4/9: headline keeps source; shares sum to 100 %; gap explained | 3.35 |
| AT-16 | calorie-only food: kcal rise; macro coverage incomplete; no invented grams | 3.15, 3.36 |
| AT-26 | Arabic "18, not 15" and mixed names → a correction, not consumption | 6.2, 6.5 |
| AT-31 | two devices edit offline: stale revision detected, reconciliation offered, never added twice | 6.22, 6.23 |
| WF-3 done-when | ≤2 taps; copy yesterday's breakfast = one meal; reports reconcile; Undo exactly one; offline syncs once; Health appears and disappears on Void | 3.3, 3.16, 3.34, 3.38, 3.5, 3.26, 3.39 |
| WF-6 done-when | "18 not 15" shows old, new, delta and replaces; yesterday's correction leaves today untouched | 6.3, 6.4, 6.5, 6.18 |
| map row: Siri, widget, one idempotent consume command | | 3.21, 3.22, 3.23 |
| map row: Ledger → HealthKit (write, rewrite on correction, delete on void) | | 3.39, 3.40, 6.24 |
| Also used | FR-009, FR-016, FR-036, FR-076, FR-071, AT-06, AT-08, AT-21, AT-32, NFR-01, NFR-02, NFR-05, NFR-06, NFR-08, FRD §7.2, §10.1–10.2, §13.1, §14, §16.5, §18 | as cited in each story |

**Count:** 69 stories (43 in WF-3, 26 in WF-6).

---

## Lens verdict (2026-10-01)

**fail**: 16 defects.

The verifier did not write this lens. It was checked against `way/blueprint.md` §0–§1, `way/vocabulary.md` (delta D2, binding), `way/brief/frd-v1.0.md`, `way/personas/_lens-brief.md`, care.md ("The questions", "By size"), `way/research/r1-*.md` with both refutations, and `way/personas/eater/research.md`, which was read as context and is verified separately.

These parts hold:
- The count is right: 69 stories (43 in WF-3, 26 in WF-6) and 256 acceptance lines (201 `/r`, 51 `/s`, 4 `/m`). Every story has a `/r` line. Every id is `eater-<WF>.<n>`, numbered without gaps.
- Every story traces to the map or the FRD. These all have stories:
  - every WF-3 and WF-6 step in map §4, and both done-when clauses in §5;
  - FR-039 to FR-043, FR-045 to FR-047, FR-069, FR-070, FR-014, FR-031 and §2.3.
- The fixture arithmetic reconciles everywhere it was checked:
  - the Day totals: 290 and 1,580; 1,098 and 772;
  - 4/4/9 for every Unit;
  - the largest-remainder shares: 33.4/33.3/33.3, 8.1/48.4/43.5 and 21.4/35.2/43.4;
  - every correction delta from 6.3 to 6.23.
- Every cycle-1 finding the file cites stands in the refutations: C5, C12, C15, C23, C34, C35, C45, C55, C56, F27, P15, P22–P24, P28–P30, P34, P36, R3, R7 and R37. Only the parts the refutations kept are used.
  - C26, C27, C46, C54 and the dropped parts of C4 appear only in the line that says they are left out.
  - The gap on Siri phrases in Arabic (P22) is labelled `assumption`.
- Every name marked *(proposed)* is listed in Conflicts item 15: Day picker, Entry details, the Templates list, Unarchive, the Settings placements and the five endpoints.
- Every shared story is marked, and the other lens files have the matching stories:
  - approver-10.28 and approver-10.42;
  - the admin lens's "Rollouts and roll backs … exactly as they were" and "the kill switch never blocking food logging";
  - the support lens's Jobs → Sync row.

### Defects

1. **FR-044, the time zone · complete. The coverage table is not true here.** FR-044 says: "The selected day and time zone remain visible." eater-3.1 says the opposite for the usual case: "with the device back in Asia/Riyadh the header names no zone". After Start new day, eater-3.32 shows only "Thu 1 Oct, empty". So the coverage row "FR-044 · … selected Day and zone visible" claims more than the stories give. Either show the zone (in the header or the Day picker), or record the narrowing in the Conflicts list.

2. **AT-26, mixed names in a correction · complete. The coverage table is not true here.** AT-26 says: "Arabic '18, not 15' and mixed English-Arabic food names are transcribed and resolved as correction, not new consumption." The coverage row points to 6.2 and 6.5. Neither has a mixed name:
   - 6.2 uses "18 not 15" and «١٨ مش ١٥», with no food name;
   - 6.5 uses Arabic only: «التلبينة كانت ١٨ مش ١٥» and «١٨ مو ١٥».
   Mixed names appear only as consumption (3.10: "ضيف ٣ cheese bites و cup laban"). No line takes a correction that names its food in mixed English and Arabic and resolves it as a correction.

3. **FRD §8.2, "Apply that measurement to today's lunch" · complete.** §8.2 defines four phrases. Three have a typed or spoken acceptance line: "18 not 15" (6.5), "add another 3" (6.6) and "I changed my spoon weight" (6.10, «لقمة العيش بقت ٩ جرام»). The fourth has none. eater-6.11 promises "so that 'apply that to yesterday's lunch' is possible", but its lines only open My Units → "Apply to past entries…" and tick a list. No line says what typing or saying "apply that measurement to today's lunch" does. And no line covers today's lunch at all; 6.11 tests 30 Sep.

4. **FRD §2.6, the biscuits preview · complete.** §2.6 uses the biscuits example for the full preview: "old and new quantities, meal difference, daily difference, and whether the correction applies only to this entry or also creates a future default." The file shows that example only partly:
   - eater-6.7 shows "125 → 50 kcal (75 less)", with no meal line, no Day line and no scope line;
   - 6.2 shows only "25 g → 10 g".
   This also breaks the file's own rule in 6.3: "a change of count has no future default; a change of weight offers one". 25 g → 10 g is a change of weight.

5. **Step C, slow and timed-out sentences (AT-32, NFR-03) · complete.** eater-3.14 cites AT-32: "AI times out. Recent units and manual logging still work; pending analysis is not reported as consumed". Its lines cover the Kill switch, the quota, a withdrawn Consent, and "50 recent-Unit logs" while a mock analyzer times out. No line covers a sentence typed or spoken into quick-add while its Analysis is Processing, or after it times out and becomes Failed. Two things are missing:
   - what the eater sees while it runs, and whether they can cancel (NFR-03; care group 4: "When a task takes long, does progress move honestly … and offer cancel?");
   - proof that nothing is consumed afterwards.
   D2's Analysis states Processing and Failed appear in no story.

6. **WF-6 step A, a spoken or typed correction when sentence reading is unavailable · complete.** 6.2 and 6.5 depend on sentence reading. No line says what typing or saying "18 not 15" does in any of these cases:
   - the Kill switch is On;
   - the AI quota is used up;
   - the Consent for Google's AI is Withdrawn;
   - the phone is offline.
   3.14 and 3.25 cover these cases for logging only. FRD §7.2 says an AI outage "shall not block … ledger access". eater-6.21 ("When she corrects «١٥ معلقة تلبينة» to 18", offline) does not say how she makes the correction.

7. **WF-6, permission denied · complete.** FRD §18 asks for "ownership verification on every private operation", and NFR-07 and the lens brief's "permission denied" path ask for it to be tested. The file never tests these cases:
   - Mona's token with Sam's entry_id on `POST /v1/consumption/{id}/corrections`, `/void`, `/restore` *(proposed)* or `GET /v1/consumption/{id}/history` *(proposed)*;
   - a request with no token (`UNAUTHENTICATED`).
   Only 3.8 tests another eater's id, and only on consume.

8. **Editing an Entry's eaten time · traced and complete.** Three lines treat the eaten time as something the eater can already edit:
   - eater-6.17: "unless Sam also edits the time";
   - eater-6.24: "When its eaten time is changed, Then the sample appears at the new time";
   - eater-3.33: "the time can be changed before Log".
   No story defines this edit. Nothing says where it is offered, what its preview shows, which Day the Entry lands on when the new time crosses the boundary, or which API carries it. FRD §18 lists a correction as "Replace quantity, unit, or diary day" only.

9. **Step E, empty states · complete.** No line shows these screens when they have nothing in them:
   - the Templates list in My Units, and the Templates section of quick-add, before any Template exists;
   - "Copy from another Day" or "Copy this Day" (3.16, 3.18) on a Day or meal with no food Entries;
   - the Home Screen widget for an eater with no recent Units yet (3.22).
   The lens brief says "Include the unhappy paths: empty, …". Care group 4 asks: "What does this screen show when there is nothing in it yet, and does it say what to do next with the button to do it?"

10. **eater-6.15, C10 · sourced.** The line says: "unlike a tracker whose undo lasts about 30 seconds (C10, weak: from a competitor blog)". `r1-refute-a.md` puts this exact point on its list of high-impact assumptions (item 3: "C10's Undo window rests on a competitor blog") and says such points "may enter the records only with the label `assumption`". The line says "weak" instead. 3.5 and 6.14 also cite C10 without the label.

11. **eater-3.8, the wrong code for a refusal · vocabulary.** The line says: "names Sam's unit_version_id, Then it returns `UNIT_NOT_FOUND` exactly as for an id that never existed". D2 gives this case its own code: "`NOT_FOUND` (also for another user's ids — never reveal existence)". The other eater file `wf2-wf4.md` (line 139) answers a request for another eater's Unit with 404 `NOT_FOUND`. This file lists `NOT_FOUND` on line 14 but never uses it. So one refusal now has two codes, which is the problem D2 was written to fix.

12. **State names outside D2 · vocabulary.**
    - eater-3.21: the Siri snippet reads "Logged 3 cheese bites · waiting to send", while Today shows the same Entry as Pending. That is two words for one D2 state.
    - eater-6.22: "the Entry is marked 'Needs your choice'". D2 has no such Entry state (its states are Pending → Confirmed, Corrected, Voided → Restored), and Conflicts item 15 does not list it.
    - Conflicts item 6: "an offline command naming a Superseded Unit version". In D2, Superseded is a state of Food, Tier B recipe record, Alias and Policy versions, never of a Unit. A Unit's states are Draft → Saved (version n) → Archived.

13. **Four FRD names not marked proposed · vocabulary.** Line 12 calls these "FRD names used as written": the **quick-add** control, the **count stepper**, the **correction preview** and the Day's **timeline**. None is in §1 ¶4 or in D2; D2's Places list names Analysis review, Unit editor, Meal planner, Meal review and Settings. Conflicts item 15 does not list them either. D2 says: "A word not here and not in §1 ¶4 is added by a dated delta first." The file already marks two other names taken from the FRD as proposed (Day picker, Entry details). Treat these four the same way.

14. **Lines that cannot be observed as written · observable.**
    - eater-3.5: "with VoiceOver off its length is a value chosen on the served screen (care group 3; `assumption` until then)". There is no value to check.
    - eater-3.12: "Given an Analysis with three unclear words". It names no words and no eater, so it cannot be seeded.
    - eater-3.29: "Then new Entries follow his 03:00 boundary from the next Day". It names no Entry time, no Day and no screen. It also does not say where the last Day under the 12:00 boundary ends once the 03:00 boundary returns.
    - eater-3.31: "Then the Entry takes the device's current zone without a question". It names no screen or field where the zone can be read.

15. **eater-3.20, the tap count · observable.** The line says: "When Mona taps it in quick-add … tapping Log … — two taps in the recorded walk". The walk starts inside quick-add. WF-3's done-when counts taps "from Today", and opening quick-add is itself a tap. The story's "so that 'usual breakfast, but two cheese bites today' is still two taps" cannot hold either: its own second line needs a − tap before Log.

16. **Matching style not checked · experience.** research.md Part 2 §4 sets these style rules, and no line in WF-3 or WF-6 checks them:
    - "Calm. No confetti, no streak flames, no sounds" and "Discreet. Nothing on screen or sound draws the family's attention to logging (E24)". No line checks that a log, an Undo or an over-target Day makes no sound and shows no celebration.
    - "Arabic text about 10% larger optically and with line height for dots and marks (E41)", and EX-33 ("Arabic gets its extra line height").
    - "Dark mode is a true dark (E28)".
    eater-3.41 checks text size, contrast, VoiceOver, the keyboard, Reduce Motion and mirroring, but none of these.
