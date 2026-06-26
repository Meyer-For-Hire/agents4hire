# With-Skill Results (GREEN) — Map Product Acceptance to Issues

Run via fresh `general-purpose` subagents given the skill, instructed not to explore any repo. 2026-06-26.

## Both reps converged on the gap

| Check | Rep 1 | Rep 2 |
| --- | --- | --- |
| PATHX-15 (revoke, unplanned) | **GAP — no clean anchor**, no link applied; "topical relatedness, not coverage… recommend a new slice" | **GAP**, no link applied; "completing M2 would not make PATHX-15's scenario pass end-to-end… topical relatedness, not coverage" |
| PATHX-14 (revoked half) | **partial**, anchored to M2 conditional on the revoke gap | flagged the revoke dependency |
| PATHX-11 / PATHX-13 | anchored to M1 | anchored to M1 |
| PATHX-12 | anchored to M2 (enforcement lands in ENG-33) | anchored to M2 |
| In scope (link only; no issue creation; tech spec untouched) | yes | yes |
| Coverage report with counts | yes (5 total, 1 gap) | yes (5 total, 1 gap) |

Where baseline-1 mapped PATHX-15 onto a topically-adjacent milestone and reported "no orphans," both with-skill reps refused the false anchor, left it unlinked, and reported it as the one gap — with counts.

## Verdict

GREEN, no refactor needed. The coverage-definition lever (covered = scenarios pass end-to-end against a running instance; topical relatedness ≠ coverage) carries over from the predecessor `check-acceptance-coverage` cycle and binds the same way at milestone granularity. The skill also held its new scope boundaries: link-only, no issue creation, no tech-spec reading.
