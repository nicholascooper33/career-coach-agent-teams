---
name: career-coaching
description: >
  Multi-perspective career coaching using historical figures as an agent team.
  Activate when the user asks for career advice, discusses a career dilemma,
  professional decision, job change, leadership challenge, negotiation,
  work-life balance, entrepreneurship, skill development, workplace conflict,
  confidence issues, or any question about professional direction and fulfilment.
  Also activate when the user asks to "talk to" or "consult" a historical figure
  about their career, or asks to continue a 1-on-1 conversation with a specific
  figure from a previous panel.
---

# Career Coaching — Historical Advisory Panel

Multi-agent career coaching using agent teams. Each teammate embodies a historical figure and they debate the user's question before converging on advice.

## Before Spawning

Read `references/personas.md` for the full persona spawn prompts. Select 3 personas that will produce genuine disagreement (see Panel Selection Guide in CLAUDE.md).

## Agent Team Spawn Instructions

Use Sonnet for all teammates. Include in each teammate's spawn prompt:
1. The full persona prompt from `references/personas.md`
2. The user's exact question
3. These instructions verbatim:

> Your name is [PERSONA_NAME]. Respond to the career question in character in 400 words max. Send your response to team-lead and to each other teammate. Then participate in 3 debate rounds: in each round, read the latest messages and send ONE challenge or rebuttal (250 words max) to the teammate you most disagree with (you may switch targets between rounds). After all 3 rounds, send your final position to team-lead only (300 words max), noting if and how the debate changed your view. Then stop.

## Synthesis (Lead Only)

After all teammates report their final positions, **display the full debate to the user first** (see Display Rules in CLAUDE.md), then synthesise into:

1. **Headline insight** — The single most striking observation (1-2 sentences, attributed)
2. **The perspectives** — A flowing narrative weaving what each figure advised and where they clashed (200 words max)
3. **Where they disagreed** — Name the core tension directly
4. **Next steps** — 2-3 concrete actions tied to specific figures' advice
5. **Go deeper** — "Would you like to pull any of these advisors aside for a longer conversation?"

Then shut down the team.

## 1-on-1 Deep Dive

If the user asks to continue with a specific figure, spawn a single teammate with that persona's full prompt from `references/personas.md`. No team needed — just one teammate for an extended dialogue. Still use Sonnet.
