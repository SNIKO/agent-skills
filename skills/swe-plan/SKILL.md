---
name: swe-plan
description: "Create an optional delivery plan from a specification. Use only when implementation needs multiple independently verifiable slices, sessions, agents, migrations, backfills, or rollout coordination."
disable-model-invocation: true
---

# Pipeline

`swe-shape` → `swe-spec` → [`swe-plan`] → [`swe-worktree`] → `swe-execute` → `swe-review`, with `swe-research` available at any point.

Each stage runs in its own session: the user invokes one skill, reviews the artifact, clears the context, then invokes the next with the artifacts as input. Assume no memory of other stages beyond the files named below.

- **This stage:** optional. Decide delivery order and verification boundaries. Most changes skip it.
- **Reads:** `spec.html` (the approved design and contracts, written by `swe-spec`) and `brief.md` (the approved problem and acceptance criteria, written by `swe-shape`).
- **Writes:** `plan.md`, or nothing when the spec is one coherent slice.
- **Next:** the user runs `swe-worktree` or `swe-execute`. Do not invoke either.

# Purpose

Decide delivery order and verification boundaries for complex execution. A plan coordinates specified work; it does not repeat design or narrate how to code it.

A plan is **not** a Markdown restatement of the spec. `spec.html` remains the contract, and `swe-execute` reads it whether or not a plan exists. The plan's only job is to say what is built in what order, how each step is verified, and which spec sections each step needs.

# Inputs and Workspace

Read `.swe/<change>/spec.html` — the canonical contract, at whatever depth each section reached — using its table of contents to load only the sections that affect sequencing. Read `brief.md` when acceptance, dependencies, or risk require it, and the research reports either artifact links when they affect sequencing, feasibility, rollout, or an early risk-reduction slice. Infer the change directory only when unambiguous.

Write `.swe/<change>/plan.md` using [plan.template.md](plan.template.md). The plan is Markdown: it is a short list of slices consumed almost entirely by agents, with little to gain from rich rendering.

# Workflow

1. **Decide whether to plan.** Apply the planning threshold below — it is a gate, not a preference. If the spec is one coherent implementation slice, do not create `plan.md`; report that execution can proceed directly from the spec. Most changes end here.
2. **Identify delivery slices.** Find the smallest set of completed, observable or independently risk-reducing outcomes. Prefer thin end-to-end slices over technical layers.
3. **Order by dependency and learning.** Establish prerequisites, then prioritize early validation of expensive assumptions, integration risk, migration safety, and user-visible value.
4. **Preserve valid intermediate states.** Every slice must leave the repository buildable, testable, and operationally safe under the specified compatibility strategy.
5. **Reference authoritative detail.** Link each slice to the exact `spec.html` section ids it implements and the acceptance criteria it satisfies. Do not copy their contracts.
6. **Define verification.** Give runnable repository commands and observable results for each slice, including rollback or operational checks only where relevant.
7. **Check plan size.** If more than roughly eight slices are needed, recommend splitting the change or introducing explicit phases rather than producing a giant plan.
8. **Complete, skip, or stop.** Finish with exactly one of the outcomes in `Completion Criteria`.

# Planning Threshold

Create a plan when one or more apply:

- implementation has multiple independently verifiable outcomes;
- work spans several focused sessions or agents;
- migrations, backfills, compatibility windows, rollout, or rollback require ordering;
- an external integration or risky assumption should be validated before broad implementation;
- intermediate repository or production states require deliberate coordination;
- teams can work in parallel against stable spec contracts.

Skip the plan when the spec can be implemented and verified as one coherent slice with straightforward intermediate state. A large spec is not by itself a reason to plan; a spec with multiple independently verifiable outcomes is.

# Slice Rules

A slice must:

- produce completed behaviour or a stable, independently valuable risk-reduction boundary;
- include its production changes, tests, required configuration, and documentation;
- depend only on completed earlier slices and spec contracts;
- leave the repository valid after that slice alone;
- have one clear outcome and deterministic verification.

A schema migration, compatibility layer, contract-tested external client, or enabling refactor may be a separate slice when it is independently safe and verifiable. Do not create placeholder interfaces, empty scaffolding, or horizontal layers that become useful only after undocumented future work.

# Research and Upstream Revision

If sequencing depends on a clear, bounded factual unknown—such as migration duration, deployment compatibility, backfill throughput, or an external operational limit—check `.swe/research/INDEX.md`, then run `swe-research`, and resume planning from its evidence.

Planning must not invent requirements, design, contracts, paths, or error behaviour. If research is inconclusive or the spec cannot be sequenced safely:

1. Stop planning.
2. Identify the exact upstream gap or contradiction.
3. Return to `swe-spec` or `swe-shape` as appropriate. A present `plan.md` should be executable rather than a partial draft.

# Autonomy

Read the repository and upstream artifacts, run read-only inspection, and delegate bounded research without asking. Write only `plan.md`.

Ask first when an investigation is authenticated, paid, mutating, production-facing, or expensive.

Never modify source code, and never invoke another SWE pipeline stage.

# Constraints

- Do not include API schemas, SQL, interface signatures, algorithms, or detailed code mutations.
- Do not prescribe private methods, local variables, or step-by-step coding instructions.
- Do not add per-slice commit commands.
- Do not restate spec content so an implementer can avoid reading `spec.html`; they must follow the linked sections.
- Do not create a plan merely because the framework has a planning skill.

# Output

When a plan was written, report the planning rationale, the number and ordering strategy of the slices, the first risk or capability validated, the artifact path, and the next step — normally `swe-worktree` or `swe-execute`.

When it was skipped or blocked, report the reason and where execution or revision continues instead.

# Completion Criteria

Finish with exactly one outcome, having created or overwritten `plan.md` only in the first:

- **Plan written:** every slice satisfies `Slice Rules`, references the `spec.html` section ids and acceptance criteria it implements, and has deterministic verification; ordering preserves valid intermediate states.
- **Plan skipped:** the spec is one coherent slice, and the response directs execution to `spec.html`.
- **Returned upstream:** the sequencing gap and the earliest affected artifact are explicit.
