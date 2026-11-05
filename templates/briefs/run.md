# Run contract: {{RUN_NAME}}

One page that defines the whole run before anything starts. Agreed between {{HUMAN}} and the orchestrator on
{{DATE}}. Every brief points back here. Change it only through the human.

## 1. Goal
By {{DEADLINE}}, {{HUMAN}} shows {{AUDIENCE}} {{N}} designs of {{SCREEN}}, each in its own clear character, with 1-2
finished versions each, live and in a deck.

Done means:
- Each design runs as a local app on {{TEST_PROJECT}}, with clickable versions.
- Each version passed the quality floor (§6) and an independent first-time-user walk.
- A deck with presenter notes and recorded walks, fact-checked against the live apps.

## 2. Already decided
{{DECISIONS}} (one line each; leads do not reopen these)

## 3. The designs: one thesis each
| Design | Thesis (every decision must follow it) | Starting point |
|---|---|---|
| A | {{THESIS_A}} | {{BRANCH_A}} |
| B | {{THESIS_B}} | {{BRANCH_B}} |
| C | {{THESIS_C}} | {{BRANCH_C}} |

Each lead proposes 2-3 versions as one-line organizing principles before building. Versions live in one branch,
switched by `?v=1|2|3`, one component file per version. Today's screen stays as `?v=0` until the finalists are chosen.

## 4. Look: fixed and free
- **Fixed:** {{FIXED}} (tokens, colors, type family, component library, light and dark mode, shell, wording).
- **Free:** layout, density, hierarchy, what the overview shows, row anatomy, how list and detail relate, disclosure,
  one named signature move per design.
- **Order:** layout in grayscale first, then taste.

## 5. The tree
```
Orchestrator ({{ORCHESTRATOR}}): contract, briefs, status board, cross-design decisions, escalations
├─ setup (orchestrator itself): branches, worktrees, shared code, settings, hooks, agents, apps
├─ lead-a, lead-b, lead-c: one worktree and tab each; builders, critic, verifier as subagents
├─ lead-details (optional): studies of small details, no code
├─ walkers (1 per version) and blind judges (1 per design): subagents of the orchestrator
└─ lead-deck: deck, recorded walks, fact checker
```
Leads talk only to the orchestrator and never edit each other's worktrees.

## 6. Gates
1. **Concept**: 2-3 one-line principles, a wireframe and a grayscale sketch each. Wait for the orchestrator.
2. **Quality floor**: typecheck, lint, touched tests; contrast ≥ 4.5:1; focus visible; no overflow at 1024 and 1440;
   empty, loading and error states; light and dark.
3. **Lead's critic**: the §7 tasks. Walk-ready = all five tasks done, no open P0 or P1.
4. **Independent walker**: same brief for every version.
5. **Blind judge** per design: shuffled labels, 1-2 finalists.
6. **Deck fact check** against the live apps.

Verdict words: `ship | fix | rebuild | recapture`.

## 7. Tasks (on {{TEST_PROJECT}}, at 1440 x 900; tasks 1-2 again at 1024 x 768)
{{TASKS}}

Ground truth: {{GROUND_TRUTH}}

## 8. Places
| What | Where |
|---|---|
| Run files | `{{RUN_DIR}}` |
| Apps | {{PORTS}}, started only through `{{LAUNCHER}}` |
| Branches | {{BRANCHES}}, local only; rollback tags before any rebase |

## 9. Guardrails
- No push, no merge request, no comments on the code host or ticket system.
- No dependency installs. No permission bypass. No bare `git stash`.
- Apps only through `{{LAUNCHER}}`. Never touch the development database.
- Walkers, judges and recorders never change data.
- Leads write only in their worktree and their run folder (enforced by hooks).

## 10. Escalate to {{HUMAN}} only for
- a change to a decision in §2;
- shared code outside {{SCREEN}};
- cost far above plan;
- anything outside §9.

Everything else: decide, log it in the status board with the reason, go on. Permission prompts always go to
{{HUMAN}}.

## 11. Milestones
Setup done · concepts chosen · finalists clickable · deck ready.

## 12. Cost
{{COST_LIMIT}}. The status board logs each session's cost at every phase change and the total at the end.
