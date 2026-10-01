# Eater — journeys WF-1, WF-9 and the eater's side of WF-10

Written 2026-10-01 by the eater lens, steps 2, 3 and 5 of `way/personas/_lens-brief.md`; fix round 1 the same day (log at the foot). Dispatch: **WF-1 Onboard and set a target** and **WF-9 Privacy**, including approving or declining a support Grant in Settings. Grant stories trace to WF-10, so they carry journey number 10 (`eater-10.n`), the same journey number the support and auditor lenses use for those steps. Research cycle 2 and the experience are in `way/personas/eater/research.md`. This file builds on them and cites their ids (findings E1–E44, experience requirements EX-01–EX-44) instead of repeating them.

## How to read this file

- **Each story** has a title, a **Covers** line (first the map line it traces to — workflow step, interaction row or done-when — and the FRD lines, then research ids), a **Shared** line when another persona owns it too, the story sentence, and acceptance lines.
- **Layers.** `/m` module (unit test of pure code) · `/s` system (an API or rules test against the emulator or a running service, negative tests included) · `/r` runtime (a verifier can watch it in the served product: the iOS simulator, the API over HTTP and its log stream, or the admin console in a browser). Every story has at least one `/r` line.
- **Words.** Only the map's vocabulary (blueprint §1.4) and `way/vocabulary.md` (delta D2): Unit, Recipe, Food, Template, Entry, Day, Target, Analysis, Evidence, Activity, Consent, Policy, Grant, Privacy job; roles Eater, Support agent, Nutrition approver, Platform admin, Auditor. States use vocabulary.md's names exactly: Entry Pending/Confirmed; Analysis Pending/Processing/Ready for review/Approved; Unit Draft/Saved; Consent Not given → Given · Withdrawn (D3); Grant Requested/Approved/Active/Expired/Ended/Withdrawn/Declined/Unanswered; Privacy job Requested/Running/Completed/Failed; Policy version In effect. "Draft" is used only for a Unit. A Consent purpose the eater has not decided is in the state **Not given** (vocabulary D3). Activity mode is **Fixed mode** or **Activity-adjusted mode**; Today shows it as "Food Target: Fixed" or "Food Target: Activity-adjusted", as in the eater's WF-7 journey (eater-7.16, 7.18).
- **Places.** vocabulary.md's: tabs Today · Capture & Plan · My Units · Progress (FRD §14's "Capture" screen is the Capture & Plan tab); screens Analysis review, Unit editor, Meal planner and Settings; Settings sections Goals, Activity, Units & language, Privacy and Export; admin console sections Grants, Jobs, Registry and Audit trail. Places this lens needs that vocabulary.md does not name are marked *(proposed)* where they first appear and are listed in conflict C-19: the eleven onboarding screens, the account line at the top of Settings, and the parts of Settings → Privacy called Grants, Support code and Delete account (the support lens uses the same three). Progress → Target history is the eater's WF-8 place (eater-8.23).
- **Copy.** Quoted text is proposed English copy. Arabic strings are proposals for the string catalogue, which fixes one Arabic label per word (EX-08). No FR, AT, Policy-version or error code ever appears in the eater's copy; codes appear only in API responses. The support code, the deletion reference and the case reference are shown because the eater is meant to read them out (support lens, "Copy rules").
- **Fixture names.** Eaters defined here are O1–O5 and T1. Accounts reused from the support lens are prefixed **SE** (SE1 = the support lens's E1, and so on), so they never read as the research findings E1–E44.

## Onboarding screens *(proposed — the map and vocabulary.md name none; conflict C-19)*

| screen | what it is for | reached from |
|---|---|---|
| Onboarding · Age | the 18+ gate | first launch |
| Onboarding · Under 18 | the end screen for an age under 18 | Onboarding · Age |
| Onboarding · Consents | where the diary lives; the AI Consent; Optional research | Onboarding · Age |
| Onboarding · Account | create an account (Sign in with Apple or email) or sign in | Onboarding · Consents; "Create account" on the account line of Settings; "Set a Target" in a local trial |
| Onboarding · Profile | age, height, weight, equation version, region and units, foods I don't eat | "Set a Target" on Today or Settings → Goals — both open the Target flow at its first unfinished step (Onboarding · Account first for a local-trial eater) |
| Onboarding · Safety screen | optional questions that choose tracking-only or protein-first | Onboarding · Profile; Settings → Goals |
| Onboarding · Energy | resting energy, maintenance, planned exercise | Onboarding · Safety screen |
| Onboarding · Target | lose, maintain or gain; the conservative choices; the proposed Target; my own value | Onboarding · Energy |
| Onboarding · Macros | macro targets in % or grams, with locks | Onboarding · Target |
| Onboarding · Activity mode | whether exercise is included; Fixed mode or Activity-adjusted mode; Apple Health (optional) | Onboarding · Macros |
| Onboarding · Review | everything once, then "Approve target" | Onboarding · Activity mode |

Settings opens from the button at the top of Today (EX-04, E35). Its first line is the **account line** *(proposed)*: "Signed in with Apple" (or "Signed in with email"), or "Not signed in · Your diary is only on this iPhone" with "Create account". The approver lens reaches the Target flow from Settings → Goals (approver-10.50, 10.68–10.70); so does this file.

## Proposed interfaces (FRD §18 style; the FRD names none for these)

- `POST /v1/age-gate` takes the age only — no account, device or other identifier. For 18 or over it returns an age confirmation (text version `age-1`, method "onboarding age question", time, app version). The app keeps it until an account or anonymous session exists, then files it with that account's Consents (auditor-9.2, 9.9). Under 18 it returns 422 `AGE_REQUIREMENT` and writes one `age.refused` event with no identifier (auditor-9.9). Offline, the iPhone applies the same rule and sends the call when it reconnects.
- `POST /v1/targets/proposals` computes resting energy, maintenance, the Target per goal and per conservative choice, the deficit or surplus, macro grams, assumptions and the review date from the inputs and the Policy in effect. It writes nothing.
- `POST /v1/targets` approves one Target (a GoalPlanVersion, FRD §17) with a `command_id`. `GET /v1/targets` lists the versions — the same path the eater's WF-8 journey reads (eater-8.23).
- `PUT /v1/me/safety-mode` takes `standard | tracking_only | protein_first`, the safety-screen version and the time. It never takes the answers.
- `POST /v1/me/consents` takes purpose, action (`given | withdrawn`), text version, method, `made_at` and `command_id`. `GET /v1/me/consents` lists them.
- `GET /v1/me/grants`, `GET /v1/me/grants/{id}/reads`, `POST /v1/grants/{id}/approve | decline | withdraw` (the support lens names `POST /v1/grants/{id}/approve`).
- The FRD's `POST /v1/privacy/export-or-delete`, plus a read of the Privacy job, `GET /v1/privacy/jobs/{id}`.
- Errors, only from vocabulary.md: `AGE_REQUIREMENT`, `CONSENT_REQUIRED`, `POLICY_FLOOR` (with a `limit` field, `floor` or `hard_stop`), `VALIDATION_ERROR` (with `field` and `reason`, such as `macro_total`, `locks_incompatible` or `tracking_only`), `RATE_LIMITED`, `STALE_REVISION`, `UNAUTHENTICATED`, `FORBIDDEN`, `NOT_FOUND`, `GRANT_REQUIRED` and `GRANT_NOT_ACTIVE`.

## Fixtures (all synthetic; the repo is public)

| fixture | values |
|---|---|
| Policy v1 | As the approver lens reads it (approver-10.48), In effect: floor 1,200 kcal (product policy, not a sourced hard floor — r1-refute-b Dropped 12); hard stop 1,000 kcal (R32); loss default 15 % with the choices 5 %, 10 % and 15 %; gain default +10 % with the choices 5 % and 10 % (FRD §3.3 "selectable conservative ranges"; approver-10.68); deficit cap the smaller of 15 % and 500 kcal (R33; 500 is the approver lens's value, `assumption`); activity multiplier × 1.2 (FRD §11.3); Activity-adjusted credit 50 % of eligible exercise, up to 300 kcal a day (EA7, approver-10.69); default macro split protein 30 / carbohydrate 40 / fat 30 % (EA5, approver-10.70); Target review 14 days after approval (EA6, approver-10.70); GLP-1: protein 1.2–1.6 g/kg and no added deficit (R35, R41); tracking-only at ≥2 of 5 SCOFF-style items, pregnancy or breastfeeding (R38, R32), with the approver's guidance text in English and Arabic (approver-10.53); retention raw scans 30 days, audio 24 h (FR-078) |
| O1 Hala | Africa/Cairo (UTC+3 until 2026-10-29); iPhone in Arabic, region Egypt, Arabic-Indic digits; diary-day boundary 00:00; age 34, height 160 cm, weight 78 kg; equation version "−161"; no planned exercise; goal lose; safety screen skipped |
| O2 Sam | Europe/London; English; weight entered as 185 lb; measured resting value 1,779 kcal a day, measured at a clinic on 20 Sep 2026 (FRD §11.3); planned exercise 200 kcal a day; types his own Target 1,870 (the FRD §11.3 Target, 20 % below maintenance) |
| O3 Faisal | Asia/Riyadh; Arabic, region Saudi Arabia, Western digits (E41: both systems are used there); age 41, 176 cm, 80 kg; equation version "+5"; safety screen: GLP-1 medicine "Yes" |
| O4 Huda | Africa/Cairo; English; age 55, 156 cm, 60 kg; "−161"; goal lose |
| O5 Amal | Asia/Riyadh; Arabic; age 68, 152 cm, 52 kg; "−161" |
| T1 trial diary | On Hala's iPhone, no account: Units «قرصة جبنة» cheese bite v1 (5.4 g cheese + 1.5 g oil + 8 g bread, FRD §2.2), «شاي بلبن» tea with milk v1, «لقمة عيش» bread bite v1 (8 g); Template «فطار» (3 cheese bites + 1 tea with milk); 12 Entries, 7 on Day 2026-09-30 and 5 on Day 2026-10-01; 16 local commands in all |
| Consent text versions | as the auditor lens stores them (auditor-9.1): age confirmation `age-1`; Diary processing `diary-1`; AI Consent `c-ai-4`; Health purposes `health-1`; Optional research `research-1`. Microphone and Photos are given on their in-app sheets at first use (eater-4.4, 4.5); the auditor lens shows no text version for them yet (conflict C-21). None of these ids is shown to the eater |
| Consent purposes | as the auditor lens lists them (auditor-9.1): Diary processing · Send photos, voice and text to Google's AI (Gemini) · Health: read workouts · Health: read active energy · Health: read body mass · Health: write food · Microphone · Photos (camera and photo library) · Optional research |
| SE accounts (support lens fixtures, §0.3 there) | **SE1** `acct_9c41e2`: Arabic, Arabic-Indic digits, Asia/Riyadh, diary-day boundary 04:00, Sign in with Apple, support code `SB-7KQ2-94XM` issued 2026-10-01 06:12 UTC; per the auditor lens it gave Optional research on 2026-09-01 and withdrew it on the device at 2026-09-21T22:40:00Z, received 2026-09-22T06:05:30Z, delivered twice (auditor-9.6). **SE2** `acct_51ab07`: English, Africa/Cairo, email sign-in; deletion `DEL-26-0915-K3Q8` Requested 2026-09-15 07:00 UTC, Running on 2026-10-01, Completes 2026-10-14. **SE3** `acct_e07d13`: export `job_exp_4410` Failed after 3 attempts. **SE6** `acct_c2a917`: Arabic, Asia/Riyadh; AI Consent Withdrawn 2026-09-30 18:14 UTC; before it, 2 queued uploads, 3 cached private analyses, 4 raw photos, 1 audio clip and 1 prepared export; its Consent-withdrawal Privacy job Completed 18:20 UTC. **SE7** `acct_a41c55`: English, Africa/Cairo; export `job_exp_31` Completed 2026-09-20, download window over 2026-09-27. **SE8**: a finished deletion, reference `DEL-26-0820-M2V5`, requested 2026-08-20, Completed 2026-09-18; former email `lina.synthetic@example.com`. **SE10** `acct_f1e0c3`: English, Africa/Cairo; `grant_40ab` (reason "An Entry is missing or appears twice", case `CASE-1170`) Requested 2026-09-27 10:05 UTC → Unanswered 2026-09-30 10:05 UTC; `grant_52a3` Active 12:05 UTC on 2026-10-01 (1 h), Withdrawn by the eater 12:26 UTC; `grant_52a7` Active 13:04 UTC (1 h), Ended by the Support agent 13:33 UTC. **SE12** `acct_0d4e9b`: Grant `grant_8e20` (by `staff_omar`) Active when it requests deletion; this file adds that SE12 signed in with Apple. **SE13**: an anonymous-session eater and a local-trial install |
| Grants reused | **G1** `grant_31f0` for SE1 by `staff_mona` "Mona K." (Support agent): Requested 2026-10-01 10:05 UTC; reason "An Entry is missing or appears twice"; Days 2026-09-28 to 2026-09-30; areas "Entries and day reports" and "My Units"; 1 hour; case `CASE-1182`; Approved and Active 10:20 UTC; reads 10:24, 10:25 and 10:27 UTC; Expired 11:20 UTC (14:20 Riyadh). **G2** `grant_31f9` for SE1: Requested 11:30 UTC, Declined 11:42 UTC (14:42 Riyadh) |

**Numbers from the fixtures.** Resting energy by Mifflin–St Jeor (FRD §11.1). Maintenance = resting energy × 1.2 + planned exercise (FRD §11.3). Resting energy and maintenance are shown as computed, to at most one decimal, as FRD §11.3 shows 2,334.8. A Target is shown and approved rounded to the nearest 10 kcal, as FRD §11.3 shows 1,867.84 as 1,870 (EA4).

| eater | resting energy | ±10 % | maintenance | lose 5 % · 10 % · 15 % (default) | maintain | gain 5 % · 10 % (default) |
|---|---|---|---|---|---|---|
| Hala | 1,449 | 1,304.1–1,593.9 | 1,738.8 | 1,651.86 → 1,650 · 1,564.92 → 1,560 · 1,477.98 → **1,480** (deficit 260.82) | 1,740 | 1,825.74 → 1,830 · 1,912.68 → 1,910 |
| Sam | 1,779 (measured) | — | 2,334.8 | 15 %: 1,984.58 → 1,980 (deficit 350.22); his own **1,870** | 2,330 | 10 %: 2,568.28 → 2,570 |
| Faisal | 1,700 | 1,530–1,870 | 2,040 | every choice → **2,040** (GLP-1: no added deficit) | 2,040 | 10 %: 2,244 → 2,240 |
| Huda | 1,139 | 1,025.1–1,252.9 | 1,366.8 | 1,298.46 → 1,300 · 1,230.12 → 1,230 · 1,161.78 is below the floor → **1,200** (deficit 166.8, 12.2 %) | 1,370 | 10 %: 1,503.48 → 1,500 |
| Amal | 969 | 872.1–1,065.9 | 1,162.8 | not offered | 1,200 (floor, conflict C-1) | 10 %: 1,279.08 → 1,280 |

Macros for Hala's 1,480 at 30/40/30 (FRD §10.3): protein 111.0 g, carbohydrate 148.0 g, fat 49.333 g.

## Assumptions this file makes

| id | assumption | why this value |
|---|---|---|
| EA1 | The age gate takes whole years from 18 to 120. A larger number reads as a typo | catches slips; no source |
| EA2 | Profile ranges are height 100–250 cm and weight 30–300 kg. Values outside them ask "Check this number" | typo guard, not a clinical rule; no source |
| EA3 | Safety-screen answers are judged on the iPhone and then discarded. Only the resulting mode is stored | EX-31; R3 ("ask only for data relevant to its core function") |
| EA4 | The Target is approved as shown (nearest 10 kcal). The unrounded value stays in the input snapshot | FRD §11.3 shows 1,870 for 1,867.84; the eater approves what they see (FRD §23.2) — conflict C-8 |
| EA5 | The default macro split is 30/40/30 | synthetic fixture value; now a Policy row the approver owns (approver-10.70) |
| EA6 | The review date is the approval date + 14 days | FR-060's proposed minimum review window; now a Policy row (approver-10.70) |
| EA7 | Activity-adjusted credit is 50 % of eligible exercise, up to 300 kcal a day | synthetic; FRD §12.2 asks for "a visible user-approved credit factor and cap" but gives no values; now a Policy row (approver-10.69) |
| EA8 | Three gram-locked macros "fit" the Target when their 4/4/9 energy is within 5 kcal of it | gram rounding makes exact equality rare; no source |
| EA9 | A Completed export stays downloadable in the app for 7 days | support lens A8 |
| EA10 | An unanswered Grant request closes after 72 h | support lens A6 |
| EA11 | An anonymous session upgrades to an account and keeps its records | Firebase account linking was not opened in this run; admin-10.41 carries the analyses over |
| EA12 | In a local trial, public Food reference search reaches the API without a user identity (App Check only) | FR-001 asks for a session only for cloud AI (conflict C-16) |
| EA13 | 1 kcal is shown as 4.184 kJ | standard conversion; FAO's chapter (FRD S12) not opened in this run |

## Goals of the eater in these journeys

- **G1 · Start in seconds, at the table.** Log before any profile, Target or account is asked for (E17, E28, EX-03).
- **G2 · A Target I understand and chose.** Resting energy, maintenance, Target and macros, each shown with its method and assumptions, and approved by me (FR-003, FR-056–FR-058).
- **G3 · Safe and never judged.** Tracking-only and protein-first when they apply, no Target below the reviewed minimum, neutral words (FRD §3.3, §11.4; EX-42–EX-44).
- **G4 · My data stays mine.** Separate Consents that one tap withdraws, an export I can take, a deletion that really deletes (WF-9; R3, R4, R22).
- **G5 · Nobody reads my diary unless I approve.** And only for as long as I said (WF-10, eater side; research.md §3, WF-10 row).

The moment, the feeling and the style for each journey are in research.md Part 2: WF-1 "I could start right away, and nobody judged me"; WF-9 "My diary is mine; I can take it and go"; WF-10 "Nobody reads my diary unless I say yes". Large, calm and one-handed (§4 there).

---

# Journey 1 — Onboard and set a target (WF-1)

Steps, in the map's order (WF-1: "age gate → consents → profile → optional safety screen → resting energy → maintenance → target and macros (floor policy) → activity mode → approve. Tracking works before a target exists"):
**1A** Age gate → **1B** Consents → **1C** Tracking before a Target → **1D** Profile → **1E** Safety screen → **1F** Resting energy and maintenance → **1G** Target → **1H** Macros → **1I** Activity mode → **1J** Approve → **1K** Later changes → **1L** Local trial, account and move → **1M** Language, access and tone.

## 1A · Age gate

#### eater-1.1 · Confirm I am 18 or older before anything else
Covers: WF-1 step "age gate"; interaction row "Eater → app: confirm age 18+ … Consent records (version, time, method)"; FR-002 (age); FRD §3.2 ("explain why each required input matters"); blueprint §1.2 "Minor (hidden, excluded)" · R13, R16, R22 · E17 · EX-03, EX-10
Shared: eater + auditor (auditor-9.2, auditor-9.9).
As the eater, I enter my age on the first screen and see why it is asked, so that an adults-only app lets me in at once and uses that age later.
- `/r` **Given** a fresh install on the iOS simulator in Arabic with region Egypt, **When** the app opens, **Then** Onboarding · Age is the first screen, right to left, with one field "Your age" («عمرك»), a number keypad and the line "Why we ask: Sips & Bytes is for adults, and your age helps estimate your resting energy."; nothing else is asked.
- `/r` **Given** Hala types «٣٤» in Arabic-Indic digits, **When** she taps Continue, **Then** Onboarding · Consents opens, and when she later reaches Onboarding · Profile its age field already shows «٣٤».
- `/r` **Given** Onboarding · Age, **When** "abc", "0" or "17.5" is entered, **Then** the field says "Enter your age in whole years", and for "150" it says "Check this number" (EA1); Continue stays disabled in each case.
- `/s` **Given** Hala passed the gate on 2026-10-01 at 09:10 Cairo and later created an account, **When** `GET /v1/me/consents` is read, **Then** the first record is the age confirmation: text version `age-1`, method "onboarding age question", `made_at` 2026-10-01T06:10Z and the app version (auditor-9.9).
- `/s` **Given** an anonymous-session or account-creation request with no age confirmation of 18 or more, **When** it reaches the API, **Then** it returns 422 `AGE_REQUIREMENT` and no user record is created.

#### eater-1.2 · Under 18: no account, nothing kept but a count
Covers: WF-1 done-when ("Under 18: no account"); blueprint §1.2 "Minor (hidden, excluded) … an age gate keeps them out" · R16 (Google's AI terms bar services likely to be used by under-18s), R13 (the store's 9+ rating does not keep minors out)
Shared: eater + auditor (auditor-9.9).
As someone under 18, I am told plainly that the app is for adults, so that I am not drawn into an adult calorie tool and nothing that identifies me is kept.
- `/r` **Given** age 16 entered on Onboarding · Age, **When** Continue is tapped, **Then** Onboarding · Under 18 reads "Sips & Bytes is for adults 18 and over." with one button "Change my answer" that returns to the age field, and no path leads to Today, Onboarding · Consents or Onboarding · Account.
- `/s` **Given** the same session, **When** the test proxy's record of its network traffic is read, **Then** exactly one request was sent, `POST /v1/age-gate` with the age as its only field; it returned 422 `AGE_REQUIREMENT`; and the iPhone's local database holds no age value.
- `/s` **Given** that refusal, **When** the Audit trail is read on the emulator, **Then** one `age.refused` event exists with no account id, device id or other identifier, and no account, age record or Consent was created (auditor-9.9).
- `/r` **Given** Onboarding · Under 18 in Arabic with VoiceOver on, **When** it is read, **Then** VoiceOver reads the sentence once in Arabic, and the screen holds no word about weight, dieting or the body (EX-42).

## 1B · Consents

#### eater-1.3 · Choose each Consent on its own, with nothing chosen for me
Covers: WF-1 step "consents"; interaction row "give separate consents (diary processing; sending photos/voice/text to Google's AI, named; each Health type; mic; photos; optional research) … explicit, per purpose, one-tap withdrawal, no feature paywalled behind consent"; FR-076; FR-001 · R2, R3, R21, R22, R31 · E28 · EX-03, EX-26
Shared: eater + auditor (auditor-9.1, auditor-9.4).
As the eater, I decide separately where my diary lives, whether photos, voice and text go to Google's AI, and whether to help research, so that I agree only to what I want and can still start.
- `/r` **Given** Onboarding · Consents on the simulator, **When** it opens, **Then** it shows three separate parts: "Your diary", with two choices — "Keep it on this iPhone for now" (the main button) and "Keep it in my account" (opens Onboarding · Account); a switch "Send photos, voice and text to Google's AI (Gemini)", off; and a switch "Optional research", off. Below them is the line "Health, the microphone and photos (camera and photo library) are asked the first time you use them." (eater-4.4, 4.5, 7.1)
- `/r` **Given** both switches are left off, **When** "Keep it on this iPhone for now" is tapped, **Then** Today opens in the local trial and no further question is asked.
- `/r` **Given** only the AI switch was turned on, **When** the eater later opens Settings → Privacy, **Then** the AI Consent reads "Given", Optional research reads "Not given", and Diary processing reads "Not given · your diary is only on this iPhone".
- `/m` **Given** the Consent record schema, **When** a record names two purposes, **Then** validation fails.

#### eater-1.4 · Know what goes to Google's AI, and what never does
Covers: interaction rows "give separate consents (… sending photos/voice/text to Google's AI, named …)" and "Eater → AI analyzer: … Health data never sent (R7)"; FR-076, FR-079; FRD §19.1 ("Do not promise zero retention"); blueprint §1.7 (residency open question: no Gemini model runs in a Middle East region, as r1-refute-b established) · R2, R7, R17 · E30 · EX-31, EX-32
Shared: eater + auditor (auditor-9.3).
As the eater, I read in a few plain sentences what is sent, to whom, where, and what never leaves my iPhone, so that my "yes" to the AI is informed.
- `/r` **Given** Onboarding · Consents, **When** the eater taps "What is sent?" under the AI switch, **Then** the text names Google (Gemini) as the receiver; lists meal photos, voice recordings and typed food sentences as what is sent; says "Apple Health data is never sent"; says "Google does not train its models on them"; says "They are processed by Google outside Egypt and Saudi Arabia"; and says voice is deleted within 24 hours and raw photos within 30 days unless saved (conflict C-26: counsel may change this wording before launch).
- `/r` **Given** the same text in Arabic, **When** shown, **Then** "Google (Gemini)" stays one left-to-right run inside the right-to-left sentence, and the words match stored text version `c-ai-4` exactly (auditor-9.3).
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
Shared: eater WF-4 journey (eater-4.4, 4.5), WF-7 journey (eater-7.1).
As the eater, I am asked for the camera, the microphone or Apple Health only when I first use them, with one reason, so that saying no to one never takes away anything else.
- `/r` **Given** an eater who never opened Capture & Plan, **When** she opens it for the first time, **Then** a sheet gives one sentence on why the Photos Consent is needed, with "Give consent" and "Not now"; the iPhone's camera prompt appears only after "Give consent", never at app launch, and nothing about the microphone or Health is asked (eater-4.4).
- `/r` **Given** she tapped "Give consent" on 1 Oct 2026, **When** Settings → Privacy opens, **Then** the Photos row reads "Given · 1 Oct 2026", and `GET /v1/me/consents` shows method "in-app sheet · first use".
- `/r` **Given** she tapped "Not now" instead, **When** the eater returns to Today and taps a recent Unit, **Then** the Entry appears and the remaining figure changes within 300 ms (NFR-02), and the Photos row reads "Not given".
- `/r` **Given** "Health: read workouts" is Given in the app but iOS returns no workouts, **When** Today shows Activity, **Then** it reads "No data from Apple Health yet", never "denied" or "0 kcal" (P30, FR-067, EX-27).

## 1C · Tracking before a Target

#### eater-1.7 · Log my first food before any profile or Target
Covers: WF-1 "Tracking works before a target exists (brief §3.2)"; FR-001 (local trial); FR-008 ("Users may track without receiving an automated weight-change prescription"); FRD §3.2 · E17, E25, E28, E35 · EX-03
Shared: eater WF-3 journey (eater-3.2).
As the eater with food already on the table, I log within two screens of opening the app, so that my food does not go cold while I answer questions.
- `/r` **Given** a fresh install, **When** Hala enters her age and taps "Keep it on this iPhone for now", **Then** Today opens after exactly two screens (Onboarding · Age and Onboarding · Consents), and the Add button's centre lies within the middle two-thirds of the screen height on the iPhone 16e/17e simulator, measured in the UI test (E35).
- `/r` **Given** Today with no Units, **When** Hala taps Add, searches «عيش بلدي», picks the Food "Baladi bread" and enters «٨٠» g, **Then** the Entry «عيش بلدي · ٨٠ جم» appears with its kcal and Evidence badge, and the Day's consumed total rises by that kcal.
- `/s` **Given** the local trial, **When** the Entry is created, **Then** it is stored on the iPhone with an entry id, a command id and a diary-day id (FR-040 fields), and no request reached `/v1/consumption`.
- `/r` **Given** no network, **When** Hala logs a calorie-only amount "250 kcal · lunch plate", **Then** the Entry shows the "user-defined" badge, macros read "unknown", and the Day total rises by 250 (FR-016, AT-16).

#### eater-1.8 · Today without a Target is honest and quiet
Covers: WF-1 "Tracking works before a target exists"; FRD §14 Today mandatory states ("Empty, partial day … missing macros"); FR-008 · E17, E18 · EX-19, EX-27, EX-42
Shared: eater WF-3 journey (eater-3.2).
As the eater with no Target yet, I see what I ate with no made-up budget and one calm way to set a Target, so that the app neither nags nor pretends.
- `/r` **Given** Hala has a 250 kcal Entry and no Target, **When** Today opens, **Then** the headline reads "250 consumed · No Target yet" with a "Set a Target" link that opens the Target flow at its first unfinished step — Onboarding · Account while she is in the local trial (eater-1.49), Onboarding · Profile when she has an account and nothing is filled in (eater-1.45), the step where she stopped otherwise (eater-1.44) (eater-3.2), with no "left" or "over" figure and no macro progress bars; consumed macro grams are listed instead.
- `/r` **Given** the same eater, **When** Today is opened on 3 later Days, **Then** no card, badge or notification asks for a Target; the "Set a Target" link stays beside "No Target yet".
- `/r` **Given** an empty Today (no Entries, no Target), **When** it opens, **Then** it shows what to do next with two buttons, "Log what you ate" and "Make your first unit" (EX-19, eater-3.2).

#### eater-1.9 · Make a Unit and see its nutrition before any plan
Covers: FRD §3.2 ("A user may create a food unit and understand its nutrition before completing a weight-management plan"); WF-2 (input path); AT-13 · E2 · EX-03
Shared: eater WF-2 journey (Unit editor).
As the eater with no Target, I save "my cheese bite" and see what it holds, so that I can start with my own food before any plan.
- `/r` **Given** Hala in the local trial with no Target, **When** she saves «قرصة جبنة» in the Unit editor as 5.4 g cheese + 1.5 g oil + 8 g bread, **Then** My Units lists it with its three components and its kcal and macros per bite, and Today's consumed total is unchanged (WF-2 done-when, AT-13).
- `/r` **Given** the saved Unit, **When** she opens it from My Units, **Then** nothing on the screen asks for a Target, a profile or an account.

## 1D · Profile

#### eater-1.10 · Enter age, height and weight in my units and my digits, and see why
Covers: WF-1 step "profile"; FR-002 ("age, height, weight, preferred units, time zone"); FRD §3.2 ("explain why each required input matters"), §14.1 (Arabic-Indic and Western numerals, decimal input); blueprint §1.6 (display units) · E40, E41 · EX-10, EX-39
As the eater, I enter my body measures the way I write numbers, in units I know, and see what they are for, so that the calculation starts from true values I chose to give.
- `/r` **Given** Hala on Onboarding · Profile in Arabic, **When** she types height «١٦٠» and weight «٧٨», **Then** the fields read «١٦٠ سم» and «٧٨ كجم», age already reads «٣٤» from the age gate, and under the fields a line reads "Why we ask: age, height and weight estimate your resting energy. They are kept in your account and never sent to the AI."
- `/m` **Given** the number parser, **When** it reads «٧٨٫٥», «٧٨.٥» and "78.5", **Then** each gives 78.5.
- `/r` **Given** Sam on Onboarding · Profile in English with body weight in lb, **When** he types 185, **Then** the field reads "185 lb" with "83.9 kg" beneath it, and the stored value is 83.91459 kg (185 × 0.45359237).
- `/r` **Given** Hala switched energy to kJ in the units row, **When** Onboarding · Review later shows her Target, **Then** it reads "6,192 kJ a day" (1,480 × 4.184, EA13).

#### eater-1.11 · Impossible values are caught beside the field, and slips get a fix
Covers: FR-006 ("Reject negative values and invalid units"); FR-002 · care.md group 4 ("quietly fix an obvious slip") · EX-23
As the eater, I see a wrong number flagged where I typed it, so that I fix it in a second and never get a Target from a typo.
- `/r` **Given** Onboarding · Profile, **When** weight "−5" or "0" is typed, **Then** the weight field says "Enter a weight above 0" and Continue is disabled.
- `/r` **Given** height "1.60" with the unit cm, **When** typed, **Then** the field offers "Did you mean 160 cm?" with one tap to accept; nothing changes unless it is tapped.
- `/r` **Given** weight 450 kg or height 300 cm, **When** typed, **Then** "Check this number" appears beside the field (EA2) and Continue stays disabled.
- `/s` **Given** `POST /v1/targets/proposals` with `weight_kg: -5`, or with height given in "ml", **When** received, **Then** it returns 422 `VALIDATION_ERROR` naming the field, and no proposal.

#### eater-1.12 · Choose the equation version knowing why — or skip it
Covers: FR-002 ("Collect the physiological equation coefficient only with an explanation; never infer it from a photograph or gender presentation"); FRD §11.1 ("+5 and −161 for the male and female equation variants") · EX-31, EX-42
As the eater, I choose which published version of the resting-energy equation fits my body after reading why it is asked, so that nothing about me is guessed.
- `/r` **Given** Onboarding · Profile, **When** the equation question shows, **Then** it says that the equation has two published versions that differ by body physiology and that the choice only selects the formula; it offers "+5 (the equation's 'male' version)", "−161 (the equation's 'female' version)" and "Skip — I'll enter a measured value or my own Target", with none preselected.
- `/r` **Given** "Skip", **When** Onboarding · Energy opens, **Then** resting energy reads "Not calculated", with "Enter a measured value", "Enter my own maintenance" and "Track without a target"; no estimate is shown.
- `/s` **Given** no choice was made, **When** `POST /v1/targets/proposals` is called, **Then** the request carries `coefficient: null` and the response has `rmr: null` — never a default or an inferred value.

#### eater-1.13 · Region, time zone, language, numerals and units come from my iPhone, and I can change them
Covers: FR-002 (preferred units, time zone); FRD §3.2 ("explain why each required input matters"); blueprint §0 line 5 (reach), §1.6 user settings (language, numerals, dialect, display units) · E27 (Saudi Arabia missing as a country in MFP), E41 · EX-05, EX-07
As the eater, I find my region, language, numerals and units already right, see what each one decides, and can change any of them in one place, so that I don't configure an app before using it.
- `/r` **Given** Faisal's iPhone in Arabic with region Saudi Arabia and Western digits, **When** Onboarding · Profile opens, **Then** one row reads Saudi Arabia · Riyadh time · Arabic · Western digits · kg, cm, kcal (in Arabic, Western digits) with "Change" and the line "Your time zone decides which Day a meal belongs to. Your region sets food names and digits."; every later onboarding screen uses Western digits inside Arabic text.
- `/r` **Given** "Change" (the same choices live later in Settings → Units & language), **When** the region list opens, **Then** it lists Egypt and Saudi Arabia with the current region first; choosing one shows the dialect it sets for food names (Egypt → EG, Saudi Arabia → Gulf; blueprint §1.6, F27) on the same sheet.
- `/s` **Given** the profile is saved, **When** `GET /v1/me` is read, **Then** it returns `time_zone` "Asia/Riyadh", `locale` "ar-SA", `numerals` "western" and `display_units` {mass g, body kg, energy kcal} (FRD §17 UserProfile).

#### eater-1.14 · Take my weight from Apple Health — or type it
Covers: interaction row "HealthKit → app → API: import … body mass"; FR-002; FRD §3.2 · R7; P29, P30 · EX-10, EX-26, EX-27
Shared: eater WF-7 journey (eater-7.1).
As the eater who already weighs in with Apple Health, I fill my weight from it after a one-sentence ask, so that I don't retype a number the phone knows — and nothing breaks if I don't.
- `/r` **Given** Onboarding · Profile, **When** "Use my weight from Apple Health" is tapped, **Then** the Health sheet of eater-7.1 opens with only "Body mass (weight)" and its one-sentence reason; after she turns that switch on, the iOS Health sheet lists only Body Mass under read, and Settings → Privacy shows "Health: read body mass · Given".
- `/r` **Given** Health holds 78.0 kg from 29 Sep 2026, **When** it is read, **Then** the weight field shows 78 kg with "From Apple Health · 29 Sep" and stays editable.
- `/r` **Given** Health returns nothing (no samples, or read access denied — the app cannot tell which, P30), **When** read, **Then** the field says "No weight found in Apple Health" and stays empty for typing; it never says "access denied".

#### eater-1.15 · Tell the app which foods I don't eat — or skip it
Covers: FR-008 ("Capture dietary exclusions … without forcing unrelated sensitive details"); FR-049 (exclusions feed the planner); FRD §3.2 (why each input matters), §19.3 ("Food-allergy absence cannot be certified from a photo") · EX-32
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
Covers: interaction row "answer the optional safety screen … ≥2 SCOFF yes … → tracking-only"; FR-008; FRD §3.3 ("Unsupported or clinically sensitive cases remain in tracking-only mode with appropriate guidance"), §11.4 ("do not generate restrictive plans"), §19.3; WF-1 done-when ("neutral wording") · R37, R38 · EX-42, EX-44
Shared: eater + nutrition approver (approver-10.53 sets the cut-off and the guidance).
As the eater whose answers suggest a calorie target could harm me, I get full tracking without a weight-change Target, explained without alarm, so that I keep a useful diary without being labelled.
- `/r` **Given** an eater answers "Yes" to 2 of the 5 SCOFF-style questions on Onboarding · Safety screen (Policy v1 cut-off ≥2), **When** they tap Continue, **Then** Onboarding · Safety screen shows, in place of its questions, the tracking-only guidance text of the Policy in effect, word for word in the eater's language (approver-10.53), with one button "Continue with tracking"; Onboarding · Energy, Target and Macros are not shown (conflict C-23).
- `/r` **Given** 1 "Yes" and 4 "No", **When** they continue, **Then** Onboarding · Energy opens next with the standard Target flow.
- `/m` **Given** the rule with Policy v1, **When** the answers are (yes, yes, no, no, no), (yes, prefer not to say × 4) and (no × 5), **Then** the modes are `tracking_only`, `standard` and `standard`.
- `/m` **Given** the guidance strings in English and Arabic, **When** scanned, **Then** none names a diagnosis (FRD §19.3 "avoid diagnosis").

#### eater-1.18 · Pregnancy or breastfeeding gives tracking-only
Covers: WF-1 done-when ("A safety-screen answer of pregnancy gives tracking-only mode with neutral wording"); interaction row safety screen ("pregnancy or breastfeeding → tracking-only"); FRD §1.3 (excluded: unsupervised pregnancy weight planning), §3.3 · R32 (NIDDK's planner is not for pregnant or breastfeeding women, r1-refute-b) · EX-44
Shared: eater + nutrition approver (approver-10.53: pregnancy and breastfeeding are locked triggers).
As a pregnant or breastfeeding eater, I keep tracking without the app setting a weight-change Target, so that I follow my clinician, not a formula.
- `/r` **Given** "Are you pregnant?" answered "Yes" on Onboarding · Safety screen in English, **When** Continue is tapped, **Then** the same tracking-only guidance as in eater-1.17 shows with one button "Continue with tracking", and the words "diet", "lose" and "restrict" do not appear on the screen.
- `/r` **Given** "Are you breastfeeding?" answered «نعم» in Arabic, **When** Continue is tapped, **Then** the guidance appears in Arabic, right to left.
- `/s` **Given** either answer, **When** the mode is saved, **Then** `PUT /v1/me/safety-mode` carries `tracking_only` and no field naming pregnancy or breastfeeding, and `POST /v1/targets` for that eater returns 422 `VALIDATION_ERROR` with reason `tracking_only`.

#### eater-1.19 · Tracking-only is the whole app, without a weight-change Target
Covers: WF-1 done-when (tracking-only with neutral wording); FR-008; FRD §11.4 ("retain neutral tracking and access to existing data") · E24 · EX-43, EX-44
Shared: eater + support agent (support-10.14: under a Grant the day report reads "Target — not included in Grants", and the mode never shows).
As an eater in tracking-only mode, I log, make Units, read reports and export like everyone else, so that the mode never feels like a lesser app or shows itself to people near me.
- `/r` **Given** an eater in tracking-only mode with a 250 kcal Entry, **When** Today opens, **Then** it reads "250 consumed · No Target", as for an eater who never set one, but without the "Set a Target" link; the words "tracking only" do not appear on Today.
- `/r` **Given** the same eater, **When** they tap a recent Unit, open My Units, open Progress and tap Settings → Export → "Prepare my export", **Then** the Entry appears on Today, My Units lists their Units, Progress shows the week's consumed kcal and coverage with no "left" or "over", and the export reaches Completed.
- `/r` **Given** Settings → Goals, **When** opened, **Then** it reads "Tracking only — no calorie target is set" with the guidance and "Retake the safety questions", and has no "Enter my own target" control (conflict C-2).
- `/m` **Given** the day-report projection for an eater with no Target, **When** built, **Then** its target, remaining and over fields are null, never 0.

#### eater-1.20 · On a GLP-1 medicine: protein first, no added deficit
Covers: interaction row safety screen ("GLP-1 → protein-first, no added deficit"); blueprint §1.6 Policy ("GLP-1 protein-first 1.2–1.6 g/kg"); FR-005, FR-058 · R35, R41 · E24 · EX-42
Shared: eater + nutrition approver (approver-10.52).
As an eater taking a GLP-1 medicine, I get a Target that adds no deficit and puts protein first, so that the plan supports me instead of cutting further.
- `/r` **Given** Faisal (80 kg, maintenance 2,040) answered "Yes" to the GLP-1 question, **When** Onboarding · Target opens with Lose selected, **Then** the Target reads 2,040 kcal a day with "No added deficit while you take a GLP-1 medicine. Follow your prescriber's advice.", no loss choices and no "below maintenance" line.
- `/r` **Given** the same, **When** Onboarding · Macros opens, **Then** protein reads "96–128 g a day (1.2–1.6 g per kg)" set at 96 g and locked, carbohydrate 237 g and fat 79 g share the rest in the default 40:30 ratio, and the protein control moves only between 96 and 128 g.
- `/m` **Given** resting 1,700, multiplier 1.2, weight 80 kg and Policy v1 with GLP-1, **When** the proposal is computed, **Then** target 2,040.0, protein 96.0 g locked, carbohydrate 236.571 g and fat 78.857 g.
- `/r` **Given** Faisal's approved Target, **When** Today opens, **Then** it shows "Target 2,040" in Western digits inside Arabic text, and nothing on Today mentions the medicine.

#### eater-1.21 · My safety answers stay on my iPhone; only the mode is kept
Covers: interaction row "SafetyScreen"; FR-008; FRD §17 UserProfile ("Does not store health preferences in analytics events"), §19.2 (no sensitive profile values in logs) · R3, R21 · EX-29, EX-31
Shared: eater + support agent (support-9.5, support-10.14).
As the eater, I know my answers about eating, pregnancy or medicine are neither stored nor seen by anyone, so that I can answer honestly.
- `/r` **Given** Onboarding · Safety screen, **When** it opens, **Then** a line reads "Your answers stay on this iPhone. Only the result is saved: standard, tracking only or protein first."
- `/s` **Given** any completed screen, **When** the app's requests are recorded, **Then** `PUT /v1/me/safety-mode` carries only the mode, the screen version and the time, and no request body, log line or crash report contains an answer (EA3; eater-9.13).
- `/s` **Given** the iPhone's local database after the screen, **When** inspected in a UI test, **Then** it stores the mode and no answer.
- `/s` **Given** every `/v1/support/*` response about that eater (support-9.5), **When** its JSON is scanned, **Then** it holds no `mode` key and no answer.

#### eater-1.22 · Retake or clear the safety screen later
Covers: FR-008; FRD §11.4; FR-058, FR-071 (past Days keep their Target) · EX-44
As the eater whose situation changed — after a pregnancy, or after stopping a medicine — I retake the questions from Settings, so that my mode follows my life without contacting anyone.
- `/r` **Given** an eater in tracking-only mode, **When** they tap Settings → Goals → "Retake the safety questions" and answer with no trigger, **Then** Onboarding · Energy opens next; after approval Today shows a Target, and the earlier tracking-only Days still read "No Target" in Progress.
- `/r` **Given** Faisal in protein-first mode, **When** he retakes the questions and answers the GLP-1 question "No", **Then** the next Lose proposal adds the default deficit (1,734 → 1,730 kcal a day) and protein is no longer locked.
- `/s` **Given** a retake that changes the mode, **When** stored, **Then** a new mode record with its own time is kept, and no earlier Target version is edited.

## 1F · Resting energy and maintenance

#### eater-1.23 · See my resting energy, its method and a ±10 % note
Covers: WF-1 step "resting energy"; WF-1 done-when ("sees resting energy (±10 % note)"); interaction row "set a target … Mifflin–St Jeor estimate ±10 % (R36)"; FR-056; FRD §11.1 · R36 · E40 · EX-32
As the eater, I see my estimated resting energy with how it was worked out and how far off it may be, so that I trust it as an estimate, not a test result.
- `/r` **Given** Hala's profile, **When** Onboarding · Energy opens, **Then** it reads "Resting energy about 1,449 kcal a day", "Estimated with the Mifflin–St Jeor equation from your age, height, weight and the version you chose", "An estimate, not a measurement: for many people it lands within 10 % of a measured value — here 1,304.1 to 1,593.9 kcal", and "Often called BMR".
- `/r` **Given** the same in Arabic with Arabic-Indic digits, **When** shown, **Then** the numbers read «١٬٤٤٩» and «١٬٣٠٤٫١–١٬٥٩٣٫٩» and the note «±١٠٪», with no digit order reversed (E40).
- `/m` **Given** the resting-energy function, **When** called with (78 kg, 160 cm, 34 y, −161), (80, 176, 41, +5) and (60, 156, 55, −161), **Then** it returns exactly 1,449.0, 1,700.0 and 1,139.0.

#### eater-1.24 · Use a measured resting value instead
Covers: FR-004 ("measured resting expenditure. Preserve its source and effective date"); FRD §11.1 ("alternative measured-value override"), §11.3
As an eater whose resting energy was measured, I enter that value and where it came from, so that the plan uses my measurement and remembers its source.
- `/r` **Given** Sam on Onboarding · Energy, **When** he taps "Enter a measured value", types 1,779, picks the source "Measured at a clinic" and the date 20 Sep 2026, **Then** resting energy reads "1,779 kcal a day · measured · 20 Sep 2026", and the ±10 % note is replaced by "Your measured value".
- `/s` **Given** Sam approves later, **When** `GET /v1/targets` is read, **Then** the version's input snapshot holds `rmr` 1,779, `rmr_source` "measured_clinic", `rmr_effective_date` 2026-09-20 and `method` "measured".
- `/r` **Given** "0" or "−1,779" is typed, **When** entered, **Then** the field says "Enter a value above 0" and nothing is used.

#### eater-1.25 · See maintenance, the activity assumption, and whether exercise is in it
Covers: WF-1 step "maintenance"; FR-057 ("reviewed activity policy … Store whether exercise is included"); FRD §11.3 (worked example) · E18 · EX-14
Shared: eater + nutrition approver (approver-10.69).
As the eater, I see how my maintenance is built from resting energy, everyday activity and planned exercise, so that I know what the Target already contains.
- `/r` **Given** Sam (resting 1,779, planned exercise 200 kcal a day), **When** Onboarding · Energy shows maintenance, **Then** it reads "Maintenance 2,334.8 kcal a day · × 1.2, exercise included" with the line "= 1,779 × 1.2 for everyday activity + 200 planned exercise" (approver-10.69).
- `/r` **Given** Hala left planned exercise empty, **When** shown, **Then** it reads "Maintenance 1,738.8 kcal a day · × 1.2, no exercise included" with the line "= 1,449 × 1.2 for everyday activity".
- `/m` **Given** (1,779, 1.2, 200) and (1,449, 1.2, 0), **When** maintenance is computed, **Then** it returns exactly 2,334.8 and 1,738.8.
- `/s` **Given** Sam approves, **When** the version is read, **Then** `maintenance` 2,334.8, `multiplier` 1.2, `planned_exercise_kcal` 200, `exercise_included` true and `policy_version` v1.

#### eater-1.26 · Use my own maintenance value
Covers: FR-057 ("or approved manual value"); FR-004 (source and effective date); FR-007
As an eater who knows my maintenance from a clinician or my own records, I enter it instead of the estimate, so that the Target starts from a number I trust.
- `/r` **Given** Huda on Onboarding · Energy, **When** she taps "Enter my own maintenance", types 1,500, picks the source "Clinician-provided" and answers "Is your usual exercise already in this number?" with "Yes", **Then** maintenance reads "1,500 kcal a day · clinician-provided · exercise included", and Onboarding · Target's default Lose proposal becomes 1,280 kcal a day (1,275 rounded).
- `/s` **Given** her approval, **When** the version is read, **Then** `maintenance_source` "clinician", `maintenance` 1,500, `exercise_included` true and `effective_from` the approval Day.

#### eater-1.27 · Every energy number comes from code, never from the AI
Covers: FR-056 ("calculate in code, never by generated prose"); FRD §1 principle ("Deterministic code calculates"), §16.2 (the AI may not change user goals); NFR-01 · E15, E22 · EX-14
As the eater, I get the same resting energy, maintenance and Target every time I enter the same numbers, so that my Target never drifts like a chatbot's answer.
- `/s` **Given** Hala's Target flow run on 2026-10-01 and again on 2026-10-08 with the same inputs and Policy v1, **When** both proposals are compared, **Then** every number is identical, and the Gemini adapter mock recorded 0 calls in both runs.
- `/m` **Given** the golden cases in the fixtures table (Hala, Sam, Faisal, Huda, Amal), **When** the proposal module runs, **Then** each value matches to the stated decimals.
- `/r` **Given** the kill switch is On for every AI task (platform admin lens), **When** Hala runs the Target flow on the simulator, **Then** Onboarding · Energy and Onboarding · Target show resting energy 1,449, maintenance 1,738.8 and Target 1,480, the same as with the switch Off (AT-32 pattern).

## 1G · Target

#### eater-1.28 · Choose lose, maintain or gain, and a conservative step, and see the gap from maintenance
Covers: WF-1 step "target … (floor policy)"; interaction row "set a target … policy defaults … deficit cap within AHA's 500–750 kcal"; FR-003 ("Offer lose, maintain, or gain weight"); FR-058 ("explicit deficit/surplus"); FRD §3.3 (defaults 0 %, 15 % below, 10 % above, "with selectable conservative ranges") · R33 · EX-09, EX-42
Shared: eater + nutrition approver (approver-10.51, approver-10.68).
As the eater, I pick lose, maintain or gain, choose how big a step from the reviewed choices, and see exactly how far it sits from maintenance, so that I know what I am agreeing to.
- `/r` **Given** Hala (maintenance 1,738.8) on Onboarding · Target, **When** she selects Lose, **Then** exactly three choices show, with 15 % preselected (approver-10.68): "1,650 kcal a day · 87 kcal below maintenance (5 %)", "1,560 kcal a day · 174 kcal below maintenance (10 %)" and "1,480 kcal a day · 261 kcal below maintenance (15 %)".
- `/r` **Given** the same, **When** she selects Maintain, **Then** it reads "1,740 kcal a day · maintenance 1,738.8, rounded to the nearest 10"; **when** she selects Gain, **then** two choices show, with 10 % preselected: "1,830 kcal a day · 87 kcal above maintenance (5 %)" and "1,910 kcal a day · 174 kcal above maintenance (10 %)".
- `/r` **Given** Sam (maintenance 2,334.8), **When** he selects Lose at 15 %, **Then** it reads "Target 1,980 kcal a day · 350 kcal below maintenance (15 %)".
- `/m` **Given** maintenance 3,600, **When** the 15 % loss deficit is computed, **Then** it is 500 kcal (the cap), not 540; for 2,334.8 it is 350.22 (approver-10.51).
- `/r` **Given** Onboarding · Target opens, **When** no goal has been chosen, **Then** none is preselected and Continue is disabled until one is.

#### eater-1.29 · A review date, and no promised weekly weight change
Covers: FR-003 ("proposed review date before approval"); FR-059 ("Show a range/uncertainty note for projected progress; do not promise a fixed weekly weight change from a calorie formula") · EX-32
Shared: eater + nutrition approver (approver-10.70).
As the eater, I see when to look at my Target again and an honest note about progress, so that I am never promised a number of kilos.
- `/r` **Given** Hala selects Lose on 1 Oct 2026, **When** Onboarding · Target shows the proposal, **Then** it reads "Review on 15 Oct 2026" and "Weight change differs from person to person. Progress will show your trend once there is enough data."
- `/m` **Given** every string of Onboarding · Target and Onboarding · Review in English and Arabic, **When** scanned, **Then** none states a weight per week or per month ("kg a week", "lb a week", «كجم في الأسبوع»).
- `/s` **Given** her approval, **When** the version is read, **Then** `review_date` is 2026-10-15, the approval Day + 14 days (EA6).

#### eater-1.30 · No proposal below the reviewed minimum
Covers: interaction row "set a target … policy floor 1,200 kcal (approver-owned)"; WF-1 done-when ("a target not below the policy floor"); FRD §3.3 (centrally versioned safety boundaries) · R33, r1-refute-b Dropped 12 · EX-42
Shared: eater + nutrition approver (approver-10.49, approver-10.57).
As the eater whose 15 % loss would fall below the reviewed minimum, I get a Target at the minimum with a calm reason, so that the app never proposes too little.
- `/r` **Given** Huda (maintenance 1,366.8), **When** she selects Lose, **Then** the choices read "1,300 kcal a day (5 %)", "1,230 kcal a day (10 %)" and, for 15 %, "1,200 kcal a day · 167 kcal below maintenance (12.2 %)" with the line "We don't propose targets below 1,200 kcal a day, the reviewed minimum."
- `/r` **Given** Amal (maintenance 1,162.8), **When** Onboarding · Target opens, **Then** Lose is shown unavailable with "Your maintenance is already close to the reviewed minimum of 1,200 kcal a day", and Maintain reads "1,200 kcal a day" (conflict C-1).
- `/r` **Given** Policy v2 (floor 1,300) In effect from 15 Oct 2026 (approver-10.57), **When** Huda starts a new proposal on 16 Oct, **Then** no Lose choice is below 1,300.
- `/s` **Given** `POST /v1/targets` with Target 1,150 for Huda, **When** received, **Then** it returns 422 `POLICY_FLOOR` with `limit` "floor", the floor value and the Policy version.

#### eater-1.31 · A Target below 1,000 kcal is never accepted
Covers: interaction row "set a target … hard stop below 1,000 (R32)"; FRD §19.3 ("Dangerous restriction requests require a safe response rather than a mathematically optimized starvation plan") · R32 (NIDDK resets the last change and suggests more time, a different activity level or a different goal) · EX-42
Shared: eater + nutrition approver (approver-10.50).
As the eater typing a very low number, I see the field reset and kinder options, so that no setting can make the app plan starvation.
- `/r` **Given** Huda's Target field holds 1,200 on Onboarding · Target, **When** she types 900 under "Enter my own target", **Then** the field returns to 1,200 and reads "Sips & Bytes doesn't set targets below 1,000 kcal a day. You could allow more time, add activity, or choose Maintain." with Maintain offered.
- `/r` **Given** she types 1,100, **When** entered, **Then** the value stays, the field reads "Below the reviewed minimum of 1,200 kcal a day", "Approve target" is disabled, and two buttons offer "Use 1,200" and "Track without a target".
- `/s` **Given** `POST /v1/targets` with 999 kcal and any source (typed by the eater or clinician-provided), **When** received, **Then** it returns 422 `POLICY_FLOOR` with `limit` "hard_stop".

#### eater-1.32 · Enter my own or my clinician's Target, with its source and date
Covers: FR-004 ("Allow a manually entered or clinician-provided calorie target … Preserve its source and effective date"); FR-058; interaction row "set a target … deficit cap within AHA's 500–750 kcal"; FRD §11.3 (the 1,870 example) · R33 · EX-14
Shared: eater WF-8 journey (eater-8.23, sources in Progress → Target history).
As an eater with a target of my own, I type it and say where it came from, so that the app uses it, tells me plainly how it compares, and remembers its source.
- `/r` **Given** Sam (maintenance 2,334.8) on Onboarding · Target, **When** he taps "Enter my own target", types 1,870 and picks the source "Typed by you", **Then** it reads "Target 1,870 kcal a day · typed by you · 465 kcal below maintenance (19.9 %)", "Includes your 200 kcal of planned exercise" (FRD §11.3), and the note "This is a bigger gap than Sips & Bytes proposes (up to 350 kcal for you). Your own number is kept." (conflict C-22).
- `/r` **Given** the source "Clinician-provided" instead, **When** approved on 1 Oct 2026, **Then** Settings → Goals reads "1,870 kcal a day · clinician-provided · from 1 Oct 2026", and Progress → Target history lists it with that source (eater-8.23).
- `/s` **Given** "Typed by you", **When** `GET /v1/targets` is read, **Then** `target` 1,870, `source` "typed_by_eater", `effective_from` the Day of 2026-10-01 in Europe/London, `deficit_kcal` 464.8, `over_deficit_cap` true and `maintenance` 2,334.8 in the snapshot.

## 1H · Macros

#### eater-1.33 · See macro grams from percentages, with the 4/4/9 label
Covers: WF-1 step "target and macros"; WF-1 done-when ("macro grams and assumptions"); FR-005; FRD §10.1 (label "share of macro-derived energy (4/4/9)"), §10.3 · E41 · EX-11
Shared: eater + nutrition approver (approver-10.70).
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
- `/s` **Given** `POST /v1/targets` with 46 / 32 / 24, **When** received, **Then** it returns 422 `VALIDATION_ERROR` with reason `macro_total`, the total 102 and the normalised alternative, and no version is stored.
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
- `/s` **Given** the same locks posted to `POST /v1/targets`, **When** received, **Then** it returns 422 `VALIDATION_ERROR` with reason `locks_incompatible`, the needed 1,666 kcal and the Target 1,480.
- `/m` **Given** three gram locks whose 4/4/9 energy is within 5 kcal of the Target (EA8), **When** validated, **Then** they are accepted; 6 kcal apart, they are not.

#### eater-1.38 · Wrong macro input is refused beside the field
Covers: FR-006 ("Reject negative values and invalid units")
As the eater, I see a slip in a macro field flagged where I typed it, so that nothing contradictory is saved.
- `/r` **Given** Onboarding · Macros, **When** protein "−10 %", carbohydrate "140 %" or fat "abc" is typed, **Then** that field says "Enter a share from 0 to 100 %" (or "Enter a number"), and Continue is disabled.
- `/r` **Given** grams mode, **When** "−5" is typed, **Then** the field says "Enter 0 g or more".
- `/s` **Given** `POST /v1/targets` with a macro unit "ml", **When** received, **Then** it returns 422 `VALIDATION_ERROR` naming the field.

## 1I · Activity mode

#### eater-1.39 · Answer whether my usual exercise is already in my maintenance
Covers: WF-1 step "activity mode"; FR-007 ("Ask whether ordinary exercise is already included in the selected maintenance estimate"); FR-057; FRD §11.3 ("not a 1,870 'net-food' allowance to which the same 200 kcal is added again"), §12.1; AT-23 · E18 · EX-10, EX-14
Shared: eater WF-7 journey (eater-7.16).
As the eater, I answer whether my usual exercise is already counted, so that the same workout is never counted twice.
- `/r` **Given** Sam's maintenance includes 200 kcal of planned exercise, **When** Onboarding · Activity mode opens, **Then** the question "Is your usual exercise already in your maintenance?" is answered "Yes — 200 kcal of planned exercise is included" (editable), and "Fixed mode" is selected with the line "Exercise you log or import shows beside your food; it doesn't add to your food Target."
- `/r` **Given** Sam approved Target 1,870 in Fixed mode and has 1,200 kcal consumed, **When** a 200 kcal workout imports from Apple Health, **Then** Today still reads Target 1,870 and remaining 670, and the Activity row reads "200 kcal active · not added to your food Target (Fixed)" (eater-7.16, AT-23).
- `/r` **Given** Hala (no planned exercise), **When** the screen opens, **Then** the question reads "No exercise is included", with Fixed mode selected by default (FRD §12.1).

#### eater-1.40 · Choose Activity-adjusted mode knowingly
Covers: FR-007; FRD §12.2 ("a documented baseline that excludes the selected exercise component … a visible user-approved credit factor and cap … never simultaneously apply an all-in multiplier and full wearable expenditure") · E18 · EX-14
Shared: eater WF-7 journey (eater-7.18, 7.19); nutrition approver (approver-10.69).
As the eater who wants exercise to add to my food budget, I choose that mode and approve how much counts, so that exercise is never counted twice or without my say.
- `/r` **Given** Sam (maintenance 2,334.8 with 200 planned exercise, own Target 1,870), as an alternative to eater-1.39, **When** he chooses "Activity-adjusted mode" on Onboarding · Activity mode, **Then** it shows "Base food Target 1,710 (maintenance without exercise 2,134.8, −20 %)", "Credit 50 % of eligible Activity" and "Cap 300 kcal a day" (EA7), the line "Planned exercise is taken out, so the exercise you log can count instead", and the formula "Target today = 1,710 + Activity credit"; Continue needs the credit and cap confirmed (eater-7.18).
- `/m` **Given** maintenance without exercise 2,134.8 and Sam's own gap 1,870 / 2,334.8, **When** the base is computed, **Then** it is 1,709.78, shown as 1,710.
- `/m` **Given** factor 0.5 and cap 300, **When** an imported net workout of 400 kcal and one of 800 kcal are credited, **Then** the credits are 200 and 300 kcal.
- `/s` **Given** his approval, **When** the version is read, **Then** `activity_mode` "adjusted", `exercise_included` false, `base_food_target` 1,710, `credit_factor` 0.5 and `credit_cap` 300.

#### eater-1.41 · Connect Apple Health here, or not — the Target never depends on it
Covers: FRD §3.2 ("Goals, permissions, and health connections are separate choices. Declining activity access must not block manual food logging."); interaction row "HealthKit → app → API: import workouts, active energy, body mass"; FR-076 · R7; P29, P30 · EX-26
Shared: eater WF-7 journey (eater-7.1), WF-3 journey (eater-3.39, writing meals to Health).
As the eater, I can connect Apple Health from the activity step or leave it, so that my Target is set either way.
- `/r` **Given** Onboarding · Activity mode, **When** "Connect Apple Health (optional)" is tapped, **Then** the sheet of eater-7.1 opens, listing "Workouts", "Active energy" and "Body mass (weight)", each with one sentence on why and its own switch, all off; writing meals to Health is not asked here (eater-3.39 asks after the first Confirmed Entry).
- `/r` **Given** Hala skips it, **When** she approves her Target and then taps a recent Unit on Today, **Then** Today shows "Target 1,480", Activity reads "Apple Health not connected", and the Entry appears.
- `/s` **Given** no Health Consent, **When** a full UI walk of the Target flow runs, **Then** no HealthKit authorization request is made.

## 1J · Approve

#### eater-1.42 · Review everything once, then approve
Covers: WF-1 step "approve"; WF-1 done-when; interaction row "set a target → GoalPlanVersion … approval → target approved"; FR-003 ("Show the estimated resting requirement, estimated maintenance, intake target, assumptions, and proposed review date before approval"); FR-058 ("Store every accepted target as an effective-dated version"); FRD §17 GoalPlanVersion · EX-09, EX-15
As the eater, I see my whole Target on one screen and approve it with one button, so that I know exactly what I agreed to.
- `/r` **Given** Hala at the end of the Target flow at 10:30 on 1 Oct 2026 (Cairo), **When** Onboarding · Review opens, **Then** it lists resting energy 1,449 (estimate, ±10 %), maintenance 1,738.8, Target 1,480 (15 % below maintenance), protein 111 g, carbohydrate 148 g, fat 49 g, Fixed mode, "Review on 15 Oct 2026", and the assumptions (the equation and the version chosen, everyday activity × 1.2, no planned exercise), with one main button "Approve target".
- `/r` **Given** "Approve target" is tapped, **When** the server confirms, **Then** Today shows "Target 1,480", "Food Target: Fixed", and a remaining figure equal to 1,480 minus the Day's consumed total shown beside it.
- `/s` **Given** the approval, **When** `GET /v1/targets` is read, **Then** one version holds `method` "mifflin_st_jeor", the input snapshot (age 34, height 160, weight 78, coefficient −161, multiplier 1.2, planned exercise 0, unrounded Target 1,477.98), `rmr` 1,449.0, `maintenance` 1,738.8, `intake_target` 1,480, the macro targets, `activity_mode` "fixed", `exercise_included` false, `review_date` 2026-10-15, `policy_version` v1 and `effective_from` Day 2026-10-01 (Africa/Cairo).
- `/s` **Given** the approval, **When** weight observations are read, **Then** one WeightObservation of 78 kg with source "profile" exists at the approval time (FRD §17).

#### eater-1.43 · Approve once, even if I tap twice or the network drops
Covers: FR-058; FRD §18 ("All mutation requests use an idempotency key"), §18.2 (bounded retries with the same command ID); AT-10 pattern · EX-13
As the eater, I end up with one Target however many times my approval is sent, so that my history has no phantom versions.
- `/s` **Given** `POST /v1/targets` delivered three times with one `command_id`, **When** processed, **Then** one version exists, and the second and third responses return that version.
- `/r` **Given** the network drops just after "Approve target" is tapped, **When** it returns, **Then** Onboarding · Review shows "Approved" once, and Progress → Target history lists one version from 1 Oct 2026 (eater-8.23).
- `/r` **Given** the API mock delays its answer by 5 s, **When** the eater waits, **Then** the button reads "Approving…" and is disabled, and after the answer exactly one version exists.

#### eater-1.44 · Leave halfway and nothing is set; my answers wait
Covers: FR-058 ("user approval"); WF-1 step "approve" · care.md group 4 ("If someone closes a half-filled form, is their work protected?") · EX-25
As the eater interrupted mid-flow, I come back to my answers and no Target exists until I approve, so that a closed app never sets a Target for me.
- `/r` **Given** Hala closes the app on Onboarding · Macros, **When** she reopens it, **Then** Today shows "No Target yet", and "Set a Target" resumes at Onboarding · Macros with her values filled in.
- `/r` **Given** she taps "Start over", **When** confirmed, **Then** her answers are cleared and Onboarding · Profile opens with only her age filled.
- `/s` **Given** the abandoned answers, **When** `GET /v1/targets` is read, **Then** no version exists.

#### eater-1.45 · No network: the Target step says why, and logging continues
Covers: WF-1 "Tracking works before a target exists"; FRD §8.3 (offline outbox); NFR-06 · care.md group 4 ("When a command cannot work right now, do we say why?") · E38 · EX-21
As the eater with no signal, I'm told the Target needs a connection while logging keeps working, so that I am never stuck.
- `/r` **Given** no network, **When** Hala taps "Set a Target", **Then** Onboarding · Profile opens and takes her answers, and Onboarding · Energy reads "Connect to calculate your Target. You can keep logging meanwhile." with "Back to Today"; "Approve target" is not shown.
- `/r` **Given** the same, **When** she taps a recent Unit on Today, **Then** the Entry appears marked Pending and the total changes (EX-21).
- `/r` **Given** the network returns, **When** she reopens "Set a Target", **Then** the flow continues at Onboarding · Energy with her answers.

#### eater-1.46 · Today shows my Target and the activity mode
Covers: WF-1 done-when ("Today shows the target and the activity mode"); FR-007 ("The chosen activity accounting mode shall be visible on the dashboard"); FRD §14 Today · EX-11, EX-35, EX-36
Shared: eater WF-7 journey (eater-7.16, 7.19).
As the eater, I see my Target, what is left and how exercise is treated at a glance, so that the number I read is the number I approved.
- `/r` **Given** Hala's approved Target 1,480 in Fixed mode, **When** Today opens on Day 2026-10-01, **Then** the headline shows the remaining kcal in large type, "Target 1,480" beneath it, and "Food Target: Fixed" as words, not a colour alone (EX-35).
- `/r` **Given** Sam in Activity-adjusted mode (eater-1.40) with 1,200 kcal consumed, **When** a 400 kcal workout imports, **Then** Today reads "Food Target: Activity-adjusted", "Target today 1,910 (1,710 + 200 Activity credit)" and remaining 710 (eater-7.19).
- `/r` **Given** VoiceOver, **When** the headline is focused, **Then** it reads the remaining kcal, the Target, the mode and the Day's date.

## 1K · Later changes

#### eater-1.47 · Change my Target later as a new version; past Days keep theirs
Covers: FR-058 (effective-dated versions); FR-071 ("Editing today's target must not rewrite last month's performance"); FRD §17 GoalPlanVersion ("Past days retain their effective plan") · E15, E16 · EX-14
Shared: eater WF-8 journey (eater-8.23); nutrition approver (approver-10.58).
As the eater changing my Target, I keep my history as it was, so that last week is read against last week's Target.
- `/r` **Given** Hala's Target 1,480 from Day 2026-10-01, **When** she approves 1,600 during Day 2026-10-10 from Settings → Goals, **Then** Progress → Target history lists "1,480 · 1–9 Oct 2026" and "1,600 · from 10 Oct 2026", and Progress shows Day 2026-10-03 against 1,480 and Day 2026-10-10 against 1,600.
- `/s` **Given** both versions, **When** `GET /v1/reports/day?day=2026-10-03` and `?day=2026-10-10` are read, **Then** their `target_version` is the 1,480 version and the 1,600 version.

#### eater-1.48 · A Policy change never changes my Target by itself
Covers: FRD §3.3 ("centrally versioned product policies"); FR-058; FR-071 · E16 · EX-14
Shared: eater + nutrition approver (approver-10.58).
As the eater, I am told calmly when the reviewed minimum changes and decide myself, so that my Target changes only when I approve.
- `/r` **Given** an eater's Target 1,250 approved on 20 Sep 2026 and Policy v2 (floor 1,300) In effect from 15 Oct 2026, **When** Today opens on 15 Oct, **Then** a calm notice reads "The reviewed minimum is now 1,300 kcal a day. Your Target stays 1,250 until you review it." with "Review my target", and the Target is unchanged.
- `/s` **Given** the same, **When** `GET /v1/reports/day?day=2026-10-14` and `?day=2026-10-15` are read, **Then** both show Target 1,250.
- `/r` **Given** "Review my target" is tapped, **When** Onboarding · Target opens, **Then** no Lose choice is below 1,300.

## 1L · Local trial, account and the move

#### eater-1.49 · Use Sips & Bytes as a local trial — no account
Covers: FR-001 ("a local trial"); FRD §3.2; WF-1 "Tracking works before a target exists" · E17, E28 · EX-03
Shared: eater + support agent (support-9.2: a local-trial eater has no support code).
As the eater trying the app, I log and make Units without an account, so that I can judge it before giving anything.
- `/r` **Given** Hala chose "Keep it on this iPhone for now" and built T1 over two Days, **When** she opens Today on each Day and then My Units, **Then** Day 2026-09-30 shows 7 Entries, Day 2026-10-01 shows 5, My Units lists the 3 Units and the Template «فطار», and the account line of Settings reads "Not signed in · Your diary is only on this iPhone" with "Create account".
- `/r` **Given** the local trial, **When** she taps "Set a Target" on Today, **Then** Onboarding · Account opens with "Your Target and profile are kept in your account" and "Not now" (conflict C-3).
- `/s` **Given** the local trial, **When** the API mock's record of T1 is read, **Then** no Entry, Unit, Template or Consent was sent; only public Food reference searches were (EA12).

#### eater-1.50 · Use AI in the trial through an anonymous session, with my Consent and a daily limit
Covers: FR-001 ("Cloud AI requires authenticated or anonymous-session access, consent, and quotas"); FR-076; blueprint §1.6 Registry ("per-user daily AI quotas"); FRD §16.5 · R2 · EX-22
Shared: eater + platform admin (admin-10.38, admin-10.41); eater WF-4 journey (eater-4.3).
As a trial eater, I analyse a photo after giving the AI Consent, without an email, and am told plainly when today's limit is used up, so that I can try the AI and still log.
- `/r` **Given** Hala in the local trial without the AI Consent, **When** she taps Meal on Capture & Plan, **Then** the AI Consent sheet of eater-4.3 shows with "Give consent" and "Not now"; "Not now" returns to Capture & Plan, and a recent Unit tapped on Today still adds an Entry.
- `/r` **Given** she taps "Give consent", **When** she takes a meal photo, **Then** an anonymous session starts without asking for an email, and Analysis review opens on the new Analysis.
- `/r` **Given** the anonymous-session limit of 3 photo analyses a day (admin-10.38, admin-10.41) is used, **When** she takes a 4th photo, **Then** Capture & Plan reads "Photo analysis is used up for today. It resets at 00:00." and still offers recent Units, Templates and typed amounts (EX-22).
- `/s` **Given** the 4th call, **When** `POST /v1/analyses` is made with the anonymous-session token, **Then** it returns 429 `RATE_LIMITED` with `resets_at` 2026-10-02T00:00+03:00 (conflict C-24 on where a session's Entries live).

#### eater-1.51 · Create an account and bring my trial diary across, once
Covers: FR-001 ("account creation and safe migration without duplicate units or meals"); interaction row consents ("diary processing"); FRD §8.3 (UUID per command); FR-042 (replay reproduces totals) · R4 (Sign in with Apple), R22 · E28
As a trial eater, I create an account and find every Unit, Template and Entry exactly once with the same Day totals, so that nothing is lost or doubled.
- `/r` **Given** T1 on Hala's iPhone, **When** she opens Onboarding · Account, **Then** it offers "Sign in with Apple" and "Continue with email", and shows the Diary processing Consent as its own switch, off, with "Needed to keep your diary in your account"; the create button stays disabled until that switch is on.
- `/r` **Given** she signs in with Apple and the move completes, **When** she opens My Units and Today, **Then** My Units lists the same 3 Units and 1 Template, Day 2026-09-30 shows 7 Entries and Day 2026-10-01 shows 5, and both Day totals equal those shown before the move.
- `/s` **Given** the move, **When** the account's Units, Templates and the day reports for both Days are read over the API, **Then** the counts are 3, 1 and 7 + 5 Entries, every Entry keeps its original entry id and command id, and replaying the ledger reproduces both totals.
- `/s` **Given** the anonymous session that held her Analyses, her age confirmation and her AI Consent, **When** the account is created, **Then** those records belong to the new account (EA11, admin-10.41) and the anonymous session no longer exists.

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
- `/r` **Given** Onboarding · Target with Lose selected, **When** shown, **Then** each gap reads "kcal below maintenance" with its number and is not coloured red.
- `/r` **Given** an eater who chose Gain, **When** shown, **Then** each gap reads in the same tone ("174 kcal above maintenance").

---

# Journey 9 — Privacy (WF-9)

Steps, in the map's order (WF-9: "consents, export, delete account"):
**9A** See and change Consents → **9B** Photos, voice and logs → **9C** Export → **9D** Delete account → **9E** Support code, language and access.

## 9A · Consents in Settings

#### eater-9.1 · See all my Consents in one place
Covers: WF-9 step "consents"; interaction row "give separate consents … per purpose, one-tap withdrawal"; FR-076 · R3, R22 · EX-07
Shared: eater + auditor (auditor-9.1, auditor-9.2), support agent (support-9.6).
As the eater, I see each Consent, whether it is Given or Withdrawn, since when and what it is for, so that I know exactly what I have agreed to.
- `/r` **Given** Hala signed in, **When** she opens Settings → Privacy, **Then** it first shows "Age 18+ confirmed · 1 Oct 2026", then one row each for Diary processing; Send photos, voice and text to Google's AI (Gemini); Health: read workouts; Health: read active energy; Health: read body mass; Health: write food; Microphone; Photos; and Optional research — each with its state (Given or Withdrawn, or "Not given" when no Consent exists), the date of the last change, a switch, and "Read what you agreed to" (or "Read the text" when never given). The Health rows add "Also in Settings → Activity".
- `/r` **Given** "Read what you agreed to" on the AI row, **When** opened, **Then** the exact wording she agreed to shows; if a newer wording exists, a line "A newer wording exists" offers "Read it".
- `/r` **Given** no Consent was ever given except Diary processing, **When** Settings → Privacy opens, **Then** the other rows read "Not given", never blank.

#### eater-9.2 · Withdraw the AI Consent in one tap, and see what it removed
Covers: interaction row ("one-tap withdrawal"); FR-076 ("Refusal must preserve unaffected functions"); AT-29 ("consent withdrawal propagate[s] to media, queues, private cached analysis, and exports"); FRD §7.2 · R3, R22 (withdrawing as easy as giving) · EX-22
Shared: eater + support agent (support-9.7), auditor (auditor-9.5); eater WF-4 journey (eater-4.6).
As the eater, I switch off sending to Google's AI with one tap and see what stopped and what was removed, so that my "no" works now and nothing else breaks.
- `/r` **Given** SE6 with the AI Consent Given and 2 Analyses Pending (captured offline, not sent), **When** she taps the AI switch in Settings → Privacy at 21:14 on 30 Sep (18:14 UTC), **Then** it turns off at once with no dialog; the row reads, in the catalogue's Arabic, the English key "Withdrawn · 30 Sep, 21:14 · Photo, voice and sentence analysis are off. Units, Templates and typed amounts still log."; and the row adds "2 photos that weren't sent were deleted", because each Pending Analysis is Discarded and its photo deleted from this iPhone (an Analysis that was Processing becomes Failed, as in eater-4.6).
- `/r` **Given** the AI Consent is Withdrawn, **When** she opens Capture & Plan, **Then** Meal, Label and voice read "Needs your Consent to send to Google's AI" with "Open Settings → Privacy", while the Unit editor and the recent Units on Today still log (AT-32 pattern).
- `/s` **Given** SE6's emulator seed (2 queued uploads, 3 cached private analyses, 4 raw photos, 1 audio clip, 1 prepared export), **When** the withdrawal runs, **Then** storage and queues hold none of them for SE6, its Consent-withdrawal Privacy job is Completed with each stage's time (support-9.7), and a later `POST /v1/analyses` from SE6 returns 403 `CONSENT_REQUIRED` while the Gemini adapter mock records 0 calls.
- `/r` **Given** the prepared export was deleted by the withdrawal, **When** SE6 opens Settings → Export, **Then** it reads "Your earlier export was deleted when you withdrew a Consent. Prepare a new one." with "Prepare my export" (AT-29, exports; auditor-9.14).

#### eater-9.3 · Give a Consent again, in Settings or at the moment I need it
Covers: FR-076; interaction row consents ("explicit, per purpose") · R22 · EX-26
Shared: eater + auditor (auditor-9.2); eater WF-4 journey (eater-4.3).
As the eater, I turn a Consent back on when I want a feature again, so that coming back is as easy as leaving.
- `/r` **Given** the AI Consent is Withdrawn, **When** Hala taps Meal on Capture & Plan, **Then** the AI Consent sheet shows the current wording with "Give consent" and "Not now" (eater-4.3), and after "Give consent" the camera opens.
- `/r` **Given** Settings → Privacy, **When** she turns the AI switch on there, **Then** the same wording sheet shows first, and the row reads "Given" only after "Give consent".
- `/s` **Given** either path, **When** `GET /v1/me/consents` is read, **Then** a new `given` record with its method ("in-app sheet · first use" or "Settings") follows the withdrawal, and the earlier records are unchanged.

#### eater-9.4 · Withdraw a Health Consent: imports and write-back stop, and I decide what stays in Health
Covers: interaction rows "Ledger → HealthKit … only after the Health consent" and "HealthKit → app → API"; FR-076; FR-062 (granular permission) · R7; P29, P30 · EX-27
Shared: eater WF-7 journey (eater-7.24), WF-3 journey (eater-3.39).
As the eater, I stop sharing with Apple Health one type at a time, so that each type stops on its own and I know what iOS still controls.
- `/r` **Given** "Health: write food" is Given (WF-3's "Write meals to Apple Health"), **When** Hala turns "Write meals to Apple Health" off in Settings → Activity, **Then** Settings → Activity reads "Write meals to Apple Health: Off", Settings → Privacy shows "Health: write food · Withdrawn", new Entries are no longer written to Health, and one choice appears: "Keep what's already in Health" (default) or "Remove what Sips & Bytes wrote to Health".
- `/r` **Given** "Remove what Sips & Bytes wrote to Health", **When** it finishes, **Then** the Health app on the simulator shows no dietary energy samples from Sips & Bytes, and Today's Entries are unchanged.
- `/r` **Given** "Health: read workouts" is withdrawn in Settings → Activity (eater-7.24), **When** Settings → Privacy opens, **Then** that row reads "Withdrawn" with the line "iOS keeps its own setting: Health app → Sharing → Apps → Sips & Bytes" (P30); what happens to workouts imported earlier is open (conflict C-11).
- `/s` **Given** "Health: read workouts" is Withdrawn, **When** the app comes to the foreground, **Then** no HealthKit workout query runs (UI-test spy).

#### eater-9.5 · Withdraw the Microphone or Photos Consent in one tap
Covers: interaction row "give separate consents (… mic; photos …) … one-tap withdrawal"; FR-076 ("Refusal must preserve unaffected functions"); AT-29 (queues) · R3, R22 · EX-26
Shared: eater WF-4 journey (eater-4.4, 4.5).
As the eater, I switch off the microphone or photos inside the app, so that the app stops listening or photographing at once, even before I change iPhone Settings.
- `/r` **Given** Microphone is Given and one voice Analysis is Pending (captured offline, not sent), **When** Hala taps the Microphone switch in Settings → Privacy at 10:12, **Then** the row reads "Withdrawn · 10:12", with the line "iPhone Settings still allows the microphone — turn it off there too if you like" and "Open iPhone Settings"; the Pending Analysis is Discarded and its recording deleted from this iPhone, never sent.
- `/r` **Given** Microphone is Withdrawn, **When** she taps the microphone on Today's quick-add, **Then** the field reads "Microphone is off — type instead" and the keyboard opens (eater-4.5), and a typed "3 cheese bites" reaches Analysis review.
- `/r` **Given** Photos is Withdrawn, **When** she opens Capture & Plan, **Then** it reads "Camera and photos are off in your settings" with "Open Settings → Privacy", and typing "3 cheese bites" in its "Add words" field reaches Analysis review as "cheese bite × 3", and "Log from My Units" → cheese bite → Log adds one Entry to Today.
- `/s` **Given** Microphone and Photos are Withdrawn, **When** the quick-add microphone and the Capture & Plan shutter are exercised in a UI test, **Then** no audio or camera session starts, and `GET /v1/me/consents` shows each withdrawal with method "Settings".

#### eater-9.6 · Withdraw while offline: it applies at once and is recorded once
Covers: interaction row ("one-tap withdrawal"); FR-076; FRD §8.3 (durable outbox, UUID per command); AT-29 · R22 · AT-10 pattern
Shared: eater + auditor (auditor-9.6); platform admin (admin-10.14).
As the eater without signal, I withdraw a Consent and it takes effect on my iPhone at once, so that "no" never waits for a network.
- `/r` **Given** SE1 has Optional research Given and no network, **When** she switches it off at 01:40 on 22 Sep (22:40 UTC on 21 Sep), **Then** the row reads, in the catalogue's Arabic, "Withdrawn · 22 Sep, 01:40 · reaches your account when you're online", and no photo is offered for research from then on.
- `/s` **Given** the iPhone reconnects and the command is delivered twice, **When** processed, **Then** one withdrawal record exists, with `made_at` 2026-09-21T22:40:00Z (device) and `received_at` 2026-09-22T06:05:30Z (server) (auditor-9.6).
- `/s` **Given** SE1 had 1 consented case in the regression set (emulator seed) and the withdrawal is received, **When** the regression set on Registry refreshes, **Then** the case is gone and `GET /v1/admin/regression-set/cases/{n}` returns 404 `NOT_FOUND` for it (admin-10.14).

#### eater-9.7 · Withdraw Diary processing: my diary goes back to this iPhone and the account's copy is deleted
Covers: interaction rows "give separate consents (diary processing …) … one-tap withdrawal" and "Eater → API: … delete account"; FR-076; FR-078; FR-001 (local use) · R22, R23 · EX-24
As the eater, I withdraw my Consent to keep my diary in the account and go on using the app on this iPhone, so that I can stop the server copy without losing my diary.
- `/r` **Given** Hala signed in, **When** she taps the Diary processing switch in Settings → Privacy, **Then** one sheet explains "Your diary stays on this iPhone. The copy in your account is deleted within 30 days and you'll be signed out." with "Withdraw and keep on this iPhone" and "Cancel" (conflict C-9).
- `/r` **Given** "Withdraw and keep on this iPhone", **When** done, **Then** the account line of Settings reads "Not signed in · Your diary is only on this iPhone", Today shows the same Entries and totals, and Settings → Privacy shows the deletion reference.
- `/s` **Given** the withdrawal, **When** the server is read, **Then** a deletion Privacy job exists (Requested, then Running) with reason "consent_withdrawn" and the same stages as eater-9.19.

#### eater-9.8 · Optional research stays off unless I choose it, and leaving it removes my cases
Covers: interaction row consents ("optional research"); FR-079 ("model-training use without a separate explicit opt-in"); FRD §19.2 ("Access to raw evidence for quality review requires explicit consent and restricted roles"); NFR-10 (consented test cases); AT-29 · R3 · E30
Shared: eater + platform admin (admin-10.14), auditor (auditor-9.8).
As the eater, I decide on my own whether my meal photos may become test cases for food recognition, so that they are never used that way by default and leave when I say so.
- `/r` **Given** any new eater, **When** Settings → Privacy opens, **Then** "Optional research" reads "Not given", and its text says that meal photos you approve, with their Analyses, would become test cases for food recognition, listed under a random number with no name or email, visible only to staff with a special permission.
- `/r` **Given** Optional research is never given, **When** the eater uses Capture & Plan, Analysis review and the Meal planner, **Then** none of them asks to turn it on in order to continue.
- `/s` **Given** an eater without the research Consent approves a meal Analysis, **When** the regression set on Registry refreshes, **Then** no case from that eater is offered (admin-10.14).
- `/r` **Given** an eater with 1 consented case withdraws Optional research in Settings → Privacy, **When** the server confirms, **Then** the row reads "Withdrawn · your photos were removed from the test cases", and the platform admin's regression set on Registry shows the consented count one lower (admin-10.14).

#### eater-9.9 · No feature, price or advert depends on my data
Covers: interaction row consents ("no feature paywalled behind consent"); FR-079 ("Do not sell health/nutrition data, use it for behavioral advertising"); FRD §19.3 (store rules on health data and advertising), §23.3 ("Export, correction, and account deletion must never be paywalled") · R1, R3, R8 · C53 · EX-32
As the eater, I use the whole app with no optional Consent Given and see no adverts, so that my diary is never the price.
- `/r` **Given** no optional Consent is Given (AI, Health, Microphone, Photos, Optional research), **When** the eater taps a recent Unit on Today, opens My Units, opens Progress, taps Settings → Export → "Prepare my export" and opens Settings → Privacy → Delete account, **Then** the Entry appears on Today, My Units lists their Units, Progress shows the week, the export reaches Completed, and the deletion screen of eater-9.18 opens; no screen shows an advert or asks for a Consent in order to continue.
- `/r` **Given** Settings → Privacy, **When** read, **Then** it states "We don't sell your data or use it for advertising. It isn't used to train AI models." in the eater's language.
- `/s` **Given** the iOS app's resolved package list in CI, **When** the dependency check runs, **Then** it holds no advertising or attribution SDK, and the API's only outbound adapters are Gemini, USDA FoodData Central, Firebase/Google Cloud and Apple's Sign in with Apple token revocation (blueprint §0 line 6; R4).

#### eater-9.10 · Read the privacy policy in my language
Covers: WF-9; FR-082 (privacy review and disclosures before release) · R5 (the policy is linked inside the app and lists uses, retention, deletion and how to revoke consent)
As the eater, I read the privacy policy inside the app in Arabic or English, so that I can check what happens to my data before and after I agree.
- `/r` **Given** Onboarding · Consents and Settings → Privacy, **When** "Privacy policy" is tapped on either, **Then** the same policy opens in the app's language, with sections on what is collected and why, how long it is kept (raw photos 30 days unless saved; voice 24 hours), how to withdraw a Consent, how to export, how to delete, and who processes the data (Google, and Apple for Sign in with Apple).
- `/r` **Given** no network, **When** the policy is opened, **Then** the copy bundled with the app version shows, with its date.

## 9B · Photos, voice and logs

#### eater-9.11 · My photos are cropped, stripped of details, encrypted on the way and kept private
Covers: FR-077 ("Crop to food where practical, strip EXIF, encrypt in transit/at rest, and prevent public access to private images. Use short-lived signed access where needed."); FR-038 · R8 · E6 · EX-31
Shared: eater WF-4 journey (eater-4.7).
As the eater photographing a family table, I know my photo leaves without its location, travels encrypted and stays private, so that the people around me are not exposed.
- `/r` **Given** a meal photo taken on the simulator with GPS metadata, **When** Analysis review shows it, **Then** it shows the food crop and the line "Location and camera details were removed before upload".
- `/s` **Given** the uploaded object in the Cloud Storage emulator, **When** read with an EXIF reader, **Then** it has no GPS, device or time tags.
- `/s` **Given** the object's path, **When** fetched over HTTPS without a signed URL, or with a signed URL after it expired, **Then** the response is 403.
- `/s` **Given** the iOS app's Info.plist in CI and the API's served configuration, **When** checked, **Then** the app has no App Transport Security exception (no `NSAllowsArbitraryLoads`), and a request to the API over plain `http://` is refused, never served — so every upload is encrypted in transit.
- `/s` **Given** the storage and database settings in the repository, **When** checked, **Then** none disables Google Cloud's encryption at rest; the check on a hosted project waits for the dropped ship rows (blueprint §0 line 10).

#### eater-9.12 · Raw photos go after 30 days and voice after 24 hours; my confirmed numbers stay
Covers: FR-078 ("keep raw scans up to 30 days unless saved; delete temporary audio within 24 hours after transcription. Confirmed records remain until deleted."); FRD §17 MeasurementEvidence ("Ephemeral photo deletion need not delete confirmed numeric data") · E30 · EX-31
Shared: eater + auditor (auditor-9.15).
As the eater, I see old photos disappear on time while the numbers I confirmed remain, so that media is never kept longer than promised.
- `/s` **Given** an Analysis photo from 2026-09-01 that was not saved to a Unit, **When** the hourly retention run passes 2026-10-01 (emulator clock; auditor-9.15), **Then** the photo object is gone, while the confirmed Entry, its quantities and its Evidence remain.
- `/r` **Given** that Entry, **When** opened from Day 2026-09-01 in Progress, **Then** its detail reads "Photo removed after 30 days · numbers kept".
- `/s` **Given** a voice recording transcribed at 08:00 on 2026-10-01, **When** 24 hours pass, **Then** the audio object is gone and the transcript stays with its Analysis.
- `/r` **Given** a photo the eater saved as Evidence for a Unit, **When** 30 days pass, **Then** the Unit editor still shows it, marked "Saved".

#### eater-9.13 · My profile, Target and Consent choices never reach diagnostic logs
Covers: FRD §19.2 ("Operational logs shall contain request IDs, timing, status, model version, cost, and validation codes, not … private diaries, audio, or sensitive profile values. Avoid copying prompts into crash reports."); FRD §17 UserProfile ("Does not store health preferences in analytics events") · EX-29
Shared: eater + platform admin (operations logs), auditor (the Audit trail keeps Consent events, auditor-9.2).
As the eater, I know my age, body measures, Target and privacy choices stay out of the logs engineers read, so that fixing a bug never means reading about me.
- `/r` **Given** the API served locally and Hala's Target flow run on the simulator, **When** the verifier reads the API's log stream (the running service's standard output, the Cloud Logging seam), **Then** every record has only the fields request id, route, status, duration, model version, cost and validation code — none named age, height, weight, rmr, maintenance, target, macro, safety, purpose or consent.
- `/r` **Given** Hala withdraws the AI Consent while the log stream is read, **When** the record for that request appears, **Then** it holds only the allowed fields of the line above (request id, route, status, duration, model version, cost, validation code) and no field naming the purpose or the action; the purpose and the action appear in the Audit trail (auditor-9.2), not in the log.
- `/s` **Given** a crash forced in the iOS debug build on Onboarding · Review, **When** the crash report (the Crashlytics seam, written to a local file) is read, **Then** it holds no profile value, Target, macro or Consent choice.
- `/m` **Given** the log formatter's allow-list, **When** a test adds any other field, **Then** the formatter test fails.

## 9C · Export

#### eater-9.14 · Export my data in the app, free, as a machine-readable file
Covers: WF-9 done-when ("export downloads entries, units, recipes, targets and consents"); interaction row "Eater → API: export … in-app"; FR-075; FRD §18 `POST /v1/privacy/export-or-delete`, §23.3 (never paywalled) · R31 · C53
Shared: eater + support agent (support-9.8), auditor (auditor-9.14).
As the eater, I ask for an export and save one file with everything I have recorded, so that I can keep or move my diary.
- `/r` **Given** Hala signed in, **When** she taps Settings → Export → "Prepare my export" at 18:20 on 1 Oct 2026, **Then** the screen reads "Running — your export is being prepared. You can leave this screen."; once done it reads "Completed · 1 Oct 2026, 18:26 · download until 8 Oct 2026, 18:26" with "Save to Files" and "Share", and the Settings button on Today shows "Data download ready".
- `/r` **Given** the saved .zip opened in Files, **When** listed, **Then** it holds entries.json and entries.csv (with each Entry's Corrections, Voids and Restores), units.json (every version), recipes.json, templates.json, targets.json (every Target version), activity.json, weight.json, consents.json (with the age confirmation), day_reports.csv and readme.txt explaining each file in the app's language (conflict C-10).
- `/s` **Given** the job, **When** `POST /v1/privacy/export-or-delete {kind: "export"}` and then `GET /v1/privacy/jobs/{id}` are read, **Then** the Privacy job moves Requested → Running → Completed, and the archive's record counts equal the API's counts for that account.
- `/r` **Given** an eater with no purchase or subscription, **When** the export is requested, **Then** no purchase, upgrade or Consent screen appears.

#### eater-9.15 · The export holds only my data, in a form machines and people can read
Covers: FR-075 ("machine-readable form"); NFR-07 · research.md §6 conflict 8 (numerals in exports) · E40, E41
As the eater, I get an export any tool can read and in which my Arabic food names are intact, so that it is useful outside the app.
- `/s` **Given** Hala's export (her display uses Arabic-Indic digits), **When** entries.csv is parsed, **Then** every number uses Western digits with a "." decimal, every time is ISO 8601 with its offset (for example 2026-10-01T08:40:00+03:00), and Unit names such as «قرصة جبنة» are intact UTF-8.
- `/s` **Given** two accounts (Hala and Sam) on the emulator, **When** Hala's export is built, **Then** it contains no record with Sam's user id, and Sam's token reading Hala's Privacy job gets 404 `NOT_FOUND` (NFR-07).
- `/r` **Given** entries.csv opened in Files' preview on the simulator, **When** viewed, **Then** Arabic names show right to left inside their cells and the header row uses fixed English field names.

#### eater-9.16 · An export that is running, Failed, out of date or offline says why and what next
Covers: FR-075; FRD §14 Settings mandatory state ("data download ready"), §18.2 (bounded retries) · care.md group 4 · EX-23
Shared: eater + support agent (support-9.8, support-9.9).
As the eater, I always know where my export stands, so that I never wonder whether it worked.
- `/r` **Given** no network, **When** "Prepare my export" is tapped, **Then** it reads "Connect to prepare your export", and nothing is queued.
- `/r` **Given** SE3's export `job_exp_4410` is Failed after 3 attempts, **When** Settings → Export opens, **Then** it reads "Failed — your export couldn't be prepared. Try again, or contact a Support agent with your support code." with "Try again"; after a Support agent retries it with the same id (support-9.9), the screen shows "Running" without a new request.
- `/r` **Given** SE7's `job_exp_31` (Completed 2026-09-20, download window over 2026-09-27), **When** Settings → Export opens on 2026-10-01, **Then** it reads "Download no longer available since 27 Sep — prepare a new export" (support-9.8), and the old file cannot be downloaded.
- `/s` **Given** "Prepare my export" is tapped twice quickly, **When** processed, **Then** one Privacy job exists.

#### eater-9.17 · Export a trial diary that lives only on this iPhone
Covers: FR-075; FR-001 (local trial); FRD §23.3 · R3
As a trial eater, I export my diary from the iPhone without an account, so that trying the app never locks my data in.
- `/r` **Given** T1 on Hala's iPhone with no account, **When** she taps Settings → Export, **Then** the export is built on the iPhone and offered to "Save to Files" with the same file set, and consents.json lists the age confirmation and the Consents made on the device.
- `/s` **Given** that export, **When** the API mock's record is read, **Then** no request was sent to build it.

## 9D · Delete account

#### eater-9.18 · Delete my account in the app, with one clear warning
Covers: WF-9 step "delete account"; interaction row "Eater → API: … delete account · in-app, no email or phone, ≤30 days"; FR-078; NFR-13 · R4, R23, R31 · EX-24
Shared: eater + support agent (support-9.10, support-9.13), auditor (auditor-9.10, auditor-9.11).
As the eater, I delete my account from Settings without writing to anyone, so that leaving is as easy as joining.
- `/r` **Given** SE2 signed in (English, Cairo), **When** she opens Settings → Privacy → Delete account, **Then** one screen says what will be deleted (diary, Units, Recipes, Templates, Targets, photos, voice, Consent choices) and that it completes within 30 days, offers "Export first", and has one destructive button "Delete account" and "Cancel"; no email, phone call or form is asked for.
- `/r` **Given** "Delete account" is tapped at 10:00 on 15 Sep 2026 (Cairo; 07:00 UTC), **When** the server confirms, **Then** the app shows "Account deletion requested. Reference DEL-26-0915-K3Q8. Completed by 15 Oct 2026 at the latest." with "Copy reference", signs out and clears the local database.
- `/s` **Given** the request, **When** `POST /v1/privacy/export-or-delete {kind: "delete"}` is processed, **Then** the Privacy job is Requested with `due_by` 2026-10-15 (30 days, NFR-13; support-9.10), and the account can no longer sign in or call a private endpoint (401 `UNAUTHENTICATED`).

#### eater-9.19 · Deletion reaches everything: media, queues, caches, exports, Grants, Google and Apple
Covers: AT-29; FR-078; FRD §17.2 ("Account deletion removes private records and media, including derived caches, subject to the disclosed backup lifecycle"); interaction row "Eater → API: … delete account · … processors told, Sign in with Apple revoked, completion record" · R4, R23
Shared: eater + support agent (support-9.10, support-10.18), auditor (auditor-9.11).
As the eater, I know my deletion reaches every copy, so that nothing of my diary is left behind.
- `/s` **Given** SE12 (signed in with Apple) with 2 photos, 1 audio file, 1 Pending Analysis, 1 queued job, 1 prepared export, private cached analyses and the Active Grant `grant_8e20`, **When** deletion runs on the emulators, **Then** Firestore and Cloud Storage hold nothing under SE12's account id outside the Audit trail, the job queue holds nothing for it, and the Grant is Withdrawn with reason `account_deletion` (support-10.18).
- `/s` **Given** the same, **When** the Sign in with Apple adapter mock and the processor-notice stage are read, **Then** the token revocation was called once, and the notice to Google is recorded with its time (R4).
- `/r` **Given** SE2's deletion Privacy job is Running on 2026-10-01, **When** it is opened in the admin console's Jobs section (support-9.10), **Then** each stage reads Completed, Running, Requested or Not applicable in words, with "Sign in with Apple token revoked — Not applicable (email sign-in)" and "backups expire — Running, by 2026-10-14".
- `/s` **Given** SE12's deletion was requested, **When** a Support agent requests a new Grant for that account, **Then** `POST /v1/grants` returns 404 `NOT_FOUND` and no Grant is created (support-10.18).

#### eater-9.20 · Deletion needs a connection and says so; Pending food goes with it
Covers: FR-078; FRD §8.3 (durable outbox) · care.md group 4 ("When a command cannot work right now, do we say why?") · EX-21
As the eater without signal, I'm told deletion has to reach the server, and once it does, nothing queued on my iPhone is sent afterwards.
- `/r` **Given** no network, **When** "Delete account" is tapped, **Then** the screen reads "Connect to delete your account — the request has to reach our servers", and nothing is deleted on the iPhone.
- `/r` **Given** 3 Pending Entries, **When** the eater opens Delete account online, **Then** the screen says "3 Pending Entries will be deleted too" before she confirms, and after deletion no Pending Entry remains on the iPhone.
- `/s` **Given** a confirmed deletion, **When** the app gains a session again for any reason, **Then** no command from the old outbox reaches `/v1/consumption`.

#### eater-9.21 · After deletion: a fresh start, and a reference I can keep
Covers: WF-9 done-when ("leaves a completion record without identifiers"); FR-078 · R4, R23
Shared: eater + support agent (support-9.12), auditor (auditor-9.11, auditor-9.13).
As the eater who left, I can start fresh like a new person and still prove my deletion with its reference, so that nothing links me to my old diary.
- `/r` **Given** the deletion is confirmed, **When** the app is reopened, **Then** Onboarding · Age shows, as on a fresh install.
- `/r` **Given** SE8's deletion Completed on 2026-09-18, **When** someone signs up with its former email `lina.synthetic@example.com` on 2026-10-01 and Today opens, **Then** Today is empty: no Units, Entries, Targets or Consents from the deleted account.
- `/r` **Given** SE8's completion record, **When** a Support agent enters `DEL-26-0820-M2V5` in Jobs → Look up an account (support-9.12), **Then** it reads "Deletion · Completed 2026-09-18 · requested 2026-08-20 · every stage Completed" and nothing else — no email, account id or device.

#### eater-9.22 · Delete a trial diary, and anything my anonymous session left
Covers: FR-001 (local trial, anonymous session); FR-078 · R4
Shared: eater + support agent (support-9.2, SE13).
As a trial eater, I erase everything on this iPhone and whatever my anonymous session left on the server, so that a trial leaves no trace.
- `/r` **Given** T1 with no account, **When** Hala taps Settings → Privacy → "Delete everything on this iPhone" and confirms once, **Then** Onboarding · Age opens and My Units is empty.
- `/s` **Given** an anonymous session that ran 2 Analyses, **When** the trial is deleted with a connection, **Then** its Analyses, photos, quota counter, age confirmation and Consent records are deleted on the server.
- `/r` **Given** the same without a connection, **When** confirmed, **Then** the screen says "Analyses from your trial will be deleted the next time you're online", the iPhone keeps only that request, and on the next launch with a connection the server records are deleted.

## 9E · Support code, language and access

#### eater-9.23 · Read my support code from Settings → Privacy
Covers: WF-9 (support side: support-9.2); FR-081 (support without diary access); FR-001 (anonymous session, local trial)
Shared: eater + support agent (support-9.2).
As the eater writing to a Support agent, I read them a short code from the app, so that they find my account without me sending my email or anything private.
- `/r` **Given** SE1 signed in on 2026-10-01, **When** she opens Settings → Privacy → Support code, **Then** it shows `SB-7KQ2-94XM` in Latin letters left to right inside the Arabic screen, with "Copy" and "Valid until 2 Oct, 09:12" (support-9.2).
- `/r` **Given** SE13's anonymous session, **When** Settings → Privacy → Support code opens, **Then** a support code shows; **given** SE13's local-trial install, **then** it reads "Your diary is only on this iPhone, so a Support agent can't see it" with "Create account".

#### eater-9.24 · Privacy in Arabic, with VoiceOver and the largest text
Covers: WF-9 (all steps); NFR-08; blueprint §0 line 5 (reach) · E36, E40, E41 · EX-15, EX-33, EX-36, EX-39
As an Arabic-speaking or VoiceOver eater, I manage Consents, export, deletion and Grants with nothing clipped or unread, so that privacy controls work for me too.
- `/r` **Given** Arabic and the largest text size on the smallest simulator, **When** Settings → Privacy, Settings → Export, Settings → Privacy → Delete account and Settings → Privacy → Grants open, **Then** nothing clips or overlaps, the layout is right to left, and dates use Arabic-Indic digits.
- `/r` **Given** VoiceOver, **When** a Consent switch is focused, **Then** it reads the purpose, "Given" or "Withdrawn" and the date of the last change, and after a one-tap withdrawal it announces "Withdrawn".
- `/r` **Given** the destructive "Delete account" button, **When** measured, **Then** it is at least 44 × 44 pt and is not the first item VoiceOver focuses on the screen (EX-15).

---

# Journey 10 — A support Grant, the eater's side (WF-10)

Steps, in the map's order (WF-10: "support requests a Grant → the eater approves or declines it in Settings → support reads within its time box → it expires, every read audited"):
**10A** A request arrives → **10B** Approve or decline → **10C** Inside the time box → **10D** The box ends.

Grant states follow vocabulary.md: Requested → Approved → Active → Expired · Ended (by the Support agent) · Withdrawn (by the eater); Requested → Declined · Unanswered. Approval starts the time box, so the app and the console show Active at once; Approved appears only in the Audit trail (support lens §0.1).

## 10A · A request arrives

#### eater-10.1 · Learn that a Support agent asked for access, without anyone around me noticing
Covers: interaction row "Support → Eater: request just-in-time diary access · Grant (requested) · the eater sees who asks, why and for how long, and approves or declines in Settings"; WF-10; FR-081 · E24 · EX-12
Shared: eater + support agent (support-10.6; support conflict K2).
As the eater, I see a quiet sign that a request is waiting, so that I decide when I am ready.
- `/r` **Given** `grant_31f0` became Requested at 10:05 UTC, **When** SE1 opens Today, **Then** the Settings button at the top of Today shows the badge "1", and Settings → Privacy shows the row "Grants · Requests from a Support agent to read your diary · 1 Requested"; no alert or sound occurs.
- `/r` **Given** SE1 had already allowed notifications for Sips & Bytes, **When** the request arrives, **Then** one notification reads "A Sips & Bytes Support agent asked to see part of your diary. Open Settings to decide." with no food or health detail; **given** notifications were never allowed, no permission prompt appears.

## 10B · Approve or decline

#### eater-10.2 · See who, why, what and for how long — and approve
Covers: interaction rows "Support → Eater … Grant (requested)" and "Support → eater diary … time box, read-only, every read audited, auto-expiry"; WF-10 done-when ("the eater approves it in Settings"); FR-081
Shared: eater + support agent (support-10.6, support-10.9), auditor (auditor-10.4, auditor-10.6).
As the eater, I read who wants access, why, to which Days and areas and for how long, and approve it, so that access exists only because I chose it.
- `/r` **Given** `grant_31f0` is Requested, **When** SE1 opens Settings → Privacy → Grants on the simulator, **Then** the request shows "Mona K. · Support agent", the reason "An Entry is missing or appears twice", "Entries and day reports, My Units · 28–30 Sep 2026", "1 hour from when you approve", case CASE-1182, "Never included: photos, voice, your Target and goal settings" (support-10.14), "The Support agent can read, not change. Every read is listed here.", and two equal-size buttons, "Approve" and "Decline" (conflict C-12).
- `/r` **Given** SE1 taps Approve at 13:20 Asia/Riyadh (10:20 UTC), **When** the server confirms, **Then** the request reads "Active · ends 14:20", and the admin console's Grants section shows `grant_31f0` as Active (support-10.6).
- `/r` **Given** SE1's app is in Arabic with Arabic-Indic digits, **When** the request shows, **Then** it is right to left, the reason reads «إدخال مفقود أو ظاهر مرتين», the Days read «٢٨–٣٠ سبتمبر ٢٠٢٦» and the end time «١٤:٢٠», and "Mona K." is an isolated left-to-right run.
- `/s` **Given** `POST /v1/grants/grant_31f0/approve` with SE1's own token, **When** processed, **Then** 200, the Grant passes Approved to Active in one transaction, and `expires_at` = `approved_at` + 1 h; with any staff token, 403 `FORBIDDEN` (support-10.9).

#### eater-10.3 · Decline without giving a reason
Covers: WF-10 done-when ("a declined Grant gives no access"); interaction row "Support → Eater … approves or declines in Settings"; FR-081
Shared: eater + support agent (support-10.7), auditor (auditor-10.7).
As the eater, I say no with one tap and no explanation, so that declining is as easy as approving.
- `/r` **Given** `grant_31f9` is Requested for SE1, **When** she taps Decline at 14:42 Asia/Riyadh (11:42 UTC), **Then** the request moves to history as "Declined · 1 Oct, 14:42", with no follow-up question and nothing asking her to reconsider.
- `/s` **Given** `grant_31f9` is Declined, **When** the Support agent reads Day 2026-09-29 under it, **Then** the API returns 403 `GRANT_NOT_ACTIVE` with state Declined (support-10.7).
- `/r` **Given** the Support agent sends a new request later, **When** it arrives, **Then** it appears as a separate Requested Grant, and `grant_31f9` stays Declined.

#### eater-10.4 · Do nothing, and the request closes by itself
Covers: interaction row "Support → Eater … Grant (requested)"; FR-081; WF-10 · research.md §6 conflict 6 ("make 'decline' the default when the eater does nothing, and set the request's own expiry")
Shared: eater + support agent (support-10.8), auditor (auditor-10.10).
As the eater who ignores a request, I am sure it gives no access and closes, so that silence is never taken as yes.
- `/r` **Given** SE10's `grant_40ab` was Requested on 27 Sep 2026 at 13:05 Cairo (10:05 UTC), **When** SE10 opens it on 28 Sep, **Then** it reads "If you don't answer, this request closes on 30 Sep at 13:05" (EA10).
- `/r` **Given** `grant_40ab` was never answered, **When** SE10 opens Settings → Privacy → Grants on 1 Oct 2026, **Then** history shows it as "Unanswered · no access was given", with no Approve button (support-10.8).
- `/s` **Given** a Requested Grant and the test clock moved 72 h ahead, **When** the eater's approve call arrives, **Then** the API returns 409 `GRANT_NOT_ACTIVE` with state Unanswered, and no access is created.

#### eater-10.5 · Answer a request only when online
Covers: interaction row "Support → Eater … approves or declines in Settings"; FR-081; FRD §8.3 (the outbox carries food commands)
Shared: eater + support agent (support-10.10).
As the eater without signal, I see the request but cannot answer until I am online, so that an approval never applies later by surprise.
- `/r` **Given** `grant_31f0` is Requested and SE1's simulator has no network, **When** she opens Settings → Privacy → Grants, **Then** the cached request shows with Approve and Decline disabled and the line "Connect to answer this request".
- `/m` **Given** the outbox, **When** a Grant answer is attempted offline, **Then** no outbox command is created.

#### eater-10.6 · Approve once, even if I tap twice
Covers: interaction row "Support → Eater … Grant approved or declined"; FR-081; FRD §18 (idempotency key) · AT-10 pattern
Shared: eater + auditor (auditor-10.6: one approve delivered three times gives one Approved event).
As the eater, I give one approval however many times it is sent, so that the record shows one decision.
- `/s` **Given** SE1's approve for `grant_31f0` delivered three times with one `command_id`, **When** processed, **Then** one `grant.approved` event exists, and the time box starts at the first approval.
- `/r` **Given** Approve is tapped twice quickly, **When** the server confirms, **Then** the request reads "Active · ends 14:20" once, and its history lists one approval.

## 10C · Inside the time box

#### eater-10.7 · See every read a Support agent made under my Grant
Covers: interaction row "Support → eater diary … every read audited"; WF-10 done-when ("every read shows in the auditor's trail"); FR-081, FR-082
Shared: eater + support agent (support-10.15), auditor (auditor-10.3; auditor conflict 13).
As the eater, I see each thing the Support agent looked at and when, so that I know exactly what was seen.
- `/r` **Given** Mona K. read Day 2026-09-29 at 10:24 UTC, Entry `en_9921` at 10:25 UTC and My Units at 10:27 UTC while `grant_31f0` was Active, **When** SE1 opens that Grant in Settings → Privacy → Grants, **Then** its history lists exactly three lines, in the catalogue's Arabic, whose English keys read "Mona K. read your Day for 29 Sep · 13:24", "Mona K. read an Entry from 29 Sep · 13:25" and "Mona K. read your Units · 13:27" (support-10.15; conflict C-14).
- `/s` **Given** the same, **When** `GET /v1/me/grants/grant_31f0/reads` is compared with the Audit trail filtered by that Grant, **Then** both list the same 3 reads at the same times.
- `/r` **Given** an Active Grant with no reads yet, **When** opened, **Then** it reads "No reads yet".

#### eater-10.8 · Withdraw access early
Covers: interaction row "Support → eater diary … time box … auto-expiry"; FR-081; AT-29 (withdrawal propagates); vocabulary.md ("Withdrawn (by the eater)")
Shared: eater + support agent (support-10.18), auditor (auditor-10.11).
As the eater, I withdraw an Active Grant whenever I want, so that my "stop" works at once.
- `/r` **Given** SE10's `grant_52a3` is Active from 15:05 Cairo (12:05 UTC) for 1 hour, **When** SE10 opens it, **Then** "Active · ends 16:05 · Withdraw access" is visible at the top of the request without scrolling.
- `/r` **Given** SE10 taps "Withdraw access" at 15:26 Cairo (12:26 UTC), **When** the server confirms, **Then** the request reads "Withdrawn · 15:26 · The Support agent can no longer read your diary", and the history keeps every read made before 15:26.
- `/s` **Given** `grant_52a3` is Withdrawn, **When** the Support agent's next read arrives, **Then** the API returns 403 `GRANT_NOT_ACTIVE` with state Withdrawn.

## 10D · The box ends

#### eater-10.9 · The Grant expires at the end of its time box
Covers: WF-10 ("→ it expires"; done-when "the Grant expires"); interaction row "Support → eater diary … time box … auto-expiry"; FR-081
Shared: eater + support agent (support-10.17), auditor (auditor-10.8).
As the eater, I see an approved Grant end by itself at the time I was promised, so that access never outlives what I agreed to.
- `/r` **Given** `grant_31f0`'s time box closed at 14:20 Asia/Riyadh (11:20 UTC), **When** SE1 opens Settings → Privacy → Grants at 14:21, **Then** the Grant reads "Expired · 14:20" with its three reads in its history, and no "Withdraw access" control remains (support-10.17).
- `/s` **Given** the Grant has expired, **When** the Support agent's read arrives at 11:20:01 UTC, **Then** the API returns 403 `GRANT_NOT_ACTIVE` with state Expired, judged by the server clock (support-10.17, auditor-10.8).
- `/r` **Given** the Grant has expired, **When** Today opens, **Then** the Settings button shows no badge for it.

#### eater-10.10 · A Grant the Support agent ended early
Covers: WF-10 (time box); FR-081; vocabulary.md ("Ended (by the support agent)")
Shared: eater + support agent (support-10.19), auditor (auditor-10.12).
As the eater, I see when a Support agent handed access back before the end, so that I know the door closed sooner than I allowed.
- `/r` **Given** SE10's `grant_52a7` was Active from 16:04 Cairo (13:04 UTC) and the Support agent ended it at 16:33 Cairo (13:33 UTC), **When** SE10 opens Settings → Privacy → Grants, **Then** the Grant reads "Ended by the Support agent · 16:33" with its history (conflict C-12 on support-10.19's wording), and no "Withdraw access" control remains.
- `/s` **Given** `grant_52a7` is Ended, **When** a read is sent, **Then** the API returns 403 `GRANT_NOT_ACTIVE` with state Ended.

---

## Stories shared with other personas

| story | shared with | why |
|---|---|---|
| eater-1.1, 1.2 | auditor (auditor-9.2, 9.9) | the age confirmation and the under-18 refusal are records the auditor reads |
| eater-1.3, 1.4, 1.5, 9.1, 9.2, 9.3, 9.6 | auditor (auditor-9.1–9.6) | the eater's Consent choices are the records the auditor proves |
| eater-9.2, 9.16 | support agent (support-9.7, 9.8, 9.9) | the Consent-withdrawal Privacy job and the export the Support agent explains |
| eater-1.17, 1.18, 1.20, 1.25, 1.28–1.31, 1.33, 1.40, 1.47, 1.48 | nutrition approver (approver-10.49–10.53, 10.57, 10.58, 10.68–10.70) | the Policy values the eater meets on the Target flow |
| eater-1.19, 1.21 | support agent (support-9.5, 10.14) | the mode and the answers never reach a support view |
| eater-1.49, 9.22, 9.23 | support agent (support-9.2) | support code, the local trial and the anonymous session |
| eater-1.50 | platform admin (admin-10.38, 10.41) | AI quotas for anonymous sessions |
| eater-9.6, 9.8 | platform admin (admin-10.14), auditor (auditor-9.6, 9.8) | consented regression cases and their removal |
| eater-9.12 | auditor (auditor-9.15) | retention runs |
| eater-9.13 | platform admin, auditor (auditor-9.2) | operational logs versus the Audit trail |
| eater-9.14 | support agent (support-9.8), auditor (auditor-9.14) | the export job |
| eater-9.18, 9.19, 9.21 | support agent (support-9.10, 9.12, 9.13, 10.18), auditor (auditor-9.10, 9.11, 9.13) | deletion, its stages and its completion record |
| eater-10.1–10.10 | support agent (support-10.6–10.10, 10.15, 10.17–10.19), auditor (auditor-10.3, 10.4, 10.6–10.8, 10.10–10.12) | the eater's side of WF-10 |
| eater-1.6, 1.7, 1.8, 1.9, 1.14, 1.15, 1.32, 1.39–1.41, 1.43, 1.46, 1.47, 1.50, 9.2–9.5, 9.11 | other eater journeys (eater-3.2, 3.39, 4.3–4.7, 7.1, 7.16, 7.18, 7.19, 7.24, 8.23; WF-2, WF-5) | the same screen seen from another workflow |

## Conflicts for the model phase

These are tensions with other personas or inside the model. They are for the model phase, never for the owner.

1. **C-1 · Floor scope (eater ↔ nutrition approver).** Does the 1,200 kcal floor bind only loss Targets, or every Target? Amal's maintenance is 1,162.8, so a Maintain Target at maintenance would sit below the floor. This lens proposes Maintain = 1,200 and no Lose (eater-1.30), following the done-when "a target not below the policy floor". The approver decides.
2. **C-2 · Own or clinician Targets below the floor, and in tracking-only mode (eater ↔ approver).** FR-004 allows a clinician-provided Target. This lens refuses any Target below the floor whatever its source (eater-1.31), and offers no own-Target entry in tracking-only mode (eater-1.19), because FRD §11.4 bars restrictive plans for excluded users. The model decides whether a clinician's Target may override either rule.
3. **C-3 · A Target in the local trial (eater ↔ model).** The proposal runs on the server (FRD §15.1, nutrition core), and profile data needs the Diary processing Consent. So this lens asks for an account before the Target flow (eater-1.49). The alternative is an on-device engine held to the same golden cases (eater-1.27).
4. **C-4 · Optional safety screen vs screening before restrictive plans (eater ↔ approver).** The map makes the screen optional, and FR-008 forbids forcing sensitive details. R35 calls for "baseline screening, including … disordered eating" and R38 for assessment before restrictive plans. The model decides whether skipping should change anything, such as a smaller default deficit.
5. **C-5 · Safety-screen minimisation vs metrics (eater ↔ approver, auditor).** This lens keeps only the mode (EA3, eater-1.21). Any wish for counts by trigger (pregnancy against SCOFF) would need the trigger stored, which this lens refuses.
6. **C-6 · Activity rows in Policy (eater ↔ approver).** Resolved by approver-10.69, which now holds the multiplier and the Activity-adjusted credit and cap. Still open: any activity level other than × 1.2.
7. **C-7 · Default macro split and review interval (eater ↔ approver).** Resolved by approver-10.70, which holds EA5 (30/40/30) and EA6 (14 days) as Policy rows.
8. **C-8 · Target rounding (eater ↔ model).** This lens has the eater approve the Target as shown, to the nearest 10 kcal (FRD §11.3), and keeps the unrounded value in the snapshot (EA4). FRD §10.2 says "round only the final displayed fields". The model fixes which value reports use.
9. **C-9 · Withdrawing Diary processing (eater ↔ auditor, model).** The interaction row promises one-tap withdrawal (R22). Here the withdrawal deletes the account's copy, so this lens adds one confirmation (eater-9.7), following care.md group 4: "warn only before loss that is both unexpected and permanent". The model confirms or removes it.
10. **C-10 · Export contents (eater ↔ support agent, auditor).** The eater's export adds Templates, Activity and weight (eater-9.14). support-9.8 and auditor-9.14 list Entries, Units, Recipes, Targets, Consents and Reports. One list is needed. Also open: whether photos saved as Unit Evidence travel in the export (this lens: no media). Settled with the other lenses: a Consent withdrawal deletes a prepared export (eater-9.2, support-9.7, auditor-9.14).
11. **C-11 · Imported Activity after a Health Consent withdrawal (eater ↔ auditor; eater WF-7 conflict 8).** Whether workouts imported before a withdrawal are kept, hidden or deleted is open; eater-9.4 and eater-7.24 both leave it open. R22's "cease processing" may require removal or an offer to remove.
12. **C-12 · Grant verbs and wording (eater ↔ support agent) — settled.** The map says the eater "approves or declines" a Grant; vocabulary.md names the eater's early stop **Withdrawn** and the Support agent's **Ended**. This file and the support lens now use the same buttons and copy: "Approve", "Decline", "Withdraw access", "Mona K. · Support agent", "The Support agent can read, not change. Every read is listed here.", "Active · ends 14:20", "Withdrawn · 15:26 · The Support agent can no longer read your diary" and "Ended by the Support agent · 16:33" (eater-10.2, 10.8, 10.10; support-10.6, 10.18, 10.19). Kept here only so the string catalogue holds this one wording.
13. **C-13 · The Grant label on the eater's screen (support K6).** This lens keeps the map's word "Grants", with the subtitle "Requests from a Support agent to read your diary" (eater-10.1).
14. **C-14 · What the eater's access history lists (support K1, auditor conflict 13).** This lens lists Grant reads only (eater-10.7). Metadata look-ups, which show no diary, appear to the auditor but not to the eater. The support and auditor fixtures also give `grant_31f0` different read times (support 10:24, 10:25, 10:27 UTC; auditor-10.3 10:24, 10:26:30, 10:31); this lens follows the support lens.
15. **C-15 · How the eater learns of a request (support K2).** This lens shows a Settings badge always, and a notification only if notifications were already allowed; a Grant request never triggers the permission prompt (eater-10.1). With a 72 h window (EA10), a request may close unseen. That is the safe failure.
16. **C-16 · Public Food search in the local trial (eater ↔ platform admin).** EA12 lets a trial reach the reference search with App Check only. Per-user quotas (NFR-12) then have no user to count against.
17. **C-17 · Duplicate Units when a trial joins an account (eater ↔ model).** Which version becomes the default for new logs, and whether "Keep both" leaves two Units or one Unit with two versions (eater-1.53).
18. **C-18 · A self-declared age gate (eater ↔ auditor).** R16 bars services "likely to be accessed by" under-18s, and the store's 9+ rating (R13) does not keep them out. Whether a self-declared age (eater-1.1, 1.2) is enough is for counsel. The age gate now sends the age alone (no identifier) so the auditor can count refusals (auditor-9.9).
19. **C-19 · Places that vocabulary.md does not name (eater ↔ support agent, vocabulary.md).** vocabulary.md lists Settings → Goals, Food rules, Activity, Units & language, Privacy and Export. This lens also needs, and marks *(proposed)*: the eleven Onboarding · … screens; the account line at the top of Settings (FRD §14 lists "Account"; vocabulary.md has no Account section); and three parts of Settings → Privacy — Grants, Support code and Delete account — which the support lens uses with the same names (support-9.2, 9.13, 10.6). Progress → Target history comes from the eater's WF-8 journey (eater-8.23). A dated delta should add them.
20. **C-20 · Resolved by delta D3** (2026-10-01): a Consent purpose the eater has not decided is in the state Not given (vocabulary.md).
21. **C-21 · Microphone and Photos: an app Consent and an iPhone permission (eater ↔ auditor).** The eater's WF-4 journey asks an in-app sheet ("Give consent" / "Not now") before the iPhone's own camera or microphone prompt (eater-4.4, 4.5), and this file follows it (eater-1.6). auditor-9.1 shows the two purposes with no text version. This lens withdraws the app Consent in one tap and points to iPhone Settings for the permission (eater-9.5). The two sheets' text versions need an owner and a version id.
22. **C-22 · The deficit cap and own Targets (eater ↔ approver).** The cap (the smaller of 15 % and 500 kcal; R33's 500–750) binds proposals (approver-10.68: "every proposal an eater sees stays inside the reviewed range"). This lens accepts a Target the eater types, or a clinician's, beyond the cap but not below the floor, shows the gap with a note, and stores `over_deficit_cap` (eater-1.32). FRD §11.3's own example (a 20 % deficit, 465 kcal) is beyond the 15 % cap. The approver decides whether own Targets beyond the cap need anything more.
23. **C-23 · Where the tracking-only guidance shows (eater ↔ approver).** approver-10.53 previews the guidance "as the eater sees it on Today". This lens shows it on Onboarding · Safety screen and in Settings → Goals, and keeps Today silent about the mode so that people near the eater learn nothing (E24, eater-1.19). The approver preview should show those two places.
24. **C-24 · Where an anonymous session's Entries live (eater ↔ platform admin).** admin-10.41 confirms Entries on the server under an anonymous-session token. This lens keeps trial Entries on the iPhone until the Diary processing Consent is Given at account creation (eater-1.49, 1.51), because diary data is sensitive and needs that explicit Consent (R21, R22). The model decides.
25. **C-25 · Raw evidence for staff (auditor ↔ platform admin, touching eater-9.8).** auditor-9.8 says no staff role may open an eater's meal photo; admin-10.14 lets a custom role with "View consented evaluation cases" see consented cases. This lens's research text says such photos are "visible only to staff with a special permission", following admin-10.14. The two lenses must agree.
26. **C-26 · Counsel's wording for the AI Consent (eater ↔ auditor).** eater-1.4 states the processing location as "outside Egypt and Saudi Arabia", the fact blueprint §1.7 records. Counsel may change the wording before launch (blueprint §1.7, residency open question); the new text would become `c-ai-5`.

Two of research.md §6's conflicts are answered here as proposals: conflict 6 (an unanswered Grant closes with no access, eater-10.4) and conflict 8 (exports use Western digits and ISO dates, eater-9.15).

## Coverage — every FR, AT and FRD section in the dispatch

| line | stories |
|---|---|
| FR-001 local trial, account creation, migration without duplicates; cloud AI needs a session, consent and quotas | 1.7, 1.49, 1.50, 1.51, 1.52, 1.53, 9.17, 9.22, 9.23 |
| FR-002 age, height, weight, units, time zone, goal; the coefficient only with an explanation | 1.1, 1.10, 1.11, 1.12, 1.13, 1.14 (goal: 1.28) |
| FR-003 lose, maintain or gain; resting energy, maintenance, Target, assumptions, review date before approval | 1.28, 1.29, 1.42 |
| FR-004 own or clinician Target, or measured resting value, with source and effective date | 1.24, 1.26, 1.32 |
| FR-005 macros by % or grams; locks; incompatible locks resolved visibly | 1.20, 1.33, 1.34, 1.36, 1.37 |
| FR-006 reject negative values and invalid units; normalise or edit, never save contradictions | 1.11, 1.35, 1.37, 1.38 |
| FR-007 ask whether exercise is included; mode visible on Today | 1.26, 1.39, 1.40, 1.46 |
| FR-008 exclusions; optional safety screen; tracking without a prescription | 1.7, 1.8, 1.15, 1.16, 1.17, 1.18, 1.19, 1.21, 1.22 |
| FR-056 method and assumptions shown; calculated in code | 1.23, 1.27 |
| FR-057 maintenance from the reviewed activity policy or a manual value; exercise-included stored | 1.25, 1.26, 1.39 |
| FR-058 proposals with explicit deficit or surplus; approval; effective-dated versions | 1.28, 1.30, 1.32, 1.42, 1.43, 1.44, 1.47, 1.48 |
| FR-059 uncertainty note; no fixed weekly change | 1.29 |
| FR-075 machine-readable export | 9.14, 9.15, 9.16, 9.17 |
| FR-076 separate Consents; refusal keeps other functions | 1.3, 1.4, 1.5, 1.6, 1.14, 1.41, 1.50, 9.1–9.8 |
| FR-077 crop, strip EXIF, encrypt in transit and at rest, private, short-lived access | 9.11 (at rest: repository check now, hosted check with the ship rows) |
| FR-078 in-app export and deletion; raw scans 30 days; audio 24 h | 9.7, 9.12, 9.18, 9.19, 9.20, 9.21, 9.22 |
| FR-079 no sale, no behavioural ads, no training without an opt-in | 1.4, 9.8, 9.9 |
| FR-080 role-based console (eater stories that observe it) | 9.19 (Jobs), 9.21 (Jobs → Look up an account), 10.2 (Grants) — the console itself belongs to the staff lenses |
| FR-081 support privileges separate; just-in-time diary access with an audit trail | 9.23, 10.1–10.10 |
| FR-082 privacy review, disclosures and retention verification before release | 9.10, 9.12 |
| AT-09 46/32/24 → 102 and the normalised alternative; nothing active without approval | 1.35 |
| AT-29 deletion and withdrawal reach media, queues, private cached analysis and exports | 9.2 (media, queues, cached analysis, exports), 9.5 (queues), 9.6 and 9.8 (research cases), 9.19 (deletion), 10.8 |
| FRD §3.1 normalisation example | 1.35 |
| FRD §3.2 progressive onboarding; explain why each required input matters; separate choices; declining Health never blocks logging | 1.1, 1.6, 1.7, 1.8, 1.9, 1.10, 1.13, 1.15, 1.41, 1.49 |
| FRD §3.3 versioned Policy; 0 / 15 / 10 % defaults with selectable conservative ranges; tracking-only | 1.17, 1.18, 1.28, 1.30, 1.48 |
| FRD §11.1 Mifflin–St Jeor; measured override; "BMR" wording | 1.12, 1.23, 1.24 |
| FRD §11.2 planning model (FR-056–FR-059; FR-060/061 are P1 and only the 14-day window is borrowed, EA6) | as the FR rows above |
| FRD §11.3 worked example (1,779 → 2,334.8; 1,870 includes the exercise) | 1.25, 1.32, 1.39, 1.40 |
| FRD §11.4 safety behaviour; neutral words | 1.17, 1.18, 1.19, 1.31, 1.56 |
| FRD §19.1 provider data governance (no promise of zero retention) | 1.4 |
| FRD §19.2 application logging (no profile values, diaries or audio in logs; raw evidence only with consent) | 1.21, 9.8, 9.13 |
| FRD §19.3 store and safety rules (health data not for advertising; general wellness; safe response to dangerous restriction) | 1.17, 1.31, 1.56, 9.9 |
| WF-1 done-when (English or Arabic; ±10 % note; maintenance; Target not below the floor; macro grams and assumptions; approve; Today shows Target and mode; pregnancy → tracking-only; under 18 → no account) | 1.2, 1.18, 1.23, 1.25, 1.30, 1.33, 1.42, 1.46, 1.54 |
| WF-9 done-when (export of entries, units, recipes, targets and consents; deletion within the window, completion record without identifiers) | 9.14, 9.18, 9.19, 9.21 |
| WF-10 done-when, eater side (approve in Settings; reads in the box; expiry; reads in the trail; a decline gives no access) | 10.2 (approve), 10.7 (reads), 10.9 (expiry), 10.3 (decline), 10.4, 10.8, 10.10 (other ends) |
| Other lines cited | AT-10 (1.5, 1.43, 1.52, 9.6, 10.6) · AT-13 (1.9) · AT-16 (1.7) · AT-23 (1.39) · AT-32 (1.27, 9.2) · FR-071 (1.22, 1.47, 1.48) · NFR-06 (1.52) · NFR-07 (9.15) · NFR-08 (1.55, 9.24) · NFR-13 (9.18) |

**Counts:** 90 stories (56 in journey 1, 24 in journey 9, 10 in journey 10) · 296 acceptance lines (`/m` 23 · `/s` 79 · `/r` 194).

## Lens verdict (2026-10-01)

**fail** — 22 defects. Checked: map §3–§5 for WF-1, WF-9 and the eater's side of WF-10; FRD FR-001…FR-008, FR-056…FR-059, FR-075…FR-079, §3, §11, §19, AT-09, AT-29 and every AT the file claims; `vocabulary.md` (D2); research.md ids and the cycle-1 ids against both refuters (no refuted or doubtful finding is cited; every C/F/P/R id used stands); every cross-lens story id against the current admin, approver, auditor and support files; the fixture arithmetic (all resting-energy, maintenance, Target, macro, kJ, lb and date figures recompute exactly); the counts line (86 stories, 277 lines, `/m` 21 · `/s` 73 · `/r` 183 — true); every story has a `/r` line.

### Complete

1. **Missing step — the Grant expires (WF-10 "→ it expires"; done-when "the Grant expires").** No eater line shows an Active Grant reaching Expired at the end of its time box (e.g. Settings → Privacy → Grants reading "Expired · 14:20" and the Support agent's next read refused), nor a Grant Ended by the Support agent. Only Withdrawn (9.26), Declined (9.23) and Unanswered (9.24) are shown; 9.22 stops at "Active · ends 14:20".
2. **Missing step — FRD §3.3 "with selectable conservative ranges".** eater-1.28 offers only the fixed defaults ("Target 1,480 kcal a day · 261 kcal below maintenance (15 %)"). approver-10.48 and approver-10.68 (shared with the Eater) set loss choices 5 / 10 / 15 % and gain choices 5 / 10 %, and approver-10.68 expects the eater to be "offered" them; the Policy v1 fixture says "As the approver lens reads it (approver-10.48)" yet leaves the choices out.
3. **Missing step — FRD §3.2 "The application shall explain why each required input matters."** eater-1.1 ("one field 'Your age' … and nothing else is asked"), 1.10 (height, weight) and 1.13 (region, time zone) have no line that shows why the input is needed; only the equation version (1.12), the safety screen (1.16) and permissions (1.6, 1.14) explain themselves.
4. **Missing step — FR-077 "encrypt in transit/at rest".** eater-9.10 quotes it in Covers but its lines check only the crop, EXIF stripping and signed access; nothing checks encryption (e.g. no plain-HTTP upload path, storage encryption on).
5. **Missing step — withdrawing the Microphone and Photos Consents (interaction row "each Health type; mic; photos … one-tap withdrawal").** eater-9.1 gives both rows a switch, but no story says what withdrawing either stops, or what iOS keeps, as eater-9.4 does for Health.
6. **AT-29 — Optional research withdrawal does not propagate.** eater-9.7 says research keeps "which photos and labels would be kept"; eater-9.5 withdraws it with "nothing else changes", and no line deletes or excludes the research-kept photos and labels.
7. **AT-29 exports, eater-9.2.** "Given a Completed export of hers, When it is scanned after the withdrawal, Then it holds no photo, audio or transcript" can never fail, since this lens puts no media in an export (C-10). support-9.7 ("1 prepared export … storage and queues hold none of them") and auditor-9.14 ("file deleted … (AI Consent withdrawn)") delete the prepared export on withdrawal; the contradiction is not in Conflicts.
8. **Rule not covered — deficit cap on own Targets (interaction row "deficit cap within AHA's 500–750 kcal"; Policy v1 "the smaller of 15 % and 500 kcal").** eater-1.32 accepts "Target 1,870 … 465 kcal below maintenance (19.9 %)" with no cap message. No rule or conflict says whether the cap binds own or clinician Targets; C-2 covers only the floor.
9. **FRD §19.2 (logs).** Only the safety answers are checked against logs and crash reports (eater-1.21). No line checks the WF-1 profile values ("sensitive profile values": age, height, weight, Target) or Consent choices.
10. **The coverage table is not true.** The WF-10 row claims "expiry" for 9.21–9.28 (defect 1). The §3.2, §3.3 and FR-077 rows claim coverage the stories lack (defects 2–4). The table has no row for §19.1–§19.3 or FR-080–FR-082, though §19 is in the dispatch; FR-081 appears only under "Other lines cited".

### Traced

11. **Stale cross-lens ids (the other lenses were renumbered).**
    - eater-1.50 and the shared table cite admin-10.35 and admin-10.36 for "the anonymous-session limit of 3 photo analyses a day". Those ids are now "Turn the kill switch off again" and "The switch works at phone width"; quotas are admin-10.38 and admin-10.41.
    - The support ids are one off:
      - eater-9.12, C-10 and C-19 cite support-9.7, now "See what a Consent withdrawal removed"; the export is support-9.8.
      - eater-9.14 cites support-9.8 for the re-queue; that is support-9.9.
      - eater-9.16 and 9.17 cite support-9.9 for deletion; that is support-9.10.
      - eater-9.19 cites support-9.11 for the reference look-up; that is support-9.12.
    - The auditor ids are wrong too:
      - eater-9.11 cites auditor-9.14 for "retention runs"; that id is the export job, and retention runs are auditor-9.15.
      - eater-9.12 and C-10 cite auditor-9.13 for the export; that id is "A deleted account in the Audit trail".
      - eater-9.16 cites auditor-9.9, which is the age gate; deletion is auditor-9.10 and 9.11.
    - C-19's "support-9.7 says Settings → Privacy → Export" is stale: support-9.8 now says "Open Settings → Export".
12. **Fixtures — `grant_40aa` "reused" is not the support lens's record.** The support fixture is E10 (`acct_f1e0c3`): "Requested 2026-09-27 10:05 UTC → Unanswered 2026-09-30". Here it is E1: "requested 2026-10-01 10:05 UTC", at the same moment as `grant_31f0`. The support lens refuses a second request while one is waiting ("Mona K. has a request waiting for this eater … no form opens"). Not in Conflicts.
13. **eater-1.1 and 1.2 against auditor-9.9 (the same WF-1 step).** Neither story carries a Shared line for auditor-9.9, and they contradict it. eater-1.1 returns "403 `AGE_REQUIREMENT`" where auditor-9.9 returns 422. eater-1.2 has "zero requests were sent", where auditor-9.9 expects the API to refuse age 17 and write one `age.refused` event. auditor-9.9 also shows an "Age 18+ confirmed · age-1 · onboarding age question" record; here the age confirmation gets no version, time or method, unlike the Consents in the same interaction row. Not in Conflicts.

### Observable

14. **Then clauses that only say "works", or that cannot be observed.**
    - "works" with no data named: eater-1.19 "each works as for a standard eater"; 1.49 "everything works"; 9.8 "each works" (Settings → Export, Delete account); 1.50 "the Unit mode still works"; 1.41 "logging works".
    - Vague: eater-1.7 "within one thumb's reach".
    - Not observable in the product: eater-1.4 "in the wording counsel approves".
    - No screen and no text named: eater-1.17 "the next screen shows the approver's guidance".
15. **Two lines contradict each other.** eater-9.8 `/s`: "the API has no outbound adapter beyond Gemini, USDA FoodData Central and Firebase/Google Cloud". eater-9.17 `/s` requires "the Sign in with Apple adapter mock … token revocation was called once". One of the two must fail unless revocation is named as going through Firebase Auth.

### Vocabulary

16. **Place names not in the vocabulary, not marked (proposed) and not in Conflicts.**
    - The eleven "Onboarding · Age … Onboarding · Review" screens ("named here; the map names none").
    - "Settings → Goals → History".
    - C-19 lists only the account line, the Support code and Export.
17. **State names not in `vocabulary.md`.**
    - Consent "Not given" (eater-1.3, 9.1, 9.7). The Consent states are Given · Withdrawn.
    - An export that "has expired" (eater-9.14). The Privacy job states are Requested · Running · Completed · Failed, and support-9.8 says "download no longer available" for the same thing.
    - Neither is in Conflicts.
18. **Two names for Pending.** eater-9.18 shows "3 entries not yet synced will be deleted too" for Entries that its own Given calls "3 Pending Entries"; eater-1.45 shows "marked Pending". eater-9.5 also uses "not yet synced".
19. **Two names for the activity mode.**
    - This file shows "Fixed target" and "Activity-adjusted target" on Today: eater-1.46 "Target 1,480 · Fixed target".
    - The map says "in fixed mode" (§5 WF-7), and the eater's WF-7 journey uses "Fixed mode" and "Activity-adjusted mode" (eater-7.16).
20. **Camera or Photos.**
    - eater-1.3 says "Health, camera, microphone and photos are asked the first time you use them", as four separate things.
    - eater-1.6 asks for "the Photos Consent" before "the iOS camera prompt".
    - eater-9.1 lists no Camera row.
21. **E1 and E2 mean two things.** The file says it cites research "findings E1–E44", then reuses "E1 `acct_9c41e2`" and "E2 `acct_51ab07`" as account fixtures. eater-1.9's "· E2 ·" is the research finding; eater-9.16's "E2 signed in" is the account.

### Ids

22. **WF-10 stories carry journey 9.** eater-9.21–9.29 trace to WF-10 ("WF-10 done-when", "interaction row 'Support → Eater …'"), but they are numbered `eater-9.n`. The rule is journey = WF number, and the support and auditor lenses number the same steps support-10.x and auditor-10.x.

## Fix round 1 (2026-10-01)

Every defect was fixed at its root; the verdict above is kept as written. Each changed line was re-read against `way/vocabulary.md` (D2) and against the shared stories it touches in `support.md`, `auditor.md`, `approver.md`, `admin.md` and the eater's other journey files (`wf2-wf4.md`, `wf3-wf6.md`, `wf5-wf7-wf8.md`). Counts are now 90 stories and 296 acceptance lines (`/m` 23 · `/s` 79 · `/r` 194), and every story still has a `/r` line.

**Renumbering (defect 22, plus the new stories).** Grant stories moved to journey 10, and the WF-9 stories were renumbered so that file order is id order. Old → new:
- 9.1–9.4 unchanged.
- New 9.5 (Microphone and Photos).
- 9.5 → 9.6; 9.6 → 9.7; 9.7 → 9.8; 9.8 → 9.9; 9.9 → 9.10; 9.10 → 9.11; 9.11 → 9.12.
- New 9.13 (logs).
- 9.12 → 9.14; 9.13 → 9.15; 9.14 → 9.16; 9.15 → 9.17; 9.16 → 9.18; 9.17 → 9.19; 9.18 → 9.20; 9.19 → 9.21; 9.20 → 9.22.
- 9.29 → 9.23; 9.30 → 9.24.
- 9.21 → 10.1; 9.22 → 10.2; 9.23 → 10.3; 9.24 → 10.4; 9.27 → 10.5; 9.28 → 10.6; 9.25 → 10.7; 9.26 → 10.8.
- New 10.9 (Expired) and 10.10 (Ended).

Every reference inside the file uses the new ids.

1. **Grant expiry and Ended.** Added eater-10.9: at 14:21 Settings → Privacy → Grants reads "Expired · 14:20" with its reads, and the read at 11:20:01 UTC gets 403 `GRANT_NOT_ACTIVE` with state Expired (support-10.17, auditor-10.8). Added eater-10.10: SE10's `grant_52a7` reads "Ended by the Support agent · 16:33", and a later read gets `GRANT_NOT_ACTIVE` with state Ended (support-10.19, auditor-10.12).
2. **Conservative ranges (FRD §3.3).** eater-1.28 now offers Lose 5 % · 10 % · 15 % (15 % preselected) and Gain 5 % · 10 % (10 % preselected), each with its gap (approver-10.68). Hala's figures are in the fixtures table. eater-1.30 shows Huda's three choices, with the floor applied to 15 %. The Policy v1 fixture lists the choices.
3. **"Explain why each required input matters" (FRD §3.2).** Added a visible "Why we ask" line, as an acceptance line, to eater-1.1 (age), 1.10 (age, height, weight) and 1.13 (time zone and region). Equation version (1.12), exclusions (1.15), safety screen (1.16) and permissions (1.6, 1.14) already explained themselves.
4. **FR-077 encryption.** eater-9.11 adds two lines. The first checks encryption in transit: no App Transport Security exception in Info.plist, and plain `http://` refused by the API. The second checks encryption at rest: no repository setting disables it. The hosted check is honestly deferred with the dropped ship rows. Signed access now names HTTPS.
5. **Microphone and Photos withdrawal.** New eater-9.5, aligned with eater-4.4 and 4.5. A one-tap switch in Settings → Privacy withdraws either Consent, and a voice recording waiting offline is deleted. After that, Today's microphone reads "Microphone is off — type instead" and Capture & Plan reads "Camera and photos are off in your settings", with words and My Units still working. The row also notes that iPhone Settings keeps its own switch.
6. **Optional research withdrawal (AT-29).** eater-9.6 (old 9.5) no longer says "nothing else changes". The consented case is removed from the regression set, and `GET /v1/admin/regression-set/cases/{n}` returns 404 (admin-10.14). eater-9.8 (old 9.7) adds the eater-visible result: "Withdrawn · your photos were removed from the test cases". The admin's consented count drops by one. The old line that expected `CONSENT_REQUIRED` from staff is gone, because it contradicted auditor-9.8.
7. **AT-29 exports, now able to fail.** eater-9.2 now uses SE6, support-9.7's fixture. Its prepared export is deleted by the withdrawal, and Settings → Export reads "Your earlier export was deleted when you withdrew a Consent. Prepare a new one." (auditor-9.14). The unfailable "holds no photo" line is removed. C-10 records the settlement.
8. **Deficit cap on own Targets.** eater-1.32 now shows Sam's 1,870 with the note "This is a bigger gap than Sips & Bytes proposes (up to 350 kcal for you). Your own number is kept." and stores `over_deficit_cap` true. New conflict C-22 says the cap binds proposals only and asks the approver whether own Targets need more.
9. **FRD §19.2 logs.** New eater-9.13 reads the served API's log stream during the Target flow and a Consent withdrawal. Only allow-listed fields may appear, so no age, height, weight, Target, macro, safety or Consent field. A forced crash report holds no profile value, and the log formatter is allow-list tested. eater-1.21 points to it.
10. **Coverage table.** The table was rebuilt from the stories. The WF-10 row names expiry (10.9). §3.2 lists the "why" stories, §3.3 the conservative ranges, and FR-077 encryption in transit (at rest deferred, said so). New rows cover FR-080, FR-081, FR-082, §19.1, §19.2 and §19.3. AT-29 lists every propagation story.
11. **Stale cross-lens ids.** Updated throughout:
    - Quotas: admin-10.35/10.36 → admin-10.38/10.41.
    - Export: support-9.7 → support-9.8. Retry: support-9.8 → support-9.9.
    - Deletion: support-9.9 → support-9.10. Reference: support-9.11 → support-9.12. In-app paths: support-9.12 → support-9.13.
    - Retention runs: auditor-9.14 → auditor-9.15. Export job: auditor-9.13 → auditor-9.14.
    - Deletion and completion record: auditor-9.9/9.10 → auditor-9.10/9.11. auditor-9.9 is now cited for the age gate.
    - C-19 no longer quotes support-9.7's old Settings path.
12. **`grant_40aa`.** eater-10.4 now uses the support lens's record, SE10's `grant_40ab` (`acct_f1e0c3`, Cairo; the support lens renamed it in its fix round 2): Requested 27 Sep 10:05 UTC, Unanswered 30 Sep 10:05 UTC. It no longer collides with `grant_31f0`. The other Grant fixtures now follow the support lens too: `grant_31f9` declined 11:42 UTC; `grant_52a3` withdrawn 12:26 UTC; `grant_52a7` ended 13:33 UTC; reads 10:24, 10:25 and 10:27 UTC.
13. **Age gate against auditor-9.9.** Added `POST /v1/age-gate` (age only, no identifier) and Shared lines to auditor-9.2 and 9.9.
    - eater-1.1 records the age confirmation (`age-1`, "onboarding age question", time, app version), and a missing confirmation returns 422 `AGE_REQUIREMENT`.
    - eater-1.2 now expects exactly one request, refused with 422 `AGE_REQUIREMENT`, and one `age.refused` event with no identifier. Settings → Privacy shows "Age 18+ confirmed".
    - C-18 is updated.
14. **Vague or unobservable Then clauses.** Each now names the data and the screen:
    - eater-1.19: the Entry appears, My Units lists Units, Progress shows the week, and the export reaches Completed.
    - eater-1.49: 7 and 5 Entries, 3 Units and the Template.
    - eater-9.9: each screen's result, including the deletion screen.
    - eater-1.50: a recent Unit adds an Entry.
    - eater-1.41: the Entry appears.
    - eater-1.7: the Add button's centre within the middle two-thirds of the screen height, measured on the smallest simulator.
    - eater-1.4: names "outside Egypt and Saudi Arabia" as shown text, with counsel's future wording moved to conflict C-26.
    - eater-1.17: names Onboarding · Safety screen and the Policy's guidance text, word for word.
15. **Outbound adapters.** eater-9.9 now lists Apple's Sign in with Apple token revocation among the API's outbound adapters (R4), so eater-9.19's revocation line and eater-9.9 agree.
16. **Places not in vocabulary.md.** The onboarding screens table, the account line and the Settings → Privacy parts (Grants, Support code, Delete account) are marked *(proposed)* and listed in C-19. "Settings → Goals → History" is replaced by the eater's WF-8 place, Progress → Target history (eater-8.23).
17. **Non-vocabulary states.**
    - A purpose with no Consent now reads "Never given", declared in "How to read" as a display label, not a state, and raised as C-20.
    - The expired export now uses support-9.8's wording, "Download no longer available since 27 Sep" (eater-9.16, SE7's `job_exp_31`); "has expired" is gone.
18. **One name for Pending.** eater-9.20 reads "3 Pending Entries will be deleted too". eater-9.6's offline withdrawal reads "reaches your account when you're online" — no second name for Pending.
19. **Activity mode names.** "Fixed target" and "Activity-adjusted target" were replaced, so the file now matches the map and eater-7.16/7.18/7.19:
    - Names: "Fixed mode" and "Activity-adjusted mode".
    - On Today: "Food Target: Fixed" and "Food Target: Activity-adjusted".
    - eater-1.39, 1.40 and 1.46 reuse eater-7.16 and 7.19's figures (Target 1,870, remaining 670; base 1,710, Target today 1,910).
    - eater-1.25's maintenance now reads "2,334.8 kcal a day · × 1.2, exercise included", as approver-10.69 expects. Resting energy and maintenance are shown to one decimal, as FRD §11.3 shows them.
20. **Camera and Photos.** The Photos Consent is named "Photos (camera and photo library)" everywhere:
    - eater-1.3's line now reads "Health, the microphone and photos (camera and photo library)".
    - eater-1.6 now follows eater-4.4: an in-app Photos Consent sheet before the iPhone's camera prompt.
    - eater-9.1 lists the auditor lens's purposes exactly, including Health: read active energy and Health: write food.
    - C-21 records that the app Consent and the iPhone permission are two switches.
21. **E1 versus E1.** Support-lens accounts are now SE1, SE2, SE3, SE6, SE7, SE8, SE10, SE12 and SE13, declared under "Fixture names". E1–E44 always mean research findings.
22. **WF-10 ids.** Grant stories are now `eater-10.1`–`10.10` in their own Journey 10 (map WF-10 order: request → approve or decline → inside the time box → the box ends); see the renumbering above.

**Other alignments found during the re-read** (no new defect numbers):
- The Target endpoints are renamed `POST /v1/targets/proposals`, `POST /v1/targets` and `GET /v1/targets`, the path eater-8.23 already reads.
- The AI, Photos and Microphone Consent sheets use "Give consent" and "Not now", with method "in-app sheet · first use", as the current eater-4.3, 4.4 and 4.5 do. On an AI withdrawal, Pending Analyses are Discarded and their photos deleted; a Processing one becomes Failed, as in eater-4.6.
- The Health sheet and Settings → Activity follow eater-7.1, 7.24 and 3.39.
- Today without a Target uses eater-3.2's "… consumed · No Target yet" with a "Set a Target" link; the dismissible card is gone.
- Policy states read "In effect".
- Grant copy matches the support lens after its fix round 2:
  - support-10.6: "Active · ends 14:20" and "Never included: photos, voice, your Target and goal settings", with "Target — not included in Grants" in support-10.14;
  - support-9.2: the code under Settings → Privacy → Support code, "Valid until 2 Oct, 09:12";
  - support-10.18: 404 `NOT_FOUND` for a Grant request after a deletion;
  - eater-1.19's Shared line now quotes support-10.14.
- New conflicts: C-23 (where the tracking-only guidance shows, against approver-10.53), C-24 (where an anonymous session's Entries live, against admin-10.41) and C-25 (raw-evidence access, auditor-9.8 against admin-10.14).
- C-6 and C-7 are marked resolved by approver-10.69 and approver-10.70.
- Other files still cite this file's old ids. They are: `support.md` (9.14, 9.17, 9.22–9.26, 9.29), `auditor.md` (9.7, 9.11, 9.12, 9.16, 9.25, 9.28), `eater/wf3-wf6.md` (9.2–9.4, unchanged) and `eater/wf2-wf4.md` (1.3, 1.6, unchanged). The renumbering map above translates each; this round edited no other file.

## Lens verdict — re-verify (2026-10-01)

**fail** — 4 defects. One is a round-1 fix that delta D3 has since overtaken; three are new, in lines fix round 1 changed.

Checked:
- each of the 22 round-1 defects against the current text;
- only the lines fix round 1 changed (a story-by-story diff against the verdict commit, through the renumbering map), against the map, the FRD (FR-002, FR-075–FR-082, AT-29, §3.2, §3.3, §11.3, §19.2), `vocabulary.md` with delta D3, and the stories they cite in `support.md`, `auditor.md`, `approver.md`, `admin.md` and the eater's other files.

What holds:
- Every cross-lens id in the body names the story it means, and every internal `eater-n.n` reference lands on the intended story.
- The set of research ids (C, F, P, R, E, EX) is unchanged by the round.
- The arithmetic in changed lines recomputes. That covers Hala's 5/10/15 % and 5/10 % choices and gaps; Huda's 1,300 · 1,230 · 1,200 and 12.2 %; her own maintenance, 1,275 → 1,280; Sam's 464.8 kcal (19.9 %); the 1,709.78 base; 1,910 and 710; 670; and every UTC and local time.
- The counts line is true: 90 stories (56 · 24 · 10) and 296 lines (`/m` 23 · `/s` 79 · `/r` 194). Every story has a `/r` line.

### Round-1 defects: fixed or not

| # | verdict | the line now |
|---|---|---|
| 1 | fixed | eater-10.9 "the Grant reads "Expired · 14:20" with its three reads in its history" and "403 `GRANT_NOT_ACTIVE` with state Expired"; eater-10.10 "the Grant reads "Ended by the Support agent · 16:33"" |
| 2 | fixed | eater-1.28 "exactly three choices show, with 15 % preselected (approver-10.68)" and "two choices show, with 10 % preselected"; Policy v1 "loss default 15 % with the choices 5 %, 10 % and 15 %; gain default +10 % with the choices 5 % and 10 %" |
| 3 | fixed | eater-1.1 "Why we ask: Sips & Bytes is for adults, and your age helps estimate your resting energy."; 1.10 "Why we ask: age, height and weight estimate your resting energy."; 1.13 "Your time zone decides which Day a meal belongs to. Your region sets food names and digits." |
| 4 | fixed (at rest deferred, and said so) | eater-9.11 "the app has no App Transport Security exception (no `NSAllowsArbitraryLoads`), and a request to the API over plain `http://` is refused, never served"; "the check on a hosted project waits for the dropped ship rows" |
| 5 | fixed | eater-9.5 "the row reads "Withdrawn · 10:12", with the line "iPhone Settings still allows the microphone — turn it off there too if you like""; "it reads "Camera and photos are off in your settings"" |
| 6 | fixed | eater-9.6 "the case is gone and `GET /v1/admin/regression-set/cases/{n}` returns 404 `NOT_FOUND`"; eater-9.8 "the row reads "Withdrawn · your photos were removed from the test cases"" |
| 7 | fixed | eater-9.2 "SE6's emulator seed (… 1 prepared export) … storage and queues hold none of them"; "Your earlier export was deleted when you withdrew a Consent. Prepare a new one." |
| 8 | fixed | eater-1.32 "This is a bigger gap than Sips & Bytes proposes (up to 350 kcal for you). Your own number is kept." and "`over_deficit_cap` true"; C-22 |
| 9 | fixed | eater-9.13 "every record has only the fields request id, route, status, duration, model version, cost and validation code — none named age, height, weight, rmr, maintenance, target, macro, safety, purpose or consent" |
| 10 | fixed | Coverage "WF-10 done-when … 10.9 (expiry)"; new rows for FR-080, FR-081, FR-082 and FRD §19.1, §19.2, §19.3; the counts line recounts true |
| 11 | fixed | eater-1.50 "Shared: eater + platform admin (admin-10.38, admin-10.41)"; 9.16 "(support-9.9)"; 9.18 "support-9.10, support-9.13 … auditor-9.10, auditor-9.11"; 9.21 "(support-9.12)"; 9.12 "auditor (auditor-9.15)"; 9.14 "support-9.8 … auditor-9.14" |
| 12 | fixed | eater-10.4 "SE10's `grant_40ab` was Requested on 27 Sep 2026 at 13:05 Cairo (10:05 UTC)" |
| 13 | fixed | eater-1.1 "Shared: eater + auditor (auditor-9.2, auditor-9.9)" and "422 `AGE_REQUIREMENT`"; eater-1.2 "exactly one request was sent, `POST /v1/age-gate` with the age as its only field" and "one `age.refused` event exists with no account id, device id or other identifier" |
| 14 | fixed | Each named Then clause now names its data:<br>• 1.19 "the Entry appears on Today, My Units lists their Units, Progress shows the week's consumed kcal and coverage … and the export reaches Completed"<br>• 1.49 "Day 2026-09-30 shows 7 Entries, Day 2026-10-01 shows 5"<br>• 9.9 "the deletion screen of eater-9.18 opens"<br>• 1.50 "a recent Unit tapped on Today still adds an Entry"<br>• 1.41 "and the Entry appears"<br>• 1.7 "the Add button's centre lies within the middle two-thirds of the screen height"<br>• 1.4 "says "They are processed by Google outside Egypt and Saudi Arabia""<br>• 1.17 "Onboarding · Safety screen shows, in place of its questions, the tracking-only guidance text of the Policy in effect, word for word" |
| 15 | fixed | eater-9.9 "the API's only outbound adapters are Gemini, USDA FoodData Central, Firebase/Google Cloud and Apple's Sign in with Apple token revocation" |
| 16 | fixed | "## Onboarding screens *(proposed — the map and vocabulary.md name none; conflict C-19)*"; "the **account line** *(proposed)*"; 1.43 "Progress → Target history lists one version"; C-19 lists the rest |
| 17 | half fixed | The export half is fixed: eater-9.16 "Download no longer available since 27 Sep — prepare a new export". The Consent half is not: "Not given" became "Never given", and delta D3 has since made that the wrong word (defect 1 below) |
| 18 | fixed | eater-9.20 "3 Pending Entries will be deleted too"; eater-9.6 "reaches your account when you're online" |
| 19 | fixed | eater-1.39 ""Fixed mode" is selected"; 1.42 ""Food Target: Fixed""; 1.46 ""Food Target: Activity-adjusted"" |
| 20 | fixed | eater-1.3 "Health, the microphone and photos (camera and photo library) are asked the first time you use them."; 1.6 "the iPhone's camera prompt appears only after "Give consent""; 9.1 lists Photos once |
| 21 | fixed | "Accounts reused from the support lens are prefixed **SE** (SE1 = the support lens's E1, and so on)" |
| 22 | fixed | "Grant stories trace to WF-10, so they carry journey number 10 (`eater-10.n`)"; eater-10.1 to eater-10.10 |

### Defects

1. **Vocabulary — "Never given" is not D3's "Not given" (round-1 defect 17, overtaken).**
   - vocabulary.md now reads: "Consent | Not given → Given · Withdrawn … "Not given" is the state before the eater has decided (delta D3)".
   - This file still says it is not a state. How to read: "A Consent purpose with no record at all is shown as "Never given" — a display label this lens proposes, not a Consent state (conflict C-20)". Its state list reads "Consent Given/Withdrawn".
   - The screens use the other word:
     - eater-1.3: "Optional research reads "Never given", and Diary processing reads "Never given · your diary is only on this iPhone"";
     - eater-1.6: "the Photos row reads "Never given"";
     - eater-9.1: "or "Never given" when no Consent exists" and "the other rows read "Never given", never blank";
     - eater-9.8: ""Optional research" reads "Never given"".
   - C-20 says "vocabulary.md's Consent states are Given and Withdrawn … A delta should fix one display label". D3 answered that, so C-20 is stale.
   - The eater's own eater-3.40 already says "Not given".
2. **Observable — eater-9.5 (new).** "and the "Add words" field and "Log from My Units" still work." names no data and no result. It is the same class as round-1 defect 14 ("the Unit mode still works"), which eater-1.50 fixed with "a recent Unit tapped on Today still adds an Entry".
3. **Observable — eater-9.13's two `/r` lines disagree (new).**
   - The first: "every record has only the fields request id, route, status, duration, model version, cost and validation code".
   - The second, for the AI Consent withdrawal: "it shows the route and status only".
   - A record with a request id and a duration fails the second line; one without them fails the first.
4. **Observable — where Today's "Set a Target" goes is stated three ways (changed lines).**
   - eater-1.8: "with a "Set a Target" link to Settings → Goals (eater-3.2)".
   - The onboarding table has Onboarding · Profile reached from ""Set a Target" on Today; Settings → Goals" — two separate entry points.
   - eater-1.45: "When Hala taps "Set a Target", Then Onboarding · Profile opens".
   - eater-1.44: ""Set a Target" resumes at Onboarding · Macros".
   - A verifier tapping the link cannot tell whether Settings → Goals or the Target flow should open.

### Cross-lens (for the model phase join; uncounted)

- **Old ids in other files.** These files still cite this file's ids from before the renumbering; the map in "Fix round 1" translates each:
  - `support.md` cites eater-9.14 (now 9.16), 9.17 (9.19), 9.21 (10.1), 9.22 (10.2), 9.23 (10.3), 9.24 (10.4), 9.25 (10.7), 9.26 (10.8), 9.27 (10.5) and 9.29 (9.23).
  - `auditor.md` §7 B rows M1, M11 and M13 cite eater-9.3, 9.7, 9.11, 9.12, 9.16, 9.25 and 9.28. They also describe this file's pre-fix state: "c-diary-1", "write dietary energy", no "read active energy", method "in-app sheet · Capture".
- **SE8's former email.** eater-9.21 signs up a new account with `lina.synthetic@example.com` on 2026-10-01. On the same seed, support-9.12 expects that email to give "No account matches this email", and support §0.3 says it "exists only in this table".
- **The Health write Consent.**
  - This file names it "Health: write food" (auditor-9.1). It also cites "WF-3's "Write meals to Apple Health"" in Settings → Activity (eater-9.4).
  - `eater/wf3-wf6.md` uses "Health: write dietary energy" in Settings → Privacy. It says that name replaced "Write meals to Apple Health", and that it follows this file's naming.
- **Own-Target source.** eater-1.32 uses "typed by you" (`typed_by_eater`) and points to eater-8.23. eater-8.23's sources are "Estimated by the app", "Entered by you", "Clinician-provided" and "Accepted suggestion".
- **The Activity-adjusted base.**
  - eater-1.40 computes 1,709.78 from 1,870 / 2,334.8 and labels the gap "−20 %"; eater-1.32 shows the same gap as 19.9 %.
  - eater-7.18 computes "your 20 % = 1,707.84".
  - Both files show 1,710.
- **Wrong story cited.** eater-1.41 says "eater-3.39 asks after the first Confirmed Entry". The story that asks is eater-3.40.
- **The Photos purpose name.** The Consent purposes fixture says "as the auditor lens lists them (auditor-9.1): … Photos (camera and photo library)". auditor-9.1 lists "Photos".


## Fix by the session (2026-10-01), after the re-verify
1. "Never given" → "Not given" everywhere (8 places); How to read and the state list follow D3; C-20 is marked resolved by D3.
2. eater-9.5: the Photos-withdrawn line now names data and result (typed "3 cheese bites" reaches Analysis review; Log from My Units adds one Entry).
3. eater-9.13: the AI-withdrawal log record holds only the allowed fields of the line above, so the two lines agree.
4. One rule for "Set a Target": from Today or Settings → Goals it opens the Target flow at its first unfinished step (Profile when nothing is filled in; where she stopped otherwise) — eater-1.8, the onboarding table, 1.44 and 1.45 agree.

## Lens verdict — closing (2026-10-01)

**fail**: 1 defect. Re-verify defects 1, 2 and 3 are fixed. Defect 4 is fixed in every place it named, but the rule now written into eater-1.8 leaves out the local trial.

This was a scoped check of commit `baaac88`. Its changed lines are:
- in How to read, the Words entry;
- the onboarding table's Onboarding · Profile row;
- eater-1.3 line 3, 1.6 line 3 and 1.8 line 1;
- eater-9.1 lines 1 and 3, 9.5 line 3, 9.8 line 1 and 9.13 line 2;
- C-20.

The check used `way/personas/_lens-verifier-brief.md` and its addendum, with `way/vocabulary.md` (D2, D3) as binding. Each changed line was checked against the stories it touches: eater-1.7, 1.44, 1.45, 1.49, 4.44 (in `wf2-wf4.md`) and 9.13 line 1, and the proposed interfaces. Unchanged material was not re-audited. No outside source was opened, and no request was sent anywhere.

### The 4 re-verify defects

| # | status | the changed line |
|---|---|---|
| 1 | fixed | How to read: "Consent Not given → Given · Withdrawn (D3)" and "A Consent purpose the eater has not decided is in the state **Not given** (vocabulary D3)".<br>1.3: "Optional research reads "Not given", and Diary processing reads "Not given · your diary is only on this iPhone"".<br>1.6: "the Photos row reads "Not given"".<br>9.1: "or "Not given" when no Consent exists" and "the other rows read "Not given", never blank".<br>9.8: ""Optional research" reads "Not given"".<br>C-20: "Resolved by delta D3".<br>No "Never given" is left in the body. Each line reaches Not given without the Consent ever being given, so D3's order holds. |
| 2 | fixed | 9.5 line 3: "typing "3 cheese bites" in its "Add words" field reaches Analysis review as "cheese bite × 3", and "Log from My Units" → cheese bite → Log adds one Entry to Today". The line now names its data (cheese bite × 3, one Entry) and where each shows (Analysis review, Today). |
| 3 | fixed | 9.13 line 2: "it holds only the allowed fields of the line above (request id, route, status, duration, model version, cost, validation code) and no field naming the purpose or the action". The field list is line 1's. The route, `POST /v1/me/consents`, names neither the purpose nor the action. |
| 4 | fixed where named; one gap remains (defect 1) | 1.8: "a "Set a Target" link that opens the Target flow at its first unfinished step (Onboarding · Profile when nothing is filled in, eater-1.45; the step where she stopped otherwise, eater-1.44)".<br>Table: ""Set a Target" on Today or Settings → Goals — both open the Target flow at its first unfinished step".<br>Both agree with 1.44 ("resumes at Onboarding · Macros") and with 1.45 ("Onboarding · Profile opens"). The link no longer goes to Settings → Goals. |

### Defects

1. **eater-1.8, line 1, against eater-1.49, line 2: in the local trial, "Set a Target" opens two different screens.**
   - 1.8 now says the link "opens the Target flow at its first unfinished step (Onboarding · Profile when nothing is filled in …)" and gives no exception.
   - 1.49 line 2 reads: "Given the local trial, When she taps "Set a Target" on Today, Then Onboarding · Account opens with "Your Target and profile are kept in your account"". The table's own Onboarding · Account row agrees: it is reached from ""Set a Target" in a local trial".
   - 1.8's Given ("Hala has a 250 kcal Entry and no Target") does not say whether Hala is signed in. The 250 kcal Entry it reuses comes from 1.7 line 4, and that Hala is in the local trial: she tapped "Keep it on this iPhone for now" in 1.7 line 1.
   - For that Hala, with nothing filled in, 1.8 expects Onboarding · Profile and 1.49 expects Onboarding · Account. No build can pass both lines.
   - The fix note says "One rule … eater-1.8, the onboarding table, 1.44 and 1.45 agree", but it leaves out 1.49. Either the rule needs its trial branch (Account first), or 1.8's Given needs a signed-in Hala.

### Cross-lens (for the model phase join), uncounted

- eater-3.2 in `wf3-wf6.md` still reads "a "Set a Target" link to Settings → Goals". 1.8 cites eater-3.2 for its new rule.
- auditor.md's Consent counts show Microphone and Photos as ""No records yet"". C-20 no longer notes that the auditor lens has its own label for a purpose with no record.
- The other cross-lens items of the re-verify were not re-checked.

### Note, uncounted

- 9.1 line 1 shows "Not given" "when no Consent exists", and "Read the text" "when never given". D3 makes Not given the state of a Consent before the eater decides. The model phase decides whether that Consent has a stored record. The screen shows the same either way.


## Second fix by the session (2026-10-01), after the closing check
eater-1.8 and the onboarding table: the first unfinished step of the Target flow is Onboarding · Account for a local-trial eater (eater-1.49), Profile for a signed-in eater with nothing filled in (1.45), and where she stopped otherwise (1.44) — one rule, no exception missing.

## Lens verdict — closing 2 (2026-10-01)

**pass**: 0 defects. The closing check's 1 defect is fixed, and the changed lines add no new defect.

This was a scoped check of commit `5e069c5`. In the body of this file, that commit changed two places: eater-1.8 line 1 and the onboarding table's Onboarding · Profile row. It also appended the closing verdict and the second fix note. The check used `way/personas/_lens-verifier-brief.md` and its addendum, with `way/vocabulary.md` as binding. The changed lines were checked against eater-1.7, 1.44, 1.45 and 1.49, and against the table's Onboarding · Account row. Unchanged material was not re-audited. No outside source was opened, and no request was sent anywhere.

### The closing defect

| # | status | the changed lines |
|---|---|---|
| 1 | fixed | 1.8 line 1: "opens the Target flow at its first unfinished step — Onboarding · Account while she is in the local trial (eater-1.49), Onboarding · Profile when she has an account and nothing is filled in (eater-1.45), the step where she stopped otherwise (eater-1.44)".<br>Table, Onboarding · Profile row: "both open the Target flow at its first unfinished step (Onboarding · Account first for a local-trial eater)".<br>**Against 1.7 and 1.49.** 1.8's Hala, with her 250 kcal Entry, is the Hala of 1.7, who tapped "Keep it on this iPhone for now" in 1.7 line 1. She is in the local trial, so 1.8 now expects Onboarding · Account. 1.49 line 2 expects the same: "When she taps "Set a Target" on Today, Then Onboarding · Account opens". The two lines now share one passing behaviour.<br>**Against the Onboarding · Account row** ("reached from … "Set a Target" in a local trial"): the row agrees with the Profile row's new parenthesis and with 1.8.<br>**Against 1.45** ("When Hala taps "Set a Target", Then Onboarding · Profile opens"): this is 1.8's account branch. 1.45 line 2 marks her Entry Pending, which means it is queued for the server, so that Hala is not the local-trial Hala, whose Entries never reach `/v1/consumption` (1.7 `/s`).<br>**Against 1.44** ("resumes at Onboarding · Macros"): this is 1.8's last branch. A local-trial eater cannot stop partway through the flow, because Onboarding · Account comes first and the profile is kept in the account (1.49 line 2), so the branches do not overlap. |

### Defects

None.

### Cross-lens (for the model phase join), uncounted

- eater-3.2 in `wf3-wf6.md` still reads "a "Set a Target" link to Settings → Goals" (line 4). 1.8 cites eater-3.2 for its rule. This is unchanged from the closing check.

### Notes, uncounted

- 1.45's Given ("no network") does not say that Hala has an account. Only its Pending mark implies one. Saying "signed in" in the Given would let a verifier choose the fixture without inferring it.
- No line covers a local-trial eater who taps "Set a Target" with no network. In that case Onboarding · Account opens, but no account can be created offline. 1.45 says why for an eater with an account only. This step was not changed by `5e069c5`.
