# Tool Commands Reference

Verification commands for package research.

**Tool Priority Chain:** Context7 → Exa → Firecrawl → Brave → WebSearch
**Stats Verification:** npm + bundlephobia (ALWAYS run)

---

## Context7 (PRIMARY — HIGH)

Use FIRST for library documentation.

```
# Step 1: Resolve library ID
mcp__context7__resolve-library-id with libraryName: "{package}"

# Step 2: Query docs with resolved ID
mcp__context7__query-docs with libraryId: {resolved-id}, query: "{specific question}"
```

**Tag all findings:** `[CITED: context7/{library}]`

---

## Exa MCP (HIGH)

Deep search with structured results.

```
# Use Exa MCP tool for research queries
mcp__exa__search with query: "{package} {topic}"
mcp__exa__search with query: "{package} vs {alternative}"
mcp__exa__search with query: "{package} migration guide"
```

**Best for:**
- Research papers, blog posts with depth
- Comparison articles
- Migration guides
- "Why company X chose Y" case studies

**Tag all findings:** `[CITED: exa/{result-url}]`

---

## Firecrawl MCP (HIGH)

Scrape official documentation directly.

```
# Scrape specific documentation page
mcp__firecrawl__scrape with url: "https://docs.{package}.dev/{page}"
mcp__firecrawl__scrape with url: "https://github.com/{owner}/{repo}/blob/main/README.md"

# Crawl documentation site
mcp__firecrawl__crawl with url: "https://docs.{package}.dev"
```

**Best for:**
- Official docs not in Context7
- CHANGELOG files
- Migration guides from official sources
- API reference pages

**Tag all findings:** `[CITED: {url}]`

---

## Brave Search (MEDIUM)

Community patterns and discussions.

```
# Use Brave Search MCP or WebSearch with Brave
mcp__brave__search with query: "{package} best practices 2025"
mcp__brave__search with query: "{package} production issues"
```

**Best for:**
- Community patterns and discussions
- Stack Overflow threads
- Reddit/HN discussions
- "Problems at scale" searches

**Tag all findings:** `[CITED: {url}]` — verify claims against primary sources

---

## WebFetch (MEDIUM)

Use for official docs not in Context7:

```
WebFetch: "https://docs.{package}.dev/..."
WebFetch: "https://github.com/{owner}/{repo}/blob/main/README.md"
WebFetch: "https://github.com/{owner}/{repo}/blob/main/CHANGELOG.md"
```

**Tag all findings:** `[CITED: {url}]`

---

## WebSearch (FALLBACK — LOW)

Use only when higher-priority tools fail.

```
# Ecosystem discovery
WebSearch: "{package} best practices {year}"
WebSearch: "{package} vs {alternative} {year}"

# Survivorship bias check
WebSearch: "{package} migrated away from {year}"
WebSearch: "{package} problems at scale"

# Confirmation bias check
WebSearch: "why not {package}"
WebSearch: "{package} alternatives {year}"
```

**Tag all findings:** `[ASSUMED]` — MUST cross-reference with primary sources

**Verification required:** Cross-reference with npm/Context7/official docs before citing.

---

## npm Registry (VERIFICATION — AUTHORITATIVE)

**ALWAYS RUN for every candidate package.**

```bash
# Full package info
npm view $PKG

# Specific fields
npm view $PKG version
npm view $PKG time.modified
npm view $PKG downloads-last-week
npm view $PKG repository.url
npm view $PKG homepage
npm view $PKG license

# TypeScript support
npm view $PKG types typings

# Dependencies count
npm view $PKG dependencies | wc -l

# Check for deprecation
npm view $PKG deprecated
```

**Tag all findings:** `[VERIFIED: npm]`

---

## Bundlephobia (VERIFICATION — AUTHORITATIVE)

**ALWAYS RUN for every candidate package.**

```bash
# Bundle size API
curl -s "https://bundlephobia.com/api/size?package=$PKG" | jq '.'

# Extract key fields
curl -s "https://bundlephobia.com/api/size?package=$PKG" | jq '{
  name: .name,
  version: .version,
  size: .size,
  gzip: .gzip,
  dependencyCount: .dependencyCount
}'

# With version
curl -s "https://bundlephobia.com/api/size?package=$PKG@$VERSION" | jq '.'
```

**Tag all findings:** `[VERIFIED: bundlephobia]`

---

## Hard Disqualify Checks

```bash
# Last update > 2 years
npm view $PKG time.modified
# Parse date, compare to now

# Low downloads
npm view $PKG downloads-last-week
# Threshold: 1000/week unless niche

# Deprecated
npm view $PKG deprecated
# Any value = disqualify

# TypeScript types (if TS project)
npm view $PKG types typings
# Empty = check @types/{pkg}
npm view @types/$PKG version 2>/dev/null
```

---

## Quick Verification Script

```bash
#!/bin/bash
PKG=$1

echo "=== $PKG ==="
echo "Version: $(npm view $PKG version 2>/dev/null)"
echo "Updated: $(npm view $PKG time.modified 2>/dev/null)"
echo "Downloads: $(npm view $PKG downloads-last-week 2>/dev/null)"
echo "Types: $(npm view $PKG types 2>/dev/null || echo 'none')"
echo "Deprecated: $(npm view $PKG deprecated 2>/dev/null || echo 'no')"
echo "Bundle: $(curl -s "https://bundlephobia.com/api/size?package=$PKG" | jq -r '.gzip // "unknown"') bytes gzip"
```

---

## Comparison Quick Check

```bash
# Compare multiple packages
for pkg in pkg1 pkg2 pkg3; do
  echo "$pkg: $(npm view $pkg version) | $(npm view $pkg time.modified | cut -d'T' -f1) | $(npm view $pkg downloads-last-week)"
done
```
