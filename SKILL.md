---
name: disney-creative-ideation
description: "Creative idea generation and refinement using Walt Disney's three-mode thinking method — Dreamer (100 unconstrained ideas), Critic (ruthless evaluation with TRL and show-stopper taxonomy), Realist (researched implementation plans for the top 3). Use this skill whenever the user wants to brainstorm, ideate, generate creative options, explore solutions, or find innovative approaches to a problem. Trigger on phrases like 'brainstorm', 'ideate', 'I need ideas for', 'how could we approach', 'give me options for', 'creative ways to', 'Disney method', 'Walt Disney thinking', 'come up with ideas', 'explore approaches', 'придумай ідеї', 'мозковий штурм', 'брейншторм', 'як вирішити' — or whenever the user describes a challenge and wants explored options rather than one direct answer. Especially trigger when the user wants both creative breadth AND practical evaluation/refinement — not just a quick list and not just analysis of a single option."
---

# Disney Creative Ideation

Walt Disney famously kept three rooms — one for dreaming, one for planning, one for criticizing — and refused to let the modes mix. This skill turns that workflow into a multi-agent process: a **Dreamer** that generates 100 unconstrained ideas, a **Critic** that evaluates each one against a real rubric, and a **Realist** that builds a researched implementation plan for the best three.

The single most important rule: **never mix the modes**. The Dreamer that pre-censors itself is useless. The Critic that proposes new ideas is wasting cycles. The Realist that ignores the Critic's show-stoppers is in denial.

## The flow

```
Phase 0: Interactive context gathering     (orchestrator + user, dialog)
       ↓ produces: Dreamer brief (stripped) and Full brief
Phase 1: DREAMER  — 1 subagent            → 100 ideas (Dreamer brief)
       ↓
Phase 2: CRITIC   — 5 subagents (parallel) → 100 scored, conditional scores
       ↓                                     where user-fit matters,
       ↓                                     gates + forward-looking questions
[GATE ROUND]: orchestrator + user, one message of ≤8 yes/no questions
       ↓ produces: enriched Full brief with user clarifications
Top-3 selection: with post-gate scores
       ↓
Phase 3: REALIST  — 3 subagents (parallel) → researched plans for Top-3
       ↓                                     (enriched Full brief, no mid-pause)
Phase 4: Assemble and deliver final markdown report
```

The architecture is parallel-by-default. Five Critics in parallel and three Realists in parallel cuts wall-clock time roughly in half on a 100-idea run. The gate round is a single brief synchronous interruption — one user message in, one user message out, then the parallel Realist phase resumes the workflow.

If you genuinely don't have subagents available, see "Single-context fallback" below — but that's the fallback, not the plan.

## Phase 0: Interactive context gathering

This is the most important phase. Bad input here destroys everything downstream — the Realist's plans are only as good as the brief they came from.

**What you produce in Phase 0:** two briefs.

- **Dreamer brief** — the goal, the inherent problem facts, the audience, and any soft preferences about what the *result* should look like. Anything that would self-censor the Dreamer is stripped.
- **Full brief** — everything: goal, facts, audience, soft preferences, hard constraints, the user's personal resources/limits, what they've already tried.

**Why two briefs:** if you tell the Dreamer "user has €8k budget and is solo with no marketing background", the Dreamer will produce 100 ideas that fit those limits — and you'll never see "raise VC and hire a team" or "find a co-founder" or "spend $200k on a brand campaign", which might be exactly the right answers. The Dreamer's job is unbounded ideation; the Critic and Realist enforce reality afterward.

**How to run Phase 0:** see `references/context-gathering.md` for the full briefing — question priority heuristic, the seven context categories, the "stop" signal recognition, the brief-assembly rules, and the pre-launch summary template. Read it before you start asking the user anything.

**Short version of the loop:**

1. Parse the user's initial message against the seven categories.
2. Identify the highest-priority gap. Ask one short, warm question (in the user's language).
3. Repeat steps 1–2 up to ~3 questions, or until the user signals "stop", or until the remaining gaps are low-leverage.
4. Output the pre-launch summary (template in context-gathering.md). It includes a brief friendly explanation of the Dreamer/Critic/Realist split and what specifically gets stripped from the Dreamer's view — so the user understands and can correct.
5. On user confirmation, build the two briefs and proceed to Phase 1.

**The "stop" signal.** At any point the user can say "поїхали" / "go" / "skip" / "досить" / "let's start" / equivalent in their language — accept and move to the summary. A reply can include both information and a stop signal; capture both.

## Phase 1: Dreamer — spawn 1 subagent

Spawn a single subagent and pass it the contents of `references/dreamer.md` together with the task brief. The Dreamer's job is to return exactly 100 ideas as a numbered list. No commentary, no scoring, no self-censoring. One short sentence per idea.

**Subagent prompt template:**

```
You are the Dreamer in a Walt Disney three-mode creativity workflow.

Read your full briefing here: <ABSOLUTE_PATH_TO>/references/dreamer.md
Follow it strictly.

The brief — this is the ONLY context you should use. It has been
deliberately stripped of constraints and personal limits so you can
generate without self-censoring. Do not invent or assume any constraints
not in this brief:

<DREAMER_BRIEF>

Deliver exactly 100 ideas, numbered 1 to 100, one sentence each.
Save the result to: <WORKSPACE>/dreamer_output.md

Match the user's language: <USER_LANGUAGE>.
```

Pass the **Dreamer brief**, not the full brief. This is the most important orchestration rule. If you accidentally pass budget / personal limits / "what they've tried", the Dreamer self-censors and you lose the lateral ideas the method exists to produce.

Do not paraphrase the briefing into the prompt — point the subagent at the file. That keeps the orchestrator's context lean and lets you update the briefing without rewriting prompts.

When the Dreamer returns, count the ideas. If there are fewer than 100, do **not** accept it — re-run with explicit feedback ("you returned 73, I need 100; expand using the techniques in dreamer.md you skipped"). The 100 number isn't decorative. The interesting ideas typically appear after #50, once the obvious territory is exhausted.

## Phase 2: Critic — spawn 5 subagents in parallel

Split the 100 ideas into five batches of 20 (ideas 1–20, 21–40, 41–60, 61–80, 81–100). Spawn five Critic subagents **in a single message with five tool calls** so they run concurrently.

**Subagent prompt template (one per batch):**

```
You are a Critic in a Walt Disney three-mode creativity workflow.

Read your full briefing here: <ABSOLUTE_PATH_TO>/references/critic.md
Follow it strictly. Use web search whenever a claim depends on current
facts (prices, regulation, technology readiness, market sizing, existing
competitors).

The full brief — including hard constraints, the user's resources,
and what they've tried. Use these when evaluating each idea; ideas
that ignore them likely have show-stoppers:

<FULL_BRIEF>

Your batch — evaluate every one of these ideas:
<IDEAS_X_TO_Y>

Output format: as specified in critic.md. Use the asymmetric depth rule
(weak ideas: 1-2 lines; strong ideas: full rubric). End your output with
your batch's nominees for Top-3 (up to 3 ideas you'd advance, with brief
justification).

Save the result to: <WORKSPACE>/critic_batch_<N>.md

Match the user's language: <USER_LANGUAGE>.
```

**Aggregation step (orchestrator does this, not a subagent):**

When all five Critics return:
1. Concatenate their outputs in idea-number order.
2. Extract their **gates and forward-looking questions** — see the gate round below before picking Top-3.
3. After the gate round resolves, collect every idea with post-gate score 7 or higher across all five batches.
4. From that pool, pick the **final Top-3** using these tie-breakers in order: highest score → fewest show-stoppers → most unique (avoid two near-duplicate ideas in Top-3) → best fit to the user's stated success criterion.
5. If fewer than three ideas score 7+, lower the floor to 6+ and note honestly that the field was thin. Don't manufacture a Top-3 from genuinely weak ideas.

Why an explicit tie-break order: parallel Critics may anchor scores slightly differently, so a raw "highest 3" rule can produce a Top-3 dominated by one Critic's batch. The tie-break enforces diversity and integrity.

## Gate round — between Critic and Top-3 selection

If any of the 5 Critic batches produced **user-fit gates** (conditional scores that depend on a user-specific fact) or **forward-looking questions** (operational facts a Realist would need), run the gate round before selecting Top-3.

Read `references/gate-round.md` for the full mechanism. Short version:

1. **Collect** all gates and forward-looking questions from the 5 batch files.
2. **Dedupe** semantically equivalent questions across batches.
3. **Prioritize** to ≤8 questions total — gates first, then forward-looking. Within each, rank by number of ideas affected and score swing.
4. **Phrase** as yes/no or one-word answerable.
5. **Ask** the user in one tight message in their language.
6. **Apply** answers: conditional scores resolve to high or low; forward-looking answers go into a new "User clarifications (gate round)" section appended to the full brief.
7. **Then** select Top-3 with the updated scores.

If the Critic batches surfaced zero gates and zero forward-looking questions, **skip the gate round entirely** and go straight to Top-3 selection. Don't manufacture questions to fill the cap.

The gate round is the only mid-flight pause in the workflow. After the user's reply, the orchestrator does Top-3 selection and spawns the 3 Realists — no further interruptions. This is by design: the user gets one tight Q&A moment, then the system delivers.

## Phase 3: Realist — spawn 3 subagents in parallel

Spawn three Realist subagents in parallel, one per Top-3 idea. Each gets the user's task brief, the idea itself, the Critic's full evaluation of that idea (so the Realist knows exactly which show-stoppers to address), and the briefing file.

**Subagent prompt template (one per Top-3 idea):**

```
You are 