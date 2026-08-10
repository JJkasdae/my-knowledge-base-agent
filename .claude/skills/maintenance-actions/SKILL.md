---
name: maintenance-actions
description: The tests and gates a maintenance proposal must pass before Maintainer may surface it — Fission's two-layer test, Convergence's identity gate, Emergence's adjacency gate. Preloaded into the maintainer sub-agent at startup, where it is the authority on every maintenance judgment call. Recorder does not run maintenance actions and has no reason to invoke this.
---

# Maintenance actions — tests and gates

What each maintenance action *is*, when it triggers, who owns it, and how it logs: `.claude/rules/actions.md` → Maintenance and Generation. This file holds what it takes to justify proposing one.

Every test below runs on material actually in the vault or in scope. Inventing support from training knowledge fails the test outright (`substance-test.md`).

## Fission — two-layer test

Both layers required, for **≥2 sub-concepts inside one note**:

1. **Substance independence** — each sub-concept independently passes the concept-level Substance Test (`substance-test.md`).
2. **Semantic independence** — draft a minimal standalone note for each candidate. If the draft has to inline another sub-concept's explanation to make sense, semantic independence fails. Wikilink-only references between them are fine.

AND'd, not OR'd. Both pass → propose Fission.

Post-Fission disposition of the original note — hub, full redistribution, or partial extract — is decided per case when the user confirms, not chosen by you in advance.

## Convergence — identity gate

Semantic similarity (a rag-search hit, shared vocabulary) only *nominates* a pair. It is never the verdict. Before proposing, ask:

> Would a knowledgeable reader say these two notes are trying to be the *same note*?

| Verdict | Condition | Propose |
|---|---|---|
| **Same concept** | one note is a redundant or conflicting representation of the other (the duplicate/conflict of `single-source-of-truth.md`) | **Convergence** — merge |
| **Distinct but related** | each has independent identity; only domain or vocabulary overlap | a **link** — never a merge |
| **Incidental** | shared words, no real relationship | nothing — drop it |

The link tier is the de-escalation path: it keeps similar-but-independent atoms separate while still surfacing the relationship. A proposed link is a link decision, so it passes `linking.md` first or it is not proposed.

Reserve Convergence for genuine same-concept duplication. **In doubt between merge and link, choose link** — it is the more reversible of the two.

## Emergence — substance test plus adjacency gate

**Test**: the concept-level Substance Test on the implicit concept, using the notes that mention it as material. Pass → propose. Fail → keep observing; say nothing.

**Adjacency gate.** A candidate often brushes an existing note that names only *one application* of it — a domain-general principle whose narrower, domain-scoped instance already has a note. Adjacency is not coverage:

| Verdict | Condition | Propose |
|---|---|---|
| **Emergent** | the general concept has no note of its own; existing notes name only slices or applications of it | **Emergence** — a new *parent* atom the slices are instances of |
| **Already covered** | the concept *as a concept* is already the subject of some note | nothing — drop it (propose a link instead if a real relationship is unlinked) |

The proposed note must be the general principle, never a restatement of an existing slice — restating one would create the duplicate the vault exists to prevent. Genuinely unsure which side of the gate a candidate falls on? **Drop it and keep observing.**

The defining sentence you draft is a probe. The user writes the one that lands (`agent ≠ author`).
