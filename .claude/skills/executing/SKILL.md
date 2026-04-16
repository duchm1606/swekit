---
name: duck:executing
description: >-
  Use after duck:planning skill completes, when phase-1-tasks.md exists.
  Triggers: "execute tasks", "implement plan", "start building", ready to code.
metadata:
  version: '1.1'
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
- **Two-stage review.** Spec compliance first, then code quality.
- **Checkpoint often.** Save state every 3 tasks or before complex operations.

---

## Input Required

From planning skill:
- `history/<topic>/phase-1-tasks.md` — TDD-structured tasks
- `history/<topic>/phase-1-spec.md` — exit criteria

**If missing:** Stop. Run planning skill first.

---

## Process

```
Load → Spawn → Handle Status → Spec Review → Quality Review → Checkpoint → Next Task
                    ↓
              BLOCKED/NEEDS_CONTEXT → Resolve → Re-dispatch
```

---

## Phase 0: Load Tasks

### Read Task File

```bash
Read history/<topic>/phase-1-tasks.md
Read history/<topic>/phase-1-spec.md
```

### Initialize Beads (if available)

```bash
br init --name "<feature-name>"
br add --title "Task 1.1: <action>" --status todo
br add --title "Task 1.2: <action>" --status todo
# ... for each task
br list
```

### Initialize Checkpoint

```bash
Write history/<topic>/checkpoint.json
```

```json
{
  "phase": 1,
  "started_at": "ISO-timestamp",
  "completed_tasks": [],
  "in_progress": [],
  "blocked": [],
  "last_commit": null,
  "task_count": 0
}
```

### Build Dependency Graph

| Task | Depends On | Can Parallel |
|------|------------|--------------|
| 1.1 | none | yes |
| 1.2 | none | yes |
| 1.3 | 1.1, 1.2 | no |

---

## Phase 1: Spawn Subagents

### Model Selection

| Task Complexity | Model | Signals |
|-----------------|-------|---------|
| Simple | haiku | 1-2 files, clear spec, isolated |
| Standard | sonnet | Multi-file, some integration |
| Complex | opus | Architecture, design judgment, debugging |

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

## Response Format (REQUIRED)
End your response with:

**Status:** DONE | DONE_WITH_CONCERNS | BLOCKED | NEEDS_CONTEXT
**Files Changed:** {list}
**Tests:** {passed/failed count}
**Commit:** {hash or "none"}
**Concerns/Blockers:** {if applicable}

## Constraints
- No new dependencies without approval
- No refactoring outside task scope
- Report BLOCKED immediately if stuck
```

### Spawning

```
Agent(description="Task 1.1: <action>", prompt=<template>, model="sonnet")
Agent(description="Task 1.2: <action>", prompt=<template>, model="sonnet")
```

**Parallel spawning:** Send independent tasks in single message.

---

## Phase 2: Handle Worker Status

### Status Protocol

| Status | Meaning | Master Action |
|--------|---------|---------------|
| **DONE** | Task complete | Proceed to spec review |
| **DONE_WITH_CONCERNS** | Complete but flagged doubts | Read concerns → address if correctness issue → proceed if observational |
| **BLOCKED** | Cannot proceed | Assess → provide context / break task / escalate |
| **NEEDS_CONTEXT** | Missing information | Provide missing info → re-dispatch |

### Handling BLOCKED

```
BLOCKED received
    ↓
Is it a context problem? → Provide context, re-dispatch same model
    ↓ no
Is task too complex? → Re-dispatch with more capable model
    ↓ no
Is task too large? → Break into smaller tasks
    ↓ no
Escalate to user
```

**Never** retry same task with same context after BLOCKED. Something must change.

### Silence Handling

| Condition | Action |
|-----------|--------|
| Subagent returns without status | Ask for status explicitly |
| Background agent silent 5+ min | Check if still running, send reminder |
| 3 retries with no progress | Mark blocked, escalate |

---

## Phase 3: Two-Stage Review

**Every completed task gets TWO reviews before proceeding.**

### Stage 1: Spec Compliance Review

Does the implementation match the spec?

| Check | Question |
|-------|----------|
| Requirements met | Does code do what spec says? |
| Constraints honored | Are limitations respected? |
| Nothing extra | No unrequested features? |
| Nothing missing | All spec items addressed? |

**If FAIL:** Subagent fixes spec gaps → re-review spec compliance.

### Stage 2: Code Quality Review

Is the implementation well-built?

| Check | Question |
|-------|----------|
| Tests exist | Test file created/updated? |
| Tests pass | All green? |
| No scope creep | Only listed files modified? |
| Code quality | Readable, maintainable? |
| Security | No obvious vulnerabilities? |

**If FAIL:** Subagent fixes quality issues → re-review quality.

### Review Flow

```
DONE received
    ↓
Spec Compliance Review
    ↓ PASS
Code Quality Review
    ↓ PASS
Mark task complete (br update --status done)
    ↓
Checkpoint if needed
    ↓
Next task
```

### Update Beads After Review

```bash
br update --id <task-id> --status done
br list  # verify state
```

---

## Phase 4: Context Management

### Checkpoint Triggers

Save checkpoint when:
- Every 3 tasks completed
- Before spawning 5+ parallel subagents
- After processing large file (500+ lines)
- When switching phases
- Before any risky operation

### Checkpoint Format

Update `history/<topic>/checkpoint.json`:

```json
{
  "phase": 1,
  "started_at": "2024-01-15T10:00:00Z",
  "updated_at": "2024-01-15T11:30:00Z",
  "completed_tasks": ["1.1", "1.2", "1.3"],
  "in_progress": ["1.4"],
  "blocked": [],
  "last_commit": "abc1234",
  "task_count": 4,
  "files_modified": ["src/auth.go", "src/auth_test.go"],
  "resume_from": "Task 1.4",
  "notes": "Task 1.4 in progress, waiting for subagent"
}
```

### Resume Protocol

If session interrupted:

1. Read `checkpoint.json`
2. Verify `last_commit` matches current HEAD
3. Check status of `in_progress` tasks
4. Resume from `resume_from` task

---

## Phase 5: Agent Mail (Large Codebases)

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

## Phase 6: Integration

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

| Criterion | Status | Evidence |
|-----------|--------|----------|
| {criterion 1} | PASS/FAIL | {file:line or test} |
| {criterion 2} | PASS/FAIL | {evidence} |
```

### Integration Commit

```bash
git add -A
git commit -m "feat(<scope>): complete phase 1 - <summary>"
```

### Final Checkpoint

```json
{
  "phase": 1,
  "status": "complete",
  "completed_tasks": ["1.1", "1.2", "1.3", "1.4"],
  "in_progress": [],
  "blocked": [],
  "last_commit": "final-hash",
  "exit_state": "verified"
}
```

---

## Phase 7: Handoff

```markdown
Phase 1 execution complete.

## Summary
- Tasks completed: {N}/{total}
- Tests passing: {count}
- Files changed: {list}
- Reviews: All passed (spec + quality)

## Exit State
All criteria from phase-1-spec.md verified.

## Next Steps
- [ ] User review
- [ ] Proceed to Phase 2 (if exists)
- [ ] Run duck:reviewing for final check

Artifacts:
- Checkpoint: history/<topic>/checkpoint.json
- Execution log: history/<topic>/phase-1-execution.md
```

---

## Anti-Patterns

| Don't | Do |
|-------|-----|
| Execute without task file | Load from planning artifacts |
| Skip either review stage | Both spec and quality reviews required |
| Retry BLOCKED without changes | Change context, model, or task scope |
| Let subagent scope creep | Strict task boundaries |
| Wait forever for silent agent | Check after 5 min, escalate after 3 retries |
| Skip checkpoints | Save every 3 tasks minimum |

---

## Red Flags

- No task file from planning
- Subagent modifying unlisted files
- Tests not written before implementation
- Skipping review "to save time"
- File conflicts between subagents
- BLOCKED status ignored
- No checkpoint for 5+ tasks
- Same error repeated 3+ times

---

## Quick Reference

### Worker Status

| Status | Action |
|--------|--------|
| DONE | → Spec review |
| DONE_WITH_CONCERNS | Read concerns → decide → review |
| BLOCKED | Change something → re-dispatch |
| NEEDS_CONTEXT | Provide info → re-dispatch |

### Review Order

```
1. Spec Compliance (matches requirements?)
2. Code Quality (well-built?)
```

### Checkpoint Triggers

```
- Every 3 tasks
- Before 5+ parallel spawns
- After large file processing
- Phase transitions
```

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

### Beads + Checkpoint Sync

After each task completion:
```bash
br update --id <task-id> --status done
# Then update checkpoint.json
```

---

## Output Summary

```
history/<topic>/
├── CONTEXT.md           (from research)
├── phase-plan.md        (from planning)
├── phase-1-spec.md      (from planning)
├── phase-1-tasks.md     (from planning)
├── phase-1-execution.md (from executing)
└── checkpoint.json      (state management)
```
