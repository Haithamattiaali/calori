# Sips & Bytes — the blueprint

## §0 The profile

Written 2026-10-01 at Tailor. Brief: `way/brief/frd-v1.0.md` (the owner's FRD v1.0, verbatim). Every later part of the run reads this section, never the chat.

| # | Line | Value | Source |
|---|---|---|---|
| 1 | Kind (per surface) | (a) **mobile — native iOS app** (SwiftUI) for the eater · (b) **service/API** (Python FastAPI) for the app and system actors · (c) **web — admin console** for the nutrition approver, support, platform admin and auditor | (a)(b) brief §1.4, §15.1; (c) the console is brief FR-080 ("role-based administrator console", no surface named) — **web is the recommended default**; the auditor is the hidden persona the first map found (`way/map-first.md` §2, from FR-081/082) |
| 2 | Size | **platform** (from product by delta D1, §3) — 10 workflows; 5 human personas (eater, nutrition approver, support agent, platform admin, auditor — the last found by the first map's hidden-persona hunt) + system actors; 3 external integrations (Gemini, USDA FoodData Central, HealthKit) on Google/Firebase infrastructure | inferred from the brief as product; changed to platform by delta D1 (§3, 2026-10-01) when the final map counted 10 workflows |
| 3 | Customization depth | **C1** — versioned product policies (calorie defaults, safety bounds, thresholds), master data (reviewed food records, aliases), model/config registry, roles screen (permissions → roles → users). The eater's own units and rules are user data, not admin builders | inferred from the brief (§3.3, §16.4, FR-080, FR-081) |
| 4 | Tenancy | **one organisation**; every private record scoped to its user; cross-user access is a release blocker | brief NFR-07, §22 |
| 5 | Reach | **English + Arabic** (right-to-left, Arabic-Indic and Western numerals, Egyptian aliases, code-switching voice); user time zones and custom diary-day boundaries; metric first, display units selectable | brief §1.2, §8.1, §14.1 |
| 6 | Systems it must talk to | Gemini (Google Gen AI SDK, Agent Platform) · USDA FoodData Central · Apple HealthKit · Firebase Auth + App Check · Firestore · Cloud Storage · Cloud Run · OR-Tools (library) · operations: Cloud Tasks, Secret Manager, Cloud Logging/Monitoring, Firebase Crashlytics and Remote Config. Each external service sits behind an adapter with a realistic mock until the owner's cutover; the operations services are hosting-side and wait with the dropped ship rows (no hosting target), their code seams (async job queue, secrets from the environment, structured redacted logs, remote-config-shaped registry) are built from the first slice | brief §15.1 (Component table incl. the Operations row); adapters with mocks until cutover: **recommended default** (the /way method's product floor) |
| 7 | First look | **clickable prototype** of the core journeys before any backend | brief §23.1 (Foundation: "prototype core flows"); recommended default |
| 8 | How it runs | **fast** — the one yes on the prototype, then alone to Close; the MVP is shown without waiting | his answer, 2026-10-01: chose "Fast (Recommended)" |
| 9 | Hard limits | Stack per brief §15.1 (SwiftUI · FastAPI · Firebase Auth/App Check · Firestore · Cloud Storage · Gemini · OR-Tools); general-wellness only — no diagnosis, dosing, child/pregnancy planning (§1.3, §19.3); release blockers of §22 are hard; region not yet chosen (§15.3) | brief |
| 10 | Where it lives and ships | **a remote, no hosting yet** — GitHub `Haithamattiaali/calori` (public), this session's branch `claude/magical-cerf-axi3k1` (pushed 2026-10-01). The remote has no `main` branch yet, so a draft PR has no base: creating `main` is a push to another branch and waits for his word (asked once, at the gate). His answer is the written go for the remote and the pipeline only; no staging, no production | his answer, 2026-10-01: chose "GitHub only, no hosting yet" |

### What the profile switches on and off

- **On:** the canvas at the prototype (mobile); proof of the iOS surface on the smallest and largest iPhone simulator; proof of the API through its own HTTP interface; the admin console proved in a browser at desktop and ~390 px; C1 (configuration module, master data, roles screen, lighter customization proof); English + Arabic RTL and time zones from the first slice; the prototype and `design.md`; the pipeline on the remote.
- **Off:** C2 builders and scouts; many-tenant probes (per-user isolation probes stay — NFR-07); the ship proof, go-live, operate and measure rows — **dropped with their risk until a dated delta names a hosting target** (risk: the certified commit is buildable and tested in CI but not deployed; no alerts, restore drill or KPI reviews run).

### Environment facts that shape the hows (read 2026-10-01)

- This session runs in a Linux cloud container: Node 22, Python 3.11, Java (Firebase emulators possible), Docker, Postgres client; **no Xcode, no Swift, no gcloud, no `gh`** (GitHub through its MCP tools).
- The iOS surface is compiled and proved on **GitHub Actions macOS runners** (free: the repo is public); its walks run on the iOS simulator there and return screenshots as run artifacts. Update 2026-10-01: he widened the environment's network access to unrestricted, so the Swift 6.4 Linux toolchain is installed here for `swift build`/`swift test` of the client's platform-neutral core; SwiftUI, HealthKit and simulator walks still need the CI macOS runner. The nutrition core lives on the server (brief §15.1).
- The Claude Design canvas is reachable from this session (the Design artifact type), so the prototype boards are drawn there.
- The keep-going loop, armed 2026-10-01: this cloud session takes the next open ledger row on every turn; background agents' completion notices start the next turn; a self check-in (`send_later`, trigger re-armed each fire (latest `trig_01SRGA9842Evo49esYNo9aMM`, 07:17Z), re-armed after each fire while work remains) is the fallback when a turn ends with nobody there. It holds only at the yes and at a true blocker; every session resumes from the pushed branch and `way/` (`/build-loop` lives on his Mac, not here).
- The repo is public: synthetic data only, no secrets in the tree, ever.

## §1 The map

Final map, 2026-10-01. Built from the first map (`way/map-first.md`, from the brief) and research cycle 1 (`way/research/r1-*.md`; refutation in `way/research/r1-refute-a.md`, `r1-refute-b.md`). Research changes are cited by finding id: C = competitors, F = food sources, P = platforms, R = rules and trends. A finding marked `assumption` in its file is cited with "(assumption)".

### 1 · The operation in one paragraph

An **adult** (18+; R13, R16, R22) who eats home-cooked, mixed and culturally specific food — Egyptian, Saudi, wider Arab — wants to lose, keep or gain weight without re-weighing and re-explaining the same food every day. Today they weigh a habitual portion, photograph plates, describe recipes and ask a chatbot for totals, and the chatbot forgets, drifts, double-counts and resets (brief §1.1). The big trackers do not fix this for them: portions are remembered at best as a saved meal, not as a versioned, measured unit with its evidence and composite rules (C6 as corrected in r1-refute-a), cooked yield is handled properly mainly by Cronometer (C22), crowd-sourced numbers are distrusted (C9), offline use is a cache at most (C35), bulk deletes cannot be recovered (C27 as corrected), and the big Western trackers list no Arabic (C16 as corrected in r1-refute-a, C29, C36) — while Loqma claims full right-to-left Arabic (F34), Kam Calorie takes Arabic voice input though its store page lists English only (F32 as corrected in r1-refute-a), and Cal AI lists Arabic (C41). In Sips & Bytes the eater defines a bite, spoonful, cup or mixed portion **once** — a versioned personal **Unit** with its evidence — then logs "three cheese bites and a cup of laban" in a few taps, by voice or from Siri and a widget — table stakes, since MFP and Lose It! offer Siri Shortcuts (C54 as corrected in r1-refute-a; P22–P24); copies a previous meal or day and saves meal **Templates** (C5, C23, C45); photographs a table, adds words for what the camera cannot see (C32; estimates anchored to a composition database err far less, R40) and plans a meal under a calorie cap and macro limits that a solver verifies; confirms what was actually eaten; corrects history without silently rewriting it; and reads a meal report and a day report that reconcile exactly with an append-only ledger, written back to Apple Health (C12, C56, P29). AI only interprets; reviewed sources supply numbers (Tier A USDA FoodData Central, CC0, F1–F5; Tier B approver-built recipe records for regional dishes, F12–F17, F19); deterministic code calculates; the eater approves what is recorded. Behind the app a nutrition approver curates reference foods, dialect-tagged Arabic aliases (F11, F27) and versioned safety policy (NIDDK's 1,000 kcal hard stop R32, AHA ranges R33; the 1,200 kcal floor is our own product policy — no source names it a hard floor, r1-refute-b); a platform admin rolls models forward and back (P1–P4, P5 as corrected in r1-refute-b); a support agent helps without seeing a diary unless access is granted just in time; an auditor reads the trail. It is a general-wellness product, never medical advice (R6, R16). **Success** = a repeat log in ≤10 s median and the fewest taps per path (C34; tap count is how users judge trackers, C4 — its rating figures unverified), ≥70 % of week-two logs reusing a unit or template, zero unexplained ledger discrepancies, and the eater's intake trend within the approved target (brief §1.5).

### 2 · Personas, and the hidden-persona hunt

| persona | who | surface | from |
|---|---|---|---|
| **Eater** | adult 18+ tracking intake for weight or exercise; "six bites", not grams; English, Arabic (Egyptian, Gulf, MSA) and code-switching; logs at the table, one-handed, often in a hurry | iOS app (+ Siri, widget) | brief §1.2; R13, R16; P22–P24 |
| **Nutrition approver** | qualified reviewer: approves reference food records and Tier B recipe records for regional dishes, dialect-tagged aliases, the versioned safety policy (calorie floors, deficit caps, tracking-only rules) | admin console | brief §3.3, FR-080; F12–F17, F19, F27; R32, R33, R35 |
| **Support agent** | answers eater issues; sees failed jobs and account state; diary access only by a just-in-time, time-boxed, audited Grant that **the eater approves in the app** (the data is theirs; no staff member can approve their own request) | admin console | FR-081; the approver is the eater — recommended default |
| **Platform admin** | rolls model IDs, prompt and schema versions and configuration through shadow → canary → rollout, with a kill switch; per-user quotas; roles | admin console | brief §16.4, FR-080; P1–P4, P5 as corrected in r1-refute-b |
| **Auditor** (hidden, found by the first map) | reads only: just-in-time access grants, consent changes, deletion completion records, policy and registry history — the privacy reviewer/DPO seat the launch markets ask for | admin console, read-only | FR-081/082; R24, R25 |
| Household member (hidden) | shares recipes; own portion weights | — | **P1** (brief §1.2) — drop list |
| Owner who pays (hidden) | a paid AI tier through in-app purchase | — | brief §23.3 "proposed"; R9 — drop list for v1, with per-user quotas instead |
| Minor (hidden, excluded) | under 18 | — | R16 forbids Gemini for under-18 services; R13 rates calorie tracking 9+ → an age gate keeps them out |
| System actors | AI analyzer (Gemini 3.8 Flash for images and recipes, 3.5 Flash-Lite candidate for text intent; P1–P4, P5 as corrected in r1-refute-b) · nutrition resolver (approved records → Tier A → recipe calculation → analogue) · planner (OR-Tools CP-SAT) · HealthKit (on device: reads activity and weight, writes food correlations; P29–P30) · outbox sync · retention and deletion jobs · model registry | API, iOS | brief §15–16 |

### 3 · The interaction table

| from → to | action | artifact | rule | value event |
|---|---|---|---|---|
| Eater → app | confirm age 18+, give separate consents (diary processing; sending photos/voice/text to Google's AI, named; each Health type; mic; photos; optional research) | Consent records (version, time, method) | explicit, per purpose, one-tap withdrawal, no feature paywalled behind consent (R2, R3, R21, R22, R31) | consents recorded |
| Eater → app | answer the optional safety screen (SCOFF-style items, pregnancy, breastfeeding, GLP-1) | SafetyScreen | ≥2 SCOFF yes, pregnancy or breastfeeding → tracking-only; GLP-1 → protein-first, no added deficit (R35, R37, R38, R41) | mode chosen |
| Eater → app | set a target | GoalPlanVersion | Mifflin–St Jeor estimate ±10 % (R36); policy defaults; policy floor 1,200 kcal (approver-owned), hard stop below 1,000 (R32); deficit cap within AHA's 500–750 kcal (R33); approval | target approved |
| Eater → app | create or recalibrate a Unit; weigh the cooked pot for a Recipe | Unit / Composite / Recipe versions | versioned, evidence status, acyclic, mass balance, cooked yield first-class (C22) | unit approved |
| Eater → AI analyzer | photo, label, voice, or photo + words | Analysis (draft) | schema-constrained output, server validation, sampling parameters not relied on (deprecated for Gemini 3.x; P5 as corrected in r1-refute-b), ≤2 questions, text in images is untrusted, Health data never sent (R7) | draft ready |
| Analyzer → resolver | candidate foods | Food reference versions | resolver order FR-025; AI cannot verify; alias by dialect (F27) | numbers resolved |
| Eater → ledger | consume: tap a recent Unit, copy a meal or day, log a Template, Siri, widget, approve an Analysis, confirm a Plan | Entry (event) | one idempotent consume command for every surface (P22–P24); server-resolved snapshot | entry accepted, day revised |
| Ledger → HealthKit | write the entry as a food correlation; rewrite on correction, delete on void | HealthKit sample ids on the Entry | only after the Health consent (P29, R1, R7) | Health updated |
| Eater → planner | plan from available foods + constraints | Plan | solver verifies with unrounded values; infeasible explained; floor policy respected | plan proposed |
| Eater → ledger | correct / void / restore / move day | Entry (supersedes) | expected revision; scope choice: this entry vs future default | effective entry replaced |
| HealthKit → app → API | import workouts, active energy, body mass | Activity, Weight | dedupe; activity mode; "no data" never shown as "denied" (P30) | activity reconciled |
| Ledger → Eater | meal and day report; 7/28/custom periods; coverage | Day projection | target version per day; 4/4/9 shares; coverage | progress understood |
| Eater → API | export; delete account | Privacy job | in-app, no email or phone, ≤30 days, processors told, Sign in with Apple revoked, completion record (R4, R23, R31) | export delivered / deletion recorded |
| Approver → reference | approve a food record or a Tier B recipe record; add a dialect-tagged alias | Food version, Alias | evidence + licence on every record (F1, F8); cross-check NNI/SFDA, never copy (F12–F17, F19) | record approved |
| Approver → policy | version calorie floors, deficit caps, thresholds, retention | Policy version | qualified review | policy live |
| Admin → registry | roll a model/prompt/schema version; kill switch | Registry version | shadow → canary → rollout; frozen model ids, no `latest` (P1–P4) | config live / rolled back |
| Support → Eater | request just-in-time diary access | Grant (requested) | the eater sees who asks, why and for how long, and approves or declines in Settings | Grant approved or declined |
| Support → eater diary | read within the Grant | Grant (active) | time box, read-only, every read audited, auto-expiry | issue resolved |
| Auditor → trail | read | Audit events | read-only | trail reviewed |

### 4 · Workflows and the vocabulary

- **WF-1 Onboard and set a target** — age gate → consents → profile → optional safety screen → resting energy → maintenance → target and macros (floor policy) → activity mode → approve. Tracking works before a target exists (brief §3.2).
- **WF-2 Define a Unit** — simple, composite (bread rules), or Recipe with weighed ingredients and **weigh-the-pot** cooked yield; by typing, scale photo or label photo; approve a version.
- **WF-3 Log what I ate** — tap a recent Unit, **copy a meal or day**, log a **Template**, speak or type, **Siri or a widget**; one-tap with Undo; offline outbox; meal and day report; written to Apple Health.
- **WF-4 Capture and analyse** — photo / label / scale / voice / **photo + words** → draft → ≤2 questions → resolve → review. An input path into WF-2, WF-3 and WF-5.
- **WF-5 Plan a meal and confirm it** — available foods + constraints → solver → Plan → Ate as planned / Change / Not eaten.
- **WF-6 Correct history** — correct, void, restore, move day; scope this entry or future default.
- **WF-7 Activity** — HealthKit and manual exercise → dedupe → activity mode → budget.
- **WF-8 Reports and progress** — meal and day report; 7/28/custom periods; coverage; weight trend; target history.
- **WF-9 Privacy** — consents, export, delete account.
- **WF-10 Govern the reference and the AI** — approve food and recipe records and aliases, version policy, roll models and config, just-in-time access: support requests a Grant → the eater approves or declines it in Settings → support reads within its time box → it expires, every read audited.

Vocabulary — one name per thing; the screen, the code and the logs use these words: **Unit** · **Composite** · **Recipe** · **Food** (reference record) · **Alias** (with dialect: EG, Gulf, MSA) · **Template** (a saved meal) · **Entry** · **Day** · **Target** · **Plan** · **Analysis** · **Evidence** (label-verified · recipe-calculated · measured · estimated analogue · user-defined) · **Correction** · **Void** · **Restore** · **Pending** · **Activity** · **Consent** · **Policy** · **Grant** (just-in-time access). Tabs: **Today · Capture & Plan · My Units · Progress**. Arabic labels are fixed once in the string catalogue and never vary between screens.

### 5 · Done-when per workflow

- WF-1: a new adult finishes onboarding in English or Arabic, sees resting energy (±10 % note), maintenance, a target not below the policy floor, macro grams and assumptions, approves; Today shows the target and the activity mode. A safety-screen answer of pregnancy gives tracking-only mode with neutral wording. Under 18: no account.
- WF-2: "cheese bite" saved as 5.4 g cheese + 1.5 g oil + 8 g bread; a Recipe saved with a weighed cooked yield gives AT-06's numbers; My Units lists them; Today's total unchanged.
- WF-3: a recent Unit logs in ≤2 taps from Today; "copy yesterday's breakfast" logs one meal; meal report and day report appear and reconcile; Undo removes exactly one entry; offline logs sync once; the entry appears in Apple Health and disappears on void.
- WF-4: a plate photo + "fried in ghee" returns editable chips with evidence badges and a range; nothing is consumed until approved; Arabic voice "١٨ مش ١٥" becomes a correction.
- WF-5: cap 500 kcal + carbs ≤30 % → counts satisfying unrounded constraints, or "infeasible" naming the blocking constraint; "Ate as planned" records exactly one meal.
- WF-6: "18 not 15" shows old, new and delta and replaces the effective entry; yesterday's correction leaves today untouched.
- WF-7: one workout from Health + a matching manual entry → one contribution; in fixed mode the food target does not grow.
- WF-8: the day report shows target, consumed, remaining, shares summing to 100.0 %, coverage; a week with 2 missing days shows coverage, not zeros.
- WF-9: export downloads entries, units, recipes, targets and consents; delete account removes private data and media within the policy window and leaves a completion record without identifiers.
- WF-10: an approver approves a Tier B recipe record (e.g. فول مدمس) with its evidence and licence; the eater's resolver uses it; an admin rolls a model version back and manual logging keeps working; support requests a Grant, the eater approves it in Settings, support reads the diary inside the time box, the Grant expires and every read shows in the auditor's trail; a declined Grant gives no access.

### 6 · The what-else pass (configuration points, never future code branches)

- **User settings:** language (en/ar), numerals (Arabic-Indic/Western), dialect (EG/Gulf/MSA — drives لبن/laban resolution, F27), display units (g/oz, kg/lb, kcal/kJ), diary-day boundary, activity mode, one-tap logging on/off, "hide numbers" view (R37), Health write on/off.
- **User rules:** accompaniment (bread per dipped bite, exceptions), preparation defaults (tea milk, laban unsweetened), with precedence: this entry > named variant > household default.
- **Policy (approver-versioned):** calorie floor 1,200 kcal, hard stop 1,000 kcal, deficit cap (smaller of 15 % and 500–750 kcal), gain +10 %, GLP-1 protein-first 1.2–1.6 g/kg, tracking-only triggers, energy-mismatch threshold (>10 % and >10 kcal), component-sum tolerance, planner increments, clarification limit 2, retention (raw scans 30 days, audio 24 h).
- **Registry (admin-versioned):** model ids per task, prompt and schema versions, rollout stage, kill switch, per-user daily AI quotas.
- **Reference (approver-curated):** foods with evidence + licence, Tier B recipes, aliases with dialect.
- **Fixed vocabulary:** the evidence badge set and the units' kinds (C1 lets admins configure values, not invent concepts).

### 7 · Open questions — research could not close them; none blocks the build

| question | why it matters | owner, when |
|---|---|---|
| Data residency (R18–R20, R26, R28) — no Gemini model runs in any Middle East region; 3.8 Flash runs only on `global` and the US/EU multi-regions, and regional endpoints do not guarantee in-region processing (P11 as corrected in r1-refute-b) | every AI request about a Saudi or Egyptian eater leaves the country; SDAIA adequacy list status unconfirmed | owner + counsel, before hosting is chosen (ship rows are dropped now) |
| Licences from SFDA and NNI (F12–F17, F19) | turns cross-checks into authoritative local numbers | owner, any time; records upgrade by a delta |
| Egypt PDPC licence for sensitive data before an Egyptian launch (R28, R29) | legal launch gate, window closes ~1 Nov 2026 | owner, before launch |
| Arabic voice: the Gemini transcription model lists only Egyptian Arabic (ar-EG), preview, `global` only; Gulf Arabic quality unverified; Siri phrases in Arabic unverified (P15, P22 as checked in r1-refute-b) | Saudi eaters' voice logging | measured in the AI evaluation set (brief NFR-10) before launch; typed and tapped logging never depend on it |
| Free vs paid split (brief §23.3, R9, C7, C21) | the loudest complaints are paywalled basics | owner, after MVP |
| Blocked research hosts (USDA FDC, Open Food Facts, Apple/Google docs, app stores) | many findings were `assumption` | **closed 2026-10-01**: he widened network access; the refuters re-opened the sources |

## §2 The blueprint

(written at Plan to the end)

## §3 The yes, the dated deltas, the production go

- **2026-10-01 · delta D1 · size line crossed at the final map.** The final map has 10 workflows (floor: product ≤ 8), 5 human personas and 3 external integrations, so §0 line 2 becomes **platform**. Impact: the whole floor and the whole of `care.md` on every screen (unchanged from product), plus the declared enterprise obligations as ordinary slices with acceptance ids — data policy (consents, retention, deletion, residency record), supply chain (dependency audit, pinned versions, SBOM), migrations (versioned schema with forward migrations, rehearsed on a copy of seeded data) and trust (audit trail, just-in-time access); a backup restored once in the release proof (on the Firestore emulator while no hosting exists). Ship-side platform extras (DR failover, migration rehearsal on live data, compliance evidence) stay dropped with the ship rows (§0 line 10). No banked work is touched — nothing is built yet.
- **2026-10-01 · delta D2 · one vocabulary for roles, states, errors and places** — `way/vocabulary.md` extends §1 ¶4. Reason: four lens verifiers found the same state named differently across lenses (e.g. Grant Lapsed/Expired, two error codes for one refusal). Impact: every lens fix round and every later contract uses it; no banked work exists.
