---
name: map-product-acceptance-to-issues
description: Use when correlating published product acceptance-criteria issues with the implementation work that satisfies them — linking each criterion to the epic or milestone after which it should be testable, and flagging any criterion no planned work covers.
---

# Map Product Acceptance to Issues

Once the implementation work for a feature exists in the tracker, **situate each product acceptance-criterion issue against the plan**: link it to the epic or milestone after which it should be testable — i.e. the point where, once that work is done, the criterion's scenarios should pass. The criterion then has a clear "test me after here" anchor.

This skill links existing issues; it does **not** create issues, author criteria, or read the tech spec. Technical acceptance criteria aren't mapped here — they ride along in the slices via `/to-issues`.

## Prerequisites

Stop and say what's missing if any of these aren't true:

- The **product acceptance-criteria issues** exist with stable IDs (from `/prd-to-acceptance-issues`).
- The **implementation work** exists and is organized under epics/milestones (from `/to-issues` and planning).
- The epic/milestone structure and the tracker are configured — see `docs/agents/document-locations.md` and `docs/agents/issue-tracker.md`.

## Process

1. **Gather** the product acceptance-criteria issues and the implementation plan (the epics/milestones and the vertical slices under them).

2. **Map each criterion to its anchor.** For each product acceptance criterion, determine the epic or milestone after which it becomes testable — the latest milestone whose completion is needed for the criterion's scenarios to pass — and link the criterion issue to it. A criterion may be satisfied by work spread across several slices; anchor it to the milestone by which all of that work is done.

   **What "the criterion is covered" means.** Covered means completing the anchored work makes the criterion's scenarios pass end-to-end against a running instance. Topical relatedness is not coverage; if a criterion is only partially served by the planned work, it is **not** covered — list it as a gap.

3. **Confirm, then apply.** Present the proposed mapping for review, then create the links.

4. **Coverage check (safety net).** This is the incidental-but-important guard: report any product acceptance criterion that maps to **no** implementation work — an important behavior with nothing planned to make it testable. Surface these explicitly with counts; do not imply full coverage you didn't verify. (The reverse — implementation work with no product criterion — is expected for internal/technical work and is not a product gap.)

## Red flags — STOP

- Creating issues, or authoring/editing criteria → out of scope; this skill only links existing issues
- Mapping technical acceptance criteria → those live in slices via `/to-issues`, not here
- Anchoring a criterion to a milestone whose completion wouldn't actually make its scenarios pass → topical relatedness isn't coverage; find the milestone that does, or report a gap
- Reporting "all covered" without listing the criteria that map to no work, with counts → list them
