---
name: beautiful-dashboards
description: |
  Use when building a dashboard, analytical report, or data-heavy HTML page where visual
  quality matters — especially if the request mentions "dashboard", "report", "make it
  beautiful", "polished", "less AI-looking", "editorial", "Tufte", "broadsheet", a
  "single-file / offline HTML", interactivity (toggles, knobs, drill-downs, progressive
  disclosure), flowcharts/diagrams, or analyst-style narrative. Self-contained: design
  system (palette, typography, spacing ladders), banned template patterns, chart recipes,
  an interaction layer, hand-built HTML/SVG diagrams, prose patterns, the inline-everything
  offline build technique, and render verification. Two visual registers: editorial
  broadsheet (warm, Tufte) and research desk (institutional navy, numbered exhibits,
  accounting negatives — bank/Goldman-style). No other skills or files required.
version: 3.1.0
---

# Beautiful Dashboards — editorial, self-contained HTML

A dashboard built with this skill is a **printed broadsheet that happens to be interactive**:
an analyst's report with a voice, set in warm paper and ink. Not a SaaS template, not a card
grid, not a "dashboard" in the generic sense. If a screenshot of the output could pass for an
admin-template demo, it has failed regardless of how correct the charts are.

The deliverable is ONE `.html` file that opens offline by double-click: library, data, and
CSS all inlined; every number computed from the embedded data; every view verified rendered.

## Why this frame (and why not your instincts)

This design was validated in a controlled bake-off: identical data and brief, isolated
builds, multiple charting libraries and design-guidance variants, judged by a human.
Unguided builds — by models as capable as you — all converged on the same look: an
"Executive/Analytics Dashboard" title, uniform bordered KPI cards, default library colorway,
tool credits in the footer. Competent, interchangeable, and instantly recognizable as AI
output. All were rejected. The build guided by Tufte's principles, then polished against an
explicit list of "AI tells," was the one selected. This document is that winning design,
compressed.

The uncomfortable implication: **your unguided instinct is the documented failure mode.**
You are not being asked to be creative about the frame. You are being asked to be creative
*inside* it.

## The four commitments

1. **Information honesty (Tufte).** Maximize data-ink; erase ink that isn't data. No
   chartjunk, no decoration posing as information. Honest scales, aligned comparison periods,
   every number computed from the actual data — never invented, never "representative."
2. **Editorial voice.** The page is written, not generated. A masthead instead of a title
   bar, a dek that states the finding, chart titles that name comparisons, subtitles that
   teach the reader how to read the chart. An analyst signed this page (figuratively — never
   literally credit a tool or a model).
3. **Restraint as the aesthetic.** One accent color doing real semantic work, earth-tone
   categoricals, hairline rules, generous whitespace. The page whispers; the data speaks.
4. **Craft in the details.** Tabular numerals everywhere numbers align. Designed
   focus/hover states. A double rule under the masthead. The reader can't name these;
   they're why the page feels human.

## Banned — the AI tells (zero latitude)

Each of these instantly marks the page as template output. Producing any of them means the
polish pass failed:

| Tell | Why it fails |
|---|---|
| "Executive / Analytics / Performance Dashboard" titles | Names the artifact, not the content. A report has a subject. |
| Tool credits ("Built with Plotly", "Tufte-inspired", model names) | Reports don't cite their typewriter. |
| Uniform bordered/rounded/shadowed KPI card grids | The #1 template signature. KPIs become a stat strip (below). |
| Default library colorway, bright red/blue, rainbow categoricals | Unconsidered color = unconsidered page. |
| Gradient hero banners, glassmorphism, decorative blobs | Decoration posing as design. |
| Emoji in headings or labels | Wrong register for an analyst's report. |
| Everything the same size in a symmetric grid | No hierarchy = no point of view. |
| Web fonts / CDN anything | The page must open offline by double-click; system fonts only. |
| Dark mode by default | Warm paper IS the identity. Don't invert it unasked. |
| Library chrome (Plotly mode bar, Vega action menu, watermarks) | Library UI is not report UI. |

## The frame (use exactly — no latitude)

The frame ships **two registers** — same bones (masthead, stat strip, hairlines, ladders,
voice, bans), different temperament. Pick by content, never mix on one page:

- **Editorial broadsheet** (default): warm paper, literary, Tufte-forward. For narrative
  reviews, ops, product analytics.
- **Research desk** (institutional / bank): cool research-white, navy-forward, numbered
  exhibits with source lines, accounting negatives. For finance, markets, econometrics,
  anything a CFO or desk head will read. Goldman-research × editorial blend.

### Register A — Editorial broadsheet: warm paper and ink

```css
:root{
  --ink:#1b1a17; --ink-mid:#5a5650; --ink-light:#8c8579;
  --rule:#d8d2c4; --rule-faint:#ece7db; --bg:#f5f3ec; --bg-warm:#efede5;
  --accent:#28415e;   /* deep slate-blue — primary series / "Actual" / one side */
  --second:#7c3b34;   /* muted oxblood — secondary series / "Budget" / other side */
  --ok:#4d6b5e; --warn:#9a7d3f; --alert:#7c3b34;
  --serif: ui-serif, Georgia, "Iowan Old Style", "Palatino Linotype", "Times New Roman", serif;
  --sans: -apple-system, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
}
body{background:var(--bg); color:var(--ink); font:13px/1.45 var(--sans);}
```

Categorical order: `#28415e` slate → `#7c3b34` oxblood → `#4d6b5e` sage → `#9a7d3f` ochre →
`#5f7355` forest → `#8c7a6b` taupe. Apply it to the **chart traces too** — a tasteful page
wrapped around default-blue charts is still a failure. Semantic colors (`--ok/--warn/--alert`)
are reserved for meaning: never use alert-oxblood decoratively.

### Register B — Research desk: institutional navy on research white

Same variable names, swapped values — everything else in this document applies unchanged:

```css
:root{
  --ink:#14171d; --ink-mid:#494f5d; --ink-light:#878e9e;
  --rule:#c9cdd6; --rule-faint:#e5e8ee; --bg:#fafbfc; --bg-warm:#eef1f5;
  --accent:#153e6e;   /* deep navy — primary / "Actual" */
  --second:#7399c6;   /* desk light blue — comparison / "Budget" */
  --ok:#2e5e4e; --warn:#9a7d3f; --alert:#8c3b3b;  /* burgundy negatives/alerts */
}
```

Categoricals: `#153e6e` navy → `#7399c6` light blue → `#8c3b3b` burgundy → `#9a7d3f` ochre
→ `#494f5d` slate → `#8fa6bd` steel. Burgundy is the *problem* color (the drifting model,
the missed line) — never a neutral category. Two-way comparisons are dark-blue vs
light-blue, the classic desk pairing. Heatmap ramp runs cool-neutral → ochre → burgundy.

Register-B conventions (these are the bank tells, in the good sense):

- **Exhibits.** Every chart, table, and diagram is numbered per view — a navy
  "Exhibit N." prefix on the title — and carries a 9px **source line** beneath
  ("Source: internal ledger extracts; desk calculations."). Number at render time in DOM
  order; never hand-number.
- **Accounting negatives.** In formal tables, negatives are parenthesized — `($652)`,
  `(6.5%)` — set in `--alert`. Signed `+/−` deltas stay only in the KPI strip.
- **Double-rule totals.** The grand-total row closes with a 3px double rule
  (`border-bottom:3px double var(--ink)`) — the accountant's signature, cousin of the
  masthead's double rule.
- **Masthead**: the heavy rule goes navy; the classification mark sets in navy; add one
  **byline** under the dek — 9px uppercase letter-spaced: desk name · distribution ·
  "Data through <date>".
- **Alignment is the aesthetic.** Banks read misalignment as error: every block shares
  the page's left edge; all numerics right-aligned (decimal-aligned where mixed
  precision); tick labels right-aligned against their axis; exhibit titles, sources, and
  chart left edges on one vertical. When in doubt, align to the strictest neighbor.
- The serif masthead, serif KPI numerals, drop caps, and sidenotes all stay — they are
  the editorial half of the blend and what keeps the page from going corporate-sterile.

### Typography: a real pairing, system fonts only

- **Serif** for identity and figures: masthead wordmark, deks, chart titles, big KPI values.
- **Sans** for machinery: labels, axes, table text, tab captions.
- Strong scale contrast is the hierarchy: 24–28px serif values against 9.5px uppercase
  letter-spaced sans labels. If everything is 12–14px the page is dead.
- `font-variant-numeric: tabular-nums` on every element containing numbers that align:
  KPIs, tables, axis ticks, hover labels.

### Masthead: a broadsheet, not a title bar

```html
<header class="masthead">
  <div class="masthead-row">
    <div class="masthead-wordmark">
      <span class="masthead-mark">Confidential</span>
      Operating Review
    </div>
    <span class="masthead-period">FY2026 &middot; through May</span>
  </div>
  <hr class="masthead-rule">      <!-- 2px solid var(--ink) -->
  <hr class="masthead-rule-thin"> <!-- 1px solid var(--ink), 2px below -->
  <p class="masthead-dek">Revenue tracking ahead of plan through May; fraud detection
  model in population drift since early March.</p>
</header>
```

- Wordmark: serif, ~24px, weight 400, named for the **subject** ("Operating Review",
  "Reliability Report", "Growth Review") — never "Dashboard".
- The **double rule** (heavy 2px over thin 1px) is the signature. Keep it.
- The classification mark ("Confidential" / "Internal") is small-caps serif in a hairline
  border, quiet ink-mid — texture, not alarm.
- The **dek** is one italic serif sentence stating the period's findings, **computed from
  the data** — an analyst's lede, not a description of the page.

### KPIs: a de-boxed stat strip (the #1 anti-template move)

No border-per-stat, no radius, no shadow, no fill. A single row bounded by hairlines above
and below, stats divided by faint hairlines, first stat flush with the left margin:

```css
.kpi-strip{display:flex;border-top:1px solid var(--rule);border-bottom:1px solid var(--rule);margin-bottom:32px;}
.kpi-stat{flex:1;padding:16px 20px 14px;border-right:1px solid var(--rule);min-width:0;}
.kpi-stat:last-child{border-right:none;} .kpi-stat:first-child{padding-left:0;}
.kpi-label{font:600 9.5px/1 var(--sans);letter-spacing:.1em;text-transform:uppercase;color:var(--ink-light);margin-bottom:5px;}
.kpi-value{font:400 28px/1 var(--serif);font-variant-numeric:tabular-nums;margin-bottom:5px;}
.kpi-variance{font-size:11px;font-variant-numeric:tabular-nums;}
.kpi-variance.pos{color:var(--ok);} .kpi-variance.neg{color:var(--alert);}
```

Each stat: uppercase label, big serif value, small signed delta with comparison context
("+6.5% vs budget"), colored only by meaning.

### Layout: rules and whitespace, never boxes

- Sections separate by **hairline rule + whitespace**. Charts sit **directly on the paper**:
  transparent backgrounds, no panel borders, no shadows.
- Tabs (if any): uppercase letter-spaced sans, active = ink underline only, visible
  `:focus-visible` outline in `--accent`.
- Tables are first-class citizens: right-aligned numerics under uppercase hairline headers,
  `--bg-warm` section-header and subtotal rows bounded by hairlines, variance colored by
  `--ok`/`--alert`. An income-statement-grade table often carries more credibility than
  another chart.
- Status badges (e.g. "DRIFT"): tiny, restrained — pale tint background (`#f0e6e5`) with
  `--alert` text. Never a filled red pill.
- **One grid, two edges.** Every block shares the page's left AND right content edges:
  no centered blocks, no self-indenting elements, every full-width hairline spans the
  same width. A block that ends mid-page (a narrow centered quote, a notes column
  hanging short of the right margin) reads as a layout error, not as emphasis.

### The ladders (layering discipline)

Three ladders govern every styling decision; if a value isn't on its ladder, pick the
nearest rung instead of inventing one:

- **Color layers** — L0 paper `--bg` (the page) · L1 tint `--bg-warm` (the ONLY area fill:
  drawers, diagram lanes, popovers, table section rows) · L2 hairlines
  `--rule-faint`/`--rule` · L3 ink ladder `--ink-light` → `--ink-mid` → `--ink` ·
  L4 accent + semantic (chart series, `--ok/--warn/--alert`; never decorative).
- **Type scale (px / role)** — 9 footnotes, edge labels · 9.5 uppercase LABELS (.1em
  letter-spacing) · 10 chart subs, sidenotes · 11 controls, tables · 12 deks, annotations ·
  13 body, chart titles · 14 prose · 18 pull-quote · 20 secondary KPI · 24 wordmark ·
  28 KPI. No other sizes.
- **Spacing, base-8** — 4/8/12/16/24/32/48/64. Section gaps 48, block gaps 24, intra-block
  8–12.
- **Interactive state grammar** (uniform everywhere): hover = `--bg-warm` wash or ink
  darkening · active/pinned = 2px ink underline or a 2px left accent bar · focus = 2px
  `--accent` outline, offset 2 · off/disabled = `--ink-light` (+ strikethrough where
  meaningful).

## Your latitude (where judgment is expected)

The frame is fixed; these decisions are yours, and you should make them opinionatedly:

- **Hierarchy.** Pick **one hero per view** — the chart or table that answers the period's
  question — and let it breathe at full width and generous height. Secondary charts are
  visibly secondary (half-width, shorter). A page where everything is the same size has no
  point of view; a page with a hero reads like an argument.
- **Chart selection.** Choose the form the comparison wants: time-series for trajectories,
  paired bars for two-way comparisons (`--accent` vs `--second`), scatter when two measures
  trade off (size by a real third field), heatmap for a matrix of intensity, small multiples
  when comparing many series beats overplotting one axes. Prefer an HTML table over a chart
  whose only job is displaying numbers.
- **The copy.** You write the masthead title, the dek, every chart title and subtitle, the
  annotations. This is where the page gets its voice — see the next section.
- **Annotation.** When the data contains an event (a drift onset, a launch, a reforecast),
  mark it **on the chart** where it happens — a quiet vertical rule and a small direct
  label — not only in prose around it.
- **Adaptation.** New domain, new data shapes, single page instead of tabs: adapt the
  composition freely. The palette, typography, voice, and bans travel unchanged.

## Writing the page (the voice)

Titles are **findings or named comparisons**, not chart-type captions. Match this register:

- Title pattern: *Subject — comparison (scope)*. "Monthly Revenue — Actual vs Budget vs
  Reforecast (FY2026)". "Drift (PSI) Over Time — All Models". Never "Revenue Chart",
  never "Bar Chart of Expenses".
- Subtitles do real work: units, scope, and how to read it. "USD thousands · Actuals
  Jan–May; Budget + RF Q1 projected through Dec". "Dashed line = AUC floor (0.80) ·
  degradation visible from 2026-03-09". One quiet line, sans, `--ink-light`.
- The dek states findings, plural, semicolon-joined, no hedging: "Revenue tracking ahead of
  plan through May; fraud detection model in population drift since early March."
- Units declared once per surface (subtitle or column header), not repeated on every number.

### The prose layer (when the page carries analysis)

When a view warrants narrative, give it an "Analyst's notes" block — serif 14px/1.7, max
width 68ch — and treat it editorially:

- A 3-line **drop cap** opens the first paragraph (serif, ink — no color gimmick).
- **Margin sidenotes** (Tufte): a `minmax(0,68ch) | 220px` grid with
  `justify-content:space-between`, so prose sits on the LEFT page edge and the sidenote
  column pins to the RIGHT page edge — the notes band must share both edges with the
  charts above it. Numbered notes in 10.5px sans `--ink-mid` with a hairline top,
  anchored by superscripts; collapse inline below ~1100px.
- At most one **pull-quote** per page — a single sentence carrying one number, 18px serif
  italic, hairline rule above and below, spanning the FULL content width from the shared
  left edge. Never centered, never `margin:auto`, never narrower than the section rules
  around it (a 760px-centered quote on a 1100px grid was a real, user-rejected bug).
- Paragraphs beyond the first collapse behind a "CONTINUE READING ▾" control.
- A numbered **footnotes** block at the page foot (hairline top, 10px) holds methodology:
  vintage definitions, formulas, the period-alignment statement. Superscripts link to it.
- Every number in prose is computed and set in tabular-nums. Write like an analyst:
  specific, factual, no hedging, no summary blandness.

## Chart craft (the Tufte moves)

- **Erase first.** Transparent backgrounds, no chart borders, faint `--rule-faint` y-grid
  only, no x-grid, outside ticks in `--rule`, zerolines off, no library chrome. What
  remains is data.
- **Data advances, context recedes.** Series lines ~1.6px in categorical ink; thresholds
  and floors are thin **dashed** lines behind the data with a small direct label — never a
  loud red band.
- **Label directly when you can.** A label at a line's end beats a legend; if you need a
  legend, set it horizontal, small, below the plot.
- **Honest geometry.** Bars start at zero. Time axes don't silently skip periods. Don't
  truncate an axis to manufacture drama; if a zoomed domain is analytically right, it must
  be obvious from the ticks.
- **Hovers are designed too**: sans, `--rule` border, tabular numerals, no default chrome.

### Concrete base recipe (Plotly shown; translate the same tokens to any library)

Every chart shares one base — with ECharts / Vega-Lite / Observable Plot, map the identical
values onto that library's config (transparent backgrounds, colorway, axis line/grid colors,
chrome off):

```js
const PAPER='rgba(0,0,0,0)', INK='#1b1a17', GRID='#ece7db', RULE='#d8d2c4';
const SERIF='ui-serif, Georgia, "Iowan Old Style", "Palatino Linotype", "Times New Roman", serif';
const SANS='-apple-system, "Segoe UI", Roboto, Helvetica, Arial, sans-serif';
const CAT=['#28415e','#7c3b34','#4d6b5e','#9a7d3f','#5f7355','#8c7a6b'];
function base(title){return {
  paper_bgcolor:PAPER, plot_bgcolor:PAPER, colorway:CAT,
  font:{family:SANS,size:11,color:INK},
  title:{text:title,font:{family:SERIF,size:14,color:INK},x:0,xanchor:'left',y:0.97},
  margin:{l:52,r:18,t:34,b:38},
  xaxis:{showgrid:false,zeroline:false,linecolor:RULE,ticks:'outside',tickcolor:RULE,tickfont:{size:10}},
  yaxis:{gridcolor:GRID,zeroline:false,linecolor:'rgba(0,0,0,0)',tickfont:{size:10}},
  legend:{orientation:'h',y:-0.2,font:{size:10}},
  hoverlabel:{font:{family:SANS},bordercolor:RULE},
};}
const CFG={displayModeBar:false, responsive:true};
```

Per chart type:
- **Lines**: `mode:'lines'`, width ~1.6; thresholds as thin dotted/dashed lines behind data.
- **Bars**: muted fills from `CAT`; a 2-way comparison is `--accent` vs `--second`.
- **Scatter/bubble**: `marker:{opacity:0.75,line:{width:0}}`, size driven by a real field.
- **Heatmap**: warm ramp `[[0,'#ece7db'],[0.4,'#d8d2c4'],[0.7,'#9a7d3f'],[0.9,'#7c3b34'],[1,'#4a1f1a']]`.
- **Radar/polar** (sparingly, for multi-axis profile comparisons): map the same tokens onto
  the polar axes — grid in GRID, axis lines in RULE, tick font 10, minimal radial tick
  labels; traces as outlines with low-opacity fills (~0.15–0.25 alpha of the categorical
  color). Normalize axes so one direction consistently means "better."
- **Tables**: prefer a real HTML `<table>` with hairline rules + `tabular-nums` over the
  library's table trace.

## The interaction layer (when interactivity is wanted)

Interaction must read as a broadsheet's furniture, not an app. The **marginalia rule**:
every control is a 9.5px uppercase letter-spaced label plus an ink-drawn control built
from hairlines. BANNED, same severity as the AI tells: iOS-style pill toggles, Material
sliders with colored fills, blue buttons, gear/hamburger icons, shadows or rounded chips
on controls. Two more laws: the **default state tells the full story** (all series on,
disclosures closed, thresholds at their published values — interaction deepens the page,
never completes it), and everything is keyboard-operable with correct ARIA and
`prefers-reduced-motion` collapsing animation to instant.

The five proven patterns:

- **Legend-as-control.** Replace the passive legend with a strip of buttons under the
  title: a dash-matched color swatch (a 14px line, not a box) + the series name in 10px
  sans. Click → `Plotly.restyle(gd, {visible: on ? true : 'legendonly'}, [i])`; off state
  = `--ink-light` text + struck swatch. Rebuild direct end-labels after every toggle (see
  Gotchas).
- **Text-pair view switch** (never a toggle pill): two uppercase options separated by a
  1px divider; the active one is ink with a 2px ink underline (`aria-pressed`). Re-plot on
  swap, then re-apply the legend state so trace toggles survive view changes.
- **Threshold knob.** A custom range input — 1px hairline track, square 10×10px ink thumb
  (no radius), serif tabular readout beside it. On input: move the threshold line
  (`Plotly.relayout` on the shape and its label annotation), recount any KPI it governs,
  and re-evaluate status badges. A knob must visibly drive data, or it is decoration.
- **Drill-in drawer.** A KPI stat becomes a `<button>` (hover = `--bg-warm` wash; a small
  › chevron that rotates when open; `aria-expanded`). It opens ONE full-width band
  directly below the strip — `--bg-warm`, hairline top and bottom, no radius or shadow —
  holding a compact breakdown: mini table, per-row sparklines, a small bridge. Animate
  with `grid-template-rows: 0fr → 1fr` (~200ms ease-out); close with a plain ink ×.
- **Progressive disclosure.** Long tables render section headers, subtotals, and the
  largest lines; the rest sits behind a centered hairline "SHOW ALL N LINES ▾" row. Prose
  beyond the first paragraph sits behind "CONTINUE READING ▾". Event markers on charts get
  superscript anchors whose click/hover opens a positioned note (≤320px, `--bg-warm`, 1px
  `--rule` border, 12px serif, computed facts) — dismissed by Esc or an outside click.

## Diagrams (flowcharts in the house style)

When the data is a structure — an architecture, a pipeline, a dependency graph — draw it
yourself in HTML plus one SVG overlay. No Mermaid, no diagram library: their output cannot
meet the frame, and the data already tells you the layout.

- **Nodes**: paper fill, 1px `--rule` border, no radius, a 2px left accent bar in the
  domain's categorical color; serif 12px name over a 9.5px sans metrics line. Nodes are
  `<button>`s, absolutely positioned on a relative stage.
- **Edges**: one `<svg>` underlay; 1px paths in `--ink-light` at ~60% opacity, small solid
  triangle arrowheads via `<marker>`, optional 9px sans edge labels. Compute path
  coordinates from the laid-out node offsets.
- **Layout**: group nodes in labeled swimlanes (9.5px uppercase on a `--bg-warm` band) —
  e.g. gateway → services by domain → data. Rank by dependency depth, distribute within
  lanes. For a "before" architecture, draw the same nodes inside one hairline boundary box
  ("monolith boundary") with their denser edges — let the tangle make the argument.
- **Interaction**: hover/focus a node → its in-edges color `--accent` and out-edges
  `--second` at 1.5px while everything else dims to 25%; click pins the highlight and
  reveals a hairline sidenote with the node's metrics and dependency lists; Esc unpins.
- **Small linear flows** (a scoring pipeline, an ETL): a single row of nodes joined by
  short arrows, status carried by the restrained badge, one quiet caption beneath.

## Numbers discipline

- Every figure on the page is **computed from the embedded data** at render time. A KPI that
  could be wrong if the data changed is a KPI done right; a hardcoded one is a lie waiting.
- **Align comparison periods.** YTD actuals vs a full-year budget is nonsense variance —
  slice both to the same months. The canonical trap: the "latest fiscal year" is usually
  **in progress**, so its KPIs must compare N actual months against the *same N budgeted
  months* (and say "YTD" on the label); route full-year comparisons to a completed year.
  This is the single most common correctness bug in this kind of page — check it explicitly.
- Format like an analyst: thousands separators, explicit signs on deltas (`+6.5%`, `−320`),
  sensible precision (money to 0–1 decimals at scale, rates to 1–2), en-dash ranges,
  middot separators.

## Self-contained single-file build (inline everything)

A charting library (1–5MB) and real data are too big to paste by hand. Author with
placeholders, then splice with a tiny inliner:

1. **Vendor the library locally**, e.g.
   `curl -fsSL https://cdn.jsdelivr.net/npm/plotly.js-dist-min@2.35.2/plotly.min.js -o vendor/plotly.min.js`
2. **Author the HTML with placeholder comments** — each on its OWN line, NOT inside a
   `<script>` tag you write yourself:
   `<!--VENDOR:plotly.min.js-->` and one `<!--DATA:data/foo.json-->` per data file.
   Path convention (the inliner below enforces it): VENDOR takes a **bare filename**
   resolved under `vendor/`; DATA takes a **path relative to the project root**. The
   embedded element id is the file's stem — read data at runtime with
   `const d = JSON.parse(document.getElementById('data-foo').textContent);`
3. **Run the inliner** (save as `inline.py`). It rewrites its target IN PLACE — keep the
   marker source as the editable original and inline a copy
   (`cp src.html out.html && python3 inline.py out.html`), or every later edit means
   stripping megabytes of bundle back out first:

```python
import sys,re,pathlib
ROOT=pathlib.Path('.'); html=pathlib.Path(sys.argv[1]); t=html.read_text()
esc=lambda s:s.replace('</script','<\\/script')
t=re.sub(r'<!--\s*VENDOR:([^>]+?)\s*-->',lambda m:f"<script>\n{esc((ROOT/'vendor'/m.group(1).strip()).read_text())}\n</script>",t)
t=re.sub(r'<!--\s*DATA:([^>]+?)\s*-->',lambda m:(lambda p:f'<script type="application/json" id="data-{p.stem}">{esc(p.read_text())}</script>')(ROOT/m.group(1).strip()),t)
html.write_text(t)
bad=re.findall(r'src="https?://|<link[^>]+https?://|fetch\(\s*["\']https?://',t,re.I)
print('OK self-contained' if not bad else f'WARN external refs: {bad[:3]}')
```

The target is **zero functional external references**: no `<script src=http…>` you wrote,
no `<link>` to the network, no web fonts, no runtime `fetch()` of URLs. Caveat: some
minified libraries (Plotly included) contain inert URL string literals (SVG namespaces,
dead code paths), so the grep above may print `WARN` even when the build is correct —
inspect the matches; if every hit sits inside the vendored bundle's own code rather than
in a loader tag you authored, the build passes. Never "fix" such warnings by editing the
bundle.

## Gotchas (hard-won — ignore these and it breaks)

- **Never wrap a `<!--DATA-->`/`<!--VENDOR-->` marker inside your own `<script>` tag.** That
  yields nested `<script><script>…`; the outer tag's textContent then starts with `<script…`
  → `JSON.parse` throws `Unexpected token '<'`, or the library never executes (`typeof
  Plotly === 'undefined'`). Markers go on their own line; the inliner creates the tags.
- **Never hand-edit a minified vendor bundle** (e.g. to silence a benign URL-string warning
  from the self-containment check). Splitting or altering strings inside the bundle corrupts
  it → blank charts. Dead-code URLs that never load at runtime are acceptable.
- **Hidden-tab zero-width:** a chart created inside a `display:none` container renders at
  0×0 (blank). Create each view's charts on first activation, AND re-trigger the library's
  resize for that view's charts every time its tab becomes visible again (sizes captured
  while hidden go stale), and on `window.resize`.
- **Direct end-labels are layout annotations — they do not follow trace visibility.** If
  series can be toggled (legend-as-control), rebuild the end-label annotations from live
  trace visibility after every `Plotly.restyle`, and de-collide them in pixel space
  (project each final y through the axis range/length, enforce ~12px separation via
  `yshift`). Otherwise hidden traces leave orphan labels, and converging series (e.g.
  cumulative views) print labels on top of each other.
- **`"YYYY-MM"` month strings:** use them directly as a category axis (or parse explicitly).
  Letting the library guess a temporal type can silently drop points → an empty line.
- **Period alignment:** see Numbers discipline — slice comparisons to the same months.
- Never invent numbers; compute every figure from the embedded data.

## Process (in order)

1. **Read the data first.** Compute the period's headline numbers and find the story — the
   beat, the miss, the drift, the trade-off. No story, no dek; no dek, no page.
2. **Sketch the hierarchy** per view: the hero, the supporting pair, the table, the strip.
   Decide what the reader should see in the first five seconds — and which optional layers
   the brief actually warrants (interaction, diagrams, prose). A structure in the data
   wants a diagram; a judgment call wants prose; a threshold wants a knob. Don't add a
   layer without a reason.
3. **Build inside the frame** (tokens above), authoring with inliner placeholders.
4. **Polish pass = hunt the tells.** Reread the banned table against your output line by
   line. Check: serif/sans pairing present? tabular-nums everywhere? first KPI flush left?
   double rule? any box that should be a hairline?
5. **Verify by looking** (next section). Structural checks prove nothing about rendering.

## Verify before declaring done (do not skip)

A file that ends in `</html>` and parses is not a file that renders. Open it headless and
LOOK, capturing console errors:

```bash
# macOS: "/Applications/Google Chrome.app/Contents/MacOS/Google Chrome"
# Linux: google-chrome or chromium
chrome --headless=new --disable-gpu --virtual-time-budget=9000 \
  --window-size=1500,2400 --screenshot=/tmp/dash.png "file://$PWD/out.html"
```

Then view the screenshot — actually look at it. For multi-tab pages, the most robust
trick is to build a tiny **URL-hash router** into the page (`#tab-id` in the URL activates
that tab on load): every view then gets its own screenshot with plain headless Chrome and
no driver script — `file://…/out.html#monitoring` — and tabs become deep-linkable for
readers as a bonus. If a JS driver is available (`puppeteer-core` or Playwright pointed at
the system browser — never download one), additionally click through each tab, capture
`pageerror` events, and assert the charting library loaded (e.g. `typeof
window.Plotly==='object'`) with zero errors. Confirm KPIs are non-zero and every chart
shows data before calling it done. If the page is interactive, screenshot the STATES too —
a drawer open, a threshold knob moved (and its KPI recounted), a series toggled off, a
diagram node pinned, the table expanded — and actually look at each one.

## Self-check before shipping

If any of these is true, stop and fix — the page is not done:

- The title could caption any company's dashboard.
- A KPI sits in its own bordered box.
- Any trace is default-library blue, or two adjacent categories are bright red/green.
- The dek (or any number) was written rather than computed.
- The self-containment check found external references.
- You never looked at a rendered screenshot of every view.
- A tool, library, or model is named anywhere on the page.
- Any control could be mistaken for SaaS chrome (pill toggle, colored slider, blue button).
- The page doesn't tell its full story before the first click (a bad default state).
