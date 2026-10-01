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
| A · see the Day | open Today: which Day, its zone, what is left; the empty Day | 3.1–3.2 |
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

#### eater-3.1 · Read the Day and what is left at a glance
As the Eater, I open Today and see which Day I am on, its time zone when it differs from my phone's, and one large "left" figure above my Entries, so that I know at a glance how much is left on this Day. · FR-044, FR-070, FRD §14 Today ("selected diary date; intake/target/remaining"), map WF-3 · EX-01, EX-07, EX-11, EX-18, E19
- `/r` Given Sam's Thu 1 Oct (290 kcal, Target 1,870), When Today opens on the iPhone 16e simulator, Then the header reads "Thu 1 Oct", the largest number reads "1,580" labelled "kcal left", the line under it reads "290 eaten of 1,870", and the timeline lists "3 cheese bites" and "1 cup of laban", each food name before its kcal.
- `/r` Given Faisal's Day Thu 1 Oct was captured in Asia/Riyadh and the simulator's zone is then set to Europe/London, When Today opens, Then the header reads "Thu 1 Oct · Riyadh time"; with the device back in Asia/Riyadh the header names no zone.
- `/r` Given Sam was viewing Tue 29 Sep on Today two minutes ago, When the app is killed and relaunched, Then Today reopens on Tue 29 Sep with "Back to today" shown, and the first screenshot already shows that Day's Entries — never the empty Today.
- `/s` Given `GET /v1/reports/day?diary_day_id=2026-10-01` for Sam, Then it returns consumed_kcal 290, target_kcal 1870 and remaining_kcal 1580 — the figures Today shows.

#### eater-3.2 · An empty Today says what to do next, even before a Target
As the Eater, I see on an empty Day what to do next, with the buttons to do it, even before I have set a Target, so that my first log needs no setup. · FRD §3.2, FR-001, FRD §14 Today ("Empty"), map WF-1 ("Tracking works before a target exists") · EX-03, EX-19, E17
- `/r` Given a new eater with no Units, no Entries and no Target, When Today opens, Then it shows two buttons, "Log what you ate" (opens quick-add) and "Make your first unit" (opens the Unit editor), and no "kcal left" figure or "0 left" appears.
- `/r` Given that eater logs a 350 kcal calorie-only Entry (eater-3.15), When Today returns, Then it reads "350 eaten · No Target yet" with a "Set a Target" link to Settings → Goals, and still no "left" figure.
- `/r` Given the device language is Arabic, When the empty Today opens, Then the layout is mirrored and both buttons show their Arabic catalogue labels in the same places, reachable with one thumb.

### B · Tap a recent Unit

#### eater-3.3 · Log a recent Unit in two taps
As the Eater, I tap a recent Unit on Today and then Log, with the count I used last time already filled in, so that a habitual food is recorded before the tea cools. · WF-3 done-when ("≤2 taps from Today"), FRD §14.1 ("persist the last used unit"), FR-040, FRD §18.1, NFR-02, brief §1.5 · EX-02, EX-10, EX-13, E3, E25, E32, C34
- `/r` Given Sam's recent Units on Today include "cheese bite", last logged ×3, When Sam taps the "cheese bite" tile and then Log on the count stepper that opens at 3, Then one Entry "3 cheese bites · 138 kcal" appears in the timeline and "kcal left" drops by 138; the recorded walk counts two taps.
- `/r` Given that walk repeated 20 times on the iPhone 16e simulator, When the time from the Log tap to the new Entry being drawn is measured, Then the 95th percentile is ≤300 ms and no spinner or screen transition appears (NFR-02).
- `/r` Given the API is online, When Log sends `POST /v1/consumption` with {command_id (a UUID), diary_day_id "2026-10-01", eaten_at, items:[{unit_version_id of cheese bite version 1, count 3}], intent "consume"}, Then the response carries entry_id, meal totals 138 kcal, day totals and a day_revision one higher, within 2 s p95 (NFR-02).
- `/s` Given the accepted Entry, When it is read from the ledger, Then it holds entry_id, user_id, eaten_at in UTC, time zone "Europe/London", diary_day_id, the component snapshot (5.4 g cheese, 1.5 g oil, 8 g bread per bite), quantity 3, the source versions and the command_id (FR-040).

#### eater-3.4 · Set the count with one thumb, in my digits
As the Eater, I change the count with large − and + buttons or type it, including halves and Arabic-Indic digits, so that I can log "two and a half" with the hand that is not holding bread. · FR-009, FRD §14.1 ("both Arabic-Indic and Western numerals, decimal input"), NFR-08 · EX-17, EX-37, EX-39, E34, E35, E36, E41
- `/r` Given the count stepper for "cheese bite" open at 3, When the eater taps − once and + twice, Then the count reads 4 and the button reads "Log 4 cheese bites"; − and + each measure at least 44×44 pt and sit in the middle band of the screen.
- `/r` Given Mona's numerals are Arabic-Indic, When she types «٢٫٥» in the count field for «قرصة جبنة», Then the field shows «٢٫٥», the Log button (in Arabic) names «٢٫٥», and the Entry stores quantity 2.5; typing "2.5" on a Western keypad stores the same 2.5.
- `/r` Given the count field is emptied or set to 0, When the eater looks at Log, Then Log is disabled and "Enter how many" shows beside the field; the keypad offers no minus sign.
- `/r` Given a request over HTTP to `POST /v1/consumption` with count 0 or −2, Then it returns `VALIDATION_ERROR` naming the count, and the Day's revision is unchanged.

#### eater-3.5 · Undo exactly what I logged
As the Eater, I tap Undo on the banner that names what I just logged, so that a slip of the thumb ("4, not 3") is reversed without touching anything else. · WF-3 done-when ("Undo removes exactly one entry"), FR-046 ("Undo for recent supported mutations"), map WF-3 ("one-tap with Undo") · EX-13, EX-24, EX-38, C10
- `/r` Given Sam's Day holds "1 cup of laban" and he then logs "4 cheese bites", When the banner "Logged 4 cheese bites · Undo" shows and he taps Undo, Then the 4-cheese-bite Entry leaves the timeline, "1 cup of laban" stays, and "kcal left" rises by exactly 184.
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
- `/r` Given a request over HTTP to `POST /v1/consumption` for 3 cheese bites that also carries "kcal": 10, Then the accepted Entry reads 138 kcal: the client's number is ignored, never stored.
- `/s` Given the analyzer adapter's call counter, When a recent-Unit tap, a Template log and a copied meal are committed, Then the counter is unchanged (FRD §16.5: "A confirmed repeated unit uses no new nutrition inference").
- `/s` Given the Platform admin moves the text-intent Registry version to Rollout, When Sam's Days 29 Sep and 1 Oct are read again with `GET /v1/reports/day`, Then every Entry and total is identical (FR-031; the admin lens's rollout story).

#### eater-3.8 · An Archived or foreign Unit never logs silently
As the Eater, I am told plainly when a Unit I logged offline was Archived on my other iPhone, and nobody else's Unit can ever be logged into my Day, so that nothing is counted against a definition I removed or do not own. · FRD §7.1 ("validate existence and ownership of every referenced unit"), FRD §18.2, NFR-07, FRD §14 My Units ("archived unit") · EX-23
- `/r` Given Sam Archived "tuna spoon" on iPhone B, When iPhone A syncs, Then the "tuna spoon" tile leaves A's recent Units on Today and My Units lists it under "Archived".
- `/r` Given iPhone A logged "2 tuna spoons" offline before that sync, When the command reaches the server, Then the Entry is kept as eaten and its Entry details note "This unit is Archived" with a "Restore unit" button — nothing is dropped silently (a proposal; Conflicts item 16).
- `/r` Given Mona's token and Sam's unit_version_id in a request over HTTP to `POST /v1/consumption`, Then it returns `UNIT_NOT_FOUND` exactly as for an id that never existed, and no Entry is created (NFR-07).

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
- `/r` Given Mona's Saved Unit «كوباية لبن» (milk) and Faisal's «كوب لبن» (yogurt drink), When each types their own word, Then each chip names that eater's own Unit; given Sam has no laban Unit and types «لبن», Then the chip shows the Food the Nutrition approver's Alias gives for Sam's dialect setting, with its Evidence badge, and waits for a tap — the eater's own Unit always wins over a dialect Alias (FRD §5.1).
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
- `/s` Given `POST /v1/analyses` for "2 kunafa", Then the Analysis returns required_questions for the unmatched item, the response for a two-Unit match carries `UNIT_AMBIGUOUS`, and no Entry is committed (FRD §7.1).

#### eater-3.13 · My words decide whether anything is logged
As the Eater, I can type "calculate and save my bite", "how many calories in 2 cheese bites?", "plan my dinner", "how much is left?", "add another 3 spoons", "18 not 15" or "start a new day" in the same quick-add box, so that each does what I meant and only eating, correcting or removing ever changes my Day. · FR-039 (estimate, calibrate, plan, consume, correct, remove, report, start a new day), AT-13, FRD §4.3, FRD §8.2, FR-045 · EX-14
- `/r` Given Sam's Day at 290 kcal, When he types "calculate and save my bite" with a photo of a cheese bite on the scale, Then the Unit editor in My Units opens with a Unit Draft; after "Save unit", Today still reads 290 eaten and `GET /v1/reports/day` returns consumed_kcal 290 (AT-13).
- `/r` Given that Day, When he types "how many calories in 2 cheese bites?", Then quick-add answers "92 kcal" with no Log button pressed and the timeline unchanged; When he types "how much is left?", Then the day report opens at "1,580 kcal left" and the Day's revision is unchanged.
- `/r` Given that Day, When he types "plan my dinner, 600 kcal", Then the Meal planner in Capture & Plan opens with a 600 kcal cap and the Day total is unchanged (WF-5).
- `/r` Given Mona's Dinner holds «١٥ معلقة تلبينة», When she types «زوّد ٣ معالق تلبينة», Then a new chip «٣ × معلقة تلبينة · ٦٠» appears as an addition; When instead she types «١٨ مش ١٥», Then the correction preview opens (eater-6.5) and no consumption chip appears (FRD §8.2).
- `/r` Given any Day, When "start a new day" (or «ابدأ يوم جديد») is typed, Then the Start new day confirmation opens (eater-3.32) and nothing is deleted.

#### eater-3.14 · AI off, used up or not allowed: my Units still log
As the Eater, I can still log from my recent Units, Templates and typed Unit names when sentence understanding is unavailable — Kill switch On, daily AI limit reached, or my Consent for Google's AI Withdrawn — and one line tells me why, so that logging never stops. · FRD §7.2, AT-32, NFR-05, FRD §16.4 ("A kill switch must preserve manual and cached logging"), FRD §16.5, FR-076, D2 Registry ("Kill switch … fail fast with AI_UNAVAILABLE") · EX-22, R3, research conflict 9 · **Shared: Eater · Platform admin**
- `/r` Given the text-intent Kill switch is On, When Sam types "3 cheese bites" in quick-add, Then one line reads "Sentences can't be read right now — pick from your units", the recent Units whose names match the typed words ("cheese bite") are listed with count steppers, and tapping it and Log records 3 cheese bites.
- `/r` Given Sam's daily AI limit is used up (`RATE_LIMITED` with a reset time), When he types a sentence, Then the line names the reset time ("Sentences are back at 00:00"), and recent Units, Templates and copied meals still log; a recent-Unit tap never uses the limit (FRD §16.5).
- `/r` Given Mona's Consent for Google's AI is Withdrawn, When she opens quick-add, Then the microphone and sentence reading are off with "Off by your choice · Settings → Privacy", and recent Units, Templates, copy and calories-only log as usual; a request over HTTP to `POST /v1/analyses` with her token returns `CONSENT_REQUIRED`.
- `/s` Given the analyzer adapter mock times out on every call, When 50 recent-Unit logs are made, Then all 50 are Confirmed and none waited on the analyzer (NFR-05).

### D · Calories only

#### eater-3.15 · A calorie-only Entry when I only have a number
As the Eater, I add "kunafa slice, 350 kcal" as a calorie-only Entry from quick-add, so that a food I cannot describe still counts — without the app making up its macros. · FR-016 ("label it user-defined and leave unknown macros unknown"), AT-16, FRD §18.1 ("Calorie-only custom items use an explicit user-override path"), FRD §20.1 · EX-27
- `/r` Given Sam's Day at 290 kcal with full macros, When he opens quick-add → "Calories only", enters "kunafa slice" and 350, and taps Log, Then the timeline shows "kunafa slice · 350 kcal · user-defined", Today reads "640 eaten", and the macro bars read "Macros known for 45 % of kcal" (AT-16).
- `/r` Given that Entry, When its Entry details open, Then protein, carbohydrate and fat read "Unknown" — never 0 g — and no grams are shown.
- `/r` Given the calories field is empty, negative or not a number, When Log is viewed, Then Log is disabled with "Enter the calories" beside the field.
- `/s` Given the calorie-only command over `POST /v1/consumption`, Then the stored Entry's Evidence is user-defined, its macros are null (not 0), and `GET /v1/reports/day` returns macros_complete false.

### E · Repeat a meal, a Day or a Template

#### eater-3.16 · Copy a meal from any past Day
As the Eater, I copy a meal from yesterday or any earlier Day into today, so that a repeated breakfast costs one action and I am not limited to yesterday. · WF-3 done-when ("copy yesterday's breakfast logs one meal"), map row "consume: copy a meal or day" · C5, E31, E32, EX-02, EX-41
- `/r` Given Mona's Wed 30 Sep Breakfast (3 cheese bites, 1 glass of milk tea, 4 foul spoons; 318 kcal), When on Thu 1 Oct she opens quick-add → "Copy from another Day", picks 30 Sep → Breakfast and taps Log, Then today's Breakfast gains exactly those three Entries (318 kcal) as one meal, and the meal report and day report appear (eater-3.34).
- `/r` Given Sam moves the Day picker *(proposed)* on Today to Tue 8 Sep, When he opens that Day's Lunch menu → "Copy to today", Then the Lunch's Entries are logged into Thu 1 Oct — copying is not limited to yesterday (E32).
- `/s` Given the copy, When the ledger is read, Then each copied Entry is new (own entry_id, the copy's command_id, diary_day_id 2026-10-01, eaten_at at the time of the copy) and the 30 Sep Entries are unchanged.
- `/r` Given the copy request is resent after a lost response with the same command_id, When Today and `GET /v1/reports/day` are read, Then the meal appears once (AT-10).

#### eater-3.17 · A copy uses my Units as they are now, and says what changed
As the Eater, I see when a Unit in the meal I copy has changed since that Day, so that the copy uses my current definition and I know why its number differs from last time. · FR-014, FRD §17 EatingUnitVersion ("Latest approved version is used for new logs only") · E15, E21, EX-14
- `/r` Given Sam's "bread bite" went from version 1 (8 g, 20 kcal) to version 2 (9 g, 22.5 kcal) on 30 Sep, and his 29 Sep Breakfast holds "6 bread bites" (120 kcal on version 1), When he copies that Breakfast to 1 Oct, Then the copy screen notes "bread bite changed since 29 Sep: 8 g → 9 g", the new Entry reads "6 bread bites · 135 kcal", and 29 Sep still reads 120 kcal.
- `/r` Given the copied meal holds "2 tuna spoons" and "tuna spoon" is Archived, When the copy screen opens, Then that line reads "tuna spoon is Archived — skipped" with "Restore unit", and the other items can still be logged.
- `/r` Given the source meal holds an Entry with Evidence "estimated analogue", When it is copied, Then the copy keeps the badge "estimated analogue" — a copy never upgrades Evidence.

#### eater-3.18 · Copy a whole Day
As the Eater, I copy a whole earlier Day's food into today and can leave a meal out, so that a routine Ramadan day or work day is logged at once. · map row "copy a meal or day", FR-045 · C23 ("not including imported activity"), E11, E12, EX-02, EX-41
- `/r` Given Faisal's Mon 8 Feb 2027 holds food Entries in three meals, an Activity "walk 30 min" and one Voided Entry, When on Tue 9 Feb he picks 8 Feb in the Day picker → "Copy this Day" and taps Log, Then today receives every effective food Entry under the same meal names, and neither the Activity nor the Voided Entry is copied.
- `/r` Given the copy screen, When it opens, Then it lists each meal with its kcal and a tick per meal, all ticked; unticking one meal leaves it out of the copy.
- `/r` Given Faisal copies the same Day again two minutes later, When he taps Log, Then the near-duplicate note appears (eater-3.24) and nothing is thrown away.

#### eater-3.19 · Save a meal as a Template
As the Eater, I save a meal I eat often as a named Template, so that next time it is one tap from quick-add. · map §1 ("save meal Templates", vocabulary "Template (a saved meal)"), FRD §2.1 ("recent units, or a meal template"), FR-045 · C5, C45, EX-02
- `/r` Given Mona's Wed 30 Sep Breakfast, When she opens the Breakfast menu → "Save as Template", names it «فطار عادي» and taps Save, Then the Templates list *(proposed)* in My Units shows «فطار عادي · ٣ عناصر · ٣١٨» and quick-add shows it under Templates.
- `/r` Given the Template was saved, When Today and `GET /v1/reports/day` for 1 Oct are read, Then nothing was logged by saving.
- `/r` Given the name is empty or every item was removed, When Save is viewed, Then Save is disabled with "Give it a name" or "Add at least one item" beside the field; given the name «فطار عادي» already exists, Then "A Template with this name exists — choose another name" appears and nothing is overwritten.
- `/s` Given `POST /v1/templates` *(proposed)*, Then the Template stores Unit ids and counts — no Unit versions and no calories — so its numbers come from the Units when it is logged.

#### eater-3.20 · Log a Template, changing counts first if I want
As the Eater, I log a Template from quick-add and can change a count or leave an item out before Log, so that "usual breakfast, but two cheese bites today" is still two taps. · FRD §2.1, map WF-3 ("log a Template"), FR-014 · C5, EX-02, EX-17
- `/r` Given the Template «فطار عادي», When Mona taps it in quick-add, Then its three items appear with count steppers at 3, 1 and 4 and a Log button naming 3 items; tapping Log adds three Entries (318 kcal) to Breakfast — two taps in the recorded walk.
- `/r` Given those steppers, When she lowers the cheese bites to 2 and unticks the tea, Then Log names 2 items and logs «٢ × قرصة جبنة» and «٤ × معلقة فول» (212 kcal), and the Template in My Units still reads 3, 1 and 4.
- `/r` Given one-tap logging is on, When she taps the Template, Then all three items log at once with an Undo banner naming «فطار عادي».
- `/r` Given she deletes the Template in My Units → Templates, When the banner offers Undo and she ignores it, Then the Template is gone and no Entry on any Day changed (a Template is not part of the ledger).

### F · Siri and the widget

#### eater-3.21 · Log with Siri or the Shortcuts action
As the Eater, I say "Log cheese bite in Sips & Bytes" or run the "Log a Unit" action, so that I can log without opening the app, through the same command the app uses. · map WF-3 ("Siri or a widget"), map row "one idempotent consume command for every surface", FR-040 · P22, P23, C55, EX-13
- `/r` Given Sam's Unit "cheese bite" (last count 3) and the App Shortcut phrase "Log cheese bite in Sips & Bytes" (the app name plus one parameter, the Unit, P22), When the "Log a Unit" App Intent runs from the simulator's Shortcuts app with Unit = cheese bite and no count, Then it logs 3 and the result snippet reads "Logged 3 cheese bites · 1,442 kcal left" with an Undo button (UndoableIntent and SnippetIntent, iOS 26, P23).
- `/r` Given the same action run with Count = 2, Then the snippet reads "Logged 2 cheese bites", and Today lists the Entry when the app is opened.
- `/r` Given Undo is tapped on the snippet, Then the Entry is Voided (reason undo) and Today no longer lists it.
- `/r` Given the simulator is offline, When the intent runs, Then the snippet reads "Logged 3 cheese bites · waiting to send", and Today shows the Entry as Pending (eater-3.25).
- Note: no source confirms App Shortcut phrases with Siri in Arabic (P22, r1-refute-b open point 3) — `assumption`. Arabic acceptance for this path runs through the Shortcuts action; typed and tapped logging never depend on Siri.

#### eater-3.22 · Log from the widget, discreetly
As the Eater, I tap a recent Unit on the Home Screen widget, so that a glass of tea is logged without opening the app — and a locked phone shows nothing of my diary. · map WF-3 ("Siri or a widget"), map row "one idempotent consume command", map §6 ("hide numbers") · P24, C55, EX-43, research Part 2 §4 ("Discreet")
- `/r` Given the Sips & Bytes widget on the simulator's Home Screen shows Sam's three most recent Units, When he taps "cup of laban", Then the app does not open, the widget shows "Logged 1 cup of laban · Undo", and Today, once opened, lists the Entry.
- `/r` Given the simulator is locked, When the Lock Screen widget's button is tapped, Then nothing is logged until the device is unlocked (P24), and the Lock Screen widget shows no food names or kcal — only "Sips & Bytes · Log" (C55: "an innocuous summary").
- `/r` Given Settings → Privacy → "Show kcal left on widgets" is turned on, When the Home Screen widget refreshes after that log, Then it shows "1,428 kcal left"; given Settings → Goals → "Hide numbers" is on, Then the widget shows no numbers at all.
- `/r` Given the widget's Undo is tapped, Then the Entry is Voided and the widget returns to its tiles.

#### eater-3.23 · Every surface sends one idempotent command
As the Eater, I can trust that a tap, the chips, a Template, a copy, Siri and the widget all record food the same way, so that a retry or a double delivery never doubles my food. · map row "one idempotent consume command for every surface", FR-040, FR-043 ("Retried commands and duplicate delivery must not add food twice"), AT-10, FRD §18, NFR-06
- `/r` Given one consumption command for 3 cheese bites with one command_id sent three times over HTTP to `POST /v1/consumption` (AT-10), When each response is read, Then all three carry the same entry_id and day_revision, and `GET /v1/reports/day` shows one new Entry and one 138 kcal addition.
- `/s` Given the tile, the quick-add chips, a Template, a copy, the Siri intent and the widget intent, When each logs once in a system test, Then each sends exactly one `POST /v1/consumption` with a fresh command_id and intent "consume", through the same validation and snapshot path.
- `/s` Given the app is killed after sending a command but before the response arrives, When it relaunches and the outbox resends with the same command_id, Then no second Entry is created (NFR-06 session recovery).

### G · Near-duplicate

#### eater-3.24 · A near-duplicate gets a quiet note, never a discard
As the Eater, I see a quiet note when I log the same thing again within minutes, with "Keep both" and "Undo this one", so that a real second helping is never thrown away and a double tap is easy to fix. · FR-043 ("Near-duplicate human commands should show a warning rather than being automatically discarded") · EX-24, EX-35
- `/r` Given Sam logged "3 cheese bites" at 08:41, When he logs "3 cheese bites" again at 08:43, Then both Entries are in the timeline and the banner reads "You logged 3 cheese bites at 08:41 · Keep both · Undo this one" — no dialog.
- `/r` Given he taps "Undo this one", Then only the 08:43 Entry is Voided; given he taps "Keep both" or ignores the note, Then both stay counted and Today reads 276 kcal for the two.
- `/s` Given two commands with different command_ids and the same items 2 minutes apart, When both reach the server, Then both are Confirmed; the note's window (10 minutes here) is a value chosen in the served product (`assumption`; Conflicts item 5).
- `/r` Given the same 3 cheese bites at 08:41 and again at 12:30, Then no note appears.

### H · Offline and slow

#### eater-3.25 · Log with no signal; Pending is shown, not hidden
As the Eater, I keep logging with no signal and see each new Entry marked Pending and the Pending kcal shown apart from the Confirmed figure, so that I know what the server has accepted without being interrupted. · FRD §8.3 ("The client distinguishes pending from confirmed totals"), FRD §7.2, NFR-06, D2 Entry ("the day total shows Pending separately"), FRD §14 Today ("offline pending") · EX-12, EX-21, E19, E29, E38, C15, C35
- `/r` Given Mona online with Breakfast 318 kcal Confirmed on Thu 1 Oct, When the simulator goes offline and she logs «١ × كوباية شاي بلبن» and «٢ × تمرة سكري», Then both Entries show Pending with a clock symbol and the word, the headline reads «١٬٤٤٤» kcal left with «١٠٨ في الانتظار» (108 Pending) beside it and the Confirmed figure «١٬٥٥٢» under it, and no alert appears.
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
- `/r` Given Faisal turns "Ramadan days" off on 10 Mar 2027, Then new Entries follow his 03:00 boundary from the next Day, and February's Days keep their Entries.

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
- `/r` Given she types «ابدأ يوم جديد» instead, Then the same confirmation opens; nothing is deleted, cleared or reset.
- `/s` Given `POST /v1/days` *(proposed)* for 2026-10-01 is sent twice, Then one Day exists, and `GET /v1/reports/day` for 2026-09-30 returns the same totals and revision as before.
- `/r` Given she started 1 Oct early, When 03:00 passes, Then Today stays on Thu 1 Oct and no extra Day is created.

#### eater-3.33 · Log onto a past Day from memory
As the Eater, I pick yesterday in the Day picker and log the dinner I forgot, so that a missed meal is recorded on its Day and today stays untouched. · FR-047, FR-044, FRD §14 Today ("Selected diary date") · E24, E33, EX-07
- `/r` Given Sam's Thu 1 Oct at 290 kcal, When he picks Wed 30 Sep in the Day picker and logs "1 cup of laban" at 21:00, Then 30 Sep's day report rises by 152 and Thu 1 Oct still reads 290 eaten.
- `/r` Given a past Day is selected, When Today is viewed, Then the header names that Day with "Back to today", so a log never lands on another Day unnoticed.
- `/s` Given that command, Then it carries diary_day_id 2026-09-30 and eaten_at 21:00 local on 30 Sep (the time can be changed before Log), and `GET /v1/reports/period` for 24–30 Sep includes the 152 kcal.

### J · Meal report and day report

#### eater-3.34 · A meal report and a day report after every entry
As the Eater, I see right after each log what was added and where my Day now stands — calories, macro grams, 4/4/9 kcal and shares, Target and remaining — so that the reports I used to ask a chatbot for are always there and always the same. · FR-069, FR-070, FRD §13.1, FRD §10.1, FRD §10.3, map row "Ledger → Eater: meal and day report" · EX-11, E22, E23
- `/r` Given Sam's Day (Provisional) at 720 kcal (P 30 g, C 105 g, F 20 g) with Target 1,870 and carbohydrate ≤30 %, When he logs a lunch resolving to 480 kcal (P 42 g, C 33 g, F 20 g), Then the meal report on Today reads "Lunch added: 480 kcal" with Protein 42 g · 168 kcal · 35.0 %, Carbohydrate 33 g · 132 kcal · 27.5 %, Fat 20 g · 180 kcal · 37.5 %, and "Carbohydrate: within the 30 % maximum".
- `/r` Given that log, Then the day report under it reads "Today: 1,200 kcal · Target 1,870 · Remaining 670" with Protein 72 g · 288 kcal · 24.0 %, Carbohydrate 138 g · 552 kcal · 46.0 %, Fat 40 g · 360 kcal · 30.0 %, and "Carbohydrate: above the 30 % maximum" in neutral wording (FRD §13.1).
- `/r` Given those share columns, When they are read, Then the heading names their basis "share of macro-derived energy (4/4/9)" (FRD §10.1).
- `/r` Given the `POST /v1/consumption` response for that lunch over HTTP, Then its meal totals and day totals carry the same figures as the two reports.

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
- `/s` Given `GET /v1/reports/day` for that Day, Then it returns consumed_kcal 640, macro_coverage_kcal 290 and macros_complete false, and attributes no protein to the kunafa Entry.

#### eater-3.37 · Target, left and over — plainly, without judgment
As the Eater, I see Target, eaten and left (or over) with the Evidence behind them and a range when estimates are included, in calm words, so that an ordinary over-target Day never feels like a failure. · FR-070 ("target, consumed, remaining/over, source confidence, and estimated range"), FR-071, FRD §11.4, FRD §14.2, FRD §20.1, FRD §14 Today ("over target") · EX-35, EX-42
- `/r` Given Sam's Day at 1,990 kcal with Target 1,870, When Today shows, Then the headline reads "120 kcal over" in the same neutral colour as "left", carries the word "over", and no red, alert, "bad" or advice to skip a meal appears.
- `/r` Given that Day includes an "estimated analogue" Entry of 450 kcal with a range of 380–520, When the day report opens, Then it reads "1,990 kcal · heuristic range 1,920–2,060", labelled a heuristic low/high, not a confidence interval.
- `/r` Given that Day, When the Evidence line of the day report is read, Then it counts Entries per badge, e.g. "measured 4 · recipe-calculated 1 · estimated analogue 1".
- `/s` Given Sam's Target changed from 1,870 to 1,800 on 1 Oct, When `GET /v1/reports/day` for 2026-09-30 is read, Then it still compares against 1,870 (FR-071).

#### eater-3.38 · Every figure adds up to Entries I can see
As the Eater, I can tap any figure and see the Entries it adds up, so that a total never moves without a visible Entry, Correction, Void or Restore. · FR-042 ("one transaction. Replaying the ledger shall reproduce the totals"), NFR-01, FRD §17 DiaryDayProjection, map row "Ledger → Eater" · E18, E19, EX-14 · **Shared: Eater · Support agent**
- `/r` Given Sam's Day at 1,200 kcal, When he taps the eaten figure on Today, Then the list of Entries shows each one's kcal and the sum line reads 1,200 kcal.
- `/s` Given that Day's accepted events (creates, a Correction, a Void, a Restore), When the Day projection is rebuilt from the ledger alone, Then consumed kcal, macro grams, coverage and revision equal the stored projection exactly.
- `/s` Given a create and its Day-projection update, When the projection write is forced to fail, Then neither is stored (one transaction) and the client keeps the Entry Pending.
- `/r` Given a Support agent reads this Day inside an Active Grant, Then the day report figures equal the eater's own (support lens, Grant diary read).

### K · Apple Health

#### eater-3.39 · Each Confirmed Entry appears in Apple Health as a food
As the Eater, I find what I log in Apple Health as one food with its energy and macros, and it disappears when I Void it, so that my other health apps see the same diary. · map row "Ledger → HealthKit: write the entry as a food correlation … delete on void", WF-3 done-when ("appears in Apple Health and disappears on void"), FR-016 · P29, C12, C56
- `/r` Given Sam turned on Settings → Activity → "Write meals to Apple Health" and allowed Dietary Energy, Protein, Carbohydrates and Total Fat in the iOS sheet, When "3 cheese bites" turns Confirmed, Then the simulator's Health app → Browse → Nutrition lists 138 kcal Dietary Energy from "Sips & Bytes" at the Entry's eaten time, with protein 7.5 g, carbohydrates 13.5 g and fat 6.0 g at the same time.
- `/s` Given that write, Then it is one HealthKit food correlation holding the four samples (P29), and the Entry stores the correlation and sample ids.
- `/r` Given the calorie-only "kunafa slice", When it is written, Then Health shows 350 kcal and no protein, carbohydrate or fat sample — unknown is never written as 0 g.
- `/r` Given an Entry is Pending offline, When Health is viewed, Then it has no sample yet; after the Entry turns Confirmed the sample appears once (Conflicts item 4).
- `/r` Given Sam taps Undo on that Entry, or Voids it, Then the correlation disappears from the Health app.

#### eater-3.40 · Health is asked once, when it matters; saying no keeps logging
As the Eater, I'm offered Apple Health writing as a quiet card after my first Confirmed Entry, so that I decide when it means something, and logging works the same if I say no. · FR-076 ("Refusal must preserve unaffected functions"), FRD §3.2 ("Goals, permissions, and health connections are separate choices"), map §6 ("Health write on/off"), map row Consent ("each Health type") · EX-26, P30, R7
- `/r` Given Sam never decided on Health, When his first Entry turns Confirmed, Then Today shows a card "Add your meals to Apple Health?" with "Turn on" and "Not now" inside the timeline (no pop-up), and "Turn on" opens the iOS sheet listing only the four nutrition types to write.
- `/r` Given Sam taps "Not now" or denies every type in the iOS sheet, When he logs 3 cheese bites, Then the Entry is logged as usual, nothing is written to Health, and Settings → Activity reads "Write meals to Apple Health: Off".
- `/r` Given Sam turns writing off later, When he logs, Then no new samples are written and his earlier samples stay in Health (`assumption`; Conflicts item 4).
- `/s` Given Health writing on, When any Entry is written, Then the app reads nothing from Health for it and nothing from Health is sent to the API or the AI analyzer (R7; map rule "Health data never sent").

### L · For every eater

#### eater-3.41 · Logging works one-handed, at the largest text, with VoiceOver and in Arabic
As the Eater, I can log with one thumb, at the largest text size, with VoiceOver, with a keyboard and in mirrored Arabic, so that the fastest path is the same for everyone. · NFR-08 ("Screen-reader and text-scaling flows pass on every P0 journey"), FRD §14.1 (right-to-left, both numeral systems), FRD §14.2 · EX-33, EX-34, EX-36, EX-37, EX-38, EX-39, E34–E37, E40, E41
- `/r` Given the largest accessibility text size on the iPhone 16e simulator, When Today, the count stepper, the quick-add chips and the meal and day reports are opened in English and in Arabic, Then no text clips or overlaps in the screenshots and Log is reachable (by scrolling if needed) and never covered by the keyboard.
- `/r` Given VoiceOver, When focus reaches the "cheese bite" tile, Then it reads "cheese bite, last 3, includes 8 g bread each, button"; after Undo the "kcal left" figure is read with its new value.
- `/r` Given Arabic, When Today is shown, Then the layout is mirrored, back points right, macro bars fill from the right, «١٬٥٨٠» keeps its digit order, and a row mixing an English Unit name with an Arabic count keeps the count beside its food.
- `/r` Given light and dark appearance, When the "kcal left" figure and Entry text are measured on the served screen, Then contrast is at least 4.5:1 and the figure uses a heavier weight than body text.
- `/r` Given Full Keyboard Access, When the eater tabs through Today and the count stepper, Then every control shows a focus ring and can be used; given Reduce Motion, When a log, Undo or count change happens, Then nothing slides or bounces.

#### eater-3.42 · "Hide numbers" keeps logging
As the Eater, I can hide calorie and macro numbers and still log my Units, so that tracking helps me without numbers I find harmful. · map §6 ("'hide numbers' view", R37), FRD §11.4, FRD §14.2 · EX-43
- `/r` Given Settings → Goals → "Hide numbers" is on, When Mona logs «٣ × قرصة جبنة» from Today, Then the timeline shows «٣ قرصة جبنة» with no kcal, Today shows no "left" figure, and the meal and day reports show food names and coverage words only.
- `/r` Given that view, When the widget and the Siri snippet show a log, Then neither shows a number.
- `/s` Given that view, Then Entries and Day totals are stored exactly as with numbers shown — the view hides, it never changes the ledger.

#### eater-3.43 · Only eating changes my Day
As the Eater, I can save a Unit, keep a Plan, take a photo or drop an Analysis without anything being added to my Day, and confirming a Plan twice still adds one meal, so that my total holds only what I ate. · FR-045 ("Planned meals, calibration photos, and abandoned drafts shall contribute zero … prevent duplicate execution"), AT-13, AT-21, FRD §4.3, FR-039 ("Only authorized consume/correct/remove commands affect the ledger")
- `/r` Given Sam's Day at 290 kcal, When he saves a Unit from a scale photo, keeps a 600 kcal Plan Saved in Capture & Plan, and closes an Analysis so it is Discarded, Then Today still reads 290 eaten and `GET /v1/reports/day` returns consumed_kcal 290.
- `/r` Given a Saved Plan is Confirmed with "Ate as planned" and the confirmation is resent with the same command_id, When Today is read, Then the Plan's meal appears once (AT-21; the Plan flow itself is WF-5).
- `/s` Given the consume command from a Plan, Then it carries source_plan_id (FRD §18.1) and a second confirmation of the same Plan adds no Entry.
