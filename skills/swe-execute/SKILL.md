---
name: swe-execute
description: "Implement a spec or delivery plan by dispatching each slice to a fresh sub-agent, verifying the result, and maintaining execution state. Use when the user chooses to implement from `spec.html` or an optional `plan.md`."
disable-model-invocation: true
---

# Pipeline

`swe-shape` → `swe-spec` → [`swe-plan`] → [`swe-worktree`] → `swe-execute` → `swe-review`, with `swe-research` available at any point.

Each stage runs in its own session: the user invokes one skill, reviews the result, clears the context, then invokes the next. Assume no memory of other stages beyond the files named below.

- **This stage:** implement the remaining slices, one fresh sub-agent per slice, and verify each.
- **Reads:** `spec.html` (the approved design and contracts, written by `swe-spec`), `brief.md` acceptance criteria (written by `swe-shape`), `plan.md` when one exists (slice ordering, written by `swe-plan`), and `state.md` (progress from earlier execution sessions).
- **Writes:** `state.md`. Sub-agents write production code and tests.
- **Next:** the user runs `swe-review` in a fresh session.

# Purpose

Act as the execution orchestrator: select slices in order, brief a fresh sub-agent for each one, confirm its work against the authoritative artifacts, record progress, and stop the run rather than let any slice silently diverge from the spec.

Your own context stays small and durable. Implementation context is disposable and lives in the sub-agents.

# Inputs and Workspace

- `CHANGE_DIR`: `.swe/<change>/` — infer only when unambiguous.
- `SPEC_FILE`: `{CHANGE_DIR}/spec.html` — canonical contract, at whatever depth each section reached. Read its table of contents and load only the sections a slice touches.
- `{CHANGE_DIR}/plan.md` — slice list and order when it exists; otherwise treat the spec as one slice.
- `{CHANGE_DIR}/state.md` — created from [state.template.md](state.template.md) when implementation starts. You are its only writer.
- `RESEARCH_DIR`: `.swe/research/` — shared corpus, indexed by `INDEX.md`.

Work in the user-selected branch or worktree and preserve unrelated changes.

# Delegation Model

One sub-agent per slice, with a fresh context, dispatched sequentially. Never run two slices concurrently: they share a working tree.

**You do:** read upstream artifacts, choose the slice, write the sub-agent brief, inspect the resulting diff, run or re-run verification, classify discoveries, update `state.md`, decide whether the run continues.

**The sub-agent does:** read the referenced spec sections and code, implement that one slice, add or update its tests, run the slice's checks, and report back. It writes nothing outside production code and tests.

**Sub-agent brief** — self-contained, since it inherits none of your context:

```markdown
Implement one slice of an approved specification. Do not exceed it.

- Change dir: `{CHANGE_DIR}`
- Spec: `{SPEC_FILE}` — read sections <ids> only
- Acceptance criteria: `brief.md` — <criteria ids>
- Slice: <name and required observable outcome, from plan.md or the spec>
- Research: <report paths, or none> — evidence only; it never overrides the spec
- Repository guidance: <paths, e.g. AGENTS.md/CLAUDE.md, conventions, test layout>
- Verify with: <commands>

Rules:
- Establish the failing behavioural check first when it is not artificial.
- Follow shared contracts exactly; choose local implementation details freely.
- Do not touch other slices, `.swe/` artifacts, or unrelated code.
- Stop and report instead of changing any acceptance criterion, interface, schema, migration, configuration, or compatibility guarantee.
- Report: files changed, behaviour delivered, commands run with real outcomes, anything unresolved.
```

If the harness offers no sub-agent capability, say so, implement one slice yourself, then stop and ask the user to clear the context before the next slice.

# Workflow

1. **Orient once.** Read `state.md`, `plan.md`, and the `spec.html` table of contents. Build the ordered list of remaining slices and confirm dependencies. Do not redo completed work unless the user asks for rework.
2. **Check workspace safety.** Inspect git status and branch. Preserve unrelated changes.
3. **For each remaining slice, in order:**
   1. **Brief.** Resolve the spec sections, acceptance criteria, research reports, repository guidance, and verification commands for that slice, and dispatch a fresh sub-agent with the brief above.
   2. **Verify independently.** Treat the sub-agent's report as a claim, not evidence. Run the slice's verification commands plus repository-required format, type, lint, migration, or focused regression checks yourself, and read the diff.
   3. **Review the diff against intent.** Compare delivered behaviour with the referenced acceptance criteria and contracts. Reject scope creep, speculative abstraction, weakened tests, and edits outside the slice; re-dispatch a corrective sub-agent with the specific defect named, or fix a trivial deviation directly.
   4. **Classify discoveries.** Apply Decision Rules. Continue on local implementation discoveries; halt the run on specification-impacting ones.
   5. **Record.** Update `state.md` with completed behaviour, verification commands and outcomes, remaining slices, and any divergence. Never mark a slice complete while a required check fails.
4. **Stop the run** when all slices are complete, a slice is blocked, verification fails and cannot be fixed within the contracts, or a specification-impacting discovery appears. Report and hand back to the user; recommend `swe-review` in a fresh session.

# Decision Rules

## Local implementation discovery

Continue when the change affects only private implementation and preserves selected behaviour and contracts — helper names, private file organization, an equivalent internal algorithm, a private test utility, or local refactoring to match repository conventions. Record it only when the next session needs it.

## Specification-impacting discovery

Halt the affected slice, and the run, when code inspection, research, or runtime evidence requires changing any:

- acceptance criterion, scope, or product behaviour;
- component responsibility, ownership, dependency direction, or data flow;
- public API, cross-module interface, event, DTO, schema, migration, configuration, security rule, or compatibility guarantee;
- delivery dependency or operational safety assumption.

When the discovery is a clear, bounded factual unknown, run `swe-research` first. Resume only if the finding fits the existing contracts. If it contradicts them or stays inconclusive, record the research, affected artifact, and partial work state in `state.md`, then stop. Do not edit specification and code into agreement.

## Research during implementation

Check `RESEARCH_DIR/INDEX.md`, then delegate `swe-research` for bounded repository analysis, dependency behaviour, read-only external verification, or a disposable experiment — never to choose new product behaviour or change a shared contract. Keep artifacts in the corpus, outside production source.

## Verification failure

- Re-dispatch or fix failures caused by the current slice when they are resolvable within the contracts.
- Treat a failure that exposes an upstream contradiction as a specification-impacting discovery.
- Record a confirmed pre-existing unrelated failure separately with evidence; never claim the slice passed that check.

# Autonomy

Dispatch slice sub-agents, delegate bounded research, and run build, test, lint, type, format, and focused regression commands without asking.

Ask first before committing, pushing, deploying, opening a pull request, running migrations against shared or production data, discarding unrelated work, or making authenticated, paid, or externally visible requests.

Outside slice scope, only you write `state.md` and the research corpus.

# Constraints

- One sub-agent per slice, sequential, fresh context each time. Do not implement several slices in one sub-agent.
- Do not accept a slice on the sub-agent's assurance alone; require deterministic evidence you observed.
- Do not change shared contracts without upstream revision.
- Do not rewrite broad adjacent code, add speculative abstractions, or expand scope for cleanup.
- Do not let implementation-generated tests redefine acceptance criteria.
- Do not read whole slice implementations into your own context; keep to diffs, reports, and verification output.

# Output

Update `state.md`. Final response:

```markdown
## Execution run

| Slice | Result | Verification |
|---|---|---|
| ... | Completed / Blocked / Verification failed | `<command>` — passed/failed |

- **Changed:** <concise paths and behaviour>
- **Divergence:** None | <summary and affected artifact>
- **Research:** None | <report reused or created>
- **Stopped because:** All slices complete | <blocker>
- **Next:** Fresh `swe-review` | Revise `<artifact>` | Resume remaining slices
```

# Completion Criteria

- Every attempted slice was implemented by its own fresh sub-agent, or the run stopped at a real blocker.
- Shared contracts and design boundaries remain intact.
- Required deterministic checks were run by you and recorded truthfully.
- The repository remains in a valid state or the failure is explicit.
- `state.md` reflects completed, remaining, blocked, and divergent work.
