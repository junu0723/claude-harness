---
description: "Evaluates codebase architecture, code quality, and pattern consistency across backend and frontend, producing improvement recommendations with prioritized action items. Triggers: \"architecture\", \"structure analysis\", \"code quality\", \"improvement direction\", \"refactoring\""
---

# Architecture Evaluation (architect)

## Execution Steps

### 1. Understand Current State
```bash
cat {{ARCHITECTURE_DOC}}
cat docs/STATUS.md
cat docs/ROADMAP.md
```

### 2. Codebase Analysis

Collaboration between architect agent and code-reviewer agent:
1. architect agent: structural analysis, design evaluation
2. code-reviewer agent: code quality, pattern consistency

### Analysis Areas

#### Backend
| Area | Checklist |
|------|-----------|
| Domain structure | DDD principles, domain boundaries |
| API design | RESTful consistency, documentation |
| Security | Authentication/authorization, input validation |
| Performance | N+1 queries, caching |
| Testing | Coverage, quality |

#### Frontend
| Area | Checklist |
|------|-----------|
| Components | Reusability, single responsibility |
| State management | Data fetching patterns |
| Type safety | TypeScript usage |
| Performance | Bundle size, lazy loading |

### 3. Write Evaluation Report

```markdown
## Architecture Evaluation Report

**Date:** YYYY-MM-DD
**Version:** vX.X.X

### Summary
- Overall status: [Good/Needs Improvement/Critical]
- Key improvement areas: N
- Immediate action required: N

### Detailed Evaluation

#### Backend
**Strengths:**
- ...

**Needs improvement:**
| Item | Current | Improvement | Priority |
|------|---------|-------------|----------|
| ... | ... | ... | P2 |

#### Frontend
(same format)

### Recommended Action Items

#### Immediate (vX.X.X)
1. [P1] Item - description

#### Short-term (vX.X+1.0)
1. [P2] Item - description

#### Long-term
1. [P3] Item - description

### Suggested GitHub Issues
| Title | Priority | Labels |
|-------|----------|--------|
| [P2] Title | P2 | refactor |
```

### 4. Actions After User Approval

```bash
# Create GitHub Issue
gh issue create --title "[PX] Title" --body "content" --label "refactor"

# Update ROADMAP
# Add issue to the relevant version
```

## Evaluation Criteria
- Industry standards and best practices
- Actionable improvement proposals
- Business impact consideration
- Incremental improvement (avoid Big Bang refactoring)

## Reference Files
- **Architecture doc:** `{{ARCHITECTURE_DOC}}`
- **Domain list:** {{YOUR_DOMAINS}}
