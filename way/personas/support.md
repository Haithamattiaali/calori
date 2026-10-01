# Support agent — persona lens (research cycle 2)

Written 2026-10-01 by the Support agent lens, from `way/personas/_lens-brief.md`, and fixed in round 1 the same day (see "Fix round 1" at the foot). Dispatch: **WF-10** (just-in-time diary access: the Support agent requests a Grant with reason and duration → the eater approves or declines it in Settings → read-only access inside the time box → auto-expiry, every read audited) and the **support side of WF-9** (help with export or deletion status without seeing private data; failed jobs — AI analysis failures, sync conflicts, deletion and export jobs — as de-identified metadata; account state lookup).

Read first: `way/blueprint.md` §0–§1, `way/vocabulary.md` (delta D2 — binding for roles, states, errors and places), `way/brief/frd-v1.0.md` (FR, NFR and AT ids cited as written there), `way/research/r1-*.md` with both refutations (no refuted or doubtful finding is cited; R34, R29's decree type and P11's residency claim are avoided), the care questions, `way/lessons.md`.

## 0 · How to read this file

**Ids and layers.** Story ids are `support-<WF>.<n>`, where the WF is the map workflow the story serves: 3 (sync of what was logged), 4 (Analysis), 7 (Activity), 9 (privacy and account state), 10 (Grants and the registry status). Each acceptance line carries its layer: `/m` module (a unit test of pure code), `/s` system (an API or rules test against the emulator or a running service, including negative tests), `/r` runtime — a verifier can **observe** it in the served product: the admin console in a browser, the API over HTTP, or the iOS simulator. Every story has at least one `/r` line. "Shared:" names the other personas whose surface a story's acceptance uses.

**Every story traces** to a map workflow step, a row of the map's interaction table (blueprint §1 ¶3) or an FRD FR, AT or NFR line on its "Covers:" line. Research (SR, R) is cited as evidence, never as the trace.

**Words.** Roles, states, errors and places are the ones in `way/vocabulary.md`. Words the lens needs that are not there yet are listed in §13 as proposals for a dated delta. The Support agent's console screens live in the **Jobs** and **Grants** sections (vocabulary "Places"); the eater's screens live in **Settings → Privacy** and **Settings → Export**.

**Eater-app language (one rule).** Copy quoted for an eater's app is the English string-catalogue text. On an eater whose app is in Arabic (E1 and every fixture marked Arabic in §0.3) the same catalogue key shows its Arabic text with Arabic-Indic digits; a verifier observes such a line by its catalogue key in that language. Where a story also gives the Arabic text, the Arabic text is the one on screen.

**Copy rules.** Eater-facing copy never shows internal ids or error codes. The support code and the deletion reference are shown because they are designed for the eater to read out. The case reference is shown because the eater already has it from the helpdesk's emails (A22). Console copy leads with plain words, and the typed code or id follows in a secondary monospaced column.

**Times (one rule).** Every console time reads "HH:MM your time (HH:MM UTC)". A time the agent may repeat to the eater (Grant end, request closing, export window, quota reset, event times the eater will ask about) adds "· HH:MM eater's time". Deadlines are the eater's calendar date. The app shows the eater's local time only. Offsets on the fixture dates come from the IANA time zone database (SR17): Asia/Riyadh UTC+3; Africa/Cairo UTC+3 until 2026-10-29 and UTC+2 after; Europe/Dublin UTC+1 until 2026-10-25.

**Proposed interfaces.** The FRD lists no support or Grant endpoints (§18). The paths `/v1/support/…`, `/v1/grants…` and `/v1/audit/…`, the console routes `/console/<section>`, and the Audit trail event names in §0.2 are proposals for the model phase. Error codes are only those in `way/vocabulary.md`.

### 0.1 · Statuses this lens shows (one list)

| thing | states shown | source |
|---|---|---|
| Grant | Requested · Approved · Active · Expired · Ended (by the Support agent) · Withdrawn (by the eater) · Declined · Unanswered | vocabulary D2. Approved is recorded at the eater's tap, and the Grant becomes Active in the same transaction, because the time box starts at approval (SR1: "starts immediately post-approval if not specified"). So the console and the app show Active, and Approved appears only in the Audit trail |
| Privacy job (export, deletion, Consent withdrawal) and each of its stages | Requested · Running · Completed · Failed (retried with the same id); a stage that does not apply reads "Not applicable" | vocabulary D2 |
| Consent | Not given → Given · Withdrawn (with version, time, method) | vocabulary D2 |
| Analysis | Failed (the only state shown in the Failed Analyses tab) | vocabulary D2 |
| Entry | Pending · Confirmed · Corrected · Voided · Restored | vocabulary D2 |
| Day | Provisional · Complete · Partial · Unlogged | vocabulary D2 |
| Kill switch | On · Off | vocabulary D2 (Registry) |
| Activity import result | accepted · updated · duplicate · conflict | FRD §18 `POST /v1/activity/import` |
| Request received outside the app | Open · Escalated to the platform admin · Closed | **proposal**, §13 |

### 0.2 · Audit trail events this lens writes (proposal)

`staff.sign_in_failed` · `staff.sign_in_locked` · `staff.session_ended` · `support.lookup` (method `support_code` · `email` · `deletion_reference` · `grant`) · `support.lookup_rate_limited` · `support.account_viewed` · `support.jobs_viewed` · `support.job_retried` · `support.escalated` · `support.request_logged` · `grant.requested` · `grant.approved` · `grant.active` · `grant.declined` · `grant.unanswered` · `grant.expired` · `grant.ended` · `grant.withdrawn` · `grant.read` · `grant.read_denied` · `grant.approve_denied`.

How events map to requests: one `/v1/support/*` request writes exactly one event. Opening an account panel is one request (`GET /v1/support/accounts/{id}`) that returns the panel together with its first tab and writes one `support.account_viewed`; opening any other tab (Sync, Analyses, Privacy jobs, Activity) is one request that writes one `support.jobs_viewed`.

Each event holds what, when (UTC), where (console route and API path), source (staff or account id and role), outcome (`allowed`, `found`, `not_found`, `failed`, `denied` or `error`) and the ids involved (SR8 AU-3). The eater's Grant history lists only `grant.read` events with outcome `allowed`.

### 0.3 · Synthetic fixtures (all invented; the repo is public)

One seed holds all of these at once. Times are on 2026-10-01 unless a date is given. Each Grant has its own reason, Days, areas, duration and case, so that no two stories give one Grant two histories.

| fixture | values |
|---|---|
| E1 | `acct_9c41e2`<br>• **Account:** Sign in with Apple; email shown masked `r•••@privaterelay.appleid.com`; Arabic, Arabic-Indic numerals; Asia/Riyadh; diary-day boundary 04:00; created 2026-08-03<br>• **Devices:** app 1.0.3 on iOS 26.1, last sync 10:02 UTC; app 1.0.2, last sync 2026-09-29 20:40 UTC<br>• **Consents:** "Send photos, voice and text to Google's AI" Given; "Health: read workouts" Given<br>• **Support codes:** `SB-7KQ2-94XM` issued 06:12 UTC, valid to 2026-10-02 06:12 UTC; older code `SB-3MRT-7WQD` issued 2026-09-29 06:12 UTC, expired 2026-09-30 06:12 UTC<br>• **AI use:** 3 of 10 analyses used today<br>• **Sync:** conflict on command `cmd_7a1e` (2026-09-30 19:15 UTC); command `cmd_44c0` delivered 3 times (2026-09-29 05:12 UTC)<br>• **Activity:** last import 04:30 UTC — accepted 1, updated 0, duplicate 2, conflict 0<br>• **Diary:** Entry `en_9921` on Day 2026-09-29, from Analysis `an_5512` (photo)<br>• No Privacy job, no request received outside the app<br>• **Grants:** G1 and G2 below |
| E2 | `acct_51ab07` · email sign-in · English · Africa/Cairo · deletion Privacy job (reference `DEL-26-0915-K3Q8`) Requested 2026-09-15 07:00 UTC, Running; it Completes 2026-10-14 |
| E3 | `acct_e07d13` · English · Africa/Cairo · export Privacy job `job_exp_4410` Failed (storage write timed out; 3 attempts; last 08:40 UTC) |
| E4 | `acct_77d2c0` · English · Asia/Riyadh · tracking-only after a pregnancy answer · weight observations<br>• **Grant `grant_7d01`:** by `staff_mona`; reason "A day report total looks wrong"; Days 2026-09-29 to 2026-09-30; area "Entries and day reports"; 1 hour; case `CASE-1225`; Requested 15:29, Active 15:30, Expired 16:30 UTC<br>• **Reads:** day report of Day 2026-09-29 at 15:35 UTC; refused attempts at 15:40 UTC (Day 2026-09-28), 15:41 (My Units), 15:42 (Activity), 15:43 (an id of E2) and 15:45 (writes) |
| E5 | `acct_b6f204` · English · Africa/Cairo<br>• **Analysis `an_5530`:** Failed with `AI_UNAVAILABLE` at 09:04 UTC (photo + words; `gemini-3.8-flash`; prompt v14; schema v6; 12,000 ms; 2 retries)<br>• **AI use:** 10 of 10 analyses used today; the 11th refused with `RATE_LIMITED` at 16:40 UTC |
| E6 | `acct_c2a917` · Arabic · Asia/Riyadh<br>• **Consent:** "Send photos, voice and text to Google's AI" Withdrawn 2026-09-30 18:14 UTC (v3, in the app)<br>• **Before the withdrawal:** 2 queued uploads, 3 cached private analyses, 4 raw photos and 1 audio clip, 1 prepared export<br>• **Consent-withdrawal Privacy job:** Completed 2026-09-30 18:20 UTC<br>• **Grant `grant_a1d4`:** by `staff_omar`; reason "A day report total looks wrong"; Days 2026-09-29 to 2026-09-30; area "Entries and day reports"; 1 hour; case `CASE-1250`; Requested 07:55, Active 08:00, Expired 09:00 UTC |
| E7 | `acct_a41c55` · email `karim.synthetic@example.com` · English · Africa/Cairo<br>• **Exports:** `job_exp_77` Completed 2026-09-30 15:09 UTC (requested 15:02 UTC; 2.4 MB); `job_exp_31` Completed 2026-09-20 12:00 UTC, download window over 2026-09-27 12:00 UTC; `job_exp_88` requested 10:40 UTC, Running<br>• **Request received outside the app:** an email at 09:30 UTC (support-9.15)<br>• No failed Analyses, sync conflicts or duplicates in 30 days; no Activity import in 7 days<br>• **Grant `grant_9b30`:** by `staff_mona`; reason "A day report total looks wrong"; Day 2026-09-30; area "Entries and day reports"; 1 hour; case `CASE-1240`; Requested 16:55, Active 17:00 UTC (would expire 18:00); Ended 17:25 UTC (support-10.25) |
| E8 | a finished deletion: reference `DEL-26-0820-M2V5`, requested 2026-08-20, Completed 2026-09-18; the former email `lina.synthetic@example.com` exists only in this table |
| E9 | `acct_9a07e5` · English · Africa/Cairo · deletion `DEL-26-0905-P7T2` Requested 2026-09-05 08:00 UTC · stage "Google notified" Failed 3 times · due by 2026-10-05 |
| E10 | `acct_f1e0c3` · English · Africa/Cairo · Grants by `staff_mona`:<br>• `grant_40ab`: reason "An Entry is missing or appears twice"; Days 2026-09-24 to 2026-09-26; area "Entries and day reports"; 1 hour; case `CASE-1170`; Requested 2026-09-27 10:05 UTC, Unanswered 2026-09-30 10:05 UTC<br>• `grant_52a3`: reason "A day report total looks wrong"; Days 2026-09-29 to 2026-09-30; area "Entries and day reports"; 1 hour; case `CASE-1220`; Requested 12:00, Active 12:05, Withdrawn by the eater 12:26 UTC<br>• `grant_52a7`: reason "A Unit or Recipe calculates unexpectedly"; Day 2026-09-30; area "My Units"; 1 hour; case `CASE-1221`; Requested 13:00, Active 13:04 (would expire 14:04), Ended by the Support agent 13:33 UTC<br>• `grant_6c10`: reason "Imported Activity looks wrong"; Days 2026-09-29 to 2026-09-30; area "Activity"; 1 hour; case `CASE-1236`; Requested 14:58, Active 15:02, Expired 16:02 UTC; `staff_mona` idle from 15:10 UTC, signed out 15:25 UTC, signed in again 15:28 UTC (support-10.21)<br>• `grant_6c14`: the same reason, Days, area and case as `grant_6c10`; Requested 15:55 UTC; still Requested at 16:00 UTC |
| E11 | `acct_e5c3a0` · email `samir.synthetic@example.com` · English · Africa/Cairo · email sign-in · last sign-in 2026-08-02 · has lost access to the app |
| E12 | `acct_0d4e9b` · emulator seed only · Grant `grant_8e20` by `staff_omar`, Active, used to request deletion during a Grant |
| E13 | anonymous-session eater `acct_anon_71f2`; and a local-trial install with no account |
| E14 | `acct_7b12aa` · 240 Failed Analyses in the last 30 days, none on any day above the daily quota · among them `an_7740`, Failed with `AI_UNAVAILABLE` at 09:20 UTC while the kill switch was On |
| Staff | `staff_mona` "Mona K." Support agent, console English, Europe/Dublin<br>• At 09:00 UTC she looked up E1 by support code, which opened its account panel (one `support.account_viewed` at 09:00); at 09:02 she opened its Sync tab<br>`staff_lee` "Lee T." Support agent, console English, Europe/Dublin, no Grants<br>• Five failed sign-ins at 09:30:00, 09:30:30, 09:31:00, 09:31:30 and 09:32:00 UTC<br>`staff_omar` "Omar S." Support agent, console Arabic, Asia/Riyadh<br>`staff_dina` Nutrition approver · `staff_ali` Platform admin · `staff_hana` Auditor<br>Every staff account has a password and an authenticator second factor (A19) |
| Grant G1 | `grant_31f0` for E1, by `staff_mona`<br>• reason "An Entry is missing or appears twice"; Days 2026-09-28 to 2026-09-30; areas "Entries and day reports" and "My Units"; 1 hour; case `CASE-1182`<br>• Requested 10:05; Approved and Active 10:20; Expired 11:20 UTC (12:20 Dublin, 14:20 Riyadh)<br>• **Reads:** Day 2026-09-29 at 10:24; Entry `en_9921` at 10:25; My Units at 10:27 UTC<br>• **Refused read attempts after expiry:** 11:20:01 and 11:20:05 UTC<br>• Nothing else happens under this Grant |
| Grant G2 | `grant_31f9` for E1, by `staff_mona`<br>• the same reason, Days, areas and case as G1<br>• Requested 11:30; Declined 11:42 UTC; one refused read attempt at 11:45 UTC |
| Platform | the kill switch for image analysis is On from 09:10 to 09:55 UTC |

---

## 1 · Research cycle 2 — the support agent's day in the benchmarks

Every source below was opened in this run on **2026-10-01** with a generic User-Agent. The date after a link is the source's own date where it states one. Quotes are short. Ids are `SR` (support research) so they do not clash with the FRD's [S01–S16]. Cycle-1 findings are cited by their r1 id; every one cited stands in `r1-refute-b.md`.

**SR1 · Microsoft Customer Lockbox: the customer approves a named engineer's request; unanswered requests expire; access is time-boxed and every action is logged.** `opened`
- https://learn.microsoft.com/en-us/purview/customer-lockbox-requests · page dated 2025-02-03 · "Usually, engineers fix issues using extensive telemetry and debugging tools … However, some cases require a Microsoft engineer to access your content" · the request "includes the organization's tenant name, service request number, expected start time of access (starts immediately post-approval if not specified), the estimated amount of time the engineer needs access" · "If the customer rejects the request or doesn't approve the request within 12 hours, the request expires and no access is granted" · "Microsoft engineers have the requested duration to fix the issue after which the access is automatically revoked." · "All actions performed by a Microsoft engineer are logged in the audit log."

**SR2 · Google Cloud Access Approval: explicit approval before staff access, revocable at any time, with a history of every outcome — and waiting costs support time.** `opened`
- https://cloud.google.com/assured-workloads/access-approval/docs/overview · last updated 2026-09-24 · "Access Approval ensures that Cloud Customer Care and engineering teams require your explicit approval whenever they need to access your Customer Data." · "Active access approval requests may be revoked at any time." · "a historical view of all requests that were approved, dismissed, revoked, or expired." · "The support response time increases by the duration that Customer Care spends waiting for your approval."
- https://cloud.google.com/assured-workloads/access-approval/docs/approve-requests · last updated 2026-09-24 · approvers get requests by "email" or "Pub/Sub", and "To approve a request, click Approve".

**SR3 · Google Access Transparency: each staff access is logged with resource, action, time, reason and who the accessor is.** `opened`
- https://cloud.google.com/assured-workloads/access-transparency/docs/overview · last updated 2026-09-24 · "Access Transparency log entries include details such as the affected resource and action, the time of the action, the reason for the action, and information about the accessor."

**SR4 · Microsoft Entra Privileged Identity Management (PIM): just-in-time, time-bound, approval-based access with a justification and multifactor authentication; activation lasts 1 to 24 hours; a ticket number is information-only.** `opened`
- https://learn.microsoft.com/en-us/entra/id-governance/privileged-identity-management/pim-configure · 2026-04-23 · "Provide just-in-time privileged access" · "Assign time-bound access" · "Require approval to activate privileged roles" · "Enforce multifactor authentication to activate any role" · "Use justification to understand why users activate".
- https://learn.microsoft.com/en-us/entra/id-governance/privileged-identity-management/pim-how-to-change-default-settings · 2026-04-23 · activation maximum duration: "This value can be from one to 24 hours." · ticket information "is an information-only field. Correlation with information in any ticketing system isn't enforced."

**SR5 · HIPAA's minimum-necessary standard (a benchmark only; Sips & Bytes is a general-wellness app and no claim is made that HIPAA applies): limit staff access to the categories of data each class of workforce needs.** `opened` (eCFR, the official US code)
- https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-E/section-164.502 · current · §164.502(b): "make reasonable efforts to limit protected health information to the minimum necessary to accomplish the intended purpose of the use, disclosure, or request."
- https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-E/section-164.514 · current · §164.514(d)(2)(i): identify "Those persons or classes of persons … who need access" and "For each such person or class of persons, the category or categories of protected health information to which access is needed and any conditions appropriate to such access."

**SR6 · HIPAA Security Rule safeguards (benchmark): unique user identification, automatic logoff, audit controls, person authentication, and monitoring of log-in attempts.** `opened`
- https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-C/section-164.312 · current · "(i) Unique user identification (Required). Assign a unique name and/or number for identifying and tracking user identity." · "(iii) Automatic logoff (Addressable). … terminate an electronic session after a predetermined time of inactivity." · "(b) Standard: Audit controls." · "(d) Standard: Person or entity authentication. Implement procedures to verify that a person or entity seeking access … is the one claimed."
- https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-C/section-164.308 · current · §164.308(a)(5)(ii)(C): "Log-in monitoring (Addressable). Procedures for monitoring log-in attempts and reporting discrepancies."

**SR7 · Break-glass is for emergencies, not a helpdesk. When used, it is view-only where possible, specially audited, and reviewed by someone other than the person who set it up.** `opened` — Yale University HIPAA program (undated page)
- https://hipaa.yale.edu/security/break-glass-procedure-granting-emergency-access-critical-ephi-systems · "The break–glass intended to specifically cover emergency cases and should not be used as a replacement for a helpdesk." · "Limit emergency access to the minimum data and functionality needed … This could potentially include view–only capability" · "Ensure that the individuals who create the accounts are not the ones reviewing the audit trails since this can be a source of abuse." · "Each use of an emergency account should be reviewed."
- **Consequence:** the Support agent has **no break-glass path** to a diary. A general-wellness diary has no clinical emergency that support could serve, so the only path is the eater's Grant (blueprint §2).

**SR8 · NIST SP 800-53 Rev 5.2.0 covers least privilege, logging of privileged functions, separation of duties, automatic disabling of temporary access, session termination, and the content of an audit record.** `opened` — NIST's own OSCAL catalogue (version 5.2.0, modified 2026-05-11)
- https://raw.githubusercontent.com/usnistgov/oscal-content/main/nist.gov/SP800-53/rev5/json/NIST_SP-800-53_rev5_catalog.json · AC-6: "allowing only authorized accesses for users … that are necessary to accomplish assigned organizational tasks." · AC-6(9): "Log the execution of privileged functions." · AC-5: "Define system access authorizations to support separation of duties." · AC-2(2): "Automatically [disable/remove] temporary and emergency accounts after [time period]." · AC-12: "Automatically terminate a user session after [conditions]." · AU-3: audit records establish "What type of event occurred; When the event occurred; Where the event occurred; Source of the event; Outcome of the event; and Identity of any individuals, subjects, or objects/entities associated with the event."

**SR9 · GDPR Arts. 12 and 25: answer rights requests within one month. Reasonable doubt about identity allows asking for more information, but only what is needed. By default, personal data is not accessible to people without the individual's intervention.** `opened` (gdpr-info.eu)
- https://gdpr-info.eu/art-12-gdpr/ · Art. 12(3): "without undue delay and in any event within one month of receipt of the request." · Art. 12(6): "where the controller has reasonable doubts concerning the identity … may request the provision of additional information necessary to confirm the identity".
- https://gdpr-info.eu/art-25-gdpr/ · Art. 25(2): "by default, only personal data which are necessary for each specific purpose of the processing are processed. That obligation applies to … their accessibility." · "by default personal data are not made accessible without the individual's intervention to an indefinite number of natural persons."

**SR10 · EDPB Guidelines 01/2022 on the right of access (v2.1, adopted 28 Mar 2023): authenticate through the account's own sign-in. An ID copy is disproportionate for a person who is already authenticated, and inappropriate unless necessary.** `opened`
- https://www.edpb.europa.eu/system/files/2023-04/edpb_guidelines_202201_data_subject_rights_access_v2_en.pdf · ¶72: "the authentication mechanism may include the same credentials, used by the data subject to log-in to the online service" · ¶73: "it is disproportionate to require a copy of an identity document in the event where the data subject making a request is already authenticated by the controller." · ¶74: "using a copy of an identity document as a part of the authentication process creates a risk for the security of personal data … it should be considered inappropriate, unless it is necessary, suitable, and in line with national law."

**SR11 · Saudi PDPL Implementing Regulation: act on rights requests within 30 days, verify identity, and record every request, oral ones included. Separate employees' levels of access to health data, and document who handles each stage.** `opened` (official SDAIA PDF)
- https://sdaia.gov.sa/en/SDAIA/about/Documents/ExecutiveRegulations.pdf · Art. 3(1)(a): "within a period not exceeding (30) days" · (c): "verify the identity of the requester before executing the request" · (d): "document and keep record of all received requests including oral requests." · Art. 26(3): "taking into account different level of access to data among employees or workers in a manner that guarantees the highest degree of Data Subjects privacy." · Art. 26(4): "Document all stages of Health Data Processing and provide the means to identify the person in charge for each stage." (Cycle 1: R22, R23, R24.)

**SR12 · FTC v. Ring (complaint filed 31 May 2023): unrestricted staff access was abused, with an email address used as the look-up key, and support access was later limited to the customer's consent.** `opened`
- https://www.ftc.gov/system/files/ftc_gov/pdf/complaint_ring.pdf · 2023-05-31 · Ring "did not limit access to customers' video data to employees who needed the access to perform their job function (e.g., customer support …)" · "Using her email address as a look-up mechanism, the employee identified his female co-worker's device and watched her stored video recordings without her permission." · "Ring narrowed employee access … so that customer service agents could only access videos with the customers' consent."
- https://techcrunch.com/2023/05/31/amazon-ring-ftc-settlement-lax-security/ · 2023-05-31 · "Ring … will pay $5.8 million over claims … that Ring employees and contractors had broad and unrestricted access to customers' videos for years." · "In one of the cases, the FTC said the employee's spying went on for months, undetected by Ring."

**SR13 · Consumer "support access" settings today are granted by the customer and time-limited, but often team-wide, on by default and not tied to a case.** `opened`
- https://support.greenhouse.io/hc/en-us/articles/360053616131-Grant-temporary-account-access-to-Greenhouse-Technical-Support (undated) · "At the end of the selected time, access is revoked automatically" · "This access is not limited to a specific Greenhouse employee." · "To edit the length of access after granting it … revoke access entirely, then grant new access" · "Your organization's Change Log creates a record any time a Greenhouse employee logs into a user's account."
- https://help.servicetitan.com/docs/give-servicetitan-support-temporary-access (undated) · "The allow access option is selected by default." · "all ServiceTitan Support Agents have access to your account for three days." · "Access can not be allowed or revoked for specific support cases."
- **Consequence:** a Grant is per agent, per case, scoped by Days and areas, and off by default — the opposite of each line above.

**SR14 · A nutrition benchmark: Cronometer lets the user accept or reject a professional's request to view their diary, and stop it later. Friends never see the diary.** `opened` (Cronometer help-centre API)
- https://support.cronometer.com/hc/en-us/articles/360058911291-Mobile-Sharing · updated 2026-07-12 · "Choose to accept or reject requests to view your profile using this section. Click stop next to your professional to remove their access to your account."
- https://support.cronometer.com/hc/en-us/articles/360018867471-Sharing · updated 2026-09-19 · "Sharing with a friend only allows you to share custom foods and recipes. This will not allow your friend to view your diary".

**SR15 · Health-record transparency: Estonia's patients can see who viewed their record.** `opened`
- https://e-estonia.com/enter-e-estonia-digital-health/ · 2020-03-13 · "the system's transparency means that the user can see who has viewed his or her medical data."

**SR16 · Support agents say they swivel between several systems to get customer context.** `opened` — Zendesk (vendor blog, last updated 2022-03-24, reporting the vendor's own survey)
- https://www.zendesk.com/blog/customer-service/support/customer-service-agents-need-context/ · "More than half of agents say they usually have to switch between multiple systems to solve a customer request." · "Constantly switching between different systems is not only tiresome for agents, it also leads to longer hold times and slower resolution times".

**SR17 · Time-zone offsets on the fixture dates.** `opened` — the IANA time zone database, version 2025b (`/usr/share/zoneinfo/tzdata.zi`, read through Python `zoneinfo` on 2026-10-01): Asia/Riyadh +03:00 on 2026-09-15, 2026-10-01 and 2026-10-31; Africa/Cairo +03:00 on 2026-09-15 and 2026-10-01, +02:00 on 2026-10-31; Europe/Dublin +01:00 on 2026-09-15 and 2026-10-01, +00:00 on 2026-10-31.

**Cycle-1 findings this lens rests on:**
- R2 and R3: explicit, separate, withdrawable consent; no feature behind consent.
- R4: deletion happens in the app, never through email, phone or "other support flows"; Sign in with Apple tokens are revoked.
- R7: an app cannot tell when HealthKit read access is denied.
- R8: camera and photo data is never mined.
- R21: inferred health is sensitive.
- R22: consent is documented.
- R23: destroy on request including backups, notify recipients, 30 days.
- R24: document every stage of health-data processing.
- R25: the DPO seat.
- R31: GDPR one month; erasure without undue delay.

**Assumptions** (no source settles them; the model phase tunes them in the served product):

| id | assumption | why this value |
|---|---|---|
| A1 | The Support agent works at a desk on a large screen with a helpdesk tool open beside the console | the dispatch; SR16 shows agents juggle several systems |
| A2 | Support code: 8 characters from an alphabet without 0, O, 1 or I, issued in the signed-in app, valid 24 h; case, spaces and hyphens are normalised on entry | it authenticates through the app's own sign-in (SR10) and gives no lasting look-up key (SR12) |
| A3 | Email look-up: exact match only (trimmed, case-insensitive), case reference required, at most 30 per agent per hour | SR12's email-as-look-up abuse; SR4's information-only ticket field |
| A4 | Grant durations 1 h (default), 4 h or 24 h | SR4 caps activation at 1–24 h; 1 h is the smallest useful box |
| A5 | At most 14 Days per Grant | minimum necessary (SR5); two weeks covers a sync or report question |
| A6 | A Requested Grant becomes Unanswered after 72 h | SR1 uses 12 h for an organisation's admin; an eater may not open the app for a day or a weekend |
| A7 | Console idle sign-out after 15 min, with a 2-minute warning banner at 13 min | SR6 automatic logoff; SR8 AC-12; care group 6 (warn before a timer acts) |
| A8 | A Completed export stays downloadable in the app for 7 days | the FRD gives no window |
| A9 | The Support agent may retry a Failed **export** once; deletion retries belong to the system and the platform admin | §18.2 bounded retries with the same id; vocabulary "retried with the same id"; see conflict K4 |
| A10 | The Grant panel carries a faint watermark (staff id and Grant id) | a deterrent for screenshots; no source |
| A11 | Grant state changes reach an open console within 5 s; Registry status within 60 s | no source |
| A12 | The daily AI quota resets at the eater's local midnight | FRD §16.5 gives no reset rule |
| A13 | Every support read about an account needs a look-up of that account in the same console session. Look-up methods: support code, exact email with case, deletion reference, or opening one of my own Active Grants from the Grants section | ties each read to a case (SR4, SR11 Art. 3(1)(d)) |
| A14 | After a Declined Grant, new requests are allowed, but the form shows the decline for 24 h | the eater sees every request anyway |
| A15 | A pressed button shows its working state within 100 ms; table placeholders appear within 300 ms; "Still loading — Cancel" appears at 10 s | no source |
| A16 | The reason catalogue and the Arabic strings below are proposals for the string catalogue | the map fixes Arabic labels once (§1 ¶4) |
| A17 | Eaters who eat suhoor or late meals ask "why is my meal on the wrong Day?", and this is answered from the diary-day boundary | FRD §8.1 supports a custom boundary for late-night eating; how often the question comes is unmeasured |
| A18 | 5 failed staff sign-ins within 15 min lock that staff account for 15 min | SR6 §164.308(a)(5)(ii)(C) asks for monitoring; the numbers have no source |
| A19 | Every staff account signs in with a password and a 6-digit authenticator code | SR4 "Enforce multifactor authentication"; the mechanism has no source |
| A20 | An eater who lost access recovers it through the sign-in screen's recovery path: "Forgot password" for email sign-in, Apple's own account recovery for Sign in with Apple | the FRD names Firebase Authentication but no recovery flow |
| A21 | The test build has a fault-injection switch that slows or fails the support API, so slow and error states can be observed | no source; needed to make the Givens reachable |
| A22 | The case reference is the helpdesk's ticket number that the eater already sees in support emails, so showing it in the app is not an internal id | no helpdesk integration is in the profile (§0 line 6) |
| A23 | A deletion is "due soon" with 5 days or fewer before its due date (error colour, support-9.19); requests received outside the app sort first when due within 5 days (support-9.15) | no source; a working week before the legal deadline |
| A24 | Lists page 50 rows at a time, with "Load 50 more" | no source |
| A25 | The outcome note on a request received outside the app holds 120 characters | enough for an outcome, too short for pasted content |
| A26 | The Grant bar warns at 10 and 2 minutes before the end, and offers "Ask the eater for more time" from 10 minutes before the end | no source |
| A27 | Failed-job tabs and "Duplicates ignored" look back 30 days; the Activity tab's "No Activity imported" looks back 7 days | 30 days matches the request-answer window (SR11); 7 days is a week of Activity |
| A28 | The Support agent's desk monitor is at least 1440 px wide | no source; the narrow end is proved at ~390 px (§0) |
| A29 | The console's text-size check is 200 % browser zoom | no source opened in this run for a zoom level |

---

## 2 · The support agent's day

**Who.** A staff member with the **Support agent** role (vocabulary D2; FR-081). They hold no Nutrition approver or Platform admin rights (FR-081; SR8 AC-5; SR11 Art. 26(3)). A staff account is never an eater account (vocabulary D2). Some agents work in Arabic, some in English (§0 line 5).

**Where and on what.** At a desk, on a large monitor, keyboard first, with the helpdesk open in another window (A1, SR16). Office network and steady light. Sometimes a laptop. The console must still work at about 390 px (§0: the admin console is "proved in a browser at desktop and ~390 px"). The eaters they serve are mostly in Asia/Riyadh and Africa/Cairo. The agent may be elsewhere, so console times follow the one rule in §0: the agent's time with UTC, plus the eater's time where the agent will repeat it.

**Tasks they repeat:**
1. **Find the right account** from what the eater gives them. Today this is usually an email address, which is exactly the look-up key misused in SR12. Here it is a support code the eater reads from the signed-in app (SR10, A2), or an exact email with a case reference (A3).
2. **Explain a failure from metadata.** In SR1, engineers "usually" fix issues "using extensive telemetry", and content access is the exception. Here the agent uses the Jobs section, built only from the operational fields FRD §19.2 allows (request ids, timing, status, model version, validation codes), never the diary.
3. **Answer privacy requests on time** — where is my export, when will my deletion finish, please delete me. The law sets 30 days (SR11, R23) or one month (SR9, R31), and every request is recorded, oral ones included (SR11). Deletion itself never goes through support (R4).
4. **Rarely, look inside a diary** — an Entry is missing, or a total looks wrong. This happens only through a Grant the eater approves (blueprint §2), like SR1's Lockbox and SR14's professional sharing, and never through a break-glass path (SR7).

**The moments that decide trust.**
- *The ask.* The eater sees who is asking, why, which Days and areas, and for how long, and can say no without giving a reason (SR1, SR2, SR14).
- *The end.* Access stops by itself, and the eater can stop it sooner (SR1, SR2, SR13).
- *The after.* The eater can see each thing the agent read (SR3; SR13's change log; SR15). The auditor sees the same trail, and the agent cannot touch it (SR7, SR8).
- *For the agent.* A tool that cannot over-share also protects the agent (`assumption`). In SR12, broad access let one employee use a colleague's email to find and watch her videos, and in another case "spying went on for months, undetected".

**What they use today, and what the sources say about it.**
- Swivelling between systems for context. This is the agents' own view: "tiresome for agents" (SR16).
- Support-access settings that are team-wide, on by default and not tied to a case. SR13 documents these settings, but not anyone's opinion of them. That eaters would dislike not knowing who looked is an `assumption`.
- Asking for ID copies. The EDPB calls this disproportionate for a signed-in person (SR10). That agents dislike asking for them is an `assumption`.
- Waiting on approvals. SR2 states the cost: "support response time increases" while waiting. The design keeps the Jobs section usable while a Grant is Requested.

---

## 3 · Goals

- **G1 · Help without seeing.** Resolve most cases from the Jobs section — the account panel, Privacy jobs and failed-job metadata — with no diary, Target, weight, mode or food content (FR-080, FR-081, §19.2).
- **G2 · Just in time, and only what's needed.** When a diary is truly needed, ask the eater for the fewest Days and areas for the shortest time. Read without changing anything, and give the access back (FR-081, WF-10).
- **G3 · Privacy requests on time.** Tell an eater exactly where an export, a deletion or a Consent withdrawal stands and by when. Point them to the in-app path, record every request that arrives outside the app, and escalate the ones the app cannot serve (FR-075, FR-078, NFR-13, AT-29; SR11).
- **G4 · A trail that protects both sides.** Record every look-up, view and Grant step with who, what, when, where and outcome. The eater sees Grant reads, and the auditor sees everything (FR-081; SR8 AU-3).

---

## 4 · Journey 9 — help with privacy and account state, without seeing private data (WF-9, support side)

Steps: **9A** sign in and find the account → **9B** read the account panel and Consents → **9C** export, deletion and Consent-withdrawal status → **9D** boundaries: role, isolation, Audit trail, desk and phone width, Arabic, and slow, failing or offline screens.

### 9A · Sign in and find the account

#### support-9.1 · Sign in as myself
Shared: Support agent + auditor (Audit trail).
As the Support agent, I sign in to the admin console with my own staff identity and a second factor, so that everything I do is tied to me and nobody can act as me.
Covers: FR-080, FR-081, NFR-12 · SR4 (MFA), SR6, SR8 (AC-12) · A7, A18, A19
- `/r` **Given** `staff_mona` with her password and authenticator, **When** she signs in to the admin console and enters the 6-digit code, **Then** the header reads "Mona K. · Support agent" and the navigation lists only Jobs, Grants and Settings.
- `/r` **Given** the right password but no code, **When** she submits, **Then** the console asks "Enter the 6-digit code from your authenticator app" and shows no section until a valid code is entered.
- `/r` **Given** `staff_lee` types a wrong password at 09:30:00 UTC, **When** he submits, **Then** sign-in reads "Email or password is incorrect" (the same text for an unknown email), no section loads, and the Audit trail has `staff.sign_in_failed` with outcome `failed`.
- `/r` **Given** `staff_lee`'s five failed sign-ins, the fifth at 09:32:00 UTC, **When** he tries again at 09:33 UTC, **Then** sign-in reads "Too many attempts. Try again at 10:47 your time (09:47 UTC).", and `staff_hana` sees the five `staff.sign_in_failed` events and one `staff.sign_in_locked` in the Audit trail.
- `/r` **Given** `staff_mona` is signed in and idle from 15:10 UTC (the idle period of support-10.21), **When** 15:23 UTC passes, **Then** a banner (not a dialog) reads "You'll be signed out in 2 minutes." with a "Stay signed in" button, the screen reader announces it once, and any key or click would keep the session.
- `/r` **Given** she stays idle, **When** 15:25 UTC passes, **Then** the console shows sign-in with no account data left on the page, and the Audit trail has `staff.session_ended` with reason `idle`.
- `/s` **Given** any `/v1/support/*` route, **When** it is called with no token, **Then** 401 `UNAUTHENTICATED`; **when** it is called with an eater's Firebase token, **then** 403 `FORBIDDEN`; neither returns data.

#### support-9.2 · Find an account by the eater's support code
Shared: Support agent + eater (Settings → Privacy → Support code; eater-9.29).
As the Support agent, I find an account by the support code the eater reads me from Settings → Privacy, so that I reach the right account without searching for people.
Covers: FR-080, FR-081, FR-001 · SR10, SR12 · A2
- `/r` **Given** E1's Settings → Privacy → Support code on the iOS simulator (E1's app in Arabic) shows `SB-7KQ2-94XM` with «نسخ» and «صالح حتى ٢ أكتوبر، ٠٩:١٢» (English catalogue: "Copy", "Valid until 2 Oct, 09:12"), **When** `staff_mona` enters `SB-7KQ2-94XM` in Jobs → Look up an account at 09:00 UTC, **Then** E1's account panel opens.
- `/r` **Given** E1's older code `SB-3MRT-7WQD` (expired 2026-09-30 06:12 UTC), **When** it is entered at 09:10 UTC, **Then** Jobs reads "This support code has expired. Ask the eater for a new one from Settings → Privacy → Support code." and no account opens.
- `/r` **Given** `sb 7kq2 94xm`, `SB7KQ294XM` or ` SB-7KQ2-94XM `, **When** each is entered at 09:12 UTC, **Then** the field shows `SB-7KQ2-94XM` and E1's account panel opens (case, spaces and hyphens are normalised).
- `/r` **Given** `SB-7KQ2-94X` (one character short), **When** it is looked up, **Then** Jobs reads "No account matches this code" with no suggestions or list, and `GET /v1/support/accounts?support_code=SB-7KQ2-94X` returns 404 `NOT_FOUND`.
- `/m` **Given** the support-code generator and normaliser, **When** 10,000 codes are issued and each is re-entered in lower case without hyphens, **Then** every code has 8 characters with no 0, O, 1 or I, no two valid codes are equal, and every re-entry normalises back to its code.
- `/r` **Given** E1's app is in Arabic with Arabic-Indic numerals, **When** Settings → Privacy → Support code opens, **Then** the code reads `SB-7KQ2-94XM` in Latin characters, left to right, inside the right-to-left screen, with "Copy".
- `/r` **Given** E13's anonymous session (FR-001), **When** its Settings → Privacy → Support code opens, **Then** a code shows, and the account panel reads sign-in "Anonymous session"; **given** the local-trial install, **then** it reads "Your diary is only on this iPhone, so a Support agent can't see it", with "Create account" (eater-9.29).

#### support-9.3 · Find an account by exact email, with a case reference
Shared: Support agent + auditor (Audit trail).
As the Support agent, I find an account by the exact email an eater wrote from, only with a case reference, so that I can help someone who cannot open the app without being able to browse people.
Covers: FR-081, NFR-12 · SR4, SR11 Art. 3(1)(d), SR12 · A3
- `/r` **Given** E7 (`karim.synthetic@example.com`), **When** `staff_mona` enters that email and case `CASE-1190` in Jobs → Look up an account, **Then** E7's account panel opens, and `staff_hana` sees `support.lookup` in the Audit trail with method `email`, case `CASE-1190`, outcome `found`, staff `staff_mona`.
- `/r` **Given** `  KARIM.Synthetic@Example.com ` with case `CASE-1190`, **When** it is looked up, **Then** E7's account panel opens (spaces trimmed, case ignored).
- `/r` **Given** the same email with an empty Case reference, **When** she tries to look it up, **Then** Look up stays disabled with "A case reference is required to look up by email", and the API returns 422 `VALIDATION_ERROR` naming the field `case_ref`.
- `/r` **Given** `karim.synthetic@` or `*@example.com`, **When** looked up, **Then** Jobs reads "No account matches this email", the API returns 404 `NOT_FOUND`, and `support.lookup` is recorded with outcome `not_found`.
- `/s` **Given** `staff_mona` made 30 email look-ups in the past 60 minutes, **When** she makes the 31st, **Then** 429 `RATE_LIMITED`, and the Audit trail has `support.lookup_rate_limited` with staff `staff_mona`, count 31, window 60 min and the time; the auditor's filter "Rate limited" lists it.

### 9B · Read the account panel and Consents

#### support-9.4 · Read the account panel — only what helps
As the Support agent, I read an account's sign-in method, language and numerals, time zone and diary-day boundary, devices with app version and last sync, Consents, Privacy jobs, AI use today and Grants, so that I can explain most issues without the diary.
Covers: FR-080, FR-081, FRD §8.1, §19.2 · SR5, SR11 Art. 26(3) · A17
- `/r` **Given** E1, **When** `staff_mona` opens its account panel in Jobs at 10:03 UTC, **Then** it shows:
  - created 2026-08-03;
  - sign-in "Sign in with Apple";
  - email `r•••@privaterelay.appleid.com`;
  - language Arabic; numerals Arabic-Indic;
  - time zone Asia/Riyadh; diary-day boundary 04:00;
  - devices "iPhone · app 1.0.3 · iOS 26.1 · last sync 11:02 your time (10:02 UTC) · 13:02 eater's time" and "iPhone · app 1.0.2 · last sync 2026-09-29 21:40 your time (20:40 UTC) · 23:40 eater's time";
  - Consents, each Not given, Given or Withdrawn;
  - AI analyses today 3 of 10;
  - Privacy jobs: none;
  - Grants: none Requested or Active (`grant_31f0` is requested at 10:05 UTC);
  - and the account id `acct_9c41e2` in the secondary column.
- `/r` **Given** the diary-day boundary 04:00, **When** the agent hovers over it or focuses it, **Then** the hint reads "Food eaten 00:00–03:59 counts on the previous Day". This answers a suhoor "wrong Day" question without the diary.
- `/r` **Given** the account panel is open, **When** the agent presses `/`, **Then** focus moves to Jobs → Look up an account.

#### support-9.5 · Never see what the eater did not share
As the Support agent, I never see Targets, weight or body data, safety-screen answers, tracking-only mode, activity mode or any food content in the Jobs section, so that support cannot become a way to learn someone's health.
Covers: FR-079, FR-081, §19.2, NFR-07 · SR5, SR8 (AC-6), SR12 · R21
- `/r` **Given** E4 (tracking-only after a pregnancy answer, with weight observations), **When** `staff_mona` opens its account panel, Privacy jobs and failed-job tabs in Jobs, **Then** no Target, kcal, macro, weight, height, age, goal, mode, activity mode, safety-screen item, Food, Unit, Recipe, Template or Entry content appears, and the screens have the same shape as for an account in ordinary mode.
- `/s` **Given** every `/v1/support/*` response for E4, **When** its JSON is scanned, **Then** none contains the keys `target`, `kcal`, `weight_kg`, `mode`, `safety`, `items`, `food`, `unit_label`, `transcript` or `photo_ref`; every response is built from an allow-list.
- `/m` **Given** the support projection's allow-list, **When** a new field is added to UserProfile, **Then** the projection test fails until the field is classed "support-visible" or "private"; an unclassed field is private.

#### support-9.6 · Explain a feature that is off because of a Consent
As the Support agent, I see each Consent's state, version and date, so that I can explain why photo analysis or Health import is not working without seeing any photo or Health data.
Covers: FR-076, FR-067; map interaction row "Eater → app: … separate consents … one-tap withdrawal" · R2, R3, R7, R22
- `/r` **Given** E6, **When** its account panel is open, **Then** Consents shows "Send photos, voice and text to Google's AI · Withdrawn · 2026-09-30 19:14 your time (18:14 UTC) · 21:14 eater's time · v3 · in the app" and the note "Analysis needs this Consent; the eater can give it again in Settings → Privacy."
- `/r` **Given** E1's Consent "Health: read workouts" is Given in the app, **When** the agent reads it, **Then** the line reads "Given in the app · the iPhone's own Health permission is not visible to us", never "denied" (R7; FR-067).
- `/s` **Given** a support token, **When** it calls any Consent-changing endpoint, **Then** 403 `FORBIDDEN`; a Consent changes only in the eater's app.

#### support-9.7 · See what a Consent withdrawal removed
As the Support agent, I see the Privacy job that follows a Consent withdrawal — media, queues, cached analyses and exports — so that I can tell the eater what was removed and when.
Covers: AT-29 ("consent withdrawal propagate[s] to media, queues, private cached analysis, and exports"), FR-076, FR-078 · R3, R22
- `/r` **Given** E6's withdrawal, **When** Jobs → Privacy jobs opens, **Then** the row reads "Consent withdrawal · Send photos, voice and text to Google's AI · Completed 2026-09-30 19:20 your time (18:20 UTC)", with these stages:
  - new Analyses refused — Completed 19:14 your time (18:14 UTC);
  - queued photo and voice uploads removed — Completed 19:15 your time (18:15 UTC);
  - cached private analyses deleted — Completed 19:17 your time (18:17 UTC);
  - raw photos and audio deleted — Completed 19:19 your time (18:19 UTC);
  - prepared exports deleted — Completed 19:20 your time (18:20 UTC);
  - confirmed Entries — Not applicable (they stay until deleted, FR-078).
- `/s` **Given** E6's emulator seed before the withdrawal (2 queued uploads, 3 cached private analyses, 4 raw photos, 1 audio clip, 1 prepared export), **When** the withdrawal runs, **Then** storage and queues hold none of them for E6, each stage time is written in UTC, and a later `POST /v1/analyses` from E6 returns `CONSENT_REQUIRED`.
- `/r` **Given** the row, **When** the agent opens "What to tell the eater", **Then** the Arabic text comes first, then the English:
  - Arabic (proposal, A16): «منذ ٢١:١٤ يوم ٣٠ سبتمبر لم تعد صورك وتسجيلاتك تُرسل إلى الذكاء الاصطناعي من Google. حذفنا الصور والتسجيلات والتحليلات المخزنة وملفات التصدير المُعدّة. إدخالاتك المؤكدة لم تتغير.»
  - English: "Since 21:14 on 30 Sep, your photos and recordings are no longer sent to Google's AI. We deleted the photos, recordings, stored analyses and prepared exports. Your confirmed Entries are unchanged."

### 9C · Export and deletion status

#### support-9.8 · Tell an eater where their export is
As the Support agent, I see an export's state, timing and section list — never the file — so that I can tell the eater exactly where their export is.
Covers: FR-075, FR-078, FRD §18 `POST /v1/privacy/export-or-delete`, WF-9 done-when ("export downloads entries, units, recipes, targets and consents") · A8
- `/r` **Given** E7's `job_exp_77`, **When** Jobs → Privacy jobs opens, **Then** the row reads "Export · Completed · requested 2026-09-30 16:02 your time (15:02 UTC) · completed 16:09 your time (15:09 UTC) · Entries, Units, Recipes, Targets, Consents, Reports · 2.4 MB · download in the app until 2026-10-07 16:09 your time (15:09 UTC) · 18:09 eater's time", with the line "Open Settings → Export to download it." ready to copy.
- `/r` **Given** E7's `job_exp_88` (requested 10:40 UTC, Running), **When** the row shows, **Then** it reads "Export · Running · requested 11:40 your time (10:40 UTC) · 13:40 eater's time", with the line "Your export is being prepared. It will appear in Settings → Export when it is ready." No time is promised.
- `/r` **Given** E7's `job_exp_31` (Completed 2026-09-20; download window over 2026-09-27), **When** the row shows, **Then** it reads "Export · Completed 2026-09-20 · download no longer available since 2026-09-27", with the line "Ask for a new export in Settings → Export."
- `/s` **Given** `GET /v1/support/accounts/acct_a41c55/privacy-jobs`, **When** it is read, **Then** no export item carries a URL, signed link, file id or per-Day counts, and `GET /v1/support/privacy-jobs/job_exp_77/file` returns 403 `FORBIDDEN`.
- `/r` **Given** E1 (no Privacy job), **When** Privacy jobs opens, **Then** it reads "No export, deletion or Consent withdrawal", with the in-app paths Settings → Export and Settings → Privacy → Delete account.

#### support-9.9 · Retry a Failed export once
Shared: Support agent + eater (Settings → Export; eater-9.14) + auditor (Audit trail) + platform admin (escalation).
As the Support agent, I see why an export Failed and retry it once with the same id, so that the eater gets their export without starting over or sending me anything.
Covers: FR-075, FR-078, FRD §18.2 ("bounded retries with the same command ID"), NFR-12 · A9, A21 · conflict K4
- `/r` **Given** E3's `job_exp_4410`, **When** Jobs → Privacy jobs opens, **Then** the row reads "Export · Failed · storage write timed out · 3 attempts · last 09:40 your time (08:40 UTC)", with a Retry button.
- `/r` **Given** the retry API returns 503 (fault injection), **When** the agent presses Retry, **Then** the row reads "Couldn't retry this export. Nothing changed. Try again." above the unchanged "Export · Failed …" line, and Retry stays available.
- `/r` **Given** the API answers normally, **When** the agent presses Retry, **Then** the row reads "Export · Requested · retried by Mona K. · 0 of 3 attempts" under the same id `job_exp_4410`, the Retry button is gone, and the row turns Running when the job starts. `staff_hana` sees `support.job_retried` in the Audit trail.
- `/r` **Given** the retried job is Running, **When** E3 opens Settings → Export on the simulator, **Then** it reads "Running", with no new request (eater-9.14).
- `/s` **Given** `POST /v1/support/privacy-jobs/job_exp_4410/retry` arrives twice, **When** the second is processed, **Then** it returns 409 `VALIDATION_ERROR` with the current state, and no second job exists.
- `/r` **Given** the retried job fails again (fault injection), **When** the row updates, **Then** it reads "Export · Failed · retried once by Mona K.", with no Retry button and only "Escalate to the platform admin" (support-9.11).
- `/r` **Given** any deletion row, **When** the agent opens it, **Then** it has no Retry control.

#### support-9.10 · Tell an eater when their deletion completes
As the Support agent, I see a deletion's stages and its due-by date, so that I can tell the eater exactly what has happened and when the rest will.
Covers: FR-078, NFR-13, AT-29, FRD §17.2; map interaction row "Eater → API: … delete account … ≤30 days, processors told, Sign in with Apple revoked, completion record" · R4, R23, R31 · SR9, SR11
- `/r` **Given** E2's deletion `DEL-26-0915-K3Q8`, **When** Jobs → Privacy jobs opens on 2026-10-01, **Then** the row reads "Deletion · Running · due by 2026-10-15", with these stages: signed out and disabled — Completed 2026-09-15; private records deleted — Completed 2026-09-15; media and cached analyses deleted — Completed 2026-09-16; queued commands and exports deleted — Completed 2026-09-16; Google notified — Completed 2026-09-16; Sign in with Apple token revoked — Not applicable (email sign-in); backups expire — Running, by 2026-10-14; completion record — Requested.
- `/m` **Given** a deletion Requested at 2026-09-15T07:00Z for an eater in Africa/Cairo, **When** the due-by date is computed, **Then** it is 2026-10-15 — 30 days, the stricter of SDAIA's 30 days and GDPR's one month (NFR-13) — stored in UTC and shown as the eater's calendar date.
- `/r` **Given** the stage list, **When** a screen reader reads it, **Then** each stage says its state in words (Completed, Running, Requested, Failed, Not applicable), never by colour or icon alone.

#### support-9.11 · Escalate a Failed or late deletion, and see it land
Shared: Support agent + platform admin (Jobs → "Escalated") + auditor (Audit trail).
As the Support agent, I see when a deletion stage Failed or its due-by date is near, and I escalate it to the platform admin, who sees it in their Jobs section, so that no deletion quietly misses its legal window.
Covers: NFR-13, AT-29, FR-080 ("failed jobs") · R23, R24 · A21, A23 · conflict K4
- `/r` **Given** E9's deletion `DEL-26-0905-P7T2`, **When** Jobs → Privacy jobs opens on 2026-10-01, **Then** the row reads "Deletion · Failed · stage 'Google notified' failed 3 times · due by 2026-10-05 · 4 days left". It offers only "Escalate to the platform admin" — no retry, cancel or "mark as completed".
- `/r` **Given** the escalation API returns 503 at 11:03 UTC (fault injection), **When** the agent presses Escalate, **Then** the row reads "Couldn't escalate. Nothing was sent to the platform admin. Try again.", and Escalate stays available.
- `/r` **Given** the API answers normally and she escalates with case `CASE-1201` at 11:05 UTC, **When** it is sent, **Then** the row keeps its state and reads "Deletion · Failed · escalated to the platform admin at 12:05 your time (11:05 UTC) by Mona K.", and `staff_hana` sees `support.escalated` in the Audit trail.
- `/r` **Given** that escalation, **When** `staff_ali` opens Jobs with the filter "Escalated", **Then** the row shows `DEL-26-0905-P7T2`, the Failed stage, due by 2026-10-05, "escalated by Mona K." and case `CASE-1201`, sorted by due date.
- `/r` **Given** `staff_ali` retries the stage with the same id at 14:10 UTC and it Completes at 14:12 UTC, **When** `staff_mona` reopens E9's Privacy jobs, **Then** the row reads "Deletion · Running · escalation resolved by the platform admin at 15:12 your time (14:12 UTC)".
- `/s` **Given** a support token, **When** it calls any endpoint that cancels, pauses, speeds up or completes a deletion, **Then** 403 `FORBIDDEN`.

#### support-9.12 · Confirm a finished deletion from its reference alone
As the Support agent, I confirm a finished deletion from the deletion reference the eater kept, so that I can reassure them while nothing that identifies them remains.
Covers: WF-9 done-when ("leaves a completion record without identifiers"), FR-078, NFR-13 · R4, R23
- `/r` **Given** E8's completion record, **When** the agent enters `DEL-26-0820-M2V5` in Jobs → Look up an account on 2026-10-01, **Then** it reads "Deletion · Completed 2026-09-18 · requested 2026-08-20 · every stage Completed" and nothing else — no email, account id, device or Consent.
- `/r` **Given** `lina.synthetic@example.com` with case `CASE-1210`, **When** it is looked up, **Then** Jobs reads "No account matches this email", exactly as for an email never registered.
- `/s` **Given** the stored completion record for `DEL-26-0820-M2V5`, **When** it is scanned, **Then** it holds no email, account id, name, device id or IP address.

#### support-9.13 · Point an eater to export or delete in the app
As the Support agent, I give the eater the in-app path to export or delete in their own language, and never do it for them, so that the eater stays in control and nobody asks them for ID.
Covers: FR-078, FRD §23.3 (export and deletion never paywalled); map interaction row "Eater → API: export; delete account — in-app, no email or phone" · R4 · SR10
- `/r` **Given** E6's account panel (app language Arabic), **When** the agent opens Privacy help, **Then** the console offers, Arabic first, «الإعدادات ← الخصوصية ← حذف الحساب» and «الإعدادات ← التصدير» (proposals, A16), then the English "Settings → Privacy → Delete account" and "Settings → Export", each with Copy.
- `/r` **Given** any screen in Jobs or Grants, **When** the agent looks for a way to export, delete or change an account, **Then** none exists, and `POST /v1/privacy/export-or-delete` with a staff token returns 403 `FORBIDDEN`.
- `/r` **Given** Privacy help, **When** it is read, **Then** it says "Don't ask for ID documents or photos: the eater proves who they are by signing in to the app." and offers no field to collect identity data (SR10 ¶73–74).

#### support-9.14 · Answer a privacy request from an eater who cannot sign in
Shared: Support agent + platform admin (Jobs → "Escalated").
As the Support agent, I answer a privacy request from an eater who has lost access — with the recovery path, a recorded request with its due date, and an escalation if recovery fails — so that the request is answered within 30 days without support exporting or deleting anything.
Covers: map interaction row "Eater → API: export; delete account — ≤30 days"; FR-078; NFR-13 · R4, R23, R31 · SR9 Art. 12(6), SR10 ¶74, SR11 Art. 3(1)(a)(c)(d) · A20, A21 · conflict K9
- `/r` **Given** E11 (`samir.synthetic@example.com`) with case `CASE-1230`, **When** `staff_mona` looks it up, **Then** the account panel shows sign-in "Email" and last sign-in 2026-08-02, and Privacy help offers: "Use 'Forgot password' on the app's sign-in screen to get back in, then go to Settings → Privacy → Delete account."
- `/r` **Given** she records the request in Jobs → Requests received outside the app (type Deletion, channel Email, case `CASE-1230`, account E11, received 10:50 your time (09:50 UTC)), **When** she saves it, **Then** the row reads "Open · due by 2026-10-31".
- `/r` **Given** the escalation API returns 503 (fault injection), **When** she presses "Escalate to the platform admin" on that row, **Then** the row reads "Couldn't escalate. Nothing was sent to the platform admin. Try again." and stays Open.
- `/r` **Given** the eater replies that recovery failed, and the API answers normally, **When** she escalates the row with the note "cannot sign in", **Then** the row reads "Escalated to the platform admin · due by 2026-10-31", the platform admin's Jobs → "Escalated" lists it sorted by due date, and support still has no export or delete control.
- `/r` **Given** this row's Privacy help, **When** it is read, **Then** it says "Don't ask for ID documents or photos" and has no field to collect identity data.

#### support-9.15 · Record a privacy request that arrived outside the app
Shared: Support agent + auditor (Audit trail).
As the Support agent, I record each privacy request that reaches support by email, chat or phone — type, channel, time, case, account and outcome, with no content — so that every request is documented and answered within 30 days.
Covers: map interaction row "Eater → API: export; delete account → Privacy job (≤30 days; R4, R23, R31)" · SR11 Art. 3(1)(a)(d), SR9 Art. 12(3) · A21, A23, A25
- `/r` **Given** E7's email asking for a copy of data, received 09:30 UTC, **When** the agent records it in Jobs → Requests received outside the app (type "Access or export", channel Email, case `CASE-1215`, account E7), **Then** the row reads "Open · received 10:30 your time (09:30 UTC) · due by 2026-10-31 · pointed to the in-app export", and `staff_hana` sees `support.request_logged` in the Audit trail.
- `/r` **Given** E7 then exported in the app (`job_exp_88`), **When** the agent links that job, **Then** the row reads "Open · linked to export job_exp_88 · Running", and it turns "Closed · completed by the in-app export on 2026-10-01" when the job Completes.
- `/r` **Given** the form, **When** the agent tries to paste the email body, **Then** the only free text is a 120-character outcome note marked "Don't paste food, health or message content".
- `/r` **Given** 3 Open rows due within 5 days, **When** the view opens, **Then** they sort first with "Due in n days" in words.
- `/r` **Given** the view is filtered to E1 (no request recorded), **When** it opens, **Then** it reads "No requests recorded for this account. Record one when a privacy request reaches you by email, chat or phone." with "Record a request".
- `/r` **Given** the save API returns 503 (fault injection), **When** the agent saves a filled form, **Then** the form keeps every field and reads "Couldn't save this request. Nothing was recorded. Try again."
- `/r` **Given** a half-filled form (type and channel chosen), **When** the agent switches to Grants and comes back in the same session, **Then** the draft is restored; signing out clears it.

### 9D · Boundaries, trail, desk, language, and failure states

#### support-9.16 · Stay inside the Support agent role
Shared: Support agent + nutrition approver (`staff_dina`'s session) + platform admin (`staff_ali`'s session).
As the Support agent, I can use only the Jobs, Grants and Settings sections, and only the Support agent role can request a Grant, so that support, nutrition approval and platform administration stay separate.
Covers: FR-081, NFR-07, NFR-12 · SR8 (AC-5, AC-6), SR11 Art. 26(3)
- `/r` **Given** `staff_mona`, **When** she opens `/console/review`, `/console/foods`, `/console/recipes`, `/console/aliases`, `/console/policy`, `/console/registry`, `/console/roles`, `/console/audit-trail` or `/console/metrics` by URL, **Then** each reads "You don't have access to this area", and the API returns 403 `FORBIDDEN`.
- `/r` **Given** `staff_dina` (Nutrition approver), **When** she opens `/console/jobs` or `/console/grants`, **Then** each reads "You don't have access to this area"; **given** `staff_dina` or `staff_ali` (Platform admin), **when** either calls `POST /v1/grants`, **then** 403 `FORBIDDEN`.
- `/s` **Given** every `/v1/support/*`, `/v1/grants*` and `/v1/audit/*` route, **When** the role-matrix test calls each with every staff role and an eater token, **Then** only the intended role gets a 2xx.

#### support-9.17 · Never cross from one account to another
As the Support agent, I reach only accounts I looked up in this session, by their own ids, so that a typo or a tampered id can never show another eater's metadata.
Covers: NFR-07, FRD release blockers ("any cross-user exposure") · A13
- `/r` **Given** E1's account panel is open, **When** the agent edits the URL to `/console/jobs/accounts/acct_51ab07` without looking that account up, **Then** Jobs reads "Look up this account first", and the API returns 404 `NOT_FOUND`.
- `/s` **Given** Analysis `an_5530` belongs to E5, **When** it is requested under E1's account, **Then** 404 `NOT_FOUND`, never the job.

#### support-9.18 · Everything I do leaves a trail
Shared: Support agent + auditor (Audit trail).
As the Support agent, I know each look-up, view, retry, escalation and Grant step is recorded with who, what, when, where and outcome, so that honest work is provable and misuse is visible.
Covers: FR-081 · SR3, SR8 (AU-3, AC-6(9)), SR11 Art. 26(4) · R24
- `/r` **Given** `staff_mona`'s look-up of E1 at 09:00, which opened its account panel once, and its Sync tab at 09:02 UTC (§0.3; `support.account_viewed` is written each time an account panel is shown), **When** `staff_hana` filters the Audit trail by `staff_mona`, account `acct_9c41e2` and 09:00–09:05 UTC, **Then** exactly three events show: `support.lookup` (method `support_code`), `support.account_viewed` and `support.jobs_viewed`. Each has the time (UTC), where (console route and API path), staff id and role, account id, case reference (none for a support-code look-up) and outcome.
- `/s` **Given** any `/v1/support/*` request, **When** it completes with 2xx or 4xx, **Then** exactly one Audit trail event is written; if the write fails, the request fails and returns no data.
- `/r` **Given** `staff_mona`, **When** she opens `/console/audit-trail`, **Then** the screen reads "You don't have access to this area", and the API returns 403 `FORBIDDEN` — a Support agent can neither read nor edit the trail.

#### support-9.19 · Work fast at a desk, and still at phone width
As the Support agent, I work keyboard first on a large screen with the account panel, jobs and the Grant panel side by side, and the console still works at phone width, so that I close cases quickly and can check one away from my desk.
Covers: FR-080, NFR-08; blueprint §0 ("admin console proved in a browser at desktop and ~390 px") · care groups 2, 3, 6 · A23, A28, A29
- `/r` **Given** a 1440×900 window and then a 1920×1080 window, **When** E1's account panel is open, **Then** at both sizes the account panel, the Privacy jobs or failed-job tabs, and the Grant panel show as three columns without horizontal scroll, with ids in a monospaced face and tabular numbers.
- `/r` **Given** a 390 px wide window, **When** the same account is open, **Then**:
  - the columns stack as account panel → Grant panel → jobs;
  - every action can be reached;
  - there is no horizontal page scroll;
  - the Grant bar stays pinned at the top while a Grant is Active.
- `/r` **Given** keyboard only, **When** the agent tabs from Look up an account through the account panel and the Sync tab to "Request a Grant", **Then** every control shows a visible focus ring, and every action works without a mouse.
- `/r` **Given** 200 % browser zoom, in light and in dark appearance, **When** the account panel is open, **Then** nothing clips or overlaps, and all text meets 4.5:1 contrast.
- `/r` **Given** Jobs and Grants with and without an Active Grant, **When** the computed colours of every element are listed, **Then** the Grant accent colour (token `--grant-active`) appears only on the Grant panel and the Grant bar, and only while a Grant is Active.
- `/r` **Given** Look up, tab switches and Retry each pressed 5 times, **When** the screen is recorded, **Then** no element animates: the computed transition and animation durations are 0 s on these controls.
- `/r` **Given** rows in the states Completed, Running, Requested, Given and Withdrawn, **When** they render, **Then** none uses the error colour. The error colour appears only on Failed rows and on a deletion with 5 days or fewer before its due date.

#### support-9.20 · Use the console in Arabic, and reply in the eater's language
As the Support agent, I can use the console in Arabic and always see the eater's app language, so that I reply in the eater's language and Arabic-speaking agents work in theirs.
Covers: FR-080, NFR-08; FRD §1.2 and §14.1 (English and Arabic, right to left) · care group 6 · A16
- `/r` **Given** `staff_omar` set Settings → language to Arabic, and his `grant_a1d4` for E6 is Active at 08:30 UTC, **When** he opens E6's account panel, **Then**:
  - the layout mirrors (navigation on the right, back arrow pointing right);
  - ids, codes and times stay left to right;
  - the Grant bar's countdown for `grant_a1d4` fills from the right.
- `/r` **Given** an eater whose language is Arabic (E1 or E6), **When** any "What to tell the eater" text opens, **Then** the Arabic text comes first and the English second, whatever the console language.

#### support-9.21 · Jobs when slow, failing or offline
As the Support agent, I get a designed answer when Look up, the account panel or Privacy jobs is slow, fails or loses the network, so that I never mistake a blank or stale screen for the truth.
Covers: FR-080, NFR-05 · care group 4 · A15, A21
- `/r` **Given** the look-up API is slowed to 3 s (fault injection), **When** the agent presses Look up, **Then** the button reads "Looking up…" within 100 ms, the result appears at 3 s, and if it takes longer than 10 s the field shows "Still looking — Cancel".
- `/r` **Given** the account panel API returns 503, **When** it loads, **Then** the panel reads "Couldn't load this account. Try again." with the request id `req_…`, and Look up stays usable.
- `/r` **Given** the Privacy jobs API returns 503, **When** that tab loads, **Then** it reads "Couldn't load Privacy jobs. Try again.", and the rest of the account panel stays usable.
- `/r` **Given** `staff_lee` (who holds no Grant) has E1's account panel loaded and his browser goes offline at 10:42 UTC, **When** he looks at it, **Then** a quiet strip reads "Offline — showing data from 11:42 your time (10:42 UTC)", the data stays, and Look up, Retry and Escalate are disabled with "Connect to look up or change anything"; when the connection returns, the strip disappears and the data refreshes.

---

## 5 · Journey 3 — sync of what was logged, as metadata (WF-3, support side)

Step: an eater asks why an Entry did not appear or appeared twice. The answer comes from the Sync tab in Jobs (WF-3: "offline logs sync once").

#### support-3.1 · See sync conflicts without the Entries
As the Support agent, I see sync conflicts — command id, operation, device, time and `STALE_REVISION` — but not the Entries, so that I can tell the eater which device to open to choose a version.
Covers: WF-3 ("offline logs sync once"), FR-041, FR-043, FR-080, §8.3, §18.2, AT-31, NFR-06
- `/r` **Given** E1's command `cmd_7a1e` (operation "correct", app 1.0.2) was refused with 409 `STALE_REVISION`, **When** the agent opens Jobs → Sync, **Then** the row reads "Pending on the device · STALE_REVISION · correct · iPhone app 1.0.2 · 2026-09-30 20:15 your time (19:15 UTC) · 22:15 eater's time", with "Waiting for the eater to choose a version on the iPhone with app 1.0.2". It shows no Food, quantity, kcal or Entry label.
- `/r` **Given** that row, **When** the agent opens "What to tell the eater", **Then** the Arabic text comes first, then the English:
  - Arabic (proposal, A16): «في جهاز iPhone الذي يعمل بالإصدار الأقدم من التطبيق نسختان من إدخال واحد. افتح Sips & Bytes على ذلك الجهاز واختر النسخة التي تريد الاحتفاظ بها. لم يُضَف شيء مرتين.»
  - English: "Your iPhone with the older app version has two versions of one Entry. Open Sips & Bytes on that iPhone and choose the one to keep. Nothing was added twice." (AT-31)
- `/s` **Given** a support token, **When** it calls any reconcile, correct, void, restore or move endpoint, **Then** 403 `FORBIDDEN`.

#### support-3.2 · Answer "did it log twice?" from the duplicate record
As the Support agent, I see how many duplicate deliveries of a command were ignored, so that I can reassure an eater that a retry did not add food twice.
Covers: WF-3, FR-043, FR-080, AT-10, AT-21 · A27
- `/r` **Given** E1's command `cmd_44c0`, delivered 3 times (the AT-10 fixture), **When** the agent opens Jobs → Sync → Duplicates ignored, **Then** the row reads "cmd_44c0 · Confirmed once · 2 duplicate deliveries ignored · 2026-09-29 06:12 your time (05:12 UTC) · 08:12 eater's time", with no item, Unit or kcal.
- `/r` **Given** E7 (no duplicates in 30 days), **When** Duplicates ignored opens, **Then** it reads "No duplicate deliveries in the last 30 days."

---

## 6 · Journey 4 — Analysis failures, as metadata (WF-4, support side)

Step: an eater says a photo "did nothing". The answer comes from the Failed Analyses tab in Jobs (WF-4: "photo / label / scale / voice / photo + words → draft").

#### support-4.1 · See failed Analyses as metadata
As the Support agent, I see an account's Failed Analyses as metadata — time, input kind, error code, model, prompt and schema versions, latency, retries — so that I can explain what failed without seeing the photo, audio or text.
Covers: WF-4, FR-080, FRD §7.2, §16.4, §18.2, §19.2, AT-32, NFR-03
- `/r` **Given** E5's Analysis `an_5530`, **When** the agent opens Jobs → Failed Analyses, **Then** the row reads "Failed · AI service unavailable (AI_UNAVAILABLE) · photo + words · 10:04 your time (09:04 UTC) · 12:04 eater's time · gemini-3.8-flash · prompt v14 · schema v6 · 12,000 ms · 2 retries".
- `/s` **Given** `GET /v1/support/accounts/acct_b6f204/failed-jobs?kind=analysis`, **When** it is read, **Then** no item carries image bytes, a storage path, a transcript, prompt text, `items[]`, `candidate_food_ids` or `assumptions[]`.
- `/r` **Given** that row, **When** the agent opens "What to tell the eater", **Then** it reads "The photo analysis at 12:04 failed, and nothing was logged. Recent Units and manual logging still work, and you can try the photo again." (AT-32)

#### support-4.2 · Explain the daily AI limit
As the Support agent, I see the eater's AI use against the daily quota and when it resets, so that I can explain `RATE_LIMITED` without changing anything myself.
Covers: WF-4, FRD §16.5, §18.2; blueprint §6 (Registry: "per-user daily AI quotas"); NFR-12 · A12
- `/r` **Given** E5, **When** its account panel is open, **Then** it reads "AI analyses today 10 of 10 · resets 22:00 your time (21:00 UTC) · 00:00 eater's time on 2026-10-02", and Failed Analyses shows "Failed · daily AI limit reached (RATE_LIMITED) · 17:40 your time (16:40 UTC) · 19:40 eater's time".
- `/r` **Given** the Support agent role, **When** the agent looks for a way to raise the quota, **Then** there is none, and the panel says "Quotas are set by the platform admin in Registry."

#### support-4.3 · Failed-job tabs when empty, slow or failing
As the Support agent, I get a designed answer when a failed-job tab is empty, slow or broken, so that I never mistake a blank screen for "nothing wrong".
Covers: FR-080 ("failed jobs") · care group 4 · A15, A21, A24, A27
- `/r` **Given** E7 (no failures in 30 days), **When** Jobs → Failed Analyses opens, **Then** it reads "No failed Analyses in the last 30 days. Next, check Privacy jobs and Consents."; **when** Jobs → Sync opens, **then** its conflict list reads "No sync conflicts in the last 30 days. Next, check Privacy jobs and Consents." The Activity tab's own empty text is in support-7.1.
- `/r` **Given** the failed-jobs API is slowed to 3 s (fault injection), **When** the Failed Analyses, Sync or Activity tab opens, **Then** table-shaped placeholders appear within 300 ms and the rows replace them at 3 s; if loading takes longer than 10 s, the tab reads "Still loading — Cancel".
- `/r` **Given** the failed-jobs API returns 503, **When** any of those tabs loads, **Then** it reads "Couldn't load failed jobs. Try again." with the request id `req_…`, and the rest of the account panel stays usable.
- `/r` **Given** E14 (240 Failed Analyses in 30 days), **When** Failed Analyses opens, **Then** 50 rows show with the total "240" and "Load 50 more".

---

## 7 · Journey 7 — Activity import results, as metadata (WF-7, support side)

Step: an eater says a workout is missing or counted once instead of twice. The answer comes from the Activity tab in Jobs (WF-7: "HealthKit and manual exercise → dedupe").

#### support-7.1 · See Activity import results
As the Support agent, I see Activity import results — accepted, updated, duplicate and conflict counts and the last import time — so that I can explain a missing or merged workout without seeing workouts.
Covers: WF-7, FR-063, FR-064, FR-067, FR-080, FRD §18 `POST /v1/activity/import`, AT-22 · R7 · A27
- `/r` **Given** E1's last import, **When** the agent opens Jobs → Activity, **Then** it reads "Last import 05:30 your time (04:30 UTC) · 07:30 eater's time · accepted 1 · updated 0 · duplicate 2 · conflict 0" with "Duplicates were merged into one contribution". It shows no activity type, energy, duration or source app.
- `/r` **Given** E7 (no import in 7 days), **When** Jobs → Activity opens, **Then** it reads "No Activity imported in 7 days. Missing data is unknown, not proof of no exercise." (FR-067)

---

## 8 · Journey 10 — just-in-time diary access, and the Registry status (WF-10, support side)

Steps: **10A** decide that metadata is not enough → **10B** request a Grant → **10C** the eater decides in Settings → Privacy → Grants → **10D** read inside the box → **10E** the box ends → **10F** the Grants list, the end-to-end proof, the Registry status, and failures.

### 10A · Decide

#### support-10.1 · Ask for diary access only from a case
Shared: Support agent + auditor (Audit trail).
As the Support agent, I start a Grant request only from an account I looked up, after metadata could not answer, so that diary access is the exception, not the habit.
Covers: WF-10 ("support requests a Grant"), FR-081 · SR1, SR5, SR7
- `/r` **Given** E1's account panel at 10:03 UTC (no Grant yet), **When** the agent looks for the diary, **Then** the only control is "Request a Grant", and `/console/grants/diary?account=acct_9c41e2` reads "No Active Grant for this account".
- `/r` **Given** the agent presses "Request a Grant", **When** the form opens in Grants, **Then** account, eater language and eater time zone are filled in, and reason, Days and areas are empty.
- `/s` **Given** a Grant id that does not exist, **When** a read under `/v1/grants/{id}/…` is called with it, **Then** 403 `GRANT_REQUIRED`, and a `grant.read_denied` event is in the Audit trail.

### 10B · Request

#### support-10.2 · Request a Grant with reason, scope, duration and case
Shared: Support agent + auditor (Audit trail).
As the Support agent, I request a Grant naming a reason from a fixed list, the Days and areas I need, a duration and my case reference, so that the eater can decide knowing exactly who wants what, why and for how long.
Covers: WF-10; map interaction row "Support → Eater: request just-in-time diary access — the eater sees who asks, why and for how long"; FR-081 · SR1, SR4, SR5 · A4, A5, A6, A16
- `/r` **Given** `staff_mona` chooses the reason "An Entry is missing or appears twice", Days 2026-09-28 to 2026-09-30, the areas "Entries and day reports" and "My Units", a duration of 1 hour and case `CASE-1182`, **When** she presses "Send request" at 10:05 UTC, **Then** the Grant panel reads "Requested · waiting for the eater · the request closes 2026-10-04 11:05 your time (10:05 UTC) · 13:05 eater's time", and `POST /v1/grants` returned 201 with state Requested.
- `/r` **Given** the form, **When** it opens, **Then** duration offers 1 hour (selected), 4 hours and 24 hours, and nothing longer. Areas offer "Entries and day reports", "My Units" (Unit, Composite and Recipe versions), "Templates" and "Activity", none selected.
- `/m` **Given** the Grant state machine, **When** events arrive in any order, **Then** the only paths are Requested → Approved → Active → Expired | Ended | Withdrawn, and Requested → Declined | Unanswered. Active is reachable only through Approved.
- `/r` **Given** the request was sent, **When** `staff_hana` opens the Audit trail, **Then** `grant.requested` shows staff, account, reason, Days, areas, duration and case.

Reason catalogue (proposal for the string catalogue, A16):

| code | English | Arabic |
|---|---|---|
| `sync_missing_entry` | An Entry is missing or appears twice | إدخال مفقود أو ظاهر مرتين |
| `report_mismatch` | A day report total looks wrong | إجمالي تقرير اليوم يبدو غير صحيح |
| `unit_calculation` | A Unit or Recipe calculates unexpectedly | وحدة أو وصفة تُحسب بشكل غير متوقع |
| `activity_import` | Imported Activity looks wrong | النشاط المستورد يبدو غير صحيح |
| `other` | (the agent's one-sentence note, shown as written) | (كما كتبها موظف الدعم) |

#### support-10.3 · Be told what to fix when a request is invalid, and keep my draft
As the Support agent, I am told what to fix when a request is incomplete or too broad, and my half-filled request survives a detour, so that only small, valid requests reach the eater and none is lost.
Covers: WF-10, FR-081 · SR5 · care group 4 · A4, A5
- `/r` **Given** no reason is chosen, **When** she presses Send request, **Then** the reason field reads "Choose why you need access", and nothing is sent (422 `VALIDATION_ERROR`, field `reason`).
- `/r` **Given** the reason "other" with an empty note, **When** she sends it, **Then** the note field reads "Describe the reason in one sentence the eater will read".
- `/r` **Given** Days 2026-09-01 to 2026-09-30, **When** she sends it, **Then** the Days field reads "Ask for 14 Days or fewer" (422 `VALIDATION_ERROR`, field `days`).
- `/r` **Given** a Day after the eater's current Day, such as 2026-10-03, **When** she sends it, **Then** the Days field reads "Choose Days up to today in the eater's time zone (Asia/Riyadh)".
- `/s` **Given** a request with a 48 h duration sent straight to the API, **When** it is received, **Then** 422 `VALIDATION_ERROR`, field `duration`, listing the allowed values 1 h, 4 h and 24 h.
- `/r` **Given** a half-filled form (reason and Days chosen), **When** the agent switches to Jobs and comes back in the same session, **Then** the draft is restored; signing out clears it.

#### support-10.4 · Never stack requests on one eater
As the Support agent, I cannot open a second request while one is waiting for the same eater, so that the eater is never asked twice at once.
Covers: WF-10, FR-081 (the conflict path)
- `/r` **Given** `grant_31f0` is Requested for E1 (10:05–10:20 UTC), **When** `staff_lee` presses "Request a Grant" on E1 at 10:10 UTC, **Then** the Grant panel reads "Mona K. has a request waiting for this eater. It closes 2026-10-04 11:05 your time (10:05 UTC)." and no form opens; `POST /v1/grants` returns 409 `VALIDATION_ERROR` with that Grant's id and state.
- `/s` **Given** two `POST /v1/grants` for one account within 50 ms, **When** both are processed, **Then** exactly one returns 201 and the other 409.

#### support-10.5 · Wait and keep helping
As the Support agent, I see a waiting request's state live, and keep helping from metadata in the meantime, so that waiting for the eater never blocks the case.
Covers: WF-10, FR-081 · SR2 ("support response time increases") · A11 · conflict K10
- `/r` **Given** `grant_31f0` is Requested and unanswered at 10:08 UTC, **When** the agent looks at the Grant panel, **Then** it reads "Requested · sent 11:05 your time (10:05 UTC) · the request closes in 2 d 23 h", and the account panel, Privacy jobs and failed-job tabs stay usable.
- `/r` **Given** the Grant panel is open, **When** the eater answers at 10:20 UTC, **Then** the panel shows the new state within 5 s without a reload, and the screen reader announces it once.
- `/r` **Given** a Requested Grant, **When** the agent looks for a way to cancel it, **Then** none exists, and the panel says "A request ends when the eater answers, or when it closes on 2026-10-04 at 11:05 your time (10:05 UTC) · 13:05 eater's time."

### 10C · The eater decides in Settings → Privacy → Grants

#### support-10.6 · The eater sees who, why, what and how long — and approves
Shared: Support agent + eater (eater-9.22) + auditor (Audit trail).
As the Support agent, I rely on the eater seeing my name, the reason, the Days and areas, the duration and the case in Settings → Privacy → Grants, and approving it there, so that access exists only because the eater chose it.
Covers: WF-10 ("the eater approves or declines in Settings"); map interaction row "Support → Eater"; FR-081; blueprint §2 (the approver is the eater) · SR1, SR2, SR14 · A22
- `/r` **Given** `grant_31f0` is Requested, **When** E1 opens Settings (badge "1") → Privacy → Grants on the iOS simulator, **Then** the request shows, in E1's Arabic app, the Arabic catalogue form of each line below (the English catalogue text is given; the Arabic line further down gives the Arabic for the Days):
  - who: "Mona K. · Support agent";
  - why: "An Entry is missing or appears twice";
  - what: "Entries and day reports, My Units · 28–30 Sep 2026";
  - how long: "1 hour from when you approve";
  - the case `CASE-1182`;
  - "Never included: photos, voice, your Target and goal settings";
  - "The Support agent can read, not change. Every read is listed here.";
  - two equal-size buttons, "Approve" and "Decline".
- `/r` **Given** the eater taps Approve at 13:20 their time (10:20 UTC), **When** the server confirms, **Then** the app reads the Arabic catalogue form of "Active · ends 14:20" (with Arabic-Indic digits «١٤:٢٠»), and the console's Grant panel turns to "Active · read-only · ends 12:20 your time (11:20 UTC) · 14:20 eater's time". The time box starts at approval, not at the request.
- `/s` **Given** `POST /v1/grants/grant_31f0/approve` with the eater's own token, **When** it is processed, **Then**:
  - it returns 200;
  - the state passes Approved to Active in one transaction;
  - `expires_at` = approval time + 1 h;
  - the Audit trail has `grant.approved` and `grant.active`, with method `in_app` and the device.
- `/r` **Given** E1's app is in Arabic, **When** the request shows, **Then**:
  - it reads right to left;
  - the reason reads «إدخال مفقود أو ظاهر مرتين»;
  - the Days read «٢٨–٣٠ سبتمبر ٢٠٢٦» in Arabic-Indic numerals;
  - the Latin name "Mona K." sits in an isolated left-to-right run, without reordering the sentence.
- `/r` **Given** the largest accessibility text size, **When** the request shows, **Then** every line wraps without clipping, and Approve and Decline stay fully visible and tappable.

#### support-10.7 · A declined request gives no access
Shared: Support agent + eater (eater-9.23) + auditor (Audit trail).
As the Support agent, I see a declined request end with no access and no pressure on the eater, so that "no" is a real answer.
Covers: WF-10 done-when ("a declined Grant gives no access"), FR-081 · A14
- `/r` **Given** `grant_31f9` is Requested for E1, **When** the eater taps Decline at 14:42 their time (11:42 UTC), **Then** the console's Grant panel reads "Declined · 12:42 your time (11:42 UTC). Next: check Sync and Privacy jobs for this account.", with links to those two tabs. The eater was not asked for a reason.
- `/s` **Given** `grant_31f9` is Declined, **When** `staff_mona` calls `GET /v1/grants/grant_31f9/days/2026-09-29` at 11:45 UTC, **Then** 403 `GRANT_NOT_ACTIVE` with state Declined, and `grant.read_denied` is in the Audit trail.
- `/r` **Given** the decline is less than 24 h old, **When** any agent opens the Grant form for E1, **Then** the form reads "The eater declined a request at 12:42 your time (11:42 UTC) today" above the reason field.

#### support-10.8 · An unanswered request closes by itself
Shared: Support agent + eater (eater-9.24).
As the Support agent, I see an unanswered request close on its own, so that an old request can never turn into access later.
Covers: WF-10, FR-081 · SR1 (unanswered requests expire) · A6
- `/r` **Given** E10's `grant_40ab` (Requested 2026-09-27 10:05 UTC, never answered), **When** `staff_mona` opens Grants on 2026-10-01, **Then** its row reads "Unanswered · the request closed 2026-09-30 11:05 your time (10:05 UTC)", and E10's Settings → Privacy → Grants lists it in history as "Unanswered · no access was given", with no Approve button.
- `/s` **Given** a Requested Grant and the test clock moved 72 h ahead, **When** the eater's approve call arrives, **Then** 409 `GRANT_NOT_ACTIVE` with state Unanswered — a late approval never makes it Active.

#### support-10.9 · Only the eater can approve
Shared: Support agent + platform admin (`staff_ali`'s token) + auditor (`staff_hana`'s token; Audit trail).
As the Support agent, I cannot approve any Grant — mine or anyone's — and no staff role can, so that nobody at Sips & Bytes can give themselves a diary.
Covers: WF-10, FR-081; blueprint §2 ("no staff member can approve their own request") · SR7, SR8 (AC-5)
- `/s` **Given** E10's `grant_6c14` is Requested, **When** `POST /v1/grants/grant_6c14/approve` is sent with the token of `staff_mona`, `staff_lee`, `staff_ali` or `staff_hana`, **Then** each returns 403 `FORBIDDEN`, and `grant.approve_denied` is in the Audit trail.
- `/s` **Given** E7's eater token, **When** it calls approve on `grant_6c14` (which is for E10), **Then** 404 `NOT_FOUND`.
- `/r` **Given** the console, **When** any staff user views a Requested Grant, **Then** no Approve control exists anywhere.

#### support-10.10 · The eater is offline
Shared: Support agent + eater (eater-9.27).
As the Support agent, I see a request keep waiting while the eater is offline, because the app answers Grants only online, so that an approval is never queued and applied later by surprise.
Covers: WF-10, FR-081, FRD §8.3 (the outbox is for food commands) · care group 4
- `/r` **Given** `grant_31f0` is Requested and E1's simulator has no network, **When** the eater opens Settings → Privacy → Grants, **Then** the cached request shows with Approve and Decline disabled and the line "Connect to answer this request", and the console still reads "Requested".
- `/m` **Given** the app's outbox, **When** a Grant answer is attempted offline, **Then** no outbox command is created.

### 10D · Read inside the box

#### support-10.11 · Read the approved Days, read-only
As the Support agent, I read the approved Days and areas in the Diary (read-only) inside the Grant panel, so that I can find the problem and nothing more.
Covers: WF-10 ("read-only access inside the time box"); map interaction row "Support → eater diary: read within the Grant"; FR-081, FR-046, FR-069, FR-070 · A10
- `/r` **Given** `grant_31f0` is Active, **When** the agent opens the Diary (read-only) for Day 2026-09-29 at 10:24 UTC, **Then** she sees:
  - that Day's Entries in time order, each with its state (Confirmed, Corrected, Voided or Restored), Unit, count, Evidence badge, source version and history;
  - the day report with consumed kcal, macro grams, coverage and the Day state, with the same numbers as the eater's own day report;
  - the line "Target — not included in Grants";
  - no Target, remaining or over value (the eater's request says "Never included: … your Target", support-10.6).
- `/r` **Given** the Diary (read-only), **When** it renders, **Then** the Grant panel:
  - has its own border and the title "Diary (read-only) · Days 28–30 Sep · ends 12:20 your time (11:20 UTC)";
  - has no edit, delete, export, copy-all or print control;
  - carries a faint watermark "staff_mona · grant_31f0".
- `/r` **Given** the areas are "Entries and day reports" and "My Units", **When** the agent opens My Units at 10:27 UTC, **Then** she sees Unit, Composite and Recipe versions with their components, Evidence and version history, while Templates and Activity read "Not in this Grant".

#### support-10.12 · Out-of-scope reads are refused
Shared: Support agent + auditor (Audit trail).
As the Support agent, I am stopped at the edge of what the eater approved, so that a Grant for two Days never becomes a look at two months.
Covers: WF-10, FR-081, NFR-07, FRD §8.1 · SR5, SR8 (AC-6) · conflict K11
- `/r` **Given** E4's `grant_7d01` (Days 2026-09-29 to 2026-09-30; area "Entries and day reports") is Active, **When** the agent moves to Day 2026-09-28 at 15:40 UTC, **Then** the Diary (read-only) reads "28 Sep is outside this Grant" and shows no data; the API returns 403 `GRANT_REQUIRED` (no Grant covers this read), and `grant.read_denied` is in the Audit trail.
- `/s` **Given** `grant_7d01`'s area, **When** `GET /v1/grants/grant_7d01/units` (15:41 UTC) or `…/activity?day=2026-09-29` (15:42 UTC) is called, **Then** 403 `GRANT_REQUIRED`.
- `/s` **Given** `grant_7d01` is for E4, **When** a read at 15:43 UTC names a Day, Entry or Unit id of E2, **Then** 404 `NOT_FOUND` and no data.
- `/m` **Given** E1's diary-day boundary of 04:00 Asia/Riyadh and a scope of Days 2026-09-28 to 2026-09-30, **When** an Entry eaten on 2026-09-30 at 02:30 local time is checked against the scope, **Then** it belongs to Day 2026-09-29 and is in scope.

#### support-10.13 · No changes under a Grant
As the Support agent, I cannot change anything while reading, so that a Grant is never a way to edit someone's history.
Covers: WF-10 ("read-only"), FR-041, FR-081, FRD §16.2; vocabulary D2 (Grant: "writes never")
- `/s` **Given** `grant_7d01`'s token at 15:45 UTC, **When** it is used on `POST /v1/consumption`, `…/corrections`, `…/void`, `POST /v1/units` or `POST /v1/recipes`, **Then** each returns 403 `FORBIDDEN` (the Support agent role has no write permission), and Day 2026-09-29's revision for E4 is unchanged.
- `/r` **Given** the Diary (read-only), **When** the agent inspects the Grant panel, **Then** it holds no input, button or shortcut that edits data.

#### support-10.14 · Media, the Target and the safety screen stay out of every Grant
As the Support agent, I never see photos, audio, transcripts, the Target, safety-screen answers or the tracking-only mode, even under an Active Grant, so that the most sensitive data has no support path at all.
Covers: FRD §19.2 ("Access to raw evidence for quality review requires explicit consent and restricted roles"), FR-038, FR-077, FR-078 · R8, R21
- `/r` **Given** E1's Entry `en_9921` on Day 2026-09-29 was logged from Analysis `an_5512`, which had a photo, **When** the agent opens it under `grant_31f0` at 10:25 UTC, **Then** she sees the Evidence "estimated analogue" and "From a photo analysis", with no image, thumbnail, transcript or link to media.
- `/r` **Given** E4's `grant_7d01` is Active, **When** the agent opens Day 2026-09-29's day report at 15:35 UTC, **Then** it reads "Target — not included in Grants", exactly as for E1 under `grant_31f0`; neither the mode nor any Target value appears.
- `/s` **Given** a Grant request, **When** its areas include media, audio, transcripts, SafetyScreen or GoalPlanVersion inputs, **Then** 422 `VALIDATION_ERROR` — the schema has no such area.

#### support-10.15 · Every read is listed for the eater and the auditor
Shared: Support agent + eater (eater-9.25) + auditor (Audit trail).
As the Support agent, I know each read I make under a Grant is recorded and listed to the eater in the Grant's history and to the auditor, so that the eater can see what I read.
Covers: WF-10 ("every read audited"; done-when "every read shows in the auditor's trail"), FR-081 · SR3, SR13, SR15
- `/r` **Given** fixture G1's three reads (Day 2026-09-29 at 10:24, Entry `en_9921` at 10:25, My Units at 10:27 UTC), **When** the eater opens Settings → Privacy → Grants → that Grant, **Then** its history lists exactly three lines, in the catalogue's Arabic, whose English keys read: "Mona K. read your Day for 29 Sep · 13:24", "Mona K. read an Entry from 29 Sep · 13:25" and "Mona K. read your Units · 13:27". The refused attempts after expiry are not listed, because nothing was shown.
- `/r` **Given** the same Grant after its end, **When** `staff_hana` filters the Audit trail by `grant_31f0`, **Then** she sees, in order:
  - `grant.requested` (10:05);
  - `grant.approved` and `grant.active` (10:20);
  - three `grant.read` (record types Day, Entry and Unit list; their record ids; 10:24, 10:25 and 10:27 UTC; staff `staff_mona`; outcome `allowed`);
  - `grant.expired` (11:20);
  - the two `grant.read_denied` attempts of support-10.17 (11:20:01 and 11:20:05 UTC).
- `/s` **Given** any Grant read, **When** its Audit trail write fails, **Then** the read returns 503 and no data.
- `/s` **Given** the Audit trail collection, **When** any role, staff member or service tries to update or delete an event, **Then** the Firestore rules test in the emulator refuses it (append-only).

#### support-10.16 · Always know how long is left, in both time zones
As the Support agent, I see the time left and the end time in my zone and the eater's, with quiet warnings before the end, so that I finish or ask again in time.
Covers: WF-10 ("inside the time box"), FR-081, FRD §8.1 · care groups 3, 6 · A26
- `/r` **Given** `grant_31f0` ends at 11:20 UTC and the agent is in Europe/Dublin, **When** the Diary (read-only) is open at 10:33 UTC, **Then** the Grant bar reads "47 min left · ends 12:20 your time (11:20 UTC) · 14:20 eater's time".
- `/r` **Given** the same Grant, **When** 11:10 UTC passes, **Then** the Grant bar reads "10 minutes left · the diary closes at 12:20 your time (11:20 UTC)"; **when** 11:18 UTC passes, **then** it reads "2 minutes left · the diary closes at 12:20 your time (11:20 UTC)". Each warning is announced once (polite), with no dialog, no sound and no per-second announcement.
- `/r` **Given** reduced motion is on, **When** the countdown advances, **Then** it updates in place without animation.

### 10E · The box ends

#### support-10.17 · Access ends by itself at the end of the box
Shared: Support agent + eater + auditor (Audit trail).
As the Support agent, I lose access exactly when the Grant expires, enforced by the server, so that no open tab or cached page outlives the eater's permission.
Covers: WF-10 ("auto-expiry"; done-when "the Grant expires"), FR-081 · SR1, SR4, SR8 (AC-2(2))
- `/r` **Given** `grant_31f0` expires at 11:20:00 UTC, **When** the agent moves to Day 2026-09-30 at 11:20:05 UTC, **Then** the Grant panel reads "This Grant expired at 12:20 your time (11:20 UTC)", and the diary content is removed from the page, not just greyed out.
- `/s` **Given** the Grant has expired, **When** a read is sent at 11:20:01 UTC, **Then** it gets 403 `GRANT_NOT_ACTIVE` with state Expired (judged by the server clock), and `grant.read_denied` is in the Audit trail.
- `/s` **Given** the expiry scheduler is stopped in the emulator, **When** a read arrives after `expires_at`, **Then** it still fails (every read checks `expires_at`), and `grant.expired` is written when the scheduler resumes.
- `/r` **Given** expiry, **When** E1 opens Settings → Privacy → Grants, **Then** the Grant reads "Expired · 14:20", with its history.
- `/s` **Given** the Diary (read-only)'s HTTP responses, **When** they are inspected, **Then** each carries `Cache-Control: no-store`, so nothing is served from the browser cache after the box ends.

#### support-10.18 · The eater withdraws access early
Shared: Support agent + eater (eater-9.26, eater-9.17).
As the Support agent, I lose access the moment the eater withdraws a Grant in Settings, so that the eater's "stop" works at once.
Covers: WF-10, FR-081, AT-29; vocabulary D2 ("Withdrawn (by the eater)") · SR2 ("revoked at any time"), SR13
- `/r` **Given** E10's `grant_52a3` is Active, **When** the eater taps "Withdraw access" in Settings → Privacy → Grants at 15:26 their time (12:26 UTC), **Then** the agent's next read returns 403 `GRANT_NOT_ACTIVE` with state Withdrawn, the Grant panel reads "Withdrawn by the eater · 13:26 your time (12:26 UTC)", and the diary content is removed.
- `/r` **Given** the eater withdrew it, **When** the app confirms, **Then** it reads "Withdrawn · 15:26 · The Support agent can no longer read your diary", and the Grant's history keeps every read made before 15:26.
- `/s` **Given** E12's `grant_8e20` is Active, **When** E12 requests account deletion, **Then** the Grant becomes Withdrawn (reason `account_deletion`), and `POST /v1/grants` for E12 returns 404 `NOT_FOUND` with no Grant created (AT-29; eater-9.17).

#### support-10.19 · End access as soon as I'm done
Shared: Support agent + eater + auditor (Audit trail).
As the Support agent, I end a Grant as soon as I have the answer, so that I hold access no longer than needed.
Covers: WF-10, FR-081; vocabulary D2 ("Ended (by the support agent)") · SR8 (AC-6)
- `/r` **Given** E10's `grant_52a7` is Active with 31 minutes left at 13:33 UTC, **When** the agent presses "End access", **Then**:
  - the state is Ended;
  - the diary content is removed;
  - the Grant panel reads "Ended by you · 14:33 your time (13:33 UTC)";
  - E10's Settings → Privacy → Grants reads "Ended by the Support agent · 16:33";
  - `grant.ended` is in the Audit trail.
- `/s` **Given** `grant_52a7` is Ended, **When** a read is sent, **Then** 403 `GRANT_NOT_ACTIVE` with state Ended.

#### support-10.20 · No extensions — ask again
Shared: Support agent + eater (a separate Requested Grant, as in eater-9.23).
As the Support agent, I cannot extend a Grant; I ask for a new one, so that every extra hour is the eater's choice.
Covers: WF-10, FR-081 · SR13 (to change the length, revoke and grant again) · A26
- `/r` **Given** E10's `grant_6c10` (reason "Imported Activity looks wrong"; Days 2026-09-29 to 2026-09-30; area "Activity"; case `CASE-1236`) is Active until 16:02 UTC, **When** the agent looks for "Extend", **Then** there is none. From 15:52 UTC, the Grant bar offers "Ask the eater for more time", which opens a new request with the same reason, Days, area and case.
- `/s` **Given** `PATCH /v1/grants/grant_6c10` changing `expires_at` or the scope, **When** any role sends it, **Then** 405 — a Grant cannot change after it is requested.
- `/r` **Given** that new request is sent at 15:55 UTC while `grant_6c10` is still Active, **When** it is processed, **Then** it is accepted as `grant_6c14` (the one-waiting-request rule counts only Requested Grants), and E10's Settings → Privacy → Grants shows it as a separate request.

#### support-10.21 · An idle console during a Grant shows nothing
Shared: Support agent + auditor (Audit trail).
As the Support agent, I find the diary hidden when my console session times out during a Grant, and I reopen it only through a look-up, so that a desk left unattended shows no diary.
Covers: WF-10, FR-081 · SR6 (automatic logoff), SR8 (AC-12) · A7, A13
- `/r` **Given** `grant_6c10` is Active with its Diary (read-only) open and the agent idle from 15:10 UTC, **When** 15:23 UTC passes, **Then** the warning banner of support-9.1 shows; **when** 15:25 UTC passes, **then** the diary content is removed and sign-in shows.
- `/r` **Given** she signs in again at 15:28 UTC, **When** she opens the Diary (read-only) URL directly, **Then** it reads "Look up this account first" (404 `NOT_FOUND`); **when** she opens `grant_6c10` from Grants instead, **then** `support.lookup` with method `grant` is in the Audit trail, and the Diary (read-only) reopens with its end time "17:02 your time (16:02 UTC)" unchanged.

### 10F · The Grants list, the end-to-end proof, the Registry status, and failures

#### support-10.22 · The Grants list — a history I can account for
As the Support agent, I see my Grants in every state with times and cases, so that I can follow up each case and account for my access.
Covers: WF-10, FR-081 · SR2 ("a historical view of all requests that were approved, dismissed, revoked, or expired") · A15, A21
- `/r` **Given** `staff_mona`'s Grants in §0.3, **When** she opens Grants at 16:00 UTC on 2026-10-01, **Then** the rows read:
  - Active: `grant_6c10`, `grant_7d01`;
  - Requested: `grant_6c14`;
  - Expired: `grant_31f0`;
  - Declined: `grant_31f9`;
  - Unanswered: `grant_40ab`;
  - Withdrawn: `grant_52a3`;
  - Ended: `grant_52a7`.

  Each row shows the eater's account, case, reason, state, and the requested, answered and closed times in her time with UTC; "—" marks a time that has not happened. The state filter works, and no diary content appears.
- `/r` **Given** the Grants API is slowed to 3 s (fault injection), **When** Grants opens, **Then** table-shaped placeholders appear within 300 ms and the rows replace them at 3 s; if loading takes longer than 10 s, the list reads "Still loading — Cancel".
- `/r` **Given** the Grants API returns 503, **When** Grants opens, **Then** it reads "Couldn't load your Grants. Try again." with the request id `req_…`.
- `/r` **Given** `staff_lee`, who has no Grants, **When** he opens Grants, **Then** it reads "You have no Grants. Most cases are solved from the account panel in Jobs."

#### support-10.23 · The whole Grant, end to end
Shared: Support agent + eater + auditor (Audit trail).
As the Support agent, I run one full Grant — request, the eater approves it in Settings, I read inside the box, it expires, the trail shows it — and I see a declined one give nothing, so that WF-10's promise is proved in the served product.
Covers: WF-10 done-when ("support requests a Grant, the eater approves it in Settings, support reads the diary inside the time box, the Grant expires and every read shows in the auditor's trail; a declined Grant gives no access"), FR-081
- `/r` **Given** E1 on the iOS simulator and `staff_mona` in the console, **When** fixture G1 runs as in §0.3, **Then**:
  - the request is sent at 10:05 and the eater approves it in Settings → Privacy → Grants at 10:20;
  - the three reads at 10:24, 10:25 and 10:27 UTC succeed;
  - the clock passes 11:20 UTC;
  - the read attempts at 11:20:01 and 11:20:05 UTC fail with `GRANT_NOT_ACTIVE` (state Expired);
  - the eater's Grant history lists the three reads;
  - the auditor's Audit trail shows requested → approved → active → read ×3 → expired → read_denied ×2.
- `/r` **Given** fixture G2 (`grant_31f9`), **When** the eater declines it and the agent tries the read of support-10.7 at 11:45 UTC, **Then** 403 `GRANT_NOT_ACTIVE` (state Declined), and the Audit trail shows requested → declined → read_denied.

#### support-10.24 · Know when the AI is paused for everyone
Shared: Support agent + platform admin (the kill switch).
As the Support agent, I see the Registry's kill switch state on every console screen, so that I don't troubleshoot one account for a platform-wide pause.
Covers: WF-10 ("roll models and config"); blueprint §6 (Registry: "kill switch"); FRD §16.4 ("A kill switch must preserve manual and cached logging"); FR-080; NFR-05; AT-32 · A11
- `/r` **Given** the kill switch for image analysis is On from 09:10 UTC, **When** any Jobs or Grants screen is open at 09:30 UTC, **Then** a quiet status bar reads "Kill switch On for image analysis since 10:10 your time (09:10 UTC) · manual and recent-Unit logging work".
- `/r` **Given** E14's `an_7740` (Failed at 09:20 UTC, while the switch was On) and E5's `RATE_LIMITED` row (16:40 UTC, after it went Off), **When** their Failed Analyses tabs open, **Then** only `an_7740` carries the tag "Kill switch On"; rows from outside 09:10–09:55 UTC never do.
- `/r` **Given** the kill switch turns Off at 09:55 UTC, **When** 60 seconds pass, **Then** the status bar is gone without a page reload.
- `/s` **Given** a support token, **When** it calls any Registry write endpoint, **Then** 403 `FORBIDDEN`.

#### support-10.25 · Grant actions and the Diary (read-only) when slow, failing or offline
Shared: Support agent + eater (Settings → Privacy → Grant history) + auditor (Audit trail).
As the Support agent, I am told plainly when Send request, a read or End access fails or the network drops, and the diary never stays on screen without the server, so that a failure never leaves a request half-sent or a diary half-open.
Covers: WF-10, FR-081, NFR-05 · care group 4 · A15, A21
- `/r` **Given** the Grants API returns 503 (fault injection), **When** the agent presses Send request on a filled form, **Then** the form keeps every field and reads "Couldn't send the request. Nothing was sent to the eater. Try again.", and no new row appears in Grants.
- `/r` **Given** the browser is offline, **When** the agent opens the Grant form, **Then** Send request is disabled with "Connect to send a request", and the form keeps its fields.
- `/r` **Given** E7's `grant_9b30` is Active and the read API is slowed to 3 s (fault injection) at 17:05 UTC, **When** the agent opens Day 2026-09-30 in the Diary (read-only), **Then** placeholder rows reading "Loading 30 Sep…" appear within 300 ms, and the Day replaces them at 3 s.
- `/r` **Given** the read API returns 503 at 17:10 UTC (fault injection), **When** the agent opens the Day again, **Then** the Diary (read-only) reads "Couldn't load 30 Sep. Nothing was shown. Try again." with the request id `req_…`. The Audit trail records the attempt as `grant.read` with outcome `error`, and the eater's Grant history does not list it.
- `/r` **Given** `grant_9b30` is Active with the Diary (read-only) open, **When** the browser goes offline at 17:20 UTC, **Then** the diary content is removed at once, and the Grant panel reads "Offline — the diary is never stored on this computer. It reopens when you're back online, until 19:00 your time (18:00 UTC)."
- `/r` **Given** the agent presses End access while still offline at 17:22 UTC, **When** the console reacts, **Then** the Grant panel reads "Couldn't reach the server. The Grant stays Active until 19:00 your time (18:00 UTC) unless this goes through. Trying again…". When the connection returns at 17:25 UTC, the call is sent, the state becomes Ended, and the panel reads "Ended by you · 18:25 your time (17:25 UTC)".

---

## 9 · The experience this persona needs

- **Device and place.** A desk, a large monitor (1440 px and up, A28; proved at 1440×900 and 1920×1080 in support-9.19), keyboard first, with a helpdesk window beside the console (A1). Steady office network and light. The same console stays usable at about 390 px for a quick check away from the desk (§0).
- **The moments that matter.**
  1. The first ten seconds of a case: one code in, the account panel on screen, the likely cause visible (SR16).
  2. The ask: a Grant request that a stranger reading it on a phone understands at once.
  3. The edge of the box: access ends exactly when promised, and the agent never wonders whether a page still shows a diary.
- **The feeling it must leave.** For the agent: *"I can help without prying, and the tool proves I did."* For the eater who answers: *"They asked, I chose, I can see what they read, and it ended."*
- **The matching style.** Dense, fast and calm, like any desk tool: tables, monospaced ids, tabular numbers, times by the one rule in §0, keyboard shortcuts, and no animation on repeated actions (checked in support-9.19).
  - One element is deliberately loud: the **Grant panel**, the only place private data can appear, with its own border, title, countdown and watermark, so the agent always knows when they are inside someone's diary.
  - One accent colour is reserved for an Active Grant and means nothing else (checked in support-9.19).
  - The error colour is used only for Failed rows and for a deletion close to its due date. Ordinary states use words (checked in support-9.19).
  - "What to tell the eater" texts come in the eater's language first.

---

## 10 · The care questions, answered as requirements

Size is platform, so every question is asked on every screen this persona uses: in **Jobs**, Look up an account, the account panel, Privacy jobs, the failed-job tabs (Failed Analyses, Sync, Activity) and Requests received outside the app; in **Grants**, the Grant form, the Grant panel with the Diary (read-only), and the Grants list; the console's **Settings**; and, where shared, the eater's **Settings → Privacy → Grants** and **Support code**. Each answer is a requirement with the story that proves it, or n/a with its reason.

**1 · Does it deserve to exist, and where does it live**

| question | answer |
|---|---|
| One sentence per screen; every element serves it | Look up: "find one account from what the eater gives you." Account panel: "explain most issues without the diary." Failed-job tabs: "what failed, when and why — never what it was about." Grant form: "ask for the least access." Grant panel: "read what the eater approved, until it ends." Grants list: "account for my access." Elements that serve no sentence (charts, food content, user lists) are left out (9.5) |
| What should they feel; what did we say no to | the feeling is in §9. No to: a list or search of users, fuzzy look-up, a "recent accounts" list, any staff export, deletion, Consent, quota or ledger control, break-glass (SR7), Grant extensions (10.20), media in Grants (10.14), and a staff approve control (10.9) |
| Need first, not technology first | yes — metadata first, as SR1 describes; diary access only by the eater's choice (10.1, 10.6) |
| Among the few things done most; else one step deeper | look-up and the account panel are the most frequent and come first in Jobs; Grants opens only from the account panel (10.1) |
| Place or action | places in the navigation: Jobs, Grants, Settings. Actions sit next to what they act on: Retry and Escalate on job rows, Request a Grant on the account panel, End access in the Grant bar (9.9, 9.11, 10.1, 10.19) |
| Does each setting need to exist | console language only (9.20). Grant duration, Days and areas exist because the eater must see exactly what is asked (SR1); the rest are fixed values (A-list) |
| Pop-up or mode needed | no dialogs: Grant state changes in place (10.5), and the idle warning is a banner (9.1) |
| Clearly better for existing users | n/a — a new console with no existing users |

**2 · How it is found and understood**

| question | answer |
|---|---|
| Where am I, what can I do, where next, how do I get out | each title names the section and the account ("Jobs · E1's account panel", "Grants · Diary (read-only) · Days 28–30 Sep"); the navigation always shows Jobs, Grants and Settings; Look up is reachable with `/` (9.4); the Grant panel always shows End access (10.19) |
| Title names the place | yes (above) |
| Icons, words and gestures as people know them | standard web tables, tabs and buttons; no custom gestures |
| One word per thing; each colour one meaning | the words of `way/vocabulary.md` and the map. The Grant accent colour means only an Active Grant, and the error colour only Failed or due-soon (9.19) |
| No internal names in labels | eater copy never shows ids or codes (§0 copy rules). Console labels lead with plain words, with the code secondary (4.1, 4.2) |
| Buttons are verbs, the same across steps | Look up, Send request, Approve, Decline, Withdraw access, End access, Ask the eater for more time, Retry, Escalate to the platform admin, Copy, Stay signed in |
| Defaults right | duration defaults to 1 h. Reason, Days and areas start empty on purpose, so the scope is chosen, never assumed (SR5; 10.1) |
| Typing what the system knows | account, eater language and time zone are filled in (10.1); reasons come from a list (10.2). The case reference is typed because there is no helpdesk integration (A22) |
| Tappable versus content | buttons look like buttons; ids are plain text with a Copy button |
| Squint test | the account panel leads with the open Privacy jobs, failed counts and Grant state; the Grant panel leads with its time left |
| Key status where people look | the Grant bar is pinned at the top (9.19, 10.16); the kill switch bar (10.24); due dates on rows (9.10, 9.15) |

**3 · How it feels**

| question | answer |
|---|---|
| Every action answers | Send request → "Requested" (10.2); Approve → "Active" (10.6); End access → "Ended by you" (10.19); Retry → "Requested" (9.9); Escalate → "escalated to the platform admin" (9.11); a failed call says that nothing changed: Retry (9.9), Escalate (9.11, 9.14), Save (9.15), Send request and End access (10.25) |
| Loudness matched | quiet: the kill switch bar, the time-left warnings, the offline strip (10.24, 10.16, 9.21). Error colour only for Failed rows or a deletion with 5 days or fewer left (9.19) |
| Responds at once | a pressed button shows its working state within 100 ms (9.21, A15) |
| Change of mind mid-motion | a draft can be left and resumed (9.15, 10.3). A sent request cannot be cancelled by the Support agent, because vocabulary D2 has no such path (conflict K10); it ends when Declined or Unanswered |
| Animation | none on repeated actions; the countdown updates in place (9.19, 10.16) |
| One main action, never destructive | Look up (Jobs), Send request (form), End access (Active Grant). None destroys data, and the console has no destructive action |
| Edges, gaps, baselines | one spacing grid for tables and panels; checked in the care pass on the served console |
| Chosen values | every value with no source is in the assumptions table (A2–A29), to be tried on the served console |
| First screen at once, back where left | after sign-in, Jobs → Look up opens at once. On purpose, it does not reopen the last account, so every read follows a look-up (A13; 10.21) |

**4 · When it goes wrong, is empty, or is slow**

| question | answer |
|---|---|
| Empty | every list says what to do next: Privacy jobs (9.8), Requests received outside the app (9.15), failed-job tabs (4.3), duplicates (3.2), Activity (7.1), Grants list (10.22) |
| First second while loading | placeholders within 300 ms (4.3); "Looking up…" within 100 ms (9.21) |
| Long tasks: honest progress, cancel | export and deletion show their stages with dates (9.8, 9.10); "Still loading — Cancel" at 10 s (4.3, 9.21, 10.22); the Diary (read-only) shows "Loading 30 Sep…" (10.25) |
| Errors next to the problem, no blame | the field errors in 10.3 and 9.3; row errors on the row (9.9, 9.11, 9.14); screen errors with the request id (9.21, 4.3, 10.22, 10.25); never "oops" |
| Check as typed, fix slips quietly | support codes ignore case, spaces and hyphens (9.2); emails are trimmed and case-insensitive (9.3) |
| Undo shows what it reversed | n/a for staff data changes, because there are none. Retry and End access cannot be undone and are safe: one repeats a job with the same id, the other only removes access |
| Warn before unexpected permanent loss | n/a — the console has no destructive action |
| Half-filled form protected | the Grant form and the Requests-received form keep a draft for the session (10.3, 9.15) |
| Permission asked when first needed | n/a for the console (it asks for no device permission). In the app, answering a Grant needs no OS permission, and push notifications are not required (conflict K2) |
| No network: last data with a quiet note | Jobs keeps the last data with an offline strip (9.21). The Diary (read-only) is never kept and is removed offline (10.25) |
| Say why a command cannot work | "This support code has expired" (9.2), "Look up this account first" (9.17), "28 Sep is outside this Grant" (10.12), "Withdrawn by the eater" (10.18), "Connect to send a request" (10.25) |

**5 · The inside the user never sees**

| question | answer |
|---|---|
| Inside finished like the front | a role-matrix test (9.16), an allow-list test (9.5), a state-machine test (10.2), append-only rules (10.15) |
| Nothing left to chance | the server clock decides expiry (10.17); every read checks `expires_at` (10.17); an audit write failure fails the read (9.18, 10.15) |
| Names match the screen | the words of `way/vocabulary.md` in code, data, the Audit trail and on screen; Audit trail events are named after the Grant states they enter (§0.2) |
| Logs hide personal data and can follow one record | ids, times, states and typed codes only (FRD §19.2; 9.5); each event carries the request id and account id (§0.2) |
| Placeholders gone; realistic sample data | synthetic fixtures with Arabic, two time zones and long cases (§0.3) |
| Collect only what is needed | support codes last 24 h (9.2); the requests log keeps no content (9.15); the completion record keeps no identifiers (9.12) |
| Start and resume time measured | n/a for this lens — it belongs to the release proof's performance pass. The lens adds no wait to start or resume |
| Claims only what it does | "The Support agent can read, not change" is true because the server refuses every write under a Grant (10.13) |

**6 · Inclusion**

| question | answer |
|---|---|
| Largest text size | console at 200 % zoom (9.19); the app's Grant request at the largest accessibility size (10.6) |
| Contrast 4.5:1 in light and dark | 9.19 |
| Visible focus ring | 9.19 |
| Meaning by colour alone | no — states are words (9.10, 10.22) |
| Screen-reader labels stay current | state changes and warnings are announced once (10.5, 10.16, 9.1) |
| Targets big enough | the app's Approve and Decline stay fully tappable at the largest text (10.6); sizes follow platform guidance (NFR-08) |
| Gestures have controls; whole flow by keyboard | 9.19 |
| Reduced motion | 10.16 |
| Timers before a slow reader can act | two deliberate security timers, both warned: the Grant end, at 10 and 2 minutes (10.16), renewable only by asking the eater (10.20); and the idle sign-out, with a 2-minute banner and "Stay signed in" (9.1). Nothing else disappears on a timer |
| Arabic mirroring | the console mirrors while ids, codes and clocks stay left to right (9.20); the app's request mirrors, uses the eater's numerals and isolates the Latin name (10.6) |
| New user, no mouse, other platform | "What to tell the eater" texts (9.7, 9.13, 4.1, 3.1); the keyboard path (9.19); standard web controls |

---

## 11 · Stories shared with other personas

| story | shared with | what the other persona does or sees |
|---|---|---|
| support-9.1 | auditor | sees failed sign-ins, lockouts and idle sign-outs in the Audit trail |
| support-9.2 | eater | reads the support code in Settings → Privacy → Support code (eater-9.29) |
| support-9.3 | auditor | sees `support.lookup` and `support.lookup_rate_limited` |
| support-9.9 | eater, auditor, platform admin | the eater sees "Running" in Settings → Export (eater-9.14); the auditor sees `support.job_retried`; the platform admin receives the escalation after a second failure |
| support-9.11 | platform admin, auditor | the platform admin sees the escalation in Jobs → "Escalated" and resolves it; the auditor sees `support.escalated` |
| support-9.14 | platform admin | receives the "cannot sign in" escalation |
| support-9.15 | auditor | sees `support.request_logged` |
| support-9.16 | nutrition approver, platform admin | `staff_dina` is refused Jobs and Grants; `staff_dina` and `staff_ali` are refused `POST /v1/grants` |
| support-9.18 | auditor | filters the Audit trail by staff, account and time |
| support-10.1 | auditor | sees `grant.read_denied` for a read with no Grant |
| support-10.2 | auditor | sees `grant.requested` |
| support-10.6 | eater, auditor | the eater sees the request in Settings → Privacy → Grants and taps Approve (eater-9.22); the auditor sees `grant.approved` and `grant.active` |
| support-10.7 | eater, auditor | the eater taps Decline without giving a reason (eater-9.23); the auditor sees `grant.read_denied` |
| support-10.8 | eater | sees "Unanswered · no access was given" in history (eater-9.24) |
| support-10.9 | platform admin, auditor | `staff_ali`'s and `staff_hana`'s tokens are refused approval; the auditor sees `grant.approve_denied` |
| support-10.10 | eater | cannot answer offline and is told to connect (eater-9.27) |
| support-10.12 | auditor | sees `grant.read_denied` |
| support-10.15 | eater, auditor | the eater reads the Grant's history (eater-9.25); the auditor reads every `grant.*` event |
| support-10.17 | eater, auditor | the eater sees "Expired · 14:20"; the auditor sees `grant.read_denied` |
| support-10.18 | eater | taps Withdraw access (eater-9.26); deletion during a Grant (eater-9.17) |
| support-10.19 | eater, auditor | the eater sees "Ended by the Support agent"; the auditor sees `grant.ended` |
| support-10.20 | eater | sees the new request as a separate one |
| support-10.21 | auditor | sees `support.lookup` with method `grant` |
| support-10.23 | eater, auditor | the end-to-end WF-10 proof |
| support-10.24 | platform admin | the kill switch the platform admin turns On and Off |
| support-10.25 | eater, auditor | the eater's Grant history does not list a read that ended in an error; the auditor sees that attempt as `grant.read` with outcome `error` |

---

## 12 · Conflicts for the model phase

- **K1 · eater ↔ Support agent: what the eater's Grant history shows — aligned in round 2.** The eater lens lists Grant reads only (eater C-14). This lens now does the same (10.15): the history shows `grant.read` events with outcome `allowed`. Metadata look-ups and refused attempts appear only in the Audit trail. The once-a-day metadata line proposed in round 1 is withdrawn.
- **K2 · eater ↔ Support agent: how the eater learns a request is waiting — answered by the eater lens.** eater-9.21 shows the Settings badge in every case, and one notification only if notifications were already allowed, with no permission prompt (R2). A request may still wait unseen, which is why the 72 h Unanswered window exists (A6).
- **K3 · platform admin ↔ nutrition approver: who owns the Grant limits.**
  - The limits are durations, maximum Days, the request window, idle sign-out and the email look-up rate. They are neither nutrition Policy (approver-owned, blueprint §6) nor the model Registry.
  - Proposal: a versioned configuration owned by the platform admin and readable by the auditor. The model phase decides where it lives.
- **K4 · platform admin ↔ Support agent: who retries Failed jobs.** This lens gives the Support agent one retry of a Failed **export** (A9) and only "escalate" for deletion and Consent-withdrawal stages. The platform-admin lens may want all job retries for itself, or may give support more.
- **K5 · auditor ↔ Support agent ↔ platform admin: role combinations.**
  - Separation of duties (SR7, SR8 AC-5, FR-081) means one staff account must not hold the Support agent role together with Auditor or Platform admin.
  - Otherwise one person could request access and then review their own trail, or grant themselves the role.
  - The Roles section must refuse the combination. The rule belongs to the platform admin or the auditor lens.
- **K6 · eater ↔ map vocabulary: the word on the eater's screen.** The map's word is "Grant". Eaters may not understand "Grants" as a Settings label. This lens keeps "Grant" with an explanatory line (10.6). The eater lens may want a plainer label, which would need a dated delta.
- **K7 · nutrition approver ↔ Support agent: approvers looking into an eater's Entry.**
  - An approver checking a complaint about a Food record may want to see which Food version an eater's Entry used.
  - FR-081 separates the roles, and only the Support agent can request a Grant (9.16).
  - The approver would have to work through support, or through de-identified quality metrics (FR-080).
- **K8 · the map: which workflow owns the Support agent's failed-job views.**
  - FR-080 puts "failed jobs" in the console, and the dispatch placed them under WF-9. But WF-9 is "Privacy — consents, export, delete account".
  - This lens therefore files the views under the workflows whose jobs they report: Sync under WF-3 (3.1, 3.2), Analysis under WF-4 (4.1–4.3), Activity under WF-7 (7.1), and the kill switch under WF-10's Registry (10.24).
  - The model phase should either add "the Support agent reads failed-job metadata" as a step of those workflows in the map, or add a support step to WF-9 and renumber.
- **K9 · platform admin ↔ auditor: who verifies identity and acts for an eater who cannot sign in.**
  - The Auditor seat (the map's privacy-reviewer/DPO seat) is read-only. The Support agent has no export or delete control (9.13).
  - Support-9.14 therefore escalates to the platform admin. Someone must still verify identity outside the app (SR9 Art. 12(6), SR11 Art. 3(1)(c)), without an ID copy unless necessary (SR10 ¶74), and run the job within 30 days.
  - The model phase names the role, and the owner may need to decide it.
- **K10 · vocabulary D2: no path for the Support agent to cancel a Requested Grant.** D2 lists Requested → Declined · Unanswered only. A mistaken request therefore waits for the eater or for its window. Proposal for a dated delta: "Requested → Ended (by the support agent)". Until then, 10.5 shows that there is no cancel control.
- **K11 · vocabulary D2: codes this lens stretches.**
  - 409 `VALIDATION_ERROR` is used when a request is already waiting (10.4) and for a second retry of the same job (9.9), with the current state in the body.
  - `GRANT_REQUIRED` is used for a read outside the Grant's Days or areas (10.12): no Grant covers the read, and the role itself has the read permission, so `FORBIDDEN` (D2: "role lacks the permission") does not fit.
  - Dedicated codes would need a dated delta.
  - A new Grant on an account whose deletion was requested now returns 404 `NOT_FOUND`, as in eater-9.17. That is not a stretch.
- **K12 · eater lens ↔ Support agent: lines in `way/personas/eater/wf1-wf9.md` that still differ from this lens.** This lens follows the map and D2. The eater lens should align these lines:
  - (a) **"view" against "read".** eater-9.22 says "Every view is listed here", eater-9.25 says "viewed your diary", and eater-9.26 says "can no longer see your diary". The map's word is "read" ("read within the Grant"; D2 "reads are allowed only while Active"), and the Audit trail event is `grant.read`. This lens's copy is "Every read is listed here", "Mona K. read your Day for 29 Sep · 13:24" and "can no longer read your diary".
  - (b) **Deletion stage words.** eater-9.17 expects each stage to read "done" or "waiting". This lens shows the D2 Privacy job words — Completed, Running, Requested, Failed — plus "Not applicable" (9.10).
  - (c) **"Re-queue".** eater-9.14 says a Support agent "re-queues" the export. The action is Retry (D2: "retried with the same id"; 9.9).
  - (d) **Grant fixtures.**
    - eater-9.23 declines `grant_31f0` at 13:12, and eater-9.26 withdraws it at 13:41 (Asia/Riyadh). Here `grant_31f0` is approved at 10:20 UTC and Expires, the declined Grant is `grant_31f9`, and the withdrawn one is `grant_52a3`.
    - eater-9.24's `grant_40aa` is Requested for E1 at 10:05 UTC, alongside `grant_31f0`. The one-waiting-request rule (10.4) forbids that, so this lens's Unanswered Grant is `grant_40ab` on E10.
  - (e) **Story ids.** The eater file cites this lens's round-0 ids (support-9.8 for the retry, support-9.9 and 9.11 for deletion). Use the map in "Fix round 1": 9.9, 9.10 and 9.12.
  - (f) **The Target line.** eater-1.19's Shared line says a Support agent's view shows "No Target". Under a Grant, every eater's day report reads "Target — not included in Grants" (10.11, 10.14), matching eater-9.22's "Never included: … your Target".
  - (g) **The reason text.** eater-9.22 quotes the reason as "An entry is missing or appears twice". The catalogue writes "An Entry is missing or appears twice" (10.2).

---

## 13 · Words this lens needs that the map and `way/vocabulary.md` do not yet name

These are proposals for a dated delta. **Console views** inside the D2 sections:
- *Jobs:* "Look up an account", the **account panel**, **Privacy help**, **Privacy jobs**, the failed-job tabs **Failed Analyses**, **Sync** (with "Duplicates ignored") and **Activity**, **Requests received outside the app** (states Open · Escalated to the platform admin · Closed), "What to tell the eater", and the filter "Escalated" (shared with the platform admin).
- *Grants:* the **Grant form**, the **Grant panel** (one Grant's state, scope and times; while the Grant is Active it holds the Diary (read-only)), the **Grant bar** (the strip pinned at the top of every console screen while the agent has an Active Grant for the account in view, showing that Grant's time left and end times), the **Diary (read-only)**, and the Grants list.

**Other words:**
- **Grant areas:** Entries and day reports · My Units · Templates · Activity.
- **The reason catalogue** (10.2).
- **Identifiers the eater reads out:** the **support code**, the **deletion reference**, and the **case reference** (the helpdesk's ticket number).
- **App items in Settings → Privacy:** Support code · Grants · Delete account.
- **Eater actions on a Grant:** Approve · Decline · Withdraw access.
- **Support actions:** Send request · End access · Ask the eater for more time · Retry · Escalate to the platform admin.
- **Consent-withdrawal Privacy job stages** (9.7) and **deletion stages** (9.10).

---

## 14 · Coverage — FRD lines and map rows to stories

| line | stories |
|---|---|
| WF-3 ("offline logs sync once") | support-3.1, 3.2 |
| WF-4 (photo / label / voice / photo + words → draft) | support-4.1, 4.2, 4.3 |
| WF-7 (HealthKit and manual exercise → dedupe) | support-7.1 |
| WF-9 done-when: export downloads entries, units, recipes, targets and consents | support-9.8 |
| WF-9 done-when: deletion leaves a completion record without identifiers | support-9.10, 9.12 |
| WF-10: support requests a Grant → the eater approves or declines in Settings → reads within the time box → expiry, every read audited | support-10.1–10.23 |
| WF-10: roll models and config (Registry, kill switch) — the Support agent's read-only side | support-10.24 |
| WF-10: approve food and recipe records and aliases, version Policy, roll models | not this persona — Nutrition approver and Platform admin lenses |
| Interaction row "Eater → API: export; delete account → Privacy job" | support-9.8–9.15 |
| Interaction row "Eater → app: … separate consents, one-tap withdrawal" | support-9.6, 9.7 |
| Interaction row "Support → Eater: request just-in-time diary access" | support-10.2, 10.6–10.10 |
| Interaction row "Support → eater diary: read within the Grant" | support-10.11–10.21 |
| Interaction row "Auditor → trail: read" (the Support agent's events) | support-9.18, 10.15, 10.23 |
| FR-001 (anonymous session, local trial) | support-9.2 |
| FR-038 (no inference from people in photos; metadata stripped) | support-10.14 |
| FR-041 (correct, void, restore, move with audit) | support-3.1, 10.13 |
| FR-043 (retries never add food twice) | support-3.1, 3.2 |
| FR-046 (timeline, source details, correction history) | support-10.11 |
| FR-063, FR-064 (provider records, dedupe) | support-7.1 |
| FR-067 (coverage; denial is "unknown") | support-9.6, 7.1 |
| FR-069, FR-070 (meal and day report, reconciled) | support-10.11 |
| FR-075 (export) | support-9.8, 9.9 |
| FR-076 (separate consents) | support-9.6, 9.7 |
| FR-077 (private images, signed access) | support-10.14 |
| FR-078 (in-app export and deletion; media policy) | support-9.7–9.14, 10.14 |
| FR-079 (no secondary use) | support-9.5 |
| FR-080 (console: failed jobs, de-identified) | support-9.1, 9.4, 9.11, 9.16, 9.19–9.21, 3.1, 3.2, 4.1–4.3, 7.1, 10.24 |
| FR-081 (separate support privileges; just-in-time access with an audit trail) | support-9.1–9.6, 9.16–9.18, 10.1–10.25 |
| FR-082 (pre-release privacy review, retention verification) | a release gate, not a product story. The evidence it reviews comes from support-9.10, 9.12 (completion record), 9.18 and 10.15 (Audit trail) |
| AT-10 (one command delivered three times) | support-3.2 |
| AT-21 (plan confirmation retried) | support-3.2 |
| AT-22 (one workout from two feeds plus manual) | support-7.1 |
| AT-29 (deletion and consent withdrawal propagate to media, queues, cached analysis, exports) | support-9.7, 9.10, 9.11, 10.18 |
| AT-31 (two devices edit offline) | support-3.1 |
| AT-32 (AI times out; manual logging works) | support-4.1, 10.24 |
| NFR-03 (AI responsiveness) | support-4.1 |
| NFR-05 (AI failure must not disable manual logging) | support-9.21, 10.24, 10.25 |
| NFR-06 (offline resilience, no duplicate replay) | support-3.1 |
| NFR-07 (no cross-user access) | support-9.5, 9.16, 9.17, 10.9, 10.12 |
| NFR-08 (accessibility) | support-9.10, 9.19, 9.20, 10.6, 10.16 |
| NFR-12 (least privilege, quotas, bounded retries) | support-9.1, 9.3, 9.9, 9.16, 4.2 |
| NFR-13 (deletion ≤30 days) | support-9.10–9.12, 9.14 |

**Totals:** 52 stories (journey 3: 2 · journey 4: 3 · journey 7: 1 · journey 9: 21 · journey 10: 25); 190 acceptance lines (journey 3: 5 · journey 4: 9 · journey 7: 2 · journey 9: 91 · journey 10: 83): 149 `/r`, 35 `/s`, 6 `/m`. Every story has at least one `/r` line.


## Lens verdict (2026-10-01)

**fail**: 21 defects.

The verifier did not write this lens. It was checked against `way/blueprint.md` §0–§1, `way/brief/frd-v1.0.md`, `way/personas/_lens-brief.md`, `care.md` ("The questions", "By size"), `way/research/r1-*.md` and both refutations.

These parts hold:
- The counts are right: 48 stories and 146 acceptance lines (106 `/r`, 34 `/s`, 6 `/m`).
- Every story has a `/r` line.
- Every WF-10 step and both done-when clauses (WF-9 and WF-10) have stories.
- Every cycle-1 finding the lens cites (R2–R4, R7, R8, R21–R25, R31) stands in `r1-refute-b.md`. R34, R29's decree type and P11 are named only as avoided.
- All 16 SR sources were re-opened on 2026-10-01 with a generic User-Agent; the one exception is SR2's approve-requests page. Every quote and date checked is on its page.

### Defects

1. **support-9.13 · traced.** "Covers: FR-082 · SR9 Art. 12(3), SR11 Art. 3(1)(a)(d) · R23, R31". FR-082 is the story's only FRD line, and FR-082 is a gate before release ("Complete launch-market privacy/legal review … before public release"). It is not a request log that runs in the product. The Privacy requests log rests on SR11 Art. 3(1)(d) and on the map's Privacy job row (R23, R31). Cite those as the trace.
2. **Missing step: consent withdrawal propagating (WF-9, AT-29) · complete.** AT-29 says "Account deletion and consent withdrawal propagate to media, queues, private cached analysis, and exports". Support can see the deletion stages (9.9). For a withdrawn Consent it sees only "Off · withdrawn 2026-09-30 21:14 · v3" (9.6). No story lets support see or explain the propagation that follows a withdrawal.
3. **support-9.7 / 9.8 · complete.** The export states shown are Ready (9.7), Failed and Queued (9.8). Two states have no acceptance: Running, when the eater asks "where is my export?" while it is still being built, and Expired, after the lens's own window (A8: "A ready export stays downloadable in the app for 7 days").
4. **support-9.8 · complete and observable.** A9 says "Support may re-queue a failed export once". No line shows what happens when the re-queued job fails again. "Re-queue … the button disappears" covers only the moment after the press, so the "once" rule and the next step after it cannot be observed.
5. **Missing unhappy path: deletion or export for an eater who cannot sign in (WF-9) · complete.** 9.3 is "so that I can help someone who cannot open the app". 9.12 says support will "never do it for them". 9.13 closes a row only as "Completed by in-app export". No story says how a request from an eater who has lost access (lost phone, Sign in with Apple account gone) is answered within the 30 days that G3 promises.
6. **support-9.1 · complete.** The story is "so that everything I do is tied to me and nobody can act as me" and it covers SR6, whose summary lists "person authentication". Its acceptance has only a successful sign-in, the idle logoff and the refusal of tokens that are not staff tokens. A failed staff sign-in has no line, and neither does a second factor (or a decision not to require one).
7. **Unhappy paths outside Failed jobs · complete.** Slow, error and offline states exist only for Failed jobs (9.20). Account lookup, Account state, Privacy jobs, the Grant request form and the Grant panel have none. §7 promises "No network in the console: … a quiet 'Offline — showing data from 10:42'", but no story carries it. "Send request", "Withdraw request" and "End access now" have no line for a call that fails or is made offline.
8. **support-9.10 · observable.** The row offers "only 'Escalate to platform admin'" and then shows "Escalated 2026-10-01 11:05 by Mona K.". Nothing says where the escalation lands or how the platform admin sees it. Past the support row, nobody can observe the story's outcome ("no deletion quietly misses its legal window").
9. **Vague acceptance lines · observable.**
   - support-9.3 `/s`: "the Audit trail marks the burst for the auditor" names no event and no field.
   - support-9.20: "Given the jobs API answers slowly … placeholders appear at once" gives no number for either delay.
   - support-10.16: "the bar says so quietly" does not give the warning text.
   - support-10.15: "lists three lines such as 'Mona K. viewed your diary for 29 Sep · 13:24'" leaves two of the three lines unspecified.
10. **The fixtures contradict each other · observable.** One seed cannot satisfy all of these lines:
    - (a) E1 withdrew "Send photos, voice and text to Google's AI" on 2026-09-30 21:14 (9.6). E1 still has "AI analyses today 3 of 10" (9.4), Analysis `an_5530` at 2026-10-01 12:04 (9.14), and "used 10 of 10 analyses on 2026-10-01" (9.15).
    - (b) E2 asked for deletion on 2026-09-15 and shows "signed out and disabled — done 09-15; private records deleted — done 09-15" (9.9). E2 still requests exports on 2026-09-30 (9.7) and on 2026-10-01 (9.13, `job_exp_88`).
    - (c) `staff_omar`'s console is "in Arabic", yet 10.4 expects him to see the English "Mona K. has a request waiting for this eater …".
11. **support-10.21 against support-9.22 · observable.** 10.21 says "after signing in again before expiry the agent can reopen the Diary". A13 says "Every support read about an account requires a look-up of that account in the same console session", and 9.22 enforces it with `LOOKUP_REQUIRED`. The new session after the idle logoff has no look-up, so a verifier cannot tell which result is right: the Diary opens directly, or a look-up comes first.
12. **Time zones · observable and experience.** support-10.19 expects the eater's app to show "Ended early by support at 10:49". 10:49 is UTC (31 min before 11:20 UTC). E1 is in Asia/Riyadh, and 10.15 says the access history is "in the eater's language and time zone", so the app should show 13:49. More broadly, §2 says "every time shows in both zones", yet many console lines show one zone or none: 9.7 "requested 2026-09-30 18:02 · ready 18:09", 9.10 "Escalated 2026-10-01 11:05", 9.16 "since 09:10 UTC", 10.5 "sent 10:05 UTC", 10.7 "at 10:12 UTC".
13. **§2 · sourced.** "What they use today and hate" credits dislikes to sources that record none. SR10 is a regulator's guidance on proportionality, SR13 documents vendors' settings, and SR2 states a cost in response time. Only SR16 gives agents' own view ("tiresome for agents"). "overbroad access made every employee a suspect" is not in SR12. "continued spying for months" is in SR12 and holds. Label the rest `assumption`, or reword it to what the sources say.
14. **Vocabulary: Day.** The map's word is **Day**. The lens writes "per-Day counts" once (9.7 `/s`) and "diary day(s)" everywhere else (fixture G1, 10.2, 10.3, 10.12 …). Use one name for the thing: keep Day, or propose "diary day" in §10.
15. **Vocabulary: Grant actions and states.** The screen, the code and the trail use different words for the same thing (care group 5):
    - The eater taps "Allow", but the endpoint, state and event are `approve` / `approved` / `grant.approved`.
    - The eater's screen says "Support can read, not change. Every view is listed here." and "Mona K. viewed your diary", but the event is `grant.read`.
    - The eater's "End access" produces state `revoked` / `GRANT_REVOKED`, but the console says "The eater ended access".
    - Support's "End access now" produces state `ended`, but a read then returns "403 `GRANT_EXPIRED` with state `ended`" (10.19).
16. **Vocabulary: the lens's own screen names drift.**
    - One view has three names: "Diary (read-only)" (10.11, §7), "Diary view" (10.11, 10.13, 10.17) and "the Diary" (10.16).
    - "Grant panel" (9.24, 10.11), "Grant bar" (9.24, 10.16, §7), "the panel" and "the bar" (10.16, 10.20) all carry the end time, and their relation is never defined.
    - "Privacy help" (9.12) and "Settings → Help" (9.2) are missing from §10's list of names the map does not yet have.
17. **§6, matching style · experience.** §6 states three style rules that no acceptance line checks: "One accent colour is reserved for 'inside an active Grant' and means nothing else", "no animation on repeated actions", and "ordinary states use words, not red". §6 also says "a large monitor (1440 px and up)", but 9.24 proves the three-column layout only at 1920×1080.
18. **Timers · experience (care group 6).** §7 says "Nothing else disappears on a timer". But 9.1 and 10.21 sign the agent out after 15 minutes idle and clear the page, with no warning before it happens. For the console, this answers the care question "Does anything disappear on a timer before a slow reader can act on it?" wrongly.
19. **Care group 4 questions this persona raises, left unanswered.**
    - "If someone closes a half-filled form, is their work protected?" is not answered for the Grant request form or the Privacy requests log form.
    - "Do we … quietly fix an obvious slip?" is not answered for the support code. 9.2 treats any inexact code as "No account matches this code", and nothing says whether lowercase, spaces or a missing hyphen in `SB-7KQ2-94XM` are normalised.
20. **Ids: journey = WF number.** support-9.14 to 9.20 (Analysis failures, AI quota, kill switch, sync conflicts, duplicate deliveries, Activity import, Failed jobs states) carry journey 9. The map's WF-9 is "Privacy — consents, export, delete account". Their content belongs to WF-4, WF-3, WF-7 and the WF-10 registry. The dispatch put failed jobs under WF-9, so this is a gap in the map: no workflow owns FR-080's failed jobs for support. The model phase should settle it, either by renumbering these stories or by adding the support side to a workflow in the map.
21. **Shared stories not marked (lens brief item 5).** These stories have acceptance on another persona's surface but carry no "Shared:" line, and §8 leaves them out:
    - eater: 10.5 ("the eater's Settings → Privacy → Grants shows it in history as 'Withdrawn by support'"), 10.8, 10.17 and 10.19;
    - auditor (its Audit trail): 9.1, 9.3, 9.8, 9.10 and 10.2 ("When the auditor opens the Audit trail");
    - platform admin: 9.10, which the escalation is addressed to.

## Fix round 1 (2026-10-01)

Every defect was fixed in the body of this file. The verdict above is kept as written, so its story numbers are the **old** ones. The binding vocabulary of delta D2 (`way/vocabulary.md`) arrived during this round and was applied throughout.

**Old → new story ids**

| old | new |
|---|---|
| 9.1–9.6 | 9.1–9.6 (unchanged ids) |
| — | 9.7 (new, defect 2) |
| 9.7 → 9.13 | 9.8 → 9.13 (each moved up one) |
| — | 9.14 (new, defect 5) |
| 9.13 | 9.15 |
| 9.14 | 4.1 |
| 9.15 | 4.2 |
| 9.16 | 10.24 |
| 9.17 | 3.1 |
| 9.18 | 3.2 |
| 9.19 | 7.1 |
| 9.20 | 4.3 |
| 9.21 → 9.25 | 9.16 → 9.20 |
| — | 9.21 (new, defect 7) |
| 10.1–10.23 | 10.1–10.23 (same ids) |
| — | 10.25 (new, defect 7) |

**Fixes, by defect number**

1. **Trace of support-9.15 (was 9.13).** FR-082 is removed from its Covers line. The story now traces to the map's interaction row "Eater → API: export; delete account → Privacy job (≤30 days)" and to SR11 Art. 3(1)(a)(d), SR9 Art. 12(3), R23 and R31. In §14, FR-082 is now listed as a release gate, with only the evidence stories named. Every other story's Covers line was also checked so that it names a WF step, an interaction row or an FR/AT/NFR line.
2. **Consent withdrawal propagating.** New support-9.7 shows the Consent-withdrawal Privacy job for E6, with stage times for media, queues, cached analyses and exports (AT-29). It has an emulator `/s` check that each is gone and that a later Analysis gets `CONSENT_REQUIRED`, and an Arabic-first "What to tell the eater" text.
3. **Export states.** support-9.8 now has Running (`job_exp_88`) and "download no longer available" after the A8 window (`job_exp_31`). Following D2, the Privacy job states are Requested · Running · Completed · Failed. "Ready" became Completed, and the end of the download window is a fact on a Completed row, not a state.
4. **Retry once.** support-9.9 now shows what happens when the retried export fails again: "Failed · retried once by Mona K.", no Retry button, and only "Escalate to the platform admin". "Re-queue" is renamed Retry, following D2's "retried with the same id".
5. **An eater who cannot sign in.** New support-9.14 gives the recovery path (A20), records the request with its due date of 2026-10-31, escalates to the platform admin's Jobs → "Escalated" when recovery fails, and forbids collecting ID copies (SR10 ¶74). New conflict K9 covers who verifies identity and acts.
6. **Staff sign-in.** support-9.1 now covers the second factor (A19; SR4 MFA), a wrong password with generic copy and `staff.sign_in_failed`, and the lockout after 5 failures with the auditor's view (A18; SR6 §164.308(a)(5)(ii)(C), opened earlier in this run and now cited).
7. **Unhappy paths outside the failed-job tabs.** New support-9.21 covers Look up, the account panel and Privacy jobs when slow, failing or offline, including the "Offline — showing data from …" strip. New support-10.25 covers Send request failing or offline, End access failing or offline, and the Diary (read-only) being removed at once when offline.
8. **Where an escalation lands.** support-9.11 now shows the escalation in the platform admin's Jobs with the "Escalated" filter, sorted by due date. When the admin's retry completes, the support row reads "escalation resolved by the platform admin". The story is marked shared with the platform admin.
9. **Vague lines.**
   - support-9.3 now names `support.lookup_rate_limited` and its fields, plus the auditor's "Rate limited" filter.
   - support-4.3 (was 9.20) gives numbers: placeholders within 300 ms with the API slowed to 3 s, and "Still loading — Cancel" at 10 s (A15, A21).
   - support-10.16 gives both warning texts and their times.
   - support-10.15 gives all three history lines and their times.
10. **Contradictory fixtures.**
    - (a) The Consent withdrawal moved to a new eater, E6. The AI failure and quota moved to E5. E1 keeps its Consent Given and "3 of 10".
    - (b) Exports moved to E7. E2 has only its deletion. Email look-ups use E7 or the finished-deletion fixture E8.
    - (c) support-10.4 now uses `staff_lee`, whose console is in English. `staff_omar` appears only in the Arabic story.
    - Branch Grants that also contradicted each other were split by eater and time (§0.3: G1, G2 and E10's Grants).
    - Givens that needed a future date or a clock now use seeded past states (the expired code `SB-3MRT-7WQD`, `grant_40aa`, E8) or the test clock (10.8 `/s`).
11. **Idle sign-out against look-up first.** A13 now lists opening one of my own Active Grants from Grants as a look-up method (`support.lookup` with method `grant`). support-10.21 shows that the direct Diary (read-only) URL gives "Look up this account first" (404 `NOT_FOUND`), and that opening the Grant from Grants reopens the diary with its end time unchanged.
12. **Time zones.** §0 now states one rule: the agent's time with UTC, and the eater's time on anything the agent repeats to the eater. Offsets come from the IANA database (new SR17, opened). Every console time was rewritten to the rule. support-10.19's app now shows "Ended by support · 16:33", the eater's time (Africa/Cairo).
13. **§2 sourcing.** "Made every employee a suspect" is removed. The dislikes are now worded as what each source says: SR16 is the agents' own view; SR13, SR10 and SR2 are stated as facts. The two opinions are labelled `assumption`.
14. **Day.** The record is "Day" everywhere: Days in a Grant, "your Day for 29 Sep", Day ids. "diary-day boundary" is kept only as the map's setting name (blueprint §1 ¶6).
15. **One name per Grant state and action.**
    - States are D2's list (§0.1): Requested · Approved · Active · Expired · Ended · Withdrawn · Declined · Unanswered.
    - Eater actions: Approve → Approved → Active; Decline → Declined; Withdraw access → Withdrawn.
    - The Support agent's action: End access → Ended.
    - Reads: the eater copy says "read", and the event is `grant.read`.
    - Each Audit trail event is named after the state it enters (§0.2).
    - Errors are D2's: `GRANT_REQUIRED`, `GRANT_NOT_ACTIVE` (with `state`), `FORBIDDEN`, `NOT_FOUND`, `VALIDATION_ERROR`, `UNAUTHENTICATED`, `RATE_LIMITED`, `CONSENT_REQUIRED`. All the lens's own codes are gone.
    - The support "Withdraw request" is removed because D2 has no such path (conflict K10).
16. **Screen names.**
    - One view is now "Diary (read-only)".
    - "Grant panel" and "Grant bar" are defined in §13 and used only as defined.
    - The screens now sit in D2's console sections (Jobs, Grants, Settings, Audit trail). "Account state" became the account panel in Jobs.
    - "Settings → Help" became Settings → Privacy → Support code, since D2's Settings has no Help.
    - Every lens-only name is listed in §13.
17. **§9 style rules (was §6).** support-9.19 now checks the accent colour token (only on the Grant panel and bar, only while a Grant is Active), the absence of animation on repeated actions, the error colour (only on Failed rows and deletions due within 5 days), and the three-column layout at 1440×900 as well as at 1920×1080.
18. **Timers.** support-9.1 adds the 2-minute idle warning banner with "Stay signed in" (A7). support-10.21 shows it during a Grant. The §10 care answer on timers now names both deliberate timers and their warnings.
19. **Care group 4.**
    - Drafts of the Grant form (10.3) and of the Requests-received form (9.15) survive a detour in the session and are cleared at sign-out.
    - Support codes are normalised for case, spaces and hyphens (9.2, plus a `/m` line), and emails are trimmed and matched without case (9.3).
    - §10 now answers every care question in every group, or marks it n/a with its reason.
20. **Ids follow the WF.** The failed-job stories were renumbered to the workflow whose jobs they report: support-3.1 and 3.2 (WF-3 sync), 4.1–4.3 (WF-4 Analysis), 7.1 (WF-7 Activity) and 10.24 (WF-10 Registry kill switch). New conflict K8 asks the model phase to add the Support agent's read-only step to those workflows in the map.
21. **Shared stories marked.**
    - "Shared:" lines and §11 rows were added for the eater (10.8, 10.17, 10.18, 10.19, 10.20), the auditor (9.1, 9.3, 9.9, 9.11, 9.15, 10.2, 10.9, 10.12) and the platform admin (9.9, 9.11, 9.14).
    - Old 10.5's "Withdrawn by support" line is gone with the removed action.

**General rules from the coordinator**, also applied:
- Every story traces to a WF step, an interaction row or an FR/AT/NFR line.
- §14 maps every FR, AT and NFR line touched, row by row, and marks the WF-10 parts that belong to other personas.
- Names follow the map and D2, with every status from one declared list (§0.1).
- Acceptance lines name their screen and data.
- Givens are seeded or reachable through the test build (A21).
- Unsourced claims are labelled `assumption`.
- No internal id appears in eater copy (§0 copy rules).
- Every care question is answered, or marked n/a with its reason.

**Counts after the fix:** 52 stories, 179 acceptance lines (138 `/r`, 35 `/s`, 6 `/m`). No owner identifier was sent to any service. The only new source opened in this round was the local IANA time zone database.

## Lens verdict — re-verify (2026-10-01)

**fail**: 10 defects.

The verifier did not write this lens. It re-read `way/blueprint.md` §0–§1, `way/vocabulary.md` (delta D2, binding), `way/brief/frd-v1.0.md`, `way/personas/_lens-brief.md`, `care.md` ("The questions", "By size") and both refutations. It checked each fix of round 1 against the body above, then ran the full check again.

These parts hold:
- The counts are right: 52 stories (journey 3: 2 · 4: 3 · 7: 1 · 9: 21 · 10: 25) and 179 acceptance lines (138 `/r`, 35 `/s`, 6 `/m`). Every story has a `/r` line.
- Every Covers line names a WF step, an interaction row or an FR/AT/NFR line, and every id carries its WF number.
- Every cited cycle-1 finding (R2–R4, R7, R8, R21–R25, R31) stands in `r1-refute-b.md`, and R4's quote contains "other support flows".
- Three sources were re-opened today with a generic User-Agent:
  - SR6 §164.308(a)(5)(ii)(C): "Log-in monitoring (Addressable). Procedures for monitoring log-in attempts and reporting discrepancies." This was read through eCFR's API, because the page itself serves a CAPTCHA.
  - SR12's complaint describes two incidents: the months-long viewing (June–August 2017, ¶17) and the email look-up (January 2018, ¶20). So "in another case" holds.
  - SR2's approve-requests page, which the first verifier could not open, is last updated 2026-09-24 and says "Receive requests through email", "Receive requests through Pub/Sub" and "To approve a request, click Approve".
- SR17's offsets match the local tzdata 2025b (Riyadh +3; Cairo +3 to 29 Oct, then +2; Dublin +1 to 25 Oct).
- No owner identifier was sent to any service.

### The 21 earlier defects

1. **Fixed.** support-9.15 Covers: "map interaction row "Eater → API: export; delete account → Privacy job (≤30 days; R4, R23, R31)" · SR11 Art. 3(1)(a)(d), SR9 Art. 12(3)". §14: "FR-082 … a release gate, not a product story".
2. **Fixed.** support-9.7: "Consent withdrawal · Send photos, voice and text to Google's AI · Completed … with stages: new Analyses refused … queued photo and voice uploads removed … cached private analyses deleted …".
3. **Fixed.** support-9.8: "Export · Running · requested 11:40 your time (10:40 UTC) · 13:40 eater's time" and "Export · Completed 2026-09-20 · download no longer available since 2026-09-27".
4. **Fixed.** support-9.9: "Export · Failed · retried once by Mona K." with no Retry button, offering only "Escalate to the platform admin".
5. **Fixed.** support-9.14: "Use 'Forgot password' on the app's sign-in screen …", then "Open · due by 2026-10-31", then "Escalated to the platform admin · due by 2026-10-31".
6. **Fixed.** support-9.1: "Enter the 6-digit code from your authenticator app", "Email or password is incorrect" with `staff.sign_in_failed`, and "Too many attempts. Try again at 10:47 your time (09:47 UTC)." with `staff.sign_in_locked`.
7. **Fixed.** support-9.21: "Offline — showing data from 11:42 your time (10:42 UTC)". support-10.25: "Couldn't send the request. Nothing was sent to the eater. Try again." The gaps that remain are new defect 9.
8. **Fixed.** support-9.11: "When `staff_ali` opens Jobs with the filter "Escalated", Then the row shows `DEL-26-0905-P7T2`, the Failed stage, due by 2026-10-05 …".
9. **Fixed.**
   - support-9.3: "`support.lookup_rate_limited` with staff `staff_mona`, count 31, window 60 min".
   - support-4.3: "placeholders appear within 300 ms … at 3 s … longer than 10 s".
   - support-10.16: "10 minutes left · the diary closes at 12:20 your time".
   - support-10.15: all three history lines.
10. **Fixed for (a), (b) and (c).**
    - E1 now has its Consent "Given" and "3 of 10 AI analyses used".
    - E2 now has only its deletion.
    - support-10.4 now uses "`staff_lee`".
    - New contradictions are defect 2.
11. **Fixed.** A13: "or opening one of my own Active Grants from the Grants section". support-10.21: "it reads "Look up this account first" (404 `NOT_FOUND`); when she opens `grant_31f0` from Grants instead, then `support.lookup` with method `grant` is recorded".
12. **Partly fixed.** support-10.19 now reads "Ended by support · 16:33", and §0 states the one rule. Several console times still break the rule; see defect 1.
13. **Fixed.** §2: "A tool that cannot over-share also protects the agent (`assumption`)" and "That eaters would dislike not knowing who looked is an `assumption`."
14. **Fixed.** "diary day" no longer appears. Days read "Days 2026-09-28 to 2026-09-30". "diary-day boundary" is kept only as the map's setting name (blueprint §1 ¶6).
15. **Fixed.** The states are D2's (§0.1). The eater's buttons are "Approve" and "Decline", the history says "Mona K. read your Day for 29 Sep", and reads fail with "`GRANT_NOT_ACTIVE` with state …". No `GRANT_REVOKED`, `GRANT_EXPIRED` or `LOOKUP_REQUIRED` remains.
16. **Fixed.** "Diary view" and "Settings → Help" no longer appear. §13 defines "the **Grant panel**" and "the **Grant bar**" and lists "**Privacy help**".
17. **Fixed.** support-9.19 checks "a 1440×900 window and then a 1920×1080 window", "token `--grant-active`", "computed transition and animation durations are 0 s" and "none uses the error colour".
18. **Fixed.** support-9.1: "a banner (not a dialog) reads "You'll be signed out in 2 minutes." with a "Stay signed in" button". §10 names both timers and their warnings.
19. **Fixed.**
    - support-10.3 and support-9.15: "the draft is restored; signing out clears it".
    - support-9.2: "`sb 7kq2 94xm`, `SB7KQ294XM` or ` SB-7KQ2-94XM ` … the field shows `SB-7KQ2-94XM`", plus a `/m` line.
20. **Fixed.** The failed-job stories are now support-3.1, 3.2, 4.1–4.3, 7.1 and 10.24, and K8 asks the map to own the step.
21. **Fixed.** "Shared:" lines and §11 rows were added for 10.8, 10.17–10.20, 9.1, 9.3, 9.9, 9.11, 9.15, 10.2, 10.9, 10.12 and 9.14. One story is still unmarked; see defect 10.

### Defects

1. **Earlier defect 12 is only partly fixed · observable and experience.** §0 says "Every console time reads "HH:MM your time (HH:MM UTC)"", and the fix log says "Every console time was rewritten to the rule." These console lines still break it:
   - support-9.7 gives the stage times in UTC only: "Completed 18:14 UTC; … Completed 18:15 UTC; … Completed 18:20 UTC".
   - support-9.8 gives the eater's time only, with no "your time (UTC)": "download in the app until 2026-10-07 18:09 eater's time".
   - support-10.5 has no UTC: "when it closes on 2026-10-04 11:05 your time."
   - The 12:20 end time has no UTC in five stories:
     - support-10.11: "Diary (read-only) · Days 28–30 Sep · ends 12:20 your time"
     - support-10.16 (twice): "the diary closes at 12:20 your time"
     - support-10.21: "its end time 12:20 your time unchanged"
     - support-10.25 (twice): "until 12:20 your time"
2. **The fixtures contradict each other again · observable.** One seed cannot satisfy all of these lines:
   - (a) `grant_31f0`. support-10.15 says the eater's history "lists exactly three lines". It also says the auditor sees "in order: `grant.requested`, `grant.approved`, `grant.active`, three `grant.read` … and `grant.expired`". Four other stories give the same Grant a different history:
     - support-10.12 adds a `grant.read_denied` event for Day 2026-09-27.
     - support-10.21 records `support.lookup` with method `grant` and then reopens the Diary (read-only) at 10:58 UTC. That is a fourth read.
     - support-10.25 ends the Grant: "the state becomes Ended when it succeeds". Its "until 12:20 your time" is `grant_31f0`'s end. Fixture G1, 10.17 and 10.22 say the Grant Expired at 11:20 UTC.
     - support-10.23 runs `grant_31f0` again with a single read: "requested → approved → active → read → expired".
   - (b) support-9.18 filters the Audit trail "by `staff_mona` and that day" (2026-10-01) and expects "three events". On the same day, the same seed gives `staff_mona`:
     - five failed sign-ins and a lockout (9.1);
     - email look-ups (9.3, 9.14);
     - a retry (9.9) and an escalation (9.11);
     - a logged request (9.15);
     - seven Grants and their reads.
   - (c) support-9.4 shows E1's panel with "Grants: none Requested or Active" and gives no time. On 2026-10-01 E1 has `grant_31f0` Requested or Active from 10:05 to 11:20 UTC, and `grant_31f9` Requested from 11:30 to 11:42 UTC.
3. **Givens that the seed does not hold · observable.**
   - support-9.20: "Given `staff_omar` set Settings → language to Arabic … the Grant bar's countdown fills from the right." `staff_omar` has no Grant in §0.3, and §13 shows the Grant bar only "while the agent has an Active Grant".
   - support-10.20 and support-10.22 need data that §0.3 does not give:
     - 10.20 says "opens a new request with the same reason, Days and areas", but `grant_6c10` has no reason, Days or areas.
     - 10.22 says "Each row shows the eater's account, case, reason, state, and requested, answered and closed times". E10's Grants and `grant_7d01` have no case or reason. `grant_6c10`, `grant_6c14` and `grant_7d01` have no request time.
   - support-9.10: "Sign in with Apple token revoked — Not applicable (email sign-in)". E2's fixture names no sign-in method.
   - support-10.24: "Failed Analyses rows after 09:10 UTC carry the tag "Kill switch On"".
     - No fixture has a Failed Analysis between 09:10 and 09:55 UTC.
     - As written, the line would also tag E5's 16:40 UTC `RATE_LIMITED` row, which came after the switch went Off. The window should be "while the kill switch was On".
4. **support-4.3 against support-7.1 · observable.** For E7, 4.3 says "each failed-job tab … reads "No failed … in the last 30 days"". 7.1 says E7's Activity tab "reads "No Activity imported in 7 days. Missing data is unknown, not proof of no exercise."" That is one tab and one fixture with two texts and two windows. The "…" also leaves 4.3's own text unspecified.
5. **Vague lines · observable.**
   - support-9.11: "escalation resolved by the platform admin at …" (his time, in her time zone, with UTC)". Neither the Given nor the Then gives a time.
   - support-10.7: "the panel suggests the metadata checks to try next" names no check and no text.
   - support-9.1: "5 failed attempts … within 15 minutes, starting at 09:32 UTC … Try again at 10:47 your time (09:47 UTC)". A18 locks the account "for 15 min" after the fifth failure, so 09:47 is right only if all five attempts fall at 09:32. The Given does not say so.
   - support-9.18 `/r`: "When she opens `/console/audit-trail`, Then 403 `FORBIDDEN`". In the browser, 9.16 says the screen reads "You don't have access to this area", but this line names no screen text.
   - support-3.1: "the text (Arabic first) says that the iPhone with the older app shows both versions …". This paraphrases the text. Every other "What to tell the eater" line gives the exact words.
6. **Values with no source and no assumption label · sourced, care group 3.** §10 says "every value with no source is in the assumptions table (A2–A22)". These values are not in it:
   - the error colour from "5 days or fewer" before a deletion's due date (9.19);
   - "Due in n days" for rows due within 5 days (9.15);
   - the 50-row page and "Load 50 more" (4.3);
   - the "120-character outcome note" (9.15);
   - the Grant warnings at 10 and 2 minutes (10.16);
   - "Ask the eater for more time" from 10 minutes before the end (10.20);
   - the look-back windows of 30 days (3.2, 4.3) and 7 days (7.1).
7. **support-10.12 · vocabulary (D2).** D2 defines `FORBIDDEN` as "(role lacks the permission)". 10.12 returns it for reads outside the Grant's Days or areas: "the API returns 403 `FORBIDDEN`", and "`…/activity?day=2026-09-29` or `…/templates` … 403 `FORBIDDEN`". The Support agent role does have the read permission; the refusal is because the Grant does not cover the read. D2's `GRANT_REQUIRED` fits; otherwise a new code needs a delta. K11 lists the lens's other code stretches but not this one.
8. **support-9.11 · vocabulary (one list of states).** §0.1 gives a Privacy job the states "Requested · Running · Completed · Failed", and uses "Escalated to the platform admin" only for "Request received outside the app". 9.11 uses it as a deletion row's status: after the escalation, "the row reads "Escalated to the platform admin · 12:05 your time (11:05 UTC) · Mona K."", and the row no longer shows Failed. Either keep the D2 state and show the escalation beside it, or add the status by a delta.
9. **Unhappy paths still missing · complete, care group 4.** Round 1 added 9.21 and 10.25, but these screens and actions still have no designed state:
   - the Grants list (10.22) has no slow or error state;
   - the Diary (read-only) has no loading state and no screen text when a read fails (10.15 `/s` only says "the read returns 503 and no data");
   - Retry (9.9) and Escalate (9.11, 9.14) have no line for a call that fails while online. 9.21 covers them only offline, and §10's "failures say so (10.25)" covers only Grant actions;
   - Requests received outside the app (9.15) has no empty state and no line for a save that fails, and §10's "Empty" row leaves it out.
10. **A shared story is not marked · lens brief item 5.** support-9.16's acceptance runs on two other personas' sessions: "Given `staff_dina` (Nutrition approver), When she opens `/console/jobs` …" and "`staff_dina` or `staff_ali` (Platform admin) … calls `POST /v1/grants`". It has no "Shared:" line and no row in §11. support-10.9 also sends `staff_ali`'s token but marks only the auditor.

## Fix round 2 (2026-10-01)

Each defect of the re-verify was fixed at its root in the body. Both verdict sections and "Fix round 1" are kept as written. Story ids did not change in this round.

1. **Times (earlier defect 12).**
   - Every console time now follows the §0 rule. The listed lines were rewritten:
     - support-9.7: each stage reads "Completed 19:14 your time (18:14 UTC)" and so on;
     - support-9.8: "download in the app until 2026-10-07 16:09 your time (15:09 UTC) · 18:09 eater's time";
     - support-10.5: "closes on 2026-10-04 at 11:05 your time (10:05 UTC) · 13:05 eater's time", and 10.2 the same;
     - the 12:20 end time now reads "12:20 your time (11:20 UTC)" in 10.11, 10.16 (both warnings) and 10.21. 10.21 now uses `grant_6c10`, so its end time reads "17:02 your time (16:02 UTC)". 10.25 now uses `grant_9b30`, so it reads "19:00 your time (18:00 UTC)".
   - A scan of every story found no "your time" without its UTC time.
2. **Fixtures.** §0.3 now states that one seed holds every line, and gives each Grant its own history:
   - (a) `grant_31f0` has exactly three reads (10:24, 10:25, 10:27 UTC) and two refused attempts after expiry (11:20:01, 11:20:05 UTC). 10.15's auditor sequence now ends with those two `grant.read_denied` events, and the eater's history lists only the three reads. The other stories moved to their own Grants:
     - the out-of-scope reads (10.12) and the write attempts (10.13) to E4's `grant_7d01`;
     - the idle sign-out (10.21) to E10's `grant_6c10`;
     - the offline and failure paths (10.25) to E7's new `grant_9b30`;
     - 10.23 now runs fixture G1 exactly as §0.3 gives it, rather than a second time.
   - (b) support-9.18 now filters by `staff_mona`, account `acct_9c41e2` and 09:00–09:05 UTC. The three seeded events (look-up 09:00, account panel 09:01, Sync tab 09:02) are named: `support.lookup`, plus the new `support.account_viewed` and `support.jobs_viewed` (§0.2). The failed sign-ins moved to `staff_lee` (9.1).
   - (c) support-9.4 is now observed at 10:03 UTC, before `grant_31f0` is Requested at 10:05.
3. **Givens the seed did not hold.**
   - `staff_omar` now has `grant_a1d4` for E6, Active 08:00–09:00 UTC, and 9.20 checks his Grant bar at 08:30.
   - Every Grant in §0.3 now has a reason, Days, areas, duration, case and request time, so 10.20's "same reason, Days, area and case" and 10.22's rows can be observed. 10.22 marks a time that has not happened with "—".
   - E2 now has "email sign-in", which 9.10's "Not applicable" stage needs.
   - E14 gains `an_7740`, Failed while the kill switch was On. 10.24 now tags only rows from 09:10–09:55 UTC, and says that E5's 16:40 UTC row has no tag.
4. **4.3 against 7.1.** 4.3's empty line now covers only Failed Analyses ("No failed Analyses in the last 30 days. Next, check Privacy jobs and Consents.") and Sync ("No sync conflicts in the last 30 days. …"). It points to 7.1 for the Activity tab, whose text is the only one for E7's Activity.
5. **Vague lines.**
   - 9.11: the platform admin retries at 14:10 UTC, and support sees "escalation resolved by the platform admin at 15:12 your time (14:12 UTC)".
   - 10.7: "Declined · 12:42 your time (11:42 UTC). Next: check Sync and Privacy jobs for this account.", with links to both tabs.
   - 9.1: the fifth failure is at 09:32:00 UTC, so the lock to 09:47 follows from A18.
   - 9.18: the screen reads "You don't have access to this area".
   - 3.1: the exact Arabic and English texts are given.
6. **Values with no label.** New assumptions:
   - A23: 5 days for "due soon", used for the error colour and the sort;
   - A24: pages of 50 rows;
   - A25: a 120-character note;
   - A26: warnings at 10 and 2 minutes, and "Ask the eater for more time" offered from 10 minutes before the end;
   - A27: look-back windows of 30 and 7 days;
   - A28: a desk monitor of 1440 px;
   - A29: 200 % zoom.

   Each story that uses one of these values cites it. §10 now says A2–A29.
7. **10.12 code.** A read outside the Grant's Days or areas now returns 403 `GRANT_REQUIRED` ("no Grant covers this read"). `FORBIDDEN` stays only where the role lacks the permission, such as writes in 10.13. K11 records the stretch.
8. **9.11 state.** The escalated deletion row keeps the D2 state: "Deletion · Failed · escalated to the platform admin at 12:05 your time (11:05 UTC) by Mona K.". The escalation is a note beside the state, not a state.
9. **Unhappy paths.** New lines cover:
   - the Grants list when slow or failing (10.22);
   - the Diary (read-only) loading ("Loading 30 Sep…") and a failed read ("Couldn't load 30 Sep. Nothing was shown. Try again.", recorded with outcome `error` and not listed to the eater) (10.25);
   - Retry failing while online (9.9);
   - Escalate failing (9.11, 9.14);
   - the empty state and a failed save of Requests received outside the app (9.15).

   §10's rows for "Empty", "Every action answers", "Errors" and "Long tasks" now name these stories.
10. **Shared stories.**
    - 9.16 is marked shared with the nutrition approver and the platform admin.
    - 10.9 is marked shared with the platform admin as well as the auditor.
    - §11 was rewritten. It also adds 9.9 (eater), 10.1, 10.6, 10.7, 10.17, 10.19 and 10.21 (auditor), and 10.24 (platform admin), and names the eater stories on the other side.

**Checked against `way/vocabulary.md` and the eater's side (`way/personas/eater/wf1-wf9.md`).** Every changed line was re-read against both. Where the eater file is right, or where the two files disagreed without the map deciding, this lens now matches it:
- the requester reads "Mona K. · Support agent";
- the request carries "Never included: photos, voice, your Target and goal settings";
- the approved request reads "Active · ends 14:20";
- the support code reads "Valid until 2 Oct, 09:12", and the local-trial text matches;
- the Unanswered history reads "Unanswered · no access was given";
- the Retry is visible to the eater as "Running" in Settings → Export;
- a Grant request after a deletion returns 404 `NOT_FOUND`, as in eater-9.17;
- the eater's history lists Grant reads only (K1, now aligned);
- under a Grant, the Target is never shown: "Target — not included in Grants" (10.11, 10.14).

Where the eater file differs from the map or from D2, K12 lists each line for the eater lens to align: "view" against "read", "done/waiting" stage words, "re-queue", reused Grant ids, stale story ids, the "No Target" note and the capital of "Entry".

**Counts after the fix:** 52 stories, 190 acceptance lines (149 `/r`, 35 `/s`, 6 `/m`); 10 defects fixed. No owner identifier was sent to any service, and no outside source was opened in this round.

## Lens verdict — re-verify 2 (2026-10-01)

**fail**: 4 defects.

The verifier did not write this lens. It re-read `way/personas/_lens-verifier-brief.md` (with its cross-lens addendum), `way/blueprint.md` §1 (the interaction table, workflows, vocabulary and done-when), `way/vocabulary.md` (delta D2, binding) and `r1-refute-b.md` for R2. It checked each of the 10 re-verify defects against the body, then checked every line that fix round 2 changed (the diff from the re-verify commit to the fix-round-2 commit) for new defects. Lines that round 2 did not touch were not judged again.

These parts hold:
- The counts are right: 52 stories (journey 3: 2 · 4: 3 · 7: 1 · 9: 21 · 10: 25) and 190 acceptance lines (149 `/r`, 35 `/s`, 6 `/m`; by journey 5 · 9 · 2 · 91 · 83), as §14 says. Every story has a `/r` line.
- Every changed Covers line still names a WF step, an interaction row or an FR/AT/NFR line, and every id carries its WF number.
- Every changed time matches SR17's offsets. Examples: 9.8's window ends at 2026-10-07 15:09 UTC, which is 16:09 Dublin and 18:09 Cairo. 10.19's app shows 16:33 Cairo, and 10.25 shows 19:00 Dublin.
- The staff timeline for 2026-10-01 holds:
  - `staff_mona` signs in again at 15:28 and requests `grant_7d01` at 15:29;
  - `staff_lee`'s lock ends at 09:47, before 10.4 (10:10) and 9.21 (10:42);
  - 10.22's eight rows match §0.3 at 16:00.
- Round 2 cites no new outside source. A23–A29 are labelled assumptions. K2 cites R2, which still stands in `r1-refute-b.md` ("may not require users to enable system functionalities").
- No owner identifier was sent to any service.

### The 10 re-verify defects

1. **Fixed.** Every "your time" in the body now carries its UTC time:
   - support-9.7: "new Analyses refused — Completed 19:14 your time (18:14 UTC)".
   - support-9.8: "download in the app until 2026-10-07 16:09 your time (15:09 UTC) · 18:09 eater's time".
   - support-10.5: "when it closes on 2026-10-04 at 11:05 your time (10:05 UTC) · 13:05 eater's time." 10.2 reads the same.
   - The Grant end times:
     - 10.11: "ends 12:20 your time (11:20 UTC)";
     - 10.16, both warnings: "the diary closes at 12:20 your time (11:20 UTC)";
     - 10.21: "its end time "17:02 your time (16:02 UTC)" unchanged";
     - 10.25: "until 19:00 your time (18:00 UTC)".
2. **Fixed.**
   - (a) §0.3 G1 lists "**Refused read attempts after expiry:** 11:20:01 and 11:20:05 UTC" and "Nothing else happens under this Grant".
     - 10.15 ends with "the two `grant.read_denied` attempts of support-10.17 (11:20:01 and 11:20:05 UTC)".
     - 10.23 reads "requested → approved → active → read ×3 → expired → read_denied ×2".
     - 10.12 and 10.13 now run on `grant_7d01`, 10.21 on `grant_6c10`, and 10.25 on `grant_9b30`.
     - Every remaining use of `grant_31f0` fits G1: 9.4, 10.2, 10.4–10.6, 10.10, 10.11, 10.14–10.17, 10.22 and 10.23.
   - (b) support-9.18: "filters the Audit trail by `staff_mona`, account `acct_9c41e2` and 09:00–09:05 UTC, **Then** exactly three events show". The failed sign-ins are now `staff_lee`'s (9.1). New defect 4 is about these three events.
   - (c) support-9.4: "opens its account panel in Jobs at 10:03 UTC" with "Grants: none Requested or Active (`grant_31f0` is requested at 10:05 UTC)".
3. **Fixed.**
   - support-9.20: "his `grant_a1d4` for E6 is Active at 08:30 UTC". In §0.3, E6 reads "Requested 07:55, Active 08:00, Expired 09:00 UTC".
   - support-10.20 and 10.22: every one of `staff_mona`'s Grants in §0.3 has a reason, Days, area, case and request time. For example, `grant_6c10` has "reason "Imported Activity looks wrong"; … case `CASE-1236`; Requested 14:58". 10.22 adds ""—" marks a time that has not happened".
   - support-9.10: E2 now reads "email sign-in".
   - support-10.24: E14 has "`an_7740`, Failed with `AI_UNAVAILABLE` at 09:20 UTC while the kill switch was On". The line reads "only `an_7740` carries the tag "Kill switch On"; rows from outside 09:10–09:55 UTC never do."
4. **Fixed.** support-4.3: "No failed Analyses in the last 30 days. Next, check Privacy jobs and Consents." and "The Activity tab's own empty text is in support-7.1."
5. **Fixed.**
   - support-9.11: "retries the stage with the same id at 14:10 UTC and it Completes at 14:12 UTC", then "escalation resolved by the platform admin at 15:12 your time (14:12 UTC)".
   - support-10.7: "Declined · 12:42 your time (11:42 UTC). Next: check Sync and Privacy jobs for this account.", with links to those two tabs.
   - support-9.1: "`staff_lee`'s five failed sign-ins, the fifth at 09:32:00 UTC". The 09:47 end of the lock follows from A18.
   - support-9.18: "the screen reads "You don't have access to this area"".
   - support-3.1 gives the whole text in both languages. The English reads "Your iPhone with the older app version has two versions of one Entry. …".
6. **Fixed.** A23–A29 are in the assumptions table, and each story that uses one cites it:
   - A23: 9.11, 9.15 and 9.19;
   - A24: 4.3;
   - A25: 9.15;
   - A26: 10.16 and 10.20;
   - A27: 3.2, 4.3 and 7.1;
   - A28 and A29: 9.19.

   §10 reads "(A2–A29)".
7. **Fixed.** support-10.12: "the API returns 403 `GRANT_REQUIRED` (no Grant covers this read)", and K11 records the stretch. 10.13 keeps `FORBIDDEN` "(the Support agent role has no write permission)".
8. **Fixed.** support-9.11: "the row keeps its state and reads "Deletion · Failed · escalated to the platform admin at 12:05 your time (11:05 UTC) by Mona K."".
9. **Fixed.**
   - support-10.22: "Still loading — Cancel" and "Couldn't load your Grants. Try again.".
   - support-10.25: "Loading 30 Sep…" and "Couldn't load 30 Sep. Nothing was shown. Try again.".
   - support-9.9: "Couldn't retry this export. Nothing changed. Try again.".
   - support-9.11 and 9.14: "Couldn't escalate. Nothing was sent to the platform admin. Try again.".
   - support-9.15: "No requests recorded for this account. …" and "Couldn't save this request. Nothing was recorded. Try again.". §10's "Empty" row now names 9.15.
10. **Fixed.**
    - support-9.16: "Shared: Support agent + nutrition approver (`staff_dina`'s session) + platform admin (`staff_ali`'s session)."
    - support-10.9: "Shared: Support agent + platform admin (`staff_ali`'s token) + auditor …".
    - §11 has a row for each. The 25 stories with a "Shared:" line and the 25 rows of §11 match one to one. New defect 1 is a story that should be among them.

### Defects

1. **support-10.25 · a shared story is not marked (lens brief item 5).** Round 2 added this line: "The Audit trail records the attempt as `grant.read` with outcome `error`, and the eater's Grant history does not list it." That acceptance runs on the auditor's Audit trail and on the eater's Settings → Privacy → Grants. Yet 10.25 has no "Shared:" line, and §11, rewritten in round 2, has no row for it. §0 says "'Shared:' names the other personas whose surface a story's acceptance uses". Round 2 marked 10.1, 10.17 and 10.21 for the same kind of line.
2. **§10 against support-10.12 · one string, two texts.** Round 2 moved 10.12 to `grant_7d01`, and its screen now reads "28 Sep is outside this Grant". The §10 row "Say why a command cannot work" still cites ""27 Sep is outside this Grant" (10.12)". No story produces that string.
3. **E1's app language · observable (one seed).** §0.3 now says "One seed holds all of these at once", and E1 is "Arabic, Arabic-Indic numerals". Yet lines rewritten in round 2 expect English copy with Western digits on E1's app, while the same stories' Arabic lines expect Arabic on the same screen:
   - support-9.2 expects "E1's Settings → Privacy → Support code shows `SB-7KQ2-94XM` with "Copy" and "Valid until 2 Oct, 09:12"". Its own later line says "E1's app is in Arabic with Arabic-Indic numerals".
   - support-10.6 expects "what: "Entries and day reports, My Units · 28–30 Sep 2026"". Its Arabic line expects "the Days read «٢٨–٣٠ سبتمبر ٢٠٢٦»". The same holds for "who: "Mona K. · Support agent"", "Never included: …", "The Support agent can read, not change. …" and "the app reads "Active · ends 14:20"".

   10.15 already shows a fix: "in the catalogue's Arabic, whose English keys read". Another fix is an English-language eater. Two unchanged lines about E1's app follow the same pattern and would take the same fix: 10.10 ("Connect to answer this request") and 10.17 ("Expired · 14:20").
4. **support-9.18 against support-9.2 and §0.3 · observable.** 9.18 expects "exactly three events": `support.lookup`, `support.account_viewed` and `support.jobs_viewed`. The seed's order of events is not clear:
   - 9.2 says the look-up at 09:00 opens the panel: "enters `SB-7KQ2-94XM` in Jobs → Look up an account at 09:00 UTC, **Then** E1's account panel opens".
   - §0.3 says "at 09:01 she opened its account panel".
   - §0.2 adds `support.account_viewed` but does not say which action writes it.

   If showing the panel writes the event, the seed gives `support.account_viewed` at 09:00 and again at 09:01: four events, not three. If the panel that the look-up opens writes no event, the lens does not say so. A verifier cannot tell which count is right.

### Cross-lens (for the model phase join; not counted)

- **Eater lens (`way/personas/eater/wf1-wf9.md`).** K12 lists the differences as of the committed eater file: "view" against "read", the "done/waiting" stage words, "re-queue", Grant fixtures reused or moved (`grant_31f0` declined or withdrawn, `grant_40aa` against this lens's `grant_40ab`), stale support ids, "No Target", and "An entry" in lowercase. The eater file was being rewritten in the working tree while this check ran. Its story ids have moved, and some of its lines now differ again: it reads "Active until 14:20" where this lens reads "Active · ends 14:20", and its `grant_40aa` now sits on E10. The join should re-read the eater file as it stands then.
- **Auditor lens (`way/personas/auditor.md`).** Its Audit trail event names differ from this lens's §0.2:
  - it writes `grant.read_refused` and `grant.write_refused`, where this lens writes `grant.read_denied` and has no event for a refused write (E4's 15:45 write attempts in 10.13 name no event);
  - it writes `staff.signed_in`, which this lens does not have.
- **Platform admin, approver and auditor lenses.** K3, K4, K5, K7 and K9 still wait on these lenses to decide who owns the Grant limits, who retries Failed jobs, which role combinations are refused, and who acts for an eater who cannot sign in.

## Diagnosis and fix by the session (2026-10-01)
After two fix rounds, 4 defects remained (re-verify 2). Cause in one sentence: round 2 changed fixtures and screens without re-reading the lines that quote them (the Shared list, §10, the seed's language and its event times). The session fixed the 4:
1. support-10.25 has its "Shared:" line and a §11 row.
2. §10 quotes "28 Sep is outside this Grant" as 10.12 now produces.
3. support-9.2 and 10.6 observe E1's Arabic app in Arabic (Arabic text with Arabic-Indic digits, the English catalogue text given for reference).
4. The seed says the 09:00 look-up opens the account panel once (one `support.account_viewed` at 09:00); 9.18's Given matches, so exactly three events show.

## Lens verdict — final (2026-10-01)

**fail**: 2 defects.

This was the final scoped check, by a verifier that did not write the lens. It re-read `way/personas/_lens-verifier-brief.md` (with its cross-lens addendum) and `way/vocabulary.md` (binding). It then checked the 4 defects of "Lens verdict — re-verify 2" against the session's diff (commit `7159d1d` to `a2cb4c1`). Only the changed lines were checked, each against the stories it touches. Unchanged material was not audited again.

These parts hold:
- The counts are unchanged: 52 stories and 190 acceptance lines (149 `/r`, 35 `/s`, 6 `/m`), as §14 says.
- The 26 stories with a "Shared:" line and the 26 rows of §11 match one to one.
- The session cites no new outside source.
- No owner identifier was sent to any service.

### The 4 re-verify-2 defects

1. **Fixed.**
   - support-10.25 now has "Shared: Support agent + eater (Settings → Privacy → Grants history) + auditor (Audit trail)."
   - §11 has the row "| support-10.25 | eater, auditor | the eater's Grant history does not list a read that ended in an error; the auditor sees that attempt as `grant.read` with outcome `error` |".
   - Both match 10.25's line: "The Audit trail records the attempt as `grant.read` with outcome `error`, and the eater's Grant history does not list it". They also match §0.2: "The eater's Grant history lists only `grant.read` events with outcome `allowed`".
2. **Fixed.** §10 now reads ""28 Sep is outside this Grant" (10.12)". That is the string 10.12 produces: "the Diary (read-only) reads "28 Sep is outside this Grant"". Day 2026-09-28 is outside `grant_7d01`'s Days 2026-09-29 to 2026-09-30 (§0.3 E4). The row's other four strings each exist in the story it names: 9.2, 9.17, 10.18 and 10.25.
3. **Partly fixed.**
   - support-9.2 now reads "E1's Settings → Privacy → Support code on the iOS simulator (E1's app in Arabic) shows `SB-7KQ2-94XM` with «نسخ» and «صالح حتى ٢ أكتوبر، ٠٩:١٢» (English catalogue: "Copy", "Valid until 2 Oct, 09:12")". The digits are right: the code is valid to 2026-10-02 06:12 UTC (§0.3), which is 09:12 in Asia/Riyadh. The month name matches 10.6's «سبتمبر».
   - support-10.6 now reads "the request shows, in E1's Arabic app, the Arabic catalogue form of each line below (the English catalogue text is given; …)".
   - 10.6 also reads "the app reads the Arabic catalogue form of "Active · ends 14:20" (with Arabic-Indic digits «١٤:٢٠»)". 14:20 Riyadh is 11:20 UTC, which matches G1.
   - What remains is new defect 1.
4. **Partly fixed.**
   - §0.3 now reads "At 09:00 UTC she looked up E1 by support code, which opened its account panel (one `support.account_viewed` at 09:00); at 09:02 she opened its Sync tab".
   - support-9.18's Given now reads "`staff_mona`'s look-up of E1 at 09:00, which opened its account panel once, and its Sync tab at 09:02 UTC (§0.3; `support.account_viewed` is written each time an account panel is shown)".
   - Both agree with 9.2 ("enters `SB-7KQ2-94XM` … at 09:00 UTC, **Then** E1's account panel opens"). The count of `support.account_viewed` is now settled. No other story puts `staff_mona` on E1 between 09:00 and 09:05 UTC.
   - What remains is new defect 2.

### Defects

1. **E1's Arabic app is still expected in English or with Western digits (re-verify 2 defect 3 is not finished · observable).** §0.3 gives E1 "Arabic, Arabic-Indic numerals". Re-verify 2 named 10.10 and 10.17 as needing "the same fix", but the session's note names only 9.2 and 10.6. These lines still expect English copy or Western digits on E1's app:
   - **support-9.2**, an unchanged line: "**Given** E1's app is in Arabic with Arabic-Indic numerals, **When** Settings → Privacy → Support code opens, **Then** the code reads `SB-7KQ2-94XM` in Latin characters, left to right, inside the right-to-left screen, with "Copy"." The story's fixed first line expects «نسخ» on the same screen, so the story now gives two texts for one control.
   - **support-10.6**, in the changed line: "E1 opens Settings (badge "1") → Privacy → Grants". That is a Western digit on an Arabic-Indic app, where the badge would read «١».
   - **support-10.10**: "E1's simulator has no network … the cached request shows with Approve and Decline disabled and the line "Connect to answer this request"".
   - **support-10.17**: "**When** E1 opens Settings → Privacy → Grants, **Then** the Grant reads "Expired · 14:20"". §11's row for 10.17 quotes the same text.
2. **support-9.18 · "exactly three events" still depends on an unstated rule for `support.jobs_viewed` (re-verify 2 defect 4 is not finished · observable).** The fix says what writes `support.account_viewed`. Neither §0.2 nor the new Given says what writes `support.jobs_viewed`, and the lens's own lines suggest a fourth event:
   - support-9.19: "**When** E1's account panel is open, **Then** at both sizes the account panel, the Privacy jobs or failed-job tabs, and the Grant panel show as three columns". At 390 px they stack, "account panel → Grant panel → jobs".
   - support-9.21: "**Given** the Privacy jobs API returns 503, **When** that tab loads". support-9.8 reads that tab from `GET /v1/support/accounts/acct_a41c55/privacy-jobs`.
   - 9.18's own `/s` line: "**Given** any `/v1/support/*` request, **When** it completes with 2xx or 4xx, **Then** exactly one Audit trail event is written".

   So the panel `staff_mona` opened at 09:00 also loads a jobs tab with a `/v1/support/*` read, which writes an event, most likely a second `support.jobs_viewed` at 09:00. That would make four events, not three. The count is three only if the jobs column loads nothing until a tab is chosen, or if its first tab comes in the panel's own read. The lens says neither, so a verifier cannot tell which count is right.

### Small slips (not counted; fix them in the next pass)

- §11 puts the new support-10.25 row between support-9.9 and support-9.11. Every other row follows story order.
- 10.25's Shared line names the eater's surface "Settings → Privacy → Grants history". Elsewhere the lens says "the Grant's history" on Settings → Privacy → Grants → that Grant (10.15, which cites eater-9.25) or "Grant history" (§0.2, 10.23 and 10.25's own line). Using one of those, and citing eater-9.25 as 10.15 does, keeps one name for one thing.

### Cross-lens (for the model phase join; not counted)

- **Eater lens (`way/personas/eater/wf1-wf9.md`, uncommitted edits in the working tree during this check).** Its Arabic-app lines for SE1 still expect English text:
  - "it shows `SB-7KQ2-94XM` in Latin letters left to right inside the Arabic screen, with "Copy" and "Valid until 2 Oct, 09:12" (support-9.2)";
  - "the request reads "Active · ends 14:20"";
  - "the line "Connect to answer this request"";
  - "the Grant reads "Expired · 14:20"".

  This lens's 9.2 and 10.6 now expect the Arabic catalogue text with Arabic-Indic digits. At the join, both lenses should use one convention: the Arabic shown, with the English catalogue key given for reference.
- The earlier cross-lens items of re-verify 2 (eater wording and Grant fixtures, auditor event names, and the ownership questions K3, K4, K5, K7 and K9) were not re-checked and still stand as listed.


## Second fix by the session (2026-10-01), after the final check
1. One rule in §0 for an eater's app language: quoted eater copy is the English catalogue text; on an Arabic app (E1) the same key shows Arabic text with Arabic-Indic digits, observed by catalogue key — this covers 9.2 "Copy", 10.6's badge, 10.10 and 10.17 and every other E1 line at once.
2. §0.2 states how events map to requests: opening an account panel is one request (panel + first tab) writing one `support.account_viewed`; opening another tab writes one `support.jobs_viewed`. So 9.18's 09:00 look-up (+ panel) and 09:02 Sync tab give exactly three events.
Also: "Grant history" in 10.25's Shared line; the §11 row for 10.25 moved to story order.


## Lens verdict — closing (2026-10-01)

**pass**: 0 defects. Both defects of the final verdict are fixed. Both of its small slips are addressed. The changed lines break none of the stories they touch.

An independent verifier ran `way/personas/_lens-verifier-brief.md`, with its addendum, as a scoped closing check at commit c0ed7a6. `way/vocabulary.md` was binding (D2, D3). The scope was the diff 1958132..c0ed7a6 (commits 384051e and c0ed7a6):
- §0's new rule for eater-app language;
- §0.2's new paragraph on how events map to requests;
- 10.25's Shared line;
- the moved §11 row;
- the fix note.

The language rule was read against 9.2, 10.6, 10.10, 10.17 and §11's 10.17 row. The event mapping was read against 9.2, 9.4, 9.5, 9.8, 9.18, 9.19, 9.21 and 4.3. Unchanged material was not audited again. The counts were recounted and hold: 52 stories and 190 acceptance lines (149 `/r`, 35 `/s`, 6 `/m`), with 26 "Shared:" lines and 26 §11 rows.

### The 2 final-verdict defects

1. **Fixed.** §0 now reads: "Copy quoted for an eater's app is the English string-catalogue text. On an eater whose app is in Arabic (E1 and every fixture marked Arabic in §0.3) the same catalogue key shows its Arabic text with Arabic-Indic digits; a verifier observes such a line by its catalogue key in that language. Where a story also gives the Arabic text, the Arabic text is the one on screen."
   - **9.2.** The later line's "with "Copy"" now names the key whose Arabic form the first line gives as «نسخ». The story no longer has two texts for one control.
   - **10.6.** The badge "1" shows «١». "Active · ends 14:20" already carries «١٤:٢٠».
   - **10.10.** "Connect to answer this request" is observed in Arabic by its key.
   - **10.17 and its §11 row.** "Expired · 14:20" shows in its Arabic form with «١٤:٢٠». 11:20 UTC is 14:20 in Riyadh, which matches G1.
   - **E6.** It is the only other fixture marked Arabic, and no story quotes E6's app copy, so the rule changes no other line.
   - The rule agrees with D2: "Arabic labels come from the string catalogue, one per English word".
2. **Fixed.** §0.2 now reads: "one `/v1/support/*` request writes exactly one event. Opening an account panel is one request (`GET /v1/support/accounts/{id}`) that returns the panel together with its first tab and writes one `support.account_viewed`; opening any other tab (Sync, Analyses, Privacy jobs, Activity) is one request that writes one `support.jobs_viewed`."
   - **9.18 now counts exactly three events:**
     - the look-up at 09:00 (`GET /v1/support/accounts?support_code=…`, as in 9.2) writes `support.lookup`;
     - opening the panel writes `support.account_viewed`;
     - the Sync tab at 09:02, which the paragraph names as an "other tab", writes `support.jobs_viewed`.
   - The count no longer depends on what the jobs column loads at 09:00.
   - **The paragraph agrees with the stories it touches:**
     - 9.18 /s: "exactly one Audit trail event";
     - 9.21: a 503 from the Privacy jobs API leaves the rest of the panel usable, so that tab has its own request;
     - 4.3: a 503 from the failed-jobs API on each tab;
     - 9.8 /s: `…/privacy-jobs`.

### The 2 small slips of the final verdict

- **Fixed.** The §11 row for support-10.25 now follows support-10.24, in story order.
- **Fixed.** 10.25's Shared line now says "Grant history", the name §0.2, 10.23 and 10.25's own line use.

### Small slips (not counted)

- **§0.2's "its first tab" names no tab.** The parenthesis lists all four tabs (Sync, Analyses, Privacy jobs, Activity) as "other" tabs. 9.18 still holds, because Sync is on that list. What is unstated is which tab opens with the panel without writing a `support.jobs_viewed`.
  - Fix: name that tab and drop it from the list, or say that the jobs column waits until a tab is chosen.
  - If the first tab is Privacy jobs, also check 9.21, which gives that tab its own request.
- **"Analyses" in §0.2.** Everywhere else the tab is "Failed Analyses" (§0.1, 4.3, 10.24, §13).
- **10.25's path skips the Grants screen.** It reads "Settings → Privacy → Grant history", but elsewhere the lens says "Settings → Privacy → Grants" (10.6–10.10, 10.17). "Settings → Privacy → Grants → Grant history", citing eater-9.25 as 10.15 does, would be exact.
- **Seen in passing, outside this scope.** Two unchanged lines predate delta D3 (Consent: Not given → Given · Withdrawn):
  - §0.1's Consent row, "Given · Withdrawn";
  - 9.4's "Consents, each Given or Withdrawn".

  They were not audited here. They are left for the model phase or the next pass.

### Cross-lens (for the model phase join; not counted)

- **Eater-app language.** The final verdict listed lines in `way/personas/eater/wf1-wf9.md` that expect English text on E1's Arabic app. They were not re-checked here. This lens's §0 rule reads quoted English as catalogue keys, and the eater lens can adopt the same rule at the join.
- The earlier cross-lens items still stand as listed.
