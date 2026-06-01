# Voidtalker Skills

Portable development skills for autonomous AI-driven workflows in [Claude Code](https://claude.com/claude-code).

## Skills

### dev-meta

Full development lifecycle skill — from problem framing through retrospective. Defines ten phases of engineering work with clear entry/exit conditions, autonomy levels, and outputs at each stage.

Use when starting features, services, integrations, or any multi-step engineering task.

### recursive-prd

Generates PRDs structured for autonomous recursive execution. Produces documents with binary acceptance criteria, recursive loop prompts, monotonic progress logs, and CLAUDE.md context — everything needed for an AI agent to converge from spec to done without human intervention per iteration.

Use when you want to write a spec and walk away while Claude Code builds it.

### churn

Forges a launch prompt for a fresh, bypass-permissions Claude Code session to autonomously churn through an existing backlog of beads — wiring in the full toolchain: masques (donned cognitive identities), tmux headless-claude fan-out, and the voidtalker skills. The "go" button after work is decomposed into beads.

Use when you want a self-contained prompt to paste into a fresh session and walk away while it grinds the backlog (solo or as a swarm). Pairs with recursive-prd (writes the spec) and ship (lands the result).

### clarity

Cognitive-load lens for code review and writing. Simulates a reader encountering code for the first time and surfaces friction through two questions: where does the logic actually live, and what do I need to remember right now?

Required by convention during code review. Recommended during writing.

### framing

Structured questioning for dev-meta Phases 1-3 — lighter than a full brainstorming session. Produces a problem statement and scope boundary that feeds into recursive-prd.

Use when you have an idea that needs sharpening before it becomes a spec.

### intent

Interview skill that probes until 95% confidence about what you actually want — not what you think you should want. Works at any phase: project kickoff, mid-build, when stuck, before shipping. Catches drift, cargo-culting, and the gap between stated goals and real goals.

Use when you want a reality check, or when something about the current direction feels off.

### grounded

Epistemic grounding standard, enforced automatically via a Stop hook. Catches Claude making claims without proof — "this works" without running it, "this fixes the bug" without reproducing it, assertions about code behavior without reading the code. When triggered, the hook blocks the response and redirects Claude to prove its claims before delivering them.

Not invoked manually — runs on every response. The skill defines the standard; the hook enforces it.

### ship

Mechanical Phase 6 of the dev-meta lifecycle — merge worktree, clean artifacts, close beads, push. The last mile after review is done.

Use when the work is reviewed and ready to land.

## Install

```
claude plugin marketplace add --source github --repo ChrisDBaldwin/voidtalker-skills
```

Then install the plugin from the marketplace.

## License

MIT
