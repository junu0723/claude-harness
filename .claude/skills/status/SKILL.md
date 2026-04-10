---
description: "Checks current development status, starts infrastructure services, and reports project health including open issues and milestones. Triggers: \"status\", \"start\", \"start server\""
---

# Check Status (status)

## Execution Steps

### 1. Check documents
```bash
# Understand current state
cat docs/STATUS.md
cat docs/ROADMAP.md
cat docs/HISTORY.md
```

### 2. Check Git status
```bash
git status
git log --oneline -5
gh issue list --limit 10
```

### 3. Start services

#### Clean up existing processes
```bash
# Check and kill processes using ports
lsof -ti:{{BACKEND_PORT}} | xargs kill -9 2>/dev/null  # Backend
lsof -ti:{{FRONTEND_PORT}} | xargs kill -9 2>/dev/null  # Frontend Dev
```

#### Check Docker services
```bash
docker ps | grep {{PROJECT_NAME}}
# If services are not running
{{INFRA_START_SCRIPT}}
```

#### Start Backend (background)
```bash
cd {{BACKEND_PATH}}
{{BACKEND_START}} &
```

#### Start Frontend (background)
```bash
cd {{FRONTEND_PATH}}
{{FRONTEND_START}} &
```

### 3.5 Service Health Check
```bash
# Check backend response
curl -s -o /dev/null -w "%{http_code}" http://localhost:{{BACKEND_PORT}}/actuator/health
# Check frontend response
curl -s -o /dev/null -w "%{http_code}" http://localhost:{{FRONTEND_PORT}}
```
MUST: Both services MUST return 200. On failure, check logs and report to the user.

### 4. Status report

```markdown
## Current Status

**Version:** vX.X.X
**Branch:** main

### Service Status
| Service | Port | Status |
|---------|------|--------|
| Backend | {{BACKEND_PORT}} | ✅ |
| Frontend | {{FRONTEND_PORT}} | ✅ |
| Docker | - | ✅ |

### Open Issues
- P1: N
- P2: N
- P3: N

### Next Milestone
vX.X.X - description
```

## Notes
- Server logs run in background
- Use `tail -f` or TaskOutput to check logs
