# R1 · Nutrition data sources for Egyptian, Saudi and wider Arab foods

Research cycle 1, question 2 of `map-first.md` §7. Run date: **2026-10-01**. Brief context: FRD §6 (FR-025…031, §6.2 grounding strategy), FR-015 (Arabic aliases).

**Question.** Which nutrition data sources can supply authoritative numbers for Egyptian, Saudi and wider Arab foods, and under what licence? How do we handle Arabic and Egyptian-dialect names? Which Arabic-first calorie apps exist?

## Method and limits (read first)

- This session's network egress allowed **only GitHub** (github.com, raw.githubusercontent.com, GitHub code search). WebFetch and curl to fdc.nal.usda.gov, api.nal.usda.gov, world.openfoodfacts.org, fao.org, sfda.gov.sa, spa.gov.sa, kaggle.com, pmc.ncbi.nlm.nih.gov, frontiersin.org, wikidata.org, apps.apple.com, platform.fatsecret.com and the app websites all returned `EGRESS_BLOCKED` / HTTP 403 from the proxy.
- So **"opened"** here means that I fetched the page in this run. That includes primary documents hosted on GitHub (Open Food Facts' own docs and taxonomies, the ODbL text) and GitHub-hosted secondary copies or mirrors of primary sources. Each one is labelled as primary or secondary.
- When WebSearch surfaced a source but I could not open it, the claim is labelled **`assumption`**. It keeps the link, and the text in brackets is a search-snippet paraphrase, not a quote.
- Numbers quoted below are copied from the source named. None were computed or invented. Numbers from unofficial copies are marked as unverified.

## Findings

### USDA FoodData Central (FDC)

**F1 — Licence: public domain, CC0 1.0. FDC also asks to be cited.** Opened (secondary: a University of Alabama Libraries cookbook that quotes FDC's own statement). The primary api-guide was blocked, but WebSearch's snippet of fdc.nal.usda.gov/api-guide gives the same wording.
· https://github.com/UA-Libraries-Research-Data-Services/UALIB_ScholarlyAPI_Cookbook/blob/main/src/overview/fdc.rst · 2026-10-01
· "USDA FoodData Central data are in the public domain and they are not copyrighted. They are published under CC0 1.0 Universal (CC0 1.0)"; citation: "U.S. Department of Agriculture, Agricultural Research Service. FoodData Central, 2019. fdc.nal.usda.gov."

**F2 — API: an api.data.gov key is required. Base URL `https://api.nal.usda.gov/fdc/v1/`; main endpoints `foods/search` and `food/{fdcId}`.** Opened (secondary: the same cookbook's notebook).
· https://github.com/UA-Libraries-Research-Data-Services/UALIB_ScholarlyAPI_Cookbook/blob/main/src/python/fdc.ipynb · 2026-10-01
· "An API key is required to access the FoodData Central API." / `BASE_URL = 'https://api.nal.usda.gov/fdc/v1/'`, `endpoint = 'foods/search'`, `endpoint = f"food/{fdc_id}"`

**F3 — Rate limits.**
- A registered key gets 1,000 requests per hour per IP. Going over blocks the key for 1 hour.
- `DEMO_KEY` gets 30 requests per hour and 50 per day.
- `/v1/foods` takes at most 20 IDs per call.
- Higher limits are available on request.

Opened (third-party profile, which states "This is not our API"). The 1,000/hour figure matches WebSearch's snippet of the official api-guide. The DEMO_KEY figures come only from this third-party profile, so treat them as assumption-grade.
· https://github.com/api-evangelist/fooddata-central/blob/main/rate-limits/fooddata-central-rate-limits.yml · 2026-10-01
· "Registered API key holders are allowed 1,000 requests per hour per IP address. Exceeding this limit will result in the API key being temporarily blocked for 1 hour." / "The /v1/foods endpoint (multi-food lookup) accepts a maximum of 20 FDC IDs per request."

**F4 — Data types.**
- **Foundation:** analysed basic foods with rich metadata.
- **SR Legacy:** the frozen April 2018 release.
- **Survey (FNDDS):** foods as reported eaten in NHANES / What We Eat in America, updated every two years.
- **Branded:** label data from brand owners via GDSN or Label Insight, with a `market_country` field.

Opened (secondary: a data dictionary that mirrors FDC's field descriptions, plus a saved copy of the FDC download page from about 2023).
· https://github.com/eiz/fooddb/blob/main/docs/data-dictionary.md · 2026-10-01 · "Foods whose consumption is measured by the What We Eat In America dietary survey component of NHANES." / "Foods from the April 2018 release of the USDA National Nutrient Database for Standard Reference." / "Foods whose nutrient values are typically obtained from food label data provided by food brand owners." / "Source of the data (GDSN or Label Insight)"
· https://github.com/MrRyanK/CostPerNutrient/blob/main/aborted/data/USDA-FoundationFoods/FoodData%20Central.html · 2026-10-01 · "The Food and Nutrient Database for Dietary Studies (FNDDS) is updated every two years, in conjunction with the two-year cycles of the National Health and Nutri-tion Examination Survey."

**F5 — FNDDS is the FDC type for cooked and mixed dishes "as eaten", and it already holds useful regional analogues.** FNDDS 2021–2023 was published in FDC as the `survey_food_csv_2024-10-31` download. Records include:

| FDC ID | FNDDS record | Our use |
|---|---|---|
| 2707408 | Falafel | analogue for طعمية |
| 2707367 | Fava beans, cooked | analogue for فول |
| 2707587 | Tahini | |
| 2706621 | Stuffed grape leaves with beef and rice | |
| 2709641 | Bitter melon, horseradish, jute, or radish leaves, cooked | analogue for ملوخية |
| 2709203 | Date | generic only; no cultivar |
| 2710168 | Ghee, clarified butter | |
| 2707730 | Bread, pita, whole wheat | |

Opened (secondary: a third-party Egyptian diet app's source registry, reviewed 2026-08-26). Re-verify these IDs on FDC once egress allows.
· https://github.com/ahmed-hassan19/diet-tracker-app/blob/main/public/nutrition-sources.json · 2026-10-01
· "USDA FNDDS 2707408 — Falafel" / "USDA FNDDS 2709641 — Bitter melon, horseradish, jute, or radish leaves, cooked" / "url": "https://fdc.nal.usda.gov/fdc-datasets/FoodData_Central_survey_food_csv_2024-10-31.zip"

`assumption`: FNDDS 2021–2023 has 5,432 foods and beverages, and most of its nutrient profiles are recipe-calculated from Foundation and SR Legacy ingredient codes. [WebSearch snippet of the ARS fact sheet https://www.ars.usda.gov/ARSUserFiles/80400530/pdf/fndds/FNDDS_2021_2023_factsheet.pdf]

**F6 — `assumption`: FDC releases in 2025–2026.** Version 14.0 came out on 2025-12-18. Monthly Branded updates followed (14.1–14.3, January to March 2026), and an April 2026 Foundation release was planned. [WebSearch snippet of https://fdc.nal.usda.gov/log, host blocked]

### Open Food Facts (OFF)

**F7 — Licences.**
- The database is under ODbL 1.0.
- Individual contents are under DbCL 1.0.
- Images are under CC BY-SA, and may also carry third-party design rights.

Opened (primary: OFF's own docs in its server repository).
· https://github.com/openfoodfacts/openfoodfacts-server/blob/main/docs/api/tutorials/license-be-on-the-legal-side.md · 2026-10-01
· "The Open Food Facts database is available under the Open Database License." / "Product images are available under the Creative Commons Attribution ShareAlike license. They may contain graphical elements subject to copyright or other rights"

**F8 — What ODbL requires of us.**
- **Showing data:** publicly showing data (a "Produced Work") needs an attribution notice.
- **Share-alike:** pulling the whole database, or a substantial part of it, into our own database creates a **Derivative Database**. That database must stay under ODbL whenever anything made from it is shown publicly.
- **Publishing the changes:** we must then offer the whole derivative database, or a file of our changes, in machine-readable form, free online.

Opened (primary: a verbatim copy of the ODbL 1.0 text).
· https://github.com/aboutcode-org/scancode-toolkit/blob/develop/src/licensedcode/data/licenses/odbl-1.0.LICENSE · 2026-10-01
· §4.3a "Contains information from DATABASE NAME, which is made available here under the Open Database License (ODbL)." / §4.4b "Extraction or Re-utilisation of the whole or a Substantial part of the Contents into a new database is a Derivative Database and must comply with Section 4.4." / §4.6 "offer to recipients … a copy in a machine readable form of: a. The entire Derivative Database; or b. A file containing all of the alterations"

**F9 — OFF API rules.**
- 15 product reads per minute per IP.
- 10 searches per minute per IP. Search-as-you-type is not allowed.
- For a mobile app, the limits apply per user.
- For more than a few hundred products, download the CSV or JSONL dump instead.
- Send a custom `User-Agent: AppName/Version (ContactEmail)` on every call.
- Reads need no authentication.

Opened (primary).
· https://github.com/openfoodfacts/openfoodfacts-server/blob/main/docs/api/index.md · 2026-10-01
· "15 req/min/IP address for all read product queries" / "10 req/min/IP address for all search queries … don't use it for a search-as-you-type feature" / "If your requests come from your users directly (ex: mobile app), the rate limits apply per user." / "download the data as a CSV or JSONL file directly"

**F10 — How many Saudi and Egyptian products OFF holds.**
- `assumption`: sa.openfoodfacts.org lists 14,797 products. [WebSearch snippet of https://sa.openfoodfacts.org/]
- Opened (secondary: a third-party Saudi app plan) gives a similar figure, quoted below.
- **Egypt: I found no product count**, so it is unknown.

· https://github.com/alajwadha/nabdh/blob/main/docs/PLAN.md · 2026-10-01 · "~15,500 Saudi-tagged products with Arabic fields and barcodes, no API key."

**F11 — OFF's Arabic vocabulary is thin, and part of it does not fit our dialects.** Counts from the raw taxonomy files on 2026-10-01:
- `categories.txt` has 170 `ar:` lines against 9,254 `en:` lines.
- `ingredients.txt` has 607 `ar:` lines against 4,834 `en:` lines.
- There is no Arabic entry for طعمية, فتة, كشري, تلبينة, صقعي or بلدي.
- `ar: لبن` sits on the **Yogurts** category, and `ar: حليب رائب` on buttermilk.
- `ar: فول` sits on the broad-bean ingredient.
- Ajwa and Medjool dates appear in English only. Sukkari and Saqai are absent.

Opened (primary taxonomy files). The taxonomies' own licence is not stated in the files I opened. `assumption`: treat them as part of the ODbL database.
· https://github.com/openfoodfacts/openfoodfacts-server/blob/main/taxonomies/food/categories.txt and …/ingredients.txt · 2026-10-01
· "en: Yogurts, yoghourts, yoghurts, yogourts, jogurts / ar: لبن" / "en: buttermilk, butter milk, sweet cream buttermilk / ar: حليب رائب, حليبب" / "en: broad bean, fava bean, faba bean … / ar: فول"

### Egypt

**F12 — `assumption`: the National Nutrition Institute (NNI) publishes *Food Composition Tables for Egypt*.** The 1996 edition (Nutrition Institute A.R.E., 115 pages, English) is catalogued by FAO/INFOODS, and the 2nd edition (2006) is widely cited. It is a printed or PDF table. I found no API, no CSV from NNI, no newer edition, and no published licence. [WebSearch snippets of https://www.fao.org/food-composition/tables-and-databases/detail/(egypt--1996)-food-composition-tables-for-egypt/en and https://www.sciepub.com/reference/259441, hosts blocked]

**F13 — A digitised 470-food, 15-category copy of the Egyptian table circulates on GitHub.**
- Values are per 100 g. Nutrient columns: water, energy, protein, fats, fiber, carbohydrates, Na, K, Ca, P, Mg, Fe, Zn, Cu, vitamins A, C, B1, B2.
- Names are **English, with Egyptian transliterations**.
- **Unverified** values as they appear in this copy, in kcal per 100 g:

| Row in the copy | kcal / 100 g |
|---|---|
| "Bread,Baladi" | 254 |
| "Beans,broad (foal medames)" | 98 |
| "beans,broad (taamia),fried" | 355 |
| "Dates,dried (Agwa)" | 315 |
| "Rice (Koshari)" | 172 |
| "Sandwiches,stewed foul" | 173 |
| "Cheese, Karesh" | 100 |
| "Kishk" | 380 |

Opened (third-party digitisation; it says it was built from a "supplied" workbook; no licence stated).
· https://github.com/3bud-ZC/Source-of-Truth/blob/main/data/manifest.json and data/part1–4.csv · 2026-10-01
· "Static Arabic-first food analysis web app built from the supplied **Food Composition Tables for Egypt** workbook." / `"foodCount":470,"categoryCount":15,"basisGrams":100`

**F14 — Copies of the Egyptian table contain transcription defects.**
- In the F13 copy, "Barley,grains" shows water 88 g and energy 335 kcal per 100 g. That combination is physically impossible.
- "Milk chocolate" sits under dairy at 81.2 g water and 80 kcal. It looks like chocolate milk.
- Some numeric fields hold "T" (trace).
- A second team, importing the Kaggle copy of the same table, reported 3 corrupt rows and 9 rows with systematic transcription errors. Seven they corrected against USDA, and two they dropped.

Opened (the F13 CSV, plus a GitHub issue dated 2026-09-14).
· https://github.com/YoussefHawarii/fitness-tracking-web-app/issues/16 · 2026-10-01 · their seed list had "started as 20 hand-typed entries with USDA-typical estimated nutrient values."

**F15 — The Kaggle "Egyptian Food" dataset (mernamohamed3) is labelled CC BY 4.0 by its uploader, not by NNI.**
- 470 items, sourced to "National Nutrition Institute (Egypt), *Food Composition Tables for Egypt*, 2nd ed. (2006)".
- Names are English only. The importing team added Arabic and colloquial names by hand.

Opened (secondary: the GitHub issue; kaggle.com was blocked). `assumption`: NNI has granted no open licence, so rights stay with NNI and the CC BY label is not a valid grant.
· https://github.com/YoussefHawarii/fitness-tracking-web-app/issues/16 · 2026-10-01 · Source: "National Nutrition Institute (Egypt), _Food Composition Tables for Egypt_, 2nd ed. (2006)"; licence CC BY 4.0; host https://www.kaggle.com/datasets/mernamohamed3/egyptian-food

### Saudi Arabia

**F16 — `assumption`: the SFDA Saudi Food Composition Database (web tool at https://fd.sfda.gov.sa/) launched in 2024–25.** It is a bilingual web platform with 2,818 foods, 322 nutrients, and 18 groups / 226 subgroups. Every record carries a confidence score (high / good / medium / low). [WebSearch snippets of https://www.sfda.gov.sa/en/news/19370, https://www.sfda.gov.sa/en/news/18975, https://www.researchgate.net/publication/403909471 and https://www.researchsquare.com/article/rs-11073271/v1, hosts blocked]

**F17 — `assumption`: SFDA released the *Saudi Food Composition Tables* book in June 2026.**
- It covers 130 Saudi dishes. Each was prepared 3 times, with at least 12 samples per dish.
- It measures 49 nutrients, from more than 19,000 lab analyses.
- It is a PDF in Arabic and English (https://www.sfda.gov.sa/sites/default/files/2026-04/SFCT-E.pdf).
- A snippet reads "All rights are reserved by the Saudi Food and Drug Authority © 2026". This is the strongest candidate for authoritative Saudi dish numbers, but it needs written permission before we seed from it.

[WebSearch snippets of https://www.spa.gov.sa/en/N2620625, https://www.sfda.gov.sa/en/news/5521984 and https://www.sfda.gov.sa/en/regulations/5521782, hosts blocked]

**F18 — Another team building for Saudi users also found no public API and no open licence for the SFDA data.** Opened (secondary: the third-party app plan, consistent with F16–F17).
· https://github.com/alajwadha/nabdh/blob/main/docs/PLAN.md · 2026-10-01 · "it covers traditional dishes but has no public API yet. Action: contact SFDA to license/obtain it"

**F19 — `assumption`: a 2025 peer-reviewed study covers 25 traditional dishes from five Saudi regions.**
- The values are calculated with ESHA Food Processor software, not lab-analysed.
- Energy runs from 89.2 kcal/100 g (Margoug, مرقوق) to 306.9 kcal/100 g (Areekah, عريكة).
- Dishes include Haneeth, Jareesh, Hininy, Kabsah and Saleeq.
- It is open access in Frontiers in Nutrition (PMC12641437). I did not see its licence.

[WebSearch snippet of https://pmc.ncbi.nlm.nih.gov/articles/PMC12641437/, host blocked]

**F20 — `assumption`: SFDA requires calorie counts on restaurant and café menus.**
- Mandatory since 2019-01-01.
- Since 2025-07-01, menus must also show caffeine, high-salt and activity-equivalent labels.
- The rules include delivery apps.

[WebSearch snippets of https://www.sfda.gov.sa/en/news/2703257 and https://gulfnews.com/amp/story/world%2Fgulf%2Fsaudi%2Fsaudi-arabia-enforces-new-menu-rules-to-flag-salt-caffeine-and-calorie-burn-1.500183654, hosts blocked]

### Gulf, regional and other Arab sources

**F21 — `assumption`: GULFOODS.** Musaiger, *Food Composition Tables for the Arab Gulf Countries*, Arab Center for Nutrition, Bahrain, 2006. It includes composite dishes from Bahrain, Kuwait, Saudi Arabia, Qatar and Oman. FAO hosts a separate Bahrain table PDF. I found no licence. [WebSearch snippets of https://www.researchgate.net/publication/236668574 and https://www.fao.org/fileadmin/templates/food_composition/documents/pdf/FOODCOMPOSITONTABLESFORBAHRAIN.pdf]

**F22 — The myfood24 Arabic food composition database (2021).**
- `assumption` [WebSearch snippets of https://www.sciencedirect.com/science/article/pii/S0889157521002477 and https://eprints.whiterose.ac.uk/id/eprint/175374/]:
  - 2,016 items with 120 nutrients, for Saudi Arabia and Kuwait.
  - 79% are generic items from the UK CoFID database.
  - 13% are back-of-pack labels of local brands.
  - 8% are composite dishes from published sources.
- Opened (secondary) on licensing: it is licensable, with no public API.

· https://github.com/alajwadha/nabdh/blob/main/docs/PLAN.md · 2026-10-01 · "Research-grade Arabic Gulf-dish nutrition (~2,016 items, Saudi + Kuwait), licensable, no public API."

**F23 — `assumption`: the FAO/INFOODS regional table is too old for primary use.** *Food composition tables for the Near East*, FAO Food and Nutrition Paper 26, was produced by FAO and USDA (Rome, 1982, 265 pages).
- Values are per 100 g edible portion.
- It has 3 tables: proximate/mineral/vitamin, amino acids, and fatty acids.
- It covers 14 food classes and has an appendix of common names.
- It is online as HTML at https://www.fao.org/4/x6879e/x6879e00.htm. I did not see its licence.
- At 44 years old, it serves only as historical cross-check evidence.

[WebSearch snippets of that URL and https://agris.fao.org/search/ar/records/65de01814c5aef494fd8a70e, hosts blocked]

**F24 — `assumption`: other Arab tables exist and could serve as cross-checks.**
- Lebanon: *Food Composition Data: Traditional Dishes, Arabic Sweets*, Lebanese University, 2021.
- Oman: an updated food composition table (PMC10930989).
- UAE: Abu Dhabi Food Intake24 (Frontiers in Nutrition, 2026-08-20) combines USDA data with regional sources.

[WebSearch snippets of https://ul.edu.lb/files/ann/20210422-LU-RePa-Report.pdf, https://www.ncbi.nlm.nih.gov/pmc/articles/PMC10930989/ and https://www.frontiersin.org/journals/nutrition/articles/10.3389/fnut.2026.1866000/full]

**F25 — FatSecret Platform API is the commercial option with Saudi and Arabic localisation.**
- `assumption` [WebSearch snippet of https://platform.fatsecret.com/docs/guides/localization]: localisation is a premium feature, Saudi Arabia is a supported region with Arabic as its default language, and the platform covers 56+ countries, 24 languages and more than 2.3M items. Commercial licence.
- Opened (secondary, a third-party opinion): see the quote below.

· https://github.com/alajwadha/nabdh/blob/main/docs/PLAN.md · 2026-10-01 · "The only provider with a localized Saudi/Gulf dataset, Arabic food names, barcode, natural-language parsing, and photo recognition in one."

### Arabic names and aliases

**F26 — Wikidata's structured data is CC0, and OFF taxonomy entries already link to Wikidata IDs.** That gives us a share-alike-free route to multilingual labels. Example IDs: falafel Q188788, tahini Q383389, dates Q1652093, halloumi Q549793.
- Opened (secondary: an app's licence notice that quotes Wikidata; the IDs come from the OFF files in F11).
- `assumption`: Wikidata carries Arabic-dialect labels; there is a paper on using Wikidata as a multi-dialect Arabic dictionary. [https://www.academia.edu/35571096/]

· https://github.com/scribe-org/Scribe-iOS/blob/main/PRIVACY.txt · 2026-10-01 · "All structured data in the main, property and lexeme namespaces is made available under the Creative Commons CC0 License"

**F27 — "لبن / laban" means a different food depending on region.**
- Gulf Arabic uses لبن for buttermilk and حليب for milk. Opened (secondary: a Gulf-dialect curriculum brief).
- OFF attaches لبن to yogurt (F11).
- `assumption`: in Egyptian Arabic, لبن means milk.

So one word maps to three foods, depending on the eater's dialect.
· https://github.com/danieljchandler/arabic-buddy/blob/main/docs/curriculum-research/gulf.md · 2026-10-01 · "تمر, رطب *ruTab* (fresh dates), لبن *laban* (buttermilk), حليب"

**F28 — The Egyptian table supplies Latin-script Egyptian names, but no Arabic script.**
- Its names include "foal medames", "taamia", "Baladi", "Agwa", "Bisara", "Menabet", "Nabet", "Termis", "Karesh", "MISH", "Kishk", "fiteer meshaltet" and "Homos sham". These can seed the English and transliteration side of our aliases.
- Arabic script and dialect spellings have to be added by hand, as the other team did (F15).

Opened (the F13 CSV). · https://github.com/3bud-ZC/Source-of-Truth/blob/main/data/part1.csv · 2026-10-01 · "Beans,broad (foal medames)" / "beans,broad (taamia),fried" / "Lupines,(Termis)"

**F29 — The open alias and value lists on GitHub are mostly hobby data.** A GitHub code search on 2026-10-01 for `"طعمية" "فول مدمس" calories` returned 65 files. They are app seed files, such as `utils/egyptianFoods.ts` and `data/foods.json`, from student and hobby projects. Opened: the search result itself. I did not audit each file. F14 shows how such lists start: "20 hand-typed entries with USDA-typical estimated nutrient values". Use them for name discovery at most, never for numbers.
· GitHub code search (via the GitHub tool) · 2026-10-01

### Specific foods named in the brief

**F30 — `assumption`: published talbina values describe the dry barley mix, not the prepared drink.** A 2021 review from King Saud University in the *International Journal of Food Properties* reports talbinah at "11.9% moisture … 386.74 kcal". That moisture level shows it is the dry mix. These values must not be applied to a prepared spoon. FRD §5.2's own rule says "Talbina spoon = 16 g — Prepared recipe, never 16 g dry barley flour". [WebSearch snippet of https://www.tandfonline.com/doi/full/10.1080/10942912.2021.1986521, host blocked]

**F31 — `assumption`: the date variety صقعي (Saqai/Suqaey) has published composition data, but only in the literature.** "Nutritional composition of fruit of 10 date palm cultivars grown in Saudi Arabia" (*Journal of Taibah University for Science*, 2015) includes Sukkari and Suqaey. The snippet gives sugar at 71.2–81.6%. The other sources do not distinguish varieties:
- FDC has only a generic "Date" (F5).
- The Egyptian table has "Agwa" (F13).
- OFF has no Saqai (F11).

[WebSearch snippet of https://www.tandfonline.com/doi/full/10.1016/j.jtusci.2014.07.002]

### Arabic-first calorie apps (competitors)

**F32 — `assumption`: Kam Calorie (iOS).**
- Voice logging in Arabic or English, up to 20 seconds per recording.
- It claims to understand Egyptian and Saudi dialects.
- Its dish list includes koshari, ful medames, molokhia, ta'meya, mahshi, feteer, kabsa, mandi, haneeth, jareesh, matazeez and saleeg.
- It detects the cooking method and adds the calories of frying oil.

[WebSearch snippets of https://www.kamcalorie.app/en and https://apps.apple.com/us/app/kam-calorie-ai-calorie-tracker/id6748948785]

**F33 — `assumption`: Kilo (كيلو), Saudi Arabia.**
- AI plate scanner.
- Saudi food database, plus 10,000+ menu items from chains such as Albaik, Herfy and McDonald's.
- Barcode scanning of Saudi products.
- Fully Arabic interface.

[WebSearch snippets of https://kiloappsa.com/ and https://apps.apple.com/us/app/kilo-ai-calorie-counter/id6752930312]

**F34 — `assumption`: Loqma, Saudi and Gulf-first. This is the app closest to our "define once" idea.**
- You describe a meal in one message.
- It "learns your portions — log a food a few times and it remembers your serving size".
- It covers local brands such as Almarai.
- It checks labels online.
- It accepts Arabic, English or a mix.

[WebSearch snippets of https://getloqma.com/en and https://apps.apple.com/us/app/loqma-ai-calorie-tracker/id6779627300]

**F35 — `assumption`: other Arabic-first apps.**
- **Mezan (ميزان):** rated 4.8, 750k+ downloads, photo AI and targets.
- **Zorest:** a database of 1.9M items and a restaurant menu analyser.
- **HealthyBytes:** an Arabic database and photo/barcode AI. Note how close this name is to ours, "Sips & Bytes".

[WebSearch snippets of https://mwm.ai/apps/mezan/6739188441, https://zorest.com/blog/best-arabic-calorie-counter-app and https://healthybytes.online/en]

## Implications for our map

1. **Recommended seeding strategy, in tiers. Each tier carries its own licence on every record.**
   - **Tier A — USDA FDC, bulk.** Download Foundation, SR Legacy and FNDDS 2021–2023 server-side into the reference DB. It is CC0: no share-alike, cite as in F1. Resolve from that copy instead of calling the API live from phones, because of the 1,000/hour limit (F1–F5).
   - **Tier B — Egyptian and Saudi dishes.** The nutrition approver curates recipe records for the top ~50 Egyptian and Saudi dishes (فول، طعمية، فتة، كشري، ملوخية، كبسة، جريش، مرقوق، تلبينة…). Build them from weighed ingredients using Tier A components, with evidence badge "recipe-calculated" (FR-028).
   - **Cross-check, don't copy.** Use NNI, SFDA and the literature only as review cross-checks until a licence is signed (F12–F19).
   - **Tier C — packaged goods.** Look up OFF by barcode at runtime (F7–F10).
2. **Pursue two licences now.** Ask SFDA for the Saudi Food Composition Tables and Database (F16–F18), and NNI for *Food Composition Tables for Egypt* (F12, F15). Both are the authoritative local numbers, and both appear to be all-rights-reserved or unlicensed. When a licence is signed, its records move from "cross-check" to "authoritative database" in the FR-025 resolver order.
3. **Do not seed from Kaggle or GitHub copies of the Egyptian table.** The CC BY label is the uploader's, not NNI's, and the copies contain transcription errors (F13–F15, F29). Use them only to list which dishes exist and how they are transliterated.
4. **Keep OFF in its own store.** Storing OFF rows in our shared reference DB makes that DB a derivative of OFF. ODbL would then require us to keep that derivative under ODbL and to offer it in machine-readable form (F8).
   - Keep OFF-derived records in a separate, exportable store.
   - Show the §4.3 notice wherever OFF data appears.
   - Send the required User-Agent.
   - Never search-as-you-type against OFF (F9).
5. **Make the alias table an approver-owned artefact of our own.** Each alias row holds Arabic script, the dialect tag (EG / Gulf / MSA), Latin transliteration and English. Seed it from Wikidata labels (CC0, reached through the Wikidata IDs in OFF's taxonomy) and the Egyptian table's transliterations (F26, F28). OFF's Arabic taxonomy is too thin to rely on (F11).
6. **Resolve لبن/laban by dialect, then confirm.** Map it by the eater's dialect (EG → milk; Gulf → buttermilk/laban drink) and confirm on first use. This becomes a seeded high-impact ambiguity in the two-question budget (F11, F27; FR-015, FR-035).
7. **Badge single-variety foods as analogues until approved.** For foods such as صقعي and سكري dates, FDC offers only a generic "Date", so show "estimated analogue" until an approved SFDA or literature record exists (F5, F11, F31). Talbina, fatta and other mixed dishes must be recipe-calculated. Published dry-mix values never stand in for a prepared spoon (F30).
8. **Saudi restaurant calories are label-grade evidence by law (F20).** That supports the FRD's restaurant entries (§6.2). Kilo already holds 10k+ menu items (F33), so a curated chain-menu import is a candidate for P1 work.
9. **Competitors overlap our core.** Loqma already "remembers your serving size" (F34), and Kam Calorie does dialect voice logging (F32). Our differentiators to put in the map:
   - versioned, measured units with evidence;
   - an append-only ledger with corrections that show old, new and the difference;
   - source and licence shown on every number.

   Also check the naming clash with HealthyBytes (F35).
10. **Re-run the blocked sources before the final map.** Re-open them with egress allowed: fdc.nal.usda.gov, world.openfoodfacts.org, sfda.gov.sa, fao.org, kaggle.com and the app stores. That converts the `assumption` findings (F6, F10, F12, F16, F17, F19–F25, F30–F35) to opened ones. Put the SFDA and NNI permission answers in the blueprint's open-questions list.
