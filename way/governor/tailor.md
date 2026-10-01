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

## Re-audit 2026-10-01

Checked 2026-10-01 04:11Z against commit `b183a45` ("way: tailor — governor gaps fixed (sources, ops services, loop armed, PR base noted)"). Records read: `way/blueprint.md`, `way/journey.md`, `way/ledger.md`, `way/lessons.md`, `way/map-first.md`, `way/brief/frd-v1.0.md`, git (`git ls-remote origin`, `git log`, `git diff 041c843 b183a45 -- way/blueprint.md`). Only `way/` and git were read. The scheduler and GitHub were not read.

1. **Branch and PR on the remote: fixed.**
   Git: `git ls-remote origin` returns `b183a45… HEAD` and `b183a45… refs/heads/claude/magical-cerf-axi3k1`. `git rev-parse HEAD @{u}` gives `b183a45` for both, and `git status` says "Your branch is up to date with 'origin/claude/magical-cerf-axi3k1'". `git ls-remote origin 'refs/pull/*'` returns nothing, so no PR exists. Record: line 10 now says "this session's branch `claude/magical-cerf-axi3k1` (pushed 2026-10-01). The remote has no `main` branch yet, so a draft PR has no base: creating `main` is a push to another branch and waits for his word (asked once, at the gate)." Git agrees: the remote has only that one branch. The record matches git.

2. **Keep-going loop armed: fixed, as far as `way/` and git can show.**
   Record: blueprint "Environment facts", "The keep-going loop, armed 2026-10-01: … a self check-in (`send_later`, trigger `trig_01TqQZcLTZVn6idwgPjS7Hd6`, first fire 2026-10-01T05:11Z, re-armed after each fire while work remains) is the fallback when a turn ends with nobody there. … every session resumes from the pushed branch and `way/`". This covers (a), a named wake with its id, and (b), a resume point outside the container: the branch is on origin (see 1). Limits: the trigger was not read from the scheduler, because this audit reads only `way/` and git. Its first fire (05:11Z) is after this check. The journey still does not mention the loop. That was not part of the gap.

3. **§0 line 6: partly fixed. The `floor.md` part is still open.**
   - Operations row: fixed. Line 6 now lists "operations: Cloud Tasks, Secret Manager, Cloud Logging/Monitoring, Firebase Crashlytics and Remote Config". It names them as deferred, with the reason: "the operations services are hosting-side and wait with the dropped ship rows (no hosting target), their code seams (async job queue, secrets from the environment, structured redacted logs, remote-config-shaped registry) are built from the first slice". Its source is "brief §15.1 (Component table incl. the Operations row)". This matches frd line 306 and the ledger's dropped ship, go-live, operate and measure rows.
   - `floor.md`: still open. The source now reads "the /way skill's `floor.md` (adapters line)". This says where the file lives. But the file is still not in `way/` or git (`grep -rn floor.md way` finds only this citation), and the adapter-and-mock rule is not labelled "recommended default". The gap asked for one of those two fixes, and neither was made. Smallest fix: add "recommended default" to that source, or copy the adapters line into `way/`.

4. **§0 line 1(c) "web": fixed.**
   Record: line 1 source now reads "(a)(b) brief §1.4, §15.1; (c) the console is brief FR-080 ("role-based administrator console", no surface named) — **web is the recommended default**". The brief supplies the console, and web is now sourced as the recommended default.

### Notes (not counted)

- **New in `b183a45`: line 1(c) adds "auditor" without a source.** Line 1 now reads "admin console for the nutrition approver, support, platform admin and auditor". The brief names no auditor role (`grep -i auditor way/brief/frd-v1.0.md` finds nothing). FR-081 names only "support", "nutrition-approver" and "platform-admin" privileges. The only record of an auditor is `way/map-first.md` line 17, "**Auditor / privacy reviewer** (hidden) | … | FR-081, FR-082, NFR-13". Line 2 still says "4 human personas (eater, nutrition approver, support agent, platform admin)". Give "auditor" a source ("inferred, map-first") and make line 2 match.
- The earlier note on lines 8 and 10 is now addressed. They read "his answer, 2026-10-01: chose "Fast (Recommended)"" and "…: chose "GitHub only, no hosting yet"".
- This re-audit section is not committed.

**Re-audit verdict: 1 gap still open. Gap 3, part 2: §0 line 6 cites "the /way skill's `floor.md`", which is not in `way/` or git, and the line is not labelled "recommended default".**

## Closing note by the session, 2026-10-01
The one gap the re-audit left open (gap 3, part 2) and its note were fixed in commit e834f12: the adapter rule is labelled a recommended default, the auditor persona is sourced to the first map's hidden-persona hunt, and line 2 counts 5 personas. The governor's rule is one re-audit per phase; Tailor is closed on this fix.
