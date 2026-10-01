# Journey — where we are

## 2026-10-01 · Tailor
- Brief: the owner's FRD v1.0 (`way/brief/frd-v1.0.md`), "we need ios app".
- Intake: 8 of 10 profile lines answered from the FRD; he answered run mode (fast) and ship target (GitHub only, no hosting yet).
- Decision: iOS compiled and proved on GitHub Actions macOS runners, because this container has no Xcode and the repo is public (free minutes).
- Next: Map — the first map from the brief, research cycle 1, the final map.
- Tailor closed: governor found 4 gaps, all fixed (b183a45, e834f12); branch pushed; keep-going check-in armed (trig_01TqQZcLTZVn6idwgPjS7Hd6).

## 2026-10-01 · Map
- Research cycle 1: 4 researchers (178 findings), 2 refuters (re-opened after he widened network access): competitors 78 stand / 3 refuted / 8 doubtful / 4 assumption; platforms+rules 81 stand / 4 refuted.
- Final map in §1: 5 personas (auditor found hidden), 10 workflows, Grants approved by the eater; delta D1 → platform size.
- Governor: 6 gaps, fixed, re-audit left 2 parts, fixed. Map closed.
- Swift 6.4 installed locally for the client core's `swift test`.
- Next: persona lenses in parallel.

## 2026-10-01 · Persona lenses
- Five lenses: Eater (fanned out: research + 4 journey files), Nutrition approver, Support agent, Platform admin, Auditor — 623 stories in all (eater 367, admin 75, approver 70, auditor 59, support 52), every one with a runtime acceptance line.
- Every lens failed its first verifier (16–27 defects each); fix rounds, then the session's own diagnosis after two rounds; every file's last verdict is pass.
- Deltas: D2 (one vocabulary for roles, states, errors, places), D3 (Consent "Not given").
- Lessons: lenses revised in parallel drift; cross-lens mismatches are joined once in the model phase; a session fix must be re-read against every story it touches.
- Next: governor audit of the lenses, then Model and architecture (start with the join: one fixture set, one event catalogue, the conflicts lists).

## 2026-10-01 · Model and architecture
- The join (J1–J160), one seed, one event catalogue (120 events); 230 interaction rows; 70 entities; 22 modules; decisions A1–A24 with sources; the contract (149 paths, 172 operations, 355 schemas, valid OpenAPI 3.1); the SDK list with a full-set resolve and audit.
- Deltas: D4 (the join's names), D5 (six stories), D6 (J149–J157), D7 (contract changes after its first write).
- Governor: 8 gaps → fixed; re-audit left 2 (google-cloud-storage audit, stale contract notes) → fixed; final re-audit running.

## 2026-10-01 · Plan to the end
- 74 slices (64 build + care pass C01–C10) in six lanes; 629 of 629 stories placed once; 172 of 172 operations provided once; dependency map and critical path in model §6; the ledger has one row per slice.
- Three planner calls decided: tab bar grows one tab per slice (no dead door) — kept; lanes rebalanced — kept; S07b (Target) below the MVP line — **overruled**: the owner's goal is weight loss, so S07d + S07b join the MVP (11 slices, 108 stories); S08 (Day report screen) stays below.
- Governor audit of the plan running.
- Next: the first look — briefs per MVP screen, the Design canvas (go-wide, then exact), the clickable prototype, the one yes.
