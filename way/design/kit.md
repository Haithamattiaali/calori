# The feel kit (model decision A21) — one kit per surface, decided 2026-10-01

The iOS app and the clickable prototype draw only with these tokens; the console uses the same palette as CSS custom properties. Every value with no provably right answer is a starting value, tried on the served product and recorded in the slice's care file (`care.md`: "a value nobody chose is a value nobody cared about"). Source of the style: `personas/eater/research.md` §4 ("large, calm and one-handed at the table; food first, numbers second; true at a glance").

## Colour (light · dark) — every text pair ≥ 4.5:1, checked with the WCAG formula

| token | light | dark | use |
|---|---|---|---|
| `bg` | #FAF7F2 (warm paper) | #121110 | screen background |
| `surface` | #FFFFFF | #1C1B19 | cards, sheets, tiles |
| `ink` | #1D1B18 | #F3EFE8 | primary text, the big remaining number |
| `ink2` | #5C564D | #B8B0A3 | secondary text (kcal beside a food name, captions) |
| `line` | #E7E1D8 | #2E2B27 | hairlines, outlines |
| `accent` | #2F6B4F (sage) | #7CC4A0 | the one primary action, selected tab, focus ring |
| `onAccent` | #FFFFFF | #121110 | text on the accent fill |
| `pending` | #8A5A00 on #FFF4DD | #F2C46B on #3A2C10 | Pending chip and the Pending part of a total |
| `caution` | #9A3412 on #FFF1E8 | #F59E6B on #3A1C10 | a failed limit, an uncertain measurement (never for ordinary eating) |
| `danger` | #B42318 | #F97066 | a failed save or a destructive action's text only |

Over-target days use `ink` with a calm sentence, never `danger` (brief §14.2). Evidence badges are outlined in `line` with an icon and a word — meaning never by colour alone.

## Type — the system families (SF Pro, SF Arabic), Dynamic Type everywhere

| role | style | notes |
|---|---|---|
| Remaining number on Today | Large Title, rounded, semibold, monospaced digits | the first glance |
| Screen title | Title 2, semibold | names the place |
| Food name in a row | Body, semibold | food first |
| kcal beside a food | Subheadline, `ink2`, monospaced digits | numbers second |
| Captions, Evidence words | Footnote | |
| Arabic | the same roles; optical size about +10 % and taller line height (research E41) | paragraphs aligned by their own language |

## Space, shape, size

- 4-pt grid: 4 · 8 · 12 · 16 · 24 · 32.
- Corner radius: cards 16, tiles 14, sheets 20, the primary button a full pill.
- Hit targets ≥ 44 × 44 pt with ≥ 12 pt between bezelled neighbours; the count stepper 56 pt tall; recent-Unit tiles 96 × 96 pt (starting values, tried at the table).
- Thumb band: logging controls in the middle band of the screen; tabs at the bottom; Settings and the Day picker at the top.

## Motion and feedback

- 0.2 s ease-out for sheets and state changes; none on the repeat-log path; Reduce Motion turns movement into fades.
- Pressed state within 100 ms; a light haptic on Log and Undo only.
- No confetti, streaks, sounds or badges for eating.

## Direction (Arabic)

Mirrored layout; back points right; progress, macro bars and week charts fill from the right; digits are never reversed inside a number; mixed Arabic–English rows are direction-isolated so a count stays beside its food.

## Contrast, measured (WCAG 2 relative-luminance formula, 2026-10-01)
Light: ink/bg 16.08 · ink2/bg 6.79 · ink2/surface 7.26 · accent text/bg 5.89 · onAccent/accent 6.29 · pending 5.43 · caution 6.61 · danger/bg 6.15. Dark: ink/bg 16.46 · ink2/bg 8.78 · ink2/surface 8.01 · accent text/bg 9.22 · onAccent/accent 9.22 · pending 8.32 · caution 7.38 · danger/bg 6.77. Every pair ≥ 4.5:1; the focus ring (accent) is 5.89:1 against the background (≥ 3:1 for UI).
