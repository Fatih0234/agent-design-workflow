# Agent design workflow

How one person and a tree of Claude Code agents explored three designs of one UI screen, built 2-3 versions of each,
checked every version with independent first-time-user walks and a blind judge, and ended with a fact-checked
presentation deck. It took one working day and cost about $300.

This repo holds the workflow only: prose, diagrams and templates. It has no app code and no runnable product. The
screen was the Data Sources page of a B2B product-data tool, a list of uploaded files and what an analysis
extracted from them.

## The workflow on one screen

One **orchestrator** session runs the whole thing. It writes the contract, the briefs and the status board. It
starts one **design lead** per design direction. Each lead works in its own git worktree and terminal tab. A lead is
a manager: it plans versions, has **builder** subagents build them, and has a **critic** subagent walk them. A
**verifier** reproduces a surprising finding before the lead acts on it. When a version is ready, the orchestrator
sends its own **walker**. A **blind judge** per design then picks the finalists from neutral labels. A **deck lead**
builds the presentation, and a fresh **fact checker** checks every claim in it against the live app.

The human hears from the orchestrator only at four milestones, and when an escalation needs a decision. Permission
prompts always go to the human.

```
                               Human
                 milestones · escalations · permission prompts
                                 │
                          ┌──────┴───────┐
                          │ Orchestrator │──spawns──► walker (1 per version)
                          └──────┬───────┘──spawns──► blind judge (1 per design)
                 SendMessage     │  (leads talk only to the orchestrator)
      ┌──────────────┬───────────┼──────────────┬──────────────┐
  ┌───┴────┐     ┌───┴────┐  ┌───┴────┐   ┌─────┴──────┐  ┌────┴──────┐
  │ lead A │     │ lead B │  │ lead C │   │lead-details│  │ lead-deck │
  └───┬────┘     └────────┘  └────────┘   │ (studies)  │  └────┬──────┘
      │ own worktree + tab, each          └────────────┘       │ spawns
      ├─► builder (1 per version, one job each)          fact checker
      ├─► critic (the lead's own walk)
      └─► verifier (reproduces one finding)
```

A version moves forward only through six gates:

```
concept ─► quality floor ─► lead's critic ─► independent walker ─► blind judge ─► deck fact check
 (2-3 one-line   (typecheck, lint,   (5 fixed tasks;   (same brief and    (shuffled X/Y/Z;   (every claim vs
  principles,     tests, contrast,    fix, re-walk)     tasks for every    picks 1-2          the live app)
  wait for OK)    focus, overflow,                      version)           finalists)
                  light + dark)

Verdict words at every walk: ship · fix · rebuild · recapture
```

## Read on

| Doc | What it holds |
|---|---|
| [docs/roles.md](docs/roles.md) | Every role, what it owns, who it talks to, and how a lead session starts |
| [docs/loop-and-gates.md](docs/loop-and-gates.md) | The phases, the six gates, the five tasks, the verdict words |
| [docs/guardrails.md](docs/guardrails.md) | Milestones, escalations, guardrails, and the hooks that enforce them |
| [docs/lessons.md](docs/lessons.md) | What went wrong or cost too much, and the rule each problem produced |
| [docs/cost.md](docs/cost.md) | The cost by role and by phase |
| [templates/](templates/) | Briefs (run contract, kickoff, lead, walker, judge, deck) and agent definitions, with blanks |

## Use it yourself

1. Fill in [templates/briefs/run.md](templates/briefs/run.md): goal, designs and theses, the five tasks, ground
   truth, guardrails.
2. Copy the agent definitions from [templates/agents/](templates/agents/) to `~/.claude/agents/`.
3. Start an orchestrator session with [templates/briefs/kickoff.md](templates/briefs/kickoff.md).
4. Read [docs/lessons.md](docs/lessons.md) before you start. Most of its rules cost money to learn.

The setup assumes Claude Code with cross-session messaging (`SendMessage`), a terminal multiplexer that can start and
prompt agent sessions (we used Herdr), git worktrees, and `playwright-cli` with Chromium for the walks.

## License

[MIT](LICENSE)
