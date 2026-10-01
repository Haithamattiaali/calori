# Vocabulary — the one name for every role, state and error (deltas D2, D3, D4 — 2026-10-01)

Extends `blueprint.md` §1 ¶4. Every lens, contract, screen, log line and test uses exactly these words. A word not here and not in §1 ¶4 is added by a dated delta first. Arabic labels come from the string catalogue, one per English word.

## Roles (permission sets; users hold roles)
**Eater** · **Nutrition approver** · **Support agent** · **Platform admin** · **Auditor**. Deny by default; a staff account is never an eater account.

## States

| thing | states, in order | notes |
|---|---|---|
| **Entry** | Pending → Confirmed; Confirmed → Corrected (a newer version replaces it) · Voided → Restored | Pending = queued on the device, not yet accepted by the server; the day total shows Pending separately |
| **Day** | Provisional (today) · Complete · Partial · Unlogged | Complete/Partial are the eater's own mark (brief FR-073) |
| **Analysis** | Processing → Needs answers → Ready for review → Approved · Discarded · Failed; Pending (captured offline, not sent) | an Analysis never adds calories; only the command it leads to does |
| **Plan** | Proposed · Infeasible → Saved → Confirmed (Ate as planned · Changed) · Not eaten | a Plan has no consumed calories until Confirmed |
| **Unit / Composite / Recipe** | Draft → Saved (version n) → Archived | "Draft" is used only here; editing a Saved version creates version n+1 |
| **Food / Tier B recipe record / Alias** (reference) | Proposed → In review → Approved · Rejected; Approved → Superseded (a newer approved version) · Retired (not used for new resolutions; existing snapshots unchanged) | Tier A rows arrive from a **USDA release** (e.g. "USDA release 15.5") already Approved with licence CC0 |
| **Policy version** | Proposed → Approved (with effective-from) → In effect → Superseded | one approver may approve; the audit trail records who proposed and who approved (two people when two exist; the same person is allowed only when the role has one holder, and the trail says so) |
| **Registry version** (model id + prompt + schema per task) | Proposed → Shadow → Canary → Rollout · Rolled back | the **Kill switch** per task is On/Off; when On, AI requests for that task fail fast with `AI_UNAVAILABLE` and nothing is queued to send later |
| **Grant** (just-in-time diary access) | Requested → Approved → Active → Expired · Ended (by the support agent) · Withdrawn (by the eater); Requested → Declined · Unanswered (no answer before the request window closes) | reads are allowed only while Active; writes never |
| **Consent** | Not given → Given · Withdrawn (each change with version, time, method) | one Consent per purpose; "Not given" is the state before the eater has decided (delta D3) |
| **Privacy job** | Requested → Running → Completed · Failed (retried with the same id) | deletion leaves a completion record with no identifiers |

## Errors (API `code`; extends brief §18.2)
`UNIT_NOT_FOUND` · `UNIT_AMBIGUOUS` · `STALE_REVISION` · `SOURCE_BASIS_UNKNOWN` · `MASS_BALANCE_ERROR` · `MACROS_INCOMPLETE` · `PLAN_INFEASIBLE` · `AI_UNAVAILABLE` · `RATE_LIMITED` (also per-user AI quota) · `VALIDATION_ERROR` · `UNAUTHENTICATED` · `FORBIDDEN` (role lacks the permission) · `NOT_FOUND` (also for another user's ids — never reveal existence) · `CONSENT_REQUIRED` · `AGE_REQUIREMENT` · `POLICY_FLOOR` · `GRANT_REQUIRED` · `GRANT_NOT_ACTIVE` (declined, unanswered, expired, ended or withdrawn).

## Places
- iOS tabs: **Today · Capture & Plan · My Units · Progress**; screens: Analysis review, Unit editor, Meal planner, Meal review, **Settings** (Goals, Food rules, Activity, Units & language, Privacy, Export).
- Admin console sections: **Review · Foods · Recipes · Aliases · Policy · Registry · Grants · Jobs · Roles · Audit trail · Metrics · Settings** (language, launch gates).
- "Audit trail" is the only name for the record of who did what.

## Delta D4 (2026-10-01) — the join's names (from `way/join.md` §21, applied by the session)
**2026-10-01 · delta D4 · the join's names.** Extends `way/vocabulary.md` (D2, D3). Reason: the model-phase join (`way/join.md`) settled the lenses' conflicts; each word below is used by a decision and by `seed.md` or `events.md`. Impact: every contract, screen, log line and test uses these words; the lens files are read through `join.md` until next edited; nothing is banked yet.

- **Things:** **Meal** (the Entries with one meal name on one Day; «وجبة») · **Flag** (Estimated analogue · Energy mismatch · Unmatched name · Ingredient updated; Open · Closed) · **Label submission** · **value basis** (measured · declared · estimated, always "amount …") · **carbohydrate convention** (total · available) · **Cross-check** · **Calorie aim** · **carbohydrate target** · **Suggested Target** · **daily AI quota** · **Quotas version** · **AI spend cap** and its **alert level** · **Canary check** · **roll-back target** · **regression set**, **regression case**, **evaluation run** (Evaluating · Finished · Cancelled) · **Grant settings version** · **Grant areas** (Entries and day reports · My Units · Templates · Activity) · **reason catalogue** · **support code** · **deletion reference** · **case reference** · **completion record** · **Request received outside the app** · **Wording** (a versioned consent or request text) · **Unit-name match** · **Health workout** · **active energy** · **credit factor** · **credit cap** · **Weight** (an observation; "Unusual — check") · **Entry history** · **review note** · **AI tasks** Meal · Label · Scale · Ingredients · Text · Voice · Explain · **capture modes** Meal · Unit · Label · Recipe.
- **States (additions):** Unit / Composite / Recipe: Archived → Saved ("Unarchive") · Registry version: Rollout → **Replaced** · Grant: Requested → Ended (cancelled by the Support agent) · Plan: Confirmed → Saved and Not eaten → Saved (Undo) · **Activity**: Pending → Confirmed; Confirmed → Corrected · Voided → Restored · **Label submission**: Proposed → In review → Approved · Rejected · **Flag**: Open → Closed · **Request received outside the app**: Open → Escalated → Closed · **Quotas version** and **Grant settings version**: In use · Rolled back · outbox command **status** (device only): Queued → Sent → Accepted · Conflict · other jobs (Analysis, import, retention, USDA release): as Privacy job.
- **Error:** `SERVICE_UNAVAILABLE` (503: a non-AI dependency failed; nothing was done; retry with the same id).
- **Audit outcomes:** Allowed · Refused · Done · Failed · Not found.
- **Places (app):** Onboarding · Age, · Under 18, · Consents, · Account, · Profile, · Safety screen, · Energy, · Target, · Macros, · Activity mode, · Review · the account line · Settings → Privacy → Grants · Support code · Delete account · Day picker · timeline · Entry details · Day report · Activity sheet · quick-add · count stepper · correction preview · Source details · My Units → Templates · Progress → Weight · Target history.
- **Places (console):** Registry › <task> (models list, prompt editor, regression set, quotas panel) · Metrics › cost view · prices panel · Jobs › Look up an account · account panel · Privacy help · Privacy jobs · Failed Analyses · Sync · Activity · Requests received outside the app · Escalated · Retention · Grants › Grant form · Grant panel · Grant bar · Diary (read-only) · Grants list · Review › flags · Label submissions · Audit trail › Events · Anomalies · Consents · Summary · Records of processing · Exports (action: Find account) · Settings › launch gates · Grant settings.

## Delta D6 (2026-10-01) — from `join.md` §22
- **Wording** (a versioned consent or request text): Proposed → Published · Superseded; flag `asks_again`. Console place: **Settings › Wordings**.
- **Grant settings version**: In use → Replaced · Rolled back.
- **Plan**: a Saved Plan whose Day has ended stays **Saved** with `expired_at`; copy "not logged"; its card lives on **Today**.
- Field names: `credit_factor` (decimal fraction), `credit_cap_kcal`.
