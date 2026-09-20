# Threat Model: [FEATURE NAME]

**Feature**: [Link to spec.md]
**Created**: [DATE]
**Status**: Draft
**Method**: STRIDE per trust boundary (Microsoft threat modeling methodology, aligned with OWASP ASVS V1 and NIST SSDF PW.4)

<!--
  This artifact is created by __SPECKIT_COMMAND_SPECIFY__ and lives in the
  feature directory next to spec.md. It is updated during planning (controls
  mapped) and at convergence (mitigation status verified). Every High or
  Critical risk MUST map to at least one SAC-### criterion in the spec.
-->

## 1. Scope

**Feature summary**: [One paragraph — what the feature does and its boundaries]

**In scope**: [Components, data flows, integrations included in this model]

**Out of scope**: [Explicitly excluded, with reason, e.g., "Third-party SaaS internals — covered by their SOC2"]

## 2. Assets

<!--
  Derive from the spec's Data Classification table. Every asset listed here
  must have a classification there.
-->

| Asset | Classification | Why it matters |
|-------|----------------|----------------|
| [e.g., User credentials] | Restricted | Account takeover → full compromise |
| [e.g., Order history] | Confidential | PII / financial exposure |
| [e.g., Feature availability] | n/a (availability asset) | Business continuity |

## 3. Actors

| Actor | Type | Motivation / Notes |
|-------|------|--------------------|
| Anonymous visitor | External, untrusted | Recon, abuse of public endpoints |
| Authenticated user | External, authenticated | May attempt object access abuse (IDOR) |
| Privileged user / admin | Internal, authenticated | Highest blast radius; monitor for misuse |
| External service | System | Compromisable; its responses are untrusted input |
| Operator / CI | System, privileged | Credential theft target; supply chain vector |

## 4. Trust Boundaries

<!--
  A trust boundary is any point where data or execution crosses a change of
  trust: internet→edge, client→server, server→database, service→service,
  system→external API. STRIDE is applied per crossing below.
-->

| ID | Boundary | Crossing data / action |
|----|----------|------------------------|
| TB-01 | Internet → Application (public entry) | [e.g., All user requests] |
| TB-02 | Application → Database | [e.g., Queries with user data] |
| TB-03 | Application → External API | [e.g., Outbound calls with API key] |
| TB-04 | Frontend → Backend API | [e.g., Session token + payloads] |

## 5. STRIDE Analysis

<!--
  Apply all six categories to each trust boundary. Rate severity by impact ×
  likelihood: Critical / High / Medium / Low. Delete nothing — mark
  "Not applicable because..." if a category doesn't apply at a boundary.
-->

### TB-01: Internet → Application

| ID | Threat | STRIDE | Severity | Mitigation | Status |
|----|--------|--------|----------|------------|--------|
| TM-001 | [e.g., Attacker spoofs another user via session theft] | Spoofing | High | [e.g., Secure/HttpOnly/SameSite cookies, TLS, token rotation → SAC-###] | [Planned / Mitigated / Accepted] |
| TM-002 | [e.g., Malformed input corrupts data] | Tampering | ... | [...] | [...] |
| TM-003 | [e.g., Attacker denies performing an action] | Repudiation | ... | [Audit trail SR-AUD-01] | [...] |
| TM-004 | [e.g., Verbose errors leak internals] | Information Disclosure | ... | [...] | [...] |
| TM-005 | [e.g., Resource exhaustion on public endpoint] | Denial of Service | ... | [SR-ABUSE-01 rate limiting] | [...] |
| TM-006 | [e.g., Forced browsing to admin functions] | Elevation of Privilege | ... | [Deny-by-default authZ SR-AUTZ-01] | [...] |

### TB-02: Application → Database

| ID | Threat | STRIDE | Severity | Mitigation | Status |
|----|--------|--------|----------|------------|--------|
| TM-### | [e.g., SQL injection via user input] | Tampering | Critical | [Parameterized queries → SAC-001] | [...] |
| TM-### | [e.g., Unencrypted sensitive columns readable by low-priv account] | Information Disclosure | ... | [...] | [...] |

### TB-03: Application → External API

| ID | Threat | STRIDE | Severity | Mitigation | Status |
|----|--------|--------|----------|------------|--------|
| TM-### | [e.g., External service returns malicious payload] | Tampering | ... | [Validate responses as untrusted input] | [...] |
| TM-### | [e.g., API key leaked in logs] | Information Disclosure | ... | [SR-LOG-02] | [...] |

### TB-04: Frontend → Backend API

| ID | Threat | STRIDE | Severity | Mitigation | Status |
|----|--------|--------|----------|------------|--------|
| TM-### | [e.g., Client-side-only authorization bypassed by direct API call] | Elevation of Privilege | Critical | [Server-side authZ on every endpoint → SAC-003] | [...] |

[Add one subsection per trust boundary from section 4]

## 6. Risk Summary

| Severity | Count | IDs |
|----------|-------|-----|
| Critical | [n] | [TM-###, ...] |
| High | [n] | [TM-###, ...] |
| Medium | [n] | [TM-###, ...] |
| Low | [n] | [TM-###, ...] |

## 7. Assumptions & Dependencies

- [Assumption, e.g., "Platform TLS termination is configured and maintained by infra team"]
- [Dependency, e.g., "Relies on identity provider for MFA enforcement"]

## 8. SAC Traceability (MANDATORY)

<!--
  Every High/Critical risk MUST map to at least one Security Acceptance
  Criterion in the spec. This table is the linkage. Checked by Converge.
-->

| Threat ID | Severity | SAC ID(s) | Control (plan matrix) | Verification task |
|-----------|----------|-----------|----------------------|-------------------|
| TM-### | Critical | SAC-### | [CM-###] | [T###] |

## 9. Change Log

| Date | Change | Author |
|------|--------|--------|
| [DATE] | Initial model created during Specify | `__SPECKIT_COMMAND_SPECIFY__` |
