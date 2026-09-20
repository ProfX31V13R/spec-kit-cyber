# Security Requirements: [FEATURE NAME]

**Feature**: [Link to spec.md]
**Created**: [DATE]
**Status**: Draft
**Standard**: OWASP ASVS (primary) — requirements are mapped to ASVS chapters below

<!--
  This artifact is created by __SPECKIT_COMMAND_SPECIFY__ and lives in the
  feature directory next to spec.md. The spec's "Security Requirements"
  section is a summary of THIS document. All eight areas MUST be addressed:
  state the requirement or justify "N/A" explicitly. Silence is not acceptable
  (Security by Design — Constitution Principle I).
-->

## 1. Authentication (OWASP ASVS V2)

- **SR-AUTH-01**: [Who must authenticate, with what strength, e.g., "All users authenticate before any operation; MFA required for admin operations"]
- **SR-AUTH-02**: [Credential rules, e.g., "Passwords hashed with Argon2/bcrypt; no plaintext or reversible storage"]
- **SR-AUTH-03**: [Failure behavior, e.g., "Repeated failures trigger progressive delay/lockout; responses do not reveal whether the account exists"]
- **N/A justification**: [Only if the feature has zero authentication surface — justify why]

## 2. Authorization (OWASP ASVS V4)

- **SR-AUTZ-01**: [Access model, e.g., "Deny-by-default; permissions checked server-side on every operation"]
- **SR-AUTZ-02**: [Object-level rules, e.g., "Users may only access resources they own (prevent IDOR)"]
- **SR-AUTZ-03**: [Privileged operations, e.g., "Role changes require the target role's privileges; no self-elevation"]
- **N/A justification**: [...]

## 3. Session Management (OWASP ASVS V3)

- **SR-SESS-01**: [Session lifecycle, e.g., "Sessions expire after [N] minutes of inactivity and at most [N] hours absolute"]
- **SR-SESS-02**: [Invalidation, e.g., "Sessions invalidated on logout, password change, and role change"]
- **SR-SESS-03**: [Token rules, e.g., "Session tokens are random ≥128-bit, transmitted only over secure channels, regenerate on privilege change"]
- **N/A justification**: [e.g., stateless/CLI feature with no sessions]

## 4. Data Protection (OWASP ASVS V6, V8, V9)

- **SR-DATA-01**: [Encryption at rest for Confidential/Restricted classes, e.g., "AES-256 or platform equivalent"]
- **SR-DATA-02**: [Encryption in transit, e.g., "TLS 1.2+ for all channels; no plaintext protocols"]
- **SR-DATA-03**: [Data minimization/masking, e.g., "Sensitive fields masked in UI and never returned in bulk responses"]
- **SR-DATA-04**: [Retention/destruction, e.g., "Data deleted within [N] days of account deletion; deletion propagates to backups per policy"]

## 5. Audit (OWASP ASVS V7)

- **SR-AUD-01**: [Events recorded, e.g., "Authentication successes/failures, authorization denials, privilege and configuration changes, data export"]
- **SR-AUD-02**: [Record content, e.g., "Actor identity, action, target, timestamp (UTC), source address, correlation ID"]
- **SR-AUD-03**: [Integrity, e.g., "Audit records are append-only/tamper-evident and retained [N] days"]

## 6. Logging (OWASP ASVS V7, A09)

- **SR-LOG-01**: [What is logged, aligned with audit events above]
- **SR-LOG-02**: [What is NEVER logged: credentials, tokens, session IDs, PII payloads, health data]
- **SR-LOG-03**: [Log injection defense, e.g., "Control characters sanitized; structured format"]

## 7. Availability (OWASP ASVS V12, V5 partially)

- **SR-AVAIL-01**: [Graceful degradation, e.g., "Feature degrades without cascading failure when a dependency is down"]
- **SR-AVAIL-02**: [Resource protection, e.g., "Timeouts, bounded queues, and backpressure on all external calls"]

## 8. Abuse Protection (OWASP ASVS V12 / Proactive Control C12)

- **SR-ABUSE-01**: [Rate limiting, e.g., "Public endpoints limited to [N] requests/minute per client"]
- **SR-ABUSE-02**: [Input abuse, e.g., "Payload size limits, content-type enforcement, anti-replay where applicable"]
- **SR-ABUSE-03**: [Anti-automation, e.g., "Challenges or progressive delays on suspicious patterns"]

## Traceability Map

<!--
  MANDATORY: every SR maps forward to a control (plan Security Controls
  Matrix) and a verification task. This is Constitution Principle VI.
-->

| Requirement | ASVS Chapter | Control (plan matrix ref) | Verification task (tasks.md ref) |
|-------------|--------------|---------------------------|----------------------------------|
| SR-AUTH-01 | V2.x | [CM-###] | [T###] |
| SR-AUTZ-01 | V4.x | [CM-###] | [T###] |
| ... | | | |

## Spec Summary Sync

- [ ] The eight areas are addressed in this document
- [ ] spec.md's Security Requirements section (SR-001..008) matches this document
- [ ] Data classification is complete in spec.md and drives the Data Protection rules above
