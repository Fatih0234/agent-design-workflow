# Lessons learned

Each lesson is a problem we hit, what it cost, and the rule it produced. The rules are already in the
[templates](../templates/).

## Context and cost

**A session can't compact itself, and nobody can compact a subagent.** Our leads reached 266-342k tokens of context.
A session re-reads its whole context on every turn, so a long lead gets more expensive with every step. Two builders
of one lead ran to 383k and 392k after about 315 turns each.

Rules:
- **The orchestrator compacts each lead at its pauses.** The lead writes a Handoff section in its status file and
  goes idle (for example while it waits for the walker). The orchestrator checks the tab is at its prompt, then
  sends `/compact` through the multiplexer, with a note: who the lead is and which files to read first. A compacted
  lead went from about 340k to under 35k.
- **Subagents get one job each and a budget of about 150k context.** Near the budget, the subagent writes a handoff
  note (done, not done, next steps, files) into the lead's folder and returns. The lead spawns a fresh one from the
  note.
- **Never resume a finished subagent.** For the next job, spawn a fresh one with a self-contained brief.
- **Idle sessions cost nothing.** Leave a waiting lead idle; don't keep it busy polling.
- Tell subagents to pipe build, test and lint output through `tail` or `grep`, and not to re-open screenshots they
  already saved.
- Running sessions don't reload agent definition files. A rule added mid-run reaches them only by message.

The leads' own turns were only about 20-25% of the cost. Their subagents were the rest.

## No build and no end-to-end tests while someone is on the app

The app served the built client files from disk. A rebuild shows on the next page load, with no restart. So a
rebuild in the middle of a walk changes the screen under the walker and invalidates the walk. The end-to-end setup
also rebuilt the client when it was stale.

Rules:
- While a walker, judge or recorder is on a lead's app: no client build and no end-to-end run. Commits are fine.
- The orchestrator announces when a walk starts and ends.
- Builders of one lead share one build lock (`flock`), so two builds never run at once.

## End-to-end tests against a shared database need one worker

The test setup created and dropped its own database, so it never touched the development data. But all apps and
test runs shared one database server. With 4 workers the seed step crashed; with 2 workers the server ran out of
connections ("too many clients") while the apps were running. With 1 worker the suites passed.

Rule: run end-to-end tests with 1 worker when they share a database server with running apps.

## The Tab pass must only press Tab

During a keyboard pass, a critic pressed Enter on a focused "Re-analyze" button and opened its confirm dialog. It
cancelled, and the lead checked that the data was unchanged. Some analyze buttons start a job at once, with no
confirm dialog.

Rules:
- In the keyboard pass, press only Tab and Shift+Tab. Never Enter or Space while a button has focus.
- Name the dangerous buttons in the walker brief ("never press a button whose label contains *analyze* or
  *retry*"), not only the general rule "never change data".
- If a walker changes data by accident, it says so in the report's first line.

## Pacing

Walk and fix loops were the most expensive phase. Our cost rose from about $75 at the end of the build to about $162
during walk and fix rounds. One lead was on its third and fourth walk; another meant to "re-walk until ship".

Rule: a lead reports **walk-ready** when the critic's latest pass did all five tasks and has no open P0 or P1
finding. Open P2 and P3 findings wait. The lead fixes them in one batch together with the independent walker's
findings. No fifth walk. The independent walk is the next check anyway.

## Judge relabeling

The judge must not know which version is which, and must not see the leads' notes. The orchestrator copies each walk
report into the judge's folder under a shuffled neutral label (X, Y, Z), keeps the map in a separate file the judge
never reads, and tells the judge that the `?v=` number in a URL says nothing about quality or order. The judge reads
no source code and no lead notes.

Judge each design before its last small fix round. The final round then covers only the finalists, and nobody fixes a
version that won't be shown.

## A fixed rule for counting clicks

The fact check found 6 false claims in 123. Three were click counts for the same task. The walkers had counted that
task through the search box, while the baseline screen had been counted without search, so the old page looked faster
than every new design.

Rule, stated in the deck and the walker brief, the same for every version:
- Count clicks until every part of the right answer is on screen, from a fresh load at the wide viewport.
- The click into a search box counts. Typing does not.
- An expand (a disclosure, a group, a row) counts as a click, even when it only reveals a name.
- Scrolling is free.
- Take the item's own path (its row or link), not a detour through another filter.
- Measure on the final build of each finalist, not on an earlier walk.

## Prefer headless Chromium

Use `playwright-cli open --browser=chromium` (headless) with a named session per walker (`-s=<label>`), so parallel
walkers never share a browser. The default Chrome channel was not installed, and headless Chromium was enough for
every walk, judge and recording.

Also:
- Never wait for "network idle". The page held an event stream open, so it never went idle. Wait for content.
- Headless screenshots don't capture native tooltips. Quote the tooltip text instead.

## Smaller lessons

- **One truth for the numbers.** Early on, one design counted an item twice and showed 48 of 60 where the truth was
  47. Write the ground truth into every brief, and make overview buckets disjoint so they sum to the total.
- **Never sum counts across the parts of a split file.** One file's parts summed to 179 attributes, where the file
  had 20 distinct ones. Show distinct counts, or none.
- **Builders never commit.** Several builders share one worktree and one git index. The lead commits, and stages only
  explicit paths, never `git add -A` while a builder works.
- **Keep today's screen as `?v=0`** next to the new versions until the finalists are chosen. It is the baseline for
  comparison and for the existing tests.
- **Shell issues get their own heading.** Walkers report problems in the shared navigation under "Shell", so they
  don't count against a version. They go onto a "later" list for the deck.
- **Start the deck early.** Structure, build script and notes first; final screenshots and videos after each design
  is frozen.
- **Check walker numbers before relaying them.** The orchestrator compares each report's counts and quotes with the
  ground truth.
- **A message wakes an idle session.** The orchestrator can send work to an idle lead without a manual prompt.
- **Expect a usage-limit pause.** Our run hit one in the evening. Status files and handoff sections made the restart
  cheap.
