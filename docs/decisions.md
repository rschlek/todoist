# Decisions

Newest first.

## 2026-10-19 - Conventions belong to the user, not the plugin

**Decided:** the plugin ships a generic default conventions file and a single
loading rule (`references/conventions-loading.md`). Each run uses the user's
conventions from a connected memory tool, else
`~/.config/todoist-skills/conventions.md`, else the default; anything the
user's conventions leave out falls back to the default.

**Why:** the skills are only as good as their picture of the user's board, and
that picture differs per person. Keeping it outside the repo lets anyone install
the plugin unchanged, and keeps one user's projects and habits out of a public
repo. One path under `~/.config` works for every harness on every platform. The
rule lives in one file so the skills cannot drift apart on it.

## 2026-10-19 - No shipped iOS shortcut file

**Decided:** the plugin documents how to build the one-press capture shortcut
(`references/ios-quick-capture.md`) instead of shipping a `.shortcut` export.

**Why:** a signed export cannot be edited and is bound to the account and the
project it was built against.

## 2026-10-19 - Doist's Todoist MCP tools only

**Decided:** skills call Doist's own Todoist MCP tools by their bare names, via
the hosted server in `.mcp.json` or the claude.ai Todoist connector. Community
Todoist MCP servers are not supported.

**Why:** both Doist surfaces expose the same tool names and behavior, so the
skills and the connector notes hold on either; community servers use other
names and would fail in confusing ways.

## 2026-10-19 - A due date means must-happen-by

**Decided:** every item is a task (dated only when something must happen by
then), an errand (undated, in its working section), or an idea (undated, in
Ideation). Unclear defaults to idea.

**Why:** dating everything floods the daily view until it stops being read, and
leaving important work undated hides it. Splitting by kind keeps both failure
modes away: anything with a real by-when is dated, and everything else has a
section that is its home.
