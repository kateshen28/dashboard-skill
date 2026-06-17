---
name: dashboard-renderer
description: |
  Use when building, extending, versioning, or debugging the shared dashboard render
  engine — the single `render(spec) -> self-contained HTML` used by every spec-backed
  dashboard. Covers the renderer contract, capability registry, semver + golden-test
  release gate, theme/register implementation, the overlay extension API, and the
  promotion pipeline that grows the schema from observed demand. Pair with dashboard-spec
  (authoring/editing discipline) and beautiful-dashboards (the visual system the themes
  implement).
version: 0.1.0
---

# Dashboard Renderer — one engine, many dashboards

The renderer is the **compiler**: one shared, versioned engine that turns *any* valid spec
into one self-contained `.html`. It is owned like a product, released like a library, and
never edited in place. Dashboards change constantly; the renderer changes rarely — that
asymmetry is the whole design.

The renderer is reused *especially when* the spec changes — spec edits are surgical
precisely because the renderer stays fixed across them. "The user likes how it looks"
never freezes renderer code; it freezes a spec (and at most pins `min_renderer`).

## Contract

- **Signature:** `render(spec_json) -> dashboard.html`. ONE file, zero network at
  runtime: vendored chart runtime (e.g. Vega-Lite + vega + vega-embed, or Plotly) +
  vendored transform runtime (Arquero) + a small native runtime for tabs / grid / KPI
  strips / tables / formatting — inlined via the inline-everything technique (vendor
  markers + data islands; see beautiful-dashboards for the build mechanics and its
  gotchas: nested script markers, never editing minified bundles, hidden-tab zero-width).
- **Embeds the spec** (INV-3): `<script type="application/json"
  id="__dashboard_spec__">…</script>` in every artifact. This is also the import path for
  any dashboard that arrives from outside (email, another workspace): read the island,
  re-render locally.
- **Capability registry:** ship `capabilities.json` — supported `spec_version` range,
  panel types, transform verbs, themes. `validate_spec` checks against it BEFORE render,
  so "renderer can't do X" is a structured pre-render error listing what IS supported —
  never a blank panel.
- **Determinism (INV-7):** same `(spec, renderer_version)` → byte-identical HTML. Stable
  key ordering, pinned vendored libs, no timestamps, no random IDs. Determinism is what
  makes re-render free and diffs meaningful.

## Themes are the visual system

Themes implement the registers from beautiful-dashboards as token sets the spec selects
by name:

- `editorial` — warm paper, serif masthead, earth categoricals (the broadsheet register).
- `research` — research-white, navy-forward, numbered exhibits + source lines, accounting
  negatives, double-rule totals (the institutional register).

Register conventions that involve *behavior* (exhibit auto-numbering in DOM order, source
lines per block, parenthesized negatives in formal tables) are **renderer capabilities
switched by theme**, not per-dashboard code. A user's aesthetic approval becomes portable
the moment it's a named theme: every future dashboard inherits it by reference, and a
theme improvement ships to the whole population through a release — never by editing
artifacts.

## Versioning and the release gate

- **Semver.** Spec migrations are explicit scripts (`migrate_0_1__0_2.py`), never silent
  coercion. Saved dashboards and templates pin `min_renderer`.
- **Released versions are immutable** (INV-2). In-place mutation of a released renderer
  silently changes every saved dashboard on its next re-render — the one unforgivable
  failure. Per-dashboard needs are overlays on a pinned base, never edits to the base.
- **Golden tests are the release gate:** a corpus of specs (one per panel type, per
  theme, plus the gnarliest real dashboards) → render → hash-compare the HTML. A change
  that alters output for an existing spec either bumps major or is a reviewed bug fix
  with regenerated goldens. Add headless screenshot diffs as visual smoke — render each
  golden, screenshot per tab (URL-hash router), compare. Reuse the verify protocol from
  beautiful-dashboards; goldens are that protocol, institutionalized.
- A renderer release without new golden coverage for its new capability is not a release.

## Extension API (tier-2 overlays)

The escape hatch that keeps freedom from forking the engine:

```python
class RendererRef(BaseModel):       # optional on spec.meta
    base: str                       # pinned shared version, e.g. "1.4.0"
    overlay_js: str | None = None   # extends base via the extension API only
    overlay_css: str | None = None  # applied after theme tokens
```

- Expose a deliberately small API: `registerPanelType(name, renderFn)`, lifecycle hooks,
  read access to theme tokens and resolved data tables. **No monkey-patching internals**
  — lint-checkable.
- Overlay panels appear in specs as `{type: "x-<name>"}` nodes → still addressable by
  every standard patch tool; determinism holds (`spec incl. overlay + base_version` →
  byte-identical); INV-3 holds because the overlay travels inside the spec.
- Overlay code runs unsandboxed in the page (unlike `custom_html` iframes) → requires
  user confirmation, flags the dashboard `variant: true`, and excludes it from template
  lift and golden corpora until promoted.

## The promotion pipeline (how the schema grows)

1. **Log** every overlay panel type, `custom_html` use, and freeform build as a
   capability-gap event: what was needed, which tier served it.
2. **Review** periodically; rank gaps by recurrence. Open-gap count is the platform
   health metric — rising `custom_html` count means the schema is missing something real.
3. **Promote** recurring patterns into renderer vN+1: typed schema node, golden tests,
   capability-registry entry.
4. **Migrate back**: mechanical rewrite (`x-waterfall` → first-class `waterfall`), drop
   the overlay, clear `variant`, restore template eligibility.

The dashboard population should trend TOWARD tier 1 over time. If it trends away, the
schema is failing its users — fix the schema, don't tighten the fence.

## Building the minimal viable renderer (phase order)

1. **Audit** real dashboards; pick panel types covering ~90% of usage (KPI strip, chart,
   table, text cover most). The rest is `custom_html` for now — measured, not forbidden.
2. **Schema v0 + renderer + goldens.** Reuse what exists: the editorial/research token
   sets, the chart base recipes, the inliner, the hash-router tab runtime, the
   render-verify harness — all specified in beautiful-dashboards. The new work is the
   spec parser, the panel registry, and the Arquero verb executor.
3. **Flip the agent**: emit specs, expose patch tools, demote HTML to artifact. *This
   phase alone delivers the surgical-edit win* — templates can wait.
4. **Templates**: lift → contract validator → instantiate.
5. **Legacy imports**: read the old HTML once, draft a spec, user confirms. Don't build
   an automated HTML→spec importer; the bespoke-markup long tail isn't worth it.

## Anti-patterns

- Forking the renderer per dashboard or team — N codebases, N security reviews, visual
  drift: the disease this design cures.
- Editing a released version in place — silently restyles every saved dashboard.
- Letting the agent "quickly fix" rendered HTML — forks artifact from spec (INV-1).
- Silent spec-version coercion — migrations are explicit scripts or they don't happen.
- A theme system that themes the page but not the chart traces — the registers apply to
  trace colorways, hovers, and annotations too (see beautiful-dashboards).

## See also

- **dashboard-spec** — the authoring/editing side: invariants, schema, patch discipline,
  templates, freedom tiers.
- **beautiful-dashboards** — the visual system themes implement: both registers' tokens,
  chart craft, interaction grammar, diagrams, the build + verify mechanics.
