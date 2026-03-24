---
name: dev-meta
description: "Development lifecycle skill for building software features, services, and systems. Use this skill whenever starting new work — features, services, integrations, infrastructure changes, or any multi-step engineering task. Trigger on phrases like 'let's build', 'new feature', 'I want to implement', 'start working on', 'spec this out', 'design this', 'plan this work', or any request that involves going from idea to shipped code. Also trigger when the user asks to decompose work, create a PR, review architecture, or plan a rollout. This skill defines the full lifecycle from problem framing through retrospective. Do NOT use for one-off scripts, quick config changes, or questions that don't involve a development lifecycle."
---

# Dev Meta — Development Lifecycle

This skill governs how Claude operates as an engineering partner across the full development lifecycle. The process has ten phases. Each phase has a clear entry condition, exit condition, and set of outputs. Claude should know where in this lifecycle the current work sits and operate accordingly.

The user is a senior engineer who works hands-on but is time-constrained. The goal of this skill is to maximize Claude's autonomous effectiveness at each phase so the user can focus on decisions that require human judgment — problem framing, architectural tradeoffs, acceptance — while Claude handles the grinding, testing, documentation, and integration work with minimal back-and-forth.

## The Ten Phases

### Phase 1: Frame the Problem — Grounded in Reality

**Entry:** An idea, request, pain point, or opportunity exists.
**Exit:** A clear problem statement with constraints and success criteria.

Before writing any code or spec, establish:
- What problem are we solving and for whom?
- What does success look like? (measurable where possible)
- What are the constraints? (time, infra, dependencies, budget)
- What's the scope boundary? (what are we explicitly NOT doing?)
- Is this the right problem? (does solving it actually move the needle?)

Claude's role: Ask the hard questions. Challenge assumptions. If the user says "I want to build X," ask what problem X solves. If the answer is vague, push for specificity. Reference existing context (repos, prior work, architecture) to ground the framing in what actually exists today.

Do not proceed to Phase 2 until the problem statement would make sense to someone with no context.

### Phase 2: Spec — Iteration and Completion

**Entry:** A validated problem statement from Phase 1.
**Exit:** A spec that has survived critical review.

Write the spec, then attack it. The spec is not done when it's written — it's done when it's survived scrutiny. This is a two-sub-phase process:

**2a. Draft the spec.** Include:
- Problem statement (from Phase 1)
- Proposed solution with rationale
- API contracts / interface definitions where relevant
- Data model changes
- Dependencies and integration points
- Risks and open questions
- Out of scope (restate from Phase 1)

**2b. Critical review loop.** Claude should actively try to break the spec:
- What failure modes exist?
- What happens at scale?
- What are the security implications?
- Does this conflict with existing systems?
- Is this overengineered for the problem?
- Is this underengineered for likely future needs?

Iterate until the spec is strong. Present concerns directly — don't soften them. The user wants honest review, not validation.

See `references/spec-template.md` for the spec format.

### Phase 3: Architecture / Mapping / Graph

**Entry:** An accepted spec.
**Exit:** A dependency graph and integration map.

Zoom out from the spec and map how it fits into the system:
- Dependency graph: what depends on what?
- Integration points: where does this touch existing services/systems?
- Interface contracts: what are the inputs/outputs at each boundary?
- Sequencing constraints: what must be built before what?

This phase is where "decompose into beads" gets its ordering. The architecture map determines which beads are load-bearing and which can be parallelized.

For infrastructure work (CloudFormation stacks, EKS/ECS configs, OTel pipelines), map the resource dependency graph explicitly. For application work, map the call graph and data flow.

Output: A document or diagram (mermaid is fine) showing components, dependencies, and build order.

### Phase 4: Beads

**Entry:** A dependency graph from Phase 3.
**Exit:** A prioritized list of discrete, testable work units.

Decompose the architecture into beads — the smallest units of work that are independently meaningful and testable. Each bead should have:
- A clear description of what it does
- Inputs and outputs
- Test criteria (how do we know it works?)
- Dependencies (which other beads must complete first?)
- Estimated complexity (S/M/L)

**Prioritization matters.** Order beads by:
1. Dependency chain (blockers first)
2. Risk (high-uncertainty beads early — fail fast)
3. Value (if two beads are independent, do the higher-value one first)

Output: A numbered, ordered list of beads with their metadata. This becomes the work plan.

### Phase 5: Grind Beads / Local Env

**Entry:** A prioritized bead list.
**Exit:** Each bead implemented, tested locally, passing.

This is where Claude should operate with maximum autonomy. For each bead:
1. Implement the code
2. Write tests (unit tests at minimum)
3. Run tests locally
4. Verify the bead meets its stated test criteria
5. Move to the next bead

Claude should grind through beads sequentially (respecting dependency order) and surface decisions only when:
- The implementation reveals a spec gap or contradiction
- A tradeoff exists that affects architecture (not just implementation detail)
- An external dependency is broken or behaves unexpectedly

For everything else — naming, internal structure, test organization, error handling patterns — Claude should make the call and move on. Explain decisions in commit messages, not in conversation.

**Local environment matters.** All work should be testable locally before any branch/PR activity. If it can't run locally, fix that first.

### Phase 6: Branch PR → Integration Test

**Entry:** All beads for a logical unit are passing locally.
**Exit:** A PR with passing integration tests.

Assemble the beads into a branch and verify they compose correctly. This is where "works in isolation but breaks when connected" gets caught.

- Create the branch with clean, logical commits
- Run the full integration test suite
- If integration tests fail, identify whether it's a bead-level bug or an interface mismatch (Phase 3 error)
- Fix and re-verify

The PR should be ready for review: clean diff, descriptive commit messages, any necessary migration notes.

### Phase 7: User Testing (Canary / Rollout)

**Entry:** A PR with passing integration tests.
**Exit:** User-validated behavior in a real or near-real environment.

The shape of this phase depends on the deployment context:
- **For services with users:** Canary deploy, feature flag, or staged rollout
- **For infrastructure:** Apply to staging/dev environment first
- **For solo projects:** Deploy to a real environment and exercise the happy path and key edge cases manually
- **For libraries/tools:** Use the tool in a real workflow, not just tests

The point is: code that passes tests is not code that works. This phase catches the gap between "correct" and "useful."

### Phase 8: Docs Check + Update

**Entry:** User-validated feature.
**Exit:** All documentation reflects the current state.

Do this BEFORE merge. Documentation after merge is where documentation goes to die.

- README updates
- API documentation
- Architecture diagrams (update Phase 3 outputs if they've drifted)
- Runbooks / operational docs for infrastructure changes
- Inline code comments where behavior is non-obvious
- CHANGELOG entry

Claude should draft all documentation updates and present them for review alongside the PR.

### Phase 9: Merge / Accept

**Entry:** PR with passing tests, user validation, and updated docs.
**Exit:** Code merged to main.

This is a gate, not a phase. If Phases 6-8 are done well, this is a formality:
- Final review of the diff
- Squash or merge per project convention
- Verify CI passes on main after merge
- Tag/release if applicable

### Phase 10: Retrospective + Ongoing Observability

**Entry:** Merged code in production.
**Exit:** This phase doesn't close — it transitions into monitoring.

**Retrospective (do once, immediately after merge):**
- What surprised us?
- What took longer than expected and why?
- What would we change about the process?
- Did the spec hold up or did we deviate? If we deviated, was the spec wrong or did we make a mistake?
- What did we learn that should feed back into future Phase 1 framing?

**Ongoing observability (continuous):**
- Are the metrics/traces/logs we expected actually flowing?
- Is the feature behaving as designed under real load?
- Are there error patterns we didn't anticipate?
- When does this need revisiting?

The retrospective feeds back into Phase 1. Observability feeds into future problem framing. The loop doesn't close — it spirals.

---

## How Claude Should Use This Skill

**At the start of any multi-step development task**, identify which phase the work is in. If the user jumps straight to "build me X," gently pull back to Phase 1 unless the framing is already clearly established.

**During grinding (Phase 5)**, operate with maximum autonomy. Don't ask permission for implementation decisions. Do ask when something contradicts the spec or reveals an architectural concern.

**Scale formality to the work.** A five-line config change doesn't need a full spec. A new service does. Use judgment. If in doubt, ask: "Would skipping this phase cost us time later?"

**Track where we are.** If a conversation spans multiple phases, note the transition. "We've got a solid spec — moving to architecture mapping" helps the user maintain context.

**Reference sub-skills** for phase-specific depth. See `references/` for templates and detailed guidance on individual phases.
