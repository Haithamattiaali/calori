# Events — the one catalogue (2026-10-01)

Every event Sips & Bytes writes, with one name each (`way/join.md` J13, J14). Two kinds:

- **Audit trail events** — who did what to access, Consent, privacy, rules, reference data, the Registry and roles. Append-only, gap-free `seq`, hash-chained, read in the console's **Audit trail** (the Auditor reads all; the Platform admin reads only the change slice of J15). The seeded trail is `seed.md` §11.
- **Domain events** — what happened to one eater's diary and settings: the ledger (FR-040–FR-042), Units, Analyses, Plans, Activity, Targets. Stored per account, replayed by the projections (Day totals reproduce from them, FR-042), shown to the eater as an Entry's **history** (J22), and deleted with the account. They are not the Audit trail.

Job records (the stages of a Privacy job, retention runs, Anomalies evaluations) are rows on **Jobs**, not events; their start and end are events where this file says so. Operational logs (request id, timing, status, Registry version, estimated cost, validation code; FRD §19.2) are not events and are kept 30 days.

---

## 1 · The envelope

**Audit trail event** — every one carries: `seq` · `occurred_at` (UTC; for an eater's offline act, the device time) · `received_at` (UTC) · `actor` {`kind`: staff · eater · system, `id`, `role` held at that moment} · `action` (the name below) · `object` {`type`, `id`, `version`} · `account_id` (when an eater is involved; kept after deletion, J18) · `outcome` ∈ allowed · refused · done · failed · not_found · `code` (the D2 code when refused or failed) · `detail` (allow-listed keys only, listed per event) · `request_id` · `surface` ∈ app · console · api · job · `app_version` (app surface) · `prev_hash` · `hash` (`hash(n) = SHA-256(hash(n−1) ‖ canonical(event n))`).

**Never in any event:** food names, quantities, calories, photos, audio, transcripts, profile values (age, height, weight), safety-screen answers, eater names or emails, IP addresses (FRD §19.2). A test scans every event for these.

**Domain event** — `event_id`, `account_id`, `occurred_at`, `received_at`, `command_id` (the idempotency key), `device_id`, `app_version`, `type` (the name below), `payload`, `supersedes` (for a Correction, Void, Restore).

**Retention.** Audit trail events: 5 years from `occurred_at`, then removed by the job that writes `audit_trail.retention_run` (J18). Domain events: the life of the account; removed by the deletion Privacy job. `age.refused` carries no identifier and is kept 5 years like the trail.

---

## 2 · Audit trail events

### 2.1 · Staff sessions and refusals

| event | written when | by which request | detail fields | read by |
|---|---|---|---|---|
| `staff.signed_in` | a staff sign-in succeeds | `POST /v1/admin/session` | `method` (password + authenticator) | auditor-10.1 (live event 267 at the auditor's clock), support-9.1 |
| `staff.sign_in_failed` | a staff sign-in fails | `POST /v1/admin/session` → 401 `UNAUTHENTICATED` | `attempt` (n of 5 in 15 min) | support-9.1; seed events 189–193 |
| `staff.sign_in_locked` | the 5th failure in 15 min | system | `locked_until` (+15 min) | support-9.1; seed event 194 |
| `staff.session_ended` | sign-out, 15 min idle, or a role removed mid-session | `DELETE /v1/admin/session` or system | `reason` ∈ sign_out · idle · role_removed | support-10.21, admin-10.61; seed event 224 |
| `access.refused` | any 403 `FORBIDDEN` (the role lacks the permission), on any path: a staff token on an eater endpoint, a media request, a staff approve of a Grant, a Registry write by support, an Audit trail open by a non-Auditor, any edit of an audit event | the refused request | `path`, `permission_missing`, `attempted` (e.g. `grant.approve`), `object` | auditor-9.8, 10.6, 10.9, 10.16, 10.17; approver-10.2; admin-10.2, 10.3, 10.68; support-9.16, 10.9, 10.24; seed events 261, 262 |
| `role.change_refused` | a role change refused by a rule: self-change, the last Platform admin, separated duties, a staff role for an eater account, a seeded role | `PUT /v1/admin/users/{id}/roles`, `PUT|DELETE /v1/admin/roles/{id}` → 422 `VALIDATION_ERROR` | `rule` ∈ self_change · last_platform_admin · separated_duties · eater_account · seeded_role | admin-10.59, 10.61, 10.62, 10.64; auditor-10.25, 10.26; seed event 123 |

### 2.2 · Roles

| event | written when | by which request | detail fields | read by |
|---|---|---|---|---|
| `role.created` · `role.updated` · `role.deleted` | a custom role is saved, changed or removed | `POST|PUT|DELETE /v1/admin/roles[/{id}]` | `name`, `permissions` before → after, `reason` | admin-10.60; seed event 121 |
| `role.assigned` · `role.removed` | a role is given or taken; the deployment sets the first Platform admin (`actor: system:deployment`, `detail: "First platform admin set at deployment"`) | `PUT /v1/admin/users/{id}/roles`; start-up | `role`, `user`, `reason` | admin-10.59, 10.61; auditor-10.24, 10.25; seed events 1–6, 94, 105, 122, 147, 164 |

### 2.3 · Accounts (look-ups and support views)

| event | written when | by which request | detail fields | read by |
|---|---|---|---|---|
| `account.lookup` | any role looks up an account (support's Look up an account; the Auditor's Find account; opening one of my Active Grants) | `POST /v1/support/lookups`, `POST /v1/admin/audit-trail/lookups` | `method` ∈ support_code · email · deletion_reference · grant · account_id; `case_ref` (email) or `reason` (Auditor); outcome done · not_found | support-9.2, 9.3, 9.12, 10.21; auditor-10.34; seed events 184, 226 |
| `account.lookup_rate_limited` | the 31st email look-up by one agent in an hour | `POST /v1/support/lookups` → 429 `RATE_LIMITED` | — | support-9.3 |
| `account.viewed` | the account panel opens (no jobs tab chosen yet) | `GET /v1/support/accounts/{id}` | — | support-9.4, 9.18; seed event 185 |
| `account.jobs_viewed` | a tab opens: Privacy jobs, Sync, Failed Analyses or Activity | `GET /v1/support/accounts/{id}/{tab}` | `tab` | support-3.1, 4.1, 7.1, 9.18, 9.21; seed event 186 |

### 2.4 · Grants (J1–J11)

| event | written when | by which request | detail fields | read by |
|---|---|---|---|---|
| `grant.requested` | a Support agent sends a request | `POST /v1/grants` | `grant_id`, `reason_code`, `note` (for `other`), `days`, `areas`, `duration`, `case_ref`, `wording_version` (`grant-req-1`), the eater's language and time zone | support-10.2; auditor-10.2–10.4; eater-10.1 |
| `grant.approved` | the eater approves; Approved and Active in one transaction (no `grant.active`) | `POST /v1/grants/{id}/approve` (the eater's token) | `active_from`, `expires_at`, `method` (in_app), `device`, `command_id`, `deliveries` | eater-10.2, 10.6; support-10.6, 10.23; auditor-10.3, 10.6 |
| `grant.declined` | the eater declines | `POST /v1/grants/{id}/decline` | `method` | eater-10.3; support-10.7; auditor-10.6, 10.7 |
| `grant.unanswered` | the request window (72 h) closes with no answer | system job | `window_hours` | eater-10.4; support-10.8; auditor-10.10 |
| `grant.read` | every read under a Grant, allowed or failed | `GET /v1/grants/{id}/days/{diary_day_id}` · `/entries/{entry_id}` · `/units` · `/templates` · `/activity?diary_day_id=` | `area`, `record_type`, `record_ids`, `count_returned`; outcome allowed, or failed with `SERVICE_UNAVAILABLE` (nothing shown) | eater-10.7 (allowed only); support-10.11, 10.15, 10.25; auditor-10.3, 10.5 |
| `grant.read_refused` | a diary read refused: no Grant covers it, it is outside the Days or areas, the Grant is not Active, or it names another eater's id | the same read paths → 403 `GRANT_REQUIRED` · 403 `GRANT_NOT_ACTIVE` · 404 `NOT_FOUND` | `reason` ∈ no_grant · outside_days · outside_areas · not_active · foreign_id; `state` (when not Active) | support-10.1, 10.7, 10.12, 10.17; auditor-10.7–10.10, 10.41; eater-10.3, 10.8, 10.9 |
| `grant.write_refused` | a staff identity carrying a Grant tries to write | any write path → 403 `FORBIDDEN` | `path` | support-10.13; auditor-10.13; seed events 234–238 |
| `grant.expired` | `expires_at` passes (written when the scheduler runs; every read checks `expires_at` itself) | system job | — | eater-10.9; support-10.17; auditor-10.8 |
| `grant.ended` | the requesting Support agent ends an Active Grant, or cancels a Requested one (J3) | `POST /v1/grants/{id}/end` | `when_state` ∈ active · requested (`cancelled_before_answer`) | eater-10.10; support-10.19, 10.25; auditor-10.12 |
| `grant.withdrawn` | the eater withdraws, or the account's deletion withdraws it | `POST /v1/grants/{id}/withdraw`; deletion job | `reason` ∈ eater · account_deletion | eater-10.8, 9.19; support-10.18; auditor-10.11 |

### 2.5 · Age and Consent (J23–J32)

| event | written when | by which request | detail fields | read by |
|---|---|---|---|---|
| `age.confirmed` | the age gate passes; the record is filed with the account or anonymous session when one exists | `POST /v1/age-gate` | `text_version` (`age-1`), `method` (onboarding_age_question), `made_at` | eater-1.1; auditor-9.2, 9.9 |
| `age.refused` | an age under 18 | `POST /v1/age-gate` → 422 `AGE_REQUIREMENT` | none — no account, device, IP or age is kept | eater-1.2; auditor-9.9 |
| `consent.given` · `consent.withdrawn` | the eater decides one purpose (one record per purpose; a repeat delivery of one command writes one event) | `POST /v1/me/consents` | `purpose` (J23 keys), `text_version`, `method` (J24), `context`, `made_at` (device), `received_at`, `command_id` | eater-1.3–1.6, 9.1–9.6, 4.3–4.6; auditor-9.1–9.7, 9.17; support-9.6 |

### 2.6 · Privacy jobs and other jobs

| event | written when | by which request | detail fields | read by |
|---|---|---|---|---|
| `privacy_job.requested` | an export or deletion is requested; a Consent withdrawal starts its job; the Platform admin starts one for a verified outside request (J43) | `POST /v1/privacy/export-or-delete`; `POST /v1/me/consents` (withdrawal); `POST /v1/admin/outside-requests/{id}/act` | `kind` ∈ export · deletion · consent_withdrawal; `job_id`; `reference` (deletion); `due_by`; `requested_by` ∈ eater · staff; `channel`; `verified_by` | eater-9.14, 9.18; support-9.7–9.10; auditor-9.10–9.14; admin-9.1 |
| `privacy_job.completed` | the job completes | job | export: `size`, category counts, `window_until`; deletion: the completion record id (the record holds no identifier); consent withdrawal: the effect counts | eater-9.21; support-9.8, 9.12; auditor-9.5, 9.11, 9.14 |
| `privacy_job.failed` | the job reaches Failed after its automatic attempts | job | `stage`, `failure_reason` (J95), `attempts` (n of 5) | admin-9.1; support-9.9, 9.11; auditor-9.12 |
| `privacy_job.export_downloaded` | the eater downloads an export | `GET /v1/privacy/jobs/{id}/file` | `count` | auditor-9.14 |
| `job.retried` | a Support agent (an export, once) or the Platform admin retries a job under the same id | `POST /v1/admin/jobs/{id}/retry` | `job_kind`, `attempt` (n of 5) | admin-9.2, 9.3, 10.56; support-9.9 |
| `job.escalated` | a Support agent escalates a job to the Platform admin | `POST /v1/admin/jobs/{id}/escalate` | `case_ref` | support-9.11; admin Jobs › Escalated |
| `job.escalation_resolved` | the escalated stage completes | system | — | support-9.11 |
| `outside_request.recorded` · `outside_request.escalated` · `outside_request.closed` | support records a privacy request received by email, chat or phone; escalates it; it closes (by a linked in-app job or the Platform admin's action) | `POST|PATCH /v1/support/outside-requests[/{id}]` | `type`, `channel`, `received_at`, `case_ref`, `due_by`, `outcome` (≤ 120 characters, no content) | support-9.14, 9.15 |

### 2.7 · Policy, wording and launch gates

| event | written when | by which request | detail fields | read by |
|---|---|---|---|---|
| `policy.version.proposed` | a Nutrition approver saves a Proposed version | `POST /v1/admin/policy/versions` | `version`, `based_on`, changed values | approver-10.49–10.61; auditor-10.18 |
| `policy.version.approved` | a version is approved | `POST /v1/admin/policy/versions/{v}/approve` | `proposer`, `approver`, `sole_holder` (true when the role had one holder), `reason`, `effective_from` | approver-10.57; auditor-10.18 |
| `policy.version.in_effect` | `effective_from` arrives | system | `supersedes` | auditor-10.19; seed events 24, 266 |
| `wording.proposed` | a new Wording version is stored as Proposed (J153) | `POST /v1/admin/wording/proposals` | `key`, `version`, languages, `asks_again` | admin-10.73 |
| `wording.published` | a consent text, the Grant request wording or the tracking-only guidance is published | `POST /v1/admin/wording` | `key`, `version`, languages, `asks_again` (J154) | auditor-9.3, 10.4; seed event 145 |
| `launch_gate.signed` | the nutrition-policy review or the privacy review is signed | `POST /v1/admin/launch-gates/{gate}/sign` | `gate`, `signer`, `version_reviewed` | approver-10.62; seed event 144 |
| `launch_gate.recorded` | the credential is loaded, a restore test runs, provider data settings are recorded, the dependency audit runs | system or `POST /v1/admin/launch-gates/{gate}` | `gate`, `result` | admin-10.12, 10.65–10.67 |

### 2.8 · Reference data

| event | written when | by which request | detail fields | read by |
|---|---|---|---|---|
| `food.version.proposed` · `food.version.claimed` · `food.version.approved` · `food.version.rejected` · `food.version.retired` | a Food or Tier B recipe record version moves (a Label submission is a Proposed Food with `origin: label_submission`) | `POST /v1/admin/foods|recipes[/{id}/versions/{v}/approve|reject|retire|claim]`; `POST /v1/label-submissions` (proposed) | `kind` ∈ food · tier_b_recipe_record; `version`; `licence`; `evidence_ids`; `origin`; `reason` | approver-10.9, 10.15, 10.28–10.39, 10.63; auditor-10.22; seed events 111, 112 |
| `alias.proposed` · `alias.approved` · `alias.rejected` · `alias.retired` | an Alias moves | `POST /v1/admin/aliases[/{id}/…]` | `text`, `dialect`, `target`, `reason` | approver-10.41–10.47; auditor-10.23; seed events 113–118 |
| `flag.closed` | an approver closes a flag | `POST /v1/admin/flags/{id}/close` | `decision` ∈ keep_analogue · keep_label_value · create_food · replace · not_applicable; `reason` | approver-10.9–10.18 |
| `usda_release.imported` · `usda_release.import_failed` | a USDA release import finishes or stops | the import job | `release`, `new`, `changed`, `removed`, `stopped_at` | approver-10.22, 10.23 |

### 2.9 · Registry, quotas and cost

| event | written when | by which request | detail fields | read by |
|---|---|---|---|---|
| `registry.version.proposed` | a Registry version is proposed | `POST /v1/admin/registry/{task}/versions` | `task`, `version`, `model`, `prompt_version`, `schema_version`, `reason` | admin-10.8–10.12; auditor-10.20; seed event 142 |
| `registry.evaluation.finished` · `registry.evaluation.cancelled` | an offline evaluation run ends | the evaluation job | `run_id`, scores per check, pass or fail | admin-10.15–10.17 |
| `registry.stage.changed` | a version moves: Shadow, Canary (share), Rollout, Replaced, Rolled back (by hand or automatically) | `POST /v1/admin/registry/{task}/versions/{v}/move|rollback` | `from`, `to`, `share`, `auto`, `reason` | admin-10.18–10.30; auditor-10.20; seed events 9–15, 143, 146, 150, 155 |
| `registry.kill_switch.on` · `registry.kill_switch.off` | a task's kill switch changes | `PUT /v1/admin/registry/{task}/kill-switch`; the spend cap (system) | `task`, `reason` (or "reason not given"), `cause` ∈ manual · spend_cap, `minutes_on` (on Off) | admin-10.31–10.37, 10.48; auditor-10.21; support-10.24; seed events 153, 154, 187, 195 |
| `quotas.version.saved` · `quotas.version.rolled_back` | quotas change or roll back | `PUT /v1/admin/quotas`, `POST /v1/admin/quotas/rollback` | before → after, `reason` | admin-10.38, 10.39; seed event 16 |
| `price.added` | a price row is added (rows in effect never change) | `POST /v1/admin/prices` | `model`, `input`, `output`, `effective_from`, `source` | admin-10.45; seed events 17–19 |
| `spend_cap.saved` · `spend.alert` | the spend cap is set; the day's estimated cost passes the alert level | `PUT /v1/admin/spend-cap`; system | `alert`, `cap`; `estimated_today` | admin-10.48; seed event 20 |
| `regression_case.added` · `regression_case.removed` · `evaluation_case.viewed` | a consented case is added or removed (research Consent withdrawn); a custom-role holder opens one | the regression-set job; `GET /v1/admin/regression-set/cases/{n}` | `case_number` (random) | admin-10.13, 10.14; auditor Anomalies (J28) |
| `grant_settings.version.saved` | Grant settings change | `PUT /v1/admin/grant-settings` | before → after, `reason` | auditor (J2); seed event 21 |

### 2.10 · The Auditor's own work

| event | written when | by which request | detail fields | read by |
|---|---|---|---|---|
| `audit_trail.queried` | an Audit trail view or filter loads (written before its results are read) | `GET /v1/admin/audit-trail/events` and the views | `filters` | auditor-10.1, 10.27–10.37 |
| `audit_trail.exported` | an extract is exported | `POST /v1/admin/audit-trail/exports` | `filters`, `rows`, `sha256` | auditor-9.16, 9.17, 10.35 |
| `audit_trail.verified` | the hash chain is checked (by the Auditor or the automated check) | `POST /v1/admin/audit-trail/verify` | `range`, `result`, `first_break` | auditor-10.15 |
| `audit_trail.review_noted` | the Auditor appends a review note (J17) | `POST /v1/admin/audit-trail/review-notes` | `period`, `scope`, `finding`, `note` (≤ 500 characters, no eater identifier) | auditor (A-3) |
| `audit_trail.retention_run` | events older than 5 years are removed | the retention job | `removed_through_seq`, `anchor_hash`, `roles_held_at_anchor` (J157) | auditor (J18) |

---

## 3 · Domain events (per account)

| event | written when | by which request | payload | read by |
|---|---|---|---|---|
| `entry.confirmed` | the server accepts a consume command (from a tap, quick-add, a copy, a Template, Siri, the widget, an approved Analysis or a confirmed Plan) | `POST /v1/consumption` | `entry_id`, `diary_day_id`, `eaten_at`, `time_zone`, `meal_name`, `unit_version_id`, `count`, `snapshot` (component nutrients, unrounded), `source_versions`, `evidence`, `value_basis`, `source_plan_id`, `source_analysis_id` | e36 3.3–3.26; e578 5.29; FR-040 |
| `entry.corrected` | a Correction replaces the effective Entry (quantity, Unit version, `eaten_at` or Day; a move is `operation: move`) | `POST /v1/consumption/{id}/corrections` | `supersedes`, `operation` ∈ correct · move, old · new · delta, `scope` ∈ this_entry · future_default, `expected_entry_version` | e36 6.3–6.12, 6.17, 6.18; AT-11, AT-14 |
| `entry.voided` · `entry.restored` | a Void or Restore | `POST /v1/consumption/{id}/void` · `/restore` | `supersedes`, `expected_entry_version` | e36 6.14, 6.15, 3.5 |
| `command.duplicate_ignored` | the same `command_id` arrives again | any command path | `deliveries` | support Sync tab ("Duplicates ignored"); AT-10 |
| `command.conflict` | a command is refused with `STALE_REVISION` (status Conflict, J69) | correct, void, restore, move paths | `current_revision` | e36 6.22; support Sync tab |
| `day.revised` | a Day's projection changes (one per accepted command, in the same transaction) | projection | `day_revision`, totals, coverage | FR-042; every `GET /v1/reports/day` |
| `day.started` · `day.marked` | "Start new day"; the eater marks a Day Complete or Partial | `POST /v1/days`; `PUT /v1/days/{diary_day_id}/mark` | `diary_day_id`; `mark`, `expected_revision` | e36 3.32; e578 8.13 |
| `unit.version.saved` · `unit.archived` · `unit.unarchived` | a Unit, Composite or Recipe version is saved; archived; unarchived | `POST /v1/units`, `/v1/units/{id}/versions`, `/v1/recipes`; `/archive` · `/unarchive` | `structure`, `unit_kind`, `version`, components, `measurement_evidence`, `evidence` | e24 2.x; e36 3.8 |
| `template.saved` · `template.deleted` | a Template is saved or deleted (Unit ids and counts only) | `POST|DELETE /v1/templates` | `name`, items | e36 3.19, 3.20 |
| `rule.version.saved` | a Food rule (accompaniment, preparation default) version is saved | `PUT /v1/rules` | `scope`, `predicate`, `default`, `effective_from` | e24 2.22–2.27 |
| `analysis.created` · `analysis.state_changed` · `analysis.question_answered` | an Analysis is created (Processing, or Pending offline) and moves through its states; a question is answered | `POST /v1/analyses`; `GET /v1/analyses/{id}` follow-ups | Registry stamp (`registry_version`, `prompt_version`, `schema_version`, nutrition algorithm and source versions), `state`, `code` (when Failed), `retry_of` | e24 4.x; admin-10.28 |
| `plan.proposed` · `plan.saved` · `plan.confirmed` · `plan.not_eaten` · `plan.reopened` · `plan.expired` | a Plan is computed (Proposed or Infeasible), saved, confirmed (`confirmed_as`), marked Not eaten, undone back to Saved (J68), or expires at the end of its Day | `POST /v1/meal-plans`, `/save`, `/not-eaten`; `POST /v1/consumption` with `source_plan_id` | `state`, `solution_status`, `blocking`, `changes`, `selected_versions`, entry ids | e578 5.x |
| `activity.import_reconciled` | an import batch is reconciled | `POST /v1/activity/import` | accepted · updated · duplicate · conflict counts | e578 7.4–7.9; support-7.1 |
| `activity.confirmed` · `activity.corrected` · `activity.linked` · `activity.voided` · `activity.restored` | an Activity is added (imported or by hand), corrected, linked to a matching import, voided, restored | `POST /v1/activity`, `/corrections`, `/link`, `/void`, `/restore` | `provider_record_id`, `origin`, `interval`, `energy_basis`, `kcal`, `import_revision`, `override` | e578 7.10–7.15 |
| `weight.recorded` · `weight.excluded` | a weight is imported or typed; the eater excludes an unusual one from the trend | `POST /v1/weights`; `PATCH /v1/weights/{id}` | `kg`, `source`, `outlier_state` | e578 8.19–8.22 |
| `health.sample_written` · `health.sample_rewritten` · `health.sample_deleted` | the confirming iPhone reports its Apple Health write (J123) | the app's sync | `entry_id`, `sample_id`, `device_id` | e36 3.39, 3.40, 6.24 |
| `target.version.approved` | a Target version is approved | `POST /v1/targets` · `POST /v1/targets/activity-credit-offer/approve` (J156) | `source`, `input_snapshot`, `policy_version`, `effective_from`, `activity_mode`, `credit_factor`, `credit_cap_kcal`, `over_deficit_cap` | e19 1.42; e578 7.18, 8.23; approver-10.69 |
| `target.suggestion.created` · `target.suggestion.accepted` · `target.suggestion.kept` | a Suggested Target (P1) is offered, accepted or declined | `GET /v1/targets/suggestions`, `/accept` | `change_kcal`, `reason`, `review_period` | e578 8.31 |
| `safety_mode.set` | the safety screen sets a mode (never the answers) | `PUT /v1/me/safety-mode` | `mode`, `screen_version` | e19 1.16–1.21 |
| `settings.changed` | a setting changes (language, numerals, dialect, units, boundary with `effective_from`, Ramadan days, one-tap logging, Hide numbers, first day of week) | `PATCH /v1/me/settings` | `key`, `old`, `new`, `effective_from` | e36 3.28–3.30; e19 1.x |
| `support_code.issued` | the eater opens Settings → Privacy → Support code and a code is issued (valid 24 h) | `POST /v1/me/support-code` | `valid_until` | eater-9.23; support-9.2 |

---

## 4 · Renamed — one name per event

| lens name | catalogue name |
|---|---|
| `grant.read_denied` (support) | `grant.read_refused` |
| `grant.active` (support) | none — part of `grant.approved` (J1) |
| `grant.approve_denied` (support) | `access.refused` with `attempted: grant.approve` |
| `support.lookup` (support) · `account.lookup` (auditor) | `account.lookup` |
| `support.lookup_rate_limited` · `support.account_viewed` · `support.jobs_viewed` | `account.lookup_rate_limited` · `account.viewed` · `account.jobs_viewed` |
| `support.job_retried` · `support.escalated` | `job.retried` · `job.escalated` |
| `support.request_logged` | `outside_request.recorded` |
| `privacy_job.export.requested|completed` · `privacy_job.deletion.requested|completed` (auditor) | `privacy_job.requested` · `privacy_job.completed` with `kind` |
| `policy.version.proposed|approved`, `food.version.proposed|approved`, `alias.proposed|approved`, `registry.kill_switch.on|off`, `role.assigned|removed` (auditor shorthand) | one event per verb, as listed above |
| auditor event 35's `access.refused` for a self-change | `role.change_refused` (J36) |
| outcome words `found` · `denied` · `error` (support) | `done` · `refused` · `failed` |
