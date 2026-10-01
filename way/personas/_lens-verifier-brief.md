# Lens verifier brief — handed whole (2026-10-01)

You verify ONE persona lens of a /way build of Sips & Bytes (repo /home/user/calori). You did not write it. Read `way/blueprint.md` §0–§1 (the profile and the final map), `way/brief/frd-v1.0.md`, `way/personas/_lens-brief.md` (what the lens had to write), the research files it cites (`way/research/`), and the persona file your dispatch names.

Check, and quote the line for each finding:
1. **Traced** — every story traces to the map (a workflow WF-n, an interaction row, a persona) or to an FRD line (FR/AT/NFR); a story that traces to nothing is drift.
2. **Complete** — every step of every workflow this persona touches (map §4, §5 done-when, the FRD FR lines for it) has its stories; list each missing step.
3. **Observable** — every acceptance line names the data and the screen or interface and can be OBSERVED by a verifier in the served product (iOS simulator, API over HTTP, admin console in a browser); every story has at least one runtime line; vague lines ("works well", "is fast") fail.
4. **Sourced** — nothing invented: every research claim has an opened source (link, date, quote) or an `assumption` label; no citation of a finding the refuters (`way/research/r1-refute-a.md`, `r1-refute-b.md`) marked refuted or doubtful.
5. **Vocabulary** — only the map's words for things (§1 ¶4); one name per thing.
6. **Experience** — device, place, the moment, the feeling, the matching style, and the care questions answered as requirements.
7. Ids follow `<persona>-<journey>.<story>` with journey = WF number.

Append at the foot of the persona file a section `## Lens verdict (date)`: `pass` or `fail`, then the defects numbered, each with the story id or missing step and what is wrong. Fix nothing yourself. Never send the owner's identifiers to any outside service. Return one line: pass/fail and the defect count.
