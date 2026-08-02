# Artifact Review

# Objective

Independently determine whether one SWE artifact is ready for the next human-controlled step or, for execution state, factually safe to resume from. Find decision-changing omissions, contradictions, unsupported claims, infeasible assumptions, unnecessary prescription, duplicated ceremony, and inaccurate progress.

# Scope

Review exactly one primary artifact. Read only:

- its authoritative upstream artifacts;
- relevant spike reports and the saved artifacts needed to verify their claims;
- repository guidance and source needed to verify material claims;
- existing architecture or contracts directly affected by the proposal.

Do not use conversational rationale as a substitute for written evidence. Do not review downstream artifacts unless the user explicitly requests consistency review across them.

# Workflow

1. Identify artifact type from its content or filename.
2. Read the primary artifact and its declared upstream dependencies.
3. Inspect the smallest repository and external evidence needed to verify important feasibility or contract claims.
4. Apply the matching rubric below.
5. Challenge the artifact with these cross-cutting questions:
   - What assumption could invalidate the recommendation?
   - Is there a materially smaller or simpler coherent option?
   - Does this artifact decide something owned by another stage?
   - Does it repeat upstream information instead of linking it?
   - Would a reviewer know exactly which unresolved issue blocks the next step?
6. Verify every candidate finding, deduplicate overlaps, and remove preferences without concrete impact.
7. Assign a verdict using the rubric and return the required format.

# Artifact Rubrics

## Brief

Consider it ready for the next step when:

- the problem and desired outcome are distinct from the proposed solution;
- in-scope and out-of-scope behaviour are coherent;
- acceptance criteria are observable and do not prescribe architecture;
- repository fit, external feasibility, complexity, risk, and main uncertainty are evidence-backed enough for the decision;
- the delivery depth is proportional to risk and uncertainty;
- assumptions that block the next step are explicit.

Flag as blocking when the brief could authorize the wrong product behaviour, relies on an unsupported feasibility claim, or cannot distinguish completion from partial success.

## Spike

Consider it complete when:

- it directly answers the user's repository, external-system, feasibility, behaviour, or impact question;
- the investigation is broad enough for the claim, including producer and consumer paths for repository-wide impact questions;
- important repository claims cite paths and symbols;
- live or experimental claims link sanitized, reproducible scripts and outputs when those artifacts are needed;
- external claims identify authoritative sources, observation date, representative inputs, and material limitations;
- facts, interpretations, limitations, and implications are distinguishable;
- the report is concise while its artifact directory preserves useful detail.

Flag shallow text search presented as repository-wide safety, documentation presented as observed runtime behaviour, missing probe artifacts, unreproducible experiments, leaked secrets, unsupported certainty, and a rigid report that obscures the answer.

## Architecture

Consider it ready for low-level specification when:

- design drivers trace to the brief or evidence;
- components have coherent responsibilities, ownership, and reasons to change;
- dependency direction, orchestration, mutation, state, retries, idempotency, and failure ownership are clear where relevant;
- normal and architecturally distinct exceptional flows are feasible in the repository;
- the design was challenged against a credible simpler alternative;
- conceptual data ownership is sufficient without leaking into exact schema or implementation;
- no product requirement or low-level contract is silently invented.

Flag circular or impossible dependencies, ownerless invariants, coupled responsibilities, missing failure transitions, and premature exact DTO/schema/file design.

## Build spec

Consider it ready for implementation or planning when:

- exact shared, persistent, externally visible, security-sensitive, and operational contracts are complete and internally consistent;
- existing paths, symbols, commands, and conventions are verified;
- API, module, event, schema, migration, compatibility, configuration, and failure details are exact where applicable;
- private implementation remains flexible unless an invariant requires precision;
- acceptance criteria and critical invariants map to deterministic verification;
- no missing decision prevents implementation;
- the spec does not repeat product background or architecture narrative.

Flag invented repository details, mismatched names or types across seams, unsafe migration states, vague failures, unverifiable acceptance, and implementation-by-prose.

## Delivery plan

Consider it executable when:

- a separate plan is justified;
- slices deliver complete behaviour or independently useful risk reduction;
- dependencies are explicit and intermediate states remain valid;
- early slices reduce important uncertainty or deliver thin end-to-end value;
- each slice references authoritative spec sections and has deterministic verification;
- contracts and coding instructions are not duplicated from the build spec;
- the plan is small enough to execute and review.

Flag horizontal placeholder layers, unsafe sequencing, future-dependent incomplete tasks, duplicated low-level design, and plans that should be omitted or split.

## Execution state

Consider it safe to resume from when:

- completed, current, blocked, and remaining work match the repository and plan;
- verification commands and results are factual and scoped correctly;
- failed or skipped checks are not represented as passing;
- specification-impacting discoveries identify the earliest affected artifact;
- notes contain only context needed to resume rather than copied specification.

Flag false completion, stale dependencies, hidden divergence, unverifiable test claims, and state that would send the next session down the wrong path.

# Finding Threshold

- **Blocking:** The artifact cannot safely advance because of a contradiction, unsupported key assumption, infeasible design, missing shared contract, unsafe sequencing, or inability to verify required behaviour.
- **Warning:** A concrete gap should be corrected before advancing but does not invalidate the core direction by itself.
- **Suggestion:** Optional improvement with clear review-value. Emit rarely.

Do not report:

- wording or formatting preferences;
- missing sections that do not apply;
- implementation details deliberately owned by a later stage;
- concerns already resolved by linked authoritative evidence or a credible spike;
- speculative scale, security, or compatibility requirements not established by context.

# Assessment

- **Ready:** No confirmed findings or suggestions only.
- **Revise:** One or more warnings or blocking issues can be corrected in this artifact.
- **Return upstream:** The artifact depends on a missing product decision, evidence, or change to an earlier artifact.

# Output Format

```markdown
## Artifact Review — `<path>`

**Type:** Brief | Spike | Architecture | Build spec | Delivery plan | Execution state
**Assessment:** Ready | Revise | Return upstream

### Summary
<Two or three sentences describing readiness and the most important reason.>

### Findings

No confirmed findings.

<!-- Otherwise list in severity order. -->

**B1 · <Short title>**
`<path>#<section>`

**Problem:** <Concrete issue and evidence.>

**Impact:** <Why advancing with it is unsafe or costly.>

**Correction:** <Smallest useful change or upstream decision needed.>

### Evidence gaps

None.

<!-- Include only gaps that prevented verification or a safe next step. -->
```

Use `B<n>`, `W<n>`, and `S<n>` for blocking, warning, and suggestion findings.

# Completion Criteria

- The assessment follows the artifact-specific rubric.
- Each finding belongs to the reviewed stage and has concrete evidence.
- Upstream and downstream ownership are not confused.
- Duplicated or speculative findings have been removed.
- The review is short enough to act on in one revision pass.
