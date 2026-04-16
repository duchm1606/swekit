# Comparison Template

Use this template for COMPARISON.md output.

Source: gsd-advisor-researcher 5-column format.

---

## Template

```markdown
# {Category} Comparison

**Research Date:** YYYY-MM-DD
**Scope:** Quick | Standard | Deep
**Calibration:** full_maturity | standard | minimal_decisive

## Context

**Constraints from CONTEXT.md:**
- C1: {constraint}
- C2: {constraint}

**Domain:** {UI | DATA | INFRA | NETWORK | UTIL | TESTING}

---

## Verification Stats

| Package | Version | Last Update | Downloads/wk | Bundle (gzip) | Types |
|---------|---------|-------------|--------------|---------------|-------|
| {pkg} | {ver} | {date} | {N} | {X}kB | {built-in/@types/none} |

**All stats: [VERIFIED: npm] + [VERIFIED: bundlephobia]**

---

## Comparison Matrix

| Option | Pros | Cons | Complexity | Recommendation |
|--------|------|------|------------|----------------|
| {pkg} | {pros, comma-separated} | {cons, comma-separated} | {impact} — Risk: {risk} | Rec if {condition} |

---

## Detailed Analysis

### {Option 1}

**Strengths:**
- {strength with source tag}

**Weaknesses:**
- {weakness with source tag}

**Best for:** {use cases}

### {Option 2}

**Strengths:**
- {strength with source tag}

**Weaknesses:**
- {weakness with source tag}

**Best for:** {use cases}

---

## Survivorship Check

**Searched for:**
- "{package} migrated away from {year}"
- "{package} problems at scale"

**Findings:**
- {finding or "No significant abandonment stories found"}

---

## Open Questions

<!-- Mark (RESOLVED) in heading when all resolved -->

1. {Question} — {status: open | resolved}

---

## Recommendation

**Winner:** `{package}@{version}`

**Why:** {One paragraph rationale tied to constraints}

**Runner-up:** `{alt-package}` — {when to use instead}

---

## Confidence Assessment

| Area | Level | Reason |
|------|-------|--------|
| Stats verification | {HIGH/MEDIUM/LOW} | {npm/bundlephobia checked} |
| Feature comparison | {HIGH/MEDIUM/LOW} | {Context7/docs depth} |
| Risk assessment | {HIGH/MEDIUM/LOW} | {survivorship check done} |

---

## Sources

### [VERIFIED] — Authoritative
- npm registry — version, downloads, last update
- bundlephobia.com — bundle size

### [CITED: url] — High confidence
- Context7 — {library docs queried}
- {Official docs URL} — {what was checked}
- Exa/Firecrawl results — {specific pages}

### [ASSUMED] — Requires verification
- {WebSearch findings} — {cross-reference before locking}
```

---

## Column Rules

### Complexity Column

**Format:** `{impact surface} — Risk: {specific risk}`

**Good:**
- `3 files, new dep — Risk: bundle +15kB`
- `API migration — Risk: breaking changes`
- `Config only — Risk: low`

**Bad:**
- `Medium` (too vague)
- `2 hours` (no time estimates)
- `Complex` (not specific)

### Recommendation Column

**Format:** `Rec if {condition}` or `Rec for {use case}`

**Good:**
- `Rec if bundle size critical`
- `Rec for TypeScript-first projects`
- `Rec if SSR/RSC needed`

**Bad:**
- `Best choice` (no condition)
- `Recommended` (no context)
- `#1` (ranking, not conditional)

---

## Calibration Guidelines

### full_maturity
- 3-5 options
- Include: stars, project age, ecosystem size
- Weighted toward battle-tested tools
- Full survivorship check

### standard
- 2-4 options
- Basic stats sufficient
- Conditional recommendations
- Quick survivorship check

### minimal_decisive
- 2 options maximum
- Decisive single recommendation
- Brief rationale (1-2 sentences)
- Skip survivorship (Quick tier)
