# Phase 2 Spec: Fix Planning File Outputs

## What Changes

Planning skill writes all artifacts to files instead of conversation. Context persists across sessions.

## Entry State

- Planning skill exists at `.claude/skills/planning/SKILL.md`
- Templates exist in `/references/`
- Skill describes file outputs but doesn't enforce writing them

## Exit State

- SKILL.md has MUST WRITE sections after each artifact phase
- Each phase explicitly requires file confirmation before proceeding
- Handoff section lists all files that should exist

## Stories

### Story 1: Add MUST WRITE enforcement to planning phases

**Why now:** Single change addresses the core problem
**Creates:** Modified SKILL.md with hard gates on file writes
**Done when:** Each output phase has explicit "MUST WRITE" instruction

## Out of Scope

- Adding new templates (they already exist)
- Changing phase structure
