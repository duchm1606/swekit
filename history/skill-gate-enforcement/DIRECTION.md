# Research Direction: Skill Gate Enforcement

**From Brainstorming:** 2026-04-16
**Selected Option:** Combined - Gate + Template + Decision Fork

## Problem Statement

The duck skills (brainstorming, planning) have workflow enforcement issues. Brainstorming doesn't produce DIRECTION.md and doesn't stop at the gate. Planning doesn't write artifacts to files, causing context rot in future conversations.

## Research Questions

1. N/A - No external research needed. This is a skill text fix.

## Known Constraints

- Must maintain backward compatibility with existing skill structure
- Should use existing `/references/` pattern for templates
- Must be clear enough that Claude follows it without rationalization

## Success Criteria

- Brainstorming produces DIRECTION.md file before proceeding
- Brainstorming offers explicit fork: research OR planning
- Planning writes phase-plan.md, phase-X-spec.md, phase-X-tasks.md to files
- Context persists across conversations via history/<topic>/ artifacts
