---
description: "Creates GitHub pull requests with conventional commit analysis, structured description, and test plan. Triggers: \"pr\", \"pull request\""
---

# Create PR (pr)

## Execution Steps

### 1. Review changes
```bash
git status
git diff --stat
git log main..HEAD --oneline
```

### 2. Create PR
```bash
gh pr create --title "title" --body "$(cat <<'EOF'
## Summary
- Change 1
- Change 2

## Related Issues
- Closes #123
- Closes #124

## Test Plan
- [ ] Test item 1
- [ ] Test item 2

🤖 Generated with [Claude Code](https://claude.com/claude-code)
EOF
)"
```

### 3. Share PR URL
Deliver the created PR URL to the user

### 4. Verify PR
```bash
# Check created PR
gh pr view --json url,title,state
```
MUST: Verify that the PR URL was returned. On failure, report the error message to the user.

## Notes
- Follow the project's branching strategy
