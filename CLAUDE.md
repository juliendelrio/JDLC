# JDLC — instructions for agents

JDLC is a public agentic SDLC, designed to be installed into other projects. Its design is documented for teaching purposes.

JDLC targets **Claude Code only** for now (ADR-0006): use its native features (plugins, marketplace, skills, subagents, hooks) freely; do not spend effort on portability to other agentic tools.

## Language

- Design documentation (`README.md`, `docs/`, `CREDITS.md`): **French**.
- Code, comments, commits, branch names, and agent-facing artefacts meant to be installed in target projects: **English**.
- File names: ASCII, no accents.

## Decisions

- Any structural decision gets an ADR in `docs/adr/` (use `docs/adr/template.md`, next free number, update the index in `docs/adr/README.md`).
- Never rewrite an accepted ADR: create a new one that supersedes it. A dated "Précisions" section may be appended when the author clarifies a decision without reversing it.
- Significant work sessions get a journal entry in `docs/journal/`.
- JDLC has no version yet: 0.1.0 will be the first implementation (ADR-0011). Accepted ADRs are hypotheses until verified by real use (1.0).

## Design rules

- JDLC is designed for teams and usable solo (ADR-0007). For every artefact, state, setting or memory, say whether it belongs to the **instance** (one working environment; user and instance are one level) or to the **project** (shared by all instances). Do not dismiss an idea "because solo".
- The work unit is a generic ticket whose storage the project owner chooses (ADR-0008). The spec has two levels: main spec and ticket spec (ADR-0009).
- JDLC covers the whole production cycle, not only the ticket: human gates and agent roles are defined in ADR-0017. When describing the cycle, say "a human" or "an agent" (never address the reader), and state for every role whether it is held by a human or by an agent.
- While the frame is being built (framing, architecture, quality requirements), JDLC guides and advises the human and does not block. Once the frame is validated, JDLC guarantees its rules during the automated and implementation steps, and may block there (ADR-0018).
- Project memory and rules change only with the human's validation: propose, do not apply (ADR-0013). User-specific learnings stay in Claude Code's own memory (ADR-0010).
- Personal working preferences (such as "commit only when asked") are instance settings, not JDLC rules.

## Verification

- Apply JDLC's own rule: the one who writes is not the one who verifies. When a note or synthesis was produced by a subagent, spot-check its claims against the source and say what was checked and what was not.
- Before treating an agreement between sources as a convergence, ask what context each source was written for (solo/local demos share the same bias).

## Inspirations and licences

Follow `docs/licences.md` strictly:

- Every reused idea, adaptation, or import is recorded in `CREDITS.md`.
- Before importing any third-party content, check its licence **at the exact commit/tag and for the exact folder** being imported — never from memory, never from GitHub's licence label (repos often mix licences per folder).
- JDLC is MIT-licensed (ADR-0004): only import content whose licence is compatible per the matrix in `docs/licences.md`.
- Imported/adapted files carry the provenance header defined in `docs/licences.md`; licence texts go in `licenses/`.
- When in doubt, do not import: rewrite from the idea only.
