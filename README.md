# agents4hire

Specialized AI agents for code quality, refactoring, and engineering excellence.

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

## Author

Meyer For Hire Consulting, LLC

## License

MIT
