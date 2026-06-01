---
name: churn
description: "Forge a launch prompt for a fresh, bypass-permissions Claude Code session to autonomously churn through an existing backlog of beads — leveraging the full toolchain: masques (donned cognitive identities), tmux headless-claude fan-out, and the voidtalker skills. Use when someone wants to 'create a prompt for a fresh session', 'churn through the beads', 'spawn/launch an autonomous session', 'hand this off to a headless run', 'set up a swarm of claudes', 'kick off a grind session', or 'a prompt I can paste into a fresh --dangerously-skip-permissions session'. This is the GO button AFTER work is decomposed into beads (often by recursive-prd or dev-meta) — it produces the self-contained, grounded, land-the-plane execution prompt, not the spec itself."
---

# Churn — Forge an Autonomous Bead-Churning Session Prompt

This skill produces one artifact: a **copy-paste prompt for a fresh Claude Code session** (running `--dangerously-skip-permissions`) that will autonomously work a backlog of beads to completion — verifying as it goes, landing the plane every iteration, and fanning out to headless tmux workers when the work parallelizes.

It is the **"go" button**. The thinking — what to build, the acceptance criteria, the decomposition into beads — has already happened (often via `recursive-prd`, `framing`, or `dev-meta`). Churn turns that backlog into an execution order a fresh, context-free agent can pick up and run with zero per-iteration steering.

## Churn vs. its neighbors

- **`recursive-prd`** writes the *spec* — binary criteria, a generic loop prompt, a progress template. Churn assumes that exists and forges the *dispatch* prompt for a specific run.
- **`dev-meta`** Phase 5 ("Grind Beads") is the *activity*; churn is what you paste in to *start* that activity in a fresh, possibly-swarming session.
- **`ship`** is the *last mile* (merge/close/push) after the churn session converges. Churn hands off to ship.

If the work is **not** yet decomposed into beads with clear acceptance criteria, stop — route to `recursive-prd` / `framing` first. Churn cannot be tighter than the beads it dispatches.

## When to use

- The user says some variant of *"make me a prompt I can paste into a fresh session to churn through these beads."*
- Work is already tracked in beads (an epic + children, or a ready queue) and the user wants to hand it to an autonomous run.
- The user wants to walk away and let Claude Code (solo or as a tmux swarm) grind a backlog.
- The user explicitly invokes the full toolchain: masques, headless claudes, skills.

## Anatomy of a churn prompt

A churn prompt is self-contained — the fresh session starts with **no memory of this conversation**. Every section below is load-bearing. Omit the negative constraints and an autonomous agent expands scope on every iteration; omit the grounding and it claims victory it didn't earn.

1. **Role + autonomy posture.** "You are working autonomously on [repo], running `--dangerously-skip-permissions`. Self-impose discipline: prove before you claim, land the plane every iteration, never fake a result."

2. **North star.** The single source of truth: the PRD path **and** the beads epic/ready-queue. "Read [PRD] fully first." Point at the tracker (`bd show <epic>`, `bd ready`).

3. **Setup (once).** Concrete first-iteration steps: `git status` / `git switch <branch>` (note if it already exists — don't recreate) / `git pull --rebase`; `bd prime`; read the PRD; skim any read-only reference repos; **seat the dogfood loop** (see Toolchain); create the progress tracker with one checkbox per acceptance criterion.

4. **Scope — DO.** The buildable beads in **dependency order**, naming the keystone bead to start at. Include the concrete stack/conventions the agent needs to not flail (where files go, what to mirror, the blessed libraries).

5. **Scope — DO NOT.** The negative constraints, explicit and per-item. Fence off design-only beads ("deepen design notes only — do NOT build"), branch/merge discipline (work only on X; never main; no merge; no force-push), read-only reference repos, and anything that must not change (schema, public API, other people's code). **This section is the one most often underwritten and the one that most determines loop stability.**

6. **Grounding (non-negotiable).** Per-criterion verification methods — *run the thing*. "Check off C-n ONLY after [its stated verification]; paste real evidence into progress." If a tool is missing, file a bead and mark blocked — never assert an unrun result. (The `grounded` Stop hook enforces this; name it so the agent expects it.)

7. **Per-iteration loop.** Read progress → pick next unchecked criterion (dep order) → minimum change → verify → evidence in progress → `bd update/close` → **LAND THE PLANE** (`git add` → `bd sync` → commit with the project's `Co-Authored-By` trailer → `bd sync` → `git push` the branch). If blocked >~2 iterations: write the blocker, `bd create` a follow-up, move on.

8. **Toolchain leverage** (the differentiator — see next section): which masque to don, when to fan out to tmux headless workers, which skills to lean on.

9. **Exit.** The convergence condition (all buildable criteria checked-or-blocked-and-filed; design-only beads carry buildable notes), a **morning report** at the top of progress (done+verified with evidence refs, blocked+why with bead IDs, the single most important decision to review, suggested next first move), branch pushed, `git status` clean, **no PR** (leave that to the human).

## Toolchain leverage — what makes a churn prompt more than a bare loop

This is the part `recursive-prd`'s generic loop prompt doesn't cover. Wire these in deliberately:

### Masques — don a lens for the work
Pick a masque whose lens fits the bead class and tell the session to `/don` it:
- methodical builder / systems work → **Codesmith**
- design-only / architecture beads → **Architect**
- DB schema / data layer → **Firekeeper**
- review passes → **Reviewer**; closing out → **Grinder**
Different phases want different lenses — instruct the session to re-don as the work shifts. Donning also **dogfoods** the audience (telemetry + attribution), which is on-thesis for masque-bearing repos.

### Dogfood the audience (where available)
If the repo has the always-on audience, have setup `/audience seat` and the session `/don` a masque, so the run generates real telemetry the judge can score (`/performance` as a periodic self-check). The session both does the work and exercises the measurement loop.

### tmux headless-claude fan-out
Once the **keystone bead lands** and shared scaffolding exists, the remaining leaf beads are often file-disjoint and parallelize. Instruct the orchestrator to spawn headless tmux claudes — but enforce the coordination contract, because the failure mode is workers colliding on one working tree:
- **one bead per worker**; claim it with `bd update <id> --status=in_progress`
- **isolate each worker in its own `git worktree`** (`git worktree add ../<slug> <branch>`) so parallel edits don't collide
- each worker **dons a fitting masque**, does the work, **verifies**, and **lands its own plane** (commit + `bd sync` + push), then reports back via the bead
- **serialize** edits to shared files (e.g. a server's tool-registration module); only fan out the genuinely independent beads
- the orchestrator reconciles worktrees/merge-forward and keeps the progress tracker current
Gate the fan-out behind the keystone — never swarm before the shared core exists.

### Skills to name explicitly
`grounded` (the Stop hook will hold the session honest), `verify` / `code-review` before closing risky beads, `ship` for the final land. Naming them tells the fresh session they're available.

## Process — how to generate a churn prompt

### Step 1 — Read the backlog and the ground truth
`bd show <epic>` / `bd ready` / `bd list`. Read the PRD. Skim the repo: branch state, conventions, `AGENTS.md`/`CLAUDE.md`, the land-the-plane protocol, the `Co-Authored-By` trailer it uses. The churn prompt must reflect *this* project's reality, not a generic one.

### Step 2 — Establish the boundaries
What is **buildable** now vs **design-only**? What must **not** be touched (other repos, other branches, schema, public contracts)? Which beads are **independent** (parallelizable) vs **serial**? This determines the DO / DO-NOT sections and the fan-out plan.

### Step 3 — Pick the masque(s) and the dogfood posture
Match a lens to the dominant bead class; note where to re-don. Decide whether to seat the audience.

### Step 4 — Fill the template
Use `references/churn-prompt-template.md`. Customize the placeholders: repo, branch, PRD path, epic id, keystone bead, stack/conventions, negative constraints, verification methods, the masque, the commit trailer, the fan-out plan.

### Step 5 — Present it copy-paste-ready
Output the prompt in a single fenced block the user can paste directly into a fresh terminal. Add a 2–3 line note on the non-obvious choices you baked in (which masque, what's fenced as design-only, how the swarm is gated). Offer to save it to the repo (e.g. `docs/loops/<name>-loop.md`) for reuse.

## Anti-patterns (these break autonomous runs)

| Anti-pattern | Why it fails | Fix |
|---|---|---|
| Thin / missing DO-NOT section | Agent expands scope every iteration | Fence design-only beads, branches, and untouchables explicitly |
| "Build it" with no verification method | Agent claims done without proof | Per-criterion, runnable verification; name the `grounded` hook |
| Swarm before the core lands | Headless workers collide on shared files | Gate fan-out behind the keystone bead; one worktree per worker |
| No land-the-plane step | Work never reaches the remote; lost on crash | Mandate add→sync→commit→sync→push every iteration |
| Generic prompt, no project reality | Agent flails on conventions/paths | Mirror the repo's stack, trailer, and AGENTS.md protocol |
| Design-only beads not fenced | Agent builds OAuth/payments/infra it shouldn't | "Deepen design notes only — do NOT build" per design bead |
| Vague exit | Session runs forever or stops early | Convergence condition + morning report + "no PR" |

## Reference files

- `references/churn-prompt-template.md` — the fill-in-the-blanks skeleton, a library of optional toolchain blocks (masque / dogfood / tmux-swarm), and a full worked example.
