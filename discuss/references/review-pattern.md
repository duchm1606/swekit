# Review Pattern — Fresh-Reviewer Step

> After `write_context` produces CONTEXT.md, spawn a **fresh subagent with
> no conversation history** to independently validate the file. The reviewer
> cannot see what the user said or what tradeoffs you weighed — only what
> the document claims.
>
> This catches misunderstandings invisible to the primary agent and produces
> structurally stronger CONTEXT.md files.

## When to skip

- User passed `--no-review`.
- (No other auto-skip cases. Even tiny / quick discussions get a review pass.)

## Subagent invocation

Use the `Explore` agent type (read-only, fast, no edit risk).

```
Agent(
  subagent_type: "Explore",
  description: "Fresh review of discuss CONTEXT.md",
  prompt: <reviewer prompt below, with $CONTEXT_PATH and $WORK_ITEM substituted>
)
```

## Reviewer prompt template

```
You are an independent reviewer. You have not seen the discussion that
produced this CONTEXT.md. Read it cold and audit it against the checklist
below. Report findings only — do not edit the file.

File to review: $CONTEXT_PATH
Stated work item: $WORK_ITEM

## Checklist

For each item, mark Pass / Fail / N/A and a one-line reason.

### P1 — blocking (must be fixed before next phase reads this)

1. **Boundary statement is concrete.** A reader who has never met this work
   item should be able to tell what's in and what's out of scope after
   reading only `<work_item>`.
2. **Every locked decision has a stable D-ID** (D-01, D-02, …) and a one-line
   rationale. No decision sentence ends with a question mark.
3. **No placeholders.** Search for `TBD`, `TODO`, `<...>`, `???`, `[fill in]`,
   `xxx`. None should appear inside `<decisions>` or `<canonical_refs>`.
4. **Every entry in `<canonical_refs>` is a full path** (not a bare filename)
   and includes a one-line note explaining its relevance.
5. **No contradictions between decisions.** Two D-IDs do not mandate
   incompatible behavior.
6. **Open questions correctly routed.** Both subsections of `<open_questions>`
   are valid:
   - **Must Resolve Before Next Phase** — each Q-NN names a real blocker, why
     it blocks the next phase, and an owner (user / team / external). "Q-01:
     figure it out" or missing owner is not acceptable.
   - **Delegated To Next Phase** — each Q-NN names what's being delegated and
     any constraint (or "none"). Items here should NOT be user-owned blockers
     in disguise; if the user must answer first, it belongs in Must Resolve.
   Agent's Discretion entries (in `<decisions>`) are NOT in this section —
   verify they're under `<decisions>` with a stated constraint each.
7. **Scope and domain are stated.** Not just default values.

### P2 — should fix (fix in same pass; don't re-review for these)

1. **Decision rationale is non-circular.** "We chose X because it's the
   right choice" fails this. The rationale should reference a code pattern,
   a prior decision, a constraint, or a tradeoff.
2. **Canonical refs are grouped by topic** rather than listed flat.
3. **Code context is concrete.** "Existing pattern" with no file/function
   name is too vague.
4. **Deferred ideas have a next-step suggestion** (e.g., "open separate
   discuss" / "discuss when X lands").

### P3 — nit (record in DISCUSSION-LOG, don't fix)

1. Grammar / typo.
2. Heading capitalization inconsistency.
3. Markdown formatting drift (lists, table alignment).

## Output format

Return exactly this shape (no preamble):

```
P1 findings: <count>
  - <one-line description with section reference>
  - ...

P2 findings: <count>
  - <one-line description with section reference>
  - ...

P3 findings: <count>
  - <one-line description with section reference>
  - ...

Overall: <pass | fix-required>
```

If P1 count > 0, Overall is fix-required.
If P1 count == 0, Overall is pass (P2/P3 may still be present).
```

## Loop control (parent skill)

After the reviewer reports:

- **P1 > 0** → fix each P1 in CONTEXT.md.
  - If a fix needs new information (e.g., the boundary statement really is
    too vague because the user didn't say enough), surface it to the user
    via AskUserQuestion. Do not invent.
  - After fixing, run review again. Max **2 review loops** total.
  - If P1 persists after 2 loops, stop looping and surface remaining P1
    findings in `confirm_creation` so the user can address before the next
    phase reads the file.
- **P2 > 0** → fix without re-review.
- **P3 > 0** → record in DISCUSSION-LOG.md `## Reviewer P3 nits`.

## Capturing review findings in DISCUSSION-LOG.md

Append a section per loop:

```markdown
## Review loop N

**P1 findings:** <count> — <list>
**P2 findings:** <count> — <list>
**P3 findings:** <count> — <list>
**Fixes applied:** <list of D-IDs / sections touched>
**Outstanding after loop:** <list or "none">
```

## Why a fresh subagent rather than self-review

A fresh subagent has no memory of the discussion. It sees only what the
document claims. This is exactly the position the next phase will be in.
A reviewer with conversation history mentally fills in the gaps the
document leaves, and misses them.

GSD does self-review inside the primary agent for cost reasons. Khuym
spawns fresh reviewers for catch rate. This skill follows Khuym for the
final pass because discuss CONTEXT.md is short and the cost is small
relative to the quality gain.
