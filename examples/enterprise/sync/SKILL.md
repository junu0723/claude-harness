---
description: "Synchronizes Google Sheets feature specification with actual development status by cross-referencing GitHub releases, issues, and project documents. Triggers: \"sync\", \"sheet sync\", \"feature spec update\""
---

# Feature Spec Synchronization

## Constraints

- **MUST NOT modify {{READONLY_COLUMNS}} columns** — read-only area
- Only update {{EDITABLE_COLUMNS}} review area
- MUST get user confirmation before updating
- `valueInputOption: USER_ENTERED` mode required when using HYPERLINK formula

## Spreadsheet Info

- **Spreadsheet ID:** `{{SPREADSHEET_ID}}`
- **Sheet tab:** `{{SHEET_TAB}}`
- **GitHub repo:** `{{OWNER}}/{{REPO}}`

<!-- Create your own references/spreadsheet-columns.md for column definitions -->

---

## Procedure

### Step 1: Collect Current State (parallel)

Collect the following simultaneously:

#### 1.1 Read spreadsheet
```bash
gws sheets +read --spreadsheet {{SPREADSHEET_ID}} --range "{{SHEET_TAB}}!A1:P70"
```

#### 1.2 Read project documents
```bash
cat docs/STATUS.md
cat docs/ROADMAP.md
cat docs/HISTORY.md
```

#### 1.3 GitHub releases
```bash
gh release list --limit 10
```

#### 1.4 GitHub P1 issues (all states)
```bash
gh issue list --label "P1" --state all --limit 30
```

#### 1.5 Recently closed GitHub issues
```bash
gh issue list --state closed --limit 20
```

---

### Step 2: Comparison Analysis

Analyze whether {{EDITABLE_COLUMNS}} columns need updates based on collected data.

#### 2.1 Check Items

| Check Item | Description |
|-----------|-------------|
| Status mismatch | Sheet shows "Planned" but feature already released → change to "Done" |
| Status mismatch | Sheet shows "Not started" but Closed issue exists → review as "Done" or "Partial" |
| Overdue | Planned date passed but not completed → warning flag |
| Unreflected release | New release not reflected in sheet → update needed |
| Unreflected issue | Closed issue not reflected in sheet → update needed |
| Missing version link | Version text without HYPERLINK formula → replace with formula |

---

### Step 3: Report Changes

Report analysis results to user:

```markdown
## Sync Results

### Items Needing Update
| Row | Feature | Current Status | Proposed Change | Reason |
|-----|---------|---------------|-----------------|--------|
| 5   | Feature A | Planned | Done | Released in vX.X.X |
| 12  | Feature B | Not started | Done | #320 Closed |

### Schedule Warnings (overdue)
- Row 15: Feature C - Planned date: YYYY-MM-DD, Current status: Planned

### New Items to Reflect
- vX.X.X release → update related feature rows

### No Changes (up to date)
- X items
```

---

### Step 4: User Confirmation

MUST get user confirmation after reporting:

```
Apply these changes to the sheet?
- Apply all: "yes" / "all"
- Apply selected: "update only Row 5, 12"
- Cancel: "no" / "cancel"
```

Wait for user response:
- "yes", "proceed", "all" → go to Step 5 (full update)
- Specific rows specified → update only those rows
- "no", "cancel" → exit skill

---

### Step 5: Update Sheet

After user confirmation, update only {{EDITABLE_COLUMNS}} columns.

#### 5.1 Prepare update data

Compose values for each row as an array:
- Unchanged rows: keep existing values
- Empty cells: `""` (empty string)
- HYPERLINK formula: write in `=HYPERLINK("url","label")` format

#### 5.2 Execute Update

```bash
gws sheets spreadsheets values update \
  --params '{"spreadsheetId":"{{SPREADSHEET_ID}}","range":"{{SHEET_TAB}}!{{EDITABLE_COLUMNS}}5:{{EDITABLE_COLUMNS_END}}70","valueInputOption":"USER_ENTERED"}' \
  --json '{
    "range": "{{SHEET_TAB}}!{{EDITABLE_COLUMNS}}5:{{EDITABLE_COLUMNS_END}}70",
    "majorDimension": "ROWS",
    "values": [
      ["Done", "P1", "=HYPERLINK(\"https://github.com/{{OWNER}}/{{REPO}}/releases/tag/vX.X.X\",\"vX.X.X\")", "YYYY-MM-DD", "", ""],
      ...
    ]
  }'
```

**Notes:**
- `valueInputOption: USER_ENTERED` required (for HYPERLINK formula processing)
- Process data rows only, skip headers
- Include unchanged rows with current values (overwrite entire range)

---

### Step 6: Final Report

Report after update:

```markdown
## Sync Complete

### Updated Items
| Row | Feature | Changes |
|-----|---------|---------|
| 5   | Feature A | Planned → Done, Added version link |
| 12  | Feature B | Not started → Done, Added issue link |

### Schedule Warnings (unresolved)
- Row 15, 22: Needs review (overdue)

Spreadsheet link: https://docs.google.com/spreadsheets/d/{{SPREADSHEET_ID}}/edit
```

---

## Error Handling

### gws command failure
```
gws CLI is not responding. Please check:
- Installation: `which gws`
- Auth status: `gws auth status`
```

### gh command failure
```
GitHub CLI authentication required: `gh auth login`
```

### Sheet data parsing errors
- Exclude header rows from processing
- Skip empty rows (where all columns are empty)

---

## Reference Files

- **Feature spec sheet:** https://docs.google.com/spreadsheets/d/{{SPREADSHEET_ID}}/edit
- **STATUS.md:** `docs/STATUS.md`
- **ROADMAP.md:** `docs/ROADMAP.md`
- **HISTORY.md:** `docs/HISTORY.md`
- **GitHub Releases:** https://github.com/{{OWNER}}/{{REPO}}/releases
- **GitHub Issues:** https://github.com/{{OWNER}}/{{REPO}}/issues
