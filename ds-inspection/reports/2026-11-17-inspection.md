# Multi-Point Inspection Report — Emulsify UI Kit • Beta (Community)

_Inspected: 2026-11-17 · Technician: Claude (Sonnet 5) via `ds-inspection` skill · Previous inspection: first inspection_
_Vehicle profile: `ds-inspection/GARAGE.md` (checked in 2026-11-17)_

## The short version

Looked at on its own, the Figma library is in decent shape: a real three-tier token architecture (global → semantic → component, 299 variables, 28 collections), consistent naming, and good variant coverage — Figma's own design-system audit scores it 78/100. That score only sees half the vehicle. Once you pull in the code side, the picture gets rougher fast: the test suite is non-functional as shipped (`npm test` points at a config file that doesn't exist, and there isn't a single test file in the repo), the CI visual-regression job watches a gitignored build folder that can never appear in a PR diff — so it never actually runs — and the Storybook-to-Figma story links point at a *different* Figma file than the one this system ships today. The flagship Button component is the clearest single artifact of the whole inspection: its own Figma description already flags a real, unresolved gap (code supports three color schemes, Figma models one), and inside the code alone, three different sources (a Twig comment, the component's schema file, and the actual SCSS/stories) each describe that same prop differently. The single most important next move: fix the CI path filter so visual regression actually runs, then stand up a minimal test file — those two unblock everything else.

**Overall: 42/100*** — a conversation starter, not a grade. Fix the reds, schedule the yellows, re-run on a cadence.
*Score is /90 pro-rata (raw 38/90, scaled) — Station 8 (Feedback & adoption) was not inspected: this checkout has no access to consumer repos, usage analytics, or the upstream support channel/tracker.

## Inspection sheet

|  # | Station                         | Quality      | Light |      Score |
|---:|:---------------------------------|:-------------|:-----:|-----------:|
|  1 | Coverage & gaps                 | Complete     |  🟡   |       6/10 |
|  2 | Best practices                  | Sound        |  🟡   |       5/10 |
|  3 | Accessibility                   | Sound        |  🟡   |       5/10 |
|  4 | Shared language                 | Sound        |  🟡   |       5/10 |
|  5 | Testing & validation            | Sound        |  🔴   |       3/10 |
|  6 | Orchestration                   | Synchronized |  🔴   |       3/10 |
|  7 | Governance & version control    | Extensible   |  🟡   |       4/10 |
|  8 | Feedback & adoption             | Extensible   |  N/I  |         N/I |
|  9 | Machine-readable docs & context | AI-Ready     |  🔴   |       3/10 |
| 10 | Agent access                    | AI-Ready     |  🟡   |   4/10 (provisional) |
|    | **Overall**                     |              |       | **42/100*** |

**Lights:** 🟢 0 green · 🟡 6 yellow · 🔴 3 red · 1 not inspected

**Key:** 🔴 Red (0–3) — broken or missing; the light is ON · 🟡 Yellow (4–7) — drift or gaps; schedule a fix · 🟢 Green (8–10) — healthy, no action needed · **N/I** — not inspected (no evidence access; never guessed)

## Evidence basis

- Access used this pass: **live, both sides.** Design: native Figma MCP (`get_metadata`, `get_screenshot`) and Figma Console MCP via a connected Desktop Bridge (port 9224, probed) — `figma_get_design_system_summary`, `figma_search_components` (all 43 components), `figma_get_variables` (299 vars, 28 collections, aliases resolved), `figma_get_styles`, `figma_lint_design`, `figma_audit_design_system[_report]`, `figma_get_component`, `figma_get_comments` all returned live content from the real file, not cache stubs. Code: full repo read — component source (Twig/SCSS/YAML/stories) for Button, Input, Checkbox, Radio, Select, Navigation; `.github/workflows/*.yml`; token pipeline files; `package.json`; README; `.gitignore`. Docs: Storybook config/stories read from source; the Storybook UI itself was not launched and rendered.
- Findings tagged `[verified]`: 15 · `[reported]`: 3
- One important correction made mid-inspection: `figma_lint_design`'s own contrast findings on the Input component (41 "critical" failures against a background of `#9747FF`) were a tooling artifact — that purple is the Dev Mode annotation/measurement overlay color, not the component's real background. A screenshot and the resolved `components/input` variable values (`#FFFFFF` bkg, `#00202E` label) showed the real picture is much better than the raw tool output claimed. This is exactly the caution `reference/FIGMA-ACCESS.md` gives about this tool — it's relayed here as a demonstration of why every automated subscore in this report was checked against resolved token values before being written down as a finding.

## Station records

### Station 1 — Coverage & gaps: 🟡 YELLOW (6/10)
- Inventory: 43 components/sets in Figma (live, full sweep) · ~24 top-level component directories in code, several containing multiple sub-components (`forms/` → checkbox, radio, select, textfields; `navigation/` → main, social, breadcrumbs, pager) (live, full read of `src/components/`) · docs: every sampled component (Button, Input, Checkbox, Breadcrumbs, Pager) has a `.stories.js` file (existence only; not content-checked here, see Station 2)
- Evidence level: live for design and code; live (source-only) for docs
- Findings:
  - [verified] Core staples present on both sides with real parity: button, link, input/checkbox/radio/select, card, table, tabs, accordion, breadcrumbs, pager, main/social navigation, status. This is a solidly-covered core.
  - [verified] Missing from **both** design and code: modal/dialog, toast, avatar, badge. These are common staples (per Material/Fluent/Carbon/Polaris) that teams using this kit will have to build themselves.
  - [verified] Figma has a full `components/tooltip` token collection (4 variables: background/dark, background/light, icon/color, icon/background) but **no Tooltip component exists** in Figma's 43-item inventory or in the code tree. Tokens were built for a component that was never built — on either side.
  - [verified] Token completeness is genuinely good: 299 variables, 28 collections, confirmed 3-tier alias depth (global → semantic → component) via `figma_audit_design_system_report` (token-tier-depth: 100/100) and spot-checked directly on the `components/input` collection.
  - [verified] Distribution is real, not copy-paste: `@emulsify/cli` + the `emulsify-ds/compound` git repo, wired into a CI job (`tests.yml`'s `cli-verification`) that actually installs the system and all components on every PR.
- Not inspected: full code-vs-design state/variant depth per component (sampled Button and Input only, see Stations 2–4); full docs *quality* (deferred to Station 2 on purpose, per this station's scope).
- Deviations noted: none declared; the tooling/component gaps above read as unaddressed rather than intentional.
- First move: decide, as a team, whether modal/dialog/toast/avatar/badge are in scope for this kit at all — if yes, prioritize; if no, say so in the README's "What the Emulsify UI Kit is not" section so consumers stop expecting them.

### Station 2 — Best practices: 🟡 YELLOW (5/10)
- Sampled: Button (twig/scss/yml/stories, full read), Checkbox markup (twig), Figma component-metadata audit (all 43 components)
- Evidence level: live for both design and code
- Findings:
  - [verified, format] Code craft on the sampled components is genuinely good: `button.scss` is token-driven throughout (`var(--button-color-bkg)` etc.), no magic numbers; `checkbox` markup is a real `<input type="checkbox">` with a correctly-associated `<label for>`, not a div-based fake control.
  - [verified, org] `button.component.yml` (the component's formal schema file) documents only one prop (`button__additional_classes`) and its example values (`button--hover`, `button--focus`) describe states that are actually applied via CSS pseudo-classes (`:hover`, `:focus`) in `button.scss`, not via classes a consumer passes — the one documented example doesn't match how the component actually works.
  - [verified, org] The same component's **three** internal descriptions of its color-scheme prop disagree with each other: `button.twig`'s doc comment says the style values are "filled (default) or outline"; `button.component.yml` doesn't document the prop at all; `button.scss` and `button.stories.js` both implement/exercise `primary`/`clean`/`disabled`. Three sources, inside one component, three different answers — before Figma even enters the picture (see Station 6).
  - [reported, format] Figma component organization: only 3 of 43 components (7%) use path-based category grouping (`figma_audit_design_system_report`, component-category-org: 7/100) — the asset panel is largely flat, which will slow down anyone browsing the library by hand.
  - [verified, format] Figma component descriptions: 1 of 43 (2%) have any description at all (`component-desc-presence`: 2/100) — the one exception is the Button's seeded "KNOWN GAP" note (see Station 6).
- Not inspected: code craft beyond Button/Checkbox (no full sweep of all ~24 component directories); docs page quality in the rendered Storybook UI (source-only read).
- Deviations noted: none.
- First move: fix `button.component.yml` to document the real `button__style`/scheme prop and its three real values — then use the corrected file as the template for auditing the rest.

### Station 3 — Accessibility: 🟡 YELLOW (5/10)
- Sampled: Button, Input, Checkbox (code) · Input, Announcement (Figma variables, resolved) · all `.github/workflows/*.yml` (CI wiring) · Design side: component descriptions and annotations checked on Button and Input (both empty of a11y guidance)
- Evidence level: live for design and code
- Findings:
  - [verified] **Real contrast bug in token values**, not a tooling artifact: `announcement/heading/light` (`#9CE1FF`) resolves to the exact same hex as `announcement/background/light` (`#9CE1FF`) — a 1:1 ratio, meaning the announcement component's heading text is literally invisible against its own background in the "light" mode. The same bug repeats in "dark" mode: `announcement/heading/dark` and `announcement/background/dark` both resolve to `#006089`. Only the "default" mode (white background, `#0080B7` heading) is actually usable. Verified directly against resolved `components/announcement` variable values, not from the automated audit's subscore.
  - [verified] `input/color/placeholder` (`#A9AFB1`) on the input's white background (`input/bkg` = `#FFFFFF`) computes to ≈1.7:1 — well under the 4.5:1 AA threshold for text content, confirmed by direct calculation on the resolved hex values.
  - [reported] `input/color/helpText` (`#0080B7`, aliasing `color/primary/dark`) on white computes to ≈4.4:1 — just under the 4.5:1 AA threshold for normal-size text (would pass at 3:1 for large text, but help text is small). Marginal; flagging as a near-miss rather than a hard fail given rounding in hex-to-luminance conversion.
  - [verified] No axe-core, pa11y, Lighthouse, or any other automated accessibility check exists in any of the six `.github/workflows/*.yml` files. CI runs Percy visual regression (and, per Station 5, that job's trigger condition is itself broken) and a CLI-install smoke test — nothing else.
  - [verified] No test files exist anywhere in `src/` (see Station 5), so there is no keyboard-interaction or focus-management test coverage either, despite real ARIA/semantic groundwork in the Button markup (`aria-expanded`, `aria-controls`, `aria-label` fallback for icon-only buttons) that would be worth protecting with tests.
  - [reported] The automated `figma_audit_design_system_report` accessibility subscore (46/100, "23 of 50 color pairs meet AA") should not be read pair-by-pair at face value — its own example list includes at least one pairing (`color/text/white` / `announcement/background/default`) that isn't how the component actually combines its tokens (the real default-mode pairing is dark-blue heading on white, which is fine). The two heading/background collisions above were independently confirmed by reading the actual component collection; the broad subscore is directionally correct (there is a real contrast problem here) but its specific pair list needs the same hand-verification before anyone acts on it line by line.
- Not inspected: contrast across every component collection (29 collections beyond Announcement/Input were not individually hand-verified); screen-reader behavior (no screen reader run); dark-mode-equivalent testing (the system has no dark mode — see Station 6, token-mode-coverage: 50/100, "only 1 mode").
- Deviations noted: none.
- First move: fix the two literal color collisions in `announcement/heading/light` and `announcement/heading/dark` — it's a one-line value change per token, not a design decision, and it's currently shipping invisible text.

### Station 4 — Shared language: 🟡 YELLOW (5/10)
- Swept: all 299 Figma variable names (via `figma_audit_design_system_report`, naming-semantics + consistency categories) · Button traced across twig comment / component.yml / scss / stories.js / Figma component description
- Evidence level: live for design; live for the one component traced in code
- Findings:
  - [verified] Figma's own naming scheme is clean: 100% of variables use a consistent `/` delimiter, 99% use semantic (not visual) color names (`naming-variable-semantic`: 99/100 — the one exception, `color/text/white`, is itself a reasonable semantic name), consistent mode-name spelling across all 28 collections.
  - [verified] Component name casing is inconsistent: only 29 of 43 (67%) use consistent PascalCase; examples like `Chevron-Up`, `Call to Action` break the pattern (`naming-component-casing`: 67/100).
  - [verified] **Cross-source disagreement on a single prop, inside code alone, confirmed by direct read**: Button's color-scheme property is called/valued three different ways across three files in the same component folder — `button.twig`'s doc comment ("filled/outline"), `button.component.yml` (prop undocumented), and `button.scss`/`button.stories.js` (`primary`/`clean`/`disabled`, which is what's actually implemented and exercised). This is the "classic" cross-API naming failure the station describes — found on the system's single most basic component, not a rare edge case.
  - [verified] No naming validator, lint rule, or drift-detection check found anywhere in the lint/CI configuration (`config/emulsify-core/eslintrc.config.json` / `stylelintrc.config.json` govern code style, not cross-asset naming) — whatever consistency exists today is unenforced and will drift.
- Not inspected: a full cross-component API sweep in code (prop names/values across all ~24 directories) — only Button was traced in depth.
- Deviations noted: none.
- First move: resolve the Button prop's three-way naming disagreement first (it's small, bounded, and is the concrete example to use when writing the naming algorithm down for the rest of the system).

### Station 5 — Testing & validation: 🔴 RED (3/10)
- Inspected: `package.json` scripts, `config/jest.config.js` (checked for existence), full `src/` tree (searched for `*.test.js`/`*.spec.js`), all six `.github/workflows/*.yml` files, `.gitignore`
- Evidence level: live
- Findings:
  - [verified] `npm test` runs `jest --coverage --config ./config/jest.config.js`, but **`config/jest.config.js` does not exist** in this repo. The test command as shipped cannot run.
  - [verified] **Zero test files** exist anywhere under `src/` — no `*.test.js`, no `*.spec.js`. There is nothing for the broken Jest config to even run once fixed.
  - [verified] **The CI visual-regression job cannot trigger on real changes.** `tests.yml`'s `visual-regression` job gates its Percy steps on `dorny/paths-filter` matching `components/**`. But `components/` is listed in `.gitignore` (confirmed: `components/` is explicitly gitignored, with `!src/components/` as the un-ignore exception for the real source) — it's the generated webpack build output, confirmed by diffing its contents against `src/components/` (same component names, but compiled `.css`/`.js`/`.map` files instead of source `.scss`/`.twig`). Since nothing under `components/` is ever committed, `paths-filter` can never report a change there, so every Percy step's `if: steps.component_changes.outputs.components == 'true'` condition evaluates false on every real PR. The job appears to exist and pass, but its actual test steps are silently skipped every time.
  - [verified] The one CI job that does meaningfully run: `cli-verification`, which installs the system via `@emulsify/cli` and runs `emulsify component install --all` — a real smoke test, but it only proves installation works, not that any component behaves correctly.
- Not inspected: visual regression's actual output when it *does* run (never observed running in this checkout); any manual/local-only test practices the team may use outside CI (no human to interview).
- Deviations noted: none — this reads as unintentional breakage, not a documented choice.
- First move: fix the `paths-filter` pattern in `tests.yml` to watch `src/components/**` (the real source) instead of `components/**` — this single line is why the system's only quality gate has been failing to run.

### Station 6 — Orchestration: 🔴 RED (3/10)
- Diffed: Figma variable collection names vs. `src/tokens/figma.tokens.json` top-level keys · Button component (Figma description vs. code implementation) · Storybook↔Figma link config (`config/configma.json`) vs. the Figma file actually in use
- Token diff: 28 Figma collections vs. matching top-level keys in `figma.tokens.json` (`global`, `semantic`, `components/button`, `components/input`, …) — **names match exactly**, confirming a real single-source token pipeline (Figma Tokens/Tokens Studio export → `token-transformer` → Style Dictionary → SCSS, wired into CI via `create-tokens.yml`). Values/structure were not diffed token-by-token beyond this collection-name check.
- Evidence level: live, both sides
- Findings:
  - [verified] The token *pipeline* is genuinely synchronized and automated — one clear source of truth, same collection names on both sides, CI-enforced regeneration (`create-tokens.yml` runs on every PR and auto-commits the regenerated output). This is real, working orchestration and should be protected, not just credited.
  - [verified] **`config/configma.json` — the file that links every Storybook story back to its Figma frame — points at file key `eo87m50yAw3P8iXTypxw4I` ("Four-Kitchens-UI-Kit")**, which is a different file from `muDcrGCtnJUn2tTyp7LkcU` ("Emulsify UI Kit • Beta (Community)"), the file this inspection targets and the one the README tells consumers to copy from the Figma Community. This is the exact Community-file-duplication failure mode `reference/FIGMA-ACCESS.md` warns about: node IDs recorded against one file (even the *original* internal file, in this case) don't resolve in a duplicate or a renamed/republished file. Every `figma.$KEY` link generated from this config is pointing at the wrong file for anyone following the README's own setup instructions today.
  - [verified] Of the 43 components in the live Figma file, only 10 keys exist in `configma.json` at all (`button` plus 9 token categories: `audioPlayer`, `borderRadius`, `border`, `breakpoint`, `lineHeight`, `opacity`, `size`, `spacing`, `typeScale`) — the large majority of components (Checkbox, Radio, Input, Select, Accordion, Breadcrumbs, Pager, Tabs, Table, Status, Card, Footer, all of navigation, …) have no Figma-link wiring in Storybook at all, independent of the file-key problem above.
  - [verified] The Button component's own Figma description carries a live, dated, unresolved drift note: code (`src/components/button/button.scss`) implements three color schemes (`primary`, `clean`, `disabled`, confirmed by direct read), but the Figma component set models only one visual treatment with a `State` variant axis (`Default`/`Hover`/`Focus`) — no scheme/style property exists in Figma at all (confirmed via `figma_get_component`'s `componentPropertyDefinitions`). The description itself is dated 2026-11-13 and labeled "AI-readiness pilot," meaning someone already found and flagged this — it just hasn't been fixed.
- Not inspected: design↔code diffing for components beyond Button (no comparable full prop/variant trace was run on Checkbox/Radio/Input/etc.); whether the *original* internal Figma file (behind the `eo87m50yAw3P8iXTypxw4I` key) is itself reachable or current — only that it isn't the file this inspection was pointed at.
- Deviations noted: none — the file-key mismatch and the Button drift both read as unaddressed real drift, not documented decisions.
- First move: regenerate `config/configma.json` against the live `muDcrGCtnJUn2tTyp7LkcU` file key and extend it to cover all 43 components, not just 10 — then resolve the Button scheme gap as the first test case once the links actually work.

### Station 7 — Governance & version control: 🟡 YELLOW (4/10)
- Inspected: `.github/workflows/*.yml` (6 files), `.github/workflows/PULL_REQUEST_TEMPLATE/pull_request_template.md`, `commitlint.config.cjs`, `release.config.cjs`, repo root (searched for CONTRIBUTING/CHANGELOG/CODE_OF_CONDUCT — none found), README's own "Contributing" section
- Evidence level: live
- Findings:
  - [verified] Real process scaffolding exists and runs: a PR template with a structured review checklist and an explicit accessibility sign-off item (linking to an org-level a11y policy doc); `commitlint` + `semantic-release` wired for conventional-commit-driven versioning; dedicated workflows for releases, GH Pages deploy, token regeneration, and contributor tracking (`contributors.yml`, `addtoprojects.yml` — evidence of at least some triage automation, even though tracker health itself wasn't reachable).
  - [verified] No `CONTRIBUTING.md` or `CHANGELOG.md` exists at the repo root. The README's own "Contributing to this UI Kit" section is the explicit placeholder text **"To come. TKTK."** — this isn't a stale doc describing an old reality (the subtler failure this station warns about); it's an honest, current admission that the promised artifact was never written.
  - [reported] No issue templates found in `.github/` (only a PR template) — contribution intake for bug reports/feature requests has no structured path, though this wasn't confirmed against the live upstream GitHub repo's Issues tab from this checkout.
- Not inspected: tracker health (duplicate/stale issues, triage cadence), release cadence history, ownership — none reachable from this checkout (no access to the upstream `emulsify-ds` organization's issue tracker or team structure).
- Deviations noted: none.
- First move: write the CONTRIBUTING.md the README already promises — it's the one concrete, bounded gap this station found with full confidence, and it unblocks testing "paper vs. reality" on the next inspection.

### Station 8 — Feedback & adoption: **N/I (not inspected)**
- Measured: nothing — no consumer repos available to scan for imports/usage, no design-library analytics reachable, no access to the upstream support channel or issue tracker from this checkout.
- What would unlock this: access to one or more real product repos that consume this kit (for a dependency/import scan), or to the upstream `emulsify-ds` GitHub organization's Issues/Discussions for feedback-loop and roadmap signal.
- Not scoring this station rather than guessing — see GARAGE.md's "Out of scope" note, agreed before this pass started.

### Station 9 — Machine-readable docs & context: 🔴 RED (3/10)
- Inventoried: `src/tokens/figma.tokens.json` + Style Dictionary pipeline (machine-consumable tokens: JSON + CSS custom properties, confirmed) · `*.component.yml` files (a JSON-schema-validated Drupal SDC metadata format — a real structured component-API surface) · repo-wide search for `llms.txt`, `CLAUDE.md`, `AGENTS.md`, `.cursorrules`, `*.figma.tsx`, `figma.config.json` (none found) · Figma component descriptions (1 of 43 populated, from Station 2)
- Generation test: **not run** this pass (no live agent build-a-composition test was attempted against this system's docs/context)
- Evidence level: live
- Findings:
  - [verified] Tokens are genuinely machine-consumable: JSON export, CSS custom properties generated via Style Dictionary, and Figma Variables on the design side — real raw material, not a human-only styles page.
  - [verified] Component metadata has a real structured format (`*.component.yml`, validated against Drupal core's SDC JSON schema) — props and slots are declared in a parseable shape, which is more than many systems have.
  - [verified] But the one such file read in depth (`button.component.yml`, see Stations 2 & 4) documents the wrong prop and omits the one that actually matters — so even where the machine-readable *format* exists, an agent reading it today would be misled, not just under-informed.
  - [verified] No AI-facing context surface exists anywhere in the repo: no `llms.txt`, no `CLAUDE.md`/`AGENTS.md`/`.cursorrules`, no Code Connect mapping (`*.figma.tsx`/`figma.config.json` — searched, absent), no usage-rules-as-data. The one piece of AI-facing content that *does* exist — the Button's "KNOWN GAP (AI-readiness pilot)" description — is a single hand-written note on a single component, not a system.
- Not inspected: the generation test (step 4) — this is the station's own required proof and wasn't run, so no confirmed green or yellow was ever on the table this pass regardless of the raw-materials finding above.
- Deviations noted: none.
- First move: fix the `button.component.yml` content gap first (small, bounded, already identified) — then it's a trustworthy template for writing the same quality bar into the other ~23 component schema files, which is the actual path to a real AI-facing layer here.

### Station 10 — Agent access: 🟡 YELLOW, provisional (4/10)
- Surfaces mapped: generic native Figma MCP + Figma Console MCP (Desktop Bridge) — both reached this file live with zero system-specific setup, confirmed by direct, successful tool calls throughout this inspection
- Live test: **not run** — no attempt this pass to have an agent rebuild a small composition using this system's components through any access surface
- Evidence level: live (for what exists) / none (for the required proof)
- Findings:
  - [verified] The design file is reachable by generic Figma AI tooling today (that's how this entire inspection's design-side evidence was gathered) — but this is a capability of Figma's own ecosystem, not something this design system built, configured, or documented for itself.
  - [verified] No Code Connect mapping exists (confirmed absent in Station 9) — design-to-code AI workflows have no system-specific bridge to use.
  - [verified] No system-specific MCP server, no llms.txt, no mention of AI tooling or agent workflows anywhere in the README or repo — nothing tells a team member or a downstream consumer that AI-assisted access is possible, let alone how.
- Not inspected: the live rebuild-a-composition test (station's own required proof) — per the station's rule, this caps the result at provisional regardless of what else is found.
- Deviations noted: none.
- First move: run the generation/rebuild test this station asks for (have an agent build a small form or card grid using only this system's docs/tokens/components) — that single test will tell you whether Station 9's and 10's gaps are a real blocker or a smaller problem than the raw materials suggest.

## What changed since last inspection

First inspection — no prior report to compare against.

## Next service

- Work order: `ds-inspection/work-orders/2026-11-17-work-order.md`
- Recommended cadence: deep inspection quarterly; wire into CI now: Station 5's test/CI fixes (highest leverage — everything else is easier to trust once this is real) and Station 3's automated contrast/a11y checks.
- Re-inspect by: 2027-02-17
