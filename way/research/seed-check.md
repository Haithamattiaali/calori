# Seed check: an independent recount of `way/seed.md` (2026-10-01)

This is an independent check of the shared fixture set. The checker did not write it. It reads `way/seed.md`, `way/join.md` (J52–J64 fixtures and J70–J88 Units), `way/events.md`, `way/vocabulary.md` and `way/brief/frd-v1.0.md` (§6.1, §9.1, §10, §11, §13.1 and AT-01…AT-32).

**Method.** The script `/tmp/claude-0/seedcheck.py` recomputes every value with `fractions.Fraction`. It starts from the Food basis values the seed states (§8.1, §8.2, §8.5) and works through:
- every Unit, Composite, Recipe, Tier B record and per-gram value;
- 4/4/9 energy and shares, displayed by largest remainder;
- Mifflin–St Jeor values, maintenance and Targets;
- every planner example, with each count set enumerated;
- Day totals, remaining amounts and prices.

Times are recomputed with `zoneinfo` (IANA tzdata). The Ramadan date comes from `hijridate` (Umm al-Qura). The script also:
- parses the §11 table and checks numbering, order, event names against `events.md`, roles at the time of each act, Grant windows, the 72 h rule, the derived counts and every Consent count;
- cross-checks the §9 Grant table against the §11 events;
- checks every `§n` and `Jn` cross-reference;
- checks ids, email domains and the FDC hummus row. That row was compared with a cached copy of the FDC record at `/tmp/claude-0/fdc2.json`. No network call was made in this run.

The script writes the table below.

**Result: 884 values checked. 868 are ok and 16 are DEFECT.** The core nutrition arithmetic is clean: every Unit, Recipe, Target, Day total, share and price recomputes exactly. Several groups of checks also hold in full:
- the §11 trail: 266 events, gap-free and ordered, every name in `events.md`, every count;
- every Grant window;
- every planner best answer.

The defects are about time and sequence, internal consistency, coverage and one join figure.

---

## Defects and exact corrections

| # | where | seed says | recomputed / found | exact correction |
|---|---|---|---|---|
| D1 | §10.4 Faisal row (line 545), AT-22 | import 2026-10-01T04:30Z (07:30 Riyadh) holds "the Watch walk 07:00–07:45 Riyadh" and the copy 07:01–07:44 | The import is at 07:30, 15 min **before** the walk ends, so a finished workout cannot be in it. His Breakfast at 07:30 also falls inside the walk. | Read "the Watch walk **06:00–06:45** Riyadh (210 kcal), the running app's copy **06:01–06:44** (205 kcal)". The import stays at 04:30Z, which E1 and support-9.19 share. Lens lines to read through a join line: e578 eater-7.9 (06:00–06:45) and eater-7.11 (manual walk "starting 06:00"; «هل هو نفس المشي 6:00–6:45 من Apple Health؟»). Alternative: import at 2026-10-01T05:00Z (08:00 Riyadh). |
| D2 | §6.1 E1 devices (line 246) vs §10.3 `cmd_7a1e` (line 538) | app 1.0.2 iPhone "last sync 2026-09-29T20:40Z"; its correction `cmd_7a1e` is a **Conflict** `STALE_REVISION` at 2026-09-30T19:15Z | The server decides `STALE_REVISION` when the command arrives. That means the 1.0.2 iPhone reached the server 22 h 35 min after its stated last sync. | §6.1: "iPhone app 1.0.2 (last sync **2026-09-30T19:15Z**)". support-9.4's device line then reads "last sync 2026-09-30 20:15 your time (19:15 UTC) · 22:15 eater's time". Alternative: date `cmd_7a1e` 2026-09-29T20:40Z. |
| D3 | §10.1 `job_exp_4402` (line 516) vs §12.3 | "Entries 212" at the export of 2026-09-15T12:00Z; §12.3 says "212 in all at the export" while E5d runs "2026-09-15 → 09-28" | 12:00Z on 15 Sep is 15:00 Riyadh. E1's boundary is 04:00, so Day 2026-09-15 already holds its 07:00 (cheese bites, tea glass) and 13:00 (kabsa, chicken) Entries. The count is 42 × 5 + 2 + **4 = 216**. | §10.1: "Entries **216**", with a join line for auditor-9.14 ("Entries 212" → 216). Alternative that keeps 212: §12.3 "2026-09-15 Unlogged · 2026-09-16 → 09-28 E5d". |
| D4 | §5.3 Sam's weights (line 234) vs §10.5 (line 552) | §5.3: "83.5 (10-01, Apple Health)"; §10.5: "after 2026-09-24 no new body mass (e578 eater-8.22)" | The seed contradicts itself on one timeline. The contradiction is inherited from e578 eater-8.19 vs 8.22. | §10.5: "body-mass samples of §5.3; the 2026-10-01 sample is written at 2026-10-01T06:00Z". §2 "Stories that start elsewhere": add "e578 eater-8.22 (Sam's line) · **2026-09-30T12:00:00Z** · no Apple Health weight since 24 Sept". Alternative: §5.3 "83.5 (10-01, by hand)", with eater-8.19's source label read "by hand". |
| D5 | §2 lines 54 and 73 (vocabulary) | "every 30 Sep Day complete"; "the week 2026-09-20 → 26 is complete" | **Complete** is a Day state, the eater's own mark (D2). Mona's, Sam's and E1's 30 Sep Days carry no Complete mark, and the week holds unmarked and Unlogged Days. | Line 54: "every 30 Sep Day past its boundary". Line 73: "the week 2026-09-20 → 26 has ended". |
| D6 | §11 event 145 vs §3 permissions (line 80) and J40 | `staff_ali` (Platform admin) `wording.published` c-ai-4 | The fixed permission list in §3 and J40 has no permission that publishes wording, so the seeded act has no permission behind it. `events.md` §2.7 names the path `POST /v1/admin/wording` without a holder. | Add "**Publish wording**" to the §3 list and to J40's list. Give it to Platform admin (consent texts, `grant-req-1`) and to Nutrition approver (`guidance-1`, approver-10.53). |
| D7 | §0 item 4 (line 14) | "USDA FoodData Central (serves §8.1 and §8.7)" | The seed has no §8.7. Its §8 ends at §8.6. | "(serves §8.1, with releases 15.4 and 15.5, and the import of §10.7)". |
| D8 | §7.6 eater-5.14 row (line 373) | "(the lens's own exact-fraction check holds)". e578 5.14 `/m`: "of the 12 count sets that meet every limit" | 12 sets meet only the ceiling, carbohydrate and Available limits. The aim band 360–440 is a limit: the lens's own limits list marks it "met", and seed eater-5.21 applies its ±10 % band as a limit. With the band, **3** sets meet every limit: 4+2 (397.6), 1+3 (384.4) and 2+3 (426.8). The best (4+2) and the next nearest (1+3, 15.6 away) hold. | §7.6 row: "4 rice + 2 chicken = 397.6 kcal, 28.17 %; 3 count sets meet every limit (4+2, 1+3, 2+3); next nearest 1 rice + 3 chicken (384.4)". Add to J55's Supersedes: e578 eater-5.14 `/m` "of the 12 count sets" (read "of the 3 count sets"). |
| D9 | §7.6 eater-5.17 row (line 369) and J55 | J55 replaces 5.17's count and calorie rows. The lens `/m` "Calorie aim about **809.7** with tolerance 0 … the counts are 6 / 4 / 1 / 8 / 1, the only answer with zero deviation" is not replaced and not restated. | With the seed's Units, 6/4/1/8/1 totals **831.7**, which is 22 kcal from 809.7. 53 other count sets total exactly 809.7, so the tolerance-0 test cannot return 6/4/1/8/1. With aim 831.7, 6/4/1/8/1 is the unique zero-deviation answer (recomputed). | §7.6 row: add "Calorie aim about 831.7 with tolerance 0 → 6/4/1/8/1, the only zero-deviation answer". J55 Supersedes: e578 eater-5.17 `/m` "809.7" (read 831.7). |
| D10 | `join.md` J104 (not the seed; Target arithmetic) | "1,870 × 2,134.8 ÷ 2,334.8 = **1,709.78**…, shown 1,710" | = 9,980,190 / 5,837 = **1,709.8149…**. "Shown 1,710" and "−19.9 %" hold. | J104: "= 1,709.81…". The same figure in e19 eater-1.40 `/m` and in J104's Supersedes line for e578 eater-7.18 reads 1,709.81. |
| D11 | §8.6 (line 469) and §8.1 Date (generic) | no flag; Energy mismatch count 4 | Per 100 g, the basis the seed uses for F-08 and F-09: 4/4/9 = 313.31 vs 282. The gap is 31.31 kcal, **11.10 %**, which meets Policy v1 (> 10 % and > 10 kcal). The seed's own "never flagged" list includes Tier A Falafel, so approved Tier A rows are in scope. | Recommended: replace line 469's first sentence with "Never flagged: Biscuits, plain (500 vs 494) and White cheese (62.5 vs 64.0 per serving) are under the threshold; Tier A rows are not cross-checked, because FDC derives their energy with food-specific factors". J77 needs the same line. Alternative: open **F-10** (Date (generic): 282 vs 313.31, gap 31.31 kcal, 11.1 %). That makes Energy mismatch 5 and open flags 10. |
| D12 | §8.6 and §8.1 Cumin, ground | no flag | 4/4/9 = 446 vs 400. The gap is 46 kcal, **11.5 %**, which meets Policy v1. | As D11. With the alternative, also open **F-11** (Cumin, ground: 400 vs 446, gap 46 kcal, 11.5 %). Counts become Energy mismatch 6, open flags 11, and the approver default clock (§2) reads "11 open flags". Under Policy v2 (12 %) neither row would open. |
| D13 | §13 B1 · B2 (line 933) | synthetic events "2026-07-01 → 2026-09-30, actors from §3" | These start 31 days before the deployment (trail start 2026-08-01T06:00:00Z, J58). The §3 actors hold no role before events 1–6. The period was kept from the auditor lens, whose deployment J58 moved. | "2026-08-01T06:00:00Z → 2026-09-30". |
| D14 | FRD AT-01 coverage | none | AT-01's fixture values (7 pieces, 71.7 g → 10.242857 g, shown 10.24 g, count 7) are not in the seed. No item in `join.md` reassigns them. e24 eater-2.13 types them in its story. The arithmetic holds: 717/70; the single weights 9.8 · 10.1 · 10.6 · 10.0 · 10.4 · 10.3 · 10.5 sum to 71.7. | Add to §5.3 a line "**Typed in a story, not stored in the seed**". AT-01 (eater-2.13): Sam's small biscuit, 7 pieces, 71.7 g after tare, single weights as listed, mean 717/70 g = 10.242857… g, shown 10.24 g. Name its Food, for example Biscuits, plain (synthetic brand). |
| D15 | FRD AT-03 coverage | none | AT-03's 15.1 + 14.4 + 8.6 = 38.1 g is not in the seed and not reassigned. e24 eater-2.17 resolves "Rice, cooked", "Peas with sauce" and "Beef, cooked", and **none of these Foods is in §8**. | Same §5.3 line: AT-03 (eater-2.17), Mona's mixed peas spoon, Rice, cooked 15.1 g + Peas with sauce 14.4 g + Beef, cooked 8.6 g = 38.1 g. Then add three synthetic rows to §8.1 for those Foods. Alternatively map Rice, cooked → "Rice, cooked, with fat (generic)" and Beef, cooked → "Beef, stewed", and add only "Peas with sauce". |
| D16 | FRD AT-09 coverage | none | AT-09's 46/32/24 → 102 → 45.10 / 31.37 / 23.53 is not in the seed and not reassigned. e19 eater-1.35 types it. The arithmetic holds: largest remainder at 2 dp, sum 100.00. | Same §5.3 line: AT-09 (eater-1.35), fat 46 %, carbohydrate 32 %, protein 24 % typed in Onboarding · Macros → total 102 %, alternative 45.10 / 31.37 / 23.53. |

The other ATs that name fixture values are reproduced exactly:
- AT-02, AT-04, AT-05, AT-06, AT-08, AT-10, AT-12, AT-17, AT-18, AT-19, AT-20, AT-25, AT-26 and the FRD §11.3, §13.1, §5.1 and §2.6 figures, all recomputed.
- AT-22: two feeds plus a re-send; eater-7.11 adds the manual entry.
- AT-23 and AT-24: the 200 kcal planned exercise and Sam's 1,200 kcal Day are in the seed. The 200 kcal import and the 175 kcal cardio are the stories' own acts.

## Notes (checked; not counted as defects)

- **Event 224** (`staff_mona` idle sign-out at 15:25). Her last act that wrote an event was 15:05, so a 15-minute idle sign-out would land at 15:20. The 15:25 time needs console activity that wrote no event until 15:10. Moving the event to 15:20 would remove the question.
- **Hala's "16 local commands"** (3 Unit saves + 1 Template save + 12 consume). This assumes one consume command per Entry. J120 allows multi-item commands, and the 08:30 Template log makes 2 Entries.
- **E1's relay address** `r7k2q9x4@privaterelay.appleid.com` has a random local part on Apple's real relay domain. It identifies no person. Every other email is on `example.test` or `example.com`. No owner identifier appears anywhere in the seed.
- **Glass of milk tea vs glass of milk.** The tea's 50 ml of milk carries the reference Milk, whole badge (measured). The glass of milk uses Mona's carton (label-verified). The numbers are equal. The seed could say which record the tea uses.
- **Event 233** is "Refused · NOT_FOUND". The envelope in `events.md` also has a `not_found` outcome. Both readings fit the catalogue, so pick one.
- **eater-5.21's "only cheese bites fit at 39.2 %"** is true with the aim band 270–330 as a limit, which is how D8 reads it. Without the band, sets such as 46 cheese bites + 1 egg bite (39.1997 %) also pass the carbohydrate limit.
- **Ramadan.** 1 Ramadan 1448 is 2027-02-08 (Umm al-Qura) and Eid is 2027-03-09. Turning "Ramadan days" off at 2027-03-10 14:00 Riyadh is the eater's own act. The Day assignment under a 12:00 boundary holds for both seeded Ramadan Days.
- **`job_del_2205` "4 days left".** At 2026-10-01T09:00Z the remaining time is 3 d 23 h, shown rounded up.

---

## Every checked value

The table below is generated by `/tmp/claude-0/seedcheck.py`. "seed says" quotes the seed, or the lens line the seed vouches for. "recomputed" is exact: a fraction is shown as `a/b (≈ decimal)`. "(shown)" means the seed's displayed value is compared with the exact value rounded to the same places.

| # | area | value checked | seed says | recomputed | result |
|---|---|---|---|---|---|
| 1 | §7.6 Recipe | Talbina · Barley flour 40 g · kcal | 140 | 140 | ok |
| 2 | §7.6 Recipe | Talbina · Barley flour 40 g · P | 3.2 | 3.2 | ok |
| 3 | §7.6 Recipe | Talbina · Barley flour 40 g · C | 30 | 30 | ok |
| 4 | §7.6 Recipe | Talbina · Barley flour 40 g · F | 0.8 | 0.8 | ok |
| 5 | §7.6 Recipe | Talbina · Milk, whole 500 g · kcal | 300 | 300 | ok |
| 6 | §7.6 Recipe | Talbina · Milk, whole 500 g · P | 16 | 16 | ok |
| 7 | §7.6 Recipe | Talbina · Milk, whole 500 g · C | 23 | 23 | ok |
| 8 | §7.6 Recipe | Talbina · Milk, whole 500 g · F | 16 | 16 | ok |
| 9 | §7.6 Recipe | Talbina · Sugar 10 g · kcal | 40 | 40 | ok |
| 10 | §7.6 Recipe | Talbina · Sugar 10 g · P | 0 | 0 | ok |
| 11 | §7.6 Recipe | Talbina · Sugar 10 g · C | 10 | 10 | ok |
| 12 | §7.6 Recipe | Talbina · Sugar 10 g · F | 0 | 0 | ok |
| 13 | §7.6 Recipe | Talbina v1 totals · kcal | 480 | 480 | ok |
| 14 | §7.6 Recipe | Talbina v1 totals · P | 19.2 | 19.2 | ok |
| 15 | §7.6 Recipe | Talbina v1 totals · C | 63 | 63 | ok |
| 16 | §7.6 Recipe | Talbina v1 totals · F | 16.8 | 16.8 | ok |
| 17 | §7.6 Recipe | Talbina 4/4/9 | 480 | 480 | ok |
| 18 | §7.6 Recipe | Talbina per 100 g (yield 384 g) · kcal | 125 | 125 | ok |
| 19 | §7.6 Recipe | Talbina per 100 g (yield 384 g) · P | 5 | 5 | ok |
| 20 | §7.6 Recipe | Talbina per 100 g (yield 384 g) · C | 16.40625 | 16.40625 | ok |
| 21 | §7.6 Recipe | Talbina per 100 g (yield 384 g) · F | 4.375 | 4.375 | ok |
| 22 | §7.6 Recipe | Talbina 16 g spoon kcal (AT-06) | 20 | 20 | ok |
| 23 | §7.6 Recipe | Talbina 15 spoons kcal (AT-06) | 300 | 300 | ok |
| 24 | §7.6 Recipe | Talbina 18 spoons kcal (AT-06, AT-26) | 360 | 360 | ok |
| 25 | §7.6 Recipe | foul v1 totals · kcal | 1200 | 1200 | ok |
| 26 | §7.6 Recipe | foul v1 totals · P | 80 | 80 | ok |
| 27 | §7.6 Recipe | foul v1 totals · C | 160 | 160 | ok |
| 28 | §7.6 Recipe | foul v1 totals · F | 28 | 28 | ok |
| 29 | §7.6 Recipe | foul per 100 g (yield 800 g) · kcal | 150 | 150 | ok |
| 30 | §7.6 Recipe | foul per 100 g (yield 800 g) · P | 10 | 10 | ok |
| 31 | §7.6 Recipe | foul per 100 g (yield 800 g) · C | 20 | 20 | ok |
| 32 | §7.6 Recipe | foul per 100 g (yield 800 g) · F | 3.5 | 3.5 | ok |
| 33 | §7.6 Recipe | molokhia · Rice, white, raw 500 g · kcal | 1760 | 1760 | ok |
| 34 | §7.6 Recipe | molokhia · Rice, white, raw 500 g · P | 35 | 35 | ok |
| 35 | §7.6 Recipe | molokhia · Rice, white, raw 500 g · C | 390 | 390 | ok |
| 36 | §7.6 Recipe | molokhia · Rice, white, raw 500 g · F | 4 | 4 | ok |
| 37 | §7.6 Recipe | molokhia · Jute leaves 500 g · kcal | 200 | 200 | ok |
| 38 | §7.6 Recipe | molokhia · Jute leaves 500 g · P | 23 | 23 | ok |
| 39 | §7.6 Recipe | molokhia · Jute leaves 500 g · C | 29 | 29 | ok |
| 40 | §7.6 Recipe | molokhia · Jute leaves 500 g · F | 1.5 | 1.5 | ok |
| 41 | §7.6 Recipe | molokhia · Chicken broth 1,000 g · kcal | 100 | 100 | ok |
| 42 | §7.6 Recipe | molokhia · Chicken broth 1,000 g · P | 15 | 15 | ok |
| 43 | §7.6 Recipe | molokhia · Chicken broth 1,000 g · C | 4 | 4 | ok |
| 44 | §7.6 Recipe | molokhia · Chicken broth 1,000 g · F | 3 | 3 | ok |
| 45 | §7.6 Recipe | molokhia · Ghee 30 g · kcal | 270 | 270 | ok |
| 46 | §7.6 Recipe | molokhia · Ghee 30 g · P | 0 | 0 | ok |
| 47 | §7.6 Recipe | molokhia · Ghee 30 g · C | 0 | 0 | ok |
| 48 | §7.6 Recipe | molokhia · Ghee 30 g · F | 30 | 30 | ok |
| 49 | §7.6 Recipe | molokhia · Garlic 20 g · kcal | 30 | 30 | ok |
| 50 | §7.6 Recipe | molokhia · Garlic 20 g · P | 1.28 | 1.28 | ok |
| 51 | §7.6 Recipe | molokhia · Garlic 20 g · C | 6.62 | 6.62 | ok |
| 52 | §7.6 Recipe | molokhia · Garlic 20 g · F | 0.1 | 0.1 | ok |
| 53 | §7.6 Recipe | molokhia · Onion 100 g · kcal | 40 | 40 | ok |
| 54 | §7.6 Recipe | molokhia · Onion 100 g · P | 1.1 | 1.1 | ok |
| 55 | §7.6 Recipe | molokhia · Onion 100 g · C | 9.3 | 9.3 | ok |
| 56 | §7.6 Recipe | molokhia · Onion 100 g · F | 0.1 | 0.1 | ok |
| 57 | §7.6 Recipe | molokhia with rice v1 totals · kcal | 2400 | 2400 | ok |
| 58 | §7.6 Recipe | molokhia with rice v1 totals · P | 75.38 | 75.38 | ok |
| 59 | §7.6 Recipe | molokhia with rice v1 totals · C | 438.92 | 438.92 | ok |
| 60 | §7.6 Recipe | molokhia with rice v1 totals · F | 38.7 | 38.7 | ok |
| 61 | §7.6 Recipe | molokhia per 100 g (yield 2,000 g) · kcal | 120 | 120 | ok |
| 62 | §7.6 Recipe | molokhia per 100 g (yield 2,000 g) · P | 3.769 | 3.769 | ok |
| 63 | §7.6 Recipe | molokhia per 100 g (yield 2,000 g) · C | 21.946 | 21.946 | ok |
| 64 | §7.6 Recipe | molokhia per 100 g (yield 2,000 g) · F | 1.935 | 1.935 | ok |
| 65 | §7.6 Recipe | kabsa rice · Rice, white, raw 700 g · kcal | 2464 | 2464 | ok |
| 66 | §7.6 Recipe | kabsa rice · Rice, white, raw 700 g · P | 49 | 49 | ok |
| 67 | §7.6 Recipe | kabsa rice · Rice, white, raw 700 g · C | 546 | 546 | ok |
| 68 | §7.6 Recipe | kabsa rice · Rice, white, raw 700 g · F | 5.6 | 5.6 | ok |
| 69 | §7.6 Recipe | kabsa rice · Ghee 90 g · kcal | 810 | 810 | ok |
| 70 | §7.6 Recipe | kabsa rice · Ghee 90 g · P | 0 | 0 | ok |
| 71 | §7.6 Recipe | kabsa rice · Ghee 90 g · C | 0 | 0 | ok |
| 72 | §7.6 Recipe | kabsa rice · Ghee 90 g · F | 90 | 90 | ok |
| 73 | §7.6 Recipe | kabsa rice · Onion 100 g · kcal | 40 | 40 | ok |
| 74 | §7.6 Recipe | kabsa rice · Onion 100 g · P | 1.1 | 1.1 | ok |
| 75 | §7.6 Recipe | kabsa rice · Onion 100 g · C | 9.3 | 9.3 | ok |
| 76 | §7.6 Recipe | kabsa rice · Onion 100 g · F | 0.1 | 0.1 | ok |
| 77 | §7.6 Recipe | kabsa rice · Chicken stock 1,000 g · kcal | 78 | 78 | ok |
| 78 | §7.6 Recipe | kabsa rice · Chicken stock 1,000 g · P | 21.9 | 21.9 | ok |
| 79 | §7.6 Recipe | kabsa rice · Chicken stock 1,000 g · C | 4.7 | 4.7 | ok |
| 80 | §7.6 Recipe | kabsa rice · Chicken stock 1,000 g · F | 0.3 | 0.3 | ok |
| 81 | §7.6 Recipe | kabsa rice v1 totals · kcal | 3392 | 3392 | ok |
| 82 | §7.6 Recipe | kabsa rice v1 totals · P | 72 | 72 | ok |
| 83 | §7.6 Recipe | kabsa rice v1 totals · C | 560 | 560 | ok |
| 84 | §7.6 Recipe | kabsa rice v1 totals · F | 96 | 96 | ok |
| 85 | §7.6 Recipe | kabsa rice 4/4/9 | 3392 | 3392 | ok |
| 86 | §7.6 Recipe | kabsa rice per 100 g (yield 2,000 g) · kcal | 169.6 | 169.6 | ok |
| 87 | §7.6 Recipe | kabsa rice per 100 g (yield 2,000 g) · P | 3.6 | 3.6 | ok |
| 88 | §7.6 Recipe | kabsa rice per 100 g (yield 2,000 g) · C | 28 | 28 | ok |
| 89 | §7.6 Recipe | kabsa rice per 100 g (yield 2,000 g) · F | 4.8 | 4.8 | ok |
| 90 | §7.6 Recipe | lentil soup · Lentils, dry 200 g · kcal | 704 | 704 | ok |
| 91 | §7.6 Recipe | lentil soup · Lentils, dry 200 g · P | 49.2 | 49.2 | ok |
| 92 | §7.6 Recipe | lentil soup · Lentils, dry 200 g · C | 126.8 | 126.8 | ok |
| 93 | §7.6 Recipe | lentil soup · Lentils, dry 200 g · F | 2.2 | 2.2 | ok |
| 94 | §7.6 Recipe | lentil soup · Onion 100 g · kcal | 40 | 40 | ok |
| 95 | §7.6 Recipe | lentil soup · Onion 100 g · P | 1.1 | 1.1 | ok |
| 96 | §7.6 Recipe | lentil soup · Onion 100 g · C | 9.3 | 9.3 | ok |
| 97 | §7.6 Recipe | lentil soup · Onion 100 g · F | 0.1 | 0.1 | ok |
| 98 | §7.6 Recipe | lentil soup · Water 1,500 g · kcal | 0 | 0 | ok |
| 99 | §7.6 Recipe | lentil soup · Water 1,500 g · P | 0 | 0 | ok |
| 100 | §7.6 Recipe | lentil soup · Water 1,500 g · C | 0 | 0 | ok |
| 101 | §7.6 Recipe | lentil soup · Water 1,500 g · F | 0 | 0 | ok |
| 102 | §7.6 Recipe | lentil soup · Olive oil 20 g · kcal | 180 | 180 | ok |
| 103 | §7.6 Recipe | lentil soup · Olive oil 20 g · P | 0 | 0 | ok |
| 104 | §7.6 Recipe | lentil soup · Olive oil 20 g · C | 0 | 0 | ok |
| 105 | §7.6 Recipe | lentil soup · Olive oil 20 g · F | 20 | 20 | ok |
| 106 | §7.6 Recipe | lentil soup v1 totals · kcal | 924 | 924 | ok |
| 107 | §7.6 Recipe | lentil soup v1 totals · P | 50.3 | 50.3 | ok |
| 108 | §7.6 Recipe | lentil soup v1 totals · C | 136.1 | 136.1 | ok |
| 109 | §7.6 Recipe | lentil soup v1 totals · F | 22.3 | 22.3 | ok |
| 110 | §7.6 Recipe | lentil soup per 100 g (yield 1,600 g) · kcal | 57.75 | 57.75 | ok |
| 111 | §7.6 Recipe | lentil soup per 100 g (yield 1,600 g) · P | 3.14375 | 3.14375 | ok |
| 112 | §7.6 Recipe | lentil soup per 100 g (yield 1,600 g) · C | 8.50625 | 8.50625 | ok |
| 113 | §7.6 Recipe | lentil soup per 100 g (yield 1,600 g) · F | 1.39375 | 1.39375 | ok |
| 114 | §8.3 rec_fm_eg | Fava beans, dry 500 g · kcal | 1678 | 1678 | ok |
| 115 | §8.3 rec_fm_eg | Fava beans, dry 500 g · P | 130.5 | 130.5 | ok |
| 116 | §8.3 rec_fm_eg | Fava beans, dry 500 g · C | 291.5 | 291.5 | ok |
| 117 | §8.3 rec_fm_eg | Fava beans, dry 500 g · F | 7.5 | 7.5 | ok |
| 118 | §8.3 rec_fm_eg | Water 2,000 g · kcal | 0 | 0 | ok |
| 119 | §8.3 rec_fm_eg | Water 2,000 g · P | 0 | 0 | ok |
| 120 | §8.3 rec_fm_eg | Water 2,000 g · C | 0 | 0 | ok |
| 121 | §8.3 rec_fm_eg | Water 2,000 g · F | 0 | 0 | ok |
| 122 | §8.3 rec_fm_eg | Olive oil 30 g · kcal | 270 | 270 | ok |
| 123 | §8.3 rec_fm_eg | Olive oil 30 g · P | 0 | 0 | ok |
| 124 | §8.3 rec_fm_eg | Olive oil 30 g · C | 0 | 0 | ok |
| 125 | §8.3 rec_fm_eg | Olive oil 30 g · F | 30 | 30 | ok |
| 126 | §8.3 rec_fm_eg | Cumin, ground 3 g · kcal | 12 | 12 | ok |
| 127 | §8.3 rec_fm_eg | Cumin, ground 3 g · P | 0.54 | 0.54 | ok |
| 128 | §8.3 rec_fm_eg | Cumin, ground 3 g · C | 1.32 | 1.32 | ok |
| 129 | §8.3 rec_fm_eg | Cumin, ground 3 g · F | 0.66 | 0.66 | ok |
| 130 | §8.3 rec_fm_eg | Salt 5 g · kcal | 0 | 0 | ok |
| 131 | §8.3 rec_fm_eg | Salt 5 g · P | 0 | 0 | ok |
| 132 | §8.3 rec_fm_eg | Salt 5 g · C | 0 | 0 | ok |
| 133 | §8.3 rec_fm_eg | Salt 5 g · F | 0 | 0 | ok |
| 134 | §8.3 rec_fm_eg | totals · kcal | 1960 | 1960 | ok |
| 135 | §8.3 rec_fm_eg | totals · P | 131.04 | 131.04 | ok |
| 136 | §8.3 rec_fm_eg | totals · C | 292.82 | 292.82 | ok |
| 137 | §8.3 rec_fm_eg | totals · F | 38.16 | 38.16 | ok |
| 138 | §8.3 rec_fm_eg | kcal per 100 g (yield 1,400 g) | 140 | 140 | ok |
| 139 | §8.3 rec_fm_eg | kcal per g | 1.4 | 1.4 | ok |
| 140 | §8.3 rec_fm_eg | 16 g spoon kcal | 22.4 | 22.4 | ok |
| 141 | §8.3 rec_fm_eg | P per 100 g | 9.36 | 9.36 | ok |
| 142 | §8.3 rec_fm_eg | C per 100 g = 14641/700 | 14641/700 (≈20.915714) | 14641/700 (≈20.915714) | ok |
| 143 | §8.3 rec_fm_eg | F per 100 g = 477/175 | 477/175 (≈2.725714) | 477/175 (≈2.725714) | ok |
| 144 | §7.1 Mona | cheese spoon · White cheese 5.4 g (5.4/27 serving) · kcal | 12.5 | 12.5 | ok |
| 145 | §7.1 Mona | cheese spoon · White cheese 5.4 g (5.4/27 serving) · P | 1.8 | 1.8 | ok |
| 146 | §7.1 Mona | cheese spoon · White cheese 5.4 g (5.4/27 serving) · C | 0.5 | 0.5 | ok |
| 147 | §7.1 Mona | cheese spoon · White cheese 5.4 g (5.4/27 serving) · F | 0.4 | 0.4 | ok |
| 148 | §7.1 Mona | cheese spoon · 5.4/27 of a serving | 0.2 | 0.2 | ok |
| 149 | §7.1 Mona | cheese spoon · Olive oil 1.5 g · kcal | 13.5 | 13.5 | ok |
| 150 | §7.1 Mona | cheese spoon · Olive oil 1.5 g · P | 0 | 0 | ok |
| 151 | §7.1 Mona | cheese spoon · Olive oil 1.5 g · C | 0 | 0 | ok |
| 152 | §7.1 Mona | cheese spoon · Olive oil 1.5 g · F | 1.5 | 1.5 | ok |
| 153 | §7.1 Mona | cheese spoon · measured total g (AT-02: 6.9 incl. 1.5 oil → cheese 5.4) | 6.9 | 6.9 | ok |
| 154 | §7.1 Mona | cheese spoon · kcal | 26.0 | 26 | ok |
| 155 | §7.1 Mona | cheese spoon · P | 1.8 | 1.8 | ok |
| 156 | §7.1 Mona | cheese spoon · C | 0.5 | 0.5 | ok |
| 157 | §7.1 Mona | cheese spoon · F | 1.9 | 1.9 | ok |
| 158 | §7.1 Mona | Bread, baladi 8 g (bread bite v1) · kcal | 20 | 20 | ok |
| 159 | §7.1 Mona | Bread, baladi 8 g (bread bite v1) · P | 0.7 | 0.7 | ok |
| 160 | §7.1 Mona | Bread, baladi 8 g (bread bite v1) · C | 4.0 | 4 | ok |
| 161 | §7.1 Mona | Bread, baladi 8 g (bread bite v1) · F | 0.1 | 0.1 | ok |
| 162 | §7.1 Mona | cheese bite · kcal | 46.0 | 46 | ok |
| 163 | §7.1 Mona | cheese bite · P | 2.5 | 2.5 | ok |
| 164 | §7.1 Mona | cheese bite · C | 4.5 | 4.5 | ok |
| 165 | §7.1 Mona | cheese bite · F | 2.0 | 2 | ok |
| 166 | §7.1 Mona | egg bite · Egg, boiled 12 g · kcal | 18.6 | 18.6 | ok |
| 167 | §7.1 Mona | egg bite · Egg, boiled 12 g · P | 1.56 | 1.56 | ok |
| 168 | §7.1 Mona | egg bite · Egg, boiled 12 g · C | 0.12 | 0.12 | ok |
| 169 | §7.1 Mona | egg bite · Egg, boiled 12 g · F | 1.32 | 1.32 | ok |
| 170 | §7.1 Mona | egg bite · kcal | 38.6 | 38.6 | ok |
| 171 | §7.1 Mona | egg bite · P | 2.26 | 2.26 | ok |
| 172 | §7.1 Mona | egg bite · C | 4.12 | 4.12 | ok |
| 173 | §7.1 Mona | egg bite · F | 1.42 | 1.42 | ok |
| 174 | §7.1 Mona | boiled egg 50 g · kcal | 77.5 | 77.5 | ok |
| 175 | §7.1 Mona | boiled egg 50 g · P | 6.5 | 6.5 | ok |
| 176 | §7.1 Mona | boiled egg 50 g · C | 0.5 | 0.5 | ok |
| 177 | §7.1 Mona | boiled egg 50 g · F | 5.5 | 5.5 | ok |
| 178 | §7.1 Mona | meat bite · Beef, stewed 10 g · kcal | 25.0 | 25 | ok |
| 179 | §7.1 Mona | meat bite · Beef, stewed 10 g · P | 3.0 | 3 | ok |
| 180 | §7.1 Mona | meat bite · Beef, stewed 10 g · C | 0 | 0 | ok |
| 181 | §7.1 Mona | meat bite · Beef, stewed 10 g · F | 1.45 | 1.45 | ok |
| 182 | §7.1 Mona | meat bite · Bread, baladi 5 g · kcal | 12.5 | 12.5 | ok |
| 183 | §7.1 Mona | meat bite · Bread, baladi 5 g · P | 0.4375 | 0.4375 | ok |
| 184 | §7.1 Mona | meat bite · Bread, baladi 5 g · C | 2.5 | 2.5 | ok |
| 185 | §7.1 Mona | meat bite · Bread, baladi 5 g · F | 0.0625 | 0.0625 | ok |
| 186 | §7.1 Mona | meat bite · kcal | 37.5 | 37.5 | ok |
| 187 | §7.1 Mona | meat bite · P | 3.4375 | 3.4375 | ok |
| 188 | §7.1 Mona | meat bite · C | 2.5 | 2.5 | ok |
| 189 | §7.1 Mona | meat bite · F | 1.5125 | 1.5125 | ok |
| 190 | §7.1 Mona | glass of milk 250 ml · kcal | 150 | 150 | ok |
| 191 | §7.1 Mona | glass of milk 250 ml · P | 8 | 8 | ok |
| 192 | §7.1 Mona | glass of milk 250 ml · C | 11.5 | 11.5 | ok |
| 193 | §7.1 Mona | glass of milk 250 ml · F | 8 | 8 | ok |
| 194 | §7.1 Mona | my teaspoon (Sugar 3.75 g) · kcal | 15.0 | 15 | ok |
| 195 | §7.1 Mona | my teaspoon (Sugar 3.75 g) · P | 0 | 0 | ok |
| 196 | §7.1 Mona | my teaspoon (Sugar 3.75 g) · C | 3.75 | 3.75 | ok |
| 197 | §7.1 Mona | my teaspoon (Sugar 3.75 g) · F | 0 | 0 | ok |
| 198 | §7.1 Mona | milk tea · Milk 50 ml · kcal | 30 | 30 | ok |
| 199 | §7.1 Mona | milk tea · Milk 50 ml · P | 1.6 | 1.6 | ok |
| 200 | §7.1 Mona | milk tea · Milk 50 ml · C | 2.3 | 2.3 | ok |
| 201 | §7.1 Mona | milk tea · Milk 50 ml · F | 1.6 | 1.6 | ok |
| 202 | §7.1 Mona | glass of milk tea · kcal | 60.0 | 60 | ok |
| 203 | §7.1 Mona | glass of milk tea · P | 1.6 | 1.6 | ok |
| 204 | §7.1 Mona | glass of milk tea · C | 9.8 | 9.8 | ok |
| 205 | §7.1 Mona | glass of milk tea · F | 1.6 | 1.6 | ok |
| 206 | §7.1 Mona | talbina spoon fraction 16/384 | 1/24 (≈0.041667) | 1/24 (≈0.041667) | ok |
| 207 | §7.1 Mona | talbina spoon · kcal | 20.0 | 20 | ok |
| 208 | §7.1 Mona | talbina spoon · P | 0.8 | 0.8 | ok |
| 209 | §7.1 Mona | talbina spoon · C | 2.625 | 2.625 | ok |
| 210 | §7.1 Mona | talbina spoon · F | 0.7 | 0.7 | ok |
| 211 | §7.1 Mona | foul spoon fraction 20/800 | 0.025 | 0.025 | ok |
| 212 | §7.1 Mona | foul spoon · kcal | 30.0 | 30 | ok |
| 213 | §7.1 Mona | foul spoon · P | 2.0 | 2 | ok |
| 214 | §7.1 Mona | foul spoon · C | 4.0 | 4 | ok |
| 215 | §7.1 Mona | foul spoon · F | 0.7 | 0.7 | ok |
| 216 | §7.1 Mona | foul spoon with oil · kcal | 43.5 | 43.5 | ok |
| 217 | §7.1 Mona | foul spoon with oil · P | 2.0 | 2 | ok |
| 218 | §7.1 Mona | foul spoon with oil · C | 4.0 | 4 | ok |
| 219 | §7.1 Mona | foul spoon with oil · F | 2.2 | 2.2 | ok |
| 220 | §7.1 Mona | foul bite · kcal | 50.0 | 50 | ok |
| 221 | §7.1 Mona | foul bite · P | 2.7 | 2.7 | ok |
| 222 | §7.1 Mona | foul bite · C | 8.0 | 8 | ok |
| 223 | §7.1 Mona | foul bite · F | 0.8 | 0.8 | ok |
| 224 | §7.1 Mona | baladi loaf 92 g · kcal | 230 | 230 | ok |
| 225 | §7.1 Mona | baladi loaf 92 g · P | 8.05 | 8.05 | ok |
| 226 | §7.1 Mona | baladi loaf 92 g · C | 46.0 | 46 | ok |
| 227 | §7.1 Mona | baladi loaf 92 g · F | 1.15 | 1.15 | ok |
| 228 | §7.1 Mona | molokhia plate fraction 300/2,000 | 0.15 | 0.15 | ok |
| 229 | §7.1 Mona | molokhia plate · kcal | 360 | 360 | ok |
| 230 | §7.1 Mona | molokhia plate · P | 11.307 | 11.307 | ok |
| 231 | §7.1 Mona | molokhia plate · C | 65.838 | 65.838 | ok |
| 232 | §7.1 Mona | molokhia plate · F | 5.805 | 5.805 | ok |
| 233 | §7.1 Mona | tuna spoon (in oil) 23 g · kcal | 46.0 | 46 | ok |
| 234 | §7.1 Mona | tuna spoon (in oil) 23 g · P | 6.67 | 6.67 | ok |
| 235 | §7.1 Mona | tuna spoon (in oil) 23 g · C | 0 | 0 | ok |
| 236 | §7.1 Mona | tuna spoon (in oil) 23 g · F | 1.84 | 1.84 | ok |
| 237 | §7.1 Mona | tuna bite · tuna in oil 6.8 g · kcal | 13.6 | 13.6 | ok |
| 238 | §7.1 Mona | tuna bite · tuna in oil 6.8 g · P | 1.972 | 1.972 | ok |
| 239 | §7.1 Mona | tuna bite · tuna in oil 6.8 g · C | 0 | 0 | ok |
| 240 | §7.1 Mona | tuna bite · tuna in oil 6.8 g · F | 0.544 | 0.544 | ok |
| 241 | §7.1 Mona | tuna bite · kcal | 33.6 | 33.6 | ok |
| 242 | §7.1 Mona | tuna bite · P | 2.672 | 2.672 | ok |
| 243 | §7.1 Mona | tuna bite · C | 4.0 | 4 | ok |
| 244 | §7.1 Mona | tuna bite · F | 0.644 | 0.644 | ok |
| 245 | §7.1 Mona | olive 4 g · kcal | 5.3 | 5.3 | ok |
| 246 | §7.1 Mona | olive 4 g · P | 0 | 0 | ok |
| 247 | §7.1 Mona | olive 4 g · C | 0.2 | 0.2 | ok |
| 248 | §7.1 Mona | olive 4 g · F | 0.5 | 0.5 | ok |
| 249 | §7.2 Faisal | cheese bite (as Mona's) · kcal | 46.0 | 46 | ok |
| 250 | §7.2 Faisal | cheese bite (as Mona's) · P | 2.5 | 2.5 | ok |
| 251 | §7.2 Faisal | cheese bite (as Mona's) · C | 4.5 | 4.5 | ok |
| 252 | §7.2 Faisal | cheese bite (as Mona's) · F | 2.0 | 2 | ok |
| 253 | §7.2 Faisal | cup of laban 60.8 × 2.5 | 152 | 152 | ok |
| 254 | §7.2 Faisal | cup of laban 250 ml · kcal | 152 | 152 | ok |
| 255 | §7.2 Faisal | cup of laban 250 ml · P | 8 | 8 | ok |
| 256 | §7.2 Faisal | cup of laban 250 ml · C | 12 | 12 | ok |
| 257 | §7.2 Faisal | cup of laban 250 ml · F | 8 | 8 | ok |
| 258 | §7.2 Faisal | Sukkari date mean mass 80.0 g / 10 (FR-013) | 8.0 | 8 | ok |
| 259 | §7.2 Faisal | Sukkari date 8.0 g · kcal | 24 | 24 | ok |
| 260 | §7.2 Faisal | Sukkari date 8.0 g · P | 0.2 | 0.2 | ok |
| 261 | §7.2 Faisal | Sukkari date 8.0 g · C | 5.8 | 5.8 | ok |
| 262 | §7.2 Faisal | Sukkari date 8.0 g · F | 0 | 0 | ok |
| 263 | §7.2 Faisal | kabsa rice spoon fraction 25/2,000 | 0.0125 | 0.0125 | ok |
| 264 | §7.2 Faisal | kabsa rice spoon · kcal | 42.4 | 42.4 | ok |
| 265 | §7.2 Faisal | kabsa rice spoon · P | 0.9 | 0.9 | ok |
| 266 | §7.2 Faisal | kabsa rice spoon · C | 7.0 | 7 | ok |
| 267 | §7.2 Faisal | kabsa rice spoon · F | 1.2 | 1.2 | ok |
| 268 | §7.2 Faisal | kabsa rice spoon = 212/5 | 42.4 | 42.4 | ok |
| 269 | §7.2 Faisal | chicken piece 60 g · kcal | 114 | 114 | ok |
| 270 | §7.2 Faisal | chicken piece 60 g · P | 15 | 15 | ok |
| 271 | §7.2 Faisal | chicken piece 60 g · C | 0 | 0 | ok |
| 272 | §7.2 Faisal | chicken piece 60 g · F | 6 | 6 | ok |
| 273 | §7.2 Faisal | cup of gahwa 60 ml · kcal | 3 | 3 | ok |
| 274 | §7.2 Faisal | cup of gahwa 60 ml · P | 0 | 0 | ok |
| 275 | §7.2 Faisal | cup of gahwa 60 ml · C | 0 | 0 | ok |
| 276 | §7.2 Faisal | cup of gahwa 60 ml · F | 0 | 0 | ok |
| 277 | §7.2 Faisal | salad spoon 30 g · kcal | 16.2 | 16.2 | ok |
| 278 | §7.2 Faisal | salad spoon 30 g · P | 0.3 | 0.3 | ok |
| 279 | §7.2 Faisal | salad spoon 30 g · C | 1.5 | 1.5 | ok |
| 280 | §7.2 Faisal | salad spoon 30 g · F | 0.99 | 0.99 | ok |
| 281 | §7.3 Sam | bread bite v2 9 g · kcal | 22.5 | 22.5 | ok |
| 282 | §7.3 Sam | bread bite v2 9 g · P | 0.7875 | 0.7875 | ok |
| 283 | §7.3 Sam | bread bite v2 9 g · C | 4.5 | 4.5 | ok |
| 284 | §7.3 Sam | bread bite v2 9 g · F | 0.1125 | 0.1125 | ok |
| 285 | §7.3 Sam | tuna spoon (water) 23 g · kcal | 26.68 | 26.68 | ok |
| 286 | §7.3 Sam | tuna spoon (water) 23 g · P | 5.865 | 5.865 | ok |
| 287 | §7.3 Sam | tuna spoon (water) 23 g · C | 0 | 0 | ok |
| 288 | §7.3 Sam | tuna spoon (water) 23 g · F | 0.184 | 0.184 | ok |
| 289 | §7.3 Sam | biscuit serving 25 g · kcal | 125 | 125 | ok |
| 290 | §7.3 Sam | biscuit serving 25 g · P | 1.5 | 1.5 | ok |
| 291 | §7.3 Sam | biscuit serving 25 g · C | 17 | 17 | ok |
| 292 | §7.3 Sam | biscuit serving 25 g · F | 5.5 | 5.5 | ok |
| 293 | §7.3 Sam | foul spoon (his tin) 20 g · kcal | 30.0 | 30 | ok |
| 294 | §7.3 Sam | foul spoon (his tin) 20 g · P | 2.0 | 2 | ok |
| 295 | §7.3 Sam | foul spoon (his tin) 20 g · C | 4.0 | 4 | ok |
| 296 | §7.3 Sam | foul spoon (his tin) 20 g · F | 0.7 | 0.7 | ok |
| 297 | §7.3 Sam | foul spoon with oil · kcal | 43.5 | 43.5 | ok |
| 298 | §7.3 Sam | foul spoon with oil · P | 2.0 | 2 | ok |
| 299 | §7.3 Sam | foul spoon with oil · C | 4.0 | 4 | ok |
| 300 | §7.3 Sam | foul spoon with oil · F | 2.2 | 2.2 | ok |
| 301 | §7.3 Sam | sandwich quarter 4/4/9 (AT-20) | 250 | 250 | ok |
| 302 | §7.3 Sam | sandwich quarter 4C (AT-20) | 75.1 | 75.1 | ok |
| 303 | §7.3 Sam | sandwich quarter carb share (AT-20) | 0.3004 | 0.3004 | ok |
| 304 | §7.3 Sam | sandwich quarter fails 30.00 % on unrounded value (AT-20) | fails | fails | ok |
| 305 | §7.3 Sam | grilled chicken bite · Chicken, grilled 15 g · kcal | 28.8 | 28.8 | ok |
| 306 | §7.3 Sam | grilled chicken bite · Chicken, grilled 15 g · P | 4.5 | 4.5 | ok |
| 307 | §7.3 Sam | grilled chicken bite · Chicken, grilled 15 g · C | 0 | 0 | ok |
| 308 | §7.3 Sam | grilled chicken bite · Chicken, grilled 15 g · F | 1.2 | 1.2 | ok |
| 309 | §7.3 Sam | grilled chicken bite · kcal | 48.8 | 48.8 | ok |
| 310 | §7.3 Sam | grilled chicken bite · P | 5.2 | 5.2 | ok |
| 311 | §7.3 Sam | grilled chicken bite · C | 4.0 | 4 | ok |
| 312 | §7.3 Sam | grilled chicken bite · F | 1.3 | 1.3 | ok |
| 313 | §7.3 Sam | hummus bite · Hummus 15 g · kcal | 34.35 | 34.35 | ok |
| 314 | §7.3 Sam | hummus bite · Hummus 15 g · P | 1.1025 | 1.1025 | ok |
| 315 | §7.3 Sam | hummus bite · Hummus 15 g · C | 2.235 | 2.235 | ok |
| 316 | §7.3 Sam | hummus bite · Hummus 15 g · F | 2.565 | 2.565 | ok |
| 317 | §7.3 Sam | hummus bite · kcal | 54.35 | 54.35 | ok |
| 318 | §7.3 Sam | hummus bite · P | 1.8025 | 1.8025 | ok |
| 319 | §7.3 Sam | hummus bite · C | 6.235 | 6.235 | ok |
| 320 | §7.3 Sam | hummus bite · F | 2.665 | 2.665 | ok |
| 321 | §7.3 Sam | fries handful 30 g · kcal | 93 | 93 | ok |
| 322 | §7.3 Sam | fries handful 30 g · P | 0.99 | 0.99 | ok |
| 323 | §7.3 Sam | fries handful 30 g · C | 12 | 12 | ok |
| 324 | §7.3 Sam | fries handful 30 g · F | 4.5 | 4.5 | ok |
| 325 | §7.3 Sam | oat biscuit 4/4/9 | 115 | 115 | ok |
| 326 | §7.4 Hala T1 | Template «فطار» 3 cheese bites + 1 tea with milk | 198 | 198 | ok |
| 327 | §7.5 E1 | cup of laban v2 300 ml · kcal | 182.4 | 182.4 | ok |
| 328 | §7.5 E1 | cup of laban v2 300 ml · P | 9.6 | 9.6 | ok |
| 329 | §7.5 E1 | cup of laban v2 300 ml · C | 14.4 | 14.4 | ok |
| 330 | §7.5 E1 | cup of laban v2 300 ml · F | 9.6 | 9.6 | ok |
| 331 | §7.5 E1 | chicken piece v2 70 g · kcal | 133 | 133 | ok |
| 332 | §7.5 E1 | chicken piece v2 70 g · P | 17.5 | 17.5 | ok |
| 333 | §7.5 E1 | chicken piece v2 70 g · C | 0 | 0 | ok |
| 334 | §7.5 E1 | chicken piece v2 70 g · F | 7 | 7 | ok |
| 335 | §7.5 E1 | tamees piece v1 50 g · kcal | 140 | 140 | ok |
| 336 | §7.5 E1 | tamees piece v1 50 g · P | 4.5 | 4.5 | ok |
| 337 | §7.5 E1 | tamees piece v1 50 g · C | 27.5 | 27.5 | ok |
| 338 | §7.5 E1 | tamees piece v1 50 g · F | 1.25 | 1.25 | ok |
| 339 | §7.5 E1 | tamees piece v2 60 g · kcal | 168 | 168 | ok |
| 340 | §7.5 E1 | tamees piece v2 60 g · P | 5.4 | 5.4 | ok |
| 341 | §7.5 E1 | tamees piece v2 60 g · C | 33 | 33 | ok |
| 342 | §7.5 E1 | tamees piece v2 60 g · F | 1.5 | 1.5 | ok |
| 343 | §7.5 E1 | hummus spoon 20 g · kcal | 45.8 | 45.8 | ok |
| 344 | §7.5 E1 | hummus spoon 20 g · P | 1.47 | 1.47 | ok |
| 345 | §7.5 E1 | hummus spoon 20 g · C | 2.98 | 2.98 | ok |
| 346 | §7.5 E1 | hummus spoon 20 g · F | 3.42 | 3.42 | ok |
| 347 | §7.5 E1 | tea glass (tea 150 ml + sugar 5 g) · kcal | 20 | 20 | ok |
| 348 | §7.5 E1 | tea glass (tea 150 ml + sugar 5 g) · P | 0 | 0 | ok |
| 349 | §7.5 E1 | tea glass (tea 150 ml + sugar 5 g) · C | 5 | 5 | ok |
| 350 | §7.5 E1 | tea glass (tea 150 ml + sugar 5 g) · F | 0 | 0 | ok |
| 351 | §7.5 E1 | E1 Units | 9 | 9 | ok |
| 352 | §7.5 E1 | E1 Unit versions | 12 | 12 | ok |
| 353 | §7.5 acct_c2d7e5 | Units (cheese bite, bread bite, cup of laban, Sukkari date) | 4 | 4 | ok |
| 354 | §7.5 acct_c2d7e5 | Unit versions (bread bite v1+v2) | 5 | 5 | ok |
| 355 | §8.2 White cheese per 100 g | kcal = 6250/27 | 6250/27 (≈231.481481) | 6250/27 (≈231.481481) | ok |
| 356 | §8.2 White cheese per 100 g | kcal | 231.5 (shown) | 6250/27 (≈231.481481) | ok |
| 357 | §8.2 White cheese per 100 g | P = 100/3 | 100/3 (≈33.333333) | 100/3 (≈33.333333) | ok |
| 358 | §8.2 White cheese per 100 g | P | 33.3 (shown) | 100/3 (≈33.333333) | ok |
| 359 | §8.2 White cheese per 100 g | C = 250/27 | 250/27 (≈9.259259) | 250/27 (≈9.259259) | ok |
| 360 | §8.2 White cheese per 100 g | C | 9.3 (shown) | 250/27 (≈9.259259) | ok |
| 361 | §8.2 White cheese per 100 g | F = 200/27 | 200/27 (≈7.407407) | 200/27 (≈7.407407) | ok |
| 362 | §8.2 White cheese per 100 g | F | 7.4 (shown) | 200/27 (≈7.407407) | ok |
| 363 | §8 4/4/9 | Barley flour 4/4/9 | 350 | 350 | ok |
| 364 | §8 4/4/9 | Biscuits, plain 10 g piece kcal (AT-08) | 50 | 50 | ok |
| 365 | §8 4/4/9 | Biscuits, plain 4/4/9 | 494 | 494 | ok |
| 366 | §8 4/4/9 | White cheese 4/4/9 per 27 g serving | 64.0 | 64 | ok |
| 367 | §8 4/4/9 | Falafel 4/4/9 (13.3/31.8/17.8) | 340.6 | 340.6 | ok |
| 368 | §8 4/4/9 | Date-filled biscuit 4/4/9 per piece | 248 | 248 | ok |
| 369 | §8 4/4/9 | Laban drink 4/4/9 per 100 ml | 60.8 | 60.8 | ok |
| 370 | §8 4/4/9 | Hummus (FDC 321358) 4/4/9 ≈ 243 general Atwater | 242.9 | 242.9 | ok |
| 371 | §8.6 flags | F-06 Oat biscuit per 30 g · 4/4/9 | 115 | 115 | ok |
| 372 | §8.6 flags | F-06 Oat biscuit per 30 g · gap kcal | 20 | 20 | ok |
| 373 | §8.6 flags | F-06 Oat biscuit per 30 g · gap % | 21.1 (shown) | 400/19 (≈21.052632) | ok |
| 374 | §8.6 flags | F-06 Oat biscuit per 30 g · flag under Policy v1 (>10 % and >10 kcal) | flag | flag | ok |
| 375 | §8.6 flags | F-07 Date-filled biscuit per piece · 4/4/9 | 248 | 248 | ok |
| 376 | §8.6 flags | F-07 Date-filled biscuit per piece · gap kcal | 48 | 48 | ok |
| 377 | §8.6 flags | F-07 Date-filled biscuit per piece · gap % | 24.0 (shown) | 24 | ok |
| 378 | §8.6 flags | F-07 Date-filled biscuit per piece · flag under Policy v1 (>10 % and >10 kcal) | flag | flag | ok |
| 379 | §8.6 flags | F-08 Basbousa per 100 g · 4/4/9 | 422 | 422 | ok |
| 380 | §8.6 flags | F-08 Basbousa per 100 g · gap kcal | 42 | 42 | ok |
| 381 | §8.6 flags | F-08 Basbousa per 100 g · gap % | 11.1 (shown) | 210/19 (≈11.052632) | ok |
| 382 | §8.6 flags | F-08 Basbousa per 100 g · flag under Policy v1 (>10 % and >10 kcal) | flag | flag | ok |
| 383 | §8.6 flags | F-09 Ma'amoul per 100 g · 4/4/9 | 488 | 488 | ok |
| 384 | §8.6 flags | F-09 Ma'amoul per 100 g · gap kcal | 58 | 58 | ok |
| 385 | §8.6 flags | F-09 Ma'amoul per 100 g · gap % | 13.5 (shown) | 580/43 (≈13.488372) | ok |
| 386 | §8.6 flags | F-09 Ma'amoul per 100 g · flag under Policy v1 (>10 % and >10 kcal) | flag | flag | ok |
| 387 | §8.6 flags | F-08 Basbousa under Policy v2 (>12 %) | not a flag | not a flag | ok |
| 388 | §8.6 flags | open flags = 5 Estimated analogue + 4 Energy mismatch | 9 | 9 | ok |
| 389 | §8.6 flags | Date (generic) (approved reference row, per 100 g) not flagged | no flag (not in F-01…F-09) | 4/4/9 313.31 vs 282: gap 31.31 kcal, 11.10 % → meets the v1 rule | **DEFECT** |
| 390 | §8.6 flags | Cumin, ground (approved reference row, per 100 g) not flagged | no flag (not in F-01…F-09) | 4/4/9 446 vs 400: gap 46 kcal, 11.50 % → meets the v1 rule | **DEFECT** |
| 391 | §5.2–5.3 Targets | Mona resting energy (836 + 1,025 − 200 − 161) | 1500 | 1500 | ok |
| 392 | §5.2–5.3 Targets | Mona maintenance (×1.2 + 400) | 2200 | 2200 | ok |
| 393 | §5.2–5.3 Targets | Mona deficit min(15 %, 500) | 330 | 330 | ok |
| 394 | §5.2–5.3 Targets | Mona tv_mona_1 kcal (−15 %) | 1870 | 1870 | ok |
| 395 | §5.2–5.3 Targets | tv_mona_1 P g | 140.25 | 140.25 | ok |
| 396 | §5.2–5.3 Targets | tv_mona_1 C g | 187.0 | 187 | ok |
| 397 | §5.2–5.3 Targets | tv_mona_1 F g | 187/3 (≈62.333333) | 187/3 (≈62.333333) | ok |
| 398 | §5.2–5.3 Targets | tv_mona_2 P g | 131.25 | 131.25 | ok |
| 399 | §5.2–5.3 Targets | tv_mona_2 C g | 175.0 | 175 | ok |
| 400 | §5.2–5.3 Targets | tv_mona_2 F g | 175/3 (≈58.333333) | 175/3 (≈58.333333) | ok |
| 401 | §5.2–5.3 Targets | tv_faisal_1 P g | 153 | 153 | ok |
| 402 | §5.2–5.3 Targets | tv_faisal_1 C g | 204 | 204 | ok |
| 403 | §5.2–5.3 Targets | tv_faisal_1 F g | 68 | 68 | ok |
| 404 | §5.2–5.3 Targets | tv_sam_1 P g | 116.875 | 116.875 | ok |
| 405 | §5.2–5.3 Targets | tv_sam_1 C g | 140.25 | 140.25 | ok |
| 406 | §5.2–5.3 Targets | tv_sam_1 F g | 93.5 | 93.5 | ok |
| 407 | §5.2–5.3 Targets | Faisal resting energy (800 + 1,100 − 205 + 5) | 1700 | 1700 | ok |
| 408 | §5.2–5.3 Targets | Faisal tv_faisal_1 (×1.2, no deficit) | 2040 | 2040 | ok |
| 409 | §5.2–5.3 Targets | Faisal protein-first floor 1.2 g/kg × 80 | 96 | 96 | ok |
| 410 | §5.2–5.3 Targets | Sam maintenance 1,779 × 1.2 + 200 (FRD §11.3) | 2334.8 | 2334.8 | ok |
| 411 | §5.2–5.3 Targets | Sam −20 % (FRD §11.3) | 1867.84 | 1867.84 | ok |
| 412 | §5.2–5.3 Targets | Sam approved (nearest 10) | 1870 | 1870 | ok |
| 413 | §5.2–5.3 Targets | Sam over_deficit_cap (deficit 464.8 vs cap min(350.22, 500)) | true | true | ok |
| 414 | join J104 | Sam Activity-adjusted base 1,870 × 2,134.8 ÷ 2,334.8 (shown 1,710) | 1710 | 1710 | ok |
| 415 | join J104 (not in seed) | Sam base 1,870 × 2,134.8 ÷ 2,334.8 | 1709.78 (shown) | 9980190/5837 (≈1709.814973) | **DEFECT** |
| 416 | join J104 | gap 1 − 1,870 ÷ 2,334.8 (%) | 19.9 (shown) | 116200/5837 (≈19.907487) | ok |
| 417 | §5.2–5.3 Targets | E1 resting energy (620 + 987.5 − 145 − 161) | 1301.5 | 1301.5 | ok |
| 418 | §5.2–5.3 Targets | E1 maintenance ×1.2 | 1561.8 | 1561.8 | ok |
| 419 | §5.2–5.3 Targets | E1 −15 % | 1327.53 | 1327.53 | ok |
| 420 | §5.2–5.3 Targets | E1 tv_e1_1 (nearest 10) | 1330 | 1330 | ok |
| 421 | §5.2–5.3 Targets | Hala O1 resting | 1449 | 1449 | ok |
| 422 | §5.2–5.3 Targets | Hala O1 maintenance | 1738.8 | 1738.8 | ok |
| 423 | §5.2–5.3 Targets | Hala O1 lose 15 % | 1477.98 | 1477.98 | ok |
| 424 | §5.2–5.3 Targets | Hala O1 Target (nearest 10) | 1480 | 1480 | ok |
| 425 | §5.2–5.3 Targets | Huda O4 resting | 1139 | 1139 | ok |
| 426 | §5.2–5.3 Targets | Huda O4 maintenance | 1366.8 | 1366.8 | ok |
| 427 | §5.2–5.3 Targets | Huda O4 lose 15 % | 1161.78 | 1161.78 | ok |
| 428 | §5.2–5.3 Targets | Huda O4 Target = max(floor 1,200, …) | 1200 | 1200 | ok |
| 429 | §5.2–5.3 Targets | Amal O5 resting | 969 | 969 | ok |
| 430 | §5.2–5.3 Targets | Amal O5 maintenance | 1162.8 | 1162.8 | ok |
| 431 | §5.2–5.3 Targets | Amal O5 maintenance ≤ floor → Maintain at floor 1,200, no Lose (J99) | 1200, no Lose | 1200, no Lose | ok |
| 432 | §5.2–5.3 Targets | Sam O2 185 lb × 0.45359237 kg | 83.91458845 | 83.91458845 | ok |
| 433 | §5.2–5.3 Targets | Sam O2 shown | 83.9 (shown) | 83.91458845 | ok |
| 434 | §5.2–5.3 Targets | Mona review date tv_mona_1 (+14 d) | 0 | 0 | ok |
| 435 | §5.2–5.3 Targets | Mona review date tv_mona_2 (+14 d) | 0 | 0 | ok |
| 436 | FRD AT values (lens-held) | AT-01 mean 71.7 g / 7 (stored 10.242857…) | 717/70 (≈10.242857) | 717/70 (≈10.242857) | ok |
| 437 | FRD AT values (lens-held) | AT-01 display | 10.24 (shown) | 717/70 (≈10.242857) | ok |
| 438 | FRD AT values (lens-held) | AT-01 single weights 9.8+10.1+10.6+10.0+10.4+10.3+10.5 (eater-2.13) | 71.7 | 71.7 | ok |
| 439 | FRD AT values (lens-held) | AT-03 15.1 + 14.4 + 8.6 | 38.1 | 38.1 | ok |
| 440 | FRD AT values (lens-held) | AT-04 three dipped egg bites × 8 g bread | 24 | 24 | ok |
| 441 | FRD AT values (lens-held) | AT-09 46 + 32 + 24 | 102 | 102 | ok |
| 442 | FRD AT values (lens-held) | AT-09 fat 46/102 | 45.10 (shown) | 2300/51 (≈45.098039) | ok |
| 443 | FRD AT values (lens-held) | AT-09 carbohydrate 32/102 | 31.37 (shown) | 1600/51 (≈31.372549) | ok |
| 444 | FRD AT values (lens-held) | AT-09 protein 24/102 | 23.53 (shown) | 400/17 (≈23.529412) | ok |
| 445 | join J74 | eater-2.19 2 % of 38.1 | 0.762 | 0.762 | ok |
| 446 | join J74 | approver-10.55 2 % of 6.9 | 0.138 | 0.138 | ok |
| 447 | §7.6 planner | eater-5.12 3 foul bites kcal | 150 | 150 | ok |
| 448 | §7.6 planner | eater-5.12 filling alone (3 foul spoons) | 90 | 90 | ok |
| 449 | §7.6 planner | eater-5.17 foul bite ×6 kcal | 300 | 300 | ok |
| 450 | §7.6 planner | eater-5.17 cheese bite ×4 kcal | 184 | 184 | ok |
| 451 | §7.6 planner | eater-5.17 olive ×1 kcal | 5.3 | 5.3 | ok |
| 452 | §7.6 planner | eater-5.17 egg bite ×8 kcal | 308.8 | 308.8 | ok |
| 453 | §7.6 planner | eater-5.17 tuna bite ×1 kcal | 33.6 | 33.6 | ok |
| 454 | §7.6 planner | eater-5.17 total kcal | 831.7 | 831.7 | ok |
| 455 | §7.6 planner | eater-5.17 total shown | 832 (shown) | 831.7 | ok |
| 456 | §7.6 planner | eater-5.17 calorie share foul (4 dp) | 36.0707 (shown) | 300000/8317 (≈36.070699) | ok |
| 457 | §7.6 planner | eater-5.17 calorie share foul (largest remainder) | 36.1 | 36.1 | ok |
| 458 | §7.6 planner | eater-5.17 calorie share cheese (4 dp) | 22.1234 (shown) | 184000/8317 (≈22.123362) | ok |
| 459 | §7.6 planner | eater-5.17 calorie share cheese (largest remainder) | 22.1 | 22.1 | ok |
| 460 | §7.6 planner | eater-5.17 calorie share olive (4 dp) | 0.6372 (shown) | 5300/8317 (≈0.637249) | ok |
| 461 | §7.6 planner | eater-5.17 calorie share olive (largest remainder) | 0.6 | 0.6 | ok |
| 462 | §7.6 planner | eater-5.17 calorie share egg (4 dp) | 37.1288 (shown) | 308800/8317 (≈37.128772) | ok |
| 463 | §7.6 planner | eater-5.17 calorie share egg (largest remainder) | 37.1 | 37.1 | ok |
| 464 | §7.6 planner | eater-5.17 calorie share tuna (4 dp) | 4.0399 (shown) | 33600/8317 (≈4.039918) | ok |
| 465 | §7.6 planner | eater-5.17 calorie share tuna (largest remainder) | 4.1 | 4.1 | ok |
| 466 | §7.6 planner | eater-5.17 displayed calorie shares sum | 100.0 | 100 | ok |
| 467 | §7.6 planner | eater-5.17 count shares 30/20/5/40/5 (AT-18) | 100.0 | 100 | ok |
| 468 | §7.6 planner | eater-5.17 count shares | 30.0 · 20.0 · 5.0 · 40.0 · 5.0 | 30 · 20 · 5 · 40 · 5 | ok |
| 469 | §7.6 planner | eater-5.17 aim 831.7 (tol 0) + count shares → unique zero-deviation counts | 6/4/1/8/1 only | [[6, 4, 1, 8, 1]] | ok |
| 470 | §7.6 planner | eater-5.21 foul bite carb share (32 ÷ 50) | 0.64 | 0.64 | ok |
| 471 | §7.6 planner | eater-5.21 foul bite 4C | 32 | 32 | ok |
| 472 | §7.6 planner | eater-5.21 foul bite E_macro | 50 | 50 | ok |
| 473 | §7.6 planner | eater-5.21 cheese bite carb share = 9/23 | 9/23 (≈0.391304) | 9/23 (≈0.391304) | ok |
| 474 | §7.6 planner | eater-5.21 cheese bite carb share % | 39.13 (shown) | 900/23 (≈39.130435) | ok |
| 475 | §7.6 planner | eater-5.21 egg bite 4C | 16.48 | 16.48 | ok |
| 476 | §7.6 planner | eater-5.21 egg bite E_macro | 38.3 | 38.3 | ok |
| 477 | §7.6 planner | eater-5.21 egg bite carb share % | 43.03 (shown) | 16480/383 (≈43.028721) | ok |
| 478 | §7.6 planner | eater-5.21 egg bite shown | 43.0 (shown) | 16480/383 (≈43.028721) | ok |
| 479 | §7.6 planner | cheese spoon 4C | 2.0 | 2 | ok |
| 480 | §7.6 planner | cheese spoon E_macro | 26.3 | 26.3 | ok |
| 481 | §7.6 planner | eater-5.21 'Use your cheese spoon' share % | 7.60 (shown) | 2000/263 (≈7.604563) | ok |
| 482 | §7.6 planner | eater-5.21 any nonempty set at carb ≤ 30 % (AT-17) | none (Infeasible) | 0 sets | ok |
| 483 | §7.6 planner | eater-5.21 smallest feasible maximum = min share 9/23 → one-decimal up | 39.2 % | 900/23 (≈39.130435) → 39.2 % | ok |
| 484 | §7.6 planner | eater-5.21 at 39.2 % and aim 270–330: feasible sets | 6 cheese (276), 7 cheese (322) | (0, 6, 0) 276; (0, 7, 0) 322 | ok |
| 485 | §7.6 planner | eater-5.21 best (closest to 300) | 7 cheese bites, 322 kcal (22 from 300) | (0, 7, 0) 322 (22 from 300) | ok |
| 486 | §7.6 planner | eater-5.21 'Use your cheese spoon' makes a plan feasible at 30 % | feasible | 53 sets | ok |
| 487 | §7.6 planner | eater-5.22 2 fries kcal | 186 | 186 | ok |
| 488 | §7.6 planner | eater-5.22 4 grilled chicken bites kcal | 195.2 | 195.2 | ok |
| 489 | §7.6 planner | eater-5.22 example total | 435.55 | 435.55 | ok |
| 490 | §7.6 planner | eater-5.22 example shown | 436 (shown) | 435.55 | ok |
| 491 | §7.6 planner | eater-5.22 example within ceiling 500 and fries ≥ 1 | fits | fits | ok |
| 492 | §7.6 planner | eater-5.22 ceiling 80: cheapest must-include = 1 fries 93 > 80 → Infeasible; raise to 93 | 93 | 93 | ok |
| 493 | §7.6 planner | eater-5.23 most protein (ceiling 500, chicken ≤ 3, carb ≤ 30 %) | 53 | 53 | ok |
| 494 | §7.6 planner | eater-5.23 the set reaching 53 g | 3 chicken + 1 laban (494 kcal) | r0 c3 s0 l1 | ok |
| 495 | §7.6 planner | eater-5.23 3 chicken + 1 laban kcal | 494 | 494 | ok |
| 496 | §7.6 planner | eater-5.23 3 chicken + 1 laban carb % (48 ÷ 494) | 9.72 (shown) | 2400/247 (≈9.716599) | ok |
| 497 | §7.6 planner | eater-5.23 protein 60 g infeasible at 500 | Infeasible | Infeasible | ok |
| 498 | §7.6 planner | eater-5.23 lowest ceiling reaching 60 g with ≤ 3 chicken | 646 | 646 | ok |
| 499 | §7.6 planner | eater-5.23 that set | 3 chicken + 2 laban (61 g) | r0 c3 s0 l2 (61 g) | ok |
| 500 | §7.6 planner | eater-5.23 4 chicken alone protein | 60 | 60 | ok |
| 501 | §7.6 planner | eater-5.23 4 chicken alone kcal | 456 | 456 | ok |
| 502 | §7.6 planner | eater-5.14 4 rice + 2 chicken kcal | 397.6 | 397.6 | ok |
| 503 | §7.6 planner | eater-5.14 4C | 112 | 112 | ok |
| 504 | §7.6 planner | eater-5.14 carb % (112 ÷ 397.6) | 28.17 (shown) | 2000/71 (≈28.169014) | ok |
| 505 | §7.6 planner | eater-5.14 count sets meeting ceiling 500, carb ≤ 30 %, chicken ≤ 3 (aim band not applied) | 12 | 12 | ok |
| 506 | §7.6 planner | eater-5.14 'of the 12 count sets that meet every limit' — every limit includes the aim band 360–440 (the limits list marks it 'met'; 5.21 applies the band as a limit) | 12 | 3: [(4, 2), (1, 3), (2, 3)] | **DEFECT** |
| 507 | §7.6 planner | eater-5.14 best set | 4 rice + 2 chicken (2.4 from 400) | (4, 2) 397.6 | ok |
| 508 | §7.6 planner | eater-5.14 next nearest | 1 rice + 3 chicken (384.4, 15.6 away) | (1, 3) 384.4 | ok |
| 509 | §7.6 planner | eater-5.14/5.18 5 rice + 2 chicken carb % (never returned) | 31.82 (shown) | 350/11 (≈31.818182) | ok |
| 510 | §7.6 planner | eater-5.14/5.18 5 rice + 2 chicken kcal | 440.0 | 440 | ok |
| 511 | e578 5.16 (unchanged) | 4 rice + 2 chicken + 2 salad kcal | 430.0 | 430 | ok |
| 512 | e578 5.16 (unchanged) | low (salad low 11.0) | 419.6 | 419.6 | ok |
| 513 | e578 5.16 (unchanged) | high (salad high 24.0) | 445.6 | 445.6 | ok |
| 514 | e578 5.11 (unchanged) | 4 rice + 100 g chicken · kcal | 359.6 | 359.6 | ok |
| 515 | e578 5.11 (unchanged) | 4 rice + 100 g chicken · P | 28.6 | 28.6 | ok |
| 516 | e578 5.11 (unchanged) | 4 rice + 100 g chicken · C | 28.0 | 28 | ok |
| 517 | e578 5.11 (unchanged) | 4 rice + 100 g chicken · F | 14.8 | 14.8 | ok |
| 518 | e578 5.11 (unchanged) | carb % 112 ÷ 359.6 | 31.15 (shown) | 28000/899 (≈31.145717) | ok |
| 519 | §7.6 planner | eater-5.32/5.44 2 egg bites | 77.2 | 77.2 | ok |
| 520 | §7.6 planner | eater-5.32 shown | 77 (shown) | 77.2 | ok |
| 521 | §7.6 planner | eater-8.7 eaten incl. Pending | 1246 | 1246 | ok |
| 522 | §7.6 planner | eater-8.7 remaining | 624 | 624 | ok |
| 523 | §12.1 Mona | B | 318 | 318 | ok |
| 524 | §12.1 Mona | L | 480 | 480 | ok |
| 525 | §12.1 Mona | D | 300 | 300 | ok |
| 526 | §12.1 Mona | B+L+D | 1098 | 1098 | ok |
| 527 | §12.1 Mona | X552 | 552 | 552 | ok |
| 528 | §12.1 Mona | X622 | 622 | 622 | ok |
| 529 | §12.1 Mona | X482 | 482 | 482 | ok |
| 530 | §12.1 Mona | X802 | 802 | 802 | ok |
| 531 | §12.1 Mona | X512 | 512 | 512 | ok |
| 532 | §12.1 Mona | Day 2026-09-03 | 1098 | 1098 | ok |
| 533 | §12.1 Mona | Day 2026-09-04 | 1098 | 1098 | ok |
| 534 | §12.1 Mona | Day 2026-09-05 | 1650 | 1650 | ok |
| 535 | §12.1 Mona | Day 2026-09-06 | 1098 | 1098 | ok |
| 536 | §12.1 Mona | Day 2026-09-07 | 1580 | 1580 | ok |
| 537 | §12.1 Mona | Day 2026-09-08 | 1098 | 1098 | ok |
| 538 | §12.1 Mona | Day 2026-09-09 | 1720 | 1720 | ok |
| 539 | §12.1 Mona | Day 2026-09-10 | 1098 | 1098 | ok |
| 540 | §12.1 Mona | Day 2026-09-12 | 1610 | 1610 | ok |
| 541 | §12.1 Mona | Day 2026-09-13 | 1098 | 1098 | ok |
| 542 | §12.1 Mona | Day 2026-09-14 | 1900 | 1900 | ok |
| 543 | §12.1 Mona | Day 2026-09-15 | 1098 | 1098 | ok |
| 544 | §12.1 Mona | Day 2026-09-16 | 1098 | 1098 | ok |
| 545 | §12.1 Mona | Day 2026-09-17 | 1580 | 1580 | ok |
| 546 | §12.1 Mona | Day 2026-09-18 | 1098 | 1098 | ok |
| 547 | §12.1 Mona | Day 2026-09-19 | 1650 | 1650 | ok |
| 548 | §12.1 Mona | Day 2026-09-20 | 1650 | 1650 | ok |
| 549 | §12.1 Mona | Day 2026-09-21 | 1720 | 1720 | ok |
| 550 | §12.1 Mona | Day 2026-09-23 | 1580 | 1580 | ok |
| 551 | §12.1 Mona | Day 2026-09-24 | 1900 | 1900 | ok |
| 552 | §12.1 Mona | Day 2026-09-26 | 1610 | 1610 | ok |
| 553 | §12.1 Mona | Day 2026-09-27 | 1098 | 1098 | ok |
| 554 | §12.1 Mona | Day 2026-09-28 | 1098 | 1098 | ok |
| 555 | §12.1 Mona | Day 2026-09-29 | 1650 | 1650 | ok |
| 556 | §12.1 Mona | Day 2026-09-30 | 1098 | 1098 | ok |
| 557 | §12.1 Mona | Complete Days 09-03 → 09-30 | 10 | 10 | ok |
| 558 | §12.1 Mona | Complete Days 09-04 → 10-01 | 9 | 9 | ok |
| 559 | §12.1 Mona | week 09-20 → 26 Days logged | 5 | 5 | ok |
| 560 | §12.1 Mona | week total | 8460 | 8460 | ok |
| 561 | §12.1 Mona | week average | 1692 | 1692 | ok |
| 562 | §12.1 Mona | intake vs Target (8,460 − 5 × 1,750) | -290 | -290 | ok |
| 563 | §12.1 Mona | 09-30 remaining vs 1,750 | 652 | 652 | ok |
| 564 | §12.1 Mona | 09-30 Entries (3 + 2 + 1) | 6 | 6 | ok |
| 565 | §12.1 Mona | 10-01 Provisional (B only) | 318 | 318 | ok |
| 566 | §12.1 Mona | Mona weights in 28-day period 09-04 → 10-01 (FR-060) | 4 | 4 | ok |
| 567 | §12.2 Faisal | Breakfast | 296 | 296 | ok |
| 568 | §12.2 Faisal | Lunch kabsa ×10 | 424 | 424 | ok |
| 569 | §12.2 Faisal | Lunch chicken ×4 | 456 | 456 | ok |
| 570 | §12.2 Faisal | Lunch | 880 | 880 | ok |
| 571 | §12.2 Faisal | Snack | 224 | 224 | ok |
| 572 | §12.2 Faisal | Day 2026-10-01 | 1400 | 1400 | ok |
| 573 | §12.2 Faisal | remaining 2,040 − 1,400 | 640 | 640 | ok |
| 574 | §12.2 Faisal | Ramadan kabsa ×4 | 169.6 | 169.6 | ok |
| 575 | §12.2 Faisal | Ramadan chicken ×2 | 228 | 228 | ok |
| 576 | §12.2 Faisal | Day 2027-02-08 (Voided gahwa excluded) | 911.6 | 911.6 | ok |
| 577 | §12.2 Faisal | Day 2027-02-10 | 911.6 | 911.6 | ok |
| 578 | §12.3 E1 | E5d kabsa ×8 | 339.2 | 339.2 | ok |
| 579 | §12.3 E1 | E5d chicken ×2 v1 | 228 | 228 | ok |
| 580 | §12.3 E1 | E5d chicken ×2 v2 | 266 | 266 | ok |
| 581 | §12.3 E1 | Days 2026-08-04 → 09-14 | 42 | 42 | ok |
| 582 | §12.3 E1 | Entries 08-04 → 09-14 (42 × 5) | 210 | 210 | ok |
| 583 | §12.3 E1 | Entries through 09-14 (+ 2 on 08-03) | 212 | 212 | ok |
| 584 | §12.3 E1 | Entries at export job_exp_4402 (2026-09-15T12:00Z = 15:00 Riyadh; Day 09-15 E5d at 07:00 and 13:00 already logged) | 212 | 216 | **DEFECT** |
| 585 | §12.3 E1 | Day 2026-09-29 Entries (E5d 5 + en_9921 + gahwa) | 7 | 7 | ok |
| 586 | §12.3 E1 | en_9921 Rice, cooked, with fat 200 g · kcal | 340 | 340 | ok |
| 587 | §12.3 E1 | en_9921 Rice, cooked, with fat 200 g · P | 7 | 7 | ok |
| 588 | §12.3 E1 | en_9921 Rice, cooked, with fat 200 g · C | 56 | 56 | ok |
| 589 | §12.3 E1 | en_9921 Rice, cooked, with fat 200 g · F | 9.6 | 9.6 | ok |
| 590 | §12.3 E1 | Day 2026-10-01 Entries | 4 | 4 | ok |
| 591 | §12.4 Sam | 09-08 tuna spoon ×2 | 53.36 | 53.36 | ok |
| 592 | §12.4 Sam | 09-08 total | 928.36 | 928.36 | ok |
| 593 | §12.4 Sam | 09-29 (bread bite ×6 v1 + laban) | 272 | 272 | ok |
| 594 | §12.4 Sam | 09-30 total | 1540 | 1540 | ok |
| 595 | §12.4 Sam | 10-01 · kcal | 290 | 290 | ok |
| 596 | §12.4 Sam | 10-01 · P | 15.5 | 15.5 | ok |
| 597 | §12.4 Sam | 10-01 · C | 25.5 | 25.5 | ok |
| 598 | §12.4 Sam | 10-01 · F | 14.0 | 14 | ok |
| 599 | §12.4 Sam | 10-01 remaining | 1580 | 1580 | ok |
| 600 | §12.4 Sam | 10-01 4/4/9 = 290 | 290 | 290 | ok |
| 601 | §12.4 Sam | 10-01 share P (2 dp) | 21.38 (shown) | 620/29 (≈21.379310) | ok |
| 602 | §12.4 Sam | 10-01 share P (largest remainder) | 21.4 | 21.4 | ok |
| 603 | §12.4 Sam | 10-01 share C (2 dp) | 35.17 (shown) | 1020/29 (≈35.172414) | ok |
| 604 | §12.4 Sam | 10-01 share C (largest remainder) | 35.2 | 35.2 | ok |
| 605 | §12.4 Sam | 10-01 share F (2 dp) | 43.45 (shown) | 1260/29 (≈43.448276) | ok |
| 606 | §12.4 Sam | 10-01 share F (largest remainder) | 43.4 | 43.4 | ok |
| 607 | §12.4 Sam | 10-01 4P · 4C · 9F | 290 | 290 | ok |
| 608 | §12.4 Sam | 10-02 two oatmeal bowls · kcal | 720 | 720 | ok |
| 609 | §12.4 Sam | 10-02 two oatmeal bowls · P | 30 | 30 | ok |
| 610 | §12.4 Sam | 10-02 two oatmeal bowls · C | 105 | 105 | ok |
| 611 | §12.4 Sam | 10-02 two oatmeal bowls · F | 20 | 20 | ok |
| 612 | §12.4 Sam | 10-02 Day (FRD §13.1) · kcal | 1200 | 1200 | ok |
| 613 | §12.4 Sam | 10-02 Day (FRD §13.1) · P | 72 | 72 | ok |
| 614 | §12.4 Sam | 10-02 Day (FRD §13.1) · C | 138 | 138 | ok |
| 615 | §12.4 Sam | 10-02 Day (FRD §13.1) · F | 40 | 40 | ok |
| 616 | §12.4 Sam | 10-02 remaining (FRD §13.1) | 670 | 670 | ok |
| 617 | §12.4 Sam | 10-02 Day share P (FRD §13.1) | 24.0 | 24 | ok |
| 618 | §12.4 Sam | 10-02 Day share C (FRD §13.1) | 46.0 | 46 | ok |
| 619 | §12.4 Sam | 10-02 Day share F (FRD §13.1) | 30.0 | 30 | ok |
| 620 | §12.4 Sam | FRD §13.1 meal share P (chicken rice box) | 35.0 | 35 | ok |
| 621 | §12.4 Sam | FRD §13.1 meal share C (chicken rice box) | 27.5 | 27.5 | ok |
| 622 | §12.4 Sam | FRD §13.1 meal share F (chicken rice box) | 37.5 | 37.5 | ok |
| 623 | §12.4 Sam | FRD §13.1 meal 4/4/9 = 480 | 480 | 480 | ok |
| 624 | §12.5 others | Huda Bread, baladi 80 g · kcal | 200 | 200 | ok |
| 625 | §12.5 others | Huda Bread, baladi 80 g · P | 7 | 7 | ok |
| 626 | §12.5 others | Huda Bread, baladi 80 g · C | 40 | 40 | ok |
| 627 | §12.5 others | Huda Bread, baladi 80 g · F | 1 | 1 | ok |
| 628 | §12.5 others | E4/E6/E7/E12 pattern (cheese ×3 + laban) | 290 | 290 | ok |
| 629 | §12.5 others | E10 pattern | 238 | 238 | ok |
| 630 | §12.6 Hala T1 | 2026-09-30 Entries | 7 | 7 | ok |
| 631 | §12.6 Hala T1 | 2026-09-30 kcal | 530 | 530 | ok |
| 632 | §12.6 Hala T1 | 2026-10-01 Entries | 5 | 5 | ok |
| 633 | §12.6 Hala T1 | 2026-10-01 kcal | 430 | 430 | ok |
| 634 | §12.6 Hala T1 | local commands 3 + 1 + 12 | 16 | 16 | ok |
| 635 | §4.5 prices | 1,000 in + 500 out on gemini-3.8-flash, 2026 | 0.002625 | 0.002625 | ok |
| 636 | §4.5 prices | same from 2027-01-01 | 0.00525 | 0.00525 | ok |
| 637 | §2/§5 time zones | Africa/Cairo UTC+3 on 2026-10-29 23:59 | +3 | 3:00:00 | ok |
| 638 | §2/§5 time zones | Africa/Cairo UTC+2 on 2026-10-30 00:30 | +2 | 2:00:00 | ok |
| 639 | §2/§5 time zones | Europe/London UTC+1 until 2026-10-25T01:00Z, UTC+0 after | +1 → +0 | 1:00:00 → 0:00:00 | ok |
| 640 | §2/§5 time zones | Europe/Dublin UTC+1 until 2026-10-25T01:00Z, UTC+0 after | +1 → +0 | 1:00:00 → 0:00:00 | ok |
| 641 | §2/§5 time zones | Asia/Riyadh UTC+3 | +3 | 3:00:00 | ok |
| 642 | §12 weekdays | 2026-09-30 weekday | Wed | Wed | ok |
| 643 | §12 weekdays | 2026-10-01 weekday | Thu | Thu | ok |
| 644 | §12 weekdays | 2026-10-02 weekday | Fri | Fri | ok |
| 645 | §12 weekdays | 2026-09-08 weekday | Tue | Tue | ok |
| 646 | §12 weekdays | 2026-09-28 weekday | Mon | Mon | ok |
| 647 | §12 weekdays | 2026-09-29 weekday | Tue | Tue | ok |
| 648 | §12 weekdays | 2027-02-08 weekday | Mon | Mon | ok |
| 649 | §12.2 Ramadan | 1 Ramadan 1448 (Umm al-Qura) | 2027-02-08 | 2027-02-08 | ok |
| 650 | §5.1 Ramadan days | 'Ramadan days' off 2027-03-10 14:00 Riyadh (1 Shawwal = Eid) | after Ramadan ends | 1 Shawwal 1448 = 2027-03-09 | ok |
| 651 | §2/§9/§10 local times | Policy v1 effective 2026-08-02T00:00+03:00 in UTC | 00:00 | 00:00 | ok |
| 652 | §2/§9/§10 local times | Policy v2 effective 2026-10-05T00:00+03:00 in UTC | 00:00 | 00:00 | ok |
| 653 | §2/§9/§10 local times | e36 eater-3.35 clock 11:29Z London | 12:29 | 12:29 | ok |
| 654 | §2/§9/§10 local times | Faisal story clock 18:30Z Riyadh | 21:30 | 21:30 | ok |
| 655 | §2/§9/§10 local times | Ramadan days on 2027-03-10T11:00Z Riyadh | 14:00 | 14:00 | ok |
| 656 | §2/§9/§10 local times | E2 deletion 07:00Z Cairo | 10:00 | 10:00 | ok |
| 657 | §2/§9/§10 local times | grant_40ab requested Cairo | 13:05 | 13:05 | ok |
| 658 | §2/§9/§10 local times | grant_31f0 approved Riyadh | 13:20 | 13:20 | ok |
| 659 | §2/§9/§10 local times | grant_31f0 ends Riyadh | 14:20 | 14:20 | ok |
| 660 | §2/§9/§10 local times | grant_31f0 ends Dublin | 12:20 | 12:20 | ok |
| 661 | §2/§9/§10 local times | grant_31f9 declined Riyadh | 14:42 | 14:42 | ok |
| 662 | §2/§9/§10 local times | grant_52a3 approved Cairo | 15:05 | 15:05 | ok |
| 663 | §2/§9/§10 local times | grant_52a3 withdrawn Cairo | 15:26 | 15:26 | ok |
| 664 | §2/§9/§10 local times | grant_52a7 approved Cairo | 16:04 | 16:04 | ok |
| 665 | §2/§9/§10 local times | grant_52a7 ended Cairo | 16:33 | 16:33 | ok |
| 666 | §2/§9/§10 local times | support code valid until (Riyadh) | 09:12 | 09:12 | ok |
| 667 | §2/§9/§10 local times | E1/Faisal import 04:30Z Riyadh | 07:30 | 07:30 | ok |
| 668 | §2/§9/§10 local times | E5 quota reset 2026-10-02T00:00 Cairo in UTC | 00:00 | 00:00 | ok |
| 669 | §2/§9/§10 local times | Mona 'Start new day' 02:00 Cairo on 10-01 in UTC | 02:00 | 02:00 | ok |
| 670 | §2 start clocks | eater-8.12 clock 2026-09-27T09:00Z after Mona's Day 09-26 ends (03:00 Cairo 09-27 = 00:00Z) | after | after | ok |
| 671 | §2 start clocks | e36 3.35 clock 11:29Z precedes Sam's lunch 12:30 London (11:30Z) | precedes | precedes | ok |
| 672 | §2 start clocks | eater-7.16 clock 13:00Z after all three Sam 10-02 Entries (12:30 London) | after | after | ok |
| 673 | §2 start clocks | Faisal 18:30Z after Snack 18:02 Riyadh | after | after | ok |
| 674 | §10.1 job durations | job_del_2120 requested → Completed (29 days) | 29 days | 29 days, 2:00:00 (calendar 29) | ok |
| 675 | §10.1 job durations | job_del_2201 (14 days) | 14 days | 14 days, 0:30:00 (calendar 14) | ok |
| 676 | §10.1 job durations | job_del_2212 (8 days) | 8 days | 8 days, 0:00:00 (calendar 8) | ok |
| 677 | §10.1 job durations | job_del_2205 (28 days) | 28 days | 27 days, 22:00:00 (calendar 28) | ok |
| 678 | §10.1 job durations | job_del_2120 backups expire (+30 d) | 2026-10-18T10:00:00Z | 2026-10-18T10:00:00Z | ok |
| 679 | §10.1 job durations | job_del_2201 backups expire (+30 d) | 2026-10-16T06:30:00Z | 2026-10-16T06:30:00Z | ok |
| 680 | §10.1 job durations | job_del_2215 due (+30 d) | 2026-10-15T07:00:00Z | 2026-10-15T07:00:00Z | ok |
| 681 | §10.1 job durations | job_del_2204 due | 2026-10-04T09:00:00Z | 2026-10-04T09:00:00Z | ok |
| 682 | §10.1 job durations | job_del_2205 due | 2026-10-05T08:00:00Z | 2026-10-05T08:00:00Z | ok |
| 683 | §10.1 job durations | job_del_2209 due | 2026-10-09T09:00:00Z | 2026-10-09T09:00:00Z | ok |
| 684 | §10.1 job durations | job_del_2213 due | 2026-10-13T09:00:00Z | 2026-10-13T09:00:00Z | ok |
| 685 | §10.1 job durations | job_exp_4402 file kept (+7 d) | 2026-09-22T12:04:00Z | 2026-09-22T12:04:00Z | ok |
| 686 | §10.1 job durations | job_exp_31 window (+7 d) | 2026-09-27T12:00:00Z | 2026-09-27T12:00:00Z | ok |
| 687 | §10.1 job durations | job_exp_77 window (+7 d) | 2026-10-07T15:09:00Z | 2026-10-07T15:09:00Z | ok |
| 688 | §10.1 job durations | support code SB-7KQ2-94XM (+24 h) | 2026-10-02T06:12:00Z | 2026-10-02T06:12:00Z | ok |
| 689 | §10.1 job durations | support code SB-3MRT-7WQD (+24 h) | 2026-09-30T06:12:00Z | 2026-09-30T06:12:00Z | ok |
| 690 | §10.1 job durations | an_7781 raw scan held (+30 d) | 2026-11-01T18:00:00Z | 2026-11-01T18:00:00Z | ok |
| 691 | §10.1 job durations | job_del_2204 open at 2026-10-05T09:00Z (> 30 d → Anomaly) | 31 days | 31 days, 0:00:00 | ok |
| 692 | §10.1 job durations | job_del_2205 days left at 2026-10-01T09:00Z | 4 days left | 3 days, 23:00:00 (due 10-05 08:00Z) | ok |
| 693 | §10.1 job durations | job_del_2213 days left at 2026-10-01T09:00Z | 12 days left | 12 days, 0:00:00 | ok |
| 694 | §10.1 job durations | job_del_2228 day on 2026-10-05 | day 7 of 30 | 7 | ok |
| 695 | §10.6 retention | R-0805 oldest raw scan 29 d 22 h ≤ 29 d 23 h; audio 22 h 10 min ≤ 23 h | consistent | consistent | ok |
| 696 | §10.6 retention | R-gap audio 24 h 20 min at 06:30Z with last run 03:00Z (runs 04:00–06:00 missing) | consistent | consistent | ok |
| 697 | §10.4 Activity | Faisal import 2026-10-01T04:30Z (07:30 Riyadh) holds the Watch walk 07:00–07:45 Riyadh | walk ended before import | import 07:30 Riyadh < walk end 07:45 | **DEFECT** |
| 698 | §6.1/§10.3 E1 devices | cmd_7a1e Conflict (server refusal) 2026-09-30T19:15Z from the app 1.0.2 iPhone vs that device's last sync 2026-09-29T20:40Z | refusal ≤ last sync | refusal 2026-09-30T19:15Z > last sync 2026-09-29T20:40Z | **DEFECT** |
| 699 | §5.3/§10.5 Sam weights | Sam body mass 83.5 kg on 2026-10-01 from Apple Health vs §10.5 'after 2026-09-24 no new body mass' | one timeline | §5.3 has a 10-01 Apple Health sample; §10.5 says none after 09-24 | **DEFECT** |
| 700 | §2 vocabulary | eater default clock 'every 30 Sep Day complete' | Day states are the eater's marks (Complete · Partial · Unlogged · Provisional) | Mona's 30 Sep, Sam's 30 Sep, E1's 30 Sep carry no Complete mark | **DEFECT** |
| 701 | §11 Audit trail | events in the table | 266 | 266 | ok |
| 702 | §11 Audit trail | numbering 1…266 gap-free | 1…266 | 1…266 | ok |
| 703 | §11 Audit trail | times non-decreasing | ordered | ordered | ok |
| 704 | §11 Audit trail | every action is an events.md Audit trail event name | all in catalogue | all in catalogue | ok |
| 705 | §11 Audit trail | distinct actions used | — | 49 | ok |
| 706 | §11 Audit trail | outcome words ⊂ {Allowed, Refused, Done, Failed, Not found} | vocabulary | Allowed, Done, Failed, Refused | ok |
| 707 | §11 Audit trail | codes ⊂ vocabulary errors | vocabulary | FORBIDDEN, GRANT_NOT_ACTIVE, GRANT_REQUIRED, NOT_FOUND, SERVICE_UNAVAILABLE, UNAUTHENTICATED, VALIDATION_ERROR | ok |
| 708 | §11 Audit trail | events.md 'seed event' references point at events of that name | all match | all match | ok |
| 709 | §11 Audit trail | allowed reads | 11: 108, 199, 200, 201, 213, 217, 223, 229, 244, 249, 254 | 11: [108, 199, 200, 201, 213, 217, 223, 229, 244, 249, 254] | ok |
| 710 | §11 Audit trail | refused reads | 10: 206, 207, 210, 230, 231, 232, 233, 251, 260, 264 | 10: [206, 207, 210, 230, 231, 232, 233, 251, 260, 264] | ok |
| 711 | §11 Audit trail | refused writes | 5: 234–238 | 5: [234, 235, 236, 237, 238] | ok |
| 712 | §11 Audit trail | other refusals | 3: 123, 261, 262 | 3: [123, 261, 262] | ok |
| 713 | §11 Audit trail | failed reads | 1: 245 | 1: [245] | ok |
| 714 | §11 Audit trail | staff sign-in failures / lock | 5 (189–193) / 1 (194) | [189, 190, 191, 192, 193] / [194] | ok |
| 715 | §11 Audit trail | staff_lee lock until 09:47:00 (= 5th failure 09:32 + 15 min; failures span 2 min ≤ 15) | 09:47:00 | 09:47:00 | ok |
| 716 | §11 Audit trail | Consents Diary processing Given · Withdrawn | 28 · 0 | 28 · 0 | ok |
| 717 | §11 Audit trail | Consents AI Given · Withdrawn | 10 · 2 | 10 · 2 | ok |
| 718 | §11 Audit trail | Consents Health: read workouts Given · Withdrawn | 3 · 0 | 3 · 0 | ok |
| 719 | §11 Audit trail | Consents read active energy Given · Withdrawn | 3 · 0 | 3 · 0 | ok |
| 720 | §11 Audit trail | Consents read body mass Given · Withdrawn | 2 · 0 | 2 · 0 | ok |
| 721 | §11 Audit trail | Consents write food Given · Withdrawn | 3 · 0 | 3 · 0 | ok |
| 722 | §11 Audit trail | Consents Microphone Given · Withdrawn | 4 · 0 | 4 · 0 | ok |
| 723 | §11 Audit trail | Consents Photos Given · Withdrawn | 8 · 0 | 8 · 0 | ok |
| 724 | §11 Audit trail | Consents Optional research Given · Withdrawn | 1 · 1 | 1 · 1 | ok |
| 725 | §11 Audit trail | Consents label review Given · Withdrawn | 3 · 0 | 3 · 0 | ok |
| 726 | §11 Audit trail | age confirmations | 29 | 29 | ok |
| 727 | §11 Audit trail | E1 Consent history rows | 12: 25–33, 130, 141, 148 | 12: [25, 26, 27, 28, 29, 30, 31, 32, 33, 130, 141, 148] | ok |
| 728 | §11 Audit trail | no c-ai-4 Consent before its publication 2026-09-24T08:00Z | none | [] | ok |
| 729 | §11 Audit trail | every account in the trail is a seed account | all | all | ok |
| 730 | §5/§6 accounts vs §11 | acct_9c41e2 created 2026-08-03T18:20:00Z = age.confirmed event 25 | 2026-08-03T18:20:00Z | 2026-08-03T18:20:00Z | ok |
| 731 | §5/§6 accounts vs §11 | acct_e5c3a0 created 2026-08-01T12:00:00Z = age.confirmed event 22 | 2026-08-01T12:00:00Z | 2026-08-01T12:00:00Z | ok |
| 732 | §5/§6 accounts vs §11 | acct_c2d7e5 created 2026-08-16T12:00:00Z = age.confirmed event 56 | 2026-08-16T12:00:00Z | 2026-08-16T12:00:00Z | ok |
| 733 | §5/§6 accounts vs §11 | acct_e9a001 created 2026-08-20T05:00:00Z = age.confirmed event 64 | 2026-08-20T05:00:00Z | 2026-08-20T05:00:00Z | ok |
| 734 | §5/§6 accounts vs §11 | acct_e9a002 created 2026-08-25T15:00:00Z = age.confirmed event 75 | 2026-08-25T15:00:00Z | 2026-08-25T15:00:00Z | ok |
| 735 | §5/§6 accounts vs §11 | acct_e9a003 created 2026-09-08T09:00:00Z = age.confirmed event 96 | 2026-09-08T09:00:00Z | 2026-09-08T09:00:00Z | ok |
| 736 | §5/§6 accounts vs §11 | acct_e9a004 created 2026-10-01T05:00:00Z = age.confirmed event 175 | 2026-10-01T05:00:00Z | 2026-10-01T05:00:00Z | ok |
| 737 | §5/§6 accounts vs §11 | acct_e9a005 created 2026-09-30T10:00:00Z = age.confirmed event 165 | 2026-09-30T10:00:00Z | 2026-09-30T10:00:00Z | ok |
| 738 | §5/§6 accounts vs §11 | acct_e9a006 created 2026-09-30T11:00:00Z = age.confirmed event 168 | 2026-09-30T11:00:00Z | 2026-09-30T11:00:00Z | ok |
| 739 | §5/§6 accounts vs §11 | acct_anon_71f2 created 2026-09-29T09:00:00Z = age.confirmed event 160 | 2026-09-29T09:00:00Z | 2026-09-29T09:00:00Z | ok |
| 740 | §5/§6 accounts vs §11 | 19 other accounts: created date = age.confirmed date | all equal | all equal | ok |
| 741 | §11 Audit trail | staff acts made while holding the needed role | all | all | ok |
| 742 | §11 Audit trail | event 8: proposer = approver allowed (sole Nutrition approver) | 1 holder | ['staff_dina'] | ok |
| 743 | §11 Audit trail | event 159: two Nutrition approvers, proposer ≠ approver | 2 holders, yara → dina | ['staff_dina', 'staff_yara'] | ok |
| 744 | §11 Audit trail | wording.published (event 145) by staff_ali: a permission in §3 covers it | a listed permission | no §3 permission names publishing wording | **DEFECT** |
| 745 | §9 Grants | grant_27b4 Active until = approved + 1 h | 10:10:00 | 10:10:00 | ok |
| 746 | §9 Grants | grant_27b4 expired at Active-until | 10:10:00 | 10:10:00 | ok |
| 747 | §9 Grants | grant_31f0 Active until = approved + 1 h | 11:20:00 | 11:20:00 | ok |
| 748 | §9 Grants | grant_31f0 expired at Active-until | 11:20:00 | 11:20:00 | ok |
| 749 | §9 Grants | grant_40aa Unanswered = requested + 72 h | 2026-10-04T10:09 | 2026-10-04T10:09 | ok |
| 750 | §9 Grants | grant_40ab Unanswered = requested + 72 h | 2026-09-30T10:05 | 2026-09-30T10:05 | ok |
| 751 | §9 Grants | grant_52a3 Active until = approved + 1 h | 13:05:00 | 13:05:00 | ok |
| 752 | §9 Grants | grant_52a7 Active until = approved + 1 h | 14:04:00 | 14:04:00 | ok |
| 753 | §9 Grants | grant_52c3 Active until = approved + 4 h | 12:05:00 | 12:05:00 | ok |
| 754 | §9 Grants | grant_5d10 Active until = approved + 1 h | 14:02:00 | 14:02:00 | ok |
| 755 | §9 Grants | grant_6c10 Active until = approved + 1 h | 16:02:00 | 16:02:00 | ok |
| 756 | §9 Grants | grant_6c10 expired at Active-until | 16:02:00 | 16:02:00 | ok |
| 757 | §9 Grants | grant_6c14 Unanswered = requested + 72 h | 2026-10-04T15:55 | 2026-10-04T15:55 | ok |
| 758 | §9 Grants | grant_6e21 Active until = approved + 1 h | 10:03:00 | 10:03:00 | ok |
| 759 | §9 Grants | grant_6e21 expired at Active-until | 10:03:00 | 10:03:00 | ok |
| 760 | §9 Grants | grant_7d01 Active until = approved + 1 h | 16:30:00 | 16:30:00 | ok |
| 761 | §9 Grants | grant_7d01 expired at Active-until | 16:30:00 | 16:30:00 | ok |
| 762 | §9 Grants | grant_8e20 Active until = approved + 1 h | 09:25:00 | 09:25:00 | ok |
| 763 | §9 Grants | grant_8e20 expired at Active-until | 09:25:00 | 09:25:00 | ok |
| 764 | §9 Grants | grant_9b30 Active until = approved + 1 h | 18:00:00 | 18:00:00 | ok |
| 765 | §9 Grants | grant_a1d4 Active until = approved + 1 h | 09:00:00 | 09:00:00 | ok |
| 766 | §9 Grants | grant_a1d4 expired at Active-until | 09:00:00 | 09:00:00 | ok |
| 767 | §9 Grants | grant_27b4 reads allowed · refused · writes refused (§9 table and §11 derived) | 1·0·0 | 1·0·0 | ok |
| 768 | §9 Grants | grant_40ab reads allowed · refused · writes refused (§9 table and §11 derived) | 0·0·0 | 0·0·0 | ok |
| 769 | §9 Grants | grant_a1d4 reads allowed · refused · writes refused (§9 table and §11 derived) | 0·0·0 | 0·0·0 | ok |
| 770 | §9 Grants | grant_8e20 reads allowed · refused · writes refused (§9 table and §11 derived) | 0·0·0 | 0·0·0 | ok |
| 771 | §9 Grants | grant_31f0 reads allowed · refused · writes refused (§9 table and §11 derived) | 3·2·0 | 3·2·0 | ok |
| 772 | §9 Grants | grant_40aa reads allowed · refused · writes refused (§9 table and §11 derived) | 0·1·0 | 0·1·0 | ok |
| 773 | §9 Grants | grant_31f9 reads allowed · refused · writes refused (§9 table and §11 derived) | 0·1·0 | 0·1·0 | ok |
| 774 | §9 Grants | grant_52a3 reads allowed · refused · writes refused (§9 table and §11 derived) | 1·0·0 | 1·0·0 | ok |
| 775 | §9 Grants | grant_52a7 reads allowed · refused · writes refused (§9 table and §11 derived) | 1·0·0 | 1·0·0 | ok |
| 776 | §9 Grants | grant_6c10 reads allowed · refused · writes refused (§9 table and §11 derived) | 1·0·0 | 1·0·0 | ok |
| 777 | §9 Grants | grant_7d01 reads allowed · refused · writes refused (§9 table and §11 derived) | 1·4·5 | 1·4·5 | ok |
| 778 | §9 Grants | grant_6c14 reads allowed · refused · writes refused (§9 table and §11 derived) | 0·0·0 | 0·0·0 | ok |
| 779 | §9 Grants | grant_9b30 reads allowed · refused · writes refused (§9 table and §11 derived) | 1·0·0 | 1·0·0 | ok |
| 780 | §9 Grants | grant_52c3 reads allowed · refused · writes refused (§9 table and §11 derived) | 1·1·0 | 1·1·0 | ok |
| 781 | §9 Grants | grant_5d10 reads allowed · refused · writes refused (§9 table and §11 derived) | 1·0·0 | 1·0·0 | ok |
| 782 | §9 Grants | grant_6e21 reads allowed · refused · writes refused (§9 table and §11 derived) | 0·0·0 | 0·0·0 | ok |
| 783 | §11 derived | Grants requested 2026-10-01T00:00+03:00 → 10-04T00:00+03:00 | 14 rows, newest first 6e21 … a1d4 | 14: grant_6e21 grant_5d10 grant_52c3 grant_9b30 grant_6c14 grant_7d01 grant_6c10 grant_52a7 grant_52a3 grant_31f9 grant_40aa grant_31f0 grant_8e20 grant_a1d4 | ok |
| 784 | §11 derived | Widen to the last 90 days | 16 | 16 | ok |
| 785 | §11 derived | none requested 2026-09-15 → 09-20 (+03:00) | none | [] | ok |
| 786 | §11 derived | No Grant Active at 2026-10-05T09:00Z | none | [] | ok |
| 787 | §4.6 Grant settings | one Requested Grant per eater | no overlap | no overlap | ok |
| 788 | §11 event 224 | staff_mona idle 15 min at 15:25 (last evented act 15:05 → would end 15:20) | 15:25 consistent | last evented act 15:05; idle end at 15:20 | ok |
| 789 | §11 derived | Registry Live at 2026-09-24T12:00Z / 09-26T12:00Z / 09-28T00:00Z | v6 100 % (v7 Shadow) / v7 5 % / v6 100 % | events 143 (Shadow 09-23) · 146 (Canary 09-25) · 150 (Rollout 09-27 08:00) · 155 (Rolled back 11:30) | ok |
| 790 | §4.3 Registry | kill switch 2026-09-27 10:12 → 10:47 minutes | 35 | 35 | ok |
| 791 | cross-references | §8.7 referenced in the seed exists | exists | no section §8.7 (outputs per story), USDA FoodData Central (serves §8.1 and §8.7), He…) | **DEFECT** |
| 792 | cross-references | every Jn the seed cites exists in join.md | all | all | ok |
| 793 | §1 ids / synthetic data | support codes avoid 0, O, 1, I | all | all | ok |
| 794 | §1 ids / synthetic data | deletion references DEL-yy-mmdd match the request date | all | all | ok |
| 795 | §1 ids / synthetic data | emails on reserved domains (example.test / example.com) | all | all except r7k2q9x4@privaterelay.appleid.com | ok |
| 796 | §1 ids / synthetic data | no owner identifier in the seed | none | none | ok |
| 797 | §8.1 FDC | Hummus, commercial 321358 (cached FDC record): 229 · 243 · 7.35 · 14.9 · 17.1 · fiber 5.4 · sugars 0.34 · NLEA fat 16.1 | 229 · 243 · 7.35 · 14.9 · 17.1 · 5.4 · 0.34 · 16.1 | 229 · 243 · 7.35 · 14.9 · 17.1 · 5.4 · 0.34 · 16.1 | ok |
| 798 | §7.6 planner | eater-5.17 lens /m 'Calorie aim about 809.7 with tolerance 0' with the seed's Units (not superseded by J55, not restated in §7.6) | aim 831.7 for 6/4/1/8/1 | 6/4/1/8/1 = 831.7 ≠ 809.7; other count sets reach 809.7 exactly: True | **DEFECT** |
| 799 | §12.2 Ramadan Days | iftar 18:02 on 2027-02-08 → Day (boundary 12:00) | 2027-02-08 | 2027-02-08 | ok |
| 800 | §12.2 Ramadan Days | «عشاء العائلة» 21:30 on 2027-02-08 → Day (boundary 12:00) | 2027-02-08 | 2027-02-08 | ok |
| 801 | §12.2 Ramadan Days | suhoor 03:40 on 2027-02-09 → Day (boundary 12:00) | 2027-02-08 | 2027-02-08 | ok |
| 802 | §12.2 Ramadan Days | iftar 18:02 on 2027-02-10 → Day (boundary 12:00) | 2027-02-10 | 2027-02-10 | ok |
| 803 | §12.2 Ramadan Days | suhoor 03:40 on 2027-02-11 → Day (boundary 12:00) | 2027-02-10 | 2027-02-10 | ok |
| 804 | §5.1 Ramadan days | on at 2027-02-07T20:00Z = 23:00 Riyadh on the eve of 1 Ramadan | 23:00 02-07 | 23:00 02-07 | ok |
| 805 | §12.1 Mona | 'Start new day' 02:00 on 10-01 is before the 03:00 boundary (J117) | before | before | ok |
| 806 | §12 Day ids | E1 en_9921 16:00Z = 19:00 Riyadh → Day 2026-09-29 (boundary 04:00) | 2026-09-29 | 2026-09-29 | ok |
| 807 | §12 Day ids | Sam 01:10 laban → Day 2026-09-30 (boundary 00:00) | 2026-09-30 | 2026-09-30 | ok |
| 808 | §10.2 | E5 26th photo 16:40Z = 19:40 Cairo, same diary day as the 25 (quota day = diary day, J91) | 2026-10-01 | 2026-10-01 | ok |
| 809 | FRD AT coverage | AT-01 seven pieces 71.7 g → 10.242857 g (10.24), count 7 | in seed or reassigned in join.md | neither: seed has no 71.7 g / 7-piece fixture; join.md has no AT-01 item (lens eater-2.13 types it in-story) | **DEFECT** |
| 810 | FRD AT coverage | AT-02 6.9 g incl. 1.5 g oil → cheese 5.4 g | seed | §7.1 cheese spoon | ok |
| 811 | FRD AT coverage | AT-03 15.1 + 14.4 + 8.6 = 38.1 g | in seed or reassigned in join.md | neither: no mixed peas spoon, and no 'Rice, cooked', 'Peas with sauce' or 'Beef, cooked' Food in §8 for eater-2.17 to resolve | **DEFECT** |
| 812 | FRD AT coverage | AT-04 three dipped egg bites × 8 g bread = 24 g | seed | §7.1 egg bite (8 g bread) | ok |
| 813 | FRD AT coverage | AT-05 tuna in oil, drained vs tuna in water | seed | §7.1 tuna spoon (Mona's in-oil label) · §8.1 in-water row, no in-oil Tier A row | ok |
| 814 | FRD AT coverage | AT-06 480 kcal / 384 g; 16 g = 20; 15 = 300; 18 = 360 | seed | §7.6 Talbina v1 (recomputed above) | ok |
| 815 | FRD AT coverage | AT-08 500 kcal/100 g; 10 g piece = 50 | seed | §8.2 Biscuits, plain (recomputed above) | ok |
| 816 | FRD AT coverage | AT-09 46/32/24 → 102; 45.10/31.37/23.53 | in seed or reassigned in join.md | neither (lens eater-1.35 types it in-story) | **DEFECT** |
| 817 | FRD AT coverage | AT-10 one command delivered three times → one Entry | seed | §10.3 cmd_44c0 'Duplicates ignored 2' | ok |
| 818 | FRD AT coverage | AT-12 8 g → 9 g; yesterday stays 8 g | seed | §7.3 bread bite v2 9 g 2026-09-30T06:00Z; Sam 09-29 bread bite × 6 on v1 | ok |
| 819 | FRD AT coverage | AT-17 every composite > 30 % with its bread → Infeasible | seed | §7.6 eater-5.21 (enumerated above) | ok |
| 820 | FRD AT coverage | AT-18 shares 30/20/5/40/5 by count | seed | §7.6 eater-5.17 (counts 6/4/1/8/1) | ok |
| 821 | FRD AT coverage | AT-19 fries required + bread with every bite | seed | §7.6 eater-5.22 | ok |
| 822 | FRD AT coverage | AT-20 30.04 % vs 30.00 % | seed | §7.3 sandwich quarter (recomputed above) | ok |
| 823 | FRD AT coverage | AT-22 one workout from two feeds (+ manual entry) | seed | §10.4 Faisal (two feeds + re-send); manual entry is eater-7.11's act | ok |
| 824 | FRD AT coverage | AT-23 Target incl. 200 kcal planned exercise; import 200 kcal | seed | §5.2 tv_sam_1 (+200 planned exercise); the import is eater-7.16's act | ok |
| 825 | FRD AT coverage | AT-24 add 175 kcal cardio | seed | Sam's 10-02 Day 1,200 (P 72, C 138, F 40); the 175 kcal is eater-7.17's act | ok |
| 826 | FRD AT coverage | AT-25 five days logged, two missing | seed | §12.1 Mona week 2026-09-20 → 26 (5 of 7) | ok |
| 827 | FRD AT coverage | AT-26 '18, not 15' | seed | §7.6 Talbina 15 → 18 spoons | ok |
| 828 | FRD AT coverage | FRD §11.3 1,779 × 1.2 + 200 → 1,867.84 → 1,870 | seed | §5.2 tv_sam_1 (recomputed above) | ok |
| 829 | FRD AT coverage | FRD §13.1 meal 480 and Day 1,200 / 1,870 / 670 | seed | §12.4 Sam 2026-10-02 (recomputed above) | ok |
| 830 | FRD AT coverage | FRD §5.1 23 g tuna spoon and 6.8 g tuna in a bite | seed | §7.1 tuna spoon 23 g, tuna bite 6.8 g | ok |
| 831 | FRD AT coverage | FRD §2.6 'Those biscuits were 10 g, not 25 g' | seed | §7.3 biscuit serving 25 g; Sam 09-30 Snack | ok |
| 832 | synthetic data | E1 relay address r7k2q9x4@privaterelay.appleid.com | synthetic | random local part on Apple's real relay domain (note, not a person) | ok |
| 833 | §9 table vs §11 | grant_27b4 requested time / by / eater | 09-10 09:00 · staff_tariq · acct_d40e17 | 09-10 09:00 · staff_tariq · acct_d40e17 | ok |
| 834 | §9 table vs §11 | grant_27b4 Approved at | 09:10 | 09:10 | ok |
| 835 | §9 table vs §11 | grant_27b4 Expired at | 10:10 | 10:10 | ok |
| 836 | §9 table vs §11 | grant_40ab requested time / by / eater | 09-27 10:05 · staff_mona · acct_f1e0c3 | 09-27 10:05 · staff_mona · acct_f1e0c3 | ok |
| 837 | §9 table vs §11 | grant_40ab Unanswered at | 10:05 | 10:05 | ok |
| 838 | §9 table vs §11 | grant_a1d4 requested time / by / eater | 10-01 07:55 · staff_omar · acct_c2a917 | 10-01 07:55 · staff_omar · acct_c2a917 | ok |
| 839 | §9 table vs §11 | grant_a1d4 Approved at | 08:00 | 08:00 | ok |
| 840 | §9 table vs §11 | grant_a1d4 Expired at | 09:00 | 09:00 | ok |
| 841 | §9 table vs §11 | grant_8e20 requested time / by / eater | 10-01 08:20 · staff_omar · acct_0d4e9b | 10-01 08:20 · staff_omar · acct_0d4e9b | ok |
| 842 | §9 table vs §11 | grant_8e20 Approved at | 08:25 | 08:25 | ok |
| 843 | §9 table vs §11 | grant_8e20 Expired at | 09:25 | 09:25 | ok |
| 844 | §9 table vs §11 | grant_31f0 requested time / by / eater | 10-01 10:05 · staff_mona · acct_9c41e2 | 10-01 10:05 · staff_mona · acct_9c41e2 | ok |
| 845 | §9 table vs §11 | grant_31f0 Approved at | 10:20 | 10:20 | ok |
| 846 | §9 table vs §11 | grant_31f0 Expired at | 11:20 | 11:20 | ok |
| 847 | §9 table vs §11 | grant_40aa requested time / by / eater | 10-01 10:09 · staff_omar · acct_3f88a1 | 10-01 10:09 · staff_omar · acct_3f88a1 | ok |
| 848 | §9 table vs §11 | grant_40aa Unanswered at | 10:09 | 10:09 | ok |
| 849 | §9 table vs §11 | grant_31f9 requested time / by / eater | 10-01 11:30 · staff_mona · acct_9c41e2 | 10-01 11:30 · staff_mona · acct_9c41e2 | ok |
| 850 | §9 table vs §11 | grant_31f9 Declined at | 11:42 | 11:42 | ok |
| 851 | §9 table vs §11 | grant_52a3 requested time / by / eater | 10-01 12:00 · staff_mona · acct_f1e0c3 | 10-01 12:00 · staff_mona · acct_f1e0c3 | ok |
| 852 | §9 table vs §11 | grant_52a3 Approved at | 12:05 | 12:05 | ok |
| 853 | §9 table vs §11 | grant_52a3 Withdrawn at | 12:26 | 12:26 | ok |
| 854 | §9 table vs §11 | grant_52a7 requested time / by / eater | 10-01 13:00 · staff_mona · acct_f1e0c3 | 10-01 13:00 · staff_mona · acct_f1e0c3 | ok |
| 855 | §9 table vs §11 | grant_52a7 Approved at | 13:04 | 13:04 | ok |
| 856 | §9 table vs §11 | grant_6c10 requested time / by / eater | 10-01 14:58 · staff_mona · acct_f1e0c3 | 10-01 14:58 · staff_mona · acct_f1e0c3 | ok |
| 857 | §9 table vs §11 | grant_6c10 Approved at | 15:02 | 15:02 | ok |
| 858 | §9 table vs §11 | grant_6c10 Expired at | 16:02 | 16:02 | ok |
| 859 | §9 table vs §11 | grant_7d01 requested time / by / eater | 10-01 15:29 · staff_mona · acct_77d2c0 | 10-01 15:29 · staff_mona · acct_77d2c0 | ok |
| 860 | §9 table vs §11 | grant_7d01 Approved at | 15:30 | 15:30 | ok |
| 861 | §9 table vs §11 | grant_7d01 Expired at | 16:30 | 16:30 | ok |
| 862 | §9 table vs §11 | grant_6c14 requested time / by / eater | 10-01 15:55 · staff_mona · acct_f1e0c3 | 10-01 15:55 · staff_mona · acct_f1e0c3 | ok |
| 863 | §9 table vs §11 | grant_6c14 Unanswered at | 15:55 | 15:55 | ok |
| 864 | §9 table vs §11 | grant_9b30 requested time / by / eater | 10-01 16:55 · staff_mona · acct_a41c55 | 10-01 16:55 · staff_mona · acct_a41c55 | ok |
| 865 | §9 table vs §11 | grant_9b30 Approved at | 17:00 | 17:00 | ok |
| 866 | §9 table vs §11 | grant_9b30 Ended at | 17:25 | 17:25 | ok |
| 867 | §9 table vs §11 | grant_52c3 requested time / by / eater | 10-02 08:00 · staff_omar · acct_c2d7e5 | 10-02 08:00 · staff_omar · acct_c2d7e5 | ok |
| 868 | §9 table vs §11 | grant_52c3 Approved at | 08:05 | 08:05 | ok |
| 869 | §9 table vs §11 | grant_52c3 Withdrawn at | 08:30 | 08:30 | ok |
| 870 | §9 table vs §11 | grant_5d10 requested time / by / eater | 10-02 13:00 · staff_mona · acct_9c41e2 | 10-02 13:00 · staff_mona · acct_9c41e2 | ok |
| 871 | §9 table vs §11 | grant_5d10 Approved at | 13:02 | 13:02 | ok |
| 872 | §9 table vs §11 | grant_5d10 Ended at | 13:20 | 13:20 | ok |
| 873 | §9 table vs §11 | grant_6e21 requested time / by / eater | 10-03 09:00 · staff_omar · acct_9c41e2 | 10-03 09:00 · staff_omar · acct_9c41e2 | ok |
| 874 | §9 table vs §11 | grant_6e21 Approved at | 09:03 | 09:03 | ok |
| 875 | §9 table vs §11 | grant_6e21 Expired at | 10:03 | 10:03 | ok |
| 876 | §4.7 launch gates | provider data settings 2026-09-01 → next check 2026-11-30 (+90 days) | 2026-11-30 | 2026-11-30 | ok |
| 877 | §8.3 launch dishes | Approved 12 + In review 3 + Proposed 5 + none 30 | 50 | 50 | ok |
| 878 | §8.3 launch dishes | فول مدمس + dish_10 … dish_20 | 12 | 12 | ok |
| 879 | §8.3 launch dishes | first nine named | 9 | 9 | ok |
| 880 | §6.1 E14 | 240 Failed Analyses over 2026-09-01 → 30 with none above the daily hard limit 25 | possible | 8 a day on average ≤ 25 | ok |
| 881 | §13 B1/B2 | B1/B2 period 2026-07-01 → 09-30 vs deployment (trail start) 2026-08-01T06:00:00Z (J58) and staff role events 1–6 | inside the trail's life | starts 31 days before the deployment; §3 actors hold no role before 2026-08-01 | **DEFECT** |
| 882 | §10.1 | job_cw_4501 'AI requests until the next Consent (2026-09-25T20:10Z): 0' = event 148 | 2026-09-25 20:10 | 2026-09-25 20:10 | ok |
| 883 | §10.1 | E6 effects before withdrawal = job_cw_1814 counts (2 · 3 · 4 + 1 · 1) | equal | equal | ok |
| 884 | §4.3 | regression set: E1 research withdrawn 2026-09-22 (event 141, made 09-21T22:40Z) | 2026-09-22 | 2026-09-22 | ok |

---

## Re-check (2026-10-01)

An independent re-check of the fixes listed under "Fixes after seed-check (2026-10-01)" at the end of `way/seed.md`, and of the matching edits in `way/join.md` (commit `40e545e`: J40, J52, J54, J55, J57, J77, J104, J139). The checker wrote neither file nor the fixes.

**Method.** A new script, `recheck.py` (session scratchpad), recomputes every number the fixed lines state with `fractions.Fraction`, and every time with `zoneinfo`. It does not reuse the statements hard-coded in `/tmp/claude-0/seedcheck.py`. Food rows are parsed live from the fixed `seed.md`. The planner sets are enumerated again, and the 5.17 count sets are counted two ways (enumeration and a coin-change count). Only the changed lines were checked, against the values and events they touch: event order and time windows, Day totals, the lens lines they supersede, and FRD AT-01, AT-03, AT-09 and AT-22. The script makes 73 checks: 69 are ok and 4 are DEFECT.

**Result: fail. 4 defects (R1–R4).** Twelve of the sixteen fixes hold in full. D1, D4, D8 and D9 hold in their arithmetic, but each one leaves one statement or one lens line wrong. No owner identifier is used here. No network call was made.

### Defects and exact corrections

| # | from | where | says | recomputed / found | exact correction |
|---|---|---|---|---|---|
| R1 | D1 | `seed.md` fix log, row D1 | "The import stays at 04:30Z, after the walk and before Faisal's 07:30 Breakfast." | 04:30Z is 07:30 Riyadh, the same minute as his Breakfast (§12.2 "Breakfast 07:30"), so the import is not before it. The walk ends 45 min before the import, and the copy 46 min before. | "The import stays at 04:30Z (07:30 Riyadh), 45 min after the walk ends and at the same minute as Faisal's 07:30 Breakfast, which no longer falls inside the walk." |
| R2 | D4 | `join.md` J54 Supersedes; e578 eater-7.6 | J54 replaces eater-8.19's "81.3 (10-01, Apple Health)" with 83.5. eater-7.6 is not replaced. It reads: "Health holds 81.3 kg at 2026-10-01 06:50 … Then 81.3 kg shows … 179.2 lb". | This is the same 2026-10-01 Health sample that §5.3 sets at 83.5 kg and §10.5 now writes at 2026-10-01T06:00:00Z. 83.5 ÷ 0.45359237 = 184.0860… lb, shown **184.1 lb**. | Add to J54's Supersedes: e578 eater-7.6 "Health holds 81.3 kg at 2026-10-01 06:50 … 81.3 kg … 179.2 lb". It reads: Health holds Sam's 2026-10-01 sample of 83.5 kg (`seed.md` §5.3, written 2026-10-01T06:00:00Z), and Progress shows 83.5 kg, or 184.1 lb in lb. |
| R3 | D8 | `join.md` J55 Supersedes; e578 eater-5.44 `/m` | The §7.6 row is titled "eater-5.14 / 5.44" and now says "**3** meet every limit". J55 replaces only 5.14's `/m`. 5.44's `/m` still says "among the 12 count sets that meet every limit, 4 + 2 is the only one that is nearest on both the aim (2.4 kcal away) and the shares (3.3 points away)". | 5.44 has the same chips and limits, plus shares by count of rice 70 %. With the aim band, 3 sets meet every limit. Their distances from the shares are: 4 + 2 at 66.7 % (3.3 points), 2 + 3 (30.0) and 1 + 3 (45.0). So 4 + 2 is still nearest on both. | Add to J55's Supersedes: e578 eater-5.44 `/m` "among the 12 count sets that meet every limit". It reads "among the 3 count sets that meet every limit (4 + 2, 1 + 3, 2 + 3)", and the rest of the line holds. |
| R4 | D9 | `seed.md` §7.6 eater-5.17 row (line 374) and fix log row D9. The figure came from seed-check D9 above. | "with aim 809.7, **53** other count sets would reach it exactly" | Whole counts ≥ 0 of the five Units, with no other limit: 500a + 460b + 53c + 386d + 336e = 8,097 (in tenths of a kcal) has **170** solutions, by enumeration and by a coin-change count. Example: 2 / 4 / 1 / 10 / 4 = 100 + 184 + 5.3 + 386 + 134.4 = 809.7. No natural bound gives 53. Requiring every Unit ≥ 1 gives 76, and a total of 20 counts gives 3. 6/4/1/8/1 still misses by 22 kcal. | In both places, read "with aim 809.7, **170** other count sets (whole counts of the five Units) would reach it exactly, for example 2 / 4 / 1 / 10 / 4". Seed-check D9's "53 other count sets" reads 170 the same way. |

### Each fix, quoted and recomputed

| # | fixed text (quoted) | recomputed | result |
|---|---|---|---|
| D1 | §10.4: "the Watch walk 06:00–06:45 Riyadh (03:00–03:45Z; 210 kcal), the running app's copy 06:01–06:44 (205 kcal) and the Watch walk sent again → one Activity of 210 kcal; accepted 1, duplicate 2, conflict 0". J54: "read 06:00–06:45 and 06:01–06:44 Riyadh … the manual walk starts 06:00 and the prompt reads «هل هو نفس المشي 6:00–6:45 من Apple Health؟»" | 06:00–06:45 Riyadh = 03:00–03:45Z. The import at 04:30Z = 07:30 Riyadh comes after both walks end. The copy lies inside the Watch window, so it is a duplicate. The walk is on Day 2026-10-01 (boundary 03:00). A manual walk of 45 min from 06:00 = 06:00–06:45, which matches the prompt. The Breakfast at 07:30 is outside the walk. AT-22 holds: two feeds plus a re-send give one Activity of 210 kcal, and eater-7.11 adds the manual entry. | ok, except R1 |
| D2 | §6.1: "iPhone app 1.0.2 (last sync 2026-09-30T19:15Z, the sync that carried `cmd_7a1e`)". J57: "read "last sync 2026-09-30 20:15 your time (19:15 UTC) · 22:15 eater's time"" | 19:15Z is 20:15 Europe/Dublin (`staff_mona`, IST UTC+1) and 22:15 Riyadh. It equals the §10.3 Conflict time and the support Sync row (support.md line 450). It comes before the support clock (10-01T10:03Z) and the 1.0.3 sync (10:02Z). No §11 event comes from 1.0.2 after it; event 141 was on 09-22. | ok |
| D3 | §10.1 "Entries 216". §12.3: "(the export at 2026-09-15T12:00Z = 15:00 Riyadh already holds 15 Sep's 07:00 and 13:00 Entries: 2 + 210 + 4 = **216** Entries)". J139 "Entries 212" → 216 | 12:00Z = 15:00 Riyadh. Days 08-04 → 09-14 = 42, and 42 × 5 = 210. Day 09-15 opens at 04:00 Riyadh, and 4 of E5d's 5 Entries (07:00 × 2, 13:00 × 2) fall before 15:00. 2 + 210 + 4 = 216. The lens line is auditor.md line 801. | ok |
| D4 | §10.5: "body-mass samples of §5.3 (2026-09-17, 09-24, and 2026-10-01 written at 2026-10-01T06:00:00Z); a story that needs "no new weight since 24 Sept" (e578 eater-8.22) starts at 2026-09-30T12:00:00Z (§2)". §2 row "e578 eater-8.22 (Sam's line) · 2026-09-30T12:00:00Z" | The 8.22 clock (09-30T12:00Z) comes before the 10-01 write (06:00Z), which comes before the eater default clock (09:00Z). The 09-24 sample is loaded at the 8.22 clock. The body-mass Consent (09-16) comes before every sample. | ok, except R2 |
| D5 | §2: "every 30 Sep Day past its boundary"; "the week 2026-09-20 → 26 has ended" | The latest end of any 30 Sep Day is E1's: 10-01 04:00 Riyadh = 01:00Z, before 09:00Z. 1 Oct 2026 is a Thursday. Mona's Day 09-26 ends at 09-27T00:00Z, before the 8.12 clock at 09:00Z. | ok |
| D6 | §3 list "… · Sign launch gates · Publish wording." Nutrition approver "· Publish wording (`guidance-1`, the tracking-only guidance; approver-10.53)". Platform admin "· Publish wording (consent texts, `grant-req-1`; after the privacy review is signed)". J40 the same | Event 144 (`launch_gate.signed`, 09-24 07:55) comes before 145 (`wording.published` c-ai-4, 08:00). `staff_ali` has been Platform admin since 08-01T06:00Z. `events.md` §2.7 names the same three kinds of text, and `model.md` E12/G6 already use the permission. | ok |
| D7 | §0 item 4: "USDA FoodData Central (serves §8.1, with releases 15.4 and 15.5, and the import of §10.7)" | §8.1 holds both releases and §10.7 the 15.5 import. Outside the fix log, no "§8.7" remains. | ok |
| D8 | §7.6: "12 count sets meet the ceiling, carbohydrate and Available limits; with the aim band, **3** meet every limit: 4 + 2 (397.6 kcal, 2.4 from 400), 1 + 3 (384.4, 15.6 away), 2 + 3 (426.8, 26.8 away) → **4 rice + 2 chicken = 397.6 kcal**, carbohydrate 112 ÷ 397.6 = 28.17 %; next nearest 1 rice + 3 chicken (384.4); 5 + 2 (440.0 kcal, 31.82 %) is never returned". J55 supersedes 5.14 `/m` | Kabsa spoon 4/4/9 = 212/5. The carbohydrate limit means 15.28 r ≤ 34.2 c. With c ≤ 3 and ≤ 500 kcal, there are 12 sets; inside 360–440 there are 3, at 2.4, 15.6 and 26.8. 4 + 2 gives 112/397.6 = 28.17 %. 5 + 2 gives 440.0 and 140/440 = 31.82 %. | ok, except R3 |
| D9 | §7.6: "the `/m` line reads "Calorie aim about **831.7** with tolerance 0" → 6 / 4 / 1 / 8 / 1, the only zero-deviation answer (with aim 809.7, 53 other count sets would reach it exactly and 6/4/1/8/1 would miss by 22 kcal)". J55 supersedes 5.17 `/m` "809.7" (read 831.7) | 300 + 184 + 5.3 + 308.8 + 33.6 = 831.7. The calorie shares, 36.0707 · 22.1234 · 0.6372 · 37.1288 · 4.0399, round by largest remainder to 36.1 · 22.1 · 0.6 · 37.1 · 4.1 (sum 100.0). Only multiples of 6/4/1/8/1 meet 30/20/5/40/5 exactly, so 6/4/1/8/1 is the unique zero-deviation answer at 831.7. On calories alone, 181 sets reach 831.7, so the uniqueness rests on the shares. The 22 kcal miss holds. | ok, except R4 |
| D10 | J104: "1,870 × 2,134.8 ÷ 2,334.8 = 9,980,190/5,837 = 1,709.81…, shown 1,710; the gap reads "−19.9 %"". The Supersedes adds e19 eater-1.40 `/m` "1,709.78" | 1,779 × 1.2 = 2,134.8. The product is exactly 9,980,190/5,837 = 1,709.8149…, shown 1,710. The gap 1 − 1,870/2,334.8 = 19.907 %, shown 19.9 %. The lens line is wf1-wf9.md line 443. | ok |
| D11 | §8.6: "**Tier A rows are not cross-checked** (J77) … Date (generic) (282 vs 4/4/9 313.31, gap 31.31 kcal, 11.10 %), Cumin, ground (400 vs 446, gap 46 kcal, 11.5 %) and Falafel (333 vs 340.6) open no flag. The check runs over … Label submissions, approver-approved Foods (§8.2) and Tier B recipe records." J77 the same | Date: 9.8 + 300 + 3.51 = 313.31, and 31.31/282 = 11.10 %. Falafel: 340.6. Each in-scope row was recomputed under v1 (§8.2 rows, including the new Peas with sauce at 0.89 % and Dates, Saqai at 3.87 %; `rec_fm_eg` 2,038.88 vs 1,960 at 4.02 %; L-15 at 21.1 %). Exactly Date-filled biscuit, Basbousa (11.05 %), Ma'amoul (13.49 %) and L-15 meet it. That gives Energy mismatch 4 and, with 5 analogues, 9 open flags, as stated. J77 agrees with approver-10.14, whose check runs over Approved Foods. | ok |
| D12 | the same text, for Cumin, ground | 72 + 176 + 198 = 446, and 46/400 = 11.5 %. It is a Tier A row, so it raises no flag. | ok |
| D13 | §13: "2026-08-01T06:00:00Z (the deployment) → 2026-09-30, actors from §3 acting only while they hold their roles" | This equals the §2 deployment time. The old start was 31 days earlier. The auditor lens's own B1 period falls under J52's "every lens fixture table". | ok |
| D14 | §5.3 AT-01: "7 pieces weigh 71.7 g after tare; single weights 9.8 · 10.1 · 10.6 · 10.0 · 10.4 · 10.3 · 10.5 g (sum 71.7); mean 717/70 g = 10.242857… g stored, shown 10.24 g; count 7 kept; one piece = 717/14 kcal = 51.2142… kcal (500 kcal per 100 g)" | The single weights sum to 71.7. 71.7/7 = 717/70 = 10.242857…, shown 10.24. On Biscuits, plain (500 kcal per 100 g), one piece = 717/14 kcal. The range 9.8–10.6 matches eater-2.13. This is FRD AT-01 exactly. | ok |
| D15 | §5.3 AT-03: "Rice, cooked 15.1 g (19.63 kcal; P 0.4077, C 4.2582, F 0.0453) + Peas with sauce 14.4 g (12.96; 0.648, 1.584, 0.4608) + Beef, cooked 8.6 g (21.5; 2.236, 0, 1.3244) → total mass 38.1 g; 54.09 kcal, P 3.2917, C 5.8422, F 1.8305". Rows §8.1 "130 · 2.7 · 28.2 · 0.3", "250 · 26 · 0 · 15.4"; §8.2 "90 · 4.5 · 11 · 3.2" | Every part and every total recomputes exactly from the live rows, and the mass is 38.1. The 4/4/9 values are 126.3, 90.8 and 242.6. This is FRD AT-03 (each part counted once). | ok (see note 2) |
| D16 | §5.3 AT-09: "46/102 = 45.0980…, 32/102 = 31.3725…, 24/102 = 23.5294… %, shown by largest remainder at two decimals **45.10 · 31.37 · 23.53** (sum 100.00); grams at 1,480 kcal: fat 34,040/459 = 74.16… → 74 g, carbohydrate 5,920/51 = 116.08… → 116 g, protein 1,480/17 = 87.06… → 87 g" | The largest remainder lifts 23.52 and 45.09, giving 45.10 · 31.37 · 23.53 (sum 100.00). The gram fractions recompute exactly, as 74, 116 and 87, matching eater-1.35. Hala's O1: 1,449 × 1.2 × 0.85 = 1,477.98, shown 1,480. This is FRD AT-09 and FRD line 59. | ok |

### Notes (checked; not counted as defects)

1. **The fix log's closing paragraph** says the old script found "883 values checked … 12 remaining DEFECT rows". Rerun on the committed files, it finds **885 values and 14 DEFECT rows**: the 12 old statements it hard-codes, and 2 cross-reference rows. Those 2 come from the fix log's own text, which mentions "support §0.3" and the old "§8.7". Read: "885 values; 14 DEFECT rows: 12 restate the old text, 2 are cross-reference rows raised by this table's own mentions of support §0.3 and §8.7".
2. **"Each is within 3 % of its 4/4/9"** (fix log, row D15). This holds when the gap is measured against the source energy, as the Policy measures it: Rice, cooked 3.7 kcal (2.85 %), Peas with sauce 0.8 kcal (0.89 %), Beef, cooked 7.4 kcal (2.96 %). Measured against the 4/4/9 value, Beef, cooked is 7.4 ÷ 242.6 = 3.05 %. "Each gap is under 3 % of its source energy" says it exactly.
3. **eater-7.11's English gloss** ("Is this the same walk as 7:00–7:45 from Apple Health?") follows the Arabic prompt that J54 supersedes. It reads 6:00–6:45.
4. **Wording dated 2026-08-01** (`age-1`, `diary-1`, `health-1`, `mic-1`, `photos-1`, `research-1`, `label-1`, `grant-req-1`, `guidance-1`) has no `wording.published` event in §11 and no recorded privacy review. J26 and the new "Publish wording" line say every publish writes the event, after the review for consent texts. This predates D6. One way to settle it: say these were "seeded with the deployment (no event)".
5. **"The week 2026-09-20 → 26"** (§2, D5) runs Sunday to Saturday. Mona's first day of week is Saturday (§5.1), so it is a 7-day span rather than her calendar week. The text ("has ended") is true either way.
