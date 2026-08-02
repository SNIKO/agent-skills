---
name: swe-plan
description: "Create an optional delivery plan from a build specification. Use only when implementation needs multiple independently verifiable slices, sessions, agents, migrations, backfills, or rollout coordination."
disable-model-invocation: true
---

# Purpose

Decide delivery order and verification boundaries for complex execution. A plan coordinates specified work; it does not repeat low-level design or narrate how to code it. Produce canonical Markdown for agents plus a derived HTML review artifact for humans only when a plan is justified.

# Inputs and Workspace

Use the `build-spec.md` named by the user, or infer the current change only when unambiguous. Read the brief and architecture only when needed to understand acceptance, dependencies, or risk. Read relevant spike reports and artifacts when they affect sequencing, feasibility, rollout, or an early risk-reduction slice. When the change instead used the progressive `swe-spec-new` flow, read `.swe-work/<change>/spec.html` directly as the canonical contract at whatever depth it reached; there is no Markdown counterpart to read instead. Write canonical `.swe-work/<change>/plan.md` using [plan.template.md](plan.template.md) only when a separate plan is justified and can be sequenced safely. Then render `.swe-work/<change>/plan.html` using [swe-artifact](../swe-artifact/SKILL.md). Markdown is authoritative; HTML is a static human-review artifact.

# Workflow

1. **Decide whether to plan.** Apply the planning threshold below. If the build spec is one coherent implementation slice, do not create `plan.md`; report that execution can proceed directly from the spec.
2. **Identify delivery slices.** Find the smallest set of completed, observable or independently risk-reducing outcomes. Prefer thin end-to-end slices over technical layers.
3. **Order by dependency and learning.** Establish prerequisites, then prioritize early validation of expensive assumptions, integration risk, migration safety, and user-visible value.
4. **Preserve valid intermediate states.** Every slice must leave the repository buildable, testable, and operationally safe under the specified compatibility strategy.
5. **Reference authoritative detail.** Link each slice to exact build-spec sections and acceptance criteria. Do not copy their contracts.
6. **Define verification.** Give runnable repository commands and observable results for each slice, including rollback or operational checks only where relevant.
7. **Check plan size.** If more than roughly eight slices are needed, recommend splitting the change or introducing explicit phases rather than producing a giant plan.
8. **Complete, skip, or stop.** Write `plan.md` and its derived `plan.html` only when a separate plan is justified and fully sequenceable. If no plan is needed, leave neither plan artifact. If an upstream gap prevents safe sequencing, report it and do not create or overwrite either paired artifact.

# Planning Threshold

Create a plan when one or more apply:

- implementation has multiple independently verifiable outcomes;
- work spans several focused sessions or agents;
- migrations, backfills, compatibility windows, rollout, or rollback require ordering;
- an external integration or risky assumption should be validated before broad implementation;
- intermediate repository or production states require deliberate coordination;
- teams can work in parallel against stable build-spec contracts.

Skip the plan when the build spec can be implemented and verified as one coherent slice with straightforward intermediate state.

# Slice Rules

A slice must:

- produce completed behaviour or a stable, independently valuable risk-reduction boundary;
- include its production changes, tests, required configuration, and documentation;
- depend only on completed earlier slices and build-spec contracts;
- leave the repository valid after that slice alone;
- have one clear outcome and deterministic verification.

A schema migration, compatibility layer, contract-tested external client, or enabling refactor may be a separate slice when it is independently safe and verifiable. Do not create placeholder interfaces, empty scaffolding, or horizontal layers that become useful only after undocumented future work.

# Research and Upstream Revision

If sequencing depends on a clear, bounded factual unknown—such as migration duration, deployment compatibility, backfill throughput, or an external operational limit—run a safe spike under the current change workspace and resume planning from its evidence. Ask before expensive, production-facing, authenticated, or mutating investigation.

Planning must not invent requirements, architecture, contracts, paths, or error behaviour. If a spike is inconclusive or the build spec cannot be sequenced safely:

1. Stop planning.
2. Identify the exact upstream gap or contradiction.
3. Return to `swe-spec`, `swe-architect`, or `swe-shape` as appropriate.
4. Do not create or overwrite `plan.md`; a present plan should be executable rather than a partial draft.

# Constraints

- Do not include API schemas, SQL, interface signatures, algorithms, or detailed code mutations.
- Do not prescribe private methods, local variables, or step-by-step coding instructions.
- Do not add per-slice commit commands.
- Do not require an implementer to treat a task as standalone documentation; they must follow linked authoritative artifacts.
- Do not create a plan merely because the framework has a planning skill.
- Do not begin implementation.

# Output

If no plan is needed, do not create `plan.md` or `plan.html`; report the reason and recommend implementation directly from `build-spec.md` as one slice.

If a plan is needed and can be completed, write canonical `plan.md`, render `plan.html` from it using `swe-artifact`, and report:

- planning rationale;
- number and ordering strategy of slices;
- first risk or capability validated;
- both artifact paths and that Markdown is canonical;
- recommended next step, normally `swe-worktree` or `swe-execute`.

If planning is blocked, do not write either paired artifact; report the upstream gap and revision path.

# Completion Criteria

Finish with exactly one outcome:

- **Plan written:** `plan.md` is a concise, authoritative agent contract; its paired `plan.html` accurately renders the same delivery commitments for direct local review; a separate plan is justified; every slice has a coherent outcome, dependencies, spec references, expected areas, and deterministic verification; ordering preserves valid intermediate states; and the artifact contains no duplicated contracts or coding narration.
- **Plan skipped:** The build spec is one coherent slice, neither plan artifact was created, and the response directs execution to the build spec.
- **Returned upstream:** The sequencing gap and earliest affected artifact are explicit, and neither plan artifact was created or overwritten.
