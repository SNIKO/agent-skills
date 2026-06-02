---
name: handoff-session
description: Summarize current chat session. Use when the user says "session handoff", "hand off", "handoff", "handoff summary", "summarize", "compact" or otherwise asks for a structured end-of-session summary. 
---

# Session Handoff

## Purpose

Produce a repeatable end-of-session context handoff so the user can clear or replace the current agent without losing continuity. The audience is the next agent, not a stakeholder. The next agent should be able to continue from the summary alone, then inspect named files or commands as needed.

## Workflow

1. Review the whole conversation, not only the latest turns.
2. Identify what changed during this session:
   - User goal, constraints, and important context.
   - Decisions made and rationale that affects future work.
   - Files created, edited, or repeatedly referenced.
   - Plans, task lists, notes, or memory artifacts explicitly used this session.
   - Commands run for verification and their outcomes.
   - Background jobs, dev servers, ports, terminals, worktrees, or branches that remain relevant.
   - Deferred work, unresolved user questions, and known risks.
3. Use tool state only when it is already known from the session or needed to confirm a specific known item. Do not perform a broad filesystem, git, or process audit just to fill the handoff.
4. Produce the handoff in chat only.

## Decision Rules

- Prefer precise session facts over general project descriptions.
- Include only items touched, created, decided, started, or made relevant in this session.
- If a section has no information, write `none`; never omit required sections.
- Use absolute paths for files, directories, worktrees, plans, and memory artifacts when a path is known. If only a relative path is known, resolve it against the current working directory before writing it.
- If a plan or task list drove the session, list it first under "Key files for next session".
- For running state, include how the next agent can stop or resume the process when that information is known.
- If evidence is uncertain or unavailable, say so briefly instead of inventing state.

## Constraints

- Chat output only. Do not write a handoff file. Do not update memory or project notes from this skill.
- Do not run broad discovery commands such as repository-wide file scans, `git log`, or full process audits solely for the handoff.
- Do not include a retrospective, praise, or stakeholder-style status language.
- Do not recommend a long roadmap. Provide only the single most likely next action in "Pick up here".
- Keep the tone terse, concrete, and engineering-focused.

## Output

Use exactly this structure every time:

```text
# Session Handoff — <one-line title of what this session was about>

## Where it started
<2-3 sentences: what the user asked for, key framing or constraints that emerged>

## Decisions locked + what shipped
- <decision or change> — <why it matters, and where it lives if applicable>
- ...

## Key files for next session
- `<absolute path>` — <why the next agent should read this first>
- Plan file: `<absolute path>` — <if a plan drove the session, otherwise `none`>
- Notes or memory touched: `<absolute path>` — <if any, otherwise `none`>

## Running state
- Background processes: <IDs, commands, purpose, and stop command if known> — or `none`
- Dev servers / ports: <URL or port, purpose, and stop command if known> — or `none`
- Worktrees / branches: <absolute path and branch name if relevant> — or `none`

## Verification — how to confirm things still work
- `<command>` — <expected or observed outcome>
- ...

## Deferred + open questions
- Deferred: <item> — <why pushed to later>
- Open: <question needing user input> — <context>

## Pick up here
<1-2 sentences: the single most likely next action for a fresh agent>
```

## Verification

Before sending, verify that:

- [ ] Every required section is present.
- [ ] No section contains invented or assumed state.
- [ ] All known file paths are absolute.
- [ ] Running processes, ports, worktrees, and branches are explicit or marked `none`.
- [ ] Verification commands are specific and tied to expected or observed outcomes.
- [ ] The final "Pick up here" section contains one focused next action.
