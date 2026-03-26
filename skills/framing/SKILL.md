---
name: framing
description: "Structured problem framing for dev-meta Phases 1-3. Use when starting new work that needs a problem statement, spec, or architecture map before building. Lighter than brainstorming — no rigid checklists, just the right questions in the right order. Trigger on 'frame this', 'what are we building', 'scope this out', 'before we build', 'what's the problem', or any request to understand a problem before solving it. Outputs a problem statement with scope boundary that feeds into recursive-prd. Do NOT use for work that's already framed — go straight to recursive-prd if the problem and scope are clear."
---

# Framing

This skill covers dev-meta Phases 1-3: problem framing, spec, and architecture mapping. It turns a vague idea into a clear problem statement with binary acceptance criteria, ready for a grinder to execute via recursive-prd.

This is NOT brainstorming. There is no rigid checklist, no mandatory visual companion offer, no multi-document pipeline. This is structured questioning that converges on a spec.

## When to Use This

- The user has an idea but the problem isn't clearly defined yet
- The user wants to start building something but hasn't scoped it
- A bead exists but needs a PRD before it can be ground
- The user says anything about "framing," "scoping," or "before we build"

## When NOT to Use This

- The problem is already clearly framed — go straight to `recursive-prd`
- The user wants to execute an existing plan — go to Phase 5 grinding
- It's a one-off fix or config change — just do it

## The Process

### Phase 1: Frame the Problem

Ask these questions, one at a time, in this order. Skip questions the user has already answered. Don't ask questions you can answer by reading the codebase.

1. **What problem are we solving?** Not "what are we building" — what pain exists today?
2. **For whom?** Who benefits? What do they care about?
3. **What exists today?** Read the codebase. What's the current state?
4. **What does success look like?** Binary if possible. "Users can X" not "X is better."
5. **What are the constraints?** Time, tech, dependencies, infra.
6. **What are we NOT doing?** This is the most important question. Scope boundary.

The scope boundary is load-bearing. Without it, the grinder will expand scope on every iteration. Be explicit. List specific things that are adjacent but out of bounds.

### Phase 2: Spec (if needed)

For non-trivial work, draft a spec after framing. The spec has:

- Problem statement (from Phase 1)
- Proposed approach with rationale
- Acceptance criteria (binary pass/fail)
- What we're NOT doing (from Phase 1)

Then attack it:
- What failure modes exist?
- Is this overengineered for the problem?
- Does this conflict with existing systems?
- What's the simplest version that solves the problem?

One round of critical review. If the spec survives, it's done. No review loops, no subagent dispatches.

### Phase 3: Architecture Map (if needed)

For work that touches multiple components or has dependency chains:

- What depends on what? Draw the graph.
- What must be built before what?
- What can be parallelized?
- Where are the integration points?

For simple work (single component, no dependencies), skip this phase entirely.

## Output

The framing process produces one of:

**For simple work:** A problem statement and scope boundary, communicated in conversation. Proceed directly to implementation or recursive-prd.

**For complex work:** A problem statement, spec with acceptance criteria, and dependency map. This feeds directly into recursive-prd to generate a grindable PRD.

## Relationship to Other Skills

- **recursive-prd:** Framing produces the input that recursive-prd consumes. Framing answers "what and why." Recursive-prd answers "how to verify it's done."
- **dev-meta:** Framing IS Phases 1-3 of dev-meta. It's the concrete implementation of those phases.
- **clarity:** During spec review, apply the clarity lens to proposed boundaries — will they produce digestible, self-contained units?

## Key Principle

The fastest way to waste time is to build the wrong thing well. Framing exists to make sure we're building the right thing before any code is written. But framing itself shouldn't take longer than the building. Scale formality to the work. A 5-minute framing conversation for a 2-hour task. A 30-minute framing session for a week of work. Never longer than 1:5 ratio.
