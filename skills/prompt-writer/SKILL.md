---
name: prompt-writer
description: "Use when creating, reviewing, or revising prompts, system prompts, developer instructions, agent policies, prompt templates, eval prompts, or skill files consumed by LLMs and AI agents. Routes to OpenAI, Anthropic/Claude, or generic prompt-writing guidance based on the target model/provider."
---

# Prompt Writer

Use this skill as a router. Do not rely on this file for detailed prompting rules; load the appropriate reference before writing, reviewing, or revising the prompt.

## Provider Selection

Infer the target provider from the user request, model names, product names, surrounding prompt context, or prompt target:

- **OpenAI**: GPT, GPT-5.x, OpenAI, ChatGPT, Codex. Read [references/openai.md](references/openai.md).
- **Anthropic/Claude**: Claude, Anthropic, Opus, Sonnet, Haiku. Read [references/anthropic.md](references/anthropic.md).
- **Unknown, provider-neutral, mixed, or generic prompts**: read [references/generic.md](references/generic.md).

If more than one provider applies, read each relevant provider reference and resolve conflicts in favor of the user's stated target. If no target is stated, use the generic reference.

## Required Workflow

1. Select and read the proper reference file before producing the prompt artifact.
2. Follow the selected reference's structure, rules, examples, output rules, and quality check.
3. Keep the user's intended behavior unless it conflicts with the user's current request or the selected reference.
4. Return the finished prompt first, with a short note only when it helps explain an important structure choice.
