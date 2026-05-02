---
description: OWASP Top 10 review of the diff
allowed_tools:
  - Read
  - Grep
  - Glob
  - "Bash(git diff*)"
  - "Bash(git log*)"
  - "Bash(git show*)"
---

Security review of the current branch against `[MAIN_BRANCH]`. Read-only.

## What to Check

1. **Broken access control** — new routes properly gated; policies used; default-deny
2. **Cryptographic failures** — secrets not in source; tokens hashed; TLS where required
3. **Injection** — SQL, OS command, path traversal, unsafe deserialization
4. **Insecure design** — missing rate limits; authorization-by-obscurity; trust without verification
5. **Misconfiguration** — features default off; debug off in prod; CORS scoped
6. **Vulnerable dependencies** — flagged versions in lock files
7. **Authentication failures** — weak password rules; missing MFA path; session handling
8. **Integrity failures** — unsigned updates; supply chain inputs not verified
9. **Logging/monitoring failures** — auth events logged; no PII or secrets in logs
10. **Server-Side Request Forgery** — outbound requests validate destinations

## Output Format

Numbered list:
- **File:line**
- **OWASP category**
- **Finding**
- **Suggested fix**

If no issues: "No security concerns identified in this diff."
