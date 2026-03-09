# vibe-kanban-mcp-skill

`vibe-kanban-mcp-skill` publishes a reusable agent skill for working with Vibe Kanban via **MCPorter CLI**.

The skill gives coding agents a focused reference for Vibe Kanban workflows: discovering organizations and projects, creating and updating issues, assigning work, managing tags and relationships, configuring repository scripts, and starting workspace sessions that launch coding agents against tracked tasks.

All tool calls are executed through MCPorter (`mcporter call vibe_kanban.<tool> param=value`), so the agent only needs terminal access and a working Vibe Kanban CLI installation.

## Included skill

- `vibe-kanban-mcp`

## Install

List the skills available in this repository:

```bash
skills add ccsert/vibe-kanban-mcp-skill --list
```

Install the skill:

```bash
skills add ccsert/vibe-kanban-mcp-skill --skill vibe-kanban-mcp
```

Install to a specific agent:

```bash
skills add ccsert/vibe-kanban-mcp-skill --skill vibe-kanban-mcp -a opencode
```

Install globally instead of per-project:

```bash
skills add ccsert/vibe-kanban-mcp-skill --skill vibe-kanban-mcp -g
```

## What this skill is for

Use this skill when a user wants an agent to work with Vibe Kanban via MCPorter, especially when they want to:

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

This skill currently documents the following Vibe Kanban tool calls as invoked through MCPorter.

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

### Context behavior

All parameters (including `organization_id`, `project_id`, `workspace_id`) must be provided explicitly in MCPorter calls. Use discovery tools like `list_organizations`, `list_projects`, and `list_workspaces` first to resolve the required IDs.

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

## MCPorter setup

Register Vibe Kanban once, then call any tool from the terminal:

### Setup

```bash
# Create config/mcporter.json (or ~/.mcporter/mcporter.json for global)
cat > config/mcporter.json << 'EOF'
{
  "mcpServers": {
    "vibe_kanban": {
      "command": "vibe-kanban",
      "args": ["--mcp"]
    }
  }
}
EOF

# Verify
mcporter list vibe_kanban
```

### Call syntax

```bash
# Flag style
mcporter call vibe_kanban.list_organizations
mcporter call vibe_kanban.create_issue title="Add login page" priority=high

# Function-call style
mcporter call 'vibe_kanban.create_issue(title: "Add login page", priority: "high")'

# Shorthand (infers call verb)
mcporter vibe_kanban.list_issues status=backlog
```

### Ad-hoc usage (no config)

```bash
mcporter call --stdio "vibe-kanban --mcp" list_organizations
```

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
| `references/overview.md` | MCPorter setup, explicit ID handling, executors, common workflows |
| `references/organizations-projects.md` | Organization, member, and project discovery |
| `references/issues.md` | Issues, assignees, tags, and relationships |
| `references/workspaces-sessions.md` | Workspace lifecycle and session launch workflows |
| `references/repos.md` | Repository inspection and lifecycle script configuration |

## Requirements

- an agent that supports `SKILL.md`-based skills
- `mcporter` installed and available on `PATH`
- `vibe-kanban` installed and available on `PATH`

## Vibe Kanban server setup

The referenced docs assume Vibe Kanban is started by MCPorter as a stdio server:

```bash
vibe-kanban --mcp
```

Example MCPorter server configuration:

```json
{
  "mcpServers": {
    "vibe_kanban": {
      "command": "vibe-kanban",
      "args": ["--mcp"]
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
