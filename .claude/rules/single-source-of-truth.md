# Single source of truth — handling

The principle ("one concept or fact = one representation, referenced never copied") lives in `CLAUDE.md`. This file is the procedure for detection and resolution.

## What counts as a duplicate or conflict
- **Similar representations**: the same idea phrased differently — across notes, or within a single note (old version + updated version coexisting after an edit).
- **Conflicting representations**: incompatible claims about the same thing, whether across notes or within one note's history.

Both are forbidden — they cause misunderstanding later.

## Scope

**What it covers**: concepts, definitions, entities (people / tools / projects), factual claims, idea descriptions.

**Where it applies**: every corpus marked *bound* in the `CLAUDE.md` corpus table. Being bound is independent of whether the action machinery runs there — a bound corpus with no machinery gets no later sweep to catch a miss, so its write-time check carries the whole weight.

**Standing claims vs dated snapshots** — the criterion behind that column. A **standing claim** is something the vault presents as currently true; two standing claims that disagree are a real conflict. A **dated snapshot** records what was true, or what was said, at one moment; two snapshots that differ are two moments, not one fact stated twice. Standing claims are bound. Snapshots are not.

**Where the two meet**: a snapshot must never contradict a standing claim. When one does, the standing claim is authoritative — update it if it has gone stale, and never rewrite the snapshot to agree with it. This holds even though the snapshot's own corpus is unbound: an unbound corpus is free from internal repetition, not free to contradict the record.

**Across corpora**: a fact lives once, in the note that owns it. A derived note carries framing, judgment, or narrative and **links** to the owning note; it never holds a second copy of the fact. Generated outputs are outputs — never written back as the source.

## Detection — search both ways before concluding "no existing representation"

Two mechanisms with different blind spots. Run both:

- **Plaintext** — exact terms; also catches notes created too recently for the RAG store to have indexed them (the store lags, by design).
- **rag-search (semantic)** — reworded or fuzzy duplicates plaintext misses; covers only what is already indexed.

Neither alone is conclusive: RAG-empty ≠ no duplicate (the store may lag); plaintext-empty ≠ no duplicate (the wording differs). Plaintext runs every time. The semantic pass is **attempted before concluding that no existing representation exists**, and whenever plaintext is inconclusive.

RAG is **best-effort, never a hard gate**. If it is unavailable (`RAG_UNAVAILABLE`; see the rag-search skill's *Availability* section), plaintext-only is a valid fallback and the work proceeds. Never reindex to close the gap in the moment — the store is refreshed manually, and plaintext covers the lag.

## When updating existing content

Replace, don't accumulate. When updating a viewpoint, fact, or section:
- Rewrite the affected content in place. Do not leave the prior version commented out, struck through, or appended as "previously…".
- Remove redundancies that the edit introduces — duplicated phrasing, leftover transition sentences, stale cross-references.
- Audit trail lives in git history and the JSONL log, never in the note body.

Same principle applied inside one note: one representation per concept, even across time.

## When you detect a duplicate or conflict

**Stop before writing.** Never silently merge, reconcile, or pick one.

Present three things:
1. **Where** the existing representation lives (wikilink to the note).
2. **What** the overlap or conflict is, in one or two sentences.
3. **Options** — typically: link to the existing note, merge, update the existing one, or rename if the scopes actually differ.

Then let the user decide.

**Recorder** raises it in the conversation and waits for the answer. **Maintainer** has no channel to ask mid-run: it puts the same three things into its proposal report and lets the main session take them to the user.
