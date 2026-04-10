---
description: "Executes comprehensive QA: parallel unit tests, E2E tests, Codex AI review, and browser testing with consolidated quality report. Triggers: \"qa\", \"test\", \"code review\", \"quality check\""
---

# Comprehensive QA (qa)

## Execution Steps

### 1. Review changes
```bash
git diff --stat HEAD~1
git log --oneline -5
```

### 2. Run parallel tests

**MUST run in parallel** — each is independent, so run simultaneously.

#### 2-1. Backend Unit Test (Bash background)
```bash
cd {{BACKEND_PATH}}
{{BACKEND_TEST_COMMAND}} 2>&1 | tee /tmp/{{PROJECT_NAME}}-backend-test.txt
```
- Success criteria: `BUILD SUCCESSFUL`
- On failure: collect list of failed tests and error messages

#### 2-2. Frontend Unit Test (Bash background)
```bash
cd {{FRONTEND_PATH}}
{{FRONTEND_TEST_COMMAND}} 2>&1 | tee /tmp/{{PROJECT_NAME}}-frontend-test.txt
```
- Success criteria: all tests pass
- Coverage threshold: 40% (statements, branches, functions, lines)

#### 2-3. Frontend E2E Test (Bash background)
> All services must be running for E2E tests.
> If services are not running, notify the user and guide them to start services using the `status` skill.

```bash
cd {{FRONTEND_PATH}}
{{E2E_TEST_COMMAND}} 2>&1 | tee /tmp/{{PROJECT_NAME}}-e2e-test.txt
```
- Success criteria: all specs pass
- On failure: collect screenshot paths (`test-results/`)

#### 2-4. Codex AI Review (Bash background)
```bash
cd {{PROJECT_ROOT}}
codex review --uncommitted 2>&1 | tee /tmp/{{PROJECT_NAME}}-codex-review.txt
```
If changes are already committed:
```bash
codex review --base main~1 2>&1 | tee /tmp/{{PROJECT_NAME}}-codex-review.txt
```
Or against a specific branch:
```bash
codex review --base main 2>&1 | tee /tmp/{{PROJECT_NAME}}-codex-review.txt
```

#### 2-5. Core Scenario Verification in Browser (browser-tester agent)
> The full stack must be running.

Use the browser-tester agent to verify **core user scenarios** in an actual browser.

**Default verification scenarios:**
1. Login → verify home screen displays correctly
2. Actually click/input 1-3 core features from the current changes to confirm behavior
3. Check for error console messages (`read_console_messages`)
4. Capture screenshots to verify no visual anomalies

**Additional scenarios by change type:**
- UI changes: capture screenshots of changed screens and verify layout
- API changes: trigger the relevant feature via UI interaction and verify network requests
- Auth/security changes: verify the full login/logout flow

```
Use Agent tool (subagent_type=browser-tester)
- If services are not running, skip and note "Browser test skipped (services not running)" in the report
- Recommend recording a GIF of the test process
```

### 3. Collect and analyze results

After all tests complete, read and analyze result files:
```bash
cat /tmp/{{PROJECT_NAME}}-backend-test.txt
cat /tmp/{{PROJECT_NAME}}-frontend-test.txt
cat /tmp/{{PROJECT_NAME}}-e2e-test.txt
cat /tmp/{{PROJECT_NAME}}-codex-review.txt
```

### 4. Write comprehensive QA report

Report to user in the following format:

```markdown
## Comprehensive QA Report

**Scope:** <commit range or change description>
**Date:** YYYY-MM-DD HH:MM

### Test Results Summary

| Area | Result | Details |
|------|--------|---------|
| Backend Unit Test | ✅/❌ | N passed, N failed |
| Frontend Unit Test | ✅/❌ | N passed, N failed |
| E2E Test | ✅/❌ | N passed, N failed |
| Browser Test | ✅/❌/⏭️ | N core scenarios verified |
| Codex Review | ⚠️/✅ | N issues found |

### Failure Details (if any)

#### Backend
- `TestClassName#methodName`: error message

#### Frontend
- `test-file.test.ts > test name`: error message

#### E2E
- `spec-file.spec.ts > test name`: error + screenshot path

### Codex Review Summary
- **High severity:** items
- **Improvements recommended:** items
- **Style/convention:** items

### Recommended Actions
1. [P1] Items requiring immediate fix
2. [P2] Items recommended for improvement
3. [P3] Notes for reference
```

### 5. When fixes are needed

If the user requests fixes, delegate to `developer` subagent:
- Fix test failures
- Apply Codex review findings

After fixes, re-run only the relevant tests to verify.

## Execution Options

Users may request partial execution:
- `/qa backend` — Backend tests only
- `/qa frontend` — Frontend Unit + E2E
- `/qa e2e` — E2E only
- `/qa browser` — Browser tests only
- `/qa review` — Codex review only
- `/qa all` — All (default)

## Prerequisites

| Test | Required Condition |
|------|--------------------|
| Backend Unit | Required infrastructure services running |
| Frontend Unit | Dependencies installed |
| E2E | Full stack running |
| Codex Review | `codex` CLI installed + authenticated |

## Notes

- E2E tests: run with `& disown`, then verify with `tail -f`
- Watch for DB conflicts with dev server during backend tests
- Codex review requires network access (API call)
- All test logs saved to `/tmp/{{PROJECT_NAME}}-*.txt`
