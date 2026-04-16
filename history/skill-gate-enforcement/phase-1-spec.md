# Phase 1 Spec: Add Brainstorming Artifacts

## What Changes

Brainstorming skill produces DIRECTION.md file and presents explicit decision gate: proceed to research OR proceed to planning.

## Entry State

- Brainstorming skill exists at `.claude/skills/brainstorming/SKILL.md`
- References directory exists at `.claude/skills/brainstorming/references/`

## Exit State

- DIRECTION.md template exists at `.claude/skills/brainstorming/references/direction-template.md`
- SKILL.md has MUST WRITE section before Phase 4
- SKILL.md has decision fork: research vs planning
- Handoff section updated to reflect both paths

## Stories

### Story 1: Create DIRECTION.md template

**Why now:** Template must exist before SKILL.md can reference it
**Creates:** `/references/direction-template.md`
**Done when:** Template file exists with all required fields

### Story 2: Update SKILL.md with gate enforcement

**Why now:** After template exists, update skill to use it
**Creates:** Modified SKILL.md with hard gate and decision fork
**Done when:** Skill text includes MUST WRITE, decision gate, and updated handoff

## Out of Scope

- Automated file existence checking
- Integration with other skills
