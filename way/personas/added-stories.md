# Stories added at the model phase (delta D5, 2026-10-01)

The model phase (`way/model.md` §2.7) found six behaviours the join decided (`way/join.md`) with no story. Each story below follows the lens brief (Given / When / Then naming the data and the screen or interface; at least one `/r` line), uses `way/vocabulary.md` (D2–D4) and `way/seed.md`, and is verified with the model phase. Ids continue each persona's numbering after its last story.

## admin-10.76 · Change the Grant settings, and a running Grant keeps its own
As the Platform admin, I change how long a Grant may last and the other Grant settings in one versioned place, so that support access rules change deliberately and never under a Grant already running. · Trace: J2 (Grant settings version), FR-081, model §2 E21 · gap G1
- `/r` Given Grant settings version 1 In use (seed §4.6: longest Grant 1 hour), When `admin.a` opens **Settings › Grant settings**, sets the longest Grant to 2 hours, enters the reason "Longer reads for export cases" and saves, Then the page reads "Grant settings version 2 · In use · saved by Admin A." and **Audit trail** shows `grant_settings.version.saved` with before → after and the reason.
- `/r` Given `grant_31f0` was requested under version 1 and is Active, When version 2 is saved, Then `grant_31f0`'s panel in **Grants** still reads its own end time, and `GET /v1/admin/grants/grant_31f0` returns `settings_version: 1`.
- `/r` Given version 2 In use, When `support.a` opens the Grant form in **Grants**, Then the duration choices go up to 2 hours.
- `/r` Given a longest Grant of 0 or 25 hours, When it is saved, Then the field reads "Choose 15 minutes to 8 hours" and nothing is saved (`VALIDATION_ERROR`).
- `/r` Given `support.a`, When `PUT /v1/admin/grant-settings` is called, Then it returns 403 `FORBIDDEN` and writes `access.refused`.

## auditor-10.42 · Leave a review note on the Audit trail
As the Auditor, I add a short note to an event I reviewed, so that the next reviewer and a regulator see what was checked and why. · Trace: J17 (review notes), FR-081, FR-082 · gap G2
- `/r` Given seeded event `grant.read` for `grant_31f0` in **Audit trail › Events**, When `auditor.a` adds the note "Checked against case CASE-1182: read inside the Grant" and saves, Then a new event `audit_trail.review_noted` appears linked to it with the note, Auditor A. and the time, and the original event is unchanged.
- `/r` Given a note of 501 characters, or one containing an eater's account id, When it is saved, Then it is refused beside the field with "Up to 500 characters, with no eater details" (`VALIDATION_ERROR`).
- `/r` Given `admin.a`, When `POST /v1/admin/audit-trail/review-notes` is called, Then it returns 403 `FORBIDDEN`.

## auditor-10.43 · Old Audit trail events leave on schedule, and the trail still proves itself
As the Auditor, I see that events older than the retention period are removed by a scheduled run and that the trail's chain still checks, so that we keep records as long as the rules ask and no longer. · Trace: J18 (5-year retention), FR-082, NFR-13 · gap G3
- `/s` Given a test trail with 3 events dated 5 years and 1 day before the test clock and 2 newer events, When the retention job runs, Then the 3 old events are removed, one `audit_trail.retention_run` event records "3 events removed, oldest kept dated …", and the chain check over the remaining events passes.
- `/r` Given that run, When `auditor.a` opens **Audit trail › Summary**, Then it reads "Last retention run: removed 3 events older than 5 years · chain check passed".

## eater-5.44 · A saved Plan expires at the end of its Day
As the Eater, I find yesterday's unused Plan marked as past, with a way to plan again, so that an old Plan is never logged by mistake. · Trace: J130 (Plan expiry at the diary-day boundary), FR-045 · gap G4
- `/r` Given Sam's Saved Plan for 30 Sep that he never confirmed and his diary-day boundary 00:00, When he opens **Capture & Plan** on 1 Oct, Then the Plan card reads "From 30 Sep · not logged" with "Plan again", and it offers no "Ate as planned".
- `/r` Given that card, When "Plan again" is tapped, Then **Meal planner** opens with the same available foods and limits for today, and no Entry is created.
- `/r` Given the expired Plan, When `POST /v1/consumption` names it as `source_plan_id`, Then it returns 422 `VALIDATION_ERROR` "This plan is from another day", and the Day total is unchanged.

## eater-7.25 · A new activity credit is offered, never applied by itself
As the Eater in activity-adjusted mode, I am told when the reviewed credit rules change and choose whether to take them, so that my food budget never changes without my say. · Trace: J103, FRD §12.2 ("a visible user-approved credit factor and cap"), approver-10.69 · gap G5
- `/r` Given Sam approved credit 50 % up to 300 kcal and Policy version 2 with credit 40 % up to 250 kcal comes In effect, When he opens **Settings → Activity**, Then it reads "Your credit: 50 % up to 300 kcal" and offers "New activity credit available: 40 % up to 250 kcal — Review"; Today still credits 50 % up to 300 kcal.
- `/r` Given he taps Review and then Approve, When a 400 kcal workout imports, Then Today shows an exercise credit of 160 kcal, and his Target history on **Progress** shows the new Target version with "Credit 40 % up to 250 kcal · Policy v2".
- `/r` Given he taps "Not now", Then nothing changes and the offer stays in Settings → Activity without a badge or notification.

## admin-10.77 · Publish a new consent Wording after the privacy review
As the Platform admin, I publish a reviewed consent Wording as a new version, so that eaters see the new text and are asked again only where the purpose changed. · Trace: J26 (Wording), J40 (permission "Publish wording"), FR-076, FR-082 · gap G6
- `/r` Given Wording `ai-processing` version 1 In use and version 2 Proposed with the note "privacy review 2026-09-30", When `admin.a`, who holds "Publish wording", publishes version 2 in **Settings › Wordings**, Then the page reads "ai-processing · version 2 · published" and **Audit trail** shows `wording.published` with the reviewer note.
- `/r` Given version 2 changes the purpose text, When Faisal (AI Consent Given under version 1) next opens **Capture & Plan**, Then a sheet shows the new text with "Give consent" and "Not now", and until he gives it, photo analysis is not sent (`CONSENT_REQUIRED`); his Consent record keeps version 1 with its time.
- `/r` Given an account without "Publish wording", When `POST /v1/admin/wordings/ai-processing/versions/2/publish` is called, Then it returns 403 `FORBIDDEN`.
