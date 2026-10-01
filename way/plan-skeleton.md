# Plan skeleton for Phase 5 (the session's slice outline, 2026-10-01)

Input for the planner who writes `blueprint.md` §2. Every story id (629: the lenses + `personas/added-stories.md`) lands in exactly one slice or on the drop list. Slices follow `model.md` §3 modules and the dependency of journeys. Each slice: stories, workflow steps, modules, contracts (operation ids in `contracts/openapi.yaml`), depends-on, lane, approach (one line), surfaces (API · iOS · console), experience line for each screen (moment, feeling, primary action, doors, care questions).

## Lanes (each a worktree with its own port and emulator project id)
- **L-core** — API foundation, ledger, nutrition_core, reports (port 8101, project `sips-core`)
- **L-ios** — SipsCore package + SipsApp screens (CI macOS; local `swift test`)
- **L-ref** — reference, units, recipes, approver console (port 8102, `sips-ref`)
- **L-ai** — Analyzer, capture, registry, quotas (port 8103, `sips-ai`)
- **L-plan** — planner, activity, HealthKit (port 8104, `sips-plan`)
- **L-gov** — privacy, Grants, support, auditor, policy, roles console (port 8105, `sips-gov`)

## Outline (MVP marked ★ — FRD §23.1: "save a bread-inclusive bite, log it offline, sync once, correct its count, and reproduce the exact day total"; plus the approver's core: approve a Tier B recipe record the eater's resolver uses)
- ★ S00 Foundation — monorepo (A22), `scripts/check.sh`, CI (Linux job: API + console + Firestore/Auth emulators; macOS job: SipsCore tests + SipsApp build + simulator walk), health, reset, seed loader (seed.md with start clock), identity (eater + staff), roles seeded (A10), configuration read (Policy v1, Registry v1), feel kits, iOS shell with the four tabs and Today empty state + its primary action, console shell with sign-in, English + Arabic from the first screen, Dockerfile, `.env.example`, allow-list logger, Audit trail store.
- ★ S01 nutrition_core — pure package: units, composites, accompaniment rules, recipes and yields, 4/4/9 shares with largest-remainder display, Mifflin–St Jeor, maintenance, target and macro conversion, normalisation; golden cases AT-01…AT-09, AT-15, AT-16 and seed values.
- ★ S02 Reference — Foods (Tier A subset from seed), search, resolver order, Food versions and states; approver Review/Foods/Recipes/Aliases minimal: approve a Tier B recipe record with evidence and licence.
- ★ S03 Units — Unit editor (simple, Composite with bread rule, Recipe with weigh-the-pot), versions, My Units, user rules; API + iOS.
- ★ S04 Log — consume command, Entry events, Day projection in one transaction, idempotency, meal + day report, Today timeline, quick-add with recent Units and count stepper, Undo.
- ★ S05 Offline — SipsCore outbox on GRDB, Pending vs Confirmed totals, one sync, conflict (STALE_REVISION), widget command files later.
- ★ S06 Correct — correct, void, restore, move Day, correction preview, scope "this entry / future default", Entry history.
- ★ S07 Onboard and Target — age gate, Consents, profile, safety screen, resting energy, maintenance, Target with Policy floor, macros and locks, activity mode, Target versions.
- ★ S08 Reports — Day report, 7/28/custom periods, coverage, Progress, Target history.
- S09 Capture and analyse — Analyzer port, FixtureAnalyzer default, Gemini adapter behind the Registry, photo + words, label, scale, voice (server transcription), ≤2 questions, intent, untrusted text, Analysis review, App Check.
- S10 Registry, quotas, prices, kill switch, metrics — Platform admin console.
- S11 Templates, copy, Siri, widget — App Intents, interactive widget (JSON command files), copy Meal/Day.
- S12 Planner — OR-Tools CP-SAT, Meal planner, Meal review, Plan states, expiry (eater-5.44).
- S13 Activity — HealthKit read, manual exercise, dedupe/link, activity modes, credit offer (eater-7.25).
- S14 Health write — food correlations, rewrite on correction, delete on void.
- S15 Privacy — Consent screens and withdrawal, Wordings (admin-10.77), export job, delete account job, retention jobs.
- S16 Grants — support request, eater approves/declines/withdraws, read-only Diary within the Grant, expiry, Grant settings (admin-10.76).
- S17 Support console — look-up, account panel, Jobs tabs, failed Analyses, Sync, Requests received outside the app, escalation.
- S18 Auditor console — Audit trail Events, Anomalies, Consents, Summary, review notes (auditor-10.42), retention run (auditor-10.43), records of processing, exports.
- S19 Policy and approver depth — Policy versions (floors, caps, credit, retention), Label submissions, Flags, Cross-checks, USDA release import.
- S20 Roles and duties — permissions → roles → users editor, separation of duties, last-admin rule.
- S21 Platform obligations (D1) — schema versions with forward migrations rehearsed on a seeded copy, dependency audit + SBOM in CI, backup export/restore drill on the emulator, launch gates page.
- Care pass — one slice per workflow's screens (C01…C10), last before the release proof.
- whole-app · pipeline (in S00) · ship/go-live/operate/measure dropped (§0 line 10).
