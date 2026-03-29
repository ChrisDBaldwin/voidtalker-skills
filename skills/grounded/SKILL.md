---
name: grounded
description: "Epistemic grounding standard enforced by a Stop hook. Catches Claude making claims without proof — 'this works' without running it, 'this fixes the bug' without reproducing it, assertions about code behavior without reading the code. The hook blocks ungrounded responses and redirects Claude to prove before claiming. Not a skill to invoke — it runs automatically via the Stop hook. The skill defines the standard; the hook enforces it."
---

# Grounded

Every claim about what code does, whether something works, or what will happen must be backed by evidence visible in the conversation. No exceptions for confidence level. If you haven't verified it, you don't know it — qualify or prove.

The standard: **a reader of this transcript should be able to follow the chain from claim to evidence without trusting Claude's word for anything.**

## What "Grounded" Means

A claim is grounded when the evidence for it is visible in the conversation:

- **"This test passes"** — grounded if the test was run and the output is in the transcript. Ungrounded if you wrote the test and assumed it passes.
- **"This function handles null input"** — grounded if you read the function and can cite the null check. Ungrounded if you're inferring from the function name.
- **"This fixes the bug"** — grounded if you reproduced the bug, applied the fix, and showed it no longer occurs. Ungrounded if you read the error message and wrote code that looks right.
- **"The API returns a 200 here"** — grounded if you called it or read code that demonstrates the behavior. Ungrounded if you're assuming based on convention.

The common thread: **the evidence is in the transcript, not in Claude's confidence.**

## The Grounding Actions

When you need to make a claim, ground it first:

1. **Run it.** The strongest evidence. Execute the code, run the test, call the endpoint. Show the output.
2. **Read it.** The next strongest. Read the actual code and cite what you found. "Line 42 checks for null before proceeding."
3. **Trace it.** Follow the execution path through the code. Show your work — which functions call which, what data flows where.
4. **Qualify it.** If you can't run, read, or trace — say so. "I expect this will work because X, but I haven't verified." Uncertainty is honest. False confidence wastes time.

These are ordered by strength. Prefer running over reading. Prefer reading over tracing. Prefer tracing over qualifying. Prefer qualifying over asserting.

## What Doesn't Need Grounding

Not every statement requires proof. These are fine without evidence:

- **General programming knowledge.** "Python lists are ordered." "HTTP 404 means not found." These are axioms, not claims about this code.
- **Tool output summaries.** If you just ran a command and the output is in the transcript, summarizing it doesn't require re-proving it.
- **Plan statements.** "I'll write a test for this" is an intention, not a claim. No evidence needed.
- **Qualified uncertainty.** "I think this might be related to X" or "this should work if Y" — the qualifier IS the grounding. You're being honest about your confidence level.

The line: if the statement could be wrong *about this specific codebase* and the reader would have to trust Claude's word for it, it needs grounding.

## Why This Matters

Ungrounded claims compound. Claude says "this works." The user trusts it. Three steps later, something breaks. Now the debugging surface is everything since the ungrounded claim — because that's where trust diverged from reality.

The cost of grounding is small: run a test, read a file, add a qualifier. The cost of not grounding is large: wasted work, broken trust, invisible divergence from the correct path.

The optimal path from question to correct answer is a smooth curve. Ungrounded claims are deviations — they feel like shortcuts but they're detours. The hook keeps Claude on the curve.

## Relationship to Other Skills

- **clarity:** Clarity asks "can a reader understand this code?" Grounded asks "can a reader verify this claim?" Both serve the same reader, from different angles.
- **intent:** Intent asks "are we building the right thing?" Grounded asks "do we actually know what we just built does?" Intent catches the wrong goal; grounded catches false confidence about the right goal.
- **dev-meta Phase 5 (grinding):** Grounding is most critical during grinding, where Claude operates with maximum autonomy. Every "this bead is done" should be backed by test output, not assertion.

## The Hook

This skill is enforced automatically via a Stop hook. When Claude is about to deliver a response, the hook reviews it for ungrounded epistemic claims. If it finds them, it blocks the response and tells Claude which claims need evidence. Claude then grounds the claims and tries again.

The hook is the enforcement. This document is the standard. Together they keep the conversation honest.
