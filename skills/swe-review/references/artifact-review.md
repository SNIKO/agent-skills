# Artifact Review

# Objective

Independently determine whether one SWE artifact is ready for the next human-controlled step or, for execution state, factually safe to resume from. Find decision-changing omissions, contradictions, unsupported claims, infeasible assumptions, unnecessary prescription, duplicated ceremony, and inaccurate progress.

# Scope

Review exactly one primary artifact. Read only:

- its authoritative upstream artifacts;
- the research reports it links, and the saved artifacts needed to verify their claims;
- repository guidance and source needed to verify material claims;
- existing structure or contracts directly affected by the proposal.

For `spec.html`, use its table of contents to scope the review to the sections in question when the user names an area; otherwise review the whole document.

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
- excluded behaviour is coherent and the acceptance criteria are the only statement of required behaviour;
- acceptance criteria are observable and do not prescribe a solution;
- repository fit, complexity, risk, and main uncertainty are evidence-backed enough for the decision;
- the delivery depth is proportional to risk and uncertainty;
- assumptions that block the next step are explicit;
- it fits on one screen.

Apply the boundary test: every statement must still be true if the codebase were rewritten in another language. Flag solution choices, mechanisms, and contracts that have leaked in — especially through Non-negotiables.

Flag as blocking when the brief could authorize the wrong product behaviour, relies on an unsupported feasibility claim, or cannot distinguish completion from partial success. Do not flag a missing in-scope list or a missing required-behaviour section; those are deliberately absent.

## Research report

Consider it complete when:

- it directly answers the repository, external-system, feasibility, behaviour, or impact question;
- the investigation is broad enough for the claim, including producer and consumer paths for repository-wide impact questions;
- important repository claims cite paths and symbols;
- live or experimental claims link sanitized, reproducible scripts and outputs when those artifacts are needed;
- external claims identify authoritative sources, observation date, representative inputs, and material limitations;
- facts, interpretations, limitations, and implications are distinguishable;
- `Current understanding` is true today and consistent with the newest finding;
- superseded findings are marked rather than deleted, and front matter records `updated` and `verified-against`;
- the subject boundary is coherent — the report is not two unrelated subjects sharing a directory.

Flag shallow text search presented as repository-wide safety, documentation presented as observed runtime behaviour, missing probe artifacts, unreproducible experiments, leaked secrets, unsupported certainty, a stale standing summary contradicted by a later finding, and duplication of a subject that already exists in the corpus.

## Spec

`spec.html` spans high-level structure and exact contracts in one document, and different sections legitimately sit at different depths. Review each section **at the depth it claims**, not against a fixed template. A shallow section is not a defect; a shallow section presented as implementable is.

Consider it ready for the next step when:

- it reads top-down — overall idea and how the pieces fit before any one part in detail — and is grouped by domain area;
- structural decisions trace to the brief or to evidence, and no product requirement is silently invented;
- components have coherent responsibilities, ownership, and reasons to change; dependency direction, mutation, state, retries, idempotency, and failure ownership are clear where relevant;
- the approach was challenged against a credible simpler alternative;
- every section that claims exact-contract depth is complete and internally consistent: API, module, event, schema, migration, compatibility, configuration, and failure details are exact, and existing paths, symbols, commands, and conventions are verified;
- private implementation remains flexible unless an invariant requires precision;
- acceptance criteria and critical invariants map to deterministic verification in the sections that are contract-complete;
- sections do not contradict each other across depths;
- section ids are stable, the table of contents matches the sections, and local links resolve.

Flag circular or impossible dependencies, ownerless invariants, coupled responsibilities, missing failure transitions, invented repository details, mismatched names or types across seams, unsafe migration states, vague failure semantics, implementation-by-prose, and private methods or algorithms specified without a correctness, security, or contract reason.

Flag as blocking any place where a deeper section silently reverses a shallower one: that is a decision the human never made.

## Delivery plan

Consider it executable when:

- a separate plan is justified;
- slices deliver complete behaviour or independently useful risk reduction;
- dependencies are explicit and intermediate states remain valid;
- early slices reduce important uncertainty or deliver thin end-to-end value;
- each slice references authoritative `spec.html` section ids and has deterministic verification;
- contracts and coding instructions are not duplicated from the spec;
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
- concerns already resolved by linked authoritative evidence or a credible research report;
- speculative scale, security, or compatibility requirements not established by context.

# Assessment

- **Ready:** No confirmed findings or suggestions only.
- **Revise:** One or more warnings or blocking issues can be corrected in this artifact.
- **Return upstream:** The artifact depends on a missing product decision, evidence, or change to an earlier artifact.

# Output Format

```markdown
## Artifact Review — `<path>`

**Type:** Brief | Research report | Spec | Delivery plan | Execution state
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
