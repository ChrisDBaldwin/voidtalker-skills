# Bead Decomposition Reference

## What Makes a Good Bead

A bead is the smallest unit of work that is independently meaningful and testable. Good beads have these properties:

**Atomic:** A bead does one thing. If you need the word "and" to describe it, it's probably two beads.

**Testable:** You can write a test that passes when the bead is done and fails when it isn't. If you can't write the test, the bead isn't well-defined.

**Bounded:** A bead has clear inputs and outputs. You know what goes in and what comes out.

**Estimable:** You can put a rough size on it (S/M/L). If you can't estimate it, you probably don't understand it well enough — break it down further or spike on it first.

## Bead Sizing

- **S (Small):** < 1 hour of focused work. Single function, config change, simple test, minor refactor.
- **M (Medium):** 1-4 hours. New module, API endpoint, integration with existing service, meaningful test suite.
- **L (Large):** 4-8 hours. New service skeleton, complex algorithm, data migration, significant refactor. If a bead is larger than L, decompose it further.

## Ordering Strategies

**Dependency-first:** If bead B depends on bead A, do A first. Map the dependency chain and execute in topological order.

**Risk-first:** When two beads are independent, do the riskier one first. If it's going to blow up the plan, find out early. This includes:
- Beads that depend on unproven assumptions
- Beads that involve unfamiliar technology
- Beads where the spec is weakest

**Value-first:** When two beads are independent and similar risk, do the one that delivers more user-facing value first. This creates optionality — if you run out of time, you shipped the more important thing.

## Spike Beads

Sometimes you can't estimate a bead because you don't know enough. Create a spike bead: a time-boxed investigation with a specific question to answer. The output of a spike is knowledge, not code.

Format: "Spike: [question to answer] (time box: [hours])"

Example: "Spike: Can ClickHouse handle our projected write volume with the proposed schema? (time box: 2h)"

The spike's output feeds back into bead decomposition — you now know enough to break down the real work.

## Anti-Patterns

**The mega-bead:** "Implement the authentication system." This is a project, not a bead. Break it down.

**The phantom bead:** "Think about error handling." This isn't testable. Make it concrete: "Add error handling for network timeouts in the API client, returning structured errors."

**The yak-shave bead:** "Upgrade Node.js to v20." Only if it's actually blocking your work. Don't let infrastructure cleanup sneak into feature work without explicit framing.

**The coupled bead:** "Add the endpoint and update the dashboard." These are two beads that happen to be related. Separate them so they can be tested independently.
