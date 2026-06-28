---
name: swe-brainstorm
description: "You MUST use this before any creative work - creating features, building components, adding functionality, or modifying behavior. Explores user intent and requirements before implementation."
disable-model-invocation: true
---

<goal>
Turn a vague user idea into an approved, scoped change with clear behavioural requirements, non-goals, constraints, assumptions, and success criteria.
</goal>

<anti-goal>
- Do not choose architecture, components, schemas, APIs, libraries, or implementation details.
- Do not begin implementation.
- Do not silently invent unresolved product or technical constraints.
- Do not create a change workspace until the idea is sufficiently scoped.
</anti-goal>

<variables>
WORK_DIR: `.swe-work/`
DATE: current date as `YYYY-MM-DD`
SLUG: short kebab-case label for the change (e.g. `auth-refresh`, `migrate-config`)
CHANGE_DIR: `.swe-work/{{DATE}}-{{SLUG}}/`
REQUIREMENTS_FILE: `{CHANGE_DIR}/01-requirements.md`
</variables>

<workflow>

```python
def brainstorm_workflow():
  inspect_repository_context()

  if change_depends_on_external_system_or_standard():
      inspect_external_constraints()

  while not change_is_sufficiently_scoped():
      ask_next_highest_value_question()
      wait_for_user_response()

  create_change_workspace()
  write_draft_requirements_file()

  request_requirements_review()

  while not user_approves_requirements():
      revise_requirements_file()
      request_requirements_review()

  mark_requirements_approved()
```

**inspect_repository_context**
- read repository guidance such as AGENTS.md, .agents/, README, architecture docs, and relevant source code.
- identify existing product terminology, constraints, integration boundaries, and conventions.
- do not infer implementation decisions from incomplete evidence.

**inspect_external_constraints**
- only for relevant external APIs, standards, protocols, compliance obligations, or vendor systems.
- capture externally imposed constraints, capabilities, limits, and terminology.
- do not select implementation architecture or libraries.

**change_is_sufficiently_scoped**
- problem and intended outcome are clear.
- in-scope and explicitly out-of-scope behaviour are defined.
- material constraints and quality attributes are known.
- success criteria are observable.
- high-impact unknowns are either resolved, recorded as assumptions, or explicitly deferred.

**ask_next_highest_value_question**
- ask one decision-oriented question per message.
- prioritise questions that affect scope, user behaviour, risk, feasibility, or acceptance criteria.
- provide 2–4 options where useful.
- state a recommendation and concise reasoning.
- do not ask questions that repository evidence already answers.

**write_draft_requirements_file**
- write a `REQUIREMENTS_FILE` as per [template](requirements.template.md)
- include: problem, goals, non-goals, user flows, functional requirements, constraints, quality attributes, assumptions, open questions, and acceptance criteria.

**prompt_user_to_review_requirements_file**
> "Change request `${CHANGE_DIR}` created. Requirements written to `${REQUIREMENTS_FILE}`. Please review it and let me know if you want to make any changes"

</workflow>

<constraints>
- ask only questions that materially affect the change.
- keep implementation and architecture choices for `swe-design`.
- record unresolved material items as assumptions or open questions.
- do not proceed to `swe-design` until requirements are approved.
</constraints>

<checklist>
- the problem, desired outcome, and boundaries are clear.
- functional requirements and non-goals are explicit.
- success criteria are observable and testable at a behavioural level.
- material constraints and quality attributes are captured.
- assumptions and deferred decisions are visible.
- no architecture or implementation details have been prematurely decided.
</checklist>