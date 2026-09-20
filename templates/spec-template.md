# Feature Specification: [FEATURE NAME]

**Feature Branch**: `[###-feature-name]`

**Created**: [DATE]

**Status**: Draft

**Input**: User description: "$ARGUMENTS"

## User Scenarios & Testing *(mandatory)*

<!--
  IMPORTANT: User stories should be PRIORITIZED as user journeys ordered by importance.
  Each user story/journey must be INDEPENDENTLY TESTABLE - meaning if you implement just ONE of them,
  you should still have a viable MVP (Minimum Viable Product) that delivers value.

  Assign priorities (P1, P2, P3, etc.) to each story, where P1 is the most critical.
  Think of each story as a standalone slice of functionality that can be:
  - Developed independently
  - Tested independently
  - Deployed independently
  - Demonstrated to users independently
-->

### User Story 1 - [Brief Title] (Priority: P1)

[Describe this user journey in plain language]

**Why this priority**: [Explain the value and why it has this priority level]

**Independent Test**: [Describe how this can be tested independently - e.g., "Can be fully tested by [specific action] and delivers [specific value]"]

**Acceptance Scenarios**:

1. **Given** [initial state], **When** [action], **Then** [expected outcome]
2. **Given** [initial state], **When** [action], **Then** [expected outcome]

---

### User Story 2 - [Brief Title] (Priority: P2)

[Describe this user journey in plain language]

**Why this priority**: [Explain the value and why it has this priority level]

**Independent Test**: [Describe how this can be tested independently]

**Acceptance Scenarios**:

1. **Given** [initial state], **When** [action], **Then** [expected outcome]

---

### User Story 3 - [Brief Title] (Priority: P3)

[Describe this user journey in plain language]

**Why this priority**: [Explain the value and why it has this priority level]

**Independent Test**: [Describe how this can be tested independently]

**Acceptance Scenarios**:

1. **Given** [initial state], **When** [action], **Then** [expected outcome]

---

[Add more user stories as needed, each with an assigned priority]

### Edge Cases

<!--
  ACTION REQUIRED: The content in this section represents placeholders.
  Fill them out with the right edge cases.
-->

- What happens when [boundary condition]?
- How does system handle [error scenario]?

## Requirements *(mandatory)*

<!--
  ACTION REQUIRED: The content in this section represents placeholders.
  Fill them out with the right functional requirements.
-->

### Functional Requirements

- **FR-001**: System MUST [specific capability, e.g., "allow users to create accounts"]
- **FR-002**: System MUST [specific capability, e.g., "validate email addresses"]
- **FR-003**: Users MUST be able to [key interaction, e.g., "reset their password"]
- **FR-004**: System MUST [data requirement, e.g., "persist user preferences"]
- **FR-005**: System MUST [behavior, e.g., "log all security events"]

*Example of marking unclear requirements:*

- **FR-006**: System MUST authenticate users via [NEEDS CLARIFICATION: auth method not specified - email/password, SSO, OAuth?]
- **FR-007**: System MUST retain user data for [NEEDS CLARIFICATION: retention period not specified]

### Key Entities *(include if feature involves data)*

- **[Entity 1]**: [What it represents, key attributes without implementation]
- **[Entity 2]**: [What it represents, relationships to other entities]

## Security Requirements *(mandatory)*

<!--
  ACTION REQUIRED: Security requirements are NOT optional. Derive them from
  OWASP ASVS. Every area below MUST be addressed: state the requirement, or
  explicitly justify why it does not apply (e.g., "N/A — feature has no
  authentication surface"). "Not specified" is not an acceptable answer.
  Use SR-### IDs for traceability to plan controls and verification tasks.
  The detailed working copy lives in security-requirements.md (from the
  security-requirements-template); this section is the specification summary.
-->

- **SR-001 Authentication**: [Who must prove identity and how strong it must be, e.g., "All administrative actions REQUIRE authenticated sessions; MFA for privileged operations"]
- **SR-002 Authorization**: [What each actor may access, deny-by-default, e.g., "Users can only access resources they own; admin roles checked server-side on every action"]
- **SR-003 Session Management**: [Session creation, expiration, rotation, invalidation rules, e.g., "Sessions expire after inactivity and are invalidated on logout/password change"]
- **SR-004 Data Protection**: [Confidentiality/integrity rules per data class, e.g., "Confidential and Restricted data encrypted at rest and in transit; sensitive fields never exposed in responses or logs"]
- **SR-005 Audit**: [Which security-relevant events are recorded immutably, e.g., "Authentication successes/failures, authorization denials, and privilege changes are audited with actor, action, target, timestamp"]
- **SR-006 Logging**: [What is logged for security events — and what must NEVER be logged (credentials, tokens, PII), e.g., "Security events logged with correlation IDs; secrets and sensitive payloads excluded"]
- **SR-007 Availability**: [Resilience expectations relevant to the feature, e.g., "The feature degrades gracefully under abnormal load; abusive clients are rate-limited"]
- **SR-008 Abuse Protection**: [Protections against misuse, e.g., "Rate limiting, input validation, and anti-automation controls on all public entry points"]

## Data Classification *(mandatory)*

<!--
  ACTION REQUIRED: Classify EVERY data entity the feature touches. The
  classification drives encryption, logging, retention, and access decisions
  (see threat model). One row per entity / data flow.
-->

| Entity / Data Flow | Classification | Justification | Handling Requirements |
|--------------------|----------------|---------------|----------------------|
| [e.g., User account record] | [Públic / Internal / Confidential / Restricted] | [Why this level] | [e.g., Encrypted at rest; never in logs; access logged] |
| [e.g., Public product catalog] | Public | [Non-sensitive reference data] | [Integrity controls only] |

**Classification levels**:

- **Públic / Public**: May be disclosed to anyone. Protect integrity only.
- **Interno / Internal**: Internal use only; disclosure causes limited harm. Access on need-to-know.
- **Confidencial / Confidential**: Disclosure causes significant harm (PII, financial data). Encrypted at rest and in transit; strict access control and audit.
- **Restringido / Restricted**: Severe regulatory/legal/financial impact (credentials, health data, keys). Strongest controls: encryption, minimal access, full audit, rotation.

## Threat Modeling *(mandatory)*

<!--
  ACTION REQUIRED: Summarize the feature threat model here using STRIDE.
  The full working model lives in threat-model.md (from the
  threat-model-template). Every identified risk with severity High or
  Critical MUST map to at least one Security Acceptance Criterion below.
-->

**Assets**: [What must be protected — data classes from above, credentials, availability of key flows]

**Actors**: [Who interacts with the feature — anonymous visitors, authenticated users, admins, external services, operators]

**Trust Boundaries**: [Where data/execution crosses a trust change — client→server, service→database, external API→system]

**STRIDE Threats**:

| ID | Threat | STRIDE Category | Asset | Severity | Mitigated By |
|----|--------|-----------------|-------|----------|--------------|
| TM-001 | [e.g., Attacker submits crafted input to bypass authorization] | Tampering / Elevation of Privilege | [asset] | [Critical/High/Medium/Low] | [SAC-### / control] |
| TM-002 | [e.g., Sensitive data exposed in API responses] | Information Disclosure | [asset] | [severity] | [SAC-###] |

## Security Acceptance Criteria *(mandatory)*

<!--
  ACTION REQUIRED: Security acceptance criteria are verifiable statements.
  Every feature MUST include at least the baseline criteria below (adapted to
  the feature) plus any derived from the threat model. They are checked by
  the Converge security gate before a feature can be closed.
-->

- **SAC-001**: All inputs are validated (type, length, format, range) before processing.
- **SAC-002**: No secrets (keys, tokens, passwords, connection strings) exist in source code, configuration templates, or tests.
- **SAC-003**: Authorization is enforced on every endpoint/operation, server-side, deny-by-default.
- **SAC-004**: Data classified Confidential or Restricted is encrypted at rest and in transit.
- **SAC-005**: Security-relevant events (authN success/failure, authZ denials, privilege changes) are logged without sensitive payloads.
- **SAC-006**: [Threat-model-derived criterion, e.g., "Repeated failed authentication attempts trigger temporary lockout (TM-003)"]
- **SAC-007**: [Feature-specific criterion, e.g., "Uploads are scanned and size/type-limited before storage"]

## Success Criteria *(mandatory)*

<!--
  ACTION REQUIRED: Define measurable success criteria.
  These must be technology-agnostic and measurable.
-->

### Measurable Outcomes

- **SC-001**: [Measurable metric, e.g., "Users can complete account creation in under 2 minutes"]
- **SC-002**: [Measurable metric, e.g., "System handles 1000 concurrent users without degradation"]
- **SC-003**: [User satisfaction metric, e.g., "90% of users successfully complete primary task on first attempt"]
- **SC-004**: [Business metric, e.g., "Reduce support tickets related to [X] by 50%"]

## Assumptions

<!--
  ACTION REQUIRED: The content in this section represents placeholders.
  Fill them out with the right assumptions based on reasonable defaults
  chosen when the feature description did not specify certain details.
-->

- [Assumption about target users, e.g., "Users have stable internet connectivity"]
- [Assumption about scope boundaries, e.g., "Mobile support is out of scope for v1"]
- [Assumption about data/environment, e.g., "Existing authentication system will be reused"]
- [Dependency on existing system/service, e.g., "Requires access to the existing user profile API"]
