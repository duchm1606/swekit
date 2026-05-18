# Handoff Contract

> What this skill guarantees to whatever comes next. What it explicitly does NOT do. How to phrase the hand-off in `confirm_creation`.

## Position in any flow

```
user intent (feature / bug / change)
        │
        ▼
    discuss            ◄── this skill (pre-implementation discussion)
        │
        ▼
    <DISCUSS_DIR>/<slug>/CONTEXT.md   ◄── single canonical handoff
        │
        ▼
  next phase          ◄── whatever the project uses:
                          spec-building skill, planning command,
                          design doc author, or direct implementation.
                          This skill is agnostic about what's next.
```

## What the next phase will find

When `discuss` completes, the next agent/user sees:

1. **`<STORY_DIR>/CONTEXT.md`** containing:
   - Confirmed boundary statement (what's in / out of scope).
   - Classification: Scope (Quick / Standard / Deep) + Domain (SEE / CALL / RUN / READ / ORGANIZE) — both advisory.
   - Locked decisions with stable IDs (D-01, D-02…) and one-line rationale each.
   - Agent's Discretion subsection of `<decisions>` — areas the user delegated to the agent within a stated constraint (no IDs).
   - Mandatory `<canonical_refs>` section listing every doc that constrains this work, with full paths.
   - Code-context snapshot (reusable assets, established patterns, integration points).
   - `<specifics>` — verbatim product texture from the user.
   - `<open_questions>` split into two buckets:
     - **Must Resolve Before Next Phase** — Q-NN blockers. The next phase MUST NOT start until these are answered. Each has an owner (user / team / external).
     - **Delegated To Next Phase** — Q-NN technical questions the next phase resolves itself with deeper investigation.
   - `<deferred>` ideas (out of scope here; captured for later).

2. **`<STORY_DIR>/DISCUSSION-LOG.md`** — human-readable audit + reviewer findings (NOT for agent consumption).

That's it. No other files.

## What the next phase will NOT find from this skill

- ❌ Specs of any kind (story files, requirements docs, behavior specs).
- ❌ Plans, task breakdowns, or implementation sequencing.
- ❌ Design docs, architecture diagrams, or component drafts.
- ❌ ADRs / decision records (the decisions are in CONTEXT.md `<decisions>`, but a formal ADR is the next phase's call).
- ❌ Code, scaffolds, file edits, or any implementation.
- ❌ Test matrices or QA plans.

Those are the **next phase's outputs**, derived from the CONTEXT.md this skill produces.

## What the next phase should do with CONTEXT.md

In order:

1. **Read CONTEXT.md end-to-end.** Treat the boundary statement as the scope anchor.
2. **Read every entry in `<canonical_refs>`.** These are why the locked decisions hold.
3. **Read project guidance** (CLAUDE.md / AGENTS.md / equivalent) if your project has them.
4. **Use the Scope/Domain classification** to choose depth and shape of the output. Revise only with explicit reasoning; record any revision in the next phase's own log.
5. **Handle `<open_questions>` per bucket:**
   - **Must Resolve Before Next Phase** — STOP. Do not start producing artifacts until each blocker is answered by its named owner. If you proceed anyway, note in your own log that you overrode a Must-Resolve.
   - **Delegated To Next Phase** — these are yours to resolve. Investigate, decide, and record the resolution in whatever artifact you produce.
6. **Honor every locked decision (D-01, D-02…)** — the next phase MUST NOT re-ask the user any question that has a D-ID. If a decision turns out to be wrong, surface the conflict and stop, don't silently override.
7. **Produce whatever artifact your project uses** — story, spec, design doc, direct patch.
8. **Archive or link CONTEXT.md** from the next-phase artifact for traceability.

## Hand-off phrasing for `confirm_creation`

Use this pattern. Keep it short. Don't invent project-specific commands.

```text
Created:
  - <STORY_DIR>/CONTEXT.md
  - <STORY_DIR>/DISCUSSION-LOG.md

Classification: <scope> · <domain>
Decisions locked: <count> · Open for next phase: <count> · Deferred: <count>
Review: <passed | N P1 findings outstanding>

▶ Next phase reads CONTEXT.md to produce whatever your project uses
  (spec, plan, design doc, story, direct patch).
```

Do not propose `/plan`, `/spec`, implementation steps, or "I'll start coding." Those are not the next step from this skill, and they may not even be commands the user's project has.

## When NOT to hand off

The workflow stops without writing CONTEXT.md when:

- The user refuses every restatement option (no agreed work item).
- A `<canonical_refs>` entry the user named cannot be located on disk. Confirm before writing.
- The user attempts to make the skill produce specs, plans, or code — redirect once; if pressed again, stop and clarify scope.

In each case, write a checkpoint so a re-run can resume.

## What the user is allowed to override

- Scope classification (Quick / Standard / Deep). Recorded as advisory anyway.
- Domain classification (primary and secondary).
- Specific decision options ("I don't like any of these, here's option D" → locks as D-NN with the user's wording).
- Whether a candidate gray area should be discussed at all (mark `pre-answered: user-declared-irrelevant`).
- Whether to run the fresh-reviewer step (`--no-review`).

What the user **cannot** override silently:

- The mandatory presence of `<canonical_refs>` (it can read "No external specs — requirements fully captured in decisions above" but the section must exist).
- Skipping CONTEXT.md altogether — the skill always writes it before exiting.
- Asking the skill to produce specs / plans / design / code. The skill redirects: "That's the next phase's job — I'll capture it as guidance."

## P1 review findings that survive 2 loops

If the fresh-reviewer step keeps flagging the same P1 finding after 2 fix attempts, do NOT block forever. Surface the findings explicitly in `confirm_creation`:

```text
⚠ 2 P1 review findings remain:
  - <finding 1>
  - <finding 2>

The CONTEXT.md was written but may not be safe for the next phase to read
without addressing these.
```

The user can then edit CONTEXT.md directly or re-run the skill to fix.
