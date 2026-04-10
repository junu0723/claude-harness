---
description: "Creates version releases with PR merge safety validation, 4-document synchronization (STATUS/ROADMAP/HISTORY/README), git tagging, and GitHub release notes. Triggers: \"release\", \"publish version\""
---

# Release (release)

## Execution Steps

### 0. Update version files
Update the `version` field in the project's version files (`{{VERSION_FILES}}`) to match the release version.
(e.g., `package.json`, `build.gradle`, `pyproject.toml`, or whatever version files the project uses)

### 1. PR Merge

> **Merge Safety Rule**: MUST verify all conditions below before merging a PR.
> **If either condition is not met, do NOT merge and notify the user.**
> 1. Is the PR author `{{REPO_OWNER}}`?
> 2. Is there an approve review from `{{REPO_OWNER}}`?

```bash
# Check PR status
gh pr view <pr-number>

# Required validation before merge
PR_AUTHOR=$(gh pr view <pr-number> --json author --jq '.author.login')
APPROVED=$(gh pr view <pr-number> --json reviews --jq '[.reviews[] | select(.state == "APPROVED" and .author.login == "{{REPO_OWNER}}")] | length')

if [ "$PR_AUTHOR" != "{{REPO_OWNER}}" ] && [ "$APPROVED" -eq 0 ]; then
  echo "MERGE BLOCKED: PR author is not {{REPO_OWNER}} and there is no approve from {{REPO_OWNER}}."
  echo "   PR Author: $PR_AUTHOR"
  echo "   {{REPO_OWNER}} Approved: No"
  exit 1
fi

# Merge after validation passes (squash)
gh pr merge <pr-number> --squash --delete-branch
```

### 2. Update documents

> **Core principle**: Each document contains only the minimum content for its role. Everything else is referenced via links.

**4 document roles:**

| Document | Role | Must Include | NOT Allowed |
|----------|------|--------------|-------------|
| STATUS.md | Current state only | Current version, health, open issues, 1 latest change | Closed Issues section, past changes |
| ROADMAP.md | Future plans only | Upcoming + Backlog + Long-term | Completed release details (links only allowed) |
| HISTORY.md | Past releases | Full release history (no limit) | - |
| README.md | Project overview | Version badge | Release notes, change details |

---

#### A. Update STATUS.md

**1) Header 3 lines:**
```markdown
**Last Updated:** YYYY-MM-DD
**Status:** vX.Y.Z RELEASED — Next: vX.Y+1.0
**Current Version:** vX.Y.Z
```

**2) Version Plan table (max 5 rows):**
- 1 latest completed + 1 Next + 3 Future
- Remove old completed entries → link to "See HISTORY.md for previous releases"
```markdown
## Version Plan

> See [HISTORY.md](./HISTORY.md) for previous releases

| Version | Focus | Status |
|---------|-------|--------|
| **vX.Y.Z** | Current release title | ✅ Done |
| **vX.Y+1.0** | Next version title | ⬅️ Next |
| **vX.Y+2.0** | Future title | Future |
| **vX.Y+3.0** | Future title | Future |
| **vX.Y+4.0** | Future title | Future |
```

**3) Delete Closed Issues section:**
- Remove all patterns matching `### v0.X.Y Issues ... ✅ Closed`
- Only update the numbers in the Open Issues table

**4) Recent Changes:**
- Remove previous version content, keep only the current release

**5) Next Up:**
- Replace with next version

---

#### B. Update ROADMAP.md

**1) Header 3 lines:**
```markdown
**Last Updated:** YYYY-MM-DD
**Current Version:** vX.Y.Z (Released)
**Next Version:** vX.Y+1.0 (title)
```

**2) Completed Releases section — links only:**
```markdown
## Completed Releases

> See [HISTORY.md](./HISTORY.md) for completed release history
```
- MUST NOT add `###` subsections under this section
- Completed version details (task lists, checkboxes, etc.) exist only in HISTORY.md

**3) Upcoming Releases:**
- Remove the `###` section for the released version
- Move the `⬅️ Next` marker to the next version

---

#### C. Update HISTORY.md

**1) Release Timeline (code block at top):**
```
vX.Y.Z (MM-DD): Release title ⬅️ Latest
vX.Y-1 (MM-DD): Previous release title
...
```

**2) Add version section (just below Timeline, above existing sections):**
```markdown
## vX.Y.Z (YYYY-MM-DD) - PR #NNN

Release title - one line summary

### Changes
| Change | Details |
|--------|---------|
| Item 1 | Specific change |
| Item 2 | Specific change |

---
```

**3) Summary of completed items moved from ROADMAP:**
- Convert the task list (checkboxes) from ROADMAP's Upcoming section into a summary table

---

#### D. Update README.md (if needed)

Update version badge:
```markdown
![Version](https://img.shields.io/badge/version-vX.Y.Z-blue.svg)
```

---

### Document Update Checklist

MUST verify before release:

- [ ] STATUS.md: header 3 lines, Version Plan ≤5 rows, no Closed Issues, only 1 Recent Change
- [ ] ROADMAP.md: header 3 lines, Completed Releases has no content (links only), completed version removed
- [ ] HISTORY.md: Timeline updated, new version section added (with PR number)
- [ ] README.md: Version badge (if needed)

### 3. Create Git Tag
```bash
git pull origin main
git tag -a vX.Y.Z -m "vX.Y.Z Release"
git push origin vX.Y.Z
```

### 4. Create GitHub Release
```bash
gh release create vX.Y.Z --title "vX.Y.Z" --notes "$(cat <<'EOF'
## What's Changed

### Features
- Feature 1 (#issue-number)

### Bug Fixes
- Bug fix (#issue-number)

### Improvements
- Improvement (#issue-number)

**Full Changelog**: https://github.com/{{OWNER}}/{{REPO}}/compare/vX.Y-1.Z...vX.Y.Z
EOF
)"
```

### 5. Close related issues
```bash
# Manually close issues not linked via Closes # in PR
gh issue close <issue-number>
```

## Notes
- Link issues in PR description using `Closes #issue-number` format
- Manually close any unlinked issues
- MUST record detailed changes in HISTORY.md
- MUST pass the full document update checklist
