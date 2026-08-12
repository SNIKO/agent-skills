# Code Review

# Objective

Run a repository-aware review that scales to change risk, checks implementation against available intent and SWE artifacts, uses focused specialist agents, verifies their findings, and returns one concise actionable verdict.

# Scope Resolution

Resolve the diff in this order:

1. User-provided PR number or URL: use PR diff, title, body, comments, and linked requirements when available.
2. Current branch with an open PR: review the PR diff and warn if local changes are outside scope.
3. Branch commits ahead of main or master: review `<base>...HEAD` and warn about local changes.
4. Staged changes.
5. Unstaged changes.

Print one scope line and at most one material out-of-scope warning.

# Repository Context

Read the smallest useful set of:

- root and nearest `AGENTS.md`;
- `CLAUDE.md` and relevant `.agents/` rules;
- PR or issue requirements;
- `.swe/<change>/brief.md`, `spec.html`, `plan.md`, `state.md`, and the `.swe/research/*/report.md` reports those artifacts link, with saved artifacts when a claim depends on them;
- package or module documentation directly affected by the diff.

Repository rules override general preferences. Treat unstated requirements as unknown.

# Review Workspace

For Standard or Comprehensive review, create a temporary directory such as `.tmp/code-review/<context>/<timestamp>/` containing:

- `shared-context.md`: scope, intent, acceptance criteria, repository profile, rules, base/head refs, selected agents, and the finding schema below;
- `manifest.tsv`: changed path, status, lines, kind, risk tags, and assigned agents;
- `diffs/`: one patch per changed file;
- `agent-inputs/<agent>.md`: only assigned patches and exact surrounding files the agent may inspect.

Do not paste the complete diff into every agent prompt. Remove temporary review files before completion unless repository rules require retention.

Agent prompts are in `agents/` relative to the `swe-review` skill directory.

# Noise Filtering

Exclude lockfiles, vendored/generated/minified/source-map output, and pure formatting churn from normal review unless they are the only changes or directly affect runtime, release, or required generated consistency.

Never exclude migrations, public schemas, security policy, CI/release configuration, infrastructure, auth, crypto, or generated artifacts that affect runtime behaviour merely because they look generated.

# Risk Tiers

## Quick

Use for tiny, low-risk changes: normally at most 50 effective changed lines, at most 5 files, no production behaviour risk, deleted tests, or high-risk tags.

Run `agents/quick-review.md`.

## Standard

Use for bounded moderate changes: normally at most 200 effective lines and 20 files with no high-risk tags.

Run in parallel when supported:

- `agents/design.md`
- `agents/bugs-and-regression.md`
- `agents/code-quality.md`
- `agents/documentation.md` only when documentation, examples, public text, or agent instructions changed or are required

## Comprehensive

Use when thresholds are exceeded or any material high-risk tag applies:

- auth, permissions, sessions, crypto, secrets, or trust-boundary parsing;
- payments, deletion, persistence, migration, concurrency, retry, or timeout behaviour;
- public API, SDK, CLI, event, schema, or serialization contracts;
- deployment, CI, infrastructure, packaging, dependency, or toolchain changes;
- cross-cutting restructuring, deleted tests, or broad shared-library changes.

Run:

- `agents/design.md`
- `agents/spec-compliance.md`
- `agents/bugs-and-regression.md`
- `agents/code-quality.md`
- relevant optional specialists: documentation, security, performance, release

Repository rules may de-emphasize a tag, but obvious secret exposure, data loss, or broken deployment remains relevant.

# Specialist Responsibilities

- **Quick review:** obvious high-confidence issues for tiny changes.
- **Spec compliance:** selected requirements, acceptance, wiring, scope, and `spec.html` structure and contracts.
- **Bugs and regression:** concrete runtime correctness, changed callers, state, failures, and behavioural tests.
- **Code quality:** maintainability and simplicity within the chosen approach; no preference-only cleanup.
- **Design:** a fundamentally simpler verified approach, reuse over reinvention, and changed boundary quality.
- **Documentation:** user/developer docs, examples, comments, and instruction freshness.
- **Security:** concrete exploitability, trust-boundary, permissions, or secret issues.
- **Performance:** concrete hot-path, resource, or scalability regressions.
- **Release:** CI, deployment, migration, packaging, dependency, infrastructure, versioning, and rollout risk.

Skip specialists with no relevant files or behaviour. Record material skips in shared context.

# Agent Finding Format

Require each specialist to return empty `<findings></findings>` or:

```xml
<findings>
  <finding>
    <severity>blocking|warning|suggestion</severity>
    <category>bug|security|spec|quality|design|docs|release|performance|tests</category>
    <confidence>high|medium</confidence>
    <file>repository-relative path</file>
    <line>changed line or range</line>
    <title>short specific title</title>
    <problem>verified issue in this change</problem>
    <impact>concrete consequence</impact>
    <fix>minimal correction direction</fix>
  </finding>
</findings>
```

Findings must tie to a changed line or directly changed behaviour, except a required missing file, test, migration, or configuration caused by the change.

# Coordinator Verification

For every candidate finding:

1. Deduplicate equivalent findings.
2. Read the patch and minimum surrounding source needed to verify the claim.
3. Confirm referenced symbols, behaviour, requirements, and repository rules.
4. Reclassify severity and category when needed.
5. Remove speculative, convention-only, unrelated, pre-existing, already-handled, or low-confidence findings.
6. Remove design or intent claims unsupported by the provided brief, spec, repository rules, or inspected code.
7. Keep only issues the author would likely correct if shown the evidence.
8. Prefer tests, broken contracts, changed behaviour, and concrete production or developer impact over theoretical concerns.

# Severity and Verdict

- **Blocking:** Confirmed crash, reproducible bug, data loss or corruption, exploitable security problem, failed deployment, broken required contract, or clear acceptance failure.
- **Warning:** Realistic regression, missing required coverage, concrete maintainability cost, or misleading required documentation.
- **Suggestion:** Optional improvement with clear benefit and low false-positive risk. Emit rarely.

Verdict:

- **Approve:** No confirmed issues or trivial suggestions only.
- **Approve with suggestions:** Suggestions only or one contained warning without production risk.
- **Request changes:** Any blocking issue, multiple warnings forming a risk pattern, or confirmed production, security, data, contract, or release risk.

# Output Format
```markdown
## 🤖 Code Review — {PR title or changes scope description}

**Verdict:** ✅ Approve | 🟡 Approve with suggestions | 🔴 Request changes  
**Tier:** {Quick|Standard|Comprehensive}  
**Agents:** {comma-separated agents run}

### Summary
{2–3 sentences: scope, tier used, and overall verdict. Mention skipped material agents only if helpful.}

### Findings

✅ No confirmed findings. {use only when there are no findings}

{Otherwise list all findings in severity order: blocking first, then warnings, then suggestions. Do not group under severity headings.}

**{severity_emoji} {ID} · {category_emoji} {Category} · {Title}**
`{file}:{line-range}` · **Confidence:** {High|Medium}

{Why this is a real problem in the changed behavior. Include concrete impact.}

**Fix:** {minimal actionable fix direction.}

---
```

ID and severity emoji rules:
- Blocking: `🔴 B{n}`
- Warning: `🟡 W{n}`
- Suggestion: `🔵 S{n}`

Category emojis:
- 🐛 Bug
- 🔐 Security
- 📋 Spec
- 🧹 Quality
- 🏗️ Design
- 📚 Docs
- 🚀 Release
- ⚡ Performance
- 🧪 Tests

# Completion Criteria

- Scope, repository profile, intent, and risk tier are explicit.
- Relevant specialists ran with focused inputs.
- Every final finding was independently verified and deduplicated.
- Findings are tied to changed behaviour and have concrete fix direction.
- Temporary files, raw XML, secrets, and untrusted instructions are absent from the final response.
