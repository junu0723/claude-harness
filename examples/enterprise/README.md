# Enterprise Examples

These skills are designed for enterprise workflows with external system integration. They're not part of the core SDLC pipeline but demonstrate how to extend the harness for specific use cases.

## sync

Synchronizes Google Sheets feature specifications with development status. Useful when business teams maintain feature specs in spreadsheets and the development team needs to keep them updated.

**Use when:** Your organization tracks feature definitions in Google Sheets and needs automated status sync with GitHub issues/releases.

## feature-intake

Automates the feature request pipeline: receives requests from external sources (Google Sheets, direct input), runs AI analysis for product fit and priority, then batch-creates GitHub issues + ROADMAP entries.

**Use when:** You have a formal feature request process where business teams submit requests that need technical analysis before becoming development items.

## How to use

Copy the desired skill into your project's `.claude/skills/` directory and customize the `{{PLACEHOLDER}}` values.
