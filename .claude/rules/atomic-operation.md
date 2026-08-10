# Atomic Operation

Every change to a concept note in `Notes/` must complete the following three steps in a single agent turn. This holds whether the change began as a Recorder capture or as an approved Maintainer proposal — Recorder holds the pen either way, because Maintainer never writes.

Writes elsewhere in the vault are outside this protocol: no log entry, no action classification (`log-schema.md`). Single source of truth still binds them.

## The three steps

1. Edit / create / delete the markdown file(s).
2. Append a JSONL line to `.claude/log/changes.jsonl` per `log-schema.md`.
3. `git add -A && git commit -m "<message>"` with the format defined in `log-schema.md`.

If any step fails, the turn is incomplete. Surface the failure to the user; do not retry partial.

## Reasoning visibility (hard constraint)

Step 2's `reason` field must carry the agent's per-dimension reasoning for that specific action.

- **Assimilation / Creation**: an explicit verdict on all five dimensions — the two branch dimensions (Semantic overlap, Substance distance) and the three fit dimensions (Structural fit, Granularity match, Reframing risk) — plus the branch decision. On Creation there is no parent, so the fit dimensions are recorded as not-applicable, never invented.
- **Fit-dimension failure**: name the dimension that failed and the disposition taken — which new section was added, or exactly what was put to the user (`actions.md`).
- **Concept-level Substance Test** (Creation / Emergence / Fission): draft defining sentence + sub-topic candidate, with source traces.
- **Tie-breaker invocation**: dimension-by-dimension reasoning showing the ambiguity is genuine. "Defaulting to append because reversible" is forbidden. See `actions.md`.

The user verifies the work through `reason` at confirm time. Maintainer reads it for anti-repeat.

## Session-start recovery

At the start of every Recorder session, run `git status`. A dirty working tree means a previous operation was interrupted — surface it to the user before taking any new action. The user decides commit / rollback / discard.
