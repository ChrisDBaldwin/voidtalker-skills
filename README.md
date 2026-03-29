# Voidtalker Skills

Portable development skills for autonomous AI-driven workflows in [Claude Code](https://claude.com/claude-code).

## Skills

### dev-meta

Full development lifecycle skill — from problem framing through retrospective. Defines ten phases of engineering work with clear entry/exit conditions, autonomy levels, and outputs at each stage.

Use when starting features, services, integrations, or any multi-step engineering task.

### recursive-prd

Generates PRDs structured for autonomous recursive execution. Produces documents with binary acceptance criteria, recursive loop prompts, monotonic progress logs, and CLAUDE.md context — everything needed for an AI agent to converge from spec to done without human intervention per iteration.

Use when you want to write a spec and walk away while Claude Code builds it.

### clarity

Cognitive-load lens for code review and writing. Simulates a reader encountering code for the first time and surfaces friction through two questions: where does the logic actually live, and what do I need to remember right now?

Required by convention during code review. Recommended during writing.

### framing

Structured questioning for dev-meta Phases 1-3 — lighter than a full brainstorming session. Produces a problem statement and scope boundary that feeds into recursive-prd.

Use when you have an idea that needs sharpening before it becomes a spec.

### intent

Interview skill that probes until 95% confidence about what you actually want — not what you think you should want. Works at any phase: project kickoff, mid-build, when stuck, before shipping. Catches drift, cargo-culting, and the gap between stated goals and real goals.

Use when you want a reality check, or when something about the current direction feels off.

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
