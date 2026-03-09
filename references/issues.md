# Issues, Assignees, Tags & Relationships

> All examples use MCPorter CLI: `mcporter call vibe_kanban.<tool> param=value`

## Table of contents

1. [Issue tools](#issue-tools)
   - [create_issue](#create_issue)
   - [list_issues](#list_issues)
   - [get_issue](#get_issue)
   - [update_issue](#update_issue)
   - [delete_issue](#delete_issue)
2. [Assignee tools](#assignee-tools)
   - [list_issue_assignees](#list_issue_assignees)
   - [assign_issue](#assign_issue)
   - [unassign_issue](#unassign_issue)
3. [Tag tools](#tag-tools)
   - [list_tags](#list_tags)
   - [list_issue_tags](#list_issue_tags)
   - [add_issue_tag](#add_issue_tag)
   - [remove_issue_tag](#remove_issue_tag)
4. [Relationship tools](#relationship-tools)
   - [create_issue_relationship](#create_issue_relationship)
   - [delete_issue_relationship](#delete_issue_relationship)

---

## Issue tools

### create_issue

Create a new issue in a project.
Use `@tagname` in `description` to expand inline tags.

**Parameters:**

| Parameter | Required | Type | Notes |
|---|---|---|---|
| `title` | Yes | string | Issue title |
| `project_id` | Yes | UUID | Resolve via `list_projects` |
| `description` | No | string | Supports `@tagname` expansion |
| `priority` | No | string | `urgent` \| `high` \| `medium` \| `low` |
| `parent_issue_id` | No | UUID | Creates a sub-task under the given issue |

**Response:**

```json
{ "issue_id": "uuid" }
```

**MCPorter:**

```bash
mcporter call vibe_kanban.create_issue project_id="<uuid>" title="Add login page" priority=high
mcporter call vibe_kanban.create_issue project_id="<uuid>" title="Sub-task" parent_issue_id="<uuid>"
```

---

### list_issues

List issues with rich filtering.  
Without filters, returns the 50 most recent issues.

**Parameters:**

| Parameter | Required | Type | Notes |
|---|---|---|---|
| `project_id` | Yes | UUID | Resolve via `list_projects` |
| `limit` | No | int | Default 50 |
| `offset` | No | int | Default 0 (for pagination) |
| `status` | No | string | Filter by status name (case-insensitive) |
| `priority` | No | string | Filter by priority |
| `parent_issue_id` | No | UUID | Returns only direct sub-tasks |
| `search` | No | string | Fuzzy match against title and description |
| `simple_id` | No | string | Exact match on short ID, e.g. `PROJ-1` |
| `assignee_user_id` | No | UUID | Filter by assignee |
| `tag_id` | No | UUID | Filter by tag ID |
| `tag_name` | No | string | Filter by tag name |

**Response:**

```json
{
  "issues": [
    {
      "id": "uuid",
      "title": "Add login page",
      "simple_id": "PROJ-1",
      "status": "In Progress",
      "priority": "high",
      "parent_issue_id": null,
      "created_at": "2024-01-01T00:00:00Z",
      "updated_at": "2024-01-01T00:00:00Z",
      "pull_request_count": 1,
      "latest_pr_url": "https://github.com/...",
      "latest_pr_status": "open"
    }
  ],
  "total_count": 100,
  "returned_count": 50,
  "limit": 50,
  "offset": 0,
  "project_id": "uuid"
}
```

**MCPorter:**

```bash
mcporter call vibe_kanban.list_issues project_id="<uuid>"
mcporter call vibe_kanban.list_issues project_id="<uuid>" status=backlog priority=high limit=10
mcporter call vibe_kanban.list_issues project_id="<uuid>" search="login"
mcporter call vibe_kanban.list_issues project_id="<uuid>" simple_id="PROJ-1"
```

---

### get_issue

Fetch the complete details for a single issue, including sub-issues, tags, relationships,
and attached pull requests.

**Parameters:**

| Parameter | Required | Type |
|---|---|---|
| `issue_id` | Yes | UUID |

**Response:** same shape as the `issue` field returned by `update_issue` (see below).

**MCPorter:**

```bash
mcporter call vibe_kanban.get_issue issue_id="<uuid>"
```

---

### update_issue

Modify one or more fields on an existing issue.  
Only fields you pass are changed; omitted fields stay as-is.

**Parameters:**

| Parameter | Required | Type | Notes |
|---|---|---|---|
| `issue_id` | Yes | UUID | |
| `title` | No | string | |
| `description` | No | string | |
| `status` | No | string | Must match a status that already exists in the project |
| `priority` | No | string | |
| `parent_issue_id` | No | UUID \| null | Omit → no change; `null` → remove parent; UUID → set new parent |

**Response:**

```json
{
  "issue": {
    "id": "uuid",
    "title": "Add login page",
    "simple_id": "PROJ-1",
    "description": "...",
    "status": "Done",
    "status_id": "uuid",
    "priority": "high",
    "parent_issue_id": null,
    "start_date": "2024-01-01",
    "target_date": "2024-01-15",
    "completed_at": "2024-01-14T10:00:00Z",
    "created_at": "2024-01-01T00:00:00Z",
    "updated_at": "2024-01-14T10:00:00Z",
    "pull_requests": [],
    "tags": [],
    "relationships": [],
    "sub_issues": []
  }
}
```

**MCPorter:**

```bash
mcporter call vibe_kanban.update_issue issue_id="<uuid>" status="Done"
mcporter call vibe_kanban.update_issue issue_id="<uuid>" title="New title" priority=urgent
```

---

### delete_issue

Permanently delete an issue. This cannot be undone.

**Parameters:**

| Parameter | Required | Type |
|---|---|---|
| `issue_id` | Yes | UUID |

**Response:**

```json
{ "deleted_issue_id": "uuid" }
```

**MCPorter:**

```bash
mcporter call vibe_kanban.delete_issue issue_id="<uuid>"
```

---

## Assignee tools

### list_issue_assignees

Return all users currently assigned to an issue.

**Parameters:**

| Parameter | Required | Type |
|---|---|---|
| `issue_id` | Yes | UUID |

**Response:**

```json
{
  "issue_id": "uuid",
  "issue_assignees": [
    {
      "id": "uuid",
      "issue_id": "uuid",
      "user_id": "uuid",
      "assigned_at": "2024-01-01T00:00:00Z"
    }
  ],
  "count": 1
}
```

**MCPorter:**

```bash
mcporter call vibe_kanban.list_issue_assignees issue_id="<uuid>"
```

---

### assign_issue

Assign a user to an issue. Call `list_org_members` first to get a valid `user_id`.

**Parameters:**

| Parameter | Required | Type |
|---|---|---|
| `issue_id` | Yes | UUID |
| `user_id` | Yes | UUID |

**Response:**

```json
{ "issue_assignee_id": "uuid" }
```

**MCPorter:**

```bash
mcporter call vibe_kanban.assign_issue issue_id="<uuid>" user_id="<uuid>"
```

---

### unassign_issue

Remove an assignee. Requires the `issue_assignee_id` from `list_issue_assignees`
(not the raw `user_id`).

**Parameters:**

| Parameter | Required | Type |
|---|---|---|
| `issue_assignee_id` | Yes | UUID |

**Response:**

```json
{ "success": true, "issue_assignee_id": "uuid" }
```

**MCPorter:**

```bash
mcporter call vibe_kanban.unassign_issue issue_assignee_id="<uuid>"
```

---

## Tag tools

### list_tags

List every tag available in a project. Tags have a name and an optional colour.

**Parameters:**

| Parameter | Required | Type | Notes |
|---|---|---|---|
| `project_id` | Yes | UUID | Resolve via `list_projects` |

**Response:**

```json
{
  "project_id": "uuid",
  "tags": [
    { "id": "uuid", "project_id": "uuid", "name": "bug", "color": "#ff0000" }
  ],
  "count": 1
}
```

**MCPorter:**

```bash
mcporter call vibe_kanban.list_tags project_id="<uuid>"
```

---

### list_issue_tags

List the tags currently attached to a specific issue.

**Parameters:**

| Parameter | Required | Type |
|---|---|---|
| `issue_id` | Yes | UUID |

**Response:**

```json
{
  "issue_id": "uuid",
  "issue_tags": [
    { "id": "uuid", "issue_id": "uuid", "tag_id": "uuid" }
  ],
  "count": 1
}
```

**MCPorter:**

```bash
mcporter call vibe_kanban.list_issue_tags issue_id="<uuid>"
```

---

### add_issue_tag

Attach a tag to an issue. Call `list_tags` first to resolve `tag_id`.

**Parameters:**

| Parameter | Required | Type |
|---|---|---|
| `issue_id` | Yes | UUID |
| `tag_id` | Yes | UUID |

**Response:**

```json
{ "issue_tag_id": "uuid" }
```

**MCPorter:**

```bash
mcporter call vibe_kanban.add_issue_tag issue_id="<uuid>" tag_id="<uuid>"
```

---

### remove_issue_tag

Detach a tag from an issue. Requires the `issue_tag_id` from `list_issue_tags`
(not the raw `tag_id`).

**Parameters:**

| Parameter | Required | Type |
|---|---|---|
| `issue_tag_id` | Yes | UUID |

**Response:**

```json
{ "success": true, "issue_tag_id": "uuid" }
```

**MCPorter:**

```bash
mcporter call vibe_kanban.remove_issue_tag issue_tag_id="<uuid>"
```

---

## Relationship tools

Relationships model the dependency graph between issues.

### create_issue_relationship

**Parameters:**

| Parameter | Required | Type | Notes |
|---|---|---|---|
| `issue_id` | Yes | UUID | Source issue |
| `related_issue_id` | Yes | UUID | Target issue |
| `relationship_type` | Yes | string | `blocking` \| `related` \| `has_duplicate` |

**Response:**

```json
{ "relationship_id": "uuid" }
```

**MCPorter:**

```bash
mcporter call vibe_kanban.create_issue_relationship \
  issue_id="<uuid>" related_issue_id="<uuid>" relationship_type=blocking
```

---

### delete_issue_relationship

**Parameters:**

| Parameter | Required | Type |
|---|---|---|
| `relationship_id` | Yes | UUID |

**Response:**

```json
{ "success": true, "deleted_relationship_id": "uuid" }
```

**MCPorter:**

```bash
mcporter call vibe_kanban.delete_issue_relationship relationship_id="<uuid>"
```
