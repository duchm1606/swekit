---
name: discuss
description: Always the first step in the flow. Brainstorm a feature, bug, or change with the user and produce ONE CONTEXT.md the next phase (spec / plan / design / implementation — whichever the project uses) can consume. Standalone — no required project framework, no required files. Optionally uses deepwiki MCP for external prior-art research and context7 MCP for current library/framework documentation when available.
argument-hint: "<topic | question | brief> [--auto] [--out=<dir>] [--no-review] [--no-research]"
allowed-tools:
  - Read
  - Write
  - Bash
  - Glob
  - Grep
  - AskUserQuestion
  - Agent
  - mcp__deepwiki__ask_question
  - mcp__deepwiki__read_wiki_structure
  - mcp__deepwiki__read_wiki_contents
  - mcp__plugin_context7_context7__resolve-library-id
  - mcp__plugin_context7_context7__query-docs
---

<objective>
Turn fuzzy user intent (feature idea, bug, change request) into ONE artifact the next phase can consume:

`<DISCUSS_DIR>/<slug>/CONTEXT.md` — restated work item, scope/domain classification, locked decisions (D-01, D-02…), Agent's Discretion items (constraints), canonical references, code-context snapshot, outstanding questions split into Must-Resolve-Before-Next-Phase (Q-NN blockers) and Delegated-To-Next-Phase (Q-NN), deferred ideas.

`<DISCUSS_DIR>` defaults to `<PROJECT_ROOT>/.discuss/`. Override with `--out=<dir>` or env var `DISCUSS_DIR`. `PROJECT_ROOT` is the nearest enclosing git repo, otherwise the current working directory.

This skill **does not** produce specs, plans, design docs, story files, ADRs, or implementation code. It produces context the next phase reads.
</objective>

<runtime_note>
**No project framework required.** This skill works in any repo. It will auto-discover common artifacts if they exist (`CLAUDE.md`, `AGENTS.md`, `README*`, `docs/`, `docs/adr/`, `docs/decisions/`, `.discuss/`) but never depends on them.

**Rich prompts are first-class.** Invoke with a topic, a question, or a brief that already contains criteria / expectations / constraints / external references. The workflow mines those signals in `restate_item`, pre-answers matching gray areas during `analyze_item`, and seeds canonical refs and deferred ideas — so the user doesn't have to repeat themselves. The skill always asks the user to confirm the restated work item and the derived slug before discussion begins.
</runtime_note>

<execution_context>
Workflow files are loaded on-demand. Do not pre-load any workflow or mode files before reading the dispatch instructions in the `<process>` section below.
</execution_context>

<context>
Topic / question / brief: $ARGUMENTS

This is a natural-language prompt, not a kebab-slug. The skill derives a slug
internally from the gist and asks the user to confirm. Examples:

  /discuss invite teammates by email
  /discuss I want to implement bulk export; required: CSV+JSON; expected: works for 100k rows
  /discuss bug — search filter doesn't persist on page reload
  /discuss should we use Resend or Postmark for transactional email? need EU data residency

If $ARGUMENTS is empty, the workflow asks the user to describe the item before
continuing. Even a single kebab token is just a brief prompt — the skill still
runs restatement + slug confirmation.
</context>

<process>
**Mandatory:** Read `discuss/workflows/discuss.md` end-to-end before taking any action. The objective and success_criteria sections here are summaries — the workflow file contains the complete step-by-step process, scope/domain rubric, gray-area generation rules, fresh-reviewer step, and handoff contract.

**Lazy loading:** mode files (`workflows/discuss/modes/*.md`), templates (`workflows/discuss/templates/*`), and references (`references/*.md`) are loaded only at the steps that need them. Do not load them from this file.
</process>

<success_criteria>
- `PROJECT_ROOT` and `DISCUSS_DIR` resolved.
- Feature/bug restated clearly; user confirmed restatement.
- Scope classified (Quick / Standard / Deep) and Domain identified (SEE / CALL / RUN / READ / ORGANIZE).
- Prior context auto-discovered from common locations (ADRs, prior discussions, CLAUDE.md / AGENTS.md if present) and applied — no re-asking settled questions.
- Gray areas item-specific (no generic category labels).
- Each selected gray area locked under the active mode's rules; decisions assigned stable IDs (D-01, D-02…).
- Non-lock items routed correctly: Agent's Discretion (constraint only), Must-Resolve blockers (Q-NN + owner), Delegated questions (Q-NN + optional constraint).
- Research accelerators used opportunistically (unless `--no-research`): context7 for library/framework gray areas, deepwiki for external prior-art questions. Skill degrades gracefully when MCPs are unavailable.
- Scope creep redirected to deferred ideas; not lost, not acted on.
- ONE primary artifact written: `<DISCUSS_DIR>/<slug>/CONTEXT.md` with canonical_refs MANDATORY.
- Fresh-reviewer pass run (unless `--no-review`); findings fixed before declaring done; max 2 review loops.
- DISCUSSION-LOG.md written alongside CONTEXT.md (audit, not for agent consumption).
- Checkpoint file written after each gray area completes; cleaned up after successful write.
- User shown the CONTEXT.md path and a generic next-step pointer.
- No specs, plans, design docs, story files, ADRs, or code produced by this skill.
</success_criteria>
