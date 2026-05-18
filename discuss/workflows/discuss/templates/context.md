# CONTEXT.md template — for discuss write_context step

> **Lazy-loaded.** Read only inside the `write_context` step of
> `workflows/discuss.md`, immediately before writing
> `${story_dir}/CONTEXT.md`. Do not put a reference in `<required_reading>` —
> that defeats progressive-disclosure savings.

## Variable substitutions

- `[Slug]` → kebab-case slug.
- `[Title]` → human-readable title.
- `[date]` → ISO date (YYYY-MM-DD) when discuss was gathered.
- `[Scope]` → `Quick` | `Standard` | `Deep` — advisory.
- `[DomainPrimary]` → `SEE` | `CALL` | `RUN` | `READ` | `ORGANIZE`.
- `[DomainSecondary]` → same set, or `none`.
- `[ProjectRoot]` → absolute path.
- `[DiscussDir]` → absolute path.

## Conditional sections

- **`<unresolved_auto>`** — include only when `--auto` left areas unresolved.
- **Code context** — if no implementation exists yet, write `No implementation yet — pre-code project` and skip subsections.

## Template body

```markdown
# CONTEXT: [Title]

**Slug:** [Slug]
**Gathered:** [date]
**Status:** Ready for next phase

> Output of the pre-implementation discussion. The next phase reads this to
> produce whatever artifact the project uses (spec, plan, design doc, story,
> direct patch). This document does NOT itself create those artifacts.

<classification>
## Classification (advisory)

- **Scope:** [Scope]
- **Domain:** [DomainPrimary] (+ [DomainSecondary] secondary)
- **User accepted recommendation?** [yes | overrode to <other> because <reason>]

The next phase MAY revise these after deeper review. Both the recommendation
and the user's choice are recorded above.

</classification>

<work_item>
## Work Item Boundary

[One-paragraph restatement of what this discuss covers. Be explicit about
what is in scope and what is NOT touched. The scope anchor for the next
phase and every downstream agent.]

**In scope:**
- [bullet]

**Out of scope (this discuss):**
- [bullet — usually deferred ideas summarized at a high level]

</work_item>

<decisions>
## Locked Decisions

These are fixed. The next phase implements them as written.

### [Category 1 — e.g., Failure Handling]
- **D-01:** [Headline decision]. [One-line rationale.]
- **D-02:** [Headline decision]. [One-line rationale.]

### [Category 2 — e.g., User-Visible Error Surface]
- **D-03:** [Headline decision]. [One-line rationale.]

### Agent's Discretion

[What the user delegated to the agent within a stated constraint. Not numbered
— these aren't locks, they're permissions with a fence. Remove the subsection
if none. Examples: "Pick the icon set; must be open-source and license-clean."
"Pick the test framework; must support snapshot testing."]

- [Area] — constraint: [the fence the agent must stay inside].

[If --auto left areas unresolved, insert this subsection:]
<unresolved_auto>
### Unresolved in --auto
- [Area] — [Reason auto-mode could not lock it.] Re-run without --auto or
  resolve in the next phase. Moved to `<open_questions>` → Delegated.
</unresolved_auto>

</decisions>

<canonical_refs>
## Canonical References

**The next phase MUST read these before producing artifacts.**

[MANDATORY section. Full paths relative to PROJECT_ROOT. Group by topic.
Every entry includes a one-line note explaining what it decides/defines
that is relevant to this discuss.]

### [Topic area 1]
- `<path>/some-doc.md` — [What it locks that applies here]
- `<path>/another.md` §<section> — [Specific section reference]

### [Topic area 2]
- `.discuss/<other-slug>/CONTEXT.md` — [Why this earlier discussion matters here]

### Research-accelerator sources (when used)
- `context7:<library-name>` — [Which gray area / D-NN this informed; one-line summary]
- `deepwiki:<org>/<repo>` — [Which gray area / D-NN this informed; one-line summary]

[Omit the Research-accelerator subsection entirely if neither MCP was used.]

[If no external docs apply: "No external specs — requirements fully captured
in decisions above"]

</canonical_refs>

<code_context>
## Existing Code Insights

[Skip subsections and write "No implementation yet — pre-code project" if applicable.]

### Reusable Assets
- [Component/util/schema]: [How it could be reused here]

### Established Patterns
- [Pattern]: [How it constrains/enables this discuss]

### Integration Points
- [Where new code attaches to the existing system]

</code_context>

<specifics>
## Specific Requests

[Any "I want it like X" moments — concrete UI references, behavior examples,
phrasings the user wants used verbatim, etc. Captures product texture for
the next phase.]

[If none: "No specific requests — standard approaches acceptable"]

</specifics>

<open_questions>
## Outstanding Questions

Two buckets. Both use a shared `Q-NN` namespace so IDs stay traceable.

### Must Resolve Before Next Phase

[Genuine blockers. The next phase should NOT start until these are answered.
Items here came from gray-area discussion where the user could not pick — not
"you decide", but "I don't know yet" or "need to check with X first".]

- **Q-01:** [Blocker question]. Why it blocks: [what the next phase cannot do
  without an answer]. Owner: [who answers — user / team / external].

[If none: "None — no blockers"]

### Delegated To Next Phase

[Technical or implementation questions the user explicitly delegated, OR areas
auto-mode could not auto-resolve. The next phase resolves these itself with
deeper investigation; the user does not need to answer first.]

- **Q-NN:** [Delegated question]. Constraint: [the fence — or "none"].

[If none: "None — all decisions locked or in Agent's Discretion"]

</open_questions>

<deferred>
## Deferred Ideas

[Ideas that came up but belong in separate discussions. Don't lose them.]

- [Idea] → next-step suggestion (e.g., "open a separate discuss for X").

[If none: "None — discussion stayed within bounded item"]

</deferred>

<handoff>
## Next-Step Pointer

This CONTEXT.md is the canonical handoff. The next phase reads it and
produces whatever artifact the project uses (spec / plan / design doc /
story / direct patch).

See `discuss/references/handoff-contract.md` for what the next phase is
expected to do with this file.

</handoff>

---

*Slug: [Slug]*
*Scope (advisory): [Scope] · Domain: [DomainPrimary]*
*Gathered: [date]*
```
