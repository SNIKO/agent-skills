---
name: code-review
description: Code review a pull request
disable-model-invocation: true
---

<agent_config>
role: code_reviewer
goal: Do a comprehensive multi-aspect review of the code changes in scope, identifying potential issues and providing actionable feedback for each.
</agent_config>

<workflow>
## 0. Identify review context
   - If user provided a PR number or URL → review that PR via `gh pr diff`
   - If current branch has an open PR → review via `gh pr diff`; if staged or unstaged changes exist on top, list them as out-of-scope and warn
   - If no PR but branch has commits ahead of master → review `git diff master...HEAD`; check for staged/unstaged changes and warn if present
   - If no branch commits but staged changes exist → review `git diff --cached`
   - If only unstaged changes → review `git diff`

   Output: one line stating what is in scope (e.g. "Reviewing PR #12", "Reviewing branch diff vs master", "Reviewing staged changes") and one line warning about any out-of-scope changes if applicable (e.g. "⚠ Staged changes are not part of this PR and will not be reviewed").

## 1. Identify requirements and intention of changes

Check the following sources in order:
   - Conversation context and user messages
   - If PR: read PR title and description for intent, linked workitems, and acceptance criteria
   - If branch: look for branch name, commit messages

## 2. Summarize
   
Fetch the diff determined in step 0. Return: title or scope description, requirements and intention of changes from step 1, summary of changes.

## 3. Launch ALL 4 Review Agents IN PARALLEL and wait for results

Input to all: title, description, diff.
./agents/spec-compliance.md
./agents/bugs-and-regression.md
./agents/code-quality.md
./agents/design-quality.md

## 4. Validate (parallel subagents, one per issue)
Input: title, description, issue description.
Confirm issue is real
Validate code facts (e.g. symbol actually undefined) and repository rule scope.

## 5. Filter
Retain only confirmed issues.

## 6. Output
</workflow>

<output>

```markdown
## Code Review — {PR title or changes scope description}

### Summary

{2–3 sentences: what changed and overall verdict}

### Issues

**High**

> No issues found. (if no issues identified, otherwise list as below)

- **H{n} | {Title}** · `{file}:{Lstart-Lend}`
  {State WHY it is a problem. If confidence is medium, explain why. Provide enough detail that an engineer reading this as a PR inline comment immediately grasps the issue and how to fix it. If the fix is small and self-contained, include a suggestion block with the exact code to commit.}
  {new line between issues}

**Medium**

- **M{n} | {Title}** · `{file}:{Lstart-Lend}`
  {same format}

**Low**

- **L{n} | {Title}** · `{file}:{Lstart-Lend}`
  {same format}

### Verdict

**Approve** | **Approve with suggestions** | **Request changes** — {one-sentence justification}
```

</output>
