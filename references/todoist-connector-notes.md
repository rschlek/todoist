# Todoist Connector Notes

**Purpose:** operational notes on the Todoist MCP tools - quirks, retries, and
fallbacks. They are properties of the connector, not of any user, so they apply
whatever conventions are loaded (`conventions-loading.md`).

## How the connector is attached

The skills call Doist's own Todoist MCP tools by their bare names
(`get-overview`, `find-projects`, `fetch-object`, `add-tasks`, `find-tasks`,
`update-tasks`, ...). Two surfaces expose those tools, and either works:

- **The hosted server declared in this plugin's `.mcp.json`**
  (`https://ai.todoist.net/mcp`). Enabling the plugin brings it along. It
  carries no secret: auth is an interactive OAuth flow per device. In Claude
  Code, run `/mcp` once and authorise `todoist` in the browser; until then the
  server is listed but its tools do not resolve, which looks exactly like "the
  connector isn't attached".
- **The Todoist connector in claude.ai / Claude Desktop**, which is the same
  Doist backend under the same tool names.

**Never substitute a community Todoist MCP server.** They expose different tool
names, so the skills would fail confusingly rather than cleanly.

## Connector quirks

### Native descriptions: read-path asymmetry (projects vs sections)

Project **and** section descriptions are readable and writable via the connector (they back the C1 model). But the read path is not symmetric:

- **Section** descriptions read back **inline** from `get-overview` (per-project) and `find-sections`. Set via `add-sections` / `update-sections`.
- **Project** descriptions do **not** appear in `get-overview` - it returns only `id`/`name` for the project. Read a project description via `find-projects` (bulk, includes `description`) or `fetch-object` (`type: project`). Set via `add-projects` / `update-projects`.
- An **empty** description is **omitted** from listings entirely - `find-sections`/`get-overview` drop the field, and only `fetch-object type: project` echoes it as `""`. Treat *field-missing = no description*, never assume the feature is off.

### Transient errors are common

The connector throws occasional transient errors (often Cloudflare 502s on the MCP proxy). Retry a failed call 2-3 times before treating it as real.

### Move-a-task quirk: exactly one destination field

`update-tasks` accepts **exactly one** of `projectId`, `sectionId`, or `parentId` per call - passing two errors out. Use `sectionId` alone to move into a section (it implies the project and moves across projects correctly), or `projectId` alone for a project root. (`add-tasks` is different: it accepts `projectId` and `sectionId` together.) Field updates (content, labels, etc.) can ride along in the same call as the move.

### Re-parenting a project silently fails

`update-projects` does not accept a `parentId` field - the connector accepts the call and reports success but strips `parentId` from the update. To move a project under a different parent, do it manually in the Todoist UI; there is no programmatic path through this connector.

### `project-move` is the wrong tool

`project-move` exists on the connector but moves projects between **workspaces**, not parents within a workspace. Never use it for re-parenting.

### A set `duration` cannot be cleared

`update-tasks` validates `duration` as a positive length only — both `"remove"` and `"0m"` are rejected (found 2026-08-30). A duration that was set (the conventions say never set one) can only be cleared by hand in the Todoist app; surface it as a manual to-do rather than retrying formats. Note the blast radius of any rejected field: the **whole task item** fails, so none of that item's other field updates in the call apply — though other task items in the same call still process. Re-send the surviving fields without the offender.

### `find-completed-tasks` window is capped at 3 months

A call with a `since`/`until` window longer than 3 months returns 400. To audit a longer span, chunk into consecutive ≤3-month windows.

### `find-tasks` searchText is phrase matching

`searchText` does *phrase* matching, not token matching - a query like `"cabin filter"` misses an existing `"cabin air filter"`. To find reworded duplicates, fetch the project (or section) broadly (no `searchText`, or a single distinctive keyword) and compare returned tasks semantically.

### `reschedule-tasks` preserves recurrence; `update-tasks` `dueString` does not

For a task that already has a recurring schedule, use `reschedule-tasks` (not `update-tasks` `dueString`) to set or move its next due date - otherwise the repeat pattern is wiped. `reschedule-tasks` errors on a *dateless* task: a dateless non-recurring task gets its first date via `update-tasks` `dueString`.

### Clearing a due date: `update-tasks` `dueString: "remove"`

Un-dating is routine under the task / errand / idea model (default conventions A5): errands and ideas leave triage undated, and an overdue item that never had a must-happen-by is un-dated rather than pushed. `update-tasks` with `dueString: "remove"` clears the due date (verified live 2026-09-17: the task came back with no `dueDate` field). `deadlineDate: "remove"` clears a deadline the same way. `reschedule-tasks` cannot do this - it only sets dates.

### Per-task activity cannot be read

`find-activity` ignores `objectId` and `taskId` and returns account-wide
activity, so it cannot say when one task was last touched. Age a task from its
`addedAt`, plus any dated edit in its description ("UPDATED <date>: ...") or a
dated comment (`find-comments`).
