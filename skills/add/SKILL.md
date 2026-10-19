---
name: add
description: >-
  Interactively add one well-formed item - a task, an errand, or an idea - to
  the user's Todoist, placed in the right project and section. Use whenever the
  user (or the agent on their behalf, mid-session) wants to capture a task,
  to-do, errand, idea, reminder, or follow-up into Todoist - "add a task", "put
  this on my Todoist", "remind me to...", "capture this idea" - or when
  something in the conversation clearly needs to become a task. Do NOT use to
  sort the Inbox (todoist:triage) or to evaluate project structure
  (todoist:structural-review).
---

# Todoist - Add

Turn one capture into one well-formed item - a task, an errand, or an idea (A5).
The agent usually has session context here, so it can propose a real
description and placement instead of parroting the title.

## Read this first

Load the user's conventions as `${CLAUDE_PLUGIN_ROOT}/references/conventions-loading.md`
says, and read `${CLAUDE_PLUGIN_ROOT}/references/todoist-connector-notes.md`
(tool quirks). The conventions define what is correct - the task standard
(Part A), the transform (Part B), routing (D1); section labels here refer to the
default conventions. This skill covers only how to ask.

## Tools

Todoist MCP: `get-overview` (the live tree - fetch every run, never route from
memory), `find-projects` / `fetch-object` (project descriptions, which
`get-overview` omits), `find-tasks` (duplicate scan), `add-tasks` (create on
approval).

## Procedure

1. **Capture the input** - what the user gave, plus relevant session context.
   Split bundled independent actions into atomic tasks (Part B step 0) and say so.
2. **Read the tree.** `get-overview`, then the descriptions of candidate
   projects, to route by purpose rather than by name (D1).
3. **Draft the item with Part B:** project and section (both mandatory, A1),
   normalized title (A2), a description only for latent context (A3; with
   session context you may propose one), priority (A4, default p4), and the
   **kind** - ask A5's question, *is there an action here that cannot wait?*
   - **Task** - something must happen by a given day. A stated day sets it;
     otherwise propose the by-when and the reasoning. The date call is the user's.
   - **Errand** - needs doing eventually. Undated, in its working section; a
     non-date trigger is recorded in the title or description.
   - **Idea** - happens only if chosen. Undated, in the project's Ideation
     section (C2). The default when the kind is genuinely unclear.
   Never date something to keep it visible, and never invent a deadline,
   priority, or facts.
4. **Ask whose work it is.** If the agent could do it itself, say so and propose
   the user's agent handoff (per their conventions) or keeping it off the board
   (A7).
5. **Check for an existing match.** Scan the target project and section for a
   task already covering this concern - a recurring obligation, a
   near-duplicate, the same outcome worded differently. Scan broadly and compare
   semantically (`searchText` is phrase matching). If found, ask: update the
   existing task, skip, or add anyway.
6. **Confirm before creating.** Show a compact preview - kind, project ▸
   section, title, description, priority, due, deadline, any duplicate flag -
   marking what was inferred. Then ask through a **structured-choice prompt**,
   not free text:
   - all clear: one confirm question (**Create it** / Adjust);
   - otherwise one question per open fork, up to 4: the kind, an ambiguous
     placement (2-3 candidate sections), a task's due date (2-3 realistic
     by-whens, nearest first), whose work it is, a possible duplicate, a
     missing description. Recommended option first; **Other** covers free text.
   - Nothing fits well: do not force it - say so and offer
     todoist:structural-review.
7. **Create it** with `add-tasks`: priority as `p1`-`p4`, `dueString` for a due
   date, `deadlineDate` (`YYYY-MM-DD`) for a hard do-by date; an errand or idea
   gets no `dueString`. Confirm what was created.

## Principles

- **One capture, one well-formed item** (or a clean split).
- **Never land in Inbox** - that is for quick captures triage cleans up (A7).
- **A date only when something must happen by then** (A5); missing fields mean
  a default or a question, never an invented value.
- **Duplicates first.** One concern, one task.
- **Confirm before writing**, through a preview and a structured choice.
- **Defer to the conventions** for every "what is correct" question.
