# agents4hire

Specialized AI agents and skills for code quality, refactoring, and the product-development workflow.

## Skills

### well-factored-code-auditor

Systematic codebase factoring audit that assesses code quality across seven principles (Clarity, SRP, DRY, YAGNI, Testability, Test Adequacy, Consistency), produces a graded scorecard with letter grades per component and principle, and generates a prioritized refactoring plan.

**Invoke:** `/agents4hire:well-factored-code-auditor` or `/well-factored-code-auditor`

**What it does:**
- Surveys codebase structure and discovers style conventions
- Analyzes components independently using subagents
- Examines cross-cutting concerns (DRY across boundaries, dependency direction)
- Produces a scorecard at `./docs/YYYY-MM-DD-well-factored-code-scorecard.md`
- Hands off to a refactoring plan workflow

**Files:**
- `skills/well-factored-code-auditor/SKILL.md` — main skill document
- `skills/well-factored-code-auditor/grading-rubric.md` — letter grade scale and per-principle criteria
- `skills/well-factored-code-auditor/scorecard-template.md` — output template for the scorecard
- `skills/well-factored-code-auditor/plan-review-checklist.md` — post-planning review checklist for refactoring plans

### Product-development workflow

A set of skills for the M4H/PathX **product-development** workflow, built on top of [Matt Pocock's skills kit](https://github.com/mattpocock/skills). They split product definition from technical design, drive precise domain language, and turn a working conversation into a PRD, a tech spec, and BDD acceptance criteria — keeping **authoring** (writing documents) separate from the **side-effecting** issue creation, which happens only after each document passes review.

Run [`/setup-m4h-agents4hire`](skills/setup-m4h-agents4hire/SKILL.md) once per repo first — it records where documents go (`docs/agents/document-locations.md`) and the issue-tracker coordinates (`docs/agents/issue-tracker.md`) the other skills read.

The flow, roughly in order:

1. [`/grill-the-pm`](skills/grill-the-pm/SKILL.md) — relentless interview to sharpen a product owner's feature definition and build the glossary, without dragging them into architecture. The PM counterpart to Pocock's `/grill-with-docs` (which stays the architect's tool). *(user-invoked)*
2. [`/create-prd-from-convo`](skills/create-prd-from-convo/SKILL.md) — synthesize a product/UX PRD (with a BDD acceptance-criteria section) and publish it to the configured PRD location. Writes a document; creates no issues. *(user-invoked)*
3. **After PRD review:** [`/prd-to-acceptance-issues`](skills/prd-to-acceptance-issues/SKILL.md) — publish the PRD's criteria as one sub-issue per Requirement (stable criterion IDs) in the product tracker. *(user-invoked)*
4. [`/create-tech-spec-from-convo`](skills/create-tech-spec-from-convo/SKILL.md) — synthesize the technical half (seam analysis, implementation + testing decisions, and **technical acceptance criteria** by category: performance, scale, privacy, security) into a tech spec. *(user-invoked)*
5. **After tech-spec review:** Pocock's `/to-issues` creates the implementation slices, folding each technical acceptance criterion into the slice it applies to. Then `/map-product-acceptance-to-issues` records a one-way reference from each product acceptance criterion to the implementation work after which it's testable (the implementation side stays clean), flagging any criterion no work covers.

Composed disciplines *(model-invoked — reached by the above or autonomously)*:

- [`/defining-acceptance-criteria`](skills/defining-acceptance-criteria/SKILL.md) — derive BDD Given/When/Then criteria as PRD text (one Requirement per behavior). Authors criteria only; does not create issues.
- [`/sharpen-domain-language`](skills/sharpen-domain-language/SKILL.md) — build and sharpen the ubiquitous language with a product owner; sliced from Pocock's `/domain-modeling` with ADRs and code cross-referencing removed.
- [`/map-product-acceptance-to-issues`](skills/map-product-acceptance-to-issues/SKILL.md) — record a one-way reference from each product acceptance-criterion issue to the implementation work after which it's testable (nothing written to the implementation side); flag criteria no planned work covers. References existing issues; creates none.

And: [`/setup-m4h-agents4hire`](skills/setup-m4h-agents4hire/SKILL.md) configures a repo for all of the above. *(user-invoked)*

## Installation

Add the Meyer For Hire marketplace, then install the plugin:

In Claude Code:
```
/plugin marketplace add Meyer-For-Hire/m4h-marketplace
/plugin install agents4hire@m4h-marketplace
```

Or from the command line:
```bash
claude plugin marketplace add Meyer-For-Hire/m4h-marketplace
claude plugin install agents4hire@m4h-marketplace
```

## Dependencies

**Optional but recommended:** The [Superpowers](https://github.com/obra/superpowers) plugin by Jesse Vincent. The well-factored-code-auditor uses `superpowers:writing-plans` for structured analysis and refactoring plans. Without it, the auditor still works but produces inline plans instead of using the Superpowers structured planning workflow.

```
/plugin marketplace add obra/superpowers-marketplace
/plugin install superpowers@superpowers-marketplace
```

**Required for the product-development workflow:**

- [Matt Pocock's skills kit](https://github.com/mattpocock/skills) — the product-development skills compose his `/grilling`, `/grill-with-docs`, `/setup-matt-pocock-skills`, and `/to-issues` skills, and `/sharpen-domain-language` is sliced from his `/domain-modeling`.
- **MCP connectors for your configured issue tracker and document store.** The skills are system-agnostic and read their destinations from `docs/agents/`; M4H's default configuration uses the **Linear** and **Google Workspace** connectors.

## Author

Meyer For Hire Consulting, LLC

## License

MIT
