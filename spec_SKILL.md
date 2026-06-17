---
name: dashboard-spec
description: |
  Use when generating, editing, saving, sharing, or reproducing dashboards inside an agent
  framework — especially when a dashboard will be edited more than once, reused across
  datasets/desks/periods, or when token cost of regeneration matters. Establishes the
  spec-first discipline: the dashboard IS a JSON spec; HTML is a build artifact. Covers the
  invariants, the spec schema, patch-based editing, templates with data contracts, and the
  freedom tiers. Pair with dashboard-renderer (the engine) and beautiful-dashboards (the
  visual system).
version: 0.1.0
---

# Dashboard Spec — the dashboard is data, not HTML

```
dashboard.html = render(spec, renderer_vN)               # always
spec           = instantiate(template, datasets, params) # optional creation path
```

Compiler analogy: **spec = source, renderer = compiler, HTML = binary, template =
scaffold.** You never patch a binary by hand, and "edit the dashboard" means "edit the
source and recompile" — cheap because compilation is deterministic.

## Why (the economics and the scars)

An agent that emits raw HTML pays for that choice forever: every edit means regenerating
or string-surgering a multi-MB artifact, every reuse means rebuilding from scratch, and
the user's approval ("I like this one") freezes nothing — there is nothing portable to
freeze. Concretely: a one-line title change on a 5MB self-contained HTML costs either a
full regeneration (tens of thousands of tokens) or fragile extraction tooling to strip the
vendored bundle back out before editing. With a spec, the same change is one ~100-token
patch call and a deterministic re-render.

What the user approved lives **in the spec** — theme tokens, layout, panel options,
formats. Freezing their approval = saving the spec (or lifting it to a template). A saved
spec + pinned renderer version is the unit of **save, share, carry, and inject**: paste it
into any session with the same renderer and the identical dashboard reproduces.

## Invariants (the framework enforces these; you obey them)

- **INV-1 — HTML is a build artifact.** Never hand-edited, never parsed as truth, never
  patched. A tool that edits rendered HTML is a bug.
- **INV-2 — One shared renderer, semver'd, releases immutable.** Specs carry
  `spec_version`; saved dashboards and templates pin `min_renderer`. Never fork or mutate
  a released renderer (see dashboard-renderer).
- **INV-3 — Every render embeds its spec**:
  `<script type="application/json" id="__dashboard_spec__">…</script>`. Every artifact is
  self-describing, re-importable, and diffable — even forwarded by email months later.
  This single invariant is what makes a dashboard "carryable": the artifact IS the source.
- **INV-4 — All mutations flow through patch tools** (validate → apply → re-render →
  diff). Wholesale spec writes only at creation. Never re-emit a full spec for a small
  change.
- **INV-5 — Stable IDs.** Panels, tabs, datasets, transforms get immutable IDs at
  creation. Address by ID — never by index, never by title.
- **INV-6 — LLM proposes, code disposes.** Schema + referential validation is
  deterministic code; errors say what's wrong, what's available, how to fix, and "no
  changes applied."
- **INV-7 — Determinism.** Same `(spec, renderer_version)` → byte-identical HTML. No
  timestamps or random IDs unless they're in the spec.
- **INV-8 — Freedom is scoped and logged, not forbidden.** Below-schema mechanisms exist
  (tiers, below) but are fenced by provenance, not prohibition.

## The spec schema (key shapes)

```python
class DashboardSpec(BaseModel):
    spec_version: Literal["0.1"]
    meta: Meta                    # id, title, description, theme: ThemeRef
    datasets: list[Dataset]       # id, name, schema (name/dtype/role), inline rows or query handle
    transforms: list[Transform]   # id, input (dataset|transform id — a DAG),
                                  # ops: typed Arquero verbs (derive/filter/rollup/pivot/join). No JS.
    panels: list[Panel]           # discriminated union on type:
                                  #   kpi   (value ref {data,field,agg}, format, delta, spark)
                                  #   chart (data name + vega_lite fragment; data injected at render)
                                  #   table (columns: field/label/format/align, sort, page_size)
                                  #   text  (markdown)
                                  #   custom_html (srcdoc, non_templateable=True)
    layout: Layout                # tabs -> rows -> cells [{panel: id, span: 1..12}]
```

Design consequences you exploit when editing:

- **Placement is separate from definition** — "move this chart" touches `layout` only.
- **Transforms are data, not code** — "change the calculation" is a one-node patch.
- **Theme is a token set** — `{name: "editorial" | "research" | …, overrides: {…}}`. The
  visual registers from beautiful-dashboards live HERE, as renderer themes — so the
  user's aesthetic approval transfers to every future dashboard by naming the theme,
  not by copying CSS.

## Edit discipline (the skill-level rules)

1. **Edit request** → `dashboard_outline(id)` (ids/types/titles only) → `get_panel` /
   `get_transform` for the one target node → smallest applicable patch tool → report the
   returned diff. Never read the HTML; never re-emit the spec.
2. **Patch format**: JSON Merge Patch for scalars/objects; dedicated tools
   (`add_panel/remove_panel/move_panel`, tab equivalents) for array surgery — index
   arithmetic in patches is a known LLM failure mode, so indices never appear.
3. **New dashboard** → `list_templates` first; if a contract fits, instantiate
   (deterministic, near-zero tokens). Otherwise compose a fresh spec from the panel
   library — this is the ONLY wholesale spec write.
4. **Reads are block-local and deterministic** — never summarize or compress spec content
   into context (it destroys prompt-cache hits and invites drift).
5. **Done-claim gate**: no success claim unless the latest `validate_spec` passed AND the
   latest render succeeded.
6. **Undo exists** (`undo(id, steps)`) because history is an ordered patch log — prefer
   it over compensating edits.

## Templates and data contracts (reuse path)

A template = a spec with holes: dataset **slots** (each with required fields as
role/dtype/nullability), parameter **slots** (title, period, thresholds), plus pinned
`min_renderer`. It is NOT a frozen spec+renderer pair — the renderer stays shared.

Lifecycle: **lift** (`lift_to_template` — you propose which literals become params and
which bindings become slots; the user confirms; `custom_html` blocks lifting) →
**validate** (`validate_against_contract` — pure code, per-slot pass/fail with hints) →
**map** (your only LLM work at instantiation: propose `{user_column → slot_role}`,
gated by the validator) → **instantiate + render** (deterministic).

## Freedom tiers (when the schema can't express it)

Descend one tier at a time, only as far as the request requires, disclose the trade,
and log the gap:

| Tier | Mechanism | Keeps | Gives up |
|---|---|---|---|
| 1 — In-schema (default) | Panel library, free Vega-Lite fragments, theme overrides, sandboxed `custom_html` | Everything | Nothing |
| 2 — Renderer overlay | `meta.renderer={base, overlay_js, overlay_css}` extending the pinned base via the extension API; panels appear as `x-<name>` nodes, still patchable | Surgical edits, determinism, INV-3 | Template lift; golden coverage (`variant: true`) |
| 3 — Freeform | Bespoke HTML with three conventions: data island, region markers, manifest comment; edited via `get_region`/`replace_region`/`patch_data_island` | Total freedom | All spec tooling |

Every tier-2/3 use is a **capability-gap event**: recurring gaps get promoted into the
next shared renderer release, affected dashboards migrate back to tier 1. Freedom is the
R&D lab; the schema grows from observed demand.

## Anti-patterns

- Editing rendered HTML on a spec-backed dashboard (forks spec from artifact — INV-1/3).
- Re-emitting the whole spec to change one field (INV-4; also token waste).
- Addressing panels by index or title (INV-5).
- Letting `custom_html` accumulate unreviewed — its count is a platform health metric.
- Treating "user approved the look" as a reason to freeze renderer code — approval lives
  in the spec; save the spec.

## See also

- **dashboard-renderer** — the engine side: contract, capability registry, versioning,
  golden tests, extension API, promotion pipeline.
- **beautiful-dashboards** — the visual system the themes encode (editorial broadsheet +
  research desk registers, chart craft, interaction grammar).
