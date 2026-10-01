# Ledger — the only place that says what is finished

status: planned → built → banked · quarantined (with a note) · dropped (with a reason) · inherited

| slice | stories | lane | status | commit | evidence | note |
|---|---|---|---|---|---|---|
| (slices are written at Plan to the end) | | | | | | |
| pipeline | part of the foundation slice | main | planned | | | GitHub Actions: Linux job (API, admin) + macOS job (iOS build, simulator walks) |
| ship | — | — | dropped | | | §0 line 10: no hosting target. Risk: nothing is deployed; staging and rollback are unproven until a delta names a target |
| go-live | — | — | dropped | | | §0 line 10. Risk: no production; no TestFlight/App Store submission |
| operate | — | — | dropped | | | §0 line 10. Risk: no alerts, runbooks or restore drill running |
| measure | — | — | dropped | | | §0 line 10. Risk: the §1.5 success measures are not read from live events |
