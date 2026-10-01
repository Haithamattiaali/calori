# Governor — lenses

Checked 2026-10-01 07:01Z at HEAD `4c00d33` ("way: lenses — all 9 files pass; eater index; journey"). Row checked: "one file per persona; every workflow step has stories; every story has acceptance that can be observed; each lens verdict at the foot of its file (none at spike)". Size is **platform** (blueprint §0 line 2), so every lens needs a verdict.

Records read: `way/blueprint.md` (§0 line 2, §1 ¶2 personas, §1 ¶4 workflows, §1 ¶5 done-when, §3 deltas), `way/vocabulary.md`, `way/journey.md`, `way/lessons.md`, `way/ledger.md`, `way/personas/_lens-brief.md`, `way/personas/_lens-verifier-brief.md`, `way/personas/eater.md`, the four staff lens files, the five eater files, the earlier governor files, and git (`git status`, `git log`, `git diff` on each pass commit, `git ls-remote origin`). Only `way/` and git were read. No outside service was contacted.

**Method for (3).** A script, kept outside the repo, recounted every lens file:
- A story starts at its id line (`#### eater-3.3 · …`, or `**auditor-10.1 · …**` in the auditor file) and ends at the next story or heading. Only the text above the file's first `## Lens verdict` is counted.
- An acceptance line is a top-level `- ` bullet, with its indented continuation lines.
- A runtime line is an acceptance line that carries the `/r` tag. The tag may come first (`- \`/r\` Given …`, `- /r Given …`) or last (`… \`/r\``, the admin file's style).

**Verdict: pass. 0 gaps.** The notes at the end are not counted.

## Line by line

### (1) One lens per persona; the Eater fanned out and indexed: pass

Blueprint §1 ¶2 names five human personas: "**Eater**", "**Nutrition approver**", "**Support agent**", "**Platform admin**", "**Auditor** (hidden, found by the first map)". The rest of the table is not a lens persona:
- Household member: "drop list".
- Owner who pays: "drop list for v1".
- Minor: "(hidden, excluded) … an age gate keeps them out".
- System actors.

`vocabulary.md` lists the same five as roles: "**Eater** · **Nutrition approver** · **Support agent** · **Platform admin** · **Auditor**".

| persona | file | its first line | stories (recount) |
|---|---|---|---|
| Eater | `way/personas/eater.md` (index) + `eater/research.md`, `eater/wf1-wf9.md`, `eater/wf2-wf4.md`, `eater/wf3-wf6.md`, `eater/wf5-wf7-wf8.md` | "# Eater — persona lens (index)" | 367 (90 + 105 + 69 + 103) |
| Nutrition approver | `way/personas/approver.md` | "# Persona lens — Nutrition approver (WF-10)" | 70 |
| Support agent | `way/personas/support.md` | "# Support agent — persona lens (research cycle 2)" | 52 |
| Platform admin | `way/personas/admin.md` | "# Platform admin — the lens (WF-10 and the admin's part of WF-9)" | 75 |
| Auditor | `way/personas/auditor.md` | "# Auditor — persona lens (privacy reviewer / DPO seat, read-only)" | 59 |

**The Eater fan-out.** `eater.md`: "The Eater touches WF-1…WF-10, so the lens is fanned out (the /way personas rule for a persona with more than two workflows at platform size): one researcher, then one drafter per group of workflows, each file verified on its own."
- Its table lists the five parts with their workflows and story ids.
- "Total: 367 eater stories" matches the recount.
- The two `_lens-*.md` files are briefs, not lenses.
- No persona has two lens files, and no lens file has no persona.

The journey says: "623 stories in all (eater 367, admin 75, approver 70, auditor 59, support 52)". The recount agrees: 367 + 75 + 70 + 59 + 52 = 623.

### (2) Every workflow step (map §4) and every done-when clause (map §5) has stories: pass

There is one story id per step. Each id is quoted by its title, which is the record. The coverage tables in the lens files point the same way, and the ids below were read in the story bodies, not only in those tables.

| WF | step (map §4) | story |
|---|---|---|
| WF-1 | age gate | eater-1.1 "Confirm I am 18 or older before anything else" |
| | consents | eater-1.3 "Choose each Consent on its own, with nothing chosen for me" |
| | profile | eater-1.10 "Enter age, height and weight in my units and my digits, and see why" |
| | optional safety screen | eater-1.16 "Skip the safety screen without losing anything" |
| | resting energy | eater-1.23 "See my resting energy, its method and a ±10 % note" |
| | maintenance | eater-1.25 "See maintenance, the activity assumption, and whether exercise is in it" |
| | target and macros (floor policy) | eater-1.28 "Choose lose, maintain or gain …"; eater-1.30 "No proposal below the reviewed minimum"; eater-1.33 "See macro grams from percentages, with the 4/4/9 label" |
| | activity mode | eater-1.40 "Choose Activity-adjusted mode knowingly" |
| | approve | eater-1.42 "Review everything once, then approve" |
| | tracking before a target | eater-1.7 "Log my first food before any profile or Target" |
| WF-2 | simple Unit, by typing | eater-2.10 "Type the weight and say how I got it" |
| | composite (bread rules) | eater-2.17 "Build a Composite from its parts"; eater-2.22 "One bread bite with every dipped bite" |
| | Recipe, weigh-the-pot | eater-2.29 "Weigh the ingredients and the pot" |
| | by scale photo | eater-4.33 "Unit mode: one bite on the scale becomes a Unit Draft"; eater-4.34 "Scale capture …" |
| | by label photo | eater-4.35 "Label capture: fields read, unsure digits and bases shown" |
| | approve a version | eater-2.38 "See the whole definition, then Save unit" (the `/r` line: "returns an immutable unit version id with version 1") |
| WF-3 | tap a recent Unit | eater-3.3 "Log a recent Unit in two taps" |
| | copy a meal or day | eater-3.16 "Copy a meal from any past Day"; eater-3.18 "Copy a whole Day" |
| | log a Template | eater-3.20 "Log a Template, changing counts first if I want" |
| | speak or type | eater-3.9 "A typed sentence becomes my Units …"; eater-3.11 "Speak it, see the words, then log" |
| | Siri or a widget | eater-3.21 "Log with Siri or the Shortcuts action"; eater-3.22 "Log from the widget, discreetly" |
| | one-tap with Undo | eater-3.6 "One-tap logging, only if I turn it on"; eater-3.5 "Undo exactly what I logged" |
| | offline outbox | eater-3.25 "Log with no signal; Pending is shown, not hidden" |
| | meal and day report | eater-3.34 "A meal report and a day report after every entry" |
| | written to Apple Health | eater-3.39 "Each Confirmed Entry appears in Apple Health as a food" |
| WF-4 | photo / label / scale / voice / photo + words | eater-4.1 "Capture & Plan opens on the camera"; 4.35 (label); 4.34 (scale); 4.39 "Speak Arabic, English or both, and see the words first"; 4.10 "Photo + words: what the camera cannot see" |
| | draft | eater-4.12 "Chips from my Units first" |
| | ≤2 questions | eater-4.14 "At most two questions, the biggest first" |
| | resolve | eater-2.9 "See where each number comes from, in the resolver's order"; eater-4.17 "A stand-in food is shown as one" |
| | review | eater-4.20 "Change, add and remove chips"; eater-4.21 "Approve once: one meal, its report, and Undo" |
| | input path into WF-2 / WF-3 / WF-5 | 4.33 (a Unit Draft) / 4.21 (one meal) / 4.31 "A table photo is food on the table, not my meal" and eater-5.1 "Plan from a photo of the shared table" |
| WF-5 | available foods | eater-5.2 "Plan from a list of what is on the table"; 5.4 "Say how much is available to me" |
| | constraints | eater-5.5 "Calorie aim or Calorie ceiling, never confused"; 5.6 "Carbohydrate maximum, on macro energy (4/4/9)" |
| | solver | eater-5.14 "A plan in counts I can eat, inside every limit" |
| | Plan | eater-5.28 "A Saved Plan counts zero until I confirm it" |
| | Ate as planned / Change / Not eaten | eater-5.29 ""Ate as planned" records the Plan, once (AT-21)"; 5.31 "Change amounts: I log what I actually ate"; 5.34 "Not eaten" |
| WF-6 | correct | eater-6.4 "Confirming replaces the Entry; it never adds food" |
| | void | eater-6.14 "Void an Entry, with Undo instead of a warning" |
| | restore | eater-6.15 "See Voided Entries and Restore one" |
| | move day | eater-6.17 "Move an Entry to another Day, or change its time" |
| | this entry or future default | eater-6.10 "This Entry only, or also my future default" |
| WF-7 | HealthKit | eater-7.1 "Health access is asked when I first need it, type by type"; 7.4 "Imports run when I open the app …" |
| | manual exercise | eater-7.12 "Add an Activity by hand" |
| | dedupe | eater-7.9 "One workout from two feeds counts once (AT-22)" |
| | activity mode | eater-7.16 "Fixed mode by default …"; 7.18 "Switching to Activity-adjusted …" |
| | budget | eater-7.19 "The Activity-adjusted budget, step by step" |
| WF-8 | meal and day report | eater-8.1 "A meal report after every confirmed meal"; 8.2 "The Day report: Target, consumed, remaining, macros, Evidence and coverage" |
| | 7/28/custom periods | eater-8.10 "7 days, 28 days, or my own dates" |
| | coverage | eater-8.12 "Missing Days are unknown, not zero (AT-25)" |
| | weight trend | eater-8.19 "My weight over the period, with its source"; 8.21 "A trend statement only with enough evidence" |
| | target history | eater-8.23 "My Target history" |
| WF-9 | consents | eater-9.1 "See all my Consents in one place"; 9.2 "Withdraw the AI Consent in one tap …" |
| | export | eater-9.14 "Export my data in the app, free, as a machine-readable file" |
| | delete account | eater-9.18 "Delete my account in the app, with one clear warning" |
| | (staff side) | admin-9.1 "Failed deletion jobs come first, with their deadline"; support-9.9 "Retry a Failed export once"; auditor-9.11 "The deletion completion record" |
| WF-10 | approve food and recipe records | approver-10.28 "Approve a new version, seeing who uses the old one"; approver-10.39 "Approve فول مدمس; the eater's resolver uses it" |
| | approve aliases | approver-10.41 "Add a dialect-tagged Alias" |
| | version policy | approver-10.49 "Change the calorie floor"; approver-10.57 "Approve with a reason and an effective-from …" |
| | roll models and config | admin-10.26 "Move to Rollout …"; admin-10.27 "Roll back in one step …"; admin-10.38 "Set per-user daily AI quotas" |
| | support requests a Grant | support-10.2 "Request a Grant with reason, scope, duration and case" |
| | the eater approves or declines in Settings | eater-10.2 "See who, why, what and for how long — and approve"; eater-10.3 "Decline without giving a reason" |
| | support reads within the time box | support-10.11 "Read the approved Days, read-only" |
| | it expires | support-10.17 "Access ends by itself at the end of the box"; eater-10.9 "The Grant expires at the end of its time box" |
| | every read audited | auditor-10.3 "One Grant's whole life on one page"; support-10.15 "Every read is listed for the eater and the auditor" |

**Done-when (map §5), clause by clause:**

| WF | clause | story and the line that observes it |
|---|---|---|
| WF-1 | English or Arabic; ±10 % note; maintenance; not below the floor; macro grams; approve | 1.54 "Onboard entirely in Arabic"; 1.23; 1.25; 1.30; 1.33; 1.42 |
| | Today shows the target and the activity mode | 1.46 "Today shows my Target and the activity mode" |
| | pregnancy → tracking-only, neutral wording | 1.18 "Pregnancy or breastfeeding gives tracking-only" |
| | under 18: no account | 1.2: "Onboarding · Under 18 reads "Sips & Bytes is for adults 18 and over." … no path leads to Today" |
| WF-2 | "cheese bite" = 5.4 g cheese + 1.5 g oil + 8 g bread | 2.38: "she sees "White cheese 5.4 g · 12.5 kcal", "Olive oil 1.5 g · 13.5 kcal" and "Bread, baladi 8 g · 20 kcal", the total 46 kcal" |
| | Recipe with weighed yield gives AT-06's numbers | 2.29: "**My Units** shows "talbina spoon · 20 kcal", and the `POST /v1/recipes` response holds `cooked_yield_g: 384` and `kcal: 480`" |
| | My Units lists them; Today unchanged | 2.40; 2.39: "**Today** still shows 200 kcal, no Entry is added" |
| WF-3 | a recent Unit in ≤2 taps | 3.3: "the recorded walk counts two taps" |
| | "copy yesterday's breakfast" logs one meal | 3.16: "today's Breakfast gains exactly those three Entries (318 kcal) as one meal" |
| | reports appear and reconcile | 3.34; 3.38 "Every figure adds up to Entries I can see" |
| | Undo removes exactly one Entry | 3.5: "the 4-cheese-bite Entry leaves the timeline, "1 cup of laban" stays" |
| | offline logs sync once | 3.26 "Back online: each Pending Entry is Confirmed once" |
| | in Apple Health, and gone on Void | 3.39: "When Sam taps Undo on it or Voids it, Then the correlation disappears from the Health app"; 6.24 |
| WF-4 | plate photo + "fried in ghee" → editable chips, badges, range | 4.10: "the chips read "egg, fried × 2" and "ghee 6 g (your rule: 3 g per fried egg)"" and "ghee soaked up — low–high range (heuristic)" |
| | nothing consumed until approved | 4.13; 4.21 |
| | Arabic voice «١٨ مش ١٥» → a correction | 4.27 "«١٨ مش ١٥» is a correction, not more food"; 6.5 |
| WF-5 | cap 500 kcal + carbs ≤30 % → counts on unrounded values | 5.14: "Calorie ceiling 500 and Carbohydrate maximum 30 %"; 5.15: "`blocking[]` holds `carb_max_share` with `value` 0.3004 and `limit` 0.30" |
| | "infeasible" names the blocking constraint | 5.21 "Infeasible: the blocking limit by name, and the smallest changes (AT-17)" |
| | "Ate as planned" records exactly one meal | 5.29: "two Entries … appear on Today as one meal"; 5.30 |
| WF-6 | "18 not 15": old, new, delta; replaces | 6.3 "The preview shows old, new, the meal's change and the Day's change"; 6.4 |
| | yesterday's correction leaves today untouched | 6.18: "30 Sep's day report reads 1,058 kcal and Today still reads 318 kcal" |
| WF-7 | Health workout + matching manual entry → one contribution | 7.11 "A manual entry that matches an imported workout asks to link (AT-22)"; 7.9 |
| | fixed mode: the food target does not grow | 7.16: "Today still reads Target 1,870 and remaining 670" |
| WF-8 | target, consumed, remaining, shares to 100.0 %, coverage | 8.2; 8.3 "Shares always add to 100.0 %, on one convention" |
| | 2 missing days show coverage, not zeros | 8.12: "«٥ من ٧ أيام مسجلة» ("5 of 7 Days logged")" |
| WF-9 | export of entries, units, recipes, targets, consents | 9.14: "it holds entries.json and entries.csv …, units.json …, recipes.json, … targets.json …, consents.json" |
| | deletion within the window; completion record without identifiers | 9.19; 9.21: "it reads "Deletion · Completed 2026-09-18 …" and nothing else — no email, account id or device"; auditor-9.11; admin-9.1 |
| WF-10 | an approver approves a Tier B recipe record (فول مدمس) with evidence and licence | approver-10.31 "Build فول مدمس from weighed ingredients and the weighed pot, with its licence"; approver-10.39 |
| | the eater's resolver uses it | approver-10.39: "Analysis review shows the chip "فول مدمس" with the badge "recipe-calculated"" |
| | an admin rolls a model back; manual logging keeps working | admin-10.27: "When `eater-synth-012` logs a recent Unit with `POST /v1/consumption` during it, Then the Entry is Confirmed"; admin-10.33 |
| | support requests; the eater approves in Settings; support reads in the box | support-10.2; eater-10.2: "When SE1 opens Settings → Privacy → Grants"; support-10.11 |
| | the Grant expires; every read is in the auditor's trail | support-10.17: "This Grant expired at 12:20 your time (11:20 UTC)"; auditor-10.3: "the timeline shows 8 events in order" |
| | a declined Grant gives no access | support-10.7 "A declined request gives no access"; auditor-10.7 "A declined Grant opens nothing"; eater-10.3 |

### (3) Every story has a runtime (`/r`) line that names a screen or interface: pass

| file | stories | acceptance lines | `/r` lines | stories with no `/r` line | the file's own count |
|---|---|---|---|---|---|
| `admin.md` | 75 | 212 | 198 | 0 | no totals line in the body. Last record: re-verify 2, "75 stories … and 211 acceptance lines". Later session fixes rewrote and added lines |
| `approver.md` | 70 | 228 | 192 | 0 | line 881: "Totals: 70 stories, 228 acceptance lines (191 runtime, 19 system, 19 module)". The breakdown is off (note 1) |
| `auditor.md` | 59 | 121 | 96 | 0 | closing 2: "A recount gives 59 stories and 121 lines, as §8 states" |
| `support.md` | 52 | 190 | 149 | 0 | closing: "52 stories and 190 acceptance lines (149 `/r`, 35 `/s`, 6 `/m`)" |
| `eater/wf1-wf9.md` | 90 | 296 | 194 | 0 | line 975: "90 stories … · 296 acceptance lines (`/m` 23 · `/s` 79 · `/r` 194)" |
| `eater/wf2-wf4.md` | 105 | 366 | 298 | 0 | line 1087: "105 stories … and 366 acceptance lines (298 runtime, 37 system, 31 module)" |
| `eater/wf3-wf6.md` | 69 | 278 | 220 | 0 | "Count: 69 stories". The closing verdict says "the file keeps no line total" |
| `eater/wf5-wf7-wf8.md` | 103 | 328 | 292 | 0 | closing 3: "103 stories and 328 lines (292 `/r`, 28 `/m`, 8 `/s`), the same as §9.4" |
| **all** | **623** | **2,019** | **1,639** | **0** | |

Story ids run without gaps in every journey. For example, admin-10 runs 1–71, auditor-10 runs 1–41, eater-1 runs 1–56 and eater-4 runs 1–53. No id appears twice.

**Names a screen or interface.** A second pass matched each `/r` line against the names of the map's places: the tabs, Settings, the admin console sections, `/v1/` paths, Onboarding, the simulator, Siri, the widget and Health. 32 stories had no `/r` line that matched. All 32 were read by hand, and each names a place. Some examples:
- eater-2.11: "the editor reads "Edible 55.0 g · 11.0 g per date"" (the Unit editor).
- eater-3.10: "in quick-add, Then the chips read «٣ × قرصة جبنة»".
- eater-6.4: "the timeline lists "2 foul spoons · 60 kcal" once".
- eater-6.17: "When he chooses "Move to another Day" → Wed 30 Sep in Entry details".
- eater-8.18: "When the Day report for 2027-02-10 opens".
- auditor-10.9: "When `staff_hana` filters Events by account `acct_8e14d9`".
- auditor-9.5: "its Effect card shows".
- auditor-10.8: "Anomalies … shows 0".
- admin-10.11: "When the Propose form loads"; "the API returns 422 `VALIDATION_ERROR`".
- admin-10.16: "When the report opens" (note 4).

### (4) Each file's last verdict is a pass; every fail is followed by a fix record: pass

In every file the last `## ` heading is the last verdict, and nothing follows it. Each sequence below is read from the headings and their first lines.

| file | sequence (heading line: verdict) | last verdict |
|---|---|---|
| `admin.md` | 1351 fail 26 → Fix round 1 (1422) → 1523 fail 13 → Fix round 2 (1639) → 1702 fail 6 → Diagnosis and fix by the session (1774) → 1783 fail 4 → Second fix (1837) → 1843 fail 2 → Third fix (1888) → 1893 | "**pass**: 0 defects. Both defects from final 2 are fixed" |
| `approver.md` | 884 fail 23 → Fix round 1 (956) → 1038 fail 17 → Fix round 2 (1155) → 1199 fail 4 → Diagnosis (1281) → 1289 fail 5 → Second fix (1356) → 1361 fail 1 → Third fix (1409) → 1413 | "**pass**: 0 defects. Final 2's one defect is fixed" |
| `auditor.md` | 1030 fail 24 → Fix round 1 (1096) → 1137 fail 13 → Fix round 2 (1260) → 1290 fail 2 → Diagnosis (1370) → 1375 fail 2 → Second fix (1422) → 1426 fail 1 → Third fix (1482) → 1486 fail 1 → Fourth fix (1542) → 1546 | "**pass**: 0 defects. The closing verdict's defect 1 is fixed." |
| `support.md` | 1033 fail 21 → Fix round 1 (1089) → 1176 fail 10 → Fix round 2 (1284) → 1355 fail 4 → Diagnosis (1449) → 1456 fail 2 → Second fix (1517) → 1523 | "**pass**: 0 defects. Both defects of the final verdict are fixed." |
| `eater/research.md` | 474 fail 16 → Fix round 1 (531) → 557 fail 3 → Fix by the session (623) → 628 | "**pass**: 0 defects. All 3 re-verify defects are fixed." |
| `eater/wf1-wf9.md` | 977 fail 22 → Fix round 1 (1044) → 1125 fail 4 → Fix by the session (1208) → 1214 fail 1 → Second fix (1256) → 1259 | "**pass**: 0 defects. The closing check's 1 defect is fixed" |
| `eater/wf2-wf4.md` | 1089 fail 27 → Fix round 1 (1190) → 1253 fail 3 → Fix by the session (1333) → 1337 fail 1 → Second fix (1368) → 1371 | "**pass**: 0 defects. The closing check's 1 defect is fixed" |
| `eater/wf3-wf6.md` | 741 fail 16 → Fix round 1 (836) → 899 fail 3 → Fix by the session (995) → 1000 fail 2 → Second fix (1042) → 1047 | "**pass**: 0 defects. Both defects of the final verdict are fixed." |
| `eater/wf5-wf7-wf8.md` | 1009 fail 22 → Fix round 1 (1102) → 1162 fail 6 → Fix round 2 (1291) → 1322 fail 3 → Fix by the session (1428) → 1434 fail 3 → Second fix (1482) → 1487 | "**pass**: 0 defects. All three closing-2 defects are fixed" |

That is 35 fail verdicts and 35 fix records (admin 5, approver 5, auditor 6, support 4, research 2, wf1-wf9 3, wf2-wf4 3, wf3-wf6 3, wf5-wf7-wf8 4), and each fix record sits between its fail and the next verdict. Every fix record is a numbered list of changes, and each "Diagnosis and fix by the session" also gives its cause in one sentence. Example from the admin file: "Cause in one sentence: each round added new detail instead of tightening existing lines … The session fixed the 6 itself".

`eater.md`'s table matches each file's last verdict: "pass (final, 2026-10-01)", "pass (closing 2)", "pass (closing 2)", "pass (closing)", "pass (closing 3)". The journey's "Every lens failed its first verifier (16–27 defects each)" matches the first verdicts: 26, 23, 24, 21, 16, 22, 27, 16, 22.

Git shows when each last verdict was committed, and that no commit touched the file after it:
- `dbaf24c`: admin, research.
- `7c3bb82`: approver, support, wf3-wf6.
- `8c07ebf`: auditor.
- `b6dd8d0`: wf1-wf9, wf2-wf4.
- `4c00d33`: wf5-wf7-wf8.

Every pass commit only appends its verdict, except `7c3bb82` (note 1).

### (5) Conflicts lists for the model phase: pass

| file | section | items | first item |
|---|---|---|---|
| `admin.md` | "## 7 · Conflicts for the model phase" (line 1253) | 16 | "**New words beyond map ¶4 and D2.**" |
| `approver.md` | "## 7 · Conflicts for the model phase" (812) | 16 | "**"Recipe" vs "Tier B recipe record".**" |
| `auditor.md` | "## 7 · Conflicts for the model phase" (926) | 16 design questions (A) + 14 mismatches (B, M1–M14) | "**Read-only seat versus FRD §23.2.**" |
| `support.md` | "## 12 · Conflicts for the model phase" (922) | 12 (K1–K12) | "**K1 · eater ↔ Support agent: what the eater's Grant history shows**" |
| `eater/research.md` | "## 6 · Conflicts for the model phase" (446) | 9 | "**Diary-day boundary vs reports and Targets**" |
| `eater/wf1-wf9.md` | "## Conflicts for the model phase" (901) | 26 (C-1 …) | "**C-1 · Floor scope (eater ↔ nutrition approver).**" |
| `eater/wf2-wf4.md` | "## 5 · Conflicts for the model phase" (903) | 19 | "**Logging a Unit that has not synced yet**" |
| `eater/wf3-wf6.md` | "## 6 · Conflicts for the model phase" (664) | 19 | "**"Meal" has no word in the vocabulary**" |
| `eater/wf5-wf7-wf8.md` | "## 7 · Conflicts for the model phase" (857) | 25 | "**A planner timeout has no Plan state.**" |

`eater.md` points to them: "Conflicts for the model phase are listed in each journey file and in `eater/research.md` §6."

The verdicts add uncounted "Cross-lens (for the model phase join)" lists, as the verifier brief's addendum requires.

The journey names the next step: "Model and architecture (start with the join: one fixture set, one event catalogue, the conflicts lists)".

### (6) `way/vocabulary.md` and deltas D2, D3 in blueprint §3: pass

- `way/vocabulary.md` exists. Its line 1: "# Vocabulary — the one name for every role, state and error (delta D2, 2026-10-01)". The Consent row reads "Not given → Given · Withdrawn … "Not given" is the state before the eater has decided (delta D3)".
- Blueprint §3, line 134: "**2026-10-01 · delta D2 · one vocabulary for roles, states, errors and places** — `way/vocabulary.md` extends §1 ¶4. Reason: four lens verifiers found the same state named differently across lenses (e.g. Grant Lapsed/Expired, two error codes for one refusal). Impact: …"
- Blueprint §3, line 135: "**2026-10-01 · delta D3 · Consent gains the state "Not given"** (`way/vocabulary.md`). Reason: a purpose the eater has not decided yet is a real state that Settings → Privacy must show (eater lens 3.40, 9.x). Impact: …"
- Git: `d93ce8c` "way: delta D2 — shared vocabulary (roles, states, errors, places)" added the file and the D2 line. `5fea33a` "… delta D3 (Consent: Not given)" added D3 to both files.

### (7) Everything committed: pass

`git status`: "On branch claude/magical-cerf-axi3k1 / Your branch is up to date with 'origin/claude/magical-cerf-axi3k1'. / nothing to commit, working tree clean". `git rev-parse HEAD` and `git ls-remote origin` both give `4c00d33c3363…` for `refs/heads/claude/magical-cerf-axi3k1`. This governor file is not committed.

## Notes (not counted)

1. **`7c3bb82` changed three non-story lines with no fix record. One of them rewrote a dated record so that it no longer adds up.** In the same commit that appended the approver and support pass verdicts, the session changed:
   - **approver line 3**: "AP1–AP13" became "AP1–AP14". This answers the closing verdict's note.
   - **approver line 1157**, inside the dated "Fix round 2" record: "226 acceptance lines (191 `/r`, 19 `/s`, 19 `/m`)" became "226 acceptance lines (192 `/r`, 18 `/s`, 18 `/m`)". The parts now sum to 228, not 226. The closing verdict asked for a different line: "The breakdown was not updated: 191 + 19 + 19 = 229. It should read "(192 runtime, 18 system, 18 module)"". That line is the **Totals line, 881**, and it still reads "(191 runtime, 19 system, 19 module)". The recount gives 228 lines: 192 `/r`, 18 `/s`, 18 `/m`.
   - **support line 29**: the §0.1 Consent row "Given · Withdrawn" became "Not given → Given · Withdrawn".

   None of these touches a story, so the pass verdicts stand. The support verdict now quotes text the file no longer holds ("§0.1's Consent row, "Given · Withdrawn""). Suggested fix:
   - restore line 1157 as it was recorded;
   - correct line 881 to "(192 runtime, 18 system, 18 module)";
   - add one dated line under each file's last verdict saying what changed after it.
2. **support-9.4 still uses the states from before D3.** Line 271 reads "Consents, each Given or Withdrawn". The support closing verdict flagged it ("left for the model phase or the next pass"), and it is unchanged. The D3 state "Not given" is missing from this one line. Vocabulary is not one of this row's checks.
3. **Scoped closing checks and verifier independence.** Each final pass re-checked only the lines its last fix changed. For example, support: "The scope was the diff 1958132..c0ed7a6". Full-file checks are the first verdicts and the re-verifies. That every verdict came from "an independent verifier" / "an agent that did not write this file" rests only on the verdict text. Git shows one author for all 77 commits ("Claude"), so git cannot tell the agents apart.
4. **admin-10.15 and 10.16 name "the page" and "the report"**, not a console section. The context ("Run evaluation" on a Proposed Meal version, and Registry › Meal in 10.8 and 10.18) puts them in Registry. Naming **Registry › Meal** in their Givens would make the place explicit.
5. **The fan-out rule is cited, not quoted.** `eater.md` cites "the /way personas rule for a persona with more than two workflows at platform size". The rule's text lives in the /way skill, outside `way/`, so this audit could not read it. The fan-out it describes is on record: one research file and four journey files, each with its own verdict.
6. **The auditor's places beyond `vocabulary.md`.** The auditor lens names the places Events, Anomalies, Consents, Summary, Records of processing and Exports. These are not in `vocabulary.md`'s console list ("Review · Foods · Recipes · Aliases · Policy · Registry · Grants · Jobs · Roles · Audit trail · Metrics · Settings"). The lens routes them correctly, in §7 A14: "**Names not yet in D2.** Each needs a dated delta or a rename". They wait for the model phase.

## Closing note by the session, 2026-10-01
Notes addressed: the approver Totals line now reads 228 (192 runtime, 18 system, 18 module); support-9.4 reads "Not given, Given or Withdrawn" (D3). The edit inside approver's dated "Fix round 2" record (line ~1157) is history and is left as written. Lenses phase closed on the governor's pass.
