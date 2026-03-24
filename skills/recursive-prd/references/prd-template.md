# Recursive PRD Template

Copy this template and fill in each section. Commentary in `<!-- -->` blocks explains what goes where — delete the commentary when done.

---

```markdown
# PRD: [Project Name]

**Project:** [repo path or project identifier]
**Author:** [name]
**Date:** [date]
**Loop Mode:** Recursive single-goal convergence
**Exit Condition:** All acceptance criteria pass

---

## Problem Statement

<!-- 
Ground the work. Answer these questions in prose, not bullets:
- What problem are we solving and for whom?
- What exists today? (current state of the code/product)
- What does success look like?
- What are the constraints? (time, tech, dependencies, budget, taste)
-->

[Describe the problem, current state, and why this work matters now.]

### Audience

<!-- Who will use or see the output of this work? Be specific about their technical level and what they care about. This shapes how criteria are written. -->

- **Primary:** [who and what they care about]
- **Secondary:** [who and what they care about]

### Constraints

<!-- Hard limits that the agent must respect. -->

- [Constraint 1]
- [Constraint 2]

### Scope Boundary — What We Are NOT Doing

<!-- 
This section is critical for loop stability. Without it, the agent will expand scope.
Be explicit. List specific things that are adjacent to the work but out of bounds.
-->

- [Explicitly out of scope item 1]
- [Explicitly out of scope item 2]

---

## Design Direction

<!-- 
For frontend/visual work: pin the aesthetic. Name the tone, typography, colors,
signature elements, and anti-patterns. The agent needs enough to make taste decisions.

For backend/infra work: rename this section "Architecture Constraints" and describe
the patterns, libraries, conventions, and boundaries the agent must follow.

Delete this section entirely if it doesn't apply (rare — most projects have conventions).
-->

### Identity / Tone

[Describe the feel. Not just "professional" — what *kind* of professional?]

### Typography

[Display font, body font, monospace font if applicable. Or: "match existing"]

### Color

[Primary, accent, semantic colors. Or: "derive from existing design tokens"]

### Signature Element

[What's the one thing someone remembers? A logo treatment, an animation, a layout pattern?]

### Anti-Patterns

[What must this NOT look like? Name specific things to avoid.]

---

## Acceptance Criteria

<!--
Rules for writing criteria:
1. Binary pass/fail — no ambiguity
2. Ordered by priority (tier 1 before tier 2)
3. Ordered by dependency within tiers (if B needs A, A comes first)
4. Independently verifiable by the agent (no "looks good")
5. One concern per criterion

Use tiered prefixes: C = core, P = polish, N = nice-to-have
Or use domain prefixes: M = marketing, D = dashboard, S = system, etc.
-->

### Tier 1: [Domain/Priority Name] (Priority 1)

- [ ] **C1:** [Criterion description. Include what "pass" means — what to check, what the expected state is.]
- [ ] **C2:** [Next criterion.]
- [ ] **C3:** [Next criterion.]

### Tier 2: [Domain/Priority Name] (Priority 2)

- [ ] **P1:** [Criterion.]
- [ ] **P2:** [Criterion.]

### Tier 3: [Domain/Priority Name] (Priority 3)

- [ ] **N1:** [Criterion.]

---

## Recursive Loop Prompt

<!--
This is the literal prompt fed to the agent on each iteration.
Customize the [bracketed] values for your project.
See references/loop-prompt-template.md for variants.
-->

```
You are working on [project name]. Your goal is convergence toward the acceptance criteria defined in the PRD at [path to this file].

On each iteration:

1. Read the PRD acceptance criteria.
2. Read progress.md for context on what's been done.
3. Assess the current state of the codebase against the criteria.
4. Identify the single highest-priority unchecked criterion.
   - Priority order: [list tier ordering, e.g., C1–C8, then P1–P4, then N1–N2].
   - Within a tier, prefer criteria that unblock others.
5. Implement the minimum change to satisfy that criterion.
6. Verify the criterion passes ([describe how — run tests, check browser, hit endpoint, etc.]).
7. Update progress.md with:
   - Which criterion was addressed
   - What was changed (files, approach)
   - Any decisions made and why
   - Any new risks or blockers discovered
8. Commit with message format: "[PREFIX] <criterion-id>: <short description>"
9. If all criteria are checked, output EXIT_SIGNAL.
   If a criterion cannot be satisfied due to a blocker outside your control,
   document it in progress.md and move to the next criterion.

Do NOT:
- [Project-specific guardrail 1]
- [Project-specific guardrail 2]
- [Project-specific guardrail 3]
- Skip verification before committing
- Introduce new dependencies without documenting the rationale in progress.md

[Optional: project-specific context the agent needs on every iteration,
e.g., design guidance, environment setup commands, test commands]
```

---

## Progress Log

<!--
Create this file at [path] in the project. The agent updates it on every iteration.
-->

Create `progress.md` at the project root:

```markdown
# [Project Name] — Progress Log

## Current State
<!-- Copy acceptance criteria checklist here -->
- [ ] C1: [description]
- [ ] C2: [description]
...

## Iteration Log

### Iteration 1 — [date]
**Criterion:** [id]
**Changes:** [files modified, approach taken]
**Decisions:** [any tradeoffs or choices made]
**Blockers:** [anything that prevents completion]
**Status:** [PASS / PARTIAL / BLOCKED]
```

---

## CLAUDE.md Additions

<!--
Add this to the project's CLAUDE.md. Keep it short — this is the 30-second briefing,
not the full PRD. The agent reads this on every session start.
-->

Add to the project's CLAUDE.md:

```markdown
## [Project Name]

[One paragraph: what this project is and what it does.]

### Architecture
[2-4 lines: where things live, what the tech stack is, key integration points.]

### Conventions
[2-4 lines: patterns, naming, styling, tools.]

### What NOT to Touch
[List of files, systems, or areas that are off-limits and why.]

### Current PRD
See `progress.md` for current acceptance criteria state and iteration log.
```

---

## Getting Started

1. Copy this PRD into your project (suggested: project root or `docs/`).
2. Create `progress.md` from the template above.
3. Add the CLAUDE.md section to your existing CLAUDE.md (or create one).
4. Open Claude Code in the project directory.
5. For interactive first pass: `claude` → paste the recursive loop prompt → let it assess and begin.
6. For autonomous loop: configure your runner (Ralph, /loop, or bash loop) with the recursive loop prompt.

The first iteration will likely be an assessment pass — reading the codebase, checking what exists against criteria, and populating progress.md with starting state. That's correct. Convergence starts on iteration 2.
```
