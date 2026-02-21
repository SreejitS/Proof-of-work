---
description: Orient Claude for a project - reads CLAUDE.md, specs context, memory, and git state
argument-hint: <folder name or path>
---

Load full context for the project at: $ARGUMENTS

## Step 1 — Resolve the project folder

If $ARGUMENTS is a bare name (e.g. `kiki-pi-one`), search under `~/Dev/`, `~/Projects/`, and the current working directory to find it.
If $ARGUMENTS is an absolute or relative path, use it directly.
Set <folder> to the resolved absolute path for all subsequent steps.

## Step 2 — Shared context read sequence

Check if `<folder>/specs/.context/` exists.
- If NOT: note "First session — no prior context. Starting fresh."
- If YES:
  1. Read `specs/.context/learnings.md` in full.
  2. Read `specs/.context/decisions.md` in full.
  3. List `specs/.context/sessions/` sorted by filename (newest first). Read the 3 most recent session files.
  4. Extract and prominently surface the `## Open questions / next session handoff` section from the most recent session.

## Step 3 — Read project guide

Read in order (skip any that don't exist):
- `<folder>/CLAUDE.md`
- `<folder>/README.md`
- `~/.claude/projects/*/memory/MEMORY.md`

## Step 4 — Git state

```bash
git -C <folder> branch --show-current
git -C <folder> status --short
git -C <folder> log --oneline -8
```

## Step 5 — Project structure

List `<folder>/` two levels deep. Note:
- Numbered component folders and which ones appear complete vs. empty
- Config files present (CLAUDE.md, .github/, package.json, etc.)

## Step 6 — Active plan

Check `<folder>/specs/` for any `.md` files (most recent filename = newest).
If one exists with `status: draft` or `status: implemented`, show its task and goal.
Ask if the user wants to continue from that plan.

## Step 7 — Orientation summary

Output this block:

```
PRIMED: [Project Name]
Branch:  [branch]
Context: [N learnings, N decisions, last session: YYYY-MM-DD or "none"]

Last session handoff:
  [Open questions from most recent session, or "No prior sessions"]

Component status:
  [Compact list of what is done / in progress / not started]

Active plan: [plan filename and task, or "none"]

Ready. What would you like to work on?
```
