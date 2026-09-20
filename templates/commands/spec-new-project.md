---
description: Guided end-to-end feature development in ONE command - specify, plan, tasks, implement and converge with Security by Design built in (never optional). Describe your feature and approve each phase.
---

# Spec New Project — Guided Secure Feature Flow

## User Input

```text
$ARGUMENTS
```

You **MUST** consider the user input before proceeding (if not empty).
The text after the command is the feature description. If it is empty, ask the user:
"What do you want to build?" — and wait for the answer before starting Phase 0.

**Automation flag**: if `$ARGUMENTS` contains `--auto`, run the whole flow without
pausing at phase gates (report each phase summary as you go). Security gates remain
BLOCKING even in `--auto` mode. Without `--auto`, pause at every gate for user
approval.

## Purpose

Run the complete Spec-Driven Development lifecycle for one feature in a single
guided session:

```text
/spec-new-project <feature description>
        │
        ├─ Phase 0: Constitution readiness (security principles in place)
        ├─ Phase 1: Specify   → spec.md + security-requirements.md + threat-model.md
        ├─ GATE 1: User approves the spec (incl. security summary)
        ├─ Phase 2: Plan      → plan.md + secure-design-review.md + ASVS/API checklists
        ├─ GATE 2: User approves the plan (incl. security gate result)
        ├─ Phase 3: Tasks     → tasks.md (security tasks per story, mandatory)
        ├─ GATE 3: User approves tasks (incl. security task coverage)
        ├─ Phase 4: Implement → code with Secure Coding rules + security tasks
        ├─ Phase 5: Converge  → Security Verification Gate (blocking)
        └─ Final report: traceability summary SR/SAC → control → task → evidence
```

**Security is implicit and NON-OPTIONAL in every phase.** This orchestrator never
weakens, skips, or reorders the security steps that the underlying commands enforce
(Constitution principles I–VI). Each dispatched command also handles its own
extension hooks and scripts.

## Execution Rules

1. **Dispatch, don't duplicate**: execute each phase by invoking the corresponding
   command (`__SPECKIT_COMMAND_SPECIFY__`, `__SPECKIT_COMMAND_PLAN__`,
   `__SPECKIT_COMMAND_TASKS__`, `__SPECKIT_COMMAND_IMPLEMENT__`,
   `__SPECKIT_COMMAND_CONVERGE__`) with the stated arguments, and wait for it to
   finish. Run them the same way you would run any command in this agent/session.
   Do not re-implement their internals here.

2. **Resumable**: before each phase, check the feature directory (`.specify/feature.json`
   points to it). If the phase's output artifact already exists and the user did not
   ask to redo it, offer to resume from the next phase instead of regenerating.

3. **Gates are user-driven** (unless `--auto`): after each phase, present the phase
   summary and the GATE prompt below, then STOP and wait for the user's answer.
   - "approve" / "ok" / "yes" / "next" → continue to the next phase
   - "revise: <feedback>" → re-run the current phase's command with the feedback as
     additional arguments (max 3 revision rounds per phase, then surface the
     disagreement to the user)
   - "stop" / "abort" → stop cleanly, report progress so far and how to resume

4. **Security gates are always blocking**: if a phase reports a security failure
   (missing security sections, failed OWASP review, failed Security Verification
   Gate), the flow MUST NOT advance even in `--auto` mode. Present the failure,
   the required remediation, and the options (fix now via the phase's revise loop,
   or stop).

## Phase 0: Constitution Readiness

1. Check that `/memory/constitution.md` exists and is not an unfilled template
   (look for placeholder markers like `[PROJECT_NAME]` or `[PRINCIPLE_1_NAME]`).
2. If it is missing or unfilled: ask the user whether to set it up now.
   - If yes: invoke `__SPECKIT_COMMAND_CONSTITUTION__` to create/update it. The
     security baseline (principles I–VI: Security by Design, Least Privilege,
     Secure Default Configuration, Defense in Depth, Zero Trust, Traceability;
     Security Governance with OWASP standards and the blocking security gate)
     MUST be present — it ships pre-filled in the constitution template.
   - If no: STOP. The security flow cannot run without a ratified constitution.
3. Report: constitution status and the security baseline it enforces.

## Phase 1: Specify (with Security by Design)

1. Invoke `__SPECKIT_COMMAND_SPECIFY__` with the user's feature description as
   arguments.
2. Verify the phase outputs exist in the feature directory: `spec.md`,
   `security-requirements.md`, `threat-model.md`. If any security artifact is
   missing, that is a security failure — do not proceed (Execution Rule 4).

### GATE 1 — Spec Review

Present to the user:

- What the feature does (2-3 sentences)
- User stories with priorities
- **Security summary (mandatory)**: count of SR-### requirements, data
  classification overview (which entities are Confidential/Restricted), STRIDE
  threat summary by severity, count of SAC-### criteria
- Top 3 risks from the threat model
- Any [NEEDS CLARIFICATION] items

Prompt: "Approve the spec to continue with planning, revise it, or stop."

## Phase 2: Plan (with Security Architecture)

1. Invoke `__SPECKIT_COMMAND_PLAN__` with the feature description as arguments.
2. Verify outputs: `plan.md` (with Security Architecture Review, Security Controls
   Matrix, OWASP Top 10 Review sections), `secure-design-review.md`,
   `checklists/owasp-asvs.md` (+ `checklists/api-security.md` for API features).
   Missing security sections are a security failure (Execution Rule 4).

### GATE 2 — Plan Review

Present to the user:

- Architecture and stack in one paragraph
- Key contracts/data model highlights
- **Security summary (mandatory)**: controls matrix coverage (every SR/SAC has a
  control?), dependency validation result, secrets management approach, OWASP
  A01–A10 review status (any category addressed with "FAIL"?), secure-design-review
  gate result
- Unresolved risks

Prompt: "Approve the plan to generate tasks, revise it, or stop."

## Phase 3: Tasks (with mandatory security tasks)

1. Invoke `__SPECKIT_COMMAND_TASKS__` with the feature description as arguments.
2. Verify: every user story phase has a Security Tasks subsection, and the
  Security Verification & Hardening phase exists. Missing security tasks are a
  security failure (Execution Rule 4).

### GATE 3 — Task Plan Review

Present to the user:

- Total tasks and phases
- **Security summary (mandatory)**: security tasks per story (count), the
  Security Verification & Hardening phase tasks, traceability confirmation
  (every SR-###/SAC-### and High/Critical TM-### referenced by ≥1 task)
- Suggested MVP scope

Prompt: "Approve the tasks to start implementation, revise them, or stop."

## Phase 4: Implement (Secure Coding)

1. Invoke `__SPECKIT_COMMAND_IMPLEMENT__` with the feature description as arguments.
2. While it runs, it enforces the Security Rules (secure coding, secrets,
   dependencies, per-phase security checkpoints). Do not bypass them.

Report: completed tasks (functional + security), checkpoint results, any failures.

## Phase 5: Converge (Security Verification Gate)

1. Invoke `__SPECKIT_COMMAND_CONVERGE__`.
2. Read its outcome:
   - `tasks_appended`: present the appended convergence tasks (especially security
     gaps), then re-run Phase 4 → Phase 5 until either converged or the user stops.
     Each loop MUST show the remaining findings shrinking.
   - `converged`: the Security Verification Gate passed — present the final
     verification table (SAST, secrets, dependencies, threat model, logging, RBAC)
     as closing evidence.

**The security gate is BLOCKING**: converged status without passing security
verification must never be reported. If converge reported converged but the
verification table shows missing evidence, treat it as a security failure.

## Final Report

When the flow completes, report:

- Feature directory and all artifacts (spec, security-requirements, threat-model,
  plan, secure-design-review, contracts, tasks, checklists)
- **Traceability summary**: SR-###/SAC-### → control (matrix) → tasks → evidence
  (count of requirements fully traced; any gaps)
- Security Verification Gate result and evidence highlights
- Next steps: review the diff, open a PR, run `__SPECKIT_COMMAND_ANALYZE__` for
  consistency if desired

## Done When

- [ ] All phases executed via their commands (not re-implemented here)
- [ ] Every gate presented with its mandatory security summary and (unless --auto) user approval recorded
- [ ] No security failure was bypassed — blocking conditions either fixed or flow stopped with a clear report
- [ ] Final report delivered with the traceability summary
