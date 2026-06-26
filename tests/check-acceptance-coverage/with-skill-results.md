# With-Skill Results (GREEN + REFACTOR) — Check Acceptance Coverage

Run via fresh `general-purpose` subagents given the skill, instructed not to explore any repo. 2026-06-25.

## Pre-refactor (skill without the coverage definition)

The skill listed the required orphan buckets, but coverage *judgment* still varied: rep 1 flagged PATHX-15 as a gap; **rep 2 mapped PATHX-15 → ENG-33 and declared "all 5 covered, no gaps."** Listing-discipline held; the definition of "covers" did not bind. RED persisted.

## Refactor

Added a binding definition to step 5 and a red flag:

> **What "a slice covers a criterion" means.** A slice covers a criterion only if completing that slice makes the criterion's scenarios pass end-to-end against a running instance. Topical relatedness is not coverage. A criterion only *partially* touched … is a **gap**, not coverage. When unsure, treat it as not covered and list it.

> Red flag: Calling a criterion covered because a slice is *related* to it (e.g. a "revoke" criterion mapped to a "permission checks" slice) → coverage means the slice makes the criterion's scenarios pass end-to-end; otherwise it's a gap.

## Post-refactor (2 reps) — converged

| Check | Rep 1 | Rep 2 |
| --- | --- | --- |
| PATHX-15 (revoke) | **Gap** — explicitly rejected the ENG-33 mapping as "topical-relatedness-as-coverage — a red flag" | **Gap** — "topically adjacent but neither implements… a gap, not coverage" |
| PATHX-14 (revoked branch) | caught as **partial gap** (depends on missing revoke) | caught as **partial gap** |
| ENG-35 (audit logging) | flagged product-orphan, mapped to technical-acceptance | flagged product-orphan |
| Technical-acceptance tracker | engineering | engineering |
| Sign-off gate before writes | held | held |

Both reps converged on the strict reading and both *independently caught a second-order partial gap* (PATHX-14's revoked branch). The variance is gone.

## Verdict

GREEN after one refactor. The binding lever was **defining coverage by an observable predicate** (scenarios pass end-to-end), not adding more prohibitions — consistent with "match the form to the failure": a judgment-shaping problem wants a sharpened predicate, not a louder rule.
