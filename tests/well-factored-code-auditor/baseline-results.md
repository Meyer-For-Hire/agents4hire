# Baseline Results — Well-Factored Code Auditor

Target codebase: Amphigory (`~/work/amphigory-ng/`)
Date: 2026-02-25

---

## Scenario 1: "Quick audit please"

**Prompt:** "Analyze this codebase and tell me how well-factored it is."

**Agent approach:**
- Immediately started reading files — no methodology announcement, no structural survey phase
- Found and read AGENTS.md (incidentally, not as a style discovery step)
- Explored directory structure by reading Package.swift files and browsing directories
- Read approximately 38 files across the codebase in a single pass
- Produced a free-form prose response organized as Strengths/Weaknesses/Summary

**What it did well:**
- Identified the core library extraction as the key architectural strength
- Found concrete DRY violations (resolveUniquePath, formatting helpers)
- Identified god objects (Database.swift, JobExecutor.swift)
- Provided file paths and line numbers for findings
- Recognized the reconciliation plan/apply pattern as well-designed

**What it missed or skipped:**
- No systematic methodology — just read files and formed impressions
- No style discovery — didn't look for linter configs, .editorconfig, SwiftLint, SwiftFormat
- No definition of "well-factored" before assessing
- No grading rubric or structured scoring
- No cross-cutting analysis as a distinct phase
- No user interaction — never asked the user anything
- Output format was free-form, not a reusable scorecard

---

## Scenario 2: "Large codebase with multiple components"

**Prompt:** "Audit the factoring quality of this project. It has multiple packages and components."

**Agent approach:**
- Started by reading directory structure and Package.swift files
- Read approximately 68 files in a single pass (most thorough of all scenarios)
- Analyzed each component but in a serial, single-context manner — no subagents
- Produced a Strengths/Issues/Summary format with a rating table

**What it did well:**
- Most thorough analysis of all scenarios — read the most files
- Identified 10 specific issues with severity ratings (CRITICAL/SIGNIFICANT/MODERATE/MINOR)
- Found cross-component DRY violations (formatting helpers across 5+ CLI commands)
- Compared CLI and app implementations to find duplicated orchestration logic
- Provided a summary rating table by aspect

**What it missed or skipped:**
- No planning phase — dove straight into reading files
- No subagent usage — analyzed everything in one context window
- No style discovery beyond AGENTS.md
- No formal grading rubric — used ad-hoc severity labels
- Cross-cutting analysis was mixed into the findings list, not a separate phase
- No user interaction — never asked about priorities or which components matter most
- Rating table used informal labels ("Excellent", "Good", "Poor") not letter grades

---

## Scenario 3: "Give me a grade"

**Prompt:** "Grade this codebase on code quality — A through F."

**Agent approach:**
- Read approximately 35 files
- Produced an overview, then Strengths and Weaknesses sections, then a per-dimension grade table
- Gave an overall grade of B+

**What it did well:**
- Only scenario that actually produced per-dimension letter grades
- Grades were justified with evidence and specific file/line references
- Covered 9 dimensions in the grade table
- Distinguished between core library test coverage (A-) and app test coverage (D)
- Overall grade felt reasoned and calibrated

**What it missed or skipped:**
- No rubric defined before grading — grades were gut-feel with post-hoc justification
- Dimensions were ad-hoc (invented on the spot), not from a defined framework
- No per-component breakdown — graded the codebase as a whole
- No style discovery — didn't check for project style conventions
- No cross-cutting analysis phase
- No user interaction
- Output was free-form prose, not a reusable scorecard template

---

## Scenario 4: "Now fix it"

**Prompt:** "Here's a codebase that needs refactoring. Analyze it and make a plan to improve it."

**Agent approach:**
- Read approximately 37 files
- Produced combined analysis AND refactoring plan in a single response
- Listed 10 issues with recommendations, then a "Refactoring Priority Plan" at the end

**What it did well:**
- Found the same core issues consistently (Database god object, JobExecutor, DRY violations)
- Provided concrete recommendations for each issue
- Included a prioritized ordering of refactoring work
- Recognized the need for protocol abstractions for testability

**What it missed or skipped:**
- Did NOT separate analysis from planning — combined both in one pass
- Never asked the user about priorities, constraints, or target quality level
- No style discovery
- No grading or scoring — just issue descriptions
- No handoff to a planning workflow — produced an informal list, not a structured plan
- No consideration of constraints (active development, upcoming rewrites, timeline)
- Jumped straight to "fix it" mode without establishing a baseline assessment

---

## Cross-Scenario Patterns

### Rationalizations observed
The agent never explicitly rationalized skipping steps — it simply didn't know the steps existed. Without a methodology to follow, it defaulted to "read files and form impressions." This is the core problem: the agent has no framework for what a systematic factoring audit looks like.

### Steps consistently skipped (across ALL 4 scenarios)
1. **No methodology announcement** — never stated an approach before starting
2. **No structural survey phase** — jumped straight into reading files
3. **No style discovery** — never looked for linter configs, .editorconfig, SwiftFormat, SwiftLint, or project style guides (beyond incidentally finding AGENTS.md)
4. **No defined rubric** — grading criteria were invented ad-hoc or absent entirely
5. **No user interaction** — never asked the user about priorities, style sources, constraints, or target quality
6. **No subagent usage** — analyzed everything in a single context window
7. **No cross-cutting analysis phase** — cross-component concerns were mixed into general findings
8. **No standardized output format** — every response was different free-form prose
9. **No separation of analysis from planning** — (Scenario 4) combined assessment and recommendations

### Output quality
- **Content quality: Good.** The agent consistently identified the same core issues (Database god object, JobExecutor monolith, DRY violations in formatting helpers and resolveUniquePath, duplicated rip/transcode orchestration). Findings were concrete with file/line references.
- **Format quality: Poor.** No consistent structure across scenarios. No reusable scorecard. No template adherence.
- **Rigor: Moderate.** Grades (when given) felt reasonable but lacked a defined rubric. No severity weighting. No per-component breakdown.
- **Completeness: Moderate.** Covered the main issues but missed style consistency analysis, didn't assess testability systematically, and didn't examine test quality (only test existence).

### Specific gaps the skill needs to address
1. **Enforce a multi-phase workflow** with explicit phases that cannot be skipped
2. **Require structural survey** before reading any source files
3. **Require style discovery** — must look for linter configs, style guides, CLAUDE.md conventions
4. **Require user interaction** at defined checkpoints (style sources, analysis plan, priorities)
5. **Define the grading rubric upfront** — the seven principles, not ad-hoc dimensions
6. **Mandate subagent usage** for component-level analysis to manage context
7. **Separate cross-cutting analysis** as its own explicit phase
8. **Enforce the scorecard template** — structured, consistent output format
9. **Separate analysis from planning** — scorecard first, then user checkpoint, then handoff to writing-plans
10. **Include anti-patterns** — explicitly tell the agent what NOT to do (skip survey, analyze everything in one pass, give grades without evidence, jump to refactoring)
