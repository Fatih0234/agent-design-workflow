# Templates

Generalized from one real run. Blanks look like `{{NAME}}`; fill every one before use. `grep -n '{{' <file>` lists
what is left.

| File | Who reads it | Filled by |
|---|---|---|
| [briefs/run.md](briefs/run.md) | everyone; the contract | the human and the orchestrator, before the run |
| [briefs/kickoff.md](briefs/kickoff.md) | the orchestrator, as its first prompt | the human |
| [briefs/lead.md](briefs/lead.md) | each design lead (one copy per lead) | the orchestrator |
| [briefs/walker.md](briefs/walker.md) | each independent walker | the orchestrator writes it once; four blanks per walk |
| [briefs/judge.md](briefs/judge.md) | each blind judge | the orchestrator writes it once; four blanks per design |
| [briefs/deck.md](briefs/deck.md) | the deck lead | the orchestrator |
| [agents/](agents/) | Claude Code agent definitions; copy to `~/.claude/agents/` | adjust paths and tools once |

Common blanks:

| Blank | Example |
|---|---|
| `{{RUN_DIR}}` | `~/work/screen-redesign/run-1/` (status board, briefs, walks, judge, deck) |
| `{{SCREEN}}` | "the Data Sources page" |
| `{{ORCHESTRATOR}}` | the orchestrator's session name, e.g. `orchestrator` |
| `{{APP_URL}}` | `http://127.0.0.1:{{PORT}}/<path to the screen>` |
| `{{TEST_PROJECT}}` | the seeded data every walk uses |
| `{{GROUND_TRUTH}}` | the counts a correct screen must show |
| `{{TASKS}}` | the five tasks (see [loop-and-gates](../docs/loop-and-gates.md#the-five-tasks)) |
| `{{LAUNCHER}}` | the one script allowed to start and stop apps |
| `{{BROWSER}}` | `playwright-cli` with `open --browser=chromium` |
