# Roles

Every role is a Claude Code session or subagent, except the human. Each role has one job, owns a fixed set of
files, and talks to a fixed set of other roles.

## Who talks to whom

```
 Human ◄──── milestones, escalations, permission prompts ──── Orchestrator
   │                                                              │
   └── answers every permission prompt, in the blocked tab        │ SendMessage (both ways)
                                                                  │
        ┌──────────────┬──────────────┬──────────────┬────────────┴─┐
     lead A         lead B         lead C       lead-details      lead-deck
        │                                                            │
        ├── builder, critic, verifier (subagents)                    └── fact checker (subagent)

 Orchestrator ── spawns ──► walker (per version), blind judge (per design)
```

- Leads talk only to the orchestrator. They never talk to each other.
- Subagents report only to the session that spawned them.
- Walkers and judges get a brief and blanks, nothing else. They never see a lead's notes.
- No message can approve a permission prompt. Only the human can, in the blocked session's own tab.

## The roles

| Role | Kind | Count | Owns | Job |
|---|---|---|---|---|
| Human | person | 1 | the decisions | Agrees the contract, answers escalations and permission prompts, hears milestones |
| Orchestrator | main session | 1 | contract, briefs, status board, cross-design decisions | Sets up, starts and watches the leads, runs gates 4 and 5, logs cost, compacts leads |
| Design lead | main session | 1 per design | its worktree, its folder in the run directory | Proposes versions, briefs builders and critics, commits, iterates to the bar |
| Builder | subagent of a lead | 1 per version (fresh per job) | the files its brief names | Builds one version to the quality floor; never commits |
| Critic | subagent of a lead | fresh per walk | its walk report | Walks one version as a first-time user with the five tasks (gate 3) |
| Verifier | subagent of a lead | per finding | nothing | Reproduces one claimed issue; returns confirmed, rejected or unclear |
| Details lead | main session | 0-1 | a studies folder | Studies small details in depth (row anatomy, status words, metrics, states) and offers 2-4 options |
| Walker | subagent of the orchestrator | 1 per version | its walk report | The independent walk (gate 4), same brief for every version |
| Blind judge | subagent of the orchestrator | 1 per design | its judge report | Picks 1-2 finalists from shuffled labels (gate 5) |
| Deck lead | main session | 1 | the deck folder | Builds the deck, presenter notes and recorded walks |
| Fact checker | subagent of the deck lead | 1 (fresh per check) | its fact-check report | Checks every deck claim against the live app (gate 6) |

## The orchestrator

The orchestrator is a fresh session with a fixed name, so leads can message it by name. It:

- writes the status board first and keeps it current: each lead's phase, gate result, cost and last note;
- does the setup itself: branches, worktrees, shared code that every design needs, settings, hooks, agent
  definitions, app starts;
- tests one hook of each kind in a throwaway session before relying on it;
- writes the briefs, then starts the leads;
- checks each concept for distinctness before any lead builds;
- decides everything that is not an escalation, and logs each decision with its reason;
- never waits idle on one lead while another needs it;
- never answers a permission prompt.

It watches the leads with a monitor on the multiplexer's agent list and reacts to state changes: working, idle,
blocked, done. A `blocked` lead means a permission prompt is open. The orchestrator tells the human.

## A design lead

A lead is a manager with taste. It owns one design direction and its thesis (one sentence that every decision must
follow, for example "Control room: see the state of everything at a glance"). It:

1. reads its brief and the shared brief, and looks at the screen as it is today at two widths;
2. proposes 2-3 versions as one-line organizing principles, with an ASCII wireframe and a grayscale sketch each,
   and waits for the orchestrator's answer;
3. spawns one builder per version, each with a self-contained brief; builders run in parallel only on disjoint files;
4. owns the shared files (the version switch, shared helpers) and all commits;
5. runs its own critic, fixes, and has a fresh critic re-walk;
6. keeps a status file current (`phase:`, `gate:`, next step, waiting for, decision log). A Stop hook blocks the lead
   while the file is stale;
7. reports each phase end to the orchestrator.

Versions live in one branch and switch by URL (`?v=1|2|3`), one component file per version. So each design needs
only one running app.

### How a lead session starts

Each lead runs in its own tab, with its worktree as the working directory:

```
<multiplexer> agent start lead-a -- \
  --name lead-a --agent design-lead --permission-mode auto \
  --settings ~/.config/design-leads/lead-a.json
<multiplexer> agent prompt lead-a "Read <run-dir>/briefs/lead-a.md and start."
```

- `--agent design-lead` loads the persona ([templates/agents/design-lead.md](../templates/agents/design-lead.md)).
  It may spawn only the builder, critic, app critic and verifier.
- `--settings` points to a per-lead file outside every worktree, so no lead can change its own rules. Don't use the
  project's `.claude/settings.local.json`: in a git worktree it resolves to the main checkout, so all worktrees
  share it.
- `--permission-mode auto` gives the lead room to work. The classifier decides; nothing bypasses permissions.

## The details lead

Optional, and cheap (about $4 in our run). It writes one short study per small detail that decides whether a
first-timer understands the page: how a row shows a file versus its parts, the words and colors for each status,
which overview numbers are true, and the empty, loading and error states. Each study shows what the designs do today,
2-4 options on the real data, and one marked recommendation. Leads pick in their own taste, or reject all options
with a reason. Some study findings became binding for all designs, for example "never sum counts across a split
file's parts".

## The deck lead

Starts early, before the finalists are frozen, and builds the structure, the build script and the presenter notes
first. It takes final screenshots and recorded walks only after the orchestrator announces a design frozen. The deck
is task-based: one slide per task, the same task across all finalists side by side.
