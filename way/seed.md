# Seed — the one shared synthetic fixture set (2026-10-01)

Every story and every test of Sips & Bytes uses this file and nothing else for its starting data (`way/join.md` J52). It reconciles the fixtures of all nine lens files; where a lens gave another value, `join.md` names the decision and the superseded line. **Everything here is synthetic**: no record describes a real person, and every name, email, id, code and diary is invented for testing (the repository is public). Nutrient values are calibration fixtures in the FRD's sense (§5.2, §21: "synthetic or explicitly supplied for testing … not automatic nutrition defaults"), except the few rows marked **(FDC, as published)**, which copy public CC0 USDA FoodData Central values that a lens quotes.

**Arithmetic.** Values are exact decimals; where a division does not end, the fraction is shown in brackets and stored as an exact rational (the nutrition core keeps unrounded values; FRD §10.2 "round only the final displayed fields"). "4/4/9" is 4 × protein + 4 × carbohydrate + 9 × fat. Masses are grams unless "ml" is written. P / C / F = protein / carbohydrate / fat, in grams.

---

## §0 · How the seed is loaded

1. **One file, one timeline.** Every record carries a time (UTC). A story declares its **start clock**; the loader writes every record whose time is at or before it (`POST /v1/test/seed {at}` in a test build), sets the clock (`PUT /v1/test/clock`), and the story runs. Moving the clock forward inside a story never loads later seed records (J52).
2. **Givens add, never contradict.** A story's Given may add records only through the public API or the test endpoints (J51), on top of what was loaded. A Given that contradicts a loaded record is a defect of the story.
3. **Live events continue the trail.** The seeded Audit trail (§11) holds 266 events; an event a story causes is numbered after the last event loaded at its start clock.
4. **Adapters run as mocks** until the owner's cutover (blueprint §0 line 6): Gemini (scripted outputs per story), USDA FoodData Central (serves §8.1, with releases 15.4 and 15.5, and the import of §10.7), HealthKit (the simulator's Health store, seeded per §10.5), Sign in with Apple, the processor-notice endpoint. Every mock keeps a request log readable by tests.
5. **Separate datasets** (§13) are loaded only by the stories that name them.

---

## §1 · Id formats

| thing | format | example |
|---|---|---|
| eater account | `acct_` + 6 lowercase hex | `acct_9c41e2` |
| anonymous session | `acct_anon_` + 4 hex | `acct_anon_71f2` |
| staff account | `staff_<name>`; sign-in email `<name>@staff.example.test` | `staff_mona` |
| Unit / Composite / Recipe | `unit_<owner>_<name>`; a version is `uv_<…>_v<n>` | `uv_mona_cheese_bite_v1` |
| Food version (reference) | `food_<name>_v<n>`; Tier A rows add their FDC id | `food_fdc_321358_v1` |
| Tier B recipe record | `rec_<name>_<dialect>` + version | `rec_fm_eg` v1 |
| Alias | `al_<name>` | `al_laban_gulf` |
| Entry · command · Analysis · Plan · Activity | `en_` · `cmd_` · `an_` · `plan_` · `act_` + 4 hex | `en_9921` |
| Day | `diary_day_id` = the Day's local date | `2026-09-30` |
| Target version | `tv_<owner>_<n>` | `tv_mona_2` |
| Grant | `grant_` + 4 hex | `grant_31f0` |
| Privacy job | `job_exp_` (export) · `job_del_` (deletion) · `job_cw_` (Consent withdrawal) + digits | `job_del_2205` |
| deletion reference | `DEL-yy-mmdd-XXXX` | `DEL-26-0905-P7T2` |
| support code | `SB-XXXX-XXXX` (alphabet without 0, O, 1, I) | `SB-7KQ2-94XM` |
| case reference (helpdesk ticket) | `CASE-nnnn` | `CASE-1182` |
| Registry version | `<task>@v<n>` | `meal@v6` |
| Label submission · flag | `L-nn` · `F-nn` | `L-17` |
| request received outside the app | `oreq_` + 4 hex | `oreq_7e01` |

---

## §2 · The clock

**Deployment** (trail start): **2026-08-01T06:00:00Z**. Policy v1 in effect from 2026-08-02T00:00:00+03:00 (2026-08-01T21:00:00Z).

**Time zones on the fixture dates** (IANA): Asia/Riyadh UTC+3 all year · Africa/Cairo UTC+3 until 2026-10-29 24:00, UTC+2 after · Europe/London and Europe/Dublin UTC+1 until 2026-10-25 01:00 UTC, UTC+0 after.

**Default start clocks** (a story that names no other clock uses its lens's):

| lens | default start clock | what it sees |
|---|---|---|
| eater (all files) | 2026-10-01T09:00:00Z | Policy v1 In effect; `meal@v6` in Rollout; every 30 Sep Day past its boundary; Thu 1 Oct Provisional |
| support | 2026-10-01T10:03:00Z | E1 with no Grant yet (`grant_31f0` is requested at 10:05) |
| platform admin | 2026-10-01T09:00:00Z | both Platform admins; failed jobs as in §10 |
| nutrition approver | 2026-09-29T07:00:00Z | Policy v1 In effect, no v2 yet; two Nutrition approvers; 3 Label submissions and 9 open flags (§8.6) |
| auditor | 2026-10-05T09:00:00Z | the whole seed (events 1–266); console zone Asia/Riyadh |

**Stories that start elsewhere** (the story names its clock; these are the ones the join fixed):

| story | start clock | why |
|---|---|---|
| admin-10.1 | 2026-09-26T12:00:00Z | `meal@v7` in Canary at 5 % (events 143–146) |
| admin-10.8 – 10.30, 10.71 (the Meal version 7 walk) | 2026-09-21T12:00:00Z | before `meal@v7` exists; the admin proposes it |
| admin-10.64 (the only Platform admin) | 2026-09-29T12:00:00Z | `staff_badr` is assigned at 2026-09-30T09:00:00Z |
| approver-10.57 `/s` (one holder) | 2026-09-08T09:00:00Z | before `staff_yara` holds the role |
| support-10.23 (G1 end to end) | 2026-10-01T10:04:00Z | G1's request at 10:05 is the story's own act |
| support-9.11 | 2026-10-01T11:00:00Z | before the 11:05 escalation |
| e36 eater-3.35, e578 eater-8.1 | 2026-10-02T11:29:00Z (12:29 London) | Sam's lunch at 12:30 is the story's act |
| e578 eater-7.16, 8.2, 8.7 | 2026-10-02T13:00:00Z | Sam's Day at 1,200 kcal |
| e578 eater-5.5 – 5.29 (Faisal's kabsa) | 2026-10-01T18:30:00Z (21:30 Riyadh) | Faisal's Day at 1,400 kcal |
| e578 eater-8.12 | 2026-09-27T09:00:00Z | the week 2026-09-20 → 26 has ended |
| e578 eater-7.1 (first Health access) | 2026-09-08T09:05:00Z | Sam has an account and no Health Consent yet |
| e578 eater-8.22 (Sam's line) | 2026-09-30T12:00:00Z | no Apple Health weight since 24 Sept (the 2026-10-01 sample is not loaded yet) |

---

## §3 · Roles, permissions and staff accounts

**Permissions** (fixed; plain sentences on Roles › Permissions; admin-10.58 plus J40): Read the Registry · Propose Registry versions · Move versions between states · Roll back · Use the kill switch · Change quotas · Change prices · Read Metrics · Read failed jobs · Retry failed jobs · Escalate jobs · Read roles · Change roles · Read the Audit trail for Registry, Roles and Jobs · Read the whole Audit trail · Add a review note · View consented evaluation cases · Read reference (Foods, Recipes, Aliases, Policy) · Approve Foods, Recipes and Aliases · Propose and approve Policy versions · Read account state · Request a Grant · Read Grants (all) · Change Grant settings · Record requests received outside the app · Sign launch gates · Publish wording. "Read a diary inside an Active Grant" is not a permission (Grant only).

**Seeded roles** (label "Seeded · read-only"):

| role | permissions |
|---|---|
| Eater | none in the console; given to every app account at sign-up |
| Nutrition approver | Read reference · Approve Foods, Recipes and Aliases · Propose and approve Policy versions · Read Metrics · Sign launch gates (nutrition-policy review) · Publish wording (`guidance-1`, the tracking-only guidance; approver-10.53) |
| Support agent | Read account state · Read failed jobs · Retry failed jobs (one export retry per job) · Escalate jobs · Request a Grant · Record requests received outside the app · Read the Registry (status bar only) |
| Platform admin | Read the Registry · Propose Registry versions · Move versions between states · Roll back · Use the kill switch · Change quotas · Change prices · Read Metrics · Read failed jobs · Retry failed jobs · Read roles · Change roles · Read the Audit trail for Registry, Roles and Jobs · Change Grant settings · Sign launch gates (privacy review, on the owner's word) · Publish wording (consent texts, `grant-req-1`; after the privacy review is signed) |
| Auditor | Read the whole Audit trail · Read Grants (all) · Read reference · Read the Registry · Read roles · Read failed jobs · Add a review note |

**Staff accounts** (every one signs in with a password and a 6-digit authenticator code; console languages and zones as listed):

| id | display name | role(s), from → to | console | zone | lens names |
|---|---|---|---|---|---|
| `staff_ali` | Ali N. | Platform admin from 2026-08-01T06:00:00Z (first platform admin, from the deployment's environment) | English | Africa/Cairo | `admin.a@example.test` |
| `staff_badr` | Badr H. | Platform admin from 2026-09-30T09:00:00Z | English | Asia/Riyadh | `admin.b@example.test` |
| `staff_mona` | Mona K. | Support agent from 2026-08-01T06:05:00Z | English | Europe/Dublin | `support.a@example.test` |
| `staff_omar` | Omar S. | Support agent from 2026-08-01T06:06:00Z | Arabic | Asia/Riyadh | — |
| `staff_lee` | Lee T. | Support agent from 2026-08-01T06:09:00Z; no Grants | English | Europe/Dublin | — |
| `staff_tariq` | Tariq B. | Support agent 2026-09-05T06:00:00Z → 2026-09-25T15:00:00Z | English | Asia/Riyadh | — |
| `staff_dina` | Dina R. | Nutrition approver from 2026-08-01T06:07:00Z | English | Africa/Cairo | `approver.a@example.test`, "approver A", "Mona Adel" (approver-10.62) |
| `staff_yara` | Yara M. | Nutrition approver from 2026-09-10T06:00:00Z | Arabic | Asia/Riyadh | "approver B" |
| `staff_hana` | Hana Q. | Auditor from 2026-08-01T06:08:00Z | English | Asia/Riyadh | `auditor.a@example.test` |
| `staff_rana` | Rana F. | custom role **Access manager** (Read roles, Change roles) from 2026-09-15T08:05:00Z | English | Africa/Cairo | `access.a@example.test` |
| `staff_new` | — | no role | English | Europe/London | `new.user@example.test` |
| `staff_sod_seed` | — | Support agent **and** Platform admin, written straight into the role store at 2026-09-30T12:00:00Z below the roles API (no event); exists only for the detective Anomalies rules (J41) | English | UTC | — |

---

## §4 · Configuration

### §4.1 · Policy v1 (In effect from 2026-08-02T00:00:00+03:00; proposed and approved by `staff_dina`, the sole Nutrition approver then)

| value | v1 | owner note |
|---|---|---|
| calorie floor | 1,200 kcal | product policy (r1-refute-b Dropped 12) |
| hard stop | 1,000 kcal; code minimum 1,000 (J98) | R32 |
| maintain | 0 % | FRD §3.3 |
| loss default · choices | 15 % · 5 %, 10 %, 15 % | FRD §3.3; approver-10.68 |
| gain default · choices | +10 % · 5 %, 10 % | FRD §3.3 |
| deficit cap | the smaller of 15 % and 500 kcal | R33 (500 is the approver's v1 value) |
| activity multiplier | × 1.2 (one level in v1) | FRD §11.3 |
| Activity-adjusted credit | factor 50 %, cap 300 kcal a day — the default and the upper bound; eligible: deduplicated workouts and confirmed manual Activity (J103, J135) | FRD §12.2 |
| default macro split | protein 30 % · carbohydrate 40 % · fat 30 % | EA5 |
| Target review | 14 days after approval | EA6 |
| GLP-1 | protein 1.2–1.6 g/kg; no added deficit | R35, R41 |
| tracking-only triggers | SCOFF ≥ 2 (cut-off 2); pregnancy; breastfeeding (locked) | R38, R32 |
| limits withheld in tracking-only | Calorie aim, Calorie ceiling, Carbohydrate maximum (Protein minimum kept) | J110 |
| tracking-only guidance | wording `guidance-1` (English and Arabic; §4.7) | approver-10.53 |
| energy mismatch | > 10 % **and** > 10 kcal per actual serving, measured against the source energy | FRD §10.2 |
| component-sum tolerance | the larger of 2 % of the measured total and the scale's step (J74) | FR-023 |
| planner increments | whole by default; halves only when the eater enables them | FR-051 |
| clarification limit | 2 questions per Analysis (J127) | FR-035 |
| carbohydrate labels (optional, eater setting) | Low < 26 % · Medium 26–50 % · High > 50 % of macro-derived energy | FRD §10.3 |
| Suggested Target (P1) | at least 14 days, 10 Complete Days and 4 weights; at most ±100 kcal a day per 14-day review; never below the floor | FR-060, FR-061 |
| retention | raw scans 30 days unless saved; temporary audio 24 h | FR-078 |

### §4.2 · Policy v2

Proposed by `staff_yara` 2026-09-29T07:30:00Z; approved by `staff_dina` 08:00:00Z with the reason "label reviewers asked for fewer flags on rounded labels"; In effect from **2026-10-05T00:00:00+03:00** (2026-10-04T21:00:00Z). One change: energy mismatch **> 12 %** and > 10 kcal. Every other value equals v1.

### §4.3 · Registry

| task | name | Rollout version (from 2026-08-01T08:0n:00Z) | model | prompt version | schema version | location | kill switch on 2026-10-01T09:00Z |
|---|---|---|---|---|---|---|---|
| `meal` | Meal | `meal@v6` | `gemini-3.8-flash` | 11 | 3 | `global` | Off (On 09:10–09:55 that day) |
| `label` | Label | `label@v3` | `gemini-3.8-flash` | 7 | 3 | `global` | Off |
| `scale` | Scale | `scale@v2` | `gemini-3.8-flash` | 4 | 3 | `global` | Off |
| `ingredients` | Ingredients | `ingredients@v2` | `gemini-3.8-flash` | 5 | 3 | `global` | Off |
| `text` | Text | `text@v4` | `gemini-3.5-flash-lite` | 9 | 3 | `global` | Off |
| `voice` | Voice | `voice@v1` | `gemini-3.8-flash` (audio input, GA) | 3 | 3 | `global` | Off |
| `explain` | Explain | `explain@v1` | `gemini-3.5-flash-lite` | 1 | 1 (`explanation` family) | `global` | Off |

**`meal@v7`**: `gemini-3.8-flash`, prompt version 12 ("asks about oil and ghee"), schema version 3. Proposed 2026-09-22T08:00Z → Shadow 09-23T08:00Z → Canary 5 % 09-25T08:00Z → Rollout 100 % 09-27T08:00Z (`meal@v6` → Replaced) → Rolled back 09-27T11:30Z (`meal@v6` → Rollout). Kill switch `meal` On 2026-09-27T10:12Z → Off 10:47Z (35 min).
**Models list**: `gemini-3.8-flash` (stable; retirement: none announced; short-term model, at least 45 days' notice), `gemini-3.5-flash-lite` (stable), `gemini-3.5-transcribe` (preview, `global`, ar-EG only: Shadow only, J93). No floating alias anywhere.
**Regression set**: 0 consented cases at deployment; cases are added only from eaters with `research` Given (none in the default seed after 2026-09-22, when E1 withdrew it).

### §4.4 · Quotas version 1 (seeded 2026-08-01T08:10Z; "Quotas version 1 in use")

| who | image tasks (Meal, Label, Scale, Ingredients) | Text, Voice, Explain |
|---|---|---|
| signed-in eater | soft 15 · hard 25 per diary day | soft 60 · hard 100 per diary day |
| anonymous session | hard 3 per day | hard 10 per day |

The quota day is the eater's diary day (J91). A repeat log of a Saved Unit, a copy or a Template never counts.

### §4.5 · Prices and the AI spend cap (Metrics › prices panel; each row "Seeded")

| model | input $ / 1 M tokens | output $ / 1 M tokens (thinking included) | effective |
|---|---|---|---|
| `gemini-3.8-flash` | 0.75 | 3.75 | from seeding (2026-08-01) through 2026-12-31 |
| `gemini-3.8-flash` | 1.50 | 7.50 | from 2027-01-01 |
| `gemini-3.5-flash-lite` | 0.30 | 2.50 | from seeding (2026-08-01) |

AI spend: alert level $40, cap $60 per UTC day. Cost check: 1,000 input + 500 output tokens on `gemini-3.8-flash` = (1,000 × 0.75 + 500 × 3.75) ÷ 1,000,000 = $0.002625 in 2026 and $0.00525 from 2027-01-01.

### §4.6 · Grant settings version 1 (seeded 2026-08-01T08:15Z; Settings › Grant settings)

durations 1 h (default) · 4 h · 24 h · at most 14 Days per Grant, none after the eater's current Day · request window 72 h → Unanswered · one Requested Grant per eater · no extension, no edit (405) · the eater answers online only · console idle sign-out 15 min, warning at 13 min · email look-up ≤ 30 per agent per hour, case reference required · 5 failed staff sign-ins in 15 min lock the account for 15 min · Grant bar warnings at 10 and 2 minutes · the Grant panel watermark "<staff id> · <grant id>".

### §4.7 · Wording (versioned texts; English and Arabic each)

| key | version | published | used for |
|---|---|---|---|
| `age-1` | 1 | 2026-08-01 | the age confirmation |
| `diary-1` | 1 | 2026-08-01 | Diary processing |
| `c-ai-3` | 3 | 2026-07-25 (before the trail) | AI Consent until 2026-09-24 |
| `c-ai-4` | 4 | 2026-09-24T08:00Z (event 145, after the privacy review, event 144) | AI Consent; adds the unkept Shadow-copy sentence (J27) |
| `health-1` | 1 | 2026-08-01 | the four Health purposes |
| `mic-1` · `photos-1` | 1 | 2026-08-01 | Microphone · Photos |
| `research-1` | 1 | 2026-08-01 | Optional research |
| `label-1` | 1 | 2026-08-01 | Send my label photos for review |
| `grant-req-1` | 1 | 2026-08-01 | the Grant request card the eater reads |
| `guidance-1` | 1 | 2026-08-01 | tracking-only guidance |
| `c-ai-2` | — | absent | exists only in separate fixture X (§13) |

The Arabic of `c-ai-3` begins «أوافق على إرسال الصور والصوت والنص إلى Google ‏(Gemini) لتحليل وجباتي» (synthetic wording). Reason catalogue (`grant-req-1`): `sync_missing_entry` "An Entry is missing or appears twice" «إدخال مفقود أو ظاهر مرتين» · `report_mismatch` "A day report total looks wrong" «إجمالي تقرير اليوم يبدو غير صحيح» · `unit_calculation` "A Unit or Recipe calculates unexpectedly" «وحدة أو وصفة تُحسب بشكل غير متوقع» · `activity_import` "Imported Activity looks wrong" «النشاط المستورد يبدو غير صحيح» · `other` (the agent's one sentence).

**Launch gates on 2026-10-01T09:00Z** (Settings › launch gates): AI provider credential set, loaded 2026-10-01T05:02:00Z · dependency audit "no known vulnerabilities · checked 1 Oct 2026" · "No restore test for this build yet" · privacy review of `c-ai-4` signed 2026-09-24 by counsel R. Haddad (synthetic) · nutrition-policy review: not yet signed (approver-10.62 signs it) · provider data settings recorded 2026-09-01, next check due 2026-11-30 · launch dishes "Approved 12 of 50" (§8.3).

---

## §5 · The named eaters (the eater lens's people, one record each — J54)

### §5.1 · Accounts and settings

| fixture name(s) | account | created (UTC) · sign-in | language · digits · dialect | zone · diary-day boundary | Consents Given (others are Not given) | other settings |
|---|---|---|---|---|---|---|
| **Mona** (`eater-synth-mona`; research "Mona, Cairo") | `acct_e9a001` | 2026-08-20T05:00:00Z · email `mona.synthetic@example.test` | Arabic · Arabic-Indic · EG | Africa/Cairo · 03:00 | Diary processing, Photos, AI (`c-ai-3`), Send my label photos for review (2026-09-26) | one-tap logging **On**; units g · kg · kcal; Hide numbers off; no Apple Health; first day of week Saturday |
| **Faisal** (`eater-synth-faisal`; e19 O3; research "Faisal, Riyadh") | `acct_e9a002` | 2026-08-25T15:00:00Z · Sign in with Apple | Arabic · Western · Gulf | Asia/Riyadh · 03:00; "Ramadan days" on from 2027-02-07T20:00:00Z (boundary 12:00) to 2027-03-10T11:00:00Z (14:00 Riyadh) | Diary processing, Photos, AI (`c-ai-3`), Microphone, Health: read workouts, read active energy, write food, label review (2026-09-28) | one-tap off; Food rules "Laban: unsweetened", "Ghee: 3 g per fried egg"; safety mode **protein-first** (GLP-1 "Yes", 2026-08-25) |
| **Sam** (`eater-synth-sam`; e19 O2; research "Sam, London") | `acct_e9a003` | 2026-09-08T09:00:00Z · email `sam.synthetic@example.test` | English · Western · not set | Europe/London · 00:00 | Diary processing, Photos, AI (`c-ai-3`), Microphone, Health: read workouts, read active energy, write food (2026-09-08T09:30Z), read body mass (2026-09-16), label review (2026-09-27) | one-tap **On**; weight shown in lb; Fixed mode |
| **Huda** (`eater-synth-huda`; e19 O4) | `acct_e9a004` | 2026-10-01T05:00:00Z · email `huda.synthetic@example.test` | English · Western · not set | Africa/Cairo · 00:00 | Diary processing | no Units, no Food rules, no Target yet; O4's inputs are the ones she types in onboarding |
| **Nadia** | `acct_e9a005` | 2026-09-30T10:00:00Z · email | English · Western | Europe/London · 00:00 | Diary processing | fresh; no Units |
| **Khalid** | `acct_e9a006` | 2026-09-30T11:00:00Z · Sign in with Apple | Arabic · Western · Gulf | Asia/Riyadh · 00:00 | Diary processing | fresh; no Units |
| **Hala** (e19 O1, trial **T1**) | none: local trial on her iPhone | — | Arabic · Arabic-Indic · EG (region Egypt) | Africa/Cairo · 00:00 | none on a server; on the device: age confirmation and the choices she makes in-story | trial diary T1 (§7.4, §12.6); 16 local commands |
| **Amal** (e19 O5) | none: fresh install | — | Arabic · Western · Gulf (region Saudi Arabia) | Asia/Riyadh | — | input profile only (§5.3) |

### §5.2 · Targets (Target versions; `source` per J106)

| eater | version | kcal | effective | source and method (row details) | macro targets |
|---|---|---|---|---|---|
| Mona | `tv_mona_1` | 1,870 | 2026-08-20 → 2026-09-14 | Estimated by the app: resting energy 1,500 (Mifflin–St Jeor, 40 y, 164 cm, 83.6 kg, "−161": 836 + 1,025 − 200 − 161) × 1.2 + planned exercise 400 = maintenance 2,200; −15 % = 1,870 (deficit 330 = the smaller of 15 % (330) and 500) · Activity × 1.2 · Policy v1 · review 2026-09-03 | 30/40/30 → P 140.25 g, C 187.0 g, F 62.333… g (187/3) |
| Mona | `tv_mona_2` | 1,750 | from 2026-09-15 | Entered by you · Policy v1 · review 2026-09-29 | 30/40/30 → P 131.25 g, C 175.0 g, F 58.333… g (175/3) |
| Faisal | `tv_faisal_1` | 2,040 | from 2026-08-25 | Estimated by the app: resting energy 1,700 (41 y, 176 cm, 80 kg, "+5": 800 + 1,100 − 205 + 5) × 1.2 = 2,040; GLP-1 → no added deficit · Policy v1 | 30/40/30 → P 153 g, C 204 g, F 68 g; protein-first floor 96 g (1.2 g/kg × 80) |
| Sam | `tv_sam_1` | 1,870 | from 2026-09-20 | Entered by you: measured resting energy 1,779 (clinic, 2026-09-20) × 1.2 + planned exercise 200 = 2,334.8; his own −20 % = 1,867.84 → approved 1,870; `over_deficit_cap: true` · Fixed mode | 25/30/45 → P 116.875 g, C 140.25 g, F 93.5 g |
| E1 `acct_9c41e2` | `tv_e1_1` | 1,330 | from 2026-08-03 | Estimated by the app: resting energy 1,301.5 (29 y, 158 cm, 62 kg, "−161": 620 + 987.5 − 145 − 161) × 1.2 = 1,561.8; −15 % = 1,327.53 → 1,330 | 30/40/30 |

Huda, Nadia, Khalid and E4 (tracking-only) have no Target. Hala's trial has none.

### §5.3 · Profiles and weights

- **Onboarding input profiles** (e19 fixtures; typed in the story, not stored in the seed): Hala O1 34 y, 160 cm, 78 kg, "−161", no planned exercise, goal lose (resting 1,449; maintenance 1,738.8; lose 15 % → 1,477.98 → **1,480**); Huda O4 55 y, 156 cm, 60 kg, "−161" (resting 1,139; maintenance 1,366.8; 15 % → 1,161.78 is below the floor → **1,200**); Amal O5 68 y, 152 cm, 52 kg, "−161" (resting 969; maintenance 1,162.8; no Lose; Maintain at the floor **1,200**); Sam O2 weight typed "185 lb" = 83.91458845 kg (185 × 0.45359237, stored unrounded, shown 83.9 kg); Faisal O3 as `tv_faisal_1`.
- **Typed in a story, not stored in the seed** (the FRD fixtures these stories reproduce; every value recomputed with exact fractions):
  - **AT-01** (e24 eater-2.13): Sam's new small biscuit on Biscuits, plain (synthetic brand, §8.2); 7 pieces weigh 71.7 g after tare; single weights 9.8 · 10.1 · 10.6 · 10.0 · 10.4 · 10.3 · 10.5 g (sum 71.7); mean 717/70 g = 10.242857… g stored, shown 10.24 g; count 7 kept; one piece = 717/14 kcal = 51.2142… kcal (500 kcal per 100 g).
  - **AT-03** (e24 eater-2.17): Mona's mixed peas spoon = Rice, cooked 15.1 g (19.63 kcal; P 0.4077, C 4.2582, F 0.0453) + Peas with sauce 14.4 g (12.96; 0.648, 1.584, 0.4608) + Beef, cooked 8.6 g (21.5; 2.236, 0, 1.3244) → total mass 38.1 g; 54.09 kcal, P 3.2917, C 5.8422, F 1.8305 (each part counted once). The three Foods are in §8.1 and §8.2.
  - **AT-09** (e19 eater-1.35, Hala's Onboarding · Macros at 1,480 kcal): fat 46 %, carbohydrate 32 %, protein 24 % → total 102 %; normalised 46/102 = 45.0980…, 32/102 = 31.3725…, 24/102 = 23.5294… %, shown by largest remainder at two decimals **45.10 · 31.37 · 23.53** (sum 100.00); grams at 1,480 kcal: fat 34,040/459 = 74.16… → 74 g, carbohydrate 5,920/51 = 116.08… → 116 g, protein 1,480/17 = 87.06… → 87 g.
- **Sam's weights**: 84.2 kg (2026-09-10, by hand) · 83.9 (09-17, Apple Health) · 84.1 (09-24, Apple Health) · 83.5 (10-01, Apple Health).
- **Mona's weights** (no Health): 83.7 kg (2026-09-05) · 83.6 (09-12) · 83.6 (09-19) · 83.5 (09-26), all by hand.
- **E4's weights**: 64.2 kg (2026-09-20) · 64.5 (09-27), by hand.

---

## §6 · Other accounts

### §6.1 · The support lens's eaters (E1–E14) and the eater lens's SE names

| support · eater lens | account | facts |
|---|---|---|
| E1 · SE1 | `acct_9c41e2` | created 2026-08-03T18:20Z; Sign in with Apple, relay `r7k2q9x4@privaterelay.appleid.com` (shown masked `r•••@privaterelay.appleid.com`); Arabic, Arabic-Indic, Gulf; Asia/Riyadh; boundary 04:00; devices: iPhone app 1.0.3 on iOS 26.1 (last sync 2026-10-01T10:02Z) and iPhone app 1.0.2 (last sync 2026-09-30T19:15Z, the sync that carried `cmd_7a1e`); support codes `SB-7KQ2-94XM` issued 2026-10-01T06:12Z valid to 2026-10-02T06:12Z, `SB-3MRT-7WQD` issued 2026-09-29T06:12Z expired 2026-09-30T06:12Z; Consents per §11 events 26–33, 130, 141, 148 (on 2026-10-01: Diary, Photos, AI `c-ai-4`, all four Health, research Withdrawn, Microphone and label review Not given); photo analyses on 2026-10-01: 3 of 25; Units §7.5; diary §12.3; Sync and Activity §10.3–§10.4; Grants §9 |
| E2 · SE2 | `acct_51ab07` | created 2026-08-10; email sign-in; English; Africa/Cairo; deletion `job_del_2215` · `DEL-26-0915-K3Q8` requested 2026-09-15T07:00Z (10:00 Cairo), due by 2026-10-15, Running (§10.1) |
| E3 · SE3 | `acct_e07d13` | created 2026-08-12; English; Africa/Cairo; export `job_exp_4410` Failed (§10.1) |
| E4 | `acct_77d2c0` | created 2026-08-14; English; Asia/Riyadh; boundary 00:00; safety mode **tracking-only** (pregnancy answer, 2026-08-14); weights §5.3; Grant `grant_7d01` (§9) |
| E5 | `acct_b6f204` | created 2026-08-16; English; Africa/Cairo; boundary 00:00; Analysis `an_5530` Failed; 25 of 25 photo analyses used on 2026-10-01, the 26th refused `RATE_LIMITED` at 16:40Z (§10.2) |
| E6 · SE6 | `acct_c2a917` | created 2026-09-03; Arabic; Asia/Riyadh; AI Consent Withdrawn 2026-09-30T18:14Z (`c-ai-3`, in the app); before it: 2 queued uploads, 3 cached private analyses, 4 raw photos, 1 audio clip, 1 prepared export; Consent-withdrawal job `job_cw_1814` Completed 18:20Z (§10.1); Grant `grant_a1d4` |
| E7 · SE7 | `acct_a41c55` | created 2026-08-18; email `karim.synthetic@example.com`; English; Africa/Cairo; exports `job_exp_31`, `job_exp_77`, `job_exp_88` (§10.1); email to support received 2026-10-01T09:30Z (in-story record); no failed Analyses, conflicts or duplicates in 30 days; no Activity import in 7 days; Grant `grant_9b30` |
| E8 · SE8 | `acct_88e0c1` (deleted) | created 2026-08-05; deletion `job_del_2120` · `DEL-26-0820-M2V5` requested 2026-08-20T08:00Z, Completed 2026-09-18T10:00Z; the former email `lina.synthetic@example.com` was destroyed with the account — no account for it exists in the seed (J62) |
| E9 | `acct_9a07e5` | created 2026-08-07; English; Africa/Cairo; deletion `job_del_2205` · `DEL-26-0905-P7T2` (§10.1) |
| E10 · SE10 | `acct_f1e0c3` | created 2026-08-09; English; Africa/Cairo; boundary 00:00; Grants `grant_40ab`, `grant_52a3`, `grant_52a7`, `grant_6c10`, `grant_6c14` (§9) |
| E11 | `acct_e5c3a0` | created 2026-08-01T12:00Z; email `samir.synthetic@example.com`, email sign-in; English; Africa/Cairo; last sign-in 2026-08-02; has lost access (support-9.14) |
| E12 · SE12 | `acct_0d4e9b` | created 2026-09-20; Sign in with Apple; Arabic; Asia/Riyadh; Grant `grant_8e20` Active 08:25–09:25Z on 2026-10-01; holds 2 photos, 1 audio file, 1 Pending Analysis, 1 queued job, 1 prepared export and 2 cached private analyses for the deletion story |
| E13 · SE13 | `acct_anon_71f2` and a local-trial install | anonymous session created 2026-09-29T09:00Z (age confirmed, AI `c-ai-4` Given in onboarding); 2 Analyses; support code `SB-9HQT-26KD` issued 2026-10-01T07:00Z |
| E14 | `acct_7b12aa` | created 2026-08-22; 240 Failed Analyses in 2026-09-01 → 30, none on a day above the quota; `an_7740` Failed `AI_UNAVAILABLE` 2026-10-01T09:20Z (kill switch On) |

### §6.2 · The auditor lens's accounts

| account | facts |
|---|---|
| `acct_3f88a1` | created 2026-08-15; Grant `grant_40aa` (§9); in separate fixture X it also holds a Consent pointing at `c-ai-2` (§13) |
| `acct_c2d7e5` | created 2026-08-16T12:00Z; 4 Units, 5 Unit versions (§7.5); Grant `grant_52c3` |
| `acct_8e14d9` | created 2026-08-17; no Grant (the 2026-10-03 refused reads) |
| `acct_d40e17` (deleted) | created 2026-08-19; Grant `grant_27b4`; deletion `job_del_2212` · `DEL-26-0912-F3P5` 2026-09-12T08:00Z → Completed 2026-09-20T08:00Z; former email `d40e17.synthetic@example.com` destroyed |
| `acct_b81c40` (deleted) | created 2026-08-11; email sign-in; deletion `job_del_2201` · `DEL-26-0902-A7K1` (§10.1) |
| `acct_e5a930` | created 2026-08-13; deletion `job_del_2204` · `DEL-26-0904-B9T2` Running |
| `acct_f61c22` | created 2026-08-23; deletion `job_del_2209` · `DEL-26-0909-C2M8` Running |
| `acct_0a7b55` | created 2026-08-26; deletion `job_del_2228` · `DEL-26-0928-D4Q6` Running |
| `acct_d13a01` | created 2026-08-28; deletion `job_del_2213` · `DEL-26-0913-H5W3` Failed (admin-9.1's second job) |

### §6.3 · The platform admin lens's bulk eaters

`eater-synth-001` … `eater-synth-400` and `anon-synth-001` … live in the separate dataset **A400** (§13), which admin stories load on top of this seed. The ids map `eater-synth-NNN` → `acct_5e0NNN`, `anon-synth-NNN` → `acct_anon_0NNN`.

---

## §7 · Units, Composites and Recipes (every number derived from §8's rows)

**Badge rule (J70):** a Unit's Evidence is its weakest component's, in the order user-defined < estimated analogue < recipe-calculated < label-verified < measured; "measured" = a reference Food (Tier A or approver-approved) on the eater's amount.

### §7.1 · Mona's (`acct_e9a001`) — all Saved version 1 on 2026-08-20 unless stated

| Unit · Arabic | kind · structure | components | kcal | P | C | F | Evidence |
|---|---|---|---|---|---|---|---|
| cheese spoon · معلقة جبنة | spoonful · Composite (the without-bread base, FR-024) | White cheese 5.4 g = 5.4/27 of a serving → 12.5 kcal, P 1.8, C 0.5, F 0.4; Olive oil 1.5 g → 13.5 kcal, F 1.5; measured total 6.9 g (AT-02) | 26.0 | 1.8 | 0.5 | 1.9 | measured |
| cheese bite · قرصة جبنة | bite · the with-bread variant of cheese spoon (bread inside) | cheese spoon + Bread, baladi 8 g (20 kcal, P 0.7, C 4.0, F 0.1) | **46.0** | **2.5** | **4.5** | **2.0** | measured |
| bread bite · لقمة عيش | bite · simple | v1: Bread, baladi 8 g | 20.0 | 0.7 | 4.0 | 0.1 | measured |
| egg bite · لقمة بيض | bite · simple, dipped (accompaniment: one bread bite, 8 g) | Egg, boiled 12 g (18.6; 1.56 / 0.12 / 1.32) + bread bite 8 g | 38.6 | 2.26 | 4.12 | 1.42 | measured |
| boiled egg · بيضة مسلوقة | piece · simple, no bread | Egg, boiled 50 g | 77.5 | 6.5 | 0.5 | 5.5 | measured |
| meat bite · لقمة لحمة | bite · simple, dipped with its own 5 g bread (FR-020) | Beef, stewed 10 g (25.0; 3.0 / 0 / 1.45) + Bread, baladi 5 g (12.5; 0.4375 / 2.5 / 0.0625) | 37.5 | 3.4375 | 2.5 | 1.5125 | measured |
| glass of milk · كوباية لبن | cup · simple | Milk, whole (her carton, label) 250 ml = 250 g (density 1.00) | 150 | 8 | 11.5 | 8 | label-verified |
| my teaspoon · معلقتي الصغيرة | spoonful · simple | Sugar 3.75 g | 15.0 | 0 | 3.75 | 0 | measured |
| glass of milk tea · كوباية شاي بلبن | cup · Composite | Brewed tea 150 ml (0) + Milk, whole 50 ml (30; 1.6 / 2.3 / 1.6) + 2 × my teaspoon (30; C 7.5) | 60.0 | 1.6 | 9.8 | 1.6 | measured |
| talbina spoon · معلقة تلبينة | spoonful · of Recipe Talbina v1 | 16 g = 16/384 = 1/24 of the pot (§7.6) | 20.0 | 0.8 | 2.625 | 0.7 | recipe-calculated |
| foul spoon · معلقة فول | spoonful · of Recipe foul v1, no bread | 20 g = 20/800 = 1/40 of the pot | 30.0 | 2.0 | 4.0 | 0.7 | recipe-calculated |
| foul spoon with oil · معلقة فول بالزيت | spoonful · Composite | foul spoon + Olive oil 1.5 g | 43.5 | 2.0 | 4.0 | 2.2 | recipe-calculated |
| foul bite · لقمة فول | bite · dipped (one bread bite) | foul spoon + bread bite 8 g | 50.0 | 2.7 | 8.0 | 0.8 | recipe-calculated |
| baladi loaf · رغيف بلدي | piece · simple | Bread, baladi 92 g | 230 | 8.05 | 46.0 | 1.15 | measured |
| molokhia plate · طبق ملوخية بالرز | plate · of Recipe molokhia with rice v1 | 300 g = 300/2,000 = 0.15 of the pot | 360 | 11.307 | 65.838 | 5.805 | recipe-calculated |
| tuna spoon · معلقة تونة | spoonful · simple | Tuna in oil, drained (her label) 23 g | 46.0 | 6.67 | 0 | 1.84 | label-verified |
| tuna bite · لقمة تونة | bite · Composite, bread inside | Tuna in oil, drained 6.8 g (13.6; 1.972 / 0 / 0.544) + Bread, baladi 8 g | 33.6 | 2.672 | 4.0 | 0.644 | label-verified (her tuna label is the weakest component) |
| olive · زيتونة | piece · simple | Olives, pickled 4 g | 5.3 | 0 | 0.2 | 0.5 | measured |

**Mona's Template** «فطار عادي» is not seeded (eater-3.19 saves it). Her rules: household default "bread with every dipped bite: 8 g" (version 1, 2026-08-20).

### §7.2 · Faisal's (`acct_e9a002`) — version 1 on 2026-08-25

| Unit · Arabic | components | kcal | P | C | F | Evidence |
|---|---|---|---|---|---|---|
| cheese bite · لقمة جبن | as Mona's cheese bite (his own Unit on the same Foods) | 46.0 | 2.5 | 4.5 | 2.0 | measured |
| cup of laban · كوب لبن | Laban drink (his bottle, label) 250 ml: 60.8 × 2.5 | 152 | 8 | 12 | 8 | label-verified |
| Sukkari date · تمرة سكري | Dates, Sukkari 8.0 g edible — the mean of a sample of 10 dates weighing 80.0 g after pits (FR-013) | 24 | 0.2 | 5.8 | 0 | measured |
| kabsa rice spoon · ملعقة رز كبسة | 25 g of Recipe kabsa rice v1 = 25/2,000 = 1/80 of the pot | 42.4 | 0.9 | 7.0 | 1.2 | recipe-calculated |
| chicken piece · قطعة دجاج | Chicken, roasted 60 g | 114 | 15 | 0 | 6 | measured |
| cup of gahwa · فنجان قهوة | Arabic coffee, brewed 60 ml (5 kcal per 100 ml, no macros) | 3 | 0 | 0 | 0 | measured |
| salad spoon · ملعقة سلطة | Salad, mixed, with dressing (generic) 30 g, the stand-in for a home salad; heuristic low/high scenario 11.0–24.0 kcal | 16.2 | 0.3 | 1.5 | 0.99 | estimated analogue |

### §7.3 · Sam's (`acct_e9a003`) — version 1 on 2026-09-08 unless stated

| Unit | components | kcal | P | C | F | Evidence |
|---|---|---|---|---|---|---|
| cheese bite | as Mona's | 46.0 | 2.5 | 4.5 | 2.0 | measured |
| cup of laban | Laban drink (his bottle, label) 250 ml | 152 | 8 | 12 | 8 | label-verified |
| bread bite | v1 Bread, baladi 8 g (20.0; 0.7 / 4.0 / 0.1); **v2 9 g saved 2026-09-30T06:00Z** (22.5; 0.7875 / 4.5 / 0.1125) | 20.0 → 22.5 | | | | measured |
| tuna spoon | Tuna, canned in water, drained 23 g (26.68; 5.865 / 0 / 0.184); **Archived 2026-09-25** | 26.68 | 5.865 | 0 | 0.184 | measured |
| biscuit serving | v1: Biscuits, plain (label) 25 g | 125 | 1.5 | 17 | 5.5 | label-verified |
| foul spoon | Foul medames, canned (his tin, label 150 / 10 / 20 / 3.5 per 100 g) 20 g | 30.0 | 2.0 | 4.0 | 0.7 | label-verified |
| foul spoon with oil | foul spoon + Olive oil 1.5 g | 43.5 | 2.0 | 4.0 | 2.2 | label-verified |
| oatmeal bowl | 1 bowl (his label, per bowl) | 360 | 15 | 52.5 | 10 | label-verified |
| chicken rice box | 1 box (his label, per box) | 480 | 42 | 33 | 20 | label-verified |
| shawarma plate | 1 plate — "one plate incl. fries and garlic sauce" (the restaurant's published value with its menu page as evidence; FRD §6.2) | 875 | 45 | 80 | 41 | label-verified ("Restaurant's published value") |
| sandwich quarter | 1 quarter of Club sandwich (his label, per quarter) — the AT-20 fixture: 4C ÷ 4/4/9 = 75.1 ÷ 250 = 30.04 % | 250 | 12 | 18.775 | 14.1 | label-verified |
| grilled chicken bite | Chicken, grilled (generic) 15 g (28.8; 4.5 / 0 / 1.2), the stand-in for restaurant grilled chicken, + bread bite 8 g | 48.8 | 5.2 | 4.0 | 1.3 | estimated analogue |
| hummus bite | Hummus, commercial (FDC 321358) 15 g (34.35; 1.1025 / 2.235 / 2.565), the stand-in for restaurant hummus, + bread bite 8 g; heuristic low/high 45.0–75.0 kcal | 54.35 | 1.8025 | 6.235 | 2.665 | estimated analogue |
| fries handful | French fries (generic) 30 g | 93 | 0.99 | 12 | 4.5 | estimated analogue |
| oat biscuit | 1 serving of Oat biscuit (his label: 95 kcal per 30 g; P 2, total C 20 incl. sugar alcohols 8 and fiber 2, F 3; 4/4/9 = 115); saved 2026-09-27T10:05Z | 95 | 2 | 20 | 3 | label-verified |

### §7.4 · Hala's trial T1 (on her iPhone; no server record)

cheese bite «قرصة جبنة» v1 (46.0; as Mona's) · tea with milk «شاي بلبن» v1 (60.0; as Mona's glass of milk tea) · bread bite «لقمة عيش» v1 (20.0; 8 g) · Template «فطار» = 3 cheese bites + 1 tea with milk (198 kcal).

### §7.5 · Other accounts' Units

- **E1** `acct_9c41e2` (9 Units, 12 versions, 2 Recipes): لقمة جبن cheese bite v1 (46.0) · كوب لبن cup of laban v1 250 ml (152) and **v2 300 ml** 2026-09-01 (182.4; 9.6 / 14.4 / 9.6) · تمرة سكري Sukkari date v1 (24) · ملعقة رز كبسة kabsa rice spoon v1 (42.4; 25 g of her Recipe كبسة v1, same ingredients and yield as Faisal's, with her own stock label of the same values) · قطعة دجاج chicken piece v1 60 g (114) and **v2 70 g** 2026-09-10 (133; 17.5 / 0 / 7) · فنجان قهوة gahwa v1 (3) · خبز تميس tamees piece v1 50 g (140; 4.5 / 27.5 / 1.25) and **v2 60 g** 2026-09-12 (168; 5.4 / 33 / 1.5) · ملعقة حمص hummus spoon v1 (Hummus, commercial 20 g: 45.8; 1.47 / 2.98 / 3.42) · كوب شاي tea glass v1 (Brewed tea 150 ml + Sugar 5 g: 20; 0 / 5 / 0). Recipes: كبسة v1; شوربة عدس lentil soup v1 (§7.6).
- **E10** `acct_f1e0c3`: cheese bite (46.0), bread bite 8 g (20.0), foul spoon (30.0; his tin of the same label values as Sam's).
- **`acct_c2d7e5`** (5 versions): cheese bite v1 · bread bite v1 (8 g) and v2 (9 g, 2026-09-20) · cup of laban v1 (his label, 152) · Sukkari date v1.
- **E12** `acct_0d4e9b`: cheese bite v1 · cup of laban v1.
- **E4, E6, E7** and the other accounts: cheese bite v1 and cup of laban v1 (their own labels of the same values).

### §7.6 · Recipes and the recomputed planner figures

| Recipe (owner) | ingredients (Food, mass → kcal; P / C / F) | totals | cooked yield | per 100 g |
|---|---|---|---|---|
| Talbina v1 (Mona, 2026-08-20; AT-06) | Barley flour 40 g → 140; 3.2 / 30 / 0.8 · Milk, whole 500 g → 300; 16 / 23 / 16 · Sugar 10 g → 40; 0 / 10 / 0 | 480 kcal; P 19.2, C 63, F 16.8 (4/4/9 = 480) | 384 g | 125 kcal; 5 / 16.40625 / 4.375 — 16 g = 20 kcal; 15 spoons = 300; 18 = 360 (AT-06) |
| foul v1 (Mona) | Foul, canned (her label) 1,000 g → 1,200; 80 / 160 / 28 · Water 200 g → 0 | 1,200; 80 / 160 / 28 | 800 g (simmered, mashed) | 150; 10 / 20 / 3.5 |
| molokhia with rice v1 (Mona) | Rice, white, raw 500 g → 1,760; 35 / 390 / 4 · Jute leaves, frozen (her label) 500 g → 200; 23 / 29 / 1.5 · Chicken broth (her label) 1,000 g → 100; 15 / 4 / 3 · Ghee 30 g → 270; 0 / 0 / 30 · Garlic 20 g → 30; 1.28 / 6.62 / 0.1 · Onion 100 g → 40; 1.1 / 9.3 / 0.1 | 2,400; 75.38 / 438.92 / 38.7 | 2,000 g | 120; 3.769 / 21.946 / 1.935 |
| kabsa rice v1 (Faisal; E1's copy identical) | Rice, white, raw 700 g → 2,464; 49 / 546 / 5.6 · Ghee 90 g → 810; 0 / 0 / 90 · Onion 100 g → 40; 1.1 / 9.3 / 0.1 · Chicken stock (his label) 1,000 g → 78; 21.9 / 4.7 / 0.3 | 3,392; 72 / 560 / 96 (4/4/9 = 3,392) | 2,000 g | 169.6; 3.6 / 28 / 4.8 |
| lentil soup v1 (E1) | Lentils, dry 200 g → 704; 49.2 / 126.8 / 2.2 · Onion 100 g → 40; 1.1 / 9.3 / 0.1 · Water 1,500 g → 0 · Olive oil 20 g → 180; 0 / 0 / 20 | 924; 50.3 / 136.1 / 22.3 | 1,600 g | 57.75; 3.14375 / 8.50625 / 1.39375 |

**The planner figures that replace e578's (J55, J56):**

| story | inputs | result |
|---|---|---|
| eater-5.12 | 3 foul bites, each with its bread | 150 kcal (90 for the filling alone); expanded vector per count 50.0 kcal, P 2.7, C 8.0, F 0.8; a cheese bite adds no second bread (46.0) |
| eater-5.17 (basis Count) | foul bite 6 · cheese bite 4 · olive 1 · egg bite 8 · tuna bite 1 | kcal 300 + 184 + 5.3 + 308.8 + 33.6 = **831.7** (shown 832); calorie shares 36.0707 · 22.1234 · 0.6372 · 37.1288 · 4.0399 % → largest remainder to one decimal **36.1 · 22.1 · 0.6 · 37.1 · 4.1** (sum 100.0); count shares 30.0 · 20.0 · 5.0 · 40.0 · 5.0; the `/m` line reads "Calorie aim about **831.7** with tolerance 0" → 6 / 4 / 1 / 8 / 1, the only zero-deviation answer (with aim 809.7, 53 other count sets would reach it exactly and 6/4/1/8/1 would miss by 22 kcal) |
| eater-5.21 (AT-17) | foul bite, cheese bite, egg bite, each with bread; Carbohydrate maximum 30 %; Calorie aim about 300 (±10 %) | shares on macro energy: foul bite 32 ÷ 50 = **64.00 %**, cheese bite 18 ÷ 46 = 9/23 = **39.13 %**, egg bite 16.48 ÷ 38.3 = **43.03 %** → Infeasible; changes "Raise the maximum to 39.2 %" and "Use your cheese spoon" (2.0 ÷ 26.3 = **7.60 %**); after raising to 39.2 %, only cheese bites fit: 6 = 276 kcal (24 from 300), **7 = 322 kcal** (22 from 300) → 7 cheese bites, carbohydrate 39.13 %; the egg bite reads "43.0 % — above the maximum" |
| eater-5.22 (AT-19) | must-include fries ≥ 1; ceiling 500 | example row set 2 fries + 4 grilled chicken bites + 1 hummus bite = 186 + 195.2 + 54.35 = **435.55** kcal (shown 436); with ceiling 80: "must-include fries (93 kcal) are above the 80 kcal ceiling", change "Raise the ceiling to 93 kcal" |
| eater-5.23 | Faisal: kabsa rice spoon, chicken piece (Available 3), salad spoon, cup of laban; ceiling 500; Protein minimum 60 g; Carbohydrate maximum 30 % | most protein under the other limits **53 g** = 3 chicken + 1 laban (494 kcal; carbohydrate 48 ÷ 494 = 9.72 %); lowest ceiling reaching 60 g with ≤ 3 chicken **646 kcal** = 3 chicken + 2 laban (61 g); 4 chicken alone = 60 g at 456 kcal → changes "Lower the protein minimum to 53 g", "Raise the ceiling to 646 kcal", "Allow 4 chicken pieces"; `changes[]` 53, 646, 4 |
| eater-5.14 / 5.44 | kabsa rice spoon 212/5 kcal, chicken piece 114 (Available 3); aim about 400 (±10 %, a limit: 360–440); ceiling 500; carbohydrate ≤ 30 % | 12 count sets meet the ceiling, carbohydrate and Available limits; with the aim band, **3** meet every limit: 4 + 2 (397.6 kcal, 2.4 from 400), 1 + 3 (384.4, 15.6 away), 2 + 3 (426.8, 26.8 away) → **4 rice + 2 chicken = 397.6 kcal**, carbohydrate 112 ÷ 397.6 = 28.17 %; next nearest 1 rice + 3 chicken (384.4); 5 + 2 (440.0 kcal, 31.82 %) is never returned |
| eater-5.32, 5.44 egg duplicates | 2 egg bites | 77.2 kcal (shown 77); aim about 77.2 with tolerance 0 → 2 of one Unit |
| eater-8.7 | Sam's Day 1,200 + 1 cheese bite offline | "1,246 eaten · 46 Pending", "Remaining 624"; after sync `GET /v1/reports/day` → 1,246 |

---

## §8 · Reference data (Approved unless stated)

### §8.1 · Tier A rows (mock USDA release 15.5, licence CC0, arrived Approved; "synthetic" = a mock row with no real FDC id)

| Food | FDC id | per 100 g: kcal · P · C · F (fiber, sugars) | note |
|---|---|---|---|
| Hummus, commercial | 321358 (Foundation) | 229 (Atwater specific; 243 general) · 7.35 · 14.9 (by difference) · 17.1 total lipid (fiber 5.4, sugars 0.34) | **(FDC, as published)**; per-nutrient derivations as approver-10.24 (Analytical → measured; Calculated, Summed → estimated); "Total fat (NLEA)" 16.1 |
| Falafel | 2707408 (FNDDS) | 333 · 13.3 · 31.8 · 17.8 | synthetic values; FNDDS, values marked "estimated" |
| Fava beans, cooked | 2707367 (FNDDS) | 110 · 7.6 · 19.6 · 0.4 | synthetic values; the analogue the Tier B record replaces (approver-10.39) |
| Bitter melon, horseradish, jute, or radish leaves, cooked | 2709641 | 34 · 3.6 · 6.0 · 0.2 | synthetic values; analogue for molokhia leaves (approver-10.38) |
| Date (generic) | 2709203 | 282 · 2.45 · 75.0 · 0.39 | synthetic values; analogue for «تمر صقعي» (approver-10.10) |
| Olive oil · Ghee · Oil, frying | synthetic | 900 · 0 · 0 · 100 each | |
| Barley flour | synthetic | 350 · 8 · 75 · 2 (4/4/9 = 350) | P 8 (J55) |
| Milk, whole | synthetic | 60 · 3.2 · 4.6 · 3.2 per 100 g and per 100 ml (density 1.00) | |
| Sugar | synthetic | 400 · 0 · 100 · 0 | |
| Honey | synthetic | 304 · 0.3 · 82 · 0 | |
| Egg, boiled | synthetic | 155 · 13 · 1 · 11 | |
| Tuna, canned in water, drained | synthetic | 116 · 25.5 · 0 · 0.8 | no "in oil" Tier A row exists (AT-05) |
| Yogurt, plain | synthetic | 61 · 3.5 · 4.7 · 3.3 | per 100 g only, no density |
| Toast, white | synthetic | 270 · 9 · 50 · 3.5 | |
| Bread, pita | synthetic | 275 · 9.1 · 55.7 · 1.2 | approver-10.37 |
| Rice, white, raw | synthetic | 352 · 7 · 78 · 0.8 | |
| Rice, cooked, with fat (generic) | synthetic | 170 · 3.5 · 28 · 4.8 | offered as the analogue for kabsa rice on a plate (e24 "Kabsa rice") |
| Lentils, dry | synthetic | 352 · 24.6 · 63.4 · 1.1 | |
| Fava beans, dry | synthetic | 335.6 · 26.1 · 58.3 · 1.5 | |
| Onion, raw · Garlic, raw | synthetic | 40 · 1.1 · 9.3 · 0.1 · and 150 · 6.4 · 33.1 · 0.5 | |
| Cumin, ground · Salt · Water | synthetic | 400 · 18 · 44 · 22 · and 0 · and 0 | |
| Brewed tea · Arabic coffee, brewed | synthetic | 0 per 100 ml · and 5 (0 · 0 · 0) per 100 ml | gahwa's 3 kcal on zero macros gives "Not applicable" shares |
| Olives, pickled | synthetic | 132.5 · 0 · 5 · 12.5 | |
| Chicken, roasted · Chicken, grilled (generic) | synthetic | 190 · 25 · 0 · 10 · and 192 · 30 · 0 · 8 | |
| Beef, stewed | synthetic | 250 · 30 · 0 · 14.5 | |
| Rice, cooked | synthetic | 130 · 2.7 · 28.2 · 0.3 | AT-03 (eater-2.17) |
| Beef, cooked | synthetic | 250 · 26 · 0 · 15.4 | AT-03 (eater-2.17) |
| French fries (generic) | synthetic | 310 · 3.3 · 40 · 15 | |
| Salad, mixed, with dressing (generic) | synthetic | 54 · 1.0 · 5.0 · 3.3 | |

USDA releases on the mock: **15.4** (loaded 2026-08-01T05:00Z) and **15.5** (published 2026-09-24, imported 2026-09-25T03:00Z: 120 new, 35 changed, 2 removed rows, synthetic counts; the 2 removed rows are Retired with "removed upstream in USDA release 15.5"; Falafel 15.4 → Superseded, 15.5 Approved).

### §8.2 · Approver-approved Foods (licence and Evidence as listed)

| Food | basis · kcal · P · C · F | source · licence | Evidence when used |
|---|---|---|---|
| White cheese (gibna beida), approver's composition v3 | per 27 g portion as analysed: 62.5 · 9 · 2.5 · 2 (per 100 g 231.48… (6250/27) · 33.33… (100/3) · 9.259… (250/27) · 7.407… (200/27); shown 231.5 · 33.3 · 9.3 · 7.4) | approver's weighed composition (synthetic) · first-party | measured; v4 is approved in eater-2.36 (in-story) |
| Bread, baladi | per 100 g: 250 · 8.75 · 50 · 1.25 | approver's weighed composition (synthetic) · first-party | measured |
| Bread, shami | 260 · 9 · 52 · 1.5 | same | measured |
| Bread, tamees | 280 · 9 · 55 · 2.5 | same | measured |
| Dates, Saqai | 300 · 2 · 75 · 0.4 | same; Aliases "Saqai date", «صقعي» (Gulf) | measured |
| Dates, Sukkari | 300 · 2.5 · 72.5 · 0 | same | measured |
| Laban drink | per 100 ml: 60.8 · 3.2 · 4.8 · 3.2 | label, approver-reviewed; Gulf Alias «لبن» points here | label-verified |
| Molasses, sugarcane «عسل أسود» | 290 · unknown · 72 · 0 | label (protein not printed) | label-verified |
| Biscuits, plain (synthetic brand) | 500 · 6 · 68 · 22 (AT-08: a 10 g piece = 50 kcal) | label | label-verified |
| Date-filled biscuit | per piece: 200 · 5 · 30 · 12 (4/4/9 = 248) | label | label-verified |
| Basbousa (synthetic brand) | 380 · 5 · 60 · 18 | label | label-verified |
| Ma'amoul (synthetic brand) | 430 · 6 · 62 · 24 | label | label-verified |
| Peas with sauce (bisilla, approver's composition) | 90 · 4.5 · 11 · 3.2 (4/4/9 = 90.8, under the threshold) | approver's weighed composition (synthetic) · first-party | measured; AT-03 (eater-2.17) |

### §8.3 · Tier B recipe records

- **`rec_fm_eg` v1 · فول مدمس — EG (olive oil, cumin) · Ful medames**: Fava beans, dry 500 g → 1,678; 130.5 / 291.5 / 7.5 · Water 2,000 g → 0 · Olive oil 30 g → 270; 0 / 0 / 30 · Cumin, ground 3 g → 12; 0.54 / 1.32 / 0.66 · Salt 5 g → 0 = **1,960 kcal**; 131.04 / 292.82 / 38.16; weighed cooked yield **1,400 g** → **140 kcal per 100 g** (exactly 1.4 kcal/g; a 16 g spoon = 22.4 kcal); P 9.36, C 20.9157… (14641/700), F 2.7257… (477/175) per 100 g. Evidence recipe-calculated; licence "Own calculation · ingredients CC0 1.0 (USDA FoodData Central — cite)" (auditor M5: the approver's line wins); Evidence files `ev_1101` (weighed ingredients) and `ev_1102` (cooked-yield weight). Proposed by `staff_yara` 2026-09-12T09:00Z, Approved by `staff_dina` 09:40Z. Aliases «فول مدمس» (EG, MSA), "ful medames".
- **Launch dishes gate**: 50 synthetic dishes; the first nine are فول، طعمية، فتة، كشري، ملوخية، كبسة، جريش، مرقوق، تلبينة; states on 2026-09-29: Approved 12 (فول مدمس and 11 unnamed synthetic dishes `dish_10`–`dish_20`), In review 3, Proposed 5, none 30 → "Approved 12 of 50". طعمية, كشري, فتة, Beef broth and مرقوق are built in the approver's stories.

### §8.4 · Aliases

| Alias | text | dialect (stored) | → Food | approved |
|---|---|---|---|---|
| `al_saqai` | «صقعي», transliteration "Saqai", English "Saqai date" | Gulf (`afb`) | Dates, Saqai | 2026-09-12T10:15Z by `staff_yara` |
| `al_laban_eg` | «لبن» | EG (`arz`) | Milk, whole | 2026-09-12T10:30Z |
| `al_laban_gulf` | «لبن» | Gulf (`afb`) | Laban drink | 2026-09-12T10:45Z |
| `al_fm` | «فول مدمس», "ful medames" | EG, MSA (`arb`) | `rec_fm_eg` | with the record, 2026-09-12T09:40Z |

MSA «لبن» has no Alias. Precedence: the eater's own Unit name → the eater's dialect → the approved Alias (J81).

### §8.5 · Eaters' own label records (private Foods)

Mona: Tuna in oil, drained (200 · 29 · 0 · 8 per 100 g) · Milk, whole (her carton; as the reference row) · Foul, canned (120 · 8 · 16 · 2.8) · Jute leaves, frozen (40 · 4.6 · 5.8 · 0.3) · Chicken broth (10 · 1.5 · 0.4 · 0.3). Faisal: Laban drink (his bottle; per 100 ml as the reference row) · Chicken stock (7.8 · 2.19 · 0.47 · 0.03). Sam: Laban drink (his bottle) · Foul medames, canned (150 · 10 · 20 · 3.5) · Oatmeal bowl (per bowl 360 · 15 · 52.5 · 10) · Chicken rice box (per box 480 · 42 · 33 · 20) · Shawarma plate (per plate 875 · 45 · 80 · 41, restaurant's published value) · Club sandwich (per quarter 250 · 12 · 18.775 · 14.1) · Oat biscuit (per 30 g 95 · 2 · 20 · 3).

### §8.6 · Flags and Label submissions (Review, at 2026-09-29T07:00Z)

- **Label submissions (Proposed)**: `L-15` Sam's Oat biscuit (2026-09-27T10:06Z; opens Energy-mismatch flag F-06: 95 vs 115 kcal, gap 20 kcal, 21.1 %) · `L-16` Mona's Molasses label photo (2026-09-26T09:05Z) · `L-17` Faisal's Laban drink bottle (2026-09-28T12:05Z). Each carries the `label_review` Consent of its eater (§11 events 149, 151, 157).
- **Open flags (9)**, counts only, no eater identifier:

  | flag | type | subject | evidence |
  |---|---|---|---|
  | F-01 | Estimated analogue | «تمر صقعي» → Date (generic), FDC 2709203 | `eaters_affected` 6 in the last 28 days (≥ 5, so the text shows, J82) |
  | F-02 | Estimated analogue | "kabsa rice" → Rice, cooked, with fat (generic) | 14 |
  | F-03 | Estimated analogue | «ملوخية» leaves → FDC 2709641 | 9 |
  | F-04 | Estimated analogue | "restaurant hummus" → Hummus, commercial (FDC 321358) | 7 |
  | F-05 | Estimated analogue | "grilled chicken" → Chicken, grilled (generic) | 11 |
  | F-06 | Energy mismatch | L-15 Oat biscuit: label 95 kcal per 30 g vs 4/4/9 115 | gap 20 kcal, 20 ÷ 95 = 21.1 % |
  | F-07 | Energy mismatch | Date-filled biscuit: label 200 per piece vs 4/4/9 248 | gap 48 kcal, 24.0 % |
  | F-08 | Energy mismatch | Basbousa (synthetic brand): label 380 per 100 g vs 4/4/9 422 (P 5, C 60, F 18) | gap 42 kcal, 11.1 % (not a flag under Policy v2's 12 %) |
  | F-09 | Energy mismatch | Ma'amoul (synthetic brand): label 430 per 100 g vs 4/4/9 488 (P 6, C 62, F 24) | gap 58 kcal, 13.5 % |

  Never flagged: Biscuits, plain (500 vs 494) and White cheese (62.5 vs 64.0 per serving) are under the threshold; Molasses has an unknown protein, so its 4/4/9 is not evaluated. **Tier A rows are not cross-checked** (J77): FDC derives their energy with food-specific factors and counts fiber inside carbohydrate by difference, so Date (generic) (282 vs 4/4/9 313.31, gap 31.31 kcal, 11.10 %), Cumin, ground (400 vs 446, gap 46 kcal, 11.5 %) and Falafel (333 vs 340.6) open no flag. The check runs over label values and approver-entered records only: Label submissions, approver-approved Foods (§8.2) and Tier B recipe records.
- **Counts on Review** (approver-10.1): Label submission 3 · Estimated analogue 5 · Energy mismatch 4; `GET /v1/admin/flags?open=true` returns the 9 flags.

---

## §9 · Grants, cases and support codes

All times UTC (the eater's local time in brackets where the stories quote it). Reads, refusals and writes are the Audit trail events of §11. Every Grant's request wording is `grant-req-1`.

| Grant | eater | requested by | reason · Days · areas · duration · case | requested | answered | Active window | end | allowed reads · refused reads · refused writes |
|---|---|---|---|---|---|---|---|---|
| `grant_27b4` | `acct_d40e17` | `staff_tariq` | report_mismatch · 2026-09-08 → 09 · Entries and day reports · 1 h · CASE-1101 | 2026-09-10 09:00 | Approved 09:10 | 09:10 → 10:10 | Expired 10:10 | 1 · 0 · 0 |
| `grant_40ab` (SE10) | `acct_f1e0c3` | `staff_mona` | sync_missing_entry · 2026-09-24 → 26 · Entries and day reports · 1 h · CASE-1170 | 2026-09-27 10:05 (13:05 Cairo) | — | — | **Unanswered** 2026-09-30 10:05 | 0 · 0 · 0 |
| `grant_a1d4` (E6) | `acct_c2a917` | `staff_omar` | report_mismatch · 2026-09-29 → 30 · Entries and day reports · 1 h · CASE-1250 | 2026-10-01 07:55 | Approved 08:00 | 08:00 → 09:00 | Expired 09:00 | 0 · 0 · 0 |
| `grant_8e20` (SE12) | `acct_0d4e9b` | `staff_omar` | unit_calculation · 2026-09-30 · My Units · 1 h · CASE-1205 | 2026-10-01 08:20 | Approved 08:25 | 08:25 → 09:25 | Expired 09:25 (the deletion story withdraws it earlier, `account_deletion`) | 0 · 0 · 0 |
| **`grant_31f0`** G1 (E1) | `acct_9c41e2` | `staff_mona` | sync_missing_entry · 2026-09-28 → 30 · Entries and day reports + My Units · 1 h · CASE-1182 | 2026-10-01 10:05 | Approved 10:20 (13:20 Riyadh) | 10:20 → 11:20 (14:20 Riyadh; 12:20 Dublin) | Expired 11:20 | 3 (10:24 Day 2026-09-29, 7 Entries; 10:25 Entry `en_9921`; 10:27 My Units, 12 Unit versions) · 2 (11:20:01, 11:20:05, `GRANT_NOT_ACTIVE` Expired) · 0 |
| `grant_40aa` | `acct_3f88a1` | `staff_omar` | activity_import · 2026-09-29 → 30 · Activity · 4 h · CASE-1183 | 2026-10-01 10:09 | — | — | **Unanswered** 2026-10-04 10:09 | 0 · 1 (2026-10-04 10:30) · 0 |
| **`grant_31f9`** G2 (E1) | `acct_9c41e2` | `staff_mona` | as G1 · CASE-1182 | 2026-10-01 11:30 | **Declined** 11:42 (14:42 Riyadh) | — | — | 0 · 1 (11:45) · 0 |
| `grant_52a3` (SE10) | `acct_f1e0c3` | `staff_mona` | report_mismatch · 2026-09-29 → 30 · Entries and day reports · 1 h · CASE-1220 | 2026-10-01 12:00 | Approved 12:05 (15:05 Cairo) | 12:05 → | **Withdrawn** by the eater 12:26 (15:26 Cairo) | 1 (12:10) · 0 · 0 |
| `grant_52a7` (SE10) | `acct_f1e0c3` | `staff_mona` | unit_calculation · 2026-09-30 · My Units · 1 h · CASE-1221 | 2026-10-01 13:00 | Approved 13:04 (16:04 Cairo) | 13:04 → (14:04) | **Ended** by the Support agent 13:33 (16:33 Cairo) | 1 (13:10) · 0 · 0 |
| `grant_6c10` (SE10) | `acct_f1e0c3` | `staff_mona` | activity_import · 2026-09-29 → 30 · Activity · 1 h · CASE-1236 | 2026-10-01 14:58 | Approved 15:02 | 15:02 → 16:02 | Expired 16:02 | 1 (15:05) · 0 · 0 |
| `grant_7d01` (E4) | `acct_77d2c0` | `staff_mona` | report_mismatch · 2026-09-29 → 30 · Entries and day reports · 1 h · CASE-1225 | 2026-10-01 15:29 | Approved 15:30 | 15:30 → 16:30 | Expired 16:30 | 1 (15:35) · 4 (15:40 Day 09-28 `GRANT_REQUIRED` outside_days; 15:41 My Units and 15:42 Activity `GRANT_REQUIRED` outside_areas; 15:43 an id of E2 `NOT_FOUND`) · 5 (15:45:00–04: consumption, corrections, void, units, recipes; `FORBIDDEN`) |
| `grant_6c14` (SE10) | `acct_f1e0c3` | `staff_mona` | as `grant_6c10` · CASE-1236 | 2026-10-01 15:55 | — | — | **Unanswered** 2026-10-04 15:55 | 0 · 0 · 0 |
| `grant_9b30` (E7) | `acct_a41c55` | `staff_mona` | report_mismatch · 2026-09-30 · Entries and day reports · 1 h · CASE-1240 | 2026-10-01 16:55 | Approved 17:00 | 17:00 → (18:00) | **Ended** 17:25 (pressed offline at 17:22, sent on reconnecting) | 1 (17:05) · 0 · 0; one read **failed** 17:10 (`SERVICE_UNAVAILABLE`, not shown to the eater) |
| `grant_52c3` | `acct_c2d7e5` | `staff_omar` | unit_calculation · 2026-10-01 · My Units · 4 h · CASE-1191 | 2026-10-02 08:00 | Approved 08:05 | 08:05 → | **Withdrawn** 08:30 | 1 (08:10, 5 Unit versions) · 1 (08:31) · 0 |
| `grant_5d10` (E1) | `acct_9c41e2` | `staff_mona` | sync_missing_entry · 2026-10-01 → 02 · Entries and day reports · 1 h · CASE-1192 | 2026-10-02 13:00 | Approved 13:02 | 13:02 → | **Ended** 13:20 | 1 (13:05, Day 2026-10-01, 4 Entries) · 0 · 0 |
| `grant_6e21` (E1) | `acct_9c41e2` | `staff_omar` | report_mismatch · 2026-10-02 · Entries and day reports · 1 h · CASE-1195 | 2026-10-03 09:00 | Approved 09:03 (one approve command delivered 3 times → one event) | 09:03 → 10:03 | Expired 10:03 | 0 · 0 · 0 |

**Other cases**: CASE-1201 (support-9.11: E9's escalation, 2026-10-01 11:05) · CASE-1210 (support-9.12: the `lina.synthetic@example.com` look-up) · CASE-1215 (support-9.15: E7's email) · CASE-1230 (support-9.14: E11). **Support codes**: E1 `SB-7KQ2-94XM` (valid 2026-10-01T06:12Z → 10-02T06:12Z; shown in E1's app as "Valid until 2 Oct, 09:12") and `SB-3MRT-7WQD` (expired 2026-09-30T06:12Z); E13 `SB-9HQT-26KD`. The local-trial install has no code.

---

## §10 · Jobs and job records

### §10.1 · Privacy jobs (attempt budget 5 per job; 3 automatic attempts, then Failed — J142; due = requested + 30 days)

| job | eater | kind | requested | stages and attempts | state on 2026-10-01T09:00Z | later |
|---|---|---|---|---|---|---|
| `job_del_2120` · DEL-26-0820-M2V5 | `acct_88e0c1` (E8) | deletion | 2026-08-20T08:00Z | every stage Completed; Sign in with Apple — Not applicable (email) | Completed 2026-09-18T10:00Z (29 days) | completion record: reference, requested, completed, "every stage Completed", "backups expire by 2026-10-18T10:00:00Z (30-day backup lifecycle, `assumption`, disclosed)" and nothing else |
| `job_del_2201` · DEL-26-0902-A7K1 | `acct_b81c40` | deletion | 2026-09-02T06:00Z | signed out and disabled 06:00 · private records deleted 06:10 · photos and audio deleted 06:20 · derived caches and cached analyses deleted 06:25 · queued commands and uploads purged 06:26 · prepared exports deleted 06:27 (0 files) · Grants withdrawn — Not applicable (none) · processors told (Google) 07:00 · processor confirmation received 2026-09-16T06:00 · Sign in with Apple — Not applicable (email) | Completed 2026-09-16T06:30Z (14 days) | backups expire by 2026-10-16T06:30Z |
| `job_del_2204` · DEL-26-0904-B9T2 | `acct_e5a930` | deletion | 2026-09-04T09:00Z | every live stage Completed 2026-09-04; processor confirmation — Running (waiting) | Running, due 2026-10-04 | at 2026-10-05T09:00Z: open 31 days → the Anomalies row "deletions open more than 30 days" |
| `job_del_2205` · DEL-26-0905-P7T2 | `acct_9a07e5` (E9) | deletion | 2026-09-05T08:00Z | live stages Completed 2026-09-05; "processors told (Google)" failed 2026-09-30T06:00, 06:20, 06:40 (`processor_timeout`) | **Failed** · 3 of 5 attempts · due 2026-10-05 · 4 days left | escalated by `staff_mona` 11:05 (CASE-1201); `staff_ali` retries 14:10 (attempt 4 of 5), stage Completed 14:12; processor confirmation 2026-10-03T05:40; Completed 2026-10-03T06:00Z (28 days) |
| `job_del_2209` · DEL-26-0909-C2M8 | `acct_f61c22` | deletion | 2026-09-09T09:00Z | live stages Completed; processor confirmation Running | Running, due 2026-10-09 | — |
| `job_del_2212` · DEL-26-0912-F3P5 | `acct_d40e17` | deletion | 2026-09-12T08:00Z | every stage Completed | Completed 2026-09-20T08:00Z (8 days) | — |
| `job_del_2213` · DEL-26-0913-H5W3 | `acct_d13a01` | deletion | 2026-09-13T09:00Z | "photos and audio deleted" failed 2026-09-29T11:20, 11:40, 12:00 (`storage_timeout`) | **Failed** · 3 of 5 · due 2026-10-13 · 12 days left | — |
| `job_del_2215` · DEL-26-0915-K3Q8 | `acct_51ab07` (E2) | deletion | 2026-09-15T07:00Z | signed out and disabled · private records deleted (2026-09-15) · photos and audio, derived caches, queued commands, prepared exports, Grants withdrawn — Not applicable · processors told (Google) (2026-09-16) · processor confirmation — **Running**, expected by 2026-10-14 · Sign in with Apple token revoked — Not applicable (email) · completion record — Requested | Running · due 2026-10-15 | — |
| `job_del_2228` · DEL-26-0928-D4Q6 | `acct_0a7b55` | deletion | 2026-09-28T10:00Z | "photos and audio deleted" failed 2026-09-28T10:05 (`storage_timeout`) and 2026-10-05T08:05; next retry 2026-10-05T09:05, same job id | Running · "1 step retrying" | day 7 of 30 on 2026-10-05 |
| `job_exp_4402` | `acct_9c41e2` | export | 2026-09-15T12:00Z | Completed 12:04 · 184 KB · Entries 216 · Units 12 versions · Recipes 2 · Templates 0 · Rules 1 · Targets 1 · Activity 30 · Weights 0 · Consents 9 (8 Consent records + the age confirmation) · Reports 11 (10 day, 1 period) · Unit pictures 0 | downloaded once 12:10; file kept to 2026-09-22T12:04 (7 days); deleted early 2026-09-20T07:45:05Z by `job_cw_4501` | — |
| `job_exp_31` | `acct_a41c55` (E7) | export | 2026-09-20T11:55Z | Completed 12:00 | download window over 2026-09-27T12:00Z | — |
| `job_exp_77` | `acct_a41c55` | export | 2026-09-30T15:02Z | Completed 15:09 · 2.4 MB | downloadable until 2026-10-07T15:09Z | — |
| `job_exp_88` | `acct_a41c55` | export | 2026-10-01T10:40Z | — | (not yet requested at 09:00) | Running 10:40 → Completed 10:47 |
| `job_exp_4410` | `acct_e07d13` (E3) | export | 2026-10-01T07:58Z | failed 08:00, 08:20, 08:40 (`storage_timeout`) | **Failed** · 3 of 5 · last 08:40 | support-9.9 retries it once (attempt 4 of 5) |
| `job_cw_4501` | `acct_9c41e2` | Consent withdrawal (AI) | 2026-09-20T07:45:00Z | new Analyses refused 07:45:00 · Pending Analyses cancelled 1 · queued uploads purged 2 · cached private analysis deleted 1 (07:45:03) · photos and audio held for analysis deleted 3 (07:45:04) · prepared export files deleted 1 (`job_exp_4402`, 07:45:05) · confirmed Entries — Not applicable · AI requests until the next Consent (2026-09-25T20:10Z): 0 | Completed 07:45:05 | — |
| `job_cw_1814` | `acct_c2a917` (E6) | Consent withdrawal (AI) | 2026-09-30T18:14Z | new Analyses refused 18:14 · queued photo and voice uploads removed 18:15 (2) · cached private analyses deleted 18:17 (3) · raw photos and audio deleted 18:19 (4 + 1) · prepared exports deleted 18:20 (1) · confirmed Entries — Not applicable | Completed 18:20 | — |

### §10.2 · Analyses (stamped with their Registry version)

| Analysis | eater | made | input | state | stamp |
|---|---|---|---|---|---|
| `an_5512` | E1 | 2026-09-29T16:00Z | photo (kabsa plate) | Approved → Entry `en_9921` (Rice, cooked, with fat 200 g as the analogue for kabsa rice: 340 kcal; P 7, C 56, F 9.6; Evidence estimated analogue) | `meal@v6` · prompt 11 · schema 3 |
| `an_7781` | E1 | 2026-10-02T18:00Z | photo | Ready for review (photo held as a raw scan until 2026-11-01) | `meal@v6` |
| `an_5530` | E5 | 2026-10-01T09:04Z | photo + words | **Failed** `AI_UNAVAILABLE` (provider timeout; 12,000 ms; 2 retries) | `meal@v6` · prompt 11 · schema 3 |
| E5's photo analyses | E5 | 2026-10-01 | 25 photos between 07:00 and 16:30 | 25 counted; the 26th at 16:40 refused `RATE_LIMITED`, `resets_at` 2026-10-02T00:00 Cairo | — |
| `an_7740` | E14 | 2026-10-01T09:20Z | photo | **Failed** `AI_UNAVAILABLE` (kill switch On) | `meal@v6` |
| E14's history | E14 | 2026-09-01 → 30 | — | 240 Failed (provider timeouts), none above the daily quota | — |
| `an_6f01`, `an_6f02` | E13 (`acct_anon_71f2`) | 2026-09-29 | photos | Approved, not logged (trial Entries stay on the device, J124) | `meal@v6` |

### §10.3 · Sync rows (support's Sync tab)

E1: command `cmd_7a1e` (a correction from the app 1.0.2 iPhone) **Conflict** · `STALE_REVISION` · 2026-09-30T19:15Z · "waiting for the eater's choice" (J69) · command `cmd_44c0` (consume 3 cheese bites, 2026-09-29T05:12Z) delivered 3 times → one Entry; "Duplicates ignored 2".

### §10.4 · Activity imports

| eater | import | result |
|---|---|---|
| E1 | 2026-10-01T04:30Z (07:30 Riyadh) | accepted 1, updated 0, duplicate 2, conflict 0 |
| Faisal | 2026-10-01T04:30Z (07:30 Riyadh), AT-22 | the Watch walk 06:00–06:45 Riyadh (03:00–03:45Z; 210 kcal), the running app's copy 06:01–06:44 (205 kcal) and the Watch walk sent again → one Activity of 210 kcal; accepted 1, duplicate 2, conflict 0 |
| Sam | 2026-10-01T18:00Z | active energy for 2026-10-01: 520 kcal including the 210 kcal walk 07:00–07:45 London (the walk is inside the aggregate; 730 appears nowhere) |
| `acct_3f88a1` | 2026-09-30T06:00Z | walks on 2026-09-29 and 30 (the Grant `grant_40aa` concerns them) |
| E10 | 2026-09-29T17:00Z | one walk 40 min, 160 kcal on 2026-09-29 |

### §10.5 · The simulator's Health store (HealthKit mock, per account)

Faisal and E1: the walks above. Sam: active-energy samples summing 520 kcal on 2026-10-01; body-mass samples of §5.3 (2026-09-17, 09-24, and 2026-10-01 written at 2026-10-01T06:00:00Z); a story that needs "no new weight since 24 Sept" (e578 eater-8.22) starts at 2026-09-30T12:00:00Z (§2). Every Confirmed Entry of an eater with `health_write_food` Given is written by the confirming iPhone as one food correlation (J123); calorie-only Entries are written with energy only.

### §10.6 · Retention runs (Jobs › Retention; hourly at minute 00)

Deletes audio older than 23 h and raw scans older than 29 d 23 h. Run **R-0805** 2026-10-05T08:00Z: raw scans deleted 2, audio deleted 1, oldest remaining raw scan 29 d 22 h, oldest audio 22 h 10 min, Policy v2. Separate fixture **R-gap** in §13.

### §10.7 · Other jobs

USDA release 15.5 import: Completed 2026-09-25T03:00Z (Platform-admin job; the approver reads it on Foods). Requests received outside the app: none seeded (support-9.14 and 9.15 record them in-story).

---

## §11 · The seeded Audit trail (events 1–266)

Hash chain over all 266; the automated chain check last ran 2026-10-05T08:30:00Z and found 1–266 intact. Names and fields follow `events.md`. Times UTC (2026). Live events of a story start after the last event loaded at its start clock (e.g. the Auditor's sign-in at 2026-10-05T09:00Z is event 267, the overview's query 268).

| # | time (UTC) | actor | action | object · detail | account | outcome · code |
|---|---|---|---|---|---|---|
| 1 | 08-01 06:00:00 | system:deployment | `role.assigned` | Platform admin → staff_ali · First platform admin set at deployment |  | Done |
| 2 | 08-01 06:05:00 | staff_ali | `role.assigned` | Support agent → staff_mona |  | Done |
| 3 | 08-01 06:06:00 | staff_ali | `role.assigned` | Support agent → staff_omar |  | Done |
| 4 | 08-01 06:07:00 | staff_ali | `role.assigned` | Nutrition approver → staff_dina |  | Done |
| 5 | 08-01 06:08:00 | staff_ali | `role.assigned` | Auditor → staff_hana |  | Done |
| 6 | 08-01 06:09:00 | staff_ali | `role.assigned` | Support agent → staff_lee |  | Done |
| 7 | 08-01 07:00:00 | staff_dina | `policy.version.proposed` | Policy v1 |  | Done |
| 8 | 08-01 07:30:00 | staff_dina | `policy.version.approved` | Policy v1 · proposer = approver, sole Nutrition approver · effective 2026-08-02T00:00:00+03:00 |  | Done |
| 9 | 08-01 08:00:00 | staff_ali | `registry.stage.changed` | meal@v6 → Rollout 100 % · launch baseline |  | Done |
| 10 | 08-01 08:01:00 | staff_ali | `registry.stage.changed` | label@v3 → Rollout 100 % · launch baseline |  | Done |
| 11 | 08-01 08:02:00 | staff_ali | `registry.stage.changed` | scale@v2 → Rollout 100 % · launch baseline |  | Done |
| 12 | 08-01 08:03:00 | staff_ali | `registry.stage.changed` | ingredients@v2 → Rollout 100 % · launch baseline |  | Done |
| 13 | 08-01 08:04:00 | staff_ali | `registry.stage.changed` | text@v4 → Rollout 100 % · launch baseline |  | Done |
| 14 | 08-01 08:05:00 | staff_ali | `registry.stage.changed` | voice@v1 → Rollout 100 % · launch baseline |  | Done |
| 15 | 08-01 08:06:00 | staff_ali | `registry.stage.changed` | explain@v1 → Rollout 100 % · launch baseline |  | Done |
| 16 | 08-01 08:10:00 | system:deployment | `quotas.version.saved` | Quotas version 1 · seeded |  | Done |
| 17 | 08-01 08:11:00 | system:deployment | `price.added` | gemini-3.8-flash $0.75 / $3.75 from 2026-08-01 · seeded |  | Done |
| 18 | 08-01 08:12:00 | system:deployment | `price.added` | gemini-3.8-flash $1.50 / $7.50 from 2027-01-01 · seeded |  | Done |
| 19 | 08-01 08:13:00 | system:deployment | `price.added` | gemini-3.5-flash-lite $0.30 / $2.50 from 2026-08-01 · seeded |  | Done |
| 20 | 08-01 08:14:00 | system:deployment | `spend_cap.saved` | AI spend alert level $40 · cap $60 (UTC day) · seeded |  | Done |
| 21 | 08-01 08:15:00 | system:deployment | `grant_settings.version.saved` | Grant settings version 1 · seeded |  | Done |
| 22 | 08-01 12:00:00 | acct_e5c3a0 | `age.confirmed` | age-1 · onboarding_age_question · app 1.0.3 | acct_e5c3a0 | Done |
| 23 | 08-01 12:01:00 | acct_e5c3a0 | `consent.given` | diary_processing · diary-1 · onboarding_choice · app 1.0.3 | acct_e5c3a0 | Done |
| 24 | 08-01 21:00:00 | system | `policy.version.in_effect` | Policy v1 |  | Done |
| 25 | 08-03 18:20:00 | acct_9c41e2 | `age.confirmed` | age-1 · onboarding_age_question · app 1.0.3 | acct_9c41e2 | Done |
| 26 | 08-03 18:21:00 | acct_9c41e2 | `consent.given` | diary_processing · diary-1 · onboarding_choice · app 1.0.3 | acct_9c41e2 | Done |
| 27 | 08-03 18:22:00 | acct_9c41e2 | `consent.given` | photos · photos-1 · first_need_sheet · Capture & Plan · app 1.0.3 | acct_9c41e2 | Done |
| 28 | 08-03 18:22:10 | acct_9c41e2 | `consent.given` | ai_processing · c-ai-3 · first_need_sheet · Capture & Plan · app 1.0.3 | acct_9c41e2 | Done |
| 29 | 08-03 18:25:00 | acct_9c41e2 | `consent.given` | health_read_workouts · health-1 · first_need_sheet · Activity sheet · app 1.0.3 | acct_9c41e2 | Done |
| 30 | 08-03 18:25:01 | acct_9c41e2 | `consent.given` | health_read_active_energy · health-1 · first_need_sheet · Activity sheet · app 1.0.3 | acct_9c41e2 | Done |
| 31 | 08-03 18:25:02 | acct_9c41e2 | `consent.given` | health_read_body_mass · health-1 · first_need_sheet · Activity sheet · app 1.0.3 | acct_9c41e2 | Done |
| 32 | 08-03 18:25:03 | acct_9c41e2 | `consent.given` | health_write_food · health-1 · first_need_sheet · Activity sheet · app 1.0.3 | acct_9c41e2 | Done |
| 33 | 08-03 18:26:00 | acct_9c41e2 | `consent.given` | research · research-1 · settings_privacy · app 1.0.3 | acct_9c41e2 | Done |
| 34 | 08-05 09:00:00 | acct_88e0c1 | `age.confirmed` | age-1 · onboarding_age_question · app 1.0.3 | acct_88e0c1 | Done |
| 35 | 08-05 09:01:00 | acct_88e0c1 | `consent.given` | diary_processing · diary-1 · onboarding_choice · app 1.0.3 | acct_88e0c1 | Done |
| 36 | 08-07 09:00:00 | acct_9a07e5 | `age.confirmed` | age-1 · onboarding_age_question · app 1.0.3 | acct_9a07e5 | Done |
| 37 | 08-07 09:01:00 | acct_9a07e5 | `consent.given` | diary_processing · diary-1 · onboarding_choice · app 1.0.3 | acct_9a07e5 | Done |
| 38 | 08-09 09:00:00 | acct_f1e0c3 | `age.confirmed` | age-1 · onboarding_age_question · app 1.0.3 | acct_f1e0c3 | Done |
| 39 | 08-09 09:01:00 | acct_f1e0c3 | `consent.given` | diary_processing · diary-1 · onboarding_choice · app 1.0.3 | acct_f1e0c3 | Done |
| 40 | 08-10 09:00:00 | acct_51ab07 | `age.confirmed` | age-1 · onboarding_age_question · app 1.0.3 | acct_51ab07 | Done |
| 41 | 08-10 09:01:00 | acct_51ab07 | `consent.given` | diary_processing · diary-1 · onboarding_choice · app 1.0.3 | acct_51ab07 | Done |
| 42 | 08-11 09:00:00 | acct_b81c40 | `age.confirmed` | age-1 · onboarding_age_question · app 1.0.3 | acct_b81c40 | Done |
| 43 | 08-11 09:01:00 | acct_b81c40 | `consent.given` | diary_processing · diary-1 · onboarding_choice · app 1.0.3 | acct_b81c40 | Done |
| 44 | 08-12 09:00:00 | acct_e07d13 | `age.confirmed` | age-1 · onboarding_age_question · app 1.0.3 | acct_e07d13 | Done |
| 45 | 08-12 09:01:00 | acct_e07d13 | `consent.given` | diary_processing · diary-1 · onboarding_choice · app 1.0.3 | acct_e07d13 | Done |
| 46 | 08-13 09:00:00 | acct_e5a930 | `age.confirmed` | age-1 · onboarding_age_question · app 1.0.3 | acct_e5a930 | Done |
| 47 | 08-13 09:01:00 | acct_e5a930 | `consent.given` | diary_processing · diary-1 · onboarding_choice · app 1.0.3 | acct_e5a930 | Done |
| 48 | 08-14 09:00:00 | acct_77d2c0 | `age.confirmed` | age-1 · onboarding_age_question · app 1.0.3 | acct_77d2c0 | Done |
| 49 | 08-14 09:01:00 | acct_77d2c0 | `consent.given` | diary_processing · diary-1 · onboarding_choice · app 1.0.3 | acct_77d2c0 | Done |
| 50 | 08-15 09:00:00 | acct_3f88a1 | `age.confirmed` | age-1 · onboarding_age_question · app 1.0.3 | acct_3f88a1 | Done |
| 51 | 08-15 09:01:00 | acct_3f88a1 | `consent.given` | diary_processing · diary-1 · onboarding_choice · app 1.0.3 | acct_3f88a1 | Done |
| 52 | 08-16 09:00:00 | acct_b6f204 | `age.confirmed` | age-1 · onboarding_age_question · app 1.0.3 | acct_b6f204 | Done |
| 53 | 08-16 09:01:00 | acct_b6f204 | `consent.given` | diary_processing · diary-1 · onboarding_choice · app 1.0.3 | acct_b6f204 | Done |
| 54 | 08-16 09:02:00 | acct_b6f204 | `consent.given` | photos · photos-1 · first_need_sheet · Capture & Plan · app 1.0.3 | acct_b6f204 | Done |
| 55 | 08-16 09:02:10 | acct_b6f204 | `consent.given` | ai_processing · c-ai-3 · first_need_sheet · Capture & Plan · app 1.0.3 | acct_b6f204 | Done |
| 56 | 08-16 12:00:00 | acct_c2d7e5 | `age.confirmed` | age-1 · onboarding_age_question · app 1.0.3 | acct_c2d7e5 | Done |
| 57 | 08-16 12:01:00 | acct_c2d7e5 | `consent.given` | diary_processing · diary-1 · onboarding_choice · app 1.0.3 | acct_c2d7e5 | Done |
| 58 | 08-17 09:00:00 | acct_8e14d9 | `age.confirmed` | age-1 · onboarding_age_question · app 1.0.3 | acct_8e14d9 | Done |
| 59 | 08-17 09:01:00 | acct_8e14d9 | `consent.given` | diary_processing · diary-1 · onboarding_choice · app 1.0.3 | acct_8e14d9 | Done |
| 60 | 08-18 09:00:00 | acct_a41c55 | `age.confirmed` | age-1 · onboarding_age_question · app 1.0.3 | acct_a41c55 | Done |
| 61 | 08-18 09:01:00 | acct_a41c55 | `consent.given` | diary_processing · diary-1 · onboarding_choice · app 1.0.3 | acct_a41c55 | Done |
| 62 | 08-19 09:00:00 | acct_d40e17 | `age.confirmed` | age-1 · onboarding_age_question · app 1.0.3 | acct_d40e17 | Done |
| 63 | 08-19 09:01:00 | acct_d40e17 | `consent.given` | diary_processing · diary-1 · onboarding_choice · app 1.0.3 | acct_d40e17 | Done |
| 64 | 08-20 05:00:00 | acct_e9a001 | `age.confirmed` | age-1 · onboarding_age_question · app 1.0.3 | acct_e9a001 | Done |
| 65 | 08-20 05:01:00 | acct_e9a001 | `consent.given` | diary_processing · diary-1 · onboarding_choice · app 1.0.3 | acct_e9a001 | Done |
| 66 | 08-20 05:02:00 | acct_e9a001 | `consent.given` | photos · photos-1 · first_need_sheet · Capture & Plan · app 1.0.3 | acct_e9a001 | Done |
| 67 | 08-20 05:02:10 | acct_e9a001 | `consent.given` | ai_processing · c-ai-3 · first_need_sheet · Capture & Plan · app 1.0.3 | acct_e9a001 | Done |
| 68 | 08-20 08:00:00 | acct_88e0c1 | `privacy_job.requested` | deletion job_del_2120 · DEL-26-0820-M2V5 | acct_88e0c1 | Done |
| 69 | 08-22 09:00:00 | acct_7b12aa | `age.confirmed` | age-1 · onboarding_age_question · app 1.0.3 | acct_7b12aa | Done |
| 70 | 08-22 09:01:00 | acct_7b12aa | `consent.given` | diary_processing · diary-1 · onboarding_choice · app 1.0.3 | acct_7b12aa | Done |
| 71 | 08-22 09:02:00 | acct_7b12aa | `consent.given` | photos · photos-1 · first_need_sheet · Capture & Plan · app 1.0.3 | acct_7b12aa | Done |
| 72 | 08-22 09:02:10 | acct_7b12aa | `consent.given` | ai_processing · c-ai-3 · first_need_sheet · Capture & Plan · app 1.0.3 | acct_7b12aa | Done |
| 73 | 08-23 09:00:00 | acct_f61c22 | `age.confirmed` | age-1 · onboarding_age_question · app 1.0.3 | acct_f61c22 | Done |
| 74 | 08-23 09:01:00 | acct_f61c22 | `consent.given` | diary_processing · diary-1 · onboarding_choice · app 1.0.3 | acct_f61c22 | Done |
| 75 | 08-25 15:00:00 | acct_e9a002 | `age.confirmed` | age-1 · onboarding_age_question · app 1.0.3 | acct_e9a002 | Done |
| 76 | 08-25 15:01:00 | acct_e9a002 | `consent.given` | diary_processing · diary-1 · onboarding_choice · app 1.0.3 | acct_e9a002 | Done |
| 77 | 08-25 15:02:00 | acct_e9a002 | `consent.given` | photos · photos-1 · first_need_sheet · Capture & Plan · app 1.0.3 | acct_e9a002 | Done |
| 78 | 08-25 15:02:10 | acct_e9a002 | `consent.given` | ai_processing · c-ai-3 · first_need_sheet · Capture & Plan · app 1.0.3 | acct_e9a002 | Done |
| 79 | 08-25 15:02:20 | acct_e9a002 | `consent.given` | microphone · mic-1 · first_need_sheet · Capture & Plan · app 1.0.3 | acct_e9a002 | Done |
| 80 | 08-25 15:10:00 | acct_e9a002 | `consent.given` | health_read_workouts · health-1 · first_need_sheet · Activity sheet · app 1.0.3 | acct_e9a002 | Done |
| 81 | 08-25 15:10:01 | acct_e9a002 | `consent.given` | health_read_active_energy · health-1 · first_need_sheet · Activity sheet · app 1.0.3 | acct_e9a002 | Done |
| 82 | 08-25 15:10:02 | acct_e9a002 | `consent.given` | health_write_food · health-1 · first_need_sheet · Activity sheet · app 1.0.3 | acct_e9a002 | Done |
| 83 | 08-26 09:00:00 | acct_0a7b55 | `age.confirmed` | age-1 · onboarding_age_question · app 1.0.3 | acct_0a7b55 | Done |
| 84 | 08-26 09:01:00 | acct_0a7b55 | `consent.given` | diary_processing · diary-1 · onboarding_choice · app 1.0.3 | acct_0a7b55 | Done |
| 85 | 08-28 09:00:00 | acct_d13a01 | `age.confirmed` | age-1 · onboarding_age_question · app 1.0.3 | acct_d13a01 | Done |
| 86 | 08-28 09:01:00 | acct_d13a01 | `consent.given` | diary_processing · diary-1 · onboarding_choice · app 1.0.3 | acct_d13a01 | Done |
| 87 | 09-02 06:00:00 | acct_b81c40 | `privacy_job.requested` | deletion job_del_2201 · DEL-26-0902-A7K1 | acct_b81c40 | Done |
| 88 | 09-03 09:00:00 | acct_c2a917 | `age.confirmed` | age-1 · onboarding_age_question · app 1.0.3 | acct_c2a917 | Done |
| 89 | 09-03 09:01:00 | acct_c2a917 | `consent.given` | diary_processing · diary-1 · onboarding_choice · app 1.0.3 | acct_c2a917 | Done |
| 90 | 09-03 09:02:00 | acct_c2a917 | `consent.given` | photos · photos-1 · first_need_sheet · Capture & Plan · app 1.0.3 | acct_c2a917 | Done |
| 91 | 09-03 09:02:10 | acct_c2a917 | `consent.given` | ai_processing · c-ai-3 · first_need_sheet · Capture & Plan · app 1.0.3 | acct_c2a917 | Done |
| 92 | 09-03 09:02:20 | acct_c2a917 | `consent.given` | microphone · mic-1 · first_need_sheet · Capture & Plan · app 1.0.3 | acct_c2a917 | Done |
| 93 | 09-04 09:00:00 | acct_e5a930 | `privacy_job.requested` | deletion job_del_2204 · DEL-26-0904-B9T2 | acct_e5a930 | Done |
| 94 | 09-05 06:00:00 | staff_ali | `role.assigned` | Support agent → staff_tariq |  | Done |
| 95 | 09-05 08:00:00 | acct_9a07e5 | `privacy_job.requested` | deletion job_del_2205 · DEL-26-0905-P7T2 | acct_9a07e5 | Done |
| 96 | 09-08 09:00:00 | acct_e9a003 | `age.confirmed` | age-1 · onboarding_age_question · app 1.0.3 | acct_e9a003 | Done |
| 97 | 09-08 09:01:00 | acct_e9a003 | `consent.given` | diary_processing · diary-1 · onboarding_choice · app 1.0.3 | acct_e9a003 | Done |
| 98 | 09-08 09:02:00 | acct_e9a003 | `consent.given` | photos · photos-1 · first_need_sheet · Capture & Plan · app 1.0.3 | acct_e9a003 | Done |
| 99 | 09-08 09:02:10 | acct_e9a003 | `consent.given` | ai_processing · c-ai-3 · first_need_sheet · Capture & Plan · app 1.0.3 | acct_e9a003 | Done |
| 100 | 09-08 09:02:20 | acct_e9a003 | `consent.given` | microphone · mic-1 · first_need_sheet · Capture & Plan · app 1.0.3 | acct_e9a003 | Done |
| 101 | 09-08 09:30:00 | acct_e9a003 | `consent.given` | health_read_workouts · health-1 · first_need_sheet · Activity sheet · app 1.0.3 | acct_e9a003 | Done |
| 102 | 09-08 09:30:01 | acct_e9a003 | `consent.given` | health_read_active_energy · health-1 · first_need_sheet · Activity sheet · app 1.0.3 | acct_e9a003 | Done |
| 103 | 09-08 09:30:02 | acct_e9a003 | `consent.given` | health_write_food · health-1 · first_need_sheet · Activity sheet · app 1.0.3 | acct_e9a003 | Done |
| 104 | 09-09 09:00:00 | acct_f61c22 | `privacy_job.requested` | deletion job_del_2209 · DEL-26-0909-C2M8 | acct_f61c22 | Done |
| 105 | 09-10 06:00:00 | staff_ali | `role.assigned` | Nutrition approver → staff_yara |  | Done |
| 106 | 09-10 09:00:00 | staff_tariq | `grant.requested` | grant_27b4 · report_mismatch · Days 09-08–09-09 · Entries and day reports · 1 h · CASE-1101 | acct_d40e17 | Done |
| 107 | 09-10 09:10:00 | acct_d40e17 | `grant.approved` | grant_27b4 · Active until 10:10:00 · in_app | acct_d40e17 | Done |
| 108 | 09-10 09:15:00 | staff_tariq | `grant.read` | grant_27b4 · Day 2026-09-09 · day report | acct_d40e17 | Allowed |
| 109 | 09-10 10:10:00 | system | `grant.expired` | grant_27b4 | acct_d40e17 | Done |
| 110 | 09-12 08:00:00 | acct_d40e17 | `privacy_job.requested` | deletion job_del_2212 · DEL-26-0912-F3P5 | acct_d40e17 | Done |
| 111 | 09-12 09:00:00 | staff_yara | `food.version.proposed` | rec_fm_eg v1 (Tier B recipe record) |  | Done |
| 112 | 09-12 09:40:00 | staff_dina | `food.version.approved` | rec_fm_eg v1 · licence, ev_1101, ev_1102 |  | Done |
| 113 | 09-12 10:00:00 | staff_dina | `alias.proposed` | al_saqai · Gulf |  | Done |
| 114 | 09-12 10:15:00 | staff_yara | `alias.approved` | al_saqai · Gulf |  | Done |
| 115 | 09-12 10:20:00 | staff_dina | `alias.proposed` | al_laban_eg · EG |  | Done |
| 116 | 09-12 10:30:00 | staff_yara | `alias.approved` | al_laban_eg · EG |  | Done |
| 117 | 09-12 10:35:00 | staff_dina | `alias.proposed` | al_laban_gulf · Gulf |  | Done |
| 118 | 09-12 10:45:00 | staff_yara | `alias.approved` | al_laban_gulf · Gulf |  | Done |
| 119 | 09-13 09:00:00 | acct_d13a01 | `privacy_job.requested` | deletion job_del_2213 · DEL-26-0913-H5W3 | acct_d13a01 | Done |
| 120 | 09-15 07:00:00 | acct_51ab07 | `privacy_job.requested` | deletion job_del_2215 · DEL-26-0915-K3Q8 | acct_51ab07 | Done |
| 121 | 09-15 08:00:00 | staff_ali | `role.created` | custom role Access manager (Read roles, Change roles) |  | Done |
| 122 | 09-15 08:05:00 | staff_ali | `role.assigned` | Access manager → staff_rana |  | Done |
| 123 | 09-15 10:00:00 | staff_ali | `role.change_refused` | Auditor → staff_ali · self-change |  | Refused · VALIDATION_ERROR |
| 124 | 09-15 12:00:00 | acct_9c41e2 | `privacy_job.requested` | export job_exp_4402 | acct_9c41e2 | Done |
| 125 | 09-15 12:04:00 | system | `privacy_job.completed` | export job_exp_4402 | acct_9c41e2 | Done |
| 126 | 09-15 12:10:00 | acct_9c41e2 | `privacy_job.export_downloaded` | export job_exp_4402 | acct_9c41e2 | Done |
| 127 | 09-16 06:30:00 | system | `privacy_job.completed` | deletion job_del_2201 · DEL-26-0902-A7K1 | acct_b81c40 | Done |
| 128 | 09-16 08:00:00 | acct_e9a003 | `consent.given` | health_read_body_mass · health-1 · settings_privacy · app 1.0.3 | acct_e9a003 | Done |
| 129 | 09-18 10:00:00 | system | `privacy_job.completed` | deletion job_del_2120 · DEL-26-0820-M2V5 | acct_88e0c1 | Done |
| 130 | 09-20 07:45:00 | acct_9c41e2 | `consent.withdrawn` | ai_processing · c-ai-3 · settings_privacy · app 1.0.3 | acct_9c41e2 | Done |
| 131 | 09-20 07:45:00 | system | `privacy_job.requested` | consent withdrawal job_cw_4501 (ai_processing) | acct_9c41e2 | Done |
| 132 | 09-20 07:45:05 | system | `privacy_job.completed` | consent withdrawal job_cw_4501 | acct_9c41e2 | Done |
| 133 | 09-20 08:00:00 | system | `privacy_job.completed` | deletion job_del_2212 · DEL-26-0912-F3P5 | acct_d40e17 | Done |
| 134 | 09-20 09:00:00 | acct_0d4e9b | `age.confirmed` | age-1 · onboarding_age_question · app 1.0.3 | acct_0d4e9b | Done |
| 135 | 09-20 09:01:00 | acct_0d4e9b | `consent.given` | diary_processing · diary-1 · onboarding_choice · app 1.0.3 | acct_0d4e9b | Done |
| 136 | 09-20 09:02:00 | acct_0d4e9b | `consent.given` | photos · photos-1 · first_need_sheet · Capture & Plan · app 1.0.3 | acct_0d4e9b | Done |
| 137 | 09-20 09:02:10 | acct_0d4e9b | `consent.given` | ai_processing · c-ai-3 · first_need_sheet · Capture & Plan · app 1.0.3 | acct_0d4e9b | Done |
| 138 | 09-20 09:02:20 | acct_0d4e9b | `consent.given` | microphone · mic-1 · first_need_sheet · Capture & Plan · app 1.0.3 | acct_0d4e9b | Done |
| 139 | 09-20 11:55:00 | acct_a41c55 | `privacy_job.requested` | export job_exp_31 | acct_a41c55 | Done |
| 140 | 09-20 12:00:00 | system | `privacy_job.completed` | export job_exp_31 | acct_a41c55 | Done |
| 141 | 09-22 06:05:30 | acct_9c41e2 | `consent.withdrawn` | research · research-1 · settings_privacy · app 1.0.2 · made on the device 2026-09-21T22:40:00Z · delivered twice | acct_9c41e2 | Done |
| 142 | 09-22 08:00:00 | staff_ali | `registry.version.proposed` | meal@v7 · prompt version 12 asks about oil and ghee |  | Done |
| 143 | 09-23 08:00:00 | staff_ali | `registry.stage.changed` | meal@v7 Proposed → Shadow · candidate gets copies; v6 answers |  | Done |
| 144 | 09-24 07:55:00 | staff_ali | `launch_gate.signed` | Privacy review of c-ai-4 · signed by counsel R. Haddad (synthetic) |  | Done |
| 145 | 09-24 08:00:00 | staff_ali | `wording.published` | c-ai-4 (English, Arabic) |  | Done |
| 146 | 09-25 08:00:00 | staff_ali | `registry.stage.changed` | meal@v7 Shadow → Canary 5 % · shadow agreement within threshold |  | Done |
| 147 | 09-25 15:00:00 | staff_ali | `role.removed` | Support agent ← staff_tariq · left the team |  | Done |
| 148 | 09-25 20:10:00 | acct_9c41e2 | `consent.given` | ai_processing · c-ai-4 · first_need_sheet · Capture & Plan · app 1.0.3 | acct_9c41e2 | Done |
| 149 | 09-26 09:00:00 | acct_e9a001 | `consent.given` | label_review · label-1 · first_need_sheet · Unit editor · app 1.0.3 | acct_e9a001 | Done |
| 150 | 09-27 08:00:00 | staff_ali | `registry.stage.changed` | meal@v7 Canary → Rollout 100 %; meal@v6 → Replaced · canary metrics within threshold |  | Done |
| 151 | 09-27 10:00:00 | acct_e9a003 | `consent.given` | label_review · label-1 · first_need_sheet · Unit editor · app 1.0.3 | acct_e9a003 | Done |
| 152 | 09-27 10:05:00 | staff_mona | `grant.requested` | grant_40ab · sync_missing_entry · Days 09-24–09-26 · Entries and day reports · 1 h · CASE-1170 | acct_f1e0c3 | Done |
| 153 | 09-27 10:12:00 | staff_ali | `registry.kill_switch.on` | meal · AI timeouts |  | Done |
| 154 | 09-27 10:47:00 | staff_ali | `registry.kill_switch.off` | meal |  | Done |
| 155 | 09-27 11:30:00 | staff_ali | `registry.stage.changed` | meal@v7 Rollout → Rolled back; meal@v6 → Rollout · photo drafts missing oil items |  | Done |
| 156 | 09-28 10:00:00 | acct_0a7b55 | `privacy_job.requested` | deletion job_del_2228 · DEL-26-0928-D4Q6 | acct_0a7b55 | Done |
| 157 | 09-28 12:00:00 | acct_e9a002 | `consent.given` | label_review · label-1 · first_need_sheet · Unit editor · app 1.0.3 | acct_e9a002 | Done |
| 158 | 09-29 07:30:00 | staff_yara | `policy.version.proposed` | Policy v2 · energy mismatch >10 % → >12 % |  | Done |
| 159 | 09-29 08:00:00 | staff_dina | `policy.version.approved` | Policy v2 · proposer staff_yara · effective 2026-10-05T00:00:00+03:00 |  | Done |
| 160 | 09-29 09:00:00 | acct_anon_71f2 | `age.confirmed` | age-1 · onboarding_age_question · app 1.0.3 | acct_anon_71f2 | Done |
| 161 | 09-29 09:01:00 | acct_anon_71f2 | `consent.given` | ai_processing · c-ai-4 · onboarding_switch · app 1.0.3 | acct_anon_71f2 | Done |
| 162 | 09-29 12:00:00 | system | `privacy_job.failed` | deletion job_del_2213 · stage photos and audio deleted · storage_timeout · 3 of 5 attempts | acct_d13a01 | Failed · SERVICE_UNAVAILABLE |
| 163 | 09-30 06:40:00 | system | `privacy_job.failed` | deletion job_del_2205 · stage processors told (Google) · processor_timeout · 3 of 5 attempts | acct_9a07e5 | Failed · SERVICE_UNAVAILABLE |
| 164 | 09-30 09:00:00 | staff_ali | `role.assigned` | Platform admin → staff_badr |  | Done |
| 165 | 09-30 10:00:00 | acct_e9a005 | `age.confirmed` | age-1 · onboarding_age_question · app 1.0.3 | acct_e9a005 | Done |
| 166 | 09-30 10:01:00 | acct_e9a005 | `consent.given` | diary_processing · diary-1 · onboarding_choice · app 1.0.3 | acct_e9a005 | Done |
| 167 | 09-30 10:05:00 | system | `grant.unanswered` | grant_40ab · 72 h request window closed | acct_f1e0c3 | Done |
| 168 | 09-30 11:00:00 | acct_e9a006 | `age.confirmed` | age-1 · onboarding_age_question · app 1.0.3 | acct_e9a006 | Done |
| 169 | 09-30 11:01:00 | acct_e9a006 | `consent.given` | diary_processing · diary-1 · onboarding_choice · app 1.0.3 | acct_e9a006 | Done |
| 170 | 09-30 15:02:00 | acct_a41c55 | `privacy_job.requested` | export job_exp_77 | acct_a41c55 | Done |
| 171 | 09-30 15:09:00 | system | `privacy_job.completed` | export job_exp_77 | acct_a41c55 | Done |
| 172 | 09-30 18:14:00 | acct_c2a917 | `consent.withdrawn` | ai_processing · c-ai-3 · settings_privacy · app 1.0.3 | acct_c2a917 | Done |
| 173 | 09-30 18:14:00 | system | `privacy_job.requested` | consent withdrawal job_cw_1814 (ai_processing) | acct_c2a917 | Done |
| 174 | 09-30 18:20:00 | system | `privacy_job.completed` | consent withdrawal job_cw_1814 | acct_c2a917 | Done |
| 175 | 10-01 05:00:00 | acct_e9a004 | `age.confirmed` | age-1 · onboarding_age_question · app 1.0.3 | acct_e9a004 | Done |
| 176 | 10-01 05:01:00 | acct_e9a004 | `consent.given` | diary_processing · diary-1 · onboarding_choice · app 1.0.3 | acct_e9a004 | Done |
| 177 | 10-01 07:55:00 | staff_omar | `grant.requested` | grant_a1d4 · report_mismatch · Days 09-29–09-30 · Entries and day reports · 1 h · CASE-1250 | acct_c2a917 | Done |
| 178 | 10-01 07:58:00 | acct_e07d13 | `privacy_job.requested` | export job_exp_4410 | acct_e07d13 | Done |
| 179 | 10-01 08:00:00 | acct_c2a917 | `grant.approved` | grant_a1d4 · Active until 09:00:00 · in_app | acct_c2a917 | Done |
| 180 | 10-01 08:20:00 | staff_omar | `grant.requested` | grant_8e20 · unit_calculation · Day 09-30 · My Units · 1 h · CASE-1205 | acct_0d4e9b | Done |
| 181 | 10-01 08:25:00 | acct_0d4e9b | `grant.approved` | grant_8e20 · Active until 09:25:00 · in_app | acct_0d4e9b | Done |
| 182 | 10-01 08:40:00 | system | `privacy_job.failed` | export job_exp_4410 · storage_timeout · 3 of 5 attempts | acct_e07d13 | Failed · SERVICE_UNAVAILABLE |
| 183 | 10-01 09:00:00 | system | `grant.expired` | grant_a1d4 | acct_c2a917 | Done |
| 184 | 10-01 09:00:00 | staff_mona | `account.lookup` | support code SB-7KQ2-94XM · method support_code | acct_9c41e2 | Done |
| 185 | 10-01 09:00:01 | staff_mona | `account.viewed` | account panel | acct_9c41e2 | Allowed |
| 186 | 10-01 09:02:00 | staff_mona | `account.jobs_viewed` | Sync | acct_9c41e2 | Allowed |
| 187 | 10-01 09:10:00 | staff_ali | `registry.kill_switch.on` | meal · AI timeouts |  | Done |
| 188 | 10-01 09:25:00 | system | `grant.expired` | grant_8e20 | acct_0d4e9b | Done |
| 189 | 10-01 09:30:00 | staff_lee | `staff.sign_in_failed` | console · attempt 1 of 5 |  | Refused · UNAUTHENTICATED |
| 190 | 10-01 09:30:30 | staff_lee | `staff.sign_in_failed` | console · attempt 2 of 5 |  | Refused · UNAUTHENTICATED |
| 191 | 10-01 09:31:00 | staff_lee | `staff.sign_in_failed` | console · attempt 3 of 5 |  | Refused · UNAUTHENTICATED |
| 192 | 10-01 09:31:30 | staff_lee | `staff.sign_in_failed` | console · attempt 4 of 5 |  | Refused · UNAUTHENTICATED |
| 193 | 10-01 09:32:00 | staff_lee | `staff.sign_in_failed` | console · attempt 5 of 5 |  | Refused · UNAUTHENTICATED |
| 194 | 10-01 09:32:00 | system | `staff.sign_in_locked` | staff_lee · locked until 09:47:00 (5 failures in 15 min) |  | Done |
| 195 | 10-01 09:55:00 | staff_ali | `registry.kill_switch.off` | meal |  | Done |
| 196 | 10-01 10:05:00 | staff_mona | `grant.requested` | grant_31f0 · sync_missing_entry · Days 09-28–09-30 · Entries and day reports + My Units · 1 h · CASE-1182 | acct_9c41e2 | Done |
| 197 | 10-01 10:09:00 | staff_omar | `grant.requested` | grant_40aa · activity_import · Days 09-29–09-30 · Activity · 4 h · CASE-1183 | acct_3f88a1 | Done |
| 198 | 10-01 10:20:00 | acct_9c41e2 | `grant.approved` | grant_31f0 · Active until 11:20:00 · in_app, Settings → Privacy → Grants · app 1.0.3, iOS 26.1 | acct_9c41e2 | Done |
| 199 | 10-01 10:24:00 | staff_mona | `grant.read` | grant_31f0 · Day 2026-09-29 · 7 Entries | acct_9c41e2 | Allowed |
| 200 | 10-01 10:25:00 | staff_mona | `grant.read` | grant_31f0 · Entry en_9921 (Day 2026-09-29) | acct_9c41e2 | Allowed |
| 201 | 10-01 10:27:00 | staff_mona | `grant.read` | grant_31f0 · My Units · 12 Unit versions | acct_9c41e2 | Allowed |
| 202 | 10-01 10:40:00 | acct_a41c55 | `privacy_job.requested` | export job_exp_88 | acct_a41c55 | Done |
| 203 | 10-01 10:47:00 | system | `privacy_job.completed` | export job_exp_88 | acct_a41c55 | Done |
| 204 | 10-01 11:05:00 | staff_mona | `job.escalated` | deletion job_del_2205 · CASE-1201 | acct_9a07e5 | Done |
| 205 | 10-01 11:20:00 | system | `grant.expired` | grant_31f0 | acct_9c41e2 | Done |
| 206 | 10-01 11:20:01 | staff_mona | `grant.read_refused` | grant_31f0 · Day 2026-09-30 · state Expired | acct_9c41e2 | Refused · GRANT_NOT_ACTIVE |
| 207 | 10-01 11:20:05 | staff_mona | `grant.read_refused` | grant_31f0 · Day 2026-09-30 · state Expired | acct_9c41e2 | Refused · GRANT_NOT_ACTIVE |
| 208 | 10-01 11:30:00 | staff_mona | `grant.requested` | grant_31f9 · sync_missing_entry · Days 09-28–09-30 · Entries and day reports + My Units · 1 h · CASE-1182 | acct_9c41e2 | Done |
| 209 | 10-01 11:42:00 | acct_9c41e2 | `grant.declined` | grant_31f9 · in_app | acct_9c41e2 | Done |
| 210 | 10-01 11:45:00 | staff_mona | `grant.read_refused` | grant_31f9 · Day 2026-09-29 · state Declined | acct_9c41e2 | Refused · GRANT_NOT_ACTIVE |
| 211 | 10-01 12:00:00 | staff_mona | `grant.requested` | grant_52a3 · report_mismatch · Days 09-29–09-30 · Entries and day reports · 1 h · CASE-1220 | acct_f1e0c3 | Done |
| 212 | 10-01 12:05:00 | acct_f1e0c3 | `grant.approved` | grant_52a3 · Active until 13:05:00 · in_app | acct_f1e0c3 | Done |
| 213 | 10-01 12:10:00 | staff_mona | `grant.read` | grant_52a3 · Day 2026-09-29 | acct_f1e0c3 | Allowed |
| 214 | 10-01 12:26:00 | acct_f1e0c3 | `grant.withdrawn` | grant_52a3 · by the eater | acct_f1e0c3 | Done |
| 215 | 10-01 13:00:00 | staff_mona | `grant.requested` | grant_52a7 · unit_calculation · Day 09-30 · My Units · 1 h · CASE-1221 | acct_f1e0c3 | Done |
| 216 | 10-01 13:04:00 | acct_f1e0c3 | `grant.approved` | grant_52a7 · Active until 14:04:00 · in_app | acct_f1e0c3 | Done |
| 217 | 10-01 13:10:00 | staff_mona | `grant.read` | grant_52a7 · My Units | acct_f1e0c3 | Allowed |
| 218 | 10-01 13:33:00 | staff_mona | `grant.ended` | grant_52a7 · by the Support agent | acct_f1e0c3 | Done |
| 219 | 10-01 14:10:00 | staff_ali | `job.retried` | deletion job_del_2205 · stage processors told (Google) · attempt 4 of 5 | acct_9a07e5 | Done |
| 220 | 10-01 14:12:00 | system | `job.escalation_resolved` | deletion job_del_2205 · stage Completed; job Running | acct_9a07e5 | Done |
| 221 | 10-01 14:58:00 | staff_mona | `grant.requested` | grant_6c10 · activity_import · Days 09-29–09-30 · Activity · 1 h · CASE-1236 | acct_f1e0c3 | Done |
| 222 | 10-01 15:02:00 | acct_f1e0c3 | `grant.approved` | grant_6c10 · Active until 16:02:00 · in_app | acct_f1e0c3 | Done |
| 223 | 10-01 15:05:00 | staff_mona | `grant.read` | grant_6c10 · Activity · Day 2026-09-29 | acct_f1e0c3 | Allowed |
| 224 | 10-01 15:25:00 | system | `staff.session_ended` | staff_mona · idle 15 min |  | Done |
| 225 | 10-01 15:28:00 | staff_mona | `staff.signed_in` | console · password + authenticator |  | Done |
| 226 | 10-01 15:28:30 | staff_mona | `account.lookup` | grant_6c10 · method grant | acct_f1e0c3 | Done |
| 227 | 10-01 15:29:00 | staff_mona | `grant.requested` | grant_7d01 · report_mismatch · Days 09-29–09-30 · Entries and day reports · 1 h · CASE-1225 | acct_77d2c0 | Done |
| 228 | 10-01 15:30:00 | acct_77d2c0 | `grant.approved` | grant_7d01 · Active until 16:30:00 · in_app | acct_77d2c0 | Done |
| 229 | 10-01 15:35:00 | staff_mona | `grant.read` | grant_7d01 · Day 2026-09-29 · day report | acct_77d2c0 | Allowed |
| 230 | 10-01 15:40:00 | staff_mona | `grant.read_refused` | grant_7d01 · Day 2026-09-28 · outside_days | acct_77d2c0 | Refused · GRANT_REQUIRED |
| 231 | 10-01 15:41:00 | staff_mona | `grant.read_refused` | grant_7d01 · My Units · outside_areas | acct_77d2c0 | Refused · GRANT_REQUIRED |
| 232 | 10-01 15:42:00 | staff_mona | `grant.read_refused` | grant_7d01 · Activity · Day 2026-09-29 · outside_areas | acct_77d2c0 | Refused · GRANT_REQUIRED |
| 233 | 10-01 15:43:00 | staff_mona | `grant.read_refused` | grant_7d01 · a Day id of acct_51ab07 · another eater's id | acct_77d2c0 | Refused · NOT_FOUND |
| 234 | 10-01 15:45:00 | staff_mona | `grant.write_refused` | grant_7d01 · POST /v1/consumption | acct_77d2c0 | Refused · FORBIDDEN |
| 235 | 10-01 15:45:01 | staff_mona | `grant.write_refused` | grant_7d01 · POST /v1/consumption/{id}/corrections | acct_77d2c0 | Refused · FORBIDDEN |
| 236 | 10-01 15:45:02 | staff_mona | `grant.write_refused` | grant_7d01 · POST /v1/consumption/{id}/void | acct_77d2c0 | Refused · FORBIDDEN |
| 237 | 10-01 15:45:03 | staff_mona | `grant.write_refused` | grant_7d01 · POST /v1/units | acct_77d2c0 | Refused · FORBIDDEN |
| 238 | 10-01 15:45:04 | staff_mona | `grant.write_refused` | grant_7d01 · POST /v1/recipes | acct_77d2c0 | Refused · FORBIDDEN |
| 239 | 10-01 15:55:00 | staff_mona | `grant.requested` | grant_6c14 · activity_import · Days 09-29–09-30 · Activity · 1 h · CASE-1236 | acct_f1e0c3 | Done |
| 240 | 10-01 16:02:00 | system | `grant.expired` | grant_6c10 | acct_f1e0c3 | Done |
| 241 | 10-01 16:30:00 | system | `grant.expired` | grant_7d01 | acct_77d2c0 | Done |
| 242 | 10-01 16:55:00 | staff_mona | `grant.requested` | grant_9b30 · report_mismatch · Day 09-30 · Entries and day reports · 1 h · CASE-1240 | acct_a41c55 | Done |
| 243 | 10-01 17:00:00 | acct_a41c55 | `grant.approved` | grant_9b30 · Active until 18:00:00 · in_app | acct_a41c55 | Done |
| 244 | 10-01 17:05:00 | staff_mona | `grant.read` | grant_9b30 · Day 2026-09-30 | acct_a41c55 | Allowed |
| 245 | 10-01 17:10:00 | staff_mona | `grant.read` | grant_9b30 · Day 2026-09-30 · nothing shown | acct_a41c55 | Failed · SERVICE_UNAVAILABLE |
| 246 | 10-01 17:25:00 | staff_mona | `grant.ended` | grant_9b30 · by the Support agent (sent after reconnecting; pressed 17:22) | acct_a41c55 | Done |
| 247 | 10-02 08:00:00 | staff_omar | `grant.requested` | grant_52c3 · unit_calculation · Day 10-01 · My Units · 4 h · CASE-1191 | acct_c2d7e5 | Done |
| 248 | 10-02 08:05:00 | acct_c2d7e5 | `grant.approved` | grant_52c3 · Active until 12:05:00 · in_app | acct_c2d7e5 | Done |
| 249 | 10-02 08:10:00 | staff_omar | `grant.read` | grant_52c3 · My Units · 5 Unit versions | acct_c2d7e5 | Allowed |
| 250 | 10-02 08:30:00 | acct_c2d7e5 | `grant.withdrawn` | grant_52c3 · by the eater | acct_c2d7e5 | Done |
| 251 | 10-02 08:31:00 | staff_omar | `grant.read_refused` | grant_52c3 · My Units · state Withdrawn | acct_c2d7e5 | Refused · GRANT_NOT_ACTIVE |
| 252 | 10-02 13:00:00 | staff_mona | `grant.requested` | grant_5d10 · sync_missing_entry · Days 10-01–10-02 · Entries and day reports · 1 h · CASE-1192 | acct_9c41e2 | Done |
| 253 | 10-02 13:02:00 | acct_9c41e2 | `grant.approved` | grant_5d10 · Active until 14:02:00 · in_app | acct_9c41e2 | Done |
| 254 | 10-02 13:05:00 | staff_mona | `grant.read` | grant_5d10 · Day 2026-10-01 · 4 Entries | acct_9c41e2 | Allowed |
| 255 | 10-02 13:20:00 | staff_mona | `grant.ended` | grant_5d10 · by the Support agent | acct_9c41e2 | Done |
| 256 | 10-03 06:00:00 | system | `privacy_job.completed` | deletion job_del_2205 · DEL-26-0905-P7T2 · processor confirmation received 2026-10-03T05:40:00Z | acct_9a07e5 | Done |
| 257 | 10-03 09:00:00 | staff_omar | `grant.requested` | grant_6e21 · report_mismatch · Day 10-02 · Entries and day reports · 1 h · CASE-1195 | acct_9c41e2 | Done |
| 258 | 10-03 09:03:00 | acct_9c41e2 | `grant.approved` | grant_6e21 · Active until 10:03:00 · in_app · command delivered 3 times | acct_9c41e2 | Done |
| 259 | 10-03 10:03:00 | system | `grant.expired` | grant_6e21 | acct_9c41e2 | Done |
| 260 | 10-03 15:00:00 | staff_omar | `grant.read_refused` | GET /v1/reports/day · no_grant | acct_8e14d9 | Refused · GRANT_REQUIRED |
| 261 | 10-03 15:01:00 | staff_ali | `access.refused` | GET /v1/reports/day · role lacks the permission | acct_8e14d9 | Refused · FORBIDDEN |
| 262 | 10-03 15:05:00 | staff_ali | `access.refused` | GET Analysis an_7781 photo · analysis_media | acct_9c41e2 | Refused · FORBIDDEN |
| 263 | 10-04 10:09:00 | system | `grant.unanswered` | grant_40aa · 72 h request window closed | acct_3f88a1 | Done |
| 264 | 10-04 10:30:00 | staff_omar | `grant.read_refused` | grant_40aa · Activity · state Unanswered | acct_3f88a1 | Refused · GRANT_NOT_ACTIVE |
| 265 | 10-04 15:55:00 | system | `grant.unanswered` | grant_6c14 · 72 h request window closed | acct_f1e0c3 | Done |
| 266 | 10-04 21:00:00 | system | `policy.version.in_effect` | Policy v2 |  | Done |

**Derived facts at the Auditor's clock (2026-10-05T09:00:00Z), replacing the auditor lens's §3 list:**
- No Grant is Active. Grants requested 2026-10-01T00:00+03:00 → 2026-10-04T00:00+03:00 (newest first; reads allowed · reads refused · writes refused): `grant_6e21` Expired, staff_omar, acct_9c41e2 0·0·0 · `grant_5d10` Ended, staff_mona, acct_9c41e2 1·0·0 · `grant_52c3` Withdrawn, staff_omar, acct_c2d7e5 1·1·0 · `grant_9b30` Ended, staff_mona, acct_a41c55 1·0·0 (1 failed read) · `grant_6c14` Unanswered, staff_mona, acct_f1e0c3 0·0·0 · `grant_7d01` Expired, staff_mona, acct_77d2c0 1·4·5 · `grant_6c10` Expired, staff_mona, acct_f1e0c3 1·0·0 · `grant_52a7` Ended, staff_mona, acct_f1e0c3 1·0·0 · `grant_52a3` Withdrawn, staff_mona, acct_f1e0c3 1·0·0 · `grant_31f9` Declined, staff_mona, acct_9c41e2 0·1·0 · `grant_40aa` Unanswered, staff_omar, acct_3f88a1 0·1·0 · `grant_31f0` Expired, staff_mona, acct_9c41e2 3·2·0 · `grant_8e20` Expired, staff_omar, acct_0d4e9b 0·0·0 · `grant_a1d4` Expired, staff_omar, acct_c2a917 0·0·0 → **14 rows**. "Widen to the last 90 days" lists **16** (adds `grant_40ab`, `grant_27b4`); none was requested 2026-09-15 → 09-20 (+03:00).
- **Allowed reads 11** (events 108, 199, 200, 201, 213, 217, 223, 229, 244, 249, 254) · **refused reads 10** (`grant.read_refused`: 206, 207, 210, 230, 231, 232, 233, 251, 260, 264) · **refused writes 5** (`grant.write_refused`: 234–238) · **other refusals 3** (123 `role.change_refused`; 261, 262 `access.refused`) · **failed reads 1** (245) · staff sign-in failures 5 (189–193) and one lock (194).
- **Consents** (Given · Withdrawn events): Diary processing 28 · 0 · Send photos, voice and text to Google's AI (Gemini) 10 · 2 · Health: read workouts 3 · 0 · read active energy 3 · 0 · read body mass 2 · 0 · write food 3 · 0 · Microphone 4 · 0 · Photos 8 · 0 · Optional research 1 · 1 · Send my label photos for review 3 · 0; age confirmations 29; age-gate refusals 0.
- **E1's Consent history** (`acct_9c41e2`): events 25, 26, 27, 28, 29, 30, 31, 32, 33, 130, 141, 148 — 12 rows (the export `consents.csv` holds 12 rows).
- **Anomalies 3**: Support agent held with Platform admin (`staff_sod_seed`) 1 · roles held with no assignment event (`staff_sod_seed`) 1 · deletions open more than 30 days (`job_del_2204`, day 31) 1. Every other rule 0, including "evaluation cases viewed without a research Consent" (J28).
- Policy: v1 Superseded (in effect 2026-08-02T00:00+03:00 → 2026-10-05T00:00+03:00), v2 In effect from 2026-10-05T00:00+03:00. Registry "Live at": 2026-09-24T12:00Z → `meal@v6` 100 % with `meal@v7` in Shadow; 2026-09-26T12:00Z → v7 5 %, v6 95 %; 2026-09-28T00:00Z → v6 100 %.

**The auditor lens's event numbers → this table** (old → new): 1→1 · 2→2 · 3→3 · 4→4 · 5→5 · 6→7 · 7→8 · 8→9 · 9→25 · 10→26 · 11→28 · 12→29 · 13→30 · 14→31 · 15→32 · 16→33 · 17→87 · 18→93 · 19→94 · 20→104 · 21→105 · 22→106 · 23→107 · 24→108 · 25→109 · 26→124 · 27→125 · 28→110 · 29→111 · 30→112 · 31→113 · 32→114 · 33→115 · 34→116 · 35→123 · 36→127 · 37→130 · 38→133 · 39→141 · 40→142 · 41→143 · 42→146 · 43→147 · 44→148 · 45→150 · 46→153 · 47→154 · 48→155 · 49→156 · 50→158 · 51→159 · 52→196 · 53→197 · 54→198 · 55→199 · 56→200 · 57→201 · 58→withdrawn (the write-refused evidence is `grant_7d01`, 234–238) · 59→205 · 60→206 and 207 · 61→208 · 62→209 · 63→210 · 64→247 · 65→248 · 66→249 · 67→250 · 68→251 · 69→252 · 70→253 · 71→254 · 72→255 · 73→257 · 74→258 · 75→259 · 76→260 · 77→261 · 78→262 · 79→263 · 80→264. Events 81 and 82 of auditor-10.1 are the live events 267 and 268; auditor-10.15's "Checking 82 events" reads "Checking 268 events" and its `audit_trail.verified` is event 269.

---

## §12 · Days and Entries

**Meal names** (J112): Breakfast · Lunch · Dinner · Snack by default (Arabic «فطار · غدا · عشا · سناك» for EG, «فطور · غداء · عشاء · وجبة خفيفة» for Gulf and MSA), or the eater's own (iftar, suhoor). Times are local to the eater unless marked Z. kcal of each Entry = count × the Unit version's kcal (§7).

### §12.1 · Mona (Africa/Cairo, boundary 03:00; Target 1,870 to 2026-09-14, 1,750 from 09-15)

Patterns: **B** Breakfast 08:40 = cheese bite × 3 (138) + glass of milk tea × 1 (60) + foul spoon × 4 (120) = 318 · **L** Lunch 15:45 = bread bite × 6 (120) + molokhia plate × 1 (360) = 480 · **D** Dinner 21:10 = talbina spoon × 15 (300) · **B+L+D = 1,098**. Extras (Snack 18:00): **X552** baladi loaf × 2 + cheese bite × 2 · **X622** molokhia plate × 1 + glass of milk × 1 + cheese bite × 2 + bread bite × 1 · **X482** molokhia plate × 1 + cheese bite × 2 + foul spoon × 1 · **X802** baladi loaf × 2 + glass of milk × 1 + cheese bite × 2 + glass of milk tea × 1 + bread bite × 2 · **X512** molokhia plate × 1 + cheese bite × 2 + glass of milk tea × 1.

| Day | Entries | kcal | mark |
|---|---|---|---|
| 2026-09-03 | B L D | 1,098 | Complete |
| 09-04 | B L D | 1,098 | — (Partial) |
| 09-05 | B L D X552 | 1,650 | — |
| 09-06 | B L D | 1,098 | Complete |
| 09-07 | B L D X482 | 1,580 | — |
| 09-08 | B L D | 1,098 | Complete |
| 09-09 | B L D X622 | 1,720 | — |
| 09-10 | B L D | 1,098 | Complete |
| 09-11 | none | — | Unlogged |
| 09-12 | B L D X512 | 1,610 | — |
| 09-13 | B L D | 1,098 | Complete |
| 09-14 | B L D X802 | 1,900 | — |
| 09-15 | B L D | 1,098 | — |
| 09-16 | B L D | 1,098 | Complete |
| 09-17 | B L D X482 | 1,580 | — |
| 09-18 | B L D | 1,098 | Complete |
| 09-19 | B L D X552 | 1,650 | — |
| 09-20 | B L D X552 | 1,650 | — |
| 09-21 | B L D X622 | 1,720 | Complete |
| 09-22 | none | — | Unlogged |
| 09-23 | B L D X482 | 1,580 | — |
| 09-24 | B L D X802 | 1,900 | Complete |
| 09-25 | none | — | Unlogged |
| 09-26 | B L D X512 | 1,610 | — |
| 09-27 | B L D | 1,098 | — |
| 09-28 | B L D | 1,098 | — |
| 09-29 | B L D X552 | 1,650 | Complete |
| **09-30 (Wed)** | **B L D — 6 Entries** | **1,098** (remaining 652 against 1,750) | — |
| **10-01 (Thu)** | "Start new day" at 02:00; B at 08:40 | 318 | Provisional |

Checks: Complete Days in 2026-09-03 → 09-30 = **10**; in 2026-09-04 → 10-01 = **9**; the week 2026-09-20 → 26 reads 1,650 · 1,720 · — · 1,580 · 1,900 · — · 1,610 → 5 of 7 Days logged, average 8,460 ÷ 5 = **1,692**, intake vs Target 8,460 − 5 × 1,750 = **−290** (e578 eater-8.12). Mona's 30 Sep timeline: B at 08:40 (3 Entries), L at 15:45 (2), D at 21:10 (1); the 00:20 loaf of eater-3.28 is the story's own act.

### §12.2 · Faisal (Asia/Riyadh, boundary 03:00; Target 2,040)

- **2026-10-01**: Breakfast 07:30 cheese bite × 3 (138) + cup of laban × 1 (152) + cup of gahwa × 2 (6) = 296 · Lunch 13:30 kabsa rice spoon × 10 (424) + chicken piece × 4 (456) = 880 · Snack 18:02 Sukkari date × 3 (72) + cup of laban × 1 (152) = 224 → **1,400**; remaining 640 at 21:30 (the kabsa tray of e578 5.x). Activity: the AT-22 walk (210 kcal; Fixed mode, not added).
- **2027-02-08 (Mon, first day of Ramadan 1448; "Ramadan days" on, boundary 12:00)**: iftar 18:02 Sukkari date × 3 + cup of laban × 1 (224) · «عشاء العائلة» 21:30 kabsa rice spoon × 4 (169.6) + chicken piece × 2 (228) · suhoor 03:40 on 02-09 cheese bite × 3 (138) + cup of laban × 1 (152) → 911.6; one Voided Entry (cup of gahwa × 2 at 22:00, voided 22:05); Activity "walk 30 min" 120 kcal at 16:30.
- **2027-02-10**: iftar 18:02 Sukkari date × 3 + cup of laban × 1 · 22:15 kabsa rice spoon × 4 + chicken piece × 2 · suhoor 03:40 on 2027-02-11 cheese bite × 3 + cup of laban × 1 → the same 911.6 on Day 2027-02-10.

### §12.3 · E1 (`acct_9c41e2`, Asia/Riyadh, boundary 04:00)

Pattern **E5d** (5 Entries): 07:00 cheese bite × 3 (138), 07:00 tea glass × 1 (20), 13:00 kabsa rice spoon × 8 (339.2), 13:00 chicken piece × 2 (228 on v1; 266 on v2 from 2026-09-10), 19:00 Sukkari date × 3 (72). Days: 2026-08-03 two Entries (19:30 Sukkari date × 3, cup of laban × 1) · 2026-08-04 → 09-14 E5d every Day (42 Days → 210 Entries) · 2026-09-15 → 09-28 E5d (the export at 2026-09-15T12:00Z = 15:00 Riyadh already holds 15 Sep's 07:00 and 13:00 Entries: 2 + 210 + 4 = **216** Entries) · **2026-09-29**: E5d (the 07:00 cheese bites came by command `cmd_44c0`, delivered 3 times at 05:12Z) + `en_9921` (16:00Z, from `an_5512`, 340 kcal, estimated analogue) + cup of gahwa × 2 (20:00) → **7 Entries** · 2026-09-30: E5d · **2026-10-01**: 07:00 cheese bite × 3, tea glass × 1; 10:00 cup of gahwa × 2; 19:00 Sukkari date × 3 → **4 Entries**.

### §12.4 · Sam (Europe/London, boundary 00:00; Target 1,870 from 2026-09-20)

| Day | Entries | kcal |
|---|---|---|
| 2026-09-08 (Tue) | Lunch 13:00 tuna spoon × 2 (53.36) + shawarma plate × 1 (875) | 928.36 |
| 2026-09-28 (Mon) | none — Unlogged | — |
| 2026-09-29 (Tue) | Breakfast 08:00 bread bite × 6 (v1, 120) + cup of laban × 1 (152) | 272 |
| 2026-09-30 (Wed) | 01:10 cup of laban × 1 (152) · Breakfast 08:30 cheese bite × 3 (138) · Lunch 13:00 shawarma plate × 1 (875) · Snack 16:00 biscuit serving × 1 (25 g, 125) · Dinner 20:00 sandwich quarter × 1 (250) | **1,540** |
| 2026-10-01 (Thu) | Breakfast 08:41 cheese bite × 3 (138) + cup of laban × 1 (152) | **290** (P 15.5, C 25.5, F 14.0; remaining 1,580); Provisional |
| 2026-10-02 (Fri) | 07:30 oatmeal bowl × 1 (360) · 10:30 oatmeal bowl × 1 (360) → 720 (P 30, C 105, F 20) · Lunch 12:30 chicken rice box × 1 (480; P 42, C 33, F 20) | **1,200** (P 72, C 138, F 40; shares 24.0 / 46.0 / 30.0 %; remaining 670) |

Shares on 2026-10-01: 62 · 102 · 126 of 290 → 21.38 · 35.17 · 43.45 % → shown **21.4 · 35.2 · 43.4 %**.

### §12.5 · Huda, E4, E6, E7, E10, E12 and the auditor's accounts

- **Huda** 2026-10-01: one Entry 09:30 Cairo, Food "Bread, baladi" 80 g typed (200 kcal; P 7, C 40, F 1; Evidence measured, amount declared); Day revision 1.
- **E4** (tracking-only) 2026-09-29 and 09-30: cheese bite × 3 + cup of laban × 1 at 08:00 each (290 each).
- **E6** 2026-09-29 and 09-30: the same 290 pattern. **E7** 2026-09-30: the same. **E12** 2026-09-30: the same.
- **E10** 2026-09-24, 25, 26, 29, 30: cheese bite × 3 (138) + bread bite × 2 (40) + foul spoon × 2 (60) at 09:00 = 238 each; 2026-09-26 also holds the same Breakfast twice at 09:00 and 09:01 (the near-duplicate behind `grant_40ab`'s reason).
- **`acct_d40e17`** 2026-09-08 and 09 (deleted with the account on 2026-09-20). **`acct_c2d7e5`** 2026-10-01: cheese bite × 3 at 08:00. **`acct_8e14d9`** 2026-10-02: cheese bite × 3 at 08:00.

### §12.6 · Hala's trial T1 (on the iPhone only; 16 local commands)

2026-09-30: 08:30 cheese bite × 3, tea with milk × 1 (logged from Template «فطار») · 11:00 tea with milk × 1 · 14:00 bread bite × 4, cheese bite × 2 · 17:00 tea with milk × 1 · 21:00 bread bite × 2 → **7 Entries, 530 kcal** · 2026-10-01: 08:30 cheese bite × 3, tea with milk × 1 · 11:00 tea with milk × 1 · 13:30 bread bite × 4, cheese bite × 2 → **5 Entries, 430 kcal**. Commands: 3 Unit saves + 1 Template save + 12 consume = 16.

---

## §13 · Separate datasets and fixtures (loaded only when a story names them)

| name | what | used by |
|---|---|---|
| **A400** | 400 eaters `eater-synth-001` … `400` (`acct_5e0001` …) and anonymous sessions `anon-synth-001` … (`acct_anon_0001` …), generated with seed 41 on this seed's configuration; 1,000 meal Analyses in the last 7 days (200 discarded, 50 retried), 100 Shadow calls, 600 confirmed meals, 90 Saved Units; named overrides: 001 sends a model field; 010 Shadow traffic; 011 and 103 AI Consent Withdrawn; 012, 021, 030–032 logging during roll back or kill switch; 040 used 3 of a hard limit of 3 (with Quotas version 2 saved in the story); 041 the 16th image analysis; 042 a voice repeat log; 043 Asia/Riyadh boundary 04:00; 044 12 image analyses; 050 an Analysis; 060 a Day; 070 an eater account offered a staff role; 090 research Given with an approved meal Analysis, 091 not; 101 a failed export; 102 a failed export and a completed deletion; 104 a failed meal job; 105 profile 82.37 kg and 178.6 cm | admin stories; admin-10.67's restore test (400 eaters) |
| **B1** · **B2** | 25,000 (seed 42) and 1,200,000 (seed 43) synthetic Audit trail events, 2026-08-01T06:00:00Z (the deployment) → 2026-09-30, actors from §3 acting only while they hold their roles | auditor large-result stories |
| **X** | one Consent record for `acct_3f88a1` pointing to the missing text version `c-ai-2` | auditor-9.18 |
| **R-gap** | clock 2026-10-05T06:30Z, last retention run 03:00Z, one audio file aged 24 h 20 min | auditor-9.15 `/r` 2 |
| **chain-40** | the seeded trail with event 40's outcome altered in storage | auditor-10.15 |
| **fresh** | an empty emulator with this seed's configuration only (§3, §4) and no accounts | `/s` lines that say "a fresh emulator" or "a fresh test database" |

---

## §14 · Lens names → seed records

| lens name | seed record |
|---|---|
| e24 `eater-synth-mona`, e36 / e578 / research "Mona" | `acct_e9a001` |
| `eater-synth-faisal`, "Faisal", e19 O3 | `acct_e9a002` |
| `eater-synth-sam`, "Sam", e19 O2 | `acct_e9a003` |
| `eater-synth-huda`, "Huda", e19 O4 | `acct_e9a004` |
| `eater-synth-nadia` · `eater-synth-khalid` | `acct_e9a005` · `acct_e9a006` |
| e19 O1 "Hala", T1 · O5 "Amal" | local trial (no account) · fresh install |
| support E1 … E14 · e19 SE1 … SE13 | §6.1 |
| auditor accounts | §6.2 |
| admin `eater-synth-NNN`, `anon-synth-NNN` | dataset A400 (§13) |
| `admin.a`, `admin.b`, `approver.a`/"approver A", "approver B", `support.a`, `auditor.a`, `access.a`, `new.user@example.test`, "Mona Adel" | `staff_ali`, `staff_badr`, `staff_dina`, `staff_yara`, `staff_mona`, `staff_hana`, `staff_rana`, `staff_new`, `staff_dina` |
| auditor `meal_photo`, `meal_photo@v6/@v7`, schema `analysis.v3` | `meal`, `meal@v6/@v7`, schema version 3 |
| auditor fixture Y (`grant_7a02`) | withdrawn; use `grant_7d01` (J7) |
| support G1 · G2 | `grant_31f0` · `grant_31f9` |
| approver L-17 and other Label submissions | §8.6 |
| e578 "laban cup", "cheese without bread", "foul spoon (dipped)" | cup of laban, cheese spoon, foul bite (§7) |

---

## Fixes after seed-check (2026-10-01)

An independent recount (`way/research/seed-check.md`, 884 values with `fractions.Fraction`) found 16 defects. Each one is fixed at its root below. Every changed number was recomputed with exact fractions.

| # | defect | change |
|---|---|---|
| D1 | AT-22: the 07:30 Riyadh import held a walk that ended at 07:45 | §10.4: the Watch walk runs **06:00–06:45** Riyadh (03:00–03:45Z) and the running app's copy 06:01–06:44. The import stays at 04:30Z, after the walk and before Faisal's 07:30 Breakfast. `join.md` J54 supersedes e578 eater-7.9 and 7.11 (manual walk from 06:00; «هل هو نفس المشي 6:00–6:45 من Apple Health؟») |
| D2 | E1's Conflict `cmd_7a1e` (2026-09-30T19:15Z) came from a device whose last sync was 2026-09-29T20:40Z | §6.1: the app 1.0.2 iPhone's last sync is **2026-09-30T19:15Z**, the sync that carried `cmd_7a1e`. `join.md` J57 supersedes support §0.3 E1 and support-9.4 (now "20:15 your time (19:15 UTC) · 22:15 eater's time") |
| D3 | the export count ignored 15 Sep's Entries | §10.1 `job_exp_4402`: **Entries 216**. §12.3 shows the sum 2 + 42 × 5 + 4: 12:00Z is 15:00 Riyadh, so the 07:00 and 13:00 Entries are already in. `join.md` J139 supersedes auditor-9.14's 212 |
| D4 | Sam's 2026-10-01 Health weight vs §10.5's "no new body mass after 24 Sept" | §10.5: the 2026-10-01 sample is written at 2026-10-01T06:00:00Z. §2 adds the start clock 2026-09-30T12:00:00Z for e578 eater-8.22 (Sam's line), when no sample after 24 Sept is loaded |
| D5 | "complete" was used for Days that carry no Complete mark | §2: "every 30 Sep Day past its boundary" and "the week 2026-09-20 → 26 has ended" |
| D6 | no permission covered `wording.published` (event 145) | §3: new permission **Publish wording**. Platform admin holds it for consent texts and `grant-req-1`; Nutrition approver holds it for `guidance-1`. `join.md` J40 lists it |
| D7 | §0 cited a §8.7 that does not exist | §0 item 4: "serves §8.1, with releases 15.4 and 15.5, and the import of §10.7" |
| D8 | eater-5.14's "12 count sets that meet every limit" | §7.6: 12 sets meet the ceiling, carbohydrate and Available limits. With the aim band 360–440 as a limit, **3** sets meet every limit: 4 + 2 (397.6), 1 + 3 (384.4), 2 + 3 (426.8). The best and the next nearest are unchanged. `join.md` J55 supersedes the `/m` line |
| D9 | eater-5.17's `/m` aim 809.7 cannot return 6/4/1/8/1 with the seed's Units | §7.6: the aim is **831.7** with tolerance 0, and 6/4/1/8/1 is the only zero-deviation answer. With 809.7, 53 other sets reach the aim exactly and 6/4/1/8/1 misses by 22 kcal. `join.md` J55 supersedes the line |
| D10 | `join.md` J104 stated 1,709.78 | J104: 1,870 × 2,134.8 ÷ 2,334.8 = 9,980,190/5,837 = **1,709.81…**, still shown 1,710; the gap is still −19.9 %. J104 now also supersedes e19 eater-1.40 `/m` "1,709.78" |
| D11 | Date (generic) meets the v1 energy-mismatch rule (282 vs 313.31, 11.10 %) but had no flag | §8.6 and `join.md` J77: **Tier A rows are not cross-checked**. FDC derives their energy with food-specific factors and counts fiber inside carbohydrate by difference. The check covers label values, approver-approved Foods and Tier B recipe records only. Counts are unchanged: Energy mismatch 4, open flags 9 (approver-10.1) |
| D12 | Cumin, ground meets the v1 rule (400 vs 446, 11.5 %) | the same rule as D11; Cumin is a Tier A row |
| D13 | B1 and B2 began 31 days before the deployment | §13: "2026-08-01T06:00:00Z (the deployment) → 2026-09-30, actors from §3 acting only while they hold their roles" |
| D14 | AT-01's values were not in the seed | §5.3 "Typed in a story": 7 pieces, 71.7 g after tare, single weights 9.8 · 10.1 · 10.6 · 10.0 · 10.4 · 10.3 · 10.5 (sum 71.7), mean 717/70 = 10.242857… g shown 10.24 g, count 7, one piece 717/14 kcal on Biscuits, plain |
| D15 | AT-03's values and its three Foods were not in the seed | §5.3: Rice, cooked 15.1 g + Peas with sauce 14.4 g + Beef, cooked 8.6 g = 38.1 g, giving 54.09 kcal, P 3.2917, C 5.8422, F 1.8305. New rows: §8.1 **Rice, cooked** (130 · 2.7 · 28.2 · 0.3) and **Beef, cooked** (250 · 26 · 0 · 15.4); §8.2 **Peas with sauce** (90 · 4.5 · 11 · 3.2). Each is within 3 % of its 4/4/9 |
| D16 | AT-09's values were not in the seed | §5.3: 46 / 32 / 24 → total 102; 45.0980… · 31.3725… · 23.5294… shown 45.10 · 31.37 · 23.53 (largest remainder, sum 100.00). Grams at 1,480 kcal: 34,040/459 → 74 g, 5,920/51 → 116 g, 1,480/17 → 87 g |

The verifier's script re-ran on the fixed file: 883 values checked. Its 12 remaining DEFECT rows are fixed statements in the script that restate the old text (the D-rows above); the parts it parses live still pass:
- the §11 trail and its counts;
- the §9 Grants;
- the Unit and planner arithmetic;
- the AT-01, AT-03 and AT-09 coverage.
