---
name: swe-review
description: "Review an SWE brief, spike, architecture, build spec, delivery plan, execution state, implementation diff, branch, or pull request. Use for fresh-context, evidence-backed findings before the human chooses the next action."
disable-model-invocation: true
---

# Purpose

Provide an independent, concise review of either an SWE artifact or changed code. Optimize for confirmed, actionable findings rather than volume.

# Modes

Choose exactly one mode from the user's target:

- **Artifact review:** a canonical `brief.md` or `spec.html`.
- **Code review:** pull request, branch, commit range, staged changes, unstaged changes, or an implementation checked against SWE artifacts.

When the target is ambiguous and artifact and code review would produce materially different scopes, ask one clarifying question. Otherwise infer the mode from the path or request.

# Workflow

1. Identify the exact review target and scope.
2. Read the corresponding reference completely:
   - artifact review: [references/artifact-review.md](references/artifact-review.md)
   - code review: [references/code-review.md](references/code-review.md)
3. Apply repository rules before general review preferences.
4. Treat the target as if entering with fresh context: verify its claims and do not defend decisions because they appeared earlier in the conversation. For paired HTML, first read the canonical Markdown, then report material drift, omissions, broken local links, or readability defects; do not treat presentation changes as contract changes.
5. Return only verified, deduplicated, actionable findings in the reference's output format.

# Shared Rules

- Prefer no findings when no concrete issue crosses the applicable review threshold.
- Review the requested artifact or changed behaviour and directly affected context, not the whole repository.
- Treat missing requirements or evidence as unknown; do not invent them.
- Match repository conventions and stated priorities when they differ from generic preferences.
- Distinguish blocking correctness or decision gaps from optional improvements.
- When verifying a material claim requires substantial repository impact analysis, an external probe, or a reproducible experiment, run a safe spike and cite its report; keep ordinary source inspection inside the review.
- Ask before consequential external research, and do not edit the reviewed artifact or code unless the user explicitly asks for fixes after the review.

# Output

Use the selected reference's exact output structure. State the review mode and target clearly. If no confirmed findings remain, say so directly.

# Completion Criteria

- The scope and mode are unambiguous.
- Relevant repository rules and authoritative artifacts were considered.
- Every finding cites concrete evidence and explains impact and minimal correction.
- Speculative, stylistic, duplicated, and out-of-scope findings were removed.
- The final assessment follows the selected reference's rubric.
