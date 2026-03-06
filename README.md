# vibe-kanban-mcp-skill

`vibe-kanban-mcp-skill` publishes a reusable agent skill for working with the Vibe Kanban MCP Server.

The skill gives coding agents a focused reference for Vibe Kanban workflows: discovering organizations and projects, creating and updating issues, assigning work, managing tags and relationships, configuring repository scripts, and starting workspace sessions that launch coding agents against tracked tasks.

## Included skill

- `vibe-kanban-mcp`

## Install

List the skills available in this repository:

```bash
npx skills add ccsert/vibe-kanban-mcp-skill --list
```

Install the skill:

```bash
npx skills add ccsert/vibe-kanban-mcp-skill --skill vibe-kanban-mcp
```

Install to a specific agent:

```bash
npx skills add ccsert/vibe-kanban-mcp-skill --skill vibe-kanban-mcp -a opencode
```

Install globally instead of per-project:

```bash
npx skills add ccsert/vibe-kanban-mcp-skill --skill vibe-kanban-mcp -g
```

## What this skill is for

Use this skill when a user wants an agent to work with Vibe Kanban through MCP, especially when they want to:

- list organizations, members, and projects
- create, inspect, update, search, or delete issues
- assign issues to teammates
- add and remove issue tags
- create dependency or duplicate relationships between issues
- inspect or update repository lifecycle scripts
- list workspaces, clean them up, or rename/archive them
- start a Vibe Kanban workspace session that launches a coding agent
- link an existing workspace back to a remote issue

## Supported tool calls

This skill currently documents the following Vibe Kanban MCP tool calls.

### Organizations and projects

| Tool | Purpose |
|---|---|
| `list_organizations` | List organizations available to the current user |
| `list_org_members` | List members in an organization and resolve `user_id` values |
| `list_projects` | List projects in an organization and resolve `project_id` values |

### Issues

| Tool | Purpose |
|---|---|
| `create_issue` | Create a new issue or sub-task |
| `list_issues` | Search and filter issues |
| `get_issue` | Fetch a full issue with tags, relationships, PRs, and sub-issues |
| `update_issue` | Update title, description, status, priority, or parent issue |
| `delete_issue` | Permanently delete an issue |

### Assignees

| Tool | Purpose |
|---|---|
| `list_issue_assignees` | List current assignees on an issue |
| `assign_issue` | Assign a user to an issue |
| `unassign_issue` | Remove an assignee from an issue |

### Tags

| Tool | Purpose |
|---|---|
| `list_tags` | List tags defined in a project |
| `list_issue_tags` | List tags attached to a specific issue |
| `add_issue_tag` | Attach a tag to an issue |
| `remove_issue_tag` | Remove a tag from an issue |

### Issue relationships

| Tool | Purpose |
|---|---|
| `create_issue_relationship` | Create a relationship such as `blocking`, `related`, or `has_duplicate` |
| `delete_issue_relationship` | Remove an issue relationship |

### Workspaces and sessions

| Tool | Purpose |
|---|---|
| `list_workspaces` | List local workspaces with filters |
| `update_workspace` | Rename, pin, or archive a workspace |
| `delete_workspace` | Delete a local workspace and optionally related remote state or branches |
| `start_workspace_session` | Create a workspace and launch a coding agent against a task or issue |
| `link_workspace` | Attach an existing workspace to a remote issue |

### Repositories and scripts

| Tool | Purpose |
|---|---|
| `list_repos` | List repositories configured in Vibe Kanban |
| `get_repo` | Inspect a repo and its configured scripts |
| `update_setup_script` | Set or clear the repo setup script |
| `update_cleanup_script` | Set or clear the repo cleanup script |
| `update_dev_server_script` | Set or clear the repo dev server script |

### Context-aware behavior covered by the skill

The skill also explains how context inference works when tools run inside an active Vibe Kanban workspace session. In that mode, some parameters can often be omitted because they are inferred automatically:

- `organization_id`
- `project_id`
- `workspace_id`

The main exception documented by the skill is `list_projects`, which still requires an explicit `organization_id`.

## Supported workspace executors

The skill documents the accepted `executor` values for `start_workspace_session`:

- `CLAUDE_CODE`
- `AMP`
- `GEMINI`
- `CODEX`
- `OPENCODE`
- `CURSOR_AGENT`
- `QWEN_CODE`
- `COPILOT`
- `DROID`

The server accepts these case-insensitively and supports hyphen or underscore variants.

## Typical workflows

### Create issues from planning and start implementation

```text
list_organizations
list_projects
create_issue
start_workspace_session
```

### Assign work to teammates

```text
list_org_members
list_issues
assign_issue
list_issue_assignees
```

### Configure repo lifecycle scripts

```text
list_repos
get_repo
update_setup_script
update_dev_server_script
update_cleanup_script
```

### Link an existing workspace to a tracked issue

```text
list_workspaces
list_issues
link_workspace
```

## Repository structure

```text
.
├── README.md
├── SKILL.md
└── references/
    ├── issues.md
    ├── organizations-projects.md
    ├── overview.md
    ├── repos.md
    └── workspaces-sessions.md
```

## Reference guide map

| File | Covers |
|---|---|
| `SKILL.md` | Trigger description, tool summary, and usage guidance |
| `references/overview.md` | Server startup, MCP registration, context inference, executors, common workflows |
| `references/organizations-projects.md` | Organization, member, and project discovery |
| `references/issues.md` | Issues, assignees, tags, and relationships |
| `references/workspaces-sessions.md` | Workspace lifecycle and session launch workflows |
| `references/repos.md` | Repository inspection and lifecycle script configuration |

## Requirements

- an agent or MCP client that supports `SKILL.md`-based skills
- access to a Vibe Kanban MCP Server environment
- a local MCP configuration that can connect to Vibe Kanban

## Local MCP server setup

The referenced docs cover two common ways to start the server:

```bash
npx vibe-kanban --mcp
```

or:

```bash
./vibe-kanban-mcp
```

Example MCP client configuration:

```json
{
  "mcpServers": {
    "vibe_kanban": {
      "command": "npx",
      "args": ["-y", "vibe-kanban@latest", "--mcp"]
    }
  }
}
```

## Why this repo may show up in CLI before search

`skills` CLI can install this repository as soon as the GitHub repo and `SKILL.md` are valid. `skills.sh` web search and ranking depend on telemetry and indexing, so discovery on the website may lag behind direct installation.

## Development

The skill definition lives in `SKILL.md`, and the detailed operational docs live in `references/`. When Vibe Kanban MCP tools change, update both the summary in `SKILL.md` and the matching reference file.

## License

Add the license that matches your intended distribution model for this repository.
