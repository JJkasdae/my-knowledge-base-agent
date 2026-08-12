# NEXT.md — after the workshop

> **Nothing in this file is required.** Setup is finished and the vault works. Three things are worth
> doing next, in this order, and two more are here for when you want them.

**Do not work through this by hand.** Open this folder in Claude Code and say:

```
Read NEXT.md and help me with number 2.
```

It reads the section, asks you only what it cannot work out itself, and does the rest.

---

## 1. Interview your agent about itself

**Free, takes fifteen minutes, and it is the one that changes how you use everything else.**

The rules this assistant follows are written in English, in files you own, and it can read and explain
its own. Reading the documentation is the slow way to understand that. Asking it is the fast way.

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

When an answer tells you the rule is not what you want, say so and ask it to change the rule. That is
not a hack; it is the design. The files are yours.

---

## 2. Turn on real search

Right now, search matches the words you typed. With this on, a note about *"keeping my options open"*
surfaces when you later ask about *"flexibility"*. Three things lean on it: answering questions from
your notes, catching a duplicate you wrote in different words, and the Maintainer finding two notes that
say the same thing.

**It costs a small amount of money and takes about twenty minutes.** Everything works without it.

### What your agent does

1. **Check for `uv`** — `uv --version`. If it is missing, install it, then **quit Claude Code and your
   terminal completely and reopen**. A terminal works out where programs live when it opens, so one that
   was already running cannot see a newly installed tool.

   | OS | Install |
   |---|---|
   | macOS (Homebrew) | `brew install uv` |
   | macOS / Linux | `curl -LsSf https://astral.sh/uv/install.sh \| sh` |
   | Windows (PowerShell) | `powershell -c "irm https://astral.sh/uv/install.ps1 \| iex"` |

2. **Build the environment.** The directory must be named exactly `.venv` — the `rag-search` skill
   resolves its interpreter relative to that name, and any other name means a silent fall back to word
   search.

   ```bash
   uv venv .venv && uv pip install -r requirements.txt && cp .env.example .env
   ```

3. **Configure `.env`.** Two modes:

   | Mode | Set | Reality |
   |---|---|---|
   | **Offline** | `EMBED_PROVIDER=mock` | No key, no cost, no network. Proves the wiring works. **Not real search** — results are consistent but meaningless. |
   | **Live** | `EMBED_PROVIDER=api` | Real meaning-based search. Needs an embeddings account and an API key. |

   Live mode also needs `EMBED_BASE_URL`, `EMBED_API_KEY`, `EMBED_MODEL`, `EMBED_DIM`. OpenAI defaults
   are already in `.env.example` and are a fine starting point. `.env` is gitignored, so a key put there
   never enters your saved history. You can paste the key into the chat or put it in the file yourself —
   if you do it yourself, the assistant never sees it.

4. **Build the index.**

   ```bash
   PY=$([ -x .venv/bin/python ] && echo .venv/bin/python || echo .venv/Scripts/python.exe)
   "$PY" .claude/skills/rag-search/scripts/reindex.py auto
   ```

   The index is refreshed **manually**. Ask for a reindex once you have written a batch of new notes.

### Getting an API key, if you do not have one

This is the step most likely to stop you, so it is worth reading before you start.

| Point | What it means |
|---|---|
| **What you are paying for** | Turning each note into a numeric fingerprint so meaning-matching works. Only that. Charged when the index is built or refreshed, never while you type, read, or ask. |
| **Roughly what it costs** | Fingerprinting is far cheaper than having a model write or reason. For one person's notes, a small top-up lasts a long time. Check the provider's pricing page for the current rate. |
| **A subscription is not a key** | ChatGPT Plus, and equally a paid Claude plan, include **nothing** here. API access is a separate account with a separate bill. This is the most common misunderstanding. |
| **Money before key** | At <https://platform.openai.com> → Settings → Billing, add a payment method and buy a small amount of credit *first*. On a zero balance the key exists but every request is refused, and the error does not mention the balance. |
| **Auto-recharge** | Leave it off unless you want it. Worst case is then that it stops working, never a surprise bill. |
| **Make the key** | At <https://platform.openai.com/api-keys> → **Create new secret key**. Defaults are correct. It is shown once — copy it immediately. |
| **Handle it like a card** | It starts with `sk-`. It belongs in `.env` and nowhere else: never an email, a screenshot, or a chat window. |

Any OpenAI-compatible provider works; only the address and the model name change. One trap:
`EMBED_SEND_DIMENSIONS=true` suits OpenAI v3 models. For any other provider set it `false`, and then
`EMBED_DIM` must exactly match that model's own dimension.

**If it says `RAG_UNAVAILABLE`**, three causes in order of likelihood: the environment is not named
exactly `.venv`; `.env` is missing, or says `api` with no key; a value in `.env` is wrong. Word search
keeps working throughout, so this is narrower, not broken.

---

## 3. Teach it a routine of your own

There is a difference between what the assistant *can* do and the **shape** you want it done in: which
files, how they get broken up, what ends up in `Notes/` and what gets thrown away. That part is
different for everybody, which is why this repo does not ship an opinion about it.

A **skill** is where you write that shape down once so you never have to explain it again. It is a
folder with a markdown file in it, in the same plain English as everything else here.

You do not have to write it yourself:

```
Read .claude/skills/creating-skills.md, then interview me about how I
want my lecture notes filed in this vault. Write the skill.
```

It asks what it needs, writes the file, and from then on *"file these lecture notes"* runs your
procedure rather than improvising a new one. The same trick works for anything you find yourself
explaining twice: a weekly review format, how you like meeting notes structured, how to pull a reading
list out of Notion.

This is the point at which the thing stops being software you were handed and starts being yours.

---

# Two more, whenever you want them

## Run the Maintainer, once you have some notes

Around fifteen notes is when this gets interesting. Say:

```
Run the Maintainer over my notes.
```

It reads everything at once and comes back with proposals: two notes saying the same thing, one note
that has quietly become two ideas, and — the good one — a theme running through several notes that you
never named. It changes nothing. Every proposal waits for your yes.

## Connect Notion, Drive, or Slack

Worth doing only if your notes already live somewhere else. Nothing depends on it.

The Claude desktop app has a graphical setup flow: **Settings → Connectors**, find your service, follow
the sign-in. If you use the `claude` command-line tool instead, your agent can add it directly:

```bash
claude mcp add --transport http notion https://mcp.notion.com/mcp
```

Left to right: add a connector, it talks over http, call it `notion`, here is its address. Only the last
two change per service. Addresses come from <https://claude.ai/directory> — look the service up rather
than guessing a URL. `claude mcp remove <name>` undoes it.

**The sign-in is yours either way.** Adding a connector records *where* the service is; it grants no
access. Signing in lets the assistant read that account, so connect only the services you are
comfortable with it seeing.

Connecting it gives the assistant the ability. Deciding what it should *do* with that ability is
number 3 above.

---

## Things it can already do, no setup needed

| Say this | What happens |
|---|---|
| *"Read this PDF and capture what matters."* | Stores the original in `Sources/`, writes a source note, and breaks the document into linked concept notes. |
| *"Summarise this YouTube video: <url>"* | Pulls the transcript, corrects the caption errors, and works from that. |
| *"Read this page and capture it: <url>"* | Strips the navigation and adverts, keeps the article. |
