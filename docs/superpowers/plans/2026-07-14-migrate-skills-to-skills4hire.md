# Migrate Product-Dev Skills to skills4hire — Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.
>
> **Adaptation note:** This plan migrates Markdown skill definitions across three git repos, not application code. There is no pytest/TDD cycle. "Verify" steps substitute for test-run steps: grep checks for stale paths/refs, and a frontmatter/link sanity pass. Each task still ends with an independently reviewable, committed deliverable.

**Goal:** Move the 8 product-development skills from `agents4hire` to `skills4hire` (repo axis: agents4hire = dispatchable agents, skills4hire = conversational skills), update both READMEs and the marketplace manifest, and leave `well-factored-code-auditor` untouched in `agents4hire`.

**Architecture:** Each skill is a self-contained directory (`SKILL.md` + optional sibling files) with no cross-skill relative file paths — they reference each other only by slash-command name and reference per-consumer-repo runtime config (`docs/agents/*.md`) that lives in whatever repo installs the plugin, not in this repo. This means the migration is a straight directory move: no content rewriting needed inside the skill bodies themselves, only in the two READMEs and two manifests.

**Tech Stack:** Markdown skill files (Claude Code plugin format), `.claude-plugin/plugin.json`, `.claude-plugin/marketplace.json`.

## Global Constraints

- No skill renames beyond what MEY-16 requires (per milestone decision — includes NOT renaming `setup-m4h-agents4hire` even though the name now looks stale living in skills4hire; that's deferred to M4H-6).
- `well-factored-code-auditor` stays in `agents4hire`, unmodified, not restructured as an agent (descoped for v1 per Jim's 2026-07-14 decision — contradicted the "no renames" constraint and was already flagged as slippable).
- Do not push, open a PR, tag, or bump either plugin to a major version in this plan — that's MEY-20's job. This plan only needs each repo's content to be correct and committed locally on branch `jim/m4h-10-migrate-skills-to-skills4hire-restructure-well-factored-code`.
- Skills being moved (all 8, verified to have zero cross-skill relative file paths and zero `${CLAUDE_PLUGIN_ROOT}` usage): `grill-the-pm`, `create-prd-from-convo`, `create-tech-spec-from-convo`, `defining-acceptance-criteria`, `sharpen-domain-language`, `prd-to-acceptance-issues`, `map-product-acceptance-to-issues`, `setup-m4h-agents4hire`.
- Test scenario dirs that move with their skill: `tests/defining-acceptance-criteria`, `tests/sharpen-domain-language`, `tests/map-product-acceptance-to-issues`. (`tests/well-factored-code-auditor` stays put.)

---

### Task 1: Move the 4 simple skills (no test dir, no sibling files)

**Files:**
- Move: `agents4hire/skills/grill-the-pm/` → `skills4hire/skills/grill-the-pm/`
- Move: `agents4hire/skills/create-prd-from-convo/` → `skills4hire/skills/create-prd-from-convo/`
- Move: `agents4hire/skills/create-tech-spec-from-convo/` → `skills4hire/skills/create-tech-spec-from-convo/`
- Move: `agents4hire/skills/prd-to-acceptance-issues/` → `skills4hire/skills/prd-to-acceptance-issues/`

**Interfaces:**
- Consumes: nothing from other tasks.
- Produces: 4 skill directories present under `skills4hire/skills/`, absent from `agents4hire/skills/`.

- [ ] **Step 1: Copy the 4 directories into skills4hire**

```bash
cp -R /Users/jim/work/agents4hire/skills/grill-the-pm /Users/jim/work/skills4hire/skills/grill-the-pm
cp -R /Users/jim/work/agents4hire/skills/create-prd-from-convo /Users/jim/work/skills4hire/skills/create-prd-from-convo
cp -R /Users/jim/work/agents4hire/skills/create-tech-spec-from-convo /Users/jim/work/skills4hire/skills/create-tech-spec-from-convo
cp -R /Users/jim/work/agents4hire/skills/prd-to-acceptance-issues /Users/jim/work/skills4hire/skills/prd-to-acceptance-issues
```

- [ ] **Step 2: Remove the originals from agents4hire**

```bash
rm -rf /Users/jim/work/agents4hire/skills/grill-the-pm
rm -rf /Users/jim/work/agents4hire/skills/create-prd-from-convo
rm -rf /Users/jim/work/agents4hire/skills/create-tech-spec-from-convo
rm -rf /Users/jim/work/agents4hire/skills/prd-to-acceptance-issues
```

- [ ] **Step 3: Verify — diff byte-identical, no stray files left behind**

Run:
```bash
diff -r /Users/jim/work/skills4hire/skills/grill-the-pm <(git -C /Users/jim/work/agents4hire show HEAD:skills/grill-the-pm/SKILL.md) 2>/dev/null; echo "(manual check below is authoritative)"
ls /Users/jim/work/agents4hire/skills | grep -E "grill-the-pm|create-prd-from-convo|create-tech-spec-from-convo|prd-to-acceptance-issues" && echo "FAIL: still present" || echo "OK: removed from agents4hire"
ls /Users/jim/work/skills4hire/skills | grep -E "grill-the-pm|create-prd-from-convo|create-tech-spec-from-convo|prd-to-acceptance-issues" | wc -l
```
Expected: "OK: removed from agents4hire", and the second `ls` lists all 4 names.

- [ ] **Step 4: Commit in agents4hire**

```bash
cd /Users/jim/work/agents4hire
git add -A skills/grill-the-pm skills/create-prd-from-convo skills/create-tech-spec-from-convo skills/prd-to-acceptance-issues
git commit -m "refactor: move grill-the-pm, create-prd-from-convo, create-tech-spec-from-convo, prd-to-acceptance-issues to skills4hire"
```

- [ ] **Step 5: Commit in skills4hire**

```bash
cd /Users/jim/work/skills4hire
git add skills/grill-the-pm skills/create-prd-from-convo skills/create-tech-spec-from-convo skills/prd-to-acceptance-issues
git commit -m "feat: add grill-the-pm, create-prd-from-convo, create-tech-spec-from-convo, prd-to-acceptance-issues (migrated from agents4hire)"
```

---

### Task 2: Move the 3 skills that carry a test scenario directory

**Files:**
- Move: `agents4hire/skills/defining-acceptance-criteria/` → `skills4hire/skills/defining-acceptance-criteria/`
- Move: `agents4hire/tests/defining-acceptance-criteria/` → `skills4hire/tests/defining-acceptance-criteria/`
- Move: `agents4hire/skills/sharpen-domain-language/` → `skills4hire/skills/sharpen-domain-language/`
- Move: `agents4hire/tests/sharpen-domain-language/` → `skills4hire/tests/sharpen-domain-language/`
- Move: `agents4hire/skills/map-product-acceptance-to-issues/` → `skills4hire/skills/map-product-acceptance-to-issues/`
- Move: `agents4hire/tests/map-product-acceptance-to-issues/` → `skills4hire/tests/map-product-acceptance-to-issues/`

**Interfaces:**
- Consumes: nothing from Task 1.
- Produces: `skills4hire/tests/` created (did not exist before); 3 more skill directories present under `skills4hire/skills/`, absent from `agents4hire/skills/` and `agents4hire/tests/`.

- [ ] **Step 1: Copy skill + test dirs into skills4hire**

```bash
mkdir -p /Users/jim/work/skills4hire/tests
cp -R /Users/jim/work/agents4hire/skills/defining-acceptance-criteria /Users/jim/work/skills4hire/skills/defining-acceptance-criteria
cp -R /Users/jim/work/agents4hire/tests/defining-acceptance-criteria /Users/jim/work/skills4hire/tests/defining-acceptance-criteria
cp -R /Users/jim/work/agents4hire/skills/sharpen-domain-language /Users/jim/work/skills4hire/skills/sharpen-domain-language
cp -R /Users/jim/work/agents4hire/tests/sharpen-domain-language /Users/jim/work/skills4hire/tests/sharpen-domain-language
cp -R /Users/jim/work/agents4hire/skills/map-product-acceptance-to-issues /Users/jim/work/skills4hire/skills/map-product-acceptance-to-issues
cp -R /Users/jim/work/agents4hire/tests/map-product-acceptance-to-issues /Users/jim/work/skills4hire/tests/map-product-acceptance-to-issues
```

- [ ] **Step 2: Remove originals from agents4hire**

```bash
rm -rf /Users/jim/work/agents4hire/skills/defining-acceptance-criteria /Users/jim/work/agents4hire/tests/defining-acceptance-criteria
rm -rf /Users/jim/work/agents4hire/skills/sharpen-domain-language /Users/jim/work/agents4hire/tests/sharpen-domain-language
rm -rf /Users/jim/work/agents4hire/skills/map-product-acceptance-to-issues /Users/jim/work/agents4hire/tests/map-product-acceptance-to-issues
```

- [ ] **Step 3: Verify**

```bash
ls /Users/jim/work/agents4hire/skills; ls /Users/jim/work/agents4hire/tests
```
Expected: only `well-factored-code-auditor` remains in both `skills/` and `tests/`.

- [ ] **Step 4: Commit in agents4hire**

```bash
cd /Users/jim/work/agents4hire
git add -A skills/defining-acceptance-criteria tests/defining-acceptance-criteria skills/sharpen-domain-language tests/sharpen-domain-language skills/map-product-acceptance-to-issues tests/map-product-acceptance-to-issues
git commit -m "refactor: move defining-acceptance-criteria, sharpen-domain-language, map-product-acceptance-to-issues (+ tests) to skills4hire"
```

- [ ] **Step 5: Commit in skills4hire**

```bash
cd /Users/jim/work/skills4hire
git add skills/defining-acceptance-criteria tests/defining-acceptance-criteria skills/sharpen-domain-language tests/sharpen-domain-language skills/map-product-acceptance-to-issues tests/map-product-acceptance-to-issues
git commit -m "feat: add defining-acceptance-criteria, sharpen-domain-language, map-product-acceptance-to-issues + test scenarios (migrated from agents4hire)"
```

---

### Task 3: Move setup-m4h-agents4hire (has 2 sibling seed files)

**Files:**
- Move: `agents4hire/skills/setup-m4h-agents4hire/` (contains `SKILL.md`, `document-locations.md`, `issue-tracker-linear.md`) → `skills4hire/skills/setup-m4h-agents4hire/`

**Interfaces:**
- Consumes: nothing from Tasks 1–2.
- Produces: `setup-m4h-agents4hire` present under `skills4hire/skills/`, absent from `agents4hire/skills/`. Its two internal relative links (`./issue-tracker-linear.md`, `./document-locations.md`) keep working because both files move together in the same directory.

- [ ] **Step 1: Copy, then remove original**

```bash
cp -R /Users/jim/work/agents4hire/skills/setup-m4h-agents4hire /Users/jim/work/skills4hire/skills/setup-m4h-agents4hire
rm -rf /Users/jim/work/agents4hire/skills/setup-m4h-agents4hire
```

- [ ] **Step 2: Verify the sibling links still resolve and the directory is gone from agents4hire**

```bash
ls /Users/jim/work/skills4hire/skills/setup-m4h-agents4hire
test -f /Users/jim/work/skills4hire/skills/setup-m4h-agents4hire/document-locations.md && test -f /Users/jim/work/skills4hire/skills/setup-m4h-agents4hire/issue-tracker-linear.md && echo OK
ls /Users/jim/work/agents4hire/skills | grep setup-m4h-agents4hire && echo FAIL || echo "OK: removed"
```
Expected: `OK`, then `OK: removed`.

- [ ] **Step 3: Commit in agents4hire**

```bash
cd /Users/jim/work/agents4hire
git add -A skills/setup-m4h-agents4hire
git commit -m "refactor: move setup-m4h-agents4hire to skills4hire"
```

- [ ] **Step 4: Commit in skills4hire**

```bash
cd /Users/jim/work/skills4hire
git add skills/setup-m4h-agents4hire
git commit -m "feat: add setup-m4h-agents4hire (migrated from agents4hire)"
```

---

### Task 4: Rewrite agents4hire README (drop product-dev section, state the axis)

**Files:**
- Modify: `agents4hire/README.md`

**Interfaces:**
- Consumes: nothing (pure doc edit).
- Produces: README describing agents4hire as the dispatchable-agents home, `well-factored-code-auditor` only, with a one-line pointer to skills4hire for the product-dev skills.

- [ ] **Step 1: Replace the README body**

Keep the `well-factored-code-auditor` section verbatim. Delete the entire "Product-development workflow" section and its `create-tech-spec-from-convo`/etc. bullet list. Delete the "Required for the product-development workflow" dependency block. Add one line under the title stating the repo axis, and a pointer to skills4hire. Add a "Superpowers" note stays as-is (auditor still depends on it).

New file content:

```markdown
# agents4hire

Specialized AI agents for code quality, refactoring, and engineering excellence.

**Repo axis:** `agents4hire` = things you dispatch (agents/autonomous workflows). The conversational product-development skills that used to live here have moved to [skills4hire](https://github.com/Meyer-For-Hire/skills4hire).

## Skills

### well-factored-code-auditor

Systematic codebase factoring audit that assesses code quality across seven principles (Clarity, SRP, DRY, YAGNI, Testability, Test Adequacy, Consistency), produces a graded scorecard with letter grades per component and principle, and generates a prioritized refactoring plan.

**Invoke:** `/agents4hire:well-factored-code-auditor` or `/well-factored-code-auditor`

**What it does:**
- Surveys codebase structure and discovers style conventions
- Analyzes components independently using subagents
- Examines cross-cutting concerns (DRY across boundaries, dependency direction)
- Produces a scorecard at `./docs/YYYY-MM-DD-well-factored-code-scorecard.md`
- Hands off to a refactoring plan workflow

**Files:**
- `skills/well-factored-code-auditor/SKILL.md` — main skill document
- `skills/well-factored-code-auditor/grading-rubric.md` — letter grade scale and per-principle criteria
- `skills/well-factored-code-auditor/scorecard-template.md` — output template for the scorecard
- `skills/well-factored-code-auditor/plan-review-checklist.md` — post-planning review checklist for refactoring plans

## Installation

Add the Meyer For Hire marketplace, then install the plugin:

In Claude Code:
```
/plugin marketplace add Meyer-For-Hire/m4h-marketplace
/plugin install agents4hire@m4h-marketplace
```

Or from the command line:
```bash
claude plugin marketplace add Meyer-For-Hire/m4h-marketplace
claude plugin install agents4hire@m4h-marketplace
```

## Dependencies

**Optional but recommended:** The [Superpowers](https://github.com/obra/superpowers) plugin by Jesse Vincent. The well-factored-code-auditor uses `superpowers:writing-plans` for structured analysis and refactoring plans. Without it, the auditor still works but produces inline plans instead of using the Superpowers structured planning workflow.

```
/plugin marketplace add obra/superpowers-marketplace
/plugin install superpowers@superpowers-marketplace
```

## Author

Meyer For Hire Consulting, LLC

## License

MIT
```

- [ ] **Step 2: Verify no dangling references to the moved skills remain**

```bash
grep -n "grill-the-pm\|create-prd-from-convo\|create-tech-spec-from-convo\|defining-acceptance-criteria\|sharpen-domain-language\|prd-to-acceptance-issues\|map-product-acceptance-to-issues\|setup-m4h-agents4hire" /Users/jim/work/agents4hire/README.md
```
Expected: no output.

- [ ] **Step 3: Commit**

```bash
cd /Users/jim/work/agents4hire
git add README.md
git commit -m "docs: state repo axis in agents4hire README, drop migrated product-dev section"
```

---

### Task 5: Rewrite skills4hire README (add product-dev section, state the axis)

**Files:**
- Modify: `skills4hire/README.md`

**Interfaces:**
- Consumes: the final skill names/invocations from Tasks 1–3 (must match exactly what's now on disk under `skills4hire/skills/`).
- Produces: README listing `pqa-mode` (existing) plus the 8 migrated skills, in the same flow-ordered style as the old agents4hire README, with the axis statement and updated Installation/Dependencies sections.

- [ ] **Step 1: Replace the README body**

Keep the existing `pqa-mode` section and Installation section structure. Add the axis statement and the full product-development workflow section (adapted from the old agents4hire README — same content, same skill names, no renames):

```markdown
# skills4hire

Skills for capturing and recalling reusable developer knowledge across projects.

**Repo axis:** `skills4hire` = skills you use in conversation. Dispatchable agents (e.g. `well-factored-code-auditor`) live in [agents4hire](https://github.com/Meyer-For-Hire/agents4hire).

## Skills

### pqa-mode

Enter **PQA** — Precision Question and Answer. Every reply is the tightest thing
that carries the meaning: one of five forms (yes/no, a number, a date, a bulleted
list, or "I don't know, but I think …" + one of those), or a single concise
question. No preamble, no offers, no decoration. Stays active until you leave it.

**Invoke:** `/skills4hire:pqa-mode` or `/pqa-mode`

### Product-development workflow

A set of skills for the M4H/PathX **product-development** workflow, built on top of [Matt Pocock's skills kit](https://github.com/mattpocock/skills). They split product definition from technical design, drive precise domain language, and turn a working conversation into a PRD, a tech spec, and BDD acceptance criteria — keeping **authoring** (writing documents) separate from the **side-effecting** issue creation, which happens only after each document passes review.

Run [`/setup-m4h-agents4hire`](skills/setup-m4h-agents4hire/SKILL.md) once per repo first — it records where documents go (`docs/agents/document-locations.md`) and the issue-tracker coordinates (`docs/agents/issue-tracker.md`) the other skills read.

The flow, roughly in order:

1. [`/grill-the-pm`](skills/grill-the-pm/SKILL.md) — relentless interview to sharpen a product owner's feature definition and build the glossary, without dragging them into architecture. The PM counterpart to Pocock's `/grill-with-docs` (which stays the architect's tool). *(user-invoked)*
2. [`/create-prd-from-convo`](skills/create-prd-from-convo/SKILL.md) — synthesize a product/UX PRD (with a BDD acceptance-criteria section) and publish it to the configured PRD location. Writes a document; creates no issues. *(user-invoked)*
3. **After PRD review:** [`/prd-to-acceptance-issues`](skills/prd-to-acceptance-issues/SKILL.md) — publish the PRD's criteria as one sub-issue per Requirement (stable criterion IDs) in the product tracker. *(user-invoked)*
4. [`/create-tech-spec-from-convo`](skills/create-tech-spec-from-convo/SKILL.md) — synthesize the technical half (seam analysis, implementation + testing decisions, and **technical acceptance criteria** by category: performance, scale, privacy, security) into a tech spec. *(user-invoked)*
5. **After tech-spec review:** Pocock's `/to-issues` creates the implementation slices, folding each technical acceptance criterion into the slice it applies to. Then `/map-product-acceptance-to-issues` records a one-way reference from each product acceptance criterion to the implementation work after which it's testable (the implementation side stays clean), flagging any criterion no work covers.

Composed disciplines *(model-invoked — reached by the above or autonomously)*:

- [`/defining-acceptance-criteria`](skills/defining-acceptance-criteria/SKILL.md) — derive BDD Given/When/Then criteria as PRD text (one Requirement per behavior). Authors criteria only; does not create issues.
- [`/sharpen-domain-language`](skills/sharpen-domain-language/SKILL.md) — build and sharpen the ubiquitous language with a product owner; sliced from Pocock's `/domain-modeling` with ADRs and code cross-referencing removed.
- [`/map-product-acceptance-to-issues`](skills/map-product-acceptance-to-issues/SKILL.md) — record a one-way reference from each product acceptance-criterion issue to the implementation work after which it's testable (nothing written to the implementation side); flag criteria no planned work covers.

And: [`/setup-m4h-agents4hire`](skills/setup-m4h-agents4hire/SKILL.md) configures a repo for all of the above. *(user-invoked)*

## Installation

Add the Meyer For Hire marketplace, then install the plugin.

In Claude Code:

```
/plugin marketplace add Meyer-For-Hire/m4h-marketplace
/plugin install skills4hire@m4h-marketplace
```

Or from the command line:

```bash
claude plugin marketplace add Meyer-For-Hire/m4h-marketplace
claude plugin install skills4hire@m4h-marketplace
```

## Dependencies

**Required for the product-development workflow:**

- [Matt Pocock's skills kit](https://github.com/mattpocock/skills) — the product-development skills compose his `/grilling`, `/grill-with-docs`, `/setup-matt-pocock-skills`, and `/to-issues` skills, and `/sharpen-domain-language` is sliced from his `/domain-modeling`.
- **MCP connectors for your configured issue tracker and document store.** The skills are system-agnostic and read their destinations from `docs/agents/`; M4H's default configuration uses the **Linear** and **Google Workspace** connectors.

## Author

Meyer For Hire Consulting, LLC

## License

MIT
```

- [ ] **Step 2: Verify every linked path in the new README exists on disk**

```bash
cd /Users/jim/work/skills4hire
for f in skills/setup-m4h-agents4hire/SKILL.md skills/grill-the-pm/SKILL.md skills/create-prd-from-convo/SKILL.md skills/prd-to-acceptance-issues/SKILL.md skills/create-tech-spec-from-convo/SKILL.md skills/defining-acceptance-criteria/SKILL.md skills/sharpen-domain-language/SKILL.md skills/map-product-acceptance-to-issues/SKILL.md; do
  test -f "$f" && echo "OK $f" || echo "MISSING $f"
done
```
Expected: `OK` for all 8.

- [ ] **Step 3: Commit**

```bash
cd /Users/jim/work/skills4hire
git add README.md
git commit -m "docs: state repo axis, document migrated product-dev skills in skills4hire README"
```

---

### Task 6: Bump plugin versions and update the marketplace manifest

**Files:**
- Modify: `agents4hire/.claude-plugin/plugin.json`
- Modify: `skills4hire/.claude-plugin/plugin.json`
- Modify: `m4h-marketplace/.claude-plugin/marketplace.json`

**Interfaces:**
- Consumes: nothing from earlier tasks except that the content move must already be committed in both repos.
- Produces: patch-level version bumps reflecting this content change (the major "v1" stamp is MEY-20's job, not this task's); marketplace descriptions/versions matching each plugin's new content.

- [ ] **Step 1: Bump agents4hire plugin.json (0.2.0 → 0.2.1) and drop the product-dev keywords**

Current `/Users/jim/work/agents4hire/.claude-plugin/plugin.json`:
```json
{
  "name": "agents4hire",
  "description": "Specialized AI agents and skills for code quality, refactoring, and the product-development workflow",
  "version": "0.2.0",
  "author": {
    "name": "Meyer For Hire Consulting, LLC"
  },
  "keywords": ["agents", "code-quality", "refactoring", "audit", "well-factored-code", "product-development", "prd", "acceptance-criteria", "domain-modeling"]
}
```
New content:
```json
{
  "name": "agents4hire",
  "description": "Specialized AI agents for code quality, refactoring, and engineering excellence",
  "version": "0.2.1",
  "author": {
    "name": "Meyer For Hire Consulting, LLC"
  },
  "keywords": ["agents", "code-quality", "refactoring", "audit", "well-factored-code"]
}
```

- [ ] **Step 2: Bump skills4hire plugin.json (0.1.0 → 0.2.0) and add product-dev keywords**

Current `/Users/jim/work/skills4hire/.claude-plugin/plugin.json`:
```json
{
  "name": "skills4hire",
  "description": "Skills for capturing and recalling reusable developer knowledge across projects.",
  "version": "0.1.0",
  "author": {
    "name": "Meyer For Hire Consulting, LLC"
  },
  "keywords": ["skills", "memory", "knowledge", "developer-knowledge"]
}
```
New content:
```json
{
  "name": "skills4hire",
  "description": "Skills for capturing and recalling reusable developer knowledge across projects, plus the M4H product-development workflow.",
  "version": "0.2.0",
  "author": {
    "name": "Meyer For Hire Consulting, LLC"
  },
  "keywords": ["skills", "memory", "knowledge", "developer-knowledge", "product-development", "prd", "acceptance-criteria", "domain-modeling"]
}
```

- [ ] **Step 3: Update m4h-marketplace marketplace.json plugin entries**

Current `/Users/jim/work/m4h-marketplace/.claude-plugin/marketplace.json` `plugins` array has `agents4hire` at `"version": "0.1.0"` with description mentioning `well-factored-code-auditor` (already correct) and `skills4hire` at `"version": "0.1.0"` with description mentioning only `/til` (stale — `til` doesn't actually exist in skills4hire; the real skill is `pqa-mode`, so this was already wrong before the migration).

New `plugins` array:
```json
  "plugins": [
    {
      "name": "agents4hire",
      "source": {
        "source": "url",
        "url": "https://github.com/Meyer-For-Hire/agents4hire.git"
      },
      "description": "Specialized AI agents for code quality, refactoring, and engineering excellence. Includes the well-factored-code-auditor skill.",
      "version": "0.2.1",
      "strict": true
    },
    {
      "name": "skills4hire",
      "source": {
        "source": "url",
        "url": "https://github.com/Meyer-For-Hire/skills4hire.git"
      },
      "description": "Skills for capturing and recalling reusable developer knowledge across projects, plus the M4H product-development workflow (grill-the-pm through map-product-acceptance-to-issues). Includes the /pqa-mode skill.",
      "version": "0.2.0",
      "strict": true
    }
  ]
```
Keep the `name`, `owner`, and `metadata` top-level fields as they are — only the two plugin entries change.

- [ ] **Step 4: Verify JSON validity on all three files**

```bash
python3 -c "import json; json.load(open('/Users/jim/work/agents4hire/.claude-plugin/plugin.json'))" && echo "agents4hire OK"
python3 -c "import json; json.load(open('/Users/jim/work/skills4hire/.claude-plugin/plugin.json'))" && echo "skills4hire OK"
python3 -c "import json; json.load(open('/Users/jim/work/m4h-marketplace/.claude-plugin/marketplace.json'))" && echo "marketplace OK"
```
Expected: all three print OK.

- [ ] **Step 5: Commit each repo**

```bash
cd /Users/jim/work/agents4hire && git add .claude-plugin/plugin.json && git commit -m "chore: bump agents4hire to 0.2.1, drop product-dev keywords"
cd /Users/jim/work/skills4hire && git add .claude-plugin/plugin.json && git commit -m "chore: bump skills4hire to 0.2.0, add product-dev keywords"
cd /Users/jim/work/m4h-marketplace && git add .claude-plugin/marketplace.json && git commit -m "chore: update plugin versions/descriptions for skill migration"
```

---

### Task 7: Final cross-repo verification and Linear update

**Files:** none (verification + tracker update only)

**Interfaces:**
- Consumes: the final committed state of all three repos from Tasks 1–6.
- Produces: confirmation that `git log` on each repo's branch shows the expected commits, and the MEY-10 Linear issue reflects completion.

- [ ] **Step 1: Confirm each repo's branch has the expected commit count and clean tree**

```bash
for d in agents4hire skills4hire m4h-marketplace; do
  echo "=== $d ==="
  git -C /Users/jim/work/$d log --oneline main.."$(git -C /Users/jim/work/$d branch --show-current)"
  git -C /Users/jim/work/$d status --short
done
```
Expected: each repo lists its commits from this plan, and `status --short` is empty (clean tree).

- [ ] **Step 2: Confirm agents4hire now only has well-factored-code-auditor**

```bash
ls /Users/jim/work/agents4hire/skills
ls /Users/jim/work/agents4hire/tests
```
Expected: `well-factored-code-auditor` only, in both.

- [ ] **Step 3: Confirm skills4hire has all 9 skills (8 migrated + pqa-mode)**

```bash
ls /Users/jim/work/skills4hire/skills
```
Expected: `pqa-mode`, `grill-the-pm`, `create-prd-from-convo`, `create-tech-spec-from-convo`, `defining-acceptance-criteria`, `sharpen-domain-language`, `prd-to-acceptance-issues`, `map-product-acceptance-to-issues`, `setup-m4h-agents4hire`.

- [ ] **Step 4: Report to Jim and stop (per 2026-07-14 autonomy decision — MEY-10 only, then check in before MEY-16)**

Do not push, open a PR, or move to MEY-16 without checking in first.
