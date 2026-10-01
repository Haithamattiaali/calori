# Eater — journeys WF-1 and WF-9, with the eater's side of the support Grant

Written 2026-10-01 by the eater lens, steps 2, 3 and 5 of `way/personas/_lens-brief.md`. Dispatch: **WF-1 Onboard and set a target** and **WF-9 Privacy**, including approving or declining a support Grant in Settings (map §3 interaction rows "Support → Eater" and "Support → eater diary", WF-10 done-when). Research cycle 2 and the experience are in `way/personas/eater/research.md`. This file builds on them and cites their ids (findings E1–E44, experience requirements EX-01–EX-44) instead of repeating them. Ids are `eater-1.n` and `eater-9.n`.

## How to read this file

- **Each story** has a title, a **Covers** line (first the map line it traces to — workflow step, interaction row or done-when — and the FRD lines, then research ids), a **Shared** line when another persona owns it too, the story sentence, and acceptance lines.
- **Layers.** `/m` module (unit test of pure code) · `/s` system (an API or rules test against the emulator or a running service, negative tests included) · `/r` runtime (a verifier can watch it in the served product: the iOS simulator, the API over HTTP, or the admin console in a browser). Every story has at least one `/r` line.
- **Words.** Only the map's vocabulary (blueprint §1.4) and `way/vocabulary.md` (delta D2): Unit, Recipe, Food, Template, Entry, Day, Target, Analysis, Evidence, Activity, Consent, Policy, Grant, Privacy job; roles Eater, Support agent, Nutrition approver, Platform admin, Auditor. States use vocabulary.md's names exactly — Entry Pending/Confirmed; Analysis Pending/Ready for review/Approved/Discarded; Unit Draft/Saved; Consent Given/Withdrawn ("Not given" means no Consent record exists for that purpose); Grant Requested/Active/Expired/Ended/Withdrawn/Declined/Unanswered; Privacy job Requested/Running/Completed/Failed. "Draft" is used only for a Unit. Places: tabs Today · Capture & Plan · My Units · Progress (the FRD §14 "Capture" screen is the Capture & Plan tab); screens Analysis review, Unit editor, Meal planner and Settings; Settings sections Goals, Activity, Units & language, Privacy and Export; admin console sections Grants, Jobs and Audit trail; and the onboarding screens named below. "The AI Consent" is short for the Consent purpose "Send photos, voice and text to Google's AI (Gemini)". "The Target flow" is Onboarding · Profile → … → Onboarding · Review.
- **Copy.** Quoted text is proposed English copy. Arabic strings are proposals for the string catalogue, which fixes one Arabic label per word (EX-08). No FR, AT, Policy-version or error code ever appears in the eater's copy; codes appear only in API responses.

## Onboarding screens (named here; the map names none)

| screen | what it is for | reached from |
|---|---|---|
| Onboarding · Age | the 18+ gate | first launch |
| Onboarding · Under 18 | the end screen for an age under 18 | Onboarding · Age |
| Onboarding · Consents | where the diary lives; the AI Consent; Optional research | Onboarding · Age |
| Onboarding · Account | create an account (Sign in with Apple or email) or sign in | Onboarding · Consents; "Create account" on the account line of Settings; "Set a target" in a local trial |
| Onboarding · Profile | age, height, weight, equation version, region and units, foods I don't eat | "Set a target" on Today; Settings → Goals |
| Onboarding · Safety screen | optional questions that choose tracking-only or protein-first | Onboarding · Profile; Settings → Goals |
| Onboarding · Energy | resting energy, maintenance, planned exercise | Onboarding · Safety screen |
| Onboarding · Target | lose, maintain or gain; the proposed Target; my own value | Onboarding · Energy |
| Onboarding · Macros | macro targets in % or grams, with locks | Onboarding · Target |
| Onboarding · Activity mode | whether exercise is included; Fixed target or Activity-adjusted target; Apple Health (optional) | Onboarding · Macros |
| Onboarding · Review | everything once, then "Approve target" | Onboarding · Activity mode |

Settings sections used are vocabulary.md's: **Settings → Goals** (with its History), **Settings → Activity**, **Settings → Units & language**, **Settings → Privacy** (with the Consents, **Grants**, **Support code** and **Delete account**), and **Settings → Export**. Settings opens from the button at the top of Today (EX-04, E35). Its first line is the **account line**: either "Signed in" with the sign-in method, or "Not signed in · Your diary is only on this iPhone" with "Create account". vocabulary.md names no Account or Help section, so this lens puts no section there (conflict C-19).

## Proposed interfaces (FRD §18 style; the FRD names none for these)

- `POST /v1/goals/proposals` computes resting energy, maintenance, the Target per goal, the deficit or surplus, macro grams, assumptions and the review date from the inputs and the live Policy. It writes nothing.
- `POST /v1/goals/versions` approves one Target, stored as a GoalPlanVersion (FRD §17), with a `command_id`. `GET /v1/goals/versions` lists the history.
- `PUT /v1/me/safety-mode` takes `standard | tracking_only | protein_first`, the safety-screen version and the time. It never takes the answers.
- `POST /v1/me/consents` takes purpose, action (`given | withdrawn`), text version, method, `made_at` and `command_id`. `GET /v1/me/consents` lists them.
- `GET /v1/me/grants`, `GET /v1/me/grants/{id}/reads`, `POST /v1/grants/{id}/approve | decline | withdraw` (the support lens names `POST /v1/grants/{id}/approve`).
- The FRD's `POST /v1/privacy/export-or-delete`, plus a read of the Privacy job, `GET /v1/privacy/jobs/{id}`.
- Errors, only from vocabulary.md: `AGE_REQUIREMENT`, `CONSENT_REQUIRED`, `POLICY_FLOOR` (with a `limit` field, `floor` or `hard_stop`), `VALIDATION_ERROR` (with `field` and `reason`, such as `macro_total`, `locks_incompatible` or `tracking_only`), `RATE_LIMITED`, `STALE_REVISION`, `UNAUTHENTICATED`, `FORBIDDEN`, `NOT_FOUND`, `GRANT_REQUIRED` and `GRANT_NOT_ACTIVE`.

## Fixtures (all synthetic; the repo is public)

| fixture | values |
|---|---|
| Policy v1 | As the approver lens reads it (approver-10.48): floor 1,200 kcal (product policy, not a sourced hard floor — r1-refute-b Dropped 12); hard stop 1,000 kcal (R32); loss 15 % below maintenance, deficit capped at the smaller of 15 % and 500 kcal (R33; 500 is the approver lens's proposed value); gain +10 % (FRD §3.3); GLP-1: protein 1.2–1.6 g/kg and no added deficit (R35, R41); tracking-only at ≥2 of 5 SCOFF-style items, pregnancy or breastfeeding (R38, R32); retention raw scans 30 days, audio 24 h (FR-078). **Proposed by this lens** (see Assumptions): non-exercise activity multiplier 1.2 (FRD §11.3); default macro split protein 30 / carbohydrate 40 / fat 30 % (EA5); review interval 14 days (EA6); Activity-adjusted credit 50 % up to 300 kcal a day (EA7) |
| O1 Hala | Africa/Cairo (UTC+3 on 1 Oct 2026); iPhone in Arabic, region Egypt, Arabic-Indic digits; diary-day boundary 00:00; age 34, height 160 cm, weight 78 kg; equation version "−161"; no planned exercise; goal lose; safety screen skipped |
| O2 Sam | Europe/London; English; weight entered as 185 lb; measured resting value 1,779 kcal a day "from a clinic test on 20 Sep 2026" (FRD §11.3); planned exercise 200 kcal a day; types his own Target 1,870 |
| O3 Faisal | Asia/Riyadh; Arabic, region Saudi Arabia, Western digits (E41: both systems are used there); age 41, 176 cm, 80 kg; equation version "+5"; safety screen: GLP-1 medicine "Yes" |
| O4 Huda | Africa/Cairo; English; age 55, 156 cm, 60 kg; "−161"; goal lose |
| O5 Amal | Asia/Riyadh; Arabic; age 68, 152 cm, 52 kg; "−161" |
| T1 trial diary | On Hala's iPhone, no account: Units «قرصة جبنة» cheese bite v1 (5.4 g cheese + 1.5 g oil + 8 g bread, FRD §2.2), «شاي بلبن» tea with milk v1, «لقمة عيش» bread bite v1 (8 g); Template «فطار» (3 cheese bites + 1 tea with milk); 12 Entries, 7 on Day 2026-09-30 and 5 on Day 2026-10-01; 16 local commands in all |
| Consent text versions | AI Consent `c-ai-4` (auditor lens fixture); Diary processing `c-diary-1`; Optional research `c-research-1` (proposed ids, never shown to the eater) |
| Support fixtures reused | E1 `acct_9c41e2` (Arabic, Arabic-Indic digits, Asia/Riyadh, diary-day boundary 04:00, Sign in with Apple, support code `SB-7KQ2-94XM`); E2 `acct_51ab07` (English, Africa/Cairo, email sign-in; deletion requested 2026-09-15 10:00 local, reference `DEL-26-0915-K3Q8`, completed 2026-10-14); Grant `grant_31f0` requested 2026-10-01 10:05 UTC by "Mona K." (Support agent), reason "An entry is missing or appears twice", Days 28–30 Sep 2026, areas "Entries and day reports" and "My Units", 1 hour, case `CASE-1182`; Grant `grant_40aa` requested 2026-10-01 10:05 UTC and never answered |

**Numbers from the fixtures.** Resting energy by Mifflin–St Jeor (FRD §11.1). Maintenance = resting energy × 1.2 + planned exercise (FRD §11.3). A proposal is shown and approved rounded to the nearest 10 kcal, as FRD §11.3 displays 1,867.84 as 1,870 (EA4). Every other energy figure is shown in whole kcal.

| eater | resting energy | ±10 % | maintenance | lose | maintain | gain |
|---|---|---|---|---|---|---|
| Hala | 1,449.0 | 1,304.1–1,593.9 | 1,738.8 | 1,477.98 (deficit 260.82, 15 %) → **1,480** | 1,740 | 1,912.68 → 1,910 |
| Sam | 1,779 (measured) | — | 2,334.8 | 1,984.58 (deficit 350.22, 15 %) → 1,980; his own **1,870** | 2,330 | 2,568.28 → 2,570 |
| Faisal | 1,700.0 | 1,530–1,870 | 2,040.0 | **2,040** (GLP-1: no added deficit) | 2,040 | 2,244 → 2,240 |
| Huda | 1,139.0 | 1,025.1–1,252.9 | 1,366.8 | 1,161.78 is below the floor → **1,200** (deficit 166.8, 12.2 %) | 1,370 | 1,503.48 → 1,500 |
| Amal | 969.0 | 872.1–1,065.9 | 1,162.8 | not offered | 1,200 (floor, conflict C-1) | 1,279.08 → 1,280 |

Macros for Hala's 1,480 at 30/40/30 (FRD §10.3): protein 111.0 g, carbohydrate 148.0 g, fat 49.333 g.

## Assumptions this file makes

| id | assumption | why this value |
|---|---|---|
| EA1 | The age gate takes whole years from 18 to 120. A larger number reads as a typo | catches slips; no source |
| EA2 | Profile ranges are height 100–250 cm and weight 30–300 kg. Values outside them ask "Check this number" | typo guard, not a clinical rule; no source |
| EA3 | Safety-screen answers are judged on the iPhone and then discarded. Only the resulting mode is stored | EX-31; R3 ("ask only for data relevant to its core function") |
| EA4 | The Target is approved as shown (nearest 10 kcal). The unrounded value stays in the input snapshot | FRD §11.3 shows 1,870 for 1,867.84; the eater approves what they see (FRD §23.2) — conflict C-8 |
| EA5 | The default macro split is 30/40/30 | synthetic fixture value; the nutrition approver sets the real one (conflict C-7) |
| EA6 | The review date is the approval date + 14 days | FR-060's proposed minimum review window |
| EA7 | Activity-adjusted credit is 50 % of eligible exercise, up to 300 kcal a day | synthetic; FRD §12.2 asks for "a visible user-approved credit factor and cap" but gives no values (conflict C-6) |
| EA8 | Three gram-locked macros "fit" the Target when their 4/4/9 energy is within 5 kcal of it | gram rounding makes exact equality rare; no source |
| EA9 | A Completed export stays downloadable in the app for 7 days | support lens A8 |
| EA10 | An unanswered Grant request expires after 72 h | support lens A6 |
| EA11 | An anonymous session upgrades to an account and keeps its records | Firebase account linking was not opened in this run |
| EA12 | In a local trial, public Food reference search reaches the API without a user identity (App Check only) | FR-001 asks for a session only for cloud AI (conflict C-16) |
| EA13 | 1 kcal is shown as 4.184 kJ | standard conversion; FAO's chapter (FRD S12) not opened in this run |

## Goals of the eater in these journeys

- **G1 · Start in seconds, at the table.** Log before any profile, Target or account is asked for (E17, E28, EX-03).
- **G2 · A Target I understand and chose.** Resting energy, maintenance, Target and macros, each shown with its method and assumptions, and approved by me (FR-003, FR-056–FR-058).
- **G3 · Safe and never judged.** Tracking-only and protein-first when they apply, no Target below the reviewed minimum, neutral words (FRD §3.3, §11.4; EX-42–EX-44).
- **G4 · My data stays mine.** Separate Consents that one tap withdraws, an export I can take, a deletion that really deletes (WF-9; R3, R4, R22).
- **G5 · Nobody reads my diary unless I approve.** And only for as long as I said (WF-10, eater side; research.md §3, WF-10 row).

The moment, the feeling and the style for both journeys are in research.md Part 2: WF-1 "I could start right away, and nobody judged me"; WF-9 "My diary is mine; I can take it and go"; WF-10 "Nobody reads my diary unless I say yes". Large, calm and one-handed (§4 there).

---

# Journey 1 — Onboard and set a target (WF-1)

Steps, in the map's order (WF-1: "age gate → consents → profile → optional safety screen → resting energy → maintenance → target and macros (floor policy) → activity mode → approve. Tracking works before a target exists"):
**1A** Age gate → **1B** Consents → **1C** Tracking before a Target → **1D** Profile → **1E** Safety screen → **1F** Resting energy and maintenance → **1G** Target → **1H** Macros → **1I** Activity mode → **1J** Approve → **1K** Later changes → **1L** Local trial, account and move → **1M** Language, access and tone.

## 1A · Age gate

#### eater-1.1 · Confirm I am 18 or older before anything else
Covers: WF-1 step "age gate"; interaction row "Eater → app: confirm age 18+"; FR-002 (age); blueprint §1.2 "Minor (hidden, excluded)" · R13, R16 · E17 · EX-03, EX-10
As the eater, I enter my age on the first screen, so that an adults-only app lets me in at once and uses that age later.
- `/r` **Given** a fresh install on the iOS simulator in Arabic with region Egypt, **When** the app opens, **Then** Onboarding · Age is the first screen, right to left, with one field "Your age" («عمرك») and a number keypad, and nothing else is asked.
- `/r` **Given** Hala types «٣٤» in Arabic-Indic digits, **When** she taps Continue, **Then** Onboarding · Consents opens, and when she later reaches Onboarding · Profile its age field already shows «٣٤».
- `/r` **Given** Onboarding · Age, **When** "abc", "0" or "17.5" is entered, **Then** the field says "Enter your age in whole years", and for "150" it says "Check this number" (EA1); Continue stays disabled in each case.
- `/s` **Given** an anonymous-session or account-creation request with no age-gate record of 18 or more, **When** it reaches the API, **Then** it returns 403 `AGE_REQUIREMENT` and no user record is created.

#### eater-1.2 · Under 18: no account, nothing kept or sent
Covers: WF-1 done-when ("Under 18: no account"); blueprint §1.2 "Minor (hidden, excluded) … an age gate keeps them out" · R16 (Google's AI terms bar services likely to be used by under-18s), R13 (the store's 9+ rating does not keep minors out)
As someone under 18, I am told plainly that the app is for adults, so that I am not drawn into an adult calorie tool and nothing about me is kept.
- `/r` **Given** age 16 entered on Onboarding · Age, **When** Continue is tapped, **Then** Onboarding · Under 18 reads "Sips & Bytes is for adults 18 and over." with one button "Change my answer" that returns to the age field, and no path leads to Today, Onboarding · Consents or Onboarding · Account.
- `/s` **Given** the same session, **When** the test proxy's record of network traffic is read, **Then** zero requests were sent, and the local database holds no age value.
- `/r` **Given** Onboarding · Under 18 in Arabic with VoiceOver on, **When** it is read, **Then** VoiceOver reads the sentence once in Arabic, and the screen contains no word about weight, dieting or the body (EX-42).

## 1B · Consents

#### eater-1.3 · Choose each Consent on its own, with nothing chosen for me
Covers: WF-1 step "consents"; interaction row "give separate consents (diary processing; sending photos/voice/text to Google's AI, named; …; optional research) … explicit, per purpose, one-tap withdrawal, no feature paywalled behind consent"; FR-076; FR-001 · R2, R3, R21, R22, R31 · E28 · EX-03, EX-26
Shared: eater + auditor (auditor-9.1, auditor-9.4).
As the eater, I decide separately where my diary lives, whether photos, voice and text go to Google's AI, and whether to help research, so that I agree only to what I want and can still start.
- `/r` **Given** Onboarding · Consents on the simulator, **When** it opens, **Then** it shows three separate parts: "Your diary", with two choices — "Keep it on this iPhone for now" (the main button) and "Keep it in my account" (opens Onboarding · Account); a switch "Send photos, voice and text to Google's AI (Gemini)", off; and a switch "Optional research", off. Below them is the line "Health, camera, microphone and photos are asked the first time you use them."
- `/r` **Given** both switches are left off, **When** "Keep it on this iPhone for now" is tapped, **Then** Today opens in the local trial and no further question is asked.
- `/r` **Given** only the AI switch was turned on, **When** the eater later opens Settings → Privacy, **Then** the AI Consent reads "Given", Optional research reads "Not given", and Diary processing reads "Not given · your diary is only on this iPhone".
- `/m` **Given** the Consent record schema, **When** a record names two purposes, **Then** validation fails.

#### eater-1.4 · Know what goes to Google's AI, and what never does
Covers: interaction rows "give separate consents (… sending photos/voice/text to Google's AI, named …)" and "Eater → AI analyzer: … Health data never sent (R7)"; FR-076, FR-079; FRD §19.1 ("Do not promise zero retention"); blueprint §1.7 (residency open question: no Gemini model runs in a Middle East region, as r1-refute-b established) · R2, R7, R17 · E30 · EX-31, EX-32
As the eater, I read in a few plain sentences what is sent, to whom, where, and what never leaves my iPhone, so that my "yes" to the AI is informed.
- `/r` **Given** Onboarding · Consents, **When** the eater taps "What is sent?" under the AI switch, **Then** the text names Google (Gemini) as the receiver; lists meal photos, voice recordings and typed food sentences as what is sent; says Apple Health data is never sent; says Google does not train its models on them; says they are processed outside Egypt and Saudi Arabia, in the wording counsel approves (blueprint §1.7); and says voice is deleted within 24 hours and raw photos within 30 days unless saved.
- `/r` **Given** the same text in Arabic, **When** shown, **Then** "Google (Gemini)" stays an intact left-to-right run inside the right-to-left sentence, and the words match stored text version `c-ai-4` exactly (auditor-9.3).
- `/s` **Given** an Analysis request built by the API, **When** the Gemini adapter mock records its body, **Then** it holds no Apple Health value (weight, workouts, active energy) and no profile age, height or Target.
- `/m` **Given** the English and Arabic texts of `c-ai-4`, **When** scanned, **Then** neither contains "never stored", "zero retention" or "deleted immediately".

#### eater-1.5 · Every choice is recorded with its version, time and method — once
Covers: interaction row "Consent records (version, time, method)"; FR-076; FRD §8.3 (each command has a UUID) · R22 ("documented through means allowing future verification"), R31 · AT-10 pattern
Shared: eater + auditor (auditor-9.2, auditor-9.6).
As the eater, I know each Consent choice is kept exactly as I made it, so that I can prove what I agreed to and nobody can claim more.
- `/r` **Given** Hala turned on the AI Consent on Onboarding · Consents at 09:12 on 1 Oct 2026 (Cairo), with no account yet, **When** she later creates an account and opens Settings → Privacy, **Then** the AI row reads "Given · 1 Oct 2026, 09:12" with "Read what you agreed to".
- `/s` **Given** the same, **When** `GET /v1/me/consents` is read, **Then** one record shows purpose `ai_processing`, action `given`, text version `c-ai-4`, method "in-app switch · onboarding", `made_at` 2026-10-01T06:12Z (device time), `received_at` (server time) and the app version.
- `/s` **Given** the same Consent command delivered three times with one `command_id`, **When** processed, **Then** exactly one record exists.
- `/r` **Given** the Consent was given while the iPhone was offline, **When** it reconnects, **Then** Settings → Privacy shows the original time 09:12, not the upload time.

#### eater-1.6 · Asked for a permission only when I first need it, and still able to log if I say no
Covers: interaction row consents ("each Health type; mic; photos"); FR-076 ("Refusal must preserve unaffected functions"); FRD §3.2 ("Goals, permissions, and health connections are separate choices. Declining activity access must not block manual food logging.") · R3, R7; P30 · EX-26, EX-27
As the eater, I am asked for the camera or Apple Health only when I first use them, with one sentence why, so that saying no to one never takes away anything else.
- `/r` **Given** an eater who has never used the camera, **When** they tap "Add a photo" for a Unit in the Unit editor, **Then** a sheet gives one sentence on why the Photos Consent is needed, with "Give consent" and "Not now"; the iOS camera prompt appears only after "Give consent", and nothing about the microphone or Health is asked.
- `/r` **Given** "Not now" was tapped (or the iOS prompt declined), **When** the eater returns to Today and taps a recent Unit, **Then** the Entry appears and the remaining figure changes within 300 ms (NFR-02).
- `/r` **Given** "Health: read workouts" is Given in the app but iOS returns no workouts, **When** Today shows Activity, **Then** it reads "No data from Apple Health yet", never "denied" or "0 kcal" (P30, FR-067, EX-27).
- `/s` **Given** the Photos Consent is not given, **When** the Unit editor's photo control is exercised in a UI test, **Then** no camera session starts.

## 1C · Tracking before a Target

#### eater-1.7 · Log my first food before any profile or Target
Covers: WF-1 "Tracking works before a target exists (brief §3.2)"; FR-001 (local trial); FR-008 ("Users may track without receiving an automated weight-change prescription"); FRD §3.2 · E17, E25, E28 · EX-03
As the eater with food already on the table, I log within two screens of opening the app, so that my food does not go cold while I answer questions.
- `/r` **Given** a fresh install, **When** Hala enters her age and taps "Keep it on this iPhone for now", **Then** Today opens after exactly two screens (Onboarding · Age and Onboarding · Consents), with the Add button in the middle band within one thumb's reach.
- `/r` **Given** Today with no Units, **When** Hala taps Add, searches «عيش بلدي», picks the Food "Baladi bread" and enters «٨٠» g, **Then** the Entry «عيش بلدي · ٨٠ جم» appears with its kcal and Evidence badge, and the Day's consumed total rises by that kcal.
- `/s` **Given** the local trial, **When** the Entry is created, **Then** it is stored on the iPhone with an entry id, a command id and a diary-day id (FR-040 fields), and no request reached `/v1/consumption`.
- `/r` **Given** no network, **When** Hala logs a calorie-only amount "250 kcal · lunch plate", **Then** the Entry shows the "user-defined" badge, macros read "unknown", and the Day total rises by 250 (FR-016, AT-16).

#### eater-1.8 · Today without a Target is honest and quiet
Covers: WF-1 "Tracking works before a target exists"; FRD §14 Today mandatory states ("Empty, partial day … missing macros"); FR-008 · E17, E18 · EX-19, EX-27, EX-42
As the eater with no Target yet, I see what I ate with no made-up budget and one calm way to set a Target, so that the app neither nags nor pretends.
- `/r` **Given** Hala has Entries and no Target, **When** Today opens, **Then** the headline shows consumed kcal and "No Target yet", with no "left" or "over" figure and no macro progress bars (consumed macro grams are listed instead), and one card "Set a target" with "Not now".
- `/r` **Given** "Not now" was tapped, **When** Hala opens Today on 3 later Days, **Then** the card stays hidden, Settings → Goals still offers "Set a target", and no notification or badge asks for one.
- `/r` **Given** an empty Today (no Entries, no Target), **When** it opens, **Then** it shows what to do next with two buttons: "Log what you ate" and "Make your first unit" (EX-19).

#### eater-1.9 · Make a Unit and see its nutrition before any plan
Covers: FRD §3.2 ("A user may create a food unit and understand its nutrition before completing a weight-management plan"); WF-2 (input path); AT-13 · E2 · EX-03
Shared: eater WF-2 journey (Unit editor).
As the eater with no Target, I save "my cheese bite" and see what it holds, so that I can start with my own food before any plan.
- `/r` **Given** Hala in the local trial with no Target, **When** she saves «قرصة جبنة» in the Unit editor as 5.4 g cheese + 1.5 g oil + 8 g bread, **Then** My Units lists it with its three components and its kcal and macros per bite, and Today's consumed total is unchanged (WF-2 done-when, AT-13).
- `/r` **Given** the saved Unit, **When** she opens it from My Units, **Then** nothing on the screen asks for a Target, a profile or an account.

## 1D · Profile

#### eater-1.10 · Enter age, height and weight in my units and my digits
Covers: WF-1 step "profile"; FR-002 ("age, height, weight, preferred units, time zone"); FRD §14.1 (Arabic-Indic and Western numerals, decimal input); blueprint §1.6 (display units) · E40, E41 · EX-10, EX-39
As the eater, I enter my body measures the way I write numbers and in units I know, so that the calculation starts from true values.
- `/r` **Given** Hala on Onboarding · Profile in Arabic, **When** she types height «١٦٠» and weight «٧٨», **Then** the fields read «١٦٠ سم» and «٧٨ كجم», and age already reads «٣٤» from the age gate.
- `/m` **Given** the number parser, **When** it reads «٧٨٫٥», «٧٨.٥» and "78.5", **Then** each gives 78.5.
- `/r` **Given** Sam on Onboarding · Profile in English with body weight in lb, **When** he types 185, **Then** the field reads "185 lb" with "83.9 kg" beneath it, and the stored value is 83.91459 kg (185 × 0.45359237).
- `/r` **Given** Hala switched energy to kJ in the units row, **When** Onboarding · Review later shows her Target, **Then** it reads "6,192 kJ a day" (1,480 × 4.184, EA13).

#### eater-1.11 · Impossible values are caught beside the field, and slips get a fix
Covers: FR-006 ("Reject negative values and invalid units"); FR-002 · care.md group 4 ("quietly fix an obvious slip") · EX-23
As the eater, I see a wrong number flagged where I typed it, so that I fix it in a second and never get a Target from a typo.
- `/r` **Given** Onboarding · Profile, **When** weight "−5" or "0" is typed, **Then** the weight field says "Enter a weight above 0" and Continue is disabled.
- `/r` **Given** height "1.60" with the unit cm, **When** typed, **Then** the field offers "Did you mean 160 cm?" with one tap to accept; nothing changes unless it is tapped.
- `/r` **Given** weight 450 kg or height 300 cm, **When** typed, **Then** "Check this number" appears beside the field (EA2) and Continue stays disabled.
- `/s` **Given** `POST /v1/goals/proposals` with `weight_kg: -5`, or with height given in "ml", **When** received, **Then** it returns 422 `VALIDATION_ERROR` naming the field, and no proposal.

#### eater-1.12 · Choose the equation version knowing why — or skip it
Covers: FR-002 ("Collect the physiological equation coefficient only with an explanation; never infer it from a photograph or gender presentation"); FRD §11.1 ("+5 and −161 for the male and female equation variants") · EX-31, EX-42
As the eater, I choose which published version of the resting-energy equation fits my body after reading why it is asked, so that nothing about me is guessed.
- `/r` **Given** Onboarding · Profile, **When** the equation question shows, **Then** it says that the equation has two published versions that differ by body physiology and that the choice only selects the formula; it offers "+5 (the equation's 'male' version)", "−161 (the equation's 'female' version)" and "Skip — I'll enter a measured value or my own Target", with none preselected.
- `/r` **Given** "Skip", **When** Onboarding · Energy opens, **Then** resting energy reads "Not calculated", with "Enter a measured value", "Enter my own maintenance" and "Track without a target"; no estimate is shown.
- `/s` **Given** no choice was made, **When** `POST /v1/goals/proposals` is called, **Then** the request carries `coefficient: null` and the response has `rmr: null` — never a default or an inferred value.

#### eater-1.13 · Region, time zone, language, numerals and units come from my iPhone, and I can change them
Covers: FR-002 (preferred units, time zone); blueprint §0 line 5 (reach), §1.6 user settings (language, numerals, dialect, display units) · E27 (Saudi Arabia missing as a country in MFP), E41 · EX-05, EX-07
As the eater, I find my region, language, numerals and units already right and can change any of them in one place, so that I don't configure an app before using it.
- `/r` **Given** Faisal's iPhone in Arabic with region Saudi Arabia and Western digits, **When** Onboarding · Profile opens, **Then** one row reads Saudi Arabia · Riyadh time · Arabic · Western digits · kg, cm, kcal (in Arabic, Western digits) with "Change", and every later onboarding screen uses Western digits inside Arabic text.
- `/r` **Given** "Change" (the same choices live later in Settings → Units & language), **When** the region list opens, **Then** it lists Egypt and Saudi Arabia with the current region first; choosing one shows the dialect it sets for food names (Egypt → EG, Saudi Arabia → Gulf; blueprint §1.6, F27) on the same sheet.
- `/s` **Given** the profile is saved, **When** `GET /v1/me` is read, **Then** it returns `time_zone` "Asia/Riyadh", `locale` "ar-SA", `numerals` "western" and `display_units` {mass g, body kg, energy kcal} (FRD §17 UserProfile).

#### eater-1.14 · Take my weight from Apple Health — or type it
Covers: interaction row "HealthKit → app → API: import … body mass"; FR-002; FRD §3.2 · R7; P29, P30 · EX-10, EX-26, EX-27
As the eater who already weighs in with Apple Health, I fill my weight from it after a one-sentence ask, so that I don't retype a number the phone knows — and nothing breaks if I don't.
- `/r` **Given** Onboarding · Profile, **When** "Use my weight from Apple Health" is tapped, **Then** a sheet asks only for the Consent "Health: read body mass" with one sentence why; after "Give consent", the iOS Health sheet lists only Body Mass under read.
- `/r` **Given** Health holds 78.0 kg from 29 Sep 2026, **When** it is read, **Then** the weight field shows 78 kg with "From Apple Health · 29 Sep" and stays editable.
- `/r` **Given** Health returns nothing (no samples, or read access denied — the app cannot tell which, P30), **When** read, **Then** the field says "No weight found in Apple Health" and stays empty for typing; it never says "access denied".

#### eater-1.15 · Tell the app which foods I don't eat — or skip it
Covers: FR-008 ("Capture dietary exclusions … without forcing unrelated sensitive details"); FR-049 (exclusions feed the planner); FRD §19.3 ("Food-allergy absence cannot be certified from a photo") · EX-32
Shared: eater WF-5 journey (Meal planner).
As the eater, I list foods I never eat, so that plans leave them out — without the app promising more than it can.
- `/r` **Given** Onboarding · Profile, **When** "Foods I don't eat (optional)" is opened, **Then** it offers a short list and a text field with nothing preselected, and "Skip" continues with none.
- `/r` **Given** Hala adds "peanuts", **When** the list closes, **Then** the line under it reads "Plans will leave these out. A photo can't show whether a dish is free of them."
- `/r` **Given** peanuts are excluded, **When** the Meal planner opens later, **Then** "peanuts" is listed among its exclusions (FR-049).

## 1E · Safety screen

#### eater-1.16 · Skip the safety screen without losing anything
Covers: WF-1 step "optional safety screen"; FR-008 ("optional safety-screening information without forcing unrelated sensitive details") · EX-31, EX-44
As the eater, I can skip the safety questions, or any one of them, so that I am never forced to share health details to get a Target.
- `/r` **Given** Onboarding · Safety screen, **When** it opens, **Then** it says the questions are optional and why they are asked ("to check a calorie target is right for you"), shows "Skip" at the same size as "Continue", and gives each question a "Prefer not to say" answer.
- `/r` **Given** "Skip", **When** Onboarding · Energy and Onboarding · Target follow, **Then** Hala's standard proposal appears (Target 1,480), with no message about the skipped screen.
- `/s` **Given** "Skip", **When** the stored mode is read back, **Then** it is `standard` with `screen: skipped`, and no answers.

#### eater-1.17 · Answers that suggest risk give tracking-only, in neutral words
Covers: interaction row "answer the optional safety screen … ≥2 SCOFF yes … → tracking-only"; FR-008; FRD §3.3 ("Unsupported or clinically sensitive cases remain in tracking-only mode with appropriate guidance"), §11.4 ("do not generate restrictive plans"); WF-1 done-when ("neutral wording") · R37, R38 · EX-42, EX-44
Shared: eater + nutrition approver (approver-10.53 sets the cut-off and the guidance).
As the eater whose answers suggest a calorie target could harm me, I get full tracking without a weight-change Target, explained without alarm, so that I keep a useful diary without being labelled.
- `/r` **Given** an eater answers "Yes" to 2 of the 5 SCOFF-style questions on Onboarding · Safety screen (Policy v1 cut-off ≥2), **When** they tap Continue, **Then** the next screen shows the approver's guidance in the eater's language with one button "Continue with tracking", and Onboarding · Energy, Target and Macros are not shown.
- `/r` **Given** 1 "Yes" and 4 "No", **When** they continue, **Then** the standard Target flow follows.
- `/m` **Given** the rule with Policy v1, **When** the answers are (yes, yes, no, no, no), (yes, prefer not to say × 4) and (no × 5), **Then** the modes are `tracking_only`, `standard` and `standard`.
- `/m` **Given** the guidance strings in English and Arabic, **When** scanned, **Then** none names a diagnosis (FRD §19.3 "avoid diagnosis").

#### eater-1.18 · Pregnancy or breastfeeding gives tracking-only
Covers: WF-1 done-when ("A safety-screen answer of pregnancy gives tracking-only mode with neutral wording"); interaction row safety screen ("pregnancy or breastfeeding → tracking-only"); FRD §1.3 (excluded: unsupervised pregnancy weight planning), §3.3 · R32 (NIDDK's planner is "not … for pregnant or breastfeeding women", r1-refute-b) · EX-44
Shared: eater + nutrition approver (approver-10.53: pregnancy and breastfeeding are locked triggers).
As a pregnant or breastfeeding eater, I keep tracking without the app setting a weight-change Target, so that I follow my clinician, not a formula.
- `/r` **Given** "Are you pregnant?" answered "Yes" on Onboarding · Safety screen in English, **When** Continue is tapped, **Then** the guidance says in neutral words that Sips & Bytes doesn't set calorie targets during pregnancy and that logging, Units and reports all work, with one button "Continue with tracking"; the words "diet", "lose" and "restrict" do not appear.
- `/r` **Given** "Are you breastfeeding?" answered «نعم» in Arabic, **When** Continue is tapped, **Then** the same guidance appears in Arabic, right to left.
- `/s` **Given** either answer, **When** the mode is saved, **Then** `PUT /v1/me/safety-mode` carries `tracking_only` and no field naming pregnancy or breastfeeding, and `POST /v1/goals/versions` for that eater returns 422 `VALIDATION_ERROR` with reason `tracking_only`.

#### eater-1.19 · Tracking-only is the whole app, without a weight-change Target
Covers: WF-1 done-when (tracking-only with neutral wording); FR-008; FRD §11.4 ("retain neutral tracking and access to existing data") · E24 · EX-43, EX-44
Shared: eater + support agent (support-10.14: a Support agent's view shows "No Target", never the mode).
As an eater in tracking-only mode, I log, make Units, read reports and export like everyone else, so that the mode never feels like a lesser app or shows itself to people near me.
- `/r` **Given** an eater in tracking-only mode, **When** Today opens, **Then** it shows consumed kcal, macro grams and "No Target", as for an eater who never set one, but without the "Set a target" card; the words "tracking only" do not appear on Today.
- `/r` **Given** the same eater, **When** they log a recent Unit, open My Units, open Progress and prepare an export, **Then** each works as for a standard eater, and Progress shows intake and coverage with no "left" or "over".
- `/r` **Given** Settings → Goals, **When** opened, **Then** it reads "Tracking only — no calorie target is set" with the guidance and "Retake the safety questions", and has no "Enter my own target" control (conflict C-2).
- `/m` **Given** the day-report projection for an eater with no Target, **When** built, **Then** its target, remaining and over fields are null, never 0.

#### eater-1.20 · On a GLP-1 medicine: protein first, no added deficit
Covers: interaction row safety screen ("GLP-1 → protein-first, no added deficit"); blueprint §1.6 Policy ("GLP-1 protein-first 1.2–1.6 g/kg"); FR-005, FR-058 · R35, R41 · E24 · EX-42
Shared: eater + nutrition approver (approver-10.52).
As an eater taking a GLP-1 medicine, I get a Target that adds no deficit and puts protein first, so that the plan supports me instead of cutting further.
- `/r` **Given** Faisal (80 kg, maintenance 2,040) answered "Yes" to the GLP-1 question, **When** Onboarding · Target opens with Lose selected, **Then** the Target reads 2,040 kcal a day with "No added deficit while you take a GLP-1 medicine. Follow your prescriber's advice." and no "below maintenance" line.
- `/r` **Given** the same, **When** Onboarding · Macros opens, **Then** protein reads "96–128 g a day (1.2–1.6 g per kg)" set at 96 g and locked, carbohydrate 237 g and fat 79 g share the rest in the default 40:30 ratio, and the protein control moves only between 96 and 128 g.
- `/m` **Given** resting 1,700, multiplier 1.2, weight 80 kg and Policy v1 with GLP-1, **When** the proposal is computed, **Then** target 2,040.0, protein 96.0 g locked, carbohydrate 236.571 g and fat 78.857 g.
- `/r` **Given** Faisal's approved Target, **When** Today opens, **Then** it shows "Target 2,040" in Western digits inside Arabic text, and nothing on Today mentions the medicine.

#### eater-1.21 · My safety answers stay on my iPhone; only the mode is kept
Covers: interaction row "SafetyScreen"; FR-008; FRD §17 UserProfile ("Does not store health preferences in analytics events"), §19.2 (no sensitive profile values in logs) · R3, R21 · EX-29, EX-31
Shared: eater + support agent (support-9.5, support-10.14).
As the eater, I know my answers about eating, pregnancy or medicine are neither stored nor seen by anyone, so that I can answer honestly.
- `/r` **Given** Onboarding · Safety screen, **When** it opens, **Then** a line reads "Your answers stay on this iPhone. Only the result is saved: standard, tracking only or protein first."
- `/s` **Given** any completed screen, **When** the app's requests are recorded, **Then** `PUT /v1/me/safety-mode` carries only the mode, the screen version and the time, and no request body, structured log line or crash report contains an answer (EA3).
- `/s` **Given** the iPhone's local database after the screen, **When** inspected in a UI test, **Then** it stores the mode and no answer.
- `/s` **Given** every `/v1/support/*` response about that eater (support-9.5), **When** its JSON is scanned, **Then** it holds no `mode` key and no answer.

#### eater-1.22 · Retake or clear the safety screen later
Covers: FR-008; FRD §11.4; FR-058, FR-071 (past Days keep their Target) · EX-44
As the eater whose situation changed — after a pregnancy, or after stopping a medicine — I retake the questions from Settings, so that my mode follows my life without contacting anyone.
- `/r` **Given** an eater in tracking-only mode, **When** they tap Settings → Goals → "Retake the safety questions" and answer with no trigger, **Then** Onboarding · Energy opens next; after approval Today shows a Target, and the earlier tracking-only Days still read "No Target" in Progress.
- `/r` **Given** Faisal in protein-first mode, **When** he retakes the questions and answers the GLP-1 question "No", **Then** the next Lose proposal adds the standard deficit (1,734 → 1,730 kcal a day) and protein is no longer locked.
- `/s` **Given** a retake that changes the mode, **When** stored, **Then** a new mode record with its own time is kept, and no earlier Target version is edited.

## 1F · Resting energy and maintenance

#### eater-1.23 · See my resting energy, its method and a ±10 % note
Covers: WF-1 step "resting energy"; WF-1 done-when ("sees resting energy (±10 % note)"); interaction row "set a target … Mifflin–St Jeor estimate ±10 % (R36)"; FR-056; FRD §11.1 · R36 · E40 · EX-32
As the eater, I see my estimated resting energy with how it was worked out and how far off it may be, so that I trust it as an estimate, not a test result.
- `/r` **Given** Hala's profile, **When** Onboarding · Energy opens, **Then** it reads "Resting energy about 1,449 kcal a day", "Estimated with the Mifflin–St Jeor equation from your age, height, weight and the version you chose", "An estimate, not a measurement: for many people it lands within 10 % of a measured value — here 1,304 to 1,594 kcal", and "Often called BMR".
- `/r` **Given** the same in Arabic with Arabic-Indic digits, **When** shown, **Then** the numbers read «١٬٤٤٩» and «١٬٣٠٤–١٬٥٩٤» and the note «±١٠٪», with no digit order reversed (E40).
- `/m` **Given** the resting-energy function, **When** called with (78 kg, 160 cm, 34 y, −161), (80, 176, 41, +5) and (60, 156, 55, −161), **Then** it returns exactly 1,449.0, 1,700.0 and 1,139.0.

#### eater-1.24 · Use a measured resting value instead
Covers: FR-004 ("measured resting expenditure. Preserve its source and effective date"); FRD §11.1 ("alternative measured-value override"), §11.3
As an eater whose resting energy was measured, I enter that value and where it came from, so that the plan uses my measurement and remembers its source.
- `/r` **Given** Sam on Onboarding · Energy, **When** he taps "Enter a measured value", types 1,779, picks the source "Measured at a clinic" and the date 20 Sep 2026, **Then** resting energy reads "1,779 kcal a day · measured · 20 Sep 2026", and the ±10 % note is replaced by "Your measured value".
- `/s` **Given** Sam approves later, **When** `GET /v1/goals/versions` is read, **Then** the version's input snapshot holds `rmr` 1,779, `rmr_source` "measured_clinic", `rmr_effective_date` 2026-09-20 and `method` "measured".
- `/r` **Given** "0" or "−1,779" is typed, **When** entered, **Then** the field says "Enter a value above 0" and nothing is used.

#### eater-1.25 · See maintenance, the activity assumption, and whether exercise is in it
Covers: WF-1 step "maintenance"; FR-057 ("reviewed activity policy … Store whether exercise is included"); FRD §11.3 (worked example) · E18 · EX-14
As the eater, I see how my maintenance is built from resting energy, everyday activity and planned exercise, so that I know what the Target already contains.
- `/r` **Given** Sam (resting 1,779, planned exercise 200 kcal a day), **When** Onboarding · Energy shows maintenance, **Then** it reads "Maintenance about 2,335 kcal a day = 1,779 × 1.2 for everyday activity + 200 planned exercise" and "Your planned exercise is included."
- `/r` **Given** Hala left planned exercise empty, **When** shown, **Then** it reads "Maintenance about 1,739 kcal a day = 1,449 × 1.2 for everyday activity" and "No planned exercise included."
- `/m` **Given** (1,779, 1.2, 200) and (1,449, 1.2, 0), **When** maintenance is computed, **Then** it returns exactly 2,334.8 and 1,738.8.
- `/s` **Given** Sam approves, **When** the version is read, **Then** `maintenance` 2,334.8, `multiplier` 1.2, `planned_exercise_kcal` 200, `exercise_included` true and `policy_version` v1.

#### eater-1.26 · Use my own maintenance value
Covers: FR-057 ("or approved manual value"); FR-004 (source and effective date); FR-007
As an eater who knows my maintenance from a clinician or my own records, I enter it instead of the estimate, so that the Target starts from a number I trust.
- `/r` **Given** Huda on Onboarding · Energy, **When** she taps "Enter my own maintenance", types 1,500, picks the source "From my clinician" and answers "Is your usual exercise already in this number?" with "Yes", **Then** maintenance reads "1,500 kcal a day · from your clinician · exercise included", and Onboarding · Target's Lose proposal becomes 1,280 kcal a day.
- `/s` **Given** her approval, **When** the version is read, **Then** `maintenance_source` "clinician", `maintenance` 1,500, `exercise_included` true and `effective_from` the approval Day.

#### eater-1.27 · Every energy number comes from code, never from the AI
Covers: FR-056 ("calculate in code, never by generated prose"); FRD §1 principle ("Deterministic code calculates"), §16.2 (the AI may not change user goals); NFR-01 · E15, E22 · EX-14
As the eater, I get the same resting energy, maintenance and Target every time I enter the same numbers, so that my Target never drifts like a chatbot's answer.
- `/s` **Given** Hala's Target flow run on 2026-10-01 and again on 2026-10-08 with the same inputs and Policy v1, **When** both proposals are compared, **Then** every number is identical, and the Gemini adapter mock recorded 0 calls in both runs.
- `/m` **Given** the golden cases in the fixtures table (Hala, Sam, Faisal, Huda, Amal), **When** the proposal module runs, **Then** each value matches to the stated decimals.
- `/r` **Given** the AI kill switch is on (platform admin lens), **When** Hala runs the Target flow on the simulator, **Then** every screen works and shows the same numbers (AT-32 pattern).

## 1G · Target

#### eater-1.28 · Choose lose, maintain or gain and see the Target with its deficit or surplus
Covers: WF-1 step "target … (floor policy)"; interaction row "set a target … policy defaults … deficit cap within AHA's 500–750 kcal"; FR-003 ("Offer lose, maintain, or gain weight"); FR-058 ("explicit deficit/surplus"); FRD §3.3 defaults (0 %, 15 % below, 10 % above) · R33 · EX-09, EX-42
As the eater, I pick lose, maintain or gain and see the proposed Target and exactly how far it sits from maintenance, so that I know what I am agreeing to.
- `/r` **Given** Hala (maintenance 1,738.8) on Onboarding · Target, **When** she selects Lose, **Then** it reads "Target 1,480 kcal a day · 261 kcal below maintenance (15 %)"; Maintain reads "1,740 kcal a day · same as maintenance"; Gain reads "1,910 kcal a day · 174 kcal above maintenance (10 %)".
- `/r` **Given** Sam (maintenance 2,334.8), **When** he selects Lose, **Then** it reads "Target 1,980 kcal a day · 350 kcal below maintenance (15 %)".
- `/m` **Given** maintenance 3,600, **When** the loss deficit is computed, **Then** it is 500 kcal (the cap), not 540; for 2,334.8 it is 350.22 (approver-10.51).
- `/r` **Given** Onboarding · Target opens, **When** no goal has been chosen, **Then** none is preselected and Continue is disabled until one is.

#### eater-1.29 · A review date, and no promised weekly weight change
Covers: FR-003 ("proposed review date before approval"); FR-059 ("Show a range/uncertainty note for projected progress; do not promise a fixed weekly weight change from a calorie formula") · EX-32
As the eater, I see when to look at my Target again and an honest note about progress, so that I am never promised a number of kilos.
- `/r` **Given** Hala selects Lose on 1 Oct 2026, **When** Onboarding · Target shows the proposal, **Then** it reads "Review on 15 Oct 2026" and "Weight change differs from person to person. Progress will show your trend once there is enough data."
- `/m` **Given** every string of Onboarding · Target and Onboarding · Review in English and Arabic, **When** scanned, **Then** none states a weight per week or per month ("kg a week", "lb a week", «كجم في الأسبوع»).
- `/s` **Given** her approval, **When** the version is read, **Then** `review_date` is 2026-10-15, the approval Day + 14 days (EA6).

#### eater-1.30 · No proposal below the reviewed minimum
Covers: interaction row "set a target … policy floor 1,200 kcal (approver-owned)"; WF-1 done-when ("a target not below the policy floor"); FRD §3.3 (centrally versioned safety boundaries) · R33, r1-refute-b Dropped 12 · EX-42
Shared: eater + nutrition approver (approver-10.49, approver-10.57).
As the eater whose 15 % loss would fall below the reviewed minimum, I get a Target at the minimum with a calm reason, so that the app never proposes too little.
- `/r` **Given** Huda (maintenance 1,366.8), **When** she selects Lose, **Then** the Target reads "1,200 kcal a day · 167 kcal below maintenance (12.2 %)" with the line "We don't propose targets below 1,200 kcal a day, the reviewed minimum."
- `/r` **Given** Amal (maintenance 1,162.8), **When** Onboarding · Target opens, **Then** Lose is shown unavailable with "Your maintenance is already close to the reviewed minimum of 1,200 kcal a day", and Maintain reads "1,200 kcal a day" (conflict C-1).
- `/r` **Given** Policy v2 (floor 1,300) is live from 15 Oct 2026 (approver-10.57), **When** Huda starts a new proposal on 16 Oct, **Then** Lose reads 1,300.
- `/s` **Given** `POST /v1/goals/versions` with Target 1,150 for Huda, **When** received, **Then** it returns 422 `POLICY_FLOOR` with `limit` "floor", the floor value and the Policy version.

#### eater-1.31 · A Target below 1,000 kcal is never accepted
Covers: interaction row "set a target … hard stop below 1,000 (R32)"; FRD §19.3 ("Dangerous restriction requests require a safe response rather than a mathematically optimized starvation plan") · R32 (NIDDK resets the last change and suggests more time, a different activity level or a different goal) · EX-42
Shared: eater + nutrition approver (approver-10.50).
As the eater typing a very low number, I see the field reset and kinder options, so that no setting can make the app plan starvation.
- `/r` **Given** Huda's Target field holds 1,200 on Onboarding · Target, **When** she types 900 under "Enter my own target", **Then** the field returns to 1,200 and reads "Sips & Bytes doesn't set targets below 1,000 kcal a day. You could allow more time, add activity, or choose Maintain." with Maintain offered.
- `/r` **Given** she types 1,100, **When** entered, **Then** the value stays, the field reads "Below the reviewed minimum of 1,200 kcal a day", "Approve target" is disabled, and two buttons offer "Use 1,200" and "Track without a target".
- `/s` **Given** `POST /v1/goals/versions` with 999 kcal and any source (self-entered or clinician), **When** received, **Then** it returns 422 `POLICY_FLOOR` with `limit` "hard_stop".

#### eater-1.32 · Enter my own or my clinician's Target, with its source and date
Covers: FR-004 ("Allow a manually entered or clinician-provided calorie target … Preserve its source and effective date"); FR-058; FRD §11.3 (the 1,870 example) · EX-14
As an eater with a target of my own, I type it and say where it came from, so that the app uses it and remembers its source.
- `/r` **Given** Sam (maintenance 2,334.8) on Onboarding · Target, **When** he taps "Enter my own target", types 1,870 and picks the source "My own number", **Then** it reads "Target 1,870 kcal a day · your own number · 465 kcal below maintenance (19.9 %)" and "Includes your 200 kcal of planned exercise" (FRD §11.3).
- `/r` **Given** the source "From my clinician" instead, **When** approved on 1 Oct 2026, **Then** Settings → Goals reads "1,870 kcal a day · from your clinician · from 1 Oct 2026".
- `/s` **Given** "My own number", **When** `GET /v1/goals/versions` is read, **Then** `target` 1,870, `target_source` "self_entered", `effective_from` the Day of 2026-10-01 in Europe/London, and `maintenance` 2,334.8 in the snapshot.

## 1H · Macros

#### eater-1.33 · See macro grams from percentages, with the 4/4/9 label
Covers: WF-1 step "target and macros"; WF-1 done-when ("macro grams and assumptions"); FR-005; FRD §10.1 (label "share of macro-derived energy (4/4/9)"), §10.3 · E41 · EX-11
As the eater, I see my macro targets in grams worked out from percentages, so that I know what to aim for at a meal.
- `/r` **Given** Hala's Target 1,480 on Onboarding · Macros, **When** it opens, **Then** it shows protein 30 % = 111 g, carbohydrate 40 % = 148 g and fat 30 % = 49 g, labelled "Share of macro-derived energy (4/4/9)", with "Default split from the reviewed policy — change it if you like".
- `/m` **Given** Target 1,480 and the fractions 0.30 / 0.40 / 0.30, **When** converted, **Then** 111.0 g, 148.0 g and 49.333 g, shown as 111, 148 and 49.
- `/r` **Given** Arabic, **When** shown, **Then** the three share bars fill from the right and the grams read «١١١», «١٤٨» and «٤٩» (E41).

#### eater-1.34 · Set a macro in grams instead of percent
Covers: FR-005 ("Allow macro targets by percentage or grams"); FRD §10.2 (largest-remainder display)
As the eater who counts protein in grams, I set protein in grams and let the rest follow, so that the target matches how I think.
- `/r` **Given** Hala's Target 1,480, **When** she switches protein to grams and types 120, **Then** protein reads 120 g (32.4 %), and carbohydrate and fat share the remaining 1,000 kcal in their 40:30 ratio: carbohydrate 143 g (38.6 %), fat 48 g (29.0 %); the shares total 100.0 %.
- `/m` **Given** those inputs, **When** computed, **Then** carbohydrate 142.857 g and fat 47.619 g, and the displayed shares 32.4 / 38.6 / 29.0 come from the largest-remainder method.
- `/s` **Given** her approval, **When** the version is read, **Then** `macro_targets` holds protein {unit g, value 120} and carbohydrate and fat {unit pct} with their fractions.

#### eater-1.35 · Percentages that don't add up to 100 are shown, and normalising waits for me
Covers: FR-006 ("If macro percentages sum to anything other than 100%, offer explicit normalization or editing; do not save contradictory targets"); FRD §3.1; AT-09
As the eater who typed 46 / 32 / 24, I see that it totals 102 % and choose a fix, so that no contradictory target is saved behind my back.
- `/r` **Given** Onboarding · Macros with fat 46 %, carbohydrate 32 % and protein 24 % (AT-09), **When** typed, **Then** "Total 102 %" shows beside the fields, Continue is disabled, and two actions appear: "Use 45.10 % fat, 31.37 % carbohydrate, 23.53 % protein" and "Edit my numbers".
- `/r` **Given** "Use 45.10 % fat …" was tapped, **When** Onboarding · Review opens, **Then** for 1,480 kcal it shows fat 45.10 % = 74 g, carbohydrate 31.37 % = 116 g and protein 23.53 % = 87 g, and nothing is active until "Approve target".
- `/s` **Given** `POST /v1/goals/versions` with 46 / 32 / 24, **When** received, **Then** it returns 422 `VALIDATION_ERROR` with reason `macro_total`, the total 102 and the normalised alternative, and no version is stored.
- `/m` **Given** 46 / 32 / 24, **When** normalised, **Then** 45.098…, 31.372… and 23.529…, and the displayed 45.10 / 31.37 / 23.53 total 100.00.

#### eater-1.36 · Lock protein grams, and a new calorie Target leaves them alone
Covers: FR-005 ("A changed calorie target shall not silently change a user-locked protein-gram target")
As the eater who locked protein at 111 g, I change my calorie Target and keep my protein, so that one change never quietly moves another.
- `/r` **Given** Hala's approved Target 1,480 with protein locked at 111 g, **When** she changes the Target to 1,600 from Settings → Goals, **Then** Onboarding · Review shows protein 111 g (locked, unchanged), carbohydrate 148 → 165 g and fat 49 → 55 g, each change marked, before "Approve target".
- `/m` **Given** Target 1,600 and protein 111 g locked, **When** recomputed, **Then** carbohydrate 165.143 g and fat 55.048 g.
- `/s` **Given** the new version, **When** read, **Then** protein is 111 g with `locked` true, and the 1,480 version is unchanged.

#### eater-1.37 · Locks that can't all hold are shown, never resolved for me
Covers: FR-005 ("Resolve incompatible locks visibly"); FR-006 ("do not save contradictory targets")
As the eater who locked all three macros in grams, I am told when they no longer fit the calorie Target and choose what to change, so that the app never picks for me.
- `/r` **Given** Target 1,480 with protein 111 g, carbohydrate 148 g and fat 70 g all locked, **When** Onboarding · Macros shows them, **Then** it reads "Your locked macros need 1,666 kcal; your Target is 1,480 kcal" with "Unlock protein", "Unlock carbohydrate", "Unlock fat" and "Change the Target", and Continue is disabled.
- `/s` **Given** the same locks posted to `POST /v1/goals/versions`, **When** received, **Then** it returns 422 `VALIDATION_ERROR` with reason `locks_incompatible`, the needed 1,666 kcal and the Target 1,480.
- `/m` **Given** three gram locks whose 4/4/9 energy is within 5 kcal of the Target (EA8), **When** validated, **Then** they are accepted; 6 kcal apart, they are not.

#### eater-1.38 · Wrong macro input is refused beside the field
Covers: FR-006 ("Reject negative values and invalid units")
As the eater, I see a slip in a macro field flagged where I typed it, so that nothing contradictory is saved.
- `/r` **Given** Onboarding · Macros, **When** protein "−10 %", carbohydrate "140 %" or fat "abc" is typed, **Then** that field says "Enter a share from 0 to 100 %" (or "Enter a number"), and Continue is disabled.
- `/r` **Given** grams mode, **When** "−5" is typed, **Then** the field says "Enter 0 g or more".
- `/s` **Given** `POST /v1/goals/versions` with a macro unit "ml", **When** received, **Then** it returns 422 `VALIDATION_ERROR` naming the field.

## 1I · Activity mode

#### eater-1.39 · Answer whether my usual exercise is already in my maintenance
Covers: WF-1 step "activity mode"; FR-007 ("Ask whether ordinary exercise is already included in the selected maintenance estimate"); FR-057; FRD §11.3 ("not a 1,870 'net-food' allowance to which the same 200 kcal is added again"), §12.1; AT-23 · E18 · EX-10, EX-14
Shared: eater WF-7 journey (Activity).
As the eater, I answer whether my usual exercise is already counted, so that the same workout is never counted twice.
- `/r` **Given** Sam's maintenance includes 200 kcal of planned exercise, **When** Onboarding · Activity mode opens, **Then** the question "Is your usual exercise already in your maintenance?" is answered "Yes — 200 kcal of planned exercise is included" (editable), and "Fixed target" is selected with the line "Exercise you log or import shows beside your food; it doesn't add to your Target."
- `/r` **Given** Sam approved Target 1,870 with Fixed target and a 200 kcal workout is then imported from Apple Health, **When** Today opens, **Then** the Target stays 1,870 and the remaining figure does not change; the workout shows as Activity beside the food (AT-23).
- `/r` **Given** Hala (no planned exercise), **When** the screen opens, **Then** the question reads "No exercise is included", with Fixed target selected by default (FRD §12.1).

#### eater-1.40 · Choose the Activity-adjusted target knowingly
Covers: FR-007; FRD §12.2 ("a documented baseline that excludes the selected exercise component … a visible user-approved credit factor and cap … never simultaneously apply an all-in multiplier and full wearable expenditure") · E18 · EX-14
Shared: eater WF-7 journey (Activity).
As the eater who wants exercise to add to my food budget, I choose that mode and approve how much counts, so that exercise is never counted twice or without my say.
- `/r` **Given** Sam (maintenance 2,334.8 with 200 planned exercise), as an alternative to eater-1.39, **When** he chooses "Activity-adjusted target", **Then** the screen shows the baseline without planned exercise, "2,135 kcal a day", with "Planned exercise is taken out, so the exercise you log can count instead", and the Lose proposal becomes 1,810 kcal a day.
- `/r` **Given** the same, **When** the credit controls show, **Then** "Count 50 % of eligible exercise, up to 300 kcal a day" (EA7) must be confirmed before Continue, and the formula reads "Today's target = 1,810 + approved exercise credit".
- `/m` **Given** factor 0.5 and cap 300, **When** an imported net workout of 400 kcal and one of 800 kcal are credited, **Then** the credits are 200 and 300 kcal.
- `/s` **Given** his approval, **When** the version is read, **Then** `activity_mode` "adjusted", `exercise_included` false, `baseline` 2,134.8, `credit_factor` 0.5 and `credit_cap` 300.

#### eater-1.41 · Connect Apple Health here, or not — the Target never depends on it
Covers: FRD §3.2 ("Goals, permissions, and health connections are separate choices. Declining activity access must not block manual food logging."); interaction row "Ledger → HealthKit … only after the Health consent"; FR-076 · R7; P29, P30 · EX-26
Shared: eater WF-7 journey (Activity).
As the eater, I can connect Apple Health from the activity step or leave it, so that my Target is set either way.
- `/r` **Given** Onboarding · Activity mode, **When** "Connect Apple Health (optional)" is tapped, **Then** the three Health Consents are asked separately — "Health: read workouts", "Health: read body mass", "Health: write dietary energy" — each with its own switch, all off.
- `/r` **Given** Hala skips it, **When** she approves, **Then** Today shows her Target, Activity reads "Apple Health not connected", and logging works.
- `/s` **Given** no Health Consent, **When** a full UI walk of the Target flow runs, **Then** no HealthKit authorization request is made.

## 1J · Approve

#### eater-1.42 · Review everything once, then approve
Covers: WF-1 step "approve"; WF-1 done-when; interaction row "set a target → GoalPlanVersion … approval → target approved"; FR-003 ("Show the estimated resting requirement, estimated maintenance, intake target, assumptions, and proposed review date before approval"); FR-058 ("Store every accepted target as an effective-dated version"); FRD §17 GoalPlanVersion · EX-09, EX-15
As the eater, I see my whole Target on one screen and approve it with one button, so that I know exactly what I agreed to.
- `/r` **Given** Hala at the end of the Target flow at 10:30 on 1 Oct 2026 (Cairo), **When** Onboarding · Review opens, **Then** it lists resting energy 1,449 (estimate, ±10 %), maintenance 1,739, Target 1,480 (15 % below maintenance), protein 111 g, carbohydrate 148 g, fat 49 g, Fixed target, "Review on 15 Oct 2026", and the assumptions (the equation and the version chosen, everyday activity × 1.2, no planned exercise), with one main button "Approve target".
- `/r` **Given** "Approve target" is tapped, **When** the server confirms, **Then** Today shows "Target 1,480 · Fixed target", and a remaining figure equal to 1,480 minus the Day's consumed total shown beside it.
- `/s` **Given** the approval, **When** `GET /v1/goals/versions` is read, **Then** one version holds `method` "mifflin_st_jeor", the input snapshot (age 34, height 160, weight 78, coefficient −161, multiplier 1.2, planned exercise 0, unrounded Target 1,477.98), `rmr` 1,449.0, `maintenance` 1,738.8, `intake_target` 1,480, the macro targets, `activity_mode` "fixed", `exercise_included` false, `review_date` 2026-10-15, `policy_version` v1 and `effective_from` Day 2026-10-01 (Africa/Cairo).
- `/s` **Given** the approval, **When** weight observations are read, **Then** one WeightObservation of 78 kg with source "profile" exists at the approval time (FRD §17).

#### eater-1.43 · Approve once, even if I tap twice or the network drops
Covers: FR-058; FRD §18 ("All mutation requests use an idempotency key"), §18.2 (bounded retries with the same command ID); AT-10 pattern · EX-13
As the eater, I end up with one Target however many times my approval is sent, so that my history has no phantom versions.
- `/s` **Given** `POST /v1/goals/versions` delivered three times with one `command_id`, **When** processed, **Then** one version exists, and the second and third responses return that version.
- `/r` **Given** the network drops just after "Approve target" is tapped, **When** it returns, **Then** Onboarding · Review shows "Approved" once, and Settings → Goals → History lists one version for 1 Oct 2026.
- `/r` **Given** the API mock delays its answer by 5 s, **When** the eater waits, **Then** the button reads "Approving…" and is disabled, and after the answer exactly one version exists.

#### eater-1.44 · Leave halfway and nothing is set; my answers wait
Covers: FR-058 ("user approval"); WF-1 step "approve" · care.md group 4 ("If someone closes a half-filled form, is their work protected?") · EX-25
As the eater interrupted mid-flow, I come back to my answers and no Target exists until I approve, so that a closed app never sets a Target for me.
- `/r` **Given** Hala closes the app on Onboarding · Macros, **When** she reopens it, **Then** Today shows "No Target yet", and "Set a target" resumes at Onboarding · Macros with her values filled in.
- `/r` **Given** she taps "Start over", **When** confirmed, **Then** her answers are cleared and Onboarding · Profile opens with only her age filled.
- `/s` **Given** the abandoned answers, **When** `GET /v1/goals/versions` is read, **Then** no version exists.

#### eater-1.45 · No network: the Target step says why, and logging continues
Covers: WF-1 "Tracking works before a target exists"; FRD §8.3 (offline outbox); NFR-06 · care.md group 4 ("When a command cannot work right now, do we say why?") · E38 · EX-21
As the eater with no signal, I'm told the Target needs a connection while logging keeps working, so that I am never stuck.
- `/r` **Given** no network, **When** Hala taps "Set a target", **Then** Onboarding · Profile opens and takes her answers, and Onboarding · Energy reads "Connect to calculate your Target. You can keep logging meanwhile." with "Back to Today"; "Approve target" is not shown.
- `/r` **Given** the same, **When** she taps a recent Unit on Today, **Then** the Entry appears marked Pending and the total changes (EX-21).
- `/r` **Given** the network returns, **When** she reopens "Set a target", **Then** the flow continues at Onboarding · Energy with her answers.

#### eater-1.46 · Today shows my Target and the activity mode
Covers: WF-1 done-when ("Today shows the target and the activity mode"); FR-007 ("The chosen activity accounting mode shall be visible on the dashboard"); FRD §14 Today · EX-11, EX-35, EX-36
As the eater, I see my Target, what is left and how exercise is treated at a glance, so that the number I read is the number I approved.
- `/r` **Given** Hala's approved Target 1,480 (Fixed target), **When** Today opens on Day 2026-10-01, **Then** the headline shows the remaining kcal in large type, "Target 1,480" beneath it, and "Fixed target" as a word label, not a colour alone (EX-35).
- `/r` **Given** Sam in Activity-adjusted mode (eater-1.40) with a 400 kcal imported workout, **When** Today opens, **Then** it reads "Activity-adjusted target · today 2,010 (1,810 + 200 exercise credit)".
- `/r` **Given** VoiceOver, **When** the headline is focused, **Then** it reads the remaining kcal, the Target, the mode and the Day's date.

## 1K · Later changes

#### eater-1.47 · Change my Target later as a new version; past Days keep theirs
Covers: FR-058 (effective-dated versions); FR-071 ("Editing today's target must not rewrite last month's performance"); FRD §17 GoalPlanVersion ("Past days retain their effective plan") · E15, E16 · EX-14
Shared: eater WF-8 journey (Progress); nutrition approver (approver-10.58).
As the eater changing my Target, I keep my history as it was, so that last week is read against last week's Target.
- `/r` **Given** Hala's Target 1,480 from Day 2026-10-01, **When** she approves 1,600 during Day 2026-10-10 in Settings → Goals, **Then** Settings → Goals → History lists "1,480 · 1–9 Oct 2026" and "1,600 · from 10 Oct 2026", and Progress shows Day 2026-10-03 against 1,480 and Day 2026-10-10 against 1,600.
- `/s` **Given** both versions, **When** `GET /v1/reports/day?day=2026-10-03` and `?day=2026-10-10` are read, **Then** their `target_version` is the 1,480 version and the 1,600 version.

#### eater-1.48 · A Policy change never changes my Target by itself
Covers: FRD §3.3 ("centrally versioned product policies"); FR-058; FR-071 · E16 · EX-14
Shared: eater + nutrition approver (approver-10.58).
As the eater, I am told calmly when the reviewed minimum changes and decide myself, so that my Target changes only when I approve.
- `/r` **Given** an eater's Target 1,250 approved on 20 Sep 2026 and Policy v2 (floor 1,300) live from 15 Oct 2026, **When** Today opens on 15 Oct, **Then** a calm notice reads "The reviewed minimum is now 1,300 kcal a day. Your Target stays 1,250 until you review it." with "Review my target", and the Target is unchanged.
- `/s` **Given** the same, **When** `GET /v1/reports/day?day=2026-10-14` and `?day=2026-10-15` are read, **Then** both show Target 1,250.
- `/r` **Given** "Review my target" is tapped, **When** Onboarding · Target opens, **Then** no Lose proposal is below 1,300.

## 1L · Local trial, account and the move

#### eater-1.49 · Use Sips & Bytes as a local trial — no account
Covers: FR-001 ("a local trial"); FRD §3.2; WF-1 "Tracking works before a target exists" · E17, E28 · EX-03
Shared: eater + support agent (support-9.2: a local-trial eater has no support code).
As the eater trying the app, I log and make Units without an account, so that I can judge it before giving anything.
- `/r` **Given** Hala chose "Keep it on this iPhone for now", **When** she uses Today, My Units and Templates for two Days (T1), **Then** everything works, and the account line of Settings reads "Not signed in · Your diary is only on this iPhone" with "Create account".
- `/r` **Given** the local trial, **When** she taps "Set a target" on Today, **Then** Onboarding · Account opens with "Your Target and profile are kept in your account" and "Not now" (conflict C-3).
- `/s` **Given** the local trial, **When** the API mock's record of T1 is read, **Then** no Entry, Unit, Template or Consent was sent; only public Food reference searches were (EA12).

#### eater-1.50 · Use AI in the trial through an anonymous session, with my Consent and a daily limit
Covers: FR-001 ("Cloud AI requires authenticated or anonymous-session access, consent, and quotas"); FR-076; blueprint §1.6 Registry ("per-user daily AI quotas"); FRD §16.5 · R2 · EX-22
Shared: eater + platform admin (admin-10.35, admin-10.36, admin-10.39).
As a trial eater, I analyse a photo after giving the AI Consent, without an email, and am told plainly when today's limit is used up, so that I can try the AI and still log.
- `/r` **Given** Hala in the local trial without the AI Consent, **When** she taps Meal on Capture & Plan, **Then** a sheet shows the AI Consent wording with "Give consent" and "Not now"; "Not now" returns to Capture & Plan, where the Unit mode still works.
- `/r` **Given** she gives the Consent, **When** she takes a meal photo, **Then** an anonymous session starts without asking for an email, and Analysis review opens on the new Analysis.
- `/r` **Given** the anonymous-session limit of 3 photo analyses a day (admin-10.35) is used, **When** she takes a 4th photo, **Then** Capture & Plan reads "Photo analysis is used up for today. It resets at 00:00." and still offers recent Units, Templates and typed amounts (EX-22).
- `/s` **Given** the 4th call, **When** `POST /v1/analyses` is made, **Then** it returns 429 `RATE_LIMITED` with `resets_at` 2026-10-02T00:00+03:00, and `POST /v1/consumption` with a recent Unit is still accepted.

#### eater-1.51 · Create an account and bring my trial diary across, once
Covers: FR-001 ("account creation and safe migration without duplicate units or meals"); interaction row consents ("diary processing"); FRD §8.3 (UUID per command); FR-042 (replay reproduces totals) · R4 (Sign in with Apple), R22 · E28
As a trial eater, I create an account and find every Unit, Template and Entry exactly once with the same Day totals, so that nothing is lost or doubled.
- `/r` **Given** T1 on Hala's iPhone, **When** she opens Onboarding · Account, **Then** it offers "Sign in with Apple" and "Continue with email", and shows the Diary processing Consent as its own switch, off, with "Needed to keep your diary in your account"; the create button stays disabled until that switch is on.
- `/r` **Given** she signs in with Apple and the move completes, **When** she opens My Units and Today, **Then** My Units lists the same 3 Units and 1 Template, Day 2026-09-30 shows 7 Entries and Day 2026-10-01 shows 5, and both Day totals equal those shown before the move.
- `/s` **Given** the move, **When** the account's Units, Templates and the day reports for both Days are read over the API, **Then** the counts are 3, 1 and 7 + 5 Entries, every Entry keeps its original entry id and command id, and replaying the ledger reproduces both totals.
- `/s` **Given** the anonymous session that held her Analyses and her AI Consent, **When** the account is created, **Then** those records belong to the new account (EA11) and the anonymous session no longer exists.

#### eater-1.52 · An interrupted move resumes without duplicates
Covers: FR-001; FR-043 ("Retried commands and duplicate delivery must not add food twice"); AT-10; NFR-06 ("no duplicate replay after reinstall/session recovery")
As the eater whose move stopped halfway, I see it finish once, so that a lost signal never doubles my food.
- `/s` **Given** T1's 16 commands, **When** the network is cut after 7 are accepted and then restored, **Then** the other 9 are sent, the first 7 come back as duplicates of accepted commands, and the server holds 3 Units, 1 Template and 12 Entries.
- `/r` **Given** the app is force-quit mid-move and reopened, **When** Today opens, **Then** a quiet note on the Today headline reads "Moving your diary to your account — 9 left" (no alert), and when done the Days show the same totals as before.
- `/r` **Given** the move has finished, **When** Settings opens, **Then** its account line reads "Signed in with Apple · diary in your account", and no Entry is Pending.

#### eater-1.53 · Sign in to an account that already has data: duplicates are shown, not merged
Covers: FR-001 ("without duplicate units or meals"); FRD §14 My Units mandatory state "duplicate candidate"; FR-014 (versions; existing logs keep theirs); FR-043 ("Near-duplicate human commands should show a warning rather than being automatically discarded")
As an eater who tried the app on a new iPhone before signing in to my existing account, I choose what to keep, so that nothing is silently merged or thrown away.
- `/r` **Given** Hala's account already has «قرصة جبنة» v2 (9 g bread) and her new iPhone's trial has v1 (8 g bread), **When** she signs in, **Then** My Units shows one row marked "Duplicate candidate" (FRD §14) with both versions side by side and the choices "Keep both" and "Use the account's version for new logs"; the trial Entries keep their v1 snapshot either way.
- `/r` **Given** the trial and the account each logged "3 cheese bites" at 08:40 on Day 2026-10-01, **When** the move finishes, **Then** Today shows both Entries with the note "Logged twice? 3 cheese bites at 08:40 on two devices" and a Void button on each; neither is removed automatically.
- `/s` **Given** the move, **When** the Units are read, **Then** no Unit version was edited or deleted by it.

## 1M · Language, access and tone

#### eater-1.54 · Onboard entirely in Arabic
Covers: WF-1 done-when ("a new adult finishes onboarding in English or Arabic"); blueprint §0 line 5 (reach); FRD §14.1 (right-to-left, both numeral systems) · E27, E40, E41 · EX-08, EX-39
As an Arabic-speaking eater, I go from the age gate to an approved Target without meeting an untranslated word or a reversed number, so that Arabic is a first language here.
- `/r` **Given** the simulator in Arabic (Egypt), **When** Hala walks Onboarding · Age → Consents → Account → Profile → Safety screen → Energy → Target → Macros → Activity mode → Review → Today, **Then** every screen is right to left, back points right, the share bars fill from the right, and no English appears except "Google (Gemini)" and Apple's "Sign in with Apple" control.
- `/r` **Given** Arabic-Indic digits, **When** numbers show, **Then** 1,480 reads «١٬٤٨٠», 15 % reads «١٥٪» and the review date «١٥ أكتوبر ٢٠٢٦», with no digit order reversed.
- `/r` **Given** Faisal (Arabic, Western digits), **When** Onboarding · Review shows, **Then** "2,040" and "96" stand in place inside the Arabic text.
- `/m` **Given** the string catalogue, **When** the onboarding keys are checked, **Then** each has an Arabic value, and the map's words (Target, Consent, Unit, Activity, Entry) each use their one fixed Arabic label.

#### eater-1.55 · Onboard with VoiceOver and the largest text
Covers: NFR-08 ("Screen-reader and text-scaling flows pass on every P0 journey"); FRD §14.2 · E36 · EX-33, EX-36, EX-37
As an eater who uses VoiceOver or very large text, I finish the Target flow with nothing clipped or unlabelled, so that the app works for me too.
- `/r` **Given** the largest accessibility text size on the smallest simulator (iPhone 16e/17e, P34), **When** each onboarding screen opens in English and in Arabic, **Then** no text is clipped or overlapping and every control can be reached by scrolling.
- `/r` **Given** VoiceOver on Onboarding · Macros, **When** the eater moves through it, **Then** each control reads its name, value and unit ("Protein, 111 grams, 30 percent, locked"), and the "Total 102 %" message is announced once.
- `/r` **Given** every onboarding control, **When** measured in the UI test, **Then** each hit region is at least 44 × 44 pt (E36).

#### eater-1.56 · Neutral words — no judgment, no shame
Covers: FRD §11.4 ("Do not describe failure to hit calorie targets as moral failure"), §14.2 ("Avoid punitive red warnings … shall not recommend skipping meals"), §19.3 (general wellness) · R37 · EX-42
As the eater, I read neutral words about my body, my goal and my Target, so that setting a Target never feels like being judged.
- `/m` **Given** every onboarding and Settings → Goals string in English and Arabic, **When** scanned against the deny-list ("bad", "cheat", "fail", "guilt", "burn off", "earn", "skip a meal", «غش», «فشل»), **Then** none matches.
- `/r` **Given** Onboarding · Target with Lose selected, **When** shown, **Then** the gap reads "kcal below maintenance" with its number and is not coloured red.
- `/r` **Given** an eater who chose Gain, **When** shown, **Then** the gap reads in the same tone ("174 kcal above maintenance").

---

# Journey 9 — Privacy (WF-9), and the eater's side of a support Grant (WF-10)

Steps, in the map's order (WF-9: "consents, export, delete account"; WF-10: "support requests a Grant → the eater approves or declines it in Settings → support reads within its time box → it expires, every read audited"):
**9A** See and change Consents → **9B** Photos and voice → **9C** Export → **9D** Delete account → **9E** Answer a support Grant → **9F** Language and access.

## 9A · Consents in Settings

#### eater-9.1 · See all my Consents in one place
Covers: WF-9 step "consents"; interaction row "give separate consents … per purpose, one-tap withdrawal"; FR-076 · R3, R22 · EX-07
Shared: eater + auditor (auditor-9.1, auditor-9.2), support agent (support-9.6).
As the eater, I see each Consent, whether it is Given or Withdrawn, since when and what it is for, so that I know exactly what I have agreed to.
- `/r` **Given** Hala signed in, **When** she opens Settings → Privacy, **Then** it lists one row each for Diary processing; Send photos, voice and text to Google's AI (Gemini); Health: read workouts; Health: read body mass; Health: write dietary energy; Microphone; Photos; and Optional research — each with its state (Given, Withdrawn, or "Not given" when no Consent exists), the date of the last change, a switch, and "Read what you agreed to" (or "Read the text" when not given).
- `/r` **Given** "Read what you agreed to" on the AI row, **When** opened, **Then** the exact wording she agreed to shows; if a newer wording exists, a line "A newer wording exists" offers "Read it".
- `/r` **Given** no Consent was ever given except Diary processing, **When** Settings → Privacy opens, **Then** the other rows read "Not given", never blank.

#### eater-9.2 · Withdraw the AI Consent in one tap
Covers: interaction row ("one-tap withdrawal"); FR-076 ("Refusal must preserve unaffected functions"); AT-29 ("consent withdrawal propagate[s] to media, queues, private cached analysis, and exports"); FRD §7.2 · R3, R22 (withdrawing as easy as giving) · EX-22
Shared: eater + auditor (auditor-9.5).
As the eater, I switch off sending to Google's AI with one tap and see what stopped, so that my "no" works now and nothing else breaks.
- `/r` **Given** the AI Consent is Given and one Analysis is Pending (captured offline, not sent), **When** Hala taps the AI switch in Settings → Privacy at 13:05, **Then** it turns off at once with no dialog, and the row reads "Withdrawn · 13:05 · Photo, voice and sentence analysis are off. Units, Templates and typed amounts still log." and "1 photo that wasn't sent was removed".
- `/r` **Given** the AI Consent is Withdrawn, **When** she opens Capture & Plan, **Then** Meal, Label and voice read "Needs your Consent to send to Google's AI" with "Open Settings → Privacy", while the Unit editor and the recent Units on Today work (AT-32 pattern).
- `/s` **Given** the withdrawal at 2026-10-01T10:05Z, **When** the app then calls `POST /v1/analyses`, **Then** it returns 403 `CONSENT_REQUIRED` and the Gemini adapter mock records 0 calls; her private cached analyses, queued uploads and the photos of Analyses never approved are gone from the emulators (the counts on auditor-9.5's effect card).
- `/s` **Given** a Completed export of hers, **When** it is scanned after the withdrawal, **Then** it holds no photo, audio or transcript, so nothing sent under the AI Consent remains in it (AT-29, exports).

#### eater-9.3 · Give a Consent again, in Settings or at the moment I need it
Covers: FR-076; interaction row consents ("explicit, per purpose") · R22 · EX-26
Shared: eater + auditor (auditor-9.2).
As the eater, I turn a Consent back on when I want a feature again, so that coming back is as easy as leaving.
- `/r` **Given** the AI Consent is Withdrawn, **When** Hala taps Meal on Capture & Plan, **Then** a sheet shows the current wording with "Give consent" and "Not now", and after "Give consent" the camera opens.
- `/r` **Given** Settings → Privacy, **When** she turns the AI switch on there, **Then** the same wording sheet shows first, and the row reads "Given" only after "Give consent".
- `/s` **Given** either path, **When** `GET /v1/me/consents` is read, **Then** a new `given` record with its method ("in-app sheet · Capture & Plan" or "Settings") follows the withdrawal, and the earlier records are unchanged.

#### eater-9.4 · Withdraw a Health Consent: imports and write-back stop, and I decide what stays in Health
Covers: interaction rows "Ledger → HealthKit … only after the Health consent" and "HealthKit → app → API"; FR-076; FR-062 (granular permission) · R7; P29, P30 · EX-27
Shared: eater WF-7 journey (Activity), WF-3 journey (Health write-back).
As the eater, I stop sharing with Apple Health one type at a time, so that each type stops on its own and I know what iOS still controls.
- `/r` **Given** "Health: write dietary energy" is Given, **When** Hala switches it off in Settings → Privacy, **Then** new Entries are no longer written to Health, and one choice appears: "Keep what's already in Health" (default) or "Remove what Sips & Bytes wrote to Health".
- `/r` **Given** "Remove what Sips & Bytes wrote to Health", **When** it finishes, **Then** the Health app on the simulator shows no dietary energy samples from Sips & Bytes, and Today's Entries are unchanged.
- `/r` **Given** "Health: read workouts" is switched off, **When** Today opens, **Then** Activity reads "Apple Health not connected for workouts", workouts imported on past Days stay listed (conflict C-11), and the Settings row adds "iOS keeps its own setting: Health → Sharing → Apps → Sips & Bytes" (P30).
- `/s` **Given** "Health: read workouts" is Withdrawn, **When** the app comes to the foreground, **Then** no HealthKit workout query runs (UI-test spy).

#### eater-9.5 · Withdraw while offline: it applies at once and is recorded once
Covers: interaction row ("one-tap withdrawal"); FR-076; FRD §8.3 (durable outbox, UUID per command) · R22 · AT-10 pattern
Shared: eater + auditor (auditor-9.6).
As the eater without signal, I withdraw a Consent and it takes effect on my iPhone at once, so that "no" never waits for a network.
- `/r` **Given** "Optional research" is Given and there is no network, **When** Hala switches it off at 22:40, **Then** the row reads "Withdrawn · 22:40 · not yet synced", and nothing else changes.
- `/s` **Given** the iPhone reconnects at 06:05 the next morning and the command is delivered twice, **When** processed, **Then** one withdrawal record exists, with `made_at` 22:40 (device) and `received_at` 06:05 (server).
- `/s` **Given** the AI Consent was withdrawn offline, **When** a photo is then taken while still offline, **Then** no analysis is queued for upload and the photo is not kept.

#### eater-9.6 · Withdraw Diary processing: my diary goes back to this iPhone and the account's copy is deleted
Covers: interaction rows "give separate consents (diary processing …) … one-tap withdrawal" and "Eater → API: … delete account"; FR-076; FR-078; FR-001 (local use) · R22, R23 · EX-24
As the eater, I withdraw my Consent to keep my diary in the account and go on using the app on this iPhone, so that I can stop the server copy without losing my diary.
- `/r` **Given** Hala signed in, **When** she taps the Diary processing switch in Settings → Privacy, **Then** one sheet explains "Your diary stays on this iPhone. The copy in your account is deleted within 30 days and you'll be signed out." with "Withdraw and keep on this iPhone" and "Cancel" (conflict C-9).
- `/r` **Given** "Withdraw and keep on this iPhone", **When** done, **Then** the account line of Settings reads "Not signed in · Your diary is only on this iPhone", Today shows the same Entries and totals, and Settings → Privacy shows the deletion reference.
- `/s` **Given** the withdrawal, **When** the server is read, **Then** a deletion Privacy job exists (Requested, then Running) with reason "consent_withdrawn" and the same stages as eater-9.17.

#### eater-9.7 · Optional research stays off unless I choose it, and nothing depends on it
Covers: interaction row consents ("optional research"); FR-079 ("model-training use without a separate explicit opt-in"); FRD §19.2 ("Access to raw evidence for quality review requires explicit consent and restricted roles"); NFR-10 (consented test cases) · R3 · E30
Shared: eater + auditor (auditor-9.8), platform admin.
As the eater, I decide on my own whether my meal photos and labels may be used to test food recognition, so that they are never used that way by default.
- `/r` **Given** any new eater, **When** Settings → Privacy opens, **Then** "Optional research" reads "Not given", and its text says which photos and labels would be kept, who could see them, and what they are used for (testing how well food recognition works).
- `/r` **Given** Optional research is not Given, **When** the eater uses Capture & Plan, Analysis review and the Meal planner, **Then** none of them asks to turn it on in order to continue.
- `/s` **Given** Optional research is not Given, **When** staff with the quality-review permission request a raw photo of that eater, **Then** the API returns 403 `CONSENT_REQUIRED` (auditor-9.8).

#### eater-9.8 · No feature, price or advert depends on my data
Covers: interaction row consents ("no feature paywalled behind consent"); FR-079 ("Do not sell health/nutrition data, use it for behavioral advertising"); FRD §23.3 ("Export, correction, and account deletion must never be paywalled") · R1, R3, R8 · C53 · EX-32
As the eater, I use the whole app with no optional Consent Given and see no adverts, so that my diary is never the price.
- `/r` **Given** no optional Consent is Given (AI, Health, Microphone, Photos, Optional research), **When** the eater opens Today, My Units, Progress, Settings → Export and Settings → Privacy → Delete account, **Then** each works, and no screen shows an advert or asks for a Consent in order to continue.
- `/r` **Given** Settings → Privacy, **When** read, **Then** it states "We don't sell your data or use it for advertising. It isn't used to train AI models." in the eater's language.
- `/s` **Given** the iOS app's resolved package list in CI, **When** the dependency check runs, **Then** it holds no advertising or attribution SDK, and the API has no outbound adapter beyond Gemini, USDA FoodData Central and Firebase/Google Cloud (blueprint §0 line 6).

#### eater-9.9 · Read the privacy policy in my language
Covers: WF-9; FR-082 (privacy review before release) · R5 (the policy is linked inside the app and lists uses, retention, deletion and how to revoke consent)
As the eater, I read the privacy policy inside the app in Arabic or English, so that I can check what happens to my data before and after I agree.
- `/r` **Given** Onboarding · Consents and Settings → Privacy, **When** "Privacy policy" is tapped on either, **Then** the same policy opens in the app's language, with sections on what is collected and why, how long it is kept (raw photos 30 days unless saved; voice 24 hours), how to withdraw a Consent, how to export, how to delete, and who processes the data (Google).
- `/r` **Given** no network, **When** the policy is opened, **Then** the copy bundled with the app version shows, with its date.

## 9B · Photos and voice

#### eater-9.10 · My photos are cropped, stripped of details and private
Covers: FR-077 ("Crop to food where practical, strip EXIF, encrypt in transit/at rest, and prevent public access to private images. Use short-lived signed access where needed."); FR-038 · R8 · E6 · EX-31
Shared: eater WF-4 journey (Capture & Plan).
As the eater photographing a family table, I know my photo leaves without its location and stays private, so that the people around me are not exposed.
- `/r` **Given** a meal photo taken on the simulator with GPS metadata, **When** Analysis review shows it, **Then** it shows the food crop and the line "Location and camera details were removed before upload".
- `/s` **Given** the uploaded object in the Cloud Storage emulator, **When** read with an EXIF reader, **Then** it has no GPS, device or time tags.
- `/s` **Given** the object's path, **When** fetched over HTTP without a signed URL, or with a signed URL after it expired, **Then** the response is 403.

#### eater-9.11 · Raw photos go after 30 days and voice after 24 hours; my confirmed numbers stay
Covers: FR-078 ("keep raw scans up to 30 days unless saved; delete temporary audio within 24 hours after transcription. Confirmed records remain until deleted."); FRD §17 MeasurementEvidence ("Ephemeral photo deletion need not delete confirmed numeric data") · E30 · EX-31
Shared: eater + auditor (auditor-9.14).
As the eater, I see old photos disappear on time while the numbers I confirmed remain, so that media is never kept longer than promised.
- `/s` **Given** an Analysis photo from 2026-09-01 that was not saved to a Unit, **When** the retention job runs on 2026-10-01 (emulator clock), **Then** the photo object is gone, while the confirmed Entry, its quantities and its Evidence remain.
- `/r` **Given** that Entry, **When** opened from Day 2026-09-01 in Progress, **Then** its detail reads "Photo removed after 30 days · numbers kept".
- `/s` **Given** a voice recording transcribed at 08:00 on 2026-10-01, **When** 24 hours pass, **Then** the audio object is gone and the transcript stays with its Analysis.
- `/r` **Given** a photo the eater saved as Evidence for a Unit, **When** 30 days pass, **Then** the Unit editor still shows it, marked "Saved".

## 9C · Export

#### eater-9.12 · Export my data in the app, free, as a machine-readable file
Covers: WF-9 done-when ("export downloads entries, units, recipes, targets and consents"); interaction row "Eater → API: export … in-app"; FR-075; FRD §18 `POST /v1/privacy/export-or-delete`, §23.3 (never paywalled) · R31 · C53
Shared: eater + support agent (support-9.7), auditor (auditor-9.13).
As the eater, I ask for an export and save one file with everything I have recorded, so that I can keep or move my diary.
- `/r` **Given** Hala signed in, **When** she taps Settings → Export → "Prepare my export" at 18:20 on 1 Oct 2026, **Then** the screen reads "Running — your export is being prepared. You can leave this screen."; once done it reads "Completed · 1 Oct 2026, 18:26 · available until 8 Oct 2026" with "Save to Files" and "Share", and the Settings button on Today shows "Data download ready".
- `/r` **Given** the saved .zip opened in Files, **When** listed, **Then** it holds entries.json and entries.csv (with each Entry's Corrections, Voids and Restores), units.json (every version), recipes.json, templates.json, targets.json (every Target version), activity.json, weight.json, consents.json, day_reports.csv and readme.txt explaining each file in the app's language.
- `/s` **Given** the job, **When** `POST /v1/privacy/export-or-delete {kind: "export"}` and then `GET /v1/privacy/jobs/{id}` are read, **Then** the Privacy job moves Requested → Running → Completed, and the archive's record counts equal the API's counts for that account.
- `/r` **Given** an eater with no purchase or subscription, **When** the export is requested, **Then** no purchase, upgrade or Consent screen appears.

#### eater-9.13 · The export holds only my data, in a form machines and people can read
Covers: FR-075 ("machine-readable form"); NFR-07 · research.md §6 conflict 8 (numerals in exports) · E40, E41
As the eater, I get an export any tool can read and in which my Arabic food names are intact, so that it is useful outside the app.
- `/s` **Given** Hala's export (her display uses Arabic-Indic digits), **When** entries.csv is parsed, **Then** every number uses Western digits with a "." decimal, every time is ISO 8601 with its offset (for example 2026-10-01T08:40:00+03:00), and Unit names such as «قرصة جبنة» are intact UTF-8.
- `/s` **Given** two accounts (Hala and Sam) on the emulator, **When** Hala's export is built, **Then** it contains no record with Sam's user id, and Sam's token reading Hala's Privacy job gets 404 `NOT_FOUND` (NFR-07).
- `/r` **Given** entries.csv opened in Files' preview on the simulator, **When** viewed, **Then** Arabic names show right to left inside their cells and the header row uses fixed English field names.

#### eater-9.14 · An export that is waiting, failed or offline says why and what next
Covers: FR-075; FRD §14 Settings mandatory state ("data download ready"), §18.2 (bounded retries) · care.md group 4 · EX-23
Shared: eater + support agent (support-9.8).
As the eater, I always know where my export stands, so that I never wonder whether it worked.
- `/r` **Given** no network, **When** "Prepare my export" is tapped, **Then** it reads "Connect to prepare your export", and nothing is queued.
- `/r` **Given** the Privacy job is Failed after its retries, **When** Settings → Export opens, **Then** it reads "Failed — your export couldn't be prepared. Try again, or contact a Support agent with your support code." with "Try again"; after a Support agent re-queues it (support-9.8), the screen shows "Running" without a new request.
- `/r` **Given** a Completed export more than 7 days old (EA9), **When** the screen opens, **Then** it reads "This export has expired — prepare a new one", and the old file can no longer be downloaded.
- `/s` **Given** "Prepare my export" is tapped twice quickly, **When** processed, **Then** one Privacy job exists.

#### eater-9.15 · Export a trial diary that lives only on this iPhone
Covers: FR-075; FR-001 (local trial); FRD §23.3 · R3
As a trial eater, I export my diary from the iPhone without an account, so that trying the app never locks my data in.
- `/r` **Given** T1 on Hala's iPhone with no account, **When** she taps Settings → Export, **Then** the export is built on the iPhone and offered to "Save to Files" with the same file set, and consents.json lists the Consents made on the device.
- `/s` **Given** that export, **When** the API mock's record is read, **Then** no request was sent to build it.

## 9D · Delete account

#### eater-9.16 · Delete my account in the app, with one clear warning
Covers: WF-9 step "delete account"; interaction row "Eater → API: … delete account · in-app, no email or phone, ≤30 days"; FR-078; NFR-13 · R4, R23, R31 · EX-24
Shared: eater + support agent (support-9.9, support-9.12), auditor (auditor-9.9, auditor-9.10).
As the eater, I delete my account from Settings without writing to anyone, so that leaving is as easy as joining.
- `/r` **Given** E2 signed in (English, Cairo), **When** she opens Settings → Privacy → Delete account, **Then** one screen says what will be deleted (diary, Units, Recipes, Templates, Targets, photos, voice, Consent choices) and that it completes within 30 days, offers "Export first", and has one destructive button "Delete account" and "Cancel"; no email, phone call or form is asked for.
- `/r` **Given** "Delete account" is tapped at 10:00 on 15 Sep 2026 (Cairo), **When** the server confirms, **Then** the app shows "Account deletion requested. Reference DEL-26-0915-K3Q8. Completed by 15 Oct 2026 at the latest." with "Copy reference", signs out and clears the local database.
- `/s` **Given** the request, **When** `POST /v1/privacy/export-or-delete {kind: "delete"}` is processed, **Then** the Privacy job is Requested with `due_by` 2026-10-15 (30 days, NFR-13), and the account can no longer sign in or call a private endpoint (401 `UNAUTHENTICATED`).

#### eater-9.17 · Deletion reaches everything: media, queues, caches, exports, Grants, Google and Apple
Covers: AT-29; FR-078; FRD §17.2 ("Account deletion removes private records and media, including derived caches, subject to the disclosed backup lifecycle"); interaction row "Eater → API: … delete account · … processors told, Sign in with Apple revoked, completion record" · R4, R23
Shared: eater + support agent (support-9.9, support-10.18), auditor (auditor-9.10).
As the eater, I know my deletion reaches every copy, so that nothing of my diary is left behind.
- `/s` **Given** E1 (Sign in with Apple) with 2 photos, 1 audio file, 1 Pending Analysis, 1 queued job, 1 prepared export, private cached analyses and the Active Grant `grant_31f0`, **When** deletion runs on the emulators, **Then** Firestore and Cloud Storage hold nothing under E1's user id, the job queue holds nothing for it, the Grant is Withdrawn with reason `account_deletion`, and the Support agent's next read returns 403 `GRANT_NOT_ACTIVE`.
- `/s` **Given** the same, **When** the Sign in with Apple adapter mock and the processor-notice step are read, **Then** the token revocation was called once, and the notice to Google is recorded with its time (R4).
- `/r` **Given** the deletion Privacy job is Running, **When** it is opened in the admin console's Jobs section (support-9.9), **Then** each stage reads "done" or "waiting" in words, including "backups expire by 2026-10-14".
- `/s` **Given** the deletion was requested, **When** a Support agent requests a new Grant for the account, **Then** the API returns 404 `NOT_FOUND` and no Grant is created.

#### eater-9.18 · Deletion needs a connection and says so; food waiting on my iPhone goes with it
Covers: FR-078; FRD §8.3 (durable outbox) · care.md group 4 ("When a command cannot work right now, do we say why?") · EX-21
As the eater without signal, I'm told deletion has to reach the server, and once it does, nothing queued on my iPhone is sent afterwards.
- `/r` **Given** no network, **When** "Delete account" is tapped, **Then** the screen reads "Connect to delete your account — the request has to reach our servers", and nothing is deleted on the iPhone.
- `/r` **Given** 3 Pending Entries in the outbox, **When** the eater opens Delete account online, **Then** the screen says "3 entries not yet synced will be deleted too" before she confirms, and after deletion the outbox is empty.
- `/s` **Given** a confirmed deletion, **When** the app gains a session again for any reason, **Then** no command from the old outbox reaches `/v1/consumption`.

#### eater-9.19 · After deletion: a fresh start, and a reference I can keep
Covers: WF-9 done-when ("leaves a completion record without identifiers"); FR-078 · R4, R23
Shared: eater + support agent (support-9.11), auditor (auditor-9.10).
As the eater who left, I can start fresh like a new person and still prove my deletion with its reference, so that nothing links me to my old diary.
- `/r` **Given** the deletion is confirmed, **When** the app is reopened, **Then** Onboarding · Age shows, as on a fresh install.
- `/r` **Given** E2 signs up again with the same email on 20 Oct 2026, **When** Today opens, **Then** it is empty: no Units, Entries, Targets or Consents from the deleted account.
- `/r` **Given** the completion record for DEL-26-0915-K3Q8 (completed 14 Oct 2026), **When** a Support agent looks the reference up in the admin console (support-9.11), **Then** it shows only "Completed" and the dates — no email, account id or device.

#### eater-9.20 · Delete a trial diary, and anything my anonymous session left
Covers: FR-001 (local trial, anonymous session); FR-078 · R4
As a trial eater, I erase everything on this iPhone and whatever my anonymous session left on the server, so that a trial leaves no trace.
- `/r` **Given** T1 with no account, **When** Hala taps Settings → Privacy → "Delete everything on this iPhone" and confirms once, **Then** Onboarding · Age opens and My Units is empty.
- `/s` **Given** an anonymous session that ran 2 Analyses, **When** the trial is deleted with a connection, **Then** its Analyses, photos, quota counter and Consent records are deleted on the server.
- `/r` **Given** the same without a connection, **When** confirmed, **Then** the screen says "Analyses from your trial will be deleted the next time you're online", the iPhone keeps only that pending request, and on the next launch with a connection the server records are deleted.

## 9E · Answer a support Grant (WF-10, the eater's side)

Grant states follow vocabulary.md: Requested → Approved → Active → Expired · Ended (by the Support agent) · Withdrawn (by the eater); Requested → Declined · Unanswered. Approval starts the time box, so an Approved Grant is Active at once.

#### eater-9.21 · Learn that a Support agent asked for access, without anyone around me noticing
Covers: interaction row "Support → Eater: request just-in-time diary access · Grant (requested) · the eater sees who asks, why and for how long, and approves or declines in Settings"; WF-10 done-when; FR-081 · E24 · EX-12
Shared: eater + support agent (support-10.6; support conflict K2).
As the eater, I see a quiet sign that a request is waiting, so that I decide when I am ready.
- `/r` **Given** `grant_31f0` became Requested at 10:05 UTC, **When** E1 opens Today, **Then** the Settings button at the top of Today shows the badge "1", and Settings → Privacy shows the row "Grants · Requests from a Support agent to read your diary · 1 Requested"; no alert or sound occurs.
- `/r` **Given** E1 had already allowed notifications for Sips & Bytes, **When** the request arrives, **Then** one notification reads "A Sips & Bytes Support agent asked to see part of your diary. Open Settings to decide." with no food or health detail; **given** notifications were never allowed, no permission prompt appears.

#### eater-9.22 · See who, why, what and for how long — and approve
Covers: interaction rows "Support → Eater … Grant (requested)" and "Support → eater diary … time box, read-only, every read audited, auto-expiry"; WF-10 done-when ("the eater approves it in Settings"); FR-081
Shared: eater + support agent (support-10.6, support-10.9), auditor (auditor-10.6).
As the eater, I read who wants access, why, to which Days and areas and for how long, and approve it, so that access exists only because I chose it.
- `/r` **Given** `grant_31f0` is Requested, **When** E1 opens Settings → Privacy → Grants on the simulator, **Then** the request shows "Mona K. · Support agent", the reason "An entry is missing or appears twice", "Entries and day reports, My Units · 28–30 Sep 2026", "1 hour from when you approve", case CASE-1182, "Never included: photos, voice, your Target and goal settings", "The Support agent can read, not change. Every view is listed here.", and two equal-size buttons, "Approve" and "Decline" (conflict C-12).
- `/r` **Given** E1 taps Approve at 13:20 Asia/Riyadh, **When** the server confirms, **Then** the request reads "Active · ends 14:20", and the admin console's Grants section shows `grant_31f0` as Active (support-10.6).
- `/r` **Given** E1's app is in Arabic with Arabic-Indic digits, **When** the request shows, **Then** it is right to left, the Days read «٢٨–٣٠ سبتمبر ٢٠٢٦» and the end time «١٤:٢٠», and "Mona K." is an isolated left-to-right run.
- `/s` **Given** `POST /v1/grants/grant_31f0/approve` with E1's own token, **When** processed, **Then** 200, the Grant is Approved and Active, and `expires_at` = `approved_at` + 1 h; with any staff token, 403 `FORBIDDEN`.

#### eater-9.23 · Decline without giving a reason
Covers: WF-10 done-when ("a declined Grant gives no access"); interaction row "Support → Eater … approves or declines in Settings"; FR-081
Shared: eater + support agent (support-10.7), auditor (auditor-10.7).
As the eater, I say no with one tap and no explanation, so that declining is as easy as approving.
- `/r` **Given** `grant_31f0` is Requested, **When** E1 taps Decline at 13:12 Asia/Riyadh, **Then** the request moves to history as "Declined · 1 Oct, 13:12", with no follow-up question and nothing asking her to reconsider.
- `/s` **Given** the Grant is Declined, **When** the Support agent reads Day 2026-09-29 under it, **Then** the API returns 403 `GRANT_NOT_ACTIVE`.
- `/r` **Given** the Support agent sends a new request later, **When** it arrives, **Then** it appears as a separate Requested Grant, and the declined one stays Declined.

#### eater-9.24 · Do nothing, and the request ends by itself
Covers: interaction row "Support → Eater … Grant (requested)"; FR-081; WF-10 · research.md §6 conflict 6 ("make 'decline' the default when the eater does nothing, and set the request's own expiry")
Shared: eater + support agent (support-10.8), auditor.
As the eater who ignores a request, I am sure it gives no access and ends, so that silence is never taken as yes.
- `/r` **Given** `grant_40aa` is Requested, **When** E1 opens it, **Then** it reads "If you don't answer, this request ends on 4 Oct at 13:05" (EA10).
- `/r` **Given** `grant_40aa` was never answered, **When** E1 opens Settings → Privacy → Grants on 4 Oct 2026 at 13:06 Asia/Riyadh, **Then** history shows it as "Unanswered · no access was given", with no Approve button.
- `/s` **Given** the Grant is Unanswered, **When** a late approve call from E1 arrives, **Then** the API returns 409 `GRANT_NOT_ACTIVE`, and no access is created.

#### eater-9.25 · See every read a Support agent made under my Grant
Covers: interaction row "Support → eater diary … every read audited"; WF-10 done-when ("every read shows in the auditor's trail"); FR-081, FR-082
Shared: eater + support agent (support-10.15), auditor (auditor-10.3; auditor conflict 13).
As the eater, I see each thing the Support agent looked at and when, so that I know exactly what was seen.
- `/r` **Given** Mona K. viewed Day 2026-09-29, an Entry's details and My Units while `grant_31f0` was Active, **When** E1 opens that Grant in Settings → Privacy → Grants, **Then** three lines show, such as "Mona K. viewed your diary for 29 Sep · 13:24", in E1's language, digits and time zone (conflict C-14).
- `/s` **Given** the same, **When** `GET /v1/me/grants/grant_31f0/reads` is compared with the Audit trail filtered by that Grant, **Then** both list the same 3 reads at the same times.
- `/r` **Given** an Active Grant with no reads yet, **When** opened, **Then** it reads "No views yet".

#### eater-9.26 · Withdraw access early
Covers: interaction row "Support → eater diary … time box … auto-expiry"; FR-081; AT-29 (withdrawal propagates)
Shared: eater + support agent (support-10.18).
As the eater, I withdraw an Active Grant whenever I want, so that my "stop" works at once.
- `/r` **Given** `grant_31f0` is Active, **When** E1 opens it, **Then** "Active · ends 14:20 · Withdraw access" is visible at the top of the request without scrolling.
- `/r` **Given** E1 taps "Withdraw access" at 13:41 Asia/Riyadh, **When** the server confirms, **Then** the request reads "Withdrawn · 13:41 · The Support agent can no longer see your diary", and the access history keeps every read made before 13:41.
- `/s` **Given** the Grant is Withdrawn, **When** the Support agent's next read arrives, **Then** the API returns 403 `GRANT_NOT_ACTIVE`.

#### eater-9.27 · Answer a request only when online
Covers: interaction row "Support → Eater … approves or declines in Settings"; FR-081; FRD §8.3 (the outbox carries food commands)
Shared: eater + support agent (support-10.10).
As the eater without signal, I see the request but cannot answer until I am online, so that an approval never applies later by surprise.
- `/r` **Given** no network, **When** E1 opens Settings → Privacy → Grants, **Then** the cached Requested Grant shows with Approve and Decline disabled and the line "Connect to answer this request".
- `/m` **Given** the outbox, **When** a Grant answer is attempted offline, **Then** no outbox command is created.

#### eater-9.28 · Approve once, even if I tap twice
Covers: interaction row "Support → Eater … Grant approved or declined"; FR-081; FRD §18 (idempotency key) · AT-10 pattern
Shared: eater + auditor (auditor fixture G-2026-0046: one approve delivered three times).
As the eater, I give one approval however many times it is sent, so that the record shows one decision.
- `/s` **Given** E1's approve for `grant_31f0` delivered three times with one `command_id`, **When** processed, **Then** one `grant.approved` event exists, and the time box starts at the first approval.
- `/r` **Given** Approve is tapped twice quickly, **When** the server confirms, **Then** the request reads "Active · ends 14:20" once, and its history lists one approval.

#### eater-9.29 · Read my support code from Settings → Privacy
Covers: interaction row "Support → Eater"; FR-081 (support without diary access); FR-001 (anonymous session, local trial)
Shared: eater + support agent (support-9.2, which places the code in a Help section; conflict C-19).
As the eater writing to a Support agent, I read them a short code from the app, so that they find my account without me sending my email or anything private.
- `/r` **Given** E1 signed in, **When** she opens Settings → Privacy → Support code, **Then** the code `SB-7KQ2-94XM` shows in Latin letters left to right (in the Arabic screen too), with "Copy" and "Valid until 2 Oct, 09:12".
- `/r` **Given** an anonymous session, **When** Settings → Privacy → Support code opens, **Then** a code shows; **given** a local trial, it reads "Your diary is only on this iPhone, so a Support agent can't see it" with "Create account".

## 9F · Language and access

#### eater-9.30 · Privacy in Arabic, with VoiceOver and the largest text
Covers: WF-9 (all steps); NFR-08; blueprint §0 line 5 (reach) · E36, E40, E41 · EX-15, EX-33, EX-36, EX-39
As an Arabic-speaking or VoiceOver eater, I manage Consents, export, deletion and Grants with nothing clipped or unread, so that privacy controls work for me too.
- `/r` **Given** Arabic and the largest text size on the smallest simulator, **When** Settings → Privacy, Settings → Export, Settings → Privacy → Delete account and Settings → Privacy → Grants open, **Then** nothing clips or overlaps, the layout is right to left, and dates use Arabic-Indic digits.
- `/r` **Given** VoiceOver, **When** a Consent switch is focused, **Then** it reads the purpose, "Given" or "Withdrawn" and the date of the last change, and after a one-tap withdrawal it announces "Withdrawn".
- `/r` **Given** the destructive "Delete account" button, **When** measured, **Then** it is at least 44 × 44 pt and is not the first item VoiceOver focuses on the screen (EX-15).

---

## Stories shared with other personas

| story | shared with | why |
|---|---|---|
| eater-1.3, 1.5, 9.1, 9.2, 9.3, 9.5 | auditor (auditor-9.1, 9.2, 9.4, 9.5, 9.6) | the eater's Consent choices are the records the auditor proves |
| eater-1.17, 1.18, 1.20, 1.30, 1.31, 1.47, 1.48 | nutrition approver (approver-10.49–10.53, 10.57, 10.58) | the Policy values the eater meets on Onboarding · Target |
| eater-1.19, 1.21 | support agent (support-9.5, 10.14) | the mode and the answers never reach a support view |
| eater-1.49, 9.29 | support agent (support-9.2) | support code and the local trial |
| eater-1.50 | platform admin (admin-10.35, 10.36, 10.39) | per-user AI quotas in the trial |
| eater-9.7 | auditor (auditor-9.8), platform admin | raw evidence only with Optional research |
| eater-9.11 | auditor (auditor-9.14) | retention runs |
| eater-9.12, 9.14 | support agent (support-9.7, 9.8), auditor (auditor-9.13) | the export job |
| eater-9.16, 9.17, 9.19 | support agent (support-9.9, 9.11, 9.12, 10.18), auditor (auditor-9.9, 9.10) | deletion, its stages and its completion record |
| eater-9.21–9.28 | support agent (support-10.6–10.10, 10.15, 10.18), auditor (auditor-10.3, 10.6, 10.7) | the eater's side of WF-10 |
| eater-1.9, 1.15, 1.39–1.41, 1.47, 9.4, 9.10 | other eater journeys (WF-2, WF-5, WF-7, WF-8, WF-3, WF-4) | the same screen seen from another workflow |

## Conflicts for the model phase

These are tensions with other personas or inside the model. They are for the model phase, never for the owner.

1. **C-1 · Floor scope (eater ↔ nutrition approver).** Does the 1,200 kcal floor bind only loss Targets, or every Target? Amal's maintenance is 1,162.8, so a Maintain Target at maintenance would sit below the floor. This lens proposes Maintain = 1,200 and no Lose (eater-1.30), following the done-when "a target not below the policy floor". The approver decides.
2. **C-2 · Own or clinician Targets below the floor, and in tracking-only mode (eater ↔ approver).** FR-004 allows a clinician-provided Target. This lens refuses any Target below the floor whatever its source (eater-1.31), and offers no own-Target entry in tracking-only mode (eater-1.19), because FRD §11.4 bars restrictive plans for excluded users. The model decides whether a clinician's Target may override either rule.
3. **C-3 · A Target in the local trial (eater ↔ model).** The proposal runs on the server (FRD §15.1, nutrition core), and profile data needs the Diary processing Consent. So this lens asks for an account before the Target flow (eater-1.49). The alternative is an on-device engine held to the same golden cases (eater-1.27).
4. **C-4 · Optional safety screen vs screening before restrictive plans (eater ↔ approver).** The map makes the screen optional, and FR-008 forbids forcing sensitive details. R35 calls for "baseline screening, including … disordered eating" and R38 for assessment before restrictive plans. The model decides whether skipping should change anything, such as a smaller default deficit.
5. **C-5 · Safety-screen minimisation vs metrics (eater ↔ approver, auditor).** This lens keeps only the mode (EA3, eater-1.21). Any wish for counts by trigger (pregnancy against SCOFF) would need the trigger stored, which this lens refuses.
6. **C-6 · Activity rows missing from Policy (eater ↔ approver).** Only the 1.2 multiplier and planned exercise are sourced (FRD §11.3). The Activity-adjusted credit factor and cap (EA7) and any other activity levels have no Policy row in the approver lens's table (approver-10.48).
7. **C-7 · Default macro split and review interval have no Policy row (eater ↔ approver).** EA5 (30/40/30) and EA6 (14 days) are placeholders the approver must own.
8. **C-8 · Target rounding (eater ↔ model).** This lens has the eater approve the Target as shown, to the nearest 10 kcal (FRD §11.3), and keeps the unrounded value in the snapshot (EA4). FRD §10.2 says "round only the final displayed fields". The model fixes which value reports use.
9. **C-9 · Withdrawing Diary processing (eater ↔ auditor, model).** The interaction row promises one-tap withdrawal (R22). Here the withdrawal deletes the account's copy, so this lens adds one confirmation (eater-9.6), following care.md group 4: "warn only before loss that is both unexpected and permanent". The model confirms or removes it.
10. **C-10 · Export contents (eater ↔ support agent, auditor).** The eater's export adds Templates, Activity and weight (eater-9.12). support-9.7 and auditor-9.13 list Entries, Units, Recipes, Targets, Consents and Reports. One list is needed. Also open: whether photos saved as Unit Evidence travel in the export (this lens: no media).
11. **C-11 · Imported Activity after a Health Consent withdrawal (eater ↔ auditor).** This lens keeps workouts already imported as diary records (eater-9.4). R22's "cease processing" may require removing them or offering removal.
12. **C-12 · Grant verbs (eater ↔ support agent).** The map says the eater "approves or declines" a Grant, and vocabulary.md names the eater's early stop **Withdrawn** (the Support agent's is Ended). support-10.6 writes "Allow" and support-10.18 "End access". This lens uses "Approve", "Decline" and "Withdraw access" (eater-9.22, 9.23, 9.26), so that each action and state has one name, as on "Approve target". The support lens should match.
13. **C-13 · The Grant label on the eater's screen (support K6).** This lens keeps the map's word "Grants", with the subtitle "Requests from a Support agent to read your diary" (eater-9.21).
14. **C-14 · What the eater's access history lists (support K1, auditor conflict 13).** This lens lists Grant reads only (eater-9.25). Metadata look-ups, which show no diary, appear to the auditor but not to the eater.
15. **C-15 · How the eater learns of a request (support K2).** This lens shows a Settings badge always, and a notification only if notifications were already allowed; a Grant request never triggers the permission prompt (eater-9.21). With a 72 h expiry (EA10), a request may lapse unseen. That is the safe failure.
16. **C-16 · Public Food search in the local trial (eater ↔ platform admin).** EA12 lets a trial reach the reference search with App Check only. Per-user quotas (NFR-12) then have no user to count against.
17. **C-17 · Duplicate Units when a trial joins an account (eater ↔ model).** Which version becomes the default for new logs, and whether "Keep both" leaves two Units or one Unit with two versions (eater-1.53).
18. **C-18 · A self-declared age gate (eater ↔ auditor).** R16 bars services "likely to be accessed by" under-18s, and the store's 9+ rating (R13) does not keep them out. Whether a self-declared age (eater-1.1, 1.2) is enough is for counsel.
19. **C-19 · Settings sections with no name (eater ↔ support agent, vocabulary.md).** vocabulary.md lists Settings → Goals, Food rules, Activity, Units & language, Privacy and Export. It has no Account or Help section, though FRD §14 lists "Account" and support-9.2 uses Settings → Help for the support code. This lens shows the account state as a line at the top of Settings and puts the support code under Settings → Privacy → Support code (eater-9.29). It also uses Settings → Export, where support-9.7 says Settings → Privacy → Export. A dated delta should settle all three.

Two of research.md §6's conflicts are answered here as proposals: conflict 6 (an unanswered Grant ends with no access, eater-9.24) and conflict 8 (exports use Western digits and ISO dates, eater-9.13).

## Coverage — every FR, AT and FRD section in the dispatch

| line | stories |
|---|---|
| FR-001 local trial, account creation, migration without duplicates; cloud AI needs a session, consent and quotas | 1.7, 1.49, 1.50, 1.51, 1.52, 1.53, 9.15, 9.20, 9.29 |
| FR-002 age, height, weight, units, time zone, goal; the coefficient only with an explanation | 1.1, 1.10, 1.11, 1.12, 1.13, 1.14 (goal: 1.28) |
| FR-003 lose, maintain or gain; resting energy, maintenance, Target, assumptions, review date before approval | 1.28, 1.29, 1.42 |
| FR-004 own or clinician Target, or measured resting value, with source and effective date | 1.24, 1.26, 1.32 |
| FR-005 macros by % or grams; locks; incompatible locks resolved visibly | 1.20, 1.33, 1.34, 1.36, 1.37 |
| FR-006 reject negative values and invalid units; normalise or edit, never save contradictions | 1.11, 1.35, 1.37, 1.38 |
| FR-007 ask whether exercise is included; mode visible on Today | 1.26, 1.39, 1.40, 1.46 |
| FR-008 exclusions; optional safety screen; tracking without a prescription | 1.7, 1.8, 1.15, 1.16, 1.17, 1.18, 1.19, 1.21, 1.22 |
| FR-056 method and assumptions shown; calculated in code | 1.23, 1.27 |
| FR-057 maintenance from the activity policy or a manual value; exercise-included stored | 1.25, 1.26, 1.39 |
| FR-058 proposals with explicit deficit or surplus; approval; effective-dated versions | 1.28, 1.30, 1.32, 1.42, 1.43, 1.44, 1.47, 1.48 |
| FR-059 uncertainty note; no fixed weekly change | 1.29 |
| FR-075 machine-readable export | 9.12, 9.13, 9.14, 9.15 |
| FR-076 separate Consents; refusal keeps other functions | 1.3, 1.4, 1.5, 1.6, 1.41, 1.50, 9.1, 9.2, 9.3, 9.4, 9.5, 9.6 |
| FR-077 crop, strip EXIF, private, short-lived access | 9.10 |
| FR-078 in-app export and deletion; raw scans 30 days; audio 24 h | 9.6, 9.11, 9.16, 9.17, 9.18, 9.19, 9.20 |
| FR-079 no sale, no behavioural ads, no training without an opt-in | 1.4, 9.7, 9.8 |
| AT-09 46/32/24 → 102 and the normalised alternative; nothing active without approval | 1.35 |
| AT-29 deletion and withdrawal reach media, queues, private cached analysis and exports | 9.2, 9.17 (also 9.26) |
| FRD §3.1 normalisation example | 1.35 |
| FRD §3.2 progressive onboarding; separate choices; declining Health never blocks logging | 1.6, 1.7, 1.8, 1.9, 1.41, 1.49 |
| FRD §3.3 versioned Policy; 0 / 15 / 10 % defaults; tracking-only | 1.17, 1.18, 1.28, 1.30, 1.48 |
| FRD §11.1 Mifflin–St Jeor; measured override; "BMR" wording | 1.12, 1.23, 1.24 |
| FRD §11.2 planning model (FR-056–FR-059; FR-060/061 are P1 and only the 14-day window is borrowed, EA6) | as the FR rows above |
| FRD §11.3 worked example (1,779 → 2,334.8; 1,870 includes the exercise) | 1.25, 1.32, 1.39 |
| FRD §11.4 safety behaviour; neutral words | 1.17, 1.18, 1.19, 1.31, 1.56 |
| WF-1 done-when (English or Arabic; ±10 % note; maintenance; Target not below the floor; macro grams and assumptions; approve; Today shows Target and mode; pregnancy → tracking-only; under 18 → no account) | 1.2, 1.18, 1.23, 1.25, 1.30, 1.33, 1.42, 1.46, 1.54 |
| WF-9 done-when (export of entries, units, recipes, targets and consents; deletion within the window, completion record without identifiers) | 9.12, 9.16, 9.17, 9.19 |
| WF-10 done-when, eater side (approve in Settings; reads in the box; expiry; reads in the trail; a decline gives no access) | 9.21–9.28 |
| Other lines cited | AT-10 (1.5, 1.43, 1.52, 9.5, 9.28) · AT-13 (1.9) · AT-16 (1.7) · AT-23 (1.39) · AT-32 (1.27, 9.2) · FR-071 (1.22, 1.47, 1.48) · FR-081 (9.21–9.29) · NFR-06 (1.52) · NFR-07 (9.13) · NFR-08 (1.55, 9.30) · NFR-13 (9.16) |

**Counts:** 86 stories (56 in journey 1, 30 in journey 9) · 277 acceptance lines (`/m` 21 · `/s` 73 · `/r` 183).
