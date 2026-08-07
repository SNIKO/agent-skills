---
name: html-artifacts
description: "Create or revise polished, standalone HTML documents for direct human reading. Use for technical documents, proposals, reports, specifications, guides, and review artifacts when the user wants a clear, attractive HTML deliverable."
---

# Purpose

Create a static, human-readable HTML document from the material the user provides or authorizes. Use [html.template.html](html.template.html) as the visual and structural starting point. The default deliverable opens directly from disk: embed CSS, use system fonts, and require no server, build step, JavaScript, or external network resource.

This skill is document-type neutral. It presents supplied content clearly; it does not prescribe a software workflow, a canonical source format, or a particular artifact type.

# Workflow

1. Read the requested source material and any directly linked local assets. Identify the audience, document purpose, required sections, output path, and whether an existing source is authoritative.
2. Ask only when a missing choice materially changes content, audience, or output location. If the supplied material is incomplete, label gaps or use placeholders rather than inventing facts, commitments, data, or decisions.
3. Choose a scannable hierarchy suited to the content: a concise header and summary, then logical sections. Use visual structure to improve comprehension rather than mechanically reproducing source formatting.
4. Start from the template and write a complete semantic HTML document. Include only elements that serve the document; remove unused template examples and placeholders.
5. Apply the element guidance below whenever the document contains that content.
6. Open the local file when practical and check it at desktop and narrow viewport widths. Confirm links and local assets resolve, text is legible, and the page has no horizontal overflow outside intentionally scrollable code, diagrams, or tables.
7. When revising, preserve content that remains valid, apply the requested change, and update the displayed date only when the document itself says it was updated.

# Document design

- Use semantic HTML: `header`, `main`, `section`, headings in order, `nav` only for real navigation, `figure`/`figcaption`, `table`, `pre`/`code`, and `aside` for supplementary notes.
- Use the template's calm, neutral technical-document style: restrained color, generous whitespace, clear hierarchy, readable line lengths, and accessible contrast. Do not apply an organization or product brand unless requested.
- Give the document a useful `<title>`, a single clear `<h1>`, and a short summary when the source supports one.
- Keep content width comfortable for reading. Use `minmax(0, ...)` for grid tracks. Long paths, identifiers, URLs, and code must wrap or scroll inside their own container rather than overflowing the page.
- Use cards only to group distinct, short items. Prefer normal sections for narrative. Use callouts for decisions, assumptions, warnings, risks, or blockers; do not rely on color alone.
- Use relative links for local files and assets. Give meaningful `alt` text to informative images. Do not add forms, hidden content, fabricated status, analytics, editing controls, or decorative illustrations unless requested.

# Standard elements

## Diagrams

- Include a diagram only when it makes relationships, flow, ownership, or sequence clearer than concise prose or a table.
- Prefer an inline SVG for a true diagram: include a `title`/`desc`, readable labels, visible arrow direction, high contrast, and a responsive `viewBox`. Keep it understandable without color alone.
- For simple flows, use a labelled text-flow block when it is clearer and less brittle than SVG.
- Do not depend on Mermaid, a CDN, JavaScript, or another runtime renderer. Do not paste unrendered Mermaid source as the only diagram.
- Caption the diagram with what it shows and preserve any important source qualification. Keep oversized diagrams in a labelled, locally scrollable frame rather than letting them overflow the page.

## Code and syntax highlighting

- Put executable or copyable code in `pre > code`; set a language class such as `language-ts`, `language-sql`, or `language-json` when known.
- Use the template's CSS token classes (`tok-keyword`, `tok-string`, `tok-comment`, `tok-number`, `tok-type`, `tok-function`, `tok-property`, `tok-punctuation`) for lightweight static highlighting. Escape HTML characters and retain indentation exactly.
- Highlight only when it improves scanning. Do not add a JavaScript syntax-highlighting library or external stylesheet.
- Label the language and, when helpful, the file path or command context adjacent to the code block. Keep code blocks horizontally scrollable.

## Diffs

- Use a `pre.diff` block for a compact patch. Prefix added lines with `+`, removed lines with `-`, and unchanged context with a space; use `@@` for hunk headers when applicable.
- Use `ins` and `del` or the template's diff classes so additions and removals remain distinguishable without color. Preserve surrounding context needed to interpret the change.
- Do not present a diff as an exhaustive implementation claim unless the source explicitly establishes that it is complete.

## File structure

- Present a repository or directory layout in a labelled `pre.file-tree` block with a monospace tree. Keep paths relative to a clearly named root.
- Include only material files and directories. Annotate entries briefly only when their role is not apparent from the name.
- Distinguish proposed, changed, and existing paths in nearby prose or a legend; do not imply a path exists when it is only illustrative or planned.

## Database schema

- Use a table for each entity or relation with columns appropriate to the supplied detail, normally: field, type, nullability, default, constraints/indexes, and notes.
- State primary keys, foreign keys, unique constraints, indexes, cardinality, delete/update behavior, and migration qualification when known. Mark unspecified details as unspecified rather than guessing.
- Add a relationship diagram only when it improves on the tables. Keep logical models distinct from physical database details.

## Tables

- Use a table for comparisons, inventories, contracts, structured facts, or repeated records; use lists for short, simple sequences.
- Give every table a caption or nearby heading that describes its purpose. Use `thead`, `tbody`, and `th scope` correctly.
- Wrap wide tables in `.table-wrap`; give `table` a sensible `min-width` so cells remain readable. Do not compress wide information until it is illegible.
- Keep cells concise. Move long explanation or code to nearby prose, callouts, or code blocks.

# Constraints

- Preserve source truth. Do not introduce facts, requirements, APIs, schema details, file paths, dates, metrics, or decisions that the supplied material does not support.
- Produce valid, complete HTML with embedded CSS by default. Use external assets, scripts, or frameworks only when the user explicitly requests them.
- Do not redesign a source artifact's content contract while rendering it. If the user designates a source as authoritative, update that source first when asked to change substance, then regenerate the HTML.
- Keep changes focused on the requested document and do not edit unrelated documents or assets.

# Completion criteria

The requested HTML document exists at the requested path, opens directly from disk without dependencies by default, accurately represents its source material, uses applicable standard elements correctly, and is legible and scan-friendly at desktop and narrow widths.
