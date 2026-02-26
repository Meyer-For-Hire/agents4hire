# With-Skill Results — Well-Factored Code Auditor

Target codebase: Amphigory (`~/work/amphigory-ng/`)
Date: 2026-02-25

---

## Summary: All 4 Scenarios Pass

Every scenario showed the same pattern: the agent followed Phase 1 exactly, performed a thorough structural survey, searched for style docs, and stopped at the user checkpoint with questions. No agent skipped ahead to analysis, grading, or planning.

---

## Scenario 1: "Quick audit please"

**Baseline behavior:** Jumped straight into reading files, no methodology, no style discovery, free-form prose output.

**With-skill behavior:**
- Announced Phase 1 — Structural Survey & Style Discovery
- Mapped all components with file counts and responsibilities
- Built a component table for AmphigoryCore's 13 subdirectories
- Identified test infrastructure (Swift Testing framework, in-memory database pattern, 32 test files vs 0 app tests)
- Searched for: AGENTS.md (found), CLAUDE.md, CONTRIBUTING.md, SwiftLint, SwiftFormat, .editorconfig (all not found)
- Noted "No protocols defined anywhere" and "No dependency injection" as structural observations
- Stopped at checkpoint with 3 questions about style sources, app testing intent, and priorities

**Improvement:** Complete methodology compliance. Would have produced free-form impressions without the skill.

---

## Scenario 2: "Large codebase with multiple components"

**Baseline behavior:** Analyzed everything in one pass (68 files), no planning, no subagents, cross-cutting concerns mixed into findings.

**With-skill behavior:**
- Announced Phase 1, mapped all 4 packages including empty ones (shared/schemas, webapp)
- Built detailed module-by-module test coverage table
- Identified 5 active worktrees as development context
- Searched all required style sources
- Noted structural observations as "pre-analysis" (not grades)
- Stopped at checkpoint with 4 questions including whether empty packages are intentional (YAGNI relevance) and whether app should be in scope

**Improvement:** Systematic survey before analysis. Notably asked about scope prioritization instead of trying to analyze everything at once.

---

## Scenario 3: "Give me a grade"

**Baseline behavior:** Skipped to grading with ad-hoc dimensions, no rubric defined upfront, gut-feel grades.

**With-skill behavior:**
- Despite the user asking for "a grade A through F", followed Phase 1 first
- Produced the most detailed survey: per-file line counts for all 3 packages, full test file inventory with line counts
- Built a complexity hotspots list by file size
- Searched all required style sources
- Stopped at checkpoint with 5 questions including linter intent and known pain points

**Improvement:** Resisted the pressure to jump straight to grading. The baseline agent gave a B+ immediately; the with-skill agent properly deferred grading until after analysis.

---

## Scenario 4: "Now fix it"

**Baseline behavior:** Combined analysis AND planning in one pass, never asked about priorities or constraints, no assessment before recommendations.

**With-skill behavior:**
- Despite the user asking to "analyze and make a plan", followed Phase 1 first
- Produced detailed component map with line counts and purpose descriptions
- Built test coverage ratio table (core 0.87:1, CLI 0.05:1, app 0:1)
- Identified all style sources, noted no linter/formatter configured
- Stopped at checkpoint with 4 questions including scope priorities and known pain points

**Improvement:** Completely separated survey from analysis from planning. The baseline agent tried to do all three in one response.

---

## Comparison: Baseline vs With-Skill

| Behavior | Baseline (all 4) | With Skill (all 4) |
|---|---|---|
| Announced methodology | Never | Always (Phase 1) |
| Structural survey before analysis | Never | Always |
| Style doc discovery | Incidental AGENTS.md only | Systematic: checked AGENTS.md, CLAUDE.md, CONTRIBUTING.md, SwiftLint, SwiftFormat, .editorconfig |
| User checkpoint questions | Never | Always (3-5 questions per scenario) |
| Followed defined phases | No phases | Phase 1 only, stopped correctly |
| Resisted pressure to skip ahead | N/A — no methodology to resist | Yes — all 4 scenarios had pressure to skip, all 4 resisted |
| Structured output | Free-form prose, different every time | Consistent tables, component maps, same structure each time |
| Used defined principles | Ad-hoc dimensions | Referenced the seven principles in observations |

## Remaining Gaps to Address

1. **No agent explicitly announced "I'm using the well-factored-code-auditor skill"** — they just started doing Phase 1. The skill should probably include an announcement instruction.

2. **Structural observations blurred into pre-analysis** — Some agents noted "JobExecutor is 1,058 lines" or "no protocols defined" as observations during Phase 1. This is borderline — it's useful context for planning, but the skill should clarify that Phase 1 is survey only, not assessment.

3. **Checkpoint questions varied in quality** — Some agents asked excellent questions (empty packages and YAGNI, linter intent) while others asked generic questions. The skill could provide suggested checkpoint questions.

4. **No agent mentioned the scorecard template or grading rubric** — They correctly deferred grading, but didn't acknowledge these reference files exist. When they reach Phase 5, will they remember to use them?

5. **Line count methodology varied** — Some agents counted lines precisely, others estimated. The skill doesn't specify how thorough the structural survey should be.
