---
name: intent
description: "Interview the user until 95% confidence about what they actually want — not what they think they should want. Use at any phase of work: project kickoff, mid-build, when stuck, before shipping. Trigger on 'what do I actually want', 'am I building the right thing', 'reality check', 'gut check', 'sanity check', 'interview me', 'check my intent', or when you detect misalignment between stated goals and actual behavior (scope creep, enthusiasm drop, cargo-culting, solving the wrong problem). Also trigger when framing is complete but something feels off — intent probes deeper than scope."
---

# Intent

This skill interviews the user until Claude reaches **95% confidence** about what the user actually wants — not what they said, not what sounds reasonable, not what they think they should want.

People are bad at stating what they want. They state what sounds right, what they've heard others ask for, what seems professionally appropriate, or what they wanted last month. This skill cuts through that by asking questions that separate stated intent from real intent.

This is not framing. Framing asks "what are we building?" and produces a problem statement. Intent asks "do you actually want what you say you want?" and produces alignment — or surfaces the real thing hiding behind the stated thing.

## When to Use This

**Explicitly invoked:**
- User says "reality check," "gut check," "sanity check," "interview me," "am I on the right track"
- User asks to check intent or alignment at any point in a project

**Proactively triggered — use judgment:**
- The user's energy doesn't match the work (grinding on something they seem bored by, or overcomplicating something they claim is simple)
- Scope has quietly shifted but nobody acknowledged it
- The user is building what's "correct" rather than what's useful
- Rationale sounds borrowed — "best practices say," "you're supposed to," "everyone does it this way"
- The user is stuck and the blocker might be clarity, not technical
- Framing produced a spec but something doesn't add up

## The Interview

The mechanic is simple: ask questions, one at a time, until you reach 95% confidence about what the user actually wants. You decide when you're at 95% — not the user. Stop when you get there.

The questions are not a checklist. They're tools. Pick the ones that apply to where the user is and what smells off. Ask follow-ups. Push on vague answers. The goal is convergence on real intent, not completion of a question list.

### At Any Phase — Core Probes

These work regardless of where the project is:

- **"If this succeeded perfectly, what changes for you tomorrow?"** — Tests whether the user has a concrete picture of the outcome. Vague answers ("it'll be better") signal unclear intent.
- **"What would you do if you couldn't build this?"** — Reveals whether the problem is real or the solution is a pet idea. If there's an easy workaround, the problem might not be what they think.
- **"Who are you building this for — and is that person you?"** — Catches building-for-an-audience vs. solving-your-own-problem. Both are valid, but they lead to different designs.
- **"What's the version of this you'd be embarrassed to ship?"** — The minimum viable version reveals what actually matters. What they refuse to cut is what they actually care about.
- **"Say back to me what you think the goal is."** — Flip it. State your understanding and let them correct it. The corrections reveal what they care about most.

### Pre-Work — Before Anything Is Built

- **"Why now? What changed?"** — If nothing changed, ask why this wasn't built six months ago. The trigger reveals the real motivation.
- **"What have you already tried or considered?"** — Prior attempts and rejected approaches reveal hidden constraints and preferences the user hasn't articulated.
- **"Describe the moment when you'll know this was worth building."** — Forces concrete success visualization. "When the deploy is clean" is different from "when I stop getting paged at 3am."

### Mid-Work — Something Is Already Being Built

- **"Is this still what you wanted when you started?"** — Intent drifts. People don't notice. Ask directly.
- **"What have you learned since you started that changes the picture?"** — New information during building often invalidates original assumptions. Surface it.
- **"If you could start over knowing what you know now, would you build the same thing?"** — The answer is often no. That's useful information.
- **"What part of this are you most excited about? Least?"** — Energy maps to real intent. If they're excited about the auth system but bored by the feature it enables, the real project might be the auth system.

### When Stuck

- **"Are you stuck on how to build it, or stuck on what to build?"** — Technical blocks and clarity blocks feel identical from inside. They require different solutions.
- **"What would you build if this constraint didn't exist?"** — Identifies whether the constraint is real or assumed.
- **"What are you avoiding?"** — Sometimes "stuck" means "I know what to do but I don't want to do it." That's an intent signal.

### Pre-Ship — Before It Goes Out

- **"Does this solve the problem you had when you started?"** — Not "does this work" — does it solve the *original* problem.
- **"What will you wish you'd done differently in a month?"** — Forces projection past the relief of shipping into the reality of living with the decision.

## The 95% Threshold

You're at 95% when you can state back to the user:
1. What they actually want (not just what they said)
2. Why they want it (the real reason, not the professional-sounding one)
3. What they'd give up if forced to choose

And the user agrees, or corrects you in ways that are refinements rather than redirections. If the correction is a redirection — "no, that's not it at all" — you're not at 95%. Keep going.

**You will never reach 100%.** People don't fully know what they want. 95% means: confident enough that building based on this understanding won't lead to "that's not what I meant" at the end.

## Output

This skill doesn't produce a document. It produces alignment. The output is:

1. **A statement of intent** — Claude states back what the user actually wants, in plain language, and the user confirms. This lives in the conversation, not in a file.
2. **Delta from stated intent** (if any) — If the real intent differs from what was originally stated, name the gap explicitly. "You said you wanted X, but what you actually want is Y because Z."
3. **Handoff** — Once intent is clear, hand off to the appropriate skill. If the work hasn't started, go to framing or recursive-prd. If mid-work, adjust the plan. If pre-ship, decide whether to ship or course-correct.

## Relationship to Other Skills

- **framing:** Intent feeds framing. Framing takes clear intent and turns it into a problem statement, spec, and scope. Running intent before framing means framing starts with honest input. Running intent after framing catches cases where framing produced a plausible spec for the wrong problem.
- **dev-meta:** Intent can intervene at any phase of dev-meta. It's most valuable at Phase 1 (before framing) and Phase 7 (before shipping), but mid-grind reality checks prevent wasted work.
- **clarity:** Clarity asks "can a reader understand this?" Intent asks "does the builder understand what they want?" Different questions, both about honest assessment.

## Key Principle

The fastest way to waste time is to efficiently build the wrong thing. Framing catches the wrong scope. Clarity catches the wrong structure. Intent catches the wrong goal. Of the three, the wrong goal is the most expensive — everything downstream is wasted, not just misshapen.
