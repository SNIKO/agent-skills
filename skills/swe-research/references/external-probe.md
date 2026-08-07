# External-System Probe

Use this mode when documentation alone is insufficient and the question asks what an API, protocol, dependency, data source, or service actually returns or does.

# Investigation

1. Read the relevant official documentation, schema, version policy, and usage constraints.
2. Select representative inputs that exercise the required behaviour. Use multiple cases when one response could be misleading.
3. Write a minimal reproducible probe under `artifacts/` before making the request.
4. Use environment variables for credentials and configurable inputs. Never embed secrets.
5. Run the probe and save:
   - the response body;
   - relevant response headers for pagination, caching, versioning, rate limits, or content type;
   - request metadata without credentials;
   - the command, access date, runtime/dependency version, and representative input.
6. Compare observed data with the actual requirement: fields, identifiers, semantics, units, history, pagination, amendment behaviour, omissions, errors, and edge cases.
7. Explain differences between documented and observed behaviour.

# Safety

A request to probe a public read-only endpoint authorizes that bounded request under the skill's autonomy policy. Follow provider identification, rate-limit, acceptable-use, and data-retention requirements.

# Suggested Artifact Names

```text
artifacts/
  probe-<system>-<operation>.<ext>
  <representative-input>-response.json
  <representative-input>-headers.txt
  README.md                    # only when reproduction needs extra setup
```

# Completion Check

The saved script must reproduce the observation, and the report must answer whether the observed behaviour fits the stated requirement, under which conditions, and with which untested cases or limitations.
