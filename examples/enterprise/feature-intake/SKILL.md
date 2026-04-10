---
description: "Automates feature request intake from Google Sheets or direct input: AI analysis for product fit, priority, effort estimation, then batch-creates tasks, GitHub issues, ROADMAP entries, and sheet updates upon approval. Triggers: \"feature-intake\", \"intake\""
---

# Feature Intake

## Constraints

- MUST NOT create tasks/issues without user approval
- MUST reference `{{PRODUCT_PHILOSOPHY_DOC}}` during analysis
- Confirm target server when using {{PROJECT_CLI}} CLI
- MUST NOT modify {{READONLY_COLUMNS}} columns (read-only area)
- Main session role: orchestrator — delegate code edits to developer agent

## Reference Info

- **Spreadsheet ID:** `{{SPREADSHEET_ID}}`
- **Sheet tab:** `{{SHEET_TAB}}` (request sheet), `internal proposals`
- **GitHub repo:** `{{OWNER}}/{{REPO}}`
- **Staging server:** `{{STAGING_SERVER}}`
- **Project CLI:** `{{PROJECT_CLI}}`

<!-- Create your own references/feature-analysis-rubric.md for analysis criteria -->

---

## Procedure

### Step 1: Collect Input

Confirm input method with user:

```
Where should feature requests come from?
A) Sheet batch — unprocessed items in '{{SHEET_TAB}}' tab
B) Sheet selection — select from 'internal proposals' tab
C) Direct input — pass feature requests as text
```

#### A) Sheet Batch
```bash
gws sheets +read --spreadsheet {{SPREADSHEET_ID}} --range "{{SHEET_TAB}}!A1:P70"
```
- Collect unprocessed items (rows where relevant columns are empty) as analysis targets

#### B) Internal Proposals Tab
```bash
gws sheets +read --spreadsheet {{SPREADSHEET_ID}} --range "internal proposals!A1:Z50"
```
- Show list of not-started items and let user select

#### C) Direct Input
- User passes feature requests as text
- Multiple items at once

---

### Step 2: AI Analysis

For each item, perform the following:

#### 2.1 Reference product philosophy
```bash
cat {{PRODUCT_PHILOSOPHY_DOC}}
```

#### 2.2 Codebase exploration (Explore agent)
- Check related domains/existing implementations
- Check if feature already exists
- Determine: extend existing vs. new feature

#### 2.3 Check duplicate issues
```bash
gh issue list --search "keyword" --json number,title,labels,state --limit 20
```

#### 2.4 Cross-reference current status
```bash
cat docs/ROADMAP.md
cat docs/STATUS.md
```

---

### Step 3: Analysis Report → User Approval

Report analysis results in table format:

```markdown
## Feature Request Analysis

### Item 1: [Feature Name]
- **Original request:** (original text)
- **Product fit:** Fit ✅ — can be naturally integrated into existing domain
- **Existing impl:** Extend — need to add fields to existing service
- **Dev spec:** Specific development details
- **Priority:** P2 — high request frequency but not urgent
- **Effort:** M (3-5 days)
- **Target version:** vX.X.X
- **Labels:** frontend, backend, enhancement

### Item 2: [Feature Name]
- **Product fit:** Not fit ❌ — unrelated to core project values
- **Reason:** ...

---

Decide how to handle each item:
- ✅ Approve: "approve 1, 3" or "approve all"
- ✏️ Modify: "change item 2 priority to P1"
- ⏸️ Hold: "hold item 4"
- ❌ Reject: "reject item 2"
```

Wait for user response → apply modifications → final confirmation

---

### Step 4: Batch Process Approved Items

For each approved item, execute in order:

#### 4.1 Create GitHub issue (follow issue skill pattern)
```bash
gh issue create \
  --title "[P2][vX.X.X] Feature title" \
  --body "$(cat <<'EOF'
## Description
Feature description based on request

**Original request:** (original text)

## AI Analysis
- Domain: relevant domain
- Existing implementation: Extend / New
- Product fit: Fit — reason

## Tasks
- [ ] Task item 1
- [ ] Task item 2
- [ ] Task item 3

## Related files
- path/to/file1
- path/to/file2

## Estimated effort
M (3-5 days)

## Source
Feature spec sheet Row XX — Created by AI Feature Intake
EOF
)" \
  --label "P2,frontend,backend,enhancement"
```

#### 4.2 Update ROADMAP.md (delegate to developer agent)
```
To developer agent:
Add to [vX.X.X section / appropriate Backlog category] in docs/ROADMAP.md:
- [ ] #issue-number Feature title
```

#### 4.3 Update sheet (sync skill pattern)
Update related columns of previously unprocessed rows:
```bash
gws sheets spreadsheets values update \
  --params '{"spreadsheetId":"{{SPREADSHEET_ID}}","range":"{{SHEET_TAB}}!{{EDITABLE_COLUMNS}}{row}","valueInputOption":"USER_ENTERED"}' \
  --json '{
    "range": "{{SHEET_TAB}}!{{EDITABLE_COLUMNS}}{row}",
    "majorDimension": "ROWS",
    "values": [
      ["Planned", "P2", "=HYPERLINK(\"https://github.com/{{OWNER}}/{{REPO}}/issues/XXX\",\"#XXX\")", "", "vX.X.X", "AI Intake: Request received"]
    ]
  }'
```

---

### Step 5: Final Report

```markdown
## Feature Intake Complete

### Results
| # | Feature | Decision | GitHub Issue | ROADMAP | Updated |
|---|---------|----------|--------------|---------|---------|
| 1 | Feature A | ✅ Approve | #460 | vX.X.X | Row 15 |
| 2 | Feature B | ❌ Reject | — | — | — |
| 3 | Feature C | ✅ Approve | #461 | vX.X.X | Row 18 |
| 4 | Feature D | ⏸️ Hold | — | — | — |

### Links
- Staging server: {{STAGING_SERVER}}
- GitHub issues: https://github.com/{{OWNER}}/{{REPO}}/issues
- Feature spec sheet: https://docs.google.com/spreadsheets/d/{{SPREADSHEET_ID}}/edit
```

---

## Error Handling

### {{PROJECT_CLI}} CLI not installed
```
{{PROJECT_CLI}} CLI is not installed. Please refer to your project's CLI installation guide.
```

### gws CLI not installed / not authenticated
```
Check Google Workspace CLI: which gws / gws auth status
```

### Sheet data parsing errors
- Exclude header rows from data processing
- Skip rows where all key columns are empty

---

## Reference Files

| Purpose | Path / Command |
|---------|----------------|
| Product philosophy | `{{PRODUCT_PHILOSOPHY_DOC}}` |
| Sheet sync pattern | `.claude/skills/sync/SKILL.md` |
| Issue creation pattern | `.claude/skills/issue/SKILL.md` |
| ROADMAP | `docs/ROADMAP.md` |
| STATUS | `docs/STATUS.md` |
