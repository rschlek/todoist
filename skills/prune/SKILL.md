---
name: prune
description: >-
  Propose the stale ideas in the user's Todoist for deletion: every idea in an
  Ideation section untouched for 90+ days, shown as one batch the user approves
  or trims, with each approved idea recorded durably (memory, or a local log)
  before it is deleted. Use when the user says "prune", asks to prune stale
  ideas, "clean out Ideation", or "what ideas have gone cold", or at the prune
  step of todoist:full-review. Do NOT use for sorting the Inbox or walking the
  overdue pile (todoist:triage), auditing whether tasks still match reality
  (todoist:reconcile), or judging the tree's shape (todoist:structural-review).
---

# Todoist - Prune

Keeps the Ideation sections worth reading. An idea **untouched for 90+ days is
a prune candidate** (A5, D2). Candidates are proposed as one batch; **nothing is
deleted without the user's answer, and this never runs unattended** (A7). Every
approved idea is recorded before it is deleted: killing an idea costs nothing
only if nothing is lost.

## Read this first

Load the user's conventions as `${CLAUDE_PLUGIN_ROOT}/references/conventions-loading.md`
says (it also defines where records go without a memory tool), and read
`${CLAUDE_PLUGIN_ROOT}/references/todoist-connector-notes.md`. The conventions
define an idea, the age threshold, and the name of the ideas section (default:
`Ideation`; labels here refer to the default conventions).

## Tools

Todoist MCP: `get-overview` (find every Ideation section), `find-tasks` by
`sectionId` (ideas, with `addedAt`), `find-comments` (a dated comment is a
touch), `delete-object` (only after the record is accepted). Per-task activity
cannot be read through the connector - see the connector notes.

## Procedure

1. **Gather.** `get-overview`; note every Ideation section across the tree,
   sub-projects included. Pull each with `find-tasks` by `sectionId`. No ideas:
   say so and stop.
2. **Age each idea** from its last touch: the newest of `addedAt`, a dated edit
   in its description, and a dated comment. Last touch 90+ days ago is a
   candidate. None: say so and stop.
3. **Propose the batch** in one short list - title, project, age - then **one
   structured question** ("3 ideas untouched 90+ days - prune?"), recommended
   option first, with the option to keep any of them.
4. **Record, then delete.** For each approved idea, record it **before**
   deleting - the idea, its project, and how long it sat: retain it to the
   connected memory tool (tagged with its project and as an idea, following
   that tool's tagging conventions), or with no memory tool append it to the
   local log. Only then `delete-object`. If the record fails, do not delete;
   report the idea as kept.
5. **Report** what was pruned, where it was recorded, and what was kept.

## Principles

- **Proposal only.** Every prune is the user's call, in one batch they can trim.
- **Record first, delete second.**
- **Ideas only.** A dated task or an errand is never a candidate here - the
  overdue walk is triage's, and a dead premise is reconcile's.
- **Defer to the conventions** for every "what is correct" question.
