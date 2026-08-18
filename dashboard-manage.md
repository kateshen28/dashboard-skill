---
name: dashboard-harness
description: Build single-file HTML dashboards and interactive apps whose geometry is computed and verified rather than eyeballed - design tokens, type scale, a component kit of nine SVG chart types plus tabs, cards, popovers, flow diagrams and stepped concept animations, and a pure-Python validator that fails the build on overflow, off-scale values, unreadable contrast, or colliding axis labels. Use this whenever the task involves an HTML dashboard, report page, KPI panel, data table, or hand-written SVG chart, including "make me a dashboard", "turn this into an HTML report", "add a chart to the page", or fixing a layout that overflows - and use it even when the request sounds simple, because the failure modes it prevents are exactly the ones that are invisible until someone else opens the file.
---

# Dashboard Harness

A dashboard is not a landing page. It is read at speed, by someone who will act
on a number, on a monitor you have never seen. Taste guidance alone cannot ship
one, because the failures that matter are geometric: a column that clips a
counterparty name, an axis label half off the plot, a card that grows 3px past
its container. You cannot see those without measuring, and you have no browser.

So this skill splits the work. **You decide; code verifies.** Direction, hierarchy
and meaning are yours. Every width, every fit, every contrast ratio is computed by
`harness/` and gated by `harness/validate.py`. If the validator fails, the
dashboard is not done, regardless of how it looks in your head.

## Engineering principles

Everything below was learned by getting it wrong first. In rough order of how
much time each one saves:

1. **Make layout predictable rather than measurable.** No browser is needed if
   fonts are metric-locked, tables are fixed-layout with integer columns, and
   charts are hand-emitted SVG.
2. **Widths flow, they are never passed.** `row -> plate -> figure -> emitter`,
   each container subtracting its own chrome. A caller that *can* name a width
   will eventually name the wrong one.
3. **A gate that checks each part in isolation will pass the composition.**
   Every seam between two verified components is an unverified place — G4 exists
   because a chart that was internally perfect hung 219px out of its panel.
4. **A prose discipline gets skipped.** If a rule matters, make it a command
   that exits non-zero. This file's own taste checklist was ignored for eight
   builds while the validator ran every time.
5. **Render it and look.** Geometry gates cannot see ugly. Nothing in this repo
   caught a single visual fault; every one arrived from a human until the
   preview tool gave the loop eyes.
6. **One question, one encoding.** Answering everything with a line or a bar is
   the deepest tell of generated work, deeper than any palette.
7. **A device used once is emphasis; used three times it is wallpaper.** Check
   whether a cut corner, a left rule or a hatch is already on the page before
   adding another.
8. **A colour serving two roles serves neither.** A limit line is a reference
   mark, not an alarm. The complaint arrives as "I hate this colour" when the
   real fault is that it is doing two jobs.
9. **Semantic colour and categorical colour are separate budgets.** A red badge
   costs you red.
10. **A plate with no reading has no point of view.** If you cannot write the
    one-line finding, you do not know why the chart is on the page.
11. **It must work for thirty seconds and for twenty minutes.** Not a compromise
    between them — three layers: verdict, readings, annotated charts.
12. **Namespace anything that appears in both CSS and SVG.** A class shared by a
    layout container and a chart mark makes the mark vanish with no error. It
    happened three times here before it became a gate.

## Design principles

The list above is how to build one without it breaking. This is how to decide
what it should be. Every item here arrived by being told the previous version
was wrong.

**Arriving at a direction**

1. **Take the direction from the subject's own world, never from a palette.** A
   risk limit is a tolerance band on an engineering drawing; a ledger is bound
   in cloth and read under a lamp. That is where "plotter" and "counting house"
   came from. A direction chosen from a colour picker looks chosen from a colour
   picker.
2. **Name what you refuse.** A direction is defined by its exclusions more than
   its inclusions. Write down the three or four moves you are not making — and
   check them against the AI-default tells: cream + serif + terracotta,
   near-black + acid accent, a grid of equal rounded cards.
3. **Spend boldness in one place.** One element is loud so nothing else has to
   be. Restraint everywhere else is what makes the loud thing read as expensive
   rather than busy.

**Composition**

4. **Name the focal element before building.** If naming it takes more than a
   second, there isn't one, and the page reads as a parking lot of equal cards.
5. **Declare the row proportion.** `row("1:2", ...)` — every row being 6/6 is the
   clearest sign nobody decided. Vary the ratio and vary the cell kind: charts,
   data, and text plates in the same page.
6. **Density is information per glance, never smaller type.** Get it from more
   modules per row and a tighter frame. The type scale does not move.
7. **Air is a material.** A drawing sitting in space reads as considered; the
   same drawing filling its box reads as output.
8. **A page of charts has no argument, only readings.** At least one text-
   dominant cell per view.

**Encoding**

9. **One question, one shape** — see the encoding table below. Answering
   everything with a line or a bar is the deepest tell of generated work.
10. **The unit is the atom.** When a count is small enough to enumerate,
    enumerate it: one tick per unit, one dot per counterparty. A reader who can
    count is not estimating. (Ceilings in `reference/limits.md` — past them the
    marks merge and the encoding is lying about what it offers.)
11. **Lightness encodes importance; area, length and position encode magnitude.**
    Most important = darkest, or lightest on a dark ground. A shade ramp
    standing in for a quantity is the weakest reading available.
12. **Solid material.** No gradient fills, no glow, no opacity ramps standing in
    for a value. Texture comes from real units — an area made of one hairline
    per day, not from paint. The exception is a chart where opacity *is* the
    encoding.
13. **Label only the extremes, and drop any axis nobody reads.** Direct labels
    beat a legend; a legend beats a key nobody looks at.
14. **Shape can carry sign, so hue does not have to.** Filled versus hollow says
    rose versus fell and costs no colour at all.

**Colour**

15. **Three budgets, kept apart:** one accent, two fixed semantics, and a
    separate categorical set for badges. Reference marks take the accent —
    a limit line is not an alarm.
16. **Match a semantic pair by contrast, not lightness**, and remember the 3:1
    floor is a floor, not a preference. Mute by dropping saturation.

**Motion**

17. **One entrance, index-staggered, transform and opacity only.** Roughly 12ms
    per dot and 100ms per bar; under 700ms total; dead under
    `prefers-reduced-motion`. Reveal on scroll so lower plates animate when they
    are actually seen.
18. **Deterministic jitter, never `random()`.** A chart that looks different on
    reload is a chart nobody trusts.

**The tension worth knowing you are choosing**

Two strategies both work and they do not combine. *Amplitude*: a 92px figure
against 11px annotation, where the type carries the drama — what this build
does, and what G7 rewards. *Restraint*: titles at 15px and everything quiet,
where the drawing and the whitespace carry it — what the editorial reference
skills do. Pick one deliberately. Half of each reads as neither.

## The one idea

Without a browser you cannot measure rendered layout — so make layout
**predictable instead of measurable**. Three moves get you there:

1. **Metric-locked fonts.** Render with `Arial, "Liberation Sans", Helvetica,
   sans-serif` (identical advance widths) or embed a font as a base64
   `@font-face` and generate metrics from that exact file. Then Python computes
   text width to the pixel.
2. **No auto layout for anything that can overflow.** Tables are
   `table-layout: fixed` with integer `<col>` widths that sum exactly to the
   container. Charts are hand-emitted SVG with computed coordinates. Auto layout
   is fine for things that cannot clip — flex rows of cards, stacked sections.
3. **Every literal traces to `:root`.** Including computed geometry: the
   generator writes widths back as `--w-*` tokens, so all geometry is auditable
   in one block instead of scattered through rules.

Anything you cannot predict, you must not use: no `foreignObject`, no
unembedded webfont, no percentage widths mixed with padding, no CSS
`text-overflow` standing in for a truncation decision.

## Workflow

```
brief -> direction -> geometry -> emit -> validate -> LOOK -> taste pass -> fix
```

Never skip to emitting: the geometry step is where the failures get designed
out. And never stop at validate. Steps 5 and 6 were prose sections in an earlier
version of this file and got skipped on eight consecutive builds while every
coded gate ran every time — which is the whole argument for putting them in the
procedure instead.

**1. Direction (yours, 4 lines, before any code).** Who reads this, what decision
they make from it, what leads, what the one accent means. Financial dashboards
have a default look for a reason — dense, neutral, one accent — but "dense and
neutral" is still a choice you must make deliberately, and the numbers themselves
are the design. If a metric card has a hero number, the number is the hero: 34px
bold, tabular figures, everything around it demoted.

**2. Geometry (code).**

```python
from harness.fit import grid, fit_table, Column, plan_y_axis, plan_x_ticks, max_columns

G = grid(page_w=1440, margin=24, gutter=16, cols=12)   # integer columns, exact sums
card_w = G.span(5)                                      # 5 columns incl. gutters
max_columns(card_w - 32)                                # how many table columns FIT
fit = fit_table(cols, rows, card_w - 32)                # widths + truncated cells
pad_l = plan_y_axis(["0","150M","300M","450M"])         # exact axis margin
plan = plan_x_ticks(months, plot_w)                     # stride that cannot collide
```

Read `fit.notes` and act on it. A note saying a column truncates in 60% of rows
is telling you the column is wrong, not that the truncation worked.

**3. Emit.** Single file. `tokens.css` inlined verbatim, then a generated
`:root` block of computed `--w-*` widths, then rules that reference only tokens.
Charts as inline SVG with explicit `font-size` on every `<text>`.

**4. Validate — this is the gate, not a suggestion.**

```
python -m harness.validate out.html          # exit 1 on any FAIL
python -m harness.validate out.html --json   # for hooks / CI
```

Fix and re-run until clean. Do not present a dashboard that has not passed.
Do not "explain" a FAIL to the user instead of fixing it. If a FAIL is genuinely
wrong, fix the validator — the rule is now wrong for everyone, not just here.

**5. Look at it.** `python -m harness.preview out.html --all preview/` rasterises
every chart. A passing gate says the geometry is sound, never that the chart is
good — the first render in this project showed a matrix that passed everything
and was plainly bad. Open the PNGs before presenting anything.

**6. Taste pass.** `python -m harness.validate out.html --taste` gates four of
the ten questions and prints the six it cannot answer. Answer them.

## What is enforced (G1–G7)

| Gate | Fails on |
|---|---|
| G6 collisions | a class used as both a layout container and an SVG mark — the mark inherits the container's animation and opacity and vanishes with no CSS error |
| G5 palette | under `/* @palette mono chromatic-budget=N badge-budget=M */`: badge hues used outside a badge selector; any structural token (ink/surface/rule/series/backdrop) carrying hue, or more distinct hues in the file than the budget (aliases counted once) |
| G1 tokens | raw hex/rgb outside `:root`, off-scale px, `!important`, dangling `var()`, literal `font-family`, inline `style=`, unmeasurable font stack, WCAG contrast under 4.5:1 on any declared pair |
| G2 tables | missing `table-layout:fixed`, missing/short `<colgroup>`, column widths that do not sum to the declared container, any cell whose measured text exceeds its box, unbounded row count with no scroll region, columns under 56px |
| G4 boxes | any fixed-width child (`svg[width]`, `table[data-fit-w]`) wider than the nearest ancestor declaring `data-w`; sibling boxes summing past their parent including declared gaps |
| G3 SVG | missing `viewBox`, any drawn point (incl. stroke width) outside it, any `<text>` without `font-size`, any label crossing the viewBox edge, overlapping x-axis labels, characters absent from the embedded subset (they render as tofu), `<foreignObject>` |

Declare the contract on the element so it can be checked:

```html
<table data-fit-w="775" data-pad="12" data-fs="13" data-header-fs="11" data-max-rows="25">
<div class="plate" data-w="808">          <!-- content width available to children -->
<div class="figure" data-w="808" data-gap-px="16">
```

**Why G4 exists.** G3 proves a chart is internally consistent — nothing escapes
its own viewBox. It says nothing about whether that viewBox fits its container.
An SVG emitted at the plate's width and then dropped into a narrower column
passes every other gate and still hangs 200px out of the panel. That happened
here, in this repo, and `example/broken_box.html` is the fixture that keeps it
from happening again.

**Widths flow, they are not passed.** `row(ratio, *cells)` → `plate(w, …)`
subtracts its own padding and hands the remainder down → `figure(w, plot_fn)`
subtracts the rail and hands the plot the width it *actually* gets. Emitters
receive a width; they never guess one. `figure()` with a rail rejects a
ready-made SVG outright, because passing a pre-sized SVG into a split container
is exactly the mistake the signature exists to prevent. The plate padding CSS is
emitted from the same Python constants the maths uses, so a padding change
cannot silently invalidate every width on the page.

## What the gates cannot see

Design principles 1–18 above are the ones no gate reaches. Four of the ten
taste-pass questions are machine-answerable (readings, scale contrast, device
count, rhythm); the other six — focal point, signature, greyscale, squint, dead
weight, swap test — are yours, and in this project every single one of them was
caught by a person rather than by the tooling.

That ratio is the honest state of the harness: it makes bad geometry impossible
and dull design merely likely.

## Wiring it as a real gate (Claude Code)

Put the validator on a Stop hook so a dashboard cannot be declared finished
while it fails:

```json
{ "hooks": { "Stop": [ { "matcher": "*", "hooks": [ { "type": "command",
  "command": "python -m harness.validate $(git diff --name-only HEAD | grep -E '\\.html$') --json || exit 2" } ] } ] } }
```

Exit 2 blocks the stop and returns the findings as the next thing you read.

## Extending

- **Brand font:** embed it as a base64 `@font-face`, then
  `python -m harness.metrics gen Brand-Regular.ttf brand 400`. Measurement and
  rendering now use the same outlines, and any weight becomes available.
- **New theme:** change values in `tokens.css`, never names. Add a
  `/* @contrast --fg on --bg */` line for every new pair or it goes unverified.
- **New chart type:** emit coordinates from Python and let G3 bound it. If you
  cannot express it as computed coordinates, you cannot verify it — pick a
  different encoding.

## Interactive single-file pages

Static geometry proves the *initial* state. Interaction changes content after
load, so two rules carry the rest:

**Fit against the union, not the initial state.** If a filter can reveal any
subset of rows, run `fit_table` over ALL rows before rendering. Every reachable
filter state is then a subset of a state that already fits, and sorting never
changes widths. This deletes the entire class of "it broke when I clicked
Europe" — no state enumeration, no fixture explosion.

**Measure at runtime with the same table.** `harness/runtime.py` emits the
advance-width metrics plus `textWidth`, `fitText`, `fitCells`, `thinTicks` and
`clampBox` as inline JS — a direct port, verified to agree with Python to the
float. Anything generated after load (hover callouts, injected labels, counters)
goes through it before being placed.

Elements positioned by script cannot be bounded statically. Mark them
`data-runtime="clampBox"`; G3 then skips their bounds and reports the count, so
the exception list stays short and visible instead of becoming a blind spot.
Initialise them at a valid coordinate anyway.

Prefer real interaction over decorative motion: a filter that re-cuts the data,
a sortable column, a readout that follows the cursor. One entrance animation is
plenty — a line drawing itself in once, a gauge filling once. Everything else
transitions in under 200ms on `transform`/`opacity` only, and
`prefers-reduced-motion` turns it off.

## Component kit

`harness/components.py` emits the pieces; `kit.css` styles them. Every emitter
computes its own geometry from measured labels, so composing them cannot
produce an overflow.

**Charts — pick by the question, never by habit.** Answering every question with
a line or a bar is the deepest tell of generated work, deeper than any palette.

| the question | the emitter |
|---|---|
| level against a threshold | `bullet` — not a donut; angle is the least accurate channel |
| trend, and where it is heading | `area_forecast` — solid history, dashed cone |
| discrete period change | `rainfall` — stems; periods do not interpolate |
| two readings per entity | `dumbbell` — the gap is the mark |
| rank movement between periods | `slope` — crossings are the point |
| composition over time | `stacked_area` |
| decomposition of one change | `waterfall` |
| ranking a countable quantity | `tick_rows` — one tick per unit, dot every fifth; the reader tallies rather than estimates |
| two continuous variables | `scatter` with quadrants |
| two categoricals | `heat_dots` — area encodes magnitude (radius by square root), fill carries a second variable, marginal bars give row and column totals free. `heatmap` (shaded grid) only when the matrix is dense enough that dots would collide |
| micro trend inside a row | `sparkline` |
| a process | `node_diagram` — boxes sized to measured labels |
| a concept that needs explaining | `paths_concept` — staged, playable |

`line_area` and `bars` remain for the cases that genuinely are a line or a bar.

**Glass** is allowed and verifiable: `composite(base, tint, alpha)` flattens the
tint over the lightest and darkest backdrop the panel can sit on; emit both as
solid tokens and declare a contrast pragma against each. Blur does not make a
backdrop unknown, it makes it an interval — so gate the interval.

**Surfaces** — `kpi_card`, `stat_strip`, `legend`, `tabs` (full keyboard
contract: arrows, Home, End, roving tabindex), `popover`.

**Layout rhythm** — `row(ratio, *cells)` takes a declared proportion:
`1:1  1:2  2:1  1:3  3:1  2:3  3:2  1:1:1  2:1:1  1:2:1  1:1:2  1:1:1:1  full`.
Every row on a page being 6/6 is the clearest sign nobody decided; naming the
ratio forces the decision and makes the rhythm reviewable by reading the
source. Cells carry `data-i` so entrances cascade left to right without an
inline style. `plate(title, hint, body, kind=)` sets density per cell —
`chart` breathes, `text` sets tighter, `data` tighter still.

**Density modules** — `micro_grid` (label/value/delta grid; four fit where one
KPI card sat), `spec_list` (dense key/value), `note_block` (a text-dominant
cell — a page of charts with no prose has no argument, only readings),
`small_multiples` (the honest answer to a chart with six lines in it),
`heat_strip` (a whole series as one band, fits a rail or a table cell).

Density is information per glance, not shrunken type. Get it from more modules
per row and tighter frames, never from smaller fonts.

**Interaction** — `runtime.app_js()` ships `initTabs`, `initTips`, `initPopover`,
`initCrosshair`, `initSort`, `initConcept`. Everything that positions something
measures it first and clamps to the viewport.

Composition rules that matter more than they look:

- **One depth strategy, committed.** `--lift-1/2/3` are a hairline ring plus
  two soft depths. Never mix ring-only cards with drop-shadow cards on one page
  — that is what makes a layout read as assembled rather than designed.
- **Namespace your animation classes.** `.stage` for the app shell and `.stage`
  for animation stages is a collision that blanks the page, and CSS gives no
  error. Component classes are prefixed for this reason.
- **Mark cells that hold markup `data-nofit`.** `fitCells` writes `textContent`;
  an unmarked cell containing a sparkline loses the sparkline on first re-fit.
- **Series colour comes from `--series-N`.** No component invents a colour, so a
  theme swap moves every chart at once.

## Thirty seconds and thirty minutes

The hardest requirement, and the one everything else serves: the same artefact
has to work for a reader who gives it thirty seconds **and** for a reader who
gives it half an hour. Not a compromise between them — layers, plus routes.

**The four questions.** After the thirty-second read the reader has exactly
four, and each needs a different move:

| question | move | components |
|---|---|---|
| **So what?** | the verdict, in a sentence, with the number in it | `verdict()` |
| **Can I find why?** | HORIZONTAL — peers, segments, the same measure cut another way at the same level | filter chips, `small_multiples`, `dumbbell`, `scatter` |
| **Where did the number come from?** | VERTICAL — aggregate to composition to record | `figure(rail=)`, drill popovers, `lineage()` |
| **Can I trust it?** | DEPTH (z) — method, vintage, known faults, revealed without losing the page | `flip_card()`, `trust_panel()`, `plate(source=)` |

**Navigation has three axes, and a page that only scrolls has one.**
Horizontal moves across peers. Vertical drills into composition. Depth turns a
card over or opens a popover without navigating away. `lead()` is the fourth
move — a route from a finding to the thing you would do about it, which may
cross tabs.

**G8 makes it structural.** Every plate that states a `reading` must offer at
least one route out: provenance, a detail popover, a method face, or a lead. A
claim the reader cannot check is asking to be believed, and a dashboard that
only answers "what" is a scoreboard.

**Trust is a surface, not a footnote.** `trust_panel()` states the checks the
run passed and failed. A dashboard that never admits a failed check has not
been checked.

The reading layers underneath this are unchanged: **L1** the verdict, **L2**
one finding per plate before any chart, **L3** the annotated chart with its
claims. Scanning only L1 and L2 should give the whole story; L3 and the four
routes are what the half hour is for.

## Making it look like something

The harness constrains geometry. It has no opinion about expression and must not
acquire one — a verified dashboard that looks like a 2009 intranet is a failure
with a green tick. Design principles 1–18 are the opinion; this section is the
one mechanical thing that unlocks them.

**Embed the typeface.** The metric-safe Arial stack is the *fallback*, not the
aesthetic — and it is the single biggest reason an early build here looked
generated. Subset a real face, base64 it into an `@font-face`, and generate
metrics from that exact file: measurement becomes more exact than before while
the page stops looking like a default. Character and precision are not in
tension; that was a wrong assumption, corrected by doing it.

    python -m harness.metrics gen Brand-Regular.ttf brand 400

Three faces, three jobs, is enough: a display face used large and sparingly, a
text face for UI, a mono reserved for figures.

**Worked example.** `example/build_app.py` states its direction in the module
docstring before any code — the subject's own world, the encodings chosen per
question, and the divergences taken knowingly. Write that paragraph first; if it
cannot be written, the direction does not exist yet.

## Rendering for review — charts and the page

    python -m harness.preview built.html chart-heatdots out.png
    python -m harness.preview built.html --all preview/
    python -m harness.pageshot built.html shots/        # one PNG per tab

Chart previews close the loop on individual SVGs. Page shots (wkhtmltoimage,
with every `var()` pre-resolved and all entrances forced to their end state)
catch what only exists at page level — an accent saturating a whole view, a
verdict band stacking four registers, a chart printing its encoding artefact
where the reader expects the value. No JavaScript runs in the shot, so blur,
motion, tooltips, popovers and flips still need a real browser; the tool's
docstring lists exactly what it cannot see.

cairosvg rasterises the SVG once the `var(--token)` references are resolved from
the token map. Two things it must get right, both learned by it lying: animation
classes are converted to presentation *attributes* rather than stripped (strip
them and `.hit` loses `fill:none` and paints a black box over the plot), and no
attribute is emitted twice. A preview that renders something other than what the
browser renders is worse than no preview.

## The taste pass

**Workflow step 6, and run as a command, not read as a document.** `python -m harness.validate out.html
--taste` gates the four questions a machine can answer and prints the six it
cannot, so the pass cannot be skipped by the simple method of not reading a file.
That method was used on this very skill for eight consecutive builds — every
geometric gate ran every time, and the checklist ran never. A taste discipline
that lives only in prose has the same failure mode as the taste skills that did
not work for you: it loses to whatever is in front of you.

Gated (G7): **readings** — a chart plate with no one-line finding fails outright.
**Scale contrast** — largest type under 4x the smallest warns. **Device count** —
more than two decorative devices at once warns. **Rhythm** — five or more rows
sharing fewer than three proportions warns.

Printed, for you: focal point, signature, greyscale, squint, dead weight, swap
test.

Ten questions — focal point, scale contrast, device count, signature, rhythm,
colour budget, greyscale, squint, dead weight, swap test. Any "no" is a real
finding, and the file records what each finding produced when it was run against
`example/app.html`.

That file also covers composing with external taste skills: which sections of
`design-taste-frontend` port (brief inference, redesign protocol, hard
pre-flight), which cannot (Tailwind/React/GSAP mandates, CDN fonts, backdrop
blur — the last one breaks the contrast gate outright), and why prose-only taste
guidance did not fix this project on its own.

The division of labour that works: **taste owns expression, the harness owns
geometry, neither argues with the other.** A taste skill that starts mandating a
CDN font breaks metric-locking; a harness that starts having opinions about
palette makes every page look the same.

## The gallery

`example/build_gallery.py` builds the reference companion to counting-house:
every emitter at its correct density, all thirteen row proportions at true
widths, the four post-read questions each answered by a working mechanism, and
the deliberate forks side by side — amplitude/restraint, reference marks in
accent/ink, the dark theme against a light one remapped through the same
tokens, and the three canvas presets computed live from `grid()`.

The back of every encoding plate is the exact call that produced the front,
captured with `inspect.getsource` so it cannot drift. Reuse is flip-and-copy.

## When not to use this

Drop it, do not adapt it, when:

- the target is a **marketing or landing page** — different rules, use a design
  skill instead;
- the page uses a **charting or component library that does its own runtime
  layout** (Recharts, AG Grid, a React design system) — then the browser is the
  harness and you should be running Playwright against it, not this. This skill
  is for hand-emitted single-file HTML, interactive or not;
- it is a **one-off throwaway** nobody else will open — the ceremony costs more
  than the mistake;
- the data is **unbounded and unknown at build time** — this harness assumes you
  can measure the actual strings. If content arrives at runtime, enforce it at
  runtime instead.

`example/build_example.py` is a working reference: real data in, verified
single-file dashboard out. `example/broken.html` is the same file with twelve
deliberate defects — run the validator on it to see every gate fire.
