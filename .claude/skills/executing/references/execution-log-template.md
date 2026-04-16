# Execution Log Template

```markdown
# Phase {N} Execution Log: {Feature Name}

**Date:** YYYY-MM-DD
**From Planning:** history/<topic>/phase-{N}-tasks.md
**Status:** IN_PROGRESS | COMPLETE | BLOCKED

---

## Task Progress

| Task | Status | Subagent | Review | Commit |
|------|--------|----------|--------|--------|
| 1.1 | done | completed | PASS | abc123 |
| 1.2 | done | completed | PASS | def456 |
| 1.3 | in-progress | running | - | - |
| 1.4 | todo | - | - | - |

---

## Execution Timeline

### Task 1.1: {action}
**Started:** HH:MM
**Subagent:** Dispatched
**Result:** 
- Files: `path/to/file.go`, `path/to/file_test.go`
- Tests: 3 passing
- Commit: abc123

**Review:** PASS
- All files match task scope
- Tests cover functionality
- No scope creep

---

### Task 1.2: {action}
**Started:** HH:MM
**Subagent:** Dispatched
**Result:**
- Files: `path/to/other.go`
- Tests: 2 passing
- Commit: def456

**Review:** PASS

---

### Task 1.3: {action}
**Started:** HH:MM
**Subagent:** Running...
**Depends on:** Task 1.1, 1.2 (complete)

---

## Blockers

| Task | Blocker | Resolution |
|------|---------|------------|
| - | - | - |

---

## Agent Mail Log (if used)

| Time | Agent | Message |
|------|-------|---------|
| HH:MM | Task 1.1 | Reserved: path/to/file.go |
| HH:MM | Task 1.1 | Complete, released files |
| HH:MM | Task 1.2 | Reserved: path/to/other.go |

---

## Exit State Verification

| Criterion | Status | Evidence |
|-----------|--------|----------|
| {from spec} | PASS | {how verified} |
| {from spec} | PASS | {how verified} |

---

## Summary

**Tasks:** {completed}/{total}
**Tests:** {count} passing
**Commits:** {list}

**Files Changed:**
- `path/to/file1.go`
- `path/to/file2.go`
- `path/to/file_test.go`

**Next:** Phase {N+1} or User Review
```

---

## Quick Status Update

For ongoing updates during execution:

```markdown
## Execution Update - HH:MM

**Progress:** {N}/{total} tasks complete
**Current:** Task {X}.{Y} - {status}
**Blockers:** None | {description}

Recent:
- Task 1.1: PASS
- Task 1.2: PASS
- Task 1.3: Running...
```
