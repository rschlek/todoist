---
name: triage
description: >-
  Interactively triage the user's Todoist: empty the Inbox into well-formed,
  correctly-placed items - tasks, errands, and ideas - then walk the overdue
  pile. Use whenever the user says it is time to triage, asks to "clear/process
  my inbox", "sort my captures", or returns from capturing tasks on the go. It
  pulls the live Inbox, proposes a placement for every capture in one summary
  table, applies what the user approves, and closes with the overdue walk. Do
  NOT use to add a single task from a chat (todoist:add), to prune stale ideas
  (todoist:prune), or to evaluate project structure (todoist:structural-review).
---

# Todoist - Triage

The pass that keeps the daily view honest, in two phases: **empty the Inbox**
(quick captures with little context, each brought to the standard, its kind,
and its home), then **walk the overdue pile**. Nothing is automatic: every move,
date, and re-date is proposed and waits for the user (A7). Stale ideas belong to
todoist:prune.

## Read this first

Load the user's conventions as `${CLAUDE_PLUGIN_ROOT}/references/conventions-loading.md`
says, and read `${CLAUDE_PLUGIN_ROOT}/references/todoist-connector-notes.md`.
The conventions define what is correct (Part A, Part B, D1; labels refer to the
default conventions).

## Tools

Todoist MCP: `get-overview` (live tree, section descriptions inline),
`find-projects` / `fetch-object` (project descriptions), `find-tasks` (the Inbox
via `projectId: "inbox"`, the `overdue` filter, duplicate scans), `update-tasks`
(the move plus field updates in one call - exactly one destination field),
`reschedule-tasks` (dates; preserves recurrence), `delete-object`.

## Present and decide

Never a wall of prose with a free-text reply:

1. **One summary table of every proposal** - one row per capture: kind (task /
   errand / idea), project ▸ section, normalized title, any split, and for tasks
   only the proposed due date. This table is the decision surface.
2. **One structured question:** **Apply all** / Let me adjust.
3. **Adjust in rounds** - for each flagged row, one structured-choice question
   per open point, up to 4 per dialogue: the kind, placement, a due date, a
   split, drop-or-file, a title, "what does X mean". Recommended option first,
   2-4 concrete options, **Other** for free text.
4. **Apply** after the answers. Decide clean calls yourself; do not re-ask what
   the table settled. Date calls stay the user's.

## Phase 1 - Empty the Inbox

`get-overview`, then pull the Inbox. Empty: say so and go to phase 2. Per capture:

1. **Split** bundled independent actions (Part B step 0).
2. **Classify** to the most specific project and a section by purpose (D1). An
   idea goes to that project's Ideation section. Never Inbox, never a root.
3. **Normalize** the title (A2) and **decide** the description (A3): do not
   fabricate; ask if a terse title clearly hides context. **Placeholder-title
   captures** (title `New Task` or the placeholder the user's conventions name,
   text in the description): promote the description into a verb-first title
   and clear the description.
4. **Set** priority (A4), then **the kind** (A5) - *is there an action here that
   cannot wait?* A task gets a proposed due date only for a real must-happen-by;
   an errand stays undated in its working section (a named trigger recorded);
   an idea stays undated in Ideation. **Unclear means idea.**
5. **Duplicate check:** scan the target project broadly and compare
   semantically; if found, ask (update existing / skip / add anyway).
6. **Apply** with `update-tasks` (move and fields in one call) and
   `reschedule-tasks` for the date.

## Phase 2 - The overdue walk

Overdue covers dated tasks only (A5), so the pile is short. Pull it
(`find-tasks` filter `overdue`) and walk it in rounds of up to 4
structured-choice questions, each self-contained (task, project, date, times
pushed), proposing one of A5's honest answers:

- **re-date** to a real by-when (spread, not stacked; batching several
  two-minute actions onto one sweep day is fine);
- **break out** the smaller thing actually blocking it;
- **kill it** (first-class);
- **un-date it as an errand** when it never had a must-happen-by.

Flag a **third push**: "push it again" is not an answer. Agent-executable items
may be offered as a delegation. **Never auto-move a slipped task.**

## Reworking a task the user points at

Run Part B in **compare mode** against the task's current state, bring the
diffs to the user, and work them together. No diffs: say so and ask what
prompted it. Preserve user-set signal unless they agree to change it.

## Closeout

Report what moved where (with its kind), what was split, what the overdue walk
decided, and anything left open. Nothing goes back into the Inbox (A7). Preserve
recurrence and user-set signal throughout, and defer to the conventions for
every "what is correct" question.
