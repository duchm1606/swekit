<purpose>
Pre-implementation discussion. Turn fuzzy user intent (feature, bug, change) into ONE CONTEXT.md the next phase can consume. Lock decisions, capture canonical refs, classify scope and domain — do NOT write specs, plans, design docs, story files, ADRs, or implementation code.

You are a thinking partner, not an interviewer. The user is the visionary — you are the builder. Your job is to capture decisions that the next phase can act on, not to figure out implementation yourself.

This skill is **standalone**. It depends on no project framework. It discovers prior context from common locations if they exist, otherwise proceeds with what the user provides.
</purpose>

<required_reading>
@discuss/references/scope-and-domain.md
@discuss/references/gray-area-recipes.md
@discuss/references/handoff-contract.md
@discuss/references/review-pattern.md
@discuss/references/research-accelerators.md
</required_reading>

<progressive_disclosure>
**Per-mode bodies and templates are lazy-loaded** to keep this file under the 500-line workflow budget. Read only what the current invocation needs:

| When | Read |
|---|---|
| `--auto` in $ARGUMENTS | `workflows/discuss/modes/auto.md` (skips AskUserQuestion entirely) |
| no mode flags | `workflows/discuss/modes/default.md` |
| in `write_context` step | `workflows/discuss/templates/context.md` |
| in `write_log` step | `workflows/discuss/templates/discussion-log.md` |
| writing checkpoints | `workflows/discuss/templates/checkpoint.json` |
| in `review_context` step | `discuss/references/review-pattern.md` (already in required_reading; re-scan the per-iteration rules) |

Do not Read mode files unless their flag is set. Do not Read templates outside their step.
</progressive_disclosure>

<downstream_awareness>
**CONTEXT.md is the canonical handoff.** What "the next phase" is depends on the project — could be a spec-building skill, a planning command, a design doc author, or direct implementation. This skill is agnostic about what comes next.

**What downstream finds in CONTEXT.md:**
1. Confirmed restatement (boundary statement: what's in / out of scope).
2. Scope classification (Quick / Standard / Deep) and Domain (SEE / CALL / RUN / READ / ORGANIZE).
3. Locked decisions with stable IDs (D-01, D-02…) and one-line rationale.
4. Agent's Discretion — items the user delegated to the agent within a constraint (no IDs; not locks, fences).
5. Canonical refs (full relative paths to every doc that constrains this work).
6. Code-context snapshot from the lightweight scout.
7. Outstanding questions in two buckets — Must Resolve Before Next Phase (Q-NN blockers) and Delegated To Next Phase (Q-NN technical questions).
8. Deferred ideas (out of scope here; captured for later).

**Your job:** capture decisions clearly enough that the next phase can act without re-asking.
**Not your job:** decide *what kind* of artifact comes next. That's the next phase's job.
</downstream_awareness>

<philosophy>
**User = founder/visionary. Claude = builder.**

The user knows: what behavior they want, what trade-offs matter, which docs constrain the work, what's essential vs. nice-to-have.

The user doesn't know (and shouldn't be asked): codebase patterns (you scout them), implementation paths (next phase figures them), success metrics (inferred from acceptance).

Ask about product intent and choices that would change the resulting artifact. Capture decisions; don't invent them.
</philosophy>

<scope_guardrail>
**No scope creep.** The item is bounded by the user's prompt. Discussion clarifies HOW within the bounded item, never WHETHER to add new capabilities.

**Allowed:** "When export fails midway, retry or surface error?" "Required or optional field?" "Plain text or templated HTML?"

**Not allowed:** "Should we also add bulk export?" "What about scheduling?" — those are new items.

**When user suggests scope creep:**
```
"[X] would be a new capability — that's a separate discuss.
Want me to note it as a deferred idea?

For now, let's focus on [current item]."
```

Capture in `<deferred>` of CONTEXT.md.
</scope_guardrail>

<gray_area_identification>
Gray areas are **decisions the user cares about** that could go multiple ways and would change the resulting artifact.

1. Read the restated work item.
2. Generate item-specific gray areas — not generic categories. See `references/gray-area-recipes.md` for recipes by domain (SEE / CALL / RUN / READ / ORGANIZE).

**Don't use generic labels** (UI, UX, Behavior). Generate specific labels with concrete options.

**Claude handles (don't ask):** implementation details, internal architecture, performance micro-optimizations.

**Three non-lock outcomes** (don't force a D-NN lock for any of these):

- **Agent's Discretion** — user said "you decide" with a stated fence. Goes under `<decisions>` → Agent's Discretion subsection. No Q-NN ID, just the constraint.
- **Blocker question** — user could not pick AND can't proceed without an answer (e.g., "depends on what legal says"). Goes under `<open_questions>` → Must Resolve Before Next Phase with a Q-NN.
- **Delegated question** — user said "let the next phase figure it out" OR the question is technical (next phase resolves with deeper investigation). Goes under `<open_questions>` → Delegated To Next Phase with a Q-NN.
</gray_area_identification>

<answer_validation>
After every AskUserQuestion, if response is empty/whitespace-only:

- **"Other" with empty text:** output `"What would you like to add?"`, STOP, wait for user's next message, reflect it back, continue. Do NOT retry the tool.
- **Any other empty response:** retry once; if still empty, present options as a plain-text numbered list. Never proceed with empty input.
</answer_validation>

<process>

<step name="initialize" priority="first">
**Resolve PROJECT_ROOT and DISCUSS_DIR.**

```bash
# PROJECT_ROOT = git toplevel if in a repo, else PWD
PROJECT_ROOT=$(git rev-parse --show-toplevel 2>/dev/null || echo "$PWD")

# DISCUSS_DIR = --out arg, then $DISCUSS_DIR env var, then default
DISCUSS_DIR="${OUT_ARG:-${DISCUSS_DIR:-$PROJECT_ROOT/.discuss}}"

mkdir -p "$DISCUSS_DIR"
```

**Argument parsing.** `$ARGUMENTS` is a **natural-language topic, question, or brief** — not a kebab-slug. Examples this skill is expected to handle:

```
/discuss invite teammates by email
/discuss I want to implement bulk export; required: CSV + JSON; expected: works for 100k rows
/discuss bug — search filter doesn't persist on page reload
/discuss should we use Resend or Postmark? need EU data residency
/discuss invite-teammates-by-email                   # bare token is also fine; treat as brief
```

Flags (anywhere in $ARGUMENTS): `--auto`, `--out=<dir>`, `--no-review`, `--no-research`.

After stripping flags, the remaining text is `<raw_prompt>`. Do NOT slugify it here — slug derivation happens in `restate_item` after parsing the prompt for structured signals.

**If `<raw_prompt>` is empty:** AskUserQuestion (header: "Topic"; question: "What feature, bug, or open question are we discussing?"; options: "Feature", "Bug fix", "Refactor / cleanup", "Open question / decision"). Then ask the user to type the brief at the next prompt and use that as `<raw_prompt>`.

**Mode dispatch:**
- `--auto` → before `discuss_areas`, Read `workflows/discuss/modes/auto.md`.
- No flags → before `discuss_areas`, Read `workflows/discuss/modes/default.md`.

**Mode dispatch:**
- `--auto` → before `discuss_areas`, Read `workflows/discuss/modes/auto.md`.
- No flags → before `discuss_areas`, Read `workflows/discuss/modes/default.md`.
</step>

<step name="restate_item">
Mine `<raw_prompt>` for structured signals, then restate as a short work item and lock the slug.

**1. Parse `<raw_prompt>` for signals.** Look for and extract:

| Signal | Cue phrases | Goes into |
|---|---|---|
| **Stated criteria** | "required:", "must support", "should handle", "needs to" | seeds `<specifics>` accumulator |
| **Stated expectations** | "expected:", "we want", "the goal is", "users should be able to" | informs `<work_item>` in-scope bullets |
| **Stated constraints** | "must not", "should not break", "constraint:", "without", "while keeping" | seeds `<prior_decisions>` accumulator with `source: user prompt` |
| **Stated examples / external refs** | "like X", "similar to Y", "how Z handles it" | seeds deepwiki trigger candidates for `analyze_item` |
| **Stated canonical docs** | "per <file>", "see <path>", "follow <spec>" | seeds `<canonical_refs>` accumulator (verify file exists; if not, ask user) |
| **Stated non-goals** | "don't worry about", "out of scope", "not touching" | seeds `<work_item>` out-of-scope bullets and `<deferred>` |
| **Stated questions** | "?" at end, "should we", "which is better" | open-question candidate; the prompt itself IS a question to discuss |

Record what was extracted. These pre-answer matching gray areas in `analyze_item` later (mark them `pre-answered: from user prompt`).

**2. Derive a candidate slug.**

- Distill `<raw_prompt>` to its 3–5 most-load-bearing words (the topic, not the criteria).
- Convert to lowercase kebab-case, ≤40 chars, no stop words ("the", "a", "implement", "feature", "want", "should").
- Examples:
  - `"invite teammates by email"` → `invite-teammates-by-email`
  - `"I want to implement bulk export; required: CSV+JSON; expected: works for 100k rows"` → `bulk-export-csv-json`
  - `"bug — search filter doesn't persist on page reload"` → `search-filter-persistence`
  - `"should we use Resend or Postmark? need EU data residency"` → `email-provider-eu-residency`

**3. Build the restatement (≤3 sentences).** Surface what the user already stated so they can see it was captured. Example shape:

```
Restating: <one-sentence headline of what we're discussing>.
You stated:
  - <criterion or expectation 1>
  - <criterion or expectation 2>
  - <constraint, if any>
Proposed slug: <derived-slug>
```

**4. Confirm.** AskUserQuestion:
- header: "Restate"
- question: "Have I captured your intent? Proposed slug: `<derived-slug>`."
- options: "Yes — proceed" / "Refine wording" / "Change slug" / "Wrong intent — re-ask"

Loop until "Yes — proceed":
- **Refine wording** → ask plain-text for the correction, re-render.
- **Change slug** → ask plain-text for the slug; validate kebab-case, ≤40 chars.
- **Wrong intent — re-ask** → discard current parse; AskUserQuestion (type: Feature / Bug / Refactor / Open question); then re-parse.

Save the confirmed restatement as `<work_item>`. Save the confirmed slug as `<slug>`. Set `STORY_DIR="$DISCUSS_DIR/$slug"`.

**5. If the prompt was itself an open question** (e.g., "should we use Resend or Postmark?"):
- The restatement should reflect that this is a decision-comparison, not a build-feature item.
- The candidate slug names the decision being made (`email-provider-eu-residency`).
- Note in DISCUSSION-LOG that the entry point was an open question — this often means scope is Quick or Standard and the output CONTEXT.md may be heavier on Agent's Discretion and Delegated questions than on D-NN locks.
</step>

<step name="classify_scope_and_domain">
Apply the rubric in `references/scope-and-domain.md`.

1. **Scope** — Quick / Standard / Deep, based on blast radius, code surface, and whether sensitive areas are touched (auth, data, external integrations, public contracts).
2. **Domain** — SEE (user-visible surface), CALL (API/CLI/RPC), RUN (job/service/cron), READ (docs/reports), ORGANIZE (data model / schema / contracts).

An item may have one primary domain and one secondary. Note both.

Present the recommendation with one-line reasoning. AskUserQuestion to confirm or override. On `--auto`, accept the recommendation.

Both **scope** and **domain** are advisory for the next phase — they hint at depth and shape, not artifact identity.
</step>

<step name="check_existing">
Check whether an discuss already exists:

```bash
CONTEXT_PATH="$STORY_DIR/CONTEXT.md"
CHECKPOINT_PATH="$STORY_DIR/.CONTEXT-CHECKPOINT.json"
[ -f "$CONTEXT_PATH" ] && echo "exists"
[ -f "$CHECKPOINT_PATH" ] && echo "checkpoint"
```

**If CONTEXT.md exists:** AskUserQuestion ("Update existing" / "View it" / "Cancel"). On `--auto`, "Update existing".

**If checkpoint exists (no CONTEXT.md):** offer "Resume" / "Start fresh". On Resume, parse the JSON checkpoint and skip already-completed areas.
</step>

<step name="discover_prior_context">
Auto-discover prior context from common locations. **Every read here is best-effort and optional** — missing files are not errors.

```bash
# Project-level guidance
for f in CLAUDE.md AGENTS.md GEMINI.md README.md README.txt; do
  [ -f "$PROJECT_ROOT/$f" ] && echo "found:$f"
done

# ADR / decision records (common locations)
for d in docs/adr docs/decisions docs/architecture/decisions .adr adr decisions; do
  [ -d "$PROJECT_ROOT/$d" ] && ls "$PROJECT_ROOT/$d"/*.md 2>/dev/null | tail -10
done

# Prior discussions
ls "$DISCUSS_DIR"/*/CONTEXT.md 2>/dev/null | tail -10

# Recent commits for context
git log --oneline -20 2>/dev/null
```

For each discovery, decide whether to read it:
- **Always read** CLAUDE.md or AGENTS.md if either exists (they describe project conventions).
- **Read 3 most recent** ADRs if any directory was found.
- **Read up to 2 prior discussions** whose slug overlaps with the current one (token overlap).
- **Skim git log** for commits that mention the work item's keywords.

Build internal `<prior_decisions>` with:
- **From project guidance** (CLAUDE.md / AGENTS.md / README).
- **From accepted ADRs** (decision IDs, what they lock).
- **From recent discussions** (D-IDs already locked, deferred ideas previously raised).
- **From git history** (recent related commits).

If nothing was discovered, record `prior_context: empty` and move on. The skill works fine in a brand-new repo.
</step>

<step name="scout_codebase">
Lightweight scan (~10% context) for assets that affect discuss decisions.

```bash
# Map work-item keywords → candidate files
rg -l --max-count 1 "<keyword>" 2>/dev/null | head -10
```

Read up to 3 files referenced by the work item. Capture into `<code_context>`:
- **Reusable assets** (existing components, utils, schemas).
- **Established patterns** (naming, error handling, test layout).
- **Integration points** (where this item would attach).

If no implementation exists yet, record `no_implementation_yet: true` and move on.
</step>

<step name="analyze_item">
With `<work_item>`, `<scope>`, `<domain>`, `<prior_decisions>`, `<code_context>` in hand, generate gray areas.

1. **Boundary statement** — one sentence: what this item delivers, what it does NOT touch.
2. **Initialize `<canonical_refs>` accumulator.** Sources now: any doc the user mentioned, any ADR/file from `<prior_decisions>` that constrains this work. Sources later: any doc the user references during `discuss_areas`. Every entry needs a full path relative to `PROJECT_ROOT`. MANDATORY in CONTEXT.md.
3. **Pre-answer pass** — for each candidate gray area, check both `<prior_decisions>` AND the signals mined from `<raw_prompt>` in `restate_item`. If the user already stated a criterion / expectation / constraint that answers this gray area, mark `pre-answered: from user prompt` (or `pre-answered: <ADR/file>` for discovered prior decisions). Skip pre-answered gray areas during discussion — they get folded directly into `<decisions>` with a `D-NN` and a rationale citing the source.
4. **Generate** 3–4 specific gray areas per `references/gray-area-recipes.md` (use the recipe for the chosen Domain). Each gray area: concrete label, 1–2 candidate options, code/prior-decision annotation.
5. **Research-accelerator pass (skip if `--no-research`)** — for each gray area whose answer hinges on a specific library / framework / external project, optionally enrich the options via MCP per `references/research-accelerators.md`:
   - **context7** when a library/framework is in play (e.g., auth library, ORM, vector store). Resolve library ID, query current docs, summarize in 1–3 bullets, cite source.
   - **deepwiki** when the user has named an external project (e.g., "like Linear", "how GitHub does it"). Ask a scoped question, summarize, cite.
   - Each MCP source consumed adds an entry to `<canonical_refs>` (`context7:<lib>` or `deepwiki:<org>/<repo>`).
   - Failure path: if an MCP is unavailable, log a one-liner and continue. Never block on it.
6. **Skip assessment** — if no meaningful gray areas remain, present this and offer to skip to `write_context` with auto-locked decisions.
</step>

<step name="present_gray_areas">
Present boundary + carried-forward decisions + gray areas.

```
Item: [restated]
Scope: [Quick|Standard|Deep] · Domain: [primary] (+ [secondary if any])
Boundary: [what this delivers / does not touch]

Carrying forward:
- [Prior decision N applies here] (from <where>)

Gray areas to discuss:
```

On `--auto`: select ALL gray areas; log selections; continue.

Otherwise AskUserQuestion (multiSelect: true; header: "Discuss"; question: "Which areas do you want to lock?"; options: 3–4 specific gray areas, each with concrete label and 1–2-line description, recommended option highlighted).

Do NOT include a "skip" option.
</step>

<step name="discuss_areas">
Discussion behavior comes from the active mode file:

- `--auto` → `workflows/discuss/modes/auto.md` (Claude picks recommended for every gray area; no AskUserQuestion).
- Default → `workflows/discuss/modes/default.md` (4 single-question turns per area, then check whether to continue).

**Universal rules (all modes):**

- **Decision IDs** — every locked decision gets a stable ID `D-01`, `D-02`, … in CONTEXT.md.
- **Canonical refs accumulation** — when the user references a doc, immediately Read it (or confirm it exists) and add to canonical_refs with full relative path. If the user names an external project mid-discussion (e.g., "like Linear handles X"), this is a deepwiki trigger per `references/research-accelerators.md` — query, summarize, cite as `deepwiki:<org>/<repo>` in canonical_refs.
- **Live library lookup** — if the user picks a specific library or framework mid-discussion that wasn't in the candidate options, this is a context7 trigger. Resolve library ID, query relevant docs, confirm the choice is supported in current versions, cite as `context7:<lib>` in canonical_refs. Skip if `--no-research`.
- **Scope creep** — capture as deferred idea and redirect per `<scope_guardrail>`.
- **Three non-lock paths** —
  - "You decide; must X" → `<decisions>` → Agent's Discretion (no Q-NN, just constraint).
  - "I don't know yet; depends on Y" → `<open_questions>` → Must Resolve (Q-NN, with owner).
  - "Let the next phase figure it out" or auto-mode could not pick confidently → `<open_questions>` → Delegated (Q-NN, with constraint if any).
- **Checkpoint** — after each area completes, write `$STORY_DIR/.CONTEXT-CHECKPOINT.json` per `workflows/discuss/templates/checkpoint.json`.
- **Log accumulation** — for each question, record area / options / selection / notes for `write_log`.
</step>

<step name="write_context">
Create the CONTEXT.md. This is the **only canonical handoff**.

**Path:**
```bash
mkdir -p "$STORY_DIR"
CONTEXT_PATH="$STORY_DIR/CONTEXT.md"
```

**Read the template now:**
```
Read(discuss/workflows/discuss/templates/context.md)
```

Substitute live values. MANDATORY sections:
- `<classification>` (scope + domain — advisory)
- `<work_item>` boundary
- `<decisions>` with D-IDs (and Agent's Discretion subsection if any)
- `<canonical_refs>` — full relative paths
- `<code_context>`
- `<open_questions>` — both buckets (Must Resolve + Delegated). Each may be "None".
- `<deferred>`

Write the file.

**Hard rule — do NOT create:** specs, plans, design docs, story files, ADRs, code, scaffolds. If the user pushes during discussion: redirect with "That belongs to the next phase. I'll note it in CONTEXT.md."
</step>

<step name="review_context">
**Skip if `--no-review`.**

Spawn a **fresh subagent** (no conversation history) to validate CONTEXT.md against the checklist in `references/review-pattern.md`. Use the `Explore` agent type for read-only validation.

```
Agent(
  subagent_type: "Explore",
  description: "Fresh review of discuss CONTEXT.md",
  prompt: <prompt from references/review-pattern.md, parameterized with $CONTEXT_PATH>
)
```

The reviewer reports findings categorized as **P1 (blocking)** / **P2 (should fix)** / **P3 (nit)**. See `review-pattern.md` for the exact checklist.

**Loop control:**
- P1 findings → fix CONTEXT.md (or surface to user if a fix needs a real decision) → re-review.
- P2 findings → fix without re-review.
- P3 findings → record in DISCUSSION-LOG.md, don't fix.
- Max **2 review loops**. If P1 findings persist after 2 loops, surface them in `confirm_creation` and let the user decide.
</step>

<step name="write_log">
Write DISCUSSION-LOG.md for audit. Not consumed by downstream agents.

**Path:** `$STORY_DIR/DISCUSSION-LOG.md`

**Read template:**
```
Read(discuss/workflows/discuss/templates/discussion-log.md)
```

Substitute area names, options, user selections, notes, deferred ideas, reviewer findings. Write the file.

**Clean up checkpoint:**
```bash
rm -f "$STORY_DIR/.CONTEXT-CHECKPOINT.json"
```
</step>

<step name="confirm_creation">
Present summary and next steps. Keep it short.

```
Created:
  - <CONTEXT_PATH>
  - <DISCUSSION_LOG_PATH>

Classification: <scope> · <domain>
Decisions locked: <count> · Open for next phase: <count> · Deferred: <count>
Review: <passed | N P1 findings outstanding>

▶ Next phase reads CONTEXT.md to produce whatever artifact your project uses
  (spec, plan, design doc, story, direct patch). This skill is agnostic.
```

Do not invent a `/spec` or `/plan` command — the user knows what their project uses. Just point at the file.

If P1 review findings remained, list them explicitly so the user can address before the next phase reads CONTEXT.md.
</step>

</process>

<success_criteria>
- `PROJECT_ROOT` and `DISCUSS_DIR` resolved.
- Work item restated and confirmed before any gray-area generation.
- Scope (Quick/Standard/Deep) and Domain (SEE/CALL/RUN/READ/ORGANIZE) classified.
- Prior context auto-discovered and applied (no re-asking settled questions).
- Gray areas item-specific.
- Decisions assigned stable IDs D-01, D-02, …
- Non-lock items routed correctly: Agent's Discretion (constraint only), Must-Resolve blockers (Q-NN + owner), Delegated questions (Q-NN + optional constraint).
- Canonical refs mandatory and complete (full relative paths).
- Exactly ONE primary artifact: `<STORY_DIR>/CONTEXT.md`. Plus DISCUSSION-LOG.md.
- Fresh-reviewer pass run (unless `--no-review`); P1 findings resolved or surfaced.
- NO specs, plans, design docs, story files, ADRs, or code produced by this skill.
- Deferred ideas preserved; scope creep redirected.
- Research accelerators used opportunistically (unless `--no-research`): context7 for library/framework gray areas, deepwiki when user names an external project. MCP failures logged but never block.
- Each MCP source consumed is cited in `<canonical_refs>` as `context7:<lib>` or `deepwiki:<org>/<repo>`.
- User shown CONTEXT.md path and generic next-step pointer.
- Workflow file stays under the 500-line budget; modes/templates lazy-loaded.
</success_criteria>
