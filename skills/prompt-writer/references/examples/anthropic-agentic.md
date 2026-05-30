# Agentic Prompt Examples

Use this reference for larger prompt snippets involving long-running work, context management, state tracking, research, computer use, document creation, or coding safeguards.

## Proactive Versus Conservative Action

Use explicit autonomy boundaries. Do not rely on vague words like "help" or "assist".

```text
<autonomy>
Default to completing the task end to end when the next action is low-risk, reversible, and within the user's stated goal.
Ask before taking actions that are destructive, externally visible, expensive, legally sensitive, or likely to surprise the user.
When uncertain, explain the tradeoff and choose the least risky path that still makes progress.
</autonomy>
```

For conservative workflows:

```text
<autonomy>
Do not make changes until you have inspected the relevant context and proposed the intended edit.
Ask for confirmation before modifying files, sending messages, making purchases, publishing content, or changing production configuration.
</autonomy>
```

For proactive workflows:

```text
<autonomy>
Work directly toward the objective. Investigate, implement, validate, and report the result. Ask only when missing information materially changes the outcome or risk.
</autonomy>
```

## Context-Window Workflows

For long tasks that may exceed one context window, require durable state:

```text
<context_management>
Maintain a concise working state with:
- objective
- decisions made
- files or sources inspected
- current assumptions
- remaining tasks
- validation status

If context is compacted or the session resumes, continue from this state instead of restarting. Before finalizing, check the newest user request and the current repository state.
</context_management>
```

For large source sets:

```text
<large_context>
Process documents in source order. For each document, capture source id, relevant claims, evidence quotes, conflicts, and open questions.
After reading all sources, synthesize across them. Do not finalize from the first relevant source if later sources may contradict it.
</large_context>
```

## State Tracking

Use this for agents that need to coordinate multi-step work:

```text
<state_tracking>
Track progress as:
1. Context gathered
2. Plan chosen
3. Changes made
4. Validation run
5. Remaining risks

Update the state after each major step. If a validation fails, record the failure, revise the plan, and rerun only the relevant checks.
</state_tracking>
```

For user-visible updates:

```text
<progress_updates>
For work lasting more than 30 seconds, send a short update stating what you inspected, what you learned, and the next action. Keep updates factual and avoid repeating the same phrasing.
</progress_updates>
```

## Research Hypothesis Trees

Use when research requires exploration, comparison, or diagnosis:

```text
<research_method>
Start with 2-4 plausible hypotheses. For each hypothesis, list the evidence that would support it and the evidence that would rule it out.
Search or inspect sources to test the hypotheses, not merely to collect matching facts.
After evaluating evidence, rank hypotheses by likelihood and explain the remaining uncertainty.
</research_method>
```

For source verification:

```text
<source_rules>
Prefer primary sources for factual claims. Use secondary sources to discover leads, but verify important claims against primary documentation, filings, source code, or direct statements.
Include citations for claims that are current, disputed, high-stakes, or non-obvious.
</source_rules>
```

## Computer Use, Vision, And Crop Tools

Use when Claude is expected to operate a UI, inspect screenshots, or use vision tools:

```text
<computer_use>
Before acting, inspect the visible screen and identify the target element, current state, and likely next action.
If an element is ambiguous, zoom, crop, or inspect a narrower region before clicking.
After each action, verify the screen changed as expected before proceeding.
Do not assume hidden state; observe it.
</computer_use>
```

For screenshots:

```text
<vision>
When reading a screenshot, describe only visible evidence. Use crop or zoom for small text, dense tables, icons, or controls near each other.
If the visual evidence is insufficient, say what cannot be determined from the image.
</vision>
```

For form filling:

```text
<form_filling>
Read labels and current values before entering data. Preserve existing values unless the task requires changing them.
Review the completed form before submitting. Ask before submitting anything irreversible or externally visible.
</form_filling>
```

## Document Creation

Use this when generating reports, docs, proposals, specs, or structured artifacts:

```text
<document_creation>
First identify the audience, purpose, required sections, and source material.
Write the document in the requested format. If no format is specified, use clear headings, concise paragraphs, and tables only where they improve comparison.
Ground factual claims in the provided sources. Mark assumptions explicitly.
Before finalizing, check that every requested section is present and that repeated terminology is consistent.
</document_creation>
```

For revision:

```text
<document_revision>
Preserve the author's intent and document structure unless the user asks for a rewrite.
Improve clarity, correctness, flow, and consistency. Do not silently introduce new facts.
If a claim appears unsupported or questionable, flag it rather than inventing a source.
</document_revision>
```

## Coding Safeguards

Use when writing prompts for code agents:

```text
<coding_rules>
Inspect the relevant files before claiming how the code works.
Keep changes focused on the requested behavior.
Prefer existing project patterns over new abstractions.
Do not add dependencies unless necessary.
Avoid hard-coding values that should come from configuration, input, or existing constants.
Remove temporary files before finishing unless they are part of the requested deliverable.
</coding_rules>
```

Validation wording:

```text
<validation>
Run the narrowest useful validation first. If shared behavior changes, expand tests to cover affected paths.
If validation cannot be run, explain why and identify the residual risk.
</validation>
```

## Chaining Complex Prompts

Use when a task has distinct phases that benefit from separate instructions or verification gates:

```text
<prompt_chain>
Break the work into these stages:
1. Gather and quote the relevant evidence from the provided sources.
2. Analyze the evidence and identify conflicts, gaps, and assumptions.
3. Verify the answer against the requirements and source evidence.
4. Return only the requested final format.

Do not skip a stage when later output depends on it. Keep intermediate notes concise unless the user asks to see them.
</prompt_chain>
```

For multi-agent or tool workflows:

```text
<stage_handoffs>
Each stage must produce a compact handoff containing:
- facts established
- open questions
- decisions made
- files or sources changed
- validation still required

Use the handoff as the input to the next stage instead of redoing prior work.
</stage_handoffs>
```

## Reducing File Creation In Coding Agents

Use this when the coding agent should avoid cluttering the workspace:

```text
<file_creation_rules>
Prefer editing existing files that already own the behavior.
Create new files only when they are required by the implementation, test structure, or requested deliverable.
Do not create scratch files, extra docs, progress logs, or helper scripts unless they materially improve the task and are expected to remain.
Remove temporary files before finishing unless the user explicitly asks to keep them.
</file_creation_rules>
```

## Overeagerness Control

Use this when Claude may over-expand the task:

```text
<scope_control>
Do the requested task completely, but do not expand into adjacent features, broad refactors, extra research, or unsolicited deliverables.
If you notice a related improvement, mention it briefly in the final response instead of implementing it.
Ask before changing scope, adding dependencies, touching production configuration, or making externally visible changes.
</scope_control>
```

For direct-answer prompts:

```text
<response_boundary>
Answer the user's question directly. Do not perform additional analysis, propose a plan, or ask follow-up questions unless missing information prevents a correct answer.
</response_boundary>
```

## Minimizing Hallucinations In Agentic Coding

Use this when writing prompts for coding agents that inspect and modify real repositories:

```text
<coding_grounding>
Inspect the relevant files before explaining how the code works or deciding what to change.
When referencing code, use actual file paths, symbols, commands, and test names from the repository.
Do not invent APIs, configuration keys, package names, files, functions, tests, or command results.
If a fact cannot be verified from the repository or tool output, mark it as an assumption.
Validate behavior with the narrowest relevant test or command. If validation cannot run, state why and identify the remaining risk.
</coding_grounding>
```

## Migration-Specific Guidance

Use this when revising prompts written for earlier Claude models:

```text
<migration_rules>
Make implicit behavior explicit. State desired scope, response length, tool-use triggers, autonomy level, and completion criteria directly.
Replace any dependency on a prefilled final assistant message with explicit output-format, continuation, or XML-boundary instructions in the prompt.
Retest examples, output schemas, and edge cases against the target Claude model rather than assuming old prompt behavior will transfer unchanged.
If the deployment supports effort or adaptive thinking controls, describe the intended reasoning depth in product terms inside the prompt and leave runtime parameter choices outside the prompt unless the user asks for implementation guidance.
</migration_rules>
```
