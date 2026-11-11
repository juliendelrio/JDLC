# JDLC — instructions for agents

JDLC is a public agentic SDLC, designed to be installed into other projects. Its design is documented for teaching purposes.

JDLC targets **Claude Code only** for now (ADR-0006): use its native features (plugins, marketplace, skills, subagents, hooks) freely; do not spend effort on portability to other agentic tools.

## Language

- Design documentation (`README.md`, `docs/`, `CREDITS.md`): **French**.
- Code, comments, commits, branch names, and agent-facing artefacts meant to be installed in target projects: **English**.
- File names: ASCII, no accents.

## Decisions

- Any structural decision gets an ADR in `docs/adr/` (use `docs/adr/template.md`, next free number, update the index in `docs/adr/README.md`).
- Never rewrite an accepted ADR: create a new one that supersedes it.
- Significant work sessions get a journal entry in `docs/journal/`.

## Inspirations and licences

Follow `docs/licences.md` strictly:

- Every reused idea, adaptation, or import is recorded in `CREDITS.md`.
- Before importing any third-party content, check its licence **at the exact commit/tag** being imported — never from memory.
- JDLC is MIT-licensed (ADR-0004): only import content whose licence is compatible per the matrix in `docs/licences.md`.
- Imported/adapted files carry the provenance header defined in `docs/licences.md`; licence texts go in `licenses/`.
- When in doubt, do not import: rewrite from the idea only.
