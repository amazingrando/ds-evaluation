# Work Order — Emulsify UI Kit • Beta (Community)

_From inspection: `reports/2026-11-17-inspection.md` · Written: 2026-11-17_

Reds get fixed now. Yellows get scheduled. Greens get left alone (and celebrated — there aren't any yet, but the token pipeline is close). Every item cites its station and evidence — no vibes-based work items. The team owns prioritization; this is the technician's recommendation.

## 🔴 Fix now (reds)

### 1. The CI visual-regression job never actually runs
- **Station:** 5, Testing & validation · **Evidence:** `[verified]` `tests.yml`'s `visual-regression` job gates Percy on `dorny/paths-filter` matching `components/**`, but `components/` is gitignored build output (confirmed via `.gitignore` and a directory diff against `src/components/`) — the filter can never match a real PR diff.
- **Why it's first:** every other quality claim in this report (code craft, accessibility, naming) was spot-checked by hand because there's no automated net catching regressions. Fixing this is what makes the *next* inspection faster and the team's day-to-day safer.
- **First move:** change the filter pattern in `.github/workflows/tests.yml` from `components/**` to `src/components/**`; open a tiny test PR touching one component's `.scss` to confirm the Percy step now actually executes.
- **AI assist:** have an agent find every CI config referencing a gitignored path (a one-line grep-and-cross-reference check) so this class of bug doesn't recur elsewhere; a human should still eyeball the fixed workflow run once before trusting it.
- **Done when:** a PR that only touches `src/components/**` shows the Percy step actually running (not skipped) in its checks.
- **Effort:** S

### 2. The test suite is non-functional as shipped
- **Station:** 5, Testing & validation · **Evidence:** `[verified]` `npm test` runs `jest --coverage --config ./config/jest.config.js`; that config file does not exist, and there are zero `*.test.js`/`*.spec.js` files anywhere in `src/`.
- **Why it's first:** this blocks Station 3's keyboard/focus test coverage, Station 9's "tests as machine-readable behavior spec" angle, and any future claim that a component "works" beyond a visual screenshot.
- **First move:** add a minimal `config/jest.config.js` and one real test for Button (render + the `aria-label` fallback behavior when `button__content` is absent) as the template for the rest.
- **AI assist:** have an agent generate the missing test-coverage report (what's asserted vs. not, per component) and draft the first batch of tests from existing Storybook stories' args; a human should review that the tests assert real behavior, not just "it renders."
- **Done when:** `npm test` runs to completion and reports real pass/fail on at least the components exercised by existing stories.
- **Effort:** M

### 3. Storybook's Figma links point at the wrong file
- **Station:** 6, Orchestration · **Evidence:** `[verified]` `config/configma.json`'s `url` resolves to file key `eo87m50yAw3P8iXTypxw4I` ("Four-Kitchens-UI-Kit"), not `muDcrGCtnJUn2tTyp7LkcU` ("Emulsify UI Kit • Beta (Community)"), the file this system ships and the README tells consumers to copy. Only 10 of 43 components have an entry at all.
- **Why it's first:** this is the exact Community-file-duplication trap the design-access reference calls out, happening in production right now — every designer or agent clicking a "view in Figma" link from Storybook today lands in the wrong place or nowhere.
- **First move:** regenerate `configma.json`'s `url` against the live file key, then add entries for the 33 components currently missing — Button's `State`/scheme gap (see item below) is a good first real test case once the link actually resolves.
- **AI assist:** an agent can map every Storybook story to its Figma node ID in one sweep (by component name) and emit the full `configma.json`; a human should spot-check a handful of links before merging.
- **Done when:** every component's story links to a real frame in `muDcrGCtnJUn2tTyp7LkcU`.
- **Effort:** M

## 🟡 Schedule (yellows)

### 4. Button's color-scheme prop has four different descriptions and none of them match
- **Station:** 2 & 4, Best practices / Shared language · **Evidence:** `[verified]` `button.twig` comment says "filled/outline"; `button.component.yml` doesn't document the prop; `button.scss`/`button.stories.js` implement `primary`/`clean`/`disabled`; Figma's Button component models no scheme property at all (confirmed via `componentPropertyDefinitions`) and says so in its own description.
- **First move:** pick one real scheme (`primary`/`clean`/`disabled`, since that's what's actually implemented and tested in Storybook), fix the twig comment and `component.yml` to match, then decide the Figma modeling approach (new variant axis vs. color-mode swap, as the component's own description already suggests).
- **Done when:** all four sources (twig comment, schema file, SCSS/stories, Figma) agree on the same prop name and the same three values.
- **Effort:** S
- **Suggested timing:** this sprint — it's small, bounded, and becomes the template for auditing the rest of the component library.

### 5. Two literal color collisions make announcement headings invisible
- **Station:** 3, Accessibility · **Evidence:** `[verified]` `announcement/heading/light` = `announcement/background/light` = `#9CE1FF`; `announcement/heading/dark` = `announcement/background/dark` = `#006089`. Only the "default" mode is currently usable.
- **First move:** pick new heading colors for the `light` and `dark` modes that meet 4.5:1 against their own mode's background (the `default` mode's `#0080B7`-on-white pairing is a reasonable model to extend).
- **Done when:** all three announcement modes pass 4.5:1 for heading text against their own background, verified against resolved variable values (not the raw combinatorial audit subscore).
- **Effort:** S
- **Suggested timing:** this sprint.

### 6. Input placeholder and help-text contrast
- **Station:** 3, Accessibility · **Evidence:** `[verified]` placeholder `#A9AFB1` on white ≈1.7:1 (fails AA by a wide margin) · `[reported]` help text `#0080B7` on white ≈4.4:1 (marginal fail for normal-size text).
- **First move:** darken `input/color/placeholder` toward `color/grays/500`+ and re-check; confirm help text's actual rendered size (if it qualifies as "large text" at 18.5px+ bold, it already passes at 3:1 — if not, nudge the color slightly darker).
- **Done when:** both tokens pass their applicable WCAG threshold, re-verified from resolved values.
- **Effort:** S
- **Suggested timing:** this sprint (bundle with item 5 — same token-editing session).

### 7. No automated accessibility checks in CI
- **Station:** 3, Accessibility · **Evidence:** `[verified]` none of the six `.github/workflows/*.yml` files run axe-core, pa11y, or Lighthouse.
- **First move:** add an axe-core (or pa11y) step to `tests.yml`, gated on the same (corrected) path filter as item 1, running against the built Storybook.
- **Done when:** a PR touching a component shows an a11y-check result in its CI run.
- **Effort:** M
- **Suggested timing:** this quarter — sequence after item 1 (fix the path filter first, or this inherits the same bug).

### 8. No CONTRIBUTING.md despite the README promising one
- **Station:** 7, Governance & version control · **Evidence:** `[verified]` README's "Contributing to this UI Kit" section reads "To come. TKTK"; no `CONTRIBUTING.md`/`CHANGELOG.md` at repo root.
- **First move:** draft `CONTRIBUTING.md` from what the CI workflows and PR template already imply about the real process (commitlint conventions, the a11y PR checklist item, the token-regeneration workflow) — document the process that's already running before inventing a new one.
- **Done when:** the file exists and the README links to it instead of "TKTK."
- **Effort:** S
- **Suggested timing:** this quarter.

### 9. Figma component descriptions and category organization
- **Station:** 2, Best practices · **Evidence:** `[verified]` 1 of 43 components (2%) have any Figma description; 3 of 43 (7%) use path-based category grouping.
- **First move:** generate first-draft descriptions for all 43 components from their existing name/props/variants (`figma_generate_component_doc`-style pass), human-review before publishing; batch-rename into category paths (`Forms/Input`, `Navigation/Pager`, etc.) in one coordinated pass so code-side references aren't silently broken.
- **Done when:** every component has a real description; category grouping is visible in the asset panel.
- **Effort:** M
- **Suggested timing:** before next inspection.

## 🔧 Access upgrades (sharper next inspection)

- **Station 8 (Feedback & adoption) was not inspected at all.** Get access to at least one real consumer repo (for an import/usage grep) or to the upstream `emulsify-ds` GitHub organization's Issues/Discussions — either would turn this from N/I into a real score next time.
- **Station 7's tracker health** (duplicate/stale issues, triage cadence) needs the same upstream GitHub access — currently `[reported: not inspected]`.
- **Stations 9 & 10's required proof steps were both skipped this pass.** Running one real "ask an agent to build a small form using only this system" test would upgrade both from provisional/red to a confirmed score, and is cheap relative to everything else on this list.

## 🟢 Keeping the greens green

- None yet — there are no green stations this pass. The token pipeline (Figma Tokens export → Style Dictionary → SCSS, CI-automated via `create-tokens.yml`) is the one piece of real, working infrastructure close to green; protecting it means making sure item 3's `configma.json` fix doesn't get treated as "the same kind of file" and skipped the same scrutiny the token pipeline itself already gets.

## Cadence

- Re-inspect (deep, all stations): 2027-02-17
- Everyday checks to wire into CI now: the corrected visual-regression path filter (item 1) and an axe-core step (item 7) — both are one-time setup that then runs forever.
- Owner of this work order: not assigned in this checkout — assign before acting on it · Review: not established; this report recommends establishing one (see Station 7 work item 8).
