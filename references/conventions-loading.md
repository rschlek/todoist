# Loading the user's conventions

Every skill in this plugin works against a set of **conventions**: what a
well-formed task looks like, how the board is shaped, and how items are routed.
The conventions belong to the user, not to the plugin. Load them once per run,
before the first Todoist call, from the first source that has them:

1. **A connected memory tool.** If the session has a memory or knowledge tool
   (any backend that can recall stored facts or documents about the user),
   recall the user's Todoist conventions from it - try queries such as "Todoist
   conventions", "how I organize Todoist", and "Todoist project structure". If
   the recall surfaces a stored conventions document, open it in full rather
   than relying on fragments. Use what it returns only if it is substantive (a
   structure, a task model, or explicit rules), not a passing mention.
2. **The per-user conventions file** at `~/.config/todoist-skills/conventions.md`
   (`~` is the home directory on every platform; on Windows that is
   `%USERPROFILE%\.config\todoist-skills\conventions.md`). Read it if it exists.
3. **The shipped default**, `${CLAUDE_PLUGIN_ROOT}/references/default-conventions.md`
   (relative to this file: `default-conventions.md`).

Rules that hold whichever source wins:

- **The user's conventions override; the default fills gaps.** When the
  winning source is silent on something a skill needs (say, the priority scheme
  or the name of the ideas section), use the default for that point. Section
  labels the skills cite (A5, C2, D1, ...) refer to the default; map them to the
  matching rule in the user's conventions by meaning.
- **Say which source was used**, in one line, at the start of the run ("Using
  your conventions from memory" / "from ~/.config/todoist-skills/conventions.md"
  / "Using the plugin's default conventions"). When the default is in use, offer
  once per session to save a personalized copy to the per-user file.
- **Live structure still wins over any document.** Conventions describe shape
  and principles; what currently exists is always read from Todoist itself.
- **Never write to the conventions** as a side effect. Changing them is an
  explicit request from the user.

Connector behavior (tool quirks, retries, fallbacks) is separate and does not
vary by user: see `todoist-connector-notes.md`.

## Durable records without a memory tool

Two skills record things outside Todoist: `prune` records each idea before
deleting it, and `reconcile` retains its decisions. With a memory tool
connected, they retain there. Without one, they append a dated line to
`~/.config/todoist-skills/log.md` (create it if missing) and say so. `reconcile`
also needs a source of truth to audit against; without a memory tool it asks
the user what to audit against before starting.
