---
name: handoff-session
description: "Use when the user asks to summarize, compact, hand off, or create an end-of-session summary for the next agent."
---

<purpose>
Produce a terse, repeatable session handoff that lets a fresh AI coding agent continue from the current conversation without losing important context.
</purpose>

<audience>
The audience is the next AI agent, not a stakeholder or end user.
</audience>

<objective>
Summarize what happened, what changed, what remains open, and exactly where the next agent should continue. The next agent should be able to use the summary first, then inspect named files or commands as needed.
</objective>

<workflow>
1. Review the whole conversation, not only the latest turns.
2. Identify session facts that affect future work:
   - user goal, constraints, and important context;
   - decisions made and rationale that affects future work;
   - files created, edited, or repeatedly referenced;
   - plans, task lists, notes, or memory artifacts explicitly used;
   - commands run for verification and their outcomes;
   - background jobs, dev servers, ports, terminals, worktrees, or branches that remain relevant;
   - deferred work, unresolved user questions, and known risks.
3. Use tool state only when it is already known from the session or needed to confirm a specific known item.
4. Produce the handoff in chat only.
</workflow>

<decision_rules>
- Prefer precise session facts over general project descriptions.
- Include only items touched, created, decided, started, or made relevant in this session.
- If a required section has no information, write `none`; never omit required sections.
- Use absolute paths for files, directories, worktrees, plans, and memory artifacts when a path is known. If only a relative path is known, resolve it against the current working directory before writing it.
- If a plan or task list drove the session, list it first under `Key files for next session`.
- For running state, include how the next agent can stop or resume the process when that information is known.
- If evidence is uncertain or unavailable, say so briefly instead of inventing state.
</decision_rules>

<constraints>
- Chat output only. Do not write a handoff file.
- Do not update memory or project notes from this skill.
- Do not run broad discovery commands such as repository-wide file scans, `git log`, or full process audits solely for the handoff.
- Do not include a retrospective, praise, or stakeholder-style status language.
- Do not recommend a roadmap. Provide only the single most likely next action in `Pick up here`.
- Keep the tone terse, concrete, and engineering-focused.
</constraints>

<output_format>
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

## Key facts for next session
- <fact> — <why it matters>
- ...

## Deferred + open questions
- Deferred: <item> — <why pushed to later>
- Open: <question needing user input> — <context>

## Pick up here
<leave empty, will be filled by the user>
```
</output_format>

<completion_criteria>
Before sending, verify that:
- Every required section is present.
- No section contains invented or assumed state.
- All known file paths are absolute.
- Running processes, ports, worktrees, and branches are explicit or marked `none`.
- Verification commands are specific and tied to expected or observed outcomes.
- `Pick up here` contains one focused next action.
</completion_criteria>
