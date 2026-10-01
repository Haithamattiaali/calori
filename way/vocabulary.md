# Vocabulary — the one name for every role, state and error (delta D2, 2026-10-01)

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
| **Consent** | Given · Withdrawn (each with version, time, method) | one Consent per purpose |
| **Privacy job** | Requested → Running → Completed · Failed (retried with the same id) | deletion leaves a completion record with no identifiers |

## Errors (API `code`; extends brief §18.2)
`UNIT_NOT_FOUND` · `UNIT_AMBIGUOUS` · `STALE_REVISION` · `SOURCE_BASIS_UNKNOWN` · `MASS_BALANCE_ERROR` · `MACROS_INCOMPLETE` · `PLAN_INFEASIBLE` · `AI_UNAVAILABLE` · `RATE_LIMITED` (also per-user AI quota) · `VALIDATION_ERROR` · `UNAUTHENTICATED` · `FORBIDDEN` (role lacks the permission) · `NOT_FOUND` (also for another user's ids — never reveal existence) · `CONSENT_REQUIRED` · `AGE_REQUIREMENT` · `POLICY_FLOOR` · `GRANT_REQUIRED` · `GRANT_NOT_ACTIVE` (declined, unanswered, expired, ended or withdrawn).

## Places
- iOS tabs: **Today · Capture & Plan · My Units · Progress**; screens: Analysis review, Unit editor, Meal planner, Meal review, **Settings** (Goals, Food rules, Activity, Units & language, Privacy, Export).
- Admin console sections: **Review · Foods · Recipes · Aliases · Policy · Registry · Grants · Jobs · Roles · Audit trail · Metrics · Settings** (language, launch gates).
- "Audit trail" is the only name for the record of who did what.
