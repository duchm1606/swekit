# Scope & Domain Rubric

> Classification rubric used by the `classify_scope_and_domain` step. Lightweight, project-agnostic. Both axes are **advisory** for the next phase — they hint at depth and shape, not at which artifacts to produce.

## Why two axes

- **Scope** = how big is the blast radius? Hints at depth of the next phase's output.
- **Domain** = where does the work live in the system? Hints at the shape of decisions to lock and the gray-area recipe to pick.

## Scope: Quick / Standard / Deep

### Quick
Use when:
- One small change, narrow blast radius.
- Doesn't touch sensitive areas (auth, persistent data, external integrations, public contracts).
- Existing patterns already cover the case.
- Reversible without migration.

Examples: copy fix, log-message tweak, single-config toggle, one-function refactor with existing tests.

### Standard
Use when:
- Bounded behavior change, single domain, predictable surface.
- Touches existing patterns but doesn't redefine them.
- Some uncertainty about edge cases; tests need updates.
- Reversible with modest effort.

Examples: add a field, new endpoint following existing convention, new screen following existing layout, bug fix that changes observable behavior.

### Deep
Use when ANY of these apply:
- Auth, authorization, sessions, identity.
- Persistent data: schema change, migration, retention, uniqueness.
- Audit / security / sensitive data / access logs.
- External providers: payment, email, push, SMS, cloud APIs, webhooks.
- Public contracts: API shape, response envelope, client-visible behavior changes.
- Cross-platform: desktop/mobile/browser split, native shell, deep links.
- Existing behavior change with weak test coverage.
- Multi-domain (more than one of SEE / CALL / RUN / READ / ORGANIZE meaningfully touched).

If any of those apply, recommend Deep. The user can narrow scope to remove the trigger, but you should not silently downgrade.

## Domain: what kind of surface

A single item has one **primary** domain and may have one **secondary**.

| Domain | Means | Typical artifact shape |
|---|---|---|
| **SEE** | User-visible surface — UI, HTML, mobile screen, terminal display | Layouts, empty/error states, loading behavior, accessibility |
| **CALL** | API / CLI / RPC / library boundary callers depend on | Endpoint shape, error envelope, versioning, idempotency |
| **RUN** | Background job, cron, service, queue worker | Trigger, schedule, retry, dead-letter, observability |
| **READ** | Generated reads — docs, reports, exports, dashboards | Format, content scope, refresh, distribution |
| **ORGANIZE** | Data model, schema, contracts, type definitions | Identity, uniqueness, optionality, migration plan |

### Picking the primary domain

Ask: "If the user only got ONE thing right, what would it be?" That's the primary.

- A new settings page that calls a new API → primary SEE, secondary CALL.
- A migration that adds a column the UI displays → primary ORGANIZE, secondary SEE.
- A scheduled report email → primary RUN, secondary READ.
- A CLI flag that prints a richer summary → primary CALL, secondary READ.

### When domain is unclear

If you genuinely can't tell, ask the user — but make options concrete:

```
Is this primarily:
  - SEE — a thing the user looks at
  - CALL — a thing the user/system invokes
  - RUN — a thing that happens on a schedule or trigger
  - READ — a generated artifact (doc, export, report)
  - ORGANIZE — a change to the underlying data model / contracts
```

## Lane / scope combinations and what they hint

| Scope | Domain | Hint to next phase |
|---|---|---|
| Quick | any | Direct patch likely sufficient; light review |
| Standard | SEE | Story + visual review |
| Standard | CALL | Story + API contract test + version note if breaking |
| Standard | RUN | Story + runbook + observability check |
| Standard | READ | Story + sample output review |
| Standard | ORGANIZE | Story + migration plan + rollback note |
| Deep | SEE | Design pass + accessibility audit + cross-device check |
| Deep | CALL | Design doc + contract test + compatibility window |
| Deep | RUN | Design doc + dry-run plan + rollback + observability |
| Deep | READ | Design doc + sample-set review + privacy check |
| Deep | ORGANIZE | Design doc + migration plan + ADR + dry-run |

Use this table as a hint only — the next phase decides what to actually produce.

## Anti-patterns

- ❌ Recommending Quick when an auth/data/external-system flag is true. Don't optimize for the user's hope of "small change".
- ❌ Picking a domain because it's the easiest to discuss. Pick the one that drives the most decisions.
- ❌ Promising the next phase a specific deliverable ("you'll need a story") — that's not this skill's call.
- ❌ Treating the Scope/Domain as a hard binding. The next phase may revise after deeper review.
