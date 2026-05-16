---
name: git
description: "Use when user asks for git operations: branch creation, committing changes, creating pull requests."
---

<constraints>
- if branch not created, create branch first before committing or opening PR
- if changes not committed, commit changes first before opening PR
</constraints>

<repository_rules>
Check for the followings for repository specific rules and instructions related to git names/messages conventions and workflows:
- AGETNS.md
- CLAUDE.md
- .agents/

Merge them with the general instructions below, with repository rules taking precedence in case of conflict.
</repository_rules>

<workflows>

## Commit

Commit message format:

```bash
<type>[scope]: <description>

<optional body>

<optional footer>
```

### Rules
- One logical change per commit, if many files changed, create multiple commits with clear scope and messages
- Present tense: "add" not "added"
- Imperative mood: "fix bug" not "fixes bug"
- Reference issues: `Closes #123`, `Refs #456`
- Keep description under 72 characters
- NEVER update git config
- NEVER run destructive commands (`--force`, hard reset) without explicit request
- NEVER skip hooks (`--no-verify`) unless user asks
- NEVER force push unless the user explicitly requests it
- NEVER commit to `master`/`main` unless repository rules allow it and the user confirms it

## Branch

Branch naming format: `{type}/{semantic-name}`

### Rules
- NEVER delete or rename existing branches without explicit user request
- NEVER force-push or reset master/main
- NEVER run destructive commands without confirmation
- If branch already exists, report this to the user and ask how to proceed

## Pull Request

Title format: `{type}[scope]: {description}`

Description format:

```markdown
## Summary
{1–3 sentences explaining what this PR does and its scope}

## Changes
- {key change 1, do not repeat file changes, focus on observable behavior changes}
- {key change 2, do not repeat file changes, focus on observable behavior changes}
- {more as needed — keep each bullet atomic and concrete}

## Why
{motivation: what problem does this solve, or what goal does it achieve.}

## Breaking Changes
{describe what breaks and how consumers should migrate. Omit if no breaking changes.}
```


Git workflows for branching, committing, and opening pull requests, with automatic convention detection and branch-safety checks.

## Types

**Change types**:

| Type | Purpose |
|---|---|
| `feat` | New feature |
| `fix` | Bug fix |
| `docs` | Documentation only |
| `style` | Formatting/style (no logic) |
| `refactor` | Code refactor |
| `perf` | Performance improvement |
| `test` | Add/update tests |
| `build` | Build system/dependencies |
| `ci` | CI/config changes |
| `chore` | Maintenance/misc |

</workflows>