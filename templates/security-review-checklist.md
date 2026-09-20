# Security Review Checklist: [FEATURE NAME]

**Feature**: [Link to spec.md]
**Created**: [DATE]
**Purpose**: Feature-wide security verification at Converge. This checklist is the evidence the Security Verification Gate checks.
**Standards**: OWASP ASVS (primary), OWASP Top 10 (verification), OWASP Proactive Controls

<!--
  Generated during planning (feature-wide items) and completed during the
  Security Verification & Hardening phase of tasks.md and at Converge
  (__SPECKIT_COMMAND_CONVERGE__ Step 5b). Check an item ONLY with recorded
  evidence — a checked box without evidence is a finding, not a pass.
-->

## SAST (Static Analysis)

- [ ] SAST scanner configured and executed on the full codebase (scanner: [name], run: [date/CI job])
- [ ] Zero unresolved Critical findings
- [ ] Zero unresolved High findings
- [ ] Medium/Low findings triaged: fix or document accepted risk

## Secret Scanning

- [ ] Secret scanner configured (pre-commit + CI)
- [ ] Zero secrets in source code
- [ ] Zero secrets in configuration files and templates
- [ ] Zero secrets in tests and fixtures
- [ ] Git history scanned (when tooling available); any historical leak rotated

## Dependency Review

- [ ] Dependency vulnerability scan executed (lockfile analyzed)
- [ ] Zero Critical vulnerabilities (or approved mitigation documented)
- [ ] Zero High vulnerabilities without approved, documented mitigation
- [ ] Lockfile present with integrity hashes; no floating versions in production manifests

## Threat Model

- [ ] threat-model.md reviewed against the final implementation
- [ ] All previously High/Critical risks: marked Mitigated with evidence, or Accepted with recorded risk acceptance
- [ ] No new High/Critical risks introduced by implementation changes
- [ ] SAC traceability table (§8) complete: threat → SAC → control → task → evidence

## Authorization / RBAC

- [ ] Every role × operation combination tested (allow list AND deny list)
- [ ] Object-level access verified (IDOR checks on every resource access)
- [ ] Privilege escalation attempts tested (self-elevation, parameter tampering)
- [ ] Authorization enforced server-side only (no client-side-only checks)

## Input Validation & Output Encoding

- [ ] Every entry point validates input (type, length, format, range)
- [ ] Output encoded per context (HTML, URL, SQL, shell, LDAP, XML)
- [ ] All database access uses parameterized queries / safe ORMs
- [ ] File uploads (if any): type/size validated, stored safely, served safely

## Logging & Auditability

- [ ] Security events recorded: authN success/failure, authZ denials, privilege/config changes
- [ ] Logs contain actor, action, target, timestamp, correlation ID
- [ ] No credentials, tokens, session IDs, or sensitive payloads in logs
- [ ] Audit trail reconstructable: "who did what, when" answerable from logs

## Data Protection

- [ ] Confidential/Restricted data encrypted at rest
- [ ] All channels encrypted in transit (TLS 1.2+)
- [ ] Sensitive fields masked in responses, UI, and exports
- [ ] Retention and deletion rules implemented as specified

## Hardening

- [ ] Security headers present (CSP, HSTS, X-Content-Type-Options, frame options — web features)
- [ ] Cookie flags: Secure, HttpOnly, SameSite (web features)
- [ ] Rate limiting active on public entry points
- [ ] Error responses generic to users; details only in server logs
- [ ] Debug endpoints, verbose modes, and default credentials disabled in production config
- [ ] CORS restricted to allowed origins (no wildcard with credentials)

## Gate Decision

**Blocking conditions** (Constitution — Security Governance):

- [ ] No unresolved critical vulnerability
- [ ] No High-severity vulnerability without approved mitigation
- [ ] Required security evidence recorded above (SAST, secrets, dependencies, RBAC, logging, threat model)

**Result**: [PASS — feature may close / FAIL — evidence gaps listed below]

**Evidence gaps / risk acceptances**:

| Item | Gap or Acceptance | Approved by | Date |
|------|-------------------|-------------|------|
| [...] | [...] | [...] | [...] |
