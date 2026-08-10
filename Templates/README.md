# `Templates/` — note shapes

> The structures Recorder applies when creating a note, so similar captures come out consistent.

When Recorder creates a note, it picks a template matching the *content shape* — a tool, a concept, a source document, a decision log. Templates make repeated kinds of capture look and read the same.

## What ships here

| Template | For |
|---|---|
| [`General Template.md`](General%20Template.md) | The fallback — used whenever no specialized template fits the content shape. |
| [`PDF Source Note.md`](PDF%20Source%20Note.md) | A source/hub note for an ingested PDF: purpose, embedded original, summary, and links to the atomic notes spawned from it. |

Placeholders like `{{title}}` and `{{date}}` are filled in at creation time.

## How templates are chosen and grow

Selection and evolution are governed by [`.claude/rules/templates.md`](../.claude/rules/templates.md). In short:

- **Selection.** Match the content shape against the templates here; use a specialized one if it fits, otherwise fall back to `General Template.md`.
- **New templates — flagged, never automatic.** When a General-Template note has a clear, reusable structure likely to recur, Recorder **flags it and asks** whether to crystallize a new template. **You decide, and you author it** — Recorder never auto-creates a template (`agent ≠ author`).
- **Refinement.** When a note outgrows its specialized template, Recorder flags the deviation. You decide whether to evolve the template — based on accumulated signals, not a single instance.

## Notes on the folder

- **Committed to git** (unlike `Sources/`) — templates are part of the shared scaffold cloners receive.
- **Not indexed for search** — only the notes in `Notes/` are.
