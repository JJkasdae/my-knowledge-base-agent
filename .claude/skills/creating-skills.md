# Writing a Skill Request — A Fill-in-the-Blanks Guide

*For non-technical people. You don't need to code. You just describe what you want clearly, and Claude builds the skill for you.*

## 1. First, what is a "skill"? (30 seconds)

A **skill** is a folder Claude opens when it recognizes a task it matches — like a labeled recipe card Claude pulls from a box at the right moment.

Inside the folder:

| Part | Plain meaning | Recipe-box analogy |
|---|---|---|
| **`SKILL.md`** | The instruction sheet — the main file. | The recipe card itself. |
| ↳ **frontmatter** (top of that file) | A label: *what this is* + *when to use it*. | The label on the card. |
| ↳ **body** (rest of the file) | The step-by-step how-to. | The cooking steps. |
| **`scripts/`** *(optional)* | Ready-made tools (small programs the skill can run). | A gadget clipped to the card. |
| **`references/`** *(optional)* | Extra documents Claude reads only when needed. | Notes stapled to the back. |

**The key idea:** *You don't write any of this.* You write a short **prompt** describing what you need, and Claude assembles the skill. This guide helps you write that prompt completely — so Claude doesn't have to guess.

## 2. The template — copy this, fill the blanks, paste it to Claude

> I want to create a **skill**. Here is what it should do.
>
> **1. Purpose (one sentence):**
> This skill should `_______________________________________`.
>
> **2. When should Claude use it (the trigger):**
> Use this skill whenever `_______________________________________`.
> Do *not* use it for `_______________________________________`.
>
> **3. What it receives (input):**
> The user gives it `_______________________________________`.
>
> **4. What it produces (output):**
> It should produce `_______________________________________`,
> in this format/shape: `_______________________________________`.
>
> **5. The field it's for (domain):**
> This is for `[my field — e.g. accounting / HR / teaching / real estate]`.
> It should follow these domain rules or conventions: `_______________________________________`.
>
> **6. Special rules and limits:**
> It must always `_______________________________________`.
> It must never `_______________________________________`.
>
> **7. One concrete example (optional but very helpful):**
> When I give it `_______________`, it should return `_______________`.

## 3. How to fill each blank (tips)

- **Purpose** — one plain sentence. If you need the word "and" twice, it's probably two skills.
- **Trigger** — the most important box. Describe the *situation* that should wake the skill up ("whenever I paste a customer complaint email"), and one situation where it should stay asleep. This prevents the skill firing at the wrong time.
- **Input / Output** — be concrete about the *shape*: a file? a paragraph? a list? a table? a number? Say so.
- **Domain rules** — anything an outsider wouldn't know: required wording, a standard format, a regulation, an internal naming convention.
- **Limits** — privacy lines, things it should never assume, a maximum length, a tone to avoid.
- **Example** — one real input→output pair teaches Claude more than three paragraphs of description.

## 4. Worked example (a filled-in one)

> I want to create a **skill**.
>
> **1. Purpose:** This skill should turn a messy customer complaint email into a short, structured support ticket.
>
> **2. Trigger:** Use this skill whenever I paste a customer complaint email. Do *not* use it for internal team emails or sales inquiries.
>
> **3. Input:** The user gives it the raw text of one complaint email.
>
> **4. Output:** It should produce a ticket with four fields — *Customer*, *Problem (one line)*, *Urgency (Low/Medium/High)*, *Suggested next step* — as a simple bulleted list.
>
> **5. Domain:** This is for a customer-support team. Urgency is "High" only if the customer mentions a refund, a deadline, or says they'll cancel.
>
> **6. Limits:** It must always keep the customer's exact order number if one appears. It must never invent details that aren't in the email.
>
> **7. Example:** When I give it *"Hi, order #4471 still hasn't arrived and I need it by Friday or I'm cancelling!"*, it should return — Customer: (unknown); Problem: Order #4471 not delivered; Urgency: High; Next step: Check shipping status and reply with an ETA.

## 5. What happens after you send this

You're not done in one shot, and that's normal. After you paste your filled-in request:

- **Claude may ask you 1–2 follow-up questions** to clear up anything ambiguous. Answer in plain words — there are no wrong answers.
- **Claude builds the skill and shows it to you.** You don't have to read the code; just check that it does the right thing.
- **You try it on one real example.** If the result isn't quite right, tell Claude what was off ("urgency should've been High here") and it adjusts. Skills get better by correction, not by getting it perfect the first time.

## 6. Before you hit send — 3-point check

1. Could a stranger build it from your description alone? If not, add detail.
2. Did you say *when it should NOT run*? (Half of a good trigger.)
3. Did you give *one example*? (The single biggest quality boost.)

---

> **Technical view:** see [`README.md`](README.md) for how skills fit with the always-loaded [rules](../rules/) and the anatomy of a skill folder.
