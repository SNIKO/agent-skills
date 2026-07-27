---
name: swe-execute
description: "Implement one build-spec or delivery-plan slice, run deterministic verification, and update execution state. Use when the user chooses to implement from `build-spec.md` or an optional `plan.md`."
disable-model-invocation: true
---

# Purpose

Implement one bounded slice in a clean, repository-aware context; verify it against the selected intent and contracts; record progress and discoveries; and stop rather than silently diverging from authoritative artifacts.

# Inputs and Workspace

Use the `build-spec.md` named by the user, or infer the current change only when unambiguous. When the user names a plan slice, execute that slice. Otherwise continue the current or next unblocked slice from `state.md` and `plan.md`; when no plan exists, treat the build spec as one slice.

Use `.swe-work/<change>/state.md`, creating it from [state.template.md](state.template.md) when implementation starts. Work in the user-selected branch or worktree and preserve unrelated changes.

# Workflow

1. **Resolve the slice.** Prefer the slice named by the user. Otherwise read `state.md` and `plan.md`, select the current or next unblocked slice, and confirm dependencies are complete. Do not repeat completed work unless the user requests rework.
2. **Load focused context.** Read the selected slice, its referenced build-spec sections and acceptance criteria, relevant architecture boundaries, repository guidance, relevant spike reports and saved artifacts, and the smallest useful source and tests. Treat spikes as evidence, not permission to override the build spec.
3. **Check workspace safety.** Inspect git status and current branch. Preserve unrelated changes. Ask before destructive, hard-to-reverse, externally visible, or production-affecting actions.
4. **Establish verification.** When practical, add or identify a behavioural check that fails for the missing behaviour before implementation. Do not force test-first work when the repository or change makes it artificial; always define the observable check before declaring completion.
5. **Implement the slice.** Follow exact shared contracts while choosing simple local implementation details consistent with repository conventions.
6. **Handle discoveries.** Classify deviations using Decision Rules. Continue through local implementation discoveries; stop on specification-impacting discoveries.
7. **Verify.** Run the slice's specified commands plus repository-required formatting, type, lint, migration, or focused regression checks. Record commands and outcomes truthfully.
8. **Review the diff.** Compare changed behaviour against referenced acceptance criteria, architecture boundaries, and build-spec contracts. Remove temporary files unless the user chose to retain them as evidence.
9. **Update state.** Record completed behaviour, verification, remaining slices, and any divergence. Do not mark the slice complete when required checks fail.
10. **Stop after one slice.** Recommend a fresh-context review or the next invocation rather than consuming the whole plan in one growing session.

# Decision Rules

## Local implementation discovery

Continue without upstream revision when a change affects only private implementation and preserves selected behaviour and contracts, for example:

- helper names or private file organization;
- a simpler internal algorithm with equivalent required properties;
- an additional private test utility;
- local refactoring needed to fit repository conventions.

Record it only when it materially helps the next session.

## Specification-impacting discovery

Stop affected implementation when code inspection, a relevant spike, or new runtime evidence requires changing any:

- acceptance criterion, scope, or product behaviour;
- component responsibility, ownership, dependency direction, or data flow;
- public API, cross-module interface, event, DTO, schema, migration, configuration, security rule, or compatibility guarantee;
- delivery dependency or operational safety assumption.

When the discovery is a clear, bounded factual unknown, run a safe spike under the current change workspace before deciding whether the specification is affected. Resume implementation only when the finding fits the existing contracts. If it contradicts them or remains inconclusive, report the spike, affected artifact, and partial work state, then stop. Do not silently edit both specification and code to agree with each other.

## Research during implementation

Run a spike automatically for bounded repository analysis, dependency behaviour, read-only external verification, or a disposable local experiment when it can resolve an implementation unknown without choosing new product behaviour or changing a shared contract. Ask first for consequential external requests or experiments. Keep research artifacts outside production source directories.

## Verification failure

- Fix failures caused by the current slice when they can be resolved within the build-spec contracts.
- If a failure exposes an upstream contradiction, classify it as a specification-impacting discovery.
- If an unrelated pre-existing failure is confirmed, record it separately with evidence; do not claim the slice passed that check.

# Constraints

- Implement one slice per invocation unless the user explicitly requests otherwise after seeing the first slice's result.
- Do not change shared contracts without upstream revision.
- Do not rewrite broad adjacent code, add speculative abstractions, or expand scope for cleanup.
- Do not let implementation-generated tests redefine acceptance criteria.
- Do not mark work complete based on model confidence; require deterministic evidence.
- Do not commit, push, deploy, migrate production data, or open a pull request unless the user explicitly requests it and the applicable repository workflow permits it.

# Output

Update `state.md`. Final response:

```markdown
## Slice result

- **Slice:** ...
- **Result:** Completed | Blocked | Verification failed
- **Changed:** <concise paths and behaviour>
- **Verification:** `<command>` — passed/failed
- **Divergence:** None | <summary and affected artifact>
- **Next:** Fresh review | Next slice | Revise `<artifact>`
```

# Completion Criteria

- Exactly one selected slice was implemented or stopped at a real blocker.
- Shared contracts and architecture boundaries remain intact.
- Required deterministic checks were run and recorded.
- The repository remains in a valid state or the failure is explicit.
- `state.md` truthfully reflects completed, remaining, blocked, and divergent work.
