---
name: check-acceptance-coverage
description: From an approved tech spec, create technical-acceptance issues in the engineering tracker, link the implementation slices to the product acceptance criteria they satisfy, and report coverage gaps. Run after the tech spec is reviewed and its slices exist.
disable-model-invocation: true
---

# Check Acceptance Coverage

Tie the implementation back to acceptance. From an **approved** tech spec, this skill: mints **technical-acceptance** issues in the engineering tracker, **links** the implementation slices to the **product acceptance criteria** they satisfy, and emits a **coverage map**.

It is the second of two deliberate engineering-side steps. The first — creating the implementation slices (vertical tracer bullets) — is Matt Pocock's `/to-issues`, which you run first. This skill does **not** create the slices itself, and it does **not** notify anyone: surfacing the coverage map to the PRD owner is handled outside the skill (the tracker's notifications or an automation), not by this skill.

## Prerequisites

Stop and say what's missing if any of these aren't true:

- The **approved tech spec** is in context or passed as a reference.
- The **implementation slices** exist in the engineering tracker (run `/to-issues` first).
- The **product acceptance criteria** have been published with stable issue IDs (run `/prd-to-acceptance-issues` first).
- The engineering tracker and the product epic are configured — see `docs/agents/document-locations.md` and `docs/agents/issue-tracker.md`.

## Process

1. **Gather** the three inputs: the tech spec, the implementation slices, and the published product criteria (with their issue IDs).

2. **Derive technical-acceptance criteria** from the tech spec — acceptance at the *engineering* boundary that is **not** product-observable (seam/integration contracts, non-functional targets, data-integrity guarantees from the Testing Decisions). Keep them verifiable. These are distinct from the product acceptance criteria, which stay in the product tracker.

3. **Sign-off gate.** Show the user the technical-acceptance issues you'll create and the slice↔criterion links you'll make. Get explicit approval before any write — these are side effects.

4. **Create** the technical-acceptance issues in the **engineering** tracker (not the product tracker).

5. **Link** (using the tracker's relation/link mechanism — see `docs/agents/issue-tracker.md`):
   - each implementation slice → the product criteria (by ID) it helps satisfy;
   - each technical-acceptance issue → the slice(s) it covers.

   **What "a slice covers a criterion" means.** A slice covers a criterion only if **completing that slice makes the criterion's scenarios pass end-to-end against a running instance.** Topical relatedness is not coverage. A criterion that is only *partially* touched — e.g. its permission is enforced by one slice but the action it describes is implemented by none — is a **gap**, not coverage. When unsure, treat it as not covered and list it.

6. **Report coverage — surface every gap; never imply coverage you didn't verify.** Produce a coverage map and post it as a comment on the product epic (the artifact the owner can later review). It MUST list, explicitly:
   - **product criteria with no linked slice** (unimplemented behavior),
   - **slices with no linked criterion** (work tracing to nothing),
   - **technical-acceptance issues with no linked slice.**
   If every item is covered, say so *and* show the counts — don't just assert "full coverage."

## What this skill does NOT do

- Create the implementation slices — that's `/to-issues`.
- Notify the PRD owner — that's external (the tracker's notifications or an automation). This skill only leaves the coverage map as an artifact.
- Block moving to implementation on the owner's review.

## Red flags — STOP

- Reporting "covered" / "done" without listing the orphan criteria and orphan slices → list them; show counts
- Calling a criterion covered because a slice is *related* to it (e.g. mapping a "revoke" criterion to a "permission checks" slice) → coverage means the slice makes the criterion's scenarios pass end-to-end; otherwise it's a gap
- Silently dropping a criterion that doesn't map to a slice → that's exactly what the coverage gap report is for
- Creating implementation slices here → run `/to-issues` instead
- Putting technical-acceptance issues in the product tracker → they belong in engineering
- Waiting on the PRD owner before finishing → the owner review is non-blocking and external
