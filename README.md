# todoist

Todoist workflow skills for Claude Code and Codex: capture well-formed tasks,
triage the Inbox, walk the overdue pile, prune stale ideas, reconcile the board
against what actually happened, and review the shape of the project tree. Every
skill works against **your** conventions for how your board is organized, and
falls back to a sensible shipped default until you write your own.

## Skills

Each skill's directory name is its invocation name (`todoist:add`, ...).

- `add` - add one well-formed task, errand, or idea, placed in the right
  project and section, after a preview you approve.
- `triage` - empty the Inbox into well-formed, correctly placed items from one
  summary table, then walk the overdue pile and re-decide each item.
- `prune` - propose ideas untouched for 90+ days for deletion, recording each
  approved one before it is deleted.
- `reconcile` - audit the board against your memory (read-only report), then
  decide the findings in small rounds.
- `structural-review` - audit the project and section tree and its
  descriptions against your conventions.
- `full-review` - triage, prune, reconcile, and structural review in one
  sitting.

Doing a task is outside this plugin; a tracker-agnostic `tackle` skill ships in
the [playbook](https://github.com/rschlek/playbook) plugin.

Nothing is written to Todoist without your approval; dates, deletions, and
structure changes are always proposed first.

## Install

1. **Install the plugin.** It is listed in the
   [paradise](https://github.com/rschlek/paradise) catalog:
   - Claude Code, inside a session: `/plugin marketplace add rschlek/paradise`,
     then `/plugin install todoist@paradise` (or from a shell:
     `claude plugin marketplace add rschlek/paradise` and
     `claude plugin install todoist@paradise`).
   - Codex: `codex plugin marketplace add rschlek/paradise`, then
     `codex plugin add todoist@paradise`.
   - Without a marketplace (Claude Code): this repo is a single plugin, not a
     marketplace, so `marketplace add` does not accept it directly. Clone it and
     load it for a session with
     `git clone https://github.com/rschlek/todoist && claude --plugin-dir ./todoist`.

   Start a new session after installing so the skills load.
2. **Connect Todoist.** The plugin declares Doist's hosted Todoist MCP server
   (`https://ai.todoist.net/mcp`) in `.mcp.json`. In Claude Code, run `/mcp`
   once and authorise `todoist` in the browser. On claude.ai or Claude Desktop,
   the built-in Todoist connector works as well: it exposes the same tools. Do
   not substitute a community Todoist MCP server; its tool names differ.

## Your conventions

The skills need to know how your board is shaped: your top-level areas, where
ideas live, how you use dates and priorities, whether you keep a project for
agent handoffs. Each run loads them from the first of:

1. **A connected memory tool**, if the session has one: the skills recall your
   stored Todoist conventions.
2. **A per-user file at `~/.config/todoist-skills/conventions.md`** (on Windows,
   `%USERPROFILE%\.config\todoist-skills\conventions.md`). The same path is read
   by Claude Code and Codex on every platform.
3. **The shipped default**, [`references/default-conventions.md`](references/default-conventions.md).

To make it yours, copy the default to the per-user path and edit it. Anything
your file leaves out falls back to the default. The full rule is in
[`references/conventions-loading.md`](references/conventions-loading.md).

### What the default contains

- **A task standard:** one action per task, verb-first sentence-case titles, a
  description only for context the title cannot carry, priority p4 by default,
  and no durations.
- **Three kinds of item:** a **task** carries a due date only when something
  must happen by then; an **errand** needs doing eventually and sits undated in
  its section; an **idea** sits undated in an `Ideation` section until you
  promote it.
- **Overdue as a feedback loop:** slipped tasks are re-decided, never silently
  moved; a task pushed three times gets a real answer; killing a task is a
  correct outcome.
- **A shallow tree:** a few domains (for example Personal and Work) with
  everyday areas as sections and distinct bodies of work one level down, every
  task in a section, and project descriptions that say what each project is for.
- **Maintenance:** stale ideas proposed for pruning after 90 days, and a
  structure review that flags missing descriptions, illegal nesting, and
  overlapping projects.

### Records outside Todoist

`prune` and `reconcile` keep a durable record of what they removed or decided:
in your memory tool if one is connected, otherwise in
`~/.config/todoist-skills/log.md`.

## Shared references

- `references/conventions-loading.md` - how a skill finds your conventions.
- `references/default-conventions.md` - the shipped default conventions.
- `references/todoist-connector-notes.md` - quirks of the Todoist MCP tools
  (move rules, recurrence-safe rescheduling, read-path asymmetries, retries).
- `references/ios-quick-capture.md` - building a one-press iPhone shortcut that
  drops raw captures into the Inbox for `triage`.

Skills read these via `${CLAUDE_PLUGIN_ROOT}/references/`, so they travel with
the plugin.

## Versioning

Neither manifest pins a `version`, so the harness uses the git commit SHA as
the version: every commit on the published branch is a new version.
