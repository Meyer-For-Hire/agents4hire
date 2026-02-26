# Grading Rubric — Well-Factored Code Auditor

## Letter Grade Scale

| Grade | Meaning | What it looks like |
|---|---|---|
| A+ | Exemplary | Could be used as a teaching example. Actively well-maintained. |
| A | Excellent | Principle consistently followed with only trivial exceptions. |
| A- | Very good | Followed well; a few minor opportunities for improvement. |
| B+ | Good | Solid adherence with notable gaps that don't cause real problems yet. |
| B | Above average | Generally follows the principle with clear areas needing attention. |
| B- | Decent | More gaps than you'd like, but intent is there and foundation is solid. |
| C+ | Adequate | Follows the principle in some areas, ignores it in others. |
| C | Mediocre | Inconsistent. Some effort visible but not systematic. Debt accumulating. |
| C- | Below average | More violations than adherence. Needs focused refactoring effort. |
| D+ | Poor | Principle rarely followed. Significant refactoring needed. |
| D | Very poor | Principle largely ignored. Systemic problems. |
| D- | Failing | Almost no evidence of this principle. Major rework required. |
| F | Absent | Principle actively violated or completely unaddressed. |

## Grading Criteria Per Principle

| Principle | A-range | B-range | C-range | D/F-range |
|---|---|---|---|---|
| Clarity of Intent | Names expressive everywhere; methods read as step lists; comments only explain "why" | Most names good; some methods need reading; occasional unclear spots | Naming inconsistent; many methods require careful reading | Names misleading or meaningless; methods opaque |
| Single Responsibility | Every method/class has one clear job; concerns well-separated | Most units have clear responsibility; some do too much | Many methods/classes mix concerns; god objects present | Responsibilities tangled; changing one thing breaks unrelated things |
| DRY | Each concept expressed once; shared utilities used consistently | Minor duplication, localized | Significant copy-paste; same logic in multiple places | Pervasive duplication; no shared utilities; shotgun surgery required |
| YAGNI | No dead code; no speculative abstractions; lean and purposeful | Small amounts unused code; minor over-engineering | Notable dead code; abstractions serving no current use case | Massive dead code; layers of abstraction for nonexistent requirements |
| Testability | Components testable in isolation; dependencies injectable; minimal mutable state | Most components testable; some hard-to-test areas | Many components hard to test without full system | Untestable without full system; deeply entangled |
| Test Adequacy | Critical paths well-tested; failures point to problems; complex methods thoroughly covered | Good happy-path coverage; some edge case gaps | Spotty coverage; complex methods undertested | Minimal or no tests; complex code completely untested |
| Consistency | Uniform style throughout; conventions followed everywhere | Mostly consistent; minor deviations | Inconsistent across components; competing conventions | No discernible conventions |

## Severity Weighting

The overall grade is a **weighted composite**, not a simple average.

High-severity findings in fundamental principles pull the grade down more than cosmetic issues:
- **Heaviest weight:** Single Responsibility, Testability — these are structural and hardest to fix later
- **Heavy weight:** DRY, Clarity of Intent — these compound over time
- **Moderate weight:** Test Adequacy, YAGNI — important but more localized in impact
- **Lighter weight:** Consistency — matters for maintainability but rarely causes bugs

## Cyclomatic Complexity Reference

Cyclomatic complexity (CC) measures the number of independent paths through a method. Use a tool when available (SwiftLint `cyclomatic_complexity`, `lizard`, `radon`, ESLint `complexity` rule). When no tool is available, estimate by counting branch points (if/else, switch cases, guard, ternary, catch blocks) and **always prefix with `~` to indicate an estimate**.

| CC | Risk Level | Interpretation |
|---|---|---|
| 1-5 | Low | Simple, easy to understand and test |
| 6-10 | Moderate | Reasonable for methods with real logic; test all paths |
| 11-15 | High | Hard to understand fully; strong candidate for decomposition |
| 16-20 | Very high | Difficult to test thoroughly; should be split |
| 21+ | Extreme | Nearly untestable; refactor immediately |

**Reporting rules:**
- If computed by a tool: report the exact number (e.g., `15`)
- If estimated by reading code: always use `~` prefix (e.g., `~15`)
- Never mix exact and estimated numbers without distinguishing them

## Calibrating to Context

Severity is relative to the codebase context:
- **Personal/hobby project:** Grade leniently on consistency and test adequacy; focus on structural principles
- **Production system:** Grade strictly across all principles; high-severity findings in testability or SRP are critical
- **Library/framework:** Grade especially strictly on API clarity, testability, and YAGNI — consumers depend on clean interfaces
- **Prototype/MVP:** Focus on whether the code is structured for evolution; forgive missing tests if structure is sound

## Overall Grade Methodology

1. Grade each principle independently per component
2. Compute per-principle grades across all components (weighted by component size/importance)
3. Compute overall grade using severity weighting above
4. Adjust for cross-cutting findings (cross-component DRY violations, architectural issues)
5. The overall grade should reflect: "If I handed this codebase to a competent developer, how quickly could they understand it, test it, and safely change it?"
