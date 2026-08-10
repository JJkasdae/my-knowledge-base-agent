# Log Schema

JSONL append-only ledger at `.claude/log/changes.jsonl`. Records every change to a concept note in `Notes/`, plus every Maintainer proposal the user decided on. Writes elsewhere in the vault are still bound by single source of truth, but are not logged here — the ledger and the action classification are `Notes/`-only. Used by Maintainer for anti-repeat, by the user for audit (mirrored in git commit messages).

## Entry format

```jsonl
{
  "timestamp": "ISO8601",
  "agent": "recorder" | "maintainer",
  "action": "Assimilation" | "Creation" | "Fission" | "Convergence" | "Emergence",
  "targets": ["note title or path", ...],
  "reason": "per-dimension reasoning required by the action's rule",
  "outcome": "executed" | "rejected",
  "decision_note": "the user's rationale for the decision — required when rejected",
  "decided_by": "the user"
}
```

## Field notes

- `targets`: note titles or paths affected. Multiple entries when an action spans notes (e.g., Convergence merges two).
- `reason`: the per-dimension reasoning the action's own rule requires. `atomic-operation.md` → Reasoning visibility owns what must appear for each action.
- `outcome`: two states only. `executed` = vault changed. `rejected` = Maintainer proposal the user declined (vault unchanged, log + commit only). Pending proposals live in chat, not in log.
- `decision_note`: the user's own rationale for the decision, in their framing — distinct from `reason` (the agent's proposal logic). **Required when `outcome` is `rejected`** so every decline is traceable; optional on `executed` (record only when the user added a notable instruction or modification). A rejection whose rationale reveals a *repeatable pattern* may also be distilled into `.claude/maintainer/lessons.md` (event-level here, pattern-level there).
- `decided_by`: always `the user`. Agents never self-approve.

## Who writes entries

**Recorder writes every entry. Maintainer writes none** — it is read-only, and that includes the log.

| Situation | Entry written |
|---|---|
| Recorder executes an Assimilation or Creation | 1 — `agent: "recorder"`, `outcome: "executed"` |
| The user approves a Maintainer proposal | 1 — `agent: "maintainer"`, `outcome: "executed"` |
| The user rejects a Maintainer proposal | 1 — `agent: "maintainer"`, `outcome: "rejected"`, `decision_note` required. Vault unchanged; log + commit only |
| A Maintainer proposal is still awaiting a decision | none — pending proposals live in the conversation, not the ledger |

The `agent` field records **whose class of action it was**, not which layer held the pen: the main session writes Maintainer's entries on its behalf. See `.claude/agents/maintainer.md`.

## Anti-repeat

Before generating any new proposal, Maintainer greps `outcome: rejected` entries and uses semantic judgment (not string equality) to decide if the candidate is materially the same as a previously rejected one. Match → skip. Repeated rejections for the same target → flag to the user rather than re-propose.

## Git commit alignment

Every log entry corresponds to one git commit (see `atomic-operation.md`). Commit message format:

```
[<outcome>] <action>: <targets> | <short reason>
```
