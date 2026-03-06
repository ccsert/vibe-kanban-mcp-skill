---
name: vibe-kanban-mcp
description: >
  Use this skill whenever the user wants to work with the Vibe Kanban MCP Server,
  Kanban MCP tools, or any workflow that creates, updates, lists, assigns, tags, links,
  or manages Vibe Kanban issues, projects, repositories, workspaces, or workspace
  sessions through an MCP client or coding agent. This skill is the reference and
  workflow guide for resolving org/project/workspace IDs, using context-aware tool
  calls, configuring repo scripts, and starting or linking workspace sessions. Trigger
  even when the user only says things like "vibe kanban", "kanban mcp", "create issue",
  "assign this issue", "start workspace session", "link workspace", or asks how to wire
  an agent, Claude Code, Codex, Cursor, OpenCode, Gemini, Copilot, Amp, Droid, or Qwen
  into Vibe Kanban programmatically.
---

# Vibe Kanban MCP Server — Skill

The Vibe Kanban MCP Server exposes a local Model Context Protocol interface that
external MCP clients (Claude Desktop, Raycast, in-workspace coding agents, etc.) use
to drive the full lifecycle of work: from organisation discovery all the way through
spawning a coding agent in an isolated workspace.

> Source: `crates/mcp/src/task_server/tools/`  
> Last updated: 2026-03-05

---

## Quick navigation

| What you need | Reference file |
|---|---|
| Start the server, understand context inference, executor values, typical workflows | [`references/overview.md`](references/overview.md) |
| `list_organizations`, `list_org_members`, `list_projects` | [`references/organizations-projects.md`](references/organizations-projects.md) |
| Create / list / get / update / delete issues | [`references/issues.md`](references/issues.md) |
| Assignees, tags, issue relationships | [`references/issues.md`](references/issues.md) |
| Workspaces, `start_workspace_session`, `link_workspace` | [`references/workspaces-sessions.md`](references/workspaces-sessions.md) |
| Repositories and script management | [`references/repos.md`](references/repos.md) |

---

## Tool module summary

| Module | Tools | Description |
|---|---|---|
| `organizations` | 2 | Organisation & member listing |
| `remote_projects` | 1 | Project listing |
| `remote_issues` | 5 | Issue CRUD |
| `issue_assignees` | 3 | Assignee management |
| `issue_tags` | 4 | Tag management |
| `issue_relationships` | 2 | Issue relationships |
| `workspaces` | 3 | Local workspace CRUD |
| `repos` | 4+ | Repository & script management |
| `task_attempts` | 2 | Workspace session start & link |
| `context` | N | Context inference (within active workspace) |

---

## How to use this skill

1. **Identify what the user wants to do** — pick the closest workflow from the table
   above and read the matching reference file.

2. **Check context availability** — many `project_id` / `organization_id` /
   `workspace_id` parameters are inferred automatically when the tool runs inside an
   active Vibe Kanban workspace session. Read
   [`references/overview.md`](references/overview.md) for details.

3. **Follow the workflow examples** — each reference file contains end-to-end call
   sequences. Always resolve IDs by calling list tools before using them in
   create/update/delete calls.

4. **Pick the right executor** — when calling `start_workspace_session`, the executor
   string must match one of the supported values.  
   Full list: [`references/overview.md`](references/overview.md) → "Supported Executors".
