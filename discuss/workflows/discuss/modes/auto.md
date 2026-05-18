# Auto mode — Claude picks recommended for every gray area

> **Lazy-loaded.** Read from `workflows/discuss.md` when `--auto` is present.
> Overrides `discuss_areas`. The parent's gray-area generation, classification,
> write, review, and log steps still run.

## When to use

- Routine discuss where the user trusts your recommendations and wants speed.
- Re-runs after a checkpoint where most decisions are obvious.
- CI / batch dispatch.

## Behavior

1. **No AskUserQuestion calls during `discuss_areas`.** All other steps that need user confirmation (restatement, scope/domain override, scope-creep redirects) still ask.

2. **For each gray area:**
   - Generate the same 2–3 candidate options.
   - Identify the recommended option using the tie-breaker order in `references/gray-area-recipes.md`:
     1. Prior decisions (preferred match wins).
     2. Existing code patterns from `<code_context>`.
     3. Smaller blast radius (less reversible damage if wrong).
     4. YAGNI / KISS (simpler wins on remaining ties).
     5. Reversible later if wrong.
   - Lock the recommended option as the decision.
   - Assign a D-ID.
   - Print:
     ```
     [auto] D-01 [Area] → Chose: <option>
       Reason: <one-line>
       Alternatives: <other options, in order of strength>
     ```

3. **Canonical refs** — still accumulate any docs you read during discover / scout / analyze. Auto mode does not skip canonical_refs.

4. **Scope creep** — even in auto mode, do NOT silently absorb scope creep. If the user's prompt suggests work outside the bounded item, write it to `<deferred>` and announce:
   ```
   [auto] Deferred scope: <idea> — not implementing, captured in CONTEXT.md.
   ```

5. **Deep-scope guardrail** — if the classification step recommended **Deep** scope (auth / data / external system / public contract / etc.), and a gray area in that domain has no clear prior decision and no clear safer option, do NOT auto-pick. Instead, halt auto for that area and AskUserQuestion. Log:
   ```
   [auto-halt] Deep-scope decision required — switching to interactive for this area.
   ```

## Single-pass cap

Auto mode runs **one pass** over all gray areas. It does not loop ("Explore more"). If an area cannot be resolved confidently (no clear prior decision, no clear code pattern, no clear safer option), auto mode does NOT force a fake lock — instead it records the area into `<open_questions>` → **Delegated To Next Phase** (NOT Must Resolve — auto-mode never creates user-owned blockers; it always routes uncertainty to the next phase). Note `[auto-delegated]` on the Q-NN. Then proceeds to `write_context`. The user is told in `confirm_creation`:

```
[auto] N gray areas could not be auto-resolved — captured in <open_questions> → Delegated:
  - Q-NN <area> — reason
Re-run without --auto to lock them now, or let the next phase resolve.
```

## Interaction with the fresh-reviewer step

Auto mode does NOT skip `review_context`. The fresh reviewer still runs (unless the user passed `--no-review`) and may flag P1 findings on auto-locked decisions — for example, if auto-locking introduced contradictions or vague rationale.

If P1 findings come from auto-locked decisions:
- First fix attempt: try to refine the rationale or pick a different option from the candidates.
- If still failing after the second loop: surface to user in `confirm_creation` for manual resolution.
