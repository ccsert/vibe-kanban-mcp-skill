# vibe-kanban-mcp-skill

Vibe Kanban MCP skill for agent environments that support `SKILL.md`-based skills.

This repository packages the `vibe-kanban-mcp` skill, which helps coding agents use the Vibe Kanban MCP Server to manage organizations, projects, issues, workspaces, repositories, assignees, tags, relationships, and workspace sessions.

## Included skill

- `vibe-kanban-mcp`

## Install

Install with the `skills` CLI:

```bash
npx skills add ccsert/vibe-kanban-mcp-skill --skill vibe-kanban-mcp
```

Install to a specific agent:

```bash
npx skills add ccsert/vibe-kanban-mcp-skill --skill vibe-kanban-mcp -a opencode
```

List the available skills in this repository:

```bash
npx skills add ccsert/vibe-kanban-mcp-skill --list
```

## What the skill covers

- Organization and project discovery
- Issue create, read, update, delete workflows
- Assignee, tag, and relationship management
- Workspace and session lifecycle operations
- Repository and repo script management
- Context-aware workflows inside active Vibe Kanban workspaces

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

## Requirements

- An agent/runtime with support for skills
- Access to a Vibe Kanban MCP Server environment
- A compatible MCP client or coding agent that can load the skill

## Development

The main skill definition lives in `SKILL.md`. Supporting workflow references live under `references/`.

If you want to package the skill as a distributable archive, you can build the `.skill` file locally with the packager from the `skill-creator` tooling.

## License

Add the license that matches your intended distribution model for this repository.
