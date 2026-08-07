# Delivery Plan: <Change>

**Based on:** [`spec.html`](spec.html)

## Delivery strategy

<!-- One short paragraph: why a plan is needed, the ordering principle, and the first risk or capability validated. -->

## Slices

### Slice 1 — <Observable outcome>

**Outcome:** <What becomes usable, safe, or independently verifiable.>

**Depends on:** None | Slice N

**Spec references:**

<!-- Section ids from spec.html, so the implementer loads only what this slice needs. -->

- `spec.html#<section-id>`
- `brief.md` AC-...

**Expected areas:**

<!-- Stable module or test areas, not an exhaustive private file prescription. -->

- `src/...`
- `tests/...`

**Verification:**

- **Command:** `...`
- **Observable result:** ...
- **Required failure/compatibility behaviour:** ...

**Operational considerations:**

<!-- Include only when this slice has migration, rollout, rollback, backfill, or monitoring implications. Otherwise omit. -->

- ...

### Slice 2 — <Observable outcome>

...

## Dependency summary

<!-- Use a compact list or Mermaid graph only when dependencies are not obvious from slice order. Omit otherwise. -->

- Slice 2 depends on Slice 1 because ...
