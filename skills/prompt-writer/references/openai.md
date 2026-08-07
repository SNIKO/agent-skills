# OpenAI Prompt Writer

Use this reference when writing, reviewing, or revising prompts for OpenAI models (GPT-5.x, including GPT-5.6).

If the prompt's purpose or use context is unclear, ask the smallest question needed: what the prompt will be used for and what output it must produce. Otherwise infer from the user request, surrounding artifact, or repository context.

Stay inside prompt text: structure, wording, formats, examples, context boundaries, output contract, evidence rules, and validation. Do not add API calls, SDK snippets, model IDs, runtime parameters, or product claims unless the user asks for them or they belong in the prompt.

## Leanness

Lean prompts outperform long ones. Removing repeated instructions, redundant examples, and bloated tool descriptions improves task performance while cutting tokens, latency, and cost. Treat prompt length as a cost, not as thoroughness.

- State each instruction exactly once, in one place. Do not restate a rule across `Role`, `Instructions`, and `Output Format`, and never repeat a rule for emphasis.
- Start from a prompt and tool set that already works. Remove one group of instructions, examples, or tools at a time, then re-run the same evaluation on representative tasks.
- Expose only tools relevant to the task, with concise, precise descriptions.
- Keep examples and style guidance only when they encode a product requirement or correct a measured gap.
- Repeated prompt and tool content is re-paid every turn and amplified in long sessions.
- When reviewing an existing prompt, name duplicated rules and delete them rather than rewording them.

Gains are directional. Recommend validating on the user's own representative tasks rather than asserting fixed numbers.

## Workflow

1. Identify the prompt type from the patterns below.
2. Extract the intended task, user-visible outcome, audience, constraints, input sources, evidence needs, output format, and failure cost.
3. Choose the lightest structure that makes the prompt reliable.
4. Place stable instructions and examples before volatile input; separate instructions from data with Markdown sections or XML tags when boundaries matter.
5. Add autonomy boundaries, evidence rules, examples, tone, length, and validation only where they change behavior.
6. Delete duplicated rules, decorative persona text, vague quality claims, chain-of-thought requests, and process narration.

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
[Decision rules and task behavior. Prefer destination and constraints over process.]

# Examples
[Only when they materially improve reliability.]

# Output Format
[Format, fields, length, order, tone, and required labels.]

# Completion Criteria
[When to answer, ask, retry, fall back, abstain, validate, or stop.]
```

For short prompts, compress the same information into a paragraph or a few bullets.

## Prompt Type Patterns

### Simple Task

One-step writing, summarization, classification, transformation, or direct answers:

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

### Structured Output

Extraction, classification, comparison, grading, or deterministic formatting:

```text
# Role
You are [specific role relevant to the task].

# Task
Perform [operation] on every item in the input.

# Rules
- Apply these rules to every item, not only the first item.
- If information is missing, use [explicit fallback such as null, unknown, or a short explanation].
- Do not infer facts unsupported by the input.

# Output Format
Return [JSON/table/bullets/prose] with these fields in this order:
1. ...
2. ...

# Input
{{INPUT}}
```

### Developer Or System Prompt

Persistent behavior for an assistant or application:

- Start with identity and purpose, then authority boundaries and what user input may configure.
- Separate core behavior, constraints, evidence rules, tone, and output policy.
- State how to handle missing context, unsafe requests, conflicts, and unsupported claims.
- Keep persistent application rules in developer/system text, separate from user-provided task input.

### Agent Prompt

For models that plan, use tools, edit files, search, or run multi-step workflows:

```text
# Role
You are [specific agent role].

# Objective
State the concrete end state.

# Context
User goals, repo/product/domain constraints, audience, known inputs.

# Operating Rules
- Investigate before making claims about unseen files, data, or external facts.
- Run independent tool calls in parallel; run dependent steps sequentially.
- Continue until the goal is handled or a real blocker is reached.
- Keep changes focused on the requested task.

# Autonomy And Approvals
[One compact policy; see "Autonomy And Approval Boundaries".]

# Progress Updates
When and how to update the user during longer work.

# Completion Criteria
What done means, including validation or tests.

# Final Response
Final answer format and required evidence or file references.
```

For tool-heavy streaming workflows, ask for a short preamble before tool calls stating the first step. Add TODO tracking or a rubric only when the agent would otherwise drop steps in complex work.

### Reusable Template

Reusable instructions first, volatile variables last under clear labels:

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

- Define the exact rubric and scoring scale; make labels mutually exclusive where possible.
- Require quoting or citing the evaluated text when judging failures.
- Include tie-breakers and abstain rules.
- Keep output structured enough to parse or compare.
- Include representative and regression cases when behavior is compared across model or prompt versions.

### Skill Prompt

Skills consumed by another agent:

```text
# Purpose
The narrow capability the skill provides.

# Trigger
Exactly when the skill should be used.

# Workflow
Steps the agent follows.

# Decision Rules
How to choose between valid approaches.

# Constraints
Boundaries, exclusions, failure behavior.

# Output
What the agent produces for the user.
```

Put trigger behavior in the frontmatter description, not only the body. Keep the body procedural; move variant-specific detail to references only when needed.

### Grounded Or Retrieval Prompt

- State which claims require citations: policies, dates, prices, statistics, named owners, product capabilities, legal or medical guidance, source-specific facts.
- Define what counts as enough evidence.
- Set a retrieval budget: start with the smallest useful search, search again only when required facts are missing or the user asks for exhaustive coverage, and stop once the core request is supported.
- Treat missing evidence as unknown, not as disproof; ask for the smallest missing field when required evidence is unavailable.

### Creative Drafting Prompt

Slides, copy, shareable summaries, talk tracks, launch notes, narrative framing:

- Distinguish source-backed facts from creative wording.
- Require retrieved or provided facts for product, customer, metric, roadmap, date, capability, competitive, or outcome claims.
- Never invent names, first-party data, metrics, roadmap status, customer outcomes, or capabilities for polish.
- If support is thin, draft generically with placeholders or labeled assumptions.

### Frontend Prompt

- Include product context, target user, design-system constraints, expected states, interaction behavior, accessibility, and responsive behavior.
- Ask for familiar controls, icons, and existing component patterns.
- Avoid generic heroes, nested cards, decorative gradients, and visible instructional text.
- Require rendering or screenshot inspection before finalizing when the environment can run the UI.

## Autonomy And Approval Boundaries

Current models are proactive and persistent on multi-step tasks. State what level of action each request authorizes so the agent continues safe, in-scope work without unnecessary pauses and stops before external, destructive, costly, or scope-expanding actions.

A compact policy is usually sufficient:

```text
For requests to answer, explain, review, diagnose, or plan, inspect the relevant
materials and report the result. Do not implement changes unless the request also
asks for them.

For requests to change, build, or fix, make the requested in-scope local changes
and run relevant non-destructive validation without asking first.

Require confirmation for external writes, destructive actions, purchases, or a
material expansion of scope.
```

- Name safe local actions explicitly: reading files, inspecting logs, editing in-scope code, running tests.
- Keep the policy in one place. Do not sprinkle "ask first", "do not mutate", or "wait for approval" elsewhere; repetition triggers unnecessary approval requests for safe, expected actions.
- The read-only-versus-change distinction, not a global confirmation rule, is what prevents unwanted edits.

## Length And Tone

Current models are concise by default. Do not add broad brevity instructions such as "Be concise" or "Keep it short" by reflex; they are often unnecessary and can make responses too brief. Keep them only when they reliably produce the output the application needs, and when migrating an older prompt, delete the ones that no longer earn their place.

When a task calls for a shorter answer, say what it must preserve and what it may drop rather than only naming a length:

```text
Lead with the conclusion. Include the evidence needed to support it, any material
caveat, and the next action. Omit secondary detail and repetition.

Keep all required facts, decisions, caveats, and next steps. Trim introductions,
repetition, generic reassurance, and optional background first.
```

Broad tone labels such as "friendly", "empathetic", or "professional" are ambiguous. Describe the writing choices instead:

```text
State the answer directly. If the user reports a problem, acknowledge the
specific issue before giving the next step. Use reassurance only when it is
relevant. Omit generic praise and unnecessary sign-offs.
```

## High-Effort And Pro Mode

Extra model work is runtime configuration, not prompt text. Under pro mode or higher reasoning effort, keep the same outcome-focused prompt used in standard mode: goal, context, constraints, required evidence, success criteria, output format.

- Never add "use pro mode", "think harder", "reason step by step", or "generate several candidates and pick the best".
- Reserve higher-effort configurations for tasks where a marginal quality gain materially changes the outcome: complex optimization, high-value coding or review, deep analysis with clear evaluation criteria. Prefer standard mode for routine, latency-sensitive, or high-volume work.
- Reasoning effort and execution mode are independent. Start from the standard-mode baseline and compare configurations on representative tasks rather than assuming highest effort is best.
- Make evaluation criteria explicit so the extra work has something to optimize against:

```text
Review this database migration plan for failure modes that could cause data loss
or extended downtime. For each finding, cite the relevant step, estimate impact
and likelihood, and recommend a specific mitigation. Return the five most
important risks in severity order.
```

## Formatting And Placement

Use Markdown headings and bullets for instruction hierarchy, and XML tags to separate large or mixed data blocks:

```text
<policy>
...
</policy>

<user_request>
...
</user_request>
```

Use JSON or schema blocks only when output must be machine-readable; then specify required keys, allowed values, ordering, null behavior, and whether extra keys are allowed.

For reusable prompts, order text so the stable prefix stays constant across calls:

1. Identity, objective, standing rules, style, output contract.
2. Stable domain context, policies, examples.
3. Variable task inputs, retrieved context, conversation snippets, user data.

Keep frequently changing values out of the stable block and label dynamic content near the end.

## Wording Rules

- Prefer affirmative instructions: say what to do before what to avoid.
- Make each instruction observable or behavior-changing. Replace "be helpful", "be professional", "be accurate" with concrete behavior or delete them.
- Use `must`, `never`, `only`, and `always` only for true invariants.
- Use examples to teach format, tone, edge cases, and classification boundaries — not to pad. Good examples are relevant, diverse, concise, and stable, and never teach patterns you do not want.
- Avoid chain-of-thought or hidden reasoning requests; ask for concise rationale, checks, or evidence when they should be visible.
- Preserve the user's terminology, voice, and domain constraints unless they weaken correctness.
- Add process only when it improves correctness, safety, compliance, tool use, or validation.

## Output Rules

- Provide the finished prompt first, then a short note only if it explains an important structure choice.
- Preserve placeholders such as `{{INPUT}}`, `{{DOCUMENTS}}`, or `{{USER_GOAL}}` in reusable prompts.
- Do not invent operational details that belong outside the prompt.
- When revising, keep the user's intended behavior unless it conflicts with this reference or the current request.

## Quality Check

- Every instruction appears exactly once, and every instruction, example, and exposed tool changes behavior.
- The desired outcome appears before process, and the output contract is specific enough for the task.
- Rule scope is explicit when rules apply across every item, source, file, step, or output section.
- Autonomy boundaries are stated once and name safe local actions; tone is described as writing choices; length guidance says what to preserve.
- Evidence, retrieval budget, citation rules, and missing-evidence behavior exist when grounding is required.
- Stop rules and validation exist when the task could loop, over-search, or produce unverifiable output.
- Stable instructions precede volatile inputs in reusable prompts.
- No instruction conflicts with another, and no runtime concern (verbosity, effort, execution mode) is smuggled into prompt text.
