# agent-skills

A collection of skills for any coding agent (Claude Code, Cursor, Codex, opencode, Copilot, Windsurf, and [others](https://github.com/vercel-labs/skills)). Its centerpiece is **SWE**, a spec-driven development framework that takes a change from a vague idea all the way to an isolated, ready-to-code worktree — one reviewable artifact at a time.

## Installation

The easiest way to install is with [`npx skills`](https://github.com/vercel-labs/skills), the open agent-skills package manager. It auto-detects which agents you have installed and wires the skills into each of them.

```bash
# Install everything into every detected agent
npx skills add SNIKO/agent-skills --all

# Or pick specific skills
npx skills add SNIKO/agent-skills --skill swe-brainstorm --skill swe-design

# Target specific agents (default: all detected)
npx skills add SNIKO/agent-skills -a claude-code -a cursor --all

# Install globally, across all your projects
npx skills add SNIKO/agent-skills --all -g
```

Manage installed skills with `npx skills list`, `npx skills find <term>`, and `npx skills update`.

Once installed, invoke a skill by name, e.g. `/swe-brainstorm`.

---

# SWE — a spec-driven development framework

Like [Spec Kit](https://github.com/github/spec-kit), [OpenSpec](https://github.com/Fission-AI/OpenSpec), and [Superpowers](https://github.com/obra/superpowers), SWE replaces "prompt the model and hope" with a **staged pipeline**. Each stage has one job, produces one durable artifact, and pauses for your review before the next stage builds on it. The model never silently jumps from a half-formed idea to code.

The core idea: **separate deciding *what* from deciding *how* from deciding *in what order* — and write each decision down.** By the time code gets written, intent, constraints, architecture, and sequencing are all settled, reviewed, and on disk. Any implementer (human or agent) can pick up the plan and execute without re-deriving the reasoning.

## The pipeline

<img width="1012" height="1066" alt="image" src="https://github.com/user-attachments/assets/810838c1-2f9f-4926-ac8b-455e2c331806" />

Every artifact lands in a single per-change workspace so the whole trail stays together:

```
.swe-work/
  2026-07-12-auth-refresh/
    01-proposal.md
    02-context.md
    03-design.md
    04-plan.md
    references/
      oauth-provider-api.md
      session-store.md
```

`swe-review` sits alongside the pipeline and can be run at any point on a diff, branch, or PR.

## The stages

### 1. `swe-brainstorm` — decide *what*

Turns a vague intent into an **approved, scoped change**. It inspects the repo for existing terminology and conventions, then asks the smallest useful set of high-value questions (with concrete options) until the change is properly scoped — problem, in/out-of-scope behavior, constraints, and observable success criteria.

- **Deliberately excludes** architecture, components, schemas, libraries, and implementation. That's a later stage's job.
- **Artifact:** `01-proposal.md` — problem/motivation, goals, non-goals, requirements (`FR-`/`NFR-`/`CON-`), acceptance examples, success criteria, and open questions.
- **Gate:** you review and approve the proposal before anything else happens.

### 2. `swe-context` — gather the *evidence*

For non-trivial, cross-module, unfamiliar, or externally-integrated changes, this builds an **evidence-based context package** so design and planning don't repeat discovery. It reads the relevant repo areas and, when the change touches external APIs/standards, fetches their real constraints (auth, API, DTOs, rate limits, schemas, error semantics, versioning).

- **Deliberately excludes** any architectural or implementation decision — it only collects facts, each linked to a source.
- **Artifacts:** `02-context.md` (a concise, indexed digest — repo map + confirmed constraints) plus `references/*.md`, one file per evidence topic. Every non-trivial claim links back to a reference.
- **Optional:** skip it for small, well-understood changes.

### 3. `swe-design` — decide *how*

Turns the approved proposal (and context) into a concise, reviewable **high-level design**. It focuses on the things that are expensive to get wrong: module boundaries, component responsibilities and data ownership, cross-component data flow, and the contracts between components. Where a decision has a clear winner it's made autonomously; where there's a genuine tradeoff, it asks.

- **Deliberately excludes** low-level internals — private methods, local algorithms, variable names — unless they're needed to satisfy a requirement, contract, or correctness/security/performance property.
- **Artifact:** `03-design.md` — selected approach and tradeoff, requirement-coverage table, key decisions, repo-structure diff, components, data flow, and boundary contracts.
- **Gate:** you review and approve the design before planning.

### 4. `swe-plan` — break it into *workable, fully-specified pieces*

Decomposes the approved design into an **ordered sequence of self-contained tasks, each specified in enough detail to implement without guessing**. This is where the low-level design lands — the internals `swe-design` deliberately left out. Every task is sized to a single focused session, is independently verifiable, and leaves the repo in a valid, committable state on its own; the plan spells out exact file locations, inputs/outputs, invariants, error handling and edge cases, cross-task contracts, verification steps, and commit boundaries, with no placeholders or "TBD". It's written for a skilled implementer who *doesn't know this codebase* and shouldn't have to re-read the design to build any single task.

Two jobs, then, not one: **carve the design into the right pieces**, and **pin down the concrete detail of each piece** so it can be coded, verified, and committed on its own.

- **Artifact:** `04-plan.md` — change/implementation overview, a requirement→task coverage map, and per-task goal, detailed steps, verification checklist, and commit command.

### 5. `swe-worktree` — isolate for *implementation*

Sets up an **isolated git worktree** for the approved plan: derives the branch name and worktree path (respecting repo conventions), creates the branch and worktree, and copies the full change workspace into it. Implementation happens on a clean branch without touching your main working tree.

- It won't install dependencies, run tests, push, or open a PR — those stay with the implementer.

## Why the staging matters

- **Review small, decide early.** Catching a scoping or boundary mistake in a 100-line proposal is cheap; catching it in a 2,000-line diff is not.
- **Each stage refuses to do the next stage's job.** Brainstorm won't design; design won't implement. This keeps decisions from being made implicitly and prematurely.
- **The artifacts are the memory.** Context isn't re-discovered, decisions aren't re-litigated, and any agent or teammate can resume from the files on disk.
- **You stay in control.** Explicit approval gates after the proposal and design mean the pipeline advances only when you're satisfied.

## `swe-review` — multi-agent code review

Standalone and pipeline-agnostic — it doesn't require the SWE pipeline at all. Point it at **any PR, branch, staged, or unstaged diff** in **any repository**, whether or not that change was ever brainstormed, designed, or planned with SWE. It's equally happy reviewing a one-line hotfix and a change that went through the full pipeline.

It can also review the pipeline's own intermediate artifacts, not just final code — run it against a `03-design.md` to sanity-check a design, against a `04-plan.md` to check task sequencing and coverage before implementation starts, or against the implementation diff itself. This lets you catch scoping, design, or planning problems before they turn into code.

It's a **coordinator that scales effort to change risk**:
- **Scope detection** — resolves the diff from a PR number/URL, the current branch's open PR, commits ahead of main, staged changes, or unstaged changes, in that priority order.
- **Repository-rule aware** — reads `AGENTS.md`/`CLAUDE.md`/`.agents/` conventions first and adjusts what it checks (e.g. skips security or performance findings if the repo says those aren't priorities).
- **Risk-based tiering** — classifies the change as **Quick** (tiny, low-risk diffs), **Standard** (bounded, moderate changes), or **Comprehensive** (broad or high-risk changes: auth, payments, migrations, public API/schema, CI/release, infra, etc.), and scales the number of specialist agents accordingly.
- **Parallel specialist agents**, dispatched only where relevant to the tier: bugs & regression, code quality, design (is there a simpler proven alternative approach?), spec compliance, documentation, security, performance, and release/CI risk.
- **Verification pass** — dedupes findings across agents and filters out speculative or low-confidence issues, so the output favors a small number of confirmed, actionable problems over a long list of noise.

The result is one concise review you can request against a design, a plan, or actual code — for pipeline-driven changes or completely ad hoc ones.

---

# Other skills

Standalone, general-purpose skills unrelated to the SWE pipeline:

- **`git`** — git operations: branch creation, committing changes, and creating pull requests.
- **`prompt-writer`** — create, review, or revise prompts, system prompts, agent policies, and skill files. Routes to OpenAI, Anthropic/Claude, or generic prompt-writing guidance based on the target model.
- **`handoff-session`** — produce a structured end-of-session summary to hand off or compact the current chat.

---

## License

[MIT](LICENSE) © 2026 Sergii Vashchyshchuk
