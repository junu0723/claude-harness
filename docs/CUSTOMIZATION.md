# Customization Guide

## Step 1: Replace Placeholders

All template files use `{{PLACEHOLDER}}` markers. Find and replace these with your project specifics:

### Project Structure
| Placeholder | Description | Example |
|-------------|-------------|---------|
| `{{PROJECT_NAME}}` | Your project name | `my-app` |
| `{{PROJECT_ROOT}}` | Absolute path to project | `/Users/you/dev/my-app` |
| `{{OWNER}}` | GitHub username/org | `your-org` |
| `{{REPO}}` | GitHub repo name | `my-app` |

### Backend
| Placeholder | Description | Example |
|-------------|-------------|---------|
| `{{BACKEND_PATH}}` | Backend directory | `backend` or `server` or `api` |
| `{{BACKEND_BUILD}}` | Build command | `./gradlew build -x test` or `go build ./...` |
| `{{BACKEND_TEST}}` | Test command | `./gradlew test` or `go test ./...` |
| `{{BACKEND_START}}` | Start command | `./gradlew bootRun` or `go run main.go` |
| `{{BACKEND_PORT}}` | Backend port | `8080` |
| `{{BACKEND_URL}}` | Backend URL | `http://localhost:8080` |
| `{{BACKEND_FRAMEWORK}}` | Framework name | `Spring Boot` or `Express` or `Gin` |

### Frontend
| Placeholder | Description | Example |
|-------------|-------------|---------|
| `{{FRONTEND_PATH}}` | Frontend directory | `frontend` or `apps/web` |
| `{{FRONTEND_BUILD}}` | Build command | `pnpm build` or `npm run build` |
| `{{FRONTEND_TEST}}` | Test command | `pnpm test` or `npm test` |
| `{{FRONTEND_DEV}}` | Dev server | `pnpm dev` or `npm run dev` |
| `{{FRONTEND_PORT}}` | Dev server port | `5173` or `3000` |
| `{{FRONTEND_URL}}` | Frontend URL | `http://localhost:5173` |
| `{{FRONTEND_FRAMEWORK}}` | Framework name | `React` or `Vue` or `Svelte` |
| `{{FRONTEND_INSTALL}}` | Install command | `pnpm install` or `npm install` |

### Infrastructure
| Placeholder | Description | Example |
|-------------|-------------|---------|
| `{{INFRA_START}}` | Start infra | `./scripts/start-dev.sh` or `docker-compose up -d` |
| `{{INFRA_STOP}}` | Stop infra | `docker-compose down` |
| `{{AUTH_SYSTEM}}` | Auth provider | `Keycloak` or `Auth0` or `Firebase Auth` |
| `{{FILE_STORAGE}}` | File storage | `MinIO` or `S3` or `local` |
| `{{CACHE_SYSTEM}}` | Cache system | `Redis` or `Memcached` |

### Deployment
| Placeholder | Description | Example |
|-------------|-------------|---------|
| `{{DEPLOY_HOST}}` | Deploy target | `192.168.1.100` or `app.example.com` |
| `{{DEPLOY_URL}}` | Deploy URL | `https://staging.example.com` |
| `{{DEPLOY_USER}}` | SSH user | `deploy` |
| `{{DEPLOY_PASS_ENV}}` | Password env var | `DEPLOY_PASS` |
| `{{DEPLOY_SCRIPT}}` | Deploy script | `./scripts/deploy.sh` |
| `{{HEALTH_ENDPOINT}}` | Health check URL | `/actuator/health` or `/api/health` |
| `{{BACKEND_CONTAINER}}` | Docker container | `my-app-backend` |

### Testing
| Placeholder | Description | Example |
|-------------|-------------|---------|
| `{{E2E_TEST_COMMAND}}` | E2E test runner | `npx playwright test` or `npx cypress run` |
| `{{TEST_USER}}` | Test login user | `test@example.com` |
| `{{TEST_PASS}}` | Test login pass | `testpass123` |
| `{{CODE_REVIEW_COMMAND}}` | AI review tool | `codex review` or skip |

### Documents
| Placeholder | Description | Example |
|-------------|-------------|---------|
| `{{ARCHITECTURE_DOC}}` | Architecture doc path | `docs/ARCHITECTURE.md` |
| `{{MIGRATION_PATH}}` | DB migration dir | `src/main/resources/db/migration/` |
| `{{VERSION_FILES}}` | Files with version | `package.json, build.gradle` |
| `{{YOUR_DOMAINS}}` | Project domains | `user, product, order, payment...` |
| `{{API_DOCS_URL}}` | API documentation | `http://localhost:8080/swagger-ui.html` |

### Codebase
| Placeholder | Description | Example |
|-------------|-------------|---------|
| `{{PROJECT_VERSION}}` | Current version tag | `v0.1.0+` or `v1.2.0+` |
| `{{TEST_COUNT}}` | Approximate test count | `200+` or `500+` |
| `{{PROJECT_STRUCTURE}}` | Short description of repo layout | `Monorepo: Spring Boot backend + React frontend` |

## Step 2: Add Domain List to architect.md

Open `.claude/agents/architect.md` and replace the comment:

```markdown
<!-- List your project's domains here -->
```

With your actual domain list, e.g.:

```markdown
- 8 domains: user, product, order, payment, notification, review, search, admin
```

## Step 3: Add PostToolUse Linting Hook

Add to `.claude/settings.local.json` under `hooks.PostToolUse`:

**For ESLint (JS/TS):**
```json
{
  "matcher": "Write|Edit",
  "hooks": [{
    "type": "command",
    "command": "file=\"$TOOL_INPUT_FILE_PATH\"; case \"$file\" in *.ts|*.tsx|*.js|*.jsx) cd {{FRONTEND_PATH}} && npx eslint --quiet \"$file\" 2>/dev/null;; esac; exit 0"
  }]
}
```

**For Ruff (Python):**
```json
{
  "matcher": "Write|Edit",
  "hooks": [{
    "type": "command",
    "command": "file=\"$TOOL_INPUT_FILE_PATH\"; case \"$file\" in *.py) ruff check \"$file\" 2>/dev/null;; esac; exit 0"
  }]
}
```

**For golangci-lint (Go):**
```json
{
  "matcher": "Write|Edit",
  "hooks": [{
    "type": "command",
    "command": "file=\"$TOOL_INPUT_FILE_PATH\"; case \"$file\" in *.go) golangci-lint run \"$file\" 2>/dev/null;; esac; exit 0"
  }]
}
```

## Step 4: Create Reference Documents (Optional)

For skills that use `references/` directories:

### issue/references/issue-labels.md
Define your project's priority levels, category labels, and size estimates.

## Step 5: Initialize Your Document Memory

1. Edit `docs/STATUS.md` with your current project state
2. Edit `docs/ROADMAP.md` with your planned versions
3. Leave `docs/HISTORY.md` empty — it fills automatically via the `release` skill

---

## Enterprise Extensions Placeholders

These placeholders are used by the enterprise skills in `examples/enterprise/`. They are not needed for the core SDLC pipeline.

### Google Sheets Integration (sync, feature-intake)
| Placeholder | Description | Example |
|-------------|-------------|---------|
| `{{SPREADSHEET_ID}}` | Google Sheets document ID | `1BxiMVs0XRA5nFMdKvBdBZjgmUUqptlbs74OgVE2upms` |
| `{{SHEET_TAB}}` | Sheet tab name | `기능정의서` or `Feature Specs` |
| `{{EDITABLE_COLUMNS}}` | Columns the skill may update | `K:P` |
| `{{READONLY_COLUMNS}}` | Columns the skill must not modify | `A:J` |

### feature-intake Specific
| Placeholder | Description | Example |
|-------------|-------------|---------|
| `{{STAGING_SERVER}}` | Staging environment URL | `https://staging.example.com` |
| `{{PROJECT_CLI}}` | Project-specific CLI tool | `myapp` or `ezq` |
| `{{PRODUCT_PHILOSOPHY_DOC}}` | Product philosophy doc path | `docs/PRODUCT.md` |
