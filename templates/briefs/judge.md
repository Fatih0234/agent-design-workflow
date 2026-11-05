# Brief: blind judge (gate 5), one design

You judge the 2-3 versions of **one** design of {{SCREEN}} and pick the **1-2 finalists** for the presentation. You
did not build or walk any of them.

| Blank | Meaning |
|---|---|
| `<thesis>` | the design's one-line idea, which every version must serve |
| `<versions>` | neutral label (`X`, `Y`, `Z`) → URL. Labels are shuffled; the `?v=` number says nothing about quality or order |
| `<walks>` | a folder with one independent walk report per label (`X.md`, …), same tasks for every version, written before the last fix round |
| `<out>` | your output folder |

## Before you start (orchestrator)
Copy each version's walk report to `<walks>/<label>.md` with a shuffled map. Keep the map in a separate file the
judge never reads. Replace version names in the copies with the labels.

## What you do
1. Read the thesis and the walk reports.
2. Open each version yourself (`playwright-cli open --browser=chromium`, session `-s=judge-<design>`) at 1440 x 900
   and 1024 x 768. Do the tasks quickly, as a check:
   {{TASKS}}
   Take one screenshot of the first screen per version per width (`<out>/<label>-first-1440.png`, `-first-1024.png`),
   plus any screenshot that backs a reason.
3. Judge each version, in this order:
   - **Tasks**: can a first-timer finish all five, how fast, with how much doubt? Are the numbers true and
     consistent? Ground truth: {{GROUND_TRUTH}}.
   - **Thesis**: does the first screen show the thesis within 5 seconds, without explanation?
   - **Character**: is it its own thing, or a weaker copy of a sibling? Would the audience remember it?
   - **Finish**: does it look finished at both widths, light and dark? Small polish counts least.
4. Pick **1 finalist, or 2 if they are truly different and both strong**. Never 3.

## Rules
- Never change data. Never press a button whose label contains {{DANGEROUS_WORDS}}. Opening a dialog and cancelling
  is fine.
- Judge the screen, not the code. Don't read source code or the leads' notes. Don't open other designs or ports.
- Don't try to find out which label is which version.

## Report: `<out>/judge.md`; your final message repeats it
1. **Finalists**: label(s), each with 3 reasons a team member would understand, each backed by a screenshot or quote.
2. **Not chosen**: per label, the one reason that decided it.
3. A table: label × (tasks, thesis, character, finish), each cell `strong | ok | weak` plus a few words.
4. What a finalist must still fix before the presentation: at most 5 items, most important first.
5. Every screenshot path, absolute, one per line.

If you could not run a real browser, the first line is `⚠️ DEGRADED: <reason>`.
