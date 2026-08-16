---
name: swe-spec
description: "Create and progressively deepen a single evolving specification document for a change, from high-level structure down to exact contracts"
disable-model-invocation: true
---

# Inputs

CHANGE_DIR - the directory for the current change, typically `.swe/<change>`, infer from the conversation if not explicitly named.
BRIEF_FILE: `{CHANGE_DIR}/brief.md`
SPEC_FILE: `{CHANGE_DIR}/spec.html`
MOCKUPS_DIR: `{CHANGE_DIR}/mockups/`
RESEARCH_DIR: `.swe/research/` — shared across all changes, indexed by `INDEX.md`

# Purpose

This `swe-spec` stage is part of the SDLC pipeline for a single change, following the path:

`swe-shape` → `swe-spec` → [`swe-plan`] → [`swe-worktree`] → `swe-execute` → `swe-review`, with `swe-research` available at any point.

Maintain one `{SPEC_FILE}` per change that always reads like a properly written spec: top-down from the overall idea down to exact, implementable detail, organized by logical/domain area the way a human architect would lay it out, and never bigger than the change actually warrants. Depth is earned one user-confirmed step at a time and is never produced further ahead than requested. Every pass rewrites the section(s) it belongs to in place and the document is reshaped as needed so it always reads as freshly authored, never as a log of edits.

# Engineering Stance

You and the user are peer engineers on this document; neither outranks the other, and being asked for something is not authorization to write it down unexamined.

- Evaluate every request against `Design Principles`, the brief, repository conventions, and any evidence already gathered before it goes into the document.
- When a request would produce an unsound, insecure, inconsistent, or unmaintainable design, or conflicts with evidence already gathered, say so plainly in the same pass: state the concern, the reasoning or evidence behind it, and the alternative you'd recommend. Do not silently comply, and do not bury the objection as a footnote.
- Raise it once, with your reasoning. Do not repeat an objection the user has already heard and decided on, and do not withhold the requested content while waiting for agreement.
- The user has the final call. Once they've heard the concern and confirm a direction, write the spec to that direction, record the decision and its rationale where a future reader needs it (a callout, not a silent overwrite of your objection), and move on without relitigating it.
- This does not license inventing scope, second-guessing an already-settled brief decision (`Brief Revision Rule` still governs infeasibility), or turning `Ask only what's blocking`'s narrow question budget into open-ended debate.

# Tools & Skills

`html-artifacts` skill - use for the visual and structural style rules (semantic HTML, diagrams, tables, code blocks, etc.) when writing or rewriting `{SPEC_FILE}`.

# Workflow

1. **Orient.** Read `{BRIEF_FILE}`, the research reports it links, the full current `{SPEC_FILE}` if it exists, and repository context relevant to the requested area. Identify the document's current section layout and how deep each section currently goes.
2. **Resolve shape and depth.** Apply `Document Shape` and `Depth Progression` to determine which section(s) the request touches and the next level of detail for each.
3. **Challenge, then ask only what's blocking.** Apply `Engineering Stance` before writing anything the request implies: raise a concern once if evidence or `Design Principles` disagree with it, then ask the smallest set of questions where two or more options have genuinely different tradeoffs at the depth now being written. Do not ask about decisions the repository, the brief, or a shallower pass already settled. Offer concrete options with a recommendation when one exists.
4. **Delegate unresolved facts.** Where a decision at this depth depends on an unknown repository fact, external behavior, or dependency capability, follow `Research Delegation` before designing that part.
5. **Design the content.** Apply `Design Principles` and decide what a reviewer actually needs to see at this depth: an early pass on a component might need only its responsibility and its contracts with neighbors; a later pass needs its exact interface, states, and error behavior. Choose whatever headings communicate the current decision well. Keep the whole change in mind — do not propose a solution that another section's already-settled constraints would make unworkable, unless that constraint can still change, in which case say so in both places.
6. **Rewrite in place, then reshape.** Update the owning section(s) directly with the new pass — never append a new section or pass to the end of the document. Then review the entire document and merge, split, rename, or reorder sections wherever it now reads better, including sections this request did not touch. Reshaping is for readability and organization; when it would require *reversing a decision*, apply `Contradiction Rule` instead.
7. **Verify coherence and structure.** Read the reshaped document top-to-bottom and confirm it moves from overall idea to exact detail with no gaps, no orphaned cross-references, and no trace of the previous structure. Then apply `Document Mechanics`: valid well-formed HTML and every section carries a stable `id`.
8. **Report.** Follow `Output`.

# Document Shape

A change's spec is organized the way a competent architect would organize any spec for that kind of change — there is no fixed skeleton. A UI feature, a backend API change, a data migration, a bug fix, and a refactor each warrant a different shape, and some may not need sections like API or schema at all. Do not invent sections nobody has asked about yet, and do not force a section to exist just to fill a slot in a template.

Three things guide the shape regardless of what sections exist:

- **Top-down layering.** The document reads from the overall idea and how the pieces fit together, down to exact, implementable contracts. Every spec opens with a short statement of the idea — for a small, single-artifact change this can be the same sentence that states what doesn't fit and why, not a separate section. When there is more than one moving part, an architecture or integration view (e.g. a component/contract diagram) comes before the sections that go into any one part in detail. A section for one logical area never appears before the material it depends on.
- **Domain-logical grouping.** Sections are organized by logical or domain area — the kind of grouping a reader would look for when reviewing that part of the design — not by when the content was written or which conversation produced it. A section born from "let's detail the API" belongs with any other API material already in the document, even if that material was written in an earlier session under a different heading.
- **Addressability.** The document is read by downstream agents that must not load all of it. Every section is a `<section id="...">` with a stable, meaningful id; that id is enough for a downstream stage to read the section it needs directly, so the document does not open with a rendered table of contents.

User-facing changes may link mockups or wireframes; keep those as separate files in `{MOCKUPS_DIR}` and link them from the owning section rather than inlining them.

# Depth Progression

Depth is how exact the current pass is for whatever section(s) are in play, ranging from the most abstract useful statement of an idea down to an exact, implementable contract. There is no fixed number of depth steps: decide, per section and per change, how many distinct passes are actually worth reviewing separately. A trivial section may only ever need two passes (concept, exact); a genuinely complex one may warrant several. A new pass is justified only if a reviewer could plausibly approve the section as it stands now and reject the next pass as a separate, real decision — never add a pass that only adds words without adding a decision.

Determine the depth for the current request as follows:

- **No area or depth stated, document does not exist yet:** determine shape and depth from the change itself. A change is small when it lands inside one owned artifact — one table, one store, one interface, one module — and does not add a new cross-component pipeline stage, a new external contract, or a new failure boundary; it stays small even when it touches many fields or columns. For a small change, write the whole spec at its final, exact depth in one pass, shaped around what actually changed: current state, why it doesn't fit, the target contract, and the diff between them. Do not add an Approach section, an architecture/integration view, a multi-stage processing lifecycle, a repository-impact list, or a verification-strategy section the change does not need — one or two sections following the `DB`/`Code` diff framing below is a complete spec for this case. If the change is genuinely larger — it spans multiple components that each warrant their own review pass, or introduces a pipeline or contract that did not exist before — produce the shallowest useful pass across the whole document first: the overall idea or approach, top-level components/responsibilities and how they integrate, headline API operations or user-facing entry points if applicable, and a UI mockup or wireframe if user-facing. Keep it one reviewable pass, not an exhaustive one.
- **Area and/or depth stated explicitly** (e.g. "just the API", "design the database tables", "high level only"): honor the requested area and go straight to the requested depth for it, even if that skips passes that would normally come first. Still add a brief entry at each shallower depth for that area if none exists, so the document stays readable top-down — a short statement, not a placeholder.
- **User asks to go deeper, get more detail, or approves and asks to continue:** advance the section just discussed (or named) exactly one pass further than what already exists there. Never skip ahead to a deeper pass than requested, and never silently deepen a section the user did not ask about.
- **A section already has content and the user gives feedback without asking to go deeper:** treat it as a revision at its current depth, not an advance.

At every depth, regardless of section: do not specify private methods, internal helper functions, local variables, or algorithm internals unless a stated correctness, security, or contract requirement genuinely depends on them.

# Document Mechanics

- **Stable ids.** Each section keeps its `id` across passes; downstream artifacts and `plan.md` link to them. When a reshape genuinely renames a section, update every reference to it in the same pass.
- **Well-formedness.** After every edit, verify the document parses: no unclosed or orphaned tags, no duplicated ids, no broken local links. In-place editing of nested markup is the main mechanical failure mode of this skill — check rather than assume.
- **Split threshold.** A single file is the default. Once the document exceeds roughly 1500 lines or several areas have independently reached exact-contract depth, split by domain area into `spec-<area>.html` and reduce `{SPEC_FILE}` to the overview, the architecture view if one exists, and a linked index of each area file. The document stays logically one spec; only its storage changes.

# Document Content

## General

- Avoid long prose paragraphs; use short paragraphs, bullet lists, and tables to make the spec easy to read and scan.
- If it is easier to explain in code than in prose or table and the code is expected to be presented in next spec depth disclosure anyways, then show the code
- Do not use tables with multiple multiline columns
- Do not add table of contents 

## Diagrams

- Use different colors, shapes, or line styles to distinguish different kinds of components, data, or flows.
- Make it clear visually which components are existing ones and which are new or changed.

## UI

- Mockups, wireframes, or screenshots of the current and proposed UI.

## DB

- Show the existing schema next to the proposed schema: table and column names, types, constraints, and relationships.
- Show the difference — what is added, changed, or removed — not just the final schema.

## Code

- Show diffs when existing abstractions are being changed.

# Design Principles

- Search `RESEARCH_DIR/INDEX.md` for prior findings before re-investigating; see `Research Delegation` for new questions.
- Link the research reports a decision depends on from the section that depends on them, so downstream stages follow the link instead of scanning the index.
- Use repository-specific design/coding rules and conventions, mixed with the general principles below. In case of conflict, use the repository's rules.
- Design boundaries and contracts so local implementation defects are contained and cannot violate system-wide invariants, public contracts, data integrity, security boundaries, or integration guarantees.
- Prefer clear ownership of data, side effects, lifecycle transitions, and cross-module contracts. Every cross-module contract has a clear owner responsible for compatibility and change coordination.
- A contract is only complete once it gives the implementer every input, dependency, and signal their component needs.
- Minimize shared mutable state; make ownership and mutation rules explicit where shared state is unavoidable.
- Isolate external I/O behind explicit clients, adapters, ports, or integration boundaries.
- Prefer the simplest structure that satisfies current requirements. Avoid abstractions without a current consumer, known variation point, or concrete maintenance problem. Avoid over-modularizing: a small cohesive file does not need to be split.
- When the brief describes evolving something that already exists, modify that table, store, or module in place. Only introduce a new one when the existing artifact cannot satisfy the target contract even after modification, and state why in the section that introduces it — do not split one artifact into two, or add a new pipeline stage, to make room for a bigger document.
- Prefer tasks that deliver observable value or a stable dependency boundary over tasks that are technically convenient to implement. Do not create a task solely to introduce a stub, placeholder, or empty interface immediately overwritten by the next task.
- Test code belongs in the same task as the production code it tests, unless the tests require infrastructure introduced by a later task.

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

# Output

After writing or updating `{SPEC_FILE}`, report:

- which section(s) were added, rewritten, merged, split, or reordered, and why;
- the current depth reached per section, and which sections are exact-contract complete;
- any engineering concern raised or confirmed decision this pass challenged, and how the user resolved it;
- research created or reused;
- the artifact path, `{SPEC_FILE}`;
- what the user can ask for next (approve, revise this depth, or go deeper on a named section), and whether enough sections are contract-complete to move to delivery.

# Completion Criteria

- `{SPEC_FILE}` reads top-to-bottom as a coherent, freshly-written spec: overall idea first, exact contracts last, grouped by logical/domain area throughout.
- Every section's content stops exactly at the depth the user has confirmed or explicitly requested — no further, no less.
- No section, once superseded by a deeper pass or a reshape, still exists in its earlier form elsewhere in the document.
- No unresolved design decision blocks the depth just written; genuine tradeoffs were asked about instead of guessed.
- Any request that conflicted with `Design Principles`, the brief, or gathered evidence was raised once with reasoning before being written in, and the final content reflects the user's decision either way.
- The document's length and section count are proportional to the scope of the underlying change: a single-artifact modification (one table, one store, one interface) is never expanded into a multi-subsystem redesign, extra pipeline stages, or a verification-strategy grid it does not need.
- `Document Mechanics` holds: the document is well-formed and ids are stable; no section relies on a rendered table of contents to be found.
