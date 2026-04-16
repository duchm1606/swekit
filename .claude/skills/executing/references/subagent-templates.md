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
4. If blocked, stop and report immediately

## Constraints
- No new dependencies without explicit approval
- No refactoring outside task boundaries
- No "improvements" beyond task scope
- Stop immediately if you encounter unexpected state

## Response Format (REQUIRED)
End your response with exactly this format:

**Status:** DONE | DONE_WITH_CONCERNS | BLOCKED | NEEDS_CONTEXT
**Files Changed:** {list with paths}
**Tests:** {X passed, Y failed}
**Commit:** {hash or "none"}
**Concerns/Blockers:** {description if applicable, "none" otherwise}
```

---

## Worker Status Reference

| Status | When to Use |
|--------|-------------|
| **DONE** | Task complete, all tests pass, committed |
| **DONE_WITH_CONCERNS** | Complete but you have doubts (correctness, scope, tech debt) |
| **BLOCKED** | Cannot proceed (missing info, unexpected state, dependency issue) |
| **NEEDS_CONTEXT** | Need specific information before starting |

---

## Model Selection

| Complexity | Model | Use When |
|------------|-------|----------|
| Simple | `model="haiku"` | 1-2 files, clear spec, isolated function |
| Standard | `model="sonnet"` | Multi-file, integration, typical tasks |
| Complex | `model="opus"` | Architecture, debugging, design judgment |

```
Agent(
  description="Task 1.1: Add validation helper",
  prompt="{template}",
  model="haiku"  # Simple isolated function
)

Agent(
  description="Task 1.2: Integrate auth middleware",
  prompt="{template}",
  model="sonnet"  # Multi-file integration
)
```

---

## Parallel Task Batch

When spawning multiple independent tasks:

```markdown
Agent(
  description="Task 1.1: Create user model",
  prompt="{standard template for 1.1}",
  model="sonnet"
)

Agent(
  description="Task 1.2: Create user repository interface", 
  prompt="{standard template for 1.2}",
  model="sonnet"
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

## Response Format (REQUIRED)
{same status format as standard template}
```

---

## Spec Compliance Review Subagent

First review stage - does implementation match spec?

```markdown
## Review Task: Spec Compliance

You are reviewing implementation for spec compliance. Be objective.

## Inputs

**Implementation Diff:**
{paste git diff output}

**Original Task:**
{paste task from phase-1-tasks.md}

**Exit Criteria:**
{paste from phase-1-spec.md}

## Your Task

Check ONLY spec compliance:

| Check | Question |
|-------|----------|
| Requirements | Does code do what spec says? |
| Constraints | Are limitations respected? |
| Nothing extra | No unrequested features added? |
| Nothing missing | All spec items addressed? |

## Response Format

**Verdict:** PASS | FAIL

**If FAIL, list each gap:**
- Gap 1: {what's missing or wrong}
- Gap 2: {what's extra or violates constraint}

**Summary:** {one sentence}
```

---

## Code Quality Review Subagent

Second review stage - is implementation well-built?

```markdown
## Review Task: Code Quality

You are reviewing code quality. Spec compliance already verified.

## Inputs

**Files Changed:**
{list of files}

**Git Diff:**
{paste diff}

**Test Results:**
{paste test output}

## Your Task

Check code quality:

| Check | Question |
|-------|----------|
| Tests exist | Test file created/updated? |
| Tests pass | All green? |
| Scope respected | Only listed files modified? |
| Code quality | Readable, maintainable? |
| Security | No obvious vulnerabilities? |
| Performance | No obvious issues? |

## Response Format

**Verdict:** APPROVED | NEEDS_FIXES

**Issues (if any):**
- P1 (blocking): {issue}
- P2 (should fix): {issue}
- P3 (minor): {issue}

**Summary:** {one sentence}
```

---

## Combined Review (Single Subagent)

For simpler tasks, combine both reviews:

```markdown
## Review Task: Full Review

Review implementation for Task {N}.{M}.

## Inputs
{diff, task, spec}

## Stage 1: Spec Compliance
- [ ] All requirements implemented
- [ ] Constraints honored
- [ ] Nothing extra added
- [ ] Nothing missing

## Stage 2: Code Quality
- [ ] Tests exist and pass
- [ ] Only listed files modified
- [ ] Code is readable
- [ ] No security issues

## Response Format

**Spec Compliance:** PASS | FAIL
**Code Quality:** APPROVED | NEEDS_FIXES
**Overall:** APPROVED | SPEC_GAPS | QUALITY_ISSUES | BOTH

**Issues:**
{list any issues found}
```

---

## Checkpoint Template

Include in long-running tasks:

```markdown
## Checkpoint Protocol

After completing this task:
1. Report your status (DONE/DONE_WITH_CONCERNS/BLOCKED/NEEDS_CONTEXT)
2. List files changed
3. Note any concerns for the checkpoint

The master agent will save checkpoint after receiving your report.
```
