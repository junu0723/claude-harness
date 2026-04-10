# Database Schema

> Update this document when migrations are added.
> Migration files: `{{MIGRATION_PATH}}`

## Entity Relationship Overview

<!-- Describe key entities and their relationships -->

## Key Tables

<!-- List important tables with brief descriptions -->
<!-- Example:
| Table | Purpose |
|-------|---------|
| users | User accounts and profiles |
| projects | Project definitions and settings |
| tasks | Task items within projects |
-->

## Conventions

- Primary keys: `BIGINT AUTO_INCREMENT`
- Timestamps: `created_at`, `updated_at` (UTC)
- Soft delete: `deleted_at` nullable timestamp
- Naming: `snake_case` for tables and columns
