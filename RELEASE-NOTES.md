# agents4hire Release Notes

## v0.2.0 (2026-06-26)

Product-development workflow skills for the M4H/PathX product-definition phase, built on Matt Pocock's skills kit.

### Skills

- **setup-m4h-agents4hire** — configures a repo for the workflow: writes `docs/agents/document-locations.md` and `docs/agents/issue-tracker.md` (the files the other skills read). Ships `document-locations.md` and `issue-tracker-linear.md` seed templates.
- **grill-the-pm** — relentless product-owner interview (`/grilling` + `/sharpen-domain-language`) that builds the glossary without dragging the PM into architecture. Counterpart to Pocock's `/grill-with-docs`.
- **sharpen-domain-language** — ubiquitous-language discipline sliced from `/domain-modeling`, with ADR-offering and code cross-referencing removed for the product-definition audience.
- **create-prd-from-convo** — product/UX PRD (problem, solution, user stories, UX/interaction design, BDD acceptance-criteria section) → configured PRD location; the product half of the old `/to-prd`. Writes a document; creates no issues.
- **create-tech-spec-from-convo** — seam analysis + implementation/testing decisions + technical acceptance criteria (by category: performance, scale, privacy, security) → configured tech-spec location; the technical half of the old `/to-prd`. Technical acceptance criteria are folded into the implementation slices by `/to-issues`, not filed separately.
- **defining-acceptance-criteria** — derive BDD Given/When/Then criteria as PRD text (one Requirement per behavior). Authors criteria only; owns the BDD form; creates no issues.
- **prd-to-acceptance-issues** — after PRD review, publish the criteria as one product-tracker sub-issue per Requirement (stable criterion IDs), and write the IDs back into the PRD.
- **map-product-acceptance-to-issues** — after the implementation work is planned, link each product acceptance-criterion issue to the epic/milestone after which it's testable, and flag any criterion no planned work covers. Links existing issues; creates none. (Model-invoked.)

### Design notes

- **Authoring is separated from issue creation.** PRD/tech-spec/criteria skills write documents and text; issue creation runs only **after each document passes review**. Product acceptance criteria (product tracker) stay decoupled from engineering decomposition (engineering tracker) — associated afterward by *linking* (`/map-product-acceptance-to-issues`), not co-creation. Technical acceptance criteria live in the tech spec and ride along in the implementation slices.
- **Honors Matt Pocock's user/model invocation divide:** document/issue entry points are user-invoked; `defining-acceptance-criteria`, `sharpen-domain-language`, and `map-product-acceptance-to-issues` are model-invoked disciplines. No user-invoked skill reaches another.
- The discipline-bearing skills (`sharpen-domain-language`, `defining-acceptance-criteria`, `map-product-acceptance-to-issues`) were authored RED→GREEN per `superpowers:writing-skills`; baseline / with-skill subagent evidence is under `tests/`. The coverage-definition lever (covered = scenarios pass end-to-end, not topical relatedness) was the key binding for `map-product-acceptance-to-issues`.
- Destinations are configuration, not hardcoded — the skills read them from `docs/agents/`; M4H's defaults are Google Docs (documents) and Linear (issues).
- Depends on Pocock's skills kit (incl. `/to-issues`) and the Linear + Google Workspace MCP connectors.

## v0.1.0 (2026-02-25)

Initial release.

### Skills

**well-factored-code-auditor** — Systematic codebase factoring audit producing a graded scorecard and refactoring plan.

- Seven-phase workflow: Structural Survey, Analysis Planning, Component Analysis (parallel subagents), Cross-Cutting Analysis, Synthesis & Scorecard, Refactoring Plan, Plan Review
- Grading rubric with letter grades (A+ through F) across seven principles: Clarity of Intent, Single Responsibility, DRY, YAGNI, Testability, Test Adequacy, Consistency
- Scorecard template with Mermaid architecture diagrams, component breakdowns, complexity hotspots, and severity summaries
- Cyclomatic complexity reference scale with `~` prefix convention for estimated values
- Plan review checklist for post-planning quality gate (coverage, specificity, risk, ordering, scope)
- Graceful degradation when Superpowers plugin is not installed. We hope no one ever tests that, because who doesn't want Superpowers?
