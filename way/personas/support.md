# Support agent — persona lens (research cycle 2)

Written 2026-10-01 by the support-agent lens, from `way/personas/_lens-brief.md`. Dispatch: **WF-10** (just-in-time diary access: support requests a Grant with reason and duration → the eater approves or declines it in Settings → read-only access inside the time box → auto-expiry, every read audited) and the **support side of WF-9** (help with export or deletion status without seeing private data; failed jobs — AI analysis failures, sync conflicts, deletion and export jobs — as de-identified metadata; account state lookup).

Read first: `way/blueprint.md` §0–§1, `way/brief/frd-v1.0.md` (FR, NFR, AT ids cited as written there), `way/research/r1-*.md` with both refutations (no refuted or doubtful finding is cited here; R34, R29's decree type and P11's residency claim are avoided), the care questions, `way/lessons.md`.

**How to read the stories.** Ids are `support-<WF>.<n>`. Each acceptance line carries its layer: `/m` module (a unit test of pure code), `/s` system (API or rules test against the emulator or a running service, including negative tests), `/r` runtime — a verifier can **observe** it in the served product: the admin console in a browser, the API over HTTP, or the iOS simulator. Every story has at least one `/r` line. "Shared:" names the other persona a story belongs to as well.

**Proposed interfaces.** The FRD lists no support or Grant endpoints (§18). Paths under `/v1/support/…`, `/v1/grants…`, `/v1/me/…` and `/v1/audit/…`, and the typed errors `ACCOUNT_NOT_FOUND`, `CASE_REF_REQUIRED`, `LOOKUP_REQUIRED`, `FORBIDDEN_ROLE`, `GRANT_REQUIRED`, `GRANT_INVALID`, `GRANT_REQUEST_OPEN`, `GRANT_DECLINED`, `GRANT_EXPIRED`, `GRANT_REVOKED`, `GRANT_OUT_OF_SCOPE`, `GRANT_READ_ONLY`, `ACCOUNT_DELETION_PENDING`, are this lens's proposals for the model phase, written in the style of FRD §18.2. The FRD's own errors (`AI_UNAVAILABLE`, `RATE_LIMITED`, `STALE_REVISION`, …) are used as the FRD names them.

**Synthetic fixtures used below** (all invented; the repo is public):

| fixture | values |
|---|---|
| Eater E1 | account `acct_9c41e2`; Sign in with Apple, masked email `r•••@privaterelay.appleid.com`; app language Arabic, Arabic-Indic numerals; time zone Asia/Riyadh (UTC+3); diary-day boundary 04:00; two iPhones (app 1.0.3 on iOS 26.1; app 1.0.2); support code `SB-7KQ2-94XM` |
| Eater E2 | account `acct_51ab07`; email sign-in `nadia.synthetic@example.com`; English; Africa/Cairo; deletion requested 2026-09-15 10:00 local, reference `DEL-26-0915-K3Q8` |
| Eater E3 | account `acct_e07d13`; export job `job_exp_4410` failed |
| Eater E4 | account `acct_77d2c0`; tracking-only mode after a pregnancy answer on the safety screen |
| Staff | `staff_mona` "Mona K." Support, console in English, Europe/Dublin (UTC+1); `staff_omar` "Omar S." Support, console in Arabic; `staff_dina` Nutrition approver; `staff_ali` Platform admin; `staff_hana` Auditor |
| Grant G1 | `grant_31f0`: requested by `staff_mona` for `acct_9c41e2` on 2026-10-01 10:05 UTC; reason `sync_missing_entry`; diary days 2026-09-28 to 2026-09-30; areas "Entries and day reports" + "My Units"; duration 1 hour; case `CASE-1182`; approved 10:20 UTC; expires 11:20 UTC (14:20 Riyadh, 12:20 Dublin) |

---

## 1 · Research cycle 2 — the support agent's day in the benchmarks

Every source below was opened in this run on **2026-10-01** with a generic User-Agent; the date after the link is the source's own date where it states one. Quotes are short. Ids are `SR` (support research) so they do not clash with the FRD's [S01–S16]. Findings from cycle 1 are cited by their r1 id (all cited ones stand in `r1-refute-b.md`).

**SR1 · Microsoft Customer Lockbox: the customer approves a named engineer's request; unanswered requests expire; access is time-boxed and every action is logged.** `opened`
- https://learn.microsoft.com/en-us/purview/customer-lockbox-requests · page dated 2025-02-03 · "Usually, engineers fix issues using extensive telemetry and debugging tools … However, some cases require a Microsoft engineer to access your content" · the request "includes the organization's tenant name, service request number, expected start time of access (starts immediately post-approval if not specified), the estimated amount of time the engineer needs access" · "If the customer rejects the request or doesn't approve the request within 12 hours, the request expires and no access is granted" · "Microsoft engineers have the requested duration to fix the issue after which the access is automatically revoked." · "All actions performed by a Microsoft engineer are logged in the audit log."

**SR2 · Google Cloud Access Approval: explicit approval before staff access, revocable at any time, with a history of every outcome — and waiting costs support time.** `opened`
- https://cloud.google.com/assured-workloads/access-approval/docs/overview · last updated 2026-09-24 · "Access Approval ensures that Cloud Customer Care and engineering teams require your explicit approval whenever they need to access your Customer Data." · "Active access approval requests may be revoked at any time." · "a historical view of all requests that were approved, dismissed, revoked, or expired." · "The support response time increases by the duration that Customer Care spends waiting for your approval."
- https://cloud.google.com/assured-workloads/access-approval/docs/approve-requests · last updated 2026-09-24 · approvers get requests by "email" or "Pub/Sub" and "To approve a request, click Approve".

**SR3 · Google Access Transparency: each staff access is logged with resource, action, time, reason and who the accessor is.** `opened`
- https://cloud.google.com/assured-workloads/access-transparency/docs/overview · last updated 2026-09-24 · "Access Transparency log entries include details such as the affected resource and action, the time of the action, the reason for the action, and information about the accessor."

**SR4 · Microsoft Entra Privileged Identity Management (PIM): just-in-time, time-bound, approval-based access with a justification; activation lasts 1 to 24 hours; a ticket number is information-only.** `opened`
- https://learn.microsoft.com/en-us/entra/id-governance/privileged-identity-management/pim-configure · 2026-04-23 · "Provide just-in-time privileged access" · "Assign time-bound access" · "Require approval to activate privileged roles" · "Use justification to understand why users activate" · "Get notifications when privileged roles are activated".
- https://learn.microsoft.com/en-us/entra/id-governance/privileged-identity-management/pim-how-to-change-default-settings · 2026-04-23 · activation maximum duration: "This value can be from one to 24 hours." · ticket information "is an information-only field. Correlation with information in any ticketing system isn't enforced."

**SR5 · HIPAA's minimum-necessary standard (benchmark only — Sips & Bytes is a general-wellness app and no claim is made that HIPAA applies): limit staff access to the categories of data each class of workforce needs.** `opened` (eCFR, the official US code)
- https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-E/section-164.502 · current · §164.502(b): "make reasonable efforts to limit protected health information to the minimum necessary to accomplish the intended purpose of the use, disclosure, or request."
- https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-E/section-164.514 · current · §164.514(d)(2)(i): identify "Those persons or classes of persons … who need access" and "For each such person or class of persons, the category or categories of protected health information to which access is needed and any conditions appropriate to such access."

**SR6 · HIPAA Security Rule technical safeguards (benchmark): unique user identification, emergency access procedure, automatic logoff, audit controls, person authentication.** `opened`
- https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-C/section-164.312 · current · "(i) Unique user identification (Required). Assign a unique name and/or number for identifying and tracking user identity." · "(iii) Automatic logoff (Addressable). … terminate an electronic session after a predetermined time of inactivity." · "(b) Standard: Audit controls. Implement … mechanisms that record and examine activity".

**SR7 · Break-glass is for emergencies, not a helpdesk; when used it is view-only where possible, specially audited, and reviewed by someone other than its creator.** `opened` — Yale University HIPAA program (undated page)
- https://hipaa.yale.edu/security/break-glass-procedure-granting-emergency-access-critical-ephi-systems · "The break–glass intended to specifically cover emergency cases and should not be used as a replacement for a helpdesk." · "Limit emergency access to the minimum data and functionality needed … This could potentially include view–only capability" · "Ensure that the individuals who create the accounts are not the ones reviewing the audit trails since this can be a source of abuse." · "Each use of an emergency account should be reviewed."
- **Consequence for this lens:** support has **no break-glass path** to a diary. A general-wellness diary has no clinical emergency that support could serve; the only path is the eater's Grant (blueprint §2).

**SR8 · NIST SP 800-53 Rev 5.2.0: least privilege, logging privileged functions, separation of duties, automatic disabling of temporary access, session termination, and the content of an audit record.** `opened` — NIST's own OSCAL catalogue (version 5.2.0, modified 2026-05-11)
- https://raw.githubusercontent.com/usnistgov/oscal-content/main/nist.gov/SP800-53/rev5/json/NIST_SP-800-53_rev5_catalog.json · AC-6: "allowing only authorized accesses for users … that are necessary to accomplish assigned organizational tasks." · AC-6(9): "Log the execution of privileged functions." · AC-5: "Define system access authorizations to support separation of duties." · AC-2(2): "Automatically [disable/remove] temporary and emergency accounts after [time period]." · AC-12: "Automatically terminate a user session after [conditions]." · AU-3: audit records establish "What type of event occurred; When the event occurred; Where the event occurred; Source of the event; Outcome of the event; and Identity of any individuals, subjects, or objects/entities associated with the event."

**SR9 · GDPR Arts. 12 and 25: answer rights requests within one month; doubt about identity allows asking for more only where needed; by default, personal data is not accessible to people without the individual's intervention.** `opened` (gdpr-info.eu)
- https://gdpr-info.eu/art-12-gdpr/ · Art. 12(3): "without undue delay and in any event within one month of receipt of the request." · Art. 12(6): "where the controller has reasonable doubts concerning the identity … may request the provision of additional information necessary to confirm the identity".
- https://gdpr-info.eu/art-25-gdpr/ · Art. 25(2): "by default, only personal data which are necessary for each specific purpose of the processing are processed. That obligation applies to … their accessibility." · "by default personal data are not made accessible without the individual's intervention to an indefinite number of natural persons."

**SR10 · EDPB Guidelines 01/2022 on the right of access (v2.1, adopted 28 Mar 2023): use the account's own sign-in to authenticate; asking an already-authenticated person for an ID copy is disproportionate.** `opened`
- https://www.edpb.europa.eu/system/files/2023-04/edpb_guidelines_202201_data_subject_rights_access_v2_en.pdf · ¶72: "the authentication mechanism may include the same credentials, used by the data subject to log-in to the online service" · ¶73: "it is disproportionate to require a copy of an identity document in the event where the data subject making a request is already authenticated by the controller."

**SR11 · Saudi PDPL Implementing Regulation: act on rights requests within 30 days, verify identity, record every request (even oral ones); separate employees' levels of access to health data and document who handles each stage.** `opened` (official SDAIA PDF)
- https://sdaia.gov.sa/en/SDAIA/about/Documents/ExecutiveRegulations.pdf · Art. 3(1)(a): "within a period not exceeding (30) days" · (c): "verify the identity of the requester before executing the request" · (d): "document and keep record of all received requests including oral requests." · Art. 26(3): "taking into account different level of access to data among employees or workers in a manner that guarantees the highest degree of Data Subjects privacy." · Art. 26(4): "Document all stages of Health Data Processing and provide the means to identify the person in charge for each stage." (Cycle 1: R22, R23, R24.)

**SR12 · FTC v. Ring (complaint filed 31 May 2023): unrestricted staff access was abused, and an email address was the look-up key; support access was later limited to the customer's consent.** `opened`
- https://www.ftc.gov/system/files/ftc_gov/pdf/complaint_ring.pdf · 2023-05-31 · Ring "did not limit access to customers' video data to employees who needed the access to perform their job function (e.g., customer support …)" · "Using her email address as a look-up mechanism, the employee identified his female co-worker's device and watched her stored video recordings without her permission." · "Ring narrowed employee access … so that customer service agents could only access videos with the customers' consent."
- https://techcrunch.com/2023/05/31/amazon-ring-ftc-settlement-lax-security/ · 2023-05-31 · "Ring … will pay $5.8 million over claims … that Ring employees and contractors had broad and unrestricted access to customers' videos for years."

**SR13 · Consumer "support access" toggles today: customer-granted and time-limited, but often team-wide, on by default, and not limited to a case.** `opened`
- https://support.greenhouse.io/hc/en-us/articles/360053616131-Grant-temporary-account-access-to-Greenhouse-Technical-Support (undated) · "At the end of the selected time, access is revoked automatically" · "This access is not limited to a specific Greenhouse employee." · "To edit the length of access after granting it … revoke access entirely, then grant new access" · "Your organization's Change Log creates a record any time a Greenhouse employee logs into a user's account."
- https://help.servicetitan.com/docs/give-servicetitan-support-temporary-access (undated) · "The allow access option is selected by default." · "all ServiceTitan Support Agents have access to your account for three days." · "Access can not be allowed or revoked for specific support cases."
- **Consequence:** the Grant is per agent, per case, scoped by days and areas, off by default — the opposite of each line above.

**SR14 · A nutrition benchmark: Cronometer lets the user accept or reject a professional's request to view their diary, and stop it later; friends never see the diary.** `opened` (Cronometer help centre API)
- https://support.cronometer.com/hc/en-us/articles/360058911291-Mobile-Sharing · updated 2026-07-12 · "Choose to accept or reject requests to view your profile using this section. Click stop next to your professional to remove their access to your account."
- https://support.cronometer.com/hc/en-us/articles/360018867471-Sharing · updated 2026-09-19 · "Sharing with a friend only allows you to share custom foods and recipes. This will not allow your friend to view your diary".

**SR15 · Health-record transparency: Estonia's patients can see who viewed their record.** `opened`
- https://e-estonia.com/enter-e-estonia-digital-health/ · 2020-03-13 · "the system's transparency means that the user can see who has viewed his or her medical data."

**SR16 · Support agents' daily pain is swivelling between tools to assemble customer context.** `opened` — Zendesk (vendor blog, last updated 2022-03-24; a vendor's own survey)
- https://www.zendesk.com/blog/customer-service/support/customer-service-agents-need-context/ · "More than half of agents say they usually have to switch between multiple systems to solve a customer request." · "Constantly switching between different systems is not only tiresome for agents, it also leads to longer hold times and slower resolution times".

**Cycle-1 findings this lens rests on:** R2 and R3 (explicit, separate, withdrawable consent; no feature behind consent), R4 (deletion in the app, never through email, phone or "other support flows"; Sign in with Apple tokens revoked), R7 (an app cannot tell when HealthKit read access is denied), R8 (camera and photo data never mined), R21 (inferred health is sensitive), R22 (consent documented), R23 (destroy on request incl. backups, notify recipients, 30 days), R24 (document every stage of health-data processing), R25 (DPO seat), R31 (GDPR one month; erasure without undue delay).

**Assumptions** (no source settles them; the model phase tunes them in the served product):

| id | assumption | why this value |
|---|---|---|
| A1 | Support works at a desk on a large screen with a helpdesk tool open beside the console | the dispatch; SR16 shows agents juggle several systems |
| A2 | Support code: 8 characters (no 0/O/1/I), issued in the signed-in app, valid 24 h | authenticates through the app's own sign-in (SR10) and gives no lasting look-up key (SR12) |
| A3 | Email look-up: exact match only, case reference required, ≤30 per agent per hour | SR12's email-as-look-up abuse; SR4's information-only ticket field |
| A4 | Grant durations 1 h (default), 4 h, 24 h | SR4 caps activation at 1–24 h; 1 h is the smallest useful box |
| A5 | At most 14 diary days per Grant | minimum necessary (SR5); two weeks covers a sync or report question |
| A6 | An unanswered request expires after 72 h | SR1 uses 12 h for an organisation's admin; an eater may not open the app for a day or a weekend |
| A7 | Console idle logoff after 15 min | SR6 (automatic logoff), SR8 AC-12 |
| A8 | A ready export stays downloadable in the app for 7 days | FRD gives no window |
| A9 | Support may re-queue a failed **export** once; deletion retries belong to the system and the platform admin | §18.2 bounded retries with the same command id; see conflict K4 |
| A10 | The Grant panel carries a faint watermark (staff id + Grant id) | deterrent for screenshots; no source |
| A11 | Live state changes reach an open console within 5 s (Grant) and 60 s (platform AI status) | no source |
| A12 | The daily AI quota resets at the eater's local midnight | FRD §16.5 gives no reset rule |
| A13 | Every support read about an account requires a look-up of that account in the same console session | ties each read to a case (SR4, SR11 Art. 3(1)(d)) |
| A14 | After a decline, later requests are allowed but the form shows the decline for 24 h | the eater sees every request anyway |
| A15 | "Still loading — Cancel" appears after 10 s | no source |
| A16 | The reason catalogue and its Arabic strings below are proposals for the string catalogue | the map fixes Arabic labels once (§1.4) |
| A17 | Eaters who log suhoor or late meals ask "why is my meal on the wrong day?" — answered from the diary-day boundary | FRD §8.1 supports a custom boundary for late-night eating; frequency unmeasured |

---

## 2 · The support agent's day

**Who.** An employee or contractor in the Support role (FR-081), answering eaters who write by email or chat. They hold no nutrition-approver or platform-admin rights (FR-081; SR8 AC-5; SR11 Art. 26(3)). Some work in Arabic, some in English (§0 line 5).

**Where and on what.** At a desk, on a large monitor, keyboard first, with the helpdesk in another window (A1, SR16). Office network, steady light. Sometimes a laptop; the console must still work at about 390 px (§0, "admin console proved in a browser at desktop and ~390 px"). The eaters they serve are in Asia/Riyadh and Africa/Cairo (UTC+3 in October); the agent may be elsewhere, so every time shows in both zones.

**Tasks they repeat** (from the benchmarks and the map):
1. **Find the right account** from what the eater gives them — today usually an email address, which is exactly the look-up key misused in SR12. Here: a support code the eater reads from the signed-in app (SR10, A2), or an exact email with a case reference (A3).
2. **Explain a failure from metadata.** SR1's engineers "usually" fix issues "using extensive telemetry"; content access is the exception. Here: Account state, Privacy jobs and Failed jobs, built from the operational fields FRD §19.2 allows (request ids, timing, status, model version, validation codes) and nothing from the diary.
3. **Answer privacy requests on time** — where is my export, when is my deletion done, please delete me. The law sets 30 days (SR11, R23) or one month (SR9, R31); every request is recorded, even oral ones (SR11). Deletion itself never goes through support (R4).
4. **Rarely, look inside a diary** — a missing entry, a total that looks wrong. Only through a Grant the eater approves (blueprint §2), like SR1's Lockbox and SR14's professional sharing, never a break-glass path (SR7).

**The moments that decide trust.**
- *The ask.* The eater sees who, why, which days and areas, and for how long, and can say no without explaining (SR1, SR2, SR14).
- *The end.* Access stops by itself, and the eater can stop it sooner (SR1, SR2, SR13).
- *The after.* The eater can see each thing the agent read (SR3, SR13's change log, SR15). The auditor sees the same trail; the agent cannot touch it (SR7, SR8).
- *For the agent:* a tool that cannot over-share protects them too. In SR12, overbroad access made every employee a suspect and let one abuse it for months.

**What they use today and hate.** Swivelling between systems for context (SR16). Support-access toggles that are team-wide, on by default and not tied to a case (SR13), so nobody can tell who looked. Asking customers for ID copies to prove who they are, which the EDPB calls disproportionate for a signed-in user (SR10). Waiting on approvals (SR2), which the design softens by keeping metadata help usable while a request waits.

---

## 3 · Goals

- **G1 Help without seeing.** Resolve most cases from account state, privacy jobs and failed-job metadata, with no diary, target, weight, mode or food content (FR-080, FR-081, §19.2).
- **G2 Just in time, and only what's needed.** When a diary is truly needed, ask the eater for the fewest days and areas for the shortest time, read without changing anything, and hand access back (FR-081, WF-10).
- **G3 Privacy requests on time.** Tell an eater exactly where an export or deletion stands and by when, point them to the in-app path, and record every request that arrives outside the app (FR-075, FR-078, NFR-13; SR11).
- **G4 A trail that protects both sides.** Every look-up, view and Grant step is recorded with who, what, when, where and outcome; the eater sees Grant reads; the auditor sees everything (FR-081, FR-082; SR8 AU-3).

---

## 4 · Journey 9 — help with privacy jobs and failures, without seeing private data (WF-9, support side)

Steps: **9A** sign in and find the account → **9B** read account state → **9C** export and deletion status → **9D** failed jobs (AI analysis, sync, Activity import) → **9E** boundaries: role, isolation, audit, desk and phone width, Arabic.

### 9A · Sign in and find the account

#### support-9.1 · Sign in as myself
As the support agent, I sign in to the admin console with my own staff identity, so that everything I do is tied to me and nobody can act as me.
Covers: FR-080, FR-081, NFR-12 · SR6, SR8 (AC-12) · A7
- `/r` **Given** staff account `staff_mona` with the Support role, **When** she signs in to the admin console, **Then** the header reads "Mona K. · Support" and the navigation lists only Account lookup, Privacy requests log and My Grants — no Reference, Policy, Registry, Roles or Audit trail.
- `/r` **Given** `staff_mona` is signed in and idle for 15 minutes, **When** she next clicks, **Then** the console shows sign-in with no account data left on the page, and the Audit trail has `staff.session_ended` with reason `idle`.
- `/s` **Given** any `/v1/support/*` route, **When** it is called with no token or with an eater's Firebase token, **Then** it returns 401 or 403 `FORBIDDEN_ROLE` and no data.

#### support-9.2 · Find an account by the eater's support code
Shared: support agent + eater (the eater's Settings → Help shows the code).
As the support agent, I find an account by the support code the eater reads me from Settings → Help, so that I reach the right account without searching for people.
Covers: FR-080, FR-081, FR-001 · SR10, SR12 · A2
- `/r` **Given** eater `acct_9c41e2` whose Settings → Help shows support code `SB-7KQ2-94XM` (issued 2026-10-01 09:12 Asia/Riyadh), **When** `staff_mona` enters it in Account lookup, **Then** Account state for `acct_9c41e2` opens.
- `/r` **Given** the same code, **When** it is entered after 2026-10-02 09:12 Asia/Riyadh, **Then** Account lookup says "This support code has expired. Ask the eater for a new one from Settings → Help." and no account opens.
- `/r` **Given** `SB-7KQ2-94X` (one character short) or any partial value, **When** it is looked up, **Then** Account lookup says "No account matches this code" with no suggestions or list, and `GET /v1/support/accounts?support_code=SB-7KQ2-94X` returns 404 `ACCOUNT_NOT_FOUND`.
- `/m` **Given** the support-code generator, **When** it issues 10,000 codes, **Then** each has 8 characters from an alphabet without 0, O, 1 or I, and no two valid codes are equal.
- `/r` **Given** E1's app is in Arabic with Arabic-Indic numerals, **When** Settings → Help (الإعدادات ← المساعدة, proposal) shows the code on the iOS simulator, **Then** it appears in Latin characters left to right as `SB-7KQ2-94XM` inside the right-to-left screen, with a Copy button.
- `/r` **Given** an anonymous-session eater (FR-001), **When** they open Settings → Help, **Then** a support code is shown and Account state reads sign-in "Anonymous session"; **given** a local-trial eater with no session, Settings → Help says that their diary is only on this phone and support cannot see it, and offers account creation.

#### support-9.3 · Find an account by exact email, with a case reference
As the support agent, I find an account by the exact email an eater wrote from, only with a case reference, so that I can help someone who cannot open the app without being able to browse people.
Covers: FR-081, NFR-12 · SR4, SR11 Art. 3(1)(d), SR12 · A3
- `/r` **Given** `acct_51ab07` with email `nadia.synthetic@example.com`, **When** `staff_mona` enters that exact email and case reference `CASE-1190` in Account lookup, **Then** Account state opens, and the Audit trail shows `support.lookup` with method `email`, case `CASE-1190`, outcome `found`, staff `staff_mona`.
- `/r` **Given** the same email, **When** Case reference is empty, **Then** Look up stays disabled with "A case reference is required to look up by email", and the API returns 422 `CASE_REF_REQUIRED`.
- `/r` **Given** `nadia.synthetic@` or `*@example.com`, **When** looked up, **Then** "No account matches this email" (exact, case-insensitive match only), 404 `ACCOUNT_NOT_FOUND`, and an audit event with outcome `not_found`.
- `/s` **Given** `staff_mona` made 30 email look-ups in the past hour, **When** she makes the 31st, **Then** 429 `RATE_LIMITED`, and the Audit trail marks the burst for the auditor.

### 9B · Read the account state

#### support-9.4 · Read the account state — only what helps
As the support agent, I read an account's sign-in method, app language and numerals, time zone and diary-day boundary, devices with app version and last sync, Consents, privacy jobs, AI use today and Grants, so that I can explain most issues without the diary.
Covers: FR-080, FR-081, FRD §8.1, §19.2 · SR5, SR11 Art. 26(3) · A17
- `/r` **Given** `acct_9c41e2`, **When** Account state opens, **Then** it shows: account `acct_9c41e2`, created 2026-08-03, sign-in "Sign in with Apple", email `r•••@privaterelay.appleid.com`, language Arabic, numerals Arabic-Indic, time zone Asia/Riyadh, diary-day boundary 04:00, devices "iPhone · app 1.0.3 · iOS 26.1 · last sync 2026-10-01 13:02 (10:02 UTC)" and "iPhone · app 1.0.2 · last sync 2026-09-29 23:40 (20:40 UTC)", AI analyses today 3 of 10, privacy jobs none, Grants none open.
- `/r` **Given** the diary-day boundary 04:00, **When** the agent hovers or focuses it, **Then** the hint reads "Food eaten 00:00–03:59 counts on the previous diary day", which answers a suhoor "wrong day" question without the diary.
- `/r` **Given** Account state is open, **When** the agent presses `/`, **Then** focus moves to Account lookup.

#### support-9.5 · Never see what the eater did not share
As the support agent, I never see Targets, weight or body data, safety-screen answers, tracking-only mode, activity mode or any food content in Account state, Privacy jobs or Failed jobs, so that support cannot become a way to learn someone's health.
Covers: FR-079, FR-081, §19.2, NFR-07 · SR5, SR8 (AC-6), SR12; R21
- `/r` **Given** E4 `acct_77d2c0` (tracking-only after a pregnancy answer, with weight observations), **When** `staff_mona` opens its Account state, Privacy jobs and Failed jobs, **Then** no Target, kcal, macro, weight, height, age, goal, mode, activity mode, safety-screen item, Food, Unit, Recipe, Template or Entry content appears, and the screens look the same as for an account in ordinary mode.
- `/s` **Given** every `/v1/support/*` response for `acct_77d2c0`, **When** its JSON is scanned, **Then** none contains the keys `target`, `kcal`, `weight_kg`, `mode`, `safety`, `items`, `food`, `unit_label`, `transcript` or `photo_ref`; responses are built from an allow-list.
- `/m` **Given** the support projection's allow-list, **When** a new field is added to UserProfile, **Then** the projection test fails until the field is classed "support-visible" or "private"; unclassed means private.

#### support-9.6 · Explain a feature that is off because of a Consent
As the support agent, I see each Consent's state, version and date, so that I can explain why photo analysis or Health import is not working without seeing any photo or Health data.
Covers: FR-076, FR-067 · R2, R3, R7, R22
- `/r` **Given** `acct_9c41e2` withdrew "Send photos, voice and text to Google's AI" on 2026-09-30 21:14 Asia/Riyadh (Consent v3), **When** Account state is open, **Then** Consents shows "Off · withdrawn 2026-09-30 21:14 · v3" with the note "Analysis needs this Consent; the eater can turn it on in Settings → Privacy".
- `/r` **Given** the in-app Consent "Health: read workouts" is on but iOS read access may be denied, **When** the agent reads Account state, **Then** the line reads "On in the app · iOS permission is not visible to us", never "denied" (R7; FR-067).
- `/s` **Given** a support token, **When** it calls any Consent-changing endpoint, **Then** 403 `FORBIDDEN_ROLE`; Consents change only in the eater's app.

### 9C · Export and deletion status

#### support-9.7 · Tell an eater where their export is
As the support agent, I see the export job's state, timing and section list — never the file — so that I can tell the eater exactly where their export is.
Covers: FR-075, FR-078, FRD §18 `POST /v1/privacy/export-or-delete`, WF-9 done-when · A8
- `/r` **Given** `acct_51ab07` requested an export on 2026-09-30 18:02 Africa/Cairo, **When** the agent opens Privacy jobs, **Then** the row shows "Export · Ready · requested 2026-09-30 18:02 · ready 18:09 · Entries, Units, Recipes, Targets, Consents, Reports · 2.4 MB · in the app until 2026-10-07 18:09" and a copy-ready line in the eater's language: "Open Settings → Privacy → Export to download it."
- `/s` **Given** `GET /v1/support/accounts/acct_51ab07/privacy-jobs`, **When** it is read, **Then** the export item has no URL, signed link, file id or per-Day counts, and `GET /v1/support/privacy-jobs/job_exp_77/file` returns 403 `FORBIDDEN_ROLE`.
- `/r` **Given** an account that never asked for an export or deletion, **When** Privacy jobs opens, **Then** it says "No export or deletion requested" and shows the in-app path to start one.

#### support-9.8 · Explain and re-queue a failed export
As the support agent, I see why an export failed and re-queue it once, so that the eater gets their export without starting over or sending me anything.
Covers: FR-075, FR-078, §18.2, NFR-12 · A9 · conflict K4
- `/r` **Given** export `job_exp_4410` for `acct_e07d13` is Failed with `EXPORT_WRITE_TIMEOUT`, attempts 3 of 3, last attempt 2026-10-01 08:40 UTC, **When** the agent opens Privacy jobs, **Then** the row shows those values and a Re-queue button.
- `/r` **Given** that row, **When** the agent presses Re-queue, **Then** the state becomes Queued with attempts 0 of 3 under the same id `job_exp_4410`, the button disappears, and the Audit trail has `support.job_requeued`.
- `/s` **Given** `POST /v1/support/privacy-jobs/job_exp_4410/requeue` arrives twice, **When** the second is processed, **Then** it returns 409 with the current state and no second job exists.
- `/r` **Given** a deletion job row, **When** the agent opens it, **Then** it has no Re-queue control (see support-9.10).

#### support-9.9 · Tell an eater when their deletion completes
As the support agent, I see a deletion's stages and its due-by date, so that I can tell the eater exactly what has happened and when the rest will.
Covers: FR-078, NFR-13, AT-29, §17.2 · R4, R23, R31 · SR9, SR11
- `/r` **Given** `acct_51ab07` requested deletion on 2026-09-15 10:00 Africa/Cairo (reference `DEL-26-0915-K3Q8`), **When** Privacy jobs opens on 2026-10-01, **Then** it shows "Deletion · In progress · due by 2026-10-15" with stages and dates: signed out and disabled — done 09-15; private records deleted — done 09-15; media and cached analyses deleted — done 09-16; queued commands and exports deleted — done 09-16; Google notified — done 09-16; Sign in with Apple token revoked — not applicable (email sign-in); backups expire by 2026-10-14 — waiting; completion record — waiting.
- `/m` **Given** a deletion requested at 2026-09-15T07:00Z, **When** the due-by date is computed, **Then** it is 2026-10-15 — 30 days, the stricter of SDAIA's 30 days and GDPR's one month (NFR-13) — stored in UTC and shown in the eater's time zone.
- `/r` **Given** the stage list, **When** a screen reader reads it, **Then** every stage says its state in words ("done", "waiting", "failed"), never by colour or icon alone.

#### support-9.10 · Escalate an overdue or failed deletion
As the support agent, I see when a deletion stage failed or the due-by date is near, and escalate it to the platform admin, so that no deletion quietly misses its legal window.
Covers: NFR-13, AT-29 · R23, R24 · conflict K4
- `/r` **Given** deletion `DEL-26-0905-P7T2` whose stage "Google notified" failed 3 times (`PROCESSOR_NOTIFY_TIMEOUT`) and whose due-by is 2026-10-05, **When** Privacy jobs opens on 2026-10-01, **Then** the row reads "Deletion · Needs attention · 4 days left" and offers only "Escalate to platform admin" — no retry, cancel or "mark done".
- `/r` **Given** the agent escalates with case `CASE-1201`, **When** it is sent, **Then** the row shows "Escalated 2026-10-01 11:05 by Mona K." and the Audit trail has `support.deletion_escalated`.
- `/s` **Given** a support token, **When** it calls any endpoint that cancels, pauses, speeds up or completes a deletion, **Then** 403 `FORBIDDEN_ROLE`.

#### support-9.11 · Confirm a finished deletion from its reference alone
As the support agent, I confirm a finished deletion from the deletion reference the eater kept, so that I can reassure them while nothing that identifies them remains.
Covers: WF-9 done-when ("a completion record without identifiers"), FR-078, NFR-13 · R4, R23
- `/r` **Given** the completion record for `DEL-26-0915-K3Q8` (requested 2026-09-15, completed 2026-10-14), **When** the agent enters `DEL-26-0915-K3Q8` in Account lookup on 2026-10-20, **Then** it shows "Deletion completed 2026-10-14 · requested 2026-09-15 · all stages done" and nothing else — no email, account id, device or Consent.
- `/r` **Given** the deleted account's email `nadia.synthetic@example.com` with case `CASE-1210`, **When** it is looked up, **Then** the answer is "No account matches this email", the same as for an email never registered.
- `/s` **Given** the stored completion record, **When** it is scanned, **Then** it holds no email, account id, name, device id or IP address.

#### support-9.12 · Point an eater to export or delete in the app
As the support agent, I give the eater the in-app path to export or delete in their own language and never do it for them, so that the eater stays in control and nobody asks them for ID.
Covers: FR-078, FRD §23.3 (export and deletion never paywalled) · R4 · SR10
- `/r` **Given** an eater whose app language is Arabic writes "please delete my account", **When** the agent opens Account state → Privacy help, **Then** the console offers the Arabic path first — «الإعدادات ← الخصوصية ← حذف الحساب» (proposal, A16) — and the English "Settings → Privacy → Delete account" second, each with Copy.
- `/r` **Given** any support screen, **When** the agent looks for a way to export, delete or change an account, **Then** none exists, and `POST /v1/privacy/export-or-delete` with a staff token returns 403 `FORBIDDEN_ROLE`.
- `/r` **Given** the Privacy help panel, **When** it is read, **Then** it never asks the agent to collect an ID document, a photo or a date of birth.

#### support-9.13 · Record a privacy request that arrived outside the app
As the support agent, I record each privacy request that reaches support by email, chat or phone — type, channel, time, case, account, outcome, and no content — so that every request is documented and answered within 30 days.
Covers: FR-082 · SR9 Art. 12(3), SR11 Art. 3(1)(a)(d) · R23, R31
- `/r` **Given** an email received 2026-10-01 09:30 UTC asking for a copy of data, **When** the agent records it in Privacy requests log as type "Access / export", channel Email, case `CASE-1215`, account `acct_51ab07`, **Then** the row shows received 2026-10-01 09:30 UTC, due by 2026-10-31, status "Open — pointed to in-app export".
- `/r` **Given** the eater then exports in the app (job `job_exp_88`), **When** the agent links that job, **Then** the row closes as "Completed by in-app export job_exp_88 on 2026-10-01".
- `/r` **Given** the log form, **When** the agent tries to paste the email body, **Then** the only free text is a 120-character outcome note marked "Don't paste food, health or message content".
- `/r` **Given** 3 rows due within 5 days, **When** Privacy requests log opens, **Then** they sort first with "Due in n days" in words.

### 9D · Failed jobs, as metadata

#### support-9.14 · See AI analysis failures as metadata
As the support agent, I see an account's failed Analyses as metadata — time, input kind, error code, model, prompt and schema versions, latency, retries — so that I can explain what failed without seeing the photo, audio or text.
Covers: FR-080, §7.2, §16.4, §18.2, §19.2, AT-32, NFR-03
- `/r` **Given** `acct_9c41e2` has Analysis `an_5530` (2026-10-01 12:04 Asia/Riyadh, input "photo + words", Failed, `AI_UNAVAILABLE`, model `gemini-3.8-flash`, prompt v14, schema v6, timed out at 12,000 ms, 2 retries), **When** the agent opens Failed jobs → Analyses, **Then** the row shows exactly those fields.
- `/s` **Given** `GET /v1/support/accounts/acct_9c41e2/failed-jobs?kind=analysis`, **When** it is read, **Then** no item carries image bytes, a storage path, a transcript, prompt text, `items[]`, `candidate_food_ids` or `assumptions[]`.
- `/r` **Given** that row, **When** the agent opens "What to tell the eater", **Then** the console offers, in Arabic and English: "The photo analysis timed out at 12:04. Recent Units and manual logging still work. The photo stays a Pending draft and was not logged." (AT-32, §7.2)

#### support-9.15 · Explain the daily AI limit
As the support agent, I see the eater's AI use against the daily quota and when it resets, so that I can explain `RATE_LIMITED` without changing anything myself.
Covers: §16.5, §18.2, blueprint §6 (Registry: per-user daily AI quotas), NFR-12 · A12
- `/r` **Given** `acct_9c41e2` used 10 of 10 analyses on 2026-10-01 and the 11th returned `RATE_LIMITED` at 19:40 Asia/Riyadh, **When** Account state is open, **Then** it reads "AI analyses today 10 of 10 · resets 2026-10-02 00:00 Asia/Riyadh (21:00 UTC)", and Failed jobs shows that `RATE_LIMITED` row.
- `/r` **Given** the Support role, **When** the agent looks for a way to raise the quota, **Then** there is none, and the panel says "Quotas are set by the platform admin."

#### support-9.16 · Know when the AI is paused for everyone
As the support agent, I see the platform's AI status, including the kill switch, on every support screen, so that I don't troubleshoot one account for a platform-wide pause.
Covers: §16.4 (kill switch preserves manual and cached logging), FR-080, AT-32 · A11
- `/r` **Given** the platform admin turned the kill switch on for image analysis at 2026-10-01 09:10 UTC, **When** any support screen is open, **Then** a quiet status bar reads "Image analysis paused for everyone since 09:10 UTC · manual and recent-Unit logging work", and Failed jobs rows after 09:10 carry the tag "paused".
- `/r` **Given** the switch is turned off at 09:55 UTC, **When** 60 seconds pass, **Then** the status bar is gone without a page reload.
- `/s` **Given** a support token, **When** it calls any Registry write endpoint, **Then** 403 `FORBIDDEN_ROLE`.

#### support-9.17 · See sync conflicts without the Entries
As the support agent, I see sync conflicts — command id, operation, device, time, `STALE_REVISION` — but not the Entries, so that I can tell the eater which device to open to reconcile.
Covers: FR-041, FR-043, §8.3, §18.2, AT-31, NFR-06
- `/r` **Given** E1 edited the same Entry offline on two devices and the server returned 409 `STALE_REVISION` to command `cmd_7a1e` (operation "correct", app 1.0.2, 2026-09-30 22:15 Asia/Riyadh), **When** the agent opens Failed jobs → Sync, **Then** the row shows those fields and "Waiting for the eater to reconcile on the iPhone with app 1.0.2", with no Food, quantity, kcal or Entry label.
- `/r` **Given** that row, **When** the agent opens "What to tell the eater", **Then** the text says the app shows both versions on that device to choose from, and that calories were not added twice (AT-31).
- `/s` **Given** a support token, **When** it calls any reconcile, correct, void, restore or move endpoint, **Then** 403 `FORBIDDEN_ROLE`.

#### support-9.18 · Answer "did it log twice?" from the duplicate record
As the support agent, I see how many duplicate deliveries of a command were ignored, so that I can reassure an eater that a retry did not add food twice.
Covers: FR-043, AT-10, AT-21
- `/r` **Given** command `cmd_44c0` was delivered 3 times on 2026-09-29 (the AT-10 fixture), **When** the agent opens Failed jobs → Sync → Duplicates ignored, **Then** it shows "cmd_44c0 · accepted once · 2 duplicate deliveries ignored · 2026-09-29 08:12", with no item, Unit or kcal.
- `/r` **Given** no duplicates in 30 days, **When** the tab opens, **Then** it says "No duplicate deliveries in the last 30 days."

#### support-9.19 · See Activity import results
As the support agent, I see Activity import results — accepted, updated, duplicate and conflict counts and the last import time — so that I can explain a missing or merged workout without seeing workouts.
Covers: FR-063, FR-064, FR-067, FRD §18 `POST /v1/activity/import`, AT-22 · R7
- `/r` **Given** E1's last import at 2026-10-01 07:30 Asia/Riyadh returned accepted 1, duplicate 2, conflict 0 (the AT-22 fixture), **When** the agent opens Failed jobs → Activity, **Then** those counts and the time show with "Duplicates were merged into one contribution", and no activity type, energy, duration or source app.
- `/r` **Given** no import for 7 days, **When** the tab opens, **Then** it says "No Activity imported in 7 days. Missing data is unknown, not proof of no exercise." (FR-067)

#### support-9.20 · Failed jobs that are empty, slow or failing
As the support agent, I get a designed answer when Failed jobs is empty, slow or broken, so that I never mistake a blank screen for "nothing wrong".
Covers: FR-080 · care group 4 · A15
- `/r` **Given** an account with no failures in 30 days, **When** Failed jobs opens, **Then** each tab says "No failed … in the last 30 days" and names the next place to check (Privacy jobs, Consents).
- `/r` **Given** the jobs API answers slowly, **When** a tab loads, **Then** table-shaped placeholders appear at once and are replaced by rows; after 10 s the tab shows "Still loading — Cancel".
- `/r` **Given** the API returns 503, **When** a tab loads, **Then** it says "Couldn't load failed jobs. Try again." with request id `req_…` for the platform admin, and the rest of Account state stays usable.
- `/r` **Given** 240 failed Analyses in 30 days, **When** the tab opens, **Then** 50 rows show with the total "240" and "Load 50 more".

### 9E · Boundaries, trail, desk and language

#### support-9.21 · Stay inside the Support role
As the support agent, I can use only support screens, and other staff roles cannot use mine, so that support, nutrition approval and platform administration stay separate.
Covers: FR-081, NFR-07, NFR-12 · SR8 (AC-5, AC-6), SR11 Art. 26(3)
- `/r` **Given** `staff_mona` (Support), **When** she opens `/console/reference`, `/console/policy`, `/console/registry`, `/console/roles` or `/console/audit` by URL, **Then** each says "You don't have access to this area" and the API returns 403 `FORBIDDEN_ROLE`.
- `/r` **Given** `staff_dina` (Nutrition approver) or `staff_ali` (Platform admin), **When** either opens Account lookup or calls `POST /v1/grants`, **Then** 403 `FORBIDDEN_ROLE` — only the Support role can request a Grant.
- `/s` **Given** every `/v1/support/*`, `/v1/grants*` and `/v1/audit/*` route, **When** the role-matrix test calls each with every staff role and an eater token, **Then** only the intended role gets a 2xx.

#### support-9.22 · Never cross from one account to another
As the support agent, I reach only the account I looked up, by its own ids, so that a typo or a tampered id can never show another eater's metadata.
Covers: NFR-07, FRD release blockers ("any cross-user exposure") · A13
- `/r` **Given** Account state for `acct_9c41e2` is open, **When** the agent edits the URL to `/console/accounts/acct_51ab07/failed-jobs` without looking that account up, **Then** the console says "Look up this account first" and the API returns 403 `LOOKUP_REQUIRED`.
- `/s` **Given** Analysis `an_5530` belongs to `acct_9c41e2`, **When** it is requested under `acct_51ab07`, **Then** 404, never the job.

#### support-9.23 · Everything I do leaves a trail
Shared: support agent + auditor.
As the support agent, I know each look-up, view, re-queue, escalation and Grant step is recorded with who, what, when, where and outcome, so that honest work is provable and misuse is visible.
Covers: FR-081, FR-082 · SR3, SR8 (AU-3, AC-6(9)), SR11 Art. 26(4) · R24
- `/r` **Given** `staff_mona` looked up `acct_9c41e2` and opened Account state and Failed jobs on 2026-10-01, **When** `staff_hana` (Auditor) filters the Audit trail by `staff_mona` and that day, **Then** three events show, each with event type, time (UTC), where (console route and API path), staff id and role, account id, case reference and outcome.
- `/s` **Given** any `/v1/support/*` request, **When** it completes with 2xx or 4xx, **Then** exactly one audit event is written; if the audit write fails, the request fails and returns no data.
- `/r` **Given** the Support role, **When** `staff_mona` opens the Audit trail, **Then** 403 — support can neither read nor edit its own trail.

#### support-9.24 · Work fast at a desk, and still at phone width
As the support agent, I work keyboard-first on a large screen with account state, jobs and the Grant side by side, and the console still works at phone width, so that I close cases quickly and can check one away from my desk.
Covers: blueprint §0 (console proved at desktop and ~390 px), NFR-08 · care groups 2, 3, 6
- `/r` **Given** a 1920×1080 browser window, **When** Account state is open, **Then** Account state, Privacy jobs / Failed jobs and the Grant panel show as three columns without horizontal scroll, with ids in a monospaced face and tabular numbers.
- `/r` **Given** a 390 px wide window, **When** the same account is open, **Then** the columns stack as Account state → Grant → jobs, every action is reachable, there is no horizontal page scroll, and the Grant bar stays pinned at the top.
- `/r` **Given** keyboard only, **When** the agent tabs from Account lookup through Account state and Failed jobs to "Request diary access", **Then** every control shows a visible focus ring and every action works without a mouse.
- `/r` **Given** 200 % browser zoom in light and in dark appearance, **When** Account state is open, **Then** nothing clips or overlaps and all text meets 4.5:1 contrast.

#### support-9.25 · Use the console in Arabic, and reply in the eater's language
As the support agent, I can use the console in Arabic and always see the eater's app language, so that I reply in the eater's language and Arabic-speaking agents work in theirs.
Covers: blueprint §0 line 5 (English + Arabic) · care group 6 · A16
- `/r` **Given** `staff_omar` set the console to Arabic, **When** Account state opens, **Then** the layout mirrors (navigation on the right, back arrow pointing right), while ids, codes and times stay left to right and the Grant countdown bar fills from the right.
- `/r` **Given** the eater's language is Arabic, **When** any "What to tell the eater" text opens, **Then** the Arabic text comes first and the English second, whatever the console language.

---

## 5 · Journey 10 — just-in-time diary access (WF-10, support side)

Steps: **10A** decide that metadata is not enough → **10B** request a Grant → **10C** the eater decides in Settings → **10D** read inside the box → **10E** the box ends → **10F** history and the end-to-end proof.

### 10A · Decide

#### support-10.1 · Ask for diary access only from a case
As the support agent, I start a Grant request only from an account I looked up, after metadata could not answer, so that diary access is the exception, not the habit.
Covers: FR-081 · SR1 ("usually … telemetry"), SR5, SR7
- `/r` **Given** Account state for `acct_9c41e2` is open, **When** the agent looks for the diary, **Then** there is no Diary link, only "Request diary access (Grant)", and `/console/accounts/acct_9c41e2/diary` says "No active Grant".
- `/r` **Given** the agent presses "Request diary access", **When** the Grant request form opens, **Then** account, eater language and eater time zone are filled in, and reason, days and areas are empty.
- `/s` **Given** any read under `/v1/grants/{id}/…`, **When** it is called without an approved, unexpired Grant, **Then** 403 `GRANT_REQUIRED` and an audit event `grant.read_denied`.

### 10B · Request

#### support-10.2 · Request a Grant with reason, scope, duration and case
As the support agent, I request a Grant naming a reason from a fixed list, the diary days and areas I need, a duration and my case reference, so that the eater can decide knowing exactly who wants what, why and for how long.
Covers: FR-081, WF-10, blueprint §3 ("Support → Eater: request just-in-time diary access") · SR1, SR4, SR5 · A4, A5, A16
- `/r` **Given** `staff_mona` chooses reason `sync_missing_entry`, diary days 2026-09-28 to 2026-09-30, areas "Entries and day reports" and "My Units", duration 1 hour and case `CASE-1182`, **When** she presses "Send request", **Then** the panel reads "Grant grant_31f0 · Waiting for the eater · request expires 2026-10-04 10:05 UTC", and `POST /v1/grants` returned 201 with state `requested`.
- `/r` **Given** the form, **When** it opens, **Then** duration offers 1 hour (selected), 4 hours and 24 hours, nothing longer; areas offer "Entries and day reports", "My Units" (Unit, Composite and Recipe versions), "Templates" and "Activity", none selected.
- `/m` **Given** the Grant state machine, **When** events arrive in any order, **Then** the only paths are requested → approved → expired | revoked | ended, and requested → declined | expired_unanswered | withdrawn; `approved` is reachable only from `requested`.
- `/r` **Given** the request was sent, **When** the auditor opens the Audit trail, **Then** `grant.requested` shows staff, account, reason, days, areas, duration and case.

Reason catalogue (proposal for the string catalogue, A16):

| code | English | Arabic |
|---|---|---|
| `sync_missing_entry` | An entry is missing or appears twice | إدخال مفقود أو ظاهر مرتين |
| `report_mismatch` | A day report total looks wrong | إجمالي تقرير اليوم يبدو غير صحيح |
| `unit_calculation` | A Unit or Recipe calculates unexpectedly | وحدة أو وصفة تُحسب بشكل غير متوقع |
| `activity_import` | Imported Activity looks wrong | النشاط المستورد يبدو غير صحيح |
| `other` | (the agent's one-sentence note, shown as written) | (كما كتبها الموظف) |

#### support-10.3 · Be told what to fix when a request is invalid
As the support agent, I am told what to fix when a request is incomplete or too broad, so that only small, valid requests reach the eater.
Covers: FR-081 · SR5 · care group 4 · A4, A5
- `/r` **Given** no reason is chosen, **When** she presses Send, **Then** the reason field says "Choose why you need access" and nothing is sent (422 `GRANT_INVALID`, field `reason`).
- `/r` **Given** reason `other` with an empty note, **When** sent, **Then** the note field says "Describe the reason in one sentence the eater will read".
- `/r` **Given** diary days 2026-09-01 to 2026-09-30, **When** sent, **Then** the days field says "Ask for 14 diary days or fewer" (422, field `days`).
- `/r` **Given** a day after the eater's current diary day, such as 2026-10-03, **When** sent, **Then** the days field says "Choose days up to today in the eater's time zone (Asia/Riyadh)".
- `/s` **Given** a request with duration 48 h sent straight to the API, **When** it is received, **Then** 422 field `duration` listing the allowed values 1 h, 4 h, 24 h.

#### support-10.4 · Never stack requests on one eater
As the support agent, I cannot open a second request while one is waiting for the same eater, so that the eater is never asked twice at once.
Covers: FR-081 · conflict path
- `/r` **Given** `grant_31f0` (state `requested`, by `staff_mona`) for `acct_9c41e2`, **When** `staff_omar` presses "Request diary access" on the same account, **Then** the panel reads "Mona K. has a request waiting for this eater (expires 2026-10-04 10:05 UTC)" and no form opens; `POST /v1/grants` returns 409 `GRANT_REQUEST_OPEN` with that Grant's id and state.
- `/s` **Given** two `POST /v1/grants` for one account within 50 ms, **When** both are processed, **Then** exactly one returns 201 and the other 409.

#### support-10.5 · Wait, keep helping, or withdraw
As the support agent, I see a waiting request's state live, keep helping from metadata meanwhile, and can withdraw it, so that waiting for the eater never blocks the case.
Covers: FR-081 · SR2 ("support response time increases") · A11
- `/r` **Given** `grant_31f0` is waiting, **When** the eater has not answered, **Then** the panel reads "Waiting for the eater · sent 10:05 UTC · expires in 2 d 23 h", and Account state, Privacy jobs and Failed jobs stay usable.
- `/r` **Given** the agent presses "Withdraw request", **When** it is confirmed by the server, **Then** the state is `withdrawn`, the eater's Settings → Privacy → Grants shows it in history as "Withdrawn by support" with no Allow button, and `grant.withdrawn` is audited.
- `/r` **Given** the panel is open, **When** the eater answers, **Then** the panel shows the new state within 5 s without a reload and the screen reader announces it once.

### 10C · The eater decides in Settings

#### support-10.6 · The eater sees who, why, what and how long — and allows it
Shared: support agent + eater.
As the support agent, I rely on the eater seeing my name, the reason, the days and areas, the duration and the case in Settings → Privacy → Grants and allowing it there, so that access exists only because the eater chose it.
Covers: FR-081, WF-10, blueprint §2 (the approver is the eater) · SR1, SR2, SR14
- `/r` **Given** `grant_31f0`, **When** E1 opens Settings (badge "1") → Privacy → Grants on the iOS simulator, **Then** the request shows who "Mona K. · Sips & Bytes Support", why (the reason text), what "Entries and day reports, My Units · 28–30 Sep 2026", how long "1 hour from when you allow", case `CASE-1182`, the buttons "Allow" and "Decline", and the line "Support can read, not change. Every view is listed here."
- `/r` **Given** the eater taps Allow at 13:20 Asia/Riyadh, **When** the server confirms, **Then** the app shows "Allowed until 14:20", and the console panel turns to "Active · read-only · ends 12:20 your time (11:20 UTC)" — the time box starts at approval, not at request.
- `/s` **Given** `POST /v1/grants/grant_31f0/approve` with the eater's own token, **When** it is processed, **Then** 200, state `approved`, `expires_at` = `approved_at` + 1 h, and `grant.approved` records method `in_app` and the device.
- `/r` **Given** E1's app is in Arabic, **When** the request shows, **Then** it is right to left, the reason reads «إدخال مفقود أو ظاهر مرتين», the days read «٢٨–٣٠ سبتمبر ٢٠٢٦» in Arabic-Indic numerals, and the Latin name "Mona K." sits in an isolated left-to-right run without reordering the sentence.

#### support-10.7 · A declined request gives no access
Shared: support agent + eater.
As the support agent, I see a declined request end with no access and no pressure on the eater, so that "no" is a real answer.
Covers: FR-081, WF-10 done-when ("a declined Grant gives no access") · A14
- `/r` **Given** the eater taps Decline on `grant_31f0` at 10:12 UTC, **When** the console updates, **Then** the panel reads "Declined by the eater at 10:12 UTC", the eater was not asked for a reason, and the panel suggests the metadata checks to try next.
- `/s` **Given** state `declined`, **When** `staff_mona` calls `GET /v1/grants/grant_31f0/days/2026-09-29`, **Then** 403 `GRANT_DECLINED` and `grant.read_denied` is audited.
- `/r` **Given** a decline less than 24 h old, **When** any agent opens the Grant form for that account, **Then** the form shows "The eater declined a request at 10:12 UTC today" above the reason field.

#### support-10.8 · An unanswered request expires by itself
As the support agent, I see an unanswered request expire on its own, so that an old request can never turn into access later.
Covers: FR-081 · SR1 (unanswered requests expire) · A6
- `/r` **Given** `grant_40aa` was requested 2026-10-01 10:05 UTC and never answered, **When** 2026-10-04 10:05 UTC passes, **Then** the console shows "Expired unanswered", and the eater's Settings → Privacy → Grants lists it in history with no Allow button.
- `/s` **Given** state `expired_unanswered`, **When** the eater's approve call arrives, **Then** 409 with state `expired_unanswered`; a late approval never activates it.

#### support-10.9 · Only the eater can approve
As the support agent, I cannot approve any Grant — mine or anyone's — and no staff role can, so that nobody at Sips & Bytes can give themselves a diary.
Covers: FR-081, blueprint §2 ("no staff member can approve their own request") · SR7, SR8 (AC-5)
- `/s` **Given** `grant_31f0`, **When** `POST /v1/grants/grant_31f0/approve` is sent with the token of `staff_mona`, `staff_omar`, `staff_ali` or `staff_hana`, **Then** each returns 403 `FORBIDDEN_ROLE` and `grant.approve_denied` is audited.
- `/s` **Given** another eater's token (`acct_51ab07`), **When** it calls approve on `grant_31f0`, **Then** 404 (NFR-07).
- `/r` **Given** the console, **When** any staff user views a waiting Grant, **Then** no Allow or Approve control exists anywhere.

#### support-10.10 · The eater is offline
Shared: support agent + eater.
As the support agent, I see a request keep waiting while the eater is offline, because the app answers Grants only online, so that an approval is never queued and applied later by surprise.
Covers: FR-081, FRD §8.3 (the outbox is for food commands) · care group 4
- `/r` **Given** the eater opens Settings → Privacy → Grants with no network, **When** the cached request shows, **Then** Allow and Decline are disabled with "Connect to answer this request", and the console still reads "Waiting for the eater".
- `/m` **Given** the app's outbox, **When** a Grant answer is attempted offline, **Then** no outbox command is created.

### 10D · Read inside the box

#### support-10.11 · Read the approved days, read-only
As the support agent, I read the approved diary days and areas in a read-only Diary view inside a clearly marked Grant panel, so that I can find the problem and nothing more.
Covers: FR-081, WF-10, FR-046 (timeline, source details, correction history), FR-069, FR-070 · A10
- `/r` **Given** `grant_31f0` is active, **When** the agent opens Diary (read-only) for 2026-09-29, **Then** she sees that day's Entries in time order, each with Unit, count, Evidence badge, source version and its Correction, Void and Restore history, and the day report (Target, consumed, remaining, coverage) with the same numbers the eater's own day report shows.
- `/r` **Given** the Diary view, **When** it renders, **Then** the Grant panel has its own border and the title "Read-only · Grant grant_31f0 · ends 12:20 your time", has no edit, delete, export, copy-all or print control, and carries a faint watermark "staff_mona · grant_31f0".
- `/r` **Given** the areas are "Entries and day reports" and "My Units", **When** the agent opens My Units, **Then** she sees Unit, Composite and Recipe versions with components, Evidence and version history, while Templates and Activity read "Not in this Grant".

#### support-10.12 · Out-of-scope reads are refused
As the support agent, I am stopped at the edge of what the eater approved, so that a Grant for three days never becomes a look at three months.
Covers: FR-081, NFR-07, FRD §8.1 · SR5, SR8 (AC-6)
- `/r` **Given** `grant_31f0` covers 2026-09-28 to 2026-09-30, **When** the agent moves to 2026-09-27, **Then** the view says "27 Sep 2026 is outside this Grant" and shows no data; the API returns 403 `GRANT_OUT_OF_SCOPE` and `grant.read_denied` is audited.
- `/s` **Given** `grant_31f0`'s areas, **When** `GET /v1/grants/grant_31f0/activity?day=2026-09-29` or `…/templates` is called, **Then** 403 `GRANT_OUT_OF_SCOPE`.
- `/s` **Given** `grant_31f0` is for `acct_9c41e2`, **When** a read names a day, Entry or Unit id of `acct_51ab07`, **Then** 404 and no data.
- `/m` **Given** E1's diary-day boundary 04:00 Asia/Riyadh, **When** an Entry eaten 2026-09-30 02:30 local is checked against scope, **Then** it belongs to diary day 2026-09-29 and is in scope.

#### support-10.13 · No changes under a Grant
As the support agent, I cannot change anything while reading, so that a Grant is never a way to edit someone's history.
Covers: FR-041, FR-081, FRD §16.2
- `/s` **Given** an active Grant token, **When** it is used on `POST /v1/consumption`, `…/corrections`, `…/void`, `POST /v1/units` or `POST /v1/recipes`, **Then** each returns 403 `GRANT_READ_ONLY` and diary day 2026-09-29's revision is unchanged.
- `/r` **Given** the Diary view, **When** the agent inspects the Grant panel, **Then** it contains no input, button or shortcut that edits data.

#### support-10.14 · Media and the safety screen stay out of every Grant
As the support agent, I never see photos, audio, transcripts, safety-screen answers or the tracking-only mode, even under an active Grant, so that the most sensitive data has no support path at all.
Covers: FRD §19.2 ("Access to raw evidence for quality review requires explicit consent and restricted roles"), FR-038, FR-077, FR-078 · R8, R21
- `/r` **Given** Entry `en_9921` on 2026-09-29 was logged from Analysis `an_5512` with a photo, **When** the agent opens it under `grant_31f0`, **Then** she sees Evidence "estimated analogue" and "From a photo analysis", with no image, thumbnail, transcript or link to media.
- `/r` **Given** E4 (tracking-only) approved a Grant for "Entries and day reports", **When** the agent opens a day report, **Then** Target reads "No Target", exactly as for an eater who never set one; the mode is not shown.
- `/s` **Given** a Grant request, **When** its areas include media, audio, transcripts, SafetyScreen or GoalPlanVersion inputs, **Then** 422 `GRANT_INVALID` — the schema has no such areas.

#### support-10.15 · Every read is listed for the eater and the auditor
Shared: support agent + eater + auditor.
As the support agent, I know each read I make under a Grant is recorded and listed to the eater in the Grant's access history and to the auditor, so that the eater can see what I saw.
Covers: FR-081, FR-082, WF-10 done-when ("every read shows in the auditor's trail") · SR3, SR13, SR15
- `/r` **Given** the agent opened Diary 2026-09-29, Entry `en_9921` and My Units during `grant_31f0`, **When** the eater opens Settings → Privacy → Grants → that Grant, **Then** its access history lists three lines such as "Mona K. viewed your diary for 29 Sep · 13:24", in the eater's language and time zone.
- `/r` **Given** the same, **When** `staff_hana` (Auditor) filters the Audit trail by `grant_31f0`, **Then** she sees in order `grant.requested`, `grant.approved`, three `grant.read` (record type, record id, time UTC, staff, outcome `allowed`) and `grant.expired`.
- `/s` **Given** any Grant read, **When** its audit write fails, **Then** the read returns 503 and no data.
- `/s` **Given** the audit collection, **When** any role, staff or service tries to update or delete an event, **Then** the Firestore rules test in the emulator refuses it (append-only).

#### support-10.16 · Always know how long is left, in both time zones
As the support agent, I see the time left and the end time in my zone and the eater's, with quiet warnings before the end, so that I finish or ask again in time.
Covers: FR-081, FRD §8.1 · care groups 3, 6
- `/r` **Given** `grant_31f0` ends 11:20 UTC and the agent is in Europe/Dublin, **When** the Diary is open at 10:33 UTC, **Then** the Grant bar reads "47 min left · ends 12:20 your time · 14:20 eater's time (Asia/Riyadh)".
- `/r` **Given** 10 minutes and then 2 minutes remain, **When** each moment passes, **Then** the bar says so quietly and the screen reader announces it once (polite), with no dialog, sound or per-second announcement.
- `/r` **Given** reduced motion is on, **When** the countdown advances, **Then** it updates in place without animation.

### 10E · The box ends

#### support-10.17 · Access ends by itself at the end of the box
As the support agent, I lose access exactly when the Grant expires, enforced by the server, so that no open tab or cached page outlives the eater's permission.
Covers: FR-081, WF-10 done-when · SR1, SR4, SR8 (AC-2(2))
- `/r` **Given** `grant_31f0` expires at 11:20:00 UTC, **When** the agent moves to 2026-09-30 at 11:20:05 UTC, **Then** the view says "This Grant ended at 11:20 UTC (expired)" and the diary content is removed from the page, not just greyed out.
- `/s` **Given** the expired Grant, **When** a read is sent at 11:20:01 UTC, **Then** 403 `GRANT_EXPIRED`, judged by the server clock, and `grant.read_denied` is audited.
- `/s` **Given** the expiry scheduler is stopped, **When** a read arrives after `expires_at`, **Then** it still fails (every read checks `expires_at`), and `grant.expired` is written when the scheduler resumes.
- `/r` **Given** expiry, **When** the eater opens Settings → Privacy → Grants, **Then** the Grant shows "Ended 14:20 · expired" with its access history.
- `/s` **Given** the Diary view's HTTP responses, **When** they are inspected, **Then** each carries `Cache-Control: no-store`, so nothing is served from the browser cache after the box ends.

#### support-10.18 · The eater ends access early
Shared: support agent + eater.
As the support agent, I lose access the moment the eater ends a Grant in Settings, so that the eater's "stop" works at once.
Covers: FR-081, AT-29 · SR2 ("revoked at any time"), SR13
- `/r` **Given** `grant_31f0` is active, **When** the eater taps "End access" in Settings → Privacy → Grants at 10:41 UTC, **Then** the agent's next read returns 403 `GRANT_REVOKED`, the panel reads "The eater ended access at 10:41 UTC", and the diary content is removed.
- `/r` **Given** the eater ended it, **When** the app confirms, **Then** it says "Support can no longer see your diary" and the access history keeps every read made before 10:41.
- `/s` **Given** a Grant is active, **When** the eater requests account deletion, **Then** the Grant becomes `revoked` (reason `account_deletion`) and `POST /v1/grants` for that account returns 409 `ACCOUNT_DELETION_PENDING` (AT-29).

#### support-10.19 · End access as soon as I'm done
As the support agent, I end a Grant as soon as I have the answer, so that I hold access no longer than needed.
Covers: FR-081 · SR8 (AC-6)
- `/r` **Given** `grant_31f0` is active with 31 min left, **When** the agent presses "End access now", **Then** the state is `ended`, the diary content is removed, the eater's Settings → Privacy → Grants shows "Ended early by support at 10:49", and `grant.ended` is audited.
- `/s` **Given** an ended Grant, **When** a read is sent, **Then** 403 `GRANT_EXPIRED` with state `ended`.

#### support-10.20 · No extensions — ask again
As the support agent, I cannot extend a Grant; I ask for a new one, so that every extra hour is the eater's choice.
Covers: FR-081 · SR13 (to change the length, revoke and grant again)
- `/r` **Given** `grant_31f0` is active, **When** the agent looks for "Extend", **Then** there is none; in the last 10 minutes the bar offers "Ask the eater for more time", which opens a new request with the same reason, days and areas.
- `/s` **Given** `PATCH /v1/grants/grant_31f0` changing `expires_at` or scope, **When** any role sends it, **Then** 405 — a Grant cannot be changed after it is requested.
- `/r` **Given** a new request while `grant_31f0` is still active, **When** it is sent, **Then** it is accepted (the one-waiting-request rule counts only `requested` Grants) and the eater sees it as a separate request.

#### support-10.21 · An idle console during a Grant shows nothing
As the support agent, I find the diary hidden when my console session times out during a Grant, so that a desk left unattended shows no diary.
Covers: FR-081 · SR6 (automatic logoff), SR8 (AC-12) · A7
- `/r` **Given** an active Grant and 15 minutes idle, **When** the session ends, **Then** the diary content is removed and sign-in shows; after signing in again before expiry the agent can reopen the Diary, and the end time is unchanged.

### 10F · History and proof

#### support-10.22 · My Grants — a history I can account for
As the support agent, I see my Grants in every state with times and cases, so that I can follow up each case and account for my access.
Covers: FR-081 · SR2 ("a historical view of all requests that were approved, dismissed, revoked, or expired")
- `/r` **Given** `staff_mona` has Grants in the states requested, approved, declined, expired_unanswered, expired, revoked, ended and withdrawn in the last 30 days, **When** she opens My Grants, **Then** each row shows Grant id, account id, case, reason, state in words, and requested, answered and ended times in her time zone; the state filter works; no diary content appears.
- `/r` **Given** no Grants, **When** My Grants opens, **Then** it says "You have no Grants. Most cases are solved from Account state and Failed jobs."

#### support-10.23 · The whole Grant, end to end
Shared: support agent + eater + auditor.
As the support agent, I run one full Grant — request, the eater allows it in Settings, I read inside the box, it expires, the trail shows it — and see a declined one give nothing, so that WF-10's promise is proved in the served product.
Covers: WF-10 done-when, FR-081, FR-082
- `/r` **Given** E1 on the iOS simulator and `staff_mona` in the console, **When** she requests `grant_31f0` (1 hour, 28–30 Sep, Entries and day reports + My Units), the eater allows it in Settings → Privacy → Grants, she reads 2026-09-29, and the clock passes `expires_at`, **Then** the read worked inside the box and failed after it with `GRANT_EXPIRED`, the eater's access history lists the read, and the auditor's Audit trail shows requested → approved → read → expired.
- `/r` **Given** a second request `grant_31f9` that the eater declines, **When** the agent tries to read any day, **Then** 403 `GRANT_DECLINED`, and the Audit trail shows requested → declined → read_denied.

---

## 6 · The experience this persona needs

- **Device and place.** A desk, a large monitor (1440 px and up), keyboard first, a helpdesk window beside the console (A1). Steady office network and light. The same console must stay usable at about 390 px for a quick check away from the desk (§0).
- **The moments that matter.** (1) The first ten seconds of a case: one code in, the account's state on screen, the likely cause visible (SR16). (2) The ask: a Grant request that a stranger reading it on a phone understands at once. (3) The edge of the box: access ends exactly when promised, and the agent never wonders whether a page still shows a diary.
- **The feeling it must leave.** For the agent: *"I can help without prying, and the tool proves I did."* For the eater who answers: *"They asked, I chose, I can see what they saw, and it ended."*
- **The matching style.** Dense, fast and calm, like an approver's desk tool: tables, monospaced ids, tabular numbers, times in two zones, keyboard shortcuts, no animation on repeated actions. One element is deliberately loud: the **Grant panel**, the only place private data can appear, with its own border, title, countdown and watermark, so the agent always knows when they are inside someone's diary. One accent colour is reserved for "inside an active Grant" and means nothing else. "Needs attention" (an overdue deletion) is the only alarm state; ordinary states use words, not red. "What to tell the eater" texts come in the eater's language first.

---

## 7 · The care questions this persona raises, answered as requirements

Size is platform, so every group is asked on every support screen: **Account lookup, Account state, Privacy jobs, Failed jobs, Privacy requests log, Grant request form, Grant panel with Diary (read-only), My Grants**, plus the eater's **Settings → Privacy → Grants** where shared.

**1 · Does it deserve to exist, and where does it live**
- Each screen has one sentence. Account lookup: "find one account from a code the eater gives you." Account state: "explain most issues without the diary." Failed jobs: "what failed, when and why — never what it was about." Grant panel: "read what the eater allowed, until it ends." Every element serves its sentence; elements that do not (charts, food content, user lists) are left out.
- What we said no to: a list or search of users; fuzzy or partial look-up; a "recent accounts" list; any staff deletion, export, consent, quota or ledger control; break-glass access (SR7); Grant extensions; media in Grants (FRD §19.2).
- Places sit in the navigation (Account lookup, Privacy requests log, My Grants); actions sit next to what they act on (Re-queue on the export row, Escalate on the deletion row, Request diary access on Account state, End access now in the Grant bar).
- No dialogs for Grant state changes; the panel changes in place. The eater's answer happens on their own screen, not in a pop-up.
- Durations, days and areas are the only Grant choices. They exist because the eater must see exactly what is asked (SR1). Reason, days and areas start empty on purpose, so the scope is chosen, never assumed (SR5).

**2 · How it is found and understood**
- Titles name the place and the account: "Account state — acct_9c41e2", "Diary (read-only) — 29 Sep 2026".
- One word per thing, the map's words: Grant, Entry, Unit, Composite, Recipe, Template, Analysis, Consent, Evidence, Activity, Policy, Pending, Correction, Void, Restore. The eater's screen uses "Grants" with the plain line "Support can read, not change" (see conflict K6).
- Every button is a verb: Look up, Send request, Withdraw request, End access now, Ask the eater for more time, Re-queue, Escalate to platform admin, Copy.
- Key status sits where the eye already is: the Grant bar at the top, the platform AI status bar under it, the deletion due-by on the job row.
- Nothing is typed that the system knows: account, eater language and time zone are filled in; reasons come from a list.

**3 · How it feels**
- Every action answers: Send request → "Waiting for the eater"; Allow (in the app) → "Active · read-only · ends …"; End access now → "Ended"; Re-queue → "Queued".
- Loudness matches weight: quiet status for the AI pause and the 10- and 2-minute warnings; "Needs attention" only for a deletion near or past its legal window.
- One main action per state: Send request (form), End access now (active Grant), Look up (lookup). None of them destroys data.
- The chosen values (1 h default, 72 h request expiry, 14 days, 15 min idle, 10 and 2 min warnings, 5 s and 60 s refresh) are assumptions A4–A11 to try and tune on the served console, never framework defaults.
- Tables align on a fixed spacing grid; ids line up in a monospaced column.

**4 · When it goes wrong, is empty, or is slow**
- Empty states say what to do next: no failed jobs, no privacy jobs, no Grants, no duplicates, no Activity import (stories 9.7, 9.18–9.20, 10.22).
- Loading shows table-shaped placeholders at once, and "Still loading — Cancel" after 10 s (9.20). Export and deletion progress is shown as honest stages with dates (9.9).
- Errors sit next to the field and say how to fix it, with no blame or "oops" (10.3). Request ids are shown for escalation (9.20).
- Undo: a waiting request can be withdrawn (10.5). Ending a Grant needs no confirmation, because it only removes access and a new request is always possible.
- There is no warning for permanent loss because the support console has no destructive action at all.
- No network in the console: Account state and job lists show the last loaded data with a quiet "Offline — showing data from 10:42"; the Diary is never cached and shows nothing offline (10.17 `no-store`). In the app, an offline eater cannot answer a Grant and is told why (10.10).
- When a command cannot work, the screen says why: "This support code has expired", "Look up this account first", "27 Sep 2026 is outside this Grant", "The eater ended access at 10:41 UTC".

**5 · The inside the user never sees**
- Code, data and logs use the map's names (`Grant`, `grant.read`, `Analysis`, `Consent`), one name per thing.
- Logs and support responses carry ids, times, states and typed codes, never food, quantities, photos, audio, transcripts or profile values (FRD §19.2); a support response is built from an allow-list and a new field is private until classed (9.5).
- Every support and Grant request writes one audit event with what, when, where, source, outcome and identity (SR8 AU-3); a failed audit write fails the request (9.23, 10.15); the trail is append-only.
- We collect only what support needs: support codes expire in 24 h; the privacy requests log keeps no message content (9.13); the completion record keeps no identifiers (9.11).
- Sample data is synthetic, with Arabic text, long names and two time zones (the fixtures above).
- The product claims only what it does: "Support can read, not change" is true because the server refuses every write with a Grant token (10.13).

**6 · Inclusion**
- At 200 % zoom and the largest text, nothing clips (9.24). Text meets 4.5:1 in light and dark. Every control shows a focus ring, and the whole support flow works by keyboard (9.24).
- No meaning by colour alone: stages and Grant states are words (9.9, 10.22).
- Screen readers get labels for each control and a polite announcement for state changes and the two countdown warnings, not every second (10.5, 10.16).
- Timers: the Grant end is a deliberate security limit. It is shown from the start in two time zones, warned at 10 and 2 minutes, and can be renewed only by asking the eater (10.20). Nothing else disappears on a timer.
- Arabic: the console mirrors, while ids, codes and clocks stay left to right (9.25). The eater's Grant request mirrors, uses the eater's numerals, and isolates the agent's Latin name (10.6).
- A newcomer to support can work from "What to tell the eater" texts, available in both languages (9.12, 9.14, 9.17).

---

## 8 · Stories shared with other personas

| story | shared with | what the other persona does |
|---|---|---|
| support-9.2 | eater | reads the support code in Settings → Help |
| support-9.23 | auditor | filters the Audit trail by staff and day |
| support-10.6 | eater | sees the request in Settings → Privacy → Grants and taps Allow |
| support-10.7 | eater | taps Decline without giving a reason |
| support-10.10 | eater | cannot answer offline; is told to connect |
| support-10.15 | eater, auditor | the eater reads the access history; the auditor reads every `grant.*` event |
| support-10.18 | eater | taps End access |
| support-10.23 | eater, auditor | the end-to-end WF-10 proof |

---

## 9 · Conflicts for the model phase

- **K1 · eater ↔ support — what the eater's access history shows.** Transparency (SR13, SR15) argues for listing every staff view, including metadata-only look-ups. Showing look-ups that touched no diary may alarm the eater and clutter Settings. This lens lists every Grant read, one per line, and proposes a once-a-day line for metadata views ("Support viewed your account status — no diary data"). The eater lens may prefer Grant reads only.
- **K2 · eater ↔ support — how the eater learns a request is waiting.** A push notification needs the notification permission, which the app may not require (R2). Without it there is only the Settings badge and the agent's email reply, so a request may wait. This interacts with the 72 h expiry (A6).
- **K3 · platform admin ↔ nutrition approver — who owns the Grant limits** (durations, maximum days, request expiry, idle logoff, email look-up rate). They are not nutrition Policy (approver-owned, blueprint §6) and not model Registry. The proposal is a versioned configuration owned by the platform admin and readable by the auditor. The model phase decides where it lives.
- **K4 · platform admin ↔ support — who re-queues failed jobs.** This lens gives support a single re-queue of a failed **export** (A9) and only "escalate" for deletion. The platform-admin lens may want all job retries for itself, or may give support more.
- **K5 · auditor ↔ support ↔ platform admin — role combination.** Separation of duties (SR7, SR8 AC-5, FR-081) means one staff account must not hold Support together with Auditor or Platform admin. Otherwise one person could request access and review their own trail, or grant themselves the role. The roles screen (platform admin) must refuse the combination. Owner of that rule: platform admin or auditor lens.
- **K6 · eater ↔ map vocabulary — the word on the eater's screen.** The map's word is "Grant". Eaters may not understand "Grants" as a Settings label. This lens keeps "Grant" with an explanatory line. The eater lens may want a plainer label, which would need a vocabulary change in the map.
- **K7 · nutrition approver ↔ support — approvers looking into an eater's Entry.** An approver checking a complaint about a Food record may want to see which Food version an eater's Entry used. FR-081 separates the roles, and only Support can request a Grant (9.21). The approver would have to work through support or through de-identified quality metrics (FR-080).

---

## 10 · Terms this lens needs that the map does not yet name

Proposals for the model phase, to be added to the map's vocabulary or renamed: **support code** (a short-lived code the eater reads from Settings → Help), **deletion reference** (the id on a deletion and its completion record), **case reference** (the helpdesk's ticket number, information-only), **Privacy requests log** (requests received outside the app), **Grant states** (requested, approved, declined, expired_unanswered, withdrawn, expired, revoked, ended), **Grant areas** (Entries and day reports, My Units, Templates, Activity), and the screen names **Account lookup, Account state, Privacy jobs, Failed jobs, Diary (read-only), My Grants**, and **Settings → Privacy → Grants** in the app.

---

## 11 · Coverage

| requirement or test | stories |
|---|---|
| FR-001 (anonymous session, local trial) | 9.2 |
| FR-041, FR-043, AT-10, AT-21, AT-31 (ledger, retries, conflicts) | 9.17, 9.18, 10.13 |
| FR-046, FR-069, FR-070 (timeline and day report, read under a Grant) | 10.11 |
| FR-063, FR-064, FR-067, AT-22 (Activity import) | 9.6, 9.19 |
| FR-075, FR-078, AT-29 (export, deletion, propagation) | 9.7–9.12, 10.18 |
| FR-076 (separate consents) | 9.6 |
| FR-077, FR-038, §19.2 (media and logs) | 9.5, 9.14, 10.14 |
| FR-079 (no secondary use) | 9.5 |
| FR-080 (console: failed jobs, de-identified) | 9.1, 9.4, 9.14–9.20 |
| FR-081 (separate support privileges; JIT and audit) | 9.1–9.6, 9.21–9.23, 10.1–10.23 |
| FR-082 (privacy review, retention verification) | 9.13, 9.23, 10.15, 10.23 |
| NFR-03, NFR-06, AT-32, §7.2, §16.4, §16.5 (AI failures, quota, kill switch) | 9.14–9.16 |
| NFR-07, release blocker "any cross-user exposure" | 9.5, 9.21, 9.22, 10.9, 10.12 |
| NFR-08 (accessibility) | 9.9, 9.24, 9.25, 10.16 |
| NFR-12 (least privilege, quotas, bounded retries) | 9.1, 9.3, 9.8, 9.15, 9.21 |
| NFR-13 (deletion ≤30 days) | 9.9–9.11, 9.13 |
| WF-9 done-when (export; deletion with a completion record without identifiers) | 9.7, 9.9, 9.11 |
| WF-10 done-when (request, approval in Settings, read in the box, expiry, trail; decline gives no access) | 10.2, 10.6, 10.7, 10.11, 10.15, 10.17, 10.23 |

**Totals:** 48 stories (journey 9: 25, journey 10: 23); 145 acceptance lines (63 in journey 9, 82 in journey 10), each tagged `/m`, `/s` or `/r`, and every story has at least one `/r` line.
