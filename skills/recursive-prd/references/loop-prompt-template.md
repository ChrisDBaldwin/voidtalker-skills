# Loop Prompt Templates

Variants of the recursive loop prompt for different project types and loop runners. Pick the closest match and customize.

---

## Core Template (All Project Types)

This is the base. Every variant is a customization of this.

```
You are working on [PROJECT]. Your goal is convergence toward the acceptance criteria defined in [PRD_PATH].

On each iteration:

1. Read the acceptance criteria in [PRD_PATH].
2. Read [PROGRESS_PATH] for context on prior iterations.
3. Assess the current state of the codebase against unchecked criteria.
4. Identify the single highest-priority unchecked criterion.
   - Priority order: [TIER_ORDER].
   - Within a tier, prefer criteria that unblock others.
5. Implement the minimum change to satisfy that criterion.
6. Verify it passes: [VERIFICATION_METHOD].
7. Update [PROGRESS_PATH] with: criterion addressed, changes made, decisions, blockers.
8. Commit: "[PREFIX] <criterion-id>: <short description>"
9. If all criteria pass → EXIT_SIGNAL.
   If blocked → document in [PROGRESS_PATH], move to next criterion.

Do NOT:
- Refactor code unrelated to the current criterion
- Modify files outside the scope boundary defined in the PRD
- Introduce dependencies without documenting rationale
- Skip verification before committing
- Work on more than one criterion per iteration
```

---

## Frontend Variant

Adds viewport testing, dev server management, and visual verification.

```
[Core template, plus:]

Verification method:
- Start the dev server if not running (`[DEV_COMMAND]`)
- Check the page at each breakpoint: [BREAKPOINTS]
- Verify no console errors in browser dev tools
- If the criterion involves visual output, describe what you see and confirm it matches the requirement

Design guidance:
- [THEME]: [description]
- [TYPOGRAPHY]: [fonts]
- [COLORS]: [palette]
- [ANTI-PATTERNS]: [what to avoid]
- [SIGNATURE]: [the memorable element to protect]

Additional guardrails:
- Do not change the design tokens without updating all consumers
- Do not remove or alter the [SIGNATURE_ELEMENT]
- Test at [BREAKPOINTS] before marking any visual criterion as complete
```

---

## Backend / API Variant

Adds endpoint testing, contract verification, and data integrity checks.

```
[Core template, plus:]

Verification method:
- Run the test suite: `[TEST_COMMAND]`
- For API criteria, verify with curl/httpie against the running service
- Check response shapes match the contract defined in the criterion
- Verify error cases return correct status codes and bodies

Additional guardrails:
- Do not change database schemas without a migration file
- Do not modify existing API contracts (add new fields, don't rename or remove)
- Run the full test suite after each change, not just the test for the current criterion
- Do not hardcode secrets, URLs, or environment-specific values
```

---

## Infrastructure Variant

Adds deployment safety, rollback awareness, and state verification.

```
[Core template, plus:]

Verification method:
- For CloudFormation/Terraform: run `[PLAN_COMMAND]` and verify the diff is scoped to the criterion
- After applying: verify resource health via `[HEALTH_CHECK]`
- Check that existing resources are unmodified unless the criterion specifically requires it

Additional guardrails:
- NEVER apply infrastructure changes without reviewing the plan/diff first
- Do not delete or replace stateful resources (databases, S3 buckets) unless explicitly in the criterion
- Tag all new resources with: [TAGGING_CONVENTION]
- If a change would cause downtime, document it in progress.md and mark as BLOCKED for human review
- Include rollback steps in the progress.md entry for any infrastructure change
```

---

## Content / Documentation Variant

Adds accuracy checks and completeness verification.

```
[Core template, plus:]

Verification method:
- For documentation criteria: verify the content matches current code behavior
- Run any code examples in the docs to confirm they work
- Check that all internal links resolve
- Verify heading hierarchy is consistent (no skipped levels)

Additional guardrails:
- Do not change code to match documentation — change documentation to match code
- Do not invent features or behavior that don't exist yet
- Mark any criterion that requires human judgment (tone, accuracy of claims) as NEEDS_REVIEW in progress.md
- Preserve existing documentation structure unless the criterion specifically requires restructuring
```

---

## Runner-Specific Configurations

### Ralph (bash loop)

Ralph manages the loop externally. The prompt above is your `prd.md` or inline prompt. Key settings:

```bash
# ralph-setup expects a PRD file
ralph-import prd.md

# Or configure inline in .ralph/config
RALPH_MAX_ITERATIONS=50
RALPH_EXIT_SIGNAL="EXIT_SIGNAL"
RALPH_RATE_LIMIT=100  # calls/hour
```

The EXIT_SIGNAL at step 9 of the loop prompt is what Ralph watches for. When the agent outputs it, Ralph stops the loop.

### Claude Code /loop

`/loop` uses natural language scheduling. Start it with:

```
/loop every 5 minutes, read progress.md and execute the next iteration of the recursive loop prompt defined in [PRD_PATH]. Stop when all acceptance criteria are met.
```

Or for a single long-running session:

```
Execute the recursive loop defined in [PRD_PATH]. Work through criteria one at a time. After each criterion, update progress.md and continue to the next. Exit when done.
```

### Bare Bash Loop

For when you don't want Ralph's overhead:

```bash
#!/bin/bash
MAX_ITERATIONS=50
ITERATION=0

while [ $ITERATION -lt $MAX_ITERATIONS ]; do
  ITERATION=$((ITERATION + 1))
  echo "=== Iteration $ITERATION ==="
  
  OUTPUT=$(claude -p "$(cat loop-prompt.md)" --dangerously-skip-permissions 2>&1)
  
  echo "$OUTPUT"
  
  if echo "$OUTPUT" | grep -q "EXIT_SIGNAL"; then
    echo "=== All criteria met. Exiting. ==="
    exit 0
  fi
  
  sleep 5  # Brief pause between iterations
done

echo "=== Max iterations reached ==="
```

Save the recursive loop prompt section of your PRD as `loop-prompt.md` for this approach.

---

## Combining with dev-meta

If the project uses the dev-meta lifecycle skill, the recursive PRD maps to specific phases:

- **Problem Statement** = Phase 1 (Frame the Problem)
- **Design Direction / Architecture Constraints** = Phase 2-3 (Spec + Architecture)
- **Acceptance Criteria** = Phase 4 (Beads — each criterion is a bead)
- **Recursive Loop Execution** = Phase 5 (Grind Beads)
- **"All criteria pass"** = Phase 6 entry gate (ready for PR + integration test)

Phases 7-10 (user testing, docs, merge, retro) happen after the loop exits. The recursive PRD automates Phase 5 — it doesn't replace human judgment in the later phases.
