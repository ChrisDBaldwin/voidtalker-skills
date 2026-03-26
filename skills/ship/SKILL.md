---
name: ship
description: "Assemble completed work into a shippable state — merge worktree, close beads, push. Use when a grinder has finished a PRD in a worktree and the work needs to land on main. Trigger on 'ship it', 'merge this', 'land this work', 'close this out', or when a worktree's progress.md shows all criteria passed. This skill handles the mechanical Phase 6 work: review the diff, clean up grinder artifacts, merge, close beads, push. It does NOT handle code review — that's the reviewer masque's job. Ship is the last mile after review is done."
---

# Ship

This skill handles dev-meta Phase 6: getting completed work from a worktree onto main. It's mechanical, not creative — the review has already happened (via the reviewer masque or human review). Ship is the last mile.

## When to Use This

- A grinder has completed all criteria in a worktree
- The work has been reviewed (by reviewer masque or human)
- The user says "ship it," "merge this," or "land this work"

## When NOT to Use This

- The work hasn't been reviewed yet — use the reviewer masque first
- The work is still in progress — let the grinder finish
- There's no worktree — this skill assumes worktree-based workflow

## The Process

### 1. Pre-flight Check

Before merging, verify:

```
[ ] progress.md shows all criteria passed (or explicitly BLOCKED with reason)
[ ] git status is clean in the worktree (no uncommitted changes)
[ ] No grinder artifacts to clean up (prd.md, progress.md, loop-prompt.txt)
```

### 2. Review the Diff

Show the user what's about to land:

```bash
git diff main..<branch> --stat
```

Call out:
- **New files** — expected from the PRD?
- **Modified files** — only files the PRD said to touch?
- **Deleted files** — intentional?
- **Grinder artifacts** — prd.md, progress.md at repo root? Remove them.
- **Unintended files** — node_modules committed? .env files? Build artifacts?

### 3. Clean Up

Remove grinder artifacts from the branch:

```bash
cd .worktrees/<branch>
git rm -f prd.md progress.md loop-prompt.txt 2>/dev/null
git commit -m "chore: remove grinder working files"
```

### 4. Merge

```bash
cd <repo-root>
git merge <branch>
```

If fast-forward, great. If merge conflicts exist, resolve them and explain each resolution.

### 5. Close Out

```bash
git worktree remove --force .worktrees/<branch>
git branch -d <branch>
bd close <bead-id> --reason="<summary of what was done>"
bd sync
git push
```

### 6. Verify

```bash
git status        # clean
git log --oneline -3   # recent commits look right
git worktree list      # no stale worktrees
```

## What Ship Does NOT Do

- **Code review.** That's the reviewer masque or human judgment.
- **Testing.** The grinder should have verified all criteria.
- **Deployment.** Ship lands code on main. Deployment is the operator's job.
- **Retro.** After shipping, the retro masque handles reflection.

## Relationship to Other Skills/Masques

- **Grinder masque:** Produces the work that ship lands
- **Reviewer masque:** Reviews the work before ship runs
- **Familiar masque:** May dispatch ship as part of the pipeline
- **recursive-prd:** Produced the PRD the grinder executed
- **beads plugin:** Ship closes the bead and syncs

## Key Principle

Ship is boring by design. If shipping is exciting, something went wrong upstream. The review should have caught problems. The grinder should have verified criteria. Ship is just the mechanical act of landing clean work on main.
