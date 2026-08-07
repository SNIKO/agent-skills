---
subject: <Human-readable subject, e.g. SEC EDGAR companyfacts API>
updated: YYYY-MM-DD
summary: <One line for the index — what this subject covers, not what was concluded.>
verified-against: <repo SHA, API/dependency version, environment, access date>
---

# <Subject>

## What this covers

<!-- One to three sentences bounding the subject, so the next agent can tell whether their question belongs here or in a new report. -->

## Current understanding

<!-- The standing answer. Rewritten in place on every update; must always be true today. This is what a reader gets if they read nothing else. Cite paths, symbols, or sources for anything material. -->

## Findings

<!-- Appended, newest first. Never overwrite an entry: mark it superseded and link the replacement. -->

### YYYY-MM-DD — <Question investigated>

**Answer:** <Direct answer first.>

**Evidence:** <Paths and symbols, observed payload fields, source links, short tables. Keep observed facts separable from interpretation.>

**Artifacts:** <Omit when none.>

- [`artifacts/<file>`](artifacts/<file>) — <what it proves>
  - Reproduce: `...`

**Limitations:** <Untested cases, stale-data risk, redactions, environmental differences. Omit when none.>

### YYYY-MM-DD — <Earlier question> · Superseded by [YYYY-MM-DD](#yyyy-mm-dd--question-investigated)

<!-- Kept for history. State briefly what changed and why the earlier conclusion no longer holds. -->

## Related subjects

<!-- Omit when none. Link sibling reports, especially after a split. -->

- [`../<other-subject>/report.md`](../<other-subject>/report.md) — ...
