---
name: duck:reviewing
description: >-
  Use after implementation completes to verify work against spec and context.
  Triggers: "review this", "check the implementation", task/phase complete, before merge.
metadata:
  version: '1.0'
  dependencies:
    - id: duck:planning
      kind: skill
      missing_effect: degraded
---

# Reviewing

Verify implementation against spec and context. Catch what escaped execution.

## Principles

- **Spec first.** Compare against CONTEXT.md and phase-spec, not assumptions.
- **Effect matters.** Every issue needs "what breaks if not fixed".
- **Evidence required.** No claims without verification commands run.

---

## Process

```
Load Context → Spec Compliance → Code Quality → Artifact Verification → Report
```

---

## Phase 1: Load Context

### Required Inputs

```bash
# From planning
Read history/<topic>/CONTEXT.md
Read history/<topic>/phase-1-spec.md

# Current diff
git diff <base>..<head>
```

### Extract Review Criteria

From CONTEXT.md:
- Requirements (R1, R2...)
- Constraints
- Out of scope

From phase-spec.md:
- Exit state criteria
- Story "done when" conditions

---

## Phase 2: Spec Compliance

**Compare implementation against locked requirements.**

### Checklist

| Check | Source | Method |
|-------|--------|--------|
| Each requirement implemented | CONTEXT.md R1, R2... | Code inspection |
| Constraints honored | CONTEXT.md constraints | Code inspection |
| Exit state achieved | phase-spec.md | Functional test |
| Stories complete | phase-spec.md "done when" | Verification command |

### Compliance Report

```markdown
## Spec Compliance

| Requirement | Status | Evidence |
|-------------|--------|----------|
| R1: {desc} | PASS/FAIL | {file:line or test output} |
| R2: {desc} | PASS/FAIL | {evidence} |

| Constraint | Status | Evidence |
|------------|--------|----------|
| {constraint} | HONORED/VIOLATED | {evidence} |
```

**If any FAIL/VIOLATED:** Create P1 finding. Do not proceed until acknowledged.

---

## Phase 3: Code Quality Review

### Review Dimensions

| Dimension | Check |
|-----------|-------|
| **Correctness** | Logic errors, edge cases, error handling |
| **Security** | Injection, auth, secrets, data exposure |
| **Performance** | O(n²) loops, unnecessary allocations, N+1 queries |
| **Maintainability** | Naming, complexity, coupling |
| **Test coverage** | Missing tests, edge cases, integration gaps |

### Isolated Reviewer Context

**Critical:** Review only sees:
1. Git diff
2. CONTEXT.md
3. phase-spec.md

Do NOT include session history or implementation notes. Objectivity requires seeing only work product.

---

## Phase 4: Artifact Verification

**3-level check on every deliverable from spec.**

### Level 1: EXISTS

```bash
ls <expected-file-path>
```

### Level 2: SUBSTANTIVE

Scan for anti-patterns:
```
return null / return {} / return []
Empty handlers: onClick={() => {}}
TODO / FIXME / PLACEHOLDER
console.log-only implementations
API routes returning static data
```

### Level 3: WIRED

```bash
# Check if actually imported/used
Grep pattern="<component/function>" path="src/"
```

### Artifact Report

| Artifact | L1 Exists | L2 Substantive | L3 Wired | Status |
|----------|-----------|----------------|----------|--------|
| {file} | YES/NO | YES/STUB | YES/NO | OK/P1/P2 |

---

## Phase 5: Finding Format

### Severity Levels

| Priority | Criteria | Action |
|----------|----------|--------|
| **P1** | Security, data corruption, spec violation, missing feature | Blocks merge |
| **P2** | Performance, architecture, reliability | Should fix |
| **P3** | Minor improvements, cleanup, style | Record for later |

### Finding Template

```markdown
## Finding: {title}

**Severity:** P1/P2/P3
**Location:** {file}:{line}
**Spec Reference:** {R1/D2/Story 1 or "code quality"}

### Issue
{Plain language: what the code does today}

### Effect
{What breaks if not fixed — concrete scenario}

### Evidence
{Code snippet or command output}

### Fix
{Smallest credible fix direction}
```

---

## Phase 6: Review Report

### Summary Format

```markdown
# Review Report: {feature}

**Date:** YYYY-MM-DD
**Scope:** {files reviewed}
**Base:** {commit}
**Head:** {commit}

## Spec Compliance
- Requirements: {N}/{total} pass
- Constraints: {N}/{total} honored
- Exit state: ACHIEVED/NOT ACHIEVED

## Findings Summary
- P1 (blocking): {count}
- P2 (should fix): {count}
- P3 (minor): {count}

## P1 Findings (if any)
{list with full details}

## P2 Findings
{list}

## P3 Findings
{list}

## Artifacts
{3-level verification table}

## Verdict
PASS / PASS WITH ISSUES / BLOCKED

## Next Steps
- [ ] {action items}
```

---

## Gates

### P1 Gate

**If any P1 findings exist:**
- STOP
- Present to user
- Do not proceed until acknowledged
- Fix before merge

### Verification Gate

**Before ANY completion claim:**
1. Run verification command
2. Read full output
3. Confirm exit code
4. THEN claim result

---

## Quick Reference

| Situation | Action |
|-----------|--------|
| Spec requirement missing | P1 finding |
| Constraint violated | P1 finding |
| Stub/placeholder found | P1 finding |
| Security issue | P1 finding |
| Performance concern | P2 finding |
| Missing test | P2 finding |
| Style/cleanup | P3 finding |
| Artifact exists but not wired | P2 finding |

---

## Anti-Patterns

| Don't | Do |
|-------|-----|
| Review from session memory | Review from diff + spec only |
| "Looks good" without evidence | Run verification commands |
| Skip artifact verification | Check all 3 levels |
| Vague findings | Specific file:line + effect |
| All P1 severity | Calibrate: P2 is default |

---

## Red Flags

- Reviewing without CONTEXT.md
- Claiming pass without running tests
- No spec reference on findings
- Missing "effect" on findings
- P1 findings and continuing
- Artifact exists but is stub
- Skipping artifact verification

---

## Handoff

After review complete:

```markdown
Review complete for {feature}.

**Verdict:** {PASS/PASS WITH ISSUES/BLOCKED}

Findings:
- P1: {count} (blocking)
- P2: {count}
- P3: {count}

{If BLOCKED: must fix P1 before proceeding}
{If PASS WITH ISSUES: recommend fixing P2 before merge}
{If PASS: ready for merge}

Artifacts:
- Review report: history/<topic>/review-report.md
```
