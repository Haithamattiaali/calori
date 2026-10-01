# Governor — tailor

Checked 2026-10-01 against commit `041c843` ("way: tailor — profile (§0), records, owner's FRD as the brief"). Row checked: "§0 holds every profile line with its source; the keep-going loop is armed". Records read: `way/blueprint.md`, `way/ledger.md`, `way/journey.md`, `way/lessons.md`, `way/brief/frd-v1.0.md`, git.

**Verdict: 4 gaps.**

## Gaps

1. **§0 line 10 says the branch and a draft PR exist, but the remote is empty.**
   Record: blueprint §0 line 10, "GitHub `Haithamattiaali/calori` (public), this session's branch `claude/magical-cerf-axi3k1` with a draft PR". Git: `git ls-remote origin` returns no refs (exit 0, empty). `git rev-parse @{u}` returns "fatal: no upstream configured for branch 'claude/magical-cerf-axi3k1'". `git for-each-ref` lists only `refs/heads/claude/magical-cerf-axi3k1`.
   Missing: the branch pushed to origin and the draft PR opened. If that is still to come, line 10 should say so. Until the push, commit `041c843` exists only in this container.

2. **The keep-going loop is described but not shown as armed.**
   Record: blueprint §0 "Environment facts", "The keep-going loop: this cloud session works the ledger turn by turn and resumes from git and `way/` (`/build-loop` lives on his Mac, not here)." Line 8 says "then alone to Close". The journey does not mention the loop.
   Missing: two things. (a) What starts the next turn when a turn ends and he is not there: a named wake with its id, such as a scheduled routine. "Works the ledger turn by turn" names no trigger. (b) A resume point that exists outside this container. The loop "resumes from git", but nothing is on the remote (gap 1). No record says the loop is armed.

3. **§0 line 6 leaves out part of the brief section it cites, and cites a file that is not in the repo.**
   Record: line 6 lists "Gemini … USDA FoodData Central · Apple HealthKit · Firebase Auth + App Check · Firestore · Cloud Storage · Cloud Run · OR-Tools", with source "brief §15.1; floor.md adapters line". The brief's §15.1 table also has this row: "Operations | Cloud Tasks, Secret Manager, Cloud Logging/Monitoring, Firebase Crashlytics and Remote Config | Async jobs, secrets, redacted diagnostics, rollout and rollback" (frd line 306). Some of these are needed before any hosting: Crashlytics and Remote Config are in-app SDKs; Cloud Tasks runs the export/delete jobs ("Returns authenticated job status", frd line 369) and the "failed jobs" in the FR-080 console; Secret Manager holds the Gemini and USDA keys.
   Missing: either list these systems, or name each one as deferred (for example with operate, under line 10) and give the reason. Also, `floor.md` is not in `way/` or git. Label the adapter-and-mock rule as the recommended default, or add the record it comes from.

4. **§0 line 1(c) gives the brief as the source for "web", but the brief does not say web.**
   Record: line 1, "(c) **web — admin console** for the nutrition approver, support and platform admin | brief §1.4, §15.1, FR-080". FR-080 (frd line 383) says only: "Provide a role-based administrator console for reviewed nutrition records, aliases, model/config rollouts, failed jobs, and de-identified quality metrics." Neither §1.4 nor §15.1 mentions a web surface.
   Missing: give "recommended default" as the source for the console being web. The console itself comes from the brief.

## Line by line (§0, blueprint lines 9–18)

| # | Line | §0 source | Brief line checked | Result |
|---|---|---|---|---|
| 1 | Kind per surface | brief §1.4, §15.1, FR-080 | "The initial interface shall be a native iOS application, with a reusable backend for Android." (§1.4) · "Mobile \| Native SwiftUI; local database and durable outbox" · "API \| Python/FastAPI on Cloud Run" (§15.1) · FR-080 as quoted in gap 4 | iOS and API are true. "web" has the wrong source: gap 4 |
| 2 | Size: product | inferred from the brief | "Separate support privileges from nutrition-approver and platform-admin privileges." (FR-081) · journeys A–E (§2.2–2.6) · USDA (§6.2), Gemini (§15.1), HealthKit (FR-062) | True: 4 personas, 3 external integrations; ≈8 workflows is a fair count |
| 3 | Customization: C1 | inferred from the brief (§3.3, §16.4, FR-080, FR-081) | "Calorie defaults and safety boundaries are centrally versioned product policies with qualified nutrition review." (§3.3) · "Freeze approved model IDs in a server registry" (§16.1) · "Threshold is configurable and evaluated." (§10.2) · FR-080 | True |
| 4 | Tenancy: one organisation, per-user scope | brief NFR-07, §22 | "Tenant isolation \| Automated negative access tests on all endpoints and storage paths; no cross-user source or media access" (NFR-07) · "Any cross-user exposure … blocks public release." (§22 release blockers) | True |
| 5 | Reach: EN + AR, RTL, time zones | brief §1.2, §8.1, §14.1 | "Launch language coverage is English and Arabic, including Egyptian food aliases and mixed-language input." (§1.2) · "Arabic shall support right-to-left layout, both Arabic-Indic and Western numerals…" (§14.1) · "A custom boundary supports night-shift or late-night eating." (§8.1) | True |
| 6 | Systems it must talk to | brief §15.1; floor.md | §15.1 table, frd lines 299–306 | Part of the cited section is missing, and floor.md is not in the repo: gap 3 |
| 7 | First look: clickable prototype | brief §23.1; recommended default | "Foundation \| Weeks 1–2 \| Confirm assumptions; prototype core flows; …" (§23.1) | True. Both sources are named |
| 8 | How it runs: fast | his answer, 2026-10-01 | Not in the brief; nothing in the brief contradicts it. The only records are §0 and journey line 5 ("he answered run mode (fast)") | Source stated |
| 9 | Hard limits | brief | Excluded scope includes "Medical diagnosis; insulin/medication dosing; unsupervised child or pregnancy weight planning" (§1.3) · "The app remains a general-wellness product; it shall avoid diagnosis, medication advice…" (§19.3) · "Choose one approved regional deployment after verifying…" (§15.3) | True |
| 10 | Where it lives and ships | his answer, 2026-10-01 | The brief wants "isolated development, staging, and production projects" (§15.3). Line 10 overrides this ("no staging, no production"), and the Off list drops ship with its risk. That override is recorded | Source stated. The remote and the PR are not real yet: gap 1 |

All ten lines are present and each has a source column. That part of the row passes.

## Ledger ship rows against §0 line 10 — pass

§0 "Off" says: "the ship proof, go-live, operate and measure rows — **dropped with their risk until a dated delta names a hosting target**". §0 "On" says: "the pipeline on the remote". `way/ledger.md` matches:
- `pipeline | … | planned | … | GitHub Actions: Linux job (API, admin) + macOS job (iOS build, simulator walks)`
- `ship | dropped | §0 line 10: no hosting target. Risk: nothing is deployed; staging and rollback are unproven…`
- `go-live | dropped | … Risk: no production; no TestFlight/App Store submission`
- `operate | dropped | … Risk: no alerts, runbooks or restore drill running`
- `measure | dropped | … Risk: the §1.5 success measures are not read from live events`

## Git — committed: pass; pushed: no (gap 1)

`git status`: "On branch claude/magical-cerf-axi3k1 / nothing to commit, working tree clean". `git log`: a single commit, `041c843`, which holds `way/blueprint.md`, `way/journey.md`, `way/ledger.md`, `way/lessons.md`, `way/brief/frd-v1.0.md`, `README.md` and `.gitignore`. This governor file is not committed.

## Notes (not counted)

- Lines 8 and 10 rest on "his answer". No exact wording of that answer is in `way/`; only §0 and the journey restate it. Recording his words with the date would make these two lines checkable.
- Line 9 could also name the brief's hard "do nots": "Do not sell health/nutrition data, use it for behavioral advertising, or enable model-training use without a separate explicit opt-in" (FR-079), and "No model response may write totals directly" (§15.2).
