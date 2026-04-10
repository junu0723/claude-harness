---
description: "Creates GitHub issues with automated codebase investigation, priority/label assignment, ROADMAP.md placement, and developer agent delegation. Triggers: \"issue\", \"register\", \"improvement\", \"dev point\""
---

# issue

## How It Works

When the user mentions an improvement, bug, or new feature, this skill investigates the codebase, determines the appropriate priority/version, creates a GitHub issue, and registers it on the ROADMAP.

---

## Execution Steps

### Step 1: Autonomous Codebase Investigation

Use the Explore agent to investigate relevant code:

**Investigation scope:**
- Current implementation status (implemented / not implemented / partially implemented)
- Related file paths and components
- Impact area (frontend / backend / infrastructure / packages)
- Related domains ({{YOUR_DOMAINS}})
- Potential conflicts with existing implementations

**Duplicate check:**
```bash
gh issue list --search "keyword" --json number,title,labels --limit 20
```

If an existing issue is found, notify the user and ask whether to continue.

---

### Step 2: Automated Judgment Based on Analysis

Based on the investigation, automatically determine priority, category, type, version, and effort.

<!-- Create your own references/issue-labels.md for detailed label definitions -->
<!-- Create your own references/roadmap-structure.md for ROADMAP placement rules -->

**MUST check the current ROADMAP.md structure!** (read `docs/ROADMAP.md`)

---

### Step 3: User Confirmation (concise)

Show a **concise** summary of the judgment:

```markdown
## Issue Creation Plan

**Title:** [P2][vX.X.X] Feature title
**Priority:** P2 (general improvement)
**Labels:** frontend, enhancement
**ROADMAP placement:** vX.X.X - Next version
**Estimated effort:** M (medium)

**Tasks:**
- [ ] Task item 1
- [ ] Task item 2

**Related files:**
- path/to/file1
- path/to/file2

Proceed as is? (Let me know if you have any feedback)
```

Wait for user response:
- "yes", "proceed", "looks good" → Step 4
- Modification request → Adjust and confirm again
- "no", "cancel" → Exit skill

---

### Step 4: Create GitHub Issue

**Title format:**
- Upcoming version: `[PX][vX.X.X] Feature title`
- Backlog: `[PX] Feature title`

**Body format:**
```markdown
## Description
[Specific description, background, and purpose]

## Tasks
- [ ] Task item 1
- [ ] Task item 2
- [ ] Task item 3

## Related Files
- path/to/file1
- path/to/file2
- path/to/file3

## Estimated Effort
S / M / L

## Related Version
vX.X.X - Version description
```

**GitHub CLI command:**
```bash
gh issue create \
  --title "[P2][vX.X.X] Feature title" \
  --body "$(cat <<'EOF'
## Description
Specific description of the feature

## Tasks
- [ ] Task item 1
- [ ] Task item 2

## Related Files
- path/to/file1

## Estimated Effort
M (medium)

## Related Version
vX.X.X - Next release
EOF
)" \
  --label "P2,frontend,enhancement"
```

**Important rules:**
- Use HEREDOC to preserve line breaks
- Labels separated by commas, no spaces
- Save issue number (used in Step 5)

---

### Step 5: Update ROADMAP.md

**Important: The main session is an orchestrator!**
- ROADMAP.md edits MUST be delegated to a developer agent
- The main session does not use Edit/Write tools

#### 5.1 Invoke developer agent

**Place in upcoming version:**
```markdown
### vX.X.X - Description <- Next
**Tasks:**
- [ ] #existing-issue-1 Title
- [ ] #new-issue-number New feature title  <- Add here
```

**Place in Backlog:**
```markdown
## Backlog
### Category
- #existing-issue-1 Title
- #new-issue-number New feature title  <- Add here
```

#### 5.2 Instructions to pass to developer

```
Please update the docs/ROADMAP.md file.

**Location to add:** [vX.X.X task list / Backlog > Category]
**Content to add:** - [ ] #issue-number Feature title

Exact format:
- Upcoming version: "- [ ] #number title" (with checkbox)
- Backlog: "- #number title" (no checkbox)

Preserve the order of existing items and append the new item to the end of the list.
```

---

### Step 6: Final Report

After all tasks are complete, report to the user:

```markdown
## Issue Created

**GitHub Issue:** https://github.com/{{OWNER}}/{{REPO}}/issues/XXX
**Title:** [P2][vX.X.X] Feature title
**Priority:** P2
**Labels:** frontend, enhancement

**ROADMAP placement:** Added to vX.X.X - Next release task list

**Next steps:**
- Review the issue and discuss further if needed
- Create a branch when development starts: `feat/issue-XXX-description`
- Include `Closes #XXX` when creating a PR after completing work
```

---

## Exception Handling

### Duplicate issue found
- Notify the user and give them options:
  - "Existing issue #XX is similar. Do you still want to create a new one?"
  - Provide link to the existing issue

### Version not found in ROADMAP.md
- Confirm with the user:
  - "Version vX.X.X is not in the ROADMAP. Should I add it to the Backlog?"

### GitHub CLI permission error
- Guide the user:
  - "Please authenticate with GitHub first using `gh auth login`."

### developer agent failure
- Manual guidance:
  - "ROADMAP.md update failed. Please manually add the following: ..."

---

## Reference Files

- **GitHub issue list:** `gh issue list --json number,title,labels`
- **ROADMAP:** `docs/ROADMAP.md`
- **Domain list:** {{YOUR_DOMAINS}}
