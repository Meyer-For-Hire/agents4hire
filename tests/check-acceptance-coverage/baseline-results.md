# Baseline Results (RED) — Check Acceptance Coverage

Run via fresh `general-purpose` subagents, 2026-06-25. Two baseline reps + two with-skill reps, then a refactor pass.

## The expected failure did NOT reproduce; a sharper one did

The predicted failure — *silently hiding coverage gaps* — largely **did not** occur. Both no-guidance baselines surfaced PATHX-15 and flagged ENG-35 as a product-orphan. Per the methodology, a failure the control doesn't exhibit isn't worth bulletproofing: agents are naturally decent at **listing** the gaps they *perceive*.

The real failure is one layer up: **inconsistent judgment of what "covers" means.**

| Run | PATHX-15 (revoke) verdict |
| --- | --- |
| Baseline 1 | **Gap** — "no slice implements the revoke action" |
| Baseline 2 | "Split across ENG-33 + ENG-34… no single owning slice" (blurred) |
| With-skill (pre-refactor) rep 1 | **Gap** — clean |
| With-skill (pre-refactor) rep 2 | **"PATHX-15 → ENG-33 ✓ … all 5 covered, no gaps"** — masked the gap |

So a permissive agent maps a *topically related* slice ("permission checks") onto a criterion describing an unimplemented *action* ("revoke"), and the gap disappears — declaring a behavior covered that would ship untested. This is the dangerous variance (different interpretations across reps ⇒ wording not binding).

## Gap the skill must address

Not "force listing orphans" (agents list what they perceive) but **bind the definition of coverage** to an observable predicate: a slice covers a criterion only if completing it makes the criterion's scenarios pass end-to-end. Topical relatedness ≠ coverage; partial touch = gap.
