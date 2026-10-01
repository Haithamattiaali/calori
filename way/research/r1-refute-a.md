# R1 · Refutation A: competitors (C) and food sources (F)

Refuter, research cycle 1. Checked 2026-10-01 against `r1-competitors.md` (C1–C58) and `r1-food-sources.md` (F1–F35).

**How this was checked.** At first only GitHub was reachable. Partway through, the session got wider network access, so `curl` reached most primary hosts: fdc.nal.usda.gov, sfda.gov.sa, fao.org, apps.apple.com, the vendor sites, Zendesk help-centre APIs, Europe PMC and Crossref. WebFetch itself was still blocked for those hosts. Some hosts stayed closed:
- behind Cloudflare or a captcha: blog.myfitnesspal.com, support.myfitnesspal.com HTML, help.yazio.com HTML, tandfonline, sciencedirect, researchgate, pmc.ncbi;
- other errors: globenewswire (empty reply), mdpi (403), fd.sfda.gov.sa (TLS), ul.edu.lb (TLS), mwm.ai article (410), marlvel.ai (403), trygaya (525).

Each quote below is page text I fetched myself in this run.

**Verdicts.**
- `stands`: the source says it.
- `stands (now opened)`: it was an assumption, and a source has now been opened that supports it.
- `stands, part dropped`: the main claim holds. The named sub-claims are dropped (see "Dropped").
- `refuted`: the source says otherwise.
- `doubtful`: the claim goes beyond the source, the quote is not on the opened page, or the source is compromised.
- `assumption (unverified)`: no primary source could be opened.

The counts treat `stands, part dropped` as stands.

**Totals (93 findings):** stands 78 · refuted 3 · doubtful 8 · assumption 4.

---

## C · Competitors

| id | verdict | evidence |
|---|---|---|
| C1 | stands, part dropped | news.myfitnesspal.com (2026-03-02): "MyFitnessPal has acquired Cal AI" … "Cal AI will continue operating as a standalone product". thenextweb (2026-03-03): "operate as a separate product … with access to MyFitnessPal's extensive nutritional database". Neither page has "closed in December 2025" or "20 million foods, 68,500 brands, 380+". MFP's own help gives "19 million foods" (support.myfitnesspal.com article 360032273292). Gist quote checked; the raw gist really does cite no source. |
| C2 | stands, part dropped | Syndicated GlobeNewswire text (compuserve.com/news/story/0022/20260224/9659625.htm, 2026-02-24): "Photo Upload feature allows iOS users to take a photo … log their meal later"; Premium+ "Recipes tab"; "Premium ($79.99/year), and Premium+ ($99.99/year)". The release has **no GLP-1 item**. GLP-1 launched separately on 2026-04-28 as "a free feature available to all users" (finance.yahoo.com copy of the PR). "US iOS only" is not in the text. |
| C3 | stands (now opened) | news.myfitnesspal.com/how-myfitnesspals-2026-summer-release… (2026-08-25): "AI Coach gives Premium and Premium+ members on iOS personalized nutrition guidance grounded in their own logging history" … "The Plan Tab … All members can browse recipes … log a recipe … with one tap". Note: AI Coach first launched 2026-06-10 (MFP press release). |
| C4 | stands, part dropped | piunikaweb 2026-04-24: "replaces the app's longtime Diary tab with a new 'Today' screen" … "basic tasks now take more taps" … "'the path forward' and there is no option to revert". piunikaweb 2026-05-05: "here to stay". The mwm.ai source returns HTTP 410. "6–10 vs 2–3 taps", "item copy removed", "3.24 → 1.54 rating" and "v26.16.0" appear on no opened page. |
| C5 | stands, part dropped | MFP help via Zendesk API, article 360032625331 (updated 2026-09-26): "My Meals lets you save a group of foods you eat together and log them all at once". Article 360032622131 (2026-08-26): Edit → "Copy" items "and select a date". The swipe-to-"bring forward" (Quick Log) is not in current help. |
| C6 | doubtful | MFP help article 360032272852 (2026-09-24) supports the fraction workaround ("enter 0.75 as the number of servings"). It also offers reusable personal portions: "save it as a remembered meal … locks in the adjusted amount for next time" and "create a new food item with the exact portion size you need". So "no personal portion unit" goes too far. The weighed-pot workaround is confirmed (workingagainstgravity.com: "change the serving size … to be the exact amount of grams that your recipe ended up weighing"). |
| C7 | stands, part dropped | pocket-lint (2022-08-25): "Starting 1 October 2022, you will need to subscribe to MyFitnessPal Premium". MFP Premium features (article 360032625951, 2026-09-18) lists Barcode Scanner, Meal Scan and Voice Logging. Prices as in C2. "Most-cited reason people leave" has no source. |
| C8 | doubtful | blog.myfitnesspal.com/voice-logging is behind a Cloudflare challenge, so the voice-log limits are not checked. The 71.2% / ±18% figures come from ai-food-tracker.com, which ranks "PlateLens (#1)" and compares every app to PlateLens (same network as C50). A peer-reviewed study contradicts it: Nutrients 2024 (PMC11314244) found "MyFitnessPal and Fastic had the highest accuracy (97% and 92%)" among seven AI image apps. |
| C9 | stands, part dropped | MFP help article 360032273292 (2026-09-21): a check mark "shows that a food … has been reviewed or added by MyFitnessPal"; "report the food so our team can update it". "Largely user-submitted, not systematically verified" (blog) is not opened. |
| C10 | stands (weak) | MFP help article 360032624311 (2026-08-27): delete is a swipe and trash can; no restore is mentioned. The Undo window comes only from a competitor blog (nutrola, opened): "'Undo' — only available for about 30 seconds after the delete". The community thread renders by JS and could not be read. |
| C11 | stands (now opened) | MFP help article 360032625951: "Macronutrients by Gram", "Different Goals by Day", "Calorie Goals By Meal", "Unlimited Weekly Digests", "Data Export" are all listed as Premium. |
| C12 | stands, part dropped | nutrola (opened; competitor blog): "MyFitnessPal writes nutrition data to Apple Health … Weight data is bidirectional"; "Cronometer writes detailed micronutrient data to Apple Health". The words "batched" and "deepest" are not on the page. The Lose It! App Store page lists "Healthkit" sync under **Premium**. |
| C13 | stands, part dropped | nutrola (opened): "MyFitnessPal discontinued its Apple Watch app several years ago". The Siri part conflicts with another nutrola page (see C54), which says MFP has limited Siri Shortcuts, so it is dropped. |
| C14 | stands (now opened) | MFP help: "Today's Calories or Today's Macros" widgets; "Interactive Water Widget for iOS … iOS 18 or later" (article 6886354417165). The Today tab adds "a Streaks view" and "Healthy Habits section for water" (39985611667341). "Intermittent fasting is a MyFitnessPal Premium feature" (10983207647117). |
| C15 | stands, part dropped | MFP help article 360032622851 (2026-08-27): "you will not be able to access the food database"; offline changes "will not appear … until you … synchronize". "Recents / custom foods / quick add work offline" is not in the current text. |
| C16 | stands, part refuted | App Store language lists, fetched 2026-10-01. MFP (id341232718): "English and 19 more", **no Arabic**. Lose It! (id297368629, US and the FI store that was cited): "English and 7 more", **no Arabic**, so the Lose It! Arabic excerpt is refuted. nutrola's claim of "partial support" for MFP is not backed by MFP's App Store localisations. |
| C17 | stands (now opened) | newswire.com (2025-04-22): "6% More Weight Loss / 3.5x Faster Meal Logging / Twice as Many Foods Logged". Company data. |
| C18 | stands (now opened) | Lose It! App Store, under "PREMIUM PLAN FEATURES": "Photo Meal Logging", "AI Voice", "Barcode Scanner". The source conflict is settled: nutrola's "free tier includes barcode" is wrong. |
| C19 | stands, part dropped | eathealthy365 (opened): "a field called Recipe Makes" … "weigh the entire finished meal … Enter this number … into the Recipe Makes". "Previous meals, one tap" was not opened (nutri.it.com 403). |
| C20 | stands, part dropped | Lose It! App Store: "NEW—GLP-1 SUPPORT", "Intermittent Fasting" (Premium), platforms "iPhone, iPad, Apple Watch". nutrola (opened): "Water logging directly from the wrist / Quick-log for a limited set of recent foods". "Free daily/weekly summaries; Patterns Premium" was not opened. |
| C21 | stands | nutrola (opened; competitor blog): "full-screen interstitial ads interrupting logging flows". The Lose It! App Store lists macronutrient tracking ("Advanced Tracking") as Premium. |
| C22 | stands (now opened) | Cronometer help via Zendesk API, article 360018510311 (2026-09-25): "Servings Based … Weight Based", "+ Add Serving Size", "'Set Cooked Recipe Weight' to enter in the actual weight of your recipe". |
| C23 | stands (now opened) | Cronometer help article 360019003171: "Copy Previous day Automatically copy the previous day's diary entries (Not including imported activity and biometrics)"; "Add to Favorites". |
| C24 | stands (now opened) | prnewswire (Sept 8, 2025): "New Gold-only feature lets users snap their meals, review AI suggestions, and log verified nutrition data". |
| C25 | stands (now opened) | Cronometer help article 26644592588692: "view today's Energy Summary, Nutrition Scores … reminders for logging foods and fasting". Article 4407693442324 lists widgets. cronometer.com/blog/fasting: "Fasting Timer (Gold Feature)". |
| C26 | doubtful | forums.cronometer.com threads 5147 (May 2022) and 5429 (Nov 2022) are user feature requests, still open in March 2025. Neither contains "license agreements won't allow them … stored on your device". No official Cronometer statement on offline mode was found. |
| C27 | doubtful | Cronometer's own Account Settings (article 360018760151, 2026-09-25) and Edit Diary Entries (360018031812) have **no Trash**. They say "Bulk Delete … deleted data cannot be recovered". The 7-day Trash is stated only by a competitor blog. |
| C28 | stands, part dropped | garagegymreviews (updated 2026-09-15): "Best Overall Calorie Counter App: Cronometer", "highly accurate food database", "Free version has ads", "very dense". The source for the video ads that "hijack … half a minute" (marlvel.ai) returns 403. |
| C29 | stands (now opened) | X post (2026-02-16, via the syndication endpoint): "Cronometer is now also in Spanish … in app Language options for German, French and English (US & UK)". The App Store lists English only. No Arabic either way. |
| C30 | stands | github.com/NicholasTamm/Macro-tracker/issues/1, opened 2026-09-27, verified 2026-09-23. Quotes match; "Seven-day trial; no free subscription tier; US$11.99/month". |
| C31 | stands, part dropped | macrofactor.com/expenditure-v3: the algorithm "adjust[s] your weekly Calorie and macronutrient targets" using intake and weight. "2–3 weeks to settle" (amyfoodjournal) was not opened. |
| C32 | stands (now opened) | macrofactor.com/mm-may-2026: "two modes: Snap and Describe … one or more photos and/or text". annual-report-2026: "snap a photo of a written recipe in a cookbook"; "Label Scanner … across different formats and languages"; "photo of the front of the product and another of the nutrition label". |
| C33 | stands (now opened) | macrofactor.com/apple-watch: "speak naturally, for example 'coffee with a croissant' … an editable suggestion you can confirm in a tap". |
| C34 | stands, part dropped | macrofactor.com/fastest-food-logger-2025: search case "MacroFactor (speed): 10 … LoseIt: 13 … MyFitnessPal: 15"; barcode "MacroFactor (speed): 5 … MyFitnessPal: 7, Cronometer: 7 … LoseIt: 7"; quick-add "3"; 21 apps listed. MF's total of 24 equals the sum of its case scores. "Cronometer 40 total" is not in the page text. Vendor-run. |
| C35 | stands (now opened) | help.macrofactorapp.com article 28: "MacroFactor will likely never support a true offline mode" … "Food search of history foods … cached online results … Adding, editing, and deleting of any data". |
| C36 | stands (now opened) | nutrola (opened): "Price is the single most-raised concern"; "deep respect for the adaptive algorithm". MacroFactor annual-report-2026: "officially be available in three languages: English, Japanese, and German". The App Store currently shows "English and Japanese". No Arabic. |
| C37 | stands, part dropped | calai.app: "Snap a photo, scan a barcode, or describe your meal"; "your phone's depth sensor calculates food volume"; "Connect with Apple Health". The App Store has a "Streak Restore" in-app purchase. Milestones tab, water tracking and deficit calculator are unverified. |
| C38 | stands, part dropped | Founder's X post (2026-03-02): "In just 18 months … we broke $50m in ARR". MFP: "over $40 million in sales in the last 12 months". TNW: "more than 15 million downloads". eesel.ai: "There's a 3-day free trial … requires payment details upfront". App Store: "FOOD SCANNING ANALYSIS RESULTS REQUIRE A SUBSCRIPTION". "$770K/month on ads" comes only from an unsourced gist. |
| C39 | doubtful | Both pages opened (getkalohealth, tooldirectory.ai), but neither contains "Edit Items" or "available offline". |
| C40 | stands, part dropped | justuseapp review: "18 ounce steak it will show me a calorie count of 23,000". eesel.ai: billing complaints and trial terms. "25–50% under-count" and "zero citations" (trygaya, HTTP 525) are not verified. |
| C41 | stands (now opened) | Cal AI App Store: "English, Arabic, Azerbaijani … Spanish" ("English and 14 more"). RTL quality is not shown. |
| C42 | stands, part dropped | snapcalorie.com/faq.html: "around 15% mean caloric error"; "scans to volume of the food with your phone's depth sensor". The homepage has "Voice Notes". "3 AI logs a day" and "paid dietitian review" come only from a competitor blog (nutriscan); the official site says "Core features are completely free". |
| C43 | stands (now opened) | Nutrition5k README quotes confirmed (CVPR 2021, authors include Norris; dataset CC 4.0). ycombinator.com/companies/snapcalorie: "Wade Norris, Founder … Founded SnapCalorie … published first algorithm in … CVPR". |
| C44 | stands, part dropped | Yazio App Store title: "AI Calorie Tracker by Yazio"; "Water tracker with reminders"; recipes. bestcalorieapps: "16:8, 18:6, 20:4, 5:2 … fasting streak tracking". The "±20%" figure comes from bestcalorieapps, which benchmarks everything against PlateLens; dropped. "Late 2025" is not verified. |
| C45 | stands (now opened) | Yazio help via Zendesk API, article 360016755618 (2026-09-27): "add the recipe to your Diary by servings or by weight in grams". The favourites article requires login. |
| C46 | assumption (unverified) | Yazio help article 360000582838 is **no longer public**: the Zendesk API returns "Couldn't authenticate you" and the HTML is behind Cloudflare. The App Store text does not mention Siri. |
| C47 | refuted | Yazio App Store, both the **SA** storefront (the one cited) and the US one: "English and 19 more … Turkish". **No Arabic.** |
| C48 | stands | sikayetvar.com/en/yazio-us (opened; the name is auto-translated as "Yazıyor"): "canceled it about a week later … the annual fee was deducted". Low authority. |
| C49 | stands, part dropped | Nutrients 2024, abstract via Europe PMC (PMC11314244): "automatic energy estimations from AI … were inaccurate … especially for mixed dishes and culturally diverse foods". The "≈30% → ≈14% with ingredients" figure (fitia, HTTP 429) is not verified. |
| C50 | doubtful | foodvision-bench README (opened). The cited figures are in its table, but the README contradicts itself: MacroFactor "4.9%" in the table and "±4.8%" in the text; Cronometer 6.7 vs 6.6; PlateLens manual 3.3 vs 3.2. Its cuisine buckets add up to 189, not 231. Every summary says "PlateLens is the most accurate". "Directional, not definitive" refers to per-cuisine results only. Treat it as PlateLens marketing. |
| C51 | stands | cronofree README (raw): its table rows for MyFitnessPal, Cronometer, MacroFactor, Lose It! and Yazio match the claim, including "recipe importer" and "fasting timer". |
| C52 | stands, part dropped | kamcalorie.app and its App Store page confirm voice in Arabic and English and the Egyptian and Gulf dishes. kiloappsa.com: "151,000+ Foods, 4,000+ Brands", menus for Albaik, Herfy and McDonald's; **"10,000+ menu items" is not on the site**. The zorest blog was opened. "Saaraty" is not verified. |
| C53 | stands (now opened) | swoodie (opened): "Cronometer — the most generous export here, and it is free"; MacroFactor "paid by construction". MFP help lists "Data Export" under Premium. Cronometer help shows a CSV export with no stated gate. |
| C54 | refuted | The same nutrola URL now says: "MyFitnessPal — Siri Shortcuts (Limited) … log frequently eaten meals"; "Lose It! has a more developed Siri Shortcuts integration … voice commands to log specific foods". The source is unreliable in both directions: it also says MFP lacks in-app voice, which contradicts MFP's Voice Log. |
| C55 | stands | developer.apple.com/health-fitness: "map actions like starting a workout, logging nutrition … to a shortcut"; "display an innocuous summary". |
| C56 | stands | Apple docs JSON: "A quantity sample type that measures the amount of energy consumed." |
| C57 | assumption (unverified) | This is a claim of absence; no source can prove it. Near misses seen in this run: MFP AI Coach "suggests exactly what to eat and how much" (C3), and Loqma "goal plan computed with real math". Neither is a table-photo solver. |
| C58 | stands (now opened) | Lose It! App Store: "Log your medication, view estimated GLP-1 levels". MFP GLP-1 Support launched 2026-04-28. |

## F · Food sources

| id | verdict | evidence |
|---|---|---|
| F1 | stands (now opened, primary) | fdc.nal.usda.gov/api-guide: "public domain and they are not copyrighted. They are published under CC0 1.0 Universal"; "No permission is needed … we request that users list FoodData Central as the source"; the citation matches. |
| F2 | stands (now opened, primary) | api-guide: "a data.gov API key must be incorporated into each API request". Endpoints listed: `/food/{fdcId}`, `/foods`, `/foods/list`, `/foods/search`. |
| F3 | stands (now opened, primary) | api-guide: "1,000 requests per hour per IP address … API key to be temporarily blocked for 1 hour"; "DEMO_KEY … Hourly Limit: 30 … Daily Limit: 50"; "Contact FoodData Central if a higher request rate setting is needed". fdc_api.yaml: "up to 20 FDC IDs", `maxItems: 20`. |
| F4 | stands | eiz/fooddb data-dictionary: "What We Eat In America", "April 2018 release", "food label data provided by food brand owners", "GDSN or Label Insight", `market_country`. Saved FDC page: FNDDS "updated every two years". |
| F5 | stands (now opened) | ahmed-hassan19 `nutrition-sources.json` (reviewedAt 2026-08-26): all 8 IDs and labels match exactly. ARS factsheet: "Primary descriptions for 5,432 foods/beverages". Ingredient values come from "USDA FoodData Central … or other sources", not specifically Foundation and SR Legacy. The FDC log confirms FNDDS 2021–2023 is still the current release. |
| F6 | stands (now opened), outdated | fdc.nal.usda.gov/log: "December 18, 2025 - … Version 14.0"; 14.1 to 14.3 in Jan–Mar 2026. The planned April release shipped as **15.0 on 2026-04-30** with Foundation updates. The latest is **15.5 (2026-09-24)**. |
| F7 | stands | OFF docs (raw): "available under the Open Database License"; "Database Contents License"; images "Creative Commons Attribution ShareAlike … may contain graphical elements subject to copyright". |
| F8 | stands (primary) | opendatacommons.org/licenses/odbl/1-0: §4.3a notice text; §4.4b "Extraction or Re-utilisation of the whole or a Substantial part … is a Derivative Database"; §4.6 "entire Derivative Database; or … alterations". Also relevant: §4.5a, a Collective Database need not be ODbL. That supports keeping OFF in a separate store. |
| F9 | stands | OFF api/index.md: "15 req/min/IP … 10 req/min/IP … don't use it for a search-as-you-type"; "rate limits apply per user"; "download the data as a CSV or JSONL"; "AppName/Version (ContactEmail)"; reads need no authentication. |
| F10 | refuted (figures superseded) | Live OFF search, 2026-10-01: Saudi-tagged **count 16,723** (cgi/search.pl, countries = saudi-arabia). Egypt-tagged **count 4,406** (v2 search and cgi search agree), so Egypt is no longer "unknown". The 14,797 figure is out of date. |
| F11 | stands | Recount of the raw taxonomies: categories `ar:` 170 / `en:` 9,254; ingredients `ar:` 607 / `en:` 4,834. None of the six words appear in either file. Ajwa and Medjool appear in English only. Mapping check: `ar: لبن` sits on Yogurts, `ar: حليب رائب, حليبب` on buttermilk (ingredients file), `ar: فول` on broad bean. The taxonomy licence is still open: the taxonomy README states none, and the repo LICENSE is AGPL-3.0 (code). |
| F12 | stands (now opened) | fao.org INFOODS page "(Egypt, 1996) Food Composition Tables for Egypt": "Print-only", "Printed book"; "National Nutrition Institute (NNI). 2006. Food composition tables for Egypt. Cairo, NNI." No licence is shown. "115 pages" was not seen. |
| F13 | stands | 3bud-ZC manifest: `"foodCount":470,"categoryCount":15,"basisGrams":100`. All 8 kcal values match the CSVs. README: "built from the supplied Food Composition Tables for Egypt workbook". |
| F14 | stands, part corrected | CSV: "Barley,grains",88,335; "Milk chocolate",81.2,80. The README says the workbook has **one** `T` (trace) value, not "some". Issue #16 (2026-09-14, via WebFetch): "3 rows with unrecoverable corruption … 9 rows with systematic transcription errors … corrected 7 … dropped 2"; "20 hand-typed entries with USDA-typical estimated nutrient values". |
| F15 | stands (now opened) | Kaggle API for mernamohamed3/egyptian-food: `"licenseNameNullable":"Attribution 4.0 International (CC BY 4.0)"`, "470 food items", sourced from NNI 2nd ed. (2006) via a **Scribd copy**. Nothing grants an NNI licence. That NNI holds the rights is still an assumption (see the high-impact list). |
| F16 | stands, part corrected | Research Square preprint rs-11073271 (CC BY 4.0): "2,818 food items covering 322 nutrients … 18 main groups, 226 subgroups"; "Food Confidence Determination Score System". **Launch date corrected:** SFDA news 18975 is dated **2026-03-31** and news 19370 is dated **2026-08-30**, not 2024–25. The confidence level names were not seen. fd.sfda.gov.sa did not answer (TLS failure / 403). |
| F17 | stands (now opened) | SFCT-E.pdf (311 pp.): "130 Saudi dishes"; "three independent replicates"; "minimum of twelve food samples"; "49 nutritional components"; "nearly 19,000" analyses. saudigazette (June 25, 2026) gives the release date. Rights: the book has no licence text. The quoted "All rights reserved © … SFDA © 2026" is the **site footer**. SFDA Terms of Use: "All rights reserved to the SFDA … the information and materials contained in these pages … for your personal use". The book is not on SFDA's open-data list. Written permission is needed. |
| F18 | doubtful | nabdh PLAN.md (opened) says "no public API yet … contact SFDA to license/obtain it". The same plan dates the launch to "2024-25", which is wrong (see F16). SFDA's 2026-08-30 news says the database makes "data accessible for developers to build digital tools". Whether there is an API is unknown. |
| F19 | stands (now opened) | Europe PMC full text, PMC12641437 (Front Nutr, 2025-11-10): "25 commonly consumed traditional dishes from five Saudi Arabian regions using … ESHA"; "Areekah (306.9 kcal) … Margoug (89.2 kcal)"; Haneeth, Jareesh, Hininy, Kabsah and Saleeq are present. **Licence: CC BY 4.0**, so the values can be reused with attribution. |
| F20 | stands (now opened, secondary) | Frontiers in Nutrition 2023 (10.3389/fnut.2023.1281293): "voluntary pledges in 2017, and it was enforced in 2019 … all restaurants … post caloric information". gulfnews (2025-07-02): "came into effect on July 1 … including food delivery apps"; salt, caffeine and calorie-burn labels. The exact date 2019-01-01 was not seen. |
| F21 | assumption (unverified) | The FAO Bahrain table PDF was opened (150 pp., with a "Composition of Composite Dishes Consumed in Bahrain" section). The GULFOODS / Musaiger 2006 entry itself was not opened (researchgate blocks it). Low impact: cross-check only. |
| F22 | stands (now opened) | White Rose eprint 175374 (paper CC BY): "2016 food items"; "120 nutrients"; "79 % (n = 1585)" CoFID; "13 % (n = 271)" local back-of-pack labels; "8% (n = 160)" composites; Saudi Arabia and Kuwait. Authors "involved in Dietary Assessment Ltd." "Licensable, no API" stays second-hand (nabdh). |
| F23 | stands (now opened) | fao.org/4/x6879e: "FAO FOOD AND NUTRITION PAPER 26 … Rome 1982", joint with USDA; tables I–III; 14 food classes in Table I; "Index of Common Names of Foods". **Licence: "All rights reserved. No part of this publication may be reproduced … without the prior permission"**. |
| F24 | stands, part unverified | Oman: Foods 2024 (PMC10930989) "An Update of the Food Composition Table". UAE: Frontiers 2026-08-20, "food composition data from the United States Department of Agriculture (USDA), regional, and local sources". Lebanon PDF: TLS certificate error, not opened. |
| F25 | stands, part corrected | platform.fatsecret.com/docs/guides/localization: "Localization is a premium feature only made available to select accounts"; the region table has "SA Saudi Arabia Arabic". That table lists about 212 region codes, so it does not prove a Saudi-specific dataset. **Coverage numbers corrected:** FatSecret's site now says "over 62 countries", "26 languages", "2.3 million". nabdh's "only provider" is an opinion. |
| F26 | stands (now opened) | wikidata.org Licensing: "All structured data (i.e. the main, Property, Lexeme, and EntitySchema namespaces) is released … under Creative Commons Zero". The API confirms the IDs and **dialect labels**: Q188788 `arz` "طعميه"; Q383389 `arz` "طحينه", `acm` "راشي". All 4 IDs appear as `wikidata:en:` in the OFF taxonomies. |
| F27 | stands (now opened) | arabic-buddy gulf.md: "لبن *laban* (buttermilk)". en.wiktionary لبن: Egyptian Arabic "# [[milk]]"; Gulf Arabic "leban, coagulated sour milk; yogurt drink"; Hijazi the same. |
| F28 | stands | The CSVs contain "foal medames", "taamia", "Baladi", "Agwa", "Bisara", "Menabet", "Nabet", "Termis", "Karesh", "MISH", "Kishk", "fiteer,meshaltet" and "Homos sham". |
| F29 | stands | GitHub code search, 2026-10-01: `"طعمية" "فول مدمس" calories` → total_count 65 (e.g. utils/egyptianFoods.ts, data.js). |
| F30 | assumption (unverified) | Crossref and Semantic Scholar confirm the paper (IJFP 2021, CC BY). The **lead affiliation is GC University Faisalabad, Pakistan**; 3 of 10 authors are from KSU, so "a review from King Saud University" overstates it. The 11.9% / 386.74 kcal values are behind Cloudflare and not seen. Low impact: FRD §5.2 already requires the prepared recipe. |
| F31 | doubtful | Semantic Scholar abstract (Assirey 2015, CC BY-NC-ND): 10 Saudi cultivars, "rich in sugar (71.2–81.4% dry weight)". That is **81.4, not 81.6, and on a dry-weight basis**. That Sukkari and Suqaey are among the cultivars is not confirmed (full text behind captcha). |
| F32 | stands, part refuted | Kam App Store (v1.1.5): voice "in Arabic or English"; "Egyptian: koshari, ful medames, ta'meya, molokhia, mahshi, feteer"; "Saudi & Gulf: kabsa, mandi, haneeth, jareesh, matazeez, saleeg"; "Detects cooking methods — grilled vs fried changes the calories". **"Up to 20 seconds per recording" is refuted:** the site shows "20s / Average Logging Time". App Store localisation: "English" only. |
| F33 | stands, part dropped | kiloappsa.com: "The Smart Calorie Tracker for Saudi Arabia"; menus for "Albaik … Herfy … McDonald's"; "151,000+ Foods / 4,000+ Brands". App Store: AI photo. **"10,000+ menu items" and barcode scanning are not found.** |
| F34 | stands (now opened) | Loqma App Store: "IT LEARNS *YOUR* PORTIONS — Log laban a few times and Loqma remembers your bottle size"; "Almarai"; "sometimes checks the actual nutrition label online"; "In Arabic, English, or any mix". It also lists "13 languages, full RTL Arabic", widgets and Live Activity. |
| F35 | stands (now opened) | mwm.ai/apps/mezan: "750k+" downloads, "4.8/5", photo AI, targets. zorest blog: "more than 1.9 million food items", "Restaurant menu analyzer". healthybytes.online: "Snap your meal or scan a barcode"; Saudi Arabia and the Gulf. These are vendor or aggregator claims. |

---

## Dropped

Whole findings (refuted or doubtful). Keep them out of the records, or replace them with the corrected statement shown:

- **C47** (refuted): Yazio's App Store lists no Arabic, including on the SA storefront.
- **C54** (refuted): its own source now says MFP and Lose It! have Siri Shortcuts logging.
- **F10** (refuted, superseded): replace with OFF on 2026-10-01: Saudi Arabia 16,723 products, Egypt 4,406.
- **C6** (doubtful): replace with "MFP has no first-class personal unit, but saving a remembered meal 'locks in the adjusted amount', or you create a custom food with the exact portion".
- **C8** (doubtful): the accuracy numbers come from a PlateLens-affiliated site, and Nutrients 2024 contradicts them. Voice-log limits are not checked.
- **C26** (doubtful): no official statement; the licence reason is not found.
- **C27** (doubtful): Cronometer's own pages show no Trash and say deleted data "cannot be recovered".
- **C39** (doubtful): the quotes are not on the opened pages.
- **C50** (doubtful): the benchmark contradicts itself and promotes PlateLens. Do not use its numbers.
- **F18** (doubtful): it rests on a third-party plan with a wrong launch date. SFDA hints at developer access.
- **F31** (doubtful): the sugar figure is misquoted (81.4% dry weight) and the cultivars are not confirmed.

Sub-claims dropped from findings that otherwise stand:

- **C1:** "deal closed in December 2025"; "20 million foods, 68,500 brands, 380+ chains".
- **C2:** GLP-1 log as part of the Winter release (it launched 2026-04-28); "US iOS only".
- **C4:** "6–10 taps vs 2–3"; "copy single items removed"; "rating 3.24 → 1.54"; "v26.16.0". Implication 1's "MFP lost its rating" rests on these.
- **C5:** swipe to "bring forward" (Quick Log).
- **C7:** "most-cited reason people leave MFP".
- **C9:** "largely user-submitted, not systematically verified".
- **C12:** "batched"; "deepest".
- **C13:** the Siri/Shortcuts part (sources conflict).
- **C15:** "recents / custom foods / quick add work offline".
- **C16:** Lose It! lists Arabic (refuted).
- **C19:** "previous meals, one tap".
- **C20:** "free daily/weekly summaries; Patterns Premium".
- **C28:** "video ads hijack up to half a minute".
- **C31:** "2–3 weeks to settle".
- **C34:** "Cronometer 40 total actions".
- **C37:** Milestones tab, water, deficit calculator.
- **C38:** "$770K/month on ads".
- **C40:** "25–50% under-count"; "zero citations".
- **C42:** "3 AI logs a day"; paid dietitian review.
- **C44:** "±20% accuracy"; "late 2025".
- **C49:** "≈30% → ≈14% with ingredients". Implication 4's "roughly halves the error" rests on this.
- **C52 / F33:** Kilo "10,000+ menu items"; Kilo barcode scanning.
- **C52:** Saaraty.
- **F6:** "April 2026 Foundation release planned". It shipped as 15.0; the current release is 15.5.
- **F14:** "some fields hold T". There is one.
- **F16:** "launched 2024–25". It launched in 2026 (news dated 2026-03-31 and 2026-08-30).
- **F25:** "56+ countries, 24 languages". Now "over 62 countries, 26 languages".
- **F30:** "a review from King Saud University".
- **F32:** "up to 20 seconds per recording". It is a 20 s *average logging time*.

## High-impact assumptions

These may enter the records only with the label `assumption`. A product decision rests on each.

1. **C57: no competitor plans from a photo of the table plus a calorie cap.** Journey C's claim to set us apart rests on this absence. It cannot be proven. Local apps were not examined, and MFP's AI Coach now suggests "exactly what to eat and how much".
2. **C46 + C54 + implication 3: "Siri logging is rare among the leaders, so it sets us apart".** C54 is refuted, and C46's Yazio article is no longer public. Build App Intents on Apple's guidance (C55), not on rarity.
3. **Implication 8: "incumbents are weak at correction history and offline".** C27 (Cronometer Trash) and C26 (Cronometer offline) are doubtful. C10's Undo window rests on a competitor blog. Only MacroFactor (C35) and MFP's lack of offline database search (C15) are primary.
4. **F15 / F12: NNI holds the rights and has granted no open licence.** Implication 3 ("do not seed from Kaggle copies") rests on this. It errs on the safe side, but it is unverified; the Kaggle copy's source is a Scribd upload.
5. **F18: SFDA data has no API or developer access.** This is doubtful; SFDA's own news speaks of "making data accessible for developers". Ask SFDA directly. F17 is already confirmed: the tables are all-rights-reserved and need written permission.
6. **C49 sub-claim: describing the ingredients roughly halves photo-estimate error.** The case for the Describe mode in WF-4 uses it. The peer-reviewed source supports only "inaccurate, especially for mixed and culturally diverse foods".
7. **C6 corrected: MFP's "remembered meal" already stores a personal adjusted serving.** Loqma (F34) "learns your portions". Treat "define a portion once" as contested ground, not a gap in the market.

## Notes for the merge

- **New fact against the capability matrix:** Cronometer has Gold "AI Voice-Logging". Help article 47549391461780, updated 2026-10-01: "dictate or type out a description of the food".
- **New fact against implication 7:** Loqma claims "full RTL Arabic", widgets and Live Activity. The local rivals are ahead of the big six on Arabic.
- **Licences now known:** F19 Saudi dishes study is CC BY 4.0, so it is usable with attribution. F23 FAO 1982 is all rights reserved. F22 myfood24 paper is CC BY, but the database itself is commercial.
- **Unverified lead, not a finding:** eesel.ai says Cal AI was "briefly removed from the App Store by Apple in April over deceptive billing" and had "a data breach affecting 3.2M+ users". Verify before use.
