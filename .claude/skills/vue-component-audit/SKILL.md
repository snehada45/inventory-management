---
name: vue-component-audit
description: Analyze Vue 3 component structure across client/src and report performance and code-reuse optimization opportunities. Use when the user asks to audit, review, or find optimizations in Vue components (not for making the changes themselves).
---

# Vue Component Audit

Read-only analysis skill. It produces a prioritized findings report — it does not edit files.
If the user wants the suggested fixes applied, that is a separate follow-up step and must go
through the **vue-expert** subagent per CLAUDE.md's mandatory rule for any `.vue` file edits.

## Scope

Default: every `.vue` file under `client/src` (views, components, and any composables in
`client/src/composables` if present). If the user names specific files/components, or points at
the current git diff, narrow to those instead — but still check them against the rest of
`client/src` for duplication, since reuse issues are inherently cross-file.

## How to run the audit

1. `Glob` `client/src/**/*.vue` (and `client/src/**/*.js` for composables/api client) to build the
   file set.
2. Read each file. For anything non-trivial (>~150 lines, or many files), delegate reading/analysis
   in parallel via the `Explore` agent or `Agent` forks rather than reading everything serially into
   context — synthesize the findings yourself afterward.
3. Check each file against the categories below.
4. Deduplicate: the same anti-pattern repeated across files is one finding with multiple locations,
   not N findings.
5. Output the report per the format below. Do not modify any files.

## What to look for

### Reactivity & performance

- `computed()` opportunities: derived values recalculated inline in the template or in methods on
  every render instead of being memoized (CLAUDE.md's own pattern: raw data in refs, derived data
  in computed properties — flag views that recompute derived state in `watch`/methods instead).
- Missing or wrong `v-for` keys — `:key="index"` instead of a stable id (`sku`, `month`, etc. per
  CLAUDE.md's known pitfall #1).
- Watchers doing work a computed could do, or watchers with no `immediate`/cleanup where needed.
- Expensive work (sorting, filtering, formatting) inside `<template>` expressions or unmemoized
  inline functions passed as props (new function identity every render).
- Deeply nested reactive objects mutated in place where a flatter structure or `shallowRef` would
  avoid unnecessary deep reactivity overhead.
- Large lists rendered without virtualization when the data volume could grow unbounded.
- Unnecessary full-object watches (`watch(obj, ..., {deep: true})`) where watching a specific field
  would do.

### Code reuse & structure

- Duplicated template markup or logic across two or more `.vue` files that isn't yet extracted into
  a shared component.
- Repeated non-trivial script logic (formatting, filter-building, API-call patterns) that belongs in
  a composable (`useX`) instead of being copy-pasted per component.
- Components mixing multiple concerns (data fetching + filtering + presentation) that would be
  clearer split into a container + presentational component, or a composable + a "dumb" component.
- Prop drilling more than 2 levels deep where `provide`/`inject` or a composable would remove the
  relay components.
- Oversized components (rough guide: >300 lines or >5 distinct responsibilities) that are hard to
  reason about.
- Inconsistent patterns for the same job across files (e.g. some views build filter query params
  inline, others via a shared helper) — flag the divergence and point at the pattern to standardize
  on.

Skip anything CLAUDE.md already prescribes as-is (e.g. the 4-filter query-param flow through
`client/src/api.js`) — only flag deviations from it, not the pattern itself.

## Report format

Group findings by category, most-impactful first. For each finding:

- **File(s) + line(s)**
- One-sentence description of the issue
- Why it matters (perf cost or duplication cost — be concrete: "recomputed on every keystroke",
  "same 40-line filter block duplicated in 3 views")
- Suggested fix, briefly (not implemented)

End with a short summary: total findings by category, and — if asked to apply fixes — a note that
the vue-expert agent should be used for that follow-up.
