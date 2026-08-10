# My Knowledge Base Agent

> A personal knowledge base you talk to. Workshop edition, built for the session at Victoria University.

This is a folder of plain text notes on your own computer, plus an assistant that helps you build it. You think out loud with it, and when something is worth keeping you say *"capture that"* — it writes the note, connects it to what you already wrote, and saves it. Months later you ask a question and it answers from your own notes, naming them.

**Nothing is uploaded.** Your notes are ordinary markdown files in an ordinary folder. Delete every tool involved and the notes are still there, still readable, still yours.

---

## If you are here for the workshop

**You do not need to do anything with this page yet.** We set it up together in the session, and doing it early just means you may end up with an older copy than everyone else. Come with the four programs installed and your three paragraphs written, and the rest takes about five minutes in the room.

If you want to look ahead, the short version of the whole thing is:

```
Open this folder in Claude Code, and say:

    Help me set this up.
```

It reads its own instructions, asks you a few questions only you can answer, and configures itself.

---

## What is in here

Four things matter. Everything else is machinery you can safely ignore.

| Where | What it is |
|---|---|
| `Notes/` | **Your knowledge.** One idea per file, connected by links. It starts with a few example notes so there is something to play with; delete them whenever you like. |
| `CLAUDE.md` | **Its personality and its rules of engagement.** Plain English. This is the file you edit when you want it to behave differently. |
| `.claude/rules/` | **How it decides things.** When is an idea worth its own note? When should two notes be merged? Also plain English, also yours to change. |
| `.claude/skills/` | **Extra abilities** it picks up only when needed: reading a PDF, pulling a video transcript, searching by meaning. |

The other folders (`Profile/`, `Streams/`, `Sources/`, `Templates/`) hold different kinds of content and stay empty until you need them. We cover what each one is for in the session.

---

## Two assistants, one folder

| | What it does | What it cannot do |
|---|---|---|
| **Recorder** | The one you talk to. Discusses, argues, and captures notes when you ask. | Merge or split your notes. |
| **Maintainer** | Runs when you ask it to. Reads everything and *proposes* cleanups: duplicates to merge, notes that have quietly become two ideas, themes running through your writing that you never named. | Change a single file. Every proposal waits for your yes. |

That last one is the interesting part. It reads the prose of every note at once, which is how it finds an idea you have been circling for months without ever giving it a name.

---

## The rules it plays by

- **It never writes to your vault unless you ask.** You can talk to it for an hour and nothing changes on disk.
- **It drafts; you write.** When it proposes a note, it asks you to put the key sentence in your own words. That friction is the point.
- **Every change is a saved snapshot.** You can always ask it to undo something.
- **It never deletes anything without asking you first.**

---

## After the workshop

Everything here is yours to keep and change. The most useful thing you can do next is ask the assistant about itself:

```
Read .claude/rules/substance-test.md and explain it to me
in plain words. Then tell me what to change if I want you
to create new notes more easily.
```

The rules are written in English, not code, and it can explain and edit its own. That is the whole design.

---

Built for **Make AI Practical × Victoria University**. Based on the [Knowledge Base Agent](https://github.com/JJkasdae/knowledge-base-agent) project. Licensed under the [MIT License](LICENSE) — free to use, modify, and share.
