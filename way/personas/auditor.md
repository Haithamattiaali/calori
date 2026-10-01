# Auditor — persona lens (privacy reviewer / DPO seat, read-only)

Lens written 2026-10-01 for the /way build of Sips & Bytes, following `way/personas/_lens-brief.md`. Workflows: **WF-10 read side** (Grants, Policy and registry history, roles, reference approvals) and **WF-9 read side** (Consents, Privacy jobs, deletion completion records, retention). Story ids: `auditor-10.n`, `auditor-9.n`.

Read first: `way/blueprint.md` §0–§1, `way/brief/frd-v1.0.md`, `way/research/r1-rules-trends.md` with `r1-refute-b.md` (R34 refuted, R29 decree type doubtful, R18's Cloud Tasks claim dropped; none of these is cited below as standing), `r1-platforms.md` (P4, P11 as corrected, P30), `r1-competitors.md` with `r1-refute-a.md`, the care questions and `way/lessons.md`. No owner identifier was sent to any outside service. Every request in this run used a generic User-Agent.

**Not legal advice.** This file reports what each source says. Counsel decides what applies (FR-082).

---

## 0 · Who

The Auditor is the privacy reviewer or data protection officer. They sit in the admin console with **read-only** rights (blueprint §1.2 and the interaction row "Auditor → trail | read | Audit events | read-only"). They never see an eater's diary. They see the **trail**: who asked for a Grant, whether the eater said yes, every read inside the Grant, and when it ended. They also see Consent changes, Privacy jobs, deletion completion records, Policy and registry versions, and role assignments. Their job is to show a regulator, management, or an eater who complains that access was lawful, documented and limited. They also catch what must never happen before anyone else does.

Hidden persona found by the first map (`way/map-first.md` §2, FR-081/082). FRD §23.2 names the seat: "Privacy/security reviewers approve consent, retention, access, and target-market compliance" (see Conflicts, item 1).

---

## 1 · Research cycle 2 — the Auditor's day in the benchmarks

All sources were opened in this run on **2026-10-01**. Quotes are short. A claim I could not open is labelled `assumption`.

### 1.1 What the three regimes expect: records of processing, access logs, breach evidence

| need | Saudi PDPL / SDAIA | Egypt PDPL 151/2020 | GDPR (if EU storefronts, R30) | what it asks of the Auditor's console |
|---|---|---|---|---|
| **Records of processing (written register)** | Law Art. 31: "the Controller shall maintain records … available whenever requested by the Competent Authority", with purpose, categories, recipients, transfers outside the Kingdom, retention. IR Art. 33: kept "during all the period Personal Data is being processed, and till to five years after the date of end of any Personal Data Processing activity"; "shall be written"; "accurate and up to date"; access for the authority "upon request". IR Art. 20(6): disclosures go into the records with "dates, methods, and purposes" | Art. 4(9): "Holding a specific register for the data including the description of the categories of the Personal Data … the persons to whom such data shall be disclosed … the duration … the mechanism set for erasing … any other data related to the Cross Border Movement … description of the technical and regulatory procedures of the Data Security". Art. 4(12): "Provide the necessary means to prove its compliance … and allow the Center to perform the necessary inspection" | Art. 30(1): name and contact details, purposes, categories, recipients, transfers, "envisaged time limits for erasure", security measures. Art. 30(3)–(4): "in writing, including in electronic form … available to the supervisory authority on request". Art. 5(2): "be able to demonstrate compliance ('accountability')" | a Records of processing view built from live, versioned configuration and exportable on request (auditor-9.15) |
| **Consent evidence** | IR Art. 11(1)(d): "documented through means allowing future verification, such as specifying time and the mean of Consent"; (e) "A separate consent … for each Processing purpose" (R22). IR Art. 12(3): on withdrawal, "cease Processing without undue delay"; 12(4): notify recipients | ER (Decree 816/2025), per the law firm Shalakany: "secure internal electronic records, which must include, inter alia, records of consent, descriptions of personal data processed, and applicable retention periods" (`opened (secondary)`) | Art. 7(1): "the controller shall be able to demonstrate that the data subject has consented"; Art. 7(3): withdrawal "as easy" as giving (R31) | each Consent carries purpose, text version, time, method and app version, plus the effect of a withdrawal (auditor-9.1 to 9.7) |
| **Rights requests and deletion** | IR Art. 3(1)(a): act "within a period not exceeding (30) days", extendable by 30 with advance notice; 3(1)(d): "document and keep record of all received requests including oral requests". IR Art. 8(2)(c): destroy "all copies … including backups" (R23). Law Art. 18(1): data may be kept after its purpose ends only if it "does not contain anything that may lead to specifically identifying Data Subject" | ER per Shalakany: "Requests made by data subjects … must be documented and maintained in accordance with the ER's record-keeping requirements" (`opened (secondary)`) | Art. 12(3): one month; Art. 17(1): erase "without undue delay"; Art. 17(3)(b), (e): no erasure where processing is needed for "compliance with a legal obligation" or "legal claims" | Privacy jobs on a 30-day clock; a deletion completion record without identifiers; trail events kept under a pseudonymous subject key (auditor-9.9 to 9.13) |
| **Access logs for health data** | Law Art. 23(1): "Restricting the right to access Health Data … to the minimum number of employees". IR Art. 26(3): "taking into account different level of access to data among employees". IR Art. 26(4): "Document all stages of Health Data Processing and provide the means to identify the person in charge for each stage" (R24) | Art. 4(6): technical and regulatory measures to "avoid any Personal Data Breach, damage, alteration or manipulation" | Art. 32(1)(b): "ongoing confidentiality, integrity" of processing systems | every read inside a Grant names the staff member and what was read; reads outside a Grant are impossible and are logged when attempted (auditor-10.3 to 10.12) |
| **Breach evidence** | IR Art. 24(1): notify "within a delay not exceeding (72) hours of becoming aware", with "time, date, and circumstances … actual or approximate numbers of impacted Data Subjects". IR Art. 24(3): "keep a copy of the reports … and document the corrective measures". SDAIA *Personal Data Breach Incidents Procedural Guide* (Issue 1.0, Oct 2024), Stage Three: "retain copies of the documents submitted to SDAIA … the corrective actions taken, and any relevant proper records" | Art. 7: report "within seventy two hours", including "the approximate number of Personal Data affected"; data subjects notified "within three business days as of the date of reporting". ER per Shalakany: breach "documented in a secure digital record … nature of the breach, its potential impact, and remedial measures" | Art. 33(1): 72 hours "where feasible"; 33(3)(a): "approximate number of data subjects"; 33(5): "The controller shall document any personal data breaches, comprising the facts … its effects and the remedial action taken" | scope a suspected incident by actor and time window in minutes, with a count of distinct subjects affected and a verifiable export (auditor-10.33). A breach *record* has no home in the map (Conflicts, item 2) |
| **The DPO seat itself** | IR Art. 32(3): DPO tasks include "audit and control reporting", "Notifying the Competent Authority of Personal Data Breach incidents", "Monitoring and updating the records of personal data processing activities". DPO Rules (v1.0, Aug 2024) Art. 8(4): "Preparing periodic reports regarding Controller activities related to processing of Personal Data"; Art. 9(5): "shall not assign tasks that may conflict with DPO tasks or affect DPO's independence". R25: a DPO is required here | Art. 9(1): "Performing a regular evaluation and inspection of the Personal Data protection system … and approving the results of such evaluation"; 9(6): "Monitoring the registration and the update of the Personal Data register" | Art. 38(6): other tasks must "not result in a conflict of interests" | a periodic report (auditor-10.34); the Auditor role cannot be combined with a write role (auditor-10.24) |

Dates and status: the KSA IR, the law and the DPO Rules were read as SDAIA's official PDFs (law as amended by M/148, 1444H; DPO Rules "Version 01, August 2024"). The breach guide is "Issue No. 1.0, October 2024". The Egypt law was read in Sharkawy & Sarhan's Arabic–English dual text; the official PDPC host is unreachable (r1-refute-b). On the Egyptian Executive Regulations, Shalakany says "the Minister of Communications and Information Technology issued the executive regulations … by virtue of Decree No.816 for the year 2025 … published in the Official Gazette on November 1st, 2025 … grace period of 1 year … (i.e. 1 November 2026)". This matches the corrected R29 ("issued 1 Nov 2025, grace period strictly to 1 Nov 2026, possibly treated as end-2026"). I did not open the ER text itself, so every ER detail above is `opened (secondary)`.

### 1.2 What good audit tools do, and where reviewers struggle with them today

| benchmark | what it does | lesson for us |
|---|---|---|
| **Google Cloud Access Approval** (docs, last updated 2026-09-24) | "require your explicit approval whenever they need to access your Customer Data … Active access approval requests may be revoked at any time … a historical view of all requests that were approved, dismissed, revoked, or expired" | the same shape as our Grant: the data owner approves, can revoke, and every outcome stays visible, including a request that **lapsed** unanswered (auditor-10.10, 10.11) |
| **Google Cloud Access Transparency** (2026-09-24) | "log entries include details such as the affected resource and action, the time of the action, the reason for the action, and information about the accessor" | each read inside a Grant names the resource, time, reason (from the Grant) and accessor (auditor-10.5) |
| **Google Cloud Audit Logs** (2026-09-30) | "Log entries written by Cloud Audit Logs are immutable." Admin Activity logs "are always written; you can't configure, exclude, or disable them". "Except for BigQuery, Data Access audit logs are disabled by default … you must explicitly enable them." | our trail is append-only, and no role can switch it off (auditor-10.15). Infrastructure-level reads of Firestore and Cloud Storage are invisible unless Data Access logs are turned on at hosting (Conflicts, item 6) |
| **GitHub organisation audit log** (docs, undated) | filters by `actor:`, `action:`, `operation:`, `created:` (ISO 8601 with "a UTC offset ( +00:00 )"); "you cannot search for entries using text"; "By default, only events from the past three months are displayed"; export "as JSON data or … CSV"; the export follows the current filters; hard limits of "100 MB compressed file, or 10 minutes export processing time"; 180 days kept | structured filters that state themselves, explicit offsets, and an export that equals the filtered view (auditor-10.25, 10.30) |
| **Microsoft Purview audit export** (ms.date 2026-06-19) | the CSV has "a column named AuditData … formatted as a JSON object", which the reviewer must split with Excel's Power Query editor. Beyond the export limit, "the exported .csv file doesn't include all results and might omit some audit logs" | today's pain: details buried in a JSON column and silent truncation. Ours: one column per field, and never a silently partial file (auditor-10.30, 10.31) |
| **OWASP Logging Cheat Sheet** (undated, current) | record "when, where, who and what"; separate event time from logging time for devices that are "only periodically or intermittently online"; exclude "Sensitive personal data … e.g. health"; "Build in tamper detection"; "All access to the logs must be recorded and monitored"; always log "Authorization (access control) failures", "Data import and export including screen-based reports" and "personal data usage consent"; "It should not be possible to completely deactivate application logging" | the event's fields, the denied attempts, the Auditor's own exports, and the hash chain (auditor-10.5, 10.14, 10.35, 9.6) |
| **NIST SP 800-53 Rev 5.2.0** (OSCAL catalogue, last modified 2026-05-11) | AU-3: "What type of event … When … Where … Source … Outcome … Identity"; AU-7(b): report generation that "Does not alter the original content or time ordering of audit records"; AU-9: protect audit information "from unauthorized access, modification, and deletion"; AU-9(4): access by a subset of privileged users; AU-10: "irrefutable evidence"; AC-2(7)(b)–(c): "Monitor privileged role or attribute assignments … changes to roles"; AC-5: separation of duties | the event schema, an export that preserves order, read access only for the Auditor role, the role-history view and separation-of-duties checks (auditor-10.22 to 10.24) |
| **ICO, personal data breaches guide** (UK GDPR; page updated after 20 Aug 2025) | "You must also keep a record of any personal data breaches, regardless of whether you are required to notify"; notify within 72 hours "even if we do not have all the details yet" | evidence is needed even for breaches that are never notified (Conflicts, item 2) |
| **AccessOwl, user access reviews** (vendor blog, 2026-03-19) | "manual exports from each app, consolidating spreadsheets"; "Don't forward the raw export … reviewers default to 'looks fine'"; "record who reviewed it, when, what they decided … and why. Keep the original export too"; "That cover sheet is what the auditor reads first"; "We run our reviews quarterly" | the console gives context instead of raw rows, and gives the periodic report a summary first page (auditor-10.34). The reviewer's sign-off needs a write (Conflicts, item 3) |

### 1.3 The Auditor's day, the place, the moments of trust

- **Tasks they repeat.** A daily glance at integrity and anomalies (`assumption`: the cadence is ours; NIST AU-6 leaves the frequency to the organisation). Periodic review of Grants and roles: AccessOwl reports quarterly reviews, and SDAIA DPO Rules Art. 8(4) require "periodic reports". Checking that rights requests close within 30 days (IR Art. 3). Keeping the records of processing current (IR Art. 32(3)(f), Art. 33(3); Egypt Art. 9(6)).
- **Event-driven tasks.** A regulator asks for records "upon request" (IR Art. 33(4); GDPR Art. 30(4); Egypt Art. 4(12)). An eater complains: in KSA, a complaint may reach SDAIA "within a period not exceeding (90) days from the date when the incident occurred or when the Data Subject became aware of it" (IR Art. 37(1)), so the trail must answer questions about events at least months old. A suspected breach starts a **72-hour** clock (KSA IR Art. 24(1), Egypt Art. 7, GDPR Art. 33(1)). The KSA and GDPR texts count hours, with no business-day carve-out. Egypt alone gives "three business days" for telling data subjects. The clock runs through weekends and holidays.
- **Where and on what.** At a desk, in a browser, with keyboard and mouse, often with a spreadsheet open beside it. That is the benchmarks' workflow: CSV and JSON exports (GitHub, Microsoft, AccessOwl). Sometimes a phone, to check one anomaly away from the desk; the profile proves the console at ~390 px (`assumption` for the phone habit). Network: office broadband (`assumption`). Large exports run as background jobs because the benchmarks hit size and time limits (GitHub: 100 MB / 10 min).
- **Language.** The console is English and Arabic, right-to-left (profile §0 line 5). The texts I read were SDAIA's English PDFs and a law firm's Arabic–English dual text for Egypt. That the Arabic texts prevail is an `assumption`; none of the opened documents says so. Whether a regulator wants Arabic column labels in an export is an `assumption`; machine keys stay stable either way.
- **What they use today and dislike** (inferred from the documented workarounds). Details hidden in a JSON column that needs Power Query. Exports silently cut at a row limit. Default views that hide older events. No free-text search. Spreadsheets stitched from many exports. Reviewers who "default to 'looks fine'" when given rows without context.
- **The moments that decide trust.** (1) The export handed to a regulator matches the screen exactly and can be verified: row count, hash, filters, as-of point. (2) An anomaly is specific and real: a rule, a count and the events behind it, never noise. (3) Under the 72-hour clock, scoping "which eaters, how many" takes minutes, not a data-engineering ticket. (4) The trail itself cannot be edited, by anyone, including the platform admin.

### 1.4 Findings carried from research cycle 1 (as corrected by the refuters)

R2 (third-party AI named in consent), R3 (withdrawal easy; no paywall), R4 (in-app deletion; Sign in with Apple tokens revoked), R7 and P30 (an app cannot tell whether Health read access was denied), R20–R25 (Saudi scope, sensitive health data, consent, destruction incl. backups, 72 h, DPIA, health controls, DPO), R26 (transfers), R28 (Egypt), R29 as corrected, R30–R31 (GDPR), P4 (floating `-latest` aliases exist), P11 as corrected ("Endpoints don't guarantee data residency"; Gemini 3.8 Flash only on `global` and the US/EU multi-regions; no Gemini model in any Middle East region).

---

## 2 · Goals

1. **G1 — No diary access without the eater's yes.** Prove that every staff read of a diary happened inside a Grant the eater approved, within its time box, and that every other attempt was refused and logged (FR-081; KSA Law Art. 23(1), IR Art. 26(3)–(4)).
2. **G2 — Consent that can be proved.** Show each Consent per purpose, with its text version, time, method and the effect of a withdrawal (IR Arts. 11–12; GDPR Art. 7; FR-076, FR-079).
3. **G3 — Deletions that really finished.** Show each deletion completed everywhere within 30 days, leaving a completion record without identifiers (FR-078, NFR-13, AT-29; R4, R23, R31).
4. **G4 — Who changed the rules.** Show who changed Policy, the registry and roles, when and why, and which version was in force at any moment (FRD §3.3, §16.4, FR-080/081; NIST AC-2(7)).
5. **G5 — A regulator gets a complete, verifiable extract quickly.** (IR Art. 33(4); GDPR Arts. 30(4), 33(5); Egypt Art. 4(12).)
6. **G6 — What must never happen is checked continuously, not when someone remembers.**

---

## 3 · Journeys (high to low)

### Journey 10 — Review who touched diaries and who changed the rules (WF-10, read side)

| step | what the Auditor does | stories |
|---|---|---|
| 10-A Arrive | sign in; Trail overview: chain integrity, open anomalies, active Grants | 10.1 |
| 10-B Grants | list Grants; open one; read its request, the eater's decision, every read, its end; declined, lapsed, revoked, self-requested | 10.2–10.12 |
| 10-C Never-events | the Anomalies page; chain verification; read-only guarantees; refusal for other roles | 10.13–10.16 |
| 10-D Versions | Policy history and diff, in force at a moment; registry history; kill switch; reference approvals | 10.17–10.21 |
| 10-E Roles | holders now; history and held-at; separation of duties | 10.22–10.24 |
| 10-F Find and hand over | filter and search; invalid input; time; large and slow results; offline; export; very large export; subject lookup; breach scoping; quarterly report; own activity | 10.25–10.35 |
| 10-G Fit | Arabic RTL; phone width; keyboard, screen reader, zoom | 10.36–10.38 |

### Journey 9 — Review consent and deletion evidence (WF-9, read side)

| step | what the Auditor does | stories |
|---|---|---|
| 9-A Consents | per purpose; one subject's history; the exact text version; one purpose per record; withdrawal effect; retries and offline time; Health honesty; raw-evidence access for quality review | 9.1–9.8, 9.17 |
| 9-B Privacy jobs | the 30-day clock; deletion completion record; a failing step; a deleted subject in the trail; an eater's export job | 9.9–9.13 |
| 9-C Retention and records | retention runs; Records of processing; consent evidence export | 9.14–9.16 |

### Console sections (names proposed; the map names no console pages)

**Trail · Grants · Anomalies · Versions (Policy, Registry, Reference) · Roles · Consents · Privacy jobs · Records of processing · Reports**. These are proposals for the model phase to fix once in the string catalogue. Admin API paths below (`/v1/admin/…`) are also proposed, in the style of FRD §18.

### The audit event (the inside, proposed from AU-3 and OWASP)

Every event carries: `event_id`, `sequence` (gap-free), `occurred_at_utc` (event time), `received_at_utc`, `actor` (staff id with their role at that moment · subject key for the eater · job name for the system), `action` (for example `grant.requested`, `grant.approved`, `grant.declined`, `grant.lapsed`, `grant.read`, `grant.read_denied`, `grant.revoked`, `grant.expired`, `consent.given`, `consent.withdrawn`, `privacy_job.export.*`, `privacy_job.deletion.*`, `retention.run`, `policy.version.approved`, `registry.version.promoted`, `registry.kill_switch.on|off`, `food.version.approved`, `alias.approved`, `role.assigned|removed`, `evidence.viewed`, `subject.lookup`, `trail.queried`, `trail.exported`, `access.denied`), `object` (Grant id, Policy version, registry version, Privacy job id, Consent id, role), `subject_key` (pseudonymous, when an eater is involved), `outcome` (Allowed · Denied · Done · Failed) with `reason_code` (for example `GRANT_EXPIRED`, `GRANT_NOT_APPROVED`, `GRANT_REVOKED`, `NO_ACTIVE_GRANT`, `ROLE_FORBIDDEN`, `SELF_GRANT_FORBIDDEN`, `CONSENT_REQUIRED`), `request_id`, `surface` (app · console · API · job) with app version, `prev_hash`, `hash`. **Never**: food names, quantities, calories, photos, audio, profile values, eater names or emails (FRD §19.2; OWASP "Data to exclude").

### Fixtures (synthetic; used by every acceptance line below)

| fixture | value |
|---|---|
| subject keys | `subj_7Q2M` (main eater) · `subj_9K4D` (declined and lapsed Grants) · `subj_3T8W` (the eater account linked to staff S-11) · `subj_2W6P` (deleted, DEL-0012) · `subj_5H1C` (research Consent given) · `subj_8N2R` (no research Consent) |
| staff | S-07 Support · S-11 Support until 2026-09-25 (also eater `subj_3T8W`) · A-02 Nutrition approver · A-05 Nutrition approver (qualified reviewer) · P-01 Platform admin · U-19 Support **and** Platform admin (seeded violation) · AU-01 Auditor |
| Grant G-2026-0042 | `subj_7Q2M`; requested by S-07 2026-09-28T09:00:00Z, 60 min, reason "Day total on 27 Sep looks doubled after offline sync"; approved by the eater 09:04:12Z in Settings; reads 09:10:41Z (Day report 2026-09-27), 09:12:03Z (Entry history 2026-09-27, 7 Entries), 09:31:55Z (Day report 2026-09-26); expired 10:04:12Z; read attempt 10:06:30Z denied |
| Grant G-2026-0043 | `subj_9K4D`; requested by S-07 11:15:00Z; declined 11:20:05Z; read attempt 11:25:00Z denied |
| Grant G-2026-0044 | `subj_7Q2M`; requested 13:55:00Z; approved 14:00:00Z for 60 min; read 14:05:00Z; revoked by the eater 14:20:00Z; read attempt 14:21:00Z denied |
| Grant G-2026-0045 | `subj_9K4D`; requested by S-07 13:00:00Z; never answered → lapsed |
| Grant G-2026-0046 | `subj_7Q2M`; requested by S-07 2026-09-29T08:00:00Z; the eater's approve command delivered 3 times; no reads |
| Policy | v7 live from 2026-09-01T00:00:00+03:00 · v8 reviewed by A-05 2026-09-29T07:30:00Z, approved by A-02 08:00:00Z, effective 2026-10-05T00:00:00+03:00; change: energy-mismatch threshold ">10 % and >10 kcal" → ">12 % and >10 kcal" (synthetic); unchanged: calorie floor 1,200 kcal, hard stop 1,000 kcal, raw scans 30 days, audio 24 h |
| registry | R-14 the prior Rollout version · R-15 `gemini-3.8-flash` for image/recipe, prompt p-12, schema s-4, Canary 5 % from 2026-09-25T08:00:00Z · R-16 same ids, Rollout 100 % 2026-09-27T08:00:00Z by P-01 · kill switch On 10:12:00Z, Off 10:47:00Z by P-01 |
| Consents | `subj_7Q2M`: "Send photos, voice and text to Google's AI (Gemini)" c-ai-3 given 2026-09-01T18:22:10Z (onboarding), withdrawn 2026-09-20T07:45:00Z (Settings), c-ai-4 given 2026-09-25T20:10:00Z (Settings); "Optional research" withdrawn offline on the device 2026-09-21T22:40:00Z, received 2026-09-22T06:05:30Z, delivered twice |
| Privacy jobs | EXP-0031 export (`subj_7Q2M`) · DEL-0007 done in 14 days · DEL-0009 open day 26 · DEL-0010 open day 31 (seeded overdue) · DEL-0011 one step retrying · DEL-0012 (`subj_2W6P`) done |
| trail | 1,248 events, sequence 1–1,248, chain intact; console time zone Asia/Riyadh (+03:00) |

---

## 4 · Micro stories with acceptance

Layer tags: **/m** module test · **/s** system (API + storage, on the emulator) · **/r** runtime, observable in the served admin console in a browser or over the API by HTTP.

### Journey 10 — WF-10, read side

**auditor-10.1 · Trail overview on arrival**
As the Auditor, I land on the Trail overview when I sign in, so that in seconds I know whether the trail is intact, how many anomalies are open and how many Grants are active.
- /r Given the seeded trail at 2026-09-28T14:10:00Z (1,248 events, chain intact, G-2026-0044 active, 1 open anomaly: U-19 holds Support and Platform admin) When AU-01 signs in to the admin console Then the first screen is **Trail**, with "Intact through event 1,248 · checked 14:10:00Z (17:10:00 +03:00)", "Active Grants 1" and "Anomalies 1", each a link to its list, and no edit, delete, approve, assign or request control anywhere.
- /r Given the trail API is unreachable When AU-01 opens Trail Then the page says "The trail could not be loaded. Nothing is shown rather than a partial trail." with **Retry**, and no count is shown as 0.
- /r Given the first second of loading When Trail is requested Then a skeleton of the header and table appears, never a blank page or zero counts.

**auditor-10.2 · Grants list**
As the Auditor, I list Grants with state, requester, subject key, window and read counts, so that I can review all diary access in a period at a glance.
- /r Given G-2026-0042…0045 When AU-01 opens **Grants** for 2026-09-28T00:00+03:00 – 2026-09-29T00:00+03:00 Then 4 rows appear, newest first, each with id, state (Expired · Declined · Revoked · Lapsed, as a word plus an icon, never colour alone), requested by "S-07 · Support", subject key, requested duration, active window, reads allowed and reads denied.
- /r Given a range with no Grants When the list loads Then it says "No Grants were requested between 1 Sep 2026 00:00 and 2 Sep 2026 00:00 (+03:00)", shows the range, and offers **Widen to the last 90 days**.

**auditor-10.3 · One Grant's whole life on one page** *(shared: support agent requests and reads; eater approves in Settings — WF-10 done-when)*
As the Auditor, I open a Grant and see request, decision, every read and its end in one timeline, so that I can show the eater opened the door and staff stayed inside the time box.
- /r Given G-2026-0042 When AU-01 opens Grants → G-2026-0042 Then one timeline shows, in order: Requested (S-07, 09:00:00Z) · Approved by the eater (09:04:12Z) · Read ×3 (09:10:41Z, 09:12:03Z, 09:31:55Z, each Allowed) · Expired (10:04:12Z) · Read denied (10:06:30Z, GRANT_EXPIRED). Every time is shown in UTC and +03:00. The header says "Active window 09:04:12Z–10:04:12Z (60 min) · 3 reads allowed · 1 denied".
- /s Given the same Grant When `GET /v1/admin/grants/G-2026-0042` is called with AU-01's token Then it returns the same 6 events with the same event ids and sequence numbers that Trail shows for `object = G-2026-0042`.
- /s Given the support and eater flows on the emulator When S-07 requests, the eater approves, S-07 reads three times and the clock passes expiry Then exactly those 6 trail events exist, with no others for that Grant.

**auditor-10.4 · Why support asked, as the eater saw it**
As the Auditor, I read the request's reason, duration and scope exactly as the eater saw them, so that I can judge whether the access was necessary and proportionate.
- /r Given G-2026-0042 When AU-01 opens its Request card Then it shows the reason verbatim ("Day total on 27 Sep looks doubled after offline sync"), 60 min, scope "diary, read-only", "S-07 · Support (role at the time)", and "Shown to the eater as:" with the exact request wording version, in the language the eater saw (en or ar).
- /s Given `grant.requested` is stored When the request wording is later revised Then the event keeps the wording version id it was shown with, and the console renders that version, not the current one and not a re-translation.

**auditor-10.5 · What was read, never the food**
As the Auditor, I see what each read touched without seeing the diary, so that I can account for access without becoming a second reader of health data.
- /r Given the read at 09:12:03Z When AU-01 expands it Then it shows "Entry history · Day 2026-09-27 · 7 Entries returned · Allowed · request id …", with no food names, quantities, calories, photos or audio, and the note "Content is not kept in the trail".
- /m Given the audit-event builder and a read of a Day whose Entries include "cheese bite ×3, 50 kcal" When it builds the event Then the stored event has only allow-listed fields, and no field holds "cheese", "kcal" or any Entry value (FRD §19.2; OWASP "Data to exclude").

**auditor-10.6 · Only the eater can approve**
As the Auditor, I see who made each decision, from where and when, so that I can prove the eater, not staff, said yes.
- /r Given G-2026-0042 (approved) and G-2026-0043 (declined) When AU-01 opens each Then the Decision card reads "Approved by the eater · in the app, Settings · 09:04:12Z · app 1.0.0, iOS 26" or "Declined by the eater · 11:20:05Z", and the actor is the subject key, never a staff id.
- /r Given any staff token (S-07, P-01, U-19) When it calls the approve endpoint for G-2026-0045 Then the API returns 403 ROLE_FORBIDDEN, the Grant stays unanswered, and the trail gains `access.denied` naming that staff id (FR-081; blueprint persona table).
- /r Given G-2026-0046's approve command was delivered 3 times (the AT-10 retry pattern) When AU-01 opens G-2026-0046 Then exactly one "Approved" event shows.

**auditor-10.7 · A declined Grant opens nothing**
As the Auditor, I see that a declined Grant gave no access, so that "no" is provably final.
- /r Given G-2026-0043 declined at 11:20:05Z When S-07 requests `subj_9K4D`'s Day report with G-2026-0043 at 11:25:00Z Then the API returns 403 GRANT_NOT_APPROVED with no diary data, and the Grant detail shows "Read denied · Grant declined · 11:25:00Z"; reads allowed stays 0.

**auditor-10.8 · A read after expiry is impossible**
As the Auditor, I see any read after expiry refused and recorded, so that the time box is a fact, not a promise.
- /r Given G-2026-0042 expired at 10:04:12Z When S-07 calls the Day report for `subj_7Q2M` with that Grant at 10:06:30Z Then the API returns 403 GRANT_EXPIRED with no diary data, and the timeline shows "Read denied · Grant expired · 10:06:30Z".
- /s Given the window is half-open [start, end) When a read arrives at exactly 10:04:12.000Z Then it is denied, and at 10:04:11.999Z it is allowed.
- /r Given the Anomalies page When opened Then "Diary reads allowed after Grant expiry" shows 0, and "Read attempts after expiry (refused)" shows 1, linked to the 10:06:30Z event.

**auditor-10.9 · No read outside a Grant**
As the Auditor, I see that no staff read of a diary happened without an active Grant, so that I can answer "did anyone else look?" with evidence.
- /r Given no active Grant for `subj_8N2R` When, on 2026-09-30, S-07, P-01 or A-02 calls any diary, report or media endpoint for `subj_8N2R` Then each returns 403 NO_ACTIVE_GRANT and a `grant.read_denied` event with the actor's staff id appears in Trail.
- /s Given the NFR-07 negative-test suite When it walks every diary, report and media endpoint with each staff role and no Grant Then every call is refused, and every refusal has one trail event.
- /m Given the anomaly rule "allowed staff diary reads without an active Grant" and a synthetic trail containing one Allowed `grant.read` whose Grant was not active When evaluated Then it returns that event id. (The rule is proven to catch it; the live count stays 0.)

**auditor-10.10 · An unanswered request lapses**
As the Auditor, I see requests the eater never answered end as Lapsed, so that silence is never taken as consent.
- /r Given G-2026-0045 requested at 13:00:00Z and not answered within the lapse time set in the live Policy version When that time passes Then the detail shows "Lapsed · no answer from the eater · Policy v7 lapse time", and a read with it returns 403 GRANT_NOT_APPROVED.

**auditor-10.11 · The eater revokes mid-window** *(shared with eater)*
As the Auditor, I see a revocation end access at once, so that the eater's change of mind is honoured to the second.
- /r Given G-2026-0044 approved 14:00:00Z for 60 min and revoked by the eater at 14:20:00Z When S-07 reads at 14:21:00Z Then the API returns 403 GRANT_REVOKED, and the detail shows "Revoked by the eater · 14:20:00Z · used 20 of 60 min · 1 read allowed · 1 denied".

**auditor-10.12 · A staff member cannot ask for their own diary**
As the Auditor, I see that a staff member who is also an eater cannot request a Grant on their own account, so that no one approves their own request (blueprint persona table).
- /r Given S-11's sign-in identity is linked to eater `subj_3T8W` When S-11 requests a Grant on `subj_3T8W` (2026-09-22T10:00:00Z) Then the API returns 403 SELF_GRANT_FORBIDDEN, no Grant is created, and Anomalies shows "Self-requested Grants (refused): 1", linked to the `access.denied` event.

**auditor-10.13 · Anomalies: what must stay at zero**
As the Auditor, I open Anomalies and see each never-event rule with its count and the events behind it, so that I check them continuously instead of by memory.
- /r Given the seeded trail on 2026-10-01 When AU-01 opens **Anomalies** Then each rule shows its count and "last evaluated" time: reads allowed after expiry 0 · reads allowed without a Grant 0 · Grants approved by anyone but the eater 0 · trail chain breaks 0 · Support held with Platform admin or Nutrition approver 1 (U-19) · Auditor held with a write role 0 · roles assigned by their receiver 0 · deletions open > 30 days 1 (DEL-0010) · AI requests after the AI Consent was withdrawn 0 · raw scans older than 30 days 0 · audio older than 24 h 0 · Live Policy versions without a recorded review 0 · registry versions naming a floating alias 0 · Consent records covering more than one purpose 0 · Consent records with missing text versions 0 · raw evidence viewed without the research Consent 0. Refused attempts are listed separately as information, not as anomalies.
- /r Given one rule's evaluation failed When Anomalies loads Then that row reads "Not evaluated — check failed at 14:10:00Z", never 0.
- /m Given each rule and a synthetic trail that violates it once When evaluated Then each rule returns exactly that event id.

**auditor-10.14 · The trail cannot be altered without it showing**
As the Auditor, I verify the trail's hash chain, so that I can tell a regulator nothing was changed or removed.
- /m Given 1,248 events where `hash(n) = SHA-256(hash(n−1) ‖ canonical(event n))` When event 600's outcome is changed in storage Then `verify()` reports "first break at event 600".
- /r Given the intact trail When AU-01 selects **Verify chain** on Trail Then progress shows "Checking 1,248 events…" with **Cancel**, and ends with "Intact · events 1–1,248 · 14:11:10Z".
- /r Given the emulator fixture with event 600 tampered When verified Then the Trail header shows "Chain broken at event 600", and Anomalies shows "Trail chain breaks 1".

**auditor-10.15 · Read-only, for everyone**
As the Auditor, I know no one, including me and the platform admin, can edit or delete an audit event, so that the trail is evidence.
- /r Given AU-01's session When AU-01 opens any event, Grant, version or role page Then no edit, delete, approve, assign or request control exists.
- /r Given AU-01's or P-01's token When `PUT`, `PATCH` or `DELETE /v1/admin/audit/events/1201` is sent Then the API returns 405, an `access.denied` event names the caller, and Verify chain stays intact.
- /s Given the storage rules on the emulator When any client identity writes to an existing audit-event document Then the write is refused; only the server's append path can add events (AU-9; OWASP).

**auditor-10.16 · Other roles cannot read the trail**
As the Auditor, I know only the Auditor role reads the trail, so that the record of who looked is not itself browsed freely (AU-9(4)).
- /r Given S-07 (Support) is signed in When S-07 opens `/trail` Then the page says "The trail is open to the Auditor role. Your roles: Support." with a way back, `GET /v1/admin/audit/events` returns 403, and the attempt appears in Trail as `access.denied` by S-07.

**auditor-10.17 · Policy history, diff and review**
As the Auditor, I see every Policy version with who reviewed, who approved, what changed and when it takes effect, so that I can show safety rules change only with qualified review (FRD §3.3).
- /r Given v7 and v8 When AU-01 opens **Versions → Policy** Then rows show version, state (Live · Scheduled · Superseded), reviewed by A-05 at 07:30:00Z, approved by A-02 at 08:00:00Z, effective from 2026-10-05T00:00:00+03:00, and the change summary.
- /r Given v7 and v8 When AU-01 selects **Compare** Then only the changed field shows, "energy-mismatch threshold >10 % and >10 kcal → >12 % and >10 kcal"; the unchanged fields (calorie floor 1,200 kcal, hard stop 1,000 kcal, …) sit under "Unchanged (n)".
- /r Given any Live Policy version When opened Then its Review card shows reviewer, role and time; Anomalies "Live Policy versions without a recorded review" is 0.

**auditor-10.18 · Which Policy was in force at a moment**
As the Auditor, I ask which Policy version applied at a given time, so that I can answer "what floor applied to this person on 15 September?".
- /r Given v7 effective 2026-09-01T00:00:00+03:00 and v8 effective 2026-10-05T00:00:00+03:00 When AU-01 enters "In force at" 2026-09-15T12:00+03:00 Then v7 shows with its values; at exactly 2026-10-05T00:00:00+03:00, v8.
- /s Given the same When `GET /v1/admin/policy/versions?in_force_at=2026-09-15T09:00:00Z` is called Then it returns v7.

**auditor-10.19 · Registry history and what was live when**
As the Auditor, I see every registry version with model id per task, prompt and schema versions, stage, who promoted it and why, so that I can explain which model processed eaters' data at any time (FRD §16.4).
- /r Given R-15 and R-16 When AU-01 opens **Versions → Registry** Then each row shows task, model id (`gemini-3.8-flash`), prompt p-12, schema s-4, stage and share (Canary 5 % · Rollout 100 %), P-01, time and reason.
- /r Given "Live at" 2026-09-26T12:00Z When applied Then R-15 (Canary 5 %) shows as live for its share, with R-14 for the rest.
- /r Given any registry version When shown Then every model id is frozen; Anomalies "registry versions naming a floating alias (`-latest`)" is 0 (P4; blueprint §1.3).

**auditor-10.20 · Kill switch history**
As the Auditor, I see each kill-switch change with who, when, why and how long, so that an AI outage decision is accountable.
- /r Given P-01 turned the kill switch On at 2026-09-27T10:12:00Z ("AI timeouts") and Off at 10:47:00Z When AU-01 filters Versions → Registry by "Kill switch" Then two events show with P-01, times, reason and "On for 35 min".

**auditor-10.21 · Reference approvals in the trail** *(shared with nutrition approver)*
As the Auditor, I see who approved each Food record and Alias, with evidence and licence, so that a regulator can trace a number to a reviewed source (FR-080; F1, F8).
- /r Given A-02 approved the Food record "فول مدمس" (Tier B recipe record) with evidence and licence When AU-01 filters Trail by action `food.version.approved` Then the row shows the record id, version, A-02, time, licence and evidence ids, and opening it shows no eater data.

**auditor-10.22 · Who holds which role now**
As the Auditor, I see every staff member's current roles with who assigned them and when, so that least privilege can be checked (NIST AC-2(7)).
- /r Given the staff fixture When AU-01 opens **Roles** Then each person shows staff id, display name, roles, assigned by and assigned at; U-19 shows "Support, Platform admin".
- /r Given no staff besides AU-01 (a fresh system) When Roles opens Then it says "Only you hold a role. Roles appear here as the platform admin assigns them."

**auditor-10.23 · Role history and "held at"** *(shared with platform admin)*
As the Auditor, I see every role assignment and removal and who held a role at a past moment, so that I can answer "who could request Grants on 20 September?".
- /r Given S-11 was assigned Support by P-01 on 2026-09-01T07:00:00Z and removed on 2026-09-25T15:00:00Z When AU-01 sets "Held at" 2026-09-20T12:00+03:00 Then S-11 is listed under Support; at 2026-09-26 S-11 is not.
- /r Given S-11's history When opened Then `role.assigned` and `role.removed` rows show who, when and the stated reason.

**auditor-10.24 · Separation of duties** *(shared with platform admin)*
As the Auditor, I see any person whose roles FR-081 keeps apart, and any self-assignment, so that no one both helps eaters and changes the system, and the Auditor stays independent (SDAIA DPO Rules Art. 9(5); GDPR Art. 38(6)).
- /r Given U-19 holds Support and Platform admin When AU-01 opens Anomalies Then "Support held with Platform admin or Nutrition approver: 1 — U-19" links to U-19's role history.
- /r Given P-01 tries to assign P-01 the Auditor role When saved through the roles API Then it returns 403 SELF_ASSIGNMENT_FORBIDDEN and the attempt appears in Trail as `access.denied`.

**auditor-10.25 · Filter and search**
As the Auditor, I narrow the trail by time, actor, action, subject key, Grant id and outcome, so that I find the events a question is about in seconds.
- /r Given the seeded trail When AU-01 sets actor S-07, action `grant.read*`, outcome Denied and 2026-09-28T00:00 – 2026-09-29T00:00 (+03:00) Then exactly 3 rows show (10:06:30Z, 11:25:00Z, 14:21:00Z). Each filter shows as a chip in words ("Actor: S-07"), and the page URL carries the filters, so a reload or a shared link reproduces the same list.
- /r Given "G-2026-0042" or "evt 1201" typed in the search box When Enter Then that Grant detail or event opens directly.
- /r Given the reason filter "offline sync" When applied Then G-2026-0042's request matches (free text on reasons only; health data is never searchable).

**auditor-10.26 · Invalid filter input**
As the Auditor, I am told exactly what is wrong with a filter, next to it, so that I fix it without losing the rest.
- /r Given start 2026-09-28 and end 2026-09-27 When applied Then an inline error under End says "End is before start — pick a date after 28 Sep 2026"; the list is not reloaded, and the other filters stay.
- /r Given "G-42" in search When Enter Then the hint under the box says "Grant ids look like G-2026-0042", and no result page replaces the list.

**auditor-10.27 · Time without ambiguity**
As the Auditor, I see every time in UTC with my offset, and event time apart from received time, so that two people in two cities read the same moment.
- /r Given event 1,201 at 2026-09-28T10:06:30Z and the console zone Asia/Riyadh When shown Then it reads "10:06:30Z · 13:06:30 +03:00", and every list header names the zone ("Times in UTC and +03:00").
- /r Given AU-01 changes the console zone to UTC When Trail reloads Then only the second column changes; event ids, order and UTC values are identical.

**auditor-10.28 · Large and slow results**
As the Auditor, I page through large result sets in stable order, see new events without rows jumping, and can cancel a slow search, so that I never lose my place.
- /r Given a query matching 25,000 events When run Then the first page of 100 rows (a starting value, tried and chosen at the care pass) appears with "25,000 events", ordered by sequence; loading more never reorders rows already shown.
- /r Given 12 events are appended while AU-01 reads When they arrive Then a bar at the top says "12 new events — show"; rows in view do not move.
- /r Given a search still running after 2 s When AU-01 waits Then "Searching… 40 %" shows with **Cancel**; Cancel stops it and keeps the previous results.

**auditor-10.29 · Offline**
As the Auditor, I keep reading what I loaded when the network drops, so that a flaky connection does not cost me my place.
- /r Given Trail loaded at 14:10:00Z When the browser goes offline Then the rows stay, with a quiet note "Offline — showing the trail as loaded at 14:10:00Z". **Export** and **Verify chain** are disabled, with the reason "Needs a connection", and they re-enable on reconnect without a reload.

**auditor-10.30 · Export exactly what I see, with a manifest**
As the Auditor, I export the filtered trail as CSV or JSON with a manifest, so that a regulator gets a complete, verifiable file that matches my screen (AU-7(b); GitHub benchmark).
- /r Given filter subject `subj_7Q2M`, 2026-09-01 – 2026-09-30 (+03:00), with 17 events When AU-01 selects **Export → CSV** Then the download holds `events.csv` (header plus 17 rows, one column per field, no embedded JSON) and `manifest.json` with the filters in words and as a query, as-of sequence 1,248, row count 17, generated by AU-01, generated at, time zone and the SHA-256 of `events.csv`.
- /m Given the export serializer and the 17 stored events When it writes the CSV Then rows keep sequence order and stored values, and `sha256(events.csv)` equals the manifest value.
- /r Given the export finished When AU-01 views Trail Then a `trail.exported` event shows AU-01, the filters and "17 rows" (OWASP).

**auditor-10.31 · A very large export is never silently cut**
As the Auditor, I get either the whole result or a clear choice, so that I never hand a regulator a partial file by accident (the Microsoft Purview pain).
- /r Given a filter matching 1,200,000 events When AU-01 selects Export Then the console says "1,200,000 events" and runs a background export with progress and **Cancel**. It delivers numbered parts whose row counts, listed in the manifest, sum to 1,200,000; or AU-01 is asked to narrow the filter. A file with fewer rows than the count never ships.
- /r Given an export job fails at part 7 When AU-01 opens **Exports** Then the job shows "Failed at part 7 of 12 — parts 1–6 are kept, nothing was marked complete" with **Retry from part 7**.

**auditor-10.32 · Find a subject for a complaint, and leave a trace**
As the Auditor, I find the subject key behind a complaint, with my reason recorded, so that I can answer about one person without browsing identities.
- /r Given a complaint quoting the sign-in email `eater.demo+1042@example.com` (synthetic) When AU-01 enters it in **Find subject** with the reason "SDAIA complaint ref C-118 (synthetic)" Then the console shows only `subj_7Q2M`, its Grants (3), reads (4 allowed, 2 denied), Consent events (5) and Privacy jobs (1: EXP-0031), with no profile or diary data, and Trail gains `subject.lookup` by AU-01 with the reason.
- /r Given **Find subject** with no reason When submitted Then the field says "Give a reason — lookups are recorded", and nothing is looked up.
- /r Given an email with no account When looked up Then "No account matches. It may never have existed, or it was deleted — deleted accounts keep no identifiers."

**auditor-10.33 · Scope a suspected breach in minutes**
As the Auditor, I summarise a staff account's activity over a window, with the distinct eaters it read, so that a 72-hour notification can state who and how many were affected (KSA IR Art. 24(1)(b); Egypt Art. 7; GDPR Art. 33(3)(a)).
- /r Given S-07 is suspected compromised from 2026-09-20T00:00:00Z to 2026-09-28T23:59:59Z When AU-01 filters actor S-07 for that window and opens **Summary** Then it shows Grants requested 4 · approved 2 · declined 1 · lapsed 1 · reads allowed 4 · reads denied 3 · **distinct subjects read 1** (`subj_7Q2M`; `subj_9K4D` had only a denied attempt) · first and last event times, and **Export** gives the events plus summary with a manifest.
- /m Given the summary function When computed Then "distinct subjects read" counts subject keys over Allowed reads only, not requests or denied attempts.

**auditor-10.34 · The quarterly report**
As the Auditor, I produce a periodic report of processing-related activity with figures that open to their events, so that management and the DPO duty to report have one page to read first (SDAIA DPO Rules Art. 8(4); AccessOwl's cover sheet).
- /r Given Q3 2026 (2026-07-01 – 2026-09-30, +03:00) When AU-01 opens **Reports → Quarter** Then one page shows: Grants requested, approved, declined, lapsed, revoked and expired; reads allowed and denied; anomalies by rule; Policy and registry versions made live; role changes; Consents given and withdrawn by purpose; Privacy jobs completed, with median and maximum days. Each figure links to its events, and the page exports as CSV plus manifest.
- /r Given a quarter with no events When opened Then each figure shows 0 with "No events in this period", never a blank.

**auditor-10.35 · My own activity is on the record**
As the Auditor, I see my own queries, lookups and exports in the trail, so that the watcher is watched too (OWASP: "All access to the logs must be recorded").
- /r Given AU-01 ran 3 queries, 1 lookup and 1 export When AU-01 filters actor AU-01 Then 3 `trail.queried`, 1 `subject.lookup` and 1 `trail.exported` events show, with filters and counts, and they are as immutable as every other event.

**auditor-10.36 · The console in Arabic**
As the Auditor working in Arabic, I read the trail right-to-left without ids, times or hashes being scrambled, so that the evidence reads the same in both languages.
- /r Given the console language is Arabic When AU-01 opens Grants → G-2026-0042 Then the layout mirrors (the timeline runs right to left, the back arrow points right), and labels come from the string catalogue with the one Arabic label fixed for Grant. Ids (`G-2026-0042`), event ids, hashes and ISO timestamps stay left-to-right and in Western digits. Counts follow the Arabic-Indic or Western numerals setting.
- /r Given a Grant reason typed in Arabic, «إجمالي يوم ٢٧ سبتمبر يبدو مضاعفًا بعد المزامنة», and another in English When both show in the Arabic console Then each paragraph aligns by its own language (Arabic right, English left) and neither is translated.
- /r Given an export from the Arabic console When opened Then CSV headers are the same stable keys as in English, and `manifest.json` records `"console_language": "ar"`.

**auditor-10.37 · On a phone, for one anomaly**
As the Auditor away from my desk, I can check one anomaly or Grant on a phone, so that I can answer quickly without a laptop.
- /r Given a 390 px wide browser When AU-01 opens Anomalies and then G-2026-0042 Then there is no horizontal page scroll; table rows become stacked cards (id, state, window, reads), the timeline runs vertically, and every control is at least 44 × 44 CSS px (a chosen default).
- /r Given the same width When AU-01 opens Trail Then filters collapse behind **Filters (3)**, which states the active count; Export stays reachable.

**auditor-10.38 · Keyboard, screen reader, zoom**
As the Auditor, I can do the whole review by keyboard and screen reader and at 200 % zoom, so that the console works for every reviewer (NFR-08; care group 6).
- /r Given keyboard only When AU-01 tabs through Trail filters, results and Export Then focus follows visual order with a visible focus ring; Enter opens a row, Esc closes a detail panel, and "/" focuses search.
- /r Given VoiceOver or NVDA When focus reaches a Grant row Then it announces "Grant G-2026-0042, Expired, 3 reads allowed, 1 denied, requested by S-07", and the announcement updates when the state changes.
- /r Given 200 % browser zoom When Trail, Grant detail and Anomalies render Then no text clips or overlaps, and all text meets 4.5:1 contrast in light and dark.

### Journey 9 — WF-9, read side

**auditor-9.1 · Consents by purpose**
As the Auditor, I see every Consent purpose with its current text version and counts, so that I can show consents are separate and current (FR-076; R22).
- /r Given seeded Consent records When AU-01 opens **Consents** Then each purpose has its own row: Diary processing · Send photos, voice and text to Google's AI (Gemini) · Health: read workouts · Health: read body mass · Health: write dietary energy · Microphone · Photos · Optional research. Each row shows the current text version, counts given, withdrawn and never asked, and the last change time.
- /r Given a fresh system with no Consent records When opened Then it says "No Consent records yet — they appear as eaters finish onboarding."

**auditor-9.2 · One subject's Consent history**
As the Auditor, I see one eater's Consent history over time, so that I can prove what they agreed to, when and how (IR Art. 11(1)(d); GDPR Art. 7(1)).
- /r Given `subj_7Q2M` When AU-01 opens Consents for that subject key Then rows show, in time order: c-ai-3 given 2026-09-01T18:22:10Z "in-app toggle · onboarding" · withdrawn 2026-09-20T07:45:00Z "Settings" · c-ai-4 given 2026-09-25T20:10:00Z "Settings", plus the research and Health rows. Each row names purpose, text version, action, method, time and app version.

**auditor-9.3 · The exact words the eater agreed to**
As the Auditor, I open a Consent text version and read it exactly as shown in the app, in English and Arabic, so that "what did they consent to?" has one answer (R2).
- /r Given c-ai-3 When AU-01 opens it Then the English and Arabic texts render exactly as the app showed them. The Arabic, «أوافق على إرسال الصور والصوت والنص إلى Google ‏(Gemini) لتحليل وجباتي» (synthetic wording), is right-aligned with the Latin "Google (Gemini)" kept intact. The provider is named, and the page shows the version id, publish date and the count of subjects who consented under it.
- /s Given c-ai-3 is published When c-ai-4 is published Then c-ai-3's stored text is byte-for-byte unchanged.

**auditor-9.4 · One purpose per record**
As the Auditor, I see that no Consent record covers two purposes, so that bundled consent cannot hide in the data (IR Art. 11(1)(e); R3).
- /m Given the Consent record schema When a record with two purposes is built Then validation fails.
- /r Given the seeded data When AU-01 opens Anomalies Then "Consent records covering more than one purpose" is 0.

**auditor-9.5 · A withdrawal takes effect** *(shared with eater)*
As the Auditor, I see what a withdrawal stopped and deleted, so that I can show processing ceased without undue delay (IR Art. 12(3); AT-29).
- /r Given `subj_7Q2M` withdrew the AI Consent at 2026-09-20T07:45:00Z When AU-01 opens that withdrawal Then the Effect card shows "AI requests after withdrawal: 0 (until c-ai-4 on 2026-09-25)", "Pending Analyses cancelled: 1", "Private cached analysis deleted: 07:45:03Z", "Queued uploads purged: 2".
- /s Given the withdrawal is recorded When the app posts `POST /v1/analyses` for `subj_7Q2M` Then the API refuses for missing Consent, and the Gemini adapter mock records 0 calls.
- /r Given Anomalies When opened Then "AI requests after the AI Consent was withdrawn" is 0.

**auditor-9.6 · Recorded once, with device time and received time**
As the Auditor, I see a retried or offline withdrawal recorded once, with when it was made and when it arrived, so that the record is neither doubled nor misdated (AT-10 pattern; OWASP event time vs log time).
- /r Given the "Optional research" withdrawal made on the device at 2026-09-21T22:40:00Z, received 2026-09-22T06:05:30Z and delivered twice When AU-01 opens it Then one event shows "Made 22:40:00Z on the device · received 06:05:30Z".

**auditor-9.7 · Health permissions shown honestly**
As the Auditor, I see what the app recorded about Health access and what it cannot know, so that I never claim iOS granted a read it cannot see (R7; P30).
- /r Given `subj_7Q2M` turned on "Health: read workouts" in the app When AU-01 opens that Consent Then it reads "In-app Consent given 2026-09-01T18:25:00Z · iOS read permission: not observable (Apple does not tell apps whether read access was denied)", never "granted by iOS".

**auditor-9.8 · Raw evidence viewed only with Consent** *(shared with platform admin)*
As the Auditor, I see every staff view of a meal photo or audio for quality review, and the Consent it relied on, so that raw evidence is never opened without explicit consent (FRD §19.2; FR-077, FR-079).
- /r Given a staff member with the quality-review permission viewed the photo of an Analysis for `subj_5H1C` (research Consent given) When AU-01 filters action `evidence.viewed` Then the row shows staff id, Analysis id, subject key, the Consent version relied on and the signed-access expiry; no image is shown in the console.
- /r Given `subj_8N2R` has no research Consent When that staff member requests the subject's raw photo Then the API returns 403 CONSENT_REQUIRED, `access.denied` is logged, and Anomalies "Raw evidence viewed without the research Consent" stays 0.

**auditor-9.9 · Privacy jobs on the 30-day clock**
As the Auditor, I see every export and deletion with its age against 30 days, so that no rights request quietly runs late (IR Art. 3(1)(a); NFR-13; R23, R31).
- /r Given EXP-0031, DEL-0007, DEL-0009 and DEL-0010 on 2026-10-01 When AU-01 opens **Privacy jobs** Then rows show kind, state, requested, completed and age in days. DEL-0009 reads "Due in 4 days (day 26 of 30)"; DEL-0010 reads "Overdue — day 31 of 30" (each as words plus an icon). Anomalies "Deletions open > 30 days" is 1.
- /r Given no Privacy jobs When opened Then it says "No export or deletion has been requested yet."

**auditor-9.10 · The deletion completion record**
As the Auditor, I open a completion record that proves every step finished and holds no identifiers, so that I can show the deletion happened without keeping who it was (WF-9 done-when; AT-29; R4, R23).
- /r Given DEL-0007 When AU-01 opens it Then it shows only: record id, requested 2026-09-02T06:00:00Z, completed 2026-09-16T06:30:00Z (14 days), the Policy version applied, and the steps with completion times. The steps are: private records deleted · photos and audio deleted · derived caches and private cached analysis deleted · queued jobs purged · prepared exports deleted · processors told (Google) · Sign in with Apple token revoked (or "not applicable — not used") · backups expire by 2026-10-16 under the disclosed backup lifecycle (FRD §17.2).
- /m Given the completion-record schema When validated Then no field from the identifier deny-list is present (user id, email, phone, name, device id, IP, Apple id, subject key); adding one fails the test.
- /s Given DEL-0007 completed on the emulator When the test searches the Firestore and Cloud Storage emulators for the deleted user id Then nothing is found (NFR-13 "verify deletion propagation").

**auditor-9.11 · A deletion step keeps failing**
As the Auditor, I see a stuck step plainly, so that a deletion is never shown as done while something remains.
- /r Given DEL-0011, where "photos and audio deleted" failed twice (storage timeout) When AU-01 opens it Then the state reads "In progress — 1 step retrying (attempt 3 at 14:30:00Z)", the failing step shows its error code, the record is not marked complete, and its age counts toward 30 days.

**auditor-9.12 · A deleted subject in the trail**
As the Auditor, I still see Grant history about a deleted eater, under a key that no longer leads to a person, so that evidence survives without identifying anyone (KSA Law Art. 18(1); GDPR Art. 17(3)(b), (e). This is a recommended default; see Conflicts, item 4).
- /r Given `subj_2W6P` was deleted (DEL-0012) When AU-01 opens Grant G-2026-0039 for that subject Then the subject shows as "subj_2W6P · account deleted", every Grant event remains, and Verify chain stays intact.
- /r Given the former sign-in email When AU-01 searches it in Find subject Then the result is "No account matches…", because the key-to-person mapping was destroyed with the account.

**auditor-9.13 · An eater's export job, not its contents**
As the Auditor, I see that an eater's export ran and what it covered, without opening it, so that the right of access is evidenced and the data stays private (FR-075).
- /r Given EXP-0031 When AU-01 opens it Then it shows requested and completed times, categories with item counts (Entries, Units, Recipes, Targets, Consents), file size, link expiry and whether it was downloaded. There is no download or preview control.

**auditor-9.14 · Retention runs**
As the Auditor, I see each retention run and the oldest item left, so that I can verify the retention promises (FR-078; FR-082 "retention verification").
- /r Given Policy v7 retention (raw scans 30 days unless saved; temporary audio 24 h) and the run at 2026-09-28T02:00:00Z When AU-01 opens **Privacy jobs → Retention** Then the run shows raw scans deleted 41, audio deleted 12, oldest remaining raw scan 29 d 22 h, oldest audio 23 h 10 min, Policy v7.
- /r Given no run in the last 26 hours (seeded skip) When opened Then it says "No retention run since 2026-09-27T02:00:00Z — expected daily", and Anomalies shows any raw scan over 30 days or audio over 24 h by count.

**auditor-9.15 · Records of processing, built from what is live** *(proposed screen; see Conflicts, item 8)*
As the Auditor, I read and export a records-of-processing view assembled from live, versioned configuration, so that a regulator's request is answered from facts, not a stale document (KSA Law Art. 31, IR Art. 33; Egypt Art. 4(9); GDPR Art. 30).
- /r Given the Consent purposes, Policy v7 retention and registry R-16 When AU-01 opens **Records of processing** Then each purpose row shows: purpose; data categories; subject categories ("adults 18+"); recipients ("Google — Gemini on Agent Platform; Firebase and Google Cloud"); transfer outside the Kingdom ("yes — Gemini runs on `global` or US/EU multi-regions only", P11 as corrected); and retention. Each value names the version it came from. A field the product does not hold shows "Not recorded in the product", never a blank.
- /r Given the view When AU-01 selects Export Then CSV plus manifest download, and Trail records `trail.exported`.

**auditor-9.16 · Consent evidence for a regulator**
As the Auditor, I export one subject's Consent history with a manifest, so that the proof of consent travels intact.
- /r Given `subj_7Q2M` When AU-01 selects Consents → Export Then `consents.csv` holds the subject's rows (purpose, text version, action, method, made at, received at, app version) and `manifest.json` the filters, row count and SHA-256, and Trail records the export.

**auditor-9.17 · A Consent record whose text is missing**
As the Auditor, I am told when a Consent points to a text version that cannot be found, so that a gap in the proof is visible, not hidden.
- /r Given a separate fixture: one Consent record referencing text version c-ai-2, which is absent When AU-01 opens it Then it reads "Text version c-ai-2 not found — the record is kept, its wording cannot be shown", and Anomalies "Consent records with missing text versions" is 1.

---

## 5 · The experience this persona needs

- **Device and place.** A desk, a large screen, a browser with keyboard and mouse, a spreadsheet beside it. Sometimes a phone, for one anomaly (390 px).
- **The moment that matters.** A regulator, the CEO or an eater asks "who saw this person's data, and were they allowed?". Or a breach clock starts. The Auditor must answer from the screen and hand over a file that matches it, in minutes.
- **The feeling it must leave.** *Certainty I can defend.* Nothing is hidden, nothing is partial, and nothing can be edited behind my back.
- **The matching style.** Dense, calm and exact. Tables first, monospace for ids and hashes, and times always in UTC with an explicit offset. Neutral colour for ordinary rows. Anomalies are loud only when a rule that must stay at zero is not at zero, and always as words plus an icon. Filters state themselves in words and live in the URL. There are no write controls anywhere. Fast: keyboard-first, with a filtered list shown without a designed wait. Long work (verify, big export) runs as a job with progress and Cancel.

### Care questions this persona raises, answered as requirements

**1 · Deserves to exist, and where it lives**
- One sentence per section: *Trail* shows the evidence; *Grants* shows diary access; *Anomalies* shows the never-events; *Versions* shows who changed the rules; *Roles* shows who could act; *Consents* and *Privacy jobs* show rights in action; *Reports* holds the periodic page.
- Said no to: free browsing of eater identities (lookup only, with a reason, 10.32); any write control (10.15); viewing diary content, photos or audio (10.5, 9.8, 9.13); dashboards of food or health metrics.
- Places versus actions: sections sit in the navigation; Export, Verify chain, Compare and Find subject sit next to the data they act on.
- No pop-ups for reading; details open in a side panel that keeps the list in place (Esc closes it).

**2 · Found and understood**
- Titles name the place ("Grants", "Grant G-2026-0042"). States and actions use the map's words: Grant, Consent, Policy, Entry, Day, Analysis.
- The same thing always has the same word. "Lapsed", "Declined", "Revoked" and "Expired" are distinct and defined in one tooltip glossary.
- Defaults work without settings: the console zone comes from the browser and is shown; the default range is the last 30 days, shown as a chip.
- Key status sits where people look: chain integrity, the anomaly count and active Grants in the Trail header and the navigation badge.

**3 · How it feels**
- Every action answers in the moment: a query shows a count; an export shows queued, then progress, then "17 rows · SHA-256 …"; verify shows its range checked.
- Loudness matches importance: refused attempts are information, while a non-zero never-event is an alert.
- No rows jump: new events wait behind a "show" bar (10.28).
- Page size (100), the progress threshold (2 s), the default range (30 days) and the lapse display were tried in the served console and chosen on purpose at the care pass.

**4 · Empty, wrong, slow**
- Each empty state says why and offers the next step (10.2, 10.22, 9.1, 9.9, 10.34).
- Errors sit next to the problem, say how to fix it and never blame (10.26, 10.32).
- Loading shows a skeleton, never zeros (10.1). Long tasks show progress and Cancel (10.14, 10.28, 10.31).
- Offline keeps the last data with a quiet note and disables what needs the network, with the reason (10.29).
- A failed check reads "Not evaluated", never 0 (10.13). A partial export never ships (10.31). A stuck deletion is never "done" (9.11).
- Permission denied names the needed role and is itself logged (10.16).

**5 · The inside**
- Names in code, data and logs match the screen: `grant.read`, `consent.withdrawn`, Policy, registry, subject key.
- The trail holds no health data (10.5 /m); the export serializer keeps content and order (10.30 /m); the completion record is checked against a deny-list (9.10 /m); every anomaly rule is proven to catch its case (10.13 /m).
- Sample data is synthetic, with Arabic reasons and long ids. There are no real names or emails.
- The product claims only what it does: the Health read state says "not observable" (9.7), and a missing consent text says so (9.17).

**6 · Inclusion**
- 200 % zoom without clipping; 4.5:1 contrast in light and dark; a visible focus ring; meaning never by colour alone; screen-reader labels that stay current; a full keyboard path (10.38).
- Arabic mirrors where direction carries meaning; ids, times and hashes stay LTR; each paragraph aligns by its own language (10.36).
- Nothing disappears on a timer: the "new events" bar and export results stay until dismissed.

---

## 6 · Stories shared with other personas

| story | shared with | why |
|---|---|---|
| auditor-10.3 | support agent, eater | the WF-10 done-when: request → the eater approves in Settings → reads inside the time box → expiry → visible in the trail |
| auditor-10.6, 10.7, 10.11 | eater, support agent | the eater's decision and revocation create the events the Auditor reads |
| auditor-10.12 | support agent | self-requests refused |
| auditor-10.21 | nutrition approver | Food and Alias approvals |
| auditor-10.23, 10.24 | platform admin | role assignments and refused self-assignment |
| auditor-9.5 | eater | withdrawal and its effect |
| auditor-9.8 | platform admin | raw-evidence access for quality review |

---

## 7 · Conflicts for the model phase

1. **Read-only seat vs FRD §23.2.** The FRD says "Privacy/security reviewers **approve** consent, retention, access, and target-market compliance". The map makes the Auditor read-only and gives retention Policy to the nutrition approver. The model phase must decide who approves consent text versions and retention values.
2. **Breach record has no home.** KSA IR Art. 24(3), SDAIA's guide (Stage Three), GDPR Art. 33(5), the ICO ("regardless of whether you are required to notify") and Egypt's ER per Shalakany all require a documented breach record with notifications and corrective actions. The map has none, and the Auditor cannot write. Options: a breach record the DPO writes in the console, or one kept outside the product. Either way, auditor-10.33's evidence export feeds it.
3. **Review sign-off needs a write.** The evidence of a periodic review is "who reviewed it, when, what they decided … and why" (AccessOwl; Egypt Art. 9(1) "approving the results of such evaluation"). Proposal: the Auditor's only write is a review note appended to the trail. It would never edit an event.
4. **Erasure vs the trail.** Grant and Consent events about a deleted eater stay as evidence (GDPR Art. 17(3)(b), (e); KSA Law Art. 18(1) allows keeping data that cannot identify the person). Recommended default: events carry a pseudonymous subject key, and deletion destroys the key-to-person mapping (9.12). Open: the **trail retention period**. No source fixes one for access logs; NIST AU-11 leaves it to the organisation. KSA IR Art. 33(1)'s "five years after the … end of any … Processing activity" is for the records of processing, not logs. Counsel decides; a value would live in Policy.
5. **Identity minimisation.** Support needs to know who it is helping; the Auditor works on subject keys and reaches identity only through a logged lookup with a reason (10.32). The support lens and this lens must agree on which identity fields a Grant request shows.
6. **Infrastructure reads are outside the trail.** Google Cloud Data Access audit logs are "disabled by default". Direct Firestore or Cloud Storage reads by an engineer would not show in the trail unless Data Access logs are enabled at hosting and correlated (NIST AU-6(3)). The ship rows are dropped (§0 line 10), so this waits for a hosting delta. Until then, "no read outside a Grant" holds for the product's API only.
7. **Consent text versions need an owner.** Who writes and publishes the English and Arabic consent wording, and who reviews it? The map is silent; FRD §23.2 points at privacy reviewers.
8. **Records of processing as a screen.** The map lists records of processing as pre-launch governance work (r1 implications), not a screen. 9.15 proposes deriving it from live configuration. Fields not in configuration (controller contact, DPO details, security measures) need an owner and a home.
9. **Linking staff and eater identities.** The rule "no staff member can approve their own request" (10.12) needs the system to know when a staff identity and an eater account belong to the same person. How they are linked is undecided.
10. **Grant lapse time and maximum duration** (10.10). No source gives values. They need a Policy field and an owner, and Policy is otherwise approver-owned and nutrition-focused.
11. **Quality-review access to raw evidence** (9.8, FRD §19.2). Which role holds it, and which Consent covers it: the "optional research" purpose or a separate one?
12. **Arabic in regulator exports** (10.36). Whether SDAIA or the PDPC expect Arabic labels in an extract is an `assumption`. Proposal: stable machine keys plus an optional Arabic label row.
13. **The eater's own view of reads.** Google's Access Transparency shows the data owner each provider read. Whether the eater sees the reads within their Grant in Settings is for the eater lens. If yes, its counts must equal auditor-10.3's.
14. **Breach notice timing differs by regime.** Egypt: data subjects within three business days of reporting. KSA: "without undue delay". This is a runbook matter outside the product, but the evidence export must serve both.

---

## 8 · Coverage

- **FRD lines:** FR-075 (9.13) · FR-076 (9.1–9.4) · FR-077 (9.8) · FR-078 (9.9–9.11, 9.14) · FR-079 (9.8) · FR-080 (10.17–10.22) · FR-081 (10.3–10.16, 10.24) · FR-082 (9.14, 9.15) · NFR-07 (10.9) · NFR-08 (10.38) · NFR-13 (9.9, 9.10) · §3.3 (10.17) · §16.4 (10.19, 10.20) · §17.2 (9.10) · §18.2 typed errors (10.6–10.12) · §19.2 (10.5, 9.8) · §23.2 (Conflicts, item 1).
- **AT fixtures reused:** AT-10 (retry delivered three times → one event: 10.6, 9.6) · AT-29 (deletion and withdrawal propagate to media, queues, private cached analysis and exports: 9.5, 9.10).
- **Research cited:** R2, R3, R4, R7, R20–R26, R28, R29 (as corrected), R30, R31; P4, P11 (as corrected), P30; plus §1.1–1.2 above.
- **Counts:** 55 stories (38 in journey 10, 17 in journey 9) · 112 acceptance lines (/m 8 · /s 10 · /r 94).
