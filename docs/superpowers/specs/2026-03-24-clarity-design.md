# Clarity — Skill Design Spec

## Overview

A cognitive-load lens for code review and writing. `clarity` ensures that each unit of code contains what a reader needs to understand it — scope is tight, logic is where you'd look for it, and state is visible where it matters.

Required during review. Recommended during writing.

## The Frame

Clarity is about **containment**. Code is clear when each unit of work contains what you need to understand it — the scope is tight enough that the reader can digest what's happening *here* without importing context from *elsewhere*.

This serves two goals simultaneously: **digestibility** (you can understand the unit) and **extensibility** (you can change it without tracing ripple effects through distant code).

Two questions drive every judgment:

1. **"Where does the logic actually live?"** — If understanding this unit requires chasing through layers, hopping files, or unwinding abstractions, the scope isn't tight enough. The logic is scattered when it should be contained.

2. **"What do I need to remember right now?"** — If understanding this unit requires holding onto values from far away, tracking mutations across call sites, or knowing about implicit setup, the scope has leaked. State that matters here should be visible here.

The principle: **what belongs together should live together, and what lives together should be understandable together.** Complexity isn't the enemy — scattered complexity is.

## The Reader Simulation

When reviewing or writing code, simulate a reader encountering it for the first time. This reader is competent — they know the language, they understand general patterns — but they have no prior context about this specific codebase. They're reading your unit of code and trying to answer: *what does this do, and could I change it confidently?*

The reader's viewport is **what they can take in when they grep into unfamiliar code** — they've landed somewhere cold and they're reading what's around the match. Clarity means the relevant context for understanding a unit of work stays within that landing zone. When it doesn't, the reader is forced to hop files, chase references, or hold invisible state — and each of those is a crack where understanding leaks out.

The simulation surfaces friction by asking what the reader experiences:

**Tracing friction** — The reader hits a call that sends them somewhere else. They follow it. That function delegates further. Each hop pushes the original context out of view. The logic *works*, but it doesn't live where the reader went looking for it. The question isn't whether an abstraction exists — it's whether it keeps context within the reader's landing zone or scatters it across several.

**State friction** — The reader encounters a variable whose meaning isn't visible from where they landed. It was assigned far above, or passed through from a distant caller, or set during initialization elsewhere. To understand what they're looking at *right now*, they have to leave and go find context that should have been here. The question isn't whether state is shared — it's whether the reader can see what they need without leaving where they are.

## Applying the Lens

The skill has two modes:

**During review (required):** Read each unit of changed code as the cold-grep reader. For each unit, ask the two questions — where does the logic live, and what do I need to remember? Flag friction, not fixes. The author (human or agent) decides how to resolve it. A review comment looks like "understanding this requires knowing what `ctx` was set to in the initializer three files up" — not "inline the ctx value here."

**During writing (optional, recommended):** Before finishing a unit of work, simulate the grep landing. If you wrote something that requires context from outside the landing zone to understand, consider whether the scope can be tightened. This isn't a gate — sometimes the answer is no, the complexity is inherent. But the question should be asked.

The lens is recursive — it applies at every scale. A function, a class, a module, a service boundary. At each level, the same questions hold: does the logic live here, and can you see what you need from here?

## Relationship to Other Skills

`clarity` is a lens, not a process. It doesn't own any phase of work — it informs judgment within phases owned by other skills.

- During **dev-meta's** spec and architecture phases, clarity asks whether the proposed boundaries will produce units that are self-contained and digestible. If a design scatters one behavior across five modules, that's a clarity signal before any code is written.
- During **dev-meta's** grind phase, clarity is a background principle — write code where the logic lives where you'd go looking for it.
- During **dev-meta's** integration and review phases, clarity becomes the required review lens.
- During **recursive-prd** criteria writing, clarity can inform what "well-structured" means for code-quality criteria — not style rules, but containment.

`clarity` never overrides another skill's process. It adds a question to every phase: *will a reader landing here cold be able to digest this?*

## Implementation Notes

**Frontmatter trigger description:** The SKILL.md will need a `description` field that triggers on code review, refactoring, architecture review, and optionally during code writing. Draft during implementation.

**Enforcement:** "Required during review" is a convention, not a mechanical gate. The skill relies on being invoked by the agent or user during review — it does not inject itself into other skills' processes. If dev-meta is later updated to reference clarity during its review phases, that's a welcome integration, not a requirement.

**Output:** Clarity findings surface as review comments — descriptions of friction experienced by the simulated reader. There is no separate report or artifact. During writing mode, the lens is internal reasoning that shapes the code; it produces no output unless it surfaces a concern.

**Reference files:** None required. The skill is a lens with no templates or checklists. If illustrative examples are useful during implementation, they belong in the SKILL.md itself, not in a references directory.

**Practical unit of analysis:** During a review pass, the unit is whatever the reader lands on — the function, class, or module containing the changed code. The lens is recursive across scales, but the reviewer starts where the diff is and expands outward only if the two questions demand it.

## Skill Metadata

- **Name:** clarity
- **Type:** Lens (review required by convention, writing recommended)
- **Language:** Agnostic
- **Style:** Principled, not prescriptive — describes friction, not fixes
- **Trigger:** Any code review, refactoring discussion, or architecture review. Optional during code writing.
