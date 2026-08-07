---
name: swe-worktree
description: "Create an isolated git worktree for an SWE change workspace. Use before implementation when the user wants the change isolated from the current working tree."
disable-model-invocation: true
---

# Pipeline

`swe-shape` → `swe-spec` → [`swe-plan`] → [`swe-worktree`] → `swe-execute` → `swe-review`, with `swe-research` available at any point.

Each stage runs in its own session: the user invokes one skill, reviews the result, clears the context, then invokes the next. Assume no memory of other stages beyond the files named below.

- **This stage:** optional infrastructure. Isolate the change before implementation.
- **Reads:** the change directory produced by earlier stages — `brief.md`, `spec.html`, and `plan.md` when present. Their contents are not needed; only their location.
- **Writes:** a git branch and worktree, with the change directory copied into it.
- **Next:** the user runs `swe-execute` inside the new worktree. Do not invoke it.

# Purpose

Create a safe isolated branch and git worktree, copy the complete SWE change workspace into it, and report where implementation should continue. Worktrees are optional execution infrastructure, not a required specification stage.

# Inputs

Infer from user input or an artifact path:

- `CHANGE_DIR`: `.swe/<change>/`
- `SLUG`: kebab-case change label
- `REPO_NAME`: repository root basename
- `BRANCH_NAME`: repository convention, otherwise `<slug>`
- `WORKTREE_PARENT`: repository convention, otherwise `../worktrees/<repo-name>/`
- `WORKTREE_PATH`: `<worktree-parent>/<slug>`

Use the change directory named by the user. If it is ambiguous, ask which one to prepare.

The shared research corpus at `.swe/research/` is **not** change scope. It is repository documentation that outlives any branch, so it is not copied and not branched: research produced during implementation is written to the corpus in the main working tree and committed independently. Copying it would fork the corpus per branch and guarantee divergence.

# Workflow

1. **Inspect repository state.** Read repository git guidance and inspect `git status --short`, current branch, existing branches, and worktrees. Preserve unrelated changes.
2. **Resolve names.** Follow repository branch and worktree conventions. Fall back to the defaults above only when guidance is absent.
3. **Handle conflicts.** If the branch or path already exists, report the exact conflict and ask whether to reuse it or choose another name.
4. **Create the worktree.** Run:

   ```bash
   git worktree add -b "$BRANCH_NAME" "$WORKTREE_PATH"
   ```

   If creation fails, stop and report the command error before retrying.
5. **Copy artifacts.** Copy the complete `CHANGE_DIR` to the same repository-relative location inside the worktree — `brief.md`, `spec.html` and any split `spec-<area>.html` files, `mockups/`, `plan.md` when present, and `state.md`. Do not copy `.swe/research/`.
6. **Verify readiness.** Confirm the new path, branch, copied artifact paths, and clean worktree status.

# Autonomy

Inspect git state, and create the branch, worktree, and artifact copies described in `Inputs` and `Workflow` without asking.

Ask first before reusing, overwriting, force-creating, or deleting an existing branch or worktree, and before changing anything in the original working tree.

Never install dependencies, run migrations, commit, push, deploy, open a pull request, or begin implementation.

# Constraints

- Do not require `plan.md` when the spec is intentionally one slice.
- Do not copy, branch, or duplicate the shared research corpus.
- Stop immediately when worktree creation or artifact copying fails.

# Output

```markdown
## Worktree ready

- **Path:** `<absolute worktree path>`
- **Branch:** `<branch>`
- **Artifacts:** `<worktree>/.swe/<change>/`
- **Execution source:** `plan.md` | `spec.html` as one slice
- **Next:** Run `swe-execute` in the new worktree.
```

# Completion Criteria

- The worktree and branch exist without overwriting user state.
- Selected artifacts are present at the expected relative path.
- The original working tree has no changes caused by this skill beyond artifact copying requested by the user.
- The final response gives the exact absolute path and branch.
