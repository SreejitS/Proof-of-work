---
description: Save session progress to specs/.context/, update MEMORY.md, and commit with a conventional message
---

## Step 1 — Gather session summary

Ask the user:
> "What was accomplished this session? What didn't go as planned? Any new learnings or architectural decisions to record?"

Accept freeform input. From their answer plus `git diff` and the active plan file, produce:
- A structured session summary
- A list of new learnings as tagged bullet points (`- [tag] learning text`)
- Any new ADR entries if architectural decisions were made

Tag format: `[component]` using the numbered folder name (e.g. `[alu]`, `[memory]`, `[web-demo]`, `[ci]`, `[publish]`).

## Step 2 — Write session file

Get current datetime:
```bash
date "+%Y-%m-%d-%H-%M"
```

Create `specs/.context/sessions/` if it does not exist.
Write `specs/.context/sessions/YYYY-MM-DD-HH-MM.md`:

```markdown
---
date: YYYY-MM-DD
time: HH-MM
component: <primary component worked on>
plan: <path to plan file if one was used, else "none">
commit: <commit message that will be used in Step 4>
---

## What was accomplished

## What did NOT go as planned

## Files changed

## New learnings captured

## New decisions captured

## Open questions / next session handoff
```

Fill every section. The `## Open questions` section is the most important — it becomes the first thing the next `/prime` surfaces.

## Step 3 — Append to learnings and decisions

Create `specs/.context/` if it does not exist.

**`specs/.context/learnings.md`**: Append a new dated block:
```markdown
## YYYY-MM-DD - session: HH-MM

- [tag] learning text
- [tag] learning text
```
Do NOT overwrite existing content. Only append.

**`specs/.context/decisions.md`**: If new architectural decisions were made, append:
```markdown
## YYYY-MM-DD - ADR-NNN: Short decision title

**Context:** What was the situation and why did a decision need to be made?
**Decision:** What was decided?
**Consequences:** What does this decision require or prevent going forward?
```
ADR numbers are sequential. Read the file to find the current highest ADR-NNN and increment.

## Step 4 — Update MEMORY.md

Read `~/.claude/projects/*/memory/MEMORY.md`.
Update the relevant section:
- Add newly completed components to "What's been built"
- Update "Next components to build" order if anything changed
- Add any new tooling or publishing learnings
Do NOT remove existing entries. Update in place or append.

## Step 5 — Git commit

Stage and commit:
```bash
git add -A
git commit -m "type(scope): imperative description"
```

Construct the commit message using these types:
| Type | Use for |
|------|---------|
| `feat(component)` | New SystemVerilog RTL |
| `tb(component)` | New or updated testbench |
| `article(component)` | Article draft created or updated |
| `publish(component)` | Frontmatter updated after publishing |
| `web(component)` | TypeScript mirror or Next.js page |
| `ci(component)` | `.github/workflows/test.yml` changes |
| `docs(component)` | README or CLAUDE.md |
| `spec(component)` | `00-spec/ISA.md` changes |
| `fix(component)` | Bug fix anywhere |
| `chore(tools)` | `tools/` scripts or scaffolding |
| `session(context)` | Only context files changed |

Rules:
- One line only, under 72 characters total
- Imperative mood: "implement", "add", "fix" — not "implemented", "added", "fixed"
- Describes the WORK done, not the act of checkpointing
- If work touched multiple components, pick the most significant one

## Step 6 — Report

Show the user:
1. Session summary (5-10 bullets)
2. Commit hash and message
3. What is in `## Open questions / next session handoff` (this is what `/prime` will surface next session)
4. One clear next action to start the following session with
