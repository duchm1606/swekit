---
name: duck:brainstorming
description: >-
  Use when starting with a vague idea, exploring possibilities, or framing a problem.
  Triggers: "I'm thinking about...", "what if we...", "explore options for", unclear direction.
metadata:
  version: '1.0'
  dependencies: []
---

# Brainstorming

## Principles

- **Diverge before converge.** Explore widely, then narrow.
- **No bad ideas yet.** Judgment comes later.
- **Frame the problem.** Right question > right answer.

---

## When to Use

| Trigger | Use Brainstorming |
|---------|-------------------|
| "Best library for X" | No - go to research |
| "I'm thinking about..." | Yes |
| "What should we build?" | Yes |
| "Explore options for..." | Yes |
| "Not sure where to start" | Yes |
| "A vs B" | No - go to research |

---

## Process

```
Understand Intent → Explore Problem Space → Generate Options → Evaluate → Frame Research
```

---

## Phase 0: Understand Intent

**Ask one question at a time.**

| Question Type | Example |
|---------------|---------|
| Goal | "What would success look like?" |
| Constraint | "What can't change?" |
| Context | "What triggered this idea?" |
| Scope | "How big is this?" |

Lock: `"So the goal is {X} because {Y}. Confirmed?"`

---

## Phase 1: Explore Problem Space

### Map What Exists

```bash
# Project structure
Glob pattern="**/*.go" | head -20

# Recent focus areas
git log --oneline -10

# Related code
Grep pattern="{relevant term}"
```

### Identify Dimensions

```markdown
## Problem Dimensions

| Dimension | Current State | Possibilities |
|-----------|---------------|---------------|
| {aspect 1} | {what exists} | {what could be} |
| {aspect 2} | {what exists} | {what could be} |
```

---

## Phase 2: Generate Options

**Quantity over quality. No filtering yet.**

### Option Generation Techniques

| Technique | When to Use |
|-----------|-------------|
| Inversion | "What if we did opposite?" |
| Analogy | "How do others solve this?" |
| Decomposition | "What are the parts?" |
| Combination | "What if A + B?" |
| Elimination | "What if we didn't need X?" |

### Option Format

```markdown
## Options

### Option A: {name}
**Idea:** {one sentence}
**Enables:** {what becomes possible}
**Requires:** {dependencies/effort}

### Option B: {name}
...

### Option C: {name}
...
```

**Minimum 3 options. No evaluation yet.**

---

## Phase 3: Quick Evaluation

Now filter. Be opinionated.

```markdown
## Evaluation

| Option | Feasibility | Impact | Fit | Verdict |
|--------|-------------|--------|-----|---------|
| A | HIGH/MED/LOW | HIGH/MED/LOW | HIGH/MED/LOW | Keep/Drop |
| B | HIGH/MED/LOW | HIGH/MED/LOW | HIGH/MED/LOW | Keep/Drop |
| C | HIGH/MED/LOW | HIGH/MED/LOW | HIGH/MED/LOW | Keep/Drop |

**Feasibility:** Can we actually do this?
**Impact:** Does it meaningfully solve the problem?
**Fit:** Does it align with project/constraints?
```

### Present to User

```markdown
Explored {N} options. Recommending:

**Top pick:** Option {X}
**Why:** {rationale tied to goal}

**Runner-up:** Option {Y}
**Why:** {when this would be better}

Proceed with {X}?
```

**Wait for approval.**

---

## HARD GATE: Write DIRECTION.md

**After user approves the selected option, you MUST write the direction file.**

Use template: `references/direction-template.md`
Save to: `history/<topic>/DIRECTION.md`

```bash
# Create directory if needed
mkdir -p history/<topic>

# Write DIRECTION.md using template
```

**Do not continue until file is written and confirmed.**

---

## Decision Point

After writing DIRECTION.md, present to user:

```markdown
Direction written to: history/<topic>/DIRECTION.md

**Next step options:**

1. **Research** - Need to evaluate packages, libraries, or technical approaches
   - Use when: comparing options, need external info, unclear implementation path
   
2. **Planning** - Requirements are clear, ready to break into implementation tasks
   - Use when: approach is decided, just need to execute

Which path: research or planning?
```

**Wait for user response. Do not assume or proceed automatically.**

---

## Path A: Research (if more information needed)

If user chose research, create research handoff:

```markdown
# Research Direction: {topic}

**From Brainstorming:** {date}
**Selected Option:** {name}

## Problem Statement
{one paragraph: what we're solving and why}

## Research Questions
1. {specific question to answer}
2. {specific question to answer}
3. {specific question to answer}

## Known Constraints
- {constraint from brainstorming}

## Success Criteria
- {how we'll know research is complete}
```

This should already be in `history/<topic>/DIRECTION.md`.

```markdown
Brainstorming complete. Research path selected.

**Direction:** {selected option}
**Research questions:** {count}

Artifact: history/<topic>/DIRECTION.md

Ready to start research? Use duck:research.
```

---

## Path B: Planning (if requirements clear)

If user chose planning and no research needed:

```markdown
Brainstorming complete. Planning path selected.

**Direction:** {selected option}
**Requirements identified:** {count}

Artifact: history/<topic>/DIRECTION.md

Ready for implementation planning. Use duck:planning.
```

---

## Anti-Patterns

| Don't | Do |
|-------|-----|
| Jump to solutions | Explore problem first |
| Filter while generating | Generate then evaluate |
| One option only | Minimum 3 options |
| Vague directions | Specific research questions |
| Skip user approval | Gate before proceeding |
| Skip writing DIRECTION.md | Always write file before next step |
| Assume research vs planning | Ask user which path |
| Proceed without file written | Confirm file exists first |

---

## Red Flags

- Jumping straight to "best library for X"
- Only one option generated
- No user approval before proceeding
- Research questions too broad
- Skipping problem exploration
- **No DIRECTION.md file written**
- **Assuming path without asking user**
- **Going directly to implementation**

---

## Output Summary

```
history/<topic>/
└── DIRECTION.md (MUST be written before proceeding)
    ↓
    User chooses path:
    ├── duck:research (if more info needed)
    └── duck:planning (if requirements clear)
```
