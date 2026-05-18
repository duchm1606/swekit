# Research Accelerators — context7 + deepwiki

> Optional MCP-backed lookups that improve gray-area options when current library docs or external prior-art would help the user choose. Both are **opt-in**. The skill must work without either; if an MCP is unavailable, log a one-line note and continue with what you can reason from code and prior decisions alone.
>
> Disabled by `--no-research` flag.

## When to use which

| Need | Use | Why |
|---|---|---|
| Current best practice for a library or framework choice (`next-auth` vs `iron-session`, `pinecone` vs `pgvector`, etc.) | **context7** | Library docs are version-sensitive; training-data summaries decay. Context7 returns the docs as they are now. |
| Current API shape / config syntax for a tool the user wants to use | **context7** | Same reason — get the syntax from the current docs, not memory. |
| "How does <popular open-source project> handle X?" | **deepwiki** | Repository wiki / structured knowledge about that codebase. |
| "Is there prior art for this in the open-source ecosystem?" | **deepwiki** | Lets you ask the wiki of a comparable project. |
| Implementation-detail questions (which file does Y live in?) | **scout_codebase** (rg + Read) — NOT these MCPs | These MCPs are external; local code lookup is local-first. |
| Architectural pattern questions ("CQRS or event sourcing?") | **neither** | Out of scope for discussion. Brainstorming locks user intent; pattern choice belongs to planning/design. |

## Triggers in the workflow

### context7 — trigger inside `analyze_item` and `discuss_areas`

Use when generating options for a gray area whose answer depends on a specific library or framework's current capabilities. Examples:

- Gray area: "Authentication strategy" → query `next-auth` docs to inform options (OAuth+PKCE current, passkeys gaining adoption).
- Gray area: "Vector store choice" → query `pinecone` and `pgvector` to compare current API shapes.
- Gray area: "ORM migration strategy" → query the ORM's current migration docs.

Sequence:

1. `mcp__plugin_context7_context7__resolve-library-id` with the library name to get the canonical ID.
2. `mcp__plugin_context7_context7__query-docs` with the resolved ID and a specific question.
3. Use the response to **inform** the options you present. Don't paste raw docs. Summarize in 1–3 bullets and cite the library name.
4. Add the library doc URL (if returned) to `<canonical_refs>` in CONTEXT.md.

**Anti-patterns:**

- ❌ Querying context7 for every gray area. Use only when library-specific knowledge would change the recommendation.
- ❌ Pasting raw doc output into the question. Summarize.
- ❌ Treating context7 as authoritative over the user. If the user says "use X anyway", that's the lock — context7 is advisory.
- ❌ Querying for a library when no library has been mentioned. Don't introduce dependencies via the back door.

### deepwiki — trigger inside `discover_prior_context` and on user reference

Use when the user references an external project or when you genuinely need prior-art research, not implementation details. Examples:

- User says "We want comments to work like GitHub's" → ask deepwiki about GitHub comment threading model.
- User says "Look at how Linear handles offline writes" → ask deepwiki about Linear's sync architecture.
- Gray area: "Notification batching" and user hasn't expressed a preference → optionally check how 1–2 named OS projects handle it, if and only if the user names one.

Sequence:

1. `mcp__deepwiki__read_wiki_structure` to see what's documented about the named repo.
2. `mcp__deepwiki__ask_question` with a specific, scoped question (not "tell me about X").
3. Summarize the response in 2–3 bullets. Cite the source repo.
4. Add the repo reference (e.g., `deepwiki: <org>/<repo>`) to `<canonical_refs>` in CONTEXT.md with a one-line note on what it informed.

**Anti-patterns:**

- ❌ Trawling deepwiki for inspiration the user didn't ask for. Only use when the user references a project.
- ❌ Asking broad questions ("how does Linear work?"). Scope to the gray area.
- ❌ Letting deepwiki output drive decisions. The user's product intent overrides external patterns.

## Availability check

Before invoking either MCP, the workflow assumes the tool may not be installed. Handle the failure path:

```
If the MCP tool returns an error or is unavailable:
  log: "[research] context7 unavailable — continuing without library-doc lookup"
  proceed with options you can reason from code and prior decisions
```

Never block the discussion on an MCP failure. The skill produces a valid CONTEXT.md whether or not external research happened.

## Citing in CONTEXT.md

When MCP research informed an option or decision, cite the source in **two** places:

1. The **option annotation** during discussion:
   ```
   - next-auth (OAuth+PKCE is current 2026 standard — per context7 next-auth docs)
   ```

2. The **`<canonical_refs>`** section of CONTEXT.md:
   ```
   ### Library / framework references
   - `context7:next-auth` — informed Authentication strategy (D-04). Confirmed PKCE-flow is the current recommendation.
   - `deepwiki:linear/linear` — informed Offline-write architecture (D-07).
   ```

This lets the next phase trace where each recommendation came from and re-query if the docs change.

## When NOT to use either

Skip both entirely when:

- `--no-research` flag is set.
- The user is on a clearly air-gapped or offline run (env var `OFFLINE=true` or similar).
- The discussion is about purely product / UX decisions (Empty state styling, copy choices) — these don't benefit from library docs or external prior art.
- The user explicitly says "don't pull from anywhere, just my preferences".

In all of those, fall back to options reasoned from local code, prior decisions, and the user's stated preferences. Note it in `DISCUSSION-LOG.md` under "Research accelerators":

```markdown
## Research accelerators

Used: <list of MCPs invoked and what they informed>, or "none — skipped per --no-research"
```
