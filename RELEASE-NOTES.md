# agents4hire Release Notes

## v0.1.0 (2026-02-25)

Initial release.

### Skills

**well-factored-code-auditor** — Systematic codebase factoring audit producing a graded scorecard and refactoring plan.

- Seven-phase workflow: Structural Survey, Analysis Planning, Component Analysis (parallel subagents), Cross-Cutting Analysis, Synthesis & Scorecard, Refactoring Plan, Plan Review
- Grading rubric with letter grades (A+ through F) across seven principles: Clarity of Intent, Single Responsibility, DRY, YAGNI, Testability, Test Adequacy, Consistency
- Scorecard template with Mermaid architecture diagrams, component breakdowns, complexity hotspots, and severity summaries
- Cyclomatic complexity reference scale with `~` prefix convention for estimated values
- Plan review checklist for post-planning quality gate (coverage, specificity, risk, ordering, scope)
- Graceful degradation when Superpowers plugin is not installed
