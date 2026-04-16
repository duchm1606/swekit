---
name: duck:executing
description: >-
  Use after duck:planning skill completes, when phase-1-tasks.md exists.
  Triggers: "execute tasks", "implement plan", "start building", ready to code.
metadata:
  version: '1.0'
  dependencies:
    - id: duck:planning
      kind: skill
      missing_effect: blocked
    - id: beads-cli
      kind: command
      command: br
      missing_effect: degraded
    - id: agent-mail
      kind: mcp_server
      server_names: [agent-mail]
      missing_effect: degraded
---

# Executing

## Principles

- **Subagents do the work.** Master agent coordinates and reviews.
- **One task per subagent.** Parallel when independent, sequential when dependent.
- **Review before merge.** No subagent work enters main without master review.

---

## Input Required

From planning skill:
- `history/<topic>/phase-1-tasks.md` — TDD-structured tasks
- `history/<topic>/phase-1-spec.md` — exit criteria

**If missing:** Stop. Run planning skill first.

---

## Process

```
Load Tasks → Spawn Subagents → Review Each → Integrate → Verify Exit State
```

---

## Phase 0: Load Tasks

### Read Task File

```bash
Read history/<topic>/phase-1-tasks.md
```

### Initialize Beads (if available)

```bash
br init --name "<feature-name>"
br add --title "Task 1.1: <action>" --status todo
br add --title "Task 1.2: <action>" --status todo
# ... for each task
br list
```

### Build Dependency Graph

| Task | Depends On | Can Parallel |
|------|------------|--------------|
| 1.1 | none | yes |
| 1.2 | none | yes |
| 1.3 | 1.1, 1.2 | no |

---

## Phase 1: Spawn Subagents

### Dispatch Rules

| Condition | Action |
|-----------|--------|
| Independent tasks | Spawn in parallel |
| Dependent task | Wait for dependencies |
| Large codebase | Use Agent Mail for coordination |
| Simple task | Single subagent |

### Subagent Prompt Template

```markdown
## Task
{paste exact task from phase-1-tasks.md}

## Context
- Working directory: {path}
- Related files: {list from task}
- Exit criteria: {from spec}

## Instructions
1. Follow TDD steps exactly as written
2. Do not modify files outside task scope
3. Commit after each passing test
4. Report: files changed, tests passed, blockers

## Constraints
- No new dependencies without approval
- No refactoring outside task scope
- Stop and report if blocked
```

### Spawning

```
Agent(description="Task 1.1: <action>", prompt=<template>)
Agent(description="Task 1.2: <action>", prompt=<template>)
```

**Parallel spawning:** Send independent tasks in single message.

---

## Phase 2: Review Each Subagent

**Every subagent result requires master review before proceeding.**

### Review Checklist

| Check | Pass Criteria |
|-------|---------------|
| Files match task | Only listed files modified |
| Tests exist | Test file created/updated |
| Tests pass | `go test` / `npm test` green |
| No scope creep | No extra features/refactoring |
| Commits atomic | One logical change per commit |

### Review Actions

| Result | Action |
|--------|--------|
| PASS | Mark task complete, proceed |
| MINOR issues | Fix inline, then mark complete |
| MAJOR issues | Re-dispatch subagent with feedback |
| BLOCKED | Escalate to user |

### Update Beads

```bash
br update --id <task-id> --status done
br list
```

---

## Phase 3: Agent Mail (Large Codebases)

Use when:
- 5+ subagents active
- File conflicts possible
- Cross-cutting changes

### Coordination Protocol

```bash
# Reserve files before work
mcp__agent-mail reserve --files "path/to/file.go"

# Release after commit
mcp__agent-mail release --files "path/to/file.go"

# Broadcast completion
mcp__agent-mail broadcast --message "Task 1.1 complete, file.go updated"
```

### Conflict Resolution

| Situation | Resolution |
|-----------|------------|
| Two agents need same file | Sequential execution |
| Merge conflict | Master resolves, re-verify tests |
| Breaking change discovered | Halt parallel work, reassess |

---

## Phase 4: Integration

After all tasks complete:

### Verify All Tests

```bash
go test ./... -v
# or
npm test
```

### Check Exit State

Compare against `phase-1-spec.md`:

```markdown
## Exit State Verification

| Criterion | Status |
|-----------|--------|
| {criterion 1} | PASS/FAIL |
| {criterion 2} | PASS/FAIL |
```

### Integration Commit

```bash
git add -A
git commit -m "feat(<scope>): complete phase 1 - <summary>"
```

---

## Phase 5: Handoff

```markdown
Phase 1 execution complete.

## Summary
- Tasks completed: {N}/{total}
- Tests passing: {count}
- Files changed: {list}

## Exit State
All criteria from phase-1-spec.md verified.

## Next Steps
- [ ] User review
- [ ] Proceed to Phase 2 (if exists)

Artifacts:
- Task log: history/<topic>/phase-1-execution.md
- Beads state: `br list`
```

---

## Anti-Patterns

| Don't | Do |
|-------|-----|
| Execute without task file | Load from planning artifacts |
| Skip subagent review | Review every result |
| Let subagent scope creep | Strict task boundaries |
| Batch all commits | Atomic commits per task |
| Assume tests pass | Verify after each task |

---

## Red Flags

- No task file from planning
- Subagent modifying unlisted files
- Tests not written before implementation
- Skipping review "to save time"
- File conflicts between subagents

---

## Beads CLI Reference

| Command | Purpose |
|---------|---------|
| `br init --name "<name>"` | Start new bead |
| `br add --title "<task>"` | Add task |
| `br update --id <id> --status <status>` | Update status |
| `br list` | Show all tasks |
| `br current` | Show active task |

Status values: `todo`, `in-progress`, `blocked`, `done`

---

## Output Summary

```
history/<topic>/
├── CONTEXT.md           (from research)
├── phase-plan.md        (from planning)
├── phase-1-spec.md      (from planning)
├── phase-1-tasks.md     (from planning)
└── phase-1-execution.md (from executing)
```
