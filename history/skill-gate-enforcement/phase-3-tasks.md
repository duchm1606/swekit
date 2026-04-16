# Phase 3 Tasks: Enforce Subagent Approach in Executing

## Task 3.1: Add subagent enforcement gate

**Story:** Story 1 - Add subagent enforcement gate
**Creates:** Hard gate section preventing direct implementation

**Files:**
- Modify: `.claude/skills/executing/SKILL.md` (after "## Input Required" section)

**Steps:**

- [ ] **Step 1: Add HARD GATE section after Input Required**

Insert after "## Input Required" section:

```markdown
---

## HARD GATE: Subagent-Only Implementation

**The master agent MUST NOT implement code directly.**

Master agent responsibilities:
- Load and parse tasks
- Dispatch subagents for implementation
- Review subagent output
- Coordinate between tasks
- Commit after each task completion

Subagent responsibilities:
- Implement the task
- Write tests
- Report status (DONE, BLOCKED, NEEDS_CONTEXT, DONE_WITH_CONCERNS)

**If you find yourself writing implementation code, STOP. Dispatch a subagent instead.**

```

---

## Task 3.2: Add commit-per-task requirement

**Story:** Story 2 - Add commit-per-task requirement
**Creates:** Explicit commit step in task completion flow

**Files:**
- Modify: `.claude/skills/executing/SKILL.md` (in Phase 3 review section)

**Steps:**

- [ ] **Step 1: Find the "Update Beads After Review" section and add commit requirement**

Change the section to include commit:

```markdown
### Update Beads + Commit After Review

```bash
# Update beads status
br update --id <task-id> --status done

# Commit the task completion
git add -A
git commit -m "feat(<scope>): task <task-id> - <task summary>"

# Verify
br list
git log --oneline -1
```

**Every task completion = beads update + git commit.**

This enables:
- Easy revert of individual tasks (`git revert <commit>`)
- Clear history of what each task changed
- Checkpoint for recovery if later tasks fail
```

---

## Task 3.3: Update anti-patterns and red flags

**Story:** Story 3 - Update anti-patterns and red flags
**Creates:** New violations added to tables

**Files:**
- Modify: `.claude/skills/executing/SKILL.md` (Anti-Patterns and Red Flags sections)

**Steps:**

- [ ] **Step 1: Add new anti-patterns**

Add rows to Anti-Patterns table:

```markdown
| Master implements directly | Dispatch subagent for all implementation |
| Skip commit after task | Always commit after task completion |
| Batch commits for multiple tasks | One commit per task for easy revert |
```

- [ ] **Step 2: Add new red flags**

Add to Red Flags list:

```markdown
- **Master agent writing implementation code**
- **Multiple tasks completed without commits**
- **Beads updated but no corresponding commit**
- **Commit includes changes from multiple tasks**
```

---

## Task 3.4: Verify changes

**Story:** All stories
**Creates:** Verification that changes are correct

**Files:**
- Read: `.claude/skills/executing/SKILL.md`

**Steps:**

- [ ] **Step 1: Verify all new sections exist**

```bash
grep -n "HARD GATE\|Subagent-Only\|Commit After Review\|Master implements directly" .claude/skills/executing/SKILL.md
```

Expected: All sections appear in file.
