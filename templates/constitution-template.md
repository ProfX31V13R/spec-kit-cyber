# [PROJECT_NAME] Constitution
<!-- Example: Spec Constitution, TaskFlow Constitution, etc. -->

## Core Principles

<!--
  PRINCIPLES I–VI ARE THE MANDATORY SECURITY BASELINE (Security by Design).
  They are NON-NEGOTIABLE and MUST NOT be removed or weakened by amendments.
  Project-specific principles (VII+) may be added below.
-->

### I. Security by Design (NON-NEGOTIABLE)

Every feature MUST define its security requirements, data classification, and threat model BEFORE it is implemented. Security is specified in the same phase as functionality — never retrofitted. A spec without Security Acceptance Criteria (SAC) is incomplete and MUST NOT advance to planning. Aligned with OWASP ASVS and NIST SSDF (PO.1, PS.1).

### II. Least Privilege (NON-NEGOTIABLE)

Every user, service, process, and component MUST be granted the minimum privileges necessary to perform its function — and no more. Access is deny-by-default; permissions are granted explicitly, reviewed per feature, and revoked when no longer needed. Aligned with OWASP ASVS V1, V4.

### III. Secure Default Configuration (NON-NEGOTIABLE)

Default configurations MUST be secure out of the box. Debug endpoints, verbose errors, permissive CORS, disabled auth, default credentials, and insecure sample data are forbidden in any shipped default. Weakening a secure default requires an explicit, documented, reviewed exception.

### IV. Defense in Depth (NON-NEGOTIABLE)

Security controls MUST exist in multiple layers. A single control failure (input validation, authentication, network boundary) MUST NOT result in a full compromise. Layered controls are validated during design review.

### V. Zero Trust (NON-NEGOTIABLE)

Never trust implicitly — users, systems, services, networks, and internal calls are untrusted by default. Every request is authenticated, authorized, and encrypted in transit regardless of origin. Service-to-service and internal-API calls follow the same rules as external ones.

### VI. Traceability (NON-NEGOTIABLE)

Every security requirement MUST be traceable end-to-end: Specification (SR-###/SAC-###) → Plan control (SC-###) → Implementation task (T###) → Verification evidence (checklists, SAST, tests, review records). A security requirement without traceable evidence is treated as unimplemented. Aligned with OWASP ASVS V1.8 and NIST SSDF (PS.2, PW.7).

### [PRINCIPLE_7_NAME] *(optional, project-specific)*
<!-- Example: VII. Library-First, CLI Interface, Test-First, Observability... -->
[PRINCIPLE_7_DESCRIPTION]
<!-- Example: Every feature starts as a standalone library; TDD mandatory; structured logging required -->

### [PRINCIPLE_8_NAME] *(optional, project-specific)*
<!-- Example: VIII. Versioning & Breaking Changes, Simplicity... -->
[PRINCIPLE_8_DESCRIPTION]

## Security Governance *(mandatory)*

**Standards baseline** (verification is MANDATORY for every feature):

- **OWASP ASVS** — primary standard. Security requirements and acceptance criteria are derived from its chapters (V1 Architecture, V2 Authentication, V3 Session Management, V4 Access Control, V5 Validation & Encoding, V6 Cryptography, V7 Error Handling & Logging, V8 Data Protection, V9 Communications, V10 Malicious Code, V11 Business Logic, V13 API & Web Services, V14 Configuration).
- **OWASP Top 10 (2021)** — verification mechanism. Every plan MUST complete an explicit A01–A10 impact review (see secure-design-review template).
- **OWASP API Security Top 10 (2023)** — mandatory for any feature exposing APIs (API1–API10).
- **OWASP Proactive Controls (C1–C10)** — secure implementation guidance for the implement phase.

**Security gate (blocking):** A feature MUST NOT be closed (converged) when any of the following holds:

- an unresolved critical vulnerability exists;
- a High-severity vulnerability exists without an approved, documented mitigation;
- required security test evidence is missing (SAST, secret scanning, dependency review, authorization tests, threat-model update).

Only an explicit, recorded risk acceptance by the project owner can override the gate, and it MUST be documented in the feature's security review checklist.

## [SECTION_2_NAME]
<!-- Example: Additional Constraints, Performance Standards, Data Governance, etc. -->

[SECTION_2_CONTENT]
<!-- Example: Technology stack requirements, compliance standards (GDPR, HIPAA, PCI-DSS), deployment policies, etc. -->

## [SECTION_3_NAME]
<!-- Example: Development Workflow, Review Process, Quality Gates, etc. -->

[SECTION_3_CONTENT]
<!-- Example: Code review requirements, testing gates, deployment approval process, etc. -->

## Governance
<!-- Example: Constitution supersedes all other practices; Amendments require documentation, approval, migration plan -->

[GOVERNANCE_RULES]
<!-- Example: All PRs/reviews must verify compliance; Security principles I–VI cannot be amended without a formal risk acceptance; Use [GUIDANCE_FILE] for runtime development guidance -->

**Version**: [CONSTITUTION_VERSION] | **Ratified**: [RATIFICATION_DATE] | **Last Amended**: [LAST_AMENDED_DATE]
<!-- Example: Version: 2.1.1 | Ratified: 2025-06-13 | Last Amended: 2025-07-16 -->
