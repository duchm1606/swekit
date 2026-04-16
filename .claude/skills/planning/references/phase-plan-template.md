# Phase Plan Template

```markdown
# Phase Plan: {Feature Name}

**Date:** YYYY-MM-DD
**From Research:** history/<topic>/CONTEXT.md
**Scope:** {locked scope from Phase 0}

---

## Phase 1: {What Becomes True}

**Observable Change:** 
{What user/system can now do that they couldn't before}

**Why First:** 
{Dependency reason - why this must come before Phase 2}

**Demo:** 
{Simplest way to prove this phase works}

**Stories:**
1. {Story 1 - what happens}
2. {Story 2 - what happens}

---

## Phase 2: {What Becomes True}

**Observable Change:** 
{What user/system can now do}

**Why After Phase 1:** 
{What from Phase 1 this depends on}

**Demo:** 
{Simplest proof}

**Stories:**
1. {Story 1}
2. {Story 2}

---

## Phase 3: {What Becomes True} (if needed)

...

---

## Dependencies

```
Phase 1 ──► Phase 2 ──► Phase 3
   │            │
   └── {reason} └── {reason}
```

---

## Out of Scope

- {Explicitly excluded from all phases}
- {Deferred to future work}

---

## Risk Summary

| Phase | Risk Level | Reason |
|-------|------------|--------|
| Phase 1 | LOW/MED/HIGH | {reason} |
| Phase 2 | LOW/MED/HIGH | {reason} |
```

## Quality Checklist

- [ ] Every phase has observable outcome (not "setup" or "foundation")
- [ ] Plain language - PM can understand
- [ ] Phase 1 has obvious reason to be first
- [ ] Each phase has 2-4 stories max
- [ ] Dependencies are explicit
- [ ] Out of scope is documented
