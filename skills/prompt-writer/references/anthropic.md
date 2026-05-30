# Anthropic Prompt Writer

Use this reference for Anthropic and Claude prompts. Keep the prompt clear, explicit, XML-friendly, and scoped to the task. Claude responds well to direct instructions, relevant context, concrete examples, and explicit rules about where an instruction applies.

## Core Rule

If the user has not provided enough information to know the task, audience, inputs, output format, or prompt use context, ask only for the missing details that materially affect the prompt. Otherwise, infer from the user request and context.

## Scope

Focus on prompt writing:

- structure and section order
- wording and instruction priority
- Markdown, XML tags, JSON schemas, or other text formats
- examples and counterexamples
- dynamic context boundaries
- output shape, style, and stop conditions
- personality, collaboration style, and response-length instructions
- retrieval budgets, citation rules, and missing-evidence behavior
- validation loops and task-specific checks
- provider-specific Anthropic and Claude prompt behavior

Do not invent operational details that belong outside the prompt, such as API calls, SDK snippets, model IDs, or runtime parameters, unless the user specifically asks for them.

## Workflow

1. Identify the prompt type: simple task, structured output, long-context, developer or system prompt, agent prompt, reusable template, eval prompt, skill prompt, grounded or retrieval prompt, creative drafting prompt, or frontend prompt.
2. Clarify the objective in plain language before adding constraints.
3. Choose the lightest structure that makes the prompt reliable.
4. Use descriptive XML tags when the prompt mixes instructions, context, examples, inputs, documents, and output rules.
5. State scope explicitly. If an instruction applies to every item, section, example, source, file, step, tool call, or output section, say so.
6. Add relevant context and motivation when it improves compliance, judgment, or tone.
7. Control the output directly: structure, fields, order, length, tone, citation style, uncertainty handling, and failure behavior.
8. Include 3-5 examples when consistency matters. Wrap examples in `<examples>` and `<example>` tags, and make them realistic, varied, and close to the real use case.
9. For prompts that replace legacy prefilled assistant messages, move the constraint into explicit instructions, XML output boundaries, structured output requirements, or continuation instructions in the user prompt.
10. Remove vague, redundant, decorative, or conflicting instructions.

## Common Structure

Use this order for medium or complex prompts, omitting sections that do not matter:

```text
<role>
[The model's job, domain, and operating context.]
</role>

<objective>
[The concrete outcome the model must produce.]
</objective>

<context>
[Only context that changes behavior or correctness.]
</context>

<requirements>
- [Observable requirement]
- [Evidence, constraint, or quality bar]
</requirements>

<instructions>
[Decision rules and task behavior. Prefer destination and constraints over unnecessary process.]
</instructions>

<examples>
  <example>
  [Use only when examples materially improve reliability.]
  </example>
</examples>

<output_format>
[Format, fields, length, order, tone, and required labels.]
</output_format>

<completion_criteria>
[When to answer, ask, retry, fallback, abstain, validate, or stop.]
</completion_criteria>
```

For short prompts, compress the same information into one paragraph or a few bullets. Do not force a large template onto a simple task.

## Prompt Type Patterns

### Simple Task Prompt

Use for one-step writing, summarization, classification, transformation, or direct answers:

```text
<task>
State the exact task.
</task>

<context>
Add only the context needed to do the task well.
</context>

<requirements>
- Specify the desired output.
- Specify constraints that affect correctness, tone, or completeness.
</requirements>
```

### Structured Output Prompt

Use for extraction, classification, comparison, grading, or repeatable output:

```text
<role>
You are [specific role relevant to the task].
</role>

<task>
Perform [operation] on every item in <input>.
</task>

<rules>
- Apply these rules to every item, not only the first item.
- If information is missing, use [explicit fallback such as null, unknown, or a short explanation].
- Do not infer facts that are not supported by the input.
</rules>

<output_format>
Return [JSON/table/bullets/prose] with these fields in this order:
1. ...
2. ...
</output_format>

<input>
{{INPUT}}
</input>
```

### Long-Context Prompt

Place large source material before the final request. Put metadata near each source:

```text
<documents>
  <document index="1">
    <source>...</source>
    <document_content>
    ...
    </document_content>
  </document>
</documents>

<instructions>
- Use only the documents above unless the user explicitly allows outside knowledge.
- Extract and quote the relevant passages first when the answer depends on finding evidence inside long documents.
- Compare sources when claims conflict.
- Cite the source identifier for each important claim.
</instructions>

<task>
Answer the following question: ...
</task>
```

If the final output should not include supporting quotes, ask Claude to use the quotes for grounding and then return only the requested answer format.

### Developer Or System Prompt

Use when writing persistent behavior for an assistant or application:

- Start with identity and purpose only when they shape behavior.
- Define authority boundaries and what user input can configure.
- Separate core behavior, constraints, evidence rules, personality, collaboration style, and output policy.
- State how to handle missing context, unsafe requests, conflicts, and unsupported claims.
- Avoid depending on prefilled assistant messages; put required starts, continuations, schemas, or XML boundaries in explicit prompt instructions.

### Agent Prompt

Use when Claude is expected to act over multiple steps, use tools, inspect files, research, or make changes:

```text
<role>
You are [specific agent role].
</role>

<objective>
State the concrete end state.
</objective>

<context>
Include user goals, repo/product/domain constraints, audience, and known inputs.
</context>

<operating_rules>
- Investigate before making claims about unseen files, documents, data, or external facts.
- Use tools when they are needed to verify facts, inspect state, or complete the task.
- If multiple independent tool calls are needed, run them in parallel; run dependent steps sequentially.
- Work directly for simple sequential tasks.
- Use subagents only when work can run independently, in parallel, or with isolated context.
- Ask for confirmation before destructive, hard-to-reverse, or externally visible actions.
- Keep changes focused on the requested task.
- If temporary files are created for iteration, remove them before finishing unless they are part of the requested deliverable.
</operating_rules>

<reasoning_guidance>
Use deeper reasoning for complex, multi-step, ambiguous, or high-stakes work. For simple tasks, respond directly. After tool results, evaluate whether the evidence is sufficient before taking the next action.
</reasoning_guidance>

<progress_updates>
Describe when and how to update the user during longer work.
</progress_updates>

<completion_criteria>
Define what done means, including validation or tests when relevant.
</completion_criteria>

<final_response>
Specify the final answer format and what evidence or file references to include.
</final_response>
```

### Reusable Prompt Template

Put reusable instructions first and volatile variables later under clear labels:

```text
<task>
Rewrite {{source_text}} for {{audience}} while preserving the original claim set.
</task>

<constraints>
- Do not add facts, numbers, names, dates, or promises.
- If a required detail is missing, use [placeholder].
</constraints>

<output_format>
{{output_format}}
</output_format>
```

### Eval Prompt

Use when grading, classifying, comparing, or judging another output:

- Define the exact rubric and scoring scale.
- Make labels mutually exclusive when possible.
- Specify whether to report every candidate issue with confidence and severity, or only issues above a concrete bar.
- Require evidence from the evaluated text when judging failures.
- Include tie-breakers and abstain rules.
- Keep the final output structured enough to parse or compare.

### Skill Prompt

Use when writing a reusable skill consumed by another agent:

```text
<purpose>
Define the narrow capability the skill provides.
</purpose>

<trigger>
State exactly when the skill should be used.
</trigger>

<workflow>
List the steps the agent should follow.
</workflow>

<decision_rules>
Describe how to choose between valid prompt structures or approaches.
</decision_rules>

<constraints>
State boundaries, exclusions, and failure behavior.
</constraints>

<output>
Specify what the agent should produce for the user.
</output>
```

Keep skill prompts procedural and reusable. Avoid user-facing tutorial language unless the skill's job is to write tutorials.

### Grounded Or Retrieval Prompt

Use when the answer must rely on external, retrieved, or provided evidence:

- State which claims require citations.
- Define acceptable sources and what counts as enough evidence.
- Ask Claude to identify or quote relevant source passages before answering when long-context evidence grounding matters.
- Compare sources when claims conflict.
- Treat missing evidence as unknown, not as proof that a claim is false.
- Stop once the answer can support the core request with useful evidence and citations.

### Creative Drafting Prompt

Use when creating slides, copy, summaries for sharing, talk tracks, launch notes, or narrative framing:

- Distinguish source-backed facts from creative wording.
- Require evidence for concrete product, customer, metric, roadmap, date, capability, competitive, or outcome claims.
- Do not invent names, first-party data, metrics, roadmap status, customer outcomes, or capabilities for polish.
- If support is thin, write a useful generic draft with placeholders or clearly labeled assumptions.

### Frontend Prompt

Use when writing prompts for frontend generation or UI changes:

- Include product context, target user, design-system constraints, expected states, interaction behavior, accessibility, responsive behavior, and visual inspection requirements.
- Give a concrete visual direction for palette, type, layout, spacing, motion, and interactions instead of relying on vague terms such as "clean", "modern", or "minimal."
- Ask for distinct visual directions before implementation when variety matters.
- Require rendering or screenshot inspection before finalizing when the environment can run the UI.

## Formatting Guidance

Use XML tags to separate instructions, context, examples, inputs, documents, and output rules. Use descriptive tag names consistently, and nest tags only when the content has a natural hierarchy.

If plain prose is the desired output, say that directly. If the prompt itself uses heavy Markdown, Claude may mirror that style.

When JSON or another machine-readable schema is required, specify required keys, allowed values, null behavior, ordering, and whether extra keys are allowed.

## Wording Rules

- Be clear and direct. Say exactly what should be done and what the final output should look like.
- Prefer positive instructions and concrete examples over broad "do not" lists.
- Use sequential numbered steps or bullets when order or completeness matters.
- Give Claude a role when role, expertise, tone, or judgment criteria affect the result.
- Add concise context explaining why constraints matter when it improves targeting or compliance.
- Specify desired response length and verbosity when the product or task depends on it.
- Define uncertainty behavior for missing, ambiguous, conflicting, or out-of-scope evidence.
- For reasoning guidance, prefer outcome-oriented instructions such as "reason through the tradeoffs before answering" over rigid step-by-step chains unless the task requires an auditable process.
- For tool use, state when tools are required, when direct reasoning is enough, and whether the agent should default to action or advice.
- Avoid prompt patterns that depend on prefilled assistant messages.
- Minimize coding hallucinations by requiring file inspection before claims, evidence-backed explanations, validation against real tests or commands, and no invented APIs, paths, functions, or configuration.

## Examples

Use examples when consistency matters. Prefer 3-5 examples for classification, extraction, style matching, tool behavior, or edge cases. Wrap them in `<examples>` and `<example>` tags, make them realistic and varied, and avoid examples that accidentally teach unwanted patterns.

## Cache-Friendly Arrangement

When a prompt will be reused, arrange it so stable content changes least often:

1. Identity, objective, standing rules, style, and output contract.
2. Stable domain context, policies, and examples.
3. Variable task inputs, retrieved context, conversation snippets, or user data.

Do not bury instructions inside variable content.

## Provider-Specific Notes

- Claude can follow instructions literally. If an instruction applies broadly, say so explicitly.
- Claude may calibrate response length by task complexity. If a product needs a specific verbosity, state it directly and prefer positive examples of the desired length.
- If comprehensive or above-and-beyond behavior is desired, request it explicitly.
- For parallel tools, say to parallelize only independent operations and run dependent operations sequentially.
- For subagents, define when delegation is useful. Simple sequential work should usually stay direct.
- If the assistant should identify itself, include self-knowledge explicitly:

```text
The assistant is Claude, created by Anthropic. The current model is Claude Opus 4.8.
```

## References

Read these only when the prompt type needs the extra detail:

- `examples/anthropic-agentic.md`: larger examples for proactive/conservative action, context-window workflows, state tracking, research hypothesis trees, document creation, computer-use/vision/crop-tool guidance, and coding safeguards.
- `examples/anthropic-frontend.md`: larger examples for markdown control, plain-text math, frontend aesthetics, and visual direction prompts.

## Output Rules

When responding to the user:

- Provide the finished prompt first.
- Include a short note only when it helps explain important structure choices.
- Preserve placeholders such as `{{INPUT}}`, `{{DOCUMENTS}}`, or `{{USER_GOAL}}` when the prompt is meant to be reusable.
- Do not invent operational details that belong outside the prompt.
- If revising an existing prompt, keep the user's intended behavior unless it conflicts with Anthropic prompt-writing best practices or the user's current request.

## Quality Check

Before finalizing a prompt, verify:

- The intended use and prompt type are clear.
- The desired outcome appears before detailed process.
- The output contract is specific enough for the task.
- Context boundaries are explicit.
- Scope is explicit when rules apply across every item, source, example, file, step, tool call, or output section.
- Evidence, retrieval budget, citation rules, and missing-evidence behavior are defined when grounding is required.
- Personality, collaboration style, and length guidance are explicit when they matter and absent when they do not.
- Stable instructions appear before volatile inputs when the prompt is reusable.
- Examples, if present, are necessary and representative.
- Stop rules and validation checks are present when the task can otherwise loop, over-search, or produce unverifiable output.
- Instructions do not conflict, duplicate each other, or mix hard invariants with discretionary judgment calls.
