# Phase 0 — context gathering

This is what the orchestrator does **before** spawning any subagent. It's a short interactive dialog whose purpose is to assemble two clean briefs:

- a **Dreamer brief** — the goal plus the inherent properties of the problem, with constraints and personal limits stripped out
- a **full brief** — everything the Dreamer brief has, plus hard constraints, what the user has tried, the user's resources/skills, and stated preferences

The Critic and the Realist both work from the full brief. Only the Dreamer works from the stripped brief, because anything that looks like a constraint to the Dreamer becomes a self-censorship vector and you lose the lateral ideas that the method is built to produce.

## The flow

1. Read the user's initial message carefully. Catalogue what they've already told you against the seven categories below.
2. Identify the highest-priority gap. Ask **one** short, warm question about it. (Not "What's your success criterion?" Try "How would you know this worked? What's the picture in your head?")
3. Wait for the answer. Parse it. If it includes a stop signal — go to step 5.
4. Repeat steps 2–3 until either: (a) you've asked three questions, (b) the remaining gaps look low-leverage, or (c) the user signals stop.
5. Show the pre-launch summary (template at the bottom of this file). Confirm "go?" or accept corrections.
6. Build the two briefs. Hand off to Phase 1.

Cap the questions at **three** by default. If you genuinely need a fourth to do good work, fine — but it must be high-leverage.

## The seven context categories

When parsing the user's message and deciding what to ask about, evaluate against these:

| # | Category | Example | Goes to Dreamer? |
|---|----------|---------|------------------|
| 1 | **Goal / outcome** | "I want to lift trial-to-paid from 6% to 15%" | Yes — this is the brief itself |
| 2 | **Inherent problem facts** | "Cafe is 25 seats in Lisbon, mornings retirees, afternoons remote workers" | Yes — these define the problem |
| 3 | **Audience / users** | "First-year non-tech students" | Yes — this is part of the problem |
| 4 | **Hard constraints** | "Budget €8k", "must launch by Sept", "EU AI Act applies" | **No** — Dreamer would self-censor |
| 5 | **Personal resources / limits** | "I'm solo", "I don't know how to code", "I have 5 hrs/week" | **No** — Dreamer would self-censor |
| 6 | **What's been tried** | "We already did email sequences, tooltips, getting-started checklist" | **No** — Dreamer would skip the next-iteration ideas |
| 7 | **Soft preferences / qualities** | "We don't want to lose the retiree vibe", "I want it to feel premium" | **Yes** — these are characteristics of the *desired result*, not constraints on resources |

Category 7 is subtle. "We don't want to lose the retiree vibe" is a property of what the user considers a successful answer, so the Dreamer benefits from knowing it. "We don't have a marketing team" is a property of the user's resource situation, so it would self-censor the Dreamer.

A useful test: **does this fact constrain what counts as a good answer (→ keep for Dreamer), or does it constrain what the user can execute (→ strip)?**

## Question priority

When deciding which gap to ask about first, use this order:

1. **Category 1 (goal / success criterion)** — without it the Critic's "probability of success" scoring is meaningless and the Realist's plans float untethered. If this is missing, ask first.
2. **Category 4 (hard constraints)** — budget, deadline, regulation, geography. These define show-stoppers downstream. Asking now prevents 20 minutes of useless plans.
3. **Category 3 (audience)** — only ask if it's genuinely unclear. For a B2B SaaS or a cafe the audience is often implicit.
4. **Category 6 (what's been tried)** — if this is non-trivial it informs the originality scoring. Ask if the user mentioned trying things vaguely.
5. **Category 5 (resources / personal limits)** — important for the Realist, but lower-priority than goal/constraints. Often you can infer enough from what the user said. Sometimes worth asking: "Solo or with a team?" if it'll change the plan shape.
6. **Category 2 (inherent facts)** — usually filled in already by the question itself. Only ask if a key fact is missing.
7. **Category 7 (soft preferences)** — only ask if there's a strong signal the user has them. Don't probe. People feel interrogated when asked "what's your aesthetic?"

If after one round of parsing the user's message you have items 1, 2, 4 and a passing answer to 3 and 5 — that's enough. Skip ahead to the summary.

## What "ask one question at a time" means

One question per chat turn. Not three questions in one message with bullets. The reason: it's how a colleague asks. Multi-question turns feel like a form. Single questions invite real answers.

Phrase the questions warmly, in the user's language. Examples:

- "Як ти зрозумієш, що ця ідея спрацювала?"
- "What's the picture in your head if this goes really well?"
- "Чи є жорсткі обмеження — бюджет, термін, регуляторика?"
- "Are you working solo or with a team?"
- "Що ви вже пробували раніше?"

Dry, formulaic phrasing ("What is your success metric?") gets dry, formulaic answers, and the brief suffers.

## The "stop" signal

Recognize stop in any language and any phrasing. The orchestrator should look for these signals in every user reply:

- Ukrainian: "поїхали", "почали", "досить", "хватить", "достатньо", "погнали", "запускай"
- English: "go", "let's go", "skip", "skip the rest", "enough", "let's start", "start now", "move on", "just go"
- Russian: "поехали", "хватит", "достаточно", "погнали"
- Or any phrasing where the user's intent to skip is clear

A reply can contain BOTH information AND a stop signal — e.g. "€8k budget, поїхали". In that case: capture the info, then go to summary. Don't ignore the info.

If the user's reply is ambiguous (could be an answer or a skip), assume answer and continue, but don't ask another question — go to the summary.

## When the initial message is rich enough

Sometimes the user's first message answers everything across the seven categories. In that case **don't ask anything**. Skip directly to the pre-launch summary. One unnecessary question is worse than zero.

Heuristic: if you can fill in a satisfying answer for categories 1, 4, and either 3 or 5 from the initial message alone, you have enough.

## When the initial message is one sentence

Conversely, "ideas for my startup" with no other context is too thin even for one question to fix. In that case start by asking the **goal**: "Tell me a bit more — what's the startup, who's it for, and what does success look like?" This is technically a multi-part question, but it's reasonable when the prompt is genuinely empty.

## The pre-launch summary

After the Q&A, output **one** message in this shape, in the user's language. Do not skip it — even if the user said "go" right away, the summary catches misunderstandings before 20 minutes of generation are wasted.

```
Ось що я зрозумів:

**Задача:** [one sentence stating the goal]
**Контекст:** [audience, geography, domain — 1-2 lines]
**Жорсткі обмеження:** [budget, deadline, regulation — or "немає / не згадано"]
**Що вже пробували:** [list — or "не згадано"]
**Ваші ресурси:** [solo/team, skills, time — or "не згадано"]
**Критерій успіху:** [how the user will know it worked]

---

Невелике пояснення про метод: він ділиться на три режими.

- **"Мрійник"** генерує 100 ідей повністю без обмежень — саме тому,
  що інакше найкращі ідеї відсікаються самоцензурою на старті.
- **"Критик"** потім безжально оцінює кожну з 100 ідей,
  враховуючи всі ваші реальні обмеження.
- **"Реаліст"** будує план для топ-3 — конкретний і під вашу
  ситуацію, з посиланнями.

Тому на стадії генерації Мрійник НЕ побачить:
[bullet list of items moved to "full brief only" — only show items
 that actually got categorized this way; if there's nothing stripped,
 omit this whole "Тому Мрійник НЕ побачить" block]

Все це піде Критику і Реалісту, які вже працюватимуть під вашу
конкретну ситуацію.

Поїхали? Або скажіть, що додати/виправити.
```

In English the same template, with phase names kept (Dreamer / Critic / Realist) and the rest translated naturally.

The "items the Dreamer won't see" block is **only shown if there are items**. If the user gave only goal + facts + audience and no constraints/resources/tried-list, there's nothing to strip — show only the summary and "поїхали?".

## Building the two briefs

Once the user confirms (or says "go" with corrections), build:

### Dreamer brief

Concatenate categories 1, 2, 3, 7 into a clean paragraph. Phrase it as a brief, not a transcript. Drop categories 4, 5, 6 entirely. Don't even hint at them.

Example:
> "The user runs a small cafe in a dense walkable Lisbon suburb (25 seats, retirees in the morning, remote workers in the afternoon). Goal: become the kind of place people specifically tell their friends about. The desired result should preserve the existing retiree atmosphere."

That's all. No "they have €8k", no "they're new owners with no F&B background", no "they've already tried..." — none of it.

### Full brief

Everything: categories 1 through 7, in a structured form so the Critic and Realist can scan it.

```
**Goal:** [from category 1]
**Problem context:** [from category 2 + 3]
**Soft preferences:** [from category 7]
**Hard constraints:** [from category 4]
**User resources / situation:** [from category 5]
**What's been tried:** [from category 6]
**Success criterion:** [from category 1, restated explicitly]
```

Pass the Dreamer brief to the Dreamer subagent, the full brief to all five Critics and all three Realists.

## A note on edge cases

**User refuses to answer or gives "I don't know."** Capture it: "user is unsure about success criterion." For the Critic, this means probability-of-success scoring becomes more open-ended. Mention it in the summary so the user knows we're proceeding without that data point.

**User contradicts themselves.** "I'm solo" then later "my co-founder thinks…" — ask one short clarifying question if material; otherwise go with the more recent or specific statement.

**Constraint that turns into a question of its own.** "I'm solo" might be hard or soft. If material to the answer space, ask: "Is solo a hard constraint, or are you open to ideas that involve finding a partner?" That single follow-up can change the entire shape of the Realist's plans.
