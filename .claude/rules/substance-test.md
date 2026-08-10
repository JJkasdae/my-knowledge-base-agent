# Substance Test

The single judgment criterion for whether a concept deserves its own structure (wikilink, note, section). Used by both Recorder and Maintainer.

## The test

> Does this concept have room to grow — background, sub-topics, examples, evolution potential?

Same question, two thresholds.

## Phrase-level — low bar, forward-looking

For wikilink decisions inside note text. Pass if the concept *could plausibly* grow into a note someday. See `linking.md`, which owns how the verdict is applied to links.

## Concept-level — high bar, present-tense

For creating new notes (Creation / Emergence) or splitting (Fission).

**Pass condition**: the agent must produce, using only material already present, both:
1. A defining sentence for the concept
2. At least one sub-topic candidate

**Allowed sources**: the material in front of you — the capture being processed (Recorder) or the in-scope notes being scanned (Maintainer) — plus any vault note you have actually read.

**Forbidden sources**: your training knowledge. Anything not traceable to the vault or to the material at hand is hallucination for this test.

**Fail handling**: the candidate stays a wikilink stub or plain text. Re-evaluate on a later pass, once more material has accumulated.

Drafts are probes, not final content — the user writes the final defining sentence (`agent ≠ author`).

## Application

| Decision | Threshold | Owner |
|---|---|---|
| Wikilink a term in note text | Phrase | `linking.md` |
| Assimilation / Creation: append vs new note | Concept | `actions.md` |
| Fission: split sub-concept out | Concept | `maintenance-actions` skill |
| Emergence: implicit theme → note | Concept | `maintenance-actions` skill |
| Capture nudge during Discuss / Brainstorm | Concept | `CLAUDE.md` → Recorder role |

## Reasoning visibility

Concept-level drafts must reach the log, never stay in the agent's head. `atomic-operation.md` → Reasoning visibility owns the requirement.
