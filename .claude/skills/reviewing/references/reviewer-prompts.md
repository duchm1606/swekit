# Reviewer Prompts

## Isolated Reviewer Subagent

When dispatching a reviewer subagent, use this template:

```markdown
## Review Task

You are reviewing implementation work against a spec. Be objective and thorough.

### Inputs

**Diff:**
{paste git diff output}

**Requirements (CONTEXT.md):**
{paste requirements section}

**Exit Criteria (phase-spec.md):**
{paste exit state and done-when criteria}

### Your Task

1. **Spec Compliance**
   - Is each requirement implemented?
   - Are constraints honored?
   - Is exit state achieved?

2. **Code Quality**
   - Correctness: logic errors, edge cases
   - Security: injection, auth, secrets
   - Performance: complexity, allocations
   - Maintainability: naming, coupling

3. **For Each Issue Found**
   - Severity: P1 (blocks) / P2 (should fix) / P3 (minor)
   - Location: file:line
   - Spec reference: which requirement/constraint
   - Effect: what breaks if not fixed
   - Fix direction: smallest credible fix

### Output Format

```markdown
## Spec Compliance
| Requirement | Status | Evidence |
|-------------|--------|----------|
| R1 | PASS/FAIL | {evidence} |

## Findings

### Finding 1: {title}
**Severity:** P1/P2/P3
**Location:** {file}:{line}
**Spec Reference:** {R1/constraint/code quality}
**Issue:** {plain language}
**Effect:** {what breaks}
**Fix:** {direction}

## Verdict
PASS / PASS WITH ISSUES / BLOCKED
```

### Rules

- Do NOT review from implementer's perspective
- Do NOT skip P1 findings to be nice
- Do NOT claim pass without evidence
- Every finding needs effect statement
```

---

## Specialist Reviewers (Optional)

For comprehensive review, dispatch multiple specialists:

### Security Reviewer

```markdown
Focus: OWASP top 10, injection, auth, secrets, data exposure

Check:
- Input validation at boundaries
- SQL/command injection
- XSS vulnerabilities
- Hardcoded secrets
- Auth bypass
- Data exposure in logs/errors
```

### Performance Reviewer

```markdown
Focus: Complexity, allocations, queries, caching

Check:
- O(n²) or worse loops
- Unnecessary allocations in hot paths
- N+1 query patterns
- Missing indexes
- Unbounded growth
- Cache invalidation
```

### Test Reviewer

```markdown
Focus: Coverage, edge cases, integration

Check:
- Happy path tested
- Error paths tested
- Edge cases covered
- Integration points tested
- Mocks appropriate
- Tests actually assert
```

---

## Finding Quality Checklist

Before finalizing a finding:

- [ ] Location is specific (file:line)
- [ ] Spec reference exists (R1, constraint, or "code quality")
- [ ] Issue explained in plain language
- [ ] Effect describes concrete failure scenario
- [ ] Fix is smallest credible direction
- [ ] Severity is calibrated (not all P1)

---

## Severity Calibration

**P1 - Blocking:**
- Security vulnerability
- Data corruption possible
- Spec requirement not met
- Feature doesn't work
- Missing critical functionality

**P2 - Should Fix:**
- Performance issue
- Missing error handling
- Test coverage gap
- Architectural concern
- Reliability risk

**P3 - Minor:**
- Style inconsistency
- Documentation gap
- Minor refactoring
- Code cleanup
- Naming improvement

**Default to P2 when uncertain.** Severity inflation wastes cycles.
