---
name: create-plan
description: Use when you have a spec or requirements for a multi-step task, before touching code
---

# Writing Plans

<purpose>
Turn an approved spec into a detailed, bite-sized implementation plan that an engineer with zero domain context can execute task by task. DRY. YAGNI. TDD. Frequent commits.
</purpose>

<trigger>
Use this skill when a spec or requirements document is ready and the next step is implementation. Run the brainstorm skill first if no spec exists yet. Ignore for trivial tasks like fixing typos or running commands.
</trigger>

<variables>
PLANS_DIR: infer from repository structure and rules, default to `docs/plans/` if not found
PLAN_FILE: infer from user preferences, default to `${PLANS_DIR}/YYYY-MM-DD-<feature-name>-plan.md`
</variables>

<coding-rules>
Read `AGENTS.md` and `.agents/` for repository-specific coding and design rules. Repository rules override the defaults below where they conflict.

## Priority (on conflict)
**Clarity → Simplicity → Maintainability → Modularity → Testability → Performance**

---

## Clarity
- One function = one job. Name it after what it does, not how.
- No abbreviations unless universally known (`id`, `url`, `err` OK; `mgr`, `proc` NOT OK).
- Avoid nesting >2 levels deep. Use early returns to flatten.
- Don't comment what code does — rename until obvious. Comment only WHY.

## Simplicity
- Before adding abstraction: does this solve a problem that exists NOW?
- Inline single-use helpers. Extract only when logic repeats 3+ times or block exceeds ~20 lines.
- Prefer flat function over class when there is no shared state.
- Delete dead code. Never comment it out.

## Maintainability
- Each module has one reason to change. Bug fix touching 3+ files = design problem.
- No shared mutable state across modules. Pass data explicitly.
- Side effects at the edges only (I/O, network, DB). Keep business logic pure.

## Modularity
- No circular imports. If A imports B and B imports A → extract shared code to C.
- Cross-module imports go through `index.ts`. Never import internals of another module directly.
- One file answers one question. Don’t mix unrelated logic just to reduce file count.
- Over-modularizing is also a problem. A 50-line file doing one thing does not need splitting.

## Testability
- Test behavior, not implementation. Call the public API, not internal functions.
- Mock only external I/O (network, DB, clock). Never mock internals.
- Deterministic inputs → deterministic outputs. No hidden state, no global side effects in logic.
- Hard-to-test code = bad design. Fix the design, not the test.

## File Structure
- <50 -> Inline into caller
- 50–400 -> Keep as single file
- More than 400 -> Split by responsibility

Target: **150–300 lines per file.**
</coding-rules>

<workflow>

```python
def planning_workflow():
  announce("I'm using the create-plan skill to create the implementation plan.")

  scope_check()

  map_file_structure()

  write_plan_doc()

  self_review_plan_doc()

  save_plan_doc()
```

**scope_check**
- If the spec covers multiple independent subsystems, suggest breaking it into separate plans — one per subsystem
- Each plan should produce working, testable software on its own

**map_file_structure**
- Use <coding-rules> to define the file structure and task breakdown

**write_plan_doc**
- Write the full plan document following the structure in `<output>`
- Assume the engineer is skilled but knows almost nothing about the toolset or problem domain, and has questionable test design instincts — document everything
- Right-size tasks: the smallest unit that carries its own test cycle and is worth a reviewer's gate
  - Fold setup, configuration, scaffolding, and documentation into the task that needs them
  - Split only where a reviewer could reject one task while approving its neighbor
  - Each task ends with an independently testable deliverable
- Write every step at bite-sized granularity (2–5 minutes each):
  - "Write the failing test" — step
  - "Run it to make sure it fails" — step
  - "Write the minimal code to make it pass" — step
  - "Run the tests and make sure they pass" — step
  - "Commit" — step

**self_review_plan_doc**
This is a checklist you run yourself — not a subagent dispatch.

1. **Spec coverage:** Skim each requirement in the spec; point to a task that implements it; list any gaps and add tasks for them
2. **Placeholder scan:** Search for TBD, TODO, "implement later", "add appropriate error handling", "write tests for the above", "similar to Task N", steps without code blocks — fix every hit
3. **Type consistency:** Verify that types, method signatures, and property names used in later tasks match what was defined in earlier tasks (e.g. `clearLayers()` in Task 3 but `clearFullLayers()` in Task 7 is a bug)

**save_plan_doc**
- Save to `${PLAN_FILE}`

</workflow>

<constraints>
- Every step must contain the actual content the engineer needs — never write vague instructions
- Every code step must include the actual code; never write "implement as appropriate" or "add error handling"
- Every command step must include the exact command and expected output
- Never write "similar to Task N" — repeat the code; the engineer may read tasks out of order
- Never reference types, functions, or methods not defined in any task
- Always use exact file paths
- Apply YAGNI: no unnecessary features, premature optimizations, or over-engineering
</constraints>

<output>
Every plan document must follow this structure:

```markdown
# [Feature Name] Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** [One sentence describing what this builds]

**Architecture:** [2-3 sentences about approach]

**Tech Stack:** [Key technologies/libraries]

## Global Constraints

[Project-wide requirements — version floors, dependency limits, naming and copy rules, platform requirements — one line each, with exact values copied verbatim from the spec. Every task implicitly includes this section.]

---
```

Each task follows this structure:

````markdown
### Task N: [Component Name]

**Files:**
- Create: `exact/path/to/file.py`
- Modify: `exact/path/to/existing.py:123-145`
- Test: `tests/exact/path/to/test.py`

**Interfaces:**
- Consumes: [exact signatures from earlier tasks]
- Produces: [exact function names, parameter and return types relied on by later tasks — a task's implementer sees only their own task; this block is how they learn the names and types neighboring tasks use]

- [ ] **Step 1: Write the failing test**

```python
def test_specific_behavior():
    result = function(input)
    assert result == expected
```

- [ ] **Step 2: Run test to verify it fails**

Run: `pytest tests/path/test.py::test_name -v`
Expected: FAIL with "function not defined"

- [ ] **Step 3: Write minimal implementation**

```python
def function(input):
    return expected
```

- [ ] **Step 4: Run test to verify it passes**

Run: `pytest tests/path/test.py::test_name -v`
Expected: PASS

- [ ] **Step 5: Commit**

```bash
git add tests/path/test.py src/path/file.py
git commit -m "feat: add specific feature"
```
````
</output>