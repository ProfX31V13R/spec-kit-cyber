# Implementation Plan: [FEATURE]

**Branch**: `[###-feature-name]` | **Date**: [DATE] | **Spec**: [link]

**Input**: Feature specification from `/specs/[###-feature-name]/spec.md`

**Note**: This template is filled in by the `__SPECKIT_COMMAND_PLAN__` command; its definition describes the execution workflow.

## Summary

[Extract from feature spec: primary requirement + technical approach from research]

## Technical Context

<!--
  ACTION REQUIRED: Replace the content in this section with the technical details
  for the project. The structure here is presented in advisory capacity to guide
  the iteration process.
-->

**Language/Version**: [e.g., Python 3.11, Swift 5.9, Rust 1.75 or NEEDS CLARIFICATION]

**Primary Dependencies**: [e.g., FastAPI, UIKit, LLVM or NEEDS CLARIFICATION]

**Storage**: [if applicable, e.g., PostgreSQL, CoreData, files or N/A]

**Testing**: [e.g., pytest, XCTest, cargo test or NEEDS CLARIFICATION]

**Target Platform**: [e.g., Linux server, iOS 15+, WASM or NEEDS CLARIFICATION]

**Project Type**: [e.g., library/cli/web-service/mobile-app/compiler/desktop-app or NEEDS CLARIFICATION]

**Performance Goals**: [domain-specific, e.g., 1000 req/s, 10k lines/sec, 60 fps or NEEDS CLARIFICATION]

**Constraints**: [domain-specific, e.g., <200ms p95, <100MB memory, offline-capable or NEEDS CLARIFICATION]

**Scale/Scope**: [domain-specific, e.g., 10k users, 1M LOC, 50 screens or NEEDS CLARIFICATION]

**Security Context** *(mandatory)*: [Summarize from spec: highest-classification data handled, top threat-model risks, authN/authZ surface. If the spec lacks security sections, STOP and re-run `__SPECKIT_COMMAND_SPECIFY__`]

## Constitution Check

*GATE: Must pass before Phase 0 research. Re-check after Phase 1 design.*

[Gates determined based on constitution file]

**Security principles gate** *(from Constitution Principles I–VI)*:

- [ ] Security by Design: spec contains SR-###, data classification, threat model, SAC-###
- [ ] Least Privilege: every component/service/role in the design runs with minimum privileges
- [ ] Secure Default Configuration: all defaults shipped by this design are secure
- [ ] Defense in Depth: identified controls span ≥2 independent layers
- [ ] Zero Trust: every trust boundary crossing is authenticated/authorized/encrypted
- [ ] Traceability: each SR-###/SAC-### maps to a control, an implementation task, and a test

## Security Architecture Review *(mandatory)*

<!--
  ACTION REQUIRED: Complete this review during Phase 1 design. The detailed
  working copy lives in secure-design-review.md; this section is the plan
  summary. ERROR if any item cannot be validated.
-->

**Architecture validation**:

- [ ] Trust boundaries from the threat model are visible and enforced in the architecture (validation at every boundary, not only at the edge)
- [ ] Components follow Least Privilege (minimum rights per service/process/role)
- [ ] Fail-secure behavior on control failures (authZ failure denies, error paths don't bypass controls)

**Dependencies validation**:

- [ ] Every third-party dependency is identified with version and license
- [ ] No dependency has known Critical/High vulnerabilities (verified via dependency review)
- [ ] Dependency provenance and integrity verification (hashes/signatures/lockfile)

**External integrations validation**:

- [ ] Every external integration is inventoried (target, protocol, data exchanged, classification)
- [ ] Outbound calls validate destination hosts and reject localhost/loopback/private/reserved addresses unless explicitly required and documented
- [ ] Responses from external systems are treated as untrusted input (validated)
- [ ] Failures in integrations fail securely without information leakage

**Secrets management validation**:

- [ ] No secrets in code, config files, templates, or tests (secret scanning evidence recorded)
- [ ] Secrets are injected at runtime from a Secret Manager / environment — never baked into artifacts
- [ ] Secret rotation is designed for (procedure documented, no hardcoded lifetimes)

## Security Controls Matrix *(mandatory)*

<!--
  ACTION REQUIRED: Map EVERY security requirement to its control,
  implementation location, and verification test. This is the traceability
  spine (Constitution Principle VI): Requirement → Control → Implementation →
  Test. One row per SR-### and SAC-###.
-->

| Requirement | Control | Implementation | Verification |
|-------------|---------|----------------|--------------|
| SR-### / SAC-### | [control, e.g., server-side authZ middleware, deny-by-default] | [file/component where it lives] | [test/evidence, e.g., authZ test per endpoint, SAST rule] |
| SAC-001 | Input validation at every boundary | [e.g., src/validation/] | [schema/validation tests] |
| SAC-002 | No secrets in code | Secret Manager + pre-commit scanning | [secret scan report, CI check] |
| SAC-003 | Per-endpoint authorization | [e.g., authZ middleware] | [authorization test matrix] |
| SAC-004 | Encryption at rest/in transit | [e.g., TLS termination, storage encryption] | [config verification test] |

## OWASP Top 10 Review *(mandatory)*

<!--
  ACTION REQUIRED: For each OWASP Top 10 (2021) category, state the design
  impact for THIS feature and how it is mitigated. "No impact" requires a
  justification. Full detail lives in secure-design-review.md.
-->

| Category | Impact on this feature | Mitigation (control / task reference) |
|----------|----------------------|----------------------------------------|
| A01 Broken Access Control | [impact] | [mitigation] |
| A02 Cryptographic Failures | [impact] | [mitigation] |
| A03 Injection | [impact] | [mitigation] |
| A04 Insecure Design | [impact] | [mitigation] |
| A05 Security Misconfiguration | [impact] | [mitigation] |
| A06 Vulnerable & Outdated Components | [impact] | [mitigation] |
| A07 Identification & Authentication Failures | [impact] | [mitigation] |
| A08 Software & Data Integrity Failures | [impact] | [mitigation] |
| A09 Security Logging & Monitoring Failures | [impact] | [mitigation] |
| A10 Server-Side Request Forgery (SSRF) | [impact] | [mitigation] |

**API features additionally complete the OWASP API Security Top 10 (API1–API10) review** in `secure-design-review.md`.

## Project Structure

### Documentation (this feature)

```text
specs/[###-feature]/
├── plan.md                      # This file (__SPECKIT_COMMAND_PLAN__ command output)
├── research.md                  # Phase 0 output (__SPECKIT_COMMAND_PLAN__ command)
├── data-model.md                # Phase 1 output (__SPECKIT_COMMAND_PLAN__ command)
├── quickstart.md                # Phase 1 output (__SPECKIT_COMMAND_PLAN__ command)
├── contracts/                   # Phase 1 output (__SPECKIT_COMMAND_PLAN__ command)
├── security-requirements.md     # Specify output (SR-### mapped to OWASP ASVS)
├── threat-model.md              # Specify output (STRIDE model)
├── secure-design-review.md      # Phase 1 security output (this command)
├── checklists/                  # Quality & security checklists
│   ├── requirements.md          # Spec quality checklist (incl. Security section)
│   ├── owasp-asvs.md            # ASVS verification checklist
│   └── api-security.md          # API Security checklist (API features only)
└── tasks.md                     # Phase 2 output (__SPECKIT_COMMAND_TASKS__ command - NOT created by __SPECKIT_COMMAND_PLAN__)
```

### Source Code (repository root)
<!--
  ACTION REQUIRED: Replace the placeholder tree below with the concrete layout
  for this feature. Delete unused options and expand the chosen structure with
  real paths (e.g., apps/admin, packages/something). The delivered plan must
  not include Option labels.
-->

```text
# [REMOVE IF UNUSED] Option 1: Single project (DEFAULT)
src/
├── models/
├── services/
├── cli/
└── lib/

tests/
├── contract/
├── integration/
└── unit/

# [REMOVE IF UNUSED] Option 2: Web application (when "frontend" + "backend" detected)
backend/
├── src/
│   ├── models/
│   ├── services/
│   └── api/
└── tests/

frontend/
├── src/
│   ├── components/
│   ├── pages/
│   └── services/
└── tests/

# [REMOVE IF UNUSED] Option 3: Mobile + API (when "iOS/Android" detected)
api/
└── [same as backend above]

ios/ or android/
└── [platform-specific structure: feature modules, UI flows, platform tests]
```

**Structure Decision**: [Document the selected structure and reference the real
directories captured above]

## Complexity Tracking

> **Fill ONLY if Constitution Check has violations that must be justified**

| Violation | Why Needed | Simpler Alternative Rejected Because |
|-----------|------------|-------------------------------------|
| [e.g., 4th project] | [current need] | [why 3 projects insufficient] |
| [e.g., Repository pattern] | [specific problem] | [why direct DB access insufficient] |
