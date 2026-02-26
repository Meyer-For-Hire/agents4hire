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

## Installation

Add `agents4hire` as a Claude Code plugin:

```bash
claude plugin add agents4hire
```

## Author

Meyer For Hire

## License

MIT
