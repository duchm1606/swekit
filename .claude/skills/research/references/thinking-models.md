# Thinking Models for Research

Apply these at decision points during research, not continuously.
Each model counters a specific documented failure mode.

Source: gsd thinking-models-research.md

---

## When to Apply

| Model | Apply When |
|-------|------------|
| Survivorship Bias | After gathering evidence FOR a recommendation |
| Confirmation Bias | After forming initial recommendation |
| First Principles | Before accepting any recommendation |
| Steel Man | Before recommending AGAINST an alternative |

---

## 1. Survivorship Bias Counter

**Counters:** Only finding successful examples while missing failures.

**Protocol:**

After gathering evidence FOR a recommended package:

```
WebSearch: "{package} migrated away from {year}"
WebSearch: "{package} problems at scale"
WebSearch: "replaced {package} with"
```

**Check for:**
- Projects that abandoned it
- GitHub issues with "migrated", "replaced", "switched to"
- Unresolved issues at scale

**Rule:** Weight negative evidence MORE heavily — failures are underreported.

**If found:** Document in COMPARISON.md under "Survivorship Check" section.

---

## 2. Confirmation Bias Counter

**Counters:** Searching for evidence that confirms initial hypothesis.

**Protocol:**

After forming your initial recommendation:

1. Spend one full research cycle searching AGAINST it:
   ```
   WebSearch: "{package} problems"
   WebSearch: "{package} alternatives"
   WebSearch: "why not {package}"
   WebSearch: "{package} vs {competitor}"
   ```

2. For each piece of disconfirming evidence:
   - Refute with higher-confidence sources, OR
   - Add as caveat to recommendation

**Rule:** If you cannot find ANY criticism, your search was too narrow.

---

## 3. First Principles Thinking

**Counters:** Accepting surface-level recommendations without decomposition.

**Protocol:**

Before accepting any recommendation:

1. **What problem does this solve?**
   - State the actual problem from CONTEXT.md
   - Not "we need a date library" but "we need to parse ISO dates and display relative time"

2. **What are the non-negotiable requirements?**
   - List constraints from CONTEXT.md
   - These are pass/fail, not nice-to-have

3. **Does this recommendation satisfy them from first principles?**
   - Check each requirement directly
   - Not "it's popular so it probably works"

**Rule:** If you cannot explain WHY from first principles, flag as `[LOW]` confidence.

---

## 4. Steel Man (Optional)

**Counters:** Dismissing alternatives without fair consideration.

**Protocol:**

Before recommending against an alternative:

1. Construct its STRONGEST possible case
2. What would a passionate advocate say?
3. What use cases does it serve better?
4. What trade-offs favor it?

**Output:** Present steel-manned alternative alongside recommendation.

**Rule:** If steel-manned alternative is competitive, flag as `[NEEDS DECISION]` rather than unilateral recommendation.

---

## When NOT to Apply

Skip structured reasoning when:

- **Locked decisions from CONTEXT.md** — Don't run Steel Man on already-decided choices
- **Standard stack lookups** — Checking version of well-known library
- **Single option phases** — No alternatives to evaluate
- **Quick tier research** — Speed over depth

---

## Quick Reference

| Situation | Model to Apply |
|-----------|---------------|
| "This package looks perfect" | Survivorship Bias → search for failures |
| "I'm recommending X" | Confirmation Bias → search against X |
| "Everyone uses X" | First Principles → does it solve OUR problem? |
| "X is better than Y" | Steel Man → give Y its best case |
