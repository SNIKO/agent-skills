---
name: swe-design
description: "Create design for a change. Use it after the proposal is approved. Do not use for trivial edits like fixing typos, formatting, or running commands."
disable-model-invocation: true
---

<goal>
Turn the approved proposal into a concise, easy-to-review high-level design document.
Focus on structural changes to the repository, component responsibilities and data ownership, cross-component data flows, and the contracts/boundaries between components.
</goal>

<anti-goal>
- Do not prescribe private methods, helper functions, local class structure, or low-level algorithms unless they are necessary to satisfy a requirement, correctness property, security constraint, performance target, or integration contract.
- Do not begin implementation.
</anti-goal>

<variables>
WORK_DIR: `.swe-work/`
CHANGE_DIR: infer from user input (e.g. `.swe-work/{{DATE}}-{{SLUG}}/`)
PROPOSAL_FILE: infer from user input (e.g. `{CHANGE_DIR}/01-proposal.md`)
CONTEXT_FILE: `{CHANGE_DIR}/02-context.md`
REFERENCES_DIR: `{CHANGE_DIR}/references/`
DESIGN_FILE: `{CHANGE_DIR}/03-design.md`
</variables>

<workflow>

## Follow the following steps

```python
def design_workflow():
  read_proposal(PROPOSAL_FILE)
  read_context(CONTEXT_FILE, REFERENCES_DIR)

  while has_unresolved_design_decisions():
      ask_next_highest_value_design_question()
      wait_for_user_response()

  write_draft_design_file()

  request_design_review()

  while user_requests_changes():
      revise_design()
      request_design_review()
```

## Steps Explained

**read_proposal**
- Read `PROPOSAL_FILE` to understand the scope of the change.

**read_context**
- Read `CONTEXT_FILE` if it exists and all files in `REFERENCES_DIR` relevant to this change.
- Use the context as the authoritative starting point and index

**has_unresolved_design_decisions**
- a design decision is unresolved if: it has two or more viable options with meaningfully different tradeoffs, and no option is clearly better given the requirements, constraints, and repository context.
- decisions with a clear winner — based on existing patterns, principles, stated constraints, or obvious fit — are made autonomously without asking.

**ask_next_highest_value_design_question**
- Ask the smallest useful set of structured questions.
- Group only independent questions in one interaction; do not group questions where one answer changes the valid options or meaning of a later question.
- For dependent decisions, ask the highest-impact blocking question first, then continue only if needed.
- Briefly explain why each question matters and what it affects.
- Present 2–3 concrete options with a short tradeoff for each.
- Put the recommended option first and mark it `(Recommended)` when there is a sensible default.
- Do not ask about decisions already answerable from the proposal or repository context.

**write_draft_design_file**
- write a `DESIGN_FILE` following the [design template](design.template.md)
- fill in all four sections: repo structure changes, components, data flow, and contracts
- keep it concise: target under 150 lines; prefer tables, annotated trees, and short bullets over prose
- include only decisions and tradeoffs a reviewer must understand; avoid RFC-style background
- omit sections that do not apply to the change (e.g. no structural changes for a pure logic fix)

**request_design_review**
> "Design written to `${DESIGN_FILE}`. Please review it and let me know if you want changes. If it looks good, recommended next step: run `swe-plan`."

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
- [ ] Important design decisions include concise rationale or tradeoffs
- [ ] Design satisfies all requirements and constraints
- [ ] Components, boundaries, and interfaces clearly defined and aligned
- [ ] Public APIs and data contracts specified
- [ ] Data flows and event schemas documented
- [ ] Design review completed
- [ ] Design document is ready for user review

</checklist>