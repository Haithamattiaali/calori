# Join — the model phase (2026-10-01)

The lenses were judged on their own stories; every mismatch between them was routed here (`way/lessons.md`, 2026-10-01). This file settles each one once. It reads `way/blueprint.md` §0–§3, `way/vocabulary.md` (D2, D3), `way/brief/frd-v1.0.md`, every lens file's "Conflicts for the model phase" list and every verdict's "Cross-lens (for the model phase join)" items, plus the governor's notes in `way/governor/lenses.md`. The shared fixture set is `way/seed.md`; the one event catalogue is `way/events.md`. Both are binding wherever this file points to them.

**How to read an item.** Each item **Jn** names the lenses and story ids involved, the **Decision** (one rule, in the map's and the vocabulary's words), the **Reason** (FRD, map, vocabulary or research ids), and the story lines it **Supersedes**. A superseded line is read as the decision says until the lens is next edited; the contracts and tests follow the decision, never the old line.

**Precedence used for every decision.** The FRD (FR, AT, NFR, §) → the map (`blueprint.md` §1) → the vocabulary (D2, D3) → research findings that stand after refutation → the lens with more stories built on the value → the choice that best serves the persona's journey. Where the FRD truly leaves a product choice open, the decision says **(product choice — the owner can revisit it)**. Nothing here is a question for the owner.

**Lens short names.** admin = `personas/admin.md`, approver = `personas/approver.md`, support = `personas/support.md`, auditor = `personas/auditor.md`, research = `personas/eater/research.md`, e19 = `eater/wf1-wf9.md`, e24 = `eater/wf2-wf4.md`, e36 = `eater/wf3-wf6.md`, e578 = `eater/wf5-wf7-wf8.md`. Source item ids in the coverage index (§20): admin §7 "A7.n", approver §7 "P7.n", support "Kn", auditor "A-n" (part A) and "Mn" (part B), research §6 "R6.n", e19 "C-n", e24 §5 "W2.n", e36 §6 "W3.n", e578 §7 "W5.n"; cross-lens items are named in words in §20.

---

## 1 · Grant flow

**J1 · One Grant state machine, and Approved becomes Active in the same transaction**
- Lenses: support-10.2 `/m`, 10.6 `/s`, 10.15, 10.23; e19 eater-10.2 `/s`, 10.6; auditor-10.3, 10.6, 10.8 (M6).
- Decision: the paths are exactly D2's: Requested → Approved → Active → Expired · Ended · Withdrawn; Requested → Declined · Unanswered; plus **Requested → Ended** (J3). The eater's approve command records Approved and makes the Grant Active in one transaction; the time box starts at that instant: `active_from` = approval time, `expires_at` = `active_from` + duration. The box is half-open, [`active_from`, `expires_at`): a read at `expires_at` is refused (auditor-10.8 `/s`). One Audit trail event records the decision: `grant.approved` (carrying `active_from` and `expires_at`); there is no separate `grant.active` event. The app and the console show "Active"; "Approved" appears only in the Audit trail and the API's `approved_at`.
- Reason: D2 Grant states ("reads are allowed only while Active"); support §0.1 (SR1: the box starts at approval); one decision is one event (auditor event design, O1); eater-10.6 already expects one `grant.approved`.
- Supersedes: support-10.6 `/s` "the Audit trail has `grant.approved` and `grant.active`"; support-10.15 `/r` "`grant.approved` and `grant.active` (10:20)"; support-10.23 "requested → approved → active → read ×3 …" (read: requested → approved → read ×3 → expired → read_refused ×2); support §11 row 10.6 "the auditor sees `grant.approved` and `grant.active`".

**J2 · Grant limits live in a versioned "Grant settings", owned by the Platform admin**
- Lenses: support K3, A4–A7, A26; auditor A-10; research R6.6; e19 EA10.
- Decision: durations 1 h (default) · 4 h · 24 h; at most 14 Days per Grant, none after the eater's current Day; request window 72 h (then Unanswered); one Requested Grant per eater at a time (Active ones do not count); no extension and no edit after sending (`PATCH` → 405); the eater answers only online (nothing is queued); console idle sign-out 15 min with a warning at 13 min; email look-up at most 30 per agent per hour; the Grant bar warns at 10 and 2 minutes. These values form **Grant settings version n** (seeded version 1 = these values, `seed.md` §4.6), shown on console **Settings › Grant settings**, changed only by the Platform admin with a reason, read-only for the Auditor, every change written as `grant_settings.version.saved`. They are not nutrition Policy (approver-owned, map §1 ¶6) and not a Registry version.
- Reason: FR-081 (just-in-time, time-boxed); map §3 ("time box, read-only, auto-expiry"); D2 (Policy belongs to the approver, Registry to model configuration); support SR1/SR4/SR5 findings. **(product choice — the owner can revisit the values)**
- Supersedes: nothing in a story; support §12 K3 and auditor §7 A10 are closed.

**J3 · A Support agent may cancel a request that is still Requested**
- Lenses: support K10, support-10.5 `/r` 3rd line; auditor cross-lens (cancelling a Requested Grant).
- Decision: add the transition **Requested → Ended (by the Support agent)**, shown to the agent as "Cancel request" on the Grant panel and to the eater as "Cancelled by the Support agent · <time>" in the Grant's history; event `grant.ended` with `detail: cancelled_before_answer`. A cancelled request frees the one-waiting-request slot at once.
- Reason: a mistaken request should not sit on the eater's screen for 72 h (care group 4: undo exists; research §3 WF-10 row "a request that is hard to decline"); the change is the smallest one D2 allows (reuses Ended). Added to delta D4.
- Supersedes: support-10.5 `/r` "the agent looks for a way to cancel it, Then none exists, and the panel says 'A request ends when the eater answers, or when it closes …'" (read: a "Cancel request" control exists; the panel still states the closing time).

**J4 · No reminder before a request closes; silence is never yes**
- Lenses: research R6.6; support K2; e19 C-15, eater-10.1, 10.4.
- Decision: the eater learns of a request by the Settings badge (always) and one notification only if notifications were already allowed (no permission prompt). No reminder is sent before the 72 h window closes. A request left unanswered becomes Unanswered and gives no access; a late approve returns 409 `GRANT_NOT_ACTIVE` with state Unanswered.
- Reason: D2 (Unanswered gives no access); R2 (no prompt for unrelated permissions); EX-12 discreet logging (E24). **(product choice — the owner can revisit it)**
- Supersedes: none.

**J5 · Only the eater approves; one person with two accounts needs no link**
- Lenses: support-10.9; auditor-10.6 `/s`, A-9, M6; blueprint §1.2 ("no staff member can approve their own request").
- Decision: no staff role holds an approve permission; any staff token on `POST /v1/grants/{id}/approve|decline|withdraw` gets 403 `FORBIDDEN` and writes **`access.refused`** (one event name for every missing-permission refusal; `detail.attempted: grant.approve`). Because staff can never approve, a person who also uses the app as an eater can approve only requests about their own diary, which is their right; no staff-to-eater account link is built.
- Reason: D2 ("a staff account is never an eater account"; `FORBIDDEN` = role lacks the permission); FR-081.
- Supersedes: support-10.9 `/s` "`grant.approve_denied` is in the Audit trail" (read: `access.refused`); support §0.2 `grant.approve_denied`; support §11 row 10.9.

**J6 · What a Grant request holds, what it may cover, and what it never covers**
- Lenses: support-10.2, 10.11, 10.14; e19 eater-10.2; auditor-10.4, A-5; support K12(f).
- Decision: a request holds account id, the eater's language and time zone (filled in, not chosen), a reason code from the catalogue (`sync_missing_entry`, `report_mismatch`, `unit_calculation`, `activity_import`, `other` + one-sentence note), Days, areas, duration, case reference, and the request wording version shown to the eater (`grant-req-1`). Areas are exactly **Entries and day reports · My Units · Templates · Activity**. Never included, whatever the request says: photos, audio, transcripts, the Target and goal settings, the safety-screen answers and mode, weight and profile values. A Grant's day report reads "Target — not included in Grants" and shows no Target, remaining or over value.
- Reason: FR-081, §19.2; support SR5 (minimum necessary); e19 eater-10.2 already promises "Never included: photos, voice, your Target and goal settings".
- Supersedes: e19 eater-1.19's Shared note "a Support agent's view shows 'No Target'" (read: "Target — not included in Grants").

**J7 · Reads outside the Grant, another eater's id, and writes under a Grant**
- Lenses: support-10.12, 10.13; auditor-10.13, 10.41, A-16; auditor cross-lens (fixture `grant_7a02` vs `grant_7d01`).
- Decision: a diary read the Support agent role may make but no Active Grant covers (outside its Days, outside its areas, an unknown Grant id) → 403 `GRANT_REQUIRED`, event `grant.read_refused` with `detail.reason` `outside_days` · `outside_areas` · `no_grant`. A read naming another eater's Day, Entry or Unit id under a Grant → 404 `NOT_FOUND`, event `grant.read_refused` with `code: NOT_FOUND`. A read under a Grant that is not Active → 403 `GRANT_NOT_ACTIVE` with the state. Any write by a staff identity carrying a Grant → 403 `FORBIDDEN`, event `grant.write_refused` (one per request), Day revision unchanged. The single out-of-scope fixture is support's E4 `grant_7d01` (`seed.md` §9); auditor fixture Y (`grant_7a02`) is withdrawn.
- Reason: D2 (`GRANT_REQUIRED`, `GRANT_NOT_ACTIVE`, `FORBIDDEN` = role lacks the permission, `NOT_FOUND` never reveals existence); FR-081; one fixture per behaviour (J57).
- Supersedes: auditor-10.41 `/r` and `/s` (read with `grant_7d01`: Day 2026-09-28 "not in Days (2026-09-29 – 2026-09-30)" at 15:40, area "My Units not in Entries and day reports" at 15:41; reads allowed for `grant_7d01` = 1, the 15:35 read); auditor §3 "separate fixture Y"; support-10.12 and 10.13 event name `grant.read_denied` (read `grant.read_refused`), and 10.13 gains "each writes one `grant.write_refused`".

**J8 · Media are refused to every role, Grant or not**
- Lenses: auditor-9.8, 10.9 `/s`; support-10.14; auditor cross-lens (direct media read).
- Decision: every request by a staff identity for an eater's photo, audio or transcript → 403 `FORBIDDEN`, event `access.refused` (`detail.object: analysis_media`), with or without an Active Grant. A Grant request whose areas name media is refused 422 `VALIDATION_ERROR` (field `areas`) — the schema has no such area.
- Reason: §19.2 ("Access to raw evidence … requires explicit consent and restricted roles"); D2 gives no role that permission (J28 covers consented evaluation copies, which are not the eater's media).
- Supersedes: none (support gave no code; this fills it).

**J9 · What the eater's Grant history lists, and in which words**
- Lenses: support K1, K12(a)(g), 10.15; e19 C-14, eater-10.7; auditor M4.
- Decision: the eater's Grant history lists only `grant.read` events with outcome `allowed`, one line each, using "read" ("Mona K. read your Day for 29 Sep · 13:24"). Look-ups, refused attempts and failed reads never appear there; the Auditor sees them all. The reason text is the catalogue's: "An Entry is missing or appears twice" (capital E, the vocabulary word).
- Reason: map §3 ("read within the Grant"); D2 ("reads are allowed only while Active"); vocabulary capitalisation of Entry.
- Supersedes: auditor-10.4 `/r` "An entry is missing or appears twice" (read "An Entry …"); any e19 line with "view", "viewed" or "see your diary" for Grants (support K12(a): eater-10.2, 10.7, 10.8 copy reads "read").

**J10 · The Grant word and the request notice**
- Lenses: support K6, K2; e19 C-13, C-15, eater-10.1.
- Decision: the eater's Settings → Privacy row is **Grants** with the subtitle "Requests from a Support agent to read your diary"; the badge and the single optional notification are as J4.
- Reason: map vocabulary keeps "Grant"; the subtitle carries the meaning (care group 2).
- Supersedes: none.

**J11 · Deletion during a Grant, and requests on a deleting account**
- Lenses: support-10.18 `/s`; e19 eater-9.19.
- Decision: an account deletion request withdraws every Active or Requested Grant on that account in the same transaction (`grant.withdrawn`, `detail.reason: account_deletion`); a new `POST /v1/grants` for that account → 404 `NOT_FOUND`.
- Reason: AT-29 (deletion propagates); D2 Withdrawn (by the eater: the deletion is the eater's act).
- Supersedes: none.

**J12 · A Nutrition approver never reads an eater's Entry**
- Lenses: support K7; approver-10.2.
- Decision: no approver path into a diary. An approver who needs to know which Food version eaters used reads the de-identified counts on the Food (eaters affected, Units using it) or asks a Support agent, who may request a Grant from the eater.
- Reason: FR-081 (separated privileges); FR-080 (de-identified metrics).
- Supersedes: none.

---

## 2 · The Audit trail and the events

**J13 · One name per event**
- Lenses: support §0.2; auditor §3 "The audit event" and M6; admin-10.59, 10.68; approver-10.63; e19 eater-1.2; support cross-lens (auditor names), auditor cross-lens (media read).
- Decision: `way/events.md` is the only list. The reconciled names: `grant.read_refused` (not `grant.read_denied`); no `grant.active` (J1); `access.refused` for every 403 `FORBIDDEN` (not `grant.approve_denied`); `grant.write_refused` for a write under a Grant; `role.change_refused` for a rule refusal on roles (J36); `account.lookup` for every look-up by any role (not `support.lookup`), `account.lookup_rate_limited`, `account.viewed`, `account.jobs_viewed`; `job.retried`, `job.escalated`, `job.escalation_resolved` for any job and any role (not `support.job_retried`, `support.escalated`); `outside_request.recorded|escalated|closed` (not `support.request_logged`); the staff session set `staff.signed_in`, `staff.sign_in_failed`, `staff.sign_in_locked`, `staff.session_ended`; `food.version.*` for Foods and Tier B recipe records (with `kind`), `alias.*`; `registry.stage.changed` for every Registry stage move including Rolled back and Replaced.
- Reason: D2 "one name per thing"; FR-081 (audit trail); the auditor's naming is used where both lenses name the same act, because the Auditor reads the trail (map §2).
- Supersedes: support §0.2 list, and its "returns the panel together with its first tab" (read: the panel opens with no jobs tab; each tab — Privacy jobs, Sync, Failed Analyses, Activity — is one request writing `account.jobs_viewed`); support-9.1, 9.3, 9.9, 9.11, 9.15, 9.18, 10.1, 10.7, 10.9, 10.12, 10.15, 10.17, 10.21, 10.23, 10.25 event names (each read with the `events.md` name); auditor §3 `account.lookup` stays; auditor `staff.signed_in` stays.

**J14 · The event envelope and the outcome words**
- Lenses: auditor §3; support §0.2; auditor A-14.
- Decision: every Audit trail event carries `seq` (gap-free), `occurred_at` (UTC), `received_at` (UTC), `actor` {kind staff|eater|system, id, role at the time}, `action`, `object` {type, id, version}, `account_id` (when an eater is involved), `outcome` ∈ **allowed · refused · done · failed · not_found**, `code` (the D2 code when refused or failed), `detail` (allow-listed keys only), `request_id`, `surface` ∈ app · console · api · job, `app_version` (app surface), `prev_hash`, `hash` (`hash(n) = SHA-256(hash(n−1) ‖ canonical(event n))`). Screen words: Allowed · Refused · Done · Failed · Not found. Support's `found` is `done`; `denied` is `refused`; `error` is `failed`. Never carried: food names, quantities, calories, photos, audio, transcripts, profile values, eater names or emails.
- Reason: §19.2; auditor O1/N1 AU-3 findings; D2 one name per thing.
- Supersedes: support §0.2 outcome list; support-10.25 `/r` "`grant.read` with outcome `error`" (read `failed`).

**J15 · Who reads the Audit trail**
- Lenses: auditor-10.17; admin-10.58, 10.68; approver-10.2; support-9.16.
- Decision: the Auditor reads the whole trail. The Platform admin reads only its change slice — Registry, quotas, prices, spend cap, Roles, Jobs, Grant settings and launch gates — and never Grant, Consent, look-up or account events (`kind=grant` → 403). The Nutrition approver and the Support agent read none. A refused open writes `access.refused`.
- Reason: FR-081 (separation); admin permission "Read the Audit trail for Registry, Roles and Jobs"; N1 AU-9(4) (the record of who looked is not browsed).
- Supersedes: auditor-10.17 title and `/r` "only the Auditor role reads the Audit trail" (read: only the Auditor reads the whole trail; the Platform admin reads its change slice; a Support agent is refused as written).

**J16 · Views inside the Audit trail, and "Find account"**
- Lenses: auditor §3 Places, A-14, 10.34; governor note 6.
- Decision: the Audit trail section has the views **Events · Anomalies · Consents · Summary · Records of processing · Exports**, and the action **Find account** (needs a reason; writes `account.lookup` with `role: Auditor`). Added to D4 as views of the existing place, not new sections.
- Reason: D2 Places keep "Audit trail" as the one section; the views are the auditor's tasks (A-8, A-14).
- Supersedes: none.

**J17 · The Auditor's one write is a review note**
- Lenses: auditor A-3; admin-10.62 ("Auditor stays read-only").
- Decision: the Auditor may append a **review note** (`audit_trail.review_noted`: what was reviewed, the period, the finding, free text ≤ 500 characters, no eater identifiers). It edits nothing and counts as read-only for separation of duties (admin-10.62's rule still refuses any changing role for an Auditor).
- Reason: K2 Art. 32(3)(b) and E1 Art. 9(1) ask the DPO to document review results (auditor A-3); the trail is append-only either way. **(product choice — the owner can revisit it)**
- Supersedes: none.

**J18 · Erasure, and how long events are kept**
- Lenses: auditor A-4, 9.13; e19 eater-9.19.
- Decision: Audit trail events about a deleted account keep the account id; deletion destroys the sign-in identity and profile, so the id leads nowhere. Audit trail events are kept **5 years** from `occurred_at`, then removed by a retention job that writes one `audit_trail.retention_run` summary event (the chain re-anchors at the first kept event, recorded in that summary). Deletion completion records (no identifiers) are kept 5 years.
- Reason: GDPR Art. 17(3)(b)(e) and K1 Art. 18(1) (auditor A-4); K2 Art. 33(1)'s five years for records of processing is the nearest published period. **(product choice — counsel can revisit the period)**
- Supersedes: none.

**J19 · Breach records, notice timing, regulator exports and infrastructure reads**
- Lenses: auditor A-2, A-6, A-12, A-13.
- Decision: the breach record and its notices live outside the product in v1 (the incident runbook); the Auditor's export (auditor-10.35) feeds it. Exports use stable machine keys with an optional Arabic label row. "No diary read outside a Grant" is a promise about the product's API; reads below it (Firestore, Cloud Storage) are covered only when a hosting delta enables and correlates Data Access logs.
- Reason: the ship rows are dropped (blueprint §0 line 10); the map has no breach record; auditor C1 (Data Access logs off by default). **(product choice — the owner can revisit it when a hosting target exists)**
- Supersedes: none.

**J20 · Records of processing**
- Lenses: auditor A-8, 9.16.
- Decision: the view is derived from live, versioned configuration (Consent purposes and wordings, Policy retention, Registry processors, the residency fact). Fields the product does not hold — controller contact, DPO details, security measures — read "Not recorded in the product" until a dated delta adds them.
- Reason: research Implications "Governance work before launch … Records of processing"; nothing invented.
- Supersedes: none.

**J21 · The first platform admin**
- Lenses: admin A7.16, 10.59; admin cross-lens (first-admin event); auditor event 1.
- Decision: the deployment's environment names the first platform admin; on that account's first sign-in the server writes `role.assigned` with `actor: system:deployment` and `detail: "First platform admin set at deployment"`. It is seeded event 1 (`seed.md` §11).
- Reason: admin-10.59 (`assumption`, kept); the auditor's anomaly rule "roles held with no assignment event" needs an event for the first holder.
- Supersedes: auditor event 1 actor "system bootstrap" (read `system:deployment`).

**J22 · An Entry's history is not the Audit trail**
- Lenses: e36 W3.14.
- Decision: the per-Entry ledger record the eater reads (FR-041, FR-046) is the Entry's **history**; "Audit trail" names only the staff and compliance record.
- Reason: D2 ("'Audit trail' is the only name for the record of who did what").
- Supersedes: none.

---

## 3 · Consent

**J23 · Purposes, keys, labels and text versions**
- Lenses: e19 1.3–1.6, 9.1–9.6, C-21, C-26, cross-lens (Health write name, Photos name); e24 4.3–4.6, W2.18; e36 3.40, W3.4; auditor 9.1–9.3, M11; support 9.6.
- Decision: one Consent per purpose:

  | key | screen label (English) | text version in force 2026-10-01 |
  |---|---|---|
  | `diary_processing` | Diary processing | `diary-1` |
  | `ai_processing` | Send photos, voice and text to Google's AI (Gemini) | `c-ai-4` (from 2026-09-24; `c-ai-3` before) |
  | `health_read_workouts` | Health: read workouts | `health-1` |
  | `health_read_active_energy` | Health: read active energy | `health-1` |
  | `health_read_body_mass` | Health: read body mass | `health-1` |
  | `health_write_food` | Health: write food (energy, protein, carbohydrate and fat) | `health-1` |
  | `microphone` | Microphone | `mic-1` |
  | `photos` | Photos (camera and photo library) | `photos-1` |
  | `research` | Optional research | `research-1` |
  | `label_review` | Send my label photos for review | `label-1` |

  The age confirmation is a separate record (`age-1`), not a Consent. `c-ai-5` is reserved for counsel's residency wording (e19 C-26).
- Reason: FR-076 (separate purposes); map §3 row 1; R22; the food correlation carries four values (P29), so the write purpose names all four (e36 W3.4); Microphone and Photos need their own texts (e19 C-21).
- Supersedes: e36 §"How to read" and 3.39/3.40 "Health: write dietary energy" (read "Health: write food"); e36 "Write meals to Apple Health"; support §0.3 E1 and support-9.6 label "Send photos, voice and text to Google's AI" (read the full label with "(Gemini)"); auditor-9.1 rows Microphone and Photos "— · No records yet" (read `mic-1`, `photos-1`, and J25's wording).

**J24 · How a Consent was given: one method list**
- Lenses: e19 1.5, 1.6; e24 4.3–4.5, W2.18, cross-lens (method wording); auditor 9.2.
- Decision: `method` ∈ `onboarding_age_question` · `onboarding_switch` · `onboarding_choice` (the "Keep it in my account" choice that gives Diary processing) · `first_need_sheet` · `settings_privacy`, with `context` naming the screen (Capture & Plan, Activity sheet, Unit editor …). Screen words: "Onboarding age question", "In-app switch · Onboarding", "Onboarding choice", "In-app sheet at first need", "Settings → Privacy".
- Reason: R22 ("documented through means allowing future verification"); one word per thing.
- Supersedes: e19 eater-1.5 `/s` "in-app switch · onboarding" (read `onboarding_switch`); e19 eater-1.6 `/r` and e24 4.3 `/s` "in-app sheet · first use" (read "In-app sheet at first need").

**J25 · "Not given" is a derived state with no stored record**
- Lenses: e19 cross-lens note on 9.1, cross-lens (auditor "No records yet"); auditor-9.1; D3.
- Decision: a Consent record is stored only when the eater decides (Given or Withdrawn). Before that the purpose is in the state **Not given**, derived from "no record". Settings → Privacy shows "Not given"; the auditor's Consents view shows "Given 0 · Withdrawn 0 · Not given by anyone yet" for a purpose with no records.
- Reason: D3; a record of a non-decision would carry no version, time or method (R22).
- Supersedes: auditor-9.1 `/r` "0 ('No records yet')" (read "Not given by anyone yet").

**J26 · Who writes consent and request wording, who owns retention, and the privacy sign-off**
- Lenses: auditor A-1, A-7; approver P7.2, 10.56, 10.62; e19 C-26.
- Decision: no sixth role in v1. Consent texts and the Grant request wording are versioned **Wording** records (key, version, English, Arabic, published_at), published by the Platform admin only after the owner's or counsel's approval is recorded on **Settings › launch gates** ("Privacy review signed by <name> on <date>"); each publish writes `wording.published`. Retention values stay in the approver's Policy (map §1 ¶6) inside the published privacy-policy bounds: the approver may shorten, never lengthen (approver-10.56 as written). The FR-082 privacy review is a launch gate, like the nutrition-policy review.
- Reason: D2 has five roles; FRD §23.2 names reviewers, not a product role; map §1 ¶6 puts retention in Policy. **(product choice — the owner can add a Privacy reviewer role by a dated delta)**
- Supersedes: none.

**J27 · Shadow copies and the AI Consent**
- Lenses: admin A7.5, 10.18–10.21; research R6.5.
- Decision: `c-ai-4` (published 2026-09-24) says that a request may also be sent, as an unkept copy, to a newer version of the reader for checking. Shadow therefore sends copies only for eaters whose AI Consent was given under `c-ai-4` or later; the Shadow copy's input and output are not stored beyond the comparison scores. Regression-set cases (stored inputs) need the `research` Consent (J28).
- Reason: FR-076, FR-079 (no training use without opt-in); R22 (purpose stated); admin-10.21 (Shadow never widens what is sent). **(product choice — counsel can revisit the wording)**
- Supersedes: none; admin-10.21 gains the rule "eaters under `c-ai-3` are not in the Shadow sample".

**J28 · Raw evidence, and consented evaluation cases**
- Lenses: admin A7.11, 10.14; auditor A-11, 9.8, 10.14; e19 C-25, eater-9.8; research R6.5.
- Decision: two different things. (a) An eater's Analysis media (photos, audio, transcripts) can be opened by no staff role, ever (J8). (b) A **regression case** is a copy taken into the evaluation store only from an eater with the `research` Consent Given, under a random case number; a custom role holding "View consented evaluation cases" (no seeded role holds it) may open the copy, writing `evaluation_case.viewed`. Withdrawing `research` deletes the copy (`regression_case.removed`). The auditor's anomaly "raw evidence opened by staff" counts (a); a new anomaly "evaluation cases viewed without a research Consent" counts (b) and must stay 0.
- Reason: §19.2 (explicit consent and restricted roles); FR-079; AT-29.
- Supersedes: e19 research wording "visible only to staff with a special permission" applies to (b) only.

**J29 · Label submissions have their own Consent; their photos outlive the raw-scan window**
- Lenses: approver P7.4, 10.15, 10.66; e24 W2.8, 4.37.
- Decision: a Label submission needs the `label_review` Consent (text `label-1`). Its photos are cropped to the label, EXIF-stripped and kept while the submission is Proposed or In review, then: Rejected → deleted 30 days after the decision; Approved → the cropped label image becomes the Food's Evidence (it shows no person) and the uncropped original is deleted. Withdrawing `label_review` deletes the photos of submissions still Proposed or In review and rejects them (`detail: consent_withdrawn`).
- Reason: R22 (one consent per purpose); FR-026 (evidence on every record); FR-077, FR-078 (crop, strip, retention). **(product choice — counsel can revisit it)**
- Supersedes: none.

**J30 · Withdrawing Diary processing asks once**
- Lenses: e19 C-9, eater-9.7.
- Decision: every Consent withdraws in one tap except Diary processing, which shows one confirmation because withdrawing it deletes the account's server copy of the diary (it becomes a deletion Privacy job with the export offered first).
- Reason: care group 4 ("warn only before loss that is both unexpected and permanent"); R22's one-tap withdrawal holds for every purpose whose withdrawal loses nothing.
- Supersedes: none.

**J31 · Health: the app Consent is not the iOS permission; withdrawal keeps or deletes, by the eater's choice**
- Lenses: e19 C-11, eater-1.6, 9.4; e36 3.40, W3.4, cross-lens (one Health Consent rule; Open Health settings target; copy differences); e578 W5.8, 7.24; auditor-9.7.
- Decision: the app records its own Consent per Health purpose; iOS read permission is never shown as granted or denied (it is not observable, P30). Withdrawing a Health read purpose stops new imports at once and asks one question: "Keep what was imported" (default) or "Delete imported <workouts|active energy|weights>"; deleting removes those records and re-projects the affected Days. Withdrawing `health_write_food` asks "Keep the foods already in Apple Health" or "Remove them". The button that sends the eater to iOS is labelled **"Open Health access"** and opens the iOS Settings page of Sips & Bytes, with one line saying where Health access sits. Copy: a read with no data → "No data from Apple Health yet"; a refused write → "Apple Health access is off — Open Health access".
- Reason: FR-067 ("unknown, not proof of no exercise"); FR-076 (refusal preserves unaffected functions); AT-29 names media, queues, cached analysis and exports, not confirmed records; P29–P30; the only stable deep link iOS offers is the app's Settings page (`assumption`, as the lenses recorded).
- Supersedes: e578 "Check Health access" (read "No data from Apple Health yet" or "Open Health access" as the case is); e36 "Open Health settings" (read "Open Health access"); e578 "a button that opens the Health settings" (same).

**J32 · The AI Consent's residency sentence, and the age gate**
- Lenses: e19 C-18, C-26, eater-1.2; auditor M10, 9.9.
- Decision: the age gate sends the age alone to `POST /v1/age-gate` (no account, device or identifier); under 18 → 422 `AGE_REQUIREMENT` and one `age.refused` event with no identifier; nothing is stored on the iPhone. Whether a self-declared age is enough is counsel's; the product keeps the gate as built. `c-ai-4` states "processed by Google outside Egypt and Saudi Arabia"; counsel's change becomes `c-ai-5`.
- Reason: R16, R13; blueprint §1.7 (residency open); the two lenses already agree after their fixes.
- Supersedes: auditor §7 M10 (closed: e19 now sends the call).

---

## 4 · Error codes

**J33 · A staff identity on an eater endpoint gets `FORBIDDEN`**
- Lenses: auditor M9, event 77, 10.9; admin-10.63 `/r` 2nd line, admin-10.51 (an Analysis read by `admin.a`).
- Decision: the role check runs before any lookup. A staff token on any eater-scoped path (`/v1/reports/*`, `/v1/consumption*`, `/v1/analyses*`, `/v1/units*`, …) → 403 `FORBIDDEN` and `access.refused`, the same for every id, so existence is never revealed. `NOT_FOUND` stays for an eater token naming another eater's id.
- Reason: D2 (`FORBIDDEN` = role lacks the permission; `NOT_FOUND` = another user's ids); NFR-07.
- Supersedes: admin-10.63 `/r` "returns 404 `NOT_FOUND`" (read 403 `FORBIDDEN`); admin line in 10.51 "`GET /v1/analyses/{id}` … for `eater-synth-050`'s Analysis … 404 `NOT_FOUND`" (read 403 `FORBIDDEN`).

**J34 · An id inside a command body that the caller cannot use → `UNIT_NOT_FOUND`**
- Lenses: e36 W3.18, 3.8; e24 4.23.
- Decision: a `unit_version_id`, Food id or Recipe id in a request body that does not exist **or** belongs to another eater → 422 `UNIT_NOT_FOUND`, the same answer for both. A resource id in the path (`GET /v1/units/{id}`) that is absent or foreign → 404 `NOT_FOUND`.
- Reason: FRD §18.2 keeps `UNIT_NOT_FOUND`; D2 forbids revealing existence, which one code for both cases achieves.
- Supersedes: e36 eater-3.8 "answers both a foreign and a random unit_version_id with `NOT_FOUND`" (read `UNIT_NOT_FOUND`).

**J35 · Conflicts with no code of their own: `VALIDATION_ERROR` with HTTP 409 and a `reason`**
- Lenses: support K11, 9.9, 10.4; admin-9.2.
- Decision: a second request while one is Requested (`reason: request_waiting`), a second retry of the same job (`reason: already_retried`), a Grant answer after it closed (`GRANT_NOT_ACTIVE` with the state, as written) — each returns the current state in the body. No new codes.
- Reason: D2 lists no conflict code besides `STALE_REVISION`, which is for revisions (FRD §18.2 "A 409 conflict returns the current revision").
- Supersedes: none.

**J36 · A rule refusal on roles is `VALIDATION_ERROR`, recorded as `role.change_refused`**
- Lenses: admin-10.61, 10.62, 10.64; auditor event 35, A-15.
- Decision: refusing a self-change, the last Platform admin's removal, a separated-duties combination, a staff role for an eater account or a change to a seeded role → 422 `VALIDATION_ERROR` and the event `role.change_refused` (`detail.rule`). `FORBIDDEN` stays for a caller without "Change roles".
- Reason: D2 (`FORBIDDEN` only for a missing permission); admin-10.64 already says so.
- Supersedes: auditor event 35 "access.refused · role.assign Auditor → staff_ali (self-change) · Refused · FORBIDDEN" (read `role.change_refused` · VALIDATION_ERROR; `seed.md` §11 event 123).

**J37 · A dependency other than the AI is down: `SERVICE_UNAVAILABLE`**
- Lenses: support-9.9, 9.11, 9.14, 9.15, 10.15, 10.22, 10.25 (fault-injected 503s); approver-10.23; admin-10.1.
- Decision: add `SERVICE_UNAVAILABLE` (HTTP 503) for a failure of storage, the database, the Audit trail write or another non-AI dependency: nothing was done, the request may be retried with the same id. `AI_UNAVAILABLE` stays for the model provider and the kill switch. A Grant read whose Audit trail write fails returns 503 `SERVICE_UNAVAILABLE` and no data.
- Reason: D2 has no code for these 503s, and `AI_UNAVAILABLE` would misname them; FRD §18.2 ("A provider timeout never produces a fabricated success"). Added to delta D4.
- Supersedes: each fault-injection line that names "503" without a code now carries `SERVICE_UNAVAILABLE`.

**J38 · HTTP status per code, and where `PLAN_INFEASIBLE` is used**
- Lenses: e578 5.21, 5.25, 5.28; all lenses' error lines.
- Decision: `UNAUTHENTICATED` 401 · `FORBIDDEN` 403 · `GRANT_REQUIRED` 403 · `GRANT_NOT_ACTIVE` 403 (409 on an answer after the request closed) · `CONSENT_REQUIRED` 403 · `NOT_FOUND` 404 · `STALE_REVISION` 409 · `VALIDATION_ERROR` 422 (409 with `reason` as J35) · `UNIT_NOT_FOUND`, `UNIT_AMBIGUOUS`, `SOURCE_BASIS_UNKNOWN`, `MASS_BALANCE_ERROR`, `MACROS_INCOMPLETE`, `AGE_REQUIREMENT`, `POLICY_FLOOR`, `PLAN_INFEASIBLE` 422 · `RATE_LIMITED` 429 (with `resets_at`, the eater's local time) · `AI_UNAVAILABLE` 503 · `SERVICE_UNAVAILABLE` 503. `POST /v1/meal-plans` answers 200 with `state: infeasible` and `blocking[]` (an answer, not an error); `PLAN_INFEASIBLE` is returned only when a command tries to save or confirm an Infeasible Plan.
- Reason: FRD §18 ("Returns feasible/infeasible status"); §18.2.
- Supersedes: none.

**J39 · Codes that appear only in verdict history**
- Lenses: auditor M14; support and auditor verdict records.
- Decision: `AGE_GATE_REQUIRED`, `FORBIDDEN_ROLE`, `ROLE_FORBIDDEN`, `GRANT_DECLINED`, `GRANT_REVOKED`, `GRANT_EXPIRED`, `GRANT_NOT_APPROVED`, `GRANT_READ_ONLY`, `GRANT_OUT_OF_SCOPE`, `LOOKUP_REQUIRED` and the state `expired_unanswered` exist only in dated verdict records; no story body uses them. Contracts use D2 codes only.
- Reason: D2.
- Supersedes: nothing live.

---

## 5 · Roles and staff duties

**J40 · The permission list and the seeded roles**
- Lenses: admin-10.58–10.60; approver-10.2; support-9.16; auditor-10.16, 10.17.
- Decision: the fixed permissions are admin-10.58's list plus "Read reference (Foods, Recipes, Aliases, Policy)", "Read Grants (all)", "Change Grant settings", "Escalate jobs", "Record requests received outside the app", "Sign launch gates", "Publish wording" (held by the Platform admin for consent texts and `grant-req-1`, and by the Nutrition approver for the tracking-only guidance `guidance-1`), "Add a review note" and "Read account state". Seeded roles (read-only, `seed.md` §3): **Eater** (no console permission), **Nutrition approver**, **Support agent**, **Platform admin**, **Auditor**. "Read a diary inside an Active Grant" is not a permission; it exists only through a Grant.
- Reason: blueprint §0 line 3 (permissions → roles → users, deny by default); FR-080, FR-081.
- Supersedes: none.

**J41 · Duties that must stay apart**
- Lenses: support K5; admin-10.62; auditor-10.26, A-15.
- Decision: one staff account may not hold Support agent together with Platform admin, Nutrition approver or Auditor; an Auditor may not hold any role with a changing permission; a custom role may not join "Request a Grant" with any Registry, Roles or Policy write. The save is refused (J36) — the preventive rule — and the Anomalies rule "Support agent held with Platform admin or Nutrition approver" plus "roles held with no assignment event" stay as the detective control (seed: `staff_sod_seed`).
- Reason: FR-081; R24 ("separated duties"); SR7, SR8 AC-5.
- Supersedes: none.

**J42 · Who retries, who escalates, who sees a job with its account**
- Lenses: support K4, A9, 9.9–9.11; admin A7.3, 9.1–9.4, 10.55–10.57; approver P7.13, 10.23.
- Decision: the Platform admin sees jobs de-identified on **Jobs** and retries any job (privacy, Analysis, import, retention, USDA release import) within its attempt budget (J142). The Support agent sees one account's jobs only after a look-up, retries a Failed **export** once under the same id, and escalates deletion and Consent-withdrawal stages to the Platform admin ("Escalated" filter on Jobs). The USDA release import is a Platform-admin job; the approver reads its outcome on **Foods**.
- Reason: FR-080 ("failed jobs"); map §2 (support sees "failed jobs and account state"); §18.2 (bounded retries, same id).
- Supersedes: none.

**J43 · An eater who cannot sign in**
- Lenses: support K9, 9.14; auditor A-9 (identity).
- Decision: the Support agent records the request (Requests received outside the app) and points the eater to the sign-in recovery path. If recovery fails, the agent escalates; the Platform admin sends a one-time verification link to the account's own sign-in email (for Sign in with Apple, the relay address). When the eater opens it, the Platform admin may start the export or deletion Privacy job for that account; the job records `requested_by: staff`, `channel: outside_app`, `verified_by: email_link`. No identity document is ever asked for.
- Reason: R4 and FR-078 (in-app deletion; ≤30 days); SR10 ¶74 (no ID copy unless necessary); SR9 Art. 12(6). **(product choice — counsel can revisit it)**
- Supersedes: support-9.14's "support still has no export or delete control" stays true (the Platform admin acts).

**J44 · The Nutrition approver on Metrics**
- Lenses: admin A7.10, 10.49, 10.50; approver-10.19.
- Decision: the approver reads acceptance by Evidence type with per-Food breakdowns, under the same small-group rule (fewer than 11 distinct eaters hidden).
- Reason: FR-080 (de-identified quality metrics); admin A26.
- Supersedes: none.

**J45 · "Due soon" is 7 days for deletions and outside requests**
- Lenses: admin §3 ("Deletion priority ≤7 days"), 9.1; support A23, 9.15, 9.19.
- Decision: one value: a deletion or a request received outside the app is "due soon" (pinned, sorted first) with 7 days or fewer before its due date.
- Reason: one name, one value; 7 days covers a working week plus a weekend before the 30-day deadline (NFR-13).
- Supersedes: support A23 "5 days or fewer" and support-9.15 "3 Open rows due within 5 days … sort first" (read 7 days).

---

## 6 · Places and screens

**J46 · The eater app's places**
- Lenses: e19 C-19 and §"Onboarding screens"; e24 W2.10, cross-lens (onboarding places); e36 W3.15; e578 W5.22, W5.23, §0.1; support §13.
- Decision: added by delta D4, one name each: the eleven screens **Onboarding · Age, · Under 18, · Consents, · Account, · Profile, · Safety screen, · Energy, · Target, · Macros, · Activity mode, · Review**; the **account line** at the top of Settings; **Settings → Privacy → Grants · Support code · Delete account**; on Today: the **Day picker**, the **timeline**, **Entry details**, the **Day report** (opened from the remaining figure), the **Activity sheet** (opened from Today's Activity row); the **quick-add** control; the **count stepper**; the **correction preview**; **Source details**; **My Units → Templates**; **Progress → Weight · Target history**; the Capture & Plan camera **modes Meal · Unit · Label · Recipe**. Settings placements: diary-day boundary, "Ramadan days" and "First day of week" in Units & language; One-tap logging in Food rules; Hide numbers in Goals; "Show kcal remaining on widgets" in Privacy.
- Reason: FRD §2.1, §2.6, §14, FR-046; D2 ("a word not here … is added by a dated delta first").
- Supersedes: every "*(proposed)*" mark on these names in the eater files.

**J47 · The console's places**
- Lenses: support §13; admin §3, A7.1, 10.15, 10.16; approver §6; auditor §3 Places; governor notes 4 and 6.
- Decision: inside D2's sections, these panels and views are named (D4): **Registry › <task>** (with the models list, prompt editor, regression set, quotas panel, kill switch); **Metrics › cost view · prices panel**; **Jobs › Look up an account · account panel · Privacy help · Privacy jobs · Failed Analyses · Sync · Activity · Requests received outside the app · Escalated · Retention**; **Grants › Grant form · Grant panel · Grant bar · Diary (read-only) · Grants list**; **Review › flags · Label submissions**; **Audit trail › Events · Anomalies · Consents · Summary · Records of processing · Exports**; **Settings › launch gates · Grant settings · language**. Admin-10.15 and 10.16 happen on **Registry › Meal**.
- Reason: D2 Places; one name per thing.
- Supersedes: admin-10.15 "When the page loads"/10.16 "When the report opens" (read "on Registry › Meal"); support §0.2 "Analyses" (read "Failed Analyses").

**J48 · The Support agent's failed-job views stay under the workflows they report**
- Lenses: support K8 and verdict defect 20; support-3.1–3.2, 4.1–4.3, 7.1, 10.24.
- Decision: the views are filed under the workflow whose jobs they report — Sync under WF-3, Analyses under WF-4, Activity under WF-7, the kill switch under WF-10 — and the map gains no new step; the support story ids stay as they are.
- Reason: map §4 workflows; FR-080 puts failed jobs in the console without a workflow of their own.
- Supersedes: none.

---

## 7 · API paths and field names

**J49 · Path families**
- Lenses: every lens's proposed interfaces (admin §3; approver §6; support §0; auditor §3; e19 "Proposed interfaces"; e24 §6; e36 "API"; e578 §6).
- Decision: FRD §18 paths as written. Eater paths under `/v1/` (`/v1/me/…` for the caller's own settings, Consents and Grants); staff paths under `/v1/admin/…` for console resources and `/v1/support/…` for the allow-listed account projections; Grants under `/v1/grants` for both sides (authorised by role). The adopted proposals: `POST /v1/age-gate`; `POST /v1/targets/proposals`, `POST /v1/targets`, `GET /v1/targets` (versions, newest first), `GET /v1/targets/current`, `GET /v1/targets/suggestions`, `POST /v1/targets/suggestions/{id}/accept`; `PUT /v1/me/safety-mode`; `PATCH /v1/me/settings`; `POST|GET /v1/me/consents`; `GET /v1/me/grants`, `GET /v1/me/grants/{id}/reads`; `POST /v1/grants`, `POST /v1/grants/{id}/approve|decline|withdraw|end`, `GET /v1/grants?mine=true`, Grant reads `GET /v1/grants/{id}/days/{diary_day_id}`, `/entries/{entry_id}`, `/units`, `/templates`, `/activity?diary_day_id=`; `GET /v1/privacy/jobs`, `GET /v1/privacy/jobs/{id}`; `GET /v1/units`, `GET /v1/units/{id}`, `GET /v1/units/{id}/picture`, `POST /v1/units/{id}/archive|unarchive`; `GET /v1/analyses/{id}`, `GET /v1/analyses?state=`; `GET /v1/rules`; `POST /v1/consumption/{id}/restore`, `GET /v1/consumption/{id}/history`; `/v1/templates`; `POST /v1/days`, `PUT /v1/days/{diary_day_id}/mark`; `GET /v1/meal-plans/{id}`, `GET /v1/meal-plans?state=`, `POST /v1/meal-plans/{id}/save|validate|not-eaten`; `POST /v1/activity`, `GET /v1/activity?diary_day_id=`, `POST /v1/activity/{id}/corrections|void|restore|link`; `POST /v1/weights`; `POST /v1/label-submissions`; `GET /v1/reference/attributions`. Staff: `GET /v1/admin/registry`, `POST /v1/admin/registry/{task}/versions`, `PUT /v1/admin/quotas`, `GET /v1/admin/quotas/usage`, `GET /v1/admin/prices`, `GET /v1/admin/metrics/cost|evidence`, `POST /v1/admin/jobs/{id}/retry|escalate`, `/v1/admin/roles`, `/v1/admin/users/{id}/roles`, `/v1/admin/policy/versions[/{v}/approve]`, `/v1/admin/foods…`, `/v1/admin/recipes…`, `/v1/admin/aliases`, `/v1/admin/flags`, `/v1/admin/usda-releases`, `/v1/admin/grants[/{id}]`, `/v1/admin/audit-trail/events[/{seq}]`, `/v1/admin/audit-trail/verify|anomalies|consents|exports`, `/v1/admin/launch-gates`, `/v1/admin/grant-settings`; `GET /v1/support/accounts/{id}`, `/v1/support/accounts/{id}/{tab}`, `/v1/support/accounts/{id}/privacy-jobs`, `POST /v1/support/lookups`, `/v1/support/outside-requests`; and the paths `events.md` names for each audited act (`POST|DELETE /v1/admin/session`, `POST /v1/admin/audit-trail/lookups|review-notes`, `POST /v1/admin/wording`, `POST /v1/admin/launch-gates/{gate}/sign`, `POST /v1/admin/flags/{id}/close`, `POST /v1/admin/registry/{task}/versions/{v}/move|rollback`, `PUT /v1/admin/registry/{task}/kill-switch`, `POST /v1/admin/quotas/rollback`, `POST /v1/admin/prices`, `PUT /v1/admin/spend-cap`, `GET /v1/admin/regression-set/cases/{n}`, `POST /v1/admin/outside-requests/{id}/act`, `POST /v1/me/support-code`, `GET /v1/privacy/jobs/{id}/file`, `PUT /v1/rules`, `PATCH /v1/weights/{id}`).
- Reason: one path per thing; FRD §18 style.
- Supersedes: auditor `/v1/admin/audit/events…` (read `/v1/admin/audit-trail/events…`); approver-10.2 `POST /v1/admin/registry/versions` (read `/v1/admin/registry/{task}/versions`); support `POST /v1/support/privacy-jobs/{id}/retry` and `/file` (read `/v1/admin/jobs/{id}/retry`; the file path stays refused); admin-9.2 "`GET /v1/privacy/export-or-delete` status" (read `GET /v1/privacy/jobs`); e24 `GET /v1/analyses?status=` (read `?state=`); support `/v1/audit/…` (read `/v1/admin/audit-trail/…`).

**J50 · Field naming**
- Lenses: all API lines.
- Decision: snake_case; energy and mass carry their unit (`_kcal`, `_g`, `_ml`, `_kg`); instants are ISO 8601 UTC with `Z` and end in `_at`; local wall times end in `_local`; a Day is `diary_day_id` = the Day's local date `YYYY-MM-DD`; every lifecycle field is `state` (Entry, Analysis, Plan, Unit, Grant, Privacy job, Registry version, Policy version, Food); solver result is `solution_status` and validator result `validation_status` (FRD §17); ids carry a type prefix (`seed.md` §1). Plan confirmation: `confirmed_as` ∈ `ate_as_planned` · `changed`. Target source: `source` ∈ `estimated` · `entered` · `clinician` · `suggestion`. Carbohydrate convention: `carbohydrate_basis` ∈ `total` · `available`. Report fields adopted from e578 §6 (`activity_coverage {source, last_sync_at, state: data | no_data | not_connected}`, `food_minus_active_kcal`, `estimated_total_expenditure_kcal`, `macro_coverage_kcal`, `macros_complete`, `provisional`, `days_logged`, `days_unlogged`, `fiber_g`, `sugars_g`, `net_carbohydrate_g`, `carbohydrate_assessment`, `carbohydrate_label`), plus `active_energy_kcal` and the Activity fields `provider_record_id`, `origin`, `energy_basis` (`active` · `gross`), `import_revision`, `override` (FR-063).
- Reason: FRD §17 field words; e578 cross-lens (§6 bookkeeping).
- Supersedes: e578 §6 `carbohydrate_basis` values `includes_fiber` · `excludes_fiber` (read `total` · `available`); e19 eater-1.32 `typed_by_eater` (read `entered`).

**J51 · Test-only endpoints**
- Lenses: admin final-3 observation (test clock); auditor test clock; support A21 (fault injection); e578 5.24 (solver time limit).
- Decision: test builds only (absent in any other build, refused 404 there): `PUT /v1/test/clock` {`now`}, `POST /v1/test/seed` {`at`} (J52), `PUT /v1/test/faults` {`target`, `mode`: slow|fail, `ms`}, `PUT /v1/test/planner` {`time_limit_ms`}, and the adapters' mock controls (Gemini, USDA, HealthKit on the simulator, Sign in with Apple).
- Reason: the Givens must be reachable and repeatable (lens brief: observable acceptance).
- Supersedes: none.

---

## 8 · Fixtures, the clock and ids

**J52 · One seed, loaded once at the story's start clock**
- Lenses: e24 W2.15 and "One seed"; e578 cross-lens (Unit seed values); auditor §7 rule; admin final-3 observation; approver "seeded synthetic dataset".
- Decision: `way/seed.md` is the only fixture set. Every seed record has a time. A story names its **start clock**; the loader writes every record whose time is at or before that clock, then the story runs. Moving the clock forward inside a story never loads further seed records. A story's Given may add records only through the public API (or the test endpoints of J51), never contradict a loaded seed record. A lens's default start clock is in `seed.md` §2, with the stories that start elsewhere.
- Reason: one fixture set (`way/lessons.md`); the lenses' timelines differ by design (an admin walks Meal version 7 through Canary before the auditor reads it rolled back), and load-at-clock lets each hold.
- Supersedes: every lens fixture table, read through `seed.md`'s map from lens names to seed records. FRD fixtures that a story types rather than loads (AT-01, AT-03, AT-09) are listed with their exact values in `seed.md` §5.3, and AT-03's three Foods are seed rows (§8.1, §8.2).

**J53 · Ids and fixture names**
- Lenses: auditor M14; admin §3 ("Synthetic staff accounts", "Synthetic eaters"); e24 seed; e19 fixture names; support §0.3.
- Decision: eater accounts are `acct_` + 6 lowercase hex; anonymous sessions `acct_anon_` + 4 hex; staff `staff_<name>`. Lens names stay usable in story text as fixture names mapped in `seed.md` §14: `eater-synth-mona` → `acct_e9a001` and the other named eaters; `eater-synth-NNN` → `acct_5e0NNN`; `anon-synth-NNN` → `acct_anon_0NNN`; support's E1–E14 and the eater lens's SE1–SE13 → their `acct_` ids; `admin.a` → `staff_ali`, `admin.b` → `staff_badr`, `approver.a` and "approver A" → `staff_dina`, "approver B" → `staff_yara`, `support.a` → `staff_mona`, `auditor.a` → `staff_hana`, `access.a` → `staff_rana`, `new.user@example.test` → `staff_new`. Every other id prefix is in `seed.md` §1.
- Reason: one name per thing; no story id needs rewriting.
- Supersedes: approver-10.62 "Mona Adel (a synthetic staff name)" (read `staff_dina` "Dina R.").

**J54 · One person per name: the named eaters are unified**
- Lenses: e19 O1–O5 and T1; e24 eaters; e36 §2.1; e578 §0.2; e24 cross-lens (Faisal's boundary); approver-10.69 (Sam's figures).
- Decision: one record per person (`seed.md` §5): **Mona** (Cairo, Arabic EG, Arabic-Indic, boundary 03:00; Target 1,870 from 2026-08-20 "Estimated by the app" then 1,750 from 2026-09-15 "Entered by you"); **Faisal** (Riyadh, Arabic Gulf, Western digits, boundary 03:00, "Ramadan days" off until Ramadan 1448; e19's O3 profile: 41 y, 176 cm, 80 kg, "+5", GLP-1 "Yes" → protein-first, Target 2,040); **Sam** (London, English, boundary 00:00; e19's O2 inputs; Target 1,870 "Entered by you" from 2026-09-20; macros 25/30/45); **Huda** (Cairo, English, account, no Target yet: e19's O4 inputs are her onboarding inputs); **Hala** (trial T1, no account; e19's O1 inputs); **Amal** (O5: inputs only, fresh install); **Nadia**, **Khalid** (fresh accounts).
- Reason: e24 and e36 seeded the same three people under the same names; one person cannot hold two Targets on one Day (FR-071).
- Supersedes: e36 §2.1 Mona Target "1,870 kcal" (read 1,750 on 30 Sep) and therefore e36 eater-6.3 `/r` "772 → 712 remaining" (read "652 → 592 remaining") and §2.3 "772 kcal remaining" (read 652); e36 §2.1 Faisal Target "1,870" and e578 §0.2 Faisal "1,900" (read 2,040), with e578 eater-5.5 "Target 1,900 and 1,260 kcal consumed" (read 2,040 and 1,400; 640 left unchanged); e578 §0.2 Faisal boundary "05:00 with the Ramadan option on" (read J116); e578 eater-8.19 Sam's weights "82.0, 81.7, 81.9, 81.3" (read 84.2, 83.9, 84.1, 83.5 kg, `seed.md` §5.3); e578 eater-8.20 Mona types "٦٨٫٤" (read «٨٣٫٤», 83.4 kg); e19 eater-1.10 "stored value is 83.91459 kg" (read 83.91458845 kg, stored unrounded); e578 eater-7.9 and eater-7.11: Faisal's walk "07:00–07:45" and the running app's copy "07:01–07:44" (read 06:00–06:45 and 06:01–06:44 Riyadh, so the walk has ended before the 07:30 import; the manual walk starts 06:00 and the prompt reads «هل هو نفس المشي 6:00–6:45 من Apple Health؟»); support §0.3 E1 "created 2026-08-03" stays and the auditor's E1 onboarding events move to 2026-08-03 (J58). Also supersedes e578 eater-7.6 "Health holds 81.3 kg at 2026-10-01 06:50 … 81.3 kg … 179.2 lb" (read 83.5 kg at 2026-10-01T06:00:00Z, shown 184.1 lb).

**J55 · One value per Unit, derived from Foods with exact arithmetic**
- Lenses: e24 W2.15 and its seed; e36 §2.2; e578 §0.2, cross-lens table; approver cross-lens (10.13's old fixture in eater-2.35).
- Decision: every Unit's numbers are computed from its Food rows and masses (`seed.md` §7, every fraction shown). The cheese bite is **46 kcal, P 2.5 g, C 4.5 g, F 2.0 g** (5.4 g cheese 12.5 kcal + 1.5 g olive oil 13.5 kcal + 8 g Bread, baladi 20 kcal), the without-bread base is **cheese spoon · معلقة جبنة** 26 kcal (1.8/0.5/1.9), the bread bite 8 g is 20 kcal (0.7/4.0/0.1) and 9 g 22.5 kcal, the cup of laban 152 kcal (8/12/8), the egg bite (12 g egg + its 8 g bread) 38.6 kcal (2.26/4.12/1.42), the tuna bite 33.6 kcal, the olive 5.3 kcal, the glass of milk tea 60 kcal (1.6/9.8/1.6), the talbina spoon 20 kcal (0.8/2.625/0.7), the baladi loaf (92 g) 230 kcal (8.05/46/1.15), the foul spoon with oil 43.5 kcal (2.0/4.0/2.2), the fries handful 93 kcal, the salad spoon 16.2 kcal (0.3/1.5/0.99), the grilled chicken bite 48.8 kcal, the hummus bite 54.35 kcal. The kabsa rice spoon (42.4) and chicken piece (114) keep their values. Eater-2.35's label fixture becomes approver-10.13's: 95 kcal per 30 g serving, P 2 g, total carbohydrate 20 g (sugar alcohols 8 g, fiber 2 g), F 3 g, 4/4/9 = 115 kcal.
- Reason: WF-2 done-when and FRD §2.2 (the cheese bite's three parts); e24 and e36 (most stories) already use 46; FR-026 and §6.1 (numbers come from records, not typed totals); NFR-01.
- Supersedes (e578, lines that used the old values; the new figures computed in `seed.md` §7.6): §0.2 Units table rows; eater-5.12 `/r` "3 foul spoons show 138 kcal, not 75" (read: 3 foul bites show 150 kcal, not 90) and `/m` "(46.0 kcal, P 2.2, C 6.6, F 1.2 per count) … (47.4 kcal)" (read foul bite 50.0 kcal, P 2.7, C 8.0, F 0.8; cheese bite 46.0); eater-5.17's count/calorie rows (read «حسب السعرات ٣٦٫١ · ٢٢٫١ · ٠٫٦ · ٣٧٫١ · ٤٫١ ٪» and «٨٣٢ سعرة»); eater-5.21 (read: blocker shares foul bite 64.00 %, cheese bite 39.13 %, egg bite 43.03 %; change "Raise the maximum to 39.2 %"; "Use your cheese spoon" at 7.60 %; after raising, 7 cheese bites, 322 kcal, carbohydrate 39.13 %; the egg bite reads "43.0 % — above the maximum"); eater-5.22 (read fries 93 kcal; "2 fries + 4 chicken + 1 hummus = 435.55 kcal", shown 436); eater-5.23 (read changes "Lower the protein minimum to 53 g" (3 chicken + 1 laban, 494 kcal), "Raise the ceiling to 646 kcal" (3 chicken + 2 laban, 61 g), "Allow 4 chicken pieces"; `changes[]` 53, 646, 4); eater-5.32 (read 2 egg bites 77.2 kcal, shown 77); eater-5.44 (read 38.6 kcal each, aim about 77.2); eater-5.14 `/m` "of the 12 count sets that meet every limit" (read "of the 3 count sets that meet every limit", the aim band 360–440 being a limit: 4 + 2, 1 + 3, 2 + 3; the best and the next nearest hold); eater-5.17 `/m` "Calorie aim about 809.7" (read 831.7: with the seed's Units 6/4/1/8/1 totals 831.7 and is then the only zero-deviation answer); eater-8.7 (read "1,246 eaten · 46 Pending", "Remaining 624", API 1,246); eater-8.35 (read "77 kcal lower"); e24 §"Reference Foods" Barley flour protein 10 (read 8 g); e36 §2.2 milk tea C 10.0 (read 9.8), talbina 0.6/3.2/0.5 (read 0.8/2.625/0.7), baladi loaf 8/46/1.5 (read 8.05/46/1.15), foul spoon with oil 45 (read 43.5); e24 eater-2.35 (read headline 95 kcal, "From the macros: 115 kcal (4/4/9). Labels can count fiber and sugar alcohols below 4 kcal/g."). Also supersedes e578 eater-5.44 `/m` "among the 12 count sets that meet every limit" (read "among the 3 count sets that meet every limit (4 + 2, 1 + 3, 2 + 3)"; the rest of the line holds).

**J56 · Mona's foul: a spoon and a dipped bite**
- Lenses: e24 seed (foul spoon "—"); e36 foul spoon 30 kcal; e578 foul spoon "dipped: + 1 bread bite".
- Decision: **foul spoon · معلقة فول** = 20 g of Mona's Recipe "foul · فول" v1 (150 kcal per 100 g), eaten with a spoon, no bread: 30 kcal, P 2.0, C 4.0, F 0.7. The planner stories use a second Unit, **foul bite · لقمة فول** = the same 20 g, dipped, with its 8 g bread bite: 50 kcal, P 2.7, C 8.0, F 0.8.
- Reason: e24 and e36 (Mona's 30 Sep Day of 1,098 kcal) use the spoon without bread; FR-018 makes bread a rule of a dipped Unit, not of the food.
- Supersedes: e578 §0.2 "foul spoon … dipped: + 1 bread bite … 46.0" and every e578 planner line naming "foul spoon" (read "foul bite").

**J57 · Grant fixtures: one history per Grant id**
- Lenses: auditor M1–M4, cross-lens (`acct_9c41e2`, `CASE-1201`, fixture Y); support K12(d), §0.3; e19 C-14, eater-10.x.
- Decision: the support and eater lenses agree, so their Grants win: `grant_31f0` (reads 10:24, 10:25, 10:27 UTC; no write attempt) and `grant_31f9` (`staff_mona`, Requested 11:30, Declined 11:42, refused read 11:45). The auditor's other Grants stay with new actors so that `staff_mona`'s Grants list (support-10.22) holds exactly support's rows: `grant_27b4` is by `staff_tariq`; `grant_40aa` (on `acct_3f88a1`) is by `staff_omar`. The auditor's write-refused evidence moves to `grant_7d01` (E4, 15:45). Fixture Y is withdrawn (J7), so `CASE-1201` belongs only to support-9.11. `acct_9c41e2` is one account: support's E1 with the auditor's Consent history (`seed.md` §11). Its second iPhone (app 1.0.2) last synced at 2026-09-30T19:15Z, the sync that carried the Conflict `cmd_7a1e`.
- Reason: eater-10.7 requires the eater's and the auditor's views to list the same reads at the same times; two lenses against one.
- Supersedes: support §0.3 E1 and support-9.4 device line "last sync 2026-09-29 20:40 UTC" (read "last sync 2026-09-30 20:15 your time (19:15 UTC) · 22:15 eater's time"); auditor §3 events 1–80 (rebuilt as `seed.md` §11 events 1–266; the 2026-10-01 → 10-04 part is events 175–266); auditor-10.2 (rows read from `seed.md` §11 "Derived facts"); auditor-10.3 `/s` ids 52, 54–60 (read 196, 198, 199, 200, 201, 205, 206, 207); auditor-10.1 "Chain intact through event 80", events 81–82 (read 266; live events 267–268); auditor-10.15 "Checking 82 events" (read 268; `audit_trail.verified` is event 269); auditor-10.3 (timeline: Requested 10:05 · Approved, Active 10:20 · Read 10:24 · Read 10:25 · Read 10:27 · Expired 11:20 · Read refused 11:20:01 · Read refused 11:20:05 — 8 events; header "3 reads allowed · 2 reads refused · 0 writes refused"); auditor-10.6 (`grant_31f9` "Declined by the eater · 11:42:00Z"); auditor-10.7 (read refused 11:45:00Z by `staff_mona`); auditor-10.13 (event read from `grant_7d01` 15:45); auditor-10.10 "Read refused at 10:30:00Z by `staff_mona`" (read `staff_omar`, event 264); auditor-9.13 "events 22–25" (read 106–109, requested by `staff_tariq`); auditor-9.10 rows (read `seed.md` §10.1); every other auditor event number through `seed.md` §11's old → new table; auditor-10.14 information line and auditor-9.1/9.2 counts (read from `seed.md` §11 "Derived facts").

**J58 · Deployment date, the trail's start and the first records**
- Lenses: auditor §3 (events 1–16 on 2026-09-01); support E1 "created 2026-08-03", E8 deletion requested 2026-08-20; e578 Mona's Target from 2026-08-20; admin §3 price "effective 2 Sep 2026".
- Decision: the deployment (trail start) is **2026-08-01T06:00:00Z**. Roles, Policy v1 (effective 2026-08-02T00:00:00+03:00) and the Registry baselines are dated 2026-08-01; E1 onboards on 2026-08-03 at the auditor's times of day. The seeded `gemini-3.8-flash` price row is "effective at seeding (2026-08-01)" through 2026-12-31; the 2027-01-01 rise is as written.
- Reason: three lenses date records before 2026-09-01 (E1 created 2026-08-03, E8's deletion 2026-08-20, Mona's Target 2026-08-20); A18's source gives no start date for the $0.75 price ("through December 31, 2026").
- Supersedes: auditor events 1–16 dates (read 2026-08-01 and 2026-08-03); auditor-9.9 "2026-09-01T18:20:00Z" (read 2026-08-03T18:20:00Z); auditor-10.18 "approved 2026-09-01T07:30:00Z · in effect 2026-09-02" (read 2026-08-01T07:30:00Z, 2026-08-02T00:00:00+03:00); auditor-10.19 "2026-09-01T12:00+03:00 shows 'No Policy version was in effect'" (read 2026-08-01T12:00+03:00); admin §3 and admin-10.45 "from 2 Sep 2026" (read "from seeding, 2026-08-01").

**J59 · The Registry history and the stamps it puts on Analyses**
- Lenses: auditor M8, events 8, 40–48; admin-10.1, 10.25–10.29; support §0.3 E5.
- Decision: task keys are the admin lens's (`meal`, `label`, …; J89). The seed holds `meal@v6` in Rollout from the launch baseline and `meal@v7` Proposed 09-22 → Shadow 09-23 → Canary 5 % 09-25 → Rollout 09-27 08:00 → Rolled back 09-27 11:30 (auditor's history). Admin-10.1 starts at 2026-09-26T12:00Z (v7 in Canary at 5 %); the admin's walk-through that proposes Meal version 7 starts at 2026-09-21T12:00Z. Schema is "schema version 3" (`analysis` family, API `schema_version: 3`). An Analysis made on 2026-10-01 is stamped `meal@v6`, prompt version 11, schema version 3.
- Reason: load-at-clock (J52) lets both lenses' timelines hold; one task key per task.
- Supersedes: auditor `meal_photo`, `meal_photo@v6/@v7`, schema `analysis.v3` (read `meal`, `meal@v6/@v7`, schema version 3) in §3 and 10.20–10.21; support §0.3 E5 "`gemini-3.8-flash`; prompt v14; schema v6" (read `meal@v6` · prompt version 11 · schema version 3).

**J60 · The Policy history and the version numbers in approver stories**
- Lenses: auditor Policy v1/v2; approver-10.49–10.62; e24/e578 "Policy version 1, In effect".
- Decision: the seed holds Policy v1 (In effect from 2026-08-02T00:00:00+03:00) and v2 (proposed by `staff_yara` 2026-09-29 07:30, approved by `staff_dina` 08:00, In effect from 2026-10-05T00:00:00+03:00; energy-mismatch threshold >12 % and >10 kcal; every other value as v1). The approver lens's default start clock is **2026-09-29T07:00:00Z** (v1 In effect, two approvers, no v2 yet), so its "Proposed v2", "v3" lines hold as written. Eater stories at 2026-10-01 see v1 In effect.
- Reason: load-at-clock (J52).
- Supersedes: approver-10.62 "signed by Mona Adel on 2026-10-01" (read "signed by Dina R. on 2026-09-29").

**J61 · Quotas version 1, and stories that need a tighter limit**
- Lenses: admin §3, 10.38–10.44; e24 seed ("image analyses hard limit 3"), eater-4.48; support §0.3 E1 ("3 of 10"), E5 ("10 of 10"), support-4.2, A12.
- Decision: the seed holds Quotas version 1 (admin §3): signed-in eaters — image tasks soft 15 / hard 25, Text, Voice and Explain soft 60 / hard 100; anonymous sessions hard 3 image / 10 text. A story needing a hard limit of 3 for a signed-in eater saves Quotas version 2 in its Given (`PUT /v1/admin/quotas`, image hard 3). The account panel shows per kind: E1 "Photo analyses today 3 of 25"; E5 "Photo analyses today 25 of 25; the 26th refused with `RATE_LIMITED` at 16:40 UTC".
- Reason: one seeded value; FRD §16.5 bounds per-user daily analyses without fixing numbers.
- Supersedes: support §0.3 E1 "3 of 10" and support-9.4 "AI analyses today 3 of 10" (read "Photo analyses today 3 of 25"); support §0.3 E5 and support-4.2 "10 of 10 … the 11th refused" (read "25 of 25 … the 26th refused"); e24 seed "image analyses hard limit 3 per eater per Day" (read: set in eater-4.48's Given as Quotas version 2).

**J62 · Former emails and other identifier clashes**
- Lenses: e19 cross-lens (SE8's email); support-9.12; auditor cross-lens (`CASE-1201`, `acct_9c41e2`).
- Decision: no account exists for `lina.synthetic@example.com` in the seed; eater-9.21's sign-up with that email is the story's own act on its own fresh copy, so support-9.12 ("No account matches this email") holds on the seed. `CASE-1201` is support-9.11's escalation only (J57).
- Reason: J52.
- Supersedes: none.

**J63 · Stale story-id cross-references**
- Lenses: auditor M7, M13; e19 cross-lens (old ids), eater-1.41; e578 cross-lens (§5 shared ids); support K12(e).
- Decision: a cross-reference is read through the target lens's current ids. The known corrections: e578 §5 eater-7.2 and 7.9 → support-7.1 (not support-9.19); eater-5.40 → admin-10.31, 10.33, 10.40 (not 10.30, 10.36); eater-8.8 → admin-10.29 (not 10.26); e19 eater-1.41 → eater-3.40 (not 3.39); auditor M7: the separation-of-duties save rule is admin-10.62, the self-change refusal admin-10.64; auditor M13: eater-9.12 → auditor-9.14, eater-9.16 → auditor-9.10–9.11, eater-9.11 → auditor-9.15, eater-9.28 → `grant_6e21`; support's old eater ids map by e19's "Fix round 1" table (support-9.14 old → 9.16, 9.17 → 9.19, 9.21 → 10.1, 9.22 → 10.2, 9.23 → 10.3, 9.24 → 10.4, 9.25 → 10.7, 9.26 → 10.8, 9.27 → 10.5, 9.29 → 9.23); e19's old support ids (9.8 → 9.9; 9.9 and 9.11 → 9.10 and 9.12).
- Reason: lenses renumbered in fix rounds (`way/lessons.md`).
- Supersedes: each listed reference.

**J64 · The simulators**
- Lenses: e24 cross-lens (17e vs 16e); e36 "iPhone 16e"; e578 "iPhone 17e".
- Decision: the smallest simulator is **iPhone 17e**, the largest **iPhone 17 Pro Max** (P34, the current models on iOS 26).
- Reason: blueprint §0 ("smallest and largest iPhone simulator"); P34.
- Supersedes: e36 "iPhone 16e" (read iPhone 17e).

---

## 9 · States (Units, Analyses, Plans, Activity, Registry, outbox)

**J65 · Units: Archived can be undone; Drafts never log**
- Lenses: e24 W2.10, eater-2.46; e36 W3.15, W3.16, eater-3.8.
- Decision: add **Archived → Saved** ("Unarchive", D4). An Archived Unit is not offered for new logs; an Entry logged offline from a Unit archived meanwhile is kept (the food was eaten) and the Entry offers "Unarchive unit".
- Reason: care group 4 (undo); FR-014 (versions are never lost).
- Supersedes: none.

**J66 · Analysis end states: Discarded and Failed stay ends**
- Lenses: admin A7.12, 10.34, 10.56; e24 W2.10, eater-4.22.
- Decision: no transition leaves Discarded or Failed. "Try again" on a Failed Analysis and "Analyse again" on a Discarded one (while its photo is within the raw-scan window) create a **new** Analysis with `retry_of` naming the old one; a job retry creates the new Analysis under the same command id.
- Reason: D2 Analysis states; FR-045 (abandoned drafts add zero).
- Supersedes: none.

**J67 · Registry versions: a replaced Rollout is "Replaced"**
- Lenses: admin A7.13, 10.26–10.27.
- Decision: add **Rollout → Replaced** (D4): when a newer version reaches Rollout, the one it replaced becomes Replaced; the roll-back target is the most recent Replaced version, shown as "Roll-back target: version n". Roll back moves the current Rollout to Rolled back and the target from Replaced back to Rollout.
- Reason: D2 has no state for the former Rollout; the auditor's "Live at" query needs it.
- Supersedes: none.

**J68 · Plans: Undo returns to Saved; a timeout makes no Plan**
- Lenses: e578 W5.1, W5.2, eater-5.24, 5.29, 5.34.
- Decision: add **Confirmed → Saved** and **Not eaten → Saved** on Undo (D4): the Plan's Entries are Voided and the Plan is Saved again. A solve that reaches its time limit with a plan returns a Proposed Plan (`solution_status: feasible`); with none it creates no Plan and answers `solution_status: unknown` with "No answer in time".
- Reason: FR-046 (Undo for recent mutations); NFR-04 (explicit timeout status); FR-045 (no double count).
- Supersedes: none.

**J69 · Activity has the Entry's states; the outbox command has its own status**
- Lenses: e578 W5.17, 7.12, 7.14; e36 W3.8; support Sync tab; e36 cross-lens (`STALE_REVISION` command).
- Decision: Activity: **Pending → Confirmed; Confirmed → Corrected · Voided → Restored** (D4). A command in the device outbox has a **status**, not a state: Queued → Sent → Accepted · Conflict. A command refused with `STALE_REVISION` is **Conflict**: it is not Pending, not counted, and waits for the eater's choice without expiry; the Confirmed value stands meanwhile. Support's Sync tab reads "Conflict · waiting for the eater's choice".
- Reason: FR-063 (manual entry and correction); FRD §8.3 ("distinguishes pending from confirmed totals").
- Supersedes: support Jobs → Sync row "Pending on the device" (read "Conflict · waiting for the eater's choice").

---

## 10 · Units, Recipes, reference data and Evidence

**J70 · The Evidence badge and the value basis**
- Lenses: approver P7.6, P7.8, 10.15, 10.24; e24 W2.2, W2.7, eater-2.10, 2.15, 2.37, 4.34.
- Decision: the five badges stay (map ¶4). A number's badge comes from its nutrition source, weakest component first: **user-defined** (a calorie-only override) · **estimated analogue** (any component resolved by analogue) · **recipe-calculated** (a Recipe or Tier B recipe record) · **label-verified** (a label or manufacturer record with its evidence, including a restaurant's published value with evidence; source details say "Restaurant's published value") · **measured** (an Approved reference Food — Tier A or approved — applied to the eater's amount; source details read "Measured unit · reference nutrition"). The **value basis** of an amount is a separate word set, **measured · declared · estimated**, always shown with the word "amount" ("amount declared"); the approver's per-nutrient marker uses the same three words.
- Reason: FRD §20.1 ("measured unit + reference nutrition"); FR-012; FR-025 (label/manufacturer data is one tier); one spelling, "estimated", for both lenses.
- Supersedes: approver §6 and 10.24 marker "estimate" (read "estimated").

**J71 · "Recipe" and the Tier B recipe record; Recipe mode**
- Lenses: approver P7.1; admin §3, A7.1; admin cross-lens (recipe analysis task).
- Decision: one calculation engine, two owners: the eater's **Recipe** (private; Draft → Saved → Archived) and the approver's **Tier B recipe record** (public; reference states). The console's **Recipes** section shows only Tier B recipe records. The capture mode "Recipe" uses the AI task **Ingredients** (`ingredients`).
- Reason: D2 states; map ¶4; FRD §14 capture modes.
- Supersedes: none.

**J72 · Unit fields**
- Lenses: e24 §6.
- Decision: `unit_kind` is the kind of amount (bite · spoonful · sip · cup · piece · slice · handful · custom; FR-009) and `structure` is simple · composite · recipe; a Unit version names exactly one Food version or Recipe version per component (FR-010).
- Reason: one field per meaning.
- Supersedes: none.

**J73 · Logging a Unit that has not synced yet**
- Lenses: e24 W2.1, eater-2.48.
- Decision: allowed. The outbox orders the Unit Save before the consume command that names it; both show Pending; the server accepts them in order and resolves the Entry from the saved version. If the Unit Save is refused, the dependent consume command is refused with it and both are shown to the eater.
- Reason: EX-21 (offline is quiet); FRD §18.1 (the server resolves from the approved snapshot, which it does once the Save is accepted).
- Supersedes: none.

**J74 · Component-sum tolerance**
- Lenses: e24 W2.5, eater-2.19; approver-10.55.
- Decision: Policy v1's component-sum tolerance is **the larger of 2 % of the measured total and the scale's step**. The step is recorded on the measurement evidence; when none is recorded it is the finest decimal place of the weights typed (0.1 g for "38.1 g"). The parts fail when they differ from the total by more than the tolerance.
- Reason: FR-023 ("configurable measurement tolerance"); a 1 g-step scale cannot meet 2 % of a 6.9 g portion (e24 W2.5); the FRD's fixtures are given to 0.1 g.
- Supersedes: none — the lens examples hold: eater-2.19 (38.1 g vs 39.1 g: 1.0 g > max(0.762, 0.1) → error; 38.4 g: 0.3 g → no error) and approver-10.55 (6.9 g vs 7.1 g: 0.2 g > max(0.138, 0.1) → error).

**J75 · Household rules, Unit versions and the "serving template"**
- Lenses: e24 W2.6, W2.10, eater-2.22, 2.27, 2.44.
- Decision: a User rule (accompaniment, preparation default) is versioned on its own (FRD §17 UserRuleVersion) and applied when logging; the applied rule version is kept in the Entry's snapshot. Changing the bread rule does not create new versions of dipped Units; My Units shows "Includes 8 g bread (your bread rule)". The bread bite's own versions (8 g → 9 g) apply to new logs. FR-022's "serving template" is a Saved Composite (a "with bread" variant), never a Template.
- Reason: FR-014, FR-018–FR-022; map ¶4 (Template = a saved meal).
- Supersedes: none.

**J76 · Which photos are kept**
- Lenses: e24 W2.9, eater-2.6, 4.34.
- Decision: a Unit's picture chosen by the eater is saved and lives with the Unit; a scale or label photo behind a measured Unit is a raw scan (deleted after 30 days) unless the eater taps "Keep photo with this unit"; the numbers survive the photo's deletion.
- Reason: FR-078 ("raw scans up to 30 days unless saved"); FRD §17 MeasurementEvidence.
- Supersedes: none.

**J77 · Energy-mismatch flags come from shared records only**
- Lenses: e24 W2.16; approver-10.13, 10.14, 10.54; seed-check D11–D12.
- Decision: a private Unit's label never raises a flag. The check runs over label values and approver-entered records only — Label submissions, approver-approved Foods and Tier B recipe records. Tier A rows are not cross-checked: FDC derives their energy with food-specific factors and counts fiber inside carbohydrate by difference, so general 4/4/9 gaps there are expected (in the seed: Date (generic) 282 vs 313.31, Cumin, ground 400 vs 446, Falafel 333 vs 340.6 — no flag).
- Reason: FR-080 (de-identified); FR-030 (keep the label; flag for review — FR-030 is about labels); FRD §10.1 and S12 (food-specific factors and fiber accounting differ); approver AP14 (FDC publishes Atwater-specific and general energy).
- Supersedes: none.

**J78 · Flags and Label submissions are vocabulary words**
- Lenses: approver P7.8, §6; e24 W2.10.
- Decision: **Flag** (types Estimated analogue · Energy mismatch · Unmatched name · Ingredient updated; Open · Closed) and **Label submission** (Proposed → In review → Approved · Rejected) join the vocabulary (D4); **Cross-check** is the map's own word.
- Reason: D2 rule for new words.
- Supersedes: none.

**J79 · Carbohydrate convention and the spelling of fiber**
- Lenses: approver §6, 10.25; e578 8.32.
- Decision: carbohydrate convention **total (fiber included) · available (fiber excluded)**; API `carbohydrate_basis` `total` · `available`; screen spelling "fiber" (the FRD's).
- Reason: FRD §10.2 wording; one name per thing.
- Supersedes: approver "fibre" (read "fiber" on screen); e578 `includes_fiber` · `excludes_fiber` (J50).

**J80 · Retired or Superseded reference data and existing Units**
- Lenses: approver P7.7, 10.29; e36 W3.12, eater-6.13.
- Decision: logging an existing Unit whose Food version was Retired keeps working on the Unit's snapshot; My Units shows "The reference for this unit was retired — Update unit". When the retirement reason is "defective", the notice also lists the affected past Entries' Days with "Apply to past entries…", which changes nothing until the eater picks the scope (FR-031).
- Reason: D2 ("existing snapshots unchanged"); FR-031.
- Supersedes: none.

**J81 · Alias precedence**
- Lenses: research R6.3; e24 W2.14, eater-2.42; e36 eater-3.10; approver-10.46.
- Decision: the eater's own Unit name or Alias → the eater's dialect setting → the approver's approved Alias for the region's default dialect. Retiring a reference Alias never touches an eater's own names.
- Reason: FRD §5.1 precedence; F27.
- Supersedes: none.

**J82 · Eater-typed text in the console**
- Lenses: approver P7.5, 10.10, 10.18; e24 W2.17, eater-4.17.
- Decision: text an eater typed or said appears in the console only when at least 5 distinct eaters used the same normalised text in the last 28 days.
- Reason: FR-080 ("de-identified"). **(product choice — counsel can revisit the threshold)**
- Supersedes: none.

**J83 · Wikidata is not an integration**
- Lenses: approver P7.10, 10.45.
- Decision: no Wikidata adapter in v1. Label seeds come from a CC0 file imported through the same import job as a USDA release (a file, not a service).
- Reason: blueprint §0 line 6 names three external integrations.
- Supersedes: approver-10.45 "Fetch Wikidata labels" (read "Import CC0 label file").

**J84 · Who owns the evaluation set's reference values**
- Lenses: approver P7.11; admin-10.13–10.17.
- Decision: the Platform admin owns the regression set and its runs; ground-truth reference values for consented cases are entered by a holder of a custom role "Evaluation reviewer" (permission "View consented evaluation cases", J28), which a Nutrition approver may also hold. NFR-11's split (weighed or recipe-grounded vs photo-only) is a column of the evaluation report.
- Reason: NFR-09–NFR-11; separation (the approver role itself gains no media access). **(product choice — the owner can revisit it)**
- Supersedes: none.

**J85 · What Support sees of a Label submission**
- Lenses: approver P7.12, 10.66.
- Decision: the account panel lists the eater's Label submissions with state and reject reason only; never the photos.
- Reason: §19.2; FR-081.
- Supersedes: none.

**J86 · The water-difference check**
- Lenses: approver P7.16, 10.11.
- Decision: INFOODS's "difference in the water content is higher than 10 %" is read in g per 100 g (percentage points).
- Reason: approver AP2 as recorded. **(product choice — the nutrition reviewer can revisit it)**
- Supersedes: none.

**J87 · Copies and Templates log the latest version; an old version in an offline command is kept**
- Lenses: e36 W3.6, eater-3.17, 6.12.
- Decision: copies and Templates log each Unit's latest Saved version and show "changed since"; an offline command naming a superseded version is stored as sent with a one-tap "Update to version n".
- Reason: FRD §17 ("Latest approved version is used for new logs only"); FR-014.
- Supersedes: none.

**J88 · A trial joining an account**
- Lenses: e19 C-17, eater-1.53.
- Decision: a trial Unit identical in name and definition to an account Unit merges; same name, different definition asks once: "Keep both" keeps two Units (the trial one renamed "<name> (from this iPhone)"), never two versions of one Unit; the account's Unit stays the default for new logs.
- Reason: FR-001 ("without duplicate units or meals").
- Supersedes: none.

---

## 11 · Registry, AI tasks, quotas and cost

**J89 · Task keys, names and the camera modes; an Explain task**
- Lenses: admin §3, A7.1; e24 §"How to read" (modes); e578 W5.19, 5.19, 5.40; admin cross-lens (recipe analysis); e578 cross-lens (plan explanations).
- Decision: seven tasks: `meal` **Meal**, `label` **Label**, `scale` **Scale**, `ingredients` **Ingredients**, `text` **Text**, `voice` **Voice**, `explain` **Explain** (the optional plain-language explanation of a verified Plan, FRD §16.2). Camera modes map: Meal → `meal`; Unit → `scale` when a scale display is in frame, else `meal` with intent calibrate; Label → `label`; Recipe → `ingredients`. Voice → `voice` then `text`; typed sentences → `text`. Explain counts against the Text and Voice quota.
- Reason: FRD §16.2 lists "explain a verified plan" as an allowed AI action; one kill switch per task (D2).
- Supersedes: admin-10.1 "Six rows are shown" and "returns six tasks" (read seven, with Explain).

**J90 · Quotas are their own version**
- Lenses: admin A7.14, 10.38–10.39.
- Decision: **Quotas version n** is a versioned configuration inside Registry, with In use · Rolled back; a Registry version stays "model id + prompt + schema per task" (D2).
- Reason: map §6 puts quotas in Registry; D2's Registry version names model, prompt and schema only.
- Supersedes: none.

**J91 · The quota day is the eater's diary day**
- Lenses: admin A7.2, 10.44; support A12, 4.2; e24 4.48; e36 3.14.
- Decision: a quota resets at the eater's diary-day boundary in their time zone (for a 00:00 boundary, local midnight); the AI spend cap stays on the UTC day. A repeat log of a Saved Unit, a copy or a Template never counts.
- Reason: FRD §8.1; FRD §16.5 ("A confirmed repeated unit uses no new nutrition inference"); research R6.9.
- Supersedes: support A12 ("local midnight") for any eater whose boundary is not 00:00; support-4.2 E5 reset "00:00 eater's time" holds (E5's boundary is 00:00).

**J92 · The AI spend cap turns every task's kill switch On**
- Lenses: admin A7.8, 10.48.
- Decision: at the cap, every task's kill switch goes On (cause `spend_cap`); typed sentences fall back to the Unit-name match (J126), and every logging path keeps working.
- Reason: §16.5 (bound cost), §16.4 (kill switch preserves manual logging). **(product choice — the owner can revisit the order)**
- Supersedes: none.

**J93 · Voice: a GA model in Rollout; Gulf Arabic is gated by evaluation**
- Lenses: admin A7.6, 10.7; e24 W2.13; blueprint §1.7.
- Decision: `voice@v1` runs on `gemini-3.8-flash` audio input (GA); `gemini-3.5-transcribe` (preview, `global`, ar-EG only) may run in Shadow only. Voice by dialect has a launch gate on the evaluation set (NFR-10); typed and tapped logging never depend on voice.
- Reason: P1–P4 (no preview models in Canary or Rollout); P15 as narrowed; NFR-10.
- Supersedes: none.

**J94 · The kill switch: the shutter, an open review screen, Shadow, and its copy**
- Lenses: admin A7.4, 10.21, 10.33, 10.34; e24 W2.19, eater-4.47; admin cross-lens (10.33's copy); e578 cross-lens ("paused").
- Decision: when the app knows the switch is On (fetched on opening Capture & Plan, propagation ≤10 s), the shutter and microphone are disabled with the note **"Photo and voice reading is off for now. You can log from My Units, a Template or an amount."**; a photo taken before the app learns it gets 503 `AI_UNAVAILABLE` and becomes a Failed Analysis (admin-10.34 is that race). An Analysis already Ready for review can still be approved. Shadow sends nothing while the switch is On. The word "paused" is never used.
- Reason: D2 (fail fast, nothing queued); §16.4; NFR-05.
- Supersedes: admin-10.33 proposed note (read the copy above); admin-10.34 `/r` Given (read "the app has not yet learned that the switch is On").

**J95 · Failure notes on Failed jobs**
- Lenses: admin A7.15, 9.3, 10.57.
- Decision: no new states; a Failed job carries `failure_reason` ∈ `account_deleted` · `consent_withdrawn` · `attempts_exhausted` · `storage_timeout` · `processor_timeout` · `provider_unavailable` · `kill_switch_on`, filterable on Jobs.
- Reason: D2 Failed; FR-080.
- Supersedes: none.

**J96 · The location field is read-only**
- Lenses: admin A7.7, 10.11.
- Decision: the model location is shown read-only on Registry › <task> (`global` in the seed); residency belongs to the owner and counsel.
- Reason: blueprint §1.7; P11 as corrected.
- Supersedes: none.

---

## 12 · Policy and Targets

**J97 · Policy v1's full list of values**
- Lenses: approver P7.15, 10.48–10.56, 10.68–10.70; e19 C-6, C-7, fixtures; e24 seed; e578 §0.2; map §1 ¶6.
- Decision: Policy v1 holds every value in `seed.md` §4.1, including the rows the map's ¶6 list lacked: the activity multiplier × 1.2 (one level in v1), the Activity-adjusted credit (factor 50 %, cap 300 kcal a day, eligible: deduplicated workouts and confirmed manual Activity), the default macro split 30/40/30, the Target review interval 14 days, the loss and gain choices, the Low/Medium/High carbohydrate thresholds (26 % and 50 %), the tracking-only withheld limits, and the Suggested Target bounds (FR-060/061). The near-duplicate window is a product constant (10 minutes), not Policy (J118).
- Reason: FR-003, FR-057, §11.3, §12.2, §10.3, §3.3; D2 (Policy is approver-owned).
- Supersedes: none.

**J98 · The hard stop is a code minimum**
- Lenses: approver P7.9, 10.50.
- Decision: Policy may raise the hard stop but never below 1,000 kcal (fixed in code); the floor may never be below the hard stop.
- Reason: R32; §19.3 ("Dangerous restriction requests require a safe response"). This narrows C1's "admins configure values" for one safety value.
- Supersedes: none.

**J99 · The floor binds every Target the app proposes**
- Lenses: e19 C-1, eater-1.30.
- Decision: no proposal (lose, maintain, gain) below the floor. When maintenance is at or below the floor, Maintain is offered at the floor and Lose is not offered.
- Reason: map §5 WF-1 done-when ("a target not below the policy floor").
- Supersedes: none.

**J100 · Entered and clinician-provided Targets against the floor and the hard stop**
- Lenses: e19 C-2, eater-1.19, 1.31; e578 W5.20, 5.25; approver-10.50; e578 cross-lens (approver has no rule).
- Decision: a Target "Entered by you" may not be below the floor (`POLICY_FLOOR`, `limit: floor`); a "Clinician-provided" Target may sit between the hard stop and the floor, with one confirmation line naming the clinician source; nothing may be below the hard stop (`POLICY_FLOOR`, `limit: hard_stop`). In tracking-only mode there is no Target entry at all.
- Reason: FR-004 allows a clinician-provided Target; FRD §11.4 bars restrictive plans for excluded users; R32. **(product choice — the owner and the nutrition reviewer can revisit it)**
- Supersedes: e19 eater-1.31 "refuses any Target below the floor whatever its source" (read: clinician-provided Targets may sit between 1,000 and 1,200).

**J101 · Entered Targets beyond the deficit cap**
- Lenses: e19 C-22, eater-1.32; e578 W5.21, 7.18; e578 cross-lens (approver cap vs −20 % Targets).
- Decision: Targets the app estimates never exceed the cap; an entered or clinician-provided Target beyond the cap (but not below the floor) is accepted with a neutral note and `over_deficit_cap: true`, and is not asked again.
- Reason: FR-004 (preserve the source); R33; FRD §11.3's own example (20 %).
- Supersedes: none.

**J102 · Target rounding**
- Lenses: e19 C-8, EA4.
- Decision: the eater approves the Target as shown (nearest 10 kcal); that approved value is the Target every report and budget uses; the unrounded computation stays in the input snapshot.
- Reason: FRD §11.3 (1,867.84 "displayed as 1,870"); FRD §23.2 (users approve their defaults); FR-071.
- Supersedes: none.

**J103 · The Activity-adjusted credit, its names and its offer**
- Lenses: approver cross-lens (credit approval, mode name, editable credit, new credit offer); approver-10.69; e19 eater-1.40; e578 eater-7.16, 7.18.
- Decision: the Policy's credit factor and cap are the **default and the upper bound**; the eater may lower either, never raise; the mode takes effect only on "Approve", and the approved factor and cap are stored on the Target version. When a newer Policy offers a different credit, Settings → Activity → Activity mode shows "New activity credit available: <factor> % up to <cap> kcal — Review", which changes nothing until approved (an eater story to add at the next lens edit). Names: **Fixed mode** and **Activity-adjusted mode**; Today reads "Food Target: Fixed" or "Food Target: Activity-adjusted".
- Reason: FRD §12.2 ("a visible user-approved credit factor and cap"); FR-058; approver-10.58 (approved Targets are not rewritten).
- Supersedes: approver-10.69 "an eater in activity-adjusted mode" (read "Activity-adjusted mode"); e19 eater-1.40 "Activity-adjusted target" (read "Activity-adjusted mode"); e578 eater-7.18 "each editable" (read "each may be lowered, not raised").

**J104 · The Activity-adjusted base**
- Lenses: e19 cross-lens (1,709.78 vs 1,707.84), eater-1.32, 1.40; e578 eater-7.18.
- Decision: base = approved Target × (maintenance without the exercise component ÷ maintenance) — for Sam 1,870 × 2,134.8 ÷ 2,334.8 = 9,980,190/5,837 = 1,709.81…, shown 1,710; the gap reads "−19.9 %" (1 − 1,870 ÷ 2,334.8).
- Reason: the approved Target is the eater's figure (J102); FRD §12.2 ("a documented baseline that excludes the selected exercise component").
- Supersedes: e578 eater-7.18 "your 20 % = 1,707.84" (read "your Target without the exercise = 1,709.81"); e19 eater-1.40 `/m` "1,709.78" (read 1,709.81) and "−20 %" (read "−19.9 %").

**J105 · The Targets API**
- Lenses: approver cross-lens; approver-10.69; e19 eater-1.42; e578 eater-8.23.
- Decision: both exist: `GET /v1/targets` (every version, newest first) and `GET /v1/targets/current` (the version in effect at the request time); each carries `input_snapshot` (with `activity_multiplier`), `policy_version`, `source`, `effective_from`, `effective_to`.
- Reason: two things, two paths.
- Supersedes: none.

**J106 · Target history: one place, one row format**
- Lenses: approver cross-lens (place, strings); e578 eater-8.23; e19 eater-1.32; approver-10.69.
- Decision: **Progress → Target history**. A row reads "<kcal> · <from> – <to> · <source>", source one of **Estimated by the app · Entered by you · Clinician-provided · Accepted suggestion**; the row's details add the method line (e.g. "Estimated by the app: maintenance 2,200, −15 %") and "Activity × 1.2 · Policy v1".
- Reason: map §4 WF-8 ("target history"); FR-004 (preserve source).
- Supersedes: approver-10.69 "the target history … shows that Target with 'Activity × 1.2 · Policy v1'" (read: in the row's details); e19 eater-1.32 "typed by you" (read "Entered by you").

**J107 · A raised floor or hard stop and Targets already approved**
- Lenses: approver P7.3, 10.58.
- Decision: a new floor applies to new proposals only; approved Targets stay until the eater approves another (Today shows "Review my target"). A raised hard stop applies at once to proposals and to the planner (no Plan below it), and the same notice appears; the approved Target is never rewritten.
- Reason: FR-058, FR-071; R32.
- Supersedes: none.

**J108 · Stopping an approved Policy version before it takes effect**
- Lenses: approver P7.14.
- Decision: no cancel state; approve a newer version with an earlier or equal effective-from.
- Reason: D2 Policy states; the trail keeps both.
- Supersedes: none.

**J109 · The safety screen**
- Lenses: e19 C-4, C-5, C-23, EA3; approver-10.53.
- Decision: skipping the screen changes no number; the Target screen keeps one neutral line offering it. Only the resulting mode is stored, never the answers or the trigger. Tracking-only guidance shows on Onboarding · Safety screen and Settings → Goals, never on Today; the approver's preview shows those two places.
- Reason: FR-008 (no forced sensitive details); R3 (minimisation); E24 (discretion). **(product choice — the nutrition reviewer can revisit it)**
- Supersedes: approver-10.53 `/r` "as the eater sees it on Today" (read "on Onboarding · Safety screen and Settings → Goals").

**J110 · Tracking-only, Hide numbers and the planner**
- Lenses: e578 W5.10, W5.14, eater-5.27, 5.43.
- Decision: Policy names the limits withheld in tracking-only: calorie aim, calorie ceiling and carbohydrate maximum (the protein minimum stays). In Hide numbers the planner is offered with a hidden Calorie aim (what is left today), no numeric limit can be typed, and results show counts only.
- Reason: FRD §11.4; R37.
- Supersedes: none.

**J111 · A Target needs an account**
- Lenses: e19 C-3, eater-1.49.
- Decision: the Target flow runs on the server engine and needs the Diary processing Consent, so a local-trial eater creates an account first; with no network, "Set a Target" says "Connect to set a Target".
- Reason: FRD §15.1 (nutrition core on the server); R21, R22.
- Supersedes: none.

---

## 13 · Timing and Day rules

**J112 · "Meal"**
- Lenses: research R6.2; e36 W3.1, eater-3.16–3.20, 3.29.
- Decision: **Meal** (Arabic «وجبة») = the Entries with one meal name on one Day. Each Entry carries a meal name: a default by time of day (Breakfast, Lunch, Dinner, Snack) that the eater may rename, with custom names (iftar, suhoor). FR-069's "addition report" covers the Entries of one command.
- Reason: FR-069 ("meal report"); E1, E11, E12. Added to D4.
- Supersedes: none.

**J113 · The headline when Entries are Pending**
- Lenses: research R6.7; e36 W3.2, 3.25, 6.21; e578 W5.12, 8.7.
- Decision: Today's remaining figure counts Pending Entries (what was eaten) and says "incl. <n> Pending"; `GET /v1/reports/day` returns Confirmed totals only (Pending lives on the device); reconciliation compares Confirmed totals.
- Reason: FRD §8.3 ("distinguishes pending from confirmed totals"); NFR-01; EX-11, EX-12.
- Supersedes: none.

**J114 · `expected_day_revision` is not enforced on consume**
- Lenses: e36 W3.3, 3.26, 6.22.
- Decision: consume commands are accepted whatever the Day's revision (additions commute); correct, void, restore and move carry and check the expected **entry** version.
- Reason: FRD §8.3 ("expected revision"); AT-31; FRD §18 ("corrections also include an expected entry version").
- Supersedes: FRD §18.1's example field `expected_day_revision` is sent and ignored for consume.

**J115 · Who assigns the Day**
- Lenses: e36 W3.11, W3.9, eater-6.17, cross-lens (6.17 vs item 11).
- Decision: on a new Entry the device proposes `diary_day_id`; the server checks it is the Day of `eaten_at` under the boundary in effect then, ±1 Day, and stores it as sent. On a correction that changes `eaten_at`, the server's Day assigner recomputes the Day with the boundary in effect at the new time; the correction carries the Day the preview showed, and a mismatch is refused 422 `VALIDATION_ERROR` (field `diary_day_id`).
- Reason: FRD §8.1 ("stable diary-day assignment"); FR-040; a late-synced boundary never moves an Entry.
- Supersedes: none.

**J116 · Default boundary, Ramadan days, and changing the boundary**
- Lenses: research R6.1; e36 3.28–3.30; e578 §0.2, 8.18; e24 cross-lens (Faisal's boundary); EX-20.
- Decision: the default diary-day boundary is **00:00** local. "Ramadan days" (one tap, Settings → Units & language) sets the boundary to **12:00** while on, so iftar, the late meal and suhoor stay on one Day; turning it off ends the current Day at the first normal boundary after the switch. A boundary change takes effect from the next Day (stored with `effective_from`), never reassigns past Entries, and each Day keeps the boundary it was built with; reports use the Target version effective on each Day.
- Reason: FRD §8.1; FR-071; EX-20; noon is inside the fasting day, so no meal sits near the boundary. **(product choice — the owner can revisit the hours)**
- Supersedes: e578 §0.2 Faisal "05:00 with the Ramadan option on" and e578 eater-8.18 "Faisal's Ramadan boundary 05:00" (read 12:00; the 2027-02-10 Day still lists all three meals).

**J117 · Start new day and the automatic boundary**
- Lenses: e36 W3.10, 3.32.
- Decision: "Start new day" creates the next Day early; the automatic boundary then creates no second Day for that date.
- Reason: FR-044.
- Supersedes: none.

**J118 · Near-duplicate window**
- Lenses: e36 W3.5, 3.24.
- Decision: 10 minutes, a product constant (not Policy): the same Unit and count logged again within 10 minutes shows a quiet note, never a block.
- Reason: FR-043 ("warning rather than being automatically discarded"). **(product choice — the owner can revisit it)**
- Supersedes: none.

**J119 · Moving an Entry and changing its time**
- Lenses: e36 W3.9, 3.33, 6.17.
- Decision: `eaten_at` is a correctable field; a move keeps the time; a time change re-assigns the Day (J115) and the correction preview shows the move; Days after the current Day are not offered; a new log's Time row offers only times inside the selected Day.
- Reason: FR-041 (move-to-day), FR-047.
- Supersedes: none.

**J120 · Undo's reach**
- Lenses: e36 W3.13, 3.5, 3.9, 3.20.
- Decision: Undo of a command still Queued removes it from the outbox (it never reaches the ledger); Undo after a multi-item command voids every Entry of that command. The WF-3 done-when "Undo removes exactly one entry" is read as "exactly what was logged".
- Reason: FR-046; EX-24.
- Supersedes: none.

**J121 · Siri**
- Lenses: e36 W3.7, 3.21.
- Decision: a Siri phrase logs one Unit at a count (P22); "Log with Sips & Bytes" with a free sentence opens quick-add with the sentence and logs nothing by itself. Arabic Siri phrases stay unverified; typed and tapped logging never depend on them.
- Reason: P22; blueprint §1.7.
- Supersedes: none.

**J122 · A late approval lands on the capture time's Day**
- Lenses: e24 W2.11, eater-4.49.
- Decision: an offline photo approved later lands on the Day of its capture time, shown and changeable before approval; never moved to today silently.
- Reason: FRD §8.1, FR-047.
- Supersedes: none.

**J123 · Apple Health writes: the device that confirms, writes**
- Lenses: e36 W3.4, 3.39, 3.40, 6.24.
- Decision: only Confirmed Entries are written. The device that confirms an Entry writes its food correlation and stores the sample id with its device id on the Entry; corrections and voids are written by that device at its next sync; another device never touches it. A sample the eater deleted in the Health app is not written again on a correction.
- Reason: map §3 ("Ledger → HealthKit"); P29; the eater's deletion in Health is their choice (`assumption` on cross-device behaviour, kept).
- Supersedes: none.

**J124 · Trial Entries stay on the iPhone; anonymous sessions are for AI only**
- Lenses: e19 C-24, C-16, EA11, EA12; admin-10.41.
- Decision: a local trial's Entries, Units and Templates live on the iPhone until Diary processing is Given at account creation. An anonymous session exists only for cloud AI (FR-001) and holds its Analyses, quota count, age confirmation and Consents. `POST /v1/consumption` with an anonymous-session token → 403 `CONSENT_REQUIRED`. Public Food search in a trial uses App Check and a per-install rate limit.
- Reason: R21, R22 (diary data needs explicit Consent); FR-001.
- Supersedes: admin-10.41 `/r` 2nd line "`POST /v1/consumption` with the anonymous-session token returns a Confirmed Entry" (read: the typed amount is logged in the trial on the iPhone; the API call returns `CONSENT_REQUIRED`).

---

## 14 · Analysis

**J125 · Who decides that a photo is a shared table**
- Lenses: e24 W2.12, eater-4.31; AT-27.
- Decision: the eater's choice ("Plan a meal" or "Log what I ate") decides; the analyzer may only suggest "Looks like a shared table — plan instead?". A dish read as shared starts at "My portion: 0".
- Reason: FR-037; AT-27.
- Supersedes: none.

**J126 · Typed Unit names without AI: the Unit-name match**
- Lenses: e24 W2.3, eater-4.44; e36 eater-3.14.
- Decision: the **Unit-name match** (D4) is a server path (and the same rule on the device offline) that matches typed words against the eater's own Unit names and Aliases and lists the matches with count steppers; it calls no model and sends nothing to Google. It runs when the Text kill switch is On, the AI Consent is not Given, the quota is used, or the device is offline.
- Reason: FRD §2.3, AT-32, FR-076.
- Supersedes: none.

**J127 · "A pass" is the whole Analysis**
- Lenses: e24 W2.4, eater-4.15; e36 eater-3.12; approver-10.55.
- Decision: an Analysis asks at most two questions in total across its life; after that it offers manual entry or an explicitly uncertain estimate.
- Reason: FR-035 (two questions per pass); a loop of passes would defeat it.
- Supersedes: e36 eater-3.12 "two questions in this pass" (read "two questions for this Analysis").

**J128 · One template for a "which one?" question**
- Lenses: e24 cross-lens (2.43 vs 4.30).
- Decision: the catalogue holds one template: "Which one? <A> or <B>", each choice a chip.
- Reason: one string per meaning (map ¶4).
- Supersedes: e24 eater-2.43 "cheese bite or cheese spoon?" (read "Which one? cheese bite or cheese spoon").

---

## 15 · Plans

**J129 · "Plan" means the meal Plan only**
- Lenses: e578 W5.3, 8.15.
- Decision: FR-072's and FR-074's "plan variance" are shown as "Intake vs Target"; "plan" in code, logs and screens is the meal Plan only.
- Reason: map ¶4.
- Supersedes: none.

**J130 · Which Unit versions a Plan confirmation uses, and when a Plan expires**
- Lenses: e578 W5.4, 5.38.
- Decision: "Ate as planned" logs the Plan's own `selected_versions` with a note if newer versions exist; a Saved Plan expires at the end of its Day (the diary-day boundary), after which its card offers "Plan again".
- Reason: FRD §17 MealPlan (`selected_versions`, `expiry`); FR-045.
- Supersedes: none.

**J131 · Day-scope plans exist only to refuse**
- Lenses: e578 W5.5, 5.25.
- Decision: v1 plans meals only; a typed "plan my day at 700" is read as intent plan with `scope: "day"` and is refused below the hard stop or floor with `POLICY_FLOOR`; an eater with a clinician-provided Target below the floor may plan a day at that Target, never below the hard stop (J100).
- Reason: FRD §19.3; FR-039.
- Supersedes: none.

**J132 · A ceiling with no aim**
- Lenses: e578 W5.6, 5.5.
- Decision: the Calorie aim is pre-filled with what is left today, lowered to the ceiling when the ceiling is lower; when the eater clears the aim, the planner aims at the ceiling from below.
- Reason: FRD §9.1 objective; "not above 500" asks for a meal near 500. **(product choice — the owner can revisit it)**
- Supersedes: none.

**J133 · Preparation complexity, available amounts and the paid tier**
- Lenses: e578 W5.25, W5.16, W5.15.
- Decision: preparation complexity = the number of distinct foods; availability on a shared tray stays unset unless the eater sets it; planning from Units is always free (the paid split stays an open owner question, blueprint §1.7).
- Reason: FRD §9.1, FR-033, FR-037, §23.3.
- Supersedes: none.

---

## 16 · Activity

**J134 · An Activity that crosses the boundary**
- Lenses: e578 W5.7, 7.22.
- Decision: it belongs to the Day it starts in.
- Reason: one rule; Entries use the eating time.
- Supersedes: none.

**J135 · Which Activity earns credit**
- Lenses: e578 W5.9, 7.19.
- Decision: deduplicated workouts and confirmed manual Activity; never the all-day active-energy aggregate. Policy v1 names this (J97).
- Reason: FRD §12.2 ("eligible net exercise"); FR-064.
- Supersedes: none.

---

## 17 · Reports and exports

**J136 · Rolling 7 days, and the first weekday**
- Lenses: research R6.4; e578 W5.11, 8.10, 8.16.
- Decision: "7 days" and "28 days" are rolling periods ending on the selected Day; the first weekday (from the device region, changeable in Settings → Units & language) is used only for separators and for the Custom picker. No calendar "this week" view in v1.
- Reason: FR-072; E14. **(product choice — the owner can revisit it)**
- Supersedes: none.

**J137 · Exports use Western digits and ISO dates**
- Lenses: research R6.8; e19 eater-9.15; e578 W5.13, 8.24.
- Decision: every export and the console's machine-readable files use Western digits, "." decimals and ISO 8601 times with offsets, whatever the eater's display setting; Arabic names stay intact UTF-8.
- Reason: FR-075 ("machine-readable").
- Supersedes: none.

**J138 · An unmarked past Day reads Partial**
- Lenses: e578 W5.18, 8.13.
- Decision: a past Day with Entries and no mark reads **Partial**; "Mark Day complete" toggles Complete ↔ Partial; a Day with no Entry is Unlogged; today is Provisional.
- Reason: FR-073; FR-060 counts only self-marked Complete Days.
- Supersedes: none.

**J139 · What an export holds**
- Lenses: e19 C-10, eater-9.14; support-9.8; auditor-9.14.
- Decision: one list: `entries.json` and `entries.csv` (each Entry with its Corrections, Voids, Restores and moves), `units.json` (every version), `recipes.json`, `templates.json`, `rules.json`, `targets.json`, `activity.json`, `weights.json`, `consents.json` (with the age confirmation), `day_reports.csv`, `period_reports.csv`, the eater's saved Unit pictures under `media/` (raw scans never), and `readme.txt` in the app's language. The console categories: Entries · Units · Recipes · Templates · Rules · Targets · Activity · Weights · Consents · Reports · Unit pictures.
- Reason: FR-075 (entries, portions, recipes, targets, reports); R31 (right of access covers saved photos).
- Supersedes: support-9.8 section list "Entries, Units, Recipes, Targets, Consents, Reports" (read the full list); auditor-9.14 categories (read the full list with counts, `seed.md` §10.1, including "Entries 212" → 216: the export at 2026-09-15T12:00Z is 15:00 Riyadh, so E1's 15 Sep Day already holds 4 Entries past her 04:00 boundary).

---

## 18 · Privacy jobs

**J140 · An export stays downloadable 7 days**
- Lenses: auditor M12, 9.5, 9.14; support A8, 9.8; e19 EA9, eater-9.14.
- Decision: 7 days. The auditor's export job moves so its file was still held when the AI Consent was withdrawn: `job_exp_4402` requested 2026-09-15T12:00:00Z, Completed 12:04:00Z, downloaded 12:10:00Z, kept to 2026-09-22T12:04:00Z, deleted early 2026-09-20T07:45:05Z.
- Reason: two lenses and the eater's own fixture agree.
- Supersedes: auditor §3 `job_exp_4402` dates and "14 days" (read as above); auditor-9.14 (read requested 2026-09-15, "link and file kept until 2026-09-22T12:04:00Z (7 days)").

**J141 · Deletion stages, and what "Completed" means**
- Lenses: support K12(b), 9.10; auditor-9.11, 9.12; e19 eater-9.17, 9.19; admin-9.1.
- Decision: one stage list, each with D2's job words (Requested · Running · Completed · Failed) or "Not applicable": signed out and disabled · private records deleted · photos and audio deleted · derived caches and cached analyses deleted · queued commands and uploads purged · prepared exports deleted · Grants withdrawn · processors told (Google) · processor confirmation received · Sign in with Apple token revoked · completion record. A deletion is **Completed** when every live-system stage is done; the backup lifecycle (30 days, `assumption`, to be disclosed) is a date on the completion record ("backups expire by …"), not a stage. The due date is the request date + 30 days, shown as the eater's calendar date.
- Reason: NFR-13 ("live-system deletion SLA ≤30 days; disclose backup expiry"); AT-29; R4.
- Supersedes: support-9.10 stage "backups expire — Running, by 2026-10-14" (read: E2's job is Running because "processor confirmation received" is Running, expected by 2026-10-14; the completion record will say "backups expire by …"); e19 eater-9.19 `/r` the same stage (same reading); e19 eater-9.17 "done"/"waiting" (read D2's words); admin-9.1 "failed with a storage error" (read: E9's stage "processors told (Google)" failed 3 of 5 attempts, `processor_timeout`).

**J142 · An attempt budget per job**
- Lenses: admin §3 ("Job attempts 5"), 9.1, 10.57; support §0.3 E3, 9.9.
- Decision: every job id has a budget of 5 attempts; the runner makes up to 3 automatic attempts, then the job is Failed; a manual Retry (support once for an export; the Platform admin while budget remains) resumes with the remaining attempts; at 5 of 5 the job reads "needs engineering" and Retry is disabled.
- Reason: §18.2 (bounded retries, same command id); NFR-12.
- Supersedes: support-9.9 `/r` "Export · Requested · retried by Mona K. · 0 of 3 attempts" (read "· attempt 4 of 5").

**J143 · Retention runs**
- Lenses: auditor §3 retention fixture, 9.15; admin-9.4; e19 eater-9.12.
- Decision: the retention job runs hourly and deletes audio older than 23 h and raw scans older than 29 d 23 h, so nothing passes 24 h or 30 days; each run is a job record (Jobs → Retention), not an Audit trail event.
- Reason: FR-078; FR-082 (retention verification).
- Supersedes: none.

**J144 · "Re-queue" is Retry**
- Lenses: support K12(c); e19 eater-9.16.
- Decision: the action is **Retry** (same id).
- Reason: D2 ("retried with the same id").
- Supersedes: e19 eater-9.16 "re-queues" (read "retries").

**J145 · Requests received outside the app**
- Lenses: support §13, 9.14, 9.15.
- Decision: a record (type, channel, received time, case, account, outcome note ≤120 characters) with states **Open · Escalated · Closed**, due 30 days after receipt (D4).
- Reason: SR11 Art. 3(1)(a)(d); NFR-13.
- Supersedes: none.

---

## 19 · Copy and string-catalogue rules

**J146 · Quoted English is the catalogue key; Arabic apps show the Arabic text**
- Lenses: support §0 "Eater-app language", cross-lens (eater Arabic lines); e19 eater-9.23, 10.2, 10.5, 10.9.
- Decision: in every lens, quoted English copy for the eater's app is the string catalogue's English key; on an eater whose app is in Arabic, the verifier observes the Arabic text of that key, with the eater's digit setting, and Latin-script runs (support codes, "Mona K.", "Google (Gemini)") isolated left to right.
- Reason: blueprint §1.4 ("Arabic labels are fixed once in the string catalogue").
- Supersedes: e19 eater-9.23, 10.2 `/r` 2nd line, 10.5, 10.9 lines that expect English on SE1's Arabic app (read their Arabic catalogue text).

**J147 · Times on each surface**
- Lenses: support §0 "Times"; admin-10.70; auditor-10.3 ("UTC and +03:00").
- Decision: storage and APIs: UTC. Console: "HH:MM your time (HH:MM UTC)", plus "· HH:MM eater's time" where the agent may repeat it; the Auditor's console zone is a setting (Asia/Riyadh in the seed). App: the eater's local time only.
- Reason: blueprint §0 line 5; one convention.
- Supersedes: auditor-10.3 "each time is shown in UTC and +03:00" (read "your time (UTC)" with the Auditor's zone).

**J148 · Words that never appear, and words that always do**
- Lenses: e578 §0 (gender-neutral Arabic), cross-lens ("paused"); admin §3 copy rule; e19 copy rule.
- Decision: one Arabic label per English word, gender-neutral (verbal nouns, impersonal phrasing); eater copy never shows FR/AT ids, error codes, field names, Policy-version or Registry-version keys; console copy leads with plain words and shows codes and ids in a secondary monospaced column; the kill switch is On or Off, never "paused".
- Reason: map ¶4; FR-002 (no inferred sex); D2.
- Supersedes: none.

---

## 20 · Coverage index — every source item and the item that settles it

| lens | items → J |
|---|---|
| admin §7 | A7.1 → J46, J47, J89 · A7.2 → J91 · A7.3 → J42 · A7.4 → J94 · A7.5 → J27 · A7.6 → J93 · A7.7 → J96 · A7.8 → J92 · A7.9 → J53 (settled by D2) · A7.10 → J44 · A7.11 → J28 · A7.12 → J66 · A7.13 → J67 · A7.14 → J90 · A7.15 → J95 · A7.16 → J21 |
| admin cross-lens | recipe analysis task → J71, J89 · 10.33 copy → J94 · first-admin event → J21 · profile values on Settings › Goals (closed by the lens) → J54 · test clock → J51, J52 · seeded prices on a fresh deployment → J58 |
| approver §7 | P7.1 → J71 · P7.2 → J26 · P7.3 → J107 · P7.4 → J29 · P7.5 → J82 · P7.6 → J70 · P7.7 → J80 · P7.8 → J70, J78, J79 · P7.9 → J98 · P7.10 → J83 · P7.11 → J84 · P7.12 → J85 · P7.13 → J42 · P7.14 → J108 · P7.15 → J97 · P7.16 → J86 |
| approver cross-lens | credit approval → J103 · mode name → J103 · 10.13 old fixture in eater-2.35 → J55 · editable credit → J103 · where the credit lives (joined) → J103 · Targets API → J105 · Target history place (joined) → J106 · new credit offer → J103 · Target history strings → J106 |
| support §12 | K1 → J9 · K2 → J4, J10 · K3 → J2 · K4 → J42 · K5 → J41 · K6 → J10 · K7 → J12 · K8 → J48 · K9 → J43 · K10 → J3 · K11 → J7, J35 · K12(a) → J9 · K12(b) → J141 · K12(c) → J144 · K12(d) → J57 · K12(e) → J63 · K12(f) → J6 · K12(g) → J9 |
| support cross-lens | eater wording and Grant fixtures → J9, J57 · auditor event names → J13 · ownership K3/K4/K5/K7/K9 → J2, J42, J41, J12, J43 · Arabic-app lines → J146 · small slips: the panel's "first tab" → J13 (the account panel opens with no jobs tab chosen and writes `account.viewed`; each tab — Privacy jobs, Sync, Failed Analyses, Activity — is its own request and writes `account.jobs_viewed`, so support-9.21's own Privacy-jobs request holds), "Analyses" → J47, 10.25's path → J46, pre-D3 lines → J25 |
| auditor §7 A | A-1 → J26 · A-2 → J19 · A-3 → J17 · A-4 → J18 · A-5 → J6 · A-6 → J19 · A-7 → J26 · A-8 → J20 · A-9 → J5 · A-10 → J2 · A-11 → J28 · A-12 → J19 · A-13 → J19 · A-14 → J13, J14, J16 · A-15 → J41 · A-16 → J7 |
| auditor §7 B | M1 → J57 · M2 → J57 · M3 → J57 · M4 → J9 · M5 → `seed.md` §8.3 (the approver's licence line wins: it is derived from the ingredients' licences) · M6 → J1, J5, J13 · M7 → J63 · M8 → J59 · M9 → J33 · M10 → J32 · M11 → J23, J24 · M12 → J140 · M13 → J63 · M14 → J39, J53 |
| auditor cross-lens | out-of-scope fixture → J7 · `CASE-1201` → J57, J62 · `acct_9c41e2` → J57 · cancelling a Requested Grant → J3 · direct media read → J8 |
| research §6 | R6.1 → J116 · R6.2 → J112 · R6.3 → J81 · R6.4 → J136 · R6.5 → J27, J28 · R6.6 → J2, J4 · R6.7 → J113 · R6.8 → J137 · R6.9 → J91 |
| e19 conflicts | C-1 → J99 · C-2 → J100 · C-3 → J111 · C-4 → J109 · C-5 → J109 · C-6 → J97 · C-7 → J97 · C-8 → J102 · C-9 → J30 · C-10 → J139 · C-11 → J31 · C-12 → J9 (settled; one wording) · C-13 → J10 · C-14 → J9, J57 · C-15 → J4 · C-16 → J124 · C-17 → J88 · C-18 → J32 · C-19 → J46 · C-20 → D3 (closed) · C-21 → J23 · C-22 → J101 · C-23 → J109 · C-24 → J124 · C-25 → J28 · C-26 → J32 |
| e19 cross-lens | old ids in other files → J63 · SE8's email → J62 · Health write name → J23 · own-Target source → J106 · Activity-adjusted base → J104 · eater-1.41's citation → J63 · Photos name → J23 · eater-3.2's "Set a Target" link → J46 (read Today's "Set a Target", which opens the Target flow at its first unfinished step; Settings → Goals is the same flow) · auditor "No records yet" → J25 · stored "Not given" → J25 · 1.45 Given and an offline trial "Set a Target" → J111 |
| e24 §5 | W2.1 → J73 · W2.2 → J70 · W2.3 → J126 · W2.4 → J127 · W2.5 → J74 · W2.6 → J75 · W2.7 → J70 · W2.8 → J29 · W2.9 → J76 · W2.10 → J46, J65, J66, J75, J78, J112 · W2.11 → J122 · W2.12 → J125 · W2.13 → J93 · W2.14 → J81 · W2.15 → J52, J55 · W2.16 → J77 · W2.17 → J82 · W2.18 → J24 · W2.19 → J94 |
| e24 cross-lens | Consent method wording → J24 · onboarding places → J46 · eater-3.6's verbless log (closed) → J126 · Fava beans FDC 2707367 in the seed → `seed.md` §8.1 · Faisal's boundary → J54, J116 · smallest simulator → J64 · "Which one?" template → J128 · Mona's egg Units note → `seed.md` §7.2 (egg bite only; "boiled egg" is a simple Unit added for eater-4.30) |
| e36 §6 | W3.1 → J112 · W3.2 → J113 · W3.3 → J114 · W3.4 → J23, J123 · W3.5 → J118 · W3.6 → J87 · W3.7 → J121 · W3.8 → J69 · W3.9 → J115, J119 · W3.10 → J117 · W3.11 → J115 · W3.12 → J80 · W3.13 → J120 · W3.14 → J22 · W3.15 → J46, J65 · W3.16 → J65 · W3.17 → J91 · W3.18 → J34 · W3.19 → J147 (Today's header always names the zone, FR-044; the second line keeps EX-07) |
| e36 cross-lens | `STALE_REVISION` command → J69 · who assigns the Day → J115 · one Health Consent rule (joined) → J31 · "Open Health settings" target → J31 · Health access copy → J31 |
| e578 §7 | W5.1 → J68 · W5.2 → J68 · W5.3 → J129 · W5.4 → J130 · W5.5 → J131 · W5.6 → J132 · W5.7 → J134 · W5.8 → J31 · W5.9 → J135 · W5.10 → J110 · W5.11 → J136 · W5.12 → J113 · W5.13 → J137 · W5.14 → J110 · W5.15 → J133 · W5.16 → J133 · W5.17 → J69 · W5.18 → J138 · W5.19 → J89 · W5.20 → J100 · W5.21 → J101 · W5.22 → J46 · W5.23 → J46, J49, J50 · W5.24 → `seed.md` §4.1 (Suggested Target bounds; the method stays FR-061's bounds plus R42's intake-and-weight approach, decided at the contract) · W5.25 → J133 |
| e578 cross-lens | §5 shared ids → J63 · plan explanations task → J89 · approver deficit cap vs −20 % Targets → J101 · "paused" → J94, J148 · Unit seed values → J55 · approver rule for entered Targets → J100 · §6 bookkeeping (Activity fields) → J50 |
| governor notes | 1 (approver totals; closed by the session) · 2 (support-9.4 D3; closed) → J25 · 3 (verifier independence) — no join item · 4 (admin-10.15/16 place) → J47 · 5 (fan-out rule cited) — no join item · 6 (auditor places) → J16, J47 |
| found by the join | Mona's and Faisal's Targets, Sam's weights → J54 · the deployment date → J58 · `PLAN_INFEASIBLE` usage → J38 · 503s with no code → J37 · the Platform admin's Audit trail slice → J15 · due-soon days → J45 · attempt budget → J142 · completion vs backups → J141 |

---

## 21 · Proposed delta D4 — words the decisions need (for the session to apply)

**2026-10-01 · delta D4 · the join's names.** Extends `way/vocabulary.md` (D2, D3). Reason: the model-phase join (`way/join.md`) settled the lenses' conflicts; each word below is used by a decision and by `seed.md` or `events.md`. Impact: every contract, screen, log line and test uses these words; the lens files are read through `join.md` until next edited; nothing is banked yet.

- **Things:** **Meal** (the Entries with one meal name on one Day; «وجبة») · **Flag** (Estimated analogue · Energy mismatch · Unmatched name · Ingredient updated; Open · Closed) · **Label submission** · **value basis** (measured · declared · estimated, always "amount …") · **carbohydrate convention** (total · available) · **Cross-check** · **Calorie aim** · **carbohydrate target** · **Suggested Target** · **daily AI quota** · **Quotas version** · **AI spend cap** and its **alert level** · **Canary check** · **roll-back target** · **regression set**, **regression case**, **evaluation run** (Evaluating · Finished · Cancelled) · **Grant settings version** · **Grant areas** (Entries and day reports · My Units · Templates · Activity) · **reason catalogue** · **support code** · **deletion reference** · **case reference** · **completion record** · **Request received outside the app** · **Wording** (a versioned consent or request text) · **Unit-name match** · **Health workout** · **active energy** · **credit factor** · **credit cap** · **Weight** (an observation; "Unusual — check") · **Entry history** · **review note** · **AI tasks** Meal · Label · Scale · Ingredients · Text · Voice · Explain · **capture modes** Meal · Unit · Label · Recipe.
- **States (additions):** Unit / Composite / Recipe: Archived → Saved ("Unarchive") · Registry version: Rollout → **Replaced** · Grant: Requested → Ended (cancelled by the Support agent) · Plan: Confirmed → Saved and Not eaten → Saved (Undo) · **Activity**: Pending → Confirmed; Confirmed → Corrected · Voided → Restored · **Label submission**: Proposed → In review → Approved · Rejected · **Flag**: Open → Closed · **Request received outside the app**: Open → Escalated → Closed · **Quotas version** and **Grant settings version**: In use · Rolled back · outbox command **status** (device only): Queued → Sent → Accepted · Conflict · other jobs (Analysis, import, retention, USDA release): as Privacy job.
- **Error:** `SERVICE_UNAVAILABLE` (503: a non-AI dependency failed; nothing was done; retry with the same id).
- **Audit outcomes:** Allowed · Refused · Done · Failed · Not found.
- **Places (app):** Onboarding · Age, · Under 18, · Consents, · Account, · Profile, · Safety screen, · Energy, · Target, · Macros, · Activity mode, · Review · the account line · Settings → Privacy → Grants · Support code · Delete account · Day picker · timeline · Entry details · Day report · Activity sheet · quick-add · count stepper · correction preview · Source details · My Units → Templates · Progress → Weight · Target history.
- **Places (console):** Registry › <task> (models list, prompt editor, regression set, quotas panel) · Metrics › cost view · prices panel · Jobs › Look up an account · account panel · Privacy help · Privacy jobs · Failed Analyses · Sync · Activity · Requests received outside the app · Escalated · Retention · Grants › Grant form · Grant panel · Grant bar · Diary (read-only) · Grants list · Review › flags · Label submissions · Audit trail › Events · Anomalies · Consents · Summary · Records of processing · Exports (action: Find account) · Settings › launch gates · Grant settings.

## 22 · Delta D6 — decisions on the model phase's last open points (2026-10-01, by the session)

Source: `way/personas/added-stories.md` ("Proposed for D6", "Conflicts for the model phase"). The nine defaults D6-A1…A9 are adopted as written, except where J151 below widens D6-A7.

**J149 · Adopt D6-A1…A9.** Grant settings bounds (1–24 h, one default), review-note fields and finding values, the no-eater-identifier check, the hourly Audit trail retention run with `removed_through_seq` and `anchor_hash`, the expired Plan's card on Today with "Plan again" only, "Plan again" refilling Meal planner without solving, a 409 `VALIDATION_ERROR` `reason: plan_expired` for a confirmation made after expiry, `credit_factor` as a decimal fraction, and a 409 `VALIDATION_ERROR` `reason: privacy_review_not_signed` — each is the decision. Reason: each default follows a decided pattern (J35's conflict form, J143's retention runs, support SR4) and serves its persona.

**J150 · An expired Plan stays Saved, with `expired_at`.** No new Plan state; copy says "not logged". `GET /v1/meal-plans?state=saved` lists unexpired Plans; `&include_expired=true` lists all. Reason: J130 already decides "no state change"; one fewer state.

**J151 · A confirmation made before expiry is accepted when it arrives later.** A consume command carries the device's `made_at`; when `made_at` is before the Plan's `expired_at`, the server accepts it once onto the Plan's Day (FRD §8.3: the server accepts an unprocessed command once; FR-047: a late edit updates the historical Day). A command made after expiry is refused (J149). With a 00:00 or 03:00 boundary, a next-morning eater logs last night's meal from the Day picker; eater-5.35 holds because its Ramadan Day runs to 12:00 (J116).

**J152 · The Saved Plan card lives on Today** (eater-5.20, 5.28, 5.30, 5.35, 5.45). Capture & Plan shows no Plan card.

**J153 · Wording states and place.** A Wording version is Proposed → Published · Superseded (a newer version of the same key is Published). Console place: **Settings › Wordings**. Reason: one vocabulary for every versioned text (J26).

**J154 · A new consent Wording asks again only when marked.** Each Wording version carries `asks_again` (set by the publisher, recorded in the Audit trail). When true, the next use of that purpose shows the Consent sheet and the purpose stays blocked (`CONSENT_REQUIRED`) until the eater gives or declines it; when false, Consents given under earlier versions stay valid under their own version. The seed's `c-ai-5` has `asks_again: false`, so Faisal stays under `c-ai-3`. Reason: R22/R31 (consent per purpose, documented) without re-asking for wording-only edits.

**J155 · A replaced Grant settings version is "Replaced"** (as a Registry version, D4). States: In use → Replaced · Rolled back.

**J156 · The activity credit offer has its own route.** `GET /v1/targets/activity-credit-offer` returns the newer Policy's credit when it differs from the eater's Target version, and `POST /v1/targets/activity-credit-offer/approve` creates the new Target version (`target.version.approved`). Field names: `credit_factor` (fraction) and `credit_cap_kcal` everywhere — `events.md` is aligned.

**J157 · Retention keeps role history provable.** The `audit_trail.retention_run` summary event carries `roles_held_at_anchor` (each user's roles at the anchor). The Anomalies rule "roles held with no assignment event" treats a role listed there as assigned.

**J158 · The publish carries the proposal's `asks_again`.** `POST /v1/admin/wording` for a Proposed version must send the same `asks_again` the proposal stored; a different value is 422 `VALIDATION_ERROR`, field `asks_again` (change it by proposing a new version). Reason: J154 — the flag is decided once, by the publisher, and recorded.

**J159 · Who reads Settings › Wordings.** Holders of "Publish wording" (Platform admin for consent texts and `grant-req-n`; Nutrition approver for `guidance-n`, J40) and the Auditor (read-only) may read `GET /v1/admin/wording/versions`; any other staff role gets 403 `FORBIDDEN` with `access.refused`. Reason: FR-081 least privilege; the Auditor reviews consent texts (auditor-9.3).
