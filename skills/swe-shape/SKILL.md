---
name: swe-shape
description: "Turn a software change idea into a concise, repository-grounded product brief. Use before designing or implementing non-trivial features, fixes, refactors, or migrations."
disable-model-invocation: true
---

# Pipeline

`swe-shape` → `swe-spec` → [`swe-plan`] → [`swe-worktree`] → `swe-execute` → `swe-review`, with `swe-research` available at any point.

Each stage runs in its own session: the user invokes one skill, reviews the artifact, clears the context, then invokes the next with the artifacts as input. Assume no memory of other stages beyond the files named below.

- **This stage:** decide whether the idea is worth building and what it must achieve.
- **Reads:** the user's idea, the repository, and the shared research corpus.
- **Writes:** `brief.md`.
- **Next:** the user runs `swe-spec` to decide what actually changes.

# Purpose

Determine whether a proposed change is worth building, what outcome it must produce, and whether it is feasible in this repository. Produce a brief — not a design, at any level of detail.

# Inputs

CHANGE_DIR: `.swe/YYYY-MM-DD-<slug>/` — create it for this change unless the conversation already names one.
BRIEF_FILE: `{CHANGE_DIR}/brief.md`, written from [brief.template.md](brief.template.md).
RESEARCH_DIR: `.swe/research/` — shared across all changes, indexed by `INDEX.md`.

# What Belongs in a Brief

A brief answers **why, and how will we know it worked**. It never says how the change is built.

The working test: **every statement in the brief must still be true if the codebase were rewritten from scratch in a different language.** If a statement would change because the implementation changed, it belongs to the next stage.

- "p95 under 300ms" belongs. "Response cached in Redis with a 60s TTL" does not.
- "Must not break the v1 API contract" belongs. "The v1 adapter maps `foo` to `bar`" does not.
- "Must reuse the existing job queue" does not — that is a solution choice. If it is genuinely non-negotiable for an external reason, record the reason in Non-negotiables, not the mechanism.

# Workflow

1. **Inspect before interviewing.** Read the smallest useful set of repository guidance, product documentation, relevant source, tests, and existing research reports. Do not ask what repository evidence or the research corpus already answers.
2. **Restate the problem.** Separate the user's underlying problem and desired outcome from their suggested solution.
3. **Challenge the idea.** Test assumptions about users, current behaviour, value, scope, edge cases, and existing capabilities. Look for a smaller coherent outcome, and for reasons the change should be narrowed, investigated, or rejected.
4. **Check feasibility.** Identify repository seams, conflicts, prerequisites, external dependencies, migration or compatibility concerns, and major unknowns. Resolve bounded factual unknowns through `Research delegation`, then continue shaping from the evidence.
5. **Interview selectively.** Ask the smallest set of questions that can change scope, acceptance criteria, feasibility, or risk. Ask dependent questions one at a time. Offer 2–4 concrete options with a recommendation when meaningful.
6. **Assess.** Choose a recommendation, rate complexity and risk, and name the main uncertainty. Do not invent calendar estimates.
7. **Write the brief,** then stop. The human decides whether to revise, investigate, or continue.

# Decision Rules

## Recommendation

- **Proceed:** Evidence is sufficient and no known blocker invalidates the change.
- **Narrow:** A smaller outcome preserves most of the value while materially reducing risk or scope.
- **Blocked:** A required investigation was deferred, could not be run safely, or was inconclusive, and scope or feasibility cannot be decided without it. Record the concrete unresolved question. This is an abnormal outcome — research that can be run should be run, inline, during shaping.
- **Stop:** The change duplicates existing capability, conflicts with a hard constraint, lacks a coherent outcome, or is demonstrably infeasible.

## Research delegation

When shaping exposes a factual unknown:

- check `RESEARCH_DIR` first — the answer may already be there from an earlier change;
- run `swe-research` with sub-agents when the question is clear, bounded, safe, and answerable through repository inspection, public documentation, a read-only probe, or a disposable local experiment;
- resume shaping from the report and link it in `## Research used`.

Ask the user directly when evidence cannot choose between valid outcomes; research does not settle subjective product trade-offs.

# Evidence Rules

- Cite repository claims with stable paths and symbols when they affect feasibility.
- Use research findings as evidence, and inspect the saved artifacts when a conclusion depends on observed payloads, experiments, or impact analysis.
- Prefer official documentation, standards, schemas, or dependency source for external claims.
- Treat missing evidence as unknown, not as proof.
- A research finding becomes product intent only once the brief incorporates it.
- Stop broad inline reading once the feasibility decision can be supported; delegate deeper or artifact-producing investigation to `swe-research`.

# Autonomy

Read repository files, run read-only inspection, and delegate bounded research without asking. Write only inside `CHANGE_DIR` and `RESEARCH_DIR`.

Ask first when an investigation is authenticated, paid, mutating, destructive, rate-sensitive, unusually broad, or when framing the question requires a product choice.

Never modify source code, and never invoke another SWE pipeline stage — recommend the next one instead.

# Constraints

- Do not design: no components, APIs, DTOs, schemas, interfaces, file structure, or chosen mechanisms.
- Do not turn every user statement into a requirement.
- Acceptance criteria are the single home for required behaviour; do not restate the same outcome as a requirement, an example, and a success criterion.
- **The brief fits on one screen — roughly 60 lines, normally 3–7 acceptance criteria.** If it does not fit, the change is too large: narrow it or split it.
- Record unresolved decisions only for `Blocked` or `Stop`. Otherwise resolve them in the interview or drop them as non-blocking follow-up.

# Output

Write `BRIEF_FILE`, then report:

- change directory;
- recommendation and main uncertainty;
- research created or reused;
- next skill (`swe-spec` unless the recommendation is `Blocked` or `Stop`), and whether a blocking decision remains.

# Completion Criteria

- The recommendation is supported by evidence proportionate to its risk.
- Problem, outcome, acceptance criteria, exclusions, and feasibility are clear enough for that recommendation.
- Every statement passes the rewritten-from-scratch test and respects the one-screen limit.
