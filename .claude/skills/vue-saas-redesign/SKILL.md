---
name: vue-saas-redesign
description: Redesigns a Vue 3 application's UI into a modern SaaS-style interface — a fixed-width left vertical sidebar instead of a top nav bar, a consistent spacing/typography system, and a polished professional look. Use when the user asks to "redesign the UI", "modernize the app", "add a sidebar nav", "make this look like a SaaS product", or wants a more professional visual overhaul of a Vue 3 app. General-purpose: works on any Vue 3 app by first discovering its existing design system rather than assuming one.
---

# Vue 3 SaaS Redesign

Converts a Vue 3 app's UI shell from a top nav bar to a modern SaaS-style left
sidebar, and brings spacing/typography/color usage into one consistent system.
This is a **plan-first** skill: always produce a written design plan and get
it approved (via Plan mode) before editing any files. A full-app visual
redesign is high blast-radius — every view gets touched — so the review
checkpoint is not optional.

## Process overview

1. **Discover** the app's current structure and design system.
2. **Design** the target sidebar layout + spacing/typography system, derived
   from what already exists (don't invent an unrelated palette).
3. **Plan** — write the concrete plan (files touched, new components, token
   values) and get user approval via `EnterPlanMode`/`ExitPlanMode` before
   touching any file.
4. **Execute** incrementally, delegating every `.vue` create/edit to a
   frontend-specialist subagent if this project's CLAUDE.md requires it
   (check for a "MANDATORY RULE" about `.vue` files before writing any
   yourself).
5. **Verify** in a real browser — every route, not just the ones you assume
   changed.

Do not skip step 3. Do not skip step 5.

## Step 1 — Discover the existing design system

Before designing anything, read the app as it exists:

- Find the root layout component (commonly `App.vue`) and identify the
  current nav: is it a top bar, tabs, a router-driven menu? List every route
  and its label/icon.
- Find where global styles live (a `<style>` block in the root component,
  a global `main.css`, Tailwind config, or CSS custom properties). Extract
  the actual colors, font stack, border-radius, and shadow values in use —
  don't assume; grep for hex codes and `rgba(`.
- Find reusable UI classes already in use (`.card`, `.stat-card`, `.badge`,
  button classes, table styles) — these define the component vocabulary you
  must keep working, not replace.
- Check for a state-management pattern (Vuex/Pinia/composables) so the new
  sidebar can reflect active-route state the same way the rest of the app
  reflects state.
- Check for i18n (a composable like `useI18n`, `locales/*.js`, or
  `vue-i18n`). If present, every new label you introduce needs a
  translation key added to *every* locale file, not just the default.
- Note the breakpoint(s) already used for responsive behavior, if any.

Summarize this as a short "current state" section before proposing changes —
it's the baseline the plan diffs against.

## Step 2 — Design the target system

### Sidebar navigation

- Fixed-width left sidebar (per this skill's default: not collapsible —
  a static width, typically 240–280px, is enough for a professional look
  without adding toggle state/persistence). If the user explicitly asks for
  a collapsible or icon-only sidebar, build that instead.
- Structure top to bottom:
  1. Brand/logo area (reuse whatever brand mark the top nav had)
  2. Primary nav links — one per existing route, in their existing order,
     each with an icon + label. Reuse existing icons/assets if the top nav
     had them; otherwise pick a consistent icon set (simple inline SVGs, no
     icon-font dependency unless one is already installed).
  3. Optional secondary/utility section (settings, profile, logout) pinned
     near the bottom if the old top nav had equivalent elements (profile
     menu, language switcher, etc.) — don't drop functionality, relocate it.
- Active-route styling: a clear but subtle treatment (background tint +
  left accent bar or bold text + accent-colored icon) — avoid loud color
  blocks that clash with the app's existing palette.
- Responsive fallback: below the app's existing mobile breakpoint (or
  ~768px if none exists), the sidebar should not silently disappear —
  collapse it into a top bar with a hamburger-triggered off-canvas drawer,
  or stack it above content. Decide and state which, in the plan.
- The main content area shifts right by the sidebar's width (fixed sidebar
  + `margin-left`/`padding-left` on the content wrapper, or a CSS grid with
  a sidebar column) — don't use JS-computed layout offsets for something CSS
  can do.

### Spacing & typography system

Define this as a small set of CSS custom properties added once (root
`:root` or the app's existing global style location) and then used
everywhere — not repeated as magic numbers per component:

```css
:root {
  /* spacing scale — 4px base unit, use these instead of ad-hoc px values */
  --space-1: 4px;
  --space-2: 8px;
  --space-3: 12px;
  --space-4: 16px;
  --space-5: 24px;
  --space-6: 32px;
  --space-8: 48px;

  /* radius */
  --radius-sm: 6px;
  --radius-md: 10px;
  --radius-lg: 16px;

  /* type scale — adjust base size to what the app already uses */
  --text-xs: 0.75rem;
  --text-sm: 0.875rem;
  --text-base: 1rem;
  --text-lg: 1.125rem;
  --text-xl: 1.5rem;
  --text-2xl: 2rem;
}
```

Derive the actual color values from Step 1's findings — reuse the app's
existing ink/muted/border/accent/status colors as custom properties if they
aren't already, rather than introducing a new palette. A redesign should
feel like the same product, more polished — not a different app.

Apply consistently:
- Every card/panel gets the same padding (`--space-5` is a common sweet
  spot) and the same `--radius-md`.
- Grid/flex gaps between cards and stat tiles are one consistent value
  (`--space-4` or `--space-5`) app-wide, not different per view.
- Page headers (title + description) get consistent margin below them
  before content starts.
- Table cell padding, badge padding, and button padding are each a single
  consistent value reused across every view — audit and fix any view using
  a different figure "by accident."

## Step 3 — Write the plan, then stop and get approval

Enter Plan mode. The plan must state, concretely:

- The current-state summary from Step 1.
- The new `Sidebar` (or equivalent) component: props/state it needs (current
  route for active-highlighting, any relocated elements like profile menu).
- The exact list of files that will change (root layout, every view that
  needs spacing normalized, i18n locale files, router config if nav labels
  live there) and, for each, what changes.
- The token values chosen in Step 2, justified by what was found in Step 1
  (not invented from scratch).
- The responsive strategy for the sidebar.
- Explicit confirmation that all existing routes, filters, i18n keys, and
  interactive behavior are preserved — a redesign changes presentation, not
  functionality, unless the user asked for both.

Only call `ExitPlanMode` (or otherwise proceed) once the user approves.

## Step 4 — Execute incrementally

Once approved:

1. Add the design tokens (Step 2) to the global stylesheet first.
2. Build the new sidebar/nav component in isolation.
3. Restructure the root layout to use it in place of the top nav, wiring
   active-route highlighting off the router's current route.
4. Sweep each view to normalize spacing/typography to the new tokens —
   do this file by file so each diff stays reviewable, not as one giant
   mechanical find-replace across the whole `src/` tree.
5. If the project has a mandatory rule about delegating `.vue` work to a
   specific subagent (check the project's CLAUDE.md files), follow it for
   every create/edit in this step — don't write `.vue` files directly if a
   rule says otherwise.
6. Update every i18n locale file for any new/relocated labels, not just the
   default locale.

## Step 5 — Verify in the browser

Don't consider this done from code review alone:

- Start the dev server(s) if not already running.
- Navigate to *every* route the app has, not just one or two — the sidebar
  and spacing changes touch all of them.
- Confirm active-route highlighting updates correctly when navigating.
- Confirm the responsive fallback actually triggers at the chosen
  breakpoint (resize the viewport, don't just read the CSS).
- Confirm nothing that existed before is missing (filters, profile menu,
  language switcher, any modal triggers that lived in the old top nav).
- Check the browser console for new errors/warnings introduced by the
  layout change.

Fix anything broken before reporting the redesign as complete.

## Common pitfalls

- ❌ Inventing a new color palette instead of systematizing the existing one
  — the app should look more polished, not like a different product.
- ❌ Hardcoding spacing values in the sidebar/new components instead of using
  the token set — defeats the "consistent spacing" goal immediately.
- ❌ Dropping functionality that lived in the old top nav (profile menu,
  language switcher, filter bar) because it didn't fit the sidebar mental
  model — relocate it, don't remove it.
- ❌ Skipping the plan-approval checkpoint because "it's just CSS" — sidebar
  navigation is a structural change to every page, not a style tweak.
- ❌ Verifying only the home route after the change and assuming the rest of
  the app inherited the layout correctly.
- ❌ Forgetting non-default i18n locale files when adding new nav labels.
