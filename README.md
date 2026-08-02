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
  --skill swe-architect \
  --skill swe-spec \
  --skill swe-execute

# Install globally
npx skills add SNIKO/agent-skills --all -g
```

Manage installed skills with `npx skills list`, `npx skills find <term>`, and `npx skills update`.

---

# SWE — progressive software development

The workflow separates five kinds of decisions:

```text
Is the idea worth and feasible to build?
        ↓
What architecture should support it?
        ↓
What exact contracts must change?
        ↓
Does execution require coordination?
        ↓
What happened, what remains, and what changed?
```

The corresponding artifacts are deliberately different in scope and lifetime:

```text
.swe-work/<change>/
  brief.md
  architecture.md       # canonical agent contract; optional
  architecture.html     # derived human review artifact
  build-spec.md         # canonical agent contract; optional for tiny/direct changes
  build-spec.html       # derived human review artifact
  plan.md               # canonical agent contract; optional
  plan.html             # derived human review artifact
  state.md              # created during execution
  spikes/               # research reports, probes, responses, and experiments
```

## Artifact ownership

| Artifact | Authoritative for | Must not become |
|---|---|---|
| `brief.md` | Problem, scope, acceptance, feasibility | Architecture or implementation design |
| `architecture.md` | Components, ownership, boundaries, conceptual data, runtime flows | Exact DTO, schema, or file specification |
| `build-spec.md` | Exact shared contracts and repository delta | Product background or architecture narrative |
| `plan.md` | Delivery slices, dependencies, sequencing | Repeated contracts or coding instructions |
| paired `.html` review artifact | Human-readable rendering of its paired Markdown only | An authoritative contract, independent decision record, or agent input |
| `state.md` | Completed work, remaining work, verification, divergence | A copy of the specification |
| spike | Evidence about repository impact, external behaviour, feasibility, or another research question | Product decisions or production implementation |

A decision belongs to one authoritative artifact. Lower-level artifacts link to it rather than restating it. Markdown is the authoritative, token-efficient contract between agents. The paired HTML files are static, direct-from-disk human review surfaces; after human feedback, update the Markdown first and regenerate its HTML counterpart.

## Human-controlled progression

Artifacts do not contain `Draft` or `Approved` metadata. There is no second workflow state machine inside the files:

- invoking the next skill is the human decision to continue from the artifact they supplied;
- a stage asks any required questions before writing its artifact;
- if a stage cannot produce a coherent result, it reports the blocker and does not create or overwrite the downstream artifact;
- `brief.md` may intentionally end with `Spike` or `Stop`, because that is a complete shaping result rather than a partial brief;
- a spike is complete when `spike.md` directly answers its question and indexes the evidence needed to support it;
- `state.md` is the exception because recording execution progress is its actual purpose.

Reviews are advisory assessments, not approval records. The human may revise, proceed, return upstream, or stop.

## Adaptive paths

Not every change runs every skill:

```text
Tiny, obvious, reversible change
  direct implementation → review

Bounded change within existing architecture
  swe-shape → swe-spec → swe-execute → swe-review

Cross-component feature
  swe-shape → swe-architect → swe-spec → swe-execute → swe-review

Multi-session, migration, backfill, or rollout-heavy feature
  swe-shape → swe-architect → swe-spec → swe-plan
            → swe-execute one slice at a time → swe-review

Uncertain or exploratory change, reviewed depth-by-depth
  swe-shape → swe-spec-new (concept → structural → contract, one confirmed
              tier at a time, per scope) → swe-plan or swe-execute → swe-review

Research needed at any stage
  any stage → swe-spike → incorporate findings in the owning artifact → continue
```

Risk, uncertainty, and coordination determine depth. File count alone does not.

## Skills

### `swe-shape` — product intent and feasibility

Inspects the repository, challenges the user's proposed solution, checks feasibility, narrows scope, defines observable acceptance criteria, and recommends the appropriate delivery depth.

- **Artifact:** `brief.md`
- **Outcomes:** `Proceed`, `Narrow`, `Spike`, or `Stop`
- **Excludes:** architecture, DTOs, schemas, interfaces, and implementation tasks

### `swe-spike` — engineering research

Answers a concrete repository, dependency, external-system, feasibility, behaviour, or impact question at any stage. It can inspect the whole codebase, verify authoritative documentation, make safe representative requests, or run disposable experiments.

- **Artifact directory:** `spikes/YYYY-MM-DD-<slug>/`
- **Report:** `spike.md` with the direct answer, findings, implications, evidence links, and material limitations
- **Saved evidence:** runnable probe scripts, captured and sanitized responses, fixtures, benchmark results, query output, or small research-only prototypes when useful
- **Excludes:** silently changing requirements or architecture and treating research code as production implementation

### `swe-architect` — high-level architecture and interface design

Defines top-level components, responsibilities, ownership, dependency direction, conceptual data, conceptual repository layout, and important normal and failure flows. For material user-facing work, it also defines screen responsibilities, navigation, layout hierarchy, visual direction, UI-state ownership, and linked low-fidelity review mockups. It considers credible alternatives but preserves only concise decision rationale. There is no fixed template; sections are shaped to fit the change.

- **Review artifact:** `architecture.html`, authored and iterated directly with the user — must make the current state, the new state, and why it's changing clear on its own
- **Canonical artifact:** `architecture.md`, written only once the user confirms the architecture in `architecture.html`, after which `architecture.html` is regenerated from it like every other paired artifact
- **Excludes:** exact routes, DTOs, SQL columns, migrations, interface signatures, implementation-critical UI/component contracts, and private implementation
- **Optional:** skip when existing architecture already determines the change

### `swe-spec` — low-level build specification

Defines exact behaviour at shared, persistent, externally visible, security-sensitive, and expensive-to-change seams while leaving private implementation flexible.

- **Canonical artifact:** `build-spec.md`
- **Human review artifact:** `build-spec.html` (derived from Markdown)
- **Includes:** exact APIs, DTOs, module contracts, events, schemas, migrations, configuration, failure semantics, repository wiring, and deterministic verification when relevant
- **Excludes:** product narrative, repeated architecture, task sequencing, and method-by-method instructions

### `swe-spec-new` — progressive-disclosure specification

Produces one continuously deepening artifact instead of separately staged ones: a concept pass (idea, top-level components and integration, headline endpoints, UI mockup when applicable), then as many structural or contract passes as the change actually needs — each added only for the scope the user confirms and asks to go lower on. Different scopes (UI, API, data model, and so on) can sit at different depths in the same document across many sessions. There is no fixed template or fixed tier count; depth ordering (abstract on top, exact at the bottom) is the only fixed structure.

- **Canonical artifact:** `spec.html`, authored and deepened directly (no separate Markdown source), read directly by downstream skills such as `swe-plan` and `swe-execute`
- **Excludes:** private methods, classes, helpers, and algorithms at any depth — hands off to `swe-execute` once enough scopes reach exact contracts

### `swe-plan` — optional delivery sequencing

Creates a plan only when implementation needs multiple independently verifiable slices, sessions, agents, migrations, backfills, or rollout coordination.

- **Canonical artifact:** `plan.md`
- **Human review artifact:** `plan.html` (derived from Markdown)
- **Prefers:** thin end-to-end or independently risk-reducing slices
- **Excludes:** repeated contracts, detailed code mutations, and commit commands
- **Skipped:** when `build-spec.md` is one coherent implementation slice

### `swe-artifact` — static human review rendering

Renders a canonical `architecture.md`, `build-spec.md`, or `plan.md` as its paired standalone HTML file. It uses embedded CSS and no server, build step, external dependency, or editing integration. It is a presentation helper used by the corresponding authoring skills, not an independent decision stage.

### `swe-worktree` — optional isolation

Creates a safe git branch and worktree, then copies the complete change workspace into it. It accepts a build spec with or without a plan.

### `swe-execute` — one slice per context

Implements one selected slice, runs deterministic checks, updates `state.md`, and stops. Local implementation discoveries may proceed; discoveries that change requirements, architecture, or shared contracts return to the earliest affected artifact.

### `swe-review` — fresh-context review

Reviews either an SWE artifact or changed code.

Artifact modes cover briefs, spikes, architecture, build specs, plans, and execution state. Code mode scales from quick to comprehensive multi-agent review and checks implementation against available acceptance criteria and contracts.

## Spike evidence across the workflow

Every current SWE skill inspects relevant spikes in the change workspace. Spike findings are evidence, not requirements or design decisions: the owning brief, architecture, build spec, or plan must incorporate a finding before downstream work treats it as authoritative. If a spike contradicts an artifact, return to the earliest affected artifact rather than working around it.

## Revision protocol

Implementation is not treated as waterfall. When lower-level evidence invalidates an upper-level decision:

1. Stop affected downstream work.
2. Identify the earliest incorrect authoritative artifact.
3. Update that artifact or run a bounded spike.
4. Review affected downstream artifacts.
5. Continue when the human invokes the downstream skill with the revised artifacts.

Do not hide an upstream contradiction inside a lower-level artifact or silently make code and documentation agree after the fact.

## Enforcement

Canonical Markdown records intent; deterministic tools enforce it. Static HTML improves human review but does not participate in the agent workflow or replace deterministic checks. Build specs and implementation should map important requirements to the strongest useful mechanism available:

- behavioural, integration, contract, migration, and end-to-end tests;
- type checking and schema validation;
- linting and import-boundary rules;
- CI, hooks, rollout gates, backups, and operational checks.

Use fresh contexts for artifact review, implementation slices, and final code review when practical.

---

# Other skills

- **`git`** — safe branch, commit, and pull-request workflows.
- **`prompt-writer`** — create or revise prompts, agent policies, templates, and skill files using provider-specific guidance.
- **`handoff-session`** — produce a structured end-of-session summary for a fresh agent.

## License

[MIT](LICENSE) © 2026 Sergii Vashchyshchuk
