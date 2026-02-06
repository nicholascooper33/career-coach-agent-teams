# Career Coach — Historical Advisory Panel

A multi-agent career coaching system for [Claude Code](https://docs.claude.com/en/docs/claude-code) that uses **Agent Teams** to spawn historical figures as AI teammates. They advise on your career question independently, debate each other's positions, and converge on a consensus recommendation.

Ask a question like *"Should I leave my law firm to build a legal tech startup?"* and get genuinely different perspectives from Seneca, Ada Lovelace, and Daniel Kahneman — who will argue with each other before giving you their final advice.

## The Panel

| Figure | Era | Core Lens | Best For |
|---|---|---|---|
| **Seneca** | 4 BC–65 AD | Stoic pragmatism | Risk, fear, resilience, mortality as motivator |
| **Aristotle** | 384–322 BC | Virtue ethics | Purpose, flourishing, the golden mean |
| **Sun Tzu** | ~544–496 BC | Military strategy | Positioning, timing, competitive advantage |
| **Benjamin Franklin** | 1706–1790 | Self-improvement | Habits, compounding skills, networks |
| **Simone de Beauvoir** | 1908–1986 | Existentialism | Freedom, authenticity, bad faith, identity |
| **Andrew Carnegie** | 1835–1919 | Industrial capitalism | Scaling, talent leverage, legacy |
| **Confucius** | 551–479 BC | Relational ethics | Duty, reciprocity, hierarchy, moral example |
| **Ada Lovelace** | 1815–1852 | Interdisciplinary innovation | Creating new categories, "poetical science" |
| **Maya Angelou** | 1928–2014 | Personal narrative | Adversity → identity, finding voice, courage |
| **Dale Carnegie** | 1888–1955 | Interpersonal influence | Rapport, persuasion, people skills |
| **Daniel Kahneman** | 1934–2024 | Behavioural psychology | Cognitive biases, loss aversion, pre-mortems |
| **Cicero** | 106–43 BC | Rhetoric & civic duty | Persuasion, ethos/pathos/logos, duty vs. ambition |

## Prerequisites

- **Claude Code** installed and authenticated ([installation guide](https://docs.claude.com/en/docs/claude-code))
- **Claude Pro, Max, or API key** — Agent Teams consume significantly more tokens than single sessions (each teammate is a full Claude instance). Max plan recommended.
- **Claude Code v2.1.32+** — Agent Teams were introduced in this version.

## Setup

### 1. Clone the repository

```bash
git clone https://github.com/YOUR_USERNAME/career-coach.git
cd career-coach
```

### 2. Verify the file structure

After cloning, you should see:

```
career-coach/
├── CLAUDE.md                                        # Project config, token rules, panel guide
├── README.md                                        # This file
├── LICENSE                                          # MIT licence
├── .claude/
│   ├── settings.json                                # Enables Agent Teams
│   ├── commands/
│   │   └── careercoach.md                           # /careercoach slash command
│   └── skills/
│       └── career-coaching/
│           ├── SKILL.md                             # Trigger, workflow, synthesis format
│           └── references/
│               └── personas.md                      # Full persona prompts (all 12 figures)
```

### 3. Enable Agent Teams

The included `.claude/settings.json` already enables Agent Teams:

```json
{
  "env": {
    "CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS": "1"
  }
}
```

If you prefer to set it globally (across all projects), add it to your shell profile instead:

```bash
# Add to ~/.bashrc, ~/.zshrc, or equivalent
export CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1
```

### 4. Launch Claude Code from the project directory

```bash
cd career-coach
claude
```

Claude Code will automatically load `CLAUDE.md`, the settings, and the career-coaching skill. You're ready to go.

## Usage

### Ask a career question

Just type your question naturally. The career-coaching skill triggers automatically when you ask about career decisions, professional dilemmas, job changes, leadership, negotiation, entrepreneurship, confidence, workplace politics, or professional direction.

**Examples:**

```
Should I leave BigLaw to start my own legal tech company?
```

```
I've been offered a promotion but it means managing people instead of doing the work I love. What should I do?
```

```
I'm 18 months into a startup that isn't working. How do I know when to quit vs. persist?
```

```
My boss takes credit for my work. How should I handle this?
```

```
I have a non-linear career — law, then tech, then consulting. How do I make that a strength, not a weakness?
```

### What happens next

1. **The lead agent** reads your question, consults the panel selection guide, and picks 3 personas that will produce genuine disagreement
2. **Three teammates are spawned** (using Sonnet to manage token costs), each embodying a historical figure with a full personality, philosophical framework, and approach
3. **Initial advice** — each teammate responds to your question in character (150 words max)
4. **Debate round** — each teammate reads the others' positions and sends one challenge to the teammate they most disagree with (80 words max)
5. **Final positions** — each teammate sends their revised view to the lead (100 words max), noting if the debate changed their thinking
6. **Synthesis** — the lead weaves the perspectives together: headline insight, where they agreed, where they clashed, 2-3 concrete action items
7. **Deep dive offer** — you're asked if you'd like to "pull aside" any figure for a longer 1-on-1 conversation
8. **Team shutdown** — teammates are dismissed to stop burning tokens

### Go deeper with a single figure

After the panel responds, you can ask for a 1-on-1:

```
I'd like to talk to Kahneman more about the biases he mentioned.
```

```
Pull Seneca aside — I want to explore the fear angle.
```

This spawns a single teammate (not a full team) for an extended conversation with that figure.

### Interact with teammates directly

During an active team session, you can message individual teammates using **Shift+Up/Down** to select them. This lets you ask follow-up questions to specific figures while the team is still active.

## How It Works — Architecture

### Why Agent Teams (not Subagents)

Claude Code offers two multi-agent architectures:

| | Subagents | Agent Teams |
|---|---|---|
| **Communication** | Report back to parent only | Message each other directly |
| **Debate** | ❌ Cannot challenge each other | ✅ Full peer-to-peer messaging |
| **Context** | Isolated | Own context + inbox from peers |
| **Cost** | Lower (lightweight workers) | Higher (each is a full Claude instance) |

Career coaching needs the figures to **actually disagree and respond to each other**, which requires Agent Teams. Subagents would just produce three independent responses with no real dialogue.

### Token Budget Controls

Agent Teams are expensive. This project includes aggressive token management:

- **3 teammates max** per question (not all 12)
- **Sonnet for all teammates** (lead uses your session's model)
- **Strict word limits**: 150 → 80 → 100 words across three rounds
- **One debate round only** — no runaway back-and-forth
- **Direct messages only** — no `broadcast` (which multiplies cost by N)
- **Immediate team shutdown** after synthesis
- **Progressive disclosure** — the 12 persona prompts live in a reference file loaded only when needed, not always in context

### File Roles

| File | Always in context? | Purpose |
|---|---|---|
| `CLAUDE.md` | ✅ Yes (all sessions + teammates) | Token rules, workflow, panel selection guide |
| `settings.json` | ✅ Yes (config) | Enables Agent Teams |
| `SKILL.md` | Only when skill triggers | Spawn instructions, synthesis format, debate protocol |
| `references/personas.md` | Only when lead reads it | Full persona prompts for spawning teammates |

This follows the **progressive disclosure** pattern recommended by Anthropic's skill framework: metadata is always available, instructions load on trigger, and heavyweight content loads on demand.

## Customisation

### Add a new figure

1. Open `.claude/skills/career-coaching/references/personas.md`
2. Add a new section following the existing pattern:
   - Biographical identity (who they are, what they lived through)
   - Philosophical core (their framework for thinking about life/work)
   - Speaking style (how they sound — tone, habits, references, humour)
   - Approach to career questions (their step-by-step method)
3. Add the figure to the panel table and selection guide in `CLAUDE.md`

**Good candidates to add:** Marcus Aurelius (Stoic leadership), Machiavelli (power dynamics), Peter Drucker (management), Marie Curie (perseverance in hostile institutions), Frederick Douglass (self-liberation), Nassim Taleb (antifragility).

### Change the number of teammates

Edit the token budget rules in `CLAUDE.md`. You can increase to 4 teammates per question if you're on the Max plan and willing to spend the tokens. Going above 4 is not recommended — the synthesis becomes unwieldy and the debate round gets expensive.

### Change the teammate model

The SKILL.md instructs the lead to "Use Sonnet for all teammates." You can change this to Opus for richer persona responses, but expect roughly 5-10x the token cost per session. A good middle ground is Sonnet for personas and Opus for the lead (which handles synthesis).

### Adjust word limits

The word limits (150/80/100) in `CLAUDE.md` and `SKILL.md` balance quality against token cost. If you find responses too terse, increase to 250/120/150. If you want even leaner sessions, reduce to 100/50/75.

### Slash command

The project includes a `/careercoach` slash command at `.claude/commands/careercoach.md`. Use it for an explicit entry point:

```
/careercoach Should I take the promotion?
```

You can duplicate and rename this file to create variants — e.g. a `/quickcoach` that uses 2 teammates instead of 3, or a `/deepcoach` that allows a longer debate round.

## Display Modes

Agent Teams support two display modes:

- **In-process (default):** All teammates run in your terminal. Use Shift+Up/Down to select teammates and view their sessions.
- **Split panes:** Each teammate gets its own terminal pane. Requires `tmux` or `iTerm2`. Set with:

```bash
export CLAUDE_CODE_SPAWN_BACKEND=tmux
```

Split panes are recommended for this project — watching the historical figures debate in real time is half the fun.

## Troubleshooting

### Skill doesn't trigger

- Make sure you're running Claude Code **from the project directory** (`cd career-coach && claude`)
- Check that `.claude/skills/career-coaching/SKILL.md` exists and has valid YAML frontmatter
- Try explicitly asking: *"Use the career-coaching skill to answer this question..."*

### Agent Teams not working

- Verify you're on Claude Code **v2.1.32+**: run `claude --version`
- Check that `.claude/settings.json` contains the `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS` flag, or set it as an environment variable
- Agent Teams are experimental — if you encounter issues, fall back by asking Claude to use the personas as subagents instead (they just won't debate each other)

### Teammates don't debate

- The debate instruction is embedded in the spawn prompt via SKILL.md. If teammates skip the debate round, try being more explicit in your initial question: *"I want the panel to debate this question before giving me their advice."*
- Ensure the lead is waiting for teammates to finish before synthesising — you can say: *"Wait for all teammates to complete their debate before synthesising."*

### Token usage is too high

- Check that Sonnet is being used for teammates (not Opus)
- Reduce to 2 teammates for simpler questions
- Lower word limits in `CLAUDE.md`
- Ensure the team is being shut down after synthesis

## Estimated Token Usage

Rough estimates per career coaching session (3 Sonnet teammates + lead):

| Phase | Tokens (approx.) |
|---|---|
| Teammate spawn (×3) | ~3,000 each (context loading) |
| Initial advice (×3) | ~600 each |
| Debate round (×3) | ~400 each |
| Final positions (×3) | ~300 each |
| Lead synthesis | ~1,500 |
| **Total** | **~15,000–20,000** |

This is a rough guide — actual usage varies with question complexity and model verbosity. A 1-on-1 deep dive adds roughly 5,000–8,000 tokens per exchange.

## Contributing

Contributions welcome — especially:

- New persona figures with well-crafted prompts
- Improved panel selection logic
- Alternative workflows (e.g. a "quick mode" using subagents instead of agent teams for cheaper sessions)
- Prompt refinements that improve persona distinctiveness or debate quality

## Licence

MIT — use it however you like.
