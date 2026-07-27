---
name: swe-shape
description: "Turn a software change idea into a concise, repository-grounded product brief. Use before designing or implementing non-trivial features, fixes, refactors, or migrations."
disable-model-invocation: true
---

# Purpose

Determine whether a proposed change is worth building, what outcome it must produce, whether it is feasible in the current repository, and how much design process it needs. For non-direct work, produce `brief.md`, not an architecture or implementation plan.

# Workspace

Use `.swe-work/YYYY-MM-DD-<slug>/` as the change directory. For every path except `Direct`, write `brief.md` there using [brief.template.md](brief.template.md). For a `Direct` change, return the concise scope and acceptance check in chat unless the user asks to persist a brief.

# Workflow

1. **Inspect before interviewing.** Read the smallest useful set of repository guidance, product documentation, relevant source, tests, existing architecture, and relevant spike reports in the change workspace. Do not ask questions already answered by repository evidence or an existing spike.
2. **Restate the problem.** Distinguish the user's underlying problem and desired outcome from their suggested solution.
3. **Challenge the idea.** Test assumptions about users, current behaviour, value, scope, edge cases, constraints, and existing capabilities. Look for a smaller coherent outcome and for reasons the change should be narrowed, investigated, or rejected.
4. **Check feasibility.** Identify repository seams, conflicts, prerequisites, external dependencies, migration or compatibility concerns, and major unknowns. Resolve clear, bounded factual unknowns through a spike using the Spike Delegation rules below, then continue shaping with the resulting evidence.
5. **Interview selectively.** Ask the smallest set of questions that can change scope, acceptance criteria, feasibility, risk, or the recommended delivery depth. Ask dependent questions sequentially. Offer 2–4 concrete options and a recommendation when meaningful.
6. **Assess the change.** Choose `Proceed`, `Narrow`, `Spike`, or `Stop`; rate complexity and risk as `Low`, `Medium`, or `High`; name the main uncertainty and affected subsystems. Do not invent calendar estimates.
7. **Choose delivery depth.** Recommend one of the paths in Decision Rules.
8. **Write the outcome.** For non-direct work, write the brief. For direct work, report the concise scope and acceptance check without creating ceremony. Stop after presenting the shaping result; the human decides whether to revise it, investigate a blocker, continue to design, or implement directly.

# Decision Rules

## Feasibility outcome

- **Proceed:** Evidence is sufficient and no known blocker invalidates the change.
- **Narrow:** A smaller outcome preserves most value while materially reducing risk or scope.
- **Spike:** A required investigation was deferred, could not be run safely, or concluded without enough evidence to decide scope or feasibility. Record the concrete unresolved research question.
- **Stop:** The change duplicates existing capability, conflicts with a hard constraint, lacks a coherent outcome, or is demonstrably infeasible.

## Delivery depth

- **Direct:** Tiny, obvious, reversible change with no shared contract, migration, new boundary, or material uncertainty. A persisted brief is optional.
- **Build spec:** Bounded change where architecture is already established, but exact contracts or repository changes need agreement. Next: `swe-spec`.
- **Architecture + build spec:** New or changed component boundaries, ownership, data flow, persistence ownership, or cross-module behaviour. Next: `swe-architect`.
- **Full plan:** Architecture and build spec are needed, and execution spans multiple independently verifiable slices, sessions, agents, migrations, backfills, or rollout phases. `swe-plan` remains optional until the build spec exists.

Use risk and uncertainty, not file count alone, to choose the path.

## Spike delegation

When shaping exposes a factual unknown:

- run a spike automatically when the question is clear, bounded, safe, and answerable through repository inspection, public documentation, a read-only probe, or a disposable local experiment;
- create it under the current change workspace and resume shaping from its report;
- ask the user first when the investigation is authenticated, paid, mutating, rate-sensitive, destructive, unusually broad, or when framing the question requires a product choice;
- return the `Spike` feasibility outcome only when the investigation is deferred, unsafe to run, or inconclusive.

Do not delegate subjective product trade-offs to research. Ask the user directly when evidence cannot choose between valid outcomes.

# Evidence Rules

- Cite repository claims with stable paths and symbols when they affect feasibility.
- Use relevant spike findings as evidence, and inspect their saved artifacts when the conclusion depends on observed payloads, experiments, or repository impact analysis.
- Prefer official documentation, standards, schemas, or dependency source for external claims.
- Record access dates for external facts that may change.
- Treat missing evidence as unknown, not as proof.
- A spike recommendation becomes product intent only after the brief incorporates it.
- Stop broad inline research once the feasibility decision can be supported. Delegate deeper or artifact-producing investigation to a spike.

# Constraints

- Do not design components, APIs, DTOs, schemas, interfaces, or file structure.
- Do not turn every user statement into a requirement.
- Do not duplicate the same outcome across goals, requirements, examples, and success criteria.
- Keep the brief near one reviewable page: normally 3–7 acceptance criteria and only decision-relevant detail.
- Record unresolved decisions only when the feasibility outcome is `Spike` or `Stop`; otherwise resolve them during the interview or exclude them as non-blocking follow-up.
- Do not use an open-questions section as a backlog.
- Do not invoke another SWE stage automatically.

# Output

For non-direct work, write `brief.md`. For direct work, return the concise scope and acceptance check in chat. Then report:

- change directory or `Not created — Direct path`;
- feasibility outcome and main uncertainty;
- recommended delivery depth;
- next skill, if any;
- whether a blocking decision or spike remains.

# Completion Criteria

- The shaping result is `Proceed`, `Narrow`, `Spike`, or `Stop` with evidence proportionate to its risk.
- The problem, outcome, scope, non-goals, and observable acceptance criteria are clear enough for that result.
- Repository fit, complexity, risk, uncertainty, and delivery depth are explicit.
- A non-direct result is captured in a concise brief; a direct result remains concise chat output unless persistence was requested.
- No architecture was selected, and unresolved decisions appear only for `Spike` or `Stop`.
