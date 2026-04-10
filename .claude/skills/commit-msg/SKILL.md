---
description: "Generates conventional commit messages by analyzing staged changes. Follows Angular/Conventional Commits spec with scope, breaking changes, and co-author attribution. Triggers: \"commit\", \"commit message\""
---

# Commit Message Generator

Automatically generates commit messages conforming to the Conventional Commits specification.

## Execution Steps

### Step 1: Analyze Changes

```bash
git diff --cached --stat
git diff --cached
```

### Step 2: Determine Commit Type

| Type | Purpose | Examples |
|------|---------|---------|
| `feat` | New feature | New API endpoint, UI component |
| `fix` | Bug fix | Error resolution, logic correction |
| `refactor` | Refactoring | Code improvement without behavior change |
| `test` | Tests | Add/modify tests |
| `docs` | Documentation | README, comments, API docs |
| `chore` | Build/config | Dependencies, CI, config files |
| `style` | Formatting | Whitespace, semicolons, lint fixes |
| `perf` | Performance | Optimization, caching |

### Step 3: Compose Message

**Format:**
```
<type>(<scope>): <subject>

<body>

<footer>
```

**Rules:**
- subject: 50 characters or less, starts lowercase, no trailing period
- scope: area of change (optional) — e.g., `auth`, `api`, `ui`
- body: focus on "why", "what" is visible in the diff
- footer: `Closes #123`, `BREAKING CHANGE:`, etc.
- 120 character maximum line length

### Step 4: Execute Commit

```bash
git commit -m "$(cat <<'EOF'
type(scope): subject

body explaining why this change was made.

Closes #123

Co-Authored-By: Claude Opus 4.6 (1M context) <noreply@anthropic.com>
EOF
)"
```

## MUST Rules

- [ ] Follow Conventional Commits format
- [ ] subject in English (follow project convention)
- [ ] BREAKING CHANGE specified in footer
- [ ] Related issue number included
- [ ] Co-Authored-By included
