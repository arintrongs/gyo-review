# Gyo Review

**Make the agent actually *look* at the UI it shipped — drive a real browser, screenshot across viewports, and fix what the source code can't reveal.**

`/gyo-review` is a skill that turns "read the code and assume it renders fine" into "render it, see it, reason about it, fix it, and look again."


```bash
npx skills add https://github.com/arintrongs/gyo-review
```


<p align="center">
  <img src="assets/sushi-gyo.png" alt="Gyo Review visual identity" width="320">
</p>


---

## Why this exists

Your agent has never actually seen the UI it just shipped. It read the JSX, traced the flex container, and concluded everything lines up — but code that compiles is not code that looks right. 
- A `flex` row that reads fine in source wraps into garbage at 375px. T
- he primary CTA sits three hundred pixels below the fold. 
- The modal fits — until its content overflows on a phone. 

**None of it is visible from the source.**

`/gyo-review` closes that gap by *looking*. It drives a real browser, screenshots the page across mobile, tablet, and desktop, and reasons multimodally about the actual rendered pixels against a substantive UX/UI checklist — then fixes the code and re-captures to confirm. **Render → see → reason → fix → re-verify**, never "read code and assume."

---

## What it does

The skill runs a tight loop instead of guessing from source:

1. **Render** — open the target URL in a real browser and wait for it to be fully loaded and idle (no mid-load screenshots).
2. **Capture** — screenshot at mobile (375px), tablet (768px), and desktop (1440px) by default, plus narrow/wide edge cases for non-trivial layouts. Full-page shots catch overflow and below-the-fold content; the images are *actually viewed*, not assumed.
3. **Reason** — evaluate each screenshot multimodally against a substantive UX/UI checklist (layout & overflow, hierarchy, spacing, responsiveness, interaction cost, accessibility, content, states, motion), and confirm hunches by reading the live DOM and computed styles.
4. **Fix** — land fixes in the actual source files, highest severity first.
5. **Re-verify** — reload, re-capture the affected viewports, and confirm each fix worked without regressing another width.

The output is a prioritized findings table (blocker / major / minor / polish) plus a summary of what changed and which viewports were actually inspected.

## Usage

Once installed, invoke it directly or just describe a visual task and let Claude reach for it.

Explicit:

```
/gyo-review review http://localhost:3000/checkout
```

It also triggers naturally on prompts like:

- "Review the UI of the checkout page."
- "Does this look good on mobile?"
- "Check the responsiveness of this component."
- "Fix the layout — something feels off."
- "Improve the UX of this flow."
- "QA this page before I ship it."

…and proactively after building or editing frontend code, because code that compiles isn't code that looks and feels right.


## Credits & license

- **Gyo** (凝) is a concept from **Hunter × Hunter**, created by **Yoshihiro Togashi** — borrowed here only as a name, with no rights claimed. See below for why.
- Author: **arintrongs**
- License: **[MIT](LICENSE)**

---

## Why "gyo"?

In *Hunter × Hunter*, **Gyo** (凝) is the technique of concentrating your focus into your eyes to see what's hidden in plain sight — traps and details an ordinary glance misses. That's the whole idea here: concentrate the agent's attention on the rendered pixels to reveal the defects the source code hides.



