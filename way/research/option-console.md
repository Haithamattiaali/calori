# Option · How to build the admin console

**Recommendation: build the console as option A: server-rendered Jinja2 + htmx 2.0.11 pages from the same FastAPI app. The console is a second, thin adapter over the same module interfaces that `/v1/admin` calls. It uses cookie sessions with CSRF tokens, native `<table>`/`<dialog>`/`<form>` elements, and a few small plain-JS modules for the offline, idle and keyboard states. There is no build step. Keep a same-origin SPA behind a cookie session (B′) as the fallback if the stories outgrow this.**

Researched and written 2026-10-01 for the /way build of Sips & Bytes. Reads `way/blueprint.md` §0–§1, `way/vocabulary.md` (D2) and the four staff lenses (`way/personas/admin.md`, `approver.md`, `support.md`, `auditor.md`). Edits nothing else.

## How to read this file

- **Labels.**
  - `opened`: I opened the source in this run, and the quote is its text.
  - `measured`: I ran it in this session's container on 2026-10-01, and the number is the output.
  - `assumption`: not proven by a source or a run.
- **Access.** Every source was accessed on **2026-10-01**. A second date is the source's own date: a page's "last updated" line, a release date in a registry, or the last commit that touched an OWASP cheat-sheet file.
- **Hosts.** The fetch tool was refused for htmx.org, so I opened htmx.org, four.htmx.org, OWASP, W3C, MDN, react.dev, vite.dev, Firebase, Playwright, React Aria, the IETF datatracker and the RFC Editor with `curl` through the session proxy. cisa.gov refused the proxy and is not cited.
- **Privacy.** Nothing about the owner was sent to any service. All requests were anonymous GETs to public documentation, registries and git.
- **Story ids** (`admin-10.31`, `approver-10.7`, `support-9.21`, `auditor-10.40` …) point into the lens files.

---

## 1 · What the console has to do: the forces from the stories

The lenses describe a **workflow console**: review queues, state machines and time-boxed access. It is not a CRUD back office. These are the forces that separate the options:

| # | force | stories | what it demands from the UI technology |
|---|---|---|---|
| F1 | **Confirmations with Cancel focused by default**. Exactly five confirmations, each with verb buttons ("Move version 7 to Rollout") | admin-10.26, 10.27, 10.31, 10.32, 10.39 | a modal with its own copy, `autofocus` on Cancel, and Esc to close. The browser's `confirm()` cannot do this |
| F2 | **Pressed state and "Sending…" within 100 ms, kept until the server answers**. Nothing is optimistic | admin-10.36, approver-10.4, support-9.21 | request-in-flight styling and disabling. The UI changes only on the server's answer |
| F3 | **Offline: keep the loaded data, show a quiet bar with the time, disable writes and say why, re-enable on reconnect without a reload.** A kill switch is never queued. The diary is removed at once and "never stored on this computer" | admin-10.1, 10.36; approver-10.4; support-9.21, 10.25; auditor-10.31 | detect online/offline, toggle buttons that need the network, wipe one region, and leave nothing in browser storage |
| F4 | **Sessions.** MFA sign-in and lockout. An idle warning at T−2 min that is announced once, then sign-out with the page emptied. Session expiry re-authenticates in place and keeps a 9-of-14-field form | support-9.1, 10.21; approver-10.3 | a server-enforced idle timeout, a warning banner, and a 401 that leads to a sign-in dialog and then a re-submit of the untouched form |
| F5 | **Concurrency.** 409 `STALE_REVISION` shows who changed what and reloads. Idempotency keys on Approve | admin-10.30; approver-10.4, 10.9 | an expected revision and an idempotency key travel with every write, and the error is rendered in place |
| F6 | **Dense tables.** Sort and filters live in the URL, sticky header and first column, placeholders within 300 ms, 100-row pages that never reorder, "12 new events — show", Cancel for a slow search, stacked cards at 390 px, j/k/o only while the table has focus, "/" focuses search | approver-10.1, 10.6, 10.7; auditor-10.27, 10.30, 10.39, 10.40 | URL-as-state, partial page updates, request abort, and a little keyboard code |
| F7 | **Live states.** A kill-switch banner on every section that disappears within 60 s without a reload. A Grant countdown. Export and evaluation progress with Cancel | support-10.16, 10.24; admin-10.15, 10.32; auditor-10.33 | polling or server-sent events, plus one client-side countdown |
| F8 | **English + Arabic RTL.** Mirrored layout. Ids, times and hashes stay LTR. Names are direction-isolated. A numerals setting. "١٢٠٠" is stored as 1200. Arabic comes first in "What to tell the eater" | approver-10.5; support-9.20; auditor-10.38 | `lang`/`dir` set on first paint, `<bdi>`/`dir="auto"`, locale formatting and digit normalisation |
| F9 | **Roles.** The navigation lists only permitted sections. A direct URL to a forbidden section names the missing permission. The Auditor gets "no … control rendered" | admin-10.2, 10.3; approver-10.2; support-9.16 | permission-aware rendering. The API still enforces every call |
| F10 | **Every console action is audited** with "where (console route and API path)". Refused attempts are audited. The Auditor's own query is written before its results are read | support-9.18; approver-10.2 `/s`; auditor-10.37 | the server must know the console route itself, not take it from the client |
| F11 | **WCAG 2.2 AA.** Visible focus, screen-reader sentences ("Grant grant_31f0, Expired, 3 reads allowed …"), 200 % zoom, 4.5:1 contrast, reduced motion, 44 × 44 px (admin, auditor) and 24 × 24 px (approver) targets | approver-10.7; support-9.19; auditor-10.40; admin-10.36 | semantic markup, live regions for status, focus management after updates |
| F12 | **Same contract.** Each UI line has a sibling API line (`GET /v1/admin/registry` with the admin's token …) | every lens | the console and the API must enforce the same permissions, audit, revisions and idempotency |

The blueprint adds two more constraints:
- the console is "proved in a browser at desktop and ~390 px" (§0, "What the profile switches on");
- D1 (§3) makes supply chain a slice: "dependency audit, pinned versions, SBOM".

---

## 2 · The options

- **A · Server-rendered from the same FastAPI app.**
  - Jinja2 templates under `/console/*`. The lenses already name these routes, for example `/console/review` and `/console/audit-trail` in support-9.16.
  - htmx swaps fragments, plus a small CSS token kit. No build step.
- **B · Separate SPA.** React + Vite + a component kit, calling `/v1/admin` with a **bearer token** (the Firebase web SDK's ID token).
- **B′ · Same-origin SPA behind a cookie session (BFF).**
  - Option B as the current security guidance says to build it (S15, S13).
  - The SPA is served by FastAPI and authenticated by an HttpOnly cookie. It never holds a token.
- **C candidates I considered:**
  - **C1 · An admin generator.** SQLAdmin, starlette-admin or react-admin.
  - **C2 · Content-negotiated single routes.** Each `/v1/admin` handler returns JSON or an HTML fragment depending on `Accept`.

  Both are rejected in §3.8. Neither beats A.

---

## 3 · The argument, criterion by criterion

### 3.1 Fit to the stories

**Most of the console is the shape hypermedia handles well.**
- htmx's own essay lists the good fits as "…If your UI is CRUD-y" and "…If your UI is 'nested', with updates mostly taking place within well-defined blocks" (S4). Review rows, a Registry task panel, the Grant panel and an Audit-trail page are exactly that.
- The essay's "not a good fit" cases are "many, dynamic interdependencies" (a spreadsheet), "UI state … updated extremely frequently", and "full functionality in an offline environment" (S4).
- None of those is in the lenses. The offline stories (F3) ask only for the case the essay calls easy: "it is also easy to detect when a hypermedia application is offline and show an offline message" (S4).

**F1 confirmations** are server-rendered `<dialog>` fragments in A.
- MDN: "The `autofocus` attribute should be added to the element the user is expected to interact with immediately upon opening a modal dialog", and "Modal dialogs can also be closed by pressing the Esc key" (S20). So Cancel-first focus and Esc come from the platform.
- **Do not use `hx-confirm`.** It "allows you to confirm an action using a simple javascript dialog" (S3). That is `window.confirm()`, which cannot carry "Move version 7 to Rollout" as its button or put focus on Cancel.
- In B, React Aria's dialog does the same job.

**F2 pressed state** is native in A.
- "When htmx issues a request, it will put a `htmx-request` class onto an element", and "You can also add the `disabled` attribute to elements for the duration of a request by using the hx-disabled-elt attribute" (S1).
- Both happen synchronously on click, so "Sending…" is pure CSS.
- htmx swaps only on the server's answer, which is what admin-10.36 requires ("confirmation only after the server confirms").
- In B the data-fetching libraries invite optimistic updates. The team would have to forbid them.

**F3 offline** needs a small amount of JS in either option.
- In A it is about 40 lines on `online`/`offline` and htmx's network-error event: "In the event of a connection error, the `htmx:sendError` event will be triggered" (S1). It toggles `[data-needs-network]` buttons and empties `[data-clear-offline]` regions.
- **One trap specific to A:** htmx 2 keeps a history snapshot of the page HTML in `localStorage`. "You may have pages that have sensitive data that you do not want stored in the users `localStorage` cache … setting its value to `false`", and "`htmx.config.historyCacheSize` — can be set to `0` to avoid storing any HTML in the `localStorage` cache" (S1).
  - Support-10.25 ("the diary is never stored on this computer") therefore needs `historyCacheSize: 0`, plus `Cache-Control: no-store` on Diary (read-only) responses.
  - htmx 4 drops the snapshot altogether: "We no longer snapshot the DOM … It also eliminates security concerns regarding keeping history state in accessible storage" (S3).

**F4 sessions** favour A slightly.
- OWASP: "Session timeout management and expiration must be enforced server-side" (S13). A server-side session record is the natural home for the idle clock in A.
- htmx's response-targets extension routes a 401 into a sign-in dialog without touching the form, which then re-submits with its 9 values intact (approver-10.3). The docs load it as `htmx-ext-response-targets@2.0.4` (S1).
- In htmx 4 the same is built in: `hx-status:422="target:#validation-errors"` (S3).
- B needs the same server session plus an HTTP interceptor.

**F5 concurrency** is simpler in A.
- The server renders `expected_revision` and a fresh idempotency key into each form, so a retry of the same rendered form reuses its key by construction.
- In B the client mints UUIDs per mutation and must keep them across retries.

**F6 tables.**
- In A, URL-as-state is the default: a GET filter form with `hx-push-url` puts `?type=label` in the address (approver-10.6, auditor-10.27).
- Slow-search Cancel is a request abort, and "12 new events — show" is a polled count fragment.
- j/k/o (approver-10.7) needs about 30 lines of JS in either option. WCAG 2.1.4 allows single-key shortcuts when they are "Active only on focus" (S19), and the story already scopes them that way.
- B with TanStack Table still has to build the same HTML. TanStack Table is headless.

**F7 live states.** Polling a status fragment every 15 s meets "gone within 60 s without a reload" (support-10.24) in both options. The Grant countdown is client JS in both.

**F8 RTL.**
- In A the server writes `<html lang="ar" dir="rtl">` from the staff member's setting, so the first paint is already mirrored. A B SPA learns the setting after load unless it adds a server step.
- A Jinja macro `name()` emits `<bdi lang="ar" dir="auto">…</bdi>`, which also satisfies WCAG 3.1.2 Language of Parts ("the human language of each passage or phrase … can be programmatically determined", S19). Approver-10.5's `/m` line becomes a pytest of that macro.
- Digits (`measured`, M2):
  - `int('١٢٠٠')` returns `1200` in Python, so the server normalises Arabic-Indic input for free.
  - Babel 2.18 `format_decimal(1234.5, locale='ar_EG', numbering_system='arab')` returns `1٬234٫5`: Arabic separators but **Latin digits**. A needs a one-line `str.translate` filter after Babel.
  - JavaScript's `Intl.NumberFormat('ar-EG')` returns `١٬٢٣٤٫٥` natively.

  This is a small point for B.
- React Aria "supports right-to-left interactions (e.g. keyboard navigation)" and has localized date handling "in many calendar and numbering systems" (S22). That is a real head start for the Auditor's date-range filter with explicit offsets (auditor-10.27–10.29).
- In A the date filter is a native `datetime-local` input plus a zone select, built and tested by hand (`assumption`: about one day of work).

**F9 roles.**
- In A, controls for other roles never leave the server. Admin-10.3's "no Propose, Move to …, Roll back or kill-switch control is rendered" holds by construction.
- In B the bundle carries every role's screens and hides them on the client. That is not a vulnerability, since the API enforces, but it is more surface to reason about.

**F10 audit "where".**
- Support-9.18 asks each event to record "where (console route and API path)".
- In A the console route is the request path the server is handling, so it is authoritative.
- In B the API sees only `/v1/admin/...`. The console route would arrive as a client-supplied header, and a trail the Auditor must "defend" should not rest on that.

### 3.2 Security

**XSS is the first threat for this console.** Eater-supplied strings reach staff screens:
- unmatched names become Alias proposals (approver-10.18);
- Label submissions arrive from eaters (approver-10.15–10.17).

Both A and B escape by default. A needs these settings:
- Starlette's `Jinja2Templates` builds `jinja2.Environment(loader=loader, autoescape=jinja2.select_autoescape())` (S8).
- `select_autoescape` defaults to `enabled_extensions=('html', 'htm', 'xml')` (S7). A fragment saved as `.jinja` or `.j2` would **not** be escaped. Rule: every template is `.html`, or `autoescape=True` is set explicitly, plus a test that renders `<script>` through each macro.
- Jinja's own default is "no automatic escaping" (S7). That is why the explicit setting matters.

**htmx-specific hardening for A** (S1):
- `htmx.config.allowEval = false` disables "event filters, `hx-on:` attributes, `hx-vals` with the `js:` prefix, `hx-headers` with the `js:` prefix". The console needs none of them. This lets the Content-Security-Policy drop `unsafe-eval`.
- `htmx.config.selfRequestsOnly` "defaults to `true`".
- `htmx.config.includeIndicatorStyles` "defaults to `true`". It injects a style element, so set it to `false` and ship the indicator CSS in the token kit to keep `style-src 'self'`.
- Any raw HTML that must be shown sits inside `hx-disable`.
- In htmx 4 the indicator CSS uses constructable stylesheets, "which are not subject to `style-src` CSP restrictions" (S3).

**Session and token storage. This is where B, as described, loses.**
- B's natural path is the Firebase web SDK. Its documented default is "to persist a user's session even after the user closes the browser" (S17), held in browser storage that page scripts can reach.
- OWASP (Session Management Cheat Sheet, last changed 2026-08-13): "Do not store authentication tokens, session IDs, JWTs, refresh tokens, or any credential in `localStorage` or `sessionStorage` … a single XSS vulnerability discloses every token. Use `HttpOnly; Secure; SameSite=Strict` cookies (preferred) or a Backend-for-Frontend (BFF) pattern" (S13).
- RFC 10017, *OAuth 2.0 for Browser-Based Applications*, BCP 212, August 2026 (S15): with a BFF "there are no tokens available to extract from the browser", and "the use of HttpOnly cookies prevents … the escalation from client hijacking to session hijacking".
- So **B with bearer tokens should not be built. B′ is the SPA done right**, and B′ inherits all of A's cookie work: "The BFF MUST implement a proper CSRF defense" (S15 §6.1.3.3).

**CSRF in A (and B′)** follows the OWASP CSRF Prevention Cheat Sheet, last changed 2026-09-11 (S12):
1. **Synchronizer token.** "Stateful software should use the synchronizer token pattern." It is sent as a header, because "it is more secure to insert the CSRF token in a custom HTTP request header via JavaScript than adding a CSRF token in the hidden field". htmx documents exactly this: `<body hx-headers='{"X-CSRF-TOKEN": "…"}'>` (S1).
2. **Fetch Metadata** as a second layer: "reject non-safe methods (POST / PUT / PATCH / DELETE) when `Sec-Fetch-Site: cross-site`", with "a fallback to standard origin verification" (S12).
3. **Cookie.** `__Host-` prefix, `Secure`, `HttpOnly`, `SameSite=Strict`, `Path=/`. OWASP's example is `Set-Cookie: __Host-SessionID=<value>; Secure; HttpOnly; SameSite=Strict; Path=/` (S13). RFC 10017 lists the same flags (S15 §6.1.3.2).
4. **Login CSRF.** "Login CSRF can be mitigated by creating pre-sessions … and including tokens in login form", and the session must be renewed after sign-in (S12). Support-9.1's sign-in needs this.

**Server-side sessions, not Starlette's cookie sessions.**
- Starlette's `SessionMiddleware` "Adds signed cookie-based HTTP sessions. Session information is readable but not modifiable" (S8). The state lives in the browser, so the server cannot end it at the 15-minute idle mark (support-9.1) or revoke it.
- Use an opaque session id that points to a server-side record (identity, roles snapshot, CSRF secret, `last_seen`, absolute expiry).
- If staff sign in with Firebase Auth (TOTP MFA is offered by Identity Platform; S16 lists "Ability to revoke session cookies"), use Firebase's documented server path. "Firebase Auth provides server-side session cookie management for traditional websites that rely on session cookies", with "custom expiration times ranging from 5 minutes to 2 weeks" (S16).
- Firebase calls those cookies "stateless", so the idle clock still needs the server record.

**App Check.**
- It is not a console control in either option. Its only web provider is reCAPTCHA Enterprise (S18), and it attests the app, not the person: "Firebase Authentication provides user authentication, which protects your users, whereas App Check provides attestation of app or device authenticity" (S18).
- The brief already says "App Check is defense in depth, not a substitute for authorization" (§18).
- OWASP warns against leaning on CAPTCHA for request forgery: "Do NOT use CAPTCHA because it is specifically designed to protect against bots" (S12).
- Recommendation: `/v1/admin` and `/console` do **not** require App Check. They require a staff session, MFA and a role.

**Verdict:** A ≈ B′ > B.

### 3.3 Accessibility (WCAG 2.2 AA; W3C Recommendation 12 December 2024, S19)

**A starts from native semantics:** `<table>` with `<th scope>`, `<dialog>`, `<form>` and labels, `<details>`. htmx's guidance is the plain-HTML guidance: "the normal HTML accessibility recommendations apply … Use semantic HTML as much as possible … Ensure focus state is clearly visible … Associate text labels with all form fields" (S3).

**Gaps A must close by hand** (each is about 20–60 lines of JS or CSS, `assumption`):
- **Status messages after a swap** (SC 4.1.3: status messages "can be programmatically determined through role or properties such that they can be presented to the user by assistive technologies without receiving focus", S19). Render every result line into one persistent `role="status"` region with an out-of-band swap. A swapped region is not announced on its own.
- **Focus after a swap and on panel close** (approver-10.7: "Esc closes it and returns focus to that row"). One helper keeps the opener and restores focus to it.
- **Timing** (SC 2.2.1: "warned before time expires and given at least 20 seconds to extend … with a simple action", S19). Support-9.1's two-minute banner with "Stay signed in" already meets this. Test it with Playwright's clock (§3.4).
- **Accessible authentication** (SC 3.3.8 forbids a cognitive test "unless that step provides … a mechanism … to assist", S19). Allow paste and password managers on the password and 6-digit fields in support-9.1.
- **Target size and reflow.** SC 2.5.8 sets a 24 × 24 CSS px minimum (S19), and the admin and auditor stories choose 44 × 44. SC 1.4.10 requires reflow at "320 CSS pixels" (S19), which the 390 px proof covers.
- **Focus.** SC 2.4.7 (focus visible) and SC 2.4.11 (focus "not entirely hidden due to author-created content", S19) both apply, because the sticky header and first column (approver-10.6) and the pinned Grant bar (support-9.19) can hide a focused cell. Add `scroll-padding` in the token kit.

**B with React Aria has a real head start on complex widgets.**
- Its components are "tested across … VoiceOver on macOS in Safari and Chrome, JAWS on Windows …, NVDA on Windows …, VoiceOver on iOS". They manage focus and "provide screen reader announcements", and support RTL keyboard navigation (S22).
- That matters most for the Auditor's date-range picker and the model combobox (admin-10.8).

**Neither option is AA by itself.** Playwright's own docs: "Automated accessibility tests can detect some common accessibility problems … But many accessibility problems can only be discovered through manual testing" (S21). The proof is the same in both: axe in Playwright, ARIA snapshots, and one manual screen-reader pass per release.

**Verdict:** both can meet AA. B′ is ahead on two widgets. A has less ARIA to get wrong.

### 3.4 Testability with Playwright

**The browser proof is the same either way.** Playwright drives whatever HTML the server or the SPA produces, and every needed hook exists in Python:
- `browser_context.set_offline(offline)` for F3;
- `page.clock.install(...)` / `fast_forward("30:00")`, documented for exactly this case: "Inactivity monitoring is a common feature in web applications that logs out users after a period of inactivity … With the help of the clock, you can speed up time" (support-9.1, 10.21);
- `expect(page).to_match_aria_snapshot(...)`, "a YAML representation of the accessibility tree", for the screen-reader sentences (S21);
- route interception for the `/r` fault-injection lines (503, 3 s slow).

**A keeps one language and one harness.** `playwright` 1.63.0 (PyPI, 2026-09-15) and `pytest-playwright` 0.9.0 (2026-08-10) run in the same pytest process that already:
- seeds fixtures;
- calls `/v1/...` with synthetic tokens;
- reads the provider mock's request log.

So an end-to-end line like support-10.23 (request → the eater approves → three reads → expiry → the trail) is one test with shared fixtures.

**Two gaps on the Python side:**
- Playwright's axe page exists only for Node.js; the Python URL returns "Page Not Found" (S21). Python uses the community `axe-playwright-python` 0.1.8 (2026-07-24) or a vendored `axe-core` 4.13.0 (npm 2026-08-05, zero dependencies) injected with `page.add_script_tag` and run with `page.evaluate`.
- Template and macro behaviour (approver-10.5's `dir="auto"` `/m` line) is a millisecond pytest through FastAPI's TestClient. No browser is needed.

**B adds a second stack.** Vitest plus Testing Library for components (jsdom), next to pytest for the API. Its e2e tests can also be Python Playwright, but its component tests cannot.

**Verdict:** A is simpler, with equal browser coverage.

### 3.5 Speed to build for a small team

**A adds no new Python package** when the API uses `fastapi[standard]`. FastAPI 0.142.2 (2026-09-30) already lists `jinja2>=3.1.5` and `python-multipart>=0.0.18` in its `standard` extra (S9). It adds:
- one vendored file, `htmx.min.js` 2.0.11: 52,182 bytes minified, 16,845 bytes gzipped, `dependencies: None` (`measured`, M3);
- one vendored `axe.min.js` for tests;
- the request and response models are the same Pydantic classes the JSON routes use, with no client generation step.

**B costs more before the first screen** (`measured`, M1):
- The official scaffold (`npm create vite@latest -- --template react-ts`, as react.dev documents, S10) installs React 19.2, Vite 8.3, TypeScript 6.0 and oxlint: 70 lockfile entries.
- Adding a realistic console kit grows it to **190 lockfile entries (144 non-optional), 285 MB of `node_modules`**. The kit was: react-router 8.4, TanStack Query 5.104 and Table 9.2, react-aria-components 1.21, i18next and react-i18next, openapi-fetch and openapi-typescript, Vitest 5, Testing Library, jsdom, Playwright and @axe-core/playwright.
- The install **failed on its first try**: "Could not resolve dependency: peer typescript@"^5.x" from openapi-typescript@7.13.0" against the template's `typescript@6.0.3`. It needed `--legacy-peer-deps`. This is the friction a small team pays for every upgrade.

**react.dev itself warns about the from-scratch path** that B takes: "Starting from scratch … does require that you make choices on which tools to use for routing, data fetching, and other common usage patterns. It's a lot like building your own framework", and "You should only choose this option if you are comfortable tackling these problems on your own" (S10). Its recommended alternative, a full-stack React framework, would bring a second server runtime next to FastAPI.

**Estimate** (`assumption`): with the stories as written, A reaches the first proved console slice (sign-in, Registry overview, one confirmation, offline bar) in about 60 % of B′'s effort. Most of the gap is scaffolding, the API client, client routing, client i18n and the second test stack.

**Verdict:** A is faster.

### 3.6 Contract alignment, the point that favours B

**B's one real structural advantage:** its screens call `/v1/admin/...` over HTTP, so every UI action goes through the exact door the stories' API lines test (`GET /v1/admin/registry` with `admin.a`'s token; `POST /v1/admin/foods/{id}/versions/{v}/approve` …).

**A has two doors**, `/console/*` (HTML) and `/v1/admin/*` (JSON). They agree only if both call one layer. A gets alignment by construction with four rules, which should go into the contract and the repo `CLAUDE.md`:
1. **Shared checks live in the module interface, not the HTTP route.** Authorization, the audit event, the idempotency key, the expected revision and the D2 error codes are enforced inside the operation, for example `registry.move_to_rollout(actor, task, version, expected_revision, idempotency_key)`. Both routes are thin adapters.
2. **An import boundary**, an import-linter contract or equivalent: `console/*` may import only `modules.*.interface`, never repositories or the Firestore client.
3. **A parity test.**
   - Every console write route declares the module operation it calls and the `/v1/admin` route that calls the same one.
   - The role-matrix test (admin-10.2: "any admin route without a declared permission fails the build") runs over both route tables.
4. **The audit event records both names.** The console route is the request path. The "API path" is the declared sibling `/v1/admin` path from rule 3. This meets support-9.18 without trusting the client.

With these rules, A and B′ give the same guarantee at the module level. A is better on F10 (§3.1), and B′ is better at "one door with zero discipline".

**C2 was rejected:** one `/v1/admin` handler serving JSON or HTML by `Accept`. It would push screen concerns (form encoding, fragment shapes, in-page error targets) into the API contract the iOS app and the probes depend on. The module-interface seam gives the same guarantee without that.

### 3.7 Maintenance

**htmx is small, has no dependencies and rarely breaks.**
- Majors: 1.0.0 on 2020-11-24, 2.0.0 on 2024-06-17, 4.0.0 on 2026-08-28 (npm, M4). The maintainers keep 1.x "supported in perpetuity" (S2).
- **Which line to pin.** 4.0.0 shipped a month ago, but "It is not currently marked as `latest` in NPM so that people using the 2.x line are not accidentally upgraded. We will mark it `latest` at some point in 2027" (S2).
  - The `four-dev` branch carries **43 commits since the `v4.0.0` tag and no 4.0.1 yet**. Among them is "fix: include submit button name/value when button owns hx-post (#4082)" (S5), a form bug the console would hit.
  - **Pin 2.0.11** (npm `latest`, 2026-09-22) with `historyCacheSize: 0`, `allowEval: false`, `includeIndicatorStyles: false`, `historyRestoreAsHxRequest: false` (the docs say "should always be disabled when using HX-Request header to optionally return partial responses") and `reportValidityOfForms: true` ("you should always enable this option", S1).
  - Write attributes directly on each element. Do not lean on inheritance: 4.0 makes it "explicit by default". Use few events.
  - Move to 4.x after it is marked `latest`, using its checker, which scans `.jinja`, `.jinja2`, `.j2` … files (S3).
- Jinja2 3.1.6 (2025-03-05) is a mature, slow-moving dependency.

**B carries a faster-moving chain.**
- Vite majors: 4.0.0 on 2022-12-09, 5.0.0 on 2023-11-16, 6.0.0 on 2024-11-26, 7.0.0 on 2025-06-24, 8.0.0 on 2026-03-12. That is five majors in 39 months (npm, M4).
- Vite's policy: regular patches only for the current minor (`vite@8.3`), and "All versions before these are no longer supported. Users should upgrade to receive updates" (S11). Majors come "usually every year".
- Vite 8 "requires Node.js version 20.19+, 22.12+" (S11), which adds Node to every CI job of a Python service.
- **D1's supply-chain slice** (dependency audit, pinned versions, SBOM) applies to every ecosystem shipped. B doubles it, with 144 more packages in the second ecosystem.
- The npm ecosystem's recent record is relevant. GitHub, 2025-09-22: "the Shai-Hulud attack, a self-replicating worm that infiltrated the npm ecosystem via compromised maintainer accounts by injecting malicious post-install scripts", which led to "removal of 500+ compromised packages" (S23).

**Reversibility favours A.**
- The stories already require a complete, permissioned `/v1/admin` JSON API.
- If a later delta adds a UI that hypermedia handles badly (spreadsheet-like editing, a drag-and-drop canvas), B′ can be added screen by screen against that API with no backend change.
- Going the other way, from an SPA to server rendering, means rewriting every screen.

**Verdict:** A is cheaper to maintain and easier to change later.

### 3.8 Why not C1, an admin generator

- **SQLAdmin** 0.32.0 (2026-09-20) requires `sqlalchemy>=2.0` (PyPI). This project's store is Firestore behind module interfaces (blueprint §0 line 6).
- **starlette-admin** 1.0.1 (2026-08-25) and **react-admin** 5.15.4 (2026-09-25) are ORM- or REST-CRUD frameworks.
- Generators edit records. The lenses need **state transitions with rules**: Proposed → Shadow → Canary → Rollout with checks and confirmations; Requested → Approved → Active → Expired with an eater's approval; two-approver Policy. They also need audit and refusal at every step.
- A generator either bypasses the module interfaces, which breaks F12, or is overridden screen by screen until it is option A with extra weight.

---

## 4 · Scorecard

| criterion | A · Jinja2 + htmx | B · SPA, bearer token | B′ · SPA + cookie session | C1 · generator |
|---|---|---|---|---|
| fit to the stories (F1–F11) | strong. Native dialog, forms and tables; URL-as-state; server knows role and route. Hand-built: date-range filter, focus restore, live region | strong. Rich widgets; client state for countdowns and offline | as B | weak. CRUD, not state machines |
| security | strong. HttpOnly cookie, synchronizer token + Fetch Metadata, CSP without `unsafe-eval`, autoescape forced | **weak**. Token in JS-readable storage against OWASP (S13) and RFC 10017 (S15) | strong, with the same CSRF duties as A (S15) | depends on its own session model |
| accessibility (WCAG 2.2 AA) | good. Native semantics; live-region and focus helpers to write | good+. React Aria tested with VoiceOver, JAWS and NVDA, RTL keys (S22) | good+ | varies by kit |
| Playwright testability | strong. One Python harness for API, browser and fixtures; axe vendored | good. Two stacks | good. Two stacks | — |
| speed to build | **fastest**. No build, no client generation, no new Python package | slower (M1: 144 packages, a peer-dependency conflict on day one) | slowest. B plus a BFF | fast to start, slow to bend |
| contract alignment | good **with the four rules in §3.6**; best on audit "where" | best by construction (one door) | best by construction | poor. Bypasses modules |
| maintenance | **lowest**. One zero-dependency file; Python only | higher. Vite major about yearly; second SBOM | higher | medium |

---

## 5 · The recommendation, as build rules

1. **Surface.**
   - `/console/*` routes in the FastAPI app, rendered with Jinja2 3.1.6 (`.html` templates, `autoescape` asserted in a test).
   - htmx **2.0.11** vendored and pinned by SHA-384. The docs publish `sha384-2OatzQy1H+Zd/IIrjr1TcuDGqLXeHhbooAyJY1KdQMKnr4LZ22k31GBLdYKHmVjg` for `htmx.min.js` (S1).
   - The configuration in §3.7.
   - No `hx-confirm`, no `hx-on`, no `js:` values, no trigger filters.
2. **One layer.** The console adapter calls only module interfaces, following §3.6 rules 1–4 (enforced, not advised).
3. **Session.**
   - An opaque `__Host-` cookie (`Secure; HttpOnly; SameSite=Strict; Path=/`) pointing to a server-side session record that holds the idle and absolute timeouts, roles snapshot and CSRF secret.
   - Session renewal on sign-in and on any role change.
   - MFA for every staff role.
   - If Firebase Auth holds staff identities, exchange the ID token once for its server session cookie (S16). The browser never keeps a token.
4. **CSRF.** A synchronizer token in `hx-headers` on `<body>` and as a hidden input on plain forms, plus a `Sec-Fetch-Site` check with an Origin fallback, on every non-safe method under `/console` (S12).
5. **CSP.** `default-src 'self'; script-src 'self'; style-src 'self'; connect-src 'self'; frame-ancestors 'none'`, sent as a header. htmx's docs: "HTTP headers are preferred" (S3).
6. **Small plain-JS modules** (ES modules served as static files, no bundler):
   - `offline.js` (F3);
   - `idle.js` (F4, server-driven expiry);
   - `table-keys.js` (j/k/o and "/", focus-scoped);
   - `dialog.js` (showModal on swap, Esc, restore focus);
   - `countdown.js` (Grant bar);
   - `draft.js` (approver-10.3 drafts of reference data only; never personal data, per S14: "avoid storing any sensitive information in local storage").
7. **CSS token kit.** Custom properties for colour (light and dark, 4.5:1 checked), spacing, the 44 px target, focus ring and `--grant-active` (support-9.19). Logical properties (`margin-inline-start`) so `dir="rtl"` mirrors without a second stylesheet. Tabular numerals and a monospace face for ids.
8. **i18n.**
   - `jinja2.ext.i18n` over the shared string catalogue (S7).
   - Babel formatting, then a digit-shaping filter for Arabic-Indic numerals (M2).
   - `<bdi dir="auto" lang="…">` for every name, through one macro.
9. **Proof.**
   - Python Playwright at 1,440 px and 390 px.
   - `set_offline`, `clock`, route faults and ARIA snapshots.
   - axe-core 4.13.0 vendored; one manual screen-reader pass per release.
   - pytest for macros and fragments.
10. **Revisit B′ when any of these holds:**
    - a dated delta adds a UI with "many, dynamic interdependencies" (S4);
    - the hand-built date-range filter fails its accessibility pass twice;
    - the console team becomes a dedicated frontend group.

    Because `/v1/admin` is complete, adding B′ later is additive.

**Risks of A, owned:**
- Hand-built focus management and live regions. Mitigation: the two helpers in rule 6 plus ARIA-snapshot tests on every confirmation and panel.
- htmx 2 history storage. Mitigation: `historyCacheSize: 0`, and the diary served `no-store`.
- Discipline on the module seam. Mitigation: the import rule and the parity test fail the build.
- A later 2 → 4 migration. Mitigation: no inheritance and few events; the upgrade checker.

---

## 5a · Measurements (this session, 2026-10-01)

- **M1** · `npm create vite@latest spa -- --template react-ts` with Node v22.22.2 and npm 10.9.7.
  - The template's `package.json`: `react ^19.2.8`, `vite ^8.3.0`, `typescript ~6.0.2`, `@vitejs/plugin-react ^6.1.1`, `oxlint`.
  - After `npm install`: 70 lockfile entries.
  - Adding the console kit listed in §3.5: 190 lockfile entries, 144 non-optional, 31 runtime and 159 dev; `node_modules` 285 MB; `npm audit` reported 0 vulnerabilities on that day.
  - The first dev install stopped with an `ERESOLVE` peer conflict (`openapi-typescript@7.13.0` wants `typescript@^5.x`; the template has `6.0.3`).
- **M2** · Arabic digits:
  - Python 3.11: `int('١٢٠٠') == 1200` and `int('۱۲۰۰') == 1200`.
  - Babel 2.18.0: `format_decimal(1234.5, locale='ar_EG', numbering_system='arab')` gives `1٬234٫5`.
  - Node 22 `Intl.NumberFormat('ar-EG').format(1234.5)` gives `١٬٢٣٤٫٥`.
  - `str.maketrans('0123456789','٠١٢٣٤٥٦٧٨٩')` fixes the Babel output.
- **M3** · `htmx.min.js` from jsDelivr: 2.0.11 is 52,182 bytes (16,845 gzipped); 4.0.0 is 36,716 bytes (13,032 gzipped). Both have `dependencies: None` in the npm registry.
- **M4** · Registry dates:
  - npm:
    - htmx.org `latest` 2.0.11 (2026-09-22), `next` 4.0.0 (2026-08-28);
    - react 19.3.0 (2026-09-09); vite 8.3.1 (2026-09-24);
    - react-aria-components 1.21.1 (2026-09-04); @playwright/test 1.63.0 (2026-09-04).
  - PyPI:
    - Jinja2 3.1.6 (2025-03-05); fastapi 0.142.2 (2026-09-30); starlette 1.7.0 (2026-09-23);
    - playwright 1.63.0 (2026-09-15); firebase-admin 7.7.0 (2026-09-23).

## 6 · Sources (all opened 2026-10-01; the source's own date in brackets)

- **S1** · htmx 2 documentation, https://htmx.org/docs/ (documents 2.0.11; npm 2026-09-22). Sections: Request Indicators, Requests & Responses, Configuring Response Handling, Security, CSP Options, CSRF Prevention, Configuring htmx, Validation. `opened`
- **S2** · htmx home, https://htmx.org/ (undated page). Quotes: "NEWS: htmx 4.0 has been released! … We will mark it `latest` at some point in 2027"; "htmx 2.x has dropped IE support … the 1.x code-line, which will be supported in perpetuity". `opened`
- **S3** · htmx 4 documentation, https://four.htmx.org/docs (4.0.0; npm 2026-08-28). Sections: Migrating From htmx 2.x to 4.x, User Confirmations, Accessibility, Browser History Support, HTTP Response Code Handling, Security Considerations (CSP, Eval, Inline Styles, CSRF). `opened`
- **S4** · Carson Gross, "When Should You Use Hypermedia?", https://htmx.org/essays/when-to-use-hypermedia/ (2022-10-23). `opened`
- **S5** · htmx git repository, https://github.com/bigskysoftware/htmx: tags `v2.0.11`, `v4.0.0`; `git log v4.0.0..origin/four-dev` gives 43 commits, the last on 2026-09-17. `opened` (git)
- **S6** · npm registry JSON for htmx.org, react, react-dom, vite, @vitejs/plugin-react, react-aria-components, @playwright/test, axe-core, react-admin, @refinedev/core. `opened`
- **S7** · Jinja 3.1.x documentation: Template Designer ("Working with Automatic Escaping"), API (`select_autoescape`), Extensions (`jinja2.ext.i18n`), https://jinja.palletsprojects.com/en/stable/ (Jinja2 3.1.6, PyPI 2025-03-05). `opened`
- **S8** · Starlette source `starlette/templating.py` (main branch) and `docs/middleware.md` (SessionMiddleware: "Adds signed cookie-based HTTP sessions. Session information is readable but not modifiable"), https://github.com/Kludex/starlette (starlette 1.7.0, PyPI 2026-09-23). `opened`
- **S9** · FastAPI, "Templates", https://fastapi.tiangolo.com/advanced/templates/, and the PyPI metadata of fastapi 0.142.2 (2026-09-30; the `standard` extra includes `jinja2>=3.1.5` and `python-multipart>=0.0.18`). `opened`
- **S10** · react.dev, "Creating a React App" and "Build a React App from Scratch", https://react.dev/learn/creating-a-react-app (undated pages; React 19.3.0 on npm 2026-09-09). `opened`
- **S11** · Vite, "Getting Started" (v8.3.1) and "Releases" (supported versions), https://vite.dev/guide/ and https://vite.dev/releases (v8.3.1, 2026-09-24). `opened`
- **S12** · OWASP, Cross-Site Request Forgery Prevention Cheat Sheet, https://cheatsheetseries.owasp.org/cheatsheets/Cross-Site_Request_Forgery_Prevention_Cheat_Sheet.html (last commit 2026-09-11). `opened`
- **S13** · OWASP, Session Management Cheat Sheet, https://cheatsheetseries.owasp.org/cheatsheets/Session_Management_Cheat_Sheet.html (last commit 2026-08-13). `opened`
- **S14** · OWASP, HTML5 Security Cheat Sheet, https://cheatsheetseries.owasp.org/cheatsheets/HTML5_Security_Cheat_Sheet.html (last commit 2026-05-29). `opened`
- **S15** · RFC 10017 / BCP 212, *OAuth 2.0 for Browser-Based Applications*, https://www.rfc-editor.org/rfc/rfc10017.txt (August 2026). Sections 6.1 BFF, 6.1.3.2 Cookie Security, 6.1.3.3 CSRF Protections. `opened`
- **S16** · Firebase, "Manage Session Cookies", https://firebase.google.com/docs/auth/admin/manage-cookies (last updated 2026-10-01 UTC). `opened`
- **S17** · Firebase, "Authentication State Persistence", https://firebase.google.com/docs/auth/web/auth-state-persistence (last updated 2026-10-01 UTC). `opened`
- **S18** · Firebase App Check overview, https://firebase.google.com/docs/app-check (last updated 2026-10-01 UTC). Web provider: reCAPTCHA Enterprise. `opened`
- **S19** · W3C, Web Content Accessibility Guidelines (WCAG) 2.2, https://www.w3.org/TR/WCAG22/ (W3C Recommendation 12 December 2024). SC 1.4.10, 2.1.4, 2.2.1, 2.4.7, 2.4.11, 2.5.8, 3.1.2, 3.3.7, 3.3.8, 4.1.3. `opened`
- **S20** · MDN, `<dialog>`, https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Elements/dialog (last modified 2026-09-02). `opened`
- **S21** · Playwright (Python) docs, https://playwright.dev/python/docs/: "Clock", "Aria snapshots", `BrowserContext.set_offline`. Playwright (Node) "Accessibility testing", https://playwright.dev/docs/accessibility-testing; the Python URL returns "Page Not Found". Release notes head "Version 1.63". `opened`
- **S22** · React Aria, "Quality" (accessibility, supported screen readers, internationalization), https://react-aria.adobe.com/quality (react-aria-components 1.21.1, npm 2026-09-04). `opened`
- **S23** · GitHub Blog, "Our plan for a more secure npm supply chain", https://github.blog/security/supply-chain-security/our-plan-for-a-more-secure-npm-supply-chain/ (2025-09-22). `opened`
- **S24** · PyPI metadata: sqladmin 0.32.0 (2026-09-20, requires `sqlalchemy>=2.0`), starlette-admin 1.0.1 (2026-08-25), axe-playwright-python 0.1.8 (2026-07-24), pytest-playwright 0.9.0 (2026-08-10), Babel 2.18.0 (2026-02-01). `opened`
