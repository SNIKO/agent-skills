# Generic Prompt Writer

Use this reference when the target model or provider is unknown, mixed, or intentionally provider-neutral. It combines OpenAI and Anthropic prompt-writing practices. When practices differ, prefer the Anthropic-compatible form because it is explicit, structured, and portable across providers.

## Core Rule

If the user has not provided enough information to know the task, audience, inputs, output format, or prompt use context, ask only for the missing details that materially affect the prompt. Otherwise, infer from the user request and available context.

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
- provider-neutral behavior that works across unknown or mixed models

Do not invent operational details that belong outside the prompt, such as API calls, SDK snippets, model IDs, or runtime parameters.

## Workflow

1. Identify the prompt type: simple task, structured output, long-context, developer or system prompt, agent prompt, reusable template, eval prompt, skill prompt, grounded or retrieval prompt, creative drafting prompt, or frontend prompt.
2. State the objective in plain language before adding detailed constraints.
3. Choose the lightest structure that makes the prompt reliable.
4. Separate instructions, context, examples, inputs, and output rules with clear Markdown sections or XML tags.
5. Prefer XML tags when the prompt mixes multiple content types, long documents, examples, or dynamic user input.
6. State scope explicitly. If a rule applies to every item, source, example, file, step, tool call, or output section, say so.
7. Control the output directly: structure, fields, order, length, tone, citation style, uncertainty handling, and failure behavior.
8. Add examples only when they clarify format, tone, classification boundaries, edge cases, tool behavior, or visual direction.
9. Add evidence rules, retrieval budgets, citation requirements, and missing-evidence behavior when answers must be grounded.
10. Add validation instructions when the output can be checked against explicit success criteria.
11. Remove vague, redundant, decorative, or conflicting instructions.

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

Use for extraction, classification, comparison, grading, or deterministic formatting:

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

When JSON or another machine-readable schema is required, specify required keys, allowed values, null behavior, ordering, and whether extra keys are allowed.

### Long-Context Prompt

Place large source material before the final request. Put source identifiers and metadata near each source:

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
- Use only the documents above unless outside knowledge is explicitly allowed.
- Extract or identify relevant passages before answering when evidence grounding matters.
- Compare sources when claims conflict.
- Cite the source identifier for each important claim.
</instructions>

<task>
Answer the following question: ...
</task>
```

If supporting quotes should not appear in the final answer, ask the model to use them for grounding and then return only the requested output format.

### Developer Or System Prompt

Use when writing persistent behavior for an assistant or application:

- Start with identity and purpose only when they shape behavior.
- Define authority boundaries and what user input can configure.
- Separate core behavior, constraints, evidence rules, personality, collaboration style, and output policy.
- State how to handle missing context, unsafe requests, conflicts, and unsupported claims.
- Keep stable policy text before variable product data or conversation context when caching or reuse matters.
- Avoid depending on prefilled assistant messages; put required starts, continuations, schemas, or XML boundaries in explicit prompt instructions.

### Agent Prompt

Use when the model can plan, use tools, inspect files, research, edit, or complete multi-step work:

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
- Run independent tool calls in parallel; run dependent steps sequentially.
- Work directly for simple sequential tasks.
- Ask for confirmation before destructive, hard-to-reverse, or externally visible actions.
- Keep changes focused on the requested task.
- Remove temporary files before finishing unless they are part of the requested deliverable.
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
- Specify whether to report every candidate issue or only issues above a concrete bar.
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

Keep skill prompts procedural and reusable. Put trigger behavior in skill frontmatter when the host system supports it.

### Grounded Or Retrieval Prompt

Use when the answer must rely on external, retrieved, or provided evidence:

- State which claims require citations, such as policies, dates, prices, statistics, named owners, product capabilities, legal or medical guidance, and source-specific facts.
- Define acceptable sources and what counts as enough evidence.
- Add a retrieval budget: start with the smallest useful source read or search, then continue only when required facts are missing or exhaustive coverage is requested.
- Treat missing evidence as unknown, not as proof that a claim is false.
- Ask for the smallest missing field when evidence is required and unavailable.
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
- Ask for familiar controls, icons, and existing component patterns where relevant.
- Give concrete visual direction instead of relying on vague language such as "clean" or "minimal."
- Require rendering or screenshot inspection before finalizing when the environment can run the UI.

## Formatting Guidance

Use Markdown headings and bullets for readable instruction hierarchy. Use XML tags for separating large or mixed data blocks. Prefer XML tags when prompt content includes multiple roles for text, such as instructions, context, examples, documents, and user input.

Use JSON or schema-like blocks only when the output must be machine-readable. When JSON is required, specify required keys, allowed values, null behavior, and whether extra keys are allowed.

## Wording Rules

- Be clear and direct. Say exactly what should be done and what the final output should look like.
- Prefer affirmative instructions before negative constraints.
- Make each instruction observable or behavior-changing.
- Replace "be helpful", "be professional", and "be accurate" with concrete behavior.
- Use `must`, `never`, `only`, and `always` only for true invariants.
- Give a role when role, expertise, tone, or judgment criteria affect the result.
- Add context explaining why constraints matter when it improves targeting or compliance.
- Define uncertainty behavior for missing, ambiguous, conflicting, or out-of-scope evidence.
- Avoid chain-of-thought or hidden reasoning requests. Ask for concise rationale, checks, evidence, or tradeoffs when they should be visible.
- Avoid prompt patterns that depend on a prefilled final assistant message. Put required starts, continuations, schemas, or XML boundaries in explicit instructions.
- Preserve the user's required terminology, voice, and domain constraints unless they weaken correctness.

## Examples

Use examples when consistency matters. Prefer 3-5 examples for classification, extraction, style matching, tool behavior, or edge cases. Wrap examples in `<examples>` and `<example>` tags, make them realistic and varied, and avoid examples that accidentally teach unwanted patterns.

## Cache-Friendly Arrangement

When a prompt will be reused, arrange it so stable content changes least often:

1. Identity, objective, standing rules, style, and output contract.
2. Stable domain context, policies, and examples.
3. Variable task inputs, retrieved context, conversation snippets, or user data.

Do not bury instructions inside variable content.

## Provider-Specific Notes

- Use explicit, portable instructions that do not assume a specific model family.
- Prefer XML tags for mixed content because they remain readable and generally portable across providers.
- When OpenAI and Anthropic guidance differs, choose the Anthropic-compatible form.
- Keep provider names, model IDs, and self-identification out of generic prompts unless the user supplies them.
- Use outcome-oriented reasoning guidance instead of hidden chain-of-thought requests.

## References

No additional provider reference files are required for generic prompts.

## Output Rules

When responding to the user:

- Provide the finished prompt first.
- Include a short note only when it helps explain important structure choices.
- Preserve placeholders such as `{{INPUT}}`, `{{DOCUMENTS}}`, or `{{USER_GOAL}}` when the prompt is meant to be reusable.
- Do not invent operational details that belong outside the prompt.
- If revising an existing prompt, keep the user's intended behavior unless it conflicts with generic prompt-writing best practices or the user's current request.

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
