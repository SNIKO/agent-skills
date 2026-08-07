---
name: swe-spec
description: "Create and progressively deepen a single evolving specification document for a change, from high-level structure down to exact contracts"
disable-model-invocation: true
---

# Pipeline

`swe-shape` → `swe-spec` → [`swe-plan`] → [`swe-worktree`] → `swe-execute` → `swe-review`, with `swe-research` available at any point.

Each stage runs in its own session: the user invokes one skill, reviews the artifact, clears the context, then invokes the next with the artifacts as input. Assume no memory of other stages beyond the files named below.

- **This stage:** decide what actually changes, from overall structure down to exact contracts.
- **Reads:** `brief.md` (the approved problem, acceptance criteria, and exclusions, written by `swe-shape`) and the shared research corpus.
- **Writes:** `spec.html`, deepened one confirmed pass at a time across many sessions.
- **Next:** the user runs `swe-plan` if delivery needs sequencing, otherwise `swe-execute`. Do not invoke either.

# Purpose

Maintain one `{SPEC_FILE}` per change that always reads like a properly written spec: top-down from the overall idea and architecture down to exact, implementable detail, organized by logical/domain area the way a human architect would lay it out. Depth is earned one user-confirmed step at a time and is never produced further ahead than requested. Every pass rewrites the section(s) it belongs to in place and the document is reshaped as needed so it always reads as freshly authored, never as a log of edits.

# Inputs

CHANGE_DIR - the directory for the current change, typically `.swe/<change>`, infer from the conversation if not explicitly named.
BRIEF_FILE: `{CHANGE_DIR}/brief.md`
SPEC_FILE: `{CHANGE_DIR}/spec.html`
MOCKUPS_DIR: `{CHANGE_DIR}/mockups/`
RESEARCH_DIR: `.swe/research/` — shared across all changes, indexed by `INDEX.md`

`{SPEC_FILE}` is canonical and is read directly by `swe-plan`, `swe-execute`, and `swe-review`. There is no Markdown counterpart; do not create one.

# Tools & Skills

[html-artifacts](../html-artifacts/SKILL.md) - use for the visual and structural style rules (semantic HTML, diagrams, tables, code blocks) when writing or rewriting `{SPEC_FILE}`. Its own workflow assumes generating a document from scratch; here it supplies style rules only — the editing itself follows the workflow below, which edits `{SPEC_FILE}` directly and never regenerates it wholesale.

# Workflow

1. **Orient.** Read `{BRIEF_FILE}`, the research reports it links, the full current `{SPEC_FILE}` if it exists, and repository context relevant to the requested area. Identify the document's current section layout and how deep each section currently goes.
2. **Resolve shape and depth.** Apply `Document Shape` and `Depth Progression` to determine which section(s) the request touches and the next level of detail for each.
3. **Ask only what's blocking.** Ask the smallest set of questions where two or more options have genuinely different tradeoffs at the depth now being written. Do not ask about decisions the repository, the brief, or a shallower pass already settled. Offer concrete options with a recommendation when one exists.
4. **Delegate unresolved facts.** Where a decision at this depth depends on an unknown repository fact, external behavior, or dependency capability, follow `Research Delegation` before designing that part.
5. **Design the content.** Apply `Design Principles` and decide what a reviewer actually needs to see at this depth: an early pass on a component might need only its responsibility and its contracts with neighbors; a later pass needs its exact interface, states, and error behavior. Choose whatever headings communicate the current decision well. Keep the whole change in mind — do not propose a solution that another section's already-settled constraints would make unworkable, unless that constraint can still change, in which case say so in both places.
6. **Rewrite in place, then reshape.** Update the owning section(s) directly with the new pass — never append a new section or pass to the end of the document. Then review the entire document and merge, split, rename, or reorder sections wherever it now reads better, including sections this request did not touch. Reshaping is for readability and organization; when it would require *reversing a decision*, apply `Contradiction Rule` instead.
7. **Verify coherence and structure.** Read the reshaped document top-to-bottom and confirm it moves from overall idea to exact detail with no gaps, no orphaned cross-references, and no trace of the previous structure. Then apply `Document Mechanics`: valid well-formed HTML, every section carries a stable `id`, and the table of contents matches the sections.
8. **Report.** Follow `Output`.

# Document Shape

A change's spec is organized the way a competent architect would organize any spec for that kind of change — there is no fixed skeleton. A UI feature, a backend API change, a data migration, a bug fix, and a refactor each warrant a different shape, and some may not need sections like API or schema at all. Do not invent sections nobody has asked about yet, and do not force a section to exist just to fill a slot in a template.

Three things guide the shape regardless of what sections exist:

- **Top-down layering.** The document reads from the overall idea and how the pieces fit together, down to exact, implementable contracts. An overview/approach statement and, when there is more than one moving part, an architecture or integration view (e.g. a component/contract diagram) come before the sections that go into any one part in detail. A section for one logical area never appears before the material it depends on.
- **Domain-logical grouping.** Sections are organized by logical or domain area — the kind of grouping a reader would look for when reviewing that part of the design — not by when the content was written or which conversation produced it. A section born from "let's detail the API" belongs with any other API material already in the document, even if that material was written in an earlier session under a different heading.
- **Addressability.** The document is read by downstream agents that must not load all of it. Every section is a `<section id="...">` with a stable, meaningful id, and the document opens with a table of contents listing those ids and one line on what each covers. A downstream stage reads the contents, then only the sections it needs.

User-facing changes may link mockups or wireframes; keep those as separate files in `{MOCKUPS_DIR}` and link them from the owning section rather than inlining them.

# Depth Progression

Depth is how exact the current pass is for whatever section(s) are in play, ranging from the most abstract useful statement of an idea down to an exact, implementable contract. There is no fixed number of depth steps: decide, per section and per change, how many distinct passes are actually worth reviewing separately. A trivial section may only ever need two passes (concept, exact); a genuinely complex one may warrant several. A new pass is justified only if a reviewer could plausibly approve the section as it stands now and reject the next pass as a separate, real decision — never add a pass that only adds words without adding a decision.

Determine the depth for the current request as follows:

- **No area or depth stated, document does not exist yet:** determine shape and depth from the change itself. If the change is small, produce the whole spec at its final, exact depth in one pass. If larger, produce the shallowest useful pass across the whole document first — the overall idea or approach, top-level components/responsibilities and how they integrate, headline API operations or user-facing entry points if applicable, and a UI mockup or wireframe if user-facing. Keep it one reviewable pass, not an exhaustive one.
- **Area and/or depth stated explicitly** (e.g. "just the API", "design the database tables", "high level only"): honor the requested area and go straight to the requested depth for it, even if that skips passes that would normally come first. Still add a brief entry at each shallower depth for that area if none exists, so the document stays readable top-down — a short statement, not a placeholder.
- **User asks to go deeper, get more detail, or approves and asks to continue:** advance the section just discussed (or named) exactly one pass further than what already exists there. Never skip ahead to a deeper pass than requested, and never silently deepen a section the user did not ask about.
- **A section already has content and the user gives feedback without asking to go deeper:** treat it as a revision at its current depth, not an advance.

At every depth, regardless of section: do not specify private methods, internal helper functions, local variables, or algorithm internals unless a stated correctness, security, or contract requirement genuinely depends on them.

# Document Mechanics

- **Stable ids.** Each section keeps its `id` across passes; downstream artifacts and `plan.md` link to them. When a reshape genuinely renames a section, update every reference to it in the same pass.
- **Well-formedness.** After every edit, verify the document parses: no unclosed or orphaned tags, no duplicated ids, no broken local links. In-place editing of nested markup is the main mechanical failure mode of this skill — check rather than assume.
- **Shallow nesting.** Prefer flat `<section>` elements with headings over deeply nested wrappers. Deep structure is what makes surgical edits go wrong.
- **Split threshold.** A single file is the default. Once the document exceeds roughly 1500 lines or several areas have independently reached exact-contract depth, split by domain area into `spec-<area>.html` and reduce `{SPEC_FILE}` to the overview, the architecture view, and the table of contents linking each area file. The document stays logically one spec; only its storage changes.

# Design Principles

- Search `RESEARCH_DIR/INDEX.md` for prior findings before re-investigating; see `Research Delegation` for new questions.
- Link the research reports a decision depends on from the section that depends on them, so downstream stages follow the link instead of scanning the index.
- Use repository-specific design/coding rules and conventions, mixed with the general principles below. In case of conflict, use the repository's rules.
- Design boundaries and contracts so local implementation defects are contained and cannot violate system-wide invariants, public contracts, data integrity, security boundaries, or integration guarantees.
- Prefer clear ownership of data, side effects, lifecycle transitions, and cross-module contracts. Every cross-module contract has a clear owner responsible for compatibility and change coordination.
- Do not expose internal domain models directly across boundaries when that would create unwanted coupling.
- A contract is only complete once it gives the implementer every input, dependency, and signal their component needs.
- Keep dependencies directional and avoid circular dependencies.
- Minimize shared mutable state; make ownership and mutation rules explicit where shared state is unavoidable.
- Isolate external I/O behind explicit clients, adapters, ports, or integration boundaries.
- Make failure modes, retries, idempotency, ordering, consistency, and observability explicit where relevant.
- Prefer the simplest structure that satisfies current requirements. Avoid abstractions without a current consumer, known variation point, or concrete maintenance problem. Avoid over-modularizing: a small cohesive file does not need to be split.
- Prefer tasks that deliver observable value or a stable dependency boundary over tasks that are technically convenient to implement. Do not create a task solely to introduce a stub, placeholder, or empty interface immediately overwritten by the next task.
- Test code belongs in the same task as the production code it tests, unless the tests require infrastructure introduced by a later task.
- Do not invent file paths, symbols, commands, APIs, or conventions without marking them `(assumed)`.
- Include migrations, configuration, observability, feature flags, and rollback concerns in the task that requires them for safe completion.

# Research Delegation

When a decision at the current depth depends on an unknown repository fact, external behavior, or dependency capability: check `RESEARCH_DIR/INDEX.md` first, then run `swe-research` if the question is clear and bounded, and resume from its report. If research is inconclusive, do not finalize that section at that depth — report the gap instead.

Research is evidence, not permission. A finding changes the spec only once this skill writes it into the owning section; a finding that contradicts the brief goes to `Brief Revision Rule`.

# Contradiction Rule

A deeper pass will sometimes show that a shallower, already-confirmed decision was wrong. Do not quietly rewrite the shallower statement to agree with the new one — the user approved it, and reversing it is a real decision, not a reshape.

1. Stop deepening the affected area.
2. State the shallower decision, the new evidence, and what it would cost to keep it.
3. Offer the options — revise the shallower decision, or constrain the deeper design to fit it — with a recommendation.
4. Apply the user's choice to both places in one pass, so the document never holds both.

This is the main risk of merging high-level and low-level design into one document: without it, the first structure proposed silently survives every pass that should have challenged it.

# Brief Revision Rule

`{BRIEF_FILE}` owns why the change is worth making and how success is judged; every statement in it would still be true if the code were rewritten in another language. This document owns everything that depends on the implementation. Do not restate product justification here, and do not settle a product question here.

If work on any section reveals that the brief itself is infeasible, contradictory, or missing a product decision:

1. Stop work on the affected section.
2. Explain the evidence and which part of the brief it contradicts.
3. Return to `swe-shape` rather than resolving it silently inside `{SPEC_FILE}`.
4. Do not add or deepen content for that section until the brief is corrected.

# Autonomy

Read the repository and linked artifacts, run read-only inspection, and delegate bounded research without asking. Write only `{SPEC_FILE}`, its `spec-<area>.html` splits, and `{MOCKUPS_DIR}`.

Ask first when an investigation is authenticated, paid, mutating, or expensive.

Never modify source code, and never invoke another SWE pipeline stage.

# Constraints

- Do not deepen a section further than the user asked for in this request.
- Do not produce a Markdown counterpart to `{SPEC_FILE}`.

# Output

After writing or updating `{SPEC_FILE}`, report:

- which section(s) were added, rewritten, merged, split, or reordered, and why;
- the current depth reached per section, and which sections are exact-contract complete;
- any confirmed decision this pass challenged, and how it was resolved;
- research created or reused;
- the artifact path, `{SPEC_FILE}`;
- what the user can ask for next (approve, revise this depth, or go deeper on a named section), and whether enough sections are contract-complete to move to delivery.

# Completion Criteria

- `{SPEC_FILE}` reads top-to-bottom as a coherent, freshly-written spec: overall idea and architecture first, exact contracts last, grouped by logical/domain area throughout.
- Every section's content stops exactly at the depth the user has confirmed or explicitly requested — no further, no less.
- No section, once superseded by a deeper pass or a reshape, still exists in its earlier form elsewhere in the document.
- No unresolved design decision blocks the depth just written; genuine tradeoffs were asked about instead of guessed.
- `Document Mechanics` holds: the document is well-formed, ids are stable, and the table of contents matches the sections.
