# Repositories & Script Management

## Table of contents

1. [list_repos](#list_repos)
2. [get_repo](#get_repo)
3. [update_setup_script](#update_setup_script)
4. [update_cleanup_script](#update_cleanup_script)
5. [update_dev_server_script](#update_dev_server_script)

---

## list_repos

Return all repositories that have been configured in Vibe Kanban.  
Use this first to resolve a `repo_id` before calling any other repo tool or
`start_workspace_session`.

**Parameters:** none

**Response:**

```json
{
  "repos": [
    { "id": "uuid", "name": "acme/backend" },
    { "id": "uuid", "name": "acme/frontend" }
  ],
  "count": 2
}
```

---

## get_repo

Fetch full details for a single repository, including all three configured scripts.

**Parameters:**

| Parameter | Required | Type |
|---|---|---|
| `repo_id` | Yes | UUID |

**Response:**

```json
{
  "id": "uuid",
  "name": "acme/backend",
  "display_name": "Backend API",
  "setup_script": "npm ci",
  "cleanup_script": "rm -rf dist node_modules",
  "dev_server_script": "npm run dev"
}
```

---

## Scripts

Each repository has three optional lifecycle scripts that Vibe Kanban executes at
different points in a workspace's life:

| Script | When it runs | Typical content |
|---|---|---|
| `setup_script` | When a new workspace is created | Install deps, run migrations, seed data |
| `cleanup_script` | When a workspace is destroyed | Remove build artefacts, temp files |
| `dev_server_script` | When the workspace dev server starts | `npm run dev`, `cargo run`, etc. |

Pass an empty string `""` to any update tool to clear the script.

---

## update_setup_script

**Parameters:**

| Parameter | Required | Type | Notes |
|---|---|---|---|
| `repo_id` | Yes | UUID | |
| `script` | Yes | string | Pass `""` to clear |

**Response:**

```json
{ "success": true, "repo_id": "uuid", "field": "setup_script" }
```

---

## update_cleanup_script

**Parameters:**

| Parameter | Required | Type | Notes |
|---|---|---|---|
| `repo_id` | Yes | UUID | |
| `script` | Yes | string | Pass `""` to clear |

**Response:** same shape as `update_setup_script`, with `"field": "cleanup_script"`.

---

## update_dev_server_script

**Parameters:**

| Parameter | Required | Type | Notes |
|---|---|---|---|
| `repo_id` | Yes | UUID | |
| `script` | Yes | string | Pass `""` to clear |

**Response:** same shape as `update_setup_script`, with `"field": "dev_server_script"`.

---

## Typical pattern

```
list_repos              → pick repo_id
get_repo                → inspect current scripts
update_setup_script     → set install / migration commands
update_dev_server_script → set the dev server command
```
