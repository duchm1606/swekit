---
name: duck:research
description: >-
  Use when choosing packages, evaluating libraries, or comparing technical options.
  Triggers: "best library for X", "A vs B", package evaluation, dependency decisions.
metadata:
  version: '1.1'
  dependencies:
    - id: beads-cli
      kind: command
      command: br
      missing_effect: degraded
    - id: context7
      kind: mcp_server
      server_names: [context7]
      missing_effect: degraded
---

# Research

## Principles

- **Training = Hypothesis.** Verify before asserting.
- **Be opinionated.** "Use X because Y" not "Consider X or Y."
- **Honest uncertainty.** LOW confidence is valuable.

---

## Scope Tiers

| Tier | Trigger | Output |
|------|---------|--------|
| **Quick** | Known category, "just pick one" | Recommendation |
| **Standard** | Comparison needed, moderate risk | COMPARISON.md |
| **Deep** | Strategic dependency, unknown domain | Parallel agents + SUMMARY.md |

**Default: Standard.**

---

## Process

```
Explore → Clarify → Propose Approaches → Research → Compare → Gate → Recommend → Planning
```

---

## Phase 0: Explore

**Before asking questions, understand the project.**

### Check

```bash
# Project structure
Glob pattern="**/*.go" | head -20
Glob pattern="**/go.mod"

# Recent changes
git log --oneline -10
git diff --stat HEAD~5

# Existing patterns
Grep pattern="import.*{related}" type="go"
```

### Gather

| Check | Tool |
|-------|------|
| File structure | Glob |
| Dependencies | Read go.mod / package.json |
| Recent commits | git log |
| Existing patterns | Grep |
| Docs | Read README, docs/ |

### Output

Brief summary of project context before asking questions.

---

## Phase 1: Clarify

**One question at a time. Wait for response.**

| Domain | Key Probes |
|--------|------------|
| UI | Bundle size, design system, SSR |
| DATA | TypeScript, performance, existing ORM |
| INFRA | Speed vs features, toolchain fit |
| NETWORK | Environment, features, error handling |
| UTIL | Tree-shaking, maintenance |

Lock each: `"C{N}: {summary}. Confirmed?"`

---

## Phase 2: Propose Approaches

**Present 2-3 approaches BEFORE deep research.**

```markdown
## Approaches

### Approach A: {name}
**Description:** {one sentence}
**Trade-offs:**
- Pro: {advantage}
- Con: {disadvantage}
**Best if:** {condition}

### Approach B: {name}
**Description:** {one sentence}
**Trade-offs:**
- Pro: {advantage}
- Con: {disadvantage}
**Best if:** {condition}

### Approach C: {name} (optional)
...

## Recommendation
**Suggested:** Approach {X}
**Why:** {rationale tied to project context + constraints}

Proceed with this approach?
```

**Wait for user approval before deep research.**

---

## Phase 3: CONTEXT.md

After approach approved, lock constraints:

```markdown
# Research Context: {topic}

**Date:** YYYY-MM-DD | **Scope:** Standard | **Domain:** DATA
**Approach:** {selected approach}

## Constraints
- **C1:** {constraint}

## Discretion
- {areas to decide}

## Out of Scope
- {excluded}
```

**MUST WRITE:** `history/<topic>/CONTEXT.md`

```bash
mkdir -p history/<topic>
# Write CONTEXT.md now - do not proceed to research without file written
```

---

## Phase 4: Research

### Tool Priority

| Tool | Trust |
|------|-------|
| Context7 | HIGH |
| WebSearch | LOW — verify |
| `npm view` / `go list` | AUTHORITATIVE |

### Codebase Search

| Tool | Use For |
|------|---------|
| Grep | Content search |
| Glob | File discovery |
| LSP | Symbol intelligence |

### Claim Tags

| Tag | Meaning |
|-----|---------|
| `[VERIFIED]` | Authoritative — lock immediately |
| `[CITED: url]` | High confidence — reviewable |
| `[ASSUMED]` | **Requires verification** |

---

## Phase 5: Comparison Matrix

```markdown
| Option | Pros | Cons | Complexity | Recommendation |
|--------|------|------|------------|----------------|
| pkg@v | pros | cons | impact — Risk: X | Rec if {condition} |
```

**Rules:**
- Complexity = impact + risk (never time)
- Recommendation = conditional

**MUST WRITE:** `history/<topic>/COMPARISON.md`

```bash
# Write COMPARISON.md now - do not proceed to gate without file written
```

---

## Phase 6: Research Gate

Block until `## Open Questions` resolved.

---

## Phase 7: Recommendation

```markdown
## Recommendation

**Winner:** `{package}@{version}`

| Metric | Value | Source |
|--------|-------|--------|
| Version | x.y.z | [VERIFIED] |

**Why:** {rationale tied to constraints}

**Runner-up:** `{alt}` — {when to use instead}
```

### Transition

Research → Planning → Spec → Implementation

**Never skip to implementation.**

---

## Phase 8: Deep Mode

Spawn 4 parallel agents:

| Agent | Output |
|-------|--------|
| Stack | STACK.md |
| Features | FEATURES.md |
| Comparison | COMPARISON.md |
| Pitfalls | PITFALLS.md |

**MUST WRITE (each agent):**
- `history/<topic>/STACK.md`
- `history/<topic>/FEATURES.md`
- `history/<topic>/COMPARISON.md`
- `history/<topic>/PITFALLS.md`

Synthesize all outputs into:

**MUST WRITE:** `history/<topic>/SUMMARY.md`

```bash
# Each agent writes its file
# Master synthesizes into SUMMARY.md
# Do not proceed to gate without all files written
```

---

## Quick Reference

**Quick:** explore → pick → recommend

**Standard:** explore → clarify → propose → research → compare → gate → recommend

**Deep:** explore → clarify → propose → parallel agents → synthesize → gate → recommend

---

## Anti-Patterns

| Don't | Do |
|-------|-----|
| Skip explore | Always check project context first |
| Jump to research | Propose approaches, get approval |
| "It depends" | Pick winner + runner-up |
| Trust training | Verify sources |
| **Output comparison in conversation** | **Write to history/<topic>/COMPARISON.md** |
| **Describe CONTEXT.md format** | **Actually write the file** |
| **Proceed without files written** | **MUST WRITE before next phase** |

---

## Red Flags

- Skipping explore phase
- No approach proposal before research
- `[ASSUMED]` becoming locked decision
- Unresolved gate questions
- **CONTEXT.md not written before Phase 4**
- **COMPARISON.md not written before gate**
- **Deep mode agents not writing their files**
- **Research exists only in conversation**

---

## References

- `references/gray-area-probes.md`
- `references/comparison-template.md`
- `references/thinking-models.md`
- `references/tool-commands.md`
