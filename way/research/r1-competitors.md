# R1 · How the best food-tracking apps run this operation today

Research cycle 1, question 1 of `map-first.md` §7. Researched and written 2026-10-01.
Apps covered: MyFitnessPal (MFP), Lose It!, Cronometer, MacroFactor, Cal AI, SnapCalorie, Yazio, plus Arabic-first local apps.

## How to read this file (please read before using any finding)

**Access limit in this run.** The session's network egress policy blocked almost every source. Every competitor, review, press and forum host I tried was denied: myfitnesspal.com (blog, support, news, community), loseit.com, cronometer.com (including support and forums), macrofactor.com, macrofactorapp.com (including help), calai.app, snapcalorie.com, yazio.com (including help), apps.apple.com, reddit.com, globenewswire.com, prnewswire.com, finance.yahoo.com, techcrunch.com, garagegymreviews.com, pubmed/ncbi, mdpi.com, arxiv.org, web.archive.org, piunikaweb.com, trustpilot.com, and the other review blogs listed below. The only hosts I could open were **developer.apple.com** and **github.com**. To open the others, someone has to add them to the environment's allowed domains under Network access.

So each finding carries one of two labels:

- **`opened`**: I opened the page in this run, and the quote is the page text as the fetch tool returned it.
- **`assumption`**: I did not open the page because it was blocked. The claim and the quote come from the web-search engine's excerpt for that URL. They are **not** verified page text, so treat them as leads to verify. The source date is the date in the URL, title or excerpt, or "undated".

Source quality varies. Several excerpts come from blogs run by competing apps (nutrola.app, nutriscan.app, fitia.app, platelens.app, snapcalorie.com/blog), which are SEO content with a commercial interest. These are flagged **(competitor blog)**. Where sources conflict, the conflict is stated.

All findings were accessed on 2026-10-01.

---

## Capability matrix (summary; details and sources in the findings)

| | MyFitnessPal | Lose It! | Cronometer | MacroFactor | Cal AI | SnapCalorie | Yazio |
|---|---|---|---|---|---|---|---|
| Custom servings / personal portions | Fractional DB servings; no personal unit (C6) | Recipe size / servings (C19) | "Advanced Serving Sizes" (C22) | Custom foods, label/photo-made foods (C30, C32) | Adjust serving on result (C39) | n/f | Servings or grams (C45) |
| Recipe with cooked yield | Workaround: serving = weighed cooked total (C6) | Servings count (C19); yield n/f | **"Set Cooked Recipe Weight"** (C22) | Multi-serving recipes, recipe-from-photo (C32); yield n/f | n/f | n/f | Portions (C45) |
| Quick / repeat logging | Bring forward last meal, copy meal, My Meals, recents (C5); redesign added taps (C4) | "Previous meals" one tap (C19) | **Copy Previous Day**, favourites (C23) | Copy/paste, favourites/history, timeline; fewest taps (C30, C34) | Saved "My Foods" (C39) | n/f | Favourites (C45) |
| Photo / voice AI | Meal Scan + Photo Upload + Voice Log, Premium (C2, C8) | Snap It, Say It (C17) | Photo Logging, Gold (C24) | Snap and Describe, multi-photo + text (C32); voice on Watch (C33) | Photo-first, describe (C37) | Photo + depth + voice note (C42) | AI photo since late 2025 (C44) |
| Barcode | Premium since 2022 (C7) | Moving to Premium, conflicting (C18) | Free (C28) | Yes (C30) | Yes (C37) | Yes (C42) | n/f |
| Corrections / history | Delete with about 30 s Undo; no restore (C10) | n/f | Trash 7 days, swipe Undo (C27) | Add, edit, delete, even in an outage (C35) | "Edit Items" before save (C39) | Chat or paid dietitian review (C42) | n/f |
| Daily / weekly reports | Weekly digest, Premium; AI Coach (C3, C11) | Daily and weekly free; Insights/Patterns Premium (C20) | Nutrient depth and trends (C51) | Weekly check-in (C31) | n/f | n/f | n/f |
| Targets / adaptive | Grams and per-weekday goals, Premium; not adaptive (C11) | Budget; macros Premium (C21) | Custom targets; not adaptive (C51) | **Adaptive expenditure** (C31) | Deficit calculator (C37) | n/f | n/f |
| Apple Health | Two-way, batched (C12) | Syncs (C12) | Deepest write (C12) | Sync (C30) | Yes (C37) | Yes (C42) | n/f |
| Offline | Partial: recents and quick add (C15) | n/f | **None** (C26) | Cached only (C35) | "My Foods" offline (C39) | n/f | n/f |
| Arabic / RTL | Not supported, conflicting (C16) | Listed in one excerpt, unverified (C16) | No (C29) | No (C36) | Listed (C41) | n/f | Listed; RTL disputed (C47) |

n/f = not found in this run.

---

## Findings

### MyFitnessPal

**C1.** MyFitnessPal bought Cal AI. The deal closed in December 2025 and was announced about 2 March 2026. Cal AI stays a separate app but now uses MFP's food database, so the photo-first leader and the database leader are one company.
- Source: https://news.myfitnesspal.com/myfitnesspal-expands-its-position-as-the-leading-player-in-digital-nutrition-tracking-with-cal-ai-acquisition/ and https://thenextweb.com/news/myfitnesspal-acquires-cal-ai-the-viral-calorie-tracking-app-built-by-teens. Date: about 2026-03-02, decoded from the founder's X post ID 2028473704359874652. `assumption`.
- Quote (search excerpt): "The deal closed in December 2025." … "The Cal AI app will remain independent … integrated with MFP's huge nutrition database that spans 20 million foods, 68,500 brands, and meals served at 380+ restaurant chains."
- Corroboration, `opened`: https://gist.github.com/Dwite/7ef215e3b9f8ba2bf5ef366bf3a5d657 (research date 2026-04-01; the gist cites no source): "Acquired by MyFitnessPal (March 2026)".

**C2.** MFP's Winter 2026 release (24 Feb 2026) added Meal Scan "Photo Upload": take the photo now, log it later (Premium, US iOS only). It also added a GLP-1 medication log and a recipe tab in the Meal Planner (Premium+).
- Source: https://www.globenewswire.com/news-release/2026/02/24/3243668/0/en/MyFitnessPal-Debuts-Its-2026-Winter-Release.html. Date: 2026-02-24. `assumption`.
- Quote (search excerpt): "Photo Upload for Meal Scan allows iOS users to take a photo of their plate and log their meal later" … "available for Premium & Premium+ members on US iOS only" … "log medication, track dose and timing, and note injection location."

**C3.** MFP's Summer 2026 release (25 Aug 2026) added an AI Coach grounded in the user's own logging history and a "Plan" tab of recipes and saved meals matched to the goal.
- Source: https://www.globenewswire.com/news-release/2026/08/25/3350516/0/en/myfitnesspal-announces-its-2026-summer-release.html. Date: 2026-08-25. `assumption`.
- Quote (search excerpt): "AI Coach gives Premium and Premium+ iOS members personalized nutrition guidance grounded in their own logging history" … "The new Plan tab is designed to help users find recipes, saved meals, and suggestions that are already matched to their goal."

**C4.** MFP's April 2026 redesign (v26.16.0, which replaced the Diary with a "Today" screen) set off a large backlash. Users counted 6–10 taps where 2–3 used to do, and copying single items between meals was removed. The rating reportedly fell from 3.24 to 1.54, and MFP said it would not revert.
- Sources: https://piunikaweb.com/2026/04/24/myfitnesspal-new-update-complaints/ (2026-04-24), https://piunikaweb.com/2026/05/05/myfitnesspal-new-design-update-is-here-to-stay/ (2026-05-05), https://mwm.ai/articles/myfitnesspal-v26-16-0-replaces-diary-with-new-ui-sparking-rating-drop-in-april-2026 (April 2026). `assumption`.
- Quote (search excerpt): "users describe counting 6 to 10 taps to complete a workflow that used to take 2 or 3" … "the update removed the ability to copy individual food items between meals" … "the new UI is 'the path forward' with no option to revert".

**C5.** MFP offers several ways to repeat a log. A swipe brings forward the foods last logged under a meal ("Quick Log"). The "•••" menu copies a meal to another day. "My Meals" saves a group of foods to log in one step, and a history list shows recent foods.
- Sources: https://support.myfitnesspal.com/hc/en-us/articles/360032622131-How-do-I-copy-a-meal-from-one-day-to-another (undated) and https://support.myfitnesspal.com/hc/en-us/articles/360032625331-Create-find-and-log-your-saved-meals (undated). `assumption`.
- Quote (search excerpt): "swipe right just below the meal name to bring forward the foods last logged under that meal" … "My Meals lets you save a group of foods you eat together and log them all at once."

**C6.** MFP has no personal portion unit. If the serving you need is missing, you log a fraction of an existing serving. For recipes, the recommended workaround is to weigh the cooked pot and set the serving size to that total weight.
- Sources: https://support.myfitnesspal.com/hc/en-us/articles/360032272852-The-serving-size-I-need-to-log-is-not-available (undated) and https://www.workingagainstgravity.com/articles/myfitnesspal-tutorial-how-to-add-bulk-recipes-log-a-single-serving (undated). `assumption`.
- Quote (search excerpt): "if the serving size is 1 cup and you use 3/4 of a cup, you can enter .75 as the number of servings" … "weigh the final cooked recipe in grams, and then add the serving size as that total weight".

**C7.** MFP moved the barcode scanner to Premium in late 2022, and Meal Scan, Barcode Scan and Voice Log are all Premium today (Premium $79.99/yr, Premium+ $99.99/yr). Paywalling barcode is still the most-cited reason people leave MFP.
- Sources: https://www.pocket-lint.com/apps/news/162386-wow-myfitnesspal-put-its-popular-barcode-scanner-feature-behind-a-paywall/ (2022) and the Winter 2026 release excerpt (C2). `assumption`.
- Quote (search excerpt): "MyFitnessPal moved barcode scanning behind its premium paywall in late 2022" … "Premium unlocks advanced tools like Meal Scan, Barcode Scan, and Voice Log".

**C8.** MFP's Voice Log matches speech to database foods and interprets loose portions such as "a handful". It reportedly cannot log custom foods or saved recipes. One third-party test (method unknown) put Meal Scan at 71.2% identification and ±18% portion error, worse on non-American food.
- Sources: https://blog.myfitnesspal.com/voice-logging/ (undated) and https://ai-food-tracker.com/reviews/myfitnesspal/ (2026; low reliability). `assumption`.
- Quote (search excerpt): "It cannot log custom foods, saved recipes, exercise, weight, or water through voice" … "71.2% food identification accuracy and ±18% portion MAPE" … "the accuracy degraded significantly on non-American cuisine."

**C9.** MFP's database is largely user-submitted and not systematically checked. A green check mark shows MFP-verified entries, and users can "Report a Food". Duplicate, wrong entries are the most common accuracy complaint.
- Sources: https://support.myfitnesspal.com/hc/en-us/articles/360032273292-Check-Marked-items (undated) and https://blog.myfitnesspal.com/how-food-database-works/ (undated). `assumption`.
- Quote (search excerpt): "user-submitted database entries, which are not systematically verified" … "foods with a green checkmark, which indicates they come from a reliable source and have been reviewed and verified by MyFitnessPal".

**C10.** Correcting history in MFP means editing or deleting in place. A deleted entry can be undone for about 30 seconds, and after that it cannot be restored. There is no correction history.
- Sources: https://support.myfitnesspal.com/hc/en-us/articles/360032624311-How-to-delete-an-entry-from-your-food-diary (undated), https://community.myfitnesspal.com/en/discussion/10879929/can-i-undelete-a-food-diary-entry-that-i-deleted-in-error (undated) and https://nutrola.app/en/blog/how-to-recover-deleted-calorie-tracking-data (competitor blog). `assumption`.
- Quote (search excerpt): "an "Undo" option, available for about 30 seconds after the delete" … "there is not a way to permanently reverse a deletion".

**C11.** MFP's targets are static: a formula plus manual edits. Macro goals in grams, different goals per weekday, macros by meal and "weekly digests" are Premium features. No adaptive target was found.
- Source: https://support.myfitnesspal.com/hc/en-us/articles/360032625951-MyFitnessPal-Premium-features (undated). `assumption`.
- Quote (search excerpt): "only Premium users can set macro goals by grams" … "Premium allows you to set unique macro goals for each day of the week" … "Premium offers unlimited weekly digests".

**C12.** Apple Health integration is standard among the leaders, and the top apps write nutrition out as well as reading activity in. MFP is two-way but batched. Cronometer writes the most detail. Lose It! syncs with Apple Health.
- Source: https://nutrola.app/en/blog/how-to-sync-calorie-tracker-with-apple-health-complete-guide (2026; competitor blog). `assumption`.
- Quote (search excerpt): "MyFitnessPal supports two-way HealthKit sync — calories and macros write out, exercise and weight read in — but updates are batched rather than real-time" … "Cronometer has the deepest data depth — full micronutrient writes, biometric reads, exercise sync."

**C13.** MFP has no real Apple Watch logging app. Siri/Shortcuts voice logging is a long-standing user request, and users say they cannot build it because Voice Log is menu-driven.
- Sources: https://community.myfitnesspal.com/en/discussion/10892050/siri-shortcuts-integration-for-easy-food-entry-in-myfitnesspal (undated) and https://nutrola.app/en/blog/why-does-myfitnesspal-not-work-on-apple-watch (competitor blog). `assumption`.
- Quote (search excerpt): "users report being unable to build shortcuts on iOS to voice log because it's a menu-driven feature not available as a choice in Apple shortcuts" … "you can't log food from your wrist."

**C14.** MFP offers Home Screen widgets (Today's Calories or Macros), an interactive water widget (iOS 18+), a "Streaks" view on the Today tab, a water tracker for all users and an intermittent-fasting timer (Premium).
- Sources: https://support.myfitnesspal.com/hc/en-us/articles/6886354417165-How-to-add-a-Widget-to-mobile-Home-Screen, https://support.myfitnesspal.com/hc/en-us/articles/39985611667341-Your-Today-tab and https://support.myfitnesspal.com/hc/en-us/articles/10983207647117-Track-Intermittent-Fasting-with-MyFitnessPal-Premium (all undated). `assumption`.
- Quote (search excerpt): "a new Interactive Water Widget for iOS on iOS 18 or later" … "a Streaks view to help you track your logging" … "The Intermittent Fasting Tracker is a MyFitnessPal Premium feature".

**C15.** MFP works only partly offline. Recents, custom foods and quick-add calories work and sync later. Database search and barcode need a connection.
- Source: https://support.myfitnesspal.com/hc/en-us/articles/360032622851-Can-I-access-MyFitnessPal-when-I-don-t-have-an-internet-connection (undated). `assumption`.
- Quote (search excerpt): "you can create a manual "quick add" entry with estimated calories, but you cannot search for specific foods" … "Your entries logged while offline should sync to the site when you connect to wifi again."

**C16.** Arabic support among the big apps is thin or unclear.
- MFP: one excerpt says Arabic is not supported, and a competitor blog says it is partly supported. These conflict.
- Lose It!: one search excerpt says the App Store listing includes Arabic. That listing was not opened.
- No source described real RTL layout in MFP or Lose It!.
- Sources: https://community.myfitnesspal.com/en/discussion/10889529/arabic-language (undated), https://nutrola.app/en/blog/nutrition-app-language-support-comparison-2026 (2026; competitor blog) and https://apps.apple.com/fi/app/lose-it-calorie-counter/id297368629 (undated). `assumption`.
- Quote (search excerpt): "Arabic is not currently supported by MyFitnessPal." versus "MyFitnessPal partially supports Arabic language in the interface". For Lose It!: "the app supports Arabic (العربية) and Thai."

### Lose It!

**C17.** Lose It! published its own data (22 Apr 2025) on its AI voice ("Say It!") and photo ("Snap It!") logging. Members using them showed 6% more weight loss, 3.5× faster logging and twice as many foods logged. This is company-reported, not peer-reviewed.
- Sources: https://www.newswire.com/news/lose-it-finds-ai-powered-logging-boosts-weight-loss-success-and-22557702 and https://www.pr-inside.com/lose-it-finds-ai-powered-logging-boosts-weight-loss-success-and-greater-r5096029.htm. Date: 2025-04-22. `assumption`.
- Quote (search excerpt): "showing 6% more weight loss, 3.5x faster meal logging, and twice as many foods logged" … "Say It! voice logging empowers members to simply describe their food to the app".

**C18.** Lose It! has moved its AI logging, and reportedly the barcode scanner, into Premium in 2026. Sources conflict on whether barcode is still free.
- Sources: https://www.amyfoodjournal.com/blog/lose-it-app-review (2026) and https://nutrola.app/en/blog/is-lose-it-worth-it-without-premium (2026; competitor blog). `assumption`.
- Quote (search excerpt): "As of August 2026, the barcode scanner is in Lose It!'s Premium feature set alongside Snap It photo logging and AI voice logging." Another excerpt says the free tier has "barcode scanning on all platforms".

**C19.** Lose It! recipes are servings-based: you set "Recipe Makes" or "Total Recipe Size" and the app works out per-serving values. A logged meal reappears under "previous meals" and can be re-added with one tap.
- Sources: https://eathealthy365.com/the-ultimate-guide-to-creating-recipes-in-lose-it/ (2025) and https://nutri.it.com/nutrition-diet-mastery-how-to-edit-recipes-in-lose-it-app (undated). `assumption`.
- Quote (search excerpt): "you can adjust the 'Total Recipe Size', which allows the app to automatically recalculate the nutritional information per serving" … "the entire meal populates under 'previous meals' and with one-click you can add it".

**C20.** Lose It! reports and extras:
- daily and weekly calorie summaries (free);
- "Insights" and "Patterns" correlations (Premium);
- intermittent fasting;
- GLP-1 support;
- a basic Apple Watch app (totals, water, quick-log of a few recent foods).
- Sources: https://nutriscan.app/blog/posts/lose-it-pricing-2026-free-vs-premium-2b4e921555 (2026; competitor blog), https://apps.apple.com/us/app/lose-it-calorie-counter/id297368629 (undated) and https://nutrola.app/en/blog/myfitnesspal-vs-lose-it-for-apple-watch-2026 (2026; competitor blog). `assumption`.
- Quote (search excerpt): "Lose It! Free includes daily and weekly calorie summaries" … "Patterns is a Premium feature that shows correlations between your food and exercise logging and your budget" … "water logging directly from the wrist, and quick-log for a limited set of recent foods."

**C21.** Lose It!'s top Reddit complaints in 2026: full-screen ads during logging, macros moved behind the paywall, a cluttered redesign, photo AI weaker than the AI-first apps, and database quality.
- Source: https://nutrola.app/en/blog/what-do-reddit-users-say-about-lose-it-2026 (2026; competitor blog; Reddit itself could not be fetched). `assumption`.
- Quote (search excerpt): "full-screen interstitial ads and banner ads interrupting logging flows" … "The lose it app now doesn't let you see your daily macros without a subscription." … "the latest update added a large panel of redundant buttons when adding food".

### Cronometer

**C22.** Cronometer handles recipe cooked yield properly: weigh the cooked recipe and enter it with "Set Cooked Recipe Weight". Recipes are "Servings Based" or "Weight Based", and "Advanced Serving Sizes" adds custom serving definitions.
- Source: https://support.cronometer.com/hc/en-us/articles/360018510311-Create-Custom-Recipe (undated). `assumption`.
- Quote (search excerpt): "After cooking, weigh your recipe and click the green text 'Set Cooked Recipe Weight' to enter in the actual weight of your recipe." … "turning the Advanced Serving Sizes toggle on and then clicking + Add Serving Size."

**C23.** Cronometer repeat logging covers "Copy Previous Day" (whole diary, not activity or biometrics), starred Favorites, and custom foods, recipes and meals.
- Sources: https://support.cronometer.com/hc/en-us/articles/360019003171-Mobile-Edit-Diary-Entries (undated) and https://cronometer.com/blog/custom-meals/ (undated). `assumption`.
- Quote (search excerpt): "To copy yesterday's entries to today, select "Copy Previous Day" in the vertical ellipsis menu."

**C24.** Cronometer launched Photo Logging for Gold subscribers on 8 Sep 2025. The AI suggests foods, the user reviews them, and the numbers come from Cronometer's verified database, not the model.
- Source: https://www.prnewswire.com/news-releases/cronometer-launches-premium-photo-logging-fast-verified-nutrition-tracking-for-real-life-302549752.html. Date: 2025-09-08. `assumption`.
- Quote (search excerpt): "lets users snap their meals, review AI suggestions, and log verified nutrition data" … "available exclusively to Gold subscribers".

**C25.** Cronometer's Apple Watch app shows the Energy Summary and nutrient scores and sends reminders to log food and to fast. iOS Home Screen widgets exist, and the fasting timer is Gold.
- Sources: https://support.cronometer.com/hc/en-us/articles/26644592588692-Apple-Watch-App, https://support.cronometer.com/hc/en-us/articles/4407693442324-iOS-Home-Screen-Widgets and https://cronometer.com/blog/fasting/ (all undated). `assumption`.
- Quote (search excerpt): "opt into notifications to get reminders for logging foods and fasting sent straight to your watch" … "To access the Fasting Timer feature, you need to upgrade to Cronometer Gold".

**C26.** Cronometer has no offline mode. It says the database is too large and its licences forbid storing it on the device, and users have asked for offline mode for years.
- Sources: https://forums.cronometer.com/discussion/5147/offline-mode and https://forums.cronometer.com/discussion/5429/limited-offline-mode-please-its-2022 (2022). `assumption`.
- Quote (search excerpt): "license agreements won't allow them to let you have them stored on your device."

**C27.** Cronometer keeps deleted entries in a Trash for 7 days, and swiping to delete shows an Undo message.
- Sources: https://support.cronometer.com/hc/en-us/articles/360018031812-Edit-Diary-Entries (undated) and https://nutrola.app/en/blog/how-to-recover-deleted-calorie-tracking-data (competitor blog). `assumption`.
- Quote (search excerpt): "Settings > Account > Trash where you can see deleted entries for up to 7 days" … "when you swipe left on an item in the diary, it shows a quick message to undo".

**C28.** Cronometer is praised for a verified, accurate database and nutrient depth, often as "best overall". Complaints: intrusive full-screen video ads in the free tier and a dense interface that puts newcomers off.
- Sources: https://www.garagegymreviews.com/best-calorie-counter-apps (2026) and https://marlvel.ai/intel-report/health-fitness/cronometer-calorie-counter (2026). `assumption`.
- Quote (search excerpt): "Cronometer is highlighted as the best overall calorie counter app, praised for its highly accurate food database" … "ads now "hijack the app for up to half a minute," appearing even in the middle of logging a meal" … "Cronometer's interface is information-dense".

**C29.** Cronometer is in English, Spanish (added Feb 2026), German and French. No Arabic.
- Source: https://x.com/cronometer/status/2023428480621240350. Date: 2026-02-16, decoded from the post ID. `assumption`.
- Quote (search excerpt): "Hola! Cronometer is now also in Spanish … We also have in app Language options for German, French and English (US & UK)."

### MacroFactor

**C30.** A third-party iOS research report on GitHub, which marks its own claims VERIFIED / PARTIALLY / UNVERIFIED, lists MacroFactor's capabilities:
- no free tier;
- logging by text search, barcode, label OCR, Quick Add, custom foods, recipes, copy/paste, favourites/history and an hourly timeline;
- AI photo, photo + text and uploaded-image analysis, with results landing on an editable plate;
- Apple Health sync, widgets and an Apple Watch app;
- coaching that does not depend on wearable burn estimates.
- Source: https://github.com/NicholasTamm/Macro-tracker/issues/1. Date: issue opened 2026-09-27; the report says it was verified 2026-09-23. `opened` (a secondary source that cites help.macrofactorapp.com and the App Store).
- Quote: "Text search, barcode, label OCR, Quick Add, custom foods, recipes, copy/paste, favorites/history, hourly timeline" … "Photo, photo + text, and uploaded-image analysis; results land on an editable plate" … "deliberately does not depend on wearable calorie-burn estimates" … "seven-day full trial and US$11.99 monthly … with no permanent free tier".

**C31.** MacroFactor's adaptive target estimates the user's real expenditure from logged intake and weight trend. A weekly check-in then proposes new calorie and macro targets. It needs about 2–3 weeks of consistent data to settle.
- Sources: https://macrofactor.com/expenditure-v3/ (undated) and https://www.amyfoodjournal.com/blog/macrofactor-review (2026). `assumption`.
- Quote (search excerpt): "MacroFactor estimates energy expenditure from logged intake and weight trend, then uses that estimate during weekly check-ins to recommend calorie and macro changes" … "about two to three weeks to settle on a useful initial expenditure estimate".

**C32.** MacroFactor's 2026 AI upgrade split logging into "Snap" (quick photo) and "Describe" (one or more photos plus text, such as portions, exclusions or a menu). It also added recipe-from-cookbook-photo, a label scanner that reads several languages, and custom foods built from a front-of-pack photo plus a label photo.
- Sources: https://macrofactor.com/mm-may-2026/ (May 2026) and https://macrofactor.com/annual-report-2026/ (2026). `assumption`.
- Quote (search excerpt): "Users can now upload multiple photos, combine photos with text, and give the AI more specific instructions" … "two modes: Snap … and Describe".

**C33.** MacroFactor's Apple Watch app (September 2025) logs food by voice. The user speaks naturally and confirms an editable suggestion with one tap.
- Sources: https://macrofactor.com/apple-watch/ and https://macrofactor.com/mm-sept-2025/ (Sept 2025). `assumption`.
- Quote (search excerpt): "You can speak naturally, for example "coffee with a croissant" or "chicken breast," and you'll get an editable suggestion you can confirm in a tap."

**C34.** MacroFactor publishes a "Food Logging Speed Index" counting the actions each common logging task takes in 21 apps. This is a vendor-run benchmark that MacroFactor wins.
- Logging through search: MacroFactor 10, Lose It! 13, MFP 15.
- Barcode: MacroFactor 5, MFP, Cronometer and Lose It! 7 each.
- Quick-add calories: MacroFactor 3.
- Totals: Cronometer 40, MacroFactor 24.
- Source: https://macrofactor.com/fastest-food-logger-2025/ (2025). `assumption`.
- Quote (search excerpt): "Logging through search (Case 1): MacroFactor (speed): 10, MyNetDiary: 11, FitGenie: 13, LoseIt: 13, MyFitnessPal: 15" … "Cronometer requires 40 total actions compared with MacroFactor's 24."

**C35.** MacroFactor has no true offline mode. During an outage it still searches history and cached results and lets the user add, edit and delete data.
- Source: https://help.macrofactorapp.com/en/articles/28-does-macrofactor-have-an-offline-mode (undated). `assumption`.
- Quote (search excerpt): "MacroFactor will likely never support a true offline mode" … "food search of history foods, food search of cached online results, and adding, editing, and deleting of any data in the app."

**C36.** MacroFactor's most-raised complaint is price, since there is no free tier. Its adaptive algorithm earns "deep respect". The app is in only a few languages (English, Japanese, German), with no Arabic.
- Sources: https://nutrola.app/en/blog/what-do-reddit-users-say-about-macrofactor-2026 (2026; competitor blog) and https://macrofactorapp.com/mm-december-2023/ (Dec 2023). `assumption`.
- Quote (search excerpt): "Price is the single most-raised concern across Reddit communities in 2026" … "deep respect for the adaptive algorithm" … "officially available in three languages: English, Japanese, and German".

### Cal AI (photo-first)

**C37.** Cal AI is built around the camera: snap, describe or scan a barcode, and it claims to use the depth sensor for volume. It has a "Milestones" tab with streak badges, Apple Health, water tracking and a deficit calculator.
- Sources: https://www.calai.app/ (undated) and https://screensdesign.com/showcase/cal-ai-calorie-tracker (undated). `assumption`.
- Quote (search excerpt): "you can snap a photo, describe your meal naturally, or scan a barcode" … "your phone's depth sensor calculates food volume" … "a dedicated 'Milestones' tab that serves as a trophy room, encouraging users to collect badges for streaks".

**C38.** Cal AI's scale and model: reported at 15M+ downloads and $30–50M annual revenue within about 18–24 months, with a hard paywall after a 3-day trial funded by heavy paid social ads.
- Source: https://x.com/zach_yadegari/status/2028473704359874652 (about 2026-03-02). `assumption`.
- Quote (search excerpt): "we broke $50m in ARR".
- `opened`: https://gist.github.com/Dwite/7ef215e3b9f8ba2bf5ef366bf3a5d657 (2026-04-01; cites no source): "Two teenagers built to $40M/yr revenue in 18 months" … "Hard paywall — can't use app without paying after 3-day trial" … "Spends $770K/month on Meta + TikTok ads".

**C39.** Corrections in Cal AI happen before saving: "Edit Items" lets the user change quantities, swap or remove detected foods and add missing ones. Saved "My Foods" are reportedly available offline. Caution: several unrelated apps are called "Cal AI", and these excerpts may describe a different one.
- Sources: https://www.getkalohealth.com/blog/is-cal-ai-accurate (undated) and https://tooldirectory.ai/tools/cal-ai (undated). `assumption`, low reliability.
- Quote (search excerpt): "opening Edit Items, where you'll see all the foods detected in the scan, allowing you to adjust quantities, edit recognized items, remove incorrect ones, or add missing foods" … "Once you have saved an item to 'My Foods', it will be available offline".

**C40.** Cal AI's top complaints:
- Photo estimates miss hidden oil, sugar and sauce, so mixed or home-cooked meals are under-counted by 25–50%.
- The "90% accuracy" claim cites no study.
- Billing friction at the end of the trial.
- Absurd outputs from text descriptions.
- Sources: https://www.trygaya.com/review/cal-ai-review (2026), https://justuseapp.com/en/app/6480417616/cal-ai-calorie-tracking/reviews (2026) and https://www.eesel.ai/blog/cal-ai-pricing (2026). `assumption`.
- Quote (search excerpt): "mixed meals showed 25-50% variance, typically undercounting calories" … "one Redditor noted the figure comes with "zero citations."" … "billing friction during the trial" … "18 ounce steak it will show me a calorie count of 23,000."

**C41.** Cal AI (App Store id6480417616) lists Arabic among 15 languages. No source confirmed RTL quality.
- Source: https://apps.apple.com/us/app/cal-ai-calorie-tracker/id6480417616 (undated). `assumption`.
- Quote (search excerpt): "supports English and 14 additional languages, including Arabic".

### SnapCalorie

**C42.** SnapCalorie measures portion volume with the iPhone LiDAR depth sensor and self-reports about 15% mean calorie error, weaker on mixed and regional dishes. It accepts photo plus voice note, barcode and text. A dietitian can review an entry for a fee, and the free tier is capped at 3 AI logs a day.
- Sources: https://nutriscan.app/blog/posts/nutriscan-vs-snapcalorie-ai-food-tracker-e2e26890e9 (2026; competitor blog) and https://macaron.im/blog/snapcalorie-review-2026 (2026). `assumption`.
- Quote (search excerpt): "accurate to about 15% mean caloric error by its own published number. However, it is best on single Western foods, weaker on mixed and regional dishes" … "A registered dietitian or trained reviewer can look over your entry for a fee" … "three AI logs per day is the hard wall".

**C43.** The research behind depth-based plate estimation is Google's Nutrition5k dataset (CVPR 2021): about 5,000 cafeteria plates with per-ingredient weighed mass and overhead RGB-D images. Its authors include Wade Norris, whom search excerpts name as a SnapCalorie co-founder; that link is an `assumption`.
- Source: https://github.com/google-research-datasets/Nutrition5k. Date: 2021. `opened`.
- Quote: "A dataset of visual and nutritional data for ~5k realistic plates of food captured from Google cafeterias using a custom scanning rig" … "overhead RGB-D images" … authors "Quin Thames, Arjun Karpur, Wade Norris, …".

### Yazio

**C44.** Yazio rebranded as "AI Calorie Tracker" and added AI photo logging in late 2025, at about ±20% accuracy in one third-party test. Its strengths are fasting timers (16:8, 18:6, 20:4, 5:2) with fasting streaks, a water tracker and a recipe library.
- Sources: https://nutrola.app/en/blog/is-yazio-still-good-2026 (2026; competitor blog) and https://bestcalorieapps.com/en/reviews/yazio/ (2026). `assumption`.
- Quote (search excerpt): "YAZIO added AI-powered photo logging in late 2025" … "around ±20% accuracy in testing" … "built-in timers, fasting log, fasting streak tracking".

**C45.** In Yazio, users build their own recipes with portions and log them by servings or by grams. Foods and recipes can be favourited, and "meals" and "recipes" are separate concepts.
- Sources: https://help.yazio.com/hc/en-us/articles/4406610369297-How-can-I-create-my-own-recipes-in-Yazio-and-access-edit-them, https://help.yazio.com/hc/en-us/articles/208942665-How-do-I-mark-a-food-item-or-recipe-as-a-favorite and https://help.yazio.com/hc/en-us/articles/360016755618-What-is-the-difference-between-a-meal-and-a-recipe-in-YAZIO (all undated). `assumption`.
- Quote (search excerpt): "enter either a specific number of servings or a quantity in grams".

**C46.** Yazio supports Siri Shortcuts, including logging food or water by voice, and has iPhone widgets. It is the only one of the six where Siri logging was found.
- Source: https://help.yazio.com/hc/en-us/articles/360000582838-Does-YAZIO-support-Siri-Shortcuts- (undated). `assumption`.
- Quote (search excerpt): "YAZIO supports Siri Shortcuts" … "can track water or foods with voice commands using Siri."

**C47.** Yazio's Arabic support is disputed. One excerpt says the App Store lists Arabic. Blogs from Arabic-first rival apps say Arabic is weak and lacks RTL.
- Sources: https://apps.apple.com/sa/app/ai-calorie-tracker-by-yazio/id946099227 (undated) and https://suuapp.com/blog/en/best-calorie-counting-app.html (2026; competitor blog). `assumption`.
- Quote (search excerpt): "the App Store listings show that Arabic (العربية) is included" … "the app does not have RTL (right-to-left) support for Arabic".

**C48.** Yazio's recurring complaints are about billing: charges continuing after cancellation, an annual charge shown as monthly, and hard-to-find subscription controls.
- Source: https://www.sikayetvar.com/en/yazio-us (undated complaints site). `assumption`.
- Quote (search excerpt): "despite canceling their subscriptions, charges have continued to be attempted and processed".

### Cross-cutting

**C49.** Research on AI photo calorie estimates agrees on three points. Error is larger for mixed and culturally specific dishes. Portion size is the weakest step. Telling the model what the ingredients are roughly halves the error compared with a photo alone.
- Sources: https://www.mdpi.com/2072-6643/16/15/2573 (Nutrients 2024) and https://fitia.app/learn/article/ai-calorie-photo-apps-accuracy-2026/ (2026; competitor blog summarising studies). `assumption`.
- Quote (search excerpt): "AI-enabled image recognition accurately identified food components, but energy estimation for mixed and cultural dishes was poor" … "within roughly 30% of its true value from a photo alone, and within roughly 14% when the user also tells the app what the ingredients are."

**C50.** An open benchmark on GitHub tests 231 weighed reference meals. For manual logging it reports calorie error of MacroFactor 4.9%, Cronometer 6.7%, Lose It! 9.6% and MFP 11.7%, which ranks curated databases above crowdsourced ones. It does not test Cal AI, SnapCalorie or MFP Meal Scan. Caution: its top-ranked system (PlateLens) also publishes marketing blogs, and no conflict-of-interest statement was found, so treat the numbers as directional.
- Source: https://github.com/foodvision-bench/foodvision-bench. Date: v0.3.6, September 2026. `opened`.
- Quote: "231 USDA-weighed reference meals" … "MacroFactor | 4.9%" … "Cronometer | 6.7%" … "Lose It! | 9.6%" … "MyFitnessPal | 11.7%" … "manual-assisted replication" … "directional, not definitive".

**C51.** A hobby project on GitHub summarises what each tracker is best at:
- MFP: copy yesterday, quick add, multi-add, streaks, recipe importer;
- Cronometer: nutrient depth and a fasting timer;
- MacroFactor: adaptive expenditure and a weekly check-in;
- Lose It!: budget framing, water and patterns;
- Yazio: fasting plans and a pleasant UI.
- Source: https://github.com/Dhruv123-123/cronofree (undated). `opened` (low authority; corroboration only).
- Quote: "Enormous food database, barcode scanning, recipes, meals, copy yesterday, quick add, multi-add, exercise database, streaks" … "Adaptive expenditure from intake + weight trend, weekly check-in that updates targets" … "Fasting plans, pleasant UI".

**C52.** The likely real competitors in the launch market are Arabic-first apps, which the big six are not:
- Kam Calorie: voice-first in Arabic and English, built for Egyptian dishes (koshari, ful) and Gulf dishes (kabsa, mandi);
- Kilo: a Saudi food database plus 10,000+ local restaurant menu items;
- Saaraty, HealthyBytes and Zorest.
- Sources: https://www.kamcalorie.app/en, https://kiloappsa.com/ and https://zorest.com/blog/best-arabic-calorie-counter-app (2026). `assumption`; these are vendor claims.
- Quote (search excerpt): "voice-first app that records meals in Arabic or English … including Egyptian dishes like Koshari and Ful Medames, and Saudi & Gulf dishes like Kabsa and Mandi" … "over 10,000+ menu items from local restaurants".

**C53.** Export: Cronometer gives every user a free CSV export, and MacroFactor's export is open to all subscribers. MFP's export is Premium only. Free export is praised.
- Source: https://swoodie.app/blog/calorie-tracker-data-export-2026 (2026; competitor blog). `assumption`.
- Quote (search excerpt): "MacroFactor and Cronometer give every user a free, in-app, machine-readable export, while MyFitnessPal has the most complete file but locks it behind Premium."

**C54.** Hands-free Siri logging is rare among the leaders: no Siri logging in MFP or Cal AI, and none in Lose It! or Cronometer. Smaller apps (Nutrola, MenuMeld, Nutix) advertise App Intents logging.
- Source: https://nutrola.app/en/blog/is-there-a-nutrition-app-that-works-with-siri-google-assistant (2026; competitor blog). `assumption`.
- Quote (search excerpt): "Lose It! offers voice search but not hands-free meal logging, with no Siri Shortcuts integration. Cronometer does not support Siri Shortcuts. As of 2026, MyFitnessPal and Cal AI do not offer Siri logging".

**C55.** Apple's own guidance for health apps says App Intents can map "logging nutrition" to a shortcut and to the Action button. WidgetKit shows calories or streaks. A Live Activity should show an "innocuous summary" when its data is sensitive.
- Source: https://developer.apple.com/health-fitness/ (undated). `opened`.
- Quote: "You can map actions like starting a workout, logging nutrition, or viewing progress to a shortcut" … "relevant data — like daily steps, calories, or workout streaks" … "display an innocuous summary and let people tap the Live Activity to access the sensitive information in your app".

**C56.** HealthKit has a standard type for consumed energy that trackers write to, which is how MFP and Cronometer can write out to Apple Health (C12).
- Source: https://developer.apple.com/documentation/healthkit/hkquantitytypeidentifier/dietaryenergyconsumed (undated). `opened`.
- Quote: "A quantity sample type that measures the amount of energy consumed."

**C57.** Among these apps, "planning" means discovering recipes or generating meal plans: MFP's Meal Planner and Plan tab (C2, C3) and Yazio's recipe library (C44). None of the searches in this run found an app that takes a photo of a shared table plus a calorie cap and macro limits and proposes counts with a solver, as our FRD Journey C does. This absence is `assumption`, not proven; local apps (C52) were not examined in depth.
- Sources: C2, C3, C44.
- Quote: n/a (absence of evidence).

**C58.** GLP-1 medication tracking became a mainstream tracker feature in 2025–26, in MFP (C2) and Lose It! (C20).
- Source: https://apps.apple.com/us/app/lose-it-calorie-counter/id297368629 (undated). `assumption`.
- Quote (search excerpt): "Lose It! now includes GLP-1 support, allowing you to log your medication, view estimated GLP-1 levels".

---

## What customers praise and complain about most

| | Praise | Complaints |
|---|---|---|
| MyFitnessPal | Biggest database: "highest odds of finding exactly what users ate" (search excerpt for C28's source, `assumption`); copy and quick-log tools (C5, C51) | Wrong crowdsourced entries (C9); paywalled barcode, scan and voice (C7); April 2026 redesign added taps and removed item copy (C4) |
| Lose It! | Simple budget framing; AI logging is faster (C17, C51) | Ads in the logging flow; macros and barcode paywalled; clutter; weak photo AI (C21, C18) |
| Cronometer | Verified data and accuracy; nutrient depth; free export (C28, C50, C53) | Full-screen video ads in the free tier; dense UI; no offline (C28, C26) |
| MacroFactor | Adaptive algorithm; fastest logging; editable AI plate (C36, C34, C30) | Price and no free tier; few languages (C36) |
| Cal AI | One-step camera logging; huge reach (C37, C38) | Under-counts mixed and oily meals; unsupported accuracy claim; trial billing (C40) |
| SnapCalorie | Most credible photo accuracy (depth) (C42) | 3 AI logs a day on free; weaker on regional dishes (C42) |
| Yazio | Fasting plans; pleasant UI (C44, C51) | Billing and cancellation (C48); mediocre photo AI (C44) |

The pattern across all of them: **speed of the repeat log** and **trust in the numbers** drive praise. **Paywalled basics**, **ads in the logging flow** and **redesigns that add taps** drive anger.

---

## Features the competitors have that our FRD does not mention

Checked against `brief/frd-v1.0.md` by keyword: widget, Siri, intent, watch, fasting, streak, notification/reminder, favourite, weekday all appear 0 times. "Water" appears only as cooking water. "Meal template" is named in §1.5 and §2.1 but no FR defines it. HealthKit is import-only (FR-062).

| Feature (who has it) | Verdict for the eater's core job (map §1: re-use a defined portion and log in seconds, trust the totals) |
|---|---|
| **Copy previous day / copy meal / bring last meal forward** (MFP C5, Cronometer C23, Lose It! C19) | **Serves the core job.** It is the cheapest repeat log there is. It belongs in WF-3 as a command that writes N new consumption entries, idempotent and undoable as one batch. |
| **Saved meal / meal template as a defined artifact** (MFP "My Meals" C5, Yazio meals C45) | **Serves the core job.** The FRD's ≥70% week-two reuse target counts "meal template" but never defines one. It needs a definition (versioned like a Composite?) in WF-2 and WF-3. |
| **Siri / App Intents logging** ("log three cheese bites" without opening the app) (Yazio C46; requested of MFP C13; Apple supports it C55) | **Serves the core job.** It is the same consume command as WF-3 from a new surface. Rare among the leaders (C54), so it can set us apart. |
| **Interactive Home and Lock Screen widgets** (MFP C14, Cronometer C25, MacroFactor C30) | **Serves the core job.** It gives remaining kcal at a glance and one-tap re-log of a recent unit. Show only a neutral summary on the Lock Screen (C55). |
| **Apple Watch logging** (MacroFactor voice C33, Lose It! recent quick-log C20) | **Partly.** Voice-to-unit on the wrist fits Journey B, but it is a new surface. P1 candidate. |
| **Write nutrition to Apple Health** (MFP, Cronometer C12; HealthKit type C56) | **Serves the core job, a little.** The eater's intake reaches other apps and doctors. It is cheap and consented, and costs no extra taps. |
| **Logging reminders / notifications** (Cronometer C25) | **Serves habit adherence** (FRD §1.2 "habit adherence"), but the FRD names no reminder. Keep it a user setting, default off. |
| **Per-weekday targets** (MFP Premium C11) | **Partly.** Weekend or Friday eating is real. Model it as a configuration point on the Target, not as new code. |
| **Barcode scanning** (all; paywalling it caused anger C7) | **Partly.** The FRD excludes a "barcode-first journey" but leaves the scanner itself unclear. For packaged Saudi and Egyptian products it shortens unit creation (WF-2). Not the main path. |
| **Recipe import from URL or from a photo of a written recipe** (MFP importer C51, MacroFactor C32) | **Partly.** FRD recipes come from text or voice; a recipe photo is a cheap extra input to WF-4 → WF-2. |
| **Streaks / badges** (MFP C14, Cal AI C37, Yazio fasting streak C44) | **Mostly noise.** A streak can push people to log fake or zero days, which fights FR-073 (missing days are not zero). Showing logging coverage serves the job better. |
| **Fasting timer** (MFP, Cronometer, Lose It!, Yazio) | **Noise** for the core job. |
| **Water tracking** (MFP, Lose It!, Cal AI, Yazio) | **Mostly noise** for calorie accounting. Caloric drinks such as laban are already units ("sip", "cup"). Only add it if the owner wants hydration. |
| **AI coach chat** (MFP Summer 2026 C3) | **Noise and a risk.** It conflicts with FRD §2.1 "not a chat window with hidden state". |
| **GLP-1 medication log** (MFP C2, Lose It! C58) | **Out of scope.** The FRD excludes medication dosing. Note it as a market trend. |
| **Recipe discovery / meal plans / grocery** (MFP Premium+, Yazio) | **Noise.** Our "plan" is a constrained plan from foods already on hand (C57). |
| **Paid dietitian review of an entry** (SnapCalorie C42) | **Noise for v1.** Our nutrition approver reviews reference foods, not each person's diary. |
| **Live Activities** (MacroFactor, for workouts) | **Noise** for eating. |

---

## Implications for our map

1. **Make speed a tested contract.** The market keeps score in taps (C34). MFP lost its rating by adding taps (C4), and AI logging's real gain is speed (C17). Give WF-3 an acceptance check: tap count per path (recent unit, voice, copy day). Treat any added tap as a regression. (C4, C17, C34)
2. **Add repeat-logging artifacts the FRD names but does not define.** Specify "copy previous day/meal" and a versioned **meal template** as ledger commands and artifacts in WF-2 and WF-3. Every leader has them, and the ≥70% week-two reuse target depends on them. (C5, C19, C23, C45, C51)
3. **Add new surfaces to the same consume command, not new logic.** App Intents/Siri, an interactive widget, and later the Watch should all call WF-3's idempotent consume command. Siri logging is rare among the leaders, so it can set us apart. (C13, C33, C46, C54, C55)
4. **Keep AI as a reviewed draft and add "photo + words".** Evidence shows photo-only estimates under-count oily, mixed and cultural dishes, and adding ingredient text roughly halves the error. Leaders are converging on an editable plate and Snap/Describe (photo + text). This supports FR-033/035 and adds a Describe mode to WF-4. (C24, C30, C32, C40, C42, C49)
5. **Make cooked yield a first-class step.** Only Cronometer has a proper "Set Cooked Recipe Weight", and MFP users rely on a workaround. WF-2 should prompt "weigh the pot" in the recipe flow and record the yield as evidence. (C6, C19, C22)
6. **Trust in numbers is the main source of praise and complaint.** MFP's crowdsourced database is its top complaint; Cronometer's and MacroFactor's curated data rank best on accuracy. Keep approver-curated sources (WF-10) and make evidence badges visible in WF-3 and WF-8. (C9, C28, C50)
7. **Arabic is open ground, but the real rivals are local.** None of the six offers proven Arabic RTL. Arabic-first apps (Kam Calorie voice with Egyptian dishes; Kilo with a Saudi restaurant database) are the true competitors and need a closer look next cycle. (C16, C29, C36, C41, C47, C52)
8. **Offline and correction history set us apart.** Incumbents are weak offline (Cronometer none, MacroFactor cache only, MFP partial), and their history editing is delete plus a few seconds of Undo or a 7-day trash. Our outbox and our append-only correct/void/restore ledger go further. Keep both P0 and demo them. (C10, C15, C26, C27, C35)
9. **Decide early what stays free.** Paywalling barcode, macros, export or AI logging, and heavy ads, cause the loudest anger. Cronometer's free export is praised, which supports FR-075/079 ("never paywalled"). The proposed free/paid split (§23.3) should keep repeat logging and reports free. (C7, C18, C21, C28, C48, C53)
10. **Leave adaptive targets at P1 but collect their data now.** MacroFactor's most praised feature needs 2–3 weeks of intake plus a weight trend. WF-7 and WF-8 should capture weight and coverage from day one, and writing dietary energy back to Apple Health is cheap value. (C12, C31, C36, C56)
