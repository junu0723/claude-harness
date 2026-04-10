# CLAUDE.md

> **Main Session Rule**: The main session is the **orchestrator**. Do NOT edit code directly. Delegate implementation to `developer` subagent, reviews to `code-reviewer` subagent. Main session only: explore (Read/Grep/Glob), plan, manage subagents, communicate with user.

Claude Code Development Guide.

> **Project:** [README.md](./README.md) | **Status:** [STATUS.md](./docs/STATUS.md) | **Roadmap:** [ROADMAP.md](./docs/ROADMAP.md)

## Development Commands

### Backend
```bash
cd {{BACKEND_PATH}}
{{BACKEND_BUILD}}           # Build (skip tests)
{{BACKEND_TEST}}            # Run tests
```

### Frontend
```bash
cd {{FRONTEND_PATH}}
{{FRONTEND_INSTALL}}        # Install dependencies
{{FRONTEND_DEV}}            # Dev server
{{FRONTEND_BUILD}}          # Production build
```

### Infrastructure
```bash
{{INFRA_START}}             # Start all services
{{INFRA_STOP}}              # Stop services
```

### Service URLs
- Frontend: {{FRONTEND_URL}}
- Backend API: {{BACKEND_URL}}
- API Docs: {{API_DOCS_URL}}

## Important Rules

### Parallel Development
- MUST NOT run full test suite in dev session (shared DB)
- MUST NOT start dev server in dev session (port conflict)
- Unit tests only in subagents
- Frontend: build only

### Type Safety
- Backend DTO and Frontend types MUST match exactly
- Check `{{BACKEND_PATH}}/dto/` before writing API clients

### Commits
- Conventional Commits: `feat:`, `fix:`, `docs:`, `test:`, `refactor:`, `chore:`
- 120 char max line length

## DB Migration
**Location:** `{{MIGRATION_PATH}}` | **Naming:** `V{number}__{description}.sql`
