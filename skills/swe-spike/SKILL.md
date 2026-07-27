---
name: swe-spike
description: "Research a concrete repository, dependency, external-system, feasibility, or impact question raised directly by the user or discovered during another SWE stage. Use codebase inspection, authoritative sources, live probes, or disposable experiments and preserve reproducible evidence."
---

# Purpose

Answer a concrete engineering question with evidence, using codebase inspection, authoritative sources, live probes, or disposable experiments. Typical questions cover repository impact, undocumented runtime behaviour, an external API or dependency, data shape, performance, or migration feasibility.

A spike may run standalone or as a subroutine inside shaping, architecture, specification, planning, implementation, or review. It returns evidence to the caller; it does not silently change product intent, architecture, or contracts.

# Workspace

Use one directory per coherent investigation:

```text
# When associated with a change
.swe-work/<change>/spikes/YYYY-MM-DD-<slug>/
  spike.md
  artifacts/

# When standalone
.swe-work/spikes/YYYY-MM-DD-<slug>/
  spike.md
  artifacts/
```

Use the change workspace named by the user. Use the standalone location when no change exists or the question applies more broadly.

`spike.md` is the concise answer and evidence index. `artifacts/` contains only material needed to inspect or reproduce the research: probe scripts, captured responses, fixtures, benchmark results, query output, diagrams, or a small disposable prototype. Omit `artifacts/` when the answer is fully supported by repository paths or cited sources.

Use [spike.template.md](spike.template.md) as a starting outline; add or omit headings to fit the investigation.

# Workflow

1. **Frame the research question.** Restate the direct user request or the factual unknown delegated by another stage, and identify what evidence would make the answer credible. Ask only when scope ambiguity would materially change the investigation.
2. **Locate relevant context.** Read repository rules and any caller-provided brief, architecture, build spec, plan, state, or earlier spike that changes the question. A spike may also be entirely standalone.
3. **Choose the investigation method.** Use the smallest credible combination of repository analysis, authoritative documentation, live observation, or experiment. Define practical stop conditions internally; do not force them into the report unless they explain a limitation.
4. **Investigate broadly enough.** Follow references and behaviour across the repository or external system to the depth required by the question. Do not restrict an impact question to the first textual match.
5. **Create reproducible artifacts.** When actual behaviour matters, write and run a minimal probe or experiment, save the script and relevant raw output, and record how it was executed.
6. **Evaluate the evidence.** Distinguish observed facts from interpretation. Compare observed behaviour with the user's requirements or the relevant SWE artifact when applicable.
7. **Write the report.** Put the direct answer first, then findings, implications, artifact links, reproduction commands, and material limitations, keeping observed facts distinguishable from interpretation. Keep raw detail in `artifacts/` rather than copying it into Markdown.
8. **Return the finding.** Answer the requester or calling stage and identify assumptions affected by the evidence.

# Investigation Modes

Load only the guidance that matches the question:

- For repository-wide dependency, removal, compatibility, or blast-radius questions, read [references/repository-impact.md](references/repository-impact.md).
- For questions about what an external API, protocol, dependency, or data source actually returns or does, read [references/external-probe.md](references/external-probe.md).
- For other empirical questions, use a minimal disposable script, benchmark, fixture, or prototype when source and documentation cannot provide the answer. Keep it outside production source directories and do not merge it into production unchanged.

Combine modes when necessary, such as probing an external payload and then tracing whether the repository can represent it.

# Artifact Rules

- Scripts must be runnable and include their command, dependencies, inputs, and required environment variables.
- Never store tokens, cookies, credentials, personal data, or sensitive headers. Read secrets from the environment and redact captured output.
- Preserve raw responses when safe and reasonably sized. For large or sensitive output, save a minimal representative fixture and state exactly what was omitted or redacted.
- Use stable, descriptive filenames such as `probe-sec-companyfacts.py`, `aapl-companyfacts.json`, or `dto-field-references.md`.
- Link every retained artifact from `spike.md` and explain what it proves.
- Do not retain generated noise, dependency directories, binaries, or results that do not support the answer.
- Record repository revision, source version, access date, or environment when findings can become stale.

# Constraints

- Do not require every spike to be a decision blocker or to have predefined outcome branches.
- Do not force fixed report sections such as hypotheses, outcome mapping, evidence standard, or result classification when they do not help the research.
- Do not create one spike per search query or minor subquestion; group related investigation under one coherent question.
- Do not modify production code except when the user explicitly changes the task from research to implementation.
- Do not update other SWE artifacts unless that is explicitly part of the request.

# Output

Write `spike.md` and any material artifacts. Final response:

- direct answer in one or two sentences;
- spike directory;
- artifacts created and how to reproduce them;
- important limitations;
- affected SWE artifacts or code decisions.

Before returning, confirm the answer addresses the request at the depth it requires.
