# Security Guidelines

> Security policies and practices for this project.
> Referenced by the `security-review` skill.

## Authentication & Authorization

<!-- Describe your auth architecture -->

## Data Protection

- Secrets: environment variables or vault, never hardcoded
- PII: encrypted at rest, masked in logs
- Passwords: bcrypt/argon2 hashed, never stored in plaintext

## API Security

- Input validation on all endpoints
- Rate limiting on auth endpoints
- CORS configured for allowed origins only
- CSRF protection enabled

## Dependency Management

- Regular `npm audit` / `pip audit` / dependency check
- No known critical CVEs in production dependencies

## Incident Response

<!-- Describe your security incident process -->
