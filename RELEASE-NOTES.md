# agents4hire Release Notes

## v0.2.0 (2026-06-25)

Product-development workflow skills for the M4H/PathX product-definition phase, built on Matt Pocock's skills kit.

### Skills

- **setup-m4h-agents4hire** — configures a repo for the workflow: writes `docs/agents/document-locations.md` and `docs/agents/issue-tracker.md` (the files the other skills read). Ships `document-locations.md` and `issue-tracker-linear.md` seed templates.
- **grill-the-pm** — relentless product-owner interview (`/grilling` + `/sharpen-domain-language`) that builds the glossary without dragging the PM into architecture. Counterpart to Pocock's `/grill-with-docs`.
- **sharpen-domain-language** — ubiquitous-language discipline sliced from `/domain-modeling`, with ADR-offering and code cross-referencing removed for the product-definition audience.
- **create-prd-from-convo** — product/UX PRD (problem, solution, user stories, UX/interaction design, BDD acceptance-criteria section) → configured PRD location; the product half of the old `/to-prd`. Writes a document; creates no issues.
- **create-tech-spec-from-convo** — seam analysis + implementation/testing decisions → configured tech-spec location; the technical half of the old `/to-prd`.
- **defining-acceptance-criteria** — derive BDD Given/When/Then criteria as PRD text (one Requirement per behavior). Authors criteria only; owns the BDD form; creates no issues.
- **prd-to-acceptance-issues** — after PRD review, publish the criteria as one product-tracker sub-issue per Requirement (stable criterion IDs), and write the IDs back into the PRD.
- **check-acceptance-coverage** — after tech-spec review (and after `/to-issues` creates the implementation slices), mint technical-acceptance issues in the engineering tracker, link slices to the product criteria they satisfy, and report coverage gaps.

### Design notes

- **Authoring is separated from issue creation.** PRD/tech-spec/criteria skills write documents and text; the two side-effecting publish skills run only **after each document passes review**. This keeps product criteria (product tracker) decoupled from engineering decomposition (engineering tracker) — they're associated by *linking*, not co-creation.
- **Honors Matt Pocock's user/model invocation divide:** side-effecting entry points are user-invoked; `defining-acceptance-criteria` and `sharpen-domain-language` are model-invoked disciplines composed by the others. No user-invoked skill reaches another.
- The discipline-bearing skills (`sharpen-domain-language`, `defining-acceptance-criteria`, `check-acceptance-coverage`) were authored RED→GREEN per `superpowers:writing-skills`; baseline / with-skill subagent evidence is under `tests/`. `check-acceptance-coverage` took one refactor to bind its coverage definition.
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
