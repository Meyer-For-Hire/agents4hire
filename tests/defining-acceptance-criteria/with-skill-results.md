# With-Skill Results (GREEN) — Defining Acceptance Criteria

Run via fresh `general-purpose` subagents given the skill, instructed not to explore any repo. 2026-06-22.

## Structure scenario (mirror of baseline B2 — recurring exports)

The scenario that defaulted to `AC1.1` checklists and one-per-story. With the skill:
- **Correct structure.** `### Requirement:` with SHALL statements, each followed by `#### Scenario:` GIVEN/WHEN/THEN/AND blocks.
- **Grouped by behavior, not per story.** Split user story 1 ("create a Schedule") into a *Creating a Schedule* Requirement **and** a separate *Authorization for Schedule management* Requirement — added auth as an acceptance-worthy non-functional behavior.
- **Sign-off gate held.** "I have NOT published anything yet… The skill requires explicit approval before any Linear write… Would you like me to publish as-is, or revise anything first?"
- Glossary-locked (Schedule/Export/Destination/Run; avoided job/cron/report/dump/target/sink/attempt); product-boundary throughout; one sub-issue per Requirement; Linear ID = stable criterion ID; no automatable/manual classification.

**PASS** — all three structural/discipline failures fixed.

## Implementation-leak bait (invitations, deliberately technical conversation)

Prompt seeded the conversation with JWT/Redis/72h-TTL/Postgres `invitations` table/`POST /api/v1/invitations`. With the skill:
- **Stripped the implementation detail** and said so: JWT, Redis, the table, and the endpoint path do not appear.
- Glossary-locked; product boundary; sign-off gate held; one Requirement per behavior.
- **Borderline (known minor):** kept "72 hours" as an *observable* expiry window, decoupled from the TTL mechanism, and reasoned transparently that a black-box test can verify it. Defensible (observable thresholds can be legitimate criteria). Not worth a nuance clause — `writing-skills` warns those backfire — so left as-is and recorded here.

**PASS** (with the noted minor).

## Verdict

3/3 GREEN across both skills' scenarios. The positive recipe (Requirement/SHALL + Scenario), the "cover behaviors not stories" rule, and the explicit sign-off gate all bind. One borderline numeric-threshold case noted, no iteration required.
