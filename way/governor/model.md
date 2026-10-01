# Governor — model and architecture

Checked 2026-10-01 09:30Z at HEAD `f2a3317` ("way: contract aligned with J158–J160 (ProposeWording schema; publish by id; Wordings readers)"). The working tree is clean, and HEAD equals `origin/claude/magical-cerf-axi3k1`.

Row checked: "interactions per workflow; the model drawn from them and covering every story's data; at C2 the variation matrix; decisions with their options; a contract for every boundary; the SDK list".

Records read:
- `way/model.md` §1–§5;
- `way/blueprint.md` §0 and §3;
- `way/vocabulary.md`, `way/join.md`, `way/seed.md`, `way/events.md`;
- `way/research/seed-check.md`, `sdks.md`, `option-console.md`, `option-ios-store.md`;
- every lens file and `way/personas/added-stories.md`;
- `contracts/openapi.yaml` and `contracts/README.md`;
- git (`status`, `log`, `show --stat` on every model-phase commit).

Not checked, as instructed: `blueprint.md` §2, `model.md` §6 and the `ledger.md` slice rows (the plan phase is being written in parallel). Only `way/`, `contracts/` and git were read. No outside service was contacted, and no identifier was sent anywhere. The validator was installed from PyPI into `/tmp/claude-0/gov`.

**Method.** Scripts kept outside the repo did the following:
- **Story ids.** They collected all 623 lens story ids (the `#### id ·` and `**id ·` heading lines above each file's first `## Lens verdict`) and the six `## id ·` headings of `added-stories.md`.
- **§1 traces.** They expanded every id range in the §1 "stories" column (for example `eater-1.3–1.6; auditor-9.4, 9.6`).
- **The contract.** They validated `contracts/openapi.yaml` with openapi-spec-validator 0.9.0 (`OpenAPIV31SpecValidator`). They listed every operation with its `x-module`, `x-events` and `x-stories`.
- **Cross-checks.** They checked those lists against the events catalogue (`events.md` §2–§3), the story ids, the §1 routes and events, and the §3.2 "Routes:" and "Emits:" lines.

**Verdict: 8 gaps.** Gaps 1, 2 and 5 have one cause: `model.md` was never brought up to date after deltas D5 and D6. Its last commit is `21c6ce7` (09:00), and that commit changed only 5 lines (§5). D5 (`0e3b517`, 08:25), D6 (`0311030`, 08:50), the three contract routes (`b5cf404`, 09:08) and J158–J160 (`2aff3ef`, `f2a3317`) all came after §1–§3 were written (`b7cba73`, 08:25), and none of them touched `model.md`. `grep` finds no mention in `model.md` of `10.72`, `10.73`, `10.42`, `10.43`, `5.45`, `7.25`, `added-stories`, `D5`, `D6` or `629`.

## Gaps

### Gap 1 — §1 and §2.7 do not trace the six D5 stories (line 1)
- **Record.**
  - Blueprint §3 D5: "six stories added at the model phase — `way/personas/added-stories.md`: admin-10.72 (…G1), auditor-10.42 (…G2), auditor-10.43 (…G3), eater-5.45 (…G4), eater-7.25 (…G5), admin-10.73 (…G6) … Impact: 629 stories".
  - Model §1 still reads:
    - "| 5.14 | system (scheduler) | a Saved Plan expires at the end of its Day … | **no story yet** (J130; §2.7 gap G4) |";
    - "| 10F.10 | Auditor | a review note → `POST /v1/admin/audit-trail/review-notes` (J17) | … | **no story yet** (J17; §2.7 gap G2) |";
    - "| 10F.11 | system (audit retention job) | … | **no story yet** (J18; §2.7 gap G3) |".
  - The §1 header says "(623 ids: eater 367, admin 75, approver 70, auditor 59, support 52)".
  - §2.7 still lists "**G1** … no admin story", "**G2** … no auditor story", "**G3** … no story", "**G4** … no eater story", "**G5** …", "**G6** …" as open.
- **What is missing.**
  - The three rows above do not cite eater-5.45, auditor-10.42 and auditor-10.43.
  - §1 has no row for:
    - saving Grant settings version n+1 (`PUT /v1/admin/grant-settings`, `grant_settings.version.saved`; admin-10.72);
    - the activity credit offer (`GET /v1/targets/activity-credit-offer`, `POST …/approve`; eater-7.25);
    - proposing and publishing a consent Wording (`POST /v1/admin/wording/proposals`, `POST /v1/admin/wording`, `wording.proposed`, `wording.published`; admin-10.73).
  - The header count should be 629.
  - §2.7 should close G1–G6 by naming their stories.
  - Every other row traces. The script finds all 623 lens ids cited across the 224 rows, and no cited id that does not exist.

### Gap 2 — §2 entities and states lag D6 (line 2)
- **Wording.**
  - Record: E12 **Wording** has the fields "`text_version` …; `family`; `version`; `en`; `ar`; `published_at`; `published_by`; `review_gate`", the states column "—", and created-by "auditor-9.3 (`/s` publishes `c-ai-4`), approver-10.53".
  - Against it: vocabulary D6 says "**Wording** …: Proposed → Published · Superseded; flag `asks_again`", and J153 and J154 say the same.
  - Missing: E12 has no `state` and no `asks_again`. The contract's `Wording` schema already carries both (`state` with `WordingState` `proposed, published, superseded`). E12 also does not name admin-10.73 as its creator.
- **Grant settings version.**
  - Record: E21 **Grant settings version** has the states "In use · Rolled back" and created-by "`seed.md` §4.6 (version 1; **no story changes it yet**, gap G1)". §2.5 item 5 reads "**Grant settings versions** are versioned the same way (In use · Rolled back)".
  - Against it: J155 says "States: In use → Replaced · Rolled back", and vocabulary D6 says the same.
  - Missing: the Replaced state (the contract's `GrantSettingsState` already reads `in_use, replaced, rolled_back`), and admin-10.72 as creator.
- **Credit cap field name.**
  - Record: §1 row 7.8 writes "a new Target version (`activity_mode`, `credit_factor`, `credit_cap`)".
  - Against it: J156 says "`credit_factor` (fraction) and `credit_cap_kcal` everywhere" (E62 already says `credit_cap_kcal`).
  - Missing: row 7.8 should use `credit_cap_kcal`.

### Gap 3 — eater-2.29's remembered pot is in no entity (line 2, sample)
- **Record.**
  - eater-2.29 (`personas/eater/wf2-wf4.md`), 4th `/r` line: "Given Mona cooked talbina before in "big pot" (1,216 g empty), When the empty-pot field opens for a new Recipe, Then it offers "big pot · 1,216 g (last time)" as a choice."
  - E45 Recipe version holds only "`ingredients[{food_version_id, mass_g}]`; `additions`; `discards`; `cooked_yield_g` (the weighed pot); `nutrient_vector_per_100g`; `assumptions`".
  - §2.7's device-only list ("the outbox commands …, Pending Entries and Analyses, Unit Drafts, the local trial's diary, the safety-screen answers and the onboarding inputs") does not name it.
  - The contract's `SaveRecipe` has only `command_id, name, ingredients, additions, discards, cooked_yield_g`.
  - `join.md` and `seed.md` hold no item on a named pot.
- **What is missing.** Somewhere to keep the named container and its empty weight per eater: an entity or field, or an explicit device-only line in §2.7 (plus the contract field if it is synced).

### Gap 4 — admin-10.47's token kinds and their prices are not in the model (line 2, sample)
- **Record.**
  - admin-10.47 `/m`: "Given an Analysis's usage metadata, When it is priced, Then input, output, thinking and cached tokens are each priced at their own rate."
  - E36 **Price** has only "`input_usd_per_mtok`; `output_usd_per_mtok`".
  - E38 **AI request record** has only "`input_tokens`; `output_tokens`".
  - §3.4 `AnalyzerResult` reads "json, input_tokens, output_tokens, latency_ms, finish".
  - The contract's `Price` requires only `input_usd_per_mtok, output_usd_per_mtok`.
  - `seed.md` §4.5 heads its column "output $ / 1 M tokens (thinking included)".
  - No join item names admin-10.47.
- **What is missing.**
  - Either the model records the thinking-token and cached-token counts and their rates (E36, E38, `AnalyzerResult`, the contract), or a join decision supersedes the `/m` line. The seed already folds thinking into output.
  - Cached tokens appear nowhere.

### Gap 5 — §3 lacks eight contract operations, one event, two interfaces and one console place (line 3)
- **Record.** Each operation below has an `x-module` naming a §3 module, but no §3.2 "Routes:" line lists it:

| operation | `x-module` | the §3.2 line as it reads |
|---|---|---|
| `GET /v1/targets/activity-credit-offer` (`getActivityCreditOffer`) | targets | targets "**Routes:** `POST /v1/targets/proposals`, `POST /v1/targets`, `GET /v1/targets`, `GET /v1/targets/current`, `GET /v1/targets/suggestions`, `POST /v1/targets/suggestions/{id}/accept`" |
| `POST /v1/targets/activity-credit-offer/approve` | targets | the same |
| `POST /v1/targets/suggestions/{id}/keep` | targets | the same (`keep_target` is in the interface) |
| `POST /v1/meal-plans/{id}/reopen` | plans | plans "`POST /v1/meal-plans/{id}/save\|validate\|not-eaten`" (`reopen` is in the interface) |
| `GET /v1/admin/grant-settings/versions` (`listGrantSettingsVersions`) | grants | grants "… `GET\|PUT /v1/admin/grant-settings`" |
| `POST /v1/admin/wording/proposals` (`proposeWording`) | privacy | privacy "… `POST /v1/admin/wording`" |
| `GET /v1/admin/wording/versions` (`listWordings`) | privacy | the same |
| `POST /v1/units/name-match` (`matchUnitNames`) | **units** | `unit_name_match` sits in the **analysis** interface (§3.2 #14), and neither module lists the route |

- **Also missing from §3.**
  - Privacy's "**Emits:**" line ends at "`wording.published`". `wording.proposed` (`events.md` §2.7: "a new Wording version is stored as Proposed (J153) | `POST /v1/admin/wording/proposals`") is emitted by no module. The script finds 120 catalogue events against 119 in the §3 Emits lines, and this is the only one missing.
  - The privacy interface has no propose or list-versions function. The targets interface has no credit-offer function (J156).
  - The §3.5 console table has no **Settings › Wordings** (J153: "Console place: **Settings › Wordings**").
  - §3.4 "Routes this model adds" lists none of the eight.
- **What is missing.** §3.2, §3.4 and §3.5 should carry these routes, functions, the event and the place. The name-match route needs one owner, either units or analysis.

### Gap 6 — 11 of the 23 decisions cite no research (line 4)
- **Record.** §4's preamble says "Each line names its research." The "why" cells of these lines cite only the brief, the blueprint or /way rule files:

| decision | what its "why" cites |
|---|---|
| A1 | "(system.md)" |
| A4 | "brief §15.1 …; (brief FR-042, S08)". [S08] is an FRD source (`frd-v1.0.md` line 518) |
| A5 | "brief FR-040…FR-043, §17.1" |
| A6 | "brief §6.1, §10, §11, NFR-01" |
| A7 | "brief §9.1; S10/S11". FRD sources, lines 523 and 525 |
| A10 | "blueprint §0 line 3; floor.md access line; FR-081" |
| A19 | "brief §15.1 operations row; §0 line 6" |
| A20 | "brief §19.2; admin-10.65, eater-9.13" |
| A21 | "floor.md experience law 5" |
| A22 | "ship.md" |
| A23 | "ship rows dropped with their risk" |

  - A2, A3, A8, A9, A11–A18 do cite `option-*.md`, `sdks` or r1 items (P, F).
  - For A4 and A7 the research exists, in `sdks.md` (google-cloud-firestore with N6, ortools with N2), but the lines do not cite it.
- **What is missing.** Each line needs a research citation: `research/option-*.md`, `sdks.md` or r1 item ids. Where the brief or a rule fixes the choice, the line should say "no research: fixed by …" so that the preamble's claim holds.

### Gap 7 — Cloud Storage has no decision, package or version (line 6)
- **Record.**
  - The model keeps media, exports, evidence, label photos, regression media and audit exports in Cloud Storage: §2.3 "Cloud Storage (private bucket; no public access; short-lived signed URLs, FR-077)" with 8 paths, and E14, E25, E27, E39, E47, E48, E52 and E67.
  - §3.4 defines `class MediaStore(Protocol): # Cloud Storage; a local directory in dev and tests` with `signed_url`.
  - §4 A4 names only "**Firestore** (google-cloud-firestore 2.33.0)".
  - `sdks.md` lists no Cloud Storage client. `grep -i storage` finds only the §0 hard-limit list and the emulator enum. Its table does list google-genai for the equally mock-first `Analyzer`.
- **What is missing.** The Cloud Storage client (for example `google-cloud-storage`) needs a row in `sdks.md` with its registry version, licence and OSV result. A4, or a new decision line, should name it.

### Gap 8 — contract changes after creation have no dated delta, and the stated counts are stale (lines 5 and 7)
- **Record.**
  - `contracts/README.md`: "**A contract change is a dated delta with its impact**, written in `way/` like D4–D6 … and a line added under "Changes" below. No change lands without its delta".
  - Its "Changes" has one line: "**2026-10-01 · created.** From model §3 (A2), join J1–J157".
  - After creation the contract changed twice:
    - `b5cf404` added 3 paths: `/v1/admin/grant-settings/versions`, `/v1/admin/wording/versions`, `/v1/admin/wording/proposals`.
    - `f2a3317` added the `ProposeWording` schema, made `en`/`ar` optional on `PublishWording` (J160), and added `access.refused` to `listWordings` (J159).
  - Blueprint §3's last delta is D6, which names "`way/join.md` §22 (J149–J157)". J158–J160 sit in join §22 and are recorded in no blueprint delta.
  - The three `x-contract-added` routes (`reopen`, `keep`, `name-match`) say "to be banked by a dated delta", and no delta banks them.
  - Stale statements:
    - README "Size on 2026-10-01: 146 paths, 169 operations …, 354 schemas" and model §5 "146 paths, 169 operations, 354 schemas". The file now has **149 paths, 172 operations, 355 schemas**.
    - Model §5 "`way/events.md` (119 events …)". The catalogue now has **120**.
    - README "Wording state `proposed` (J153) has no route of its own in v1 … a proposing route … is a delta". The route now exists.
    - The README module table: privacy "9", grants "17". These are now 11 and 18.
- **What is missing.**
  - A dated delta in blueprint §3, with its impact, for J158–J160 and the routes added after creation (or D6 widened to say so).
  - Matching "Changes" lines in the README.
  - The counts in README and model §5 corrected.

## Line by line

### (1) One interaction table per workflow, every row traced to story ids: fail (gap 1)

There are ten tables, WF-1 to WF-10, with the blueprint §1 ¶4 workflow names: "### WF-1 · Onboard and set a target" … "### WF-10 · Govern the reference and the AI (and just-in-time access)". WF-10 is "One table, in six parts" (10A–10F), with one header and no break.
- Each table has the columns persona · screen or interface · data read · data written · event raised · who is notified · stories.
- The row count is "WF-1 18 · WF-2 18 · WF-3 20 · WF-4 23 · WF-5 15 · WF-6 13 · WF-7 12 · WF-8 12 · WF-9 27 · WF-10 66 — **224 rows**", and the script's recount agrees.
- The script finds:
  - every lens story id cited (623 of 623);
  - no cited id that does not exist;
  - every event name in a §1 row in the `events.md` catalogue;
  - every §1 route present as a contract operation (the only differences are `a|b` shorthand and `{tab}`).
- The three "no story yet" rows and the untraced D5 stories are gap 1.

### (2) Data model: ER diagram, entity table, creating and reading story per entity, every story's data present: fail (gaps 2, 3, 4)

- **Diagram and table.**
  - §2.1 holds a `mermaid erDiagram`.
  - §2.2 has 70 entities ("**Count: 70 entities.**"). Each has keys and fields, states, owner · audit, versions and a "created by · read by" pair.
  - Every pair names a story, except:
    - E6 Permission: "code (`seed.md` §3)", fixed in code, which is acceptable;
    - E21: seed only (gap 2);
    - E30: seed plus admin-10.31.
- **Spot-checked cites.** 17 created-by and read-by cites were opened, and each story does create or read the record:
  - support-9.4 reads Account and Devices ("devices "iPhone · app 1.0.3 …"");
  - auditor-10.25 reads Role assignments ("**Held at**");
  - eater-9.12, auditor-9.15, admin-10.67, eater-7.21, eater-8.23, support-10.2, eater-1.13, eater-3.28, admin-10.61, eater-2.6, approver-10.62, eater-9.19, support-9.12, eater-3.23 and support-3.2, each by its title.
- **Sample of 19 stories across the five lenses and all six added stories, data looked up in §2.**

| story | data the story needs | in the model? |
|---|---|---|
| eater-1.23 | resting energy and method | ✓ E62 `resting_energy_kcal`, `input_snapshot`; row 1.9 |
| eater-2.29 | Recipe and yield | ✓ E45 |
| eater-2.29 | named pot remembered | ✗ gap 3 |
| eater-3.39 | Health sample ids | ✓ E54 `health_samples[{sample_id, device_id}]` |
| eater-6.15 | Restore and Entry history | ✓ E53, E54 |
| eater-8.31 | Suggested Target | ✓ E63 `change_kcal`, `reason`, `review_period` |
| eater-9.23 | support code | ✓ E17 |
| approver-10.22 | USDA release, Units count, Ingredient-updated flag | ✓ E26, E22, E28 |
| approver-10.49 | Policy floor, de-identified count | ✓ E29 |
| approver-10.53 | guidance in Policy | ✓ E29 `guidance_wording`, E12 |
| admin-10.47 | cost per Saved Unit and per confirmed meal | ✓ E70, E38 |
| admin-10.47 | thinking and cached token rates | ✗ gap 4 |
| support-9.15 | outside request | ✓ E18 |
| support-10.21 | idle session | ✓ E5 `idle_expires_at` |
| auditor-10.14 | Anomalies | ✓ E68 |
| added: eater-5.45 | Plan `expired_at`, consume `made_at` | ✓ E56, E53 |
| added: auditor-10.42 | review-note detail | ✓ E66 allow-listed detail; route in §3 audit |
| added: auditor-10.43 | retention summary | ✓ E66, E69 `audit_retention`; `roles_held_at_anchor` in `events.md` §2.10 |
| added: eater-7.25 | Target `credit_factor`, `credit_cap_kcal` | ✓ E62 (route: gap 5) |
| added: admin-10.72 | Grant settings Replaced | ✗ gap 2 |
| added: admin-10.73 | Wording `state`, `asks_again` | ✗ gap 2 |

- **The claim.** §2.7's claim "**Every story's data is in the model.**" does not hold for gaps 2–4.

### (2a) Variation matrix: not applicable

Blueprint §0 line 3: "**C1** — versioned product policies …". Model header: "C1 profile: no scouts, no variation matrix (customization.md: C2 only)." C2 is not reached, so no matrix is due.

### (3) Modules with owned collections, interfaces, routes and events: fail (gap 5)

- §3.2 has 22 modules ("**Count: 22 modules**"). Each has "**Owns:**", "**Interface:**" (Python signatures), "**Routes:**" with console sections, and "**Emits:** … **Consumes:** … **Depends on:**".
- Supporting sections:
  - §2.3 gives each collection one owner;
  - §3.1 gives the module rules and the hook protocols;
  - §3.3 is `nutrition_core`;
  - §3.4 gives the ports;
  - §3.5 is the console adapter;
  - §3.6 is a dependency graph with no cycle.
- The contract's 22 `x-module` values equal the 22 module names.
- The gaps are the eight operations, one event, the functions and the console place in gap 5.

### (4) Decisions A1–A23: options → winner → why, with research: fail (gap 6)

- All 23 rows have an options cell with at least two options, a bold winner and a why, for example "A15 | iOS local store | GRDB · SwiftData · Core Data | **GRDB.swift 7.11.1 on SQLite** … | `research/option-ios-store.md`: …".
- A15's earlier "pending" is closed (`d56c06a`).
- The research citations are gap 6.

### (5) Contracts: OpenAPI 3.1 valid, every §3 route an operation, events contract: pass on the file, gaps 5 and 8 on the record

- **Valid.** `openapi: 3.1.0`. `OpenAPIV31SpecValidator(spec).iter_errors()` gives **0 errors** (openapi-spec-validator 0.9.0). The file has 149 paths and 172 operations.
- **Every §3.2 route has an operation.**
  - All 22 modules' "**Routes:**" lines were matched one by one, including the shorthand (`approve|reject|retire|claim`, `[/{id}]`, `{tab}`) and the four test-build routes. Every one exists.
  - The reverse direction fails: eight operations have no §3 route (gap 5).
- **Contract extensions.**
  - Every operation has `x-module`, `x-events` and `x-stories`.
  - Every `x-events` name is in the `events.md` catalogue.
  - Every `x-stories` id exists.
  - The 15 catalogue events in no operation are written by jobs or the clock (`grant.expired`, `plan.expired`, `audit_trail.retention_run` …).
- **Events contract.**
  - `way/events.md` gives the envelope (§1), 120 events (§2 Audit trail, §3 domain) and the renames (§4).
  - Model §5 names it: "**Events:** `way/events.md` … is the event contract".
- The stale counts and the missing delta are gap 8.

### (6) `sdks.md`: each decision's package and version from a registry: fail (gap 7)

- **Method stated.** "Each version comes from the registry or the installed binary, never from memory: the PyPI JSON plus `pip index versions`, `npm view`, `git ls-remote` …". Licence and OSV results are given per package.
- **Versions in §4 match `sdks.md`.**

| decision | versions |
|---|---|
| A3 | FastAPI 0.142.2, Pydantic 2.13.5, Uvicorn 0.54.0, Starlette floor 1.3.1 |
| A4 | google-cloud-firestore 2.33.0, firebase-tools 15.32.1, Java 21 |
| A7 | ortools 9.15.6755 |
| A8 | firebase-admin 7.7.0 |
| A11 | google-genai 2.26.0 |
| A13 | jinja2 3.1.6, htmx 2.0.11 |
| A14 | Xcode 26.6 / Swift 6.3 |
| A15 | GRDB 7.11.1 |
| A16 | swift-openapi-generator 1.13.1, firebase-ios-sdk 12.19.2 |
| §5 | openapi-core 0.23.1 |

- **No package needed.** A1, A5, A6, A10, A12, A18 (HealthKit ships with the SDK), A19 (in-process), A20, A21 and A23 need no third-party package. A19's Cloud Tasks adapter is explicitly deferred "with a hosting delta".
- The Cloud Storage client is gap 7.

### (7) Join, seed, seed check with re-check and fixes, deltas D4–D6: pass, except the delta for J158–J160 (gap 8)

- **Join.**
  - `way/join.md` J1–J148 plus §22 J149–J160 ("Delta D6 — decisions on the model phase's last open points").
  - The §20 coverage index maps every lens conflict list. The recount agrees with the index: e578 §7 has 25 items (W5.1–W5.25), e36 §6 has 19, e24 §5 has 19, admin §7 has 16 and approver §7 has 16.
- **Seed.** `way/seed.md` is the one fixture set.
- **Seed check.** `way/research/seed-check.md`: "**Result: 884 values checked. 868 are ok and 16 are DEFECT.**"
  - The fixes are in `seed.md` "## Fixes after seed-check" (D1–D16) and in `join.md` (`40e545e`).
  - The re-check reads "**Result: fail. 4 defects (R1–R4).** Twelve of the sixteen fixes hold in full."
  - The session's fixes are in `seed.md` "## Fixes after the re-check (2026-10-01, by the session)" (`6824f8d`). Each R fix was checked here against its target:

| fix | where | what it reads now | holds? |
|---|---|---|---|
| R1 | fix-log row D1 | "The import stays at 04:30Z (07:30 Riyadh), 45 min after the walk ends and at the same minute as Faisal's 07:30 Breakfast" | ✓ |
| R2 | J54 | "Also supersedes e578 eater-7.6 "Health holds 81.3 kg …" (read 83.5 kg at 2026-10-01T06:00:00Z, shown 184.1 lb)" | ✓ |
| R3 | J55 | "eater-5.44 `/m` "among the 12 count sets that meet every limit" (read "among the 3 count sets …"" | ✓ |
| R4 | §7.6 eater-5.17 row | "with aim 809.7, 170 other count sets would reach it exactly" | ✓ (see note 3) |

- **Deltas.** Blueprint §3 holds:
  - "**delta D4 · the join's names**";
  - "**delta D5 · six stories added at the model phase**";
  - "**delta D6 · the model phase's last open points** — `way/join.md` §22 (J149–J157)".
  - `vocabulary.md` carries "## Delta D6 (2026-10-01) — from `join.md` §22".
  - J158–J160 and the later contract changes have no delta (gap 8).

### (8) Committed: pass

`git status`: "nothing to commit, working tree clean". HEAD `f2a3317` equals the upstream branch.

## Notes (not counted)

1. **The ER diagram** draws every §2.2 entity except E48 **Unit picture**, and the Shadow-comparison half of E41.
2. **Anonymous sessions and support codes.**
   - E3 says an Anonymous session "holds only Analyses, the daily AI quota count, the Age confirmation and Consents (J124)".
   - Against it: `seed.md` gives SE13's anonymous session "support code `SB-9HQT-26KD`", and eater-9.23 shows one.
   - E17's `user_id` can hold the anonymous id, so the data is present, but E3's "only" list omits it.
3. **The fix-log D9 row.** `seed.md`'s row D9 still reads "With 809.7, 53 other sets reach the aim exactly". The re-check asked for the change "In both places". The §7.6 row is fixed, and the R4 line directly below the fix log corrects the D9 row ("170 … not 53"), but the row itself was not edited.
4. **`sdks.md` is keyed by decision in words** ("Python runtime", "Document store client" …), not by A-number. It was written one minute before §4 (`142bf83` 07:24, `9a1ea98` 07:25). The mapping above is by name.
5. **admin-10.41 against J124.** admin-10.41's second `/r` line (anonymous `POST /v1/consumption` returns a Confirmed Entry) is superseded by J124 ("→ 403 `CONSENT_REQUIRED`"). The model follows J124 (§3.2 ledger). Recorded only because the lens line still reads the old way.

## Re-audit 2026-10-01

Checked at HEAD `3369c2b` ("way: model governor gaps 1–7 fixed (model.md); CostView token fields"). The working tree was clean, and HEAD equals `origin/claude/magical-cerf-axi3k1`. The fixes came in two commits after the first audit: `892156d` (delta D7, `Price.cached_input_usd_per_mtok`, the `google-cloud-storage` row in `sdks.md`, README size line) and `3369c2b` (`model.md` 88 lines in and 73 out; `CostView` token fields; D7 widened).

Records read: `way/model.md` §1–§5, `contracts/openapi.yaml`, `contracts/README.md`, `way/research/sdks.md`, `way/blueprint.md` §3 (D7), `way/join.md` J158–J160, `way/events.md`, `way/seed.md` §4.5, the lens files and `added-stories.md` (story ids only), and git. Not checked, as instructed: `blueprint.md` §2, `model.md` §6 and the ledger's slice rows. No outside service was contacted, and no identifier was sent anywhere.

**Method.** The scripts of the first audit were run again, from outside the repo:
- **Story ids and §1 traces.** 629 ids (eater 369, admin 77, approver 70, auditor 61, support 52); 230 §1 rows; every id cited, no cited id that does not exist; no row reads "no story yet".
- **The contract.** openapi-spec-validator 0.9.0 in the venv `/tmp/claude-0/gov`: `OpenAPIV31SpecValidator(spec).iter_errors()` gives **0 errors**. `openapi: 3.1.0`, `info.version` `1.0.0-2026-10-01`, **149 paths, 172 operations, 355 schemas**.
- **§3 against the contract.** Every one of the 172 operations is on the "**Routes:**" line of the module its `x-module` names, and every §3.2 route is an operation. The §3.2 "**Emits:**" lines name 120 events, exactly the 120 of the `events.md` catalogue (§2–§3). Every `x-events` name and every §1 event name is in the catalogue. Every §1 route is an operation (the only differences are the `\|` table escapes, `{tab}` and the concrete gate `privacy_review`).

**Verdict: 6 of 8 fixed; gaps 7 and 8 are still open, each narrowed.**

### Gap 1 — the six D5 stories in §1 and §2.7: fixed
- The §1 header reads "the five lenses and the six stories of delta D5 (`personas/added-stories.md`) — 629 ids: eater 369, admin 77, approver 70, auditor 61, support 52". The script agrees.
- The three rows now cite their stories: 5.14 "… `plan.expired` | Eater: the Today card changes (no push) | eater-5.45 |"; 10F.10 "… `POST /v1/admin/audit-trail/review-notes` {`scope`, `period`, `finding`, `note`} (J17, J149) … | auditor-10.42 |"; 10F.11 "… | auditor-10.43 |".
- The missing rows exist:
  - 10E.15 "`PUT /v1/admin/grant-settings` … Grant settings version n+1 In use; the previous → Replaced (J155) … | `grant_settings.version.saved`, `access.refused` | … | admin-10.72 |";
  - 7.13 "`GET /v1/targets/activity-credit-offer`; Review → Approve … `POST /v1/targets/activity-credit-offer/approve` … | eater-7.25 |";
  - 10D.6 "`POST /v1/admin/wording/proposals` … | `wording.proposed`, `access.refused` …| admin-10.73 |", 10D.7 "`POST /v1/admin/wording` {`text_version`, `asks_again`} (J158, J160) … | admin-10.73 |" and 9.28 (asks again, admin-10.73).
- §2.7: "The six gaps this section first listed are **closed by delta D5**", with G1–G6 each naming its story and row.
- The row count line reads "WF-1 18 · … · WF-10 69 — **230 rows**", and the recount agrees.

### Gap 2 — §2 entities and states after D6: fixed
- E12 **Wording**: fields "`state`; `asks_again` (J154); `proposed_at`, `proposed_by`", states "Proposed → Published · Superseded (a newer version of the family is Published) (J153)", created-by "admin-10.73, approver-10.53 · auditor-9.3". This matches the contract's `Wording` (`state`, `asks_again` required) and `WordingState` `proposed, published, superseded`.
- E21 **Grant settings version**: states "In use → Replaced · Rolled back (J155)", created-by "admin-10.72 (`seed.md` §4.6 holds version 1) · support-10.2". §2.5 item 5: "**Grant settings versions** too (In use → Replaced · Rolled back, J155)". This matches `GrantSettingsState` `in_use, replaced, rolled_back`.
- Row 7.8: "a new Target version (`activity_mode`, `credit_factor`, `credit_cap_kcal`)". `grep` finds no bare `credit_cap` left in `model.md`.

### Gap 3 — eater-2.29's remembered pot: fixed
- §2.7 device-only records now end: "and the **remembered containers** — per eater, a container's name and empty weight in grams (tare), e.g. "big pot · 1,216 g", offered as "(last time)" when a Recipe's pot is weighed (eater-2.29). They are not synced in v1, so no contract field carries them; only the resulting `cooked_yield_g` reaches the server (E45)."
- Row 2.7 reads it: "the remembered containers on the iPhone ("big pot · 1,216 g (last time)", device-only, §2.7)". `SaveRecipe` is unchanged, as the device-only line says it should be.

### Gap 4 — admin-10.47's token kinds and prices: fixed
- E36 **Price**: "`cached_input_usd_per_mtok` (when the provider states one; else cached input is priced at the input rate)" and "thinking tokens are priced at the output rate (Gemini bills thinking as output; `seed.md` §4.5)". The seed's column reads "output $ / 1 M tokens (thinking included)", so model and seed agree.
- E38 **AI request record**: "`input_tokens` (not cached); `cached_input_tokens`; `output_tokens`; `thinking_tokens`; … `estimated_usd` (input × input rate + cached × cached rate + (output + thinking) × output rate, admin-10.47)".
- §3.4: "AnalyzerResult: json, input_tokens (not cached), cached_input_tokens, output_tokens, thinking_tokens (Gemini bills them as output), latency_ms, finish." The registry interface: "`record_request` … input, cached input, output and thinking tokens, each at its rate (thinking at the output rate)".
- Contract: `Price` and `PriceIn` carry `cached_input_usd_per_mtok`; `Price.description` gives the thinking and cached rule; `CostView.by_task` carries `input_tokens`, `cached_input_tokens`, `output_tokens`, `thinking_tokens`. D7 records it: "`Price.cached_input_usd_per_mtok` and per-task token fields on `CostView` (cached input, thinking at the output rate; admin-10.47)".

### Gap 5 — §3 routes, event, interfaces and console place: fixed
- §3.2 "**Routes:**" lines now carry all eight: targets "`POST /v1/targets/suggestions/{id}/keep` (`keepTarget`) *(new)*, `GET /v1/targets/activity-credit-offer` (`getActivityCreditOffer`), `POST /v1/targets/activity-credit-offer/approve` (`approveActivityCreditOffer`) (J156)"; plans "`POST /v1/meal-plans/{id}/reopen` (`reopenMealPlan`) *(new)*"; grants "`GET /v1/admin/grant-settings/versions` (`listGrantSettingsVersions`)"; privacy "`POST /v1/admin/wording/proposals` (`proposeWording`), `GET /v1/admin/wording/versions` (`listWordings`)"; units "`POST /v1/units/name-match` (`matchUnitNames`) *(new)*".
- The name match has one owner: units, with "`def match_unit_names(…)` # matchUnitNames: the Unit-name match (J126)". `unit_name_match` no longer appears in `model.md`, and the contract's `x-module` is `units`.
- Privacy "**Emits:**" ends "`wording.proposed`, `wording.published`". The script finds 120 events emitted against 120 in the catalogue.
- Interfaces: privacy `propose_wording` and `wordings`; targets `keep_target`, `activity_credit_offer`, `approve_activity_credit_offer`; plans `reopen`; grants `settings_versions`.
- §3.5: "| Settings › Wordings (J153) | privacy |". §3.4 "Routes this model adds" ends with the eight routes, "added since §1–§3 were first written (the contract's own additions, D5, D6)".

### Gap 6 — research behind the decisions: fixed
- The §4 preamble now reads "Each line names its source — the brief, research or the /way method", and every line A1–A24 does.
- A4 and A7 now cite the research that exists: A4 "`research/sdks.md` (google-cloud-firestore 2.33.0; N2 protobuf pin; N6 the emulators on Java 21)"; A7 "`research/sdks.md` (ortools 9.15.6755; N2)". A21 now cites "`way/design/kit.md` … and `personas/eater/research.md` §4"; both exist.
- The lines with no research say what fixes them, for example A1 "source: the /way method — … (system.md)", A5 "source: brief FR-040–FR-043 and §17.1", A23 "source: blueprint §0 line 10". The preamble's claim now holds.

### Gap 7 — the Cloud Storage client: still open (narrowed)
- **Fixed:**
  - §4 has a new line: "A24 | Media store | Cloud Storage · Firestore blobs | **Cloud Storage through `google-cloud-storage` 3.15.1** (PyPI, 2026-09-29, Apache-2.0, Python ≥ 3.10) behind the `MediaStore` port (§3.4) …".
  - `sdks.md` has a row: "| Media store (raw scans, audio, Unit pictures) | `google-cloud-storage` | 3.15.1 (2026-09-29) | … | Apache-2.0 | … (PyPI JSON, opened 2026-10-01 by the session) …". The privacy interface depends on "the `MediaStore` port (Cloud Storage, A24)".
- **Still open:** the first audit asked for a row "with its registry version, licence and **OSV result**". There is no OSV result for `google-cloud-storage` 3.15.1:
  - The table "Security and upkeep per package" has no row for it.
  - The file's method says "OSV.dev was queried for the exact pinned version", but nothing records that query for this package.
  - N19's audit ("`pip-audit` … over the full Python 3.13 venv (133 distributions) → "No known vulnerabilities found"") and N1's measured resolve ("`uv pip compile` resolves the whole pinned set") both ran before the package was added. The record does not show that it resolves with ortools' protobuf cap (N2).
  - Unlike the other rows, it has no N-note quoting the PyPI JSON.

### Gap 8 — dated delta for later contract changes; stale counts: still open (narrowed)
- **Fixed:**
  - Blueprint §3 now has a delta: "**2026-10-01 · delta D7 · contract and model changes after the contract's first write** — `contracts/openapi.yaml`: three routes added (`GET /v1/admin/grant-settings/versions`, `GET /v1/admin/wording/versions`, `POST /v1/admin/wording/proposals` with event `wording.proposed`), the `ProposeWording` schema and publish-by-id (J158–J160), Wording readers (J159), … `join.md` J158–J160. Counts now 149 paths, 172 operations, 355 schemas, 120 events. Impact: no banked work." J158–J160 in `join.md` match the contract (`PublishWording` requires `text_version` and `asks_again`, `en`/`ar` optional; `listWordings` lists `access.refused`; 422 on `publishWording`).
  - Model §5: "OpenAPI 3.1.0, 149 paths, 172 operations, 355 schemas" and "`way/events.md` (120 events …)". Both are true.
  - README: "**Size on 2026-10-01:** 149 paths, 172 operations …, 355 schemas". True.
- **Still open, all in `contracts/` (the README and three `x-contract-added` texts):**
  1. **No "Changes" line.** The README's rule: "… and a line added under "Changes" below. No change lands without its delta". "## Changes" still has the single line "**2026-10-01 · created.** From model §3 (A2), join J1–J157 …". D7 has no line there.
  2. **The audience split is stale.** The README reads "149 paths, 172 operations (78 for the eater app, 87 for staff, 4 in test builds only)". 78 + 87 + 4 = 169. By security scheme the file has 78 eater, **90** staff (89 `staffSession` plus the sign-in `POST /v1/admin/session`) and 4 test.
  3. **The module table is stale.** It reads privacy "| 9 | 1 |" and grants "| 17 | 12 |". The file has privacy **11 operations, 3 staff** and grants **18, 13 staff**.
  4. **The proposing route is said not to exist.** The README reads "Wording state `proposed` (J153) has no route of its own in v1: the texts are supplied when publishing; a proposing route, if the console needs one over HTTP, is a delta." `POST /v1/admin/wording/proposals` exists (D7), and publishing no longer supplies the texts (J160).
  5. **The `x-contract-added` routes are still marked unbanked.** The README reads "**Added** (`x-contract-added`: model §3.2 names the interface but no route; to be banked by a dated delta): `POST /v1/units/name-match` (`analysis.unit_name_match`, J126 …)". The three `x-contract-added` values in `openapi.yaml` read "… §3.2 lists none" (reopen, keep) and "model §3.2 analysis.unit_name_match … §3.2 lists no route for it". `info.description` also reads "(to be banked by a dated delta)". All are now false:
     - model §3.2 lists all three routes;
     - the name match is `units.match_unit_names`;
     - no delta names `reopen`, `keep` or `name-match` (D7 names only the three later routes).

## Line by line (re-audit)

1. Interactions traced to every story: **pass** (gap 1 fixed).
2. Data model covering every story's data: **pass** (gaps 2, 3 and 4 fixed). The sample rows marked ✗ in the first audit (eater-2.29 pot, admin-10.47 rates, admin-10.72 Replaced, admin-10.73 `state` and `asks_again`) are now in the model.
3. Variation matrix: **not applicable** (C1).
4. Modules, routes, events: **pass** (gap 5 fixed).
5. Decisions with research: **pass** (gap 6 fixed).
6. Contracts: the file is **valid** (0 errors, 149 · 172 · 355), and §3 and the contract match in both directions. The README and the `x-contract-added` texts are **stale** (gap 8).
7. `sdks.md`: **fail**. The OSV result for `google-cloud-storage` 3.15.1 is missing (gap 7).
8. Join, seed, deltas: D7 covers J158–J160 and the later routes. The README "Changes" line and the banking of the three `x-contract-added` routes are missing (gap 8).
9. Committed: `3369c2b` was clean and pushed when checked. This section is not yet committed.

## Notes (not counted)

1. **D7's impact line.** It reads "Impact: no banked work". The README's rule asks a delta for its "impact on modules, the iOS client, the console and the stories or tests that read it". D6 has the same short form.
2. **The Storage emulator.** The `sdks.md` firebase-tools row says "Firestore, Auth and Tasks emulators for tests", but A24 and the new row use the Storage emulator. N6's enum (`… STORAGE …`) shows it exists. The firebase-tools row does not name it.

**Open: gap 7** (no OSV result, audit or resolve on record for `google-cloud-storage` 3.15.1 in `sdks.md`) and **gap 8** (`contracts/README.md` has no "Changes" line for D7 and four stale statements; the three `x-contract-added` texts and `info.description` still say "§3.2 lists none" or "to be banked by a dated delta").
