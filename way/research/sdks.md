# R3 · The SDKs and libraries behind each architecture decision

Research cycle 3, written 2026-10-01 for the /way build of Sips & Bytes. Reads `way/blueprint.md` §0 (hard limits: SwiftUI · FastAPI · Firebase Auth/App Check · Firestore · Cloud Storage · Gemini · OR-Tools), `way/research/r1-platforms.md` and `way/research/r1-refute-b.md`. Edits nothing else.

## How to read this file

- **Labels.**
  - `opened`: I opened the source in this run, and any quote is its text.
  - `measured`: I ran it in this session's container on 2026-10-01, and the result is the output.
  - `assumption`: no source opened and no run. Verify it before relying on it.
- **Date.** Every source was accessed on **2026-10-01**. A second date is the date the source itself carries (upload, tag commit or page update).
- **Versions.** Each version comes from the registry or the installed binary, never from memory: the PyPI JSON plus `pip index versions`, `npm view`, `git ls-remote` on the vendor's repository with the tag's commit date from a shallow clone, swift.org's release API, and `swift --version`.
- **Licence.** From the LICENSE file at the release tag (raw.githubusercontent.com) and the registry metadata.
- **Security.** OSV.dev was queried for the exact pinned version. OSV merges the GitHub Advisory Database with the PyPA database. I also checked deps.dev's advisory keys. The open-issue counts and OpenSSF Scorecards come from deps.dev's project API, which reads GitHub.
- **Access limit.** Through the session proxy, GitHub's REST API, its release pages and its security-advisory pages answered 403 for repositories not attached to this session. Git (`ls-remote`, clone) and raw files worked. So "open security issues" here means *published advisories that affect the pinned version*. A private vulnerability report that has not been published is invisible to anyone outside the project, by design.
- **Privacy.** All requests used a generic User-Agent. No owner identifier was sent anywhere, and the authenticated GitHub connector was not used. The emulator probe used a synthetic e-mail.

---

## The table

| decision | package | version | link | licence | why |
|---|---|---|---|---|---|
| Python runtime | CPython **3.13** | 3.13.16 (2026-09-30). Alternative: 3.12.15 (2026-09-30) | https://devguide.python.org/versions/ · https://github.com/python/cpython | PSF-2.0 | 3.13 gets bug fixes until 2029-10; 3.12 gets security fixes only (until 2028-10). Every pinned package supports 3.13. firebase-admin tests stop at 3.13, so 3.14 must wait (N1) |
| API framework | `fastapi` | 0.142.2 (2026-09-30) | https://github.com/fastapi/fastapi | MIT | The stack is fixed by §0 line 9. It emits OpenAPI **3.1.0** by default (N5) |
| ASGI toolkit (FastAPI dependency, pinned for security) | `starlette` | 1.7.0 (2026-09-23); **floor 1.3.1** | https://github.com/Kludex/starlette | BSD-3-Clause | FastAPI asks only for `>=0.46.0`. Four advisories of 2026-06-15 are fixed in 1.1.0–1.3.1 (N4) |
| Validation, settings, response schemas | `pydantic` | 2.13.5 (2026-08-28) | https://github.com/pydantic/pydantic | MIT | Latest stable; 2.14.0b2 is a pre-release. Gemini's `response_json_schema` is built from these models (r1 P8) |
| ASGI server | `uvicorn[standard]` | 0.54.0 (2026-09-25) | https://github.com/Kludex/uvicorn | BSD-3-Clause | FastAPI's own server. Needs Python ≥3.10 |
| Document store client | `google-cloud-firestore` | 2.33.0 (2026-09-29) | https://github.com/googleapis/google-cloud-python/tree/main/packages/google-cloud-firestore | Apache-2.0 | Official client. Its README says "Python >= 3.10, including 3.14". Works against the emulator (N6) |
| Auth ID-token and App Check verification | `firebase-admin` | 7.7.0 (2026-09-23) | https://github.com/firebase/firebase-admin-python | Apache-2.0 | Official Admin SDK: `auth.verify_id_token` and `app_check.verify_token(token, consume=False)` (N7). Pins `httpx[http2]==0.28.1` exactly (N3) |
| Gemini (Gemini API and Agent Platform) | `google-genai` | 2.26.0 (2026-09-30) | https://github.com/googleapis/python-genai | Apache-2.0 | One SDK for both backends: `genai.Client(enterprise=True, project=…, location=…)` (N8). The older SDKs are deprecated (r1 P10) |
| Planner (CP-SAT) | `ortools` | 9.15.6755 (2026-01-14) | https://github.com/google/or-tools | Apache-2.0 | Google's solver suite, `ortools.sat.python.cp_model`. Wheels for cp39–cp314, manylinux x86_64/aarch64. It caps protobuf at `<6.34` (N2) |
| Console templates | `jinja2` | 3.1.6 (2025-03-05) | https://github.com/pallets/jinja | BSD-3-Clause | Starlette's `Jinja2Templates`. 3.1.6 fixed GHSA-cpwx-vrp4-4pq7, so it is also the floor |
| Console form parsing | `python-multipart` | 0.0.32 (2026-06-04); **floor 0.0.31** | https://github.com/Kludex/python-multipart | Apache-2.0 | FastAPI needs it for `Form()`. Its own floor is `>=0.0.18`, but seven advisories are fixed up to 0.0.31 (N4) |
| Console interactivity | `htmx.org` | **2.0.11** (2026-09-22, npm `latest`); 4.0.0 (2026-08-28) is on npm `next` | https://github.com/bigskysoftware/htmx | 0BSD | Matches `option-console.md`: no build step, one vendored file. htmx says 4.0 becomes `latest` "at some point in 2027" (N15) |
| Console SPA alternative: UI | `react` + `react-dom` | 19.3.0 (2026-09-09) | https://github.com/react/react | MIT | Only if the fallback (B′ in `option-console.md`) is chosen. npm now names `github.com/react/react` as the repository |
| Console SPA alternative: build | `vite` + `@vitejs/plugin-react` | 8.3.1 (2026-09-24) + 6.1.1 (2026-08-28) | https://github.com/vitejs/vite | MIT | Needs Node `^20.19.0 \|\| >=22.12.0`; this container has 22.22.2 (N16) |
| API tests: runner | `pytest` | 9.1.1 (2026-06-19) | https://github.com/pytest-dev/pytest | MIT | Standard runner. Schemathesis needs `pytest<10,>=8.4`, which this meets |
| API tests: HTTP client | `httpx2` | 2.13.1 (2026-09-23) | https://github.com/pydantic/httpx2 | BSD-3-Clause | Starlette 1.7.0's `TestClient` imports `httpx2` first and calls plain `httpx` "deprecated". It is Pydantic's continuation of HTTPX (N3) |
| HTTP client inside the runtime tree (not chosen; pinned by others) | `httpx` | 0.28.1 (2024-12-06) | https://github.com/encode/httpx | BSD-3-Clause | firebase-admin pins `==0.28.1` and google-genai needs `<1.0.0`. Its last stable release is 22 months old, and only `1.0.dev` builds have followed (N3) |
| Console walks in a browser | `playwright` + `pytest-playwright` | 1.63.0 (2026-09-15) + 0.9.0 (2026-08-10) | https://github.com/microsoft/playwright-python | Apache-2.0 | Official Python port. It rendered an RTL Arabic page at 390×844 here (N17) |
| Contract tests from the OpenAPI 3.1 file | `schemathesis` | 4.28.0 (2026-09-22) | https://github.com/schemathesis/schemathesis | MIT | Generates property-based requests from the schema. Supports OpenAPI 2.0, 3.0, **3.1** and 3.2 (N5) |
| Contract tests alternative: request/response validation | `openapi-core` (no extras) | 0.23.1 (2026-04-02) | https://github.com/python-openapi/openapi-core | BSD-3-Clause | Validates against 3.0/3.1/3.2. **Its `[fastapi]` and `[starlette]` extras conflict with our pins** (N5) |
| Lint and format | `ruff` | 0.16.9 (2026-09-24) | https://github.com/astral-sh/ruff | MIT | One tool for lint and format |
| Type check | `mypy` | 2.3.1 (2026-08-15) | https://github.com/python/mypy | MIT | Reference checker with Pydantic support. Needs Python ≥3.10 |
| Type check alternative | `pyright` (PyPI wrapper) / npm `pyright` | 1.1.414 (2026-09-10 PyPI, 2026-09-09 npm) | https://github.com/microsoft/pyright | MIT | Faster and stricter. The PyPI package downloads Node and the npm build (N18) |
| Dependency audit | `pip-audit` | 2.10.1 (2026-06-10) | https://github.com/pypa/pip-audit | Apache-2.0 | PyPA's auditor (PyPI or OSV service). Found nothing in the full pinned set (N19) |
| SBOM | `cyclonedx-bom` (`cyclonedx-py`) | 7.5.0 (2026-09-29) | https://github.com/CycloneDX/cyclonedx-python | Apache-2.0 | The OWASP CycloneDX generator. Writes spec 1.0–1.7, default 1.6 (N19) |
| Local Firebase emulators | `firebase-tools` (npm) + OpenJDK **21** | 15.32.1 (2026-09-30); Java 21.0.10 installed | https://github.com/firebase/firebase-tools | MIT | Firestore, Auth and Tasks emulators for tests. The code requires Java ≥21 and Node ≥20; the docs are stale (N6) |
| Swift toolchain (Linux, platform-neutral core) | Swift | **6.4.0** (2026-09-14); installed: `swift-6.4-RELEASE` | https://www.swift.org/install/ · https://github.com/swiftlang/swift | Apache-2.0 with Runtime Library Exception | `swift build`/`swift test` of the client core here. Built and tested on Ubuntu 24.04 (N9) |
| Xcode on the CI macOS runner | Xcode on `macos-26` (arm64) | **26.6** default (Swift **6.3**, iOS 26.5 SDK), image 20260907; Xcode 27.0 (Swift 6.4, iOS 27 SDK) only on the `xcode-27` preview label | https://github.com/actions/runner-images/blob/main/images/macos/macos-26-arm64-Readme.md | MIT (runner-images) | The free runner for a public repo. The Swift version differs from Linux, which sets the package's tools version (N10) |
| Local store (ledger, outbox) | `GRDB.swift` | 7.11.1 (2026-06-18) | https://github.com/groue/GRDB.swift | MIT | Explicit transactions, unique idempotency keys, versioned migrations (r1 P21). Built and ran on Linux here; Linux support is unofficial (N12) |
| Local store alternative | SwiftData (Apple framework) | ships with the SDK; iOS 17.0+, `HistoryDescriptor` iOS 18.0+ | https://developer.apple.com/documentation/swiftdata | Apple SDK licence | Apple-only, no Linux build (`no such module 'SwiftData'`, measured). Its useful observers are iOS 27-only (r1 P20). Kept as the fallback only |
| Firebase on iOS (Auth, App Check) | `firebase-ios-sdk` (SPM products `FirebaseAuth`, `FirebaseAppCheck`) | **12.19.2** (2026-09-15); 13.0.0 is staged as the `CocoaPods-13.0.0` tag (2026-09-24), with no SPM tag yet | https://github.com/firebase/firebase-ios-sdk | Apache-2.0 | Official, distributed through SPM. Needs Xcode 26.2+ and iOS 15+. App Attest provider on device, debug provider on simulator and CI (N7, N13) |
| OpenAPI → Swift client: generator | `swift-openapi-generator` (build plugin) | 1.13.1 (2026-08-28) | https://github.com/apple/swift-openapi-generator | Apache-2.0 | Apple's generator. Reads OpenAPI 3.0/3.1 (3.2 preliminary). Runs on macOS, Linux and Windows (N11) |
| OpenAPI → Swift client: runtime | `swift-openapi-runtime` | 1.12.2 (2026-09-29) | https://github.com/apple/swift-openapi-runtime | Apache-2.0 | Needed by the generated code. Supported on Linux and iOS 13+ |
| OpenAPI → Swift client: transport | `swift-openapi-urlsession` | 1.3.2 (2026-09-28) | https://github.com/apple/swift-openapi-urlsession | Apache-2.0 | A URLSession transport, so no third-party HTTP stack. Streaming works only on Apple OSes (N11) |
| Swift tests | swift-testing (`import Testing`) | ships with the toolchain: 6.4.0 tag (2026-08-18) on Linux and `xcode-27`; the Swift 6.3 build on `macos-26` | https://github.com/swiftlang/swift-testing | Apache-2.0 with Runtime Library Exception | Apple docs: "Swift 6.0, Xcode 16.0". Ran on Linux here (N9) |
| CI: checkout | `actions/checkout` | v7.0.1 (2026-07-17), commit `3d3c42e5aac5ba805825da76410c181273ba90b1` | https://github.com/actions/checkout | MIT | `node24`. v7 refuses to check out fork PR code under `pull_request_target`/`workflow_run` by default (N14) |
| CI: Python | `actions/setup-python` | v7.0.0 (2026-07-19), commit `5fda3b95a4ea91299a34e894583c3862153e4b97` | https://github.com/actions/setup-python | MIT | `node24`. Its manifest has 3.13.16, 3.12.15, 3.14.8 and 3.15.0-rc.2 (N14) |
| CI: artifacts (screenshots, SBOM, reports) | `actions/upload-artifact` | v7.0.1 (2026-04-10), commit `043fb46d1a93c77aae656e7c1c64a875d1fc6a0a` | https://github.com/actions/upload-artifact | MIT | `node24`. Carries the simulator screenshots from `.xcresult` (r1 P36) |

### Security and upkeep per package

"OSV" means the advisories that affect the pinned version. "Issues" means deps.dev's count of open issues for the GitHub repository. "Scorecard" is the OpenSSF Scorecard from deps.dev, dated 2026-08-24; "—" means none is published. All `opened` on 2026-10-01.

| package | OSV | issues | Scorecard |
|---|---|---|---|
| fastapi 0.142.2 | 0 | 82 | — |
| starlette 1.7.0 | 0 (1.0.1 has 4, see N4) | 58 | — |
| pydantic 2.13.5 | 0 | 590 | 6.8 |
| uvicorn 0.54.0 | 0 | 103 | — |
| google-cloud-firestore 2.33.0 | 0 | 569 (monorepo) | 8.3 |
| firebase-admin 7.7.0 | 0 | 135 | 8.1 |
| google-genai 2.26.0 | 0 | 323 | — |
| ortools 9.15.6755 | 0 | 125 | 4.2 |
| jinja2 3.1.6 | 0 | 104 | 5.9 |
| python-multipart 0.0.32 | 0 | 12 | — |
| htmx.org 2.0.11 / 4.0.0 | 0 / 0 | 273 | 5.0 |
| react 19.3.0 | 0 | 1,384 | 7.0 |
| vite 8.3.1 · @vitejs/plugin-react 6.1.1 | 0 · 0 | 759 · 103 | 6.8 · — |
| pytest 9.1.1 | 0 | 842 | 7.0 |
| httpx2 2.13.1 | 0 | 100 | — |
| httpx 0.28.1 | 0 | 140 | 4.9 |
| playwright 1.63.0 · pytest-playwright 0.9.0 | 0 · 0 | 20 · 27 | 5.7 · — |
| schemathesis 4.28.0 | 0 | 13 | 5.9 |
| openapi-core 0.23.1 | 0 | 105 | — |
| ruff 0.16.9 | 0 | 2,189 | — |
| mypy 2.3.1 | 0 | 3,231 | 6.6 |
| pyright 1.1.414 | 0 | 322 | 6.8 |
| pip-audit 2.10.1 | 0 | 63 | — |
| cyclonedx-bom 7.5.0 | 0 | 33 | 6.7 |
| firebase-tools 15.32.1 | 0 | 1,050 | 6.1 |
| GRDB.swift 7.11.1 | 0 (none ever recorded in OSV) | 12 | 5.0 |
| firebase-ios-sdk 12.19.2 | 0 (none ever recorded) | 427 | 6.0 |
| swift-openapi-generator / -runtime / -urlsession | 0 / 0 / 0 (none ever recorded) | 106 / 12 / 12 | — |
| swift-testing | not in OSV | 131 | — |
| actions/checkout v7.0.1 · setup-python v7.0.0 · upload-artifact v7.0.1 | 0 · 0 · 0 | 704 · 55 · 262 | 7.0 · 6.6 · 5.2 |

---

## Compatibility notes

**N1 · Python 3.13 is the runtime. 3.14 must wait for firebase-admin. This container's Python 3.11 is not the target.** `opened`, `measured`
- https://devguide.python.org/versions/ · 2026-10-01 · "3.13 | PEP 719 | bugfix | 2024-10-07 | 2029-10"; "3.12 | PEP 693 | security | 2023-10-02 | 2028-10"; "3.10 | … | security | … | 2026-10"; "3.15 | PEP 790 | prerelease | 2026-10-01".
- https://www.python.org/api/v2/downloads/release/ · 2026-10-01 · "Python 3.13.16" and "Python 3.12.15", both released 2026-09-30. The cpython tags `v3.13.16`, `v3.12.15`, `v3.14.8` and `v3.15.0rc2` also exist.
- PyPI JSON · 2026-10-01 · every backend package needs Python ≥3.10 (fastapi, google-genai, google-cloud-firestore, pytest, mypy, schemathesis, playwright, pip-audit) or ≥3.9 (pydantic, firebase-admin, ortools).
- https://github.com/firebase/firebase-admin-python (`setup.py`, `.github/workflows/ci.yml`) · 2026-10-01 · classifiers stop at "Python :: 3.13", and the CI matrix is `['3.9', '3.10', '3.11', '3.12', '3.13', 'pypy3.9']`. The README says "We currently support Python 3.9+. However, Python 3.9 support is deprecated". **3.14 is not tested**, so do not move past 3.13 yet.
- https://github.com/googleapis/google-cloud-python/…/google-cloud-firestore/README.rst · 2026-10-01 · "Python >= 3.10, including 3.14".
- PyPI `ortools` 9.15.6755 files · 2026-10-01 · `cp312`, `cp313` and `cp314` wheels for `manylinux_2_27_x86_64`, `manylinux_2_28_aarch64`, `macosx_11_0_arm64` and `win_amd64`, and free-threaded `cp313t`/`cp314t` for Linux.
- `measured`: `uv pip compile` resolves the whole pinned set for Python 3.12, 3.13 and 3.14. I installed it into a uv-managed CPython 3.13.12 venv. A pytest smoke run imported every package, solved a CP-SAT model behind a FastAPI route through `TestClient`, and checked that `app.openapi()["openapi"]` starts with "3.1": "2 passed". The container's system Python is 3.11.15, so local runs use `uv` (`uv venv -p 3.13`), and CI uses `actions/setup-python` with `3.13`.

**N2 · OR-Tools holds the whole Google stack on protobuf 6.33.x.** `opened`, `measured`
- PyPI `ortools` 9.15.6755 `requires_dist` · 2026-10-01 · `protobuf<6.34,>=6.33.1`, `numpy>=2.0.2`, `pandas>=2.0.0`.
- PyPI `google-cloud-firestore` 2.33.0 · 2026-10-01 · `protobuf<8.0.0,>=6.33.5`, `grpcio<2.0.0,>=1.75.1; python_version >= "3.14"`.
- `pip index versions protobuf` · 2026-10-01 · latest **7.36.2**, so the newest allowed by OR-Tools is 6.33.6.
- `measured`: the resolver chose `protobuf==6.33.6`, `grpcio==1.84.0`, `numpy==2.5.3` and `pandas==3.0.6` on 3.12, 3.13 and 3.14.
- OR-Tools' last release was 2026-01-14 (git tag `v9.15`, commit 2026-01-09). Until it ships again, a future firestore or google-api-core that needs protobuf 7 cannot be installed with it. The lockfile must pin protobuf, and the weekly pip-audit run watches 6.33.x.

**N3 · Use httpx2 for tests. Leave httpx 0.28.1 where firebase-admin pins it.** `opened`, `measured`
- PyPI `firebase-admin` 7.7.0 `requires_dist` · 2026-10-01 · `httpx[http2]==0.28.1` (an exact pin). PyPI `google-genai` 2.26.0 · `httpx<1.0.0,>=0.28.1`.
- PyPI `httpx` · 2026-10-01 · latest stable 0.28.1, uploaded 2024-12-06; pre-releases up to `1.0.dev6` (2026-08-31).
- https://github.com/pydantic/httpx2 (README) · 2026-10-01 · "With HTTPX itself seeing limited activity recently, Pydantic is picking up stewardship under the HTTPX2 name so that users have a reliably maintained path forward - including timely security updates". PyPI `httpx2` 2.13.1 was uploaded 2026-09-23.
- https://github.com/Kludex/starlette/blob/1.7.0/starlette/testclient.py · 2026-10-01 · `import httpx2 as httpx`, falling back to `import httpx` with the warning "Using `httpx` with `starlette.testclient` is deprecated; install `httpx2` instead."
- `measured`: httpx 0.28.1 and httpx2 2.13.1 install side by side, because their import names differ. With httpx2 present, `TestClient` raised no warning.

**N4 · Some security floors sit above FastAPI's own minimums. Pin them.** `opened`, `measured`
- PyPI `fastapi` 0.142.2 · 2026-10-01 · `starlette>=0.46.0`; its `standard` extra adds `python-multipart>=0.0.18` and `jinja2>=3.1.5`.
- OSV `starlette` (https://api.osv.dev/v1/vulns/…) · 2026-10-01, all published 2026-06-15: GHSA-82w8-qh3p-5jfq (HIGH, "request.form() limits silently ignored", fixed 1.3.1); GHSA-wqp7-x3pw-xc5r (HIGH, "SSRF and NTLM credential theft via UNC paths in StaticFiles on Windows", fixed 1.1.0); GHSA-x746-7m8f-x49c (MODERATE, fixed 1.1.0); GHSA-jp82-jpqv-5vv3 (LOW, fixed 1.3.0).
- OSV `python-multipart` 0.0.17 · 2026-10-01 · seven GHSAs, fixed in 0.0.18, 0.0.22, 0.0.26, 0.0.27, 0.0.30 and 0.0.31. OSV `jinja2` 3.1.5 · GHSA-cpwx-vrp4-4pq7, fixed in 3.1.6.
- Result: declare `starlette>=1.3.1`, `python-multipart>=0.0.31` and `jinja2>=3.1.6` directly in `pyproject.toml`. Without these lines, a resolver that has to back-track (see N5) can legally fall below the fixed versions. `measured`: pip-audit found nothing in the pinned set (N19).
- Install extra: `fastapi[standard]` pulls `fastapi-cli[standard]`, which includes the FastAPI Cloud CLI. PyPI lists a `standard-no-fastapi-cloud-cli` extra, so use it, or list the needed packages yourself (`opened`, PyPI `requires_dist`).

**N5 · Contract tests: schemathesis drives them against the OpenAPI 3.1 file. Install openapi-core only without extras.** `opened`, `measured`
- https://github.com/fastapi/fastapi/blob/0.142.2/fastapi/applications.py · 2026-10-01 · "FastAPI will generate OpenAPI version 3.1.0". `measured`: `app.openapi()["openapi"]` starts with "3.1".
- https://schemathesis.readthedocs.io/en/stable/ · 2026-10-01 · "Supported specifications … OpenAPI 2.0 (Swagger), 3.0, 3.1, 3.2". The repo's `docs/index.md` says the same.
- https://github.com/python-openapi/openapi-core (README.md) · 2026-10-01 · "for the OpenAPI v3.0 and OpenAPI v3.1 and OpenAPI v3.2 specifications".
- PyPI `openapi-core` 0.23.1 `requires_dist` · 2026-10-01 · `fastapi<0.140,>=0.111; extra == "fastapi"` and `starlette<1.1.0,>=0.40.0; extra == "starlette"`.
- `measured`: `fastapi==0.142.2` + `openapi-core[fastapi]==0.23.1` fails with "your requirements are unsatisfiable". `openapi-core[starlette]` resolves, but only by **downgrading Starlette to 1.0.1**, which has four published advisories (N4). `openapi-core` without extras resolves cleanly with Starlette 1.7.0.
- Plan: keep `contracts/openapi.yaml` (3.1) as the single source. A CI test asserts that FastAPI's generated document matches it. `schemathesis run` (or its pytest integration) runs against the app. The Swift client is generated from the same file (N11).

**N6 · The Firebase emulators run here on Java 21. The Firebase install page is stale. There is no App Check emulator.** `opened`, `measured`
- https://github.com/firebase/firebase-tools/blob/v15.32.1/src/emulator/commandUtils.ts · 2026-10-01 · `MIN_SUPPORTED_JAVA_MAJOR_VERSION = 21` / "firebase-tools no longer supports Java version before 21." `npm view firebase-tools` gives engines `>=20.0.0 || >=22.0.0 || >=24.0.0`.
- https://firebase.google.com/docs/emulator-suite/install_and_configure · 2026-10-01 · "Node.js version 16.0 or higher. Java JDK version 11 or higher." **This contradicts the code. Follow the code: Java 21+ and Node 20+.**
- `src/emulator/downloadableEmulatorInfo.json` @v15.32.1 · 2026-10-01 · Firestore emulator `1.22.0`. `src/emulator/types.ts` · `enum Emulators { AUTH, HUB, FUNCTIONS, FIRESTORE, DATABASE, HOSTING, APPHOSTING, PUBSUB, UI, LOGGING, STORAGE, EXTENSIONS, EVENTARC, DATACONNECT, TASKS }`. The list has **no App Check emulator**, but it has a **Tasks** emulator, which can back the async-job seam.
- `measured`: `npx firebase emulators:exec --project demo-sips --only firestore,auth` on OpenJDK 21.0.10 and Node 22.22.2 downloaded `cloud-firestore-emulator-v1.22.0.jar` and started both emulators. A Python 3.13 script then wrote and read a Firestore document (`{'kcal': 481}`) and created a synthetic Auth user through firebase-admin 7.7.0: "Script exited successfully (code 0)".
- Result: App Check verification sits behind an adapter with a mock in tests and CI, as §0 line 6 already requires for external services.

**N7 · App Check replay protection: the Python SDK has it, but the docs say Node.js only.** `opened`
- https://github.com/firebase/firebase-admin-python/blob/v7.7.0/firebase_admin/app_check.py · 2026-10-01 · `def verify_token(token: str, app=None, consume: bool = False)`, and when consumed the result "also includes an ``already_consumed`` boolean key". The server must reject the request when that key is true (r1-refute-b, Dropped 6).
- https://firebase.google.com/docs/app-check/custom-resource-backend · 2026-10-01 · "Replay protection (beta) … Note: The replay protection beta supports only the Node.js SDK. Using replay protection adds a network round trip … we recommend that you enable replay protection only on particularly sensitive endpoints." It needs the "Firebase App Check Token Verifier" role on the service account.
- https://firebase.google.com/docs/app-check/ios/debug-provider · 2026-10-01 · "your app's features that depend on that backend service won't run in a simulator or from a continuous integration (CI) environment because these environments don't qualify as valid devices." Use the debug provider there.
- https://firebase.google.com/docs/app-check/ios/app-attest-provider · 2026-10-01 · App Attest on iOS 14+, which our iOS 26 target covers.
- Whether `consume=True` works from Python against a real project: `assumption`. Treat it as a beta, use it only on the AI endpoints, and prove it at cutover.

**N8 · google-genai 2.26.0 really has `enterprise=True`.** `opened`, `measured`
- https://github.com/googleapis/python-genai/blob/v2.26.0/google/genai/client.py · 2026-10-01 · `enterprise: Optional[bool] = None,` / "vertexai (bool): Legacy flag for `enterprise`." The README says `genai.Client(enterprise=True, project='your-project-id', location='global')` and `export GOOGLE_GENAI_USE_ENTERPRISE=true`. CHANGELOG: "## [2.26.0] … (2026-09-30)".
- `measured`: `"enterprise" in inspect.signature(genai.Client.__init__).parameters` holds on the installed 2.26.0.
- The model and region facts (3.8 Flash only on `global` and the `us`/`eu` multi-regions; no Middle East region) stay as corrected in r1-refute-b P11.

**N9 · Swift 6.4 on Linux builds and tests the platform-neutral SwiftPM core, including the OpenAPI client and GRDB.** `opened`, `measured`
- https://www.swift.org/api/v1/install/releases.json · 2026-10-01 · "6.4.0 · 2026-09-14 · swift-6.4.0-RELEASE", with platforms Ubuntu 22.04, 24.04 and 26.04, Debian 12 and 13, Fedora 41, Amazon Linux 2023, UBI 9 and 10, Windows 10, Static SDK, Wasm SDK and Android SDK. The `swiftlang/swift` tag `swift-6.4.0-RELEASE` has commit date 2026-09-13.
- Installed binary · 2026-10-01 · `swift --version` → "Swift version 6.4 (swift-6.4-RELEASE) Target: x86_64-unknown-linux-gnu" on Ubuntu 24.04.
- `measured`: I built a probe package with `swift-tools-version: 6.3` and `platforms: [.iOS(.v26), .macOS(.v15)]`, using the `OpenAPIGenerator` build plugin 1.13.1, `OpenAPIRuntime` 1.12.2, `OpenAPIURLSession` 1.3.2 and `GRDB` 7.11.1.
  - `swift build --build-tests` → "Build complete! (61.20 secs)". It generated a `Client` from an OpenAPI **3.1.0** file that uses `type: [string, 'null']`. The only warnings were unused-`Foundation`-import warnings in the generated sources.
  - `swift test` → "Testing Library Version: 6.4 … Target Platform: x86_64-unknown-linux-gnu … ✔ Test run with 2 tests in 0 suites passed". One test constructed `Client(serverURL:transport: URLSessionTransport())`; the other opened a GRDB `DatabaseQueue` in memory and ran SQL.
  - Resolved versions: OpenAPIKit 6.4.0, Yams 6.2.2, swift-http-types 1.8.0, swift-collections 1.7.1, swift-argument-parser 1.8.2, swift-algorithms 1.2.1.
- `measured`: with `swift-tools-version: 6.1` the manifest fails: "'v26' is unavailable … 'v26' was introduced in PackageDescription 6.2". An iOS 26 floor therefore needs tools version ≥6.2.
- `measured`: `swiftc -typecheck` of `import SwiftData` → "error: no such module 'SwiftData'". `import Testing` type-checks.
- https://github.com/firebase/firebase-ios-sdk/blob/12.19.2/Package.swift · 2026-10-01 · `platforms: [.iOS(.v15), .macCatalyst(.v15), .macOS(.v10_15), .tvOS(.v15), .watchOS(.v7)]` has no Linux, so Firebase Auth and App Check live in the iOS app target, behind protocols the core defines.

**N10 · Linux and the default macOS runner have different Swift versions. Use tools version 6.3.** `opened`
- https://github.com/actions/runner-images/blob/main/images/macos/macos-26-arm64-Readme.md · 2026-10-01 · "OS Version: macOS 26.6.2 (25G83)", "Image Version: 20260907.0351.1", "26.6 (default) | 17F113 | /Applications/Xcode_26.6.app", and Xcodes 26.0.1–26.6 with no Xcode 27. Simulators: iPhone 17e … 17 Pro Max on iOS 26.4/26.5.
- https://developer.apple.com/documentation/xcode-release-notes/xcode-26_6-release-notes · 2026-10-01 · "Xcode 26.6 includes Swift 6.3 and SDKs for iOS 26.5". The Xcode 27 release notes: "Xcode 27 includes Swift 6.4 and SDKs for iOS 27".
- https://github.com/actions/runner-images/blob/main/images/macos/xcode-27-arm64-Readme.md · 2026-10-01 · "27.0 (default) | 27A266a"; 27.1; "27.2 (beta)". The README marks the `xcode-27` label "[preview]" (issue #14404). The README maps `macos-latest` and `macos-26` to "macOS 26 Arm64".
- Result: keep the shared package at `swift-tools-version: 6.3` and avoid 6.4-only features in the core, so it builds on Linux (6.4), `macos-26` (6.3) and `xcode-27` (6.4). Run the iOS lane on `xcode-27` for the iOS 27 SDK (r1 implication 11), with `macos-26` + Xcode 26.6 as the fallback. Uploads need the iOS 27 SDK from April 2027 (r1-refute-b R15).

**N11 · swift-openapi-generator works on Linux. The URLSession transport works on Linux without streaming.** `opened`, `measured`
- https://github.com/apple/swift-openapi-generator/blob/1.13.1/README.md · 2026-10-01 · "Works with OpenAPI Specification versions 3.0 and 3.1 and has preliminary support for version 3.2." / "The generator is used during development and is supported on macOS, Linux, and Windows." / "Generator plugin and CLI | ✅ 10.15+ | ✅ (Linux, Windows) | ✖️ (iOS)" / "Generated code and runtime library | … ✅ (Linux, Windows) | ✅ 13+ (iOS)".
- https://github.com/apple/swift-openapi-urlsession/blob/1.3.2/README.md · 2026-10-01 · "Note: Streaming support only available on macOS 12+, iOS 15+ … For streaming support on Linux, please use the AsyncHTTPClient Transport".
- Package.swift at the tags · 2026-10-01 · all three packages declare `swift-tools-version:6.1`; the generator's platforms are `.macOS(.v10_15)`, plus the iOS family "to emit a more descriptive compiler error".
- `measured` on Linux: see N9.
- `assumption`: an `xcodebuild` run in CI must skip the interactive "Trust & Enable" step for package build plugins (`-skipPackagePluginValidation`). I did not open a source for this flag in this run. The alternative is to run the generator's CLI ahead of time and commit the output.

**N12 · GRDB on Linux works but is unofficial. SwiftData is Apple-only.** `opened`, `measured`
- https://github.com/groue/GRDB.swift/blob/v7.11.1/README.md · 2026-10-01 · "**Requirements**: iOS 13.0+ / macOS 10.15+ / tvOS 13.0+ / watchOS 7.0+ • SQLite 3.20.0+ • Swift 6.1+ / Xcode 16.3+" and "**Note**: Linux support is provided by contributors. It is not automatically tested, and not officially maintained." Package.swift defines `SQLITE_DISABLE_SNAPSHOT` for Linux.
- `measured`: GRDB 7.11.1 built and ran against the system SQLite (libsqlite3-dev 3.45.1) on Linux with Swift 6.4 (N9).
- https://developer.apple.com/documentation/swiftdata · 2026-10-01 · iOS 17.0 (ModelContainer iOS 17.0, HistoryDescriptor iOS 18.0).
- Result: GRDB stays the choice (r1 P20/P21). The core exposes a storage protocol. The Linux suite may run the GRDB tests, but the iOS simulator lane is the authority if Linux breaks.

**N13 · The Firebase iOS SDK stays on 12.19.x. 13.0 is staged but has no SPM tag.** `opened`
- `git ls-remote` firebase-ios-sdk · 2026-10-01 · the latest plain tag is `12.19.2` (commit 2026-09-15, "Analytics 12.19.2"); `CocoaPods-13.0.0` has commit 2026-09-24; there is no `13.0.0` tag.
- https://github.com/firebase/firebase-ios-sdk/blob/main/FirebaseCore/CHANGELOG.md · 2026-10-01 · "# Firebase 13.0.0 … Firebase now requires Swift tools version 6.2.1 and the Swift 6.2.3 compiler … no longer resolve in Xcode versions older than 26.2"; "Added support for Swift Package Traits (SE-0450) to allow developers to opt out of unused features"; "A Google Sign-In release compatible with Firebase 13 via Swift Package Manager is not yet available".
- The 12.19.2 CHANGELOG at the tag says Xcode 26.2 "remains the minimum officially supported version". Both CI images (Xcode 26.6 and 27.0) meet it.
- Pin with `.upToNextMinor(from: "12.19.2")` and take only `FirebaseAuth` and `FirebaseAppCheck`. Moving to 13 is a dated task once its SPM tag exists; its traits let us leave out Firestore on the client.

**N14 · GitHub Actions: all three actions are v7 on node24. Pin them by commit SHA.** `opened`
- https://github.com/actions/checkout/blob/v7.0.1/action.yml, setup-python v7.0.0 and upload-artifact v7.0.1 · 2026-10-01 · `using: node24` in all three. The checkout README: "Updated to the node24 runtime — This requires a minimum Actions Runner version of v2.327.1"; "checkout now refuses to check out fork pull request code by default when the workflow is triggered by `pull_request_target` or `workflow_run`"; "`persist-credentials` now stores credentials in a separate file under `$RUNNER_TEMP`".
- The setup-python README: "What's new in V7 — Migrated action internals to ESM … No changes to action inputs, outputs, or behavior."
- https://raw.githubusercontent.com/actions/python-versions/main/versions-manifest.json · 2026-10-01 · newest stable builds 3.14.8, 3.13.16 and 3.12.15; 3.15.0-rc.2 is unstable.
- Tag commits (`git ls-remote`): checkout `3d3c42e5aac5ba805825da76410c181273ba90b1`, setup-python `5fda3b95a4ea91299a34e894583c3862153e4b97`, upload-artifact `043fb46d1a93c77aae656e7c1c64a875d1fc6a0a`. Pinning the SHA, with the tag in a comment, is the supply-chain row's pin for actions.

**N15 · htmx: pin 2.0.11. 4.0 is released but not `latest` until 2027.** `opened`
- `npm view htmx.org dist-tags` · 2026-10-01 · `"latest": "2.0.11"`, `"next": "4.0.0"`. Publish times: 2.0.11 on 2026-09-22, 4.0.0 on 2026-08-28. Git: `master` is at `v2.0.11`, and `v4.0.0` is on the `four` branch.
- https://htmx.org/ · 2026-10-01 · "htmx 4.0 has been released! It is not currently marked as latest in NPM so that people using the 2.x line are not accidentally upgraded. We will mark it latest at some point in 2027."
- https://github.com/bigskysoftware/htmx (README @master) · the quick start gives `htmx.org@2.0.11/dist/htmx.min.js` with `integrity="sha384-2OatzQy1H+Zd/IIrjr1TcuDGqLXeHhbooAyJY1KdQMKnr4LZ22k31GBLdYKHmVjg"`. LICENSE: "Zero-Clause BSD".
- Vendor the file into the repo and check this hash in a test. `option-console.md` already weighs the 4.0 changes.

**N16 · The React/Vite alternative is current. Node 22 here meets Vite 8.** `opened`
- `npm view` · 2026-10-01 · react and react-dom 19.3.0 (2026-09-09), with repository `git+https://github.com/react/react.git`; vite 8.3.1 (2026-09-24), engines `^20.19.0 || >=22.12.0`; @vitejs/plugin-react 6.1.1 (2026-08-28), with the same engines.
- The container has Node v22.22.2 (`node --version`).

**N17 · Playwright drives an RTL page at phone width in this container.** `measured`, `opened`
- `measured`: `python -m playwright install chromium` (1.63.0) downloaded "Chrome Headless Shell 153.0.8010.12 (playwright chromium-headless-shell v1243)". A script launched it, set a 390×844 viewport, loaded `<html dir="rtl" lang="ar">` and printed "pw ok rtl 153.0.8010.12".
- PyPI · 2026-10-01 · playwright 1.63.0 needs Python ≥3.10 (classifiers 3.10–3.14); pytest-playwright 0.9.0 classifiers stop at 3.13.

**N18 · Type checkers.** `opened`, `measured`
- https://github.com/RobertCraigie/pyright-python (README) · 2026-10-01 · "It's highly recommended to install `pyright` with the `nodejs` extra which uses `nodejs-wheel` to download Node.js binaries as it is more reliable than the default `nodeenv` solution". So use `pyright[nodejs]` if pyright is chosen. The npm `pyright` 1.1.414 is the same build.
- `measured`: the installed venv reports `ruff 0.16.9`, `mypy 2.3.1 (compiled: yes)` and `pyright 1.1.414`. mypy is the default because it needs no Node in the Python job; pyright is the alternative.

**N19 · The audit and the SBOM work on the pinned set.** `measured`, `opened`
- `measured`: `pip-audit --path <venv site-packages>` over the full Python 3.13 venv (133 distributions) → "No known vulnerabilities found" (2026-10-01).
- `measured`: `cyclonedx-py environment venv313 --of JSON` wrote a CycloneDX **1.6** SBOM with 133 components. `--spec-version` accepts "1.7, 1.6, 1.5, 1.4, 1.3, 1.2, 1.1, 1.0 (default: 1.6)".
- https://github.com/CycloneDX/cyclonedx-python (README) · 2026-10-01 · "`uv` manifest and lockfile are not explicitly supported. However, uv's Python virtual environments are fully supported." So generate the SBOM from the built environment, not from `uv.lock`.
- https://github.com/pypa/pip-audit (README) · 2026-10-01 · "Support for multiple vulnerability services (PyPI, OSV)".

## Assumptions left open

1. `assumption`: in CI, `xcodebuild` needs `-skipPackagePluginValidation` (or a pre-generated client) to run the OpenAPI build plugin (N11).
2. `assumption`: `app_check.verify_token(..., consume=True)` works from Python against a real Firebase project, even though Firebase's page says the beta supports only Node.js (N7).
3. `assumption`: GRDB keeps building on Linux in later releases, since the author does not test it (N12).
4. `assumption`: Python 3.14 works with firebase-admin. Nothing contradicts it, but the project does not test 3.14 (N1). Not needed while we stay on 3.13.
5. Not visible: open security reports that the maintainers have not published. The GitHub advisory pages were refused by the proxy (see "How to read this file").
