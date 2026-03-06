# Organizations & Projects

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

---

## list_org_members

List every member of a given organisation, including their roles and profile details.
Useful before calling `assign_issue` when you need to resolve a name to a `user_id`.

**Parameters:**

| Parameter | Required | Type | Notes |
|---|---|---|---|
| `organization_id` | No | UUID | Can be omitted when running inside a workspace with an inferred organisation |

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

---

## list_projects

List all projects under a given organisation.  
Unlike most tools, `organization_id` is **always required** here — it is never inferred
from workspace context.

**Parameters:**

| Parameter | Required | Type | Notes |
|---|---|---|---|
| `organization_id` | Yes | UUID | Must be explicit |

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

---

## Typical call sequence

```
list_organizations  →  pick organization_id
list_projects       →  pick project_id
```

From here, proceed to issue management or workspace creation.
