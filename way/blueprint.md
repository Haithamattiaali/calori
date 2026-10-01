# Sips & Bytes — the blueprint

## §0 The profile

Written 2026-10-01 at Tailor. Brief: `way/brief/frd-v1.0.md` (the owner's FRD v1.0, verbatim). Every later part of the run reads this section, never the chat.

| # | Line | Value | Source |
|---|---|---|---|
| 1 | Kind (per surface) | (a) **mobile — native iOS app** (SwiftUI) for the eater · (b) **service/API** (Python FastAPI) for the app and system actors · (c) **web — admin console** for the nutrition approver, support and platform admin | brief §1.4, §15.1, FR-080 |
| 2 | Size | **product** — 4 human personas (eater, nutrition approver, support agent, platform admin) + system actors; ≈8 workflows; 3 external integrations (Gemini, USDA FoodData Central, HealthKit) on Google/Firebase infrastructure | inferred from the brief |
| 3 | Customization depth | **C1** — versioned product policies (calorie defaults, safety bounds, thresholds), master data (reviewed food records, aliases), model/config registry, roles screen (permissions → roles → users). The eater's own units and rules are user data, not admin builders | inferred from the brief (§3.3, §16.4, FR-080, FR-081) |
| 4 | Tenancy | **one organisation**; every private record scoped to its user; cross-user access is a release blocker | brief NFR-07, §22 |
| 5 | Reach | **English + Arabic** (right-to-left, Arabic-Indic and Western numerals, Egyptian aliases, code-switching voice); user time zones and custom diary-day boundaries; metric first, display units selectable | brief §1.2, §8.1, §14.1 |
| 6 | Systems it must talk to | Gemini (Google Gen AI SDK, Agent Platform) · USDA FoodData Central · Apple HealthKit · Firebase Auth + App Check · Firestore · Cloud Storage · Cloud Run · OR-Tools (library). Each external service sits behind an adapter with a realistic mock until the owner's cutover | brief §15.1; floor.md adapters line |
| 7 | First look | **clickable prototype** of the core journeys before any backend | brief §23.1 (Foundation: "prototype core flows"); recommended default |
| 8 | How it runs | **fast** — the one yes on the prototype, then alone to Close; the MVP is shown without waiting | his answer, 2026-10-01 |
| 9 | Hard limits | Stack per brief §15.1 (SwiftUI · FastAPI · Firebase Auth/App Check · Firestore · Cloud Storage · Gemini · OR-Tools); general-wellness only — no diagnosis, dosing, child/pregnancy planning (§1.3, §19.3); release blockers of §22 are hard; region not yet chosen (§15.3) | brief |
| 10 | Where it lives and ships | **a remote, no hosting yet** — GitHub `Haithamattiaali/calori` (public), this session's branch `claude/magical-cerf-axi3k1` with a draft PR. His answer is the written go for the remote and the pipeline only; no staging, no production | his answer, 2026-10-01 |

### What the profile switches on and off

- **On:** the canvas at the prototype (mobile); proof of the iOS surface on the smallest and largest iPhone simulator; proof of the API through its own HTTP interface; the admin console proved in a browser at desktop and ~390 px; C1 (configuration module, master data, roles screen, lighter customization proof); English + Arabic RTL and time zones from the first slice; the prototype and `design.md`; the pipeline on the remote.
- **Off:** C2 builders and scouts; many-tenant probes (per-user isolation probes stay — NFR-07); the ship proof, go-live, operate and measure rows — **dropped with their risk until a dated delta names a hosting target** (risk: the certified commit is buildable and tested in CI but not deployed; no alerts, restore drill or KPI reviews run).

### Environment facts that shape the hows (read 2026-10-01)

- This session runs in a Linux cloud container: Node 22, Python 3.11, Java (Firebase emulators possible), Docker, Postgres client; **no Xcode, no Swift, no gcloud, no `gh`** (GitHub through its MCP tools).
- The iOS surface is compiled and proved on **GitHub Actions macOS runners** (free: the repo is public); its walks run on the iOS simulator there and return screenshots as run artifacts.
- The keep-going loop: this cloud session works the ledger turn by turn and resumes from git and `way/` (`/build-loop` lives on his Mac, not here).
- The repo is public: synthetic data only, no secrets in the tree, ever.

## §1 The map

(written at Map)

## §2 The blueprint

(written at Plan to the end)

## §3 The yes, the dated deltas, the production go

(empty)
