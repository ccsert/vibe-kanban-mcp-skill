# Overview — Vibe Kanban via MCPorter

## Table of contents

1. [Prerequisites](#prerequisites)
2. [Install MCPorter](#install-mcporter)
3. [Register Vibe Kanban](#register-vibe-kanban)
4. [Call syntax](#call-syntax)
5. [Reading output](#reading-output)
6. [Supported executors](#supported-executors)
7. [Typical workflows](#typical-workflows)

---

## Prerequisites

- **MCPorter** — installed and available on `PATH`.
- **Vibe Kanban** — the `vibe-kanban` npm package (MCPorter launches it as a stdio
  MCP server automatically).

---

## Install MCPorter

```bash
# Global install
npm i -g mcporter

# Verify installation
mcporter list
```

---

## Register Vibe Kanban

Create or edit `config/mcporter.json` (project-level) or
`~/.mcporter/mcporter.json` (global):

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

Verify:

```bash
mcporter list vibe_kanban
```

This prints all Vibe Kanban tools with TypeScript-style signatures.

### Ad-hoc usage (no config file)

```bash
mcporter call --stdio "vibe-kanban --mcp" list_organizations
```

Add `--persist config/mcporter.json` to save the definition for future runs.

---

## Call syntax

All tool calls follow `mcporter call vibe_kanban.<tool> param=value`.

### Flag style (recommended)

```bash
mcporter call vibe_kanban.list_organizations
mcporter call vibe_kanban.create_issue title="Add login page" priority=high
mcporter call vibe_kanban.update_issue issue_id="<uuid>" status="Done"
```

### Function-call style

```bash
mcporter call 'vibe_kanban.create_issue(title: "Add login page", priority: "high")'
```

### Shorthand (infers `call` verb)

```bash
mcporter vibe_kanban.list_organizations
mcporter vibe_kanban.list_issues status=backlog limit=10
```

### Passing arrays / objects

JSON values can be passed as quoted strings:

```bash
mcporter call vibe_kanban.start_workspace_session \
  title="Feature X" executor=CLAUDE_CODE \
  repos='[{"repo_id":"<uuid>","base_branch":"main"}]'
```

---

## Reading output

MCPorter auto-formats JSON responses. Use flags to control format:

```bash
mcporter call vibe_kanban.list_issues --output json   # raw JSON
mcporter call vibe_kanban.list_issues --output raw    # unprocessed MCP envelope
```

> **Important:** `project_id`, `organization_id`, and `workspace_id` must always be
> passed explicitly. Context inference is not available through MCPorter — resolve
> these IDs first by calling the corresponding list tools.

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

```bash
mcporter call vibe_kanban.list_organizations
# → pick organization_id from output

mcporter call vibe_kanban.list_projects organization_id="<org_id>"
# → pick project_id from output

mcporter call vibe_kanban.create_issue project_id="<proj_id>" title="Task 1" priority=high
# → get issue_id from output

mcporter call vibe_kanban.start_workspace_session \
  title="Implement Task 1" executor=CLAUDE_CODE \
  repos='[{"repo_id":"<repo_id>","base_branch":"main"}]' \
  issue_id="<issue_id>"
```

### 2. Team assignment workflow

```bash
mcporter call vibe_kanban.list_org_members organization_id="<org_id>"
mcporter call vibe_kanban.list_issues project_id="<proj_id>"
mcporter call vibe_kanban.assign_issue issue_id="<issue_id>" user_id="<user_id>"
mcporter call vibe_kanban.list_issue_assignees issue_id="<issue_id>"
```

### 3. Multi-repo workspace

```bash
mcporter call vibe_kanban.list_repos

mcporter call vibe_kanban.start_workspace_session \
  title="Cross-repo feature" executor=CLAUDE_CODE \
  repos='[{"repo_id":"<id1>","base_branch":"main"},{"repo_id":"<id2>","base_branch":"main"}]'
```

### 4. Planning agent

A coding agent running inside a Vibe Kanban workspace can use MCPorter to populate
issues automatically:

1. Create a planning issue with a custom agent profile (see Agent Configurations docs).
2. The agent explores the codebase and calls `create_issue` for each sub-task.
3. Other agents pick up those issues via `start_workspace_session`.

This closes the loop from high-level intent to executable work items without manual
data entry.
