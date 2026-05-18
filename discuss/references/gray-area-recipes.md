# Gray Area Recipes

> Generate **specific** gray areas, not generic category labels. Recipes are organized by Domain (SEE / CALL / RUN / READ / ORGANIZE) plus universal recipes that apply regardless. Use them to seed `analyze_item` — then refine based on `<code_context>` and prior decisions.

## Generation rules

1. **One gray area = one decision that would change the output artifact.** If two options produce identical artifacts, it's not a gray area — it's a tactical choice the next phase makes.
2. **Concrete labels.** `"Partial-failure policy"` not `"Behavior"`. `"Invite expiry window"` not `"UX"`.
3. **Annotate** every gray area with either (a) the relevant code/file pattern, (b) a prior decision/ADR that bounds the answer, or (c) "no prior art".
4. **3–4 areas per discuss.** More fragments the discussion; fewer often leaves real ambiguity unresolved.
5. **Skip if pre-answered.** Cross-check `<prior_decisions>` first. Mark settled ones `pre-answered: <where>` and don't ask.
6. **Universal recipes always apply.** Domain recipes are additive.

## Universal recipes (every discuss)

- **Failure mode** — what happens when the operation can't complete? abort / retry / skip / surface / fall back?
- **Reversibility** — is the change reversible? if not, what's the rollback path?
- **Backward compatibility** — does old data, old clients, or old behavior still work?
- **Observability** — what should be logged, metered, or alerted on?

## Domain: SEE (user-visible surface)

- **Layout style** — cards / list / table / timeline / canvas?
- **Empty state** — what does the user see before any data exists?
- **Loading state** — skeleton / spinner / inline progress / nothing?
- **Error surface** — toast / inline / modal / page-level / silent log?
- **Density** — compact / comfortable / spacious?
- **Responsive behavior** — does the layout change at small widths, and how?
- **Accessibility constraint** — any specific A11y requirements (keyboard nav, screen reader, color contrast)?
- **Animation / motion** — animated transitions or instant?

## Domain: CALL (API / CLI / RPC / library)

- **Request shape** — required fields, optional fields, default values?
- **Response envelope** — bare object / wrapped / paginated / streaming?
- **Error contract** — error code list / HTTP status mapping / structured error body?
- **Idempotency** — is the call idempotent? if not, how is replay handled?
- **Versioning** — additive change / breaking change? version bump?
- **Authentication / authorization** — who can call this? what scope is required?
- **Rate limiting** — limited per caller? what's the limit?
- **Backward compatibility** — old clients still work after the change?

## Domain: RUN (job / cron / service / worker)

- **Trigger** — cron schedule / event / queue / manual?
- **Concurrency** — single instance / multiple workers / locked?
- **Retry policy** — none / fixed / exponential? max attempts?
- **Dead-letter behavior** — fail loud / fail silent / DLQ?
- **Partial progress** — checkpointable / restart from beginning?
- **Observability** — what shows up in logs / metrics / traces?
- **Failure surface** — who gets notified, where?
- **Rollout** — flagged behind toggle / dark launch / shipped on merge?

## Domain: READ (generated artifact — doc, report, export)

- **Format** — JSON / CSV / HTML / Markdown / PDF / structured doc?
- **Content scope** — full snapshot / delta since last / filtered by query?
- **Refresh model** — on-demand / scheduled / push?
- **Distribution** — file on disk / email / webhook / dashboard?
- **Privacy** — what's redacted? who can read?
- **Sample size** — full data / first N rows / sampled?
- **Stability** — schema may evolve / fixed contract for consumers?

## Domain: ORGANIZE (data model / schema / contracts)

- **Identity** — what's the primary key? natural or surrogate?
- **Uniqueness** — what combinations must be unique?
- **Optionality** — required / optional? what's the default for existing rows?
- **Deletion model** — hard delete / soft delete / archive?
- **Retention** — kept forever / TTL / explicit purge policy?
- **Migration safety** — online / requires window / requires backfill?
- **Compatibility** — old consumers tolerate new shape?
- **Indexing** — what query patterns must stay fast?

## Annotation patterns

When listing a gray area to the user, attach a short annotation:

```text
☐ Partial-failure policy — abort vs skip-and-report
   (lib/db/transactions.ts uses transactional abort everywhere; switching
   to skip-and-report would introduce a new pattern. Reversibility wins
   here unless the user explicitly wants partial commits.)
```

The annotation answers "why might this be hard / non-obvious?" Without it, the user can't tell which option has the smaller blast radius.

## Anti-patterns

- ❌ Generic categories: `UI`, `UX`, `Behavior`, `Performance`.
- ❌ Implementation-detail questions: `Which library should we use?` (next phase decides).
- ❌ Architecture probes: `Should we use CQRS?` (out of scope for discuss).
- ❌ Scope questions: `Should we also do X?` (scope creep; see guardrail).
- ❌ Bundling: `Layout AND loading behavior AND empty state` (one question per turn).
- ❌ False binaries: presenting 2 options when a third is obviously relevant.

## Tie-breaker order when recommending

When multiple options are viable, recommend in this order:

1. Matches a prior decision / ADR.
2. Matches an existing code pattern in `<code_context>`.
3. Smaller blast radius (less reversible damage if wrong).
4. Simpler (YAGNI / KISS).
5. Reversible later if wrong.

Surface the rationale in the option's annotation.
