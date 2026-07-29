# Disney Creative Ideation

A [Claude Code](https://claude.com/claude-code) / Claude Agent skill for creative idea generation and refinement, built on Walt Disney's famous three-room method — one room for dreaming, one for planning, one for criticizing, never mixed. It turns that discipline into a structured multi-agent workflow: a **Dreamer** that generates 100 unconstrained ideas, a **Critic** that ruthlessly evaluates each one against a real rubric (technology-readiness levels, a show-stopper taxonomy), and a **Realist** that produces a researched implementation plan for the top 3.

## What it does

Given a problem or challenge, the skill runs a strict pipeline:

1. **Phase 0 — Context gathering.** An interactive dialogue builds two briefs: a stripped-down *Dreamer brief* (goal, facts, audience — no constraints) and a *Full brief* (adds hard constraints, resources, prior attempts).
2. **Phase 1 — Dreamer** (1 subagent). Generates exactly 100 unconstrained ideas from the Dreamer brief, with no self-censoring and no scoring.
3. **Phase 2 — Critic** (5 subagents in parallel). Splits the 100 ideas into batches of 20 and scores each against a real rubric, using the Full brief and web search where facts matter. Surfaces show-stoppers, conditional "gates," and forward-looking questions.
4. **Gate round.** If the Critics raised user-fit gates or forward-looking questions, the orchestrator asks the user one tight message (≤8 yes/no questions) before picking the Top-3.
5. **Phase 3 — Realist** (3 subagents in parallel). Builds a researched implementation plan for each of the Top-3 ideas, directly addressing the Critic's flagged show-stoppers.
6. **Phase 4 — Assembly.** Combines everything into a final Markdown report.

The architecture is parallel-by-default (5 Critics + 3 Realists run concurrently) with exactly one synchronous interruption — the gate round — by design.

## When to use it

Reach for it whenever you want a challenge *explored* rather than answered with a single suggestion — brainstorming, ideating, or looking for innovative approaches to a problem. It's especially suited to requests that need both creative *breadth* (a wide, unconstrained set of options) and practical *evaluation/refinement* (which of those options actually survive contact with reality) — not just a quick list, and not just an analysis of one option in isolation.

In an agent setup, this maps to prompts like "brainstorm", "I need ideas for...", "how could we approach...", "give me options for...", "creative ways to...", or "explore approaches" (also recognized in Ukrainian: "придумай ідеї", "мозковий штурм", "як вирішити"). Claude picks up the skill automatically when a request matches this shape — no need to name it explicitly.

## Contents

| File | Purpose |
|---|---|
| `SKILL.md` | The orchestrator's main instructions: the full phase-by-phase flow, subagent prompt templates, and aggregation/tie-break rules. |
| `references/context-gathering.md` | Phase 0 briefing: question-priority heuristic, the seven context categories, the "stop" signal, brief-assembly rules, pre-launch summary template. |
| `references/dreamer.md` | Full briefing for the Dreamer subagent — how to generate 100 unconstrained ideas. |
| `references/critic.md` | Full briefing for the Critic subagents — scoring rubric, show-stopper taxonomy, asymmetric depth rule, gates and forward-looking questions. |
| `references/gate-round.md` | Mechanism for collecting, deduping, prioritizing, and resolving gate-round questions between Critic and Top-3 selection. |
| `references/realist.md` | Full briefing for the Realist subagents — how to turn a Top-3 idea and its Critic evaluation into a researched implementation plan. |
| `assets/final_report_template.md` | Template for the final assembled Markdown report delivered to the user. |

## Installation

Drop the `disney-creative-ideation/` folder into your Claude Code skills directory (or package it as a plugin skill) so it's discoverable by name. See the [Claude Code skills documentation](https://docs.claude.com/en/docs/claude-code) for the current installation mechanism.
