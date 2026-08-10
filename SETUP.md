# SETUP.md — the setup procedure

> **This file is written for the agent.** If you are a person: you do not need to read it. Open this
> folder in Claude Code and say *"Read SETUP.md and set this vault up for me."* The agent works through
> what follows and asks you the handful of questions only you can answer.

---

## How to run this (agent, read this section first)

You are configuring a fresh copy of this template — cloned *or* unzipped — into **this user's personal vault**. Work through
the phases **in order**. Each phase is written as:

| Field | Meaning |
|---|---|
| **Goal** | What must be true when the phase is done. |
| **Do** | The actions to take. |
| **Check** | The verification that proves it worked. Run it. Do not assume. |
| **On fail** | What to do when the check fails. |

**Five standing constraints for the whole run:**

1. **Never skip a Check.** A phase is not done because you ran the command; it is done because the check
   passed. If a check fails, stop at that phase and resolve it. Do not carry a broken step forward.
2. **Stop at every 🔸 STOP.** Those are the points where only the user can decide. Ask, wait, use their
   answer. Never guess a name, a preference, or a deletion on their behalf.
3. **`atomic-operation.md` does not apply to setup.** That protocol governs *note* changes in `Notes/`.
   Setup touches configuration, so: **ordinary git commits, no `changes.jsonl` entries, no action
   classification.** Do not log setup steps to the ledger.
4. **Never delete anything without explicit permission** (project rule). Two phases want to remove
   something. Both are gated on a direct confirmation, and neither is optional to ask.
5. **Report honestly at the end.** Phases the user declined are *skipped*, not *failed*. Say which is
   which, and say what still needs their hand.

**Shell assumption.** Commands are written for a POSIX shell. On Windows, Claude Code's Bash tool runs
through Git Bash, so these work as written. Where a path differs by OS, the difference is called out.

**Your first message to the user.** Before Phase 0, tell them in two sentences what is about to happen:
roughly ten minutes, you will ask them about four things, and nothing outside this folder changes except
one profile file in their home directory.

---

## Phase 0 — Preflight

**Goal.** You are in the right folder, and git can actually make a commit.

**Do.**

```bash
ls CLAUDE.md .claude/rules/ >/dev/null 2>&1 && echo "STRUCTURE OK" || echo "STRUCTURE MISSING"
git --version || echo "GIT MISSING"
echo "name=[$(git config user.name)] email=[$(git config user.email)]"
```

**Run it exactly as written — do not rejoin these into one `&&` chain.** `git config user.name` exits
non-zero when the value is unset, which is precisely the case you are testing for; chained with `&&` it
would swallow the email check and you would never see it. Each line reports independently.

**Check.** `STRUCTURE OK`, a git version, and **both** brackets non-empty.

**On fail.**

| Symptom | Meaning | Action |
|---|---|---|
| `STRUCTURE MISSING` | Wrong directory, or a zip unpacked one level too deep | Stop. Ask the user to confirm the path they cloned into. Do not create the file. |
| `GIT MISSING` | git is not installed | Stop. Point them at <https://git-scm.com/downloads>, then restart Claude Code after installing (a program installed after a terminal opened is invisible to it). |
| Either bracket is **empty** | **The most common blocker.** Git has no identity to stamp on saved versions, and every capture ends in one | Fix it now, before anything else. 🔸 **STOP** and ask for a name and an email, then set them: |

```bash
git config --global user.name "Their Name"
git config --global user.email "their@email.com"
```

Tell them plainly what this is: a label stamped on each saved version so the history says who made the
change. It is local, it is not an account, and nothing is sent anywhere. Any email is fine.

**Do not tell them capture will crash without it — that is not reliably true, and the truth is worse.**
Depending on the machine, Git either refuses to save *or* quietly invents an identity from their
username and hostname (`jj@Their-MacBook.local`) and stamps that on every note they ever keep. The first
failure is loud and fixable; the second is silent and permanent. Setting it now avoids both.

Re-run the Check before moving on.

---

## Phase 1 — Give it a version history of its own

**Goal.** The vault tracks its own history, and no path exists by which the user's private notes could
be pushed back to the template's repository.

**First find out how they got the files.** The two routes leave the folder in genuinely different
states, and guessing wrong either fails outright or silently leaves the upstream link in place:

```bash
test -d .git && echo "CLONED — already has history" || echo "ZIP — no history yet"
```

### Route A — `ZIP` (the common case)

A downloaded zip carries **no** `.git` directory, so the folder is not tracked at all yet. There is
nothing to detach from, and something to start:

```bash
git init && git branch -M main
```

A zip never had an `origin`, so do not remove one and do not add one. Skip to the Check.

### Route B — `CLONED`

The folder still points at the template. 🔸 **STOP** and offer two options. Recommend the first.

| Option | Command | Effect |
|---|---|---|
| **Keep history, drop the link** *(recommended)* | `git remote remove origin` | Nothing is deleted. The template's commits stay as backstory; every commit from here is theirs. Push is now impossible until they add their own remote. |
| **Start history clean** | `rm -rf .git && git init` | A vault whose history begins today. **Destructive and irreversible** — it discards the template's git history. Only on an explicit yes. |

If they choose the second, confirm once more before running it, then re-run Phase 0's `git config` check
(a fresh `git init` keeps the global identity, but verify rather than assume).

**Check.** Both routes end in the same place — a git repository with no remote:

```bash
git rev-parse --is-inside-work-tree && git remote -v
```

**On fail.**

| Symptom | Action |
|---|---|
| `not a git repository` | Route A's `git init` did not run. Run it. |
| Output still lists `origin` | Route B's removal did not run. Repeat it. |

`true` followed by no remote lines is the correct result on both routes.

---

## Phase 2 — Name the agent

**Goal.** `CLAUDE.md` line 1 carries a name the user chose.

**Do.** Line 1 currently reads `# [Please rename your agent] — Knowledge Base Agent`.

🔸 **STOP.** Ask what they want to call their assistant. This is not decoration: they will type this
name daily, and it is the difference between *"ask Ada what I know about X"* and *"ask the knowledge
base agent"*. Offer two or three suggestions if they hesitate, but **the choice is theirs** — the
`agent ≠ author` rule in `CLAUDE.md` covers exactly this kind of naming.

Edit line 1 only. Change nothing else in the file.

**Check.**

```bash
head -1 CLAUDE.md
```

**On fail.** Square brackets still present → the edit did not land. Re-read the line and retry.

---

## Phase 3 — The user-level profile

**Goal.** A personal profile exists at the user level, filled in with *their* real details, so every
Claude Code session calibrates to them.

**Why this is the highest-value phase.** Without it, the agent guesses at their background and re-learns
their preferences every session. With it, it knows whether to explain a term or assume it, and how
directly to push back.

**Do.**

1. Determine the target path:

   | OS | Path |
   |---|---|
   | macOS / Linux / WSL | `~/.claude/CLAUDE.md` |
   | Windows | `%USERPROFILE%\.claude\CLAUDE.md` (as `~/.claude/CLAUDE.md` in Git Bash) |

2. **Check whether it already exists.** If it does: **do not overwrite it.** Read it, show the user what
   is already there, and 🔸 **STOP** to ask whether to merge anything from the template into it. Their
   existing profile wins by default.

3. If it does not exist, create the directory and copy the template:

   ```bash
   mkdir -p ~/.claude && cp user_level_file_template/CLAUDE.md ~/.claude/CLAUDE.md
   ```

4. **Fill it in by interviewing them, not by handing them a form.** A template with `<Your Name>` in it
   is worse than nothing, and a non-technical user will not fill it in alone. Ask about three things,
   conversationally, one at a time:

   | Ask about | What you are listening for |
   |---|---|
   | **Background** | What they do, and what they already know, so explanations land at the right level. |
   | **Working mode** | How much time they have; whether they want thorough or quick. |
   | **Communication** | Length, tone, and whether they want to be disagreed with. |

   Then write the file in their words and show it to them for approval before saving. Be specific on
   their behalf: *"Explain things simply"* is weak; *"I have never written code, use everyday words and
   skip the jargon"* is strong. Delete any template section that does not apply to them, and remove all
   scaffolding: the `<Your Name>` placeholder, every `> **Guidance:**` note, and any example persona
   they did not keep.

**Check.**

```bash
test -f ~/.claude/CLAUDE.md \
  && ! grep -q "<Your Name>\|> \*\*Guidance:\*\*" ~/.claude/CLAUDE.md \
  && echo "PROFILE OK" || echo "PROFILE INCOMPLETE"
```

Prints `PROFILE OK`.

**Read the printed word, not the exit code.** This check deliberately prints a verdict because the
obvious alternative (`grep -c`) exits **1** when it finds zero matches, which is the *passing* case.
Both outcomes here exit 0; only the text distinguishes them.

**On fail.** `PROFILE INCOMPLETE` means one of two things: the file does not exist, or scaffolding is
still in it. Either way setup is *not* complete. Finish the personalization before continuing. This same
check gates Phase 7.

---

## Phase 4 — The first commit

**Goal.** A clean working tree.

**Why this is not optional.** `atomic-operation.md` tells the agent to run `git status` at the start of
every session and to treat a dirty tree as an interrupted operation that must be surfaced to the user.
A configured-but-never-committed vault therefore reports a problem *every single session* until
something commits. This phase is what stops that.

**Do.**

```bash
git add -A && git commit -m "chore: configure vault (agent name, profile, history started)"
```

**Check.**

```bash
git status --short && git log --oneline -1
```

**On fail.**

| Symptom | Action |
|---|---|
| `Please tell me who you are` | Phase 0's identity check was skipped or did not stick. Go back and set it. |
| `nothing to commit` | Phases 1–3 made no local change. Verify each landed rather than moving on. |

Working tree clean, one commit listed → done.

---

## Phase 5 — Semantic search *(optional)*

**Goal.** Either semantic search works, or the user has knowingly declined it.

**Why it is worth offering.** Without it, search matches the words they typed. With it, a note about
"keeping options open" surfaces when they later ask about "flexibility". Three things lean on it:
answering questions from their notes, catching a duplicate they wrote in different words, and finding
two notes that say the same thing during a maintenance pass.

🔸 **STOP.** Ask whether to set it up now. Make declining genuinely comfortable: **everything works
without it**, it degrades to word search, and it can be added any time. If they decline, skip to Phase 6
and record it as *skipped*, not failed.

**Do.**

1. Check for `uv`:

   ```bash
   uv --version
   ```

   Missing? Give them the line for their OS, then tell them to **quit Claude Code and their terminal
   completely and reopen** before continuing. This is not superstition: a terminal resolves where
   programs live when it opens, so one that was already running cannot see a newly installed tool.

   | OS | Install |
   |---|---|
   | macOS (Homebrew) | `brew install uv` |
   | macOS / Linux | `curl -LsSf https://astral.sh/uv/install.sh \| sh` |
   | Windows (PowerShell) | `powershell -c "irm https://astral.sh/uv/install.ps1 \| iex"` |

2. Create the environment and install into it. **The directory must be named exactly `.venv`** — the
   `rag-search` skill resolves its interpreter relative to that name, and any other name means a silent
   fall back to word search.

   ```bash
   uv venv .venv && uv pip install -r requirements.txt && cp .env.example .env
   ```

3. Configure `.env`. 🔸 **STOP** and ask which mode they want:

   | Mode | Set | Reality |
   |---|---|---|
   | **Offline** | `EMBED_PROVIDER=mock` | No key, no cost, no network. Proves the wiring works. **Not real search** — the results are consistent but meaningless. |
   | **Live** | `EMBED_PROVIDER=api` | Real meaning-based search. Needs an embeddings provider account and an API key. |

   For live mode they also need `EMBED_BASE_URL`, `EMBED_API_KEY`, `EMBED_MODEL`, `EMBED_DIM`. OpenAI
   defaults are already in `.env.example` and are a fine starting point. Before they paste a key into
   the chat, tell them the alternative: they can put it into `.env` themselves and you will not see it.
   Either way `.env` is gitignored, so the key never enters git history.

   **If they do not have a key, walk them through getting one — do not just name the requirement.**
   This is the step most likely to end the session, so spend the time. Cover these in order:

   | Point | What to tell them |
   |---|---|
   | **What they are paying for** | Turning each note into a numeric fingerprint so meaning-matching works. Only that. It is charged when the index is built or refreshed, never while they type, read, or ask. |
   | **Roughly what it costs** | Fingerprinting is far cheaper than having a model write or reason. For one person's notes a small top-up typically lasts a long time. Give the shape, not a number — rates change, and point them at the provider's pricing page. |
   | **A subscription is not a key** | **State this explicitly.** ChatGPT Plus, and equally a paid Claude plan, include nothing here. API access is a separate account with a separate bill. This is the single most common misunderstanding. |
   | **Money before key** | At <platform.openai.com> → Settings → Billing: add a payment method and buy a small amount of credit *first*. On a zero balance the key exists but every request is refused, and the error does not mention the balance. |
   | **Auto-recharge** | Suggest leaving it off unless they want it. Worst case then is that it stops working, never a surprise bill. |
   | **Make the key** | At <platform.openai.com/api-keys> → **Create new secret key**, named something recognisable. Defaults are correct. |
   | **Copy it immediately** | It is shown once. Closing the dialog without copying means deleting it and making another. |
   | **Handle it like a card** | It starts with `sk-`. It belongs in `.env` and nowhere else — never an email, a screenshot, or a chat window. |

   Any OpenAI-compatible provider works; only the address and model name change. Offer that only if they
   raise it — a second option here is a decision they do not need.

   One trap worth stating: `EMBED_SEND_DIMENSIONS=true` suits OpenAI v3 models. For any other provider
   set it `false`, and then `EMBED_DIM` must exactly match that model's own dimension.

4. Build the index:

   ```bash
   PY=$([ -x .venv/bin/python ] && echo .venv/bin/python || echo .venv/Scripts/python.exe)
   "$PY" .claude/skills/rag-search/scripts/reindex.py auto
   ```

**Check.** The command reports how many notes it processed and a `.rag-index/` directory appears.

**Expect 0 notes on a fresh vault, and say so.** `Notes/` starts empty, so there is genuinely nothing to
index yet. That is a correct result, not a failure. Tell the user the index is refreshed manually, and
they should ask for a reindex once they have written some notes.

**On fail.** `RAG_UNAVAILABLE` → three causes, in order of likelihood: the venv is not named exactly
`.venv`; `.env` is missing or says `api` with no key; a value in `.env` is wrong. Word search keeps
working throughout, so this is narrower, not broken.

---

## Phase 6 — Outside connectors *(optional)*

**Goal.** Either the services holding their existing material are connected, or they have declined.

🔸 **STOP.** Ask whether their notes already live somewhere else (Notion, Drive, Slack). If not, skip
this phase entirely — nothing else depends on it.

**First work out which surface they are on.** This decides everything below, and getting it wrong wastes
their time on a command that cannot exist:

```bash
command -v claude >/dev/null 2>&1 && echo "CLI AVAILABLE" || echo "NO CLI — desktop app"
```

The **desktop app ships Claude Code without installing the `claude` command-line tool**. If this prints
`NO CLI`, every `claude mcp …` command below will fail with *command not found*, and that is expected,
not a broken install.

**Route A — `NO CLI` (desktop app).** You cannot add the connector for them, so do not try. Hand them
the steps instead: connectors have a graphical setup flow in the app. Tell them to open **Settings →
Connectors**, find their service, and follow the sign-in. Services not listed there need an entry in a
settings file, which is worth doing only if they actually need one — offer, do not assume.

**Route B — `CLI AVAILABLE`.** Add it yourself, using Notion as the worked shape:

```bash
claude mcp add --transport http notion https://mcp.notion.com/mcp
```

Reading it left to right: add a connector, it talks over http, call it `notion`, here is its address.
Only the last two change per service. Addresses come from <https://claude.ai/directory> — look the
service up rather than guessing a URL. `claude mcp remove <name>` undoes it.

**Then hand the sign-in back to them, on either route.** Adding a connector only records *where* the
service is; it grants no access. The sign-in happens in a browser and only they can complete it.

Say plainly what they are agreeing to: signing in lets the assistant read that account. Connect only
services they are comfortable with it seeing.

**Check.** Route B: `claude mcp list` names it. Route A: ask them to confirm the app shows it connected.

**On fail.** `failed to connect` → the address is wrong; check it against the directory. `command not
found` on Route B → you misread the surface; go back to Route A.

---

## Phase 7 — Remove the setup scaffolding

**Goal.** `user_level_file_template/` is gone, but **only** once the profile it exists to seed is real.

**Do.**

1. **Re-run Phase 3's check.** Anything other than `PROFILE OK` means setup is **not** complete: say so
   and **do not delete**. This gate is not a formality — deleting the template before the profile is
   written destroys the only copy the user has.

   ```bash
   test -f ~/.claude/CLAUDE.md \
     && ! grep -q "<Your Name>\|> \*\*Guidance:\*\*" ~/.claude/CLAUDE.md \
     && echo "PROFILE OK" || echo "PROFILE INCOMPLETE"
   ```
2. Gate passed → 🔸 **STOP.** Show them exactly what will be removed and ask for a direct confirmation.
   The project's *never delete without explicit permission* rule applies in full.
3. On their yes:

   ```bash
   rm -rf user_level_file_template/
   git add -A && git commit -m "chore: remove user-level template after setup"
   ```

   Its own commit, separate from Phase 4.

**Check.** `ls user_level_file_template` reports no such file, and `git status --short` is clean.

---

## Phase 8 — Verify and hand over

**Goal.** The user has seen it work, and knows what to do next.

**Do.**

1. **Confirm the vault's instructions loaded — by behaviour, not by a command.** Have them ask:
   *"Which agent are you, and what are you not allowed to do?"* A correct answer names Recorder, says it
   handles conversation and capture, and says it does **not** merge or split notes. A vague or generic
   answer means `CLAUDE.md` did not load, and nothing below will behave properly.

2. **Confirm their profile loaded, the same way.** Have them ask: *"What do you know about me?"* The
   answer should reflect what they told you in Phase 3. Generic flattery means the profile is not being
   read — check the path and the exact filename.

   > **Do not send them to `/context` for this.** It renders an interactive panel, and panel commands
   > behave differently in the desktop app's Code tab, so the instruction fails for exactly the people
   > most likely to need it. The two questions above test the same thing by observing behaviour, and
   > they work on every surface. If they are on the CLI and want the raw list, `/context` is there —
   > offer it as extra, never as the check.
3. **Point them at Obsidian.** The vault is plain markdown, so any editor works, but Obsidian is the
   reading surface this is built around: graph view, backlinks, and the wikilinks rendered properly.
   *Open folder as vault*, pointed at this directory. Download: <https://obsidian.md>. It is optional
   and changes nothing about how the agent works.
4. **Write the closing report.** Cover exactly four things:

   | Report | Content |
   |---|---|
   | **Configured** | Which phases completed. |
   | **Skipped** | Which optional phases they declined, and how to add each later. |
   | **Needs them** | Anything still in their hands — an MCP sign-in, an API key, a reindex. |
   | **Next** | One concrete first move: talk about something they are working through, then say *"capture that as a note."* |

**Check.** Both questions in steps 1 and 2 came back correct, and they have a stated next action.

---

## Quick reference — what setup touches

| Path | Change |
|---|---|
| `CLAUDE.md` | Line 1 only: the agent's name |
| `~/.claude/CLAUDE.md` | Created and filled in (**the only file written outside this folder**) |
| `.git/` | Created (zip route), or `origin` removed (clone route) |
| `.env` | Created from `.env.example` *(optional phase)* |
| `.venv/`, `.rag-index/` | Created; both gitignored *(optional phase)* |
| `user_level_file_template/` | Deleted, on confirmation, last |

Untouched: every file in `.claude/rules/`, `.claude/skills/`, `.claude/agents/`, and all four content
folders. Setup configures the vault; it does not modify how it thinks.
