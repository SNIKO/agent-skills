---
name: prompt-writer
description: "Apply when editing/creating prompts, system prompts, agent instructions, or skill files, everything that is consumed by LLMs and AI agents."
---

# Prompt Writer

Goal: max signal, min tokens. Use empirically-tested prompt engineering principles.

## Rules

### Format Hierarchy

Mix formats for maximum token-efficiency and clarity:

- **XML tags** - for structural boundaries (`<instructions>`, `<context>`, `<examples>`)
- **Yaml or key-value pairs** - for configs, variables, definitions
- **JSON** - for output schemas
- **Markdown** - for everything else (instructions, context)

### Kill the Hedge Tax

Hedge phrases (`if possible`, `please try to`, `where appropriate`) dilute attention and waste tokens. Be direct and specific.

Three-Primitives compression:
1. Extract **Task + Format**
2. Extract **Minimum Viable Context**
3. Convert all rules to **declarative assertions**

<example type="bad">
   Please make sure the response is not too long and stays professional and avoids jargon that non-technical users might not understand.
   Return only valid JSON if possible and avoid adding extra commentary.
</example>

<example type="good">
   Max 200 words. Grade-8 reading level. No technical jargon.
   Output: valid JSON only. No prose. No commentary.
</example>

### Affirmative Blueprint + Late Fences

Negative constraints echo the prohibited concept into output.

<example type="bad">
   Don't exceed 4 columns. Don't add meta-commentary.
</example>

<example type="good" >
   Create a 4-column table: Option, Pros, Cons, Verdict. No other columns.
</example>

### Persona + Goal + Anti-Goal

<example type="bad">
   You are an expert editor.
</example>

<example type="good">
   Role: Sharp developmental editor at a top literary agency.
   Goal: Surface structural weaknesses in the argument.
   Anti-goal: Do NOT rewrite sentences — surface issues only, don't fix them.
</example>

<example type="bad">
   act like a database expert
</example>

<example type="good">
   Senior DBA at Stripe, 15 years Postgres, skeptical of ORMs
</example>

**Specificity rule:** every detail must be *causal* to the task. Generic roles average the output. Decorative details add noise. Test: if removing it wouldn't change the answer, it's decoration.

### Interface Prompt, Not Narrative

<example type="bad">
   You are a helpful assistant that tries to be concise and always explains your reasoning before giving an answer.
</example>

<example type="good">
   Role: Summarizer
   Goal: <=150 words
   Constraints:
   - No introductions
   - No repetition
   - Preserve technical terms
   Output: plain text
</example>


### Static-Top, Dynamic-Bottom

For prompt caching:
- **Top**: all static content — system instructions, tool schemas, fixed config
- **Bottom**: all dynamic content — user queries, injectable context, injectable variables