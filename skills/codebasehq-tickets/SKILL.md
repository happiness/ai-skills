---
name: codebasehq-tickets
description: Creates and updates tickets in CodebaseHQ through the Codebase MCP tools (create_ticket, update_ticket, get_ticket, list_tickets). Use when asked to open, file, update, comment on, close, or reassign a Codebase ticket, or to write ticket descriptions and notes that use Codebase-specific markdown such as {branch:}, {tag:}, {commit:} and "Note:" blocks.
---

# CodebaseHQ tickets

Tickets live in CodebaseHQ and are managed through the `codebase` MCP server
(tools named `mcp__codebase__*`; a `mcp__claude_ai_Codebase__*` duplicate may
also exist, so use whichever is connected). Tool schemas are deferred, so load
them with ToolSearch before calling.

## Workflow

1. **Find the project permalink.** Call `list_projects` once and reuse the
   permalink as `project`. Do not guess it.
2. **Look before writing.** For an update, call `get_ticket` (and
   `get_ticket_notes` if history matters) first. For a new ticket, check
   `list_tickets` for duplicates.
3. **Use valid names for fields.** `status`, `priority` and `category` take
   *names*, not IDs. If unsure, look them up with `get_ticket_statuses`,
   `get_ticket_priorities`, `get_ticket_categories`. `assignee` is a username
   (see `get_project_users`).
4. **Create:** `create_ticket` with `summary` (short title) and `description`
   (markdown, see below).
5. **Update:** `update_ticket` with `ticket_id`. Put the comment in `content`;
   it becomes a ticket note. Status, priority, category, assignee and summary
   changes can go in the same call.
6. **Report back** with the ticket ID and what changed.

Creating tickets and posting notes is visible to the whole team and cannot be
cleanly retracted. Show the user the summary and description and get a go-ahead
before calling `create_ticket` or `update_ticket`, unless they already asked
for exactly that text.

## Codebase markdown

Descriptions and notes are Markdown, plus these Codebase-specific constructs.

| Purpose | Syntax |
|---------|--------|
| Note / callout block | `Note: I am a note` |
| Link to a branch | `{branch:repo/master}` |
| Link to a tag | `{tag:repo/v1.3.1}` |
| Link to a commit | `{commit:repo/12345}` |
| Link to another ticket | `#123` |

- `repo` is the repository permalink in Codebase (not the local directory
  name). Take it from `get_project` or ask the user if it is unclear.
- Use the full commit SHA or a short prefix in `{commit:...}`.
- Always prefix a ticket number with `#` (e.g. `#123`) when referencing
  another ticket. Codebase turns it into a link to that ticket; a bare number
  stays plain text.
- Prefer these constructs over pasting raw URLs or bare SHAs/branch names.

Example description:

```markdown
## Problem
Old /student/ aliases still resolve after the migration.

Note: Blocked until the client confirms the pathauto patterns.

## Work
Implemented on {branch:kise/3901-purge-old-aliases}, see {commit:kise/3e459c1181}.
Related to #3864. Released in {tag:kise/v1.3.1}.
```

## Project conventions

- Branches are named `<TICKET_ID>-<SHORT_DESCRIPTION>`, e.g.
  `3901-purge-old-aliases`.
- Commit subjects on ticket branches end with `[touch:<TICKET_ID>]`, which
  links the commit to the ticket in Codebase.
- Write ticket content in the language the ticket is already written in.
