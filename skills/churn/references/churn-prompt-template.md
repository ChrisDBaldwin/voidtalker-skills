# Churn Prompt Templates

A fill-in-the-blanks skeleton, a library of optional toolchain blocks, and a full worked example. Pick the skeleton, splice in the blocks the project warrants, replace every `[PLACEHOLDER]`, and present the result in a single fenced block the user can paste into a fresh `--dangerously-skip-permissions` session.

---

## Skeleton

```
You are working autonomously on [REPO_PATH], running --dangerously-skip-permissions.
Self-impose discipline: prove before you claim, land the plane every iteration, never fake a result.

NORTH STAR
  [PRD_PATH] — read it FULLY first. Tracker: beads epic [EPIC_ID] ([`bd show EPIC_ID`], [`bd ready`]).

SETUP (once, first iteration)
1. `git status`; `git switch [BRANCH]` (it already exists[/is pushed] — do NOT recreate it); `git pull --rebase`.
2. `bd prime`; `bd ready`; read [PRD_PATH] in full.[ Skim read-only reference: [REF_PATHS] — NEVER modify them.]
3. [DOGFOOD_BLOCK — optional]
4. Create [PROGRESS_PATH]: a checkbox per acceptance criterion ([CRITERIA_IDS]) + an iteration log. This is your monotonic memory.[ Do NOT clobber [EXISTING_PROGRESS].]

SCOPE — DO ([BUILD_PHASE])
Work the beads in dependency order. [KEYSTONE_BEAD] is the keystone and gates the rest; start there.
  - Stack/conventions: [STACK_NOTES — where files go, what to mirror, blessed libs].
  - [BEAD_ID] — [what + acceptance criteria refs]
  - [BEAD_ID] — [...]
  [MASQUE_BLOCK — optional]
  [TMUX_SWARM_BLOCK — optional]

SCOPE — DO NOT
  - Do NOT build [DESIGN_ONLY_BEADS]. They are DESIGN ONLY — only DEEPEN their design notes
    (`bd update <id> --design`) / write design docs. No [FORBIDDEN_BUILD: e.g. OAuth/Postgres/payments].
  - Do NOT modify [READ_ONLY_REPOS] (reference only).
  - Do NOT touch [PROTECTED_BRANCHES]; do NOT merge; do NOT force-push. Work only on [BRANCH].
  - Do NOT change [UNTOUCHABLES: schema / public contracts / other components].

GROUNDING (non-negotiable — the `grounded` Stop hook enforces this)
Check off a criterion ONLY after running its stated verification and pasting real evidence into [PROGRESS_PATH]:
  - [CRITERION]: [runnable verification — start the thing, call it, paste output]
  - ...
If a tool is missing, file a bead and mark the criterion blocked — never assert an unrun result.

PER-ITERATION LOOP
1. Read [PROGRESS_PATH]; pick the next unchecked criterion (dependency order).
2. Implement the minimum change. 3. VERIFY by its method; capture evidence in progress.
4. `bd update --status` / `bd close` the matching bead.
5. LAND THE PLANE: git add → bd sync → git commit (end with `[COAUTHOR_TRAILER]`) → bd sync → git push [BRANCH].
6. If blocked >~2 iterations: write the blocker, `bd create` a follow-up, move on.

EXIT
Stop when [BUILD_PHASE] criteria are checked-or-blocked-and-filed[ and design-only beads carry buildable notes].
Write a MORNING REPORT at the top of [PROGRESS_PATH]: done+verified (evidence refs), blocked+why (bead IDs),
the single most important decision to review, and the suggested next first move. Confirm the branch is pushed
and `git status` is clean. Do NOT open a PR — leave that to me.
```

---

## Optional toolchain blocks

Splice these in where the skeleton marks them. Omit any the project doesn't warrant.

### DOGFOOD_BLOCK (repos with the always-on audience / masques)
```
4. DOGFOOD while you build: `/audience seat` (start the always-on local collector), then `/don [MASQUE]`.
   Your session then generates real telemetry the judge can score — run `/performance` on yourself
   periodically as a sanity check.
```

### MASQUE_BLOCK (any masque-bearing repo)
```
  Don a fitting lens for the work and re-don as it shifts: [MASQUE] for [build class];
  Architect for design-only beads; Firekeeper for DB/schema; Reviewer for review passes;
  Grinder for closing out.
```

### TMUX_SWARM_BLOCK (when leaf beads parallelize)
```
PARALLELIZE once the keystone ([KEYSTONE_BEAD]) and shared scaffolding have landed: the leaf beads
([INDEPENDENT_BEADS]) are file-disjoint. Use your tmux headless-claude capability to run them concurrently.
Per worker: claim ONE bead (`bd update <id> --status=in_progress`); isolate it in its own worktree
(`git worktree add ../<slug> [BRANCH]`) so workers don't collide; it dons a masque, does the work, VERIFIES,
and LANDS ITS OWN PLANE (commit + bd sync + push), then reports via the bead. Keep edits to shared files
(e.g. [SHARED_FILE]) serialized; only fan out the independent beads. You (orchestrator) reconcile
worktrees/merge-forward and keep [PROGRESS_PATH] current. Never swarm before the keystone lands.
```

---

## Worked example — Masques v1.2 MCP server

A real churn prompt. Note: keystone-gated build, design-only fences, dogfood + masque, tmux fan-out behind the core.

```
You are working autonomously on /Users/chris/git/masques, running --dangerously-skip-permissions.
Self-impose discipline: prove before you claim, land the plane every iteration, never fake a result.

NORTH STAR
  docs/prd-v1.2-mcp-server.md — Masques as an MCP server. Read it FULLY first (incl. the OpenGander
  reuse map + appendix). Tracker: beads epic masques-zyu (`bd show masques-zyu`, `bd ready`).

SETUP (once)
1. `git status`; `git switch prd-v1.2-mcp-server` (exists + pushed — do NOT recreate); `git pull --rebase`.
2. `bd prime`; `bd ready`; read the PRD. Skim read-only: ~/git/opengander/services/mcp-server/src/opengander_mcp/
   (server.py, jwt_auth.py, scopes.py). NEVER modify the opengander repo.
3. DOGFOOD: `/audience seat`, then `/don codesmith` (methodical builder lens for the Python work).
   Use `/performance` on yourself as a periodic check.
4. Create progress-v1.2-mcp.md: a checkbox per criterion (M1–M10) + iteration log. Do NOT clobber progress.md (v1.1).

SCOPE — DO (Phase A, BUILD)
Work in dependency order. masques-oru (the core) is the keystone; start there.
  - Stack: Python + FastMCP (fastmcp>=2.14), managed with uv; MIRROR ~/git/opengander/services/mcp-server,
    put the server at services/mcp/ (minus ClickHouse, minus auth — Phase A is unauth).
  - masques-oru — core: resolve (private ~/.masques over personas/), compose (lens+context = what don.md
    injects), list, inspect; expose a `masque` CLI. [M2,M3,M4,M7]
  - masques-b83 — FastMCP stdio server: tools list_masques/inspect_masque/don/doff. [M1–M4]
  - masques-0gg prompts · masques-vvw resources · masques-nsn score(→judge.sh, LOCAL only) · masques-6sc
    plugin shells out to the CLI (M7) · masques-0mf parity · masques-emq docs.
  Don a fitting lens and re-don as work shifts (Codesmith build; Architect/Firekeeper for design beads).
PARALLELIZE once oru + b83 land: 0gg/vvw/nsn/emq/0mf are file-disjoint. Spawn headless tmux claudes — one
  bead each, `bd update --status=in_progress`, isolated in `git worktree add ../mcp-<bead> prd-v1.2-mcp-server`,
  each dons a masque, verifies, lands its own plane, reports via the bead. Serialize server.py registration
  edits. Never swarm before oru lands.

SCOPE — DO NOT
  - Do NOT build Phase B/C (masques-46z Postgres, an2 OAuth-clone, og3 resource server, c3k deploy, ass Stripe,
    9n1 payouts). DESIGN ONLY — deepen `bd update --design` notes / docs. No OAuth, Postgres, Stripe, Solana/TigerBeetle.
  - Do NOT modify ~/git/opengander (read-only). Do NOT touch main/mvp/prd-v1.1-persistent-audience; no merge; no
    force-push; only prd-v1.2-mcp-server. Do NOT change the schema, personas, or TUI; don't break the plugin/evals/judge.

GROUNDING (the `grounded` Stop hook enforces this)
  - M1: start the stdio server and list tools with `npx @modelcontextprotocol/inspector` (or a scripted client); paste it.
  - M2–M6: call the tools (list ≥35 w/ private precedence; inspect Firekeeper; don Codesmith returns its lens+context;
    a prompt resolves; score returns a band); paste output.
  - M7: show the plugin path uses the CLI; paste the parity comparison. M8: register in a real client; show it working.
  Missing tool (uv/npx/duckdb) → file a bead, mark blocked, say so. Never assert an unrun result.

PER-ITERATION LOOP
  Read progress → next unchecked M (dep order) → minimum change → VERIFY by running → evidence in progress →
  bd update/close → LAND THE PLANE: git add → bd sync → commit (end with
  `Co-Authored-By: Claude Opus 4.8 (1M context) <noreply@anthropic.com>`) → bd sync → git push the branch.
  Blocked >~2 iterations → write blocker, `bd create` follow-up, move on.

EXIT
  Stop when M1–M8 are checked-or-blocked-and-filed and Phase B/C beads carry buildable design notes. Write a MORNING
  REPORT atop progress-v1.2-mcp.md (done+verified w/ evidence, blocked+why w/ bead IDs, the single most important
  decision to review, suggested next first move). Confirm branch pushed + `git status` clean. Do NOT open a PR.
```
