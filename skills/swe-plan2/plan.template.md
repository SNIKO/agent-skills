# Implementation Plan: <title>

## Change overview
<!-- One paragraph summary of the problem, intended outcome, and scope. -->

<!-- Briefly describe the approach and key changes. Use bullet points if necessary. -->

### Module and File Structure

<!--
Show only what changes. Omit unchanged subtrees

```diff
src/
  alerting/
+   domain/          # new module
+   application/     # new module
    api/             # extended with alert endpoints
- legacy-notifications/
```
-->

### API

<!-- Show only what changes. Omit unchanged endpoints. Omit the whole section if there are no changes.

```diff
+ GET /company/{cik}/facts.json

POST /ingestion/sec-fundamentals-run
{
  "mode": string,
-  "trackedSecurityCount": number,
+  "securityCount": number,                # rename
-  "secEligibleCompanyCount": number,
-  "trackedSecurityCount": number,         # removed because the worker can derive it from the company results
}
```

-->

### DB Schema

<!-- Show only what changes. Omit unchanged tables. Omit the whole section if there are no changes. 

```diff
+ CREATE TABLE users (      -- new users table to support authentication and authorization
+  id SERIAL PRIMARY KEY,
+  name TEXT NOT NULL
+ );
```

```diff
CREATE TABLE companies (
  id SERIAL PRIMARY KEY,
  name TEXT NOT NULL
- domain TEXT NOT NULL
+ industry TEXT NOT NULL  -- changed to industry because the domain is not always known
);
```

-->

## Details

<!-- Detailed explanation of steps to be done, including rationale, design decisions, and any relevant context. Must include mutations required to the codebase implicitly showing any modifications to:
- apis and DTOs
- database schema and migrations
- public interfaces and contracts
- boundaries and integration points
- any internal logic that is important to the high level design 

This section should provide enough detail for the reader to understand the repository changes from its current state to the desired end state. Prefer structured markdown over long prose — use headings, bullet points, and code blocks where appropriate to describe the required mutations.

Use 'diff' code blocks to show the changes to the codebase or db schema.
Use syntax specific code blocks to show new code (e.g., ```sql for SQL, ```typescript for TypeScript, ```python for Python)
Use tables for simple structures like code-description pairs, enums.

-->

 ## Data and Control Flow

Describe a few the most important runtime flows:

1. input enters the system
2. validation occurs
3. ownership transfers between modules
4. data is stored or retrieved
5. output or side effects occur

Include exceptional flows only when architecturally significant.
