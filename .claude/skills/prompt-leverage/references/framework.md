# Prompt Leverage Framework

## Block Definitions

### Objective

State the task in one or two lines. Define success in observable terms.

**Good:**
```
Fix the authentication bug that causes 401 errors on valid tokens.
Success = valid tokens authenticate without errors.
```

**Bad:**
```
Make auth better.
```

### Context

Specify relevant files, URLs, constraints, assumptions, and information boundaries.

| Element | Example |
|---------|---------|
| Files | `auth/handler.go`, `auth/middleware.go` |
| Constraints | "Must not change API contract" |
| Assumptions | "Using JWT tokens" |
| Boundaries | "Only fix auth, don't refactor" |

### Work Style

Control how the agent approaches the task.

| Directive | When |
|-----------|------|
| Go broad first | System understanding matters |
| Go deep | High risk/complexity areas |
| First-principles | Before changing existing code |
| Fresh eyes | Non-trivial tasks, final review |

### Tool Rules

Define when tools are required. Prevent skipping prerequisites.

| Task Type | Tool Rules |
|-----------|------------|
| Coding | Inspect files first, validate changes |
| Research | Retrieve evidence, don't guess facts |
| Review | Read context before critiquing |

### Output Contract

Define exact structure, tone, formatting, depth.

| Element | Example |
|---------|---------|
| Structure | "Summary, changes, validation notes" |
| Tone | "Technical, concise" |
| Format | "Markdown with code blocks" |
| Depth | "Include edge cases" |

### Verification

Require checks before finalizing.

| Check | Description |
|-------|-------------|
| Correctness | Does it actually work? |
| Grounding | Based on evidence, not assumption? |
| Completeness | All requirements addressed? |
| Side effects | Any unintended consequences? |
| Alternatives | Better approach available? |

### Done Criteria

Define what must be true before stopping.

**Good:**
```
Stop when: tests pass, edge cases handled, no regressions
```

**Bad:**
```
Stop when it looks good
```

---

## Intensity Guide

| Level | Trigger Words | Blocks to Use |
|-------|---------------|---------------|
| Light | "quick", "simple", "just" | Objective + Output |
| Standard | default, typical tasks | All except deep verification |
| Deep | "careful", "thorough", "critical", "production" | All blocks, explicit verification |

---

## Task-Type Templates

### Coding

```markdown
**Objective:** {specific fix/feature}
**Context:** Inspect {files} first. Constraint: {limitation}.
**Work Style:** Understand before changing. Smallest correct change.
**Tool Rules:** Read code, run tests, validate locally.
**Output:** Summary + code + validation notes.
**Verification:** Tests pass, edge cases, no regressions.
**Done:** Working code, validated, committed.
```

### Research

```markdown
**Objective:** {research question}
**Context:** Sources: {where to look}. Boundaries: {scope}.
**Work Style:** Evidence-based. Acknowledge uncertainty.
**Tool Rules:** Retrieve facts, cite sources.
**Output:** Findings + evidence + bottom line.
**Verification:** Claims grounded, sources reliable.
**Done:** Question answered with evidence.
```

### Writing

```markdown
**Objective:** {writing task}
**Context:** Audience: {who}. Tone: {style}. Length: {constraint}.
**Work Style:** Match voice to audience.
**Output:** Polished copy in requested format.
**Verification:** Tone consistent, structure clear.
**Done:** Ready to publish/send.
```

### Review

```markdown
**Objective:** {what to review}
**Context:** Scope: {boundaries}. Focus: {priorities}.
**Work Style:** Fresh eyes. Distinguish confirmed vs. possible issues.
**Output:** Findings by severity + recommendations.
**Verification:** Issues verified, not assumed.
**Done:** Actionable feedback provided.
```

---

## Upgrade Heuristics

1. Add blocks only when they improve execution
2. Don't inflate one-liners into giant specs
3. Preserve user language where possible
4. Prefer concrete criteria over vague adjectives
5. Match scaffolding to actual complexity

---

## Upgrade Rubric

An upgraded prompt is good when it:

| Criterion | Check |
|-----------|-------|
| Preserves original intent | User's goal unchanged |
| Reduces ambiguity | Fewer interpretations possible |
| Sets right depth | Matches task complexity |
| Defines output clearly | Know what "done" looks like |
| Includes verification | Appropriate for risk level |
| Has stop condition | Agent knows when to finish |
