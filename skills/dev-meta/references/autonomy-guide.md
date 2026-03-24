# Autonomy Guide — When to Act vs. When to Ask

This reference defines Claude's decision-making boundaries at each phase. The goal is maximum throughput: act autonomously on implementation, surface only decisions that require human judgment.

## The Core Principle

**Act** when the decision is reversible and contained within the current bead.
**Ask** when the decision affects the spec, architecture, or another bead's interface.

If unsure, bias toward acting and explaining after — the user can course-correct on review. Blocking on a question costs more than a minor rework.

## Phase-Specific Autonomy

### Phase 1 (Problem Framing): LOW autonomy
This is fundamentally a human judgment phase. Claude contributes by asking probing questions, surfacing relevant context, and challenging assumptions. Claude does NOT frame the problem unilaterally.

### Phase 2 (Spec): MEDIUM autonomy
Claude can draft the spec, but the critical review loop requires genuine back-and-forth. Claude should attack its own spec aggressively before presenting it, but the user validates and accepts.

### Phase 3 (Architecture): MEDIUM autonomy
Claude can propose the architecture and dependency graph. But architectural tradeoffs (e.g., "do we add a new service or extend an existing one?") are decision points that need the user's call.

### Phase 4 (Beads): MEDIUM-HIGH autonomy
Claude can decompose and order beads. Present the bead list for review, but don't wait for approval on individual bead definitions — present the full list and let the user adjust.

### Phase 5 (Grind): HIGH autonomy
This is Claude's zone. For each bead:
- Make implementation decisions (naming, structure, patterns) without asking
- Write tests without asking what to test (the bead definition already says)
- Handle error cases using reasonable defaults
- Choose between equivalent approaches without deliberation

**Ask only when:**
- The implementation reveals a spec contradiction
- A dependency behaves differently than documented
- A tradeoff exists that could change the architecture
- You discover a security concern not covered in the spec

**Never ask about:**
- Variable/function naming (just be consistent with the codebase)
- File organization (follow existing patterns)
- Which test framework to use (match the project)
- Formatting/linting (match the project config)
- Import ordering, log levels, comment style

### Phase 6 (Integration): HIGH autonomy
Claude should assemble, test, and fix integration issues autonomously. Only surface issues that indicate an architecture problem (Phase 3 error), not bead-level bugs.

### Phase 7 (User Testing): LOW autonomy
The user is driving. Claude supports by setting up test environments, providing test scripts, and documenting findings.

### Phase 8 (Docs): HIGH autonomy
Claude drafts all documentation. Present for review, don't ask what to document — that should be obvious from the work done.

### Phase 9 (Merge): LOW autonomy
This is a gate the user controls.

### Phase 10 (Retro): MEDIUM autonomy
Claude can draft the retrospective with observations, but the user validates whether those observations match their experience.

## Communication Style During High-Autonomy Phases

When grinding (Phase 5), Claude should:
- Report progress, not ask questions: "Completed bead 3/7. Moving to bead 4."
- Explain decisions in commit messages, not in conversation
- Batch non-blocking observations for later: "Note for retro: the auth library's error types are poorly documented, cost ~30min of investigation"
- Only interrupt the flow for genuine blockers

When the user checks in during a grind, give a status update: what's done, what's next, any flags. Keep it terse.
