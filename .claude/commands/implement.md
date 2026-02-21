---
description: Load an approved plan from specs/, pre-load context, and implement with engineering rigour
argument-hint: [optional: specs/YYYY-MM-DD-HH-MM-task.md]
---

## Step 1 — Load the plan

If $ARGUMENTS is given, read that file.
Otherwise: list all `.md` files in `specs/` sorted by filename (newest = highest date), read the most recent one.

Show the user:
- Plan filename, task, created datetime, goal
- The **Highest-risk assumption** from the plan — this is what to watch for first

## Step 2 — Load prior context

Check if `specs/.context/` exists.
- If NOT: note "No prior context. Proceeding from plan only."
- If YES:
  1. Read `specs/.context/learnings.md` in full.
  2. Read `specs/.context/decisions.md` in full.
  3. Read the most recent session file from `specs/.context/sessions/`.
  4. Grep `learnings.md` for tags matching this plan's component and `[ci]`.
  5. These are your known-good patterns — apply them without rediscovering.

## Step 3 — Pre-implementation check

Before writing any code, do this for each non-trivial step in the plan:

**Re-read the correctness argument.**
If the plan includes a hand-trace or truth table, verify it yourself before coding.
If the plan's correctness argument is missing or unclear for a logic-heavy step, derive it now:
- Trace the expected output for 3 representative inputs by hand
- Check boundary conditions: zero, max value, overflow, reset state
- If you cannot do this cleanly, the design needs more thought before code is written

**Check for the simplest implementation.**
For each step ask: is there a simpler way to achieve the same result?
Fewer lines, fewer states, fewer dependencies — prefer the simpler version if correctness is equal.
Do not add generality, abstraction, or error handling that the plan does not call for.

## Step 4 — Implement

You are now in implementation mode. The plan file is your sole source of truth.
Do not infer requirements from prior conversation turns. If the plan is ambiguous, ask.

Load all plan steps into TodoWrite immediately as pending todos.
Mark each step `in_progress` before touching it.
Mark `complete` only when the step is fully verified (see Step 5).

For each step:
1. Re-read any file you will modify before editing — do not rely on memory.
2. Write the implementation.
3. Verify it against the correctness argument in the plan (Step 5 below).
4. Mark the todo complete.
5. One-line status before moving on.

**Pause and show output to the user after completing any file that produces a deliverable.**

**If blocked:** stop, describe the problem clearly, ask. Do not silently work around it.

## Step 5 — Prove each step before moving on

After completing each non-trivial piece of code, verify it before moving to the next step:

**For logic (RTL, TypeScript simulation, algorithms):**
- Trace the code by hand for the same 3 representative inputs you used in the correctness argument
- Verify each boundary condition: zero input, max value, reset, edge cases from the plan
- If the trace does not match the expected output, fix it now — do not defer

**For interfaces and wiring:**
- Check that every port/parameter name exactly matches the spec (ISA.md, interface table, etc.)
- Check that bit widths are consistent end-to-end
- Check that signal directions are correct (input/output, in/out)

**For tests:**
- Verify the test covers the correctness argument's representative inputs
- Verify the test checks boundary conditions explicitly
- A test that only checks the happy path is incomplete

**For anything touching existing code:**
- Re-read the existing code after your edit and confirm the logic is still coherent
- Check that you have not silently changed behaviour outside the intended scope

Only proceed to the next step after this verification passes.
If it does not pass, fix it in place before continuing.

## Step 6 — On completion

Update the plan file frontmatter: `status: draft` → `status: implemented`.

Summarize:
- Files created or modified
- For each logic-heavy component: one sentence on what was verified and how
- How to test end-to-end (exact commands)
- Any deviations from the plan and the reasoning behind them
- Whether the highest-risk assumption turned out to be correct or not
