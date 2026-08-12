# `Notes/` — your knowledge

> Every note lives here, flat, one concept per file. This folder *is* your knowledge base; everything else exists to serve it.

This is the heart of the vault. The agents read, search, link, and maintain what's in here — and ground every answer in it.

## What goes here

- **One concept per note.** Each file is a single atomic idea — no concept represented twice ([single source of truth](../.claude/rules/single-source-of-truth.md)).
- **Flat, no sub-folders.** Every note sits directly in `Notes/`. Structure comes from `[[wikilinks]]`, not directories — no folders, indexes, MOCs, tags, or properties unless you ask or a template requires them.
- **Connected, not copied.** Relationships between notes are always `[[wikilinks]]`; never inline a copy of another note's content.

The folder starts with ten example notes, so there is something real to search, question, and maintain from the first minute. They are someone else's, and deleting them is a perfectly good first change to make. Your own notes accumulate here as you capture.

## How notes get here

- **Recorder creates them** at your explicit capture, via the **Creation** action — picking a [template](../Templates/) for the content shape, then writing the note as one [atomic operation](../.claude/rules/atomic-operation.md) (edit → log → commit).
- **The Maintainer reshapes them** over time — splitting (Fission), merging (Convergence), or surfacing a missing parent note (Emergence). See [two assistants, one folder](../README.md#two-assistants-one-folder).

Both follow the [rules](../.claude/rules/) — the discipline that keeps this folder coherent (atomicity, single source of truth, wikilinks) lives there, not here.

## Notes on the folder

- **Versioned in git.** Each note change is one commit, so the full history is auditable.
- **The only corpus carrying the full machinery.** Other folders are bound by single source of truth too, but `Notes/` alone gets the action classification, the `changes.jsonl` ledger, and the Maintainer sweep — see the corpus table in [`CLAUDE.md`](../CLAUDE.md).
- **What gets indexed.** Only the notes in `Notes/` and `Profile/` are indexed for [semantic search](../.claude/skills/rag-search/SKILL.md) — `Sources/`, `Streams/`, `Templates/`, and project meta files are excluded.
- This `README.md` is documentation, not a knowledge note (it's excluded from search). Your real notes are the other `.md` files here.
