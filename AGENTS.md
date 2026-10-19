# Agent guidance

<!-- project-guide:base start (0.7.1) -->
## Working in this repo

This repo follows the project-guide standard, version 0.7.1
(https://github.com/rschlek/project-guide). This block is replaced when the
standard updates; put project-specific guidance in the section below it.

- Read `README.md` and `project.yaml` first: what this is, who it is for,
  and where related things live.
- Before changing `project.yaml`, `README.md`, `AGENTS.md`, `CLAUDE.md`, or
  the repo's layout, read `docs/project-conventions.md` and keep to it.
- Make one change for one purpose, and run the checks this repo documents
  before proposing it.
- Commit only the files you changed, by path. Never commit credentials,
  tokens, or data extracts.
- If `project.yaml` says `visibility: public`, anyone can read this repo:
  write no person, employer, team, host, or machine names into it.
- If it says `visibility: internal`, everyone in the organization that
  hosts this repo can read it: the organization's own names are fine,
  other people's personal details are not.
- Documents are for someone who opens this repo without the user's memory.
  If `project.yaml` says `shared: true` or sets `visibility`, write the plan
  for a piece of work and the decisions a reader needs into the repo: plans
  in `docs/plan/`, decisions in `docs/decisions.md`. Otherwise write such
  documents only when asked.
- Never track progress in a document: no checkboxes, status lines, or live
  handoff files. Read `docs/project-conventions.md` before writing or moving
  anything under `docs/`, and leave existing documents as they are.
- In a repo other people use, changes go in through its review process,
  not straight to the main branch.
- Keep work in progress in a worktree under `.claude/worktrees/`, one
  session per worktree, and push its branch before leaving it.
- Do not move or rename the repo, and keep `project.yaml` true when the
  project changes.
<!-- project-guide:base end -->

## This project

`todoist` is a Claude Code and Codex plugin; the repo root is the plugin root.

- Layout: `.claude-plugin/plugin.json` and `.codex-plugin/plugin.json`
  (neither pins `version`, so each promoted commit is a new version),
  `.mcp.json` (the Todoist MCP server), `skills/<name>/SKILL.md`, and the
  shared `references/`.
- `references/default-conventions.md` is the shipped default for a
  well-formed task and tree, and `references/conventions-loading.md` is the one
  place the rule for finding a user's own conventions lives (memory tool, then
  `~/.config/todoist-skills/conventions.md`, then the default). Skills point at
  these and never redefine them.
- This repo is public. Never commit a user's own conventions, project or
  section names, Todoist IDs, people, employers, or machine paths; those live in
  the user's memory or per-user conventions file. Write skill text for any user
  ("the user", "a connected memory tool").
- Branches: work lands on `main`; `stable` is the release branch that
  installs follow. `stable` moves only by a deliberate fast-forward from
  `main`, never as a side effect of other work.
- It ships for both harnesses: `.claude-plugin/plugin.json` for Claude Code
  and `.codex-plugin/plugin.json` for Codex. Keep the two manifests' names and
  descriptions consistent. It is distributed through the paradise marketplace
  (https://github.com/rschlek/paradise), which lists it in both its Claude and
  its Codex catalog.
- Authoring rules: frontmatter `name` equals the skill's directory; a
  description rich in trigger phrases that names its sibling skills for what it
  is not; skills stay under about 100 lines; siblings are referenced as
  `todoist:<name>`.
- Checks before proposing a change: every manifest parses; every `SKILL.md`
  has `name` and `description` with `name` equal to its directory; every
  `todoist:<name>` reference resolves to a skill; every `references/` path a
  skill names exists.
