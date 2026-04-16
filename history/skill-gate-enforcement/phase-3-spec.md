# Phase 3 Spec: Enforce Subagent Approach in Executing

## What Changes

Executing skill enforces subagent-driven development, prevents direct implementation by master agent, and requires commit after each task completion for easy revert.

## Entry State

- Executing skill exists at `.claude/skills/executing/SKILL.md`
- Skill mentions subagents but doesn't enforce usage
- No commit-per-task requirement

## Exit State

- SKILL.md has hard gate at start enforcing subagent usage
- Each task completion requires commit before next task
- Anti-patterns include "implementing directly"
- Red flags include "no commit between tasks"
- Beads integration requires `br update` + `git commit` together

## Stories

### Story 1: Add subagent enforcement gate

**Why now:** Core enforcement must come first
**Creates:** Hard gate section at process start
**Done when:** Skill explicitly forbids direct implementation

### Story 2: Add commit-per-task requirement

**Why now:** After subagent enforcement, add the commit discipline
**Creates:** Modified Phase 2/3 with commit requirements
**Done when:** Each task completion includes commit step

### Story 3: Update anti-patterns and red flags

**Why now:** Reinforce the new requirements
**Creates:** Updated tables with new violations
**Done when:** Direct implementation and missing commits are flagged

## Out of Scope

- Creating separate prompt templates (reference superpowers)
- Changing beads CLI commands
