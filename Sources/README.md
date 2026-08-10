# `Sources/` — original source files

> Where the agent keeps the original of anything it captures from — PDFs, documents — so every note can be traced back to where it came from.

When you ingest a source (e.g. via [`pdf-to-notes`](../.claude/skills/pdf-to-notes/SKILL.md)), the agent does two things: it creates notes in [`Notes/`](../Notes/), and it stores the **original file here** as the provenance reference. A *source / hub note* in `Notes/` embeds the original (`![[Sources/your-file.pdf]]`) and links the atomic notes spawned from it — see the [`PDF Source Note`](../Templates/PDF%20Source%20Note.md) template.

## Why keep originals

- **Provenance.** A note can always be checked against the exact source it was distilled from.
- **Re-distillation.** Keeping the original lets you extract more from it later — not just what you captured the first time.
- **Embeds.** Obsidian renders the embedded source right inside its hub note.

## Notes on the folder

- **Not committed to git.** `Sources/*` is gitignored (only `.gitkeep` and this `README.md` are tracked). Originals are often large binaries and personal — they stay **local**, synced with your Obsidian vault rather than pushed to a public repo.
- **Not indexed for search.** [Semantic search](../.claude/skills/rag-search/SKILL.md) indexes only the notes in `Notes/` — the notes you wrote, not the raw sources.
- Starts empty (just a `.gitkeep`).
