# Lens brief — handed whole to every persona lens (2026-10-01)

You are one lens of a /way build of **Sips & Bytes**, a native iOS nutrition-tracking app with a FastAPI backend and a web admin console. A lens studies ONE persona and writes that persona's journeys, micro stories with acceptance, and experience.

## Read first (paths, in /home/user/calori)
- `way/blueprint.md` — §0 the profile (platform size, C1, English + Arabic RTL, iOS 26+), §1 the final map (operation, personas, interaction table, WF-1…WF-10, vocabulary, done-when, what-else, open questions).
- `way/brief/frd-v1.0.md` — the owner's FRD: the FR-nnn requirements and AT-nn acceptance tests are binding evidence; cite them.
- `way/research/r1-*.md` — research cycle 1 (C, F, P, R findings) and the refutations `r1-refute-a.md`, `r1-refute-b.md`: never cite a finding they mark refuted or doubtful.
- `/tmp/claude-0/-home-user-calori/73daf21c-a910-5eea-9326-d74267c69cbe/scratchpad/way/way/references/care.md` — the care questions (use "The questions" and "By size": platform = every group on every screen).
- `way/lessons.md`.

## What you write, in order
1. **Research the persona (research cycle 2).** The persona's day in the benchmarks: tasks they repeat, the moments that decide their trust, where and on what device they work (at a table, one-handed, in a kitchen, at a desk; sunlight, weak network, Ramadan iftar/suhoor timing for the eater), what they use today and hate. Open every source you cite in this run, quote briefly with link and date; an unopened claim is labelled `assumption`. Never send the owner's identifiers (name, email) to any outside service; use a generic User-Agent.
2. **The journey, high to low.** Goals → one journey per workflow the persona touches → steps → micro user stories: "As <persona>, I <do>, so that <outcome>", each with acceptance as **Given / When / Then naming the data and the screen or interface** (use the map's vocabulary and tab names exactly; Arabic where the story is about Arabic). Nothing stays at "manage X": every verb becomes the stories it takes. Cover the FRD's FR lines for these workflows and reuse its AT-nn fixtures as acceptance where they apply (cite them). Include the unhappy paths: empty, error, offline, permission denied, slow, invalid input, conflict.
3. **Story ids**: `<persona>-<journey>.<story>` (e.g. `eater-3.4`); journey number = the WF number. Add `/m`, `/s` or `/r` to an acceptance line when one story has checks on more than one layer (module, system, runtime). Every story has at least one runtime (`/r`) line a verifier can OBSERVE in the served product (iOS simulator, API over HTTP, or the admin console in a browser).
4. **The experience this persona needs**: device and place of use, the moment that matters, the feeling it must leave, the matching style (e.g. large, calm, one-handed for the eater at the table; dense and fast for the approver at a desk), and the care questions this persona raises (from care.md), answered as requirements.
5. Mark stories shared with another persona with both names. Conflicts with another persona go in a "Conflicts for the model phase" list — never to the owner.

## Rules
- Use only the map's vocabulary (Unit, Composite, Recipe, Food, Alias, Template, Entry, Day, Target, Plan, Analysis, Evidence, Correction, Void, Restore, Pending, Activity, Consent, Policy, Grant; tabs Today · Capture & Plan · My Units · Progress).
- Nothing invented without a source or a labelled assumption. Synthetic examples only.
- Write only the file(s) your dispatch names. Return one line: the path, the number of stories, the number of acceptance lines.
