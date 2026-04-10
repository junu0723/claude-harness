# Architecture

> This document describes the high-level architecture of the project.
> CLAUDE.md references this for structural context. Update when major architectural decisions are made.

## Overview

<!-- Describe your system's high-level architecture here -->
<!-- Example: Monorepo with backend ({{BACKEND_FRAMEWORK}}) + frontend ({{FRONTEND_FRAMEWORK}}) -->

## Domain Structure

<!-- List your project's domains/modules -->
<!-- Example:
- user — Authentication, profiles, preferences
- project — Project CRUD, membership, settings
- task — Task management, assignments, status tracking
-->

## Technology Stack

| Layer | Technology |
|-------|-----------|
| Backend | {{BACKEND_FRAMEWORK}} |
| Frontend | {{FRONTEND_FRAMEWORK}} |
| Database | <!-- e.g., PostgreSQL, MySQL --> |
| Cache | <!-- e.g., Redis --> |
| Auth | {{AUTH_SYSTEM}} |
| File Storage | {{FILE_STORAGE}} |

## Key Patterns

<!-- Document your codebase's recurring patterns -->
<!-- Example:
- Controller → Service → Repository layering
- Soft delete with `deletedAt` timestamp
- Audit logging via `@Auditable` annotation
-->

## Infrastructure

<!-- Describe deployment topology, service dependencies -->
