---
description: OWASP Top 10 mapped to [PROJECT_NAME]
---

# Security

Security review is required when a change touches: authentication, authorization, queries (SQL or otherwise), file I/O, external API calls, or response shapes.

## Access Control
- Every new route is behind the right middleware
- Authorization decisions go through policies, not inline checks
- Default-deny: opt in to access, never opt out

## Injection
- No string concatenation into queries — use parameterized queries / query builders
- No string concatenation into shell commands — use argv arrays
- No string concatenation into file paths — validate against an allowlist of base directories
- HTML output escapes by default; opt in to raw output explicitly

## Data Exposure
- Response shapes (resources, serializers, DTOs) only expose fields that the caller is authorized to see
- Sensitive fields (passwords, tokens, PII) never leave the database in logs, errors, or responses
- Error messages do not leak internal state to unauthorized callers

## Configuration
- Secrets come from environment variables, never from source
- Features default to **off**; opt in via configuration
- `env()` is read in config layers only — application code reads from config

## Dependencies
- Pin dependency versions
- Run dependency audit (`[DEPENDENCY_AUDIT_COMMAND]`) regularly
- Update on a cadence; security patches promptly

## Project-Specific Security Notes

[SECURITY_NOTES]
