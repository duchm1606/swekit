# Phase 1 Tasks: Add Brainstorming Artifacts

## Task 1.1: Create DIRECTION.md template

**Story:** Story 1 - Create DIRECTION.md template
**Creates:** Template file for brainstorming output

**Files:**
- Create: `.claude/skills/brainstorming/references/direction-template.md`

**Steps:**

- [ ] **Step 1: Create template file**

```markdown
# Direction: {Topic}

**From Brainstorming:** YYYY-MM-DD
**Selected Option:** {option name}

## Problem Statement
{One paragraph: what we're solving and why}

## Selected Approach
{Description of chosen option from evaluation}

## Research Questions (if going to research)
1. {specific question}
2. {specific question}

## Requirements (if going to planning)
- {requirement from brainstorming}

## Known Constraints
- {constraint}

## Success Criteria
- {how we'll know we're done}
```

- [ ] **Step 2: Verify file exists**

```bash
cat .claude/skills/brainstorming/references/direction-template.md
```

---

## Task 1.2: Add MUST WRITE gate to brainstorming SKILL.md

**Story:** Story 2 - Update SKILL.md with gate enforcement
**Creates:** Hard gate before Phase 4

**Files:**
- Modify: `.claude/skills/brainstorming/SKILL.md` (after Phase 3, before Phase 4)

**Steps:**

- [ ] **Step 1: Add MUST WRITE section after Phase 3**

Insert after "## Phase 3: Quick Evaluation" and before "## Phase 4: Frame Research":

```markdown
---

## HARD GATE: Write DIRECTION.md

**Before proceeding, you MUST write the direction file.**

Use template: `references/direction-template.md`
Save to: `history/<topic>/DIRECTION.md`

**Do not continue until file is written.**

---

## Decision Point

Present to user:

```
Direction written to: history/<topic>/DIRECTION.md

Next step options:
1. **Research** - Need to evaluate packages/libraries/approaches
2. **Planning** - Requirements clear, ready to break into tasks

Which path?
```

**Wait for user response before proceeding.**
```

- [ ] **Step 2: Update Phase 4 title**

Change "## Phase 4: Frame Research" to "## Path A: Frame Research (if research needed)"

- [ ] **Step 3: Add Path B section**

Insert after Path A (research) section:

```markdown
---

## Path B: Handoff to Planning (if requirements clear)

If user chose planning and no research needed:

```markdown
Brainstorming complete.

**Direction:** {selected option}
**Requirements:** {count from DIRECTION.md}

Artifact: history/<topic>/DIRECTION.md

Ready for implementation planning. Use duck:planning.
```
```

- [ ] **Step 4: Update final Handoff section**

Replace the existing "## Handoff to Research" with updated text that reflects both paths.

---

## Task 1.3: Verify changes

**Story:** Story 2 - Update SKILL.md with gate enforcement
**Creates:** Verification that changes are correct

**Files:**
- Read: `.claude/skills/brainstorming/SKILL.md`

**Steps:**

- [ ] **Step 1: Read updated file and verify structure**

```bash
grep -n "HARD GATE\|Decision Point\|Path A\|Path B" .claude/skills/brainstorming/SKILL.md
```

Expected: All four sections appear in correct order.
