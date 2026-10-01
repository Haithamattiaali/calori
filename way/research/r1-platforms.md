# R1 · What is current today on the platforms Sips & Bytes is built on

Research cycle 1, question 3 and the platform half of question 4 of `map-first.md` §7. Researched and written 2026-10-01. Checks the brief's platform claims (FRD §7, §15, §16).

## How to read this file

**Access limit in this run.** The network egress policy blocked the vendor documentation sites I would normally cite: ai.google.dev, docs.cloud.google.com, firebase.google.com, blog.google, deepmind.google, support.apple.com, www.apple.com, docs.github.com, wikipedia, openrouter.ai and the press blogs. The hosts I could open were **github.com** (and its raw files and git), **pypi.org**, **registry.npmjs.org** and **developer.apple.com** (Apple's doc pages through their JSON form at `developer.apple.com/tutorials/data/documentation/<path>.json`). So for Google I used Google's **own GitHub repositories**:

- `googleapis/python-genai`: the SDK, its README, its CHANGELOG and its generated model list.
- `google/skills`: Google Cloud's skill files for the Agent Platform.
- `google-gemini/gemini-skills`: the Gemini API team's skill files, which state the "current models".

These repositories are vendor-authored, but they are not the model pages themselves.

Each finding has one of two labels:
- **`opened`**: I opened the source in this run (a page, file, registry JSON or git tag), and the quote is its text.
- **`assumption`**: I could not open the source. The claim comes from a web-search excerpt or from general knowledge. Verify it before relying on it.

Every source was accessed on **2026-10-01**. Where a finding gives a second date, that is the date the source itself carries.

---

## A · Gemini (models, capabilities, SDK, retention)

**P1 · `gemini-3.8-flash` is Google's current Flash model and its stated production example, on both the Gemini API and Agent Platform.** The brief's §16.1 claim is **confirmed**. `opened`
- https://github.com/google/skills/blob/main/skills/cloud/gemini-api/SKILL.md (Agent Platform skill, commit d5d9052 of 2026-09-30) · 2026-10-01 · "Use `gemini-3.8-flash` for fast, balanced performance, multimodal (1M tokens)" … "For production environments, consult the documentation for stable model versions (e.g. `gemini-3.8-flash`)."
- https://github.com/google-gemini/gemini-skills/blob/main/skills/gemini-api-dev/SKILL.md (Gemini API skill) · 2026-10-01 · "### Current Models (Use These) — `gemini-3.8-flash`: 1M tokens, fast, balanced performance for agentic and multimodal tasks"

**P2 · `gemini-3.5-flash-lite` exists. It is the latest Flash-Lite and is listed on both surfaces.** The brief's claim is **confirmed**. One correction: it is not "the" cheap model. `gemini-3.1-flash-lite` is still listed too, and 3.5 Flash-Lite replaces it. `opened`
- https://github.com/google-gemini/gemini-skills/blob/main/skills/gemini-api-dev/references/migration.md · 2026-10-01 · "`gemini-2.5-flash-lite` or `gemini-3.1-flash-lite` → `gemini-3.5-flash-lite` · Latest Flash-lite with Interactions API support"
- https://github.com/google/skills/blob/main/skills/cloud/gemini-api/SKILL.md · 2026-10-01 · "Use `gemini-3.5-flash-lite` for high-frequency, lightweight tasks (1M tokens)"
- Release on 21 July 2026 and retirement "July 21, 2027 or later": `assumption`. This comes from the search excerpt for https://ai.google.dev/gemini-api/docs/models/gemini-3.5-flash-lite (blocked).

**P3 · Google released Flash versions about once a month. The SDK added 3.5 Flash in May 2026, 3.7 Flash in August and 3.8 Flash on 2 September 2026.** `opened`
- https://github.com/googleapis/python-genai/blob/main/CHANGELOG.md · 2026-10-01 · "## [2.22.0] (2026-09-02) … Add Gemini 3.8 Flash model to SDKs and update Flash model descriptions"; "## [2.18.1] (2026-08-13) … Add gemini-3.7-flash"; "## [2.6.0] (2026-05-21) … Add gemini-3.5-flash"
- The GA date of 2 September 2026 and the pricing of $0.75/$3.75 per 1M tokens until 31 December 2026: `assumption` (search excerpt for codersera.com and the ai.google.dev changelog, both blocked).

**P4 · Floating aliases exist (`gemini-flash-latest`, `gemini-flash-lite-latest`), and the SDK's own README examples use them. The SDK's generated model list includes 3.5/3.6/3.7/3.8-flash and 3.1-flash-lite but not 3.5-flash-lite, which the SDK accepts as an unrecognised string.** `opened`
- https://github.com/googleapis/python-genai/blob/main/google/genai/_gaos/types/interactions/model.py · 2026-10-01 · `# Latest release of Gemini Flash` / `"gemini-flash-latest",` … `"gemini-3.8-flash",` … `UnrecognizedStr,`

**P5 · Gemini 3.8 Flash and 3.5 Flash-Lite drop the sampling knobs and change how thinking is set.** When you migrate, Google says to remove `temperature`, `top_p` and `top_k`, and to use `thinking_level` instead of `thinking_budget`. Thinking defaults to MEDIUM on 3.8 Flash and MINIMAL on 3.5 Flash-Lite. `opened`
- https://github.com/google-gemini/gemini-skills/blob/main/skills/gemini-api-dev/references/migration.md · 2026-10-01 · "Removed `temperature`, `top_p`, `top_k` from config" / "Replaced `thinking_budget` with `thinking_level` (`minimal`, `low`, `medium`, `high`)"
- https://github.com/google/skills/blob/main/skills/cloud/gemini-api/references/advanced_features.md · 2026-10-01 · "`gemini-3.8-flash` (default `MEDIUM`). `gemini-3.5-flash-lite` defaults to `MINIMAL`."

**P6 · Older models are legacy.** `gemini-2.0-*` and `1.5-*` are deprecated. The Gemini API team now also calls `2.5-*` "legacy and deprecated". The Agent Platform skill allows 3.5/3.6/3.7 Flash and 2.5 "only if explicitly requested". `opened`
- https://github.com/google-gemini/gemini-skills/blob/main/skills/gemini-api-dev/SKILL.md · 2026-10-01 · "Models like `gemini-2.5-*`, `gemini-2.0-*`, `gemini-1.5-*` are **legacy and deprecated**."

**P7 · Image understanding is supported.** Images go in as inline bytes or as a Cloud Storage URI, together with text, in one request. `opened`
- https://github.com/googleapis/python-genai/blob/main/README.md · 2026-10-01 · "If your image is stored in your local file system, you can read it in as bytes data and use the `from_bytes` class method to create a `Part` object."
- https://github.com/google/skills/blob/main/skills/cloud/gemini-api/SKILL.md · 2026-10-01 · "**Multimodal understanding** - Process images, audio, video, and documents"

**P8 · Structured output works with a JSON Schema or a Pydantic schema.** You pass `response_mime_type='application/json'` and `response_json_schema=...`. In the Interactions API, this moves to a top-level `response_format`. `opened`
- https://github.com/googleapis/python-genai/blob/main/README.md · 2026-10-01 · "`response_json_schema=CountryInfo.model_json_schema()`"
- https://github.com/google/skills/blob/main/skills/cloud/gemini-api/references/structured_and_tools.md · 2026-10-01 · "# response.text is guaranteed to be valid JSON matching the schema". The FRD §7 point still applies: the schema guarantees the format, not the facts.

**P9 · The Python SDK is `google-genai` 2.26.0.** It was uploaded on 30 September 2026, needs Python ≥ 3.10, and lives at github.com/googleapis/python-genai. 2.0.0 was released on 7 May 2026. `opened`
- https://pypi.org/pypi/google-genai/json · 2026-10-01 · `"version": "2.26.0"`, `"requires_python": ">=3.10"`, upload `2026-09-30T22:53:21Z`, `"Homepage": "https://github.com/googleapis/python-genai"`

**P10 · The same SDK targets the Gemini API (API key) and Agent Platform (`enterprise=True`).** `vertexai=` survives as a legacy alias. The older SDKs are deprecated: `google-cloud-aiplatform`, whose PyPI version is 2.3.0, and `google-generativeai`. `opened`
- https://github.com/googleapis/python-genai/blob/main/README.md · 2026-10-01 · "It supports the Gemini Developer API and Gemini Enterprise Agent Platform APIs." / `genai.Client(enterprise=True, project='your-project-id', location='global')` / env `GOOGLE_GENAI_USE_ENTERPRISE=true`
- https://github.com/googleapis/python-genai/blob/main/google/genai/client.py · 2026-10-01 · "vertexai (bool): Legacy flag for `enterprise`."
- https://github.com/google/skills/blob/main/skills/cloud/gemini-api/SKILL.md · 2026-10-01 · "Legacy SDKs like `google-cloud-aiplatform`, `@google-cloud/vertexai`, and `google-generativeai` are deprecated."

**P11 · Vertex AI is now "Gemini Enterprise Agent Platform", and its default location is `global`, which routes to whichever region has capacity.** Requests stay in one region only if you set that region explicitly. `opened`
- https://github.com/google/skills/blob/main/skills/cloud/gemini-api/SKILL.md · 2026-10-01 · "Agent Platform (full name Gemini Enterprise Agent Platform) was previously named "Vertex AI"" / "use `location="global"` to access the global endpoint, which provides automatic routing to regions with available capacity."
- Whether Gemini 3.8 Flash is served in a Gulf region such as `me-central2`: `assumption` (unverified; the locations page on docs.cloud.google.com was blocked).

**P12 · The Interactions API is now the Gemini API's main surface, and `generateContent` still works. On Agent Platform, however, Interactions cannot call a base model directly.** The Gemini API's breaking change of May 2026 (`Api-Revision: 2026-05-20`, SDK ≥ 2.0) affects Interactions only. `opened`
- https://github.com/googleapis/python-genai/blob/main/CHANGELOG.md · 2026-10-01 · "Note: The breaking changes are only in interactions. `GenerateContent` usage in unaffected."
- https://github.com/google/skills/blob/main/skills/cloud/gemini-interactions-api/SKILL.md · 2026-10-01 · "On Gemini Enterprise Agent Platform (GEAP), direct/base-model calls (`model="..."`) via the Interactions API are **not supported yet**."

**P13 · Interactions are stored by default: 55 days on the paid tier, 1 day on the free tier.** `store=False` turns storage off, and with it `previous_interaction_id` and background runs. `opened`
- https://github.com/google-gemini/gemini-skills/blob/main/skills/gemini-api-dev/SKILL.md · 2026-10-01 · "Interactions are **stored by default** (store=True …). Paid tier retains for 55 days, free tier for 1 day."
- https://github.com/google/skills/blob/main/skills/cloud/gemini-interactions-api/SKILL.md · 2026-10-01 · "Passing `store=False` disables server-side retention and therefore also disables `previous_interaction_id` and `background`"

**P14 · Agent Platform keeps a zero-data-retention page, which changed on 29 September 2026: Deep Research session data is now kept 7 days.** The details below are `assumption` (from the search excerpt for https://docs.cloud.google.com/gemini-enterprise-agent-platform/resources/zero-data-retention, blocked): Gemini caches inputs and outputs in memory with a 24-hour TTL that can be turned off per project; abuse-monitoring prompt logging needs an exception request; zero retention needs `store=false`. The page update itself is `opened`:
- https://github.com/esamatic/ai-tietoturva/issues/14 (third-party change tracker) · 2026-10-01 · "Google stores prompts and session data…via the Interactions API for a period of seven (7) days."

**P15 · Gemini now has a dedicated speech-to-text model, `gemini-3.5-transcribe`.** It does automatic language detection, diarization and word timestamps. `opened`. Its quality on Arabic and Egyptian/Saudi code-switching: `assumption` (no language list found).
- https://github.com/google-gemini/gemini-skills/blob/main/skills/gemini-api-dev/references/migration.md · 2026-10-01 · "Legacy audio understanding / ASR → `gemini-3.5-transcribe` · Dedicated speech-to-text with auto language detection, diarization, word timestamps, and smart formatting"

## B · iOS (versions, frameworks, on-device AI, speech, HealthKit, Firebase)

**P16 · The current releases are iOS 27.0.1 (28 Sep 2026) and Xcode 27 (14 Sep 2026).** Xcode 27.1 is in beta, and Xcode 27.2 and iOS 27.2 are at beta 2. `opened`
- https://developer.apple.com/news/releases/rss/releases.rss · 2026-10-01 · "Mon, 28 Sep 2026 | iOS 27.0.1 (24A446)"; "Mon, 14 Sep 2026 | Xcode 27 (27A266a)"; "Mon, 28 Sep 2026 | Xcode 27.2 beta 2 (27B5028f)"

**P17 · Xcode 27 ships Swift 6.4 and the iOS 27 SDK, needs macOS Tahoe 26.6 or later, and can deploy to iOS 15–27.** `opened`
- https://developer.apple.com/documentation/xcode-release-notes/xcode-27-release-notes · 2026-10-01 · "Xcode 27 includes Swift 6.4 and SDKs for iOS 27 … Xcode 27 requires a Mac running macOS Tahoe 26.6 or later."
- https://developer.apple.com/xcode/system-requirements · 2026-10-01 · Deployment Targets "iOS 15–27"

**P18 · The App Store already requires the iOS 26 SDK, built with Xcode 26 or later.** `opened`
- https://developer.apple.com/news/upcoming-requirements/ · 2026-10-01 · "Since April 28, 2026 … Apps uploaded to App Store Connect must be built with Xcode 26 or later using an SDK for iOS 26"

**P19 · Adoption: 86 % of iPhones introduced in the last four years run iOS 26 (measured 7 June 2026). Apple has not published a figure for iOS 27 yet.** `opened`
- https://developer.apple.com/support/app-store/ · 2026-10-01 · "As measured by devices that transacted on the App Store on June 7, 2026 … 86% of all devices introduced in the last four years use iOS 26."

**P20 · Observation needs iOS 17, and so does SwiftData. SwiftData's live observers (`ResultsObserver`, `HistoryObserver`, sectioned `@Query`) are new in iOS 27 only.** History tracking (`HistoryDescriptor`) needs iOS 18. `opened`
- https://developer.apple.com/documentation/swiftdata/resultsobserver · 2026-10-01 · platforms "iOS 27.0"; "Observes and tracks changes to a collection of persistent models in a model context."
- https://developer.apple.com/documentation/observation · 2026-10-01 · platforms "iOS 17.0"

**P21 · GRDB, the SQLite toolkit, is at 7.11.1 (tag dated 18 June 2026).** It needs iOS 13+ and Swift 6.1+ / Xcode 16.3+. `opened`
- https://github.com/groue/GRDB.swift (tag `v7.11.1`, README) · 2026-10-01 · "**Requirements**: iOS 13.0+ / macOS 10.15+ … SQLite 3.20.0+ • Swift 6.1+ / Xcode 16.3+"

**P22 · App Intents puts app actions into Siri, Spotlight, Shortcuts and widgets. An App Shortcut phrase must contain the app name and can carry only one parameter.** `opened`
- https://developer.apple.com/documentation/appintents · 2026-10-01 · "Make content and actions discoverable by Apple Intelligence and support system experiences like Siri, Spotlight, Shortcuts, and widgets."
- https://developer.apple.com/design/human-interface-guidelines/app-shortcuts · 2026-10-01 · "An App Shortcut can include a single optional value, or parameter" / "You have to include your app name"
- That a phrase parameter must be an `AppEntity` or `AppEnum`, so a free number like "three" cannot be one: `assumption` (WWDC23 guidance, not reopened). Whether App Shortcut phrases work in Arabic with Siri: `assumption`.

**P23 · Some App Intents APIs match our flows: `UndoableIntent` (iOS 26), `SnippetIntent` (iOS 26), and `LongRunningIntent` and `IntentModes` (iOS 27).** Apple's App Intents schema domains do not include health or nutrition. `opened`
- https://developer.apple.com/documentation/updates/appintents · 2026-10-01 · "Reverse the effect of an app intent's action by adopting [UndoableIntent]."
- https://developer.apple.com/documentation/appintents/app-schema-domains · 2026-10-01 · Domains listed are audio, calendar, camera, clock, mail, maps, messages, notes, phone, photos, reminders, search, assistant, visual-intelligence, books, browser, files, journaling, … (no health or food domain)

**P24 · Widget and Live Activity buttons run app intents without opening the app. On a locked device they stay inactive until the person authenticates.** `opened`
- https://developer.apple.com/documentation/widgetkit/adding-interactivity-to-widgets-and-live-activities · 2026-10-01 · "On a locked device, buttons and toggles are inactive and the system doesn't perform actions unless a person authenticates and unlocks their device."
- https://developer.apple.com/documentation/activitykit · 2026-10-01 · "keep track of an event or activity over several hours"

**P25 · The Foundation Models framework (iOS 26+) runs the on-device model and has guided generation through `@Generable`.** On iOS 27 it can also use Private Cloud Compute (`PrivateCloudComputeLanguageModel`) and any `LanguageModel` provider. The on-device model changes with OS updates, and it needs an Apple Intelligence device in a supported region. `opened`
- https://developer.apple.com/documentation/foundationmodels · 2026-10-01 · "With the `@Generable` macro … the framework provides strong guarantees that the model generates instances of your type." / "people need a device that supports Apple Intelligence"
- https://developer.apple.com/documentation/foundationmodels/systemlanguagemodel · 2026-10-01 · "Currently, there are 3 model versions that align with: … 26.0 - 26.3 … 26.4 … 27.0" / "Model availability depends on whether the device and region supports Apple Intelligence."

**P26 · Foundation Models covers only the languages Apple Intelligence supports. Arabic is not among them as of iOS 27.** The language rule is `opened`. The fact that Arabic is missing is an `assumption`: it comes from search excerpts ("sixteen languages, and Arabic is not among them" as of the iOS 27 launch, 14 Sep 2026), and support.apple.com/121115 was blocked.
- https://developer.apple.com/documentation/foundationmodels/supporting-languages-and-locales-with-foundation-models · 2026-10-01 · "the same model understands and generates text in any language that Apple Intelligence supports" / "If the model detects a language it doesn't support, the session throws [unsupportedLanguageOrLocale]" / "Guardrails … are only for supported languages and locales."

**P27 · SpeechAnalyzer / SpeechTranscriber (iOS 26+) is the new on-device speech-to-text. Apple confirmed that its Arabic support was listed in error.** `DictationTranscriber` uses the same on-device models as `SFSpeechRecognizer`. `opened`
- https://developer.apple.com/documentation/speech/speechtranscriber · 2026-10-01 · "Use the [isAvailable] or [supportedLocales] properties to see if the current device supports the speech-to-text models"
- https://developer.apple.com/forums/thread/797835 · 2026-10-01 (Apple reply, Jan 2026) · "`SpeechTranscriber` erroneously listed Arabic as "supported". It actually wasn't."
- https://developer.apple.com/documentation/speech/dictationtranscriber · 2026-10-01 · "This transcriber does not support languages or locales that `SFSpeechRecognizer` only supports via network access."
- Whether iOS 27 added Arabic, or whether `SFSpeechRecognizer` has on-device ar-SA/ar-EG: `assumption` (unverified; check at runtime with `supportsOnDeviceRecognition`).

**P28 · `SFSpeechRecognizer` handles one language per recognizer. Server recognition has a one-minute limit, and Apple advises against sending health data to it.** `opened`
- https://developer.apple.com/documentation/speech/sfspeechrecognizer · 2026-10-01 · "Each speech recognizer supports only one language" / "Plan for a one-minute limit on audio duration." / "Don't send passwords, health or financial data, and other sensitive speech for recognition."

**P29 · HealthKit can read active energy, workouts and body mass, and can write dietary energy and macros, grouped per food as a `food` correlation.** `opened`
- https://developer.apple.com/documentation/healthkit/hkquantitytypeidentifier/dietaryenergyconsumed · 2026-10-01 · "A quantity sample type that measures the amount of energy consumed." (the same holds for `dietaryProtein`, `dietaryCarbohydrates` and `dietaryFatTotal`, all iOS 8+)
- https://developer.apple.com/documentation/healthkit/hkcorrelationtypeidentifier/food · 2026-10-01 · "Food correlation types combine any number of nutritional samples into a single food object."
- https://developer.apple.com/documentation/healthkit/hkquantitytypeidentifier/activeenergyburned · 2026-10-01 · "Active energy is the energy that the user has burned due to physical activity and exercise." (with `bodyMass` and `HKWorkoutType`)

**P30 · HealthKit permissions are granted per type, separately for read and write. The app cannot tell whether read access was denied, and people can now grant only a limited window of history.** Background delivery needs an entitlement, and some types update at most hourly. `opened`
- https://developer.apple.com/documentation/healthkit/authorizing-access-to-health-data · 2026-10-01 · "your app doesn't know whether someone granted or denied permission to read data" / "people can choose to grant your app access to only a limited window of recent data"
- https://developer.apple.com/documentation/healthkit/hkhealthstore/enablebackgrounddelivery(for:frequency:withcompletion:) · 2026-10-01 · "you must enable the HealthKit Background Delivery by adding the [com.apple.developer.healthkit.background-delivery] entitlement"

**P31 · The Firebase iOS SDK's latest release is 12.19.2 (15 Sep 2026), distributed through Swift Package Manager.** It needs Xcode 26.2+ and iOS 15+. Firebase 13.0.0 is staged on `main` (`CocoaPods-13.0.0` tag of 24 Sep 2026, no SPM `13.0.0` tag yet) and drops CocoaPods. `opened`
- https://github.com/firebase/firebase-ios-sdk (tag `12.19.2`, `Package.swift`) · 2026-10-01 · `let firebaseVersion = "12.19.2"`, `platforms: [.iOS(.v15), …]`; release note "This is a Swift Package Manager only release."
- https://github.com/firebase/firebase-ios-sdk/blob/main/FirebaseCore/CHANGELOG.md · 2026-10-01 · "# Firebase 13.0.0 … Firebase is no longer distributed via CocoaPods … exclusively via Swift Package Manager" / "Added support for Swift Package Traits … to opt out of unused features" / "Firebase now requires at least Xcode 26.2"

## C · GitHub Actions (macOS runners, Xcode, simulators, XCUITest)

**P32 · The macOS runner labels are `macos-latest` = `macos-26` (arm64), `macos-15` (arm64) and a preview `xcode-27` label. `macos-14` is deprecated and fully unsupported by 2 November.** `opened`
- https://github.com/actions/runner-images (README, commit 14d8569 of 2026-09-30) · 2026-10-01 · "macOS 26 Arm64 | arm64 | `macos-latest`, `macos-26` or `macos-26-xlarge`"; "Xcode 27 [preview] | arm64 | `xcode-27` or `xcode-27-xlarge`"
- https://github.com/actions/runner-images/issues/14404 · 2026-10-01 (opened 16 Jul 2026) · "The image is marked as 'preview' for now. It means some software can be unstable"

**P33 · Each image carries a different set of Xcode versions.** `macos-26` has Xcode 26.0.1–26.6 with **26.6 as default**, and no Xcode 27. `macos-15` has Xcode 16.0–16.4 (default 16.4) plus 26.0.1–26.3. `xcode-27` runs macOS 27.0 with **27.0 as default**, plus 27.1 and 27.2 beta. `opened`
- https://github.com/actions/runner-images/blob/main/images/macos/xcode-27-arm64-Readme.md · 2026-10-01 · "Image Version: 20260921.0210.1" / "27.0 (default) | 27A266a | /Applications/Xcode_27.app"
- https://github.com/actions/runner-images/blob/main/images/macos/macos-26-arm64-Readme.md · 2026-10-01 · "26.6 (default) | 17F113 | /Applications/Xcode_26.6.app"

**P34 · The iPhone simulators are iPhone 17, 17e, Air, 18 Pro and 18 Pro Max on `xcode-27` (iOS 27.0), and iPhone 16e/17e, 17, Air, 17 Pro and 17 Pro Max on `macos-26` (iOS 26.2 / 26.4 / 26.5).** The smallest iPhone is the 17e (or 16e) and the largest is the 18 Pro Max (or 17 Pro Max). The simulator names are `opened`. The screen-size ranking (17e about 6.1", Pro Max about 6.9") is an `assumption`, because Apple's spec pages were blocked.
- https://github.com/actions/runner-images/blob/main/images/macos/xcode-27-arm64-Readme.md · 2026-10-01 · "iOS 27.0 | 27.0 | iPhone 17 · iPhone 17e · iPhone 18 Pro · iPhone 18 Pro Max · iPhone Air"

**P35 · Runner policy: one major Xcode version per macOS image, with betas only "as-is" on the newest image. Default Xcode versions change on announced dates.** `opened`
- https://github.com/actions/runner-images (README) · 2026-10-01 · "only one major version of Xcode will be supported per macOS version … beta and RC versions will be provided "as-is" in the latest available macOS image only"

**P36 · XCUITest screenshots reach CI artifacts in three steps.** First, attach each screenshot with `XCTAttachment(screenshot:)` and set `lifetime = .keepAlways`, because the default deletes it when the test passes. Next, extract the screenshots from the `.xcresult` bundle with `xcresulttool export attachments` (Xcode 16+). Then upload them with `actions/upload-artifact@v7`. The latest tags are `v7.0.1` for both upload-artifact and checkout. `opened`
- https://developer.apple.com/documentation/xctest/xctattachment/lifetime-swift.property · 2026-10-01 · "Defaults to [deleteOnSuccess] … Set this property to [keepAlways] to persist an attachment even when its test passes."
- https://developer.apple.com/documentation/xcode-release-notes/xcode-16_3-release-notes · 2026-10-01 · "In Xcode 16, xcresulttool introduced new commands, including: … `export attachments`"
- https://github.com/actions/upload-artifact (README; git tags) · 2026-10-01 · "- uses: actions/upload-artifact@v7"; latest tag `v7.0.1`

## D · Python backend and Firebase emulators

**P37 · FastAPI is at 0.142.2 (30 Sep 2026) and needs Python ≥ 3.10.** It depends on `pydantic>=2.9.0`. `opened`
- https://pypi.org/pypi/fastapi/json · 2026-10-01 · `"version": "0.142.2"`, `"requires_python": ">=3.10"`

**P38 · The latest stable Pydantic is 2.13.5 (28 Aug 2026). 2.14.0b2 is a pre-release.** `opened`
- https://pypi.org/pypi/pydantic/json · 2026-10-01 · `"version": "2.13.5"`; releases include `2.14.0b2`

**P39 · OR-Tools (`ortools`) is at 9.15.6755, released 14 Jan 2026 with no newer release since.** It ships wheels for CPython 3.9–3.14, including manylinux x86_64 and aarch64. `opened`
- https://pypi.org/pypi/ortools/json · 2026-10-01 · `"version": "9.15.6755"`; wheel tags `cp39 … cp314`; git tag `v9.15` on github.com/google/or-tools

**P40 · `google-cloud-firestore` is at 2.33.0 (29 Sep 2026) and needs Python ≥ 3.10.** `opened`
- https://pypi.org/pypi/google-cloud-firestore/json · 2026-10-01 · `"version": "2.33.0"`, `"requires_python": ">=3.10"`

**P41 · `firebase-admin` is at 7.7.0 (23 Sep 2026). Its App Check verification supports limited-use tokens through `consume=True`.** `opened`
- https://pypi.org/pypi/firebase-admin/json · 2026-10-01 · `"version": "7.7.0"`
- https://github.com/firebase/firebase-admin-python/blob/v7.7.0/firebase_admin/app_check.py · 2026-10-01 · `def verify_token(token: str, app=None, consume: bool = False)` / "Set to ``True`` only if the token is a limited-use (one-time) token that should be consumed upon verification"

**P42 · The Firebase Local Emulator Suite needs firebase-tools 15.32.1 (npm, 30 Sep 2026) on Node ≥ 20, and Java 21 or later.** This container has OpenJDK 21.0.10 and Node 22.22.2. `opened`
- https://registry.npmjs.org/firebase-tools · 2026-10-01 · `latest: 15.32.1`, `engines: {"node": ">=20.0.0 || >=22.0.0 || >=24.0.0"}`
- https://github.com/firebase/firebase-tools/blob/main/src/emulator/commandUtils.ts · 2026-10-01 · `MIN_SUPPORTED_JAVA_MAJOR_VERSION = 21` / "firebase-tools no longer supports Java version before 21."

---

## Implications for our architecture

1. **Model registry.** Freeze `gemini-3.8-flash` for photo, label and recipe interpretation. Treat `gemini-3.5-flash-lite` as the candidate for text intent parsing. Never use a `*-latest` alias. Google ships a new Flash about every month, so shadow and canary runs (§16.4) are routine work, not a rare event. Store the model ID, `thinking_level` and SDK version with every analysis. (P1–P5)
2. **Determinism without temperature.** 3.8 Flash and 3.5 Flash-Lite drop `temperature`, `top_p` and `top_k`, so repeatability has to come from three things: `response_json_schema` built from our Pydantic models, server validation (FRD §16.3), and reusing confirmed units. Re-running a model must never change a ledger entry. (P5, P8)
3. **Use `generateContent` on Agent Platform, not Interactions.** It is stateless, and Interactions on Agent Platform cannot call a base model directly. If the Gemini API with an API key is used in development and calls Interactions, set `store=False`, because the default keeps data 55 days. The retention review in FRD §20 must cover the 24-hour cache and the abuse-monitoring logs before we promise anything. (P12–P14)
4. **One SDK, two backends.** Use `google-genai` ≥ 2.26 behind an adapter. Development runs on a mock or an API key; staging and production use `enterprise=True` with an **explicit regional `location`**, never `global`, wherever residency is promised (FRD §15.3). Regional availability of 3.8 Flash in the chosen region is still unverified. Do not depend on `google-cloud-aiplatform`. (P9–P11)
5. **iOS target.** Set the minimum deployment target to **iOS 26.0** and build with **Xcode 27 / the iOS 27 SDK**. Foundation Models, SpeechAnalyzer, `UndoableIntent` and `SnippetIntent` all need iOS 26; 86 % of recent iPhones already run it; and the App Store already requires the iOS 26 SDK. Put iOS 27-only APIs (`ResultsObserver`, `LongRunningIntent`, PCC) behind `#available`. (P16–P20, P23, P25)
6. **Local ledger and outbox.** Use `@Observable` view models with a SQLite store through **GRDB 7.11**. GRDB gives explicit transactions, unique idempotency keys and versioned migrations, which an append-only ledger and an outbox need. SwiftData's useful observers are iOS 27-only, and its history API cannot express our correction semantics. If the team prefers Apple-only code, SwiftData is the fallback. (P20, P21)
7. **Voice logging through Siri.** A Siri phrase carries one parameter and must contain the app name, so model the **Unit as an `AppEntity`** ("Log cheese bite in Sips & Bytes") and ask for the count as a follow-up, or open the in-app voice capture for free speech. Back the one-tap Undo with `UndoableIntent`, and put the top units on interactive widget buttons, which work only when the device is unlocked. No Apple schema domain fits nutrition, so we write custom intents. (P22–P24)
8. **No on-device path for Arabic.** SpeechTranscriber's Arabic support was a listing error, and Apple Intelligence (and therefore Foundation Models) appears not to cover Arabic. Arabic and code-switched voice must therefore go to the server: `gemini-3.5-transcribe` or Gemini 3.8 Flash audio, under our consent and retention rules. This also follows Apple's advice not to send health speech to its recognition servers. On-device Foundation Models and SpeechAnalyzer are only an opportunistic offline path for English, gated by `supportsLocale` and `isAvailable`. A missing model must never block tap-to-log. (P15, P25–P28)
9. **HealthKit.** Read active energy, workouts and body mass. Write each confirmed entry as a `food` correlation of dietary energy, protein, carbohydrate and fat samples, and keep the HealthKit sample UUIDs on the entry so a correction or void can delete and rewrite them. The app cannot tell a denied read from empty data, so the UI shows freshness and "no data", never "denied". Background delivery needs its entitlement and may arrive only hourly. (P29, P30)
10. **Firebase on iOS.** Add Firebase **through SPM only**, pinned to 12.19.x (Xcode 26.2+). Plan the 13.0 upgrade: CocoaPods is gone, and package traits let us leave out unused products. Verify App Check on the server with `firebase-admin` 7.7 `verify_token`, using limited-use tokens (`consume=True`) on the AI endpoints to block replays. (P31, P41)
11. **CI on GitHub Actions.** Run the iOS job on `xcode-27`, which is in preview, to get the iOS 27 SDK and simulators. Keep `macos-26` with Xcode 26.6 as the fallback lane. Select Xcode by path and never rely on the default. Run the smallest/largest walks on **iPhone 17e** and **iPhone 18 Pro Max**. Collect screenshots with `XCTAttachment(... lifetime: .keepAlways)`, then `xcrun xcresulttool export attachments`, then `actions/upload-artifact@v7`. (P32–P36)
12. **Backend pins.** Use Python 3.12 or 3.13. Pin FastAPI 0.142.x, Pydantic 2.13.x (not the 2.14 beta), `ortools` 9.15, `google-cloud-firestore` 2.33, `firebase-admin` 7.7 and `google-genai` 2.26. The emulator suite runs here and in the Linux CI job with Java 21, Node 20+ and firebase-tools 15.32. (P37–P42)
