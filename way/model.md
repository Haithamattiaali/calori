# Model — interactions, data model, decisions, contracts, the dependency map

Written at the model phase, 2026-10-01. Reads `blueprint.md` §0–§3, `vocabulary.md`, `join.md` (the cross-lens decisions), `seed.md` (the one fixture set), `events.md` (the one event catalogue), the lenses in `personas/`, and research cycles 1–3 (`research/`). C1 profile: no scouts, no variation matrix (customization.md: C2 only).

## §1 Interactions on the platform

(written from the lenses and the join — see below)

## §2 The data model

(written from the interaction tables)

## §3 Modules and boundaries

(written with §1–§2)

## §4 Architecture decisions — options → winner → why

Each line names its research. Versions are those in `research/sdks.md` (research cycle 3, opened 2026-10-01); a new dependency later is a dated delta.

| # | decision | options | winner | why |
|---|---|---|---|---|
| A1 | Shape | modular monolith · services per bounded context | **modular monolith** (one FastAPI app, one module per bounded context, each owning its collections, services and routes; cross-module calls through declared Python interfaces) | no measured reason (scale, teams, release pace) to split; contracts between modules from day one keep a later split cheap (system.md) |
| A2 | API | REST per brief §18 · GraphQL · gRPC | **REST, versioned `/v1`, contract-first OpenAPI 3.1 file in `contracts/openapi.yaml`** that the server implements and both clients are generated or validated from | brief §18 lists REST endpoints; FastAPI emits OpenAPI 3.1 (sdks N5); Apple's swift-openapi-generator reads 3.1 (sdks) |
| A3 | Runtime | Python 3.12 · 3.13 | **Python 3.13** (FastAPI 0.142.2, Pydantic 2.13.5, Uvicorn 0.54.0, Starlette ≥1.3.1 floor) | supported to 2029; every chosen package has 3.13 wheels (sdks) |
| A4 | Persistence | Firestore · Postgres | **Firestore** (google-cloud-firestore 2.33.0) behind one repository interface per module; **Firestore emulator** (firebase-tools 15.32.1, Java 21) in dev, tests and CI | brief §15.1 hard limit; transactions for the ledger (brief FR-042, S08); the emulator gives real transaction semantics without a cloud project |
| A5 | Ledger | mutable rows · append-only events + projections | **append-only Entry events + effective-entry projection + Day projection, written in one Firestore transaction with the day revision; idempotency by `command_id`** | brief FR-040…FR-043, §17.1; replay reproduces totals (FR-042) |
| A6 | Nutrition core | in the API module · a pure package | **pure Python package `nutrition_core`** (no I/O: conversions, recipe yield, composite expansion, 4/4/9 shares with largest-remainder display, Mifflin–St Jeor, target math), decimals not floats | brief §6.1, §10, §11, NFR-01 golden cases; deterministic and testable alone |
| A7 | Planner | hand-rolled search · OR-Tools CP-SAT · MIP | **OR-Tools CP-SAT 9.15** with integer scaling of masses and nutrients; results re-checked unrounded by `nutrition_core` | brief §9.1; S10/S11; the solver verifies feasibility, not the model |
| A8 | Identity | Firebase Auth · own accounts | **Firebase Auth** (Sign in with Apple, email link; anonymous for the local trial's cloud calls) verified server-side with firebase-admin 7.7.0; **App Check** verified on AI endpoints with limited-use tokens and `already_consumed` rejected | brief §15.1; P31/P41 as corrected in r1-refute-b |
| A9 | Staff access | same tokens as eaters · separate staff accounts | **separate staff accounts** (Firebase Auth users with a staff flag; roles from our RBAC store), console sessions by secure cookie + CSRF | vocabulary D2 ("a staff account is never an eater account"); option-console.md |
| A10 | RBAC | roles in token claims · permissions → roles → users in our store | **permissions → roles → users in Firestore, deny by default, checked at the service layer and reflected in the UI**; five seeded read-only roles | blueprint §0 line 3; floor.md access line; FR-081 |
| A11 | AI | direct client calls · server adapter | **server-side `Analyzer` interface** with `GeminiAnalyzer` (google-genai 2.26.0, `generateContent`, `response_json_schema` from Pydantic, `store=False`, frozen model ids from the Registry) and **`FixtureAnalyzer`** (deterministic realistic outputs + injected failures) as the default until cutover | brief §15.2, §16; P5/P8/P12 as corrected; the floor's adapters line; no API key in the repo |
| A12 | Nutrition sources | live USDA API · bulk import | **bulk import of USDA FoodData Central (Foundation, SR Legacy, FNDDS) into the reference store with licence CC0**; a curated subset in the seed for dev/tests; Tier B regional recipe records curated by the approver; Open Food Facts not in v1 | F1–F5, F8 (ODbL kept apart), r1-food-sources implication 1 |
| A13 | Admin console | server-rendered · SPA | **server-rendered Jinja2 3.1.6 + htmx 2.0.11 from the same FastAPI app**, a thin adapter over the same module interfaces the `/v1/admin` API uses; CSS token kit; `dir="rtl"` for Arabic | `research/option-console.md` |
| A14 | iOS app | SwiftUI · UIKit | **SwiftUI, iOS 26.0 minimum, Swift 6 language mode**, built on GitHub `macos-26` with Xcode 26.6 (Swift 6.3) — code stays within Swift 6.3 | P16–P19; sdks (Xcode row) |
| A15 | iOS local store | GRDB · SwiftData · Core Data | **(pending `research/option-ios-store.md`)** | — |
| A16 | iOS code split | one app target · core package + app | **`SipsCore` SwiftPM package** (platform-neutral: models, outbox, pending-total arithmetic, API client generated by swift-openapi-generator 1.13.1) tested with `swift test` on Linux (Swift 6.4 here) and macOS; **`SipsApp`** (SwiftUI, HealthKit, App Intents, widgets, Firebase iOS SDK 12.19.2 via SPM) built and walked on the simulator in CI | blueprint §0 environment facts; sdks |
| A17 | Voice | on-device · server | **server-side transcription through the Analyzer** (Arabic and code-switching); on-device only as an opportunistic English path, never required | P25–P28 as corrected (no on-device Arabic) |
| A18 | HealthKit | read only · read + write | **read** workouts, active energy, body mass; **write** each confirmed Entry as a food correlation, keeping sample ids on the Entry to delete and rewrite on correction or void | P29, P30 |
| A19 | Jobs | inline · queue | **a job queue interface** (`JobQueue`) with an in-process worker in dev/tests and a Cloud Tasks adapter later (ship rows dropped); export, deletion, retention purge, USDA import run as jobs with the same id on retry | brief §15.1 operations row; §0 line 6 |
| A20 | Observability | free logs · structured allow-list | **structured JSON logs through an allow-list formatter** (request id, route, status, duration, model version, cost, validation code), one request id end to end; the Audit trail is a separate append-only store | brief §19.2; admin-10.65, eater-9.13 |
| A21 | Feel kit | per screen · one kit | **one kit per surface**: SwiftUI tokens (type, spacing, colour, motion; Dynamic Type; RTL) and console CSS tokens; every value chosen on the served product (care.md) | floor.md experience law 5 |
| A22 | Repo layout | many repos · monorepo | **monorepo**: `api/` (FastAPI app + modules), `packages/nutrition_core/`, `console/` (templates, static), `ios/SipsCore/`, `ios/SipsApp/`, `contracts/`, `seed/`, `scripts/check.sh` (the one check script the landing queue and CI share), `.github/workflows/` | ship.md "one script for both" |
| A23 | Hosting | Cloud Run · none yet | **none yet** (§0 line 10); the app runs anywhere a container runs (a Dockerfile from the first slice) and reads settings from the environment | ship rows dropped with their risk |

## §5 Contracts

(written after §1–§3: `contracts/openapi.yaml`, `contracts/events.md` = `events.md`, module interfaces)

## §6 The dependency map

(written at Plan to the end)
