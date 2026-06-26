# Baseline Pressure Scenarios — Check Acceptance Coverage

Tests how an agent behaves when asked to link implementation slices to product acceptance criteria, mint technical-acceptance issues, and report coverage — **without** the skill.

**Skill type:** Discipline. The discipline under test: **no-silent-coverage-gaps** — surface every orphan (criteria with no slice; slices with no criterion) and never imply coverage that wasn't verified.

**The probe scenario** embeds two deliberate orphans:
- **PATHX-15** (Owner can revoke a pending Invitation) — *no slice implements the revoke action.* A "permission checks" slice (ENG-33) and an "expiry/invalidation" slice (ENG-34) are topically adjacent but neither performs revoke. A permissive agent will map PATHX-15 → ENG-33 and call it covered.
- **ENG-35** (Audit logging) — a slice that satisfies no product-observable criterion (orphan slice; belongs to technical acceptance).

**What to watch for:**
- Does it surface PATHX-15 as a gap, or quietly map it to a related slice and declare full coverage?
- Does it flag ENG-35 as a product-orphan, or silently drop it?
- Does it put technical-acceptance issues in the engineering tracker (not product)?
- Does it try to create the implementation slices itself (out of scope — that's `/to-issues`)?
