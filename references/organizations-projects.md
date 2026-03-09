# Organizations & Projects

> All examples use MCPorter CLI: `mcporter call vibe_kanban.<tool> param=value`

## Table of contents

1. [list_organizations](#list_organizations)
2. [list_org_members](#list_org_members)
3. [list_projects](#list_projects)

---

## list_organizations

List every organisation the current user belongs to.  
This is usually the first call in any workflow — it resolves the `organization_id`
needed by downstream tools.

**Parameters:** none

**Response:**

```json
{
  "organizations": [
    {
      "id": "uuid",
      "name": "Acme Corp",
      "slug": "acme-corp",
      "is_personal": false
    }
  ],
  "count": 1
}
```

**MCPorter:**

```bash
mcporter call vibe_kanban.list_organizations
```

---

## list_org_members

List every member of a given organisation, including their roles and profile details.
Useful before calling `assign_issue` when you need to resolve a name to a `user_id`.

**Parameters:**

| Parameter | Required | Type | Notes |
|---|---|---|---|
| `organization_id` | Yes | UUID | Resolve via `list_organizations` |

**Response:**

```json
{
  "organization_id": "uuid",
  "members": [
    {
      "user_id": "uuid",
      "role": "MEMBER",
      "joined_at": "2024-01-01T00:00:00Z",
      "first_name": "Zhang",
      "last_name": "San",
      "username": "zhangsan",
      "email": "zhangsan@example.com",
      "avatar_url": "https://..."
    }
  ],
  "count": 1
}
```

**MCPorter:**

```bash
mcporter call vibe_kanban.list_org_members organization_id="<uuid>"
```

---

## list_projects

List all projects under a given organisation.

**Parameters:**

| Parameter | Required | Type | Notes |
|---|---|---|---|
| `organization_id` | Yes | UUID | Resolve via `list_organizations` |

**Response:**

```json
{
  "projects": [
    {
      "id": "uuid",
      "name": "Backend Rewrite",
      "created_at": "2024-01-01T00:00:00Z",
      "updated_at": "2024-01-01T00:00:00Z"
    }
  ],
  "count": 1
}
```

**MCPorter:**

```bash
mcporter call vibe_kanban.list_projects organization_id="<uuid>"
```

---

## Typical call sequence

```bash
mcporter call vibe_kanban.list_organizations
# → pick organization_id from output

mcporter call vibe_kanban.list_projects organization_id="<org_id>"
# → pick project_id from output
```

From here, proceed to issue management or workspace creation.
