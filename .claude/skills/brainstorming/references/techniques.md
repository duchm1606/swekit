# Brainstorming Techniques

## Divergent Techniques

### Inversion
Flip the problem. Instead of "how to make X faster", ask "what makes X slow?"

```markdown
**Normal:** How do we improve user onboarding?
**Inverted:** What makes onboarding terrible?
- Too many steps
- Unclear value proposition
- No progress indication

**Insight:** Remove steps, show value immediately, add progress bar
```

### Analogy
Borrow from other domains.

```markdown
**Problem:** How to handle config across environments?
**Analogies:**
- How does Docker handle this? → Environment variables, layers
- How does Kubernetes? → ConfigMaps, Secrets
- How does 12-factor app? → Config in environment, not code

**Insight:** Separate config from code, environment-specific overrides
```

### Decomposition
Break into smaller parts.

```markdown
**Problem:** Build a notification system
**Parts:**
1. Triggering (when to notify)
2. Routing (who gets it)
3. Delivery (how it arrives)
4. Tracking (did they see it)

**Insight:** Each part can be solved independently
```

### Combination
Merge existing solutions.

```markdown
**Existing:**
- A: Fast but limited
- B: Flexible but slow

**Combination:** Use A for hot path, B for edge cases
```

### Elimination
Remove the need entirely.

```markdown
**Problem:** How to sync data between services?
**Elimination:** What if they didn't need to sync?
- Single source of truth
- Event-driven updates
- Query on demand

**Insight:** Maybe we don't need sync at all
```

---

## Problem Framing Templates

### 5 Whys

```markdown
**Problem:** Deployments are slow

1. Why? → CI pipeline takes 20 minutes
2. Why? → Running all tests every time
3. Why? → No test categorization
4. Why? → Tests grew organically
5. Why? → No testing strategy

**Root:** Need testing strategy, not faster CI
```

### Constraint Mapping

```markdown
| Constraint | Type | Negotiable? |
|------------|------|-------------|
| Must use Go | Tech | No |
| 2 week deadline | Time | Maybe |
| Team of 2 | Resource | No |
| Must integrate with X | Dependency | No |
```

### Impact/Effort Matrix

```markdown
              Low Effort    High Effort
High Impact   [Quick Wins]  [Major Projects]
Low Impact    [Fill-ins]    [Avoid]
```

---

## Option Generation Prompts

When stuck, try these:

| Prompt | Generates |
|--------|-----------|
| "What would a 10x solution look like?" | Ambitious options |
| "What's the laziest solution?" | Simple options |
| "What would we do with unlimited budget?" | Unconstrained options |
| "What would we do with zero budget?" | Creative constraints |
| "How would X solve this?" | Domain expertise (X = expert/company) |
| "What if we had to ship tomorrow?" | MVP options |
| "What if this had to last 10 years?" | Sustainable options |

---

## Evaluation Criteria

### Feasibility

| Level | Meaning |
|-------|---------|
| HIGH | Team has skills, tools exist, low risk |
| MED | Some learning needed, tools available |
| LOW | New territory, uncertain tools |

### Impact

| Level | Meaning |
|-------|---------|
| HIGH | Directly solves core problem |
| MED | Partial solution or enables future work |
| LOW | Nice to have, tangential |

### Fit

| Level | Meaning |
|-------|---------|
| HIGH | Aligns with stack, team, timeline |
| MED | Some adaptation needed |
| LOW | Significant changes required |
