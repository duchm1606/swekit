# Phase Plan: Skill Gate Enforcement

**Date:** 2026-04-16
**From Brainstorming:** history/skill-gate-enforcement/DIRECTION.md
**Scope:** Fix brainstorming and planning skills to enforce file outputs and decision gates

---

## Phase 1: Add Brainstorming Artifacts

**Observable Change:** 
Brainstorming skill produces DIRECTION.md file and stops at decision gate (research vs planning)

**Why First:** 
Brainstorming is the entry point. If it doesn't produce artifacts, planning has nothing to consume.

**Demo:** 
Run brainstorming, see DIRECTION.md created, see prompt asking "Research or Planning?"

**Stories:**
1. Create DIRECTION.md template in references
2. Update SKILL.md with hard gate and decision fork

---

## Phase 2: Fix Planning File Outputs

**Observable Change:** 
Planning skill writes phase-plan.md, phase-X-spec.md, phase-X-tasks.md to history/<topic>/

**Why After Phase 1:** 
Planning consumes brainstorming output. Fix the producer before the consumer.

**Demo:** 
Run planning, see files created in history/<topic>/, context persists to next conversation

**Stories:**
1. Add MUST WRITE enforcement sections to planning SKILL.md
2. Ensure each phase has explicit file write instruction

---

## Phase 3: Enforce Subagent Approach in Executing

**Observable Change:**
Executing skill enforces subagent usage, prevents direct implementation, commits after each task completion.

**Why After Phase 2:**
Executing consumes planning output. Fix the flow first (brainstorming → planning), then fix execution.

**Demo:**
Run executing, see subagent dispatched for each task, see commit after each task, easy to revert individual tasks.

**Stories:**
1. Add hard gate enforcing subagent usage
2. Add "commit after each task" requirement
3. Add anti-patterns for direct implementation

---

## Phase 4: Research Skill File Enforcement

**Observable Change:**
Research skill writes CONTEXT.md, COMPARISON.md, SUMMARY.md with MUST WRITE enforcement.

**Why After Phase 3:**
Research feeds into planning. Fix execution first, then fix the research input.

**Demo:**
Run research, see CONTEXT.md written before deep research, COMPARISON.md after evaluation.

**Stories:**
1. Add MUST WRITE for CONTEXT.md
2. Add MUST WRITE for COMPARISON.md after Phase 5
3. Add MUST WRITE for SUMMARY.md in deep mode
4. Update anti-patterns and red flags

---

## Phase 5: Debugging Skill File Enforcement

**Observable Change:**
Debugging skill writes debug-notes.md at investigation start, patterns.md after learning.

**Why After Phase 4:**
Debugging can feed into planning for substantial fixes. Complete the full chain.

**Demo:**
Run debugging, see debug-notes.md created before diagnosis, patterns.md after fix.

**Stories:**
1. Add MUST WRITE for debug-notes.md at Phase 1
2. Change Save to MUST WRITE for patterns.md
3. Add gate before fix phase
4. Update anti-patterns and red flags

---

## Dependencies

```
Phase 1 (brainstorming) ──► Phase 2 (planning) ──► Phase 3 (executing)
         │                          │                       │
         └── DIRECTION.md           └── phase-X-tasks.md    └── subagent + commit
                                           ▲                       ▲
                                           │                       │
Phase 4 (research) ─────────────────────────┘                       │
         │                                                         │
         └── CONTEXT.md, COMPARISON.md                             │
                                                                   │
Phase 5 (debugging) ───────────────────────────────────────────────┘
         │
         └── debug-notes.md → planning (if substantial)
```

---

## Out of Scope

- Automated validation that files were written
- Changes to reviewing skill

---

## Risk Summary

| Phase | Risk Level | Reason |
|-------|------------|--------|
| Phase 1 | LOW | Simple text additions |
| Phase 2 | LOW | Simple text additions |
