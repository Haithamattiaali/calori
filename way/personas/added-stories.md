# Stories added at the model phase (delta D5, 2026-10-01)

The model phase (`way/model.md` §2.7) found six behaviours the join decided (`way/join.md`) with no story (gaps G1–G6). Each story below follows the lens brief: Given / When / Then naming the data and the screen or interface, and at least one `/r` line. The stories use:
- `way/vocabulary.md` (D2–D6);
- `way/join.md`, including §22 (J149–J157, delta D6);
- `way/seed.md` and `way/events.md`;
- `contracts/openapi.yaml`, for every route, field and example value.

**Ids** continue each persona's numbering after its last story:
- the admin lens ends at admin-10.71 (`personas/admin.md`);
- the auditor lens ends at auditor-10.41 (`personas/auditor.md`);
- the eater's WF-5 ends at eater-5.44 and WF-7 at eater-7.24 (`personas/eater/wf5-wf7-wf8.md`).

The new ids are admin-10.72, auditor-10.42, auditor-10.43, eater-5.45, eater-7.25 and admin-10.73.

**Start clocks.** Every story names its start clock. The loader writes only the seed records at or before it (`join.md` J52, `seed.md` §0). A Given that needs a record the seed does not hold at that clock adds it through the public API or the test endpoints (J51), and says so. Moving the test clock forward inside a story loads no further seed records.

**Readers.** Each line names who reads what, and that reader holds the permission (`seed.md` §3). Console times follow J147: "HH:MM your time (HH:MM UTC)". App copy in quotes is the string catalogue's English key. An eater whose app is in Arabic sees the Arabic text of that key with their digit setting (J146).

**Wire values** follow the contract:
- numbers are strings (`"1400"`, `"0.4"`), and `credit_factor` is a decimal fraction (J149, J156);
- durations are ISO 8601 (`PT4H`);
- states are lower-case (`saved`, `in_use`, `published`).

The nine defaults first proposed here as D6-A1…A9 are now decisions (J149, with J151 widening A7). Each line cites the J item it rests on. The open items once listed here were closed by the session (end of file).

---

## admin-10.72 · Change the Grant settings; a Grant already sent keeps its own
As the Platform admin, I change the Grant durations and the request window in one versioned place, with a reason, so that support access rules change on purpose and never under a Grant already sent.

Trace: J2, J149 (bounds), J155 (Replaced), FR-081, `seed.md` §4.6, model E20 (`grant_settings_version`) and E21, §3 `grants`, `contracts/openapi.yaml` `getGrantSettings` / `saveGrantSettings` / `getGrant` / `requestGrant`, events.md §2.9 `grant_settings.version.saved`, support-10.2 and 10.3 · gap G1

**Start clock 2026-10-01T10:21:00Z.** Loaded:
- Grant settings version 1 In use (seed §4.6, event 21): durations 1 h (default) · 4 h · 24 h, request window 72 h.
- `grant_31f0` (E1 `acct_9c41e2`, `staff_mona`, 1 h): Active since 10:20:00Z, `expires_at` 11:20:00Z (event 198).
- `grant_40aa` (`acct_3f88a1`, `staff_omar`, 4 h): Requested at 10:09:00Z, closing 2026-10-04T10:09:00Z (event 197).
- No Requested Grant on E10 (`acct_f1e0c3`).

The save below happens at 10:22:00Z on the test clock.

- `/r` **The save.**
  - Given that start, When `staff_ali` (Ali N., Platform admin, holds "Change Grant settings"; console zone Africa/Cairo) opens **Settings › Grant settings**, removes 24 h from the durations, sets the request window to 48 h, enters the reason "No case has needed more than 4 h, and eaters answer within 2 days" and saves at 10:22:00Z.
  - Then the page reads "Grant settings version 2 · In use · saved by Ali N. · 13:22 your time (10:22 UTC)" with durations "1 h (default) · 4 h" and request window 48 h.
  - The page's version list reads "Version 1 · Replaced" (J155).
  - `PUT /v1/admin/grant-settings` (`expected_version: 1`, `durations: ["PT1H", "PT4H"]`, `default_duration: "PT1H"`, `request_window: "PT48H"`, the reason) returned 200 with `version: 2` and `state: "in_use"`, and `GET /v1/admin/grant-settings` now returns the same.
  - `staff_hana` (Hana Q., Auditor) sees in **Audit trail › Events** one new `grant_settings.version.saved`: actor `staff_ali` (role Platform admin), object version 2, outcome Done, detail before → after "durations 1 h (default) · 4 h · 24 h → 1 h (default) · 4 h; request window 72 h → 48 h" with that `reason`.
- `/r` **Grants already sent keep version 1.**
  - Given version 2 In use, When `staff_hana` (Auditor, holds "Read Grants (all)") calls `GET /v1/admin/grants/grant_31f0` and `GET /v1/admin/grants/grant_40aa`.
  - Then `grant_31f0` returns `state: "active"`, `grant_settings_version: 1` and `expires_at: "2026-10-01T11:20:00Z"`.
  - `grant_40aa` returns `state: "requested"`, `grant_settings_version: 1`, `duration: "PT4H"` and `request_closes_at: "2026-10-04T10:09:00Z"` (72 h, not 48 h).
  - `staff_mona`'s Grant panel for `grant_31f0` still reads "Active · read-only · ends 12:20 your time (11:20 UTC) · 14:20 eater's time" (support-10.6; her console zone is Europe/Dublin).
- `/r` **A new request uses version 2.**
  - Given version 2 In use, When `staff_mona` (Mona K., Support agent) opens **Grants › Grant form** for E10 (`acct_f1e0c3`), Then the duration offers 1 hour (selected) and 4 hours, and nothing longer.
  - When she sends the reason "A day report total looks wrong", Day 2026-09-30, the area "Entries and day reports", 4 hours and the case reference `CASE-1262` (typed in this story) at 10:23:00Z,
  - Then the Grant panel reads "Requested · waiting for the eater · the request closes 2026-10-03 11:23 your time (10:23 UTC) · 13:23 eater's time".
  - `POST /v1/grants` returned 201 with `state: "requested"`, `duration: "PT4H"`, `grant_settings_version: 2` and `request_closes_at: "2026-10-03T10:23:00Z"`.
- `/r` Given version 2 In use, When the same request for E10 is sent to `POST /v1/grants` with `duration: "PT24H"` instead, Then it returns 422 `VALIDATION_ERROR`, field `duration`, listing the allowed values 1 h and 4 h (support-10.3's check, read against version 2), and no Grant is created.
- `/r` Given version 1 In use, When `staff_ali` changes a value on **Settings › Grant settings** and saves with the reason empty, Then:
  - Save reads "Add a short reason";
  - `PUT /v1/admin/grant-settings` returns 422 `VALIDATION_ERROR` naming `reason`;
  - the page still reads "Grant settings version 1 · In use" (J2: changed only with a reason).
- `/r` Given version 1 In use, When `staff_ali` saves with every duration removed, or with a 48-hour duration added, Then the durations field reads "Keep at least one duration, each from 1 to 24 hours", `PUT /v1/admin/grant-settings` returns 422 `VALIDATION_ERROR` naming `durations`, and nothing is saved (J149, from support A4 and SR4).
- `/r` **Who may change it.**
  - Given version 2 In use, When `staff_hana` (Auditor) opens **Settings › Grant settings**, Then she reads version 2's values and version 1 as Replaced, with no edit or Save control (J2: read-only for the Auditor); and `GET /v1/admin/grant-settings/versions` with her token returns version 2 with `state: "in_use"` and version 1 with `state: "replaced"` (J155).
  - When `PUT /v1/admin/grant-settings` is called with the token of `staff_mona` (Support agent, without "Change Grant settings"), Then it returns 403 `FORBIDDEN`.
  - `staff_hana` sees one `access.refused` naming `staff_mona`, with `path` `/v1/admin/grant-settings` and `permission_missing` naming "Change Grant settings".
  - `GET /v1/admin/grant-settings` still returns `version: 2`.

## auditor-10.42 · Leave a review note on the Audit trail
As the Auditor, I add a review note saying what I reviewed, for which period and what I found, so that the next reviewer and a regulator see what was checked and why.

Trace: J17, J149 (finding values, the identifier check), FR-081, events.md §2.10 `audit_trail.review_noted`, model §1 row 10F.10, §3 `audit.add_review_note`, `contracts/openapi.yaml` `addReviewNote` (`ReviewNote`) and `verifyAuditTrail`, `seed.md` §3 ("Add a review note") · gap G2

**Start clock 2026-10-05T09:00:00Z** (the Auditor's default, seed §2). Events 1–266 are loaded and the chain 1–266 is intact. `staff_hana` (Hana Q., Auditor: "Read the whole Audit trail", "Add a review note"; console zone Asia/Riyadh). `grant_31f0`'s life is events 196, 198, 199, 200, 201, 205, 206 and 207 (auditor-10.3, J57).

- `/r` **A note is added.**
  - Given `staff_hana` has filtered **Audit trail › Events** to `grant_31f0`, When she chooses "Add review note", enters the values below and saves:
    - what was reviewed: "grant_31f0 · events 196, 198–201, 205–207";
    - the period: 1 Oct 2026 in her zone;
    - the finding: "No issue found";
    - the note: "Checked against case CASE-1182: 3 reads inside the Grant's Days and areas; 2 reads refused after it expired; no write attempted."
  - Then `POST /v1/admin/audit-trail/review-notes` returned 201 with `actor` {`kind: staff`, `id: staff_hana`, `role: Auditor`}, `action: audit_trail.review_noted` and `outcome: done`.
  - The detail holds `scope` "grant_31f0 · events 196, 198–201, 205–207", `period` {`from: "2026-09-30T21:00:00Z"`, `to: "2026-10-01T21:00:00Z"`}, `finding: "no_issue"` (J149) and the `note` exactly as entered, with no other detail key (J14).
  - The new row shows Hana Q., Done and its time as "HH:MM your time (HH:MM UTC)".
- `/s` Given that save, When the trail is read, Then:
  - the new event's `seq` is one more than the newest event before it;
  - its `prev_hash` equals that event's `hash`;
  - events 196–207 keep their stored `hash`;
  - `POST /v1/admin/audit-trail/verify` by `staff_hana` returns `result: "intact"`, with `from_seq: 1` and `to_seq` the new event, and writes `audit_trail.verified`.
- `/r` Given a note of 501 characters, When `staff_hana` saves it, Then the note field reads "Up to 500 characters, with no eater details", the call returns 422 `VALIDATION_ERROR` with `field: "note"`, and no `audit_trail.review_noted` is added (J17).
- `/r` Given the note, or what was reviewed, contains an account id such as `acct_9c41e2` or an email address, When she saves it, Then that field reads "Up to 500 characters, with no eater details", the call returns 422 `VALIDATION_ERROR` naming that field (`note` or `scope`), and nothing is added (J17; the check is J149).
- `/r` Given no finding is chosen, When she saves, Then the finding field reads "Required", the call returns 422 `VALIDATION_ERROR` with `field: "finding"`, and nothing is added (J17, J149).
- `/r` Given `staff_ali` (Ali N., Platform admin, without "Add a review note"), When `POST /v1/admin/audit-trail/review-notes` is called with his token, Then:
  - it returns 403 `FORBIDDEN`;
  - `staff_hana` sees one `access.refused` naming `staff_ali`, with `path` `/v1/admin/audit-trail/review-notes` and `permission_missing` naming "Add a review note";
  - there is no `audit_trail.review_noted` by him.

## auditor-10.43 · Events older than 5 years leave on schedule, and the chain still checks
As the Auditor, I see that the retention run removed the events older than 5 years and recorded where the chain now starts and who held which role, so that the trail is kept as long as decided and no longer, and still proves itself.

Trace: J18, J149 (the hourly run, `removed_through_seq`, `anchor_hash`), J157 (`roles_held_at_anchor`), FR-082 ("retention verification"), events.md §1 (Retention) and §2.10 `audit_trail.retention_run`, model §1 row 10F.11, E66, E68, E69 (`audit_retention`), `contracts/openapi.yaml` `RetentionRunDetail`, `listAuditTrailEvents`, `verifyAuditTrail`, `getAuditSummary`, `getAnomalies` · gap G3

**Start clock 2026-10-05T09:00:00Z** (the Auditor's default, seed §2). Events 1–266 are loaded and the chain 1–266 is intact. The test clock is then moved (`PUT /v1/test/clock`, J51) to **2031-08-02T00:00:00Z**; no further seed record loads (J52).

At that time:
- Events 1–24 (2026-08-01T06:00:00Z to 2026-08-01T21:00:00Z) are more than 5 years old. They include the `role.assigned` events 1–6 of `staff_ali`, `staff_mona`, `staff_omar`, `staff_dina`, `staff_hana` and `staff_lee`.
- Event 25 (`age.confirmed`, 2026-08-03T18:20:00Z) and every later event are not.
- Any hourly run after 2031-08-01T21:00:00Z and not after 2031-08-03T18:20:00Z gives the same result.

The reader is `staff_hana` (Hana Q., Auditor).

- `/r` **The run.**
  - Given the clock at 2031-08-02T00:00:00Z, When the hourly Audit trail retention run completes (J149) and `staff_hana` opens **Audit trail › Events**.
  - Then the oldest event listed is 25, and no event 1–24 is listed.
  - One new `audit_trail.retention_run` row shows actor system (`system:audit_retention`, surface job), outcome Done and detail `removed_through_seq: 24`.
  - Its `anchor_hash` equals event 25's `prev_hash` (J149).
  - Its `roles_held_at_anchor` lists exactly `staff_ali` [Platform admin], `staff_mona` [Support agent], `staff_omar` [Support agent], `staff_dina` [Nutrition approver], `staff_hana` [Auditor] and `staff_lee` [Support agent] (J157). These are the roles held at the anchor. `staff_tariq`, `staff_yara`, `staff_rana`, `staff_badr` and `staff_sod_seed` held no role then.
- `/r` **The chain and the Summary.**
  - Given that run, When `staff_hana` selects Verify chain, Then it ends "Intact · events 25–" followed by the newest event's number.
  - `POST /v1/admin/audit-trail/verify` returns `result: "intact"`, `from_seq: 25` and no `first_break`, and writes `audit_trail.verified`.
  - **Audit trail › Summary** for 2031-08-01T00:00:00Z to 2031-08-03T00:00:00Z (`GET /v1/admin/audit-trail/summary`) shows `last_retention_run` with `detail.removed_through_seq: 24` and `chain_check: "passed"`.
- `/r` **Anomalies.**
  - Given that run, When `staff_hana` opens **Audit trail › Anomalies** after its next evaluation (`last_evaluated_at` later than the run).
  - Then "roles held with no assignment event" still counts 1, with `record_ids` [`staff_sod_seed`]. The six roles in `roles_held_at_anchor` count as assigned, though their `role.assigned` events 1–6 are gone (J157).
- `/s` Given that run, When the store is read, Then:
  - it holds no event with `seq` ≤ 24;
  - events 25–266 keep their stored `prev_hash` and `hash`;
  - `SHA-256(anchor_hash ‖ canonical(event 25))` equals event 25's stored `hash`;
  - the `audit_trail.retention_run` event's `prev_hash` is the `hash` of the event before it;
  - the run wrote exactly one `audit_trail.retention_run`.

## eater-5.45 · A Saved Plan I never confirmed expires at the end of its Day
As the Eater, I find last night's unconfirmed Plan marked as not logged, with a way to plan again, so that an old Plan is never logged by mistake. A meal I confirmed in time still counts, even if it arrives late.

Trace: J130, J149 (card, "Plan again", the 409), J150, J151, J152, FR-045, FR-047, FRD §8.1 and §8.3, FRD §17 MealPlan (`expiry`), model §1 row 5.14, E56 (`expired_at`), events.md §3 `plan.expired` · `plan.confirmed`, `contracts/openapi.yaml` `listMealPlans` (`include_expired`), `getMealPlan`, `consume` (`made_at`, `source_plan_id`) · gap G4

**Start clock 2026-10-01T18:30:00Z** (21:30 Riyadh; seed §2's clock for Faisal's kabsa stories). Faisal (`acct_e9a002`) is on Asia/Riyadh with a diary-day boundary of 03:00, an Arabic app with Western digits, Fixed mode and Target 2,040. His Day 2026-10-01 is at 1,400 kcal (seed §12.2).

The seed holds no Plan, so the Given adds one through the public API:
- eater-5.14's request to `POST /v1/meal-plans`: chips kabsa rice spoon and chicken piece (`available_count: "3"`), salad and laban under Exclude, Calorie aim about 400 (±10 %), Calorie ceiling 500 and Carbohydrate maximum 30 %;
- it returns the Proposed Plan 4 rice + 2 chicken (397.6 kcal), with `selected_versions` [`uv_faisal_kabsa_rice_spoon_v1`, `uv_faisal_chicken_piece_v1`];
- Faisal saves it at 21:30 with «حفظ الخطة» (`POST /v1/meal-plans/{id}/save`, eater-5.28).

He never confirms it, except in the J151 line below. The Plan card lives on **Today** (J152).

- `/r` **Expiry at the boundary.**
  - Given the Saved Plan and the clock moved to 2026-10-01T23:59:00Z (02:59 Riyadh, still Day 2026-10-01), When Faisal opens **Today**, Then the Plan card still offers «أكلت كما في الخطة», «تغيير الكميات» and «لم تُؤكل».
  - When the clock is then moved to 2026-10-02T05:00:00Z (08:00 Riyadh, Day 2026-10-02) and he opens Today, Then the card shows the Arabic text of "From 1 Oct · not logged" with "Plan again" (J146, Western digits). It offers none of those three buttons (J149, J150).
- `/r` **The API at 2026-10-02T05:00:00Z.**
  - `GET /v1/meal-plans/{id}` with Faisal's token returns `state: "saved"`, `diary_day_id: "2026-10-01"`, `expired_at: "2026-10-02T00:00:00Z"` (03:00 Riyadh, the end of its Day) and no `entry_ids` (J150).
  - `GET /v1/meal-plans?state=saved` returns no Plan, and `GET /v1/meal-plans?state=saved&include_expired=true` lists this Plan with its `expired_at` (J150).
  - `GET /v1/reports/day?diary_day_id=2026-10-01` still returns `consumed_kcal: "1400"`.
- `/s` Given the same, When Faisal's `plan_events` are read, Then:
  - exactly one `plan.expired` exists for the Plan, with `account_id` `acct_e9a002`, payload `state: saved`, `selected_versions` [`uv_faisal_kabsa_rice_spoon_v1`, `uv_faisal_chicken_piece_v1`] and no entry ids;
  - no `entry.confirmed` carries the Plan's id as `source_plan_id`.
- `/r` **"Plan again".**
  - Given the expired card, When Faisal taps "Plan again", Then **Meal planner** opens for Day 2026-10-02 with:
    - the chips kabsa rice spoon and chicken piece (Available 3);
    - salad and laban under Exclude;
    - the Calorie aim about 400 («360–440»), the ceiling «لا يزيد عن 500 سعرة» and Carbohydrate maximum 30 %.
  - `GET /v1/meal-plans?state=proposed` returns no Plan (J149).
  - When he taps «حساب الكميات», Then a new Plan returns with `state: "proposed"`, `diary_day_id: "2026-10-02"` and 4 rice + 2 chicken (397.6 kcal), and the expired Plan still reads `state: "saved"`.
- `/r` **A confirmation made after expiry is refused.**
  - Given the expired Plan, When `POST /v1/consumption` is sent with Faisal's token, a new command id, `made_at: "2026-10-02T05:00:00Z"` (after `expired_at`), `confirmed_as: "ate_as_planned"`, the Plan's own counts and versions, and `source_plan_id` set to its id,
  - Then it returns 409 `VALIDATION_ERROR` with `reason: "plan_expired"`, `state: "saved"` and `field: "source_plan_id"` (J149), and no Entry is created.
  - `GET /v1/reports/day` returns `consumed_kcal: "1400"` for 2026-10-01 and `"0"` for 2026-10-02, with both `day_revision` values unchanged.
- `/r` **A confirmation made before expiry and delivered after it is accepted** (J151).
  - Given instead that the simulator is in airplane mode and, at 2026-10-01T23:50:00Z (02:50 Riyadh, Day 2026-10-01), Faisal taps «أكلت كما في الخطة» on the Plan card. The consume command waits in the outbox with `made_at: "2026-10-01T23:50:00Z"`, `diary_day_id: "2026-10-01"` and `source_plan_id`.
  - When the network returns at 2026-10-02T05:00:00Z, after `expired_at`, and the command is delivered,
  - Then `POST /v1/consumption` returns 201 with two Entries (kabsa rice spoon × 4, chicken piece × 2) on Day 2026-10-01.
  - `GET /v1/reports/day?diary_day_id=2026-10-01` returns `consumed_kcal: "1797.6"` (1,400 + 397.6) with `day_revision` one higher.
  - `GET /v1/meal-plans/{id}` returns `state: "confirmed"`, `confirmed_as: "ate_as_planned"` and both `entry_ids`.
  - A second delivery of the same `command_id` returns the original answer with `replayed: true` and adds nothing.

## eater-7.25 · A new activity credit is offered, never applied by itself
As the Eater in Activity-adjusted mode, I am told when the Policy's activity credit changes and I choose whether to take it, so that my food budget never changes without my say.

Trace: J103, J156, J149 (the number form), FRD §12.2 ("Apply a visible user-approved credit factor and cap"), FR-058, FR-071, J104, J105, J106, J107, approver-10.69, eater-7.18, 7.19, model §1 row 7.8, E62, events.md §3 `target.version.approved`, `contracts/openapi.yaml` `getActivityCreditOffer`, `approveActivityCreditOffer`, `getCurrentTarget`, `getDayReport` (`target.activity_credit_kcal`) · gap G5

**Start clock 2026-10-06T08:00:00Z** (09:00 London, Day 2026-10-06). The whole seed through event 266 is loaded:
- Policy v2 has been In effect since 2026-10-05T00:00:00+03:00, with v1's Activity-adjusted credit: factor 50 % and cap 300 kcal a day (seed §4.1, §4.2).
- Sam (`acct_e9a003`, Europe/London, boundary 00:00, English) is in Fixed mode on `tv_sam_1` (1,870, Entered by you).
- Sam's simulator Health store holds no workout after 2026-10-01.

The Given adds, through the public API and the test endpoints (J51, J52):
- **(a)** At 09:05 London on 6 Oct, Sam approves Activity-adjusted mode with Policy v2's credit, 50 % and 300 kcal a day (eater-7.18's Approve; `POST /v1/targets`). This gives Target version `tv_sam_2`: base 1,710 (J104), `policy_version: 2`, `credit_factor: "0.5"`, `credit_cap_kcal: "300"`.
- **(b)** `staff_yara` (Yara M., Nutrition approver) proposes Policy version 3 with `POST /v1/admin/policy/versions`: `based_on: 2`, `values.activity_adjusted_credit` {`credit_factor: "0.4"`, `credit_cap_kcal: "250"`} and every other value as v2. `staff_dina` (Dina R., Nutrition approver) approves it with a reason and `effective_from: "2026-10-06T21:00:00Z"`, which is 2026-10-07T00:00:00+03:00 (`POST /v1/admin/policy/versions/3/approve`).
- **(c)** The clock is moved to 2026-10-07T08:00:00Z (09:00 London, Day 2026-10-07), after Policy v3 comes In effect.
- **(d)** An outdoor run 07:00–07:40 London on 7 Oct, 400 kcal active energy, is written to Sam's simulator Health store through the HealthKit mock and imported when the app opens.

- `/r` **The offer appears only when the credit differs.**
  - Given (a) only, When Sam opens **Settings → Activity → Activity mode** on 6 Oct, Then it reads "Your credit: 50 % up to 300 kcal" and shows no offer. `GET /v1/targets/activity-credit-offer` returns no `offer`, because Policy v2 offers the same credit (J103, J156).
  - Given (a)–(d), When he opens it on 7 Oct, Then it reads "Your credit: 50 % up to 300 kcal" and "New activity credit available: 40 % up to 250 kcal — Review".
  - `GET /v1/targets/activity-credit-offer` returns `offer` {`policy_version: 3`, `credit_factor: "0.4"`, `credit_cap_kcal: "250"`, `current` {`target_version_id: "tv_sam_2"`, `credit_factor: "0.5"`, `credit_cap_kcal: "300"`, `policy_version: 2`}}.
  - **Today** reads "Food Target: Activity-adjusted" and "Target today 1,910 (1,710 + 200 Activity credit)" (400 × 50 % = 200 ≤ 300; eater-7.19's form).
  - `GET /v1/reports/day?diary_day_id=2026-10-07` returns `target.target_version_id: "tv_sam_2"` and `target.activity_credit_kcal: "200"`.
- `/r` Given (a)–(d), When `GET /v1/targets/current` is called with Sam's token, Then `target` is `tv_sam_2` with `activity_mode: "activity_adjusted"`, `credit_factor: "0.5"`, `credit_cap_kcal: "300"` and `policy_version: 2`. The new Policy rewrote nothing (J107, model E62).
- `/r` **Approving the offer.**
  - Given (a)–(d), When he taps Review, Then the preview reads "Base food Target 1,710", "Credit 40 % of eligible Activity" and "Cap 250 kcal a day", with "Approve" and "Cancel" (eater-7.18's form).
  - When he taps Approve, Then the app sends `POST /v1/targets/activity-credit-offer/approve` {`policy_version: 3`, `credit_factor: "0.4"`, `credit_cap_kcal: "250"`}, which returns 201 with `target_version_id: "tv_sam_3"`, `source: "entered"`, `activity_mode: "activity_adjusted"`, `credit_factor: "0.4"`, `credit_cap_kcal: "250"` and `policy_version: 3` (J156).
  - Today reads "Target today 1,870 (1,710 + 160 Activity credit)" (400 × 40 % = 160 ≤ 250), and the Day report returns `target.activity_credit_kcal: "160"`.
  - Settings → Activity → Activity mode reads "Your credit: 40 % up to 250 kcal" with no offer, and `GET /v1/targets/activity-credit-offer` returns no `offer`.
  - **Progress → Target history** has a new top row for 1,870 from 7 Oct, "Entered by you". Its details read "Activity-adjusted · credit 40 % up to 250 kcal" and "Activity × 1.2 · Policy v3" (J106).
- `/s` Given that approval, When Sam's `target_events` are read, Then:
  - one new `target.version.approved` carries `source: entered`, `policy_version: 3`, `activity_mode: activity_adjusted`, `credit_factor: "0.4"` and `credit_cap_kcal: "250"` (J156: one field name everywhere);
  - `GET /v1/targets` lists `tv_sam_3`, `tv_sam_2` and `tv_sam_1`, newest first;
  - `tv_sam_2` still holds `credit_factor: "0.5"` and `credit_cap_kcal: "300"`.
- `/r` Given (a)–(d), When he taps Review and then Cancel, Then:
  - Settings → Activity → Activity mode still reads "Your credit: 50 % up to 300 kcal" with the offer line;
  - Today still reads "Target today 1,910 (1,710 + 200 Activity credit)";
  - Progress → Target history has no new row;
  - `GET /v1/targets/current` still returns `tv_sam_2`, and `GET /v1/targets/activity-credit-offer` still returns the offer.
- `/r` Given the Review preview, When he raises the credit to 45 % or the cap to 260 kcal, Then:
  - that field reads "Up to 40 %" or "Up to 250 kcal", and Approve is disabled;
  - `POST /v1/targets/activity-credit-offer/approve` with `credit_factor: "0.45"` returns 422 `VALIDATION_ERROR` with `field: "credit_factor"`;
  - no Target version is created (J103: the Policy's factor and cap are the upper bound; the eater may lower either, never raise).

## admin-10.73 · Publish a consent Wording after its privacy review is signed
As the Platform admin, I publish counsel's reviewed consent text as a new Wording version only after its privacy review is signed, and I mark whether eaters are asked again. Every eater who decides from now on reads the reviewed words. A Consent already given keeps its own version unless I mark the new text to ask again.

Trace: J26, J40, J149 (the 409), J153, J154, J23, J24, J32 (`c-ai-5` is counsel's residency wording), FR-076, FR-082, model E11, E12, E42, §3 `privacy.publish_wording` and `gates.sign`, `contracts/openapi.yaml` `publishWording` (`PublishWording`, `Wording`), `getWording`, `signLaunchGate`, `listConsents` (`PurposeConsent`), `createAnalysis`, `listWordings`, `proposeWording`, events.md `wording.proposed`, events.md §2.5 and §2.7 · gap G6

**Start clock 2026-10-01T09:00:00Z** (the Platform admin's default, seed §2). Loaded:
- **Settings › Wordings** (J153) shows family `c-ai`: `c-ai-5` Proposed since 2026-09-30T10:00:00Z (by `staff_ali`, `asks_again: false`, seed §4.7), `c-ai-4` Published since 2026-09-24T08:00Z (event 145) and `c-ai-3` Superseded; `GET /v1/admin/wording/versions?family=c-ai` returns the same three states.
- **Settings › launch gates** holds the privacy review of `c-ai-4` only (signed 2026-09-24 by counsel R. Haddad (synthetic), event 144).
- `c-ai-5`'s stored English and Arabic texts are counsel's synthetic wording; they change only the residency sentence of `c-ai-4` (J32).
- `staff_ali` (Ali N., Platform admin) holds "Publish wording" and "Sign launch gates". `staff_hana` (Hana Q., Auditor) reads the events.

- `/r` **Publishing before the review is signed is refused.**
  - Given that start, When `staff_ali` opens the Proposed `c-ai-5` on **Settings › Wordings** ("Ask eaters again" off, as stored), Then Publish is disabled with "Sign the privacy review of c-ai-5 first."
  - `POST /v1/admin/wording` {`text_version: "c-ai-5"`, `en` and `ar` equal to the stored Proposed texts, `asks_again: false`} returns 409 `VALIDATION_ERROR` with `reason: "privacy_review_not_signed"`, `state: "not_signed"` and `field: "text_version"` (J26, J149).
  - Nothing is published: `GET /v1/wording?text_version=c-ai-4` still returns `state: "published"`, and `staff_hana` finds no `wording.published` for `c-ai-5` in **Audit trail › Events**.
- `/r` **Sign, then publish.**
  - Given that start, When `staff_ali` signs on Settings › launch gates the privacy review of `c-ai-5` (`POST /v1/admin/launch-gates/privacy_review/sign` with `version_reviewed: "c-ai-5"` and `signer_name: "R. Haddad"`),
  - Then the gate reads "Privacy review signed by R. Haddad on 2026-10-01" for `c-ai-5` (J26).
  - When he then publishes `c-ai-5` on Settings › Wordings with "Ask eaters again" off, Then `POST /v1/admin/wording` returns 201 with `state: "published"`, `asks_again: false`, `published_by: "staff_ali"` and `review_gate: "privacy_review"`.
  - Settings › Wordings lists `c-ai-5` Published and `c-ai-4` Superseded (J153), and `GET /v1/admin/wording/versions?family=c-ai` returns `published` for `c-ai-5` and `superseded` for `c-ai-4` and `c-ai-3`.
  - `staff_hana` sees in Audit trail › Events:
    - `launch_gate.signed`: actor `staff_ali`, role Platform admin; detail `gate: privacy_review`, `signer` R. Haddad, `version_reviewed: c-ai-5`; outcome Done;
    - followed by `wording.published`: actor `staff_ali`; detail `key: c-ai-5`, `version: 5`, the languages English and Arabic, and `asks_again: false` (J154: recorded in the Audit trail); outcome Done.
- `/s` Given that publish, When `GET /v1/wording?text_version=c-ai-3&text_version=c-ai-4&text_version=c-ai-5` is read, Then:
  - it returns `state` `superseded`, `superseded` and `published`;
  - `c-ai-5` has `asks_again: false`;
  - the stored English and Arabic texts of `c-ai-3` and `c-ai-4` are byte for byte unchanged (model E12).
- `/r` **An earlier Consent keeps its version** (J154).
  - Given `c-ai-5` published with `asks_again: false`, When `GET /v1/me/consents` is called with Faisal's token (`acct_e9a002`; AI Consent Given under `c-ai-3`, seed §5.1, event 78),
  - Then `ai_processing` reads `state: "given"`, `record.text_version: "c-ai-3"`, `record.made_at: "2026-08-25T15:02:10Z"`, `text_version_in_force: "c-ai-5"` and `asks_again: false`.
  - When he next sends typed words for analysis on **Capture & Plan**, Then no Consent sheet appears, and `POST /v1/analyses` returns 201: Consents given under earlier versions stay valid under their own version.
- `/r` **A new decision uses the new text.**
  - Given `c-ai-5` published, When Nadia (`acct_e9a005`, English app, AI Consent Not given) first sends typed words for analysis on **Capture & Plan**, Then the sheet's words match `c-ai-5`'s stored English text exactly, with "Give consent" and "Not now" (eater-4.3).
  - When she taps "Give consent", Then `GET /v1/me/consents` returns `ai_processing` with `state: "given"`, `record.text_version: "c-ai-5"`, `record.method: "first_need_sheet"` and `record.context` Capture & Plan (J24).
  - `staff_hana` sees one `consent.given` with `purpose: ai_processing`, `text_version: c-ai-5` and `method: first_need_sheet`.
- `/r` **A version marked to ask again blocks the purpose** (J154).
  - Given `c-ai-5` published as above, `staff_ali` proposes `c-ai-6` (a synthetic text with a changed purpose) through `POST /v1/admin/wording/proposals` with `asks_again: true` — `staff_hana` sees `wording.proposed` with `key: c-ai-6`, `version: 6`, `asks_again: true` — then signs its privacy review and publishes `c-ai-6`. `wording.published` carries `asks_again: true`, and `c-ai-5` becomes Superseded.
  - When `GET /v1/me/consents` is called with Faisal's token, Then `ai_processing` reads `state: "given"`, `record.text_version: "c-ai-3"`, `text_version_in_force: "c-ai-6"` and `asks_again: true`.
  - When he next sends typed words on Capture & Plan, Then the Consent sheet shows `c-ai-6`'s stored Arabic text exactly, with the Arabic catalogue text of "Give consent" and "Not now" (J146).
  - `POST /v1/analyses` with his token returns 403 `CONSENT_REQUIRED`, and the analyzer mock receives no request.
  - When he taps "Give consent", Then `GET /v1/me/consents` returns `record.text_version: "c-ai-6"` and `asks_again: false`, and his next `POST /v1/analyses` returns 201.
  - `staff_hana` sees a new `consent.given` with `text_version: c-ai-6`, while event 78 (his `c-ai-3` decision) is unchanged.
- `/r` **Who may publish.**
  - Given the privacy review of `c-ai-5` signed and `c-ai-5` not yet published, When `POST /v1/admin/wording` with `text_version: "c-ai-5"` is called with the token of either:
    - `staff_mona` (Mona K., Support agent: no "Publish wording"); or
    - `staff_dina` (Dina R., Nutrition approver: "Publish wording" for `guidance-1` only, J40),
  - Then each returns 403 `FORBIDDEN`, and `staff_hana` sees one `access.refused` per call naming the caller with `path` `/v1/admin/wording`.
  - No `wording.published` is written, and `c-ai-4` stays Published.

---

## Decided by D6 (`join.md` §22) — formerly "Proposed for D6"

- D6-A1 (Grant settings bounds) → **J149**, adopted as written.
- D6-A2 (review-note fields and finding values) → **J149**, adopted as written.
- D6-A3 (the no-eater-identifier check) → **J149**, adopted as written.
- D6-A4 (hourly Audit trail retention run, `removed_through_seq`, `anchor_hash`) → **J149**, widened by **J157** (`roles_held_at_anchor`).
- D6-A5 (the expired Plan's card on Today with "Plan again" only) → **J149**, with **J150**: no new state, copy "not logged".
- D6-A6 ("Plan again" refills Meal planner without solving) → **J149**, adopted as written.
- D6-A7 (409 `reason: plan_expired`) → **J149** for a confirmation made after expiry, widened by **J151**: one made before is accepted later onto the Plan's Day.
- D6-A8 (`credit_factor` as a decimal fraction) → **J149** and **J156**.
- D6-A9 (409 `reason: privacy_review_not_signed`) → **J149**, adopted as written.

## Conflicts from round 1 — decided by D6

1. A Saved Plan after its Day → **J150**: it stays Saved with `expired_at`; `?state=saved` lists unexpired Plans, and `&include_expired=true` lists all.
2. The Plan card's place → **J152**: Today.
3. Late confirmations and expiry → **J151**: made before `expired_at`, the confirmation is accepted once onto the Plan's Day; made after, it is refused. eater-5.35 holds through its 12:00 Ramadan boundary.
4. Wording states and place → **J153**: Proposed → Published · Superseded, on Settings › Wordings.
5. Asking again after a new Wording → **J154**: `asks_again`, set by the publisher.
6. A replaced Grant settings version → **J155**: Replaced.
7. The credit-offer route → **J156**: `GET /v1/targets/activity-credit-offer` and `POST /v1/targets/activity-credit-offer/approve`.
8. `credit_cap` vs `credit_cap_kcal` → **J156**: `credit_cap_kcal` everywhere.
9. Retention and the "roles held with no assignment event" rule → **J157**: `roles_held_at_anchor`.

## Open items for the model phase
None open: each was closed by the session (see "Open items closed by the session" at the end of the file).

---

## Lens verdict (2026-10-01)

**fail** — 26 defects. Read against `way/model.md` §2.7 (G1–G6), `way/join.md` (J2, J15, J17, J18, J26, J40, J47, J49, J52, J60, J103, J130), `way/vocabulary.md` (D2–D4), `way/seed.md`, `way/events.md`, `way/brief/frd-v1.0.md`. What holds: each story traces to the join item and gap it names (J2/E21/G1, J17/G2, J18/G3, J130/G4, J103/G5, J26 and J40/G6); every story has a `/r` line; the error codes are D2/D4 codes with J38's statuses; the events named (`grant_settings.version.saved`, `access.refused`, `grant.read`, `audit_trail.review_noted`, `audit_trail.retention_run`, `wording.published`) are in `events.md`; `grant_31f0`, CASE-1182, Sam's 00:00 boundary and the permission "Publish wording" are in the seed; the arithmetic holds (400 kcal × 40 % = 160 ≤ 250; 501 > 500; 3 of 5 events older than 5 years).

1. **admin-10.76, admin-10.77 (ids)** — the file says "Ids continue each persona's numbering after its last story", but the admin's last story is admin-10.71 (`personas/admin.md` line 1030) and admin-10.72–10.75 exist nowhere. These two are admin-10.72 and admin-10.73.
2. **admin-10.76 `/r` 1 and 3** — "Given Grant settings version 1 In use (seed §4.6: longest Grant 1 hour)" misreads the seed. §4.6 and J2 hold "durations 1 h (default) · 4 h · 24 h", so the longest is 24 h and 1 h is the default. "sets the longest Grant to 2 hours" with the reason "Longer reads for export cases" therefore shortens it. "the duration choices go up to 2 hours" does not say which of 1 h · 4 h · 24 h remain. Model E21 holds a durations list, not a "longest Grant".
3. **admin-10.76 `/r` 4** — "the field reads 'Choose 15 minutes to 8 hours'": these bounds have no source (J2, seed §4.6 and model E21 give none) and no `assumption` label. They would also refuse version 1's own 24 h duration.
4. **admin-10.76 `/r` 1** — "saved by Admin A.": `admin.a` is `staff_ali`, display name "Ali N." (seed §3, §14; J53).
5. **admin-10.76 `/r` 2** — "Given `grant_31f0` … is Active": `grant_31f0` is requested at 10:05 and Active only from 10:20 to 11:20Z on 2026-10-01 (seed §9). The story names no start clock, and at the platform admin's default clock 2026-10-01T09:00:00Z (seed §2) the Grant is not loaded. An admin cannot add it through the API. "still reads its own end time" names no value (11:20Z, which `staff_mona`'s Dublin console shows as 12:20).
6. **admin-10.76 `/r` 2** — "`GET /v1/admin/grants/grant_31f0` returns `settings_version: 1`": the model's field is `grant_settings_version` (model E20). The line also names no caller. The Platform admin holds no "Read Grants (all)" (seed §3) and never reads Grant data (J15), so `admin.a` would be refused. The reader has to be named (`support.a` or `auditor.a`).
7. **auditor-10.42 `/r` 1** — "with the note, Auditor A. and the time": `auditor.a` is `staff_hana`, display name "Hana Q." (seed §3, §14).
8. **auditor-10.42** — J17 decides what a review note holds: "what was reviewed, the period, the finding, free text ≤ 500 characters". `events.md` gives `audit_trail.review_noted` the detail `period`, `scope`, `finding` and `note`. The story enters and checks only the free-text note. No line sets or observes the period, scope or finding.
9. **auditor-10.43 `/s` and `/r`** — "Given a test trail with 3 events dated 5 years and 1 day before the test clock": `seed.md` has no such dataset (§13 lists A400, B1, B2, X, R-gap, chain-40 and fresh). The seeded trail starts at the deployment, 2026-08-01T06:00:00Z, and the story names no start clock. So the `/r` line ("Given that run") cannot be set up in the served product from the seed (J52).
10. **auditor-10.43 `/s`** — "one `audit_trail.retention_run` event records '3 events removed, oldest kept dated …'": the elided value cannot fail. `events.md` gives this event the detail `removed_through_seq` and `anchor_hash` (J14: allow-listed keys only), and the story checks neither. It also does not check J18's "the chain re-anchors at the first kept event, recorded in that summary".
11. **auditor-10.43 (trace)** — "NFR-13" is the deletion SLA ("Proposed live-system deletion SLA ≤30 days; disclose backup expiry and verify deletion propagation"), not the 5-year Audit trail retention. The behaviour traces to J18 and FR-082 ("retention verification") only.
12. **eater-5.44 (id)** — this id is already used. `personas/eater/wf5-wf7-wf8.md` line 408 holds "eater-5.44 · My limits come before my preferences, then the simpler meal", and J55 supersedes some of its lines. The eater's last WF-5 story is 5.44, so this story is eater-5.45. Blueprint delta D5 and `plan-skeleton.md` S12 cite the duplicate, and the count of 629 stories holds only if the ids are distinct.
13. **eater-5.44 `/r` 1** — "Given Sam's Saved Plan for 30 Sep that he never confirmed": `seed.md` holds no Plan (no `plan_` record; §12.4 lists only Sam's Entries). The eater's default clock, 2026-10-01T09:00:00Z, is after 30 Sep's Day, so the Given cannot add the Plan through the API. The story names no start clock (for example one on 30 Sep, with the clock then moved past 00:00 London).
14. **eater-7.25 `/r` 1** — "Policy version 2 with credit 40 % up to 250 kcal comes In effect": seed §4.2 says v2 has "One change: energy mismatch > 12 % … Every other value equals v1", so v2's credit is 50 % up to 300 kcal. V2 is also In effect only from 2026-10-05T00:00:00+03:00, and no start clock is named (eater stories at 2026-10-01 see v1, J60). The 40 % / 250 kcal offer needs a newer Policy version added in the Given (by approver acts) or a seed change.
15. **eater-7.25 `/r` 1** — "Given Sam approved credit 50 % up to 300 kcal": seed §5.1 and §5.2 have Sam in Fixed mode (`tv_sam_1` "Fixed mode"). The Given contradicts the loaded record (seed §0 item 2) unless it adds the Activity-adjusted Target version (eater-7.18's Approve, `POST /v1/targets`), and it does not say so.
16. **eater-7.25 `/r` 1** — "Today still credits 50 % up to 300 kcal" names no Activity and no figure, so nothing on Today can be read to check it. In eater-7.19's form it would be, for example, a 400 kcal workout giving "Target today 1,910 (1,710 + 200 Activity credit)".
17. **eater-7.25 `/r` 2** — "Today shows an exercise credit of 160 kcal": the same story calls it an "activity credit" in its title and in the offer. Today's line in eater-7.19 is "Activity credit", and the map's word is Activity (§1 ¶4). That is two names for one thing.
18. **eater-7.25 `/r` 3** — "Given he taps 'Not now', Then nothing changes …": the line has no When, and "nothing changes" names no data. It could check, for example, that "Your credit: 50 % up to 300 kcal" still shows, that no new row appears in Progress → Target history, and that `GET /v1/targets/current` is unchanged.
19. **admin-10.77 `/r` 1** — "Wording `ai-processing` version 1 In use and version 2 Proposed": no such Wording exists. The AI Consent's texts are `c-ai-3` and `c-ai-4` (in force since 2026-09-24, event 145), and `c-ai-5` is reserved for counsel's residency wording (seed §4.7; J23, J32; model E12). `ai_processing` is the Consent purpose key, not a Wording id.
20. **admin-10.77 `/r` 2** — "Faisal (AI Consent Given under version 1)": seed §5.1 records Faisal's AI Consent under `c-ai-3`. His app is also in Arabic (seed §5.1), so the sheet he sees uses the Arabic catalogue strings, not "Give consent" and "Not now".
21. **admin-10.77 `/r` 1** — Wording states "In use", "Proposed" and "published": D2 and D4 give Wording no states. Model E12 has none: a Wording is immutable, and a change is a new version with `published_at`. "In use" is a state of Quotas versions and Grant settings versions; "Proposed" is a state of reference records, Policy versions and Registry versions.
22. **admin-10.77 `/r` 1** — "Settings › Wordings" is not a console place (D4 Places (console): Settings › launch gates · Grant settings; D2: language; J47).
23. **admin-10.77 `/r` 1** — under J26 a consent text is published "only after the owner's or counsel's approval is recorded on **Settings › launch gates** ('Privacy review signed by <name> on <date>')" (model E12 `review_gate`). The Given puts a "note 'privacy review 2026-09-30'" on a Proposed version instead of a signed gate; the seed's only signed privacy review is for `c-ai-4`, on 2026-09-24 (event 144). No line checks that publishing is refused before the gate is signed.
24. **admin-10.77 `/r` 1** — "**Audit trail** shows `wording.published` with the reviewer note": `events.md` gives `wording.published` the detail `key`, `version` and languages, with no note (J14: allow-listed keys only). The viewer is also unnamed. The Platform admin's slice (J15: Registry, quotas, prices, spend cap, Roles, Jobs, Grant settings, launch gates) does not include this event (`events.md` lists auditor-9.3 and 10.4 as its readers), so `auditor.a` has to be the one who observes it.
25. **admin-10.77 `/r` 3** — "`POST /v1/admin/wordings/ai-processing/versions/2/publish`": both the join and the event catalogue give the path as `POST /v1/admin/wording` (J49; `events.md` §2). The line also names no account ("an account without 'Publish wording'"; for example `support.a`).
26. **admin-10.77 `/r` 2 and the story's "so that … asked again only where the purpose changed"** — no join item, model row or FRD line decides that a new Wording version blocks photo analysis with `CONSENT_REQUIRED` until the eater gives consent again. J26 decides only who publishes and when. The seed shows the opposite precedent: Mona, Faisal and Sam still hold AI Consents under `c-ai-3` after `c-ai-4` was published (seed §5.1, §4.7; J27), and nothing blocks them. This behaviour is untraced and needs a join decision or an `assumption` label.

### Cross-lens (for the model phase join) — not counted
- **eater-5.44 vs e578** — e578 shows the Saved Plan card on Today (eater-5.20: "no Plan card appears on Today"; eater-5.30: "the Plan card on Today"), but this story reads the card on **Capture & Plan**. One place should be decided.
- **Plan state after expiry (model)** — model E56 has `expired_at` and `events.md` has `plan.expired`, but the D2/D4 Plan states have no state for an expired Saved Plan. The model should name it, along with what `GET /v1/meal-plans/{id}` returns.

## Fix round 1 (2026-10-01)

All six stories were rewritten above. Each defect is fixed where it started: in the id, the Given and its start clock, the seed value, the route or field, or the source of a rule. Counts after the fix: **6 stories, 33 acceptance lines** (admin-10.72: 7 · auditor-10.42: 6 · auditor-10.43: 3 · eater-5.45: 5 · eater-7.25: 6 · admin-10.73: 6). There are 9 assumptions (D6-A1 to A9) and 9 conflicts for the model phase.

| # | defect | fix |
|---|---|---|
| 1 | admin ids 10.76 and 10.77 skipped 10.72–10.75 | Renumbered **admin-10.72** (Grant settings) and **admin-10.73** (Wording). The admin lens ends at admin-10.71. The auditor lens ends at auditor-10.41, so auditor-10.42 and 10.43 stay. The header now names each lens's last id. |
| 2 | "longest Grant 1 hour" misread seed §4.6; "up to 2 hours" named no list | Version 1 is read as seed §4.6 and J2 write it: durations 1 h (default) · 4 h · 24 h, request window 72 h. Version 2 changes the **durations list** (as model E21 holds it) to "1 h (default) · 4 h" and the request window to 48 h, with a reason that matches the change. The Grant form line names what is left: "1 hour (selected) and 4 hours, and nothing longer". |
| 3 | "Choose 15 minutes to 8 hours" had no source and refused version 1's 24 h | Removed. The reason-required line is sourced to J2 ("changed only … with a reason") and uses admin-10.68's copy. The duration bound is **D6-A1** (`assumption`), 1–24 h from support A4 and SR4, which keeps version 1 valid. |
| 4 | "saved by Admin A." | "saved by **Ali N.**" (`staff_ali`, seed §3, §14; J53), with the time in his zone: "13:22 your time (10:22 UTC)" (J147). |
| 5 | `grant_31f0` not Active at the admin's default clock; end time not named | admin-10.72 names **start clock 2026-10-01T10:21:00Z**, after event 198, so `grant_31f0` is Active. Its end is named: `expires_at` 2026-10-01T11:20:00Z, and `staff_mona`'s Dublin panel reads "ends 12:20 your time (11:20 UTC)" (support-10.6). A second Grant sent under version 1, `grant_40aa`, shows that its 72 h window survives a 48 h version 2. |
| 6 | `settings_version`; no reader, and the Platform admin cannot read Grants | Field **`grant_settings_version`** (model E20). The caller is **`staff_hana`** (Auditor, "Read Grants (all)") on `GET /v1/admin/grants/{id}`. |
| 7 | "Auditor A." | "**Hana Q.**" (`staff_hana`). |
| 8 | the review note entered and checked only free text | The note enters and checks **`scope`** (what was reviewed), **`period`**, **`finding`** and **`note`** (J17; events.md §2.10), and no other detail key (J14). One line refuses a note with no finding. The finding values are **D6-A2**. |
| 9 | no seed dataset of 5-year-old events, and no start clock | auditor-10.43 starts at **2026-10-05T09:00:00Z**, the whole seeded trail. It then moves the test clock (J51) to **2031-08-02T00:00:00Z**, when seeded events 1–24 are over 5 years old and event 25 is not. That is reachable in the served product with no new dataset (J52). |
| 10 | elided summary text; `removed_through_seq` and `anchor_hash` not checked; re-anchoring not checked | The `/r` line checks `removed_through_seq: 24` and `anchor_hash` = event 25's `prev_hash`. Verify chain must read "Intact · events 25–…". The `/s` line recomputes `hash(25)` from `anchor_hash`, checks that the summary event is chained, and checks that exactly one summary is written. The run's schedule and the anchor's meaning are **D6-A4**. |
| 11 | NFR-13 cited for the 5-year retention | Removed. The trace is J18, FR-082 ("retention verification"), events.md §1 and §2.10, model 10F.11, E66 and E69. |
| 12 | eater-5.44 already used | Renumbered **eater-5.45**. |
| 13 | no Plan in the seed, and no start clock | eater-5.45 starts at **2026-10-01T18:30:00Z**, seed §2's clock for Faisal's kabsa stories. The Given **creates** the Plan through the API: eater-5.14's request, which deterministically gives 4 rice + 2 chicken at 397.6 kcal, saved as in eater-5.28. The clock is then moved past Faisal's **03:00** boundary: to 23:59Z it is still Day 1 Oct, and at 05:00Z it has expired. `expired_at` is named as 2026-10-02T00:00:00Z. |
| 14 | Policy v2's credit is 50 %/300, and v2 is not in effect at the eater's clock | eater-7.25 starts at **2026-10-06T08:00:00Z**, after v2 comes In effect. The Given adds **Policy version 3** (40 %, 250 kcal) through the approver API: `staff_yara` proposes and `staff_dina` approves, effective 2026-10-07T00:00:00+03:00. The clock then moves to 7 Oct. |
| 15 | Sam is in Fixed mode in the seed | Given (a) adds the Activity-adjusted Target version `tv_sam_2` through eater-7.18's Approve (`POST /v1/targets`) under v2's 50 %/300. |
| 16 | "still credits 50 % up to 300 kcal" named no Activity or figure | Given (d) adds a 400 kcal run through the HealthKit mock. Today must read "Target today 1,910 (1,710 + 200 Activity credit)" in eater-7.19's form, and `GET /v1/targets/current` must return `tv_sam_2` with 0.5/300. |
| 17 | "exercise credit" vs "activity credit" | Today's line is "Activity credit" throughout (eater-7.19). The offer keeps J103's own words. |
| 18 | "Not now" line had no When and named no data | The line is "When he taps Review and then Cancel" (eater-7.18's button). It checks four things: the "Your credit: 50 % up to 300 kcal" line and the offer, Today's 1,910 figure, no new Target history row, and `GET /v1/targets/current` = `tv_sam_2`. The badge and notification claim, which has no source, is dropped. |
| 19 | Wording `ai-processing` does not exist | The Wording is **`c-ai-5`**, reserved for counsel's residency wording (J23, J32; seed §4.7). `c-ai-4` is in force (event 145). |
| 20 | Faisal's Consent is under `c-ai-3`; his app is in Arabic | Faisal's Consent is read as `c-ai-3` with `made_at` 2026-08-25T15:02:10Z (event 78), and it is checked through `GET /v1/me/consents`, so no English copy is claimed for him. The English sheet copy is checked on Nadia (English app), reusing eater-4.3's "Give consent" and "Not now". |
| 21 | Wording states "In use", "Proposed", "published" | No Wording state is used. "Published" appears only as `published_at` and the `wording.published` event (model E12). The missing words are Conflicts, item 4. |
| 22 | "Settings › Wordings" is not a console place | Publishing happens on **Settings › launch gates** (D4, J47), at the privacy review that J26 ties it to. Conflicts, item 4 asks the model phase to confirm or add a place. |
| 23 | a note on a Proposed version instead of a signed gate; no refusal before signing | Line 1 refuses publishing before the gate is signed: Publish is disabled, the API returns 409 (form **D6-A9**), and no `wording.published` is written. Line 2 records "Privacy review signed by counsel R. Haddad (synthetic) on 2026-10-01" for `c-ai-5` (J26 copy; event 144's precedent) and then publishes. The `/s` line checks `review_gate` on E12. |
| 24 | `wording.published` carried a note; no permitted viewer | The detail checked is `key: c-ai-5`, `version: 5` and the languages English and Arabic, with no other key (events.md §2.7; J14). The reader is **`staff_hana`**, the Auditor (J15; events.md lists auditor-9.3 and 10.4). |
| 25 | the publish path was invented; the account was unnamed | The path is **`POST /v1/admin/wording`** (J49; events.md §2.7; model §3 `privacy`). The callers are named: `staff_mona` (no "Publish wording") and `staff_dina` (whose "Publish wording" covers `guidance-1` only, J40). Each gets 403 `FORBIDDEN` and `access.refused`. |
| 26 | blocking analysis until re-consent had no source | Removed. The line now follows the seed's precedent: Faisal's `c-ai-3` Consent stays Given, unchanged, and no sheet appears. New decisions use `c-ai-5` (Nadia). Whether a new Wording asks again is an open question in Conflicts, item 5. The story's so-that clause is rewritten to match. |

**Cross-lens items in the verdict:**
- The Plan card's place: eater-5.45 reads it on **Today**, as e578 does (Conflicts, item 2).
- The Plan state after expiry: eater-5.45 reads model E56 and row 5.14 as they stand. `GET /v1/meal-plans/{id}` returns `state: "saved"` with `expired_at`. Conflicts, item 1 asks the model phase to confirm this or name a state.

**Also fixed in passing:**
- Every Given names its start clock.
- Every reader is named with the permission they hold.
- Four new conflicts were found while fixing and are recorded:
  - eater-5.35's next-morning confirmation (item 3b);
  - the replaced Grant settings version's state (item 6);
  - the credit-offer route (item 7);
  - `credit_cap` vs `credit_cap_kcal` (item 8).
- The Anomalies rule "roles held with no assignment event" after retention is also recorded (item 9).

## Lens verdict — re-verify (2026-10-01)

**fail** — 12 defects. All 26 earlier defects are fixed as the first verdict asked. Four of those fixes (21, 22, 24, 26) followed D2–D4, and the session's later decisions J153 and J154 changed those rules, so the lines now disagree with the model (new defects 8–10). The other 11 counted defects are also places where the stories do not yet carry J149–J157. Read against `way/join.md` (J1–J3, J14–J18, J23–J27, J35, J38, J40, J47, J49–J53, J60, J103–J107, J116, J130, J143, J146, J147 and §22 J149–J157), `way/vocabulary.md` (D2–D6), `way/seed.md`, `way/events.md`, `way/model.md` and the lens files the stories cite.

**What holds**
- **Ids.** admin-10.72 and 10.73 follow admin-10.71 (`admin.md` line 1030). auditor-10.42 and 10.43 follow auditor-10.41. eater-5.45 and 7.25 follow eater-5.44 and 7.24. No id is used twice. `blueprint.md` D5 and `plan-skeleton.md` cite the same six ids.
- **Start clocks and seed values match seed.md.**
  - Grant settings version 1 (§4.6, event 21).
  - `grant_31f0`: requested 10:05, Active 10:20 → 11:20, events 196, 198–201 and 205–207, CASE-1182, 3 · 2 · 0.
  - `grant_40aa`: `staff_omar`, 4 h, requested 10:09, Unanswered 2026-10-04 10:09 (events 197, 263).
  - E10: Africa/Cairo, no Requested Grant at 10:21.
  - Events 1–24 fall on 2026-08-01 06:00–21:00, and event 25 is at 2026-08-03 18:20. No later `seq` has an earlier `occurred_at`.
  - Faisal: 03:00 boundary, Western digits, Fixed mode, 2,040, and 1,400 on 1 Oct (§12.2). His Units are version 1 (§7.2).
  - Sam: `tv_sam_1` 1,870, Entered by you, Fixed mode.
  - Policy v2 equals v1 except the mismatch threshold (§4.2).
  - `c-ai-4`: events 144 and 145. Faisal's `c-ai-3`: event 78, 2026-08-25T15:02:10Z. Nadia (`acct_e9a005`): English, AI Consent Not given.
  - Permissions as §3.
- **Arithmetic is exact.**
  - Time zones: 10:22Z = 13:22 Cairo. 11:20Z = 12:20 Dublin = 14:20 Riyadh. 10:23Z + 48 h = 2026-10-03T10:23Z, which is 11:23 Dublin and 13:23 Cairo. 10:09Z + 72 h = 2026-10-04T10:09Z.
  - Audit trail retention: 2031-08-02T00:00Z is after 2031-08-01T21:00Z (event 24 plus 5 years) and before 2031-08-03T18:20Z (event 25 plus 5 years).
  - The Plan: 18:30Z = 21:30 Riyadh. 23:59Z = 02:59, still Day 1 Oct. `expired_at` 00:00Z = 03:00 Riyadh. 4 × 42.4 + 2 × 114 = 397.6.
  - The activity credit: 1,870 × 2,134.8 ÷ 2,334.8 = 1,709.81, shown 1,710 (J104). 400 × 50 % = 200 ≤ 300, and 1,709.81 + 200 = 1,909.81, shown **1,910**. 400 × 40 % = 160 ≤ 250, and 1,709.81 + 160 = 1,869.81, shown **1,870**. Policy v3's effective-from 2026-10-07T00:00+03:00 = 2026-10-06T21:00Z.
- **Routes, fields, events and codes.**
  - The routes are in model §3 and J49: `GET|PUT /v1/admin/grant-settings`, `GET /v1/admin/grants/{id}`, `POST /v1/grants`, `POST /v1/admin/audit-trail/review-notes|verify`, `GET /v1/meal-plans/{id}`, `GET /v1/meal-plans?state=`, `POST /v1/consumption`, `GET /v1/reports/day`, `POST|GET /v1/targets`, `GET /v1/targets/current`, `POST /v1/admin/policy/versions[/{v}/approve]`, `POST /v1/admin/wording` and `POST /v1/admin/launch-gates/{gate}/sign`.
  - The E20 fields are model E20's: `grant_settings_version`, `request_closes_at` and `expires_at`.
  - Event details match `events.md`: `grant_settings.version.saved` (before → after, `reason`), `access.refused` (`path`, `permission_missing`), `audit_trail.review_noted` (`period`, `scope`, `finding`, `note`), `audit_trail.verified`, `launch_gate.signed`, `consent.given` and `plan.expired` (`state`, `selected_versions`).
  - The codes and statuses follow J35 and J38.
- **Agrees with §22.**
  - J149: each D6-A1…A9 default is applied where its line uses it.
  - J150: the Plan reads `state: "saved"` with `expired_at`, and the copy says "not logged".
  - J152: the Plan card is on Today.
  - J154's outcome: Faisal stays Given under `c-ai-3`.
  - J156's number form: `credit_factor: 0.5`.

**The 26 earlier defects — each fixed (the line now reads):**
1. "## admin-10.72 · Change the Grant settings; a Grant already sent keeps its own" · "## admin-10.73 · Publish a consent Wording after its privacy review is signed".
2. "Grant settings version 1 In use (seed §4.6, event 21: durations 1 h (default) · 4 h · 24 h; request window 72 h)" · "the duration offers 1 hour (selected) and 4 hours, and nothing longer".
3. "the durations field reads "Keep at least one duration, each from 1 to 24 hours" … — `assumption (D6-A1)`" (now J149).
4. "Grant settings version 2 · In use · saved by Ali N. · 13:22 your time (10:22 UTC)".
5. "**Start clock 2026-10-01T10:21:00Z.** … `grant_31f0` … Active since 10:20:00Z, `expires_at` 11:20:00Z (event 198)" · "ends 12:20 your time (11:20 UTC) · 14:20 eater's time".
6. "`staff_hana` (Auditor, holds "Read Grants (all)") calls `GET /v1/admin/grants/grant_31f0` … `grant_settings_version: 1`".
7. "actor Hana Q. (role Auditor)".
8. "detail `scope`, `period`, `finding` and `note` exactly as entered, with no other detail key (J14)" · "Given no finding is chosen … naming `finding`".
9. "**Start clock 2026-10-05T09:00:00Z** … The test clock is then moved (`PUT /v1/test/clock`, J51) to **2031-08-02T00:00:00Z**".
10. "detail `removed_through_seq: 24` and `anchor_hash` equal to event 25's `prev_hash`" · "`SHA-256(anchor_hash ‖ canonical(event 25))` equals event 25's stored `hash`".
11. "Trace: J18, FR-082 ("retention verification"), events.md §1 (Retention) and §2.10 …" (no NFR-13).
12. "## eater-5.45 · A Saved Plan I never confirmed expires at the end of its Day".
13. "**Start clock 2026-10-01T18:30:00Z** … the Given adds one through the public API: eater-5.14's request … returns the Proposed Plan 4 rice + 2 chicken (397.6 kcal)".
14. "`staff_yara` … proposes Policy version 3 based on v2 with one change, the Activity-adjusted credit factor 40 % and cap 250 kcal a day … `staff_dina` … approves it … effective-from 2026-10-07T00:00:00+03:00".
15. "Sam approves Activity-adjusted mode with Policy v2's credit … (eater-7.18's Approve; `POST /v1/targets`), which gives Target version `tv_sam_2`".
16. "Today reads "Food Target: Activity-adjusted" and "Target today 1,910 (1,710 + 200 Activity credit)"".
17. "Target today 1,870 (1,710 + 160 Activity credit)". "exercise credit" appears nowhere.
18. "When he taps Review and then Cancel, Then Settings → Activity → Activity mode still reads "Your credit: 50 % up to 300 kcal" … Progress → Target history has no new row, and `GET /v1/targets/current` still returns `tv_sam_2`".
19. "`c-ai-4` in force for the AI Consent since 2026-09-24T08:00Z (event 145), `c-ai-5` not published".
20. "`ai_processing` still reads Given with `text_version: "c-ai-3"` and `made_at: "2026-08-25T15:02:10Z"`". The English copy is checked on Nadia.
21. Fixed against D2–D4: the story uses no Wording state. J153 has since given Wording states (new defect 9).
22. Fixed against D4: the story no longer names "Settings › Wordings". J153 has since made **Settings › Wordings** the place, so the fix now contradicts the model (new defect 8).
23. "Then Publish is disabled with "Sign the privacy review of c-ai-5 first", and `POST /v1/admin/wording` with key `c-ai-5` returns 409 `VALIDATION_ERROR` with `reason: privacy_review_not_signed`" · "a `review_gate` naming the privacy review signed for `c-ai-5`".
24. "`staff_hana` sees … `wording.published` (actor `staff_ali`; detail `key: c-ai-5`, `version: 5` and the languages English and Arabic …)". Its "with no other detail key" now conflicts with J154 (new defect 10).
25. "`POST /v1/admin/wording` with key `c-ai-5` is called with the token of `staff_mona` … or of `staff_dina`".
26. "`ai_processing` still reads Given with `text_version: "c-ai-3"` … and opening **Capture & Plan** shows him no Consent sheet". The untraced re-consent block is gone. The line's source is now J154 (new defect 10).

**Defects**
1. **admin-10.72 — J155 not carried.** After the save, the only state shown is "Grant settings version 2 · In use · saved by Ali N. …". No line reads version 1 after it is replaced, and Conflicts item 6 still says "admin-10.72 does not name version 1's state after the save". J155 decides: "A replaced Grant settings version is "Replaced" … In use → Replaced · Rolled back" (also D6). A line must observe version 1 as Replaced, on Settings › Grant settings and in `GET /v1/admin/grant-settings`.
2. **auditor-10.43 — J157 not carried.** The `/r` line checks only "`removed_through_seq: 24` and `anchor_hash` …". J157: "The `audit_trail.retention_run` summary event carries `roles_held_at_anchor` (each user's roles at the anchor). The Anomalies rule "roles held with no assignment event" treats a role listed there as assigned." The run removes events 1–6, the `role.assigned` events of `staff_ali`, `staff_mona`, `staff_omar`, `staff_dina`, `staff_hana` and `staff_lee`. No line checks `roles_held_at_anchor`. No line checks that Audit trail › Anomalies "roles held with no assignment event" still counts 1 (`staff_sod_seed`) after the run. Conflicts item 9 is still written as open.
3. **eater-5.45 `/r` 5 — J151: the refusal names no `made_at`.** "When `POST /v1/consumption` is sent with Faisal's token, a new command id, the Plan's own counts and versions and `source_plan_id` set to its id, Then it returns 409 `VALIDATION_ERROR` with `reason: plan_expired`". Under J151, "A consume command carries the device's `made_at`; when `made_at` is before the Plan's `expired_at`, the server accepts it once onto the Plan's Day". Without a `made_at` after 2026-10-02T00:00:00Z in the command, the line does not decide between 409 and acceptance.
4. **eater-5.45 — J151's accepted late confirmation has no line.** The story still follows D6-A7's old scope: "A confirmation made offline before `expired_at` and delivered later is not decided here (Conflicts, item 3)". J151 decides it: the server accepts it once onto the Plan's Day. A line is needed, for example: "Ate as planned" tapped offline at 2026-10-01T23:50Z (02:50 Riyadh) and delivered at 05:00Z is accepted once onto Day 2026-10-01 (1,400 + 397.6 = 1,797.6). The Plan becomes Confirmed (`confirmed_as: "ate_as_planned"`), and a repeat delivery adds nothing.
5. **eater-5.45 — J150's list has no line.** Conflicts item 1 asked the model phase to "say what `GET /v1/meal-plans?state=saved` lists". J150 answers: "`GET /v1/meal-plans?state=saved` lists unexpired Plans; `&include_expired=true` lists all". No line checks either at 2026-10-02T05:00:00Z. The only list check is `?state=proposed`.
6. **eater-7.25 `/s` — two names for one field.** The line reads "`credit_factor: 0.4` and `credit_cap: 250`". J156: "`credit_factor` (fraction) and `credit_cap_kcal` everywhere — `events.md` is aligned". `events.md` §3 gives the `target.version.approved` payload "`credit_factor`, `credit_cap_kcal`". The same story writes `credit_cap_kcal` in its `/r` 2 and in this line's `tv_sam_2` check, so it uses two names for one field.
7. **eater-7.25 — J156's routes are not used.** Conflicts item 7 still says "eater-7.25 observes it on screen only", and the raise check calls only "`POST /v1/targets` with `credit_factor: 0.45`". J156: "`GET /v1/targets/activity-credit-offer` returns the newer Policy's credit when it differs from the eater's Target version, and `POST /v1/targets/activity-credit-offer/approve` creates the new Target version". The missing checks:
   - With Given (a) only on 6 Oct, the offer route returns no offer.
   - With (a)–(d) on 7 Oct, it returns `credit_factor: 0.4`, `credit_cap_kcal: 250` and `policy_version: 3`.
   - After Approve, it returns no offer.
   - Tapping Approve calls the approve route.
   - A raised factor sent to the approve route is refused.
8. **admin-10.73 `/r` 1 — the wrong place.** The line reads "When `staff_ali` … tries to publish `c-ai-5` on **Settings › launch gates**, Then Publish is disabled …". J153: "Console place: **Settings › Wordings**" (also D6). The privacy review is still signed on Settings › launch gates (J26). Publishing, and the disabled Publish with its message, belong on Settings › Wordings. Conflicts item 4 still proposes launch gates.
9. **admin-10.73 — no Wording states.** The Given says only "`c-ai-5` not published". The `/s` line checks only that "the stored English and Arabic texts of `c-ai-4` and `c-ai-3` are byte for byte unchanged". J153: "A Wording version is Proposed → Published · Superseded (a newer version of the same key is Published)". No line reads `c-ai-5` as Proposed before the publish and Published after it. No line reads `c-ai-4` as Superseded after the publish, alongside Consents under `c-ai-4` keeping their version. Fix-log row 21's "No Wording state is used" now runs against D6.
10. **admin-10.73 `/r` 2, `/s`, `/r` 4 — `asks_again` is missing and refused.** `/r` 2 reads "`wording.published` (… detail `key: c-ai-5`, `version: 5` and the languages English and Arabic, with no other detail key …)". `/r` 4 rests on "the seed's precedent … see Conflicts, item 5". J154: "Each Wording version carries `asks_again` (set by the publisher, recorded in the Audit trail) … The seed's `c-ai-5` has `asks_again: false`". The publish never sets `asks_again`. The `/s` line does not read it on the Wording. The event line forbids the key that J154 says is recorded. Faisal's "no Consent sheet" should trace to `asks_again: false` (J154), not to a precedent and an open conflict.
11. **admin-10.73 — J154's `asks_again: true` path has no line.** J154: "When true, the next use of that purpose shows the Consent sheet and the purpose stays blocked (`CONSENT_REQUIRED`) until the eater gives or declines it". No story covers it. For example: a version published with `asks_again: true`; Faisal's next Text analysis on Capture & Plan shows the sheet; `POST /v1/analyses` returns 403 `CONSENT_REQUIRED` until he decides; his `c-ai-3` record stays as it was.
12. **Proposed for D6 and Conflicts — stale after J149–J157.** The file still says "Nothing in `join.md`, `seed.md` or the FRD decides these … each default can be replaced by a dated delta D6", and lists Conflicts items 1–9 as open. J149 says "The nine defaults D6-A1…A9 are adopted as written, except where J151 below widens D6-A7", and J150–J157 decide items 1–9. The ten `assumption (D6-An)` labels in the story lines should trace to J149, to J151 for A7's wider rule and to J156 for A8. Line 3's "`way/vocabulary.md` (D2–D4)" is now D2–D6.

### Cross-lens and model documents (for the model phase) — not counted
- **`events.md` lags §22.** `wording.published` has no `asks_again` (J154). `audit_trail.retention_run` has no `roles_held_at_anchor` (J157). `target.version.approved` names only `POST /v1/targets` as its request (J156 adds `POST /v1/targets/activity-credit-offer/approve`).
- **`model.md` lags §22.**
  - §3 `targets` routes lack J156's two routes.
  - §1 row 7.8 still writes `credit_cap` (J156).
  - E12 Wording has no state and no `asks_again` (J153, J154).
  - E21's states lack Replaced (J155).
  - No module names the console place Settings › Wordings (J153).
- **`seed.md` §4.7 has no `c-ai-5` row.** J154 says "The seed's `c-ai-5` has `asks_again: false`". admin-10.73 has the test supply the texts. The model phase should say whether `c-ai-5` is a seeded Proposed Wording, and at what time.

## Fix round 2 (2026-10-01)

The stories now carry the session's decisions in `join.md` §22 (J149–J157, delta D6; vocabulary D6). They were also checked against `contracts/openapi.yaml`: every route, field name, wire value (numbers as strings, ISO durations, lower-case states) and error body matches an operation, schema or example there. Each of the 12 re-verify defects is fixed where it started.

**Counts after the fix:** 6 stories, 36 acceptance lines.

| story | lines |
|---|---|
| admin-10.72 | 7 |
| auditor-10.42 | 6 |
| auditor-10.43 | 4 |
| eater-5.45 | 6 |
| eater-7.25 | 6 |
| admin-10.73 | 7 |

"Proposed for D6" and the round-1 Conflicts are replaced by one line each, citing the J item that decided them. Four open items remain, all for the model phase and none for the owner.

| # | defect | fix |
|---|---|---|
| 1 | admin-10.72 did not carry J155 | `/r` 1 now reads "Version 1 · Replaced" on Settings › Grant settings, and `PUT`/`GET /v1/admin/grant-settings` return `version: 2`, `state: "in_use"`. `/r` 7 shows the Auditor version 1 as Replaced. The contract's GET returns only the version in use, so version 1's state has no API read; that gap is Open item 2. |
| 2 | auditor-10.43 did not carry J157 | `/r` 1 checks `roles_held_at_anchor`: exactly six holders (`staff_ali` Platform admin; `staff_mona`, `staff_omar` and `staff_lee` Support agent; `staff_dina` Nutrition approver; `staff_hana` Auditor), matching the contract example. It also names the staff who held no role then. A new `/r` checks that Audit trail › Anomalies "roles held with no assignment event" still counts 1 (`staff_sod_seed`) after the run. Round-1 conflict 9 now cites J157. |
| 3 | eater-5.45 `/r` 5 named no `made_at` | The refused command carries `made_at: "2026-10-02T05:00:00Z"`, after `expired_at`. The answer is the contract's body: 409 `VALIDATION_ERROR`, `reason: "plan_expired"`, `state: "saved"`, `field: "source_plan_id"` (J149). |
| 4 | J151's accepted late confirmation had no line | A new `/r`: "Ate as planned" is tapped offline at 2026-10-01T23:50:00Z (02:50 Riyadh) with `made_at` before `expired_at`, and delivered at 05:00:00Z. It is accepted once onto Day 2026-10-01: `consumed_kcal: "1797.6"` (1,400 + 397.6), `day_revision` one higher, the Plan `confirmed` and `ate_as_planned` with both `entry_ids`, and a repeat delivery `replayed: true` adds nothing. |
| 5 | J150's list had no line | `/r` 2 checks that `GET /v1/meal-plans?state=saved` returns no Plan and that `&include_expired=true` lists this Plan with its `expired_at`. |
| 6 | eater-7.25 used `credit_cap` and `credit_cap_kcal` | One name, `credit_cap_kcal`, everywhere (J156), with contract values `credit_factor: "0.4"` and `credit_cap_kcal: "250"`. The `/s` line's `target.version.approved` payload uses it too. |
| 7 | J156's routes were not used | `/r` 1 calls `GET /v1/targets/activity-credit-offer` twice. On 6 Oct, with Given (a) only, it returns no `offer`. On 7 Oct it returns the offer {3, "0.4", "250", current `tv_sam_2` "0.5"/"300"/2}. Approve sends `POST /v1/targets/activity-credit-offer/approve`, which returns 201 `tv_sam_3`, after which the offer is gone. Cancel leaves the offer in place. The raise check sends `credit_factor: "0.45"` to the approve route and gets 422 `field: "credit_factor"`. Today's credit is also read from the Day report as `target.activity_credit_kcal` "200" and "160". |
| 8 | admin-10.73 published from the wrong place | Publishing, and the disabled Publish with "Sign the privacy review of c-ai-5 first.", happen on **Settings › Wordings** (J153). Signing stays on Settings › launch gates (J26). |
| 9 | admin-10.73 used no Wording states | The Given reads `c-ai-4` Published and `c-ai-3` Superseded. After the publish, Settings › Wordings and `GET /v1/wording` read `c-ai-5` Published and `c-ai-4` Superseded (the `/s` line reads all three states), and the asks-again line makes `c-ai-5` Superseded by `c-ai-6`. `c-ai-5` cannot be read as Proposed: the seed has no `c-ai-5` row, and the contract stores a Wording only on publish. That is Open item 1, which the model phase must close. |
| 10 | `asks_again` was missing, and its key was forbidden | The publish sends `asks_again: false` and the answer carries it. The `/s` line reads `c-ai-5` with `asks_again: false`. `wording.published` now checks `asks_again: false` in its detail (J154: "recorded in the Audit trail"), so the "no other detail key" clause is gone. Faisal's line traces to J154 and reads `text_version_in_force: "c-ai-5"` and `asks_again: false` from `PurposeConsent`; a 201 analysis shows that nothing blocks him. |
| 11 | J154's `asks_again: true` path had no line | A new `/r`: `c-ai-6`, a synthetic text with a changed purpose, is signed and published with `asks_again: true`. Faisal's `ai_processing` reads `asks_again: true` with his record still `c-ai-3`. His next typed words show the sheet with `c-ai-6`'s Arabic text, and `POST /v1/analyses` returns 403 `CONSENT_REQUIRED` with no analyzer call. "Give consent" records `c-ai-6`, and the next analysis returns 201. Event 78 is unchanged. |
| 12 | "Proposed for D6" and Conflicts were stale; vocabulary was cited as "D2–D4" | Both sections are replaced by "Decided by D6" (A1–A9, each with its J item) and "Conflicts from round 1 — decided by D6" (items 1–9, each with its J item). Every `assumption (D6-An)` label in the story lines now cites J149, J151 or J156. The header cites `way/vocabulary.md` (D2–D6), §22 and the contract. |

**Contract alignment beyond the defects:**
- admin-10.72 uses `PT1H`/`PT4H`/`PT24H`/`PT48H`, `expected_version` and the contract's durations message.
- auditor-10.42 uses `finding: "no_issue"`, `period` {`from`, `to`} and `ChainCheck` (`result: "intact"`, `from_seq`).
- auditor-10.43 uses the actor `system:audit_retention` and the Summary's `last_retention_run.chain_check: "passed"`, as the contract's `x-stories` for `getAuditSummary` expects.
- eater-5.45 uses the Unit version ids `uv_faisal_kabsa_rice_spoon_v1` and `uv_faisal_chicken_piece_v1` and `consumed_kcal` as strings.
- admin-10.73 uses `SignGate` (`version_reviewed`, `signer_name`), so the signer reads "R. Haddad", and `PublishWording` (`text_version`, `en`, `ar`, `asks_again`).

**Found while fixing** (Open items 3 and 4, not counted):
- `events.md` lags §22 on `wording.published` (`asks_again`), `audit_trail.retention_run` (`roles_held_at_anchor`) and `target.version.approved` (its request route).
- The contract has three slips: a stale "admin-10.76" on `Grant.grant_settings_version`, "+ 72 h" on `Grant.request_closes_at`, and the launch-gates example's 07:30 time for the `c-ai-4` privacy review against seed event 144 at 07:55.

## Open items closed by the session (2026-10-01)
1. A Proposed Wording can be created and read: `POST /v1/admin/wording/proposals` (event `wording.proposed`) and `GET /v1/admin/wording/versions`; the seed holds `c-ai-5` as **Proposed** (seed §4.7, `asks_again: false`).
2. Every Grant settings version with its state is readable through `GET /v1/admin/grant-settings/versions`.
3. `events.md`: `wording.published` carries `asks_again`; `audit_trail.retention_run` carries `roles_held_at_anchor`; `target.version.approved` names the activity-credit-offer route.
4. Contract slips fixed: `grant_settings_version` cites admin-10.72; `request_closes_at` reads the request window of its Grant settings version; the `c-ai-4` privacy review is recorded at 07:55 (seed event 144).
The contract still validates (OpenAPI 3.1, 149 paths).

## Lens verdict — closing (2026-10-01)

**fail** — 4 defects. Ten of the 12 re-verify defects are fixed. Two (1 and 9) are fixed only as far as the contract allowed at fix round 2. The session then closed those gaps ("Open items closed by the session" 1 and 2), but the stories were not brought up to the new contract and seed. Read against `way/vocabulary.md` (D2–D6), `way/join.md` (J2, J17, J18, J26, J51, J52, J103–J107, J130 and §22 J149–J157), `way/seed.md` (§2, §3, §4.6, §4.7, §5, §9, §11, §12.2, §13), `way/events.md` and `contracts/openapi.yaml` (OpenAPI 3.1.0, 149 paths, every `$ref` resolves).

**The 12 re-verify defects (each line as it now reads)**
1. J155 — **half fixed.** The screen half is done: "The page's version list reads "Version 1 · Replaced" (J155)." and "she reads version 2's values and version 1 as Replaced, with no edit or Save control". The Conflicts line is done: "6. A replaced Grant settings version → **J155**: Replaced." No API line reads version 1's state (closing defect 1).
2. J157 — fixed. "Its `roles_held_at_anchor` lists exactly `staff_ali` [Platform admin], `staff_mona` [Support agent], …" · "Then "roles held with no assignment event" still counts 1, with `record_ids` [`staff_sod_seed`]." · "9. Retention and the "roles held with no assignment event" rule → **J157**". This matches the contract's `retention` example, seed events 1–6 and seed §13 ("roles held with no assignment event (`staff_sod_seed`) 1").
3. `made_at` — fixed. "`made_at: "2026-10-02T05:00:00Z"` (after `expired_at`)" · "returns 409 `VALIDATION_ERROR` with `reason: "plan_expired"`, `state: "saved"` and `field: "source_plan_id"` (J149)". This is the `consume` 409 example word for word.
4. J151's accepted late confirmation — fixed. "**A confirmation made before expiry and delivered after it is accepted** (J151)" · "`consumed_kcal: "1797.6"` (1,400 + 397.6) with `day_revision` one higher" · "returns the original answer with `replayed: true` and adds nothing".
5. J150's list — fixed. "`GET /v1/meal-plans?state=saved` returns no Plan, and `GET /v1/meal-plans?state=saved&include_expired=true` lists this Plan with its `expired_at` (J150)." `listMealPlans` has `include_expired`.
6. One field name — fixed. "`credit_factor: "0.4"` and `credit_cap_kcal: "250"` (J156: one field name everywhere)". A bare `credit_cap` appears in no story line.
7. J156's routes — fixed. "`GET /v1/targets/activity-credit-offer` returns no `offer`" · "returns `offer` {`policy_version: 3`, `credit_factor: "0.4"`, `credit_cap_kcal: "250"`, `current` {…}}" · "the app sends `POST /v1/targets/activity-credit-offer/approve`" · "`POST /v1/targets/activity-credit-offer/approve` with `credit_factor: "0.45"` returns 422 `VALIDATION_ERROR` with `field: "credit_factor"`". These match the contract's `sam` and `raised` examples.
8. Place — fixed. "When `staff_ali` enters `c-ai-5`'s texts on **Settings › Wordings** … Then Publish is disabled with "Sign the privacy review of c-ai-5 first."" Signing stays "on Settings › launch gates" (J26).
9. Wording states — **half fixed.** Published and Superseded are read: "`c-ai-4` Published since 2026-09-24T08:00Z (event 145) and `c-ai-3` Superseded" · "Settings › Wordings lists `c-ai-5` Published and `c-ai-4` Superseded (J153)" · "it returns `state` `superseded`, `superseded` and `published`". No line reads `c-ai-5` as Proposed (closing defects 2 and 3). The earlier-Consent half holds through Faisal's `c-ai-3` record, which J154 covers for every earlier version.
10. `asks_again` — fixed. "{`text_version: "c-ai-5"`, `en`, `ar`, `asks_again: false`}" · "`wording.published`: … and `asks_again: false` (J154: recorded in the Audit trail)" · "`c-ai-5` has `asks_again: false`" · "`text_version_in_force: "c-ai-5"` and `asks_again: false`". "No other detail key" is gone.
11. The `asks_again: true` path — fixed. "**A version marked to ask again blocks the purpose** (J154)" · "`POST /v1/analyses` with his token returns 403 `CONSENT_REQUIRED`, and the analyzer mock receives no request." · "`staff_hana` sees a new `consent.given` with `text_version: c-ai-6`, while event 78 … is unchanged."
12. Stale sections — fixed as asked. "`way/vocabulary.md` (D2–D6);" · "## Decided by D6 (`join.md` §22) — formerly "Proposed for D6"" · "## Conflicts from round 1 — decided by D6". No `assumption (D6-An)` label is left in the story lines. A new stale list has taken their place (closing defect 4).

**Routes, fields, events and seed values: what holds**
- **Routes.** All 26 operations named in the stories exist in the contract with those methods, including the following:
  - `PUT /v1/test/clock`;
  - `POST /v1/admin/launch-gates/privacy_review/sign` (`LaunchGateKey` `privacy_review`, `SignGate` `version_reviewed` and `signer_name`);
  - `GET /v1/wording` with a repeated `text_version`;
  - `GET /v1/reports/day` (`DayReport.consumed_kcal`, `day_revision`, `target.activity_credit_kcal`);
  - `GET /v1/me/consents` (`PurposeConsent.text_version_in_force`, `asks_again`; `ConsentRecord.text_version`, `made_at`, `method`, `context`).
- **Fields and wire values.** These match the schemas and examples: `GrantSettingsIn` and `GrantSettingsVersion` (`in_use`, `PT48H`, the durations message), `Grant.grant_settings_version` and `request_closes_at`, `ReviewNote` (`finding: no_issue`) and the 422 note message, `ChainCheck`, `PeriodSummary.last_retention_run.chain_check`, `RetentionRunDetail`, `Plan.expired_at` · `entry_ids` · `confirmed_as`, `ConsumeCommand.made_at` · `source_plan_id`, `ApproveActivityCreditOffer`, `TargetVersion` (`tv_sam_2` and `tv_sam_3` examples), `ProposePolicy.values.activity_adjusted_credit`, `ApprovePolicy` (`reason`, `effective_from`), `PublishWording`, `Wording` (`published_by`, `review_gate`) and the 409 `not_signed` example.
- **Events.** Every event the stories name is in `events.md`, with the detail fields the stories check: `grant_settings.version.saved`, `access.refused` (`path`, `permission_missing`), `audit_trail.review_noted`, `audit_trail.verified`, `audit_trail.retention_run` (now with `roles_held_at_anchor`), `launch_gate.signed`, `wording.published` (now with `asks_again`), `consent.given`, `plan.expired`, `entry.confirmed` (`source_plan_id`) and `target.version.approved` (it now names `POST /v1/targets/activity-credit-offer/approve`).
- **Seed values and time arithmetic.**
  - Grant settings version 1 (§4.6, event 21), `grant_31f0` and `grant_40aa` (§9, events 196–207 and 263), E10's zone and its Grants.
  - Events 1–25, the staff roles and their dates (§3), Faisal's Day of 1,400 and his Unit versions (§12.2, §7.2), `tv_sam_1`, Policy v2 (§4.2, event 266), Nadia, and Faisal's event 78.
  - The time-zone arithmetic holds.
- **The session's closures exist.** `POST /v1/admin/wording/proposals` (`proposeWording`, event `wording.proposed`), `GET /v1/admin/wording/versions` (`listWordings`, `WordingState` proposed · published · superseded), `GET /v1/admin/grant-settings/versions` (`listGrantSettingsVersions`, `GrantSettingsState` in_use · replaced · rolled_back), seed §4.7 "`c-ai-5` | 5 | **Proposed** 2026-09-30T10:00:00Z by `staff_ali`, `asks_again: false`", and the three contract slips (now admin-10.72, "the request window of the Grant settings version", 07:55:00Z).
- **Counts.** 6 stories and 36 acceptance lines (7 · 6 · 4 · 6 · 6 · 7), as fix round 2 says.

**Defects**
1. **admin-10.72 — version 1's Replaced state still has no API line (re-verify 1, unfinished).**
   - The re-verify asked for a line that observes version 1 as Replaced both on Settings › Grant settings and through the API. The only API reads are "`PUT /v1/admin/grant-settings` … returned 200 with `version: 2` and `state: "in_use"`, and `GET /v1/admin/grant-settings` now returns the same".
   - The contract now has `GET /v1/admin/grant-settings/versions`: "Every Grant settings version with its state (In use · Replaced · Rolled back, J155)", with `x-stories: [admin-10.72]`. No line calls it. A line is needed that reads version 1 `replaced` and version 2 `in_use` there, for example from `staff_hana`.
2. **admin-10.73 — `c-ai-5` is never read as Proposed (re-verify 9, unfinished).**
   - J153: "A Wording version is Proposed → Published · Superseded". Fix-round-2 row 9 deferred this ("`c-ai-5` cannot be read as Proposed … That is Open item 1"). The session closed that item: seed §4.7 now holds `c-ai-5` **Proposed**, and `GET /v1/admin/wording/versions` returns each version's state.
   - Still no line reads `c-ai-5` `proposed` before the publish, on Settings › Wordings or through that route. The `/s` line reads only "`superseded`, `superseded` and `published`".
   - `listWordings`, `proposeWording` (`x-stories: [admin-10.73]`) and `events.md` §2.7 `wording.proposed` (reader "admin-10.73") all cite this story, but it exercises none of them.
3. **admin-10.73 — the Given contradicts seed §4.7.**
   - The story says "Seed §4.7 holds no `c-ai-5` row, so the test supplies counsel's `c-ai-5` English and Arabic texts." Seed §4.7 now holds "`c-ai-5` | 5 | **Proposed** 2026-09-30T10:00:00Z by `staff_ali`, `asks_again: false`". J52's loader writes that record before the 2026-10-01T09:00:00Z start clock.
   - The Given's "**Settings › Wordings** (J153) shows family `c-ai`: `c-ai-4` Published … and `c-ai-3` Superseded" leaves out the Proposed `c-ai-5` that the page will list.
   - In `/r` 1, "When `staff_ali` enters `c-ai-5`'s texts on **Settings › Wordings**", he types texts into a version whose stored texts are immutable (model E12) and already exist. The Given and line 1 should start from the seeded Proposed `c-ai-5`.
4. **Open items listed as open after the session closed them** (the same class as re-verify 12).
   - Line 25 still says "The few points D6 still leaves open are under "Open items for the model phase"". "## Open items for the model phase (after D6)" still states, as current, that "`contracts/openapi.yaml` stores a Wording only on publish", "Seed §4.7 has no `c-ai-5` row", "`GET /v1/admin/grant-settings` returns only the version in use. … A `version` parameter or a versions list would let the API show it", "`events.md` lags §22" and the three contract slips.
   - Fix round 2 says "Four open items remain". All four are closed (see "Open items closed by the session"), and each claim above is now false against the contract, seed or events.
   - Line 25 and that section should say closed, or be replaced by one line per closure.

### Cross-lens and model documents (for the model phase) — not counted
- **Seed §11 has no `wording.proposed` for `c-ai-5`.** §4.7 records `c-ai-5` Proposed at 2026-09-30T10:00:00Z by `staff_ali`, but the seeded trail has no `wording.proposed` event. `events.md` §2.7 writes one whenever a version is stored as Proposed. Adding it renumbers every later event, and many stories cite "events 1–266" and fixed `seq` values.
- **`PublishWording` requires `en` and `ar` even for a stored Proposed version.** The model should say whether a publish of a Proposed version sends only `text_version`, or what happens when the texts differ from the stored ones.
- **The contract's `getAnomalies` `seed` example lists no "roles held with no assignment event" rule.** Seed §13 lists it at count 1 (`staff_sod_seed`), and auditor-10.43 reads it.
- **`x-stories` gaps.**
  - `setTestClock` lacks auditor-10.43 and eater-7.25.
  - `getAnomalies` and `listAuditTrailEvents` lack auditor-10.43.
  - `signLaunchGate`, `getWording`, `listConsents` and `createAnalysis` lack admin-10.73. This is the only story that exercises J154's `asks_again` read and its `CONSENT_REQUIRED` block.

## Second closing fix by the session (2026-10-01)
1. admin-10.72 reads version 1 as `replaced` through `GET /v1/admin/grant-settings/versions` (J155).
2–3. admin-10.73 starts from the seeded Proposed `c-ai-5` (seed §4.7) and reads it through `GET /v1/admin/wording/versions`; the admin opens it rather than entering texts; `c-ai-6` is proposed through `POST /v1/admin/wording/proposals` and `wording.proposed` is read.
4. The open-items text now says what is closed.
