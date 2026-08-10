# Templates

Rules for Recorder's template handling — both when creating a note and when appending into one that already follows a template. Maintainer-side template work (template-emergence, refinement across the vault) is out of scope for now.

## Template selection during Creation

When Recorder enters the Creation branch (per `actions.md`):

1. Identify the content shape — what kind of thing is being captured (a tool, a concept claim, a process, a decision log, a project, etc.).
2. Look at templates in `Templates/`. Match content shape against existing templates.
3. If a specialized template fits the shape → use it.
4. Otherwise → use `Templates/General Template.md` as fallback.

## Flagging template-candidate notes

When falling back to General Template, evaluate whether the content shape is reusable:

- Does the new note have clear sectional structure (not just freeform prose)?
- Does the structure plausibly repeat for similar future captures?

Both yes → flag to the user at confirm time:

> This note follows a [content-type] shape that could become a reusable template. Want to crystallize a new template after this note is saved?

Crystallizing one is a separate follow-up turn; no rule covers that flow yet, so it stays manual. Who writes it is settled by `agent ≠ author`.

## Refinement signaling

A template goes stale when notes keep needing something it doesn't offer. Two moments surface that:

**On Creation** — the note's content runs past the specialized template's structure (new sections, fields left empty every time, recurring deviations):

> Note content extends beyond [template name] — added [new section / callout / element]. Future similar captures may suggest refining the template.

**On Assimilation** — a fit-dimension disposition adds a new section to a templated parent (`actions.md` → When a fit dimension fails on Assimilation). That added section *is* a deviation from the template; flag it the same way. Watch for the same kind of section recurring across several notes built on one template — that pattern, not any single instance, is what says the template is missing something.

Whether the template then evolves is the user's call, on accumulated signals. A single deviation is never enough on its own.

## Default

When in doubt, use General Template and proceed. Better to ship the note than stall on template selection.
