---
name: swe-review
description: "Review an SWE brief, research report, spec, delivery plan, execution state, implementation diff, branch, or pull request. Use for fresh-context, evidence-backed findings before the human chooses the next action."
disable-model-invocation: true
---

# Pipeline

`swe-shape` → `swe-spec` → [`swe-plan`] → [`swe-worktree`] → `swe-execute` → `swe-review`, with `swe-research` available at any point.

Each stage runs in its own session — that separation is what makes this review independent. Assume no memory of the session that produced the target.

- **This stage:** review one artifact or one code change with fresh context.
- **Reads:** the review target, its upstream artifacts, and the repository. Artifacts are `brief.md` (problem and acceptance, from `swe-shape`), `spec.html` (design and contracts, from `swe-spec`), `plan.md` (slice ordering, from `swe-plan`), `state.md` (execution progress, from `swe-execute`), and research reports (from `swe-research`).
- **Writes:** nothing. Findings are returned in chat.
- **Next:** the user decides whether to revise, return upstream, or proceed.

# Purpose

Provide an independent, concise review of either an SWE artifact or changed code. Optimize for confirmed, actionable findings rather than volume.

# Modes

Choose exactly one mode from the user's target:

- **Artifact review:** `brief.md`, `spec.html`, `plan.md`, `state.md`, or a research `report.md`.
- **Code review:** pull request, branch, commit range, staged changes, unstaged changes, or an implementation checked against SWE artifacts.

When the target is ambiguous and artifact and code review would produce materially different scopes, ask one clarifying question. Otherwise infer the mode from the path or request.

# Workflow

1. Identify the exact review target and scope.
2. Read the corresponding reference completely:
   - artifact review: [references/artifact-review.md](references/artifact-review.md)
   - code review: [references/code-review.md](references/code-review.md)
3. Apply repository rules before general review preferences.
4. Treat the target as if entering with fresh context: verify its claims and do not defend decisions because they appeared earlier in the conversation.
5. Return only verified, deduplicated, actionable findings in the reference's output format.

# Shared Rules

- Prefer no findings when no concrete issue crosses the applicable review threshold.
- Review the requested artifact or changed behaviour and directly affected context, not the whole repository.
- Treat missing requirements or evidence as unknown; do not invent them.
- Match repository conventions and stated priorities when they differ from generic preferences.
- Distinguish blocking correctness or decision gaps from optional improvements.
- When verifying a material claim requires substantial repository impact analysis, an external probe, or a reproducible experiment, check `.swe/research/INDEX.md` and then run `swe-research` if needed, citing the report; keep ordinary source inspection inside the review.

# Autonomy

Read artifacts, source, diffs, branches, and history, and run read-only inspection and non-destructive repository checks without asking. The only files this skill writes are the temporary review workspace files described in the code-review reference, which it removes before finishing.

Ask first before authenticated, paid, mutating, or otherwise consequential external research.

Never edit the reviewed artifact or code; the user decides what to fix after seeing the findings.

# Output

Use the selected reference's exact output structure. State the review mode and target clearly. If no confirmed findings remain, say so directly.

# Completion Criteria

- The scope and mode are unambiguous.
- Relevant repository rules and authoritative artifacts were considered.
- Every finding cites concrete evidence and explains impact and minimal correction.
- Speculative, stylistic, duplicated, and out-of-scope findings were removed.
- The final assessment follows the selected reference's rubric.
