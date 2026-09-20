# Secure Design Review: [FEATURE NAME]

**Feature**: [Link to spec.md]
**Plan**: [Link to plan.md]
**Date**: [DATE]
**Reviewer**: [Name / agent]
**Standards**: OWASP ASVS (primary), OWASP Top 10 2021 (verification), OWASP API Security Top 10 (API features), OWASP Proactive Controls (implementation guidance)

<!--
  This artifact is generated during Phase 1 of __SPECKIT_COMMAND_PLAN__.
  The plan's "Security Architecture Review", "Security Controls Matrix",
  and "OWASP Top 10 Review" sections are summaries of THIS document.
  GATE: if any High/Critical threat lacks a mapped mitigation, planning
  MUST NOT proceed to tasks.
-->

## 1. Security Architecture Review

### 1.1 Architecture Validation

- [ ] Trust boundaries from threat-model.md are visible and enforced in the design (validation at EVERY boundary, not only at the edge)
- [ ] Least Privilege applied: each component/service/process/role has minimum rights (enumerate below)
- [ ] Fail-secure defaults: authZ failure denies; error paths do not bypass controls; external dependency failure fails closed where security-relevant
- [ ] Single points of security failure identified and compensated by a second layer (Defense in Depth)

**Component privilege inventory**:

| Component | Privileges | Minimized? (justify) |
|-----------|-----------|----------------------|
| [e.g., API service] | [e.g., Read/write orders table only — no DDL, no users table] | Yes/No + why |

### 1.2 Dependencies Validation

| Dependency | Version | License | Known vulns (Critical/High) | Provenance verified |
|-----------|---------|---------|------------------------------|---------------------|
| [e.g., fastapi] | [pinned] | [MIT] | [None / CVE-...] | [lockfile hash ✓] |

- [ ] No dependency carries an unresolved Critical/High vulnerability (or has approved, documented mitigation)
- [ ] Lockfile pins all transitive dependencies with integrity hashes
- [ ] Registries/sources are trusted; no curl|bash style installs

### 1.3 External Integrations Validation

| Integration | Direction | Protocol | Data exchanged (classification) | AuthN |
|-------------|-----------|----------|--------------------------------|-------|
| [e.g., payments API] | Outbound | HTTPS | [payment data — Confidential] | [API key from Secret Manager] |

- [ ] Every integration inventoried above (complete list)
- [ ] Outbound URL fetching (if any): only http/https schemes allowed; host validation rejects localhost/loopback/private/reserved addresses unless explicitly required and documented (SSRF — A10)
- [ ] Responses from external systems validated as untrusted input
- [ ] Failures handled securely (timeouts, no sensitive data in error messages)

### 1.4 Secrets Management Validation

- [ ] No secrets in source, config templates, IaC, or tests (secret scan evidence)
- [ ] Secrets injected at runtime from Secret Manager / environment — never baked into artifacts or images
- [ ] Each secret has a defined rotation procedure without code changes
- [ ] Access to secrets is Least Privilege and audited

## 2. Security Controls Matrix

<!--
  The traceability spine (Constitution Principle VI):
  Requirement → Control → Implementation → Test. One row per SR-###/SAC-###.
  A requirement without a control row is a design gap — resolve before tasks.
-->

| Req ID | Requirement (summary) | Control | Implementation location | Verification |
|--------|----------------------|---------|------------------------|--------------|
| SR-AUTH-01 / SAC-### | [e.g., All operations require authentication] | [AuthN middleware] | [src/middleware/auth] | [AuthN tests: unauthenticated → 401 on every endpoint] |
| SR-AUTZ-01 / SAC-003 | [Deny-by-default authorization] | [AuthZ policy layer] | [src/authz/] | [Role×operation test matrix, allow + deny] |
| SAC-001 | [Input validation] | [Schema validation at boundary] | [src/validation/] | [Validation test suite incl. malformed inputs] |
| SAC-002 | [No secrets in code] | [Secret Manager + scanners] | [config/, .pre-commit] | [Secret scan CI — zero findings] |
| SAC-004 | [Encryption of Confidential/Restricted] | [TLS + at-rest encryption] | [infra/platform config] | [Config verification test] |
| TM-### | [e.g., SQL injection] | [Parameterized queries only] | [data layer] | [SAST rule + injection tests] |

## 3. OWASP Top 10 (2021) Review

<!--
  For each category: state the concrete impact of THIS design and the
  mitigation. "No impact" requires justification. Ties to the matrix above.
-->

| Category | Impact on this feature | Mitigation (matrix ref) | Status |
|----------|----------------------|-------------------------|--------|
| A01 Broken Access Control | [impact] | [mitigation / CM row] | [Addressed / N/A because...] |
| A02 Cryptographic Failures | [impact] | [mitigation] | [...] |
| A03 Injection | [impact] | [mitigation] | [...] |
| A04 Insecure Design | [impact] | [mitigation — this review + threat model] | [...] |
| A05 Security Misconfiguration | [impact] | [secure defaults, hardened config] | [...] |
| A06 Vulnerable & Outdated Components | [impact] | [dependency validation §1.2] | [...] |
| A07 Identification & Authentication Failures | [impact] | [mitigation] | [...] |
| A08 Software & Data Integrity Failures | [impact] | [lockfile, provenance, CI integrity] | [...] |
| A09 Security Logging & Monitoring Failures | [impact] | [audit + logging SRs] | [...] |
| A10 Server-Side Request Forgery (SSRF) | [impact] | [outbound validation §1.3] | [...] |

## 4. OWASP API Security Top 10 (2023) — API features only

*Complete this section when the feature exposes or consumes APIs. Delete otherwise.*

| Category | Impact | Mitigation | Status |
|----------|--------|------------|--------|
| API1 Broken Object Level Authorization | [impact] | [object-level checks] | [...] |
| API2 Broken Authentication | [impact] | [...] | [...] |
| API3 Broken Object Property Level Authorization | [impact] | [property filtering/mass assignment guards] | [...] |
| API4 Unrestricted Resource Consumption | [impact] | [rate limits, payload caps] | [...] |
| API5 Broken Function Level Authorization | [impact] | [...] | [...] |
| API6 Unrestricted Access to Sensitive Business Flows | [impact] | [abuse protection] | [...] |
| API7 Server Side Request Forgery | [impact] | [...] | [...] |
| API8 Security Misconfiguration | [impact] | [...] | [...] |
| API9 Improper Inventory Management | [impact] | [API inventory/versioning] | [...] |
| API10 Unsafe Consumption of APIs | [impact] | [validate responses, outbound rules] | [...] |

## 5. Gate Evaluation

- [ ] Every SR-###/SAC-### has a row in the Security Controls Matrix
- [ ] Every High/Critical threat (threat-model.md) maps to a control AND a verification
- [ ] No unresolved Critical/High dependency vulnerabilities
- [ ] No secrets in design artifacts or configuration templates
- [ ] OWASP Top 10 review complete (all rows addressed or justified N/A)
- [ ] API Security review complete (API features)

**Result**: [PASS → proceed to tasks / FAIL → gaps listed below]

**Gaps (if FAIL)**:

| Gap | Blocking? | Owner | Resolution |
|-----|-----------|-------|------------|
| [...] | [...] | [...] | [...] |
