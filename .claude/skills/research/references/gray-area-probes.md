# Gray Area Probes by Domain

Use these probes in Phase 1 to extract constraints. Select 2-4 probes per domain that apply.
Do NOT use all probes — pick the ones genuinely undecided for this research.

Source: gsd domain-probes + khuym gray-area-probes.

---

## UI — Component libraries, styling, design systems

**Bundle & Performance**
- Is bundle size critical? (frontend SPA vs SSR vs backend)
- Tree-shaking required?
- SSR/RSC compatibility needed?

**Design System Fit**
- Must integrate with existing design system? (shadcn, MUI, etc.)
- Need unstyled/headless components?
- Accessibility (WCAG) level required?

**Ecosystem**
- React-only or framework-agnostic?
- Need Tailwind integration?
- Animation requirements?

---

## DATA — ORMs, validation, state management

**TypeScript**
- TypeScript-first required?
- Need type inference from schema?
- Runtime validation needed?

**Scale & Performance**
- Data volume expectations? (100 rows vs 1M rows)
- Real-time sync needed?
- Optimistic updates required?

**Integration**
- Must work with existing ORM/database?
- GraphQL or REST?
- Server components compatibility?

---

## INFRA — Build tools, testing, CI/CD

**Speed vs Features**
- Build speed critical? (large monorepo)
- Need HMR/fast refresh?
- Incremental builds required?

**Ecosystem**
- Must work with existing toolchain?
- Plugin ecosystem important?
- Team familiarity with alternatives?

**Configuration**
- Zero-config preference?
- Need extensive customization?
- Monorepo support?

---

## NETWORK — HTTP clients, WebSocket, real-time

**Environment**
- Browser, Node, or both?
- Edge runtime compatibility?
- Bundle size constraints?

**Features**
- Retry/timeout handling built-in?
- Request/response interceptors?
- Streaming support?

**Error Handling**
- Typed error responses?
- Automatic error parsing?
- Offline support?

---

## UTIL — Date, string, crypto, helpers

**Bundle Impact**
- Full library or specific functions?
- Tree-shaking support?
- Alternative: native APIs sufficient?

**Maintenance**
- Actively maintained?
- Security-sensitive? (crypto)
- Localization needs? (date/time)

---

## TESTING — Runners, assertions, mocking

**Speed**
- Watch mode performance?
- Parallel execution?
- Incremental test runs?

**Ecosystem**
- Jest compatibility needed?
- Existing test infrastructure?
- Coverage requirements?

**Features**
- Snapshot testing?
- Mocking depth? (modules, timers, network)
- Browser testing needed?

---

## Cross-Cutting Probes

Apply to any domain when relevant:

**Constraints**
- What is explicitly OUT of scope?
- Any existing pattern this should match?
- Prior decisions in this domain?

**Risk**
- Is this a strategic/long-term dependency?
- Switching cost if wrong choice?
- Team familiarity considerations?

**Integration**
- What existing code touches this?
- API surface area concerns?
- Migration path from current solution?
