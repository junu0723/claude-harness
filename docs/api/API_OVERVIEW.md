# API Overview

> Reference for backend API endpoints.
> API docs: {{API_DOCS_URL}}

## Authentication

<!-- Describe auth mechanism: JWT, OAuth, session-based -->

## Base URL

- Development: {{BACKEND_URL}}
- Staging: {{DEPLOY_URL}}

## Conventions

- RESTful resource naming
- Response format: `{ "data": ..., "error": ... }`
- Pagination: `?page=0&size=20`
- Error codes: HTTP standard + custom domain codes

## Endpoint Groups

<!-- List your API groups -->
<!-- Example:
| Group | Base Path | Description |
|-------|-----------|-------------|
| Auth | `/api/v1/auth` | Login, logout, token refresh |
| Users | `/api/v1/users` | User CRUD, profile |
| Projects | `/api/v1/projects` | Project management |
-->
