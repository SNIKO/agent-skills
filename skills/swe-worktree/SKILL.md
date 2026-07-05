---
name: swe-worktree
description: "Create an isolated git worktree for a plan ready for implementation. Use after swe-plan produces an approved plan."
disable-model-invocation: true
---

<goal>
Set up an isolated git worktree for an approved implementation plan.
Derive the branch name and worktree path from the change, create the branch and worktree, copy all change artefacts into it, and confirm the workspace is ready.
</goal>

<variables>
CHANGE_DIR: infer from user input or the plan file path (e.g. `.swe-work/{{DATE}}-{{SLUG}}/`)
SLUG: kebab-case label extracted from CHANGE_DIR (e.g. `auth-refresh`)
REPO_NAME: basename of the repository root directory
BRANCH_NAME: derived from SLUG; check repo git rules for branch naming convention; default to SLUG
WORKTREE_PARENT: check repo git rules for worktree location preference; default to `../worktrees/{{REPO_NAME}}/`
WORKTREE_PATH: `{{WORKTREE_PARENT}}/{{SLUG}}`
</variables>

<workflow>

## 1. Read plan and repo context

- Infer `CHANGE_DIR` and `SLUG` from the plan file path or user input.
- Read repository guidance (AGENTS.md, `.agents/`, README) for worktree location preferences and branch naming conventions.
- Fall back to defaults when guidance is absent.

## 2. Resolve worktree settings

- Set `BRANCH_NAME` from repo naming convention, or default to `SLUG`.
- Set `WORKTREE_PARENT` from repo guidance, or default to `../worktrees/{{REPO_NAME}}/`.
- If the branch already exists, report it and ask the user whether to reuse it or choose a different name.

## 3. Create the branch and worktree

```bash
git worktree add -b "$BRANCH_NAME" "$WORKTREE_PATH"
```

- If this fails, report the error and ask the user how to proceed before retrying.
- Do not force-create or overwrite an existing branch or worktree without explicit user approval.

## 4. Copy change artefacts

```bash
cp -r "$CHANGE_DIR" "$WORKTREE_PATH/$CHANGE_DIR"
```

Copy the full `CHANGE_DIR` into the worktree at the same relative path. Include all files: proposal, context, design, plan, and any references.

## 5. Confirm ready

> "Worktree created at `{{WORKTREE_PATH}}` on branch `{{BRANCH_NAME}}`. Change artefacts copied from `{{CHANGE_DIR}}`. Ready to implement."

</workflow>

<constraints>
- do not modify any files in the original repository working tree.
- do not push the branch or open a pull request.
- do not run dependency installation or tests — those are the implementer's responsibility.
- do not proceed past **create_branch_and_worktree** if the worktree creation failed.
</constraints>
