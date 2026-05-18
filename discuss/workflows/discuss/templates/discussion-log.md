# DISCUSSION-LOG template — for discuss write_log step

> **Lazy-loaded.** Read only inside the `write_log` step of
> `workflows/discuss.md`, immediately before writing
> `${story_dir}/DISCUSSION-LOG.md`. This file is for human audit only —
> downstream agents do NOT read it.

## Variable substitutions

- `[Slug]`, `[Title]`, `[date]`, `[Scope]`, `[DomainPrimary]`, `[DomainSecondary]` — as in context.md.
- `[Mode]` → `default` | `auto` (combine future overlays with `+`).

## Template body

```markdown
# DISCUSSION LOG: [Title]

**Slug:** [Slug]
**Date:** [date]
**Scope (advisory):** [Scope] · **Domain:** [DomainPrimary] (+ [DomainSecondary]) · **Mode:** [Mode]

> Human audit trail only. The canonical record for the next phase is
> `CONTEXT.md` in this folder.

## Restatement

[The confirmed restatement of the user's intent, exactly as accepted.]

## Classification

- **Scope recommendation:** [Scope] — [reason]
- **Domain recommendation:** [DomainPrimary] / [DomainSecondary] — [reason]
- **User accepted / overrode:** [accepted | overrode to <other> because <reason>]

## Prior context applied

- **From CLAUDE.md / AGENTS.md / README:** [bullets, or "none discovered"]
- **From ADRs:** [bullets, or "none discovered"]
- **From prior discussions:** [bullets, or "none discovered"]
- **From git log:** [bullets, or "none discovered"]

## Gray areas discussed

### [Area 1 name]
**D-IDs locked:** D-01, D-02
**Open questions raised here:** Q-NN (if any)

**Question 1:** [verbatim question]
Options presented:
- [option A] — [one-line reason]
- [option B] — [one-line reason]
- [option C] — [one-line reason]

**User chose:** [selection]
Notes: [any follow-up clarification]

**Question 2:** [verbatim question]
Options presented: [as above]
**User chose:** [selection or "you decide → captured as Q-NN"]
Notes: [if any]

[Repeat per question. If --auto, replace per-question Q&A with:
"[auto] D-NN — chose <option>. Reason: <one-line>. Alternatives: <list>."
or "[auto] Q-NN — deferred to next phase. Candidate options: <list>."]

### [Area 2 name]
[Same shape.]

## Deferred ideas surfaced

- [Idea] → [where captured: deferred / open question / dropped]

## Canonical refs added during discussion

- `path/to/doc.md` — added when user said "[verbatim phrase that referenced it]".

## Non-lock outcomes captured

### Agent's Discretion (no IDs)
- [Area] — constraint: [the fence].

### Must Resolve Before Next Phase (blockers)
- Q-NN [Area] — Why it blocks: [reason]. Owner: [user / team / external].

### Delegated To Next Phase
- Q-NN [Area] — Constraint: [or "none"]. Routed because: [user delegated / auto-mode could not resolve].

## Scope-creep redirects

- [Idea user proposed mid-discussion] — redirected back to [current area]; captured in deferred or as a new discussion suggestion.

## Research accelerators

- **context7:**
  - `<library>` — queried for [gray area / D-NN]; informed: [one-line summary].
  - [Repeat per query, or write "not used"]
- **deepwiki:**
  - `<org>/<repo>` — asked: "[scoped question]"; informed: [one-line summary].
  - [Repeat per query, or write "not used"]
- **Skipped:** [reason — `--no-research` set / MCP unavailable / not relevant to any gray area]

## Skill-scope redirects

- [Time user asked for a spec / plan / code, and how the skill redirected to "that's the next phase's job"].

## Review loop 1

**P1 findings:** <count> — <list>
**P2 findings:** <count> — <list>
**P3 findings:** <count> — <list>
**Fixes applied:** <list of D-IDs / sections touched>
**Outstanding after loop:** <list or "none">

[Repeat for review loop 2 if it ran. If `--no-review`, write
"Review skipped (--no-review)".]

---

*This log exists for retrospectives and audits. Do not feed it to the next
phase or downstream agents — they read `CONTEXT.md`.*
```
