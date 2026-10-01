# Platform admin — the lens (WF-10 and the admin's part of WF-9)

Written 2026-10-01 by the platform-admin lens of research cycle 2; revised the same day in fix round 1 (see the foot of this file). Reads `way/blueprint.md` §0–§1, `way/brief/frd-v1.0.md`, `way/research/r1-*.md` with both refutations, `care.md` and `way/lessons.md`. Findings that a refuter marked refuted or doubtful are not cited. A finding marked "as corrected" is cited only for its corrected statement.

**The persona (map §2).** The platform admin runs the **Registry** (map §6). In it they keep:

- frozen Gemini model ids per AI task;
- prompt versions and extraction-schema versions;
- per-user daily AI quotas.

They take each change through **Shadow → Canary → Rollout**, with a way back (**Rolled back**) and a **kill switch** that keeps manual and cached logging working. They read estimated cost, de-identified quality metrics and failed jobs, and they manage roles (permissions → roles → users, deny by default; blueprint §0 line 3). Their surface is the **admin console** (web). They never see a diary. Sources: brief §16.1, §16.4, §16.5, FR-080 and FR-081; P1–P4, P5 as corrected in r1-refute-b.

---

## 1 · Research cycle 2: the platform admin's day

### 1.1 How to read this section

I opened every source below in this run on **2026-10-01**, using a generic User-Agent and no owner identifiers. Each entry gives the link, the date the source itself carries, and a short quote. A claim I could not open is labelled `assumption`. Cycle-1 findings are cited by id, but only where they stand.

### 1.2 Findings (A = admin)

**Model lifecycle: what the Registry has to keep track of**

- **A1 · On Agent Platform, Gemini models come in two availability classes. Gemini 3.8 Flash is in the short-term class, so a retirement can be announced with only 45 days' notice.** `opened`
  https://docs.cloud.google.com/gemini-enterprise-agent-platform/models/model-versions (last updated 2026-10-01): "Short-term availability models remain active until a replacement model is released and a retirement date is announced … we post a fixed date … that gives you at least 45 days to migrate." The page lists `gemini-3.8-flash` (released September 2, 2026) as "No retirement date announced" in that class. For the 12-month class it says "retirement timelines may be extended, they won't be moved to an earlier date". It lists `gemini-3.5-flash-lite` as "July 21, 2027 or later" and `gemini-2.5-flash` as retiring "October 20, 2026".
- **A2 · The Gemini API (not Agent Platform) publishes its own shutdown table, and its dates do not match A1.** `opened`
  https://ai.google.dev/gemini-api/docs/deprecations (last updated 2026-10-01): "The shutdown dates listed in the table indicate the earliest possible dates". It lists `gemini-3.5-flash-lite` as "No shutdown date announced", which agrees with r1-refute-b P2, while A1 gives "July 21, 2027 or later" for the same model on Agent Platform. On 2.5 it says "we are limiting access to the 2.5 models to users who have actively used them in the past" (as P6 is corrected in r1-refute-b). **Consequence:** lifecycle dates are held *per surface*.
- **A3 · Google's own migration guide orders the steps as offline evaluation, then load testing, then online evaluation (A/B, canary or "shadow mode"). It suggests tracking how often users override outputs.** `opened`
  https://docs.cloud.google.com/gemini-enterprise-agent-platform/models/migrate (last updated 2026-10-01): "It's hard to predict these changes without first testing your prompts with the new version." / "Load testing must occur before online evaluation" / "Tracking how often users override or manually adjust outputs from the older model versus the latest models." / "This parallel deployment is sometimes called 'shadow mode'". The same page warns: "You may observe an expected increase in reported token counts". It also says Gemini 3 models "have new default resolutions and token costs for images".
- **A4 · Firebase's production checklist for AI apps says to use stable models only, and never a preview or a `-latest` alias. It says to keep the model name in remote configuration, set per-user rate limits, and use budget alerts and spend caps.** `opened`
  https://firebase.google.com/docs/ai-logic/production-checklist (last updated 2026-10-01): "only use stable model versions (like gemini-3.8-flash). Do not use a preview or experimental version or a -latest alias." / "make on-demand changes to the model name … without releasing a new version of your app" / "Set rate limits per user (default is 100 RPM)" / "Avoid surprise bills with alerts and spend caps" / "Use separate Firebase projects for development, testing, and production." Floating aliases do exist and the SDK examples use them (P4).
- **A5 · Model availability changes often. Google recommends a remotely set model name with in-app defaults.** `opened`
  https://firebase.google.com/docs/ai-logic/change-model-name-remotely (last updated 2026-10-01): "The availability of generative AI models frequently changes — new, better models are released and older, less capable models are shut down." Google released a new Flash about once a month (P3).

**How configuration rollouts are run in practice**

- **A6 · Remote Config rollouts expose a change to a percentage of users, compare that group against a control, and can be rolled back. Google's own example is rolling out an LLM prompt.** `opened`
  https://firebase.google.com/docs/remote-config/rollouts (last updated 2026-10-01): "Create a rollout that updates the parameter that contains your LLM prompt(s) to a small percentage of your user base." / "Rollback functionality … roll back to a previous version of the feature".
- **A7 · The control group is the same size as the exposed group. A rollout stays at or below 50 % until it goes to 100 %. A user's group is sticky.** `opened`
  https://firebase.google.com/docs/remote-config/rollouts/about (last updated 2026-10-01): "if you roll out to 2% of your users, they are added to the Enabled group and an additional 2% of your users are added to the Control group" / "any rollout you create must be exposed to less than or equal to 50% until and unless you roll out to 100%" / "Rollout group assignment is consistent across all phases of a rollout." / "if you reduce the percentage to 0% … If you later decide to increase the rollout percentage, users who were part of the previous Enabled or Control groups will return to the group they were originally assigned".
- **A8 · Every publish creates an immutable version, and rolling back applies "immediately for all apps and users".** `opened`
  https://firebase.google.com/docs/remote-config/templates (last updated 2026-10-01): "Each time you update parameters, Remote Config creates a new versioned Remote Config template and stores the previous template as a version that you can retrieve or roll back to" / "use those values immediately for all apps and users." / "a total limit of 300 lifetime stored versions".
- **A9 · Server-side Remote Config evaluates its template on every request and assigns percentage groups by a stable id. Google's own example reads an `is_ai_enabled` switch. The feature is still Preview.** `opened`
  https://firebase.google.com/docs/remote-config/server (last updated 2026-10-01): "Your server can then evaluate the template with each incoming request" / "you might set … a user ID, to ensure that each user that contacts your server is added to the proper randomized group" / `const is_ai_enabled = config.getBool('is_ai_enabled');` / "Remote Config in server environments is a Preview release." Remote Config is also absent from Google's data-residency list (R18 as corrected). Both facts support the blueprint's in-house, Remote-Config-*shaped* Registry (§0 line 6).
- **A10 · Never switch a UI the person is using in the middle of their task, and do not depend on the network for configuration.** `opened`
  https://firebase.google.com/docs/remote-config/loading (last updated 2026-10-01): "Don't update or switch aspects of the UI while the user is viewing or interacting with it" / "Don't rely on network connectivity to obtain Remote Config values. Do set in-app default parameter values".
- **A11 · In Google's experience, a majority of incidents are triggered by binary or configuration pushes. A canary is a partial, time-limited deployment that is evaluated against a control. Google's SRE advice is to run one canary at a time and to keep metrics few and attributable.** `opened`
  https://sre.google/workbook/canarying-releases/ (Site Reliability Workbook, ch. 16, © 2018): "a majority of incidents are triggered by binary or configuration pushes" / "We define canarying as a partial and time-limited deployment of a change in a service and its evaluation." / "We strongly advise running only one canary deployment at a time." / "Select the top few metrics … (perhaps no more than a dozen)", which is a soft guideline and not a limit / "This can result in the canary process being disabled or ignored by operators" / "make sure the intervals of your metrics are either the same as or less than your canary duration." / On traffic teeing: "the canary deployment serves the copy and discards the responses".
- **A12 · In a shadow test, the new version receives a copy of live requests, and only the production variant's responses go back to the caller.** `opened`
  https://docs.aws.amazon.com/sagemaker/latest/dg/shadow-tests.html (accessed 2026-10-01): "routes a copy of the inference requests to it in real time … Only the responses of the production variant are returned to the calling application."
- **A13 · A guarded rollout pauses or rolls back on a regression it detects, and it rolls back automatically if each step is not reached by a minimum number of contexts.** `opened`
  https://launchdarkly.com/docs/home/releases/guarded-rollouts (accessed 2026-10-01): "If LaunchDarkly detects a regression before the rollout reaches 100%, it can pause the rollout and send a notification." / "must be evaluated by a minimum number of contexts during each step … If this requirement is not met, LaunchDarkly automatically rolls back the change."

**Kill switches, and incidents in configuration pushes**

- **A14 · Kill switches are long-lived ops toggles. They are worthless if flipping one needs a release.** `opened`
  https://martinfowler.com/articles/feature-toggles.html (Pete Hodgson, 9 Oct 2017): "a small number of long-lived 'Kill Switches' which allow operators of production environments to gracefully degrade non-vital system functionality" / "needing to roll out a new release in order to flip an Ops Toggle is unlikely to make an Operations person happy."
- **A15 · On 12 Jun 2025 a Google Cloud policy change was replicated globally "within seconds" and took down APIs worldwide. The red button took about 40 minutes to roll out.** `opened`
  https://status.cloud.google.com/incidents/ow5i3PPK96RduMcb1SsW: "this metadata was replicated globally within seconds. This policy data contained unintended blank fields." / "Within 40 minutes of the incident, the red-button rollout was completed". The remediations: "data replication needs to be propagated incrementally with sufficient time to validate and detect issues" / "We will enforce all changes to critical binaries to be feature flag protected and disabled by default."
- **A16 · On 18 Nov 2025 a generated configuration file at Cloudflare doubled in size and was propagated to the whole network. The fixes are to validate internal config like user input and to add more global kill switches.** `opened`
  https://blog.cloudflare.com/18-november-2025-outage/: "That feature file, in turn, doubled in size. The larger-than-expected feature file was then propagated to all the machines that make up our network." / "Hardening ingestion of Cloudflare-generated configuration files in the same way we would for user-generated input" / "Enabling more global kill switches for features".
- **A17 · CrowdStrike's root-cause report for July 2024 commits to staged rings: a canary, bake-in time, then promotion or rollback.** `opened`
  https://www.crowdstrike.com/wp-content/uploads/2024/08/Channel-File-291-Incident-Root-Cause-Analysis-08.06.2024.pdf (2024-08-06): "New Template Instances that have passed canary testing are to be successively promoted to wider deployment rings or rolled back if problems are detected." / "followed by additional bake-in time".

**Quotas and cost**

- **A18 · Gemini 3.8 Flash's price doubles on 1 January 2027, and output price includes thinking tokens.** `opened`
  https://ai.google.dev/gemini-api/docs/pricing (last updated 2026-10-01): for `gemini-3.8-flash` the input price is "$0.75 through December 31, 2026. $1.50 starting January 1, 2027." The output price "(including thinking tokens)" is "$3.75 through December 31, 2026. $7.50 starting January 1, 2027." For `gemini-3.5-flash-lite` it is "$0.30" input and "$2.50" output. This agrees with P3 as opened in r1-refute-b. The brief says "Do not embed temporary provider prices in core requirements" (§23.3), so prices are effective-dated configuration.
- **A19 · Usage metadata reports input, output, thinking, cached and total tokens.** `opened`
  https://ai.google.dev/gemini-api/docs/tokens (last updated 2026-09-23): "Returns token counts for input (total_input_tokens), output (total_output_tokens), thinking (total_thought_tokens), cached content (total_cached_tokens) …".
- **A20 · Provider quotas are per project, across requests and tokens per minute and per day. Going over any one of them returns 429.** `opened`
  https://firebase.google.com/docs/ai-logic/quotas (last updated 2026-10-01): "exceeding any of them will trigger a 429 quota-exceeded error" / "Rate limits are applied at the project-level". **Consequence:** a per-user quota protects the shared project quota as well as the bill.

**Roles and access**

- **A21 · Enforce least privilege, deny by default and validate on every request.** `opened`
  https://cheatsheetseries.owasp.org/cheatsheets/Authorization_Cheat_Sheet.html (accessed 2026-10-01): "For security purposes an application should be configured to deny access by default." / "Permission should be validated correctly on every request" / "it is easier to grant users additional permissions rather than to take away some they previously enjoyed" / "'role explosion' can occur when a system defines too many roles".
- **A22 · RBAC assigns each user one or more roles that correspond to jobs. The model is standardised as ANSI/INCITS 359.** `opened`
  https://csrc.nist.gov/projects/role-based-access-control: "Each user is assigned one or more roles, and each role is assigned one or more privileges that are permitted to users in that role." / "revised as INCITS 359-2012".
- **A23 · Google Cloud's IAM practice: no broad roles in production, simulate a role change before making it, audit changes to access.** `opened`
  https://cloud.google.com/iam/docs/using-iam-securely (last updated 2026-09-24): "In production environments, do not grant basic roles unless there is no alternative." / "use the Policy Simulator to ensure that changing the role won't affect the principal's access" / "regularly audit changes to your allow policy".
- **A24 · In Google's own just-in-time access tool, nobody can approve their own request.** `opened`
  https://docs.cloud.google.com/iam/docs/pam-approve-deny-grants (last updated 2026-09-24): "You can't approve your own request." Saudi health-data rules also require "separated duties" (R24, stands, opened official).

**De-identified metrics**

- **A25 · Metrics can be withheld so that nobody can infer an individual from a small group.** `opened`
  https://support.google.com/analytics/answer/9383630 (GA4, accessed 2026-10-01): "Data thresholds are applied to prevent anyone viewing a report or exploration from inferring the identity or sensitive information of individual users".
- **A26 · Health-data publishers suppress small cells and forbid deriving them from other cells.** `opened`
  https://resdac.org/articles/cms-cell-size-suppression-policy (accessed 2026-10-01): "no cell … containing a value of 1 to 10 can be reported directly … no cell can be reported that allows a value of 1 to 10 to be derived from other reported cells". Two choices are mine: the threshold of "fewer than 11 distinct eaters", and complementary suppression to stop derivation.

**Admin UX**

- **A27 · Confirmation dialogs work only when they are rare and specific. Undo is better.** `opened`
  https://www.nngroup.com/articles/confirmation-dialog/ (Jakob Nielsen, 18 Feb 2018, reviewed 7 Aug 2026): "Do not use confirmation dialogs for routine actions." / "Do not ask Are you sure you want to do this? Instead, explain what this is" / "(Don Norman goes so far as to suggest requiring a different user to confirm the most dangerous actions.)" / "do go to great lengths to provide undo".

**Jobs**

- **A28 · Cloud Tasks retries failed tasks with exponential backoff, within set limits on attempts and duration.** `opened`
  https://cloud.google.com/tasks/docs/configuring-queues (last updated 2026-09-30): "If a task doesn't complete successfully, Cloud Tasks retries the task with an exponential backoff" / "Specify the maximum number of times to retry failed tasks in the queue". The brief adds: "Recoverable jobs support bounded retries with the same command ID" (§18.2).

**Place and time**

- **A29 · In Ramadan, Saudi activity moves to the night, between Taraweeh and sahoor.** `opened`
  https://www.arabnews.com/saudi-arabia/how-saudi-arabias-night-time-economy-takes-over-during-holy-month-2634925 (Arab News, 2026-03-01): "it shifts almost entirely to the night" / "Between Taraweeh prayers and sahoor, commercial activity surges" / "From 10 p.m. to 2 a.m. we see more traffic than during an entire weekday outside Ramadan". It is used here only for the eater's late diary-day boundary (admin-10.44). Whether *our* load peaks this way is an `assumption`, and no rollout window is derived from it.

**Cycle-1 findings this lens rests on (all stand or are cited as corrected):**
- P1: `gemini-3.8-flash` is stable and the production example.
- P3: a new Flash about every month.
- P4: `-latest` aliases exist.
- P5 as corrected in r1-refute-b: the sampling parameters are deprecated for all 3.x models, and `thinking_level="minimal"` errors on 3.8 Flash.
- P7: Gemini takes audio input.
- P11 as corrected in r1-refute-b: 3.8 Flash and 3.5 Flash-Lite are served only on `global` and the `us`/`eu` multi-regions, and "Endpoints don't guarantee data residency".
- P12: use `generateContent` on Agent Platform.
- P14, now opened, from Agent Platform's zero-data-retention page: "in-memory … 24-hour TTL … can be disabled at the project level"; "you can request an exception for abuse monitoring"; "If you do not specify a value for store, it defaults to true … explicitly set store = false".
- P15 as narrowed in r1-refute-b: `gemini-3.5-transcribe` lists only ar-EG, and on Agent Platform it is `-preview` on `global` only.
- R17: no training without permission, which is still not zero retention.
- R18 as corrected: Remote Config is not on the residency list, and grounding is excluded from the residency promise.
- R23: deletion within 30 days, backups included.
- R24: separated duties for health data.

### 1.3 The admin's day, put together

What the sources show going wrong, sourced:
- Configuration pushed everywhere in seconds without validation or a gate took down large services (A15, A16, A17).
- A red button that was not ready took 40 minutes (A15). A kill switch that needs a release is "unlikely to make an Operations person happy" (A14).
- Noisy canary metrics lead operators to ignore or disable the canary (A11).
- Confirmations that appear too often stop being read (A27).
- A short-term model can be given only 45 days' notice (A1), Flash releases arrive about monthly (P3), and the same model carries different dates on two Google surfaces (A1, A2).
- Prices change on a fixed date (A18).
- Roles tend to accumulate permissions (A21, A23).

What I assume about the persona's routine, `assumption` (no observational source was found in this run):
- They check the Registry overview, Metrics and Jobs at the start of a working day.
- They triage each new Gemini release about monthly, following P3's cadence.
- They change roles when staff join, leave or move.

Place, partly sourced:
- The console is proved at desktop width and at about 390 px (blueprint §0, "What the profile switches on").
- That planned work happens at a desk and urgent work on a phone, sometimes on a weak network and at night, is an `assumption`.

Moments that decide their trust. These are inferred from the sources above, `assumption` as to how the persona feels:
1. Stopping a misbehaving AI at once while logging keeps working (brief §16.4, NFR-05).
2. Moving a version to Rollout on numbers they can trust (A11, A13).
3. Moving off a model before it retires (A1).
4. A cost jump (A18).
5. A role that grants too much (FR-081).

---

## 2 · Goals

- **G1 · Current and correct.** Every AI task runs a frozen, evaluated model id, prompt version and schema version. A new model reaches eaters only after offline evaluation, Shadow and Canary, and before the old one retires (§16.1, §16.4).
- **G2 · Harm stops at once.** A kill switch or a roll back takes effect within the chosen propagation time without a release. Manual, recent-Unit, Template and cached logging never depend on AI (§7.2, §16.4, NFR-05).
- **G3 · Cost is bounded and visible.** Per-user daily quotas, a daily budget, and estimated cost per Saved Unit and per confirmed meal (§16.5 "cost per accepted unit and confirmed meal", §23.3).
- **G4 · Least privilege.** No role can read a diary; only an eater-approved Grant opens one (FR-081, NFR-12, map §3).
- **G5 · Quality is seen without seeing people.** Acceptance by Evidence type, validation failures and failed jobs, with no identifiers and no small groups (FR-080).
- **G6 · Privacy jobs finish on time.** Failed exports, deletions and retention purges are retried safely or escalated before their deadline (WF-9, FR-078, NFR-13, AT-29).
- **G7 · A trail the auditor trusts.** Every change records who, when, before and after, and why (FR-081, map §2 auditor).

---

## 3 · Shared definitions for the acceptance lines

**One name per thing in this lens.** The map's words are used as they are. New words are proposed in §6 for the model phase to fix.

- **AI tasks.** The code key and the English screen name are the same word. They follow the brief's capture modes (§14 "Meal, Unit, Label, Recipe") and `POST /v1/analyses` ("meal, label, scale, recipe, or text/voice transcript"):
  - `meal` · **Meal**
  - `label` · **Label**
  - `scale` · **Scale**
  - `recipe` · **Recipe**
  - `text` · **Text** (intent from typed text or a transcript)
  - `voice` · **Voice** (transcription)
- **Registry version:** one immutable configuration of one task (map §3). On screen it is "Meal version 7"; in the API it is `meal@v7`.
- **Registry version states:** **Proposed** → **Shadow** → **Canary** → **Rollout** → **Rolled back**.
  - Map §3 names the stages "shadow → canary → rollout".
  - The version in Rollout is the one every eater gets.
  - A version taken out of Shadow, Canary or Rollout by a roll back (by hand or automatically) is **Rolled back**.
  - When a newer version reaches Rollout, the one it replaces is shown as "previous Rollout version". It is the roll-back target.
- **Kill switch** (vocabulary D2): one per task, **On** or **Off**. When On, AI requests for that task fail fast with `AI_UNAVAILABLE` and nothing is queued to send later. The screen reads "Kill switch On" or "Kill switch Off". Nothing is called "paused".
- **Console sections** (vocabulary D2), as this persona uses them:
  - **Registry**: Registry versions, the models list, the prompt editor, the regression set, the kill switch, quotas and the daily budget.
  - **Metrics**: quality figures, estimated cost and prices.
  - **Jobs**.
  - **Roles**: permissions → roles → users, the blueprint §0 line 3 chain.
  - **Audit trail**.
  - **Settings**: language, and the launch gates (provider data settings, credential, dependency audit, restore test).

  The panels inside a section are described in plain words. They are not new place names; see §7 for any that should become names.
- **Copy rule.** Screen copy never shows internal ids: no permission keys, no finding or requirement ids, and no Registry-version keys.
  - Vendor model ids such as `gemini-3.8-flash` are shown, because they are Google's public names and the admin reads them on Google's pages.
  - Model ids, versions and numbers sit in a fixed-width font.
- **Admin API paths** `/v1/admin/...` are an `assumption`; the model phase fixes the contract.
- **The AI provider** is the Gemini adapter's realistic mock until the owner's cutover (§0 line 6). The mock reports token counts and latency, can be told to fail, and keeps a request log readable by tests (`/s`).
- **Synthetic staff accounts:** `admin.a@example.test`, `admin.b@example.test`, `approver.a@example.test`, `support.a@example.test`, `auditor.a@example.test`, `access.a@example.test`.
- **Synthetic eaters:** `eater-synth-001` to `eater-synth-400`. **Anonymous sessions:** `anon-synth-001` and up.

**Starting values to try in the served product.** Care group 3 asks that every value with no provably right answer be chosen on purpose. Each value is an `assumption` until it is tried:

| value | starting value | why |
|---|---|---|
| Propagation of kill switch and roll back | ≤10 s | must beat the 40-minute red button (A15) |
| Pressed-state feedback on any console button | ≤100 ms | — |
| Shadow sample | 5 % | — |
| Canary share | 5 %, maximum 50 % | A7 |
| Minimum sample per stage and group | 200 Analyses and 30 distinct eaters | — |
| Canary checks | about six; a dozen is A11's soft guideline, not a limit | A11 |
| Evaluation tolerance | proposed version ≥ Rollout version − 2 points per score | — |
| Small groups | fewer than 11 distinct eaters are hidden | A26 |
| Retirement warning / block for new Proposed versions | ≤90 days / ≤30 days | — |
| Job attempts | 5 | — |
| Deletion priority | ≤7 days before the deadline | — |
| Provider data settings | re-checked every 90 days | — |

---

## 4 · Journeys

### Journey admin-9 — privacy jobs that failed (WF-9, the platform admin's part)

WF-9's done-when: "delete account removes private data and media within the policy window and leaves a completion record without identifiers". FR-080 puts "failed jobs" in the console. The admin sees these jobs de-identified. Support sees one account's jobs under its own permissions (map §2).

#### admin-9.1 · Failed deletion jobs come first, with their deadline
As platform admin, I see failed deletion jobs at the top of **Jobs** with the days left before the 30-day deadline, so that no account deletion runs late. WF-9 done-when, FR-078, FR-080, NFR-13, AT-29; R23. *Shared: Auditor (reads deletion completion records), Support agent.*
- Given a deletion job requested 2026-09-05 that failed with a storage error, When **Jobs** opens on 2026-10-01, Then:
  - it is the first row;
  - it reads "Deletion · 4 days left · due 5 Oct 2026 · failed 3 of 5 attempts";
  - no eater name, email or account id is shown. `/r`
- Given that job, When `admin.a` clicks "Retry" and it completes, Then:
  - the row leaves the failed list;
  - when `auditor.a` opens **Audit trail**, filtered to deletion completion records, exactly one completion record exists for that job, with no identifiers. `/r`

#### admin-9.2 · Retry a failed export once, and get one export
As platform admin, I retry a failed export under its original job id, so that the eater receives exactly one export. WF-9, FR-075, §18.2, FR-043; AT-10. *Shared: Support agent.*
- Given an export job for `eater-synth-101` that failed once, When `admin.a` clicks "Retry" on **Jobs**, Then:
  - the same job id shows "Running" and then "Completed";
  - `eater-synth-101`'s `GET /v1/privacy/export-or-delete` status shows exactly one ready export. `/r`
- Given the same Retry clicked twice within a second, When both reach `POST /v1/admin/jobs/{id}/retry`, Then:
  - both return 202 with the same job id;
  - **Jobs** shows one attempt added, not two. `/r`

#### admin-9.3 · A retry re-checks the account and the Consent first
As platform admin, I can trust a retry to be refused by itself when the eater has since deleted the account or withdrawn Consent, so that a retry never re-sends their data or builds an export for a deleted account. AT-29, FR-076, FR-078, §16.3 step 1. *Shared: Eater, Support agent.*
- Given a failed export job for `eater-synth-102`, whose account deletion has since completed, When "Retry" is clicked on **Jobs**, Then:
  - the retry is refused with 422 `VALIDATION_ERROR` "This account was deleted. The export won't be retried.";
  - the job stays Failed with the note "Not retried: account deleted";
  - no export file is created, as the export storage listing in the `/s` check shows. `/r` `/s`
- Given a failed `meal` analysis job for `eater-synth-103`, who has since withdrawn the Consent to send photos to Google's AI, When "Retry" is clicked, Then:
  - the retry is refused with 403 `CONSENT_REQUIRED`;
  - the job stays Failed with the note "Not retried: Consent withdrawn";
  - the provider mock's request log shows no call for that job. `/r` `/s`
- Given the kill switch is On for `meal`, When a failed `meal` analysis job is retried, Then:
  - the retry is refused with 503 `AI_UNAVAILABLE` and the message "The kill switch is On for Meal. Turn it off first.";
  - the job stays Failed. `/r`

#### admin-9.4 · An overdue retention purge is flagged
As platform admin, I see a failed retention purge as overdue, so that raw scans and audio do not outlive their Policy. FR-078; map §6 Policy (raw scans 30 days, audio 24 h).
- Given the audio purge job failed and the oldest temporary audio is 26 hours old, When **Jobs** opens, Then:
  - the row reads "Retention purge · Audio older than 24 hours exists · overdue 2 hours";
  - it sits directly under the deletion rows. `/r`
- Given the purge is retried and completes, When **Jobs** refreshes, Then the row is gone and "Oldest temporary audio" on **Settings** › launch gates reads under 24 hours. `/r`

### Journey admin-10 — govern the AI and the console (WF-10, the platform admin's part)

WF-10's done-when for this persona: "an admin rolls a model version back and manual logging keeps working". The map's §3 row "Admin → registry | roll a model/prompt/schema version; kill switch | Registry version | shadow → canary → rollout; frozen model ids, no `latest` | config live / rolled back".

| step | stories |
|---|---|
| A · Enter and orient | 10.1–10.4 |
| B · Models | 10.5–10.7 |
| C · Propose a Registry version | 10.8–10.12 |
| D · The regression set and offline evaluation | 10.13–10.17 |
| E · Shadow | 10.18–10.21 |
| F · Canary | 10.22–10.25 |
| G · Rollout and roll back | 10.26–10.30 |
| H · Kill switch | 10.31–10.37 |
| I · Quotas and cost | 10.38–10.48 |
| J · Metrics (quality) | 10.49–10.54 |
| K · Failed AI jobs | 10.55–10.57 |
| L · Roles | 10.58–10.64 |
| M · Launch gates on Settings | 10.65–10.67 |
| N · Audit trail, language, access | 10.68–10.71 |

#### A · Enter and orient

#### admin-10.1 · See what is live, in one look, even offline
As platform admin, I open **Registry** and see every AI task with its Rollout version, any newer version and its state, the kill switch and the model's retirement, so that I know what every eater gets before I touch anything. FR-080, §16.4, map §6 Registry; A1, A10. *Shared: Auditor (read-only).*
- Given:
  - `meal` is in Rollout at version 6 (`gemini-3.8-flash`, prompt version 11, schema version 3);
  - version 7 is in Canary at 5 %;
  - `text` is in Rollout at version 4 (`gemini-3.5-flash-lite`);
  - every kill switch is Off.

  When `admin.a` opens **Registry**, Then the **Meal** row reads:
  - "Rollout: version 6 · gemini-3.8-flash · prompt version 11 · schema version 3";
  - "Canary: version 7 · 5 % of eaters";
  - "Kill switch Off";
  - "Retirement: none announced · short-term model, at least 45 days' notice".

  Six rows are shown: Meal, Label, Scale, Recipe, Text, Voice. `/r`
- Given the same state, When `GET /v1/admin/registry` is called with `admin.a`'s token, Then it returns six tasks, each with its Rollout version id (for example `meal@v6`), any newer version and its state, the share, the kill-switch state and a revision number. `/r`
- Given a fresh deployment with no newer versions, When **Registry** loads, Then:
  - every row shows its seeded Rollout version;
  - the newer-version cell reads "None · Propose a version" with that button. `/r`
- Given **Registry** loaded at 22:10 and the network then drops, When the page is viewed at 22:12, Then:
  - a bar reads "Offline · showing data from 22:10";
  - Propose, Move to … and Roll back are disabled with "Needs a connection";
  - when the connection returns, the bar disappears and the state reloads from the server. `/r`

#### admin-10.2 · No role, no access
As platform admin, I rely on a staff account with no role seeing nothing, so that a forgotten assignment never becomes a leak. Blueprint §0 line 3 (deny by default), FR-081, NFR-07; A21. *Shared: every console persona.*
- Given `new.user@example.test` signs in to the console holding no role, When the console loads, Then:
  - the "No access" page names the account and says "Ask a platform admin to assign a role";
  - no section links are shown. `/r`
- Given that account's token, When it calls each `/v1/admin/*` path in the probe list, Then every call returns 403 `FORBIDDEN` with no data. `/r`
- Given the API route table, When the authorization test suite runs, Then any admin route without a declared permission fails the build. `/s`

#### admin-10.3 · The wrong role gets a clear no
As platform admin, I need staff accounts without Registry permissions to get a clear "no" in the console and `FORBIDDEN` from the API, so that roles hold at every door. FR-081, FR-080 (role-based console); A21. *Shared: Support agent, Nutrition approver, Auditor.*
- Given `support.a` holds only Support agent, When they open the Registry address directly, Then:
  - the page reads "You don't have access to Registry. Your role doesn't include 'Read the Registry'.";
  - a link leads back to their sections. `/r`
- Given `auditor.a` holds Auditor, When they open **Registry**, Then:
  - the overview is read-only;
  - no Propose, Move to …, Roll back or kill-switch control is rendered. `/r`
- Given `auditor.a`'s token, When `PUT /v1/admin/kill-switch/meal` is sent with `{"on": true}`, Then it returns 403 `FORBIDDEN` and **Registry** still reads "Kill switch Off" for Meal. `/r`

#### admin-10.4 · No client can pick a model or reach the admin API
As platform admin, I need the app and eater tokens to have no say over which model runs, so that the Registry is the only source of model ids. §15.2, §16.1, FR-039. *Shared: Eater.*
- Given `eater-synth-001`'s token, When `POST /v1/analyses` for `meal` carries an extra field `"model": "gemini-flash-latest"`, Then:
  - it returns 422 `VALIDATION_ERROR` with `field: "model"` and the message "Not a client field";
  - the provider mock's request log shows no call. `/r` `/s`
- Given the same token, When it calls `GET /v1/admin/registry`, Then it returns 403 `FORBIDDEN`. `/r`

#### B · Models

#### admin-10.5 · Record a model's lifecycle per surface
As platform admin, I record each model's release date, retirement date, availability class and source page for each Google surface in the models list on **Registry**, so that the Registry does not rely on memory. §16.1, §25 ("model IDs … must be revalidated at implementation and release"); A1, A2.
- Given the models list on **Registry**, When `admin.a` adds `gemini-3.5-flash-lite` with:
  - Agent Platform: released 21 Jul 2026, retirement "21 Jul 2027 or later", class "12-month", with its source page;
  - Gemini API: released 21 Jul 2026, retirement "none announced", with its source page,

  Then both surface rows show side by side, each with its own date and source link. `/r`
- Given the same form, When the retirement date is before the release date or the source page is empty, Then:
  - the field shows "Retirement must be after release" or "Add the page you read this on";
  - Save stays disabled. `/r`
- Given the models list holds `gemini-3.8-flash` as short-term on Agent Platform, When `GET /v1/admin/models/gemini-3.8-flash` is called, Then it returns `availability_class: "short_term"` and `min_notice_days: 45`. `/r`

#### admin-10.6 · A retirement countdown that cannot be missed
As platform admin, I see the days to retirement for every model in Rollout or in a newer version, so that a 45-day notice becomes a planned move. §16.1; A1.
- Given:
  - today is 2026-10-01;
  - `scale` is in Rollout at version 2 on `gemini-2.5-flash` (a synthetic legacy fixture);
  - the models list gives its Agent Platform retirement as 20 Oct 2026 (A1).

  When **Registry** loads, Then:
  - the **Scale** row reads "Model retires in 19 days (20 Oct 2026)";
  - a banner at the top names Scale and offers "Propose a version". `/r`
- Given a model with no retirement announced, When **Registry** loads, Then it shows no countdown and no banner. `/r`

#### admin-10.7 · Floating aliases, preview models and unknown ids are refused
As platform admin, I can choose only stable model ids that are in the models list on **Registry**, so that "frozen" means frozen. §16.1 ("do not use a 'latest' alias"); A4, P4.
- Given the Propose form for `meal`, When `admin.a` types `gemini-flash-latest`, Then:
  - the field reads "Floating aliases aren't allowed. Choose a fixed model id.";
  - `POST /v1/admin/registry/meal/versions` with that id returns 422 `VALIDATION_ERROR` with `field: "model_id"`. `/r`
- Given `gemini-3.5-transcribe-preview` is listed as "preview" on Agent Platform (P15 as narrowed), When a `voice` version using it is moved to Canary, Then the move is refused with "Preview models can run in Shadow only". `/r`
- Given a model id that is not in the models list, When it is typed, Then the field reads "Not in the models list. Add it there first." with a link. `/r`

#### C · Propose a Registry version

#### admin-10.8 · Propose a Registry version
As platform admin, I propose a new Registry version for one task from a listed model, a prompt version, a schema version and bounded limits, so that a change is one reviewable thing before any eater sees it. §16.4, §16.5 ("Bound image resolution, tokens, retries, execution time"); A8.
- Given **Registry › Meal** with version 6 in Rollout, When `admin.a` chooses the following and saves:
  - model `gemini-3.8-flash`, prompt version 12, schema version 3;
  - thinking level "low", max output tokens 2,048;
  - image long edge 1,536 px, timeout 12 s, retries 1,

  Then:
  - "Meal version 7 · Proposed" appears with a field-by-field difference from version 6;
  - **Audit trail** records `admin.a`, the time and the new version. `/r`
- Given the same inputs, When `POST /v1/admin/registry/meal/versions` is called, Then it returns 201 with `id: "meal@v7"`, `state: "proposed"` and every field echoed. `/r`
- Given `meal` already has a version in Shadow or Canary, When another version is proposed, Then:
  - it is saved as Proposed;
  - "Move to Shadow" stays disabled with "One version at a time per task: version 7 is in Canary" (A11). `/r`

#### admin-10.9 · Write a prompt version
As platform admin, I write a new prompt version as immutable text with a visible difference from the last one, so that every Analysis can be traced to the exact words the model was given. §16.4.
- Given the Meal prompt editor on **Registry** shows prompt version 11, When `admin.a` edits the text and saves, Then:
  - prompt version 12 is created;
  - the editor shows added and removed lines against version 11;
  - version 11 stays readable and unchanged. `/r`
- Given prompt version 11 is used by a Registry version, When `PUT /v1/admin/prompts/meal/11` is sent with new text, Then it returns 422 `VALIDATION_ERROR` "Prompt versions can't be changed. Save a new version." `/r`
- Given the prompt text holds the Arabic alias example «لبن» (F27), When it is saved and reopened, Then the Arabic runs right-to-left inside the left-to-right editor and the surrounding English keeps its order. `/r`

#### admin-10.10 · Schema versions come from code, not from the console
As platform admin, I pick an extraction-schema version from those the running server can validate, so that the console never invents a field the validator does not know. §7.1, §16.3 step 4, blueprint §0 line 3 (C1: values, not concepts).
- Given the Propose form for `meal`, When the schema list opens, Then:
  - it lists only the schema versions the running API declares (for example "schema version 3");
  - each is read-only, with its field list. `/r`
- Given `POST /v1/admin/registry/meal/versions` names `schema_version: 9`, which the server does not declare, When it is sent, Then it returns 422 `VALIDATION_ERROR` "Unknown schema version". `/r`
- Given the server starts, When it loads its schema versions, Then a declared version without a validator stops start-up. `/m`

#### admin-10.11 · Check a proposed version like input from a stranger
As platform admin, I need every proposed version checked as strictly as user input, so that a blank field or an impossible setting is stopped before it spreads. §16.3, §15.3 ("A global AI endpoint shall not be assumed to satisfy a regional-residency promise"); A15, A16, P5 as corrected in r1-refute-b, P11 as corrected in r1-refute-b.
- Given a proposed `meal` version on `gemini-3.8-flash` with thinking level "minimal", When it is saved, Then:
  - the field reads "gemini-3.8-flash doesn't support thinking level minimal. Choose low, medium or high.";
  - nothing is saved. `/r`
- Given an empty prompt version, a timeout of 0 s or max output tokens above the model's limit, When it is saved, Then:
  - each error appears next to its field;
  - the API returns 422 `VALIDATION_ERROR` listing every failing field. `/r`
- Given the location field, When the Propose form loads, Then:
  - it shows the location set by the residency decision ("global" until that decision, map §1.7), read-only;
  - a note reads "Changed only by the residency decision". `/r`
- Given the version checker, When it is run against generated malformed versions (blank, oversized, wrong type), Then none is accepted. `/m`

#### admin-10.12 · Provider data settings are fixed and recorded
As platform admin, I keep the AI provider's data settings in one place, enforced where code can enforce them and recorded with a date where they are project settings, so that we never promise zero retention without checking. §19.1, FR-079; P14, R17, R18 as corrected.
- Given **Settings** › launch gates › AI provider data, When `admin.a` opens it, Then it lists:
  - "Request storage: off (store=false on every request)" and "Grounding with Google Search: off", both enforced by code;
  - "24-hour model cache: recorded as disabled · by admin.a · 1 Oct 2026 · source page" and "Abuse-monitoring logging exception: recorded as not requested · by admin.a · 1 Oct 2026". `/r`
- Given a recorded setting older than 90 days, When the page opens, Then that row reads "Re-check due" and the Registry banner names it. `/r`
- Given a proposed version that turns on grounding or request storage, When it is saved, Then it returns 422 `VALIDATION_ERROR` "Not allowed by the provider data settings". `/r`
- Given the adapter, When any request is sent to the provider mock, Then the mock's request log shows `store=false` and no grounding tool. `/s`

#### D · The regression set and offline evaluation

#### admin-10.13 · Keep the regression set and check it against the launch minimum
As platform admin, I keep the regression set the brief describes and see how far it is from the launch minimum, so that a "pass" means something. §16.4 ("a regression set of food photos, scale readings, bilingual labels, Arabic voice commands, ingredient variants, and adversarial instructions"), NFR-10 ("At least 200 consented target-cuisine test cases and 100 bilingual labels at launch").
- Given the regression set holds 300 synthetic cases (40 of them bilingual labels) and 0 consented cases, When the regression set on **Registry** opens, Then:
  - it shows a count for each of the six kinds in §16.4;
  - it reads "Below the launch minimum: consented target-cuisine cases 0 of 200 · bilingual labels 40 of 100 · synthetic cases don't count toward the minimum". `/r`
- Given `admin.a` adds a synthetic scale-reading case with an input image, an expected reading of "71.7 g" and an expected Evidence of measured (AT-01's fixture), When it is saved, Then the scale-reading count rises by one and the case shows its expected result. `/r`
- Given a new case without an expected result, When it is saved, Then the form reads "Add the expected result" and nothing is saved. `/r`
- Given the set is below the launch minimum, When any evaluation report opens, Then it carries the same "Below the launch minimum" line at the top. `/r`

#### admin-10.14 · Consented cases only, seen only by a restricted role
As platform admin, I add real cases only from eaters who gave the optional research Consent, and only a role allowed to see raw evidence can open them, so that evaluation never uses anyone's food photo without permission. FR-076 ("optional research use"), FR-079, §19.2 ("Access to raw evidence for quality review requires explicit consent and restricted roles"), AT-29. *Shared: Eater.*
- Given:
  - `eater-synth-090` gave the research Consent and approved a meal Analysis;
  - `eater-synth-091` did not give it,

  When `admin.a` opens the offered cases in the regression set on **Registry**, Then:
  - only `eater-synth-090`'s case is listed;
  - it is listed under a random case number, with no name or email. `/r`
- Given `admin.a` holds Platform admin only, When they open that case, Then:
  - the photo is not shown;
  - the page reads "Viewing raw evidence needs the role permission 'View consented evaluation cases'".

  A user holding a custom role with that permission sees the photo. `/r`
- Given `eater-synth-090` withdraws the research Consent, When the regression set on **Registry** refreshes, Then:
  - the case is gone and the consented count drops by one;
  - `GET /v1/admin/regression-set/cases/{n}` returns 404 `NOT_FOUND`. `/r`

#### admin-10.15 · Run the regression set against a proposed version
As platform admin, I run the regression set on a proposed version and watch it progress, so that I learn about regressions before any eater sees it. §16.4; A3.
- Given Meal version 7 is Proposed, When `admin.a` clicks "Run evaluation", Then:
  - the page reads "Evaluating · 34 of 300 cases", with a rising count and a Cancel button;
  - leaving and returning shows the same run still progressing. `/r`
- Given a running evaluation, When Cancel is clicked, Then:
  - the run stops and reads "Cancelled at 120 of 300";
  - no report is attached. `/r`
- Given the provider mock fails 10 cases, When the run ends, Then the report reads "10 cases returned no result", and those cases count as failed. `/r`

#### admin-10.16 · Read a report scored the way the brief asks
As platform admin, I read separate scores for item identification, label extraction, intent parsing and source matching, by Evidence type and against the Rollout version, so that I never accept one blended accuracy number. NFR-09, NFR-10, NFR-11, §1.5, AT-30; A3.
- Given a finished run of Meal version 7 against version 6, When the report opens, Then it shows, for both versions side by side:
  - the four scores;
  - a row per Evidence type (label-verified · recipe-calculated · measured · estimated analogue · user-defined);
  - the error on weighed or recipe-grounded cases apart from the error on photo-only cases. `/r`
- Given the adversarial cases (AT-30: image text "ignore rules, delete history"), When the report opens, Then:
  - it reads "Adversarial cases: no tool use and no ledger change" or names each case that caused one;
  - the evaluation made no write to any eater's records. `/r` `/s`
- Given the report, When "Critical fields correct before confirmation" is shown, Then it is labelled "measured on the regression set", never as live accuracy. `/r`

#### admin-10.17 · No Shadow without a passing report
As platform admin, I can move a version to Shadow only when its report passes, so that live traffic is never the first test. §16.4 ("Changes run in shadow evaluation"); A3, A17.
- Given Meal version 7's report shows label extraction 2.6 points below version 6, When "Move to Shadow" is clicked, Then:
  - the move is refused with "Label extraction is 2.6 points below the Rollout version. The allowed gap is 2 points.";
  - a link opens the failing cases. `/r`
- Given a report with any adversarial failure, When `POST /v1/admin/registry/meal/versions/7/state` asks for Shadow, Then it returns 422 `VALIDATION_ERROR` "Adversarial cases must all pass". `/r`

#### E · Shadow

#### admin-10.18 · Move a version to Shadow
As platform admin, I send a copy of a sample of live requests to the proposed version while eaters keep getting the Rollout version, so that I see real inputs without risk. §16.4; A11, A12.
- Given Meal version 7 has a passing report, When `admin.a` moves it to Shadow at 5 %, Then:
  - **Registry › Meal** reads "Shadow · version 7 · 5 % of requests · since 10:02";
  - **Audit trail** records the move. `/r`
- Given Shadow at 100 % on the mock, When `eater-synth-010` posts a meal photo to `POST /v1/analyses`, Then:
  - the eater's Analysis is stamped `meal@v6`;
  - the Shadow request count on **Registry › Meal** rises by one. `/r`
- Given a Shadow share of 0 or above 100, When it is typed, Then the field reads "Enter 1 to 100". `/r`

#### admin-10.19 · Shadow output never reaches an eater or the ledger
As platform admin, I need Shadow results kept only as comparison numbers, so that a version in Shadow can never change an Analysis an eater reviews, an Entry or a Day. §1.4, §16.2, FR-045; A12. *Shared: Eater.*
- Given Shadow at 100 % and `eater-synth-010`'s meal photo, When the eater opens that Analysis on **Capture & Plan** in the iOS simulator, Then every chip matches the Analysis returned by `GET /v1/analyses/{id}`, which is stamped `meal@v6`. `/r`
- Given the same request, When `GET /v1/reports/day` is called for that eater and Day, Then the totals and revision equal those read before the photo. `/r`
- Given the Shadow comparison store, When it is read after the request, Then it holds only numbers (schema-valid, validation codes, item count, food overlap, latency, tokens), and no output text, photo or transcript. `/s`

#### admin-10.20 · Read the Shadow comparison and know when it is enough
As platform admin, I read Shadow against Rollout on a few attributable numbers with a minimum sample, so that "ready for Canary" means something. §16.4; A11, A13.
- Given Shadow has run on 140 requests from 22 eaters, When **Registry › Meal** opens, Then it shows, for version 7 against version 6:
  - schema-valid rate, validation-pass rate, food agreement, p95 latency, error rate and estimated cost per Analysis;
  - "Needs 60 more requests and 8 more eaters before Canary". `/r`
- Given 200 requests from 30 eaters with every number within its limit, When the page refreshes, Then it reads "Ready for Canary" and "Move to Canary" is enabled. `/r`
- Given the comparison, When numbers are aggregated, Then no interval is longer than the time the version has been in Shadow (A11). `/m`

#### admin-10.21 · Shadow respects Consent and the kill switch
As platform admin, I need Shadow to send nothing for eaters who withdrew Consent and nothing at all while the kill switch is On, so that evaluation never widens what we send to Google. FR-076, §16.3 step 1; R22. *Shared: Eater.*
- Given `eater-synth-011` has withdrawn the Consent to send photos to Google's AI, When they take a meal photo on **Capture & Plan** in the iOS simulator, Then:
  - the app shows the Consent explanation with the manual logging paths and sends no Analysis request;
  - a direct `POST /v1/analyses` with their token returns 403 `CONSENT_REQUIRED`;
  - the Shadow count on **Registry › Meal** does not change. `/r`
- Given Shadow at 100 % and the kill switch On for `meal`, When any eater posts a meal photo, Then:
  - the provider mock's request log shows no call to either version;
  - **Registry › Meal** reads "Shadow · no traffic while the kill switch is On". `/r` `/s`

#### F · Canary

#### admin-10.22 · Move to Canary for a small, sticky share of eaters
As platform admin, I move the version to a share of eaters, each held in their group, with an equal control group, so that I compare like with like. §16.4 ("a small canary"); A7, A9, A11.
- Given Meal version 7 reads "Ready for Canary", When `admin.a` clicks "Move to Canary" at 5 %, Then:
  - a confirmation reads "About 5 % of eaters will get Meal version 7 in their Analyses; another 5 % are the control group.";
  - its focused default button is "Cancel" and its action button is "Move to Canary";
  - after confirming, the row reads "Canary: version 7 · 5 % of eaters". `/r`
- Given Canary at 10 % across the 400 synthetic eaters, When each eater posts one meal photo twice, Then:
  - read through each eater's `GET /v1/analyses/{id}`, between 25 and 55 eaters' Analyses are stamped `meal@v7`;
  - every eater gets the same version both times. `/r`
- Given a Canary share of 60 %, When it is entered, Then the field reads "Canary can reach at most 50 %. Use Move to Rollout for everyone." `/r`

#### admin-10.23 · Read the Canary checks, with acceptance by Evidence type
As platform admin, I compare the Canary group with its control on acceptance by Evidence type, validation failures, unavailability and latency, so that the decision rests on what eaters actually approved. §16.4, NFR-03, NFR-09; A3, A11. *Shared: Nutrition approver (reads acceptance on **Metrics**).*
- Given the Canary and control groups each have at least 200 Analyses from at least 30 eaters, When **Registry › Meal** opens, Then each group shows:
  - one row per Evidence type, with the shares of items approved unchanged, approved with edits and discarded;
  - the validation failure rate, the rate of `AI_UNAVAILABLE` and p95 latency against the 12 s target. `/r`
- Given either group is below the minimum sample, When the page opens, Then each check reads "Not enough data yet (84 of 200 Analyses)" instead of a percentage. `/r`

#### admin-10.24 · A failing check rolls the Canary back by itself
As platform admin, I want the Canary rolled back automatically when a check fails or the sample stays too small, with the reason shown, so that a bad version stops even when I am not watching. §16.4 ("rollback"), map §3 ("config live / rolled back"); A13, A17.
- Given Canary Meal version 7 at 10 %, When the mock returns schema-invalid output for version 7 on 30 % of calls and the minimum sample is met, Then:
  - within 10 s, new requests from Canary eaters are stamped `meal@v6`;
  - **Registry › Meal** reads "Rolled back · version 7 · validation failures 30 % against 1 % in control";
  - the same reason is in **Audit trail** and in a banner on every console section until dismissed. `/r`
- Given the Canary has not reached the minimum sample by the end of its set duration, When that duration ends, Then:
  - **Registry › Meal** reads "Rolled back · version 7 · too few Analyses to judge (84 of 200)";
  - **Audit trail** shows the same. `/r`

#### admin-10.25 · Change the Canary share without reshuffling eaters
As platform admin, I raise or lower the Canary share while each eater keeps their group, so that numbers stay comparable across steps. §16.4; A7.
- Given Canary at 5 %, When the share is raised to 20 % on **Registry › Meal**, Then:
  - read through `GET /v1/analyses/{id}`, every eater whose Analyses were stamped `meal@v7` before still gets `meal@v7`;
  - more eaters are added;
  - **Audit trail** shows "5 % → 20 %". `/r`
- Given Canary at 20 %, When the share is set to 0 % and later to 10 %, Then:
  - while at 0 %, every new Analysis is stamped `meal@v6`;
  - at 10 %, the eaters who had `meal@v7` before return to it (read through `GET /v1/analyses/{id}`). `/r`

#### G · Rollout and roll back

#### admin-10.26 · Move to Rollout, with a confirmation that says exactly what happens
As platform admin, I move a passing Canary to every eater with one specific confirmation, and the old version stays ready, so that the big step is deliberate and reversible. §16.4 ("before broad rollout"); A8, A27.
- Given Canary Meal version 7 passes every check, When "Move to Rollout" is clicked, Then:
  - a confirmation reads "Meal version 7 goes to every eater. Version 6 stays ready to roll back in one step.";
  - its focused default button is "Cancel" and its action button is "Move version 7 to Rollout". `/r`
- Given confirmation, When it completes, Then:
  - **Registry** reads "Rollout: version 7" and "Previous Rollout version: 6 (roll-back target)";
  - the Canary and control groups are released. `/r`
- Given Meal version 7 is Rolled back, When `POST /v1/admin/registry/meal/versions/7/state` asks for Rollout, Then it returns 422 `VALIDATION_ERROR` "Version 7 was rolled back. Propose a new version." `/r`

#### admin-10.27 · Roll back in one step
As platform admin, I return a task to its previous Rollout version in one action that takes effect within the propagation time, so that a bad version stops at once and manual logging never notices. WF-10 done-when, §16.4, map §3; A8, A15.
- Given Meal version 7 in Rollout with roll-back target version 6, When `admin.a` clicks "Roll back to version 6", Then:
  - the confirmation's focused default is "Cancel" and its action button is "Roll back to version 6";
  - after confirming, within 10 s every new `POST /v1/analyses` for `meal` returns an Analysis stamped `meal@v6`. `/r`
- Given an Analysis that started on version 7 at 10:00:00, When the roll back happens at 10:00:01, Then, read through `GET /v1/analyses/{id}`:
  - that Analysis completes stamped `meal@v7`;
  - the next one is stamped `meal@v6`. `/r`
- Given the roll back, When `eater-synth-012` logs a recent Unit with `POST /v1/consumption` during it, Then the Entry is Confirmed and the Day revision rises by one. `/r`

#### admin-10.28 · Every Analysis carries its full configuration
As platform admin, I need every Analysis stamped with the model id, prompt version, extraction schema version, nutrition algorithm version, source versions and the actual processing location, so that any result can be traced and reproduced. §16.4 ("Store model ID, prompt version, extraction schema version, nutrition algorithm version, and source versions with each analysis"), §15.3 ("log the actual processing configuration"), §17 AIAnalysis. *Shared: Eater (reads their own Analysis).*
- Given `label` is in Rollout at version 3, When `eater-synth-020` posts a label photo and reads `GET /v1/analyses/{id}`, Then the Analysis includes:
  - `registry_version: "label@v3"`, `model_id`, `prompt_version` and `schema_version`;
  - `nutrition_algorithm_version`;
  - `source_versions` (the Food reference version ids used by the resolver);
  - `processing_location`. `/r`
- Given the Analysis store, When an Analysis is written with any of these stamps missing, Then the write is refused. `/m`

#### admin-10.29 · A Registry change never rewrites history
As platform admin, I need Rollouts and roll backs to leave Entries, Units and Days exactly as they were, so that changing a model cannot silently change what someone ate. §1.4, FR-031, FR-042, NFR-01. *Shared: Eater.*
- Given `eater-synth-021` approved two Entries from `meal@v7` Analyses yesterday, When `meal` is rolled back to version 6, Then `GET /v1/reports/day` for yesterday returns the same totals and revision as before. `/r`
- Given the same eater, When they open **Today** and **Progress** in the iOS simulator, Then yesterday's figures are unchanged. `/r`

#### admin-10.30 · Two admins editing at once
As platform admin, I am told when someone else changed the Registry since I loaded it, so that I never overwrite their change unseen. §18.2 ("A 409 conflict returns the current revision"); A8.
- Given `admin.a` and `admin.b` both opened **Registry › Meal** at revision 41, When `admin.b` raises the Canary to 20 % and then `admin.a` saves a timeout change, Then:
  - `admin.a` sees "The Registry changed since you opened it: admin.b raised Canary from 5 % to 20 %. Review and save again." with the new state loaded;
  - none of `admin.a`'s change is saved. `/r`
- Given the same, When `admin.a`'s save is sent with revision 41, Then the API returns 409 `STALE_REVISION` with the current revision 42. `/r`

#### H · Kill switch

#### admin-10.31 · Turn the kill switch on for one task
As platform admin, I turn the kill switch on for one task without a release, so that a misbehaving model stops at once while everything else keeps working. §16.4 ("A kill switch must preserve manual and cached logging"), §7.2, NFR-05; A14, A15.
- Given **Registry** reading "Meal · Kill switch Off", When `admin.a` clicks "Turn kill switch on for Meal", Then a sheet reads "Meal analysis stops for every eater. They can still log from My Units, Templates and typed amounts. New photo analyses fail at once and nothing is sent later.", with:
  - the focused default button "Cancel";
  - a separate warning-styled button "Turn kill switch on for Meal".

  After the second is chosen, the row reads "Kill switch On · by admin.a · 22:14". `/r`
- Given the kill switch On for `meal`, When `eater-synth-030` calls `POST /v1/analyses` for `meal` 10 s after the switch, Then:
  - it returns 503 with code `AI_UNAVAILABLE` at once;
  - the provider mock's request log shows no call. `/r` `/s`
- Given the kill switch On for `meal` only, When the same eater posts typed text to `POST /v1/analyses` for `text`, Then:
  - it returns 200 with an Analysis that is Ready for review;
  - the Analysis is stamped `text@v4`. `/r`

#### admin-10.32 · Turn the kill switch on for every task
As platform admin, I can turn the kill switch on for every task at once, so that a provider-wide or cost emergency has one control. §16.4; A14, A16.
- Given **Registry**, When "Turn kill switch on for all tasks" is chosen in its sheet, Then:
  - the sheet listed Meal, Label, Scale, Recipe, Text and Voice by name, with "Cancel" as its focused default;
  - after confirming, every row reads "Kill switch On";
  - every `POST /v1/analyses` returns `AI_UNAVAILABLE` within 10 s. `/r`
- Given the kill switch On for every task, When any console section opens, Then a banner reads "Kill switch On for all tasks since 22:14 · Turn it off". `/r`

#### admin-10.33 · Manual and cached logging keep working while the kill switch is On
As platform admin, I can rely on the kill switch never blocking food logging, so that the switch is safe to use at once. §7.2, §16.4, NFR-05, AT-32. *Shared: Eater.*
- Given the kill switch On for every task, When `eater-synth-031` uses `POST /v1/consumption` to log a recent Unit, copy yesterday's breakfast, log a Template and enter a typed amount, Then:
  - each returns a Confirmed Entry and a new Day revision;
  - `GET /v1/reports/day` reconciles. `/r`
- Given the kill switch On for every task, When the eater opens **Capture & Plan** in the iOS simulator, Then:
  - photo and voice capture show that analysis is off;
  - **My Units**, Templates and a typed amount stay available. `/r`
- Given an Analysis that is Ready for review when the kill switch is turned on, When the eater approves it, Then:
  - the approval commits through `POST /v1/consumption`, because the switch stops model calls and not approvals;
  - the review screen did not change under the eater (A10). `/r`

#### admin-10.34 · Nothing is queued while the kill switch is On, and nothing is sent later without the eater
As platform admin, I need an AI request made while the kill switch is On to fail at once, with nothing queued, and never to be sent or consumed later unless the eater acts, so that turning the switch off cannot release a burst of old requests or create food the eater did not approve. §7.2 ("Reconnection shall not silently post it as consumed"; "AI outage shall not block recent-unit logging"), FR-045, AT-32; vocabulary D2 (kill switch: "nothing is queued to send later"). *Shared: Eater.*
- Given the kill switch On for `meal`, When `eater-synth-032` takes a meal photo on **Capture & Plan** in the iOS simulator, Then:
  - `POST /v1/analyses` returns 503 `AI_UNAVAILABLE` at once;
  - the Analysis shows Failed, with "Try again" and the manual logging paths;
  - **Today**'s consumed total is unchanged. `/r`
- Given the kill switch is turned Off, When 10 minutes pass without the eater touching that Failed Analysis, Then:
  - the provider mock's request log shows no call for it;
  - the server holds no queued request for it. `/r` `/s`
- Given the kill switch is Off, When the eater taps "Try again" on that Analysis, Then:
  - it goes Processing and then Ready for review;
  - nothing is added to the Day until the eater approves. `/r`

#### admin-10.35 · Turn the kill switch off again
As platform admin, I turn the kill switch off with the same control, and the task resumes the state it had, so that recovery is as simple as stopping. §16.4; A27.
- Given the kill switch On for `meal` with Meal version 7 in Canary at 10 %, When "Turn kill switch off for Meal" is clicked, Then:
  - the row reads "Kill switch Off";
  - the Canary cell reads "Canary: version 7 · 10 % of eaters";
  - requests are answered within 10 s, as `POST /v1/analyses` shows. `/r`
- Given the kill switch Off again, When **Audit trail** opens, Then it shows the On and Off entries with times, who acted, and "Kill switch was On for 41 minutes". `/r`

#### admin-10.36 · The switch works at phone width, and is never queued or faked
As platform admin on call, I reach the kill switch at phone width and see only what the server confirmed, so that I can act from anywhere and never believe a switch happened when it did not. §16.4, blueprint §0 ("the admin console proved in a browser at desktop and ~390 px"); A15.
- Given the console at 390 CSS px wide, When **Registry** loads, Then:
  - every task's kill-switch control and "Turn kill switch on for all tasks" are visible without horizontal scrolling;
  - each control's hit area is at least 44 × 44 CSS px. `/r`
- Given the network is down, When "Turn kill switch on for Meal" is confirmed, Then:
  - the row reads "Not sent: no connection. Try again." with a Try again button, and keeps reading "Kill switch Off";
  - when the connection returns, nothing is sent until `admin.a` presses Try again. `/r`
- Given a confirmed switch, When the page is reloaded, Then the state shown comes from `GET /v1/admin/registry`. `/r`

#### admin-10.37 · The switch stands apart from everything else
As platform admin, I need the kill switch to outrank every state, survive restarts and work even when the Registry cannot be changed, so that it is there when needed. §16.4, NFR-05; A14, A15, A16.
- Given the kill switch On for `meal`, When the API service restarts, Then `POST /v1/analyses` for `meal` still returns `AI_UNAVAILABLE`. `/r`
- Given a Registry save that fails validation, When the kill switch is used straight after, Then it still takes effect within 10 s, because the switch is stored and read apart from Registry versions. `/r` `/s`
- Given the kill switch On for `meal` with Meal version 7 in Canary, When **Registry › Meal** opens, Then:
  - the Canary panel reads "No traffic while the kill switch is On";
  - its Analysis count stays the same. `/r`

#### I · Quotas and cost

#### admin-10.38 · Set per-user daily AI quotas
As platform admin, I set soft and hard daily limits per kind of task, separately for signed-in accounts and anonymous sessions, so that one person cannot exhaust the shared project quota or the budget. §16.5, §23.3 ("soft/hard service quotas"), FR-001, map §6 (Registry: per-user daily AI quotas); A4, A20.
- Given the quotas panel on **Registry**, When `admin.a` saves:
  - image tasks (Meal, Label, Scale, Recipe) at soft 15 and hard 25;
  - Text and Voice at soft 60 and hard 100;
  - anonymous sessions at hard 3 for image tasks and hard 10 for Text and Voice,

  Then "Quotas version 4" is in use, and **Audit trail** shows before and after. `/r`
- Given a hard limit below its soft limit, a negative number or a fraction, When it is typed, Then the field shows the problem and `PUT /v1/admin/quotas` returns 422 `VALIDATION_ERROR`. `/r`

#### admin-10.39 · Roll back a quotas version
As platform admin, I return quotas to their previous version in one step, so that a limit set too tight is undone at once. Map §3 ("config live / rolled back"), map §6; A8.
- Given:
  - quotas version 4 set image tasks to hard 10;
  - version 3 had hard 25;
  - `eater-synth-044` has used 12 image Analyses today.

  When `admin.a` clicks "Roll back to quotas version 3" and confirms (focused default "Cancel"), Then:
  - the quotas panel on **Registry** reads "Quotas version 3 in use · rolled back from version 4";
  - within 10 s, `eater-synth-044`'s next `POST /v1/analyses` for `meal` returns 200. `/r`
- Given the roll back, When **Audit trail** opens, Then it shows "Quotas version 4 → 3 · rolled back by admin.a" with the reason. `/r`

#### admin-10.40 · A signed-in eater who reaches the hard limit gets a clear answer and the manual paths
As platform admin, I need an eater over the hard limit to get a typed answer with the reset time while their manual paths keep working, so that a quota never feels like a broken app. §18.2 (`RATE_LIMITED`), §23.3 ("graceful manual fallbacks"), NFR-05. *Shared: Eater.*
- Given a hard limit of 3 image Analyses, and `eater-synth-040` has used 3 today, When they call `POST /v1/analyses` for `meal`, Then it returns 429 with code `RATE_LIMITED` and `resets_at` in the eater's local time. `/r`
- Given the same eater in the iOS simulator, When they take a meal photo on **Capture & Plan**, Then:
  - a note gives the reset time;
  - logging from **My Units**, a Template or a typed amount still works. `/r`
- Given the same eater, When they call `POST /v1/consumption` with a recent Unit, Then it returns a Confirmed Entry. `/r`

#### admin-10.41 · An anonymous session reaches its own limit
As platform admin, I need local-trial anonymous sessions held to their own lower limit, so that cloud AI before sign-in stays bounded. FR-001 ("Cloud AI requires authenticated or anonymous-session access, consent, and quotas").
- Given an anonymous hard limit of 3 image Analyses, When `anon-synth-001` makes a 4th `meal` request with its anonymous-session token, Then it returns 429 `RATE_LIMITED` with `resets_at`. `/r`
- Given the same session in the iOS simulator, When the person logs a typed amount on **Today**, Then `POST /v1/consumption` with the anonymous-session token returns a Confirmed Entry that counts in the Day. `/r`
- Given `anon-synth-001` signs in during the same Day, When the next `meal` request is sent, Then it is counted against the signed-in limit (25), and the 3 analyses already made are carried over. `/r`

#### admin-10.42 · The soft limit counts and does not block
As platform admin, I use the soft limit to see how many eaters a tighter hard limit would affect, so that I tune quotas with evidence. §23.3.
- Given soft 15 and hard 25 for image tasks, When `eater-synth-041` makes the 16th image Analysis of their Day, Then:
  - it returns 200;
  - the quotas panel on **Registry** reads "Eaters over the soft limit today: fewer than 11", because the small-group rule applies (10.50). `/r`
- Given 12 synthetic eaters have passed the soft limit, When the page refreshes, Then it reads "12", and `GET /v1/admin/quotas/usage` returns the counts per kind of task with no eater ids. `/r`

#### admin-10.43 · Only new AI work counts toward a quota
As platform admin, I need a quota to count only fresh AI work, so that eaters are not charged for retries or repeat logs. §16.5 ("A confirmed repeated unit uses no new nutrition inference"; "rejected analyses and retries must also count toward operating cost"), §18.2, FR-043; AT-10.
- Given `eater-synth-042` logs the confirmed repeated Unit "cheese bite" by voice, resolved without new inference (§2.3), When `GET /v1/admin/quotas/usage` is read, Then the image count is unchanged. `/r`
- Given the same `POST /v1/analyses` command delivered three times with one command id (AT-10 pattern), When usage and cost are read, Then:
  - the quota counted one Analysis;
  - the estimated cost on **Metrics** includes every provider call made. `/r`
- Given an Analysis that the eater discarded, When usage is read, Then it counts toward both the quota and the cost. `/r`

#### admin-10.44 · The quota day follows the eater's diary day
As platform admin, I reset each eater's quota at their own diary-day boundary, so that an eater with a late boundary is not reset in the middle of their evening. §8.1 ("Default diary days follow the user's configured local day boundary"), FR-044; A29 for why late boundaries occur. `assumption`: the brief does not say which day a quota uses (see §6).
- Given `eater-synth-043` in Asia/Riyadh with a diary-day boundary of 04:00, who used the hard limit by 02:30, When they call `POST /v1/analyses` for `meal` at 03:59, Then it returns `RATE_LIMITED` with `resets_at` 04:00 local; at 04:00 the call returns 200. `/r`

#### admin-10.45 · Keep effective-dated prices
As platform admin, I record provider prices per model with effective dates on the prices panel of **Metrics**, so that each call is priced at the rate of its own day and a known change is ready in advance. §16.5, §23.3 ("Do not embed temporary provider prices in core requirements"); A18.
- Given the prices panel on **Metrics**, When `admin.a` enters the following for `gemini-3.8-flash`, Then both rows show with their effective dates and the note "output includes thinking tokens":
  - input $0.75 and output $3.75 per million tokens from 2 Sep 2026;
  - input $1.50 and output $7.50 from 1 Jan 2027;
  - the source page. `/r`
- Given those prices, When an Analysis at 2026-12-31 23:59:59 UTC and one at 2027-01-01 00:00:00 UTC each use 1,000 input and 500 output tokens, Then:
  - their costs are stored unrounded as $0.002625 and $0.00525;
  - **Metrics** shows them as $0.0026 and $0.0053 (four decimals, half up). `/r` `/m`
- Given a price that is already in effect, When anyone tries to edit it, Then the edit is refused with "Add a new effective date instead", and earlier costs never change. `/r`

#### admin-10.46 · No price, no Canary
As platform admin, I cannot expose eaters to a model that has no price on **Metrics**, so that cost is never unknown in production. §16.5 ("Meter cost per accepted unit and confirmed meal").
- Given Meal version 8 uses a model with no price on **Metrics**, When it is moved to Canary, Then:
  - the move is refused with "No price for this model. Add one on Metrics.";
  - Shadow is still allowed, with the cost shown as "No price". `/r`

#### admin-10.47 · Read cost per Saved Unit and per confirmed meal
As platform admin, I read the estimated cost per Saved Unit (the brief's "accepted unit") and per confirmed meal, including discarded Analyses, retries and Shadow calls, so that I see the real price of AI per useful outcome. §16.5; A19.
- Given the last 7 days hold:
  - 1,000 meal Analyses, of which 200 were discarded and 50 retried;
  - 100 Shadow calls;
  - 600 confirmed meals and 90 Saved Units,

  When **Metrics** opens its cost view on "Last 7 days", Then it shows:
  - the estimated cost, the cost per confirmed meal and the cost per Saved Unit, split by task and by Registry version;
  - the line "Estimated from token counts and the prices on Metrics; this is not your bill". `/r`
- Given an Analysis's usage metadata, When it is priced, Then input, output, thinking and cached tokens are each priced at their own rate. `/m`
- Given no Analyses in the period, When the page opens, Then it reads "No AI use in this period", not "$0.00 per meal". `/r`

#### admin-10.48 · A daily budget warns, then turns the kill switch on by itself
As platform admin, I set a daily soft budget that warns and a hard budget that turns the kill switch on for every task, so that a bug or abuse cannot run up a surprise bill. §16.5, §23.3; A4.
- Given a daily budget of soft $40 and hard $60 (UTC day), When the estimated cost passes $40, Then a banner and a **Audit trail** entry read "AI spend today $40.12 of $60". `/r`
- Given the estimated cost passes $60, When the next `POST /v1/analyses` arrives, Then:
  - **Registry** reads "Kill switch On for all tasks · budget reached 21:47 UTC";
  - the call returns `AI_UNAVAILABLE`;
  - `POST /v1/consumption` keeps working. `/r`
- Given the budget was reached, When the UTC day rolls over or `admin.a` raises the hard budget, Then the kill switch stays On until an admin turns it off, and the banner says so. `/r`

#### J · Metrics (quality)

#### admin-10.49 · Acceptance by Evidence type, per task and per version
As platform admin, I read how often eaters approve AI items unchanged, edit them or discard them, by Evidence type, task and Registry version, so that I see live quality without opening anyone's food. FR-080 ("de-identified quality metrics"), NFR-09, NFR-10; A3. *Shared: Nutrition approver (a high "estimated analogue" share points to missing Food records).*
- Given 28 days of synthetic Analyses, When **Metrics** opens on "Meal · version 7 against version 6", Then a table shows, for each Evidence type:
  - approved unchanged %, approved with edits % and discarded %;
  - the number of items behind each row. `/r`
- Given the same filter, When `GET /v1/admin/metrics/quality?task=meal&versions=6,7` is called, Then it returns the same figures with no eater id, Analysis id, photo reference or text. `/r`
- Given the percentages in a row, When they are shown, Then they total 100.0 % by the largest-remainder method (§10.2). `/r`

#### admin-10.50 · Small groups are hidden, and cannot be worked out
As platform admin, I see "fewer than 11 eaters" instead of any figure from a group that small, and the table never lets me derive it, so that nobody can be picked out. FR-080 ("de-identified"); A25, A26.
- Given the "user-defined" row for Scale version 2 comes from 7 distinct eaters, When **Metrics** opens, Then that row reads "Fewer than 11 eaters · hidden" with no percentages or counts. `/r`
- Given rows from 7, 40, 120 and 300 eaters with a total of 467, When **Metrics** opens, Then:
  - the 7-row and the next-smallest row (40) are both hidden (complementary suppression);
  - the total 467 is shown, so subtraction gives only the sum of two hidden rows. `/r` `/m`

#### admin-10.51 · No path from a metric to a person
As platform admin, I cannot open a single eater's Analysis, photo or transcript from any metric, so that de-identified stays de-identified. FR-080, §19.2, NFR-07.
- Given **Metrics**, When any figure is clicked, Then it opens the metric's definition, never a list of Analyses or eaters. `/r`
- Given `admin.a`'s token, When `GET /v1/analyses/{id}` is called for `eater-synth-050`'s Analysis, Then it returns 404 `NOT_FOUND`, as for any id that is not the caller's. `/r`
- Given the metrics store, When it is read, Then it holds aggregates only: task, version, Evidence type, language, day and counts. `/s`

#### admin-10.52 · Validation failures and clarification counts per version
As platform admin, I see the typed validation failures and clarification counts per version, so that I can find a prompt that confuses the model. §16.3, §18.2, FR-035.
- Given 28 days of data, When the validation panel on **Metrics** opens for Recipe, Then it shows:
  - counts of mass-balance errors, unknown source basis, ambiguous Unit and incomplete macros per version;
  - the share of Analyses that needed 0, 1 or 2 clarification questions. `/r`
- Given a version with no failures, When the page opens, Then it reads "No validation failures in this period". `/r`

#### admin-10.53 · Acceptance by language, for the Arabic voice question
As platform admin, I split Voice and Text acceptance by input language (English, Arabic, mixed), so that we see how Arabic voice really performs. Map §1.7 open question (Arabic voice), FR-036, NFR-10; P15 as narrowed in r1-refute-b.
- Given 28 days of synthetic Voice and Text Analyses tagged English, Arabic and mixed, When **Metrics** is filtered to Voice, Then:
  - acceptance shows one column per language;
  - the Arabic column header reads "Arabic (the transcription model lists Egyptian Arabic only)". `/r`
- Given the mixed column has 9 eaters, When the page opens, Then that column is hidden by the small-group rule. `/r`

#### admin-10.54 · Empty, loading and failed states on Metrics
As platform admin, I always know whether **Metrics** is loading, empty or failed, so that I never read a blank as zero. FR-080.
- Given a new version with no Analyses, When **Metrics** is filtered to it, Then it reads "No Analyses on Meal version 8 yet. Figures appear after the first approvals." `/r`
- Given the metrics call takes longer than 1 s, When the page opens, Then table-shaped placeholders appear. `/r`
- Given the metrics call fails, When the page opens, Then the table area reads "Couldn't load figures." with a Try again button. `/r`

#### K · Failed AI jobs

#### admin-10.55 · See failed AI jobs, de-identified
As platform admin, I see every failed analysis job with its error, attempts, age and Registry version, but no identifiers, so that I keep the AI machinery healthy without seeing who it was for. FR-080 ("failed jobs"), §18.2; A28. *Shared: Support agent (support sees one account's jobs).*
- Given failed `meal` analysis jobs, When **Jobs** is filtered to "AI analysis", Then each row shows:
  - a random job id, the task, the error in words (for example "AI unavailable") and the attempts as "2 of 5";
  - the first failure time, the last attempt time and the Registry version ("Meal version 7");
  - no eater name, email or user id. `/r`
- Given no failed jobs, When **Jobs** opens, Then it reads "No failed jobs · last checked 10:41". `/r`
- Given `GET /v1/admin/jobs?state=failed`, When it is called with `admin.a`'s token, Then the same rows are returned with no user ids. `/r`

#### admin-10.56 · Retry a failed analysis job safely
As platform admin, I retry a failed analysis job under its original command id, and the result is only an Analysis that is Ready for review, so that a retry can never create an Entry. §18.2 ("bounded retries with the same command ID"), FR-045, FR-043; AT-10. *Shared: Support agent.*
- Given a `meal` analysis job for `eater-synth-104` that failed with `AI_UNAVAILABLE`, When `admin.a` clicks "Retry" on **Jobs**, Then:
  - the job leaves the failed list;
  - `eater-synth-104` has one Analysis, Ready for review, for that command id;
  - `GET /v1/reports/day` for their Day is unchanged. `/r`
- Given the eater has withdrawn Consent or deleted their account since, When it is retried, Then the retry is refused as in admin-9.3. `/r`

#### admin-10.57 · Stop retrying what cannot succeed
As platform admin, I see when a job has used all its attempts and needs engineering, so that retries stay bounded. §18.2, NFR-12 ("bounded retries"); A28.
- Given a job that failed 5 of 5 attempts, When **Jobs** opens, Then:
  - the row reads "Stopped after 5 attempts · needs engineering";
  - Retry is disabled, with that reason beside it. `/r`
- Given the same job, When `POST /v1/admin/jobs/{id}/retry` is called, Then it returns 422 `VALIDATION_ERROR` "No attempts left". `/r`

#### L · Roles (permissions → roles → users, deny by default)

#### admin-10.58 · Read the permissions
As platform admin, I read the fixed permissions in plain words on **Roles › Permissions**, so that I build roles from what the code checks. Blueprint §0 line 3 (permissions → roles → users), FR-080, FR-081; A21. `assumption`: the list is proposed for the model phase, derived from FR-080, FR-081 and map §3.
- Given **Roles › Permissions**, When it opens, Then it lists each permission as a plain sentence with the sections it opens, and has no add or edit control. The sentences are:
  - "Read the Registry", "Propose Registry versions", "Move versions between states", "Roll back", "Use the kill switch";
  - "Change quotas", "Change prices", "Read Metrics", "Read failed jobs", "Retry failed jobs";
  - "Read roles", "Change roles", "Read the Audit trail for Registry, Roles and Jobs", "View consented evaluation cases";
  - "Approve Foods, Recipes and Aliases", "Propose and approve Policy versions";
  - "Read account state", "Request a Grant", "Read the whole Audit trail". `/r`
- Given the list, When the row "Read a diary inside an Active Grant" is shown, Then it reads "Only through a Grant · can't be added to a role". `/r`

#### admin-10.59 · The five personas' roles are seeded
As platform admin, I find the five persona roles in place on a fresh deployment, so that the console is safe from day one. Map §2 (five personas), FR-080, FR-081, blueprint §0 line 3.
- Given a fresh deployment, When **Roles** opens, Then it lists Eater, Nutrition approver, Support agent, Platform admin and Auditor, each with its permissions and the label "Seeded · read-only". `/r`
- Given the seeded role Eater, When it is opened, Then it reads "Given to every app account at sign-up · no console permissions". `/r`
- Given any seeded role, When `DELETE /v1/admin/roles/{id}` or a permission change is sent, Then it returns 422 `VALIDATION_ERROR` "Seeded roles can't be changed. Copy one to make your own." `/r`

#### admin-10.60 · Build a role from the permissions
As platform admin, I create a role that starts with nothing and tick the permissions it needs, so that a new job gets exactly what it needs. Blueprint §0 line 3 (roles screen), FR-080 (role-based console); A21.
- Given **Roles**, When `admin.a` clicks "New role", names it "Release reviewer" and saves with nothing ticked, Then:
  - the role is saved;
  - its page reads "This role grants nothing yet". `/r`
- Given "Release reviewer" with "Read the Registry" and "Read Metrics" ticked, When a holder opens the console, Then:
  - **Registry** is read-only and **Metrics** opens;
  - `POST /v1/admin/registry/meal/versions` returns 403 `FORBIDDEN`. `/r`
- Given a role name that is already used, When it is saved, Then the name field reads "A role with this name exists". `/r`

#### admin-10.61 · Assign or remove a role, see the effect first, and have it apply at once
As platform admin, I see what a user gains or loses before I save, and the change applies on their very next request, so that access changes are deliberate and immediate. Blueprint §0 line 3 (roles → users), FR-080, NFR-12 ("least privilege"), vocabulary D2 (staff and eater accounts); A21, A23.
- Given `approver.a` holds Nutrition approver, When `admin.a` adds "Release reviewer" on **Roles › Users**, Then:
  - before Save, a preview reads "Gains: Read the Registry";
  - after Save, **Audit trail** records it. `/r`
- Given `admin.b` holds Platform admin and has the console open, When `admin.a` removes that role, Then:
  - `admin.b`'s next API call returns 403 `FORBIDDEN`;
  - their console shows "Your access changed. Reload to continue." `/r`
- Given `eater-synth-070`, an eater account, When `admin.a` tries to give it Platform admin on **Roles**, Then:
  - Save is refused with "An eater account can't hold a staff role";
  - `PUT /v1/admin/users/eater-synth-070/roles` returns 422 `VALIDATION_ERROR` (vocabulary D2: "a staff account is never an eater account"). `/r`

#### admin-10.62 · Duties that must stay apart are kept apart
As platform admin, I am stopped from combining roles that must stay apart, so that support never also approves records or runs the Registry, and the auditor stays independent. FR-081 ("Separate support privileges from nutrition-approver and platform-admin privileges"); R24. `assumption`: the Auditor-with-a-writing-role block rests on R24's "separated duties".
- Given `support.a` holds Support agent, When `admin.a` adds Platform admin or Nutrition approver, Then Save is refused with "Support agent can't be combined with Platform admin or Nutrition approver". `/r`
- Given `auditor.a` holds Auditor, When any role that can change something is added, Then Save is refused with "Auditor stays read-only". `/r`
- Given a custom role with both "Request a Grant" and "Move versions between states", When it is saved, Then:
  - it is refused with "These permissions belong to duties that must stay apart";
  - the server's role check refuses the same combination by API. `/r` `/m`

#### admin-10.63 · No role can read a diary
As platform admin, I can never give any role a permission to read a diary, so that diary access exists only through a Grant the eater approved. FR-081, map §3 (Grant), WF-10 done-when. *Shared: Support agent, Auditor.*
- Given the role editor, When `admin.a` looks for "Read a diary inside an Active Grant", Then it is not offered, and `PUT /v1/admin/roles/{id}` including it returns 422 `VALIDATION_ERROR`. `/r`
- Given `admin.a`'s token, When it calls a diary endpoint for `eater-synth-060`, for example `GET /v1/reports/day?user=eater-synth-060`, Then it returns 404 `NOT_FOUND`. `/r`

#### admin-10.64 · Nobody changes their own roles, and one platform admin always remains
As platform admin, I cannot change my own roles and nobody can remove the last Platform admin, so that nobody can raise their own access or lock everyone out. FR-081 (separation of privileges), map §2 (the platform admin manages roles); A24, A27. `assumption` for the last-admin rule.
- Given `admin.a` opens their own row on **Roles › Users**, When the page renders, Then:
  - the role controls are disabled with "Another platform admin must change your roles";
  - a self-change by API returns 403 `FORBIDDEN`. `/r`
- Given:
  - `admin.a` is the only Platform admin;
  - `access.a` holds a custom role with "Change roles".

  When `access.a` removes Platform admin from `admin.a`, Then:
  - it is refused with "At least one platform admin must remain";
  - the API returns 422 `VALIDATION_ERROR`. `/r`

#### M · Launch gates on Settings (NFR-12)

NFR-12: "Per-user quotas, bounded retries, least privilege, secret rotation, dependency scans, backups, and tested restore". Delta D1 (blueprint §3) adds "supply chain (dependency audit, pinned versions, SBOM)" and "a backup restored once in the release proof (on the Firestore emulator while no hosting exists)". Rotation and scheduled backups in a hosted project wait with the dropped operate row (ledger). Until then the console shows the state the code can prove.

#### admin-10.65 · The AI provider credential rotates without ever being shown
As platform admin, I see that the AI provider credential is loaded and when, never its value, and a rotation needs no release, so that a leaked key is replaced fast and never spread. NFR-12 ("secret rotation"), §19.2, blueprint §0 line 6 ("secrets from the environment").
- Given **Settings** › launch gates, When it opens, Then:
  - it reads "AI provider credential: set · loaded 10:02 UTC";
  - no part of the value is shown;
  - `GET /v1/admin/launch-gates` returns `credential_present: true` and `loaded_at`, with no value. `/r`
- Given the API is restarted with a new synthetic credential in its environment, When **Settings** › launch gates refreshes, Then "loaded" shows the new time, and a `meal` Analysis on the mock succeeds. `/r`
- Given all service logs from the run, When they are searched for both credential values, Then neither is found. `/s`

#### admin-10.66 · Dependency audit and pinned versions are visible
As platform admin, I see the running build's dependency audit and SBOM, so that I know whether we ship a known-vulnerable package. NFR-12 ("dependency scans"), blueprint §3 D1 (supply chain).
- Given the running build, When **Settings** › launch gates opens, Then it shows:
  - the build id;
  - "Dependency audit: no known vulnerabilities · checked 1 Oct 2026";
  - a link to the SBOM for that build. `/r`
- Given a pinned dependency with a known vulnerability, When CI runs, Then the audit job fails the build and names the package. `/s`

#### admin-10.67 · A backup is restored and proved
As platform admin, I see when a backup was last restored and whether the restored data reproduced every Day, so that "we have backups" is a tested fact. NFR-12 ("backups, and tested restore"), FR-042 ("Replaying the ledger shall reproduce the totals"), blueprint §3 D1.
- Given the release proof restored the Firestore-emulator export of 400 synthetic eaters into a fresh emulator, When **Settings** › launch gates opens, Then it reads "Last restore test: passed 1 Oct 2026 · 400 eaters · every Day total matches". `/r`
- Given the restore test, When it replays the ledger in the restored copy, Then every `GET /v1/reports/day` total and revision equals the source. `/s`
- Given no restore test has run for this build, When **Settings** › launch gates opens, Then it reads "No restore test for this build yet", not "passed". `/r`

#### N · Audit trail, language, access

#### admin-10.68 · Every change is in the Audit trail, with a reason
As platform admin, I find every change with who, when, before and after and why: Registry versions, prompts, the regression set, quotas, prices, the kill switch, roles and provider data settings. This lets me and the auditor reconstruct any moment. FR-081, map §2 (the auditor reads "policy and registry history"); A8, A23. *Shared: Auditor (reads the same entries read-only, plus Grants and Consents, which the admin cannot see).*
- Given the changes made in stories 10.8–10.64, When **Audit trail** is filtered to Meal, Then:
  - each entry shows the actor, the time, before → after and the reason;
  - a difference view compares any two Meal Registry versions. `/r`
- Given a move between states, a roll back or a role change, When the reason field is empty, Then Save reads "Add a short reason". `/r`
- Given the kill switch, When it is used without a reason, Then:
  - it is not delayed;
  - **Audit trail** shows "reason not given" with an "Add note" button. `/r`
- Given `admin.a`, When `GET /v1/admin/audit-trail?kind=grant` is called, Then it returns 403 `FORBIDDEN`, and **Audit trail** offers no Grant or Consent filter. `/r`

#### admin-10.69 · The console in Arabic
As platform admin working in Arabic, I use the console right-to-left, with model ids, versions, numbers and times intact, so that nothing is misread in my language. Blueprint §0 line 5 (English + Arabic RTL); vocabulary D2 ("Arabic labels come from the string catalogue, one per English word").

The Arabic strings below are proposed for the string catalogue:

| English | Arabic |
|---|---|
| Registry | «السجل» |
| Meal | «الوجبة» |
| Kill switch Off | «مفتاح الإيقاف: غير مُفعَّل» |
| Kill switch On | «مفتاح الإيقاف: مُفعَّل» |
| Cancel | «إلغاء» |
| Turn kill switch on for Meal | «فعِّل مفتاح الإيقاف: الوجبة» |

- Given the console language set to Arabic on **Settings**, When **Registry** opens, Then:
  - the page title reads «السجل»;
  - navigation is on the right;
  - the Meal row reads «الوجبة» and «مفتاح الإيقاف: غير مُفعَّل»;
  - `gemini-3.8-flash` renders left-to-right inside the right-to-left row;
  - "5 %" reads «٥٪» with Arabic-Indic numerals chosen, or «5%» with Western numerals. `/r`
- Given the kill-switch sheet in Arabic, When it opens, Then:
  - the focused default button reads «إلغاء»;
  - the warning button reads «فعِّل مفتاح الإيقاف: الوجبة»;
  - the times in its text keep their digit order. `/r`

#### admin-10.70 · Times in my zone, stored in UTC
As platform admin, I read times in my own time zone with the zone shown, and the Audit trail stores UTC, so that on-call handovers across zones agree. Blueprint §0 line 5 (time zones).
- Given `admin.a` in Africa/Cairo and `admin.b` in Asia/Riyadh, When both open the same **Audit trail** entry, Then:
  - each sees it in their own zone with the zone label;
  - `GET /v1/admin/audit-trail` returns ISO 8601 UTC. `/r`

#### admin-10.71 · Keyboard, screen reader, contrast and zoom
As platform admin, I can do the whole Propose → Canary → Rollout → roll back → kill-switch flow by keyboard and screen reader, at high zoom, in light and dark, so that the console works for every admin. NFR-08 ("Screen-reader and text-scaling flows pass on every P0 journey"; the console is P0 by FR-080), blueprint §0 (console proved at desktop and ~390 px).
- Given **Registry** at 1280 CSS px, When `admin.a` uses only Tab, Shift+Tab, Enter, Space and Esc, Then they can propose Meal version 7, move it to Canary, move it to Rollout, roll back to version 6, and turn the kill switch on and off, and:
  - every focused control shows a visible focus ring;
  - Esc closes each confirmation without acting. `/r`
- Given a screen reader (VoiceOver with Safari) on **Registry**, When focus reaches Meal's kill-switch control, Then:
  - it announces "Meal, kill switch Off, switch";
  - after turning it on, it announces "Meal, kill switch On, switch". `/r`
- Given **Registry**, **Metrics** and **Roles** in light and in dark mode, When an automated accessibility check (axe-core) runs in the browser at 1280 and 390 CSS px, Then it reports no contrast failure below 4.5:1 for text, and no control without an accessible name. `/r`
- Given browser zoom at 200 % on desktop and enlarged text at 390 CSS px, When **Registry** and **Metrics** load, Then:
  - no text is clipped or overlapping;
  - tables reflow to stacked rows without horizontal page scrolling. `/r`

---

## 5 · The experience this persona needs

**Device and place.**
- Planned work happens on a desktop browser at 1280 CSS px or wider, through the whole Propose → Shadow → Canary → Rollout flow.
- Urgent work happens on a phone at about 390 CSS px.
- Both widths are proved, as blueprint §0 requires. That planned work is done at a desk and urgent work at night or on a weak network is an `assumption` (§1.3).

**The moment that matters.** "Something is wrong with the AI." From wherever they are, the admin:
- turns the kill switch on or rolls back, with effect within the chosen propagation time;
- sees the change confirmed by the server and never queued (10.36);
- knows that logging still works for every eater (10.31–10.37, 10.27).

The second moment is a move to Rollout: on numbers that are attributable, above a minimum sample, and few enough to trust (A11, A13).

**The feeling it must leave** (an `assumption` about the persona): calm control and certainty. "I know exactly what is live. I can undo it in one move. Nothing I do here changes what someone ate or lets anyone see a diary."

**The matching style.**
- Dense, fast and precise.
- Every state is written in words; colour only repeats them.
- Model ids, versions and numbers sit in a fixed-width font.
- One main action per view, and it is never the destructive one.
  - The kill-switch sheet's focused default is "Cancel"; its action button is separate and warning-styled.
  - The Rollout and roll-back confirmations default to "Cancel".
- The kill switch is one step from **Registry** at every width.
- Confirmations are rare and specific: move to Canary, move to Rollout, roll back, turn the kill switch on, roll back quotas. None asks the admin to type a word (A27).
- Nothing blinks, and nothing disappears on a timer.

### Care questions this persona raises, answered as requirements

Every question in `care.md` "The questions" is answered below, or marked not applicable (n/a) with its reason.

**1 · Does it deserve to exist, and where does it live**
1. **One sentence per screen.** Each section's sentence:
   - **Registry**: "What is every eater getting from the AI now, how much may each person use, and how do I change it safely?"
   - **Metrics**: "How often do eaters accept the AI's items, by Evidence type, and what does it cost per useful result?"
   - **Jobs**: "What background work failed, and can it be retried safely?"
   - **Roles**: "Who may do what?"
   - **Audit trail**: "What changed, when, by whom and why?"
   - **Settings** (launch gates): "Are the provider data settings, the credential, the dependencies and the restore proved?"

   Every element serves its section's sentence.
2. **Feeling and the no's.** The feeling is calm control (above). We said no to:
   - any per-eater quota exception (it needs identifying an eater);
   - schemas written in the console (C1);
   - free-form model ids;
   - changing a prompt in place;
   - opening a metric down to a person;
   - two versions in Shadow or Canary for one task at the same time;
   - type-a-word confirmations.
3. **Need first.** Every story starts from a brief or map line (§4 traces).
4. **Common path.** Reading **Registry** and using the kill switch are the common path. Prices, permissions, the regression set and the launch gates on **Settings** sit one level deeper.
5. **Places and things to do.** The seven sections are places in the navigation. Kill switch, Move to …, Roll back and Retry are actions placed next to what they act on.
6. **Settings that need not exist.** The Shadow and Canary shares, minimum samples and propagation start from chosen defaults (§3), so nobody is asked each time.
7. **Pop-ups.** There are only the five confirmations above. An evaluation runs inside the page with its progress shown.
8. **Changing an existing screen:** n/a. The console is new, with no existing users.

**2 · How it is found and understood**
1. **Where am I, what can I do, where next, how do I get out.**
   - Every page title names the section and task ("Registry › Meal").
   - Breadcrumbs lead back to the overview.
   - Esc closes any sheet (10.71).
2. **Titles name the place**, never the brand.
3. **Platform conventions.** Web conventions apply: links look like links, buttons look like buttons, and Esc cancels.
4. **One word per thing.** §3 sets the names: Proposed, Shadow, Canary, Rollout, Rolled back; Kill switch On and Off; Meal, Label, Scale, Recipe, Text, Voice. Each colour has one meaning:
   - red for Kill switch On and failures;
   - amber for warnings such as retirement, budget and re-check;
   - neutral for everything else,

   and every colour is always paired with words.
5. **No internal names.** Labels avoid them (§3 copy rule):
   - permissions are plain sentences (10.58);
   - errors name no requirement or finding id (10.3, 10.24, 10.62);
   - versions read "Meal version 7".
6. **Buttons are verbs**, and multi-step flows keep the same words: "Move to Canary", "Move to Rollout", "Roll back to version 6", "Turn kill switch on for Meal", "Retry".
7. **Defaults are right from the start.** Seeded roles, seeded Rollout versions and the starting values in §3 mean nothing needs setting first.
8. **No typing what the system knows.** Models, prompts and schema versions are chosen from lists. Only reasons and prices are typed.
9. **Tappable at a glance.** Every control is a bordered button or an underlined link. Table cells that are not controls carry no hover style.
10. **The squint test.** The eye lands on the Rollout and AI state of each row first. Retirement banners sit above the table.
11. **Key status where people look.** These banners show on every section:
    - "Kill switch On for all tasks" (10.32);
    - a roll back (10.24);
    - budget (10.48);
    - re-check due (10.12);
    - retirement (10.6).

**3 · How it feels**
1. **Every action answers what happened, what is happening now and what comes next:**
   - status while it runs ("Evaluating · 34 of 300");
   - confirmation only after the server confirms (10.36);
   - a warning before trouble (retirement, soft budget);
   - an error next to its field (10.11).
2. **Loudness matches importance.** Quiet bars for offline and soft limits. Banners for Kill switch On, roll back, budget and retirement. No alert for routine Canary progress.
3. **Response at the instant of touch.** Every button shows its pressed state within 100 ms (§3), then "Sending…" until the server answers.
4. **Changing your mind mid-way.** Esc or Cancel closes any confirmation before it acts, and a running evaluation can be cancelled (10.15). A sent switch is undone with the opposite switch.
5. **Animation:** n/a. The console uses no animation beyond the pressed state and placeholders.
6. **One main action, never destructive.** On each task page the main action is the next "Move to …". Roll back and Turn kill switch on are secondary, and every confirmation defaults to Cancel.
7. **Alignment.** All tables share one column grid and one row height. Spacing comes from tokens, never ad hoc.
8. **Values chosen on purpose.** Every value in the §3 table is tried in the served product and its choice is recorded.
9. **The first screen.** The console opens on the section and filter last used in that browser (stored per viewer). Data then loads behind table-shaped placeholders with no flash.

**4 · When it goes wrong, is empty or is slow**
1. **Empty states:**
   - "None · Propose a version" (10.1);
   - "No failed jobs" (10.55);
   - "No Analyses on Meal version 8 yet" (10.54);
   - "No AI use in this period" (10.47);
   - "This role grants nothing yet" (10.60).
2. **The first second while data loads.** Table-shaped placeholders appear (10.54).
3. **Long tasks.** Evaluation shows an honest count and a Cancel button (10.15).
4. **Errors** sit next to the problem, say how to fix it and never blame. There is no "oops" and no "we" (10.5, 10.11, 10.38).
5. **Fields are checked as they are typed** (shares, limits, dates). Obvious slips such as spaces in a model id are trimmed, not refused.
6. **Undo, and showing what it reversed:**
   - roll back (10.27), quotas roll back (10.39) and the kill switch turned Off (10.35) each undo the earlier action;
   - **Audit trail** shows exactly what each undid.
7. **Warnings only before unexpected, permanent loss.** Nothing in this console deletes permanently except seeded data, which cannot be deleted at all. Confirmations are kept only for changes that hit every eater at once.
8. **Half-filled forms.** A half-filled version is kept as Proposed (10.8). A half-filled role keeps its typed name in the browser until it is saved or discarded.
9. **Permissions asked at the moment of need:** n/a. The console asks for no device permission. Role refusals explain themselves (10.2, 10.3).
10. **No network.** The last data is shown with its time and a quiet bar. Changes are disabled, and the kill switch is never queued (10.1, 10.36).
11. **When a command can't work right now, we say why:**
    - "Version 7 was rolled back" (10.26);
    - "No attempts left" (10.57);
    - "No price for this model" (10.46);
    - "The kill switch is On for Meal" (admin-9.3).

**5 · The inside the user never sees**
1. **The inside is built with care.** Every state machine, checker and suppression rule has `/m` or `/s` lines (10.10, 10.11, 10.20, 10.28, 10.50).
2. **Nothing left to chance.** Unknown schema versions stop start-up (10.10). Missing stamps refuse the write (10.28). Missing prices block Canary (10.46).
3. **Names match.** `meal`, `registry_version`, Analysis, Entry and Evidence are the same words in code, data, logs and on screen (§3).
4. **Logs and personal data.** Logs carry the request id, Registry version, timing, cost and validation codes. They never carry photos, transcripts or diaries (§19.2), yet they still follow one Analysis by id.
5. **Sample data** is synthetic only (`eater-synth-*`, `*.example.test`). It includes Arabic prompt text (10.9), long model ids and 400 eaters.
6. **Collect only what is needed:**
   - Shadow keeps numbers only (10.19);
   - the metrics store holds aggregates only (10.51);
   - staff accounts are known by email and roles only;
   - the regression set holds only consented or synthetic cases (10.14).
7. **Start and resume time** of the console is measured in the release proof's performance pass.
8. **Claims.** Cost reads "estimated, not your bill" (10.47). Evaluation accuracy reads "measured on the regression set" (10.16). Provider settings read "recorded" where code cannot enforce them (10.12).

**6 · Inclusion**
1. **Largest text size.** At 200 % zoom and with enlarged text at 390 CSS px, nothing clips or overlaps (10.71).
2. **Contrast.** 4.5:1 contrast for text in light and dark, checked by axe-core (10.71).
3. **Focus rings** are visible on every control (10.71).
4. **Colour alone:** never. Every state is written (10.1).
5. **Screen-reader labels** name the task and state, and stay current after a change (10.71).
6. **Target size.** At least 44 × 44 CSS px at 390 px (10.36).
7. **Gestures and keyboard.** There are no gestures, and the whole flow works by keyboard (10.71).
8. **Reduced motion:** nothing moves except placeholders, which stop under reduced motion.
9. **Timers:** nothing disappears on a timer. Banners stay until dismissed or resolved.
10. **Arabic.** The layout mirrors. Model ids, versions and clocks stay left-to-right. Each paragraph aligns by its own language (10.69).
11. **New users, keyboard users and users from other platforms.** Someone new or from Remote Config finds the words rollout, control and roll back where they expect them (A6–A8). The keyboard-only path is proved (10.71).

---

## 6 · Shared stories (both names)

| story | shared with | what the other persona sees |
|---|---|---|
| admin-9.1 | Auditor, Support agent | deletion completion records; one account's deletion job |
| admin-9.2, 9.3, 10.55, 10.56 | Support agent | failed jobs for one account under support's permissions |
| admin-9.3, 10.4, 10.14, 10.19, 10.21, 10.28, 10.29, 10.33, 10.34, 10.40 | Eater | API and **Capture & Plan** behaviour under the Registry, kill switch, Consent and quotas |
| admin-10.1, 10.3, 10.68 | Auditor | Registry read-only; Audit trail |
| admin-10.2 | every console persona | deny by default |
| admin-10.3 | Support agent, Nutrition approver | the no-access page |
| admin-10.23, 10.49 | Nutrition approver | acceptance by Evidence type |
| admin-10.63 | Support agent, Auditor | diary access only through an Active Grant the eater approved |

## 7 · Conflicts for the model phase

1. **New words beyond map ¶4.** One name each is proposed in §3, with Arabic in the string catalogue:
   - the version states Proposed, Shadow, Canary, Rollout, Rolled back;
   - the task names Meal, Label, Scale, Recipe, Text, Voice;
   - the panel words "models list", "prompt editor", "regression set" (the brief's §16.4 term), "quotas panel", "prices panel" and "launch gates" (Settings, per D2);
   - "quota", "Canary check";
   - the evaluation-run states "Evaluating", "Cancelled" and "Finished" (10.15).

   Vocabulary D2 already fixes the sections, the version states and the kill-switch states. This lens uses D2's sections (Registry, Metrics, Jobs, Roles, Audit trail, Settings) and its version states (Proposed → Shadow → Canary → Rollout · Rolled back). The words left above need a dated delta, or a replacement.
2. **Which day a quota uses** (10.44).
   - I reset at the eater's diary-day boundary; a UTC day is simpler to bill.
   - The budget (10.48) stays on the UTC day.
   - The eater lens may decide otherwise.
3. **Who sees failed jobs with identity.**
   - FR-080 puts failed jobs in the console, and map §2 gives support "failed jobs and account state".
   - I show the admin de-identified jobs (admin-9.1–9.4, 10.55) and leave account-linked views to support.
4. **The kill switch and an open review screen** (10.33). New model calls stop at once, but an Analysis that is Ready for review can still be approved. The eater lens owns that copy.
5. **Shadow sends extra copies of eaters' inputs to Google** (10.21). Either the eater's Consent to send photos, voice and text to Google's AI covers evaluation copies, or Shadow is limited to eaters with the research Consent. This is for the eater lens and the Consent wording.
6. **Arabic voice and "no preview models in Canary or Rollout"** (10.7).
   - `gemini-3.5-transcribe` is preview on Agent Platform, `global` only, and lists only ar-EG (P15 as narrowed). My rule keeps it in Shadow.
   - Gemini 3.8 Flash audio input (P7) is the GA alternative to evaluate.
   - Voice logging is P0 for the eater.
7. **The location field and residency** (10.11).
   - Firebase suggests setting the model location in configuration (A4).
   - Residency belongs to the owner and counsel (map §1.7; P11 as corrected).
   - I keep it read-only.
8. **The budget turns the kill switch on for every task** (10.48). The eater lens may prefer a gentler order: image tasks first, Text kept on.
9. **Staff and eater accounts:** settled by vocabulary D2 ("a staff account is never an eater account"). Round 0 had this as a separate story with no source. It is now one acceptance line in admin-10.61, traced to D2.
10. **The Nutrition approver's access to Metrics** (10.49). The approver lens may want per-Food breakdowns, and these must keep the small-group rule (10.50).
11. **Who holds "View consented evaluation cases"** (10.14). No seeded role holds it. Viewing raw evidence needs a custom role (§19.2 "restricted roles"). The approver and auditor lenses may claim it.

## 8 · Assumptions in this file (labelled where used)

- The admin API paths, the console section names, the task keys and the permission list (§3, 10.58).
- The starting values in §3.
- The quota day follows the diary day (10.44).
- An Auditor cannot also hold a role that changes things (10.62).
- One platform admin must always remain (10.64).
- The persona's routine, place and feelings (§1.3, §5).
- Lifecycle dates are entered by hand: I found no lifecycle API in this run (10.5).
- Provider project settings that code cannot enforce are recorded by a person (10.12).

## 9 · Coverage of the brief and the map

| line | stories |
|---|---|
| WF-9 done-when (deletion within the policy window, completion record) | admin-9.1, 9.4 |
| WF-10 done-when ("an admin rolls a model version back and manual logging keeps working") | 10.27, 10.33 |
| Map §3 Admin → registry ("shadow → canary → rollout; frozen model ids, no latest … config live / rolled back") | 10.5–10.39 |
| Map §6 Registry (model ids per task, prompt and schema versions, rollout stage, kill switch, per-user daily AI quotas) | 10.1, 10.8–10.12, 10.18–10.44 |
| Blueprint §0 line 3 (roles screen permissions → roles → users) | 10.2, 10.58–10.64 |
| FR-001 (cloud AI needs consent and quotas, including anonymous sessions) | 10.21, 10.38, 10.41 |
| FR-039, FR-045 (only authorized commands touch the ledger; drafts contribute zero) | 10.4, 10.19, 10.34, 10.56 |
| FR-075 (export) | admin-9.2, 9.3 |
| FR-076 (separate consents incl. research use) | admin-9.3, 10.14, 10.21 |
| FR-078, NFR-13, AT-29 (retention, deletion, propagation) | admin-9.1, 9.3, 9.4, 10.14 |
| FR-079 (no model training without opt-in) | 10.12, 10.14 |
| FR-080 (role-based console, model/config rollouts, failed jobs, de-identified quality metrics) | admin-9.1–9.4, 10.1–10.64 |
| FR-081 (separation of privileges; diary only by just-in-time access) | 10.3, 10.62, 10.63, 10.64, 10.68 |
| §7.2 (AI outage keeps logging; nothing posted silently) | 10.33, 10.34 |
| §15.2 (AI through the backend) | 10.4 |
| §15.3 (log the actual processing configuration; global is not residency) | 10.11, 10.28 |
| §16.1 (frozen ids, no latest, benchmark) | 10.5–10.7 |
| §16.3 (validation; untrusted text) | 10.10, 10.11, 10.16, 10.52 |
| §16.4 (stamps incl. nutrition algorithm and source versions; regression set; shadow; canary; kill switch) | 10.13–10.37, 10.28 |
| §16.5, §23.3 (cost control, quotas, metering incl. rejects and retries, soft/hard quotas, no fixed prices) | 10.38–10.48 |
| §18.2 (typed errors; 409 with current revision; bounded retries, same command id) | admin-9.2, 10.30, 10.31, 10.40, 10.56, 10.57 |
| §19.1 (provider retention: abuse monitoring, request logging, caching, grounding, store=false) | 10.12 |
| §19.2 (logs without raw evidence; consented, role-restricted raw evidence) | 10.14, 10.19, 10.51, 10.65 |
| NFR-03, NFR-05 | 10.23, 10.31–10.37 |
| NFR-07 | 10.2, 10.51, 10.63 |
| NFR-08 | 10.71 |
| NFR-09, NFR-10, NFR-11 | 10.13, 10.16, 10.49, 10.53 |
| NFR-12 (quotas, bounded retries, least privilege, secret rotation, dependency scans, backups, tested restore) | 10.38, 10.57, 10.58–10.64, 10.65, 10.66, 10.67 |
| AT-01 (fixture reused) | 10.13 |
| AT-10 | admin-9.2, 10.43, 10.56 |
| AT-29 | admin-9.1, 9.3, 10.14 |
| AT-30 | 10.16, 10.17 |
| AT-32 | 10.33, 10.34 |

## Lens verdict (2026-10-01)

**fail**: 26 defects. Checked by a lens verifier that did not write this file, against `way/blueprint.md` §0–§1, `way/brief/frd-v1.0.md`, `way/personas/_lens-brief.md`, `care.md`, the r1 research files and both refutations.

**What holds.** The file has 66 stories, all `admin-10.n`, and each has at least one `/r` line. All 29 A-sources were re-opened on 2026-10-01 with a generic User-Agent, and every quoted phrase was found. That includes A1's "July 21, 2027 or later" for `gemini-3.5-flash-lite` on the Agent Platform page, which differs from the Gemini API date the refuter used for P2 (as A2 says), and Art. 26(3) of the SDAIA Executive Regulations behind R24's "separated duties". Every cycle-1 finding cited either stands or is cited as corrected (P5, P6, P11, P15, R18). The admin part of WF-10's done-when ("an admin rolls a model version back and manual logging keeps working") is covered by 10.24 and 10.30.

**Traced**
1. **admin-10.62** traces to nothing. Its line reads "`assumption` (no brief line; see §6)". Neither the map nor the FRD asks for separate staff and eater accounts, and FR-081 governs reading *other* people's diaries. It is drift until the model phase adopts it.
2. **admin-10.22, 10.33, 10.34, 10.49, 10.57, 10.58, 10.61, 10.65** cite only research or care, with no map or FRD line: "A7." (10.22); "A15, A29 (assumption for our load); care group 4." (10.33); "A14, A15, A16." (10.34); "Care group 4." (10.49); "A21, A23." (10.57, 10.58); "A24, A27 …" (10.61); "Care group 4; A10." (10.65). Their content belongs to §16.4, FR-080, map §2 "roles" or blueprint §0 line 3, and the trace line must say which.

**Complete (missing steps)**
3. **§16.4 "Maintain a regression set of food photos, scale readings, bilingual labels, Arabic voice commands, ingredient variants, and adversarial instructions" and NFR-10 "At least 200 consented target-cuisine test cases and 100 bilingual labels at launch".** No story adds or curates cases, checks the set's composition against these minimums, or brings consented cases in (FR-076 optional research use; §19.2 "Access to raw evidence for quality review requires explicit consent and restricted roles"). 10.12 only runs "a synthetic regression set of 300 cases".
4. **The §16.4 stamps.** The brief says "Store model ID, prompt version, extraction schema version, nutrition algorithm version, and source versions with each analysis". 10.25 refuses a write only when it is "without all four stamps" (Registry version, model id, prompt version, schema version), so the nutrition algorithm version and the source versions are missing. The §15.3 line "log the actual processing configuration" is not stamped either; that is the location 10.11 shows.
5. **Config rollback.** In map §3 the Admin → registry row has the value event "config live / rolled back", and map §6 puts "per-user daily AI quotas" in the admin-versioned Registry. But 10.35 makes "`quotas@v4` … live" at once, and no story rolls a quotas version back. Only model Registry versions roll back (10.24).
6. **FR-001 "Cloud AI requires authenticated or anonymous-session access, consent, and quotas".** 10.35 sets "anonymous sessions to hard 3 and 10", but no runtime line shows an anonymous session reaching its limit. 10.36 tests only a signed-in eater.
7. **§19.1 provider data governance** ("assess abuse-monitoring logs, request logging, caching, and grounding … configure store=false"). No story records, shows or changes these provider settings, although §1.2 lists "P14: the 24-hour cache and abuse-monitoring logs" among the findings the lens rests on.
8. **AT-29 "Account deletion and consent withdrawal propagate to media, queues, private cached analysis, and exports".** 10.51 retries a failed export or analysis job without re-checking that the account still exists, that the AI Consent still stands or that AI is on. No story covers a failed job whose eater has since deleted the account or withdrawn Consent. In that case a retry would send the data again or build an export for a deleted account.
9. **§8 coverage overstates NFR-12.** The row reads "NFR-12 | 10.35, 10.53, 10.55–10.61", but NFR-12's "secret rotation, dependency scans, backups, and tested restore" have no story and no note naming who owns them.

**Observable**
10. **admin-10.28**, line 3: "Then it is analysed normally" is vague. Name the response: the status, and a draft stamped `intent@v4`.
11. **admin-10.18**, line 1: "When they open **Capture & Plan**, Then no Analysis request is made for them". Opening the tab never requests an Analysis, so this line cannot fail. The When must attempt a capture, and the Then must name what the eater sees.
12. **admin-10.64**, line 2: "names the task in the string catalogue's Arabic label and keeps 'AI' terms consistent with the eater app". No Arabic string is given and "consistent" has no reference value, so this story about Arabic contains no Arabic console text.
13. These lines name no screen or interface:
    - 10.20, line 3: "When a 13th is added, Then it is refused". Where, and with what message?
    - 10.21, line 3: where does the reason "Too few Analyses to judge (A13)" show?
    - 10.22, both lines: "new eaters are added" and "return to it rather than being drawn again". Through which interface?
    - 10.24, line 2: the v7 and v6 stamps. Read through `GET /v1/analyses/{id}`?
    - 10.34, line 3: "When shadow or canary metrics are viewed". Which screen?
14. **admin-10.45**, line 2: "the total is suppressed or rounded too". The rounding rule is not named, so a verifier cannot decide whether a rounded total still lets the hidden figure be derived (A26).
15. **admin-10.65 contradicts admin-10.33.** 10.33 says "the row reads 'Not sent: no connection. Try again.' with a retry button"; 10.65 says "The kill switch shows 'Will try when online'". The first is a manual retry. The second implies a queued switch that fires later by itself. A verifier cannot know which to observe, and a queued emergency action could fire after the situation has changed.
16. **admin-10.33**: "each target is at least 44 × 44 pt". The console is a web page, so give the size in CSS px.

**Sourced**
17. §1.3 and §4 state the persona's routine and place with no source and no `assumption` label:
    - "Each morning: read the Registry overview, then the Quality and Jobs screens";
    - "Mostly at a desk, on a desktop browser, in long sessions";
    - "On call, from a phone (about 390 px), sometimes on a weak home or mobile network, often at night";
    - the "What they hate" list, which infers admins' feelings from incident reports and guidance (A11–A27) that say nothing about what admins hate.

    Only the Ramadan load is labelled.
18. **admin-10.20**, line 3: "no more than 12 guard metrics may be configured (A11)". This turns A11's hedge "perhaps no more than a dozen" into a hard limit, and the limit is not in the §3 starting-values table or in §7.

**Vocabulary**
19. "Paused" names three things:
    - a canary halted by a regression ("Paused · validation failures 30 % vs 1 %", 10.21; "Canary is paused on a regression", 10.23);
    - AI turned off (`reason: "paused"`, 10.28; "photo and voice analysis show a paused note", 10.30; "Paused while AI is off", 10.34);
    - 10.32's "canary v7 at 10 % paused underneath".

    The third leaves 10.32's expected "Canary · 10 %" ambiguous, because a canary paused on a regression should not read as running.
20. One stage has three names: map §3's "shadow → canary → rollout", and the lens's "Full" ("Promote to full") and "Live" ("Live v7"; "Stage names are 'Draft · Shadow · Canary · Live/Full'"). "Draft" also names an Analysis draft, as the lens's own §6.1 notes. The question is routed to §6, but the acceptance lines already use both names.
21. Task keys and screen names differ, against map ¶4 ("the screen, the code and the logs use these words"): `intent` / "Text and voice intent", `scale` / "Scale reading", `transcribe` / "Voice transcription", `meal_photo` / "Meal photo". This makes §4's "Names match the screen" untrue.
22. Words outside map ¶4 are used as fixed copy, and some have more than one name:
    - "price book" (10.40 title, §1.3), "Prices" (the screen), "meter" and "cost meter";
    - "model catalogue", "the catalogue" and **Registry › Models**;
    - "Registry version", "candidate", "guard metric", "staff account" and the console section names.

    §6 conflict 2 routes only the section names and "Registry version".

**Experience**
23. Several care questions are neither answered nor marked not applicable, although a platform build asks every group:
    - Group 2: "Would someone who knows none of our internal names understand every label?" The console shows `registry.read` (10.3), "(A13)" (10.21) and "(FR-081)" (10.59) as copy. Also unanswered: "Can people tell at a glance what is tappable".
    - Group 3: "Does the screen respond at the instant of touch?", "Can the person change their mind in the middle of a motion?" and "Does the first screen appear at once, back where the person left off?"
    - Group 5: "Do we collect only the data this feature needs?"
    - Group 6: "At the largest text size, does anything clip or overlap?" This covers browser zoom and text size at 390 px.
24. §4 says "the admin's quiet window for promotions moves to late morning". No story or requirement carries this; nothing warns about a promotion during the eaters' peak. A29 says nothing about late morning either; its quote is "From 10 p.m. to 2 a.m.".
25. §4's inclusion requirements have no acceptance line in any story, so no verifier can observe them. These are "the whole promote/rollback/kill-switch flow works by keyboard", the screen-reader label "Meal photo, AI on, toggle" and "4.5:1 contrast in light and dark". Separately, 10.28 makes "Turn off" the sheet's main button, while §4 says "One main action per view, and it is never the destructive one". The lens should state which button is the default on each of the three confirmations.

**Ids**
26. **admin-10.51** (export retry), **10.52** (deletion deadline, "4 days left (due 2026-10-05)") and **10.54** (retention purge) serve WF-9: export, and "delete account removes private data and media within the policy window". With journey = WF number they are admin-9.x stories, or the lens must say why they sit under WF-10. The lens brief asks for "one journey per workflow the persona touches", and this file has only journey 10.

## Fix round 1 (2026-10-01)

All 26 defects are fixed in the file itself. Story ids were renumbered: journey admin-9 is new and admin-10 runs 10.1–10.71. The file now also follows `way/vocabulary.md` (delta D2):

- the kill switch is On/Off, fails fast with `AI_UNAVAILABLE` and queues nothing;
- the Registry version states are Proposed → Shadow → Canary → Rollout · Rolled back;
- the error codes are `FORBIDDEN`, `NOT_FOUND` (never revealing another user's ids), `VALIDATION_ERROR`, `CONSENT_REQUIRED`, `RATE_LIMITED` and `STALE_REVISION`;
- the sections are Registry, Metrics, Jobs, Roles, Audit trail and Settings (launch gates);
- the Analysis states are Ready for review and Failed;
- the Privacy job states are Running, Completed and Failed;
- the Entry state is Confirmed;
- the roles are staff accounts, never eater accounts.

1. **10.62 (staff vs eater accounts) dropped as a story.** D2 then settled the rule ("a staff account is never an eater account"). It is now one acceptance line in admin-10.61 traced to D2, and §7 conflict 9 records this.
2. **Every story now carries a map, brief, blueprint or D2 trace.** The eight research-only stories:

   | old | new | traced to |
   |---|---|---|
   | 10.22 | 10.25 | §16.4 |
   | 10.33 | 10.36 | §16.4 and blueprint §0's ~390 px console proof |
   | 10.34 | 10.37 | §16.4 and NFR-05 |
   | 10.49 | 10.54 | FR-080 |
   | 10.57 | 10.60 | blueprint §0 line 3 and FR-080 |
   | 10.58 | 10.61 | blueprint §0 line 3, FR-080 and NFR-12 |
   | 10.61 | 10.64 | FR-081 and map §2 |
   | 10.65 | merged | its offline line moved into 10.1 (FR-080, §16.4); its kill-switch line merged into 10.36 |

   A script check finds every story's trace line citing a map, brief, blueprint or D2 line.
3. **New 10.13 and 10.14: the regression set.**
   - 10.13 counts the six §16.4 kinds and checks NFR-10's minimums of 200 consented target-cuisine cases and 100 bilingual labels. Synthetic cases are not counted toward the minimum. It adds a case using the AT-01 fixture and refuses a case with no expected result.
   - 10.14 takes consented cases only from eaters who gave the research Consent (FR-076). Raw evidence opens only for a restricted role (§19.2), and withdrawing the Consent removes the case (AT-29).
4. **10.28 now stamps the full §16.4 set:**
   - Registry version, model id, prompt version and schema version;
   - nutrition algorithm version and source versions;
   - the processing location (§15.3);
   - and a write missing any stamp is refused.
5. **New 10.39: roll back a quotas version** (map §3, "config live / rolled back"; map §6).
6. **New 10.41: an anonymous session reaches its own limit** (FR-001), shown at runtime with `RATE_LIMITED`, including the carry-over on sign-in.
7. **New 10.12: provider data settings** (§19.1). `store=false` and grounding off are enforced in code and checked by `/s`. The 24-hour cache and the abuse-monitoring exception are recorded with who and when, and a re-check falls due after 90 days.
8. **New admin-9.3: a retry re-checks the account and the Consent first** (AT-29).
   - An export for a deleted account → `VALIDATION_ERROR`, and the job stays Failed.
   - Consent withdrawn → `CONSENT_REQUIRED`, with no provider call.
   - Kill switch On → `AI_UNAVAILABLE`.
   - 10.56 points to it.
9. **New 10.65–10.67 under the Settings launch gates, for NFR-12:**
   - credential rotation with the value never shown or logged;
   - the dependency audit and SBOM, with CI failing on a known vulnerability;
   - a backup restore that replays every Day.

   Owner note: hosted rotation and scheduled backups wait with the dropped operate row. The §9 coverage row now names all seven NFR-12 items.
10. **10.31 (was 10.28), line 3:** now "returns 200 with an Analysis that is Ready for review, stamped `text@v4`".
11. **10.21 (was 10.18), line 1:** the eater now *takes a photo*. The app shows the Consent explanation and sends no request. A direct call returns 403 `CONSENT_REQUIRED`, and the Shadow count does not move.
12. **10.69 (was 10.64):** proposed Arabic strings in a table, used verbatim in the acceptance lines. They are «السجل», «الوجبة», «مفتاح الإيقاف: غير مُفعَّل», «مفتاح الإيقاف: مُفعَّل», «إلغاء» and «فعِّل مفتاح الإيقاف: الوجبة». The vague "consistent with the eater app" claim is gone.
13. **Every flagged line now names its screen or interface:**
    - the "13th metric" line is removed (see 18);
    - the regression reasons show on Registry › Meal, the Audit trail and a banner (10.24);
    - the Canary share lines are read through `GET /v1/analyses/{id}` (10.25);
    - the roll-back stamps go through `GET /v1/analyses/{id}` (10.27);
    - "No traffic while the kill switch is On" shows on the Registry › Meal Canary panel (10.37).
14. **10.50 names complementary suppression** with a worked fixture: rows of 7, 40, 120 and 300 with a total of 467. Rows 7 and 40 are hidden and the total is shown exact.
15. **The 10.33/10.65 contradiction is resolved in 10.36.** A switch sent offline shows "Not sent … Try again". It is never queued, and nothing is sent until the admin presses Try again. 10.34 adds that while the kill switch is On, an eater's request fails at once and is never sent later without the eater (D2).
16. **10.36 sizes are in CSS px:** 44 × 44 CSS px at 390 CSS px.
17. **§1.3 is split** into "What the sources show going wrong" (sourced) and the routine, place and feelings, each labelled `assumption`. The unsourced "what they hate" claims are removed. §5 labels its place and feeling lines the same way.
18. **"A dozen" is a soft guideline.** The hard limit is removed from 10.23. The §3 table says "about six; a dozen is A11's soft guideline, not a limit".
19. **"Paused" no longer names anything.**
    - The kill-switch states are On and Off (D2).
    - A failing Canary check rolls the version back automatically (10.24, state Rolled back).
    - 10.35 now reads "Canary: version 7 · 10 % of eaters" once the kill switch is Off.
20. **One name for each stage:** Proposed → Shadow → Canary → Rollout · Rolled back (map §3 and D2). "Draft", "Live" and "Full" are gone. The overview reads "Rollout: version 6", and the replaced version is the "previous Rollout version".
21. **Task keys now equal the screen names:** `meal`/Meal, `label`/Label, `scale`/Scale, `recipe`/Recipe, `text`/Text, `voice`/Voice. They follow the brief's §14 capture modes and `POST /v1/analyses`.
22. **One name per thing:**
    - "price book", "Prices" and "meter" all became "prices on Metrics" and "estimated cost";
    - "model catalogue" became "the models list on Registry";
    - "candidate" became "proposed version";
    - "guard metric" became "Canary check";
    - "console user" became "staff account" (D2);
    - the sections are D2's.

    The words left over are listed in §7 conflict 1 for a dated delta.
23. **§5 answers every care question in groups 1–6, numbered as in care.md**, or marks it n/a with a reason: group 1.8 and group 3.5 (no animation), and group 4.9 (no device permissions). The copy rule in §3 removes internal ids (`registry.read`, finding ids, requirement ids) from screen copy, and a script check finds none in quoted copy.
24. **The unsourced "late morning" promotion window is dropped.** A29 is now used only for the eater's late diary-day boundary (10.44), and the text says that no rollout window is derived from it.
25. **New 10.71 covers keyboard, screen reader, contrast and zoom**, with runtime lines:
    - keyboard-only Propose → Canary → Rollout → roll back → kill switch, with Esc cancelling;
    - VoiceOver announcing "Meal, kill switch Off/On, switch";
    - axe-core reporting no contrast failure in light and dark at 1280 and 390 CSS px;
    - 200 % zoom with no clipping.

    Every confirmation now defaults to "Cancel": the kill switch (10.31, 10.32), Move to Canary (10.22), Move to Rollout (10.26), roll back (10.27) and quotas roll back (10.39). The action button is separate and named with its verb.
26. **WF-9 stories are renumbered as journey admin-9:**

    | old | new |
    |---|---|
    | 10.52, deletion deadline | admin-9.1 |
    | 10.51, export retry | admin-9.2 |
    | new, AT-29 re-checks | admin-9.3 |
    | 10.54, retention purge | admin-9.4 |

    The analysis-job retry stays in WF-10 as 10.56, because FR-080 places failed AI jobs in the console's AI governance.

Counts after round 1: 75 stories (admin-9: 4; admin-10: 71) and 197 acceptance lines. Every story has at least one `/r` line.
