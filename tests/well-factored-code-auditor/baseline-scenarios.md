# Baseline Pressure Scenarios — Well-Factored Code Auditor

These scenarios test how an agent behaves WITHOUT the well-factored-code-auditor skill when asked to assess codebase factoring quality. This establishes what gaps the skill needs to address.

**Target codebase:** Amphigory (`~/work/amphigory-ng/`)
**Skill type:** Technique skill — tests whether the agent can apply the technique correctly without explicit guidance.

---

## Scenario 1: "Quick audit please"

**Prompt:** "Analyze this codebase and tell me how well-factored it is."

**Pressure:** Time pressure — will the agent skip the structural survey? Skip style discovery? Produce a shallow, unstructured response?

**What to watch for:**
- Does it follow a systematic methodology or just skim files and give impressions?
- Does it survey the full structure before diving into files?
- Does it look for style docs (CLAUDE.md, linter configs, etc.)?
- Does it produce structured output or free-form prose?
- Does it define what "well-factored" means before assessing?

**Baseline result:** _(to be filled after running)_

---

## Scenario 2: "Large codebase with multiple components"

**Prompt:** "Audit the factoring quality of this project. It has multiple packages and components."

**Pressure:** Scope — will the agent try to analyze everything in one pass and run out of context? Will it consider cross-component concerns?

**What to watch for:**
- Does it plan before analyzing?
- Does it use subagents to parallelize component analysis?
- Does it look at cross-cutting concerns (DRY across boundaries, dependency direction)?
- Does it get overwhelmed and produce a shallow analysis?
- Does it prioritize which components to examine?

**Baseline result:** _(to be filled after running)_

---

## Scenario 3: "Give me a grade"

**Prompt:** "Grade this codebase on code quality — A through F."

**Pressure:** Specificity — will the agent provide a rigorous, principle-based assessment or just give a gut feeling grade?

**What to watch for:**
- Does it define what it's grading on?
- Are grades justified with evidence?
- Does it use a consistent rubric?
- Does it grade per-principle or just give one overall grade?
- Does it distinguish between severity levels of findings?

**Baseline result:** _(to be filled after running)_

---

## Scenario 4: "Now fix it"

**Prompt:** "Here's a codebase that needs refactoring. Analyze it and make a plan to improve it."

**Pressure:** Jumping to action — will the agent skip the assessment and go straight to refactoring? Will it produce a report first?

**What to watch for:**
- Does it separate analysis from planning?
- Does it ask what the user's priorities are?
- Does it produce a structured assessment before proposing changes?
- Does it hand off to a planning workflow or just start listing fixes?
- Does it consider constraints (active development, test coverage, timeline)?

**Baseline result:** _(to be filled after running)_

---

## Cross-Scenario Patterns

_(to be filled after running all scenarios)_

- **Rationalizations used:**
- **Steps consistently skipped:**
- **Output quality:**
- **Specific gaps the skill needs to address:**
