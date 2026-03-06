# Overview — Vibe Kanban MCP Server

## Table of contents

1. [Starting the server](#starting-the-server)
2. [Context inference](#context-inference)
3. [Supported executors](#supported-executors)
4. [Typical workflows](#typical-workflows)

---

## Starting the server

The MCP server ships with the Vibe Kanban package. Two launch modes:

```bash
# Via npx (no local build required)
npx vibe-kanban --mcp

# Via locally built binary
./vibe-kanban-mcp
```

The server is **local-only** — it cannot be reached from a public URL.  
Any application on the same machine can connect to it as a standard MCP client.

### Registering the server in an MCP client

Add the following to your client's MCP server configuration (e.g. Claude Desktop,
Raycast, a coding agent's `mcpServers` config block):

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

---

## Context inference

When the MCP server is running **inside an active Vibe Kanban workspace session**,
the following parameters are resolved automatically from context and may be omitted:

| Parameter | Inferred from |
|---|---|
| `project_id` | Current workspace's linked remote project |
| `organization_id` | Current workspace's linked organisation |
| `workspace_id` | Current workspace |

The single exception is `list_projects`, which always requires an explicit
`organization_id` even when running inside a workspace.

---

## Supported executors

Use these values for the `executor` field in `start_workspace_session`.  
The server accepts them case-insensitively and with either hyphens or underscores.

| Value | Agent |
|---|---|
| `CLAUDE_CODE` | Anthropic Claude Code |
| `AMP` | Google Amp |
| `GEMINI` | Google Gemini CLI |
| `CODEX` | OpenAI Codex |
| `OPENCODE` | OpenCode |
| `CURSOR_AGENT` | Cursor Agent |
| `QWEN_CODE` | Alibaba Qwen Code |
| `COPILOT` | GitHub Copilot |
| `DROID` | Droid Agent |

---

## Typical workflows

### 1. Turn a description into issues and start work

This is the most common end-to-end pattern.

```
list_organizations          → get organization_id
list_projects               → get project_id
create_issue (×N)           → create structured issues
start_workspace_session     → kick off a coding agent for each issue
```

### 2. Team assignment workflow

```
list_org_members            → find user_id values
list_issues                 → find issue_id values
assign_issue (×N)           → link users to issues
list_issue_assignees        → verify assignments
```

### 3. Multi-repo workspace

```
list_repos                  → get repo_id values
start_workspace_session     → pass multiple {repo_id, base_branch} objects in repos[]
```

### 4. Planning agent inside Vibe Kanban

A coding agent running *within* a Vibe Kanban workspace can use the MCP server to
populate issues automatically:

1. Create a planning issue with a custom agent profile (see Agent Configurations docs).
2. The agent explores the codebase and calls `create_issue` for each sub-task.
3. Other agents pick up those issues via `start_workspace_session`.

This closes the loop from high-level intent to executable work items without manual
data entry.
