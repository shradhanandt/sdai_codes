# Claude Workspace Instructions

This is the root workspace for multi-project Claude-assisted development.

## Folder Layout

| Folder | Purpose |
|--------|---------|
| `core/` | Reusable prompts, system instructions, templates |
| `plugins/` | Custom tools, scripts, integrations |
| `projects/` | Individual project directories |
| `data/` | Shared datasets, input files, exports |
| `outputs/` | Generated files (HTML, reports, CSVs, etc.) |
| `config/` | Global configs, environment settings |

## Working Conventions

- Each project lives in `projects/<project-name>/`
- Place shared prompt templates in `core/`
- Place input/reference data in `data/`
- Write all generated output to `outputs/`
- Keep project-specific configs inside the project folder; shared configs in `config/`

## Custom Slash Commands

| Command | Usage |
|---------|-------|
| `/review <file>` | Code review with Critical / Warnings / Suggestions |
| `/new-project <name>` | Scaffold a new project in `projects/` |
| `/summarize <path>` | Summarize any file or folder |
| `/ship <project>` | Pre-ship checklist — review, clean, document |

## Hooks (auto-active)

| Event | Action |
|-------|--------|
| `PostToolUse` on Write/Edit | Logs file changes to `outputs/activity.log` |
| `Notification` | macOS notification when Claude needs attention |
| `Stop` | macOS notification + sound when task completes |

## MCP Servers

Available servers defined in `config/mcp-servers.json`.
To enable: copy entries into `~/.claude/settings.json` under `"mcpServers"`.

## Prompt Templates (`core/`)

| File | Purpose |
|------|---------|
| `system-prompt.md` | Default workspace system prompt |
| `code-review.md` | Structured code review prompt |
| `project-brief.md` | New project scoping template |
| `debug.md` | Debugging context template |

## Token Efficiency Rules

Always minimise token usage. In order of preference:

| Approach | Use when |
|----------|----------|
| `Grep` / `Glob` | Finding files or searching content |
| `Read` with offset+limit | Large files — read only relevant section |
| `Edit` | Modifying existing files (sends diff only, not full file) |
| Parallel tool calls | Independent reads/searches — batch in one message |
| Bash one-liners | Quick info (git status, ls, cat short files) |
| Agent | Only for open-ended multi-step research |

**Avoid:**
- Reading full large files when only a section is needed
- Sequential tool calls that could run in parallel
- Repeating file contents back in responses — reference by path instead
- Re-reading files already seen in the same session

## Notes

- Do not create files outside this structure unless explicitly asked
- Prefer editing existing files over creating new ones
- Keep outputs organized by project: `outputs/<project-name>/`

## Session-End Checklist (run at end of every session)

At the end of each Claude session, always update the following:

| File | What to update |
|------|---------------|
| `outputs/activity.log` | Append dated summary of everything done |
| `README.md` | Update project status + last updated date |
| `Home.md` | Add row to Recent Sessions table |
| `projects/<name>/README.md` | Update status, new files, notes |
| `projects/<name>/SESSION_NOTES.md` | Add dated section with detailed changes |

> This checklist is mandatory. Do not end a session without completing it.
