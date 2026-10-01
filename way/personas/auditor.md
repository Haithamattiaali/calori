# Auditor — persona lens (privacy reviewer / DPO seat, read-only)

Lens written 2026-10-01 for the /way build of Sips & Bytes, following `way/personas/_lens-brief.md`. Revised the same day in fix round 1, against the lens verdict at the foot of this file and the binding vocabulary in `way/vocabulary.md` (delta D2).

- **Workflows:** WF-10 read side (Grants, Policy, Registry, reference approvals, Roles) and WF-9 read side (Consents, the age confirmation, Privacy jobs, deletion completion records, retention).
- **Story ids:** `auditor-10.n` and `auditor-9.n`.
- **Read first:** `way/blueprint.md` §0–§1, `way/vocabulary.md`, `way/brief/frd-v1.0.md`, and `way/research/r1-rules-trends.md` with `r1-refute-b.md`. None of these is cited below as standing: R34 (refuted), R29's decree type (doubtful) and R18's Cloud Tasks claim (dropped). Also read: `r1-platforms.md` (P4, P11 as corrected, P29, P30), `r1-competitors.md` with `r1-refute-a.md`, the care questions, `way/lessons.md`, and the support, platform-admin and approver lenses, whose fixtures this lens reuses where they overlap.
- **Privacy of this run:** no owner identifier was sent to any outside service, and every request used a generic User-Agent.

**Not legal advice.** This file reports what each source says. Counsel decides what applies (FR-082).

---

## 0 · Who

The **Auditor** is the privacy reviewer or DPO seat. It works in the admin console with **read-only** rights (blueprint §1.2; interaction row "Auditor → trail | read | Audit events | read-only") and never sees an eater's diary.

What the Auditor reads:
- **The Audit trail**: who asked for a Grant, whether the eater said yes, every read inside the Grant, every refused read or write, and how the Grant ended.
- **Consents**, plus the eater's age confirmation.
- **Privacy jobs** and **deletion completion records**.
- **Policy versions** and **Registry versions**.
- **Food, Tier B recipe record and Alias approvals.**
- **Role assignments.**

**The job.** The Auditor has to show a regulator, management or a complaining eater that access was lawful, documented and limited. They also catch what must never happen before anyone else does.

This is a hidden persona, found by the first map (`way/map-first.md` §2, from FR-081/082). FRD §23.2 names the seat: "Privacy/security reviewers approve consent, retention, access, and target-market compliance" (see Conflicts, item 1).

---

## 1 · Research cycle 2 — the Auditor's day in the benchmarks

Every source was opened in this run on **2026-10-01**. Quotes are short. A claim that could not be opened is labelled `assumption`.

### 1.0 Sources opened (link · the source's own date)

| id | source | link | source date |
|---|---|---|---|
| K1 | SDAIA — Personal Data Protection Law (English, official PDF) | https://sdaia.gov.sa/en/SDAIA/about/Documents/Personal%20Data%20English%20V2-23April2023-%20Reviewed-.pdf | Royal Decree M/19, amended by M/148 (5/9/1444H) |
| K2 | SDAIA — Implementing Regulation of the PDPL (English, official PDF) | https://sdaia.gov.sa/en/SDAIA/about/Documents/ExecutiveRegulations.pdf | undated in the PDF |
| K3 | SDAIA — Rules for Appointing Personal Data Protection Officer | https://sdaia.gov.sa/en/SDAIA/about/Documents/RulesforAppointingPersonalDataProtectionOfficer.pdf | "Version 01, August 2024" |
| K4 | SDAIA — Personal Data Breach Incidents Procedural Guide | https://sdaia.gov.sa/en/SDAIA/about/Documents/PersonalDataBreachIncidents.pdf | "Issue No. 1.0, October 2024" |
| E1 | Egypt PDPL 151/2020, Arabic–English dual text (Sharkawy & Sarhan) | https://sharkawylaw.com/wp-content/uploads/2021/05/Data-Protection-Law-Translation-dual-text.pdf | uploaded May 2021 |
| E2 | Shalakany, "New Executive Regulations for Egypt's Personal Data Protection Law" (law-firm note, secondary) | https://shalakany.com/wp-content/uploads/2025/12/New-Executive-Regulations-for-Egypts-Personal-Data-Protection-Law-.pdf | uploaded December 2025 |
| G1 | GDPR Art. 5 · Art. 7 · Art. 17 · Art. 30 · Art. 32 · Art. 33 · Art. 38 (gdpr-info.eu) | https://gdpr-info.eu/art-5-gdpr/ · https://gdpr-info.eu/art-7-gdpr/ · https://gdpr-info.eu/art-17-gdpr/ · https://gdpr-info.eu/art-30-gdpr/ · https://gdpr-info.eu/art-32-gdpr/ · https://gdpr-info.eu/art-33-gdpr/ · https://gdpr-info.eu/art-38-gdpr/ | Regulation (EU) 2016/679 |
| U1 | ICO — Personal data breaches: a guide | https://ico.org.uk/for-organisations/report-a-breach/personal-data-breach/personal-data-breaches-a-guide/ | notes a 20 Aug 2025 change |
| O1 | OWASP Logging Cheat Sheet | https://cheatsheetseries.owasp.org/cheatsheets/Logging_Cheat_Sheet.html | undated (current) |
| N1 | NIST SP 800-53 Rev 5.2.0, OSCAL catalogue | https://raw.githubusercontent.com/usnistgov/oscal-content/main/nist.gov/SP800-53/rev5/json/NIST_SP-800-53_rev5_catalog.json | last modified 2026-05-11 |
| C1 | Google Cloud — Cloud Audit Logs overview | https://docs.cloud.google.com/logging/docs/audit | last updated 2026-09-30 |
| C2 | Google Cloud — Access Transparency overview | https://docs.cloud.google.com/assured-workloads/access-transparency/docs/overview | last updated 2026-09-24 |
| C3 | Google Cloud — Access Approval overview | https://docs.cloud.google.com/assured-workloads/access-approval/docs/overview | last updated 2026-09-24 |
| H1 | GitHub — Reviewing the audit log for your organization | https://docs.github.com/en/organizations/keeping-your-organization-secure/managing-security-settings-for-your-organization/reviewing-the-audit-log-for-your-organization | undated |
| M1 | Microsoft Learn — Export, configure, and view audit log records (Purview) | https://learn.microsoft.com/en-us/purview/audit-log-export-records | ms.date 2026-06-19 |
| A1 | AccessOwl — User access reviews best practices (vendor blog) | https://www.accessowl.com/blog/user-access-reviews-best-practices | published 2026-03-19 |

### 1.1 What the three regimes expect: records of processing, access logs, breach evidence

| need | Saudi PDPL / SDAIA | Egypt PDPL 151/2020 | GDPR (if EU storefronts, R30) | what it asks of the Auditor's console |
|---|---|---|---|---|
| **Records of processing (written register)** | [K1](https://sdaia.gov.sa/en/SDAIA/about/Documents/Personal%20Data%20English%20V2-23April2023-%20Reviewed-.pdf) Art. 31: "the Controller shall maintain records … available whenever requested by the Competent Authority" (purpose, categories, recipients, transfers outside the Kingdom, retention). [K2](https://sdaia.gov.sa/en/SDAIA/about/Documents/ExecutiveRegulations.pdf) Art. 33: keep them "during all the period Personal Data is being processed, and till to five years after the date of end of any Personal Data Processing activity"; they "shall be written" and "accurate and up to date". K2 Art. 20(6): disclosures are recorded with "dates, methods, and purposes" | [E1](https://sharkawylaw.com/wp-content/uploads/2021/05/Data-Protection-Law-Translation-dual-text.pdf) Art. 4(9): "Holding a specific register for the data including the description of the categories of the Personal Data … the persons to whom such data shall be disclosed … the duration … any other data related to the Cross Border Movement … description of the technical and regulatory procedures of the Data Security". E1 Art. 4(12): "Provide the necessary means to prove its compliance … and allow the Center to perform the necessary inspection" | [Art. 30](https://gdpr-info.eu/art-30-gdpr/)(1): purposes, categories, recipients, transfers, "envisaged time limits for erasure", security measures. Art. 30(3)–(4): "in writing, including in electronic form … available to the supervisory authority on request". [Art. 5](https://gdpr-info.eu/art-5-gdpr/)(2): "be able to demonstrate compliance ('accountability')" | a Records of processing view, built from live versioned configuration and exportable (auditor-9.16) |
| **Consent evidence** | K2 Art. 11(1)(d): "documented through means allowing future verification, such as specifying time and the mean of Consent"; 11(1)(e): "A separate consent … for each Processing purpose" (R22). K2 Art. 12(3): "cease Processing without undue delay"; 12(4): notify recipients | [E2](https://shalakany.com/wp-content/uploads/2025/12/New-Executive-Regulations-for-Egypts-Personal-Data-Protection-Law-.pdf), on the Executive Regulations: "secure internal electronic records, which must include, inter alia, records of consent, descriptions of personal data processed, and applicable retention periods" (`opened (secondary)`) | [Art. 7](https://gdpr-info.eu/art-7-gdpr/)(1): "the controller shall be able to demonstrate that the data subject has consented"; Art. 7(3): withdrawal "as easy" as giving (R31) | every Consent carries purpose, text version, time, method and app version, and a withdrawal shows its effect (auditor-9.1 to 9.8) |
| **Capacity (adults)** | K2 Art. 11(1)(c): "Consent shall be given by a person who has full legal capacity" (R22) | E1 definitions: "children's data shall be deemed sensitive personal data" (R28) | — | the age confirmation is recorded with the Consents (auditor-9.9) |
| **Rights requests and deletion** | K2 Art. 3(1)(a): act "within a period not exceeding (30) days"; 3(1)(d): "document and keep record of all received requests including oral requests". K2 Art. 8(2)(c): destroy "all copies … including backups" (R23). K1 Art. 18(1): data kept after the purpose ends must not contain "anything that may lead to specifically identifying Data Subject" | E2: "Requests made by data subjects … must be documented and maintained in accordance with the ER's record-keeping requirements" (`opened (secondary)`) | Art. 12(3): one month (R31); [Art. 17](https://gdpr-info.eu/art-17-gdpr/)(1): "without undue delay"; Art. 17(3)(b), (e): no erasure where processing is needed "for compliance with a legal obligation" or "legal claims" | Privacy jobs on a 30-day clock; a completion record without identifiers; Audit trail events kept under the account id, which leads nowhere once the account is deleted (auditor-9.10 to 9.14) |
| **Access logs for health data** | K1 Art. 23(1): "Restricting the right to access Health Data … to the minimum number of employees". K2 Art. 26(3): "different level of access to data among employees". K2 Art. 26(4): "Document all stages of Health Data Processing and provide the means to identify the person in charge for each stage" (R24) | E1 Art. 4(6): measures to "avoid any Personal Data Breach, damage, alteration or manipulation" | [Art. 32](https://gdpr-info.eu/art-32-gdpr/)(1)(b): "ongoing confidentiality, integrity" | every read inside a Grant names the Support agent and what was read; every refused read or write is logged (auditor-10.3 to 10.13) |
| **Breach evidence** | K2 Art. 24(1): notify "within a delay not exceeding (72) hours of becoming aware", with "time, date, and circumstances … actual or approximate numbers of impacted Data Subjects". K2 Art. 24(3): "keep a copy of the reports … and document the corrective measures". [K4](https://sdaia.gov.sa/en/SDAIA/about/Documents/PersonalDataBreachIncidents.pdf) Stage Three: "retain copies of the documents submitted to SDAIA … the corrective actions taken, and any relevant proper records" | E1 Art. 7: report "within seventy two hours", including "the approximate number of Personal Data affected"; tell data subjects "within three business days as of the date of reporting". E2: the breach is "documented in a secure digital record" | [Art. 33](https://gdpr-info.eu/art-33-gdpr/)(1): 72 hours "where feasible"; 33(3)(a): "approximate number of data subjects"; 33(5): "The controller shall document any personal data breaches, comprising the facts … its effects and the remedial action taken" | scope a suspected incident by actor and window, count the distinct accounts read, export it verifiably (auditor-10.35). The breach record itself has no home in the map (Conflicts, item 2) |
| **The DPO seat** | K2 Art. 32(3)(b): "Supervising impact assessment procedures, audit and control reporting … documenting assessment results"; (d) "Notifying the Competent Authority of Personal Data Breach incidents"; (f) "Monitoring and updating the records of personal data processing activities". [K3](https://sdaia.gov.sa/en/SDAIA/about/Documents/RulesforAppointingPersonalDataProtectionOfficer.pdf) Art. 8(4): "Preparing periodic reports regarding Controller activities related to processing of Personal Data"; K3 Art. 9(5): "shall not assign tasks that may conflict with DPO tasks or affect DPO's independence". R25: a DPO is required here | E1 Art. 9(1), from the **Arabic text**: «إجراء التقييم والفحص الدوري لنظم حماية البيانات الشخصية ومنع اختراقها، وتوثيق نتائج التقييم», that is, a regular evaluation and inspection "**and documenting the results of the evaluation**". The firm's English rendering says "approving"; the Arabic says documenting (توثيق). E1 Art. 9(6): "Monitoring the registration and the update of the Personal Data register" | [Art. 38](https://gdpr-info.eu/art-38-gdpr/)(6): other tasks must "not result in a conflict of interests" | a period report (auditor-10.36); the Auditor role combines with no other role (auditor-10.26) |

**Dates and status.**
- **Saudi (K1–K4):** read as SDAIA's official English PDFs.
- **Egypt law (E1):** read in a law firm's dual text; the official PDPC host was unreachable (r1-refute-b).
- **Egypt's Executive Regulations (E2):** Shalakany says "the Minister of Communications and Information Technology issued the executive regulations … by virtue of Decree No.816 for the year 2025", "published in the Official Gazette on November 1st, 2025", with "a transitional grace period of 1 year … (i.e. 1 November 2026)". r1-refute-b leaves the decree type **doubtful**. Its CMS source says the regulations were "not made publicly available until 25 December 2025", and that the PDPC may treat the grace period as running to the end of 2026. This lens does not settle either point. It records only the refuter's corrected statement: "issued 1 Nov 2025, grace period strictly to 1 Nov 2026, possibly treated as end-2026". E2's other statements are one firm's reading of a text this lens did not open, so they are `opened (secondary)`.

### 1.2 What good audit tools do, and where reviewers struggle with them today

| benchmark | what it does | lesson for us |
|---|---|---|
| [C3 Access Approval](https://docs.cloud.google.com/assured-workloads/access-approval/docs/overview) (2026-09-24) | "require your explicit approval whenever they need to access your Customer Data … Active access approval requests may be revoked at any time … a historical view of all requests that were approved, dismissed, revoked, or expired" | the same shape as our Grant. The data owner approves and can withdraw, and every outcome stays visible, including a request left unanswered (auditor-10.10, 10.11) |
| [C2 Access Transparency](https://docs.cloud.google.com/assured-workloads/access-transparency/docs/overview) (2026-09-24) | "log entries include details such as the affected resource and action, the time of the action, the reason for the action, and information about the accessor" | each read inside a Grant names the resource, the time, the Grant's reason and the accessor (auditor-10.5) |
| [C1 Cloud Audit Logs](https://docs.cloud.google.com/logging/docs/audit) (2026-09-30) | "Log entries written by Cloud Audit Logs are immutable." Admin Activity logs "are always written; you can't configure, exclude, or disable them". "Except for BigQuery, Data Access audit logs are disabled by default … you must explicitly enable them." | our Audit trail is append-only and nobody can switch it off (auditor-10.16). Infrastructure reads of Firestore and Cloud Storage are invisible until hosting enables Data Access logs (Conflicts, item 6) |
| [H1 GitHub audit log](https://docs.github.com/en/organizations/keeping-your-organization-secure/managing-security-settings-for-your-organization/reviewing-the-audit-log-for-your-organization) (undated) | qualifiers `actor:`, `action:`, `operation:` and `created:`, with ISO 8601 times plus "a UTC offset ( +00:00 )"; "you cannot search for entries using text"; "By default, only events from the past three months are displayed"; export "as JSON data or … CSV" following the current filters; hard limits of "100 MB compressed file, or 10 minutes export processing time" | structured filters that state themselves, explicit offsets, and an export that equals the filtered view (auditor-10.27, 10.32) |
| [M1 Microsoft Purview export](https://learn.microsoft.com/en-us/purview/audit-log-export-records) (2026-06-19) | the CSV has "a column named AuditData … formatted as a JSON object", which the reader splits with Power Query. Past the row limit, "the exported .csv file doesn't include all results and might omit some audit logs" | today's pain is detail buried in JSON and silent truncation. Ours has one column per field and never ships a file that is silently partial (auditor-10.32, 10.33) |
| [O1 OWASP Logging Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Logging_Cheat_Sheet.html) | record "when, where, who and what"; keep event time apart from logging time for devices "only periodically or intermittently online"; exclude "Sensitive personal data … e.g. health"; "Build in tamper detection"; "All access to the logs must be recorded and monitored"; always log "Authorization (access control) failures", "Data import and export including screen-based reports" and "personal data usage consent"; "It should not be possible to completely deactivate application logging" | the event fields, the refused attempts, the Auditor's own exports and the hash chain (auditor-10.5, 10.15, 10.37, 9.6) |
| [N1 NIST SP 800-53 Rev 5.2.0](https://raw.githubusercontent.com/usnistgov/oscal-content/main/nist.gov/SP800-53/rev5/json/NIST_SP-800-53_rev5_catalog.json) (2026-05-11) | AU-3: "What type of event … When … Where … Source … Outcome … Identity"; AU-7(b): "Does not alter the original content or time ordering of audit records"; AU-9: protect audit information "from unauthorized access, modification, and deletion"; AU-9(4): access by a subset of privileged users; AU-10: "irrefutable evidence"; AC-2(7)(b)–(c): "Monitor privileged role or attribute assignments … Monitor changes to roles"; AC-5: separation of duties | the event schema, an order-preserving export, Auditor-only read access, role history and separation checks (auditor-10.24 to 10.26) |
| [U1 ICO breach guide](https://ico.org.uk/for-organisations/report-a-breach/personal-data-breach/personal-data-breaches-a-guide/) | "You must also keep a record of any personal data breaches, regardless of whether you are required to notify" | breach evidence is needed even when nothing is notified (Conflicts, item 2) |
| [A1 AccessOwl](https://www.accessowl.com/blog/user-access-reviews-best-practices) (2026-03-19, vendor) | "manual exports from each app, consolidating spreadsheets"; "Don't forward the raw export … reviewers default to 'looks fine'"; "record who reviewed it, when, what they decided … and why. Keep the original export too"; "That cover sheet is what the auditor reads first"; "We run our reviews quarterly" | context instead of raw rows, and a report whose first page summarises (auditor-10.36). A review sign-off is a write (Conflicts, item 3) |

### 1.3 The Auditor's day, the place, the moments of trust

**Tasks they repeat**
- A daily glance at integrity and anomalies. The cadence is an `assumption`; NIST AU-6 leaves the review frequency to the organisation.
- A periodic review of Grants and Roles. A1 reviews quarterly, and K3 Art. 8(4) requires periodic reports.
- Checking that rights requests close within 30 days (K2 Art. 3).
- Keeping the records of processing current (K2 Art. 32(3)(f), 33(3); E1 Art. 9(6)).

**Tasks that arrive as events**
- **A regulator asks for records** "upon request" (K2 Art. 33(4); GDPR Art. 30(4); E1 Art. 4(12)).
- **An eater complains.** In KSA, a complaint may reach SDAIA "within a period not exceeding (90) days from the date when the incident occurred or when the Data Subject became aware of it" (K2 Art. 37(1)), so the Audit trail must answer about events months old.
- **A suspected breach starts a 72-hour clock** (K2 Art. 24(1); E1 Art. 7; GDPR Art. 33(1)). The KSA and GDPR texts count hours, with no business-day carve-out. Only Egypt counts business days, for telling data subjects ("three business days"). The clock runs through weekends and holidays.

**Where and on what**
- At a desk, in a browser, with keyboard and mouse and a spreadsheet beside it. That is the workflow every benchmark serves with CSV and JSON exports (H1, M1, A1).
- Sometimes on a phone, to check one anomaly (`assumption`; the profile proves the console at ~390 px).
- On office broadband (`assumption`). Large exports run as background jobs, because the benchmarks hit size and time limits (H1: 100 MB / 10 min).

**Language**
- The console is English and Arabic, right to left (profile §0 line 5).
- The texts read here were SDAIA's English PDFs and a law firm's Arabic–English dual text. That the Arabic texts prevail is an `assumption`; none of the opened documents says so.
- Whether a regulator wants Arabic column labels in an export is also an `assumption`. Machine keys stay stable either way.

**What they use today and dislike** (inferred from the documented workarounds)
- detail hidden in a JSON column;
- exports silently cut at a limit;
- default views that hide older events;
- no free-text search;
- spreadsheets stitched from many exports;
- reviewers who "default to 'looks fine'".

**The moments that decide trust**
1. The file given to a regulator matches the screen and can be verified: rows, hash, filters and the as-of event.
2. An anomaly is specific: a rule, a count, and the events behind it.
3. Under the 72-hour clock, "which accounts, how many" takes minutes.
4. No one can edit the Audit trail, the Platform admin included.

### 1.4 Findings carried from research cycle 1 (as corrected by the refuters)

- **R2:** third-party AI is named in the consent.
- **R3:** withdrawal is easy, and nothing is paywalled behind consent.
- **R4:** deletion happens in the app, and Sign in with Apple tokens are revoked.
- **R7 and P30:** an app cannot tell whether a Health read was denied.
- **R16 and R22:** adults only, with full legal capacity.
- **R20–R26:** Saudi scope, sensitive health data, consent, destruction including backups, 72 hours, impact assessment, health controls, the DPO, transfers.
- **R28:** Egypt.
- **R29:** as corrected.
- **R30–R31:** GDPR.
- **P4:** floating `-latest` aliases exist.
- **P11 as corrected:** "Endpoints don't guarantee data residency"; Gemini 3.8 Flash runs only on `global` and the US/EU multi-regions.
- **P29:** HealthKit writes food as correlations.

---

## 2 · Goals

1. **G1 — No diary access without the eater's yes.** Every staff read of a diary happened inside a Grant while it was **Active**. Every other read, and every write, was refused and logged (FR-081; K1 Art. 23(1); K2 Art. 26(3)–(4); D2 "reads are allowed only while Active; writes never").
2. **G2 — Consent that can be proved.** Each Consent per purpose, plus the age confirmation, with text version, time, method and the effect of a withdrawal (K2 Arts. 11–12; GDPR Art. 7; FR-076, FR-079).
3. **G3 — Deletions that finished.** Completed everywhere within 30 days, leaving a completion record without identifiers (FR-078, NFR-13, AT-29; R4, R23, R31).
4. **G4 — Who changed the rules.** Policy, Registry, reference records and Roles: who proposed, who approved, when, why, and what was in force at any moment (FRD §3.3, §16.4, FR-080/081; D2; N1 AC-2(7)).
5. **G5 — A complete, verifiable extract, quickly** (K2 Art. 33(4); GDPR Arts. 30(4), 33(5); E1 Art. 4(12)).
6. **G6 — What must never happen is checked continuously.**

---

## 3 · Journeys (high to low)

### Journey 10 — Review who touched diaries and who changed the rules (WF-10, read side)

| step | what the Auditor does | stories |
|---|---|---|
| 10-A Arrive | sign in; the Audit trail overview (chain, anomalies, Active Grants) | 10.1 |
| 10-B Grants | list; one Grant's life; the request as the eater saw it; what was read; the eater's decision; Declined, Expired, Unanswered, Withdrawn, Ended; reads without a Grant; writes refused | 10.2–10.13 |
| 10-C Never-events | Anomalies; chain verification; read-only for everyone; other roles kept out | 10.14–10.17 |
| 10-D Versions | Policy history and in force at a moment; Registry history with Shadow, Canary, Rollout and Rolled back; kill switch; Food, Tier B recipe record and Alias approvals | 10.18–10.23 |
| 10-E Roles | holders now; history and held-at; separation of duties | 10.24–10.26 |
| 10-F Find and hand over | filter and search; invalid input; time; large and slow results; offline; export; export in parts; account lookup; breach scoping; period report; own activity | 10.27–10.37 |
| 10-G Fit | Arabic RTL; phone width; keyboard, screen reader and zoom | 10.38–10.40 |

### Journey 9 — Review consent and deletion evidence (WF-9, read side)

| step | what the Auditor does | stories |
|---|---|---|
| 9-A Consents and age | per purpose; one account's history; the exact text; one purpose per record; withdrawal effect (AT-29); retries and offline time; Health honesty; raw evidence never opened; the 18+ confirmation and the age gate | 9.1–9.9 |
| 9-B Privacy jobs | the 30-day clock; the deletion completion record; a failing step; a deleted account in the Audit trail; an eater's export job | 9.10–9.14 |
| 9-C Retention and records | retention runs; Records of processing; Consent evidence export; a missing consent text | 9.15–9.18 |

### Places (from D2)

The Auditor works in the console sections **Audit trail · Grants · Policy · Registry · Foods · Recipes · Aliases · Roles · Jobs · Settings**, all read-only.

Inside **Audit trail**, this lens proposes the views **Events · Anomalies · Consents · Summary · Records of processing · Exports**. These view names are not yet in D2 (Conflicts, item 15). Admin API paths (`/v1/admin/…`) are proposals in the style of FRD §18. Error codes are D2's: `FORBIDDEN`, `GRANT_REQUIRED`, `GRANT_NOT_ACTIVE`, `CONSENT_REQUIRED`, `AGE_REQUIREMENT`, `NOT_FOUND`, `VALIDATION_ERROR`.

### The audit event (the inside, proposed from N1 AU-3 and O1)

**Every event carries:**
- `event_id`, which is the gap-free sequence number;
- `occurred_at_utc` (event time) and `received_at_utc`;
- `actor`: a staff id with the role held at that moment, an account id for the eater, or a job name for the system;
- `action`;
- `object`;
- `account_id` (when an eater is involved);
- `outcome` (Allowed · Refused · Done · Failed) and `code`, the D2 error code when refused;
- `detail` (for example, the Grant's state at refusal);
- `request_id`;
- `surface` (app · console · API · job) and the app version;
- `prev_hash` and `hash`.

**Never carried:** food names, quantities, calories, photos, audio, profile values, or eater names and emails (FRD §19.2; O1 "Data to exclude").

**Actions used below:**
- **Grants:** `grant.requested`, `grant.approved`, `grant.declined`, `grant.unanswered`, `grant.read`, `grant.read_refused`, `grant.write_refused`, `grant.expired`, `grant.withdrawn`, `grant.ended`.
- **Eater and privacy:** `age.confirmed`, `age.refused`, `consent.given`, `consent.withdrawn`, `privacy_job.export.requested|completed`, `privacy_job.deletion.requested|completed`.
- **Rules and reference:** `policy.version.proposed|approved`, `registry.version.proposed`, `registry.stage.changed`, `registry.kill_switch.on|off`, `food.version.proposed|approved`, `alias.proposed|approved`.
- **Roles and staff:** `role.assigned|removed`, `access.refused`, `staff.signed_in`.
- **The Auditor's own work:** `account.lookup`, `audit_trail.queried`, `audit_trail.exported`, `audit_trail.verified`.

Retention runs and the steps of a Privacy job are **Job records** (Jobs), not Audit trail events. The Audit trail records a Privacy job's request and completion; Jobs holds its steps.

### Fixtures (synthetic; every acceptance line below uses only these)

**Test clock.** Lines are read at **2026-10-05T09:00:00Z** unless a line says otherwise. The console zone is **Asia/Riyadh (+03:00)**. Ids follow the support lens where that lens defines the same thing (Grant `grant_31f0`, account `acct_9c41e2`, `staff_*`).

| fixture | value |
|---|---|
| staff and roles | `staff_mona` "Mona K." Support agent · `staff_omar` "Omar S." Support agent · `staff_tariq` Support agent from 2026-09-05 to 2026-09-25 · `staff_dina` Nutrition approver · `staff_yara` Nutrition approver from 2026-09-10 · `staff_ali` Platform admin · `staff_hana` Auditor · `staff_sod_seed`, holding Support agent **and** Platform admin, written **directly into the role store by the test seed** on 2026-09-30, below the roles API. admin-10.59 refuses this combination at save, so the product cannot produce it; it exists to prove the detective check |
| eater accounts | `acct_9c41e2` (E1 in the support lens; app in Arabic; Sign in with Apple, relay address `r7k2q9x4@privaterelay.appleid.com`, synthetic) · `acct_3f88a1` · `acct_c2d7e5` · `acct_8e14d9` · `acct_d40e17` (deleted) · `acct_b81c40` (deleted) · `acct_e5a930`, `acct_f61c22`, `acct_0a7b55` (deletion open) |
| Grant request fields | as the support lens defines them: reason code (`sync_missing_entry`, `report_mismatch`, `unit_calculation`, `activity_import`, `other` + note), diary days, areas, duration (1 h · 4 h · 24 h), case reference. The eater sees request wording version `grant-req-1` in their app language. The request window before **Unanswered** is **72 h** (support lens A6, `assumption`) |
| consent text versions | `age-1` · `diary-1` · `c-ai-3` (published 2026-08-25) · `c-ai-4` (published 2026-09-24) · `health-1` · `research-1`; each in English and Arabic |
| Policy | v1: proposed and approved by `staff_dina` alone (the sole Nutrition approver at the time); effective 2026-09-02T00:00:00+03:00. v2: proposed by `staff_yara`, approved by `staff_dina`; effective 2026-10-05T00:00:00+03:00; changes the energy-mismatch threshold ">10 % and >10 kcal" → ">12 % and >10 kcal" (synthetic). Unchanged in both: calorie floor 1,200 kcal, hard stop 1,000 kcal, raw scans 30 days unless saved, temporary audio 24 h |
| Registry (task `meal_photo`, names from the admin lens) | `meal_photo@v6`: `gemini-3.8-flash`, prompt v11, schema `analysis.v3`; the launch baseline. `meal_photo@v7`: `gemini-3.8-flash`, prompt v12, schema `analysis.v3`; Proposed → Shadow → Canary 5 % → Rollout 100 % → Rolled back to v6 |
| reference records | Tier B recipe record `rec_fm_eg` v1 "فول مدمس — EG (olive oil, cumin)": licence "first-party calculation (approver-built)", evidence `ev_1101` (weighed ingredients) and `ev_1102` (cooked-yield weight). Alias `al_saqai` "صقعي", dialect Gulf (stored `afb`), transliteration "Saqai", English "Saqai date" → Food "Dates, Saqai". Alias `al_laban_eg` "لبن", dialect EG (stored `arz`) → Food "Milk, whole" |
| Privacy jobs (Jobs) | `job_exp_4402`: export for `acct_9c41e2`, requested 2026-09-10T12:00:00Z, Completed 12:04:00Z. `job_del_2201` (`acct_b81c40`, reference `DEL-26-0902-A7K1`): requested 2026-09-02T06:00:00Z, Completed 2026-09-16T06:30:00Z. `job_del_2204` (`acct_e5a930`, `DEL-26-0904-B9T2`): requested 2026-09-04T09:00:00Z, Running. `job_del_2209` (`acct_f61c22`, `DEL-26-0909-C2M8`): requested 2026-09-09T09:00:00Z, Running. `job_del_2212` (`acct_d40e17`, `DEL-26-0912-F3P5`): requested 2026-09-12T08:00:00Z, Completed 2026-09-20T08:00:00Z. `job_del_2228` (`acct_0a7b55`, `DEL-26-0928-D4Q6`): requested 2026-09-28T10:00:00Z, Running; its step "photos and audio deleted" Failed at 2026-09-28T10:05:00Z and 2026-10-05T08:05:00Z (storage timeout); next retry 2026-10-05T09:05:00Z |
| backups | the backup lifecycle is **30 days** (`assumption`). FRD §17.2 and NFR-13 require it to be disclosed but give no period; counsel and hosting set it |
| withdrawal effect (Jobs record for event 37) | AI requests for `acct_9c41e2` between 07:45:00Z on 09-20 and 20:10:00Z on 09-25: 0 · Pending Analyses cancelled: 1 · photos and audio held for analysis deleted: 3 (07:45:04Z) · private cached analysis deleted: 1 (07:45:03Z) · queued uploads purged: 2 · prepared export files deleted: 1, `job_exp_4402`'s file (07:45:05Z) |
| retention (Jobs → Retention) | runs **hourly**, deleting audio older than 23 h and raw scans older than 29 d 23 h, so nothing passes FR-078's 24 h / 30 days (a chosen default derived from FR-078; a daily run could leave audio nearly 48 h old). Run R-0805 at 2026-10-05T08:00:00Z: raw scans deleted 2, audio deleted 1, oldest remaining raw scan 29 d 22 h, oldest audio 22 h 10 min, Policy v2. **Separate fixture R-gap:** clock 2026-10-05T06:30:00Z, last run 03:00:00Z, one audio file aged 24 h 20 min |
| bulk fixtures (separate emulator datasets) | **B1**: 25,000 synthetic events, seed 42, 2026-07-01 to 2026-09-30, actors from the staff above. **B2**: 1,200,000 synthetic events, seed 43, same period |
| separate fixture X | one Consent record for `acct_3f88a1` pointing to text version `c-ai-2`, which is absent from the store |

**The seeded Audit trail** — exactly these 80 events. Live actions during a test append from event 81. Times are UTC.

| # | time | actor | action · object · detail | outcome · code |
|---|---|---|---|---|
| 1 | 09-01 06:00:00 | system bootstrap | role.assigned · Platform admin → staff_ali | Done |
| 2 | 09-01 06:05:00 | staff_ali | role.assigned · Support agent → staff_mona | Done |
| 3 | 09-01 06:06:00 | staff_ali | role.assigned · Support agent → staff_omar | Done |
| 4 | 09-01 06:07:00 | staff_ali | role.assigned · Nutrition approver → staff_dina | Done |
| 5 | 09-01 06:08:00 | staff_ali | role.assigned · Auditor → staff_hana | Done |
| 6 | 09-01 07:00:00 | staff_dina | policy.version.proposed · v1 | Done |
| 7 | 09-01 07:30:00 | staff_dina | policy.version.approved · v1 · proposer = approver, "sole Nutrition approver" · effective 2026-09-02T00:00:00+03:00 | Done |
| 8 | 09-01 08:00:00 | staff_ali | registry.stage.changed · meal_photo@v6 → Rollout 100 % · "launch baseline" | Done |
| 9 | 09-01 18:20:00 | acct_9c41e2 | age.confirmed · age-1 · onboarding age question | Done |
| 10 | 09-01 18:21:00 | acct_9c41e2 | consent.given · Diary processing · diary-1 · onboarding | Done |
| 11 | 09-01 18:22:10 | acct_9c41e2 | consent.given · Send photos, voice and text to Google's AI (Gemini) · c-ai-3 · onboarding | Done |
| 12 | 09-01 18:25:00 | acct_9c41e2 | consent.given · Health: read workouts · health-1 | Done |
| 13 | 09-01 18:25:01 | acct_9c41e2 | consent.given · Health: read active energy · health-1 | Done |
| 14 | 09-01 18:25:02 | acct_9c41e2 | consent.given · Health: read body mass · health-1 | Done |
| 15 | 09-01 18:25:03 | acct_9c41e2 | consent.given · Health: write food (energy and macros as food correlations, P29) · health-1 | Done |
| 16 | 09-01 18:26:00 | acct_9c41e2 | consent.given · Optional research · research-1 | Done |
| 17 | 09-02 06:00:00 | acct_b81c40 | privacy_job.deletion.requested · job_del_2201 | Done |
| 18 | 09-04 09:00:00 | acct_e5a930 | privacy_job.deletion.requested · job_del_2204 | Done |
| 19 | 09-05 06:00:00 | staff_ali | role.assigned · Support agent → staff_tariq | Done |
| 20 | 09-09 09:00:00 | acct_f61c22 | privacy_job.deletion.requested · job_del_2209 | Done |
| 21 | 09-10 06:00:00 | staff_ali | role.assigned · Nutrition approver → staff_yara | Done |
| 22 | 09-10 09:00:00 | staff_mona | grant.requested · grant_27b4 · acct_d40e17 · report_mismatch · days 09-08–09-09 · Entries and day reports · 1 h · CASE-1101 | Done |
| 23 | 09-10 09:10:00 | acct_d40e17 | grant.approved · grant_27b4 · Active until 10:10:00 | Done |
| 24 | 09-10 09:15:00 | staff_mona | grant.read · grant_27b4 · Day report 2026-09-09 | Allowed |
| 25 | 09-10 10:10:00 | system | grant.expired · grant_27b4 | Done |
| 26 | 09-10 12:00:00 | acct_9c41e2 | privacy_job.export.requested · job_exp_4402 | Done |
| 27 | 09-10 12:04:00 | system | privacy_job.export.completed · job_exp_4402 · acct_9c41e2 | Done |
| 28 | 09-12 08:00:00 | acct_d40e17 | privacy_job.deletion.requested · job_del_2212 | Done |
| 29 | 09-12 09:00:00 | staff_yara | food.version.proposed · rec_fm_eg v1 | Done |
| 30 | 09-12 09:40:00 | staff_dina | food.version.approved · rec_fm_eg v1 · licence, ev_1101, ev_1102 | Done |
| 31 | 09-12 10:00:00 | staff_dina | alias.proposed · al_saqai · Gulf | Done |
| 32 | 09-12 10:15:00 | staff_yara | alias.approved · al_saqai · Gulf | Done |
| 33 | 09-12 10:20:00 | staff_dina | alias.proposed · al_laban_eg · EG | Done |
| 34 | 09-12 10:30:00 | staff_yara | alias.approved · al_laban_eg · EG | Done |
| 35 | 09-15 10:00:00 | staff_ali | access.refused · role.assign Auditor → staff_ali (self-change, admin-10.61) | Refused · FORBIDDEN |
| 36 | 09-16 06:30:00 | system | privacy_job.deletion.completed · job_del_2201 | Done |
| 37 | 09-20 07:45:00 | acct_9c41e2 | consent.withdrawn · Google's AI (Gemini) · c-ai-3 · Settings | Done |
| 38 | 09-20 08:00:00 | system | privacy_job.deletion.completed · job_del_2212 | Done |
| 39 | 09-22 06:05:30 | acct_9c41e2 | consent.withdrawn · Optional research · research-1 · Settings · made on the device 2026-09-21T22:40:00Z, delivered twice | Done |
| 40 | 09-22 08:00:00 | staff_ali | registry.version.proposed · meal_photo@v7 | Done |
| 41 | 09-23 08:00:00 | staff_ali | registry.stage.changed · v7 → Shadow · "candidate gets copies; v6 answers" | Done |
| 42 | 09-25 08:00:00 | staff_ali | registry.stage.changed · v7 → Canary 5 % · "shadow agreement within threshold" | Done |
| 43 | 09-25 15:00:00 | staff_ali | role.removed · Support agent ← staff_tariq · "left the team" | Done |
| 44 | 09-25 20:10:00 | acct_9c41e2 | consent.given · Google's AI (Gemini) · c-ai-4 · Settings | Done |
| 45 | 09-27 08:00:00 | staff_ali | registry.stage.changed · v7 → Rollout 100 % · "canary metrics within threshold" | Done |
| 46 | 09-27 10:12:00 | staff_ali | registry.kill_switch.on · meal_photo · "AI timeouts" | Done |
| 47 | 09-27 10:47:00 | staff_ali | registry.kill_switch.off · meal_photo | Done |
| 48 | 09-27 11:30:00 | staff_ali | registry.stage.changed · v7 → Rolled back; v6 live · "photo drafts missing oil items" | Done |
| 49 | 09-28 10:00:00 | acct_0a7b55 | privacy_job.deletion.requested · job_del_2228 | Done |
| 50 | 09-29 07:30:00 | staff_yara | policy.version.proposed · v2 | Done |
| 51 | 09-29 08:00:00 | staff_dina | policy.version.approved · v2 · effective 2026-10-05T00:00:00+03:00 | Done |
| 52 | 10-01 10:05:00 | staff_mona | grant.requested · grant_31f0 · acct_9c41e2 · sync_missing_entry · days 09-28–09-30 · Entries and day reports + My Units · 1 h · CASE-1182 | Done |
| 53 | 10-01 10:09:00 | staff_mona | grant.requested · grant_40aa · acct_3f88a1 · activity_import · days 09-29–09-30 · Activity · 4 h · CASE-1183 | Done |
| 54 | 10-01 10:20:00 | acct_9c41e2 | grant.approved · grant_31f0 · Active until 11:20:00 · in-app, Settings → Privacy → Grants · app 1.0.3, iOS 26.1 | Done |
| 55 | 10-01 10:24:00 | staff_mona | grant.read · grant_31f0 · Entries and day reports · Day 2026-09-29 · 7 Entries | Allowed |
| 56 | 10-01 10:26:30 | staff_mona | grant.read · grant_31f0 · Entry en_9921 | Allowed |
| 57 | 10-01 10:31:00 | staff_mona | grant.read · grant_31f0 · My Units · 12 Unit versions | Allowed |
| 58 | 10-01 10:40:00 | staff_mona | grant.write_refused · grant_31f0 · POST /v1/consumption | Refused · FORBIDDEN |
| 59 | 10-01 11:20:00 | system | grant.expired · grant_31f0 | Done |
| 60 | 10-01 11:20:01 | staff_mona | grant.read_refused · grant_31f0 · state Expired | Refused · GRANT_NOT_ACTIVE |
| 61 | 10-01 12:00:00 | staff_omar | grant.requested · grant_31f9 · acct_9c41e2 · report_mismatch · day 09-30 · Entries and day reports · 1 h · CASE-1185 | Done |
| 62 | 10-01 12:06:00 | acct_9c41e2 | grant.declined · grant_31f9 | Done |
| 63 | 10-01 12:10:00 | staff_omar | grant.read_refused · grant_31f9 · state Declined | Refused · GRANT_NOT_ACTIVE |
| 64 | 10-02 08:00:00 | staff_omar | grant.requested · grant_52c3 · acct_c2d7e5 · unit_calculation · day 10-01 · My Units · 4 h · CASE-1191 | Done |
| 65 | 10-02 08:05:00 | acct_c2d7e5 | grant.approved · grant_52c3 · Active until 12:05:00 | Done |
| 66 | 10-02 08:10:00 | staff_omar | grant.read · grant_52c3 · My Units · 5 Unit versions | Allowed |
| 67 | 10-02 08:30:00 | acct_c2d7e5 | grant.withdrawn · grant_52c3 · by the eater | Done |
| 68 | 10-02 08:31:00 | staff_omar | grant.read_refused · grant_52c3 · state Withdrawn | Refused · GRANT_NOT_ACTIVE |
| 69 | 10-02 13:00:00 | staff_mona | grant.requested · grant_5d10 · acct_9c41e2 · sync_missing_entry · days 10-01–10-02 · Entries and day reports · 1 h · CASE-1192 | Done |
| 70 | 10-02 13:02:00 | acct_9c41e2 | grant.approved · grant_5d10 · Active until 14:02:00 | Done |
| 71 | 10-02 13:05:00 | staff_mona | grant.read · grant_5d10 · Entries and day reports · Day 2026-10-01 · 4 Entries | Allowed |
| 72 | 10-02 13:20:00 | staff_mona | grant.ended · grant_5d10 · by the Support agent | Done |
| 73 | 10-03 09:00:00 | staff_omar | grant.requested · grant_6e21 · acct_9c41e2 · report_mismatch · day 10-02 · Entries and day reports · 1 h · CASE-1195 | Done |
| 74 | 10-03 09:03:00 | acct_9c41e2 | grant.approved · grant_6e21 · Active until 10:03:00 · command delivered 3 times | Done |
| 75 | 10-03 10:03:00 | system | grant.expired · grant_6e21 | Done |
| 76 | 10-03 15:00:00 | staff_omar | grant.read_refused · no Grant · GET /v1/reports/day for acct_8e14d9 | Refused · GRANT_REQUIRED |
| 77 | 10-03 15:01:00 | staff_ali | access.refused · GET /v1/reports/day for acct_8e14d9 | Refused · FORBIDDEN |
| 78 | 10-03 15:05:00 | staff_ali | access.refused · GET Analysis an_7781 photo · acct_9c41e2 (raw evidence) | Refused · FORBIDDEN |
| 79 | 10-04 10:09:00 | system | grant.unanswered · grant_40aa · 72 h request window closed | Done |
| 80 | 10-04 10:30:00 | staff_mona | grant.read_refused · grant_40aa · state Unanswered | Refused · GRANT_NOT_ACTIVE |

**Derived facts used below** (at the test clock):
- **No Grant is Active.**
- **Allowed reads:** 6 (events 24, 55, 56, 57, 66, 71).
- **Refused reads:** 5 (events 60, 63, 68, 76, 80).
- **Refused writes:** 1 (event 58).
- **Other refusals:** 3 (events 35, 77, 78).
- **Open anomaly rules:** 3 — Support agent held with Platform admin (`staff_sod_seed`); roles held with no assignment event (`staff_sod_seed`); deletions open more than 30 days (`job_del_2204`, 31 days).
- **The automated chain check** last ran at 2026-10-05T08:30:00Z and found events 1–80 intact.

---

## 4 · Micro stories with acceptance

Layer tags: **/m** module test · **/s** system (API plus storage, on the emulator) · **/r** runtime, observable in the served admin console in a browser or over the API by HTTP.

### Journey 10 — WF-10, read side

**auditor-10.1 · The Audit trail overview on arrival**
As the Auditor, I land on the Audit trail overview when I sign in, so that in seconds I know whether the chain is intact, how many anomaly rules are open and how many Grants are Active.
- /r Given the seeded Audit trail (events 1–80) at the test clock When `staff_hana` signs in to the admin console Then:
  - the first screen is **Audit trail**;
  - its header reads "Chain intact through event 80 · checked 08:30:00Z (11:30:00 +03:00)", "Active Grants 0" and "Anomalies 3", each linked to its list;
  - the newest rows are event 82, `audit_trail.queried` (this view's own load; a query event is written before its results are read), and event 81, `staff.signed_in` by `staff_hana`;
  - no edit, delete, approve, assign or request control is on the page.
- /r Given the Audit trail API is stopped (fault injection) When `staff_hana` opens Audit trail Then the page says "The Audit trail could not be loaded. Nothing is shown rather than a partial trail." with **Retry**, and no count shows 0.
- /r Given the first second of loading When Audit trail is requested Then a skeleton of the header and table shows, never a blank page or zero counts.

**auditor-10.2 · The Grants list**
As the Auditor, I list Grants with state, requester, account, window and counts, so that I can review all diary access in a period at a glance.
- /r Given the seeded Audit trail When `staff_hana` opens **Grants** for 2026-10-01T00:00+03:00 – 2026-10-04T00:00+03:00 Then 6 rows show, newest requested first. Each row reads: id · state (a word plus an icon, never colour alone) · requested by · account · reads allowed · reads refused · writes refused.
  - `grant_6e21` · Expired · staff_omar · acct_9c41e2 · 0 · 0 · 0
  - `grant_5d10` · Ended · staff_mona · acct_9c41e2 · 1 · 0 · 0
  - `grant_52c3` · Withdrawn · staff_omar · acct_c2d7e5 · 1 · 1 · 0
  - `grant_31f9` · Declined · staff_omar · acct_9c41e2 · 0 · 1 · 0
  - `grant_40aa` · Unanswered · staff_mona · acct_3f88a1 · 0 · 1 · 0
  - `grant_31f0` · Expired · staff_mona · acct_9c41e2 · 3 · 1 · 1
- /r Given the range 2026-09-15T00:00+03:00 – 2026-09-20T00:00+03:00 When the list loads Then it says "No Grants were requested between 15 Sep 2026 00:00 and 20 Sep 2026 00:00 (+03:00)" and offers **Widen to the last 90 days**, which lists 7 Grants.

**auditor-10.3 · One Grant's whole life on one page** *(shared: Support agent requests and reads; eater approves in Settings → Privacy → Grants. This is the WF-10 done-when)*
As the Auditor, I open a Grant and see its request, the eater's decision, every read, every refused attempt and its end in one timeline, so that I can show the eater opened the door and staff stayed inside the time box.
- /r Given `grant_31f0` When `staff_hana` opens **Grants → grant_31f0** Then:
  - the timeline shows 8 events in order: Requested (10:05:00Z) · Approved by the eater, Active from 10:20:00Z · Read (10:24:00Z) · Read (10:26:30Z) · Read (10:31:00Z) · Write refused (10:40:00Z, FORBIDDEN) · Expired (11:20:00Z) · Read refused (11:20:01Z, GRANT_NOT_ACTIVE);
  - each time is shown in UTC and +03:00;
  - the header reads "Active 10:20:00Z–11:20:00Z (1 h) · 3 reads allowed · 1 read refused · 1 write refused".
- /s Given the same When `GET /v1/admin/grants/grant_31f0` is called with `staff_hana`'s token Then it returns the same 8 events (ids 52, 54, 55, 56, 57, 58, 59, 60) in that order.
- /s Given a fresh emulator When the support and eater flows run (request, the eater approves, three reads, one write attempt, the clock passes expiry, one read attempt) Then the Audit trail holds exactly 8 events for that Grant, with the same actions in the same order.

**auditor-10.4 · The request as the eater saw it**
As the Auditor, I read a request's reason, days, areas, duration and case exactly as the eater saw them, so that I can judge whether the access was necessary and proportionate.
- /r Given `grant_31f0` When `staff_hana` opens its Request card Then it shows:
  - reason `sync_missing_entry` "An entry is missing or appears twice";
  - days 2026-09-28 to 2026-09-30;
  - areas "Entries and day reports", "My Units";
  - 1 h, case `CASE-1182`, "staff_mona · Support agent (role at the time)";
  - "Shown to the eater in Arabic, wording grant-req-1" with the reason as the eater read it: «إدخال مفقود أو ظاهر مرتين».
- /s Given `grant.requested` (event 52) is stored When `grant-req-2` is later published Then the event still names `grant-req-1`, and the card renders `grant-req-1`, not the newer wording or a re-translation.

**auditor-10.5 · What was read, never the food**
As the Auditor, I see what each read touched without seeing the diary, so that I can account for access without becoming a second reader of health data.
- /r Given event 55 When `staff_hana` expands it Then it shows "Entries and day reports · Day 2026-09-29 · 7 Entries returned · Allowed · request id", with no food names, quantities, calories, photos or audio, and the note "Content is not kept in the Audit trail".
- /m Given the audit-event builder and a read of a Day whose Entries include "cheese bite ×3, 150 kcal" When it builds the event Then the event has only allow-listed fields, and no field contains "cheese", "kcal" or any Entry value (FRD §19.2; O1).

**auditor-10.6 · Only the eater can approve**
As the Auditor, I see who made each decision, from where and when, so that I can prove the eater, not staff, said yes.
- /r Given events 54 and 62 When `staff_hana` opens `grant_31f0` and `grant_31f9` Then the Decision cards read:
  - "Approved by the eater · in the app, Settings → Privacy → Grants · 10:20:00Z · app 1.0.3, iOS 26.1";
  - "Declined by the eater · 12:06:00Z";
  - the actor on both is the account id, never a staff id.
- /r Given `grant_6e21`, whose approve command was delivered 3 times (the AT-10 retry pattern), When `staff_hana` opens it Then exactly one "Approved" event (74) shows.
- /s Given a new Grant in state Requested on a fresh emulator When `POST /v1/grants/{id}/approve` is sent with the token of `staff_mona`, `staff_ali` or `staff_hana` Then each returns 403 `FORBIDDEN`, the Grant stays Requested, and an `access.refused` event names that staff id (FR-081; D2 Grant states).

**auditor-10.7 · A declined Grant opens nothing**
As the Auditor, I see that a declined Grant gave no access, so that "no" is final.
- /r Given `grant_31f9` (Declined at 12:06:00Z) When `staff_hana` opens it Then the timeline shows Requested · Declined by the eater · Read refused (12:10:00Z, `staff_omar`, GRANT_NOT_ACTIVE, "state Declined"), with reads allowed 0.

**auditor-10.8 · A read after expiry is impossible**
As the Auditor, I see any read after expiry refused and recorded, so that the time box is a fact.
- /r Given `grant_31f0` Expired at 11:20:00Z When `staff_hana` opens event 60 Then it reads "Read refused · grant_31f0 · state Expired · GRANT_NOT_ACTIVE · 11:20:01Z · staff_mona".
- /s Given a 1 h Grant Active from T on a fresh emulator When a read arrives at exactly T + 1 h Then it is refused with GRANT_NOT_ACTIVE, and at T + 1 h − 1 ms it is allowed. The window is half-open, [T, T + 1 h).
- /r Given Anomalies When opened Then "Diary reads allowed after a Grant stopped being Active" shows 0.

**auditor-10.9 · No diary read without a Grant**
As the Auditor, I see that no staff member read a diary without a Grant, so that I can answer "did anyone else look?" with evidence.
- /r Given events 76 and 77 When `staff_hana` filters Events by account `acct_8e14d9` Then two rows show:
  - 15:00:00Z `staff_omar` "Read refused · no Grant · GRANT_REQUIRED";
  - 15:01:00Z `staff_ali` "Refused · FORBIDDEN".
  - Anomalies shows "Diary reads allowed without a Grant: 0".
- /s Given the NFR-07 negative-test suite on a fresh emulator When it calls every diary, report and media endpoint with each staff role and no Grant Then every call is refused (GRANT_REQUIRED for the Support agent, FORBIDDEN for every other role), and each refusal writes exactly one event.
- /m Given the rule "allowed staff diary reads without an Active Grant" and a synthetic Audit trail containing one Allowed `grant.read` whose Grant was Expired When evaluated Then it returns that event id.

**auditor-10.10 · An unanswered request ends as Unanswered**
As the Auditor, I see that a request the eater never answered closes as Unanswered, so that silence is never taken as consent.
- /r Given `grant_40aa` (requested 2026-10-01T10:09:00Z; the request window is 72 h, an `assumption` from support lens A6) When `staff_hana` opens it Then the timeline shows:
  - Requested;
  - Unanswered at 2026-10-04T10:09:00Z, "request window closed (72 h)";
  - Read refused at 10:30:00Z by `staff_mona`, GRANT_NOT_ACTIVE, "state Unanswered".

**auditor-10.11 · The eater withdraws access mid-window** *(shared with eater; traced to D2 Grant state "Withdrawn (by the eater)" and C3 "revoked at any time")*
As the Auditor, I see the eater's withdrawal end access at once, so that a change of mind is honoured to the second.
- /r Given `grant_52c3` (Active from 08:05:00Z for 4 h, withdrawn by the eater at 08:30:00Z) When `staff_hana` opens it Then the timeline shows:
  - Requested · Approved · Read (08:10:00Z) · Withdrawn by the eater (08:30:00Z) · Read refused (08:31:00Z, GRANT_NOT_ACTIVE, "state Withdrawn");
  - the header says "Active 25 min of 4 h".

**auditor-10.12 · The Support agent ends access early** *(shared with Support agent; D2 "Ended (by the support agent)")*
As the Auditor, I see when a Support agent handed access back before the box ran out, so that early, voluntary ends are on the record too.
- /r Given `grant_5d10` (Active from 13:02:00Z for 1 h) When `staff_hana` opens it Then the timeline shows:
  - Requested · Approved · Read (13:05:00Z, Day 2026-10-01, 4 Entries) · Ended by the Support agent `staff_mona` (13:20:00Z);
  - the header says "Active 18 min of 1 h".

**auditor-10.13 · A write inside an Active Grant is refused** *(interaction row "Support → eater diary | read within the Grant | … read-only")*
As the Auditor, I see that a Support agent could not change a diary even while the Grant was Active, so that "read-only" is proven, not promised.
- /r Given event 58 When `staff_hana` opens it from `grant_31f0`'s timeline Then it reads "Write refused · POST /v1/consumption · grant_31f0 (Active) · FORBIDDEN · 10:40:00Z · staff_mona". Anomalies shows "Writes allowed under a Grant: 0".
- /s Given an Active Grant on a fresh emulator When its token is used on `POST /v1/consumption`, `…/corrections`, `…/void`, `POST /v1/units` or `POST /v1/recipes` Then each returns 403 `FORBIDDEN`, the day revision is unchanged, and each writes one `grant.write_refused` event.

**auditor-10.14 · Anomalies: what must stay at zero**
As the Auditor, I open Anomalies and see each never-event rule with its count and the events behind it, so that I check them continuously, not by memory.
- /r Given the seeded Audit trail at the test clock When `staff_hana` opens **Audit trail → Anomalies** Then each rule shows its count and its "last evaluated" time:
  - diary reads allowed after a Grant stopped being Active 0
  - diary reads allowed without a Grant 0
  - writes allowed under a Grant 0
  - Grants approved by anyone but the eater 0
  - chain breaks 0
  - Support agent held with Platform admin or Nutrition approver **1** (`staff_sod_seed`)
  - Auditor held with any other role 0
  - roles held with no assignment event in the Audit trail **1** (`staff_sod_seed`)
  - Policy versions approved by their proposer while the Nutrition approver role had two or more holders 0
  - Registry versions naming a floating alias 0
  - deletions open more than 30 days **1** (`job_del_2204`)
  - AI requests after the AI Consent was withdrawn 0
  - Consent records covering more than one purpose 0
  - Consent records whose text version is missing 0
  - raw evidence opened by staff 0
  - raw scans older than 30 days 0
  - audio older than 24 h 0

  Below the rules, as information rather than anomalies: "Refused reads 5 · refused writes 1 · other refusals 3".
- /r Given the "deletions open more than 30 days" evaluator made to throw (fault injection) When Anomalies loads Then that row reads "Not evaluated — check failed at 09:00:00Z", never 0.
- /m Given each rule and a synthetic Audit trail that violates it once When evaluated Then each rule returns exactly that event id or record id.

**auditor-10.15 · The chain shows any change**
As the Auditor, I verify the hash chain, so that I can tell a regulator nothing was changed or removed.
- /m Given 80 events where `hash(n) = SHA-256(hash(n−1) ‖ canonical(event n))` When event 40's outcome is changed in storage Then `verify()` reports "first break at event 40".
- /r Given the seeded Audit trail, `staff_hana` signed in (event 81) and on the overview (event 82) When she selects **Verify chain** Then progress shows "Checking 82 events…" with **Cancel**. It ends with "Intact · events 1–82", and event 83 `audit_trail.verified` records the result.
- /r Given the separate fixture with event 40 altered in the emulator store When verified Then the header shows "Chain broken at event 40", and Anomalies shows "chain breaks 1".

**auditor-10.16 · Read-only, for everyone**
As the Auditor, I know nobody, including me and the Platform admin, can edit or delete an audit event, so that the Audit trail is evidence.
- /r Given `staff_hana`'s session When she opens any event, Grant, Policy, Registry, Food, Alias or role page Then no edit, delete, approve, assign or request control exists.
- /r Given the token of `staff_hana` or of `staff_ali` When `PUT`, `PATCH` or `DELETE /v1/admin/audit/events/58` is sent Then each returns 403 `FORBIDDEN`, an `access.refused` event names the caller, and Verify chain stays intact.
- /s Given the emulator's storage rules When any client identity writes to an existing audit-event document Then the write is refused; only the server's append path adds events (N1 AU-9; O1).

**auditor-10.17 · Other roles cannot read the Audit trail**
As the Auditor, I know only the Auditor role reads the Audit trail, so that the record of who looked is not itself browsed (N1 AU-9(4)).
- /r Given `staff_mona` (Support agent) is signed in When she opens **Audit trail** by URL Then:
  - the page says "The Audit trail is open to the Auditor role. Your role: Support agent." with a way back;
  - `GET /v1/admin/audit/events` returns 403 `FORBIDDEN`;
  - the attempt appears as `access.refused` by `staff_mona`.

**auditor-10.18 · Policy history: who proposed, who approved, what changed** *(traced to D2 Policy states and "the audit trail records who proposed and who approved")*
As the Auditor, I see every Policy version with proposer, approver, state and changes, so that safety rules change only with an accountable approval (FRD §3.3).
- /r Given v1 and v2 When `staff_hana` opens **Policy → History** Then:
  - v1 reads "Superseded · proposed and approved by staff_dina (sole Nutrition approver at the time) · approved 2026-09-01T07:30:00Z · in effect 2026-09-02T00:00:00+03:00 – 2026-10-05T00:00:00+03:00";
  - v2 reads "In effect · proposed by staff_yara · approved by staff_dina 2026-09-29T08:00:00Z · in effect from 2026-10-05T00:00:00+03:00".
- /r Given v1 and v2 When `staff_hana` selects **Compare** Then only "energy-mismatch threshold >10 % and >10 kcal → >12 % and >10 kcal" is listed as changed; calorie floor 1,200 kcal, hard stop 1,000 kcal, raw scans 30 days and audio 24 h sit under "Unchanged (n)".
- /m Given the rule "approved by its proposer while the role had two or more holders" When it is run on v1 (one holder on 09-01) and on a synthetic v3 proposed and approved by `staff_dina` on 10-04 (two holders) Then v1 passes and v3 is returned.

**auditor-10.19 · Which Policy was in force at a moment**
As the Auditor, I ask which Policy version applied at a time, so that I can answer "what floor applied to this person on 15 September?".
- /r Given v1 and v2 When `staff_hana` enters "In force at" Then:
  - 2026-09-15T12:00+03:00 shows v1 with its values;
  - exactly 2026-10-05T00:00:00+03:00 shows v2;
  - 2026-09-01T12:00+03:00 shows "No Policy version was in effect — v1 took effect 2026-09-02T00:00:00+03:00".
- /s Given the same When `GET /v1/admin/policy/versions?in_force_at=2026-09-15T09:00:00Z` is called Then it returns v1.

**auditor-10.20 · Registry history, through Shadow, Canary, Rollout and Rolled back** *(WF-10 done-when "an admin rolls a model version back")*
As the Auditor, I see every Registry version and stage change with who, when and why, and what was live at any moment, so that I can explain which model processed eaters' data (FRD §16.4).
- /r Given events 8, 40–42, 45 and 48 When `staff_hana` opens **Registry → History** for `meal_photo` Then rows show, in order:
  - v6 Rollout 100 % (launch baseline);
  - v7 Proposed;
  - v7 Shadow;
  - v7 Canary 5 %;
  - v7 Rollout 100 %;
  - v7 Rolled back, "v6 live".
  - Each row shows model id `gemini-3.8-flash`, prompt (v11 or v12), schema `analysis.v3`, `staff_ali`, the time and the reason.
- /r Given **Live at** When `staff_hana` enters:
  - 2026-09-24T12:00Z, it shows v6 answering 100 % with v7 in Shadow (copies only);
  - 2026-09-26T12:00Z, v7 5 % and v6 95 %;
  - 2026-09-28T00:00Z, v6 100 %.
- /r Given every Registry version When shown Then each model id is frozen, and Anomalies "Registry versions naming a floating alias" is 0 (P4; blueprint §1.3).

**auditor-10.21 · Kill switch history**
As the Auditor, I see each kill-switch change with who, when, why and how long, so that an AI outage decision is accountable.
- /r Given events 46 and 47 When `staff_hana` filters Registry → History by "Kill switch" Then two rows show "On · meal_photo · staff_ali · 10:12:00Z · AI timeouts" and "Off · 10:47:00Z", with "On for 35 min".

**auditor-10.22 · Food and Tier B recipe record approvals** *(shared with Nutrition approver)*
As the Auditor, I see who proposed and approved each reference record, with its licence and evidence, so that a regulator can trace a number to a reviewed source (FR-080; F1, F8).
- /r Given events 29 and 30 When `staff_hana` opens **Recipes → rec_fm_eg → History** Then it shows:
  - "v1 · proposed by staff_yara 09:00:00Z · approved by staff_dina 09:40:00Z";
  - licence "first-party calculation (approver-built)";
  - evidence `ev_1101`, `ev_1102`;
  - no eater data.

**auditor-10.23 · Alias approvals with their dialect** *(shared with Nutrition approver)*
As the Auditor, I see each Alias approval with its dialect, so that I can show which word resolves to which Food for which eaters (FR-015; F27).
- /r Given events 31–34 When `staff_hana` filters Events by action `alias.approved` Then two rows show:
  - «صقعي» · Gulf · → "Dates, Saqai" · proposed by staff_dina · approved by staff_yara 10:15:00Z;
  - «لبن» · EG · → "Milk, whole" · approved 10:30:00Z.
  - Filtering by dialect Gulf leaves only the first row.

**auditor-10.24 · Who holds which role now**
As the Auditor, I see every staff member's current roles, with who assigned them and when, so that least privilege can be checked (N1 AC-2(7)).
- /r Given the role store at the test clock When `staff_hana` opens **Roles** Then the rows are:
  - staff_ali, Platform admin (system bootstrap, 09-01);
  - staff_mona, Support agent;
  - staff_omar, Support agent;
  - staff_dina, Nutrition approver;
  - staff_yara, Nutrition approver;
  - staff_hana, Auditor;
  - staff_sod_seed, Support agent and Platform admin, "assigned by: no event in the Audit trail".
  - staff_tariq is not listed.

**auditor-10.25 · Role history and "held at"** *(shared with Platform admin)*
As the Auditor, I see every assignment and removal and who held a role at a past moment, so that I can answer "who could request Grants on 20 September?".
- /r Given events 19 and 43 When `staff_hana` sets **Held at** Then:
  - 2026-09-20T12:00+03:00 lists Support agent: staff_mona, staff_omar, staff_tariq;
  - 2026-09-26T12:00+03:00 lists staff_mona and staff_omar only.
- /r Given **Held at** 2026-08-31T12:00:00Z When applied Then it reads "Nobody held a role at this time — the first assignment was 2026-09-01T06:00:00Z (event 1)".

**auditor-10.26 · Separation of duties, as a check behind the save rule** *(shared with Platform admin)*
As the Auditor, I see anyone holding roles that FR-081 keeps apart, even if they got there around the roles screen, and every refused self-change, so that the save rule of admin-10.59 and admin-10.61 has a detective check behind it (K3 Art. 9(5); GDPR Art. 38(6)).
- /r Given `staff_sod_seed` was written straight into the role store by the test seed (below the roles API, which admin-10.59 would refuse) When `staff_hana` opens Anomalies Then two rows link to that account:
  - "Support agent held with Platform admin or Nutrition approver: 1 — staff_sod_seed";
  - "Roles held with no assignment event: 1 — staff_sod_seed".
- /r Given event 35 When `staff_hana` filters Events by `access.refused` and actor `staff_ali` Then the row reads "role.assign Auditor → staff_ali · self-change · FORBIDDEN · 2026-09-15T10:00:00Z".

**auditor-10.27 · Filter and search, fast**
As the Auditor, I narrow the Audit trail by time, actor, action, account, Grant and outcome, so that I find the events a question is about in seconds.
- /r Given the seeded Audit trail When `staff_hana` sets actor `staff_omar`, action `grant.read_refused` and 2026-10-01T00:00+03:00 – 2026-10-04T00:00+03:00 Then exactly 3 rows show (events 63, 68, 76). Each filter shows as a chip in words ("Actor: staff_omar"), and the URL carries the filters, so a reload or a shared link reproduces the list.
- /r Given "grant_31f0" or "58" typed in the search box When Enter Then that Grant or event opens directly.
- /r Given fixture B1 (25,000 events) When `staff_hana` applies a two-filter query 20 times Then the first page of results renders within 1 s at the 95th percentile (a chosen default; the brief's nearest figure is NFR-02's 2 s p95 for an online commit).

**auditor-10.28 · Invalid filter input**
As the Auditor, I am told exactly what is wrong with a filter, next to it, so that I fix it without losing the rest.
- /r Given start 2026-09-28 and end 2026-09-27 When applied Then:
  - the End field says "End is before start — pick a date after 28 Sep 2026";
  - the list is not reloaded, and other filters stay;
  - `GET /v1/admin/audit/events?from=2026-09-28&to=2026-09-27` returns 422 `VALIDATION_ERROR` with field `to`.
- /r Given "grant-31f0" in search When Enter Then the hint says "Grant ids look like grant_31f0", and the list stays as it was.

**auditor-10.29 · Time without ambiguity**
As the Auditor, I see every time in UTC with my offset, and the device time apart from the received time, so that two people in two cities read the same moment.
- /r Given event 60 When shown in the Asia/Riyadh console Then it reads "11:20:01Z · 14:20:01 +03:00", and every list header names the zone ("Times in UTC and +03:00").
- /r Given `staff_hana` sets the console zone to UTC in Settings When Events reloads Then only the second time column changes; event ids, order and UTC values are identical.

**auditor-10.30 · Large and slow results**
As the Auditor, I page large results in stable order, see new events without rows jumping, and can cancel a slow search, so that I never lose my place.
- /r Given fixture B1 When an unfiltered query runs Then the first 100 rows show with "25,000 events", ordered by event id. Loading more never reorders rows already shown. (100 is a starting value, tried and chosen at the care pass.)
- /r Given the seeded Audit trail open on Events When a test appends 12 events Then a bar at the top reads "12 new events — show", and rows in view do not move.
- /r Given B1 and a query slowed by fault injection to 5 s When 2 s pass Then "Searching…" shows with progress and **Cancel**; Cancel stops the request and keeps the previous results.

**auditor-10.31 · Offline**
As the Auditor, I keep reading what I loaded when the network drops, so that a flaky connection does not cost me my place.
- /r Given Events loaded at 09:05:00Z When the browser goes offline Then:
  - the rows stay, with the quiet note "Offline — showing the Audit trail as loaded at 09:05:00Z";
  - **Export** and **Verify chain** are disabled with "Needs a connection";
  - both re-enable on reconnect without a reload.

**auditor-10.32 · Export exactly what I see, with a manifest**
As the Auditor, I export the filtered Audit trail as CSV or JSON with a manifest, so that a regulator gets a complete, verifiable file that matches my screen (N1 AU-7(b); H1).
- /r Given account `acct_9c41e2` and 2026-09-01T00:00+03:00 – 2026-10-01T00:00+03:00, which shows 13 events (9–16, 26, 27, 37, 39, 44), When `staff_hana` selects **Export → CSV** Then the download holds:
  - `events.csv`: a header plus 13 rows, one column per field, no embedded JSON;
  - `manifest.json`: the filters in words and as a query; the as-of event (the newest event id when Export started, also shown in the dialog); the row count 13; "generated by staff_hana"; the time; the zone; the SHA-256 of `events.csv`.
- /m Given the export serializer and those 13 stored events When it writes the CSV Then the rows keep event order and stored values, and `sha256(events.csv)` equals the manifest value.
- /r Given the export finished When `staff_hana` looks at Events Then a `audit_trail.exported` event shows her id, the filters and "13 rows" (O1).

**auditor-10.33 · A very large export comes in parts, never cut**
As the Auditor, I get the whole result in numbered parts, so that I never hand a regulator a partial file (the M1 pain).
- /r Given fixture B2 (1,200,000 events) When `staff_hana` exports it unfiltered Then:
  - the dialog says "1,200,000 events · 12 parts of up to 100,000 rows" (part size is a chosen default);
  - a background job shows progress and **Cancel**;
  - **Audit trail → Exports** delivers parts 1–12, with a manifest listing each part's row count, which sum to 1,200,000.
- /r Given the same export with part 7 failed by fault injection When `staff_hana` opens Exports Then the job reads "Failed at part 7 of 12 — parts 1–6 kept, nothing marked complete" with **Retry from part 7**, which completes with the same manifest totals.

**auditor-10.34 · Find an account for a complaint, and leave a trace**
As the Auditor, I find the account behind a complaint with my reason recorded, so that I can answer about one person without browsing identities.
- /r Given a complaint quoting `r7k2q9x4@privaterelay.appleid.com` (synthetic) When `staff_hana` enters it in **Audit trail → Find account** with the reason "SDAIA complaint ref C-118 (synthetic)" Then:
  - the console shows only `acct_9c41e2` and its counts: Grants 4 · reads allowed 4 · reads refused 2 · writes refused 1 · age confirmation 1 · Consent events 10 · Privacy jobs 1 · raw-evidence attempts refused 1;
  - no profile or diary data is shown;
  - an `account.lookup` event records the reason.
- /r Given **Find account** with no reason When submitted Then the field says "Give a reason — lookups are recorded", and nothing is looked up.
- /r Given an email with no account When looked up Then the result reads "No account matches. It may never have existed, or it was deleted — deleted accounts keep no identifiers", and `account.lookup` records outcome `NOT_FOUND`.

**auditor-10.35 · Scope a suspected breach in minutes**
As the Auditor, I summarise a staff account's activity over a window, with the distinct accounts it read, so that a 72-hour notification can say who and how many were affected (K2 Art. 24(1)(b); E1 Art. 7; GDPR Art. 33(3)(a)).
- /r Given `staff_mona` is suspected compromised from 2026-10-01T00:00:00Z to 2026-10-04T23:59:59Z When `staff_hana` filters by that actor and window and opens **Summary** Then it shows, for her actions and the Grants she requested:
  - Grants requested 3 (31f0, 40aa, 5d10) · approved 2 · Unanswered 1 · Expired 1 · Ended 1;
  - reads allowed 4 · reads refused 2 · writes refused 1;
  - **distinct accounts read 1** (`acct_9c41e2`; `acct_3f88a1` had only a refused read);
  - the first and last event times (10:05:00Z on 10-01, 10:30:00Z on 10-04);
  - **Export** gives the events plus the summary, with a manifest.
- /m Given the summary function When computed Then "distinct accounts read" counts account ids over Allowed reads only.
- /r Given fixture B1 When the same Summary is run 20 times for one actor over 90 days Then it renders within 3 s at the 95th percentile (a chosen default).

**auditor-10.36 · The period report**
As the Auditor, I produce a periodic report whose figures open to their events, so that management, and the DPO's duty to report, have one page to read first (K3 Art. 8(4); A1's cover sheet).
- /r Given Q3 2026 (2026-07-01T00:00+03:00 – 2026-10-01T00:00+03:00) When `staff_hana` opens **Audit trail → Summary → Quarter** Then one page shows these figures, each linked to its events:

  | figure | value |
  |---|---|
  | Grants | requested 1 · approved 1 · Expired 1 (grant_27b4) |
  | reads | allowed 1 · refused 0 |
  | Policy versions | approved 2 (v1, v2) · put in effect 1 (v1) |
  | Registry | stage changes 5 (events 8, 41, 42, 45, 48) · versions proposed 1 · kill-switch changes 2 |
  | roles | assigned 7 · removed 1 · refused self-changes 1 |
  | age confirmations | 1 |
  | Consents | given 8 · withdrawn 2 |
  | deletions | requested 5 (events 17, 18, 20, 28, 49) · completed 2 (median 11 d, maximum 14 d) |
  | exports | completed 1 |

  The page exports as CSV plus a manifest.
- /r Given Q2 2026 When opened Then each figure shows 0 with "No events in this period", never a blank.

**auditor-10.37 · My own activity is on the record**
As the Auditor, I see my own queries, lookups and exports in the Audit trail, so that the watcher is watched too (O1 "All access to the logs must be recorded").
- /r Given `staff_hana` signed in and landed on the overview (her first query), then applied 2 filters, made 1 lookup and 1 export When she filters Events by actor `staff_hana` and today Then the list shows:
  - 1 `staff.signed_in`;
  - 4 `audit_trail.queried` (the landing view, the 2 filters, and this one; a query event is written before its results are read);
  - 1 `account.lookup`;
  - 1 `audit_trail.exported`.
  - These events are as immutable as every other event.

**auditor-10.38 · The console in Arabic**
As the Auditor working in Arabic, I read the Audit trail right to left without ids, times or hashes being scrambled, so that the evidence reads the same in both languages.
- /r Given `staff_hana` sets the console language to Arabic in Settings When she opens **Grants → grant_31f0** Then:
  - the layout mirrors: the timeline runs right to left, and the back arrow points right;
  - labels come from the string catalogue, one Arabic label per D2 word;
  - ids (`grant_31f0`, `CASE-1182`), event ids, hashes and ISO timestamps stay left to right with Western digits;
  - counts follow the numerals setting (Arabic-Indic or Western).
- /r Given events 32 and 34 in the Arabic console When shown Then «صقعي» and «لبن» sit right-aligned in their Arabic runs, while "Dates, Saqai" and "Milk, whole" stay isolated left-to-right runs; nothing is translated.
- /r Given an export from the Arabic console When opened Then the CSV headers are the same stable keys as in English, and `manifest.json` records `"console_language": "ar"`.

**auditor-10.39 · On a phone, for one anomaly**
As the Auditor away from my desk, I check one anomaly or Grant on a phone, so that I can answer quickly without a laptop.
- /r Given a browser 390 px wide When `staff_hana` opens Anomalies and then `grant_31f0` Then:
  - there is no horizontal page scroll;
  - rows become stacked cards (id, state, counts);
  - the timeline runs vertically;
  - every control is at least 44 × 44 CSS px (a chosen default).
- /r Given the same width When she opens Events Then the filters collapse behind **Filters (3)**, which states the active count, and Export stays reachable.

**auditor-10.40 · Keyboard, screen reader, zoom**
As the Auditor, I can do the whole review by keyboard and screen reader and at 200 % zoom, so that the console works for every reviewer (NFR-08).
- /r Given keyboard only When `staff_hana` tabs through the Events filters, results and Export Then focus follows visual order with a visible ring; Enter opens a row, Esc closes the detail panel, and "/" focuses search.
- /r Given VoiceOver or NVDA When focus reaches `grant_31f0`'s row Then it announces "Grant grant_31f0, Expired, 3 reads allowed, 1 read refused, 1 write refused, requested by staff_mona".
- /r Given 200 % browser zoom When Events, Grant detail and Anomalies render Then no text clips or overlaps, and every text pair meets 4.5:1 contrast in light and dark.

### Journey 9 — WF-9, read side

**auditor-9.1 · Consents by purpose**
As the Auditor, I see every Consent purpose with its current text version and event counts, so that I can show consents are separate and current (FR-076; R22; map interaction row 1).
- /r Given the seeded Audit trail When `staff_hana` opens **Audit trail → Consents** Then one row per purpose shows the current text version and its Given and Withdrawn event counts:

  | purpose | current text | Given | Withdrawn |
  |---|---|---|---|
  | Diary processing | diary-1 | 1 | 0 |
  | Send photos, voice and text to Google's AI (Gemini) | c-ai-4 | 2 | 1 |
  | Health: read workouts | health-1 | 1 | 0 |
  | Health: read active energy | health-1 | 1 | 0 |
  | Health: read body mass | health-1 | 1 | 0 |
  | Health: write food | health-1 | 1 | 0 |
  | Microphone | — | 0 ("No records yet") | 0 |
  | Photos | — | 0 ("No records yet") | 0 |
  | Optional research | research-1 | 1 | 1 |

  The age confirmation is a separate row: "Age 18+ confirmed · age-1 · 1".
- /r Given an emulator with no Consent events When Consents opens Then it reads "No Consent records yet — they appear as eaters finish onboarding."

**auditor-9.2 · One account's Consent history**
As the Auditor, I see one account's age confirmation and Consents over time, so that I can prove what they agreed to, when and how (K2 Art. 11(1)(d); GDPR Art. 7(1)).
- /r Given `acct_9c41e2` When `staff_hana` opens its Consents Then 11 rows show in event order: 9, 10, 11, 12, 13, 14, 15, 16, 37, 39, 44. Each row names the purpose (or "Age 18+"), the text version, the action, the method ("onboarding" or "Settings"), the time and the app version.

**auditor-9.3 · The exact words the eater agreed to**
As the Auditor, I open a consent text version exactly as the app showed it, in English and Arabic, so that "what did they consent to?" has one answer (R2).
- /r Given `c-ai-3` When `staff_hana` opens it Then:
  - the English and Arabic texts render exactly as published;
  - the Arabic, «أوافق على إرسال الصور والصوت والنص إلى Google ‏(Gemini) لتحليل وجباتي» (synthetic wording), is right-aligned, with "Google (Gemini)" kept as one left-to-right run;
  - the provider is named;
  - the page shows "published 2026-08-25 · given under this version: 1 event (11)".
- /s Given `c-ai-3` is stored When `c-ai-4` is published Then `c-ai-3`'s stored text is byte-for-byte unchanged.

**auditor-9.4 · One purpose per record**
As the Auditor, I see that no Consent record covers two purposes, so that bundled consent cannot hide in the data (K2 Art. 11(1)(e); R3).
- /m Given the Consent record schema When a record with two purposes is built Then validation fails.
- /r Given the seeded Audit trail When `staff_hana` opens Anomalies Then "Consent records covering more than one purpose" is 0.

**auditor-9.5 · A withdrawal takes effect everywhere AT-29 names** *(shared with eater)*
As the Auditor, I see what a withdrawal stopped and deleted, including media and exports, so that I can show processing ceased without undue delay (K2 Art. 12(3); AT-29).
- /r Given event 37 and its withdrawal-effect record When `staff_hana` opens event 37 Then its Effect card shows:
  - "AI requests until the next Consent (2026-09-25T20:10:00Z): 0";
  - "Pending Analyses cancelled: 1";
  - "Photos and audio held for analysis deleted: 3 · 07:45:04Z";
  - "Private cached analysis deleted: 1 · 07:45:03Z";
  - "Queued uploads purged: 2";
  - "Prepared export files deleted: 1 (job_exp_4402) · 07:45:05Z".
- /s Given the AI Consent is withdrawn on a fresh emulator When the app posts `POST /v1/analyses` for that account Then the API returns 403 `CONSENT_REQUIRED`, and the Gemini adapter mock records 0 calls.
- /r Given Anomalies When opened Then "AI requests after the AI Consent was withdrawn" is 0.

**auditor-9.6 · Recorded once, with device time and received time**
As the Auditor, I see a retried, offline withdrawal recorded once, with when it was made and when it arrived, so that the record is neither doubled nor misdated (the AT-10 pattern; O1 event time versus log time).
- /r Given event 39 (the research withdrawal, made on the device 2026-09-21T22:40:00Z, received 2026-09-22T06:05:30Z, delivered twice) When `staff_hana` opens it Then one event shows "Made 22:40:00Z on 21 Sep on the device · received 06:05:30Z on 22 Sep", and no second withdrawal event exists for that purpose.

**auditor-9.7 · Health permissions shown honestly**
As the Auditor, I see what the app recorded about Health access and what it cannot know, so that I never claim iOS granted a read it cannot see (R7; P30).
- /r Given events 12–15 When `staff_hana` opens them Then:
  - each read row (workouts, active energy, body mass) reads "In-app Consent given · iOS read permission: not observable (Apple does not tell apps whether read access was denied)";
  - the write row reads "In-app Consent given · iOS write permission: as reported by the app";
  - no row says "granted by iOS" for a read.

**auditor-9.8 · Raw evidence is never opened by staff** *(FRD §19.2; FR-077, FR-079; D2 roles hold no such permission)*
As the Auditor, I see that no staff member opened an eater's meal photo or audio, and that every attempt was refused, so that raw evidence stays private.
- /r Given event 78 When `staff_hana` filters Events by `access.refused` and object "Analysis photo" Then one row reads "staff_ali · Analysis an_7781 photo · acct_9c41e2 · FORBIDDEN · 2026-10-03T15:05:00Z". No image appears in the console, and Anomalies "raw evidence opened by staff" is 0.
- /s Given each staff role on a fresh emulator When it requests a raw photo or audio of any Analysis Then each returns 403 `FORBIDDEN` and writes one `access.refused` event.

**auditor-9.9 · The 18+ confirmation and the age gate** *(map interaction row 1; WF-1 done-when "Under 18: no account"; R16, R22)*
As the Auditor, I see each account's age confirmation and that an under-18 answer created nothing, so that I can show the service is for adults with full legal capacity.
- /r Given event 9 When `staff_hana` opens `acct_9c41e2`'s Consents Then the first row reads "Age 18+ confirmed · age-1 · onboarding age question · 2026-09-01T18:20:00Z · app 1.0.3".
- /s Given a fresh install on the emulator When the onboarding call sends age 17 Then:
  - it returns 422 `AGE_REQUIREMENT`;
  - no account, age record or Consent is created;
  - one `age.refused` event is written with no account id, device id or other identifier.
- /r Given that refusal When `staff_hana` opens Consents Then "Age gate refusals: 1 (no identifiers kept)" shows under the age row.

**auditor-9.10 · Privacy jobs on the 30-day clock**
As the Auditor, I see every export and deletion with its age against 30 days, so that no rights request quietly runs late (K2 Art. 3(1)(a); NFR-13; R23, R31).
- /r Given the Privacy jobs fixture When `staff_hana` opens **Jobs → Privacy jobs** Then the rows read:
  - `job_del_2228` Running, day 7 of 30, "1 step retrying";
  - `job_del_2209` Running, day 26 of 30, "Due in 4 days";
  - `job_del_2204` Running, "Overdue — day 31 of 30";
  - `job_del_2212` Completed in 8 days;
  - `job_del_2201` Completed in 14 days;
  - `job_exp_4402` Completed in 4 min.
  - Each state shows as a word plus an icon. Anomalies "deletions open more than 30 days" is 1.
- /r Given an emulator with no Privacy jobs When opened Then it reads "No export or deletion has been requested yet."

**auditor-9.11 · The deletion completion record**
As the Auditor, I open a completion record that proves every step finished and holds no identifiers, so that I can show the deletion happened without keeping who it was (WF-9 done-when; AT-29; R4, R23).
- /r Given `job_del_2201` When `staff_hana` opens it Then it shows only:
  - deletion reference `DEL-26-0902-A7K1`;
  - requested 2026-09-02T06:00:00Z;
  - completed 2026-09-16T06:30:00Z (14 days);
  - the steps, each with its completion time: private records deleted · photos and audio deleted · derived caches and private cached analysis deleted · queued jobs purged · prepared exports deleted · processors told (Google) · Sign in with Apple token revoked (or "not applicable — not used");
  - "backups expire by 2026-10-16T06:30:00Z", labelled "(30-day backup lifecycle — `assumption`, to be disclosed)".
- /m Given the completion-record schema When validated Then no field from the identifier deny-list is present (account id, email, phone, name, device id, IP, Apple id); adding one fails the test.
- /s Given `job_del_2201` run to completion on the emulator When the test searches the Firestore and Cloud Storage emulators for `acct_b81c40` outside the Audit trail Then nothing is found (NFR-13 "verify deletion propagation").

**auditor-9.12 · A deletion step keeps failing**
As the Auditor, I see a stuck step plainly, so that a deletion is never shown as done while something remains.
- /r Given `job_del_2228` When `staff_hana` opens it Then:
  - the state reads "Running — 1 step retrying";
  - the step "photos and audio deleted" shows Failed at 2026-09-28T10:05:00Z and 2026-10-05T08:05:00Z, "storage timeout", and "next retry 09:05:00Z (same job id)";
  - the record is not marked Completed;
  - its age reads "day 7 of 30".

**auditor-9.13 · A deleted account in the Audit trail**
As the Auditor, I still see Grant history about a deleted account, under an id that no longer leads to a person, so that evidence survives without identifying anyone (K1 Art. 18(1); GDPR Art. 17(3)(b), (e). This is a recommended default; see Conflicts, item 4).
- /r Given `acct_d40e17` was deleted by `job_del_2212` When `staff_hana` opens `grant_27b4` Then:
  - the account shows as "acct_d40e17 · account deleted 2026-09-20";
  - events 22–25 remain;
  - Verify chain stays intact.
- /r Given **Find account** with the reason "test" and that account's former email When looked up Then the result reads "No account matches…", because the sign-in identity and profile were destroyed with the account.

**auditor-9.14 · An eater's export job, not its contents**
As the Auditor, I see that an eater's export ran and what it covered, without opening it, so that the right of access is evidenced and the data stays private (FR-075; WF-9 done-when).
- /r Given `job_exp_4402` When `staff_hana` opens it Then it shows:
  - requested 12:00:00Z and completed 12:04:00Z on 2026-09-10;
  - the categories with item counts: Entries, Units (portions), Recipes, Targets, Reports (day and period), Consents;
  - the file size;
  - the link expiry, and "file deleted 2026-09-20T07:45:05Z (AI Consent withdrawn)";
  - whether it was downloaded.
  - There is no download or preview control.

**auditor-9.15 · Retention runs**
As the Auditor, I see each retention run and the oldest item left, so that I can verify the retention promises (FR-078; FR-082 "retention verification").
- /r Given run R-0805 When `staff_hana` opens **Jobs → Retention** Then it reads "2026-10-05T08:00:00Z · raw scans deleted 2 · audio deleted 1 · oldest remaining raw scan 29 d 22 h · oldest audio 22 h 10 min · Policy v2 · schedule hourly".
- /r Given the separate fixture R-gap (clock 06:30:00Z, last run 03:00:00Z, one audio aged 24 h 20 min) When Retention opens Then it says "No retention run since 03:00:00Z — expected hourly", and Anomalies "audio older than 24 h" is 1.

**auditor-9.16 · Records of processing, built from what is live** *(proposed view; see Conflicts, item 8)*
As the Auditor, I read and export a records-of-processing view assembled from live, versioned configuration, so that a regulator's request is answered from facts, not a stale document (K1 Art. 31; K2 Art. 33; E1 Art. 4(9); GDPR Art. 30).
- /r Given the consent purposes, Policy v2 and the Registry When `staff_hana` opens **Audit trail → Records of processing** Then each purpose row shows:
  - the purpose and the data categories;
  - the subject categories ("adults 18+");
  - the recipients ("Google — Gemini on Agent Platform; Firebase and Google Cloud");
  - transfer outside the Kingdom: "yes — Gemini 3.8 Flash runs on `global` or US/EU multi-regions only" (P11 as corrected);
  - retention, from Policy v2.
  - Each value names the version it came from. A field the product does not hold reads "Not recorded in the product", never a blank.
- /r Given the view When **Export** is chosen Then CSV plus a manifest download, and `audit_trail.exported` is recorded.

**auditor-9.17 · Consent evidence for a regulator**
As the Auditor, I export one account's Consent history with a manifest, so that the proof of consent travels intact.
- /r Given `acct_9c41e2` When `staff_hana` selects Consents → **Export** Then:
  - `consents.csv` holds 11 rows (purpose or age, text version, action, method, made at, received at, app version);
  - `manifest.json` holds the filters, the row count 11 and the SHA-256;
  - `audit_trail.exported` is recorded.

**auditor-9.18 · A Consent record whose text is missing**
As the Auditor, I am told when a Consent points to a text version that cannot be found, so that a gap in the proof is visible.
- /r Given separate fixture X When `staff_hana` opens `acct_3f88a1`'s Consent Then it reads "Text version c-ai-2 not found — the record is kept, its wording cannot be shown", and Anomalies "Consent records whose text version is missing" is 1.

---

## 5 · The experience this persona needs

- **Device and place.** A desk, a large screen, a browser with keyboard and mouse, a spreadsheet beside it; sometimes a phone (390 px) for one anomaly.
- **The moment that matters.** A regulator, management or an eater asks "who saw this person's data, and were they allowed?", or the 72-hour breach clock starts. The Auditor answers from the screen and hands over a file that matches it, in minutes.
- **The feeling it must leave.** *Certainty I can defend*: nothing hidden, nothing partial, nothing editable behind my back.
- **The matching style.** Dense, calm and exact:
  - tables first, monospace for ids and hashes, times always in UTC with an explicit offset;
  - neutral colour for ordinary rows; anomalies loud only when a must-be-zero rule is not zero, always as words plus an icon;
  - filters that state themselves in words and live in the URL;
  - no write controls anywhere;
  - keyboard-first;
  - a filtered list within 1 s p95 and a breach Summary within 3 s p95 (10.27, 10.35);
  - long work (Verify chain, big exports) runs as a job with progress and Cancel.

### Care questions this persona raises, answered as requirements

**1 · Deserves to exist, and where it lives**
- One sentence per place:
  - **Audit trail**: the evidence;
  - **Grants**: diary access;
  - **Anomalies**: the never-events;
  - **Policy, Registry, Foods, Recipes, Aliases**: who changed the rules;
  - **Roles**: who could act;
  - **Consents** and **Jobs**: rights in action;
  - **Summary**: the periodic page.
- Said no to:
  - free browsing of identities (a lookup needs a reason, 10.34);
  - any write control (10.16);
  - viewing diary content, photos or audio (10.5, 9.8, 9.14);
  - food or health dashboards.
- Places sit in the navigation. Actions sit next to their data: Export, Verify chain, Compare, Find account.
- Details open in a side panel that keeps the list in place (Esc closes it); there are no pop-ups for reading.

**2 · Found and understood**
- Titles name the place ("Grants", "Grant grant_31f0"). Words are D2's: Support agent, Active, Expired, Ended, Withdrawn, Declined, Unanswered, Audit trail, Policy, Registry, Shadow, Canary, Rollout, Rolled back.
- One word per thing: "Withdrawn" is always the eater's act and "Ended" always the Support agent's, as one tooltip glossary defines.
- Defaults work without settings: the console zone comes from the browser and is shown; the default range is the last 30 days, shown as a chip.
- Key status sits where people look: the chain, the anomaly count and Active Grants are in the Audit trail header and the navigation badge.

**3 · How it feels**
- Every action answers:
  - a query shows its count;
  - an export shows queued, then progress, then "13 rows · SHA-256 …";
  - Verify shows the range it checked.
- Loudness matches importance: refused attempts are information; a non-zero never-event is an alert.
- Rows never jump; new events wait behind a "show" bar (10.30).
- Page size 100, the progress threshold of 2 s, the 30-day default range, part size 100,000, and the 1 s and 3 s bounds were tried in the served console and chosen on purpose at the care pass.

**4 · Empty, wrong, slow**
- Empty states say why and offer the next step (10.2, 10.25, 9.1, 9.10, 10.36).
- Errors sit next to the problem and say how to fix it, without blame (10.28, 10.34).
- A skeleton shows while loading, never zeros (10.1). Long tasks show progress and Cancel (10.15, 10.30, 10.33).
- Offline keeps the last data with a quiet note and disables what needs the network, saying why (10.31).
- What never happens:
  - a failed check never shows 0; it shows "Not evaluated" (10.14);
  - a partial export never ships (10.33);
  - a stuck deletion is never "Completed" (9.12).
- Permission refused names the needed role and is itself logged (10.17).

**5 · The inside**
- Names in code, data and logs match the screen: `grant.read_refused`, `consent.withdrawn`, Policy, Registry, `account_id`, and the D2 codes.
- Tests:
  - the Audit trail holds no health data (10.5 /m);
  - the serializer keeps content and order (10.32 /m);
  - the completion record is checked against a deny-list (9.11 /m);
  - every anomaly rule is proven to catch its case (10.14 /m, 10.18 /m).
- Sample data is synthetic: Arabic Aliases, mixed scripts, long ids, no real names or emails.
- The product claims only what it does: Health read state is "not observable" (9.7), a missing consent text says so (9.18), and the backup period is labelled an assumption (9.11).

**6 · Inclusion**
- 200 % zoom without clipping; 4.5:1 contrast in light and dark; a visible focus ring; meaning never by colour alone; current screen-reader labels; a full keyboard path (10.40).
- Arabic mirrors where direction carries meaning; ids, times and hashes stay left to right; each run aligns by its own language (10.38).
- Nothing disappears on a timer.

---

## 6 · Stories shared with other personas

| story | shared with | why |
|---|---|---|
| auditor-10.3 | Support agent, eater | the WF-10 done-when, end to end |
| auditor-10.6, 10.7, 10.10, 10.11 | eater, Support agent | the eater's decision, silence and withdrawal create the events |
| auditor-10.12, 10.13 | Support agent | Ended by the Support agent; a write refused inside an Active Grant |
| auditor-10.22, 10.23 | Nutrition approver | Food, Tier B recipe record and Alias approvals |
| auditor-10.20, 10.21 | Platform admin | Registry stages, rollback, kill switch |
| auditor-10.25, 10.26 | Platform admin | role history; the detective check behind admin-10.59 and admin-10.61 |
| auditor-9.5, 9.9 | eater | withdrawal effect; the age gate |

---

## 7 · Conflicts for the model phase

1. **Read-only seat versus FRD §23.2.** The FRD says "Privacy/security reviewers **approve** consent, retention, access, and target-market compliance". The map makes the Auditor read-only and gives retention Policy to the Nutrition approver. Who approves consent text versions and retention values is undecided.
2. **The breach record has no home.** These all require a documented breach record with notifications and corrective actions:
   - K2 Art. 24(3);
   - K4 Stage Three;
   - GDPR Art. 33(5);
   - U1 ("regardless of whether you are required to notify");
   - E2.

   The map has none, and the Auditor cannot write. Options: a breach record the DPO writes in the console, or one kept outside the product. 10.35's export feeds either.
3. **A review sign-off is a write.** The evidence of a periodic review is "who reviewed it, when, what they decided … and why" (A1). K2 Art. 32(3)(b) asks the DPO for "documenting assessment results"; E1 Art. 9(1) in Arabic asks for «توثيق نتائج التقييم», documenting the evaluation's results. Proposal: the Auditor's only write is a review note appended to the Audit trail, never an edit of an event.
4. **Erasure versus the Audit trail.** Grant and Consent events about a deleted account stay as evidence (GDPR Art. 17(3)(b), (e); K1 Art. 18(1) allows keeping data that cannot identify the person). Recommended default: events keep the account id, and deletion destroys the sign-in identity and profile, so the id leads nowhere (9.13).
   - **Open:** the Audit trail's retention period. No source fixes one for access logs; N1 AU-11 leaves it to the organisation, and K2 Art. 33(1)'s five years is for records of processing. Counsel decides.
5. **Identity in Grant requests.** The support lens shows the Support agent the account, the eater's language and zone; this lens sees account ids and reaches an identity only by a logged lookup with a reason (10.34). Both lenses must agree on the fields of a Grant request.
6. **Infrastructure reads are outside the Audit trail.** Google Cloud Data Access audit logs are "disabled by default" (C1). Direct Firestore or Cloud Storage reads by an engineer would not show unless hosting enables them and correlates them (N1 AU-6(3)). The ship rows are dropped (§0 line 10). Until a hosting delta, "no read outside a Grant" holds for the product's API only.
7. **Consent text versions need an owner.** Who writes and publishes the English and Arabic consent wording, and the Grant request wording (`grant-req-1`)? The map is silent; FRD §23.2 points at privacy reviewers.
8. **Records of processing as a view.** The map lists records of processing as pre-launch governance work (r1 implications), not a screen. 9.16 derives it from live configuration. Fields not in configuration need an owner: controller contact, DPO details, security measures.
9. **One person, two accounts.** D2 says "a staff account is never an eater account". A person may still hold both under two accounts. Then the rule "no staff member can approve their own request" (blueprint §1.2) needs a link between the two accounts, which the map lacks. This lens dropped its self-request story (formerly 10.12) for that reason.
10. **The Grant request window.** The request window (72 h) is support lens A6's `assumption`, with no source. It needs an owner and a home (Policy is approver-owned and nutrition-focused). The same goes for the allowed durations (1 h · 4 h · 24 h).
11. **Raw-evidence quality review.** FRD §19.2 allows "Access to raw evidence for quality review" with "explicit consent and restricted roles". D2 has no role with that permission, so 9.8 treats every attempt as refused. If a role is added, it needs a Consent link and a new anomaly rule.
12. **Arabic in regulator exports.** Whether SDAIA or the PDPC expect Arabic labels in an extract is an `assumption`. Proposal: stable machine keys, with an optional Arabic label row.
13. **The eater's own view of reads.** The support lens shows the eater the access history inside a Grant. Its counts must equal this lens's timeline (10.3).
14. **Breach-notice timing differs by regime.** Egypt tells data subjects within three business days of reporting; KSA says "without undue delay". This belongs to the runbook outside the product, but the evidence export must serve both.
15. **Names this lens uses that are not yet in D2** (each needs a dated delta or a rename):
    - the Audit trail views Events, Anomalies, Consents, Summary, Records of processing, Exports, and the action Find account;
    - event actions such as `grant.read_refused` and `grant.write_refused`;
    - the outcome words Allowed, Refused, Done, Failed.
16. **Differences from other lenses still to align** (not settled here):
    - **Grant states and codes.** The support lens's states (`requested`, `approved`, `expired_unanswered`, `revoked`, `withdrawn` for the agent's own cancellation) and codes (`GRANT_DECLINED`, `GRANT_EXPIRED`, `GRANT_REVOKED`, `GRANT_OUT_OF_SCOPE`, `GRANT_READ_ONLY`, `FORBIDDEN_ROLE`) predate D2. This lens uses D2's Unanswered, Withdrawn (by the eater), Ended (by the Support agent), `GRANT_NOT_ACTIVE`, `GRANT_REQUIRED` and `FORBIDDEN`.
    - **A Support agent cancelling their own unanswered request.** D2 has no state for it.
    - **Staff id styles.** The support lens uses `staff_mona`; the admin lens uses `admin.a@example.test`.
    - **Policy fixtures.** The approver lens's Policy v1 carries a separate signed "nutrition-policy review" (approver-10.x). D2 records only proposer and approver.
    - **Registry wording.** The admin lens says "Draft" and "full"; D2 says Proposed and Rollout.
17. **The seeded separation-of-duties violation** (`staff_sod_seed`) can be produced only below the roles API. The anomaly rule in 10.26 is a **detective control** behind admin-10.59's preventive save rule. The model phase should keep both.

---

## 8 · Coverage

- **FRD lines:**

  | line | stories |
  |---|---|
  | FR-015 | 10.23 |
  | FR-075 | 9.14 |
  | FR-076 | 9.1–9.4 |
  | FR-077 | 9.8 |
  | FR-078 | 9.10–9.12, 9.15 |
  | FR-079 | 9.8 |
  | FR-080 | 10.18–10.24 |
  | FR-081 | 10.3–10.17, 10.26 |
  | FR-082 | 9.15, 9.16 |
  | NFR-02 (as the nearest figure) | 10.27 |
  | NFR-07 | 10.9 |
  | NFR-08 | 10.40 |
  | NFR-13 | 9.10, 9.11 |
  | §3.3 | 10.18 |
  | §16.4 | 10.20, 10.21 |
  | §17.2 | 9.11 |
  | §19.2 | 10.5, 9.8 |
  | §23.2 | Conflicts, item 1 |

- **Map lines:**

  | map line | stories |
  |---|---|
  | interaction row 1 (age and Consents) | 9.1, 9.2, 9.9 |
  | the Grant rows | 10.2–10.13 |
  | "Auditor → trail" | all |
  | WF-9 done-when | 9.11, 9.14 |
  | WF-10 done-when | 10.3, 10.7, 10.20 |
  | WF-1 done-when "Under 18: no account" | 9.9 |
  | D2 Grant, Policy and Registry states | 10.10–10.12, 10.18, 10.20 |

- **AT fixtures reused:**
  - AT-10, a retry delivered three times gives one event (10.6, 9.6);
  - AT-29, withdrawal and deletion propagate to media, queues, private cached analysis and exports (9.5, 9.11, 9.14).
- **Research cited:** R2, R3, R4, R7, R16, R20–R26, R28, R29 (as corrected), R30, R31; P4, P11 (as corrected), P29, P30; and K1–K4, E1, E2, G1, U1, O1, N1, C1–C3, H1, M1, A1 in §1.0.
- **Counts:** 58 stories (40 in journey 10, 18 in journey 9) · 118 acceptance lines (/m 9 · /s 14 · /r 95).

---

## Lens verdict (2026-10-01)

**fail** — 24 defects.

An independent verifier, following `way/personas/_lens-verifier-brief.md`, checked this lens and changed nothing above. What passed:
- **Ids (check 7).** All ids follow `auditor-<WF>.<n>`.
- **Counts.** The §8 counts are exact: 55 stories and 112 acceptance lines (/m 8, /s 10, /r 94).
- **Runtime lines.** Every story has at least one /r line.
- **Experience (check 6).** §5 answers device, place, moment, feeling and style, and the six care groups.
- **Refuted findings.** No refuted finding is cited as standing (R34, P5, R18's Cloud Tasks claim).
- **Quotes.** The quotes in §1.1–§1.2 were spot-checked against the sources, re-opened on 2026-10-01 with a generic User-Agent and no owner identifier sent. Every quote checked was found: the SDAIA Implementing Regulation, the Law and the DPO Rules, the Sharkawy dual text, Shalakany's PDF, Google Cloud Audit Logs, Access Approval and Access Transparency, GitHub's audit-log docs, the OWASP Logging Cheat Sheet, Microsoft Purview, the NIST OSCAL v5.2.0 catalogue, the ICO and AccessOwl.

The eater lens is not written yet (`way/personas/eater/` holds only `research.md`), so the eater side of the shared stories (10.3, 10.6, 10.7, 10.11, 9.5) could not be cross-checked.

### Observable

1. **auditor-10.3: the event count contradicts itself.** The /r timeline lists 7 events: "Requested · Approved by the eater · Read ×3 · Expired · Read denied (10:06:30Z, GRANT_EXPIRED)". The first /s line says the API "returns the same 6 events … that Trail shows for `object = G-2026-0042`". Both lines cannot pass.
2. **Fixture "trail", used by 10.1, 10.14 and 10.30: a 1,248-event trail cannot exist at 2026-09-28T14:10Z.** 10.1 shows "Intact through event 1,248 · checked 14:10:00Z" on 2026-09-28, and 10.14 shows "Intact · events 1–1,248 · 14:11:10Z". The same 1,248 events must also hold later events: the revocation at 14:20:00Z, the denied read at 14:21:00Z, G-2026-0046 on 09-29 and the 09-30 denials of 10.9. 10.30 then exports September "as-of sequence 1,248". The fixture needs dated sequence numbers.
3. **auditor-10.30 and 10.32: the counts do not match the fixture.** 10.30 says `subj_7Q2M` has "17 events" in September. 10.32's own figures give at least 19: 7 for G-2026-0042, 5 for G-2026-0044 (requested, approved, read, revoked, denied), 2 for G-2026-0046, and "Consent events (5)". The 5 cannot be checked either. The fixture has 4 Consent events plus 9.7's Health event, with no Diary processing Consent and no "given" event before the research withdrawal.
4. **auditor-10.31 (also 10.28): two possible outcomes, and data outside the fixture.** The line says "It delivers numbered parts … ; or AU-01 is asked to narrow the filter", so a verifier cannot tell which outcome passes. The "25,000 events" and "1,200,000 events" are outside the fixture table, which claims to be "used by every acceptance line below" and holds a 1,248-event trail.
5. **auditor-10.10: no lapse value, so the line cannot be observed.** The line says "not answered within the lapse time set in the live Policy version", but gives no value, and the map's Policy (§1 ¶6) has no such field (Conflicts item 10). The read made with G-2026-0045 has no actor or time. If S-07 makes it on 09-28, 10.25's "exactly 3 rows" and 10.33's "reads denied 3" no longer hold. A fixture value labelled `assumption`, plus an actor and a time for that read, are needed.
6. **Fixture gaps: acceptance lines name data the fixture never defines.**
   - 9.12 opens "Grant G-2026-0039", which is not in the fixture.
   - 10.2 and 10.25 need G-2026-0044's requester ("requested 13:55:00Z", no actor).
   - 9.8 uses "a staff member with the quality-review permission", who is not in the staff row.
   - 10.21 needs the فول مدمس record's id, version, time, licence and evidence ids.
   - Line 3 of 10.17 needs v7's reviewer.
   - 10.4 gives "the exact request wording version, in the language the eater saw (en or ar)" with no version id and no language.
   - Line 2 of 9.14 gives "Anomalies shows any raw scan over 30 days … by count" with no count, and it clashes with line 1's run on 2026-09-28 without being marked as a separate fixture.
7. **auditor-10.22: the empty state cannot occur.** The line says "Given no staff besides AU-01 (a fresh system) … 'Only you hold a role. Roles appear here as the platform admin assigns them.'" Under 10.24 (no self-assignment), the read-only Auditor and admin-10.61 ("cannot remove the last Platform admin"), a system where only the Auditor holds a role has nobody who can assign one.
8. **auditor-10.25, 10.33 and §5: speed is promised but never checked.** The stories promise "find the events a question is about in seconds" and "Scope a suspected breach in minutes", and §5 says "Fast: keyboard-first". No acceptance line bounds the time for a filtered list or for the Summary. The only time in any line is the 2 s progress threshold.

### Traced

9. **auditor-10.11: "Revoked by the eater · 14:20:00Z" traces to nothing.** The map's Grant rows end with "approves or declines in Settings" and "auto-expiry". Revoking an active Grant is in no map line or FR line. The story cites neither, and it is not in Conflicts. The support lens also has `revoked`, so the model phase has to add it to the map.
10. **auditor-10.17, and the 10.13 rule "Live Policy versions without a recorded review": the review split traces to nothing.** The lines show "reviewed by A-05 at 07:30:00Z, approved by A-02 at 08:00:00Z". The map says only "qualified review", and the approver lens publishes alone (approver.md §7 item 8: "A single fractional approver publishes alone"). The two lenses disagree on whether a Policy version has a separate reviewer, and this lens does not list the disagreement in Conflicts.

### Complete

11. **Missing step: WF-10 done-when "an admin rolls a model version back and manual logging keeps working".** Neither the registry fixture nor 10.19/10.20 has a rollback event (for example R-16 → R-14, with who, when and why). The "shadow" stage of "shadow → canary → rollout" never appears.
12. **Missing step: interaction row "Support → eater diary | read within the Grant | time box, read-only".** No story shows the Auditor a write attempt inside an active Grant, refused and logged. 10.9 covers only reads made without a Grant. The support lens also defines `GRANT_READ_ONLY` and `GRANT_OUT_OF_SCOPE`, and the Grant states `withdrawn` and `ended` ("Ended early by support at 10:49"). This lens gives the Auditor no view of any of them.
13. **auditor-10.21: Alias approvals have no acceptance line.** The story says "who approved each Food record and Alias", but its only acceptance line covers a Food record. No line shows `alias.approved` with its dialect (EG, Gulf, MSA; F27).
14. **Missing step: interaction row "Eater → app | confirm age 18+, give separate consents … | Consent records (version, time, method)".** No story lets the Auditor see the 18+ confirmation, which R22 ("full legal capacity") and R16 rest on.
15. **auditor-9.1: the Health purposes are incomplete.** The list reads "Health: read workouts · Health: read body mass · Health: write dietary energy". The map asks for consent per "each Health type" and imports "workouts, active energy, body mass", so "Health: read active energy" is missing. The map also calls the write a "food correlation", not dietary energy alone.
16. **auditor-9.5: AT-29 is only half covered.** The story cites AT-29: "consent withdrawal propagate[s] to media, queues, private cached analysis, and exports". The Effect card shows AI requests, Pending Analyses, the private cached analysis and queued uploads, but nothing for media or exports.
17. **auditor-9.13: reports are missing from the export.** The story cites FR-075 ("entries, portions, recipes, targets, and reports"), but its categories are "Entries, Units, Recipes, Targets, Consents".

### Sourced

18. **§1.1–§1.2: no source has a link.** The file holds no URL at all, while both briefs require "link, date, quote". The quotes check out; the links are what is missing. These need links: the SDAIA PDFs, the Sharkawy dual text, Shalakany's PDF, GDPR, the Google Cloud pages, GitHub's docs, Microsoft Learn, OWASP, NIST OSCAL, the ICO and AccessOwl.
19. **§1.1 "Dates and status": the R29 match is overstated.** The lens quotes Shalakany, "the Minister of Communications and Information Technology issued … published in the Official Gazette on November 1st, 2025", and says this "matches the corrected R29". The corrected R29 leaves out the decree type as doubtful, and R29's own evidence (CMS) says the regulations were "not made publicly available until 25 December 2025". The lens brings a doubtful sub-claim back and does not flag the conflict.
20. **§1.1 row "The DPO seat itself" and Conflicts item 3: the Egypt Art. 9(1) wording is the translation's, not the Arabic's.** The lens quotes "approving the results of such evaluation". In the same dual text, the Arabic reads «توثيق نتائج التقييم» ("documenting the results"). Conflict 3 rests its write proposal partly on "approving", while §1.3 itself treats the Arabic prevailing as an assumption.
21. **auditor-9.10 and 9.14: two values have no source and no label.** 9.10 says "backups expire by 2026-10-16 under the disclosed backup lifecycle", which is 30 days after completion. 9.14 says "expected daily". Neither value has a source or an `assumption` or "chosen default" label, and neither FRD §17.2 nor NFR-13 gives a backup period.

### Vocabulary

22. **The role "Support" has two names.** This lens writes "S-07 · Support", "Your roles: Support." and "Support" in the staff fixture. The map's persona is "Support agent", and the admin lens names the role "Support agent" ("Support agent cannot be combined with Platform admin (FR-081)").
23. **Other lenses use different names for the same things, and Conflicts lists none of these differences** (Conflict 5 covers identity fields only):
   - "Lapsed" (10.10) here; "Expired unanswered" / `expired_unanswered` in the support lens.
   - `GRANT_NOT_APPROVED` for a declined Grant (10.7) here; `GRANT_DECLINED` in the support lens.
   - `ROLE_FORBIDDEN` here; `FORBIDDEN_ROLE` in the approver lens.
   - The page "Trail" here; "Audit trail" in the support lens.
   - Grant ids `G-2026-0042` here; `grant_31f0` in the support lens.
   - Here a Grant request has a free-text reason and the scope "diary, read-only" (10.4, 10.25). In the support lens it has a reason from a fixed list plus a note, diary days, areas and a case reference.
24. **auditor-10.24 and the staff fixture: U-19 cannot be set up through the product.** The fixture reads "U-19 Support **and** Platform admin (seeded violation)", but admin-10.59 refuses that combination at save. The acceptance line must say U-19 is seeded below the roles API, in storage. Conflicts should record that this anomaly is a detective control behind admin-10.59.

## Fix round 1 (2026-10-01)

All 24 defects in the lens verdict above are fixed at their root. Throughout, the lens now uses `way/vocabulary.md` (delta D2), so each word below is D2's:
- the role **Support agent**;
- the Grant states **Requested, Approved, Active, Expired, Ended, Withdrawn, Declined, Unanswered**;
- the Policy states, with proposer and approver recorded;
- the Registry stages **Proposed → Shadow → Canary → Rollout · Rolled back**;
- the error codes `FORBIDDEN`, `GRANT_REQUIRED`, `GRANT_NOT_ACTIVE`, `CONSENT_REQUIRED`, `AGE_REQUIREMENT`, `NOT_FOUND` and `VALIDATION_ERROR`;
- **Audit trail** as the only name for the record.

Fixture ids follow the support lens wherever it defines the same thing (`grant_31f0`, `acct_9c41e2`, `staff_*`). Registry ids follow the admin lens (`meal_photo@v6`, `meal_photo@v7`).

Count before the fix: 55 stories and 112 acceptance lines. Count after: 58 stories and 118 acceptance lines. One story was dropped (the old 10.12, self-request) and four were added: 10.12 Ended, 10.13 write refused, 10.23 Alias approvals, 9.9 the age gate. Stories were renumbered; no other file references these ids.

| # | defect | what changed |
|---|---|---|
| 1 | 10.3: 7 events against "6 events" | 10.3's timeline and API line now both list the same **8** events, ids 52, 54–60. The write refused inside the Active Grant is the eighth |
| 2 | a 1,248-event trail cannot exist at 14:10Z | the Audit trail fixture is now an explicit table of **80 dated, numbered events** (2026-09-01 to 2026-10-04). There is one test clock (2026-10-05T09:00:00Z), and live actions append from event 81. 10.1 and 10.15 state 80, 81, 82 and 83 exactly. Export manifests name the as-of event shown in the dialog |
| 3 | 10.30 and 10.32 counts do not add up | every count is recomputed from the table. 10.32: 13 events (9–16, 26, 27, 37, 39, 44). 10.34: Grants 4, reads 4 allowed and 2 refused, writes refused 1, age 1, Consent events 10, Privacy jobs 1, raw-evidence attempts 1. Diary processing and every Health Consent now have their own "given" events (10–16) |
| 4 | 10.31 has two outcomes; bulk data outside the fixture | bulk fixtures **B1** (25,000 events) and **B2** (1,200,000) are defined in the fixture table. 10.33 has one outcome: 12 parts of up to 100,000 rows, plus one failure-and-retry line |
| 5 | 10.10 has no lapse value, actor or time | the state is renamed **Unanswered** (D2). The request window is **72 h** (support lens A6, labelled `assumption`). The refused read is event 80, by `staff_mona` at 2026-10-04T10:30:00Z. Every filter and summary count includes it |
| 6 | fixture gaps | every Grant (`grant_27b4`, `_31f0`, `_31f9`, `_40aa`, `_52c3`, `_5d10`, `_6e21`), every requester, every staff member and every account is defined. Also defined: `rec_fm_eg` (id, version, licence, evidence `ev_1101`, `ev_1102`); Policy v1 and v2 with proposer and approver; request wording `grant-req-1`, in Arabic for `acct_9c41e2`; the retention runs **R-0805** and **R-gap**, separate and with counts; fixture X for 9.18. The undefined "quality-review" staff member is gone: no D2 role may open raw evidence, so 9.8 now shows attempts refused (event 78) |
| 7 | 10.22's empty state cannot occur | replaced by a reachable state, **Held at** 2026-08-31 ("Nobody held a role at this time — the first assignment was … event 1"), in 10.25 |
| 8 | speed promised, never checked | added 10.27: first filtered page ≤1 s p95 on B1, and 10.35: Summary ≤3 s p95 on B1. Both are labelled chosen defaults; NFR-02 is cited as the nearest brief figure. §5 cites both |
| 9 | 10.11 "Revoked" traces to nothing | the state is renamed **Withdrawn (by the eater)** and traced to D2's Grant states, with C3's "revoked at any time" as the benchmark |
| 10 | 10.17's reviewer/approver split traces to nothing | now 10.18. Policy history shows **proposer and approver** per D2: v1 by `staff_dina` alone as sole holder; v2 proposed by `staff_yara`, approved by `staff_dina`. The anomaly rule became "approved by its proposer while the role had two or more holders", with a /m line. The approver lens's separate review signature is listed in Conflicts, item 16 |
| 11 | no rollback, no Shadow | the Registry fixture and 10.20 now run `meal_photo@v7` Proposed → Shadow → Canary 5 % → Rollout 100 % → **Rolled back** (v6 live), with who, when and why. "Live at" covers Shadow, Canary and after the rollback |
| 12 | no write refused inside an Active Grant; no Withdrawn or Ended view | new 10.13 (event 58, `FORBIDDEN`, plus a /s line over every write endpoint); new 10.12 **Ended** by the Support agent (`grant_5d10`); 10.11 **Withdrawn** (`grant_52c3`). 10.2 lists all six states |
| 13 | Alias approvals have no line | new 10.23: «صقعي» Gulf and «لبن» EG with proposer, approver and time, plus a dialect filter. Food and Tier B approvals stay in 10.22 |
| 14 | no 18+ confirmation | new 9.9: the age record (event 9) with version, method and time; a /s line (age 17 → 422 `AGE_REQUIREMENT`, nothing created, one `age.refused` event without identifiers); a refusal count in Consents. 9.1 and 9.2 include the age row |
| 15 | Health purposes incomplete | 9.1 and events 12–15 now cover **Health: read workouts, read active energy, read body mass, write food** (energy and macros as food correlations, P29). 9.7 covers the three reads and the write |
| 16 | AT-29 half covered | 9.5's Effect card adds "Photos and audio held for analysis deleted: 3" and "Prepared export files deleted: 1 (job_exp_4402)". 9.14 shows that export file deleted |
| 17 | reports missing from the export | 9.14's categories are now Entries, Units (portions), Recipes, Targets, **Reports (day and period)** and Consents (FR-075) |
| 18 | no links | new §1.0 lists every source with its link and the source's own date (K1–K4, E1, E2, G1, U1, O1, N1, C1–C3, H1, M1, A1). The §1.1 and §1.2 tables link each citation |
| 19 | R29 overstated | §1.1 "Dates and status" no longer says the two "match". It quotes E2, then r1-refute-b's doubt and CMS's "not made publicly available until 25 December 2025", and records only the refuter's corrected statement |
| 20 | Egypt Art. 9(1) wording | §1.1 now quotes the **Arabic** «… وتوثيق نتائج التقييم» as "documenting the results of the evaluation", and notes that the firm's English says "approving". Conflict 3 now rests on K2 Art. 32(3)(b) "documenting assessment results", E1's Arabic and A1, not on "approving" |
| 21 | backup and retention values unsourced | the 30-day backup lifecycle is labelled `assumption` in the fixtures and in 9.11. The retention run is now **hourly**, a chosen default derived from FR-078's 24 h and 30-day limits (a daily run could leave audio nearly 48 h old) and stated in the fixture |
| 22 | "Support" has two names | **Support agent** everywhere: fixtures, screens, refusal text ("Your role: Support agent"), §5, §6 |
| 23 | names differ from other lenses | adopted D2: Unanswered; `GRANT_NOT_ACTIVE`; `FORBIDDEN`; **Audit trail** (section, views and `audit_trail.*` event names); support-lens ids `grant_31f0`, `acct_9c41e2`, `staff_mona`; and the support lens's request fields (reason code, diary days, areas, duration, case reference). Remaining differences are listed in Conflicts, item 16 (support pre-D2 states and codes, staff id styles, approver review signature, admin "Draft"/"full"). Names not yet in D2 are listed in item 15 |
| 24 | the separation-of-duties violation cannot be made through the product | `staff_sod_seed` is now "written directly into the role store by the test seed … below the roles API", in the fixture and in 10.26. A second rule, "roles held with no assignment event", catches it. Conflicts, item 17 records it as a detective control behind admin-10.59 |
