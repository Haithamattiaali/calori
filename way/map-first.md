# The first map — from the brief only (2026-10-01), before research

Source: `way/brief/frd-v1.0.md` (cited as §n / FR-nnn). The final map in `blueprint.md` §1 supersedes this file once research cycle 1 is in it.

## 1 · The operation in one paragraph

An adult who eats home-cooked, mixed and culturally specific food (Egyptian, Saudi) wants to lose, keep or gain weight without re-weighing and re-explaining the same food every day. Today they weigh a habitual portion, photograph plates, describe recipes and ask a chatbot for meal and day totals — and the chatbot forgets definitions, drifts, double-counts and resets (§1.1). In Sips & Bytes the eater defines a bite, spoonful, cup or mixed portion **once** (a versioned personal unit, measured or estimated, with its evidence), then logs "three cheese bites and a cup of laban" in seconds, plans a meal from a photo of the table under a calorie cap and macro limits, confirms what was actually eaten, corrects history without rewriting it silently, and sees a meal report and a day report that reconcile exactly with an append-only ledger. AI only interprets (photo, label, voice, recipe text); reviewed nutrition sources supply the numbers; deterministic code calculates; a solver checks plans; the eater approves what is recorded (§ principle). Behind the app, a nutrition approver curates reference foods, Arabic aliases and versioned policy defaults; a platform admin rolls models and configuration out and back; a support agent helps without seeing a diary unless access is granted just in time and audited. **Success** = a repeat log in ≤10 s median, ≥70 % of week-two logs reusing a unit or template, zero unexplained ledger discrepancies, and the eater's intake staying within the approved target trend (§1.5).

## 2 · Personas, and the hidden-persona hunt

| persona | who | surface | from |
|---|---|---|---|
| **Eater** | adult tracking intake for weight or exercise; prefers "six bites" to grams; English/Arabic/code-switching | iOS app | §1.2 |
| **Nutrition approver** | qualified nutrition reviewer: approves public food reference records, aliases, launch defaults and safety bounds | admin console | §3.3, §23.2, FR-080 |
| **Support agent** | answers eater issues; sees failed jobs and account state; no diary access without just-in-time grant | admin console | FR-081 |
| **Platform admin** | rolls model IDs, prompts, schema versions and remote config forward and back; kill switch; quotas; roles | admin console | §16.4, FR-080 |
| **Auditor / privacy reviewer** (hidden) | reads only: the audit of JIT diary access, deletion completion records, consent changes | admin console (read-only) | FR-081, FR-082, NFR-13 |
| Household member (hidden, **P1**) | shares recipes; own portion weights | — | §1.2: household sharing is P1 → not in v1 |
| Owner who pays (hidden) | free tier + paid tier for expanded AI (§23.3, "proposed") | — | commercial model is proposed, not P0 → open question |
| System actors | AI analyzer (Gemini) · nutrition resolver (USDA FDC + approved local records) · planner (OR-Tools) · HealthKit (on device) · outbox sync · retention/deletion jobs · model registry | API | §15, §16 |

## 3 · The interaction table

| from → to | action | artifact | rule | value event |
|---|---|---|---|---|
| Eater → app | onboard, set goal | GoalPlanVersion | Mifflin–St Jeor, policy defaults, safety scope, approval | target approved |
| Eater → app | create/recalibrate a unit | EatingUnitVersion / CompositeUnitVersion / RecipeVersion | versioned, evidence status, acyclic, mass balance | unit approved |
| Eater → AI analyzer | photo / label / voice / text | AIAnalysis (draft) | schema-constrained, validated, ≤2 questions, untrusted text | draft ready for review |
| AI analyzer → resolver | candidate food ids | FoodReferenceVersion | resolver order FR-025; AI cannot verify | numbers resolved |
| Eater → ledger | consume (tap, voice, plan confirm) | ConsumptionEvent | idempotent command, server-resolved snapshot | entry accepted, day revised |
| Eater → planner | plan from available foods + constraints | MealPlan | solver verifies, unrounded check, infeasible explained | plan proposed |
| Eater → ledger | correct / void / restore / move day | ConsumptionEvent (supersedes) | expected revision; scope choice | effective entry replaced |
| HealthKit → app → API | import activity, weight | ActivityEvent, WeightObservation | dedupe, activity mode, no double credit | activity reconciled |
| Ledger → Eater | meal + day report, periods | DiaryDayProjection | target version per day; coverage; 4/4/9 shares | progress understood |
| Eater → API | export / delete account / consent | privacy job | separate consents; never paywalled | export delivered / deletion recorded |
| Nutrition approver → reference DB | review, approve, alias | FoodReferenceVersion, alias | evidence status, moderation | record approved |
| Nutrition approver → policy | version calorie defaults, bounds | PolicyVersion | qualified review | policy live |
| Platform admin → registry | roll model/prompt/config, kill switch | RegistryVersion | shadow → canary → rollout | config live / rolled back |
| Support agent → eater account | request JIT diary access | AccessGrant | approval + time box + audit | issue resolved |
| Auditor → audit trail | read | AuditEvent | read-only | audit reviewed |

## 4 · Workflows and the vocabulary

- **WF-1 Onboard and set a target** — local trial → profile → resting energy → maintenance → target and macros → activity mode → approve (FR-001…008, 056…059).
- **WF-2 Define a unit** — simple, composite (with bread rules) or recipe; measured by scale/label photo or typed; approve a version (FR-009…031).
- **WF-3 Log what I ate** — tap a recent unit, speak or type "three cheese bites", or approve an analysis; one-tap with Undo; offline outbox (FR-039…047).
- **WF-4 Capture and analyse** — photo/label/scale/voice → draft → ≤2 questions → resolve sources → review (FR-032…039). An input path into WF-2, WF-3 and WF-5.
- **WF-5 Plan a meal and confirm it** — available foods + constraints → solver → plan → Ate as planned / Change / Not eaten (FR-048…055, FR-045).
- **WF-6 Correct history** — correct, void, restore, move day; scope this entry vs future default (FR-041, §8.2).
- **WF-7 Activity** — HealthKit and manual exercise → dedupe → activity mode → budget (FR-062…068).
- **WF-8 Reports and progress** — meal and day report after each entry; 7/28/custom periods; coverage; weight trend (FR-069…075).
- **WF-9 Privacy** — consents, export, delete account (FR-076…079).
- **WF-10 Govern the reference and the AI** — approve food records and aliases, version policies, roll models and config, JIT access with audit (FR-080…082, §16.4).

Vocabulary (one name per thing, screen = code = logs): **Unit** (personal eating unit; kinds bite, spoonful, sip, cup, piece, slice, handful, custom) · **Composite** (a unit made of components) · **Recipe** · **Food** (a reference food record) · **Entry** (one consumption) · **Day** (diary day) · **Target** (an approved goal-plan version) · **Plan** (meal plan) · **Analysis** (an AI draft) · **Evidence** (badge: label-verified, recipe-calculated, measured unit, estimated analogue, user-defined) · **Correction**, **Void**, **Restore** · **Pending** (queued offline) · **Activity**. Tabs: Today · Capture & Plan · My Units · Progress.

## 5 · Done-when per workflow

- WF-1: a new eater finishes onboarding, sees resting energy, maintenance, target and macro grams with assumptions, approves; Today shows the target and the activity mode.
- WF-2: "cheese bite" saved with 5.4 g cheese + 1.5 g oil + 8 g bread; My Units lists it; Today's total unchanged.
- WF-3: "three cheese bites" logged in ≤3 taps; meal report and day report appear and reconcile; Undo removes exactly one entry; offline logs sync once.
- WF-4: a plate photo returns editable chips with evidence badges; nothing is consumed until approved.
- WF-5: a table photo + cap 500 kcal + carbs ≤30 % → counts that satisfy unrounded constraints, or an infeasible answer naming the blocking constraint; "Ate as planned" records exactly one meal.
- WF-6: "18 not 15" shows old/new/delta and replaces the effective entry; yesterday's correction leaves today untouched.
- WF-7: one workout from HealthKit + a matching manual entry → one contribution; in fixed mode the food target does not grow.
- WF-8: the day report shows target, consumed, remaining, shares summing to 100.0 %, coverage; a week with 2 missing days shows coverage, not zeros.
- WF-9: export downloads all entries, units, recipes, targets; delete account removes private data and media and leaves a completion record.
- WF-10: an approver approves a food record with its source; the eater's resolver uses it; an admin rolls back a model version and manual logging keeps working.

## 6 · The what-else pass (configuration points, never future code branches)

- Display units (g/oz, kg/lb, kcal/kJ), numerals (Arabic-Indic/Western), language → user settings.
- Diary-day boundary (default local midnight; custom) → user setting.
- Accompaniment rules (bread per dipped bite, exceptions) → user rules with precedence.
- Activity mode (fixed target / activity-adjusted with credit factor and cap) → user setting, policy-bounded.
- Calorie defaults (loss −15 %, gain +10 %), safety bounds, energy-mismatch threshold (>10 % and >10 kcal), component-sum tolerance, planner increments (whole/half), clarification limit (2) → versioned policy, admin-edited.
- Retention (raw scans 30 days, audio 24 h) → policy.
- Model IDs, prompt and schema versions, quotas per user → registry.
- Evidence badge set → fixed vocabulary (C1 does not let admins invent badges).

## 7 · Open questions → research (cycle 1)

1. How do the best tracking apps (MyFitnessPal, Lose It!, Cronometer, MacroFactor, Cal AI, SnapCalorie and others) handle custom servings, recipes, quick logging, photo AI, corrections and reports; what do users praise and hate?
2. Which nutrition sources cover Egyptian and Saudi foods (national food composition tables, SFDA, FAO/INFOODS), with what licensing?
3. Which current Gemini model IDs and SDK are stable today (the brief names gemini-3.8-flash)?
4. What has changed recently in iOS that this app should use (App Intents/Siri, widgets, Live Activities, on-device Foundation Models, Arabic speech recognition), and what do App Store rules and the likely launch market's data law (Saudi PDPL, Egypt) require of a health-data app?
5. Where is the operation going (AI photo logging accuracy evidence, GLP-1-era tracking, protein focus, adaptive targets)?
