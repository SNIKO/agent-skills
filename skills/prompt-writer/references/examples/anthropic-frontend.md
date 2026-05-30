# Formatting And Frontend Examples

Use this reference for larger examples involving markdown control, math formatting, document style, frontend visual direction, and aesthetic diversity.

## Markdown Control

If markdown should be minimized, say what to output instead of only saying what to avoid:

```text
<style>
Write in smooth prose paragraphs. Use paragraph breaks for structure.
Use inline code only for literal commands, paths, variables, and code identifiers.
Use fenced code blocks only for multi-line code.
Do not use headings, tables, or bullet lists unless the answer would be hard to read without them.
</style>
```

If the output must be plain text:

```text
<output_format>
Return plain text only.
Do not use markdown headings, bullet markers, numbered lists, tables, links, bold, italics, or code fences.
Separate sections with short uppercase labels followed by a colon.
</output_format>
```

For strict JSON:

```text
<output_format>
Return only valid JSON. Do not wrap the JSON in markdown. Do not include comments or explanatory text.
Use null for missing values. Preserve the exact field order shown in the schema.
</output_format>
```

## Plain-Text Math

Claude may default to LaTeX for math. Use explicit plain-text constraints when needed:

```text
<math_format>
Write math in plain text only.
Do not use LaTeX, MathJax, dollar delimiters, \( \), \[ \], or fraction commands.
Use text operators such as /, *, ^, sqrt(), and parentheses.
</math_format>
```

## Frontend Aesthetic Direction

Avoid relying on generic terms such as "clean", "modern", or "beautiful". Provide a concrete visual system.

```text
<visual_direction>
Design a dense operational dashboard for analysts.
Use a white canvas, charcoal text, one saturated blue accent, fine gray dividers, compact typography, 4px radii, and tabular spacing.
Favor toolbars, segmented controls, filters, tables, inline status, and keyboard-friendly interactions.
Avoid marketing-style hero sections, decorative cards, and oversized typography.
</visual_direction>
```

For a consumer creative tool:

```text
<visual_direction>
Design a tactile creative workspace.
Use a pale neutral canvas, black text, one vivid coral accent, generous previews, visible tool controls, and subtle drag/resize affordances.
Use cards only for repeated assets or modals. Keep the editor itself full-width and unframed.
</visual_direction>
```

For an editorial site:

```text
<visual_direction>
Design an editorial reading experience.
Use high-contrast serif headlines, readable sans-serif body text, strong image placement, restrained accent color, and clear article hierarchy.
The first viewport should establish the subject immediately and hint at the next section below the fold.
</visual_direction>
```

## Ask For Distinct Directions First

Use this when the user has not specified a look and the visual direction materially affects the result:

```text
<visual_direction_selection>
Before building, propose 4 distinct visual directions tailored to the product.
For each direction include:
- name
- background color
- primary text color
- accent color
- typography style
- layout density
- motion style
- one-sentence rationale

Ask the user to choose one direction. Implement only after a direction is selected.
</visual_direction_selection>
```

## Complete Frontend Prompt Snippet

```text
<role>
You are a senior product designer and frontend engineer.
</role>

<task>
Build the actual usable first screen of the application, not a landing page or static mockup.
</task>

<product_context>
The product is {{PRODUCT_DESCRIPTION}} for {{TARGET_USER}}.
The most important workflow is {{PRIMARY_WORKFLOW}}.
</product_context>

<requirements>
- Make the primary workflow visible and usable in the first viewport.
- Include expected controls, empty states, loading states, and error states.
- Use icons for common tool actions when an icon is clearer than text.
- Keep text within its containers at desktop and mobile sizes.
- Avoid nested cards and decorative backgrounds.
- Use stable dimensions for boards, toolbars, counters, tiles, and fixed-format controls so labels and hover states do not shift layout.
</requirements>

<visual_direction>
{{CONCRETE_VISUAL_DIRECTION}}
</visual_direction>

<validation>
Check the UI at desktop and mobile widths. Fix overlap, clipped text, blank media, and controls that are hard to scan or tap.
</validation>
```

## Document Style Control

Use this when the generated document must follow a house style:

```text
<document_style>
Write for {{AUDIENCE}}.
Use direct, specific language.
Prefer short paragraphs.
Use headings only for meaningful section breaks.
Use tables only for comparison or structured data.
Avoid filler, generic benefits, and unsupported claims.
</document_style>
```
