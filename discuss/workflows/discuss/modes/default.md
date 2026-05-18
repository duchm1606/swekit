# Default mode — interactive discuss discussion

> **Lazy-loaded.** Read from `workflows/discuss.md` when no mode flag is set.

This file defines `discuss_areas` for the default flow. Shared steps (initialize, restate_item, classify_scope_and_domain, check_existing, discover_prior_context, scout_codebase, analyze_item, present_gray_areas, write_context, review_context, write_log, confirm_creation) live in the parent and run for every mode.

## discuss_areas (default, interactive)

For each selected gray area, conduct a focused locking loop.

**Philosophy:** stay adaptive. Default flow is **4 single-question turns per area**, then check whether to continue. Each answer should reveal the next question.

**For each area:**

1. **Announce the area:**
   ```
   Let's lock [Area].
   ```

2. **Ask up to 4 questions using AskUserQuestion:**
   - header: `[Area]` (max 12 chars — abbreviate if needed).
   - question: a specific decision for this area.
   - options: 2–3 concrete choices with the recommended choice clearly marked and a one-line reason why.
   - **Annotate options with code context or prior-decision context** when relevant:
     ```
     "How should partial import failures behave?"
     - Abort & roll back (matches existing transactional pattern in /lib/db.ts)
     - Skip & report (would be a new pattern; needs result envelope)
     - Resume from last good row (requires new checkpointing infra)
     ```
   - Include "You decide" as an option when reasonable — captures Claude discretion as a flagged decision.

3. **Assign a stable ID** to the locked answer (`D-01`, `D-02`, …) immediately. Add it to the running decisions list.

4. **After the current batch of questions, check:**
   - header: `[Area]` (max 12 chars).
   - question: "More questions about [area], or move to next? (Remaining: [list other unvisited areas])"
   - options: "More questions" / "Next area"

   - "More questions" → ask another batch of up to 4 single questions, then check again.
   - "Next area" → proceed to next selected area.
   - "Other" → interpret intent. Continuation phrases ("keep going", "yes", "more") → "More questions". Advancement phrases ("done", "next", "skip") → "Next area". If ambiguous, ask plainly.

5. **After all initially-selected areas complete:**
   - Summarize captured decisions (just D-IDs and headlines).
   - AskUserQuestion:
     - header: "Done"
     - question: "Discussed [list]. Any gray areas still unclear?"
     - options: "Explore more" / "Ready to write discuss"
   - "Explore more" → identify 2–3 new gray areas based on what was learned; loop back to `present_gray_areas`.
   - "Ready to write context" → proceed to `write_context`.

   Where the user gives a non-lock answer, route to one of three places — do not force a D-NN lock:

   - **"You decide; constraint is X"** → `<decisions>` → Agent's Discretion. No Q-NN — just the area name and the constraint.
   - **"I don't know yet; depends on Y / need to check with Z"** → `<open_questions>` → Must Resolve Before Next Phase. Assign Q-NN. Record the owner (user / team / external).
   - **"Let the next phase figure it out"** → `<open_questions>` → Delegated To Next Phase. Assign Q-NN. Record any constraint.

   Mark the gray area resolved (open: true) and move on. The skill must NEVER force a D-NN lock on an answer that wasn't actually a lock.

## Canonical ref accumulation during discussion

When the user references a doc/spec/ADR during any answer — e.g., "follow adr-0002", "see permissions.md", "per the export contract" — immediately:

1. Read the referenced doc (or confirm it exists at the path).
2. Add it to `<canonical_refs>` with full path relative to `PROJECT_ROOT`.
3. Use what you learned from the doc to inform subsequent questions in this area.

These user-referenced docs are often MORE important than the ones you started with. Never drop them.

## Question design rules

- Options must be concrete, not abstract ("Abort & roll back", not "Option A").
- Each answer should inform the next question — don't pre-script all four.
- If the user picks "Other" for free-form input, ask the follow-up as plain text. Do NOT call AskUserQuestion again. Wait for the user's next message, reflect it back, then resume normal flow.
- Never bundle questions in default mode. One decision per turn.

## Scope creep handling

If the user mentions something outside the bounded item:
```
"[X] sounds like a separate discuss — I'll note it as a deferred idea.

Back to [current area]: [return to current question]"
```

Append to internal `<deferred>` list. Surface in CONTEXT.md `<deferred>` and the confirmation summary.

## Incremental checkpoint

After each area is resolved (user said "Next area" or "Ready to write context"), immediately write a checkpoint with all decisions captured so far. Prevents data loss on session interruption.

**Path:** `${story_dir}/.CONTEXT-CHECKPOINT.json`
**Schema:** read `workflows/discuss/templates/checkpoint.json` for the canonical shape; substitute live values.

On session resume, the parent's `check_existing` step detects the checkpoint and offers to resume. After `write_context` completes successfully, the parent's `write_log` step deletes the checkpoint.

## Track discussion log data

For each question asked, accumulate:
- Area name
- Options presented (label + one-line reason)
- User's selection (or free-text response)
- Follow-up notes / clarifications
- Assigned D-ID

This is what `write_log` writes into DISCUSSION-LOG.md.
