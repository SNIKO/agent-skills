# <title> — Design

## Summary

- **Selected approach**: ...
- **Why**: ...
- **Main tradeoff**: ...

## Requirements coverage

| Requirement | Design element |
|---|---|
| FR-01 | ... |

## Key decisions

| Decision | Choice | Reason |
|---|---|---|
| ... | ... | ... |

## 1. Repository structure

<!--
Show only what changes. Use markers:
  +  added
  ~  modified
  -  removed

Example:
src/
  auth/
    + token-store.ts       # new module
    ~ session.ts           # extended with refresh logic
  - legacy-auth.ts         # removed
-->

```
<paste tree here>
```

## 2. Components

<!--
One entry per component involved in this change.
"Data owned" = state, storage, or source-of-truth the component controls exclusively.
-->

### `<name>`
- **Responsibility**: ...
- **Data owned**: ...

## 3. Data flow

<!--
Describe how data moves between components for the primary scenario(s).
Use a numbered sequence or a simple arrow notation: A -> B: <what/why>
Include the trigger (user action, event, cron, etc.) and the terminal outcome.
-->

1. ...
2. ...

## 4. Contracts and boundaries

<!--
One entry per cross-component boundary.
Specify the interface type (function call, HTTP, event, queue message, shared DB table, etc.),
the payload / schema, and any guarantees (idempotency, ordering, error behaviour).
Omit internal implementation details.
-->

### `<ComponentA>` → `<ComponentB>`

- **Interface**: ...
- **Payload**: ...
- **Guarantees**: ...

---

## Risks, assumptions, and open questions

- **Risk**: ... — **Mitigation**: ...
- **Assumption**: ...
- **Open question**: ...
