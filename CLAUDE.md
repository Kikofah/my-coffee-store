# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project status

This repository currently contains no application source code, package manifest, or build/test
tooling. It is an **Obsidian vault** (see `.obsidian/`) used purely for project documentation, plus
a `README.md`. There are no commands to build, lint, or test yet — when code is added, this file
should be updated with the actual commands (build/lint/test/single-test) and a description of the
real architecture.

## Current focus: Requirements & Product Backlog

Work right now is concentrated at the very start of the documentation pipeline, before any design
or code exists:

- `docs/01-requirements/01-spec/` — the **Requirements** docs. Source of truth for feature
  requirements, user stories, business rules, and scope (what's in/out). Write and update
  requirements here first, before anything else derives from them.
- `docs/01-requirements/backlog.md` — the **Product Backlog**: a single file listing every backlog
  item derived from the specs, with priority and status. (Supersedes the earlier idea of one
  `02-plan/BL-*.md` file per item — `02-plan/` is not currently used.)

Downstream stages (`03-task` task breakdown, `02-design`, `03-testing`) are scaffolded but not the
current priority — don't populate them speculatively; let them get filled in once backlog items are
actually picked up for design/build.

`index.md` in each `docs/` folder is a **structural description of the folder's purpose only** — it
is not where actual content goes. Add real requirement docs as new files alongside `index.md`, never
by overwriting it.

### Requirement workflow (`01-spec/`)

1. File name: `{YYYYMMDD}-{RUNNING_NO}-{short-topic-slug}.md`, e.g.
   `20260802-01-self-order-from-table.md`. `RUNNING_NO` is a 2-digit global sequence (`01`, `02`,
   ...), not reset per day.
2. Before writing a new requirement, check existing files in `01-spec/` for overlap. If the new
   requirement references or overlaps an existing one, explicitly decide (and record in the new
   doc, under a "ความสัมพันธ์กับเอกสารอื่น" section) whether it should be merged into the existing
   doc or kept as a separate one — e.g. cross-cutting/non-functional requirements (compliance,
   security) are usually kept separate from the feature doc(s) they apply to.
3. If anything about the requirement is ambiguous or has multiple reasonable designs, suggest a
   recommendation and ask the user before finalizing — don't silently assume. Anything asked and
   resolved goes in a "ข้อสมมติฐาน/การตัดสินใจที่ยืนยันแล้ว" section; anything still unresolved goes
   in an "จุดที่ยังไม่ได้ระบุ / ควรยืนยันเพิ่มเติม" section, so it isn't silently dropped.
4. After creating/updating a requirement doc, update `docs/01-requirements/backlog.md` — add or
   revise the corresponding backlog row (ID, linked requirement, priority, status).
5. Summarize the work done in `docs/05-log/{YYYYMMDD}-log.md` (create if it doesn't exist for that
   date; append if it does).

### Product Backlog (`backlog.md`)

A single markdown table in `docs/01-requirements/backlog.md`, one row per item: ID (`BL-<3-digit>`),
linked requirement file, short title, **Priority** (MoSCoW: Must/Should/Could/Won't), **Status**,
and notes.

Status lifecycle: `Draft` (just written) → `Ready` (reviewed, ready to be picked up) →
`In Progress` (being designed/built) → `Done`. If an item is dropped or superseded instead of
completed, move it to `docs/00-archived/` per the archiving rule below rather than deleting it.

### Language

Existing documentation content (all `index.md` files, and any requirement/backlog files) is written
in **Thai**, matching the project's working language. Write new requirement/backlog/doc content in
Thai. This `CLAUDE.md` file itself stays in English, as instructions for Claude Code.

### Automation for this workflow

The requirement → backlog → log workflow above is automated:

- Subagent `requirement-writer` (`.claude/agents/requirement-writer.md`, at the parent
  `Documents/Claude` level) — takes one raw requirement, does the survey / clarifying-questions
  (always ≥3 options) / cross-reference analysis / doc-writing / backlog-update / log-update steps.
- Skill `/create-requirement` (`.claude/skills/create-requirement/SKILL.md`, same location) — the
  entry point; splits a message into one or more raw requirements and runs one `requirement-writer`
  subagent per requirement, sequentially.

Prefer invoking `/create-requirement` over improvising this workflow inline. If the conventions
above change, update the subagent/skill files too, not just this section.

## Documentation structure

The `docs/` folder follows a fixed, numbered pipeline convention. Each stage is meant to feed into
the next, and every folder's `index.md` explains its purpose and links to its upstream/downstream
neighbors — read the relevant `index.md` before adding a document to make sure it goes in the right
place:

1. `docs/01-requirements/` — requirements:
   - `01-spec/` — **Requirements** (see above)
   - `backlog.md` — **Product Backlog** (see above; not a subfolder, a single file)
   - `02-plan/` — currently unused (superseded by `backlog.md`)
   - `03-task/` — task breakdown derived from the backlog (concrete to-dos, status, owners)
2. `docs/02-design/` — design derived from requirements:
   - `01-prototypes/` — UI/UX prototypes, wireframes, user flow, design system basics
   - `02-technical/` — technical design: architecture, database schema, API design, tech choices
3. `docs/03-testing/` — testing derived from design:
   - `01-test-plan/` — test cases/scenarios, test data, in/out of scope
   - `02-test-result/` — actual pass/fail results and bugs found
4. `docs/04-retrospectives/` — retrospectives per phase/sprint/milestone (what went well, what to
   improve, action items), informed by test results and the log
5. `docs/05-log/` — chronological changelog/decision log of significant project events
6. `docs/00-archived/` — superseded or cancelled documents; **never delete a doc directly, move it
   here instead** to preserve decision history

When adding project documentation, place it in the stage-appropriate folder rather than at the
repo root, and follow the upstream/downstream references noted in each `index.md`.
