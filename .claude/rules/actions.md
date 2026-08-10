# Actions

Every vault change is one of these actions, in three families: **Inflow** — one judgment with two branches; **Maintenance** — two; **Generation** — one. Each branch is its own value in the log's `action` field (`log-schema.md`).

## Assimilation / Creation (Inflow)

One action, two branches. Triggered by Recorder at the user's explicit capture.

**One capture ≠ one action.** A single capture may contain several distinct concepts (e.g., a multi-concept document). Decompose first, then apply the judgment below to each concept independently — one capture can yield multiple actions, mixing Assimilation, Creation, and deferral. Each resulting action is its own atomic operation (`atomic-operation.md`): one edit, one log line, one commit.

Decompose semantically, where the capture changes subject. The split is a hypothesis, not a verdict — the judgment below prices it. **When unsure how fine to cut, cut coarser**: an over-split fragment fails the substance test and is deferred, so that content never lands in the vault at all, while an under-split note at least exists and can be Fissioned later.

- **Assimilation** — append to existing note.
- **Creation** — create a new atomic note inside `Notes/` (flat). Template selection per `templates.md`.

### 5-dimension judgment

Applied against the candidate parent note(s) turned up by the dual search in `single-source-of-truth.md` → Detection. Run that search first: these dimensions are only as good as the candidates they judge.

The five split into two kinds. **Branch dimensions** are properties of the content and the vault, judgeable on their own — being independent, they form the quadrants. **Fit dimensions** are properties of a *pairing* (this content, that parent) and mean nothing until a specific parent is on the table — they decide whether the chosen branch can actually be carried out.

**Branch dimensions**

| Dimension | Question |
|---|---|
| Semantic overlap | Does an existing note already cover this concept? |
| Substance distance | Does the new content alone pass concept-level Substance Test? |

Resulting quadrants:

- Existing covers + not substantial → **Assimilation**.
- No coverage + substantial → **Creation**.
- Existing covers + substantial → SSOT risk; raise per `single-source-of-truth.md`. Possible resolutions: child concept gets its own note with wikilink to parent, or refinement of existing — the user decides.
- No coverage + not substantial → defer (no vault change).

**Fit dimensions**

| Dimension | Question |
|---|---|
| Structural fit | Does the new content slot naturally into a parent's existing sections? |
| Granularity match | Are new content and parent at the same abstraction level? |
| Reframing risk | Would appending change the parent's claim or scope? |

On Creation there is no parent to fit into: record the three as not-applicable rather than inventing a verdict. All five still get an explicit verdict in the log (`atomic-operation.md` → Reasoning visibility).

### When a fit dimension fails on Assimilation

The quadrant can be right and the append still wrong. Check the fit dimensions before appending — each failure means something different, so each gets its own disposition:

| Failing | What it means | Disposition |
|---|---|---|
| **Structural fit** alone (granularity and reframing both pass) | right altitude, no reframing — the parent simply has no section for this | **Add a new section** to the parent and append there. The agent's call. |
| **Granularity match** | the content is a *child concept*, not an addition to this one | **Do not append.** Propose a child note carrying a `[[wikilink]]` to the parent — the user decides. |
| **Reframing risk** | appending would widen or alter what the parent claims | **Do not append.** Surface the parent's current scope against the proposed change — the user decides. |

Two failing together: take the stricter disposition. The two "do not append" rows exit the same way as the SSOT-risk quadrant (`single-source-of-truth.md` → When you detect a duplicate or conflict): name the failing dimension, present the options, let the user choose. Never append past a failing fit dimension on the agent's own authority.

### Tie-breaker

Only when the 5 dimensions are genuinely ambiguous: prefer the more reversible action — Assimilation + wikilink stub. Invocation requires `reason` to show per-dimension verdicts proving ambiguity. Defaulting to append without per-dimension reasoning is forbidden. See `atomic-operation.md`.

Substance Test reference: `substance-test.md`.

## Maintenance and Generation

Maintainer-only. Each is named here with its trigger and its owner; the tests and gates a candidate must pass before it can be proposed live in the `maintenance-actions` skill, preloaded into that sub-agent.

| Action | Family | Trigger |
|---|---|---|
| **Fission** | Maintenance | one note holds multiple concepts that should be separate atoms |
| **Convergence** | Maintenance | two notes describe the **same concept**. Detection and handling per `single-source-of-truth.md` |
| **Emergence** | Generation | several notes share a concept **named nowhere** — not a note title, not a wikilink target |

Recorder never proposes or executes these. It surfaces SSOT conflicts at capture time and hands them over; it does not merge or split. Maintainer proposes and stops; the main session executes what the user approves.

**A named stub is not Emergence.** A dangling `[[wikilink]]` — a target referenced across notes but never written — is *already named*, which makes it a Recorder Creation-deferred stub (`substance-test.md` → fail handling). That puts it outside Emergence, and outside Maintainer's scope entirely, since Maintainer never proposes Creation. Emergence covers only the genuinely *implicit*: a theme recurring in note **prose** that no one has even linked yet.

**The link tier is not a fifth action.** When Convergence de-escalates to "add a `[[wikilink]]` rather than merge", the approved change executes as a narrow Assimilation — one appended link — and logs as `Assimilation` with `agent: "maintainer"`.
