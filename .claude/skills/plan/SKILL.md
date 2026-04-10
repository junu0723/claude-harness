---
description: "Creates implementation plans with parallel/sequential execution phases, dependency graphs, round-based development workflow, and E2E test requirements. Triggers: \"plan\", \"next version\", \"roadmap\""
---

# Development Plan (plan)

## Planning Principles

### 1. Parallel vs. Sequential
- Items with no dependencies are planned for **parallel development**
- Items with dependencies are planned for **sequential development**
- Create a dependency graph

### 2. Development Rounds (main session is orchestrator)
Each item goes through **2 rounds**. **The main session MUST NOT modify code directly.**

**Round flow:**
1. Main session: Organize tasks with TaskCreate
2. Main session: Delegate development via Task tool (subagent_type=developer)
   - Include implementation requirements, relevant file paths, and patterns in the prompt
3. Main session: Delegate review via Task tool (subagent_type=code-reviewer)
   - Instruct to review the developer's changes
4. Round 2: Re-delegate to developer agent with review feedback included
5. Round 2: Final review via code-reviewer agent
6. Commit

### 3. E2E Tests Required
- Include writing/updating E2E tests for each feature
- **MUST NOT skip** - tests must be practically verifiable
- Verify that tests sufficiently cover the requirements

### 4. Final Validation
- After all item rounds complete → **browser-tester agent** tests
- If bugs are found → 1 additional round of fixes
- Start {{APP_DEV_COMMAND}} for user testing

## Code Implementation Principles

### Preserve Existing Structure & Reuse Code
- **Follow existing patterns**: Before writing new code, MUST explore similar existing code and follow the same patterns/structure
  - Example: New Filter → reference existing Filter patterns
  - Example: New Service → follow the DI/exception handling patterns in the existing Service layer
- **No duplicate code**: If the same logic already exists, reuse it as a utility/service (do not create new ones)
- **Minimal change principle**: Modify only the minimum number of files needed to implement the feature. Refactoring existing code should be a separate issue
- **DTO/type alignment**: Backend DTO field names and frontend types MUST match exactly. Finalize BE first, then write FE types

## Execution Steps

### 1. Update main branch & create working branch
```bash
# MUST start from the latest main
git checkout main
git pull origin main

# Create working branch
git checkout -b feat/v0.X.X-description
```
> MUST NOT start planning/development when main is not up to date.

### 2. Check ROADMAP
```bash
cat docs/ROADMAP.md
cat docs/STATUS.md
```

### 3. Analyze target items
- Explore related code with Explore agent
- **Explore similar existing features/patterns** — check for reusable code before creating new ones
- Identify dependencies
- Estimate effort

### 4. Write the plan

```markdown
## v0.X.X Development Plan

**Goal:** Version description
**Branch:** feat/v0.X.X-description

### Target Issues
| Issue | Title | Dependencies |
|-------|-------|--------------|
| #123  | Feature A | None |
| #124  | Feature B | #123 |

### Dependency Graph
#123 (Feature A) ──→ #125 (Feature C)
#124 (Feature B) ──┘

### Development Order

#### Phase 1 (Parallel)
| Item | Description | E2E |
|------|-------------|-----|
| #123 | Implement Feature A | Modify auth.spec.ts |

#### Phase 2 (Sequential)
| Item | Description | E2E | Prerequisite |
|------|-------------|-----|--------------|
| #124 | Implement Feature B | Add feature.spec.ts | #123 |

### Round Plan

#### Item #123
- Round 1: developer → code-reviewer
- Round 2: developer (with feedback) → code-reviewer
- Commit

#### Item #124
(same)

### Final Validation
1. browser-tester agent tests
2. If bugs found, 1 additional round of fixes
3. Start {{APP_DEV_COMMAND}}
4. Manual user testing

### Estimated Effort
- Development: N items × 2 rounds
- Testing: browser-tester + user verification
```

### 5. Start implementation after user approval

## Execution Rules
- MUST NOT: Edit/Write code directly in the main session
- MUST NOT: Perform development/review directly in the main session
- MUST use: Task tool (subagent_type=developer) for development
- MUST use: Task tool (subagent_type=code-reviewer) for review
- MUST use: Task tool (subagent_type=browser-tester) for browser testing
- Main session: exploration, planning, subagent management, and commits only

## Notes
- MUST NOT skip E2E tests
- Build verification required after each round
- Watch for conflicts during parallel development
- Commit once after all items are complete
