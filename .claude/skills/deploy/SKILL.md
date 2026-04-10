---
description: "Deploys latest version to server with pre-deployment validation, health checks, smoke tests, and rollback verification. Triggers: \"deploy\", \"server deploy\""
---

# Deploy (deploy)

## Server Information
- **Host:** {{DEPLOY_HOST}}
- **URL:** {{DEPLOY_URL}}
- **User:** {{DEPLOY_USER}}

## Execution Steps

### 0. Pre-deploy Checklist
MUST verify ALL items before proceeding:
- [ ] `git status` — working tree clean
- [ ] Frontend build successful
- [ ] Backend build successful
- [ ] Current branch is `main` and up to date

If any item fails, stop the deployment and report to the user.

### 1. Run deployment script
```bash
# Check environment variable
echo ${{DEPLOY_PASS_ENV}}

# Run deployment
{{DEPLOY_SCRIPT}}
```

The password is read automatically from the environment variable `{{DEPLOY_PASS_ENV}}`.

### 2. Verify deployment
```bash
# Check server status via SSH
sshpass -p "${{DEPLOY_PASS_ENV}}" ssh {{DEPLOY_USER}}@{{DEPLOY_HOST}} "docker ps"

# Check logs
sshpass -p "${{DEPLOY_PASS_ENV}}" ssh {{DEPLOY_USER}}@{{DEPLOY_HOST}} "docker logs {{BACKEND_CONTAINER}} --tail 50"
```

### 2.5 Health Check
```bash
# Verify service response (wait up to 60 seconds)
for i in $(seq 1 12); do
  HTTP_CODE=$(curl -s -o /dev/null -w "%{http_code}" {{HEALTH_ENDPOINT}})
  if [ "$HTTP_CODE" = "200" ]; then echo "Backend healthy"; break; fi
  sleep 5
done

# Verify frontend
curl -s -o /dev/null -w "%{http_code}" {{DEPLOY_URL}}
```
MUST: Backend health endpoint MUST return 200.

### 3. Web access test
- Verify access to {{DEPLOY_URL}}
- Login test: {{TEST_USER}} / {{TEST_PASS}}

## Deployment Script Details

`{{DEPLOY_SCRIPT}}`:
1. git pull (on server)
2. docker-compose pull
3. docker-compose up -d
4. Service health check

## Rollback Procedure

**Rollback trigger conditions:**
- Backend health check fails (non-200 response)
- Cannot log in
- Many 500 errors in console

On failure:
```bash
# Roll back to previous version
sshpass -p "${{DEPLOY_PASS_ENV}}" ssh {{DEPLOY_USER}}@{{DEPLOY_HOST}} "
  cd ~/project
  git checkout vX.Y-1.Z
  docker-compose up -d --build
"
```

## Completion Checklist
MUST verify:
- [ ] Backend health: 200 OK
- [ ] Frontend accessible: OK
- [ ] Login test: {{TEST_USER}} successful
- [ ] Rollback command ready (see rollback procedure above)
