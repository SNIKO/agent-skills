---
name: brainstorm
description: "You MUST use this before any creative work - creating features, building components, adding functionality, or modifying behavior. Explores user intent, requirements and design before implementation."
disable-model-invocation: true
---

# Brainstorming Ideas Into Specs

<purpose>
Turn ideas into fully formed designs and specs through structured collaborative dialogue before any implementation begins.
</purpose>

<trigger>
Use this skill before any creative work: creating features, building components, adding functionality, or modifying behavior. Ignore for trivial tasks like fixing typos, updating documentation, or running commands.
</trigger>

<variables>
SPECS_DIR: infer from repository structure and rules, default to `docs/specs/` if not found
SPEC_FILE: infer from user preferences, default to `${SPECS_DIR}/YYYY-MM-DD-<topic>-spec.md`
</variables>

<workflow>

```python
def brainstorm_workflow():
  explore_project_context_with_subagents()

  while not idea_fully_understood():
    ask_clarifying_question()
    wait_for_user_response()
  
  write_high_level_design()
  while not user_approves_high_level_design():
    revise_high_level_design()

  if design_is_complex():
    write_detailed_design_in_parts()
    for part in design_parts:
      while not user_approves_part(part):
        revise_part(part)
  
  write_spec_doc()
  
  self_review_spec_doc()

  prompt_user_to_review_spec_doc()

  while not user_approves_spec():
    revise_spec_doc()
```

**explore_project_context_with_subagents**
- read source code, documentation, and any repository-specific rules (AGENTS.md, .agents/)

**idea_fully_understood**
- use your knowledge of the project
- use the user's responses to your clarifying questions
- check if you have enough information to propose a design and write a detailed spec

**ask_clarifying_question**
- ask one question at a time, focusing on purpose, constraints, and success criteria
- for each question, provide multiple choice options with your recommendation and reasoning

**write_high_level_design**
- write a high-level design covering architecture, component interactions, and data flow
- list each module or component affected and define the boundaries between them

**write_detailed_design_in_parts**
- break the design into sections scaled to their complexity, up to 200-300 words
- make sure that each section has one clear purpose, communicates through well-defined interfaces, and can be understood and tested independently
- for each section, answer: what does it do, how do you use it, what does it depend on?
- cover: architecture, components, key interfaces, key data structures, error handling, important implementation details, testing

**write_spec_doc**
- aggregate all approved design sections into a single spec document
- include: purpose, high-level design, detailed component designs, key interfaces, data structures, error handling, and testing approach
- save to `${SPEC_FILE}`

**self_review_spec_doc**
- no TBD, TODO, incomplete sections, or vague requirements.
- no sections contradict; architecture matches feature descriptions.
- focused enough for a single implementation plan.
- every requirement has exactly one interpretation.

**prompt_user_to_review_spec_doc**
> "Spec written and committed to `${SPEC_FILE}`. Please review it and let me know if you want to make any changes"

</workflow>

<constraints>
- one question per message — never ask multiple questions at once.
- apply YAGNI - remove unnecessary features, premature optimizations, unrealistic edge cases, and over-engineering.
- propose alternative solutions when the user's initial idea has a simpler or better-fitting approach given the project context.
</constraints>