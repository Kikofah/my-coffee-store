---
name: create-requirement
description: Turn one or more raw/informal requirement descriptions from the user into formal requirement documents for the my-coffee-store project (docs/01-requirements/01-spec/), asking clarifying questions with at least 3 options whenever something is ambiguous, analyzing whether a new requirement should be merged into an existing one or kept separate, then updating docs/01-requirements/backlog.md and today's docs/05-log/{YYYYMMDD}-log.md. Use when the user describes a new feature, business rule, or compliance need in plain language ("ต้องการ...", "เพิ่มเติมในส่วนของ...", "we need a system that...") and wants it turned into a proper requirement doc, or invokes `/create-requirement` directly.
---

# /create-requirement

Turns raw requirement descriptions into this project's formal documentation pipeline
(requirement doc → backlog → log), delegating the actual writing/questioning/analysis work to the
`requirement-writer` subagent — one subagent run per distinct requirement.

## Step 1 — Identify the raw requirement(s)

Parse `$ARGUMENTS` and/or the surrounding conversation for the raw requirement(s) the user wants
documented. A single message may contain more than one distinct requirement (e.g. "Requirement 1
... Requirement 2 ...") — split them into separate items. If nothing usable is found, ask the user
what requirement(s) they want documented.

## Step 2 — Confirm the target repo

This skill operates on the `my-coffee-store` project. If the current working directory isn't
inside it, locate it (it's the git repo containing `docs/01-requirements/`) before proceeding.

## Step 3 — Run one `requirement-writer` subagent per requirement, sequentially

For each raw requirement, in the order given, dispatch the `requirement-writer` subagent with:

- The raw requirement text, verbatim
- Today's date
- A note of which other requirement(s) from this same batch were already written (with their
  assigned file names), so it can correctly do its Step 1 survey / cross-reference analysis against
  them too, not just against docs already on disk

**Run these one at a time, not in parallel.** Each one needs to see the requirement doc(s) the
previous one just created — both to pick the next `RUNNING_NO`/`BL-<id>` without collisions, and to
correctly analyze cross-references between requirements in the same batch (e.g. a compliance
requirement that references a feature requirement submitted moments earlier).

## Step 4 — Summarize

After all subagents finish, report to the user:

- Every requirement doc created (with links), one line each
- Every backlog row added/changed
- Any open questions/points left unresolved that the user should keep in mind before this work
  moves to `02-design/`

## Notes

- All file-naming, backlog, and logging conventions live in the `requirement-writer` subagent
  definition and this project's `CLAUDE.md` — don't duplicate or reinvent them here; if they seem
  out of date, fix them at the source (the subagent file / `CLAUDE.md`), not by improvising in this
  skill.
- This skill only handles the requirements → backlog → log pipeline. It does not touch
  `02-design/`, `03-testing/`, or any application code.
