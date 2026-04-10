---
description: "Analyzes code changes for performance issues: N+1 queries, unnecessary re-renders, memory leaks, bundle size impact, and missing indexes. Triggers: \"perf\", \"performance\", \"slow\", \"optimization\""
---

# Performance Check

Analyzes the performance impact of changed code and suggests improvements.

## Execution Steps

### Step 1: Identify Scope of Changes

```bash
git diff --name-only HEAD~1
```

### Step 2: Layer-by-Layer Performance Check

#### Backend

**N+1 query detection:**
- JPA/Hibernate: check fetch strategy for `@OneToMany`/`@ManyToOne` relationships
- `FetchType.LAZY` + access inside a loop = potential N+1
- Recommend `@EntityGraph` or `JOIN FETCH`

**DB query optimization:**
- Whether pagination is applied for large data retrieval
- Whether indexes exist on columns used in WHERE clauses
- Query only needed columns instead of SELECT *

**Caching opportunities:**
- Service methods called repeatedly with the same parameters
- Whether caching is applied to infrequently changing data

#### Frontend

**Unnecessary re-renders:**
```bash
# Detect inline object/function creation (cause of re-renders)
grep -rn "style={{" --include="*.tsx" --include="*.jsx"
grep -rn "onClick={() =>" --include="*.tsx" --include="*.jsx"
```

**Bundle size:**
```bash
# Detect large imports
grep -rn "import .* from ['\"]lodash['\"]" --include="*.ts" --include="*.tsx"
grep -rn "import .* from ['\"]moment['\"]" --include="*.ts" --include="*.tsx"
```
- lodash -> lodash-es or individual imports
- moment -> date-fns or dayjs

**Memory leaks:**
- Presence of useEffect cleanup functions
- EventListener registered but not removed
- setInterval/setTimeout properly cleared

#### Infrastructure

**Docker/Deployment:**
- Changes in image size
- Whether multi-stage builds are used
- Unnecessary files included in .dockerignore

### Step 3: Performance Report

```markdown
## Performance Analysis Results

### Findings

| Severity | Area | File | Description | Expected Impact |
|----------|------|------|-------------|----------------|
| High | Backend | path:42 | N+1 query | List API response time multiplied by N |
| Medium | Frontend | path:15 | Inline object | Unnecessary re-renders |
| Low | Frontend | path:88 | Full lodash import | Bundle +70KB |

### Recommended Actions
1. [High] Fix immediately — directly impacts user-perceived performance
2. [Medium] Fix recommended in this PR
3. [Low] Can be addressed at a later time
```

## MUST Rules

- [ ] List APIs have pagination applied
- [ ] No N+1 query patterns
- [ ] No unnecessary re-render causes in frontend components
- [ ] Large libraries imported in a tree-shakable form
