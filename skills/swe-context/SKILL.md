---
name: swe-context
description: "Build evidence-based repository and external context for an approved change before technical design. Use for non-trivial, cross-module, unfamiliar, or externally integrated changes."
disable-model-invocation: true
---

<goal>
Create a concise, evidence-based context package that enables later design and
planning without repeating initial repository and external-system discovery.
</goal>

<anti-goals>
- Do not decide architecture or choose an implementation approach.
- Do not create implementation tasks.
- Do not modify product requirements.
- Do not begin implementation.
- Do not collect unrelated repository information.
</anti-goals>

<variables>
WORK_DIR: `.swe-work/`
CHANGE_DIR: infer from user input (e.g. `.swe-work/{{DATE}}-{{SLUG}}/`)
REQUIREMENTS_FILE: infer from user input (e.g. `{CHANGE_DIR}/01-requirements.md`)
CONTEXT_FILE: `{CHANGE_DIR}/02-context.md`
REFERENCES_DIR: `{CHANGE_DIR}/references/`
</variables>

<workflow>

## Steps

```python
def context_workflow():
    read_requirements(REQUIREMENTS_FILE)
    inspect_repository_context()
    if change_depends_on_external_system_or_standard():
        inspect_external_constraints()
    write_context_and_references()
    announce_completion()
```

## Steps Explained

**read_requirements**
- Read `REQUIREMENTS_FILE` to understand the scope of the change.
- Identify modules, integration boundaries, external systems, and quality constraints mentioned.

**inspect_repository_context**
- Read repository README, architecture docs, and relevant source code.
- Identify existing product terminology, module ownership, integration patterns, naming and file conventions, error handling, and test patterns.
- Focus only on areas relevant to the stated requirements.
- Do not infer implementation approaches from incomplete evidence.

**inspect_external_constraints**
- Only when the change involves external APIs, standards, protocols, compliance obligations, or vendor systems.
- Fetch or read relevant documentation: rate limits, authentication schemes, payload schemas, error semantics, versioning policies, and imposed identifiers.
- Do not select implementation architecture or libraries.

**write_context_and_references**

Write two artifacts:

1. `CONTEXT_FILE` — a structured summary and index as per [template](context.template.md). Every non-trivial claim must link to a file in `REFERENCES_DIR`.

- Always include: **Repository index** (key modules and file locations) and **References** (index of all files created in `REFERENCES_DIR`).
- Include other sections only where evidence exists. Omit empty sections entirely.
- Each section that draws on a reference file must link to it inline.
- Keep the file concise, <200 lines, the rest of the evidence is in `REFERENCES_DIR`.

2. `REFERENCES_DIR` — one Markdown file per meaningful evidence topic. Choose file names and topics based on the change. Each file contains concise evidence notes, stable repository paths and symbols, official external links, relevant payload fields, constraints, and short excerpts only when they are necessary to preserve meaning that cannot be captured by reference.

- Name files after the evidence topic. Examples: `sec-edgar-api.md`, `filing-payload-schema.md`, `twitter-api.md`, `<platform>-error-format.md`.
- Omit topics for which no meaningful evidence was found.
- Omit `REFERENCES_DIR` files entirely when all relevant concisely fits in `CONTEXT_FILE`;

**announce_completion**
> "Context written to `{CONTEXT_FILE}` and `{REFERENCES_DIR}`. Let me know what else you'd like me to research."

</workflow>

<constraints>
- Do not reproduce code that is accessible in the repository; link to it instead.
- Do not repeat information already captured in `CHANGE_DIR`.
- Do not make any architectural or implementation decisions.
- Do not modify `REQUIREMENTS_FILE` or any source code.
- Do not create files outside `CHANGE_DIR`.
</constraints>

<checklist>
- [ ] Requirements read and scope understood
- [ ] Repository structure, conventions, and relevant modules identified
- [ ] External constraints captured (if applicable)
- [ ] Context file and references written
- [ ] `CONTEXT_FILE` written with only evidence-backed claims
- [ ] Each `CONTEXT_FILE` section links to supporting files in `REFERENCES_DIR`
- [ ] No implementation decisions or architecture choices made
</checklist>
