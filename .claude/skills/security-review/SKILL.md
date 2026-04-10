---
description: "Performs security review against OWASP Top 10 and common vulnerability patterns. Scans code changes for injection, auth bypass, data exposure, and misconfiguration risks. Triggers: \"security review\", \"vulnerability\""
---

# Security Review

Code security review based on OWASP Top 10. Scans changed code for vulnerabilities.

## Execution Steps

### Step 1: Identify Scope of Changes

```bash
git diff --name-only HEAD~1
git diff --stat
```

Categorize changed files:
- **API endpoints** — input validation, authentication/authorization
- **Database queries** — SQL injection, ORM safety
- **Frontend** — XSS, CSRF, sensitive data exposure
- **Config/Infra** — hardcoded secrets, security headers
- **Auth** — session management, token handling, permission verification

### Step 2: OWASP Top 10 Check

Check applicable items for each changed file:

| # | Vulnerability | Checklist |
|---|--------------|-----------|
| A01 | Broken Access Control | Missing permission checks, IDOR, path traversal |
| A02 | Cryptographic Failures | Plaintext storage, weak algorithms, hardcoded keys |
| A03 | Injection | SQL/NoSQL/OS/LDAP injection, parameter binding |
| A04 | Insecure Design | Business logic flaws, missing rate limiting |
| A05 | Security Misconfiguration | Debug mode, default accounts, unnecessary features |
| A06 | Vulnerable Components | Dependencies with known vulnerabilities |
| A07 | Auth Failures | Weak password policy, session fixation, brute force |
| A08 | Data Integrity | Deserialization vulnerabilities, CI/CD pipeline integrity |
| A09 | Logging Failures | Logging sensitive data, missing audit trail |
| A10 | SSRF | Server-side request forgery, missing URL validation |

### Step 3: Code Pattern Scan

MUST check for these patterns:

**Injection prevention:**
```bash
# Detect SQL string concatenation
grep -rn "\".*SELECT.*\" + \|\".*INSERT.*\" + \|\".*UPDATE.*\" + \|\".*DELETE.*\" +" --include="*.java" --include="*.ts" --include="*.py"

# Dangerous eval/exec
grep -rn "eval(\|exec(\|Runtime.exec\|ProcessBuilder" --include="*.java" --include="*.ts" --include="*.py"
```

**Hardcoded secrets:**
```bash
# Hardcoded passwords/tokens
grep -rn "password\s*=\s*[\"']\|secret\s*=\s*[\"']\|api_key\s*=\s*[\"']\|token\s*=\s*[\"']" --include="*.java" --include="*.ts" --include="*.py" --include="*.yml" --include="*.properties"
```

**XSS prevention:**
```bash
# React dangerouslySetInnerHTML
grep -rn "dangerouslySetInnerHTML\|innerHTML\s*=" --include="*.tsx" --include="*.jsx" --include="*.ts"
```

### Step 4: Check Dependency Vulnerabilities

```bash
# Node.js
npm audit 2>/dev/null || pnpm audit 2>/dev/null

# Java/Gradle (if OWASP dependency check is installed)
./gradlew dependencyCheckAnalyze 2>/dev/null || echo "dependency-check not installed"

# Python
pip audit 2>/dev/null || echo "pip-audit not installed"
```

### Step 5: Security Review Report

```markdown
## Security Review Results

**Scope:** <range of changes>
**Date:** YYYY-MM-DD

### Findings

| Severity | OWASP | File | Description | Remediation |
|----------|-------|------|-------------|-------------|
| Critical | A03 | path/file.java:42 | SQL string concatenation | Use PreparedStatement |
| Medium | A01 | path/api.ts:15 | Missing permission check | Add @PreAuthorize |
| Low | A09 | path/service.java:88 | Password logged | Apply masking |

### Items Not Found
- OWASP A02, A04, A05, A06, A07, A08, A10: not applicable or no risk found

### Recommended Actions
1. [Critical] Requires immediate fix
2. [Medium] Fix recommended in this PR
3. [Low] Can be addressed in a future sprint
```

## MUST Rules

- [ ] All API endpoints have authentication/authorization checks
- [ ] User input MUST be validated/escaped before use
- [ ] Secrets MUST be loaded from environment variables or a vault (hardcoding is MUST NOT)
- [ ] Error messages MUST NOT expose internal implementation details
- [ ] Sensitive data (passwords, tokens) MUST NOT be written to logs
