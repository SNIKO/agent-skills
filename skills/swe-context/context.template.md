# <Change title> — Context

Status: Current  
Collected: YYYY-MM-DD  
Repository revision: <git SHA if available>
External sources verified: YYYY-MM-DD  

## Repository index

| Area | Relevant symbols | Why relevant |
|---|---|---|
| `src/...` | `SomeService`, `SomeContract` | ... |

## Confirmed constraints

- ...
- ...

## Existing contracts and conventions

- ...
  See [`references/<topic>.md`](references/<topic>.md).

<!-- 
- Record repository-relative paths, relevant symbols, the observed convention, and why it matters. Include a short code excerpt only when a precise detail would otherwise be lost.
- For internal code, prefer:
Path: src/integrations/sec/SecClient.ts
Symbols: SecClient, SecApiError
Why relevant: Existing retry/error mapping pattern
Observed behaviour: Retries 429 and 5xx, maps failures to IntegrationError-->

## External system: <name>

- Relevant capability:
- Constraints:
- Authentication:
- Versioning:
- Rate limits:
- Error semantics:

See [`references/<topic>.md`](references/<topic>.md).

<!--
- Fetch and record the relevant endpoints, request/response schemas, authentication headers, rate limits, and any SDK or client examples. Include a link to the full API reference for the designer to explore further.
- Prefer official vendor documentation, published schemas, RFCs, standards, and
  source repositories maintained by the dependency owner.
- Treat blog posts, Stack Overflow, issues, and generated summaries as secondary
  evidence; label them as such.
- Record the access date and relevant version or endpoint version for every
  external source. -->

## Design questions

<!-- Questions that design must answer, based on trade-offs or gaps in evidence. -->

- ...
- ...

## Assumptions and inferences

- **Assumption:** ...
- **Inference:** ...

## Open unknowns

- ...

## References

- [`references/<topic>.md`](references/<topic>.md) — why it matters