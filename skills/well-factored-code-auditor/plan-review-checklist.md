# Plan Review Checklist

Review a refactoring plan against its source scorecard before handing off for execution. This catches gaps, underspecification, and missing safety measures while the plan is cheap to fix.

## When to Run

Run this review when **any** of these are true:
- Plan has 15+ tasks
- Plan touches components graded C or below
- Plan involves extracting from god objects (500+ lines) or splitting orchestration logic
- Plan modifies code the user flagged as fragile or hard-won

If none apply, skip the review — the plan is simple enough that `executing-plans` Step 1 will catch any issues.

## The Checklist

### 1. Coverage: Every High-Severity Finding Has a Task

Cross-reference the scorecard's findings tables against the plan's tasks.

- [ ] Every **high-severity** finding from the scorecard is addressed by at least one task
- [ ] Every **medium-severity** finding is either addressed or explicitly marked as out of scope with rationale
- [ ] Low-severity findings may be deferred — no action required

**Output:** List any high/medium findings with no corresponding task. For each gap, recommend: add a task, or document why it's intentionally excluded.

### 2. Specificity: Complex Tasks Are Fully Specified

Tasks involving mechanical changes (rename, delete, replace call sites) can be brief. Tasks involving architectural extraction need more detail.

- [ ] Tasks extracting logic from methods 100+ lines have **exact source line ranges** to extract
- [ ] Tasks creating new types have **complete type signatures** (public API, not just `...` placeholders)
- [ ] Tasks with test code include **concrete assertions** (`#expect(result == expected)`), not placeholder bodies (`{ ... }`)
- [ ] No task says "same pattern as Task N" for the most complex step — spell it out

**Output:** List any underspecified tasks with what's missing. Provide the missing detail inline or flag it for the plan author.

### 3. Risk: Fragile Areas Have Safety Measures

- [ ] User-flagged constraints appear in **both** the plan header and the relevant task(s)
- [ ] Tasks modifying shared infrastructure (runners, DB layer, progress streaming) include rollback guidance: "if X breaks, check Y"
- [ ] God-object splits have a verification step **immediately after** the split, not deferred to end
- [ ] Any task that changes public API has a step to update all call sites in the same task (not a later task)

**Output:** List any risky tasks missing safety measures. Recommend specific additions.

### 4. Ordering: Dependencies Flow Correctly

- [ ] Tests for new types are written **before or alongside** the type, not in a separate late phase (unless the type is extracted from existing tested code)
- [ ] Shared utilities are created before tasks that consume them
- [ ] No task depends on the output of a later task
- [ ] Manual verification checkpoints appear after the riskiest phase, not just at the end

**Output:** List any ordering issues. Recommend task reordering if needed.

### 5. Scope: Plan Matches the Target Grade

- [ ] The plan's tasks, if all completed successfully, would plausibly achieve the target grade
- [ ] No tasks are included that don't contribute to reaching the target (YAGNI for plans)
- [ ] The plan doesn't over-reach — if the target is B-, don't include tasks that only matter for A-range

**Output:** Note any scope mismatches — tasks that won't move the needle, or missing tasks that would.

## How to Report

After running the checklist, present findings as:

```
## Plan Review

**Verdict: [Ready / Needs Revision]**

### Gaps Found
[Numbered list of issues, grouped by checklist section]

### Recommended Changes
[Specific additions, reorderings, or clarifications to make]

### Items That Passed
[Brief summary of what looks good — builds confidence in the plan]
```

If verdict is **Needs Revision**, make the changes to the plan and re-commit before handing off for execution. If verdict is **Ready**, proceed to execution handoff.
