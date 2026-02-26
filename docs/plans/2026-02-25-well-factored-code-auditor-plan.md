# Well-Factored Code Auditor — Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Build the `agents4hire:well-factored-code-auditor` skill — a Claude Code skill that analyzes codebase factoring quality, produces a graded scorecard, and generates a refactoring plan.

**Architecture:** This is a skill (documentation, not code). Per the writing-skills methodology, we follow TDD for documentation: write baseline pressure scenarios first, observe agent behavior without the skill, write the skill to address gaps, then close loopholes. The skill lives in a Claude Code plugin bundle (`agents4hire`) with supporting reference files.

**Tech Stack:** Markdown (SKILL.md), Claude Code plugin system (.claude-plugin/plugin.json), superpowers skill conventions.

**Design doc:** `docs/plans/2026-02-25-well-factored-code-auditor-design.md`

---

### Task 1: Set Up Repository Structure

**Files:**
- Create: `.claude-plugin/plugin.json`
- Create: `.claude-plugin/marketplace.json`
- Create: `skills/well-factored-code-auditor/` (directory only, SKILL.md comes later)
- Create: `README.md`
- Create: `LICENSE`

**Step 1: Create plugin manifest**

Create `.claude-plugin/plugin.json`:
```json
{
  "name": "agents4hire",
  "description": "Specialized AI agents for code quality, refactoring, and engineering excellence",
  "version": "0.1.0",
  "author": {
    "name": "Meyer For Hire"
  },
  "keywords": ["agents", "code-quality", "refactoring", "audit", "well-factored-code"]
}
```

**Step 2: Create marketplace manifest**

Create `.claude-plugin/marketplace.json`:
```json
{
  "name": "agents4hire",
  "description": "Specialized AI agents for code quality, refactoring, and engineering excellence",
  "owner": {
    "name": "Meyer For Hire"
  },
  "plugins": [
    {
      "name": "agents4hire",
      "description": "Specialized AI agents for code quality, refactoring, and engineering excellence",
      "version": "0.1.0",
      "source": "./",
      "author": {
        "name": "Meyer For Hire"
      }
    }
  ]
}
```

**Step 3: Create README.md**

Brief README describing the agents4hire package, its purpose, and installation instructions (modeled on superpowers README).

**Step 4: Create LICENSE**

MIT license, copyright Meyer For Hire.

**Step 5: Create skill directory**

```bash
mkdir -p skills/well-factored-code-auditor
```

**Step 6: Commit**

```bash
git add .claude-plugin/ README.md LICENSE skills/
git commit -m "chore: initialize agents4hire plugin structure"
```

---

### Task 2: Write Grading Rubric Reference File

The grading rubric is heavy reference material (~100+ lines) that should live in a separate file from the main SKILL.md, per the writing-skills guidance.

**Files:**
- Create: `skills/well-factored-code-auditor/grading-rubric.md`

**Step 1: Write the grading rubric**

This file contains:
- The letter grade scale (A+ through F) with meanings and descriptions
- Per-principle grading criteria table (what A/B/C/D/F look like for each principle)
- Guidance on severity weighting (SRP and Testability weigh more than Consistency)
- Guidance on calibrating to codebase context (personal project vs production)
- The overall grade composite methodology

Source: The "Letter Grade Rubric" and "Grading criteria per principle" sections from the design doc (`docs/plans/2026-02-25-well-factored-code-auditor-design.md`, lines 199-231).

**Step 2: Commit**

```bash
git add skills/well-factored-code-auditor/grading-rubric.md
git commit -m "docs: add grading rubric reference for well-factored-code-auditor"
```

---

### Task 3: Write Scorecard Template Reference File

The scorecard template is another heavy reference that the skill will use when producing output. Keeping it separate prevents the SKILL.md from being bloated.

**Files:**
- Create: `skills/well-factored-code-auditor/scorecard-template.md`

**Step 1: Write the scorecard template**

This file contains the exact markdown template for the scorecard output, with placeholder markers showing what goes where. Source: the "Scorecard Format" section from the design doc (lines 233-259), expanded with the full table structures from the design discussion.

Include:
- Header with date, scope, auditor fields
- Overall grade
- Executive summary placeholder
- Grades by Principle table
- Grades by Component matrix
- Style Reference section
- Architecture Overview section
- Strengths section
- Per-component assessment sections (findings table, complexity hotspots table)
- Cross-cutting findings table
- Severity summary table
- Recommended priority order section

**Step 2: Commit**

```bash
git add skills/well-factored-code-auditor/scorecard-template.md
git commit -m "docs: add scorecard template for well-factored-code-auditor"
```

---

### Task 4: RED — Write Baseline Pressure Scenarios

Per writing-skills TDD: before writing the skill, we must observe how an agent behaves WITHOUT the skill when asked to do a codebase factoring audit. This establishes what the skill needs to teach.

**Files:**
- Create: `tests/well-factored-code-auditor/baseline-scenarios.md`

**Step 1: Write 3-4 pressure scenarios**

This skill is a **technique skill**, so we test whether the agent can apply the technique correctly without explicit guidance. Scenarios to write:

**Scenario 1: "Quick audit please"**
Prompt: "Analyze this codebase and tell me how well-factored it is."
Pressure: Time pressure — will the agent skip the structural survey? Skip style discovery? Produce a shallow, unstructured response?
What to watch for: Does it follow a systematic methodology or just skim files and give impressions?

**Scenario 2: "Large codebase with multiple components"**
Prompt: "Audit the factoring quality of this project. It has packages/cli, packages/app, and packages/shared."
Pressure: Scope — will the agent try to analyze everything in one pass and run out of context? Will it consider cross-component concerns?
What to watch for: Does it plan before analyzing? Does it use subagents? Does it look at cross-cutting concerns?

**Scenario 3: "Give me a grade"**
Prompt: "Grade this codebase on code quality — A through F."
Pressure: Specificity — will the agent provide a rigorous, principle-based assessment or just give a gut feeling grade?
What to watch for: Does it define what it's grading on? Are grades justified with evidence? Does it use a consistent rubric?

**Scenario 4: "Now fix it"**
Prompt: "Here's a codebase that needs refactoring. Analyze it and make a plan to improve it."
Pressure: Jumping to action — will the agent skip the assessment and go straight to refactoring? Will it produce a report first?
What to watch for: Does it separate analysis from planning? Does it ask what the user's priorities are? Does it hand off to writing-plans?

**Step 2: Commit**

```bash
git add tests/
git commit -m "test: add baseline pressure scenarios for well-factored-code-auditor"
```

---

### Task 5: RED — Run Baseline Scenarios

Run each pressure scenario with a subagent that does NOT have the well-factored-code-auditor skill loaded. Use the Amphigory codebase as the target. Document exact behavior verbatim.

**Files:**
- Modify: `tests/well-factored-code-auditor/baseline-scenarios.md` (add results)
- OR Create: `tests/well-factored-code-auditor/baseline-results.md`

**Step 1: Run Scenario 1 with subagent**

Launch a Task subagent with the "quick audit" prompt against the Amphigory codebase. Record:
- What approach did the agent take?
- Did it survey the structure first?
- Did it look for style docs?
- What format was the output?
- What did it miss?

**Step 2: Run Scenario 2 with subagent**

Launch a Task subagent with the "large codebase" prompt. Record same observations.

**Step 3: Run Scenario 3 with subagent**

Launch a Task subagent with the "give me a grade" prompt. Record same observations.

**Step 4: Run Scenario 4 with subagent**

Launch a Task subagent with the "now fix it" prompt. Record same observations.

**Step 5: Document patterns**

Summarize across all scenarios:
- What rationalizations did the agent use?
- What steps did it consistently skip?
- What was the quality of its output?
- What specific gaps does the skill need to address?

**Step 6: Commit**

```bash
git add tests/
git commit -m "test: document baseline behavior for well-factored-code-auditor scenarios"
```

---

### Task 6: GREEN — Write the SKILL.md

Using the baseline results to understand what the agent gets wrong without guidance, write the main skill document that addresses those specific gaps.

**Files:**
- Create: `skills/well-factored-code-auditor/SKILL.md`

**Step 1: Write YAML frontmatter**

```yaml
---
name: well-factored-code-auditor
description: Use when you want to assess how well-factored a codebase is, identify refactoring opportunities, or audit code quality across DRY, YAGNI, testability, naming, and structural concerns
---
```

**Step 2: Write the skill body**

The SKILL.md must include (drawing from the design doc and adjusted based on baseline findings):

- **Overview:** What the auditor does, the core test ("well-factored code is easy to test"), the "factoring as discovery" insight
- **The Seven Principles:** Concise summary of each (Clarity, SRP, DRY, YAGNI, Testability, Test Adequacy, Consistency) with complexity as a cross-cutting lens
- **Important Boundaries:** Practical limits on factoring, context-dependent method length, performance tradeoffs
- **Workflow flowchart:** The 6-phase workflow as a dot graph, with user checkpoints clearly marked
- **Phase instructions:** What to do in each phase, with enough detail that the agent follows the methodology
  - Phase 1: Structural Survey & Style Discovery (including the user question about style sources)
  - Phase 2: Analysis Planning (invoke writing-plans)
  - Phase 3: Component Analysis (per-component criteria checklist, structured output format)
  - Phase 4: Cross-Cutting Analysis (cross-cutting concerns table)
  - Phase 5: Synthesis & Scorecard (reference grading-rubric.md, reference scorecard-template.md, output path)
  - Phase 6: Refactoring Plan Handoff (user checkpoint questions, spec format, invoke writing-plans)
- **References to supporting files:** Link to grading-rubric.md and scorecard-template.md
- **Anti-patterns:** What NOT to do (skip structural survey, analyze everything in one pass, give grades without evidence, jump to refactoring without assessment)

Keep SKILL.md under 500 words where possible (per writing-skills token efficiency guidance), using cross-references to the grading-rubric.md and scorecard-template.md for heavy reference material.

**Step 3: Commit**

```bash
git add skills/well-factored-code-auditor/SKILL.md
git commit -m "feat: add well-factored-code-auditor skill"
```

---

### Task 7: GREEN — Run Scenarios WITH Skill

Re-run the same pressure scenarios, this time with the skill loaded, to verify the agent now follows the methodology.

**Files:**
- Create: `tests/well-factored-code-auditor/with-skill-results.md`

**Step 1: Run all 4 scenarios with skill loaded**

For each scenario, launch a Task subagent that has the well-factored-code-auditor skill in its context. Record:
- Did the agent follow the 6-phase workflow?
- Did it survey the structure first?
- Did it discover style docs?
- Did it ask the user about style sources?
- Did it produce a scorecard in the correct format?
- Did it use the grading rubric correctly?
- Did it hand off to writing-plans for the refactoring plan?

**Step 2: Compare baseline vs with-skill results**

Document improvements and remaining gaps.

**Step 3: Commit**

```bash
git add tests/
git commit -m "test: document with-skill behavior for well-factored-code-auditor scenarios"
```

---

### Task 8: REFACTOR — Close Loopholes

Based on Task 7 results, identify where the agent still deviates from the intended methodology and update the skill to address those gaps.

**Files:**
- Modify: `skills/well-factored-code-auditor/SKILL.md`
- Possibly modify: `skills/well-factored-code-auditor/grading-rubric.md`
- Possibly modify: `skills/well-factored-code-auditor/scorecard-template.md`

**Step 1: Identify remaining gaps**

Review with-skill results for:
- Rationalizations the agent used to skip steps
- Phases it abbreviated or combined
- Places where output format deviated from the template
- Grading inconsistencies

**Step 2: Add explicit counters**

For each gap:
- Add anti-rationalization language to SKILL.md if it's a discipline issue
- Add more specific instructions if it's an instruction gap
- Add red flags list if the agent is finding excuses to shortcut

**Step 3: Re-run most problematic scenario**

Pick the scenario where the agent deviated most and re-run to verify the fix.

**Step 4: Commit**

```bash
git add skills/ tests/
git commit -m "refactor: close loopholes in well-factored-code-auditor skill"
```

---

### Task 9: Final Verification and Cleanup

**Files:**
- Verify all files in `skills/well-factored-code-auditor/`
- Update: `README.md` with skill documentation

**Step 1: Verify skill file structure**

```
skills/well-factored-code-auditor/
├── SKILL.md
├── grading-rubric.md
└── scorecard-template.md
```

**Step 2: Word count check**

```bash
wc -w skills/well-factored-code-auditor/SKILL.md
```

Target: under 500 words for SKILL.md (heavy reference is in supporting files).

**Step 3: Verify frontmatter**

- `name` field: only letters, numbers, hyphens
- `description` field: starts with "Use when...", under 500 chars, no workflow summary
- Max 1024 chars total frontmatter

**Step 4: Update README**

Add skill documentation to README.md describing the well-factored-code-auditor, what it does, and how to invoke it.

**Step 5: Final commit**

```bash
git add .
git commit -m "docs: finalize well-factored-code-auditor skill and update README"
```

---

### Task 10: Live Test Against Amphigory

Run the full auditor against the Amphigory codebase as a real-world validation. This is not a pressure scenario — it's a genuine use of the skill to verify it works end-to-end.

**Step 1: Invoke the skill**

In a Claude Code session with the agents4hire plugin loaded, invoke:
`/well-factored-code-auditor` against `~/work/amphigory-ng/`

**Step 2: Walk through all 6 phases**

Verify each phase produces the expected output and user checkpoints work correctly.

**Step 3: Review the scorecard**

Does it match the template? Are grades justified? Are findings actionable?

**Step 4: Review the refactoring plan handoff**

Does it correctly invoke writing-plans? Does the spec contain everything needed?

**Step 5: Document any issues and fix**

If issues are found, loop back to Task 8 pattern (fix SKILL.md, re-test).

**Step 6: Final commit if changes were needed**

```bash
git add .
git commit -m "fix: address issues found in live test against Amphigory"
```
