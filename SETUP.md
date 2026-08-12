# SETUP.md — the setup procedure

> **This file is written for the agent.** If you are a person: you do not need to read it. Open this
> folder in Claude Code and say *"Read SETUP.md and set this vault up for me."*

---

## How to run this (agent, read this section first)

You are configuring a fresh copy of this template into **this user's personal vault**. Four phases, in
order. Target: **five minutes**, and **one** point where you ask the user anything.

**Four standing constraints for the whole run:**

1. **Ask everything in Phase 2, in a single message. Nothing before it, nothing after it.** Phases 1, 3
   and 4 are silent: run them, report what happened, do not stop for approval. This is not a style
   preference. Setup is judged on whether it finishes, every extra round is somewhere it can stall,
   and the questions genuinely worth asking are already listed in Phase 2.
2. **Never skip a Check.** A phase is done when its check passes, not when you ran the command.
3. **`atomic-operation.md` does not apply to setup.** That protocol governs *note* changes in `Notes/`.
   Setup touches configuration: **ordinary git commits, no `changes.jsonl` entries, no action
   classification.** Do not log setup steps to the ledger.
4. **Report honestly at the end.** Say what is configured and what still needs the user's hand.

**Shell assumption.** Commands are written for a POSIX shell. On Windows, Claude Code's Bash tool runs
through Git Bash, so they work as written.

**Your first message to the user**, before Phase 1, in two sentences: this takes about five minutes, you
will ask them three things all at once and then do the rest yourself, and nothing outside this folder
changes except one profile file in their home directory.

---

## Phase 1 — Preflight (silent)

**Goal.** You know the four facts that decide what Phase 2 asks and what Phase 3 does.

**Do.** Run this as one block. **Do not rejoin the lines into an `&&` chain** — `git config user.name`
exits non-zero when unset, which is precisely the case you are testing for, and a chain would swallow
the lines after it.

```bash
ls CLAUDE.md .claude/rules/ >/dev/null 2>&1 && echo "STRUCTURE OK" || echo "STRUCTURE MISSING"
git --version || echo "GIT MISSING"
echo "name=[$(git config user.name)] email=[$(git config user.email)]"
test -d .git && echo "CLONED" || echo "ZIP"
```

**Check.** You have read all four lines and know: the structure is OK, git exists, whether **either**
identity bracket is empty, and whether this is a clone or an unzipped copy.

**On fail.**

| Symptom | Meaning | Action |
|---|---|---|
| `STRUCTURE MISSING` | Wrong folder, or a zip unpacked one level too deep | Stop. Ask the user to confirm the folder they opened. Do not create the file. |
| `GIT MISSING` | Git is not installed | Stop. Point them at <https://git-scm.com/downloads>, then have them restart Claude Code after installing — a program installed after a session started is invisible to it. |

An empty identity bracket is **not** a failure. It is the trigger for question 1 in the next phase.

---

## Phase 2 — Ask, once

**Goal.** You have the two or three answers only the user can give.

**Ask all of them in one message, then stop and wait.** Two if their git identity is already set, three
if it is not. Number them so the reply is easy to match up.

| # | Ask | Only when | Say why, in one line |
|---|---|---|---|
| 1 | A name and an email address for Git to stamp on saved versions. | either bracket in Phase 1 was empty | It is a label on each saved version, stored on their own machine. Not an account, nothing is sent anywhere, any email is fine. |
| 2 | What they want to call their assistant. | always | They will type this name daily. Offer three suggestions **in the same message** so hesitation does not cost a round. |
| 3 | The three paragraphs about themselves they wrote before the session — paste them in. | always | It is what makes the assistant theirs: it sets how it explains things, how long its answers run, and whether it argues with them. |

**Forbidden in this phase**, all of it, without exception:

- Asking the questions one at a time.
- Any follow-up round — clarifying an answer, confirming a spelling, offering to improve their wording.
- Interviewing them about their background. Question 3 already is the interview; they did it at home.
- Asking permission to proceed. They already asked you to set this up.

**If they have no paragraphs** — they never wrote any, or they pasted the template with `[brackets]`
still in it — do **not** interview them and do **not** go round again. Put these three prompts in one
message and ask for all three answered in a single reply:

1. Who you are, what you are studying or doing, what you already know well, and what you are new to.
2. How much time you usually get in one sitting, and whether you want quick answers or thorough ones.
3. How long answers should be, how formal or casual, and whether you want to be told when you are wrong.

Whatever comes back is the profile. Write it in their words, tell them in one sentence that *"update my
profile"* changes it any time, and carry on. A thin profile that exists beats a good one that never got
written.

---

## Phase 3 — Configure and commit (silent)

**Goal.** Their answers are on disk and the working tree is clean.

**Do**, in this order:

1. **Git identity**, only if you asked question 1:

   ```bash
   git config --global user.name "Their Name"
   git config --global user.email "their@email.com"
   ```

   Do this first. Everything below ends in a commit, and a commit without an identity either fails
   loudly or quietly stamps an invented one (`them@Their-MacBook.local`) on every note they ever keep.

2. **Version history**, branching on Phase 1's last line. Both routes end the same way: a git repository
   with no remote, so there is no path by which their private notes could reach the template's repo.

   | Phase 1 said | Run | Why |
   |---|---|---|
   | `ZIP` | `git init && git branch -M main` | A zip carries no history and never had a remote. Nothing to detach. |
   | `CLONED` | `git remote remove origin` | Keeps the template's commits as backstory; every commit from here is theirs. Nothing is deleted. |

3. **Name the agent.** `CLAUDE.md` line 1 currently reads `# [Please rename your agent] — Knowledge Base
   Agent`. Put their answer to question 2 in place of the bracketed text. **Edit line 1 only.**

4. **Write their profile** to `~/.claude/CLAUDE.md` (on Windows, the same path in Git Bash). Create the
   directory if it is missing.

   **Paste their paragraphs in verbatim.** Do not rewrite them, do not tidy the grammar, do not add
   sections they did not write, do not append a note explaining what you did. Their words, their file —
   the `agent ≠ author` rule in `CLAUDE.md` covers exactly this. If the file already exists with real
   content in it, **do not overwrite**: tell them it is there, leave it alone, and move on.

5. **Commit:**

   ```bash
   git add -A && git commit -m "chore: configure vault (agent name, profile, history)"
   ```

   Without this, `atomic-operation.md` makes the agent report a dirty tree as an interrupted operation
   at the start of **every** session until something commits.

**Check.**

```bash
head -1 CLAUDE.md
test -f ~/.claude/CLAUDE.md && echo "PROFILE WRITTEN" || echo "PROFILE MISSING"
git status --short && git log --oneline -1
```

Line 1 carries their name with no square brackets, `PROFILE WRITTEN`, an empty status, one commit.

**On fail.**

| Symptom | Action |
|---|---|
| Square brackets still on line 1 | The edit did not land. Redo step 3. |
| `PROFILE MISSING` | The directory did not exist. `mkdir -p ~/.claude`, then write it again. |
| `Please tell me who you are` | Step 1 was skipped or did not stick. Set the identity, then commit. |
| `nothing to commit` | Steps 2–4 changed nothing. Verify each landed rather than moving on. |

---

## Phase 4 — Hand over

**Goal.** They know it worked and what to do next.

**Do.** Tell them these four things, briefly, in this order:

1. **Start a new chat in this same folder.** Their profile is read when a conversation begins, so the
   one they are in now has not seen it. The new chat is where it takes effect.
2. **In that new chat, ask it two questions.** *"What do you know about me?"* — the answer should
   reflect their own paragraphs back at them; generic flattery means the profile is not being read, so
   check the path. *"Which agent are you, and what are you not allowed to do?"* — a correct answer
   names Recorder and says it does not merge or split notes.

   > Do not send them to `/context` for this. It renders an interactive panel, and panel commands behave
   > differently in the desktop app's Code tab, so the instruction fails for exactly the people most
   > likely to need it. These two questions test the same thing by observing behaviour, on every surface.
3. **Open the folder in Obsidian**, via *Open folder as vault*, pointed at this directory. The vault is
   plain markdown so any editor works, but this is the reading surface it is built around: the graph,
   backlinks, and wikilinks rendered properly. It changes nothing about how the agent works.
4. **The example content belongs to somebody else.** `Notes/` and `Streams/` ship full rather than
   empty, so there is something to search and maintain from the first minute. It is a sample student's
   material, it is harmless, and it can stay as practice for as long as it is useful. When they want it
   gone: *"delete the example notes."* Nothing is deleted without their say-so.

   **`Profile/` is the exception that ships empty**, and it is worth one sentence on why. It is the only
   corpus git is told never to save — see `.gitignore` under *Personal record* — because names,
   employers, education, and visa status do not belong in a history that might one day be pushed
   somewhere. Their notes are versioned; their personal record is not. The first entry is theirs to add
   whenever they want one.

5. **Their first real move**: talk to it about something they are actually working through, then say
   *"capture that as a note."*

Then mention `NEXT.md` in one line: the optional extras — searching by meaning, and connecting Notion or
Drive — are set up there, in their own time, and nothing needs them today.

**Check.** You have said all five. Setup is done.

---

## Quick reference — what setup touches

| Path | Change |
|---|---|
| `CLAUDE.md` | Line 1 only: the agent's name |
| `~/.claude/CLAUDE.md` | Created from their paragraphs (**the only file written outside this folder**) |
| `.git/` | Created (zip route), or `origin` removed (clone route) |
| Global git config | `user.name` and `user.email`, only if they were unset |

Untouched: every file in `.claude/`, and all five content folders. Setup configures the vault; it does
not modify how it thinks.
