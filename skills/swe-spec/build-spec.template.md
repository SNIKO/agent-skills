# Build Spec: <Change>

**Based on:** [`brief.md`](brief.md)<!-- and [`architecture.md`](architecture.md) when present -->

## Repository delta

<!-- Exact for contracts, wiring, migrations, configuration, and stable module locations. Mark private locations as expected when intentionally flexible. -->

```diff
src/
  alerting/
+   domain/                 # responsibility
+   application/            # responsibility
    api/                    # what is being changed
- legacy-notifications/     # removal reason
```
-->

## Module contracts

<!-- Omit when no shared module contract changes. Use the repository language and exact signatures. -->

```text
<exact contract>
```

- **Owner:** ...
- **Input semantics:** ...
- **Output semantics:** ...
- **Errors:** ...
- **Compatibility:** ...

## API contracts

<!-- Omit when no API changes. Define exact routes, methods, DTOs, validation, statuses, and errors. -->

### `<METHOD> <route>`

**Request**

```json
{}
```

**Response**

```json
{}
```

| Condition | Status | Response |
|---|---:|---|
| ... | ... | ... |

## Events and messages

<!-- Omit when not applicable. Define exact schema, producer, consumer, delivery, ordering, and compatibility semantics. -->

## Persistence and migrations

<!-- Omit when no persistence changes. Use exact schema syntax where possible. -->

```sql
-- exact tables, columns, constraints, and indexes
```

- **Migration:** ...
- **Rollback or forward recovery:** ...
- **Transaction boundary:** ...
- **Idempotency/uniqueness:** ...
- **Compatibility window:** ...

## State and failure behaviour

| State, event, or failure | Required behaviour | Owner |
|---|---|---|
| ... | ... | ... |

## External integration

<!-- Omit when not applicable. -->

- **Requests and authentication:** ...
- **Timeout and retry policy:** ...
- **Rate limiting:** ...
- **Payload mapping:** ...
- **Error mapping:** ...

## Configuration, security, and observability

<!-- Include only applicable subsections. -->

| Key, permission, metric, or signal | Exact semantics | Owner |
|---|---|---|
| ... | ... | ... |

## Rollout and compatibility

<!-- Feature flags, deployment ordering, backfill, dual-read/write, deprecation, or operational checks. Omit when not applicable. -->

- ...

## Verification contract

| Acceptance criterion or invariant | Verification boundary | Command or observable check |
|---|---|---|
| AC-1 | Integration | `...` |

