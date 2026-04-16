---
name: duck:debugging
description: >-
  Use when encountering bugs, test failures, build errors, or unexpected behavior.
  Triggers: error messages, stack traces, "it doesn't work", flaky tests, crashes.
  Use BEFORE proposing fixes.
metadata:
  version: '1.0'
  dependencies: []
---

# Debugging

Resolve issues systematically. Do not guess — triage, reproduce, diagnose, then fix.

## Principles

- **Root cause first.** Symptom fixes are failures.
- **Reproduce before diagnose.** Can't fix what you can't trigger.
- **3-fix rule.** After 3 failed fixes, question the architecture.

---

## Process

```
Triage → Check Known → Reproduce → Root Cause → Hypothesis → Fix → Learn
                                       ↓
                                  3-fix gate
```

---

## Phase 1: Triage

Classify before investigating. Misclassifying wastes time.

| Type | Signals |
|------|---------|
| **Build** | Compilation error, type error, missing module |
| **Test** | Assertion mismatch, snapshot diff, timeout, flaky |
| **Runtime** | Crash, exception, segfault, undefined behavior |
| **Integration** | HTTP 4xx/5xx, env missing, API mismatch, auth |
| **Blocker** | Stuck process, circular dependency, conflict |

**Output:** `[TYPE] in [component]: [symptom]`

Example: `Build failure in auth/handler.go: undefined: ValidateToken`

---

## Phase 2: Check Known Patterns

**Before investigating, check if this is solved:**

```bash
# Check project learnings
cat history/learnings/patterns.md 2>/dev/null | grep -i "<keyword>"

# Check git for similar fixes
git log --oneline --grep="<keyword>" | head -5
```

**If match found:** Apply documented resolution. Skip to Phase 5 (Fix).

---

## Phase 3: Reproduce

### Run Exact Command

```bash
# Whatever failed — run it verbatim
go test ./auth/... -v 2>&1 | tee /tmp/debug.txt
```

**Capture error verbatim.** Line numbers and messages matter.

### Confirm Reproducibility

| Result | Action |
|--------|--------|
| Fails consistently | Proceed to Phase 4 |
| Intermittent | Classify as flaky, check race conditions |
| Cannot reproduce | Gather more context, check environment |

### Minimal Reproduction

- Can you trigger with one file?
- Does it fail in isolation?
- Is it environment-specific?

---

## Phase 4: Root Cause

Work through in order. Stop when you find the cause.

### 4a. Read Error Carefully

- Don't skip past errors
- Read stack traces completely
- Note line numbers, file paths, error codes

### 4b. Check Recent Changes

```bash
git log --oneline -10
git blame <file> -L <line>,<line>
git diff HEAD~3 -- <file>
```

Recent commit introduced failure? Fix is likely reverting or adjusting that change.

### 4c. Trace Data Flow

**When error is deep in call stack:**

```
Symptom → Immediate cause → What called this? → Keep tracing up → Source
```

Example:
```
Error: invalid token
  ← ValidateToken() received empty string
  ← GetToken() returned ""
  ← Config.Token not set
  ← env var missing   ← ROOT CAUSE
```

### 4d. Multi-Component Instrumentation

**When system has multiple layers:**

```bash
# Add logging at each boundary
echo "=== Layer 1: Input ===" && echo "Value: $INPUT"
echo "=== Layer 2: Process ===" && echo "Result: $RESULT"
echo "=== Layer 3: Output ===" && echo "Final: $FINAL"
```

Run once to find WHERE it breaks, then investigate that layer.

### 4e. Write Root Cause Statement

> Root cause: `<file>:<line>` — `<what is wrong and why>`

**If you cannot write this sentence, you don't have root cause. Do not proceed.**

---

## Phase 5: Hypothesis and Fix

### Form Hypothesis

State clearly: "I think X is the root cause because Y"

### Test Minimally

- SMALLEST possible change
- ONE variable at a time
- Don't fix multiple things at once

### 3-Fix Gate

| Fixes Tried | Action |
|-------------|--------|
| 1-2 | Continue hypothesis testing |
| 3+ | STOP. Question architecture. |

**After 3 failed fixes:**
- Is this pattern fundamentally sound?
- Should we refactor vs. keep patching?
- Discuss with user before Fix #4

### Fix Size Determines Approach

| Size | Approach |
|------|----------|
| Small (1-3 files) | Fix directly, commit |
| Substantial (cross-cutting) | Use duck:planning first |
| Decision violation | Report to user, don't silently patch |

### Verify Fix

```bash
# Rerun original failing command
<original-failing-command>

# Run broader test suite
go test ./... -v
```

**If verification fails:** Return to Phase 4 with new information.

---

## Phase 6: Learn

### Capture New Pattern

```markdown
## Pattern: <classification>
**Trigger:** <what causes this>
**Root cause:** <one sentence>
**Fix:** <what resolves it>
**Signal:** <how to recognize in future>
```

Save to: `history/learnings/patterns.md`

### Update If Known Pattern Failed

If documented resolution didn't work:
```markdown
⚠ Pattern '<name>' outdated as of <date> — <what changed>
```

---

## Red Flags — STOP

If you catch yourself thinking:

| Thought | Reality |
|---------|---------|
| "Quick fix for now" | Symptom fix = future rework |
| "Just try changing X" | Guessing wastes time |
| "Skip reproduction" | Can't fix what you can't trigger |
| "I'll test after" | Untested fixes don't stick |
| "One more fix attempt" (after 2+) | 3+ = architecture problem |
| "It's probably X" | Probably ≠ root cause |

**All mean: STOP. Return to Phase 3 or 4.**

---

## Rationalizations

| Excuse | Reality |
|--------|---------|
| "Issue is simple" | Simple issues have root causes too |
| "Emergency, no time" | Systematic is FASTER than thrashing |
| "I see the problem" | Seeing symptom ≠ understanding cause |
| "Just this once" | First fix sets the pattern |
| "Multiple fixes saves time" | Can't isolate what worked |

---

## Quick Reference

| Situation | First Action |
|-----------|--------------|
| Build fails | `git log --oneline -10` — check recent changes |
| Test fails | Run verbatim, capture exact output |
| Flaky test | Run 5× — check shared state, race conditions |
| Runtime crash | Read stack trace, find first line in your code |
| Integration error | Check env vars, then response body |
| Recurring issue | Check `history/learnings/patterns.md` |

---

## Handoff to Planning

When fix is substantial:

```markdown
Debugging complete.

**Root cause:** <statement from Phase 4e>
**Fix scope:** Substantial — requires planning

Recommend: Use duck:planning to break down the fix.

Artifacts:
- Debug notes: history/<topic>/debug-notes.md
```

---

## Output Summary

```
history/<topic>/
├── debug-notes.md      (from debugging)
└── ... planning artifacts if needed
```
