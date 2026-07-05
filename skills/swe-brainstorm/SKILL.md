---
name: swe-brainstorm
description: "Use this before any planned change — new features, bug fixes, refactoring, repository scaffolding, or high-level design. Clarifies intent and scopes the change before implementation begins."
disable-model-invocation: true
---

<goal>
Turn a vague idea or intent into an approved, scoped change with clear requirements — functional or non-functional — that an implementer or designer needs before starting work.
</goal>

<anti-goal>
- Architecture, components, schemas, APIs, libraries, or implementation details
- Implementation
</anti-goal>

<variables>
WORK_DIR: `.swe-work/`
DATE: current date as `YYYY-MM-DD`
SLUG: short kebab-case label for the change (e.g. `auth-refresh`, `migrate-config`)
CHANGE_DIR: `.swe-work/{{DATE}}-{{SLUG}}/`
PROPOSAL_FILE: `{CHANGE_DIR}/01-proposal.md`
</variables>

<workflow>

```python
def brainstorm_workflow():
  inspect_repository_context()

  while not change_is_sufficiently_scoped():
      ask_next_highest_value_question()
      wait_for_user_response()

  create_change_workspace()
  write_proposal_file()

  request_proposal_review()

  while user_requests_changes():
      revise_proposal_file()
      request_proposal_review()
```

**inspect_repository_context**
- read repository guidance such as AGENTS.md, .agents/, README, architecture docs, and relevant source code.
- identify existing product terminology, constraints, integration boundaries, patterns, and conventions.
- do not infer implementation decisions from incomplete evidence.

**change_is_sufficiently_scoped**
- problem or motivation is clear.
- in-scope and explicitly out-of-scope behaviour are defined.
- material constraints and quality attributes are known.
- success criteria are observable.
- for bug fixes: current and expected behaviour are described; reproduction is clear.
- for refactoring: externally observable behaviour that must be preserved is explicit.
- for scaffolding or high-level design: purpose, conventions, and structural boundaries are defined.
- high-impact unknowns are either resolved, recorded as assumptions, or explicitly deferred.

**ask_next_highest_value_question**
- Ask the smallest useful set of structured questions.
- Group only independent questions in one interaction; do not group questions where one answer changes the valid options or meaning of a later question.
- For dependent decisions, ask the highest-impact blocking question first, then continue only if needed.
- Prioritise questions that affect scope, user behaviour, risk, feasibility, or acceptance criteria.
- Provide 2–4 concrete options for each question unless the user must provide free-form data such as an error message, URL, stack trace, or reproduction detail.
- Use multi-select checkboxes when multiple answers can apply.
- Put the recommended option first and mark it `(Recommended)` when there is a sensible default.
- State concise reasoning for the recommendation.
- Do not ask questions that repository evidence already answers.
- tailor questions to the change type:
  - *feature*: who triggers it, what is the expected outcome, what are the edge cases?
  - *bug fix*: what is the current vs expected behaviour, can it be reproduced reliably, what is the blast radius?
  - *refactoring*: what behaviour must be preserved, what is the measurable improvement, is there existing test coverage?
  - *scaffolding / high-level design*: what is the purpose of the structure, what conventions are being established, what is deliberately excluded?

**write_proposal_file**
- write a `PROPOSAL_FILE` as per [template](proposal.template.md) using existing repository terminology, patterns, scope and convention for similar changes and user answers
- keep it concise: target under 120 lines; include only details needed to decide scope and acceptance

**request_proposal_review**
> "Change request `${CHANGE_DIR}` created. Proposal written to `${PROPOSAL_FILE}`. Please review it and let me know if you want to make any changes"

</workflow>

<constraints>
- ask only questions that materially affect the change.
- keep implementation and architecture choices out of scope, unless the user explicitly requests them.
- record unresolved material items as assumptions or open questions.
- keep the proposal file concise and focused, nobody wants to read a long, rambling document.
</constraints>

<checklist>
- the problem or motivation, desired outcome, and boundaries are clear.
- requirements and non-goals are explicit.
- success criteria are observable and testable.
- material constraints and quality attributes are captured.
- assumptions and deferred decisions are visible.
- no architecture or implementation details have been prematurely decided.
</checklist>