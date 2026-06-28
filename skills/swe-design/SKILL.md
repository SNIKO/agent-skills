---
name: swe-design
description: "Create design for a change. Use it after requirements are approved. Do not use for trivial edits like fixing typos, formatting, or running commands."
disable-model-invocation: true
---

<goal>
Turn user requirements into a clear and structured high level design.
Focus on components, modules and boundaries between them, public interfaces, APIs, schemas, DTOs, events, and data flows.
</goal>

<anti-goal>
- Do not prescribe private methods, helper functions, local class structure, or low-level algorithms unless they are necessary to satisfy a requirement, correctness property, security constraint, performance target, or integration contract.
- Do not begin implementation.
</anti-goal>

<variables>
WORK_DIR: `.swe-work/`
CHANGE_DIR: infer from user input (e.g. `.swe-work/{{DATE}}-{{SLUG}}/`)
REQUIREMENTS_FILE: infer from user input (e.g. `{CHANGE_DIR}/01-requirements.md`)
CONTEXT_FILE: `{CHANGE_DIR}/02-context.md`
REFERENCES_DIR: `{CHANGE_DIR}/references/`
DESIGN_FILE: `{CHANGE_DIR}/03-design.md`
</variables>

<workflow>

## Follow the following steps

```python
def design_workflow():
  read_requirements(REQUIREMENTS_FILE)
  read_context(CONTEXT_FILE, REFERENCES_DIR)

  propose_a_few_approaches_to_design()

  while not user_approves_design():
      revise_design()      
  
  write_draft_design_document()

  request_design_review()

  while not user_approves_design():
      revise_design()
      request_design_review()

  mark_design_approved()
```

## Steps Explained

**read_requirements**
- Read `REQUIREMENTS_FILE` to understand the scope of the change.

**read_context**
- Read `CONTEXT_FILE` if it exists and all files in `REFERENCES_DIR` relevant to this change.
- Use the context as the authoritative starting point and index

**propose_a_few_approaches_to_design**
- use repository design rules and design principles below (repository rules override these if they conflict) and build 2-3 alternative approaches to design that satisfy the requirements and constraints.
- for each approach, state a recommendation and concise reasoning
- it doesn't have to be a complete design, just enough to show the user the tradeoffs and give your direction for the final design.
- if design is pretty straightforward, you can propose a single approach and ask the user to approve it.

**write_draft_requirements_file**
- write a `DESIGN_FILE` as per principles below

**request_design_review**
> "Design written to `${DESIGN_FILE}`. Please review it and let me know if you want to make any changes"

</workflow>

<principles>

- Focus on module boundaries, ownership, public interfaces, APIs, schemas, DTOs, events, and cross-boundary data flows.
- Design boundaries and contracts so that local implementation defects are contained and cannot violate system-wide invariants, public contracts, data integrity, security boundaries, or integration guarantees.
- Preserve implementation freedom inside a module, provided its externally visible contract and required quality properties are met.
- Internal structure is intentionally left to implementation, except where it affects correctness, performance, security, reliability, or an external contract.

- Prefer clear ownership of data, side effects, lifecycle transitions, and cross-module contracts.
- Every cross-module contract has a clear owner responsible for compatibility and change coordination.
- Define stable contracts at module and integration boundaries.
- Do not expose internal domain models directly across boundaries when that would create unwanted coupling.

- Keep dependencies directional and avoid circular dependencies.
- Minimize shared mutable state; make ownership and mutation rules explicit where shared state is unavoidable.
- Isolate external I/O behind explicit clients, adapters, ports, or integration boundaries.

- Make failure modes, retries, idempotency, ordering, consistency, and observability explicit where relevant.
- Design for testability through public contracts and controlled external dependencies.

- Prefer the simplest structure that satisfies current requirements.
- Avoid abstractions without a current consumer, known variation point, or concrete maintenance problem.
- Give each module a coherent responsibility and a clear reason to change.
- Avoid mixing unrelated responsibilities in the same module merely to reduce file count.
- Avoid over-modularizing: a small cohesive file does not need to be split.
- Repeated coordinated changes across unrelated modules may indicate a boundary or ownership problem; investigate before adding more coupling.

</principles>

<constraints>

- do not do any implementation
- do not do low level design, internal implementation, or internal data flow inside a module

</constraints>

<checklist>

- [ ] Repository context and architecture understood
- [ ] External constraints identified (if applicable)
- [ ] Multiple design approaches proposed with tradeoffs
- [ ] Design satisfies all requirements and constraints
- [ ] Components, boundaries, and interfaces clearly defined and aligned
- [ ] Public APIs and data contracts specified
- [ ] Data flows and event schemas documented
- [ ] Design review completed
- [ ] Design document approved and marked as final

</checklist>