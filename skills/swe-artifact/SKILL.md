---
name: swe-artifact
description: "Render a canonical SWE Markdown artifact as a standalone, human-reviewable HTML document. Use when swe-architect, swe-spec, or swe-plan writes or revises its paired HTML review artifact."
disable-model-invocation: true
---

# Purpose

Create a static, readable review surface for a canonical SWE Markdown contract. The HTML is for human comprehension; the Markdown remains authoritative for agents and implementation.

Start from [html.template.html](html.template.html). The generated file must be standalone: embed CSS, use only system fonts, and require neither a server, a build step, JavaScript, nor external network resources.

# Workflow

1. Read the completed canonical Markdown artifact, its directly linked upstream artifacts, and any local review mockups linked by the Markdown.
2. Write or revise the Markdown first. Do not let the HTML introduce, own, or alter a decision, contract, acceptance criterion, or delivery commitment.
3. Render a complete HTML review artifact from that Markdown. Preserve every material decision and contract, but use visual structure instead of copying Markdown syntax mechanically. For an architecture artifact, render linked local SVG or image mockups inline when practical; otherwise include a clearly labelled local link and its decision caption. Do not invent or alter a mockup while rendering.
4. Open the local HTML file when practical to check that it is readable at desktop and narrow viewport widths, including any rendered mockups. Do not start a server or use a review/editing service.
5. When human feedback arrives in conversation, revise the canonical Markdown first, then regenerate its HTML counterpart in the same response.

# Rendering rules

- Include a small header naming the artifact, its change, its canonical Markdown source, and the generation/update date.
- Use a calm, neutral technical-document design: restrained color, generous whitespace, clear hierarchy, semantic HTML, and accessible contrast. Do not apply the branding of this repository or of the subject repository unless the user asks.
- Use Mermaid only when a diagram materially improves comprehension. Because this artifact has no runtime dependencies, render the diagram as readable text or inline SVG; do not depend on a Mermaid CDN or script.
- Use tables for contracts, state/failure behavior, verification, dependencies, and delivery slices. Wrap wide tables in a horizontally scrollable container.
- Use callouts for decisions, risks, assumptions, and blockers; do not use color as the only signal.
- Make code, schemas, commands, paths, IDs, and anchors easy to copy with `pre`/`code` and a monospace face.
- Do not add interaction, forms, edit controls, hidden content, metrics, invented status, or decorative illustrations. A wireframe is acceptable only when it is a local mockup linked by the canonical Markdown.
- Keep rendered mockups reviewable at narrow widths: constrain them to the content width, preserve aspect ratio, and permit local scrolling within a labelled frame only when necessary.
- Avoid horizontal page overflow. Every grid track must use `minmax(0, ...)`, and long paths/identifiers must wrap or scroll within their local container.
- Link to sibling Markdown and upstream artifacts with relative paths. The HTML must still be useful when opened directly from disk.

# Artifact-specific emphasis

- **Architecture:** decision drivers, component ownership, dependency direction, conceptual repository layout, user-facing screen/navigation/state decisions, linked mockups, normal/failure flows, and trade-offs.
- **Build spec:** repository delta, exact contracts, migration/compatibility behavior, and the verification matrix. Fidelity takes priority over visual brevity.
- **Plan:** delivery strategy, ordered slices, dependencies, per-slice verification, and operational considerations.

# Constraints

- HTML is derived, non-authoritative, and must never be the only record of an agent-relevant decision.
- Do not create an HTML artifact when its canonical Markdown artifact was intentionally skipped or blocked.
- Do not edit unrelated paired artifacts merely because one is being rendered.

# Completion criteria

The paired HTML exists beside its canonical Markdown, accurately reflects it, opens directly from disk without dependencies, and is visually scan-friendly without weakening the Markdown contract.
