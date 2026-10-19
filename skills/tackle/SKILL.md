---
name: tackle
description: >-
  Help the user do an existing Todoist task and close it out honestly, or pick
  one worth doing now. Use when they say "tackle", "what should I work on",
  "let's knock out [task]", "pick something for me", or pick up a to-do to
  actually do it. Not for capturing new tasks (todoist:add) or triaging the
  Inbox (todoist:triage).
---

# Todoist - Tackle

Do the work of a task that already exists, then leave it in an honest state.

## Read this first

Load the user's conventions as `${CLAUDE_PLUGIN_ROOT}/references/conventions-loading.md`
says, and read `${CLAUDE_PLUGIN_ROOT}/references/todoist-connector-notes.md`.
They define label semantics (A6), honest rescheduling (A5), and the standard for
any follow-up you create (Part A); labels refer to the default conventions.

## Quick start

User: "Let's tackle the quarterly-report task."
1. `find-tasks` (searchText: "report") - locate it.
2. `fetch-object` + `find-comments` - load its real context.
3. Do the work with the user.
4. `complete-tasks` when done.

## Named task (the user pointed at one)

1. **Locate it** with `find-tasks` and one distinctive keyword (`searchText` is
   phrase matching - do not over-specify). Disambiguate if several match; offer
   todoist:add if it is not in Todoist.
2. **Load context:** `fetch-object` (description, fields), `find-comments`,
   subtasks (`parentId`), the project's description (`find-projects` /
   `fetch-object`). The title is rarely the whole story.
3. If another installed skill already automates this kind of task, hand off to
   it instead of redoing the work by hand.
4. **Do the work** interactively; confirm scope if it is vaguer than its title.
5. **Close out** (below).

## Pick a task (none named)

1. Gather `find-tasks` filter `(today | overdue)`, plus `p1` / `p2`.
2. If that comes back empty, propose **errands**: undated items in a working
   section (never Ideation), from the section that fits the moment - the
   free-time list (A5). Never propose an idea as work.
3. Propose about 3, each with a one-line reason (overdue, p1, quick, unblocks)
   and rough effort. Check the user's calendar if timing matters and a calendar
   tool is connected.
4. The user picks one; run the named-task flow. Do not start before they pick.

## Close out honestly

- **Done:** `complete-tasks` (a recurring task rolls forward; keep the
  recurrence). If the skill performed and verified the action itself, complete
  and say so; if the user does the last step, complete only after they confirm.
- **Partly done:** `reschedule-tasks` forward (A5) plus an `add-comments` status
  note. Never fake-complete.
- **Follow-ups:** their own well-formed tasks, via todoist:add.
- **Not worth doing:** say so; the user decides to complete, reschedule, or
  delete.

## Notes

- Execution first: the Todoist calls bracket the work; they are not the work.
- Do not re-file or re-shape the task you are tackling - that is add, triage,
  or structural-review. Surface it if it is wrong.
