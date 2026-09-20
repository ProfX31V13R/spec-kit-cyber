# Security by Design — Spec Kit Cyber Edition

This document describes the security transformation applied to this Spec Kit fork:
every control added, **why** it exists (justification), where it lives, which
standard backs it, and how to use it. The original SDD flow (Specify → Plan →
Tasks → Implement → Converge) is fully preserved — security is **injected into**
it as mandatory controls, never as a separate optional path.

**Design goals**

1. Preserve the original flow and its commands 1:1.
2. Security controls are **implicit and non-optional** at every phase; failures block.
3. Compatible with Web, APIs, microservices, mobile, and enterprise systems.
4. Stay compatible with future upstream Spec Kit updates (additive changes; see §8).

**Standards mapping**: OWASP ASVS (primary standard for requirements),
OWASP Top 10 2021 (verification mechanism), OWASP API Security Top 10 (APIs),
OWASP Proactive Controls (implementation), NIST SSDF (process alignment),
OWASP SAMM (practice framing).

---

## 1. One guided command: `/speckit-spec-new-project`

**File**: `templates/commands/spec-new-project.md` (installed as a core command —
`/speckit:spec-new-project`, `/speckit.spec-new-project`, or skills-mode
`/speckit-spec-new-project` depending on the agent).

**What it does**: runs the complete flow from a single user prompt:

```text
/speckit-spec-new-project Build a multi-tenant invoicing API with Stripe payments and an admin dashboard.
```

Phase 0 checks the constitution is ratified (security baseline present).
Phases 1–5 dispatch the existing commands — `specify`, `plan`, `tasks`,
`implement`, `converge` — verifying after each that its **security outputs**
exist, and presenting a mandatory **security summary at every user gate**
(approve / revise / stop). `--auto` skips pauses but never the blocking gates.

**Justification**: the biggest operational risk in spec-driven flows is
skipping steps. A single entry point that *always* includes the security
artifacts makes the secure path the default path (Secure by Default). The
orchestrator dispatches the real commands rather than duplicating them, so
extension hooks, scripts, and future upstream behavior are preserved.

**Reusing it mid-flight**: the command is resumable — if artifacts already
exist it offers to continue from the next phase.

## 2. Constitution — mandatory security principles

**File**: `templates/constitution-template.md` (pre-filled; projects ratify it).

| Principle | Justification | Standard |
|-----------|---------------|----------|
| I. Security by Design | Security decided *with* requirements, not after code — retrofitting costs 10–100× and leaves gaps; specs without SAC cannot advance | ASVS V1, NIST SSDF PS.1 |
| II. Least Privilege | Limits blast radius of any compromise; deny-by-default removes the "forgotten endpoint" class of bugs | ASVS V4, Zero Trust |
| III. Secure Default Configuration | Most breaches exploit defaults (debug on, wildcard CORS, default creds); shipping insecure-by-default transfers risk to users | ASVS V14, Top 10 A05 |
| IV. Defense in Depth | Single controls fail; independent layers make one failure non-fatal | ASVS V1.4 |
| V. Zero Trust | Internal traffic is attacker-reachable (lateral movement, compromised pod); every crossing authenticates | NIST 800-207 |
| VI. Traceability | An untraceable requirement cannot be verified; the SR→control→task→evidence chain makes gaps visible at converge | ASVS V1.8, SSDF PW.7 |

Plus a **Security Governance** section fixing the standards baseline and the
**blocking security gate**: no closure with critical vulns, unmitigated High
findings, or missing evidence (only a recorded risk acceptance can override).

## 3. Specify — security becomes requirements

**Files changed**: `templates/spec-template.md`, `templates/commands/specify.md`.
**New artifacts**: `security-requirements.md` (from
`templates/security-requirements-template.md`), `threat-model.md` (from
`templates/threat-model-template.md`) — both created in the feature directory
during Specify, resolved through the standard template stack (overridable by
presets/extensions like any other template).

| Added section | Content | Justification |
|---------------|---------|---------------|
| Security Requirements (SR-001..008) | authN, authZ, session mgmt, data protection, audit, logging, availability, abuse protection — each stated or justified N/A | Covers the full ASVS requirements surface; "8 areas" prevents the classic "we only did auth" spec |
| Data Classification | Public / Internal / Confidential / Restricted per entity + handling rules | Classification drives every downstream control (encryption, logging, access); makes sensitivity explicit and reviewable |
| Threat Modeling | Assets, actors, trust boundaries, STRIDE per boundary, severity, SAC mapping | STRIDE-per-boundary is systematic and repeatable; forces attackers' view before code exists (A04 Insecure Design) |
| Security Acceptance Criteria (SAC-###) | Verifiable statements: input validated, no secrets in code, authZ per endpoint, encryption, security logging + threat-derived criteria | Turns "secure" into binary, testable claims that Converge gates on |

The spec quality checklist gains a **Security section** — unchecked security
items block planning. Specify also applies **secure-by-default assumptions**
(deny-by-default, session expiry, rate limiting, secrets never in code) so the
user gets a secure baseline even without answering security questions.

**Justification for timing**: requirements-phase security is the cheapest place
to fix design flaws (A04) and the only place where *what* data matters can
decide *how* it must be handled.

## 4. Plan — security architecture & controls

**Files changed**: `templates/plan-template.md`, `templates/commands/plan.md`.
**New artifacts**: `secure-design-review.md` (from template), plus checklists
`checklists/owasp-asvs.md` and `checklists/api-security.md` copied into the
feature (items left unchecked for implementation/converge).

| Added control | Checks | Justification |
|---------------|--------|---------------|
| Security Architecture Review | architecture (boundaries enforced, least privilege per component, fail-secure), dependencies (versions, vulns, provenance/lockfile), external integrations (inventory, egress validation incl. rejecting localhost/loopback/private hosts, responses as untrusted input), secrets (manager, no secrets in artifacts, rotation) | These four surfaces are where designs introduce systemic risk: implicit trust, poisoned/sloppy dependencies, SSRF-prone egress, and leaked secrets (A05, A06, A08, A10) |
| Security Controls Matrix | one row per SR/SAC: requirement → control → implementation location → verification test | The traceability spine (Principle VI); makes "who implements this and how is it tested" explicit before task generation |
| OWASP Top 10 review | A01–A10 impact + mitigation per feature; N/A requires justification; API features add API1–API10 | Top 10 is the most actionable verification lens; forcing all ten prevents "we checked injection but not SSRF" |
| Security gate at plan | ERROR if any High/Critical threat lacks a mapped mitigation | An unmitigated High risk entering tasks becomes an implemented vulnerability |

Contracts and data-model now must state authN/authZ/validation/error contracts
per interface and field-level sensitivity — so tasks inherit them automatically.

## 5. Tasks — security work is real work

**Files changed**: `templates/tasks-template.md`, `templates/commands/tasks.md`.

Rules (enforced by the command): Setup phase configures SAST/dependency/secret
scanners and secret management; Foundational phase builds authN/authZ, security
middleware and secure logging; **every user story phase gets a mandatory
Security Tasks subsection** (authorization + its test matrix including
privilege-escalation cases, input validation + tests, security logging, secret/
dependency scans, story-specific controls citing SR/SAC/TM IDs); a feature-wide
**Security Verification & Hardening phase** runs before Polish (SAST, secrets,
dependencies, threat-model update, RBAC allow+deny matrix, logging auditability,
ASVS/API checklist completion, hardening, quickstart security scenario).

**Justification**: "a story isn't done until its security tasks are done"
removes the end-of-project security crunch; per-story authorization tests
encode negative testing (deny paths) which is chronically undertested; scanner
setup in Setup means evidence exists from day one. Citing SR/SAC/TM IDs in
task descriptions preserves traceability into tasks.md.

## 6. Implement & Converge — enforce and verify

**Implement** (`templates/commands/implement.md`) adds the **Security Rules**:
secure coding (validate all input; contextual output encoding; parameterized
queries only; server-side deny-by-default authZ on every operation; secure
error handling; modern crypto only; centralized controls), **secrets**
(never in code/config/tests; Secret Manager at runtime; rotation without code
changes; scan before reporting tasks complete), **dependencies** (pinned
lockfile; Critical/High stops and requires approved mitigation), and a
**per-phase security checkpoint** (no secrets, authZ on new endpoints,
validation on new inputs, safe logging — fix before proceeding, never defer).

**Converge** (`templates/commands/converge.md`) integrates security into its
existing append-only contract: the intent inventory now includes SR-###/SAC-###
and controls-matrix rows; a **Security Verification Gate (Step 5b)** checks
SAST (no Critical/High), secret scan (clean), dependencies (reviewed), threat
model (updated), logging (auditable), RBAC (validated). Gate failures become
CRITICAL/HIGH findings, forcing `tasks_appended` — the feature **cannot report
`converged`** while a critical vulnerability, an unmitigated High, or missing
evidence remains. `security-review-checklist.md` is the evidence sheet.

**Justification**: implementation-phase rules catch what design missed (defense
in depth); the converge gate is the single point where "is it actually secure?"
must be answered with evidence — matching the Constitution's blocking gate and
closing the traceability chain (spec → control → task → evidence).

## 7. New artifacts (summary)

| Artifact | Phase | Purpose |
|----------|-------|---------|
| `templates/security-requirements-template.md` | Specify | Detailed SR-### per ASVS chapters + traceability map |
| `templates/threat-model-template.md` | Specify | STRIDE per trust boundary + SAC traceability |
| `templates/secure-design-review.md` | Plan | Architecture/dependencies/integrations/secrets review + controls matrix + A01–A10 (+API1–API10) |
| `templates/security-review-checklist.md` | Tasks/Converge | Feature-wide verification evidence (SAST, secrets, deps, RBAC, logging, hardening) |
| `templates/owasp-asvs-checklist.md` | Plan→Converge | ASVS chapter-by-chapter verification |
| `templates/api-security-checklist.md` | Plan→Converge | OWASP API Security Top 10 verification |

All resolve through the standard template stack — presets and extensions can
override them exactly like `spec-template` (compatibility with your tooling).

## 8. Compatibility with upstream Spec Kit

- The five core commands keep their names, scripts, hooks, and flows; changes
  are **additive sections and rules** inside them.
- New templates live in `templates/` and are copied to `.specify/templates/` by
  the existing init/upgrade machinery; registered in `pyproject.toml`
  (`force-include`), `_CORE_COMMAND_TEMPLATE_ORDER`, and
  `_FALLBACK_CORE_COMMAND_NAMES` so wheel installs behave identically.
- The existing test suite passes (only pre-existing Windows symlink-privilege
  failures remain, identical to upstream).
- Merge strategy for future upstream updates: templates are markdown prompts;
  upstream edits to hook/script mechanics merge cleanly because security
  additions live in dedicated sections. If upstream adds phases, apply the same
  pattern: security inputs verified at entry, security outputs verified at exit,
  gate summary to the user.

## 9. Usage examples

**Guided single command (recommended)**

```text
/speckit-spec-new-project A REST API for expense reports with receipt upload, manager approval flows, and SSO login.
```

Gate 1 shows: 8 SR areas filled, classification (receipts = Confidential),
7 STRIDE threats (1 High: IDOR on approvals), 6 SAC. You approve or revise;
the flow continues to plan (with controls matrix + OWASP review), tasks
(security tasks per story), implementation, and converge (verification gate).

**Step-by-step (same security, phase by phase)**

```text
/speckit-specify Add password reset via email with expiring tokens.
/speckit-clarify
/speckit-plan Use Node.js with Express and PostgreSQL.
/speckit-tasks
/speckit-implement
/speckit-converge
```

Identical security enforcement — specify creates the threat model, plan gates
on mitigations, tasks embed security work, converge blocks on evidence.

**Mobile + API project**: same flow; the API checklist (API1–API10) is added
automatically because the feature exposes APIs; mobile storage/pinning
concerns surface through SR data-protection rules and the threat model's
device trust boundary.

**Microservices**: trust boundaries map service→service calls (Zero Trust
checks each), the controls matrix assigns authZ per service, dependency and
secret rules apply per repo/module.

## 10. Control justification index (quick map)

| Control | Fixes / prevents | Standard |
|---------|------------------|----------|
| SR 8 mandatory areas | Incomplete security specs | ASVS V2–V12 |
| Data classification | Mishandled sensitive data | A02, ASVS V8 |
| STRIDE threat model | Insecure design found too late | A04, SSDF PW.4 |
| SAC baseline (validation, secrets, authZ, crypto, logging) | Untestable "secure" claims | ASVS, Proactive Controls |
| Architecture review (4 surfaces) | Systemic design risk | A05/A06/A08/A10, ASVS V1/V14 |
| Controls matrix | Orphan requirements, untested controls | V1.8, SSDF PW.7 |
| OWASP A01–A10 + API1–API10 forced review | Category blind spots | Top 10 / API Top 10 |
| Per-story security tasks + negative tests | AuthZ undertesting, security crunch | A01, Proactive C7 |
| Scanner setup in Setup phase | Missing evidence | SSDF PW.8 |
| Secure coding rules | Injection, XSS, crypto misuse | A03, Proactive C3/C5/C8 |
| Secrets rules | Leaked credentials | A02/A07, Proactive C9 |
| Dependency rules | Vulnerable components | A06, A08 |
| Per-phase checkpoint | Drift within implementation | Defense in Depth |
| Converge blocking gate | Closing with known criticals | SSDF PW.7, governance |
| Traceability IDs everywhere | "Done" without evidence | V1.8, Principle VI |
