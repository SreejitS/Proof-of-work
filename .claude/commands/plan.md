---
description: Explore codebase, load prior context, think from first principles, and write a proven plan to specs/
argument-hint: <task description>
---

You are in planning mode. Think deeply before designing. Do NOT write or edit any files except the plan file itself.

## Task

$ARGUMENTS

---

## Step 1 — Load prior context

Check if `specs/.context/` exists in the current project.
- If NOT: note "No prior context."
- If YES:
  1. Read `specs/.context/learnings.md` in full.
  2. Read `specs/.context/decisions.md` in full.
  3. Read the 3 most recent files in `specs/.context/sessions/`.
  4. Grep `learnings.md` for tags matching the component or area this task is about.
  5. Note any `## Open questions / next session handoff` items from the most recent session.

## Step 2 — Read project guide

Read `CLAUDE.md` and `README.md` if present.

## Step 3 — Explore the codebase

Use Glob and Grep to find files directly relevant to this task. Read every relevant file.
Find existing patterns to follow — do not invent structure when a pattern already exists.
Check for: existing similar components, tests covering this area, dependencies to touch.

## Step 4 — First principles analysis

Before designing anything, reason from the ground up:

**What is the actual problem?**
State it in one sentence without referencing implementation details.
Strip away any assumed solution from the task description and identify the underlying need.

**What are the hard constraints?**
List things that are non-negotiable: ISA spec requirements, interface contracts, existing dependencies, correctness invariants.
Distinguish hard constraints (cannot be changed) from soft ones (preferences, conventions).

**What is the simplest correct solution?**
Start from the minimal design that satisfies the hard constraints. Ask:
- What is the irreducible core of this component?
- What can be removed without breaking correctness?
- Does this really need to be general, or is a specific solution sufficient?
Prefer the simpler design. Complexity must justify itself.

**What are the failure modes?**
For each key design decision, state what breaks if that decision is wrong.
Identify the highest-risk assumption in the plan and call it out explicitly.

**Can you prove correctness before writing code?**
For logic-heavy components (ALU operations, state machines, address decoding):
- Write out the truth table or state transition diagram mentally first
- Verify boundary conditions by hand (zero input, max value, overflow, reset)
- If you cannot trace the expected output for 3 representative inputs without writing code, the design is not clear enough yet

## Step 5 — Ask clarifying questions

If anything remains ambiguous, use AskUserQuestion before designing.
Do not assume scope, patterns, or "done" criteria. Never guess at a constraint — ask.

## Step 6 — Write the plan file

Get the current datetime:
```bash
date "+%Y-%m-%d-%H-%M"
```

Create `specs/` if it does not exist.
Save the plan to: `specs/YYYY-MM-DD-HH-MM-<3-5-word-slug>.md`

File structure:
```markdown
---
task: "<full $ARGUMENTS text>"
created: "YYYY-MM-DD HH:MM"
status: draft
---

## Problem
One sentence stating the actual problem, independent of implementation.

## Hard constraints
- ...

## Design
The simplest correct approach. State what was considered and ruled out, and why.
If there is a non-obvious design decision, explain the reasoning — not just the conclusion.

## Correctness argument
For any logic-heavy step: a brief hand-trace or truth table showing the design is right
before any code is written. Skip this for trivial steps (file moves, config edits).

## Highest-risk assumption
One sentence. What is the thing most likely to be wrong, and how will you find out quickly?

## Files to create or modify
| File | Action | Reason |
|------|--------|--------|
| ... | create/modify | ... |

## Steps
1. ...
2. ...

## Out of scope
- ...
```

Then call ExitPlanMode to hand control back to the user for approval.
