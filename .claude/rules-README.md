# `.claude/rules/` — the agent's judgment layer

> The always-loaded rules that decide **what** the agents do and **how** every change lands. Edit these to change how your assistant thinks.

Seven markdown files, the agents' *constitution*. Unlike [skills](skills/) (on-demand procedures), the rules are **always in context** — and in *both* agents' context, because Claude Code loads every `.claude/rules/*.md` into the main session and into every sub-agent, with no setting that opts one out. So every capture, link, and cleanup is judged against them.

They hold **judgment criteria, not step-by-step procedures** — *should this become a note? is this a duplicate? how is a change recorded?* Behavior follows the files: change a rule and the agents change with it, no code to touch.

> This file lives *outside* `.claude/rules/` on purpose. Anything with a `.md` extension inside that folder is loaded into every session of both agents, so documentation written for humans would be paid for on every turn, twice over, forever.

## The files

Three groups: what to decide, how to keep the graph clean, how a change lands.

**Judgment core — decide what to do**

| File | Governs |
|---|---|
| [`substance-test.md`](rules/substance-test.md) | The single criterion — does a concept have enough room to grow to deserve its own note or link? Two thresholds: phrase-level (links) and concept-level (notes). |
| [`actions.md`](rules/actions.md) | The taxonomy every vault change maps to — Assimilation/Creation (capture), Fission, Convergence, Emergence — plus the full Inflow judgment. |

**Relationship integrity — keep the graph clean**

| File | Governs |
|---|---|
| [`linking.md`](rules/linking.md) | When to wikilink a term (phrase-level substance test) versus leave it plain text. |
| [`single-source-of-truth.md`](rules/single-source-of-truth.md) | Detecting and resolving duplicate / conflicting representations *before* writing. Drives Convergence. |

**Write discipline — how a change lands**

| File | Governs |
|---|---|
| [`atomic-operation.md`](rules/atomic-operation.md) | The non-negotiable 3-step write protocol — edit → log → commit, all in one turn. |
| [`log-schema.md`](rules/log-schema.md) | The JSONL ledger format every change is recorded in. |
| [`templates.md`](rules/templates.md) | Template selection when a note is created, and the deviation signal when one is appended to. |

Two pieces of judgment deliberately live elsewhere:

- **Which corpora exist, and what binds each**, is a table in [`CLAUDE.md`](../CLAUDE.md) → Corpora. Adding a corpus is a row there, not a rule change.
- **The tests behind Fission, Convergence, and Emergence** are in the [`maintenance-actions`](skills/maintenance-actions/SKILL.md) skill, preloaded into the Maintainer sub-agent. Recorder never runs those actions, so it never carries their tests — the one slice of the judgment layer that reaches a single agent.

## How they fit together

`substance-test.md` is the **foundation** — one criterion reused everywhere. `actions.md` is the **spine** — it routes every change to an action and leans on the substance test for the hard calls. The rest are **specifics the spine delegates to**: `linking.md` and `single-source-of-truth.md` keep relationships clean; `atomic-operation.md`, `log-schema.md`, and `templates.md` govern the mechanics of writing a change down.

One discipline runs through all of them — the same single source of truth the vault enforces, applied to the rules themselves: **each rule is stated in exactly one file; the others reference it.** (The "use wikilinks" *principle* lives in [`CLAUDE.md`](../CLAUDE.md); the *criterion* in `substance-test.md`; the *link specifics* in `linking.md`.) Respect this when editing — change the one authoritative file, never a copy.

## A newcomer's reading order

1. [`CLAUDE.md`](../CLAUDE.md) — who the two agents are, which corpora exist, what binds them.
2. [`substance-test.md`](rules/substance-test.md) — the criterion everything else uses.
3. [`actions.md`](rules/actions.md) — the taxonomy that organizes all changes.
4. [`atomic-operation.md`](rules/atomic-operation.md) + [`log-schema.md`](rules/log-schema.md) — how a change is executed and recorded.
5. The rest as needed — `linking.md`, `single-source-of-truth.md`, `templates.md`.

## Customizing

These files *are* the customization surface — the [root README](../README.md) points here for "where to change the agents' behavior." To tune the assistant to how you think:

- **Edit the authoritative file, not a restatement elsewhere** (single source of truth — see above). Some behaviors are split by design: how aggressively **Emergence** fires lives in both the [`maintenance-actions`](skills/maintenance-actions/SKILL.md) skill (the test and the adjacency gate) and [`maintainer.md`](agents/maintainer.md) (the ≥3-notes negative-space sweep).
- **Keep them terse.** Rules load into *every* session of *both* agents, so every line costs context twice. Add judgment, not prose.
- **Put role-specific judgment where only that role sees it.** A rule file reaches both agents no matter what it says. To give one agent something the other never carries, it has to be a skill preloaded through the `skills:` frontmatter of that sub-agent — the only per-agent channel there is.
- **Preserve `agent ≠ author`.** The rules deliberately make the agent *propose* drafts while *you* write the final wording. It is a vault principle in [`CLAUDE.md`](../CLAUDE.md); loosening it trades your voice for speed, so do it knowingly.

> A rule change takes effect on the **next** session (context is rebuilt at startup). Test a tweak by running a capture or a Maintainer scan and checking the result against your intent.
