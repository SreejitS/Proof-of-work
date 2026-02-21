---
description: Auto-detect and run the test suite; report PASS/FAIL clearly for every test
argument-hint: [optional: component name e.g. alu]
---

## Step 1 — Shared context read sequence

Check if `specs/.context/` exists.
- If YES: grep `specs/.context/learnings.md` for tags matching `$ARGUMENTS` and `[ci]`.
  Note any known test quirks or failure patterns before running.

## Step 2 — Detect and run tests

Check in this order. Run the first match, or all that apply.

### 1. SystemVerilog testbenches (iverilog)

If $ARGUMENTS is given:
- Look for `XX-$ARGUMENTS/tb/tb_$ARGUMENTS.sv` and `XX-$ARGUMENTS/rtl/$ARGUMENTS.sv`
- Compile and run:
  ```bash
  iverilog -g2012 -o tb_$ARGUMENTS XX-$ARGUMENTS/tb/tb_$ARGUMENTS.sv XX-$ARGUMENTS/rtl/$ARGUMENTS.sv && vvp tb_$ARGUMENTS
  ```

If no argument:
- Find all `tb/tb_*.sv` files across numbered component folders
- For each one that has a matching `rtl/*.sv` file, compile and run it

### 2. JavaScript / TypeScript

If a `package.json` exists with a `test` script:
```bash
npm test
```
Run from the directory containing `package.json`.

### 3. Python

If `.py` files contain test functions and `pytest` is available:
```bash
python -m pytest -v
```

### 4. CI reference

Read `.github/workflows/test.yml`. Show which testbench steps are currently active (not commented out) vs. pending (commented out).

## Step 3 — Report

```
TEST RESULTS
============
[PASS] register  — 6/6 tests passed
[FAIL] alu       — 3/5 tests passed (see output below)
[SKIP] memory    — rtl/memory.sv not found yet

CI active:   register, alu
CI pending:  memory, pc, cpu, computer
```

Show the full error output for any test that fails. Do not truncate failures.
If a failure matches a known pattern from `learnings.md`, note it explicitly.
