# Phase 4 Spec: Research Skill File Enforcement

## What Changes

Research skill writes all artifacts to files with MUST WRITE enforcement. No proceeding without written files.

## Entry State

- Research skill exists at `.claude/skills/research/SKILL.md`
- Uses `Save:` language (weak)
- COMPARISON.md and SUMMARY.md mentioned but not enforced

## Exit State

- CONTEXT.md has MUST WRITE enforcement after Phase 3
- COMPARISON.md has MUST WRITE enforcement after Phase 5
- SUMMARY.md has MUST WRITE enforcement after Phase 8 (deep mode)
- Anti-patterns include "output to conversation"
- Red flags include "missing artifact files"

## Stories

### Story 1: Add MUST WRITE for CONTEXT.md

**Why now:** CONTEXT.md is first artifact, must be enforced first
**Creates:** MUST WRITE block after Phase 3
**Done when:** Skill enforces file write before Phase 4

### Story 2: Add MUST WRITE for COMPARISON.md

**Why now:** After CONTEXT.md, comparison matrix needs enforcement
**Creates:** MUST WRITE block after Phase 5
**Done when:** Comparison must be written to file

### Story 3: Add MUST WRITE for SUMMARY.md (deep mode)

**Why now:** Deep mode outputs need same enforcement
**Creates:** MUST WRITE block in Phase 8
**Done when:** Deep mode artifacts written to files

### Story 4: Update anti-patterns and red flags

**Why now:** Reinforce new requirements
**Creates:** New violations in tables
**Done when:** Missing files are flagged

## Out of Scope

- Changing research process flow
- Adding new phases
