---
name: swe-research
description: "Answer a concrete repository, dependency, external-system, feasibility, behaviour, or impact question and preserve the evidence for reuse. Use when the answer is worth keeping — codebase inspection, authoritative sources, live probes, or disposable experiments."
---

# Pipeline

`swe-shape` → `swe-spec` → [`swe-plan`] → [`swe-worktree`] → `swe-execute` → `swe-review`. This skill is not one of those stages: it runs at any point, standalone or delegated by any of them, often several times between two stages.

- **This stage:** answer one concrete factual question and preserve the evidence.
- **Reads:** the repository, external sources, the existing corpus, and any artifact the caller supplies.
- **Writes:** a subject report in the shared corpus, plus artifacts.
- **Next:** return the answer to whoever asked. The caller decides what to do with it.

# Purpose

Answer a concrete engineering question with evidence, and add that evidence to a shared, long-lived research corpus so later changes reuse it instead of rediscovering it.

Research returns evidence to the caller; it does not change product intent, design, or contracts.

**Invoke when the answer is worth preserving.** Ordinary reading — "what does this function do", "where is this defined" — is not research and needs no artifact. Research is for questions whose answer another change, or another agent, would otherwise have to rediscover: how an external API actually behaves, what the blast radius of a change is, whether a dependency supports something, why a subsystem works the way it does.

# Corpus

One shared corpus per repository, organized by **subject**, not by question:

```text
.swe/research/
  INDEX.md                      # generated — never edit by hand
  <subject-slug>/
    report.md
    artifacts/
```

There is no per-change research directory. Findings about an API, a subsystem, or a dependency outlive the change that prompted them, and changes in the same repository overwhelmingly ask about the same domain and the same external services. A change references the reports it relied on from its own `brief.md` and `spec.html`.

A subject is a thing you can keep learning about: `sec-edgar-api`, `tenant-resolution-middleware`, `legacy-dto-usage`, `postgres-jsonb-indexing`. It is not a single question. `report.md` holds a standing summary plus dated findings; `artifacts/` holds only what is needed to inspect or reproduce the evidence — probe scripts, captured responses, fixtures, benchmark output, query results. Omit `artifacts/` when repository paths and cited sources fully support the answer.

Use [report.template.md](report.template.md).

# Workflow

1. **Frame the question.** Restate the direct request or the unknown delegated by another stage, and identify what evidence would make the answer credible. Ask only when scope ambiguity would materially change the investigation.
2. **Search the corpus first.** Read `INDEX.md` and open any report whose subject plausibly covers the question. Then decide:
   - **Reuse** — an existing finding already answers it and is still valid. Return that answer and its report; do not re-investigate or create anything.
   - **Extend** — the subject exists but this question is new, or the standing answer needs re-verification. Add a dated finding to that report.
   - **Create** — no existing subject covers it. Start a new subject directory.

   Prefer extending. Two reports on the same subject is the failure mode that makes a corpus useless.
3. **Locate context.** Read repository rules, plus any change artifact the caller supplies — typically `brief.md` (approved problem and acceptance criteria) or `spec.html` (the design and contracts) — when it changes what the question means. A standalone investigation has neither.
4. **Choose the method.** Use the smallest credible combination of repository analysis, authoritative documentation, live observation, or experiment.
5. **Investigate broadly enough.** Follow references and behaviour across the repository or external system to the depth the question requires. Do not restrict an impact question to the first textual match.
6. **Create reproducible artifacts.** When actual behaviour matters, write and run a minimal probe or experiment, save the script and relevant raw output, and record how it was executed.
7. **Evaluate the evidence.** Distinguish observed facts from interpretation. Note where observed behaviour contradicts an existing finding, a brief, or a spec.
8. **Write the report.** Apply `Report Rules`. Update front matter, rewrite `Current understanding`, append the dated finding.
9. **Regenerate the index.** Rewrite `INDEX.md` from the front matter of every report directory.
10. **Return the finding.** Answer the requester, link the report, and name the artifacts or assumptions the evidence affects.

# Report Rules

- **`Current understanding` is rewritten in place.** It is the standing answer a reader gets if they read nothing else, and it must always be true today.
- **Findings are appended, never overwritten.** When new evidence contradicts an earlier finding, keep the old entry and mark it superseded with a link to the replacement. The history of what was believed and why is part of the evidence.
- **Front matter carries what the index needs** and what tells a reader whether the answer is stale: `subject`, `updated`, `summary`, and `verified-against` (repository revision, API or dependency version, access date, environment).
- Keep raw detail in `artifacts/` rather than pasting it into Markdown.
- Split a subject when it grows a coherent sub-subject worth finding on its own, and leave a link in both directions.
- Do not force fixed sections — hypotheses, outcome branches, result classification — when they do not help the research.
- Do not create a report per search query or minor subquestion.

# Artifact Rules

- Scripts must be runnable and record their command, dependencies, inputs, and required environment variables.
- Never store tokens, cookies, credentials, personal data, or sensitive headers. Read secrets from the environment and redact captured output. These rules apply to every retained artifact, in every investigation mode.
- Preserve raw responses when safe and reasonably sized. For large or sensitive output, save a minimal representative fixture and state exactly what was omitted.
- Use stable, descriptive filenames such as `probe-companyfacts.py`, `aapl-companyfacts.json`, or `dto-field-references.md`.
- Link every retained artifact from `report.md` and explain what it proves.
- Do not retain generated noise, dependency directories, binaries, or output that does not support an answer.

# Investigation Modes

Load only the guidance that matches the question:

- Repository-wide dependency, removal, compatibility, or blast-radius questions — [references/repository-impact.md](references/repository-impact.md).
- What an external API, protocol, dependency, or data source actually returns or does — [references/external-probe.md](references/external-probe.md).
- Other empirical questions — use a minimal disposable script, benchmark, fixture, or prototype when source and documentation cannot answer. Keep it outside production source directories and never merge it into production unchanged.

Combine modes when necessary, such as probing an external payload and then tracing whether the repository can represent it.

# Index

`INDEX.md` is **generated** from report front matter, so parallel changes never conflict on it and it cannot drift from the reports. Regenerate it in full on every write:

```markdown
<!-- Generated by swe-research. Do not edit; regenerated from report front matter. -->
# Research Index

| Subject | Covers | Updated | Verified against |
|---|---|---|---|
| [sec-edgar-api](sec-edgar-api/report.md) | Payload shape, units, fiscal frames, rate limits | 2026-02-16 | API v3, accessed 2026-02-16 |
| [legacy-dto-usage](legacy-dto-usage/report.md) | Call sites binding `LegacyDto` and which are public | 2026-02-14 | repo `1a2b3c4` |
```

The index is the discovery surface for this skill. Other stages do not scan it — they follow the direct report links recorded in `brief.md` and `spec.html`.

# Autonomy

Read the repository, run read-only inspection, and run local or public read-only probes, benchmarks, and disposable experiments without asking. Write only inside `.swe/research/`.

Ask first when an investigation is authenticated, paid, mutating, bulk, rate-sensitive, or otherwise consequential.

Never modify production code, and never update another SWE artifact unless the user explicitly asks — recording a finding is not adopting it.

# Constraints

- Do not hand-edit `INDEX.md`.
- Do not require every investigation to be a decision blocker.

# Output

Final response:

- direct answer in one or two sentences;
- report path, and whether it was reused, extended, or created;
- artifacts created and how to reproduce them;
- material limitations;
- SWE artifacts or code decisions the finding affects, and whether any of them now contradicts it.

Before returning, confirm the answer addresses the request at the depth it requires.
