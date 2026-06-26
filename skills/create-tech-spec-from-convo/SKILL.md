---
name: create-tech-spec-from-convo
description: Turn the current technical-design conversation into a tech spec (seam analysis, implementation decisions, testing decisions) and publish it to the configured tech-spec location — no interview, just synthesis of what you've already discussed.
disable-model-invocation: true
---

# Create Tech Spec from conversation

Take the current conversation and codebase understanding and produce a **tech spec** — the technical-design counterpart to the product PRD. Do NOT interview the user; synthesize what you already know.

This is the **technical** half of what used to be one PRD. The product/UX material (problem, solution, user stories, UX, acceptance-criteria references) lives in the PRD (`/create-prd-from-convo`). This spec carries only the engineering decisions.

The destination is configured per repo. Read `docs/agents/document-locations.md` for where to create the tech spec; run `/setup-m4h-agents4hire` if that file doesn't exist.

## Prerequisites

- The PRD (and its acceptance criteria) should exist — the tech spec is the design that satisfies them. Link it from the same product epic.
- Use the project's domain glossary vocabulary throughout, and respect any ADRs in the area you're touching. Surface conflicts with existing ADRs rather than silently overriding them.

## Process

1. Explore the repo to understand the current state of the codebase, if you haven't already.

2. **Sketch the seams** at which you'll test the feature. Existing seams should be preferred to new ones. Use the highest seam possible. If new seams are needed, propose them at the highest point you can. The fewer seams across the codebase, the better — the ideal number is one. **Check with the user that these seams match their expectations** before writing the spec.

3. Write the tech spec using the template below, then publish it to the **tech-spec location** named in `docs/agents/document-locations.md`, linked from the product epic. This spec enters technical review; the downstream issue-planning step (`/to-issues`) is what mints `ready-for-agent` implementation issues from it — so do **not** apply that label here.

<tech-spec-template>

## Implementation Decisions

The implementation decisions that were made. This can include:

- The modules that will be built/modified
- The interfaces of those modules that will be modified
- Technical clarifications from the developer
- Architectural decisions
- Schema changes
- API contracts
- Specific interactions

Do NOT include specific file paths or code snippets. They may end up being outdated very quickly.

Exception: if a prototype produced a snippet that encodes a decision more precisely than prose can (state machine, reducer, schema, type shape), inline it within the relevant decision and note briefly that it came from a prototype. Trim to the decision-rich parts — not a working demo, just the important bits.

## Testing Decisions

The testing decisions that were made. Include:

- A description of what makes a good test (only test external behavior, not implementation details)
- Which modules will be tested
- The seams identified in step 2, and why they were chosen
- Prior art for the tests (i.e. similar types of tests in the codebase)

## Further Notes

Any further technical notes about the feature.

</tech-spec-template>
