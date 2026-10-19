---
name: reconcile
description: >-
  Audit the user's entire Todoist board against their persistent memory as the
  source of truth, report every task that no longer lines up with reality, then
  walk them through the findings in bite-sized decision rounds and apply what
  they approve. Use when the user asks to "reconcile" their Todoist, audit tasks
  against memory, find stale, dead, or already-done tasks, or says the board has
  drifted from reality. Can run read-only ("report only, don't touch
  anything"). An audit of premise against reality - the overdue walk belongs to
  todoist:triage. Not for sorting the Inbox (todoist:triage) or reviewing the
  tree's shape (todoist:structural-review).
---

# Todoist - Reconcile

The board says what exists; memory says what actually happened. This skill
diffs them, in two phases with a hard wall between them: an **audit** that reads
everything and writes nothing, then a **review** where the user decides each
finding in small rounds and only their answers are applied.

## Read this first

Load the user's conventions as `${CLAUDE_PLUGIN_ROOT}/references/conventions-loading.md`
says, and read `${CLAUDE_PLUGIN_ROOT}/references/todoist-connector-notes.md`.
The conventions define a well-formed task; this skill decides which tasks still
reflect reality. **It needs a source of truth:** a connected memory tool, or,
without one, whatever the user names to audit against (notes, a repo, a
calendar). With neither, say so and stop.

## Phase 1 - Audit (read-only, no exceptions)

Run unattended, state the contract: nothing is completed, deleted,
rescheduled, or edited during the audit.

1. **Gather the board.** `get-overview` for the tree, then `get-overview` with
   `projectId` per project (the root overview carries no tasks, and a board-wide
   `find-tasks` can exceed output limits). Include the Inbox. Note per task:
   title, description, dates, priority, recurrence, age.
2. **Gather memory's version of reality.** Prefer a per-project summary if the
   memory tool keeps one, then recall per project, re-querying with concrete
   names. Newest dated fact wins; open the source when wording is load-bearing.
3. **Verify cheaply before claiming.** Memory lags reality in both directions.
   Where ground truth is one cheap read away (a file, a repo log, an installed
   tool), take it, and mark each finding verified or inferred.
4. **Classify every mismatch:**
   - **A. Done in reality** - the deliverable exists; the task is still open.
   - **B. Stale content on live work** - still wanted, but it points at retired
     systems, dead paths, old names, or a superseded method.
   - **C. Lifted gates** - "blocked on X" where X has resolved.
   - **D. Ownership or scope drift** - re-decided, reassigned, or moved.
   - **E. Time-critical mismatches** - overdue tasks, and undated items that
     carry a real must-happen-by (a mis-kinded errand or idea, A5).
   - **F. Coverage gaps** - live next steps with no task. Only the user's
     own-hands work belongs on the board (D1).
   - **G. Global hygiene** - board-wide patterns, one line each.
5. **Report in full, ranked by actionability**, evidence attached to each
   finding. Then stop unless the user is ready to review.

## Phase 2 - Review rounds

Walk the report in **rounds of at most 4 structured-choice decisions**, grouped
by category, easiest wins first. Each question is self-contained (task,
project, mismatch, evidence), recommended option first. **Apply each round
before the next**, and say what was applied.

**Honor the answer given.** Verify an unverified "I think it's done" when
ground truth is reachable; if it contradicts, add a dated STATUS note instead of
completing. "Leave it" means exactly that.

**Apply with the right verb:**

- Done or superseded: `complete-tasks` (history preserved).
- Dead premise: `delete-object` (killing is first-class).
- Stale content: `update-tasks` rewrite, with a dated note in the description
  ("STATUS (verified <date>): ...").
- Lifted gate: rewrite plus a kind decision (A5) - a dated task if there is a
  real must-happen-by (the date is the user's call), else an errand.
- Coverage gap: `add-tasks`, conventions-compliant, only for work the user
  confirms is theirs.
- Dates: `reschedule-tasks` (dateless tasks are the exception - connector
  notes).

## Closeout

- **Record the decisions** with their reasons - in the memory tool, or the local
  log without one - especially supersessions and ground truth memory had wrong.
  The next reconcile starts from what this one learned.
- **Tally:** completed / deleted / rewritten / created / kept; loose ends.
- **Hand off** what belongs to a sibling: tree problems to structural-review,
  stale ideas to prune, an unsorted Inbox or overdue pile to triage.

## Principles

- **The wall is absolute:** the audit writes nothing; review writes only what
  an answer approved. One uncorroborated recall never kills a task.
- **Memory says what happened, the board what exists, the conventions what
  correct looks like.** Cheap ground truth beats memory; record the discrepancy.
- **Preserve recurrence and user-set signal.** Four decisions made beat forty
  waved through.
