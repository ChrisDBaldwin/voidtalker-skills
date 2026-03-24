---
name: recursive-prd
description: "Generate PRDs structured for autonomous recursive execution by Claude Code, Ralph loops, or /loop. Use this skill whenever someone wants to create a PRD, product requirements document, development plan, or spec that an AI agent will execute autonomously. Also trigger when someone says 'make it buildable', 'set up a loop', 'autonomous development', 'write a PRD for Claude Code', 'plan this for ralph', 'I want to walk away and let it build', or any variation of wanting a structured plan that converges to done without human intervention per iteration. This skill produces PRDs with binary acceptance criteria, a recursive loop prompt, a monotonic progress log, and CLAUDE.md context — everything needed to go from spec to autonomous execution."
---

# Recursive PRD Generator

This skill produces PRDs designed for one thing: **autonomous convergence**. The output is a document that an AI coding agent can execute in a recursive loop — one criterion per iteration, monotonic progress, clean exit when done.

This is not a traditional PRD. Traditional PRDs describe what to build for humans to interpret. A recursive PRD describes what "done" looks like in terms a machine can verify, then provides the execution protocol for getting there without human steering at each step.

## When to Use This

- The user wants to build something and have Claude Code (or any agent loop) execute it autonomously
- The user has an idea, a repo, or existing work and wants to set up a development loop
- The user says anything about PRDs, specs, plans, or requirements in the context of autonomous/agentic development
- The user wants to go from conversation to "make it so" — this skill bridges that gap

## The Recursive PRD Structure

Every recursive PRD has six sections. Each is required. Read `references/prd-template.md` for the full template with commentary.

### 1. Problem Statement

Ground the work in reality. What are we building, for whom, why now, and what are we explicitly not doing? This section exists so the agent understands the *purpose* behind the criteria — it needs judgment context for decisions the criteria don't cover.

Include:
- What problem this solves and for whom
- Current state (what exists today)
- Constraints (time, infra, dependencies, budget, taste)
- Scope boundary (what we are NOT doing — be explicit)

The scope boundary is load-bearing. Without it, autonomous agents will expand scope on every iteration. Negative constraints are more important than positive ones for loop stability.

### 2. Design Direction (if applicable)

For frontend, visual, or brand work, establish the aesthetic guardrails. For backend/infra work, establish the architectural constraints. The agent needs enough direction to make taste-level decisions without asking.

This section prevents the most common failure mode of autonomous frontend work: the agent produces something technically correct but aesthetically generic. Pin the visual identity, name the anti-patterns, identify the signature element.

For non-visual work, this section becomes "Architecture Constraints" — what patterns to follow, what libraries are blessed, what conventions exist.

### 3. Acceptance Criteria

**This is the core of the recursive PRD.** Every criterion must be:

- **Binary.** Pass or fail. No "mostly done" or "looks good." If you can't write a clear pass/fail check, the criterion is too vague.
- **Ordered by priority.** The agent works top to bottom. Higher criteria are addressed first.
- **Ordered by dependency.** If criterion B depends on criterion A existing, A comes first.
- **Independently verifiable.** The agent can check whether this passes without human input. "Looks good" is not verifiable. "Renders without console errors at 4 viewport widths" is.
- **Scoped to one concern.** Each criterion is one thing. "Navigation works and looks good" is two criteria.

Use tiered prefixes to group criteria by domain and priority:

```
### Tier 1: Core Functionality (Priority 1)
- [ ] C1: [description of criterion and how to verify it]
- [ ] C2: ...

### Tier 2: Polish (Priority 2)
- [ ] P1: ...
- [ ] P2: ...

### Tier 3: Nice-to-Have (Priority 3)
- [ ] N1: ...
```

The agent works through Tier 1 completely before starting Tier 2. Within a tier, it takes the first unchecked item unless dependency order dictates otherwise.

**Writing good criteria — common failure modes:**

| Bad Criterion | Why It Fails | Better Version |
|---|---|---|
| "Page looks professional" | Subjective, unverifiable | "Page uses design tokens from S1, no inline styles, consistent spacing scale" |
| "API is fast" | No threshold | "API responds in <200ms at p95 for the /sessions endpoint with <1000 rows" |
| "Error handling works" | Too broad | "Invalid JWT returns 401 with JSON body `{error: 'invalid_token'}`, no stack trace" |
| "Tests pass" | Which tests? | "All files matching `**/*.test.ts` pass via `npm test` with 0 failures" |
| "Mobile responsive" | At what breakpoints? | "Layout renders without horizontal scroll at 320px, 768px, 1024px, 1440px" |

### 4. Recursive Loop Prompt

This is the literal prompt the agent receives on each iteration. It must be self-contained — the agent starts each iteration with fresh context (no memory of prior iterations except what's in files).

The loop prompt follows this structure:

```
You are working on [project]. Your goal is convergence toward the acceptance criteria in [path to PRD].

On each iteration:
1. Read the acceptance criteria.
2. Read progress.md for what's been done.
3. Assess current state against criteria.
4. Identify the single highest-priority unchecked criterion.
5. Implement the minimum change to satisfy it.
6. Verify the criterion passes.
7. Update progress.md.
8. Commit with format: "[prefix] <criterion-id>: <description>"
9. If all criteria pass, output EXIT_SIGNAL.

Do NOT: [list of guardrails specific to this project]
```

Adapt the guardrails to the project. Common ones:
- Don't refactor unrelated code
- Don't change files outside the scope boundary
- Don't introduce dependencies without documenting why
- Don't skip verification before committing

See `references/loop-prompt-template.md` for the full template with variants for different project types.

### 5. Progress Log Template

The progress log (`progress.md`) is the monotonic memory layer. It serves three purposes:

1. **State tracking:** Which criteria are done, which are open, which are blocked.
2. **Decision log:** Why the agent made the choices it made — this is the audit trail.
3. **Handoff context:** When a fresh agent picks up the loop, this is what it reads first.

The progress log must be updated on every iteration, even if the iteration fails. Failed iterations are valuable signal — they prevent the next iteration from trying the same thing.

### 6. CLAUDE.md Context

The CLAUDE.md additions give every Claude Code session (including loop iterations) the project context it needs without re-reading the entire PRD. This is the "what to know, what to touch, what not to touch" briefing.

Must include:
- One-paragraph project description
- Architecture summary (what's where)
- Conventions (naming, styling, patterns)
- Guardrails (what not to touch and why)
- Pointer to the PRD and progress.md

## Process: How to Generate a Recursive PRD

### Step 1: Understand the Project

Before writing anything, gather:
- What exists today? (repo structure, current state, tech stack)
- What does "done" look like? (the user's vision)
- Who is it for? (audience shapes acceptance criteria)
- What's off-limits? (scope boundary)

Search past conversations for context. Check if the user has discussed this project before. The more context you have, the tighter the criteria.

If the project is vague, push for specificity. "Make the frontend better" needs to become "the marketing site should communicate X to Y audience with Z visual identity." The PRD can't be tighter than the user's intent.

### Step 2: Draft the Criteria

This is where most of the thinking happens. Transform the user's intent into binary pass/fail checks.

Start with the obvious ones (does it render, does it work), then add the quality ones (performance, accessibility, consistency), then the polish ones (meta tags, documentation, edge cases).

For each criterion, mentally simulate: "If I were a fresh Claude Code instance reading this criterion and looking at the codebase, would I know exactly what to check and what counts as passing?" If no, rewrite it.

### Step 3: Write the Loop Prompt

Customize the template from `references/loop-prompt-template.md` for the specific project. The key customization points are:
- The path references (where is the PRD, where is progress.md)
- The commit prefix
- The project-specific guardrails (what not to touch)
- Any project-specific verification steps (run tests, check a URL, etc.)

### Step 4: Write the CLAUDE.md Context

Extract the minimum viable context from the PRD. The CLAUDE.md section should be readable in 30 seconds and give a new agent enough orientation to start working.

### Step 5: Assemble and Present

Combine all sections into a single markdown file. Present it to the user along with:
- The progress.md template (ready to create)
- The CLAUDE.md additions (ready to paste)
- A "Getting Started" section with the exact steps to begin execution

The output should be something the user can take directly to their terminal and start running. No additional interpretation required.

## Adapting to Project Type

The template is general but different project types emphasize different things:

**Frontend / Visual Work**
- Design Direction section is critical — pin aesthetics early
- Criteria should include viewport breakpoints, performance scores, accessibility checks
- Verification often means "run the dev server and check the browser"

**Backend / API Work**
- Design Direction becomes Architecture Constraints
- Criteria focus on contracts (request/response shapes), performance thresholds, error handling
- Verification means running tests and hitting endpoints

**Infrastructure / DevOps**
- Criteria focus on resource state (does the stack deploy, do health checks pass)
- Guardrails are extra important — autonomous agents touching infra can be destructive
- Include rollback steps in the loop prompt

**Content / Documentation**
- Criteria focus on completeness (all sections present), accuracy (matches current code), and structure
- Verification may require human review — flag criteria that can't be machine-verified and mark them as requiring a manual check in progress.md

**Full-Stack Features**
- Layer the criteria: data model → API → frontend → integration
- The dependency ordering within criteria matters more here than anywhere else
- Consider splitting into multiple PRDs if the feature crosses too many boundaries

## Reference Files

- `references/prd-template.md` — The full PRD template with inline commentary. Read this before generating any PRD.
- `references/loop-prompt-template.md` — Loop prompt variants for different project types and loop runners (Ralph, /loop, bare bash loop).
