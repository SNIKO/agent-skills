---
name: swe-architect
description: "Create a concise high-level architecture from a software brief. Use when a change introduces or alters component boundaries, ownership, data flow, persistence ownership, or cross-module behaviour."
disable-model-invocation: true
---

# Purpose

Decide how the system should be divided at a high level: major components, responsibilities, ownership, allowed dependencies, conceptual data, and important runtime flows. Produce `architecture.md` that is expressive without becoming a low-level build specification.

# Inputs and Workspace

Use the `brief.md` named by the user, or infer the current change only when unambiguous. Inspect relevant `spikes/*/spike.md` reports in the change workspace and open their saved artifacts when observed behaviour or impact details affect architecture. Write `.swe-work/<change>/architecture.md` using [architecture.template.md](architecture.template.md).

If the brief recommends no architecture stage and repository inspection confirms that existing boundaries are sufficient, do not create a ceremonial document; explain why `swe-spec` can proceed directly.

# Workflow

1. **Read intent and evidence.** Read the selected brief, relevant spike reports and artifacts, repository guidance, existing architecture, and only the source needed to understand current boundaries. Do not repeat research already preserved by a credible spike.
2. **Extract design drivers.** Identify the requirements, constraints, risks, and repository realities that materially shape architecture.
3. **Develop alternatives.** Consider at least two materially different feasible approaches when alternatives exist. Challenge the preferred approach: identify assumptions that could make it fail, a simpler established pattern, and the likely pressure at larger scale.
4. **Resolve real trade-offs.** Make decisions with a clear repository-grounded winner autonomously. Ask the user only when viable options have meaningfully different product, operational, security, consistency, or maintenance consequences.
5. **Design boundaries.** Define top-level components, responsibilities, ownership, dependency direction, orchestration, conceptual data stores, and failure ownership.
6. **Model runtime behaviour.** Describe the normal flow and 1–3 architecturally distinct flows such as retries, duplicates, partial failure, replay, or dependency outage.
7. **Check feasibility.** Ensure the proposed dependencies are possible in the repository and every important side effect, state transition, and system-wide invariant has an owner. Resolve clear factual unknowns through a spike using the Research Delegation rules below, then revisit the affected design decision.
8. **Complete or stop.** If a product decision or evidence gap still prevents a coherent architecture, report it and do not create or overwrite `architecture.md`. Otherwise write the architecture using the template and preserve only concise rationale for the selected approach and credible rejected alternatives.

# Decision Rules

- A component earns a boundary when it owns a coherent responsibility, data, side effect, integration, lifecycle, or policy that changes for a distinct reason.
- Prefer existing repository boundaries and patterns unless they conflict with the brief.
- Keep dependencies directional and isolate external I/O behind an explicit boundary.
- Assign one owner for orchestration, mutation, retries, idempotency, and compatibility at each relevant boundary.
- Use logical entities or stores in architecture. Physical table names, columns, indexes, and migrations belong in `swe-spec`.
- Name operations and exchanged information semantically. Exact method signatures, routes, and DTO schemas belong in `swe-spec`.
- Include a failure flow only when it changes ownership, state, consistency, security, or cross-component behaviour.
- Prefer the simplest architecture that satisfies current requirements. Do not add abstractions for hypothetical consumers or variation points.

# Research Delegation

When architecture depends on an unknown repository constraint, external-system behaviour, payload shape, dependency capability, or measurable property:

- run a spike automatically when the research question is clear, bounded, and safe;
- store it under the current change workspace and resume architecture from its evidence;
- ask the user before consequential external requests, expensive experiments, or research whose framing requires an architectural or product choice;
- if the spike is inconclusive, report the unresolved assumption and do not finalize the architecture.

Do not use a spike to avoid choosing between understood architectural trade-offs; make the decision or ask the user.

# Upstream Revision Rule

If architecture work reveals that the brief is infeasible, contradictory, or missing a product decision:

1. Stop design work.
2. Identify the brief section that must change.
3. Report the impact and return to `swe-shape`.
4. Do not create or overwrite `architecture.md`; a present architecture file should represent a completed architectural decision, not partial work.

If delegated research is inconclusive, report the spike path and unresolved factual assumption instead of disguising it as an architectural choice.

# Constraints

- Do not define exact API routes, DTOs, event schemas, SQL columns, indexes, migrations, configuration keys, or exhaustive file lists.
- Do not prescribe private methods, classes, helpers, algorithms, or local data structures.
- Do not repeat product background, spike findings, or repository context; link the brief, spike report, saved evidence, and existing architecture.
- Resolve design questions before writing the artifact. Keep non-blocking risks as concise decision trade-offs, not an open-question backlog.
- Do not begin implementation.

# Output

When architecture is complete, write `architecture.md` and report:

- selected approach in one sentence;
- main components and ownership boundary;
- material trade-off or risk;
- recommended next step, normally `swe-spec`.

When blocked, do not write the artifact; report the blocking decision or evidence gap and the upstream skill to run.

# Completion Criteria

Finish with exactly one outcome:

- **Architecture written:** Components have coherent responsibilities and ownership; dependency direction, orchestration, data ownership, failure ownership, and important runtime flows are feasible; credible alternatives were challenged; and no unresolved design decision prevents low-level specification.
- **Architecture skipped:** Existing boundaries already determine the change, and the response explains why `swe-spec` can proceed without a new artifact.
- **Returned upstream:** The blocking product decision or evidence gap and earliest affected artifact are explicit, and `architecture.md` was not created or overwritten.
