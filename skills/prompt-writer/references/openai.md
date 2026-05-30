# OpenAI Prompt Writer

Use this reference for OpenAI prompts. Keep prompt artifacts clear, compact, task-specific, easy to evaluate, and explicit about success criteria and evidence. Prefer outcome-first prompts with light process unless the process is truly required.

## Core Rule

If the prompt's purpose or use context is unclear, ask the smallest question needed: what the prompt will be used for and what kind of output it must produce. Otherwise, infer from the user request, surrounding artifact, or repository context.

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
- cache-friendly placement of stable prompt text

Do not add API calls, SDK snippets, model IDs, runtime parameters, or product claims unless the user specifically asks for them or they are part of the prompt text.

## Workflow

1. Identify the prompt type: simple task, structured output, long-context, developer or system prompt, agent prompt, reusable template, eval prompt, skill prompt, grounded or retrieval prompt, creative drafting prompt, or frontend prompt.
2. Extract the intended task, user-visible outcome, audience, constraints, input sources, evidence needs, output format, validation options, and failure cost.
3. Choose the lightest structure that makes the prompt reliable.
4. Put stable reusable instructions and stable examples before volatile user input, retrieved context, or examples that change often.
5. Separate instructions from data with Markdown sections or XML tags when boundaries matter.
6. Use strict words such as `must`, `never`, `only`, and `always` only for true invariants.
7. Add personality and collaboration instructions only when they shape the user experience; keep them short and separate from task rules.
8. Add examples only when they clarify format, tone, classification boundaries, edge cases, or tool behavior.
9. Add retrieval budgets, citation rules, and missing-evidence behavior when answers need grounding.
10. Add validation instructions when the output can be checked against explicit success criteria.
11. Remove duplicated rules, decorative persona text, vague quality claims, chain-of-thought requests, and process narration that does not change behavior.

## Common Structure

Use this order for medium or complex prompts, omitting sections that do not matter:

```text
# Role
[The model's job, domain, and operating context.]

# Objective
[The concrete outcome the model must produce.]

# Context
[Only context that changes behavior or correctness.]

# Requirements
- [Observable requirement]
- [Evidence, constraint, or quality bar]

# Instructions
[Decision rules and task behavior. Prefer destination and constraints over unnecessary process.]

# Examples
[Use only when examples materially improve reliability.]

# Output Format
[Format, fields, length, order, tone, and required labels.]

# Completion Criteria
[When to answer, ask, retry, fallback, abstain, validate, or stop.]
```

For short prompts, compress the same information into one paragraph or a few bullets. Do not force a large template onto a simple task.

## Prompt Type Patterns

### Simple Task Prompt

Use for one-step writing, summarization, classification, transformation, or direct answers:

```text
Summarize the transcript for a product manager deciding next steps.

Output:
- 5 bullets max
- include decisions, blockers, owners, and dates
- label anything uncertain as `Unclear`

Transcript:
<transcript>
...
</transcript>
```

### Structured Output Prompt

Use for extraction, classification, comparison, grading, or deterministic formatting:

```text
# Role
You are [specific role relevant to the task].

# Task
Perform [operation] on every item in the input.

# Rules
- Apply these rules to every item, not only the first item.
- If information is missing, use [explicit fallback such as null, unknown, or a short explanation].
- Do not infer facts that are not supported by the input.

# Output Format
Return [JSON/table/bullets/prose] with these fields in this order:
1. ...
2. ...

# Input
{{INPUT}}
```

When JSON or another machine-readable schema is required, specify required keys, allowed values, null behavior, ordering, and whether extra keys are allowed.

### Long-Context Prompt

Place stable instructions before dynamic source material when reusable prompt caching matters. Use clear source identifiers:

```text
# Task
Answer the final question using the supplied documents.

# Evidence Rules
- Use only the documents unless outside knowledge is explicitly allowed.
- Cite the source identifier for each important claim.
- Compare sources when claims conflict.
- Treat unsupported claims as unknown.

# Documents
<documents>
  <document index="1">
    <source>...</source>
    <document_content>
    ...
    </document_content>
  </document>
</documents>

# Question
...
```

If source material is very large, keep metadata close to each source and make the final request easy to find.

### Developer Or System Prompt

Use when writing persistent behavior for an assistant or application:

- Start with identity and purpose.
- Define authority boundaries and what user input can configure.
- Separate core behavior, constraints, evidence rules, personality, collaboration style, and output policy.
- State how to handle missing context, unsafe requests, conflicts, and unsupported claims.
- Keep stable policy text before variable product data or conversation context.
- Write persistent application rules as developer/system prompt text, separate from user-provided task input.

### Agent Prompt

Use when the model can plan, use tools, edit files, search, or run multi-step workflows:

```text
# Role
You are [specific agent role].

# Objective
State the concrete end state.

# Context
Include user goals, repo/product/domain constraints, audience, and known inputs.

# Operating Rules
- Investigate before making claims about unseen files, documents, data, or external facts.
- Use tools when they are needed to verify facts, inspect state, or complete the task.
- Run independent tool calls in parallel; run dependent steps sequentially.
- Continue until the user's concrete goal is handled or a real blocker is reached.
- Ask for confirmation before destructive, hard-to-reverse, or externally visible actions.
- Keep changes focused on the requested task.

# Progress Updates
Describe when and how to update the user during longer work.

# Completion Criteria
Define what done means, including validation or tests when relevant.

# Final Response
Specify the final answer format and what evidence or file references to include.
```

### Reusable Prompt Template

Put reusable instructions first and volatile variables later under clear labels:

```text
# Task
Rewrite `{{source_text}}` for `{{audience}}` while preserving the original claim set.

# Constraints
- Do not add facts, numbers, names, dates, or promises.
- If a required detail is missing, use `[placeholder]`.

# Output
{{output_format}}
```

### Eval Prompt

Use when asking a model to grade, classify, compare, or judge another output:

- Define the exact rubric and scoring scale.
- Make labels mutually exclusive when possible.
- Specify evidence requirements: quote or cite the evaluated text when judging failures.
- Include tie-breakers and abstain rules.
- Keep the final output structured enough to parse or compare.
- Include representative examples and regression cases when prompt behavior will be compared across model or prompt versions.

### Skill Prompt

Use when writing a skill consumed by another agent:

```text
# Purpose
Define the narrow capability the skill provides.

# Trigger
State exactly when the skill should be used.

# Workflow
List the steps the agent should follow.

# Decision Rules
Describe how to choose between valid prompt structures or approaches.

# Constraints
State boundaries, exclusions, and failure behavior.

# Output
Specify what the agent should produce for the user.
```

Put trigger behavior in the frontmatter description, not only the body. Keep the body procedural and concise. Move large variant-specific details to references only when they are needed.

### Grounded Or Retrieval Prompt

Use when the answer must rely on external, retrieved, or provided evidence:

- State which claims require citations, such as policies, dates, prices, statistics, named owners, product capabilities, legal or medical guidance, and source-specific facts.
- Define what counts as enough evidence for the task.
- Add a retrieval budget: start with the smallest useful search or source read, search again only when required facts are missing or the user asks for exhaustive coverage.
- Treat missing evidence as unknown, not as proof that a claim is false.
- Ask for the smallest missing field when evidence is required and unavailable.
- Stop once the answer can support the core request with useful evidence and citations.

### Creative Drafting Prompt

Use when creating slides, copy, summaries for sharing, talk tracks, launch notes, or narrative framing:

- Distinguish source-backed facts from creative wording.
- Require retrieved or provided facts for concrete product, customer, metric, roadmap, date, capability, competitive, or outcome claims.
- Do not invent names, first-party data, metrics, roadmap status, customer outcomes, or capabilities for polish.
- If support is thin, write a useful generic draft with placeholders or clearly labeled assumptions.

### Frontend Prompt

Use when writing prompts for frontend generation or UI changes:

- Include product context, target user, design-system constraints, expected states, interaction behavior, accessibility, responsive behavior, and visual inspection requirements.
- Ask for familiar controls, icons, and existing component patterns where relevant.
- Avoid generic heroes, nested cards, decorative gradients, visible instructional text, and layouts that have not been rendered or inspected.
- Require rendering or screenshot inspection before finalizing when the environment can run the UI.

## Formatting Guidance

Use Markdown headings and bullets for instruction hierarchy. Use XML tags for separating large or mixed data blocks:

```text
<policy>
...
</policy>

<user_request>
...
</user_request>
```

Use JSON or schema-like blocks only when the output must be machine-readable. When JSON is required, specify required keys, allowed values, null behavior, and whether extra keys are allowed.

## Wording Rules

- Prefer affirmative instructions: say what to do before saying what to avoid.
- Make each instruction observable or behavior-changing.
- Replace "be helpful", "be professional", and "be accurate" with concrete behavior.
- Use `must`, `never`, `only`, and `always` only for true invariants.
- Use examples to teach boundaries, not to pad the prompt.
- Avoid chain-of-thought or hidden reasoning requests. Ask for concise rationale, checks, or evidence when they should be visible.
- Preserve the user's required terminology, voice, and domain constraints unless they weaken correctness.
- Avoid legacy prompt stacks that over-specify process; add process only when it improves correctness, safety, compliance, tool use, or validation.

## Examples

Use examples when format, tone, edge cases, or classification boundaries matter. Good examples are relevant, diverse, concise, and stable. Avoid examples that accidentally teach unwanted patterns.

## Cache-Friendly Arrangement

When a prompt will be reused, arrange the text so stable content changes least often:

1. Identity, objective, standing rules, style, and output contract.
2. Stable domain context, policies, and examples.
3. Variable task inputs, retrieved context, conversation snippets, or user data.

Do not mix frequently changing values into the stable instruction block. Put dynamic content under clear labels near the end so reusable prompt prefixes remain consistent across calls.

## Provider-Specific Notes

- Prefer compact, outcome-first prompt text with explicit success criteria.
- Keep process light unless it improves correctness, safety, compliance, tool use, or validation.
- For persistent application behavior, separate developer/system instructions from user-provided task input.
- For longer or tool-heavy streaming workflows, ask for a short preamble before tool calls that acknowledges the request and states the first step.
- Include TODO tracking or a rubric only when it helps the agent avoid missing complex multi-step work.

## References

No additional provider reference files are required for the common OpenAI patterns in this skill.

## Output Rules

When responding to the user:

- Provide the finished prompt first.
- Include a short note only when it helps explain important structure choices.
- Preserve placeholders such as `{{INPUT}}`, `{{DOCUMENTS}}`, or `{{USER_GOAL}}` when the prompt is meant to be reusable.
- Do not invent operational details that belong outside the prompt.
- If revising an existing prompt, keep the user's intended behavior unless it conflicts with OpenAI prompt-writing best practices or the user's current request.

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
