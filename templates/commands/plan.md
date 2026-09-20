---
description: Execute the implementation planning workflow using the plan template to generate design artifacts.
handoffs:
  - label: Create Tasks
    agent: speckit.tasks
    prompt: Break the plan into tasks
    send: true
  - label: Create Checklist
    agent: speckit.checklist
    prompt: Create a checklist for the following domain...
scripts:
  sh: scripts/bash/setup-plan.sh --json
  ps: scripts/powershell/setup-plan.ps1 -Json
  py: scripts/python/setup_plan.py --json
---

## User Input

```text
$ARGUMENTS
```

You **MUST** consider the user input before proceeding (if not empty).

## Pre-Execution Checks

**Check for extension hooks (before planning)**:
- Check if `.specify/extensions.yml` exists in the project root.
- If it exists, read it and look for entries under the `hooks.before_plan` key
- If the YAML cannot be parsed or is invalid, do not skip silently: tell the user that `.specify/extensions.yml` could not be read (include the parser error) and that no hooks were checked, including any mandatory (`optional: false`) hooks registered there, then continue normally
- Filter out hooks where `enabled` is explicitly `false`. Treat hooks without an `enabled` field as enabled by default.
- For each remaining hook, do **not** attempt to interpret or evaluate hook `condition` expressions:
  - If the hook has no `condition` field, or it is null/empty, treat the hook as executable
  - If the hook defines a non-empty `condition`, skip the hook and leave condition evaluation to the HookExecutor implementation
- For each executable hook, output the following based on its `optional` flag:
  - **Optional hook** (`optional: true`):
    ```
    ## Extension Hooks

    **Optional Pre-Hook**: {extension}
    Command: `/{command}`
    Description: {description}

    Prompt: {prompt}
    To execute: `/{command}`
    ```
  - **Mandatory hook** (`optional: false`):
    ```
    ## Extension Hooks

    **Automatic Pre-Hook**: {extension}
    Executing: `/{command}`
    EXECUTE_COMMAND: {command}

    Wait for the result of the hook command before proceeding to the Outline.
    ```
    After emitting the block above you MUST actually invoke the hook and wait for it to finish before continuing. Run it the same way you would run the command yourself in this agent/session (the invocation may differ from the literal `{command}` id shown above, e.g. a skills-mode agent runs it as `/skill:speckit-...` or `$speckit-...`). Emitting the block alone does not run the hook.
- If no hooks are registered or `.specify/extensions.yml` does not exist, skip silently

## Outline

1. **Setup**: Run `{SCRIPT}` from repo root and parse JSON for FEATURE_SPEC, IMPL_PLAN, FEATURE_DIR, BRANCH. For single quotes in args like "I'm Groot", use escape syntax: e.g 'I'\''m Groot' (or double-quote if possible: "I'm Groot").

2. **Load context**: Read FEATURE_SPEC and `/memory/constitution.md`. Load IMPL_PLAN template (already copied). Also load the feature's security artifacts from the same feature directory: `security-requirements.md` and `threat-model.md` (created by `__SPECKIT_COMMAND_SPECIFY__`). If these are missing, the spec is incomplete under the Security by Design principle — ERROR with a message telling the user to re-run `__SPECKIT_COMMAND_SPECIFY__` (or run `__SPECKIT_COMMAND_CHECKLIST__` security review on the existing spec) before planning.

3. **Execute plan workflow**: Follow the structure in IMPL_PLAN template to:
   - Fill Technical Context (mark unknowns as "NEEDS CLARIFICATION"), including the mandatory Security Context summary
   - Fill Constitution Check section from constitution, including the Security principles gate (Principles I–VI)
   - Evaluate gates (ERROR if violations unjustified)
   - Phase 0: Generate research.md (resolve all NEEDS CLARIFICATION)
   - Phase 1: Generate data-model.md, contracts/, quickstart.md, and the security design review (secure-design-review.md + security checklists)
   - Re-evaluate Constitution Check post-design, including the security gates

## Mandatory Post-Execution Hooks

**You MUST complete this section before reporting completion to the user.**

Check if `.specify/extensions.yml` exists in the project root.
- If it does not exist, or no hooks are registered under `hooks.after_plan`, skip to the Completion Report.
- If it exists, read it and look for entries under the `hooks.after_plan` key.
- If the YAML cannot be parsed or is invalid, do not skip silently: tell the user that `.specify/extensions.yml` could not be read (include the parser error) and that no hooks were checked, including any mandatory (`optional: false`) hooks registered there, then continue to the Completion Report.
- Filter out hooks where `enabled` is explicitly `false`. Treat hooks without an `enabled` field as enabled by default.
- For each remaining hook, do **not** attempt to interpret or evaluate hook `condition` expressions:
  - If the hook has no `condition` field, or it is null/empty, treat the hook as executable
  - If the hook defines a non-empty `condition`, skip the hook and leave condition evaluation to the HookExecutor implementation
- For each executable hook, output the following based on its `optional` flag:
  - **Mandatory hook** (`optional: false`) — **You MUST emit `EXECUTE_COMMAND:` for each mandatory hook**:
    ```
    ## Extension Hooks

    **Automatic Hook**: {extension}
    Executing: `/{command}`
    EXECUTE_COMMAND: {command}
    ```
    After emitting the block above you MUST actually invoke the hook and wait for it to finish before continuing. Run it the same way you would run the command yourself in this agent/session (the invocation may differ from the literal `{command}` id shown above, e.g. a skills-mode agent runs it as `/skill:speckit-...` or `$speckit-...`). Emitting the block alone does not run the hook.
  - **Optional hook** (`optional: true`):
    ```
    ## Extension Hooks

    **Optional Hook**: {extension}
    Command: `/{command}`
    Description: {description}

    Prompt: {prompt}
    To execute: `/{command}`
    ```

## Completion Report

Command ends after Phase 1 design. Report branch, IMPL_PLAN path, and generated artifacts.

## Phases

### Phase 0: Outline & Research

1. **Extract unknowns from Technical Context** above:
   - For each NEEDS CLARIFICATION → research task
   - For each dependency → best practices task (including known-vulnerability posture of the dependency)
   - For each integration → patterns task (including secure integration patterns: authN, TLS, input validation of responses)
   - For each High/Critical threat in threat-model.md → mitigation research task

2. **Generate and dispatch research agents**:

   ```text
   For each unknown in Technical Context:
     Task: "Research {unknown} for {feature context}"
   For each technology choice:
     Task: "Find best practices for {tech} in {domain}"
   For each High/Critical threat:
     Task: "Research mitigations for {threat} applicable to {feature context}"
   ```

3. **Consolidate findings** in `research.md` using format:
   - Decision: [what was chosen]
   - Rationale: [why chosen]
   - Alternatives considered: [what else evaluated]
   - Security note (for security-relevant decisions): [how the choice preserves the security requirements]

**Output**: research.md with all NEEDS CLARIFICATION resolved

### Phase 1: Design & Contracts

**Prerequisites:** `research.md` complete

1. **Extract entities from feature spec** → `data-model.md`:
   - Entity name, fields, relationships
   - Validation rules from requirements
   - State transitions if applicable
   - Data classification per entity (from spec) and field-level sensitivity (which fields are Confidential/Restricted — they drive encryption, masking, and logging rules)

2. **Define interface contracts** (if project has external interfaces) → `/contracts/`:
   - Identify what interfaces the project exposes to users or other systems
   - Document the contract format appropriate for the project type
   - Examples: public APIs for libraries, command schemas for CLI tools, endpoints for web services, grammars for parsers, UI contracts for applications
   - Skip if project is purely internal (build scripts, one-off tools, etc.)
   - For every contract: document authN requirement, authZ rule (who may call it), rate limit, input validation, and error contract that never leaks internals

3. **Create quickstart validation guide** → `quickstart.md`:
   - Document runnable validation scenarios that prove the feature works end-to-end
   - Include prerequisites, setup commands, test/run commands, and expected outcomes
   - Use links or references to contracts and data model details instead of duplicating them
   - Do not include full implementation code, model/service/controller bodies, migrations, or complete test suites
   - Keep this artifact as a validation/run guide; implementation details belong in `tasks.md` and the implementation phase
   - Include at least one security validation scenario (e.g., verify an unauthorized request is rejected)

4. **Security design review (MANDATORY — Security by Design)**:
   - Resolve the active `secure-design-review` template through the Spec Kit preset/template resolution stack (equivalent to `specify preset resolve secure-design-review`; fall back to `.specify/templates/secure-design-review.md`)
   - Produce `secure-design-review.md` in the feature directory covering:
     - **Security Architecture Review**: validate architecture (trust boundaries enforced, least privilege per component, fail-secure defaults), dependencies (identified, no known Critical/High vulnerabilities, provenance/lockfile), external integrations (inventoried, destinations validated, responses treated as untrusted), and secrets management (Secret Manager, no secrets in artifacts, rotation designed)
     - **Security Controls Matrix**: one row per SR-###/SAC-### — requirement → control → implementation location → verification test (this is the traceability spine)
     - **OWASP Top 10 review (A01–A10)**: impact of this design per category and its mitigation; "no impact" requires justification
     - **OWASP API Security Top 10 (API1–API10)**: additional mandatory review when the feature exposes APIs
   - Fill the plan's Security Architecture Review, Security Controls Matrix, and OWASP Top 10 Review sections as summaries of secure-design-review.md
   - **Gate**: if any High/Critical threat from threat-model.md lacks a mitigation mapped in the controls matrix, ERROR — do not proceed to tasks
   - Generate verification checklists (leave all items unchecked — they are completed during implementation/convergence):
     - Resolve `owasp-asvs-checklist` and copy to `FEATURE_DIR/checklists/owasp-asvs.md`, selecting the ASVS chapters relevant to the feature (at minimum V1, V2/V3/V4/V5 as applicable, V7, V8)
     - If the feature exposes APIs: resolve `api-security-checklist` and copy to `FEATURE_DIR/checklists/api-security.md`

**Output**: data-model.md, /contracts/*, quickstart.md, secure-design-review.md, checklists/owasp-asvs.md (+ checklists/api-security.md for API features)

## Key rules

- Use absolute paths for filesystem operations; use project-relative paths for references in documentation
- ERROR on gate failures or unresolved clarifications
- ERROR if the spec lacks security sections (SR-###, data classification, threat model, SAC-###) — Security by Design is not optional
- ERROR if any High/Critical threat lacks a mapped mitigation in the Security Controls Matrix

## Done When

- [ ] Plan workflow executed and design artifacts generated
- [ ] secure-design-review.md generated with Security Architecture Review, Security Controls Matrix, and OWASP A01–A10 review complete
- [ ] checklists/owasp-asvs.md generated (and checklists/api-security.md for API features), items left unchecked for the implementation/converge phases
- [ ] Security gates evaluated with the Constitution Check (Principles I–VI)
- [ ] Extension hooks dispatched or skipped according to the rules in Mandatory Post-Execution Hooks above
- [ ] Completion reported to user with branch, plan path, and generated artifacts
