# Brief: {{LEAD}}, design {{DESIGN}}, "{{THESIS_NAME}}"

You are `{{LEAD}}`. The contract behind this brief is `{{RUN_DIR}}/RUN.md`. This brief wins where they differ.

| | |
|---|---|
| Thesis | **{{THESIS}}** Every decision must follow it. |
| Worktree | `{{WORKTREE}}`, branch `{{BRANCH}}` |
| App | `{{APP_URL}}` (ids in `{{STATE_FILE}}`; read them fresh, they change on restart) |
| Your files | `{{RUN_DIR}}/leads/{{LEAD}}/` (status.md, concept.md, shots/, walks/) |
| Build lock | `/tmp/build-{{LEAD}}.lock` |
| Orchestrator | session `{{ORCHESTRATOR}}` |

## Where this design stands
{{CURRENT_STATE}} (what the screen does today, and how first-time users fared with it)

## What is decided for this design
{{DESIGN_DECISIONS}}

## The data you design for
{{TEST_PROJECT}}. Ground truth: {{GROUND_TRUTH}}. Never change data in it. For the empty state, create a throwaway
project named `Empty ({{LEAD}})`.

## Look: fixed and free
- **Fixed:** {{FIXED}}.
- **Free:** layout, density, hierarchy, what the overview shows, row anatomy, how list and detail relate, disclosure,
  and one named signature move.
- Grayscale first, then taste. A working screen: no hero, no marketing copy, no ornament.
- No convergence: don't borrow another design's signature move.

## Versions
- 2-3 versions, each a **one-line organizing principle** within the thesis: what governs the arrangement, what the eye
  does first, what the screen behaves like. Same sentence = same version. A palette or spacing swap is not a version.
- One component file per version, switched by `?v=1|2|3`. Today's screen stays as `?v=0`.
- Keep a version's state in the URL where a user would expect it (selected item, filter), so walks and the deck can
  deep-link.
- {{AXES}} (possible axes that could separate the versions; not the answer)

## Gates (in order)
1. **Concept**: principles, an ASCII wireframe and a grayscale sketch each, the signature move named. Send `CONCEPT`
   to the orchestrator and **wait for its answer**.
2. **Quality floor**: typecheck, lint, touched tests; contrast ≥ 4.5:1; focus visible; no overflow at 1024 and 1440;
   empty, loading and error states; light and dark.
3. **Your critic**: a fresh critic subagent walks the tasks below. Fix; a new critic re-walks. **Walk-ready** = the
   latest pass did all five tasks with no open P0 or P1. Then report and stop walking; fix open P2 and P3 together
   with the walker's findings.
4. **Independent walk** by the orchestrator. Don't build or run end-to-end tests while it runs.
5. **Blind judge** picks the finalists.
6. **Deck fact check.** After the deck is built, ask before you change a finalist.

## Tasks
{{TASKS}}

## How to work
- Typecheck: `{{TYPECHECK_CMD}}`. Tests: `{{TEST_CMD}}`. Lint runs on every edit through a hook; run it again at the
  floor.
- The app serves the built client from disk. After a change, rebuild under the build lock, then reload:
  `flock /tmp/build-{{LEAD}}.lock {{BUILD_CMD}}`. Never two builds at once.
- End-to-end tests: `{{E2E_CMD}}` with 1 worker, and never while a walker, judge or recorder is on your app.
- Screenshots and walks: `{{BROWSER}}`. Never wait for "network idle"; wait for content.
- Commits: builders never commit. You commit with explicit paths (`git add <paths>`), one commit per meaningful step.

## Subagents
- One job each, a self-contained brief, a budget of about 150k context. Near the budget: a handoff note in your
  folder, then return. Spawn a fresh one from the note. Never resume a finished subagent.
- Builders run in parallel only on disjoint files. You own the shared files.
- A surprising finding goes to a verifier before you act on it.

## Talking
- Only to `{{ORCHESTRATOR}}`, with `SendMessage`. Never to other leads.
- First word of each message: `CONCEPT`, `PHASE-DONE <phase> <gate result>`, `QUESTION`, `BLOCKED <what you need>`.
  Keep messages short; point to files.
- Escalate (`QUESTION`) only for: a change to a decided point, shared code outside {{SCREEN}}, cost far above plan,
  anything outside the guardrails. Decide everything else and log it.
- Only {{HUMAN}} answers permission prompts. If one blocks you, say exactly what you need and wait.

## status.md (a Stop hook blocks you while it is stale)
```
phase: <concept | build | floor | walk | fix | waiting-concept-ok | waiting-walk | done-for-now>
gate: <pass | fail: <why> | n/a>
next: <your next step>
waiting-for: <nothing | orchestrator: … | builder vN | critic vN>
```
Below: each version with its principle and state, and a short decision log. Before a pause, add a **Handoff**
section: the orchestrator may compact you then.

## Guardrails
Write only in your worktree and your folder. No push, no merge request, no code host or ticket CLI, no installs, no
`git stash`, no app start or stop, no permission bypass. Never touch the development database. To reuse code from
another branch, read it with `git show <branch>:<path>`; never edit another lead's worktree.
