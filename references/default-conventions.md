# Default Todoist conventions

**Purpose:** the starting conventions this plugin ships with. They define what a
well-formed task looks like and what a well-shaped board looks like, and they
are used only when the user has not supplied their own (see
`conventions-loading.md`). Copy this file to
`~/.config/todoist-skills/conventions.md` and edit it to make it yours; any part
you delete falls back to this default.

## Using this doc

- **The system works without an agent.** Everything here can be run by hand in
  the Todoist app. If a rule needs software to run, it does not belong here.
- **Structure is read live.** Fetch the tree each run (`get-overview`). This doc
  describes shape and principles, never the current contents.
- **Area context lives in Todoist.** Each project carries a native project
  description (C1) saying what it is for; routing reads these live. When intent
  does not clearly map, ask rather than guess (A7).
- **Connector behavior lives elsewhere:** `todoist-connector-notes.md`.

---

# PART A - TASK STANDARD

## A1. The fields

- a **single action** (atomic); a capture that bundles independent actions is
  split into separate tasks. A single outcome with sub-steps stays one task
  (sub-steps go in the description).
- a **project** and a **section**, both mandatory: the project root holds no
  tasks. A project with no natural split still carries one general section.
- a **title** that is verb-first, action-oriented, and self-explanatory
- a **description** only when there is latent context not recoverable from the
  title (A3)
- a **priority** (default p4, A4)
- a **due date** only when something must happen by then (A5)
- a **deadline** only for a stated, hard do-by date
- a **kind** - task, errand, or idea (A5). The kind is not a field; it is what
  the date and the section together say.

**Duration is out of scope:** never set it, and do not invent time estimates.

## A2. Title normalization

- **Verb-first.** "Renew car registration", not "Car registration".
- **Sentence case**, not Title Case.
- **Concise but not clipped.** Keep the user's own nouns and specifics.
- **Links and context go in the description**, not the title.
- No filler; do not restate the project name.

## A3. Description (latent context)

A description is warranted when there is **context not recoverable from the
title** that a future reader would need to act well - what was asked, by whom,
what a good answer looks like. It is not a function of how complex the title
looks.

- A surface with **no prior context** (a raw capture) never fabricates a
  description; keep what was typed, otherwise none.
- A surface **with context** (an active session, memory) may propose one.
- **Never add a filler description.**
- **Placeholder-title captures** (a quick-capture flow that sets a fixed title
  such as `New Task` and puts the typed text in the description, see
  `ios-quick-capture.md`): the description *is* the task. Promote it into a
  verb-first title and clear the description.

## A4. Priority

| Priority | Meaning |
|---|---|
| p4 (default) | everything not below |
| p3 | unused by default |
| p2 | important to do today, but survivable if it slips |
| p1 | must be done today |

**Do not inflate.** Most tasks stay p4. "today", "asap", "urgent" point to p1;
"important" without a same-day need points to p2. Priority is about urgency
within a day; **never use a priority or a date to keep something visible** -
that is the section's job (A5).

## A5. Kinds, dates, deadlines, and recurrence

**Every item is one of three kinds:**

- **Task** - has to get done, and carries a **due date only when something must
  happen by then**. Four triggers, and only these: a hard external deadline;
  someone else waiting on it or blocked by it; a window where drift costs (an
  appointment slot, a return period); a recurring chore where drift matters.
  A date answers "when must this happen", never "when should I look at this".
- **Errand** - needs doing eventually, no rush. **Undated**, in its working
  section. This is the free-time list: when the day is clear, open a project and
  take errands off the top. Work waiting on a non-date trigger ("replace the
  headlights when they die") is an errand with the trigger recorded.
- **Idea** - happens only if the user chooses it. **Undated**, in the project's
  **Ideation** section (C2). Not work; a candidate for work.

**Setting the date.**

- An **explicit stated day** sets it ("tomorrow", "Friday", a start time).
- **No must-happen-by, no date.** Never date something to keep it visible.
- **Classify at sorting, not at capture.** The question that settles it: *is
  there an action here that cannot wait?* Yes: task. No, but it needs doing:
  errand. No: idea.
- **When unsure, the default is idea** - promotion is cheap, a flooded day is
  not. Propose and confirm (A7).
- **Date calls are the user's.** Propose a day and the reasoning; they set it.

**Overdue is the feedback loop.** It covers dated tasks only, which keeps it
short. A short overdue list is normal. A growing one means dates are being used
for "look at this" - the fix is to un-date what never had a real by-when.
**A task pushed three times is asking a question:** date it seriously, break out
the smaller thing actually blocking it, kill it, or admit it is an errand and
un-date it. **Killing is first-class.** **Never auto-move a slipped task**;
silent rescheduling destroys trust in the dates.

**Ideas.** Undated, in Ideation, never dated to resurface. **Promotion is
deliberate:** when an action appears that cannot wait, the idea becomes a task
or errand and moves to a working section. An idea **untouched for 90+ days is a
prune candidate** - proposed, never automatic - and anything pruned is recorded
somewhere durable first.

**Deadlines and recurrence.** A deadline is the immovable done-by date ("by
Friday", "before April 15") and is never invented. A firm deliverable often has
both: the deadline is the hard date, the due date is when the work is planned,
meaningfully before it. **Recurrence lives on the due date**; deadlines are
one-off. **Preserve existing recurrence** when editing dates. Parse relative
dates in the user's Todoist timezone.

## A6. Labels

**Labels are the user's, and deliberately few.** A label is a flag the user
raises by hand: never a routing signal, never a category, never applied on
their behalf. Categories are what projects and sections are for. A new label
needs a reason a section cannot serve: it cuts across projects and is
genuinely transient.

## A7. Hard rules

- **Never leave a task unclassified.** Every task ends with a project and a
  section. Inbox is a start state, never a destination.
- **Never invent** a date, deadline, or priority (A4, A5).
- **Never auto-move a slipped or overdue task.** Propose; the user decides.
- **Never force a task into a home that does not fit.** Recommend a structure
  change instead (D2).
- **When a call is genuinely ambiguous, ask** rather than guess.
- **Preserve user-set signal.** Do not silently remove or downgrade a priority,
  date, deadline, recurrence, or description the user set; surface the change.
- **The board is the user's surface.** Work an agent can do itself does not go
  into the user's projects. If the user keeps an agent handoff project or label
  (their conventions name it), propose that; otherwise keep it off the board.
- **Be idempotent.** Leave any field that already meets the standard.
- **Closing a recurring task a skill worked:** if the skill itself performed and
  verified the real-world action, complete the task and say so. If the user
  performs the final step, complete it only after they confirm. Never complete
  before the action, never silently.

---

# PART B - CORE TRANSFORMATION

The steps for bringing one task to standard against the live tree. A caller may
run a subset (polish in place, or compare mode for a task raised for rework).

0. **Split** bundled independent actions into atomic tasks.
1. **Classify** to the deepest project that genuinely fits, then the section
   that fits (the general section if none is specific). Never Inbox, never the
   project root. An idea routes to the project's Ideation section. Resolve
   ambiguity by purpose, using project descriptions (D1); if still unclear, ask.
2. **Normalize the title** (A2).
3. **Decide the description** (A3).
4. **Set priority** (A4).
5. **Decide the kind, then the dates** (A5).

At each step, leave a field that is already correct. **Compare mode** (a task
the user raised for rework): compute the ideal end-state, bring the diffs to the
user; if there are none, say so and ask what prompted it.

---

# PART C - PROJECT STRUCTURE

## C1. Project and section descriptions

Every project except Inbox carries a native **project description** stating its
durable purpose; a section may carry one too. They render in the app and are
readable through the connector, so they are both documentation and the routing
signal.

- **Purpose, not structure.** Do not enumerate sections or sub-projects or
  write routing paths ("X > Y > Z"); they go stale on reorg.
- **Brief.** A theme-level statement of what goes in the project.
- A missing or empty description is a gap to surface (D2). (Read-path quirks:
  connector notes.)

## C2. The levels

A shallow tree whose shape fits in one sentence: **a few top-level domains, each
with its everyday areas as sections and its distinct bodies of work one level
down.** A common starting point is two domains, **Personal** and **Work**.

- **Domain** - a top-level project. Its everyday, ongoing areas (for Personal:
  home, money, health, travel; for Work: admin, recurring meetings, career) are
  **sections directly on the domain**, so opening the domain shows the whole
  everyday list.
- **Sub-project** - a distinct body of work with a start, a deliverable, or
  enough going on to need its own sections (a side project, a renovation, a
  work initiative). **Exactly one level below a domain.**
- **Sections** - the only subdivision inside a project. Every task-holding
  project has at least one.
- **`Ideation`** - the optional standard section for a project's ideas, on any
  domain or sub-project. Ideas only: never an errand, never a dated task.
- **Agent handoff (optional)** - a separate top-level project where an agent
  leaves work that needs the user's hands, if the user wants one.

**Sub-projects are one level deep only.** Nothing nests below a sub-project.
Everyday areas stay sections so the domain view stays useful; Todoist project
views do not roll up sub-projects.

## C3. How structure evolves

A concern enters at the lightest level that holds it and graduates as it earns
more: tasks in a section first; its own sub-project when it becomes a distinct
body of work with enough internal structure to need sections. Nothing graduates
past a sub-project; a new domain is rare and the user's call. A container whose
work has stopped **retires by archiving**; its live tasks move to whatever
absorbed the work.

## C4. Design heuristics

- **Lean above the section floor.** Fewest sections that work; do not pre-build
  empty ones. Ideation is created when the first idea needs it.
- **One home per concern.** Persistent routing ambiguity usually means
  overlapping structure.
- **Mirror how the user actually works**, not an ideal taxonomy. A container
  with no completions in months is evidence.
- **Flatter when in doubt.**

## C5. Colors (optional)

Colors can mark what kind of thing a project is: one color per domain, and one
shared sibling color for that domain's sub-projects. If the user's conventions
define a palette, check for drift against it; if not, colors are not checked.

---

# PART D - ROUTING AND EVOLUTION

## D1. Routing

- **Route by described intent**, not a lookup map, so a new project is routable
  on its first run.
- **The live tree says what exists; descriptions say what each is for.**
- **The narrower owner wins** when a task fits both a broad parent and a more
  specific project. When two genuinely compete, ask.
- **Ask whose work it is** before where it goes (A7).

## D2. Recommending structure changes

When the structure shows strain, recommend a change rather than forcing a poor
fit. The agent proposes; the user approves. A new-project recommendation
includes its description.

- **Skeleton checks** (no task content needed): missing or malformed
  descriptions; color drift (when a palette is defined); illegal nesting (more
  than one level below a domain); a task-holding project with no section; tasks
  at a project root; label misuse.
- **Per task:** a task with no good home - recommend a section, sub-project, or
  clarified boundary.
- **Staleness:** a project or section with tasks but no completions in a long
  stretch - an archive candidate, never archived unasked.
- **Stale ideas:** Ideation items untouched for 90+ days, proposed as one batch
  and recorded before deletion.

These surface in a maintenance pass, not per capture. Re-parenting a project
cannot be done through the connector (see connector notes); say so in the
recommendation.
