# Option · the iOS local store for the cached ledger, Units, Days and the durable outbox

**Recommendation: (A) GRDB.swift on SQLite, pinned exactly to 7.11.1 (move to 7.12.0 once it is tagged), inside a platform-neutral SwiftPM package `SipsKit` that holds the domain types, the Pending/Confirmed arithmetic, the API client, the store and the sync engine, all tested with `swift test` on Linux and again on the macOS runner; SwiftUI, Firebase, HealthKit and the widget stay in the Xcode app.**

Written 2026-10-01 for the /way build. It argues one architecture choice from `blueprint.md` §0, `r1-platforms.md` (P16–P21, implication 6), `r1-refute-b.md` (P20, P21 stand), FRD §8, §8.3, FR-040–047, NFR-02, NFR-06, AT-10, AT-31, and the offline stories in `personas/eater/wf3-wf6.md` (3.23, 3.25–3.27, 6.21–6.23) and `wf1-wf9.md` (1.52). I opened every source on **2026-10-01**: the GitHub repositories through `git` (clone and `ls-remote`), and Apple's pages through their JSON form (`developer.apple.com/tutorials/data/documentation/<path>.json`) or the WWDC session pages. I ran every Linux claim marked **ran** in this container (Swift 6.4 on Ubuntu 24.04, system SQLite 3.45.1). I sent nothing about the owner to any service.

---

## 1 · What the store has to do

The ledger itself lives on the server: FR-040–042 put the immutable events, the effective-entry projection and the one-transaction day revision in the API, on Firestore. The phone keeps a **cache** of what the server confirmed, plus an **outbox** of commands it has not confirmed yet. The phone never rebuilds totals from events. It shows the server's Day projection with the outbox laid over it.

| # | The store must | From |
|---|---|---|
| S1 | keep each command durably, with a UUID `command_id` and an expected revision, through an app kill | FRD §8.3; eater-3.25 `/s`; eater-6.21 `/s` |
| S2 | hold one row per `command_id`, however many times a surface sends it (a tap, a Template, Siri, the widget) | FR-043, AT-10; eater-3.23 |
| S3 | give Pending and Confirmed totals separately. A correction counts as a delta (15→18 = +60 Pending), and a refused command counts as nothing | FRD §8.3; eater-3.25, 6.21, 6.22 |
| S4 | show the Entry within 300 ms p95, so the first write is local | NFR-02; eater-3.27 |
| S5 | keep 30 days of Units, Templates and Days readable offline, and never prune a queued command | NFR-06; eater-3.25 `/r` |
| S6 | never cause a duplicate after a reinstall, a restore or a session recovery | NFR-06; eater-1.52; research EX-61 |
| S7 | handle `409 STALE_REVISION`: the command leaves Pending and is not counted, and the eater's choice is kept | AT-31; eater-6.22, 6.23; vocabulary D2 |
| S8 | run one command path from every surface, while the widget's intent runs in **another process** | eater-3.22, 3.23; §5.2 below |
| S9 | carry a versioned schema with forward migrations, rehearsed on a copy of seeded data | blueprint §3 delta D1 |
| S10 | be testable here, where Swift 6.4 compiles on Linux but SwiftUI and SwiftData do not | blueprint §0 "Environment facts" |

S1, S2, S3 and S7 need three things from the store: **explicit transactions**, **a uniqueness rule that refuses or ignores rather than overwrites**, and **an aggregate query**. Those three properties decide this choice. S10 decides how cheaply we can prove them.

---

## 2 · The three options, side by side

| | (A) GRDB.swift 7.11.1 on SQLite | (B) SwiftData | (C) Core Data |
|---|---|---|---|
| Explicit transactions | `writer.write { db in … }` is one SQLite transaction; savepoints are available; nothing saves on its own | `transaction(block:)` writes at the end of the closure, but `mainContext` **autosaves by default**, including "during the lifecycle of windows, scenes, views, and sheets" | contexts save explicitly; `perform` / `performAndWait` serialise work |
| Uniqueness for `command_id` | a SQL `PRIMARY KEY`/`UNIQUE` column; `INSERT … ON CONFLICT IGNORE` returns "0 rows changed", and a plain insert throws | `#Unique` (iOS 18) **upserts on collision**: a re-enqueue silently overwrites the row's state | `uniquenessConstraints` (iOS 9); the default `NSErrorMergePolicy` makes the save **fail** with an `NSConstraintConflict` |
| Pending and Confirmed sums | one SQL `SUM` over the cache plus the outbox | no aggregate API (`fetch`, `fetchCount`, `fetchIdentifiers`, `enumerate` only), so the sum is fetched and added in memory | `NSExpressionDescription` with a dictionary-result fetch ("expressions can aggregate data") |
| Migrations | `DatabaseMigrator`: named, ordered steps where "only non-applied migrations are run"; testable on a seeded file **on Linux** (ran) | `VersionedSchema` + `SchemaMigrationPlan` (iOS 17); Apple platforms only | model versions, lightweight migration and `NSStagedMigrationManager` (iOS 17); Apple platforms only |
| Observation for SwiftUI | `ValueObservation` → `AsyncValueObservation` → an `@Observable` model; runs on Linux (ran) | `@Query` inside views (iOS 17); `ResultsObserver` and `HistoryObserver` outside views need **iOS 27** | `@FetchRequest` (iOS 13), `NSFetchedResultsController`; `NSManagedObject` is `ObservableObject`, not `@Observable` |
| Changes from another process | not seen ("does not detect changes performed by external processes") | not seen by observation ("not those made to your data store from another process, such as widget or extension") | remote-change notifications and persistent history (iOS 11/13) |
| Swift 6 concurrency | records are plain `Sendable` structs; the spike built in Swift 6 mode with no warnings in our code (ran) | `ModelContext` and models are not `Sendable`; pass `PersistentIdentifier` | `NSManagedObject` is not `Sendable`; pass `NSManagedObjectID` |
| `swift test` on Linux | **yes**: our spike passed 6/6, and GRDB's own suite passed 2,781 tests with 0 failures (ran); Linux support is unofficial (see A.4) | **no**: `no such module 'SwiftData'` (ran) | **no**: `no such module 'CoreData'` (ran) |
| At the iOS 26 minimum target | everything works (GRDB needs iOS 13+; UPSERT and `RETURNING` need iOS 15+) | the iOS 18 feature set plus 4 symbols new in 26; 79 symbols are iOS 27-only | everything works; 13 new symbols in iOS 27 are optional |
| Maintenance | one main maintainer, MIT licence, zero transitive dependencies, a release every 2–4 months | Apple; active, but new capability ships only with the newest OS | Apple; maintained, low feature velocity; Apple's iOS 27 sample shows how to move a Core Data app to SwiftData |
| **Fit to this job** | **strong** | **weak** | **adequate** |

---

## 3 · The evidence, option by option

### (A) GRDB.swift

**A.1 · Version and cadence.** `git ls-remote --tags` gives **v7.11.1** as the latest tag, with commit date 2026-06-18. This confirms P21, which still stands. The `releases/v7.12.0` branch carries release notes that read "## 7.12.0 · Released September 29, 2026", but on 2026-10-01 no `v7.12.0` tag exists. One of its fixes matters to us: "Work around an SQLite bug in WAL snapshot ordering (#1884)", because a `DatabasePool` uses WAL. Release dates from the tags: 7.0.0 2025-01-26 · 7.8.0 2025-10-02 · 7.9.0 2025-12-13 · 7.10.0 2026-02-15 · 7.11.0 2026-06-01 · 7.11.1 2026-06-18. The first commit is dated 2015-06-30, and the repository has 332 tags. Since 2025-10-01 there have been 108 non-merge commits on all branches: 71 by Gwendal Roué, 18 by Tim De Jong, and the rest from 4+ others. A `dev/xcode-27` branch was updated 2026-09-29, and 7.11.1 already contains "Fix a warning in Version 27.0 beta" (2026-06-10). (github.com/groue/GRDB.swift: tags, CHANGELOG.md on `releases/v7.12.0`, git log)

**A.2 · Requirements.** The README at tag v7.11.1 says: "**Requirements**: iOS 13.0+ / macOS 10.15+ / tvOS 13.0+ / watchOS 7.0+ • SQLite 3.20.0+ • Swift 6.1+ / Xcode 16.3+". It also says: "Upsert apis are available from SQLite 3.35.0+: iOS 15.0+ …" and "Support for the `RETURNING` clause is available from SQLite 3.35.0+: iOS 15.0+". Both are satisfied at our iOS 26 floor and by Linux's SQLite 3.45.1.

**A.3 · Dependencies and supply chain.** `Package.swift` at v7.11.1 declares no package dependencies. The DocC plugin is added only when `SPI_BUILDER=1` is set. On Linux it links the system `libsqlite3` through `.systemLibrary(name: "GRDBSQLite", providers: [.apt(["libsqlite3-dev"])])`. The spike's `Package.resolved` has exactly one pin, `grdb.swift 7.11.1` (ran). One MIT package with no transitive dependencies is the smallest possible footprint for D1's dependency audit and SBOM.

**A.4 · Linux: unofficial, but it works today.** The README at v7.11.1 says: "Linux support is provided by contributors. It is not automatically tested, and not officially maintained." Recent releases still carry Linux work: 7.10.0 has "Linux adjustments (#1825)" and "Android and Windows adjustments (#1849, #1850)", and 7.9.0 has a change that "aims at easing Linux and Android compatibility". What I ran:
- **The library builds on Linux** with no change, in 18 s (debug).
- **GRDB's own test target did not compile at first.** `Tests/GRDBTests/Core/TransactionObserver/DatabaseEventObservationStrategyTest.swift` has an unguarded `import SQLite3`, and the build reported `no such module 'SQLite3'`. After a one-line `#if canImport(SQLite3) … #else import GRDBSQLite` in a scratch copy, the **full suite ran: 2,781 tests, 46 skipped, 0 failures, in 122.8 s**. Every migrator, transaction, savepoint, `DatabasePool`, record-persistence and `ValueObservation` suite passed.
- This is the "not automatically tested" warning in practice: the library is sound on Linux, and only a test file had rotted.

**A.5 · Fit to the outbox.** A write closure is one transaction. A `PRIMARY KEY` on `commandID` with `insert(onConflict: .ignore)` turns a second enqueue into "0 rows changed", which S2 needs. Pending and Confirmed are one `SUM` each. `DatabaseMigrator` gives S9 (Migrations.md: "When a user upgrades your application, only non-applied migrations are run"). Undo of a command that is still queued is a single serialised transaction: check that the row is `queued`, then delete it. This closes the race with the sender picking the row up. If the row is already in flight or Confirmed, Undo enqueues a Void instead.

**A.6 · Observation.** `ValueObservation.tracking { … }.values(in: writer)` gives an `AsyncSequence` that emits after each commit that touches the observed tables. A `@MainActor @Observable` model holds the latest value for SwiftUI. Two optional SwiftUI conveniences exist: GRDBQuery (tag 0.11.0, 2025-03-15) and Point-Free's SQLiteData (tag 1.12.0, 2026-08-31; "a fast, lightweight replacement for SwiftData … built on top of the popular GRDB library"). SQLiteData adds 11 more packages and CloudKit sync we do not need, so I do not recommend either. An `@Observable` model is enough.

**A.7 · Sharing between processes.** GRDB's guide (`DatabaseSharing.md` at v7.11.1) says: "Preventing errors that may happen due to database sharing is difficult. It is extremely difficult on iOS. And it is almost impossible to test. Always consider sharing plain files, or any other inter-process communication technique, before sharing an SQLite database." It also says GRDB's observation "does not detect changes performed by external processes". §5.2 designs around both points.

### (B) SwiftData

**B.1 · Uniqueness upserts.** `Unique(_:)` is iOS 18.0 and `Attribute.Option.unique` is iOS 17.0. Apple's WWDC24 session "What's new in SwiftData" (10137) says: "When two model instances share the same unique values, SwiftData will perform an upsert on collision with an existing model!" WWDC23 "Model your schema with SwiftData" (10195) says: "If the insert collides with existing data, it becomes an update and updates the properties of the existing data." That is the opposite of what an outbox needs. A second enqueue of the same `command_id` would reset `state` and `attempts` on a row already in flight. Every insert would need a fetch first, in the same context, to stay safe.

**B.2 · Autosave and transactions.** `transaction(block:)` (iOS 17.0): "Runs the provided closure, and once it finishes, writes any pending inserts, changes, and deletes to the persistent storage." `autosaveEnabled` (iOS 17.0): "When `true`, the context calls save() after you make changes … The context also calls `save()` at various times during the lifecycle of windows, scenes, views, and sheets. The default value is `false`. SwiftData automatically sets this property to `true` for the model container's mainContext." A half-built pair (outbox row plus optimistic Entry) on the main context can therefore be saved at a moment we did not choose. We would have to keep all writes off `mainContext`.

**B.3 · No aggregates.** The `ModelContext` topics list `fetch`, `fetch(_:batchSize:)`, `fetchCount`, `fetchIdentifiers` and `enumerate`, with no sum or group-by. Pending and Confirmed kcal would be fetched and added in memory. That works for 30 days of data, but it moves the S3 arithmetic out of the store's transaction.

**B.4 · Observation at an iOS 26 floor.** `ResultsObserver` ("Observes and tracks changes to a collection of persistent models") and `HistoryObserver` are **iOS 27.0**, which confirms P20, which still stands. Apple's "SwiftData updates" page lists them under **June 2026**, with sectioned queries and the `codable` attribute option. June 2025 brought model inheritance and `sortBy` in `HistoryDescriptor`. June 2024 brought `#Index`, `#Unique`, history and custom `DataStore`. I crawled 411 SwiftData doc pages and counted symbols by the iOS version that introduced them: 168 in iOS 17, 146 in iOS 18, **4 in iOS 26**, **79 in iOS 27**. At our iOS 26 floor, the sync engine and the Today headline outside a view would need manual re-fetches on `didSave`, plus an `#available(iOS 27, *)` path: two observation designs to build and test. WWDC25 "SwiftData: Dive into inheritance and schema migration" (291) adds: "not all changes are Observable, only those made to your models in process, not those made to your data store from another process, such as widget or extension, or even another model container within your app."

**B.5 · Linux.** `import SwiftData` fails with `no such module 'SwiftData'` on Swift 6.4 for Linux (ran). Every store test would need a macOS runner, and the Linux job could test only code behind a protocol, against a fake. The code that enforces S1, S2 and S7 would never run on Linux.

**B.6 · Concurrency.** Apple's conformance list for `ModelContext` shows `SendableMetatype` but not `Sendable`. `PersistentIdentifier` and `ModelContainer` are `Sendable`. A sync engine on its own actor needs a `@ModelActor` with its own context, and passes identifiers across.

### (C) Core Data

**C.1 · Uniqueness errors by default.** `NSEntityDescription.uniquenessConstraints` (iOS 9.0) notes: "Uniqueness constraint violations can be computationally expensive to handle. The recommendation is to use only one uniqueness constraint per entity hierarchy." `mergePolicy` says: "The default is NSErrorMergePolicy". `NSErrorMergePolicy` is "The default merge policy for all managed object contexts", and a save that conflicts fails with an `NSConstraintConflict` (iOS 9.0), described as: "A constraint conflict occurs when your data model is using unique constraints and one or more managed objects are violating that constraint." This is the right behaviour for an outbox: a duplicate is refused, not merged.

**C.2 · Aggregates, migrations and cross-process changes.** `NSExpressionDescription` (iOS 3.0) says: "expressions can aggregate data". `NSStagedMigrationManager` (iOS 17.0) "contains the individual stages of a migration and applies those stages, in the order you specify". `NSPersistentStoreRemoteChangeNotificationPostOptionKey` (iOS 13.0) makes the store post "a remote change notification for every write to the store, including writes by other processes". `NSPersistentHistoryTrackingKey` (iOS 11.0) notes: "Persistent history tracking is off by default." Core Data is the only option of the three that watches a widget's writes for us.

**C.3 · Maintenance.** I crawled 876 Core Data doc pages: 23 symbols are new in iOS 17, 3 in iOS 18, 3 in iOS 26 and 13 in iOS 27. The iOS 27 ones are typed notifications such as `NSManagedObjectContext.DidSaveMessage`. Apple's sample "Adopting SwiftData for a Core Data app" (iOS 27.0, Xcode 27.0) ships "a Core Data version … a SwiftData version … and a coexistence version". Apple keeps Core Data working and points new work at SwiftData.

**C.4 · Ergonomics and Linux.** `NSManagedObject` conforms to `ObservableObject`, not `Sendable`, while `NSManagedObjectID` and `NSManagedObjectContext` are `Sendable`. In Swift 6 that means object IDs and `perform` blocks everywhere. The model lives in an `.xcdatamodeld`. `import CoreData` fails on Linux with `no such module 'CoreData'` (ran), so C has B's testing cost.

### Out of scope: the Firestore iOS SDK's offline cache

The FRD mentions the Firestore SDK's offline cache (§8.3, [S07]). It is not an option here. The app talks to the FastAPI API (§15.1, "iOS app → authenticated API"), not to Firestore. The FRD itself adds that "application-specific correction semantics and deduplication must still be implemented".

---

## 4 · What I ran here (the Linux spike)

The spike is a throwaway package in this session's scratchpad. It is not in the repo, and it is enough to reproduce the result.

- `Package.swift` declares `swift-tools-version:6.2`, `platforms: [.iOS(.v26), .macOS(.v26)]`, and `.package(url: "https://github.com/groue/GRDB.swift.git", exact: "7.11.1")`. It has a `SipsCore` target (commands, `DayTotals`, a URLSession transport) and a `SipsStore` target (GRDB).
  - **Finding:** `.iOS(.v26)` needs tools version **6.2**. With 6.1, SwiftPM stops: "'v26' is unavailable … introduced in PackageDescription 6.2".
- **Schema:**
  - `outbox` (`commandID TEXT PRIMARY KEY`, `seq INTEGER UNIQUE`, kind, entryID, diaryDayID, expectedEntryRevision, kcalDelta, state `queued|inFlight|confirmed|rejected`, attempts)
  - `confirmedEntry` (entryID PK, diaryDayID, revision, kcal, voided)
  - Pending = `SUM(kcalDelta)` over queued and in-flight rows; Confirmed = `SUM(kcal)` over non-voided cache rows.
- **Result:** `swift test` passed 6/6 in Swift 6 language mode, with no warnings in our sources:
  1. `duplicateEnqueueIsIgnored`: the same command enqueued 3× gives one row and Pending 138 (AT-10, client side).
  2. `pendingThenConfirmedOnce`: a duplicate server acknowledgement is applied once (eater-3.26).
  3. `outboxSurvivesReopen`: the file is reopened after a simulated kill, and the queued command is still there (eater-3.25 `/s`).
  4. `observationEmitsPendingChanges`: `AsyncValueObservation` emits the new Pending total after an enqueue.
  5. `correctionIsPendingDeltaThenStaleRejected`: 300 Confirmed plus a +60 correction shows Pending 60; after a 409 the command is rejected, Pending is 0, and nothing is resent (eater-6.21, 6.22).
  6. `migratesExistingFileForward`: a file written by a v1-only migrator, holding a queued command, is opened by v1+v2. The new column exists and Pending is still 42 (D1 rehearsal on seeded data).
- The `HTTPLedgerTransport` (URLSession with an `Idempotency-Key` header) compiles on Linux through `FoundationNetworking`.
- Also checked: `Observation` and `FoundationNetworking` typecheck on Linux; `SwiftUI`, `SwiftData` and `CoreData` give "no such module".

---

## 5 · What no store choice fixes, and how the design handles it

### 5.1 Reinstall, restore and session recovery (S6)

- **Where the file lives.** The database goes in `Application Support`, **not** `Caches` or `tmp`. Apple says of those two: "The system periodically purges these directories, so iCloud Backup excludes them by default … Don't use these directories to exclude nonpurgeable data from iCloud Backup." A purge would silently lose queued commands. Do not set `isExcludedFromBackup` on the database either: Apple says it "is only useful for excluding cache and other application support files which are not needed in a backup", and the outbox is user-created data.
- **File protection.** Use `completeUntilFirstUserAuthentication`: "After the user unlocks the device for the first time, your app can access the file and continue to access it even if the user subsequently locks the device." Background sync can then drain the outbox while the phone is locked.
- **Restoring an old backup** brings back an outbox that may hold commands the server already accepted. They are resent with the **same** `command_id`. The server must keep `(user, command_id) → original result` for at least as long as a backup can be restored, and answer with the original `entry_id` (eater-3.23 `/r`: "all three carry the same entry_id"). This is a server obligation, written into the API contract. The client never creates a new id for an old command.
- **A clean reinstall** has an empty outbox, so nothing replays. The client rebuilds the 30-day cache from the server after sign-in. Any offline commands on the deleted install are lost. No local store can prevent that, so Settings → sign out warns while Pending > 0 (one `COUNT` query).
- **Outbox rows carry the owner's uid.** After an account switch, the old owner's rows are held back, never sent under the new token (NFR-07; eater-1.51–1.53 move flows).
- **Test on Linux:** copy the database file after an enqueue, confirm the command against the in-memory server stub, restore the copy and drain again. The stub must answer "duplicate", and the Day must hold one Entry.

### 5.2 The widget runs in another process (S8)

Apple's widget docs say: "By default, the system runs the app intent in the same process as the widget extension. However, if the app intent's openAppWhenRun property is `true`, or if the intent conforms to AudioPlaybackIntent, ForegroundContinuableIntent, LiveActivityIntent, or PushToTalkTransmissionIntent, the system performs the app intent in the app's process." Apple's crash guide describes `0xdead10cc`: "The operating system terminated the app because it held on to a file lock or SQLite database lock during suspension." All three options are SQLite underneath, so all three share this risk if the widget opens the database.

- **The design:** the database stays in the app's own container, and **the widget never opens it**. GRDB's own advice is "consider sharing plain files". A widget or out-of-process intent writes one small JSON file per command, named by its `command_id`, into an App Group `inbox/` directory with an atomic write. It may also try a direct POST through `SipsAPI` with the same `command_id`.
- **When the app runs,** it imports the inbox with `INSERT … ON CONFLICT IGNORE` and deletes each file only after its row is committed. Whichever path reaches the server first wins, and the other gets "duplicate".
- **The widget's display** (recent Units, the optional kcal remaining) comes from a JSON snapshot that the app writes to the App Group.
- This also removes Core Data's one real advantage here (C.2): with no second writer to the database, nothing needs cross-process notifications.
- App writes that might straddle suspension run inside a background-task assertion, per the same Apple page.

### 5.3 Pending numbers must not drift

- **Pending is never stored.** It is always a query over the outbox, so it cannot disagree with the queue.
- **The same rule exists twice.** `SipsDomain` has a pure fold (consume adds its kcal; correct adds new minus old; void subtracts the Confirmed value; rejected and confirmed rows add 0). A property test checks that the SQL sum equals the fold for random outboxes.
- **No floating-point sums.** Kcal values use the API contract's exact representation: integer tenths, or `Decimal` decoded from strings, never `Double` (NFR-01).

---

## 6 · How to split the code

```
ios/                                   (proposal; the blueprint §2 names the final paths)
  SipsKit/                             SwiftPM package · tools 6.2 · platforms iOS 26, macOS 26 · builds on Linux
    Sources/
      SipsDomain/        no dependencies. IDs; LedgerCommand (consume · correct · void · restore · move,
                         expected entry revision on all but consume, eater Conflicts item 3); the Pending fold;
                         DayTotals; the offline diary-day proposal from the cached boundary and time zone
                         (eater-3.28–3.33; the server's Day assigner stays authoritative); the error codes of D2
      SipsAPI/           → SipsDomain. Codable DTOs from the OpenAPI contract; LedgerTransport protocol and a
                         URLSession implementation (FoundationNetworking on Linux); TokenProvider protocol
                         (Firebase Auth + App Check tokens are supplied by the app); bounded retry policy with
                         the same command_id (FRD §18.2) as a pure function
      SipsStore/         → SipsDomain, GRDB 7.11.1 (exact). Migrations; outbox, confirmed-entry, Day, Unit-version,
                         Template and sync-cursor tables; enqueue, confirm, reject, cancel-if-queued; Pending and
                         Confirmed queries; 30-day pruning that never touches a queued row; AsyncValueObservation
                         streams; the owner uid on every row
      SipsSync/          → SipsStore, SipsAPI. `actor SyncEngine`: drains in `seq` order, one command in flight per
                         Entry, maps 201/200/409/429/503, rebuilds the cache after sign-in or reinstall, imports
                         the App Group inbox; clock and reachability are injected
      SipsFeatures/      (optional) → SipsSync. @MainActor @Observable screen models (Today, Entry details,
                         conflict choice). Observation exists on Linux, so screen state is tested there too
      SipsTestSupport/   a scripted fake transport; an in-memory server stub that applies the same idempotency
                         and revision rules as FastAPI; the lens files' fixture eaters (Mona, Sam); golden JSON
                         fixtures shared with the Python server's tests
    Tests/               one suite per target, run by `swift test`
  App/                   Xcode project · macOS runner only
    SwiftUI views · Firebase Auth/App Check adapter (TokenProvider) · HealthKit writer, driven by
    Confirmed transitions from SipsStore (eater-3.39, 6.24) · BGTaskScheduler · App Intents and Siri ·
    widget extension (inbox writer + snapshot reader, never the SQLite file)
```

**The rules that keep the package platform-neutral:**
- No `import SwiftUI`, `UIKit`, `HealthKit`, `WidgetKit` or Firebase anywhere in `SipsKit`.
- `#if canImport(FoundationNetworking)` around networking imports.
- `libsqlite3-dev` is installed in the Linux CI image; it is already present here.

**Two test lanes:**
1. **Linux, every push:** `swift test` on `SipsKit`. It covers AT-10 on the client, 3.25–3.27, 6.21–6.23, the 1.52 interrupted move, the restored-backup replay of §5.1, every migration on a seeded copy of every earlier schema, and the Pending fold property test.
2. **The macOS runner** (`xcode-27`, fallback `macos-26`, P32–P33): the same `swift test` again, against Darwin's SQLite, plus the XCUITest `/r` walks on the iPhone 17e and the 18 Pro Max.

Running the same tests on Darwin covers the gap GRDB's README admits, that Linux is "not automatically tested".

---

## 7 · Risks, and what would change the decision

| Risk | What reduces it |
|---|---|
| **One main maintainer**: 71 of 108 commits in the last year | MIT licence, zero dependencies, pinned `exact:`. We use a small surface (migrator, write/read, records, `ValueObservation`), so a fork is cheap, and SQLite is the real engine |
| **Linux support is unofficial** (README) | The identical suite runs on the macOS runner. A Linux break blocks only the Linux lane. Today the library builds clean and only one test file needed a guard (A.4); a fix PR upstream would be welcome |
| **Hand-written SQL schema** is more code than `@Model` | Typed records plus migration tests on Linux; the API contract's golden fixtures run on both client and server |
| **Xcode 27 / Swift 6.4 on iOS** is not yet proved for GRDB | 7.11.1 already fixes a Version 27.0 beta warning, and `dev/xcode-27` is active (2026-09-29). The first CI run on `xcode-27` confirms it. Take 7.12.0 when it is tagged |
| **`0xdead10cc`** | The database is never shared with an extension (§5.2); writes that may straddle suspension run under a background-task assertion |

**Where this disagrees with research.** `r1-platforms.md` implication 6 names SwiftData as the fallback "if the team prefers Apple-only code". On the evidence above, the Apple-only fallback should be **Core Data**, not SwiftData. Core Data refuses duplicates by default (C.1), has aggregates (C.2), and needs nothing from iOS 27. SwiftData upserts on collision (B.1), autosaves its main context (B.2), has no aggregates (B.3), and puts its observers out of reach at an iOS 26 floor (B.4). Both Apple options lose S10 entirely.

**The decision would change if:**
- a dated delta forbids third-party Swift packages → Core Data, with the store behind a protocol, a fake on Linux, and every store test on the macOS runner;
- the minimum target rose to iOS 27 → SwiftData's observation gap closes, but B.1, B.3 and B.5 still stand;
- the product wanted CloudKit sync → it does not; the server is the source of truth (FRD §15.1).

---

## Sources (all opened 2026-10-01)

**GRDB.swift**: https://github.com/groue/GRDB.swift (git clone and `ls-remote`)
- `README.md` @ v7.11.1: Requirements; the Linux note; the UPSERT and `RETURNING` notes
- `Package.swift` @ v7.11.1: no dependencies; `GRDBSQLite` system library with `.apt(["libsqlite3-dev"])`; `SQLITE_DISABLE_SNAPSHOT` on Linux
- `CHANGELOG.md` @ `releases/v7.12.0`: 7.12.0 "Released September 29, 2026"; 7.11.1 June 18, 2026; 7.11.0 June 1, 2026; 7.10.0 February 15, 2026 (Linux, Android and Windows adjustments); 7.9.0 December 13, 2025
- `GRDB/Documentation.docc/DatabaseSharing.md` @ v7.11.1; `GRDB/Documentation.docc/Migrations.md` @ v7.11.1
- Tag and commit dates from `git log`; contributor counts from `git log --since=2025-10-01 --no-merges --all`
- `LICENSE`: MIT, "Copyright (C) 2015-2025 Gwendal Roué"

**Other packages:**
- GRDBQuery: https://github.com/groue/GRDBQuery (tag 0.11.0, 2025-03-15)
- SQLiteData: https://github.com/pointfreeco/sqlite-data (tag 1.12.0, 2026-08-31; `README.md`, `Package.swift`)

**SwiftData** (developer.apple.com/documentation/…): `swiftdata`; `swiftdata/unique(_:)`; `swiftdata/schema/attribute/option/unique`; `swiftdata/modelcontext` (topics); `swiftdata/modelcontext/transaction(block:)`; `swiftdata/modelcontext/autosaveenabled`; `swiftdata/modelactor`; `swiftdata/resultsobserver`; `swiftdata/historyobserver`; `swiftdata/historydescriptor`; `swiftdata/schemamigrationplan`; `swiftdata/versionedschema`; `swiftdata/query`; `updates/swiftdata`

**WWDC sessions:**
- https://developer.apple.com/videos/play/wwdc2023/10195/ (Model your schema with SwiftData)
- https://developer.apple.com/videos/play/wwdc2024/10137/ (What's new in SwiftData)
- https://developer.apple.com/videos/play/wwdc2024/10075/ (Track model changes with SwiftData history)
- https://developer.apple.com/videos/play/wwdc2025/291/ (SwiftData: Dive into inheritance and schema migration)

**Core Data** (developer.apple.com/documentation/…): `coredata`; `coredata/nspersistentcontainer`; `coredata/nsentitydescription/uniquenessconstraints`; `coredata/nsmanagedobjectcontext/mergepolicy`; `coredata/nserrormergepolicy`; `coredata/nsconstraintconflict`; `coredata/nsexpressiondescription`; `coredata/nsstagedmigrationmanager`; `coredata/nspersistenthistorytrackingkey`; `coredata/nspersistentstoreremotechangenotificationpostoptionkey`; `coredata/nsfetchedresultscontroller`; `coredata/adopting-swiftdata-for-a-core-data-app`; `swiftui/fetchrequest`

**Doc crawls:** symbol counts by iOS version from each page's `metadata.platforms`, reached through the topic sections (SwiftData 411 pages, Core Data 876 pages). Swift conformances from each page's `relationshipsSections` (`coredata/nsmanagedobject`, `coredata/nsmanagedobjectid`, `coredata/nsmanagedobjectcontext`, `swiftdata/modelcontext`, `swiftdata/persistentidentifier`, `swiftdata/modelcontainer`)

**Platform pages:**
- `widgetkit/adding-interactivity-to-widgets-and-live-activities`
- `xcode/sigkill` (`0xdead10cc`)
- `foundation/optimizing-your-app-s-data-for-icloud-backup`
- `foundation/urlresourcevalues/isexcludedfrombackup`
- `foundation/fileprotectiontype/completeuntilfirstuserauthentication`

**Ran locally:** Swift 6.4 (swift-6.4-RELEASE, x86_64-unknown-linux-gnu), Ubuntu 24.04.4, libsqlite3 3.45.1. The spike's build and tests, GRDB 7.11.1's full test suite, and the `import` checks for SwiftData, CoreData, SwiftUI, Observation and FoundationNetworking.
