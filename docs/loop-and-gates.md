# The loop and its gates

## Phases

```
setup ─► leads start ─► concepts ─► build ─► floor ─► walks ─► judge ─► final round ─► deck ─► fact check
  │                        │                            │         │                      │
  orchestrator        orchestrator OKs           walker per   judge per            starts early,
  alone               distinctness               version      design               final shots last
```

It is one continuous run with no calendar. Leads run in parallel. The deadline is only the latest point for the
presentation.

## The contract

Before anything starts, the orchestrator and the human agree one page: the run contract
([template](../templates/briefs/run.md)). It holds the goal, what is already decided, one thesis per design, what is
fixed and what is free in the look, the tasks, the gates, the guardrails and when to escalate. Every brief points
back to it.

"Fixed versus free" is what keeps the designs at home in the app:

| Fixed (feels at home) | Free (the design's own) |
|---|---|
| design tokens, colors, type family, the UI component library, light and dark mode, app shell and navigation, the app's wording conventions | page layout, density, information hierarchy, what the overview shows, row anatomy, how list and detail relate, progressive disclosure, one named signature move |

Layout comes first, in grayscale. Color must not hide a weak layout.

## The six gates

A version moves forward only through these, in order.

### 1. Concept

The lead proposes 2-3 versions. Each is a **one-line organizing principle**: what governs the arrangement, what the
eye does first, what the screen behaves like. Each comes with an ASCII wireframe, a grayscale sketch screenshot and
the named signature move.

Distinctness rules:
- If two lines resolve to the same sentence, they are one version.
- A palette, font or spacing swap over the same layout is not a version.
- Test: someone could tell the versions apart at thumbnail size.
- Across designs, signature moves must not collide. No design borrows another's move ("no convergence").

The orchestrator checks this and answers. No lead builds before the answer.

### 2. Quality floor

The builder checks all of these before it reports done, and the lead checks again:

- typecheck, lint and the touched tests pass;
- text contrast at least 4.5:1 (large text 3:1);
- visible keyboard focus on every control;
- no horizontal overflow or clipped text at 1024 and 1440 px;
- loading, empty and error states exist;
- light and dark mode both correct;
- icon-only buttons have an accessible label, async status updates are announced;
- reduced motion is respected; numbers in tables use tabular figures.

### 3. The lead's own critic

A fresh critic subagent, never a builder, walks the version with the five tasks. The lead fixes, then a new critic
re-walks. A finding that surprises the lead goes to a verifier first.

A version is **walk-ready** when the critic's latest pass did all five tasks and has no open P0 or P1 finding. The
lead then reports it and stops walking. Open P2 and P3 findings wait and get fixed together with the walker's
findings (see [lessons](lessons.md#pacing)).

### 4. Independent walker

The orchestrator spawns a fresh walker per version. Every walker gets the same brief
([template](../templates/briefs/walker.md)) and four blanks: label, port, version, output folder. It gets no design
rationale on purpose. It judges only the screen.

The orchestrator checks the walker's counts and quoted answers against the ground truth before it relays the report
to the lead.

### 5. Blind judge

One judge per design, after the lead's fix round. The orchestrator copies each version's walk report under a shuffled
neutral label (X, Y, Z) and keeps the map in a file the judge never reads. The judge reads the reports, opens each
version itself at two widths, does the five tasks as a quick check, and scores each label on four things, in this
order:

1. **Tasks**: can a first-timer finish all five, how fast, with how much doubt? Are the numbers true?
2. **Thesis**: does the first screen show the thesis within 5 seconds?
3. **Character**: is it its own thing, or a weaker copy of a sibling?
4. **Finish**: does it look finished at both widths, light and dark?

It picks one finalist, or two if they are truly different and both strong. Never three.
[Template](../templates/briefs/judge.md).

### 6. Deck fact check

When the deck is complete, the deck lead spawns a fresh fact checker. It lists every factual claim (numbers, quotes,
click counts, "it does X"), checks each against the live app and the walk and judge reports, and writes
claim → verdict (true, false, unverifiable) → evidence. The deck lead fixes every false claim, and the checker
re-checks those. In our run it checked 123 claims and found 6 false ones.

## The five tasks

Every critic, walker and judge uses the same five tasks on the same seeded test project, at 1440 x 900, then tasks 1
and 2 again at 1024 x 768. Ours were:

1. **Overview**: what is the overall state of this project?
2. **Attention**: which items need my attention, and why?
3. **One big item**: what did this large file give me, and what is missing?
4. **Find one**: show me what this specific file produced.
5. **Add**: open the add dialog, then cancel.

Plus one screen in dark mode and one keyboard pass (Tab only) through the first screen.

Write the tasks for your own screen, but keep the shape: one overview task, one triage task, one deep task, one find
task, one action that changes nothing.

The test project needs a **ground truth**: the counts a correct screen must show (for example "60 items: 4 need
attention, 9 not analyzed, 47 done"). Walkers, judges and the fact checker all check against it.

## Verdict words

Every walk ends with one of four fixed words:

| Word | Means |
|---|---|
| `ship` | Good enough for the presentation as is |
| `fix` | Tasks work; findings need a fix round |
| `rebuild` | The organizing principle fails; start the version again |
| `recapture` | The evidence is broken (wrong version loaded, no browser, missing screenshots); walk again |

Findings rank P0 (blocks a task) to P3 (polish). A finding without a screenshot or quoted text does not count.
