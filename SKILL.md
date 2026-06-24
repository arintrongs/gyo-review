---
name: gyo-review
description: Drive a real browser via the /browser-use skill to visually review and fix web UI — capture screenshots across desktop, tablet, and mobile, reason about the rendered result against UX/UI heuristics, then fix the code and re-verify.
version: 0.0.1
author: arintrongs
tags: [ui, ux, frontend, visual-review, responsive, accessibility, browser-use, qa, gyo, multimodal]
---

# Gyo Review

## Why this skill exists

Your code compiles, your tests pass, your diff looks clean — and your agent has still never *seen* the UI it just shipped. Reasoning from source is blind reasoning: the JSX, the CSS, the Tailwind classes can all be "correct" while the rendered screen is broken. 
- A `flex` row that reads fine in source wraps into garbage at 375px. 
- The primary CTA is "in the layout" and also three hundred pixels below the fold. 
- Contrast passes in your head and fails WCAG. 
- The modal fits — until its content overflows the viewport on a phone. 

**None of it is visible from the code; the defect is live and hidden in the render.**

The only reliable way to know how a UI behaves is to look at the rendered result and reason about what you actually see. This skill does exactly that: it drives a real browser, captures screenshots across viewports, reasons multimodally about the rendered pixels against concrete UX/UI heuristics, then fixes the code and looks again to confirm. The loop is **render → capture → reason → fix → re-verify**, never "read code → assume".

## When to run (and when not to)

Run this skill:
- After building or substantially editing any page, component, or flow.
- When the user reports anything visual or experiential ("looks off", "cramped", "broken on my phone").
- Before declaring any frontend task "done".

You do **not** need this skill for pure logic, backend, data, or non-rendered code. If there is nothing to look at, skip it.

## Prerequisites — confirm before starting

This skill relies on the **`/browser-use` skill** to do the actual browser driving (navigating, screenshotting, inspecting the page). Don't reimplement browser control here — delegate it.

Before starting a review, confirm `/browser-use` is installed. If it isn't, **stop and ask the user to install it first** — do not attempt the review without it. Point them at:
```
npx skills add https://github.com/browser-use/browser-use --skill browser-use
```
Then you also need a URL where the UI is running (a dev server like `http://localhost:3000`, a preview deploy, or a static file):
1. Ask for the URL if you don't have one — do not guess ports.
2. If a dev server isn't running, offer to start it (e.g. `npm run dev`) and wait for it to be reachable before navigating.
3. Do not silently fall back to reviewing code only. If `/browser-use` isn't installed, ask the user to install it rather than degrading to a guess-from-source review.

## The core loop

### 1. Render
Use `/browser-use` to open the target URL. Wait for the page to be fully loaded and idle (network quiet, fonts loaded, any skeleton/loading states resolved) before capturing — screenshotting mid-load produces false findings.

### 2. Capture — responsive by default
**Unless the user names a single viewport, review all three.** Have `/browser-use` set the window/viewport size for each tier, then screenshot:

| Tier    | Width   | Represents                          |
|---------|---------|-------------------------------------|
| Mobile  | 375 px  | iPhone-class, smallest common phone |
| Tablet  | 768 px  | iPad portrait                       |
| Desktop | 1440 px | Common laptop                       |

Also sanity-check one very narrow width (320 px) for the worst case, and one very wide width (1920 px) to catch unconstrained `max-width` problems, when the layout is non-trivial.

For each viewport capture a **full-page** screenshot (to catch overflow and off-screen content); also grab a **viewport-only** shot where seeing what's above the fold matters. Name each capture with its tier and width so your reasoning stays anchored, then actually `view` the images — the point is to look at the rendered result, not assume it.

### 3. Reason
Look at each screenshot and evaluate it against the checklist in `references/ux-ui-checklist.md`. **Read that file before your first reasoning pass** — it is the substantive standard for this skill. Don't pattern-match "it has buttons, looks fine"; go category by category (layout & overflow, hierarchy, spacing, responsiveness, interaction cost, accessibility, content, states, motion). Confirm suspicions by having `/browser-use` run JavaScript in the live page to read the DOM and computed styles: inspect computed styles, the box model, z-index, scroll dimensions (`scrollWidth > clientWidth` ⇒ horizontal overflow), and contrast.

Produce a findings list. For each finding record: **what** (the problem), **where** (selector + viewport), **why it matters** (which heuristic / what it costs the user), and **severity** (blocker / major / minor / polish).

### 4. Fix
Address findings in the code, highest severity first. Make the change in the actual source files, not just live DOM tweaks (live edits are fine for *probing* a fix, but the fix must land in code).

### 5. Re-verify
Reload the page via `/browser-use` and re-capture the affected viewports. Confirm each fix actually resolved its finding and didn't regress another viewport. A fix isn't done until you've *seen* it work at the widths it touched. Repeat the loop until findings are down to acceptable polish-level or the user is satisfied.

## Output format

Report findings as a prioritized table, grouped by severity, then a short summary of what you changed and what you verified. Always state which viewports you actually looked at. Example:

```
## Visual review — Checkout page

### Blockers
- [mobile 375px] "Place order" button is below the fold and the page doesn't scroll past it — users can't complete checkout. (cut off by fixed footer; selector .order-cta)

### Major
- [tablet 768px] Form labels overlap inputs; grid drops to 1 col but gap is 0. (.field-grid)
- [all] Primary and secondary buttons are near-identical weight — no clear primary action (hierarchy).

### Minor / polish
- [desktop] Body copy runs to ~140 chars/line; cap measure ~70ch for readability.

### Fixed & verified
- Reworked footer to be non-fixed on <600px; re-captured 320/375/768 — CTA now reachable.
```

Be honest. If something is genuinely good, say so briefly; don't invent problems. If you couldn't verify something (e.g. a flow requiring login), say so rather than guessing.

## Probing the live page — quick reference

Have `/browser-use` run these as JavaScript in the live page to turn a visual hunch into a confirmed finding:
- **Horizontal overflow / off-screen:** compare `document.documentElement.scrollWidth` to `clientWidth`; find the offending element by scanning for children wider than the viewport.
- **Off-screen / clipped elements:** `getBoundingClientRect()` — flag elements with negative left/top or right/bottom beyond viewport that aren't intentionally hidden.
- **Hierarchy:** inspect computed `font-size`, `font-weight`, color, and size of competing CTAs.
- **Contrast:** pull foreground/background colors and check against WCAG AA (4.5:1 body, 3:1 large text).
- **Spacing rhythm:** read computed margins/paddings; look for inconsistent, non-systematic values.
- **Letter-spacing / tracking:** read computed `letter-spacing` on identifiers, codes, names, and labels — wide tracking that fragments a token (e.g. `Test-HN-2` looking broken apart) is a finding.
- **Sibling consistency:** for items in a set (chips, tabs, pills, cards, rows), compare computed `border-radius`, height, padding, and shape across siblings — an odd one out (a circle among pills, a card taller than the rest) breaks grouping. Also check whether sub-elements like count badges/icons are present consistently.
- **Column grid in repeated rows:** for a list/table of similar rows, measure the left edge (`getBoundingClientRect().left`) of the *same* field across several rows — if a given field starts at a different x in each row, the columns aren't on a fixed grid and the table will look ragged. Variable-width names and status pills are the usual cause.
- **Stacking/overlap:** check `z-index` and `position` when elements overlap.
- **Interaction states:** drive clicks/typing/keyboard via `/browser-use` and capture `:hover`, `:focus`, `:active`, disabled, error, empty, and loading states — these are where UIs most often break and are invisible in a single static screenshot.
- **ALL-CAPS check:** scan for uppercase reading text (labels, paragraphs, menu/table items) both in the markup and via computed `text-transform: uppercase`. All-caps reading text causes eye strain — flag it and convert to sentence case. See the readability section of the checklist for the one narrow exception (short stylistic accents).

## Notes
- Keep the actual heuristic standards in `references/ux-ui-checklist.md` so this file stays focused on the workflow. Read it at the start of every review.
- All browser driving is delegated to the `/browser-use` skill — confirm it's installed before starting, and ask the user to install it if it isn't.
- Prefer fixing root causes (layout system, design tokens, responsive strategy) over per-symptom patches.
- Don't over-capture: three core viewports plus targeted edge widths is enough for most pages. Scale up only for complex, content-heavy, or highly interactive screens.
