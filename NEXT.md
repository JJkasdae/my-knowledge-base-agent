# NEXT.md — after the workshop

Two halves.

**Part one is three things to do**, in order, and the first one takes fifteen minutes.
**Part two is a reference** — the things there was no time to explain in the room. Do not
read it front to back. Come here when you hit one of them.

**Every entry in part two is written the same way: what it is, and then a question to ask
your assistant.** That is deliberate. The explanations here are short on purpose, because
the fastest way to understand any of this is to have it explained to *you*, at your level,
by something that has read the actual files. Paste the question. Argue with the answer.

**Do not work through this by hand.** Open this folder in Claude Code and say:

```
Read NEXT.md and help me with number 2.
```

---

# Part one — the three things

## 1. Interview your agent about itself

**Free, takes fifteen minutes, and it is the one that changes how you use everything else.**

The rules this assistant follows are written in English, in files you own, and it can read
and explain its own. Reading the documentation is the slow way. Asking is the fast way.

Paste these one at a time, in a normal chat:

```
Read .claude/rules/substance-test.md and explain it to me in plain
words. Then tell me what to change if I want you to create new notes
more easily.
```

```
Read CLAUDE.md and tell me the three things you are not allowed to do.
```

```
What is the difference between Notes/, Profile/, Streams/ and Sources/?
Give me one example of something of mine that would go in each.
```

```
Read .claude/rules/actions.md. Which of the five actions can you do,
and which ones only the Maintainer can?
```

When an answer tells you the rule is not what you want, say so and ask it to change the
rule. That is not a hack; it is the design. The files are yours.

## 2. Turn on real search

Right now, search matches the words you typed. With this on, a note about *"keeping my
options open"* surfaces when you later ask about *"flexibility"*.

**It costs a small amount of money and takes about twenty minutes.** Everything works
without it. The concepts underneath every step here — virtual environments, dependencies,
environment variables — are explained in **[part two](#search-and-the-machinery-under-it)**.

### What your agent does

1. **Check for `uv`** — `uv --version`. Missing? Install it, then **quit Claude Code and
   your terminal completely and reopen.**

   | OS | Install |
   |---|---|
   | macOS (Homebrew) | `brew install uv` |
   | macOS / Linux | `curl -LsSf https://astral.sh/uv/install.sh \| sh` |
   | Windows (PowerShell) | `powershell -c "irm https://astral.sh/uv/install.ps1 \| iex"` |

   > **The restart is not superstition.** A terminal works out where programs live at the
   > moment it opens. One that was already running cannot see a tool installed afterwards.

2. **Build the environment.**

   ```bash
   uv venv .venv && uv pip install -r requirements.txt && cp .env.example .env
   ```

   > **It must be named exactly `.venv`.** The `rag-search` skill resolves its interpreter
   > relative to that name. Any other name and search silently falls back to word matching —
   > no error, just worse results.

3. **Configure `.env`.**

   | Mode | Set | Reality |
   |---|---|---|
   | **Offline** | `EMBED_PROVIDER=mock` | No key, no cost, no network. Proves the wiring. **Not real search** — consistent but meaningless results. |
   | **Live** | `EMBED_PROVIDER=api` | Real meaning-based search. Needs an account and a key. |

   Every variable in that file is explained in part two.

4. **Build the index.**

   ```bash
   PY=$([ -x .venv/bin/python ] && echo .venv/bin/python || echo .venv/Scripts/python.exe)
   "$PY" .claude/skills/rag-search/scripts/reindex.py auto
   ```

   Refreshed **manually**. Ask for a reindex after you have written a batch of new notes.

### Getting an API key, if you do not have one

The step most likely to stop you. Read it before you start.

| Point | What it means |
|---|---|
| **What you are paying for** | Turning each note into a numeric fingerprint. Only that. Charged when the index is built or refreshed, never while you type, read, or ask. |
| **Roughly what it costs** | Fingerprinting is far cheaper than having a model write or reason. For one person's notes a small top-up lasts a long time. Check the provider's pricing page. |
| **A subscription is not a key** | ChatGPT Plus, and equally a paid Claude plan, include **nothing** here. API access is a separate account with a separate bill. Most common misunderstanding by a distance. |
| **Money before key** | At <https://platform.openai.com> → Settings → Billing, add a payment method and buy a small amount of credit *first*. **On a zero balance the key exists but every request is refused, and the error does not mention the balance.** |
| **Auto-recharge** | Leave it off unless you want it. Worst case is then that it stops working, never a surprise bill. |
| **Make the key** | <https://platform.openai.com/api-keys> → **Create new secret key**. Shown once — copy it immediately. |
| **Handle it like a card** | Starts with `sk-`. Belongs in `.env` and nowhere else: never an email, a screenshot, or a chat window. |

**If it says `RAG_UNAVAILABLE`**, three causes in order of likelihood: the environment is not
named exactly `.venv`; `.env` is missing or says `api` with no key; a value in `.env` is
wrong. Word search keeps working throughout — this is narrower, not broken.

## 3. Teach it a routine of your own

There is a difference between what the assistant *can* do and the **shape** you want it done
in: which files, how they get broken up, what ends up in `Notes/` and what gets thrown away.
That part is different for everybody, which is why this repo does not ship an opinion.

A **skill** is where you write that shape down once so you never explain it again. It is a
folder with a markdown file in it, in the same plain English as everything else here.

You do not have to write it yourself:

```
Read .claude/skills/creating-skills.md, then interview me about how I
want my lecture notes filed in this vault. Write the skill.
```

It asks what it needs, writes the file, and from then on *"file these lecture notes"* runs
your procedure rather than improvising a new one. Same trick for anything you explain twice:
a weekly review format, how you like meeting notes structured, how to pull a reading list
out of Notion.

**This is the point at which the thing stops being software you were handed.**

## And once you have some notes — run the Maintainer

Around fifteen notes is when this gets interesting.

```
Run the Maintainer over my notes.
```

It reads everything at once and proposes: two notes saying the same thing, one note that has
quietly become two ideas, and — the good one — a theme running through several notes that
you never named. It changes nothing. Every proposal waits for your yes.

---

# Part two — when you want to understand it

<a id="search-and-the-machinery-under-it"></a>

## Search, and the machinery under it

### What RAG actually is

Your assistant cannot read two hundred notes every time you ask a question. So instead:
every chunk of every note is turned into a long list of numbers that encodes **what it
means**. That list is called an *embedding*, or a fingerprint. Your question gets the same
treatment. Then it finds the chunks whose numbers sit closest to your question's numbers.

Closeness in numbers turns out to track closeness in meaning. That is the whole trick, and
it is why a note about *"keeping my options open"* comes back when you ask about
*"flexibility"* — no shared words at all.

Chunks rather than whole notes, because a note usually holds several ideas and you want the
one that matches, not the file that happens to contain it.

**Ask it:**

```
Explain how the rag-search skill in this folder works, from the moment
I ask a question to the moment I get an answer. I have no technical
background — use an everyday comparison, and tell me what happens on
my computer versus what leaves it.
```

### Why the index has to be rebuilt by hand

Making fingerprints costs money and takes time. Doing it after every keystroke would be
absurd. So the index is a snapshot: accurate as of the last reindex, blind to anything you
wrote since. Say *"reindex my notes"* after a batch of writing.

**Ask it:**

```
What happens if I search before reindexing? Will it miss my new notes
silently, or tell me?
```

### Virtual environments — and why the folder must be `.venv`

Python installs libraries for the whole computer by default. Two projects that want
different versions of the same library then fight, and the loser breaks. A **virtual
environment** is a private folder of libraries belonging to one project only. Delete
`.venv` and nothing else on your machine changes.

The exact name matters here for one reason: the `rag-search` skill looks for the Python
interpreter at that exact path. Call it something else and there is no error — search just
quietly stops being smart.

**Ask it:**

```
What is inside the .venv folder in this project, and what would break
if I deleted it? Explain it like I have never installed Python.
```

### Dependencies — the three things in `requirements.txt`

A dependency is code somebody else wrote that this project needs in order to run. This
project needs exactly three:

| | |
|---|---|
| **`lancedb`** | Where the fingerprints live. A database that is just files on your disk — nothing is running in the background, nothing is on a server. |
| **`openai`** | The client used to call the fingerprinting endpoint. It works with any OpenAI-compatible provider, not only OpenAI. |
| **`python-dotenv`** | Reads your `.env` file so the scripts can see the settings in it. |

**Ask it:**

```
Read requirements.txt and tell me what each package does and whether
any of them sends my notes anywhere.
```

### Environment variables, `.env`, and `.env.example`

An **environment variable** is a setting handed to a program when it starts, kept outside
the program's own code. A `.env` file is just a plain list of them.

Two reasons this pattern exists everywhere:

1. **Secrets must never be in code or in saved history.** A key in a source file gets
   committed, pushed, and then it is public forever.
2. **The same code runs differently on different machines** without anybody editing the
   code — your key, your model, your settings.

That is why there are two files:

| | |
|---|---|
| **`.env.example`** | The template. Tracked in git. Every variable listed, **no secrets**. This is the file that tells you what settings exist. |
| **`.env`** | Yours. Gitignored, so it never enters your saved history and never gets pushed anywhere. You made it by copying the example. |

**Ask it:**

```
Show me the difference between .env and .env.example in this project,
and prove to me that my API key cannot end up in git history.
```

### Every variable in `.env`, one by one

| Variable | What it does |
|---|---|
| `EMBED_PROVIDER` | `mock` = offline, fake but consistent fingerprints, useful only to prove the wiring works. `api` = real. |
| `EMBED_BASE_URL` | Which endpoint to call. OpenAI by default; any OpenAI-compatible provider works by changing this. |
| `EMBED_API_KEY` | Your key. The only secret in the file. |
| `EMBED_MODEL` | Which fingerprinting model. |
| `EMBED_DIM` | How many numbers per fingerprint. More is finer-grained, larger on disk, slower. |
| `EMBED_SEND_DIMENSIONS` | `true` for OpenAI v3 models, which can shorten a fingerprint on request. **`false` for everyone else — and then `EMBED_DIM` must exactly equal that model's native dimension**, or it fails. |
| `SEARCH_TOP_K` | How many hits a search returns. Default 5. |
| `SEARCH_MAX_ROUNDS` | How many rounds of searching the assistant may do before it must stop and answer you. Default 3. |

**The last two are yours to tune and need no code change.** Getting shallow answers? Raise
`SEARCH_TOP_K`. Feels like it gives up too early on hard questions? Raise
`SEARCH_MAX_ROUNDS`. It is a text file. Change it and see.

**Ask it:**

```
I keep getting answers that miss notes I know I wrote. Look at my
.env and tell me which setting to change and what the trade-off is.
```

---

## The folder that makes it think

### The rules — `.claude/rules/`

Seven files, always loaded, plain English. They are what stops the assistant improvising a
different standard every time.

| File | Governs |
|---|---|
| `substance-test.md` | Does this idea deserve its own note, or a link? The single criterion the others reuse. |
| `actions.md` | The five things that can happen to your notes, and the test each must pass. |
| `single-source-of-truth.md` | Detecting and resolving the same thing written twice. |
| `linking.md` | When a term becomes a `[[link]]` and when it stays plain text. |
| `atomic-operation.md` | The write protocol: edit, log, commit — all three or none. |
| `log-schema.md` | The format of the ledger every change is recorded in. |
| `templates.md` | Which template a new note gets. |

**These are the customisation surface.** If the assistant creates too many notes, too few,
links too eagerly, or is too cautious about merging — the fix is a sentence in one of these
files, not a workaround in how you talk to it.

**Ask it:**

```
Read every file in .claude/rules/ and give me a one-line summary of
each, then tell me which single file I should change if I think you
are too reluctant to create new notes.
```

```
I want you to be more willing to link concepts together. Show me the
exact line you would change in linking.md, and what it would mean in
practice.
```

### Permissions — `.claude/settings.json`

Every time an assistant wants to run a command on your computer, it should ask. This folder
comes pre-configured so that it mostly does not — that was a decision for the workshop, so
twenty-five people were not clicking *Allow* every thirty seconds.

Inside you will find an **allow** list (things it may do without asking) and a **deny** list
(things it may never do, even if you say yes). Open a different folder in Claude Code and
you will be asked about everything, which is the normal experience and will now feel strange.

**This is yours to tighten.** If handing it that much freedom in your notes folder makes you
uncomfortable, narrow the allow list. Nothing here is load-bearing.

**Ask it:**

```
Read .claude/settings.json and explain what I have allowed you to do
without asking, and what I have forbidden outright. Then tell me what
you would recommend changing for someone who wants to be more careful.
```

### The ledger — `.claude/log/changes.jsonl`

**It will not exist until your first capture.** The folder ships empty; the file appears the
first time a note is written.

**`.jsonl` means one record per line.** Not one big document — a list, where each line is a
self-contained entry. That shape exists so a new record can be added to the end without
rewriting or re-reading everything before it. It looks like code when you open it. It is
not; it is a list of events.

One line records: **when**, which assistant, which action, which notes, **the reasoning
behind it**, whether it went ahead or was rejected, and — when you said no — **your own
reason in your words**.

**It is append-only.** Entries are never edited and never removed. A record you can go back
and change is not an audit trail, so this one is not changeable. If something was wrong, a
new line says so; the old line stays.

**The same event is recorded twice, on purpose.** Every entry here has exactly one matching
Git commit, whose message carries the short version:

```
[executed] Creation: Weekly Review | first note on the Friday habit
```

Git history is the readable version you scroll through. The ledger is the complete version,
carrying the full reasoning the commit message has no room for.

**Only `Notes/` is logged.** Changes to `Profile/`, `Streams/` or `Sources/` do not appear
here — those folders are still bound by single source of truth, but the ledger and the
action machinery cover the concept notes alone.

Two uses. The obvious one:

```
Read .claude/log/changes.jsonl and summarise everything that has
happened to my notes so far, in plain English, oldest first.
```

And the one actually worth doing, once you have said no to a few things:

```
Read .claude/log/changes.jsonl and find every proposal I rejected.
For each one, tell me what my reason was, and whether you think I was
right.
```

That second one is where `lessons.md` below comes from.

### Learned lessons — `.claude/maintainer/lessons.md`

When you reject a proposal and your reason reveals a **repeatable pattern** rather than a
one-off, it gets written down here as a lesson, and the Maintainer reads it before proposing
anything in future.

This is why saying *why* you said no is worth the extra ten seconds.

**Ask it:**

```
Read .claude/maintainer/lessons.md and tell me what you have learned
from my decisions so far.
```

### Templates — `Templates/`

Two ship: a general one, and one for notes made from a PDF. When a note is created, the
assistant matches the shape of what you are capturing against the templates and picks one,
falling back to the general template.

Add your own by putting a markdown file in that folder.

**Ask it:**

```
Look at the notes I have written and tell me whether any of them share
a shape that should become a template. If so, draft it.
```

### Adding a corpus

`Notes/`, `Profile/`, `Streams/`, `Sources/` are not hard-coded anywhere. They are **rows in
a table in `CLAUDE.md`**, and each row answers two questions: is this bound by single source
of truth, and does the action machinery run on it?

A fifth is one more row. Two ways to do it, and both are legitimate:

- Open `CLAUDE.md` and write the row yourself.
- Say: *"add a corpus for my recipes and tell me what rules it should follow."*

**Ask it:**

```
I want to add a folder for things I am tracking that are not notes and
not facts about me. Read the corpus table in CLAUDE.md, tell me what
questions I need to answer to define it properly, and then write the row.
```

---

## Git — you installed it, nobody explained it

### What it is actually doing

Every time your notes change, Git takes a labelled snapshot first. That is the entire reason
you cannot break this by accident.

| Term | Meaning |
|---|---|
| **commit** | One snapshot, with a message saying what changed and why. |
| **history** | Every commit, in order. `git log --oneline` lists them. |
| **working tree** | Your files as they are right now, which may differ from the last snapshot. |

**You do not need to operate it.** The fastest way to undo something is to say so:

```
Undo the last change you made to my notes.
```

**Ask it:**

```
Explain Git to me as if I have never used it, using this folder as the
example. Then show me the last five things that happened to my notes.
```

### `.gitignore` — what never gets saved

A list of things Git must ignore. In this project it includes `Profile/*` and `Sources/*`,
which is why your personal record and your original files stay on your machine and never go
anywhere.

**Ask it:**

```
Read .gitignore and tell me exactly which of my files are not being
saved into history, and why each one is excluded.
```

### Backing it up

Setup deliberately leaves this folder with **no remote** — nowhere to push to — so your notes
cannot accidentally end up back in the template's repository.

When you want a backup, add your own:

1. Create a repository on GitHub. **Private.** If you make it public, your notes are public.
2. ```bash
   git remote add origin https://github.com/YOURNAME/YOURREPO.git
   git push -u origin main
   ```

**One thing that catches people.** `Profile/` and `Sources/` are gitignored, so a Git backup
**does not back them up**. If those matter to you, keep the whole folder somewhere that syncs
files — iCloud, Dropbox, an external drive — and let Git handle the notes.

**Ask it:**

```
I want to back this vault up privately. Walk me through it step by
step, and tell me what will not be included and what I should do about
that.
```

---

## Connecting outside services — MCP

### What it is

**MCP** is a shared standard for letting an assistant talk to an outside service. The
service publishes a small server; you tell Claude where that server is; from then on the
assistant can use that service's tools directly, without you copying anything in and out.

Notion, Google Drive, Slack, GitHub, Figma and plenty of others publish one.

### Finding one

Search for **"Notion MCP server"** — substitute your service. What you want is the official
one, and the addresses for those are listed at <https://claude.ai/directory>. Look it up
rather than guessing a URL.

### Adding it

**In the desktop app**, which is what most of you have: **Settings → Connectors**, find the
service, follow the sign-in. Graphical, no commands.

**On the command line**, if you use the `claude` CLI:

```bash
claude mcp add --transport http notion https://mcp.notion.com/mcp
```

Left to right: add a connector, it talks over http, call it `notion`, here is its address.
Only the last two change per service. `claude mcp remove notion` undoes it. `claude mcp list`
shows what you have.

> The desktop app ships Claude Code **without** installing the `claude` command. If those
> commands say *command not found*, that is expected — use Settings → Connectors instead.

### What signing in actually grants

Adding a connector records *where* the service is. It grants no access at all. The sign-in,
in your browser, is what grants access — and what it grants is real: the assistant can read
that account, and often write to it.

**Connect only the services you are comfortable with it seeing.** You can remove a connector
at any time, and revoke access from the service's own settings page.

### Then decide what it should do

Connecting Notion gives the assistant the ability. Deciding what it should *do* with that
ability — which pages, broken up how, ending where — is a **skill**, which is item 3 in part
one.

**Ask it:**

```
I have connected Notion. Look at what tools you now have available and
tell me, in plain English, what you can and cannot do with my Notion
account.
```

---

## Skills — what you have, and writing your own

A rule is always loaded. A skill sits on disk until something you say matches it. That is
how the assistant can know how to do a dozen specialised jobs without carrying all of them
around in every conversation.

**Eight ship with this folder.**

| Skill | What it does | Needs |
|---|---|---|
| `pdf-to-notes` | A PDF becomes a source note plus linked concept notes. The original is kept in `Sources/`. | — |
| `youtube-transcript` | Pulls a video's transcript, corrects the caption errors, works from that. | `uv` |
| `defuddle` | A web page stripped of navigation and adverts, kept as clean text. | Defuddle CLI |
| `rag-search` | Meaning-based search, and the reindex that keeps it current. | setup in part one |
| `obsidian-markdown` | Writes Obsidian-flavoured markdown correctly: wikilinks, callouts, embeds, properties. | — |
| `obsidian-bases` | `.base` files — database-like table and card views over your notes, with filters and formulas. | — |
| `json-canvas` | `.canvas` files — visual canvases, mind maps, flowcharts. | — |
| `maintenance-actions` | The Maintainer's judgement. **Not invoked by you** — it is loaded into the sub-agent. | — |

The last three Obsidian ones are published by Obsidian themselves, open source. We only
downloaded them.

**Two to try tonight:**

```
Read this PDF and capture what matters: <drag the file in>
```

```
Summarise this and capture anything worth keeping: <youtube url>
```

The rest are here for when you want them. Ask what a skill does before you use it — that is
what the description at the top of each `SKILL.md` is for, and it is one sentence.

### Writing your own

`.claude/skills/creating-skills.md` ships with this folder. It is the guide the assistant
reads when you ask it to write a skill — which means you do not have to read it yourself.

A skill is a folder with a `SKILL.md` inside it. The top of that file says **what the skill
is for and when to use it** — that description is the only part always in memory, and it is
what decides whether the skill gets picked up at all. The rest is your procedure.

**Ask it:**

```
Read .claude/skills/creating-skills.md and explain what makes a good
skill versus a bad one. Then look at how I have been working with you
over the last few weeks and suggest one I should write.
```

The honest test for whether something should be a skill: **have you explained it twice?** If
yes, write it down once instead.
