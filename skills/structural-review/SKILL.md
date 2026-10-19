---
name: structural-review
description: >-
  Evaluate the SHAPE of the user's Todoist - the project / sub-project / section
  tree and its descriptions - against their structure conventions, and bring
  grouped recommendations they approve one by one. Use when the user asks to
  review or clean up their Todoist structure, mentions a "structural review" or
  "maintenance pass", suspects two projects overlap, thinks a section has
  outgrown itself, or wants to audit project and section descriptions. Not for
  individual tasks (todoist:add, todoist:triage).
---

# Todoist - Structural Review

A maintenance pass over the **shape** of the board, not the tasks in it. add and
triage bring tasks to standard; this asks *is the tree itself right?* It is
**advisory**: the agent proposes, the user approves, and only then is anything
applied.

## Read this first

Load the user's conventions as `${CLAUDE_PLUGIN_ROOT}/references/conventions-loading.md`
says, and read `${CLAUDE_PLUGIN_ROOT}/references/todoist-connector-notes.md`.
Part C (structure, descriptions, evolution, heuristics, colors) and D2 (the
recommendation catalog) are the whole spec for good structure; labels refer to
the default conventions.

## Tools

- Read: `get-overview` (no args: the tree with section descriptions and colors;
  with `projectId`: one project's sections and tasks), `find-projects` (project
  descriptions in bulk - not in `get-overview`; empty ones are omitted),
  `find-tasks` (evidence for a suspicion; fetch broadly), `fetch-object`.
- Apply, on approval only: `add-projects` / `add-sections` (a new project gets
  its description in the same call, and a task-holding project gets at least
  one section), `update-projects` / `update-sections` (descriptions, renames,
  colors), `update-tasks` (re-home a root task into a section; one destination
  field). **Re-parenting a project is a manual step in the Todoist app** - the
  connector drops `parentId`.
- Graduations and merges move many tasks: propose the plan, then execute only
  the moves the user approves, one project at a time.

## Gather

1. `get-overview` for the whole tree.
2. `find-projects` for every project's description.
3. Only for a specific suspicion, pull that project's tasks. Do not bulk-pull
   every task up front.

## The checks (D2)

**Skeleton** (no task content needed):

- **Missing description** on any project except Inbox (C1).
- **Malformed description** - enumerates sections, uses "X > Y > Z" paths, or
  is empty; C1 wants a brief purpose. Section descriptions, same bar.
- **Color drift** (C5) - only when the user's conventions define a palette;
  confirm against siblings before flagging. A new domain's color is the user's
  choice, not drift.
- **Illegal nesting** - deeper than the conventions allow (C2).
- **Task-holding project with no section** (C2); a pure container is exempt.
- **Task at a project root** - re-home into a section (A1).
- **Label misuse** (A6) - a label doing a section's job. A structure finding;
  never strip a label the user applied.

**Pattern** (needs task aggregates):

- **A section outgrowing itself** (C3) - enough distinct, recurring activity to
  earn a sub-project. Flatter when in doubt (C4).
- **Projects competing for the same tasks** (C4) - recommend a merge or a
  sharper boundary, with the description edits that encode it.
- **Staleness** - tasks but no completions in a long stretch: an archive
  candidate (C3), never archived unasked.

**Per task:** a task with no good home - recommend a section, sub-project, or
clarified boundary rather than forcing it. add and triage hand off here.

## Bring it to the user

1. **One grouped summary report** - skeleton fixes, pattern recommendations,
   per-task homes - each with the concrete change and a one-line rationale tied
   to a Part C or D principle. Cheap, safe fixes first; heavy changes last.
2. **Decisions through structured-choice prompts:** the safe skeleton fixes as
   one multi-select (apply all recommended), and each heavy change (graduation,
   merge, new project or section, a new name) as its own question - approve /
   adjust / skip, **Other** for free text. Up to 4 questions per dialogue.
3. **Apply only what was approved.** For bulk moves, one project at a time,
   reconfirming.
4. **Report** what changed and what stays as a standing recommendation.

## Principles

- **Shape, not contents.** The one overlap is the no-good-home task.
- **Lean and flat above the section floor** (C4).
- **Structure is read live**, never from memory.
- **Propose, do not impose.** Every structural change is the user's call.
- **Descriptions carry area context** (C1): repair a weak one rather than
  ignoring it.
