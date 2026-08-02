---
name: swe-worktree
description: "Create an isolated git worktree for an SWE build spec or delivery plan. Use before implementation when the user wants the change isolated from the current working tree."
disable-model-invocation: true
---

# Purpose

Create a safe isolated branch and git worktree, copy the complete SWE change workspace into it, and report where implementation should continue. Worktrees are optional execution infrastructure, not a required specification stage.

# Inputs

Infer from user input or an artifact path:

- `CHANGE_DIR`: `.swe-work/<change>/`
- `SLUG`: kebab-case change label
- `REPO_NAME`: repository root basename
- `BRANCH_NAME`: repository convention, otherwise `<slug>`
- `WORKTREE_PARENT`: repository convention, otherwise `../worktrees/<repo-name>/`
- `WORKTREE_PATH`: `<worktree-parent>/<slug>`

Use the change directory or `build-spec.md` named by the user. Include `plan.md` when it exists. If the change directory is ambiguous, ask which one to prepare.

# Workflow

1. **Inspect repository state.** Read repository git guidance and inspect `git status --short`, current branch, existing branches, and worktrees. Preserve unrelated changes.
2. **Resolve names.** Follow repository branch and worktree conventions. Fall back to the defaults above only when guidance is absent.
3. **Handle conflicts.** If the branch or path already exists, report the exact conflict and ask whether to reuse it or choose another name. Never overwrite or force-create it.
4. **Create the worktree.** Run:

   ```bash
   git worktree add -b "$BRANCH_NAME" "$WORKTREE_PATH"
   ```

   If creation fails, stop and report the command error before retrying.
5. **Copy artifacts.** Copy the complete `CHANGE_DIR` to the same repository-relative location inside the worktree, including canonical Markdown artifacts, their paired derived HTML review artifacts when present, state, and every spike report and saved spike artifact.
6. **Verify readiness.** Confirm the new path, branch, copied artifact paths, and clean worktree status.

# Constraints

- Do not modify source files in the original working tree.
- Do not overwrite, delete, or reuse a branch or worktree without user approval.
- Do not install dependencies, run migrations, push, deploy, open a pull request, or begin implementation.
- Do not require `plan.md` when the build spec is intentionally one slice.
- Stop immediately when worktree creation or artifact copying fails.

# Output

```markdown
## Worktree ready

- **Path:** `<absolute worktree path>`
- **Branch:** `<branch>`
- **Artifacts:** `<worktree>/.swe-work/<change>/`
- **Execution source:** `plan.md` | `build-spec.md` as one slice
- **Next:** Run `swe-execute` in the new worktree.
```

# Completion Criteria

- The worktree and branch exist without overwriting user state.
- Selected artifacts are present at the expected relative path.
- The original working tree has no changes caused by this skill beyond artifact copying requested by the user.
- The final response gives the exact absolute path and branch.
