# UX/UI Review Checklist

The substantive standard for the `gyo-review` skill. These are the concealed defects you concentrate your sight to reveal — the things that are live in the render but invisible to an agent reading only source. Go through every category for each viewport. A finding is anything that violates one of these — record it with severity (blocker / major / minor / polish).

## Table of contents
1. Layout & overflow
2. Visual hierarchy & consistency within a set
3. Spacing, alignment & grouping
4. Responsiveness
5. Interaction cost (clicks, taps, friction)
6. Navigation & wayfinding
7. Accessibility
8. Content & readability (incl. casing & letter-spacing)
9. States (loading, empty, error, success, disabled)
10. Feedback & motion
11. Forms specifically
12. Touch & input ergonomics
13. Performance perception

---

## 1. Layout & overflow
- **No unintended horizontal scroll.** `scrollWidth` must not exceed `clientWidth`. A horizontal scrollbar on a normal page is almost always a bug (an element with a fixed width, an unwrapped flex row, a too-wide image/table, negative margins).
- **Nothing off-screen.** No interactive element pushed beyond the viewport where the user can't reach it. Watch CTAs hidden behind fixed headers/footers, especially on mobile.
- **Nothing clipped.** Text isn't cut off by `overflow: hidden` containers; long words/URLs wrap or ellipsize intentionally, not by accident.
- **No overlap.** Elements don't sit on top of each other unintentionally (check z-index, absolute positioning, negative margins).
- **Containers constrained.** Wide screens shouldn't stretch text to full width — line length should be capped (see readability). Watch for `max-width` that's missing on large viewports.
- **Above the fold.** The most important content/action is visible without scrolling on each tier, or it's obvious that scrolling is expected.

## 2. Visual hierarchy & consistency within a set
- **One clear primary action per view.** The main CTA should be visually dominant (color, size, weight, position). If two buttons have equal weight, hierarchy is broken.
- **Type scale is intentional.** Headings, subheadings, body, captions are distinguishable and consistent. Not five sizes that are all almost the same.
- **Scanning path.** The eye should land on the right thing first (usually title → key info → action). Size, weight, color, and whitespace should guide it.
- **Grouping reflects meaning.** Related items are visually grouped (proximity, shared container, divider); unrelated items are separated. Gestalt: proximity and similarity should match logical structure.
- **Emphasis is rationed.** If everything is bold/colored/boxed, nothing stands out. Color and weight are spent on what matters.
- **Sibling elements share a shape language.** Items that belong to the same set — filter chips, tabs, status pills, cards, list rows, buttons in a group — must share the *same* shape, size, corner radius, padding, and structure. Don't make one chip a solid circle while its siblings are pills, or one card taller than the rest for no reason. *(Example: a filter row where "All" is a solid circle and "Drafts / Awaiting / Completed" are pills — the odd one out reads as a different kind of thing, not as the selected state.)*
- **Selected/active state = same shape, added emphasis.** Communicate "active" by changing fill/weight/color on the *same* shape as its inactive siblings, never by swapping it for a different shape or size. Changing the shape breaks the visual grouping and makes the set look like unrelated controls.
- **Consistent sub-elements across siblings.** If items in a set carry a sub-element (a count badge, an icon, a secondary line), apply it consistently — either all have it or the absence is meaningful and obvious. *(Example: three chips show a count badge and the fourth doesn't, with no clear reason — looks like a bug, not a choice.)*

## 3. Spacing, alignment & grouping
- **Consistent spacing system.** Gaps/margins/paddings follow a scale (e.g. 4/8/12/16/24/32), not arbitrary one-off pixel values.
- **Rhythm.** Vertical spacing between sections is consistent and breathable; content isn't cramped or floating in a void.
- **Alignment.** Edges line up — labels, inputs, cards, columns share alignment lines. Ragged left edges and misaligned grids read as broken.
- **Balanced density.** Not so dense it's overwhelming, not so sparse the user scrolls forever for little content.
- **No orphaned metadata.** Secondary info (timestamps like "updated just now", counts, status text) must be clearly tied to what it describes — grouped with its subject by proximity or alignment, not stranded in dead space between a title on one side and a button on the other. When a header has a title (left), metadata (middle), and an action (right) all on one line, the metadata often ends up floating with no clear owner; bind it to either the title or the action, or give it its own line. *(Example: a section header where "updated just now" sits alone between the heading and a Refresh button, belonging to neither.)*
- **Align to shared edges, not just center-of-mass.** Elements on the same row should sit on a shared baseline and align to consistent left/right edges. A pill or badge next to a heading should align to the heading's baseline, not float at a different vertical position.
- **Columns in repeated rows must hold a fixed grid.** In any list/table of rows that share the same fields (name, ID, status, value), each field must occupy a *fixed-width column* so the same field starts at the same x-position in every row and the user can scan straight down. Don't let each row size its columns to its own content — when one row's name or status is longer than another's, variable-width columns push every following field to a different position, so equivalent values (e.g. `69/187`, `69/175`, `69/174`) no longer line up and the table looks ragged. Fixes: give columns fixed/min widths or a shared grid template (CSS Grid with defined column tracks, or a table layout), truncate-with-ellipsis overlong values rather than letting them stretch the column, and right-align numeric columns so digits line up. *(Example: a patient list where short codes like `Test-HN-2` vs long ones like `DEMO-Q-WAIT-B`, and short vs long status pills, make every column start at a different place row to row.)*
- **Reserve space for variable-width content.** Status pills, badges, and tags vary in label length; give their column a consistent width (or a fixed slot) so a long label in one row doesn't shift the columns of its neighbors. Variable-content elements are the usual cause of ragged repeated rows.

## 4. Responsiveness
- **Reflows, not shrinks.** Layouts should restructure (columns collapse, nav becomes a menu) rather than just scaling down until unreadable.
- **No fixed widths that break small screens.** `width: 600px` on a 375px screen = overflow.
- **Images/media scale.** `max-width: 100%`; aspect ratios preserved; no distortion.
- **Tables on mobile.** Wide tables need a strategy (horizontal scroll container, card view, or column hiding) — they're the #1 mobile overflow source.
- **Tap targets grow / spacing increases** on touch viewports.
- **Test the breakpoints, not just between them.** Check exactly at and just past each breakpoint (e.g. 767/768px) where layouts switch — that's where they break.
- **Edge widths:** 320px (smallest) and very wide (1920px) catch the extremes.

## 5. Interaction cost
- **Minimize clicks/taps to the goal.** Count the steps for the primary task; every extra click is friction. Don't bury common actions behind menus/modals when they could be one tap.
- **Don't make users hunt.** Frequent actions should be immediately visible, not hidden in overflow menus.
- **Avoid dead ends.** Every screen has an obvious next step or way back.
- **Reduce typing.** Sensible defaults, autofill, smart input types, remembered choices.
- **Forgiving.** Destructive actions are confirmable/undoable; mistakes are cheap to recover from.

## 6. Navigation & wayfinding
- **Location is clear.** The user can tell where they are (active nav state, breadcrumbs, page title).
- **Back/exit is always available** — especially out of modals, wizards, and full-screen states. No focus or navigation traps.
- **Consistent nav placement** across pages.
- **Labels are predictable** — links/buttons say what they do; no mystery-meat icons without labels or tooltips.

## 7. Accessibility (this is UX, not a nice-to-have)
- **Contrast** meets WCAG AA: 4.5:1 for normal text, 3:1 for large text and meaningful UI/graphics.
- **Focus visible.** Keyboard focus has a clear visible indicator; tab order is logical; nothing is reachable only by mouse.
- **Hit targets ≥ 44×44px** on touch (24px absolute minimum with spacing).
- **Don't rely on color alone** to convey state (error/success needs icon/text too).
- **Text scales.** Layout survives 200% zoom / larger system font without clipping.
- **Semantics & labels.** Inputs have labels; images have alt; buttons have accessible names; landmarks/headings are structured. (Inspect the DOM, not just the pixels.)
- **Motion respects `prefers-reduced-motion`.**

## 8. Content & readability
- **Line length ~45–75 characters** for body text; cap with `max-width` / `measure`. Full-window-wide paragraphs on desktop are tiring.
- **Line height** comfortable (~1.4–1.6 for body).
- **Font size** ≥ ~16px for body on screen; nothing critical in tiny type.
- **No ALL-CAPS for reading text.** Do not set form labels, field names, paragraphs, list items, table cells, menu items, descriptions, or any text the user actually reads in all uppercase. All-caps removes the word-shape cues (ascenders/descenders) that the eye uses to read, so it scans slower and causes real eye strain — it reads as shouting and hurts comprehension. This is a common agent-generated mistake: flag every instance. Use normal sentence case ("Email address", not "EMAIL ADDRESS"). Uppercase is acceptable *only* as a deliberate stylistic accent on very short, non-reading elements — a single short button, a tiny eyebrow/kicker label above a heading, a logo/wordmark — and even then it should have generous letter-spacing (`letter-spacing` ~0.05–0.1em) and never exceed a couple of words. Check both literal uppercase text in the markup and CSS `text-transform: uppercase`; the latter hides the problem in source, so confirm it visually in the screenshot and in computed styles.
- **Letter-spacing fits the text.** Don't apply wide `letter-spacing` (tracking) to normal reading text, codes, IDs, or names. Over-tracked text breaks a word into disconnected characters, slows reading, and on identifiers with punctuation it makes hyphens/slashes look like separators with their own gaps — so `Test-HN-2` reads as fragmented pieces instead of one token. *(Example: patient/record codes where the hyphens have so much space around them the code looks broken apart.)* Reserve modest positive tracking (~0.05–0.1em) for short uppercase accents only; identifiers and codes should use normal or even slightly tight tracking, ideally a monospace or tabular font so columns of them align.
- **Unlabeled values are ambiguous.** A bare value in its own column (e.g. `69/187`) with no header or adjacent label leaves the user guessing what it means. Give it a column header, an inline label, or an icon+tooltip. Numbers that look like ratios, IDs, or counts especially need a name.
- **Microcopy.** Buttons/labels/empty-states are clear and action-oriented ("Save changes", not "Submit"). Error messages say what's wrong and how to fix it.
- **No lorem ipsum / placeholder leaking** into a "finished" UI.

## 9. States — the most commonly missed
A component that only looks good in its happy, full state is half-built. Check and capture:
- **Loading** — skeletons/spinners; no layout shift when content arrives.
- **Empty** — first-run / no-data states are designed, not blank, and tell the user what to do.
- **Error** — failures are explained and recoverable, near the thing that failed.
- **Success/confirmation** — the user gets clear feedback an action worked.
- **Disabled** — visually distinct and explained (why is it disabled?).
- **Partial/overflowing data** — very long names, huge numbers, many items, one item. Does the layout hold?
- **Hover / focus / active / pressed** — visible and consistent.

## 10. Feedback & motion
- **Immediate feedback** on every interaction (≤100ms perceived); long operations show progress.
- **Optimistic or buffered UI** for slow actions instead of a frozen screen.
- **Transitions aid understanding** (where something came from / went) rather than decorate. Keep them short (~150–300ms) and interruptible.
- **No jank/layout shift** (CLS) as things load or animate.

## 11. Forms
- Labels visible (not placeholder-as-label, which vanishes on input).
- Logical grouping and order; related fields together; single column is usually easier than multi-column.
- Inline validation at the right time (on blur / submit, not aggressively per keystroke), errors adjacent to the field.
- Correct input types/keyboards (email, tel, number, date) — especially on mobile.
- Clear required vs optional.
- Don't clear the form on error; preserve user input.
- Obvious primary submit; secondary/cancel de-emphasized.

## 12. Touch & input ergonomics
- Primary actions reachable in the thumb zone on mobile (bottom/center), not stranded in top corners.
- Adequate spacing between tap targets to prevent mis-taps.
- No hover-only affordances on touch (tooltips, hover menus) without a tap equivalent.
- Swipe/scroll areas don't conflict (e.g. horizontal carousels trapping vertical scroll).

## 13. Performance perception
- First meaningful paint feels fast; above-the-fold renders before everything else.
- Images sized/lazy-loaded; no giant unoptimized assets pushing layout around.
- No flash of unstyled/unfonted content (FOUC/FOUT) that jolts the layout.

---

## Severity guide
- **Blocker** — user cannot complete a core task (off-screen CTA, broken layout, unreadable contrast on key text).
- **Major** — task is possible but painful or confusing (broken hierarchy, overlap, bad mobile reflow, missing error state).
- **Minor** — noticeable rough edge that doesn't block (inconsistent spacing, slightly long line length).
- **Polish** — refinement that elevates quality (motion tuning, micro-alignment, microcopy).

Always tie each finding to the category/heuristic it violates and to the cost it imposes on the user — that's what makes the review actionable rather than opinion.