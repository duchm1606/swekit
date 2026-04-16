---
name: duck:prompt-leverage
description: >-
  Use when improving a raw prompt, creating reusable prompt templates, or adding
  structure to vague requests. Triggers: "make this prompt better", "upgrade prompt",
  "add structure to this request".
metadata:
  version: '1.0'
  dependencies: []
---

# Prompt Leverage

Strengthen a raw prompt into an execution-ready instruction set without changing intent.

## Principles

- **Preserve intent.** Improve structure, not meaning.
- **Proportional scaffolding.** Simple task = simple prompt.
- **Explicit completion.** Every prompt needs "done when".

---

## Process

```
Identify Intent → Classify Task → Select Blocks → Upgrade → Verify
```

---

## Phase 1: Identify Intent

Read the raw prompt. Answer:

| Question | Purpose |
|----------|---------|
| What's the real job? | Core objective |
| What's implicit? | Missing context |
| What could go wrong? | Risk level |

---

## Phase 2: Classify Task

| Type | Keywords | Focus |
|------|----------|-------|
| Coding | code, fix, implement, refactor | File inspection, validation, smallest change |
| Research | compare, find, analyze, sources | Evidence, citations, uncertainty |
| Writing | write, draft, email, blog | Audience, tone, structure |
| Review | review, audit, critique | Fresh eyes, severity, alternatives |
| Planning | plan, roadmap, strategy | Phases, dependencies, scope |
| Analysis | analyze, explain, diagnose | Root cause, breakdown |

---

## Phase 3: Framework Blocks

Use selectively. Not every prompt needs all blocks.

### Objective
```
State the task in 1-2 lines. Define success in observable terms.
```

### Context
```
Specify: files, URLs, constraints, assumptions, boundaries.
Say when to retrieve facts vs. assume.
```

### Work Style
```
- Intensity: Light / Standard / Deep
- Go broad first when system understanding matters
- Go deep where risk/complexity highest
- First-principles before changing things
```

### Tool Rules
```
When browsing, file inspection, tests, or external tools are required.
Prevent skipping prerequisite checks.
```

### Output Contract
```
Exact structure, tone, formatting, depth.
Required sections or schemas.
```

### Verification
```
Checks for: correctness, grounding, completeness, side effects.
Consider better alternatives.
```

### Done Criteria
```
What must be true before stopping.
```

---

## Phase 4: Intensity Levels

| Level | When | Blocks |
|-------|------|--------|
| Light | Simple edits, formatting | Objective + Output |
| Standard | Typical coding, research | All except deep verification |
| Deep | Debugging, architecture, high-stakes | All blocks, explicit verification |

---

## Phase 5: Task-Specific Adjustments

### Coding
- Emphasize: repo context, file inspection, smallest correct change
- Add: validation step, edge case check

### Research
- Emphasize: source quality, evidence gathering, synthesis
- Add: uncertainty acknowledgment, citations

### Writing
- Emphasize: audience, tone, structure constraints
- Add: revision criteria

### Review
- Emphasize: fresh-eyes critique, failure modes
- Add: severity levels, alternatives

---

## Output Modes

| Mode | When |
|------|------|
| Inline upgrade | Just the improved prompt |
| Upgrade + rationale | Prompt + list of improvements |
| Template extraction | Reusable fill-in-the-blank |
| Hook spec | Auto-upgrade before execution |

---

## Upgrade Template

```markdown
**Objective:**
- {task in 1-2 lines}
- Success = {observable outcome}

**Context:**
- {files/constraints/assumptions}
- {what to retrieve vs. assume}

**Work Style:**
- Task type: {coding/research/writing/review/planning/analysis}
- Intensity: {Light/Standard/Deep}
- {specific approach guidance}

**Tool Rules:**
- {when/how to use tools}

**Output Contract:**
- {structure, format, depth}

**Verification:**
- {what to check before finalizing}

**Done Criteria:**
- {stop when X is true}
```

---

## Quality Bar

Before finalizing, check:

| Check | Pass? |
|-------|-------|
| Matches original intent | |
| No unnecessary ceremony | |
| Right verification level | |
| Clear definition of done | |

If prompt is already strong, say so. Minimal edits only.

---

## Anti-Patterns

| Don't | Do |
|-------|-----|
| Over-specify simple tasks | Match scaffolding to complexity |
| Rewrite user's language | Preserve tone, add structure |
| Add all blocks always | Select relevant blocks |
| Vague quality words | Concrete completion criteria |

---

## Example

**Raw prompt:**
```
Fix the login bug
```

**Upgraded (Standard intensity):**
```markdown
**Objective:**
- Fix the login bug that prevents users from authenticating
- Success = users can log in without errors

**Context:**
- Inspect auth-related files first
- Check recent commits for related changes

**Work Style:**
- Task type: coding
- Intensity: Standard
- Understand the auth flow before changing code

**Tool Rules:**
- Read the failing code path before proposing fixes
- Run existing tests after changes

**Verification:**
- Test the fix locally
- Check for edge cases (empty password, special chars)

**Done Criteria:**
- Login works for normal and edge cases
- No regressions in existing tests
```
