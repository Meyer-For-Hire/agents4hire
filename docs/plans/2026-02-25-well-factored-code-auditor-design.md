# Well-Factored Code Auditor — Design Document

Date: 2026-02-25
Status: Draft
Package: agents4hire (Meyer For Hire)
Skill name: well-factored-code-auditor

## Overview

A Claude Code skill that analyzes a codebase to assess how well-factored it is, produces a graded scorecard, and generates a prioritized refactoring plan. The auditor runs as an agent within a Claude Code session, using subagents for component-level analysis and integrating with the superpowers workflow for planning and execution.

Invocable as `/agents4hire:well-factored-code-auditor` or `/well-factored-code-auditor`.

## What Is Well-Factored Code?

Well-factored code is code structured so that each piece has a clear purpose, can be understood in isolation, and can be changed safely. The term "factored" is borrowed from mathematics — just as you factor an expression into its simplest components, you factor code into units that are each doing exactly one thing.

Factoring is not just cleanup — it's a discovery process. When you extract methods and objects, you often uncover hidden abstractions that reveal deeper insight about the problem domain (TomStambaugh, C2 Wiki). When methods grow long because of shared local variables, that's the code telling you there's an object waiting to be discovered.

**The core test:** Well-factored code is easy to test. "Writing code that's easy to test... is actually just well written code!" (Mike Austin). If code is hard to test, that difficulty is a symptom of poor factoring — too many dependencies, too much mutable state, too many side effects tangled together.

### The Seven Principles

**1. Clarity of Intent**
- Code reads like prose: method bodies read like a list of steps, each step named expressively
- Names reveal purpose — variables, methods, types all communicate what and why
- Well-factored code is self-documenting: method names replace comments. By the time you finish addressing factoring issues, you usually don't need as much commenting
- Comments explain why, not what — the code itself explains the what
- Methods are short enough to understand at a glance (~60-100 lines max); longer methods are decomposed into well-named helpers

**2. Single Responsibility & Separation of Concerns**
- Each function/method does one thing well (SRP)
- Each module/class owns one domain of responsibility
- Coupling is low (components don't know each other's internals), cohesion is high (related things live together)
- Dependencies are explicit and injected, not hidden in singletons or global state
- Mutable state is minimized; side effects are isolated

**3. DRY — Don't Repeat Yourself**
- Each concept is expressed once, in one place
- Common operations live in shared utilities and are used consistently
- Duplication is a signal that an abstraction is missing
- Replace local variables with methods (Fowler): instead of `temp = someCalculation(); use(temp)`, call `use(someCalculation())` — this makes refactoring easier and the calculation often gets reused elsewhere

**4. YAGNI — You Ain't Gonna Need It**
- No speculative features or over-engineering for hypothetical future needs
- Kent Beck's "fewest elements" rule: minimize unnecessary abstractions and constructs
- Dead code is aggressively removed — unused code is worse than no code because it misleads and adds cognitive load

**5. Testability**
- Components can be tested in isolation (few dependencies, injectable collaborators)
- Minimal mutable state and side effects
- Tests serve as living documentation — they show how the API is meant to be used
- Tests make refactoring safe by catching regressions
- Tests as a living formal design: they explicitly reveal dependencies

**6. Test Adequacy**
- Coverage as a starting signal (low = red flag, but high coverage alone doesn't mean well-tested)
- For complex methods: are critical paths tested? Would failures point to the problem?
- Enough happy path and sad path tests to be confident breakage will be seen quickly and clearly

**7. Consistency**
- Naming conventions are uniform across the codebase
- Patterns are applied consistently (if you use protocol-based DI in one place, you use it everywhere)
- Code style is unified — indentation, formatting, file organization
- A consistent style streamlines adoption of best practices and helps onboard new contributors

### Complexity as a Lens

Cyclomatic complexity is not a separate principle but a signal that cuts across several principles. High cyclomatic complexity in a method is a symptom of poor single responsibility, and when combined with method length, it's a primary indicator for refactoring.

Two refactoring patterns for complex methods:
- **Tease out the complex part:** Isolate the branching logic to make it concise, easily testable, and clearly evident; factor the rest into wrapper methods according to calling signatures and validations
- **Expand overly concise code:** Trade cleverness for cognitive clarity, relying on the compiler to optimize. Requires performance testing to verify no degradation

### Important Boundaries

- **Factoring has practical limits.** Splitting a short routine in half can make it harder to understand if both halves must be read together. The goal is comprehension, not minimizing line count.
- **Context determines method length.** The ~60-100 line guideline is a practical ceiling, not a target. If a method forces you to scroll to read it, it's probably too long.
- **Performance-critical code may need different tradeoffs.** Factor first, then selectively unfactor measured hotspots. Kent Beck: Make it work, make it right, make it fast — in that order.

## Architecture: Two-Phase with Cross-Cutting Pass

The auditor uses a multi-phase approach that scales to large codebases by analyzing components independently (each in its own context window) and then synthesizing findings.

### Why not single-pass?

A single-pass analysis won't scale to large codebases — the context window fills before analysis is complete. The multi-phase approach also provides natural checkpoints for user review.

### Workflow

```
PHASE 1: Structural Survey & Style Discovery
  Agent: surveys codebase, discovers style docs
  → USER: "Here's what I found about your coding style.
     Other sources I should know about?"
  Agent: incorporates response into style reference
  ↓
PHASE 2: Analysis Planning
  Agent: maps components, prioritizes, drafts analysis plan
  → USER: "Here's my plan for analyzing the codebase.
     Any components to prioritize or skip?"
  Agent: invokes /superpowers:writing-plans for analysis plan
  → USER: reviews analysis plan
  ↓
PHASE 3: Component Analysis
  Agent: executes analysis plan via /superpowers:executing-plans
  [Subagents analyze components, produce structured findings]
  ↓
PHASE 4: Cross-Cutting Analysis
  Agent: examines cross-component concerns using component summaries
  ↓
PHASE 5: Synthesis & Scorecard
  Agent: produces ./docs/YYYY-MM-DD-well-factored-code-scorecard.md
  → USER: "Here's the scorecard. [Overall grade: B]
     What grade are you targeting?
     Areas to exclude from refactoring?
     Any constraints?"
  ↓
PHASE 6: Refactoring Plan
  Agent: invokes /superpowers:writing-plans with spec from scorecard
  → USER: reviews refactoring plan
  Agent: plan ready for /superpowers:executing-plans
```

## Phase Details

### Phase 1: Structural Survey & Style Discovery

**1a. Map the codebase:**
- Components, their boundaries (directories, modules, packages)
- Dependencies between components (imports, shared types)
- Shared code / utility libraries
- Test locations and coverage infrastructure

**1b. Style discovery — search for:**
- AGENTS.md, CLAUDE.md, CONTRIBUTING.md, README
- Linter/formatter configs (SwiftLint, SwiftFormat, ESLint, Prettier, .editorconfig, etc.)
- IDE project settings (Xcode schemes, VS Code settings, etc.)
- Any linked external style guides
- Package/build conventions (module structure, naming patterns)

**1c. Present findings to user:**
"Here's what I found about your team's preferred coding style: [summary]. Are there other sources I should consider, inside or outside the codebase?"

**1d. Incorporate user's response** into a working style reference that informs all subsequent phases.

### Phase 2: Analysis Planning

- Prioritize components by risk/complexity
- Identify which components can be analyzed in parallel vs. sequentially
- Include the style reference in subagent context
- Produce analysis plan via `/superpowers:writing-plans`
- User reviews and approves

### Phase 3: Component Analysis

Each subagent assesses one component against the seven principles.

**Per-component assessment criteria:**

| Principle | What to look for | Severity signals |
|---|---|---|
| Clarity of Intent | Method/variable naming quality; do method bodies read as step lists? Are helpers expressively named? | Methods where you have to read the body to understand the name |
| Single Responsibility | Methods doing multiple things; classes with multiple reasons to change; mixed concern levels | Long methods with multiple logical sections separated by blank lines or comments |
| DRY | Duplicated logic within the component; copy-paste patterns; similar-but-different implementations | Two or more blocks doing essentially the same thing with minor variations |
| YAGNI | Unused code paths; over-engineered abstractions; speculative generality; parameters nobody uses | Dead code, unused protocol conformances, configuration for nonexistent use cases |
| Testability | Can components be tested in isolation? Are dependencies injectable? Mutable state? Side effects? | Classes that can't be instantiated without standing up the whole system |
| Test Adequacy | Coverage as starting signal. For complex methods: are critical paths tested? Would failures localize? | Complex methods with no tests, or tests covering only the happy path |
| Complexity as signal | High cyclomatic complexity + long methods = primary refactoring targets. Flag which pattern applies | Methods where branch count and length are both high |
| Method Length | Methods exceeding ~60-100 lines; could logical sections be extracted into well-named helpers? | Methods requiring scrolling to read |
| Consistency | Does the component follow the style reference? Naming patterns, formatting, structural conventions | Inconsistencies within the component itself |

**Structured output per component:**
- Component name and scope (files analyzed)
- Strengths (what's done well)
- Findings organized by principle: description, location(s), severity (high/medium/low), suggested approach
- Complexity hotspots: methods with high cyclomatic complexity, their test adequacy
- Dependencies: what this component depends on, what depends on it

### Phase 4: Cross-Cutting Analysis

| Concern | What to look for | Why it matters |
|---|---|---|
| DRY across boundaries | Same logic in multiple components; local reimplementations of shared concepts | Highest-value DRY findings often live at component boundaries |
| Dependency direction | Does dependency flow follow intended architecture? Logic leaking upward or sideways? | Wrong-direction dependencies create coupling that prevents isolated testing and change |
| Shared utility coverage | Are shared utilities used consistently? Local helpers that should be promoted? Shared utilities nobody uses? | Underuse suggests discoverability problems or poor abstraction fit |
| Naming consistency | Same concepts with different names across components? Style reference followed uniformly? | Inconsistent names hide duplication and create confusion |
| Dead code across modules | Protocols never conformed to; public API surface nothing calls; types exported but never imported | Cross-module dead code is harder to spot than within-component dead code |
| Style consistency | Do all components follow the style reference equally? | Divergence suggests evolving standards or neglected areas |
| Abstraction level alignment | Components at consistent abstraction levels? One operating much lower than peers? | Mismatched levels make architecture harder to reason about |
| Complexity patterns | Same operations complex everywhere? Suggests missing utility or abstraction | Repeated complexity across components points to systemic gaps |

**Key question:** Is the architecture delivering on its promises? If you have a Core/CLI/App split, is Core actually the shared foundation, or are CLI and App doing their own thing?

### Phase 5: Synthesis & Scorecard

Output: `./docs/YYYY-MM-DD-well-factored-code-scorecard.md`

## Letter Grade Rubric

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

**Grading criteria per principle:**

| Principle | A-range | B-range | C-range | D/F-range |
|---|---|---|---|---|
| Clarity of Intent | Names expressive everywhere; methods read as step lists; comments only explain "why" | Most names good; some methods need reading; occasional unclear spots | Naming inconsistent; many methods require careful reading | Names misleading or meaningless; methods opaque |
| Single Responsibility | Every method/class has one clear job; concerns well-separated | Most units have clear responsibility; some do too much | Many methods/classes mix concerns; god objects present | Responsibilities tangled; changing one thing breaks unrelated things |
| DRY | Each concept expressed once; shared utilities used consistently | Minor duplication, localized | Significant copy-paste; same logic in multiple places | Pervasive duplication; no shared utilities; shotgun surgery required |
| YAGNI | No dead code; no speculative abstractions; lean and purposeful | Small amounts unused code; minor over-engineering | Notable dead code; abstractions serving no current use case | Massive dead code; layers of abstraction for nonexistent requirements |
| Testability | Components testable in isolation; dependencies injectable; minimal mutable state | Most components testable; some hard-to-test areas | Many components hard to test without full system | Untestable without full system; deeply entangled |
| Test Adequacy | Critical paths well-tested; failures point to problems; complex methods thoroughly covered | Good happy-path coverage; some edge case gaps | Spotty coverage; complex methods undertested | Minimal or no tests; complex code completely untested |
| Consistency | Uniform style throughout; conventions followed everywhere | Mostly consistent; minor deviations | Inconsistent across components; competing conventions | No discernible conventions |

Severity is relative to the codebase — a "high" in a personal project means something different than in a production system. The auditor calibrates to context.

The overall grade is a weighted composite, not a simple average — high-severity findings in fundamental principles (SRP, Testability) pull the grade down more than cosmetic issues.

## Scorecard Format

```markdown
# Well-Factored Code Scorecard
Date: YYYY-MM-DD
Scope: [full codebase / specific components]
Auditor: agents4hire:well-factored-code-auditor

## Overall Grade: [A+ through F]

## Executive Summary
[2-3 paragraphs]

## Grades by Principle
[Table: Principle | Grade | Summary]

## Grades by Component
[Matrix: Component x Principle grades]

## Style Reference
## Architecture Overview
## Strengths
## Component Assessments (per component: grade, findings table, complexity hotspots)
## Cross-Cutting Findings
## Severity Summary
## Recommended Priority Order
```

### Phase 6: Refactoring Plan Handoff

**User checkpoint before handoff:**
1. "Here's the scorecard. What overall grade are you targeting?"
2. "Are there any areas you want to exclude from the refactoring plan?"
3. "Any constraints — active development areas, upcoming rewrites, timeline pressure?"

**Spec passed to /superpowers:writing-plans:**

```markdown
# Refactoring Plan Spec

## Goal
Improve codebase factoring from overall grade [current] toward [target].

## Constraints
- Respect established style conventions (from style reference)
- All refactoring must maintain or improve test coverage
- No behavioral changes — refactoring only
- [User-specified constraints]

## Priority Items (from scorecard)
[Each with: what to fix, which component(s), which principle(s),
expected grade impact, dependencies, suggested approach]

## Style Reference
[From scorecard]

## Architecture Context
[From scorecard]
```

Output: `./docs/plans/YYYY-MM-DD-well-factored-code-refactoring-plan.md`

The plan is then executable via `/superpowers:executing-plans`.

## Future Adjunct Skills

Skills to develop after the auditor, which would enhance its capabilities:

1. **Assessing test adequacy for complex code** — Judge whether a complex method's test suite would catch breakage quickly and point to the problem. Goes beyond coverage metrics.
2. **Running and interpreting complexity tools** — Reference skill for finding, running, and interpreting complexity analysis tools for common languages (SwiftLint cyclomatic_complexity, lizard, radon, etc.).
3. **Judging individual test quality** — Assess whether a test is testing the right thing, testing it well, and would produce useful output on failure.

## References

- C2 Wiki: WellFactoredCode — factoring as discovery, self-documenting code, method length heuristics
- C2 Wiki: WellFactoredCodeLeadsToBetterOptimizations — isolation enables targeted optimization, MakeItWorkMakeItRightMakeItFast
- Mike Austin: "Well-Factored Code" — testability as the core test, mutable data/side effects/dependencies as barriers
- Liz Acosta, The New Stack: "Why Quality Code Matters" — DRY, SRP, consistency, technical debt costs
- Kent Beck: Four Rules of Simple Design — passes tests, reveals intention, no duplication, fewest elements
- Martin Fowler: Refactoring, code smells, replace temp with query
- SOLID principles as structural foundation
