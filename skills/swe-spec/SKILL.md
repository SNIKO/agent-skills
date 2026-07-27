---
name: swe-spec
description: "Create an implementation-ready low-level build specification from a brief and, when required, architecture. Use to define exact shared contracts, persistence, APIs, migrations, wiring, and deterministic verification before coding."
disable-model-invocation: true
---

# Purpose

Translate the selected intent and architecture into exact, repository-grounded changes at shared, persistent, externally visible, security-sensitive, and expensive-to-change seams. Produce `build-spec.md` while leaving private implementation choices flexible.

# Inputs and Workspace

Use the `brief.md` named by the user, or infer the current change only when unambiguous. Read `architecture.md` when the brief selected an architecture path or when the change alters boundaries, ownership, or cross-module data flow. Inspect relevant `spikes/*/spike.md` reports and saved artifacts, especially captured payloads, impact analyses, fixtures, and experiments that affect exact contracts.

Write `.swe-work/<change>/build-spec.md` using [build-spec.template.md](build-spec.template.md) only after all implementation-blocking contract decisions are resolved.

# Workflow

1. **Read authoritative inputs.** Read the selected brief, architecture when present, relevant spike reports and artifacts, repository guidance, and existing contracts relevant to the change. Treat spikes as evidence; the brief and architecture still own intent and design decisions.
2. **Inspect exact repository seams.** Verify existing paths, symbols, framework conventions, test commands, migration mechanisms, API patterns, configuration, and observability conventions. Delegate clear factual unknowns to a spike using the Research Delegation rules below rather than inventing details.
3. **Define the repository delta.** Identify exact contract, wiring, migration, configuration, documentation, and stable module locations that must be added, changed, or removed. Do not enumerate every private helper file.
4. **Specify shared contracts.** Define exact public or cross-module interfaces, routes, DTOs, events, serialization, errors, compatibility behaviour, and ownership where relevant.
5. **Specify persistence.** Define exact tables, columns, types, nullability, constraints, indexes, migrations, rollback or forward-recovery behaviour, transactions, and idempotency where relevant.
6. **Specify runtime obligations.** Resolve state transitions, validation, retries, timeouts, duplicate handling, partial failure, security checks, configuration, logging, metrics, and rollout mechanics that implementation must preserve.
7. **Define verification contracts.** Map each acceptance criterion and critical invariant to the highest-value deterministic boundary and repository command. Prefer behavioural, integration, contract, migration, and end-to-end checks over tests tied only to private structure.
8. **Check consistency.** Verify names and types across API, module, event, and persistence contracts. Ensure every architecture boundary has enough information to implement without changing that boundary.
9. **Complete or stop.** If an exact contract cannot be specified without changing intent, architecture, or missing evidence, report the gap and do not create or overwrite `build-spec.md`. Otherwise write the complete build spec.

# Decision Rules

## Required exactness

Be exact about any relevant:

- public API route, method, request, response, status, and error contract;
- cross-module interface, event, command, result, and ownership boundary;
- database table, column, type, constraint, index, migration, and compatibility rule;
- external integration request policy, payload interpretation, timeout, rate limit, and error mapping;
- configuration key, secret boundary, permission, feature flag, metric, and rollout condition;
- deterministic acceptance or invariant check.

## Deliberate flexibility

Do not freeze:

- private methods, local classes, helper functions, or variable names;
- internal algorithms unless correctness, performance, security, or interoperability depends on them;
- every implementation file when a stable module location is sufficient;
- unit-test organization unrelated to observable behaviour or a system seam.

New contract or wiring paths must be explicit. Private files may be listed as `expected` rather than mandatory when their exact shape is not a shared decision.

## Verification depth

- Use integration or contract tests for module and external seams.
- Use migration tests for schema and compatibility behaviour.
- Use end-to-end tests for critical user or system workflows.
- Use unit tests for dense rules or state transitions where they provide faster diagnosis.
- Put architecture restrictions into deterministic import, lint, type, schema, or CI checks when the repository supports them.

# Research Delegation

When an exact contract depends on unknown repository usage, external payloads, dependency behaviour, migration feasibility, or another observable fact:

- run a spike automatically when the question is clear, bounded, and safe;
- preserve probes, responses, impact maps, or experiments under the current change workspace;
- resume specification using the evidence, while keeping decisions in the build spec rather than the spike;
- ask the user before consequential requests or when choosing what to investigate requires changing intent or architecture;
- if research is inconclusive, do not finalize the build spec.

# Upstream Revision Rule

If exact design or spike evidence reveals an invalid brief or architecture assumption:

1. Stop specification work.
2. Identify the earliest incorrect artifact.
3. Explain the evidence and downstream impact.
4. Return to `swe-shape` or `swe-architect` as appropriate.
5. Do not create or overwrite `build-spec.md`; a present build spec should be implementation-ready, not a partial draft.
6. Do not compensate by silently changing the boundary inside the build spec.

# Constraints

- Do not repeat the brief or architecture summary. Link them and describe only the delta.
- Do not write an implementation sequence, task list, commit plan, or method-by-method coding instructions.
- Do not use `TBD`, vague error handling, assumed commands, or invented repository conventions.
- Keep sections conditional: omit API, persistence, events, rollout, or other sections when they do not apply.
- Resolve blockers before writing the artifact rather than embedding workflow state in it.
- Do not begin implementation.

# Output

When specification is complete, write `build-spec.md` and report:

- exact shared seams being changed;
- migration or compatibility impact;
- verification boundaries;
- whether `swe-plan` is useful or implementation can proceed as one slice.

When blocked, do not write the artifact; report the missing decision or evidence and the earliest upstream artifact that must change.

# Completion Criteria

Finish with exactly one outcome:

- **Build spec written:** Exact shared and persistent contracts are internally consistent and repository-grounded; acceptance criteria and critical invariants have deterministic verification; relevant migration, compatibility, failure, configuration, and operational behaviour are covered; private implementation remains flexible; and no unresolved blocker prevents implementation.
- **Returned upstream:** The missing decision or evidence, downstream impact, and earliest affected artifact are explicit, and `build-spec.md` was not created or overwritten.
