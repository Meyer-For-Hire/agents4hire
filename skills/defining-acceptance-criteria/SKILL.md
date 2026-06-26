---
name: defining-acceptance-criteria
description: Use when deriving product acceptance criteria from a conversation or a PRD's user stories, or when writing BDD Given/When/Then criteria for a feature. Product-level observable behavior only — not test code, not implementation, and not issue creation.
---

# Defining Acceptance Criteria

Synthesize **product acceptance criteria** from the conversation, the PRD's user stories, and the domain glossary already in context. Do NOT interview — you already have what you need.

This skill **produces the criteria as text** — typically the Acceptance Criteria section of a PRD. It does **not** create issues, write tests, or classify criteria automatable vs. manual. It **stops at criteria**: turning them into tracker sub-issues is a separate, deliberate step, and test authoring and automatable/manual classification happen later, in the acceptance phase.

## Prerequisites

- **The domain glossary (`CONTEXT.md`) must be in context.** If it isn't, stop and ask. Every criterion MUST use glossary vocabulary so scenario nouns and verbs stay consistent across the suite — this is what lets a downstream issue step turn each Requirement into a reliable, testable issue. (See `/sharpen-domain-language`.)

## Scope

Criteria describe **externally observable behavior at the product boundary** (API/UI) only. Phrase every scenario so a black-box test could verify it against a running instance — arrange, observe, and assert all expressible from outside the system. Never reference modules, internal seams, file paths, state, or implementation. (This is distinct from the tech spec's Testing Decisions, which cover implementation seams — do not conflate them.)

## The criteria are written like this

Cover the acceptance-worthy *behaviors* — including non-functional ones (auth/permissions, performance, data integrity) where they're part of "done." **Cover behaviors, not one-per-story mechanically.** Include negative and edge scenarios, not just happy paths.

Each criterion is one `### Requirement:` with a SHALL statement, then an **Issue:** line (`_(unpublished)_` until it's published as a sub-issue in the tracker), followed by one or more `#### Scenario:` blocks. One behavior per scenario.

```md
### Requirement: Owner can send an Invitation by email

A Workspace Owner SHALL be able to invite a person by supplying their email and a Role.

**Issue:** _(unpublished)_

#### Scenario: Owner invites a new person as a Collaborator

- GIVEN I am signed in as an Owner of a Workspace
- WHEN I create an Invitation for a person with the Role Collaborator
- THEN a pending Invitation with the Role Collaborator exists in the Workspace
- AND an invitation email with an acceptance link is sent to that address

#### Scenario: A Collaborator cannot send an Invitation

- GIVEN I am signed in as a Member with the Role Collaborator
- WHEN I attempt to create an Invitation
- THEN the Invitation is not created
- AND I am told I am not permitted to invite people
```

The `### Requirement:` / **Issue:** / `#### Scenario:` structure is a contract: one Requirement maps to exactly one publishable issue, and its scenarios are that issue's checkable behaviors. Keep it exact so the downstream issue step can parse it.

## Process

1. Derive the criteria covering the acceptance-worthy behaviors, in the form above.
2. **Review with the user.** Present the full set and revise on feedback. Surface open questions for the spec owner. (Publishing to the issue tracker — with its own sign-off — is a separate step; this skill stops at agreed criteria text.)

## Red flags — STOP

- A scenario names a module, table, endpoint, file path, or internal state → rewrite it at the product boundary
- You're writing test code, Gherkin step definitions, or tagging criteria automatable/manual → out of scope; stop at criteria
- You're creating issues in the tracker → out of scope; this skill stops at criteria text
- You grouped criteria one-per-user-story instead of one-per-Requirement/behavior → regroup by behavior
- A scenario uses a noun the glossary tells you to `_Avoid_` → use the canonical term
