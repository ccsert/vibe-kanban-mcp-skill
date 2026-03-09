# Workspaces & Sessions

> All examples use MCPorter CLI: `mcporter call vibe_kanban.<tool> param=value`

## Table of contents

1. [list_workspaces](#list_workspaces)
2. [update_workspace](#update_workspace)
3. [delete_workspace](#delete_workspace)
4. [start_workspace_session](#start_workspace_session) ← core tool
5. [link_workspace](#link_workspace)

---

## list_workspaces

Return a paginated list of local workspaces, with optional filters.

**Parameters:**

| Parameter | Required | Type | Notes |
|---|---|---|---|
| `archived` | No | bool | Filter by archived state |
| `pinned` | No | bool | Filter by pinned state |
| `branch` | No | string | Exact branch name match (case-insensitive) |
| `name_search` | No | string | Fuzzy match on workspace display name |
| `limit` | No | int | Default 50 |
| `offset` | No | int | Default 0 |

**Response:**

```json
{
  "workspaces": [
    {
      "id": "uuid",
      "branch": "feature-login",
      "archived": false,
      "pinned": true,
      "name": "Login page",
      "created_at": "2024-01-01T00:00:00Z",
      "updated_at": "2024-01-01T00:00:00Z"
    }
  ],
  "total_count": 10,
  "returned_count": 5,
  "limit": 50,
  "offset": 0
}
```

**MCPorter:**

```bash
mcporter call vibe_kanban.list_workspaces
mcporter call vibe_kanban.list_workspaces archived=true
mcporter call vibe_kanban.list_workspaces name_search="login" limit=10
```

---

## update_workspace

Update display properties of an existing workspace.

**Parameters:**

| Parameter | Required | Type | Notes |
|---|---|---|---|
| `workspace_id` | Yes | UUID | Resolve via `list_workspaces` |
| `archived` | No | bool | Set archived state |
| `pinned` | No | bool | Set pinned state |
| `name` | No | string | Set display name; pass `""` to clear |

**Response:**

```json
{
  "success": true,
  "workspace_id": "uuid",
  "archived": false,
  "pinned": true,
  "name": "New display name"
}
```

**MCPorter:**

```bash
mcporter call vibe_kanban.update_workspace workspace_id="<uuid>" name="New name" pinned=true
```

---

## delete_workspace

Delete a local workspace. Optionally cascade to remote workspace and Git branches.

**Parameters:**

| Parameter | Required | Type | Notes |
|---|---|---|---|
| `workspace_id` | Yes | UUID | Resolve via `list_workspaces` |
| `delete_remote` | No | bool | Also delete the remote workspace (default `false`) |
| `delete_branches` | No | bool | Also delete associated Git branches (default `false`) |

**Response:**

```json
{
  "success": true,
  "workspace_id": "uuid",
  "delete_remote": false,
  "delete_branches": true
}
```

**MCPorter:**

```bash
mcporter call vibe_kanban.delete_workspace workspace_id="<uuid>"
mcporter call vibe_kanban.delete_workspace workspace_id="<uuid>" delete_branches=true
```

---

## start_workspace_session

**This is the most important tool.** It creates a new workspace, checks out an
isolated branch from the chosen base, and launches a coding agent to work on the
given task.

When `issue_id` is provided:
- The workspace is automatically linked to that issue.
- If `prompt_override` is omitted, the issue's title and description become the prompt.

**Parameters:**

| Parameter | Required | Type | Notes |
|---|---|---|---|
| `title` | Yes | string | Human-readable label for the workspace |
| `executor` | Yes | string | Coding agent type (see table below) |
| `repos` | Yes | Array | At least one `{repo_id, base_branch}` object |
| `prompt_override` | No | string | Custom task prompt; required if `issue_id` is also omitted |
| `variant` | No | string | Agent model variant / version |
| `issue_id` | No | UUID | Remote issue to link the session to |

### `repos` array shape

Each entry:

```json
{ "repo_id": "uuid", "base_branch": "main" }
```

Pass multiple entries for multi-repo sessions.

### Supported executors

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

Values are case-insensitive and accept hyphens or underscores.

**Response:**

```json
{ "workspace_id": "uuid" }
```

### Example

```bash
# With custom prompt:
mcporter call vibe_kanban.start_workspace_session \
  title="Implement JWT login" \
  executor=CLAUDE_CODE \
  repos='[{"repo_id":"aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee","base_branch":"main"}]' \
  prompt_override="Create a login page with username/password form and JWT-based auth"

# With an issue (prompt derived from issue title/description):
mcporter call vibe_kanban.start_workspace_session \
  title="Implement JWT login" \
  executor=CLAUDE_CODE \
  repos='[{"repo_id":"<uuid>","base_branch":"main"}]' \
  issue_id="<uuid>"
```

---

## link_workspace

Associate an existing workspace with a remote issue after the fact (useful when a
workspace was created without an `issue_id` but you later want to track it).

**Parameters:**

| Parameter | Required | Type |
|---|---|---|
| `workspace_id` | Yes | UUID |
| `issue_id` | Yes | UUID |

**Response:**

```json
{
  "success": true,
  "workspace_id": "uuid",
  "issue_id": "uuid"
}
```

**MCPorter:**

```bash
mcporter call vibe_kanban.link_workspace workspace_id="<uuid>" issue_id="<uuid>"
```

---

## Common patterns

### Start work on an existing issue

```bash
mcporter call vibe_kanban.list_repos
# → pick repo_id

mcporter call vibe_kanban.start_workspace_session \
  title="Work on PROJ-1" executor=CLAUDE_CODE \
  repos='[{"repo_id":"<uuid>","base_branch":"main"}]' issue_id="<uuid>"
```

### Manually track a workspace against an issue

```bash
mcporter call vibe_kanban.list_workspaces
mcporter call vibe_kanban.list_issues project_id="<uuid>"
mcporter call vibe_kanban.link_workspace workspace_id="<uuid>" issue_id="<uuid>"
```

### Clean up old workspaces

```bash
mcporter call vibe_kanban.list_workspaces archived=true
mcporter call vibe_kanban.delete_workspace workspace_id="<uuid>" delete_branches=true
```
```
