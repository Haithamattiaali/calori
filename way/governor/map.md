# Governor — map

Checked 2026-10-01 05:01Z. The map phase ends at commit `06a40d1` ("way: delta D1 — size becomes platform (10 workflows)"). HEAD is now `3492423` ("way: lenses — shared lens brief"), which adds only `way/personas/_lens-brief.md`. `git diff --stat 06a40d1 HEAD -- way/blueprint.md way/research way/map-first.md` is empty, so the records checked are the same at both commits.

Row checked: "operation research with opened sources; the final map cites it; every persona named, or its absence argued".

Records read: `way/blueprint.md`, `way/map-first.md`, the six `way/research/r1-*.md` files, `way/journey.md`, `way/ledger.md`, `way/lessons.md`, `way/brief/frd-v1.0.md` (FR-081), and git. Only `way/` and git were read. No source was fetched again.

**Verdict: 6 gaps.**

## Gaps

1. **§0 line 2 contradicts itself after delta D1.**
   Record: blueprint line 10 reads "**platform** (from product by delta D1, §3) — 10 workflows; 5 human personas (…) + system actors; ≈8 workflows; 3 external integrations …". Its source column still says "inferred from the brief". `git show 06a40d1` shows that D1 put "10 workflows" in front of the old text and left "≈8 workflows" in place.
   Missing: delete "≈8 workflows" and add "delta D1 (§3)" to the source column. §0 says every later phase reads it, so it must give one workflow count.

2. **The range "P1–P5" includes P5, which refuter B marked refuted. Three places cite it without "as corrected".**
   Record: the three places are:
   - line 39: "a platform admin rolls models forward and back (P1–P5)";
   - line 48, the Platform admin "from" cell: "brief §16.4, FR-080; P1–P5";
   - line 53, System actors: "Gemini 3.8 Flash for images and recipes, 3.5 Flash-Lite candidate for text intent; P1–P5".

   r1-refute-b has "| P5 | **refuted (in part)** |" and Dropped item 1: "P5 / platform implication 2. '3.8 Flash and 3.5 Flash-Lite *drop* `temperature`, `top_p` and `top_k`.'" Line 63 does say "P5 as corrected in r1-refute-b", but these three places do not.
   Missing: at these three places, cite "P1–P4" (all four stand) or write "P1–P5, P5 as corrected in r1-refute-b". The text at these places does not depend on the refuted part, so only the citation needs to change.

3. **The range "F12–F19" includes F18, which refuter A marked doubtful. Four places cite it without "as corrected".**
   Record: the four places are:
   - line 39: "Tier B approver-built recipe records for regional dishes, F12–F19";
   - line 46, the Nutrition approver "from" cell: "F12–F19";
   - line 72: "cross-check NNI/SFDA, never copy (F12–F19)";
   - line 120: "Licences from SFDA and NNI (F12–F19)".

   r1-refute-a has "| F18 | doubtful | nabdh PLAN.md (opened) says 'no public API yet …' … SFDA's 2026-08-30 news says the database makes 'data accessible for developers to build digital tools'. Whether there is an API is unknown." Its Dropped list says: "F18 (doubtful): it rests on a third-party plan with a wrong launch date. SFDA hints at developer access."
   Missing: cite "F12–F17, F19", or add "F18 as corrected in r1-refute-a". The open question on line 120 can also take the refuter's corrected point: ask SFDA about developer access as well as licences (r1-refute-a, high-impact assumption 5).

4. **F32 is cited for a claim that its refutation record contradicts.**
   Record: line 39 says "the big Western trackers list no Arabic (C29, C36; MFP and Lose It! per r1-refute-a) — while local apps such as Loqma and Kam Calorie (F32, F34) and Cal AI (C41) do." r1-refute-a has "| F32 | stands, part refuted | Kam App Store (v1.1.5): voice 'in Arabic or English'; … App Store localisation: 'English' only." The sentence compares App Store language lists; C29 and C16 in r1-refute-a are both language-list checks. Loqma lists Arabic (F34: "13 languages, full RTL Arabic") and so does Cal AI (C41: "English, Arabic, …"). Kam does not.
   Missing: drop Kam Calorie from the apps that list Arabic. Or say only what the record supports: Kam accepts Arabic voice input, but its App Store page lists only English (F32 as corrected in r1-refute-a).

5. **The hidden-persona hunt does not name who approves a support agent's diary access.**
   Record: the map requires an approval in three places:
   - line 47, Support agent: "diary access only by a just-in-time, time-boxed, approved, audited grant";
   - line 75: "Support → eater account | request just-in-time diary access | Access grant | approval + time box + audit | issue resolved";
   - line 104, WF-10 done-when: "a support grant expires and its use shows in the auditor's trail".

   The persona table (lines 43–53) lists the eater, nutrition approver, support agent, platform admin, auditor, household member, owner who pays, minor and system actors. None of them approves a grant. The Auditor only reads, and the Nutrition approver approves food records, recipes, aliases and policy, not access. The brief says only "Private diary access requires just-in-time authorization and an audit trail" (FR-081, frd line 384).
   Missing: one of two fixes.
   - Name the approver (for example a support lead, the platform admin, or the eater through in-app consent), add that approval to the interaction table, and add an approval step to WF-10's done-when.
   - Or argue that no approver exists (for example, the grant runs only inside its time box and is audited afterwards) and remove "approved"/"approval" from lines 47 and 75.

6. **Two research changes cite a refutation file, not a finding id.**
   Record: line 35 says "Research changes are cited by finding id". Two places break this:
   - Line 39 says "MFP and Lose It! per r1-refute-a". The finding is C16: "| C16 | stands, part refuted | … MFP … **no Arabic**. Lose It! … **no Arabic**, so the Lose It! Arabic excerpt is refuted".
   - The open question on line 122 says "Arabic voice: the Gemini transcription model lists only Egyptian Arabic (ar-EG), preview, `global` only; Gulf Arabic quality unverified; Siri phrases in Arabic unverified (r1-refute-b)". The findings are P15 ("Its Arabic entry is **only 'Arabic (Egypt) ar-EG'** … `gemini-3.5-transcribe-preview`, on `global` only") and P22 ("Whether App Shortcut phrases work in Arabic: assumption (unverified)").

   Missing: cite "C16 as corrected in r1-refute-a" on line 39 and "P15; P22 (assumption)" on line 122.

## Line by line

### (1) Research cycle 1 with sources opened in this run: pass

These are the files and the commits that added them:

| file | commit |
|---|---|
| r1-food-sources.md | `d43ce99` |
| r1-competitors.md | `0a5a036` |
| r1-platforms.md | `6a53900` |
| r1-rules-trends.md | `c71449e` |
| r1-refute-b.md | `ac35a5c` |
| r1-refute-a.md | `a253a4b` |

The researchers worked on a narrow network. r1-competitors.md says: "The only hosts I could open were **developer.apple.com** and **github.com**". Because of that, most competitor and food findings are still labelled `assumption` in their own files: r1-competitors.md has 55 lines labelled `assumption` and 9 labelled `opened`, and r1-food-sources.md has 25 lines labelled `assumption`.

The network was widened partway through the run. lessons.md records: "Network access was widened by the owner mid-run; the refuters were told at once so the assumption findings are re-opened instead of carried". The refuters then opened the sources:
- r1-refute-a: "Each quote below is page text I fetched myself in this run". Totals: "stands 78 · refuted 3 · doubtful 8 · assumption 4" (93 findings).
- r1-refute-b: "Stands 81 (P 39, R 42), refuted 4 (P5, P6, P11, R34), doubtful 0, assumption 0".

The four findings still labelled assumption (C46, C57, F21, F30) are not cited by the map.

Spot-check of five findings (link, date, quote):

| id | research file | refuter | result |
|---|---|---|---|
| C22 | https://support.cronometer.com/hc/en-us/articles/360018510311-Create-Custom-Recipe · "(undated)", accessed 2026-10-01 · a search-excerpt quote labelled `assumption`: "After cooking, weigh your recipe and click the green text 'Set Cooked Recipe Weight'…" | "Cronometer help via Zendesk API, article 360018510311 (2026-09-25): … ''Set Cooked Recipe Weight' to enter in the actual weight of your recipe'" (stands, now opened) | pass |
| F27 | https://github.com/danieljchandler/arabic-buddy/blob/main/docs/curriculum-research/gulf.md · 2026-10-01 · "لبن *laban* (buttermilk), حليب" (the Egyptian sense is `assumption`) | "arabic-buddy gulf.md: 'لبن *laban* (buttermilk)'. en.wiktionary لبن: Egyptian Arabic '# [[milk]]'; Gulf Arabic 'leban, coagulated sour milk; yogurt drink'" (stands, now opened) | pass |
| F32 | https://www.kamcalorie.app/en and the App Store page (id6748948785). It gives no date of its own beyond the file's "Run date: 2026-10-01", and no quote, only a bracketed search paraphrase | "Kam App Store (v1.1.5): voice 'in Arabic or English'; 'Saudi & Gulf: kabsa, mandi, …'; … App Store localisation: 'English' only." Checked 2026-10-01 | pass, but the quote exists only in r1-refute-a (see gap 4 for how the map uses it) |
| P29 | https://developer.apple.com/documentation/healthkit/hkquantitytypeidentifier/dietaryenergyconsumed · 2026-10-01 · "A quantity sample type that measures the amount of energy consumed." (`opened`) | "HKCorrelation: 'HealthKit uses correlations to represent both blood pressure and food … Correlations are immutable'" (stands) | pass |
| R32 | https://raw.githubusercontent.com/oruburos/ExpPsyWL/HEAD/nhsWP/controllers/appController.js · 2026-10-01 · "Calorie goals must be at least 1000 calories/day…" (`opened (mirror)`) | "Live https://www.niddk.nih.gov/bwp loads `controllers/appController.js`: 'Calorie goals must be at least 1000 calories/day …'" (stands, now opened, official) | pass |

The refuting pass is recorded. `r1-refute-a.md` covers C1–C58 and F1–F35, and `r1-refute-b.md` covers P1–P42 and R1–R43. Each file has verdict definitions, a verdict and evidence for every finding, a Dropped list and a list of high-impact assumptions.

### (2) The final map in blueprint §1: pass except gap 5

| part | record | result |
|---|---|---|
| Operation paragraph | §1.1, line 39 ("An **adult** (18+; R13, R16, R22) who eats home-cooked …") | present |
| Admin | line 48, "**Platform admin** \| rolls model IDs, prompt and schema versions …" | named |
| Supervisor / approver | line 46, "**Nutrition approver** \| qualified reviewer: approves reference food records …". The approver of support grants is missing | gap 5 |
| Auditor | line 49, "**Auditor** (hidden, found by the first map) \| reads only …" | named |
| System actor | line 53, "System actors \| AI analyzer … · nutrition resolver … · planner … · HealthKit … · outbox sync · retention and deletion jobs · model registry" | named |
| Owner who pays | line 51, "Owner who pays (hidden) \| a paid AI tier through in-app purchase \| — \| brief §23.3 "proposed"; R9 — drop list for v1, with per-user quotas instead" | absence argued |
| Other hidden personas | Household member (line 50, drop list, brief priority P1), Minor (line 52, "R16 forbids Gemini for under-18 services; R13 rates calorie tracking 9+ → an age gate keeps them out") | argued |
| Interaction table with value events | lines 57–76: 18 rows, each with a value event, from "consents recorded" to "trail reviewed" | present |
| Workflows and vocabulary | lines 80–91: WF-1 to WF-10, and "Vocabulary — one name per thing …" with the tab names | present |
| Done-when per workflow | lines 95–104: one for each of WF-1 to WF-10 | present |
| What-else pass | lines 108–113: user settings, user rules, policy, registry, reference, fixed vocabulary | present |
| Open questions | lines 117–124: 6 rows, each with an owner and a when | present |

### (3) Citations checked against the refutation files: 4 gaps (2, 3, 4, 6)

The ids cited in §1, with ranges expanded:
C4, C5, C6, C7, C9, C12, C21, C22, C23, C27, C29, C32, C34, C35, C36, C41, C45, C54, C56 · F1–F5, F8, F11, F12–F19, F27, F32, F34 · P1–P5, P11, P22–P24, P29, P30 · R1–R4, R6, R7, R9, R13, R16, R18–R26, R28, R29, R31–R33, R35–R38, R40, R41.

("C1" on line 113 is the customization depth from §0, not a finding.)

Findings the refuters count as refuted or doubtful, where the map cites them:

| id | refuter verdict | map citation | result |
|---|---|---|---|
| C6 | doubtful | "C6 as corrected in r1-refute-a" (line 39) | pass |
| C27 | doubtful | "C27 as corrected" (line 39) | pass |
| C54 | refuted | "C54 as corrected in r1-refute-a" (line 39) | pass |
| P5 | refuted (in part) | "P5 as corrected in r1-refute-b" (line 63) passes; the range "P1–P5" on lines 39, 48 and 53 does not | gap 2 |
| P11 | refuted (in part) | "P11 as corrected in r1-refute-b" (line 119) | pass |
| F18 | doubtful | the range "F12–F19" on lines 39, 46, 72 and 120 | gap 3 |

The map does not cite these refuted or doubtful findings: C8, C26, C39, C47, C50, F10, F31, P6 and R34. The 1,200 kcal floor is not credited to R34 or to any source: "the 1,200 kcal floor is our own product policy — no source names it a hard floor, r1-refute-b". That matches r1-refute-b Dropped item 12.

Findings that stand overall but have a refuted, doubtful or dropped part, and that the map cites:
- F32: gap 4.
- P2, "retirement date refuted": the map uses no retirement date. Pass.
- R29, "decree type doubtful": line 121 says "window closes ~1 Nov 2026", which matches Dropped item 11 ("grace period strictly to 1 Nov 2026"). Pass.
- C4, figures dropped: line 39 says "its rating figures unverified". Pass.
- P23 (the IntentModes date) and R18 (Cloud Tasks): the map uses neither part. Pass.

Every other cited id is `stands` or `stands (now opened)` in r1-refute-a or r1-refute-b.

### (4) Size line: delta present, §0 update incomplete (gap 1)

D1 (§3, line 132): "**2026-10-01 · delta D1 · size line crossed at the final map.** The final map has 10 workflows (floor: product ≤ 8), 5 human personas and 3 external integrations, so §0 line 2 becomes **platform**."

The counts check out:
- 10 workflows: WF-1 to WF-10.
- 5 human personas in §2: eater, nutrition approver, support agent, platform admin, auditor.
- 3 external integrations: Gemini, USDA and HealthKit.

§0 line 2 now starts "**platform** (from product by delta D1, §3)". What is left is the old "≈8 workflows" (gap 1).

### (5) Everything committed: pass

`git status` reports: "On branch claude/magical-cerf-axi3k1 / Your branch is up to date with 'origin/claude/magical-cerf-axi3k1'. / nothing to commit, working tree clean". `git ls-remote origin` returns `349242322a5e…  refs/heads/claude/magical-cerf-axi3k1`, which is the same commit as `git rev-parse HEAD`.

The map-phase commits are `caa1fb8`, `d43ce99`, `0a5a036`, `6a53900`, `c71449e`, `d3942d3`, `ac35a5c`, `a253a4b` and `06a40d1`. This governor file is not committed.

## Notes (not counted)

- **Assumption label.** Line 35 says "A finding marked `assumption` in its file is cited with "(assumption)"". No finding in §1 carries "(assumption)"; the only match is that sentence itself. Yet many cited findings are still labelled `assumption` in their own files, for example C22, C35, F19 and F34. In practice the map follows the refuters' verdicts ("stands (now opened)"), which is correct. The sentence should say so: "… unless a refuter re-opened it".
- **"P1" in the Household row.** Line 50 has "**P1** (brief §1.2)". It means the brief's priority P1, but line 35 says "P = platforms", so it reads as finding P1 (gemini-3.8-flash). Write "priority P1".
- **C9.** Line 39 says "crowd-sourced numbers are distrusted (C9)". r1-refute-a dropped C9's "largely user-submitted, not systematically verified". What the refuter confirmed is the check mark and "report the food". The map's claim rests on the dropped part.
- **C7.** The free-vs-paid open question (line 123) says "the loudest complaints are paywalled basics". r1-refute-a dropped C7's "most-cited reason people leave MFP". C21, which stands, still supports complaints about Lose It!'s paywall.
- **R40.** Line 39 says "estimates anchored to a composition database err far less". r1-refute-b notes: "This was not an ablation of the same MLLM with and without the database." "Err far less" is stronger than that record supports.
- **WF-3 tap count.** The WF-3 done-when changed from "≤3 taps" in the first map to "≤2 taps" (line 97), with no citation. It reads as a design target rather than a research finding.
- **When the size line was crossed.** D1 says the size line was "crossed at the final map". But the first map already had 10 workflows (`git show caa1fb8:way/map-first.md` lists WF-1 to WF-10) while §0 line 2 still said "≈8 workflows". The delta is still dated and recorded in §3.
- **§0 "What the profile switches on and off".** Line 22 does not list the platform obligations that D1 adds: data policy, supply chain, migrations, trust, and one backup restore. They appear only in §3.
- **NNI rights.** r1-refute-a, high-impact assumption 4 (NNI holds the rights): "These may enter the records only with the label `assumption`." Line 72, "never copy (F12–F19)", rests on that assumption without the label. It errs on the safe side, and line 120 keeps the licence question open.

## Fixes by the session, 2026-10-01
1. §0 line 2: "≈8 workflows" removed; source now "inferred from the brief as product; changed to platform by delta D1".
2. "P1–P5" → "P1–P4, P5 as corrected in r1-refute-b" (3 places).
3. "F12–F19" → "F12–F17, F19" (F18 doubtful, dropped) (4 places).
4. Kam Calorie: "takes Arabic voice input though its store page lists English only (F32 as corrected)".
5. The just-in-time Grant is approved by the eater in the app (persona table + two interaction rows: request, then read within the Grant).
6. Citations by finding id: C16 for MFP/Lose It! Arabic; P15, P22 for the Arabic voice question.
