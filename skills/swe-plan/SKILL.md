---
name: swe-plan
description: "Break an approved design into a sequenced, verifiable implementation plan. Use after swe-design produces an approved design. Do not use before the design is approved."
disable-model-invocation: true
---

<goal>
Decompose an approved design into an ordered sequence of self-contained implementation tasks.
Each task must be small enough to implement in a single focused session, independently verifiable, and safe to commit on its own.
The plan must give an implementer — one who is skilled but unfamiliar with this codebase — everything they need to write each task correctly without guessing.
</goal>

<anti-goal>
- Do not implement any code.
- Do not revise requirements or design.
- Do not prescribe private method bodies, local variable names, or internal algorithms unless they are needed to satisfy a verification step, a cross-task contract, or a correctness constraint.
- Do not produce tasks so large they cannot be reviewed in isolation.
- Do not produce tasks so small they cannot compile, pass tests, or be committed without the next task.
- Do not write placeholder content — no "TBD", "TODO", "add appropriate error handling", or "similar to task N".
</anti-goal>

<variables>
WORK_DIR: `.swe-work/`
CHANGE_DIR: infer from user input (e.g. `.swe-work/{{DATE}}-{{SLUG}}/`)
REQUIREMENTS_FILE: `{CHANGE_DIR}/01-requirements.md`
CONTEXT_FILE: `{CHANGE_DIR}/02-context.md`
DESIGN_FILE: `{CHANGE_DIR}/03-design.md`
PLAN_FILE: `{CHANGE_DIR}/04-plan.md`
</variables>

<workflow>

## Read Context

- Read `REQUIREMENTS_FILE`
- Read `CONTEXT_FILE` and relevant files in its `references/` directory.
- Read `DESIGN_FILE`

## Identify implementation tasks

- Use repository conventions, existing module boundaries, test patterns, and external constraints when forming tasks.
- Break the design into the minimal set of coherent implementation tasks.
- Prefer vertical slices that deliver a complete capability over horizontal technical layers.
- A task must leave the repository in a valid, compilable, testable state.

Read <task_definition> for guidance on when to keep work in one task or split it into multiple tasks.

## Order tasks by dependency and delivery value

- Order tasks so each can build on completed, verified prior tasks.
- Minimize temporary scaffolding and speculative abstractions.
- Prefer this dependency shape where applicable:
  1. Foundation / schema / migration
  2. Boundary contract / owned interface
  3. Integration or domain capability
  4. Public API or workflow
  5. UI / consumer
  6. Observability / rollout hardening
- Do not force this sequence when repository architecture or delivery risk suggests a different order.
- Explicitly record dependencies between tasks.

## Write the plan

Create `PLAN_FILE` using the format in `<plan_file_format>`

## Fix issues

Run the `<checklist>` section; revise the plan until all items pass.

</workflow>

<task_definition>

A plan task is a completed, verifiable, committable increment of functionality.

A task is complete only when:
- its intended behaviour exists,
- required tests or checks exist and pass,
- relevant contracts are satisfied,
- error handling and edge cases required by the design are included,
- documentation or configuration changes necessary for safe operation are included,
- the repository remains in a valid, compilable state after the task is applied.

A task may depend on previous tasks, but must not depend on undocumented future work for its own correctness.

**When to keep work in one task**
- The code, tests, and contract changes together implement one behaviour.
- Splitting would leave incomplete functionality or require fake scaffolding.
- The behaviour cannot be meaningfully verified until all changes are present.

**When to split work into multiple tasks**
- A migration or compatibility layer can be completed and verified independently.
- An external client can be contract-tested before consumers use it.
- A backend capability can be verified before a UI consumes it.
- A rollout or backfill has separate operational risk.
- A refactor is needed to safely enable later work and has its own testable outcome.

</task_definition>

<plan_file_format>

````markdown
# Implementation Plan: <title>

## Change overview
<!-- One paragraph summary of the problem, intended outcome, and scope. -->

## Implementation Overview
<!-- One paragraph summary of the implementation approach, major components, and how they fit together. -->

## Requirement Coverage
<!-- Table mapping requirements and design elements to the tasks that implement them. 
| Requirement / Design Element | Covered By |
|---|---|
| FR-01: ... | Task 1, Task 3 |
| FR-02: ... | Task 4 |
| Design § External Integration | Task 2 |
| Quality Attribute: rate limiting | Task 2 |-->

## Task N — <title>

**Goal**
One or two sentences describing the completed behaviour or capability this task delivers.

**Dependencies**
- None
- or: Task N — <title>
- External prerequisite: ...

**Files**
[create|modify|delete] `path/to/file` — <description of what changes>

- [ ] Step N: <title>
<!-- Describe the step in implementation-level detail. Include inputs, outputs, invariants, error handling, and edge cases. -->

- [ ] Step N+1: Verify
  
  - [ ] Observable behaviour 1
  - [ ] Observable behaviour 2
  - [ ] Required failure or error behaviour
  - [ ] Required compatibility behaviour
  - [ ] Required quality property

- [ ] Step N+2: Commit

  ```bash
  git add tests/path/test.py src/path/file.py
  git commit -m "feat: add specific feature"
  ```

````

</plan_file_format>

<principles>

- Prefer tasks that deliver observable value or a stable dependency boundary over tasks that are technically convenient to implement.
- Do not create a task solely to introduce a stub, placeholder, or empty interface immediately overwritten by the next task.
- Each task's verification steps must be runnable against the repository after that task alone is applied.
- Cross-task contracts (types, interface signatures, event schemas) belong in the task that defines them, not repeated in each consumer.
- Test code belongs in the same task as the production code it tests, unless the tests require infrastructure introduced by a later task.
- Do not invent file paths, symbols, commands, APIs, or conventions without marking them as `(assumed)`.
- Include migrations, configuration, observability, feature flags, and rollback steps in the task that requires them for safe completion.
- Do not create files outside `CHANGE_DIR`.

</principles>

<checklist>

- [ ] Requirements, context, and design were read
- [ ] Requirements and design are approved or explicitly ready for planning
- [ ] Scope check performed; subsystem split suggested if warranted
- [ ] Every requirement and design element maps to at least one task
- [ ] Tasks are ordered by explicit dependencies
- [ ] Each task delivers a completed, coherent capability
- [ ] Each task has exact repository locations or clearly marked `(assumed)` paths
- [ ] Each task has implementation details sufficient for coding without guessing
- [ ] Each task has acceptance criteria and verification commands
- [ ] Each task covers failure behaviour and edge cases where required
- [ ] Each task is safe to review, compile, and commit independently
- [ ] Migration, rollout, observability, and rollback concerns are included where relevant
- [ ] No new architecture or unstated design decision was introduced
- [ ] No placeholder content in the plan
- [ ] Type and name consistency verified across all tasks
- [ ] User reviewed and approved the plan

</checklist>
