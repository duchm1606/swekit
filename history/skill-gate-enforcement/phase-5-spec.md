# Phase 5 Spec: Debugging Skill File Enforcement

## What Changes

Debugging skill writes debug-notes.md at investigation start and patterns.md after learning, with MUST WRITE enforcement.

## Entry State

- Debugging skill exists at `.claude/skills/debugging/SKILL.md`
- Uses `Save to:` language (weak)
- debug-notes.md mentioned in handoff but never written

## Exit State

- debug-notes.md has MUST WRITE at Phase 1 (Triage)
- patterns.md has MUST WRITE at Phase 6 (Learn)
- Gate before Phase 5: "Do not fix without debug-notes.md"
- Anti-patterns include "skip writing notes"
- Red flags include "no debug-notes.md"

## Stories

### Story 1: Add MUST WRITE for debug-notes.md at Phase 1

**Why now:** Notes should start at triage, not after
**Creates:** MUST WRITE block after Phase 1
**Done when:** Triage results written to file

### Story 2: Add gate before fix phase

**Why now:** Must have documented root cause before fixing
**Creates:** Gate checking debug-notes.md exists
**Done when:** Cannot proceed to fix without file

### Story 3: Change Save to MUST WRITE for patterns.md

**Why now:** Learning must be persisted
**Creates:** MUST WRITE block in Phase 6
**Done when:** Patterns always written to file

### Story 4: Update anti-patterns and red flags

**Why now:** Reinforce new requirements
**Creates:** New violations in tables
**Done when:** Missing files are flagged

## Out of Scope

- Changing debugging process flow
- Adding new phases
