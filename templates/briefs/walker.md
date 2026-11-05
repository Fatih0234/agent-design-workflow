# Brief: independent walker (gate 4), the same for every version

You walk **one version** of {{SCREEN}} as a first-time user. The orchestrator fills in four blanks when it starts
you. Everything else is the same for every version, so walks compare fairly.

| Blank | Example |
|---|---|
| `<label>` | `a-v1` (names your files; says nothing about quality) |
| `<port>` | `8080` |
| `<v>` | `1` (the `?v=` value) |
| `<out>` | `{{RUN_DIR}}/walks/a-v1/` |

You did not build this version and you get no design rationale, on purpose. Judge only the screen.

## Start
- Data: {{TEST_PROJECT}}; its id is in `{{STATE_FILE}}`. Read it fresh.
- Start URL: `{{APP_URL}}?v=<v>`. Start every task from it.
- Browser: `playwright-cli open --browser=chromium` (headless), with your own session `-s=<label>` so parallel
  walkers don't share a browser. Never `npx`. Never wait for "network idle"; wait for content.
- If `?v=<v>` shows no version (an error, or today's screen), stop and report `recapture` with the screenshot.

## Tasks (at 1440 x 900; then tasks 1 and 2 again at 1024 x 768)
{{TASKS}}

Also: one screen in dark mode, and one keyboard pass through the first screen. Is focus visible?

## Rules
- **Never change data**: no delete, analyze, re-analyze, retry, upload, import, rename. Never press a button whose
  label contains {{DANGEROUS_WORDS}}; some start a job at once, with no confirm. Opening a dialog and cancelling is
  fine. If you changed data by accident, say so in the report's first line.
- **In the keyboard pass, press only Tab and Shift+Tab.** Never Enter or Space while a button has focus.
- Use only what the screen shows. Read source code only afterwards, to explain a bug you already saw.
- Report problems in the shared navigation under a separate heading "Shell", not as findings of this version.
- Don't open other versions or ports. Write only into `<out>`.

## Counting (the deck quotes this; be exact)
- Clicks until every part of the right answer is on screen, from a fresh load at 1440.
- The click into a search box counts; typing does not. An expand (disclosure, group, row) counts, even when it only
  reveals a name. Scrolling is free, but say how far you scrolled.
- Take the item's own path (its row or link), not a detour through a filter. If you used search, say so.

## Record per task
- Numbered steps: what you clicked or typed, and what the screen then showed.
- Clicks, keystrokes, URL changes, scrolling.
- Done or not, and the answer **in the app's own words** (quoted).
- Where you hesitated and why: confusing, missing, wrong or inconsistent (one thing with two names; numbers that
  don't add up).
- One screenshot per step: `<out>/<label>-t<task>-<step>.png` (`<label>-1024-t<task>-<step>.png` at 1024).

## Report: `<out>/walk.md`; your final message repeats sections 1-3
1. Verdict word: `ship | fix | rebuild | recapture`.
2. Task table: task, done?, clicks, keystrokes, URL changes, scroll, answer as quoted.
3. Findings P0 (blocks a task) to P3 (polish), each with a screenshot path or quoted text. No evidence, no finding.
4. What changes at 1024.
5. The 3 strongest things, in a user's words.
6. Every screenshot path, absolute, one per line.

If you could not run a real browser, the first line is `⚠️ DEGRADED: <reason>`.
