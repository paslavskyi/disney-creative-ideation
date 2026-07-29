# Gate round — between Critic and Top-3 selection

The orchestrator runs this phase **after** all 5 Critic batches have returned and **before** Top-3 selection. Its purpose: get user input on facts that materially affect scoring or planning, in a single tight round of yes/no questions, then continue.

The cost of this phase is a momentary interruption. The benefit is large: it prevents the workflow from spending an hour generating a Realist plan for the wrong idea — e.g., a high-scoring "GDPR consultant" idea that the user has no expertise to execute.

## What the Critic gives you

Each of the 5 Critic batches outputs ideas with one of three scoring patterns:

1. **Unconditional score** — most ideas. "Score: 5/10". The user-fit doesn't materially change the score. Ignore for gate purposes.

2. **Conditional score with user-fit gate** — for ideas where the user's situation makes a 3+ point difference:

   ```
   Score: 9/10 if user has GDPR / EU privacy law expertise; 3/10 otherwise.
   User-fit gate: "Do you have GDPR / EU privacy law experience?"
   ```

3. **Forward-looking operational question** — for ideas the Critic is *nominating* to advance, where a Realist plan would need a fact the brief doesn't have:

   ```
   #43 nominated. Forward-looking question for Realist:
   "Are you open to producing video content, or text-only?"
   ```

The Critic produces these naturally. Your job is to collect, dedup, prioritize, ask, and apply.

## Step 1: Collect

Read all 5 critic_batch_*.md files. From each, extract:

- All **user-fit gate** questions (with the affected idea numbers and conditional scores)
- All **forward-looking operational** questions (with the affected idea numbers)

Build a working list. Each entry:

```yaml
- question: "Do you have GDPR / EU privacy law experience?"
  type: user-fit-gate | forward-looking
  affected_ideas: [43]
  score_swing: 6  # for gates only — how many score points it shifts
```

## Step 2: Dedup

Multiple Critics may have asked variants of the same question. Group questions that mean the same thing into one. Keep the cleanest phrasing.

Common dedup patterns:
- "Do you have X expertise?" / "Are you familiar with X?" / "Background in X?" → one question
- "Solo or team?" / "Working alone?" / "Do you have a team?" → one question
- "Open to X format?" / "Comfortable with X?" → one question

When merging, sum `affected_ideas` (union) and use the maximum `score_swing`.

## Step 3: Prioritize to ≤8 questions

The cap is **8 questions total**. The 7-8 range is the cognitive sweet spot for batched yes/no — past that, it feels like a chore.

Allocation guideline:
- **Default:** ~5 user-fit gates + ~3 forward-looking
- **If gates >5:** drop forward-looking questions to make room
- **If gates ≤2:** allow up to 6 forward-looking questions

Within each category, rank by:

1. **Number of affected ideas.** A gate that affects 3 of 5 nominated ideas matters more than one affecting 1.
2. **Score swing.** A gate that swings 6 points (9→3) matters more than one that swings 2 points (7→5).
3. **Whether it affects ideas already in likely Top-3.** If you can roughly tell from raw scores which ideas are advancing, prioritize gates that touch those.

If you must drop a gate to fit the cap: for each dropped gate, the affected ideas keep their **conservative (lower)** score. This is correct behavior — without confirmation we shouldn't optimistically advance ideas.

## Step 4: Phrase the questions

Every question must be answerable in one of:
- **Yes / No** (preferred — fastest)
- **A single word or short phrase** (acceptable for choice questions)

Bad phrasings to avoid:

- "Tell me about your experience with GDPR." → too open
- "What's your time availability?" → too open; rephrase as "Can you commit 10+ hours/week? (yes/no)"
- "Are you not familiar with X?" → negative phrasing; rephrase positively

Good examples:

- "Do you have GDPR / EU privacy law experience? (yes / no)"
- "Solo, or are you open to finding a co-founder? (solo / open / either works)"
- "Comfortable being on camera in YouTube videos? (yes / no / sometimes ok)"
- "Do you have an existing email list or audience? (yes / no — rough size if yes)"

Keep questions warm, in the user's language. They're checking facts, not interrogating.

## Step 5: Ask the user

One message, in the user's language, in this shape:

```
Перш ніж обрати Топ-3, маю кілька швидких запитань — ваші відповіді
впливають на те, які саме ідеї підуть далі і як виглядатимуть плани.
Більшість — yes/no.

1. [питання 1]
2. [питання 2]
3. [питання 3]
...

Можна відповісти "не знаю" на будь-яке — я зроблю обережне припущення.
Або скажіть "досить" якщо хочете рухатись з тим, що є.
```

Not numbered with too many sub-questions, not split into bullets. Tight list, easy to scan.

In English the equivalent: "Before I pick the Top-3, a few quick yes/no questions — your answers shape which ideas advance and how the plans look. ..."

## Step 6: Process the answers

Parse the user's reply. For each question, classify the answer as:

- **Affirmative** ("yes", "так", "yep", "definitely")
- **Negative** ("no", "ні", "nope", "not really")
- **Unsure** ("не знаю", "don't know", "maybe", "?", silence in the answer list)
- **Specific value** for non-binary questions ("solo", "10 hrs", "B2C")
- **Stop signal** anywhere in the reply ("досить", "skip the rest", "go") — apply collected answers, treat unanswered as "Unsure", proceed

For each conditional-scored idea, apply the corresponding gate answer:

| Answer | Score applied |
|--------|---------------|
| Affirmative to "Do you have X?" | High score |
| Negative | Low score |
| Unsure | **Conservative — use the lower score**. Note in the Top-3 justification: "scored conservatively pending user confirmation on X" |
| Specific value | Critic's conditional should have specified — apply the matching branch |

For forward-looking operational questions, store the answer in the **enriched full brief** under a new "User clarifications (gate round)" section. The Realist will pick these up automatically.

## Step 7: Top-3 selection with updated scores

Now run the standard Top-3 tie-break (highest score → fewest show-stoppers → most unique → best fit to success criterion) using the post-gate scores. The result will likely differ from a naive raw-score Top-3 — that's the whole point.

Document the selection rationale clearly so the user can see why each idea advanced. If a previously high-scoring idea was demoted, explain: "#43 GDPR consultant scored 9/10 conditionally, but you indicated no GDPR experience, so it dropped to 3/10 and didn't advance."

## Step 8: Pass enriched brief to Realists

The Realists receive:
- The original full brief (from Phase 0)
- The Critic's evaluation of their assigned idea
- A new **"User clarifications (gate round)"** section in their input, containing every answer from this round

They do **not** start a fresh round of questions. If something's still unclear in their plan, they make an explicit assumption and note its impact ("Assuming you can commit 10 hrs/week — if less, scope reduces by ~40%").

## Skipping the gate round

If, after collecting from all 5 Critic batches, there are **zero** user-fit gates and zero forward-looking questions — skip the gate round entirely. Move straight to Top-3 selection. Don't manufacture questions to hit the cap.

This happens when the brief from Phase 0 was rich enough that nothing critical was missing. That's fine — the gate round exists to catch what Phase 0 missed, not to add ceremony.

## Edge cases

**User goes silent or unresponsive.** After a reasonable wait (or in headless test runs), treat all answers as "Unsure" and proceed. Note in the final report: "User did not respond to gate-round questions; conservative scoring applied."

**User answers reveal a Phase 0 mistake.** E.g., user says "actually I have a partner now" when the brief said "solo". Update the full brief, note the correction, proceed. Don't re-run earlier phases.

**User adds new context not asked for.** "I have GDPR experience and I'm open to consulting with a lawyer." Capture both — apply to scoring AND store the bonus context in the enriched brief.

**Question count limit forces hard tradeoffs.** Document what was dropped: "Critic surfaced 11 gates; capped at 8. Dropped: [list]. Affected ideas keep conservative scoring."
