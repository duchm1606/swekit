# Subagent Templates

## Standard Task Subagent

```markdown
## Task
### Task {N}.{M}: {Specific Action}

**Story:** Story {N} - {story name}
**Creates:** {What this task produces}

**Files:**
- Create: `exact/path/to/new_file.go`
- Modify: `exact/path/to/existing.go:45-60`
- Test: `exact/path/to/file_test.go`

**Steps:**
{paste exact steps from task file}

## Context
- Working directory: {absolute path}
- Related files: {list files this task touches}
- Dependencies: {any tasks this depends on - should be complete}
- Exit criteria: {specific verification from spec}

## Instructions
1. Follow TDD steps exactly as written in the task
2. Do not modify files outside the listed scope
3. Create atomic commits after each passing test
4. If blocked, stop and report the blocker

## Constraints
- No new dependencies without explicit approval
- No refactoring outside task boundaries
- No "improvements" beyond task scope
- Stop immediately if you encounter unexpected state

## Deliverables
Report back with:
- Files changed (with paths)
- Tests written and status
- Commits made (with hashes)
- Any blockers or concerns
```

---

## Parallel Task Batch

When spawning multiple independent tasks:

```markdown
Agent(
  description="Task 1.1: Create user model",
  prompt="{standard template for 1.1}"
)

Agent(
  description="Task 1.2: Create user repository interface", 
  prompt="{standard template for 1.2}"
)
```

**Key:** Send in single message for parallel execution.

---

## Sequential Task Chain

When tasks have dependencies:

```markdown
# First, complete Task 1.1
Agent(description="Task 1.1: Create schema", prompt="{template}")

# Wait for result, review, then:
Agent(description="Task 1.2: Create migrations", prompt="{template + 1.1 result context}")
```

---

## Large Codebase Subagent (with Agent Mail)

```markdown
## Task
{standard task content}

## Coordination
This task is part of a parallel execution. Use Agent Mail for coordination:

### Before Starting
```bash
mcp__agent-mail reserve --files "{list of files}"
```

### After Each Commit
```bash
mcp__agent-mail broadcast --message "Task {N}.{M}: completed {step}, modified {files}"
```

### On Completion
```bash
mcp__agent-mail release --files "{list of files}"
mcp__agent-mail broadcast --message "Task {N}.{M}: COMPLETE"
```

### If Blocked
```bash
mcp__agent-mail broadcast --message "Task {N}.{M}: BLOCKED - {reason}"
```
Stop and wait for master agent response.

## Instructions
{standard instructions}
```

---

## Review Subagent

When master needs deep review of complex work:

```markdown
## Review Task
Review the work completed by subagent for Task {N}.{M}.

## Files to Review
{list of changed files}

## Checklist
- [ ] Only listed files were modified
- [ ] Tests exist and cover the functionality
- [ ] Tests actually pass (`go test ./...`)
- [ ] No scope creep (extra features, refactoring)
- [ ] Commits are atomic and well-messaged
- [ ] Code follows project conventions
- [ ] No security issues introduced

## Context
- Original task: {paste task}
- Expected outcome: {from spec}
- Subagent report: {paste subagent's completion report}

## Deliverables
Report:
- PASS / MINOR ISSUES / MAJOR ISSUES / BLOCKED
- Specific issues found (if any)
- Recommended fixes (if minor)
- Whether re-dispatch is needed (if major)
```
