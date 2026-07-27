# Repository Impact Investigation

Use this mode for questions such as:

- Can a DTO field be removed, and what will break?
- Is this interface still used?
- What depends on this event or database column?
- Can this module be moved or deleted safely?

# Investigation

1. Locate the declaration and all generated or versioned forms.
2. Trace producers: constructors, mappers, deserializers, database reads, API clients, fixtures, and generated code.
3. Trace consumers: property reads, serializers, validators, handlers, templates, events, persistence, external exports, and downstream packages.
4. Check compatibility surfaces: public APIs, stored data, migrations, messages, SDKs, documentation, examples, and deployment skew.
5. Inspect tests for intended behaviour and blind spots; do not equate missing tests with no usage.
6. Use semantic references, type checking, builds, targeted tests, or generated-schema checks when available.
7. Classify findings:
   - definite compile, test, runtime, data, or contract breakage;
   - compatibility risk requiring consumer or deployment evidence;
   - safe internal cleanup;
   - apparently unused, with the evidence limits stated.

Inspect the whole repository when the contract may cross modules. Follow re-exports, aliases, reflection, string-key access, serialization names, and generated code that plain text search can miss.

# Evidence

The report should cite affected paths and symbols and explain how each depends on the target. Save an artifact such as `artifacts/dto-field-impact.md` only when a separate detailed reference map materially improves review; do not preserve raw search dumps by default.

# Completion Check

Before claiming a removal is safe, account for declaration, producers, consumers, serialized or persisted forms, tests, documentation, and external compatibility. State what cannot be proven from the repository alone.
