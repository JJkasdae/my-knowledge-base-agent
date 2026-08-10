# `Profile/` — the record of you

> Your background, roles, education, and skills, held as standing fact. Résumés, cover letters, and applications are generated *from* here — never written back into it.

A hub plus a few cross-linked notes. Unlike [`Notes/`](../Notes/), this isn't knowledge you worked out; it's the factual record the agent grounds every claim about you in, and the one source the generation skills read.

## What goes here

- **Facts about you** — background, roles held, education, skills, credentials, public profiles.
- **One fact, one home.** A fact lives in the note that owns it. A derived note (a story bank, a positioning summary) carries framing and **links** to the owning note; it never keeps a second copy ([single source of truth](../.claude/rules/single-source-of-truth.md)).
- **Not outputs.** A tailored résumé is generated *from* this folder. It never comes back here as the record.

## Notes on the folder

- **Bound by single source of truth, with nothing sweeping behind it.** `Profile/` is a bound corpus that runs no action machinery — no action classification, no `changes.jsonl` entry, no Maintainer pass. Nothing catches a duplicate later, so the whole check happens at the moment the agent writes.
- **Structure is yours.** The flat-notes rule governs `Notes/`; here a hub plus a handful of topic notes is usually enough.
- Starts empty (just a `.gitkeep`).
