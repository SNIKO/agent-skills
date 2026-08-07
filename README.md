# agent-skills

A collection of skills for coding agents such as Claude Code, Cursor, Codex, opencode, Copilot, and Windsurf. Its centerpiece is a lightweight, spec-driven software workflow built around progressive disclosure rather than a mandatory mega-spec.

## Installation

Install with the [`skills`](https://github.com/vercel-labs/skills) package manager:

```bash
# Install everything into every detected agent
npx skills add SNIKO/agent-skills --all

# Install selected SWE skills
npx skills add SNIKO/agent-skills \
  --skill swe-shape \
  --skill swe-research \
  --skill swe-spec \
  --skill swe-execute

# Install globally
npx skills add SNIKO/agent-skills --all -g
```

Manage installed skills with `npx skills list`, `npx skills find <term>`, and `npx skills update`.

---

# SWE — progressive software development

The workflow separates four kinds of decisions:

```text
Is the idea worth and feasible to build?
        ↓
What should be built, and at what level of detail?
        ↓
Does execution require coordination?
        ↓
What happened, what remains, and what changed?
```

Research is not a stage. It runs at any point, from any stage, into a corpus shared by every change:

```text
.swe/
  research/                     # shared across all changes, outlives them
    INDEX.md                    # generated from report front matter
    <subject>/
      report.md
      artifacts/
  <change>/
    brief.md                    # why, and how we know it worked
    spec.html                   # what changes — high level down to exact contracts
    spec-<area>.html            # only once the spec is large enough to split
    mockups/                    # linked from the spec for user-facing work
    plan.md                     # optional delivery slices
    state.md                    # created during execution
```

## Artifact ownership

| Artifact | Authoritative for | Must not become |
|---|---|---|
| `brief.md` | Problem, acceptance criteria, exclusions, feasibility | Any statement about how it is built |
| `spec.html` | Structure, boundaries, ownership, flows, and exact contracts — each section at its own depth | Product justification, or a delivery schedule |
| `plan.md` | Delivery slices, dependencies, sequencing | A restatement of the spec |
| `state.md` | Completed work, remaining work, verification, divergence | A copy of the specification |
| research `report.md` | Evidence about the repository, an external system, a dependency, or feasibility | Product decisions or production implementation |

A decision belongs to one artifact. Lower-level artifacts link to it rather than restating it.

### The brief/spec boundary

`brief.md` answers **why, and how will we know it worked**. `spec.html` answers **what changes**.

The test: every statement in the brief must still be true if the codebase were rewritten from scratch in a different language. Anything that would change belongs in the spec.

- "p95 under 300ms" → brief. "Response cached in Redis with a 60s TTL" → spec.
- "Must not break the v1 API contract" → brief. "The v1 adapter maps `foo` to `bar`" → spec.
- "Must reuse the existing job queue" → spec. That is a solution choice, not a requirement.

### One spec, not architecture plus spec

There is no separate architecture artifact. The line between high-level and low-level design is not stable — it moves between repositories, and between changes in the same repository. Splitting it across two documents reliably produces the same decision half-stated in two places, with neither authoritative.

`swe-spec` owns both. Levels survive as **depth within a section**, which is where they belong: the API section can sit at exact contracts while the data model is still one paragraph, because that is genuinely where the change is. Depth advances one confirmed step at a time, per section, and the human approves each one.

### Formats

`spec.html` is HTML because a spec is tables, diagrams, diffs, and mockups, and because it is reviewed continuously. It is canonical — read directly by `swe-plan`, `swe-execute`, and `swe-review`, with no Markdown counterpart to keep in sync. Everything else is Markdown, because a one-screen brief, a slice list, and a progress log gain nothing from rich rendering and are cheaper to rewrite.

## Human-controlled progression

Artifacts carry no `Draft` or `Approved` metadata. There is no second workflow state machine inside the files:

- invoking the next skill is the human decision to continue from the artifact they supplied;
- a stage asks any required questions before writing its artifact;
- if a stage cannot produce a coherent result, it reports the blocker and does not create or overwrite the downstream artifact;
- `brief.md` may intentionally end with `Blocked` or `Stop`, because that is a complete shaping result rather than a partial brief;
- in `spec.html` the confirmation gate is per section and per depth, not per document;
- `state.md` is the exception, because recording execution progress is its actual purpose.

Reviews are advisory assessments, not approval records.

## Adaptive paths

Not every change runs every skill:

```text
Tiny, obvious, reversible change
  direct implementation → swe-review

Ordinary change
  swe-shape → swe-spec (as deep as it needs) → swe-execute → swe-review

Multi-session, migration, backfill, or rollout-heavy change
  swe-shape → swe-spec → swe-plan → swe-worktree
            → swe-execute (one sub-agent per slice) → swe-review

Research needed at any point
  any stage → swe-research → link the report from brief.md or spec.html → continue
```

Risk, uncertainty, and coordination determine depth. File count does not.

## Skills

### `swe-shape` — product intent and feasibility

Inspects the repository, challenges the user's proposed solution, checks feasibility, narrows scope, and defines observable acceptance criteria. The brief fits on one screen; if it does not, the change is too large.

- **Artifact:** `brief.md`
- **Outcomes:** `Proceed`, `Narrow`, `Blocked`, or `Stop`
- **Excludes:** anything that would change if the implementation changed

### `swe-research` — engineering research

Answers a concrete repository, dependency, external-system, feasibility, behaviour, or impact question at any stage. It can inspect the whole codebase, verify authoritative documentation, make safe representative requests, or run disposable experiments.

Findings go into **one shared corpus per repository, organized by subject** — not per change. Changes in the same repository ask about the same domain and the same external services, so `swe-research` searches the corpus first and reuses or extends an existing subject before creating a new one. `INDEX.md` is generated from report front matter, so parallel branches never conflict on it and it cannot drift.

- **Corpus:** `.swe/research/<subject>/report.md` plus `artifacts/`
- **Report:** a standing `Current understanding` rewritten in place, plus dated findings that are appended and superseded rather than overwritten
- **Saved evidence:** runnable probe scripts, sanitized responses, fixtures, benchmark results, query output, or small research-only prototypes
- **Discovery:** the index is for `swe-research` itself; other stages follow the direct report links recorded in `brief.md` and `spec.html`
- **Excludes:** silently changing requirements or design, and treating research code as production implementation

### `swe-spec` — the specification, at every level

One evolving `spec.html` per change, read top-down from the overall idea and how the pieces fit, down to exact implementable contracts, grouped by domain area. Depth is earned one confirmed step at a time and never produced further ahead than requested. Different areas sit at different depths in the same document, across many sessions. Every pass rewrites the owning section in place and reshapes the document, so it always reads as freshly authored rather than as a log of edits.

When a deeper pass shows an already-confirmed shallower decision was wrong, it stops and surfaces the contradiction rather than quietly rewriting it — reversing an approved decision is a decision, not a reshape.

- **Canonical artifact:** `spec.html`, read directly by `swe-plan`, `swe-execute`, and `swe-review`
- **Includes:** components, ownership, dependency direction, data flows, failure behaviour, and exact APIs, DTOs, schemas, migrations, configuration, and wiring — each at the depth that area has reached
- **Excludes:** private methods, helpers, and algorithms at any depth, unless a correctness, security, or contract requirement depends on them
- **Structure:** stable section ids and a table of contents, so downstream stages load only the sections they need; splits into `spec-<area>.html` once it outgrows one file

### `swe-plan` — optional delivery sequencing

Creates a plan only when implementation needs multiple independently verifiable slices, sessions, agents, migrations, backfills, or rollout coordination. Most changes skip it. A large spec is not a reason to plan; a spec with multiple independently verifiable outcomes is.

- **Artifact:** `plan.md`
- **Prefers:** thin end-to-end or independently risk-reducing slices
- **Excludes:** restated contracts, detailed code mutations, and commit commands — the plan says what order and how it is verified, and links `spec.html` section ids for everything else

### `swe-worktree` — optional isolation

Creates a safe git branch and worktree and copies the change workspace into it. The shared research corpus is deliberately not copied: it is repository documentation, not change scope, and forking it per branch would guarantee divergence.

### `swe-execute` — orchestrated slices, fresh context each

Orchestrates implementation: each slice goes to its own fresh sub-agent, briefed with only the spec sections, acceptance criteria, and verification commands it needs. The orchestrator never trusts the report — it re-runs the deterministic checks, reviews the diff against the contracts, updates `state.md`, and moves to the next slice. Its own context stays small; implementation context is disposable. Local implementation discoveries may proceed; discoveries that change requirements or shared contracts halt the run and return to the earliest affected artifact.

### `swe-review` — fresh-context review

Reviews either an SWE artifact or changed code.

Artifact mode covers briefs, research reports, specs, plans, and execution state. It reviews each spec section **at the depth it claims** — a shallow section is not a defect, but a shallow section presented as implementable is. Code mode scales from quick to comprehensive multi-agent review and checks implementation against available acceptance criteria and contracts.

## Research across the workflow

Every SWE skill checks the shared corpus before investigating, and links the reports a decision depends on from the artifact that depends on them. Findings are evidence, not requirements or design: the owning brief or spec must incorporate a finding before downstream work treats it as authoritative. If a finding contradicts an artifact, return to the earliest affected artifact rather than working around it.

## Revision protocol

Implementation is not treated as waterfall. When lower-level evidence invalidates an upper-level decision:

1. Stop affected downstream work.
2. Identify the earliest incorrect artifact.
3. Update that artifact, or run bounded research.
4. Review affected downstream artifacts.
5. Continue when the human invokes the downstream skill with the revised artifacts.

Do not hide an upstream contradiction inside a lower-level artifact or silently make code and documentation agree after the fact.

## Enforcement

Artifacts record intent; deterministic tools enforce it. Specs and implementation should map important requirements to the strongest useful mechanism available:

- behavioural, integration, contract, migration, and end-to-end tests;
- type checking and schema validation;
- linting and import-boundary rules;
- CI, hooks, rollout gates, backups, and operational checks.

Use fresh contexts for artifact review, implementation slices, and final code review when practical.


# Other skills

- **`git`** — safe branch, commit, and pull-request workflows.
- **`prompt-writer`** — create or revise prompts, agent policies, templates, and skill files using provider-specific guidance.
- **`handoff-session`** — produce a structured end-of-session summary for a fresh agent.

## License

[MIT](LICENSE) © 2026 Sergii Vashchyshchuk
