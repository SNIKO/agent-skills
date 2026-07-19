# Design: <Title>

## 1. Summary

One or two paragraphs describing:

- what capability is being added
- the proposed architectural approach
- the main areas of the system affected

## 2. Existing Context

Only the repository context relevant to this feature:

- existing modules involved
- current ownership
- current flow
- constraints and patterns that must be preserved

## 3. Proposed Design

### Architecture Overview

A concise description or Mermaid diagram.

### Components and Responsibilities

For each component, explain:
- what it does
- what data or decisions it owns
- how consumers use it
- what it depends on

### Boundary Rules

Explicitly state:

- what each component must not do
- which dependencies are allowed
- where orchestration belongs
- where business rules belong

## 4. Data and Control Flow

Describe the important runtime flows:

1. input enters the system
2. validation occurs
3. ownership transfers between modules
4. data is stored or retrieved
5. output or side effects occur

Include exceptional flows only when architecturally significant.

## 5. Data Model and State

Only meaningful design-level information:

- entities or aggregates
- ownership
- relationships
- important states and transitions
- uniqueness and consistency rules

Do not list every database column unless it affects the design.

## 6. Interfaces and Contracts

Describe contracts that cross boundaries:

- public module interfaces
- APIs
- events
- messages
- database ownership boundaries
- external integrations

Include representative fields and semantics, but not full implementation code.

## 7. Key Decisions

For each non-trivial decision:

### <Decision>

**Choice:** What was selected.

**Reason:** Why it fits the requirements and repository.

**Trade-offs:** What becomes harder or less flexible.

**Alternatives rejected:** Other credible approaches.

## 8. Failure and Edge-Case Behaviour

Cover design-significant cases:

- retries
- duplicates
- partial failure
- invalid state
- dependency unavailable
- concurrency
- backward compatibility

## 9. Verification Strategy

Describe behavioural verification boundaries:

- unit-test boundaries for important rules
- integration paths
- contract tests
- end-to-end scenarios
- failure and concurrency scenarios

Do not enumerate tests for every class or file.

## 10. Module and File Structure

Show only proposed directories and major files:

<!--
Show only what changes. Omit unchanged subtrees. Use markers:
  +  added
  ~  modified
  -  removed

Example:
src/
  alerting/
    + domain/          # new module
    + application/     # new module
    ~ api/             # extended with alert endpoints
  - legacy-notifications/
-->

Explain the responsibility of each major file or directory.

## 11. Open Questions

Only unresolved decisions that materially block or change implementation.