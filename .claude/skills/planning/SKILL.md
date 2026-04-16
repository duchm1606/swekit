---
name: duck:planning
description: >-
  Use when ready to plan implementation. Two entry points: after duck:research 
  completes, OR with direct feature spec (requirements + behavior defined).
  Triggers: "create implementation plan", "break down into tasks", "implement feature X".
metadata:
  version: '1.1'
  dependencies:
    - id: duck:research
      kind: skill
      missing_effect: degraded
---

# Planning

## Principles

- **Plain language first.** If you can't explain it simply, you don't understand it.
- **What becomes true.** Every phase describes observable change, not technical activity.
- **No placeholders.** Every task has exact file paths, code, and commands.

---

## Entry Points

| Entry | When to Use | Input |
|-------|-------------|-------|
| From research | After package/library decision | CONTEXT.md from research |
| From feature spec | Clear requirements, no package decision | User-provided spec |

---

## Process

```
[Gather Requirements] → Scope Challenge → Phase Plan → GATE → Phase Spec → Tasks → Handoff
        ↑
  Only for direct entry
```

---

## Phase -1: Gather Requirements (Direct Entry Only)

**Skip if coming from duck:research.**

### Explore Project

```bash
# Project structure
Glob pattern="**/*.go" | head -20

# Recent changes
git log --oneline -10

# Related code
Grep pattern="{feature-related term}"
```

### Clarify Requirements

Ask one at a time:

| Question | Purpose |
|----------|---------|
| "What should this feature do?" | Core behavior |
| "Who/what triggers it?" | Entry point |
| "What's the expected output?" | Exit criteria |
| "What can't change?" | Constraints |
| "What's out of scope?" | Boundaries |

Lock each: `"R{N}: {requirement}. Confirmed?"`

### Create CONTEXT.md

```markdown
# Planning Context: {feature}

**Date:** YYYY-MM-DD
**Entry:** Direct feature spec

## Requirements
- **R1:** {requirement}
- **R2:** {requirement}

## Constraints
- {constraint}

## Out of Scope
- {excluded}

## Project Context
- Language: {detected}
- Related files: {found}
```

Save: `history/<topic>/CONTEXT.md`

---

## Phase 0: Scope Challenge

**Before expanding, narrow.** Use CONTEXT.md (from research or Phase -1).

Ask (pick relevant):
1. What's the smallest version that proves the concept?
2. What can we defer to a later phase?
3. What existing code can we reuse?
4. What's explicitly out of scope?

Lock scope: `"Scope: {summary}. Confirmed?"`

---

## Phase 1: Phase Plan

Break feature into 2-4 phases.

```markdown
# Phase Plan: {feature}

## Phase 1: {what becomes true}
**Observable change:** {user/system can now do X}
**Why first:** {dependency reason}
**Demo:** {simplest proof}

## Phase 2: {what becomes true}
...

## Dependencies
Phase 2 requires Phase 1 because {reason}.
```

### Phase Rules

- Every phase = observable capability, not technical layer
- "Foundation" or "setup" phases must still have observable outcome
- If phase has 5+ stories, split it
- Plain language test: can a PM understand it?

Save: `history/<topic>/phase-plan.md`

---

## HARD GATE: User Approval

Present phase plan summary:
```
Feature: {name}
Phases: {count}
1. {phase 1 summary}
2. {phase 2 summary}

Proceed with Phase 1 spec?
```

**Do not continue without explicit approval.**

---

## Phase 2: Current Phase Spec

Only after approval, detail current phase.

```markdown
# Phase 1 Spec: {phase name}

## What Changes
{Observable outcome in 1-2 sentences}

## Entry State
{What must be true before starting}

## Exit State
{What must be true when done}

## Stories

### Story 1: {what happens}
**Why now:** {ordering reason}
**Creates:** {artifact/capability}
**Done when:** {verification}

### Story 2: {what happens}
...

## Out of Scope
- {explicitly excluded}
```

### Story Rules

- Story 1 must have obvious reason to be first
- Each story unlocks next story or closes exit state
- "Done when" must be verifiable

Save: `history/<topic>/phase-1-spec.md`

---

## Phase 3: Task Breakdown

Convert stories to executable tasks.

### Task Format

````markdown
### Task 1.1: {action}

**Files:**
- Create: `exact/path/to/file.go`
- Modify: `exact/path/to/existing.go:45-60`
- Test: `exact/path/to/file_test.go`

**Steps:**

- [ ] **Step 1: Write failing test**
```go
func TestSpecificBehavior(t *testing.T) {
    result := Function(input)
    assert.Equal(t, expected, result)
}
```

- [ ] **Step 2: Run test, verify failure**
```bash
go test ./path/... -run TestSpecificBehavior -v
# Expected: FAIL
```

- [ ] **Step 3: Implement**
```go
func Function(input Type) Result {
    return expected
}
```

- [ ] **Step 4: Run test, verify pass**
```bash
go test ./path/... -run TestSpecificBehavior -v
# Expected: PASS
```

- [ ] **Step 5: Commit**
```bash
git add path/to/files
git commit -m "feat: add specific feature"
```
````

### Task Rules

| Rule | Requirement |
|------|-------------|
| Granularity | Each step = 2-5 minutes |
| File paths | Exact, never "appropriate location" |
| Code | Complete, never "implement logic here" |
| Commands | Exact with expected output |
| No placeholders | TBD, TODO, "similar to" = failure |

Save: `history/<topic>/phase-1-tasks.md`

---

## Anti-Patterns

| Don't | Do |
|-------|-----|
| "Set up foundation" | "User can create account" |
| "Implement validation" | Show exact validation code |
| "Add tests" | Show exact test code |
| "Similar to Task 1" | Repeat the code |
| "Handle edge cases" | List specific cases with code |

---

## Red Flags

- Direct entry without gathering requirements
- Phase without observable outcome
- Task without exact file paths
- Step without code block
- "Foundation" phase with no demo
- Skipping user approval gate
- Placeholders in any form

---

## Output Summary

```
history/<topic>/
├── CONTEXT.md        (from research OR Phase -1)
├── COMPARISON.md     (from research, if applicable)
├── phase-plan.md     (Phase 1)
├── phase-1-spec.md   (Phase 2)
└── phase-1-tasks.md  (Phase 3)
```

---

## Handoff

After tasks complete:
```
Planning complete for {feature}.

Artifacts:
- Phase plan: history/<topic>/phase-plan.md
- Phase 1 spec: history/<topic>/phase-1-spec.md  
- Phase 1 tasks: history/<topic>/phase-1-tasks.md

Ready for execution. Tasks are TDD-structured with exact file paths.
```
