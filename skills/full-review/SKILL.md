---
name: full-review
description: >-
  Run the user's complete Todoist maintenance ritual in one sitting by chaining
  the four sibling passes in order: triage (empty the Inbox and walk the overdue
  pile), then prune (propose stale ideas for deletion), then reconcile (audit
  the board against memory), then structural-review (evaluate the tree's
  shape). Use when the user asks for a "full review", a "full pass", a complete
  Todoist review or maintenance session, or to run triage, prune, reconcile, and
  structural review together. Not for a single pass on its own (todoist:triage,
  todoist:prune, todoist:reconcile, todoist:structural-review) or for adding one
  task (todoist:add).
---

# Todoist - Full Review

One sitting, four passes, fixed order: **triage → prune → reconcile →
structural-review**. Content settles before shape is judged:

1. **Triage** empties the Inbox and walks the overdue pile, so later passes see
   a complete board with no slippage undecided.
2. **Prune** clears the ideas that have gone cold.
3. **Reconcile** trues the remaining tasks against memory, clearing dead and
   stale work.
4. **Structural-review** judges the shape last, on a board whose contents are
   real - a section's volume means something only once its dead weight is gone.

**Not a weekly ritual.** Run it when the board has drifted: a pile-up after time
away, a stretch of unsorted captures, a tree that stopped matching the work.
The daily view is meant to stay honest on its own (A5).

This skill is a **conductor**: it owns the sequence, the checkpoints, the
cross-phase ledger, and the combined closeout. Each pass's procedure lives in
its sibling skill and is not redefined or abridged here. Load the user's
conventions once, as `${CLAUDE_PLUGIN_ROOT}/references/conventions-loading.md`
says, and carry that choice through all four phases.

## Run it

1. Announce the plan in one line. The user may drop or reorder a phase up front.
2. **Phase 1:** invoke `todoist:triage` and run it through its closeout.
3. Checkpoint, then **phase 2:** `todoist:prune`, through its closeout.
4. Checkpoint, then **phase 3:** `todoist:reconcile`, both phases (read-only
   audit, then review rounds), through its closeout. Without a source of truth
   to audit against, skip it and say so.
5. Checkpoint, then **phase 4:** `todoist:structural-review`, folding in the
   ledger items below.
6. **Combined closeout.**

**Checkpoints:** a one-line tally of the finished phase, then continue unless
the user calls a stop. Each pass stands alone, so stopping is clean: report what
ran, which phases remain, and leave ledger items as named recommendations.

**Each phase reads the board live at its own start.** Never reuse an earlier
phase's reads; earlier phases changed the board.

## The ledger (cross-phase handoffs)

- Triage hits a capture with **no good home** - bring it to phase 4's per-task
  check instead of forcing a placement.
- Reconcile flags **tree problems** - phase 4 input, not standing
  recommendations.
- Anything this run cannot absorb - an explicit leftover in the closeout.
- New captures landing mid-sitting - a quick triage encore before the closeout,
  so the ritual ends with an empty Inbox.

## Combined closeout

- One tally across all phases: captures triaged, ideas pruned or kept, tasks
  completed / deleted / rewritten / created, structural changes applied,
  recommendations left open.
- Confirm the ledger is empty: every handoff landed or is named as a leftover.
- Records follow the sibling skills' own closeouts (prune records each idea
  before deleting; reconcile records its decisions). Add one ritual-level
  record only when the sitting produced something durable beyond those.

## Principles

- **Conduct, don't redefine.** Every "how" belongs to the sibling skill,
  including prune's record-before-delete and reconcile's read-only audit.
- **Order is fixed; participation is the user's.**
- **Nothing handed off gets dropped.**
