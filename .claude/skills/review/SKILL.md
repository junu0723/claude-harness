---
description: "Addresses Codex and reviewer comments by analyzing PR feedback, implementing fixes, and verifying resolution. Triggers: \"review\", \"codex\", \"fix review\""
---

# Address Review Comments (review)

## Execution Steps

### 1. Fetch PR comments
```bash
# Check open PRs
gh pr list

# Fetch PR comments
gh pr view <pr-number> --comments

# Review comment details
gh api repos/:owner/:repo/pulls/<pr-number>/comments
```

### 2. Analyze comments
- List items requiring fixes
- Identify priorities
- Review related code

### 3. Implement fixes
**Delegate to developer subagent:**
- Apply fixes per comment
- Verify build after fixing

### 4. Re-commit and push
```bash
git add -A
git commit -m "fix: address review comments

- Fix 1
- Fix 2

🤖 Generated with [Claude Code](https://claude.com/claude-code)

Co-Authored-By: Claude Opus 4.6 (1M context) <noreply@anthropic.com>"

git push
```

### 5. Respond on PR
Post a response comment on the PR (if needed):
```bash
gh pr comment <pr-number> --body "Review comments have been addressed."
```

### 6. Completion verification
MUST: Confirm all review comments have been addressed.
- [ ] Each comment fixed
- [ ] Build succeeds
- [ ] Fix commits pushed

## Common Codex Review Patterns

Typical Codex findings:
- Type safety
- Error handling
- Code duplication
- Naming conventions
- Performance optimization

## Notes
- Do not resolve review comments — only fix them
- Let the reviewer resolve them directly
