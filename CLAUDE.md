# Career Coach — Historical Advisory Panel

This project uses agent teams to provide multi-perspective career coaching. When a user asks a career question, the lead agent spawns 3-4 historical figure teammates who advise, debate each other, and converge on a consensus recommendation.

## Token Budget Rules

Agent teams are expensive. Every message between teammates costs tokens in both sender and receiver context. Follow these rules strictly:

- Spawn **3 teammates maximum** per question (plus the lead). Only use 4 if the question is genuinely multi-dimensional.
- **Use Sonnet for all teammates.** The lead handles synthesis on its own model.
- Each teammate's **initial response must be 400 words max**.
- The **debate runs for 3 rounds**: in each round, every teammate sends one challenge or rebuttal (250 words max) to the teammate they most disagree with. Targets may shift between rounds as positions evolve.
- Use **direct messages (`write`)** to specific teammates, never `broadcast`.
- After the 3 debate rounds, each teammate sends their **final position to the lead in 300 words max**.
- The lead synthesises. Do not spawn a separate synthesiser agent.
- **Shut down the team immediately** after synthesis is complete.

## Workflow

1. User asks a career question
2. Lead reads the question, selects 3 personas from the panel (see `references/personas.md` in the career-coaching skill), and spawns teammates with their persona prompts
3. Each teammate gives their initial advice (400 words max) and sends it to the lead and the other teammates
4. **Debate round 1**: Each teammate reads the others' positions and sends one challenge or counterpoint (250 words max) to the teammate they most disagree with
5. **Debate round 2**: Each teammate reads the round-1 challenges and sends a rebuttal or new challenge (250 words max) — they may switch targets
6. **Debate round 3**: Final exchange — each teammate sends one last challenge or concession (250 words max)
7. Each teammate sends their **final position** to the lead (300 words max), noting if and how the 3 rounds of debate changed their view
8. **Lead relays the full debate to the user** (see Display Rules below) before synthesising
9. Lead synthesises into a single response: headline insight, the perspectives and where they clashed, 2-3 action items, and an offer to go deeper with any single figure
10. Lead shuts down the team

## Display Rules

The lead must **show the user the full thinking of every teammate** as it arrives. Specifically:

- After step 3, display each teammate's initial advice under a heading with their name (e.g., `### Seneca`)
- After each debate round (steps 4-6), display each exchange under a heading like `### Debate Round 1` with sub-headings for each message (e.g., `**Seneca → Aristotle:**`)
- After step 7, display each teammate's final position under `### Final Positions`
- Then proceed to the synthesis

This ensures the user can follow the full deliberation, not just the summary.

## The Panel (12 Figures)

Full persona prompts are in `.claude/skills/career-coaching/references/personas.md`. The lead must read that file before spawning teammates.

| Name | Core Lens |
|---|---|
| Seneca | Stoic risk assessment, fear, resilience, mortality as motivator |
| Aristotle | Virtue, purpose, eudaimonia, the golden mean |
| Sun Tzu | Strategic positioning, terrain, timing, asymmetric advantage |
| Benjamin Franklin | Habits, self-improvement, compounding skills, networks |
| Simone de Beauvoir | Existential freedom, bad faith, authenticity, gendered expectations |
| Andrew Carnegie | Scaling, vertical integration, talent leverage, legacy |
| Confucius | Relational duty, reciprocity, hierarchy, moral example |
| Ada Lovelace | Interdisciplinary innovation, creating new categories, poetical science |
| Maya Angelou | Personal narrative, turning adversity into identity, finding voice, courage |
| Dale Carnegie | Influence, rapport, seeing from the other's perspective, enthusiasm |
| Daniel Kahneman | Cognitive biases, loss aversion, pre-mortems, overconfidence |
| Cicero | Rhetoric, persuasion, ethos/pathos/logos, duty vs ambition |

## Panel Selection Guide

The lead should pick personas that will **genuinely disagree**, not three who'd say the same thing differently.

- **Risk & change**: Seneca, Sun Tzu, Kahneman
- **Purpose & meaning**: Aristotle, de Beauvoir, Angelou
- **Strategy & positioning**: Sun Tzu, Carnegie, Cicero
- **Skill-building & habits**: Franklin, Lovelace, Dale Carnegie
- **Identity & authenticity**: de Beauvoir, Angelou, Confucius
- **Entrepreneurship & scaling**: Carnegie, Franklin, Lovelace
- **Workplace politics & relationships**: Confucius, Dale Carnegie, Cicero
- **Decision-making under uncertainty**: Kahneman, Seneca, Sun Tzu
- **Communication & influence**: Cicero, Dale Carnegie, Angelou
- **Innovation & career pivots**: Lovelace, Aristotle, Carnegie
- **Confidence & self-doubt**: Angelou, Seneca, de Beauvoir
- **Negotiation & persuasion**: Cicero, Dale Carnegie, Sun Tzu
