---
name: well-factored-code-auditor
description: Use when assessing how well-factored a codebase is, identifying refactoring opportunities, auditing code quality across clarity, SRP, DRY, YAGNI, testability, test adequacy, and consistency, or when asked to grade or score code quality
---

# Well-Factored Code Auditor

## Overview

**Announce at start:** "I'm using the well-factored-code-auditor skill to audit this codebase."

Systematic codebase factoring audit producing a graded scorecard and refactoring plan.

**Core test:** Well-factored code is easy to test. Hard-to-test code is a symptom of poor factoring.

**Factoring is discovery.** Extracting methods and objects uncovers hidden abstractions about the problem domain.

## The Seven Principles

1. **Clarity of Intent** — names reveal purpose; methods read as step lists
2. **Single Responsibility** — each unit does one thing; concerns separated
3. **DRY** — each concept expressed once; duplication signals missing abstraction
4. **YAGNI** — no speculative features; dead code removed
5. **Testability** — testable in isolation; dependencies injectable
6. **Test Adequacy** — critical paths tested; failures localize problems
7. **Consistency** — conventions uniform across the codebase

**Complexity** is a cross-cutting signal, not a separate principle. High cyclomatic complexity + long methods = primary refactoring targets.

## Workflow

```dot
digraph audit {
  rankdir=TB;
  node [shape=box];
  P1 [label="Phase 1: Structural Survey\n& Style Discovery"];
  U1 [label="USER: Other style sources?" shape=diamond];
  P2 [label="Phase 2: Analysis Planning\n(invoke writing-plans)"];
  U2 [label="USER: Reviews plan" shape=diamond];
  P3 [label="Phase 3: Component Analysis\n(subagents per component)"];
  P4 [label="Phase 4: Cross-Cutting Analysis"];
  P5 [label="Phase 5: Synthesis & Scorecard"];
  U3 [label="USER: Target grade?\nExclusions? Constraints?" shape=diamond];
  P6 [label="Phase 6: Refactoring Plan\n(invoke writing-plans)"];
  P7 [label="Phase 7: Plan Review\n(plan-review-checklist.md)"];
  P1 -> U1 -> P2 -> U2 -> P3 -> P4 -> P5 -> U3 -> P6 -> P7;
}
```

**Phase 1 — Structural Survey & Style Discovery:** Map components, boundaries, dependencies, test infrastructure (count tests, not test lines; include coverage percentage if tooling is available). Search for AGENTS.md, CLAUDE.md, CONTRIBUTING.md, linter/formatter configs (.swiftlint.yml, .eslintrc, .prettierrc, .editorconfig), IDE settings. **Survey only — do not assess or grade.** Present findings and ask: "Are there additional style sources I should consider? Any components to prioritize or exclude?"

**Phase 2 — Analysis Planning:** Prioritize components by risk/complexity. Produce analysis plan via `superpowers:writing-plans`. User reviews.

**Phase 3 — Component Analysis:** Dispatch subagents — one per component. Each assesses against the seven principles using `grading-rubric.md`. Output: strengths, findings by principle (with severity), complexity hotspots, dependencies.

**Phase 4 — Cross-Cutting Analysis:** Using component summaries, examine: DRY across boundaries, dependency direction, shared utility coverage, naming consistency, dead code across modules.

**Phase 5 — Synthesis & Scorecard:** Read `grading-rubric.md` for criteria and `scorecard-template.md` for output format. Grade each principle per component, then compute overall. Write `./docs/YYYY-MM-DD-well-factored-code-scorecard.md`. Present to user: "What grade are you targeting? Are there areas you'd like to exclude from refactoring? Any other constraints you want me to keep in mind?"

**Phase 6 — Refactoring Plan Handoff:** From scorecard findings, user's target grade, and constraints, produce a spec and invoke `superpowers:writing-plans`.

**Phase 7 — Plan Review:** Read `plan-review-checklist.md` and review the plan against the scorecard. Run this phase when the plan has 15+ tasks, touches components graded C or below, involves god-object extraction, or modifies user-flagged fragile code. If the review finds gaps, fix the plan and re-commit before handing off for execution.

## Superpowers Dependency

This skill builds on the excellent [Superpowers](https://github.com/obra/superpowers) plugin by Jesse Vincent, whose disciplined approach to skill-driven AI workflows is our inspiration. We're grateful for that foundation, and to Jesse and the community that maintains and improves Superpowers.

**At the start of the audit, check if `superpowers` skills are available** by looking for `superpowers:writing-plans` in the available skills list. **Announce the result to the user.**

**If Superpowers IS installed:** Tell the user: "I see you have Superpowers installed — excellent. The Well-Factored Code Auditor was built to work with Superpowers' structured planning workflows, so I'll use `writing-plans` for the analysis and refactoring plans." Then use `superpowers:writing-plans` in Phases 2 and 6 as specified. Use `superpowers:executing-plans` for Phase 3 if the analysis plan warrants it.

**If Superpowers is NOT installed:** Tell the user:
"You don't seem to have Superpowers installed. The Well Factored Code Auditor will still do its job, but might not do it as well as if you give it Superpowers."

Then explain:
- Phases 1, 3, 4, and 5 work fully without superpowers
- Phase 2 (Analysis Planning) will produce a plan inline instead of using the structured writing-plans workflow — still functional but less rigorous
- Phase 6 (Refactoring Plan) will produce a prioritized list inline instead of a formal plan document — actionable but less structured
- Offer: "The superpowers plugin would improve the planning phases. Would you like instructions for installing it?"

**Install instructions (provide if asked):**
[You'll find the official install instructions here.](https://github.com/obra/superpowers#claude-code-via-plugin-marketplace). 

Open Claude Code, then paste these commands:
```
/plugin marketplace add obra/superpowers-marketplace
/plugin install superpowers@superpowers-marketplace
```

You can also do this from a command line:
```
claude plugin marketplace add obra/superpowers-marketplace
claude plugin install superpowers@superpowers-marketplace
```

## Boundaries

- Splitting a short routine in half can reduce comprehension — factoring has practical limits
- Method length (~60-100 lines) is a ceiling, not a target
- Performance-critical code: make it work, make it right, make it fast — in that order

## Red Flags — STOP

- Skipping Phase 1 (structural survey) to "save time"
- Analyzing all components in one context window instead of using subagents
- Giving grades without citing evidence from specific files
- Combining analysis and refactoring planning into one step
- Producing free-form prose instead of following the scorecard template
- Skipping user checkpoints

**All of these mean: Go back to the phase you skipped.**

## References

- `grading-rubric.md` — letter grade scale, per-principle criteria, severity weighting, context calibration
- `scorecard-template.md` — exact output template with all required sections
- `plan-review-checklist.md` — post-planning review checklist for coverage, specificity, risk, ordering, and scope
