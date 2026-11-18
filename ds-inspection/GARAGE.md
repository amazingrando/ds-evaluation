# GARAGE.md — Emulsify UI Kit • Beta (Community)
_Checked in: 2026-11-17 · Re-confirm at next inspection_

## Vehicle
- System: **Emulsify UI Kit • Beta (Community)**, a public starter design system from Four Kitchens (Drupal-oriented, built on the Emulsify framework). This checkout (`/Users/oest/Sites/ds-evaluation`) is explicitly a sandbox for evaluating design-system inspection skills against a sample system, not a production team's live repo.
- Team: unknown from repo evidence alone — README credits multiple Four Kitchens contributors (Randy Oest, Brian Lewis, Mariana Núñez, +more). No in-repo org chart or ownership doc.
- Consumers: distributed publicly as a Figma Community file + npm-installable Drupal component library (via `@emulsify/cli`); real consumer count unknown — this is a *starter kit*, not a single product's system.
- Age & mileage: v1.1.0 per `package.json`; single flattened git history in this checkout (3 commits, "first" + 2 setup commits) — not representative of the upstream project's real commit history.
- Why now: running the `ds-inspection` skill as an evaluation of the skill itself against a real-shaped sample system.

## Assets
- **Design library**: Figma, file "Emulsify UI Kit • Beta (Community)" (`muDcrGCtnJUn2tTyp7LkcU`), distributed as a public Community file. 43 components/sets, 299 variables across 28 collections (global → semantic → per-component tiers), 0 text/color styles (typography and color are modeled as variables instead of styles).
- **Code library**: Drupal Single Directory Components (Twig + SCSS + YAML schema + Storybook stories), source in `src/components/`, `src/foundation/`, `src/layout/`, `src/tokens/`; compiled output in gitignored `components/`. Distributed via `@emulsify/cli` and the `emulsify-ds/compound` git repo.
- **Docs**: Storybook (local `npm run develop` → localhost:6006; published build via GitHub Pages workflow). No separate docs site.
- **Process ephemera**: GitHub Actions in `.github/workflows/` (tests, release, token sync, GH Pages deploy, contributor tracking); a PR template; semantic-release + commitlint configured. No CONTRIBUTING.md, no CHANGELOG.md at repo root (README's "Contributing" section reads "To come. TKTK").
- **AI surface**: none found — no llms.txt, no CLAUDE.md/AGENTS.md, no Figma Code Connect (`*.figma.tsx`/`figma.config.json`), no MCP server of its own. One Figma component (`button`) carries a hand-written "KNOWN GAP (AI-readiness pilot)" note in its description, dated 2026-11-13 — evidence someone has started probing this exact question.

## Evidence access map
| Asset | Access | Verified how |
|---|---|---|
| Design library | **live** — both bridges | Native Figma MCP (`get_metadata`, `get_screenshot`) returned real content; Figma Console MCP (Desktop Bridge, port 9224, probed) returned the full file: `figma_get_design_system_summary`, `figma_search_components`, `figma_get_variables`, `figma_get_styles`, `figma_lint_design`, `figma_audit_design_system[_report]`, `figma_get_component` all returned live data, not cache misses. |
| Code library | **live** | Full repo read: component source (Twig/SCSS/YAML/stories) for button, input, checkbox, radio, select; token pipeline files (`src/tokens/figma.tokens.json`, `tokensTransform.mjs`); `package.json` scripts. |
| Docs | **live** (source only) | Storybook config and stories read from repo; did not launch `npm run develop` / render the live Storybook UI. |
| Process | **live** | `.github/workflows/*.yml` read directly; PR template read; searched root for CONTRIBUTING/CHANGELOG (absent). |

## Known symptoms
- None reported by a human — this is a self-directed inspection of a sample/starter system, not a team reporting a complaint.
- One symptom the system itself reports: the Figma `button` component's own description flags a design↔code gap (color-scheme variants exist in code but not in the Figma component).

## Probable greens
- Token architecture *looks* mature on paper: 28 collections, global→semantic→component tiers, a real build pipeline (Figma Tokens export → `token-transformer` → Style Dictionary → SCSS) wired into CI (`create-tokens.yml`).
- Component code uses real semantic HTML (`<button>`, `<input type="checkbox">` + `<label for>`) rather than div/ARIA-soup reimplementations.

## Intentional deviations
- None declared in writing beyond the one seeded "KNOWN GAP" note on the button component — treated as a genuine open finding, not a resolved deviation, since it reads as flagged-but-unfixed rather than accepted-and-documented.

## Scope & frame
- Stations this pass: **all 10**.
- Scoring frame: **public starter/community kit**, not a staffed internal platform team — governance, adoption-tracking, and agent-access stations are graded against what a maintained open-source starter kit should have, not against an enterprise platform org.
- Out of scope: live Storybook rendering/interaction testing (not launched), consumer-repo adoption scanning (no consumer repos available), issue tracker health (no access to the upstream `emulsify-ds` GitHub issues from this checkout).
