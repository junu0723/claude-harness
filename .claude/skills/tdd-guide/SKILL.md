---
description: "Guides Test-Driven Development workflow: writes failing tests first, implements minimal code to pass, then refactors with safety net. Supports unit, integration, and E2E test cycles. Triggers: \"tdd\", \"test first\", \"red green refactor\""
---

# TDD Guide

Test-driven development workflow following the Red-Green-Refactor cycle.

## Workflow

### Phase 1: RED — Write a Failing Test

1. Convert requirements into test cases
2. **MUST**: Write the test first and run it to **confirm it fails**
3. Verify the test fails for the right reason (assertion failure, not a compile error)

```bash
# Run test -> confirm failure
# Java/Gradle: ./gradlew test --tests "*TargetTest"
# JS/TS: pnpm test -- --testPathPattern="target.test"
# Python: pytest tests/test_target.py -v
```

### Phase 2: GREEN — Pass with Minimal Code

1. Write the **simplest possible** implementation to make the test pass
2. **MUST NOT**: Add unnecessary abstractions, optimizations, or error handling at this stage
3. Re-run tests -> confirm they pass

### Phase 3: REFACTOR — Improve Safely

1. Improve code while keeping tests green
2. Remove duplication, improve naming, apply patterns
3. **MUST**: Re-run tests after each refactoring step to confirm no regressions

## Test Writing Principles

### Given-When-Then Structure
```
Given: initial state / preconditions
When:  execute the behavior under test
Then:  verify expected outcome
```

### Test Naming Convention
- what_underWhatCondition_expectedResult
- Example: `should_return_404_when_user_not_found`
- Example: `createProject_withDefaultSettings_appliesDefaults`

### MUST Rules
- [ ] Each test verifies a single behavior
- [ ] No dependencies between tests (each can run independently)
- [ ] Use mock/stub for external dependencies (unit tests)
- [ ] Integration tests use real dependencies
- [ ] Test data is created and cleaned up within each test

## Repeating the Cycle

Add multiple test cases for a feature sequentially:

1. Happy path first
2. Edge cases (empty values, null, boundary values)
3. Error cases (exceptions, insufficient permissions)
4. Repeat RED -> GREEN -> REFACTOR for each case

## Completion Checklist

MUST verify before finishing:
- [ ] All tests pass
- [ ] Coverage is 80%+ for changed code
- [ ] No unnecessary code (YAGNI)
- [ ] Test names are clear enough to serve as documentation
