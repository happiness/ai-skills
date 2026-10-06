---
name: codebasehq-tickets
description: Creates and updates tickets in CodebaseHQ through the Codebase MCP tools (create_ticket, update_ticket, get_ticket, list_tickets). Use when asked to open, file, update, comment on, close, or reassign a Codebase ticket, or to write ticket descriptions and notes that use Codebase-specific markdown such as {branch:}, {tag:}, {commit:} and "Note:" blocks.
---

# CodebaseHQ tickets

Managed through the `codebase` MCP server (`mcp__codebase__*`; a
`mcp__claude_ai_Codebase__*` duplicate may exist, use whichever is connected).
Tool schemas are deferred: load them with ToolSearch first.

## Workflow

1. Call `list_projects` once and reuse the permalink as `project`. Don't guess.
2. Before updating, call `get_ticket` (and `get_ticket_notes` if history
   matters). Before creating, check `list_tickets` for duplicates.
3. `status`, `priority`, `category` take *names*, not IDs (look up with
   `get_ticket_statuses`, `get_ticket_priorities`, `get_ticket_categories`).
   `assignee` is a username (`get_project_users`).
4. Create: `create_ticket` with `summary` and markdown `description`.
5. Update: `update_ticket` with `ticket_id`; the comment goes in `content`
   (becomes a note). Field changes can go in the same call.
6. Report the ticket ID and what changed.

Tickets and notes are team-visible and not cleanly retractable: show the user
the text and get a go-ahead before `create_ticket`/`update_ticket`, unless they
already asked for exactly that text.

## Codebase markdown

Markdown plus:

| Purpose | Syntax |
|---------|--------|
| Note block | `Note: text` |
| Branch | `{branch:repo/master}` |
| Tag | `{tag:repo/v1.3.1}` |
| Commit | `{commit:repo/12345}` (full SHA or short prefix) |
| Ticket | `#123` (bare numbers aren't linked) |

- `repo` is the Codebase repository permalink, not the local directory name
  (see `get_project`, or ask).
- Prefer these over raw URLs, bare SHAs or branch names.

```markdown
## Problem
Old /student/ aliases still resolve after the migration.

Note: Blocked until the client confirms the pathauto patterns.

## Work
Implemented on {branch:kise/3901-purge-old-aliases}, see {commit:kise/3e459c1181}.
Related to #3864. Released in {tag:kise/v1.3.1}.
```

## Conventions

- Branches: `<TICKET_ID>-<SHORT_DESCRIPTION>`, e.g. `3901-purge-old-aliases`.
- Commit subjects on ticket branches end with `[touch:<TICKET_ID>]`.
- Write in the language the ticket is already written in.
