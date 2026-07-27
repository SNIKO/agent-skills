---
name: swe-plan2
description: "Combine high-level design and task breakdown into a single implementation plan. Use after the proposal (and context) is approved, as an alternative to running swe-design then swe-plan separately. Do not use before the proposal is approved."
disable-model-invocation: true
---

<goal>
Turn an approved proposal (and context) directly into an implementation plan: make the necessary design decisions, then decompose the resulting design into an ordered sequence of self-contained implementation tasks — each described by the mutations it makes, not by step-by-step instructions.
</goal>

<anti-goal>
- Do not implement any code.
- Do not produce a separate long-form design document with background, alternatives narrative, or verification-strategy prose — fold decisions directly into the plan.
- Do not prescribe private method bodies, local variable names, step-by-step instructions, or internal algorithms unless needed to satisfy a cross-task contract or a correctness constraint.
- Do not produce tasks so large they cannot be reviewed in isolation, or so small they cannot compile, pass tests, or be committed on their own.
- Do not write placeholder content — no "TBD", "TODO", "add appropriate error handling", or "similar to task N".
</anti-goal>

<variables>
WORK_DIR: `.swe-work/`
CHANGE_DIR: infer from user input (e.g. `.swe-work/{{DATE}}-{{SLUG}}/`)
PROPOSAL_FILE: infer from user input (e.g. `{CHANGE_DIR}/01-proposal.md`)
CONTEXT_FILE: `{CHANGE_DIR}/02-context.md`
REFERENCES_DIR: `{CHANGE_DIR}/references/`
PLAN_FILE: `{CHANGE_DIR}/03-plan.md`
</variables>

<workflow>

## Follow the following steps

```python
def plan_workflow():
  read_proposal(PROPOSAL_FILE)
  read_context(CONTEXT_FILE, REFERENCES_DIR)

  while has_unresolved_design_decisions():
      ask_next_highest_value_design_question()
      wait_for_user_response()

  do_design()
  identify_tasks()
  order_tasks()

  write_plan_file()

  request_plan_review()

  while user_requests_changes():
      revise_plan()
      request_plan_review()
```

## Steps Explained

**read_proposal**
- Read `PROPOSAL_FILE` to understand the scope of the change.

**read_context**
- Read `CONTEXT_FILE` if it exists and all files in `REFERENCES_DIR` relevant to this change.
- Use the context as the authoritative starting point and index.

**has_unresolved_design_decisions**
- A design decision is unresolved if: it has two or more viable options with meaningfully different tradeoffs, and no option is clearly better given the requirements, constraints, and repository context.
- Decisions with a clear winner — based on existing patterns, principles, stated constraints, or obvious fit — are made autonomously without asking.

**ask_next_highest_value_design_question**
- Ask the smallest useful set of structured questions.
- Group only independent questions in one interaction; do not group questions where one answer changes the valid options or meaning of a later question.
- For dependent decisions, ask the highest-impact blocking question first, then continue only if needed.
- Briefly explain why each question matters and what it affects.
- Present 2–3 concrete options with a short tradeoff for each. Put the recommended option first and mark it `(Recommended)` when there is a sensible default.
- Do not ask about decisions already answerable from the proposal or repository context.

**do_design**
- Follow <principles> and <scope> and think thoroughly about the design: module/file structure, ownership, cross-component contracts, data flow, and failure modes.
- Make sure components can actually see each other and that data flows are feasible given the repository structure and dependencies.
- For each component, estimate what its low-level implementation will need — data, upstream/downstream dependencies, error and state information, configuration — and ensure the mutations recorded per task expose all of it, so implementation can proceed without requiring a redesign.
- Keep the outcome of this step as working knowledge, not a deliverable: do not write a standalone design document. Its conclusions are expressed directly as the module/file structure diff and the per-task mutation details in `PLAN_FILE`.

**identify_tasks**
- Use repository conventions, existing module boundaries, test patterns, and external constraints when forming tasks.
- Break the design into the minimal set of coherent implementation tasks. Read <task_definition> for guidance on when to keep work in one task or split it into multiple tasks.
- Prefer vertical slices that deliver a complete capability over horizontal technical layers.
- Each task must leave the repository in a valid, compilable, testable state.

**order_tasks**
- Order tasks so each can build on completed, verified prior tasks.
- Prefer this dependency shape where applicable:
  1. Foundation / schema / migration
  2. Boundary contract / owned interface
  3. Integration or domain capability
  4. Public API or workflow
  5. UI / consumer
  6. Observability / rollout hardening
- Do not force this sequence when repository architecture or delivery risk suggests a different order.
- Minimize temporary scaffolding and speculative abstractions.

**write_plan_file**
- Write `PLAN_FILE` following the [plan template](plan.template.md).

**request_plan_review**
> "Plan written to `${PLAN_FILE}`. Please review it and let me know if you want changes. If it looks good, recommended next step: run `swe-worktree`."

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

<principles>

- Design boundaries and contracts so that local implementation defects are contained and cannot violate system-wide invariants, public contracts, data integrity, security boundaries, or integration guarantees.
- Prefer clear ownership of data, side effects, lifecycle transitions, and cross-module contracts. Every cross-module contract has a clear owner responsible for compatibility and change coordination.
- Do not expose internal domain models directly across boundaries when that would create unwanted coupling.
- A contract is only complete once it gives the implementer every input, dependency, and signal their component needs.

- Keep dependencies directional and avoid circular dependencies.
- Minimize shared mutable state; make ownership and mutation rules explicit where shared state is unavoidable.
- Isolate external I/O behind explicit clients, adapters, ports, or integration boundaries.
- Make failure modes, retries, idempotency, ordering, consistency, and observability explicit where relevant.

- Prefer the simplest structure that satisfies current requirements.
- Avoid abstractions without a current consumer, known variation point, or concrete maintenance problem.
- Avoid over-modularizing: a small cohesive file does not need to be split.

- Prefer tasks that deliver observable value or a stable dependency boundary over tasks that are technically convenient to implement.
- Do not create a task solely to introduce a stub, placeholder, or empty interface immediately overwritten by the next task.
- Test code belongs in the same task as the production code it tests, unless the tests require infrastructure introduced by a later task.
- Do not invent file paths, symbols, commands, APIs, or conventions without marking them as `(assumed)`.
- Include migrations, configuration, observability, feature flags, and rollback concerns in the task that requires them for safe completion.
- Do not create files outside `CHANGE_DIR`.

</principles>

<scope>

**In scope**
- repository structure, modules, folders, and files
- top-level components and their responsibilities
- cross-component data models, flows, and ownership
- public interfaces, APIs, and contracts between components
- DB schema, API endpoints, and DTOs
- task decomposition and sequencing

**Out of scope**
- private methods, helper functions, local class structure, internal data structures, and algorithms inside a component — unless necessary to satisfy a requirement, correctness property, security constraint, performance target, or integration contract
- step-by-step implementation instructions, verification checklists, and commit commands per task
- the implementation itself

</scope>

<checklist>

- [ ] Repository context and architecture understood
- [ ] External constraints identified (if applicable)
- [ ] Important design decisions include concise rationale or tradeoffs
- [ ] Plan satisfies all requirements and follows principles
- [ ] Components, boundaries, and interfaces clearly defined and aligned
- [ ] Public APIs and data contracts specified
- [ ] Each task's mutations give its implementer everything needed (data, dependencies, error/state signals) without requiring a redesign
- [ ] Every requirement and design element maps to at least one task
- [ ] Tasks are ordered by explicit dependencies
- [ ] Each task delivers a completed, coherent capability and leaves the repository in a valid, compilable state
- [ ] Module and file structure changes identified
- [ ] No new architecture or unstated design decision was introduced
- [ ] No placeholder content in the plan
- [ ] Type and name consistency verified across all tasks
- [ ] Plan document is ready for user review

</checklist>
