# `Streams/` — dated operational records

> One sub-folder per recurring series, every entry stamped with the moment it belongs to. Read material, not knowledge.

A stream is something that arrives on a schedule or accumulates as you work: an application tracker, an action-item list, a periodic report. Two entries that look alike are two moments, not one fact written twice — which is exactly why single source of truth does not bind this folder.

## What goes here

- **One sub-folder per series**, e.g. `Streams/Job Applications/`.
- **Tabular by default.** A tracker is a `.csv`: the time ordering stays explicit, diffs stay readable in git, and the file stays out of the semantic index.
- **Dated entries.** Every row or file says which moment it belongs to.

## Notes on the folder

- **Not bound by single source of truth.** Repetition across entries is the shape of a series, not a defect. One limit still holds: a stream entry must never contradict a standing claim in a bound corpus. When one does, the standing claim wins — fix the claim if it has gone stale, never rewrite the entry to agree ([single source of truth](../.claude/rules/single-source-of-truth.md)).
- **Not indexed for search.** [Semantic search](../.claude/skills/rag-search/SKILL.md) skips this folder, so a stale entry can never surface as an answer.
- **No log, no classification.** Stream writes carry no `changes.jsonl` entry and no action verdict.
- **Distil, don't promote.** When an entry yields something worth keeping, capture it as a note in [`Notes/`](../Notes/) through the normal flow. The file stays put; the knowledge moves.
- **Starts with two example series**: `Job Applications/` (a tracker, the tabular default) and `Journal/` (dated prose). They belong to the sample vault, they are not yours, and deleting them costs nothing.
