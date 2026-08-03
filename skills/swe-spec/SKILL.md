---
name: swe-spec
description: "Create and progressively deepen design and specification for a change"
disable-model-invocation: true
---

# Purpose

Maintain one evolving `{SPEC_FILE}` per change, always readable top-to-bottom from the most abstract decisions to the most exact ones. Depth is earned per scope, one user-confirmed tier at a time, and is never produced further ahead than requested. Different scopes of the same change (UI, an API surface, the data model, an integration, and so on) may sit at different depths simultaneously, each clearly separated, so the same document keeps growing correctly across many sessions instead of being regenerated from scratch.

# Inputs

CHANGE_DIR - the directory for the current change, typically `.swe-work/<change>`, infer from the conversation if not explicitly named.
BRIEF_FILE: `{CHANGE_DIR}/brief.md` - the brief for the change
SPEC_FILE: `{CHANGE_DIR}/spec.html` - the canonical specification artifact, progressively deepened in place per scope and tier

# Tools & Skills

[html-document-writer](../html-document-writer/SKILL.md) - use when generating or updating `{SPEC_FILE}` to produce a human-reviewable HTML artifact in the required style and format.

# Workflow

1. **Orient.** Read the `{BRIEF_FILE}`, the full current `{SPEC_FILE}` if it exists, and repository context relevant to the requested scope. Identify every scope already present and the tier each has reached.
2. **Resolve the request.** Apply the Scope and Depth Model above to determine which scope(s) to touch and the target tier for each.
3. **Ask only what's blocking.** Ask the smallest set of questions where two or more options have genuinely different tradeoffs at the target tier; do not ask about decisions the repository, the brief, or a shallower tier already settled. Offer concrete options with a recommendation when one exists.
4. **Design the content for that scope and tier.** Decide what a reviewer actually needs to see at this depth for this kind of scope. For instance, a UI scope's shallowest tier might need a wireframe, screen list, and navigation shape, while its deepest tier needs exact component contracts, states, and copy; a data scope might move from conceptual entities to exact tables, columns, and migrations; an API scope might move from named operations and payload shapes to exact routes, DTOs, and error codes. Choose whatever headings communicate this specific decision well.
5. **Keep private detail out.** Regardless of tier, do not specify private methods, internal helper functions, local variables, or algorithm internals unless a correctness, security, or contract requirement genuinely depends on them.
6. **Integrate, do not regenerate.** Merge the new or revised content directly into `{SPEC_FILE}` at the right tier section, next to its scope's existing material. Leave every other scope and tier untouched. If a deeper decision changes something a shallower tier asserted, update that shallower statement so the two never contradict; do not duplicate the same decision at both tiers.
7. **Maintain the index.** Keep a short top-of-document index listing each scope and the deepest tier it has reached, so a returning session can immediately see what exists and what can still be requested.
8. **Report.** After writing `{SPEC_FILE}`, ask the user to review it. State what depth is now available for which scope(s), and that they can approve, request changes at the current tier, or ask to go deeper on any scope.

# Scope and Depth Model

Two independent things must be identified before writing anything:

- **Scope** — the topic being specified right now (for example, UI, an API surface, the data model, an integration, or rollout/config). A change accumulates as many scopes as it actually needs; do not pre-create scopes nobody has asked about yet.
- **Depth tier** — how exact the current pass is for that scope, ranging from the most abstract useful statement of the idea down to the most exact contract. There is no fixed set or count of tiers: decide, per scope and per change, how many distinct levels of detail are actually worth reviewing separately. A trivial scope may only ever need two tiers (concept, exact); a genuinely complex one may warrant three or four. Do not invent an intermediate tier that adds ceremony without adding a real reviewable decision.

The only fixed rule is ordering: within the document as a whole, content belonging to a shallower tier must never sit below content from a deeper tier. Organize the document primarily by tier, shallowest first, with every scope's material for that tier grouped underneath it; a scope that hasn't reached a tier yet simply has nothing there yet.

## Determining the tier for the current request

- **No scope or depth stated, and the scope has no prior content:** produce the shallowest useful tier for the whole change — normally the idea or approach, the top-level components or responsibilities and how they integrate, headline API operations or user-facing entry points if applicable, and a UI mockup or wireframe if the change is user-facing. Keep it one reviewable pass, not an exhaustive one.
- **Scope and/or depth stated explicitly** (for example "just the UI", "design the database tables", "high level only"): honor the requested scope and go straight to the requested depth for it, even if that skips tiers that would normally come first. Still add a brief entry at each shallower tier for that scope if none exists, so the document stays readable top-down — a short summary, not a placeholder.
- **User asks to go deeper, go lower, get more detail, or approves and asks to continue:** advance the scope just discussed (or the scope named) exactly one tier further than what already exists for it. Never skip ahead to a deeper tier than requested, and never silently deepen a scope the user did not ask about.
- **A scope already has content and the user gives feedback without asking to go deeper:** treat it as a revision at its current tier, not an advance.

# Decision Rules

- A new tier for a scope must contain at least one decision a reviewer could not already get from the shallower tier; if it would only restate the shallower tier in different words, it is not a new tier.
- Prefer extending an existing scope section over creating a near-duplicate one; only split a scope into two when they have genuinely different owners, audiences, or review cadences.
- When a scope's deepest tier reaches exact, unambiguous contracts (routes, schemas, migrations, component props, and equivalents) sufficient to implement without further design decisions, say so explicitly — that scope is complete, not merely detailed.
- When most or all in-scope areas of the change have reached that exact tier, recommend `swe-plan` (if delivery needs coordinated slices) or proceeding straight to implementation; otherwise state which scopes still need deepening.
- If a request would require inventing a product or architectural decision rather than choosing among informed options, ask instead of guessing.

# Research Delegation

When a decision at the current tier depends on an unknown repository fact, external behaviour, or dependency capability, run `swe-spike` automatically when the question is clear, bounded, and safe; preserve its evidence under the change workspace and resume from it. Ask the user first for expensive, authenticated, or mutating investigation. If research is inconclusive, do not finalize that tier for that scope; report the gap instead.

# Brief Revision Rule

If work on any scope or tier reveals that the brief itself is infeasible, contradictory, or missing a product decision:

1. Stop work on the affected scope.
2. Explain the evidence and which part of the brief it contradicts.
3. Return to `swe-shape` rather than resolving it silently inside `{SPEC_FILE}`.
4. Do not add or deepen content for that scope until the brief is corrected.

# Constraints

- Do not use a fixed template of sections or a fixed number of tiers; decide structure per change and per scope, keeping only the ordering rule fixed: shallower tiers above deeper ones.
- Do not deepen a scope further than the user asked for in this request.
- Do not silently regenerate or reorder scopes or tiers the user did not ask about.
- Do not specify private implementation detail unless a stated requirement genuinely depends on it.
- Do not begin implementation.

# Output

After writing or updating `{SPEC_FILE}`, report:

- which scope(s) and tier(s) were added or revised;
- the current deepest tier reached per scope, and which scopes (if any) are exact-contract complete;
- the artifact path, `.swe-work/<change>/spec.html`;
- what the user can ask for next (approve, revise this tier, or go deeper on a named scope), and whether enough scopes are contract-complete to move to delivery.

# Completion Criteria

- `{SPEC_FILE}` reads top-to-bottom from the most abstract material to the most exact, across every scope it contains.
- Every scope's content stops exactly at the tier the user has confirmed or explicitly requested — no further, no less.
- The top-of-document index correctly reflects every scope present and its deepest tier.
- No unresolved design decision blocks the tier just written; genuine tradeoffs were asked about instead of guessed.
